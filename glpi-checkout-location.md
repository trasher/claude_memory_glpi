---
name: glpi-checkout-location
description: Le checkout GLPI de référence est /var/www/webapps/glpi, pas ~/Workdir/glpi (clone obsolète)
metadata:
  type: project
---

Pour vérifier quoi que ce soit dans le code GLPI (existence d'une classe, comptage
d'usages, exécution de l'extension PHPStan sur du code réel), utiliser
`/var/www/webapps/glpi` — checkout de travail, tenu à jour.

`~/Workdir/glpi` est un clone ancien, resté sur `11.0/bugfixes` ; son `origin` n'est pas
rafraîchi, donc ses branches `main` / `12.0/bugfixes` sont périmées et ne contiennent pas
le code récent (ex. `Glpi\DBAL\QueryIdentifier`, `QueryValue`, `QueryElementInterface`,
pourtant bien présents en 12.0).

**Why:** j'ai conclu à tort que ces classes n'existaient nulle part dans GLPI en me fiant
au clone obsolète, et j'ai remonté une fausse alerte sur la PR #33.

**How to apply:** avant toute affirmation sur « telle classe n'existe pas dans GLPI »,
vérifier dans `/var/www/webapps/glpi` et regarder la date du dernier commit du checkout
utilisé.
