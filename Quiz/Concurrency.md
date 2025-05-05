**1. Concurrency enables an application to____.**

- [ ] process a single instruction on multiple CPUs at once.
- [x] perform more than one logically separate task at the same time.
- [ ] guarantee faster execution of every individual operation.
- [ ] avoid the need for synchronization primitives entirely.

---

**2. Which pair of techniques are explicitly listed as higher‑level concurrency abstractions beyond “raw”
multithreading?**

- [ ] Distributed computing and memory‑mapped files.
- [ ] Garbage collection and just‑in‑time compilation.
- [x] Parallel processing and asynchronous programming.
- [ ] Reflection and dynamic code generation.

---

**3. In an `async` method returning a value, what should the declared return type be?**

- [ ] `void`.
- [ ] `ValueTask`.
- [ ] `IAsyncEnumerable<T>`.
- [x] `Task<T>`.

---

**4. What is the primary purpose of `ConfigureAwait(false)` in library or server code?**

- [x] Skip resuming on the original synchronization context to reduce overhead.
- [ ] Enable speculative execution on multiple cores automatically.
- [ ] Force the continuation to run on the UI thread.
- [ ] Combine multiple `Task` objects into a single pipeline.

---

**5. Which practice most commonly leads to deadlocks in GUI applications using async methods?**

- [ ] Throwing exceptions inside `async` lambdas.
- [ ] Calling `ConfigureAwait(false)` for UI updates.
- [x] Blocking on `Task.Result` or `Task.Wait` instead of `await`.
- [ ] Capturing a `CancellationToken` by value.

---

**6. Why should long‑running operations accept a `CancellationToken` parameter?**

- [ ] To guarantee lower memory use in all cases.
- [x] To allow cooperative termination when the operation is no longer needed.
- [ ] To force the runtime to create a dedicated thread per task.
- [ ] To avoid the overhead of exception handling.

---

**7. Which scenario best fits the use of *data parallelism*?**

- [ ] Parsing one very large XML document that must be read sequentially.
- [ ] Handling unrelated user requests over HTTP.
- [x] Rotating thousands of independent matrices by a fixed angle.
- [ ] Streaming a video while downloading subtitles.

---

**8. When multiple exceptions are thrown inside `Parallel.Invoke`, they are____.**

- [ ] lost; only the first exception is reported.
- [ ] wrapped in an `OperationCanceledException`.
- [x] aggregated into a single `AggregateException` instance.
- [ ] automatically rethrown on the main thread one by one.

---

**9. What resource do *all* threads in a single .NET process share?**

- [ ] Their individual stacks.
- [x] The process’s address space (heap memory).
- [ ] The current culture settings of the OS.
- [ ] A unique hardware interrupt vector.

---

**10. Stephen Cleary’s phrase “There is no thread” emphasizes that asynchronous work____.**

- [ ] always runs on a background thread pool thread.
- [ ] cannot be used inside web servers.
- [ ] must yield control every 15 milliseconds.
- [x] does not inherently imply creating or switching threads at all.

---

**11. Which .NET asynchronous pattern is *task‑based* and forms the foundation of `async`/`await`?**

- [ ] Event‑based Asynchronous Pattern (EAP).
- [x] Task‑based Asynchronous Pattern (TAP).
- [ ] Asynchronous Programming Model (APM).
- [ ] Reactive Extensions (Rx).

---

**12. Which of the following uses of `Task.Run` aligns with the guidance in the text?**

- [ ] Wrapping a synchronous SQL query inside library code so callers can `await` it.
- [ ] Converting every method in an ASP.NET controller to use `Task.Run` for “scalability.”
- [x] Off‑loading a CPU‑bound image‑processing algorithm from the UI thread.
- [ ] Hiding network latency by running I/O‑bound work on the thread pool.

---

**13. *All of the following* are cited as benefits of asynchronous programming **except**____.**

- [ ] Keeping GUI applications responsive.
- [ ] Improving scalability on the server.
- [ ] Avoiding unnecessary thread creation overhead.
- [x] Guaranteeing maximum CPU utilization on compute‑heavy tasks.

---

### Open‑Response Questions

** 1.** Describe a concrete situation where combining asynchronous programming with parallel processing in the same
application brings clear advantages.

*Example answer:* A web server that *awaits* database queries without blocking request threads and also processes large
image uploads in parallel chunks—keeping I/O‑bound paths responsive while accelerating CPU‑bound work.

---

** 2.** Explain why library authors should avoid embedding `Task.Run` inside their reusable APIs.

*Example answer:* It hides thread management decisions from callers, can waste thread‑pool resources, complicates
testing, and might degrade scalability; consumers should decide when CPU off‑loading is appropriate for their context.
```
