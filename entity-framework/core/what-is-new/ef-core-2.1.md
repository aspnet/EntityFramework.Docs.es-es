---
title: Novedades de EF Core 2.1 - EF Core
description: Cambios y mejoras en Entity Framework Core 2.1
author: ajcvickers
ms.date: 02/20/2018
uid: core/what-is-new/ef-core-2.1
ms.openlocfilehash: f235c310cbd336da65cd05d25e1338d87534eb2a
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429343"
---
# <a name="new-features-in-ef-core-21"></a><span data-ttu-id="e831e-103">Novedades de EF Core 2.1</span><span class="sxs-lookup"><span data-stu-id="e831e-103">New features in EF Core 2.1</span></span>

<span data-ttu-id="e831e-104">Además de numerosas correcciones de errores y pequeñas mejoras funcionales y de rendimiento, EF Core 2.1 incluye algunas características nuevas muy atractivas:</span><span class="sxs-lookup"><span data-stu-id="e831e-104">Besides numerous bug fixes and small functional and performance enhancements, EF Core 2.1 includes some compelling new features:</span></span>

## <a name="lazy-loading"></a><span data-ttu-id="e831e-105">Carga diferida</span><span class="sxs-lookup"><span data-stu-id="e831e-105">Lazy loading</span></span>

<span data-ttu-id="e831e-106">EF Core contiene ahora los bloques de creación necesarios para quienes quieran crear clases de entidad que puedan cargar las propiedades de navegación a petición.</span><span class="sxs-lookup"><span data-stu-id="e831e-106">EF Core now contains the necessary building blocks for anyone to author entity classes that can load their navigation properties on demand.</span></span> <span data-ttu-id="e831e-107">También hemos creado otro paquete, Microsoft.EntityFrameworkCore.Proxies, que aprovecha los bloques de creación para generar clases proxy de carga diferida basadas en clases de entidad apenas modificadas (por ejemplo, clases con propiedades de navegación virtual).</span><span class="sxs-lookup"><span data-stu-id="e831e-107">We have also created a new package, Microsoft.EntityFrameworkCore.Proxies, that leverages those building blocks to produce lazy loading proxy classes based on minimally modified entity classes (for example, classes with virtual navigation properties).</span></span>

