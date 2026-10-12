# Developer API

Other plugins (bots, scoreboards, rewards, anti-cheat exemptions...) can read the state of matches and listen to match events.

## Adding the dependency

The API ships inside the plugin jar; there is no separate artifact.

1. Add `MRTBattleRoyale-2.0.2.jar` to your project as a **compile-only** dependency (it must not be shaded into your plugin).

    === "Maven"

        ```xml
        <dependency>
            <groupId>it.mrt</groupId>
            <artifactId>MRTBattleRoyale</artifactId>
            <version>2.0.2</version>
            <scope>system</scope>
            <systemPath>${project.basedir}/libs/MRTBattleRoyale-2.0.2.jar</systemPath>
        </dependency>
        ```

    === "Gradle"

        ```groovy
        dependencies {
            compileOnly files('libs/MRTBattleRoyale-2.0.2.jar')
        }
        ```

2. In your `plugin.yml`, add the plugin to `depend` (required) or `softdepend` (optional):

    ```yaml
    softdepend:
      - MRTBattleRoyale
    ```

    With `softdepend`, check that the plugin is present before using the API (`Bukkit.getPluginManager().isPluginEnabled("MRTBattleRoyale")`).

## `BattleRoyaleAPI`

Package `it.mrt.battleroyale.api`, static methods.

- Only plain Java types cross the API (`UUID`, `String`, `boolean`, `int`, `Set<UUID>`), so your code does not depend on the plugin's internals, which can change between versions.
- Every method is safe to call at any time: when the plugin is not enabled or the player is not in a game, it returns `false`, `null`, `-1` or an empty set.
- Call it from the server **main thread**, like the rest of the Bukkit API.

| Method | Returns |
|---|---|
| `isInGame(UUID)` | `true` if the player belongs to a game (queue, countdown or match). |
| `isInMatch(UUID)` | `true` if the player's game is being played (deploy, fight, deathmatch). |
| `getArenaName(UUID)` | Arena name, or `null` when not in a game. |
| `getGameState(UUID)` | `WAITING`, `COUNTDOWN`, `DEPLOYING`, `ACTIVE`, `DEATHMATCH`, `ENDING`, `RESETTING`, or `null`. |
| `getMode(UUID)` | `SOLO`, `DUO`, `SQUAD`, or `null`. |
| `isAlive(UUID)` | Alive and fighting in the arena (not eliminated, not spectating; in the gulag = `false`). |
| `isSpectating(UUID)` | Eliminated and watching the match. |
| `isInGulag(UUID)` | In the gulag: waiting for a duel or fighting one. |
| `isInGulagDuel(UUID)` | Fighting a gulag duel right now. |
| `isKnocked(UUID)` | Knocked down, waiting for a revive or bleeding out (DUO/SQUAD). |
| `getTeamId(UUID)` | Team number (`0`, `1`, `2`...), or `-1` when not on a team. |
| `getTeamMembers(UUID)` | Every member of the player's team, the player included (empty when none). Unmodifiable. |
| `areTeammates(UUID, UUID)` | `true` if both are on the same team of the same game. |

Example:

```java
import it.mrt.battleroyale.api.BattleRoyaleAPI;

UUID bot = botPlayer.getUniqueId();
UUID target = targetPlayer.getUniqueId();
if (BattleRoyaleAPI.isInMatch(bot)
        && BattleRoyaleAPI.isAlive(bot)
        && !BattleRoyaleAPI.areTeammates(bot, target)) {
    // the bot may attack the target
}
```

## Events

Package `it.mrt.battleroyale.api.event`. Standard Bukkit events: listen to them with `@EventHandler` in a registered `Listener`. They are informational (not cancellable).

| Event | Fired when | Useful getters |
|---|---|---|
| `GameStartEvent` | A match goes live: the arena world is loaded, players are deployed and combat is about to begin (state `ACTIVE`). | `getGame()` |
| `GameEndEvent` | A match ends (state `ENDING`), before the end-screen delay and the world teardown. | `isDecisive()` (`false` when the match was aborted and nobody won), `getWinner()` (`null` when there is no winner), `getGame()` |
| `PlayerEliminatedEvent` | A player is eliminated **for good**. Entering the gulag is not an elimination; losing the gulag duel is. | `getVictim()` (`null` if the player was offline), `getKiller()` (`null` for zone, fall, bleed-out, disconnect...), `getPlacement()` (1 = winner), `getGame()`, `getParticipant()` |

!!! warning "Prefer the player getters and `BattleRoyaleAPI`"
    `getGame()`, `getParticipant()` and `getWinner()` return the plugin's internal objects, which are not part of the stable API and may change between versions. Use the events as triggers and read details with the `Player` getters, `getPlacement()`, `isDecisive()` and the `BattleRoyaleAPI` methods (e.g. `getArenaName(uuid)`, `getTeamMembers(uuid)`).

Example:

```java
import it.mrt.battleroyale.api.BattleRoyaleAPI;
import it.mrt.battleroyale.api.event.PlayerEliminatedEvent;
import org.bukkit.entity.Player;
import org.bukkit.event.EventHandler;
import org.bukkit.event.Listener;

public class BrListener implements Listener {

    @EventHandler
    public void onEliminated(PlayerEliminatedEvent event) {
        Player killer = event.getKiller();
        Player victim = event.getVictim();
        if (killer != null && victim != null) {
            String arena = BattleRoyaleAPI.getArenaName(killer.getUniqueId());
            killer.sendMessage("You eliminated " + victim.getName() + " on " + arena
                    + " (placement #" + event.getPlacement() + ")");
        }
    }
}
```

## Other integration points

- **PlaceholderAPI**: everything in [Placeholders](placeholders.md) is also readable through PlaceholderAPI from any plugin.
- **Reward commands**: console commands for winners and kills are configured in `economy.yml` (`rewards`), with no code needed. See [Stats, levels & shop](../features/progression.md).
