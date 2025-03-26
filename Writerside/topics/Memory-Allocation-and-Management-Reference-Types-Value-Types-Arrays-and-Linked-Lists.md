# Memory Allocation and Management: Reference Types, Value Types, Arrays, and Linked Lists

## Reference types vs. value types

![Stack vs Heap](stack-heap1.png)

```C#
void DemonstrateMemoryAllocation()
{
    int result = 5; // Value type (stored on the stack)
    var builder = new StringBuilder(); 
        // Reference type (reference stored on the stack, 
        // object stored on the heap)
    var date = new DateTime(1984, 10, 9); 
        // Struct (entire struct stored on the stack, 
        // if inside the method)
    string formula = "2 + 2 = ";  
        // Reference type (reference stored on the stack, 
        // string data stored on the heap)
    
    builder.Append(formula); 
        // `formula()` reference used to access 
        // heap-stored string data
    builder.Append(result); 
        // `result()` value directly appended, 
        // since it's a value type
    builder.Append(date.ToString()); 
        // `date()` is a value type but converted to a string 
        // (new string on the heap)
    
    Console.WriteLine(builder.ToString()); 
        // Outputs the final string 
        // (builder and its contents in the heap)
}
```

## Class vs. Struct: Memory Behavior

### Class Example

```C#
public class Id 
{
  public int Value { get; private set; }
  public Id (int value) 
  {
    this.Value = value;
  }
}
```

```C#
public struct Id 
{
  public int Value { get; private set; }
  public Id (int value) 
  {
    this.Value = value;
  }
}
```

```C#
var a = new Id(123); 
```

When using classes, a reference to the object is stored on the stack, but the actual object itself is stored in the
heap. For structs, since they are value types, the entire object is stored on the stack if declared within a method.

```C#
var a = new Id(123);
var b = a;
```

In the class example, only the reference is copied, so both `a()` and `b()` point to the same object in the heap. In the
struct example, the entire object is copied, so `a()` and `b()` are independent.

![Stack vs Heap](stack-heap2.png)

## Class and Struct Example with Person

### Class

```C#
public class Person {                    ❶
  public int Id { get; private set; }
  public string FirstName { get; private set; }
  public string LastName { get; private set; }
  public string City { get; private set; }
  
  public Person(int id, string firstName, string lastName,
    string city) 
  {
    Id = id;
    FirstName = firstName;
    LastName = lastName;
    City = city;
  }
}
```

### Struct

```C#
public struct Person {                    ❶
  public int Id { get; private set; }
  public string FirstName { get; private set; }
  public string LastName { get; private set; }
  public string City { get; private set; }
  
  public Person(int id, string firstName, string lastName,
    string city) 
  {
    Id = id;
    FirstName = firstName;
    LastName = lastName;
    City = city;
  }
}
```

```C#
var a = new Person(42, "Sedat", "Kapanoglu", "San Francisco");
var b = a;
```

![Stack vs Heap](stack-heap3.png)

For classes, both `a()` and `b()` refer to the same object in the heap. For structs, `a()` and `b()` are independent
objects, with the contents of `a()` copied into `b()`.

## Arrays

An array is a collection of elements, all of the same type, stored contiguously in memory. Contiguous means that the
elements are placed next to each other in memory, with no gaps between them. Homogeneous means that every element in the
array is of the same data type. Elements in an array can be accessed using an index.

```C#
void DemonstrateArrayMemoryAllocation()
{
    // Declare and initialize an array of integers
    var values = new int[] { 1, 2, 3, 4, 5, 6, 7, 8 };
    
    // Explanation:
    // 1. `values()` is a reference to an array, so the 
    //    reference itself is stored on the stack.
    // 2. The actual array (int[] { 1, 2, 3, 4, 5, 6, 7, 8 }) 
    //    is allocated in the heap.
    // 3. The array consists of 8 integers, and each integer 
    //    (since it's a value type) is directly stored in 
    //    contiguous memory in the heap.
    // 4. The `values()` reference on the stack points to the 
    //    memory location of the first element in the array 
    //    in the heap.
    
    // Accessing the array elements:
    Console.WriteLine(values[0]); 
        // Outputs 1, accessing the first element in the heap 
        // via the reference.
    Console.WriteLine(values[7]); 
        // Outputs 8, accessing the eighth element in the heap 
        // via the reference.
        
    // Each access to an element uses the reference stored 
    // on the stack to get the corresponding value from the heap.
}
```

Actual layout of an array in memory

![Array](array1.png)

## Linked List

A linked list is a collection of heterogeneous elements where each element, called a node, contains a value and a
reference (or pointer) to the next node in the sequence. Unlike arrays, the elements of a linked list are not stored
contiguously in memory; instead, each element can be located anywhere in memory, with the references connecting them. By
heterogeneous, we mean that the memory locations of the elements (nodes) are scattered, not necessarily next to one
another, though the data type of the value within the nodes is homogeneous.

![Linked List](linkedlist1.png)

```C#
void DemonstrateLinkedList()
{
    // Linked list declaration
    LinkedList<int> myList = new LinkedList<int>();
    
    // Adding some nodes
    myList.AddLast(10);  
        // A node containing 10 is allocated in the heap
    myList.AddLast(20);  
        // A node containing 20 is allocated in the heap
    myList.AddLast(30);  
        // A node containing 30 is allocated in the heap
        
    // `myList()` reference is on the stack
    // But the actual linked list nodes are all stored in the heap
}
```

## Garbage Collection in .NET

In .NET, garbage collection (GC) is responsible for automatically managing memory. The GC monitors which objects in the
heap are no longer being referenced and frees up that memory. In modern object-oriented environments, almost every
aspect of execution has been optimized for speed. As a result, if your application experiences performance bottlenecks
that aren't caused by your own code, they are most likely due to garbage collection. This is especially true when
dealing with large numbers of objects or frequent memory allocations and deallocations.

In recent versions, Microsoft has been focusing on reducing the impact of garbage collection by introducing mechanisms
like `Span<T>()` and `Memory<T>()`, which allow working with memory in a more efficient way, minimizing heap
allocations. This helps improve performance by reducing the frequency and cost of garbage collection.

## Queue: First In, First Out (FIFO)

![Queue](linkedlist2.png)

## Stack: Last In, First Out (LIFO)

![Stack](linkedlist3.png)


