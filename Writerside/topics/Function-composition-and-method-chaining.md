# Function composition and method chaining

In computer science, function composition is an act or mechanism to combine simple functions to build more complicated
ones. Like the usual composition of functions in mathematics, we pass the result of each function as the argument of the
next, and the result of the last one results from the whole. wikipedia

For example, say you want to get an email address for someone working at Manning. You can have a method to calculate the
local part (identifying the person) and another append the domain:

```C#
static string AppendDomain(string localPart) => $"{localPart}@manning.com";
static string AbbreviateName(Person p) => Abbreviate(p.FirstName) + Abbreviate(p.LastName);
static string Abbreviate(string s) => s.Substring(0, 2).ToLower();
```

`AbbreviateName` and `AppendDomain` are two methods that you can compose to get a new function that will yield the
Manning email for a hypothetical person:

```C#
static string EmailFor(Person p) => AppendDomain(AbbreviateName(p));
```

How to use it:

```C#
var email = EmailFor(new Person("John", "Doe")); // "jodo@manning.com"
```

In function composition, functions appear in the reverse order in which they're performed. For example, in
`AppendDomain(AbbreviateName(p))`, you first execute the rightmost function, and then the one to its left. This is, of
course, not ideal for readability, especially if you want to compose several functions. Therefore, in C# it's usual to
resort to method chaining instead.

The method chaining syntax (that is, chaining the invocation of several methods with the `.` operator) provides a more
readable way of achieving function composition in C#. Given an expression, you can chain to it any method that's defined
as an instance or extension method on the type of the expression.

The previous example would need to be changed:

```C#
static string AppendDomain(this string localPart) => $"{localPart}@manning.com";
static string AbbreviateName(this Person p) => Abbreviate(p.FirstName) + Abbreviate(p.LastName);
```

How to use it:

```C#
var email = new Person("John", "Doe")
              .AbbreviateName()
              .AppendDomain(); // "jodo@manning.com"
```

Notice that now the extension methods appear in the order in which they will be executed. This significantly improves
readability, especially as the complexity of the workflow increases, and it's why method chaining is the preferable way
of achieving function composition in C#.

There are some properties that make some functions more composable than others:

* **Pure:** If your function has side effects, it's less reusable.
* **Chainable:** A `this` argument (implicit on instance methods and explicit on extension methods) makes it possible to
  compose through chaining.
* **General:** The more specific the function, the fewer cases there will be where it's useful to compose it.
* **Shape-preserving:** The function preserves the "shape" of the structure; so, if it takes an `IEnumerable`, it
  returns an `IEnumerable`, and so on.

Function composition is not only powerful and expressive but also pleasant to work with. For example, have you noticed
that when you use LINQ to work with lists, you can get a lot done with only a few lines of code? That's because LINQ is
a functional API, designed with composition in mind.

*This information came from Buonanno 102-104*

---
See Also:
- [Functional in the Small and Object-Oriented in the Large](Functional-in-the-Small-and-Object-Oriented-in-the-Large.md)
- [Imperative vs Functional (Declarative, Expressive) Style of Programming](Imperative-vs-Functional-Declarative-Expressive-Style-of-Programming.md)
- [Higher-Order Function Examples](Higher-Order-Function-Examples.md)
- [Using SelectMany](Using-SelectMany.md) (LINQ example)