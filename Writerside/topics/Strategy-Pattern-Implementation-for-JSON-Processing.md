# Strategy Pattern Implementation for JSON Processing

The Strategy pattern provides a way to encapsulate a family of algorithms and make them interchangeable. In this
example, we refactored a rigid JSON converter into a more flexible strategy-based solution for processing different
types of JSON data.

## Before: Hard-Coded JSON Conversion

Previously, our `UserPreferencesJsonConverter` class contained hard-coded logic with explicit checks for specific JSON
keys:

```C#
public class UserPreferencesJsonConverter : JsonConverter<IContextItem>
{
    private const string Categories = "Categories";
    private const string Thumbnail = "Thumbnail";
    
    public override IContextItem ReadJson(JsonReader reader, 
        Type objectType, IContextItem existingValue, 
        bool hasExistingValue, JsonSerializer serializer)
    {
        // Hard-coded key-checking logic
        if (obj.ContainsKey(Categories))
        {
            return obj.ToObject<PersonalCategoriesContext>(newSerializer);
        }
        if (obj.ContainsKey(Thumbnail))
        {
            return obj.ToObject<ThumbnailContext>(newSerializer);
        }
        throw new JsonSerializationException("Cannot deserialize the input JSON object to a known context type.");
    }
}
```

This approach violates the Open Closed Principle (OPC) because the class must be modified every time a new JSON format
needs to be supported. Adding a new format requires changing the existing code, increasing the risk of regression bugs.

## After: Strategy Pattern Implementation

### 1. Define the Strategy Interface

```C#
public interface IJsonProcessor
{
    bool CanProcess(JObject json);
    object? Process(JObject json);
}
```

### 2. Implement Concrete Strategies

```C#
public class PersonalCategoriesJsonProcessor : IJsonProcessor
{
    private const string Categories = "Categories";
    
    public bool CanProcess(JObject json)
    {
        return json.ContainsKey(Categories);
    }
    
    public object? Process(JObject json)
    {
        return json.ToObject<PersonalCategoriesContext>();
    }
}

public class ThumbnailJsonProcessor : IJsonProcessor
{
    private const string Thumbnail = "Thumbnail";
    
    public bool CanProcess(JObject json)
    {
        return json.ContainsKey(Thumbnail);
    }
    
    public object? Process(JObject json)
    {
        return json.ToObject<ThumbnailContext>();
    }
}
```

### 3. Context Class Using the Strategies

```C#
public class UserPreferencesService : IUserPreferencesService
{
    private readonly ILogger<UserPreferencesService> _logger;
    private readonly IEnumerable<IJsonProcessor> _processors;
    
    public UserPreferencesService(
        ILogger<UserPreferencesService> logger, 
        IEnumerable<IJsonProcessor> processors)
    {
        _processors = Guard.VerifyArgumentNotNull(processors, nameof(processors));
        _logger = Guard.VerifyArgumentNotNull(logger, nameof(logger));
    }
    
    public object? ProcessJsonString(string? jsonString)
    {
        if (jsonString == null)
        {
            _logger.LogError("The provided JSON string is null");
            return null;
        }
        var json = JObject.Parse(jsonString);
        var handler = _processors.FirstOrDefault(h => h.CanProcess(json));
        if (handler != null)
        {
            return handler.Process(json);
        }
        _logger.LogError("No handler found for the JSON: {Json}", jsonString);
        return null;
    }
}
```

### 4. Register Strategies with Dependency Injection

```C#
services.AddSingleton<IJsonProcessor, PersonalCategoriesJsonProcessor>();
services.AddSingleton<IJsonProcessor, ThumbnailJsonProcessor>();
```

This registration pattern leverages a key behavior of most DI containers: when multiple implementations of the same
interface are registered, injecting `IEnumerable<IJsonProcessor>` will provide all registered implementations in the
order they were defined.
This is crucial for our strategy pattern implementation as it allows the `UserPreferencesService` to automatically
receive all JSON processors without modification. If we had injected just `IJsonProcessor` (without the `IEnumerable`),
only the last registered implementation would be available, defeating the purpose of our flexible processor system.
When adding a new JSON processor, simply register it in the DI container and it becomes immediately available to all
consumers of `IEnumerable<IJsonProcessor>` without any code changes in those consumers - a perfect demonstration of the
Open/Closed Principle in action.

This strategy pattern implementation adheres to the Open/Closed Principle by allowing the system to be extended with new
JSON processors without modifying existing code. Adding support for a new JSON format is simply a matter of:

1. Creating a new class that implements `IJsonProcessor`
2. Registering it with the dependency injection container

The core processing logic in `UserPreferencesService` remains unchanged, reducing the risk of introducing bugs while
enhancing the system's capability.
