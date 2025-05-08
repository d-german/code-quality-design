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
    // This service acts as a seam, wrapping the RealDocDataProvider.
    // RealDocDataProvider might be a legacy class without an interface.
    public MyPageData GetData(
        IContext ctx,
        MyRendition rend,
        int index,
        GetDataProps props)
    {
        // RealDocDataProvider is the concrete, hard-to-test dependency.
        return new RealDocDataProvider(ctx as RealContext)
            .FetchData(rend, index, props);
    }
}

// A class consuming the service via constructor injection
public class DocumentProcessor
{
    private readonly IMyDocumentDataService _dataService;

    public DocumentProcessor(IMyDocumentDataService dataService)
    {
        _dataService = dataService;
    }

    public string GetProcessedDocumentTitle(IContext ctx, MyRendition rend, int index)
    {
        // Props can be created or passed in
        var props = new GetDataProps { /* ... initialize properties ... */ };
        MyPageData pageData = _dataService.GetData(ctx, rend, index, props);
        // Example processing
        return $"Title: {pageData?.Title ?? "N/A"}";
    }
}
```

The `RealDocDataProvider` class (used internally by `MyDocumentDataService`) often represents a concrete legacy
component or a third-party library that does not implement a suitable interface itself, making it difficult or even
impossible to mock directly in a test environment. The `IMyDocumentDataService` interface is created as a seam
specifically to wrap this `RealDocDataProvider`. By providing this interface and a concrete implementation (
`MyDocumentDataService`) that uses the problematic class, client classes like `DocumentProcessor` can be tested in
isolation. For testing `DocumentProcessor`, a mock implementation of `IMyDocumentDataService` can be injected, allowing
tests to run without relying on `RealDocDataProvider` and its complex dependencies. Ideally, `RealDocDataProvider` might
have had its own interface, but the Seam Interface pattern addresses scenarios where this is not the case.

### Test Example (NUnit & Moq)

The following demonstrates how `DocumentProcessor` can be tested by providing a mock implementation of
`IMyDocumentDataService`:

```C#
using NUnit.Framework;
using Moq;
// Assuming other necessary using statements for IContext, MyRendition, MyPageData etc.

[TestFixture]
public class DocumentProcessorTests
{
    [Test]
    public void GetProcessedDocumentTitle_WithValidData_ReturnsFormattedTitle()
    {       
        var mockDataService = new Mock<IMyDocumentDataService>();
        var fakePageData = new MyPageData { Title = "Test Document" };

        // Setup the mock to return fakePageData when GetData is called with any arguments
        mockDataService.Setup(s => s.GetData(
            It.IsAny<IContext>(),
            It.IsAny<MyRendition>(),
            It.IsAny<int>(),
            It.IsAny<GetDataProps>()))
            .Returns(fakePageData);

        IContext nullContext = null;
        MyRendition nullRendition = null;

        var processor = new DocumentProcessor(mockDataService.Object);
       
        string result = processor.GetProcessedDocumentTitle(nullContext, nullRendition, 0);
        
        Assert.AreEqual("Title: Test Document", result);
        mockDataService.Verify(s => s.GetData(nullContext, nullRendition, 0, It.IsAny<GetDataProps>()), Times.Once());
    }
}
```

---
See Also:

- [Dependency Injection (DI)](Dependency-Injection-DI.md)
- [Inversion of Control (IoC)](Inversion-of-Control-IoC.md)
- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md)
- [Stable vs Volatile Dependencies](Stable-vs-Volatile-Dependencies.md) (Seams help manage volatile dependencies)