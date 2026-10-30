# Map holograms

A **3D miniature of an arena** floating in your hub or lobby and turning slowly on itself: terrain, mountains, lakes and big buildings, with the arena's name, mode and players above it. It's made of display entities, so **no other plugin is needed**.

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
    - Display entities can't show liquids: water and lava are drawn with the blocks set in `water-block` and `lava-block`.
    - Plants, fences, slabs, chests and similar are ignored: the model uses the ground under them. Leaves and full blocks are kept.

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
  # Relief reads better exaggerated: 3.0 = three times the real height.
  height-scale: 3.0
  # Most pieces of each model: more = more detail, more work for the clients.
  # Needs a rebuild.
  max-parts: 600
  # Liquids can't be shown by display entities: the blocks used instead.
  water-block: BLUE_CONCRETE
  lava-block: MAGMA_BLOCK
  # Height of the text above the highest point of the model.
  label-height: 1.0
  # It appears when a player is within these blocks.
  activation-radius: 48
  # Entities created per tick when it appears.
  spawn-per-tick: 60
```

The text above the model is `map-hologram.label` in the language files (`<arena>`, `<mode>`, `<players>`, `<max>`) and updates by itself.

`max-parts` is the main trade-off: every piece is one entity for the clients near the hologram. With the default 600 a map shows about 40 cells per side, enough to recognise coasts, lakes and mountains. Change it, then run `/br hologram rebuild <arena>`.
