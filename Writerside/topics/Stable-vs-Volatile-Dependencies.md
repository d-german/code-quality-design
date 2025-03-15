# Stable vs Volatile Dependencies

Design principles like **"Program to an interface, not an implementation"** (GOF 18) and the **Dependency Inversion
Principle (DIP)** are essential for code maintainability and extensibility. However, overuse can cause needless
complexity. In practice, we only need to concern ourselves with *volatile dependencies*, as applying these principles to
*stable dependencies* is overkill. (2002 Martin 269)

## Stable Dependency Criteria

- The class or module already exists, and you expect that new versions won’t contain breaking changes.
- The types in question contain deterministic algorithms.
- You never expect to have to mock, replace, wrap, decorate, or intercept the class or module with another.
- By default, most (but not all) types defined in the .NET BCL (e.g., `String`, `DateTime`, `Array`, etc.) are
  considered stable because they’re already there, they are backward compatible, and invoking them has a deterministic
  outcome.

## Volatile Dependency Criteria

- The dependency makes it hard to set up and run automated tests.
- The dependency introduces a requirement to set up and configure a runtime environment for the application (e.g.,
  databases, logging, user context, etc.).
- The dependency doesn’t yet exist or is still in development.
- The dependency contains nondeterministic behavior.
- The dependency communicates with the outside world (I/O, sends email, etc.).
- *Example:* If you don’t hide a relational database behind an abstraction, you can never replace it with any other
  technology. Typical sources of nondeterminism include random numbers and algorithms that depend on the current date or
  time.

## What About POCOs, DTOs, VMs, etc.?

Programming to an interface doesn’t mean that all classes should implement an interface. Short-lived objects, such as
POCOs, DTOs, and VMs (View Models), typically contain no behavior that requires mocking, interception, decoration, or
replacement. They are mere data objects. This makes them safe to create in your code, so there’s no risk in tightly
coupling your code to these data objects. These objects contain the application’s runtime data that flows through the
system after classes like controllers and services have long been created. (Seemann 58)

### Adding Behavior to DTOs

Let's say we have an existing DTO and we need to add some behavior. If we want to keep our DTO pure, we can look to
functional programming (FP) concepts for clues. In OOP, data and behavior live in the same object, and methods in the
object can typically change the object’s state. In FP, data is captured with “dumb” data objects while behavior is
encoded separately in functions. (Buonanno 113)

Following this pattern, we can separate the state from the behavior:

- **DTO:** Contains only state.

```c#
public record Person // Immutable DTO
{
    public string FirstName { get; init; }
    public string LastName { get; init; }
}
```

- **Extension Methods:** Contain the behavior that interacts with the DTO.

```c#
public static class PersonExtensions
{
    public static string GetFullName(this Person person)
    {
        return $"{person.FirstName} {person.LastName}";
    }
}
```
