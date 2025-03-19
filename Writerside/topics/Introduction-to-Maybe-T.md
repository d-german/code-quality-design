# Introduction to Maybe&lt;T&gt;

This article presents a generic implementation of a custom `Maybe<T>` type in C#. The `Maybe<T>` type is designed to
mirror the concept of discriminated unions commonly found in functional programming. It provides a way to represent a
value that might exist (Something), might be absent (Nothing), or might have resulted in an error (Error).

---

## Defining the `Maybe<T>` Type

We define `Maybe<T>` as an abstract record with three distinct states, each represented by its own record:

```C#
public abstract record Maybe<T>
{
    public virtual T Value { get; protected init; }
}

public sealed record Something<T> : Maybe<T>
{
    public Something(T value) => Value = value;
}

public sealed record Nothing<T> : Maybe<T>
{
    public override T Value => default;
}

public sealed record Error<T> : Maybe<T>
{
    public Error(Exception exception) => ExceptionObject = exception;
    public Error(string errorDetails) => ErrorDetails = errorDetails;
    public Exception ExceptionObject { get; init; }
    public string ErrorDetails { get; init; }
    public int StatusCode { get; init; }
    public override T Value => default;
}
```

### The Error State in `Maybe<T>`

The `Error<T>` state is designed to represent an error condition. It provides flexibility by allowing error information
to be conveyed either through an `Exception` or through a simple error details string.

---

## Functional vs. Object-Oriented Paradigms

In traditional object-oriented programming, classes combine both data and behavior. In our implementation, `Maybe<T>`
represents state only; behavior is handled externally through pattern matching. This separation of data and behavior is
a key characteristic of functional programming.

### Adherence to OOP Principles

The use of pattern matching with `Maybe<T>` adheres to the Open/Closed Principle (OCP) because once the pattern matching
logic is defined, it rarely requires modification. However, when adding behavior for each state of `Maybe<T>`, care must
be taken to ensure each function maintains a single responsibility. If the responsibilities become too broad, consider
refactoring the behavior into separate components.

---

## Using `Maybe<T>` in Applications

In many applications, especially those interacting with external systems, it's beneficial to encapsulate operation
outcomes using `Maybe<T>`. Instead of returning a raw type, methods return a `Maybe<T>`, which provides a robust
mechanism to handle success, absence of a value, or errors.

### Example: Repository Interface

Here is a generic repository interface method that returns a `Maybe<T>`:

```C#
public interface IDataRepository
{
    Task<Maybe<DataStatus>> GetDataStatusAsync(string identifier, string authToken);
}
```

### Example: Controller Method Using `Maybe<T>`

Below is a generic example of a controller method that uses a `Maybe<T>` pipeline. The pipeline starts by converting an
input into a `Maybe` instance, applies transformations and asynchronous operations, and finally handles the outcome with
pattern matching:

```C#
public async Task<ActionResult<DataStatus>> GetDataStatusAsync([FromRoute] string identifier)
{
    var maybeDataStatus = await identifier.ToMaybe()
        .Bind(UrlEncode)
        .BindAsync(encodedId => _repository.GetDataStatusAsync(encodedId, AuthToken));

    // Optionally log the outcome
    maybeDataStatus.OnAny(maybe => _logger.LogMaybeWithCaller<DataStatus, string>(maybe, identifier));

    return maybeDataStatus switch
    {
        Something<DataStatus> s => Ok(s.Value),
        Error<DataStatus> e => e.ExceptionObject != null
            ? StatusCode(StatusCodes.Status500InternalServerError)
            : StatusCode(e.StatusCode, e.ErrorDetails),
        _ => StatusCode(StatusCodes.Status500InternalServerError)
    };
}
```

In this implementation:

- The pipeline begins by converting `identifier` to a `Maybe<string>`.
- `Bind` and `BindAsync` are used to transform and process the data.
- The repository method returns a `Maybe<DataStatus>`.
- Finally, pattern matching determines the response based on the `Maybe<T>` state.

---

## Extension Methods: Bind and BindAsync

The `Bind` and `BindAsync` extension methods are critical for creating and managing the `Maybe<T>` pipeline. They allow
for data transformation while preserving the `Maybe<T>` structure, with built-in error handling.

### Bind Method (Synchronous)

```C#
public static Maybe<TResult> Bind<TSource, TResult>(
    this Maybe<TSource> maybe, 
    Func<TSource, TResult> func)
{
    try
    {
        return maybe switch
        {
            Something<TSource> s => new Something<TResult>(func(s.Value)),
            Nothing<TSource> _ => new Nothing<TResult>(),
            Error<TSource> e => new Error<TResult>(e.ExceptionObject),
            _ => new Error<TResult>(new Exception("Unexpected Maybe state"))
        };
    }
    catch (Exception ex)
    {
        return new Error<TResult>(ex);
    }
}
```

### BindAsync Method (Asynchronous)

```C#
public static async Task<Maybe<TResult>> BindAsync<TSource, TResult>(
    this Maybe<TSource> maybe, 
    Func<TSource, Task<TResult>> func)
{
    try
    {
        return maybe switch
        {
            Something<TSource> s => new Something<TResult>(await func(s.Value)),
            Nothing<TSource> _ => new Nothing<TResult>(),
            Error<TSource> e => new Error<TResult>(e.ExceptionObject),
            _ => new Error<TResult>(new Exception("Unexpected Maybe state"))
        };
    }
    catch (Exception ex)
    {
        return new Error<TResult>(ex);
    }
}
```

### Key Features

- **Error Handling:**  
  Both methods use a try-catch block to transform exceptions into an `Error<T>` state, reducing the need for external
  error handling.

- **Short-Circuiting:**  
  If an error occurs at any step, the pipeline stops executing further actions and propagates the error.

---

## Summary and Key Takeaways

This article explored a generic implementation of `Maybe<T>` in C#, demonstrating:

- How to represent three states (Something, Nothing, Error) using records.
- The benefits of separating data from behavior via pattern matching.
- How functional programming techniques, such as the use of `Bind` and `BindAsync`, allow for clean and concise
  pipelines.
- The importance of error handling and short-circuiting in functional pipelines.
- The distinction between functional and object-oriented programming paradigms.

**Key Takeaways:**

- **Immutability:**  
  Use immutable data structures (e.g., records, immutable collections) to ensure consistency and thread safety.
- **Error Handling:**  
  Incorporate error handling within functional pipelines to reduce external try-catch boilerplate.
- **Separation of Concerns:**  
  Keep data (the `Maybe<T>` type) separate from behavior (pattern matching and transformation functions).
- **Adopt Functional Techniques:**  
  Consider using functional programming concepts like `Bind`, `BindAsync`, and LINQ to simplify data processing and
  improve code maintainability.

This approach provides a robust framework for managing variable outcomes in a predictable and composable manner.