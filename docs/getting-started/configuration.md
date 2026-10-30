# Configuration files

All files live in `plugins/MRTBattleRoyale/`. Each one covers a single topic, and every file is fully commented, so this page is a map: what each file controls and the keys you are most likely to change. The excerpts below are the real defaults.

| File | Controls | Per arena? |
|---|---|---|
| [`config.yml`](#configyml) | Language, scoreboard, chat, blocked commands, texts, statistics/database, party, updates | No |
| [`game.yml`](#gameyml) | Match rules: players, zone, loot, knockdown, geysers, deploy, spectators, visuals, effects, airdrops, sounds | Most sections |
| [`gulag.yml`](#gulagyml) | The gulag (1v1 respawn duel) and its kits | Yes |
| [`lobby.yml`](#lobbyyml) | Main hub and hub items | No |
| [`economy.yml`](#economyyml) | XP, coins, reward commands | Reward commands only |
| [`loot.yml`](#lootyml) | Loot tables (rarity tiers) | Yes, by table |
| [`events.yml`](#eventsyml) | Battle Royale events | Per event |
| `languages/messages_*.yml` | Every text, in MiniMessage | No |
| `arenas/*.yml` | One file per arena, written by the setup GUI | – |

!!! tip "Global vs per arena"
    The values in these files are the **global defaults**. Any arena can override the sections `game`, `zone`, `loot`, `knockdown`, `geyser`, `deploy`, `gulag`, `effects`, `supply-drop`, `sounds` and `rewards` for itself only, from the setup GUI or in its file. See [Per-arena settings](arena-settings.md).

## Applying changes: `/br reload`

After editing a file by hand, run:

```text
/br reload
```

It reloads the configuration, the language files, the loot tables, the events and the arena files, then rebuilds the arenas. It needs the `br.admin.reload` permission.

!!! warning "What needs a restart"
    - Changing the database (`stats.storage` or the MySQL credentials): restart the server.
    - A new plugin version: always stop the server, replace the jar and start it again. Never use `/reload` or PlugManX. See [Installation](installation.md#updating).

New keys added by an update are written into your files automatically on start, keeping your values.

---

## config.yml

The plugin's "system" settings.

```yaml
# Language of the plugin's texts. Available values: en, it.
language: en

# Logs the whole internal flow to the console (useful for testing). Keep false in production.
debug: false
```

| Section | What it does |
|---|---|
| `language` | Which `languages/messages_<code>.yml` is used. See [Installation](installation.md#language). |
| `scoreboard` | The plugin's own sidebar, a different board for each situation (hub, waiting, countdown, match, gulag, spectator, ending, event). Turn single boards on/off, set `server-address`. See [Scoreboard](../features/scoreboard.md). |
| `arena-menu` | `by-mode: true` shows Solo / Duo / Squad first, then the arenas of that mode. |
| `tab` | `per-game-visibility`: each player only sees the players of their own match. |
| `chat` | Battle Royale chats (hub, each match, spectators), team chat, proximity chat, moderation. See [Chat](../features/chat.md). |
| `commands` | Blocks other commands during a match (anti-escape); `allowed` lists the exceptions. |
| `messages` | `smallcaps` for all texts, and `command-label` (the command shown in texts). |
| `stats` | Statistics on/off and the database (SQLite or MySQL). |
| `party` | Invite expiry and cooldown. |
| `update-checker`, `metrics` | Modrinth update check and anonymous bStats statistics. |
| `setup` | `chest-scan`: the containers `/br setup scanchests` registers as loot chests (`types`, default `CHEST`, `TRAPPED_CHEST`, `BARREL`) and how many chunks it loads at once (`chunks-at-once`, default 16; also used to draw the [map wall](../gameplay/map-wall.md)). See [Your first arena](first-arena.md). |
| `setup-guide` | Offer the setup guide to admins when no arena exists. |

```yaml
commands:
  # Blocks commands during the match.
  block-in-game: true
  # Commands still allowed to players in a match.
  # (/br and /battleroyale are always allowed.)
  allowed: []

messages:
  smallcaps: true
  #   auto         = /br, or /battleroyale when another plugin owns /br
  #   battleroyale = always /battleroyale
  #   br           = always /br
  command-label: auto
```

Admins with `br.admin.bypass` are never blocked, and `/br` itself can never be blocked.

```yaml
setup:
  chest-scan:
    types: [CHEST, TRAPPED_CHEST, BARREL]
    chunks-at-once: 16
```

---

## game.yml

The actual match rules. Every section except `spectator` and `team` can be overridden per arena.

### `game` – general rules

```yaml
game:
  min-players: 2
  max-players: 10
  countdown-seconds: 30
  grace-period-seconds: 10
  # Default mode when an arena is created: SOLO, DUO or SQUAD.
  team-mode: SOLO
  duo-size: 2
  squad-size: 4
  auto-start: true
  end-delay-seconds: 10
  deathmatch-timeout-seconds: 60
  deathmatch-collapse-seconds: 20
  allow-building: false
  drop-loot-on-death: true
  combat-tag-seconds: 15
  world-time: 6000
  block-teleport-items: false
  rejoin-grace-seconds: 60
  force-join-seconds: 60
  quick-join: true
  end-summary: true
```

- `min-players` / `max-players` are used by arenas that don't set their own (`players.min` / `players.max` at 0).
- `deathmatch-timeout-seconds`: after the last zone phase, how long the final showdown lasts before the zone collapses completely (0 = no limit).
- `allow-building` with `placed-blocks` (not shown): placed blocks and cobwebs disappear after a while.
- `rejoin-grace-seconds`: how long a disconnected player can come back without being eliminated.
- `force-join-seconds`: for how long after the deploy `/br forcejoin` puts a player into the running match as a **player** (own team in SOLO, a team with a free place in DUO/SQUAD). Later, or with `0`, they join as a spectator.
- `quick-join`: `/br join` without an arena joins the fullest available match.
- `end-summary`: personal recap in chat at the end of the match.

### `zone` – the shrinking border

```yaml
zone:
  damage-per-second: 2.0
  move:
    enabled: false
    from-phase: 3
    max-drift: 0.6
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

Each phase waits, then shrinks the border to `target-radius` (half-width in blocks) in `shrink-seconds`. Every `target-radius` must be smaller than the previous one; `0` closes the zone completely. The default phases start at 120, so they fit an arena whose radius is larger than that: give bigger or smaller maps their own phases. Full details in [Zone](../gameplay/zone.md).

### `loot` – chests

```yaml
loot:
  chests:
    mode: per-player     # all | per-player
    per-player: 3
    min: 10
    max: 0
    spread: true
  refill-seconds: 0
  min-items: 3
  max-items: 6
  chest-sound:
    enabled: true
    radius: 1.5
    stop-when-opened: false
    sound: block.amethyst_block.chime
    volume: 0.8
    pitch: 1.2
    interval-ticks: 20
```

How many of the arena's chest points are used each match, refills, items per chest and the "glowing chest" sound. What's **inside** the chests is in [`loot.yml`](#lootyml). See [Loot, chests & airdrops](../gameplay/loot.md).

### Other sections

| Section | What it does |
|---|---|
| `knockdown` | DUO/SQUAD only: downed instead of eliminated, bleed-out and revive times, revive radius and health, who sees the downed glow. See [Teams, knockdown & colours](../gameplay/teams.md). |
| `geyser` | Stepping on a geyser launches you into the air to glide: `power`, `glide-delay-ticks`, particle `marker` and `marker-height`. |
| `deploy` | Skydive: `skydive-default-height` (Y used when an arena has no launch point), `remove-elytra-on-land`, `skydive-max-seconds`. |
| `map-wall` | [Map wall](../gameplay/map-wall.md), spawn picker of SKYDIVE arenas: `lock-seconds` (5, last seconds of the countdown in which the choice is locked) and `click-distance` (32 blocks). |
| `spectator` | Follow camera for eliminated players: `follow`, `auto-follow`, `hud`. See [Spectators](../gameplay/spectators.md). |
| `team.visuals` | Name colours, teammate glow (needs ProtocolLib), `nametags` (`hidden` / `enemies` / `visible`), SOLO markers. |
| `effects` | Victory fireworks, zone particle wall and its colour, closed-zone ring, firework colours. |
| `supply-drop` | Airdrops: first delay, interval, max drops, items per crate, crate title, landing fireworks. |
| `sounds` | `master-volume` and a volume per event (`0` mutes one); `enabled: false` mutes them all. |

```yaml
supply-drop:
  enabled: true
  first-delay-seconds: 60
  interval-seconds: 60
  max-drops: 3
  min-items: 6
  max-items: 9
```

---

## gulag.yml

A killed player gets a second chance in a 1v1 duel; the winner rejoins the match. The gulag **positions** are not here: they are saved in each arena file (set them from the setup GUI).

```yaml
gulag:
  enabled: true
  min-players: 4
  close-seconds: 240
  countdown-seconds: 3
  duel-seconds: 60
  start-delay-seconds: 2
  fast-queue-threshold: 2
  fast-duel-seconds: 25
  fast-countdown-seconds: 1
  anti-afk:
    enabled: true
    area: 3
    warn-seconds: 10
  redeploy-skydive: true
  redeploy-spread: 20.0
  respawn-protection-seconds: 3
```

- `min-players`: players still in play needed to keep the gulag open (at least 4 makes sense).
- `close-seconds`: after this many seconds of match, nobody new enters the gulag.
- Fast mode: with a long queue (`fast-queue-threshold`), duels get shorter.
- `anti-afk`: a fighter who never leaves the `area` around their spawn loses when the timer ends.
- `kit` and `respawn-kit` (not shown): what both fighters get in the duel, and what the winner gets to go back in. Materials are Minecraft item names, `NONE` leaves a slot empty.

More in [Gulag](../gameplay/gulag.md).

---

## lobby.yml

The main hub. Set its position in game with `/br setlobby` (not to be confused with `/br setup lobby`, which sets an arena's own waiting lobby).

```yaml
lobby:
  world: ''
  spawn-on-join: false
  protect: true
  void-level: -100
  hub-items: true
  clear-inventory-in-hub: false
```

- `protect`, `void-level` and `hub-items` work **only** in the hub world. With no hub set, they do nothing.
- `items` and `compass` set the hotbar slots of the hub items; the `items:` section at the bottom sets their materials. Their names are in the language file.

See [Hub, NPCs & signs](../features/hub.md).

---

## economy.yml

```yaml
progression:
  xp-per-level: 500
  xp:
    play: 5
    win: 100
    per-kill: 10
  coins:
    play: 0
    win: 50
    per-kill: 5

rewards:
  winner-commands: []
  kill-commands: []
```

- `progression` is **always global** (it can't be overridden per arena).
- `rewards` are console commands with `%player%` (or `<player>`), for example `'eco give %player% 100'`: the way to hook up any economy, rank or kit plugin. They **can** be overridden per arena.

See [Stats, levels & shop](../features/progression.md).

---

## loot.yml

Rarity tiers (`common`, `rare`, `epic`, `legendary`) with weighted items. For each stack in a chest a tier is picked by its `weight`, then an item inside that tier by its own `weight`.

```yaml
tiers:
  common:
    weight: 55
    items:
      - { material: STONE_SWORD, weight: 10 }
      - { material: COOKED_BEEF, min: 2, max: 5, weight: 16 }
  epic:
    weight: 12
    items:
      - { material: DIAMOND_SWORD, weight: 8, enchantments: { sharpness: 1 } }
      - { material: POTION, potion: speed, duration: 45, weight: 4, name: '<aqua>Sprint Potion' }
```

Items support `min`/`max`, `enchantments`, `name`, `lore` and potion effects. Named `tables` can be assigned to arenas in the `arenas:` section (`inherit: true` starts from the default tiers). Chests and airdrops of an arena both use its table. Full guide: [Loot, chests & airdrops](../gameplay/loot.md).

---

## events.yml

Battle Royale events: sessions of several matches for registered players, with a map rotation, points, prizes and an optional schedule. Create one in game with `/br event create <id>`. See [Events](../events.md).

---

## Language files

`languages/messages_en.yml` and `languages/messages_it.yml` hold every text: chat messages, GUI names and lore, titles, scoreboard boards. They use [MiniMessage](https://docs.advntr.dev/minimessage/format.html) (`<red>`, `<gradient:...>`, `<#ff5f6d>`). Edit them freely and run `/br reload`.
