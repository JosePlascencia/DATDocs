# build/ — Vaelmoor landscape source

Source inputs for the `L_Vaelmoor` World Partition blockout in the game repo
(`Content/Maps/Persistent/L_Vaelmoor`). Kept here so the landscape can be
re-imported or regenerated without hunting for the originals.

| File | What it is |
|---|---|
| `vaelmoor_blockout_1009x2017.r16` | 16-bit raw heightmap, 1009 × 2017, little-endian, **no header** |
| `vaelmoor_blockout_1009x2017.raw` | byte-identical copy of the `.r16` |
| `vaelmoor_blockout_1009x2017_16bit.png` | same heightmap as 16-bit PNG — **use this to import** (carries dimensions; UE can't read the headerless raw) |
| `vaelmoor_height_v4.png` | 8-bit height reference / preview |
| `vaelmoor_provinces_v4.png` | province colour mask (for future paint-layer / data-layer work) |
| `vaelmoor_poi_transforms.json` | landscape transform + monument & vent-mouth world coordinates |

## Import parameters (from `vaelmoor_poi_transforms.json`)

| | |
|---|---|
| Resolution | 1009 × 2017 verts |
| Landscape config | 8 × 16 components · 63 quads/section · 2 sections/component |
| Scale (uu) | 800 / 800 / 400 |
| Extent | 8064 × 16128 m  (806 400 × 1 612 800 uu, corner-anchored at origin) |
| Height range | ±1024 m at Z-scale 400; actual data −205 … 993 m |
| Sea level | pixel 32768 → world Z 0 |
| World Partition | grid size 2 → 32 `LandscapeStreamingProxy` actors |

### Editor: New Landscape → Import from File
1. New level from the **Open World** template, delete its default landscape.
2. Landscape mode → **Import from File** → point at `vaelmoor_blockout_1009x2017_16bit.png`
   (or the `.r16` and set resolution manually to **1009 × 2017**).
3. Scale **800 / 800 / 400**, section size **63×63**, **2** sections/component,
   **8 × 16** component count, WP grid size **2**.

Monuments and vent mouths from the JSON are **not placed yet**.
