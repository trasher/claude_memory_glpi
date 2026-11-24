---
name: glpi-doctrine-poc
description: "POC Doctrine DBAL + DoctrineBundle sous DBmysql/DBmysqlIterator — worktree, choix structurants, pièges de test"
metadata:
  node_type: memory
  type: project
  originSessionId: 9fb166fe-666e-48b9-ae1f-d7cf7c6b1dae
  modified: 2026-11-05T14:21:54.068Z
---

POC démarré le 2026-10-23 : `DBmysql`/`DBmysqlIterator` tournent sur Doctrine DBAL 4 (pilote mysqli) + DoctrineBundle (sans ORM), sur `main`.
Worktree `/var/www/projects/teclib/glpi/feature/doctrine-poc` (branche `feature/doctrine-poc`), base de test `glpi_tests_doctrine` (créée par Johan). Plan : `~/.claude/plans/je-voudrai-faire-un-curious-torvalds.md`.

Choix structurants :
- Paramètres de `config_db.php` fournis à la connexion par un middleware DBAL (`LegacyConfigurationMiddleware`, ConnectionNameAware, priorité la plus basse) ; le conteneur est compilé avant le chargement de config_db.php.
- `analyseCrit`/`analyseJoins` conservés (API publique, sortie dont dépendent Search & co) ; le QueryBuilder n'assemble que l'instruction, et les jointures sont collées au dernier FROM (le modèle de jointure DBAL exige des alias uniques).
- Le profilage Doctrine est lié à `shouldEnableExtraDevAndDebugTools()`, et non à `kernel.debug`.
- Reprend deux commits de [[glpi-pdo-postgresql-project]] (958294a05a, 7f80a7f48c), qui excluait pourtant Doctrine : c'est un chantier concurrent.

**Why:** évaluer DBAL à la place de la couche maison.
**How to apply:**
- Le conteneur de test (`tests/files/_cache/*-testing/app`) n'est pas recompilé après un `git stash` : il faut le supprimer.
- `RichTextTest::testGetEnhancedHtml` abaissait `pcre.backtrack_limit` sans le restaurer en cas d'échec, ce qui fait tomber les tests suivants. Le correctif est sur la branche.
- Comparer la suite complète à `main` via une worktree détachée qui partage la même base.
- Le conteneur de production (`files/_cache/*-production/app`) n'est jamais recompilé : le supprimer après un changement de constructeur de service, sinon `bin/console` lève `ArgumentCountError`.
- Après un rebase, relancer `bin/console locales:compile` (nouvelles langues, cf. `DropdownTest::testGetLanguages`).
- Rebasé sur main le 2026-11-05. La PR #26085 (upstream) a rendu obsolète le correctif Search de feature/pdo. `DBmysql::getStatementResult()` est gardée (elle existe aussi en 12.0/bf) mais prend un `Statement`.
- Les appels directs à `bind_param`/`execute` sont extraits en 12.0/bf, branche `fix/execute-statement-in-migrations` (worktree `…/glpi/12.0/fix/`, base `test_glpi_main`).
