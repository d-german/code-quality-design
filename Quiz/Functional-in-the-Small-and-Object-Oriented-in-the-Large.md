**1. Which statement best defines a *pure function*?**

- [ ] Returns a value while mutating external program state.
- [x] Always returns the same value and has no side effects.
- [ ] Returns the same value but writes a log entry each call.
- [ ] Selects a value based on the current system clock.

---

**2. Why can uncontrolled side effects in a function be troublesome?**

- [ ] They invariably double the function’s execution time.
- [ ] They restrict the use of generic type parameters in code.
- [ ] They only affect private local variables inside the function.
- [x] They may create unintended external changes each time the function runs.

---

**3. A function whose result varies with *when* or *how many times* it is invoked is classified as a ____.**

- [x] Action.
- [ ] Calculation.
- [ ] Data.
- [ ] None of the above.

---

**4. The literal collection `[1, 2, 3]` is treated as which category in functional thinking?**

- [x] Data.
- [ ] Actions.
- [ ] Calculations.
- [ ] None of the above.

---

**5. In C#, a `with` expression primarily enables developers to ____.**

- [x] Create a modified copy without changing the original instance.
- [ ] Enforce compile‑time immutability on reference types.
- [ ] Serialize objects into JSON format automatically.
- [ ] Allocate objects on the stack instead of the heap.

---

**6. What is a key reason functional programmers favor immutable data?**

- [ ] It lowers disk‑space requirements for large datasets.
- [ ] It removes the need for defensive copying entirely.
- [ ] It guarantees faster CPU branch prediction.
- [x] It prevents hidden state changes, easing reasoning, testing, and maintenance.  
  *(“All of the above” would also be correct if every listed benefit applied,  
  but only the marked option fits the section content.)*

---

**7. After the logging call is removed, `BuildErrorResponse` can *still* be an Action because ____.**

- [ ] lazy evaluation defers computation.
- [x] mutability leaks purity if `WeatherForecast` can be changed elsewhere.
- [ ] the function returns an anonymous type.
- [ ] defensive copying was used incorrectly.

---

**8. Which of the following is *not* first‑class in C#?**

- [ ] A `Func<int,int>` delegate variable.
- [ ] A `string` literal value.
- [ ] An `int` numeric constant.
- [x] The `if` keyword itself.

---

**9. Which description correctly characterizes a *higher‑order function*?**

- [ ] A function that executes in parallel on multiple threads.
- [x] A function that accepts one or more functions or returns a function.
- [ ] A function that only uses primitive numeric types.
- [ ] A function that performs tail‑call recursion exclusively.

---

**10. What core purpose does a `Map` operation serve on a collection?**

- [ ] It filters elements according to a Boolean predicate.
- [ ] It collapses elements into a single accumulated result.
- [x] It transforms each element and returns a new collection of equal length.
- [ ] It guarantees elements are processed concurrently.

---

**11. A typical `Reduce` implementation requires which pair of inputs?**

- [ ] A mapping function and a collection iterator.
- [ ] A comparator and a sorting key.
- [ ] A cancellation token and a buffer size.
- [x] A seed value and an accumulator function.

---

### Open‑Response Questions

**Open 1.** Explain why functional programmers prefer *calculations* over *actions*.

*Example answer:* Calculations are deterministic and side‑effect‑free, so they are  
simpler to test, reason about, and parallelize.

---

**Open 2.** Describe the *copy‑on‑write* technique and how it supports immutability.

*Example answer:* Copy‑on‑write makes a fresh copy of data before modifications,  
leaving the original unchanged, thus preserving immutability guarantees.

---

**Open 3.** Define a higher‑order function and give one concrete C# example.

*Example answer:* A higher‑order function takes a function as a parameter or  
returns one; e.g., LINQ’s `Select` accepts a lambda to transform each element.
