# DRY Principle

Every piece of knowledge must have a single, unambiguous, authoritative representation within a system. (Hunt 27)

Duplication may be the root of all evil in software. Many principles and practices have been created for the purpose of
controlling or eliminating it. (2008 Martin 48)

**AKA:**

- One rule, one place (Shalloway 131)
- Once and Only Once rule (Beck 108)

Duplication is a problem because it bloats the code and will require multiple changes should the algorithm ever have to
change. If you change one, you have to remember to change the others. (2008 Martin 48)

## Reasons to Violate the DRY Principle

There are different kinds of duplication:

- **True duplication:** Every change to one instance causes the same change to every duplicate of that instance.
- **False or accidental duplication:** If two apparently duplicated sections of code evolve along different paths—if
  they change at different rates, and for different reasons—then they are not true duplicates. Return to them in a few
  years, and you’ll find that they diverge from each other.

Be careful. Resist the temptation to commit the sin of the knee-jerk elimination of duplication. Make sure the
duplication is real. (2017 Martin 154)

Since causing code bloat and/or needing to update multiple places if the algorithm changes are the reasons to follow the
DRY principle, it seems that if there is a very low probability that an algorithm will change and it requires more lines
of code to not have the duplication, then the DRY principle does not apply.

## Example

For example, this particular line of code is repeated across multiple modules in a TypeScript-based system. Since
TypeScript does not offer extension methods as C# does, and there is no globally accessible utility class for
consolidating such methods, eliminating the duplication by introducing a utility class or a base class would add extra
code and dependencies. Moreover, given that the method is unlikely to require future changes, the duplication is
considered acceptable.


```typescript
static readonly isSuccessful = (statusCode: number) => statusCode >= 200 && statusCode < 300;

```
