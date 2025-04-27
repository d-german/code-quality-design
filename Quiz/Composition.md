# Multiple Choice Questions: Composition

## Question 1
How does the author, Matt Weisfeld, view composition in relation to the classical pillars of OOP (Encapsulation, Inheritance, Polymorphism)?
- [ ] As a less important concept than the classical three.
- [ ] As a replacement for inheritance.
- [x] As a core object-oriented concept alongside the classical three.
- [ ] As a direct consequence of polymorphism only.

## Question 2
What is the fundamental idea behind composition in object-oriented programming?
- [ ] Objects inheriting features from a parent class.
- [x] Objects being made up of, or containing, other objects.
- [ ] Objects having multiple forms or behaviors.
- [ ] Hiding the internal state of an object.

## Question 3
The article uses examples like a computer containing video cards and drives, or a car containing an engine and wheels. These illustrate which OOP concept?
- [ ] Inheritance
- [ ] Polymorphism
- [ ] Encapsulation
- [x] Composition

## Question 4
According to the article, an object that is composed of other objects included as fields can also be called a:
- [ ] Base object
- [ ] Abstract object
- [x] Compound, aggregate, or composite object
- [ ] Polymorphic object

## Question 5
The article mentions that a hard drive can be removed from one computer and installed in another. What characteristic of the hard drive (as an object) does this reinforce?
- [ ] That it must be an abstract class.
- [x] That it is a standalone object capable of existing independently.
- [ ] That it can only function inside a computer.
- [ ] That it primarily uses inheritance.

## Question 6
What combination of concepts does the article suggest can be powerful when building objects?
- [ ] Inheritance and Encapsulation only
- [ ] Inheritance and Aggregation
- [x] Composition and Polymorphism
- [ ] Encapsulation and Aggregation

# Free Response Questions: Composition

## Short Answer Question 1
In your own words, define composition as described in the article.
> Answer: Composition is the concept where an object is constructed by containing other objects as its parts or members (fields). It models a "has-a" relationship, like a car "has-an" engine or a computer "has-a" hard drive.

## Short Answer Question 2
Provide two examples of composition mentioned in the article.
> Answer: Examples mentioned include:
> 1. A television set containing a tuner and video display.
> 2. A computer containing video cards, keyboards, and drives.
> 3. A car containing an engine, wheels, steering wheel, and stereo.

## Short Answer Question 3
The author includes composition along with encapsulation, inheritance, and polymorphism as key object-oriented concepts. Why might composition be considered so fundamental? (Infer from the text).
> Answer: Composition is fundamental because it directly models how real-world objects are often built from smaller, distinct parts. It allows complex systems to be represented by assembling simpler, often reusable, objects, mirroring how we often structure things conceptually and physically (like a computer being made of drives, cards, etc.).

## Short Answer Question 4
What does the article suggest can be achieved by combining composition with polymorphism?
> Answer: By combining composition with polymorphism, an object can be composed of other objects where the composing object knows the *type* (interface or abstract base class) of the contained objects, but not their specific *implementation* (concrete class). This allows for greater flexibility, as different concrete implementations conforming to the expected type can be plugged in.