# Placeholders

With [PlaceholderAPI](https://modrinth.com/plugin/placeholderapi) installed, the plugin registers the expansion **`brbattle`**. Format: `%brbattle_<key>%`.

!!! note "Requirements"
    - PlaceholderAPI must be installed: on start the console shows `Hooked into PlaceholderAPI.` Without it, none of these placeholders exist (the plugin's own scoreboard and messages still work).
    - The expansion is built into the plugin: you do **not** need `/papi ecloud download`.

**How values behave**

- Match placeholders are **relative to the player**: they read the match the player is in. Outside a match they return `NONE` or `0`, never an empty string (empty values break TAB conditions and number formats).
- Translated texts (state names, zone text, gulag status, Yes/No) come from the language file chosen with `language` in `config.yml`. The examples below use English.
- Yes/No values come from `placeholder.bool-true` / `placeholder.bool-false` in the messages file (`Yes` / `No` in English).
- Values are case-insensitive: `%brbattle_WINS%` works too.

## Board

| Placeholder | Description | Values |
|---|---|---|
| `%brbattle_board%` | Which screen the player is on, made for TAB `display-condition`s | `HUB`, `LOBBY`, `MATCH` |

`HUB` = not in a game, `LOBBY` = in a queue or countdown, `MATCH` = deploy, match or end screen. This is what lets you show a different scoreboard in each phase. See [Scoreboard](../features/scoreboard.md).

## Match (relative to the player)

| Placeholder | Description | Examples |
|---|---|---|
| `%brbattle_state%` | Internal state | `WAITING`, `COUNTDOWN`, `DEPLOYING`, `ACTIVE`, `DEATHMATCH`, `ENDING`, `RESETTING`, `NONE` |
| `%brbattle_state_name%` | Translated state (section `state` of the messages file, includes its colour tags) | `Waiting`, `In progress` |
| `%brbattle_arena%` | Arena name (in small caps) | `ꜰᴏʀᴇꜱᴛ`, `NONE` |
| `%brbattle_players%` | Players in the game | `8` |
| `%brbattle_player_count%` | Same as `players` (older name, kept for existing TAB configs) | `8` |
| `%brbattle_min%` | Minimum players | `2` |
| `%brbattle_max%` | Maximum players | `10` |
| `%brbattle_needed%` | Players still missing before the countdown starts | `2` |
| `%brbattle_alive%` | Players alive | `7` |
| `%brbattle_countdown%` | Seconds to the start | `30` |
| `%brbattle_mode%` | Team mode | `SOLO`, `DUO`, `SQUAD`, `NONE` |
| `%brbattle_teams%` | Teams still alive | `3` |
| `%brbattle_time%` | Elapsed match time (mm:ss) | `04:12` |

## Zone

| Placeholder | Description | Examples |
|---|---|---|
| `%brbattle_zone%` | Zone text (`placeholder.zone-*` in the messages file) | `In 45s`, `Closing: 20s`, `Final showdown` |
| `%brbattle_zone_timer%` | Seconds left in the current zone step | `45` |
| `%brbattle_zone_state%` | Zone state | `WAITING`, `SHRINKING`, `DEATHMATCH`, `NONE` |
| `%brbattle_zone_phase%` | Current phase number, or `DM` in the deathmatch | `2`, `DM` |

See [Zone](../gameplay/zone.md).

## Gulag

| Placeholder | Description | Examples |
|---|---|---|
| `%brbattle_gulag_status%` | Gulag status | `OPEN`, `CLOSED`, `IN DUEL`, `NONE` |
| `%brbattle_gulag_queue%` | Players waiting for a duel | `2` |
| `%brbattle_gulag_duel%` | Is a duel running? | `Yes`, `No` |

See [Gulag](../gameplay/gulag.md).

## Player (in a match)

| Placeholder | Description | Examples |
|---|---|---|
| `%brbattle_in_match%` | `1` from the deploy to the end screen, `0` otherwise (also in the queue). Numeric, so it does not depend on the language: use it in TAB conditions | `1`, `0` |
| `%brbattle_is_alive%` | Alive? | `Yes`, `No` |
| `%brbattle_is_spectator%` | Spectating? | `Yes`, `No` |
| `%brbattle_is_in_gulag%` | In the gulag? | `Yes`, `No` |
| `%brbattle_is_down%` | Knocked down? | `Yes`, `No` |
| `%brbattle_match_kills%` | Kills in this match | `3` |
| `%brbattle_match_deaths%` | `0` while alive or in the gulag, `1` once eliminated | `0`, `1` |
| `%brbattle_teammates%` | Teammates still alive | `2` |
| `%brbattle_teamsize%` | Size of the player's team | `4` |
| `%brbattle_placement%` | Placement (0 while still in play) | `1`, `5` |
| `%brbattle_team_name%` | Team name | `Team 1`, `NONE` |
| `%brbattle_team_color%` | Colour name of the team (in SOLO: the player's colour) | `aqua`, `red`, `NONE` |
| `%brbattle_name_color%` | Legacy colour code ready for TAB prefixes: team colour in DUO/SQUAD, the player's colour in SOLO. Empty outside a match | `&b`, `&c` |
| `%brbattle_name_hex%` | Hex colour of the name: the player's own unique colour in SOLO, the team colour in DUO/SQUAD | `#ff5555` |
| `%brbattle_name_color_hex%` | Same as `name_hex`, ready for TAB (`&#rrggbb`) | `&#ff5555` |
| `%brbattle_solo_marker%` | The coloured SOLO marker (`team.visuals.solo-marker` in `game.yml`) in `&#rrggbb` form for TAB. Empty in DUO/SQUAD | `&#ff5555● ` |

See [Teams, knockdown & colours](../gameplay/teams.md).

## Party

| Placeholder | Description | Examples |
|---|---|---|
| `%brbattle_in_party%` | In a party? | `Yes`, `No` |
| `%brbattle_party_size%` | Party members | `3` |
| `%brbattle_party_max%` | Maximum party size | `4` |
| `%brbattle_party_leader%` | Leader's name (empty without a party) | `Steve` |
| `%brbattle_party_is_leader%` | Is the player the leader? | `Yes`, `No` |
| `%brbattle_party_members%` | Members, comma separated (empty without a party) | `Steve, Alex` |

## Arena (for selectors and holograms)

Format: `%brbattle_arena_<what>_<arena>%`, where `<what>` is one of `state`, `state_name`, `players`, `min`, `max`, `alive`, `needed` and `<arena>` is the arena name. No player needed. An unknown arena gives `NONE` / `0`.

| Placeholder | Description | Examples |
|---|---|---|
| `%brbattle_arena_state_forest%` | State of arena `forest` | `WAITING`, `NONE` |
| `%brbattle_arena_state_name_forest%` | Translated state | `Waiting` |
| `%brbattle_arena_players_forest%` | Players in the arena | `4` |
| `%brbattle_arena_min_forest%` / `_max_` | Minimum / maximum players | `2` / `16` |
| `%brbattle_arena_alive_forest%` | Players alive | `9` |
| `%brbattle_arena_needed_forest%` | Players missing before the countdown | `2` |

## Global (no player needed)

| Placeholder | Description | Examples |
|---|---|---|
| `%brbattle_total_players%` | Players in all games | `24` |
| `%brbattle_arenas%` | Loaded arenas | `3` |
| `%brbattle_active_games%` | Matches in progress | `2` |
| `%brbattle_waiting_games%` | Games accepting players (waiting or countdown) | `1` |
| `%brbattle_total_online%` | Players online on the server | `37` |

## Leaderboards (no player needed)

`%brbattle_top_<category>_<n>_name%` and `%brbattle_top_<category>_<n>_value%`, with `n` from 1 to 10. Read from a cache refreshed every `stats.leaderboard-refresh-seconds` (`config.yml`, default 60, minimum 5): they never query the database on the main thread. An empty row gives `---` / `0`.

Categories: `wins`, `kills`, `deaths`, `games`, `xp`, `coins`, `gulag_wins`, `winstreak`, `damage_dealt`, plus the aliases `level` (= xp), `gulag` and `damage`.

| Placeholder | Description | Examples |
|---|---|---|
| `%brbattle_top_wins_1_name%` | First in the wins leaderboard | `Steve` |
| `%brbattle_top_wins_1_value%` | Their wins | `42` |
| `%brbattle_top_kills_3_name%` | Third by kills | `Alex` |
| `%brbattle_top_gulag_wins_10_value%` | Tenth by gulag wins | `7` |

## Profile (from the database)

Read from the profile loaded when the player joins; a player not loaded gives `0`.

| Placeholder | Description | Examples |
|---|---|---|
| `%brbattle_level%` | Level | `12` |
| `%brbattle_xp%` | Total XP | `5840` |
| `%brbattle_xp_progress%` | XP inside the current level | `340` |
| `%brbattle_xp_target%` | XP needed for the next level | `500` |
| `%brbattle_xp_percent%` | Progress to the next level, % | `68` |
| `%brbattle_coins%` | Coins | `1250` |
| `%brbattle_wins%` | Total wins | `7` |
| `%brbattle_games%` | Games played | `96` |
| `%brbattle_kills%` | Total kills | `143` |
| `%brbattle_deaths%` | Total deaths | `89` |
| `%brbattle_winrate%` | Win rate, % | `7.3` |
| `%brbattle_kd%` | Kill/death ratio | `1.6` |
| `%brbattle_gulag_wins%` | Gulag duels won | `12` |
| `%brbattle_winstreak%` | Current win streak | `2` |
| `%brbattle_best_placement%` | Best placement (`-` if none yet) | `1`, `-` |
| `%brbattle_damage%` | Total damage dealt (half hearts) | `1840` |

See [Stats, levels & shop](../features/progression.md).

## Events

All with the `%brbattle_` prefix. See [Events](../events.md).

**Running event**

| Placeholder | Description | Examples |
|---|---|---|
| `event_running` | `1` while an event is running, else `0` | `1` |
| `event_name` | Event name | `Friday Cup`, `-` |
| `event_state` | Phase (translated) | `registration`, `break between matches`, `match in progress`, `-` |
| `event_registered` | `1` if the player is registered | `1`, `0` |
| `event_timer` | Phase timer (mm:ss) | `02:30`, `00:00` |
| `event_time_left` | Minutes left in the event | `45` |
| `event_map` | Current map | `ꜰᴏʀᴇꜱᴛ`, `-` |
| `event_next_map` | Map of the next match | `ᴅᴇꜱᴇʀᴛ`, `-` |
| `event_players` | Registered players | `18` |
| `event_points` | The player's points | `35` |
| `event_rank` | The player's position (0 = not ranked) | `3` |
| `event_matches` | Matches the player has played in the event | `4` |
| `event_wins` | The player's event wins | `1` |
| `event_kills` | The player's event kills | `9` |
| `event_winner` | Current leader | `Steve`, `-` |
| `event_top_<n>_<field>` | Row `n` of the live standings; `<field>` = `name`, `points`, `wins`, `kills`, `matches`, `best` | `event_top_1_name` = `Steve` |

**Next scheduled event**

| Placeholder | Description | Examples |
|---|---|---|
| `event_next_name` | Name of the next scheduled event | `Friday Cup`, `-` |
| `event_next_time` | When it starts (dd/MM HH:mm) | `16/10 21:00` |
| `event_next_in` | Time until it starts | `2h 15m`, `40m`, `1d 3h` |

**Last finished event**

| Placeholder | Description | Examples |
|---|---|---|
| `event_last_name` | Name | `Friday Cup`, `-` |
| `event_last_date` | Date (dd/MM/yyyy HH:mm) | `09/10/2026 22:40` |
| `event_last_winner` | Winner | `Steve`, `-` |
| `event_last_winner_points` | Winner's points | `58` |
| `event_last_matches` | Matches played | `6` |
| `event_last_players` | Players in the final standings | `18` |
| `event_last_top_<n>_<field>` | Row `n` of the final standings (same fields as above) | `event_last_top_2_points` = `51` |

Empty rows give `-` for `name` and `best`, `0` for the numbers.

## Using the TAB plugin

### One scoreboard per phase

Use `%brbattle_board%` in the `display-condition` of TAB's scoreboards (the `scoreboard` section of TAB's `config.yml`):

```yaml
scoreboard:
  scoreboards:
    br-hub:
      display-condition: "%brbattle_board%=HUB"
      # ...
    br-lobby:
      display-condition: "%brbattle_board%=LOBBY"
      # ...
    br-match:
      display-condition: "%brbattle_board%=MATCH"
      # ...
```

If you use TAB's scoreboard, turn off the plugin's own sidebar (`scoreboard.enabled: false` in the plugin's `config.yml`) so the two don't fight. See [Scoreboard](../features/scoreboard.md).

### Name colours and name tags in a match

During a match the plugin colours every name (team colour in DUO/SQUAD, a unique colour per player in SOLO) and hides name tags (`team.visuals.nametags` in `game.yml`: `hidden`, `enemies` or `visible`). It does this with scoreboard teams.

If TAB's **`scoreboard-teams`** feature is on, TAB blocks this (`plugins/TAB/anti-override.log` shows lines like *"Blocked attempt to add player ... into team br..."*). Tell TAB not to manage players **while they are in a match**, in `plugins/TAB/config.yml`:

```yaml
scoreboard-teams:
  enabled: true
  # ...
  disable-condition: '%world%=disabledworld|%brbattle_in_match%=1'
```

`|` means "or". Outside a match (hub, queue) TAB keeps handling names and sorting; in a match the plugin does. Then run `/tab reload`.

### Colours in the tab list

The tab list colour comes from the team automatically when TAB's `tablist-name-formatting` is off. If you use it, add the colour to the tab prefix:

```yaml
# plugins/TAB/groups.yml
_DEFAULT_:
  tabprefix: "%brbattle_name_color%"
```

In SOLO every player has a unique **hex** colour. With `team.visuals.hex-tab-names: true` (default, `game.yml`) the plugin writes the tab list name itself, in that colour and with the SOLO marker in front: for this, TAB must not manage tab list names during a match. If you prefer to let TAB do it, set `hex-tab-names: false` and use:

```yaml
tabprefix: "%brbattle_solo_marker%%brbattle_name_color_hex%"
```
