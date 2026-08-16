# Install the `.pak` and JSON

## Install the asset PAK

Copy the final legacy PAK to the Windows directory:

```text
S:\SteamLibrary\steamapps\common\DragonSword  Awakening\DS\Content\Paks\~mods\DSMS_HMV_Astria_RedSwimsuit_P.pak
```

Replace the Steam library root with the user's real game location.

## Install the JSON

Recommended organization:

```text
HMV_DS_SELECTOR/
`-- OtherAuthor/
    `-- ModType/
        `-- MyMod/
            `-- DSMS-MyPreset.json
```

The folder names below `HMV_DS_SELECTOR` are organizational only. The selector scans all child folders recursively.

Example Windows location:

```text
S:\SteamLibrary\steamapps\common\DragonSword  Awakening\DS\Content\Paks\~mods\HMV_DS_SELECTOR\HoverModsVault\COSTUMES\Astria_RedSwimsuit\DSMS-HMV_Astria_RedSwimsuit.json
```

To install a preset:

1. Confirm that its filename begins with `DSMS-` and ends in `.json`.
2. Copy `.json` file into a folder below `HMV_DS_SELECTOR`.
3. Instal its associated custom `.pak` (if the JSON references custom assets) in `mods~`.
4. Start or return to the loaded game world.
5. Press **F5** to rescan without restarting the game, or restart the game.
6. Press **F2**, to open/close the mod menu, choose the desired mod type, then select the mod preset.

For `CUSTOM` and `COSTUME` profiles, first select the "in-game" character named by `TargetCharacterID`, then apply the right preset.\
Target protection prevents a preset from being applied to the wrong character.

Files ending in `.json.example` are documentation templates and are not loaded as active presets.\
Copy the template and rename it to a filename such as `DSMS-ModAuthor-Character-Costume.json` before using it.

> [!IMPORTANT]
> If the `.json` file don't start with the exact `DSMS-` prefix, it will be ignored and reported in `UE4SS.log`.\
> If the `.pak` file don't start with the exact `DSMS_` prefix, it will be ignored and reported in `UE4SS.log`.

---


[← Guide Home](https://github.com/Hoverbike51/DSMS-ModLoader) · [← Previous](02-ModLoader-Installation.md) · [Next →](04-Creating-JSON-presets.md)
