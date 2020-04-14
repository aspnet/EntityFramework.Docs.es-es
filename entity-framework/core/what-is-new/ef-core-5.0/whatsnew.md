---
title: Novedades en EF Core 5.0
description: Información general sobre las nuevas características de EF Core 5.0
author: ajcvickers
ms.date: 03/30/2020
uid: core/what-is-new/ef-core-5.0/whatsnew.md
ms.openlocfilehash: c047a308cadf44eea577dcab29b68b36942a50df
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/07/2020
ms.locfileid: "80634278"
---
# <a name="whats-new-in-ef-core-50"></a><span data-ttu-id="d2030-103">Novedades en EF Core 5.0</span><span class="sxs-lookup"><span data-stu-id="d2030-103">What's New in EF Core 5.0</span></span>

<span data-ttu-id="d2030-104">EF Core 5.0 está actualmente en desarrollo.</span><span class="sxs-lookup"><span data-stu-id="d2030-104">EF Core 5.0 is currently in development.</span></span>
<span data-ttu-id="d2030-105">Esta página contendrá información general sobre los cambios interesantes introducidos en cada versión preliminar.</span><span class="sxs-lookup"><span data-stu-id="d2030-105">This page will contain an overview of interesting changes introduced in each preview.</span></span>

<span data-ttu-id="d2030-106">Esta página no duplica el [plan para EF Core 5.0](plan.md).</span><span class="sxs-lookup"><span data-stu-id="d2030-106">This page does not duplicate the [plan for EF Core 5.0](plan.md).</span></span>
<span data-ttu-id="d2030-107">En el plan se describen los temas generales relativos a EF Core 5.0, incluido todo lo que estamos planeando incluir antes de publicar la versión final.</span><span class="sxs-lookup"><span data-stu-id="d2030-107">The plan describes the overall themes for EF Core 5.0, including everything we are planning to include before shipping the final release.</span></span>

<span data-ttu-id="d2030-108">A medida que se publique el contenido, se agregarán vínculos que redirigirán de esta página a la documentación oficial.</span><span class="sxs-lookup"><span data-stu-id="d2030-108">We will add links from here to the official documentation as it is published.</span></span>

## <a name="preview-2"></a><span data-ttu-id="d2030-109">Versión preliminar 2</span><span class="sxs-lookup"><span data-stu-id="d2030-109">Preview 2</span></span>

### <a name="use-a-c-attribute-to-specify-a-property-backing-field"></a><span data-ttu-id="d2030-110">Uso de un atributo de C# para especificar un campo de respaldo de propiedad</span><span class="sxs-lookup"><span data-stu-id="d2030-110">Use a C# attribute to specify a property backing field</span></span>

<span data-ttu-id="d2030-111">Ahora se puede usar un atributo de C# para especificar el campo de respaldo de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="d2030-111">A C# attribute can now be used to specify the backing field for a property.</span></span>
<span data-ttu-id="d2030-112">Este atributo permite a EF Core seguir escribiendo y leyendo en el campo de respaldo como sucedería normalmente, incluso si no se puede encontrar el campo de respaldo automáticamente.</span><span class="sxs-lookup"><span data-stu-id="d2030-112">This attribute allows EF Core to still write to and read from the backing field as would normally happen, even when the backing field cannot be found automatically.</span></span>
<span data-ttu-id="d2030-113">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d2030-113">For example:</span></span>

```CSharp
public class Blog
{
    private string _mainTitle;

    public int Id { get; set; }

    [BackingField(nameof(_mainTitle))]
    public string Title
    {
        get => _mainTitle;
        set => _mainTitle = value;
    }
}
```

