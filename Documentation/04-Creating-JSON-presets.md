> [!IMPORTANT]
> Do not bundle UE4SS, DSMS-ModLoader and scripts in your mod! Prefer linking users to the official dependency pages.
# Creating JSON v3 Presets 

Start from:

```text
HMV_DS_SELECTOR/_EXAMPLES/DSMS-Costume-Template.json.example
```

Save your completed copy with a unique filename beginning with `DSMS-` and ending in `.json`.

Example:

```text
DSMS-HMV_Awaken_Lute_Costume_Base.json
```

## 4.1 Complete JSON v3 template

```json
{
  "Version": 3,
  "UniqueID": "author_character_costume_name",
  "DisplayName": "Character - Costume Name",
  "Type": "Costume",
  "TargetCharacterID": "CharacterID",
  "Requirements": ["None"],

  "PhysicsAssetPath": "/Game/mods/Mod_Author/Mod_Name/Mesh/SK_Body_PhysicsAsset.SK_Body_PhysicsAsset",
  "PhysicsAnimBlueprintPath": "/Game/Design/DsCharacter/DsPhysics/PC/Character/DsABP_Character_Costume_Physics.DsABP_Character_Costume_Physics_C",

  "FaceMorphPath": "/Game/Art/Character/Player/DS_Character/Meshs/ch_Character_ingame_face_mesh.ch_Character_ingame_face_mesh",
  "FacePath": "/Game/Art/Character/Player/DS_Character/Meshs/ch_Character_mesh_face.ch_Character_mesh_face",
  "FaceClearMaterialOverrides": true,
  "FaceMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/mods/Mod_Author/Mod_Name/Materials/MI_Face.MI_Face"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/mods/Mod_Author/Mod_Name/Materials/MI_Eyes.MI_Eyes"
    }
  ],
  "FaceMorphTargets": [
    {
      "MorphName": "costume_morph_name",
      "Value": 1.0
    }
  ],

  "BodyPath": "/Game/mods/Mod_Author/Mod_Name/Mesh/SK_Body.SK_Body",
  "BodyClearMaterialOverrides": true,
  "BodyMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/mods/Mod_Author/Mod_Name/Materials/MI_Body.MI_Body"
    }
  ],
  "BodyMorphTargets": [
    {
      "MorphName": "body_morph_name",
      "Value": 1.0
    }
  ],

  "IconPath": "/Game/mods/Mod_Author/Mod_Name/Icons/T_Icon.T_Icon"
}
```


## 4.2 Field reference

### `Version`

- Required: yes
- Value for new profiles: `3`

Example:


```json
{
  "Version": 3
}
```

### `UniqueID`

- Required: yes
- Must be unique across every loaded JSON file.
- Use a stable lowercase identifier. (could containing the author, character, and costume name)

Example:


```json
{
  "UniqueID": "hovermodsvault_astria_swimsuit_red"
}
```

If two JSON use the same `UniqueID`, the duplicate is rejected and reported in `UE4SS.log`.

### `DisplayName`

- Required: yes
- This is the readable name shown in the selector.

Example:


```json
{
  "DisplayName": "Astria - Red Swimsuit"
}
```

### `Type`

- Required: yes
- Allowed values:

```text
Costume
Custom
Weapon
Mounts
NPC
```

Example:


```json
{
  "Type": "Costume"
}
```

Use `Costume` for a playable-character outfit replacement.\
Use `Custom` for a playable-custom character replacement.

