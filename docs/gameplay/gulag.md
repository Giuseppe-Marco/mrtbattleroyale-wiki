# Gulag

The gulag gives killed players a **second chance**: instead of being eliminated they go to the gulag and fight a **1v1 duel**. The winner gets back into the match, the loser is eliminated.

Each player can go to the gulag **once per match**.

All the rules are in `gulag.yml` and can be changed per arena (setup GUI **Gulag → Rules**, or the arena's `settings:` section, see [Per-arena settings](../getting-started/arena-settings.md)). The gulag **positions** are saved in the arena file.

## Setting it up

Build the gulag somewhere players can't reach from the map (under it, or far away) and set its spots in the setup GUI, **Gulag** category:

| Spot | What it is |
|---|---|
| **Duel spot A / B** | Where the two fighters start (B faces A). **Required**: without A and B the gulag is off for this arena |
| **Extra duel rings** | More A/B pairs, so several duels run **at the same time** (useful in DUO/SQUAD). First click sets A, second click sets B. Build each ring apart from the others |
| **Waiting points** | Where queued players stand and watch, one player per point (extra players share the last one). Without them, players wait between A and B |

Commands: `/br setup gulag <a|b|wait|removewait|clearwait|pair|removepair|clearpairs>`.

## When it is open

A killed player goes to the gulag only while **all** of these are true:

- `gulag.enabled` is on and the arena has spots A and B;
- the match started with at least `min-players` players, and at least `min-players` are still in play (alive **or** waiting in the gulag);
- the match is in the active phase (never in the deathmatch);
- fewer than `close-seconds` have passed since the match started.

Once it closes it **stays closed** for the rest of the match (*The gulag is closing!*). Players already in the queue still get their duel if they have an opponent; anyone left without one goes back into the match as if they had won.

```yaml
gulag:
  enabled: true
  min-players: 4
  close-seconds: 240
```

!!! info "Why at least 4 players?"
    With 3 players the first one down would wait for the second, and the third would be left alone in the arena. A match starting with fewer than `min-players` never activates the gulag.

Admins can override this with `/br admin gulag open|close|auto` (until the end of the match).

## The queue

1. You are killed (or bleed out): *X heads to the GULAG!* and you are put on a waiting point.
2. While waiting you are safe: you can watch the duels but can't fight, use items or open anything.
3. As soon as two players can be paired, a duel starts. In DUO/SQUAD **teammates are never paired** against each other.
4. With extra rings, every free ring is filled at once, so a long queue drains fast.

`start-delay-seconds` is the pause between the end of one duel and the next wave.

## The duel

1. Both fighters are teleported to their spot, get the **duel kit**, full health and food.
2. They are frozen and protected during the countdown (*DUEL vs name*, then the numbers).
3. **FIGHT!** Only the two fighters can hit each other.
4. A lethal hit ends the duel at once: no real death.

The remaining time is shown on the action bar. When the timer ends:

- the fighter with **more health** wins;
- **equal health = draw**: neither earns the respawn, **both are eliminated**.

If a fighter quits, the other wins. Falling into the void counts as a lost duel.

```yaml
gulag:
  countdown-seconds: 3
  duel-seconds: 60
  start-delay-seconds: 2
```

### Fast mode

With many players in the queue, duels get shorter so nobody waits for minutes:

```yaml
gulag:
  fast-queue-threshold: 2
  fast-duel-seconds: 25
  fast-countdown-seconds: 1
```

When at least `fast-queue-threshold` players are waiting (`0` = never), new duels last at most `fast-duel-seconds` with a `fast-countdown-seconds` countdown, and duels already running are cut down too (*HURRY UP! The queue is full*).

### Anti-AFK

Around each duel spot there is a small area (`area: 3` = 3×3 blocks, the spot in the middle). A fighter who **never leaves it** during the fight counts as AFK. When the timer ends:

- one AFK, one active → the **active one wins**, whatever the health;
- both AFK → **both are eliminated**.

Only the fight counts, not the countdown. Being knocked out of the area counts as moving. In the last `warn-seconds` a fighter who hasn't moved yet sees *Move or lose the duel!*.

```yaml
gulag:
  anti-afk:
    enabled: true
    area: 3
    warn-seconds: 10
```

The area is visible with particles while you edit the arena (`/br setup`).

## Back into the match

The winner sees **REDEPLOY!** and returns to the match:

- **gliding** (`redeploy-skydive: true`): launched from the arena's launch point closest to the current zone, spread randomly by `redeploy-spread` blocks so winners don't all land together. Without launch points, high above a random spot in the zone;
- **teleport** (`false`): to a random spot inside the zone.

The winner gets the **return kit**, full health and `respawn-protection-seconds` of damage protection against third parties.

```yaml
gulag:
  redeploy-skydive: true
  redeploy-spread: 20.0
  respawn-protection-seconds: 3
```

In the GUI: **Gulag → Back into the match**.

## Kits

Both fighters get the **duel kit**; the winner gets the **return kit** when going back in. Materials are Minecraft item names; `NONE` leaves a slot empty; `items` takes `MATERIAL:amount` entries.

```yaml
gulag:
  kit:
    weapon: STONE_SWORD
    armor:
      helmet: LEATHER_HELMET
      chestplate: LEATHER_CHESTPLATE
      leggings: LEATHER_LEGGINGS
      boots: LEATHER_BOOTS
    offhand: NONE
    golden-apples: 0
    items: []
  respawn-kit:
    enabled: true
    weapon: WOODEN_SWORD
    armor:
      helmet: NONE
      chestplate: NONE
      leggings: NONE
      boots: NONE
    offhand: NONE
    bread: 4
    items: []
```

!!! tip "Edit the kits in-game"
    In **Gulag → Duel kit** (or **Back into the match → Return kit**), equip yourself as the kit should be and click **Save from my inventory**: held item = weapon, armour, off hand, everything else = items. ++shift++ + click on **Global kit** goes back to the kit in `gulag.yml`. `respawn-kit.enabled: false` = the winner returns with nothing.

## Area protection

The gulag area is **always protected**, on every map, even when building is allowed: nobody can place or break blocks there, and explosions and fire can't damage it. This is a fixed rule so every duel is the same.

The protected area is everything within **20 blocks horizontally** and **10 blocks up or down** of any duel spot or waiting point.

!!! warning
    Keep the gulag at least 20 blocks away from the playable map, or that part of the map will be protected too.

Players in the gulag can't build at all, and the zone has no effect there (no border, no boss bar, no damage).
