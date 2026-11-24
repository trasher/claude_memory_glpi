---
name: glpi-branch-versions
description: "Correspondance branches GLPI ↔ versions (main = v13, 12.0/bugfixes = v12…) et abréviation « 12.0/bf »"
metadata:
  node_type: memory
  type: project
  originSessionId: e4b94f55-a9a4-4983-bfbe-9ccbd644caec
  modified: 2026-10-19T06:34:10.626Z
---

Branches GLPI (état au 2026-10-19) : `main` = v13 (13.0.0-dev), `12.0/bugfixes` = v12, `11.0/bugfixes` = v11, etc. Johan abrège souvent `X.Y/bugfixes` en `X.Y/bf`.

**Why:** la version d'une branche n'est pas déductible de son nom pour `main`, et sert à caler les gardes de version (ex. rector-glpi : dossiers `GlpiNNNx` et `version_compare` dans `config/sets/default.php`).

**How to apply:** « activer à partir de la prochaine majeure » = version de `main`. À revérifier après chaque release majeure (main avance d'un cran). Voir [[glpi-merge-up-flow]].
