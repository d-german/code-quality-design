**1. Which sentence best encapsulates the Open-Closed Principle (OCP)?**

- [ ] Locked against both extension and modification.
- [x] Open for extension, closed for modification.
- [ ] Open to both extension and modification operations.
- [ ] Closed for extension, open for modification.

**2. Introducing a type check for `SpecialConcreteShape` in `SumAreas` most clearly signals a violation of which principle?**

- [ ] Single Responsibility Principle.
- [ ] Software Entropy concept.
- [x] Liskov Substitution Principle.
- [ ] Dependency Inversion Principle.

**3. In the original `AreaCalculator` example, which design mechanism allows new shapes to be handled without altering the `SumAreas` logic?**

- [x] Interface-based polymorphism through `IShape`.
- [ ] Global flags controlling shape behavior.
- [ ] Direct property access on each shape.
- [ ] Switch-case statements inside the loop.

**4. To keep a system easy to change when adding new behaviour, developers should primarily:**

- [ ] Rewrite existing modules from scratch each time.
- [ ] Duplicate classes with minor edits for new cases.
- [ ] Comment out obsolete lines and then proceed.
- [x] Add new code while leaving current code unchanged.

**5. Which item is explicitly listed as often violating the OCP?**

- [ ] Strategy pattern for JSON processing.
- [x] Explicit case analysis using `switch` or `if-else`.
- [ ] Dependency inversion abstractions.
- [ ] Abstract classes versus interfaces usage.

**6. Which principles are mentioned as related to the Open-Closed Principle?**

- [ ] Liskov Substitution Principle (LSP) only.
- [ ] Dependency Inversion Principle (DIP) only.
- [ ] Single Responsibility Principle (SRP) only.
- [x] All of the above.

---

### Open-Response Questions

1. Explain how adding the `if (shape is SpecialConcreteShape)` branch in `SumAreas` violates the Open-Closed Principle and describe the connection to the Liskov Substitution Principle.

2. Discuss how the Single Responsibility Principle and the Dependency Inversion Principle work together to minimize modifications when extending software functionality.

3. The generic `Max<T>` method is said to be “closed for modification but open for extension.” Describe why this is true and identify the language feature that makes it possible.