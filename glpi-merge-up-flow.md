---
name: glpi-merge-up-flow
description: "Remontées 10.0 → 11.0 → main dans GLPI ; la PR ne sert que de véhicule CI, l'atterrissage est un ff-push local"
metadata: 
  node_type: memory
  type: project
  originSessionId: 4a06ba8f-55f7-40f8-8159-fa76aee8c842
  modified: 2026-09-01T08:21:18.276Z
---

Les remontées de correctifs GLPI (`10.0/bugfixes` → `11.0/bugfixes` → `main`) passent par
une PR, mais **cette PR ne doit jamais être mergée par le bouton GitHub**. Le script
`~/bin/glpi-merge-up` (start / status / locks / finish / land) implémente le flux ;
worktree dédié `/var/www/projects/teclib/glpi/merge-up`.

**Why:** le dépôt a `allow_merge_commit: false` — seuls squash et rebase sont proposés, et
aucun des deux ne rend les commits de la branche source ancêtres de la cible : tous les
conflits déjà résolus reviendraient à la remontée suivante. `enforce_admins: false` sur les
protections de branche permet donc de pousser localement le commit de merge exactement testé
par la CI ; GitHub close alors la PR en « Merged ».

**How to apply:** toujours 10 → 11 avant 11 → main, séquentiellement (jamais 10 → main).
Vérifier après atterrissage que `git rev-list --count origin/<source> ^origin/<cible>` vaut 0
et que le commit a 2 parents. Attention à `commit-authorship.yml` : il inspecte *tous* les
commits de la PR (auteurs et trailers `Co-authored-by`) et n'est pas contournable côté PR —
un seul commit historique refusé bloque toute la remontée. Voir [[no-ungrounded-magic-numbers]]
pour le style de garde attendu dans ce genre d'outillage.
