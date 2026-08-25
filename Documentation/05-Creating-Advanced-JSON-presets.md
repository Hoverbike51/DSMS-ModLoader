>[!TIP]
> [DSMS Preset Studio](https://github.com/Hoverbike51/DSMS-Preset-Studio) was released to simplify the creation of presets

## Choose the smallest suitable JSON variant

Do not copy every optional property into every preset. Start with the smallest variant that solves the asset's real structure.

| Asset structure or correction | Recommended variant |
|---|---|
| Existing outfit mesh, new Material Instances only | Costume retexture |
| Existing weapon mesh, new Material Instances only | Weapon retexture with `WeaponMaterialsOnly` |
| One replacement weapon component | `WeaponPath` |
| Several replacement weapon components | `WeaponPaths` with one exact `ComponentMatch` per mesh |
| Body plus separate hair, head or cloth mesh | `AuxiliaryMeshPath` |
| Body plus a dedicated parallel outline mesh | `BodyOutlinePath` and, when needed, `BodyOutlineMaterials` |
| Auxiliary mesh has its own Physics Asset | Add `AuxiliaryPhysicsAssetPath` |
| Old outline or costume component remains visible | `HiddenComponentMeshMatches` |
| A native secondary body component must receive another mesh | `LinkedBodyComponentMeshMatches` and, when necessary, `LinkedBodyReplacementPath` |
| Costume has a dedicated no-mask Skeletal Mesh | Use that mesh as `BodyPath`; do not attempt to hide arbitrary material sections |
| Costume needs makeup, eyes or face changes | `FaceMaterials` and, when needed, `FaceMorphTargets` |
| Body shape is driven by Morph Targets | `BodyMorphTargets` |

`AuxiliarySpawnOnly` is intentionally not documented as a release recipe. Its Charlotte ALT experiment has not been validated and must not be treated as a stable construction method.

## 5.1 Costume retexture: keep the mesh and replace its materials

A retexture preset still needs a valid `BodyPath`. Point it to the original body mesh that the retexture was authored for, then reference only cooked **Material Instances** from your PAK. Keep the matching physics assets so the original costume remains animated correctly.

```json
{
  "Version": 3,
  "UniqueID": "example_astria_base_red_retexture",
  "DisplayName": "Astria - Red Main Outfit [Custom]",
  "Type": "Custom",
  "TargetCharacterID": "Astria",
  "Requirements": ["None"],

  "PhysicsAssetPath": "/Game/Art/Character/Player/DS_Astria/Meshs/ch_Astria_mesh_body_PhysicsAsset.ch_Astria_mesh_body_PhysicsAsset",
  "PhysicsAnimBlueprintPath": "/Game/Design/DsCharacter/DsPhysics/PC/Astria/DsABP_Astria_Physics.DsABP_Astria_Physics_C",
  "FaceMorphPath": "/Game/Art/Character/Player/DS_Astria/Meshs/ch_Astria_ingame_face_mesh.ch_Astria_ingame_face_mesh",

  "BodyPath": "/Game/Art/Character/Player/DS_Astria/Meshs/ch_Astria_mesh_body.ch_Astria_mesh_body",
  "BodyClearMaterialOverrides": true,
  "BodyMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/MODS/ExampleAuthor/Astria_Red/Materials/MI_Astria_Red_Body.MI_Astria_Red_Body"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/MODS/ExampleAuthor/Astria_Red/Materials/MI_Astria_Red_Skin.MI_Astria_Red_Skin"
    }
  ],

  "IconPath": "/Game/MODS/ExampleAuthor/Astria_Red/Textures/T_Astria_Red_Icon.T_Astria_Red_Icon"
}
```

Important:

- The retexture must preserve the original material-slot order.
- Include every slot that your mod intends to override; undeclared slots retain the mesh default after overrides are cleared.
- Omit `FacePath`, `FaceMaterials`, `FaceMorphTargets` and `BodyMorphTargets` when the preset does not need them.
- `FaceMorphPath` remains required for `Custom` and `Costume` presets in JSON v3.

## 5.2 Weapon retexture: keep every weapon mesh

Use `WeaponMaterialsOnly: true` when the original weapon geometry must remain unchanged. Do not add `WeaponPath` or `WeaponPaths` to this variant.

```json
{
  "Version": 3,
  "UniqueID": "example_theresia_gold_weapon_retexture",
  "DisplayName": "Theresia - Gold Weapon [Custom]",
  "Type": "Weapon",
  "TargetCharacterID": "Theresia",
  "Requirements": ["None"],

  "WeaponMaterialsOnly": true,
  "WeaponClearMaterialOverrides": true,
  "WeaponMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/MODS/ExampleAuthor/Theresia_Gold/Materials/MI_Theresia_Gold_WP.MI_Theresia_Gold_WP"
    }
  ],

  "IconPath": "/Game/MODS/ExampleAuthor/Theresia_Gold/Textures/T_Theresia_Gold_Icon.T_Theresia_Gold_Icon"
}
```

This recipe is appropriate when the weapon consists of one or several native components but they all use the intended slot index. If unrelated weapon components also expose that slot, inspect the UE4SS log before release and use a complete weapon replacement when the retexture cannot be targeted safely.

## 5.3 Weapon replacement: single mesh and multi-part weapons

For a single detected weapon component, use `WeaponPath`:

```json
{
  "Version": 3,
  "UniqueID": "example_single_weapon",
  "DisplayName": "Example - Custom Sword [Custom]",
  "Type": "Weapon",
  "TargetCharacterID": "ExampleCharacter",
  "Requirements": ["None"],
  "WeaponPath": "/Game/MODS/ExampleAuthor/CustomSword/Mesh/SK_CustomSword.SK_CustomSword",
  "WeaponClearMaterialOverrides": true,
  "WeaponMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/MODS/ExampleAuthor/CustomSword/Materials/MI_CustomSword.MI_CustomSword"
    }
  ],
  "IconPath": "/Game/MODS/ExampleAuthor/CustomSword/Textures/T_CustomSword_Icon.T_CustomSword_Icon"
}
```

For a chained or multi-part weapon, every entry needs a unique `ComponentMatch` taken from the component names reported in the UE4SS log:

```json
{
  "Version": 3,
  "UniqueID": "example_astria_four_part_weapon",
  "DisplayName": "Astria - Four-Part Weapon [Custom]",
  "Type": "Weapon",
  "TargetCharacterID": "Astria",
  "Requirements": ["None"],
  "WeaponPaths": [
    {
      "ComponentMatch": "DsWP_Astria_Default",
      "WeaponPath": "/Game/MODS/ExampleAuthor/Astria_WP/Mesh/SK_Astria_WP_General.SK_Astria_WP_General"
    },
    {
      "ComponentMatch": "DsWP_Astria_C",
      "WeaponPath": "/Game/MODS/ExampleAuthor/Astria_WP/Mesh/SK_Astria_WP_Body.SK_Astria_WP_Body"
    },
    {
      "ComponentMatch": "DsWP_Astria_Enable",
      "WeaponPath": "/Game/MODS/ExampleAuthor/Astria_WP/Mesh/SK_Astria_WP_Part01.SK_Astria_WP_Part01"
    },
    {
      "ComponentMatch": "DsWP_Astria_Disable",
      "WeaponPath": "/Game/MODS/ExampleAuthor/Astria_WP/Mesh/SK_Astria_WP_Part02.SK_Astria_WP_Part02"
    }
  ],
  "WeaponClearMaterialOverrides": true,
  "WeaponMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/MODS/ExampleAuthor/Astria_WP/Materials/MI_Astria_WP.MI_Astria_WP"
    }
  ],
  "IconPath": "/Game/MODS/ExampleAuthor/Astria_WP/Textures/T_Astria_WP_Icon.T_Astria_WP_Icon"
}
```

Do not invent `ComponentMatch` values. A wrong but partially matching name can replace the wrong attached mesh.

## 5.4 Split costume: body plus an auxiliary mesh

Use an auxiliary mesh for a separate hair, head, cape, cloth or similar component. `AuxiliaryPhysicsAssetPath` is optional, but it is required when that separate mesh owns costume-specific physics.

```json
{
  "Version": 3,
  "UniqueID": "example_split_body_and_hair",
  "DisplayName": "Example - Split Body and Hair [Custom]",
  "Type": "Custom",
  "TargetCharacterID": "ExampleCharacter",
  "Requirements": ["None"],

  "PhysicsAssetPath": "/Game/MODS/ExampleAuthor/SplitCostume/Mesh/PHYS_SplitBody.PHYS_SplitBody",
  "PhysicsAnimBlueprintPath": "/Game/Design/DsCharacter/DsPhysics/PC/ExampleCharacter/ABP_Example_Physics.ABP_Example_Physics_C",
  "FaceMorphPath": "/Game/Art/Character/Player/DS_ExampleCharacter/Meshs/SK_Example_IngameFace.SK_Example_IngameFace",

  "AuxiliaryMeshPath": "/Game/MODS/ExampleAuthor/SplitCostume/Mesh/SK_SplitHair.SK_SplitHair",
  "AuxiliaryPhysicsAssetPath": "/Game/MODS/ExampleAuthor/SplitCostume/Mesh/PHYS_SplitHair.PHYS_SplitHair",
  "AuxiliaryClearMaterialOverrides": true,
  "AuxiliaryMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/MODS/ExampleAuthor/SplitCostume/Materials/MI_SplitHair.MI_SplitHair"
    }
  ],

  "BodyPath": "/Game/MODS/ExampleAuthor/SplitCostume/Mesh/SK_SplitBody.SK_SplitBody",
  "BodyClearMaterialOverrides": true,
  "BodyMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/MODS/ExampleAuthor/SplitCostume/Materials/MI_SplitBody.MI_SplitBody"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/MODS/ExampleAuthor/SplitCostume/Materials/MI_SplitSkin.MI_SplitSkin"
    }
  ],
  "IconPath": "/Game/MODS/ExampleAuthor/SplitCostume/Textures/T_SplitCostume_Icon.T_SplitCostume_Icon"
}
```

The auxiliary mesh must be compatible with the leader pose used by the target character. A Physics Asset does not repair an incompatible skeleton or bone hierarchy.

## 5.5 Hide a native outline and install a dedicated body outline

Some characters keep their outline in a separate Skeletal Mesh Component. Replacing only `BodyPath` leaves the old silhouette visible. DSMS 0.7.1 provides a dedicated outline channel parallel to the body. The stable correction is:

1. Match and hide the exact native outline component.
2. Load the replacement body normally.
3. Load the custom outline as `BodyOutlinePath`.
4. Apply outline Material Instances in the exact outline slot order.

```json
{
  "Version": 3,
  "UniqueID": "example_costume_with_custom_outline",
  "DisplayName": "Example - Costume with Custom Outline [Custom]",
  "Type": "Costume",
  "TargetCharacterID": "ExampleCharacter",
  "Requirements": ["None"],

  "PhysicsAssetPath": "/Game/MODS/ExampleAuthor/OutlineCostume/Mesh/PHYS_OutlineCostume.PHYS_OutlineCostume",
  "PhysicsAnimBlueprintPath": "/Game/Design/DsCharacter/DsPhysics/PC/ExampleCharacter/ABP_Example_Physics.ABP_Example_Physics_C",
  "FaceMorphPath": "/Game/Art/Character/Player/DS_ExampleCharacter/Meshs/SK_Example_IngameFace.SK_Example_IngameFace",

  "HiddenComponentMeshMatches": [
    "ch_example_body_mesh_outline.ch_example_body_mesh_outline"
  ],
  "BodyOutlinePath": "/Game/MODS/ExampleAuthor/OutlineCostume/Mesh/SK_OutlineCostume_Outline.SK_OutlineCostume_Outline",
  "BodyOutlineClearMaterialOverrides": true,
  "BodyOutlineMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/MODS/ExampleAuthor/OutlineCostume/Materials/MI_Body_Outline.MI_Body_Outline"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/MODS/ExampleAuthor/OutlineCostume/Materials/MI_Hair_Outline.MI_Hair_Outline"
    }
  ],

  "BodyPath": "/Game/MODS/ExampleAuthor/OutlineCostume/Mesh/SK_OutlineCostume.SK_OutlineCostume",
  "BodyClearMaterialOverrides": true,
  "BodyMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/MODS/ExampleAuthor/OutlineCostume/Materials/MI_OutlineCostume_Body.MI_OutlineCostume_Body"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/MODS/ExampleAuthor/OutlineCostume/Materials/MI_OutlineCostume_Skin.MI_OutlineCostume_Skin"
    }
  ],
  "IconPath": "/Game/MODS/ExampleAuthor/OutlineCostume/Textures/T_OutlineCostume_Icon.T_OutlineCostume_Icon"
}
```

Use the full mesh object name in `HiddenComponentMeshMatches` whenever possible. Broad fragments such as `body`, `hair` or `outline` can match several components and may hide the face, the new costume, or another follower component. Replacing body material slots with an invisible material does **not** hide a separate native outline component. Keep `AuxiliaryMeshPath` for separate hair, head or cloth geometry; do not consume it for the primary body outline when `BodyOutlinePath` is available.

If a costume has no dedicated outline mesh, it is safer to hide the native outline and omit `BodyOutlinePath` than to reuse an incompatible outline retained from the previous costume.

## 5.6 Replace a linked secondary body component

Some Pawns have a persistent secondary body or outline component that must receive a new mesh instead of merely being hidden. Use exact native matches. When `LinkedBodyReplacementPath` is omitted, DSMS assigns the main `BodyPath` mesh to each matched component. When it is present, DSMS assigns that dedicated replacement mesh instead.

```json
{
  "Version": 3,
  "UniqueID": "example_linked_body_replacement",
  "DisplayName": "Example - Linked Component Costume [Custom]",
  "Type": "Costume",
  "TargetCharacterID": "ExampleCharacter",
  "Requirements": ["None"],
  "PhysicsAssetPath": "/Game/MODS/ExampleAuthor/LinkedCostume/Mesh/PHYS_LinkedCostume.PHYS_LinkedCostume",
  "PhysicsAnimBlueprintPath": "/Game/Design/DsCharacter/DsPhysics/PC/ExampleCharacter/ABP_Example_Physics.ABP_Example_Physics_C",
  "FaceMorphPath": "/Game/Art/Character/Player/DS_ExampleCharacter/Meshs/SK_Example_IngameFace.SK_Example_IngameFace",

  "LinkedBodyComponentMeshMatches": [
    "ch_example_mesh_body_outline.ch_example_mesh_body_outline"
  ],
  "LinkedBodyReplacementPath": "/Game/MODS/ExampleAuthor/LinkedCostume/Mesh/SK_LinkedCostume_Outline.SK_LinkedCostume_Outline",

  "BodyPath": "/Game/MODS/ExampleAuthor/LinkedCostume/Mesh/SK_LinkedCostume.SK_LinkedCostume",
  "BodyClearMaterialOverrides": true,
  "BodyMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/MODS/ExampleAuthor/LinkedCostume/Materials/MI_LinkedCostume.MI_LinkedCostume"
    }
  ],
  "IconPath": "/Game/MODS/ExampleAuthor/LinkedCostume/Textures/T_LinkedCostume_Icon.T_LinkedCostume_Icon"
}
```

Use `HiddenComponentMeshMatches` for a component that must disappear. Use `LinkedBodyComponentMeshMatches` for a component that must stay active but receive a replacement mesh. Do not place the same native component in both arrays unless a tested character-specific correction explicitly requires that transition.

## 5.7 Mask and no-mask variants

Create two independent JSON files with different `UniqueID` values. The safest no-mask variant points to a dedicated no-mask Skeletal Mesh:

```json
{
  "Version": 3,
  "UniqueID": "example_costume_no_mask",
  "DisplayName": "Example - Special Costume (No Mask) [Custom]",
  "Type": "Costume",
  "TargetCharacterID": "ExampleCharacter",
  "Requirements": ["None"],
  "PhysicsAssetPath": "/Game/MODS/ExampleAuthor/SpecialCostume/Mesh/PHYS_SpecialCostume.PHYS_SpecialCostume",
  "PhysicsAnimBlueprintPath": "/Game/Design/DsCharacter/DsPhysics/PC/ExampleCharacter/ABP_Example_Special_Physics.ABP_Example_Special_Physics_C",
  "FaceMorphPath": "/Game/Art/Character/Player/DS_ExampleCharacter/Meshs/SK_Example_IngameFace.SK_Example_IngameFace",
  "HiddenComponentMeshMatches": [
    "ch_example_base_body.ch_example_base_body",
    "ch_example_special_mask_outline.ch_example_special_mask_outline"
  ],
  "BodyPath": "/Game/MODS/ExampleAuthor/SpecialCostume/Mesh/SK_SpecialCostume_NoMask.SK_SpecialCostume_NoMask",
  "BodyClearMaterialOverrides": true,
  "BodyMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/MODS/ExampleAuthor/SpecialCostume/Materials/MI_SpecialCostume_NoMask.MI_SpecialCostume_NoMask"
    }
  ],
  "IconPath": "/Game/MODS/ExampleAuthor/SpecialCostume/Textures/T_SpecialCostume_NoMask_Icon.T_SpecialCostume_NoMask_Icon"
}
```

Do not create a no-mask preset by guessing which body material slot contains the mask. A mask can be geometry inside another section, an auxiliary mesh, or a separate outline component. Inspect the original assets first.

## 5.8 Custom Body and Face Outline

Some presets use a standard mesh for the body and face, as well as a body and face outline. If you don't apply them, the pose may not display correctly, or the game may even crash.

```json
{
  "Version": 3,
  "UniqueID": "ExampleCharacter_MainCostume",
  "DisplayName": "Example Character - Main Outfit [Costume Base]",
  "Type": "Costume",
  "TargetCharacterID": "ExampleCharacter",

  "Requirements": [
    "None"
  ],

  "PhysicsAssetPath": "/Game/Example/Character/Meshes/example_body_PhysicsAsset.example_body_PhysicsAsset",
  "PhysicsAnimBlueprintPath": "/Game/Example/Character/Blueprints/ABP_Example_Physics_Costume.ABP_Example_Physics_Costume_C",

  "FaceMorphPath": "/Game/Example/Character/Meshes/example_ingame_face_mesh.example_ingame_face_mesh",
  "FacePath": "/Game/Example/Character/Meshes/example_face_mesh.example_face_mesh",

  "FaceOutlinePath": "/Game/Example/Character/Meshes/example_ingame_face_mesh_outline.example_ingame_face_mesh_outline",

  "FaceOutlineClearMaterialOverrides": true,

  "FaceOutlineMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/Example/Materials/example_face_outline_mi.example_face_outline_mi"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/Example/Materials/example_face_outline_mi.example_face_outline_mi"
    }
  ],

  "FaceClearMaterialOverrides": true,

  "FaceMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/Example/Character/Materials/example_face_mi.example_face_mi"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/Example/Character/Materials/example_eye_mi.example_eye_mi"
    }
  ],

  "FaceMorphTargets": [],

  "BodyPath": "/Game/Example/Character/Meshes/example_body_mesh.example_body_mesh",

  "BodyOutlinePath": "/Game/Example/Character/Meshes/example_body_mesh_outline.example_body_mesh_outline",

  "BodyOutlineClearMaterialOverrides": true,

  "BodyOutlineMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/Example/Materials/example_hair_outline_mi.example_hair_outline_mi"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/Example/Materials/example_body_outline_mi.example_body_outline_mi"
    },
    {
      "SlotIndex": 2,
      "MaterialPath": "/Game/Example/Materials/example_body_outline_mi.example_body_outline_mi"
    },
    {
      "SlotIndex": 3,
      "MaterialPath": "/Game/Example/Materials/example_body_outline_mi.example_body_outline_mi"
    }
  ],

  "BodyClearMaterialOverrides": true,

  "BodyMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/Example/Character/Materials/example_hair_mi.example_hair_mi"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/Example/Character/Materials/example_skin_mi.example_skin_mi"
    },
    {
      "SlotIndex": 2,
      "MaterialPath": "/Game/Example/Character/Materials/example_top_mi.example_top_mi"
    },
    {
      "SlotIndex": 3,
      "MaterialPath": "/Game/Example/Character/Materials/example_bottom_mi.example_bottom_mi"
    }
  ],

  "BodyMorphTargets": [],

  "IconPath": "/Game/Example/UI/Icons/Img_Character_Example.Img_Character_Example"
}
```


## 5.9 Face materials and Morph Target variants

The complete example in section 10.1 already demonstrates all supported face/body customization fields:

- `FaceClearMaterialOverrides` and `FaceMaterials` for makeup, eyes and face materials.
- `FaceMorphTargets` for one or several face Morph Targets.
- `BodyMorphTargets` for one or several body Morph Targets.
- `FacePath` for the optional secondary face mesh.

Both Morph Target arrays accept several entries, with unique names and values from `0.0` to `1.0`:

```json
{
  "FaceMorphTargets": [
    { "MorphName": "makeup_variant", "Value": 1.0 },
    { "MorphName": "eye_shape_soft", "Value": 0.65 }
  ],
  "BodyMorphTargets": [
    { "MorphName": "body_shape_a", "Value": 0.8 },
    { "MorphName": "cloth_adjust", "Value": 1.0 }
  ]
}
```

The fragment above documents the two arrays; it is not a complete preset by itself. Omit an unused array or use an empty array. Never publish Morph Target names that do not exist on the selected mesh.

## 5.10 Correction-field reference

| Field | Accepted value | Effect and release rule |
|---|---|---|
| `BodyClearMaterialOverrides` | Boolean | Clears existing body overrides before applying `BodyMaterials`. Recommended when replacing a body or performing a full retexture. |
| `BodyOutlinePath` | Full Skeletal Mesh object path | Loads a dedicated body-outline mesh in parallel with `BodyPath`. |
| `BodyOutlineClearMaterialOverrides` | Boolean | Clears stale outline overrides before applying `BodyOutlineMaterials`. |
| `BodyOutlineMaterials` | Material override array | Applies zero-based Material Instance slots to the dedicated body-outline mesh. |
| `FaceOutlinePath` | Full Skeletal Mesh object path | Loads a dedicated face-outline mesh in parallel with `FacePath`. |
| `FaceClearMaterialOverrides` | Boolean | Clears face overrides before applying `FaceMaterials`. |
| `FaceOutlineMaterials` | Material override array | Applies zero-based Material Instance slots to the dedicated face-outline mesh. |
| `WeaponClearMaterialOverrides` | Boolean | Clears weapon overrides before applying `WeaponMaterials`. |
| `AuxiliaryClearMaterialOverrides` | Boolean | Clears overrides on the auxiliary component before applying `AuxiliaryMaterials`. |
| `WeaponMaterialsOnly` | Boolean | Keeps native weapon meshes and changes materials only. When `true`, `WeaponPath` and `WeaponPaths` may be omitted. |
| `WeaponPath` | Full Unreal object path | Replaces one detected weapon component. |
| `WeaponPaths` | Array of objects | Replaces several weapon components. Every entry needs `ComponentMatch` when the array contains more than one mesh. |
| `AuxiliaryMeshPath` | Full Skeletal Mesh object path | Loads one separate hair, head or cloth mesh on the available auxiliary follower component. Use `BodyOutlinePath` for the primary body outline. |
| `AuxiliaryPhysicsAssetPath` | Full Physics Asset object path | Assigns costume-specific physics to the auxiliary mesh. |
| `AuxiliaryMaterials` | Material override array | Applies zero-based material slots to the auxiliary mesh. |
| `HiddenComponentMeshMatches` | Array of exact strings | Hides matching native secondary Skeletal Mesh Components and restores their state during the next preset transition. |
| `LinkedBodyComponentMeshMatches` | Array of exact strings | Finds persistent secondary body components that need a replacement mesh. |
| `LinkedBodyReplacementPath` | Full Skeletal Mesh object path | Supplies the dedicated mesh for linked components; without it, DSMS uses `BodyPath`. |
| `FacePath` | Full Skeletal Mesh object path | Optional secondary face mesh. Omit the property when the character does not use it. |
| `FaceMorphTargets` | Array of name/value objects | Applies face Morph Targets after the face mesh is installed. |
| `BodyMorphTargets` | Array of name/value objects | Applies body Morph Targets after the body mesh is installed. |

## 5.11 Stability rules learned from corrected presets

- Always use a unique `UniqueID`, including mask/no-mask and retexture variants.
- Keep `TargetCharacterID` protection enabled while creating public presets. Cross-character application can produce an A-pose, wrong proportions, persistent components or a crash.
- Use full object paths in the form `/Game/Folder/Asset.Asset`. Do not paste `DS/Content/...` or a Windows path into JSON.
- Prefer exact component/mesh matches copied from logs. Broad substring matches are unsafe.
- Apply the correct physics Animation Blueprint class ending in `_C`; a Physics Asset alone does not activate secondary physics.
- Keep body and weapon presets independent. Do not place weapon material overrides in a Costume JSON.
- When switching between a special costume and the base outfit, verify both directions repeatedly. A preset is not release-ready if it works only after a fresh game launch.
- Use F6 to reload loose JSON files after editing them. Opening and closing the selector with F5 reuses the cached profile list.
- If a dedicated outline cannot be reproduced safely, hide the incompatible outline instead of retaining the outline from the previously loaded costume.

---

[← Guide Home](https://github.com/Hoverbike51/DSMS-ModLoader) · [← Previous](04-Creating-JSON-presets.md) · [Next →](06-Troubleshooting.md)
