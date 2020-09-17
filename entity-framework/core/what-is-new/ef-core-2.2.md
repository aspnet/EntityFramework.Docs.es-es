---
title: Novedades de EF Core 2.2 - EF Core
description: Cambios y mejoras en Entity Framework Core 2.2
author: divega
ms.date: 11/14/2018
uid: core/what-is-new/ef-core-2.2
ms.openlocfilehash: 68e3cbd5c7345330a47f1457c9b096fee5dd49e9
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072334"
---
# <a name="new-features-in-ef-core-22"></a>Novedades de EF Core 2.2

## <a name="spatial-data-support"></a>Compatibilidad con datos espaciales

Los datos espaciales pueden usarse para representar la ubicación física y la forma de los objetos.
Muchas bases de datos pueden almacenar, indexar y consultar datos espaciales de forma nativa.
Entre los escenarios habituales se incluye la consulta de objetos dentro de una distancia determinada y la prueba de si un polígono contiene una ubicación determinada.
EF Core 2.2 ahora admite trabajar con datos espaciales de varias bases de datos utilizando tipos de la biblioteca [ NetTopologySuite ](https://github.com/NetTopologySuite/NetTopologySuite) (NTS).

La compatibilidad con datos espaciales se implementa como una serie de paquetes de extensión específicos del proveedor.
Cada uno de estos paquetes contribuye a las asignaciones de tipos y métodos de NTS y los correspondientes tipos espaciales y funciones en la base de datos.
Estas extensiones de proveedor ahora están disponibles para [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/), [SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) y [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (del [proyecto Npgsql](https://www.npgsql.org/)).
Los tipos espaciales pueden usarse directamente con el [proveedor en memoria de EF Core](xref:core/providers/in-memory/index) sin extensiones adicionales.

Una vez que se instala la extensión del proveedor, puede agregar propiedades de los tipos admitidos a las entidades. Por ejemplo:

``` csharp
using NetTopologySuite.Geometries;

namespace MyApp
{
  public class Friend
  {
    [Key]
    public string Name { get; set; }
  
    [Required]
    public Point Location { get; set; }
  }
}
```

Luego puede guardar entidades con datos espaciales:

``` csharp
using (var context = new MyDbContext())
{
    context.Add(
        new Friend
        {
            Name = "Bill",
            Location = new Point(-122.34877, 47.6233355) {SRID = 4326 }
        });
    context.SaveChanges();
}
```

Y puede ejecutar consultas de base de datos basadas en datos y operaciones espaciales:

``` csharp
  var nearestFriends =
      (from f in context.Friends
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

Para obtener más información sobre esta característica, consulte la [documentación sobre tipos espaciales](xref:core/modeling/spatial).

## <a name="collections-of-owned-entities"></a>Colecciones de entidades en propiedad

EF Core 2.0 agregó la capacidad de modelar la propiedad en asociaciones de uno a uno.
EF Core 2.2 extiende la capacidad de expresar la propiedad a asociaciones de uno a varios.
La propiedad ayuda a restringir el modo en que se usan las entidades.

Por ejemplo, las entidades en propiedad:

- Solo pueden aparecer en las propiedades de navegación de otros tipos de entidad.
- Se cargan automáticamente, y solo se puede hacer su seguimiento por un DbContext junto con su propietario.

En bases de datos relacionales, las colecciones en propiedad se asignan a tablas independientes del propietario, al igual que las asociaciones regulares de uno a varios.
Pero en las bases de datos orientadas a documentos, tenemos previsto anidar entidades en propiedad (en colecciones o referencias en propiedad) dentro del mismo documento que el propietario.

Puede usar la característica mediante una llamada a la nueva API OwnsMany():

``` csharp
modelBuilder.Entity<Customer>().OwnsMany(c => c.Addresses);
```

Para obtener más información, consulte la [documentación actualizada de entidades en propiedad](xref:core/modeling/owned-entities#collections-of-owned-types).

## <a name="query-tags"></a>Etiquetas de consulta

Esta característica simplifica la correlación de las consultas LINQ en el código con las consultas SQL generadas capturadas en los registros.

Para aprovechar las ventajas de las etiquetas de consulta, anote una consulta LINQ mediante el nuevo método TagWith().
Uso de la consulta espacial de un ejemplo anterior:

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith(@"This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

Esta consulta LINQ producirá la siguiente salida SQL:

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

Para obtener más información, vea la [documentación de etiquetas de consulta](xref:core/querying/tags).
