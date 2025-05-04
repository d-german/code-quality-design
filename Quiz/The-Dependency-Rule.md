**1. What are the two major elements that software systems can be decomposed into according to the Dependency Rule?**

- [ ] Entities and use cases.
- [ ] High-level and low-level modules.
- [x] Policy and details.
- [ ] Business rules and processes.

**2. According to the Dependency Rule, how should details relate to policies?**

- [ ] Policies should depend on details.
- [x] Details should depend on policies.
- [ ] Details should be merged with policies.
- [ ] Policies should implement details.

**3. Which interface location ensures correct inversion of control between high-level and low-level modules?**

- [ ] Declared in the low-level module.
- [x] Declared in the high-level module.
- [ ] Reflection at runtime.
- [ ] Defined in a separate infrastructure component.

**4. Which of the following are recommended practices for passing data across component boundaries according to the Dependency Rule?**

- [ ] Use simple data structures such as structs or DTOs.
- [ ] Avoid passing entity objects or database rows directly.
- [ ] Avoid using framework-generated low-level models in high-level modules.
- [x] All of the above.

**5. In the notation used for dependencies, what does the open arrow represent?**

- [ ] Any type of dependency.
- [ ] Composition relationship.
- [ ] Aggregation relationship.
- [x] Inheritance.

**6. Why are entities placed at a higher level than use cases?**

- [ ] Use cases generalize across multiple applications.
- [ ] Entities depend on specific user inputs.
- [ ] Use cases implement universal business policies.
- [x] Entities encapsulate general critical business rules across applications.


**Open-Response Questions**

**1. Explain the distinction between policy and details in the context of dependency management.**

*Bare minimum response:*  
Policy embodies business rules and has no knowledge of implementation details; details include IO, databases, frameworks, and do not affect the core business rules.

**2. Describe how the Dependency Inversion Principle is applied to manage the flow of control between high-level and low-level modules.**

*Bare minimum response:*  
An interface is declared in the high-level module and implemented by the low-level module, so high-level code depends on the interface rather than on concrete details.

**3. Discuss why inheritance is considered the strongest form of dependency and how it should be used carefully.**

*Bare minimum response:*  
Inheritance tightly couples subclasses to their base class’s implementation, so it should only be used when a true “is-a” relationship exists to avoid rigidity.
