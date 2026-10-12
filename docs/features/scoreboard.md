# Scoreboard

MRTBattleRoyale draws its own sidebar scoreboard. You don't need TAB or PlaceholderAPI for it. The board changes automatically with the situation the player is in.

## The boards

| Board | Shown when |
|---|---|
| `hub` | in the hub world, not in a match |
| `waiting` | in an arena's waiting lobby |
| `countdown` | the match is about to start |
| `match` | playing (deploy, match, deathmatch) |
| `gulag` | in the gulag (waiting or fighting) |
| `spectator` | eliminated, watching the match |
| `ending` | end-of-match screen |
| `event` | registered in a running [event](../events.md), in the event lobby |

Outside these situations (for example in your survival world) the plugin shows no sidebar. Until you set the hub with `/br setlobby` (and `hub-worlds` is empty), the `hub` board is shown in every world.

## Settings: config.yml

```yaml
scoreboard:
  enabled: true
  # How often the boards are refreshed, in ticks (20 = 1 second). Minimum 5.
  update-ticks: 10
  # Worlds where the "hub" board is shown. Empty = the lobby world
  # (/br setlobby), or every world when no lobby is set.
  hub-worlds: []
  # Format of the <date> placeholder (Java date pattern).
  date-format: 'dd/MM/yyyy'
  # Your server address, shown by the <server> placeholder at the bottom of the boards.
  server-address: 'play.yourserver.com'
  # Turn single boards on/off (off = no sidebar in that situation).
  boards:
    hub: true
    waiting: true
    countdown: true
    match: true
    gulag: true
    spectator: true
    ending: true
    # Registered in a running event, waiting in the event lobby.
    event: true
```

!!! tip "Set your server address"
    The default boards end with `<server>`. Change `server-address` to your own address, or remove that line from the boards.

## Texts: the messages file

Titles and lines are in the `scoreboard.boards` section of `languages/messages_<lang>.yml`, so they follow the plugin language. For example, the default `match` board:

```yaml
scoreboard:
  boards:
    match:
      title: '<#ff5f6d><bold>ʙᴀᴛᴛʟᴇ<reset> <#ffc371><bold>ʀᴏʏᴀʟᴇ'
      lines:
        - '<#ff5f6d><strikethrough>                              '
        - '<gray>ᴍᴀᴘ <dark_gray>» <white><arena> <dark_gray>(<gray><mode><dark_gray>)'
        - '<gray>ᴛɪᴍᴇ <dark_gray>» <white><match_time>'
        - ' '
        - '<gray>ᴀʟɪᴠᴇ <dark_gray>» <green><alive> <dark_gray>• <gray>ᴛᴇᴀᴍꜱ <dark_gray>» <white><teams>'
        - '<gray>ʏᴏᴜʀ ᴋɪʟʟꜱ <dark_gray>» <#ff5f6d><bold><kills>'
        - '  '
        - '<gray>ᴢᴏɴᴇ <dark_gray>» <red><zone>'
        - '<gray>ᴘʜᴀꜱᴇ <dark_gray>» <white><phase>'
        - '   '
        - '<dark_purple>ɢᴜʟᴀɢ <dark_gray>» <white><gulag_status> <dark_gray>(<gray>ǫᴜᴇᴜᴇ <white><gulag_queue><dark_gray>)'
        - '<#ff5f6d><strikethrough>                              '
        - '<#ffc371><bold><server>'
```

Rules for the lines:

