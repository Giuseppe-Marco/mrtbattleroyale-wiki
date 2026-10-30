# Map holograms

A **3D miniature of an arena** floating in your hub or lobby and turning slowly on itself, like a painted model: coasts, biomes, mountains and big buildings in plain colours, with the arena's name, mode and players above it. It's made of display entities, so **no other plugin is needed**.

## Two kinds of model

| | **Full-detail model** (default) | **Block model** |
|---|---|---|
| Detail | Up to 512 columns per side, with the real colours and relief of the map | About 40 cells per side |
| Entities | **One**, whatever the size | Up to `max-parts` |
| Needs | A **resource pack** (ItemsAdder or your own) | Nothing |
| Pack weight | About 130 KB per map at resolution 128, 400 KB at 256 | None |

The block model is used when `model.enabled` is `false`, or until an arena has a generated model.

!!! warning "Players need the pack"
    Without it the full-detail model is a broken cube. If you can't use a resource pack, set `model.enabled: false`.

### With ItemsAdder

`/br hologram rebuild <arena>` (or the first `add`) writes the model into `plugins/ItemsAdder/contents/mrtbattleroyale/`. Then run **`/iazip`**: that's all.

### With your own pack

The model is written in `plugins/MRTBattleRoyale/hologram-pack/assets/mrtbr/`: copy that `assets` folder into your resource pack. Models use the `items/` format of Minecraft 1.21.4+.

## Adding one

Stand where you want it and run:

```
/br hologram add <arena> [size]
```

`size` is the width of the model in blocks (2 to 40, default 8). The first time, the plugin reads the arena's map: progress is shown in the action bar, the server doesn't freeze and no new terrain is generated. The same model is used by all the holograms of that arena.

| Command | What it does |
|---|---|
| `/br hologram add <arena> [size]` | Places a hologram here |
| `/br hologram size <size>` | Changes the size of the nearest hologram (within 20 blocks) |
| `/br hologram rebuild <arena>` | Reads the map again, after you changed it |
| `/br hologram remove` | Removes the nearest hologram |
| `/br hologram list` | Lists the holograms |

All of them need the `br.admin.setup` permission.

!!! tip "Good to know"
    - The arena needs its **zone center and radius** set, and its map world must be loaded. The model is a **round** miniature of that area.
    - Holograms exist only while a player is within `activation-radius`: nobody around, no entities. They are never saved in the world.
    - They follow the arena if you rename it, and go away if you delete it.
    - The model uses **plain colour blocks** (concrete, terracotta, smooth sandstone) chosen from the real colours of the map, the same colours as the map wall: stretched textures look bad at this scale.
    - **Trees don't count as height** (forests stay green, without spikes) and the tallest 3% of the map is cut, so a tower doesn't become a pillar. The relief is capped by `max-relief`.

## Settings (`config.yml`)

```yaml
map-hologram:
  enabled: true
  # Default size: blocks across the model.
  default-size: 8
  # How many blocks above your feet it is placed.
  height: 1.5
  # Degrees per second of the rotation (0 = still).
  degrees-per-second: 12
  # Relief reads better exaggerated: 2.0 = twice the real height.
  height-scale: 2.0
  # ... but never taller than this share of the model's size.
  max-relief: 0.16
  # Most pieces of each model: more = more detail, more work for the clients.
  # Needs a rebuild.
  max-parts: 800
  # The full-detail model: one 3D model in the resource pack, shown by a single
  # entity. With enabled false the block model is used.
  model:
    enabled: true
    # Columns per side (32-512). More = more detail, heavier pack.
    resolution: 256
    # Height steps. Fewer = lighter, flatter relief.
    levels: 24
  # Height of the text above the highest point of the model.
  label-height: 1.0
  # It appears when a player is within these blocks.
  activation-radius: 48
  # Entities created per tick when it appears.
  spawn-per-tick: 60
```

The text above the model is `map-hologram.label` in the language files (`<arena>`, `<mode>`, `<players>`, `<max>`) and updates by itself.

`max-parts` is the main trade-off: every piece is one entity for the clients near the hologram. With the default 800 a varied map shows about 40 cells per side, enough to recognise coasts, lakes and mountains; simpler maps get more detail. Change it, then run `/br hologram rebuild <arena>`.
