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

Composition can be combined with polymorphism to compose your object out of other objects, whose type (interface) you
know, but not the actual class (implementation).

---
See Also:
- [Inheritance](Inheritance.md)
- [Prefer Composition over Class Inheritance](Prefer-Composition-over-Class-Inheritance.md)
- [Polymorphism](Polymorphism.md)
- [Pillars of OOP](Pillars-of-OOP.md)
- [Design Relationships](Design-Relationships.md)