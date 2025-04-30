# Quiz: Single Responsibility Principle (SRP)

These questions are based on the content provided in the article "**Single Responsibility Principle (SRP)**".

## Multiple Choice Questions

**Question 1:**
According to the article, what is the most accurate definition of the Single Responsibility Principle (SRP)?

*   [ ] Every method should perform only one, small, specific task.
*   [x] A class or component should have only one reason to change, meaning it should be responsible to only one actor or stakeholder.
*   [ ] Classes should depend on abstractions, not on concretions.
*   [ ] A class should primarily focus on data encapsulation.

**Question 2:**
The article explicitly warns against a common misunderstanding of SRP. What is this misunderstanding?

*   [ ] That SRP only applies to large enterprise systems.
*   [ ] That SRP is the same as the DRY (Don't Repeat Yourself) principle.
*   [x] That SRP means every module (class or method) should "do only one thing" functionally, rather than having only one *reason to change*.
*   [ ] That SRP prevents the use of inheritance.

**Question 3:**
In the article's example violation, the `Employee` class has methods `calculatePay()`, `reportHours()`, and `save()`. Why does this violate SRP?

*   [ ] The methods are too complex.
*   [ ] The class has too many methods.
*   [ ] The class manages employee data, which is inherently complex.
*   [x] The methods serve different actors/departments (Accounting/CFO, HR/COO, DBAs/CTO) who represent different reasons for the class to change.

**Question 4:**
The article suggests two possible solutions for the SRP violation example. One involves separating the responsibilities into distinct classes. What is the other solution mentioned?

*   [ ] Rewriting the `Employee` class to use the Strategy pattern.
*   [ ] Making all methods in the `Employee` class static.
*   [x] Using the Facade pattern to provide a unified interface over the separated responsibilities.
*   [ ] Combining the `Employee` class with other related classes.

**Question 5:**
The article states that SRP is based on likely patterns of change. What does this imply?

*   [ ] SRP is only relevant when code changes frequently.
*   [x] Identifying responsibilities requires thinking about *who* or *what* might request changes to a piece of code in the future.
*   [ ] SRP primarily relates to optimizing code performance.
*   [ ] SRP means grouping code based on functional similarity alone.

**Question 6:**
According to the article, how can a Facade pattern adhere to SRP despite having methods that appear to serve multiple actors?

*   [ ] It cannot—Facade patterns inherently violate SRP.
*   [ ] By making all methods static to remove state.
*   [x] Its single responsibility is coordination—delegating to specialized classes rather than implementing complex logic itself.
*   [ ] By limiting the number of methods to fewer than five.

## Free Response Questions

**Short Answer Question 1:**
The article quotes Robert C. Martin: "Gather together those things that change at the same time and for the same reasons. Separate those things that change at different times or for different reasons." In your own words, explain how this quote captures the essence of SRP.

> **Answer:** This quote emphasizes that SRP isn't about the *actions* a class performs, but about the *sources of change*. If multiple distinct user groups, stakeholders, or requirements (the "reasons") might independently require modifications to a class, those different responsibilities should be separated. Code related to a single responsibility (one reason for change) should be grouped together.

**Short Answer Question 2:**
Based on the article, why is the name "Single Responsibility Principle" considered potentially misleading?

> **Answer:** The name is potentially misleading because it's easy to interpret "responsibility" as "task" or "function." This leads programmers to incorrectly assume it means every class or method should just "do one thing." The article clarifies the principle is actually about having a single *source* or *reason* for change (a single "actor" to be responsible to).

**Short Answer Question 3:**
According to the article, how does adhering to SRP contribute to better software design and maintenance?

> **Answer:** Adhering to SRP contributes to better design by decoupling unrelated concerns. When a change is required by one actor or for one reason, it doesn't risk breaking functionality related to other actors/reasons contained within the same class. This makes the code easier and safer to modify, reduces the chance of merge conflicts, and improves overall maintainability.