<span data-ttu-id="d2030-114">En el problema [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-114">Documentation is tracked by issue [#2230](https://github.com/dotnet/EntityFramework.Docs/issues/2230).</span></span>

### <a name="complete-discriminator-mapping"></a><span data-ttu-id="d2030-115">Asignación completa de discriminador</span><span class="sxs-lookup"><span data-stu-id="d2030-115">Complete discriminator mapping</span></span>

<span data-ttu-id="d2030-116">EF Core usa una columna de discriminador para la [asignación de TPH de una jerarquía de herencia](/ef/core/modeling/inheritance).</span><span class="sxs-lookup"><span data-stu-id="d2030-116">EF Core uses a discriminator column for [TPH mapping of an inheritance hierarchy](/ef/core/modeling/inheritance).</span></span>
<span data-ttu-id="d2030-117">Se posibilitan algunas mejoras de rendimiento siempre que EF Core conozca todos los valores posibles del discriminador.</span><span class="sxs-lookup"><span data-stu-id="d2030-117">Some performance enhancements are possible so long as EF Core knows all possible values for the discriminator.</span></span>
<span data-ttu-id="d2030-118">EF Core 5.0 ahora implementa estas mejoras.</span><span class="sxs-lookup"><span data-stu-id="d2030-118">EF Core 5.0 now implements these enhancements.</span></span>

<span data-ttu-id="d2030-119">Por ejemplo, las versiones anteriores de EF Core siempre generaban este SQL para una consulta que devuelve todos los tipos de una jerarquía:</span><span class="sxs-lookup"><span data-stu-id="d2030-119">For example, previous versions of EF Core would always generate this SQL for a query returning all types in a hierarchy:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
WHERE [a].[Discriminator] IN (N'Animal', N'Cat', N'Dog', N'Human')
```

<span data-ttu-id="d2030-120">EF Core 5.0 ahora genera lo siguiente cuando se configura una asignación completa de discriminador:</span><span class="sxs-lookup"><span data-stu-id="d2030-120">EF Core 5.0 will now generate the following when a complete discriminator mapping is configured:</span></span>

```sql
SELECT [a].[Id], [a].[Discriminator], [a].[Name]
FROM [Animal] AS [a]
```

<span data-ttu-id="d2030-121">Este va a ser el comportamiento predeterminado a partir de la versión preliminar 3.</span><span class="sxs-lookup"><span data-stu-id="d2030-121">It will be the default behavior starting with preview 3.</span></span>

### <a name="performance-improvements-in-microsoftdatasqlite"></a><span data-ttu-id="d2030-122">Mejoras de rendimiento de Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="d2030-122">Performance improvements in Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="d2030-123">Se han realizado dos mejoras de rendimiento para SQLIte:</span><span class="sxs-lookup"><span data-stu-id="d2030-123">We have made two performance improvements for SQLIte:</span></span>

* <span data-ttu-id="d2030-124">La recuperación de datos binarios y de cadena con GetBytes, GetChars y GetTextReader ahora es más eficaz gracias al uso de SqliteBlob y flujos.</span><span class="sxs-lookup"><span data-stu-id="d2030-124">Retrieving binary and string data with GetBytes, GetChars, and GetTextReader is now more efficient by making use of SqliteBlob and streams.</span></span>
* <span data-ttu-id="d2030-125">La inicialización de SqliteConnection ahora es diferida.</span><span class="sxs-lookup"><span data-stu-id="d2030-125">Initialization of SqliteConnection is now lazy.</span></span>

<span data-ttu-id="d2030-126">Estas mejoras se encuentran en el proveedor Microsoft.Data.Sqlite de ADO.NET y, por lo tanto, también mejoran el rendimiento fuera de EF Core.</span><span class="sxs-lookup"><span data-stu-id="d2030-126">These improvements are in the ADO.NET Microsoft.Data.Sqlite provider and hence also improve performance outside of EF Core.</span></span>

## <a name="preview-1"></a><span data-ttu-id="d2030-127">Versión preliminar 1</span><span class="sxs-lookup"><span data-stu-id="d2030-127">Preview 1</span></span>

### <a name="simple-logging"></a><span data-ttu-id="d2030-128">Registro sencillo</span><span class="sxs-lookup"><span data-stu-id="d2030-128">Simple logging</span></span>

<span data-ttu-id="d2030-129">Esta característica agrega funcionalidad similar a `Database.Log` en EF6.</span><span class="sxs-lookup"><span data-stu-id="d2030-129">This feature adds functionality similar to `Database.Log` in EF6.</span></span>
<span data-ttu-id="d2030-130">Es decir, proporciona una manera sencilla de obtener registros de EF Core sin necesidad de configurar ningún tipo de plataforma de registro externa.</span><span class="sxs-lookup"><span data-stu-id="d2030-130">That is, it provides a simple way to get logs from EF Core without the need to configure any kind of external logging framework.</span></span>

<span data-ttu-id="d2030-131">La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="d2030-131">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="d2030-132">En el problema [n.º 2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="d2030-132">Additional documentation is tracked by issue [#2085](https://github.com/dotnet/EntityFramework.Docs/issues/2085).</span></span>

### <a name="simple-way-to-get-generated-sql"></a><span data-ttu-id="d2030-133">Forma sencilla de generar contenido SQL</span><span class="sxs-lookup"><span data-stu-id="d2030-133">Simple way to get generated SQL</span></span>

<span data-ttu-id="d2030-134">EF Core 5.0 presenta el método de extensión `ToQueryString`, que devuelve el SQL que EF Core genera al ejecutar una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="d2030-134">EF Core 5.0 introduces the `ToQueryString` extension method, which will return the SQL that EF Core will generate when executing a LINQ query.</span></span>

<span data-ttu-id="d2030-135">La documentación preliminar se incluye en el [estado semanal de EF del 9 de enero de 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span><span class="sxs-lookup"><span data-stu-id="d2030-135">Preliminary documentation is included in the [EF weekly status for January 9, 2020](https://github.com/dotnet/efcore/issues/19549#issuecomment-572823246).</span></span>

<span data-ttu-id="d2030-136">En el problema [n.º 1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="d2030-136">Additional documentation is tracked by issue [#1331](https://github.com/dotnet/EntityFramework.Docs/issues/1331).</span></span>

### <a name="use-a-c-attribute-to-indicate-that-an-entity-has-no-key"></a><span data-ttu-id="d2030-137">Uso de un atributo de C# para indicar que una entidad no tiene clave</span><span class="sxs-lookup"><span data-stu-id="d2030-137">Use a C# attribute to indicate that an entity has no key</span></span>

<span data-ttu-id="d2030-138">Ahora se pueden configurar los tipos de entidad para indicar que no tienen clave mediante el nuevo valor `KeylessAttribute`.</span><span class="sxs-lookup"><span data-stu-id="d2030-138">An entity type can now be configured as having no key using the new `KeylessAttribute`.</span></span>
<span data-ttu-id="d2030-139">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d2030-139">For example:</span></span>

```CSharp
[Keyless]
public class Address
{
    public string Street { get; set; }
    public string City { get; set; }
    public int Zip { get; set; }
}
```

<span data-ttu-id="d2030-140">En el problema [n.º 2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-140">Documentation is tracked by issue [#2186](https://github.com/dotnet/EntityFramework.Docs/issues/2186).</span></span>

### <a name="connection-or-connection-string-can-be-changed-on-initialized-dbcontext"></a><span data-ttu-id="d2030-141">Posibilidad de cambiar la conexión o la cadena de conexión en una instancia inicializada de DbContext</span><span class="sxs-lookup"><span data-stu-id="d2030-141">Connection or connection string can be changed on initialized DbContext</span></span>

<span data-ttu-id="d2030-142">Ahora es más fácil crear una instancia de DbContext sin ninguna conexión o cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="d2030-142">It is now easier to create a DbContext instance without any connection or connection string.</span></span>
<span data-ttu-id="d2030-143">Además, la conexión o la cadena de conexión ahora también se pueden mutar en la instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="d2030-143">Also, the connection or connection string can now be mutated on the context instance.</span></span>
<span data-ttu-id="d2030-144">Esta característica permite que la misma instancia de contexto se conecte de forma dinámica a diferentes bases de datos.</span><span class="sxs-lookup"><span data-stu-id="d2030-144">This feature allows the same context instance to dynamically connect to different databases.</span></span>

<span data-ttu-id="d2030-145">En el problema [n.º 2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-145">Documentation is tracked by issue [#2075](https://github.com/dotnet/EntityFramework.Docs/issues/2075).</span></span>

### <a name="change-tracking-proxies"></a><span data-ttu-id="d2030-146">Proxies de seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="d2030-146">Change-tracking proxies</span></span>

<span data-ttu-id="d2030-147">Ahora EF Core puede generar proxies del entorno de ejecución que implementen automáticamente [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) y [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span><span class="sxs-lookup"><span data-stu-id="d2030-147">EF Core can now generate runtime proxies that automatically implement [INotifyPropertyChanging](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanging?view=netcore-3.1) and [INotifyPropertyChanged](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?view=netcore-3.1).</span></span>
<span data-ttu-id="d2030-148">A continuación, los cambios de valor en las propiedades de las entidades se notifican directamente a EF Core, lo cual evita la necesidad de buscar los cambios.</span><span class="sxs-lookup"><span data-stu-id="d2030-148">These then report value changes on entity properties directly to EF Core, avoiding the need to scan for changes.</span></span>
<span data-ttu-id="d2030-149">Sin embargo, los proxies vienen con su propio conjunto de limitaciones, por lo que no son para todo el mundo.</span><span class="sxs-lookup"><span data-stu-id="d2030-149">However, proxies come with their own set of limitations, so they are not for everyone.</span></span>

<span data-ttu-id="d2030-150">En el problema [n.º 2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-150">Documentation is tracked by issue [#2076](https://github.com/dotnet/EntityFramework.Docs/issues/2076).</span></span>

### <a name="enhanced-debug-views"></a><span data-ttu-id="d2030-151">Vistas de depuración mejoradas</span><span class="sxs-lookup"><span data-stu-id="d2030-151">Enhanced debug views</span></span>

<span data-ttu-id="d2030-152">Las vistas de depuración son una forma fácil de consultar los aspectos internos de EF Core al depurar problemas.</span><span class="sxs-lookup"><span data-stu-id="d2030-152">Debug views are an easy way to look at the internals of EF Core when debugging issues.</span></span>
<span data-ttu-id="d2030-153">Hace algún tiempo ya se implementó una vista de depuración para el modelo.</span><span class="sxs-lookup"><span data-stu-id="d2030-153">A debug view for the Model was implemented some time ago.</span></span>
<span data-ttu-id="d2030-154">En el caso de EF Core 5.0, hemos facilitado la lectura de la vista de modelo y hemos agregado una nueva vista de depuración para las entidades de las que se ha realizado un seguimiento en el administrador de estado.</span><span class="sxs-lookup"><span data-stu-id="d2030-154">For EF Core 5.0, we have made the model view easier to read and added a new debug view for tracked entities in the state manager.</span></span>

<span data-ttu-id="d2030-155">La documentación preliminar se incluye en el [estado semanal de EF del 12 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span><span class="sxs-lookup"><span data-stu-id="d2030-155">Preliminary documentation is included in the [EF weekly status for December 12, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-565196206).</span></span>

<span data-ttu-id="d2030-156">En el problema [n.º 2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="d2030-156">Additional documentation is tracked by issue [#2086](https://github.com/dotnet/EntityFramework.Docs/issues/2086).</span></span>

### <a name="improved-handling-of-database-null-semantics"></a><span data-ttu-id="d2030-157">Control mejorado de la semántica de valores NULL de base de datos</span><span class="sxs-lookup"><span data-stu-id="d2030-157">Improved handling of database null semantics</span></span>

<span data-ttu-id="d2030-158">Normalmente, las bases de datos relacionales tratan NULL como valores desconocidos y, por lo tanto, no son iguales a otros valores NULL.</span><span class="sxs-lookup"><span data-stu-id="d2030-158">Relational databases typically treat NULL as an unknown value and therefore not equal to any other NULL.</span></span>
<span data-ttu-id="d2030-159">Mientras, C# trata NULL como un valor definido que se compara igual que cualquier otro valor NULL.</span><span class="sxs-lookup"><span data-stu-id="d2030-159">While C# treats null as a defined value, which compares equal to any other null.</span></span>
<span data-ttu-id="d2030-160">De forma predeterminada, EF Core traduce las consultas para que usen la semántica de valores NULL de C#.</span><span class="sxs-lookup"><span data-stu-id="d2030-160">EF Core by default translates queries so that they use C# null semantics.</span></span>
<span data-ttu-id="d2030-161">EF Core 5.0 mejora en gran medida la eficacia de dichas traducciones.</span><span class="sxs-lookup"><span data-stu-id="d2030-161">EF Core 5.0 greatly improves the efficiency of these translations.</span></span>

<span data-ttu-id="d2030-162">En el problema [n.º 1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-162">Documentation is tracked by issue [#1612](https://github.com/dotnet/EntityFramework.Docs/issues/1612).</span></span>

### <a name="indexer-properties"></a><span data-ttu-id="d2030-163">Propiedades del indizador</span><span class="sxs-lookup"><span data-stu-id="d2030-163">Indexer properties</span></span>

<span data-ttu-id="d2030-164">EF Core 5.0 admite la asignación de propiedades de indizador de C#.</span><span class="sxs-lookup"><span data-stu-id="d2030-164">EF Core 5.0 supports mapping of C# indexer properties.</span></span>
<span data-ttu-id="d2030-165">Estas propiedades permiten a las entidades actuar como bolsas de propiedades en las que las columnas se asignan a propiedades con nombre de la bolsa.</span><span class="sxs-lookup"><span data-stu-id="d2030-165">These properties allow entities to act as property bags where columns are mapped to named properties in the bag.</span></span>

<span data-ttu-id="d2030-166">En el problema [n.º 2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-166">Documentation is tracked by issue [#2018](https://github.com/dotnet/EntityFramework.Docs/issues/2018).</span></span>

### <a name="generation-of-check-constraints-for-enum-mappings"></a><span data-ttu-id="d2030-167">Generación de restricciones CHECK para las asignaciones de enumeración</span><span class="sxs-lookup"><span data-stu-id="d2030-167">Generation of check constraints for enum mappings</span></span>

<span data-ttu-id="d2030-168">Las migraciones de EF Core 5.0 ahora pueden generar restricciones CHECK para las asignaciones de propiedades de enumeración.</span><span class="sxs-lookup"><span data-stu-id="d2030-168">EF Core 5.0 Migrations can now generate CHECK constraints for enum property mappings.</span></span>
<span data-ttu-id="d2030-169">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d2030-169">For example:</span></span>

```SQL
MyEnumColumn VARCHAR(10) NOT NULL CHECK (MyEnumColumn IN ('Useful', 'Useless', 'Unknown'))
```

<span data-ttu-id="d2030-170">En el problema [n.º 2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-170">Documentation is tracked by issue [#2082](https://github.com/dotnet/EntityFramework.Docs/issues/2082).</span></span>

### <a name="isrelational"></a><span data-ttu-id="d2030-171">IsRelational</span><span class="sxs-lookup"><span data-stu-id="d2030-171">IsRelational</span></span>

<span data-ttu-id="d2030-172">Se ha agregado un nuevo método `IsRelational`, además de los existentes, que son `IsSqlServer`, `IsSqlite` y `IsInMemory`.</span><span class="sxs-lookup"><span data-stu-id="d2030-172">A new `IsRelational` method has been added in addition to the existing `IsSqlServer`, `IsSqlite`, and `IsInMemory`.</span></span>
<span data-ttu-id="d2030-173">Se puede usar este método para comprobar si DbContext está usando algún proveedor de bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="d2030-173">This method can be used to test if the DbContext is using any relational database provider.</span></span>
<span data-ttu-id="d2030-174">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d2030-174">For example:</span></span>

```CSharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    if (Database.IsRelational())
    {
        // Do relational-specific model configuration.
    }
}
```

<span data-ttu-id="d2030-175">En el problema [n.º 2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185) se realiza el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-175">Documentation is tracked by issue [#2185](https://github.com/dotnet/EntityFramework.Docs/issues/2185).</span></span>

### <a name="cosmos-optimistic-concurrency-with-etags"></a><span data-ttu-id="d2030-176">Simultaneidad optimista de Cosmos con mecanismos ETag</span><span class="sxs-lookup"><span data-stu-id="d2030-176">Cosmos optimistic concurrency with ETags</span></span>

<span data-ttu-id="d2030-177">El proveedor de bases de datos de Azure Cosmos DB ya es compatible con la simultaneidad optimista mediante mecanismos ETag.</span><span class="sxs-lookup"><span data-stu-id="d2030-177">The Azure Cosmos DB database provider now supports optimistic concurrency using ETags.</span></span>
<span data-ttu-id="d2030-178">Utilice el generador de modelos de OnModelCreating para confirmar un mecanismo ETag:</span><span class="sxs-lookup"><span data-stu-id="d2030-178">Use the model builder in OnModelCreating to configure an ETag:</span></span>

```CSharp
builder.Entity<Customer>().Property(c => c.ETag).IsEtagConcurrency();
```

<span data-ttu-id="d2030-179">Después, SaveChanges generará una excepción `DbUpdateConcurrencyException` en un conflicto de simultaneidad, que [se podrá manipular](https://docs.microsoft.com/ef/core/saving/concurrency), por ejemplo, para implementar reintentos.</span><span class="sxs-lookup"><span data-stu-id="d2030-179">SaveChanges will then throw an `DbUpdateConcurrencyException` on a concurrency conflict, which [can be handled](https://docs.microsoft.com/ef/core/saving/concurrency) to implement retries, etc.</span></span>

<span data-ttu-id="d2030-180">En el problema [n.º 2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-180">Documentation is tracked by issue [#2099](https://github.com/dotnet/EntityFramework.Docs/issues/2099).</span></span>

### <a name="query-translations-for-more-datetime-constructs"></a><span data-ttu-id="d2030-181">Traducciones de consultas para más construcciones DateTime</span><span class="sxs-lookup"><span data-stu-id="d2030-181">Query translations for more DateTime constructs</span></span>

<span data-ttu-id="d2030-182">Ahora las consultas que contienen la nueva construcción DateTime se traducen.</span><span class="sxs-lookup"><span data-stu-id="d2030-182">Queries containing new DateTime construction are now translated.</span></span>

<span data-ttu-id="d2030-183">Además, ahora se asignan las funciones de SQL Server que hay a continuación:</span><span class="sxs-lookup"><span data-stu-id="d2030-183">In addition, the following SQL Server functions are now mapped:</span></span>

* <span data-ttu-id="d2030-184">DateDiffWeek</span><span class="sxs-lookup"><span data-stu-id="d2030-184">DateDiffWeek</span></span>
* <span data-ttu-id="d2030-185">DateFromParts</span><span class="sxs-lookup"><span data-stu-id="d2030-185">DateFromParts</span></span>

<span data-ttu-id="d2030-186">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d2030-186">For example:</span></span>

```CSharp
var count = context.Orders.Count(c => date > EF.Functions.DateFromParts(DateTime.Now.Year, 12, 25));

