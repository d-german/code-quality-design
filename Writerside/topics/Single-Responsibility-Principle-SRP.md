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

![](srp-violation.png)

- The `calculatePay()` method is specified by the accounting department, which reports to the CFO.
- The `reportHours()` method is specified and used by the human resources department, which reports to the COO.
- The `save()` method is specified by the database administrators (DBAs), who report to the CTO.

---

## One Possible Solution

![](srp-possible-solution.png)

---

## Another Possible Solution Uses the Facade Pattern

![](srp-possible-solution-facade.png)

> **Facade Pattern:** Provide a unified interface to a set of interfaces in a subsystem. (GOF 185)

Relates to the **Interface Segregation Principle (ISP)**.

---
See **Cohesion**.