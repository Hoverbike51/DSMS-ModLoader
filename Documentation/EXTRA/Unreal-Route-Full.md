>[!WARNING]
>This guide is still a work in progress; some information is subject to change and may be outdated.


# DragonSword: Awakening Custom Character & Costume PAK Guide

## Unreal Engine 5.3 + DSMS-ModLoader 0.7.0

This guide explains how to prepare an **Unreal Engine 5.3** project, import a custom Character or Costume, package the assets as a **legacy `.pak`**, create the matching **DSMS JSON v3 preset**, and install both files for DragonSword: Awakening.

> [!IMPORTANT]
> This is an **Unreal Engine 5.3-only** guide. Do not author or cook the release files in UE 5.4, 5.5, 5.6, or another engine version.

> [!NOTE]
> This guide covers only custom Character/Costume assets, their `.pak`, and their loose JSON v3 preset.

---

## 1. Scope and final files

This workflow is for:

- A custom Costume used on an existing playable DragonSword character.
- A custom Character appearance used on an existing playable Pawn.
- Custom body and face Skeletal Meshes.
- Custom textures and **Material Instances only**.
- Optional Morph Targets, Physics Asset, and physics Animation Blueprint references.
- DSMS-ModLoader **0.7.0** and JSON schema **v3**.

Each release contains two independent files:

```text
YourCustomAssetMod.pak
DSMS-YourPreset.json
```

The `.pak` contains the cooked custom Unreal assets. The loose JSON tells DSMS 0.7.0 which assets to load and how to apply them.

### Final installation example

```text
DragonSword  Awakening\
`-- DS\
    `-- Content\
        `-- Paks\
            `-- ~mods\
                |-- HMV_Astria_RedSwimsuit.pak
                `-- HMV_DS_SELECTOR\
                    `-- HoverModsVault\
                        `-- COSTUMES\
                            `-- Astria_RedSwimsuit\
                                `-- DSMS-HMV_Astria_RedSwimsuit.json
```

The DSMS JSON library root is mandatory and is scanned recursively:

```text
DS\Content\Paks\~mods\HMV_DS_SELECTOR\
```

---

## 2. Required software

- [DragonSword: Awakening](https://store.steampowered.com/app/4570720/DragonSword__Awakening/) for Windows.
- [DSMS-ModLoader 0.7.0](https://github.com/Hoverbike51/DSMS-ModLoader) already installed and working.
- [Unreal Engine 5.3](https://www.unrealengine.com/download).
- [FModel](https://fmodel.app) or another asset browser for finding the original game paths.
- [Blender](https://www.blender.org), Maya, or another DCC application when editing the model.

---

## 3. Path syntax: never mix Windows paths and Unreal paths

DragonSword modding uses several path formats. They are not interchangeable.

| Path type | Separator | Example | Where it is used |
|---|---:|---|---|
| Windows filesystem path | `\` | `C:\UE_Modding\DragonSword\DS\Content\mods\HoverModsVault\Astria_RedSwimsuit\Mesh` | Explorer, file dialogs, command input/output files |
| Unreal Content Browser path | `/` | `/Game/mods/HoverModsVault/Astria_RedSwimsuit/Mesh` | Unreal Editor |
| Unreal object path | `/` plus `.ObjectName` | `/Game/mods/HoverModsVault/Astria_RedSwimsuit/Mesh/SK_Astria_Red.SK_Astria_Red` | DSMS JSON properties |
| Internal PAK mount path | `/` | `../../../DS/Content/mods/HoverModsVault/Astria_RedSwimsuit/Mesh/SK_Astria_Red.uasset` | PAK listing and mount verification |

### Rules

1. Use backslashes for physical Windows locations.
2. Use forward slashes for Unreal package paths and JSON object paths.
3. Never paste `/Game/...` into Windows Explorer.
4. Never use `C:\...` inside `BodyPath`, `FacePath`, `MaterialPath`, or `IconPath`.
5. Never put `.uasset` at the end of a JSON object path.
6. A JSON object path normally repeats the asset name after a dot:

```text
/Game/mods/Author/Mod/Mesh/SK_Body.SK_Body
```

7. Generated class paths, including a physics Animation Blueprint class, end in `_C`:

```text
/Game/Design/DsCharacter/DsPhysics/PC/Astria/DsABP_Astria_SwimSuithysics.DsABP_Astria_SwimSuithysics_C
```

### One asset shown in every relevant format

```text
Windows source location:
C:\UE_Modding\DragonSword\DS\Content\mods\HoverModsVault\Astria_RedSwimsuit\Mesh\SK_Astria_Red.uasset

