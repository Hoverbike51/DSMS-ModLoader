# DSMS-ModLoader — Changelog

Current stable version: **0.7.1**  
Target game: **DragonSword: Awakening**  
Profile format: **JSON v3**


## 0.7.1 — Outline safety and Studio compatibility

- Added generalized JSON v3 `BodyOutlinePath`, `BodyOutlineMaterials` and `BodyOutlineClearMaterialOverrides` support for dedicated parallel body-outline meshes.
- Preserved author-selected `PhysicsAnimBlueprintPath` values without character-specific substitution.
- Fixed rejected-target handling so an incompatible preset can no longer clean up the appearance that is already active.
- Added safe world-transition state disposal for body outlines, auxiliary meshes, hidden/linked components, morph caches, followers and pending animation verification.
- Synchronized the runtime contract with DSMS Preset Studio v0.5.2, including `WeaponMaterials.MaterialMatch` selectors.
- Documented the Studio's optional FModel asset index: vanilla paths can be checked against exported game assets, while custom `/Game/MODS/` paths remain author-controlled and are reported as unverified when absent from the index.
- Kept Studio repair non-destructive for custom presets. Known-character references may produce explicit suggestions and safe syntax normalization, but validation never replaces an author's preset by filename, `UniqueID`, or database recipe.

## 0.7.0 — Stable modular release

- Cleaned and finalized the modular UE4SS/Lua runtime.
- Reduced the active script folder to the twelve required Lua modules.
- Added cached JSON scanning for faster and more stable menu operation.
- Changed the controls to:
  - **F5**: open or close DSMS-ModLoader.
  - **F6**: manually reload JSON profiles.
- Added the `CUSTOM`, `COSTUME`, `WEAPON`, `DLC` and `BASE GAME` filters.
- Hidden unsupported `NPC` and `MOUNTS` categories from the public selector.
- Added independent JSON v3 weapon profiles.
- Added single-mesh, multi-part and material-only weapon support.
- Added auxiliary costume meshes and auxiliary Physics Asset support.
- Added exact hidden-component and linked-body corrections.
- Improved transitions between base, DLC, mask and no-mask costumes.
- Improved character-specific outline handling.
- Reorganized native, DLC and custom JSON profile folders.
- Updated the Unreal Engine 5.3 modding guide with reusable JSON v3 recipes.
- Added validated base-game and DLC presets for supported playable characters.

## 0.6.4 — Target protection and runtime stability

- Added mandatory `TargetCharacterID` protection for character-related profiles.
- Blocked incompatible presets before they can modify the active character.
- Fixed a long-running widget callback issue that could break JSON selection and mouse focus.
- Removed the obsolete Lua synchronization with `BP_DSMS_JSONManager`.
- Renamed the `Character` profile category to `Custom`.
- Kept `Character` as a legacy JSON alias.
- Made the secondary `FacePath` optional.
- Added a warning when author-only target protection is disabled.

## 0.6.3 — Modular Lua bridge

- Split the original Lua bridge into specialized modules.
- Separated shared helpers, JSON profile parsing and appearance handling.
- Preserved the validated mesh, animation, physics and Morph Target behavior.

## 0.6.2 — Body Morph Targets

- Added multiple `BodyMorphTargets` per JSON profile.
- Added automatic cleanup of body Morph Targets from the previous profile.

## 0.6.1 — Face materials and Morph Targets

- Fixed detection of DragonSword's in-game face components.
- Added multiple `FaceMorphTargets` per profile.
- Added automatic cleanup of previous face Morph Targets.
- Added independent face and eye Material Instance overrides.

## 0.6.0 — JSON v3

- Introduced the JSON v3 profile format.
- Separated body and face material settings.
- Added independent body and face material override cleanup.
- Added support for the morphable in-game face and optional secondary face mesh.

## 0.5.8 — Readable profile fields

- Introduced the current PascalCase JSON property names.
- Standardized full Unreal object paths using `/Game/.../Asset.Asset`.
- Retained compatibility with older snake_case field names.

## 0.5.6–0.5.7 — Costume physics

- Added `PhysicsAnimBlueprintPath`.
- Added dynamic costume physics Animation Blueprint layers.
- Fixed switching between costumes with different secondary physics.

## 0.5.4–0.5.5 — Native animation preservation

- Replaced compatible meshes directly on the original character body component.
- Preserved the native DragonSword Anim Instance.
- Removed the A-pose for compatible costume meshes.
- Added stronger Physics Asset application and diagnostics.

## 0.5.1–0.5.3 — First complete replacements

- Added body material overrides and Physics Asset paths.
- Added post-apply verification and detailed UE4SS logging.
- Tested follower-mesh animation as an early fallback method.
- Improved UE5 Blueprint class loading.

## 0.4.x — First UE4SS JSON bridge

- Added recursive loose JSON scanning.
- Added schema, ID and Unreal-path validation.
- Connected JSON profiles to the UMG drop-down menus.
- Added the first runtime body and face mesh replacements.

## Initial Blueprint prototype

- Created the `HMV_DS_MeshSelector_UI` LogicMod.
- Created the `WBP_DSMSRuntime` UMG selector.
- Added world-load protection and mouse cursor handling.
- Fixed UMG texture serialization with `CanUseUnversionedPropertySerialization=False`.

## Known limitations

- DSMS modifies an existing possessed playable character; it does not create a complete new playable Pawn.
- DLC `Requirements` values are metadata and do not currently verify ownership.
- NPC and Mount possession are not supported by the public selector.
- Only repeatedly tested JSON profiles should be distributed as stable presets.

---

[← Guide Home](https://github.com/Hoverbike51/DSMS-ModLoader) · [Next →](01-Overview.md)
