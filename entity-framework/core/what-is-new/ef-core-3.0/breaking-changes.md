---
title: 'Cambios importantes en EF Core 3.0: EF Core'
author: ajcvickers
ms.date: 12/03/2019
uid: core/what-is-new/ef-core-3.0/breaking-changes
ms.openlocfilehash: 6e0c17a22b56b206f18e47f678e3e237d5c42375
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/07/2020
ms.locfileid: "78413562"
---
# <a name="breaking-changes-included-in-ef-core-30"></a><span data-ttu-id="56f1e-102">Cambios importantes incluidos en EF Core 3.0</span><span class="sxs-lookup"><span data-stu-id="56f1e-102">Breaking changes included in EF Core 3.0</span></span>

<span data-ttu-id="56f1e-103">Es posible que los siguientes cambios de API y comportamiento interrumpan las aplicaciones actuales cuando se actualicen a la versión 3.0.0.</span><span class="sxs-lookup"><span data-stu-id="56f1e-103">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.0.0.</span></span>
<span data-ttu-id="56f1e-104">Los cambios que esperamos que solo afecten a proveedores de base de datos se documentan en [Cambios para proveedores](xref:core/providers/provider-log).</span><span class="sxs-lookup"><span data-stu-id="56f1e-104">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="56f1e-105">Resumen</span><span class="sxs-lookup"><span data-stu-id="56f1e-105">Summary</span></span>

