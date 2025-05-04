# Template Method

The Strategy pattern provides a way to encapsulate a family of algorithms (JSON processing logic in this case) and make
them interchangeable. The Template Method pattern defines the skeleton of an algorithm in a base class, deferring some
steps to subclasses. In this example, we refactored a JSON processing system using both patterns for improved
flexibility and reduced boilerplate code.

## The Problem: Rigidity and Boilerplate

Initially, the JSON processing logic might have been implemented in one of two ways, both with drawbacks:

1. **Hard-Coded Logic:** A single class or method contains large `if/else if` or `switch` statements to check JSON
   structures and deserialize accordingly. This violates the Open/Closed Principle (OCP), as modifying the class is
   required to support new JSON formats.
2. **Simple Strategy Implementation:** Using the Strategy pattern with a simple interface like `IJsonProcessor`, each
   concrete strategy implements both the check (`CanProcess`) and the action (`Process`). This often leads to
   boilerplate code within the `Process` method of each strategy, typically checking `if (!CanProcess(json))` before
   proceeding.

```C#
// Example: Boilerplate in a simple Strategy implementation
public class SomeJsonProcessor : IJsonProcessor
{
    public bool CanProcess(JObject json) { /* check logic */ }

    public object? Process(JObject json)
    {
        // --- BOILERPLATE START ---
        if (!CanProcess(json)) return null;
        // --- BOILERPLATE END ---

        // Actual processing logic
        return json.ToObject<SomeModel>();
    }
}
```

This repetition violates the Don't Repeat Yourself (DRY) principle.

## The Solution: Combining Strategy and Template Method

We combine the flexibility of Strategy with the boilerplate reduction of the Template Method pattern.

### 1. Define the Strategy Interface

The core interface defines the contract for any JSON processor, allowing the context class to work with any processor
uniformly.

```C#
public interface IJsonProcessor
{
    bool CanProcess(JObject json);
    object? Process(JObject json); // Returns the processed object or null
}
```

### 2. Implement the Template Method Base Class

An abstract base class implements `IJsonProcessor` and uses the Template Method pattern. It defines the overall
processing algorithm skeleton, handling the common `CanProcess` check.

```C#
// Implements the strategy interface
public abstract class BaseJsonProcessor<T> : IJsonProcessor
{
    // The "Template Method" - Implements IJsonProcessor.Process
    public object? Process(JObject json)
    {
        // Check if processable; if so, delegate to subclass, else return null.
        return !CanProcess(json) ? null : ProcessInternal(json);
    }

    // Abstract steps (primitives) that concrete strategies must implement
    public abstract bool CanProcess(JObject json);
    protected abstract T? ProcessInternal(JObject json); // Actual processing logic
}
```

The `Process` method acts as the template method. Subclasses only need to provide the varying parts: how to check if
the JSON is processable (`CanProcess`) and how to actually process it (`ProcessInternal`).

### 3. Implement Concrete Strategies (Subclasses)

Concrete strategies now inherit from `BaseJsonProcessor<T>` and provide specific implementations for the abstract
methods, free from the repetitive check.

```C#
public class UserJsonProcessor : BaseJsonProcessor<User>
{
    public override bool CanProcess(JObject json)
    {
        return json.ContainsKey(nameof(User.Name)) 
            && json.ContainsKey(nameof(User.Email));
    }

    // Core logic for processing a User
    protected override User? ProcessInternal(JObject json)
    {
        var user = json.ToObject<User>();
        // Example modification
        return user! with { Name = "John Doe 2" };
    }
}
```

```C#
public class ProductJsonProcessor : BaseJsonProcessor<Product>
{
    public override bool CanProcess(JObject json)
    {
        return json.ContainsKey(nameof(Product.ProductName)) 
            && json.ContainsKey(nameof(Product.Price));
    }

    // Core logic for processing a Product
    protected override Product? ProcessInternal(JObject json)
    {
        return json.ToObject<Product>();
    }
}
```

### 4. Context Class Using the Strategies

The context class (e.g., `JsonController`) depends on the `IEnumerable<IJsonProcessor>` abstraction. It iterates
through the available processors (strategies) and uses the first one that successfully processes the JSON (returns a
non-null result). It doesn't need to know about the `BaseJsonProcessor` or the template method.

```C#
[ApiController]
[Route("[controller]")]
public class JsonController : ControllerBase
{
    private readonly IEnumerable<IJsonProcessor> _processors;

    // Injects all registered IJsonProcessor implementations
    public JsonController(IEnumerable<IJsonProcessor> processors)
    {
        _processors = processors;
    }

    // ... (Post method omitted for brevity) ...

    private object? ProcessJsonString(string jsonString)
    {
        if (string.IsNullOrEmpty(jsonString)) return null; // Basic validation

        try
        {
            var json = JObject.Parse(jsonString);

            // Iterate through processors, call Process (the template method),
            // and take the first non-null result.
            var result = _processors
                .Select(p => p.Process(json))
                .FirstOrDefault(res => res != null);

            return result;
        }
        catch (Newtonsoft.Json.JsonReaderException ex)
        {
            // Handle invalid JSON input
            // Log the error: _logger.LogError(ex, "Invalid JSON received");
            return null;
        }
    }
}
```

### 5. Register Strategies with Dependency Injection

Register the *concrete* processor types against the `IJsonProcessor` interface in the DI container. The container
provides all registered implementations when `IEnumerable<IJsonProcessor>` is requested.

```C#
// Register concrete strategies against the common interface
services.AddSingleton<IJsonProcessor, UserJsonProcessor>();
services.AddSingleton<IJsonProcessor, ProductJsonProcessor>();
services.AddSingleton<IJsonProcessor, TodoItemProcessor>();
// Add new processors here—no changes needed in JsonController
```

## Benefits

* **Open/Closed Principle (OCP):** The system is open for extension (add new `BaseJsonProcessor<T>` subclasses and
  register them) but closed for modification (the `JsonController` and `BaseJsonProcessor` logic remain unchanged).
* **Single Responsibility Principle (SRP):** Each concrete processor focuses solely on handling its specific JSON
  structure. The base class handles the common workflow.
* **Don't Repeat Yourself (DRY):** The Template Method in `BaseJsonProcessor` eliminates the repetitive `CanProcess`
  check from concrete strategies.
* **Flexibility & Maintainability:** Easily add, remove, or modify JSON processing strategies without impacting
  unrelated code. Concrete strategies are simpler and easier to understand.

---

See Also:

* [Open Closed Principle (OCP)](Open-Closed-Principle-OCP.md)
* [Dependency Injection (DI)](Dependency-Injection-DI.md)
* [Polymorphism](Polymorphism.md)
* [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md)
* [Explicit case analysis (switch/if-else statement)](Explicit-case-analysis-switch-if-else-statement.md)
* [Find What Is Varying and Encapsulate It (Shalloway 123)](Find-What-Is-Varying-and-Encapsulate-It-Shalloway-123.md)
