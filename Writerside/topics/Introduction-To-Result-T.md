# Introduction to Result&lt;T&gt;

This article introduces the `Result<T>` pattern in C#, a way to explicitly represent the outcome of an operation that
might succeed with a value or fail with an error. This pattern promotes clearer, more robust code compared to relying
solely on exceptions for expected failure scenarios or returning nulls.

---

## Defining the `Result<T>` Concept

Conceptually, `Result<T>` is a type that encapsulates two primary possibilities:

1. **Success: ** The operation completed successfully and holds a value of type `T`.
2. **Failure: ** The operation failed, and the `Result<T>` holds information about the error.

We can model this using a base type and specific subtypes. Here's a minimal, usable implementation:

```C#
// Base type for all results
public abstract record Result<T>;

// Represents a successful outcome with a value
public sealed record Success<T>(T Value) : Result<T>;

// Represents a failure outcome with error information
// 'ErrorDetails' could be replaced with Exception or a more complex type
public sealed record Failure<T>(ErrorDetails Error) : Result<T>;

// Basic placeholder for error information
public record ErrorDetails(string Message);

// Optional static helper for creating results easily
public static class Result
{
    public static Result<T> Ok<T>(T value) => new Success<T>(value);
    public static Result<T> Fail<T>(string errorMessage) => new Failure<T>(new ErrorDetails(errorMessage));
    public static Result<T> Fail<T>(ErrorDetails errorDetails) => new Failure<T>(errorDetails);
}
```

### The Failure State

The `Failure<T>` state indicates that the operation did not succeed. It carries an `ErrorDetails` object (or similar)
explaining *why* it failed. You can adapt `ErrorDetails` to carry more structured information like error codes or the
original exception if needed.

---

## Functional Pipelines vs. Traditional Error Handling

Traditionally, methods might signal failure by throwing exceptions or returning `null`. The `Result<T>` pattern
encourages a more functional approach to using pipelines.

The `Result<T>` type itself primarily holds the *state* (Success or Failure). The *behavior* (chaining operations,
transforming values, handling errors) is managed by extension methods like `Map` and `FlatMap`. This separation of
data (the `Result<T>` state) and behavior (the pipeline methods) is a powerful aspect of this pattern.

---

## Using `Result<T>` in Applications

This pattern is particularly useful when calling services, interacting with external systems, or performing any
multistep process where any step might fail. Methods return `Result<T>` instead of `T` directly, making the possibility
of failure explicit.

### Example: Service Interface

A service method signature clearly indicates it returns a result:

```C#
public interface ISomeService
{
    Task<Result<ProcessedData>> ProcessDataAsync(InputData input);
}
```

### Example: Using a `Result<T>` Pipeline

Here's a conceptual example showing how a pipeline might look when calling the service, using the extension methods
defined later:

```C#
public async Task<IActionResult> HandleProcessingRequestAsync(InputData requestData)
{
    // Start the pipeline with an initial successful Result
    var pipeline = Result.Ok(requestData)
        // Chain the asynchronous service call using FlatMapAsync
        .FlatMapAsync(data => _service.ProcessDataAsync(data))
        // If successful, transform the data into a view model using MapAsync
        .MapAsync(processedData => new ProcessedDataViewModel(processedData));

    // Await the pipeline's final result
    Result<ProcessedDataViewModel> finalResult = await pipeline;

    // Handle the final outcome explicitly using pattern matching
    return finalResult switch
    {
        Success<ProcessedDataViewModel> success => Ok(success.Value),
        Failure<ProcessedDataViewModel> failure => BadRequest(failure.Error.Message), // Use error info
        _ => StatusCode(500) // Should not happen with sealed types if handled properly
    };
}

// Placeholder types for the example
public record InputData(string Id);
public record ProcessedData(string Content);
public record ProcessedDataViewModel(string DisplayContent);
public partial record ProcessedDataViewModel {
    public ProcessedDataViewModel(ProcessedData data) : this(data.Content) { }
}
// Assume _service is an instance of ISomeService
```

In this flow:

1. The process starts with an initial `Success<InputData>`.
2. `FlatMapAsync` chains the service call. If `_service.ProcessDataAsync` returns a `Failure`, the pipeline
   short-circuits.
3. `MapAsync` transforms the `ProcessedData` into a `ProcessedDataViewModel` *only if* the service call was successful.
4. The final `Result` is explicitly handled to generate the HTTP response.

---

## Core Pipeline Extension Methods: Map and FlatMap

`Map` and `FlatMap` (and their async counterparts) are fundamental to building `Result<T>` pipelines. Here are sample
implementations:

