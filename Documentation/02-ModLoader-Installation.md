# Installation
## 2.1 Install UE4SS

Download and extract [UE4SS-experimental](https://github.com/UE4SS-RE/RE-UE4SS/releases) in the game's Win64 executable directory:


```text
DragonSword  Awakening/DS/Binaries/Win64/
```

After installation, this directory must contain the UE4SS loader and a folder named `ue4ss`.

## 2.2 Install DSMS-ModLoader (LogicMods)

Copy:

```text
HMV_DS_MeshSelector_UI.pak
```

to:

```text
DragonSword  Awakening/DS/Content/Paks/LogicMods/
```

The final path must be:

```text
DragonSword  Awakening/DS/Content/Paks/LogicMods/HMV_DS_MeshSelector_UI.pak
```


> [!NOTE]
> This Blueprint component belongs in `LogicMods`, not directly in `~mods`.
> LogicMods uses a `.pak` file only.


## 2.3 Install the DSMS-ModLoader scripts dependencies

Copy the complete folder:

```text
HMVDSMeshSelector
```

to:

```text
DragonSword  Awakening/DS/Binaries/Win64/ue4ss/Mods/
```


---


[← Guide Home](https://github.com/Hoverbike51/DSMS-ModLoader) · [← Previous](01-Overview.md) · [Next →](03-Installing-presets.md)
  
