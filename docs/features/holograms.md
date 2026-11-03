# Map holograms

A **3D miniature of an arena** floating in your hub or lobby and turning slowly on itself, like a painted model: coasts, biomes, mountains and big buildings in plain colours, with the arena's name, mode and players above it. It's made of display entities, so **no other plugin is needed**.

## Two kinds of model

| | **Full-detail model** (default) | **Block model** |
|---|---|---|
| Detail | Up to 512 columns per side, with the real colours and relief of the map | About 40 cells per side |
| Entities | **One**, whatever the size | Up to `max-parts` |
| Needs | A **resource pack** (ItemsAdder or your own) | Nothing |
| Pack weight | About 300 KB per map at resolution 256 | None |

The block model is used when `model.enabled` is `false`, or until an arena has a generated model.

!!! warning "Players need the pack"
    Without it the full-detail model is a broken cube. If you can't use a resource pack, set `model.enabled: false`.

### With ItemsAdder

`/br hologram rebuild <arena>` (or the first `add`) writes the model into `plugins/ItemsAdder/contents/mrtbattleroyale/`. Then run **`/iazip`**: that's all.

!!! tip "Older clients (1.21.1)"
    The plain model uses the `item_model` component, which exists only from 1.21.4. For older clients, create an ItemsAdder item named **`mrtbr:map_<arena>`** (arena name in lower case, e.g. `mrtbr:map_lighthouse`) that uses the model `mrtbr:item/map_<arena>` written by the plugin. When that item exists the hologram uses it, with its custom model data; otherwise it falls back to `item_model`.

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
    - **Trees don't count as height** (forests stay green, without spikes). **Buildings keep their real height**: glass, slabs, stairs and walls count, and every column of the full-detail model takes the highest point of its area, so narrow towers don't get flattened.
    - The full-detail model is in **real proportions** (times `model.height-scale`), capped by `model.max-height`; sides hidden by a taller neighbour are left out of the file to keep the pack light.

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
    # Heights in real proportion to the width, times this (1 = true scale).
    height-scale: 1.5
    # ... but never taller than this share of the model's width.
    max-height: 0.4
    # Blocks per height step: 1 = every block (heavier), 2 = lighter.
    height-step: 2
  # Height of the text above the highest point of the model.
  label-height: 1.0
  # It appears when a player is within these blocks.
  activation-radius: 48
  # Entities created per tick when it appears.
  spawn-per-tick: 60
```

The text above the model is `map-hologram.label` in the language files (`<arena>`, `<mode>`, `<players>`, `<max>`) and updates by itself.

When several arenas use the **same map** (for example a Solo, a Duo and a Squad one), one hologram is enough: name, mode and players follow the arena with the most players on that map, so the text says SQUAD while a Squad match fills up. With nobody on any of them it shows the hologram's own arena.

`max-parts` is the main trade-off: every piece is one entity for the clients near the hologram. With the default 800 a varied map shows about 40 cells per side, enough to recognise coasts, lakes and mountains; simpler maps get more detail. Change it, then run `/br hologram rebuild <arena>`.
