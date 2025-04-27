# Multiple Choice Questions: Polymorphism

## Question 1
What is the core idea of Polymorphism as described in the article?
- [ ] Making all objects behave identically at compile time.
- [ ] Forcing developers to know the exact type of every object.
- [x] The ability to treat objects of different classes (subtypes) as if they were objects of a common supertype.
- [ ] A way to strictly enforce early binding for all methods.

## Question 2
According to the "Fruit Example" (Riel 95), polymorphism allows a collection of different specific fruits (like apples, oranges) to be treated homogeneously as:
- [ ] A collection of specific types that must be checked individually.
- [ ] Abstract classes that cannot be instantiated.
- [x] A collection of the general type "fruit", while retaining their specific behavior at runtime.
- [ ] Objects that lose their specific identity once placed in a collection.

## Question 3
When a method accepts an instance of a supertype, what does polymorphism allow regarding subtypes? (McLean Hall, 281)
- [ ] It requires explicit casting to the subtype within the method.
- [x] It allows instances of the subtype to be passed in and treated as the supertype without casting or type-checking.
- [ ] It prevents subtypes from being used where the supertype is expected.
- [ ] It requires the method to check the specific subtype before proceeding.

## Question 4
The C# example using the `IFoo` interface, classes `A` and `B`, and the `Choose` method demonstrates which type of polymorphism?
- [x] Dynamic / Runtime / Late Binding Polymorphism
- [ ] Static / Compile-time / Early Binding Polymorphism
- [ ] Method Overloading
- [ ] Generics

## Question 5
Method overloading, as shown in the C# examples with different `Foo` methods, is a form of:
- [ ] Runtime Polymorphism
- [x] Static / Compile-time Polymorphism
- [ ] Inheritance
- [ ] Interface implementation

## Question 6
Generics, like the `Swap<T>` method, are categorized under which type of polymorphism in the article?
- [ ] Dynamic / Runtime Polymorphism
- [x] Static / Compile-time Polymorphism
- [ ] Duck Typing
- [ ] Covariance

# Free Response Questions: Polymorphism

## Short Answer Question 1
In your own words, explain the main benefit of polymorphism for a programmer, as mentioned in the initial definition (Coplien 86).
> Answer: The main benefit is abstraction. Polymorphism shields the programmer from needing to know the specific implementation details of different derived classes. They can interact with objects through a common interface (the supertype), simplifying the code that uses these objects.

## Short Answer Question 2
Using the C# example with `IFoo`, `A`, `B`, and `Choose`, explain how runtime polymorphism works. What determines whether `A`'s or `B`'s `Bar` method is called?
> Answer: The `Choose` method accepts any object that implements the `IFoo` interface. The specific object passed in (either an instance of `A` or `B`) determines which `Bar` method is executed. This decision is made at *runtime* based on the actual type of the object held by the `foo` variable, not at compile time. This is late binding.

## Short Answer Question 3
Explain what method overloading is and why it's considered compile-time polymorphism.
> Answer: Method overloading is defining multiple methods within the same scope (e.g., a class) that have the same name but different parameter lists (different types, number, or order of parameters). It's considered compile-time polymorphism because the compiler determines exactly which overloaded method to call based on the arguments provided at the call site during compilation (early binding).

## Short Answer Question 4
How does polymorphism relate to the concept of treating a subtype as its supertype, as described by McLean Hall?
> Answer: Polymorphism is the mechanism that enables a subtype instance to be used wherever a supertype instance is expected. Code written against the supertype interface can operate on subtype objects without needing to know their specific subtype, simplifying interactions and promoting decoupling. The subtype *is-a* supertype, so it can be treated as such.