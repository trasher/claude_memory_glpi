---
name: glpi-phpstan-extension-repo
description: "Les règles PHPStan GLPI ont vocation à vivre dans glpi-project/phpstan-glpi (checkout local ~/Workdir/phpstan-glpi) ; conventions de portage et pièges"
metadata:
  type: project
---

Une règle PHPStan prototypée dans GLPI (`tools/src/PHPStan/Rules`, cf.
[[glpi-phpstan-rawsql-ratchet]]) finit dans le paquet **`glpi-project/phpstan-glpi`**,
checkout local **`~/Workdir/phpstan-glpi`** (déjà requis par GLPI en `require-dev`,
inclus via `vendor/glpi-project/phpstan-glpi/extension.neon`).

Conventions du paquet, à respecter au portage :
- namespace `PHPStanGlpi\Rules`, `declare(strict_types=1)`, propriétés explicites
  (pas de promotion), fonctions internes préfixées `\`.
- **aucune classe GLPI n'est importable** : les FQCN (`Glpi\DBAL\QueryExpression`, …)
  sont des chaînes ; un `@var array<class-string, …>` fait échouer PHPStan level max.
- garde de version via `GlpiVersionResolver` injecté
  (`version_compare(..., '12.0.0-dev', '<') → return []`), + section README
  « > Since GLPI 12.0. » et enregistrement dans `extension.neon`
  (`services:` si la règle prend `%treatPhpDocTypesAsCertain%`, sinon `rules:`).
- tests : `RuleTestCase` + fixtures dans `tests/data/<Rule>/`, **plus** un test
  `tests/IgnoredGlpiVersion/Rules/` (le trait itère *tous* les fichiers du dossier de
  fixtures → n'y mettre que des `.php`). Les classes GLPI absentes se stubbent via un
  neon `scanFiles` passé par `getAdditionalConfigFiles()`.
- `.php-cs-fixer` applique `@PHP84Migration` alors que `composer.json` exige php >= 8.2 :
  il réécrit `(new X())->m()` en syntaxe 8.4 illégale sur 8.2 → passer par une variable
  locale.
- outillage local possible sans docker : `composer install` puis
  `vendor/bin/phpunit`, `vendor/bin/phpstan`, `vendor/bin/php-cs-fixer check`.

**Why:** la règle « SQL brut » (`ForbidNonLiteralSqlExpressionRule`) a été portée ainsi le
2026-09-15, ce qui permet d'y ajouter de vrais tests unitaires, impossibles côté GLPI.

**How to apply:** garder le **message d'erreur identique au mot près** lors du portage,
sinon la baseline dédiée côté GLPI (`.phpstan-baseline.rawSql.php`) ne matche plus.
Vérifier l'équivalence en symlinkant `vendor/glpi-project/phpstan-glpi` vers le checkout
local et en comparant les sorties `--error-format=json` des deux montages.
