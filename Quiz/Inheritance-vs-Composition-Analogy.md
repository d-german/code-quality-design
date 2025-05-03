**1. Which analogy best represents inheritance in object-oriented design?**

- [ ] Building with Lego bricks using modular connectors.
- [ ] Connecting parts via standard studs and tubes.
- [x] Stacking playing cards to form a fragile structure.
- [ ] Snapping modules together through defined interfaces.

**2. Which outcome is NOT a typical problem of deep inheritance hierarchies?**

- [ ] Fragile dependencies break when the base class changes.
- [ ] Tight coupling causes ripple effects on refactoring.
- [ ] Single foundation limits multiple base class inheritance.
- [x] Easy localized changes without affecting other parts.

**3. Which snippet illustrates composition done wrong (the “glued” model)?**

- [ ] IPaymentGateway provided through constructor injection.
- [x] ConcretePaymentGateway instantiated directly inside OrderProcessor.
- [ ] Dependency fetched from DI container at runtime.
- [ ] OrderProcessor extends PaymentProcessor base class.

**4. Which combination of principles is upheld by proper composition using abstractions and DI?**

- [ ] Liskov Substitution Principle only.
- [ ] Single Responsibility Principle only.
- [x] Dependency Inversion & Open-Closed Principles.
- [ ] None of these design principles apply.

**5. What are benefits of building with Lego-style composition?**

- [ ] Modularity aligning with the Single Responsibility Principle.
- [ ] Flexibility to swap components without system-wide changes.
- [ ] Loose coupling achieved through standardized connection points.
- [x] All of the above.

**Open Response Questions**

**6. Explain the house-of-cards and Lego analogies, listing two drawbacks of inheritance and two benefits of composition.**

Bare minimum example response:  
Inheritance is like a house of cards—fragile when base classes change and rigid to refactor. Composition is like Lego—modular and flexible, allowing easy swapping of parts and loose coupling.

**7. Describe what happens when composition is misused by depending directly on concrete classes, and explain how this violates the Dependency Inversion Principle.**

Bare minimum example response:  
Gluing Lego pieces represents instantiating concrete classes inside a consumer, which locks the design and breaks DIP by making high-level modules depend on low-level implementations.

**8. Outline how using interfaces and IoC/DI enables proper composition and which design principles this supports.**

Bare minimum example response:  
By snapping components together via interfaces and injecting implementations, you preserve DIP and OCP without modifying existing code.
