**1. Which of the following is NOT a criterion for a stable dependency?**

- [ ] Executes deterministic algorithms without side effects.
- [x] Sends email or performs I/O operations.
- [ ] Maintains backward compatibility across versions.
- [ ] Requires no mocking, wrapping, or interception.

**2. Which scenario indicates a volatile dependency?**

- [ ] Calling a deterministic mathematical library.
- [ ] Using a simple DTO for data transfer.
- [x] Depending on a relational database instance.
- [ ] Relying on the built-in `String` class.

**3. Which statement best describes the treatment of POCOs, DTOs, and VMs?**

- [ ] They often require complex mocking for tests.
- [x] They should be tightly coupled and created directly.
- [ ] They must implement interfaces to allow injection.
- [ ] They are long-lived objects needing runtime interception.

**4. What is the recommended technique for adding behavior to a pure DTO while keeping it immutable?**

- [ ] Subclass the DTO and override its behavior.
- [x] Define extension methods that operate on the record.
- [ ] Embed methods directly within the data record.
- [ ] Use a separate service class with dependencies.

**5. Which of the following are examples of volatile dependencies?**

- [ ] Dependencies still under development.
- [ ] Services that yield nondeterministic output.
- [ ] Requirements for runtime environment configuration.
- [x] All of the above.

**Open Response Questions**

**6. Explain the key differences between stable and volatile dependencies, citing at least two criteria for each and providing one example of each.**

Bare minimum example response:  
Stable dependencies are types like String or Array that are backward compatible and deterministic; volatile dependencies include things like database connections or time services that require setup and may change unpredictably.

**7. Discuss why POCOs, DTOs, and VMs can be tightly coupled to consuming code without violating maintainability principles.**

Bare minimum example response:  
They contain only data with no behavior to mock or replace, are short-lived, and thus tight coupling doesn’t hinder testing or extension.

**8. Describe how functional programming concepts inform the addition of behavior to DTOs, and outline the benefits of using extension methods for this purpose.**

Bare minimum example response:  
State stays in an immutable DTO; behavior lives in extension methods, enabling clear separation of concerns and easier testing without altering the DTO itself.
