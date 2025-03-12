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

