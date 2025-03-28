# Cohesion

Here's a cleaned-up and nicely structured version of your content, preserving your original wording and formatting intent, and keeping line lengths under 70 characters for readability:

---

### Cohesion

Cohesion is the degree to which the elements in a module belong together.  
A module with high cohesion means that the module's elements have a high  
degree of connectedness.

“Cohesion refers to what extent the parts of a module should be contained  
within the same module. In other words, it is a measure of how related  
the parts are to one another. Ideally, a cohesive module is one where  
all the parts should be packaged together, because breaking them into  
smaller pieces would require coupling the parts together via calls  
between modules to achieve useful results.” 
 Mark Richards and Neal Ford

Attempting to divide a cohesive module would only result in increased  
coupling and decreased readability.  
— *Larry Constantine*

“Software modules should aim for high cohesion.”
 Dianxiang Xu, p. 85

This is a foundational design principle. For example:

- Classes that violate the **Single Responsibility Principle (SRP)**  
  have low cohesion.
- Interfaces or abstract classes that violate the  
  **Interface Segregation Principle (ISP)** also exhibit low cohesion.

---

![](cohesion.png)

---
See Also:
- [Coupling](Coupling.md)
- [Modularity, Module](Modularity-Module.md)
- [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md)
- [Interface Segregation Principle (ISP)](Interface-Segregation-Principle-ISP.md)
- [SOLID Design Principles](SOLID-Design-Principles.md)