# Hub, NPCs & signs

The **hub** (or BR spawn) is where players arrive, pick a match and come back after it. MRTBattleRoyale gives it a set of hotbar items, an arena menu, protection, and optional NPCs and join signs.

All hub settings are in `plugins/MRTBattleRoyale/lobby.yml`.

## Setting the hub

Stand where players should spawn and run:

```
/br setlobby
```

This saves your position (world, coordinates, yaw and pitch) in `lobby.yml`. Players are sent there when they leave a match or the queue.

!!! warning "Nothing works until you set it"
    Until you run `/br setlobby`, the hub world is **empty**: teleports fall back to the main world's spawn, but the **hub items and the protection are not active anywhere**. They only ever work in the world of the lobby you set, so the plugin never touches your survival or creative worlds.

    Don't type a world into `lobby.yml` by hand unless it really is your hub: if you write `world` and that is your survival world, everyone entering it becomes invulnerable and gets the compass.

!!! note "/br setlobby vs /br setup lobby"
    `/br setlobby` sets the **main hub**. `/br setup lobby` is a different thing: it sets the waiting lobby of a single arena during [arena setup](../getting-started/first-arena.md).

## lobby.yml

```yaml
lobby:
  world: ''
  x: 0.5
  y: 64.0
  z: 0.5
  yaw: 0.0
  pitch: 0.0

  # Teleports anyone joining the server to the hub. Players rejoining a
  # match in progress (within rejoin-grace-seconds) stay where they were.
  spawn-on-join: false

  # Protects whoever is in the hub: no breaking, placing, damage, hunger
  # or item drops, and no PvP in any direction.
  protect: true

  # Y level below which a hub player is sent back to the spawn.
  void-level: -100

  # Gives the hub items (arena menu, party, stats, shop) in the hub world.
  hub-items: true

  # Clears the inventory when the hub items are given. Enable it ONLY on a
  # dedicated hub where players must not carry anything else.
  clear-inventory-in-hub: false

  # Hotbar slots (0-8) of the other hub items.
  items:
    party-slot: 0
    stats-slot: 7
    shop-slot: 8

  # Compass that opens the arena menu.
  compass:
    enabled: true
    give-on-join: true
    slot: 4

items:
  party: PLAYER_HEAD
  stats: BOOK
  shop: ENDER_CHEST
  leave: RED_BED
  teamselect: WHITE_BANNER
  spectate: COMPASS
  follow: ENDER_EYE
```

`protect`, `void-level` and `hub-items` work **only** in the hub world.

## Hub items

With `hub-items: true`, players in the hub world get these items in their hotbar:

