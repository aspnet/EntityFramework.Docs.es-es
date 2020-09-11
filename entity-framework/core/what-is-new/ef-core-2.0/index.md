---
title: Novedades de EF Core 2.0 - EF Core
description: Cambios y mejoras en Entity Framework Core 2.0
author: divega
ms.date: 02/20/2018
ms.assetid: 2CB5809E-0EFB-44F6-AF14-9D5BFFFBFF9D
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: e6683e2b70bce2b07881f3bfd047d54768b988f6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620672"
---
# <a name="new-features-in-ef-core-20"></a>Nuevas características de EF Core 2.0

## <a name="net-standard-20"></a>.NET Standard 2.0

EF Core tiene ahora como destino .NET Standard 2.0, lo que significa que puede trabajar con .NET Core 2.0, .NET Framework 4.6.1 y otras bibliotecas que implementan .NET Standard 2.0.
Vea [Implementaciones de .NET compatibles](xref:core/platforms/index) para obtener más detalles sobre lo que se admite.

## <a name="modeling"></a>Modelado

### <a name="table-splitting"></a>División de tablas

Ahora es posible asignar dos o más tipos de entidad a la misma tabla en la que se van a compartir las columnas de clave principal y cada fila va a corresponder a dos o más entidades.

Para usar la división de tabla, debe configurarse una relación de identificación (donde las propiedades de clave externa forman la clave principal) entre todos los tipos de entidad que comparten la tabla:

``` csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

Consulte la [sección sobre la división de las tablas](xref:core/modeling/table-splitting) para obtener más información sobre esta característica.

### <a name="owned-types"></a>Tipos de propiedad

Un tipo de entidad en propiedad puede compartir el mismo tipo .NET con otro tipo de entidad en propiedad, pero, dado que no se puede identificar simplemente por el tipo .NET, debe haber una navegación a él desde otro tipo de entidad. La entidad que contiene la navegación definitoria es el propietario. Al consultar al propietario, los tipos de propiedad se incluyen de forma predeterminada.

Por convención, se crea una clave principal paralela para el tipo de propiedad y se asigna a la misma tabla que el propietario mediante la división de tabla. Esto permite usar tipos de propiedad de forma similar al modo en que se usan los tipos complejos en EF6:

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, cb =>
    {
        cb.OwnsOne(c => c.BillingAddress);
        cb.OwnsOne(c => c.ShippingAddress);
    });

public class Order
{
    public int Id { get; set; }
    public OrderDetails OrderDetails { get; set; }
}

public class OrderDetails
{
    public StreetAddress BillingAddress { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```

Consulte la [sección sobre tipos de entidad en propiedad](xref:core/modeling/owned-entities) para obtener más información sobre esta característica.

### <a name="model-level-query-filters"></a>Filtros de consulta de nivel de modelo

EF Core 2.0 incluye una nueva característica que se denomina filtros de consulta de nivel de modelo. Esta característica permite que los predicados de consulta LINQ (una expresión booleana que normalmente se pasa al operador de consulta Where de LINQ) se definan directamente en tipos de entidad del modelo de metadatos (normalmente en OnModelCreating). Estos filtros se aplican automáticamente a las consultas LINQ que implican a esos tipos de entidad, incluidos aquellos a los que se hace referencia de forma indirecta, por ejemplo mediante el uso de Include o de referencias de propiedad de navegación directas. Algunas aplicaciones comunes de esta característica son:

- Eliminación temporal: un tipo de entidad define una propiedad IsDeleted.
- Servicios multiinquilino: un tipo de entidad define una propiedad TenantId.

Este es un ejemplo sencillo que muestra la característica para los dos escenarios mencionados arriba:

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    public int TenantId { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>().HasQueryFilter(
            p => !p.IsDeleted
            && p.TenantId == this.TenantId);
    }
}
```

Se define un filtro de nivel de modelo que implementa los servicios multiinquilino y la eliminación temporal para instancias del tipo de entidad `Post`. Observe el uso de una propiedad de nivel de instancia `DbContext`: `TenantId`. Los filtros de nivel de modelo usan el valor de la instancia de contexto correcta (es decir, la instancia de contexto que está ejecutando la consulta).

Los filtros se pueden deshabilitar para consultas LINQ individuales mediante el operador IgnoreQueryFilters().

#### <a name="limitations"></a>Limitaciones

- No se permiten las referencias de navegación. Esta característica se puede agregar en función de los comentarios.
- Solo se pueden definir filtros en el tipo de entidad raíz de una jerarquía.

### <a name="database-scalar-function-mapping"></a>Asignación de función escalar de base de datos

EF Core 2.0 incluye una importante contribución de [Paul Middleton](https://github.com/pmiddleton) que permite la asignación de funciones escalares de base de datos a stubs de método para que puedan usarse en consultas LINQ y trasladarse a SQL.

Esta es una breve descripción de cómo se puede usar la característica:

Declare un método estático en `DbContext` y anótelo con `DbFunctionAttribute`:

``` csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new NotImplementedException();
    }
}
```

Los métodos como este se registran automáticamente. Una vez registrados, las llamadas al método de una consulta LINQ pueden trasladarse a llamadas a funciones de SQL:

``` csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

