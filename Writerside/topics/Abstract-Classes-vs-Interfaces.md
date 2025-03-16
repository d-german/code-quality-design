# Abstract Classes vs Interfaces

Compare and contrast abstract classes and interfaces. Why would you use an abstract class? Why would you use an interface?

One way to see the difference between the two is to compare programming languages that have explicit interfaces such as Java and C# and those that do not, such as C++. C++ only has classes. In C++, you can simulate interfaces by using pure abstract classes. (GOF 17) Pure abstract classes in C++ are abstract classes that have only pure virtual functions. A pure virtual function is a function that has no implementation or body; it is a pure signature. A virtual function is "made pure" by the "pseudo initializer" `=0` (Stroustrup 598):

```cpp
// C++ "Interface" Using Pure Abstract Class

class Shape
{
public:
    virtual ~Shape() = default;
    virtual Point center() const = 0;
    virtual void move(Point to) = 0;
    virtual void draw() const = 0;
    virtual void rotate(int angle) = 0;
};

```

Some modern languages do not have Abstract Classes or Interfaces.  
This is true for the Rust programming language, which also does not have inheritance.  
Rust has a concept called Traits, which is like interfaces.

> Note that there is nothing keeping a developer from updating `Shape` to contain a non-pure virtual function, but in that case it would no longer be an "Interface".

----------

## C# “Pure Abstract Class”

How would we write a “pure abstract class” in C#? In C#, we declare a pure virtual function or method using the **abstract** keyword modifier. If we declare any of a class's methods `abstract`, then we must also declare the containing class as `abstract`. So the equivalent Pure Abstract class looks like this:

```C#
// C# "Interface" Using an Abstract Class

public abstract class Shape
{
    public abstract Point Center();
    public abstract void Move(Point to);
    public abstract void Draw();
    public abstract void Rotate(int angle);
}

```

Like before, there is nothing stopping this class from being updated to contain virtual methods (i.e., methods that have implementation).

----------

### C# Method Declared as `virtual`

```C#
public abstract class Shape
{
    public abstract Point Center();
    public abstract void Move(Point to);
    public abstract void Draw();
    public abstract void Rotate(int angle);

    public virtual void Foo()
    {
        // Some default implementation that can
        // be shared or overridden by derived classes.
    }
}

```

Note `Foo()` contains an implementation body. It is no longer a pure signature. If you want to ensure that there is no implementation, you can use a C# interface instead.

----------

### Example: Deriving from an Abstract Class

```C#
public class Square : Shape
{
    public override Point Center()
    {
        throw new NotImplementedException();
    }

    public override void Move(Point to)
    {
        throw new NotImplementedException();
    }

    public override void Draw()
    {
        throw new NotImplementedException();
    }

    public override void Rotate(int angle)
    {
        throw new NotImplementedException();
    }

    // public override void Foo()
    // {
    //     // Optional override of a virtual method
    // }
}

```

> **Note:** `override` is required for abstract methods. You do not have to override virtual methods unless you want to change their behavior.

----------

## C# Interface

Note interfaces cannot have an implementation. By convention, the name of an interface begins with `I` (e.g., `IShape`). This helps distinguish interfaces from classes, improving code readability. Interface methods are implicitly `public`:

```C#
public interface IShape
{
    Point Center();
    void Move(Point to);
    void Draw();
    void Rotate(int angle);
}

```

### Example: Implementing an Interface

```C#
public class Square : IShape
{
    public Point Center()
    {
        throw new NotImplementedException();
    }

    public void Move(Point to)
    {
        throw new NotImplementedException();
    }

    public void Draw()
    {
        throw new NotImplementedException();
    }

    public void Rotate(int angle)
    {
        throw new NotImplementedException();
    }
}

```

----------

## Compare and Contrast Abstract Classes and Interfaces

