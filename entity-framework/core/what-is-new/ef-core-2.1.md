---
title: Novedades de EF Core 2.1 - EF Core
description: Cambios y mejoras en Entity Framework Core 2.1
author: divega
ms.date: 02/20/2018
ms.assetid: 585F90A3-4D5A-4DD1-92D8-5243B14E0FEC
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: 5aa8aa5d3578b9f69e63adaf731a1642f748c5c2
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618753"
---
# <a name="new-features-in-ef-core-21"></a>Novedades de EF Core 2.1

Además de numerosas correcciones de errores y pequeñas mejoras funcionales y de rendimiento, EF Core 2.1 incluye algunas características nuevas muy atractivas:

## <a name="lazy-loading"></a>Carga diferida

EF Core contiene ahora los bloques de creación necesarios para quienes quieran crear clases de entidad que puedan cargar las propiedades de navegación a petición. También hemos creado otro paquete, Microsoft.EntityFrameworkCore.Proxies, que aprovecha los bloques de creación para generar clases proxy de carga diferida basadas en clases de entidad apenas modificadas (por ejemplo, clases con propiedades de navegación virtual).

Consulte la [sección sobre cargas diferidas](xref:core/querying/related-data#lazy-loading) para obtener más información sobre el tema.

## <a name="parameters-in-entity-constructors"></a>Parámetros en constructores de entidad

Como uno de los bloques de creación necesarios para la carga diferida, se habilita la creación de entidades que aceptan parámetros en sus constructores. Puede usar parámetros para insertar valores de propiedad, delegados de carga diferida y servicios.

Consulte la [sección sobre constructores de entidad con parámetros](xref:core/modeling/constructors) para obtener más información sobre el tema.

## <a name="value-conversions"></a>Conversiones de valores

Hasta ahora, EF Core solo podía asignar propiedades de tipos admitidas de forma nativa por el proveedor de bases de datos subyacente. Los valores se copiaban de un lado a otro entre las columnas y las propiedades sin ninguna transformación. A partir de EF Core 2.1, pueden aplicarse conversiones de valores para transformar los valores obtenidos en las columnas antes de que se apliquen a las propiedades, y viceversa. Tenemos varias conversiones que pueden aplicarse por convención según sea necesario, así como una API de configuración explícita que permite registrar conversiones personalizadas entre columnas y propiedades. Algunas de las aplicaciones de esta característica son:

- Almacenamiento de enumeraciones como cadenas
- Asignación de enteros sin signo con SQL Server
- Cifrado y descifrado automáticos de valores de propiedad

Consulte la [sección sobre conversiones de valores](xref:core/modeling/value-conversions) para obtener más información sobre el tema.  

## <a name="linq-groupby-translation"></a>Traslación de GroupBy de LINQ

Antes de la versión 2.1, el operador GroupBy de LINQ en EF Core siempre se evaluaba en la memoria. Ahora se admite su traslación a la cláusula GROUP BY de SQL en los casos más comunes.

En este ejemplo se muestra una consulta con GroupBy utilizada para calcular diversas funciones de agregado:

``` csharp
var query = context.Orders
    .GroupBy(o => new { o.CustomerId, o.EmployeeId })
    .Select(g => new
        {
          g.Key.CustomerId,
          g.Key.EmployeeId,
          Sum = g.Sum(o => o.Amount),
          Min = g.Min(o => o.Amount),
          Max = g.Max(o => o.Amount),
          Avg = g.Average(o => o.Amount)
        });
```

La traslación correspondiente a SQL tiene este aspecto:

``` SQL
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a>Propagación de datos

Con la nueva versión, será posible proporcionar datos iniciales para rellenar una base de datos. A diferencia de en EF6, la propagación de datos está asociada a un tipo de entidad como parte de la configuración del modelo. Las migraciones de EF Core pueden luego calcular automáticamente las operaciones de inserción, actualización y eliminación que hay que aplicar al actualizar la base de datos a una nueva versión del modelo.

Por ejemplo, esto se puede usar para configurar los datos de inicialización de un método POST en `OnModelCreating`:

``` csharp
modelBuilder.Entity<Post>().HasData(new Post{ Id = 1, Text = "Hello World!" });
```

Consulte la [sección sobre propagación de datos](xref:core/modeling/data-seeding) para obtener más información sobre el tema.  

## <a name="query-types"></a>Tipos de consulta

Un modelo de EF Core ahora puede incluir tipos de consulta. A diferencia de los tipos de entidad, los tipos de consulta no tienen claves definidas en ellos y no se pueden insertar, eliminar ni actualizar (es decir, son de solo lectura), pero se pueden devolver directamente en las consultas. Algunos de los escenarios de uso para los tipos de consulta son:

- Asignar a vistas sin claves principales
- Asignar a tablas sin claves principales
- Asignar a consultas definidas en el modelo
- Actuar como tipo de valor devuelto en consultas `FromSql()`

Consulte la [sección sobre tipos de consulta](xref:core/modeling/keyless-entity-types) para obtener más información sobre el tema.

## <a name="include-for-derived-types"></a>Include en tipos derivados

Ahora será posible especificar propiedades de navegación definidas solo en tipos derivados al escribir expresiones para el método `Include`. Para la versión fuertemente tipada de `Include`, se admite el uso de una conversión explícita o el operador `as`. Ahora también se admite hacer referencia a los nombres de propiedad de navegación definidos en tipos derivados en la versión de cadena de `Include`:

``` csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

Consulte la [sección sobre Include con tipos derivados](xref:core/querying/related-data#include-on-derived-types) para obtener más información sobre el tema.

## <a name="systemtransactions-support"></a>System.Transactions

Se ha agregado la posibilidad de trabajar con características de System.Transactions tales como TransactionScope. Esto funcionará en .NET Framework y en .NET Core cuando se usen proveedores de bases de datos que lo admitan.

Consulte la [sección sobre System.Transactions](xref:core/saving/transactions#using-systemtransactions) para obtener más información sobre el tema.

## <a name="better-column-ordering-in-initial-migration"></a>Mejor ordenación de columnas en la migración inicial

En función de los comentarios de clientes, hemos actualizado las migraciones para que las columnas de tablas se generen inicialmente en el mismo orden en que se declaran las propiedades en clases. Tenga en cuenta que EF Core no puede cambiar el orden cuando se agregan nuevos miembros después de la creación de la tabla inicial.

## <a name="optimization-of-correlated-subqueries"></a>Optimización de subconsultas correlacionadas

Se ha mejorado la traslación de consultas para evitar la ejecución de "N + 1" consultas SQL en muchos escenarios comunes en los que el uso de una propiedad de navegación en la proyección conduce a unir los datos de la consulta raíz con los datos de una subconsulta correlacionada. La optimización requiere el almacenamiento en búfer de los resultados de la subconsulta, y hay que modificar la consulta para que participe en el nuevo comportamiento.

Por ejemplo, la siguiente consulta normalmente se traslada a una consulta para clientes, más N consultas separadas para pedidos (donde "N" corresponde al número de clientes devueltos):

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

Al incluir `ToList()` en el lugar correcto, se indica que el almacenamiento en búfer es adecuado para los pedidos, lo que permite la optimización:

``` csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

Tenga en cuenta que esta consulta solo se trasladará a dos consultas SQL: una para clientes y la siguiente para pedidos.

## <a name="owned-attribute"></a>Atributo [Owned]

Ahora es posible configurar [tipos de entidad en propiedad](xref:core/modeling/owned-entities) anotando simplemente el tipo con `[Owned]` y asegurándose luego de que la entidad de propietario se agrega al modelo:

``` csharp
[Owned]
public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}
```

## <a name="command-line-tool-dotnet-ef-included-in-net-core-sdk"></a>Herramienta de línea de comandos dotnet-ef incluida en el SDK de .NET Core

Los comandos de _dotnet-ef_ ahora forman parte del SDK de .NET Core, así que ya no es necesario usar DotNetCliToolReference en el proyecto para poder usar migraciones o para aplicar la técnica scaffolding a DbContext desde una base de datos existente.

Vea la sección sobre [cómo instalar las herramientas](xref:core/miscellaneous/cli/dotnet#installing-the-tools) para obtener más información sobre cómo habilitar herramientas de línea de comandos para diferentes versiones del SDK de .NET Core y EF Core.

## <a name="microsoftentityframeworkcoreabstractions-package"></a>Paquete Microsoft.EntityFrameworkCore.Abstractions

El nuevo paquete contiene atributos e interfaces que puede usar en los proyectos para activar características de EF Core sin depender de EF Core como un todo. Por ejemplo, el atributo [Owned] y la interfaz de ILazyLoader se encuentran aquí.

## <a name="state-change-events"></a>Eventos de cambio de estado

Los nuevos eventos `Tracked` y `StateChanged` de `ChangeTracker` se pueden usar para escribir lógica que reaccione a las entidades que entran en DbContext o que cambian su estado.

## <a name="raw-sql-parameter-analyzer"></a>Analizador de parámetros de SQL sin formato

Un nuevo analizador de código se incluye en EF Core que detecta los usos potencialmente poco seguros de nuestras API de SQL sin formato, como `FromSql` o `ExecuteSqlCommand`. Por ejemplo, para la consulta siguiente, verá una advertencia porque _minAge_ no tiene parámetros:

``` csharp
var sql = $"SELECT * FROM People WHERE Age > {minAge}";
var query = context.People.FromSql(sql);
```

## <a name="database-provider-compatibility"></a>Compatibilidad del proveedor de bases de datos

Se recomienda usar EF Core 2.1 con proveedores que se hayan actualizado o que al menos se haya comprobado que funcionan con EF Core 2.1.

> [!TIP]
> Si encuentra alguna incompatibilidad inesperada o algún problema en las nuevas características o si tiene comentarios sobre ellas, notifíquelos mediante [nuestro rastreador de problemas](https://github.com/aspnet/EntityFrameworkCore/issues/new).
