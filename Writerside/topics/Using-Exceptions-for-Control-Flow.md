# Using Exceptions for Control Flow

Using exceptions as a means of controlling program flow is generally considered an anti-pattern in software development.
Exceptions are intended to handle “exceptional” conditions that a program should not expect to occur frequently. Relying
on them for regular control flow can lead to code that is more difficult to read, debug, and maintain. Moreover,
exceptions can be expensive in terms of system resources, as the process of creating, throwing, and catching exceptions
involves significant overhead. Instead of using exceptions for control flow, consider using specialized result types to
handle expected conditions.

---

## Example: Throwing Exceptions for Control Flow

Below is an example of an ASP.NET Core controller method that uses exceptions for handling different outcomes in a
search operation:

```C#
public async Task<IActionResult> EncounterSearch(
    [FromQuery(Name = "filter")] string base64EncodedFilter,
    [FromQuery(Name = "includeVIP")] string vipFilter)
{
    try
    {
        Guard.VerifyArgumentNotNull(base64EncodedFilter);
        Guard.AuthTokenIsNotNullOrWhitespace(Request?.Headers);
        var encounters = await _encounterAdapter.EncounterSearch(base64EncodedFilter, vipFilter);        
        return new OkObjectResult(encounters);
    }
    catch (NotSuccessfulException nse)
    {
        if (nse.StatusCode == StatusCodes.Status400BadRequest)
        {
            return new BadRequestObjectResult(
                ProblemDetailsHelper.BadRequestProblemDetails(nse.Content));
        }
        else
        {
            return new ContentResult
            {
                StatusCode = nse.StatusCode,
                Content = nse.Content,
                ContentType = Constants.ApplicationJson
            };
        }
    }
    catch (ArgumentException ae)
    {
        var problem = ProblemDetailsHelper.BadRequestProblemDetails(
            DiagnosticConstants.EncounterFilterInvalidFormat,
            DiagnosticConstants.IncompatibleEncounterFilter);        
        return BadRequest(problem);
    }
    catch (GatewayTimeoutException gte)
    {
        var problemDetails = ProblemDetailsHelper.GatewayProblemDetails();
        return new ObjectResult(problemDetails)
        {
            StatusCode = StatusCodes.Status504GatewayTimeout
        };
    }
    catch (InvalidFilterException ife)
    {
        if (!string.IsNullOrWhiteSpace(ife.DetailedMessage))
        {
            logging.AddLogDatum(DiagnosticConstants.DetailedException, ife.DetailedMessage);
        }
        var problemDetails = ProblemDetailsHelper.BadRequestProblemDetails(ife.Message);
        return new BadRequestObjectResult(problemDetails);
    }
    catch (UnsupportedFilterException ufe)
    {
        return new OkObjectResult(null);
    }
    catch (AuthorizationException ahe)
    {
        return Unauthorized();
    }
    catch (NotImplementedException nie)
    {
        return StatusCode(StatusCodes.Status501NotImplemented);
    }
    catch (Exception ex) when (ex is FormatException || ex is Newtonsoft.Json.JsonException)
    {
        var problemDetails = ProblemDetailsHelper.BadRequestProblemDetails(
            DiagnosticConstants.EncounterFilterInvalidFormat);
        return new BadRequestObjectResult(problemDetails);
    }
    catch (Exception ex)
    {
        return StatusCode(StatusCodes.Status500InternalServerError);
    }
}
```

This method uses exceptions to handle various error cases and return the appropriate HTTP status codes. Although this
approach works, it relies heavily on throwing exceptions as part of normal control flow.

### Additional Service Method

The service method below also uses exceptions to signal different outcomes (e.g., HTTP 400, 404, 501). Each case throws
an exception to indicate the result, which is then caught and translated into an appropriate response:

