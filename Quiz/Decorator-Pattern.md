**1. What is the primary intent of the Decorator pattern?**

- [ ] Provide a simplified interface to multiple subsystems.
- [x] Add responsibilities to objects without subclassing them.
- [ ] Generate factory classes for object creation at runtime.
- [ ] Enforce compile-time type safety across all components.

**2. Which relationship describes a decorator’s structure?**

- [ ] It inherits behavior and state from its base class.
- [x] Implements the interface and holds a reference to another object implementing it.
- [ ] It delegates calls through a static helper utility class.
- [ ] It replaces the original component with a modified subclass.

**3. Which benefit is provided by using decorators?**

- [ ] They modify existing classes without subclassing them.
- [ ] They hide the original interface behind a simpler API.
- [x] They allow adding behavior by wrapping objects dynamically.
- [ ] They improve runtime performance predictably.

**4. How does the Decorator pattern support the Open/Closed Principle?**

- [ ] By allowing changes to existing classes through inheritance.
- [x] By extending behavior via new decorators without modifying code.
- [ ] By enforcing that classes be closed for extension at compile time.
- [ ] By bundling all behaviors into a single overridable base class.

**5. Which analogy is commonly used to illustrate decorators?**

- [ ] A house of cards that collapses easily under change.
- [x] Russian Matryoshka dolls nesting functionality layers.
- [ ] Lego bricks assembled into rigid structures.
- [ ] Power strips with multiple plug-in extension outlets.

**6. Which of the following are benefits of applying the Decorator pattern?**

- [ ] Simplifies client code by hiding complexity of subsystems.
- [ ] Supports adding and removing features by changing layers.
- [ ] Maintains separation of concerns across components.
- [x] All of the above.

**Open Response Questions**

**7. Explain how a decorator differs from subclassing for extending behavior.**

Bare minimum example response:  
A decorator wraps an object at runtime to add features without creating subclasses, while subclassing requires compile-time inheritance and alters class hierarchies.

**8. Describe how multiple decorators can be stacked and one benefit of doing so.**

Bare minimum example response:  
Decorators can wrap other decorators sequentially, forming a chain that layers behaviors; this allows combining independent enhancements flexibly.

**9. Discuss a potential drawback of overusing the Decorator pattern and how to mitigate it.**

Bare minimum example response:  
Excessive decorators can lead to many small classes that are hard to manage; mitigation includes grouping related features or using fewer, more focused decorators.
