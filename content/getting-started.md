+++
title = "Getting Started"
group = "Getting Started"
order = 1
description = "Install Gantry, configure your project, and author your base experience."
+++

# Getting Started

Gantry is a game-agnostic content framework for Unreal Engine. It composes
**experiences** into a declared dependency tree, activates them in stages
you can observe, and tracks every load as a named, tagged scope. It
supports Unreal Engine 5.6, 5.7 and 5.8.

> [!WARNING]
> **Beta engine features.** Gantry builds on Unreal's **Game Features** and **Modular Gameplay**
> plugins, which Epic marks as beta. Gantry enables both for you.

## Install

1. Install Gantry from Fab into your engine.
2. In your project, open **Edit → Plugins**, search for **Gantry** and enable it.
3. Restart the editor. If your project has C++, regenerate project files.

The plugin ships six modules:

| Module | Type | Purpose |
| --- | --- | --- |
| `GantryCore` | Runtime | Shared types, pluggable class selection, gameplay tag requirement checks |
| `GantryCoreEditor` | Editor | The shared Gantry toolbar menu and editor style |
| `GantryGauge` | Runtime | The load ledger: loads tracked as named, tagged scopes |
| `GantryGaugeUI` | Runtime | A loading screen widget driven by the ledger |
| `GantryGaugeEditor` | Editor | The live Load Gauge panel |
| `GantryExperience` | Runtime | Experience definitions, the experience tree and staged activation |

## Set up your project

Gantry needs four pieces of project configuration. Each is shown as the
`.ini` lines to add; the same settings are available in **Project Settings**.

### 1. The asset manager

The **Load Assets** experience stage and the load ledger run through
`UGantryAssetManager`. Use it directly, or a subclass of your own:

```ini
; Config/DefaultEngine.ini
[/Script/Engine.Engine]
AssetManagerClassName=/Script/GantryGauge.GantryAssetManager
```

### 2. The game feature policy

Gantry's policy refuses a game feature plugin built for a different engine,
or outside the game version range it declares, before it mounts:

```ini
; Config/DefaultGame.ini
[/Script/GameFeatures.GameFeaturesSubsystemSettings]
GameFeaturesManagerClassName=/Script/GantryExperience.GantryGameFeaturesProjectPolicies

[/Script/GantryExperience.GantryGameFeaturesProjectPolicies]
; Turn off while iterating locally to log incompatibilities instead of refusing them.
bEnforceCompatibility=True
```

### 3. Asset manager scan rules

The asset manager has to find your experiences, and Game Features requires
a rule for its own `GameFeatureData` assets:

```ini
; Config/DefaultGame.ini
[/Script/Engine.AssetManagerSettings]
+PrimaryAssetTypesToScan=(PrimaryAssetType="GantryExperience",AssetBaseClass="/Script/GantryExperience.GantryExperienceDefinition",bHasBlueprintClasses=True,bIsEditorOnly=False,Directories=((Path="/Game/Experiences")),SpecificAssets=,Rules=(Priority=-1,ChunkId=-1,bApplyRecursively=True,CookRule=AlwaysCook))
+PrimaryAssetTypesToScan=(PrimaryAssetType="GameFeatureData",AssetBaseClass="/Script/GameFeatures.GameFeatureData",bHasBlueprintClasses=False,bIsEditorOnly=False,Directories=((Path="/Game/Unused")),SpecificAssets=,Rules=(Priority=-1,ChunkId=-1,bApplyRecursively=True,CookRule=AlwaysCook))
```

Without the second rule the engine logs an error at every startup and game
feature plugins do not activate.

### 4. The base experience

Every project has exactly one **base experience**: the root of its
experience tree. Everything that can be activated must be reachable from it.

1. Create a data asset of type `GantryBaseExperienceDefinition`, for example
   `/Game/Experiences/EXP_Base`.
2. Point Gantry at it:

```ini
; Config/DefaultGame.ini
[/Script/GantryExperience.GantryExperienceSettings]
BaseExperience=/Game/Experiences/EXP_Base.EXP_Base
```

3. Create experiences of type `GantryExperienceDefinition` and list them in
   the base experience's **Children**. Set its **Default Experience** to the
   one that activates when a map has no other preference.

See [`UGantryBaseExperienceDefinition`](/reference/ugantrybaseexperiencedefinition/)
and [`UGantryExperienceDefinition`](/reference/ugantryexperiencedefinition/) for
every setting.

## Example project

The **GantryExamples** project shows a complete setup: the configuration
above, a base experience, and a sandbox experience with its map. It is linked
from Gantry's Fab listing, and it depends on the Gantry plugin you installed
from Fab rather than bundling its own copy.