Algunas observaciones:

- Por convención, el nombre del método se usa como nombre de una función (en este caso una función definida por el usuario) al generar el código SQL, pero puede invalidar el nombre y el esquema durante el registro del método.
- Actualmente solo se admiten las funciones escalares.
- Debe crear la función asignada en la base de datos. La migraciones de EF Core no se encargarán de crearla.

### <a name="self-contained-type-configuration-for-code-first"></a>Configuración de tipo independiente para Code First

En EF6 era posible encapsular la configuración de Code First de un tipo de entidad concreto al derivarlo de *EntityTypeConfiguration*. En EF Core 2.0 se vuelve a incluir este patrón:

``` csharp
class CustomerConfiguration : IEntityTypeConfiguration<Customer>
{
    public void Configure(EntityTypeBuilder<Customer> builder)
    {
        builder.HasKey(c => c.AlternateKey);
        builder.Property(c => c.Name).HasMaxLength(200);
    }
}

...
// OnModelCreating
builder.ApplyConfiguration(new CustomerConfiguration());
```

## <a name="high-performance"></a>Alto rendimiento

### <a name="dbcontext-pooling"></a>Agrupación de DbContext

El patrón básico para usar EF Core en una aplicación de ASP.NET Core normalmente implica el registro de un tipo de DbContext personalizado en el sistema de inserción de dependencias y la posterior obtención de instancias de ese tipo a través de los parámetros del constructor de los controladores. Esto significa que se crea una nueva instancia de DbContext para cada solicitud.

En la versión 2.0 se incorpora una nueva manera de registrar tipos de DbContext personalizados en la inserción de dependencias que presenta un grupo de instancias de DbContext reutilizables de forma transparente. Para usar la agrupación de DbContext, use `AddDbContextPool` en lugar de `AddDbContext` durante el registro del servicio:

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

Si se usa este método, en el momento en que un controlador solicita una instancia de DbContext, primero se comprueba si hay una disponible en el grupo. Una vez que termina el procesamiento de la solicitud, se restablece cualquier estado en la instancia y la propia instancia se devuelve al grupo.

Esto es conceptualmente similar a la forma en que funciona la agrupación de conexiones en los proveedores de ADO.NET y tiene la ventaja de ahorrar algunos de los costos de inicialización de la instancia de DbContext.

### <a name="limitations"></a>Limitaciones

El nuevo método presenta algunas limitaciones con respecto a lo que se puede hacer en el método `OnConfiguring()` de DbContext.

> [!WARNING]  
> Evite el uso de la agrupación de DbContext si mantiene su propio estado (por ejemplo, campos privados) en la clase derivada DbContext que no debe compartirse con otras solicitudes. EF Core solo restablece el estado del que está informado antes de agregar una instancia de DbContext al grupo.

### <a name="explicitly-compiled-queries"></a>Consultas compiladas de manera explícita

Esta es la segunda característica de rendimiento opcional diseñada para ofrecer ventajas en escenarios de gran escala.

Las API de consulta compiladas de forma manual o explícita han estado disponibles en versiones anteriores de EF y también en LINQ to SQL para permitir que las aplicaciones almacenen en caché la traducción de consultas de modo que se puedan calcular una sola vez y ejecutarse muchas veces.

Aunque en general EF Core puede compilar y almacenar en caché automáticamente las consultas en función de una representación con hash de las expresiones de consulta, este mecanismo puede usarse para obtener una pequeña mejora de rendimiento al omitir el cálculo del hash y la búsqueda en caché, lo que permite que la aplicación use una consulta ya compilada mediante la invocación de un delegado.

``` csharp
// Create an explicitly compiled query
private static Func<CustomerContext, int, Customer> _customerById =
    EF.CompileQuery((CustomerContext db, int id) =>
        db.Customers
            .Include(c => c.Address)
            .Single(c => c.Id == id));

// Use the compiled query by invoking it
using (var db = new CustomerContext())
{
   var customer = _customerById(db, 147);
}
```

## <a name="change-tracking"></a>Seguimiento de cambios

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a>La asociación permite realizar un seguimiento de un gráfico de entidades nuevas y existentes.

EF Core admite la generación automática de valores de clave a través de una serie de mecanismos. Al usar esta característica, se genera un valor si la propiedad de clave es el valor predeterminado de CLR, normalmente cero o null. Esto significa que se puede pasar un gráfico de entidades a `DbContext.Attach` o `DbSet.Attach` y que EF Core marca aquellas entidades que tienen una clave ya establecida como `Unchanged`, mientras que las que no tienen establecida una clave se marcan como `Added`. Esto facilita la tarea de asociar un gráfico de entidades mixtas nuevas y existentes al usar claves generadas. `DbContext.Update` y `DbSet.Update` funcionan de la misma manera, salvo que las entidades con una clave establecida se marcan como `Modified` en lugar de `Unchanged`.

