# Higher Order Function Examples

This article illustrates how to replace explicit loops with higher-order functions in a generic, functional programming
style using C#. The examples below demonstrate converting a simple for-loop into more expressive and reusable functions.

---

## Converting For Loops into a Higher-Order Function

Consider the following imperative approach to process an array of items:

```C#
var sum = 0;
for (var i = 0; i < items.Length; i++)
{
    Process(items[i]);
}
```

This loop explicitly controls the iteration over the array, updating mutable variables along the way. To simplify and
make the code more declarative, many languages offer the `foreach` statement:

```C#
foreach (var item in items)
{
    Process(item);
}
```

The `foreach` statement abstracts the iteration details (like index incrementation), making the code more concise and
easier to maintain.

We can further improve this by defining a higher-order extension method called `ForEach`, which encapsulates the
iteration logic:

```C#
public static class ArrayExtensions
{
    public static void ForEach<T>(this T[] array, Action<T> action)
    {
        foreach (var element in array)
        {
            action(element);
        }
    }
}
```

Here, `Action<T>` is a delegate representing a method that takes a single argument of type `T` and returns void. With
this extension method, we can now write:

```C#
items.ForEach(Process);
```

Or even chain multiple operations:

```C#
items.ForEach(item =>
{
    Process(item);
    Log(item);
});
```

The .NET framework also includes a similar `ForEach` method on `List<T>`, but defining it as an extension method for
arrays (or any `IEnumerable<T>`) offers greater flexibility.

---

## Returning Functions from Functions

In functional programming, functions are first-class objects, meaning they can be passed as arguments, returned as
values, and assigned to variables. In C#, delegates such as `Action` allow you to treat methods as first-class objects.

For example, consider a higher-order function that wraps another function with error logging:

```C#
public static Action WrapLogging(Action action)
{
    return () =>
    {
        try
        {
            action();
        }
        catch (Exception ex)
        {
            Console.WriteLine("Error occurred: " + ex.Message);
        }
    };
}
```

This `WrapLogging` function takes an `Action` as input and returns a new `Action` that executes the original action
within a try-catch block. You can use it as follows:

```C#
// Defer execution of Process with logging
var processWithLogging = WrapLogging(() => Process(someItem));
// When invoked, processWithLogging() will execute Process and log any exceptions.
processWithLogging();
```

Deferred execution here means that the wrapped function isn’t executed until you explicitly invoke the returned
delegate.

---

## Partial Application

Partial application is the process of fixing a few arguments of a function, producing another function with fewer
parameters. For example, we can create a function that always adds a fixed value to its input:

```C#
public static Func<int, int> MakeAdder(int fixedValue)
{
    return number => fixedValue + number;
}

var addFive = MakeAdder(5);
Console.WriteLine(addFive(3)); // Outputs 8
```

In this example, calling `MakeAdder(5)` returns a function that adds 5 to any number, effectively "partially applying"
the value 5.

---

## Summary

In this article, we explored several functional programming techniques in C#:

- **Higher-order functions:** Replace explicit loops with reusable methods like `ForEach`.
- **Returning functions:** Wrap functions with additional behavior (e.g., logging) by returning new delegates.
- **Partial application:** Create new functions by fixing some arguments of a function.

These approaches promote more declarative, concise, and maintainable code compared to traditional imperative loops,
reducing repetition and minimizing errors.

---
See Also:
- [Functional in the Small and Object-Oriented in the Large](Functional-in-the-Small-and-Object-Oriented-in-the-Large.md)
- [Imperative vs Functional (Declarative, Expressive) Style of Programming](Imperative-vs-Functional-Declarative-Expressive-Style-of-Programming.md)
- [Function composition and method chaining](Function-composition-and-method-chaining.md)
- [Partial Application Example](Partial-Application-Example.md)
- [Find What Is Varying and Encapsulate It (Shalloway 123)](Find-What-Is-Varying-and-Encapsulate-It-Shalloway-123.md) (mentions delegates)