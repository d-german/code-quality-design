# Code Review Checklist

Based on team discussions of *Clean Code* by Robert C. Martin

> "Be consistent with the existent"

> "Leave code cleaner than you found it"

## Functions

*   Are my functions short, simple, and doing one thing?
*   Do my new functions take too many parameters? Can they be marked `static`?
*   Do I have any functions that take boolean parameters that should be split into 2 functions instead?
*   Have I removed any unused functions?
*   Are my private methods right after the public method where they're used?
*   Have I made my function `static` if it does not reference class instance fields?

## Variables

*   Are there any temporary variables I can remove and inline? Or any inline variables I should break up to improve readability?
*   Am I using `var` in a repository that specifies types, or specifying types in a repository that uses `var`? (Maintain consistency)
*   Do I have any magic strings / numbers that should be replaced with constants?
*   Are there any variables I can mark `const` or `readonly`?

## Conditionals

*   Are there any complicated conditionals I can simplify or encapsulate into their own method?
*   Are there any negative conditionals I can make positive?
*   Am I using a `switch` statement that can be refactored (use polymorphism instead)?

## Comments

*   Did I add unnecessary comments? Are there existing comments that I can remove?
*   Did I make changes without updating the existing comments?
*   Did I remove all commented-out code?

## General

*   Is there any duplicated code I can extract and reuse? (DRY - Don't Repeat Yourself)
*   Did I run Code Coverage Analysis on new methods / classes?
*   If using Linq, have I chosen the correct / most performant methods?
*   Have I removed any unused imports / `using` statements?
*   Is my formatting consistent with the existing code (spacing, capitalization style, etc)?

## Immutability

*   Are we using `init` for properties whenever possible?
*   Are there any variables I can mark `const` or `readonly`? (Duplicated from Variables - consider consolidating if desired)
*   Are we using immutable collections when possible?

Note this was authored by a coworker named Josie

---
See Also:
- [SOLID Design Principles](SOLID-Design-Principles.md)
- [Cohesion](Cohesion.md)
- [Coupling](Coupling.md)
<!--- [DRY Principle](Empty-MD-Topic.md)  TODO add dry article -->
- [Code Coverage](Code-Coverage.md)
- [Static Methods](Static-Methods.md)
- [Difference between const and static readonly](Difference-between-const-and-static-readonly.md)
- [Maximizing Performance and Readability with const Variables in C#](Maximizing-Performance-and-Readability-with-const-Variables-in-C.md)
- [Transitioning from Classes to Records and Adopting Immutable Collections in C#](Transitioning-from-Classes-to-Records-and-Adopting-IImmutableList-in-C.md)
- [Explicit case analysis (switch/if-else statement)](Explicit-case-analysis-switch-if-else-statement.md)
- [Prefer Dictionary to Switch Statement](Prefer-Dictionary-over-Switch-Statement.md)
- [Imperative vs Functional (Declarative, Expressive) Style of Programming](Imperative-vs-Functional-Declarative-Expressive-Style-of-Programming.md)
- [var keyword](var-keyword.md)
- [Using Required Members in C# 11](Using-Required-Members-in-C-11.md)