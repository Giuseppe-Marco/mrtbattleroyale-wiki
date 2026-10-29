# Spectators

Eliminated players don't have to leave: they stay and **watch the rest of the match**. Players who join a match that has already started also become spectators (an admin's `/br forcejoin` within `game.force-join-seconds` of the deploy makes them play instead, see [How a match works](match.md#1-lobby-and-queue)).

## Where you appear

When you are eliminated you stay **where you died**, floating just above the spot (*SPECTATOR*). If that spot is unusable (the void, the gulag) you appear above a living player instead, and only as a last resort above the zone centre.

## The free camera

As a spectator you fly freely, invisible to the living players, and you get three items:

| Item | Use |
|---|---|
| **Spectate** (compass) | Right click: list of living players, click one to teleport to them. Left click: jump to the next living player |
| **Follow a player** (ender eye) | Start the follow camera (see below) |
| **Leave** (red bed) | Leave the match (same as `/br leave`) |

Spectators:

- can't touch blocks, open chests or crates, pick up or drop items, or hurt anyone;
- see each other, but the living players never see them;
- are kept inside the zone border like everyone else.

## The follow camera

Like in Fortnite, you can watch the match **through the eyes of a living player**, in first person, with their hotbar and health.

- Start it with the **ender eye**, `/br spectate`, or let it start by itself (auto-follow).
- ++shift++ = **next player**.
- Clickable chat buttons: **[◀ Previous]**, **[Next ▶]**, **[Free camera]**.
- An action bar shows the followed player's name, health (or *DOWN*), kills and how many are alive.

Teammates come first in the list, then everyone else by name. Players in the gulag can't be followed.

**Automatic camera.** A few seconds after your elimination the camera starts by itself: a living **teammate** first, then **whoever eliminated you**, then the **nearest** player.

**When the followed player is eliminated**, the camera moves to their killer, or to the next player. With nobody left to watch you go back to the free camera. At the end of the match everyone goes back to the free camera.

### Commands

| Command | What it does |
|---|---|
| `/br spectate` or `/br spectate next` | Follow the next player |
| `/br spectate prev` | Follow the previous player |
| `/br spectate free` | Back to the free camera |
| `/br spectate <player>` | Follow a specific living player of your match |

These need the `br.spectate` permission (everyone by default). See [Commands](../reference/commands.md).

### Settings

```yaml
spectator:
  follow: true
  auto-follow: true
  hud: true
```

| Key | Meaning |
|---|---|
| `follow` | Enables the follow camera. `false` = only the free flying camera (and no ender eye) |
| `auto-follow` | The camera starts by itself after an elimination |
| `hud` | Action bar with the followed player's name, health and kills |

!!! note
    The `spectator` section is global: it can't be changed per arena.

## Spectator chat

Spectators have their **own chat channel**, marked `[SPECTATOR]`: only other spectators of the same match read it. This way a dead player can't call out enemy positions to the living.

Spectators still read what the living players write. Once the match is over, everyone talks together again. Details in [Chat](../features/chat.md).

## Admin spectate

Admins can watch any running match without playing in it:

```
/br admin spectate [arena]
```

- You are put in spectator mode in that match, **invisible to everyone**, and you are not counted as a player (no zone, no boss bar, no stats).
- You see the match like its spectators do.
- Stop with the same command again or `/br leave`: your own inventory and position are given back. This also happens if the match ends.
- You can't join a match while watching one.

More admin tools in [Admin tools](../reference/admin-tools.md).
