# Folder Structure — Death and Taxes

Annotated guide to the Source and Content layouts.
Follow this structure when adding new files — do not create ad-hoc folders.

---

## Source Tree

```
Source/DeathAndTaxes/
│
├── Core/                   Game framework classes (singleton-level)
│   ├── DATGameMode         Server rules: spawn, extraction validation, vent auth
│   ├── DATGameState        Replicated world state: vent ready, monument phase
│   ├── DATGameInstance     Session-persistent cache: identity, inventory, honor
│   ├── DATPlayerController Input routing, CommonUI stack owner
│   └── DATPlayerState      Replicated identity: honor rank, clan tag, W/L record
│
├── Character/              All living pawns
│   ├── DATCharacterBase    Abstract base: owns ASC + AttributeSet, death/extraction events
│   └── DATPlayerCharacter  Player-controlled orc: camera rig, Enhanced Input, vent launch
│
├── Abilities/              Gameplay Ability System layer
│   ├── DATAbilitySystemComponent   Project ASC: feat grant helpers, combo slot activation
│   ├── DATAttributeSet             All 20 attributes + resource pools + fatigue + weapon weights
│   └── DATGameplayAbility          Base ability: combo slot awareness, weapon pillar check
│
├── Combat/                 Shared combat type definitions (header-only)
│   └── DATCombatTypes      FCharacterRequirement, FFlatModifierRule, FDamageDiceRule,
│                           FIWRRule, FRollOptionRule, FAuraRule
│
├── Items/                  Item data classes
│   ├── DATItemTypes        EDATItemType enum, FDATSocketSlot struct
│   ├── DATWeaponDataAsset  Damage dice, swing speed, pillar tag, weapon weights, traits
│   └── DATArmorDataAsset   Soak, hardness, presence tags, socket capacity
│
├── Progression/            Character advancement layer
│   ├── DATRaceDataAsset    HP, speed, size, attribute biases, innate rules
│   ├── DATFeatDataAsset    Feat tag, action traits, requirements, cost, cooldown, ability class
│   ├── DATBookItemDataAsset World mesh, flavor lore, decipher DC, linked feat
│   └── DATFeatComponent    Grimoire state: known feats, study queue, study progress
│
├── Social/                 Honor, renown, clan (types only at this stage)
│   └── DATSocialTypes      EDATHonorRank, FDATClanInfo, FDATHonorRecord, FDATNameplate
│
├── AI/                     NPC intelligence
│   └── DATAIControllerBase Sight + hearing perception, aggro interface
│
├── UI/                     HUD and widget layer
│   └── DATHUDBase          CommonUI activatable widget stack owner
│
└── World/                  Persistent world actors
    ├── DATExtractionZone   Recall trigger sphere, suppression flag, channel start
    └── DATVentLaunchActor  Vent launch pad, launch velocity, GameState vent-ready check
```

---

## Content Tree

```
Content/
│
├── _Core/                  Game framework Blueprint overrides
│                           (BP_GameMode, BP_PlayerController, etc.)
│
├── Characters/
│   ├── Orc/                Orc character content (player + orc NPCs) — from the Toon RTS Orcs pack
│   │   ├── Meshes/         SK_Orc_* bodies + unit variants; Parts/{Cavalry,Units}/ modular pieces
│   │   ├── Rig/            Orc_*_Skeleton, Orc_*_PhysicsAsset
│   │   ├── Animations/     Per-unit sets: Archer, Cavalry*, Infantry, Mage, Spearman, Worker…
│   │   ├── Materials/      M_Orcs_Parent, M_Wolf_Parent, MI_Orc_* skin tints, MI_Wolf_*
│   │   └── Textures/       T_Orcs_* atlases
│   ├── Player/             Player-specific setup (camera rig, input) if it needs its own folder
│   ├── Enemies/            Vaelmoor human enemies
│   └── NPCs/               Home base non-combat characters
│
├── World/
│   ├── Vorgathul/          Home base — volcanic caldera, vent launch pads, clan halls
│   ├── Ashreach/           No-man's land — ash flats, natural PvP band around the volcano
│   ├── Provinces/          Eight Vaelmoor provinces (Kaltspine, Thornwake, Greyfen…)
│   └── Monuments/          Raid encounter volumes — The Throat, Obsidian Shelf, etc.
│
├── Items/
│   ├── Weapons/            Weapon actor Blueprints; Meshes/ holds SM_Orc_weapon_*, shields, etc.
│   ├── Armor/              Armor actor Blueprints
│   ├── Tomes/              Book world items (linked to DATBookItemDataAsset)
│   └── Consumables/        Potions, food, utility items
│
├── DataAssets/             Primary Data Assets — fill these in the editor
│   ├── Races/              DA_Race_Orc, DA_Race_Dwarf, DA_Race_Human…
│   ├── Feats/              DA_Feat_CrushingBlow, DA_Feat_Riposte…
│   ├── Weapons/            DA_Weapon_Hammer, DA_Weapon_Dagger…
│   ├── Armor/              DA_Armor_IronPlate…
│   └── Books/              DA_Book_TomeOfRiposte…
│
├── UI/
│   ├── HUD/                In-world HUD widgets (health bars, combo HUD)
│   ├── Menus/              Market, loadout, clan, character screen
│   └── CommonUI/           Shared CommonUI widget library and style sets
│
├── VFX/                    Niagara systems and material FX
├── Audio/                  Sound cues and audio assets
│
└── Maps/
    ├── Persistent/         World Partition main world (the persistent shared zone)
    └── Dev/                Small isolated test maps + vendor reference (e.g. the modular-orc
                            assembly test Blueprints) — not shipped
```

> **Third-party packs:** integrate imported packs into the trees above rather than leaving them
> as a top-level vendor folder. The **Toon RTS Orcs** pack was redistributed into `Characters/Orc/`,
> `Items/Weapons/Meshes/`, and `Maps/Dev/`; its demo maps were dropped.

---

## Naming Conventions

| Type | Convention | Example |
|---|---|---|
| C++ class | `UDAT` / `ADAT` prefix | `ADATPlayerCharacter` |
| Blueprint | `BP_` prefix | `BP_PlayerCharacter` |
| Data Asset | `DA_` prefix | `DA_Weapon_Hammer` |
| Widget | `WBP_` prefix | `WBP_HUD_ComboBar` |
| Material | `M_` / `MI_` | `M_OrcSkin`, `MI_OrcSkin_Red` |
| Map | `L_` prefix | `L_Vorgathul_Persistent` |
| Enum | `EDAT` prefix | `EDATHonorRank` |
| Struct | `FDAT` prefix | `FDATNameplate` |
