# Troubleshooting

## The preset does not appear

Check that:

- The filename ends in `.json`, not `.json.example`.
- The filename begins with the exact `DSMS-` prefix.
- The file is below `DS\Content\Paks\~mods\HMV_DS_SELECTOR`.
- `Version` is `3`.
- `UniqueID` is not duplicated.
- Required strings are not empty.
- JSON syntax contains no comments or trailing commas.
- Every object path starts with `/Game/` and contains `.AssetName`.
- F6 was pressed after adding the file.

Read `UE4SS.log` and search for `JSON warning`.

## The preset is visible but nothing changes

Check that:

- The custom `.pak` is installed.
- The filename begins with the exact `DSMS_` prefix.
- The file is below `DS\Content\Paks\~mods`.
- Its internal mount path is `../../../Game/MODS/...`.
- `BodyPath` matches the cooked Skeletal Mesh name exactly.
- The correct targeted character is selected.
- An older `.pak` is not overriding the new one.

## Target-character protection rejects the preset

The selected character does not match `TargetCharacterID`. Select the intended character first, then apply the preset again.\
Do not remove target protection to force an incompatible costume onto another character.

### Disable Target-character

> [!CAUTION]
> I strongly advise against disabling it!\
> You may encounter unknowns issue that could even cause the game to crash, abnormal gameplay detection and corrupt your save file permanently.

Change `EnableTargetCharacterProtection`=`true` to `false`, but **be careful**: this is intended exclusively for **mod development purposes**.


```lua
-- HMV DragonSword Mesh Selector switches.
-- Restart the game after changing a value in this file.

-- WARNING: KEEP TARGET CHARACTER PROTECTION ENABLED.
-- Disabling this protection is intended only for the mod author's controlled development tests!
-- Applying a preset to the wrong character can create an invalid game state
-- and may corrupt save data. Back up your save before!
-- disabling it. If you do anything reckless, there will be dragons.
local Config = {
    EnableTargetCharacterProtection = true,

    TargetProtectionDisabledWarning =
        'WARNING: TargetCharacterID protection is DISABLED. This switch is for controlled mod-development tests only. ' ..
        'Applying a preset to the wrong character may corrupt save data. Back up your save, or there will be dragons.'
}

return Config


return Config
```

`DragonSword  Awakening\DS\Binaries\Win64\ue4ss\Mods\HMVDSMeshSelector\Scripts\hmv_config.lua`

## The body appears in an A-pose

Likely causes:

- Incompatible skeleton or bone hierarchy.
- Incorrect or missing `PhysicsAnimBlueprintPath`.
- The mesh was imported against a different reference skeleton.
- A base costume preset uses the physics Anim Blueprint from another costume.

Use the exact physics Anim Blueprint associated with the target character/costume and verify skeleton compatibility.

## The body animates but secondary physics are static

`PhysicsAssetPath` alone is not sufficient for many DragonSword costumes. Verify the costume-specific `PhysicsAnimBlueprintPath`, especially for breast, butts, feather, cloth, ribbon, and hair physics.

## Materials are missing, grey, or incorrect

Check that:

- Material paths point to cooked Material Instances.
- All required textures are included in the `.pak`.
- Slot indices match the mesh.
- `BodyClearMaterialOverrides` or `FaceClearMaterialOverrides` is set appropriately.
- The Material Instance uses a compatible parent Material.
- The mod does not ship a newly authored base character Material.

## Face makeup or eye materials are not applied

Verify `FaceMaterials`, not `BodyMaterials`. Confirm the correct face mesh path and face slot indices. Some costumes use their own face and eye Material Instances.

## A morph target does not apply

Check that:

- The target exists in the mesh referenced by `BodyPath` or `FaceMorphPath`.
- `MorphName` matches exactly.
- `Value` is between `0.0` and `1.0`.
- Morph targets were enabled during FBX import and preserved during cooking.

## The game crashes before the main menu

Remove the latest custom `.pak` and check for:

- A package cooked with the wrong Unreal Engine version.
- Io Store output still enabled by mistake.
- Missing dependencies on the packaged asset.

Proceed step by step. Most often, this error is caused by the way the JSON file was modified.

---

[← Guide Home](https://github.com/Hoverbike51/DSMS-ModLoader) · [← Previous](05-Creating-Advanced-json.md)
