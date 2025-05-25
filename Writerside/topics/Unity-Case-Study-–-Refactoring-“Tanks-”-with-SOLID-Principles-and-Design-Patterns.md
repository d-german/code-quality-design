# Unity Case Study – Refactoring “Tanks!” with SOLID Principles and Design Patterns

The starting point was the `GameManager` class from Unity's "Tanks!" tutorial. This class initially handled numerous
responsibilities, including game loop management, tank spawning, win condition determination, UI message updates, and
camera control.

**Phase 1: Applying the Single Responsibility Principle (SRP) & Establishing Testability**

* **Core Problem Identified:** The original `GameManager` violated the Single Responsibility Principle, concentrating
  too many distinct concerns in one place. This made the class large, difficult to understand, challenging to modify
  without unintended side effects, and hard to unit test effectively.
* **First Key Refactoring - UI Responsibility Extraction:**
    * The logic and state related to displaying UI messages were identified as a distinct responsibility.
    * This responsibility was extracted from `GameManager` into a new, focused class: `GameMessageUIService`.
    * Unit tests were written for `GameMessageUIService` to verify its message formatting and display logic, ensuring
      this extracted component behaved correctly in isolation. This established a baseline for testable code.
* **Second Key Refactoring - Game Rules Responsibility Extraction:**
    * Similarly, the logic for determining round end conditions and identifying winners (e.g., `OneTankLeft`,
      `GetRoundWinner`, `GetGameWinner`) was recognized as another distinct responsibility.
    * This rules logic was extracted from `GameManager` into a new, dedicated class (initially, this might have been a
      single `GameRulesManager` or similar).
    * Unit tests were written for this extracted rules logic to ensure its correctness independently of the
      `GameManager`'s orchestration.

  *(At this juncture, even without further design patterns, the codebase was significantly improved. `GameManager` was
  simpler, and two core pieces of functionality—UI messaging and game rules determination—were now isolated, better
  organized, and independently testable. This adherence to SRP provided a more maintainable and understandable
  foundation.)*

**Phase 2: Leveraging Decoupling to Introduce Design Patterns for Flexibility and Extensibility**

With key responsibilities now encapsulated in their own classes, the design became more amenable to advanced software
design patterns, enabling greater flexibility and cleaner extension.

* **Enhancing UI Service with the Decorator Pattern:**
    * **Requirement:** Add logging to UI message interactions without modifying the existing, tested
      `GameMessageUIService`.
    * **Solution:** An `IGameMessageUIService` interface was introduced, with `GameMessageUIService` implementing it.
    * The **Decorator pattern** was applied by creating `LoggingGameMessageUIServiceDecorator`, which also implemented
      `IGameMessageUIService`. This decorator wrapped an instance of `IGameMessageUIService`, added logging behavior
      around the method calls, and then delegated the actual work to the wrapped service. An `ILogger` interface (with
      `UnityLogger` as a concrete implementation) was used by the decorator.
    * `GameManager` was updated to use the decorated `IGameMessageUIService`.

* **Implementing Multiple Game Modes with the Strategy Pattern:**
    * **Requirement:** Support different game modes (e.g., classic deathmatch, timed rounds) with varying rules for
      round completion and winner determination.
    * **Solution:** The previously extracted game rules class was further evolved by applying the **Strategy pattern**.
    * An `IGameRulesStrategy` interface was defined, establishing a contract for how game rules are applied (
      `StartRound`, `IsRoundOver`, `DetermineRoundWinner`, `DetermineGameWinner`).
    * Concrete strategy classes were created:
        * `ClassicDeathmatchRulesStrategy`: Encapsulated the original "last tank standing" logic.
        * `TimedRoundRulesStrategy`: Implemented rules for rounds ending either by a timer or by a single tank
          remaining, with winner determination logic including health comparison if time expired.
    * `GameManager` was modified to select and hold an instance of `IGameRulesStrategy` based on a configurable
      `GameMode` enum, delegating rule-based decisions to the active strategy.

**Phase 3: Deepening Testability for Complex Strategies by Abstracting External Dependencies**

* **Problem:** The `TimedRoundRulesStrategy`, while now a separate strategy, still contained direct dependencies on
  `UnityEngine.Time.time` and relied on `TankManager`'s `m_Instance` (a `GameObject`) for health and activity status,
  making pure unit testing of its specific logic challenging.
* **Solution - Abstracting Time:**
    * An `ITimeProvider` interface was introduced to abstract away `UnityEngine.Time`.
    * `UnityTimeProvider` was created as the production implementation.
    * `TimedRoundRulesStrategy` was refactored to accept an `ITimeProvider` via its constructor, allowing a
      `MockTimeProvider` to be used in tests for precise control over time.
* **Solution - Abstracting Health Data Access:**
    * An `IHealthProvider` interface was introduced.
    * The existing `TankHealth` component was made to implement `IHealthProvider`.
    * `TankManager` was updated to cache a reference to `IHealthProvider` (obtained from its tank instance's
      `TankHealth` component) in a field named `m_HealthProvider`.
    * `TimedRoundRulesStrategy` was updated to access health data through `tankManager.m_HealthProvider.CurrentHealth`,
      enabling the use of mock health providers in tests by controlling the `m_HealthProvider` on test `TankManager`
      instances.
* **Testing the Strategies:**
    * Unit tests for `ClassicDeathmatchRulesStrategy` were straightforward due to its simpler logic.
    * Unit tests for `TimedRoundRulesStrategy` now effectively utilized `MockTimeProvider` and `MockHealthProvider` (via
      test `TankManager` instances whose `m_Instance` was a dummy `GameObject` to control `activeSelf`). This allowed
      for thorough verification of its time-dependent and health-dependent logic.

**Summary of Applied Principles and Outcomes:**

This refactoring journey demonstrates a progression:

1. **Foundation via SRP:** First, key responsibilities were extracted from the monolithic `GameManager` into separate,
   testable classes. This was a critical prerequisite for further improvements.
2. **Flexibility via Design Patterns:** Once responsibilities were well-defined and encapsulated, design patterns like
   Decorator and Strategy were applied to introduce flexibility (logging, multiple game modes) in a clean, maintainable
   way.
3. **Enhanced Testability via Abstraction:** For more complex components like the `TimedRoundRulesStrategy`, further
   abstraction of external dependencies (like Unity's time and component system access) was necessary to achieve robust,
   isolated unit tests.

This approach led to a codebase that is more modular, easier to understand, more flexible to extend with new features or
behaviors, and significantly more testable than the original implementation.