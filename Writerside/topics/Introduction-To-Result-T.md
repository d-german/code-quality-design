# Introduction to Result&lt;T&gt;

This article introduces the `Result<T>` pattern in C#, a way to explicitly
represent the outcome of an operation that might succeed with a value or
fail with an error. This pattern promotes clearer, more robust code
compared to relying solely on exceptions for expected failure scenarios or
returning nulls.

---

## Defining the `Result<T>` Concept

Conceptually, `Result<T>` is a type that encapsulates two primary
possibilities:

1. **Success:** The operation completed successfully and holds a value
   of type `T`.
2. **Failure:** The operation failed, and the `Result<T>` holds structured
   error information, in this article using a `ProblemDetails`.

We can model this using a base type and specific subtypes. Here's a
minimal, usable implementation using `ProblemDetails` for errors.

```C#
public abstract record Result<T>;
public sealed record Success<T>(T Value) : Result<T>;
public sealed record Failure<T>(ProblemDetails Problem) : Result<T>;

// Optional static helper for creating results easily
public static class Result
{
    public static Result<T> Ok<T>(T value) => new Success<T>(value);

    public static Result<T> Fail<T>(ProblemDetails problemDetails) =>
        new Failure<T>(problemDetails);

    // Convenience overload for creating simple failures
    public static Result<T> Fail<T>(
        int statusCode,
        string title,
        string detail = null) =>
        new Failure<T>(new ProblemDetails
        {
            Status = statusCode,
            Title = title,
            Detail = detail
        });
}
```

### The Failure State

The `Failure<T>` state indicates that the operation did not succeed. It carries a `ProblemDetails` object (from
`Microsoft.AspNetCore.Mvc`). This object standardizes error reporting, allowing for an HTTP status code,
a title, detailed messages, and even extension members to convey rich error information. This approach aligns well with
building web APIs.

**Note:** This implementation is for educational purposes only. For production use,consider using established libraries
instead of implementing your own solution. The `ProblemDetails` approach used here is just one implementation choice
that may not be appropriate for all scenarios. This inheritance-based implementation will incur heap allocations for
each `Result<T>` instance, which may impact performance in high-throughput scenarios.
See **[](Memory-Allocation-and-Management-Reference-Types-Value-Types-Arrays-and-Linked-Lists.md)**

---

## Functional Pipelines vs. Traditional Error Handling

Traditionally, methods might signal failure by throwing exceptions or
returning `null`. The `Result<T>` pattern encourages a more functional
approach to using pipelines.

The `Result<T>` type itself primarily holds the *state* (Success or
Failure). The *behavior* (chaining operations, transforming values,
handling errors) is managed by extension methods like `Map` and `FlatMap`.
This separation of data (the `Result<T>` state) and behavior (the
pipeline methods) is a powerful aspect of this pattern.

---

## Using `Result<T>` in Applications

This pattern is particularly useful when calling services, interacting with
external systems, or performing any multistep process where any step might
fail. Methods return `Result<T>` instead of `T` directly, making the
possibility of failure explicit.

### Example: Service Interface

A service method signature clearly indicates it returns a result:

```C#
public interface ISomeService
{
    Task<Result<ProcessedData>> ProcessDataAsync(InputData input);
}
```

### Example: Using a `Result<T>` Pipeline

Here's a conceptual example showing how a pipeline might look when calling
the service, using the extension methods defined later:

```C#
public async Task<IActionResult> HandleProcessingRequestAsync(
    InputData requestData)
{
    // Start the pipeline with an initial successful Result
    var pipeline = Result.Ok(requestData)
        // Chain the asynchronous service call using FlatMapAsync
        .FlatMapAsync(data => _service.ProcessDataAsync(data))
        // If successful, transform the data into a view model
        .MapAsync(processedData =>
            new ProcessedDataViewModel(processedData));

    // Await the pipeline's final result
    Result<ProcessedDataViewModel> finalResult = await pipeline;

    // Handle the final outcome explicitly using pattern matching
    return finalResult switch
    {
        Success<ProcessedDataViewModel> s => Ok(s.Value),
        Failure<ProcessedDataViewModel> f => new ObjectResult(f.Problem)
        {
            StatusCode = f.Problem.Status ??
                         StatusCodes.Status500InternalServerError
        },
        _ => StatusCode(StatusCodes.Status500InternalServerError,
            "An unexpected result type was returned.")
    };
}

// Placeholder types for the example
public record InputData(string Id);
public record ProcessedData(string Content);
public record ProcessedDataViewModel(string DisplayContent);
public partial record ProcessedDataViewModel {
    public ProcessedDataViewModel(ProcessedData data) : this(data.Content) { }
}
// Assume _service is an instance of ISomeService and controller
// has using Microsoft.AspNetCore.Mvc; and Microsoft.AspNetCore.Http;
```

In this flow:

1. The process starts with an initial `Success<InputData>`.
2. `FlatMapAsync` chains the service call. If `_service.ProcessDataAsync`
   returns a `Failure`, the pipeline short-circuits.
3. `MapAsync` transforms the `ProcessedData` into a
   `ProcessedDataViewModel` *only if* the service call was successful.
4. The final `Result` is handled: `Success` yields an `OkObjectResult`,
   and `Failure` yields an `ObjectResult` containing the `ProblemDetails`.

---

## Core Pipeline Extension Methods: Map and FlatMap

`Map` and `FlatMap` (and their async counterparts) are fundamental to
building `Result<T>` pipelines. Here are sample implementations:

