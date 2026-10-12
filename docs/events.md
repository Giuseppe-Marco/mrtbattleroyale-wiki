# Events

An **event** is a session of several Battle Royale matches in a row, like a small tournament:

- only the players who **registered** can play;
- the **map** changes between matches, in a fixed rotation or by a random draw;
- the event can use its own **rules** and has its own **points leaderboard**, reset at every event;
- there are **prizes** at the end.

Events live in `plugins/MRTBattleRoyale/events.yml`. You can edit everything in game with `/br event edit`, start an event by hand with `/br event start <id>`, or let it start by itself at the scheduled times.

!!! info "Events don't touch normal progression"
    Event matches do **not** change the normal stats, XP, coins or the reward commands of `economy.yml`. They only count towards the event's own leaderboard.

## Quick start

1. `/br event create cup` creates a new event with the id `cup`.
2. `/br event edit cup` opens the editor. Click **Maps** and add the arenas you want to use.
3. Stand where players should wait between matches and run `/br event setlobby cup`.
4. `/br event start cup` opens the registration. Players click **[JOIN]** in chat or type `/br event join`.

## How an event goes

1. **Registration.** `/br event start <id>` opens the registration. The chat announces it with a clickable **[JOIN]** button, and a boss bar shows the time left. Players join with the button, the event menu or `/br event join`, and are taken straight to the **event lobby**. Whoever leaves (`/br event leave`) or is kicked goes back to the BR spawn.
2. **Start.** When the registration time ends:
    - if at least `players.min` registered players are online, the event starts;
    - otherwise it is cancelled and entry fees are refunded.
