# PATRÓN REPOSITORIO

> Adaptación de [Gauffin](http://blog.gauffin.org/2013/01/repository-pattern-done-right/)

Tabla de contenidos

- [Qué es el Patrón Repositorio](#qué-es-el-patrón-repositorio)
- [Implementaciones](#implementaciones)
- [Basic contract](#basic-contract)

## Qué es el Patrón Repositorio

Un repositorio media entre el dominio y las capas de mapeo de datos, actuando como una colección de objetos de dominio en memoria. Los objetos del cliente construyen especificaciones de consulta de forma declarativa y las envían al repositorio para su satisfacción. Los objetos se pueden agregar y eliminar del Repositorio, como pueden hacerlo desde una simple colección de objetos, y el código de mapeo encapsulado por el Repositorio llevará a cabo las operaciones apropiadas detrás de las escenas.

## Implementaciones

### Base class

Estas clases se pueden reutilizar para las diferentes implementaciones.

### UnitOfWork

La unidad de trabajo representa una transacción cuando se usa en capas de datos. Normalmente, la unidad de trabajo retrotraerá la transacción si `SaveChanges()` no se ha invocado antes de desecharla.

```csharp
interfaz pública IUnitOfWork: IDisposable
{
    void SaveChanges();
}
```

### Paginación / Paging

También necesitamos tener resultados de página.

```csharp
public class PagedResult<TEntity>
{
    IEnumerable<TEntity> _items;
    int _totalCount;

    public PagedResult(IEnumerable<TEntity> items, int totalCount)
    {
        _items = items;
        _totalCount = totalCount;
    }

    public IEnumerable<TEntity> Items { get { return _items; } }
    public int TotalCount { get { return _totalCount; } }
}
```

Podemos con la ayuda del código anterior, crear métodos como:

```csharp
public class UserRepository
{
    public PagedResult<User> Find(int pageNumber, int pageSize)
    {
    }
}
```

### Clasificación / Sorting

Finalmente, preferimos hacer los elementos de clasificación y página, ¿verdad?

```csharp
var constraints = new QueryConstraints<User>()
    .SortBy("FirstName")
    .Page(1, 20);

var page = repository.Find("Jon", constraints);
```

Tenga en cuenta que utilicé el nombre de la propiedad, pero también podría haber escrito `constraints.SortBy(x => x.FirstName)`. Sin embargo, es un poco difícil de escribir en aplicaciones web donde obtenemos la propiedad de ordenación como una cadena.

La clase completa puedes encontrarla en [github](https://github.com/jgauffin/Griffin.Data/blob/master/src/Griffin.Data/Queries/QueryConstraintsT.cs).

En nuestro repositorio (si es compatible con LINQ) podemos aplicar las restricciones como sigue:

```csharp
public class UserRepository
{
    public PagedResult<User> Find(string text, QueryConstraints<User> constraints)
    {
        var query = _dbContext.Users.Where(x => x.FirstName.StartsWith(text) || x.LastName.StartsWith(text));
        var count = query.Count();

        //easy
        var items = constraints.ApplyTo(query).ToList();

        return new PagedResult(items, count);
    }
}
```

Los métodos de extensión también están disponibles en [github](https://github.com/jgauffin/Griffin.Data/blob/master/src/Griffin.Data/Queries/QueryConstraintsExtensions.cs).

## Basic contract

Normalmente empiezo a usar una definición pequeña para el repositorio, ya que hace que mis otros contratos sean menos detallados. Tenga en cuenta que algunos de mis contratos de repositorio no implementan esta interfaz (por ejemplo, si alguno de los métodos no se aplica).

```csharp
public interface IRepository<TEntity, in TKey> where TEntity : class
{
    TEntity GetById(TKey id);
    void Create(TEntity entity);
    void Update(TEntity entity);
    void Delete(TEntity entity);
}
```

Luego lo especializo por modelo de dominio:

```csharp
public interface ITruckRepository : IRepository<Truck, string>
{
    IEnumerable<Truck> FindBrokenTrucks();
    IEnumerable<Truck> Find(string text);
}
```

Esa especialización es importante. Mantiene el contrato simple. Sólo crea métodos que sabes que necesitas.

## Entity Framework

Tenga en cuenta que **el patrón de repositorio solo es útil si tiene POCO que están mapeados usando Code First**. De lo contrario, solo romperás la abstracción usando las entidades. El patrón repositorio no es muy útil entonces.

Lo que quiero decir es que si usa el diseñador de modelos siempre obtendrá una representación perfecta de la base de datos (pero como clases). El problema es que esas clases pueden no ser una representación perfecta de su modelo de dominio. Por lo tanto, debe cortar las esquinas en el modelo de dominio para poder usar sus clases de db generadas.

Si, por otro lado, usa Code First, puede modificar los modelos para que sean una representación perfecta de su modelo de dominio (si el DB es razonablemente similar). No tiene que preocuparse de que sus cambios sean sobrescritos como lo habrían sido por el diseñador del modelo.

## Base class

```csharp
public class EntityFrameworkRepository<TEntity, TKey> where TEntity : class
{
    private readonly DbContext _dbContext;

    public EntityFrameworkRepository(DbContext dbContext)
    {
        if (dbContext == null) throw new ArgumentNullException("dbContext");
        _dbContext = dbContext;
    }

    protected DbContext DbContext
    {
        get { return _dbContext; }
    }

    public void Create(TEntity entity)
    {
        if (entity == null) throw new ArgumentNullException("entity");
        DbContext.Set<TEntity>().Add(entity);
    }

    public TEntity GetById(TKey id)
    {
        return _dbContext.Set<TEntity>().Find(id);
    }

    public void Delete(TEntity entity)
    {
        if (entity == null) throw new ArgumentNullException("entity");
        DbContext.Set<TEntity>().Attach(entity);
        DbContext.Set<TEntity>().Remove(entity);
    }

    public void Update(TEntity entity)
    {
        if (entity == null) throw new ArgumentNullException("entity");
        DbContext.Set<TEntity>().Attach(entity);
        DbContext.Entry(entity).State = EntityState.Modified;
    }
}
```

Luego hago la implementación:

```csharp
public class TruckRepository : EntityFrameworkRepository<Truck, string>, ITruckRepository
{
    private readonly TruckerDbContext _dbContext;
    public TruckRepository(TruckerDbContext dbContext)
    {
        _dbContext = dbContext;
    }
    public IEnumerable<Truck> FindBrokenTrucks()
    {
        //compare having this statement in a business class compared
        //to invoking the repository methods. Which says more?
        return _dbContext.Trucks.Where(x => x.State == 3).ToList();
    }
    public IEnumerable<Truck> Find(string text)
    {
        return _dbContext.Trucks.Where(x => x.ModelName.StartsWith(text)).ToList();
    }
}
```

## Unit of work

La implementación de UnitOfWork es simple para Entity Framework:

```csharp
public class EntityFrameworkUnitOfWork : IUnitOfWork
{
    private readonly DbContext _context;
    public EntityFrameworkUnitOfWork(DbContext context)
    {
        _context = context;
    }
    public void Dispose()
    {

    }
    public void SaveChanges()
    {
        _context.SaveChanges();
    }
}
```
