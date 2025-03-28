# The Dependency Rule

The long-term health, adaptability, and possibly the viability of a project relies on sound management of dependencies.
A mess of spaghetti-like interdependencies will occur if developers create classes that reference each other without
careful consideration, which can seriously affect a team's ability to deliver business value in a consistent,
predictable fashion. (McLean Hall, Gary)

## Policy and Details

All software systems can be decomposed into two major elements: **policy** and **details**. (2017 Martin 140)

* The **policy** element embodies all the business rules. The policy is where the true value of the system lives. The
  policy should have no knowledge of the details and does not depend on the details in any way.
* The **details** are those things that are necessary to enable humans, other systems, and programmers to communicate
  with the policy, but that does not impact the behavior of the policy at all. They include IO devices, databases, web
  systems, servers, frameworks, GUI, communication protocols, and so forth.

## Business Rules

**Business rules** are rules or procedures that make or save the business money.

* Some business rules are considered Critical Business Rules and are critical to the business itself and would exist
  even if there were no system to automate them. The fact that a bank charges N% interest for a loan is a business rule
  that makes the bank money.
* Other business rules may be closely tied to the application, or they may be more general. For example, the validation
  of input fields is a business rule that is closely tied to the application itself.

## Separating Business Rules (Critical vs. Application Specific)

These two different kinds of rules (Critical Business Rules and Application Specific Business Rules) will change at
different rates, and for different reasons—so they should be separated so that they can be independently changed. See
SRP (2017 Martin 190)

## Entities: Critical Business Rules

An **Entity** is a class that contains a small set of critical business rules operating on Critical Business Data. Loan,
for example:

```PlantUML
    @startuml
class Loan {
  - _principle
  - _rate
  - _period
  + MakePayment()
  + ApplyInterest()
  + ChargeLateFee()
}
@enduml    
    
```

## Use Cases: Application-Specific Business Rules

Use cases specifies the input to be provided by the user, the output to be returned to the user, and the processing
steps involved in producing that output.

![](usecase.png)

## Relationship Between Entities and Use Cases

Entities classes are at a higher level than classes that implement use cases.

Why? Because use cases are specific to a single application and, therefore, are closer to the inputs and outputs of that
system. Entities are generalizations that can be used in many different applications, so they are farther from the
inputs and outputs of the system. Use cases depend on Entities; Entities do not depend on use cases. (2017 Martin 193)

## Levels and Change Rate (SRP)

Policies that change for the same reasons, and at the same time, are at the same level. Policies that change for
different reasons, or at different times, are at different levels. see Single Responsibility Principle (SRP)

## Details Depend on Policies

The code that implements high-level (HL) policy should not depend on the code that implements low-level (LL) details.
Rather, **details should depend on policies**.

## Dependency Notation

Here we are letting a simple arrow `⟶` represent any type of dependency and we represent **inheritance** with the open
arrow `─▷`.

* **The reason for the distinction is that inheritance is the strongest, and most rigid, of all the source code
  relationships; consequently, it should be used with great care.** (2017 Martin 88)

## The Problem with Dependencies

For a class, its dependencies include all the modules on which each of its elements depends. The key problem with
dependencies is that a module may break whenever one of its dependent modules has changed. Here, change is not only
about the syntax but also the semantics of the coupled modules. (Dianxiang Xu 89)

*![The Dependency Rule](dependency-rule1.png){style="block"}*
In this diagram module A has no dependencies but module B depends on A. This means that module A is protected from
changes made to module B. This also means that module B can break if changes are made to module A. **We should organize
our dependencies so that higher-level modules are protected from changes from lower-level modules.**

## Organizing Levels and Dependencies

Where to put items that belong to the same level?

### Small to Medium Applications

* folders or namespaces.

### Larger Applications

* Components (assemblies in .NET) are the units of deployment.
    * Java- .jar files; .NET - .dll; c++ .lib, .dll, ...
    * Can be linked together into a single executable. Or they can be aggregated together into a single archive, such as
      a .war file.
    * Can be independently deployed as separate dynamically loaded plugins, such as .jar or .dll or .exe files.

## The Dependency Rule (Formal Statement)

* Source code dependencies must point only toward higher-level (HL) software artifacts (classes, modules, components,
  etc.).
  *![](dependency-rule2.png){style="block"}*
* That includes functions, classes, variables, or any other software artifact.
* Data Formats (`DTO` etc.) declared in LL code should not be used by HL code, especially if those formats are generated
  by a framework in the LL (database models). We don't want anything in the LL code to impact the HL.
* Data that crosses the boundaries should be simple data structures. (Structs or DTOs)
* Don't pass Entity objects or database rows.
* When passing data across a boundary, it is always in the form that is most convenient for the HL module.

## High-Level Invisibility of Low-Level Details

**Nothing at a HL can know anything at all about something in a lower level (LL).**

## Name Restrictions

The name of something declared in LL code must not be mentioned in HL code.

## Handling Flow of Control (Dependency Inversion)

Suppose something in a HL module (namespace or component) needs to call (flow of control) something in a LL module.
*![](dependency-rule3.png){style="block"}*

* This call must not be direct because that would violate the Dependency Rule. See Dependency Inversion Principle (DIP)
* Instead have the HL code call an interface that the LL code implements.
* Also, the interface must be declared in the HL module.

*![](dependency-rule4.png){style="block"}*

The HL component is calling the `DataProvider` via the `IDataProvider` interface. Notice that whenever a dependency
arrow crosses the border of a component the direction is always in the direction of LL to HL.

*![](dependency-rule5.png){style="block"}*
This is not the correct design since a dependency arrow is crossing from a HL to a LL component.

---
See Also:
- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md) (related to levels/change rate)
- [Coupling](Coupling.md)
- [Stable vs Volatile Dependencies](Stable-vs-Volatile-Dependencies.md)
- [Big Ball of Mud](Big-Ball-of-Mud.md) (result of violating dependency rules)
- [Inheritance](Inheritance.md) (mentioned as strongest dependency)
- [DTO - Data Transfer Object](DTO-Data-Transfer-Object.md) (used for boundary crossing)