# Using the new keyword

Any line of code that uses the `new` keyword violates the **DIP**. Sometimes, the violation of the DIP is mostly
harmless. The more likely a concrete class is to change, the more likely depending on it will lead to trouble. But if
the concrete class is not volatile, then depending on it is not worrisome. (2002 Martin 269)

The `String` class, for example, is very stable. Changes to that class are exceedingly rare and tightly controlled. We
do not have to worry about frequent and capricious changes to `String`.

Another example is our use of simple DTOs (Data Transfer Objects):

> Because only concrete classes have constructors, constructors are an implementation detail. An interesting corollary
> to this is that, aside from a few exceptions, you can consider an appearance of the `new` keyword to be a code smell. (
> McLean Hall, 83)

This leads to a second point. Using the `new` keyword could violate the **SRP**. (TBD find a reference for this.)

Creating objects can be complex. An object may depend on several other objects. Manually creating instances of
everything and passing them to classes that aggregate their functionality, and repeating until you’re finished, is a
responsibility that we should delegate to a **DI** (dependency injection) or **IoC** (Inversion of Control) container.

---
See Also:

- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Stable vs Volatile Dependencies](Stable-vs-Volatile-Dependencies.md)
- [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md)
- [Dependency Injection (DI)](Dependency-Injection-DI.md)
- [Inversion of Control (IoC)](Inversion-of-Control-IoC.md)
- [Composition Root](Composition-Root.md)
- [Relationship between Dependency Inversion, Dependency Injection, Inversion of Control and SOLID](Relationship-between-Dependency-Inversion-Dependency-Injection-Inversion-of-Control-and-SOLID.md)