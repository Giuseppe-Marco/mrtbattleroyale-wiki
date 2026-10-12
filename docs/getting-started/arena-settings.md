# Per-arena settings

Every arena plays with the **global rules** of `game.yml`, `gulag.yml` and `economy.yml`, unless it changes some of them for itself. This way a big squad map can have longer zone phases and more airdrops, while a small solo map keeps the defaults.

## How overrides work

- An arena's own values are stored in the **`settings:`** section of its file, `arenas/<Name>.yml`, with the **same paths** as the global files.
- A key that is missing there uses the global value. Change the global file and every arena without its own value follows.
- Overridable sections: **`game`, `zone`, `loot`, `knockdown`, `geyser`, `deploy`, `gulag`, `effects`, `supply-drop`, `sounds`, `rewards`**.
- **Always global**: `progression` (XP and coins), `spectator`, `team` visuals and everything in `config.yml` and `lobby.yml`.
- An arena's **loot table** is chosen in `loot.yml` (`arenas:` section), not in the arena file. See [Loot, chests & airdrops](../gameplay/loot.md).

You don't need to write any of this by hand: the setup GUI does it for you.

## Changing settings in the GUI

Open the arena with `/br setup edit <Name>` (or click it in `/br setup`). Every button that edits a value works the same way:

| Click | Effect |
|---|---|
| **Left click** | Increase the value by the step shown in the lore, or switch it on/off |
| **Right click** | Decrease the value by the step |
| ++shift++ + **click** | Go back to the **global value** (removes the arena's own value) |

The lore always shows the **current value** and whether it is *Set for this arena* or the *Global value (game.yml)*. Values are kept within sensible limits. Changes are written to the file when you **save** the arena.

!!! tip
    Want an arena to follow the global file again? ++shift++ + click each value you changed, or delete the key from its `settings:` section and run `/br reload`.

## The setup GUI, screen by screen

The hub of the setup GUI shows whether the arena is playable, and opens these categories.

### General

| Button | What it sets |
|---|---|
| Team mode | SOLO → DUO → SQUAD (team sizes: `game.duo-size`, `game.squad-size`) |
| Deploy mode | SPAWNS ↔ SKYDIVE |
| Min / Max players | Left click +1 min, right click +1 max, ++shift++ = −1. *global* means `game.min-players` / `game.max-players` |
| Arena info | Prints the full summary in chat (same as `/br setup info`) |

### Zone & Geometry

| Button | What it sets |
|---|---|
| Zone centre & radius | Click to put the centre at your position. Change the radius with `/br setup center <radius>` |
| Zone phases | Opens the phase list (below) |
| Damage outside the zone | `zone.damage-per-second`, ±0.5 half hearts per second |
| Zone movement | `zone.move.enabled`, `zone.move.from-phase`, `zone.move.max-drift`. A phase with its own `move` value ignores these |

**Zone phases.** One clock per phase, showing wait, closing time, final radius and centre move. A phase whose radius is not smaller than the previous one turns **red** (it would not shrink).

- Click a phase to edit it: **Wait** and **Closing time** (±5 s, ++shift++ ±30 s), **Final radius** (±5, ++shift++ ±25; `0` closes the zone completely), **Centre move** (±0.1 fraction of the radius, ++shift++ = follow the global `zone.move` settings), **Delete phase** (++shift++ + click to confirm).
- **+ Add phase** adds one with the same timings as the last phase and half its radius (up to 14 phases).
- **Use the global phases** (++shift++ + click) removes this arena's phases and goes back to the ones in `game.yml`.

The first edit copies the global phases into the arena as a starting point. More in [Zone](../gameplay/zone.md).

### Spawns & Launch

| Button | What it does |
|---|---|
| Add spawn / Clear spawns | Add a spawn at your position / remove them all (SPAWNS mode) |
| Launch points (skydive) | Left click adds a point here (up to 16), right click removes the ones within 15 blocks, ++shift++ + click removes them all |
| Launch settings | `deploy.skydive-default-height`, `deploy.skydive-max-seconds`, `deploy.remove-elytra-on-land` (SKYDIVE only) |

### Loot & Features

| Button | What it does |
|---|---|
| Add chest | Adds the chest you are looking at |
| Remove nearby chests | Removes the chest points within 3 blocks of you |
| Airdrops | Opens the airdrop window |
| Geysers | Opens the geyser window |
| Loot settings | Opens the loot settings window |

**Loot settings**

| Button | Key |
|---|---|
| Chest mode | `loot.chests.mode` (`all` or `per-player`) |
| Chests per player, Minimum / Maximum chests, Spread over the map | `loot.chests.per-player`, `.min`, `.max`, `.spread` (per-player mode only) |
| Chest refill | `loot.refill-seconds` (0 = never) |
| Min / Max items per chest | `loot.min-items`, `loot.max-items` |
| Chest sound | Opens the chest sound window |

**Chest sound** – players near a match chest that still has loot hear it. On/off (`loot.chest-sound.enabled`), sound radius, *Quiet once opened* (`stop-when-opened`), how often (`interval-ticks`), the **sound** (left/right click cycles through a list of chimes and bells; other sound keys can be written in `game.yml`), volume, pitch, and a **Listen** button to hear it with the current settings.

**Airdrops**

| Button | Key / action |
|---|---|
| Add airdrop / Remove airdrop points | Add a landing point at the block you look at / remove the ones within 3 blocks. Without points a random spot in the zone is used |
| Airdrops enabled | `supply-drop.enabled` |
| Drops per match | `supply-drop.max-drops` (0 = unlimited) |
| First drop after, Time between drops | `supply-drop.first-delay-seconds`, `supply-drop.interval-seconds` |
| Min / Max items per crate | `supply-drop.min-items`, `supply-drop.max-items` |

**Geysers** – add a geyser at the block you look at, remove the ones within 3 blocks, launch power (`geyser.power`, ±0.5), marker height (`geyser.marker-height`, ±5) and geysers on/off (`geyser.enabled`).

### Match rules

| Button | Key |
|---|---|
| Countdown | `game.countdown-seconds` |
| Grace period | `game.grace-period-seconds` |
| Deathmatch duration | `game.deathmatch-timeout-seconds` (0 = no limit) |
| Final collapse | `game.deathmatch-collapse-seconds` |
| Rejoin time | `game.rejoin-grace-seconds` |
| Time of day | `game.world-time` |
| Building | `game.allow-building` |
| Drop loot on death | `game.drop-loot-on-death` |
| Temporary blocks / cobwebs | `game.placed-blocks.decay-seconds`, `game.placed-blocks.cobweb-seconds` (only with Building on) |

### Knockdown & Revive

Only used in DUO/SQUAD: `knockdown.enabled`, bleed-out time (`bleed-seconds`), revive time (`revive-seconds`), revive distance (`revive-radius`) and health after revive (`revive-health`, in half hearts). See [Teams, knockdown & colours](../gameplay/teams.md).

### Gulag

The overview shows what is set and what is missing.

| Button | What it does |
|---|---|
| Duel spot A / B | Left click: set it here. Right click: teleport there. ++shift++ + click: remove it |
| Extra duel rings | Left click adds a ring (1st click = its A, 2nd click = its B), right click removes the ring near you, ++shift++ + click removes all extra rings |
| Waiting points | Left click adds one here, right click removes the ones near you, ++shift++ + click removes them all |
| Rules | Opens the gulag rules |
| Duel kit | Opens the duel kit |
| Back into the match | Opens the return settings |

**Rules** – gulag enabled, minimum players, *Closes after*, countdown, duel duration, pause between duels, fast mode (*Fast mode from*, fast duel duration, fast countdown) and **anti-AFK** (on/off, area size, warning seconds). While you edit the arena, each duel spot's anti-AFK area is drawn with particles.

**Back into the match** – *Return by gliding* (`gulag.redeploy-skydive`), landing spread (`gulag.redeploy-spread`), protection on return (`gulag.respawn-protection-seconds`) and the **Return kit** (with its own on/off switch).

**Kits** (duel kit and return kit) show the current kit. To change one:

1. Equip yourself the way the kit should be: the item in your hand is the weapon, plus armour, off hand and the rest of the inventory as items.
2. Click **Save from my inventory**.

Only item types and amounts are saved (no enchantments or custom names). **Global kit** (++shift++ + click) goes back to the kit in `gulag.yml`. More in [Gulag](../gameplay/gulag.md).

### Dedicated lobby

Optional: click to set this arena's waiting lobby at your position. Without it players wait in the global hub (`/br setlobby`). The lobby's world must be loaded.

---

## The arena file

The GUI writes `arenas/<Name>.yml`. You can also edit it by hand (then run `/br reload`), or start from the two commented examples in `arenas/`: `example-spawns.yml.example` and `example-skydive.yml.example`.

!!! note "Coordinate format"
    - Positions: `x;y;z;yaw;pitch`, e.g. `10.5;64.0;-3.5;90.0;0.0`
    - Block positions (chests, airdrops, geysers): `x;y;z`, e.g. `10;64;-3`

    Coordinates are those of the **template** world: every match plays on a copy of it.

=== "SPAWNS arena"

    ```yaml
    name: ExampleSpawns
    ready: false
    world-template: example_map
    players:
      min: 2        # 0 = game.min-players
      max: 12       # 0 = game.max-players (also limited by the spawns)
    mode: SOLO      # SOLO | DUO | SQUAD
    deploy-mode: SPAWNS
    spawns:
    - 35.5;65.0;0.5;90.0;0.0
    - 30.8;65.0;18.0;120.0;0.0
    # ... one per player
    lobby:
      world: lobby
      pos: 0.5;100.0;0.5;0.0;0.0
    center:
      x: 0.5
      y: 65.0
      z: 0.5
      radius: 120.0
    gulag:
      a: 500.5;65.0;500.5;0.0;0.0
      b: 500.5;65.0;520.5;180.0;0.0
      wait:
      - 480.5;66.0;510.5;-90.0;0.0
    chests:
    - 10;65;10
    - -10;65;-10
    airdrops:
    - 30;65;30
    geysers: []
    settings:
      loot:
        chests:
          per-player: 2
          min: 6
        refill-seconds: 180
      zone:
        phases:
        - wait-seconds: 60
          shrink-seconds: 45
          target-radius: 80
        - wait-seconds: 45
          shrink-seconds: 40
          target-radius: 45
        - wait-seconds: 30
          shrink-seconds: 30
          target-radius: 20
        - wait-seconds: 20
          shrink-seconds: 25
          target-radius: 0
    ```

=== "SKYDIVE arena"

    ```yaml
    name: ExampleSkydive
    ready: false
    world-template: example_map
    players:
      min: 8
      max: 40
    mode: SQUAD
    deploy-mode: SKYDIVE
    skydive:            # one point, or a list of points
    - 0.5;250.0;0.5;0.0;45.0
    - 150.5;250.0;150.5;135.0;45.0
    - -150.5;250.0;-150.5;-45.0;45.0
    spawns: []          # not needed in SKYDIVE mode
    center:
      x: 0.5
      y: 70.0
      z: 0.5
      radius: 250.0
    gulag:
      a: 500.5;65.0;500.5;0.0;0.0
      b: 500.5;65.0;520.5;180.0;0.0
      wait:
      - 480.5;66.0;510.5;-90.0;0.0
      pairs:            # extra rings: "A|B"
      - 540.5;65.0;500.5;0.0;0.0|540.5;65.0;520.5;180.0;0.0
    chests:
    - 60;70;60
    airdrops:
    - 80;70;80
    geysers:
    - 0;70;100
    settings:
      game:
        countdown-seconds: 60
      geyser:
        power: 4.0
        marker-height: 40
      zone:
        move:
          enabled: true
          from-phase: 3
          max-drift: 0.6
      supply-drop:
        max-drops: 4
    ```

### Keys

| Key | Meaning |
|---|---|
| `name` | Informative. The arena's real name is always the **file name** (`Forest.yml` → `Forest`): letters, numbers, `_` and `-` |
| `ready` | Written on save: whether the arena is playable. Informative only |
| `world-template` | The world **folder** copied for every match (not a Multiverse alias) |
| `players.min` / `players.max` | `0` = use `game.min-players` / `game.max-players` |
| `mode` | `SOLO`, `DUO` or `SQUAD` |
| `deploy-mode` | `SPAWNS` or `SKYDIVE` |
| `spawns` | Ground spawns (SPAWNS mode): one per player |
| `skydive` | Launch point(s) (SKYDIVE mode). Optional: without it players drop above the centre at `deploy.skydive-default-height` |
| `lobby.world` / `lobby.pos` | Optional dedicated waiting lobby |
| `center.x/y/z`, `center.radius` | Zone centre and starting half-width in blocks. Every phase `target-radius` must be smaller than `radius` |
| `gulag.a` / `gulag.b` | Main duel spots. Without both, the gulag is off for this arena |
| `gulag.pairs` | Extra rings for parallel duels, `positionA|positionB` |
| `gulag.wait` | Where queued players wait |
| `chests` | Chest points (how many are used: `loot.chests`) |
| `airdrops` | Airdrop landing points (none = random spot in the zone) |
| `geysers` | Geyser blocks |
| `settings` | Per-arena overrides, same paths as the global files |

A gulag kit set from the GUI is saved under `settings.gulag.kit` (duel) or `settings.gulag.respawn-kit` (return), in the same format as `gulag.yml`. Reward commands for one arena go under `settings.rewards`:

```yaml
settings:
  rewards:
    winner-commands:
    - 'eco give %player% 500'
```
