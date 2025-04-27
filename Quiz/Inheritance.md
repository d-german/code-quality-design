# Multiple Choice Questions: Inheritance

## Question 1
What type of relationship does inheritance primarily model?
- [ ] has-a
- [x] is-a
- [ ] can-do
- [ ] uses-a

## Question 2
According to the article, what is the main *risk* associated with using inheritance primarily for code reuse?
- [ ] It makes code run slower.
- [ ] It increases the amount of code needed.
- [x] It can lead to issues when trying to use the relationship polymorphically (violating LSP).
- [ ] It prevents the use of interfaces.

## Question 3
Which principle states that objects of a superclass should be replaceable with objects of its subclasses without affecting the correctness of the program?
- [ ] DRY Principle
- [ ] Encapsulation
- [x] Liskov Substitution Principle (LSP)
- [ ] Prefer Composition over Inheritance

## Question 4
What is the generally recommended design principle regarding inheritance and composition?
- [ ] Prefer inheritance over composition for flexibility.
- [x] Prefer composition over class (implementation) inheritance.
- [ ] Use inheritance and composition equally.
- [ ] Avoid both inheritance and composition.

## Question 5
Interface inheritance, as described in the article, primarily involves inheriting:
- [ ] Data members and method implementations.
- [x] Only the public interface (method signatures).
- [ ] Protected members only.
- [ ] Private helper methods.

## Question 6
What is the recommended practice regarding data fields (members) in a base class according to the article's guidelines?
- [ ] Make them public for easy access.
- [ ] Make them protected so derived classes can access them directly.
- [x] Make them private to enforce encapsulation.
- [ ] Make them internal.

## Question 7
The "Diamond Problem" is an ambiguity associated with which feature?
- [ ] Single Inheritance
- [ ] Interface Inheritance
- [x] Multiple Inheritance (of classes)
- [ ] Composition

## Question 8
What kind of relationship is typically associated with implementing an interface (interface inheritance)?
- [ ] is-a
- [ ] like-a
- [x] can-do
- [ ] owns-a

# Free Response Questions: Inheritance

## Short Answer Question 1
In your own words, define inheritance in object-oriented programming and state its primary intended purpose according to the article.
> Answer: Inheritance is a mechanism where a new class (subclass) derives properties and behaviors (attributes and methods) from an existing class (superclass). Its primary purpose is to model an "is-a" relationship, representing specialization, where the subclass is a specific type of the superclass. Code reuse is often a secondary benefit, not the primary goal.

## Short Answer Question 2
Explain the difference between *implementation inheritance* and *interface inheritance*.
> Answer: Implementation inheritance means the subclass inherits both the interface (method signatures) and the actual code (implementation) and data from the base class. Interface inheritance means the subclass inherits only the interface (the contract, typically method signatures) and must provide its own implementation for those methods.

## Short Answer Question 3
Why does the article recommend preferring composition over class inheritance? What potential problem does this help avoid?
> Answer: Composition is often preferred because it creates looser coupling between classes compared to implementation inheritance, which creates a very strong and rigid dependency. Preferring composition helps avoid problems associated with tight coupling, such as difficulty in modifying the base class without breaking subclasses, and prevents misuse of inheritance solely for code reuse, which can lead to violations of the Liskov Substitution Principle.

## Short Answer Question 4
Briefly explain the Liskov Substitution Principle (LSP) and why it's crucial for correct inheritance hierarchies.
> Answer: The Liskov Substitution Principle states that you should be able to substitute instances of a subclass anywhere an instance of the base class is expected, without altering the correctness or behavior of the program. It's crucial because if a subclass changes behavior inherited from the base class in an incompatible way, it breaks the "is-a" relationship, meaning it can't reliably be used polymorphically where the base class is expected.

## Short Answer Question 5
What is the guideline regarding `protected` data members in a base class, and why is this recommendation made?
> Answer: The guideline is to avoid protected data members and keep all base class fields private. This is recommended because protected fields are essentially implementation details of the base class. Exposing them to derived classes creates a tight coupling; changes to these protected fields in the base class can potentially break all derived classes that depend on them.