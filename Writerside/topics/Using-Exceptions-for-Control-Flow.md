# Using Exceptions for Control Flow

Using exceptions as a means of controlling program flow is generally considered an anti-pattern in software development.
Exceptions are intended to handle "exceptional" conditions that a program should not expect to occur frequently. Relying
on them for regular control flow can lead to code that is more difficult to read, debug, and maintain. Moreover,
exceptions can be expensive in terms of system resources, as the process of creating, throwing, and catching exceptions
involves significant overhead. Instead of using exceptions for control flow, consider using specialized result types to
handle expected conditions.

## Example: Throwing Exceptions for Control Flow

Below is an example of an ASP.NET Core controller method that uses exceptions for handling different outcomes in a
search operation:

```C#
public async Task<IActionResult> ProductSearch(
    [FromQuery(Name = "filter")] string base64EncodedFilter,
    [FromQuery(Name = "includeDiscontinued")] string discontinuedFilter)
{
    try
    {
        Guard.VerifyArgumentNotNull(base64EncodedFilter);
        Guard.AuthTokenIsNotNullOrWhitespace(Request?.Headers);
        var products = await _productAdapter.ProductSearch(
            base64EncodedFilter, 
            discontinuedFilter);        
        return new OkObjectResult(products);
    }
    catch (NotSuccessfulException nse)
    {
        if (nse.StatusCode == StatusCodes.Status400BadRequest)
        {
            return new BadRequestObjectResult(
                ProblemDetailsHelper.BadRequestProblemDetails(
                    nse.Content));
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
            DiagnosticConstants.ProductFilterInvalidFormat,
            DiagnosticConstants.IncompatibleProductFilter);        
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
            logging.AddLogDatum(
                DiagnosticConstants.DetailedException, 
                ife.DetailedMessage);
        }
        var problemDetails = ProblemDetailsHelper
            .BadRequestProblemDetails(ife.Message);
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
    catch (Exception ex) when (
        ex is FormatException || 
        ex is Newtonsoft.Json.JsonException)
    {
        var problemDetails = ProblemDetailsHelper.BadRequestProblemDetails(
            DiagnosticConstants.ProductFilterInvalidFormat);
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
public async Task<IProductCollection> ProductSearch(
    string filter, 
    string discontinuedFilter)
{
    if (string.IsNullOrEmpty(filter))
    {
        throw new ArgumentException("Filter cannot be null or empty");
    }

    string decodedFilter;
    try
    {
        decodedFilter = DecodeBase64(filter);
    }
    catch (FormatException)
    {
        throw new InvalidFilterException("Invalid filter format") 
        { 
            DetailedMessage = "Base64 encoding format is invalid" 
        };
    }
    
    var searchCriteria = JsonConvert.DeserializeObject<SearchCriteria>(
        decodedFilter);
    
    if (!IsSearchCriteriaValid(searchCriteria))
    {
        throw new InvalidFilterException(
            "Search criteria validation failed");
    }
    
    if (searchCriteria.UseAdvancedFeatures && 
        !_featureFlags.AdvancedSearchEnabled)
    {
        throw new UnsupportedFilterException(
            "Advanced search is not supported");
    }
    
    try
    {
        var result = await _repository.SearchProducts(
            searchCriteria, 
            discontinuedFilter == "true");
            
        if (result == null || !result.Any())
        {
            return new ProductCollection { 
                Items = new List<Product>(), 
                TotalCount = 0 
            };
        }
        
        return new ProductCollection { 
            Items = result, 
            TotalCount = result.Count 
        };
    }
    catch (TimeoutException)
    {
        throw new GatewayTimeoutException("Repository search timed out");
    }
    catch (NotSupportedException)
    {
        throw new NotImplementedException(
            "Search feature not implemented for this repository");
    }
}
```

## A Better Alternative: Result Object Pattern

Instead of using exceptions for control flow, a better approach is to use a result object that encapsulates the success
or failure outcome along with any relevant data. (See `Maybe<T>`) Here's how we can refactor the above example:

