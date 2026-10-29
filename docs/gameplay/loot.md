# Loot, chests & airdrops

Players find their gear in **chests** placed around the map and in **airdrops** (supply crates) that fall during the match. What they contain comes from the loot tables in `loot.yml`.

## Loot tables (`loot.yml`)

Loot is organised in **tiers** (rarities). Each chest rolls several item stacks; for every stack:

1. a tier is chosen by its `weight`;
2. an item inside that tier is chosen by its own `weight`.

The default file has four tiers:

| Tier | Weight | Examples |
|---|---|---|
| `common` | 55 | Stone sword, leather armour, food, planks, arrows |
| `rare` | 30 | Iron sword and armour, bow, shield, golden apple, ender pearls |
| `epic` | 12 | Diamond gear, enchanted bow, crossbow, potions, totem |
| `legendary` | 3 | Netherite sword and chestplate, *Vortex Blade*, enchanted golden apple |

### Item fields

| Field | Meaning |
|---|---|
| `material` | Bukkit material name (required) |
| `weight` | Chance inside the tier (default 1, `0` = disabled) |
| `min` / `max` | Stack size range (default 1) |
| `enchantments` | Map of `<enchant key>: <level>` (vanilla keys, lowercase) |
| `name` | Custom name (MiniMessage) |
| `lore` | List of lore lines (MiniMessage) |
| `potion` | Effect for `POTION`, `SPLASH_POTION`, `LINGERING_POTION` (e.g. `speed`) |
| `duration` | Effect duration in seconds (default 30) |
| `amplifier` | Effect level, `0` = I (default 0) |

Examples from the default file:

```yaml
tiers:
  epic:
    weight: 12
    items:
      - { material: DIAMOND_SWORD, weight: 8, enchantments: { sharpness: 1 } }
      - { material: POTION, potion: speed, duration: 45, weight: 4, name: '<aqua>Sprint Potion' }
  legendary:
    weight: 3
    items:
      - material: DIAMOND_SWORD
        weight: 6
        enchantments: { sharpness: 2, unbreaking: 3 }
        name: '<gradient:#8be9fd:#bd93f9><bold>Vortex Blade</bold></gradient>'
        lore:
          - '<gray>Forged in the heart of the storm.'
```

!!! tip
    A tier with `weight: 0` never appears in normal chests, but airdrops can still use it. Handy for airdrop-only loot.

### Per-arena loot

`tiers` is the default loot of every arena. To give an arena different loot, use the `tables` and `arenas` sections at the end of `loot.yml`:

```yaml
tables:
  hardcore:
    inherit: true          # start from the default tiers
    tiers:
      legendary:           # same name = this tier is replaced
        weight: 8
        items:
          - { material: NETHERITE_SWORD, weight: 5 }
          - { material: ENCHANTED_GOLDEN_APPLE, min: 1, max: 2, weight: 4 }
arenas:
  Events: hardcore         # arena name -> table name
```

- `tables` are named tables that several arenas can share.
- In `arenas` an arena can point to a table, or have a table written right there.
- `inherit: true` keeps the default tiers and only replaces or adds the tiers you write.
- Chests and airdrops of an arena both use its table.

Apply changes with `/br reload`.

## Chests

### Chest points

Register chest points while editing the arena: look at a chest and use **Loot & Features → Add chest** or `/br setup chest`. **Remove nearby chests** (or `/br setup removechest`) deletes the points within 3 blocks of you.

A map that is already full of chests doesn't need clicking one by one: **Loot & Features → Find chests automatically** (or `/br setup scanchests [radius]`) registers every chest, trapped chest and barrel within the zone radius (types and speed: `setup.chest-scan` in `config.yml`). **Remove all chests** (++shift++ + click, or `/br setup clearchests confirm`) removes every chest point. See [Your first arena](../getting-started/first-arena.md#placing-chests).

