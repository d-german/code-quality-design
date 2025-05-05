# Functional in the Small and Object-Oriented in the Large

This article is motivated by Eric Normand's book _Grokking Simplicity: Taming complex software with functional thinking_
. (Normand 2021) Even though I’m an object-oriented developer, I understand the benefits of functional programming and
want to use it in some parts of my code. **This is called "functional in the small and OO in the large". (Buonanno 2017)
This is a design philosophy that suggests using functional programming techniques within the methods of object-oriented
objects, but still using an overall object-oriented design for the larger structure of the program.** This article is a
simple introduction to functional programming and how it's used in our C# code.

---

## Side Effects

Side effects refer to any action a function performs apart from returning a value. This can include activities like
sending an email, launching a rocket, or altering global state. These can create issues because the side effects occur
each time the function is executed. This can result in unintended consequences if the purpose of the function is only to
return a value and not to produce side effects.

In contrast, a **pure function** is a function that always produces the same output for the same input and has no side
effects. Pure functions rely only on their input parameters and don't modify any external state.

Functional programming is often reduced to the idea of *"No side effects!"*, but this is an oversimplification.
Functional programming is not just about avoiding side effects, but rather controlling and managing them.

<note>
The management of state is a key difference between functional programming and object-oriented programming. Class
instance variables can be considered "global" to the class, and the methods of the class can change their values. (TBD)
This is a frequent practice in object-oriented programming. In functional programming, altering the state of global
variables is discouraged. This is why I recommended making class methods static, when possible, to express that they do
not alter any "global" variables.

<code-block lang="c#">
    public class BankAccount
{
    private decimal _balance; // "global" variable

    public void Deposit(decimal amount)
    {
        _balance += amount; // side effect
    }

    public void Withdraw(decimal amount)
    {
        _balance -= amount; // side effect
    }

    // Static - no access to instance variables
    public static decimal CalculateInterest(decimal principal, decimal interest)
    {
        return principal * interest / 100; // No side effects
    }

}
</code-block>
</note>

## Actions, Calculations, and Data

All code can be divided into **actions**, **calculations**, and **data**.

### Actions

- Also known as impure functions or functions that cause _side effects_.
- Actions depend on **when** they are called or **how many times** they are called.
- We must be extra careful when using them.

**Examples:**  
`SendEmail(to, from, subject, body)`, `SaveUserDb(user)`, `GetCurrentTime()`

Functional programmers try to avoid unnecessary side effects. They spend a lot of effort refactoring **Actions** into
**Calculations**.

### Calculations

- Also known as pure functions. They do **not** cause side effects.
- **Examples:** `Sum(numbers)`, `StringLength(name)`

It does not matter when you call `Sum` or `StringLength`; it will give you the correct answer any time, and it doesn't
matter how many times you call it. It won't influence the rest of the program or the outside world.

Calculations are *opaque*, meaning that the calculation is a black box, and the details of how it operates are hidden
from the caller. The calculation is defined only by its inputs and outputs, and its internal logic and implementation
are abstracted away.

With pure functions, you can be confident that the same inputs will always produce the same outputs, without having to
worry about potential side effects. This makes it easier to test and debug the code, as well as reason about its
behavior.

Making the distinction between calculations and actions is similar to the **Command Query Separation (CQS)** principle,
which distinguishes between commands (change state) and queries (return information).

### Data

Data is recorded facts about things. Data provides the input for calculations and actions and is used to produce results
and drive the behavior of the code.

**Examples:**  
`[1, 10, 2, 45, 3, 98]`, `{FirstName: John, LastName: Williams}`, DTOs

It can include values such as numbers, text, dates, or more complex structures like arrays, dictionaries, or objects.

Functional programmers often prefer to use *immutable* data:

- **Immutable data** means that once it's created, its value can't change.
- This makes it more predictable and easier to use because you don't have to worry about unexpected changes.

**Techniques**:

- **Copy-on-write**: create a new copy of the data before modifying it, so the original remains unchanged.
- **Defensive copying**: make a copy of data before sharing it externally, sending the copy to clients while preserving
  our original data intact.

Calculations and Data are much easier to work with than Actions. Generally, functional programmers prefer data over
calculations, and calculations over actions.

Functional programming emphasizes data and calculations, but actions are often necessary to interact with the external
world (e.g., reading files, sending network requests, updating a database). Functional programmers try to organize these
actions to reduce their impact and make them easier to manage.

---

## Examples of Immutability in C#

**With Expression**: Allows non-destructive mutation by creating a copy and modifying properties.

```C#
var originalPoint = new Point { X = 1, Y = 2 };  // (1,2)
var modifiedPoint = originalPoint with { X = 3 }; // (3,2)
```

**.NET `ImmutableArray<T>`** uses defensive copying.

```C#
var originalArray = ImmutableArray.Create(1, 2, 3); // [1, 2, 3]
var modifiedArray = originalArray.Add(4);           // [1, 2, 3, 4]
```

---

## Distinguishing Action vs. Calculation

#### Example: buildErrorResponse