Unreal Content Browser folder:
/Game/mods/HoverModsVault/Astria_RedSwimsuit/Mesh

JSON BodyPath:
/Game/mods/HoverModsVault/Astria_RedSwimsuit/Mesh/SK_Astria_Red.SK_Astria_Red

Expected PAK entry:
../../../DS/Content/mods/HoverModsVault/Astria_RedSwimsuit/Mesh/SK_Astria_Red.uasset
```

---

## 4. Create the UE 5.3 project for DragonSword

### 4.1 Create the project

In Unreal Engine 5.3:

1. Select **Games**.
2. Select **Blank**.
3. Select **Blueprint** as the project type.
4. Disable Starter Content.
5. Set the project name to exactly:

```text
DS
```

> [!NOTE]
> Unreal will create the project in your Document

```text
C:\Users\%USERPROFILE%\Documents\Unreal Projects\DS\DS.uproject
```

The project name matters. A project called `DS` produces the DragonSword-compatible mount root:

```text
../../../DS/Content/
```

![UEB](https://github.com/Hoverbike51/DSMS-ModLoader/blob/main/Previews/Extra/Unrealroject_Browser_001.png)


If `[Core.System]` already exists, add only the setting below the existing heading. Do not create a duplicate heading.

This is required for the DragonSword asset workflow used by this project.

### 4.2 Direct legacy PAK packaging settings

For the recommended Primary Asset Label route, open **Project Settings > Packaging** and set:

```text
Use Pak File                  Enabled
Use Io Store                  Disabled
Generate Chunks               Enabled
Full Rebuild                  Enabled for a release build
Cook Everything               Disabled
Include Starter Content       Disabled
Target Platform               Windows
```

Compression is optional. Use the same choice consistently while testing and releasing.

![PackageSetting](https://github.com/Hoverbike51/DSMS-ModLoader/blob/main/Previews/Extra/UEackaging_setting_001.png)

---

## 5. Required custom asset layout

Every distributable asset must be placed below:

```text
/Game/mods/Mod_Author/Mod_Name/
```

Recommended structure:

```text
/Game/mods/HoverModsVault/Astria_RedSwimsuit/

|-- Mesh/
|   |-- SK_Astria_Red
|   |-- SK_Astria_Red_Face
|   |-- SK_Astria_Red_IngameFace
|   `-- PHYS_Astria_Red
|-- Materials/
|   |-- MI_Astria_Red_Body
|   |-- MI_Astria_Red_Skin
|   |-- MI_Astria_Red_Face
|   `-- MI_Astria_Red_Eyes
|-- Textures/
|   |-- T_Astria_Red_Body_D
|   |-- T_Astria_Red_Body_N
|   |-- T_Astria_Red_Body_ORM
`-- Icons/
    `-- T_Astria_Red_Icon
```

These four subfolders are the public convention:

```text
/Game/mods/Mod_Author/Mod_Name/Mesh
/Game/mods/Mod_Author/Mod_Name/Materials
/Game/mods/Mod_Author/Mod_Name/Textures
/Game/mods/Mod_Author/Mod_Name/Icons
```
![ContentBroswer](https://github.com/Hoverbike51/DSMS-ModLoader/blob/main/Previews/Extra/Content_Browser_001.png)
---

## 6. Inspect the original DragonSword character first

Before importing anything, locate the target character's original data with FModel:

- Body Skeletal Mesh path.
- In-game face Skeletal Mesh path containing Morph Targets.
- Secondary face mesh path, when present.
- Skeleton and bone hierarchy.
- Physics Asset path.
- Costume physics Animation Blueprint class path.
- Body and face material-slot count, order, and names.
- Material Instance parent paths.
- Required Morph Target names and values.
- Character icon path if using a vanilla icon.

Useful DragonSword content roots:

```text
Player assets:
/Game/Art/Character/Player

