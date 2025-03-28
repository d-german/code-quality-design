# Defensive Copying in C# Example

Defensive copying is a technique used to prevent external code from
modifying an object's state. This is achieved by creating a new
instance of the object, copying the original object's properties to the
new instance.

In C#, this can be implemented using a `Lazy<T>` object, which provides
support for lazy initialization. This means the object is not created
until it is accessed for the first time, improving performance by
avoiding unnecessary computation.

Here's an example from a `ProblemDetailsResult<T>` class:

```C#
public record ProblemDetailsResult<T> : Result<T>
{
    private readonly Lazy<ProblemDetails> _problemDetailsLazy;
    private readonly ProblemDetails _initialProblemDetails;

    // Public property uses Lazy<T> for deferred creation
    public required ProblemDetails ProblemDetails
    {
        get => _problemDetailsLazy.Value;
        // Init setter stores the *original* reference privately
        init => _initialProblemDetails = value;
    }

    // CallerMemberName might be used for logging or diagnostics
    public string CallerMemberName { get; }
    // Assuming SourceId is another property possibly set elsewhere
    public Guid SourceId { get; /* set; */ } = Guid.NewGuid(); // Example

    public ProblemDetailsResult(
        [CallerMemberName] string callerMemberName = "")
    {
        CallerMemberName = callerMemberName;
        // Initialize Lazy<T> with the factory method
        _problemDetailsLazy = new Lazy<ProblemDetails>(
                                  CreateExtendedProblemDetails);
    }

    // Factory method called by Lazy<T> on first access to ProblemDetails
    private ProblemDetails CreateExtendedProblemDetails()
    {
        // *** Defensive Copy ***
        // Create a new ProblemDetails instance
        var detailsCopy = new ProblemDetails
        {
            // Copy properties from the initial object
            Title = _initialProblemDetails.Title,
            Status = _initialProblemDetails.Status,
            Detail = _initialProblemDetails.Detail,
            Instance = _initialProblemDetails.Instance,
            Type = _initialProblemDetails.Type,
            // *** Deep Copy for mutable collections ***
            // Create a new dictionary for extensions
            Extensions = new Dictionary<string, object?>(
                                _initialProblemDetails.Extensions ?? new()
                         )
        };

        // Add or update extensions specific to this result context
        // These modifications only affect the *copy*
        detailsCopy.Extensions[nameof(SourceId)] = SourceId;
        detailsCopy.Extensions[nameof(CallerMemberName)] = CallerMemberName;

        // Return the newly created and populated copy
        return detailsCopy;
    }
}
```

In this `ProblemDetailsResult<T>` example:

1.  The `init` accessor for `ProblemDetails` stores the originally provided `ProblemDetails` object in the private field `_initialProblemDetails`.
2.  The `get` accessor returns `_problemDetailsLazy.Value`.
3.  The `Lazy<T>` object (`_problemDetailsLazy`) is initialized with the `CreateExtendedProblemDetails` factory method.
4.  When `ProblemDetails` is accessed for the first time, `CreateExtendedProblemDetails` is executed.
5.  Inside `CreateExtendedProblemDetails`, a **new** `ProblemDetails` object (`detailsCopy`) is created. Its properties are populated from `_initialProblemDetails`. Crucially, the `Extensions` dictionary, which is mutable, is also copied into a **new** `Dictionary` instance. This prevents changes to the copied extensions from affecting the original object's extensions dictionary.
6.  Additional context-specific information (`SourceId`, `CallerMemberName`) is added to the *copy's* `Extensions`.
7.  This newly created and modified `detailsCopy` is returned and cached by the `Lazy<T>`. Subsequent accesses to `ProblemDetails` return this cached copy.

This ensures that the `ProblemDetails` object exposed by the `ProblemDetailsResult<T>` is a distinct instance from the one initially provided, protecting the internal state and adding context without modifying the original object.

---
See Also:
- [Functional in the Small and Object-Oriented in the Large](Functional-in-the-Small-and-Object-Oriented-in-the-Large.md) (discusses immutability techniques)
- [Transitioning from Classes to Records and Adopting Immutable Collections in C#](Transitioning-from-Classes-to-Records-and-Adopting-IImmutableList-in-C.md)
- [Pillars of OOP Encapsulation Abstraction](Pillars-of-OOP-Encapsulation-Abstraction.md)