---
name: glpi-test-database
description: Base MySQL et configuration à utiliser pour lancer la suite PHPUnit de GLPI
metadata:
  node_type: memory
  type: project
  originSessionId: 480bac13-2fe3-493f-b485-81358ee965dd
  modified: 2026-09-25T09:08:44.700Z
---

La base `glpi_claude` est celle mise à disposition pour lancer les tests GLPI. Les identifiants
MySQL sont à reprendre du `tests/config/config_db.php` d'un checkout déjà configuré (par exemple
`/var/www/webapps/glpi`) ; attention, le mot de passe y est écrit sous forme URL-encodée et doit
être décodé pour être utilisé en ligne de commande.

Dans un worktree neuf, `tests/config/` est vide (gitignoré) et la suite s'arrête sur
« Configuration file for tests not found ». Installation :

```
php bin/console tools:locales:compile   # sinon l'install échoue sur locales/en_GB.mo manquant
php bin/console database:install -r -f --db-host=localhost --db-name=glpi_claude \
    --db-user=glpi --db-password=<mot de passe> --no-interaction --no-telemetry --env=testing
```

Échecs pré-existants sans rapport avec le code testé, sur numaios : les 5 assertions
de `UserTest::test*PdfFont*` / `testPdffontPreference` (polices PDF absentes) et 2 tests LDAP
sautés (extension `php-ldap` non installée). Voir [[glpi-checkout-location]].