```C#
public async Task<IEncounterCollection> EncounterSearch(string filter, string vipFilter)
{
    if (string.IsNullOrWhiteSpace(filter))
    {
        throw new InvalidFilterException(DiagnosticConstants.FilterRequiredForEncounterSearch);
    }

    var tasks = _hylandPatientAPIContext.Adapters.Values.Select(adapter =>
    {
        return _encounterService.EncounterSearchAsync(adapter, filter, vipFilter);
    });

    var results = await Task.WhenAll(tasks);
    var aggregateStatusCodes = results.AggregateResponseStatusAndStatusCode<EncounterAggregator>(_structuredLoggingFactory);

    if (aggregateStatusCodes.StatusCode == HttpStatusCode.OK)
    {
        return await AggregateEncountersFromResponses(results, aggregateStatusCodes.ResultStatus);
    }
    else if (aggregateStatusCodes.StatusCode == HttpStatusCode.BadRequest)
    {
        throw new NotSuccessfulException(
            GlobalDiagnosticConstants.BadRequestTitle,
            (int)aggregateStatusCodes.StatusCode,
            DiagnosticConstants.NoEncountersFound);
    }
    else if (aggregateStatusCodes.StatusCode == HttpStatusCode.Unauthorized)
    {
        throw new NotSuccessfulException(
            DiagnosticConstants.Unauthorized,
            (int)aggregateStatusCodes.StatusCode,
            DiagnosticConstants.AuthorizationHeaderError);
    }
    else if (aggregateStatusCodes.StatusCode == HttpStatusCode.NotFound)
    {
        throw new NotSuccessfulException(
            GlobalDiagnosticConstants.NotFoundTitle,
            (int)aggregateStatusCodes.StatusCode,
            DiagnosticConstants.NoEncountersFound);
    }
    else if (aggregateStatusCodes.StatusCode == HttpStatusCode.NotImplemented)
    {
        throw new NotSuccessfulException(
            DiagnosticConstants.MethodNotImplemented,
            (int)aggregateStatusCodes.StatusCode,
            DiagnosticConstants.MethodNotImplemented);
    }
    else if (aggregateStatusCodes.StatusCode == HttpStatusCode.GatewayTimeout)
    {
        throw new GatewayTimeoutException();
    }
    else
    {
        throw new NotSuccessfulException(
            GlobalDiagnosticConstants.NotFoundTitle,
            (int)aggregateStatusCodes.StatusCode,
            DiagnosticConstants.NoEncountersFound);
    }
}
```

While this design might be convenient for signaling errors, it effectively uses exceptions to control normal flow (e.g.,
“not found,” “bad request,” etc.), which is generally discouraged.

---

## Throwing Exceptions Alternatives

### Returning Errors as Values

1. **Return a value**  
   When appropriate, return a nullable type, an empty collection, or an empty string rather than throwing an exception.
2. **Use a `Result` type**  
   Consider returning a specialized “either” type that indicates success or failure without throwing.

### When to Throw an Exception

- **Exceptions can be expensive**  
  Throwing exceptions is generally more costly than returning a value or result.
- **Pure methods**  
  If possible, design methods to be “pure,” meaning the same input always yields the same output with no side effects.
  This leads to more predictable code.

### Testing

- **Easier to test**  
  Returning values or results can simplify tests, because you no longer need to handle or expect thrown exceptions.
- **Example**  
  Instead of:
  ```C#
  Assert.Throws<NegativeNumberException>(() => GetMilesPerGallon(1.0, 5.0));
  ```
  you could return an error code or a result object, making the test straightforward.

### When Are Exceptions Appropriate?

- **Ensuring a valid application object graph**  
  During application startup or dependency injection, throwing may be necessary if a critical component is missing.
- **Constructor failures**  
  If an object cannot be safely constructed in a valid state, throwing an exception is often justified.
- **Service injection**  
  If a required service is not provided, throwing can signal the error early.

---

## Example: Specialized Result Types

One way to avoid using exceptions for regular control flow is to return a specialized result type. For instance:

```C#
public class ProblemDetails
{
    public string? Type { get; set; }
    public string? Title { get; set; }
    public int? Status { get; set; }
    public string? Detail { get; set; }
    public string? Instance { get; set; }
}

public record RepositoryResponse
{
    public int Status { get; init; }
    public ProblemDetails? ProblemDetails { get; init; }
    public bool IsSuccess => Status.IsSuccessfulStatusCode();
}

public sealed record RepositoryResponse<T> : RepositoryResponse
{
    public T? ResponseObject { get; init; }
}
```

By returning a `RepositoryResponse<T>`, the caller can check the `IsSuccess` property instead of catching an exception
to determine whether the operation succeeded. For example:

```C#
public async Task<ActionResult<Patient>> GetByPatientIdAsync(
    [RequiredFromQuery][Required] string patientId,
    [FromQuery] string assigningAuthority)
{
    try
    {
        var response = await _repository.GetPatientByPatientIdAsync(patientId, assigningAuthority, AuthToken);
        if (!response.IsSuccess)
        {
            return StatusCode(response.Status, response.ProblemDetails);
        }
        return response.ResponseObject;
    }
    catch (Exception ex)
    {
        return StatusCode(StatusCodes.Status500InternalServerError);
    }
}
```

In this approach, most of the error handling is done by returning a `RepositoryResponse` with an appropriate status and
problem details, rather than throwing exceptions.

---

## Conclusion

Using exceptions for regular control flow can lead to code that is harder to maintain and debug, and exceptions
themselves can be costly. Instead, consider returning specialized result types or other forms of error codes when
dealing with expected or routine conditions. By reserving exceptions for truly exceptional scenarios, you can create
more predictable, testable, and robust software.
