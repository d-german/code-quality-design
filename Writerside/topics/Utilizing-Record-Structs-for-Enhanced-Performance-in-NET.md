# Utilizing Record Structs for Enhanced Performance in .NET

When developing DTOs or Value Objects in .NET, consider the adoption of
`record struct`s instead of traditional `record`s (which are classes).
This change can lead to improved performance metrics and reduced memory
overhead due to the value-type nature of structs, avoiding heap
allocations for small, frequently created objects.

**Record Class Example:**

```C#
public record BookId
{
    public required Guid Value { get; init; }
}
```

**Record Struct Example:**

```C#
public record struct AuthorId
{
    public required Guid Value { get; init; }
}
```

Through benchmarking, we observe a considerable performance increase and
memory savings when opting for `record struct`s, especially when these
types are instantiated frequently:

| Method               | Mean (ns) | Allocated (B) |
|:---------------------|:----------|:--------------|
| BookIdRecord         | 126.41    | 128           |
| AuthorIdRecordStruct | 51.90     | 96            |

While the differences may appear trivial at a glance, with execution times
in nanoseconds, the shift to `record struct` yields a roughly 140% increase
in speed (lower is better, so it takes less time) and a notable 32 B
reduction in memory allocation per instance in this specific benchmark.
This optimization, albeit small on a per-instance basis, can accumulate
significantly in high-throughput systems, reducing memory pressure on the
garbage collector. Choose `record struct` when value-type semantics and
performance are critical for small, immutable data holders.

---
See Also:
- [Memory Allocation and Management](Memory-Allocation-and-Management-Reference-Types-Value-Types-Arrays-and-Linked-Lists.md)
- [Transitioning from Classes to Records and Adopting Immutable Collections in C#](Transitioning-from-Classes-to-Records-and-Adopting-IImmutableList-in-C.md)
- [DTO - Data Transfer Object](DTO-Data-Transfer-Object.md) (Structs can be used for DTOs)
- [Self-Validating Value Objects in C#](Self-Validating-Value-Objects-in-C.md) (Value objects often benefit from struct performance)