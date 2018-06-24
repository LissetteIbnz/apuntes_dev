# ENTITY FRAMEWORK

Tabla de contenidos

- [Net](#net)
- [Core](#core)
  - [Propiedades shadow](#propiedades-shadow)

## NET

## CORE

> https://www.learnentityframeworkcore.com/

### Propiedades shadow

Las propiedades de sombreado en Entity Framework Core son propiedades que no aparecen como parte de la clase de entidad pero que se pueden incluir en el modelo y se asignan a columnas de base de datos.

Las propiedades de sombreado son útiles en varios escenarios. Por ejemplo, se pueden usar para extender un modelo en el que no tiene acceso al código fuente de las clases de entidad. También se pueden usar en los casos en los que prefiera que las definiciones de clase de sus entidades no incluyan "artefactos relacionales" como columnas de clave externa o propiedades de metadatos como DateCreated o LastUpdated, o una propiedad rowversion .

Las propiedades de sombra se pueden configurar mediante la API Fluent en el OnModelCreating método. Este ejemplo ilustra una LastUpdated propiedad de sombra que se configura en la Contact entidad:

```csharp
public class SampleContext : DbContext
{
    public DbSet<Contact> Contacts { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
         modelBuilder.Entity<Contact>()
            .Property<DateTime>("LastUpdated");
    } 
}

public class Contact
{
    public int ContactId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; } 
}
```

#### Establecer el valor de las propiedades shadow

Puede acceder a la propiedad shadow a través de la DbContext.Entry propiedad y establecer su valor a través de la CurrentValue propiedad:

```csharp
var context = new SampleContext();
var contact = new Contact { FirstName = "John", LastName = "Doe" };
context.Add(contact);
context.Entry(contact).Property("LastUpdated").CurrentValue = DateTime.UtcNow;
context.SaveChanges();
```

También puede establecer valores a través de la ChangeTrackerAPI a través de su Entries() método. Esto ofrece una forma más lógica de establecer un LastUpdated valor anulando el SaveChanges método:

```csharp
public class SampleContext:  DbContext
{
    protected override void OnModelBuilding(ModelBuider modelBuilder)
    {
        modelBuilder.Entity<Contact>()
            .Property<DateTime>("LastUpdated");
    }

    public override int SaveChanges()
    {
        ChangeTracker.DetectChanges();
 
        foreach (var entry in ChangeTracker.Entries())
        {
            if(entry.State == EntityState.Added || entry.State == EntityState.Modified)
            {
                entry.Property("LastUpdated").CurrentValue = DateTime.UtcNow;
            }
        }
        return base.SaveChanges();
    }

    public DbSet<Contact> Contacts { get; set; }
}

public class Contact
{
    public int ContactId { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; } 
}
```

#### Consultando con propiedades shadow

Las propiedades shadow se pueden referenciar en consultas LINQ a través del Property método estático de la EF clase de utilidad:

```csharp
var contacts = context.Contacts.OrderBy(contact => EF.Property<DateTime>(contact, "LastUpdated"));

```
