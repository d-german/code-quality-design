# Quiz: Abstract Classes vs Interfaces

These questions are based on the content provided in the article "**Abstract-Classes-vs-Interfaces.md**".

## Multiple Choice Questions

**Question 1:**
According to the article "**Abstract-Classes-vs-Interfaces.md**", what is a primary difference between an abstract class and an interface in C# (prior to C# 8.0)?

*   [ ] Only interfaces can be inherited by structs.
*   [ ] Only abstract classes can have method signatures without implementation.
*   [x] Abstract classes can contain instance fields and method implementations, while interfaces traditionally cannot.
*   [ ] Interfaces support multiple inheritance, while abstract classes do not.

**Question 2:**
The article mentions simulating interfaces in C++ using what construct?

*   [ ] Virtual classes
*   [ ] Template classes
*   [x] Pure abstract classes (classes with only pure virtual functions)
*   [ ] Friend classes

**Question 3:**
In C#, if a class contains at least one method declared with the `abstract` keyword, what else must be true about the class itself?

*   [ ] It must be declared `sealed`.
*   [ ] It must provide a public constructor.
*   [x] It must also be declared `abstract`.
*   [ ] It must implement at least one interface.

**Question 4:**
What keyword is used in C# to provide an optional implementation in an abstract class that derived classes *can* override, but don't *have* to?

*   [ ] `abstract`
*   [x] `virtual`
*   [ ] `override`
*   [ ] `base`

**Question 5:**
The article "**Abstract-Classes-vs-Interfaces.md**" states that implementing an interface is sometimes referred to as establishing which kind of relationship?

*   [ ] "is-a"
*   [x] "can-do"
*   [ ] "has-a"
*   [ ] "uses-a"

**Question 6:**
Which of the following is NOT possible according to the C# rules described in the article?

*   [ ] A class implementing multiple interfaces.
*   [x] A class inheriting from multiple base classes.
*   [ ] A struct implementing an interface.
*   [ ] An abstract class containing `virtual` methods.

**Question 7:**
According to the article, when might you typically prefer using an interface over an abstract class?

*   [ ] When you want to provide substantial default implementation to be shared.
*   [ ] When defining a core identity ("is-a" relationship) for a set of classes.
*   [x] When unrelated classes need to share common method signatures (capabilities) without inheriting implementation.
*   [ ] When you need to include instance fields in the base type.

**Question 8:**
The article "**Abstract-Classes-vs-Interfaces.md**" suggests that violating the Liskov Substitution Principle (LSP) might be easier when using abstract classes compared to interfaces. Why?

*   [ ] Because interfaces cannot be instantiated.
*   [x] Because derived classes might override inherited implementation in a way that breaks the expected behavior of the base abstract class.
*   [ ] Because abstract classes do not support polymorphism.
*   [ ] Because interfaces enforce stricter type checking.

**Question 9:**
What potential design principle violation does the article associate with "fat interfaces" (interfaces with too many unrelated methods)?

*   [ ] Open Closed Principle (OCP)
*   [ ] Liskov Substitution Principle (LSP)
*   [ ] Dependency Inversion Principle (DIP)
*   [x] Interface Segregation Principle (ISP)

**Question 10:**
What capability was introduced in C# 8.0 regarding interfaces, as mentioned in the article?

*   [ ] Interfaces can now contain instance fields.
*   [x] Interfaces can define default implementations for methods.
*   [ ] Interfaces can inherit from abstract classes.
*   [ ] Interfaces can have private constructors.

## Free Response Questions

**Short Answer Question 1:**
In your own words, based on the article "**Abstract-Classes-vs-Interfaces.md**", what is the fundamental difference between inheriting from an abstract class and implementing an interface in C# regarding *what* is inherited or implemented?

> **Answer:** Inheriting from an abstract class means inheriting *both* the contract (abstract method signatures) and potentially *shared implementation* (virtual/concrete methods and fields). Implementing an interface means agreeing to fulfill a *contract* (method signatures) without inheriting any implementation (traditionally) or instance state.

**Short Answer Question 2:**
The article mentions the GOF principle "Program to an interface; not an implementation". How do C# abstract classes and interfaces both help in adhering to this principle?

> **Answer:** Both allow you to define variables or parameters using the abstract class type or the interface type, rather than a specific concrete class type. This decouples the client code from the specific implementation details, allowing different concrete subclasses (that inherit the abstract class or implement the interface) to be used polymorphically without changing the client code.

**Short Answer Question 3:**
Explain the concept of a "pure abstract class" in C++ as described in the article "**Abstract-Classes-vs-Interfaces.md**", and how it relates to the concept of an interface in languages like C#.

> **Answer:** A "pure abstract class" in C++ is an abstract class that contains *only* pure virtual functions (functions declared with `= 0` and having no implementation). This essentially defines a pure contract or signature, much like an interface does in C# or Java, forcing derived classes to provide implementations for all methods.

**Short Answer Question 4:**
According to the article, why might providing both an interface and an abstract base class implementation (like `IShape` and an `abstract class Shape : IShape`) be a useful pattern in framework design?

> **Answer:** This pattern offers flexibility to consumers. Developers who need the shared/default implementation provided by the abstract class can inherit from it directly. Those who cannot inherit from that specific base class (perhaps due to needing a different base class or being a struct) or who want to provide a completely custom implementation can instead choose to implement only the interface.