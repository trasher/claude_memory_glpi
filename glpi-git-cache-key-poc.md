---
name: glpi-git-cache-key-poc
description: PoC sur main — révision git dans GLPI_FILES_VERSION au lieu de forcer le mode debug quand .git existe (suite PR #26300)
metadata:
  type: project
---

PoC démarré le 2026-11-26, worktree `/var/www/html/private/glpi-git-cache-key`, branche `poc/git-aware-cache-key` (depuis origin/main, sans upstream), commits ba08f9dc3d puis 69a3ed29b0, non poussés.

Origine : PR #26300 (Curtis) retire le test `.git` de `Environment::shouldExpectResourcesToChange()` pour ses benchs ; Cédric objecte que les installs git en prod (~1,15 %) ont alors un container Symfony périmé après `git pull` sur une branche (même GLPI_VERSION, fichier `version/x.y.z` vide → même répertoire de cache). Benchs de Curtis : un `git worktree` suffit (`.git` y est un fichier, `is_dir` échoue).

Choix du PoC :
- `Glpi\Application\GitCheckout` sans dépendance (lit HEAD, refs loose, packed-refs, worktrees via `commondir`) ; 12 premiers caractères du hash ajoutés à GLPI_FILES_VERSION.
- `shouldExpectResourcesToChange()` ne dépend plus que de l'environnement (paramètre `$root_dir` supprimé).
- Test `.git` (file_exists) conservé dans ResourcesChecker et pour le résumé d'intégrité de Config.

**Why:** séparer « est-ce une prod ? » (env) de « le cache suit-il le code ? » (clé de cache).
Suite (2026-11-26, décision de Johan) : GLPI_FILES_VERSION ajouté au namespace des caches Redis/Memcached ; option `cache:clear --obsolete` qui supprime les répertoires `<version>-<env>` des autres versions ; menu en session périmé jusqu'à reconnexion accepté.

**How to apply:** limites restantes — anciennes clés Redis/Memcached orphelines (sans TTL), reftable non géré (revision null → ancien comportement de clé). Viser main, pas 11.0/bf.
