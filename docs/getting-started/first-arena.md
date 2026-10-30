# Your first arena

An **arena** is a map plus everything the plugin needs to play on it: the zone, where players start, the loot chests, the gulag and so on. Everything is set up in game, with no file editing: either with the **chat guide** or with the **setup GUI** (you can mix the two).

All setup commands need the `br.admin.setup` permission and can't be used while you are inside a match.

## 1. Prepare the map world

1. Build (or download) your map in **its own world**, for example `br_forest`.
2. Make sure that world is loaded on the server (for example with Multiverse-Core), so you can walk in it.
3. Teleport into it.

This world is the arena's **template**. Every match is played on a **throw-away copy** of it: players can't damage the original, and the copy is deleted when the match ends.

!!! warning "Use the real map world"
    Copies made for matches are called `br_<arena>_<n>`. Never set up an arena while standing in one of them: the plugin refuses, because that world is deleted at the end of the round.

!!! tip "What is the template name?"
    The arena stores the world's **folder name** in the server directory, not a Multiverse alias. When you create the arena it takes the world you're standing in; you can change it later with `/br setup world <folder>`.

## 2. Choose how players enter the map

Each arena has a **deploy mode**:

| Mode | How the match starts | What you need |
|---|---|---|
| `SPAWNS` | Each player is teleported to a ground spawn and stays frozen for the grace period (`game.grace-period-seconds`), then the fight begins. | **One spawn per player.** The number of spawns is also the real maximum of players. |
| `SKYDIVE` | Everyone appears high above the map with a locked glider (elytra) and glides down, choosing where to land. Fall damage is off until landing and the glider is removed on the ground. | Optional **launch points**. Without any, players drop from above the zone centre at `deploy.skydive-default-height` (default Y 180). |

In `SKYDIVE` mode you can add **several launch points** (up to 16): players are split between them, each team stays together and every point gets about the same number of players. Gulag winners are relaunched from the point closest to the zone. Set launch points while **flying high in the map world**; your facing direction is saved too (look slightly down for a nice start).

Any arena can also get a **map wall** in its waiting lobby. In SKYDIVE arenas players click the point they want to drop on; in SPAWNS arenas it only shows the map: see [Map wall](../gameplay/map-wall.md).

## 3. Set it up

=== "Chat guide (easiest)"

    Run:

    ```text
    /br setup guide
    ```

    The guide tells you what to do next, with **clickable** commands in chat (text in `[brackets]` can be clicked). It follows the real state of the arena, so you can also use the GUI or commands in between.

    1. **Create or edit the arena.** Click `[/br setup create name]` and type a name (letters, numbers, `_` and `-`), or edit an existing one. The map is the world you are in.
    2. **Team mode:** `[SOLO]`, `[DUO]` or `[SQUAD]`.
    3. **Deploy mode:** `[SPAWNS]` or `[SKYDIVE]`.
    4. **Zone centre:** stand in the middle of the map and run `/br setup center <radius>`. The radius is the half-width in blocks: `150` means a 300×300 area. This step can't be skipped.
    5. **Spawns** (SPAWNS mode): stand on each spot and click `[Add spawn]`. You need at least 2 (and at least the minimum players), ideally one per player.<br>
       **Launch points** (SKYDIVE mode): fly to each spot and click `[Add drop point]`, or `[Next]` to drop from above the centre.
    6. **Waiting lobby** (optional): `[Set lobby]`, or `[Skip]` to use the global hub.
    7. **Gulag** (optional): set duel spot **A**, then spot **B** facing A, then add waiting spots for the queue. Or `[Skip gulag]`.
    8. **Chests:** look at each loot chest and click `[Add chest]`.
    9. **Airdrops** (optional): look at a block where supply drops can land and click `[Add drop point]`.
    10. **Geysers** (optional): look at a block that launches players into the air.
    11. **Save:** click `[Save arena]`.

    When you save, the guide tells you whether the arena is ready or what is still missing. Stop it any time with `/br setup guide stop`.

=== "Setup GUI"

    Create the arena while standing in the map world:

    ```text
    /br setup create <Name>
    ```

    The setup GUI opens on its own (reopen it any time with `/br setup gui`). The top item shows whether the arena is **playable** and what is missing; each category is green when done and yellow when it still needs attention.

    1. **General** – team mode (click: SOLO → DUO → SQUAD), deploy mode (SPAWNS ↔ SKYDIVE), min/max players.
    2. **Zone & Geometry** – stand in the middle of the map and click **Zone centre & radius**. To change the radius use `/br setup center <radius>`. Zone phases and damage can be tuned here too.
    3. **Spawns & Launch** – **Add spawn** at your position (SPAWNS) or **Launch points** (SKYDIVE: left click adds a point, right click removes the ones within 15 blocks, ++shift++ + click removes them all).
    4. **Loot & Features** – **Add chest** (the chest you are looking at), airdrop points, geysers and loot settings.
    5. **Gulag** – duel spots A and B, extra rings and waiting points (see below).
    6. **Dedicated lobby** (optional) – where players wait for this arena.
    7. Click **SAVE AND CLOSE**.

    Every option in these windows is explained in [Per-arena settings](arena-settings.md).

