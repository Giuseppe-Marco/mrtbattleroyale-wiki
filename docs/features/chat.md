# Chat

MRTBattleRoyale has its own chat system built around one Battle Royale rule:

> **Alive players can always talk to each other, but dead players and spectators have a separate channel that the alive players never hear.**

Otherwise someone watching from above could call out enemy positions.

All chat settings are in the `chat:` section of `config.yml`. Set `chat.enabled: false` to leave every chat to another plugin.

## Scope: br or server

`chat.scope` decides how much of the server's chat the plugin handles.

=== "scope: br (default)"

    Only the Battle Royale chats are handled:

    - the **BR spawn** (the hub world) has its own chat, only for who is there;
    - every **match** has its own chat;
    - dead players and spectators of each match have their own chat;
    - registered players of a running **event** have the event lobby chat.

    The rest of the server's chat is **not touched** (format, chat plugins): BR players just don't see it, and their messages don't reach the rest of the server. Perfect for a server where Battle Royale is one mode among many.

=== "scope: server"

    The plugin handles the **whole server's chat**: everyone outside a match shares one chat, formatted with `chat.format`. Use it on servers that are only Battle Royale.

Players in a match are always kept out of the outside chat, whatever the scope, so hub talk never leaks into a round.

## The chats

| Where you are | Who hears you | Format |
|---|---|---|
| BR spawn (hub world) | Players at the BR spawn, not in a match | `format` |
| Event lobby | Registered players of the running event who are not in a match | `event-format` |
| Arena waiting lobby / countdown | Everyone in that arena | `game-format` |
| In a match, alive | All players of that match (alive and spectators), or only nearby ones with `PROXIMITY` | `game-format` |
| In a match, with the team prefix | Only your teammates | `team-prefix` + `game-format` |
| In a match, dead / spectating | Only the dead and spectators of that match | `spectator-prefix` + `game-format` |
| End-of-match screen | Everyone in that arena | `game-format` |

Spectators **do** hear the alive players: they are out of the round and can't act on what they hear, and following the chat is part of watching. Only the other direction is blocked.

Every match line is also written to the console, tagged with the arena name.

### BR spawn chat

With `scope: br`, the BR spawn chat covers the worlds in `chat.hub-worlds`, or the hub world set with `/br setlobby` when the list is empty.

### Event lobby chat

Players registered in a running [event](../events.md) and not in a match talk only among themselves, with `event-format`.

### Team chat

In DUO and SQUAD, a message that starts with `team-chat-prefix` (default `!`) goes **only to your teammates**:

```
!rotate to the north, zone is closing
```

It is ignored in SOLO. Turn it off with `team-chat: false`.

### Proximity chat

`active-chat-mode` decides how the **alive** players hear each other during the match:

| Value | Effect |
|---|---|
| `ALL` (default) | Every alive player in the match hears you |
| `PROXIMITY` | Only players within `proximity-radius` blocks hear you |

Spectators always hear everything, and their own channel works the same in both modes.

## Formats

```yaml
chat:
  enabled: true
  scope: br
  hub-worlds: []
  smallcaps: false

  # Outside matches (BR spawn, or the whole server with scope: server)
  format: '<#ffffff>%player% <#333333>» <gray>%message%'
  allow-player-colors: false

  # Event lobby
  event-format: '<gold>[EVENT] <#ffffff>%player% <#333333>» <gray>%message%'

  # Inside a match: <hex> becomes the team color
  game-format: '<hex><player> <dark_gray>»</dark_gray> <white><message>'
  default-name-color: '#ffffff'
  spectator-prefix: '<gray>[SPECTATOR] '
  team-prefix: '<green>[TEAM] '

  active-chat-mode: ALL
  proximity-radius: 48.0

  team-chat: true
  team-chat-prefix: '!'
```

| Key | What it does |
|---|---|
| `format` / `event-format` | Use `%player%`, `%message%`, `%world%` and any PlaceholderAPI placeholder (for example `%vault_prefix%`). Colors: legacy `&a` / `§a`, hex `<#rrggbb>` and MiniMessage like `<gradient:...>`. |
| `game-format` | Use `<player>`, `<message>` and `<hex>`, the player's team color. `default-name-color` is used in SOLO or before teams are assigned. |
| `smallcaps` | Small caps for the fixed text of the format (prefixes, arrows). Player names and messages are never converted. Off by default because it makes conversations harder to read. |
| `allow-player-colors` | Lets players use colors and decorations (MiniMessage or `&` codes) in their own messages. Click, hover and similar tags are never allowed. |

!!! warning "Don't close `<hex>`"
    `<hex>` is replaced by an opening color tag like `<#55ffff>`. Don't write `</hex>`: it would show up as text. The next color in the format (here `<dark_gray>`) ends it.

## Moderation

```yaml
  moderation:
    anti-caps:
      enabled: true
      min-length: 8
      max-percent: 50
    anti-flood:
      enabled: true
      cooldown-seconds: 2
      block-repeats: true
    blacklist:
      enabled: false
      block-message: false
      replacement: '***'
      words: []
```

| Filter | What it does |
|---|---|
| **anti-caps** | Lowercases a message of at least `min-length` characters when more than `max-percent`% of its letters are capitals. |
| **anti-flood** | Blocks messages sent less than `cooldown-seconds` apart and, with `block-repeats`, the same message twice in a row. The player is told why. |
| **blacklist** | Replaces each banned word with `replacement` (case-insensitive), or cancels the whole message with `block-message: true`. |

The `br.chat.bypass` permission exempts a player from the anti-flood filter.

## Command blocking in matches

To stop players escaping or cheating (teleporting away, flying, passing on information), commands are **blocked while a player is in a match or in an arena queue**:

```yaml
commands:
  # Blocks commands during the match.
  block-in-game: true
  # Commands still allowed to players in a match.
  # (/br and /battleroyale are always allowed.)
  allowed: []
```

- `/br` and `/battleroyale` can never be blocked, so players can always `/br leave`.
- Namespaced forms are blocked too: blocking `spawn` also blocks `/essentials:spawn`.
- Add commands that should still work to `allowed`, without the slash, for example:

    ```yaml
    allowed:
      - msg
      - r
    ```

    An entry also matches its namespaced forms (`msg` allows `/essentials:msg`).

- Private message commands (`/msg`, `/tell`, `/me`...) are blocked like any other command unless you allow them.
- Players with `br.admin.bypass` (part of `br.admin`) are never blocked.

Blocked players see the `general.command-blocked` message.
