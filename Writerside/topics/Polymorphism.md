# Polymorphism

- **Polymorphism** (Coplien 86)  
  Polymorphism, the ability of a program to treat many forms of a class as though they were one, is a powerful
  abstraction mechanism that shields the programmer from the implementation details of derived classes.

- **Fruit Example** (Riel 95)  
  If you do not mind believing in magic, then imagine the ability to walk up to any fruit and tell it to give you its
  cost. You know the object to which you sent the cost message is not a fruit object because there is no such thing as a
  fruit object (fruit is an abstract class). Therefore, it is an apple, or a banana, or an orange masquerading as a
  fruit. The idea behind polymorphism is that a group of heterogeneous objects (e.g., apples, oranges, bananas) can be
  made to look homogeneous (e.g., a bunch of fruit), but can then be distinguished based on their own specific type at
  runtime.

- **Subtype vs. Supertype** (McLean Hall, 281)  
  Polymorphism is the ability of a subtype to be treated as if it were an instance of the supertype. Thanks to this
  feature of object-oriented programming, which C# supports, any method that accepts an instance of Supertype will also
  be able to accept an instance of Subtype with no casting required by either the client or service code, and with no
  type-sniffing by the service. To the service, we have handed it an instance of Supertype, and this is the only fact it
  is concerned with. It doesn’t care what specific subtype has been handed to it.

## Dynamic, Run Time, Late Binding Polymorphism

```c#
public interface IFoo
{
    void Bar();
}

class A : IFoo
{
    public void Bar()
    {
        WriteLine("A");
    }
}

class B : IFoo
{
    public void Bar()
    {
        WriteLine("B");
    }
}

internal class Program
{
    private static void Main()
    {
        WriteLine("Choose A or B");
        var input = ReadLine();
        switch (input)
        {
            case "A":
                Choose(new A());
                break;
            case "B":
                Choose(new B());
                break;
        }
    }

    private static void Choose(IFoo foo)
    {
        foo.Bar();
    }
}
```

## Static Compile time or Early Binding Polymorphism

### Method Overloading

```C#

void Foo(int x)
{
    Console.WriteLine("int");
}

void Foo(double x)
{
    Console.WriteLine("double");
}

void Foo(int x, float y)
{
    Console.WriteLine("int, float");
}

void Foo(float x, int y)
{
    Console.WriteLine("float, int");
}

private void Main()
{
    Foo(123);       // int
    Foo(123.0);     // double
    Foo(123, 123F); // int, float
    Foo(123F, 123); // float, int
}
```

### Generics

```c#
public static void Swap<T>(ref T a, ref T b)
{
    var temp = a;
    a = b;
    b = temp;
}
```

---
See Also:
- [Pillars of OOP](Pillars-of-OOP.md)
- [Inheritance](Inheritance.md)
- [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md)
- [Liskov Substitution Principle (LSP)](Liskov-Substitution-Principle-LSP.md)
- [Open Closed Principle (OCP)](Open-Closed-Principle-OCP.md)
- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Composition](Composition.md) (can be combined with polymorphism)
- [Find What Is Varying and Encapsulate It (Shalloway 123)](Find-What-Is-Varying-and-Encapsulate-It-Shalloway-123.md) (mentions polymorphism types)