# Avoiding Liskov Substitution Principal Violations in Type Hierarchies

Consider the following type hierarchy:

```C#
public interface IHealthcareDocument
{
    long ID { get; }
    DateTime? ClinicallyRelevantDate { get; }
    DateTime? AttachedDate { get; }
    string FacilityName { get; }
    string DepartmentName { get; }
    string Name { get; }
    DateTime? StoredDate { get; }
    string Description { get; }
    bool IsRestricted { get; }
}

public class HealthcareDocument : IHealthcareDocument
{
    public long ID { get; set; }
    public DateTime? ClinicallyRelevantDate { get; set; }
    public DateTime? AttachedDate { get; set; }
    public string FacilityName { get; set; }
    public string DepartmentName { get; set; }
    public string Name { get; set; }
    public DateTime? StoredDate { get; set; }
    public string Description { get; set; }
    public bool IsRestricted { get; set; }
}
```

**Note:**  
`IHealthcareDocument` is immutable while `HealthcareDocument` is mutable. This means that the derived type has different
semantics than its base type. This difference can cause problems when these types are used polymorphically.

---

## The ToggleIsRestricted Method and Its Flaws

Consider the following `ToggleIsRestricted` method that toggles the `IsRestricted` property:

```C#
[Test]
public void LspOcpViolation()
{
    IHealthcareDocument ToggleIsRestricted(IHealthcareDocument healthcareDocument)
    {
        if (healthcareDocument is HealthcareDocument otherHealthcareDocument)
        {
            otherHealthcareDocument.IsRestricted = !otherHealthcareDocument.IsRestricted;
        }
        return healthcareDocument;
    }
    var restrictedHealthcareDocument = new HealthcareDocument { IsRestricted = true };
    var unrestrictedHealthcareDocument = ToggleIsRestricted(restrictedHealthcareDocument);
    Assert.That(unrestrictedHealthcareDocument.IsRestricted, Is.False);
}
```

Because `IHealthcareDocument` is immutable, we must first cast the input parameter before we can update the
`IsRestricted` property. The `ToggleIsRestricted` method signals a design flaw—it violates the **Open Closed Principle (
OCP)**. If a new type implementing `IHealthcareDocument` were added, the method would have to be modified to accommodate
it.

---

## Why the Violation Occurs

The root cause of this issue is a violation of the **Liskov Substitution Principle (LSP)**. The LSP asserts that derived
types must be fully substitutable for their base types. In this case, `HealthcareDocument` is not fully substitutable
for `IHealthcareDocument` because of its mutability. A derived type can violate the LSP by:

- Breaking the base type's invariants.
- Overriding methods with stronger preconditions or weaker postconditions.
- Altering the underlying assumptions and semantics.

For example, consider this contrived hierarchy that violates the intended semantics:

```C#
public interface IAdder
{
    int Add(int a, int b);
}

class Adder : IAdder
{
    public int Add(int a, int b)
    {
        return a - b; // LSP violation: returns the difference instead of the sum
    }
}
```

The interface `IAdder` is intended to sum two integers, but the implementation in `Adder` subtracts them—clearly
violating the expected semantics. Similarly, `HealthcareDocument` violates the LSP by having semantics (mutability) that
differ from those of `IHealthcareDocument`.

---

## Improving the Design

To improve the design, we need to address the LSP violation by ensuring that derived types are fully substitutable for
their base types. There are two primary options:

### Option 1: Make Both Types Mutable

Change the interface to allow mutability:

```C#
public interface IHealthcareDocument
{
    long ID { get; set; }
    DateTime? ClinicallyRelevantDate { get; set; }
    DateTime? AttachedDate { get; set; }
    string FacilityName { get; set; }
    string DepartmentName { get; set; }
    string Name { get; set; }
    DateTime? StoredDate { get; set; }
    string Description { get; set; }
    bool IsRestricted { get; set; }
}
```

Then, `ToggleIsRestricted` can be simplified:

