# Unity Case Study – Refactoring “Tanks!” with SOLID Principles and Design Patterns

The refactor began with the `GameManager` class from Unity’s **“Tanks!”** tutorial.  
Originally it shouldered many duties at once: driving the game loop, spawning tanks, evaluating win conditions,
updating the HUD and manipulating the camera.

---

## Phase 1 – Single-Responsibility Principle (SRP) & Testability

* **Problem** – `GameManager` intermixed unrelated concerns, making unit tests impractical.
* **UI Extraction** – HUD-message behaviour was moved to `GameMessageUIService`, accessed only through the
  `IGameMessageUIService` interface.
* **Rules Extraction** – round-completion rules were relocated behind an `IGameRulesStrategy` abstraction.

### Representative Code

```
csharp
public interface IGameMessageUIService
{
void DisplayRoundStart(int round);
void ClearMessage();
void DisplayRoundEndResults(
TankManager roundWinner,
TankManager gameWinner,
TankManager[] allTanks);
}

public sealed class GameMessageUIService : IGameMessageUIService
{
private readonly Text _msg;

    public GameMessageUIService(Text msg) =>
        _msg = msg ?? throw new ArgumentNullException(nameof(msg));

    public void DisplayRoundStart(int round) => _msg.text = $"ROUND {round}";

    public void ClearMessage() => _msg.text = string.Empty;
}
```

---

## Phase 2 – Design-Pattern Flexibility

With individual responsibilities separated, traditional patterns could be added cleanly:

* **Decorator** – `LoggingGameMessageUIServiceDecorator` injects logging without altering already-verified classes.
* **Strategy** – `IGameRulesStrategy` enables drop-in game-mode algorithms:

    * `ClassicDeathmatchRulesStrategy`
    * `TimedRoundRulesStrategy`

### Strategy / Decorator Sketch

```
csharp
public interface IGameRulesStrategy
{
void StartRound();
bool IsRoundOver(TankManager[] tanks);
TankManager DetermineRoundWinner(TankManager[] tanks);
TankManager DetermineGameWinner(TankManager[] tanks);
}

public sealed class LoggingGameMessageUIServiceDecorator : IGameMessageUIService
{
private readonly IGameMessageUIService _inner;
private readonly ILogger _log;

    public LoggingGameMessageUIServiceDecorator(
        IGameMessageUIService inner,
        ILogger log)
    {
        _inner = inner;
        _log  = log;
    }

    public void DisplayRoundStart(int round)
    {
        _inner.DisplayRoundStart(round);
        _log.Info($"Round {round} started");
    }

    // Remaining members delegate then log…
}
```

---

## Phase 3 – Abstraction of External Dependencies

`TimedRoundRulesStrategy` referenced `Time.time` and live `GameObject` state.  
Two purpose-built interfaces removed those direct Unity dependencies:

```
csharp
public interface ITimeProvider
{
float Time     { get; }
float DeltaTime { get; }
}

public class UnityTimeProvider : ITimeProvider
{
public float Time      => UnityEngine.Time.time;
public float DeltaTime => UnityEngine.Time.deltaTime;
}

public interface IHealthProvider
{
float CurrentHealth { get; }
}
```

Tests inject `MockTimeProvider` / `MockHealthProvider`, allowing every rule to execute under a standard .NET test
runner.

---

## Wiring – Manual Composition inside `GameManager`

No DI container is required; dependencies are created once in `Start()` and then handed to the strategies/services:

```
csharp
private void Start()
{
ILogger log = new UnityLogger();

    var baseUi   = new GameMessageUIService(m_MessageTextComponent);
    m_GameMessageUIService =
        new LoggingGameMessageUIServiceDecorator(baseUi, log);

    m_GameRulesStrategy = m_SelectedGameMode switch
    {
        GameMode.TimedRounds =>
            new TimedRoundRulesStrategy(
                m_RoundDurationSeconds,
                m_NumRoundsToWin,
                new UnityTimeProvider()),
        _ => new ClassicDeathmatchRulesStrategy(m_NumRoundsToWin)
    };
}
```

`GameManager` therefore becomes a **composition root**—it assembles pure C# building blocks and
then funnels Unity lifecycle events into them.

---

## UML Overview

```
plantuml
@startuml
interface IGameMessageUIService
class GameMessageUIService
class LoggingGameMessageUIServiceDecorator

interface IGameRulesStrategy
class ClassicDeathmatchRulesStrategy
class TimedRoundRulesStrategy

interface ITimeProvider
class UnityTimeProvider

class GameManager

IGameMessageUIService <|.. GameMessageUIService
IGameMessageUIService <|.. LoggingGameMessageUIServiceDecorator

IGameRulesStrategy <|.. ClassicDeathmatchRulesStrategy
IGameRulesStrategy <|.. TimedRoundRulesStrategy

ITimeProvider <|.. UnityTimeProvider

GameManager --> IGameMessageUIService
GameManager --> IGameRulesStrategy
@enduml
```

---

## Summary

1. **SRP Implementation** – UI and rule logic were separated from `GameManager`.
2. **Design Patterns Applied** – Decorator supplies logging; Strategy supports additional game modes.
3. **Test-Oriented Abstractions** – Time and health access were wrapped in interfaces.
4. **Toward Dedicated Roots** – Manual wiring is now a stepping-stone to fully-fledged composition-root `MonoBehaviour`
   s.

> **Further reading:** Unite 2016 “Overthrowing the MonoBehaviour Tyranny”; **Unity in Action** (2nd ed.) Ch. 9;  
> Unity e-book *Level Up Your Code with SOLID*.

---

## See also

- [Single-Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md)
- [Open-Closed Principle (OCP)](Open-Closed-Principle-OCP.md)
- [Interface Segregation Principle (ISP)](Interface-Segregation-Principle-ISP.md)
- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Decorator Pattern](Decorator-Pattern.md)
- [Strategy Pattern Implementation for JSON Processing](Strategy-Pattern-Implementation-for-JSON-Processing.md)
- [Relationship between Dependency Inversion, Dependency Injection, Inversion of Control, and SOLID](Relationship-between-Dependency-Inversion-Dependency-Injection-Inversion-of-Control-and-SOLID.md)
