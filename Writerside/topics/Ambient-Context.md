# Ambient Context (Anti-Pattern)

While having data or services readily available in the environment might seem convenient, a specific problematic approach is known as the **Ambient Context** anti-pattern.

> **DEFINITION:** An Ambient Context supplies application code outside the Composition Root with global access to a Volatile Dependency or its behavior by the use of static class members. *(Source: Seemann, Dependency Injection Principles, Practices, and Patterns, Ch 5.3)*

This pattern often manifests when dealing with ubiquitous cross-cutting concerns like accessing the current time or logging.

**Example: Ambient TimeProvider**

Instead of directly calling `DateTime.Now` (which makes testing difficult), developers might introduce an abstraction like `ITimeProvider` but then provide access via a static property:

```C#
// Abstraction
public interface ITimeProvider
{
    DateTime Now { get; }
}

// Ambient Context Implementation (Anti-Pattern)
public static class TimeProvider
{
    private static ITimeProvider current = new DefaultTimeProvider();

    // Static property providing global access
    public static ITimeProvider Current
    {
        get { return current; }
        // Allows global state modification (problematic for tests)
        set { current = value ?? new DefaultTimeProvider(); }
    }

    private class DefaultTimeProvider : ITimeProvider
    {
        public DateTime Now => DateTime.Now;
    }
}

// Consuming Class using Ambient Context
public class WelcomeMessageGenerator
{
    public string GetWelcomeMessage()
    {
        // Accessing the dependency via static member
        ITimeProvider provider = TimeProvider.Current;
        DateTime now = provider.Now;

        string partOfDay = now.Hour < 12 ? "morning" : "afternoon";
        return $"Good {partOfDay}!";
    }
}
```

**Why is this an Anti-Pattern?**

1.  **Hidden Dependencies:** The `WelcomeMessageGenerator` *requires* an `ITimeProvider` to function correctly, but this dependency is hidden. Its constructor signature (`public WelcomeMessageGenerator()`) doesn't declare the need, making the API dishonest about its requirements.
2.  **Testing Difficulties:** Unit testing `WelcomeMessageGenerator` becomes awkward. Tests must manipulate the global static state (`TimeProvider.Current`), often by replacing it with a stub or mock before the test and (hopefully) resetting it afterward. This creates risks:
    *   **Test Interference:** Parallel tests manipulating the same static property will interfere with each other.
    *   **State Leakage:** If a test forgets to reset the static property, it can affect subsequent tests even when run sequentially.
    *   **Temporal Coupling:** The code relies on the `TimeProvider.Current` being set *before* it's used.
3.  **Masks Design Smells:** Hiding dependencies makes it easier to overlook violations of the Single Responsibility Principle, such as when a class accumulates too many hidden dependencies (related to Constructor Over-injection).

Static loggers (e.g., `LogManager.GetLogger(...)`) are another extremely common example of this anti-pattern, often copied from library documentation examples focused on simplicity over best practice.

**Preferred Alternative: Dependency Injection**

The recommended solution is to make dependencies explicit, typically through Constructor Injection:

```C#
// Consuming Class using Dependency Injection
public class WelcomeMessageGenerator
{
    private readonly ITimeProvider _timeProvider;

    // Dependency is explicitly requested in the constructor
    public WelcomeMessageGenerator(ITimeProvider timeProvider)
    {
        _timeProvider = timeProvider
            ?? throw new ArgumentNullException(nameof(timeProvider));
    }

    public string GetWelcomeMessage()
    {
        // Uses the injected instance
        DateTime now = _timeProvider.Now;
        string partOfDay = now.Hour < 12 ? "morning" : "afternoon";
        return $"Good {partOfDay}!";
    }
}
```

**Benefits of Injection:**

*   **Testability:** Easily provide a test-specific `ITimeProvider` (stub/mock) during test setup via the constructor. No global state manipulation is needed.
*   **Explicit Dependencies:** The class constructor clearly declares its requirements.
*   **Flexibility:** Adheres to the Dependency Inversion Principle, allowing different `ITimeProvider` implementations to be supplied without changing `WelcomeMessageGenerator`.

While Ambient Context might offer superficial convenience, it introduces hidden coupling and significantly complicates testing compared to explicitly managing dependencies through Dependency Injection.

---

**See Also:**

*   [Composition Root](Composition-Root.md) (Where dependencies should be configured)
*   [Coupling](Coupling.md)
*   [Dependency Injection (DI)](Dependency-Injection-DI.md)
*   [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
*   [Inversion of Control (IoC)](Inversion-of-Control-IoC.md)
*   [Minimize Accessibility of Classes and Members](Minimize-Accessibility-of-Classes-and-Members.md)
*   [Open-Closed Principle (OCP)](Open-Closed-Principle-OCP.md)
*   [Pillars of OOP (Encapsulation, Abstraction)](Pillars-of-OOP-Encapsulation-Abstraction.md)
*   [Program to an interface; not an Implementation (GOF 18)](Program-to-an-interface-not-an-Implementation-GOF-18.md)
*   [Relationship between Dependency Inversion, Dependency Injection, Inversion of Control, and SOLID](Relationship-between-Dependency-Inversion-Dependency-Injection-Inversion-of-Control-and-SOLID.md)
*   [Seam-Interface](Seam-Interface.md)
*   [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md) (Related via Constructor Over-injection)
*   [Stable vs Volatile Dependencies](Stable-vs-Volatile-Dependencies.md) (Ambient Context typically involves Volatile Dependencies)
*   [Static Methods](Static-Methods.md)