> [!IMPORTANT]
> `Costume` should be used for outfits of existing character. (vanilla or modded)\
> `Custom` must be used only for any new character added in the game. (That doesn't even exist in the base game)\
>  It also mean that any variants you'll made will stay in `Custom`
> 
> `Weapon` should be used for any weapon mod.\
> `Mounts` should be used for any mounts mod.\
> `NPC` should be used for any npc mod.

> [!NOTE]
> `Astria Red Swimsuit` will be in `Costume` category, because it's a vanilla asset.\
> `Castella Custom Body` will be in `Costume` category, even though it is a brand-new body, because it is intended for an existing character from the base game.\
> `Any custom characters` will be in `Custom` category, since these are characters that do not even exist in the base game.



### `TargetCharacterID`

- Required for `Custom` and `Costume` category.
- Identifies the playable character that may receive the preset.
- Use the game's internal character name, for example `Astria` or `Aria`.
- Only letters, numbers, `_`, and `-` are accepted.
- Allowed values:

```text
Aileen
Alex
Aria
Aruru
Astria
Awaken_Lute
Cassius
Castella
Cerese
Charlotte
Dana
Elize
Ewald
Gilford
Hood
Jerome
Johnny
kalien
Karina
Lampi
Lana
Little_Lute
Logan
Lute
lyn
Onette
Othello
Reina
Ropie
Roxy
Rubens
Ryza
Sion
Tarte
Theresia
Veronica
Viola
Ysera
```

Example:


```json
{
  "TargetCharacterID": "Astria"
}
```

> [!IMPORTANT]
> The correct target character must be active before applying the preset. This is mandatory!

### `Requirements`

- Recommended value in version 0.7.0: `["None"]`
- Reserved for preset requirement metadata.

Example:


```json
{
  "Requirements": ["None"]
}
```

### `BodyPath`

- Required: yes
- Must point to the body Skeletal Mesh.
- Must use the complete object-path form:

```text
/Game/Folder/AssetName.AssetName
```
Do not use a Windows filename or append `.uasset`.

Example:


```json
{
  "BodyPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Mesh/ch_Astria_swimsuit_01_body_mesh.ch_Astria_swimsuit_01_body_mesh"
}
```
> [!NOTE]
> Sometime devs make mistake typo "Astira" ≠ "Astria". Don't change anything!


### `FaceMorphPath`

- Required for `Custom` and `Costume` category.
- Points to the in-game face Skeletal Mesh containing facial morph targets.

Example:


```json
{
  "FaceMorphPath": "/Game/Art/Character/Player/DS_Astria/Meshs/ch_Astria_ingame_face_mesh.ch_Astria_ingame_face_mesh"
}
```

### `FacePath`

- Required for `Custom` and `Costume` category.
- Points to the secondary face mesh used by the character/costume setup.
- Keep it paired with the intended body and in-game face, even if both face paths appear visually similar.

Example:


```json
{
  "FacePath": "/Game/Art/Character/Player/DS_Astria/Meshs/ch_Astria_mesh_face.ch_Astria_mesh_face"
}
```

### `IconPath`

- Required: yes
- Points to a Texture asset used as preset icon metadata.
- Use the complete `/Game/.../Asset.Asset` form.

Example:


```json
{
  "IconPath": "/Game/Art/UI/InGame/Img_Character/Signal/Img_Character_Signal_Astria_Unique_01.Img_Character_Signal_Astria_Unique_01"
}
```

### `PhysicsAssetPath`

- Required: no
- Points to a Physics Asset compatible with the selected body mesh.
- If omitted, the Skeletal Mesh default Physics Asset is used.
- A Physics Asset controls collision bodies and constraints; it does not replace the costume Physics Animation Blueprint.

Example:


```json
{
  "PhysicsAssetPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Mesh/ch_Astria_swimsuit_01_body_mesh_PhysicsAsset.ch_Astria_swimsuit_01_body_mesh_PhysicsAsset"
}
```

### `PhysicsAnimBlueprintPath`

- Required: technically optional, but strongly recommended for character costumes.
- Required in practice when a costume uses specific bones (breast, hair, feather, cloth, ribbon, or accessory physics)
- The path normally ends with `_C`:

```text
/Game/Design/.../DsABP_Name.DsABP_Name_C
```

Example:


```json
{
  "PhysicsAnimBlueprintPath": "/Game/Design/DsCharacter/DsPhysics/PC/Astria/DsABP_Astria_SwimSuit_Physics.DsABP_Astria_SwimSuit_Physics_C"
}
```

Using the wrong physics Anim Blueprint can cause an A-pose or missing secondary motion even if `PhysicsAssetPath` is correct.

### `BodyClearMaterialOverrides`

- Required: no
- Recommended: `true` when the JSON explicitly supplies body materials.
- Clears previous body material overrides before applying `BodyMaterials`.

Example:


```json
{
  "BodyClearMaterialOverrides": true
}
```

> [!NOTE]
> This ensures that the custom body materials will be applied properly.

### `BodyMaterials`

- Required: no; use an empty array when no override is required.
- Each entry contains a zero-based `SlotIndex` and a `MaterialPath`.

Example:

```json
{
  "BodyMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_body_mi.ch_Astria_swimsuit_01_body_mi"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_skin_mi.ch_Astria_swimsuit_01_skin_mi"
    },
    {
      "SlotIndex": 2,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_hair_sdw.ch_Astria_swimsuit_01_hair_sdw"
    },
    {
      "SlotIndex": 3,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_grass_mi.ch_Astria_swimsuit_01_grass_mi"
    },
    {
      "SlotIndex": 4,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_hair_mi.ch_Astria_swimsuit_01_hair_mi"
    }
  ]
}
```

Material slot indices must match the selected Skeletal Mesh exactly. A mesh with five slots supports indices `0` through `4`.\ A custom mesh with seven slots supports indices `0` through `6`. A JSON entry cannot create a new material slot that does not exist in the mesh.

Do not declare the same slot more than once.

### `FaceClearMaterialOverrides`

- Required: no
- Recommended: `true` when supplying `FaceMaterials`.
- Clears previous face overrides before the new face materials are applied.

Example:


```json
{
  "FaceClearMaterialOverrides": true
}
```

> [!NOTE]
> This ensures that the custom face materials will be applied properly.

### `FaceMaterials`

- Required: no; use an empty array when no override is required.
- Uses the same zero-based slot format as `BodyMaterials`.
- Useful for costume-specific face makeup, eyes, skin, or other face variations.

Example:

```json
{
  "FaceMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_face_mi.ch_Astria_swimsuit_01_face_mi"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_eye_mi.ch_Astria_swimsuit_01_eye_mi"
    }
  ]
}
```

### `BodyMorphTargets` and `FaceMorphTargets`

- Required: no; use an empty array when unused.
- Every entry contains `MorphName` and `Value`.
- `Value` must be between `0.0` and `1.0`.
- Morph names must exactly match morph targets contained in the selected Skeletal Mesh.

Example:

```json
{
  "FaceMorphTargets": [
    {
      "MorphName": "swimsuit_01",
      "Value": 1.0
    }
  ]
}
```

Multiple morph targets are supported:


Example:

```json
{
  "BodyMorphTargets": [
    {
      "MorphName": "BodyShape_A",
      "Value": 0.75
    }
    {
      "MorphName": "Accessory_Open"
      "Value": 1.0
    }
  ]
}
```

Do not declare the same morph name more than once in the same array.

> [!WARNING]
> It is not yet possible to change the “morph target” value from the mod's menu; you must edit this value directly in the JSON file.

## 4.3 Final JSON for Astria Red Swimsuit

Example:

```json
{
  "Version": 3,
  "UniqueID": "hovermodsvault_astria_swimsuit_red",
  "DisplayName": "Astria - Red Swimsuit",
  "Type": "Costume",
  "TargetCharacterID": "Astria",
  "Requirements": ["None"],

  "PhysicsAssetPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Mesh/ch_Astria_swimsuit_01_body_mesh_PhysicsAsset.ch_Astria_swimsuit_01_body_mesh_PhysicsAsset",
  "PhysicsAnimBlueprintPath": "/Game/Design/DsCharacter/DsPhysics/PC/Astria/DsABP_Astria_SwimSuit_Physics.DsABP_Astria_SwimSuit_Physics_C",

  "FaceMorphPath": "/Game/Art/Character/Player/DS_Astria/Meshs/ch_Astria_ingame_face_mesh.ch_Astria_ingame_face_mesh",
  "FacePath": "/Game/Art/Character/Player/DS_Astria/Meshs/ch_Astria_mesh_face.ch_Astria_mesh_face",
  "FaceClearMaterialOverrides": true,
  "FaceMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_face_mi.ch_Astria_swimsuit_01_face_mi"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_eye_mi.ch_Astria_swimsuit_01_eye_mi"
    }
  ],
  "FaceMorphTargets": [
    {
      "MorphName": "swimsuit_01",
      "Value": 1.0
    }
  ],

  "BodyPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Mesh/ch_Astria_swimsuit_01_body_mesh.ch_Astria_swimsuit_01_body_mesh",
  "BodyClearMaterialOverrides": true,
  "BodyMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_body_mi.ch_Astria_swimsuit_01_body_mi"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_skin_mi.ch_Astria_swimsuit_01_skin_mi"
    },
    {
      "SlotIndex": 2,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_hair_sdw.ch_Astria_swimsuit_01_hair_sdw"
    },
    {
      "SlotIndex": 3,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_grass_mi.ch_Astria_swimsuit_01_grass_mi"
    },
    {
      "SlotIndex": 4,
      "MaterialPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Materials/ch_Astria_swimsuit_01_hair_mi.ch_Astria_swimsuit_01_hair_mi"
    }
  ],
  "BodyMorphTargets": [],

  "IconPath": "/Game/Art/UI/InGame/Img_Character/Signal/Img_Character_Signal_Astria_Unique_01.Img_Character_Signal_Astria_Unique_01"
}

```

# Distribution

> [!IMPORTANT]
> Do not bundle UE4SS, DSMS-ModLoader and scripts in your mod! Prefer linking users to the official dependency pages.

A clean release archive should use this layout:

```text
DragonSword  Awakening/
`-- DS/
    `-- Content/
        `-- Paks/
            `-- ~mods/
                |-- Mod_Author_Mod_Name_P.pak
                `-- HMV_DS_SELECTOR/
                    `-- Mod_Author/
                        `-- Mod_Type/
                            `-- Mod_Name/
                                `-- DSMS-Mod_Author-Mod_Name.json
```

Include a short readme

- Required DSMS-ModLoader version.
- Required UE4SS and DSMS-ModLoader scripts dependencies.
- Must Target a specific character.
- Whether the mod references assets from an optional official costume.
- Installation and uninstall paths.
- Known incompatibilities.

---

[← Guide Home](https://github.com/Hoverbike51/DSMS-ModLoader) · [← Previous](03-Installing-json.md) · [Next →](05-Creating-Advanced-json.md)
