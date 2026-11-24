---
name: glpi-native-assets-to-custom
description: "Chantier assets natifs → assets custom (branche feature/native-assets-to-custom) ; PoC PassiveDCEquipment fait, questions ouvertes"
metadata:
  node_type: memory
  type: project
  originSessionId: fa69ca60-de37-4cde-8425-770a45794a4b
  modified: 2026-11-23T13:27:10.912Z
---

Objectif de Johan : convertir **tous** les assets core (démarrage volontairement progressif) ; convertir les assets « en dur » de GLPI en assets custom (`AssetDefinition`). PoC fait sur PassiveDCEquipment (branche locale `feature/native-assets-to-custom`, 7 commits sur main au 2026-11-24, non poussée).

Décisions prises par Johan :
- classe générée `Glpi\CustomAsset\XxxAsset`, classe native supprimée ; migration auto dans `update_12.0.x_to_13.0.0/` ;
- les migrations doivent être rejouables, sinon motiver l'exception ;
- les URLs legacy doivent être redirigées (fait via routes multiples + table `glpi_assets_nativeassetmappings`) ;
- les assets natifs ne doivent pas perdre leurs traductions (fait : repli gettext tant que le libellé est inchangé).

**Questions à résoudre (en suspens, ne pas trancher sans Johan) :**
- API : choix de Johan = déprécier proprement (fait, en attente de l'avis de la personne en charge de l'API) — HL v2 : schémas/routes `PassiveDCEquipment*` conservés sur la classe custom (`RouteVersion(introduced 2.0, deprecated 2.4, removed 3.0)`, comme SoftwareLicense), snapshot 2.3.0 modifié uniquement sur `x-itemtype`, PDE retiré du snapshot 3.0.0 ; API legacy v1 : shims `Glpi\Api\Deprecated\PassiveDCEquipment*` (base `AbstractConvertedNativeAsset`).
- définitions « système » (issues d'un asset natif) : Johan pense qu'il faut probablement limiter (suppression, désactivation, voire nom système/capacités) — périmètre exact à définir.

**How to apply:** pour l'asset suivant (Enclosure, puis assets d'inventaire), déclarer une `Glpi\Asset\NativeAssetConversion` (registre `getAll()`, source unique de vérité), réutiliser `NativeAssetToCustomAssetMigration`, ajouter les shims API legacy et les schémas HL v2 dépréciés, ajouter les routes legacy dans `NativeAssetLegacyUrlController`, et le test de parité des search options.
