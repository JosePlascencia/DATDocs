# Architecture — Death and Taxes

Technical architecture overview for the UE5.8 implementation.
Read alongside the Master Design Doc (Section 10) and FolderStructure.md.

---

## Engine Version & Platform

| Property | Value |
|---|---|
| Engine | Unreal Engine 5.8 |
| Renderer | Deferred (DX12 SM6), Nanite, Lumen + hardware ray tracing |
| Multiplayer | Dedicated server, server-authoritative |
| World structure | World Partition (persistent open world) |
| Input | Enhanced Input System |
| UI framework | CommonUI |

---

## Module Dependencies (`DeathAndTaxes.Build.cs`)

| Module | Purpose |
|---|---|
| `GameplayAbilities` | GAS — feats, attribute modifiers, gameplay effects |
| `GameplayTags` | Tag registry used for all requirements and state flags |
| `GameplayTasks` | Async tasks backing GAS abilities |
| `NetCore` | Replication helpers (`DOREPLIFETIME`, net conditions) |
| `AIModule` | AIController, perception, behaviour trees |
| `NavigationSystem` | NavMesh queries for AI pathfinding |
| `CommonUI` | Activatable widget stack, input routing for UI |
| `CommonInput` | Platform-agnostic input abstraction for menus |
| `EnhancedInput` | Player input mappings and contexts |
| `UMG` | Unreal Motion Graphics — widget base classes |

---

## Core Framework

```
ADATGameMode          (server only)
    |
    +-- ADATGameState         (replicated to all clients)
    +-- ADATPlayerController  (per connected player)
    |       |
    |       +-- ADATPlayerState    (replicated identity)
    |       +-- ADATPlayerCharacter (pawn)
    |               |
    |               +-- UDATAbilitySystemComponent
    |               +-- UDATAttributeSet
    |               +-- UDATFeatComponent
    |
    +-- UDATGameInstance      (session-persistent, local only)
```

### Replication Model

| Data | Owner | Replicated to |
|---|---|---|
| World state (vent, monument phase) | `ADATGameState` | All clients |
| Honor rank, clan tag, W/L | `ADATPlayerState` | All clients |
| Attributes | `UDATAttributeSet` | All clients (`COND_None`) |
| Known feats, study queue | `UDATFeatComponent` | Owner only (`COND_OwnerOnly`) |
| Inventory, grimoire | Server backend | Owner only (not in initial scope) |

---

## Gameplay Ability System (GAS)

### Component Setup

- **ASC owner**: `ADATCharacterBase` (base class for all living actors)
- **Replication mode**: `Mixed` — effects replicated to owner; minimal data to others
- **Attribute Set**: `UDATAttributeSet` — subobject on the character, registered automatically

### Attribute List

| Category | Attributes |
|---|---|
| Primary (1–20 scale) | Strength, Endurance, Agility, Dexterity, Intellect, Wisdom, Charisma, Luck |
| Resources | Health/MaxHealth, Stamina/MaxStamina, Mana/MaxMana |
| Expedition | ExpeditionFatigue (0–100, resets on extraction) |
| Weapon weights | WeaponStrengthWeight, WeaponDexterityWeight, WeaponIntellectWeight |
| Meta (transient) | IncomingDamage (damage pipeline scratch attribute) |

### Growth Model (design doc §4)

Attributes increase via XP earned **during a raid** through actions:
- Strength XP: granted by swinging melee weapons
- Endurance XP: granted by taking damage
- Intellect XP: granted by using tomes and spells

On **successful extraction**, pending XP locks in as permanent attribute points.
On **death**, all pending XP for the run is lost.
Expedition Fatigue penalises XP rate at high values; resets to 0 on extraction.

---

## Gameplay Tag Taxonomy

Full registry in `Config/DefaultGameplayTags.ini`. Key namespaces:

| Namespace | Purpose |
|---|---|
| `Actor.*` | Race, size, sensory identity |
| `Stat.*` | Attribute and resource tags |
| `Item.*` | Equipped gear tags (granted by weapon/armor on equip) |
| `State.*` | Transient combat/world flags (Off-Guard, In-Channel…) |
| `Trait.*` | Weapon traits and combo slot tags (Opener/Press/Finisher) |
| `Damage.*` / `Effect.*` | Damage typing and conditions (Bleed, Burn…) |
| `Ability.*` | Feat ability tags |
| `Social.*` | Honor rank, clan membership |
| `Pillar.*` | Class pillar tags (Tank/Mage/Healer/MeleeDPS) |

