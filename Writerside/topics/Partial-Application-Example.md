# Partial Application Example

In this article, we'll explore different approaches to loading
configuration files. We'll start with a basic method, introduce partial
application, and then refine the design to make it more streamlined.

## Initial Configuration Loading Method

Let's start with a simple method to load configuration files based on
the current environment:

```C#
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.Configuration;
using System.IO;

public static class WebApplicationBuilderExtensions
{
    public static WebApplicationBuilder LoadConfiguration(
        this WebApplicationBuilder builder)
    {
        var basePath = Directory.GetCurrentDirectory();
        var environment = builder.Environment.EnvironmentName;
        // Add specific configuration files
        AddJsonFile("appsettings", builder, basePath, environment);
        AddJsonFile("AppointmentScheduling", builder, basePath, environment);
        AddJsonFile("PatientManagement", builder, basePath, environment);
        return builder;
    }

    private static void AddJsonFile(string fileName,
        WebApplicationBuilder builder, string basePath, string env)
    {
        builder.Configuration.AddJsonFile(
            Path.Combine(basePath, "config", env,
                         $"{fileName}.{env}.json"),
            optional: false, reloadOnChange: true);
    }
}
```

## Partial Application

Partial application is a technique where a function is applied to some
of its arguments, producing another function that takes the remaining
arguments.

Example:

```C#
// Define a function that takes two arguments
Func<int, int, int> add = (a, b) => a + b;

// Partially apply the 'add' function with the first argument as 5
// Note: C# doesn't have direct placeholder syntax like `_`.
// We achieve partial application typically using a lambda closure.
Func<int, int> addFive = b => add(5, b);

// Call the new function with the remaining argument
int result = addFive(10); // result is 15
```

## Using Partial Application in LoadConfiguration

We can use partial application (via lambda closures in C#) to
streamline the addition of configuration files:

```C#
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.Configuration;
using System;
using System.IO;

public static class WebApplicationBuilderExtensions
{
    public static WebApplicationBuilder LoadConfiguration(
        this WebApplicationBuilder builder)
    {
        var basePath = Directory.GetCurrentDirectory();
        var environment = builder.Environment.EnvironmentName;

        // Create a delegate capturing common parameters
        var loadJsonFile = LoadJsonFile(builder, basePath, environment);

        // Call the delegate with only the varying parameter (fileName)
        loadJsonFile("appsettings");
        loadJsonFile("AppointmentScheduling");
        loadJsonFile("PatientManagement");

        return builder;
    }

    // Returns a function that takes fileName and loads the corresponding file
    private static Action<string> LoadJsonFile(
        WebApplicationBuilder builder, string basePath, string env)
    {
        // This lambda captures builder, basePath, and env from the outer scope
        return fileName => builder.Configuration.AddJsonFile(
            Path.Combine(basePath, "config", env,
                         $"{fileName}.{env}.json"),
            optional: false, reloadOnChange: true);
    }
}
```

In the `LoadConfiguration` method, we utilize partial application to
streamline the addition of configuration files. A delegate,
`loadJsonFile`, is created to capture the common parameters (builder,
basePath, environment). This delegate requires only the file name when
called, reducing parameter repetition and enhancing code readability.

## Further Improvements

To enhance readability and make updates easier, we can use an
`ImmutableHashSet<string>` for configuration file names:

```C#
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Immutable;
using System.IO;

public static class WebApplicationBuilderExtensions
{
    // Centralized list of configuration files
    private static readonly ImmutableHashSet<string> ConfigurationFiles =
    [
        "appsettings",
        "AppointmentScheduling",
        "PatientManagement",
    ];

    public static WebApplicationBuilder LoadConfiguration(
        this WebApplicationBuilder builder)
    {
        var basePath = Directory.GetCurrentDirectory();
        var environment = builder.Environment.EnvironmentName;

        // Create the partially applied function
        var loadJson = LoadJson(builder, basePath, environment);

        // Iterate and call the function for each file name
        foreach (var fileName in ConfigurationFiles)
        {
            loadJson(fileName);
        }
        return builder;
    }

    // The function factory remains the same
    private static Action<string> LoadJson(
        WebApplicationBuilder builder, string basePath, string environment)
    {
        return fileName => builder.Configuration.AddJsonFile(
            Path.Combine(basePath, "config", environment,
                         $"{fileName}.{environment}.json"),
            optional: false, reloadOnChange: true);
    }
}
```

The `WebApplicationBuilderExtensions` class now uses
`ImmutableHashSet<string>` for configuration file names. This design
centralizes configuration files at the file's top, simplifying
management and updates. Adding new files only requires updating the
`ConfigurationFiles` set.

## Simplified Configuration Loading Without Partial Application

Finally, we decided to remove the partial application as it was no longer
needed. Here is the streamlined version:

```C#
using Microsoft.AspNetCore.Builder;
using Microsoft.Extensions.Configuration;
using System.Collections.Immutable;
using System.IO;

public static class WebApplicationBuilderExtensions
{
    // Centralized list of configuration files
    private static readonly ImmutableHashSet<string> ConfigurationFiles =
    [
        "appsettings",
        "AppointmentScheduling",
        "PatientManagement",
    ];

    public static WebApplicationBuilder LoadConfiguration(
        this WebApplicationBuilder builder)
    {
        // Iterate through the defined configuration files
        foreach (var fileName in ConfigurationFiles)
        {
            // Call the simplified loading method directly
            LoadJson(builder, fileName);
        }
        return builder;
    }

    // Simplified method - no longer returns an Action
    private static void LoadJson(WebApplicationBuilder builder,
                                 string fileName)
    {
        var environment = builder.Environment.EnvironmentName;
        // Base path calculation moved inside
        builder.Configuration.AddJsonFile(
            Path.Combine(Directory.GetCurrentDirectory(), "config",
                         environment, $"{fileName}.{environment}.json"),
            optional: false, reloadOnChange: true);
    }
}
```

I decided to remove the partial application as it was no longer needed.
The `WebApplicationBuilderExtensions` class now inlines the base path
directly within the `LoadJson` method. Configuration files are iterated
through using an `ImmutableHashSet<string>`, and the `LoadJson` method
is called for each file, simplifying the code.

---
See Also:
- [Higher-Order Function Examples](Higher-Order-Function-Examples.md)
- [Function composition and method chaining](Function-composition-and-method-chaining.md)
- [Transitioning from Classes to Records and Adopting Immutable Collections in C#](Transitioning-from-Classes-to-Records-and-Adopting-IImmutableList-in-C.md)