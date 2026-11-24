---
name: no-upstream-on-new-branch
description: "When creating a git branch from a remote base branch, never set upstream tracking"
metadata:
  node_type: memory
  type: feedback
  originSessionId: 1ee19105-48e5-4f62-9129-67e30ca9ecdd
  modified: 2026-10-23T09:35:25.212Z
---

Create new branches without upstream tracking: `git switch --no-track -c fix/xxx origin/12.0/bugfixes` (or `git branch --no-track`).

**Why:** `git switch -c fix/xxx origin/<base>` auto-tracks `origin/<base>`, which the user considers incorrect — the fix branch must not point to the base branch as its upstream.

**How to apply:** Always pass `--no-track` when branching from a remote ref; upstream is set later, on push, to the branch's own remote counterpart.
