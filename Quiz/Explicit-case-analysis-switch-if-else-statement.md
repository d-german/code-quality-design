**1. What is a primary drawback of using explicit case analysis (switch statements) in code?**

- [ ] It reduces coupling between unrelated classes.
- [x] It encourages code duplication across multiple locations.
- [ ] It ensures classes remain closed for modification.
- [ ] It automatically delegates responsibilities to relevant objects.

**2. Which principle is directly violated when adding a new case to an existing switch requires modifying existing code?**

- [ ] Single Responsibility Principle (one class, one reason to change)
- [x] Open-Closed Principle (open for extension, closed for modification)
- [ ] Liskov Substitution Principle (subtypes must replace supertypes)
- [ ] Dependency Inversion Principle (depend on abstractions, not concretions)

**3. Under which scenario is using a switch statement considered acceptable?**

- [ ] When cases are scattered across multiple modules.
- [ ] When it switches on a large, evolving set of values.
- [x] When it’s centralized, uses a stable set, and shows cohesion.
- [ ] When responsibilities are split across different classes.

**4. In the logging example, the switch on log level maintains Single Responsibility because:**

- [x] Error handling is delegated to an external reporting service.
- [ ] Logging levels are defined in a central configuration file.
- [ ] Only one switch exists, so coupling remains low.
- [ ] Each case prints messages without any method calls.

**5. Which question helps determine if a switch should be replaced by polymorphism?**

- [ ] Does it improve execution performance under load?
- [x] Does it introduce tight coupling between components?
- [ ] Does it use descriptive names for all cases?
- [ ] Does it handle errors consistently across all branches?

**6. Which of the following are common issues associated with explicit case analysis (switch statements)?**

- [x] Code duplication, violation of the Open-Closed Principle, and increased coupling.
- [ ] Code duplication and violation of the Open-Closed Principle only.
- [ ] Violation of the Open-Closed Principle and increased coupling only.
- [ ] None of the above issues are associated with switch statements.

---

**Open-Response Questions**

**1. How can polymorphism replace a switch statement to reduce coupling, and what are the benefits?**
- **Bare minimum example response:**  
  Polymorphism moves behavior into subclasses; adding new types requires no change to existing code, reducing coupling and improving Open-Closed Principle compliance.

**2. Explain the role of the Open-Closed Principle in deciding whether to refactor a switch statement.**
- **Bare minimum example response:**  
  If adding new cases forces modification of the switch, it violates OCP; refactoring into extensible classes preserves OCP and prevents repeated changes.

**3. Describe the criteria that make a switch statement “good” according to the utility function example.**
- **Bare minimum example response:**  
  A good switch is centralized, operates on a limited stable set of values, and performs cohesive actions in a single location.
