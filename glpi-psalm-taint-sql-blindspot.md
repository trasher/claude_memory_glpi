---
name: glpi-psalm-taint-sql-blindspot
description: Le scan Psalm de GLPI ne voit aucun TaintedSql parce que QueryExpression::getValue() porte @psalm-taint-escape sql
metadata: 
  node_type: memory
  type: project
  originSessionId: 1da2e44f-5ba2-414e-aca4-d5467a172edb
  modified: 2026-09-08T12:51:08.456Z
---

`psalm.xml` a `runTaintAnalysis="true"` et ne supprime que `TaintedCallable`/`TaintedLdap` :
`TaintedSql` est donc actif en CI (`.github/workflows/security-scan.yml`, sur chaque PR).
Mais il ne remonte jamais rien, car `@psalm-taint-escape sql` est posé sur
`Glpi\DBAL\QueryExpression::getValue()` — le seul point d'entrée de SQL brut du framework
est aussi le point aveugle de l'unique analyseur de sécurité du projet.

Déplacer le sink vers le constructeur (`@psalm-taint-sink sql $expression` +
`@psalm-taint-specialize`) fait remonter **15 flux, tous depuis `$_POST`** (mesuré le
2026-09-08 sur `feature/improve-dbal-enhanced`, run `vendor/bin/psalm --no-cache`, ~4 min).

Triage de ces 15 flux :
- **1 vrai défaut** : `IPNetwork::getCriteriaForMatchingElement()` interpolait
  `$this->fields["netmask_$i"]` et `$start[$i]` dans le SQL **sans aucun quoting**.
- **14 faux positifs**, tous de la même forme : un **nom de table** interpolé dans un
  fragment de SQL brut. Psalm empoisonne globalement le cache statique
  `CommonDBTM::$tables_of` (alimenté par `DbUtils::getExpectedTableNameForClass()` depuis
  un `isPluginItemType($_POST['itemtype'])`), donc n'importe quel `X::getTable()` devient
  tainted. Ils disparaissent dès que l'identifiant passe par
  `QueryIdentifier`/`quoteName` (qui portent, eux, un `taint-escape` légitime).
  Les plus coûteux à couper : `ITILFollowup::buildParentCondition()` (×3, demande la
  réécriture des conditions de visibilité ITIL) et `SQLProvider.php:3031`, qui enveloppe
  le contrat plugin « raw SQL string » et ne peut être fermé que par sa dépréciation.

**Why:** sans ce déplacement, ajouter des règles de style ou relire du code ne protège de
rien : la CI valide silencieusement n'importe quelle interpolation dans une
`QueryExpression`. Et connaître le triage évite de reconclure « 15 vulnérabilités ».

**How to apply:** ne pas faire atterrir le déplacement du sink dans la même PR que le
reste — il faut d'abord couper les 14 faux positifs (ou accepter un
`errorBaseline` Psalm, que ce dépôt n'a jamais eu). Voir [[glpi-phpstan-rawsql-ratchet]].