```C#
public static Result<U> Map<T, U>(
    this Result<T> result,
    Func<T, U> transformFunc)
{
    return result switch
    {
        Success<T> s => SafeExecuteTransform(() => 
                                transformFunc(s.Value)),
        Failure<T> f => Result.Fail<U>(f.Problem), // Propagate failure
        _ => Result.Fail<U>(
            StatusCodes.Status500InternalServerError,
            "Unknown Result Type",
            "An unexpected result type was encountered during Map.")
    };
}

public static async Task<Result<U>> MapAsync<T, U>(
    this Task<Result<T>> resultTask,
    Func<T, U> transformFunc)
{
    try
    {
        var result = await resultTask;
        return result switch
        {
            Success<T> s => SafeExecuteTransform(() => 
                                    transformFunc(s.Value)),
            Failure<T> f => Result.Fail<U>(f.Problem), // Propagate
            _ => Result.Fail<U>(
                StatusCodes.Status500InternalServerError,
                "Unknown Result Type",
                "An unexpected result type was encountered in MapAsync.")
        };
    }
    catch (Exception ex)
    {
        return Result.Fail<U>(
            StatusCodes.Status500InternalServerError,
            "Async Map Operation Exception",
            $"Exception during MapAsync: {ex.Message}");
    }
}
  
public static Result<U> FlatMap<T, U>(
    this Result<T> result,
    Func<T, Result<U>> nextOperationFunc)
{
    return result switch
    {
        Success<T> s => SafeExecuteNextOperation(() => 
                                nextOperationFunc(s.Value)),
        Failure<T> f => Result.Fail<U>(f.Problem), // Propagate failure
        _ => Result.Fail<U>(
            StatusCodes.Status500InternalServerError,
            "Unknown Result Type",
            "An unexpected result type was encountered.")
    };
}

public static async Task<Result<U>> FlatMapAsync<T, U>(
    this Task<Result<T>> resultTask,
    Func<T, Task<Result<U>>> nextOperationFunc)
{
    try
    {
        var result = await resultTask;
        return result switch
        {
            Success<T> s => await SafeExecuteNextOperationAsync(
                                async () => 
                                    await nextOperationFunc(s.Value)),
            Failure<T> f => Result.Fail<U>(f.Problem), // Propagate
            _ => Result.Fail<U>(
                StatusCodes.Status500InternalServerError,
                "Unknown Result Type",
                "An unexpected result type was encountered.")
        };
    }
    catch (Exception ex)
    {
        return Result.Fail<U>(
            StatusCodes.Status500InternalServerError,
            "Async FlatMap Operation Exception",
            $"Exception during FlatMapAsync: {ex.Message}");
    }
}

private static Result<U> SafeExecuteTransform<U>(Func<U> transform)
{
    try
    {
        return Result.Ok(transform());
    }
    catch (Exception ex)
    {
        return Result.Fail<U>(
            StatusCodes.Status500InternalServerError,
            "Transformation Exception",
            ex.Message);
    }
}

private static Result<U> SafeExecuteNextOperation<U>(
                            Func<Result<U>> nextOperation)
{
    try
    {
        return nextOperation();
    }
    catch (Exception ex)
    {
        return Result.Fail<U>(
            StatusCodes.Status500InternalServerError,
            "Next Operation Exception",
            ex.Message);
    }
}

private static async Task<Result<U>> SafeExecuteNextOperationAsync<U>(
    Func<Task<Result<U>>> asyncNextOperation)
{
    try
    {
        return await asyncNextOperation();
    }
    catch (Exception ex)
    {
        return Result.Fail<U>(
            StatusCodes.Status500InternalServerError,
            "Async Next Operation Exception",
            ex.Message);
    }
}
```

### Understanding Map

`Map` applies a transformation function (`transformFunc`) to the value
inside a `Success<T>`, creating a `Success<U>`. If the input is `Failure`,
`Map` bypasses the function and passes the `Failure` (with its
`ProblemDetails`) through. It's for changing the *type* or *value* within
a success state.

### Understanding FlatMap

`FlatMap` (or `Bind`) chains operations where the next step
(`nextOperationFunc`) *also* returns a `Result`. If the input is
`Success<T>`, `FlatMap` executes the function and returns its result
(`Result<U>`). **Crucially, `FlatMap` prevents nesting like
`Result<Result<U>>`**, which would occur if `Map` were used with a
function returning a `Result`. It "flattens" the output. If the input is
`Failure<T>`, the function is skipped, and the `Failure` propagates.

### Key Features of the Implementations

- **Error Handling:** The `try-catch` blocks in `MapAsync`,
  `FlatMapAsync`, and the `SafeExecute` helpers ensure that exceptions
  (whether from awaiting the input task or from executing the provided
  functions) are caught and converted into `Failure` results containing
  `ProblemDetails`.
- **Short-Circuiting:** The `switch` expressions naturally implement
  short-circuiting. If a `Failure` is encountered, the later
  transformation/operation function is not called, and the `Failure` is
  passed along the chain.

---

## Summary

This article introduced the `Result<T>` pattern, using `ProblemDetails` for
errors, as a robust way to handle operations with potential success or
failure outcomes in C#. Key points include:

- Representing outcomes explicitly using `Success<T>` and `Failure<T>`
  (with `ProblemDetails`).
- Separating the state (`Result<T>`) from behavior (pipeline extension
  methods).
- Using functional pipeline methods like `Map` (for transformation) and
  `FlatMap` (for chaining and avoiding nested results) to compose
  operations.
- Leveraging built-in error handling (exception-to-`Failure` conversion)
  and short-circuiting within the pipeline methods.

For production systems, consider using established libraries such as
OneOf, FluentResults, or LanguageExt that provide optimized, well-tested
implementations of this pattern.
