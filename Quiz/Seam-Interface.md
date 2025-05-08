**1. What is the primary purpose of a Seam  
Interface in software design?**

- [ ] Improve runtime performance of legacy classes.  
- [x] Isolate hard-to-test dependencies for easier mocking.  
- [ ] Generate user‐friendly error messages automatically.  
- [ ] Enforce security policies at application boundaries.

**2. According to Michael Feathers, a “seam”  
is best described as…**

- [ ] A database transaction wrapper.  
- [ ] An automated deployment script.  
- [x] A point in code that can be altered for testing.  
- [ ] A logging framework integration.

**3. Which scenario below best justifies creating  
a Seam Interface?**

- [ ] Replacing a well-tested `String` class.  
- [x] Wrapping a legacy class without its own interface.  
- [ ] Refactoring pure functions for readability.  
- [ ] Adding comments to clarify complex algorithms.

**4. In the example, `DocumentProcessor` receives  
`IMyDocumentDataService` via constructor injection.  
Which principle does this illustrate?**

- [ ] Law of Demeter.  
- [ ] Least Privilege.  
- [ ] Open/Closed Principle.  
- [x] Dependency Injection.

**5. Which of the following are direct benefits of  
using a Seam Interface?**

- [ ] Simplifies mocking in unit tests.  
- [ ] Reduces coupling to volatile classes.  
- [ ] Enables replacement of third-party libraries.  
- [x] All of the above.

**6. When testing `DocumentProcessor`, the mock  
service is verified with `Times.Once()`. This practice  
primarily ensures…**

- [ ] Thread-safety across multiple calls.  
- [x] The collaborator is invoked exactly once.  
- [ ] Database consistency after commits.  
- [ ] None of the above.

---

### Open-Response Questions

1. **Explain how a Seam Interface helps manage  
volatile dependencies while maintaining testability.**  
*Example response (succinct):*  
> By wrapping a volatile class in an interface, client code depends  
> on the seam instead of the changing implementation. Tests can inject  
> mocks, so changes in the concrete class don’t break test suites.

2. **Describe the relationship between Seam  
Interfaces and the Dependency Inversion Principle (DIP).**  
*Example response (succinct):*  
> DIP states that high-level modules should depend on abstractions.  
> A Seam Interface provides that abstraction, allowing high-level logic  
> to remain stable even if low-level legacy code evolves.

3. **Outline the steps needed to replace  
`RealDocDataProvider` with a new data source while  
minimizing impact on existing tests.**  
*Example response (succinct):*  
> Implement a new class that fulfills `IMyDocumentDataService`, inject  
> it where needed, run existing tests to confirm behavior, then retire  
> the old provider once parity is verified.

