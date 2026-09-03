# Death and Taxes — Design & Architecture Documents

World design, systems design, and technical architecture for **Death and Taxes** (formerly *Zug Zug*).
A persistent-world PvPvE extraction RPG built on Unreal Engine 5.8.

---

## Documents

### Design
| File | Description |
|---|---|
| [Death and Taxes Master Design Doc v3.html](./Death%20and%20Taxes%20Master%20Design%20Doc%20v3.html) | Full game design — world, combat, progression, honor system, technical spec |
| [Death and Taxes Work Split Proposal.dc.html](./Death%20and%20Taxes%20Work%20Split%20Proposal.dc.html) | Lane A / Lane B dev split, phase gating, open decisions |
| [Death and Taxes World Map.dc.html](./Death%20and%20Taxes%20World%20Map.dc.html) | Survey Plate 01 — Vaelmoor continent, province/biome breakdown |

### Technical
| File | Description |
|---|---|
| [Architecture.md](./Architecture.md) | System overview, GAS setup, tag taxonomy, data asset hierarchy, combat types, open questions |
| [FolderStructure.md](./FolderStructure.md) | Annotated Source + Content tree with purpose of every folder and naming conventions |
| [GettingStarted.md](./GettingStarted.md) | Prerequisites, cloning, first compile, LFS setup, branch strategy, commit conventions |

---

## Game Overview

Death and Taxes is a **persistent-world PvPvE extraction RPG**.

- Players are **Zug'"'"'thar orcs** — holding only one volcano (**Vorgathul**) in the middle of a human kingdom (**Vaelmoor**)
- Every raid is an **incursion** into enemy territory; every extraction is a run back to a single point
- **Full loot on death** (protected bag + weapon insurance system)
- Progression through **growth-by-doing attributes** (Strength XP from swinging, Endurance from taking hits)
- Abilities unlocked by **studying tomes** found in raids — at home base after extracting
- **Honor / Renown / Clan** social layer with assembled nameplates: `<given name>'"'"'<rank> the <title> of the <clan>`

> Working Draft v3 — Internal
