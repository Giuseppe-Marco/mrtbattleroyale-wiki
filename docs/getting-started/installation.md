# Installation

This page gets MRTBattleRoyale running on your server: what you need, which files appear on the first start, how to update safely and how to pick a language and a database.

## Requirements

| What | Version |
|---|---|
| Server software | **Paper 1.21.4 – 1.21.11** or **Paper 26.1 / 26.2** |
| Java | **21** or newer |

The same jar works on both generations. On 26.x the plugin detects the new world layout (worlds stored under `dimensions/`) and handles it for you.

### Optional plugins

None of these are required. Install them only if you want what they add.

| Plugin | What it adds |
|---|---|
| **PlaceholderAPI** | `%brbattle_*%` placeholders for scoreboards, tab lists, holograms... See [Placeholders](../reference/placeholders.md). |
| **ProtocolLib** | Teammates glow through walls, visible **only to their own team** (DUO/SQUAD). Without it the teammate glow is off, because a normal glow would reveal teams to enemies too. |
| **Citizens** | Clickable arena NPCs with `/br npc`. See [Hub, NPCs & signs](../features/hub.md). |
| **Multiverse-Core** | Handy for loading your map and lobby worlds. MRTBattleRoyale loads after it, so the arena maps already exist when the plugin starts. |

!!! tip "TAB plugin"
    The plugin draws its own sidebar scoreboard but does not format the tab list. If you use the **TAB** plugin for that, it works fine with the `%brbattle_*%` placeholders. See [Scoreboard](../features/scoreboard.md) and the [FAQ](../faq.md).

## Installing

1. Stop the server.
2. Put `MRTBattleRoyale-2.1.1.jar` in the `plugins/` folder.
3. Start the server. The plugin creates its folder `plugins/MRTBattleRoyale/` with all the default files.
4. Pick your language in `config.yml` (see [Language](#language) below).
5. Stand where your hub should be and run `/br setlobby`.
6. Create your first arena: follow [Your first arena](first-arena.md).

!!! warning "Set the hub with `/br setlobby`"
    Until you run `/br setlobby`, the hub items and the hub protection are **not active anywhere**: teleports to the hub fall back to the main world's spawn. Don't type a world into `lobby.yml` by hand unless it really is your hub, otherwise everyone entering that world gets the hub items and becomes invulnerable.

When an admin joins and no arena exists yet, the plugin offers the step-by-step setup guide in chat (you can turn this off with `setup-guide.announce-on-join` in `config.yml`).

## Files created on the first start

Everything lives in `plugins/MRTBattleRoyale/`:

```text
plugins/MRTBattleRoyale/
├── config.yml          core: language, chat, scoreboard, commands, statistics/database...
├── game.yml            match rules: zone, loot, knockdown, deploy, airdrops, sounds...
├── gulag.yml           gulag rules and kits
├── lobby.yml           main hub and hub items
├── economy.yml         XP, coins, reward commands
├── loot.yml            loot tables (rarity tiers)
├── events.yml          Battle Royale events
├── languages/
│   ├── messages_en.yml
│   └── messages_it.yml
├── arenas/
│   ├── example-spawns.yml.example
│   └── example-skydive.yml.example
└── stats.db            player statistics (SQLite, created when the database starts)
```

The two `.yml.example` files in `arenas/` are fully commented sample arenas. They end in `.example`, so they are **never loaded**: they are only there as documentation. Your own arenas are written in the same folder as `<Name>.yml` by the setup GUI.

Each file is described in [Configuration files](configuration.md).

## Updating

!!! danger "Never hot-reload the plugin"
    Do **not** update with `/reload`, PlugManX or similar plugin managers. Matches copy worlds, hold players and open database connections: always do a full restart.

1. **Stop** the server.
2. Optionally make a backup of `plugins/MRTBattleRoyale/`.
3. **Replace** the old jar in `plugins/` with the new one (delete the old file, don't keep both).
4. **Start** the server.

On start the plugin adds any **new keys** to your configuration and language files, without touching the values you changed. If you come from an older version that used a single big `config.yml`, its sections are moved into the new files automatically.

!!! tip "Restart vs `/br reload`"
    `/br reload` is for applying changes you made to the configuration files while the server runs. It is **not** a way to load a new jar.

## Language

All texts (chat, GUIs, scoreboard, titles) live in `languages/messages_<code>.yml`, written in MiniMessage. Choose the file with `language` in `config.yml`:

```yaml
# Language of the plugin's texts. Available values: en, it.
language: en
```

- Bundled languages: `en` (English) and `it` (Italian).
- Apply the change with `/br reload`.
- Keys missing from the selected file fall back to English.
- **Your own translation:** create `languages/messages_<code>.yml` (for example by copying `messages_en.yml`), translate it and set `language: <code>`. An unknown code falls back to English with a warning in the console.

## Storage: SQLite or MySQL

Statistics, levels, XP, coins and cosmetics are stored in a database, configured in the `stats` section of `config.yml`:

```yaml
stats:
  enabled: true
  # SQLITE (local stats.db file, no configuration) or MYSQL.
  storage: SQLITE
  leaderboard-refresh-seconds: 60
  # Only used if storage: MYSQL.
  mysql:
    host: localhost
    port: 3306
    database: battleroyale
    user: root
    password: ''
```

=== "SQLite (default)"

    Nothing to do: the data is saved in `plugins/MRTBattleRoyale/stats.db`. Perfect for a single server.

=== "MySQL"

    1. Create an empty database (for example `battleroyale`) and a user that can use it.
    2. Set `storage: MYSQL` and fill in `host`, `port`, `database`, `user` and `password`.
    3. **Restart** the server.

    The plugin uses the MySQL driver that ships with Paper. If the connection fails, the console shows `Could not initialise stats storage: ...` and statistics stay disabled until you fix it.

!!! note
    The storage is opened when the server starts: after changing `storage` or the MySQL credentials, restart the server instead of using `/br reload`.

Statistics are global, never per arena. More in [Stats, levels & shop](../features/progression.md).
