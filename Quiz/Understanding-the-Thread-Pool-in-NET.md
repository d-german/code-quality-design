**1. What is the main advantage of using .NET’s Thread Pool?**

- [ ] Provides dedicated threads for each long-running operation by default.
- [ ] Automatically ensures threads complete tasks within 100 ms.
- [x] Minimizes overhead from creating and destroying threads.
- [ ] Eliminates the need for any synchronization primitives.

---

**2. Which of the following is *not* true about pooled threads in .NET?**

- [ ] They always run as background threads.
- [ ] Their priority resets to normal after use.
- [x] You can set a persistent thread name at runtime.
- [ ] You can attach a debug description in Visual Studio.

---

**3. Which conditions optimize CLR thread pool performance?**

- [ ] Tasks should be mostly short-running (≤ 100 ms).
- [ ] Jobs spending most time blocked should not dominate the pool.
- [x] Both of the above.
- [ ] None of the above.

---

**4. For CPU-bound tasks that cannot be broken into smaller pieces, the document recommends ____.**

- [ ] Offloading them to the thread pool via `Task.Run`.
- [ ] Converting them into asynchronous file operations.
- [x] Running them on a dedicated thread outside the pool.
- [ ] Using PLINQ to automatically partition work.

---

**5. When handling long-running I/O-bound operations, you should ____.**

- [x] Use `async`/`await` to free up threads during I/O.
- [ ] Wrap blocking I/O calls in PLINQ for parallelism.
- [ ] Execute them on a dedicated thread via `Thread`.
- [ ] Block threads until each I/O operation completes.

---

### Open-Response Questions

**Open 1.** Explain why maintaining short-running tasks and avoiding blocked threads is critical for thread pool hygiene.

*Example answer:*  
Keeping tasks under about 100 ms lets the CLR measure CPU performance accurately, and preventing blocked threads from dominating avoids oversaturating the pool with idle threads.

---

**Open 2.** Compare how you would handle a long-running CPU-bound task versus a long-running I/O-bound task in .NET.

*Example answer:*  
For CPU-bound work, run on a dedicated thread or use PLINQ to split into short tasks; for I/O-bound work, use async/await so threads aren’t blocked waiting on I/O.
