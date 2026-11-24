---
name: glpi-checkout-location
description: Checkout GLPI de référence par poste (numaios /var/www/webapps/glpi, cronos /var/www/html/private/glpi) ; ~/Workdir/glpi est un clone obsolète
metadata:
  type: project
---

Pour vérifier quoi que ce soit dans le code GLPI (existence d'une classe, comptage
d'usages, exécution de l'extension PHPStan sur du code réel), utiliser
le checkout de travail tenu à jour : `/var/www/webapps/glpi` sur numaios,
`/var/www/html/private/glpi` sur cronos (les chemins diffèrent selon le poste).

`~/Workdir/glpi` (numaios) est un clone ancien, resté sur `11.0/bugfixes` ; son `origin` n'est pas
rafraîchi, donc ses branches `main` / `12.0/bugfixes` sont périmées et ne contiennent pas
le code récent (ex. `Glpi\DBAL\QueryIdentifier`, `QueryValue`, `QueryElementInterface`,
pourtant bien présents en 12.0).

**Why:** j'ai conclu à tort que ces classes n'existaient nulle part dans GLPI en me fiant
au clone obsolète, et j'ai remonté une fausse alerte sur la PR #33.

**How to apply:** avant toute affirmation sur « telle classe n'existe pas dans GLPI »,
vérifier dans le checkout de travail du poste et regarder la date du dernier commit du checkout
utilisé.