```C#
IHealthcareDocument ToggleIsRestricted(IHealthcareDocument healthcareDocument)
{
   healthcareDocument.IsRestricted = !healthcareDocument.IsRestricted;
   return healthcareDocument;
}
```

### Option 2: Make Both Types Immutable

Keep immutability by using `init` accessors:

```C#
public interface IHealthcareDocument
{
    long ID { get; }
    DateTime? ClinicallyRelevantDate { get; }
    DateTime? AttachedDate { get; }
    string FacilityName { get; }
    string DepartmentName { get; }
    string Name { get; }
    DateTime? StoredDate { get; }
    string Description { get; }
    bool IsRestricted { get; }
}

public class HealthcareDocument : IHealthcareDocument
{
    public long ID { get; init; }
    public DateTime? ClinicallyRelevantDate { get; init; }
    public DateTime? AttachedDate { get; init; }
    public string FacilityName { get; init; }
    public string DepartmentName { get; init; }
    public string Name { get; init; }
    public DateTime? StoredDate { get; init; }
    public string Description { get; init; }
    public bool IsRestricted { get; init; }
}
```

Now, `ToggleIsRestricted` must create a new instance:

```C#
IHealthcareDocument ToggleIsRestricted(IHealthcareDocument healthcareDocument)
{
    return new HealthcareDocument
    {
        Description = healthcareDocument.Description,
        Name = healthcareDocument.Name,
        AttachedDate = healthcareDocument.AttachedDate,
        DepartmentName = healthcareDocument.DepartmentName,
        ID = healthcareDocument.ID,
        FacilityName = healthcareDocument.FacilityName,
        StoredDate = healthcareDocument.StoredDate,
        ClinicallyRelevantDate = healthcareDocument.ClinicallyRelevantDate,
        // This is the only updated property
        IsRestricted = !healthcareDocument.IsRestricted,
    };
}
```

This design, although improved in terms of immutability, still violates the OCP due to the boilerplate code needed to
copy every property.

#### A Better Approach: Using Records (C# 9)

We can simplify the copy process by using a record:

```C#
public record HealthcareDocument : IHealthcareDocument
{
    public long ID { get; init; }
    public DateTime? ClinicallyRelevantDate { get; init; }
    public DateTime? AttachedDate { get; init; }
    public string FacilityName { get; init; }
    public string DepartmentName { get; init; }
    public string Name { get; init; }
    public DateTime? StoredDate { get; init; }
    public string Description { get; init; }
    public bool IsRestricted { get; init; }
}
```

Then, `ToggleIsRestricted` can be elegantly written as:

```C#
IHealthcareDocument ToggleIsRestricted(IHealthcareDocument healthcareDocument)
{
    if (healthcareDocument is HealthcareDocument document)
    {
        return document with { IsRestricted = !healthcareDocument.IsRestricted };
    }
    return healthcareDocument;
}
```

This design no longer violates the LSP, but note that the use of the `with` keyword only works with records. If we try
to use `with` directly on `IHealthcareDocument`, the code will not compile:

```C#
IHealthcareDocument ToggleIsRestricted(IHealthcareDocument healthcareDocument)
{
    return healthcareDocument with { IsRestricted = !healthcareDocument.IsRestricted }; // compile error
}
```

The compiler error indicates that `IHealthcareDocument` is not recognized as a record type. Therefore, we must either
abandon immutability or abandon polymorphism. I prefer keeping immutability, so an alternative is to remove the
interface altogether:

```C#
HealthcareDocument ToggleIsRestricted(HealthcareDocument healthcareDocument)
{
    return healthcareDocument with { IsRestricted = !healthcareDocument.IsRestricted };
}
```

This is, in my opinion, the best design for a Data Transfer Object (DTO) like `HealthcareDocument`, even though it still
violates the Dependency Inversion Principle (DIP). After all, the main motivation for using an interface or pure
abstract class is to achieve polymorphism. Since DTOs only contain data and not behavior, they are as stable as
interfaces and should not be considered volatile.

