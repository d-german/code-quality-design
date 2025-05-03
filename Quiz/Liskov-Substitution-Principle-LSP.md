**1. What is the primary requirement stated by the Liskov Substitution Principle?**

- [ ] That subclasses must be sealed to prevent unintended inheritance.
- [ ] That inherited methods may redefine semantics to suit subclass needs.
- [x] That subtypes must be substitutable for their base types without affecting correctness.
- [ ] That composition should always be preferred over inheritance.

**2. Which practice is a symptom of violating the Liskov Substitution Principle?**

- [ ] Using virtual methods to achieve dynamic binding at runtime.
- [ ] Sealing base classes to prevent unintended inheritance.
- [x] Requiring explicit type checks and case analysis on subtypes.
- [ ] Delegating behavior via composition of private members.

**3. When designing a Stack to adhere to the Liskov Substitution Principle, which approach is recommended?**

- [ ] Inherit from List and implement Push, Pop by overriding Add and Remove.
- [ ] Seal the Stack class to prevent any extensions that alter its behavior.
- [ ] Expose both LIFO and arbitrary-position methods in the public interface.
- [x] Compose a private List instance and implement Push, Pop by delegating to it.

**4. Which rule regarding preconditions and postconditions must an overriding method follow to comply with the Liskov
Substitution Principle?**

- [x] It must have equal or weaker preconditions and equal or stronger postconditions compared to the base.
- [ ] It must introduce stronger preconditions and weaker postconditions to enforce stricter contracts.
- [ ] It can freely alter preconditions and postconditions as long as both remain consistent.
- [ ] Both preconditions and postconditions must be strictly stronger than in the base class.

**5. According to principles of inheritance motivations, which activity is generally not a goal of inheritance?**

- [ ] Letting clients treat many concrete types via one abstraction.
- [ ] Sharing code implementation without anticipating substitution.
- [x] Reducing coupling between unrelated classes by hiding dependencies.
- [ ] Facilitating code reuse for engine callbacks and editor integration.

**6. Which of the following are direct consequences of adhering to the Liskov Substitution Principle?**

- [ ] It supports the Open Closed Principle by enabling safe extension.
- [ ] It prevents fragile code by disallowing substitution that breaks behavior.
- [ ] It requires subtypes to fulfill base type contracts uniformly.
- [x] All of the above.

**7.** Describe a scenario in which violating the Liskov Substitution Principle leads to fragile or hard-to-maintain
code, and explain how you would refactor the design to restore substitutability.

**8.** Explain why a Square is not a valid subtype of Rectangle under the Liskov Substitution Principle, and propose an
alternative class design that preserves both rectangle and square behaviors without violating LSP.

**9.** Discuss the roles of preconditions and postconditions in maintaining substitutability according to the Liskov
Substitution Principle, and illustrate how a method override should correctly apply these conditions.

Penguin inherits fly() but can’t fly, so clients get runtime errors. Refactor by extracting IFlyable; only flying birds
implement it, keeping Bird and Penguin substitutable.

Square overrides setWidth/setHeight to keep sides equal, violating clients’ expectations of independent dimensions.
Instead, define a Shape interface and implement separate Rectangle and Square classes.

An override must not tighten preconditions or loosen postconditions. It must accept at least the same inputs and
guarantee at least the same outputs as the base method.