**1. Which principle is directly violated when you must modify existing switch-based code every time a new case is added?**

- [ ] Single Responsibility Principle  
- [ ] Liskov Substitution Principle  
- [x] Open-Closed Principle  
- [ ] DRY (Don’t Repeat Yourself)  

**2. What common problem do switch statements introduce, according to the discussion on explicit case analysis?**

- [ ] They obscure the program’s control flow.  
- [x] They encourage code duplication across the codebase.  
- [ ] They degrade runtime performance significantly.  
- [ ] They increase memory usage unpredictably.  

**3. When might a switch statement be considered acceptable practice?**

- [x] When it’s centralized in one location and the set of values is stable.  
- [ ] When it is spread across many modules and values change frequently.  
- [ ] When performance is critical and method calls must be avoided.  
- [ ] When it handles only error-reporting responsibilities.  

**4. Which technique is recommended as an alternative to using switch statements for handling different behaviors?**

- [ ] Using a global lookup table  
- [ ] Embedding conditional logic in a configuration file  
- [x] Applying polymorphism via subclass overrides  
- [ ] Relying on exception-based control flow  

**5. Which question is *not* one of the three you should ask yourself before replacing a switch with polymorphism?**

- [ ] Does it cause a lot of tight coupling?  
- [ ] Does it violate the Open-Closed Principle (OCP)?  
- [x] Does it improve runtime performance?  
- [ ] Does it violate the Single Responsibility Principle (SRP)?  

**6. Which of the following are consequences of overusing switch statements?**

- [ ] They encourage code duplication.  
- [ ] They increase tight coupling.  
- [ ] They violate the Single Responsibility Principle.  
- [x] All of the above.  

---

### Open-Response Questions

**7. Describe how polymorphism replaces the switch-based shape example.**  
*Bare minimum response:* Define an abstract `DoSomething()` method in `Shape` and have each subclass (`Square`, `Rectangle`, `Circle`) override it; then call `shape.DoSomething()` instead of using a switch.

**8. Explain why a switch in a logging method might still violate the Open-Closed Principle if logging requirements evolve.**  
*Bare minimum response:* Because adding new log levels or behaviors requires modifying the switch block rather than extending via new subclasses or handlers, which violates OCP.  

