# Quiz: DRY Principle

These questions are based on the content provided in the article "**DRY Principle**".

## Multiple Choice Questions

**Question 1:**
According to the article "**DRY Principle**", what is the core definition of the DRY principle?

*   [ ] Code should always be as short as possible.
*   [ ] Every function should do only one thing.
*   [x] Every piece of knowledge must have a single, unambiguous, authoritative representation within a system.
*   [ ] Classes should be open for extension but closed for modification.

**Question 2:**
The article cites Robert C. Martin (2008) on the primary problem with duplication. What is this problem?

*   [ ] It makes code harder to read initially.
*   [ ] It violates the Single Responsibility Principle.
*   [x] It bloats the code and requires multiple changes if the logic needs updating, risking inconsistency.
*   [ ] It prevents the use of polymorphism.

**Question 3:**
Which of the following is mentioned in the article as an alias for the DRY principle?

*   [ ] Keep It Simple, Stupid (KISS)
*   [x] Once and Only Once rule
*   [ ] Law of Demeter
*   [ ] Liskov Substitution Principle

**Question 4:**
The article "**DRY Principle**" introduces the concept of "False or accidental duplication". What characterizes this type of duplication?

*   [ ] It occurs only in test code, not production code.
*   [ ] It is duplication introduced by automated code generation tools.
*   [x] The duplicated sections look similar now but evolve differently over time because they represent different underlying concepts.
*   [ ] It is duplication that exists across different programming languages.

**Question 5:**
According to Robert C. Martin (2017), as cited in the article, what should a developer do before eliminating apparent duplication?

*   [ ] Always extract the duplication into a separate function immediately.
*   [ ] Rewrite the code using a different algorithm.
*   [x] Make sure the duplication is real (true duplication) and not accidental.
*   [ ] Add comments explaining the duplication.

**Question 6:**
The article suggests a pragmatic reason why the DRY principle might *not* apply or could be violated. What is this reason?

*   [ ] When the duplicated code is very short (e.g., one line).
*   [x] When the probability of the logic changing is very low AND eliminating the duplication adds more complexity/code than it saves.
*   [ ] When working on legacy code that is difficult to refactor.
*   [ ] When performance is the absolute highest priority.

**Question 7:**
What was the specific purpose of the duplicated TypeScript code snippet provided as an example in the article?

*   [ ] To validate an email address format.
*   [ ] To connect to a database.
*   [x] To check if an HTTP status code represents success (200-299).
*   [ ] To sort an array of numbers.

## Free Response Questions

**Short Answer Question 1:**
In your own words, based on the article "**DRY Principle**", explain the difference between "True duplication" and "False or accidental duplication".

> **Answer:** True duplication means two or more pieces of code represent the exact same knowledge or logic, such that a change to one *must* always be identically applied to the others. False or accidental duplication refers to code sections that look similar currently but represent different concepts or requirements that are likely to change independently in the future.

**Short Answer Question 2:**
According to the article "**DRY Principle**", why is adhering to the DRY principle generally beneficial for software maintenance?

> **Answer:** Adhering to DRY is beneficial because it ensures that a specific piece of logic or knowledge exists in only one place. When a change is needed, it only needs to be made in that single location, reducing the effort required and minimizing the risk of introducing inconsistencies by forgetting to update one or more duplicated instances.

**Short Answer Question 3:**
Describe the reasoning provided in the article's example for *not* eliminating the duplicated `isSuccessful` check in TypeScript, despite it violating the DRY principle on the surface.

> **Answer:** The reasoning was that eliminating the duplication (e.g., by creating a shared utility function/class) would introduce extra code, potentially add unwanted dependencies between modules, and the specific logic (`statusCode >= 200 && statusCode < 300`) was considered highly unlikely to ever change. Therefore, the cost and complexity of the abstraction outweighed the perceived benefit of removing this stable, simple duplication.