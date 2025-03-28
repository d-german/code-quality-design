# Using SelectMany()

Here we are combining several lists into a single list and returning it. `rule(config)` returns
`IEnumerable<ValidationError>`.
This code is very imperative. We are creating and mutating state.

```C#
public virtual IEnumerable<ValidationError> ValidationErrors(
    Configuration config)
{
    var errors = new List<ValidationError>();

    foreach (var rule in ValidationRules)
    {
        errors.AddRange(rule(config));
    }

    return errors;
}
```

Whenever you're dealing with multiple lists that need to be flattened into a single list, consider using `SelectMany()`.

```C#
public virtual IEnumerable<ValidationError> ValidationErrors(
    Configuration config)
{
    return ValidationRules.SelectMany(rule => rule(config));
}
```

The `SelectMany()` method is used to "flatten" a sequence in which each element of the sequence is an underling
sequence, like a two-dimensional array or a list of lists.

```C#
var list1 = new List<List<int>>
{
    new() { 1, 2, 3 },
    new() { 4, 5, 6 },
    new() { 7, 8, 9 }
}.SelectMany(numbers => numbers).ToList();

var list2 = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9 };

Debug.Assert(list1.SequenceEqual(list2));
```

Of course, if you need to transform each element before flattening, you can use a normal `Select()`.

```C#
var list1 = new List<List<int>>
{
    new() { 1, 2, 3 },
    new() { 4, 5, 6 },
    new() { 7, 8, 9 }
}.SelectMany(numbers => numbers.Select(number => number * 10))
 .ToList();

var list2 = new List<int> { 10, 20, 30, 40, 50, 60, 70, 80, 90 };

Debug.Assert(list1.SequenceEqual(list2));
```
---
See Also:
- [Imperative vs Functional (Declarative, Expressive) Style of Programming](Imperative-vs-Functional-Declarative-Expressive-Style-of-Programming.md)
- [Functional in the Small and Object-Oriented in the Large](Functional-in-the-Small-and-Object-Oriented-in-the-Large.md) (Map/Filter/Reduce)
- [Function composition and method chaining](Function-composition-and-method-chaining.md)
