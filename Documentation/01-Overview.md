# Overview
## DragonSword Mesh Selector


The menu can only be opened in UE World (after map loaded). Press **F5** to open or close it. Press **F6** to rescan the JSON library after adding or editing a preset.\
The selector scans this mandatory directory recursively:

Any folder created below that directory is scanned. This allows authors and users to organize presets without placing every JSON in one folder.

---
# Reference Locations

## DSMS-Mod Loader version

```text
0.7.0
```

### JSON schema

```text
Version 3
```

### JSON filename prefix:
```text
DSMS-
```

### Loose JSON library root

```text
DS\Content\Paks\~mods\HMV_DS_SELECTOR\
```

### Unreal mod root

```text
/Game/MODS/Mod_Author/Mod_Name/
```

### PAK filename prefix

```text
DSMS_
```

### Final PAK installation folder:

```text
DragonSword  Awakening\DS\Content\Paks\~mods\
```

### Blueprint LogicMod:

```text
DS\Content\Paks\LogicMods\HMV_DS_MeshSelector_UI.pak
```

### Custom Asset Organization

All custom assets must be authored below a unique `/Game/MODS/` namespace.

Required structure:

```text
/Game/MODS/Mod_Author/Mod_Name/
/Game/MODS/Mod_Author/Mod_Name/Mesh/
/Game/MODS/Mod_Author/Mod_Name/Materials/
/Game/MODS/Mod_Author/Mod_Name/Textures/
/Game/MODS/Mod_Author/Mod_Name/Icons/
```

On Unreal project, `/Game/` corresponds to the project's `Content` directory:

```text
Project/Content/MODS/Mod_Author/Mod_Name/
Project/Content/MODS/Mod_Author/Mod_Name/Mesh/
Project/Content/MODS/Mod_Author/Mod_Name/Materials/
Project/Content/MODS/Mod_Author/Mod_Name/Textures/
Project/Content/MODS/Mod_Author/Mod_Name/Icons/
```

Example:

```text
Content/
`-- MODS/
    `-- HoverMODSVault/
        `-- Astria_Red_Swimsuit/
            |-- Mesh/
            |   `-- SK_Astria_Red_Swimsuit
            |-- Materials/
            |   |-- MI_Astria_Red_Swimsuit
            |   `-- MI_Astria_Red_Skin
            `-- Textures/
            |   |-- T_Astria_Red_Swimsuit_D
            |   `-- T_Astria_Red_Swimsuit_N
            `-- Icons/
                `-- T_Icon
```

---

[← Guide Home](https://github.com/Hoverbike51/DSMS-ModLoader) · [← Previous](00-Changelog.md) · [Next →](02-Mod-Installation.md)
