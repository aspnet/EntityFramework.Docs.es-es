---
title: Características nuevas de Entity Framework Core 3.0 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: 39684cdcc17e3baa4b77cf29d54b626294771332
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103144"
---
# <a name="new-features-in-entity-framework-core-30"></a>Características nuevas de Entity Framework Core 3.0

En la lista siguiente se incluyen las principales características nuevas de EF Core 3.0.

Como versión principal, EF Core 3.0 también presenta varios [cambios importantes](xref:core/what-is-new/ef-core-3.0/breaking-changes), que son mejoras en la API que podrían afectar negativamente a las aplicaciones existentes.  

## <a name="linq-overhaul"></a>Revisión de LINQ

LINQ permite escribir consultas a la base de datos en el lenguaje .NET que prefiera, con lo que se aprovecha la información de tipo enriquecido para ofrecer la comprobación de IntelliSense y de tipos en tiempo de compilación.
Pero LINQ también permite escribir un número ilimitado de consultas complicadas que contienen expresiones arbitrarias (llamadas a métodos u operaciones).
Cómo controlar todas esas combinaciones es el principal desafío para los proveedores LINQ.

En EF Core 3,0, hemos rediseñado nuestro proveedor LINQ para habilitar la conversión de más patrones de consulta en SQL, la generación de consultas eficientes en más casos y la prevención de que las consultas ineficaces no se detecten. El nuevo proveedor LINQ es la base sobre la que podremos ofrecer nuevas funcionalidades de consulta y mejoras de rendimiento en futuras versiones, sin interrumpir las aplicaciones y los proveedores de datos existentes.

### <a name="restricted-client-evaluation"></a>Evaluación de cliente restringida

El cambio de diseño más importante tiene que ver con la forma en que manejamos las expresiones LINQ que no se pueden convertir a parámetros ni traducir a SQL.

En las versiones anteriores, EF Core identificada qué partes de una consulta se podían traducir a SQL y ejecutaba el resto de la consulta en el cliente.
Este tipo de ejecución en el lado cliente es una opción interesante en algunas situaciones, pero en muchos otros casos puede dar lugar a consultas ineficaces.

Por ejemplo, si EF Core 2.2 no podía traducir un predicado en una llamada a `Where()`, ejecutaba una instrucción SQL sin filtro, transfería todas las filas de la base de datos y luego las filtraba en memoria:

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

Esta operación puede ser aceptable si la base de datos contiene pocas filas, pero puede dar lugar a problemas de rendimiento considerables o incluso errores en la aplicación si la base de datos contiene muchas filas.

En EF Core 3.0 hemos restringido la evaluación de cliente para que solo suceda en la proyección de nivel superior (fundamentalmente, la última llamada a `Select()`).
Cuando EF Core 3.0 detecta expresiones que no se pueden traducir en ningún otro lugar de la consulta, produce una excepción en tiempo de ejecución.

Para evaluar una condición de predicado en el cliente como en el ejemplo anterior, los desarrolladores ahora tienen que cambiar explícitamente la evaluación de la consulta a LINQ to Objects:

