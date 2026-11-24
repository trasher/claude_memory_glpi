---
name: never-push
description: "Ne jamais faire `git push` ; commiter suffit, la publication reste la décision de Johan"
metadata:
  node_type: memory
  type: feedback
---

**Ne jamais exécuter `git push`**, sous aucune forme (`push`, `push -u`, `push --force`,
`gh pr create` qui pousse implicitement), même sur une branche de travail déjà publiée,
même quand la tâche consiste explicitement à corriger une PR ouverte. S'arrêter au commit
local et le dire ; c'est Johan qui pousse.

**Why:** pousser est une action sortante et irréversible du point de vue des autres — ça
déclenche la CI, notifie les reviewers et les bots (Qodo relance une review à chaque push),
et fige publiquement un travail que Johan veut relire avant. Une demande de « corrige ça »
ou un « allez, go » autorise le changement, pas sa publication.

**How to apply:** finir par `git commit`, puis annoncer ce qui est prêt à être poussé et
laisser la main. Seule exception : un ff-push explicitement piloté par le flux décrit dans
[[glpi-merge-up-flow]], et seulement quand Johan le demande à ce moment-là.
