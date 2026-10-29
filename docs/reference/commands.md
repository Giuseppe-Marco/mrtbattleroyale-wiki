# Commands

Every command of the plugin is a sub-command of **`/br`** (alias **`/battleroyale`**). `/br help` (or `/br` alone) lists the commands; staff also get the admin part of the list.

!!! note "No top-level shortcuts"
    The plugin registers **only** `/br` and `/battleroyale`. There is no `/join`, `/leave`, `/party`, `/top`... of its own, so it never takes those commands away from the other plugins of your server. If you want shortcuts, create aliases with your server's command alias system.

!!! tip "Help and tab completion follow permissions"
    `/br help` and tab completion only show the commands the sender is allowed to use. `/br leave` and `/br help` never need a permission. See [Permissions](permissions.md).

**Notation:** `<required>`, `[optional]`, `a|b` = one of the values.

## Player commands

| Command | Description | Permission |
|---|---|---|
| `/br help` | Shows the command list. | none |
| `/br join` | Joins a match. With a single arena, joins it; otherwise joins the fullest available match (quick-join, `game.quick-join` in `game.yml`) or opens the arena menu if quick-join is off. | `br.play` |
| `/br join <arena>` | Joins the named arena. | `br.play` |
| `/br join <solo\|duo\|squad>` | Quick-joins the best available match of that team mode (needs quick-join on). | `br.play` |
| `/br leave` | Leaves the match or the queue. A party leader leaving the **queue** takes the party with them (never mid-match). Also stops `/br admin spectate`. | none |
| `/br menu` | Opens the arena selector. | `br.play` |
| `/br list` | Lists the arenas with mode, players and state. | `br.play` |
| `/br stats` | Your own statistics (level, coins, wins, kills, deaths, games, K/D). | `br.stats` |
| `/br stats <player>` | Another player's statistics (online, or offline if known to the server). | `br.stats.others` |
| `/br top [category]` | Top 10 leaderboard. Categories: `wins` (default), `kills`, `deaths`, `games`, `xp`, `level`, `coins`, `gulag` / `gulag_wins`, `winstreak`, `damage` / `damage_dealt`. Unknown categories show `wins`. | `br.top` |
| `/br shop` | Opens the cosmetics shop (glider trails, kill and victory effects). | `br.shop` |
| `/br spectate [next\|prev\|free\|<player>]` | Spectator camera for eliminated players: next / previous player (`prev` or `previous`), free camera (`free` or `stop`), or follow a named player. Without arguments = `next`. Alias: `/br spec`. | `br.spectate` |

### Party

All party commands need `br.party`.

