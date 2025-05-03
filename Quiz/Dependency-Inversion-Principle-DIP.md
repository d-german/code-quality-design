**1. Under the Dependency Inversion Principle, when is it acceptable for high‐level modules to depend on concrete types?**

- [ ] When the concrete type is treated as a general utility dependency across multiple abstraction layers.
- [x] When the concrete type represents a stable, non-volatile component such as the String class.
- [ ] When the concrete type belongs to a third-party library with predictable, versioned releases.
- [ ] When the concrete type is instantiated within the application’s startup configuration code.  

**2. In the context of the Dependency Inversion Principle, which keyword usage is often considered a code smell?**

- [ ] `abstract` keyword indicating no concrete implementation.
- [ ] `interface` keyword signifying type abstraction.
- [ ] `using` keyword in import or namespace declarations.
- [x] `new` keyword indicating instantiation of concrete types.

**3. Why are interfaces said to be less volatile than implementations, according to DIP?**

- [x] Because changes to implementations rarely require modifying the interface.
- [ ] Because implementations are declared volatile in the system type hierarchy.
- [ ] Because interfaces enforce compile-time type checking across different implementations.
- [ ] Because implementations never change once they are defined in code.

**4. In which part of the application is it generally acceptable to reference concrete classes according to DIP guidelines?**

- [ ] Business logic layer of the application.
- [ ] Unit test classes for testing behavior.
- [x] The application entry point or Main function.
- [ ] Data access layer for database operations.

**5. Which of the following statements about the Dependency Inversion Principle are true?**

- [ ] Abstractions should not depend on details.
- [ ] Details should depend on abstractions.
- [ ] Constructors are considered implementation details.
- [x] All of the above.

---

**Open Response Questions**

**1. Explain why constructors and the new keyword are considered implementation details and how that relates to the Dependency Inversion Principle.**
- **Bare minimum example response:** Constructors instantiate specific implementations, so using the new keyword directly ties code to concrete classes and violates the rule to depend only on abstractions.

**2. Describe a situation where it is acceptable to reference concrete classes despite the DIP guidelines, and justify why.**
- **Bare minimum example response:** In the application entry point or Main function, concrete classes may be instantiated to wire up dependencies without affecting abstraction layers, keeping the rest of the code decoupled.

**3. Discuss how depending on abstractions rather than implementations enhances system flexibility and maintainability.**
- **Bare minimum example response:** Relying on abstractions allows swapping or updating concrete implementations independently, reducing coupling and simplifying future changes.