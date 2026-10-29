# Teams, knockdown & colours

## Team modes

Every arena has a team mode:

| Mode | Team size | Notes |
|---|---|---|
| `SOLO` | 1 | Everyone for themselves |
| `DUO` | `game.duo-size` (2) | Knockdown & revive, teammate glow |
| `SQUAD` | `game.squad-size` (4) | Knockdown & revive, teammate glow |

Set it in the setup GUI (**General → Team mode**, click to cycle SOLO → DUO → SQUAD) or with `/br setup mode <solo|duo|squad>`. `game.team-mode` is only the default for newly created arenas.

```yaml
game:
  team-mode: SOLO
  duo-size: 2
  squad-size: 4
```

In DUO/SQUAD **friendly fire is off**: teammates can't hurt each other, also with harmful splash or lingering potions. Teammates can talk privately with the team chat prefix `!` (see [Chat](../features/chat.md)).

## How teams are formed

When the countdown ends, players are put in teams in this order:

1. **Team choices.** In the waiting lobby of a DUO/SQUAD arena players get the **Choose Team** banner: click a colour to join that team (a full team can't be picked; choices are locked once the match starts).
2. **Parties.** Party members who didn't pick a colour are kept together.
3. **Everyone else** fills the teams with room, evenly.

If everybody ends up on a single team, the teams are split automatically so the match can start.

## Parties

Players can group up before joining:

| Command | What it does |
|---|---|
| `/br party invite <player>` | Invite a player (creates the party, you are the leader) |
| `/br party accept\|deny [leader]` | Answer an invite |
| `/br party kick <player>` | Leader: remove a member |
| `/br party leave` | Leave the party |
| `/br party disband` | Leader: delete the party |
| `/br party info` | Members of your party |

- The maximum party size is `game.squad-size` (at least 2).
- When the **leader joins** a match, the online members are pulled into the same match. If the party is bigger than the team size of that mode, the leader is warned that some members will end up on other teams.
- If the leader leaves the **queue**, the party leaves with them (never during a running match).
- The party can't be changed during a running match.
- In a SKYDIVE arena with a [spawn-picker map wall](map-wall.md), the **leader** chooses the drop point for the whole party.

```yaml
party:
  invite-expire-seconds: 60
  invite-cooldown-seconds: 10
```

These two are in `config.yml`.

## Colours and names

```yaml
team:
  visuals:
    enabled: true
    glow: true
    nametags: hidden
    solo-marker: '<color>● '
    hex-tab-names: true
```

!!! note
    `team.visuals` is global: it can't be changed per arena.

### DUO / SQUAD

Every team gets one of the game's base colours (*Team 1*, *Team 2*...). Minecraft has only **16 base colours** for names and glow; teams use 15 of them (black is left out), so with more teams the colours repeat. The colour is used for the name in the tab list, above the head and in chat. There is no marker in DUO/SQUAD (`<marker>` and `%brbattle_solo_marker%` are empty).

### SOLO

In SOLO every player gets a **hex colour of their own**, all different, however many players there are:

- `solo-marker`: a marker in that colour **before the name** (tab list and name tags). `<color>` is the player's hex colour; `''` = no marker. Since names and glow can only use the 16 base colours, the marker is what makes everyone unique.
- `hex-tab-names: true`: the name in the **tab list** is written in the player's own hex colour, after the marker. The name above the head still uses the closest base colour.
- In the match chat the name is written in the player's own hex colour, with the same marker before it (`<marker><hex>` in `chat.game-format`, see [Chat](../features/chat.md)).

!!! tip "Using the TAB plugin?"
    TAB must not manage name tags or tab list names of players in a match, or it overrides these colours. Set `hex-tab-names: false` to leave the tab list to TAB and use the `%brbattle_name_color_hex%` / `%brbattle_solo_marker%` placeholders. See [Placeholders](../reference/placeholders.md) and the [FAQ](../faq.md).

### Name tags

`nametags` decides whose names are shown above the heads during a match:

| Value | Effect |
|---|---|
| `hidden` | Nobody's name is shown (players can't be identified) |
| `enemies` | Enemies' names hidden, teammates still see each other |
| `visible` | Everyone's name is shown |

### Teammate glow

In DUO/SQUAD teammates **glow through walls**, and only their own team sees it (`glow: true`). It is never used in SOLO.

!!! warning "Requires ProtocolLib"
    The teammate glow needs **ProtocolLib**. Without it there is no teammate glow at all: a normal glow would be visible to enemies too and reveal the teams.

Eliminated players and players in the gulag don't glow.

## Knockdown and revive

In DUO/SQUAD, lethal damage **knocks you down** instead of killing you (*KNOCKED DOWN — Hold on, a teammate can revive you!*).

While downed:

- you crawl slowly, with reduced vision, and can't fight, use items, open chests or throw anything;
- you can't be hurt and the zone doesn't hurt you: you only **bleed out** (`bleed-seconds`);
- a red pulse of particles above you marks you as down (`marker-particles`);
- you glow according to `knockdown.glow`.

A teammate who stays within `revive-radius` blocks for `revive-seconds` **revives** you, with `revive-health` half hearts and a few seconds without zone damage (`revive-zone-grace-seconds`). If they step away, the revive starts over. If you bleed out, the player who knocked you down gets the kill.

You are eliminated directly (no knockdown) when:

- no teammate is left who could revive you (all dead, downed, offline or in the gulag);
- you fall into the void;
- you are holding a totem of undying (the totem saves you instead).

```yaml
knockdown:
  enabled: true
  bleed-seconds: 20
  revive-seconds: 5
  revive-radius: 3.0
  revive-health: 8.0
  slowness-amplifier: 3
  glow: team
  marker-particles: true
  revive-zone-grace-seconds: 3
```

| `glow` | Who sees the downed player's glow |
|---|---|
| `team` | Only their teammates, to find and revive them (recommended, needs ProtocolLib) |
| `all` | Everyone, enemies included |
| `off` | Nobody |

The main values are in the setup GUI under **Knockdown & Revive** (enabled, bleed-out time, revive time, revive distance, health after revive).
