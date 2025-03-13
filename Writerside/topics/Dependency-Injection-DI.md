# Dependency Injection (DI)

We focus on developing classes that delegate to abstractions. These abstractions are then implemented by other classes, which delegate some work to more abstractions. Eventually, a class is so small and directed that it need not delegate any further, and the chain of dependency is ended. In order to construct the dependent classes, first their dependencies are constructed and then injected in as dependencies.




Dependency injection (DI) is a very simple concept with a simple implementation. However, this simplicity belies the importance of the pattern. DI is the glue without which the techniques of SOLID would not be possible.

Implemented correctly, dependency injection is invisible to the majority of a project’s code.




When a dependency exists between class A and class B, if class B has a dependency on class C, it follows that class A is implicitly dependent on class C. If you ensure that your interfaces generalize—that is, that they correctly abstract their behavior—a class that depends on an interface does not depend on anything further. This holds true even though implementations of the interface might depend on something heavy and external, such as a database.

If you ensure that your interfaces generalize—that is, that they correctly abstract their behavior—a class that depends on an interface does not depend on anything further. This holds true even though implementations of the interface might depend on something heavy and external, such as a database.

When used correctly, interfaces provide your code with the extension points that make it adaptive.

McLean Hall, Gary