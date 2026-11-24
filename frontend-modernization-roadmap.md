---
name: frontend-modernization-roadmap
description: "Ongoing effort to remove old vendored JS libs from glpiinventory, migrate to Twig, then a11y"
metadata: 
  node_type: memory
  type: project
  originSessionId: 83b9a704-976e-4367-b743-f04cfa8d3255
---

Chantier de modernisation front-end du plugin `glpiinventory` (branche `feature/d3`,
démarré le 2026-07-09). But : sortir **une par une** les vieilles libs JS de `public/lib/`,
retravailler **page par page** vers des templates **Twig**, puis passe **accessibilité**.

Cible : GLPI 11.0.x, qui fournit déjà Tabler 1.x, Vue 3 et **echarts 5** (pas de d3). Twig via
namespace auto `@glpiinventory`. Travail en **commits atomiques** (1 sujet/commit) pour la revue.

Séquencement retenu (plan détaillé : `~/.claude/plans/le-plugin-glpiinventory-utilise-shiny-kahn.md`) :
1. jQuery mort + chaîne nvd3/stats (code mort) — **FAIT** (PR ; commits `ae2941848`, `e762cd3b1`).
2. timeslot (d3 → grille HTML native + Twig) — **FAIT** (commit `c72f69b05`). Choix : grille HTML
   native (pas echarts, meilleure a11y), page complète migrée (form principal via generic_show_form,
   entrée+liste+grille via `templates/forms/timeslot/entry.html.twig`, logique grille = `getWeekGrid`).
3. task/taskjob (lazy.js + mustache + d3 dans `taskjobs.js`) — **EN COURS**. Découpé :
   - 3a formulaires : **FAIT**. form tâche (commit `b9836ca3f`, generic_show_form + macros, bouton
     Force start via params.addbuttons) ; **form job** (commit `158679034`) : template autonome
     `templates/forms/taskjob.html.twig` (PAS generic_show_form car form custom `#taskjobs_form`
     posté à taskjob.form.php, injecté en ajax dans l'onglet), macros de champ, tous les hooks JS
     préservés (rand du dropdown method → register_update_method, #method_selected, boutons
     cibles/acteurs .show_moduletypes, slots moduletypes/moduleitems, listes + clear/delete,
     boutons cancel/purge/update). `task_method_change` déplacé inline→`taskjobs.js`,
     `getAddItemtypeButton` supprimé. Vérifié en réel via curl authentifié sur
     `ajax/taskjob_form.php` (edit+new, HTTP 200, rand match OK). Bug attribut Twig évité :
     `style="display:none"` conditionnel via `{% if %}` (un ternaire renvoyant la string échappe
     les guillemets en `&quot;`).
   - 3b écran de suivi (`taskview::showJobLogs` + gros de taskjobs.js) : décision = **fragments Twig
     rendus serveur + poller JS léger** (façon Ajax::updateItem), donut succès/erreur en **Tabler/SVG
     serveur** (pas de lib), supprime Mustache + Lazy. echarts dispo globalement (`window.echarts`) si besoin.
     Découpage retenu (user, 2026-07-15) : **fusion 2+3** en un gros commit (rendu serveur arbre+counters+
     agents, drop Mustache/d3/lazy d'un coup), puis poller léger, puis retrait libs.
     **Commit donut FAIT** (`fff1dfae8`) : `PluginGlpiinventoryTaskView::getProgressbarSvg()` +
     `templates/monitoring/donut.html.twig` ; getJoblogs attache `progressbar` (SVG) par cible ; poller
     injecte le SVG dans `div.progressbar` (toggle `.empty`). Supprime create_progressbar/update_progressbar
     (d3) ; d3 reste chargé (utilisé par la liste agents). Vérifié via endpoint ajax (task 1, success=1 →
     donut correct).
     **3b FAIT** (fusion 2+3, UX simplifiée choisie par user). Commits :
     `0df3856f5` rendu serveur complet arbre (tasks>jobs>targets>counters>donut>agents) via
     `PluginGlpiinventoryTaskView::renderJobLogs()` + `templates/monitoring/tasks.html.twig` ;
     `ajaxGetJobLogs` renvoie du HTML (plus de JSON) ; le poller fait `$('.tasks_block').html(data)`.
     Agents affichés direct (groupés par statut), counters = filtres client show/hide, épinglage→logs
     (jobstates_logs.php) dans table construite en JS (template literal, sans lib), relance simple+masse.
     taskjobs.js perd ~800 lignes (d3/mustache/lazy : update_logs, compute_data, agents_chart,
     display/update_agents_view, dispatch, create_block, init_templates → remplacés par
     filter_agents/toggle_agent_logs/render_runs/restart_jobs + handlers délégués).
     `337e0aebf` retire d3+mustache+lazy de setup.php + supprime les 3 dossiers `public/lib/` (~836K).
     **d3 global retiré** (step 5) car plus aucun consommateur (vérifié : deploypackage n'utilise PAS d3).
     **Vérif navigateur RÉELLE OK** (zéro erreur console) : rendu, filtre, épinglage+logs, checkbox→relance.

     **ENV & VÉRIF (crucial pour reprendre)** : instance accessible en navigateur sur
     **`http://glpi.routed.localhost`** (routing + assets OK ; le vhost `glpi.localhost` a un DocumentRoot
     mal placé → NE PAS l'utiliser en navigateur). Chromium système présent ; le MCP Playwright veut le
     canal `chrome` (absent) → je pilote via script node CommonJS `require('/var/www/webapps/glpi/node_modules/playwright')`,
     `chromium.launch({args:['--no-sandbox']})`, login form `/front/login.php` (glpi/glpi), puis
     `/plugins/glpiinventory/front/taskjob.php`. Scripts de pilotage dans le job tmp (drive.cjs,
     interact_after.cjs). DB `glpi_11`, user glpi (mot de passe : voir `config/config_db.php` de l'instance, jamais recopié ici). Données test : task 1
     (collect, 1 agent success), task 2 (deploy). NB: front/deploypackage.php & front/iprange.php
     renvoient 500 dans cet env (préexistant, sans rapport). Reste mineur 3c : taskjobs.js encore chargé
     (vestigial) sur iprange.form.php (gate setup.php ~280) — non retiré (iprange 500 en env, non vérifiable).
   - 3c retrait chargement lazy.js/mustache.js dans setup.php + dossiers + chargement vestigial de
     taskjobs.js sur iprange.form.php.
   Data JSON du suivi = `ajax/taskjob_logs.php` → `PluginGlpiinventoryTask::getJoblogs()`.
4. deploypackage (ExtJS + REDIPS.drag, d3 inline ; `deploypackage.class.php` = 212 echo).
5. Clôture : retrait du `d3` global (`setup.php`) une fois 2/3/4 finis.

**Why:** effort multi-sessions, itératif « au moment venu » ; connaître l'ordre et la cible évite de
replanifier. **How to apply:** reprendre l'incrément suivant du plan, réutiliser les composants core
(`components/datatable.html.twig`, `fields_macros.html.twig`) et les classes déjà migrées comme
modèles (`inc/iprange.class.php`, `inc/agentmodule.class.php`, `inc/config.class.php`).
