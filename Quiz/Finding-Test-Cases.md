**Multiple-Choice Questions**

**1. What is the main advantage of crossing out impossible or irrelevant rows in a truth table when designing tests?**

- [ ] It guarantees full branch coverage for every path.
- [x] It limits test creation to only meaningful scenarios.
- [ ] It proves the underlying code has zero defects.
- [ ] It doubles the total number of test cases.

---

**2. In the `Branch` example, why does the test case `(a = true, b = true)` reach 100 % statement coverage but only 33 % decision coverage?**

- [ ] The method includes unreachable dead code.
- [ ] Variable `b` is never evaluated in any branch.
- [x] Other combinations of branch outcomes are not exercised.
- [ ] The test uses an incorrect expected value.

---

**3. A test model contains three inputs: size (3 values), age (2 values), and color (2 values). Before applying constraints, how many potential test cases exist?**

- [ ] 6
- [ ] 9
- [x] 12
- [ ] 18

---

**4. Which statement best describes the purpose of equivalence partitioning?**

- [ ] Group input values so each class behaves differently.
- [ ] Ensure every individual value is tested at least once.
- [ ] Focus testing only on boundary values.
- [x] All of the above statements together capture the goal.

---

**5. When selecting representative values from each equivalence class, which set should normally be included?**

- [ ] Only the most common valid values.
- [ ] Only extreme invalid values.
- [x] Both the happy-path values and error-path values.
- [ ] Randomly generated values with no constraints.

---

**6. After pruning test branches with domain constraints, what is the primary benefit of redrawing the mind-map tree?**

- [ ] It hides unnecessary branches from auditors.
- [x] It visually tracks the reduced set of cases and testing progress.
- [ ] It increases the branch count for better coverage metrics.
- [ ] None of the above.

---

**Open-Response Questions**

**7. Explain the difference between statement coverage and branch (decision) coverage, using the `Branch` method as an example.**  
*Example response (concise):*  
Statement coverage counts whether each line executes at least once; one test with `a=true, b=true` executes every line. Branch coverage checks whether every possible true/false outcome of each decision executes; the same test misses paths where `a` is false or `b` is false, so it achieves only one-third of the decision outcomes.

---

**8. Describe how a mind-mapping tool can help manage combinatorial test cases for inputs like size, age, and color.**  
*Example response (concise):*  
The tester creates branches for each input and appends their possible values, producing a visual tree whose leaf count equals potential cases. Constraints can quickly be applied by deleting branches, and completed leaves can be marked, giving an up-to-date picture of remaining work and coverage.

---

**9. For an integer variable `month` with a valid range of 1–12, list three equivalence classes and give one representative test value for each.**  
*Example response (concise):*
- Valid lower-bound range (1–12): test with 1.
- Invalid below range (<1): test with 0.
- Invalid above range (>12): test with 13.  
  These values respectively exercise the happy path and two error paths without redundant cases.
