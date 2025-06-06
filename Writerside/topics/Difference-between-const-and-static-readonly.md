# Difference between const and static readonly

In C#, the difference between `static readonly` and `const` is as follows:

- **`static readonly`:**
    - Is a runtime constant.
    - Its value can be initialized either at the time of declaration or in a static constructor, and it cannot be
      changed thereafter.
    - It is accessed via the class name, and its memory is allocated only once.
    - It can be of any type, including reference types.

- **`const`:**
    - Is a compile-time constant.
    - Its value must be initialized when declared and cannot be changed at runtime.
    - It is accessed via the class name, and its memory is also allocated only once.
    - It can only be used with primitive types or other compile-time constants.

The main difference is that a `static readonly` field can be initialized at runtime, whereas a `const` must be
initialized at compile time.

---

## Exploring the Differences Through Code

### Original C# Code Example

```C#
public class ConstVsStaticReadonlyDemo
{
    private const string ConstStr = "Hello, world!";
    private static readonly string StaticReadonlyStr = "Hello, world!";   
    private static readonly List<string> StaticReadonlyList = new()
    {
        "Hello, world!",
        "Hello, world!"
    };

    public static void PrintValues()
    {
        Console.WriteLine(StaticReadonlyStr);
        Console.WriteLine(ConstStr);
        Console.WriteLine(StaticReadonlyList[0]);
    }
}
```

### Compiled Low-Level C# Code

Below is an illustration of what the compiled code might resemble:

```C#
[NullableContext(1)]
[Nullable(0)]
public class ConstVsStaticReadonlyDemo 
{
    private const string ConstStr = "Hello, world!";
    private static readonly string StaticReadonlyStr;   
    private static readonly List<string> StaticReadonlyList;
    
    public static void PrintValues() 
    {
        Console.WriteLine(ConstVsStaticReadonlyDemo.StaticReadonlyStr);
        Console.WriteLine("Hello, world!");
    }
    
    public ConstVsStaticReadonlyDemo() 
    {
        base..ctor();
    }
    
    static ConstVsStaticReadonlyDemo() 
    {
        List<string> stringList = new List<string>();
        stringList.Add("Hello, world!");
        stringList.Add("Hello, world!");
        ConstVsStaticReadonlyDemo.StaticReadonlyList = stringList;
        ConstVsStaticReadonlyDemo.StaticReadonlyStr = "Hello, world!";
    }
}
```

---

## Explanation

- **`const` Variables:**  
  When the compiler encounters a constant string, it embeds the constant's value directly into the code wherever it is
  referenced. This eliminates additional memory overhead and provides fast access times.

- **`static readonly` Variables:**  
  The compiler generates a static constructor for the class to initialize `static readonly` fields at runtime, during
  the class's first use. Although this introduces a slight overhead at first use, it allows for runtime initialization
  and supports non-primitive types.

---

## Performance Considerations

- **`const` Variables:**  
  Offer better performance since their values are embedded at compile time, resulting in direct, inlined values with no
  extra memory access.

- **`static readonly` Variables:**  
  Require a static constructor to initialize them at runtime, introducing a small overhead on first use, but they offer
  more flexibility regarding initialization and type usage.

*Note: Using `static readonly` for a collection such as a `List<T>` ensures that the reference remains constant;
however, it does not prevent modification of the collection's contents at runtime.*

---

## Conclusion

Both `static readonly` and `const` are useful for declaring constants in C#, but they serve different purposes:

- Use **`const`** when:
    - The value is truly constant and can be determined at compile time.
    - You are working with primitive types or compile-time constants.
    - You require maximum performance with no runtime overhead.

- Use **`static readonly`** when:
    - The value needs to be determined at runtime.
    - You are working with complex types or reference types.
    - You need the flexibility of runtime initialization despite a small initial overhead.

---
See Also:
- [Code Review Checklist](Code-Review-Checklist.md) (mentions const/readonly)
- [Maximizing Performance and Readability with const Variables in C#](Maximizing-Performance-and-Readability-with-const-Variables-in-C.md)
- [Static Methods](Static-Methods.md) (related 'static' concept)
- [Transitioning from Classes to Records and Adopting Immutable Collections in C#](Transitioning-from-Classes-to-Records-and-Adopting-IImmutableList-in-C.md) (related to immutability)
