# Inversion of Control (IoC)

Instead of you being in control of when a piece of code is executed, you hand the control to something else. The flow of
control is now inverted. This is also known as the Hollywood principle: "Don't call us, we will call you." Inversion of
Control moves secondary responsibilities from an object to other objects dedicated to that purpose, supporting the
Single Responsibility Principle. (Martin 209)

## Examples

### LINQ

In LINQ, you supply the lambda expression, but LINQ controls when and how often it gets called.

```c#
var valuesSquared = _values.Select(value => value * value);
```

### Windows Forms

In Windows Forms, the framework declares events on controls. You bind a method to the event using a delegate. For
example, the user is in control when the `ui_CalculateButton_Click` method is executed by clicking the
`ui_CalculateButton`.

```C#
this.ui_CalculateButton.Click =
    new System.EventHandler(this.ui_CalculateButton_Click);
```

### Unity Game Framework

In Unity, a game object must inherit from `MonoBehaviour` and implement specific methods (such as `Update()`) that the
framework calls during the game loop.

```C#
public class Laser : MonoBehaviour
{
    [SerializeField]
    private float _speed = 8.0f;

    private void Update()
    {
        transform.Translate(
            Vector3.up * (_speed * Time.deltaTime));

        if (transform.position.y > 8f)
        {
            Destroy(gameObject);
        }
    }
}
```

### IoC Containers

In dependency management, an object should not instantiate its own dependencies. Instead, this responsibility should be
inverted and handled by an authoritative mechanism, such as an IoC container. IoC containers allow you to link
interfaces to their implementations and resolve instances by automatically injecting dependencies. (Martin 2009; McLean
Hall, Gary)

```c#
private static void ConfigureAppServices(
    IServiceCollection s,
    IConfiguration c)
{
    s.AddSingleton<IMyLauncher, MyLauncher>();
    s.AddSingleton<ISampleAuthenticator, SampleAuthenticator>();
    s.AddSingleton<IMyLaunchProvider, MyLaunchProvider>();
}
```

---
See Also:
- [Dependency Injection (DI)](Dependency-Injection-DI.md)
- [Dependency Inversion Principle (DIP)](Dependency-Inversion-Principle-DIP.md)
- [Composition Root](Composition-Root.md)
- [Single Responsibility Principle (SRP)](Single-Responsibility-Principle-SRP.md)
- [Relationship between Dependency Inversion, Dependency Injection, Inversion of Control and SOLID](Relationship-between-Dependency-Inversion-Dependency-Injection-Inversion-of-Control-and-SOLID.md)
- [Seam Interface](Seam-Interface.md)