# Dependency Inversion Principle in Unity (DIP)

**DIP**: High-level modules depend on abstractions, not concrete implementations.

---

## 1. Define an Interface

```C#
public interface ISwitchable
{
    bool IsActive { get; }
    void Activate();
    void Deactivate();
}
```

---

## 2. Implement the Interface

```C#
public class Door : MonoBehaviour, ISwitchable { /* ... */ }
public class Trap : MonoBehaviour, ISwitchable { /* ... */ }
```

---

## 3. Use DIP in a MonoBehaviour

```C#
public class Switch : MonoBehaviour
{
    [SerializeField] private MonoBehaviour m_ClientBehaviour;
    private ISwitchable m_Client => m_ClientBehaviour as ISwitchable;

    public void Toggle()
    {
        if (m_Client == null) return;
        if (m_Client.IsActive) m_Client.Deactivate();
        else m_Client.Activate();
    }
}
```

---

## 4. Assign References in the Inspector

* **Drag and drop** a component (e.g., `Door` or `Trap`) that implements `ISwitchable` into the `Client Behaviour` field
  of `Switch` in the Inspector.

**Example:**

![Unity Inspector: Assign Door (Door) to Client Behaviour field](trap.png)

---

## 5. Result

* The **Switch** operates on abstractions (`ISwitchable`), not specific types.
* You can swap any implementation without changing the Switch script.

### **Benefits**

* Loose coupling between Switch and Door/Trap.
* Easily extendable with new ISwitchable types.

---

See Also:
- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Polymorphism](Polymorphism.md)
- [Program to an interface; not an Implementation (GOF 18)](Program-to-an-interface-not-an-Implementation-GOF-18.md)
- [SOLID Design Principles](SOLID-Design-Principles.md)
- [Unity Case Study – Refactoring “Tanks” with SOLID Principles and Design Patterns](Unity-Case-Study-–-Refactoring-“Tanks-”-with-SOLID-Principles-and-Design-Patterns.md)
