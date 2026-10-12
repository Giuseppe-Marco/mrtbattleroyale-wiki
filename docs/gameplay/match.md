# How a match works

Every match goes through the same phases:

```
WAITING → COUNTDOWN → DEPLOYING → ACTIVE → DEATHMATCH → ENDING → reset
```

Each match is played on a **temporary copy** of the arena's map. The original map (the template) is never touched, and the copy is deleted when the match ends.

!!! tip "Per-arena values"
    All the `game.*` values on this page live in `game.yml` and can be changed for a single arena, either from the setup GUI (**Match rules** category) or in the arena's `settings:` section. See [Per-arena settings](../getting-started/arena-settings.md).

## 1. Lobby and queue

Players join with `/br join`, the arena menu (`/br menu` or the hub compass), a `[BR]` sign or an NPC (see [Hub, NPCs & signs](../features/hub.md)).

- `/br join` without an arena joins the **fullest available match** when `quick-join` is on; `/br join solo|duo|squad` does the same for one mode. With `quick-join: false` it opens the menu instead.
- Waiting players are sent to the arena's own lobby, or the global lobby (`/br setlobby`) when the arena has none.
- In the waiting lobby players get a **Cosmetics** item, a **stats** book and a red **Leave** bed. In DUO/SQUAD they also get the **Choose Team** banner (see [Teams](teams.md)).
- A party leader brings the whole party into the same match.
- The action bar shows `Waiting for players... 3/4` until `min-players` is reached.
- Joining a match that has **already started** makes you a spectator.

The maximum number of players is the arena's own `max` (or `game.max-players`). In `SPAWNS` mode it is also capped by the number of spawns: every player needs one.

## 2. Countdown

When `min-players` is reached (and `auto-start` is on) the countdown starts. It is announced in chat and with a big title at every multiple of 30 seconds, at 15, at 10 and on each of the last 5 seconds.

- If players leave and the count drops below the minimum, the arena goes back to waiting.
- An admin can force a start with `/br start` (a 5 second countdown that ignores the minimum) or change the countdown with `/br admin countdown <seconds>`.

When the countdown ends, teams are formed. If everyone ended up on **one** team (one big party, or all players picking the same colour) the teams are split automatically. A match with only one team never starts: the players stay in the lobby until someone else joins.

```yaml
game:
  min-players: 2
  max-players: 10
  countdown-seconds: 30
  auto-start: true
  quick-join: true
```

## 3. Deploy

The arena's **deploy mode** decides how players arrive on the map. Change it in the setup GUI (**General → Deploy mode**) or with `/br setup deploymode <spawns|skydive>`.

=== "SPAWNS"

    Every player is teleported to a spawn point and **frozen** there for the grace period: you can look around, but you can't walk or jump. The title says *Get ready*.

=== "SKYDIVE"

    Every player appears high in the sky wearing a **locked elytra** and starts gliding right away (*DROP! Glide and choose where to land!*).

    - **Several launch points**: an arena can have up to 16. Players are **split between them**: each team stays together on one point, and every point gets about the same number of players. Ties are random, so the points change every match.
    - **No launch point**: everyone drops above the zone centre, at `deploy.skydive-default-height` (always at least 60 blocks above the ground there).
    - No fall damage while gliding and for a moment after landing.
    - Gliding players can't hit anyone.
    - The glide ends when you touch the ground, water or a ladder/vine, or after `skydive-max-seconds`. The locked elytra is then removed and any chestplate you wore before is given back.

    Add launch points in **Spawns & Launch → Launch points (skydive)** (fly high above the map and left click) or with `/br setup skydive`.

```yaml
deploy:
  skydive-default-height: 180
  remove-elytra-on-land: true
  skydive-max-seconds: 60
```

The skydive values are in the setup GUI under **Spawns & Launch → Launch settings**.

## 4. Grace period

The deploy lasts `grace-period-seconds` (counted once the map copy is loaded). During it:

- in `SPAWNS` mode players are frozen on their spawn;
- hunger does not drain and nobody can build;
- team colours and the teammate glow are already active, so you can spot your squad while gliding.

When it ends, everyone sees **GO! Loot, survive, win!** and the match becomes active.

```yaml
game:
  grace-period-seconds: 10
```

## 5. Active phase

This is the real match:

