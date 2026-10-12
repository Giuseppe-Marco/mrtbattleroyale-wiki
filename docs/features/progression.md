# Stats, levels & shop

Every match counts. Players collect **statistics**, earn **XP** to level up and **coins** to spend in the **cosmetics shop**, and compete on the **leaderboards**.

## Statistics

The plugin tracks, for every player:

| Stat | Notes |
|---|---|
| Games | matches played |
| Wins | your own win, or your team's |
| Kills | lifetime kills |
| Deaths | matches you did not win |
| Gulag wins | duels won in the [gulag](../gameplay/gulag.md) |
| Win streak | wins in a row (reset by a match not won) |
| Best placement | your best finishing position |
| Damage dealt | total damage dealt |
| XP, level, coins | see below |

From these the plugin also shows the **K/D** (kills / deaths) and the **win rate** (wins / games, in %).

Stats are recorded at the end of every real match. A match that is aborted (stopped by an admin, or with no real contest) records nothing, and [event](../events.md) matches never touch these stats.

### Seeing your stats

| How | What you get |
|---|---|
| `/br stats` | your stats in chat (level, coins, wins, kills, deaths, games, K/D) |
| `/br stats <player>` | someone else's stats (needs `br.stats.others`, given to everyone by default) |
| Book in the hub hotbar | a stats screen with wins and streak, games and win rate, kills and K/D, deaths, damage, gulag wins, best placement, coins and level |

## XP and levels

You earn XP at the end of each match. The level comes from your total XP: every `xp-per-level` XP (default **500**) is one level, starting from level 1. Players get a message when they level up.

## Coins

Coins are the plugin's own currency, earned at the end of each match. They are spent in the [cosmetics shop](#cosmetics-shop) and on event [entry fees](../events.md#registration).

Admins can manage coins and XP (needs `br.admin.economy`):

```
/br coins <give|take|set> <player> <amount>
/br xp <give|take|set> <player> <amount>
```

## Rewards: economy.yml

```yaml
progression:
  # XP needed to level up.
  xp-per-level: 500

  xp:
    # Just for playing the match.
    play: 5
    # Bonus for winning (your own win or your team's).
    win: 100
    # For each kill.
    per-kill: 10

  coins:
    play: 0
    win: 50
    per-kill: 5

# ---- Reward commands ----
# Run from the console. Use %player% (or <player>) in place of the name.
rewards:
  # At the end of the match, once per winner.
  winner-commands: []
  # For each kill (%player% = the killer).
  kill-commands: []
```

So with the defaults, a player who wins with 3 kills earns `5 + 100 + 3×10 = 135` XP and `0 + 50 + 3×5 = 65` coins.

### Reward commands

`winner-commands` and `kill-commands` run console commands, so you can hook up **any** other plugin (economy, ranks, kits, crates) without a dedicated integration:

```yaml
rewards:
  winner-commands:
    - 'eco give %player% 100'
  kill-commands:
    - 'lp user %player% permission settemp rank.vip true 1d'
```

!!! note "Global vs per arena"
    The `progression` section (XP and coins) is **global**: it can't be changed per arena. The reward commands **can** be overridden per arena, in the arena's `settings:` section. See [Per-arena settings](../getting-started/arena-settings.md).

## Cosmetics shop

Players open the shop with `/br shop` or the **ender chest** in the hub hotbar (needs `br.shop`, given to everyone by default). There are three categories, and one cosmetic per category can be active at a time:

=== "Glider trails"

    Particles behind you while you skydive into the map.

    | Cosmetic | Price |
    |---|---|
    | Cloud Trail | 80 |
    | Flame Trail | 100 |
    | Heart Trail | 150 |
    | Soul Trail | 250 |

=== "Kill effects"

    Played where your victim falls.

    | Cosmetic | Price |
    |---|---|
    | Critical Hit | 100 |
    | Totem Burst | 200 |
    | Lightning | 300 |

=== "Victory effects"

    Played when you win.

    | Cosmetic | Price |
    |---|---|
    | Party | 150 |
    | Inferno | 200 |
    | Totem Rain | 250 |

How it works:

- Click a cosmetic you don't own to **buy** it: the coins are taken and it is selected right away.
- Click one you already own to **select** it.
- If you don't have enough coins, the shop tells you the price.

The list of cosmetics and their prices are built into the plugin. Their **names** can be changed in the messages file (`cosmetic.names` and `cosmetic.types` sections). Cosmetics are saved with the player's stats, so the shop needs statistics enabled.

## Leaderboards

```
/br top [category]
```

shows the top 10 of a category (default `wins`). Categories:

| Category | Ranks by |
|---|---|
| `wins` | wins |
| `kills` | kills |
| `deaths` | deaths |
| `games` | matches played |
| `xp` (or `level`) | total XP |
| `coins` | coins |
| `gulag_wins` (or `gulag`) | gulag wins |
| `winstreak` | current win streak |
| `damage_dealt` (or `damage`) | damage dealt |

An unknown category falls back to `wins`.

For holograms and the tab list, the same leaderboards are available as placeholders: `%brbattle_top_<category>_<n>_name%` and `%brbattle_top_<category>_<n>_value%`, for positions 1 to 10. They are refreshed every `stats.leaderboard-refresh-seconds` (default 60). See [Placeholders](../reference/placeholders.md).

## Storage: SQLite or MySQL

Stats, levels, coins and cosmetics are stored in a database, set in `config.yml`:

```yaml
stats:
  enabled: true
  # SQLITE (local stats.db file, no configuration) or MYSQL.
  storage: SQLITE
  # How often (seconds) the leaderboards behind the
  # top_<category>_<n>_name / _value placeholders (holograms, tab) are refreshed. Minimum 5.
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

    Zero configuration: everything goes into `plugins/MRTBattleRoyale/stats.db`. Best for a single server.

=== "MySQL"

    Set `storage: MYSQL` and fill in the `mysql` section. Use it to share stats between several servers or to read them from a website. The connection is checked and reopened automatically if the MySQL server drops it.

Data is global, not per arena. With `stats.enabled: false` nothing is saved: `/br stats` and `/br top` say that statistics are disabled, and purchases in the shop are not possible.

## Resetting stats (/br resetstats)

`/br resetstats` resets the statistics of **every player**, for example at the start of a new season. It needs `br.admin.resetstats`, which is **not** part of `br.admin` on purpose: only `br.*` (or the node given by hand) has it.

| Command | What is reset |
|---|---|
| `/br resetstats stats` | wins, kills, deaths, games, gulag wins, win streak, best placement and damage. **Level, XP, coins and cosmetics are kept.** |
| `/br resetstats all` | everything: the whole profile of every player is deleted (stats, level, XP, coins, cosmetics). |

It takes two steps:

1. `/br resetstats <stats|all>` shows what will be deleted and a random **code**.
2. Within **60 seconds**, type `/br resetstats <stats|all> confirm <code>` (the line in chat is clickable).

!!! success "Automatic backup"
    Before touching anything, the plugin makes a **backup**: a copy of the database in `plugins/MRTBattleRoyale/backups/` (SQLite) or a `br_stats_backup_<date>` table (MySQL). If the backup can't be made, nothing is reset. The reset runs in one transaction, and if it fails, online players get their values back.

The reset is refused while a match is in progress.
