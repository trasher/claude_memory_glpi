---
name: glpi-pull-request-target-default-branch
description: "Workflows GitHub en pull_request_target → branche par défaut du dépôt (12.0/bugfixes actuellement), pas 11.0"
metadata:
  node_type: memory
  type: project
  originSessionId: b87f17f2-634c-4b14-aaa8-cbb4a1c12727
  modified: 2026-11-03T07:41:26.309Z
---

Depuis fin 2025, GitHub lit les workflows `pull_request_target` sur la **branche par défaut** du dépôt, quelle que soit la cible de la PR. Sur glpi-project/glpi, la branche par défaut est `12.0/bugfixes` (au 2026-11-03).

**Why:** j'avais créé un workflow d'étiquetage sur 11.0/bugfixes en pensant à une remontée ; Johan m'a corrigé (PR #26200).

**How to apply:** un nouveau workflow de ce type se base sur la branche par défaut (à vérifier avec `gh repo view --json defaultBranchRef`), sans passer par le [[glpi-merge-up-flow]]. Voir aussi [[glpi-branch-versions]].
