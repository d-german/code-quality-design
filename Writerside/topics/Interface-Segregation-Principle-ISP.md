# Interface Segregation Principle (ISP)

This principle advises software designers to avoid depending on things that they don’t use.

We should not force clients to depend upon methods that they do not use.

**AKA:** Interface Pollution or Fat Interface (2002 Martin 235)

When a class is large, rarely do all of its clients use all of its methods. Often we can see different groupings of
methods that particular clients use. If we create an interface for each of these groupings and have the large class
implement those interfaces, each client can see the big class through that particular interface. This helps us hide
information and also decreases dependency on the system. (Feathers 263)

Since we typically use interfaces for unrelated classes, they tend to have a leaner set of members. Remember, each
member of an interface must be implemented in derived classes, but we should not force clients to implement methods they
do not use. This “interface pollution” or “fat interface” tends to violate the Interface Segregation Principle (ISP).

---

## Example: Poor Design

```C#
/// <summary>
/// This violates the ISP
/// </summary>
public interface IVehicle
{
    void Drive();
    void Fly();
}

/// <summary>
/// This class probably violates the SRP.
/// Since the code (responsibility) for driving will change for different 
/// times and for different reasons than the code for flying.
/// </summary>
public class MultiFunctionalCar : IVehicle
{
    public void Drive()
    {
        Console.WriteLine("Drive a multifunctional car");
    }

    public void Fly()
    {
        Console.WriteLine("Fly a multifunctional car");
    }
}

public class Car : IVehicle
{
    public void Drive()
    {
        Console.WriteLine("Driving a car");
    }

    // We must implement each member of an interface in derived classes, 
    // including ones we do not use.
    public void Fly() 
    {    // we don't need to fly
        noop();
        // or throw new NotImplementedException();
    }
}

```

---

## Example: Better Design

```C#
// Interfaces split by responsibility

public interface IDrive
{
    void Drive();
}

public interface IFly
{
    void Fly();
}

// A class that only drives
public class Car : IDrive
{
    public void Drive()
    {
        Console.WriteLine("Driving a car");
    }
}

// A class that only flies
public class Airplane : IFly
{
    public void Fly()
    {
        Console.WriteLine("Flying a plane");
    }
}

/// <summary>
/// This class probably violates the SRP unless 
/// it implements the Facade design pattern
/// where it delegates driving and flying behavior 
/// to other classes
/// </summary>
public class MultiFunctionalCar : IDrive, IFly
{
    public void Drive()
    {
        // actions to drive a car
        Console.WriteLine("Drive a multifunctional car");
    }

    public void Fly()
    {
        // actions to fly a car
        Console.WriteLine("Fly a multifunctional car");
    }
}
```

---

## Relationship Between ISP and SRP

A fat interface with low cohesion forces any implementer to include many different behaviors, which breaks
the Single Responsibility Principle. The main exception is a
pure [**Facade**](Facade-Pattern-with-the-Single-Responsibility-Principle.md): it simply delegates each group of
behavior to
a dedicated helper, so each class still has only one reason to change.

See Also:

- [SOLID Design Principles](SOLID-Design-Principles.md)
- [Abstract Classes vs Interfaces](Abstract-Classes-vs-Interfaces.md)
- [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md)
- [Cohesion](Cohesion.md) (Fat interfaces often lack cohesion)
- [Evolving Configuration Interfaces From Boolean Flags to Expressive Design](Evolving-Configuration-Interfaces-From-Boolean-Flags-to-Expressive-Design.md) (
  mentions ISP)
- [Facade Pattern](Facade-Pattern-with-the-Single-Responsibility-Principle.md)