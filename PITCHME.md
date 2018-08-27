## Using <span class="orange">Immutable</span> Data Structures 
#### In C# and .NET

### @schneidenbach

---

```csharp
var person = new Person();
```

---

```csharp
var person = new Person();
person.FirstName = "Spencer";
```

---

```csharp
var person = new Person();
person.FirstName = "Spencer";
person.LastName = "Schneidenbach";
```

---

![Don't do it](assets/dontdoit.gif)

---

```csharp
var person = new Person();
//person.FirstName = "Spencer";
//person.LastName = "Schneidenbach";
```

---

# Questions?

---

![Love isn't true](assets/donttellme.gif)

---

# <span class="orange">Functional programming</span>

---

### Q. How do you know someones <span class="orange">"does"</span> functional programming?

---

# <span class="orange">Functional programming</span>

---

# immutability

---

# WARNING
There are a lot of bad immutability jokes in these slide deck

---

# <span class="orange">Have you done this?</span>

---

![](assets/mutability1.png)

---

![](assets/mutability2.png)

---

![](assets/mutability3.png)

---

![](assets/mutability4.png)

---

![](assets/mutability5.png)

---

![](assets/mutability6.png)

---

![](assets/mutability7.png)

---

![](assets/mutability8.png)

---

![NullReferenceException](assets/walterwhite.gif)

---

# `NullReferenceException`

---

# `NullReferenceException`
cause you mutated your state

---

# `NullReferenceException`
cause you mutated your state  
...among other things

---

## Mixed data and view stuff

---

# BUGS

---

> A large fraction of the flaws in software development are due to programmers not fully understanding all the possible states their code may execute in.

John Carmack

---

![Scott Hanselman Tweet](assets/hanseltweet.png)

---

![Real Elon Tweet](assets/goodelontweet.png)

---

![Real Elon Tweet](assets/goodelontweetretweeted.png)

---

![Questionable Elon Tweet](assets/questionableelontweet.png)

---

> A large fraction of the flaws in software development are due to programmers not fully understanding all the possible states their code may execute in.

John Carmack

---

### Change Tweet w/o edit button
- ...You can't

---

### Change Tweet w/ edit button
- What if it's been retweeted?
- What if it's been liked?

---

![Reasoning](assets/reasoning.png)

---

![Synchronization Quadrant](assets/syncquad1.png)

---

![Synchronization Quadrant](assets/syncquad2.png)

---

# Just imagine

---

History of Immutability in C#

---

# `string`
# `DateTime`
# number types

---

```csharp
var str = "replaceable?";
str.Replace("?", "!");
Console.WriteLine(str);
```

---

```csharp
var str = "replaceable?";
str.Replace("?", "!");
Console.WriteLine(str);
//output: replaceable?
```

---

```csharp
var now = DateTime.Now;
now.AddHours(1);
```

---

```csharp
var now = DateTime.Now
                  .AddHours(1);
```

---

# C# classes

---

```csharp
public class Person
{
    //private setters - still mutable
    public string FirstName { get; private set; }
    public string LastName { get; private set; }

    public Person(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;
    }
}
```

---

```csharp
public class Person
{
    //readonly fields - does the job... but very ugly
    public readonly string _firstName;
    public string FirstName
    {
        get
        {
            return _firstName;
        }
    }

    public Person(string firstName)
    {
        _firstName = firstName;
    }
}
```

---

## `private get` or `readonly` fields

---

## `private get` or `readonly` fields
Neither solve the problem perfectly

---

# C# 6

```csharp
public class Person
{
    public string FirstName { get; }
    public string LastName { get; }

    public Person(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;
    }
}
```

---

# Much better!

```csharp
public class Person
{
    public string FirstName { get; }
    public string LastName { get; }

    public Person(string firstName, string lastName)
    {
        FirstName = firstName;
        LastName = lastName;
    }
}
```

---

# Immutability today

---

# <span class="orange">LINQ</span>

---

## Anonymous types

---

```csharp
var projection = employees.Select(e => new {
    e.FirstName,
    e.LastName,
    e.Number,
    ManagerCode = e.Manager?.Number
});
```

---

## Anonymous types
* Immutable
* Structural equality*

*VB.NET, it's opt in

---

## <span class="orange">LINQ</span> methods

---

## <span class="orange">LINQ</span> methods
`Where<T>`   
`Select<T>`

---

```csharp
var employees = GetEmployees();
employees
.Where(e => e.FirstName.StartsWith("S"))
.Select(e => new {
    e.FirstName,
    e.LastName,
    e.Number,
    ManagerCode = e.Manager?.Number
});
```

---

```csharp
var employees = GetEmployees();
employees
.Where(e => e.FirstName.StartsWith("S"))
.Select(e => new {
    e.FirstName,
    e.LastName,
    e.Number,
    ManagerCode = e.Manager?.Number
});
```
"Return value of pure method is not used"

---

# Projection

---

```csharp
public interface IColumn
{
    public string ColumnName { get; }
    public Type ColumnType { get; }
}
```

---

```csharp
public class Column : IColumn
{
    public string ColumnName { get; }
    public Type ColumnType { get; }

    public Column(string columnName, Type columnType)
    {
        ColumnName = columnName;
        ColumnType = columnType;
    }
}
```

---

```csharp
public IEnumerable<IColumn> GetColumnsForTable() {
    //for People table it returns
    return new[] {
        new Column("FirstName", typeof(string)),
        new Column("LastName", typeof(string)),
        new Column("Age", typeof(int)),
        new Column("Id", typeof(Guid))
    };
}

var columns = GetColumnsForTable("People");
```

---

```csharp
public IEnumerable<IColumn> GetColumnsForTable() {
    //for People table it returns
    return new[] {
        new Column("FirstName", typeof(string)),
        new Column("LastName", typeof(string)),
        new Column("Age", typeof(int)),
        new Column("Id", typeof(Guid))  //needs to be String
    };
}

var columns = GetColumnsForTable("People");
```

---

```csharp
public interface IColumn
{
    public string ColumnName { get; }
    public Type ColumnType { get; }
}
```

---

```csharp
public interface IColumn
{
    public string ColumnName { get; }
    public Type ColumnType { get; set; }
}
```

---

```csharp
var columns = GetColumnsForTable("People");
foreach (var column in columns)
{
    if (column.ColumnType == typeof(Guid))
    {
        column.ColumnType = typeof(string);
    }
}
```

---

```csharp
var columns = GetColumnsForTable("People")
.Select(c => new Column(c.ColumnName, 
                        c.ColumnType == typeof(Guid) ? typeof(string)));

```

---

# Projection

---

## `System.Collections.Immutable`

---

## Library support

---

## AutoMapper

```csharp
public class Employee
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
}

public class Person
{
    public string FirstName { get; }
    public string LastName { get; }

    public Person(string firstName, string lastName) {...}
}

var person = Mapper.Map<Person>(employee);
```

---

## AutoMapper

```csharp
config.CreateMap<Employee, Person>()
      .ConstructUsing(e => new Person(e.FirstName, e.LastName));
```

---

## Newtonsoft.Json

```csharp
public class Person
{
    public string FirstName { get; }
    public string LastName { get; }

    public Person(string firstName, string lastName) {...}
}

var person = JsonConvert.DeserializeObject<Person>(
    "{\"FirstName\": \"Spencer\", \"LastName\": \"Schneidenbach\"}"
);
```

---

## Newtonsoft.Json

```csharp
public class Person
{
    public string FirstName { get; }
    public string LastName { get; }
    public int Age { get; }

    public Person(string firstName, string lastName) {...}
    [JsonConstructor]
    public Person(string firstName, string lastName, int age) {...}
}
```

---

## Builder pattern

Couple mutable "config" object with immutable "final state"

---

```csharp
public class DbContextOptionsBuilder
{
    public string ConnectionString { get; set; }
    public bool UseConnectionPool { get; set; }

    //returns immutable copy of options!
    public DbContextOptions Build() {...}
}
```

---

## Builder pattern

---

![Yin and Yang](assets/yinyang.png)

---

![I'll kill you](assets/illkillyou.gif)

---

![Lipstick on a pig](assets/lipstick.gif)

---

## Where mutability shines

---

# Entity Framework

---

# Entity Framework
* Databases are mutable

---

# Entity Framework
* EF depends on mutability

---



---

## Thank you!

[@schneidenbach](https://twitter.com/schneidenbach)  
schneids.net
