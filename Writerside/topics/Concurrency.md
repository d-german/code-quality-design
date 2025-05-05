# Concurrency

Concurrency is a fundamental aspect of software development, allowing multiple tasks to be executed simultaneously.
Despite its historical complexity, modern .NET programs and advancements in development tools (such as Visual Studio
2012 and later) have made concurrency more accessible to all developers—not just experts.

---

## Introduction to Concurrency

Concurrency is the ability to do more than one thing at a time. It's essential for applications that need to perform
multiple tasks simultaneously, such as responding to user input while writing to a database or handling multiple server
requests.

Most developers associate concurrency with multithreading, which uses multiple threads of execution. However,
multithreading is just one form of concurrency. Modern applications often use higher-level abstractions that are more
powerful and efficient than traditional multithreading techniques.

- **Parallel Processing** divides work among multiple threads running concurrently, maximizing the use of multiple
  processors.
- **Asynchronous Programming** uses futures or callbacks to avoid unnecessary threads. By starting an operation that
  completes later without blocking the original thread, asynchronous programming has been made almost as simple as
  synchronous programming with the introduction of async and await.
- **Reactive Programming** is a declarative style where the application reacts to events. It builds on asynchronous
  events rather than traditional operations.

In practice, a concurrent program may use a mix of these techniques—multithreading, asynchronous programming, and
parallel processing—depending on the application's requirements.

---

## Introduction to Asynchronous Programming

Asynchronous programming offers two main benefits: improved responsiveness in GUI applications and increased scalability
in server-side applications. Modern asynchronous .NET applications use the `async` and `await` keywords. An `async`
method should return `Task<T>` if it returns a value, or `Task` if it does not. These task types act as futures,
notifying the caller when the operation completes.

### Example: Basic Async Method

```C#
async Task DoSomethingAsync()
{
    int val = 13;
    // Asynchronously wait 1 second.
    await Task.Delay(TimeSpan.FromSeconds(1));
    val *= 2;
    // Asynchronously wait 1 second.
    await Task.Delay(TimeSpan.FromSeconds(1));
    Trace.WriteLine(val);
}
```

An async method begins executing synchronously. The `await` keyword pauses execution if the awaited operation is not
complete, returning an incomplete task. When the operation completes, the async method resumes execution in the captured
context.

### Example: Using ConfigureAwait to Avoid Capturing the Context

When you use `await` in an async method, by default it captures the current synchronization context (such as the UI
thread in a GUI application or the request context in ASP.NET). This ensures that code after the `await` runs in the
same context as before the await. While helpful for UI updates, this context capturing can cause performance overhead
and potential deadlocks in library code.
`ConfigureAwait(false)` tells the runtime not to return to the original context after the awaited task completes. This
improves performance by avoiding unnecessary context switches and prevents potential deadlocks in libraries that don't
need to interact with the original context.

```C#
async Task DoSomethingAsync()
{
    int val = 13;
    // Asynchronously wait 1 second without capturing the current context.
    await Task.Delay(TimeSpan.FromSeconds(1)).ConfigureAwait(false);
    val *= 2;
    // Asynchronously wait 1 second without capturing the current context.
    await Task.Delay(TimeSpan.FromSeconds(1)).ConfigureAwait(false);
    Trace.WriteLine(val.ToString());
}
```

Use `ConfigureAwait(false)` in library code and server applications where context doesn't matter. For UI applications,
maintain the default behavior when you need to update UI elements after an async operation.

### Error Handling in Async Methods

When an async method throws an exception, the exception is stored in the returned `Task`. Awaiting that task will
rethrow the exception while preserving the original stack trace.

```C#
async Task TrySomethingAsync()
{
    try
    {
        await PossibleExceptionAsync();
    }
    catch (NotSupportedException ex)
    {
        LogException(ex);
        throw;
    }
}
```

It is important to await async methods rather than blocking on them with `Task.Wait` or `Task<T>.Result`, as doing so
can lead to deadlocks.

For more details on async programming, refer to resources such as "Async in C# 5.0" by Alex Davies and Microsoft's async
programming documentation.

### Cancellation in Asynchronous Operations

In real-world applications, long-running operations often need to be cancellable. .NET provides the `CancellationToken`
system to enable cooperative cancellation of asynchronous operations. Cancellation is cooperative, meaning operations
check if cancellation has been requested rather than being forcibly terminated.

