# Composition

Historically, object-oriented languages are defined by the following: encapsulation, inheritance, and polymorphism (
“classical” OO). Thus, if a language does not implement all of these, it is generally not considered completely
object-oriented. Along with these three terms, I always include composition in the mix; thus, my list of object-oriented
concepts looks like this:

- **Encapsulation**
- **Inheritance**
- **Polymorphism**
- **Composition**

  Matt Weisfeld

It is natural to think of objects as containing other objects. A television set contains a tuner and video display. A
computer contains video cards, keyboards, and drives. The computer can be considered an object unto itself, and a flash
drive is also considered a valid object. You could open up the computer and remove the hard drive and hold it in your
hand. In fact, you could take the hard drive to another computer and install it. The fact that it is a standalone object
is reinforced because it works in multiple computers.

Most people would think it natural for a car to contain an engine. However, a car contains many objects besides an
engine, including wheels, a steering wheel, and a stereo. Whenever a particular object is composed of other objects, and
those objects are included as object fields, the new object is known as a compound, an aggregate, or a composite object.

![Composition](composition.png)

Composition can be powerfully combined with polymorphism. This means an object can contain parts referenced by their
abstract type (like an interface or an abstract base class) instead of their specific concrete implementation. For
instance, a `Car` might use an `IEngine` interface to interact with its engine. This allows different engine
implementations (a `V6Engine`, an `ElectricMotor`) to be swapped in or out, as long as they satisfy the `IEngine`
contract, without needing to change the `Car` class itself. The `Car` interacts with whatever engine part it holds using
the common interface methods defined by `IEngine`.

---
See Also:

- [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md)
- [Decorator-Pattern](Decorator-Pattern.md)
- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Design Relationships](Design-Relationships.md)
- [Inheritance vs Composition Analogy](Inheritance-vs-Composition-Analogy.md)
- [Inheritance](Inheritance.md)
- [Pillars of OOP](Pillars-of-OOP.md)
- [Pillars of OOP (Encapsulation, Abstraction)](Pillars-of-OOP-Encapsulation-Abstraction.md)
- [Polymorphism](Polymorphism.md)
- [Prefer Composition over Class Inheritance](Prefer-Composition-over-Class-Inheritance.md)
- [Program to an interface; not an Implementation (GOF 18)](Program-to-an-interface-not-an-Implementation-GOF-18.md)
- [Strategy-Pattern-Implementation-for-JSON-Processing](Strategy-Pattern-Implementation-for-JSON-Processing.md)
