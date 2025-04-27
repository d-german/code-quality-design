# Multiple Choice Questions: Encapsulation & Abstraction

## Question 1
What is the primary concept of Encapsulation?
- [ ] Hiding the internal implementation details.
- [x] Bundling related data and behavior together in one unit.
- [ ] Making all methods public by default.
- [ ] Exposing all data members for easy access.

## Question 2
Abstraction, in the context of OOP, primarily refers to:
- [ ] Grouping data and methods.
- [x] Hiding implementation complexity behind a simpler interface.
- [ ] Ensuring all data members are public.
- [ ] The process of creating abstract classes.

## Question 3
According to the guidelines, what should the default accessibility of class data members be?
- [ ] Public
- [ ] Protected
- [x] Private
- [ ] Internal

## Question 4
Why is it generally recommended to keep public method signatures stable?
- [ ] It makes the code run faster.
- [x] Changing them forces updates in all dependent classes.
- [ ] It reduces the amount of code needed.
- [ ] It is required by the compiler.

## Question 5
Which method name best avoids a "leaky abstraction"?
- [ ] `WriteCustomerRecordToSQLServerTable()`
- [ ] `UpdateUserInDatabase()`
- [x] `SaveCustomer()`
- [ ] `CommitChangesToOracleDB()`

## Question 6
Methods that are only used internally by other methods within the same class should typically be:
- [ ] Public
- [ ] Protected
- [x] Private
- [ ] Static

# Free Response Questions: Encapsulation & Abstraction

## Short Answer Question 1
In your own words, explain the concept of Encapsulation.
> Answer: Encapsulation is the practice of combining data (attributes) and the methods (behavior) that operate on that data into a single unit, typically a class. This helps organize code and protects the internal state of an object.

## Short Answer Question 2
Describe Abstraction and its main benefit. Use the doorknob example or create your own.
> Answer: Abstraction means hiding the complex internal details of how something works and providing a simple interface to interact with it. The main benefit is simplifying usage; for example, you only need to know how to turn a doorknob (`Turn()`), not the intricate mechanics inside, to open a door.

## Short Answer Question 3
Why is it a recommended guideline to make all data members private?
> Answer: Making data members private enforces encapsulation. It prevents external code from directly modifying an object's internal state in unexpected ways, ensuring data integrity and allowing the class to control how its data is accessed and changed via methods.

## Short Answer Question 4
Explain what a "leaky abstraction" is and why `SaveToDatabase()` might be considered one compared to `Save()`.
> Answer: A leaky abstraction exposes implementation details that the user of the abstraction shouldn't need to know. `SaveToDatabase()` leaks the detail that the data is being saved specifically to a database, potentially tying the calling code to that specific storage mechanism. `Save()` is a better abstraction as it hides the storage detail, allowing the implementation to change (e.g., save to a file, a database, or the cloud) without affecting the calling code.