```C#
private WeatherForecast BuildErrorResponse(string errorMessage)
{
    _logger.LogError("Weather Service Error: {ErrorMessage}", errorMessage);
    return new WeatherForecast { IsError = true, ErrorMessage = errorMessage };
}
````

This method is an **Action** because `_logger.LogError()` causes a side effect. A functional programmer would move the
logging out of this method:

```C#
private WeatherForecast BuildErrorResponse(string errorMessage)
{
    return new WeatherForecast { IsError = true, ErrorMessage = errorMessage };
}
````

Now it looks like a **Calculation**, but if `WeatherForecast` is mutable, then it’s still an Action! Mutable data
can cause implicit side effects.

#### Example: Immutable Data

```C#
public record WeatherForecast
{
    public bool IsError { get; init; }
    public string? ErrorMessage { get; init; }
    public IImmutableList<WeatherData> Forecasts { get; init; }
        = ImmutableArray<WeatherData>.Empty;
}
````

#### Example: Temporary Local Modifications

```C#
ImmutableArray<int> Add(ImmutableArray<int> numbers, int number)
{
    var temp = new int[numbers.Length];
    for (var i = 0; i < numbers.Length; i++)
    {
        temp[i] = numbers[i] + number;
    }
    return ImmutableArray.Create(temp);
}
````

This is still a **Calculation** because the final data structure (`ImmutableArray`) is immutable, even though local
variables are mutated during the creation process.

---

## First-Class Objects

A **first-class object** can be passed as an argument, assigned to a variable, or returned from a function. In C#,
numbers, strings, booleans, and arrays are first-class. C# also supports first-class functions via delegates, which can
be stored in variables, passed around, etc.

Operators like `+` or `*`, and keywords like `if` or `for`, are **not** first-class. A key concept in functional
programming is to recognize when something isn’t first-class and make it first-class.

```C#
Func<double, double, double> multiply = (x, y) => x * y;
Assert.That(multiply(2, 3), Is.EqualTo(6));
```

`Func<T1, T2, TResult>` is a delegate representing a method that takes two inputs and returns a result. This approach
makes the operation a first-class entity.

---

## Higher-Order Functions

**Higher-order functions** take one or more functions as arguments and/or return a function as their result.

#### Converting if/else into a Higher-Order Function

```C#
// Example of a higher-order function
T If<T>(bool condition, Func<T> trueFunc, Func<T> falseFunc)
{
    if (condition)
        return trueFunc();
    else
        return falseFunc();
}

var result = If(true, () => 1, () => 2);
Assert.That(result, Is.EqualTo(1));
````

---

## Functional Tools for Collections: Map(), Filter(), Reduce()

Many functional languages have powerful functions for collections. Let’s derive three common ones:

1. **Map**
2. **Filter**
3. **Reduce**

They replace loops as the workhorse of the functional programmer.

### Deriving Map()

Let's say we have a collection of integers, and we want to create two new collections based on it:

A new collection of integers with each item increased by 2.

A new collection of strings by converting each integer in the original collection to a string.

<tabs group="map-examples">
    <tab id="map-add2" title="Add 2 to Each Number" group-key="add2">
<code-block lang="c#">
<![CDATA[
IEnumerable<int> Add2(IEnumerable<int> numbers)
{
    List<int> result = new List<int>();

    foreach (var number in numbers)
    {
        result.Add(number + 2);
    }

    return result;

}
]]>
</code-block>
    </tab>
    <tab id="map-tostring" title="Map Number to String" group-key="tostring">
<code-block lang="c#">
<![CDATA[
IEnumerable<string> IntToString(IEnumerable<int> numbers)
{
    List<string> result = new List<string>();

    foreach (var number in numbers)
    {
        result.Add(number.ToString());
    }

    return result;

}
]]>
</code-block>
    </tab>
</tabs>

The only difference between the two functions lies in how new elements are generated in the collection. Using functional
techniques and C# generics we can remove the repetitive boiler-plate code.

```C#
public static IEnumerable<TResult> Map<TResult, TSource>(
    this IEnumerable<TSource> items,
    Func<TSource, TResult> func)
{
    var result = new List<TResult>();

    foreach (var item in items)
    {
        result.Add(func(item));
    }

    return result;
}
```

Usage:

```C#
var numbers = new[] { 1, 2, 3 };
var numbersPlus2 = numbers.Map(x => x + 2);
var strings = numbers.Map(x => x.ToString());
Assert.That(numbersPlus2, Is.EqualTo(new[] { 3, 4, 5 }));
Assert.That(strings, Is.EqualTo(new[] { "1", "2", "3" }));
```

The `Map` method is a generic function that takes an input collection of elements of type `TSource` and returns a new
collection of elements of type `TResult`. The method uses a delegate `Func<TSource, TResult>` that defines the
transformation or mapping from `TSource` to `TResult`.

#### Eager versus Lazy evaluation

The `Map()` method uses eager evaluation, meaning that a new collection is created, and the entire source collection is
iterated through, even if only a few items are needed. This can cause performance problems when working with large
collections.

```C#
public static IEnumerable<TResult> Map<TResult, TSource>(
    this IEnumerable<TSource> items,
    Func<TSource, TResult> func)
{
    foreach (var item in items)
    {
        yield return func(item);
    }
}
````

