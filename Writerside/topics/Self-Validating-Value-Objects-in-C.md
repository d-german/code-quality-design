# Self-Validating Value Objects in C#

A Self-Validating Value Object is an immutable object that encapsulates validation, ensuring it always represents a
valid state within its domain context. It is characterized by its value-type semantics, meaning its equality is
determined based on the value of its attributes rather than by an object identity.

In this article, we discuss how to implement a self-validating value object using a read-only record in C# that includes
built-in validation logic.

---

## Example Use Case: Query Validation

Consider a service that performs a search based on a query string. In a traditional design, the service method might
validate the string parameter before proceeding:

```C#
public class SearchService
{
    public static int Search(string query)
    {
        if (string.IsNullOrWhiteSpace(query))
        {
            throw new ArgumentException(
            "Query cannot be null or empty", nameof(query));
        }
        if (query.Length < 3)
        {
            throw new ArgumentException(
            "Query must be at least 3 characters long", nameof(query));
        }
        return PerformSearch(query);
    }
    private static int PerformSearch(string query) => 5;
}
```

Usage:

```C#
[Test]
public void SearchWithQueryReturnsExpectedResult()
{
    var searchResultCount = SearchService.Search("ExtensiveQueryExample");
    Assert.That(searchResultCount, Is.EqualTo(5));
}
```

The problem with this design is that every time a string is passed in as a query, the validation must be repeated.
Instead, a better design is to use a self-validating value object for the query.

---

## Refactoring to a Self-Validating Value Object

### Using a Read-Only Record

We can define a read-only record that encapsulates the validation logic for a query string:

```C#
public record SearchQuery
{
    private readonly string _query = null!;
    public required string Query
    {
        get => _query;
        init
        {
            if (string.IsNullOrWhiteSpace(value))
            {
                throw new ArgumentException(
                "Query cannot be null or whitespace.", nameof(Query));
            }
            if (value.Length < 3)
            {
                throw new ArgumentException(
                "Query must be at least 3 characters long.", nameof(Query));
            }
            _query = value;
        }
    }
}
```

Notice the use of the `required` keyword and the validation logic in the `init` accessor. This ensures that a
`SearchQuery` object is always created in a valid state.

### Refactored Service Method

With the new value object, the service method can be refactored to accept a `SearchQuery` parameter instead of a plain
string:

```C#
public class SearchService
{
    public static int Search(SearchQuery query)
    {
        return PerformSearch(query.Query);
    }
    private static int PerformSearch(string query) => 5;
}
```

Usage:

```C#
[Test]
public void SearchWithQueryObjectReturnsExpectedResult()
{
    var searchQuery = new SearchQuery { Query = "ExtensiveQueryExample" };
    var searchResultCount = SearchService.Search(searchQuery);
    Assert.That(searchResultCount, Is.EqualTo(5));
}

[Test]
public void CreatingSearchQueryWithShortStringThrowsException()
{
    const string searchQuery = "su";
    Assert.Throws<ArgumentException>(() => 
    _ = new SearchQuery { Query = searchQuery });
}
```

---

Wrapping values inside new objects called value objects isn't just useful for avoiding repeated validation logic. As
soon as you notice that a method accepts a primitive-type value (`string`, `int`, etc.), you should consider introducing a
class for it. The guiding question for deciding whether to do this is, "Would any `string`, `int`, etc., be
acceptable here?" If the answer is no, introduce a new class for the concept.

You should consider the value object class itself to be a type, just like `string`, `int`, etc., are types. By introducing
more objects to represent domain concepts, you're effectively extending the type system. Your language's compiler or
runtime will be able to support you much better, because it can do type-checking for you and make sure that only the
right types end up being used when passing method arguments and returning values.

Noback (pp. 78-79)

## Enhancing Usability with Implicit Conversions

To simplify usage further, we can add an implicit conversion from `string` to `SearchQuery`. This allows us to pass a
string directly to methods expecting a `SearchQuery`, while still enforcing validation:

```C#
public record SearchQuery
{
    private readonly string _query = null!;
    public required string Query
    {
        get => _query;
        init
        {
            if (string.IsNullOrWhiteSpace(value))
            {
                throw new ArgumentException(
                "Query cannot be null or whitespace.", nameof(Query));
            }
            if (value.Length < 3)
            {
                throw new ArgumentException(
                "Query must be at least 3 characters long.", nameof(Query));
            }
            _query = value;
        }
    }

    // Implicit conversion from string to SearchQuery
    public static implicit operator SearchQuery(string query) => 
    new() { Query = query };
}
```

Example usage with implicit conversion:

```C#
[Test]
public void SearchWithImplicitStringConversionReturnsExpectedResult()
{
    const string searchQuery = "ExtensiveQueryExample";
    var searchResultCount = SearchService.Search(searchQuery);
    Assert.That(searchResultCount, Is.EqualTo(5));
}

[Test]
public void SearchWithShortStringQueryThrowsException()
{
    const string searchQuery = "su";
    Assert.Throws<ArgumentException>(() => 
    _ = SearchService.Search(searchQuery));
}
```

---

## Conclusion

Implementing self-validating value objects, such as `SearchQuery`, not only streamlines
validation logic but also enriches the application's type system. By encapsulating domain-specific validation within
these objects, you ensure that only valid data is processed, leading to code that is more expressive, easier to
understand, and less prone to errors. This approach extends the type system, allowing the compiler and runtime to
enforce domain rules and making the application more robust and maintainable.
