# Using Required Members in C# 11

C# has evolved significantly, especially in handling required properties.
Traditionally, ensuring required properties were set involved
constructors. C# 11 introduces a more streamlined approach.

## Old Approach: Using Constructors

Previously, required properties were set via constructors. This method,
while effective, had its drawbacks, particularly in terms of
readability and potential for errors.

Example (Positional Record):

```C#
// Concise, but order matters and intent might be less clear initially
public record Person (string FirstName, string Lastname);
```

Or (Nominal Record/Class with Constructor):

```C#
public record Person
{
    public string FirstName { get; init; }
    public string LastName { get; init; }

    // Constructor ensures properties are set upon creation
    public Person(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;
    }
}
```

### Issues:

*   **Order-Dependent:** Parameters like `firstName` and `lastName` in the constructor must be supplied in the correct order. Easy to mix up, leading to bugs.
*   **Less Expressive:** The constructor signature itself doesn't explicitly highlight which properties are mandatory requirements of the *type* versus just constructor parameters, especially with optional parameters or multiple constructors.

## New Approach: `required` member keyword in C# 11

C# 11 introduces the `required` modifier for members. This new keyword
enhances code clarity and reduces the risk of errors by enforcing
initialization during object creation using object initializers.

Example:

```C#
public record Person
{
    // Compiler enforces these properties are set in an object initializer
    public required string FirstName { get; init; }
    public required string LastName { get; init; }
    // Can still have optional properties
    public string? MiddleName { get; init; }
}

// Usage: Compiler error if FirstName or LastName are omitted
var person = new Person { FirstName = "John", LastName = "Doe" };
```

### Benefits:

*   **Explicit Declaration:** Each property is explicitly marked as `required`, increasing expressiveness at the type definition level.
*   **Reduced Error Risk:** When using object initializers, named properties eliminate the risk of mixing up values based on order (like first and last names).
*   **Clearer Intent:** The `required` keyword makes it immediately clear which properties *must* be set when initializing the object, independent of specific constructor signatures.

## Conclusion

The new `required` modifier in C# 11 offers a more expressive,
error-resistant way to handle mandatory properties, particularly when
using object initializers. This enhancement improves code readability and
maintainability, especially important for non-native English speakers
and in large, complex projects.