The `Map()` method is now a lazy evaluation version using yield return. This returns items one at a time as requested,
instead of all at once as in eager evaluation. It's more efficient as it only processes needed items. The code is
simpler because the compiler handles much of the work. A foreach statement can be used to retrieve items from the
method. Lazy `Map()` Extension Method
<note>

Lazy evaluation is a concept used in functional programming, Deferred execution is a type of lazy evaluation used in
.NET's LINQ, where a query is run only when its results are needed, instead of right away.

</note>

<note>

The `Map()` implementation is a simplified version of the .NET `System.Linq.Select()` method. This article uses
functional
programming terms and provides examples, but it's worth noting that many of these examples already exist in .NET.
</note>
### Deriving Filter()

Eager `LessThan5` method

```C#
IEnumerable<int> LessThan5(IEnumerable<int> numbers)
{
    List<int> result = new List<int>();
    foreach (var number in numbers)
    {
        if (number < 5) result.Add(number);
    }
    return result;
}
```

Lazy and Generic Filter Extension Method

```C#
public static IEnumerable<TSource> Filter<TSource>(
    this IEnumerable<TSource> items,
    Func<TSource, bool> predicate)
{
    foreach (var item in items)
    {
        if (!predicate(item)) continue;
        yield return item;
    }
}
````

Because `Map()` and `Filter()` return `IEnumerable<T>` we can chain them together.

```C#
var numbers = new[] { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
var numberLessThan5Plus2 = numbers
    .Filter(x => x < 5)
    .Map(x => x + 2);
foreach (var number in numberLessThan5Plus2)
{
    Console.WriteLine(number); // 3 4 5 6
}
````

### Deriving Reduce()

The Reduce() method in functional programming condenses a collection of items into a single result by iteratively
applying an operation to each item and passing the result to the next iteration. The result is obtained by repeating
this process until only one value remains. This technique can be used for operations such as addition, multiplication,
or custom operations.

Let's add up all numbers in a list or translate a point in the x direction by using numbers in the list, reducing the
collection to one item.

<tabs group="reduce">
    <tab id="sum-reduce" title="Sum all numbers" group-key="sum">
<code-block lang="c#">
<![CDATA[
var ints = new[] { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
int Total(IEnumerable<int> numbers)
{
    var result = 0;
    foreach (var number in numbers)
    {
        result += number;
    }
    return result;
}
var total = Total(ints);
Assert.That(total, Is.EqualTo(55));
]]>
</code-block>
    </tab>
    <tab id="point-reduce" title="Translate X coordinates" group-key="point">
<code-block lang="c#">
<![CDATA[
var ints = new[] { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
Point TranslateX(IEnumerable<int> numbers)
{
    var result = new Point(0,0);
    foreach (var number in numbers)
    {
       result = result with {X = result.X + number};
    }
    return result;
}
var finalPoint = TranslateX(ints);
Assert.That(finalPoint.X, Is.EqualTo(55));
]]>
</code-block>
    </tab>
</tabs>

In these two examples, the only differences are the way the initial value is set `var result = 0;` or
`var result = new Point(0,0);`  and the calculation that
updates the value on each iteration of the foreach loop `result += number;` or
`result = result with {X = result.X + number};`. We use the previous value and the current element of
the collection to compute a new value. To make this process generic, we can consider creating a single generic method
that accepts an initial seed value and a calculation function to perform the updates.

**Reduce** combines values in a collection into a single result.

```C#
public static TAccumulate Reduce<TSource, TAccumulate>(
    this IEnumerable<TSource> source,
    TAccumulate seed,
    Func<TAccumulate, TSource, TAccumulate> func)
{
    TAccumulate result = seed;
    foreach (TSource element in source)
    {
        result = func(result, element);
    }
    return result;
}
```

Usage:

```C#
var ints = new[] { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
var total = ints.Reduce(0, (acc, x) => acc + x);
Assert.That(total, Is.EqualTo(55));

var finalPoint = ints.Reduce(
    new Point(0, 0),
    (acc, x) => acc with { X = acc.X + x }
);
Assert.That(finalPoint.X, Is.EqualTo(55));
````

---

---
See Also:

- [Imperative vs Functional (Declarative, Expressive) Style of Programming](Imperative-vs-Functional-Declarative-Expressive-Style-of-Programming.md)
- [Higher-Order Function Examples](Higher-Order-Function-Examples.md)
- [Function composition and method chaining](Function-composition-and-method-chaining.md)
- [Transitioning from Classes to Records and Adopting Immutable Collections in C#](Transitioning-from-Classes-to-Records-and-Adopting-IImmutableList-in-C.md)
- [Defensive Copying in C# Example](Defensive-Copying-in-C-Example.md)
- [Using SelectMany](Using-SelectMany.md) (related to Map/Filter/Reduce)
- [Static Methods](Static-Methods.md) (mentioned as avoiding side effects on instance state)
- [DRY Principle](DRY-Principle.md)