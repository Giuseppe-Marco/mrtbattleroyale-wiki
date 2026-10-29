# Admin tools

Tools for staff: managing a live match, diagnosing problems, resetting statistics, and the update checker and metrics settings. For the full command list see [Commands](commands.md).

## Match tools: `/br admin ...`

All need **`br.admin.match`** and, except `spectate`, also work from the console.

!!! info "Which match?"
    When `[arena]` is omitted, the tool acts on the match you are in or are watching with `/br admin spectate`; otherwise the only arena; otherwise the only active one (match running, countdown, or players queued). If several are active, the plugin asks you to name the arena and lists them.

### Watching

| Command | What it does |
|---|---|
| `/br admin info [arena]` | Live status. In the lobby: players, min/max and the countdown. In a match: players alive / total / still in contention, teams alive, zone phase and step (waiting, shrinking, collapsing, paused), zone radius, gulag status (open/closed, forced, queue, duels running), airdrops dropped, chests, elapsed time. |
| `/br admin spectate [arena]` | Watch a running match invisibly in spectator mode. You are **not** a participant: no counts, win checks, zone damage or placeholders see you. Run it again, or `/br leave`, to stop: your inventory and position are restored. Not possible while you are in a game yourself. |

### Players

| Command | What it does |
|---|---|
| `/br admin revive <player>` | An eliminated (spectating) or gulag-queued player goes back on the map exactly like a gulag winner returning (respawn kit, redeploy, zone border, spawn protection). A knocked-down player is simply revived. Their placement is cleared; their one gulag chance stays used if it was. The player must be online. |
| `/br admin eliminate <player> [nostats]` | Takes an alive, knocked-down or gulag player out of the match at once: no gulag, no kill credit. Also works on a player who disconnected and is still in the rejoin window. With `nostats` the match is not written to their statistics (no game, no loss). |
| `/br admin end <player\|team number> [arena]` | Declares the winner. A player name picks that player's team (any mode); a number picks a team (DUO/SQUAD, 1-based, as in the team selector). Everyone else still in contention is eliminated without kill credit, then the match ends normally: rewards, stats, winner commands, fireworks. In SOLO use a name, not a number. |
| `/br admin team <player> <number>` | Moves a **queued** player to team `number` (DUO/SQUAD, before the start). Stored as their team choice, so the team assignment honours it. Refused if the team is full or the number is invalid. |
| `/br admin broadcast <arena\|all> <message>` | A staff message (`[Staff] ...`, with a sound) to every player of one match — lobby, players and spectators — or of **all** matches. MiniMessage colours work. Alias: `bc`. Format: `admin.broadcast-format` in the language file. |

### Zone

| Command | What it does |
|---|---|
| `/br admin zone pause [arena]` | Freezes the zone: the timer stops and the border holds still; the boss bar shows that it is paused. Damage outside the zone continues. |
| `/br admin zone resume [arena]` | Unfreezes it (a shrink continues over the time it had left). |
| `/br admin skipzone [arena]` | Skips the wait: the next closing starts now. In the deathmatch (all phases done) it starts the final collapse at once. Refused while the zone is paused or already shrinking. |
| `/br admin deathmatch [arena]` | Skips all remaining phases and starts the final collapse now. |

See [Zone](../gameplay/zone.md).

### Loot and gulag

