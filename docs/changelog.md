# Changelog

Full version history: [Modrinth versions](https://modrinth.com/plugin/mrtbattleroyale/versions).

## 2.1.0 — Events, spectators & much more

The biggest update so far. Configs and arenas are upgraded automatically: replace the jar and **restart** the server (no hot reload). 📖 New **[wiki](index.md)** with every feature, command, permission and placeholder.

### ⚠️ Important changes
- **All commands are now only under `/br` (or `/battleroyale`).** The shortcuts `/join`, `/leave`, `/menu`, `/stats`, `/top`, `/party`, `/shop` (and their aliases) and the `/brgame` alias were removed, so the plugin never takes commands away from your other plugins. Check `commands.allowed` in `config.yml`: the old entries `join`, `leave`, `esci`, `party`, `p` now allow *other plugins'* commands in a match — remove them if you don't want that.
- **Chat:** new `chat.scope` (default `br`) — the plugin handles only the Battle Royale chats (BR spawn, each match, spectators, event lobby) and leaves the rest of the server's chat untouched. `scope: server` = the old behaviour.
- **Permissions split per area** (`br.play`, `br.stats`, `br.top`, `br.party`, `br.shop`, `br.spectate`, `br.event`… and `br.admin.setup`, `br.admin.game`, `br.admin.match`, `br.admin.event`…). `br.player` and `br.admin` groups, `br.*` = everything. Help and tab completion only show what you may use.
- Admins **playing** a match now follow the map's building rule too.
- Config files reorganised: `languages/` folder, `effects.yml` merged into `game.yml`, `stats.yml` into `config.yml`, hub items into `lobby.yml` — all moved automatically.

### 🏆 New: Events
- Tournaments of matches in a row for registered players: registration with a clickable [JOIN], boss bar, **event lobby** from the registration on, own chat and scoreboard.
- **Map rotation** every N minutes, or **random map draw**: in the last seconds of the break everyone sees a spinning reel of the maps that stops on the drawn one (only free maps, fitting the players online, never the same twice in a row, less-played maps favoured).
- Points (placement + kills), final standings, podium, prizes (commands and coins), history files.
- Custom rules switch, players per match, late join, entry fee, **automatic schedule** (date / weekly / daily, timezone, announcements).
- `/br event undo [confirm|replay]` cancels the results of the last match (and can replay the same map).
- Editor GUI (`/br event edit`), full admin commands, placeholders for holograms (`event_top_<n>_…`, `event_last_…`, `event_next_…`).

### 👀 New: spectators & end of match
- **Fortnite-style spectator camera**: watch living players, Shift = next, clickable buttons, auto-follow (teammate, then the killer), `/br spectate`.
- Spectators now appear **where they died** (no more landing on the waiting-lobby platform).
- **End-of-match titles per player** (VICTORY / DEFEAT / MATCH OVER) and an **end-of-match summary**.

### 📊 New: scoreboards & colours
- The plugin draws its **own sidebar scoreboard** for hub, lobby, countdown, match, gulag, spectating, end screen and events (texts in the language files; works alongside TAB).
- **Solo: a unique hex colour for every player** — a marker before the name and the tab name in that colour; 16 different base colours for names. Team colours in Duo/Squad.
- New placeholders: `name_color`, `name_hex`, `name_color_hex`, `solo_marker`, `in_match` and many event ones.

### ⚔️ Gulag
- **Anti-AFK:** a 3×3 area around each duel spawn — whoever never leaves it during the duel loses, the active player wins; both AFK = both out. Warning in the last seconds; area shown with particles while editing.
- **Always protected:** nobody can break or build in the gulag arena (also on building maps), explosions and fire can't damage it; players in the gulag can't build at all.
- New setup windows: duel spots, extra rings, waiting points, **rules**, **duel kit** and **return kit** (saved from your inventory), back into the match.

### 🧱 Building maps
- Placed blocks **fall apart after a while** (`game.placed-blocks.decay-seconds`, default 60) with a crack animation; **cobwebs** have their own time (default 15 s); liquids from buckets too.
- On maps with building off, blocks, cobwebs, water/lava buckets and dispensers are always blocked.
- A **"BUILDING ON"** title while gliding, on maps with building enabled.

### 🛠️ Setup
- **Spawn picker map wall** (SKYDIVE arenas): `/br setup mapwall` builds a wall of maps in the waiting lobby showing the arena from above; players click a point to choose where they drop and are launched above it. Place names can be added (`/br setup mapwall label add <name>`), shown like named banners. Each player sees their own pin, party leaders choose for their party, the choice locks 5 s before the start, no choice = the usual launch points. Not used in events.
- `/br setup` opens the **list of arenas** (click to edit, right click to teleport).
- **Find chests automatically:** `/br setup scanchests [radius]` (GUI: Loot → Find chests) registers every chest, trapped chest and barrel already built in the map as a loot chest — ideal for downloaded maps full of chests. `/br setup clearchests` removes them all.
- **Step-by-step chat guide** (`/br setup guide`).
- **Markers in the map** while editing (chests, airdrops, geysers, spawns, launch points, gulag spots).
- **Several skydive launch points** per arena, players split between them, teams together.
- Almost every option of `game.yml` is now in the GUI, per arena: match rules, knockdown, zone phases and movement, launch, loot settings, **chest sound** (own window), airdrops, geysers.
- Arena menu split by **Solo / Duo / Squad** with quick join.

### 📦 Loot
- Chests **scale with the players** (`loot.chests`: per-player or all, spread over the map), named loot tables per arena, `loot.refill-seconds` works.
- **Chest sound:** players near a chest that still has loot hear it (radius, sound, pitch, volume; optional stop when opened).

### 🧰 Admin tools
- `/br admin` match tools: `info`, `spectate`, `revive`, `eliminate`, `end`, `zone pause|resume`, `skipzone`, `deathmatch`, `airdrop`, `refill`, `gulag open|close|auto`, `countdown`, `team`.
- `/br resetstats <stats|all>`: reset everyone's statistics with a confirmation code and an **automatic backup** (refused during a match). Permission `br.admin.resetstats` (op only, not in `br.admin`).
- `/br forcejoin` within `game.force-join-seconds` (default 60) of the start puts the player into the running match as a **player** (own team in Solo, a free place in Duo/Squad, deployed normally); later as a spectator.
- `/br debug report`: a report file for support (passwords hidden).
- Update checker (Modrinth).
- `messages.command-label`: texts show `/br`, or `/battleroyale` when another plugin owns `/br`.

### 🐞 Fixes
- Holes (empty chunks) in the match map when the template had just been saved.
- Duo/Squad placements now per team; teams fill evenly and parties stay together.
- Match items left on the cursor or crafting grid no longer come back after the match; safer inventory restore.
- Players disconnecting together can no longer hand an offline player the win.
- Losers no longer see the winner's victory title.
- Deploy teleport blocked by another plugin is retried; no geyser launches in the final showdown; a launch that never lifts off ends after 3 s.
- The closed-zone red ring no longer shows to spectators or when the zone hasn't started.
- Plugin fireworks do no damage; chest sound correct for double chests.
- Setup GUI: no double clicks, kits keep every stack, rename never publishes unsaved edits, incomplete arenas get no game.
- Events: back to the right lobby when leaving, no double fees, summer-time safe schedule, thread-safe placeholders.
- MySQL connect timeout; broken config files are never overwritten.

### ❌ Removed
- `/br bot` (test bots) and the plugin's private messages (`/msg`, `/r`).
- The top-level command shortcuts (see *Important changes*).