Since both are missing at least some implementation, neither can be instantiated (or “newed up”). This also means they do not need a public and/or default constructor. Abstract classes may contain some instance fields and default implementation meant to be shared or overridden by derived classes. If an abstract class has instance fields, you can use a protected constructor to initialize them. C# allows you to declare an abstract base class constructor as public, but I do not recommend this. (FDG 95-96).

You can say that you inherit from a base class but implement an interface. Inheritance is an “is-a” relationship. We say “a cat is a mammal” and “a mammal is an animal.” In both cases it's common to call this an "is-a" relationship. Some refer to implementing an interface as a "can-do" relationship. (FDG 95, Jeffrey Richter)

C# structs can implement interfaces but cannot inherit from classes.

C# and Java do not allow derived classes to inherit from more than one base class (multiple inheritance), but they allow a class to inherit from a single base class and/or implement many interfaces.

----------

### Additional Points (Deitel 494–495)

We often use an interface in place of an abstract class when there’s **no default implementation** to inherit—that is, no fields and no default method implementations. We typically use an interface when **unrelated classes** need to share common methods. This allows objects of unrelated classes to be processed polymorphically—objects of classes that implement the same interface can respond to the same method calls. When declaring a method in an interface, choose a name that describes the method's purpose in a general manner, because the method may be implemented by a broad range of unrelated classes.

----------

## Relationship to General Design Principles

-   **"Program to an interface; not an implementation" (GOF 18)**: Here, the word “interface” refers to both pure abstract classes or interfaces. We can restate this as "Program to an interface or (pure?) abstract class; not an implementation.”
-   **"Prefer composition ("has-a") over class inheritance" (GOF 20)**: This distinguishes “class inheritance” from “interface inheritance” (GOF 17). “Interface inheritance” is equivalent to deriving from a pure abstract class or implementing an interface.

_TBD_

----------

## Relationship to SOLID Design Principles

Both pure abstract classes and interfaces enable us to follow the **Open Closed Principle (OCP)** and the **Dependency Inversion Principle (DIP)**.

Abstract classes can contain implementation. It is possible that derived classes may override the default behavior in a way that makes them not fully substitutional for the base class. When this happens, they lose their ability to be used polymorphically. The “is-a” relationship has degenerated into a “is-like-a” relationship (Coplien 251), thus violating the **Liskov Substitution Principle (LSP)**. Since interfaces lack implementation, in theory it is harder to violate the LSP using interfaces.

Since we typically use interfaces for unrelated classes, they tend to have a leaner set of members. Remember, each member of an interface must be implemented in derived classes, but we should not force clients to implement methods they do not use. This “interface pollution” or “fat interface” tends to violate the **Interface Segregation Principle (ISP)**.

### Example Fat Interface

```C#
public interface IAbc
{
    // A
    void A();
    void AA();
    void AAA();

    // B
    void B();
    void BB();

    // C
    void C();
}

```

A class that implements this interface must implement every method. The problem is that the implementing class would most likely violate the **SRP**. The class A implementations would need to be updated for different reasons and at different times than the B or C implementations.

----------

![Abstract Classes vs Interfaces](Abstract Classes vs Interfaces.png)

----------

## Using Interfaces with Base Classes

Sometimes it’s good to use an interface along with a base class so developers can choose which is best for them. They can get free default implementation if they use the base class. This is a common practice in the .NET framework as we can see in the **System.Collections** Namespace.

_TBD_  
_edit notes_

----------

## Summery

If your public interface contains more than a few members (don’t know how many) and they belong together (high cohesion), then represent them as an **abstract class**. If the members do not relate well (low cohesion), then we should divide them into multiple smaller **interfaces**.

**Beginning with C# 8.0 on .NET Core 3.0, you can define an implementation when you declare a member of an interface.** The most common scenario is to safely add members to an interface already released and used by innumerable clients. This includes static fields and methods and excludes instance fields. You can provide a body to interface methods, but the method does not have access to any instance fields.