Costume assets:
/Game/Art/Character/Costume

Physics Animation Blueprints:
/Game/Design/DsCharacter/DsPhysics/PC

Character icons:
/Game/Art/UI/InGame/Img_Character/Signal
```

DragonSword characters commonly use separate body and face Skeletal Meshes. Do not assume that replacing only the body also replaces the face, makeup, eye material, or face Morph Targets.

![Fmodel](https://github.com/Hoverbike51/DSMS-ModLoader/blob/main/Previews/Extra/Fmodel_search_001.png)

---

## 7. Import and validate the custom assets

### 7.1 Body Skeletal Mesh

Import the body FBX into the mod's `Mesh` folder with:

- Skeletal Mesh enabled.
- Import Animations disabled unless the asset genuinely supplies animations.
- Import Morph Targets enabled when the body contains Morph Targets.
- Correct normals and tangents for the source model.
- The compatible target Skeleton selected when the hierarchy is identical.
- No accidental extra root bone.
- No renamed, removed, duplicated, or reordered required bones.

After import, verify:

- Scale and orientation.
- Skeleton and bone hierarchy.
- Skin weights.
- Material-slot count and order.
- Morph Target names.
- Bounds.
- Physics Asset assignment.

### 7.2 Face meshes

When the costume changes face shape, makeup, or eyes, prepare both paths expected by the preset:

- `FaceMorphPath`: the in-game face mesh containing the relevant Morph Targets.
- `FacePath`: the secondary face mesh used by the game's asset structure.

Verify the face material slots independently from the body material slots.

### 7.3 Skeleton compatibility and the A-pose

DSMS 0.7.0 applies an appearance to an existing DragonSword Pawn. It does not create a new Pawn or a new playable character slot.

For a reliable mod:

- Set `TargetCharacterID` to the exact intended playable character.
- Keep target protection enabled during normal use.
- Preserve the target character's required Skeleton hierarchy.
- Use the correct costume physics Animation Blueprint class.

A custom mesh being visible does not prove that it is animation-compatible. An A-pose usually indicates a Skeleton, target, Pawn Animation Blueprint, or physics Animation Blueprint mismatch.

### 7.4 Physics Asset versus physics Animation Blueprint

These JSON fields have different purposes:

```text
PhysicsAssetPath
PhysicsAnimBlueprintPath
```

- `PhysicsAssetPath` points to the Physics Asset assigned to the body Skeletal Mesh.
- `PhysicsAnimBlueprintPath` points to the generated class used by DragonSword for costume-specific secondary motion.

A valid Physics Asset alone may not animate hair, feathers, cloth-like chains, or breasts. Use the matching DragonSword physics Animation Blueprint class when the original costume requires it.

---

## 8. Materials: Material Instances only

The release `.pak` must contain **Material Instances**, not custom master/base Materials.

Allowed custom assets include:

- Material Instances.
- Textures used by those instances.
- Skeletal Meshes.
- Physics Assets.
- The Primary Asset Label Data Asset.

Do not ship:

- A custom master Material.
- A copied vanilla master Material.
- Extracted vanilla textures or meshes.
- Unused test assets.

### 8.1 Material slots are zero-based

The first slot is `0`, the second is `1`, and so on.

If the Skeletal Mesh contains five material slots, valid indices are:

```text
0, 1, 2, 3, 4
```

The JSON cannot create a material slot that does not exist in the cooked Skeletal Mesh. A seven-slot custom mesh may use indices `0` through `6`; a five-slot mesh may not.

### 8.2 Parent Material references

Create each Material Instance below the mod's `Materials` folder and use the appropriate DragonSword parent Material reference.

If the stock UE 5.3 project needs a local placeholder asset to resolve a vanilla reference, treat that placeholder as authoring-only. It must not be distributed. Verify the final PAK listing; if it contains a copied or placeholder game asset outside your `/Game/mods/Author/Mod/` namespace, do not release that PAK.

---

## 9. Recommended route: package with a Primary Asset Label

Unreal Engine's chunk system can generate a separate PAK for a group of assets. A Primary Asset Label is a **Data Asset** used to assign the mod folder and its dependencies to a unique chunk.

Epic documentation:

- [Preparing Assets for Chunking](https://dev.epicgames.com/documentation/en-us/unreal-engine/preparing-assets-for-chunking-in-unreal-engine)
- [Cooking Content and Creating Chunks](https://dev.epicgames.com/documentation/en-us/unreal-engine/cooking-content-and-creating-chunks-in-unreal-engine)

### 9.1 Create the Primary Asset Label Data Asset

1. Open the mod root folder, for example:

```text
/Game/mods/HoverModsVault/Astria_RedSwimsuit
```

2. Right-click in the Content Browser.
3. Select **Miscellaneous > Data Asset**.
4. Select **PrimaryAssetLabel** as the Data Asset class.
5. Name it:

```text
PAL_HMV_Astria_RedSwimsuit
```

Do not create a Blueprint subclass of `PrimaryAssetLabel`. Create the Data Asset directly.

### 9.2 Configure the label

Use these settings:

| Primary Asset Label property | Recommended value |
|---|---|
| Is Runtime Label | Enabled |
| Priority | `1` |
| Chunk ID | A unique value greater than `0`, for example `5101` |
| Cook Rule | `Always Cook` |
| Label Assets in My Directory | Enabled |
| Include Redirectors | Disabled |
| Explicit Assets | Empty unless you intentionally include an asset outside the folder |

`Label Assets in My Directory` captures assets in the label's folder and its subfolders. Referenced custom dependencies should remain below the same mod root whenever possible.

Give each released mod a different Chunk ID. Do not reuse the same Chunk ID for two active mods in one authoring project.

### 9.3 Audit the chunk before packaging

Open **Tools > Audit > Asset Audit** and display the chunk columns. Confirm that:

- The custom body and face meshes are assigned to chunk `5101`.
- Custom Material Instances and textures are assigned to chunk `5101`.
- The custom Physics Asset is assigned to chunk `5101` when used.
- Unrelated project assets are not assigned to chunk `5101`.
- No copied vanilla game asset is included.

If an expected dependency is absent, fix its reference or add it deliberately to the label. Do not solve missing dependencies by labeling the entire project.

### 9.4 Package for Windows

Use **Platforms > Windows > Package Project** and choose an empty output folder.

The staged PAK files normally appear below a Windows path similar to:

```text
C:\UE_Modding\DragonSword\DS\Saved\StagedBuilds\Windows\DS\Content\Paks\
```

Find the PAK matching your Chunk ID, for example:

```text
pakchunk5101-Windows.pak
```

Copy it to a release folder and rename the copy clearly:

```text
DSMS_Astria_RedSwimsuit.pak
```

Do not combine it with chunk `0`, the packaging map, or unrelated project content.

### 9.5 Inspect the PAK

Run the UE 5.3 `UnrealPak.exe` in Command Prompt:

```bat
"D:\Unreal Engine\UE_5.3\Engine\Binaries\Win64\UnrealPak.exe" "C:\UE_Modding\Release\DSMS_Astria_RedSwimsuit.pak" -List
```

Every distributable custom asset should be below:

```text
../../../DS/Content/mods/HoverModsVault/Astria_RedSwimsuit/
```

Reject the build if the PAK contains:

- Assets mounted below another project name instead of `DS`.
- Authoring-only placeholders.
- Source FBX files.
- Test maps.
- Duplicated vanilla DragonSword assets.
- Another mod.

---

## 10. Create the DSMS JSON v3 preset

The preset is a loose UTF-8 JSON file. Its filename must begin with:

```text
DSMS-
```

Example filename:

```text
DSMS-HMV_Astria_RedSwimsuit.json
```

### 10.1 Complete custom Costume example

```json
{
  "Version": 3,
  "UniqueID": "hmv_astria_red_swimsuit",
  "DisplayName": "Astria - Red Swimsuit [HMV]",
  "Type": "Costume",
  "TargetCharacterID": "Astria",
  "Requirements": ["None"],

  "PhysicsAssetPath": "/Game/Art/Character/Costume/DS_Astria/Astira_swimsuit_01/Mesh/ch_Astria_swimsuit_01_body_meshhysicsAsset.ch_Astria_swimsuit_01_body_meshhysicsAsset",
  "PhysicsAnimBlueprintPath": "/Game/Design/DsCharacter/DsPhysics/PC/Astria/DsABP_Astria_SwimSuithysics.DsABP_Astria_SwimSuithysics_C",

  "FaceMorphPath": "/Game/mods/HoverModsVault/Astria_RedSwimsuit/Mesh/SK_Astria_Red_IngameFace.SK_Astria_Red_IngameFace",
  "FacePath": "/Game/mods/HoverModsVault/Astria_RedSwimsuit/Mesh/SK_Astria_Red_Face.SK_Astria_Red_Face",
  "FaceClearMaterialOverrides": true,
  "FaceMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/mods/HoverModsVault/Astria_RedSwimsuit/Materials/MI_Astria_Red_Face.MI_Astria_Red_Face"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/mods/HoverModsVault/Astria_RedSwimsuit/Materials/MI_Astria_Red_Eyes.MI_Astria_Red_Eyes"
    }
  ],
  "FaceMorphTargets": [
    {
      "MorphName": "swimsuit_01",
      "Value": 1.0
    }
  ],

  "BodyPath": "/Game/mods/HoverModsVault/Astria_RedSwimsuit/Mesh/SK_Astria_Red.SK_Astria_Red",
  "BodyClearMaterialOverrides": true,
  "BodyMaterials": [
    {
      "SlotIndex": 0,
      "MaterialPath": "/Game/mods/HoverModsVault/Astria_RedSwimsuit/Materials/MI_Astria_Red_Body.MI_Astria_Red_Body"
    },
    {
      "SlotIndex": 1,
      "MaterialPath": "/Game/mods/HoverModsVault/Astria_RedSwimsuit/Materials/MI_Astria_Red_Skin.MI_Astria_Red_Skin"
    }
  ],
  "BodyMorphTargets": [
    {
      "MorphName": "custom_body_shape",
      "Value": 1.0
    }
  ],

  "IconPath": "/Game/mods/HoverModsVault/Astria_RedSwimsuit/Textures/T_Astria_Red_Icon.T_Astria_Red_Icon"
}
```

Delete optional Morph Target entries when the mesh does not contain those Morph Targets. Do not leave invented names in a release preset.

### 10.2 Important JSON fields

| Field | Purpose |
|---|---|
| `Version` | Must be `3` for this guide. |
| `UniqueID` | Stable unique identifier; do not reuse it for another preset. |
| `DisplayName` | Text shown in the DSMS menu. |
| `Type` | Use `Costume` or `Character` for this guide. |
| `TargetCharacterID` | Exact intended playable character ID. Use the matching character for a public release. |
| `Requirements` | Metadata array. `None` means no declared requirement; DSMS 0.7.0 does not enforce DLC ownership through this field. |
| `PhysicsAssetPath` | Body Physics Asset object path. |
| `PhysicsAnimBlueprintPath` | Generated physics Anim Blueprint class path ending in `_C`. |
| `FaceMorphPath` | In-game face Skeletal Mesh with Morph Targets. |
| `FacePath` | Secondary face Skeletal Mesh. |
| `FaceMaterials` | Zero-based face material overrides. |
| `FaceMorphTargets` | One or more face Morph Target names and values. |
| `BodyPath` | Custom body Skeletal Mesh object path. |
| `BodyMaterials` | Zero-based body material overrides. |
| `BodyMorphTargets` | One or more body Morph Target names and values. |
| `IconPath` | Texture object path used by the preset. |

Use JSON syntax exactly:

- Double quotes around keys and strings.
- No comments.
- No trailing comma after the last property or array item.
- Decimal values such as `1.0` for Morph Targets.
- Forward slashes in every Unreal object path.

---

## 11. Install the `.pak` and JSON

### 11.1 Install the asset PAK

Copy the final legacy PAK to the Windows directory:

```text
S:\SteamLibrary\steamapps\common\DragonSword  Awakening\DS\Content\Paks\~mods\DSMS_Astria_RedSwimsuit.pak
```
The `.pak` contains the cooked custom Unreal assets. The loose JSON tells DSMS 0.7.0 which assets to load and how to apply them.\

The `DSMS_` PAK filename prefix is mandatory for DSMS runtime mounting. The comparison is
case-insensitive, but using the exact uppercase spelling keeps releases consistent. PAKs without
this prefix are intentionally ignored by the DSMS loader so traditional replacement mods are not
mounted a second time.


### 11.2 Install the JSON

Recommended organization:

```text
HMV_DS_SELECTOR/
`-- OtherAuthor/
    `-- ModType/
        `-- MyMod/
            `-- DSMS-MyPreset.json
