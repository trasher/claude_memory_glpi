---
name: no-ungrounded-magic-numbers
description: "When fixing bugs, don't add special-case handling/magic numbers not grounded in the codebase or a concrete constraint"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 2f672f33-59a5-4654-94ce-2a62f5ade2e4
---

When writing a fix, do not introduce magic numbers or special-case branches that aren't grounded in the codebase or a concrete, verifiable constraint. In the networkport speed fix I added a guard `ifspeed != 4294967295` justified only by my SNMP IF-MIB knowledge; the user pushed back ("where did you find that? it's not handled in GLPI"). It was both arbitrary (nothing in GLPI handled it) and incomplete (missed the `4294967295000000` variant present in test fixtures). The correct guard was grounded in the actual constraint: the signed `int` column max (`2147483647`) it gets written to.

**Why:** Ungrounded special-casing looks authoritative but is often wrong, and hides the real constraint.

**How to apply:** Tie guards/bounds to something concrete (a column type, an existing constant, a documented requirement). If a value's handling comes only from outside knowledge, either verify it against the codebase or flag it explicitly to the user instead of baking it in silently.
