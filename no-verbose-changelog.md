---
name: no-verbose-changelog
description: "Don't add CHANGELOG.md entries to GLPI changes by default; wait until a reviewer asks for one"
metadata:
  node_type: memory
  type: feedback
  originSessionId: 5ff77576-a478-484f-929f-03c687b73aa0
  modified: 2026-11-09T10:02:57.393Z
---

Do not add entries to GLPI's `CHANGELOG.md` by default, and never verbose ones. Leave it untouched and let a reviewer request an entry if one is really needed.

**Why:** the user is in the GLPI core team; the "update the changelog" rule (also pushed by review bots like Qodo) targets external contributors, not the core team. The user removed the entries I had added ("Clean changelog" commit on PR #26254).

**How to apply:** when fixing or porting GLPI changes, don't touch `CHANGELOG.md`, even for new public methods or API/schema changes; don't treat a bot's "changelog missing" remark as something to fix. If an entry is explicitly requested, keep it to one short line.
