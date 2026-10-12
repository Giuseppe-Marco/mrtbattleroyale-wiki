# Permissions

All permissions are declared in `plugin.yml`, so permission plugins such as LuckPerms show them with a description. They are organised in groups: a group grants everything below it.

```text
br.*                         (op)        everything
├── br.player                (everyone)  every player permission
│   ├── br.play
│   ├── br.stats
│   ├── br.stats.others
│   ├── br.top
│   ├── br.party
│   ├── br.shop
│   ├── br.spectate
│   └── br.event
├── br.admin                 (op)        every staff permission
│   ├── br.admin.setup
│   ├── br.admin.game
│   ├── br.admin.match
│   ├── br.admin.build
│   ├── br.admin.economy
│   ├── br.admin.event
│   ├── br.admin.npc
│   ├── br.admin.signs
│   ├── br.admin.reload
│   ├── br.admin.debug
│   ├── br.admin.bypass
│   ├── br.admin.notify
│   └── br.chat.bypass
└── br.admin.resetstats      (op)        NOT part of br.admin
```

| Permission | Default | Grants |
|---|---|---|
| `br.*` | op | Everything: `br.player`, `br.admin` and `br.admin.resetstats`. |
| `br.player` | everyone | Every player permission. |
| `br.admin` | op | Every staff permission, plus `br.chat.bypass`. |

!!! warning "`br.admin.resetstats` is not in `br.admin`"
    `/br resetstats` wipes the statistics of **every** player, so it is deliberately left out of `br.admin`. Only `br.*` (operators by default) grants it, or you give the node by hand.

## Player permissions

Everyone has these by default.

| Permission | What it allows |
|---|---|
| `br.play` | Joining matches: `/br join`, `/br menu`, `/br list`, arena NPCs and `[br]` join signs. Leaving (`/br leave`) is always allowed. |
| `br.stats` | Your own statistics: `/br stats` and the stats item in the hub. |
| `br.stats.others` | Other players' statistics: `/br stats <player>`. |
| `br.top` | Leaderboards: `/br top`. |
| `br.party` | Parties: `/br party ...`. |
| `br.shop` | Cosmetics shop: `/br shop` and the shop item. |
| `br.spectate` | Spectator camera: `/br spectate` and the follow item. |
| `br.event` | Events: `/br event`, `/br event join`, `leave`, `info`, `top` and the event menu. |

To take something away from players, negate it. For example, with LuckPerms:

```text
lp group default permission set br.shop false
```

closes the cosmetics shop for everyone in the `default` group.

!!! tip "Per-event permission"
    An event can additionally require a permission of your choice to register: `registration.permission` in `events.yml`. See [Events](../events.md).

## Staff permissions

Operators have these by default.

| Permission | What it allows |
|---|---|
| `br.admin.setup` | Arena setup: `/br setup ...`, the setup GUI and chat guide, `/br setlobby`. Also the "start the setup guide" hint on join while no arena exists. |
| `br.admin.game` | `/br start`, `/br forcestart`, `/br stop`, `/br forcejoin`, `/br kick`. |
| `br.admin.match` | Match tools: `/br admin info`, `spectate`, `revive`, `eliminate`, `end`, `zone`, `skipzone`, `deathmatch`, `airdrop`, `refill`, `gulag`, `countdown`, `team`. |
| `br.admin.build` | Build mode in the hub (`/br admin` with no arguments) and building in the hub while it is on. |
| `br.admin.economy` | `/br coins` and `/br xp` (give / take / set). |
| `br.admin.event` | Event management: `/br event list`, `create`, `delete`, `edit`, `setlobby`, `schedule`, `start`, `forcestart`, `stop`, `end`, `next`, `map`, `pause`, `resume`, `add`, `kick`, `points`, `announce`, `undo`, and the event editor GUI. |
| `br.admin.npc` | Arena NPCs: `/br npc ...`. |
| `br.admin.signs` | Creating `[br]` join signs. |
| `br.admin.reload` | `/br reload`. |
| `br.admin.debug` | `/br debug` and `/br debug report`. |
| `br.admin.bypass` | Not blocked inside matches: commands (`commands.block-in-game`) and teleports. Building (blocks, cobwebs, buckets) always follows the map's rule, admins included. |
| `br.admin.notify` | Told about new plugin versions when joining (see [Admin tools](admin-tools.md#update-checker)). |
| `br.admin.resetstats` | `/br resetstats`: resets the statistics of every player. **Not** in `br.admin`. |
| `br.chat.bypass` | No chat anti-flood limits (see [Chat](../features/chat.md)). |

Holding any staff permission also shows the admin part of `/br help`.

## LuckPerms examples

A moderator who runs events and manages matches, but cannot touch arenas or coins:

```text
lp group mod permission set br.admin.event true
lp group mod permission set br.admin.match true
lp group mod permission set br.admin.game true
```

A builder who only sets up arenas:

```text
lp group builder permission set br.admin.setup true
```

A full admin who can also reset the statistics:

```text
lp group admin permission set br.admin true
lp group admin permission set br.admin.resetstats true
```

Players that may only play (no shop, no parties):

```text
lp group default permission set br.shop false
lp group default permission set br.party false
```

A staff member who should **not** be able to run other plugins' commands inside a match:

```text
lp group helper permission set br.admin.bypass false
```