``` csharp
var specialCustomers = context.Customers
    .Where(c => c.Name.StartsWith(n))
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

Consulte la [documentación sobre cambios importantes](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) para más detalles sobre cómo esto puede afectar a las aplicaciones existentes.

### <a name="single-sql-statement-per-linq-query"></a>Instrucción SQL única por consulta LINQ

Otro aspecto del diseño que cambió significativamente en la versión 3.0 es que ahora siempre se genera una única instrucción SQL por cada consulta LINQ. En versiones anteriores, se usaba para generar varias instrucciones SQL en ciertos casos, llamadas `Include()` traducidas en las propiedades de navegación de la colección y consultas traducidas que seguían determinados patrones con subconsultas. Aunque en ocasiones este diseño resultaba práctico y, en el caso de `Include()`, incluso ayudaba a evitar el envío de datos redundantes a través de la conexión, la implementación era compleja y se producían algunos comportamientos considerablemente ineficaces (consultas N+1). Había situaciones en las que los datos devueltos en varias consultas eran incoherentes en potencia.

De forma similar a la evaluación del cliente, si EF Core 3.0 no puede convertir una consulta LINQ en una única instrucción SQL, se inicia una excepción en tiempo de ejecución. Pero hicimos que EF Core fuera capaz de traducir muchos de los patrones comunes que solían generar varias consultas en una sola consulta con JOIN.

## <a name="cosmos-db-support"></a>Compatibilidad con Cosmos DB

Con el proveedor de Cosmos DB para EF Core, los desarrolladores que están familiarizados con el modelo de programación de EF puedan usar fácilmente Azure Cosmos DB como base de datos de aplicación. El objetivo es hacer que algunas de las ventajas de Cosmos DB, como la distribución global, la disponibilidad "AlwaysOn", la escalabilidad elástica y la baja latencia, sean aún más accesibles para los desarrolladores de .NET. El proveedor habilita la mayoría de las características de EF Core, como el seguimiento automático de cambios, LINQ y conversiones de valores, en comparación con SQL API de Cosmos DB.

Consulte la [documentación del proveedor Cosmos DB](xref:core/providers/cosmos/index) para más detalles.

## <a name="c-80-support"></a>Compatibilidad con C# 8.0

EF Core 3.0 aprovecha varias [características nuevas de C# 8.0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8):

### <a name="asynchronous-streams"></a>Secuencias asincrónicas

Los resultados de la consulta asincrónica se exponen ahora mediante la nueva interfaz de `await foreach` estándar y se pueden usar con `IAsyncEnumerable<T>`.

``` csharp
var orders =
    from o in context.Orders
    where o.Status == OrderStatus.Pending
    select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
    Process(o);
}
```

Consulte las [transmisiones asíncronas en la documentación de C#](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) para más detalles.

### <a name="nullable-reference-types"></a>Tipos de referencia que aceptan valores NULL

Cuando esta nueva característica está habilitada en el código, EF Core examina la nulabilidad de las propiedades de tipo de referencia y la aplica a las columnas y relaciones correspondientes en la base de datos: las propiedades de los tipos de referencia no anulables se tratan como si tuvieran el atributo de anotación de datos `[Required]`.

Por ejemplo, en la clase siguiente, las propiedades marcadas como de tipo `string?` se configurarán como opcionales, mientras que `string` se configurará según sea necesario:

``` csharp
public class Customer
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string? MiddleName { get; set; }
}
```

Consulte [Trabajar con tipos de referencia que aceptan valores NULL](xref:core/miscellaneous/nullable-reference-types) en la documentación de EF Core para más detalles.

## <a name="interception-of-database-operations"></a>Intercepción de operaciones de bases de datos

La nueva API de intercepción en EF Core 3.0 permite proporcionar una lógica personalizada que se invoca automáticamente cada vez que se producen operaciones de base de datos de bajo nivel como parte del funcionamiento normal de EF Core. Por ejemplo, al abrir conexiones, confirmar transacciones o ejecutar comandos.

De manera similar a las características de intercepción que existían en EF 6, los interceptores le permiten interceptar operaciones antes o después de que sucedan. Cuando las intercepta antes de que sucedan, puede omitir la ejecución y proporcionar resultados alternativos de la lógica de intercepción.

Por ejemplo, para manipular el texto del comando, puede crear `DbCommandInterceptor`:

``` csharp
public class HintCommandInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(
        DbCommand command,
        CommandEventData eventData,
        InterceptionResult<DbDataReader> result)
    {
        // Manipulate the command text, etc. here...
        command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
        return result;
    }
}
```

Y registrarlo con su  `DbContext`:

``` csharp
services.AddDbContext(b => b
    .UseSqlServer(connectionString)
    .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a>Ingeniería inversa de vistas de base de datos

El nombre de los tipos de consulta, que representan datos que pueden leerse de la base de datos pero no actualizarse, se ha cambiado a [tipos de entidad sin clave](xref:core/modeling/keyless-entity-types).
Como son una excelente opción para asignar vistas de bases de datos en la mayoría de los escenarios, EF Core ahora crea automáticamente tipos de entidades sin clave cuando se invierten las vistas de bases de datos de ingeniería.

Por ejemplo, con la [herramienta de línea de comandos dotnet ef](xref:core/miscellaneous/cli/dotnet), puede escribir:

```dotnetcli
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

Y la herramienta ahora anulará automáticamente los tipos de scaffold para vistas y tablas sin claves:

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Names>(entity =>
    {
        entity.HasNoKey();
        entity.ToView("Names");
    });

    modelBuilder.Entity<Things>(entity =>
    {
        entity.HasNoKey();
    });
}
```

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a>Ahora, las entidades dependientes que comparten la tabla con la entidad de seguridad son opcionales

A partir de la versión EF Core 3.0, si `OrderDetails` pertenece a `Order` o está asignado a la misma tabla explícitamente, será posible agregar `Order` sin `OrderDetails`; todas las propiedades `OrderDetails`, salvo la clave principal, se asignarán a columnas que aceptan valores NULL.

Al realizar consultas, EF Core establecerá `OrderDetails` en `null` si ninguna de las propiedades necesarias tiene un valor, o bien no tiene las propiedades necesarias más allá de la clave principal y todas las propiedades son `null`.

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a>EF 6.3 en .NET Core

Esta no es realmente una característica de EF Core 3.0, pero creemos que es importante para muchos de nuestros clientes actuales.

Entendemos que muchas aplicaciones existentes utilizan versiones anteriores de EF y que portarlas a EF Core solo para aprovechar las ventajas de .NET Core puede requerir un esfuerzo considerable.
Por ese motivo, decidimos portar a la versión más reciente de EF 6 para que se ejecute en .NET Core 3.0.

Para más detalles, consulte [Novedades de EF 6](xref:ef6/what-is-new/index).

## <a name="postponed-features"></a>Características pospuestas

Algunas características planeadas originalmente para EF Core 3.0 se pospusieron para versiones futuras:

- Capacidad de omitir partes de un modelo en migraciones, con seguimiento realizado a través del problema [nº 2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).
- Entidades contenedoras de propiedades, de las que se realiza un seguimiento a través de dos problemas independientes: [nº 9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) sobre las entidades de tipo compartido y [nº 13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) sobre la compatibilidad con la asignación de propiedades indizadas.