```

<span data-ttu-id="d2030-187">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-187">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translations-for-more-byte-array-constructs"></a><span data-ttu-id="d2030-188">Traducciones de consultas para más construcciones de matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="d2030-188">Query translations for more byte array constructs</span></span>

<span data-ttu-id="d2030-189">Las consultas que usan Contains, Length, SequenceEqual, etc. en las propiedades byte[] ahora se traducen a SQL.</span><span class="sxs-lookup"><span data-stu-id="d2030-189">Queries using Contains, Length, SequenceEqual, etc. on byte[] properties are now translated to SQL.</span></span>

<span data-ttu-id="d2030-190">La documentación preliminar se incluye en el [estado semanal de EF del 5 de diciembre de 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span><span class="sxs-lookup"><span data-stu-id="d2030-190">Preliminary documentation is included in the [EF weekly status for December 5, 2019](https://github.com/dotnet/efcore/issues/15403#issuecomment-562332863).</span></span>

<span data-ttu-id="d2030-191">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) se realiza el seguimiento de la documentación adicional.</span><span class="sxs-lookup"><span data-stu-id="d2030-191">Additional documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-reverse"></a><span data-ttu-id="d2030-192">Traducción de consultas para Reverse</span><span class="sxs-lookup"><span data-stu-id="d2030-192">Query translation for Reverse</span></span>

<span data-ttu-id="d2030-193">Las consultas que usan `Reverse` ahora se traducen.</span><span class="sxs-lookup"><span data-stu-id="d2030-193">Queries using `Reverse` are now translated.</span></span>
<span data-ttu-id="d2030-194">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d2030-194">For example:</span></span>

```CSharp
context.Employees.OrderBy(e => e.EmployeeID).Reverse()
```

<span data-ttu-id="d2030-195">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-195">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-bitwise-operators"></a><span data-ttu-id="d2030-196">Traducción de consultas para operadores bit a bit</span><span class="sxs-lookup"><span data-stu-id="d2030-196">Query translation for bitwise operators</span></span>

<span data-ttu-id="d2030-197">Las consultas que usan operadores bit a bit ahora se traducen en más casos, por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d2030-197">Queries using bitwise operators are now translated in more cases For example:</span></span>

```CSharp
context.Orders.Where(o => ~o.OrderID == negatedId)
```

<span data-ttu-id="d2030-198">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-198">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>

### <a name="query-translation-for-strings-on-cosmos"></a><span data-ttu-id="d2030-199">Traducción de consultas para cadenas en Cosmos</span><span class="sxs-lookup"><span data-stu-id="d2030-199">Query translation for strings on Cosmos</span></span>

<span data-ttu-id="d2030-200">Al emplear el proveedor Azure Cosmos DB, las consultas que usan los métodos de cadena Contains, StartsWith y EndsWith ahora se traducen.</span><span class="sxs-lookup"><span data-stu-id="d2030-200">Queries that use the string methods Contains, StartsWith, and EndsWith are now translated when using the Azure Cosmos DB provider.</span></span>

<span data-ttu-id="d2030-201">En el problema [n.º 2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079) realiza se el seguimiento de la documentación.</span><span class="sxs-lookup"><span data-stu-id="d2030-201">Documentation is tracked by issue [#2079](https://github.com/dotnet/EntityFramework.Docs/issues/2079).</span></span>