```C#
public class SearchResult<T>
{
    public bool IsSuccess { get; private set; }
    public T Data { get; private set; }
    public int StatusCode { get; private set; }
    public string ErrorMessage { get; private set; }
    public ProblemDetails ProblemDetails { get; private set; }

    public static SearchResult<T> Success(T data)
    {
        return new SearchResult<T>
        {
            IsSuccess = true,
            Data = data,
            StatusCode = StatusCodes.Status200OK
        };
    }

    public static SearchResult<T> Failure(
        int statusCode, 
        string errorMessage, 
        ProblemDetails problemDetails = null)
    {
        return new SearchResult<T>
        {
            IsSuccess = false,
            StatusCode = statusCode,
            ErrorMessage = errorMessage,
            ProblemDetails = problemDetails
        };
    }
}

public async Task<IActionResult> ProductSearch(
    [FromQuery(Name = "filter")] string base64EncodedFilter,
    [FromQuery(Name = "includeDiscontinued")] string discontinuedFilter)
{
    // Validate inputs
    if (string.IsNullOrEmpty(base64EncodedFilter))
    {
        var problem = ProblemDetailsHelper.BadRequestProblemDetails(
            DiagnosticConstants.ProductFilterInvalidFormat);
        return BadRequest(problem);
    }
    
    if (!AuthHelper.HasValidToken(Request?.Headers))
    {
        return Unauthorized();
    }

    // Perform the search operation
    var result = await _productAdapter.ProductSearch(
        base64EncodedFilter, 
        discontinuedFilter);
    
    // Handle the result based on success/failure
    if (result.IsSuccess)
    {
        return new OkObjectResult(result.Data);
    }
    
    // Handle different types of failures
    switch (result.StatusCode)
    {
        case StatusCodes.Status400BadRequest:
            return new BadRequestObjectResult(result.ProblemDetails);
            
        case StatusCodes.Status401Unauthorized:
            return Unauthorized();
            
        case StatusCodes.Status404NotFound:
            return NotFound();
            
        case StatusCodes.Status504GatewayTimeout:
            return new ObjectResult(result.ProblemDetails)
            {
                StatusCode = StatusCodes.Status504GatewayTimeout
            };
            
        case StatusCodes.Status501NotImplemented:
            return StatusCode(StatusCodes.Status501NotImplemented);
            
        default:
            return StatusCode(StatusCodes.Status500InternalServerError);
    }
}
```

And here's how the service method would be refactored:

```C#
public async Task<SearchResult<IProductCollection>> ProductSearch(
    string filter, 
    string discontinuedFilter)
{
    // Validate filter
    if (string.IsNullOrEmpty(filter))
    {
        var problemDetails = ProblemDetailsHelper.BadRequestProblemDetails(
            DiagnosticConstants.ProductFilterInvalidFormat);
            
        return SearchResult<IProductCollection>.Failure(
            StatusCodes.Status400BadRequest, 
            "Filter cannot be null or empty", 
            problemDetails);
    }

    // Decode base64 filter
    string decodedFilter;
    try
    {
        decodedFilter = DecodeBase64(filter);
    }
    catch (FormatException)
    {
        var problemDetails = ProblemDetailsHelper.BadRequestProblemDetails(
            DiagnosticConstants.ProductFilterInvalidFormat);
            
        return SearchResult<IProductCollection>.Failure(
            StatusCodes.Status400BadRequest, 
            "Invalid filter format", 
            problemDetails);
    }
    
    // Parse the search criteria
    SearchCriteria searchCriteria;
    try
    {
        searchCriteria = JsonConvert.DeserializeObject<SearchCriteria>(
            decodedFilter);
    }
    catch (JsonException)
    {
        var problemDetails = ProblemDetailsHelper.BadRequestProblemDetails(
            DiagnosticConstants.ProductFilterInvalidFormat);
            
        return SearchResult<IProductCollection>.Failure(
            StatusCodes.Status400BadRequest, 
            "Invalid search criteria JSON", 
            problemDetails);
    }
    
    // Validate search criteria
    if (!IsSearchCriteriaValid(searchCriteria))
    {
        var problemDetails = ProblemDetailsHelper.BadRequestProblemDetails(
            DiagnosticConstants.IncompatibleProductFilter);
            
        return SearchResult<IProductCollection>.Failure(
            StatusCodes.Status400BadRequest, 
            "Search criteria validation failed", 
            problemDetails);
    }
    
    // Check for unsupported features
    if (searchCriteria.UseAdvancedFeatures && 
        !_featureFlags.AdvancedSearchEnabled)
    {
        return SearchResult<IProductCollection>.Success(null);
    }
    
    try
    {
        // Perform the actual search
        var result = await _repository.SearchProducts(
            searchCriteria, 
            discontinuedFilter == "true");
        
        // Return search results
        if (result == null || !result.Any())
        {
            return SearchResult<IProductCollection>.Success(
                new ProductCollection { 
                    Items = new List<Product>(), 
                    TotalCount = 0 
                }
            );
        }
        
        return SearchResult<IProductCollection>.Success(
            new ProductCollection { 
                Items = result, 
                TotalCount = result.Count 
            }
        );
    }
    catch (TimeoutException)
    {
        var problemDetails = ProblemDetailsHelper.GatewayProblemDetails();
        
        return SearchResult<IProductCollection>.Failure(
            StatusCodes.Status504GatewayTimeout, 
            "Repository search timed out", 
            problemDetails);
    }
    catch (NotSupportedException)
    {
        return SearchResult<IProductCollection>.Failure(
            StatusCodes.Status501NotImplemented, 
            "Search feature not implemented for this repository");
    }
    catch (Exception ex)
    {
        // Log the unexpected exception
        _logger.LogError(ex, "Unexpected error during product search");
        
        return SearchResult<IProductCollection>.Failure(
            StatusCodes.Status500InternalServerError, 
            "An unexpected error occurred");
    }
}
```

