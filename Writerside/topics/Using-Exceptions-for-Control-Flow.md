# Using Exceptions for Control Flow

Using exceptions as a means of controlling program flow is generally
considered an antipattern in software development. Exceptions are intended
to handle "exceptional" conditions that a program should not expect to
occur frequently. Relying on them for regular control flow can lead to
code that is more difficult to read, debug, and maintain. Moreover,
exceptions can be expensive in terms of system resources. Instead,
consider using specialized result types like `Result<T>` to handle
expected conditions.

## Example: Throwing Exceptions for Control Flow

Below is an example of an ASP.NET Core controller method that calls a
service. The service method uses exceptions for handling different
outcomes in a user input processing operation.

```C#
public class DataController : ControllerBase
{
    private readonly DataProcessingService _processingService;

    public DataController(DataProcessingService processingService)
    {
        _processingService = processingService;
    }

    public IActionResult ProcessUserData([FromQuery] string userInput)
    {
        try
        {
            var processedData = _processingService.ProcessInput(userInput);
            return Ok(processedData);
        }
        catch (ArgumentNullException ane) // From service
        {
            return BadRequest($"Input error: {ane.ParamName} is required.");
        }
        catch (FormatException fe) // From service
        {
            return BadRequest($"Invalid format: {fe.Message}");
        }
        catch (ArgumentOutOfRangeException aoore) // From service
        {
            return BadRequest($"Invalid value: {aoore.Message}");
        }
        catch (OperationFailedException ofe) // From service
        {
            return UnprocessableEntity($"Operation failed: {ofe.Message}");
        }
        catch (Exception ex) // Catch-all for unexpected errors
        {
            // Log the generic exception: _logger.LogError(ex, "...");
            return StatusCode(StatusCodes.Status500InternalServerError,
                "An unexpected error occurred.");
        }
    }
}
```

This controller method uses a `try-catch` block to handle various
exceptions thrown by the service method.

### Service Method Using Exceptions

The service method below uses exceptions to signal different outcomes.

```C#
public class ProcessedData
{
    public string Value { get; set; }
}

// Custom exception for example
public class OperationFailedException : Exception
{
    public OperationFailedException(string message) : base(message) { }
}

public class DataProcessingService
{
    public ProcessedData ProcessInput(string rawInput)
    {
        if (string.IsNullOrWhiteSpace(rawInput))
        {
            throw new ArgumentNullException(nameof(rawInput),
                "Input cannot be null or empty.");
        }

        int number;
        try
        {
            number = int.Parse(rawInput);
        }
        catch (FormatException fe)
        {
            throw new FormatException("Input must be a valid integer.", fe);
        }

        if (number <= 0)
        {
            throw new ArgumentOutOfRangeException(nameof(rawInput),
                "Input number must be positive.");
        }

        if (number == 99) // Simulate a specific business rule failure
        {
            throw new OperationFailedException(
                "Processing for number 99 is not allowed.");
        }
        return new ProcessedData { Value = $"Processed: {number}" };
    }
}
```

## A Better Alternative: Result Object Pattern

Instead of using exceptions for control flow, a better approach is to
use a result object that encapsulates the success or failure outcome along
with any relevant data or error details.

(See [**Introduction to `Result<T>`**](Introduction-To-Result-T.md)
for the definition of `Result<T>`, `Success<T>`, `Failure<T>`, and the
`Result.Ok()`, `Result.Fail()` helpers used below.)

Here's how we can refactor the above example:

```C#
public class DataController : ControllerBase
{
    private readonly DataProcessingService _processingService;

    public DataController(DataProcessingService processingService)
    {
        _processingService = processingService;
    }

    public IActionResult ProcessUserData([FromQuery] string userInput)
    {
        if (userInput == null) // Controller level input check
        {
            // Directly create ProblemDetails or use a helper
            var problemDetails = new ProblemDetails
            {
                Status = StatusCodes.Status400BadRequest,
                Title = "Missing Input",
                Detail = "Query parameter 'userInput' is missing."
            };
            return new ObjectResult(problemDetails)
            {
                StatusCode = problemDetails.Status
            };
        }
        
        var result = _processingService.ProcessInput(userInput);

        return result switch
        {
            Success<ProcessedData> s => Ok(s.Value),
            Failure<ProcessedData> f => new ObjectResult(f.Problem)
            {
                // Ensure status code from ProblemDetails is used
                StatusCode = f.Problem.Status ??
                             StatusCodes.Status500InternalServerError
            },
            _ => StatusCode(StatusCodes.Status500InternalServerError,
                "An unexpected result type was returned.")
        };
    }
}
```

And here's how the service method would be refactored to return `Result<T>`
with `ProblemDetails`:

