# DTO - Data Transfer Object

A Data Transfer Object (DTO) is an object that carries data within an application or between processes.

Programming to an interface doesn’t mean that all classes should implement an interface. Short-lived objects, such as
POCOs, DTOs and VMs (View Model TBD), typically contain no behavior that requires mocking, interception, decoration, or
replacement. They are mere data objects. This makes them safe to create in your code, so there’s no risk in tightly
coupling your code to these data objects. These objects contain the application’s runtime data that flows through the
system after classes like controllers and services have long been created. (Seemann 58)

TBD

Starting in C# 9, we should implement DTOs as read-only record types.

## Original mutable class DTO

```C#
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public DateTime DateOfBirth { get; set; }
}
```

## C# 9 read-only record type DTO

```C#
public record Person(
    int Id, 
    string FirstName, 
    string LastName, 
    DateTime DateOfBirth
);
```

or

```C#
public record Person
{
    public int Id { get; init; }
    public string FirstName { get; init; }
    public string LastName { get; init; }
    public DateTime DateOfBirth { get; init; }
}
```

I favor the property-based record syntax with `{ get; init; }` blocks over positional parameters. This approach prevents
parameter order confusion during initialization and provides better code clarity when objects are instantiated. Named
properties make the code more self-documenting and less error-prone, especially when dealing with multiple string
parameters that could easily be transposed.

See Stable vs Volatile Dependencies
