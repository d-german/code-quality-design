# Imperative vs Functional (Declarative, Expressive) Style of Programming

To sum the elements of an int array named `_values` (the data source), you can utilize the following code:

### Imperative Style Example (External Iteration)

```C#
var sum = 0;
for (var counter = 0; counter < _values.Count; counter++)
{
    sum += _values[counter];
}
```

This loop clearly outlines the process of adding each element's value to the sum using a for loop that iterates through
each element one at a time. This approach is referred to as **external iteration** because you dictate how the iteration
occurs rather than relying on the library. It necessitates sequential access to the elements from start to finish within
a single execution thread. In this case, two mutable variables (`sum` and `counter`) are created, which change their
values throughout the iteration. (Detiel 868)

However, external iteration presents several opportunities for error, even in this straightforward loop. Potential
issues include:

- Incorrect initialization of the variable `sum`
- Incorrect initialization of the control variable `counter`
- Misuse of the loop-continuation condition
- Improper incrementing of the control variable `counter`
- Incorrectly adding each value in the array to the sum

---

## Functional (Declarative, Expressive) Programming

In functional programming, you specify **what** you want to achieve rather than **how** to achieve it. (Detiel 868)

For example, using LINQ you can express:

```C#
var sum = _values.Aggregate(0, (result, item) => result + item);
```

Or even more succinctly:

```C#
var sum = _values.Sum();
```

Here, you declare: “Here’s a data source; provide me with the sum of its elements.” There is no need to specify how to
iterate through the elements or to declare and manipulate any mutable variables. This method is known as **internal
iteration**, as the library code (LINQ, in this case) handles the iteration through all the elements to perform the
task. (Detiel 868)

---

## Sorting: Destructive vs. Non-Destructive Methods

Sorting an array imperatively:

```C#
Array.Sort(_values);
```

The `_values` array is sorted **in place**, meaning the original array is modified. Thus, the `Sort()` method is
considered “destructive.” If you wish to preserve `_values` from changing, you would need to create a copy first. (TBD)

Using LINQ for a non-destructive sort:

```C#
var sortedValues = _values.OrderBy(item => item);
```

Here, `_values` remains unchanged.

---

## Three Common Functional Programming Operations on Collections

| Operation | LINQ Equivalent                                    | Description                                                                                                              | Example                                                       |
|-----------|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------|
| filter    | `Where()`                                          | Produces a new collection containing only the elements that meet a specified condition                                   | `var evenValues = _values.Where(value => value % 2 == 0);`    |
| map       | `Select()`                                         | Generates a new collection where each element of the original collection is transformed to a new value                   | `var valuesSquared = _values.Select(value => value * value);` |
| reduce    | `Aggregate()`, `Average()`, `Max()`, `Sum()`, etc. | Combines the elements of a collection into a single new value, typically using a lambda that defines how to combine them | `var sum = _values.Sum();`                                    |

*Note:* These operations do not modify the original collection.

---

## Declarative Style and Cyclomatic Complexity (CC)

Using LINQ typically reduces Cyclomatic Complexity. For example:

```C#
var greaterThan2 = new List<int> { 3, 10, 6, 1, 4, 8, 2, 5, 9, 7 }
    .Where(i => i > 2);
```

- This method has a CC of 1 since it contains no explicit branching.
- However, the LINQ `Where()` method itself has a CC value of 7.
- By utilizing LINQ or a declarative style, we reduced the overall CC by 85%.

---

## Implementation of the LINQ `Where()` Method

Below is an example implementation of the `Where()` method:

```C#
public static IEnumerable<TSource> Where<TSource>(
    this IEnumerable<TSource> source, 
    Func<TSource, bool> predicate)
{
    if (source == null)
    {
        ThrowHelper.ThrowArgumentNullException(ExceptionArgument.source);
    }
    if (predicate == null)
    {
        ThrowHelper.ThrowArgumentNullException(ExceptionArgument.predicate);
    }
    if (source is Iterator<TSource> iterator)
    {
        return iterator.Where(predicate);
    }
    if (source is TSource[] array)
    {
        return array.Length == 0 ? Empty<TSource>() : new WhereArrayIterator<TSource>(array, predicate);
    }
    if (source is List<TSource> list)
    {
        return new WhereListIterator<TSource>(list, predicate);
    }
    return new WhereEnumerableIterator<TSource>(source, predicate);
}
```

For more details on cyclomatic complexity in LINQ, see:  
[StackOverflow: Cyclomatic Complexity of C# LINQ](https://stackoverflow.com/questions/47102263/cyclomatic-complexity-of-c-sharp-linq)

---

## Summary

- **Imperative style** involves external iteration, explicit loops, and mutable state, which can introduce errors.
- **Functional style** leverages internal iteration through LINQ, which abstracts the iteration details and promotes
  immutability.
- Functional programming emphasizes clear separation between data and behavior, reducing side effects and improving code
  testability.
- Common functional operations such as **filter (Where)**, **map (Select)**, and **reduce (Aggregate)** provide powerful
  abstractions for working with collections.
- Declarative approaches can significantly reduce cyclomatic complexity, though it's important to understand the
  underlying cost of methods like `Where()`.

This transition from imperative to functional programming highlights how modern programming paradigms can lead to
cleaner, more maintainable, and expressive code.