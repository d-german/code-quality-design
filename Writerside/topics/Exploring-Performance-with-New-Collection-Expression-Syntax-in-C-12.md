# Exploring Performance with New Collection Expression Syntax in C# 12

C# 12 introduces a new way to initialize collections, known as the collection expression syntax. This feature not only
simplifies code but also enhances performance, especially for large collections.

[IDE0305: Use collection expression for fluent - .NET](https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/style-rules/ide0305)

## Old Syntax

```C#
List<int> i = new[] { 1, 2, 3 }.ToList();
```

## New Syntax

```C#
List<int> i = [1, 2, 3];
```

## A Comparative Analysis

### 1. Small Collections

**Traditional Syntax**

```C#
List<int> numbers = new List<int> { 1, 2 };
```

**Compiles to**

```C#
List<int> intList = new List<int>();
intList.Add(1);
intList.Add(2);
```

**New C# 12 Syntax**

```C#
List<int> numbers = [1, 2];
```

**Compiles to**

```C#
List<int> list = new List<int>();
// Pre-sizes the list's internal capacity if possible
CollectionsMarshal.SetCount<int>(list, 2);
// Gets a Span<T> pointing to the list's internal storage
Span<int> span = CollectionsMarshal.AsSpan<int>(list);
// Directly writes values into the Span<T>
span[0] = 1;
span[1] = 2;
```

**Benchmark Result:**

| Method             | Mean     | Allocated |
|:-------------------|:---------|:----------|
| Traditional Syntax | 20.17 ns | 72 B      |
| New C# 12 Syntax   | 17.26 ns | 72 B      |

* Traditional Syntax: 20.17 ns
* New Syntax: 17.26 ns

The new syntax shows a ~17% performance improvement, which can be significant in performance-critical applications.

### 2. Large Collections

For larger collections, the performance difference becomes more pronounced.

**Traditional Syntax (Loop-Based)**

```C#
List<int> numbers = new List<int>();
for(int i = 0; i < 100000; i++)
{
    numbers.Add(i);
}
```

**Compiles to**

```C#
List<int> numbers = new List<int>();
for (int i = 0; i < 100000; ++i)
  numbers.Add(i);
```

**New C# 12 Syntax**

```C#
// Assumes largeRange is preallocated, e.g., an array or list
// var largeRange = Enumerable.Range(1, 100000).ToArray();
List<int> numbers = [..largeRange];
```

**Compiles to**

```C#
// Assuming largeRange is an int[] field 'this.largeRange'
int[] largeRange = this.largeRange;
List<int> list = new List<int>();
// Pre-sizes the list using the source length
CollectionsMarshal.SetCount<int>(list, largeRange.Length);
// Gets a Span<T> pointing to the list's internal storage
Span<int> span = CollectionsMarshal.AsSpan<int>(list);
int index1 = 0;
int[] numArray = largeRange;
// Iterates through the source array
for (int index2 = 0; index2 < numArray.Length; ++index2)
{
  int num = numArray[index2];
  // Directly copies value into the destination Span<T>
  span[index1] = num;
  ++index1;
}
```

**Benchmark Result:**

| Method             | Mean     | Allocated  |
|:-------------------|:---------|:-----------|
| Traditional Syntax | 572.2 us | 1024.48 KB |
| New C# 12 Syntax   | 127.6 us | 390.72 KB  |

* Traditional Syntax: 572.2 us
* New Syntax: 127.6 us

In this scenario, the new syntax is more than 77% or 4 times faster than the traditional approach. Additionally, there's
a significant reduction in memory allocation (about 63% less), which is crucial for large-scale applications.

## Understanding `CollectionsMarshal` and `Span<T>`

* **`CollectionsMarshal`**: This class in .NET provides low-level utilities for collections. In the new syntax,
  `CollectionsMarshal.SetCount` efficiently sets the number of elements in the list, potentially reducing the overhead
  of multiple memory allocations that might occur with repeated `Add` calls if the list needs resizing.
* **`Span<T>`**: The `Span<T>` struct is a versatile façade over arrays, strings, or any contiguous memory block. It's
  primarily designed for micro-optimization, particularly in writing code that minimizes managed memory allocations,
  thus easing the burden on the garbage collector. (`CollectionsMarshal.AsSpan` provides access to a list's internal
  buffer as a `Span<T>`). This allows the compiler to generate code that directly writes data into the list's memory
  block, bypassing the overhead of the `Add` method for each element. Additionally, `Span<T>` facilitates slicing, which
  allows working with parts of an array or string efficiently, without the need for creating copies of these segments. (
  C# 10 in a Nutshell Ch 23)

## Conclusion

The new collection expression syntax in C# 12 offers a significant performance boost, particularly with larger
collections created from existing collections (using the spread `..` operator). This feature enhances not just the speed
but also the memory efficiency, making it a preferable choice in scenarios where performance is a key concern. While the
benefit is less noticeable with small, literal collections, it becomes substantial with larger datasets, demonstrating
the efficiency of modern C# capabilities in handling complex and large-scale data.

---
See Also:

- [Memory Allocation and Management](Memory-Allocation-and-Management-Reference-Types-Value-Types-Arrays-and-Linked-Lists.md) (
  mentions `Span<T>`)
- [Imperative vs Functional (Declarative, Expressive) Style of Programming](Imperative-vs-Functional-Declarative-Expressive-Style-of-Programming.md) (
  declarative style)
- [Transitioning from Classes to Records and Adopting Immutable Collections in C#](Transitioning-from-Classes-to-Records-and-Adopting-IImmutableList-in-C.md) (
  collections focus)