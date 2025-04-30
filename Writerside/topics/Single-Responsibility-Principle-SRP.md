# Single Responsibility Principle (SRP)

In the most general form, it applies to both classes and components.

- **Gather together** those things that change at the same time and for the same reasons. **Separate** those things that
  change at different times or for different reasons. (2017 Martin 107)

The hard thing to remember about the SRP is that it is based on likely patterns of change, not dependency or functional
relationships within the code.

- A class should have one, and only one, reason to change.
- A class should be responsible to one, and only one, user or stakeholder.
- A class should be responsible to one, and only one, actor.
- Separate code that supports different actors.

> **Note:** This does not mean that every class should do just one thing.

Of all the SOLID principles, the Single Responsibility Principle (SRP) might be the least well understood. That’s likely
because it has a particularly inappropriate name. It is too easy for programmers to hear the name and then assume that
it means that every module should do just one thing. Make no mistake, there is a principle like that. A method should
do one, and only one, thing. We use that principle when we are refactoring large methods into smaller methods; we
use it at the lowest levels. But it is not one of the SOLID principles—it is not the SRP. (Martin 2017)

---

## Example SRP Violation

<img src="srp-violation.png" width="512" alt=""/>

- The `calculatePay()` method is specified by the accounting department, which reports to the CFO.
- The `reportHours()` method is specified and used by the human resources department, which reports to the COO.
- The `save()` method is specified by the database administrators (DBAs), who report to the CTO.

---

## One Possible Solution

<img src="srp-possible-solution.png" width="512" alt=""/>

---

## Another Possible Solution Uses the Facade Pattern

<img src="srp-possible-solution-facade.png" width="512" alt=""/>

> **Facade Pattern: ** Provide a unified interface to a set of interfaces in a subsystem. (GOF 185)

A Facade's public interface may appear to serve multiple actors, but its single responsibility is
coordination—delegating work to specialized classes rather than implementing complex logic itself. Its adherence to SRP
comes from having only one reason to change: the orchestration of its collaborators.

---
See Also:

- [SOLID Design Principles](SOLID-Design-Principles.md)
- [DRY Principle](DRY-Principle.md) (SRP helps achieve DRY)
- [Cohesion](Cohesion.md) (High cohesion often aligns with SRP)
- [Coupling](Coupling.md) (Violating SRP can increase coupling)
- [Interface Segregation Principle (ISP)](Interface-Segregation-Principle-ISP.md) (Related principle of separation)
- [The Facade Pattern with the Single Responsibility Principle](The-Facade-Pattern-with-the-Single-Responsibility-Principle.md) (
  Facade as solution)
- [How do we write a test for a private method](How-do-we-write-a-test-for-a-private-method.md) (SRP violation leads to
  testing issues)
- [Open Closed Principle (OCP)](Open-Closed-Principle-OCP.md) (SRP helps enable OCP)
- [The Dependency Rule](The-Dependency-Rule.md) (Separating by change rate/actor aligns with SRP)