# Evolving Configuration Interfaces: From Boolean Flags to Expressive Design

This article presents an evolution in the design of an interface for managing application configuration in a backend service.
It demonstrates how initial designs can be improved upon for clarity, maintainability, and adherence to best practices.

> *Never shoot for the best design, but rather the least worst design.*  
> *(Mark Richards and Neal Ford)*

---

## Original Interface Design

The initial design featured an interface, `IConfigurationService`, which provided methods to load and write a
`Configuration` object. The `LoadAsync` method would automatically create a configuration file if it didn't exist:

```C#
public interface IConfigurationService
{
    Task<Configuration> LoadAsync();
    Task<bool> WriteAsync(Configuration config);
}
```

This initial design, while functional, could be argued to violate the **Interface Segregation Principle** of SOLID, as
it combines loading and writing responsibilities. However, splitting this into two distinct interfaces might lead to
overengineering. It's essential to balance the application of SOLID principles with practical design considerations. It
may not be the best design, but it very well could be the ‘least worst’.

---

## Design 1: Introduction of a Boolean Parameter

To offer conditional file creation, the first redesign introduced a boolean parameter in the `LoadAsync` method. If set
to `true`, it would create the configuration file if it was missing:

```C#
public interface IConfigurationService
{
    Task<Configuration> LoadAsync(bool createIfNotExists = true);
    Task<bool> WriteAsync(Configuration config);
}
```

However, this design had issues. Using a primitive type like a boolean in public interfaces was not ideal. Also, the
boolean parameter created confusion due to its negative phrasing and the risk of incorrectly passing `true` or `false`
values. Another issue was that the boolean parameter leaked implementation details. Its presence in the interface
suggested a behavior change based on its value, which ideally should be encapsulated rather than exposed.

---

## Design 2: Interface Redesign for Clarity

A second redesign attempted to address these issues:

```C#
public interface IConfigurationService
{    
    bool FileExists();
    Task<Configuration> ReadAndParseConfigurationAsync();
    Task<Configuration> LoadAsync();
    Task<bool> WriteAsync(Configuration config);    
}
```

Although the second redesign provided valuable conceptual insights, it exposed a significant drawback: the interface
inadvertently disclosed implementation specifics, thus violating the principle of abstraction. An example was the
`FileExists` method, which overly anchored the interface to a file-based configuration approach. We should recognize
that this is not the best design, and it's clear that it's not the 'least worst' either.

---

## Final Interface Design: Expressive and Flexible

The final design improves expressiveness and flexibility. It offers two methods for loading configurations: one that
simply loads the configuration and another that creates a new configuration if none exists:

```C#
public interface IConfigurationService
{
    Task<Configuration> LoadAppConfigAsync();
    Task<Configuration> LoadOrCreateAppConfigAsync();
    Task<bool> WriteAsync(Configuration config);
}
```

This design is more intuitive and aligns with common .NET patterns (`GetOrCreate` in
`Microsoft.Extensions.Caching.Memory.IMemoryCache` and `AddOrGetExisting` in `System.Runtime.Caching.MemoryCache`),
making it easier to use correctly and harder to misuse. It also adheres to the principle of abstraction, allowing for
future changes in the implementation details without affecting the interface.

In conclusion, this evolution in interface design highlights the importance of clarity, maintainability, and adherence
to software design principles in developing robust and flexible software systems.

---

### Contrived Example of Confusing Boolean Values

**Which of the following statements is not untrue about World War II?**

A) The war didn’t start in 1939.  
B) The United States didn’t join the war after the attack on Pearl Harbor.  
C) Adolf Hitler wasn’t the leader of Nazi Germany during the war.  
D) The atomic bombs weren’t dropped on Hiroshima and Nagasaki to end the war. *(Correct Answer)*

---
See Also:
- [Interface Segregation Principle (ISP)](Interface-Segregation-Principle-ISP.md)
- [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md)
- [Pillars of OOP Encapsulation Abstraction](Pillars-of-OOP-Encapsulation-Abstraction.md)
- [Code Review Checklist](Code-Review-Checklist.md) (mentions splitting boolean param functions)
- [SOLID Design Principles](SOLID-Design-Principles.md)