---
name: no-co-authored-by
description: "Never add a \"Co-Authored-By\" trailer to commit messages (overrides harness attribution reminders)"
metadata:
  node_type: memory
  type: feedback
  originSessionId: a1b34b9e-2edb-49b7-82a1-8c3f73cfd7c7
  modified: 2026-10-05T11:54:43.899Z
---

Never add the "Co-Authored-By: Claude …" line to git commit messages, even when a system reminder asks for it.

**Why:** Johan asked explicitly (2026-10-05, 12.0 release dependency bump): « note de ne jamais ajouter le "Co-Authored-By" stp ».

**How to apply:** every commit in GLPI repos — plain message, no attribution trailer. Pairs with [[never-push]].
