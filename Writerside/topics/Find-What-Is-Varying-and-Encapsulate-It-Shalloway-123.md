# Find What Is Varying and Encapsulate It (Shalloway 123)

Take what varies, and "encapsulate" it so it won't affect the rest of your code. The result? Fewer unintended
consequences from code changes and more flexibility in your systems! (Freeman 9)

* Abstract out anything that is likely to change. (Riel TBD)
* Using the word encapsulate may seem odd if you only think about encapsulation as data hiding. It is much more sensible
  when you think of encapsulation as hiding classes with an abstract class or interface — "type encapsulation."
  Containing a reference of this abstract class or interface type hides these derived classes that represent variations
  in behavior. (i.e. polymorphism). The using class has a reference to an abstract class or interface that has more than
  one specialized derivation. We hide (encapsulated) these derivations from the using class. (Shalloway 123)
* The deeper use of the word comes directly from the GOF, where they list out different things to consider when choosing
  a design pattern.
    * This approach is the opposite of focusing on the causes of redesign. Instead of considering what might force a
      change to a design, consider what you want to change without redesign. The focus here is on *encapsulating* the
      concept that varies a theme of many design patterns. (GOF 29)
    * There are other ways other than object-oriented programming to "encapsulate" the things that vary.
        * Generic programming can easily "encapsulate" things that vary. (Coplien 1999 47)
          ```C#
          public static void Swap<T>(ref T a, ref T b)
          {
              var temp = a;
              a = b;
              b = temp;
          }
          ```
        * Another way to "encapsulate" differences is with method overloading. (Coplien 1999 49).
          ```C#
          void Foo (int x)           { Console.WriteLine("int"); }
          void Foo (double x)        { Console.WriteLine("double"); }
          void Foo (int x, float y)  { Console.WriteLine("int, float"); }
          void Foo (float x, int y)  { Console.WriteLine("float, int"); }

          private void Main()
          {
              Foo (123);          // int
              Foo (123.0);        // double
              Foo (123, 123F);    // int, float
              Foo (123F, 123);    // float, int
          }
          ```
        * It's interesting that Generic Methods/classes and function overloading are also considered polymorphism. We
          call this static or compile time polymorphism. Also referred to as early binding. That's why, for example,
          `Foo("Hello");` would cause a compile error. But "true" polymorphism, the kind we normally use the term for,
          we call runtime or dynamic polymorphism. Which is also referred to as late binding. That's why improper usage
          causes errors during program execution. (TBD find a source for this paragraph)
* One design trick of procedural programing is to encapsulate varying algorithms in one procedure by using a formal
  parameter with explicit case analysis to discriminate between alternatives. (Coplien 1999 57)
* Still another mechanism to encapsulate the things that vary is to use C# Delegates or C++ Function Objects.

  ```C#
  internal static class Program
  {
      public delegate int Transformer(int x);

      public static int Transform(int value, Transformer t)
      {
          return t(value);
      }

      static int Square(int x) => x * x;
      static int Cube(int x)   => x * x * x;

      private static void Main()
      {
          Console.WriteLine(Transform(2, Square)); // 4
          Console.WriteLine(Transform(2, Cube));   // 8
      }
  }
  ```
* Finally, this principle is a general case of three of the five SOLID design principles.
  See: The Open/Closed Principle (OCP),
  the Liskov Substitution Principle (LSP) and
  the Dependency Inversion Principle (DIP).
---
See Also:
- [Pillars of OOP Encapsulation Abstraction](Pillars-of-OOP-Encapsulation-Abstraction.md)
- [DRY Principle](DRY-Principle.md)
- [Polymorphism](Polymorphism.md)
- [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md)
- [Open Closed Principle (OCP)](Open-Closed-Principle-OCP.md)
- [Liskov Substitution Principle (LSP)](Liskov-Substitution-Principle-LSP.md)
- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Higher-Order Function Examples](Higher-Order-Function-Examples.md) (using delegates/Func)
- [Strategy Pattern Implementation for JSON Processing](Strategy-Pattern-Implementation-for-JSON-Processing.md) (encapsulates algorithms)