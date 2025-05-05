```markdown
### Multiple Choice Questions

**1. What happens when a value type is assigned to another variable?**

- [x] A copy of the entire object is made.
- [ ] A reference to the object is passed.
- [ ] A pointer to the heap is returned.
- [ ] The memory location is shared between variables.

**2. Which of the following best describes how arrays are stored in memory in .NET?**

- [ ] Each element is stored in a separate memory location across the heap.
- [x] All elements are stored contiguously in heap memory.
- [ ] The array reference and data are both stored on the stack.
- [ ] Array elements are stored in linked list fashion on the heap.

**3. In C#, what happens to a local variable captured by a closure?**

- [x] It is moved from the stack to the heap.
- [ ] It is optimized away during compilation.
- [ ] It remains on the stack with a reference from the heap.
- [ ] It becomes a static variable.

**4. What is a major performance advantage of using `Span<T>` or `Memory<T>`?**

- [x] They minimize heap allocations, improving GC performance.
- [ ] They allow value types to be stored in the LOH.
- [ ] They replace the need for garbage collection entirely.
- [ ] They allow non-contiguous storage of memory blocks.

**5. Which of the following statements is true about garbage collection in .NET?**

- [ ] Gen 2 collections happen more frequently than Gen 0.
- [x] Gen 0 collections are fast and frequent.
- [ ] The LOH is collected with every Gen 0 cycle.
- [ ] All generations are collected simultaneously each time.

**6. When is a class object stored on the heap in C#?**

- [x] Always, because classes are reference types.
- [ ] Only when the object is large enough.
- [ ] Only when used in arrays.
- [ ] When explicitly allocated using `new` with `GCHandle`.

**7. Which of the following is a drawback of implementing a queue using an array instead of a linked list?**

- [x] Dequeue operations require shifting elements forward.
- [ ] Enqueue operations are always O(1).
- [ ] Array-based queues support unlimited capacity.
- [ ] Linked lists do not support FIFO behavior.

**8. What is true of struct fields within a class instance?**

- [ ] They are stored on the stack.
- [ ] They are stored separately from the class on the heap.
- [x] They are stored with the class on the heap.
- [ ] They require special allocation using pointers.

**9. In which of the following scenarios do `a` and `b` reference the same object in memory?**

- [ ] When `a` and `b` are both structs.
- [x] When `a` and `b` are both classes and `b = a`.
- [ ] When `a` and `b` are arrays of value types.
- [ ] When `a` is a closure and `b` is a captured variable.

**10. Which of the following best describes how a linked list is stored in memory?**

- [x] Nodes are stored non-contiguously and linked via references.
- [ ] Nodes are always stored contiguously in the stack.
- [ ] Nodes must reside in the Large Object Heap.
- [ ] The entire list is allocated in the stack.

---

### Open Response Questions

**1. Explain the difference in memory behavior between a struct and a class when assigning one to another.**  
*Example response:* A struct is a value type, so assigning it to another variable copies the entire object, creating two independent objects. A class is a reference type, so the assignment only copies the reference, meaning both variables point to the same object on the heap.

**2. Why is using a linked list more efficient than an array for implementing a queue?**  
*Example response:* In a linked list-based queue, enqueue and dequeue operations are O(1) because nodes can be added or removed from the ends without shifting other elements. An array-based queue would require shifting elements after dequeueing, which is less efficient.

**3. Describe how garbage collection improves performance in .NET and how recent features like `Span<T>` help.**  
*Example response:* Garbage collection reclaims unused memory, especially from short-lived objects in Gen 0, improving memory efficiency. `Span<T>` reduces heap allocations by working with stack-allocated memory, thus lowering GC pressure and improving performance.
```
