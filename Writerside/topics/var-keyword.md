# var keyword

CONSIDER using the `var` keyword whenever the resulting type of the
variable is obvious. Avoid using it if it decreases readability.

```C#
// Good usage: Type is obvious from the right side
var names = new List<string>();

// Explicit type needed if GetSource() return type isn't obvious
string source = GetSource();

// OK: String.Split return type (string[]) is well-known
var tokens = source.Split(' ');

// Probably not good: Return type of GetId() is unclear
var id = GetId();
```

*Framework Design Guidelines Conventions, Idioms, and Patterns for Reusable .NET Libraries Second Edition (367)*

## Official Microsoft recommendation

* `var` requires less typing. It also is shorter and easier to read.
* `var` encourages you to use a descriptive name for the variable.
* You shouldn't have to care what the type of the object is
* TBD

Add more information.

In this example:

```C#
// Explicitly typed as IEnumerable - might fetch all data
IEnumerable<CaseTable> test = from x in dataBase.CaseTables
                              where x.case_id.Equals("12")
                              select x;
```

If you wrote `test.First()` you could retrieve lots of rows from the
database to get only the first one. If you used `var` or explicitly
`IQueryable<CaseTable>`, you would retrieve only the first row (assuming
`dataBase.CaseTables` is `IQueryable`).

If the underlying type returned by the query is actually
`IQueryable<CaseTable>` but you assign it to `IEnumerable<CaseTable>`,
then the performance when calling methods like `First()` could be much
slower (fetching all data first) although the code compiles and looks
correct. Using `var` here would correctly infer `IQueryable<CaseTable>`
if that's what the LINQ provider returns.
