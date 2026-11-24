---
name: glpi-release-bump
description: Préparation de release GLPI — commit « Bump version » minimal ; le post-release est automatisé
metadata:
  node_type: memory
  type: project
  originSessionId: 16ac5ee0-a9c7-4026-92b5-b3e9500318e7
  modified: 2026-10-28T09:49:33.589Z
---

Commit de release « Bump version » : seulement `GLPI_VERSION` (retirer `-dev`) dans `src/autoload/constants.php` et date de la section dans `CHANGELOG.md` (sections vides conservées).

Le post-release (version suivante `-dev`, renommage `version/`, dump SQL `glpi-X-empty.sql`, nouvelle section changelog) est une action automatique : ne pas la proposer ni la faire.

**Why:** Johan l'a précisé lors de la release 12.0.1 (2026-10-28).
**How to apply:** se limiter au commit de release ; voir [[never-push]].
