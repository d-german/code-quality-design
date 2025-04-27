# Multiple Choice Questions: Motivation for Good Design

## Question 1
According to the article, software entropy refers to:
- [ ] A measure of how efficiently a program uses memory
- [x] The tendency of software systems to become more disordered over time
- [ ] The speed at which software executes its functions
- [ ] The rate at which new features are added to software

## Question 2
The "Bag of Bricks" analogy is used to illustrate:
- [ ] How to efficiently organize code modules
- [ ] The importance of using design patterns
- [x] Why systems naturally tend toward disorder rather than order
- [ ] The relationship between code size and performance

## Question 3
Which statement best describes the relationship between software modification and entropy?
- [ ] Modifying software always decreases its entropy
- [ ] Software entropy remains constant regardless of modifications
- [ ] Software entropy decreases proportionally to the size of modifications
- [x] When software is modified, its entropy will increase unless actively worked against

## Question 4
According to the article, what is easier to change?
- [x] An ordered system with low entropy
- [ ] A disordered system with high entropy
- [ ] Both are equally easy to change
- [ ] It depends on the programming language used

## Question 5
The Law of Change by Max Kanat-Alexander states that:
- [ ] Software should never change after it is deployed
- [ ] The chance of introducing defects decreases with larger changes
- [x] The longer a program exists, the more probable it is that any piece of it will have to change
- [ ] Changes should be made to software only when absolutely necessary

## Question 6
What is the relationship between the size of changes made to software and the chance of introducing defects?
- [ ] They are inversely proportional
- [x] They are directly proportional
- [ ] There is no relationship between them
- [ ] The relationship depends on the developer's experience

## Question 7
What does the second law of thermodynamics imply about software systems?
- [ ] Software systems naturally become more organized over time
- [x] Software systems tend toward disorder unless energy is expended to maintain order
- [ ] Software systems maintain the same level of order throughout their lifetime
- [ ] The organization of software systems is random and unpredictable

## Question 8
When designing a system to be maintainable, we should aim to:
- [ ] Maximize its initial software entropy
- [x] Minimize its initial software entropy
- [ ] Keep software entropy at a moderate level
- [ ] Ignore software entropy entirely

## Question 9
Why is it important to design for evolution in software?
- [ ] To make the software run faster
- [ ] To reduce the initial development time
- [x] To structure the program to evolve through changes in requirements and technology
- [ ] To minimize the need for documentation

# Free Response Questions: Motivation for Good Design

## Short Answer Question 1
Explain the concept of software entropy in your own words.
> Answer: Software entropy refers to the natural tendency of software systems to become more disordered over time. Like physical systems, software requires energy (deliberate effort) to maintain order. Without this effort, complexity and disorder will increase with each modification.

## Short Answer Question 2
How does the "Bag of Bricks" analogy relate to software design?
> Answer: The Bag of Bricks analogy shows that there are more ways for bricks to form a disordered pile than an ordered wall. Similarly, there are far more ways to write messy code than clean code. Both require deliberate energy to maintain order.

## Short Answer Question 3
According to the Law of Change, what is the relationship between change and defects in software?
> Answer: The Law of Change states that software inevitably needs to change over time. The probability of introducing defects is directly proportional to the size of changes. Therefore, good design allows for environmental changes with minimal code changes.

## Short Answer Question 4
Why is initial software entropy important to consider when designing systems?
> Answer: Initial software entropy determines how quickly disorder will increase with future modifications. Starting with low entropy makes a system more maintainable. Maintaining order is easier than trying to restore it after disorder has set in.

## Short Answer Question 5
How does good design help with the inevitable need for software to change?
> Answer: Good design creates low-entropy systems that are easier to modify. It allows for more environmental change with less code change. Good design principles like SOLID and DRY help maintain this low-entropy state during modifications.