- Formatting is [MiniMessage](https://docs.advntr.dev/minimessage/format.html), like the rest of the plugin.
- At most **15 lines**.
- `''` is an empty spacer line.
- A line made **only of placeholders** that ends up empty is hidden. Lines with any literal text stay, including spaces and strikethrough separators. For example `<teammate_3>` disappears when you don't have a third teammate, and `<team_header>` disappears in SOLO.
- With PlaceholderAPI installed, any `%placeholder%` works too.
- Apply changes with `/br reload`.

The texts of a few placeholders are in the same `scoreboard` section: `zone-waiting`, `zone-shrinking`, `zone-final`, `zone-inside`, `zone-outside`, `team-header`, `teammate-alive`, `teammate-down`, `teammate-gulag`, `teammate-dead`, `teammate-offline`, `gulag-duel` and `none`.

## Placeholders inside the boards

These `<tags>` work only inside the plugin's scoreboard. For other plugins use the `%brbattle_...%` placeholders: see [Placeholders](../reference/placeholders.md).

=== "Always"

    | Placeholder | Value |
    |---|---|
    | `<player>` | player name |
    | `<online>` | online players |
    | `<date>` | today's date (`date-format`) |
    | `<server>` | your server address (`server-address`) |
    | `<level>` `<xp>` `<coins>` | progression |
    | `<xp_progress>` `<xp_target>` `<xp_percent>` | XP in the current level, XP needed for the level, percent |
    | `<wins>` `<kills_total>` `<deaths>` `<games>` | lifetime stats |
    | `<kd>` `<winrate>` `<winstreak>` | lifetime stats |

=== "Arena and match"

    Outside an arena they show `-` or `0`.

    | Placeholder | Value |
    |---|---|
    | `<arena>` `<mode>` | arena name, SOLO / DUO / SQUAD |
    | `<state>` | match state (Waiting, Starting, In progress...) |
    | `<players>` `<max_players>` `<min_players>` `<needed>` | queue |
    | `<countdown>` | seconds to the start |
    | `<alive>` `<teams>` | living players / teams |
    | `<match_time>` | match time (mm:ss) |
    | `<kills>` `<damage>` `<placement>` | your match |
    | `<team>` | your team, in its color |
    | `<phase>` `<phases>` | current zone phase / total |
    | `<zone>` | the zone in one text: closing in / shrinking / final |
    | `<zone_state>` | "Zone closes in" / "Zone shrinking" / "Final zone" |
    | `<zone_timer>` | seconds of the current zone step (mm:ss) |
    | `<zone_size>` | width of the zone in blocks |
    | `<zone_status>` | "Inside the zone" or "N m from the zone" |
    | `<gulag_opponent>` `<gulag_queue>` | gulag opponent / players queued |
    | `<gulag_status>` `<gulag_duel>` | gulag state, whether a duel is running |
    | `<spectating>` | the player you are watching |
    | `<team_header>` | "Your team" in DUO/SQUAD, empty in SOLO |
    | `<teammate_1>` … `<teammate_4>` | each teammate: name + health, or DOWN / GULAG / eliminated / offline |

=== "Events"

    | Placeholder | Value |
    |---|---|
    | `<event_name>` `<event_state>` | event name / registration, break or match |
    | `<event_timer>` | time to the next step (mm:ss) |
    | `<event_time_left>` | minutes left of the event |
    | `<event_map>` `<event_next_map>` | current / next map |
    | `<event_players>` | registered players |
    | `<event_points>` `<event_rank>` | your points / position |
    | `<event_matches>` `<event_wins>` `<event_kills>` | your event stats |

## Using the TAB plugin

Only **one sidebar** can be visible at a time. If you use the **TAB** plugin (by NEZNAMY), pick who draws the sidebar:

=== "Keep the plugin's scoreboard"

    Turn off TAB's scoreboard feature in `plugins/TAB/config.yml`:

    ```yaml
    scoreboard:
      enabled: false
    ```

    TAB's tab list, name tags and the rest keep working.

=== "Use TAB's scoreboard"

    Turn off the plugin's boards in `config.yml`:

    ```yaml
    scoreboard:
      enabled: false
    ```

    Then build TAB's boards with the `%brbattle_...%` placeholders. `%brbattle_board%` returns `HUB`, `LOBBY` or `MATCH`, made for TAB's `display-condition` to switch board by situation.

The tab list itself is **not** formatted by the plugin: use TAB with the `%brbattle_*%` placeholders for that.

### Name colors in matches with TAB

In a match the plugin colors each player's name (team color in DUO/SQUAD, a random color in SOLO) and hides name tags according to `team.visuals.nametags` in `game.yml`. It does this with scoreboard teams.

If TAB's **`scoreboard-teams`** feature is on, TAB takes over the names and blocks the plugin (you'll see lines like *"Blocked attempt to add player ... into team br..."* in `plugins/TAB/anti-override.log`). Tell TAB to leave players alone **while they are in a match**, in `plugins/TAB/config.yml`:

```yaml
scoreboard-teams:
  enabled: true
  # ...
  disable-condition: '%world%=disabledworld|%brbattle_in_match%=1'
```

Outside matches TAB keeps handling names and sorting as before. Run `/tab reload` afterwards.

If you use TAB's `tablist-name-formatting`, add the color to the tab prefix:

```yaml
# plugins/TAB/groups.yml
_DEFAULT_:
  tabprefix: "%brbattle_name_color%"
```

In SOLO, with `team.visuals.hex-tab-names: true` (the default) the plugin writes each player's tab name itself, with their unique hex color and a marker in front; TAB must not handle tab names in matches for this. If you prefer to do it with TAB, set `hex-tab-names: false` and use:

```yaml
tabprefix: "%brbattle_solo_marker%%brbattle_name_color_hex%"
```
