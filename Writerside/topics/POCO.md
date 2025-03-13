# POCO

- **A Plain Old CLR Object (POCO)**  
  A POCO, or Plain Old C# Object, is a simple class created in C# (or another CLR-compatible language) that has no
  dependencies on an external framework.

- **Example in C#**
  ```c#
  public class Person
  {
      public int Id { get; set; }
      public string FirstName { get; set; }
      public string LastName { get; set; }
      public DateTime DateOfBirth { get; set; }
  }
  ```

- **CLR Language Support**  
  One of the key features of the Common Language Runtime (CLR) is its ability to support multiple programming languages.
  Code written in different languages can be executed within the .NET environment, as long as those languages are
  compatible with the CLR.

- **Example in Visual Basic .NET**
  ```vb.net
  Public Class Person
      Public Property Id As Integer
      Public Property FirstName As String
      Public Property LastName As String
      Public Property DateOfBirth As DateTime
  End Class
  ```

- **Further Reading**  
  See **Stable vs Volatile Dependencies** for more information.
