# Static Methods

It is beneficial to use static methods when the method does not reference any instance fields, as it improves
performance, enhances code readability, promotes better code organization, reduces memory usage, and can be called
directly from the class name.

1. Increased performance: Since a static method does not need to be invoked on an instance of a class, it can be called
   more efficiently.
2. Enhanced readability: When reading code, it is often clearer to see that a method is static and therefore does not
   rely on any specific instance state.
3. Better organization: By separating methods that operate independently of instances from methods that rely on instance
   state, the code can be more organized and easier to understand.
4. Lowers memory usage: Since static methods don't require instantiation, it reduces the memory usage when comparing to
   instance methods.
5. Can be called directly from the class name: You don't need to create an object to call static methods, which is
   convenient and neat.

---
See Also:
- [Code Review Checklist](Code-Review-Checklist.md) (mentions static methods)
- [Difference between const and static readonly](Difference-between-const-and-static-readonly.md) (related `static` keyword)
- [Functional in the Small and Object-Oriented in the Large](Functional-in-the-Small-and-Object-Oriented-in-the-Large.md) (pure functions often map to static methods)