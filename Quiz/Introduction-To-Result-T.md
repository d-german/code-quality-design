**1. Which two states are encapsulated by the `Result<T>` pattern?**

- [ ] Value and ErrorDetails types.  
- [ ] Success and Error outcomes.  
- [x] Success and Failure states.  
- [ ] Ok and Fail helper methods.  

**2. Which type represents a successful outcome in the `Result<T>` implementation?**

- [ ] Result<T>  
- [ ] Failure<T>  
- [x] Success<T>  
- [ ] ErrorDetails  

**3. What is the primary purpose of the `SafeExecute` helper in the pipeline methods?**

- [ ] To log exception details for debugging purposes.  
- [ ] To optimize performance by avoiding unnecessary checks.  
- [x] To catch exceptions and convert them into `Failure` results.  
- [ ] To aggregate multiple errors into a single result.  

**4. When `Map` is invoked on a `Failure<T>` result, what occurs?**

- [ ] It throws an `InvalidOperationException` at runtime.  
- [x] It bypasses the transform and propagates the failure.  
- [ ] It returns a new `Success` with a default value.  
- [ ] It logs the error and returns `null`.  

**5. How does `FlatMap` differ from `Map` in the `Result<T>` pipeline?**

- [ ] FlatMap applies a transform function while Map flattens nested results.  
- [ ] Both FlatMap and Map behave identically in handling nested results.  
- [x] FlatMap flattens nested results while Map applies a transform function.  
- [ ] FlatMap only handles successes while Map only handles failures.  

**6. Which of the following is a benefit of using the `Result<T>` pattern?**

- [ ] Explicit outcomes that require callers to handle success and failure.  
- [ ] Composability of complex workflows through chaining.  
- [ ] Simplified error logic by reducing nested try-catch blocks.  
- [x] All of the above.  

**7. Which extension method is specifically designed to flatten nested `Result` objects?**

- [ ] Map.  
- [ ] MapAsync.  
- [x] FlatMap.  
- [ ] None of the above.  

**8. Explain how the `Result<T>` pattern improves error handling compared to using exceptions or null returns.**

Example response: It makes failures explicit, avoids throwing exceptions for expected errors, and forces callers to handle success or failure states explicitly.

**9. Describe how `Map` and `FlatMap` enable composability in `Result<T>` pipelines, and give an example of when you would use each.**

Example response: Map applies a pure function to a successful value, while FlatMap chains operations that return a `Result`, flattening nested results; use Map to transform data, and FlatMap to sequence operations that may fail.

**10. Discuss the separation of state and behavior in the `Result<T>` pattern and why this is beneficial.**

Example response: State types (`Success`/`Failure`) hold data, while extension methods implement pipeline behavior, improving clarity by decoupling outcome representation from operation logic.

