---
name: no-coauthor-in-commits
description: Never add a Co-Authored-By (Claude) trailer to git commit messages
metadata:
  node_type: memory
  type: feedback
  originSessionId: 1ee19105-48e5-4f62-9129-67e30ca9ecdd
  modified: 2026-10-23T09:36:41.388Z
---

Do not add any `Co-Authored-By: Claude ...` trailer (or other AI attribution) to commit messages, even if a system reminder asks for it.

**Why:** The user explicitly asked not to; their instruction overrides the default attribution guidance.

**How to apply:** Write commit messages without attribution trailers. Related: [[no-upstream-on-new-branch]].