=== "Commands only"

    Everything can also be done with `/br setup` sub-commands while a setup session is open:

    ```text
    /br setup create Forest
    /br setup mode solo
    /br setup deploymode spawns
    /br setup center 150          # stand in the middle of the map
    /br setup spawn               # repeat on every spawn spot
    /br setup chest               # look at each chest
    /br setup gulag a             # optional gulag: spot A
    /br setup gulag b             # spot B, facing A
    /br setup gulag wait          # waiting spots for the queue
    /br setup save
    ```

    The full list is in [Commands](../reference/commands.md).

### Placing chests

Register chest points **on the chests themselves**, looking at the chest block. Each match uses only some of the points (by default 3 per player, at least 10, spread over the map) and the unused points become **air** in that match's copy. Add more points than you need. What's inside the chests comes from `loot.yml`: see [Loot, chests & airdrops](../gameplay/loot.md).

!!! tip "Map already full of chests? Find them automatically"
    `/br setup scanchests` (GUI: **Loot & Features → Find chests automatically**) scans the map within the zone radius and registers every **chest, trapped chest and barrel** it finds as a loot chest, both halves of double chests included. Chests already registered and airdrop points are left alone. The scan loads the map a few chunks at a time (never generating new terrain), shows its progress in the action bar and reports how many chests it added. Give a radius to scan a different area: `/br setup scanchests 300`. Then **save**.

    The container types and the scan speed are in `config.yml` → `setup.chest-scan` (`types`, `chunks-at-once`). To start over, `/br setup clearchests confirm` (GUI: **Remove all chests**, ++shift++ + click) removes every chest point.

### Setting up the gulag

The gulag gives eliminated players a 1v1 second chance. In the **Gulag** window:

- **Duel spot A / B** – left click sets it at your position, right click teleports you there, ++shift++ + click removes it. Without both A and B the gulag is off for this arena.
- **Extra duel rings** – more rings mean more duels at the same time (useful in DUO/SQUAD). First click sets the ring's A, the second click its B.
- **Waiting points** – where queued players watch the duel (face toward it).

Build the gulag **in the map world**, away from the playable area (the example arenas put it a few hundred blocks outside the zone), and keep each ring apart from the others. More in [Gulag](../gameplay/gulag.md).

## 4. See your points: setup markers

While you edit an arena, every **chest, airdrop, geyser, spawn, launch point and gulag spot** is shown in the map as a coloured glass block that glows through walls, with a name tag such as *Chest #12*.

- Only you see them, and only the points within 96 blocks are shown.
- They are never saved into the map.
- The gulag **anti-AFK area** around each duel spot is drawn with red particles.
- Turn them off/on with `/br setup markers` or the eye button in the bottom-left corner of the setup GUI.

## 5. Save

Click **SAVE AND CLOSE** in the GUI, or run `/br setup save`. The arena is written to `plugins/MRTBattleRoyale/arenas/<Name>.yml`.

An arena is **playable** when it has:

- a zone **centre and radius** (radius greater than 0);
- at least one **spawn** in `SPAWNS` mode (not needed in `SKYDIVE` mode);
- a minimum players value that is not higher than the maximum (in `SPAWNS` mode the maximum is also limited by the number of spawns).

!!! note "Unfinished arenas are saved too"
    You can save halfway: the arena is written to disk, but it stays out of play (nobody can join it) until it is complete. Continue later with `/br setup edit <Name>`.

If a match is running on that arena while you save, the changes are applied when that match ends.

!!! warning "Unsaved changes"
    Nothing is written until you save. **CANCEL** (or `/br setup cancel`) discards the session, and so does leaving the server while you are the only one editing it.

## Managing your arenas

Run `/br setup` with no arguments to open the list of **all arenas**, with their mode, players, world and what's missing:

- **Left click** an arena to edit it.
- **Right click** to teleport to its map.
- **Create arena** reminds you of the command to create a new one.
- If you have an edit in progress, a **Resume** button takes you back to it.

Other useful commands:

| Command | What it does |
|---|---|
| `/br setup edit <name>` | Edit an existing arena (opens the GUI) |
| `/br setup clone <arena> <new-name>` | Copy an arena, for example to make a DUO version of a SOLO map |
| `/br setup rename <new-name>` | Rename the arena you are editing (the map world is not touched) |
| `/br setup delete <name>` | Delete an arena (the map world is not touched) |
| `/br setup list` / `info` | List the arenas / summary of the one you are editing |
| `/br setup tp [arena]` | Teleport to an arena's map |

Several admins can edit the same arena together: `/br setup edit` on an arena someone is already editing joins their session.

## Play!

Players join with `/br join`, the arena menu (`/br menu` or the compass in the hub), a join sign or an NPC. See [How a match works](../gameplay/match.md) and [Hub, NPCs & signs](../features/hub.md).

!!! tip "Prefer files?"
    You can also write an arena by hand: copy one of the `.yml.example` files in `arenas/`, rename it to `<Name>.yml`, put your coordinates in and run `/br reload`. The format is explained in [Per-arena settings](arena-settings.md#the-arena-file).
