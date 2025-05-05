# Code Quality and Design

## Introduction

This document is a practical guide to help with software development mentoring and code review. It is a selected
collection of principles, patterns, and examples I have gathered over the years. It's not meant to be a complete
textbook that covers everything.

This guide focuses on specific topics that I believe are very important for real coding work. Sometimes, these topics
are not focused on in schools or other learning materials.

A key example is the idea of "preferring composition over inheritance." Many developers are taught inheritance
extensively, but understanding when and why to use composition instead is crucial for building flexible and maintainable
software.

Many ideas here come from books and articles I have read (you will see the sources mentioned).

## Table of Contents

*   Code Quality Design
    *   [Motivation for Good Design](Motivation-for-Good-Design.md)
    *   [Code Review Checklist](Code-Review-Checklist.md)
    *   Object Oriented Programming
        *   [Pillars of OOP](Pillars-of-OOP.md)
            *   [Pillars of OOP Encapsulation Abstraction](Pillars-of-OOP-Encapsulation-Abstraction.md)
                *   [Minimize Accessibility of Classes and Members](Minimize-Accessibility-of-Classes-and-Members.md)
            *   [Inheritance](Inheritance.md)
            *   [Polymorphism](Polymorphism.md)
            *   [Composition](Composition.md)
            *   [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md)
        *   Core Design Principles
            *   [DRY Principle](DRY-Principle.md)
            *   [ETC Principle](ETC-Principle.md)
            *   [Program to an interface; not an Implementation (GOF 18)](Program-to-an-interface-not-an-Implementation-GOF-18.md)
            *   [Find What Is Varying and Encapsulate It (Shalloway 123)](Find-What-Is-Varying-and-Encapsulate-It-Shalloway-123.md)
            *   [SOLID Design Principles](SOLID-Design-Principles.md)
                *   [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md)
                *   [Open Closed Principle (OCP)](Open-Closed-Principle-OCP.md)
                *   [Liskov Substitution Principle (LSP)](Liskov-Substitution-Principle-LSP.md)
                    *   [Avoiding Liskov Substitution Principal Violations in Type Hierarchies](Avoiding-Liskov-Substitution-Principal-Violations-in-Type-Hierarchies.md)
                *   [Interface Segregation Principle (ISP)](Interface-Segregation-Principle-ISP.md)
                *   [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
            *   [Prefer Composition over Class Inheritance](Prefer-Composition-over-Class-Inheritance.md)
                *   [Inheritance vs Composition Analogy](Inheritance-vs-Composition-Analogy.md)
        *   Design Patterns
            *   [The Facade Pattern with the Single Responsibility Principle](Facade-Pattern-with-the-Single-Responsibility-Principle.md)
            *   [Decorator Pattern](Decorator-Pattern.md)
            *   [Strategy Pattern Implementation for JSON Processing](Strategy-Pattern-Implementation-for-JSON-Processing.md)
        *   Dependencies and Coupling
            *   [Stable vs Volatile Dependencies](Stable-vs-Volatile-Dependencies.md)
            *   [The Dependency Rule](The-Dependency-Rule.md)
            *   [Using the new keyword](Using-the-new-keyword.md)
            *   [Design Relationships](Design-Relationships.md)
            *   [Relationship between Dependency Inversion, Dependency Injection, Inversion of Control and SOLID](Relationship-between-Dependency-Inversion-Dependency-Injection-Inversion-of-Control-and-SOLID.md)
        *   Implementation Techniques and Heuristics
            *   [Memory Allocation and Management Reference Types Value Types Arrays and Linked Lists](Memory-Allocation-and-Management-Reference-Types-Value-Types-Arrays-and-Linked-Lists.md)
            *   [Evolving Configuration Interfaces From Boolean Flags to Expressive Design](Evolving-Configuration-Interfaces-From-Boolean-Flags-to-Expressive-Design.md)
            *   [Static Methods](Static-Methods.md)
            *   [Self Validating Value Objects in C](Self-Validating-Value-Objects-in-C.md)
            *   [Some Object Oriented Design Heuristics Riel updated](Some-Object-Oriented-Design-Heuristics-Riel-updated.md)
    *   Functional Programming
        *   [Imperative vs Functional Declarative Expressive Style of Programming](Imperative-vs-Functional-Declarative-Expressive-Style-of-Programming.md)
        *   [Functional in the Small and Object Oriented in the Large](Functional-in-the-Small-and-Object-Oriented-in-the-Large.md)
        *   [Prefer Dictionary over Switch Statement](Prefer-Dictionary-over-Switch-Statement.md)
        *   [Using SelectMany](Using-SelectMany.md)
        *   [Introduction to Result T](Introduction-To-Result-T.md)
        *   [Higher Order Function Examples](Higher-Order-Function-Examples.md)
        *   [Transitioning from Classes to Records and Adopting IImmutableList in C](Transitioning-from-Classes-to-Records-and-Adopting-IImmutableList-in-C.md)
        *   [Function composition and method chaining](Function-composition-and-method-chaining.md)
        *   [Defensive Copying in C Example](Defensive-Copying-in-C-Example.md)
        *   [Partial Application Example](Partial-Application-Example.md)
    *   [Concurrency](Concurrency.md)
        *   [Understanding Threads in Programming A Restaurant Analogy](Understanding-Threads-in-Programming-A-Restaurant-Analogy.md)
        *   [Understanding the Thread Pool in NET](Understanding-the-Thread-Pool-in-NET.md)
    *   Performance
        *   [Utilizing Record Structs for Enhanced Performance in NET](Utilizing-Record-Structs-for-Enhanced-Performance-in-NET.md)
        *   [Maximizing Performance and Readability with const Variables in C](Maximizing-Performance-and-Readability-with-const-Variables-in-C.md)
        *   [Exploring Performance with New Collection Expression Syntax in C 12](Exploring-Performance-with-New-Collection-Expression-Syntax-in-C-12.md)
    *   Language Specifics (C#/.NET)
        *   [Using Required Members in C 11](Using-Required-Members-in-C-11.md)
        *   [var keyword](var-keyword.md)
        *   [When to use Target typed new expressions](When-to-use-Target-typed-new-expressions.md)
        *   [Difference between const and static readonly](Difference-between-const-and-static-readonly.md)
    *   Anti-Patterns and Code Smells
        *   [Ambient Context](Ambient-Context.md)
        *   [Naming Examples](Naming-Examples.md)
        *   [Big Ball of Mud](Big-Ball-of-Mud.md)
        *   [Using Exceptions for Control Flow](Using-Exceptions-for-Control-Flow.md)
        *   [High Complexity](High-Complexity.md)
        *   [Explicit case analysis switch if else statement](Explicit-case-analysis-switch-if-else-statement.md)
    *   Testing
        *   [Finding Test Cases](Finding-Test-Cases.md)
        *   [Code Coverage](Code-Coverage.md)
        *   [How do we write a test for a private method](How-do-we-write-a-test-for-a-private-method.md)
        *   [Seam Interface](Seam-Interface.md)
        *   [Comparing Objects for Equality in C Using JSON Serialization](Comparing-Objects-for-Equality-in-C-Using-JSON-Serialization.md)
    *   Python
        *   [Immutable DTOs in Python](Immutable-DTOs-in-Python.md)
    *   Glossary
        *   [Cognitive Complexity](Cognitive-Complexity.md)
        *   [Cohesion](Cohesion.md)
        *   [Composition Root](Composition-Root.md)
        *   [Coupling](Coupling.md)
        *   [Cyclomatic Complexity](Cyclomatic-Complexity.md)
        *   [DTO Data Transfer Object](DTO-Data-Transfer-Object.md)
        *   [Dependency Injection DI](Dependency-Injection-DI.md)
        *   [Interception](Interception.md)
        *   [Inversion of Control IoC](Inversion-of-Control-IoC.md)
        *   [Modularity Module](Modularity-Module.md)
        *   [POCO Plain Old CLR Objects](POCO-Plain-Old-CLR-Objects.md)
    *   Appendices
        *   [OOP Design Quotes](OOP-Design-Quotes.md)
        *   [References](References.md)