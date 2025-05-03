**1. What does the Interface Segregation Principle primarily advise designers to do?**

- [x] Keep interfaces small by grouping related methods so clients aren’t forced to depend on unused methods.
- [ ] Merge related interfaces into a single large interface to simplify client implementation.
- [ ] Require all derived classes to implement every interface member regardless of usage.
- [ ] Centralize unrelated methods into a single abstract base class to encourage reuse.

**2. What are alternative names for a fat interface?**

- [ ] Interface Pollution.
- [ ] Fat Interface.
- [ ] Bloated Interface.
- [x] A & B only.

**3. In the poor design example, why does the Car class violate the Interface Segregation Principle?**

- [ ] It groups driving and flying behaviors into separate, lean interfaces.
- [ ] It restricts classes to a single responsibility despite client needs.
- [x] It implements methods unrelated to its core driving responsibility.
- [ ] It forces classes to depend on facade patterns unnecessarily.

**4. Which interfaces are introduced in the better design to replace IVehicle?**

- [x] IDrive and IFly.
- [ ] IVehicle and IFly.
- [ ] IDrive and IVehicle.
- [ ] None of the above.

**5. What is the direct benefit of applying the Interface Segregation Principle?**

- [ ] Improves compile times by merging redundant interfaces.
- [x] Reduces dependency on methods that clients do not use.
- [ ] Forces all behaviors into a single, monolithic interface.
- [ ] Increases coupling between unrelated modules unnecessarily.

**1. How does a fat interface with low cohesion violate lead to violations of the Single Responsibility Principle?**

- [ ] It delegates each set of behaviors to dedicated helpers.
- [x] It forces implementers to depend on many unrelated behaviors.
- [ ] It groups each responsibility into separate, lean interfaces.
- [ ] It isolates behaviors to reduce coupling across modules.

**2. Why does a pure Facade not break the Single Responsibility Principle?**


**Open Response Questions**

**6. How does splitting a fat interface into smaller interfaces help decrease dependency among classes?**  
Example response: It ensures clients only depend on methods they use, reducing coupling and simplifying implementations.

**7. Describe the violation of the Interface Segregation Principle in the poor design example and explain how the better design resolves it.**  
Example response: IVehicle forced Car to implement an unused Fly method; splitting into IDrive and IFly lets classes implement only the behaviors they need.