- the [zone](zone.md) starts its phases and its boss bar appears;
- [chests](loot.md) are full, airdrops start falling after `supply-drop.first-delay-seconds`;
- hunger drains normally;
- killed players may go to the [gulag](gulag.md) for a second chance;
- in DUO/SQUAD lethal damage [knocks you down](teams.md#knockdown-and-revive) instead of killing you.

Other match rules:

| Setting | Default | What it does |
|---|---|---|
| `drop-loot-on-death` | `true` | Eliminated players drop their inventory |
| `combat-tag-seconds` | `15` | The last attacker gets the kill if the victim dies of a fall, the zone or anything else within this time |
| `world-time` | `6000` | Fixed time of day of the match world (6000 = noon) |
| `block-teleport-items` | `false` | Blocks ender pearls and chorus fruit during the match |

Other commands are blocked during a match so nobody can teleport away (see `commands` in `config.yml` and [Commands](../reference/commands.md)).

## 6. Building

By default players **can't** place or break blocks. Turn it on per arena with **Match rules → Building** or:

```yaml
game:
  allow-building: false
  placed-blocks:
    decay-seconds: 60
    cobweb-seconds: 15
    crack-seconds: 3
```

When building is on, players see a **BUILDING ON** title right after the drop, plus a chat line.

### Temporary blocks

Blocks placed by players **don't stay forever**:

- a normal block disappears after `decay-seconds`;
- a **cobweb** has its own, usually shorter time: `cobweb-seconds` (a trap, not a wall);
- water, lava and powder snow poured with a bucket are temporary too;
- in the last `crack-seconds` the block shows the **crack animation**, so nobody is surprised when their wall breaks.

`0` means the block stays. These two values are in **Match rules → Temporary blocks / Temporary cobwebs** (shown only with Building on).

### When building is always blocked

Even with building on, blocks can't be placed or broken:

- before the fight starts (lobby, deploy) and after it ends;
- by players in the gulag;
- by **anyone** inside the gulag area. The gulag is always protected, also from explosions and fire. See [Gulag](gulag.md#area-protection).

Buckets and dispensers follow the same rules. Admins playing a match follow them too.

## 7. Geysers

Geysers are spots on the map that launch you into the air: step on one and you are thrown upwards, then you glide down wherever you want (*WOOSH!*).

- A column of white particles marks each geyser (visible within 48 blocks).
- They only work during the active phase, not in the deathmatch.
- 3 seconds cooldown per player. Downed players can't use them.

Add geysers in **Loot & Features → Geysers** (also launch power, marker height and on/off) or with `/br setup geyser` / `/br setup removegeyser`.

```yaml
geyser:
  enabled: true
  power: 2.5
  glide-delay-ticks: 8
  marker: true
  marker-height: 20
```

## 8. Deathmatch and anti-stall

When the last zone phase is over, the match enters the **deathmatch** (*FINAL SHOWDOWN*, purple boss bar). The gulag closes for good.

To avoid endless matches (camping, running in circles), after `deathmatch-timeout-seconds` the zone starts closing again until it is **completely closed**, in `deathmatch-collapse-seconds` (*THE ZONE IS CLOSING*). At that point nowhere is safe.

```yaml
game:
  deathmatch-timeout-seconds: 60
  deathmatch-collapse-seconds: 20
```

`deathmatch-timeout-seconds: 0` disables the anti-stall. Both values are in **Match rules** (*Deathmatch duration*, *Final collapse*). Admins can trigger the collapse at once with `/br admin deathmatch`.

!!! note
    With the default phases the last phase already closes the zone completely (`target-radius: 0`), so the deathmatch is fought with zone damage everywhere. Give the last phase a radius above 0 if you want a small safe area during the deathmatch. See [Zone](zone.md).

## 9. End of the match

The last player or team still in play wins. Players waiting in the gulag still count as "in play".

**Titles per viewer:**

| Who | Title |
|---|---|
| The winners | **VICTORY ROYALE** (*You won with X kills!* / *Your team won!*) |
| Players who took part and lost | **DEFEAT**, with the winner and your own placement |
| Players who only watched | **MATCH OVER**, with the winner |

Then fireworks on the winners (`effects.victory-fireworks`), the winner's reward commands, and the **end-of-match summary**. After `end-delay-seconds` everyone goes back to the hub with their own inventory, and the match copy is deleted.

### End-of-match summary

With `game.end-summary: true` every player gets a personal recap in chat:

- placement (per team in DUO/SQUAD, e.g. `#2/6`) and win/eliminated;
- kills, damage dealt, time survived;
- teammates revived (DUO/SQUAD only);
- gulag won/lost (if you went there);
- XP and coins earned (not in [event](../events.md) matches);
- the top killer of the match.

```yaml
game:
  end-delay-seconds: 10
  end-summary: true
```

!!! info
    A "win" with nobody else ever in play (every other team left during the deploy) gives no stats or rewards. A match where everyone disconnects ends with no winner.

## 10. Disconnects and rejoin

If an alive player disconnects, they have `rejoin-grace-seconds` to come back:

- everyone is told *X disconnected. They have 60 seconds to rejoin*;
- when they log back in they are put back on the map and keep playing;
- if the time runs out they are eliminated (and come back as a spectator if they log in later).

Some disconnects are **never** paused:

- a **downed** player or a player in the **gulag** who quits is eliminated at once;
- quitting while **combat-tagged** (hit by a player within `combat-tag-seconds`) is a **combat log**: you are eliminated at once, your loot drops and the attacker gets the kill.

```yaml
game:
  rejoin-grace-seconds: 60
```

`0` = eliminated immediately on disconnect. The value is in **Match rules → Rejoin time**.

## 11. Leaving

Leave with `/br leave` (or `/battleroyale leave`) or the red **Leave** bed.

- **In the queue**: you just leave. If you are a party leader, your party leaves the queue with you.
- **During the match**: it counts as a death. Your loot drops where you stood (if `drop-loot-on-death` is on), you get a placement and the match is recorded as a loss in your stats.
- **As a spectator**: you go back to the hub.
- **In an event match**: you go back to the event lobby instead (see [Events](../events.md)).
