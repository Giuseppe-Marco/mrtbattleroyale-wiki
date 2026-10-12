# Zone

The zone is the safe area of the match. It shrinks in **phases**, pushing players together; outside it you take damage every second.

The zone is a **square** centred on the arena's zone centre. Its size is given as a **radius** = half the width in blocks (radius 120 = a 240 × 240 square). The starting radius is the one set with **Zone & Geometry → Zone centre & radius** or `/br setup center <radius>`.

## Phases

Each phase first **waits**, then **shrinks** the zone down to its `target-radius` in `shrink-seconds`. After the last phase the match enters the [deathmatch](match.md#8-deathmatch-and-anti-stall).

```yaml
zone:
  damage-per-second: 2.0
  phases:
  - wait-seconds: 60
    shrink-seconds: 45
    target-radius: 120
  - wait-seconds: 45
    shrink-seconds: 40
    target-radius: 60
  - wait-seconds: 30
    shrink-seconds: 30
    target-radius: 25
  - wait-seconds: 20
    shrink-seconds: 25
    target-radius: 0
```

| Key | Meaning |
|---|---|
| `wait-seconds` | Pause before this phase starts closing |
| `shrink-seconds` | How long the closing takes (`0` = the zone jumps to the new size) |
| `target-radius` | Radius at the end of the phase. `0` = the zone **closes completely**: everyone is outside and takes damage |
| `move` | Optional, see [Moving zone](#moving-zone) |

!!! warning
    Each `target-radius` must be **smaller** than the previous one: the zone never grows. A phase with a radius that is not smaller just holds the zone in place (the console warns you, and the setup GUI shows the phase in red).

### The boss bar

| Boss bar | When |
|---|---|
| Yellow, *Zone shrinks in Xs* | Waiting before a closing |
| Red, *The zone is shrinking!* | Closing |
| Purple, *FINAL SHOWDOWN* | Deathmatch (all phases done) |
| White, *ZONE PAUSED* | An admin paused the zone |

### Editing phases in the GUI

**Zone & Geometry → Zone phases** lists the arena's phases. Click a phase to edit its wait, closing time, final radius and centre move; add phases or delete them (++shift++ + click to confirm), or **Use the global phases** to go back to the ones in `game.yml`.

## Damage

Outside the zone you take `damage-per-second` **half hearts** every second. Edit it in **Zone & Geometry → Damage outside the zone** (±0.5).

Not damaged by the zone:

- downed players (they only bleed out);
- players still gliding in;
- players in the gulag;
- a player just revived, for `knockdown.revive-zone-grace-seconds` (to run back inside).

If you die in the zone everyone reads *X died in the zone!*.

## Moving zone

By default every phase closes toward the same centre. With the moving zone, from a given phase on the **centre drifts** toward a random point, forcing players to move (Fortnite/Warzone style). The new zone always stays inside the original map.

```yaml
zone:
  move:
    enabled: false
    from-phase: 3
    max-drift: 0.6
```

| Key | Meaning |
|---|---|
| `enabled` | Turns the moving zone on |
| `from-phase` | First phase that moves (1 = the first closing) |
| `max-drift` | How far the centre can move, as a fraction of the phase radius (`0` = no movement, `1` = up to one radius) |

A single phase can override this with its own `move` value (a fraction like `0.5`, or `0` to stay put):

```yaml
  - wait-seconds: 60
    shrink-seconds: 45
    target-radius: 120
    move: 0.5
```

In the GUI: **Zone & Geometry → Zone movement**, and **Center move** inside each phase (++shift++ + click = follow the global settings).

## Zone wall and closed zone

```yaml
effects:
  zone-particles: true
  zone-particle-color: 255,60,60
  closed-zone-ring: true
```

- **Zone wall**: a wall of particles along the border, drawn near each player only (when you are within about 14 blocks of the edge). The colour is `R,G,B` (0-255); a colour name like `RED` or `AQUA` works too.
- **Closed-zone ring**: once the zone has **closed completely** (radius 0), a ring of particles in the same colour turns around every player still fighting: nowhere is safe any more. Spectators and gulag players don't get it.

## The border you see

The zone is shown to each player as **their own world border** (only the visual border, the damage is handled by the plugin). The real world border is kept huge, so places off the zone, like a gulag built under or outside the map, still work normally.

- Alive players and spectators see the zone border; spectators are kept inside it too.
- Gulag players have no zone border and no boss bar.

!!! note "Outside the border you can't use blocks"
    Vanilla Minecraft doesn't let you interact with blocks beyond the world border. If you are **outside** the zone and try to eat or drink while looking at the ground, it may fail because you are "using" a block outside the border. **Look at the sky** (or straight ahead into the air) and it works.

## Admin tools

| Command | What it does |
|---|---|
| `/br admin skipzone [arena]` | Skip the wait: the next closing starts now (in the deathmatch: the final collapse starts now) |
| `/br admin zone pause\|resume [arena]` | Freeze / unfreeze the zone (damage outside stays on) |
| `/br admin deathmatch [arena]` | Final zone collapse now |

More in [Admin tools](../reference/admin-tools.md).