One of my favorite quotes is:
> “Never shoot for the best design, but rather the least worst design.” (Richards and Ford)

As long as `HealthcareDocument` remains a DTO, then we have the least worst design and are almost finished.

---

## Adding Behavior to HealthcareDocument

What happens if we begin adding nontrivial behavior to the `HealthcareDocument` class? Consider again the
`ToggleIsRestricted` method. Traditional Object-Oriented reasoning advocates encapsulating related data and behavior.
Thus, we might consider moving the `ToggleIsRestricted` method into the `HealthcareDocument` class:

```C#
public record HealthcareDocument
{
    public long ID { get; init; }
    public DateTime? ClinicallyRelevantDate { get; init; }
    public DateTime? AttachedDate { get; init; }
    public string FacilityName { get; init; }
    public string DepartmentName { get; init; }
    public string Name { get; init; }
    public DateTime? StoredDate { get; init; }
    public string Description { get; init; }
    public bool IsRestricted { get; init; }
    public HealthcareDocument ToggleIsRestricted() => this with { IsRestricted = !IsRestricted };
}
```

And use it like this:

```C#
var restrictedHealthcareDocument = new HealthcareDocument { IsRestricted = true };
var healthcareDocument = restrictedHealthcareDocument.ToggleIsRestricted();
Assert.That(healthcareDocument.IsRestricted, Is.False);
```

However, adding behavior like this to `HealthcareDocument` makes it a traditional class rather than a pure DTO. If we
start adding nontrivial logic (e.g., validation, business rules), we must reconsider the DIP.

A simple solution is to move the `ToggleIsRestricted` method into an extension method, thereby keeping
`HealthcareDocument` a DTO:

```C#
public record HealthcareDocument
{
    public long ID { get; init; }
    public DateTime? ClinicallyRelevantDate { get; init; }
    public DateTime? AttachedDate { get; init; }
    public string FacilityName { get; init; }
    public string DepartmentName { get; init; }
    public string Name { get; init; }
    public DateTime? StoredDate { get; init; }
    public string Description { get; init; }
    public bool IsRestricted { get; init; }
}

public static class HealthcareDocumentExtensions
{
    public static HealthcareDocument ToggleIsRestricted(this HealthcareDocument healthcareDocument)
    {
        return healthcareDocument with { IsRestricted = !healthcareDocument.IsRestricted };
    }
}
```

Test code remains unchanged:

```C#
var restrictedHealthcareDocument = new HealthcareDocument { IsRestricted = true };
var healthcareDocument = restrictedHealthcareDocument.ToggleIsRestricted();
Assert.That(healthcareDocument.IsRestricted, Is.False);
```

---

## Conclusion

In conclusion, the `HealthcareDocument` class violates the **Liskov Substitution Principle (LSP)** because it is not
fully substitutable for the `IHealthcareDocument` interface. The LSP states that derived types must be fully
substitutable for their base types. In this case, `HealthcareDocument` is not, because it is mutable while
`IHealthcareDocument` is immutable. Consequently, the `ToggleIsRestricted` method, which expects an
`IHealthcareDocument` object, may not work correctly if passed a `HealthcareDocument` object—it would be unable to
change the value of the `IsRestricted` property. This LSP violation leads to a violation of the **Open Closed
Principle (OCP)**, as the `ToggleIsRestricted` method must be modified to accommodate new types implementing
`IHealthcareDocument`.

The design can be improved by either:

- Making both types mutable.
- Making both types immutable and using records (thereby leveraging the `with` syntax).

Each approach has trade-offs, but for a Data Transfer Object (DTO) like `HealthcareDocument`, the benefits of
immutability and a simple, non-volatile design may outweigh the need for polymorphism.

In summary, the problems encountered with `ToggleIsRestricted` highlight how a violation of the LSP can cascade into
violations of other design principles like the OCP, emphasizing the importance of consistent type semantics when using
polymorphism.
