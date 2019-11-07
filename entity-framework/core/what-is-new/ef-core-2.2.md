---
title: Novedades de EF Core 2.2 - EF Core
author: divega
ms.date: 11/14/2018
ms.assetid: 998C04F3-676A-4FCF-8450-CFB0457B4198
uid: core/what-is-new/ef-core-2.2
ms.openlocfilehash: fb9de799753bebd7b4092cd8f4af74703dee3e45
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656194"
---
# <a name="new-features-in-ef-core-22"></a><span data-ttu-id="e34d9-102">Novedades de EF Core 2.2</span><span class="sxs-lookup"><span data-stu-id="e34d9-102">New features in EF Core 2.2</span></span>

## <a name="spatial-data-support"></a><span data-ttu-id="e34d9-103">Compatibilidad con datos espaciales</span><span class="sxs-lookup"><span data-stu-id="e34d9-103">Spatial data support</span></span>

<span data-ttu-id="e34d9-104">Los datos espaciales pueden usarse para representar la ubicación física y la forma de los objetos.</span><span class="sxs-lookup"><span data-stu-id="e34d9-104">Spatial data can be used to represent the physical location and shape of objects.</span></span>
<span data-ttu-id="e34d9-105">Muchas bases de datos pueden almacenar, indexar y consultar datos espaciales de forma nativa.</span><span class="sxs-lookup"><span data-stu-id="e34d9-105">Many databases can natively store, index, and query spatial data.</span></span>
<span data-ttu-id="e34d9-106">Entre los escenarios habituales se incluye la consulta de objetos dentro de una distancia determinada y la prueba de si un polígono contiene una ubicación determinada.</span><span class="sxs-lookup"><span data-stu-id="e34d9-106">Common scenarios include querying for objects within a given distance, and testing if a polygon contains a given location.</span></span>
<span data-ttu-id="e34d9-107">EF Core 2.2 ahora admite trabajar con datos espaciales de varias bases de datos utilizando tipos de la biblioteca [ NetTopologySuite ](https://github.com/NetTopologySuite/NetTopologySuite) (NTS).</span><span class="sxs-lookup"><span data-stu-id="e34d9-107">EF Core 2.2 now supports working with spatial data from various databases using types from the [NetTopologySuite](https://github.com/NetTopologySuite/NetTopologySuite) (NTS) library.</span></span>

<span data-ttu-id="e34d9-108">La compatibilidad con datos espaciales se implementa como una serie de paquetes de extensión específicos del proveedor.</span><span class="sxs-lookup"><span data-stu-id="e34d9-108">Spatial data support is implemented as a series of provider-specific extension packages.</span></span>
<span data-ttu-id="e34d9-109">Cada uno de estos paquetes contribuye a las asignaciones de tipos y métodos de NTS y los correspondientes tipos espaciales y funciones en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="e34d9-109">Each of these packages contributes mappings for NTS types and methods, and the corresponding spatial types and functions in the database.</span></span>
<span data-ttu-id="e34d9-110">Estas extensiones de proveedor ahora están disponibles para [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/), [SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/) y [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (del [proyecto Npgsql](https://www.npgsql.org/)).</span><span class="sxs-lookup"><span data-stu-id="e34d9-110">Such provider extensions are now available for [SQL Server](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/), [SQLite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/), and [PostgreSQL](https://www.nuget.org/packages/Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite/) (from the [Npgsql project](https://www.npgsql.org/)).</span></span>
<span data-ttu-id="e34d9-111">Los tipos espaciales pueden usarse directamente con el [proveedor en memoria de EF Core](xref:core/providers/in-memory/index) sin extensiones adicionales.</span><span class="sxs-lookup"><span data-stu-id="e34d9-111">Spatial types can be used directly with the [EF Core in-memory provider](xref:core/providers/in-memory/index) without additional extensions.</span></span>

<span data-ttu-id="e34d9-112">Una vez que se instala la extensión del proveedor, puede agregar propiedades de los tipos admitidos a las entidades.</span><span class="sxs-lookup"><span data-stu-id="e34d9-112">Once the provider extension is installed, you can add properties of supported types to your entities.</span></span> <span data-ttu-id="e34d9-113">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="e34d9-113">For example:</span></span>

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

<span data-ttu-id="e34d9-114">Luego puede guardar entidades con datos espaciales:</span><span class="sxs-lookup"><span data-stu-id="e34d9-114">You can then persist entities with spatial data:</span></span>

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

<span data-ttu-id="e34d9-115">Y puede ejecutar consultas de base de datos basadas en datos y operaciones espaciales:</span><span class="sxs-lookup"><span data-stu-id="e34d9-115">And you can execute database queries based on spatial data and operations:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="e34d9-116">Para obtener más información sobre esta característica, consulte la [documentación sobre tipos espaciales](xref:core/modeling/spatial).</span><span class="sxs-lookup"><span data-stu-id="e34d9-116">For more information on this feature, see the [spatial types documentation](xref:core/modeling/spatial).</span></span>

## <a name="collections-of-owned-entities"></a><span data-ttu-id="e34d9-117">Colecciones de entidades en propiedad</span><span class="sxs-lookup"><span data-stu-id="e34d9-117">Collections of owned entities</span></span>

<span data-ttu-id="e34d9-118">EF Core 2.0 agregó la capacidad de modelar la propiedad en asociaciones de uno a uno.</span><span class="sxs-lookup"><span data-stu-id="e34d9-118">EF Core 2.0 added the ability to model ownership in one-to-one associations.</span></span>
<span data-ttu-id="e34d9-119">EF Core 2.2 extiende la capacidad de expresar la propiedad a asociaciones de uno a varios.</span><span class="sxs-lookup"><span data-stu-id="e34d9-119">EF Core 2.2 extends the ability to express ownership to one-to-many associations.</span></span>
<span data-ttu-id="e34d9-120">La propiedad ayuda a restringir el modo en que se usan las entidades.</span><span class="sxs-lookup"><span data-stu-id="e34d9-120">Ownership helps constrain how entities are used.</span></span>

<span data-ttu-id="e34d9-121">Por ejemplo, las entidades en propiedad:</span><span class="sxs-lookup"><span data-stu-id="e34d9-121">For example, owned entities:</span></span>

- <span data-ttu-id="e34d9-122">Solo pueden aparecer en las propiedades de navegación de otros tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="e34d9-122">Can only ever appear on navigation properties of other entity types.</span></span>
- <span data-ttu-id="e34d9-123">Se cargan automáticamente, y solo se puede hacer su seguimiento por un DbContext junto con su propietario.</span><span class="sxs-lookup"><span data-stu-id="e34d9-123">Are automatically loaded, and can only be tracked by a DbContext alongside their owner.</span></span>

<span data-ttu-id="e34d9-124">En bases de datos relacionales, las colecciones en propiedad se asignan a tablas independientes del propietario, al igual que las asociaciones regulares de uno a varios.</span><span class="sxs-lookup"><span data-stu-id="e34d9-124">In relational databases, owned collections are mapped to separate tables from the owner, just like regular one-to-many associations.</span></span>
<span data-ttu-id="e34d9-125">Pero en las bases de datos orientadas a documentos, tenemos previsto anidar entidades en propiedad (en colecciones o referencias en propiedad) dentro del mismo documento que el propietario.</span><span class="sxs-lookup"><span data-stu-id="e34d9-125">But in document-oriented databases, we plan to nest owned entities (in owned collections or references) within the same document as the owner.</span></span>

<span data-ttu-id="e34d9-126">Puede usar la característica mediante una llamada a la nueva API OwnsMany():</span><span class="sxs-lookup"><span data-stu-id="e34d9-126">You can use the feature by calling the new OwnsMany() API:</span></span>

``` csharp
modelBuilder.Entity<Customer>().OwnsMany(c => c.Addresses);
```

<span data-ttu-id="e34d9-127">Para obtener más información, consulte la [documentación actualizada de entidades en propiedad](xref:core/modeling/owned-entities#collections-of-owned-types).</span><span class="sxs-lookup"><span data-stu-id="e34d9-127">For more information, see the [updated owned entities documentation](xref:core/modeling/owned-entities#collections-of-owned-types).</span></span>

## <a name="query-tags"></a><span data-ttu-id="e34d9-128">Etiquetas de consulta</span><span class="sxs-lookup"><span data-stu-id="e34d9-128">Query tags</span></span>

<span data-ttu-id="e34d9-129">Esta característica simplifica la correlación de las consultas LINQ en el código con las consultas SQL generadas capturadas en los registros.</span><span class="sxs-lookup"><span data-stu-id="e34d9-129">This feature simplifies the correlation of LINQ queries in code with generated SQL queries captured in logs.</span></span>

<span data-ttu-id="e34d9-130">Para aprovechar las ventajas de las etiquetas de consulta, anote una consulta LINQ mediante el nuevo método TagWith().</span><span class="sxs-lookup"><span data-stu-id="e34d9-130">To take advantage of query tags, you annotate a LINQ query using the new TagWith() method.</span></span>
<span data-ttu-id="e34d9-131">Uso de la consulta espacial de un ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="e34d9-131">Using the spatial query from a previous example:</span></span>

``` csharp
  var nearestFriends =
      (from f in context.Friends.TagWith(@"This is my spatial query!")
      orderby f.Location.Distance(myLocation) descending
      select f).Take(5).ToList();
```

<span data-ttu-id="e34d9-132">Esta consulta LINQ producirá la siguiente salida SQL:</span><span class="sxs-lookup"><span data-stu-id="e34d9-132">This LINQ query will produce the following SQL output:</span></span>

``` sql
-- This is my spatial query!

SELECT TOP(@__p_1) [f].[Name], [f].[Location]
FROM [Friends] AS [f]
ORDER BY [f].[Location].STDistance(@__myLocation_0) DESC
```

<span data-ttu-id="e34d9-133">Para obtener más información, vea la [documentación de etiquetas de consulta](xref:core/querying/tags).</span><span class="sxs-lookup"><span data-stu-id="e34d9-133">For more information, see the [query tags documentation](xref:core/querying/tags).</span></span>