!!! warning "Register points on the chests themselves"
    Points that are **not** picked in a match become **air**, whatever block is there (in the match copy only, the original map is never touched). So register chest points on real chests, not on the floor. If a chosen point has no container, a chest is placed there.

### How many chests per match

```yaml
loot:
  chests:
    mode: per-player
    per-player: 3
    min: 10
    max: 0
    spread: true
```

| Mode | Chests used |
|---|---|
| `all` | Every chest point of the arena, every match |
| `per-player` | Players × `per-player`, never fewer than `min`, never more than `max` (`0` = up to all points). Chosen at random every match |

With `spread: true` the chosen chests are spread over the whole map instead of bunching up in one area.

### Items per chest and refill

```yaml
loot:
  refill-seconds: 0
  min-items: 3
  max-items: 6
```

- Each chest gets between `min-items` and `max-items` item stacks, in random slots.
- `refill-seconds` refills the match chests every N seconds of match (`0` = never). Players see *The chests have been refilled!*. A chest that was broken is not put back.
- Admins can refill now with `/br admin refill`.

All these values are in the setup GUI under **Loot & Features → Loot settings**.

### Chest sound

A living player standing close to a match chest that **still has loot** hears a sound coming from it, only that player, like the glowing chests of other battle royales. An empty chest goes quiet.

```yaml
loot:
  chest-sound:
    enabled: true
    radius: 1.5
    stop-when-opened: false
    sound: block.amethyst_block.chime
    volume: 0.8
    pitch: 1.2
    interval-ticks: 20
```

| Key | Meaning |
|---|---|
| `radius` | Distance (blocks) within which the sound is heard |
| `stop-when-opened` | `true` = a chest goes quiet once someone opened it (until a refill), even with loot left. `false` = it sounds while it holds loot |
| `sound` | Minecraft sound key, e.g. `block.note_block.chime`, `block.beacon.ambient` |
| `interval-ticks` | How often it plays (20 = 1 second, minimum 10) |

In the setup GUI: **Loot & Features → Loot settings → Chest sound**. There you can also cycle through a few sounds and press **Listen** to hear the current settings.

## Airdrops (supply drops)

During the match supply crates fall on the map: an **ender chest** with better loot, announced to everyone with a title and the coordinates (*SUPPLY DROP — Landed at x, z*), firework bursts and a light beam above the crate.

```yaml
supply-drop:
  enabled: true
  first-delay-seconds: 60
  interval-seconds: 60
  max-drops: 3
  min-items: 6
  max-items: 9
  crate-title: '<gradient:#ff5f6d:#ffc371><bold>Supply Drop</bold></gradient>'
  beacon-fireworks: 6
```

| Key | Meaning |
|---|---|
| `first-delay-seconds` | Seconds from the start of the match to the first drop |
| `interval-seconds` | Seconds between drops (`0` = a single drop per match) |
| `max-drops` | Drops per match (`0` = unlimited while the match lasts) |
| `min-items` / `max-items` | Item stacks in each crate |
| `crate-title` | Title of the crate window (MiniMessage) |
| `beacon-fireworks` | Firework bursts that mark the landing |

### Where they land

- **Airdrop points** (optional): add them in **Loot & Features → Airdrops** or with `/br setup airdrop` (`/br setup removeairdrop` removes the ones within 3 blocks). A drop picks a free point **inside the current zone**.
- Without points, or when no point is inside the zone, the crate lands on a random spot in the zone.

### What's inside

Airdrops roll from the **`epic` and `legendary`** tiers of the arena's loot table (by their weight). If the table has neither, any tier is used.

### Rules

- The crate is shared: everyone opens the same inventory.
- Spectators, players waiting in the gulag and downed players can't open it.
- Ordinary ender chests in the match world don't open (they would be a personal stash).
- Admins can drop one now with `/br admin airdrop`: under their feet when they are inside the zone of that match, otherwise at the usual spot. It counts toward `max-drops`.

All airdrop settings are in **Loot & Features → Airdrops**. Firework colours are in `effects.firework.airdrop-color` / `airdrop-fade`.