```C#
// Basic async method that supports cancellation
async Task ProcessDataAsync(CancellationToken cancellationToken = default)
{
    // Check for cancellation before starting work
    cancellationToken.ThrowIfCancellationRequested();
    
    // Simulate long-running work with cancellation support
    await Task.Delay(1000, cancellationToken);
    
    // More processing with periodic cancellation checks
    for (int i = 0; i < 10; i++)
    {
        // Check if cancellation was requested
        if (cancellationToken.IsCancellationRequested)
        {
            Console.WriteLine("Operation cancelled");
            return; // Early exit
        }
        
        // Do some work
        await Task.Delay(100);
    }
}

// Usage example
async Task RunWithCancellationAsync()
{
    // Create a cancellation token source
    using var cts = new CancellationTokenSource();
    
    // Set it to cancel after 2 seconds
    cts.CancelAfter(TimeSpan.FromSeconds(2));
    
    try
    {
        // Start the operation with the token
        await ProcessDataAsync(cts.Token);
        Console.WriteLine("Operation completed successfully");
    }
    catch (OperationCanceledException)
    {
        Console.WriteLine("Operation was cancelled");
    }
}
```

A `CancellationToken` is created from a `CancellationTokenSource` and passed to methods that support cancellation:

---

## Parallel Programming

Parallel programming is used when there is significant computational work that can be divided into independent chunks.
It increases CPU usage to improve throughput, which is beneficial for client systems with idle CPUs but less so for
server systems.

There are two forms of parallelism:

- **Data Parallelism:**  
  Used when processing many data items independently.  
  Example:

  ```C#
  void RotateMatrices(IEnumerable<Matrix> matrices, float degrees)
  {
      Parallel.ForEach(matrices, matrix => matrix.Rotate(degrees));
  }
  ```

- **Task Parallelism:**  
  Used when there is a pool of independent work to be done.  
  Example:

  ```C#
  void ProcessArray(double[] array)
  {
      Parallel.Invoke(
          () => ProcessPartialArray(array, 0, array.Length / 2),
          () => ProcessPartialArray(array, array.Length / 2, array.Length)
      );
  }
  ```

**Guideline:** The chunks of work should be as independent as possible to maximize efficiency.

### Error Handling in Parallel Programming

Since parallel operations may throw multiple exceptions, these are aggregated in an `AggregateException`:

```C#
try
{
    Parallel.Invoke(
        () => { throw new Exception("Error in Task 1"); },
        () => { throw new Exception("Error in Task 2"); }
    );
}
catch (AggregateException ex)
{
    ex.Handle(exception =>
    {
        Trace.WriteLine(exception);
        return true; // Exception handled.
    });
}
```

---

## Introduction to Multithreaded Programming

A thread is an independent executor within a process, capable of performing tasks concurrently. Each thread has its own
stack but shares memory with other threads in the process. For example, in UI applications, there is typically one
dedicated UI thread.

Every .NET application has a thread pool that maintains worker threads to execute tasks. The thread pool dynamically
adjusts the number of threads based on system load. Generally, you do not need to create threads manually; instead, use
higher-level abstractions such as parallel programming or asynchronous programming, which automatically queue tasks to
the thread pool.

---

## There Is No Thread

Stephen Cleary explains in his blog post that not every asynchronous operation runs on a separate thread. **Asynchronous
operations do not imply multithreading.** Rather, they allow tasks to run independently, without necessarily creating
new threads. This is a common misconception.

[There Is No Thread](https://blog.stephencleary.com/2013/11/there-is-no-thread.html)

---

## Asynchronous Programming Models

.NET provides several models for asynchronous programming:

- **Event-based Asynchronous Pattern (EAP)**
- **Asynchronous Programming Model (APM)**
- **Task-based Asynchronous Pattern (TAP)**

These models allow developers to write asynchronous code without manually managing threads.

---

## Summary

- **Asynchronous programming** is about structuring code so it can continue executing while waiting for long-running
  operations, keeping applications responsive.
- **Parallel programming** divides work among multiple threads to maximize processor usage, especially for
  compute-intensive tasks.
- **Multithreaded programming** involves managing individual threads or relying on the thread pool, but higher-level
  abstractions often make direct thread management unnecessary.
- **Deferred execution** and **asynchronous operations** enable efficient, responsive code without the overhead of
  managing multiple threads.

---

## Proper Usage of Task.Run

**Task.Run** should be used only for CPU-bound operations. Avoid wrapping synchronous methods in Task.Run merely to
simulate asynchrony, as this can lead to "fake-asynchronous" methods that confuse consumers and reduce scalability,
particularly in ASP.NET Core applications. Instead, use Task.Run externally to call the method when necessary, rather
than incorporating it into reusable library code. Let library consumers apply Task.Run when needed based on their
specific use case and threading requirements.

---


---
See Also:

- [Understanding Threads in Programming: A Restaurant Analogy](Understanding-Threads-in-Programming-A-Restaurant-Analogy.md)
- [Understanding the Thread Pool in .NET](Understanding-the-Thread-Pool-in-NET.md)
