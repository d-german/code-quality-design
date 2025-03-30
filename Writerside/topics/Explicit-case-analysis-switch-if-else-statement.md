# Explicit case analysis (switch/if-else statement)

What's Wrong with Switch Statements? It Depends.

**Explicit case analysis** (using switch or if-else statements) is often problematic:

- **Explicit case analysis on the type of an object is usually an error.** (Riel 98)
- **Explicit case analysis on the value of an attribute is often an error.** (Riel 105)

The problem with switch statements is essentially one of duplication. Often you find the same switch statement scattered throughout a program. If you add a new clause to the switch, you have to find all these instances and change them. In most cases, seeing a switch statement should prompt you to consider using polymorphism instead. (Fowler 82)

Often a switch statement indicates misplaced responsibilities. Instead, consider giving the responsibility to other objects. (Shalloway 195)

---

## Example: Switch Statement for Shapes

```C#
private static void DoSomething(Shape shape)
{
    switch (shape)
    {
        case Shape.Square:
            Console.WriteLine("Do something Square related");
            break;
        case Shape.Rectangle:
            Console.WriteLine("Do something Rectangle related");
            break;
        case Shape.Circle:
            Console.WriteLine("Do something Circle related");
            break;
        default:
            Console.WriteLine("Not a Shape.");
            break;
    }
}
```

**Use Polymorphism Instead**

If you have a switch statement, ask yourself these three questions:
1. Does it cause a lot of tight coupling?
2. Does it violate the Open-Closed Principle (OCP)?
3. Does it violate the Single Responsibility Principle (SRP)?

If you answer **yes** to any of these, then consider using polymorphism.

### Explanation

- **Tight Coupling:**  
  If a new `Shape` is added, multiple parts of the program might need to be updated (e.g., both `fill()` and `rotate()` methods), even though they are unrelated.

- **Violation of OCP:**  
  Every time you add a new `Shape`, you must modify existing code (e.g., `DisplayArea()`), which goes against the idea of being open for extension but closed for modification.

- **Violation of SRP:**  
  A switch statement that handles multiple responsibilities suggests that the class is doing too much, which might be a sign of misplaced responsibilities.

---

## Example: Switch Statement in Logging

```C#
public void RecordEntry(LogEntry entry)
{
    switch (entry.Level)
    {
        case LogLevel.Error:
            Console.Error.WriteLine(entry.Message, entry.Details);
            this._errorReportingService.RecordEntry(entry);
            break;
        case LogLevel.Warn:
            Console.WriteLine($"WARN: {entry.Message}, {entry.Details}");
            break;
        case LogLevel.Info:
        default:
            Console.WriteLine($"{entry.Message}, {entry.Details}");
            break;
    }
}
```

For this logging example:
- **Tight Coupling:**  
  Currently, this is the only place where the switch on `LogLevel` occurs. If you later need to add similar switches elsewhere, you should consider polymorphism.

- **Open-Closed Principle (OCP):**  
  Logging levels are standardized and unlikely to change, so modifying the switch statement is less problematic. However, if logging requirements change, the OCP might be violated.

- **Single Responsibility Principle (SRP):**  
  In this example, the responsibility for error handling is properly delegated to the `ErrorReportingService`, so the SRP is maintained.

---

## Example: Utility Function with Switch Statement

```javascript
Utility.formatValue = function (val, type, format) {
    var result = _.isUndefined(val) ? "" : val;
    switch (type) {
        case Utility.DATA_TYPE.DATE:
            result = Utility.formatDate(val, format);
            break;
        case Utility.DATA_TYPE.TIME_SPAN:
            result = Utility.formatTimeSpan(val);
            break;
        case Utility.DATA_TYPE.INTEGER:
            // fallthrough
        case Utility.DATA_TYPE.NUMBER:
            result = Utility.formatNumber(val, format);
            break;
        default:
            break;
    }
    return result;
};
```

A good switch statement is one where:
- It is centralized in one location.
- It switches on a limited, stable set of values.
- The actions performed demonstrate good cohesion.

---

## Summary

Switch statements can be problematic because they:
- Encourage code duplication.
- Increase tight coupling.
- Violate the Open-Closed Principle (OCP) and Single Responsibility Principle (SRP) if overused.
- Often indicate misplaced responsibilities.

When possible, consider using polymorphism to replace switch statements, unless the set of possible values is both limited and stable (as in the logging example).

---
See Also:
- [Prefer Dictionary to Switch Statement](Prefer-Dictionary-over-Switch-Statement.md)
- [Open Closed Principle (OCP)](Open-Closed-Principle-OCP.md)
- [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md)
- [DRY Principle](DRY-Principle.md)
- [Polymorphism](Polymorphism.md)
- [Liskov Substitution Principle (LSP)](Liskov-Substitution-Principle-LSP.md) (type sniffing violates LSP)
- [Code Review Checklist](Code-Review-Checklist.md) (mentions refactoring switch)