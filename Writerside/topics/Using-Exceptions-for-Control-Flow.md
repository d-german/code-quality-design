# Using Exceptions for Control Flow

Using exceptions as a means of controlling program flow is generally
considered an antipattern in software development. Exceptions are intended
to handle "exceptional" conditions that a program should not expect to
occur frequently. Relying on them for regular control flow can lead to
code that is more difficult to read, debug, and maintain. Moreover,
exceptions can be expensive in terms of system resources, as the process
of creating, throwing, and catching exceptions involves significant
overhead. Instead of using exceptions for control flow, consider using
specialized result types to handle expected conditions.

## Example: Throwing Exceptions for Control Flow

Below is an example of an ASP.NET Core controller method that calls a
service. The service method uses exceptions for handling different
outcomes in a user input processing operation.

```C#
// In your controller:
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
            // Assume Guard.AuthTokenIsValid() checks auth
            // Guard.AuthTokenIsValid(Request?.Headers);

            var processedData = _processingService.ProcessInput(userInput);
            return Ok(processedData);
        }
        catch (ArgumentNullException ane)
        {
            return BadRequest($"Input error: {ane.ParamName} is required.");
        }
        catch (FormatException fe)
        {
            return BadRequest($"Invalid format: {fe.Message}");
        }
        catch (ArgumentOutOfRangeException aoore)
        {
            return BadRequest($"Invalid value: {aoore.Message}");
        }
        catch (OperationFailedException ofe)
        {
            // Custom exception for specific business rule failures
            return UnprocessableEntity($"Operation failed: {ofe.Message}");
        }
        // catch (AuthenticationException aex)
        // {
        //     return Unauthorized();
        // }
        catch (Exception ex)
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

The service method below also uses exceptions to signal different outcomes
(e.g., invalid input, processing errors). Each case throws an exception
to indicate the result, which is then caught by the controller.

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
            // Re-throw or wrap if adding more context,
            // or let original exception propagate.
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

        // Simulate a successful operation
        return new ProcessedData { Value = $"Processed: {number}" };
    }
}
```

## A Better Alternative: Result Object Pattern

Instead of using exceptions for control flow, a better approach is to
use a result object that encapsulates the success or failure outcome along
with any relevant data or error details.

(See [**Introduction to `Result<T>`**](Introduction-To-Result-T.md)
for the definition of `Result<T>`, `Success<T>`, `Failure<T>`,
`ErrorDetails`, and the `Result.Ok()`, `Result.Fail()` helpers used below.)

Here's how we can refactor the above example:

```C#
// In your controller:
public class DataController : ControllerBase
{
    private readonly DataProcessingService _processingService;

    public DataController(DataProcessingService processingService)
    {
        _processingService = processingService;
    }

    public IActionResult ProcessUserData([FromQuery] string userInput)
    {
        // Example: Basic input check before service call
        if (userInput == null) // Controller level check
        {
            return BadRequest("Query parameter 'userInput' is missing.");
        }
        // Assume AuthHelper.IsValid(Request?.Headers) -> Result<AuthInfo>
        // var authResult = AuthHelper.IsValid(Request?.Headers);
        // if (authResult is Failure<AuthInfo> authFailure)
        //     return Unauthorized(authFailure.Error.Message);

        var result = _processingService.ProcessInput(userInput);

        return result switch
        {
            Success<ProcessedData> s => Ok(s.Value),
            Failure<ProcessedData> f => f.Error.Message switch
            {
                // Match specific error messages or add error codes
                // to ErrorDetails for more robust matching.
                var msg when msg.Contains("null or empty") =>
                    BadRequest(f.Error.Message),
                var msg when msg.Contains("valid integer") =>
                    BadRequest(f.Error.Message),
                var msg when msg.Contains("must be positive") =>
                    BadRequest(f.Error.Message),
                var msg when msg.Contains("not allowed") =>
                    UnprocessableEntity(f.Error.Message),
                _ => StatusCode(StatusCodes.Status500InternalServerError,
                                f.Error.Message) // Default failure
            },
            _ => StatusCode(StatusCodes.Status500InternalServerError,
                            "An unexpected result type was returned.")
        };
    }
}
```

And here's how the service method would be refactored to return `Result<T>`:

```C#
// ProcessedData class remains the same.
// No custom OperationFailedException needed if using Result<T>.

public class DataProcessingService
{
    public Result<ProcessedData> ProcessInput(string rawInput)
    {
        if (string.IsNullOrWhiteSpace(rawInput))
        {
            return Result.Fail<ProcessedData>(
                "Input cannot be null or empty.");
        }

        int number;
        if (!int.TryParse(rawInput, out number))
        {
            return Result.Fail<ProcessedData>(
                "Input must be a valid integer.");
        }

        if (number <= 0)
        {
            return Result.Fail<ProcessedData>(
                "Input number must be positive.");
        }

        if (number == 99) // Simulate a specific business rule failure
        {
            return Result.Fail<ProcessedData>(
                "Processing for number 99 is not allowed.");
        }

        // Simulate a successful operation
        return Result.Ok(
            new ProcessedData { Value = $"Processed: {number}" }
        );
    }
}
```

## Benefits of the Result Object Pattern

1.  **Explicit flow control**: The code clearly shows the possible outcomes
    and how they're handled by returning and inspecting the `Result<T>`.
2.  **Better performance**: Avoids the overhead of creating and handling
    exceptions for expected, non-exceptional failure conditions.
3.  **Improved readability**: Makes it easier to understand the different
    possible outcomes of a method by looking at its signature and how
    the `Result<T>` is constructed and consumed.
4.  **More maintainable**: Changes to result handling can often be made by
    modifying the `Result<T>` construction or the consuming pattern
    matching, without restructuring large `try-catch` blocks.
5.  **Better testability**: Easier to test different outcomes by checking
    the returned `Result<T>` state and value/error.

By using result objects instead of exceptions for *expected* control flow,
we create code that is more intention-revealing, performs better for
common failure paths, and is easier to maintain and test.

## Throwing Exceptions Alternatives

### Returning Errors as Values

1.  **Return a value**:
    When appropriate, return a nullable type (e.g., `int?`), an empty
    collection, or an empty string rather than throwing an exception for
    "not found" or "no data" scenarios.
2.  **Use a `Result` type**:
    Consider returning a specialized type like `Result<T>` that
    indicates success or failure without throwing for predictable errors.

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

### When to Throw an Exception

-   **Pure methods**:
    If possible, design methods to be "pure," where the same input
    always yields the same output with no side effects. This leads to
    more predictable code. If a pure method cannot compute its result
    due to invalid input not caught by preconditions, `Result.Fail` is
    often better than throwing.

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
    // Check specific error message or code in failure.Error
    Assert.Equal("Input number must be positive.", failure.Error.Message);
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