```

Example Windows location:

```text
S:\SteamLibrary\steamapps\common\DragonSword  Awakening\DS\Content\Paks\~mods\HMV_DS_SELECTOR\HoverModsVault\COSTUMES\Astria_RedSwimsuit\DSMS-HMV_Astria_RedSwimsuit.json
```

Do not place the JSON inside the PAK. DSMS 0.7.0 scans loose JSON files recursively.

---

## 12. Test the mod

1. Back up the save.
2. Start DragonSword.
3. Load a save and wait until the playable world is fully loaded.
4. Select the character named by `TargetCharacterID`.
5. Press **F5** to rescan the DSMS JSON library when necessary.
6. Press **F2** to open DSMS-ModLoader.
7. Select `Costume` or `Character`.
8. Select the new preset.
9. Close the menu and test:

```text
Idle
Walking and running
Jumping and dodging
Combat
Weapon attachment
Face animation
Body and face Morph Targets
Body materials
Face and eye materials
Hair, feathers, cloth-like parts, and breast physics
Character switching
Returning to the base outfit
```

> [!TIP]
> Inspect the UE4SS log after each test:

```text
DragonSword  Awakening\DS\Binaries\Win64\ue4ss\UE4SS.log
```

Do not release a preset merely because the custom mesh appears once. Animation, secondary physics, materials, morphs, switching, and reloading must also work.

---

## 13. Troubleshooting

### Preset does not appear

- Filename does not begin with `DSMS-`.
- JSON is outside `HMV_DS_SELECTOR`.
- Invalid JSON syntax or trailing comma.
- `Version` is not `3`.
- `Type` is not `Costume` or `Character`.
- The JSON scan has not been refreshed with F5.

### Preset appears but nothing changes

- The PAK is not installed in `DS\Content\Paks\~mods`.
- The PAK mount root is not `../../../Game/MODS/`.
- A JSON object path contains a backslash.
- The asset name after the dot is missing or misspelled.
- The PAK was built with another project name.
- The custom asset was not cooked into the selected chunk.

### Body appears in an A-pose

- Wrong `TargetCharacterID`.
- Incompatible Skeleton or bone hierarchy.
- Accidental extra root bone.
- Wrong physics Animation Blueprint class.
- Attempted inter-character replacement beyond the capabilities of DSMS 0.7.0.

### Body animates but secondary physics is static

- `PhysicsAnimBlueprintPath` is missing or points to the wrong generated class.
- `PhysicsAssetPath` is wrong or incompatible.
- Required physics bones were renamed or removed.
- The mesh was imported without the expected hierarchy.

### Materials are wrong or missing

- Incorrect zero-based `SlotIndex`.
- Material-slot order changed during import.
- JSON references a master Material instead of a Material Instance.
- The Material Instance's parent is unresolved.
- A custom texture was not included in the chunk or filtered PAK.

### Face makeup or eyes do not change

- `FaceMaterials` uses body slot indices instead of face slot indices.
- `FaceMorphPath` and `FacePath` were confused.
- Face material overrides were not cleared.
- The requested face Morph Target is absent from the in-game face mesh.

### The PAK contains unrelated assets

- The Primary Asset Label is in the wrong folder.
- `Label Assets in My Directory` labels a folder that is too broad.
- The same Chunk ID is used by unrelated labels.
- Test assets were saved below `/Game/mods/Author/Mod_Name/`.

### Game crashes before the menu

- Assets were authored or cooked with the wrong Unreal Engine version.
- `.utoc` or `.ucas` files were installed instead of only the legacy `.pak`.
- A placeholder or copied vanilla asset overrides a real game package.
- The PAK uses the wrong mount point.
- An incompatible cooked dependency was included.

---

## 14. Release checklist

### Project

- [ ] Project opened and cooked in Unreal Engine 5.3.
- [ ] Project name is exactly `DS`.
- [ ] `CanUseUnversionedPropertySerialization=False` is set.
- [ ] Packaging map is outside `/Game/mods/`.

### Assets

- [ ] All distributable assets are below `/Game/mods/Mod_Author/Mod_Name/`.
- [ ] Only `Mesh`, `Materials`, and `Textures` subfolders are used for release assets.
- [ ] Material Instances only; no custom or copied master Materials.
- [ ] No extracted vanilla asset is redistributed.
- [ ] Body and face material-slot order is verified.
- [ ] Skeleton hierarchy and Morph Target names are verified.
- [ ] Physics Asset and physics Animation Blueprint paths are verified.

### Packaging

- [ ] Primary Asset Label is a Data Asset, not a Blueprint subclass.
- [ ] Unique Chunk ID is greater than `0`.
- [ ] Cook Rule is `Always Cook`.
- [ ] `Label Assets in My Directory` is enabled.
- [ ] Asset Audit contains only the expected mod and dependencies.
- [ ] Final release contains one legacy `.pak` and no `.utoc`/`.ucas`.
- [ ] `UnrealPak -List` shows `../../../DS/Content/mods/Mod_Author/Mod_Name/`.

### JSON

- [ ] Filename begins with `DSMS-`.
- [ ] `Version` is `3`.
- [ ] `UniqueID` is unique.
- [ ] `TargetCharacterID` matches the intended playable character.
- [ ] All Unreal object paths use `/` and repeat the object name after the dot.
- [ ] Generated class path ends in `_C`.
- [ ] Material indices exist in the cooked mesh.
- [ ] JSON is valid UTF-8 with no comments or trailing commas.

### Test

- [ ] Correct character selected before applying the preset.
- [ ] Idle, movement, combat, and switching work.
- [ ] No A-pose.
- [ ] Body and face materials work.
- [ ] Body and face Morph Targets work.
- [ ] Secondary physics works.
- [ ] Base outfit can be restored.
- [ ] UE4SS log contains no unresolved path or apply failure.

---

## 15. Quick reference

```text
Engine:
Unreal Engine 5.3 only

Project name:
DS

Unreal mod root:
/Game/MODS/Mod_Author/Mod_Name/

Final PAK installation folder:
DragonSword  Awakening\DS\Content\Paks\~mods\

Loose JSON library root:
DragonSword  Awakening\DS\Content\Paks\~mods\HMV_DS_SELECTOR\

JSON filename prefix:
DSMS-

DSMS version:
0.7.0

JSON schema:
Version 3

Release container:
Legacy .pak only
```

---

## 16. References

- [Epic Games: Preparing Assets for Chunking](https://dev.epicgames.com/documentation/en-us/unreal-engine/preparing-assets-for-chunking-in-unreal-engine)
- [Epic Games: Cooking Content and Creating Chunks](https://dev.epicgames.com/documentation/en-us/unreal-engine/cooking-content-and-creating-chunks-in-unreal-engine)