## Benefits of the Result Object Pattern

1. **Explicit flow control**: The code clearly shows the possible outcomes and how they're handled.
2. **Better performance**: Avoids the overhead of creating and handling exceptions for expected conditions.
3. **Improved readability**: Makes it easier to understand the different possible outcomes and how they're handled.
4. **More maintainable**: Changes to result handling can be made in isolation without disrupting the try-catch
   structure.
5. **Better testability**: Easier to test different outcomes without having to simulate exceptions.

By using result objects instead of exceptions for control flow, we create code that is more intention-revealing,
performs better, and is easier to maintain and test.

## Throwing Exceptions Alternatives

### Returning Errors as Values

1. **Return a value**
   When appropriate, return a nullable type, an empty collection, or an empty string rather than throwing an exception.
2. **Use a `Result` type**
   Consider returning a specialized "either" type that indicates success or failure without throwing.

### Why Exceptions are Expensive

Exceptions come with significant performance costs due to several factors:

- **Call Stack Unwinding**: When an exception is thrown, the runtime must unwind the call stack, searching for an
  appropriate exception handler. This process involves walking back through potentially dozens or hundreds of stack
  frames.
- **Stack Trace Generation**: Creating the stack trace for an exception requires capturing the entire call stack, which
  is computationally expensive.
- **Memory Allocation**: Exceptions are objects that need to be allocated on the heap.
- **Just-In-Time Compilation**: Throwing exceptions can cause the JIT compiler to generate less optimized code in
  methods that might throw.
- **Thread Coordination**: In multi-threaded applications, exception handling may require additional thread
  synchronization mechanisms.

For these reasons, exceptions should be used for truly exceptional conditions rather than expected error cases that are
part of normal program flow.

### When to Throw an Exception

- **Pure methods**
  If possible, design methods to be "pure," meaning the same input always yields the same output with no side effects.
  This leads to more predictable code.

### Testing

- **Easier to test**
  Returning values or results can simplify tests, because you no longer need to handle or expect thrown exceptions.
- **Example**
  Instead of:

```C#
  Assert.Throws<NegativeNumberException>(() => 
      GetMilesPerGallon(1.0, 5.0));
```

you could return an error code or a result object, making the test straightforward:

```C#
  var result = GetMilesPerGallon(1.0, 5.0);
  Assert.False(result.IsSuccess);
  Assert.Equal(ErrorCode.NegativeValue, result.Error);
```

### When Are Exceptions Appropriate?

- **Ensuring a valid application object graph**
  During application startup or dependency injection, throwing may be necessary if a critical component is missing.
- **Constructor failures**
  If an object cannot be safely constructed in a valid state, throwing an exception is often justified.
- **Service injection**
  If a required service is not provided, throwing can signal the error early.

Exceptions should be reserved for truly exceptional circumstances that prevent the normal operation of your application,
not for handling expected error conditions that can be anticipated and managed through return values or result objects.

---
See Also:
- [Introduction to Maybe T](Introduction-to-Maybe-T.md) (Result object alternative)
