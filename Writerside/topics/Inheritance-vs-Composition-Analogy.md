# Inheritance vs. Composition: A House of Cards and Legos Analogy

When designing object-oriented systems, two fundamental mechanisms for code
reuse and establishing relationships are **inheritance** ("is-a") and
**composition** ("has-a"). While inheritance is often taught first, the
widely respected design principle **"Prefer composition over class
inheritance" (GOF 20)** suggests caution. Why? Let's explore this using
analogies.

## Inheritance: The House of Cards

Imagine building with a deck of playing cards. You can create impressive,
tall structures by carefully balancing cards on top of each other. This is
similar to building a deep inheritance hierarchy.

<img src="Houseofcards.png" width="512"/>

* **The Benefit:** You get height (reuse) quickly by leveraging the
  structure below. A `Manager` "is-a" `Employee`, inheriting common
  properties and behaviors.
* **The Problem:** The structure is inherently fragile and tightly coupled.
    * **Fragility:** Nudge a card at the bottom, and the entire structure
      above it might collapse. Similarly, changing a base class (even with
      `protected` members) can unintentionally break derived classes
      further down the chain. The dependencies flow downwards, but the
      impact of changes flows upwards.
    * **Rigidity:** It's difficult to change just one part of the structure
      without affecting others. Replacing a card might require rebuilding
      significant portions. In software, refactoring base classes in a
      deep hierarchy can be complex and risky.
    * **Single Foundation:** A standard house of cards typically rests on a
      single base layer, much like many languages restrict classes to
      inheriting from a single base class (excluding interfaces).

Inheritance creates the strongest coupling between classes. While powerful
for modeling true "is-a" relationships that adhere to the Liskov
Substitution Principle (LSP), it can lead to brittle designs if used
primarily for code reuse where the relationship isn't a true, stable
specialization.

## Composition: Building with Legos

Now, imagine building with Lego bricks. Each brick is a self-contained unit
with standard connection points (studs and tubes). You connect bricks to
create larger structures.

<img src="lego-house.png" width="512"/>

* **The Benefit:** The structure is modular and flexible.
    * **Modularity:** Each Lego brick represents a distinct responsibility
      or piece of data/behavior (aligning with the Single Responsibility
      Principle - SRP).
    * **Flexibility:** You can easily swap one brick for another of the
      same connection type (e.g., a red 2x4 for a blue 2x4). Changes are
      often localized. Need a different engine in your Lego car? Just pop
      the old one out and snap the new one in.
    * **Loose Coupling:** Bricks depend on the standard connection
      mechanism (the studs and tubes), not the specific internal details
      of other bricks.

Composition allows objects to be assembled from smaller, often independent
parts. An `OrderProcessor` "has-a" `PaymentGateway` and "has-a"
`InventoryManager`. This generally leads to more flexible, maintainable,
and testable designs, much like building with standard Lego bricks allows
for easy assembly and reconfiguration.

## The Catch: Composition Gone Wrong (The Glued Model)

But what happens if we misuse composition? Think about building with Legos.
Their very purpose is modularity; you assemble, disassemble, and reconfigure.
You wouldn't normally consider permanently *gluing* the pieces together,
would you? Doing so destroys the flexibility that makes them useful, locking
you into a single, rigid design. If you wanted to change *anything*, you'd
likely have to break the structure apart.

Using composition in software but depending directly on [**concrete, volatile
classes**](Stable-vs-Volatile-Dependencies.md) is analogous to building with glued-together Lego bricks. Just as
glue makes the physical structure rigid and difficult (or impossible) to change
without breaking it, depending directly on specific, changeable
implementations creates tight coupling in your code.

```C#
public class OrderProcessor
{
    // Direct dependency - like using glue!
    private readonly ConcretePaymentGateway _paymentGateway =
        new ConcretePaymentGateway(); // If this changes, we break.
    private readonly ConcreteInventoryManager _inventoryManager =
        new ConcreteInventoryManager("some_config"); // If this changes...

    public void Process()
    {
        // ... uses _paymentGateway and _inventoryManager ...
    }
}
```

If that `ConcretePaymentGateway` needs to change (violating the Open-Closed
Principle for the `OrderProcessor`), or if you need to swap it for a
`StripePaymentGateway`, your `OrderProcessor` is 'stuck'. Changes become
difficult, ripple through the system, and increase risk. You've lost the
flexibility composition was meant to provide. This violates the **Dependency
Inversion Principle (DIP)**. (See:
[Using the new keyword](Using-the-new-keyword.md),
[Stable vs Volatile Dependencies](Stable-vs-Volatile-Dependencies.md)).

## The Solution: Composition Done Right (Using the Standard Connectors)

The proper way to use composition mirrors using the standard studs and tubes
of Legos – these connection points are the **abstractions** (interfaces or
abstract classes) in software.

```C#
public class OrderProcessor
{
    // Dependency on abstractions (standard connectors)
    private readonly IPaymentGateway _paymentGateway;
    private readonly IInventoryManager _inventoryManager;

    // Dependencies are provided (IoC/DI) - like snapping pieces together
    public OrderProcessor(
        IPaymentGateway gateway,
        IInventoryManager inventory)
    {
        _paymentGateway = gateway;
        _inventoryManager = inventory;
    }

    public void Process()
    {
        // ... uses _paymentGateway and _inventoryManager
        //     via their interfaces ...
    }
}
```

By depending on the `IPaymentGateway` interface (the standard connection
point), we can connect *any* implementation without modifying
`OrderProcessor`. This preserves flexibility, adheres to the **DIP**, and
enables the **Open Closed Principle (OCP)**.

## Conclusion

While inheritance has its place for modeling genuine specialization (like a
house of cards, carefully constructed), it often leads to fragile, tightly
coupled designs when overused. Composition, like building with Legos, offers
superior modularity and flexibility. However, to gain the full benefits, avoid
the "glue" of depending directly on concrete, volatile classes. Just as you'd
avoid glue unless you *never* wanted your Lego model to change again, avoid
these direct dependencies in your software. Prefer composing with abstractions
(interfaces or abstract classes) using the standard "connection points" to keep
your design adaptable, maintainable, and robust.

---

**See Also:**

* [Composition](Composition.md)
* [Inheritance](Inheritance.md)
* [Prefer Composition over Class Inheritance](Prefer-Composition-over-Class-Inheritance.md)
* [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
* [Open Closed Principle (OCP)](Open-Closed-Principle-OCP.md)
* [Liskov Substitution Principle (LSP)](Liskov-Substitution-Principle-LSP.md)
* [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md)
* [Stable vs Volatile Dependencies](Stable-vs-Volatile-Dependencies.md)
* [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md)
* [Using the new keyword](Using-the-new-keyword.md)
* [Coupling](Coupling.md)
* [Inversion of Control (IoC)](Inversion-of-Control-IoC.md) / [Dependency Injection (DI)](Dependency-Injection-DI.md)