```C#
public class DataProcessingService
{
    public Result<ProcessedData> ProcessInput(string rawInput)
    {
        if (string.IsNullOrWhiteSpace(rawInput))
        {          
            return Result.Fail<ProcessedData>(
                StatusCodes.Status400BadRequest,
                "Invalid Input",
                "Input cannot be null or empty.");
        }

        int number;
        if (!int.TryParse(rawInput, out number))
        {
            return Result.Fail<ProcessedData>(
                StatusCodes.Status400BadRequest,
                "Invalid Format",
                "Input must be a valid integer.");
        }

        if (number <= 0)
        {
            return Result.Fail<ProcessedData>(
                StatusCodes.Status400BadRequest,
                "Invalid Value",
                "Input number must be positive.");
        }

        if (number == 99) // Simulate a specific business rule failure
        {
            return Result.Fail<ProcessedData>(
                StatusCodes.Status422UnprocessableEntity,
                "Business Rule Violation",
                "Processing for number 99 is not allowed.");
        }

        // Simulate a successful operation
        return Result.Ok(
            new ProcessedData { Value = $"Processed: {number}" }
        );
    }
}
```

## Benefits of the Result Object Pattern (with ProblemDetails)

1.  **Explicit flow control**: The `Result<T>` signature clearly indicates
    that an operation can succeed or fail with structured error details.
2.  **Better performance**: Avoids exception overhead for predictable
    failures.
3.  **Improved readability**: The service method defines the exact HTTP
    response characteristics (`StatusCode`, `Title`, `Detail`) for failures
    via `ProblemDetails`. The controller transparently uses this.
4.  **More maintainable**: API error responses are defined closer to the
    logic that determines them (in the service). The controller's role
    in error handling is minimized to simply passing through the
    `ProblemDetails` from a `Failure<T>`.
5.  **Better testability**: Service methods can be tested to ensure they
    return the correct `ProblemDetails` for various failure scenarios.
6.  **Supports pure methods**: Enables methods to be "pure," where the same input
    always yields the same output with no side effects. This leads to
    more predictable code. A pure method that can't compute a result due to invalid input
    can return `Result.Fail` rather than throwing an exception, maintaining its truthfulness
    about the operation outcome. See:**[](Functional-in-the-Small-and-Object-Oriented-in-the-Large.md#side-effects)**

By using `Result<T>` with embedded `ProblemDetails`, the service layer takes
responsibility for crafting detailed error responses, simplifying the
controller and leading to a cleaner separation of concerns.

### Why Exceptions are Expensive

Exceptions come with significant performance costs due to several factors:

-   **Call Stack Unwinding**: When an exception is thrown, the runtime
    must unwind the call stack, searching for an appropriate handler.
-   **Stack Trace Generation**: Creating the stack trace for an exception
    requires capturing the call stack, which is computationally expensive.
-   **Memory Allocation**: Exceptions are objects allocated on the heap.
-   **Just-In-Time Compilation**: Throwing can cause the JIT compiler to
    generate less optimized code in methods that might throw.

For these reasons, exceptions should be used for truly exceptional,
unexpected conditions rather than expected error cases that are part of
normal program flow.

### Testing

-   **Easier to test**:
    Returning values or `Result<T>` objects can simplify tests because
    you no longer need to set up tests to expect specific exceptions
    for predictable failures.
-   **Example**:
    Instead of (exception-based):

    ```C#
    // Assume _service.ProcessInput throws ArgumentOutOfRangeException
    // for non-positive numbers.
    Assert.Throws<ArgumentOutOfRangeException>(() => 
        _service.ProcessInput("0"));
    ```

    You could test the `Result<T>` version like this:

    ```C#
    var result = _service.ProcessInput("0"); // Uses Result-based service
    Assert.True(result is Failure<ProcessedData>);
    var failure = result as Failure<ProcessedData>;
    Assert.NotNull(failure.Problem); // Check that ProblemDetails exists
    Assert.Equal(StatusCodes.Status400BadRequest, failure.Problem.Status);
    Assert.Equal("Invalid Value", failure.Problem.Title);
    // Optionally check failure.Problem.Detail if set
    ```

### When Are Exceptions Appropriate?

-   **Ensuring a valid application object graph**:
    During application startup or dependency injection, throwing may be
    necessary if a critical component is missing or misconfigured.
-   **Constructor failures**:
    If an object cannot be safely constructed in a valid state (violating
    its invariants), throwing an exception is often justified.
-   **Unrecoverable system errors**:
    For issues like `OutOfMemoryException` or critical I/O failures
    where the application cannot reasonably continue.
-   **Violations of method contracts in unexpected ways**:
    If a method receives parameters that should have been impossible
    given the calling context (e.g., a null passed to a private helper
    that assumes non-null after public checks), an `ArgumentNullException`
    might still be appropriate to signal a programming error.

Exceptions should be reserved for truly exceptional circumstances that
prevent the normal operation of your application, not for handling
predictable error conditions that can be managed through return values or
`Result<T>` objects.

---
See Also:

- [Introduction to `Result<T>`](Introduction-To-Result-T.md)
  (Result object alternative)