```C#
public static class ResultExtensions
{
    public static Result<U> Map<T, U>(this Result<T> result, Func<T, U> transformFunc)
    {
        return result switch
        {
            Success<T> s => SafeExecute(() => Result.Ok(transformFunc(s.Value))),
            Failure<T> f => Result.Fail<U>(f.Error), // Propagate failure
            _ => Result.Fail<U>("Unknown result type") // Handle unexpected cases
        };
    }

    public static async Task<Result<U>> MapAsync<T, U>(this Task<Result<T>> resultTask, Func<T, U> transformFunc)
    {
        var result = await resultTask;
        return result.Map(transformFunc); // Reuse synchronous Map logic
    }
        
    public static Result<U> FlatMap<T, U>(this Result<T> result, Func<T, Result<U>> nextOperationFunc)
    {
        return result switch
        {
            Success<T> s => SafeExecute(() => nextOperationFunc(s.Value)),
            Failure<T> f => Result.Fail<U>(f.Error), // Propagate failure
            _ => Result.Fail<U>("Unknown result type") // Handle unexpected cases
        };
    }
    
    public static async Task<Result<U>> FlatMapAsync<T, U>(this Task<Result<T>> resultTask, Func<T, Task<Result<U>>> nextOperationFunc)
    {
        var result = await resultTask;
        return result switch
        {
            Success<T> s => await SafeExecuteAsync(async () => await nextOperationFunc(s.Value)),
            Failure<T> f => Result.Fail<U>(f.Error), // Propagate failure
            _ => Result.Fail<U>("Unknown result type") // Handle unexpected cases
        };
    }   
    private static Result<T> SafeExecute<T>(Func<Result<T>> func)
    {
        try
        {
            return func();
        }
        catch (Exception ex)
        {
            // Convert unexpected exceptions into Failure results
            return Result.Fail<T>($"Operation failed: {ex.Message}");
        }
    }
      
     private static async Task<Result<T>> SafeExecuteAsync<T>(Func<Task<Result<T>>> asyncFunc)
    {
        try
        {
            return await asyncFunc();
        }
        catch (Exception ex)
        {
            // Convert unexpected exceptions into Failure results
            return Result.Fail<T>($"Async operation failed: {ex.Message}");
        }
    }
}
```

### Understanding Map

`Map` applies a transformation function (`transformFunc`) to the value inside a `Success<T>`, creating a `Success<U>`.
If the input is `Failure`, `Map` bypasses the function and passes the `Failure` through. It's for changing the *type* or
*value* within a success state.

### Understanding FlatMap

`FlatMap` (or `Bind`) chains operations where the next step (`nextOperationFunc`) *also* returns a `Result`. If the
input is `Success<T>`, `FlatMap` executes the function and returns its result (`Result<U>`). **Crucially, `FlatMap`
prevents nesting like `Result<Result<U>>`**, which would occur if `Map` were used with a function returning a `Result`.
It "flattens" the output. If the input is `Failure<T>`, the function is skipped, and the `Failure` propagates.

### Key Features of the Implementations

- **Error Handling: ** The `SafeExecute` / `SafeExecuteAsync` helpers wrap function execution in `try-catch`. Exceptions
  thrown inside your transformation or chaining functions are caught and turned into `Failure` results, preventing
  crashes for expected function-level errors.
- **Short-Circuiting: ** The `switch` expressions naturally implement short-circuiting. If a `Failure` is encountered,
  the later transformation/operation function is not called, and the `Failure` is passed along the chain.

---

## Summary and Key Takeaways

This article introduced the `Result<T>` pattern as a robust way to handle operations with potential success or failure
outcomes in C#. Key points include:

- Representing outcomes explicitly using `Success<T>` and `Failure<T>` subtypes.
- Separating the state (`Result<T>`) from behavior (pipeline extension methods).
- Using functional pipeline methods like `Map` (for transformation) and `FlatMap` (for chaining and avoiding nested
  results) to compose operations.
- Leveraging built-in error handling (exception-to-Failure conversion) and short-circuiting within the pipeline methods.

**Key Takeaways:**

- **Explicit Outcomes: ** Make success and failure explicit return types, improving code clarity and forcing callers to
  handle both cases.
- **Composability:** Build complex workflows by chaining simple operations together using `Map` and `FlatMap`.
- **Simplified Error Logic: ** Reduce deep nesting of `if` statements or `try-catch` blocks for *expected* failures by
  letting the pipeline handle propagation.
- **Separation of Concerns: ** Keep the core operation logic separate from the pipeline orchestration. Ensure functions
  used *within* the pipeline focus on a single responsibility.
- **Functional Style: ** Embrace functional concepts for cleaner, more declarative handling of sequential operations and
  their potential failures.

The `Result<T>` pattern, along with `Map` and `FlatMap` extensions, provides a powerful and elegant way to manage
operations that might not always succeed, leading to more predictable and maintainable code. You can adapt the
`ErrorDetails` and exception handling within `SafeExecute` to fit your specific application's needs.
