# Transitioning from Classes to Records and Adopting Immutable Collections in C#

The purpose of this article is to advocate for using read-only records and immutable data structures when creating new
types in our codebase. Moreover, whenever possible, we should update existing types to adopt these patterns.

In this article, we'll discuss how to update a C# code snippet to use records instead of classes and switch collection
types to an immutable list interface. We'll also demonstrate two common implementations of immutable collections and
briefly compare their differences.

---

## Original Class Definitions

Initially, we had the following class definitions:

```C#
public class DocumentType
{
    public string Id { get; set; }
    public string SystemName { get; set; }
}

public class DocumentTypes
{
    public IEnumerable<DocumentType> Items;
}
```

---

## Changes Made

We made the following changes:

- Converted `DocumentType` and `DocumentTypes` from classes to records.
- Changed the `Items` property in `DocumentTypes` to use `ImmutableArray<DocumentType>`.

The updated code now looks like this:

```C#
public record DocumentType
{
    public string Id { get; init; }
    public string SystemName { get; init; }
}

public record DocumentTypes
{
    public ImmutableArray<DocumentType> Items { get; init; }
}
```

Having an immutable array doesn't prevent someone from modifying the contents of an element in the list. To prevent
unintended modifications, it's recommended to make the items in the list immutable. Since `DocumentType` is now a
read-only record and its properties are of type string (which are immutable), we don't have to worry about modifications
to individual elements.

---

## Demonstrating Immutable Collections

To showcase the flexibility of immutable collections, we implemented two tests using two different concrete
implementations: `ImmutableArray<T>` and `ImmutableList<T>`. (Note: These examples are generic and do not refer to any
specific project.)

### Using `ImmutableArray<T>`

```C#
[Test]
public void DocumentTypesImmutableArray()
{
    var documentTypes = new DocumentTypes
    {
        Items = new List<DocumentType>
        {
            new DocumentType { Id = "1", SystemName = "SystemName1" },
            new DocumentType { Id = "2", SystemName = "SystemName2" },
        }.ToImmutableArray()
    };
    Assert.That(documentTypes.Items, Has.Count.EqualTo(2));
}
```

### Using `ImmutableList<T>`

```C#
[Test]
public void DocumentTypesImmutableList()
{
    var documentTypes = new DocumentTypes
    {
        Items = new List<DocumentType>
        {
            new DocumentType { Id = "1", SystemName = "SystemName1" },
            new DocumentType { Id = "2", SystemName = "SystemName2" },
        }.ToImmutableList()
    };
    Assert.That(documentTypes.Items, Has.Count.EqualTo(2));
}
```

---

## Comparing Immutable Collections

Both `ImmutableArray<T>` and `ImmutableList<T>` provide immutable collections, but they have different performance
characteristics:

- **`ImmutableArray<T>`**:  
  Backed by a simple, contiguous array, it has lower memory overhead and better cache locality, making it ideal for
  scenarios requiring efficient random access.

- **`ImmutableList<T>`**:  
  Uses a more complex data structure (such as a balanced tree) that offers better performance for insertions and
  deletions in the middle of the collection but comes with higher memory usage.

For brevity, the remainder of this discussion will focus solely on the `ImmutableArray<T>` approach.

---

## Creating Immutable Arrays in C#

The `ImmutableArray<T>` class provides several methods to create immutable collections, each with its own benefits and
trade-offs:

1. **`ToImmutableArray()`**:  
   An extension method available on any `IEnumerable<T>` (from the `System.Collections.Immutable` namespace). It creates
   an `ImmutableArray<T>` by copying elements into a new array. This is convenient when converting an existing mutable
   collection.

2. **`ImmutableArray.Create<T>(params T[] items)`**:  
   A static method to create an `ImmutableArray<T>` from a fixed set of items. This is useful when you have a small
   number of items known at compile time.

   ```C#
   var array = ImmutableArray.Create(
       new DocumentType { Id = "1", SystemName = "SystemName1" },
       new DocumentType { Id = "2", SystemName = "SystemName2" }
   );
   ```

3. **`ImmutableArray.CreateBuilder<T>()`**:  
   This method creates a mutable builder for an `ImmutableArray<T>`. You can add or remove items, and then convert it to
   an immutable array using the `ToImmutable()` method. This approach is more efficient when constructing large
   collections or when the number of items is unknown. This approach is analogous to using `StringBuilder` in C# to
   efficiently create a string by building it incrementally before finalizing the immutable result.

   ```C#
   var builder = ImmutableArray.CreateBuilder<DocumentType>();
   builder.Add(new DocumentType { Id = "1", SystemName = "SystemName1" });
   builder.Add(new DocumentType { Id = "2", SystemName = "SystemName2" });
   var array = builder.ToImmutable();
   ```

This article illustrates how converting classes to records and implementing immutable collection types not only promotes
immutability but also enhances overall design stability. The performance differences between `ImmutableArray<T>` and
`ImmutableList<T>` arise from their data structure choices rather than their immutable nature.

---