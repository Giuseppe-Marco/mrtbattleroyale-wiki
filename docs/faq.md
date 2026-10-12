# FAQ & troubleshooting

!!! tip "Still stuck?"
    Run `/br debug report` and send the generated file (`plugins/MRTBattleRoyale/reports/`) to whoever helps you: it contains versions, plugins, arenas and configs, with database passwords hidden. `/br debug` turns on detailed console logging until restart. See [Admin tools](reference/admin-tools.md#debug).

## Installation and updates

### I replaced the jar and now get `NoClassDefFoundError` / strange errors

You replaced the jar **while the server was running** (or used `/reload` or a plugin manager to reload it). Java keeps reading classes from the old jar, which is gone, so classes fail to load.

**Always do a full restart** after updating the plugin: stop the server, replace the jar, start the server. Never use `/reload`, PlugMan or similar. `/br reload` only reloads configuration files, messages and arenas; it does not load a new jar.

### The console shows an update check error / Modrinth 404

The update checker never prints errors: problems (offline server, firewall) are only logged with `/br debug` on. If Modrinth answers "not found" (404), the checker stops until the next restart; nothing else is affected. To turn the check off: `update-checker.enabled: false` in `config.yml`. See [Admin tools](reference/admin-tools.md#update-checker).

### Do I need to download a PlaceholderAPI expansion?

No. The `brbattle` expansion is built in. Just install PlaceholderAPI and restart: the console shows `Hooked into PlaceholderAPI.`

## Placeholders, TAB and scoreboard

### `%brbattle_...%` placeholders show as plain text

- **PlaceholderAPI is not installed** (or failed to load). Without it no `%brbattle_...%` placeholder exists. Install it and restart.
- Check the spelling against [Placeholders](reference/placeholders.md): the identifier is `brbattle`, e.g. `%brbattle_wins%`.
- The plugin reading the placeholder must support PlaceholderAPI (TAB, holograms, scoreboards...).
- Test with `/papi parse me %brbattle_wins%`.

### TAB: name colours or hidden name tags don't work in matches

TAB's `scoreboard-teams` feature takes over the scoreboard teams the plugin uses for colours and name tags (`plugins/TAB/anti-override.log` shows *"Blocked attempt to add player ... into team br..."*). Tell TAB not to manage players while they are in a match, in `plugins/TAB/config.yml`:

```yaml
scoreboard-teams:
  enabled: true
  disable-condition: '%world%=disabledworld|%brbattle_in_match%=1'
```

Then `/tab reload`. For tab list colours (`tablist-name-formatting`, SOLO hex colours) see [Placeholders - Using the TAB plugin](reference/placeholders.md#using-the-tab-plugin).

### Two sidebars fight each other / the scoreboard flickers

Two plugins are drawing a sidebar. Either disable TAB's scoreboard, or set `scoreboard.enabled: false` in `config.yml` and build the boards in TAB with `%brbattle_board%`. See [Scoreboard](features/scoreboard.md).

### Teammates don't glow

The teammate glow (visible only to the own team) needs **ProtocolLib**. Without it the glow is disabled on purpose, because a normal glow would show teams to enemies; the console says `ProtocolLib not found — teammate glow DISABLED`. Install ProtocolLib and restart. The glow is also controlled by `team.visuals.glow` in `game.yml` and only applies in DUO/SQUAD. See [Teams, knockdown & colours](gameplay/teams.md).

## Matches and worlds

### The match doesn't start / the map isn't copied

- The arena must be playable: run `/br setup info` (zone center and radius, enough spawns in SPAWNS mode, minimum players not above the maximum).
- `world-template` must be the world's **folder name** in the server directory, not a Multiverse alias. Fix it with `/br setup world <folder>`.
- A match copy (`br_<arena>_<n>`) can't be used as a template: set up the arena in the real map world.
- Look in the console for `Copy failed` or `Loading world ... failed` warnings. On Windows a world copy can be briefly locked: the plugin retries deleting old copies on its own.
- With Multiverse-Core, the plugin loads after it, so your map worlds exist at startup.

### Chunks are missing or old in the match copy

Each match plays on a fresh copy of the template world. The plugin saves a loaded template before copying it and waits for the save to finish, but chunks changed **while** the copy runs can come out broken (console: *"Region file ... kept changing while copied"*).

- Don't edit the template world while a match is starting.
- After building in the map, run `save-all` (or wait for the autosave) before starting a match.
- Make sure the whole play area has been generated/saved in the template, not just loaded around you.

### The skydive drop point is in the wrong place

Launch points must be set **in the map world** itself, flying high above the map (`/br setup skydive`). The plugin warns you if you set a point in another world.

### Players outside the zone can't open chests

This is vanilla Minecraft: the zone is shown to each player as a **world border**, and the game does not let you interact with blocks beyond the world border. Players must get back into the zone. Out-of-zone damage is applied by the plugin.

### Players can't build in the gulag

By design. Building is blocked for players in the gulag (the duel is fought with the kit, and the ring must stay as it is), and **nobody** can break or place blocks inside the gulag area, even from the map. Explosions and fire can't damage it either. Elsewhere, building follows the map rule `game.allow-building` (off by default; can be set per arena), admins included. See [Gulag](gameplay/gulag.md).

### Players can't use other plugins' commands during a match

While a player is in a game (queue or match), every command except `/br` is blocked so nobody can teleport or escape. Allow specific commands in `config.yml`:

```yaml
commands:
  block-in-game: true
  # Commands still allowed to players in a match.
  allowed:
    - msg
    - r
```

Write the command names without arguments (`/` optional). `block-in-game: false` turns the block off. Staff with `br.admin.bypass` are never blocked. Private-message commands (`/msg`, `/tell`...) are blocked too unless you add them.

### Players in different arenas can't see each other in the hub

`tab.per-game-visibility` (`config.yml`) shows each player only the players of their own match. If several arenas share the global lobby, give each arena its own lobby (`/br setup lobby`) or turn the option off.

### Hub items or hub protection are active in the wrong world (or nowhere)

They only work in the world set with `/br setlobby`. Until you run it, hub items and protection are not active anywhere. Don't type a world into `lobby.yml` by hand unless it really is the hub. Staff can build in the hub with `/br admin` (build mode). See [Hub, NPCs & signs](features/hub.md).

## Commands and permissions

### Where are `/join`, `/leave`, `/party`?

There are none: every command is a sub-command of `/br` (alias `/battleroyale`), e.g. `/br join`, `/br leave`, `/br party`. This avoids clashes with other plugins. See [Commands](reference/commands.md).

### A player gets "no permission"

Check the permission in [Permissions](reference/permissions.md). Player permissions are granted to everyone through `br.player`, unless your permission plugin negates them. `/br resetstats` needs `br.admin.resetstats`, which is **not** included in `br.admin`.

### `/br npc` says Citizens is missing

NPC commands need the Citizens plugin. Without it, use `[br]` join signs or the arena menu instead.

## Statistics

### I reset the statistics by mistake

Every `/br resetstats` takes a backup first: `plugins/MRTBattleRoyale/backups/stats-<date>.db` (SQLite) or a `br_stats_backup_<date>` table (MySQL). See [Restoring a backup](reference/admin-tools.md#restoring-a-backup).

### Leaderboard placeholders update slowly

`%brbattle_top_...%` placeholders are served from a cache refreshed every `stats.leaderboard-refresh-seconds` (default 60) so the database is never queried on the main thread. Lower the value if needed (minimum 5).
