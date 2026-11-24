---
name: glpi-no-indepnet-copyright
description: "GLPI — ne jamais mettre le copyright INDEPNET dans l'en-tête d'un nouveau fichier"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 9a6715ef-8ead-4f5e-82a7-75797fde5233
  modified: 2026-09-04T06:47:10.226Z
---

Dans GLPI, l'en-tête de licence d'un **nouveau** fichier ne doit **jamais** contenir
la ligne `@copyright 2003-2014 by the INDEPNET Development Team.` — seule
`@copyright 2015-<année> Teclib' and contributors.` est conservée. Les fichiers
existants la gardent telle quelle.

**Why:** ce copyright ne couvre que le code hérité de l'équipe INDEPNET ; l'apposer
sur du code neuf est une attribution fausse. Le piège est de créer un fichier en
copiant l'en-tête d'un fichier voisin, qui lui la porte légitimement.

**How to apply:** en créant un fichier GLPI (src/, tests/, …), partir de l'en-tête
d'un voisin puis supprimer la ligne INDEPNET. Rien ne l'impose côté outillage :
`.php-cs-fixer.dist.php` n'a pas de règle `header_comment`, donc la CI ne le
rattrapera pas.
