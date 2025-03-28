# Encapsulation/Abstraction

### **Encapsulation**

Encapsulation is the concept of bundling related data and behavior within a single entity.

### **Abstraction**

Abstraction refers to information hiding.

- Users do not need to know or care about the internal workings of a system to use it through its public interface.
- Example: A doorknob provides a simple interface (`Turn()`) without exposing its internal mechanism.

### **Guidelines**

- **All data members should be private.**
- **Methods needed only by other methods should be private.**
- **Only methods required by other classes should be public or protected.**
- **Public methods should avoid implementation details.**
    - Example: Use `Save()` instead of `SaveToDatabase()` to prevent a *leaky abstraction*.
- **Public method signatures should remain stable.**
    - Changing them requires updating all dependencies.

---
See Also:
- [Pillars of OOP](Pillars-of-OOP.md)
- [Minimize Accessibility of Classes and Members](Minimize-Accessibility-of-Classes-and-Members.md)
- [Some Object-Oriented Design Heuristics - Riel updated](Some-Object-Oriented-Design-Heuristics-Riel-updated.md) (Heuristics 2.1, 2.2, 2.5, 2.6, 5.3)
- [Find What Is Varying and Encapsulate It (Shalloway 123)](Find-What-Is-Varying-and-Encapsulate-It-Shalloway-123.md)