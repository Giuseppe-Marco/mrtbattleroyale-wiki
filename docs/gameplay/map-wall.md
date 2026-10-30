# Spawn picker (map wall)

In **SKYDIVE** arenas you can build a **wall of maps** in the waiting lobby that shows the arena from above. While waiting for the match, players **click a point of the wall** to choose where they drop: when the match starts they are launched in the air above that point and glide down.

!!! info "Where it works"
    - Only arenas with the **SKYDIVE** deploy mode (in SPAWNS arenas the wall can't be built).
    - Never in **events**: event matches always use the arena's launch points.

## For players

- In the waiting lobby, **right-click** (or left-click) the point of the wall where you want to land. You can click from up to 32 blocks away.
- A red pin appears on the wall where you chose. **Only you** see it (party members see their leader's pin). You can change it as many times as you want.
- **5 seconds before the start** the choice is locked, so the deploy is calculated calmly.
- **In a party**, the **party leader** chooses for everybody and the whole party lands together. The other members see the leader's pin.
- **Didn't choose?** You are launched as usual, from the arena's launch points.
- Points outside the zone are pulled back inside it.

## For admins: building the wall

1. Edit the arena (`/br setup` → the arena). It must be a **SKYDIVE** arena with its **zone center and radius** set.
2. Go to the **waiting lobby** of the arena and look at a **flat vertical wall**: the block you look at becomes the **center** of the map wall.
3. Run `/br setup mapwall` (7×7 maps), or `/br setup mapwall <width> <height>` (1 to 15 maps per side). In the GUI: **Spawns → Launch settings → Map wall**, left click.
4. The plugin places invisible, fixed glow item frames and **draws the map** as a **3D model** of the playable area, seen at an angle: real block colours, relief, block sides, buildings, light and shadows. The progress is shown in the action bar.

Every frame needs a **solid block behind it** and **free space in front**: if something is in the way, the command tells you how many places are blocked and builds nothing.

| Command | GUI (Launch settings → Map wall) | What it does |
|---|---|---|
| `/br setup mapwall [width height]` | Left click | Builds the wall on the wall you look at (replaces the old one) |
| `/br setup mapwall render` | Right click | Redraws the map (after you changed the map) |
| `/br setup mapwall remove` | ++shift++ + click | Removes the wall and its picture |
| `/br setup mapwall style <3d\|flat> [rotation]` | - | Changes how the map is drawn and redraws it |

### 3D or flat

| Style | What you get |
|---|---|
| `3d` (default) | A round model of the playable area seen at an angle, like a tabletop model. `rotation` turns it (degrees, default 45). |
| `flat` | The map seen straight from above, like a vanilla map, north at the top. Walls built before the 3D view stay flat until you change them. |

In 3D every point of the picture is linked to the exact point of the map it shows, so clicks, pins and place names stay accurate over mountains and buildings. Clicking the background around the model does nothing.

## Place names

Like the named banners on vanilla maps, you can write the names of the places on the wall (Desert, Jungle, Old Town...). Go to the place **in the arena's map** and run:

| Command | What it does |
|---|---|
| `/br setup mapwall label add <name>` | Adds a name where you stand. MiniMessage colours work: `<gold>Old Town` |
| `/br setup mapwall label remove [name]` | Removes that name, or the one closest to you |
| `/br setup mapwall label list` | Lists the names with their coordinates |
| `/br setup mapwall label clear` | Removes every name |

The names are drawn by Minecraft itself as map markers (a white banner with the text under it), so they stay sharp. Every player sees them, together with their own pin. They are saved with the wall and kept when you rebuild it.

!!! tip "Good to know"
    - The wall is saved immediately (no need for `/br setup save`) in `mapwalls.yml`, and the picture in `mapwalls/<arena>.png`: it is drawn **once**, not at every restart.
    - Drawing loads the map a few chunks at a time and never generates new terrain, so it doesn't freeze the server. Speed: `setup.chest-scan.chunks-at-once` in `config.yml`.
    - The frames can't be broken, rotated or emptied by players.
    - If you change the zone center or radius, rebuild the wall so the picture matches the new area.

## Settings (`game.yml`)

```yaml
map-wall:
  # Last seconds of the countdown in which the choice is locked.
  lock-seconds: 5
  # From how far (blocks) players can click the wall.
  click-distance: 32
```

Both can also be set per arena (`settings:` of the arena file). The launch height is the one of the arena's nearest launch point (or `deploy.skydive-default-height`), never less than 40 blocks above the ground at the chosen point.
