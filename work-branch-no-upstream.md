---
name: work-branch-no-upstream
description: "Les branches de travail créées doivent n'avoir aucun upstream (git worktree add/branch --no-track)"
metadata:
  node_type: memory
  type: feedback
  originSessionId: e1507916-82cd-44b6-baaf-45879751532f
  modified: 2026-10-13T12:26:48.979Z
---

Quand je crée une branche de travail (worktree ou non) à partir d'une branche distante, elle ne doit pas suivre d'upstream : utiliser `--no-track` (`git worktree add --no-track -b <branche> <chemin> origin/<base>`), ou `git branch --unset-upstream` après coup.

**Why:** Johan l'a demandé explicitement (fix/25929 créé avec suivi automatique de origin/12.0/bugfixes). Un upstream sur la branche de base risque un push ou un pull accidentel vers la mauvaise branche.

**How to apply:** à chaque création de branche à partir de `origin/...`, ajouter `--no-track`. Lié à [[never-push]] et [[glpi-merge-up-flow]].
