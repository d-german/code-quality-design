# POCO - Plain Old CLR Objects

A Plain Old CLR Object (POCO), or Plain Old C# Object as it’s sometimes 
referred to, is a simple class created in C# or another language for the 
Common Language Runtime (CLR), which is free from dependencies on an 
external framework.

Below is an example of a POCO in C#:

```C#
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public DateTime DateOfBirth { get; set; }
}
```

One of the key features of the CLR is its ability to support multiple
programming languages. This means that you can write code in various
languages, and if they are compatible with the CLR, they can be executed
within the .NET environment.

Besides C#, you can also write POCO classes in Visual Basic .NET:

```vb.net
Public Class Person
    Public Property Id As Integer
    Public Property FirstName As String
    Public Property LastName As String
    Public Property DateOfBirth As DateTime
End Class
```

---
See Also:
- [DTO - Data Transfer Object](DTO-Data-Transfer-Object.md) (often implemented as POCOs)
- [Stable vs Volatile Dependencies](Stable-vs-Volatile-Dependencies.md) (POCOs are often stable)

