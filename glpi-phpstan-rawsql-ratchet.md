---
name: glpi-phpstan-rawsql-ratchet
description: "Une règle PHPStan locale peut vivre dans tools/src/PHPStan sans toucher au paquet phpstan-glpi, avec une baseline dédiée comme cliquet"
metadata: 
  node_type: memory
  type: project
  originSessionId: 1da2e44f-5ba2-414e-aca4-d5467a172edb
  modified: 2026-09-08T12:51:24.885Z
---

`composer.json` déclare déjà `"Glpi\\Tools\\": "tools/src/"` en `autoload-dev`, donc une
règle PHPStan dans `tools/src/PHPStan/Rules/` (namespace `Glpi\Tools\PHPStan\Rules`) est
autoloadable **sans redump et sans toucher au paquet externe `glpi-project/phpstan-glpi`** ;
il suffit d'un bloc `services:` dans `phpstan.neon.dist` (même montage que
`ForbidDynamicInstantiationRule`, avec `%treatPhpDocTypesAsCertain%` en argument).
`tools/` n'est ni dans `parameters.paths` de PHPStan ni dans `projectFiles` de Psalm, donc
le fichier de règle n'est pas auto-analysé.

Deux pièges vérifiés :
- `composer-dependency-analyser` **scanne les chemins `autoload-dev`** (`tools/src`,
  `tests/src`) et tourne dans `lint_php-lint.sh` avec `|| EXIT_CODE=1`. Les classes
  `PHPStan\*` (exposées seulement par le phar) et `PhpParser\*` (dépendance dev
  transitive) le font échouer → il faut
  `->addPathToExclude(__DIR__ . '/tools/src/PHPStan')` dans `composer-dependency-analyser.php`.
- L'`include` d'une baseline doit être ajouté **après** sa première génération : PHPStan
  échoue au chargement de config sur un `include` de fichier inexistant.

Le cliquet est gratuit : `reportUnmatchedIgnoredErrors` n'est pas désactivé dans
`phpstan.neon.dist`, donc dès qu'un site baseliné est corrigé, PHPStan échoue en
`ignore.count` / `ignore.unmatched` et force à régénérer la baseline **à la baisse**.
Un test PHPUnit « compteur de patterns » serait inférieur et redondant (et
`tests/bootstrap.php` exige une base installée, donc il tournerait dans le job `tests`,
pas dans `lint`).

**Why:** c'est le seul moyen d'empêcher la réintroduction de SQL brut sans attendre une
release du paquet externe, et sans noyer la CI sous ~400 signalements existants.

**How to apply:** générer dans un fichier de baseline **dédié**
(`.phpstan-baseline.rawSql.php`), jamais en régénérant `.phpstan-baseline.php` (17k lignes
de dette sans rapport) ; PHPStan exclut du chargement le fichier qu'il génère, donc les
autres baselines restent actives et le fichier produit ne contient que la nouvelle règle.
Ajouter une cible `make` symétrique de `phpstan-generate-baseline`.
Voir [[glpi-psalm-taint-sql-blindspot]].