Requirements are **always evaluated as tag queries** — no duplicate `Requirement.*` tags.

---

## Data Asset Hierarchy

All authored in `Content/DataAssets/` and loaded via the Asset Manager.

```
UPrimaryDataAsset
    ├── UDATRaceDataAsset       Races — biases, innate rules, racial feats
    ├── UDATFeatDataAsset       Feats — requirements, costs, combo slot, ability class
    ├── UDATBookItemDataAsset   Tomes — decipher DC, linked feat
    ├── UDATWeaponDataAsset     Weapons — dice, swing speed, pillar, traits, weights
    └── UDATArmorDataAsset      Armor — soak, hardness, presence tags, sockets
```

Asset IDs follow the pattern `Type/AssetName`:
- `Race/DA_Race_Orc`
- `Weapon/DA_Weapon_Hammer`
- `Feat/DA_Feat_CrushingBlow`

---

## Combat Types Reference (`DATCombatTypes.h`)

| Struct | Description |
|---|---|
| `FCharacterRequirement` | Tag query + min attributes + prerequisite feats — gate for feats and items |
| `FFlatModifierRule` | Typed bonus stacking (Circumstance/Status/Item — highest wins; Untyped stacks) |
| `FDamageDiceRule` | Injects bonus dice on tag condition (e.g. +1d6 Precision when target is Off-Guard) |
| `FIWRRule` | Immunity / Weakness / Resistance resolution |
| `FRollOptionRule` | Positional tag evaluation — grants/removes Dodge, Parry, crit range |
| `FAuraRule` | Spherical overlap emitting presence tags to allies or foes |

---

## Combo Chain System

Abilities slot into **Technique Sequences**:

```
[Opener] → activates → grants State.Combat.OpenerActive
           [Press]  → activates → grants State.Combat.PressActive
                       [Finisher] → weapon group effect fires
```

Each `UDATGameplayAbility` declares its `EDATComboSlot` (None / Opener / Press / Finisher).
Activation is gated by the `ComboGateTag` on the owner's ASC.
The single hotkey for the sequence calls `UDATAbilitySystemComponent::TryActivateComboSlot()`.

---

## World Systems

### Extraction Zone (`ADATExtractionZone`)
- Sphere trigger volume; overlapping starts the recall channel
- `bRecallSuppressed` flag (replicated) disables recall near monuments/raids
- Channel duration: 8 seconds by default (configurable per zone)
- Interruption logic: to be implemented (Phase 3 engineering roadmap)

### Vent Launch (`ADATVentLaunchActor`)
- Platform volume on the Vorgathul vent
- Validates `ADATGameState::bVentReady` before launching
- Applies `LaunchVelocity` via `ADATPlayerCharacter::OnVentLaunch()`

---

## Engineering Roadmap (from design doc §10.4)

| Phase | Core Systems | Verification Target |
|---|---|---|
| 1 | Tag registry, `UDATAttributeSet` | Attributes gain XP from swinging/taking damage; fatigue accumulates |
| 2 | Data Assets, `FCharacterRequirement` | Player finds tome, inspects requirements, studies at home, ability unlocks |
| 3 | Attack table, GCD, Off-Guard detection | Flanking removes Dodge/Parry; crits escalate |
| 4 | Rune socketing, Technique Sequence HUD | Full combo chain fires on one hotkey; crits trigger weapon group effect |

---

## Open Questions (from design doc §11)

> These are blocking decisions — work in their dependent phases cannot complete until resolved.

- **Weapon-to-pillar mapping** — which weapons belong to Tank / Mage / Healer / DPS, and how hard those edges are
- **Weapon attribute boosting** — how each weapon re-weights the attributes behind its abilities
- **Armor Presence / Guardian Passives** — rescoping aggro thresholds now that threat rides on abilities
- **Weapon save cost and odds on death** — and anti-exploit gating on the death-during-extraction reward
- **Human faction structure** — single Vaelmoor crown vs. multiple provincial agendas
- **The Divide** — true cause withheld by design
