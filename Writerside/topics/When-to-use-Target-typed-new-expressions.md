# When to use Target-typed new expressions

We shouldn't be using `new()` frequently unless it enhances visibility
and readability.

## This would be a suitable time to use it:

```C#
// Before: Type name repeated
private readonly Dog dog = new Dog();

// After: Type name inferred, arguably more readable for field declarations
private readonly Dog dog = new();
```

## The following example is NOT the time to use it:

```C#
// Overuse of new() obscures the types being created, harming readability
var dictionary = new Dictionary<object, RepositoryResponse<PatientContents>>
{
    [categoryId1] = new() // What type is this? RepositoryResponse<PatientContents>
    {
        ResponseObject = new() // What type is this? PatientContents
        {
            Items = new() // What type is this? Presumably List<PatientItem> or similar
            {
                // What type are these items? PatientItem?
                new()
                {
                    Type = type1
                },
                new()
                {
                    Type = type2,
                    Restricted = true
                }
            }
        }
    },
    [categoryId2] = new()
    {
        ResponseObject = new()
        {
            Items = new()
            {
                new()
                {
                    Type = type3,
                    Restricted = false
                }
            }
        }
    },
    [categoryId3] = new()
    {
        ResponseObject = new()
        {
            Items = new() // Creates an empty list/collection
        }
    }
};
```

In the above example, the developer now must hover over the `new()`
keywords in an IDE with the mouse to see what type is being instantiated.
A reader without an IDE wouldn’t know the types, significantly reducing
code clarity. Explicitly stating the type names in complex initializations
like this is preferable for maintainability.

note: The author of this article Is a fellow coworker named Drew.

---
See Also:
- [var keyword](var-keyword.md) (Related type inference concept)
- [Code Review Checklist](Code-Review-Checklist.md) <!-- Could add a check for overuse of new() -->