| Item (default) | Slot | What it does |
|---|---|---|
| Compass (**Play**) | `compass.slot` (4) | Opens the [arena menu](#arena-menu) |
| Player head (**Party**) | `items.party-slot` (0) | Shows your party (`/br party info`) |
| Book (**Your stats**) | `items.stats-slot` (7) | Opens your statistics screen |
| Ender chest (**Cosmetics**) | `items.shop-slot` (8) | Opens the [cosmetics shop](progression.md#cosmetics-shop) |

Good to know:

- Leaving the hub world removes the items again, so they never take up slots in other worlds.
- Hub items can't be dropped, moved around the inventory or swapped to the off hand.
- If a slot holds one of the player's own items, that item is moved to a free slot, never deleted. If two hub items share a slot, the first free slot is used.
- By default the rest of the inventory is left as it is. Set `clear-inventory-in-hub: true` only on a dedicated hub server.
- Items are recognised by an internal tag, not by their material: you can change the materials freely in the `items:` section. Their names and lore are in the messages file (`lobby` section).

The `items:` section also sets the look of the match items: `leave` (leave the queue), `teamselect` (team selector in DUO/SQUAD lobbies), `spectate` and `follow` (spectator tools, see [Spectators](../gameplay/spectators.md)).

## Arena menu

The arena menu opens with the hub compass, `/br menu` or a **play** NPC. Each arena shows its state at a glance:

- **green**: open, you can join (it also says how many players are still needed);
- **yellow**: countdown running;
- **red**: match in progress;
- **grey**: arena not ready (not fully set up).

Arenas reserved by a running [event](../events.md) are not listed.

### Solo / Duo / Squad split

With `arena-menu.by-mode: true` in `config.yml` (the default), the menu has two steps:

1. **Mode choice**: Solo, Duo and Squad, each with how many arenas it has and how many players are playing or waiting.
    - **Left click**: the arenas of that mode, with pages.
    - **Right click**: **quick join**, straight into the best open lobby of that mode.
2. **Arenas of the mode**, with a **Quick join** button in the bottom bar and a back arrow.

The mode step is skipped when all your arenas have the same mode. While an event is running, the mode screen also shows an **event** button that opens the event menu.

```yaml
arena-menu:
  # true = first the mode (Solo / Duo / Squad, right click = join now),
  # then the arenas of that mode, with pages. Skipped when every arena has
  # the same mode. false = one list with every arena.
  by-mode: true
```

Quick join uses the same matchmaking as `/br join solo|duo|squad`.

## NPCs (Citizens)

With the **Citizens** plugin installed you can place clickable NPCs. Commands need `br.admin.npc`.

| Command | What it does |
|---|---|
| `/br npc create play` | Creates an NPC at your position that opens the arena menu |
| `/br npc create join <arena>` | Creates an NPC that joins that arena directly |
| `/br npc remove` | Removes the NPC you have selected in Citizens |
| `/br npc remove <id>` | Removes a linked NPC by its id (works from the console) |
| `/br npc list` | Lists the linked NPCs and their action (works from the console) |

**Join NPCs** have a live hologram above their head with the map, the mode, the players (`players/max` and the state) and, while waiting, how many players are still missing. It updates every second. Its lines are in the messages file (`npc` section).

!!! info "How NPCs are stored"
    The NPCs themselves belong to Citizens: MRTBattleRoyale only stores the link (NPC id → action) in `npcs.yml`. `/br npc remove <id>` refuses NPCs that are not linked, so it can never delete your shopkeepers or quest NPCs. If you delete a linked NPC through Citizens, the link is cleaned up automatically.

Without Citizens, `/br npc` just tells you that Citizens is missing. Use [join signs](#join-signs) instead.

## Join signs

A join sign works without any other plugin. Place a sign and write:

| Line | Text |
|---|---|
| 1 | `[BR]` |
| 2 | the arena name |

Creating one needs `br.admin.signs` (without it, the first line is cleared and the sign is just a normal sign). Right-clicking a join sign is the same as `/br join <arena>`. Signs keep working even if a protection plugin (WorldGuard, spawn protection) blocks block interaction in your hub.

## Hub protection

With `protect: true`, players in the hub world (and not in a match) are protected:

- no breaking or placing blocks, no buckets;
- no damage, no hunger, no item drops;
- no PvP in either direction;
- no using doors, trapdoors, gates, containers, beds, item frames, armor stands and similar. **Buttons still work** (handy for parkour and other hub features);
- no trampling farmland or eggs.

With `void-level`, a hub player who falls below that Y level is teleported back to the hub spawn.

## Build mode (/br admin)

Admins are protected like everyone else when they walk through the hub, so a stray click can't damage your spawn. To build or decorate the hub, toggle **build mode**:

```
/br admin
```

(no arguments, needs `br.admin.build`).

While build mode is on:

- you are exempt from the hub protection;
- you get **no hub items**, so your building inventory is not replaced on every join or teleport;
- your inventory is never cleared, whatever `clear-inventory-in-hub` says.

Build mode is saved on your player, so it stays on after a relog or a restart until you run `/br admin` again. Turning it off gives you the hub items back.

!!! tip
    `/br admin` **with** arguments is a different set of tools (match control: revive, zone, airdrop...). See [Admin tools](../reference/admin-tools.md).
