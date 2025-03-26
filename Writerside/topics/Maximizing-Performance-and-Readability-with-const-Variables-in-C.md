# Maximizing Performance and Readability with const Variables in C#

In C#, it is considered good practice to use the `const` keyword when possible because it has several benefits, such as:

1. **Improved performance**: Since the value of a `const` variable is known at compile-time, the compiler can replace
   the variable with its value throughout the code, resulting in faster execution.
2. **Enhanced readability**: `const` variables make it clear that the value will not change and can help to make the
   code more self-documenting.
3. **Memory optimization**: `const` variables are stored in memory as part of the metadata and their values are directly
   used in the memory, which is more memory efficient than storing the values in heap memory.
4. **Can be accessed directly from the class name**: You don't need to create an object to access the `const` variables,
   which is convenient and neat.

It's important to note that `const` variables can only be of a primitive type such as an integer, a string or a char and
must be initialized when they are declared and cannot be reassigned.

In cases where the value can't be determined at compile-time, it's better to use `static readonly`.

## const variables are stored in memory as part of the metadata

In C#, metadata refers to information about the types, members, and other elements in an assembly. This information is
stored in the assembly's metadata table, which is a data structure that contains information about the types, methods,
properties, and other elements that make up the assembly.

When a variable is declared as `const` its value is known at compile-time and it is embedded into the metadata table of
the assembly. The compiler will replace the variable with its value throughout the code, so that the value is available
at runtime without the need to look it up in memory.

This means that the value of a `const` variable is stored as part of the assembly's metadata, which is stored in memory,
rather than in the heap memory. This is more memory-efficient than storing the value in heap memory. This also allows
the constant to be accessed directly from the class name, without the need to create an object.

It's important to note that `const` variables are implicitly static and their values are shared among all instances of
the class, and therefore only one copy is stored in memory.

See Difference between const and static readonly
