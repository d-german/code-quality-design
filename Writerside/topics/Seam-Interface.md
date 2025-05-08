# Seam Interface

A Seam Interface is a design pattern in which an interface is created to isolate a complex dependency, to make it more
testable and easier to replace or mock in test scenarios. It allows us to isolate the complex/hard-to-test dependencies
and to control how they interact with the rest of the system.

The term "seam" (analogous to a clothing seam: a point where parts join and can be altered) comes from Michael Feathers'
book "Working Effectively with Legacy Code", where he introduces the
concept of "seamful design" as a way to design code that is more testable by creating these seams, or small, focused
interfaces, which allow you to test parts of the system in isolation.

```C#

public interface IMyDocumentDataService
{
    MyPageData GetData(
        IContext ctx,
        MyRendition rend,
        int index,
        GetDataProps props);
}

internal sealed class MyDocumentDataService
    : IMyDocumentDataService
{
    // Test wrapper for a complex dependency
    // that is already tested elsewhere.
    public MyPageData GetData(
        IContext ctx,
        MyRendition rend,
        int index,
        GetDataProps props)
    {
        return new RealDocDataProvider(ctx as RealContext)
            .FetchData(rend, index, props);
    }
}

// A class consuming the service
public class DocumentProcessor
{
    private readonly IMyDocumentDataService _dataService;

    public DocumentProcessor(IMyDocumentDataService dataService)
    {
        _dataService = dataService;
    }

    public string GetProcessedDocumentTitle(IContext ctx, MyRendition rend, int index)
    {       
        //...
        MyPageData pageData = _dataService.GetData(ctx, rend, index, props);       
        return $"Title: {pageData?.Title ?? "N/A"}";
    }
}
```

The `RealDocDataProvider` class (used internally by `MyDocumentDataService`) is a dependency that is often difficult or
even impossible to mock in a test
environment. By wrapping it in an interface, `IMyDocumentDataService`, and providing a concrete implementation in the
form of `MyDocumentDataService`, client classes like `DocumentProcessor` can be tested in isolation. For testing
`DocumentProcessor`, a mock implementation
of `IMyDocumentDataService` can be injected, allowing tests to run without relying
on `RealDocDataProvider` and its complex dependencies.

---
See Also:

- [Dependency Injection (DI)](Dependency-Injection-DI.md)
- [Inversion of Control (IoC)](Inversion-of-Control-IoC.md)
- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md)
- [Stable vs Volatile Dependencies](Stable-vs-Volatile-Dependencies.md) (Seams help manage volatile dependencies)