| <span data-ttu-id="56f1e-106">**Cambio importante**</span><span class="sxs-lookup"><span data-stu-id="56f1e-106">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="56f1e-107">**Impacto**</span><span class="sxs-lookup"><span data-stu-id="56f1e-107">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="56f1e-108">Las consultas LINQ ya no se evalúan en el cliente</span><span class="sxs-lookup"><span data-stu-id="56f1e-108">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="56f1e-109">Alto</span><span class="sxs-lookup"><span data-stu-id="56f1e-109">High</span></span>       |
| [<span data-ttu-id="56f1e-110">EF Core 3.0 tiene como destino .NET Standard 2.1, y no .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="56f1e-110">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>](#netstandard21) | <span data-ttu-id="56f1e-111">Alto</span><span class="sxs-lookup"><span data-stu-id="56f1e-111">High</span></span>      |
| [<span data-ttu-id="56f1e-112">La herramienta de línea de comandos de EF Core, dotnet ef, ya no forma parte del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="56f1e-112">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="56f1e-113">Alto</span><span class="sxs-lookup"><span data-stu-id="56f1e-113">High</span></span>      |
| [<span data-ttu-id="56f1e-114">DetectChanges respeta los valores de clave generados por el almacén</span><span class="sxs-lookup"><span data-stu-id="56f1e-114">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="56f1e-115">Alto</span><span class="sxs-lookup"><span data-stu-id="56f1e-115">High</span></span>      |
| [<span data-ttu-id="56f1e-116">FromSql, ExecuteSql y ExecuteSqlAsync han cambiado de nombre</span><span class="sxs-lookup"><span data-stu-id="56f1e-116">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="56f1e-117">Alto</span><span class="sxs-lookup"><span data-stu-id="56f1e-117">High</span></span>      |
| [<span data-ttu-id="56f1e-118">Los tipos de consulta se consolidan con tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="56f1e-118">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="56f1e-119">Alto</span><span class="sxs-lookup"><span data-stu-id="56f1e-119">High</span></span>      |
| [<span data-ttu-id="56f1e-120">Entity Framework Core ya no forma parte del marco compartido ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56f1e-120">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="56f1e-121">Media</span><span class="sxs-lookup"><span data-stu-id="56f1e-121">Medium</span></span>      |
| [<span data-ttu-id="56f1e-122">Las eliminaciones en cascada ahora se realizan inmediatamente de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="56f1e-122">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="56f1e-123">Media</span><span class="sxs-lookup"><span data-stu-id="56f1e-123">Medium</span></span>      |
| [<span data-ttu-id="56f1e-124">La carga diligente de entidades relacionadas ahora se realiza en una sola consulta</span><span class="sxs-lookup"><span data-stu-id="56f1e-124">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="56f1e-125">Media</span><span class="sxs-lookup"><span data-stu-id="56f1e-125">Medium</span></span>      |
| [<span data-ttu-id="56f1e-126">DeleteBehavior.Restrict tiene una semántica más limpia</span><span class="sxs-lookup"><span data-stu-id="56f1e-126">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="56f1e-127">Media</span><span class="sxs-lookup"><span data-stu-id="56f1e-127">Medium</span></span>      |
| [<span data-ttu-id="56f1e-128">La API de configuración para las relaciones de tipo de propiedad ha cambiado</span><span class="sxs-lookup"><span data-stu-id="56f1e-128">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="56f1e-129">Media</span><span class="sxs-lookup"><span data-stu-id="56f1e-129">Medium</span></span>      |
| [<span data-ttu-id="56f1e-130">Cada propiedad usa la generación de claves enteras en memoria independiente</span><span class="sxs-lookup"><span data-stu-id="56f1e-130">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="56f1e-131">Media</span><span class="sxs-lookup"><span data-stu-id="56f1e-131">Medium</span></span>      |
| [<span data-ttu-id="56f1e-132">Las consultas sin seguimiento ya no realizan la resolución de la identidad</span><span class="sxs-lookup"><span data-stu-id="56f1e-132">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="56f1e-133">Media</span><span class="sxs-lookup"><span data-stu-id="56f1e-133">Medium</span></span>      |
| [<span data-ttu-id="56f1e-134">Cambios en la API de metadatos</span><span class="sxs-lookup"><span data-stu-id="56f1e-134">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="56f1e-135">Media</span><span class="sxs-lookup"><span data-stu-id="56f1e-135">Medium</span></span>      |
| [<span data-ttu-id="56f1e-136">Cambios en la API de metadatos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="56f1e-136">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="56f1e-137">Media</span><span class="sxs-lookup"><span data-stu-id="56f1e-137">Medium</span></span>      |
| [<span data-ttu-id="56f1e-138">Se ha quitado el elemento UseRowNumberForPaging</span><span class="sxs-lookup"><span data-stu-id="56f1e-138">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="56f1e-139">Media</span><span class="sxs-lookup"><span data-stu-id="56f1e-139">Medium</span></span>      |
| [<span data-ttu-id="56f1e-140">Cuando el método FromSql se usa con un procedimiento almacenado no se puede redactar</span><span class="sxs-lookup"><span data-stu-id="56f1e-140">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="56f1e-141">Media</span><span class="sxs-lookup"><span data-stu-id="56f1e-141">Medium</span></span>      |
| [<span data-ttu-id="56f1e-142">Solo se pueden especificar métodos de FromSql en raíces de consulta</span><span class="sxs-lookup"><span data-stu-id="56f1e-142">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="56f1e-143">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-143">Low</span></span>      |
| [<span data-ttu-id="56f1e-144">~~La ejecución de consultas se registra en el nivel de depuración~~ Revertido</span><span class="sxs-lookup"><span data-stu-id="56f1e-144">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | <span data-ttu-id="56f1e-145">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-145">Low</span></span>      |
| [<span data-ttu-id="56f1e-146">Los valores de clave temporal ya no se establecen en instancias de entidad</span><span class="sxs-lookup"><span data-stu-id="56f1e-146">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="56f1e-147">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-147">Low</span></span>      |
| [<span data-ttu-id="56f1e-148">Las entidades dependientes que comparten la tabla con la entidad de seguridad son ahora opcionales</span><span class="sxs-lookup"><span data-stu-id="56f1e-148">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="56f1e-149">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-149">Low</span></span>      |
| [<span data-ttu-id="56f1e-150">Todas las entidades que compartan una tabla con una columna de token de simultaneidad tienen que asignarla a una propiedad</span><span class="sxs-lookup"><span data-stu-id="56f1e-150">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="56f1e-151">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-151">Low</span></span>      |
| [<span data-ttu-id="56f1e-152">Las entidades en propiedad no se pueden consultar sin el propietario mediante una consulta de seguimiento</span><span class="sxs-lookup"><span data-stu-id="56f1e-152">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="56f1e-153">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-153">Low</span></span>      |
| [<span data-ttu-id="56f1e-154">Las propiedades heredadas de tipos sin asignar se asignan ahora a una única columna para todos los tipos derivados</span><span class="sxs-lookup"><span data-stu-id="56f1e-154">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="56f1e-155">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-155">Low</span></span>      |
| [<span data-ttu-id="56f1e-156">La convención de propiedad de clave externa ya no coincide con el mismo nombre que la propiedad de entidad de seguridad</span><span class="sxs-lookup"><span data-stu-id="56f1e-156">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="56f1e-157">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-157">Low</span></span>      |
| [<span data-ttu-id="56f1e-158">La conexión de base de datos ahora se cierra si ya no se usa antes de que se complete TransactionScope</span><span class="sxs-lookup"><span data-stu-id="56f1e-158">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="56f1e-159">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-159">Low</span></span>      |
| [<span data-ttu-id="56f1e-160">Los campos de respaldo se usan de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="56f1e-160">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="56f1e-161">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-161">Low</span></span>      |
| [<span data-ttu-id="56f1e-162">Inicio de excepciones si se encuentran varios campos de respaldo compatibles</span><span class="sxs-lookup"><span data-stu-id="56f1e-162">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="56f1e-163">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-163">Low</span></span>      |
| [<span data-ttu-id="56f1e-164">Los nombres de propiedades de solo campo deben coincidir con el nombre del campo</span><span class="sxs-lookup"><span data-stu-id="56f1e-164">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="56f1e-165">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-165">Low</span></span>      |
| [<span data-ttu-id="56f1e-166">AddDbContext/AddDbContextPool ya no llaman a AddLogging ni a AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="56f1e-166">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="56f1e-167">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-167">Low</span></span>      |
| [<span data-ttu-id="56f1e-168">AddEntityFramework\* agrega IMemoryCache con un límite de tamaño</span><span class="sxs-lookup"><span data-stu-id="56f1e-168">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="56f1e-169">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-169">Low</span></span>      |
| [<span data-ttu-id="56f1e-170">DbContext.Entry realiza ahora una operación DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="56f1e-170">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="56f1e-171">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-171">Low</span></span>      |
| [<span data-ttu-id="56f1e-172">El cliente no genera las claves de matriz de cadena y byte de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="56f1e-172">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="56f1e-173">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-173">Low</span></span>      |
| [<span data-ttu-id="56f1e-174">ILoggerFactory es ahora un servicio con ámbito</span><span class="sxs-lookup"><span data-stu-id="56f1e-174">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="56f1e-175">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-175">Low</span></span>      |
| [<span data-ttu-id="56f1e-176">En los proxies de carga diferida ya no se supone que las propiedades de navegación están totalmente cargadas</span><span class="sxs-lookup"><span data-stu-id="56f1e-176">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="56f1e-177">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-177">Low</span></span>      |
| [<span data-ttu-id="56f1e-178">La creación excesiva de proveedores de servicios internos ahora es un error de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="56f1e-178">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="56f1e-179">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-179">Low</span></span>      |
| [<span data-ttu-id="56f1e-180">Comportamiento nuevo de HasOne/HasMany llamado con una sola cadena</span><span class="sxs-lookup"><span data-stu-id="56f1e-180">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="56f1e-181">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-181">Low</span></span>      |
| [<span data-ttu-id="56f1e-182">El tipo de valor devuelto para varios métodos asincrónicos se ha cambiado de Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="56f1e-182">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="56f1e-183">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-183">Low</span></span>      |
| [<span data-ttu-id="56f1e-184">La anotación Relational:TypeMapping ahora es simplemente TypeMapping</span><span class="sxs-lookup"><span data-stu-id="56f1e-184">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="56f1e-185">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-185">Low</span></span>      |
| [<span data-ttu-id="56f1e-186">ToTable en un tipo derivado produce una excepción</span><span class="sxs-lookup"><span data-stu-id="56f1e-186">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="56f1e-187">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-187">Low</span></span>      |
| [<span data-ttu-id="56f1e-188">EF Core ya no envía pragma para el cumplimiento de SQLite FK</span><span class="sxs-lookup"><span data-stu-id="56f1e-188">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="56f1e-189">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-189">Low</span></span>      |
| [<span data-ttu-id="56f1e-190">Microsoft.EntityFrameworkCore.Sqlite ahora depende de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="56f1e-190">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="56f1e-191">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-191">Low</span></span>      |
| [<span data-ttu-id="56f1e-192">Los valores GUID se almacenan ahora como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="56f1e-192">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="56f1e-193">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-193">Low</span></span>      |
| [<span data-ttu-id="56f1e-194">Los valores char se almacenan ahora como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="56f1e-194">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="56f1e-195">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-195">Low</span></span>      |
| [<span data-ttu-id="56f1e-196">Los id. de migración ahora se generan usando el calendario de la referencia cultural invariable</span><span class="sxs-lookup"><span data-stu-id="56f1e-196">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="56f1e-197">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-197">Low</span></span>      |
| [<span data-ttu-id="56f1e-198">La información o los metadatos de la extensión se han quitado de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="56f1e-198">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="56f1e-199">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-199">Low</span></span>      |
| [<span data-ttu-id="56f1e-200">LogQueryPossibleExceptionWithAggregateOperator ha cambiado de nombre</span><span class="sxs-lookup"><span data-stu-id="56f1e-200">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="56f1e-201">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-201">Low</span></span>      |
| [<span data-ttu-id="56f1e-202">Clarificación de la API para nombres de restricciones de claves externas</span><span class="sxs-lookup"><span data-stu-id="56f1e-202">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="56f1e-203">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-203">Low</span></span>      |
| [<span data-ttu-id="56f1e-204">IRelationalDatabaseCreator.HasTables/HasTablesAsync se han hecho públicos</span><span class="sxs-lookup"><span data-stu-id="56f1e-204">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="56f1e-205">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-205">Low</span></span>      |
| [<span data-ttu-id="56f1e-206">Microsoft.EntityFrameworkCore.Design es ahora un paquete DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="56f1e-206">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="56f1e-207">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-207">Low</span></span>      |
| [<span data-ttu-id="56f1e-208">SQLitePCL.raw se ha actualizado a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="56f1e-208">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="56f1e-209">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-209">Low</span></span>      |
| [<span data-ttu-id="56f1e-210">NetTopologySuite se actualizó a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="56f1e-210">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="56f1e-211">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-211">Low</span></span>      |
| [<span data-ttu-id="56f1e-212">Se usa Microsoft.Data.SqlClient en lugar de System.Data.SqlClient</span><span class="sxs-lookup"><span data-stu-id="56f1e-212">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="56f1e-213">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-213">Low</span></span>      |
| [<span data-ttu-id="56f1e-214">Se deben configurar varias relaciones de referencia automática ambiguas</span><span class="sxs-lookup"><span data-stu-id="56f1e-214">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="56f1e-215">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-215">Low</span></span>      |
| [<span data-ttu-id="56f1e-216">DbFunction.Schema es NULL o la cadena vacía lo configura para estar en el esquema predeterminado del modelo</span><span class="sxs-lookup"><span data-stu-id="56f1e-216">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="56f1e-217">Bajo</span><span class="sxs-lookup"><span data-stu-id="56f1e-217">Low</span></span>      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="56f1e-218">Las consultas LINQ ya no se evalúan en el cliente</span><span class="sxs-lookup"><span data-stu-id="56f1e-218">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="56f1e-219">[Problema de seguimiento n.° 14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Consulte también el problema n.° 12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="56f1e-219">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="56f1e-220">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-220">**Old behavior**</span></span>

<span data-ttu-id="56f1e-221">Antes de 3.0, cuando en EF Core no se podía convertir una expresión que formaba parte de una consulta SQL o un parámetro, la expresión se evaluaba de forma automática en el cliente.</span><span class="sxs-lookup"><span data-stu-id="56f1e-221">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="56f1e-222">De forma predeterminada, la evaluación de cliente de expresiones potencialmente costosas solo desencadenaba una advertencia.</span><span class="sxs-lookup"><span data-stu-id="56f1e-222">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="56f1e-223">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-223">**New behavior**</span></span>

<span data-ttu-id="56f1e-224">A partir de 3.0, en EF Core solo se permite que se evalúen en el cliente las expresiones en la proyección de nivel superior (la última llamada a `Select()` de la consulta).</span><span class="sxs-lookup"><span data-stu-id="56f1e-224">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="56f1e-225">Cuando las expresiones de otra parte de la consulta no se pueden convertir en SQL o un parámetro, se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="56f1e-225">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="56f1e-226">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-226">**Why**</span></span>

<span data-ttu-id="56f1e-227">La evaluación de cliente automática de las consultas permite que se ejecuten muchas consultas incluso si no se pueden convertir elementos importantes de ellas.</span><span class="sxs-lookup"><span data-stu-id="56f1e-227">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="56f1e-228">Esto puede provocar un comportamiento inesperado y potencialmente dañino que es posible que solo sea evidente en entornos de producción.</span><span class="sxs-lookup"><span data-stu-id="56f1e-228">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="56f1e-229">Por ejemplo, una condición en una llamada a `Where()` que no se puede convertir puede provocar que todas las filas de la tabla se transfieran desde el servidor de base de datos y que el filtro se aplique en el cliente.</span><span class="sxs-lookup"><span data-stu-id="56f1e-229">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="56f1e-230">Esta situación puede pasar desapercibida fácilmente si la tabla solo contiene algunas filas en la fase de desarrollo, pero ser más grave cuando la aplicación pase a producción, donde la tabla puede contener millones de filas.</span><span class="sxs-lookup"><span data-stu-id="56f1e-230">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="56f1e-231">Las advertencias de evaluación de cliente también se suelen pasar por alto durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="56f1e-231">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="56f1e-232">Además de esto, la evaluación de cliente automática puede causar problemas en los que la mejora de la traducción de consultas para expresiones específicas provocaba cambios importantes no deseados entre versiones.</span><span class="sxs-lookup"><span data-stu-id="56f1e-232">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="56f1e-233">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-233">**Mitigations**</span></span>

<span data-ttu-id="56f1e-234">Si una consulta no se puede traducir totalmente, vuelva a escribirla en un formato que se pueda traducir, o bien use `AsEnumerable()`, `ToList()` o una función similar para devolver los datos al cliente de forma explícita, donde después se puedan seguir procesando mediante LINQ to Objects.</span><span class="sxs-lookup"><span data-stu-id="56f1e-234">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a><span data-ttu-id="56f1e-235">EF Core 3.0 tiene como destino .NET Standard 2.1, y no .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="56f1e-235">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>

[<span data-ttu-id="56f1e-236">Problema de seguimiento n.º 15498</span><span class="sxs-lookup"><span data-stu-id="56f1e-236">Tracking Issue #15498</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

> [!IMPORTANT] 
> <span data-ttu-id="56f1e-237">EF Core 3.1 vuelve a tener como objetivo a .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="56f1e-237">EF Core 3.1 targets .NET Standard 2.0 again.</span></span> <span data-ttu-id="56f1e-238">Esto reincorpora la compatibilidad con .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="56f1e-238">This brings back support for .NET Framework.</span></span>

<span data-ttu-id="56f1e-239">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-239">**Old behavior**</span></span>

<span data-ttu-id="56f1e-240">Antes de la versión 3.0, EF Core tenía como destino .NET Standard 2.0 y se podía ejecutar en todas las plataformas que admitieran dicho estándar, incluido .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="56f1e-240">Before 3.0, EF Core targeted .NET Standard 2.0 and would run on all platforms that support that standard, including .NET Framework.</span></span>

<span data-ttu-id="56f1e-241">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-241">**New behavior**</span></span>

<span data-ttu-id="56f1e-242">A partir de la versión 3.0, EF Core tiene como destino .NET Standard 2.1 y se puede ejecutar en todas las plataformas que admitan dicho estándar.</span><span class="sxs-lookup"><span data-stu-id="56f1e-242">Starting with 3.0, EF Core targets .NET Standard 2.1 and will run on all platforms that support this standard.</span></span> <span data-ttu-id="56f1e-243">Esto no incluye .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="56f1e-243">This does not include .NET Framework.</span></span>

<span data-ttu-id="56f1e-244">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-244">**Why**</span></span>

<span data-ttu-id="56f1e-245">Esto forma parte de una decisión estratégica para todas las tecnologías de .NET que tiene como objetivo centrar los esfuerzos en .NET Core y otras plataformas modernas de .NET, como Xamarin.</span><span class="sxs-lookup"><span data-stu-id="56f1e-245">This is part of a strategic decision across .NET technologies to focus energy on .NET Core and other modern .NET platforms, such as Xamarin.</span></span>

<span data-ttu-id="56f1e-246">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-246">**Mitigations**</span></span>

<span data-ttu-id="56f1e-247">Use EF Core 3.1.</span><span class="sxs-lookup"><span data-stu-id="56f1e-247">Use EF Core 3.1.</span></span>

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="56f1e-248">Entity Framework Core ya no forma parte del marco compartido ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="56f1e-248">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="56f1e-249">Anuncios del problema de seguimiento n.º 325</span><span class="sxs-lookup"><span data-stu-id="56f1e-249">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="56f1e-250">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-250">**Old behavior**</span></span>

<span data-ttu-id="56f1e-251">Antes de ASP.NET Core 3.0, cuando se agregaba una referencia de paquete a `Microsoft.AspNetCore.App` o `Microsoft.AspNetCore.All`, se incluía EF Core y algunos de los proveedores de datos de EF Core, como el de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="56f1e-251">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="56f1e-252">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-252">**New behavior**</span></span>

<span data-ttu-id="56f1e-253">A partir de la versión 3.0, el marco compartido ASP.NET Core no incluye EF Core ni ningún proveedor de datos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="56f1e-253">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="56f1e-254">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-254">**Why**</span></span>

<span data-ttu-id="56f1e-255">Antes de este cambio, para obtener EF Core se necesitaban varios pasos en función de si la aplicación se destinaba a ASP.NET Core y SQL Server o no.</span><span class="sxs-lookup"><span data-stu-id="56f1e-255">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="56f1e-256">Además, la actualización de ASP.NET Core forzaba la de EF Core y el proveedor de SQL Server, lo que no siempre es deseable.</span><span class="sxs-lookup"><span data-stu-id="56f1e-256">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="56f1e-257">Con este cambio, la experiencia de obtención de EF Core es la misma en todos los proveedores, implementaciones admitidas de .NET y tipos de aplicación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-257">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="56f1e-258">Ahora los desarrolladores también pueden controlar exactamente cuándo se actualizan EF Core y los proveedores de datos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="56f1e-258">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="56f1e-259">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-259">**Mitigations**</span></span>

<span data-ttu-id="56f1e-260">Para usar EF Core en una aplicación ASP.NET Core 3.0 o cualquier otra aplicación compatible, debe agregar de forma explícita una referencia de paquete al proveedor de base de datos de EF Core que se va a usar en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-260">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="56f1e-261">La herramienta de línea de comandos de EF Core, dotnet ef, ya no forma parte del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="56f1e-261">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="56f1e-262">Problema de seguimiento n.º 14016</span><span class="sxs-lookup"><span data-stu-id="56f1e-262">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="56f1e-263">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-263">**Old behavior**</span></span>

<span data-ttu-id="56f1e-264">Antes de 3.0, la herramienta `dotnet ef` se incluía en el SDK de .NET Core y estaba disponible para usarse desde la línea de comandos de cualquier proyecto sin necesidad de realizar pasos adicionales.</span><span class="sxs-lookup"><span data-stu-id="56f1e-264">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="56f1e-265">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-265">**New behavior**</span></span>

<span data-ttu-id="56f1e-266">A partir de la versión 3.0, el SDK de .NET no incluye la herramienta `dotnet ef`, por lo que antes de poder usarla tendrá que instalarla de forma explícita como una herramienta local o global.</span><span class="sxs-lookup"><span data-stu-id="56f1e-266">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="56f1e-267">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-267">**Why**</span></span>

<span data-ttu-id="56f1e-268">Este cambio nos permite distribuir y actualizar `dotnet ef` como una herramienta convencional de la CLI de .NET en NuGet, coherente con el hecho de que la versión 3.0 de EF Core también se distribuye siempre como un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="56f1e-268">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="56f1e-269">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-269">**Mitigations**</span></span>

<span data-ttu-id="56f1e-270">Para poder administrar las migraciones o aplicar scaffolding a `DbContext`, instale `dotnet-ef` como herramienta global:</span><span class="sxs-lookup"><span data-stu-id="56f1e-270">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

  ``` console
    $ dotnet tool install --global dotnet-ef
  ```

<span data-ttu-id="56f1e-271">También se puede obtener una herramienta local cuando se restauran las dependencias de un proyecto que la declara como una dependencia de herramientas mediante un [archivo de manifiesto de herramientas](https://github.com/dotnet/cli/issues/10288).</span><span class="sxs-lookup"><span data-stu-id="56f1e-271">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](https://github.com/dotnet/cli/issues/10288).</span></span>

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="56f1e-272">FromSql, ExecuteSql y ExecuteSqlAsync han cambiado de nombre</span><span class="sxs-lookup"><span data-stu-id="56f1e-272">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="56f1e-273">Problema de seguimiento n.º 10996</span><span class="sxs-lookup"><span data-stu-id="56f1e-273">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="56f1e-274">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-274">**Old behavior**</span></span>

<span data-ttu-id="56f1e-275">Antes de EF Core 3.0, estos nombres de métodos se sobrecargaban para funcionar tanto con una cadena normal como con una cadena que se debería interpolar en SQL y parámetros.</span><span class="sxs-lookup"><span data-stu-id="56f1e-275">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="56f1e-276">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-276">**New behavior**</span></span>

<span data-ttu-id="56f1e-277">A partir de la versión EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw` y `ExecuteSqlRawAsync` para crear una consulta con parámetros donde los parámetros se pasan por separado de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="56f1e-277">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="56f1e-278">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-278">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="56f1e-279">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated` y `ExecuteSqlInterpolatedAsync` para crear una consulta con parámetros donde los parámetros se pasan como parte de una cadena de consulta interpolada.</span><span class="sxs-lookup"><span data-stu-id="56f1e-279">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="56f1e-280">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-280">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="56f1e-281">Tenga en cuenta que las dos consultas anteriores producirán el mismo código SQL parametrizado con los mismos parámetros SQL.</span><span class="sxs-lookup"><span data-stu-id="56f1e-281">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="56f1e-282">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-282">**Why**</span></span>

<span data-ttu-id="56f1e-283">Las sobrecargas del método como esta facilitan las llamadas accidentales al método de cadena sin procesar cuando la intención era llamar al método de cadena interpolada y viceversa.</span><span class="sxs-lookup"><span data-stu-id="56f1e-283">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="56f1e-284">Esto podría resultar en consultas que no se parametrizan cuando deberían.</span><span class="sxs-lookup"><span data-stu-id="56f1e-284">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="56f1e-285">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-285">**Mitigations**</span></span>

<span data-ttu-id="56f1e-286">Haga el cambio para usar los nuevos nombres de métodos.</span><span class="sxs-lookup"><span data-stu-id="56f1e-286">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="56f1e-287">Cuando el método FromSql se usa con un procedimiento almacenado no se puede redactar</span><span class="sxs-lookup"><span data-stu-id="56f1e-287">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="56f1e-288">Problema de seguimiento n.° 15392</span><span class="sxs-lookup"><span data-stu-id="56f1e-288">Tracking Issue #15392</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

<span data-ttu-id="56f1e-289">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-289">**Old behavior**</span></span>

<span data-ttu-id="56f1e-290">Antes de EF Core 3.0, el método FromSql intentaba detectar si se podía redactar en el código SQL pasado.</span><span class="sxs-lookup"><span data-stu-id="56f1e-290">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="56f1e-291">Cuando el código SQL no se podía redactar, como un procedimiento almacenado, realizaba la evaluación de cliente.</span><span class="sxs-lookup"><span data-stu-id="56f1e-291">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="56f1e-292">La consulta siguiente funcionaba al ejecutar el procedimiento almacenado en el servidor y aplicar FirstOrDefault en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="56f1e-292">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

<span data-ttu-id="56f1e-293">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-293">**New behavior**</span></span>

<span data-ttu-id="56f1e-294">A partir de EF Core 3.0, EF Core no intentará analizar el código SQL.</span><span class="sxs-lookup"><span data-stu-id="56f1e-294">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="56f1e-295">Por tanto, si va a redactar después de FromSqlRaw/FromSqlInterpolated, EF Core redactará el código SQL generando una subconsulta.</span><span class="sxs-lookup"><span data-stu-id="56f1e-295">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="56f1e-296">Por tanto, si usa un procedimiento almacenado con la redacción, obtendrá una excepción de sintaxis de SQL no válida.</span><span class="sxs-lookup"><span data-stu-id="56f1e-296">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

<span data-ttu-id="56f1e-297">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-297">**Why**</span></span>

<span data-ttu-id="56f1e-298">EF Core 3.0 no admite la evaluación automática de cliente, ya que era propenso a errores, como se explica [aquí](#linq-queries-are-no-longer-evaluated-on-the-client).</span><span class="sxs-lookup"><span data-stu-id="56f1e-298">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

<span data-ttu-id="56f1e-299">**Mitigación**</span><span class="sxs-lookup"><span data-stu-id="56f1e-299">**Mitigation**</span></span>

<span data-ttu-id="56f1e-300">Si usa un procedimiento almacenado en FromSqlRaw/FromSqlInterpolated, sabe que no se puede redactar, por lo que puede agregar __AsEnumerable/AsAsyncEnumerable__ justo después de la llamada al método FromSql para evitar cualquier redacción en el lado servidor.</span><span class="sxs-lookup"><span data-stu-id="56f1e-300">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="56f1e-301">Solo se pueden especificar métodos de FromSql en raíces de consulta.</span><span class="sxs-lookup"><span data-stu-id="56f1e-301">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="56f1e-302">Problema de seguimiento n.° 15704</span><span class="sxs-lookup"><span data-stu-id="56f1e-302">Tracking Issue #15704</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

<span data-ttu-id="56f1e-303">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-303">**Old behavior**</span></span>

<span data-ttu-id="56f1e-304">Antes de EF Core 3.0, el método `FromSql` podía especificarse en cualquier lugar en la consulta.</span><span class="sxs-lookup"><span data-stu-id="56f1e-304">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="56f1e-305">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-305">**New behavior**</span></span>

<span data-ttu-id="56f1e-306">A partir de EF Core 3.0, los nuevos métodos `FromSqlRaw` y `FromSqlInterpolated` (que reemplazan a `FromSql`) solo pueden especificarse en las raíces de la consulta, es decir, directamente en `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-306">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="56f1e-307">Si intenta especificarlos en cualquier otro lugar se producirá un error de compilación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-307">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="56f1e-308">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-308">**Why**</span></span>

<span data-ttu-id="56f1e-309">La especificación de `FromSql` en cualquier otro lugar diferente de `DbSet` no tenía un significado o valor agregado, y podría causar ambigüedad en ciertos escenarios.</span><span class="sxs-lookup"><span data-stu-id="56f1e-309">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="56f1e-310">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-310">**Mitigations**</span></span>

<span data-ttu-id="56f1e-311">Las invocaciones de `FromSql` se deben mover para que estén directamente en el `DbSet` al que se aplican.</span><span class="sxs-lookup"><span data-stu-id="56f1e-311">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="56f1e-312">Las consultas sin seguimiento ya no realizan la resolución de la identidad</span><span class="sxs-lookup"><span data-stu-id="56f1e-312">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="56f1e-313">Problema de seguimiento n.º 13518</span><span class="sxs-lookup"><span data-stu-id="56f1e-313">Tracking Issue #13518</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

<span data-ttu-id="56f1e-314">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-314">**Old behavior**</span></span>

<span data-ttu-id="56f1e-315">Antes de EF Core 3.0, se usaba la misma instancia de la entidad para cada aparición de una entidad con un tipo e identificador determinados.</span><span class="sxs-lookup"><span data-stu-id="56f1e-315">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="56f1e-316">Este comportamiento coincide con el de las consultas de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="56f1e-316">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="56f1e-317">Por ejemplo, esta consulta:</span><span class="sxs-lookup"><span data-stu-id="56f1e-317">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
<span data-ttu-id="56f1e-318">Esta consulta devolverá la misma instancia de `Category` para cada elemento `Product` asociado con la categoría determinada.</span><span class="sxs-lookup"><span data-stu-id="56f1e-318">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

<span data-ttu-id="56f1e-319">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-319">**New behavior**</span></span>

<span data-ttu-id="56f1e-320">A partir de EF Core 3.0, se crean distintas instancias de la entidad si se encuentra una entidad con un tipo e identificador determinados en varias ubicaciones del gráfico devuelto.</span><span class="sxs-lookup"><span data-stu-id="56f1e-320">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="56f1e-321">Por ejemplo, la consulta anterior ahora devolverá una nueva instancia de `Category` para cada elemento `Product` cuando haya dos productos asociados a la misma categoría.</span><span class="sxs-lookup"><span data-stu-id="56f1e-321">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

<span data-ttu-id="56f1e-322">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-322">**Why**</span></span>

<span data-ttu-id="56f1e-323">La resolución de las identidades (es decir, el hecho de determinar que una entidad tiene los mismos tipo e identificador que la entidad encontrada) agrega más rendimiento y sobrecarga de memoria.</span><span class="sxs-lookup"><span data-stu-id="56f1e-323">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="56f1e-324">Este enfoque suele ser contrario a por qué las consultas sin seguimiento se usan en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="56f1e-324">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="56f1e-325">Además, aunque la resolución de las identidades a veces puede resultar útil, no es necesaria si las entidades se van a serializar y enviar a un cliente, algo habitual para las consultas sin seguimiento.</span><span class="sxs-lookup"><span data-stu-id="56f1e-325">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

<span data-ttu-id="56f1e-326">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-326">**Mitigations**</span></span>

<span data-ttu-id="56f1e-327">Si se requiere la resolución de identidad, use una consulta de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="56f1e-327">Use a tracking query if identity resolution is required.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="56f1e-328">~~La ejecución de consultas se registra en el nivel de depuración~~ Revertido</span><span class="sxs-lookup"><span data-stu-id="56f1e-328">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="56f1e-329">Problema de seguimiento n.º 14523</span><span class="sxs-lookup"><span data-stu-id="56f1e-329">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="56f1e-330">Revertimos este cambio porque la nueva configuración de EF Core 3.0 permite a la aplicación especificar el nivel de registro para cualquier evento.</span><span class="sxs-lookup"><span data-stu-id="56f1e-330">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="56f1e-331">Por ejemplo, para cambiar el registro de SQL a `Debug`, configure el nivel de forma explícita en `OnConfiguring` o `AddDbContext`:</span><span class="sxs-lookup"><span data-stu-id="56f1e-331">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="56f1e-332">Los valores de clave temporal ya no se establecen en instancias de entidad</span><span class="sxs-lookup"><span data-stu-id="56f1e-332">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="56f1e-333">Problema de seguimiento n.º 12378</span><span class="sxs-lookup"><span data-stu-id="56f1e-333">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="56f1e-334">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-334">**Old behavior**</span></span>

<span data-ttu-id="56f1e-335">Antes de EF Core 3.0, los valores temporales se asignaban a todas las propiedades de clave para las que posteriormente la base de datos generaba un valor real.</span><span class="sxs-lookup"><span data-stu-id="56f1e-335">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="56f1e-336">Normalmente, estos valores temporales eran números negativos grandes.</span><span class="sxs-lookup"><span data-stu-id="56f1e-336">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="56f1e-337">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-337">**New behavior**</span></span>

<span data-ttu-id="56f1e-338">A partir de la versión 3.0, en EF Core se almacena el valor de clave temporal como parte de la información de seguimiento de la entidad y la propiedad clave en sí no se modifica.</span><span class="sxs-lookup"><span data-stu-id="56f1e-338">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="56f1e-339">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-339">**Why**</span></span>

<span data-ttu-id="56f1e-340">Este cambio se ha realizado para evitar que los valores de clave temporales se conviertan erróneamente en permanentes cuando una entidad de la que previamente una instancia de `DbContext` ha realizado el seguimiento se mueve a otra instancia de `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-340">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="56f1e-341">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-341">**Mitigations**</span></span>

<span data-ttu-id="56f1e-342">Las aplicaciones que asignan valores de clave principal a claves externas para crear asociaciones entre entidades pueden depender del comportamiento anterior si las claves principales son generadas por el almacén y pertenecen a entidades en el estado `Added`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-342">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="56f1e-343">Esto se puede evitar con las siguientes situaciones:</span><span class="sxs-lookup"><span data-stu-id="56f1e-343">This can be avoided by:</span></span>
* <span data-ttu-id="56f1e-344">No se usan claves generadas por el almacén.</span><span class="sxs-lookup"><span data-stu-id="56f1e-344">Not using store-generated keys.</span></span>
* <span data-ttu-id="56f1e-345">Se establecen propiedades de navegación para crear relaciones en lugar de establecer valores de clave externa.</span><span class="sxs-lookup"><span data-stu-id="56f1e-345">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="56f1e-346">Se obtienen los valores de clave temporal reales de la información de seguimiento de la entidad.</span><span class="sxs-lookup"><span data-stu-id="56f1e-346">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="56f1e-347">Por ejemplo, `context.Entry(blog).Property(e => e.Id).CurrentValue` devolverá el valor temporal aunque no se haya establecido `blog.Id`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-347">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="56f1e-348">DetectChanges respeta los valores de clave generados por el almacén</span><span class="sxs-lookup"><span data-stu-id="56f1e-348">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="56f1e-349">Problema de seguimiento n.º 14616</span><span class="sxs-lookup"><span data-stu-id="56f1e-349">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="56f1e-350">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-350">**Old behavior**</span></span>

<span data-ttu-id="56f1e-351">Antes de EF Core 3.0, se realizaba el seguimiento en el estado `Added` de las entidades sin seguimiento detectadas por `DetectChanges` y se insertaban como una fila nueva cuando se llamaba a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-351">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="56f1e-352">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-352">**New behavior**</span></span>

<span data-ttu-id="56f1e-353">A partir de EF Core 3.0, si una entidad usa valores de clave generados y se establece un valor de clave, se realizará el seguimiento de la entidad en el estado `Modified`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-353">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="56f1e-354">Esto significa que se supone que existe una fila para la entidad y que se actualizará cuando se llame a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-354">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="56f1e-355">Si no se establece el valor de clave, o bien si el tipo de entidad no usa claves generadas, se seguirá realizando el seguimiento de la entidad nueva como `Added` al igual que en las versiones anteriores.</span><span class="sxs-lookup"><span data-stu-id="56f1e-355">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="56f1e-356">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-356">**Why**</span></span>

<span data-ttu-id="56f1e-357">Este cambio se ha realizado para que sea más sencillo y coherente trabajar con gráficos de entidades desconectadas mientras se usan claves generadas por el almacén.</span><span class="sxs-lookup"><span data-stu-id="56f1e-357">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="56f1e-358">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-358">**Mitigations**</span></span>

<span data-ttu-id="56f1e-359">Este cambio puede interrumpir una aplicación si se configura un tipo de entidad para usar claves generadas, pero se establecen de forma explícita valores de clave para las instancias nuevas.</span><span class="sxs-lookup"><span data-stu-id="56f1e-359">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="56f1e-360">La solución consiste en configurar de forma explícita las propiedades de clave para que no usen valores generados.</span><span class="sxs-lookup"><span data-stu-id="56f1e-360">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="56f1e-361">Por ejemplo, con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="56f1e-361">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="56f1e-362">O bien con anotaciones de datos:</span><span class="sxs-lookup"><span data-stu-id="56f1e-362">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="56f1e-363">Las eliminaciones en cascada ahora se realizan inmediatamente de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="56f1e-363">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="56f1e-364">Problema de seguimiento n.º 10114</span><span class="sxs-lookup"><span data-stu-id="56f1e-364">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="56f1e-365">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-365">**Old behavior**</span></span>

<span data-ttu-id="56f1e-366">Antes de la versión 3.0, en EF Core no se aplicaban acciones en cascada (eliminación de entidades dependientes cuando se eliminaba una entidad de seguridad obligatoria o cuando se rompía la relación con una entidad de seguridad obligatoria) hasta que se llamaba a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="56f1e-366">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="56f1e-367">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-367">**New behavior**</span></span>

<span data-ttu-id="56f1e-368">A partir de 3.0, en EF Core las acciones en cascada se aplican en cuanto se detecta la condición desencadenadora.</span><span class="sxs-lookup"><span data-stu-id="56f1e-368">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="56f1e-369">Por ejemplo, como resultado de la llamada a `context.Remove()` para eliminar una entidad de seguridad, todos los dependientes obligatorios relacionados de los que se realiza el seguimiento también se establecen en `Deleted` inmediatamente.</span><span class="sxs-lookup"><span data-stu-id="56f1e-369">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="56f1e-370">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-370">**Why**</span></span>

<span data-ttu-id="56f1e-371">Este cambio se ha realizado para mejorar la experiencia en escenarios de auditoría y enlace de datos, donde es importante comprender qué entidades se van a eliminar _antes_ de llamar a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-371">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="56f1e-372">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-372">**Mitigations**</span></span>

<span data-ttu-id="56f1e-373">El comportamiento anterior se puede restaurar mediante opciones de `context.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-373">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="56f1e-374">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-374">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="56f1e-375">La carga diligente de entidades relacionadas ahora se realiza en una sola consulta</span><span class="sxs-lookup"><span data-stu-id="56f1e-375">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="56f1e-376">Problema de seguimiento n.º 18022</span><span class="sxs-lookup"><span data-stu-id="56f1e-376">Tracking issue #18022</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

<span data-ttu-id="56f1e-377">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-377">**Old behavior**</span></span>

<span data-ttu-id="56f1e-378">Antes de la versión 3.0, la carga diligente de navegaciones de colección a través de operadores `Include` provocaba la generación de varias consultas en la base de datos relacional, una para cada tipo de entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="56f1e-378">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

<span data-ttu-id="56f1e-379">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-379">**New behavior**</span></span>

<span data-ttu-id="56f1e-380">A partir de la versión 3.0, EF Core genera una sola consulta con operadores JOIN en las bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="56f1e-380">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

<span data-ttu-id="56f1e-381">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-381">**Why**</span></span>

<span data-ttu-id="56f1e-382">La emisión de varias consultas para implementar una única consulta LINQ provocaba numerosos problemas, incluido el rendimiento negativo, ya que se necesitaban varios recorridos de ida y vuelta a la base de datos, y problemas de coherencia de datos, ya que cada consulta podía observar un estado distinto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="56f1e-382">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

<span data-ttu-id="56f1e-383">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-383">**Mitigations**</span></span>

<span data-ttu-id="56f1e-384">Aunque técnicamente esto no es un cambio importante, podría tener un efecto considerable en el rendimiento de la aplicación cuando una sola consulta contiene un gran número de operadores `Include` en las navegaciones de la colección.</span><span class="sxs-lookup"><span data-stu-id="56f1e-384">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="56f1e-385">[Vea este comentario](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) para obtener más información y para volver a escribir las consultas de una manera más eficaz.</span><span class="sxs-lookup"><span data-stu-id="56f1e-385">[See this comment](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="56f1e-386">DeleteBehavior.Restrict tiene una semántica más limpia</span><span class="sxs-lookup"><span data-stu-id="56f1e-386">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="56f1e-387">Problema de seguimiento n.º 12661</span><span class="sxs-lookup"><span data-stu-id="56f1e-387">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="56f1e-388">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-388">**Old behavior**</span></span>

<span data-ttu-id="56f1e-389">Antes de la versión 3.0, `DeleteBehavior.Restrict` creaba claves externas en la base de datos con la semántica `Restrict`, pero también realizaba una corrección interna de manera no evidente.</span><span class="sxs-lookup"><span data-stu-id="56f1e-389">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="56f1e-390">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-390">**New behavior**</span></span>

<span data-ttu-id="56f1e-391">A partir de la versión 3.0, `DeleteBehavior.Restrict` garantiza que las claves externas se crean con la semántica `Restrict`, es decir, sin cascadas y realizando una infracción de restricción, sin llevar a cabo correcciones internas de EF.</span><span class="sxs-lookup"><span data-stu-id="56f1e-391">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="56f1e-392">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-392">**Why**</span></span>

<span data-ttu-id="56f1e-393">Este cambio se realizó para mejorar la experiencia de uso de `DeleteBehavior` de manera intuitiva sin efectos secundarios inesperados.</span><span class="sxs-lookup"><span data-stu-id="56f1e-393">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="56f1e-394">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-394">**Mitigations**</span></span>

<span data-ttu-id="56f1e-395">El comportamiento anterior se puede restaurar con `DeleteBehavior.ClientNoAction`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-395">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="56f1e-396">Los tipos de consulta se consolidan con tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="56f1e-396">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="56f1e-397">Problema de seguimiento n.º 14194</span><span class="sxs-lookup"><span data-stu-id="56f1e-397">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="56f1e-398">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-398">**Old behavior**</span></span>

<span data-ttu-id="56f1e-399">Antes de EF Core 3.0, los [tipos de consulta](xref:core/modeling/keyless-entity-types) eran un medio para consultar los datos que no definen una clave principal de una manera estructurada.</span><span class="sxs-lookup"><span data-stu-id="56f1e-399">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="56f1e-400">Es decir, un tipo de consulta se usaba para asignar tipos de entidad sin claves (más probablemente desde una vista, pero posiblemente desde una tabla), mientras que un tipo de entidad estándar se usaba cuando había una clave disponible (más probablemente desde una tabla, pero posiblemente desde una vista).</span><span class="sxs-lookup"><span data-stu-id="56f1e-400">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="56f1e-401">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-401">**New behavior**</span></span>

<span data-ttu-id="56f1e-402">Ahora un tipo de consulta se convierte en un tipo de entidad sin una clave principal.</span><span class="sxs-lookup"><span data-stu-id="56f1e-402">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="56f1e-403">Los tipos de entidad sin clave tienen la misma funcionalidad que los tipos de consulta de las versiones anteriores.</span><span class="sxs-lookup"><span data-stu-id="56f1e-403">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="56f1e-404">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-404">**Why**</span></span>

<span data-ttu-id="56f1e-405">Este cambio se ha realizado para reducir la confusión en torno a la finalidad de los tipos de consulta.</span><span class="sxs-lookup"><span data-stu-id="56f1e-405">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="56f1e-406">En concreto, son tipos de entidad sin clave y, por ello, intrínsecamente son de solo lectura, pero no se deben usar solo porque un tipo de entidad tenga que ser de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="56f1e-406">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="56f1e-407">Del mismo modo, se suelen asignar a vistas, pero solo porque las vistas no suelen definir claves.</span><span class="sxs-lookup"><span data-stu-id="56f1e-407">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="56f1e-408">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-408">**Mitigations**</span></span>

<span data-ttu-id="56f1e-409">Los elementos siguientes de la API ahora están obsoletos:</span><span class="sxs-lookup"><span data-stu-id="56f1e-409">The following parts of the API are now obsolete:</span></span>
* <span data-ttu-id="56f1e-410">**`ModelBuilder.Query<>()`** : en su lugar es necesario llamar a `ModelBuilder.Entity<>().HasNoKey()` para marcar un tipo de entidad como sin claves.</span><span class="sxs-lookup"><span data-stu-id="56f1e-410">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="56f1e-411">Esto todavía no se configurará por convención para evitar una configuración incorrecta cuando se espera una clave principal, pero no coincide con la convención.</span><span class="sxs-lookup"><span data-stu-id="56f1e-411">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="56f1e-412">**`DbQuery<>`** : en su lugar se debe usar `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-412">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="56f1e-413">**`DbContext.Query<>()`** : en su lugar se debe usar `DbContext.Set<>()`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-413">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="56f1e-414">La API de configuración para las relaciones de tipo de propiedad ha cambiado</span><span class="sxs-lookup"><span data-stu-id="56f1e-414">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="56f1e-415">[Problema de seguimiento n.º 12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Problema de seguimiento n.º 9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Problema de seguimiento n.º 14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="56f1e-415">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="56f1e-416">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-416">**Old behavior**</span></span>

<span data-ttu-id="56f1e-417">Antes de EF Core 3.0, la configuración de la relación de propiedad se realizaba directamente después de la llamada a `OwnsOne` o `OwnsMany`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-417">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="56f1e-418">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-418">**New behavior**</span></span>

<span data-ttu-id="56f1e-419">A partir de EF Core 3.0, ahora hay una API fluida para configurar una propiedad de navegación para el propietario mediante `WithOwner()`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-419">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="56f1e-420">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-420">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="56f1e-421">La configuración relacionada con la relación entre el propietario y lo que se posee ahora se debe encadenar después de `WithOwner()`, de forma similar a cómo se configuran otras relaciones.</span><span class="sxs-lookup"><span data-stu-id="56f1e-421">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="56f1e-422">Pero la configuración del propio tipo de propiedad se seguirá encadenando después de `OwnsOne()/OwnsMany()`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-422">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="56f1e-423">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-423">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details, eb =>
    {
        eb.WithOwner()
            .HasForeignKey(e => e.AlternateId)
            .HasConstraintName("FK_OrderDetails");
            
        eb.ToTable("OrderDetails");
        eb.HasKey(e => e.AlternateId);
        eb.HasIndex(e => e.Id);

        eb.HasOne(e => e.Customer).WithOne();

        eb.HasData(
            new OrderDetails
            {
                AlternateId = 1,
                Id = -1
            });
    });
```

<span data-ttu-id="56f1e-424">Además, la llamada a `Entity()`, `HasOne()` o `Set()` con un tipo de propiedad de destino ahora iniciará una excepción.</span><span class="sxs-lookup"><span data-stu-id="56f1e-424">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="56f1e-425">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-425">**Why**</span></span>

<span data-ttu-id="56f1e-426">Este cambio se ha realizado para crear una separación más clara entre la configuración del propio tipo de propiedad y la _relación con_ el tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="56f1e-426">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="56f1e-427">A su vez, esto elimina la ambigüedad y la confusión de métodos como `HasForeignKey`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-427">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="56f1e-428">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-428">**Mitigations**</span></span>

<span data-ttu-id="56f1e-429">Cambie la configuración de las relaciones de tipo de propiedad para usar la nueva superficie de API, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="56f1e-429">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="56f1e-430">Ahora, las entidades dependientes que comparten la tabla con la entidad de seguridad son opcionales</span><span class="sxs-lookup"><span data-stu-id="56f1e-430">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="56f1e-431">Problema de seguimiento n.º 9005</span><span class="sxs-lookup"><span data-stu-id="56f1e-431">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="56f1e-432">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-432">**Old behavior**</span></span>

<span data-ttu-id="56f1e-433">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="56f1e-433">Consider the following model:</span></span>
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```
<span data-ttu-id="56f1e-434">Antes de EF Core 3.0, si `OrderDetails` era propiedad de `Order` o estaba asignado explícitamente a la misma tabla, siempre era necesaria una instancia de `OrderDetails` al agregar un elemento `Order` nuevo.</span><span class="sxs-lookup"><span data-stu-id="56f1e-434">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="56f1e-435">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-435">**New behavior**</span></span>

<span data-ttu-id="56f1e-436">A partir de la versión 3.0, EF Core permite agregar `Order` sin `OrderDetails` y asigna todas las propiedades `OrderDetails` a excepción de la clave principal a columnas que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="56f1e-436">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="56f1e-437">Al realizar consultas, EF Core establece `OrderDetails` en `null` si ninguna de las propiedades necesarias tiene un valor o si no tiene propiedades necesarias más allá de la clave principal y todas las propiedades son `null`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-437">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="56f1e-438">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-438">**Mitigations**</span></span>

<span data-ttu-id="56f1e-439">Si el modelo tiene una tabla que comparte dependencias con todas las columnas opcionales, pero la navegación que apunta a ella no se espera que sea `null`, la aplicación debería modificarse para controlar los casos en los que la navegación sea `null`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-439">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="56f1e-440">Si esto no es posible, debería agregarse una propiedad necesaria al tipo de entidad o, al menos, una entidad debería tener un valor distinto a `null` asignado.</span><span class="sxs-lookup"><span data-stu-id="56f1e-440">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="56f1e-441">Todas las entidades que compartan una tabla con una columna de token de simultaneidad tienen que asignarla a una propiedad</span><span class="sxs-lookup"><span data-stu-id="56f1e-441">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="56f1e-442">Problema de seguimiento n.º 14154</span><span class="sxs-lookup"><span data-stu-id="56f1e-442">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="56f1e-443">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-443">**Old behavior**</span></span>

<span data-ttu-id="56f1e-444">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="56f1e-444">Consider the following model:</span></span>
```csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public byte[] Version { get; set; }
    public OrderDetails Details { get; set; }
}

public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Order>()
        .Property(o => o.Version).IsRowVersion().HasColumnName("Version");
}
```
<span data-ttu-id="56f1e-445">Antes de EF Core 3.0, si `OrderDetails` era propiedad de `Order` o estaba asignado explícitamente a la misma tabla, si solo se actualizaba `OrderDetails`, no se actualizaba el valor `Version` en el cliente y se producía un error en la próxima actualización.</span><span class="sxs-lookup"><span data-stu-id="56f1e-445">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="56f1e-446">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-446">**New behavior**</span></span>

<span data-ttu-id="56f1e-447">A partir de la versión 3.0, EF Core propaga el nuevo valor `Version` en `Order` si posee `OrderDetails`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-447">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="56f1e-448">En caso contrario, se produce una excepción durante la validación del modelo.</span><span class="sxs-lookup"><span data-stu-id="56f1e-448">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="56f1e-449">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-449">**Why**</span></span>

<span data-ttu-id="56f1e-450">Este cambio se realizó para evitar un valor de token de simultaneidad obsoleto cuando solo se actualiza una de las entidades asignadas a la misma tabla.</span><span class="sxs-lookup"><span data-stu-id="56f1e-450">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="56f1e-451">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-451">**Mitigations**</span></span>

<span data-ttu-id="56f1e-452">Todas las entidades que comparten la tabla deben incluir una propiedad que se asigna a la columna del token de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="56f1e-452">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="56f1e-453">Es posible crear una en estado reemplazado:</span><span class="sxs-lookup"><span data-stu-id="56f1e-453">It's possible the create one in shadow-state:</span></span>
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="56f1e-454">Las entidades en propiedad no se pueden consultar sin el propietario mediante una consulta de seguimiento</span><span class="sxs-lookup"><span data-stu-id="56f1e-454">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="56f1e-455">Problema de seguimiento n.º 18876</span><span class="sxs-lookup"><span data-stu-id="56f1e-455">Tracking Issue #18876</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18876)

<span data-ttu-id="56f1e-456">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-456">**Old behavior**</span></span>

<span data-ttu-id="56f1e-457">Antes de EF Core 3.0, las entidades en propiedad se podían consultar como cualquier otra navegación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-457">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

<span data-ttu-id="56f1e-458">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-458">**New behavior**</span></span>

<span data-ttu-id="56f1e-459">A partir de la versión 3.0, EF Core iniciará una excepción si una consulta de seguimiento proyecta una entidad en propiedad sin el propietario.</span><span class="sxs-lookup"><span data-stu-id="56f1e-459">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

<span data-ttu-id="56f1e-460">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-460">**Why**</span></span>

<span data-ttu-id="56f1e-461">Las entidades en propiedad no se pueden manipular sin el propietario, por lo que en la mayoría de los casos es un error consultarlas de esta manera.</span><span class="sxs-lookup"><span data-stu-id="56f1e-461">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

<span data-ttu-id="56f1e-462">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-462">**Mitigations**</span></span>

<span data-ttu-id="56f1e-463">Si se debe realizar el seguimiento de la entidad en propiedad para modificarla de cualquier manera posterior, el propietario se debe incluir en la consulta.</span><span class="sxs-lookup"><span data-stu-id="56f1e-463">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="56f1e-464">De lo contrario, agregue una llamada a `AsNoTracking()`:</span><span class="sxs-lookup"><span data-stu-id="56f1e-464">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="56f1e-465">Ahora, las propiedades heredadas de tipos sin asignar se asignan a una única columna para todos los tipos derivados</span><span class="sxs-lookup"><span data-stu-id="56f1e-465">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="56f1e-466">Problema de seguimiento n.º 13998</span><span class="sxs-lookup"><span data-stu-id="56f1e-466">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="56f1e-467">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-467">**Old behavior**</span></span>

<span data-ttu-id="56f1e-468">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="56f1e-468">Consider the following model:</span></span>
```csharp
public abstract class EntityBase
{
    public int Id { get; set; }
}

public abstract class OrderBase : EntityBase
{
    public int ShippingAddress { get; set; }
}

public class BulkOrder : OrderBase
{
}

public class Order : OrderBase
{
}

protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>();
    modelBuilder.Entity<Order>();
}
```

<span data-ttu-id="56f1e-469">Antes de EF Core 3.0, la propiedad `ShippingAddress` se asignaba a columnas distintas para `BulkOrder` y `Order` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="56f1e-469">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="56f1e-470">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-470">**New behavior**</span></span>

<span data-ttu-id="56f1e-471">A partir de la versión3.0, EF Core solo crea una columna para `ShippingAddress`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-471">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="56f1e-472">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-472">**Why**</span></span>

<span data-ttu-id="56f1e-473">El comportamiento anterior no era el esperado.</span><span class="sxs-lookup"><span data-stu-id="56f1e-473">The old behavoir was unexpected.</span></span>

<span data-ttu-id="56f1e-474">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-474">**Mitigations**</span></span>

<span data-ttu-id="56f1e-475">Todavía se puede asignar explícitamente la propiedad a columnas separadas en los tipos derivados:</span><span class="sxs-lookup"><span data-stu-id="56f1e-475">The property can still be explicitly mapped to separate column on the derived types:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Ignore<OrderBase>();
    modelBuilder.Entity<EntityBase>();
    modelBuilder.Entity<BulkOrder>()
        .Property(o => o.ShippingAddress).HasColumnName("BulkShippingAddress");
    modelBuilder.Entity<Order>()
        .Property(o => o.ShippingAddress).HasColumnName("ShippingAddress");
}
```

<a name="fkp"></a>

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="56f1e-476">La convención de propiedad de clave externa ya no coincide con el mismo nombre que la propiedad de entidad de seguridad</span><span class="sxs-lookup"><span data-stu-id="56f1e-476">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="56f1e-477">Problema de seguimiento n.º 13274</span><span class="sxs-lookup"><span data-stu-id="56f1e-477">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="56f1e-478">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-478">**Old behavior**</span></span>

<span data-ttu-id="56f1e-479">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="56f1e-479">Consider the following model:</span></span>
```csharp
public class Customer
{
    public int CustomerId { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```
<span data-ttu-id="56f1e-480">Antes de EF Core 3.0, se podía usar la propiedad `CustomerId` para la clave externa por convención.</span><span class="sxs-lookup"><span data-stu-id="56f1e-480">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="56f1e-481">Pero si `Order` es un tipo de propiedad, entonces esto convertiría también a `CustomerId` en la clave principal, algo que no suele ser lo esperado.</span><span class="sxs-lookup"><span data-stu-id="56f1e-481">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="56f1e-482">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-482">**New behavior**</span></span>

<span data-ttu-id="56f1e-483">A partir de la versión 3.0, EF Core no intenta usar las propiedades de claves externas por convención si tienen el mismo nombre que la propiedad de entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="56f1e-483">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="56f1e-484">Los patrones de nombre de tipo de entidad de seguridad concatenado con el nombre de propiedad de la entidad de seguridad y de nombre de navegación concatenado con el nombre de propiedad de la entidad de seguridad todavía se hacen coincidir.</span><span class="sxs-lookup"><span data-stu-id="56f1e-484">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="56f1e-485">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-485">For example:</span></span>

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
}
```

```csharp
public class Customer
{
    public int Id { get; set; }
    public ICollection<Order> Orders { get; set; }
}

public class Order
{
    public int Id { get; set; }
    public int BuyerId { get; set; }
    public Customer Buyer { get; set; }
}
```

<span data-ttu-id="56f1e-486">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-486">**Why**</span></span>

<span data-ttu-id="56f1e-487">Este cambio se ha realizado para evitar definir erróneamente una propiedad de clave principal en el tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="56f1e-487">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="56f1e-488">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-488">**Mitigations**</span></span>

<span data-ttu-id="56f1e-489">Si la propiedad se ha diseñado para ser la clave externa y, por tanto, parte de la clave principal, se debe configurar explícitamente como tal.</span><span class="sxs-lookup"><span data-stu-id="56f1e-489">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="56f1e-490">Ahora, la conexión de base de datos se cierra si ya no se usa antes de que se complete TransactionScope</span><span class="sxs-lookup"><span data-stu-id="56f1e-490">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="56f1e-491">Problema de seguimiento n.º 14218</span><span class="sxs-lookup"><span data-stu-id="56f1e-491">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="56f1e-492">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-492">**Old behavior**</span></span>

<span data-ttu-id="56f1e-493">Antes de EF Core 3.0, si el contexto abría la conexión dentro de `TransactionScope`, la conexión permanecía abierta mientras el ámbito actual `TransactionScope` estuviese activo.</span><span class="sxs-lookup"><span data-stu-id="56f1e-493">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        // Old behavior: Connection is still open at this point

        var categories = context.ProductCategories().ToList();
    }
}
```

<span data-ttu-id="56f1e-494">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-494">**New behavior**</span></span>

<span data-ttu-id="56f1e-495">A partir de la versión 3.0, EF Core cierra la conexión en cuanto se deja de usar.</span><span class="sxs-lookup"><span data-stu-id="56f1e-495">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="56f1e-496">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-496">**Why**</span></span>

<span data-ttu-id="56f1e-497">Este cambio permite usar varios contextos en el mismo ámbito `TransactionScope`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-497">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="56f1e-498">El comportamiento nuevo también coincide con el de EF6.</span><span class="sxs-lookup"><span data-stu-id="56f1e-498">The new behavior also matches EF6.</span></span>

<span data-ttu-id="56f1e-499">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-499">**Mitigations**</span></span>

<span data-ttu-id="56f1e-500">Si la conexión debe permanecer abierta, una llamada explícita a `OpenConnection()` asegurará que EF Core no la cierre de forma prematura:</span><span class="sxs-lookup"><span data-stu-id="56f1e-500">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

```csharp
using (new TransactionScope())
{
    using (AdventureWorks context = new AdventureWorks())
    {
        context.Database.OpenConnection();
        context.ProductCategories.Add(new ProductCategory());
        context.SaveChanges();

        var categories = context.ProductCategories().ToList();
        context.Database.CloseConnection();
    }
}
```

<a name="each"></a>

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="56f1e-501">Cada propiedad usa la generación de claves enteras en memoria independiente</span><span class="sxs-lookup"><span data-stu-id="56f1e-501">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="56f1e-502">Problema de seguimiento n.º 6872</span><span class="sxs-lookup"><span data-stu-id="56f1e-502">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="56f1e-503">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-503">**Old behavior**</span></span>

<span data-ttu-id="56f1e-504">Antes de EF Core 3.0, se usaba un generador de valores compartidos para todas las propiedades de clave entera en memoria.</span><span class="sxs-lookup"><span data-stu-id="56f1e-504">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="56f1e-505">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-505">**New behavior**</span></span>

<span data-ttu-id="56f1e-506">A partir de EF Core 3.0, cada propiedad de clave entera obtiene su propio generador de valores cuando se usa la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="56f1e-506">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="56f1e-507">Además, si se elimina la base de datos, la generación de claves se restablece para todas las tablas.</span><span class="sxs-lookup"><span data-stu-id="56f1e-507">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="56f1e-508">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-508">**Why**</span></span>

<span data-ttu-id="56f1e-509">Este cambio se ha realizado para alinear la generación de claves en memoria más estrechamente a la generación de claves de base de datos reales y para mejorar la capacidad para aislar las pruebas entre sí cuando se usa la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="56f1e-509">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="56f1e-510">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-510">**Mitigations**</span></span>

<span data-ttu-id="56f1e-511">Esto puede interrumpir una aplicación que se base en el establecimiento de valores de clave específicos en memoria.</span><span class="sxs-lookup"><span data-stu-id="56f1e-511">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="56f1e-512">En su lugar, considere la posibilidad de no depender de valores de clave específicos, o bien de actualizar para que coincida con el comportamiento nuevo.</span><span class="sxs-lookup"><span data-stu-id="56f1e-512">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="56f1e-513">Los campos de respaldo se usan de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="56f1e-513">Backing fields are used by default</span></span>

[<span data-ttu-id="56f1e-514">Problema de seguimiento n.º 12430</span><span class="sxs-lookup"><span data-stu-id="56f1e-514">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="56f1e-515">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-515">**Old behavior**</span></span>

<span data-ttu-id="56f1e-516">Antes de la versión 3.0, incluso si se conocía el campo de respaldo de una propiedad, de forma predeterminada en EF Core se leía y escribía el valor de propiedad mediante los métodos captadores y establecedores de propiedades.</span><span class="sxs-lookup"><span data-stu-id="56f1e-516">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="56f1e-517">La excepción era la ejecución de consultas, donde el campo de respaldo se establecía directamente si se conocía.</span><span class="sxs-lookup"><span data-stu-id="56f1e-517">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="56f1e-518">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-518">**New behavior**</span></span>

<span data-ttu-id="56f1e-519">A partir de EF Core 3.0, si se conoce el campo de respaldo para una propiedad, EF Core siempre la leerá y escribirá mediante el campo de respaldo.</span><span class="sxs-lookup"><span data-stu-id="56f1e-519">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="56f1e-520">Esto podría provocar una interrupción de la aplicación si depende de un comportamiento adicional codificado en los métodos captadores o establecedores.</span><span class="sxs-lookup"><span data-stu-id="56f1e-520">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="56f1e-521">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-521">**Why**</span></span>

<span data-ttu-id="56f1e-522">Este cambio se ha realizado para evitar que EF Core desencadene erróneamente lógica de negocios de forma predeterminada al realizar operaciones de base de datos que implican entidades.</span><span class="sxs-lookup"><span data-stu-id="56f1e-522">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="56f1e-523">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-523">**Mitigations**</span></span>

<span data-ttu-id="56f1e-524">El comportamiento anterior a la versión 3.0 se puede restaurar mediante la configuración del modo de acceso de propiedad en `ModelBuilder`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-524">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="56f1e-525">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-525">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="56f1e-526">Inicio de excepciones si se encuentran varios campos de respaldo compatibles</span><span class="sxs-lookup"><span data-stu-id="56f1e-526">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="56f1e-527">Problema de seguimiento n.º 12523</span><span class="sxs-lookup"><span data-stu-id="56f1e-527">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="56f1e-528">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-528">**Old behavior**</span></span>

<span data-ttu-id="56f1e-529">Antes de EF Core 3.0, si varios campos coincidían con las reglas para buscar el campo de respaldo de una propiedad, se elegía un campo según un orden de prioridad.</span><span class="sxs-lookup"><span data-stu-id="56f1e-529">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="56f1e-530">Esto podía producir que, en caso de ambigüedad, se usara el campo incorrecto.</span><span class="sxs-lookup"><span data-stu-id="56f1e-530">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="56f1e-531">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-531">**New behavior**</span></span>

<span data-ttu-id="56f1e-532">A partir de EF Core 3.0, si varios campos coinciden con la misma propiedad, se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="56f1e-532">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="56f1e-533">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-533">**Why**</span></span>

<span data-ttu-id="56f1e-534">Este cambio se ha realizado para evitar de forma silenciosa el uso de un campo con respecto a otro cuando solo uno puede ser correcto.</span><span class="sxs-lookup"><span data-stu-id="56f1e-534">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="56f1e-535">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-535">**Mitigations**</span></span>

<span data-ttu-id="56f1e-536">En las propiedades con campos de respaldo ambiguos se debe especificar de forma explícita el campo que se va usar.</span><span class="sxs-lookup"><span data-stu-id="56f1e-536">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="56f1e-537">Por ejemplo, con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="56f1e-537">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="56f1e-538">Los nombres de propiedades de solo campo deben coincidir con el nombre del campo</span><span class="sxs-lookup"><span data-stu-id="56f1e-538">Field-only property names should match the field name</span></span>

<span data-ttu-id="56f1e-539">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-539">**Old behavior**</span></span>

<span data-ttu-id="56f1e-540">Antes de EF Core 3.0, una propiedad podía especificarse con un valor de cadena y, si no había ninguna propiedad con ese nombre en el tipo .NET, EF Core intentaba hacerla coincidir con un campo mediante reglas de convención.</span><span class="sxs-lookup"><span data-stu-id="56f1e-540">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

```csharp
private class Blog
{
    private int _id;
    public string Name { get; set; }
}
```

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id");
```

<span data-ttu-id="56f1e-541">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-541">**New behavior**</span></span>

<span data-ttu-id="56f1e-542">A partir de EF Core 3.0, una propiedad de solo campo debe coincidir exactamente con el nombre del campo.</span><span class="sxs-lookup"><span data-stu-id="56f1e-542">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="56f1e-543">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-543">**Why**</span></span>

<span data-ttu-id="56f1e-544">Este cambio se realizó para evitar el uso del mismo campo para dos propiedades con nombres similares. También hace que las reglas de coincidencia para propiedades solo de campo sean las mismas que para las propiedades asignadas a propiedades CLR.</span><span class="sxs-lookup"><span data-stu-id="56f1e-544">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="56f1e-545">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-545">**Mitigations**</span></span>

<span data-ttu-id="56f1e-546">Las propiedades solo de campo deberían tener el mismo nombre que el campo al que están asignadas.</span><span class="sxs-lookup"><span data-stu-id="56f1e-546">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="56f1e-547">En una próxima versión de EF Core 3.0 tenemos planeado volver a habilitar la configuración explícita de un nombre de campo distinto al nombre de la propiedad (vea el problema [n.° 15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span><span class="sxs-lookup"><span data-stu-id="56f1e-547">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="56f1e-548">AddDbContext/AddDbContextPool ya no llaman a AddLogging ni a AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="56f1e-548">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="56f1e-549">Problema de seguimiento n.° 14756</span><span class="sxs-lookup"><span data-stu-id="56f1e-549">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="56f1e-550">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-550">**Old behavior**</span></span>

<span data-ttu-id="56f1e-551">Antes de EF Core 3.0, la llamada a `AddDbContext` o `AddDbContextPool` también podría registrar los servicios de almacenamiento en caché y de registro con inserción de dependencias a través de llamadas a [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) y [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="56f1e-551">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="56f1e-552">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-552">**New behavior**</span></span>

<span data-ttu-id="56f1e-553">A partir de EF Core 3.0, `AddDbContext` y `AddDbContextPool` ya no registrarán estos servicios con inserción de dependencias (DI).</span><span class="sxs-lookup"><span data-stu-id="56f1e-553">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="56f1e-554">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-554">**Why**</span></span>

<span data-ttu-id="56f1e-555">EF Core 3.0 no requiere que estos servicios estén en el contenedor de inserción de dependencias de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-555">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="56f1e-556">Pero si `ILoggerFactory` se registra en el contenedor de DI de la aplicación, EF Core lo empezará a usar de todos modos.</span><span class="sxs-lookup"><span data-stu-id="56f1e-556">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="56f1e-557">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-557">**Mitigations**</span></span>

<span data-ttu-id="56f1e-558">Si la aplicación necesita estos servicios, regístrelos de manera explícita con el contenedor de DI mediante [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) o [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="56f1e-558">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="56f1e-559">AddEntityFramework\* agrega IMemoryCache con un límite de tamaño</span><span class="sxs-lookup"><span data-stu-id="56f1e-559">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="56f1e-560">Problema de seguimiento n.º 12905</span><span class="sxs-lookup"><span data-stu-id="56f1e-560">Tracking Issue #12905</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12905)

<span data-ttu-id="56f1e-561">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-561">**Old behavior**</span></span>

<span data-ttu-id="56f1e-562">Antes de EF Core 3.0, la llamada a los métodos `AddEntityFramework*` también registraba los servicios de almacenamiento en caché de memoria con inserción de dependencias sin límite de tamaño.</span><span class="sxs-lookup"><span data-stu-id="56f1e-562">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

<span data-ttu-id="56f1e-563">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-563">**New behavior**</span></span>

<span data-ttu-id="56f1e-564">A partir de EF Core 3.0, `AddEntityFramework*` registrará un servicio IMemoryCache con un límite de tamaño.</span><span class="sxs-lookup"><span data-stu-id="56f1e-564">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="56f1e-565">Si otros servicios agregados después dependen de IMemoryCache, pueden alcanzar rápidamente el límite predeterminado y provocar excepciones o un rendimiento degradado.</span><span class="sxs-lookup"><span data-stu-id="56f1e-565">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

<span data-ttu-id="56f1e-566">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-566">**Why**</span></span>

<span data-ttu-id="56f1e-567">El uso de IMemoryCache sin un límite podría dar lugar a un uso de memoria no controlado si hay un error en la lógica de almacenamiento en caché de las consultas o las consultas se generan de forma dinámica.</span><span class="sxs-lookup"><span data-stu-id="56f1e-567">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="56f1e-568">Tener un límite predeterminado mitiga un posible ataque DoS.</span><span class="sxs-lookup"><span data-stu-id="56f1e-568">Having a default limit mitigates a potential DoS attack.</span></span>

<span data-ttu-id="56f1e-569">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-569">**Mitigations**</span></span>

<span data-ttu-id="56f1e-570">En la mayoría de los casos, no es necesario llamar a `AddEntityFramework*` si también se llama a `AddDbContext` o `AddDbContextPool`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-570">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="56f1e-571">Por tanto, la mejor mitigación consiste en quitar la llamada a `AddEntityFramework*`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-571">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="56f1e-572">Si la aplicación necesita estos servicios, registre de forma explícita una implementación de IMemoryCache con el contenedor de DI por anticipado mediante [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="56f1e-572">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](https://docs.microsoft.com/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="56f1e-573">Ahora DbContext.Entry realiza una operación DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="56f1e-573">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="56f1e-574">Problema de seguimiento n.º 13552</span><span class="sxs-lookup"><span data-stu-id="56f1e-574">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="56f1e-575">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-575">**Old behavior**</span></span>

<span data-ttu-id="56f1e-576">Antes de EF Core 3.0, la llamada a `DbContext.Entry` provocaba que se detectaran cambios para todas las entidades con seguimiento.</span><span class="sxs-lookup"><span data-stu-id="56f1e-576">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="56f1e-577">Esto garantizaba que el estado expuesto en `EntityEntry` estuviera actualizado.</span><span class="sxs-lookup"><span data-stu-id="56f1e-577">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="56f1e-578">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-578">**New behavior**</span></span>

<span data-ttu-id="56f1e-579">A partir de EF Core 3.0, ahora la llamada a `DbContext.Entry` solo intenta detectar cambios en la entidad dada y cualquier entidad de seguridad relacionada con ella de la que se haya realizado el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="56f1e-579">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="56f1e-580">Esto significa que es posible que la llamada a este método no haya detectado otros cambios, lo que podría tener implicaciones en el estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-580">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="56f1e-581">Observe que si `ChangeTracker.AutoDetectChangesEnabled` se establece en `false` incluso esta detección de cambios local se deshabilitará.</span><span class="sxs-lookup"><span data-stu-id="56f1e-581">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="56f1e-582">Otros métodos que provocan la detección de cambios (como `ChangeTracker.Entries` y `SaveChanges`) siguen provocando una acción `DetectChanges` completa de todas las entidades de las que se realiza el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="56f1e-582">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="56f1e-583">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-583">**Why**</span></span>

<span data-ttu-id="56f1e-584">Este cambio se ha realizado para mejorar el rendimiento predeterminado del uso de `context.Entry`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-584">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="56f1e-585">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-585">**Mitigations**</span></span>

<span data-ttu-id="56f1e-586">Llame a `ChangeTracker.DetectChanges()` de forma explícita antes de llamar a `Entry` para garantizar el comportamiento anterior a la versión 3.0.</span><span class="sxs-lookup"><span data-stu-id="56f1e-586">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="56f1e-587">El cliente no genera las claves de matriz de cadena y byte de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="56f1e-587">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="56f1e-588">Problema de seguimiento n.º 14617</span><span class="sxs-lookup"><span data-stu-id="56f1e-588">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="56f1e-589">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-589">**Old behavior**</span></span>

<span data-ttu-id="56f1e-590">Antes de EF Core 3.0, se podían usar las propiedades de clave `string` y `byte[]` sin tener que establecer de forma explícita un valor distinto de NULL.</span><span class="sxs-lookup"><span data-stu-id="56f1e-590">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="56f1e-591">En ese caso, el valor de clave se generaba en el cliente como un GUID, que se serializaba en bytes para `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-591">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="56f1e-592">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-592">**New behavior**</span></span>

<span data-ttu-id="56f1e-593">A partir de EF Core 3.0, se iniciará una excepción en la que indica que no se ha establecido ningún valor de clave.</span><span class="sxs-lookup"><span data-stu-id="56f1e-593">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="56f1e-594">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-594">**Why**</span></span>

<span data-ttu-id="56f1e-595">Este cambio se ha realizado porque los valores `string`/`byte[]` generados por el cliente no suelen ser útiles, y el comportamiento predeterminado dificultaba razonar sobre los valores de clave generados de una forma habitual.</span><span class="sxs-lookup"><span data-stu-id="56f1e-595">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="56f1e-596">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-596">**Mitigations**</span></span>

<span data-ttu-id="56f1e-597">Se puede obtener el comportamiento anterior a la versión 3.0 si se especifica de forma explícita que las propiedades de clave deben usar los valores generados si no se establece ningún otro valor distinto de NULL.</span><span class="sxs-lookup"><span data-stu-id="56f1e-597">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="56f1e-598">Por ejemplo, con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="56f1e-598">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="56f1e-599">O bien con anotaciones de datos:</span><span class="sxs-lookup"><span data-stu-id="56f1e-599">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="56f1e-600">Ahora ILoggerFactory es un servicio con ámbito</span><span class="sxs-lookup"><span data-stu-id="56f1e-600">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="56f1e-601">Problema de seguimiento n.º 14698</span><span class="sxs-lookup"><span data-stu-id="56f1e-601">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="56f1e-602">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-602">**Old behavior**</span></span>

<span data-ttu-id="56f1e-603">Antes de EF Core 3.0, `ILoggerFactory` se registraba como un servicio de singleton.</span><span class="sxs-lookup"><span data-stu-id="56f1e-603">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="56f1e-604">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-604">**New behavior**</span></span>

<span data-ttu-id="56f1e-605">A partir de EF Core 3.0, `ILoggerFactory` ahora se registra como con ámbito.</span><span class="sxs-lookup"><span data-stu-id="56f1e-605">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="56f1e-606">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-606">**Why**</span></span>

<span data-ttu-id="56f1e-607">Este cambio se ha realizado para permitir la asociación de un registrador con una instancia de `DbContext`, lo que habilita otras funciones y quita algunos casos de comportamiento patológico como un aumento vertiginoso de los proveedores de servicios internos.</span><span class="sxs-lookup"><span data-stu-id="56f1e-607">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="56f1e-608">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-608">**Mitigations**</span></span>

<span data-ttu-id="56f1e-609">Este cambio no debería afectar al código de la aplicación a menos que registre y use servicios personalizados en el proveedor de servicios internos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="56f1e-609">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="56f1e-610">Esto no es habitual.</span><span class="sxs-lookup"><span data-stu-id="56f1e-610">This isn't common.</span></span>
<span data-ttu-id="56f1e-611">En estos casos, la mayoría de los elementos seguirá funcionando, pero cualquier servicio de singleton que dependiera de `ILoggerFactory` tendrá que cambiarse para obtener la interfaz `ILoggerFactory` de otra forma.</span><span class="sxs-lookup"><span data-stu-id="56f1e-611">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="56f1e-612">Si experimenta situaciones como esta, registre un problema en el [rastreador de problemas de GitHub de EF Core](https://github.com/aspnet/EntityFrameworkCore/issues) para hacernos saber cómo usa `ILoggerFactory`, para que podamos comprender mejor cómo evitar esta interrupción en el futuro.</span><span class="sxs-lookup"><span data-stu-id="56f1e-612">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="56f1e-613">En los proxies de carga diferida ya no se supone que las propiedades de navegación están totalmente cargadas</span><span class="sxs-lookup"><span data-stu-id="56f1e-613">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="56f1e-614">Problema de seguimiento n.º 12780</span><span class="sxs-lookup"><span data-stu-id="56f1e-614">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="56f1e-615">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-615">**Old behavior**</span></span>

<span data-ttu-id="56f1e-616">Antes de EF Core 3.0, una vez que se eliminaba `DbContext` no había ninguna forma de saber si una determinada propiedad de navegación de una entidad obtenida de ese contexto se había cargado completamente o no.</span><span class="sxs-lookup"><span data-stu-id="56f1e-616">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="56f1e-617">En su lugar, los proxies asumían que una navegación de referencia se cargaba si tenía un valor distinto de NULL, y que una navegación de colección se cargaba si no estaba vacía.</span><span class="sxs-lookup"><span data-stu-id="56f1e-617">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="56f1e-618">En estos casos, el intento de carga diferida era no operativo.</span><span class="sxs-lookup"><span data-stu-id="56f1e-618">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="56f1e-619">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-619">**New behavior**</span></span>

<span data-ttu-id="56f1e-620">A partir de EF Core 3.0, los proxies realizan el seguimiento de si una propiedad de navegación se carga o no.</span><span class="sxs-lookup"><span data-stu-id="56f1e-620">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="56f1e-621">Esto significa que el intento de acceder a una propiedad de navegación que se carga después de que se haya eliminado el contexto siempre será no operativo, incluso cuando la navegación cargada está vacía o es NULL.</span><span class="sxs-lookup"><span data-stu-id="56f1e-621">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="56f1e-622">Por el contrario, el intento de acceder a una propiedad de navegación que no está cargada iniciará una excepción si el contexto se ha eliminado, incluso si la propiedad de navegación es una colección no vacía.</span><span class="sxs-lookup"><span data-stu-id="56f1e-622">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="56f1e-623">Si se produce esta situación, significa que el código de aplicación está intentando usar la carga diferida en un momento no válido y que se debe cambiar la aplicación para que lo no haga.</span><span class="sxs-lookup"><span data-stu-id="56f1e-623">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="56f1e-624">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-624">**Why**</span></span>

<span data-ttu-id="56f1e-625">Este cambio se ha realizado para que el comportamiento sea coherente y correcto cuando se intenta la carga diferida de una instancia de `DbContext` eliminada.</span><span class="sxs-lookup"><span data-stu-id="56f1e-625">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="56f1e-626">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-626">**Mitigations**</span></span>

<span data-ttu-id="56f1e-627">Actualice el código de la aplicación para que no intente la carga diferida con un contexto eliminado, o bien configúrelo para que sea no operativo, como se describe en el mensaje de la excepción.</span><span class="sxs-lookup"><span data-stu-id="56f1e-627">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="56f1e-628">La creación excesiva de proveedores de servicios internos ahora es un error de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="56f1e-628">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="56f1e-629">Problema de seguimiento n.º 10236</span><span class="sxs-lookup"><span data-stu-id="56f1e-629">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="56f1e-630">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-630">**Old behavior**</span></span>

<span data-ttu-id="56f1e-631">Antes de EF Core 3.0, se registraba una advertencia para una aplicación que creaba un número patológico de proveedores de servicios internos.</span><span class="sxs-lookup"><span data-stu-id="56f1e-631">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="56f1e-632">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-632">**New behavior**</span></span>

<span data-ttu-id="56f1e-633">A partir de EF Core 3.0, ahora esta advertencia se considera un error y se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="56f1e-633">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="56f1e-634">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-634">**Why**</span></span>

<span data-ttu-id="56f1e-635">Este cambio se ha realizado para controlar mejor el código de la aplicación mediante la exposición de este caso patológico de una forma más explícita.</span><span class="sxs-lookup"><span data-stu-id="56f1e-635">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="56f1e-636">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-636">**Mitigations**</span></span>

<span data-ttu-id="56f1e-637">Cuando se produce este error, la acción más adecuada consiste en comprender la causa raíz y detener la creación de tantos proveedores de servicios internos.</span><span class="sxs-lookup"><span data-stu-id="56f1e-637">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="56f1e-638">Pero el error se puede convertir en una advertencia (u omitirse) mediante configuración en `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-638">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="56f1e-639">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-639">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="56f1e-640">Comportamiento nuevo de HasOne/HasMany llamado con una sola cadena</span><span class="sxs-lookup"><span data-stu-id="56f1e-640">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="56f1e-641">Problema de seguimiento n.° 9171</span><span class="sxs-lookup"><span data-stu-id="56f1e-641">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="56f1e-642">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-642">**Old behavior**</span></span>

<span data-ttu-id="56f1e-643">Antes de EF Core 3.0, el código para llamar a `HasOne` o `HasMany` con una cadena se interpretaba de manera confusa.</span><span class="sxs-lookup"><span data-stu-id="56f1e-643">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="56f1e-644">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-644">For example:</span></span>
```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="56f1e-645">El código parece relacionar `Samurai` con otro tipo de entidad mediante la propiedad de navegación `Entrance`, que puede ser privada.</span><span class="sxs-lookup"><span data-stu-id="56f1e-645">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="56f1e-646">En realidad, este código intenta crear una relación con algún tipo de entidad denominada `Entrance` sin ninguna propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-646">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="56f1e-647">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-647">**New behavior**</span></span>

<span data-ttu-id="56f1e-648">A partir de EF Core 3.0, el código anterior ahora hace lo que parecía que debía hacer antes.</span><span class="sxs-lookup"><span data-stu-id="56f1e-648">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="56f1e-649">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-649">**Why**</span></span>

<span data-ttu-id="56f1e-650">El comportamiento anterior era muy confuso, especialmente al leer el código de configuración y al buscar errores.</span><span class="sxs-lookup"><span data-stu-id="56f1e-650">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="56f1e-651">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-651">**Mitigations**</span></span>

<span data-ttu-id="56f1e-652">Esto solo interrumpirá las aplicaciones que configuran de manera explícita las relaciones con cadenas para nombres de tipos y sin especificar explícitamente la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-652">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="56f1e-653">Esto no es habitual.</span><span class="sxs-lookup"><span data-stu-id="56f1e-653">This is not common.</span></span>
<span data-ttu-id="56f1e-654">El comportamiento anterior se puede obtener al pasar de manera explícita `null` para el nombre de la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-654">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="56f1e-655">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-655">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="56f1e-656">El tipo de valor devuelto para varios métodos asincrónicos se ha cambiado de Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="56f1e-656">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="56f1e-657">Problema de seguimiento n.º 15184</span><span class="sxs-lookup"><span data-stu-id="56f1e-657">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="56f1e-658">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-658">**Old behavior**</span></span>

<span data-ttu-id="56f1e-659">Antes, los siguientes métodos asincrónicos devolvían `Task<T>`:</span><span class="sxs-lookup"><span data-stu-id="56f1e-659">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="56f1e-660">`ValueGenerator.NextValueAsync()` (y las clases derivadas)</span><span class="sxs-lookup"><span data-stu-id="56f1e-660">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="56f1e-661">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-661">**New behavior**</span></span>

<span data-ttu-id="56f1e-662">Dichos métodos ahora devuelven `ValueTask<T>` durante el mismo `T` que antes.</span><span class="sxs-lookup"><span data-stu-id="56f1e-662">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="56f1e-663">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-663">**Why**</span></span>

<span data-ttu-id="56f1e-664">Este cambio reduce el número de asignaciones de montones que se producen al invocar estos métodos, lo que mejora el rendimiento general.</span><span class="sxs-lookup"><span data-stu-id="56f1e-664">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="56f1e-665">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-665">**Mitigations**</span></span>

<span data-ttu-id="56f1e-666">Las aplicaciones que simplemente esperen las API anteriores solo necesitan recompilarse, sin que sea necesario realizar cambios en el código fuente.</span><span class="sxs-lookup"><span data-stu-id="56f1e-666">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="56f1e-667">Un uso más complejo (p. ej., pasar el valor `Task` devuelto a `Task.WhenAny()`) normalmente requiere que el valor `ValueTask<T>` devuelto se convierta en `Task<T>` mediante una llamada a `AsTask()` en él.</span><span class="sxs-lookup"><span data-stu-id="56f1e-667">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="56f1e-668">Tenga en cuenta que esto niega la reducción de asignación que implica este cambio.</span><span class="sxs-lookup"><span data-stu-id="56f1e-668">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="56f1e-669">La anotación Relational:TypeMapping ahora es simplemente TypeMapping</span><span class="sxs-lookup"><span data-stu-id="56f1e-669">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="56f1e-670">Problema de seguimiento n.º 9913</span><span class="sxs-lookup"><span data-stu-id="56f1e-670">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="56f1e-671">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-671">**Old behavior**</span></span>

<span data-ttu-id="56f1e-672">El nombre de anotación para las anotaciones de asignación de tipos era "Relational:TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="56f1e-672">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="56f1e-673">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-673">**New behavior**</span></span>

<span data-ttu-id="56f1e-674">Ahora, el nombre de anotación para las anotaciones de asignación de tipos es "TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="56f1e-674">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="56f1e-675">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-675">**Why**</span></span>

<span data-ttu-id="56f1e-676">Ahora, las asignaciones de tipos se usan para algo más que solo para proveedores de bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="56f1e-676">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="56f1e-677">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-677">**Mitigations**</span></span>

<span data-ttu-id="56f1e-678">Esto solo interrumpirá a las aplicaciones que acceden directamente a la asignación de tipos como una anotación, lo que no es habitual.</span><span class="sxs-lookup"><span data-stu-id="56f1e-678">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="56f1e-679">La acción más apropiada para corregir es usar la superficie de API para acceder a las asignaciones de tipos en lugar de usar directamente la anotación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-679">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="56f1e-680">ToTable en un tipo derivado inicia una excepción</span><span class="sxs-lookup"><span data-stu-id="56f1e-680">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="56f1e-681">Problema de seguimiento n.º 11811</span><span class="sxs-lookup"><span data-stu-id="56f1e-681">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="56f1e-682">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-682">**Old behavior**</span></span>

<span data-ttu-id="56f1e-683">Antes de EF Core 3.0, la llamada a `ToTable()` en un tipo derivado se omitía, ya que la única estrategia asignación de herencia era TPH, lo que no es válido.</span><span class="sxs-lookup"><span data-stu-id="56f1e-683">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="56f1e-684">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-684">**New behavior**</span></span>

<span data-ttu-id="56f1e-685">A partir de EF Core 3.0, y en preparación para agregar compatibilidad con TPT y TPC en una versión posterior, ahora la llamada a `ToTable()` en un tipo derivado iniciará una excepción para evitar un cambio de asignación inesperado en el futuro.</span><span class="sxs-lookup"><span data-stu-id="56f1e-685">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="56f1e-686">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-686">**Why**</span></span>

<span data-ttu-id="56f1e-687">En la actualidad no se considera válido asignar un tipo derivado a otra tabla.</span><span class="sxs-lookup"><span data-stu-id="56f1e-687">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="56f1e-688">Este cambio evita interrupciones en el futuro, cuando se convierta en una operación válida.</span><span class="sxs-lookup"><span data-stu-id="56f1e-688">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="56f1e-689">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-689">**Mitigations**</span></span>

<span data-ttu-id="56f1e-690">Quite todos los intentos de asignar tipos derivados a otras tablas.</span><span class="sxs-lookup"><span data-stu-id="56f1e-690">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="56f1e-691">ForSqlServerHasIndex se ha reemplazado por HasIndex</span><span class="sxs-lookup"><span data-stu-id="56f1e-691">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="56f1e-692">Problema de seguimiento n.º 12366</span><span class="sxs-lookup"><span data-stu-id="56f1e-692">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="56f1e-693">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-693">**Old behavior**</span></span>

<span data-ttu-id="56f1e-694">Antes de EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` proporcionaba una manera de configurar las columnas que se usaban con `INCLUDE`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-694">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="56f1e-695">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-695">**New behavior**</span></span>

<span data-ttu-id="56f1e-696">A partir de EF Core 3.0, ya se admite el uso de `Include` en un índice en el nivel relacional.</span><span class="sxs-lookup"><span data-stu-id="56f1e-696">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="56f1e-697">Mediante `HasIndex().ForSqlServerInclude()`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-697">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="56f1e-698">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-698">**Why**</span></span>

<span data-ttu-id="56f1e-699">Este cambio se ha realizado para consolidar la API para índices con `Include` en un mismo lugar para todos los proveedores de base de datos.</span><span class="sxs-lookup"><span data-stu-id="56f1e-699">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="56f1e-700">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-700">**Mitigations**</span></span>

<span data-ttu-id="56f1e-701">Use la API nueva, como se ha mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="56f1e-701">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="56f1e-702">Cambios en la API de metadatos</span><span class="sxs-lookup"><span data-stu-id="56f1e-702">Metadata API changes</span></span>

[<span data-ttu-id="56f1e-703">Problema de seguimiento n.º 214</span><span class="sxs-lookup"><span data-stu-id="56f1e-703">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="56f1e-704">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-704">**New behavior**</span></span>

<span data-ttu-id="56f1e-705">Las siguientes propiedades se han convertido en métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="56f1e-705">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="56f1e-706">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-706">**Why**</span></span>

<span data-ttu-id="56f1e-707">Este cambio simplifica la implementación de las interfaces mencionadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="56f1e-707">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="56f1e-708">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-708">**Mitigations**</span></span>

<span data-ttu-id="56f1e-709">Use los nuevos métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="56f1e-709">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="56f1e-710">Cambios en la API de metadatos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="56f1e-710">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="56f1e-711">Problema de seguimiento n.º 214</span><span class="sxs-lookup"><span data-stu-id="56f1e-711">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="56f1e-712">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-712">**New behavior**</span></span>

<span data-ttu-id="56f1e-713">Los métodos de extensión específicos del proveedor se simplificarán:</span><span class="sxs-lookup"><span data-stu-id="56f1e-713">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

<span data-ttu-id="56f1e-714">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-714">**Why**</span></span>

<span data-ttu-id="56f1e-715">Este cambio simplifica la implementación de los métodos de extensión mencionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="56f1e-715">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="56f1e-716">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-716">**Mitigations**</span></span>

<span data-ttu-id="56f1e-717">Use los nuevos métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="56f1e-717">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="56f1e-718">EF Core ya no envía pragma para el cumplimiento de SQLite FK</span><span class="sxs-lookup"><span data-stu-id="56f1e-718">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="56f1e-719">Problema de seguimiento n.º 12151</span><span class="sxs-lookup"><span data-stu-id="56f1e-719">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="56f1e-720">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-720">**Old behavior**</span></span>

<span data-ttu-id="56f1e-721">Antes de EF Core 3.0, EF Core enviaba `PRAGMA foreign_keys = 1` cuando se abría una conexión con SQLite.</span><span class="sxs-lookup"><span data-stu-id="56f1e-721">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="56f1e-722">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-722">**New behavior**</span></span>

<span data-ttu-id="56f1e-723">A partir de EF Core 3.0, EF Core ya no envía `PRAGMA foreign_keys = 1` cuando se abre una conexión con SQLite.</span><span class="sxs-lookup"><span data-stu-id="56f1e-723">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="56f1e-724">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-724">**Why**</span></span>

<span data-ttu-id="56f1e-725">Este cambio se ha realizado porque en EF Core se usa `SQLitePCLRaw.bundle_e_sqlite3` de forma predeterminada, lo que a su vez significa que el cumplimiento de CD está activado de forma predeterminada y no es necesario habilitarlo explícitamente cada vez que se abra una conexión.</span><span class="sxs-lookup"><span data-stu-id="56f1e-725">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="56f1e-726">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-726">**Mitigations**</span></span>

<span data-ttu-id="56f1e-727">Las claves externas se habilitan de forma predeterminada en SQLitePCLRaw.bundle_e_sqlite3, que en EF Core se usa de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="56f1e-727">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="56f1e-728">Para otros casos, las claves externas se pueden habilitar mediante la especificación de `Foreign Keys=True` en la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="56f1e-728">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="56f1e-729">Microsoft.EntityFrameworkCore.Sqlite ahora depende de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="56f1e-729">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="56f1e-730">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-730">**Old behavior**</span></span>

<span data-ttu-id="56f1e-731">Antes de EF Core 3.0, en EF Core se usaba `SQLitePCLRaw.bundle_green`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-731">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="56f1e-732">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-732">**New behavior**</span></span>

<span data-ttu-id="56f1e-733">A partir de EF Core 3.0, en EF Core se usa `SQLitePCLRaw.bundle_e_sqlite3`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-733">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="56f1e-734">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-734">**Why**</span></span>

<span data-ttu-id="56f1e-735">Este cambio se ha realizado para que la versión de SQLite que se usa en iOS sea coherente con otras plataformas.</span><span class="sxs-lookup"><span data-stu-id="56f1e-735">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="56f1e-736">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-736">**Mitigations**</span></span>

<span data-ttu-id="56f1e-737">Para usar la versión nativa de SQLite en iOS, configure `Microsoft.Data.Sqlite` para usar otra agrupación `SQLitePCLRaw`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-737">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="56f1e-738">Almacenamiento de valores GUID como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="56f1e-738">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="56f1e-739">Problema de seguimiento n.º 15078</span><span class="sxs-lookup"><span data-stu-id="56f1e-739">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="56f1e-740">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-740">**Old behavior**</span></span>

<span data-ttu-id="56f1e-741">Antes, los valores GUID se almacenaban como valores BLOB en SQLite.</span><span class="sxs-lookup"><span data-stu-id="56f1e-741">Guid values were previously stored as BLOB values on SQLite.</span></span>

<span data-ttu-id="56f1e-742">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-742">**New behavior**</span></span>

<span data-ttu-id="56f1e-743">Ahora, los valores GUID se almacenan como TEXT.</span><span class="sxs-lookup"><span data-stu-id="56f1e-743">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="56f1e-744">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-744">**Why**</span></span>

<span data-ttu-id="56f1e-745">El formato binario de los GUID no está normalizado.</span><span class="sxs-lookup"><span data-stu-id="56f1e-745">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="56f1e-746">El almacenamiento de los valores como TEXT mejora la compatibilidad de la base de datos con otras tecnologías.</span><span class="sxs-lookup"><span data-stu-id="56f1e-746">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="56f1e-747">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-747">**Mitigations**</span></span>

<span data-ttu-id="56f1e-748">Puede migrar las bases de datos existentes al nuevo formato ejecutando SQL de la siguiente forma.</span><span class="sxs-lookup"><span data-stu-id="56f1e-748">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET GuidColumn = hex(substr(GuidColumn, 4, 1)) ||
                 hex(substr(GuidColumn, 3, 1)) ||
                 hex(substr(GuidColumn, 2, 1)) ||
                 hex(substr(GuidColumn, 1, 1)) || '-' ||
                 hex(substr(GuidColumn, 6, 1)) ||
                 hex(substr(GuidColumn, 5, 1)) || '-' ||
                 hex(substr(GuidColumn, 8, 1)) ||
                 hex(substr(GuidColumn, 7, 1)) || '-' ||
                 hex(substr(GuidColumn, 9, 2)) || '-' ||
                 hex(substr(GuidColumn, 11, 6))
WHERE typeof(GuidColumn) == 'blob';
```

<span data-ttu-id="56f1e-749">En EF Core, también puede seguir usando el comportamiento anterior configurando un convertidor de valores en estas propiedades.</span><span class="sxs-lookup"><span data-stu-id="56f1e-749">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="56f1e-750">Microsoft.Data.Sqlite sigue siendo capaz de leer valores GUID de ambas columnas BLOB y TEXT. Sin embargo, dado que el formato predeterminado de los parámetros y las constantes ha cambiado, seguramente deberá realizar alguna acción en la mayoría de casos que impliquen el uso de valores GUID.</span><span class="sxs-lookup"><span data-stu-id="56f1e-750">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="56f1e-751">Ahora los valores char se almacenan como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="56f1e-751">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="56f1e-752">Problema de seguimiento n.º 15020</span><span class="sxs-lookup"><span data-stu-id="56f1e-752">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="56f1e-753">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-753">**Old behavior**</span></span>

<span data-ttu-id="56f1e-754">Anteriormente los valores char se almacenaban como valores INTEGER en SQLite.</span><span class="sxs-lookup"><span data-stu-id="56f1e-754">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="56f1e-755">Por ejemplo, un valor char de *A* se almacenaba como el valor entero 65.</span><span class="sxs-lookup"><span data-stu-id="56f1e-755">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="56f1e-756">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-756">**New behavior**</span></span>

<span data-ttu-id="56f1e-757">Ahora, los valores char se almacenan como TEXT.</span><span class="sxs-lookup"><span data-stu-id="56f1e-757">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="56f1e-758">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-758">**Why**</span></span>

<span data-ttu-id="56f1e-759">El almacenamiento de valores como TEXT es más natural y mejora la compatibilidad de la base de datos con otras tecnologías.</span><span class="sxs-lookup"><span data-stu-id="56f1e-759">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="56f1e-760">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-760">**Mitigations**</span></span>

<span data-ttu-id="56f1e-761">Puede migrar las bases de datos existentes al nuevo formato ejecutando SQL de la siguiente forma.</span><span class="sxs-lookup"><span data-stu-id="56f1e-761">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="56f1e-762">En EF Core, también puede seguir usando el comportamiento anterior configurando un convertidor de valores en estas propiedades.</span><span class="sxs-lookup"><span data-stu-id="56f1e-762">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="56f1e-763">Microsoft.Data.Sqlite también puede leer valores de caracteres tanto de columnas INTEGER como de columnas TEXT, por lo que es posible que no deba hacer nada dependiendo de su caso.</span><span class="sxs-lookup"><span data-stu-id="56f1e-763">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="56f1e-764">Ahora los id. de migración se generan usando el calendario de la referencia cultural invariable</span><span class="sxs-lookup"><span data-stu-id="56f1e-764">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="56f1e-765">Problema de seguimiento n.º 12978</span><span class="sxs-lookup"><span data-stu-id="56f1e-765">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="56f1e-766">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-766">**Old behavior**</span></span>

<span data-ttu-id="56f1e-767">Los identificadores de migración se generaban de forma involuntaria con el calendario de la referencia cultural actual.</span><span class="sxs-lookup"><span data-stu-id="56f1e-767">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="56f1e-768">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-768">**New behavior**</span></span>

<span data-ttu-id="56f1e-769">Ahora los id. de migración siempre se generan usando el calendario de la referencia cultural invariable (gregoriano).</span><span class="sxs-lookup"><span data-stu-id="56f1e-769">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="56f1e-770">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-770">**Why**</span></span>

<span data-ttu-id="56f1e-771">El orden de las migraciones es importante al actualizar la base de datos o al solucionar conflictos de combinación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-771">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="56f1e-772">Al usar el calendario invariable, se evitan problemas de ordenación que pueden producirse si los miembros del equipo tienen distintos calendarios del sistema.</span><span class="sxs-lookup"><span data-stu-id="56f1e-772">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="56f1e-773">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-773">**Mitigations**</span></span>

<span data-ttu-id="56f1e-774">Esta cambio afecta a todas las personas que usan un calendario no gregoriano en el que el año sea superior al del calendario gregoriano (como el calendario budista tailandés).</span><span class="sxs-lookup"><span data-stu-id="56f1e-774">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="56f1e-775">Los id. de migración existentes deberán actualizarse para que las migraciones nuevas se ordenen después de las existentes.</span><span class="sxs-lookup"><span data-stu-id="56f1e-775">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="56f1e-776">Puede ver el id. de migración en el atributo Migration de los archivos de diseñador de la migración.</span><span class="sxs-lookup"><span data-stu-id="56f1e-776">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="56f1e-777">También debe actualizarse la tabla de historial de migraciones.</span><span class="sxs-lookup"><span data-stu-id="56f1e-777">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="56f1e-778">Se ha quitado el elemento UseRowNumberForPaging</span><span class="sxs-lookup"><span data-stu-id="56f1e-778">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="56f1e-779">Problema de seguimiento n.º 16400</span><span class="sxs-lookup"><span data-stu-id="56f1e-779">Tracking Issue #16400</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

<span data-ttu-id="56f1e-780">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-780">**Old behavior**</span></span>

<span data-ttu-id="56f1e-781">Antes de EF Core 3.0, `UseRowNumberForPaging` se podía usar para generar SQL para la paginación de forma que fuera compatible con SQL Server 2008.</span><span class="sxs-lookup"><span data-stu-id="56f1e-781">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

<span data-ttu-id="56f1e-782">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-782">**New behavior**</span></span>

<span data-ttu-id="56f1e-783">A partir de EF Core 3.0, EF solo genera SQL para la paginación que únicamente es compatible con las versiones posteriores de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="56f1e-783">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span> 

<span data-ttu-id="56f1e-784">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-784">**Why**</span></span>

<span data-ttu-id="56f1e-785">El motivo de este cambio es que [SQL Server 2008 ya no se admite](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/). Además, la actualización de esta característica para que funcionase con los cambios en las consultas implementados en EF Core 3.0 llevaría mucho trabajo.</span><span class="sxs-lookup"><span data-stu-id="56f1e-785">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

<span data-ttu-id="56f1e-786">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-786">**Mitigations**</span></span>

<span data-ttu-id="56f1e-787">Se recomienda actualizar a una versión más reciente de SQL Server, o bien utilizar un nivel de compatibilidad superior, de modo que el SQL que se genere se admita.</span><span class="sxs-lookup"><span data-stu-id="56f1e-787">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="56f1e-788">Dicho esto, si no puede hacerlo, [escriba un comentario en el problema de seguimiento](https://github.com/aspnet/EntityFrameworkCore/issues/16400) con los detalles al respecto.</span><span class="sxs-lookup"><span data-stu-id="56f1e-788">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) with details.</span></span> <span data-ttu-id="56f1e-789">En función de los comentarios, es posible que volvamos a valorar esta decisión.</span><span class="sxs-lookup"><span data-stu-id="56f1e-789">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="56f1e-790">La información o metadatos de la extensión se han quitado de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="56f1e-790">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="56f1e-791">Problema de seguimiento n.º 16119</span><span class="sxs-lookup"><span data-stu-id="56f1e-791">Tracking Issue #16119</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

<span data-ttu-id="56f1e-792">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-792">**Old behavior**</span></span>

<span data-ttu-id="56f1e-793">`IDbContextOptionsExtension` incluía métodos para proporcionar metadatos sobre la extensión.</span><span class="sxs-lookup"><span data-stu-id="56f1e-793">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

<span data-ttu-id="56f1e-794">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-794">**New behavior**</span></span>

<span data-ttu-id="56f1e-795">Estos métodos se han movido a una nueva clase base abstracta `DbContextOptionsExtensionInfo`, que se devuelve desde una nueva propiedad `IDbContextOptionsExtension.Info`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-795">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

<span data-ttu-id="56f1e-796">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-796">**Why**</span></span>

<span data-ttu-id="56f1e-797">Al lanzarse las versiones 2.0 y 3.0, tuvimos que agregar o cambiar estos métodos varias veces.</span><span class="sxs-lookup"><span data-stu-id="56f1e-797">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="56f1e-798">Su división en una nueva clase base abstracta facilitará la realización de este tipo de cambios sin interrumpir las extensiones existentes.</span><span class="sxs-lookup"><span data-stu-id="56f1e-798">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

<span data-ttu-id="56f1e-799">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-799">**Mitigations**</span></span>

<span data-ttu-id="56f1e-800">Actualice las extensiones para seguir el nuevo patrón.</span><span class="sxs-lookup"><span data-stu-id="56f1e-800">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="56f1e-801">Encontrará ejemplos en las muchas implementaciones de `IDbContextOptionsExtension` para los diferentes tipos de extensiones en el código fuente de EF Core.</span><span class="sxs-lookup"><span data-stu-id="56f1e-801">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="56f1e-802">Cambio de nombre de LogQueryPossibleExceptionWithAggregateOperator</span><span class="sxs-lookup"><span data-stu-id="56f1e-802">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="56f1e-803">Problema de seguimiento n.º 10985</span><span class="sxs-lookup"><span data-stu-id="56f1e-803">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="56f1e-804">**Cambio**</span><span class="sxs-lookup"><span data-stu-id="56f1e-804">**Change**</span></span>

<span data-ttu-id="56f1e-805">Se ha cambiado el nombre de `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` a `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span><span class="sxs-lookup"><span data-stu-id="56f1e-805">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="56f1e-806">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-806">**Why**</span></span>

<span data-ttu-id="56f1e-807">Conviene alinear el nombre de este evento de advertencia con el del resto de eventos de advertencia.</span><span class="sxs-lookup"><span data-stu-id="56f1e-807">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="56f1e-808">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-808">**Mitigations**</span></span>

<span data-ttu-id="56f1e-809">Use el nuevo nombre.</span><span class="sxs-lookup"><span data-stu-id="56f1e-809">Use the new name.</span></span> <span data-ttu-id="56f1e-810">(Tenga en cuenta que el número de id. evento sigue siendo el mismo).</span><span class="sxs-lookup"><span data-stu-id="56f1e-810">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="56f1e-811">Clarificación de la API para nombres de restricciones de claves externas</span><span class="sxs-lookup"><span data-stu-id="56f1e-811">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="56f1e-812">Problema de seguimiento n.º 10730</span><span class="sxs-lookup"><span data-stu-id="56f1e-812">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="56f1e-813">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-813">**Old behavior**</span></span>

<span data-ttu-id="56f1e-814">Antes de EF Core 3.0, se utilizaba simplemente el término "nombre" para hacer referencia a los nombres de las restricciones de claves externas.</span><span class="sxs-lookup"><span data-stu-id="56f1e-814">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="56f1e-815">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-815">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="56f1e-816">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-816">**New behavior**</span></span>

<span data-ttu-id="56f1e-817">A partir de EF Core 3.0, el término con el que se hace referencia a los nombres de las restricciones de claves externas es "nombre de la restricción".</span><span class="sxs-lookup"><span data-stu-id="56f1e-817">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="56f1e-818">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-818">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="56f1e-819">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-819">**Why**</span></span>

<span data-ttu-id="56f1e-820">Este cambio permite mejorar la coherencia relativa a la nomenclatura en este aspecto y aclarar que se trata del nombre de una restricción de clave externa, y no del de la columna o propiedad en la que está definida la clave externa.</span><span class="sxs-lookup"><span data-stu-id="56f1e-820">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="56f1e-821">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-821">**Mitigations**</span></span>

<span data-ttu-id="56f1e-822">Use el nuevo nombre.</span><span class="sxs-lookup"><span data-stu-id="56f1e-822">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="56f1e-823">IRelationalDatabaseCreator.HasTables/HasTablesAsync se han hecho públicos</span><span class="sxs-lookup"><span data-stu-id="56f1e-823">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="56f1e-824">Problema de seguimiento n.° 15997</span><span class="sxs-lookup"><span data-stu-id="56f1e-824">Tracking Issue #15997</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

<span data-ttu-id="56f1e-825">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-825">**Old behavior**</span></span>

<span data-ttu-id="56f1e-826">Antes de EF Core 3.0, estos métodos estaban protegidos.</span><span class="sxs-lookup"><span data-stu-id="56f1e-826">Before EF Core 3.0, these methods were protected.</span></span>

<span data-ttu-id="56f1e-827">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-827">**New behavior**</span></span>

<span data-ttu-id="56f1e-828">Desde EF Core 3.0, estos métodos son públicos.</span><span class="sxs-lookup"><span data-stu-id="56f1e-828">Starting with EF Core 3.0, these methods are public.</span></span>

<span data-ttu-id="56f1e-829">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-829">**Why**</span></span>

<span data-ttu-id="56f1e-830">EF usa estos métodos para determinar si se ha creado una base de datos, pero está vacía.</span><span class="sxs-lookup"><span data-stu-id="56f1e-830">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="56f1e-831">Esto también puede resultar útil fuera de EF al determinar si se deben aplicar migraciones o no.</span><span class="sxs-lookup"><span data-stu-id="56f1e-831">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

<span data-ttu-id="56f1e-832">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-832">**Mitigations**</span></span>

<span data-ttu-id="56f1e-833">Cambie la accesibilidad de cualquier invalidación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-833">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="56f1e-834">Microsoft.EntityFrameworkCore.Design es ahora un paquete DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="56f1e-834">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="56f1e-835">Problema de seguimiento n.° 11506</span><span class="sxs-lookup"><span data-stu-id="56f1e-835">Tracking Issue #11506</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

<span data-ttu-id="56f1e-836">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-836">**Old behavior**</span></span>

<span data-ttu-id="56f1e-837">Antes de EF Core 3.0, Microsoft.EntityFrameworkCore.Design era un paquete NuGet regular con un ensamblado al que podían hacer referencia los proyectos que dependían de él.</span><span class="sxs-lookup"><span data-stu-id="56f1e-837">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

<span data-ttu-id="56f1e-838">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-838">**New behavior**</span></span>

<span data-ttu-id="56f1e-839">Desde EF Core 3.0, es un paquete DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="56f1e-839">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="56f1e-840">Esto significa que la dependencia no fluirá de manera transitiva en otros proyectos y que ya no puede, de forma predeterminada, hacer referencia a su ensamblado.</span><span class="sxs-lookup"><span data-stu-id="56f1e-840">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

<span data-ttu-id="56f1e-841">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-841">**Why**</span></span>

<span data-ttu-id="56f1e-842">Este paquete solo está destinado a usarse en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="56f1e-842">This package is only intended to be used at design time.</span></span> <span data-ttu-id="56f1e-843">Las aplicaciones implementadas no deben hacer referencia al mismo.</span><span class="sxs-lookup"><span data-stu-id="56f1e-843">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="56f1e-844">Hacer que el paquete sea DevelopmentDependency refuerza esta recomendación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-844">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

<span data-ttu-id="56f1e-845">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-845">**Mitigations**</span></span>

<span data-ttu-id="56f1e-846">Si tiene que hacer referencia a este paquete para invalidar el comportamiento en tiempo de diseño de EF Core, puede actualizar los metadatos de elementos PackageReference del proyecto.</span><span class="sxs-lookup"><span data-stu-id="56f1e-846">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="56f1e-847">Si se hace referencia al paquete de manera transitiva a través de Microsoft.EntityFrameworkCore.Tools, tendrá que agregar una PackageReference explícita al paquete para cambiar sus metadatos.</span><span class="sxs-lookup"><span data-stu-id="56f1e-847">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="56f1e-848">Este tipo de referencia explícita debe agregarse a cualquier proyecto que requiera los tipos de paquete.</span><span class="sxs-lookup"><span data-stu-id="56f1e-848">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="56f1e-849">SQLitePCL.raw se ha actualizado a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="56f1e-849">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="56f1e-850">Problema de seguimiento n.° 14824</span><span class="sxs-lookup"><span data-stu-id="56f1e-850">Tracking Issue #14824</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

<span data-ttu-id="56f1e-851">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-851">**Old behavior**</span></span>

<span data-ttu-id="56f1e-852">Microsoft.EntityFrameworkCore.Sqlite dependía anteriormente de la versión 1.1.12 de SQLitePCL.raw.</span><span class="sxs-lookup"><span data-stu-id="56f1e-852">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

<span data-ttu-id="56f1e-853">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-853">**New behavior**</span></span>

<span data-ttu-id="56f1e-854">Hemos actualizado nuestro paquete para depender de la versión 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="56f1e-854">We've updated our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="56f1e-855">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-855">**Why**</span></span>

<span data-ttu-id="56f1e-856">La versión 2.0.0 de SQLitePCL.raw selecciona .NET Standard 2.0 como destino.</span><span class="sxs-lookup"><span data-stu-id="56f1e-856">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="56f1e-857">Anteriormente seleccionaba .NET Standard 1.1 como destino, que requería el cierre a gran escala de paquetes transitivos para su funcionamiento.</span><span class="sxs-lookup"><span data-stu-id="56f1e-857">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

<span data-ttu-id="56f1e-858">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-858">**Mitigations**</span></span>

<span data-ttu-id="56f1e-859">En la versión 2.0.0 de SQLitePCL.raw se incluyen algunos cambios importantes.</span><span class="sxs-lookup"><span data-stu-id="56f1e-859">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="56f1e-860">Consulte las [notas de la versión](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) para obtener detalles.</span><span class="sxs-lookup"><span data-stu-id="56f1e-860">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="56f1e-861">NetTopologySuite se actualizó a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="56f1e-861">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="56f1e-862">Problema de seguimiento n.° 14825</span><span class="sxs-lookup"><span data-stu-id="56f1e-862">Tracking Issue #14825</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

<span data-ttu-id="56f1e-863">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-863">**Old behavior**</span></span>

<span data-ttu-id="56f1e-864">Los paquetes espaciales anteriormente dependían de la versión 1.15.1 de NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="56f1e-864">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

<span data-ttu-id="56f1e-865">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-865">**New behavior**</span></span>

<span data-ttu-id="56f1e-866">Hemos actualizado nuestro paquete para depender de la versión 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="56f1e-866">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="56f1e-867">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-867">**Why**</span></span>

<span data-ttu-id="56f1e-868">La versión 2.0.0 de NetTopologySuite pretende resolver varios problemas de usabilidad que encontraron los usuarios de EF Core.</span><span class="sxs-lookup"><span data-stu-id="56f1e-868">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

<span data-ttu-id="56f1e-869">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-869">**Mitigations**</span></span>

<span data-ttu-id="56f1e-870">En la versión 2.0.0 de NetTopologySuite se incluyen algunos cambios importantes.</span><span class="sxs-lookup"><span data-stu-id="56f1e-870">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="56f1e-871">Consulte las [notas de la versión](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) para obtener detalles.</span><span class="sxs-lookup"><span data-stu-id="56f1e-871">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="56f1e-872">Se usa Microsoft.Data.SqlClient en lugar de System.Data.SqlClient</span><span class="sxs-lookup"><span data-stu-id="56f1e-872">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="56f1e-873">Problema de seguimiento n.º 15636</span><span class="sxs-lookup"><span data-stu-id="56f1e-873">Tracking Issue #15636</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

<span data-ttu-id="56f1e-874">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-874">**Old behavior**</span></span>

<span data-ttu-id="56f1e-875">Microsoft.EntityFrameworkCore.SqlServer dependía anteriormente de la versión System.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="56f1e-875">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

<span data-ttu-id="56f1e-876">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-876">**New behavior**</span></span>

<span data-ttu-id="56f1e-877">Hemos actualizado nuestro paquete para que dependa de Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="56f1e-877">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="56f1e-878">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-878">**Why**</span></span>

<span data-ttu-id="56f1e-879">A partir de ahora, Microsoft.Data.SqlClient es el controlador de acceso a datos insignia para SQL Server y System.Data.SqlClient ya no es el centro de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="56f1e-879">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="56f1e-880">Algunas características importantes, como Always Encrypted, solo están disponibles en Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="56f1e-880">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="56f1e-881">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-881">**Mitigations**</span></span>

<span data-ttu-id="56f1e-882">Si el código toma una dependencia directa en System.Data.SqlClient, debe cambiarla para que haga referencia a Microsoft.Data.SqlClient en su lugar. Dado que los dos paquetes mantienen un grado muy alto de compatibilidad con la API, solo debería ser un paquete simple y un cambio de espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="56f1e-882">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="56f1e-883">Se deben configurar varias relaciones de referencia automática ambiguas</span><span class="sxs-lookup"><span data-stu-id="56f1e-883">Multiple ambiguous self-referencing relationships must be configured</span></span> 

[<span data-ttu-id="56f1e-884">Problema de seguimiento n.º 13573</span><span class="sxs-lookup"><span data-stu-id="56f1e-884">Tracking Issue #13573</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

<span data-ttu-id="56f1e-885">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-885">**Old behavior**</span></span>

<span data-ttu-id="56f1e-886">Un tipo de entidad con varias propiedades de navegación unidireccional de referencia automática y claves externas coincidentes se configuró incorrectamente como una única relación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-886">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="56f1e-887">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-887">For example:</span></span>

```csharp
public class User 
{
        public Guid Id { get; set; }
        public User CreatedBy { get; set; }
        public User UpdatedBy { get; set; }
        public Guid CreatedById { get; set; }
        public Guid? UpdatedById { get; set; }
}
```

<span data-ttu-id="56f1e-888">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-888">**New behavior**</span></span>

<span data-ttu-id="56f1e-889">Este escenario se detecta ahora en la generación del modelo y se produce una excepción que indica que el modelo es ambiguo.</span><span class="sxs-lookup"><span data-stu-id="56f1e-889">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

<span data-ttu-id="56f1e-890">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-890">**Why**</span></span>

<span data-ttu-id="56f1e-891">El modelo resultante era ambiguo, y lo más probable es que sea incorrecto en este caso.</span><span class="sxs-lookup"><span data-stu-id="56f1e-891">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

<span data-ttu-id="56f1e-892">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-892">**Mitigations**</span></span>

<span data-ttu-id="56f1e-893">Utilice la configuración completa de la relación.</span><span class="sxs-lookup"><span data-stu-id="56f1e-893">Use full configuration of the relationship.</span></span> <span data-ttu-id="56f1e-894">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="56f1e-894">For example:</span></span>

```csharp
modelBuilder
     .Entity<User>()
     .HasOne(e => e.CreatedBy)
     .WithMany();
 
 modelBuilder
     .Entity<User>()
     .HasOne(e => e.UpdatedBy)
     .WithMany();
```

<a name="udf-empty-string"></a>
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="56f1e-895">DbFunction.Schema es NULL o la cadena vacía lo configura para estar en el esquema predeterminado del modelo</span><span class="sxs-lookup"><span data-stu-id="56f1e-895">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="56f1e-896">Problema de seguimiento n.º 12757</span><span class="sxs-lookup"><span data-stu-id="56f1e-896">Tracking Issue #12757</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

<span data-ttu-id="56f1e-897">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="56f1e-897">**Old behavior**</span></span>

<span data-ttu-id="56f1e-898">Una función DbFunction configurada con el esquema como una cadena vacía se trataba como una función integrada sin un esquema.</span><span class="sxs-lookup"><span data-stu-id="56f1e-898">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="56f1e-899">Por ejemplo, el código siguiente asignará la función CLR `DatePart` a la función integrada `DATEPART` en SqlServer.</span><span class="sxs-lookup"><span data-stu-id="56f1e-899">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

<span data-ttu-id="56f1e-900">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="56f1e-900">**New behavior**</span></span>

<span data-ttu-id="56f1e-901">Todas las asignaciones de DbFunction se consideran asignadas a funciones definidas por el usuario.</span><span class="sxs-lookup"><span data-stu-id="56f1e-901">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="56f1e-902">Por lo tanto, el valor de cadena vacía colocaría la función dentro del esquema predeterminado del modelo,</span><span class="sxs-lookup"><span data-stu-id="56f1e-902">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="56f1e-903">que podría ser el esquema configurado de forma explícita mediante `modelBuilder.HasDefaultSchema()` de la API fluida o `dbo` en caso contrario.</span><span class="sxs-lookup"><span data-stu-id="56f1e-903">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

<span data-ttu-id="56f1e-904">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="56f1e-904">**Why**</span></span>

<span data-ttu-id="56f1e-905">Anteriormente, el esquema vacío era una manera de indicar que la función estaba integrada, pero esa lógica solo es aplicable a SqlServer, donde las funciones integradas no pertenecen a ningún esquema.</span><span class="sxs-lookup"><span data-stu-id="56f1e-905">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

<span data-ttu-id="56f1e-906">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="56f1e-906">**Mitigations**</span></span>

<span data-ttu-id="56f1e-907">Configure la traslación de DbFunction manualmente para asignarla a una función integrada.</span><span class="sxs-lookup"><span data-stu-id="56f1e-907">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```