<span data-ttu-id="e831e-108">Consulte la [sección sobre cargas diferidas](xref:core/querying/related-data#lazy-loading) para obtener más información sobre el tema.</span><span class="sxs-lookup"><span data-stu-id="e831e-108">Read the [section on lazy loading](xref:core/querying/related-data#lazy-loading) for more information about this topic.</span></span>

## <a name="parameters-in-entity-constructors"></a><span data-ttu-id="e831e-109">Parámetros en constructores de entidad</span><span class="sxs-lookup"><span data-stu-id="e831e-109">Parameters in entity constructors</span></span>

<span data-ttu-id="e831e-110">Como uno de los bloques de creación necesarios para la carga diferida, se habilita la creación de entidades que aceptan parámetros en sus constructores.</span><span class="sxs-lookup"><span data-stu-id="e831e-110">As one of the required building blocks for lazy loading, we enabled the creation of entities that take parameters in their constructors.</span></span> <span data-ttu-id="e831e-111">Puede usar parámetros para insertar valores de propiedad, delegados de carga diferida y servicios.</span><span class="sxs-lookup"><span data-stu-id="e831e-111">You can use parameters to inject property values, lazy loading delegates, and services.</span></span>

<span data-ttu-id="e831e-112">Consulte la [sección sobre constructores de entidad con parámetros](xref:core/modeling/constructors) para obtener más información sobre el tema.</span><span class="sxs-lookup"><span data-stu-id="e831e-112">Read the [section on entity constructor with parameters](xref:core/modeling/constructors) for more information about this topic.</span></span>

## <a name="value-conversions"></a><span data-ttu-id="e831e-113">Conversiones de valores</span><span class="sxs-lookup"><span data-stu-id="e831e-113">Value conversions</span></span>

<span data-ttu-id="e831e-114">Hasta ahora, EF Core solo podía asignar propiedades de tipos admitidas de forma nativa por el proveedor de bases de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="e831e-114">Until now, EF Core could only map properties of types natively supported by the underlying database provider.</span></span> <span data-ttu-id="e831e-115">Los valores se copiaban de un lado a otro entre las columnas y las propiedades sin ninguna transformación.</span><span class="sxs-lookup"><span data-stu-id="e831e-115">Values were copied back and forth between columns and properties without any transformation.</span></span> <span data-ttu-id="e831e-116">A partir de EF Core 2.1, pueden aplicarse conversiones de valores para transformar los valores obtenidos en las columnas antes de que se apliquen a las propiedades, y viceversa.</span><span class="sxs-lookup"><span data-stu-id="e831e-116">Starting with EF Core 2.1, value conversions can be applied to transform the values obtained from columns before they are applied to properties, and vice versa.</span></span> <span data-ttu-id="e831e-117">Tenemos varias conversiones que pueden aplicarse por convención según sea necesario, así como una API de configuración explícita que permite registrar conversiones personalizadas entre columnas y propiedades.</span><span class="sxs-lookup"><span data-stu-id="e831e-117">We have a number of conversions that can be applied by convention as necessary, as well as an explicit configuration API that allows registering custom conversions between columns and properties.</span></span> <span data-ttu-id="e831e-118">Algunas de las aplicaciones de esta característica son:</span><span class="sxs-lookup"><span data-stu-id="e831e-118">Some of the application of this feature are:</span></span>

- <span data-ttu-id="e831e-119">Almacenamiento de enumeraciones como cadenas</span><span class="sxs-lookup"><span data-stu-id="e831e-119">Storing enums as strings</span></span>
- <span data-ttu-id="e831e-120">Asignación de enteros sin signo con SQL Server</span><span class="sxs-lookup"><span data-stu-id="e831e-120">Mapping unsigned integers with SQL Server</span></span>
- <span data-ttu-id="e831e-121">Cifrado y descifrado automáticos de valores de propiedad</span><span class="sxs-lookup"><span data-stu-id="e831e-121">Automatic encryption and decryption of property values</span></span>

<span data-ttu-id="e831e-122">Consulte la [sección sobre conversiones de valores](xref:core/modeling/value-conversions) para obtener más información sobre el tema.</span><span class="sxs-lookup"><span data-stu-id="e831e-122">Read the [section on value conversions](xref:core/modeling/value-conversions) for more information about this topic.</span></span>

## <a name="linq-groupby-translation"></a><span data-ttu-id="e831e-123">Traslación de GroupBy de LINQ</span><span class="sxs-lookup"><span data-stu-id="e831e-123">LINQ GroupBy translation</span></span>

<span data-ttu-id="e831e-124">Antes de la versión 2.1, el operador GroupBy de LINQ en EF Core siempre se evaluaba en la memoria.</span><span class="sxs-lookup"><span data-stu-id="e831e-124">Before version 2.1, in EF Core the GroupBy LINQ operator would always be evaluated in memory.</span></span> <span data-ttu-id="e831e-125">Ahora se admite su traslación a la cláusula GROUP BY de SQL en los casos más comunes.</span><span class="sxs-lookup"><span data-stu-id="e831e-125">We now support translating it to the SQL GROUP BY clause in most common cases.</span></span>

<span data-ttu-id="e831e-126">En este ejemplo se muestra una consulta con GroupBy utilizada para calcular diversas funciones de agregado:</span><span class="sxs-lookup"><span data-stu-id="e831e-126">This example shows a query with GroupBy used to compute various aggregate functions:</span></span>

```csharp
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

<span data-ttu-id="e831e-127">La traslación correspondiente a SQL tiene este aspecto:</span><span class="sxs-lookup"><span data-stu-id="e831e-127">The corresponding SQL translation looks like this:</span></span>

```sql
SELECT [o].[CustomerId], [o].[EmployeeId],
    SUM([o].[Amount]), MIN([o].[Amount]), MAX([o].[Amount]), AVG([o].[Amount])
FROM [Orders] AS [o]
GROUP BY [o].[CustomerId], [o].[EmployeeId];
```

## <a name="data-seeding"></a><span data-ttu-id="e831e-128">Propagación de datos</span><span class="sxs-lookup"><span data-stu-id="e831e-128">Data Seeding</span></span>

<span data-ttu-id="e831e-129">Con la nueva versión, será posible proporcionar datos iniciales para rellenar una base de datos.</span><span class="sxs-lookup"><span data-stu-id="e831e-129">With the new release it will be possible to provide initial data to populate a database.</span></span> <span data-ttu-id="e831e-130">A diferencia de en EF6, la propagación de datos está asociada a un tipo de entidad como parte de la configuración del modelo.</span><span class="sxs-lookup"><span data-stu-id="e831e-130">Unlike in EF6, seeding data is associated to an entity type as part of the model configuration.</span></span> <span data-ttu-id="e831e-131">Las migraciones de EF Core pueden luego calcular automáticamente las operaciones de inserción, actualización y eliminación que hay que aplicar al actualizar la base de datos a una nueva versión del modelo.</span><span class="sxs-lookup"><span data-stu-id="e831e-131">Then EF Core migrations can automatically compute what insert, update or delete operations need to be applied when upgrading the database to a new version of the model.</span></span>

<span data-ttu-id="e831e-132">Por ejemplo, esto se puede usar para configurar los datos de inicialización de un método POST en `OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="e831e-132">As an example, you can use this to configure seed data for a Post in `OnModelCreating`:</span></span>

```csharp
modelBuilder.Entity<Post>().HasData(new Post{ Id = 1, Text = "Hello World!" });
```

<span data-ttu-id="e831e-133">Consulte la [sección sobre propagación de datos](xref:core/modeling/data-seeding) para obtener más información sobre el tema.</span><span class="sxs-lookup"><span data-stu-id="e831e-133">Read the [section on data seeding](xref:core/modeling/data-seeding) for more information about this topic.</span></span>

## <a name="query-types"></a><span data-ttu-id="e831e-134">Tipos de consulta</span><span class="sxs-lookup"><span data-stu-id="e831e-134">Query types</span></span>

<span data-ttu-id="e831e-135">Un modelo de EF Core ahora puede incluir tipos de consulta.</span><span class="sxs-lookup"><span data-stu-id="e831e-135">An EF Core model can now include query types.</span></span> <span data-ttu-id="e831e-136">A diferencia de los tipos de entidad, los tipos de consulta no tienen claves definidas en ellos y no se pueden insertar, eliminar ni actualizar (es decir, son de solo lectura), pero se pueden devolver directamente en las consultas.</span><span class="sxs-lookup"><span data-stu-id="e831e-136">Unlike entity types, query types do not have keys defined on them and cannot be inserted, deleted or updated (that is, they are read-only), but they can be returned directly by queries.</span></span> <span data-ttu-id="e831e-137">Algunos de los escenarios de uso para los tipos de consulta son:</span><span class="sxs-lookup"><span data-stu-id="e831e-137">Some of the usage scenarios for query types are:</span></span>

- <span data-ttu-id="e831e-138">Asignar a vistas sin claves principales</span><span class="sxs-lookup"><span data-stu-id="e831e-138">Mapping to views without primary keys</span></span>
- <span data-ttu-id="e831e-139">Asignar a tablas sin claves principales</span><span class="sxs-lookup"><span data-stu-id="e831e-139">Mapping to tables without primary keys</span></span>
- <span data-ttu-id="e831e-140">Asignar a consultas definidas en el modelo</span><span class="sxs-lookup"><span data-stu-id="e831e-140">Mapping to queries defined in the model</span></span>
- <span data-ttu-id="e831e-141">Actuar como tipo de valor devuelto en consultas `FromSql()`</span><span class="sxs-lookup"><span data-stu-id="e831e-141">Serving as the return type for `FromSql()` queries</span></span>

<span data-ttu-id="e831e-142">Consulte la [sección sobre tipos de consulta](xref:core/modeling/keyless-entity-types) para obtener más información sobre el tema.</span><span class="sxs-lookup"><span data-stu-id="e831e-142">Read the [section on query types](xref:core/modeling/keyless-entity-types) for more information about this topic.</span></span>

## <a name="include-for-derived-types"></a><span data-ttu-id="e831e-143">Include en tipos derivados</span><span class="sxs-lookup"><span data-stu-id="e831e-143">Include for derived types</span></span>

<span data-ttu-id="e831e-144">Ahora será posible especificar propiedades de navegación definidas solo en tipos derivados al escribir expresiones para el método `Include`.</span><span class="sxs-lookup"><span data-stu-id="e831e-144">It will be now possible to specify navigation properties only defined on derived types when writing expressions for the `Include` method.</span></span> <span data-ttu-id="e831e-145">Para la versión fuertemente tipada de `Include`, se admite el uso de una conversión explícita o el operador `as`.</span><span class="sxs-lookup"><span data-stu-id="e831e-145">For the strongly typed version of `Include`, we support using either an explicit cast or the `as` operator.</span></span> <span data-ttu-id="e831e-146">Ahora también se admite hacer referencia a los nombres de propiedad de navegación definidos en tipos derivados en la versión de cadena de `Include`:</span><span class="sxs-lookup"><span data-stu-id="e831e-146">We also now support referencing the names of navigation property defined on derived types in the string version of `Include`:</span></span>

```csharp
var option1 = context.People.Include(p => ((Student)p).School);
var option2 = context.People.Include(p => (p as Student).School);
var option3 = context.People.Include("School");
```

<span data-ttu-id="e831e-147">Consulte la [sección sobre Include con tipos derivados](xref:core/querying/related-data#include-on-derived-types) para obtener más información sobre el tema.</span><span class="sxs-lookup"><span data-stu-id="e831e-147">Read the [section on Include with derived types](xref:core/querying/related-data#include-on-derived-types) for more information about this topic.</span></span>

## <a name="systemtransactions-support"></a><span data-ttu-id="e831e-148">System.Transactions</span><span class="sxs-lookup"><span data-stu-id="e831e-148">System.Transactions support</span></span>

<span data-ttu-id="e831e-149">Se ha agregado la posibilidad de trabajar con características de System.Transactions tales como TransactionScope.</span><span class="sxs-lookup"><span data-stu-id="e831e-149">We have added the ability to work with System.Transactions features such as TransactionScope.</span></span> <span data-ttu-id="e831e-150">Esto funcionará en .NET Framework y en .NET Core cuando se usen proveedores de bases de datos que lo admitan.</span><span class="sxs-lookup"><span data-stu-id="e831e-150">This will work on both .NET Framework and .NET Core when using database providers that support it.</span></span>

<span data-ttu-id="e831e-151">Consulte la [sección sobre System.Transactions](xref:core/saving/transactions#using-systemtransactions) para obtener más información sobre el tema.</span><span class="sxs-lookup"><span data-stu-id="e831e-151">Read the [section on System.Transactions](xref:core/saving/transactions#using-systemtransactions) for more information about this topic.</span></span>

## <a name="better-column-ordering-in-initial-migration"></a><span data-ttu-id="e831e-152">Mejor ordenación de columnas en la migración inicial</span><span class="sxs-lookup"><span data-stu-id="e831e-152">Better column ordering in initial migration</span></span>

<span data-ttu-id="e831e-153">En función de los comentarios de clientes, hemos actualizado las migraciones para que las columnas de tablas se generen inicialmente en el mismo orden en que se declaran las propiedades en clases.</span><span class="sxs-lookup"><span data-stu-id="e831e-153">Based on customer feedback, we have updated migrations to initially generate columns for tables in the same order as properties are declared in classes.</span></span> <span data-ttu-id="e831e-154">Tenga en cuenta que EF Core no puede cambiar el orden cuando se agregan nuevos miembros después de la creación de la tabla inicial.</span><span class="sxs-lookup"><span data-stu-id="e831e-154">Note that EF Core cannot change order when new members are added after the initial table creation.</span></span>

## <a name="optimization-of-correlated-subqueries"></a><span data-ttu-id="e831e-155">Optimización de subconsultas correlacionadas</span><span class="sxs-lookup"><span data-stu-id="e831e-155">Optimization of correlated subqueries</span></span>

<span data-ttu-id="e831e-156">Se ha mejorado la traslación de consultas para evitar la ejecución de "N + 1" consultas SQL en muchos escenarios comunes en los que el uso de una propiedad de navegación en la proyección conduce a unir los datos de la consulta raíz con los datos de una subconsulta correlacionada.</span><span class="sxs-lookup"><span data-stu-id="e831e-156">We have improved our query translation to avoid executing "N + 1" SQL queries in many common scenarios in which the usage of a navigation property in the projection leads to joining data from the root query with data from a correlated subquery.</span></span> <span data-ttu-id="e831e-157">La optimización requiere el almacenamiento en búfer de los resultados de la subconsulta, y hay que modificar la consulta para que participe en el nuevo comportamiento.</span><span class="sxs-lookup"><span data-stu-id="e831e-157">The optimization requires buffering the results from the subquery, and we require that you modify the query to opt-in the new behavior.</span></span>

<span data-ttu-id="e831e-158">Por ejemplo, la siguiente consulta normalmente se traslada a una consulta para clientes, más N consultas separadas para pedidos (donde "N" corresponde al número de clientes devueltos):</span><span class="sxs-lookup"><span data-stu-id="e831e-158">As an example, the following query normally gets translated into one query for Customers, plus N (where "N" is the number of customers returned) separate queries for Orders:</span></span>

```csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount));
```

<span data-ttu-id="e831e-159">Al incluir `ToList()` en el lugar correcto, se indica que el almacenamiento en búfer es adecuado para los pedidos, lo que permite la optimización:</span><span class="sxs-lookup"><span data-stu-id="e831e-159">By including `ToList()` in the right place, you indicate that buffering is appropriate for the Orders, which enable the optimization:</span></span>

```csharp
var query = context.Customers.Select(
    c => c.Orders.Where(o => o.Amount  > 100).Select(o => o.Amount).ToList());
```

<span data-ttu-id="e831e-160">Tenga en cuenta que esta consulta solo se trasladará a dos consultas SQL: una para clientes y la siguiente para pedidos.</span><span class="sxs-lookup"><span data-stu-id="e831e-160">Note that this query will be translated to only two SQL queries: One for Customers and the next one for Orders.</span></span>

## <a name="owned-attribute"></a><span data-ttu-id="e831e-161">Atributo [Owned]</span><span class="sxs-lookup"><span data-stu-id="e831e-161">[Owned] attribute</span></span>

<span data-ttu-id="e831e-162">Ahora es posible configurar [tipos de entidad en propiedad](xref:core/modeling/owned-entities) anotando simplemente el tipo con `[Owned]` y asegurándose luego de que la entidad de propietario se agrega al modelo:</span><span class="sxs-lookup"><span data-stu-id="e831e-162">It is now possible to configure [owned entity types](xref:core/modeling/owned-entities) by simply annotating the type with `[Owned]` and then making sure the owner entity is added to the model:</span></span>

```csharp
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

## <a name="command-line-tool-dotnet-ef-included-in-net-core-sdk"></a><span data-ttu-id="e831e-163">Herramienta de línea de comandos dotnet-ef incluida en el SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="e831e-163">Command-line tool dotnet-ef included in .NET Core SDK</span></span>

<span data-ttu-id="e831e-164">Los comandos de _dotnet-ef_ ahora forman parte del SDK de .NET Core, así que ya no es necesario usar DotNetCliToolReference en el proyecto para poder usar migraciones o para aplicar la técnica scaffolding a DbContext desde una base de datos existente.</span><span class="sxs-lookup"><span data-stu-id="e831e-164">The _dotnet-ef_ commands are now part of the .NET Core SDK, therefore it will no longer be necessary to use DotNetCliToolReference in the project to be able to use migrations or to scaffold a DbContext from an existing database.</span></span>

<span data-ttu-id="e831e-165">Vea la sección sobre [cómo instalar las herramientas](xref:core/cli/dotnet#installing-the-tools) para obtener más información sobre cómo habilitar herramientas de línea de comandos para diferentes versiones del SDK de .NET Core y EF Core.</span><span class="sxs-lookup"><span data-stu-id="e831e-165">See the section on [installing the tools](xref:core/cli/dotnet#installing-the-tools) for more details on how to enable command line tools for different versions of the .NET Core SDK and EF Core.</span></span>

## <a name="microsoftentityframeworkcoreabstractions-package"></a><span data-ttu-id="e831e-166">Paquete Microsoft.EntityFrameworkCore.Abstractions</span><span class="sxs-lookup"><span data-stu-id="e831e-166">Microsoft.EntityFrameworkCore.Abstractions package</span></span>

<span data-ttu-id="e831e-167">El nuevo paquete contiene atributos e interfaces que puede usar en los proyectos para activar características de EF Core sin depender de EF Core como un todo.</span><span class="sxs-lookup"><span data-stu-id="e831e-167">The new package contains attributes and interfaces that you can use in your projects to light up EF Core features without taking a dependency on EF Core as a whole.</span></span> <span data-ttu-id="e831e-168">Por ejemplo, el atributo [Owned] y la interfaz de ILazyLoader se encuentran aquí.</span><span class="sxs-lookup"><span data-stu-id="e831e-168">For example, the [Owned] attribute and the ILazyLoader interface are located here.</span></span>

## <a name="state-change-events"></a><span data-ttu-id="e831e-169">Eventos de cambio de estado</span><span class="sxs-lookup"><span data-stu-id="e831e-169">State change events</span></span>

<span data-ttu-id="e831e-170">Los nuevos eventos `Tracked` y `StateChanged` de `ChangeTracker` se pueden usar para escribir lógica que reaccione a las entidades que entran en DbContext o que cambian su estado.</span><span class="sxs-lookup"><span data-stu-id="e831e-170">New `Tracked` And `StateChanged` events on `ChangeTracker` can be used to write logic that reacts to entities entering the DbContext or changing their state.</span></span>

## <a name="raw-sql-parameter-analyzer"></a><span data-ttu-id="e831e-171">Analizador de parámetros de SQL sin formato</span><span class="sxs-lookup"><span data-stu-id="e831e-171">Raw SQL parameter analyzer</span></span>

<span data-ttu-id="e831e-172">Un nuevo analizador de código se incluye en EF Core que detecta los usos potencialmente poco seguros de nuestras API de SQL sin formato, como `FromSql` o `ExecuteSqlCommand`.</span><span class="sxs-lookup"><span data-stu-id="e831e-172">A new code analyzer is included with EF Core that detects potentially unsafe usages of our raw-SQL APIs, like `FromSql` or `ExecuteSqlCommand`.</span></span> <span data-ttu-id="e831e-173">Por ejemplo, para la consulta siguiente, verá una advertencia porque _minAge_ no tiene parámetros:</span><span class="sxs-lookup"><span data-stu-id="e831e-173">For example, for the following query, you will see a warning because _minAge_ is not parameterized:</span></span>

```csharp
var sql = $"SELECT * FROM People WHERE Age > {minAge}";
var query = context.People.FromSql(sql);
```

## <a name="database-provider-compatibility"></a><span data-ttu-id="e831e-174">Compatibilidad del proveedor de bases de datos</span><span class="sxs-lookup"><span data-stu-id="e831e-174">Database provider compatibility</span></span>

<span data-ttu-id="e831e-175">Se recomienda usar EF Core 2.1 con proveedores que se hayan actualizado o que al menos se haya comprobado que funcionan con EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="e831e-175">It is recommended that you use EF Core 2.1 with providers that have been updated or at least tested to work with EF Core 2.1.</span></span>

> [!TIP]
> <span data-ttu-id="e831e-176">Si encuentra alguna incompatibilidad inesperada o algún problema en las nuevas características o si tiene comentarios sobre ellas, notifíquelos mediante [nuestro rastreador de problemas](https://github.com/dotnet/efcore/issues/new).</span><span class="sxs-lookup"><span data-stu-id="e831e-176">If you find any unexpected incompatibility or any issue in the new features, or if you have feedback on them, please report it using [our issue tracker](https://github.com/dotnet/efcore/issues/new).</span></span>
