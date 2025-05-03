**1. What is the primary role of the Facade pattern?**

- [ ] To enforce uniform coding standards across modules.
- [ ] To directly expose all internal services to clients.
- [x] To provide a simplified interface hiding subsystem complexity.
- [ ] None of the above.

**2. Which of these is a key benefit of using a Facade?**

- [x] Reduces client dependency on multiple subsystem components.
- [ ] Guarantees faster execution of subsystem operations.
- [ ] Increases visibility of internal module interactions.
- [ ] None of the above.

**3. In the building analogy for the Facade pattern, the facade is most like:**

- [ ] The wiring hidden behind walls.
- [x] The front entrance providing a clean view.
- [ ] The roof covering all structural elements.
- [ ] The plumbing beneath the floor.

**4. How does the Facade pattern relate to the Single Responsibility Principle?**

- [x] It centralizes orchestration without mixing subsystem logic.
- [ ] It mandates each class implement only one method.
- [ ] It merges subsystem and client responsibilities into one.
- [ ] It requires classes to have multiple responsibilities.

**5. Which of the following are benefits of applying the Facade pattern?**

- [ ] Simplifies client code by hiding subsystem complexity.
- [ ] Decouples client from subsystem implementation details.
- [ ] Centralizes orchestration of multiple components.
- [x] All of the above.

**6. What is a potential drawback of using a Facade improperly?**

- [ ] It can make subsystems completely inaccessible.
- [x] It may become a god object if given too many tasks.
- [ ] It eliminates the need for testing of subsystems.
- [ ] None of the above.

**Open Response Questions**

**7. Explain how the Facade pattern improves usability and maintainability in software systems.**

Bare minimum example response:  
By hiding complex subsystem interactions behind one interface, it lets clients call simple methods and reduces coupling, making code easier to change.

**8. Discuss how the Facade pattern complements the Single Responsibility Principle.**

Bare minimum example response:  
The facade has the single responsibility of orchestrating tasks, while subsystems handle their own logic, keeping concerns separated.

**9. Compare calling subsystems directly versus using a Facade, and describe trade-offs of each approach.**

Bare minimum example response:  
Direct calls give full control but increase coupling and complexity in client code; a facade simplifies calls but can obscure subsystem details.