3. **Matches.** Matches are played one after the other, only with the registered players, on the current map. While the event runs, its arenas are **reserved**: nobody else can join them, and they are hidden from the arena menu. Eliminated players watch the rest of the match with the spectator camera, and everyone leaves together when it ends.
4. **Break between matches.** Players are back in the event lobby. They see their result (placement, kills, points earned), the top 5 of the standings and the time left. A boss bar counts down to the next match and shows its map.
5. **Map change.** Depending on the [map choice](#map-choice-rotation-or-random-draw), the map rotates on a timer or is drawn before every match. A running match is never cut short.
6. **End.** After `duration-minutes`, the match in progress ends normally. Then the final standings are announced, the podium is broadcast and the prizes are handed out. The standings are saved to `events/history/<id>-<date>.yml`.

## Map choice: rotation or random draw

In the editor, **Map choice** switches between the two modes (`map-selection` in `events.yml`).

=== "Rotation (default)"

    The maps are played in the order of the list, changing every `map-minutes`. The change always happens between two matches. With `map-minutes: 0` the event always uses the first map.

=== "Random draw"

    Before every match a map is drawn, with a slot-machine **reel animation**:

    - `draw-seconds` before the match (default **10**, max 30), every registered player in the event lobby gets a window with a reel of the maps that scrolls, slows down and stops on the drawn map. Everyone sees the same reel at the same time.
    - The map is announced in chat and with a title.
    - The window closes `draw-free-seconds` before the match (default **3**, max 10), leaving a few free seconds.
    - Players in a match, spectating or offline only get the chat message. Whoever closes the window is not forced to see it again.
    - With `draw-seconds: 0` there is no animation: the map is just announced.

    `map-minutes` is not used in this mode.

### How the map is drawn

1. Only **free** maps: playable, and not taken by a normal match.
2. Preferably maps that **fit the players online** (between their minimum and maximum players), else at least the ones whose minimum is reached.
3. **Never the map of the last match**, when there is another choice.
4. **Weighted**: a map played less in this event is more likely, with weight `1 / (1 + times played)²`. The maps take turns without a fixed order.

!!! tip "Forcing a map"
    `/br event map <arena>` forces the map of the next match: in random mode the window opens straight on it, without spinning. `/br event next` throws away the drawn map and draws again.

## Registration

| Option | What it does |
|---|---|
| `registration.minutes` | How long the registration stays open. |
| `registration.permission` | Permission needed to join (`''` = everybody). Only editable in the file. |
| `registration.entry-fee` | Plugin coins to pay to join. Refunded if the event is cancelled, or if you leave (or are kicked) **before it starts**. `0` = free. |
| `registration.late-join` | Players can still join after the start, between matches. |
| `players.min` | Registered players needed when the registration closes (at least 2). |
| `players.max` | Most registered players (`0` = no limit). |

A few details:

- A player waiting in a normal arena queue is taken out of it when they register. A player in a match in progress must finish it first (or leave) before joining late.
- A registered player who disconnects **stays registered**. When they come back they are sent to the event lobby and play the next match.
- With **late join**, a newcomer is told how many seconds are left until the next match.

## Event lobby

The event lobby is where registered players wait during the registration and between matches. Set it with `/br event setlobby <id>` (or in the editor: **Event lobby**, left click = set here, right click = teleport there). If it is not set, the players wait in the hub.

While in the event lobby, registered players:

- have their own **event chat** (`chat.event-format`, see [Chat](features/chat.md));
- see the **event scoreboard** (see [below](#scoreboard-and-placeholders)).

## Players per match

`match-players.min` / `max` (in the editor: *Players per match*) apply the same limits to every arena of the event:

- **min** = registered players online needed to start a match;
- **max** = players per match. Whoever does not fit plays the next match. In arenas with fixed spawns, the number of spawns is still the limit.

`0` = each arena keeps its own min / max players.

!!! warning
    Keep `players.max` within the arenas' maximum players. If a map is full, the players who don't fit play the next match.

## Points

Each match gives **placement points** plus **points per kill**. With the defaults: 25 points for 1st place, 18 for 2nd, 15 for 3rd... and 2 per kill.

```yaml
scoring:
  placement:
    '1': 25
    '2': 18
    '3': 15
    '4': 12
    '5': 10
    '6': 8
    '7': 6
    '8': 4
    '9': 2
    '10': 1
  per-kill: 2
```

- A placement that is not in the table gives 0 placement points (kills still count).
- A player who leaves a match while still alive gets no placement points for it, but keeps their kills.
- The player with the most points wins. On equal points, more **wins**, then more **kills**, then the **best placement** decide.

Admins can fix a score with `/br event points <player> <+n|-n>`.

### Undoing the last match

If a match went wrong (a bug, a cheater, a crash), `/br event undo` cancels its results: points, matches, wins, kills and best placement go back to what they were before it.

1. `/br event undo` shows what would be undone (match number, map, players, points) and a clickable confirmation.
2. Within **60 seconds**, confirm with one of:
    - `/br event undo confirm` cancels the results;
    - `/br event undo replay` cancels them **and** plays the same map again in the next match.

Repeat it to undo the matches before, up to the last **20**. The registered players are told about it in chat.

!!! note
    Undo works on **finished** matches. To cancel a match that is still running, use `/br stop <arena>`: it ends without results.

## Custom rules

By default an event plays every arena with its own settings, exactly like normal matches. The **Custom rules** switch (top of the **Rules** screen, `custom-rules` in the file) changes that:

| `custom-rules` | Effect |
|---|---|
| `false` (default) | Every arena uses its own settings. The event rules are ignored, but kept for later. |
| `true` | The rules apply to **every** arena of the event, over the arena settings. A rule that is not set leaves each arena with its own value (its `settings:`, else `game.yml`). |

The editor offers these rules: gulag, knockdown, moving zone, building, drop on death, airdrops, chest sound, damage outside the zone, grace period, deathmatch duration, chests per player, chest refill, airdrop interval, duel duration and bleed-out time. **Shift+click** removes a rule, so every arena uses its own value again.

Any other option of `game.yml` or `gulag.yml` can be added by hand under `rules:`, with the same path:

```yaml
custom-rules: true
rules:
  gulag:
    enabled: false
  zone:
    damage-per-second: 4.0
```

!!! warning "0 is a real value"
    A rule set to `0` really means 0 (for example `loot.refill-seconds: 0` = no refill). It does **not** mean "use the arena's value": to get that, remove the rule.

## Schedule (automatic start)

An event can start by itself at set times. The time you set is when the **first match starts**: the registration opens `registration.minutes` before it. For example, a start at 21:00 with 5 minutes of registration opens the registration at 20:55.

Accepted formats:

| Format | Meaning |
|---|---|
| `'2026-10-12 21:00'` or `'12/10/2026 21:00'` | Once, on that date. |
| `'SAT 21:00'` | Every week. Days: `MON TUE WED THU FRI SAT SUN` (or the Italian `LUN MAR MER GIO VEN SAB DOM`). |
| `'DAILY 21:00'` | Every day. |

Three ways to set it:

=== "GUI"

    `/br event edit <id>` → **Schedule**. It shows the start times with their next date; **Shift+click** removes one. The bottom row builds a new start time with clicks: repeat (once / every week / every day), date or day of the week, hour, minutes, then **Add**.

=== "Command"

    ```
    /br event schedule <id> add SAT 21:00
    /br event schedule <id> list
    /br event schedule <id> remove <n>
    ```

=== "events.yml"

    ```yaml
    schedule:
      - 'SAT 21:00'
      - 'DAILY 18:30'
    announce-before: [60, 30]
    ```

More options:

- `announce-before: [60, 30]` announces the event in chat that many minutes before the start, before the registration opens.
- `timezone:` at the top of `events.yml` (for example `Europe/Rome`) sets the timezone of the times. Empty = the server's.
- If another event is still running at that time, the scheduled one is skipped and the console says so.
- A date in the past never starts again. The menu shows it in red.

## Rewards

```yaml
rewards:
  '1':
  - 'say %player% won the event with %points% points!'
  '2': []
  '3': []
  participation: []
  coins:
    '1': 500
    '2': 250
    '3': 100
```

- `'1'`, `'2'`, `'3'`... are **console commands** for that final rank. You can use `%player%`, `%rank%` and `%points%`.
- `participation` runs for everybody who played at least one match.
- `coins` gives plugin coins by rank. The editor's **Prizes** screen edits the coins of the first 5 places; the commands are set in the file.

Withdrawn or kicked players are not in the final standings, but they stay in the history file.

## The editor GUI

`/br event edit` opens the list of events; `/br event edit <id>` opens one directly. Every click is saved to `events.yml` right away.

- **Events list.** Left click opens the editor of an event, right click opens its registration. Each event shows whether it is ready or what is missing.
- **Editor.** Team mode, maps, map choice (rotation / random) with its timings, map change interval, duration, minimum and maximum players, players per match, break between matches, registration time, entry fee, late join, points per kill, placement points, rules, prizes, schedule, event lobby (left click = set here, right click = teleport there), start (or stop, while running) and delete.
- **Maps.** Click an arena to add it to the list or remove it. Right click moves it earlier in the order.
- **Points.** Placement points for the places from 1st to 14th, plus points per kill.
- **Rules.** The Custom rules switch and the rules described [above](#custom-rules).
- **Prizes.** Coins for the first 5 places.
- **Schedule.** See [Schedule](#schedule-automatic-start).

Players get their own simple menu with `/br event` (or the **event** button in the arena menu while an event runs): join or leave, phase, map, their points and the top 10.

## Commands

All commands are sub-commands of `/br` (alias `/battleroyale`). Admin commands need `br.admin.event` and also work from the console. See also [Commands](reference/commands.md).

| Command | Who | What it does |
|---|---|---|
| `/br event` | everyone | Event menu: join / leave and standings |
| `/br event join` / `leave` | everyone | Join the open event / leave it (the fee is refunded only before the start) |
| `/br event info` / `top` | everyone | State, maps and time left / standings |
| `/br event list` | admin | Events in `events.yml` |
| `/br event edit [id]` | admin | Event editor GUI |
| `/br event create <id>` / `delete <id>` | admin | Create / delete an event |
| `/br event setlobby <id>` | admin | Set the event lobby to your position |
| `/br event start <id>` | admin | Open the registration |
| `/br event forcestart` | admin | End the registration now (needs at least 2 players online) |
| `/br event end` | admin | End now, with the standings so far and the prizes |
| `/br event stop` | admin | Cancel the event (no prizes, fees refunded) |
| `/br event next` | admin | Next match on the next map (random mode: draw again) |
| `/br event map <arena>` | admin | Force the map of the next match |
| `/br event pause` / `resume` | admin | Stop / resume starting new matches |
| `/br event add <player>` / `kick <player>` | admin | Register (no fee, no limits) / remove a player |
| `/br event points <player> <+n/-n>` | admin | Correct someone's points |
| `/br event announce` | admin | Send the [JOIN] announcement again |
| `/br event undo [confirm\|replay]` | admin | Cancel the results of the last finished match ([details](#undoing-the-last-match)) |
| `/br event schedule <id> [list\|add <time>\|remove <n>]` | admin | Start times |

## Scoreboard and placeholders

While waiting in the event lobby, registered players see the **`event`** board. Its texts are in the messages file under `scoreboard.boards.event`, and you can turn it off with `scoreboard.boards.event: false` in `config.yml`. During a match the normal match board stays, and you can add the event placeholders to it. See [Scoreboard](features/scoreboard.md).

| Scoreboard | PlaceholderAPI | Value |
|---|---|---|
| `<event_name>` | `%brbattle_event_name%` | event name |
| `<event_state>` | `%brbattle_event_state%` | registration / break / match |
| `<event_timer>` | `%brbattle_event_timer%` | time to the next step (mm:ss) |
| `<event_time_left>` | `%brbattle_event_time_left%` | minutes left of the event |
| `<event_map>` / `<event_next_map>` | `%brbattle_event_map%` / `%brbattle_event_next_map%` | current / next map |
| `<event_players>` | `%brbattle_event_players%` | registered players |
| `<event_points>` / `<event_rank>` | `%brbattle_event_points%` / `%brbattle_event_rank%` | your points / position |
| `<event_matches>` `<event_wins>` `<event_kills>` | `%brbattle_event_matches%` `%brbattle_event_wins%` `%brbattle_event_kills%` | your event stats |
| — | `%brbattle_event_running%` / `%brbattle_event_registered%` | `1` / `0` |

### Placeholders for holograms

These are made for holograms and signs (DecentHolograms, FancyHolograms...). They read a copy of the standings refreshed every second, so they are safe from any thread.

=== "Next scheduled event"

    | Placeholder | Value |
    |---|---|
    | `%brbattle_event_next_name%` | name of the next scheduled event |
    | `%brbattle_event_next_time%` | when it starts (dd/MM HH:mm) |
    | `%brbattle_event_next_in%` | how long until then (for example `2h 15m`) |

=== "Running event"

    Live standings. `<n>` is the position (1, 2, 3...).

    | Placeholder | Value |
    |---|---|
    | `%brbattle_event_top_<n>_name%` | player in position n (`-` if none) |
    | `%brbattle_event_top_<n>_points%` | their points |
    | `%brbattle_event_top_<n>_wins%` / `_kills%` / `_matches%` | wins / kills / matches played |
    | `%brbattle_event_top_<n>_best%` | best placement |
    | `%brbattle_event_winner%` | who is first right now |

=== "Last finished event"

    Stays until the next event ends, and is reloaded from `events/history/` after a restart.

    | Placeholder | Value |
    |---|---|
    | `%brbattle_event_last_name%` | event name |
    | `%brbattle_event_last_date%` | when it ended (dd/MM/yyyy HH:mm) |
    | `%brbattle_event_last_winner%` / `%brbattle_event_last_winner_points%` | winner and their points |
    | `%brbattle_event_last_matches%` / `%brbattle_event_last_players%` | matches played / players ranked |
    | `%brbattle_event_last_top_<n>_name%` (also `_points`, `_wins`, `_kills`, `_matches`, `_best`) | final standings |

Example hologram:

```
&6&lLAST EVENT &7- %brbattle_event_last_name%
&7%brbattle_event_last_date%
&e#1 &f%brbattle_event_last_top_1_name% &7- &6%brbattle_event_last_top_1_points% pts
&7#2 &f%brbattle_event_last_top_2_name% &7- &6%brbattle_event_last_top_2_points% pts
&c#3 &f%brbattle_event_last_top_3_name% &7- &6%brbattle_event_last_top_3_points% pts
```

All placeholders are listed in [Placeholders](reference/placeholders.md).

## Full example of events.yml

This is the default example event, as shipped:

```yaml
# Timezone of the schedule times (e.g. Europe/Rome). Empty = the server's.
timezone: ''

events:
  example:
    name: '<gradient:#ffd700:#ff8c00><bold>Example Cup</bold></gradient>'
    mode: ''                      # SOLO / DUO / SQUAD, empty = each arena's own
    maps: []                      # arena names, in order
    map-selection: rotation       # rotation | random
    map-minutes: 15               # rotation only; 0 = never change
    draw-seconds: 10              # random only; 0 = no animation
    draw-free-seconds: 3
    duration-minutes: 60          # 0 = until /br event end
    players:
      min: 4
      max: 0                      # 0 = no limit
    lobby:                        # /br event setlobby <id>; empty = the hub
      world: ''
      pos: ''
    registration:
      minutes: 5
      permission: ''
      entry-fee: 0
      late-join: true
    between-matches-seconds: 30
    match-players:                # 0 = each arena's own
      min: 0
      max: 0
    schedule: []
    announce-before: [60, 30]
    scoring:
      placement:
        '1': 25
        '2': 18
        '3': 15
        '4': 12
        '5': 10
        '6': 8
        '7': 6
        '8': 4
        '9': 2
        '10': 1
      per-kill: 2
    custom-rules: false
    rules:
      gulag:
        enabled: false
      zone:
        damage-per-second: 4.0
    rewards:
      '1':
      - 'say %player% won the event with %points% points!'
      '2': []
      '3': []
      participation: []
      coins:
        '1': 500
        '2': 250
        '3': 100
```

Apply changes made in the file with `/br reload` (changes made in the editor are saved and applied at once).

## Good to know

- Only **one event** runs at a time.
- If an arena of the event is busy with a normal match when the event needs it, the event waits for that match to end.
- A match that ends without a result (for example stopped by an admin) gives no points.
- A server restart cancels a running event: fees are refunded and the arenas are released.
