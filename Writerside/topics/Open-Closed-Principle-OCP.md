# Open Closed Principle (OCP)

A software module should be open for extension but closed for modification. (2002 Martin 99)

> The OCP is at the heart of many of the claims made for OOD. (2002 Martin 125)

The gist is that for software systems to be easy to change, they must be designed to allow the behavior of those systems
to be changed by adding new code, rather than changing existing code.

- **OCP relates to the Single Responsibility Principle (SRP), and Dependency Inversion Principle (DIP).**

Clearly, some new code must be written. But how much old code will have to change?

- We should reduce the amount of changed code to the barest minimum. Ideally, zero.
- How? By properly separating the things that change for different reasons (SRP), and then organizing the dependencies
  between those things properly (DIP). (2017 Martin 70)

If class A should be protected from changes in class B, then class B should depend on class A.

See **Coupling**.

---
See Also:
- [SOLID Design Principles](SOLID-Design-Principles.md)
- [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md)
- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Liskov Substitution Principle (LSP)](Liskov-Substitution-Principle-LSP.md) (enabler for OCP)
- [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md) (mechanism for OCP)
- [Polymorphism](Polymorphism.md) (mechanism for OCP)
- [Strategy Pattern Implementation for JSON Processing](Strategy-Pattern-Implementation-for-JSON-Processing.md) (demonstrates OCP)
- [Decorator Pattern](Decorator-Pattern.md) (demonstrates OCP)
- [Explicit case analysis (switch/if-else statement)](Explicit-case-analysis-switch-if-else-statement.md) (often violates OCP)
- [Coupling](Coupling.md) (OCP aims for looser coupling)