| Command | Description |
|---|---|
| `/br party invite <player>` | Invites a player to your party (if you are already in a party, only its leader can invite). Not possible during a match. |
| `/br party accept [player]` | Accepts an invite (the inviter's name is needed only with several invites). |
| `/br party deny [player]` | Declines an invite. |
| `/br party kick <player>` | Removes a member (leader only). |
| `/br party leave` | Leaves the party. |
| `/br party disband` | Disbands the party (leader only). |
| `/br party info` | Shows the party members. |

### Events (players)

Alias: `/br events`. See [Events](../events.md).

| Command | Description | Permission |
|---|---|---|
| `/br event` | Opens the event menu (from the console: the event info). | `br.event` |
| `/br event join` | Registers you for the running event. Events may also require their own permission (`registration.permission` in `events.yml`). | `br.event` |
| `/br event leave` | Leaves the event. | `br.event` |
| `/br event info` | Information about the running event. | `br.event` |
| `/br event top` | Current event standings. | `br.event` |

## Arena setup: `/br setup`

All need **`br.admin.setup`**, must be run by a player, and cannot be used while you are in a match. See [Your first arena](../getting-started/first-arena.md) and [Per-arena settings](../getting-started/arena-settings.md).

### Sessions and arenas

| Command | Description |
|---|---|
| `/br setup` | Opens the list of arenas: click one to edit it. |
| `/br setup help` | Shows the setup usage line. |
| `/br setup guide` | Starts the step-by-step chat guide (clickable lines). |
| `/br setup guide stop` | Stops the guide. |
| `/br setup guide done <step>` | Marks a guide step as done (`mode`, `deploy`, `center`, `spawns`, `skydive`, `lobby`, `gulag`, `chests`, `airdrops`, `geysers`). Normally clicked from the guide. |
| `/br setup create <name>` | Starts a setup session for a new arena in your current world and opens the setup GUI. Names: letters, numbers, `_` and `-`. |
| `/br setup edit <name>` | Starts a session on an existing arena (several admins can join the same session). |
| `/br setup gui` | Opens the setup GUI of the current session. Alias: `/br setup menu`. |
| `/br setup clone <arena> <new-name>` | Copies an arena under a new name. |
| `/br setup rename <new-name>` | Renames the arena you are editing. |
| `/br setup delete <name>` | Deletes an arena. |
| `/br setup list` | Lists all arenas with world, players and what is missing. |
| `/br setup tp [arena]` | Teleports to the arena's map world (the arena you are editing if omitted). |
| `/br setup info` | Summary of the arena you are editing. |
| `/br setup markers` | Shows / hides the markers of the arena's points in the map (only you see them). |
| `/br setup save` | Saves the arena and closes the session. |
| `/br setup cancel` | Discards the changes of the session. |

### Arena settings (inside a session)

| Command | Description |
|---|---|
| `/br setup world <folder>` | Sets the template world (the world's **folder name**, not a Multiverse alias). |
| `/br setup mode <SOLO\|DUO\|SQUAD>` | Team mode. |
| `/br setup deploymode <SPAWNS\|SKYDIVE>` | How players enter the map. |
| `/br setup min <number>` | Minimum players. |
| `/br setup max <number>` | Maximum players. |
| `/br setup center <radius>` | Zone center at your position, with the given radius (half-width in blocks, greater than 0). |
| `/br setup lobby` | Dedicated waiting lobby of the arena at your position. |

### Points (inside a session)

| Command | Description |
|---|---|
| `/br setup spawn` | Adds a spawn at your position (SPAWNS mode). |
| `/br setup clearspawns` | Removes all spawns. |
| `/br setup skydive` | Adds a skydive launch point at your position (fly high above the map). |
| `/br setup skydive remove` | Removes the launch points near you. |
| `/br setup skydive clear` | Removes all launch points. |
| `/br setup chest` | Registers the chest you are looking at as a loot chest. |
| `/br setup removechest` | Removes the chest points near you. |
| `/br setup scanchests [radius]` | Finds every chest, trapped chest and barrel of the map (within the zone radius, or the given radius) and adds them as loot chests. |
| `/br setup clearchests [confirm]` | Removes every chest point of the arena (asks for `confirm`). |
| `/br setup mapwall [width height]` | SKYDIVE arenas: builds the spawn-picker map wall on the wall you look at (default 7×7) and draws the map. See [Spawn picker](../gameplay/map-wall.md). |
| `/br setup mapwall render\|remove` | Redraws the map of the wall / removes the wall. |
| `/br setup mapwall label add <name>\|remove [name]\|list\|clear` | Place names on the wall, at your position in the arena map. |
| `/br setup airdrop` | Adds an airdrop landing point. |
| `/br setup removeairdrop` | Removes nearby airdrop points. |
| `/br setup geyser` | Adds a geyser. |
| `/br setup removegeyser` | Removes nearby geysers. |

### Gulag points (inside a session)

See [Gulag](../gameplay/gulag.md).

| Command | Description |
|---|---|
| `/br setup gulag a` | Spawn A of the main duel ring (also the default with no argument). |
| `/br setup gulag b` | Spawn B of the main ring. |
| `/br setup gulag cleara` / `clearb` | Removes spawn A / B (the gulag stays off until both are set). |
| `/br setup gulag pair` | Extra ring for parallel duels: run it once at point A, then again at point B. |
| `/br setup gulag removepair` | Removes the extra rings near you. |
| `/br setup gulag clearpairs` | Removes all extra rings. |
| `/br setup gulag wait` | Adds a waiting point (where queued players watch). |
| `/br setup gulag removewait` | Removes nearby waiting points. |
| `/br setup gulag clearwait` | Removes all waiting points. |

## Match and server management

| Command | Description | Permission |
|---|---|---|
| `/br start [arena]` | Starts the match now (skips the wait for players). Alias: `/br forcestart`. | `br.admin.game` |
| `/br stop [arena]` | Stops a match (or countdown) without results. | `br.admin.game` |
| `/br forcejoin <player> [arena]` | Puts a player into an arena (arena optional when only one exists). In the lobby they join normally. In a match that started less than `game.force-join-seconds` ago (default 60, also in the setup GUI → Match rules) they join and **play**: their own team in Solo, a team with a free place in Duo/Squad, deployed like everybody. Later they join as a spectator. | `br.admin.game` |
| `/br kick <player>` | Removes a player from their match. | `br.admin.game` |
| `/br setlobby` | Sets the main hub at your position (enables hub items and protection there). | `br.admin.setup` |
| `/br admin` | Toggles build mode in the hub (removes the hub items, lets you build). | `br.admin.build` |
| `/br admin <tool> ...` | Live match tools, see [Admin tools](admin-tools.md). | `br.admin.match` |
| `/br reload` | Reloads configuration files, messages and arenas. | `br.admin.reload` |
| `/br debug` | Toggles debug logging in the console (until restart). | `br.admin.debug` |
| `/br debug report` | Writes a support report file. | `br.admin.debug` |
| `/br resetstats <stats\|all> [confirm <code>]` | Resets the statistics of **every** player, with confirmation code and automatic backup. | `br.admin.resetstats` |

!!! info "Which arena when `[arena]` is omitted?"
    The match you are in (or watching with `/br admin spectate`); otherwise the only arena; otherwise the only active one (match running, countdown or players queued). If that is still ambiguous, the plugin asks you to name the arena and lists the active ones.

### Match tools: `/br admin`

All need **`br.admin.match`**. Details and examples in [Admin tools](admin-tools.md).

| Command | Description |
|---|---|
| `/br admin info [arena]` | Live status of the match. |
| `/br admin spectate [arena]` | Watch a running match invisibly; run again (or `/br leave`) to stop. |
| `/br admin revive <player>` | Brings a player back into the match. |
| `/br admin eliminate <player> [nostats]` | Eliminates a player now (no gulag, no kill credit). |
| `/br admin end <player\|team number> [arena]` | Declares the winner and ends the match. |
| `/br admin zone <pause\|resume> [arena]` | Freezes / unfreezes the zone. |
| `/br admin skipzone [arena]` | Skips the wait: the next zone closing starts now. |
| `/br admin deathmatch [arena]` | Skips the remaining phases: final collapse now. |
| `/br admin airdrop [arena]` | Drops a supply crate now. |
| `/br admin refill [arena]` | Refills the match chests now. |
| `/br admin gulag <open\|close\|auto> [arena]` | Forces the gulag open / closed, or back to automatic. |
| `/br admin countdown <seconds> [arena]` | Sets (or starts) the start countdown (1-3600 s). |
| `/br admin team <player> <number>` | Moves a queued player to a team (DUO/SQUAD). |
| `/br admin broadcast <arena\|all> <message>` | Staff message to the players of one match or of all matches. |

### Economy

Need **`br.admin.economy`**. Work for offline players known to the server. Amounts cannot be negative; results are clamped at 0.

| Command | Description |
|---|---|
| `/br coins <give\|take\|set> <player> <amount>` | Changes a player's coins. |
| `/br xp <give\|take\|set> <player> <amount>` | Changes a player's XP (the level follows the XP). |

### NPCs (Citizens)

Need **`br.admin.npc`** and the Citizens plugin. See [Hub, NPCs & signs](../features/hub.md).

| Command | Description |
|---|---|
| `/br npc create play` | Creates an NPC at your position that opens the arena menu. |
| `/br npc create join <arena>` | Creates an NPC that joins the arena, with a live hologram. |
| `/br npc remove [id]` | Removes a linked NPC by id, or (no id) the NPC selected with Citizens' `/npc select`. |
| `/br npc list` | Lists the linked NPCs. |

`list` and `remove <id>` also work from the console.

### Join signs

Not a command: write `[br]` on the first line of a sign and the arena name on the second. Creating one needs **`br.admin.signs`**; using it needs `br.play`.

## Event management

Need **`br.admin.event`** (except the player commands above). See [Events](../events.md).

| Command | Description |
|---|---|
| `/br event list` | Lists the events defined in `events.yml`. |
| `/br event create <id>` | Creates a new event with default settings. |
| `/br event delete <id>` | Deletes an event. |
| `/br event edit [id]` | Opens the event editor GUI (list of events, or the given event). |
| `/br event setlobby <id>` | Sets the event lobby at your position. |
| `/br event schedule <id> [list]` | Shows the automatic start times. |
| `/br event schedule <id> add <time>` | Adds a start time: `2026-10-12 21:00`, `SAT 21:00` or `DAILY 21:00`. |
| `/br event schedule <id> remove <n>` | Removes start time number `n`. |
| `/br event start <id>` | Opens the registration of an event. |
| `/br event forcestart` | Closes the registration now (at least 2 players needed). |
| `/br event announce` | Broadcasts the registration announcement with the join button. |
| `/br event stop` | Cancels the event (no prizes, entry fees refunded). |
| `/br event end` | Ends the event now with the current standings and the prizes. |
| `/br event next` | The next match is played on the next map (or starts now when waiting). |
| `/br event map <arena>` | Forces the map of the next match. |
| `/br event pause` / `resume` | Pauses / resumes the event. |
| `/br event add <player>` | Registers a player (no fee or limits). |
| `/br event kick <player>` | Removes a player from the event. |
| `/br event points <player> <+n\|-n>` | Adds or removes points. |
| `/br event undo [confirm\|replay]` | Cancels the results of the last finished match of the event. The first call shows what would be undone; confirm within 60 s. `replay` also replays the same map next. Can be repeated for earlier matches. |

## Console

Commands that act on a player (`join`, `leave`, `menu`, `shop`, `spectate`, `party`, `setup`, `setlobby`, `/br admin` alone, `/br admin spectate`, `npc create`, `event join/leave/edit/setlobby`) need a player. Everything else (start, stop, forcejoin, kick, coins, xp, reload, debug, resetstats, the other `/br admin` tools, event management) works from the console.
