---
name: port-commit-author
description: "Portage d'une PR sur une autre branche : demander à Johan qui doit être l'auteur du commit avant de commiter"
metadata:
  node_type: memory
  type: feedback
  originSessionId: 9168f398-4fba-460c-8453-77ca6447835e
  modified: 2026-11-02T08:15:28.171Z
---

Quand je porte une PR d'un autre contributeur sur une autre branche (ex. #26149 de 11.0/bf vers 12.0/bf), je demande à Johan qui doit être l'auteur du commit avant de commiter. Je ne choisis pas seul entre lui et l'auteur de la PR d'origine.

**Why:** pour #26149, j'avais repris l'auteur de la PR et Johan a corrigé (« l'auteur, c'est moi »). Il a ensuite précisé que les portages sont rares et qu'il faut demander, sans fixer de règle.

**How to apply:** avant le commit d'un portage, poser la question (AskUserQuestion). Voir aussi [[no-co-authored-by]].
