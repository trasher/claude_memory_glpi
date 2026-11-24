---
name: glpi-pdo-postgresql-project
description: "Chantier PDO (remplace mysqli) puis PostgreSQL expérimental sur main — décisions, worktree, base de test"
metadata:
  node_type: memory
  type: project
  originSessionId: 813c1a66-d17a-4c49-9059-bbed8345231d
  modified: 2026-10-07T14:17:54.784Z
---

Chantier démarré le 2026-10-07 : remplacer mysqli par PDO dans GLPI (parité MySQL/MariaDB stricte), puis support PostgreSQL expérimental.

Décisions prises par Johan :
- Branche cible `main` (13.0.0-dev), worktree `/var/www/projects/teclib/glpi/feature/pdo` (branche `feature/pdo`).
- PDO **remplace** mysqli (pas de double pilote). Pas de Doctrine DBAL (abstraction maison légère derrière `DBmysql`).
- PHP 8.4 minimum (pour `Pdo\Mysql::getWarningCount()`) ; plateforme composer laissée à 8.3.99 car laminas-mail/mime bloquent.
- Avertissements SQL lus après lecture des lignes (désormais remontés) ; Search corrigé (pas de `= ''` sur colonnes non texte).
- PostgreSQL : nouvelles installations seules, schéma obtenu par conversion de `install/mysql/glpi-empty.sql`.
- Plan détaillé : `~/.claude/plans/glpi-tourne-uniquement-sur-snuggly-chipmunk.md`.

**Why:** retours utilisateurs sur PG en version publiée, sans régression MySQL.
**How to apply:** base de test du worktree = `glpi_tests_pdo` (droits accordés par Johan) ; ne jamais push ([[never-push]]) ; comparer les échecs de la suite complète avec le code non modifié (stash) car beaucoup de tests sont environnementaux/instables (LDAP absent, TicketTest, TicketValidationTest).
