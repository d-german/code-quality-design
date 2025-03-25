# Seam Interface

A Seam Interface is a design pattern in which an interface is created to isolate a complex dependency, to make it more
testable and easier to replace or mock in test scenarios. It allows us to isolate the complex/hard-to-test dependencies
and to control how they interact with the rest of the system.

The term "seam" comes from Michael Feathers' book "Working Effectively with Legacy Code", where he introduces the
concept of "seamful design" as a way to design code that is more testable by creating seams, or small, focused
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

```

The `RealDocDataProvider` class is a dependency that is often difficult or even impossible to mock in a test
environment. By wrapping it in an interface, `IMyDocumentDataService`, and providing a concrete implementation in the
form of `MyDocumentDataService`, you can test the `GetData()` method in isolation by substituting a mock implementation
of the interface. This approach allows you to test the classes that depend on the document data service without relying
on the real implementation and its complex dependencies.

See Dependency Injection, Inversion of Control (IoC), and Dependency Inversion Principle (DIP).


