# Open Closed Principle (OCP)

## Overview

A software module should be open for extension but closed for modification. (Martin, 2002: 99)

## Core Concepts

The OCP is at the heart of many of the claims made for OOD. (Martin, 2002: 125)

The gist is that for software systems to be easy to change, they must be designed to allow the behavior of those systems
to be changed by adding new code, rather than changing existing code.

## Related Principles

OCP relates to the Single Responsibility Principle (SRP) and Dependency Inversion Principle (DIP).

Clearly, some new code must be written. But how much old code will have to change?

- We should reduce the amount of changed code to the barest minimum. Ideally, zero.
- How? By properly separating the things that change for different reasons (SRP), and then organizing the dependencies
  between those things properly (DIP). (Martin, 2017: 70)

## Implementation Examples

```C#
public class AreaCalculator
{
    public double SumAreas(IShape[] shapes)
    {
        double sum = 0;
        foreach (var shape in shapes)
        {
            sum += shape.CalculateArea();
        }
        return sum;
    }
}
```

This calculator is open for extension because new `IShape` implementations can be added, yet closed for modification as
the `SumAreas` logic itself doesn't need to change to accommodate them.

```C#
public static T Max<T>(T first, T second) where T : IComparable<T>
{
    return first.CompareTo(second) >= 0 ? first : second;
}
```

This generic method is closed for modification but open to work with any new types `T` that fulfill the `IComparable<T>`
contract, demonstrating extension
through [**Static Polymorphism**](Polymorphism.md#static-compile-time-or-early-binding-polymorphism) and constraints.

---

## Relation to Liskov Substitution Principle (LSP)

OCP also relates to the Liskov Substitution Principle (LSP). Violations of the LSP often force developers into using
special
type checks or conditional logic, which undermines the Open-Closed Principle by requiring modifications in existing
code.

```C#
public class AreaCalculator_Violating_OCP
{
    public double SumAreas(IEnumerable<IShape> shapes)
    {
        double sum = 0;
        foreach (var shape in shapes)
        {           
            if (shape is SpecialConcreteShape specialShape)
            {               
                // Custom logic for SpecialConcreteShape
                sum += specialShape.BaseValue;               
            }
            else
            {
                // Original logic for other shapes
                sum += shape.CalculateArea();
            }           
        }
        return sum;
    }
}
```

Adding the `if (shape is SpecialConcreteShape)` check violates the Open-Closed Principle, as it requires modifying the
existing SumAreas method. This modification becomes necessary when `SpecialConcreteShape` cannot be transparently
substituted for `IShape` (violating the Liskov Substitution Principle) because it demands unique handling by the
calculator.

[**Software Entropy**](Motivation-for-Good-Design.md) naturally increases as changes are made to the codebase. If
developers don't actively work against this tendency, modifications like these type checks will continue to be added,
degrading the system over time.

---
See Also:

- [SOLID Design Principles](SOLID-Design-Principles.md)
- [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md)
- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Liskov Substitution Principle (LSP)](Liskov-Substitution-Principle-LSP.md) (enabler for OCP)
- [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md) (mechanism for OCP)
- [Polymorphism](Polymorphism.md) (mechanism for OCP)
- [Strategy Pattern Implementation for JSON Processing](Strategy-Pattern-Implementation-for-JSON-Processing.md) (
  demonstrates OCP)
- [Decorator Pattern](Decorator-Pattern.md) (demonstrates OCP)
- [Explicit case analysis (switch/if-else statement)](Explicit-case-analysis-switch-if-else-statement.md) (often
  violates OCP)
- [Coupling](Coupling.md) (OCP aims for looser coupling)