| Command | What it does |
|---|---|
| `/br admin airdrop [arena]` | Drops a supply crate now (counted in the match's drops). If you are in that match's world and inside the zone it lands at your position, otherwise at the usual random point in the zone. |
| `/br admin refill [arena]` | Refills the match chests now and tells you how many were refilled. |
| `/br admin gulag open [arena]` | Forces the gulag **open** for the rest of the match, ignoring `gulag.min-players` and `gulag.close-seconds`. |
| `/br admin gulag close [arena]` | Forces it **closed**, like the automatic permanent close: no new entries; the queued players are resolved (last duels, or back into the match). |
| `/br admin gulag auto [arena]` | Back to the automatic rules. |

See [Loot, chests & airdrops](../gameplay/loot.md) and [Gulag](../gameplay/gulag.md).

### Start

| Command | What it does |
|---|---|
| `/br admin countdown <seconds> [arena]` | Sets the countdown of a starting match, or starts the countdown with that value from the lobby when there are enough players. Range 1-3600. |
| `/br start [arena]` / `/br stop [arena]` | Start now / stop without results (`br.admin.game`). |
| `/br forcejoin <player> [arena]` | Puts a player into an arena (`br.admin.game`). In the lobby they join normally. Within `game.force-join-seconds` (default 60) from the deploy of a running match they join and **play**: their own team in SOLO, a team with a free place in DUO/SQUAD (or a new team), deployed like everybody. Later they join as a spectator. The window is also in the setup GUI (**Match rules → Late force-join**). |

Most tools only work on a **live** match: they reply with a clear message otherwise (not running, not a participant, already alive, in a duel...).

## Debug

| Command | What it does |
|---|---|
| `/br debug` | Toggles debug logging (`[DEBUG]` lines in the console) until the next restart or until toggled again. |
| `/br debug report` | Writes a report file for support. |

Both need **`br.admin.debug`**.

The report is saved to `plugins/MRTBattleRoyale/reports/report-<date>_<time>.txt` and contains:

- plugin, server, Minecraft and Java versions, OS, online players, language, debug flag and stats storage;
- the list of plugins (enabled or not) with versions;
- the loaded worlds;
- every arena: mode, deploy mode, template world (flagged `NOT FOUND` if missing), player limits, spawns, chests, airdrops, geysers, radius, gulag, and what is missing;
- the state of every match;
- the contents of `config.yml`, `game.yml`, `lobby.yml`, `gulag.yml`, `economy.yml`, `loot.yml` and every `arenas/*.yml`.

!!! tip "Safe to share"
    Database access lines (`password`, `user`, `username`, `host`, `url`, `jdbc-url`) are replaced with `'<hidden>'`. The file is only written on your server: you decide whether to share it. Attach it when asking for help.

## Resetting statistics: `/br resetstats`

Resets the statistics of **every** player. Needs **`br.admin.resetstats`**, which is **not** part of `br.admin` (only `br.*` or given by hand). Works from the console too.

| Mode | What is reset |
|---|---|
| `/br resetstats stats` | Wins, kills, deaths, games, gulag wins, win streak, best placement and damage go back to zero. **Level, XP, coins and cosmetics are kept.** |
| `/br resetstats all` | **Every** row is deleted from the database: statistics, level, XP, coins and cosmetics. |

### How it is protected

1. **Two steps.** The first command only shows what will be deleted and a random 4-digit code. To run it, type `/br resetstats <mode> confirm <code>` (the line is clickable) within **60 seconds**, from the same sender and with the same mode. A wrong or expired code cancels; run the first command again for a new code.
2. **Refused during matches.** If any match is in progress (deploy, fight, deathmatch, or the end screen), the reset is refused: wait for it to end. Queues and countdowns do not block it.
3. **Automatic backup first.** If the backup cannot be made, nothing is touched.
    - **SQLite:** a full copy of the database in `plugins/MRTBattleRoyale/backups/stats-<yyyyMMdd_HHmmss>.db`.
    - **MySQL:** a table `br_stats_backup_<yyyyMMdd_HHmmss>` with a copy of `br_stats`.
4. **One transaction.** The reset is applied in a single transaction; if it fails, nothing changes and the online players' profiles get their values back.

The result message and the console show the number of players reset and the backup name. Statistics must be enabled (`stats.enabled`).

### Restoring a backup

=== "SQLite"

    1. **Stop the server** (the database file must not be open).
    2. In `plugins/MRTBattleRoyale/`, rename or move `stats.db` away.
    3. Copy `backups/stats-<date>.db` to `plugins/MRTBattleRoyale/stats.db`.
    4. Start the server.

=== "MySQL"

    Stop the server (or make sure no match is running and nobody joins), then copy the rows back:

    ```sql
    DELETE FROM br_stats;
    INSERT INTO br_stats SELECT * FROM br_stats_backup_20261012_210000;
    ```

    Use the table name shown in the reset message. Copying the rows keeps the keys and indexes of the original `br_stats` table, so prefer this to renaming the backup table. Drop old backup tables when you no longer need them.

## Update checker

Configured in `config.yml`:

```yaml
update-checker:
  # Checks Modrinth for a new version at start and every 6 hours.
  enabled: true
  # Tells admins when they join.
  notify-admins: true
  # Also report beta versions.
  include-beta: false
```

- The check runs off the main thread a few seconds after start and every 6 hours, against the plugin's Modrinth project.
- **Nothing is ever downloaded**: you are only told that an update exists.
- A newer version is logged once in the console, and players with **`br.admin.notify`** get a message with a clickable download link when they join (if `notify-admins` is on).
- Offline servers, firewalls and network errors never produce errors: the failure is only logged in debug mode. If Modrinth answers "not found" (404), the plugin stops checking until the next restart.
- `/br debug report` also says whether a newer version is available.

## Metrics (bStats)

```yaml
# Anonymous usage statistics (bStats, https://bstats.org).
metrics: true
```

Anonymous, aggregate data only: server and Java versions, player count and a few settings (language, storage type, number of arenas, whether the plugin's scoreboard, TAB and PlaceholderAPI are used). Never names or IPs. Turn it off here with `metrics: false`, or for every plugin at once in `plugins/bStats/config.yml`.