## <a name="query"></a>Consulta

### <a name="improved-linq-translation"></a>Traducción de LINQ mejorada

Permite que más consultas se ejecuten correctamente, con más lógica evaluada en la base de datos (en lugar de en memoria) y menos datos innecesariamente recuperados de la base de datos.

### <a name="groupjoin-improvements"></a>Mejoras de GroupJoin

Este trabajo mejora el SQL que se genera para las combinaciones agrupadas. Las combinaciones agrupadas suelen ser un resultado de subconsultas en propiedades de navegación opcionales.

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a>Interpolación de cadenas en FromSql y ExecuteSqlCommand

C# 6 presentó la interpolación de cadenas, una característica que permite insertar expresiones de C# directamente en literales de cadena, lo que proporciona una forma útil de compilar cadenas en tiempo de ejecución. En EF Core 2.0 se ha agregado compatibilidad especial con las cadenas interpoladas a las dos API principales que aceptan cadenas SQL sin formato: `FromSql` y `ExecuteSqlCommand`. Esta nueva compatibilidad permite que la interpolación de cadenas de C# se use de forma "segura". Es decir, de una forma que protege frente a errores de inserción de SQL comunes que pueden producirse al crear SQL de forma dinámica en tiempo de ejecución.

A continuación se muestra un ejemplo:

``` csharp
var city = "London";
var contactTitle = "Sales Representative";

using (var context = CreateContext())
{
    context.Set<Customer>()
        .FromSql($@"
            SELECT *
            FROM ""Customers""
            WHERE ""City"" = {city} AND
                ""ContactTitle"" = {contactTitle}")
            .ToArray();
  }
```

En este ejemplo hay dos variables insertadas en la cadena de formato SQL. EF Core genera el SQL siguiente:

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a>EF.Functions.Like()

Se ha agregado la propiedad EF.Functions, que EF Core o los proveedores pueden usar para definir métodos que se asignen a los operadores o a las funciones de base de datos de forma que se puedan invocar en consultas LINQ. El primer ejemplo de este método es Like():

``` csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%")
    select c;
```

Observe que Like() incluye una implementación en memoria, lo que puede resultar útil al trabajar en una base de datos en memoria o cuando es necesario evaluar el predicado en el lado cliente.

## <a name="database-management"></a>Administración de bases de datos

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a>Enlace de pluralización para scaffolding de DbContext

EF Core 2.0 presenta un nuevo servicio *IPluralizer* que se usa para singularizar nombres de tipo de entidad y pluralizar nombres DbSet. La implementación predeterminada no está operativa, por lo que simplemente se trata de un enlace en el que los usuarios pueden conectar fácilmente su propio pluralizador.

Este es el aspecto del enlace de un desarrollador de su propio pluralizador:

``` csharp
public class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
    {
        services.AddSingleton<IPluralizer, MyPluralizer>();
    }
}

public class MyPluralizer : IPluralizer
{
    public string Pluralize(string name)
    {
        return Inflector.Inflector.Pluralize(name) ?? name;
    }

    public string Singularize(string name)
    {
        return Inflector.Inflector.Singularize(name) ?? name;
    }
}
```

## <a name="others"></a>Otros

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a>Traslado del proveedor de SQLite de ADO.NET a SQLitePCL.raw

Esto proporciona una solución más robusta en Microsoft.Data.Sqlite para distribuir archivos binarios nativos de SQLite en distintas plataformas.

### <a name="only-one-provider-per-model"></a>Solo un proveedor por modelo

Mejora considerablemente la forma en que los proveedores pueden interactuar con el modelo y simplifica el funcionamiento de las convenciones, las anotaciones y las API fluidas con distintos proveedores.

EF Core 2.0 ahora compila un elemento [IModel](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/Metadata/IModel.cs) diferente para cada proveedor que se va a usar. Esto suele ser transparente para la aplicación. Esto ha permitido una simplificación de las API de metadatos de nivel inferior, de modo que cualquier acceso a *conceptos de metadatos relacionales comunes* siempre se realiza mediante una llamada a `.Relational` en lugar de a `.SqlServer`, `.Sqlite`, etc.

### <a name="consolidated-logging-and-diagnostics"></a>Registro y diagnóstico consolidados

Los mecanismos de registro (basados en ILogger) y diagnóstico (basados en DiagnosticSource) ahora comparten más código.

Los identificadores de evento de los mensajes enviados a un elemento ILogger han cambiado en 2.0. Los identificadores de evento ahora son únicos en el código de EF Core. Ahora, estos mensajes también siguen el patrón estándar de registro estructurado que usa, por ejemplo, MVC.

Las categorías de registrador también han cambiado. Ahora hay un conjunto conocido de categorías a las que se accede a través de [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/master/src/EFCore/DbLoggerCategory.cs).

Los eventos de DiagnosticSource ahora usan los mismos nombres de identificador de evento que los mensajes de `ILogger` correspondientes.
