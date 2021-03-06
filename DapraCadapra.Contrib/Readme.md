DapraCadapra.Contrib - more extensions for DapraCadapra
===========================================

Features
--------
DapraCadapra.Contrib contains a number of helper methods for inserting, getting,
updating and deleting files.

The full list of extension methods in DapraCadapra.Contrib right now are:

```csharp
T Get<T>(id);
IEnumerable<T> GetAll<T>();
int Insert<T>(T obj);
int Insert<T>(Enumerable<T> list);
bool Update<T>(T obj);
bool Update<T>(Enumerable<T> list);
bool Delete<T>(T obj);
bool Delete<T>(Enumerable<T> list);
bool DeleteAll<T>();
```

For these extensions to work, the entity in question _MUST_ have a
key-property, a property named "`id`" or decorated with a `[Key]` attribute.

```csharp
public class Car
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class User
{
    [Key]
    int TheId { get; set; }
    string Name { get; set; }
    int Age { get; set; }
}
```

`Get` methods
-------

Get one specific entity based on id

```csharp
var car = connection.Get<Car>(1);
```

or a list of all entities in the table.

```csharp
var cars = connection.GetAll<Car>();
```

`Insert` methods
-------

Insert one entity

```csharp
connection.Insert(new Car { Name = "Volvo" });
```

or a list of entities.

```csharp
connection.Insert(cars);
```



`Update` methods
-------
Update one specific entity

```csharp
connection.Update(new Car() { Id = 1, Name = "Saab" });
```

or update a list of entities.

```csharp
connection.Update(cars);
```

`Delete` methods
-------
Delete an entity by the specified `[Key]` property

```csharp
connection.Delete(new Car() { Id = 1 });
```

a list of entities

```csharp
connection.Delete(cars);
```

or _ALL_ entities in the table.

```csharp
connection.DeleteAll<Car>();
```

Special Attributes
----------
DapraCadapra.Contrib makes use of some optional attributes:

* `[Table("Tablename")]` - use another table name instead of the name of the class

	```csharp
    [Table ("emps")]
    public class Employee
    {
    	public int Id { get; set; }
        public string Name { get; set; }
    }
    ```
* `[Key]` - this property is the identity/key (unless it is named "Id")
* `[Write(true/false)]` -  this property is (not) writeable
* `[Computed]` - this property is computed and should not be part of updates

Limitations and caveats
-------

### SQLite

`SQLiteConnection` exposes an `Update` event that clashes with the `Update`
extension provided by DapraCadapra.Contrib. There are 2 ways to deal with this.

1. Call the `Update` method explicitly from `SqlMapperExtensions`

	```Csharp
    SqlMapperExtensions.Update(_conn, new Employee { Id = 1, Name = "Mercedes" });
    ```
2. Make the method signature unique by passing a type parameter to `Update`

	```Csharp
    connection.Update<Car>(new Car() { Id = 1, Name = "Maruti" });
    ```
