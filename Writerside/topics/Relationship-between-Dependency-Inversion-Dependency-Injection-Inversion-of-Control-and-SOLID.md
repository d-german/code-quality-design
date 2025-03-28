# Relationship between Dependency Inversion, Dependency Injection, Inversion of Control and SOLID

`Example1` class contains (composition) a stable and a volatile dependency.

```C#
public class Example1
{
    private readonly StableDependency _stableDependency = new();
    private readonly VolatileDependency _volatileDependency = new();

    public void Method()
    {
        _stableDependency.DoWork();
        _volatileDependency.SendNotification();
    }
}
```

This class violates at least three **SOLID** principles, namely the **SRP**, **DIP**, and the **OCP**. The **DIP**
violation should be obvious since we are depending on a volatile concrete implementation (`VolatileDependency`) instead
of an interface or abstract class. Since the other class (`StableDependency`) is stable, it is not as worrisome to
depend on it. (See *Using the new keyword*.)

**Example1** also violates the **SRP** when it instantiates (news up) the `VolatileDependency`. **Example1** should
not take responsibility for instantiating volatile dependencies itself. We should move the responsibility of
instantiating volatile dependencies out of **Example1** and into the composition root. This is an application of **IoC
** (inversion of control).

**Example1** violates the **OCP** whenever `VolatileDependency` is updated with a new parameter added to its
constructor. Adding a string parameter to the constructor `public VolatileDependency(string foo)` would instantly break
or cause compile errors in the **Example1** class.

Instead of **Example2** creating `VolatileDependency`, we inject it into the constructor. We know this as **Dependency
Injection**:

```C#
public class Example2
{
    private readonly StableDependency _stableDependency = new();
    private readonly VolatileDependency _volatileDependency;

    public Example2(VolatileDependency volatileDependency)
    {
        _volatileDependency = volatileDependency 
            ?? throw new ArgumentNullException(nameof(volatileDependency));
    }

    public void Method()
    {
        _stableDependency.DoWork();
        _volatileDependency.SendNotification();
    }
}
```
Note that this is **Dependency Injection**, but it is **not** Dependency Inversion. **Example2**, like **Example1**, violates the **DIP**, but it does **not** violate the **SRP** because it uses **IoC** (inversion of control) for its volatile dependency management.

The `Test1` method creates the dependencies and injects them into **Example2**.

```C#
[Test]
public void Test1()
{
    var example2 = new Example2(new VolatileDependency());
    example2.Method();
}
```

Finally, `Example3`  does not violate any of the SOLID principles:

```C#
public class Example3
{
    private readonly StableDependency _stableDependency = new();
    private readonly IVolatileDependency _volatileDependency;

    public Example3(IVolatileDependency volatileDependency)
    {
        _volatileDependency = volatileDependency 
            ?? throw new ArgumentNullException(nameof(volatileDependency));
    }

    public void Method()
    {
        _stableDependency.DoWork();
        _volatileDependency.SendNotification();
    }
}
```

Note that **Example3** no longer depends on the concrete `VolatileDependency` class but now depends on the `IVolatileDependency` interface.

In summary, **Dependency Inversion**, **Dependency Injection**, and **Inversion of Control** are distinct but related concepts, and we can explain them in terms of the **SOLID** design principles.

---
See Also:
- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Dependency Injection (DI)](Dependency-Injection-DI.md)
- [Inversion of Control (IoC)](Inversion-of-Control-IoC.md)
- [SOLID Design Principles](SOLID-Design-Principles.md)
- [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md)
- [Open Closed Principle (OCP)](Open-Closed-Principle-OCP.md)
- [Stable vs Volatile Dependencies](Stable-vs-Volatile-Dependencies.md)
- [Using the new keyword](Using-the-new-keyword.md)
- [Composition Root](Composition-Root.md)