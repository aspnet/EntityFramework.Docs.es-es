---
title: 'Cambios importantes en EF Core 3.x: EF Core'
description: Lista completa de los cambios importantes introducidos en Entity Framework Core 3.x
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: 644e61994dab4e9993c6a78792ff584c57fbe48a
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620692"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a><span data-ttu-id="2b05e-103">Cambios importantes incluidos en EF Core 3.x</span><span class="sxs-lookup"><span data-stu-id="2b05e-103">Breaking changes included in EF Core 3.x</span></span>

<span data-ttu-id="2b05e-104">Es posible que los siguientes cambios de API y comportamiento interrumpan las aplicaciones existentes cuando se actualicen a las versiones 3.x.</span><span class="sxs-lookup"><span data-stu-id="2b05e-104">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.x.</span></span>
<span data-ttu-id="2b05e-105">Los cambios que esperamos que solo afecten a proveedores de base de datos se documentan en [Cambios para proveedores](xref:core/providers/provider-log).</span><span class="sxs-lookup"><span data-stu-id="2b05e-105">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="2b05e-106">Resumen</span><span class="sxs-lookup"><span data-stu-id="2b05e-106">Summary</span></span>

| <span data-ttu-id="2b05e-107">**Cambio importante**</span><span class="sxs-lookup"><span data-stu-id="2b05e-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="2b05e-108">**Impacto**</span><span class="sxs-lookup"><span data-stu-id="2b05e-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="2b05e-109">Las consultas LINQ ya no se evalúan en el cliente</span><span class="sxs-lookup"><span data-stu-id="2b05e-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="2b05e-110">Alto</span><span class="sxs-lookup"><span data-stu-id="2b05e-110">High</span></span>       |
| [<span data-ttu-id="2b05e-111">EF Core 3.0 tiene como destino .NET Standard 2.1, y no .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="2b05e-111">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>](#netstandard21) | <span data-ttu-id="2b05e-112">Alto</span><span class="sxs-lookup"><span data-stu-id="2b05e-112">High</span></span>      |
| [<span data-ttu-id="2b05e-113">La herramienta de línea de comandos de EF Core, dotnet ef, ya no forma parte del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="2b05e-113">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="2b05e-114">Alto</span><span class="sxs-lookup"><span data-stu-id="2b05e-114">High</span></span>      |
| [<span data-ttu-id="2b05e-115">DetectChanges respeta los valores de clave generados por el almacén</span><span class="sxs-lookup"><span data-stu-id="2b05e-115">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="2b05e-116">Alto</span><span class="sxs-lookup"><span data-stu-id="2b05e-116">High</span></span>      |
| [<span data-ttu-id="2b05e-117">FromSql, ExecuteSql y ExecuteSqlAsync han cambiado de nombre</span><span class="sxs-lookup"><span data-stu-id="2b05e-117">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="2b05e-118">Alto</span><span class="sxs-lookup"><span data-stu-id="2b05e-118">High</span></span>      |
| [<span data-ttu-id="2b05e-119">Los tipos de consulta se consolidan con tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="2b05e-119">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="2b05e-120">Alto</span><span class="sxs-lookup"><span data-stu-id="2b05e-120">High</span></span>      |
| [<span data-ttu-id="2b05e-121">Entity Framework Core ya no forma parte del marco compartido ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2b05e-121">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="2b05e-122">Media</span><span class="sxs-lookup"><span data-stu-id="2b05e-122">Medium</span></span>      |
| [<span data-ttu-id="2b05e-123">Las eliminaciones en cascada ahora se realizan inmediatamente de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="2b05e-123">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="2b05e-124">Media</span><span class="sxs-lookup"><span data-stu-id="2b05e-124">Medium</span></span>      |
| [<span data-ttu-id="2b05e-125">La carga diligente de entidades relacionadas ahora se realiza en una sola consulta</span><span class="sxs-lookup"><span data-stu-id="2b05e-125">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="2b05e-126">Media</span><span class="sxs-lookup"><span data-stu-id="2b05e-126">Medium</span></span>      |
| [<span data-ttu-id="2b05e-127">DeleteBehavior.Restrict tiene una semántica más limpia</span><span class="sxs-lookup"><span data-stu-id="2b05e-127">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="2b05e-128">Media</span><span class="sxs-lookup"><span data-stu-id="2b05e-128">Medium</span></span>      |
| [<span data-ttu-id="2b05e-129">La API de configuración para las relaciones de tipo de propiedad ha cambiado</span><span class="sxs-lookup"><span data-stu-id="2b05e-129">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="2b05e-130">Media</span><span class="sxs-lookup"><span data-stu-id="2b05e-130">Medium</span></span>      |
| [<span data-ttu-id="2b05e-131">Cada propiedad usa la generación de claves enteras en memoria independiente</span><span class="sxs-lookup"><span data-stu-id="2b05e-131">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="2b05e-132">Media</span><span class="sxs-lookup"><span data-stu-id="2b05e-132">Medium</span></span>      |
| [<span data-ttu-id="2b05e-133">Las consultas sin seguimiento ya no realizan la resolución de la identidad</span><span class="sxs-lookup"><span data-stu-id="2b05e-133">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="2b05e-134">Media</span><span class="sxs-lookup"><span data-stu-id="2b05e-134">Medium</span></span>      |
| [<span data-ttu-id="2b05e-135">Cambios en la API de metadatos</span><span class="sxs-lookup"><span data-stu-id="2b05e-135">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="2b05e-136">Media</span><span class="sxs-lookup"><span data-stu-id="2b05e-136">Medium</span></span>      |
| [<span data-ttu-id="2b05e-137">Cambios en la API de metadatos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="2b05e-137">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="2b05e-138">Media</span><span class="sxs-lookup"><span data-stu-id="2b05e-138">Medium</span></span>      |
| [<span data-ttu-id="2b05e-139">Se ha quitado el elemento UseRowNumberForPaging</span><span class="sxs-lookup"><span data-stu-id="2b05e-139">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="2b05e-140">Media</span><span class="sxs-lookup"><span data-stu-id="2b05e-140">Medium</span></span>      |
| [<span data-ttu-id="2b05e-141">Cuando el método FromSql se usa con un procedimiento almacenado no se puede redactar</span><span class="sxs-lookup"><span data-stu-id="2b05e-141">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="2b05e-142">Media</span><span class="sxs-lookup"><span data-stu-id="2b05e-142">Medium</span></span>      |
| [<span data-ttu-id="2b05e-143">Solo se pueden especificar métodos de FromSql en raíces de consulta</span><span class="sxs-lookup"><span data-stu-id="2b05e-143">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="2b05e-144">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-144">Low</span></span>      |
| [<span data-ttu-id="2b05e-145">~~La ejecución de consultas se registra en el nivel de depuración~~ Revertido</span><span class="sxs-lookup"><span data-stu-id="2b05e-145">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | <span data-ttu-id="2b05e-146">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-146">Low</span></span>      |
| [<span data-ttu-id="2b05e-147">Los valores de clave temporal ya no se establecen en instancias de entidad</span><span class="sxs-lookup"><span data-stu-id="2b05e-147">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="2b05e-148">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-148">Low</span></span>      |
| [<span data-ttu-id="2b05e-149">Ahora, las entidades dependientes que comparten la tabla con la entidad de seguridad son opcionales</span><span class="sxs-lookup"><span data-stu-id="2b05e-149">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="2b05e-150">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-150">Low</span></span>      |
| [<span data-ttu-id="2b05e-151">Todas las entidades que compartan una tabla con una columna de token de simultaneidad tienen que asignarla a una propiedad</span><span class="sxs-lookup"><span data-stu-id="2b05e-151">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="2b05e-152">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-152">Low</span></span>      |
| [<span data-ttu-id="2b05e-153">Las entidades en propiedad no se pueden consultar sin el propietario mediante una consulta de seguimiento</span><span class="sxs-lookup"><span data-stu-id="2b05e-153">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="2b05e-154">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-154">Low</span></span>      |
| [<span data-ttu-id="2b05e-155">Ahora, las propiedades heredadas de tipos sin asignar se asignan a una única columna para todos los tipos derivados</span><span class="sxs-lookup"><span data-stu-id="2b05e-155">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="2b05e-156">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-156">Low</span></span>      |
| [<span data-ttu-id="2b05e-157">La convención de propiedad de clave externa ya no coincide con el mismo nombre que la propiedad de entidad de seguridad</span><span class="sxs-lookup"><span data-stu-id="2b05e-157">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="2b05e-158">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-158">Low</span></span>      |
| [<span data-ttu-id="2b05e-159">Ahora, la conexión de base de datos se cierra si ya no se usa antes de que se complete TransactionScope</span><span class="sxs-lookup"><span data-stu-id="2b05e-159">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="2b05e-160">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-160">Low</span></span>      |
| [<span data-ttu-id="2b05e-161">Los campos de respaldo se usan de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="2b05e-161">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="2b05e-162">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-162">Low</span></span>      |
| [<span data-ttu-id="2b05e-163">Inicio de excepciones si se encuentran varios campos de respaldo compatibles</span><span class="sxs-lookup"><span data-stu-id="2b05e-163">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="2b05e-164">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-164">Low</span></span>      |
| [<span data-ttu-id="2b05e-165">Los nombres de propiedades de solo campo deben coincidir con el nombre del campo</span><span class="sxs-lookup"><span data-stu-id="2b05e-165">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="2b05e-166">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-166">Low</span></span>      |
| [<span data-ttu-id="2b05e-167">AddDbContext/AddDbContextPool ya no llaman a AddLogging ni a AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="2b05e-167">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="2b05e-168">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-168">Low</span></span>      |
| [<span data-ttu-id="2b05e-169">AddEntityFramework\* agrega IMemoryCache con un límite de tamaño</span><span class="sxs-lookup"><span data-stu-id="2b05e-169">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="2b05e-170">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-170">Low</span></span>      |
| [<span data-ttu-id="2b05e-171">Ahora DbContext.Entry realiza una operación DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="2b05e-171">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="2b05e-172">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-172">Low</span></span>      |
| [<span data-ttu-id="2b05e-173">El cliente no genera las claves de matriz de cadena y byte de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="2b05e-173">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="2b05e-174">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-174">Low</span></span>      |
| [<span data-ttu-id="2b05e-175">Ahora ILoggerFactory es un servicio con ámbito</span><span class="sxs-lookup"><span data-stu-id="2b05e-175">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="2b05e-176">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-176">Low</span></span>      |
| [<span data-ttu-id="2b05e-177">En los proxies de carga diferida ya no se supone que las propiedades de navegación están totalmente cargadas</span><span class="sxs-lookup"><span data-stu-id="2b05e-177">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="2b05e-178">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-178">Low</span></span>      |
| [<span data-ttu-id="2b05e-179">La creación excesiva de proveedores de servicios internos ahora es un error de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="2b05e-179">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="2b05e-180">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-180">Low</span></span>      |
| [<span data-ttu-id="2b05e-181">Comportamiento nuevo de HasOne/HasMany llamado con una sola cadena</span><span class="sxs-lookup"><span data-stu-id="2b05e-181">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="2b05e-182">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-182">Low</span></span>      |
| [<span data-ttu-id="2b05e-183">El tipo de valor devuelto para varios métodos asincrónicos se ha cambiado de Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="2b05e-183">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="2b05e-184">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-184">Low</span></span>      |
| [<span data-ttu-id="2b05e-185">La anotación Relational:TypeMapping ahora es simplemente TypeMapping</span><span class="sxs-lookup"><span data-stu-id="2b05e-185">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="2b05e-186">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-186">Low</span></span>      |
| [<span data-ttu-id="2b05e-187">ToTable en un tipo derivado produce una excepción</span><span class="sxs-lookup"><span data-stu-id="2b05e-187">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="2b05e-188">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-188">Low</span></span>      |
| [<span data-ttu-id="2b05e-189">EF Core ya no envía pragma para el cumplimiento de SQLite FK</span><span class="sxs-lookup"><span data-stu-id="2b05e-189">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="2b05e-190">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-190">Low</span></span>      |
| [<span data-ttu-id="2b05e-191">Microsoft.EntityFrameworkCore.Sqlite ahora depende de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="2b05e-191">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="2b05e-192">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-192">Low</span></span>      |
| [<span data-ttu-id="2b05e-193">Los valores GUID se almacenan ahora como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="2b05e-193">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="2b05e-194">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-194">Low</span></span>      |
| [<span data-ttu-id="2b05e-195">Ahora los valores char se almacenan como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="2b05e-195">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="2b05e-196">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-196">Low</span></span>      |
| [<span data-ttu-id="2b05e-197">Ahora los id. de migración se generan usando el calendario de la referencia cultural invariable</span><span class="sxs-lookup"><span data-stu-id="2b05e-197">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="2b05e-198">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-198">Low</span></span>      |
| [<span data-ttu-id="2b05e-199">La información o los metadatos de la extensión se han quitado de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="2b05e-199">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="2b05e-200">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-200">Low</span></span>      |
| [<span data-ttu-id="2b05e-201">LogQueryPossibleExceptionWithAggregateOperator ha cambiado de nombre</span><span class="sxs-lookup"><span data-stu-id="2b05e-201">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="2b05e-202">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-202">Low</span></span>      |
| [<span data-ttu-id="2b05e-203">Clarificación de la API para nombres de restricciones de claves externas</span><span class="sxs-lookup"><span data-stu-id="2b05e-203">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="2b05e-204">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-204">Low</span></span>      |
| [<span data-ttu-id="2b05e-205">IRelationalDatabaseCreator.HasTables/HasTablesAsync se han hecho públicos</span><span class="sxs-lookup"><span data-stu-id="2b05e-205">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="2b05e-206">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-206">Low</span></span>      |
| [<span data-ttu-id="2b05e-207">Microsoft.EntityFrameworkCore.Design es ahora un paquete DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="2b05e-207">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="2b05e-208">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-208">Low</span></span>      |
| [<span data-ttu-id="2b05e-209">SQLitePCL.raw se ha actualizado a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="2b05e-209">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="2b05e-210">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-210">Low</span></span>      |
| [<span data-ttu-id="2b05e-211">NetTopologySuite se actualizó a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="2b05e-211">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="2b05e-212">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-212">Low</span></span>      |
| [<span data-ttu-id="2b05e-213">Se usa Microsoft.Data.SqlClient en lugar de System.Data.SqlClient</span><span class="sxs-lookup"><span data-stu-id="2b05e-213">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="2b05e-214">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-214">Low</span></span>      |
| [<span data-ttu-id="2b05e-215">Se deben configurar varias relaciones de referencia automática ambiguas</span><span class="sxs-lookup"><span data-stu-id="2b05e-215">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="2b05e-216">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-216">Low</span></span>      |
| [<span data-ttu-id="2b05e-217">DbFunction.Schema es NULL o la cadena vacía lo configura para estar en el esquema predeterminado del modelo</span><span class="sxs-lookup"><span data-stu-id="2b05e-217">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="2b05e-218">Bajo</span><span class="sxs-lookup"><span data-stu-id="2b05e-218">Low</span></span>      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="2b05e-219">Las consultas LINQ ya no se evalúan en el cliente</span><span class="sxs-lookup"><span data-stu-id="2b05e-219">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="2b05e-220">[Problema de seguimiento n.° 14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Consulte también el problema n.° 12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="2b05e-220">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="2b05e-221">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-221">**Old behavior**</span></span>

<span data-ttu-id="2b05e-222">Antes de 3.0, cuando en EF Core no se podía convertir una expresión que formaba parte de una consulta SQL o un parámetro, la expresión se evaluaba de forma automática en el cliente.</span><span class="sxs-lookup"><span data-stu-id="2b05e-222">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="2b05e-223">De forma predeterminada, la evaluación de cliente de expresiones potencialmente costosas solo desencadenaba una advertencia.</span><span class="sxs-lookup"><span data-stu-id="2b05e-223">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="2b05e-224">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-224">**New behavior**</span></span>

<span data-ttu-id="2b05e-225">A partir de 3.0, en EF Core solo se permite que se evalúen en el cliente las expresiones en la proyección de nivel superior (la última llamada a `Select()` de la consulta).</span><span class="sxs-lookup"><span data-stu-id="2b05e-225">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="2b05e-226">Cuando las expresiones de otra parte de la consulta no se pueden convertir en SQL o un parámetro, se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="2b05e-226">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="2b05e-227">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-227">**Why**</span></span>

<span data-ttu-id="2b05e-228">La evaluación de cliente automática de las consultas permite que se ejecuten muchas consultas incluso si no se pueden convertir elementos importantes de ellas.</span><span class="sxs-lookup"><span data-stu-id="2b05e-228">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="2b05e-229">Esto puede provocar un comportamiento inesperado y potencialmente dañino que es posible que solo sea evidente en entornos de producción.</span><span class="sxs-lookup"><span data-stu-id="2b05e-229">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="2b05e-230">Por ejemplo, una condición en una llamada a `Where()` que no se puede convertir puede provocar que todas las filas de la tabla se transfieran desde el servidor de base de datos y que el filtro se aplique en el cliente.</span><span class="sxs-lookup"><span data-stu-id="2b05e-230">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="2b05e-231">Esta situación puede pasar desapercibida fácilmente si la tabla solo contiene algunas filas en la fase de desarrollo, pero ser más grave cuando la aplicación pase a producción, donde la tabla puede contener millones de filas.</span><span class="sxs-lookup"><span data-stu-id="2b05e-231">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="2b05e-232">Las advertencias de evaluación de cliente también se suelen pasar por alto durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2b05e-232">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="2b05e-233">Además de esto, la evaluación de cliente automática puede causar problemas en los que la mejora de la traducción de consultas para expresiones específicas provocaba cambios importantes no deseados entre versiones.</span><span class="sxs-lookup"><span data-stu-id="2b05e-233">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="2b05e-234">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-234">**Mitigations**</span></span>

<span data-ttu-id="2b05e-235">Si una consulta no se puede traducir totalmente, vuelva a escribirla en un formato que se pueda traducir, o bien use `AsEnumerable()`, `ToList()` o una función similar para devolver los datos al cliente de forma explícita, donde después se puedan seguir procesando mediante LINQ to Objects.</span><span class="sxs-lookup"><span data-stu-id="2b05e-235">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a><span data-ttu-id="2b05e-236">EF Core 3.0 tiene como destino .NET Standard 2.1, y no .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="2b05e-236">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>

[<span data-ttu-id="2b05e-237">Problema de seguimiento n.º 15498</span><span class="sxs-lookup"><span data-stu-id="2b05e-237">Tracking Issue #15498</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

> [!IMPORTANT] 
> <span data-ttu-id="2b05e-238">EF Core 3.1 vuelve a tener como objetivo a .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="2b05e-238">EF Core 3.1 targets .NET Standard 2.0 again.</span></span> <span data-ttu-id="2b05e-239">Esto reincorpora la compatibilidad con .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="2b05e-239">This brings back support for .NET Framework.</span></span>

<span data-ttu-id="2b05e-240">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-240">**Old behavior**</span></span>

<span data-ttu-id="2b05e-241">Antes de la versión 3.0, EF Core tenía como destino .NET Standard 2.0 y se podía ejecutar en todas las plataformas que admitieran dicho estándar, incluido .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="2b05e-241">Before 3.0, EF Core targeted .NET Standard 2.0 and would run on all platforms that support that standard, including .NET Framework.</span></span>

<span data-ttu-id="2b05e-242">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-242">**New behavior**</span></span>

<span data-ttu-id="2b05e-243">A partir de la versión 3.0, EF Core tiene como destino .NET Standard 2.1 y se puede ejecutar en todas las plataformas que admitan dicho estándar.</span><span class="sxs-lookup"><span data-stu-id="2b05e-243">Starting with 3.0, EF Core targets .NET Standard 2.1 and will run on all platforms that support this standard.</span></span> <span data-ttu-id="2b05e-244">Esto no incluye .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="2b05e-244">This does not include .NET Framework.</span></span>

<span data-ttu-id="2b05e-245">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-245">**Why**</span></span>

<span data-ttu-id="2b05e-246">Esto forma parte de una decisión estratégica para todas las tecnologías de .NET que tiene como objetivo centrar los esfuerzos en .NET Core y otras plataformas modernas de .NET, como Xamarin.</span><span class="sxs-lookup"><span data-stu-id="2b05e-246">This is part of a strategic decision across .NET technologies to focus energy on .NET Core and other modern .NET platforms, such as Xamarin.</span></span>

<span data-ttu-id="2b05e-247">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-247">**Mitigations**</span></span>

<span data-ttu-id="2b05e-248">Use EF Core 3.1.</span><span class="sxs-lookup"><span data-stu-id="2b05e-248">Use EF Core 3.1.</span></span>

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="2b05e-249">Entity Framework Core ya no forma parte del marco compartido ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2b05e-249">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="2b05e-250">Anuncios del problema de seguimiento n.º 325</span><span class="sxs-lookup"><span data-stu-id="2b05e-250">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="2b05e-251">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-251">**Old behavior**</span></span>

<span data-ttu-id="2b05e-252">Antes de ASP.NET Core 3.0, cuando se agregaba una referencia de paquete a `Microsoft.AspNetCore.App` o `Microsoft.AspNetCore.All`, se incluía EF Core y algunos de los proveedores de datos de EF Core, como el de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="2b05e-252">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="2b05e-253">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-253">**New behavior**</span></span>

<span data-ttu-id="2b05e-254">A partir de la versión 3.0, el marco compartido ASP.NET Core no incluye EF Core ni ningún proveedor de datos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="2b05e-254">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="2b05e-255">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-255">**Why**</span></span>

<span data-ttu-id="2b05e-256">Antes de este cambio, para obtener EF Core se necesitaban varios pasos en función de si la aplicación se destinaba a ASP.NET Core y SQL Server o no.</span><span class="sxs-lookup"><span data-stu-id="2b05e-256">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="2b05e-257">Además, la actualización de ASP.NET Core forzaba la de EF Core y el proveedor de SQL Server, lo que no siempre es deseable.</span><span class="sxs-lookup"><span data-stu-id="2b05e-257">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="2b05e-258">Con este cambio, la experiencia de obtención de EF Core es la misma en todos los proveedores, implementaciones admitidas de .NET y tipos de aplicación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-258">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="2b05e-259">Ahora los desarrolladores también pueden controlar exactamente cuándo se actualizan EF Core y los proveedores de datos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="2b05e-259">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="2b05e-260">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-260">**Mitigations**</span></span>

<span data-ttu-id="2b05e-261">Para usar EF Core en una aplicación ASP.NET Core 3.0 o cualquier otra aplicación compatible, debe agregar de forma explícita una referencia de paquete al proveedor de base de datos de EF Core que se va a usar en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-261">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="2b05e-262">La herramienta de línea de comandos de EF Core, dotnet ef, ya no forma parte del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="2b05e-262">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="2b05e-263">Problema de seguimiento n.º 14016</span><span class="sxs-lookup"><span data-stu-id="2b05e-263">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="2b05e-264">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-264">**Old behavior**</span></span>

<span data-ttu-id="2b05e-265">Antes de 3.0, la herramienta `dotnet ef` se incluía en el SDK de .NET Core y estaba disponible para usarse desde la línea de comandos de cualquier proyecto sin necesidad de realizar pasos adicionales.</span><span class="sxs-lookup"><span data-stu-id="2b05e-265">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="2b05e-266">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-266">**New behavior**</span></span>

<span data-ttu-id="2b05e-267">A partir de la versión 3.0, el SDK de .NET no incluye la herramienta `dotnet ef`, por lo que antes de poder usarla tendrá que instalarla de forma explícita como una herramienta local o global.</span><span class="sxs-lookup"><span data-stu-id="2b05e-267">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="2b05e-268">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-268">**Why**</span></span>

<span data-ttu-id="2b05e-269">Este cambio nos permite distribuir y actualizar `dotnet ef` como una herramienta convencional de la CLI de .NET en NuGet, coherente con el hecho de que la versión 3.0 de EF Core también se distribuye siempre como un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="2b05e-269">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="2b05e-270">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-270">**Mitigations**</span></span>

<span data-ttu-id="2b05e-271">Para poder administrar las migraciones o aplicar scaffolding a `DbContext`, instale `dotnet-ef` como herramienta global:</span><span class="sxs-lookup"><span data-stu-id="2b05e-271">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

  ``` console
    $ dotnet tool install --global dotnet-ef
  ```

<span data-ttu-id="2b05e-272">También se puede obtener una herramienta local cuando se restauran las dependencias de un proyecto que la declara como una dependencia de herramientas mediante un [archivo de manifiesto de herramientas](https://github.com/dotnet/cli/issues/10288).</span><span class="sxs-lookup"><span data-stu-id="2b05e-272">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](https://github.com/dotnet/cli/issues/10288).</span></span>

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="2b05e-273">FromSql, ExecuteSql y ExecuteSqlAsync han cambiado de nombre</span><span class="sxs-lookup"><span data-stu-id="2b05e-273">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="2b05e-274">Problema de seguimiento n.º 10996</span><span class="sxs-lookup"><span data-stu-id="2b05e-274">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="2b05e-275">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-275">**Old behavior**</span></span>

<span data-ttu-id="2b05e-276">Antes de EF Core 3.0, estos nombres de métodos se sobrecargaban para funcionar tanto con una cadena normal como con una cadena que se debería interpolar en SQL y parámetros.</span><span class="sxs-lookup"><span data-stu-id="2b05e-276">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="2b05e-277">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-277">**New behavior**</span></span>

<span data-ttu-id="2b05e-278">A partir de la versión EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw` y `ExecuteSqlRawAsync` para crear una consulta con parámetros donde los parámetros se pasan por separado de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="2b05e-278">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="2b05e-279">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-279">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="2b05e-280">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated` y `ExecuteSqlInterpolatedAsync` para crear una consulta con parámetros donde los parámetros se pasan como parte de una cadena de consulta interpolada.</span><span class="sxs-lookup"><span data-stu-id="2b05e-280">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="2b05e-281">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-281">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="2b05e-282">Tenga en cuenta que las dos consultas anteriores producirán el mismo código SQL parametrizado con los mismos parámetros SQL.</span><span class="sxs-lookup"><span data-stu-id="2b05e-282">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="2b05e-283">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-283">**Why**</span></span>

<span data-ttu-id="2b05e-284">Las sobrecargas del método como esta facilitan las llamadas accidentales al método de cadena sin procesar cuando la intención era llamar al método de cadena interpolada y viceversa.</span><span class="sxs-lookup"><span data-stu-id="2b05e-284">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="2b05e-285">Esto podría resultar en consultas que no se parametrizan cuando deberían.</span><span class="sxs-lookup"><span data-stu-id="2b05e-285">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="2b05e-286">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-286">**Mitigations**</span></span>

<span data-ttu-id="2b05e-287">Haga el cambio para usar los nuevos nombres de métodos.</span><span class="sxs-lookup"><span data-stu-id="2b05e-287">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="2b05e-288">Cuando el método FromSql se usa con un procedimiento almacenado no se puede redactar</span><span class="sxs-lookup"><span data-stu-id="2b05e-288">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="2b05e-289">Problema de seguimiento n.° 15392</span><span class="sxs-lookup"><span data-stu-id="2b05e-289">Tracking Issue #15392</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

<span data-ttu-id="2b05e-290">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-290">**Old behavior**</span></span>

<span data-ttu-id="2b05e-291">Antes de EF Core 3.0, el método FromSql intentaba detectar si se podía redactar en el código SQL pasado.</span><span class="sxs-lookup"><span data-stu-id="2b05e-291">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="2b05e-292">Cuando el código SQL no se podía redactar, como un procedimiento almacenado, realizaba la evaluación de cliente.</span><span class="sxs-lookup"><span data-stu-id="2b05e-292">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="2b05e-293">La consulta siguiente funcionaba al ejecutar el procedimiento almacenado en el servidor y aplicar FirstOrDefault en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="2b05e-293">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

<span data-ttu-id="2b05e-294">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-294">**New behavior**</span></span>

<span data-ttu-id="2b05e-295">A partir de EF Core 3.0, EF Core no intentará analizar el código SQL.</span><span class="sxs-lookup"><span data-stu-id="2b05e-295">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="2b05e-296">Por tanto, si va a redactar después de FromSqlRaw/FromSqlInterpolated, EF Core redactará el código SQL generando una subconsulta.</span><span class="sxs-lookup"><span data-stu-id="2b05e-296">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="2b05e-297">Por tanto, si usa un procedimiento almacenado con la redacción, obtendrá una excepción de sintaxis de SQL no válida.</span><span class="sxs-lookup"><span data-stu-id="2b05e-297">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

<span data-ttu-id="2b05e-298">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-298">**Why**</span></span>

<span data-ttu-id="2b05e-299">EF Core 3.0 no admite la evaluación automática de cliente, ya que era propenso a errores, como se explica [aquí](#linq-queries-are-no-longer-evaluated-on-the-client).</span><span class="sxs-lookup"><span data-stu-id="2b05e-299">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

<span data-ttu-id="2b05e-300">**Mitigación**</span><span class="sxs-lookup"><span data-stu-id="2b05e-300">**Mitigation**</span></span>

<span data-ttu-id="2b05e-301">Si usa un procedimiento almacenado en FromSqlRaw/FromSqlInterpolated, sabe que no se puede redactar, por lo que puede agregar __AsEnumerable/AsAsyncEnumerable__ justo después de la llamada al método FromSql para evitar cualquier redacción en el lado servidor.</span><span class="sxs-lookup"><span data-stu-id="2b05e-301">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="2b05e-302">Solo se pueden especificar métodos de FromSql en raíces de consulta.</span><span class="sxs-lookup"><span data-stu-id="2b05e-302">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="2b05e-303">Problema de seguimiento n.° 15704</span><span class="sxs-lookup"><span data-stu-id="2b05e-303">Tracking Issue #15704</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

<span data-ttu-id="2b05e-304">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-304">**Old behavior**</span></span>

<span data-ttu-id="2b05e-305">Antes de EF Core 3.0, el método `FromSql` podía especificarse en cualquier lugar en la consulta.</span><span class="sxs-lookup"><span data-stu-id="2b05e-305">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="2b05e-306">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-306">**New behavior**</span></span>

<span data-ttu-id="2b05e-307">A partir de EF Core 3.0, los nuevos métodos `FromSqlRaw` y `FromSqlInterpolated` (que reemplazan a `FromSql`) solo pueden especificarse en las raíces de la consulta, es decir, directamente en `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-307">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="2b05e-308">Si intenta especificarlos en cualquier otro lugar se producirá un error de compilación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-308">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="2b05e-309">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-309">**Why**</span></span>

<span data-ttu-id="2b05e-310">La especificación de `FromSql` en cualquier otro lugar diferente de `DbSet` no tenía un significado o valor agregado, y podría causar ambigüedad en ciertos escenarios.</span><span class="sxs-lookup"><span data-stu-id="2b05e-310">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="2b05e-311">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-311">**Mitigations**</span></span>

<span data-ttu-id="2b05e-312">Las invocaciones de `FromSql` se deben mover para que estén directamente en el `DbSet` al que se aplican.</span><span class="sxs-lookup"><span data-stu-id="2b05e-312">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="2b05e-313">Las consultas sin seguimiento ya no realizan la resolución de la identidad</span><span class="sxs-lookup"><span data-stu-id="2b05e-313">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="2b05e-314">Problema de seguimiento n.º 13518</span><span class="sxs-lookup"><span data-stu-id="2b05e-314">Tracking Issue #13518</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

<span data-ttu-id="2b05e-315">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-315">**Old behavior**</span></span>

<span data-ttu-id="2b05e-316">Antes de EF Core 3.0, se usaba la misma instancia de la entidad para cada aparición de una entidad con un tipo e identificador determinados.</span><span class="sxs-lookup"><span data-stu-id="2b05e-316">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="2b05e-317">Este comportamiento coincide con el de las consultas de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="2b05e-317">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="2b05e-318">Fijémonos en esta consulta:</span><span class="sxs-lookup"><span data-stu-id="2b05e-318">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
<span data-ttu-id="2b05e-319">Esta consulta devolverá la misma instancia de `Category` para cada elemento `Product` asociado con la categoría determinada.</span><span class="sxs-lookup"><span data-stu-id="2b05e-319">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

<span data-ttu-id="2b05e-320">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-320">**New behavior**</span></span>

<span data-ttu-id="2b05e-321">A partir de EF Core 3.0, se crean distintas instancias de la entidad si se encuentra una entidad con un tipo e identificador determinados en varias ubicaciones del gráfico devuelto.</span><span class="sxs-lookup"><span data-stu-id="2b05e-321">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="2b05e-322">Por ejemplo, la consulta anterior ahora devolverá una nueva instancia de `Category` para cada elemento `Product` cuando haya dos productos asociados a la misma categoría.</span><span class="sxs-lookup"><span data-stu-id="2b05e-322">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

<span data-ttu-id="2b05e-323">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-323">**Why**</span></span>

<span data-ttu-id="2b05e-324">La resolución de las identidades (es decir, el hecho de determinar que una entidad tiene los mismos tipo e identificador que la entidad encontrada) agrega más rendimiento y sobrecarga de memoria.</span><span class="sxs-lookup"><span data-stu-id="2b05e-324">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="2b05e-325">Este enfoque suele ser contrario a por qué las consultas sin seguimiento se usan en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="2b05e-325">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="2b05e-326">Además, aunque la resolución de las identidades a veces puede resultar útil, no es necesaria si las entidades se van a serializar y enviar a un cliente, algo habitual para las consultas sin seguimiento.</span><span class="sxs-lookup"><span data-stu-id="2b05e-326">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

<span data-ttu-id="2b05e-327">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-327">**Mitigations**</span></span>

<span data-ttu-id="2b05e-328">Si se requiere la resolución de identidad, use una consulta de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="2b05e-328">Use a tracking query if identity resolution is required.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="2b05e-329">~~La ejecución de consultas se registra en el nivel de depuración~~ Revertido</span><span class="sxs-lookup"><span data-stu-id="2b05e-329">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="2b05e-330">Problema de seguimiento n.º 14523</span><span class="sxs-lookup"><span data-stu-id="2b05e-330">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="2b05e-331">Revertimos este cambio porque la nueva configuración de EF Core 3.0 permite a la aplicación especificar el nivel de registro para cualquier evento.</span><span class="sxs-lookup"><span data-stu-id="2b05e-331">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="2b05e-332">Por ejemplo, para cambiar el registro de SQL a `Debug`, configure el nivel de forma explícita en `OnConfiguring` o `AddDbContext`:</span><span class="sxs-lookup"><span data-stu-id="2b05e-332">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="2b05e-333">Los valores de clave temporal ya no se establecen en instancias de entidad</span><span class="sxs-lookup"><span data-stu-id="2b05e-333">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="2b05e-334">Problema de seguimiento n.º 12378</span><span class="sxs-lookup"><span data-stu-id="2b05e-334">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="2b05e-335">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-335">**Old behavior**</span></span>

<span data-ttu-id="2b05e-336">Antes de EF Core 3.0, los valores temporales se asignaban a todas las propiedades de clave para las que posteriormente la base de datos generaba un valor real.</span><span class="sxs-lookup"><span data-stu-id="2b05e-336">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="2b05e-337">Normalmente, estos valores temporales eran números negativos grandes.</span><span class="sxs-lookup"><span data-stu-id="2b05e-337">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="2b05e-338">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-338">**New behavior**</span></span>

<span data-ttu-id="2b05e-339">A partir de la versión 3.0, en EF Core se almacena el valor de clave temporal como parte de la información de seguimiento de la entidad y la propiedad clave en sí no se modifica.</span><span class="sxs-lookup"><span data-stu-id="2b05e-339">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="2b05e-340">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-340">**Why**</span></span>

<span data-ttu-id="2b05e-341">Este cambio se ha realizado para evitar que los valores de clave temporales se conviertan erróneamente en permanentes cuando una entidad de la que previamente una instancia de `DbContext` ha realizado el seguimiento se mueve a otra instancia de `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-341">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="2b05e-342">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-342">**Mitigations**</span></span>

<span data-ttu-id="2b05e-343">Las aplicaciones que asignan valores de clave principal a claves externas para crear asociaciones entre entidades pueden depender del comportamiento anterior si las claves principales son generadas por el almacén y pertenecen a entidades en el estado `Added`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-343">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="2b05e-344">Esto se puede evitar con las siguientes situaciones:</span><span class="sxs-lookup"><span data-stu-id="2b05e-344">This can be avoided by:</span></span>
* <span data-ttu-id="2b05e-345">No se usan claves generadas por el almacén.</span><span class="sxs-lookup"><span data-stu-id="2b05e-345">Not using store-generated keys.</span></span>
* <span data-ttu-id="2b05e-346">Se establecen propiedades de navegación para crear relaciones en lugar de establecer valores de clave externa.</span><span class="sxs-lookup"><span data-stu-id="2b05e-346">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="2b05e-347">Se obtienen los valores de clave temporal reales de la información de seguimiento de la entidad.</span><span class="sxs-lookup"><span data-stu-id="2b05e-347">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="2b05e-348">Por ejemplo, `context.Entry(blog).Property(e => e.Id).CurrentValue` devolverá el valor temporal aunque no se haya establecido `blog.Id`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-348">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="2b05e-349">DetectChanges respeta los valores de clave generados por el almacén</span><span class="sxs-lookup"><span data-stu-id="2b05e-349">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="2b05e-350">Problema de seguimiento n.º 14616</span><span class="sxs-lookup"><span data-stu-id="2b05e-350">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="2b05e-351">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-351">**Old behavior**</span></span>

<span data-ttu-id="2b05e-352">Antes de EF Core 3.0, se realizaba el seguimiento en el estado `Added` de las entidades sin seguimiento detectadas por `DetectChanges` y se insertaban como una fila nueva cuando se llamaba a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-352">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="2b05e-353">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-353">**New behavior**</span></span>

<span data-ttu-id="2b05e-354">A partir de EF Core 3.0, si una entidad usa valores de clave generados y se establece un valor de clave, se realizará el seguimiento de la entidad en el estado `Modified`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-354">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="2b05e-355">Esto significa que se supone que existe una fila para la entidad y que se actualizará cuando se llame a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-355">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="2b05e-356">Si no se establece el valor de clave, o bien si el tipo de entidad no usa claves generadas, se seguirá realizando el seguimiento de la entidad nueva como `Added` al igual que en las versiones anteriores.</span><span class="sxs-lookup"><span data-stu-id="2b05e-356">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="2b05e-357">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-357">**Why**</span></span>

<span data-ttu-id="2b05e-358">Este cambio se ha realizado para que sea más sencillo y coherente trabajar con gráficos de entidades desconectadas mientras se usan claves generadas por el almacén.</span><span class="sxs-lookup"><span data-stu-id="2b05e-358">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="2b05e-359">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-359">**Mitigations**</span></span>

<span data-ttu-id="2b05e-360">Este cambio puede interrumpir una aplicación si se configura un tipo de entidad para usar claves generadas, pero se establecen de forma explícita valores de clave para las instancias nuevas.</span><span class="sxs-lookup"><span data-stu-id="2b05e-360">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="2b05e-361">La solución consiste en configurar de forma explícita las propiedades de clave para que no usen valores generados.</span><span class="sxs-lookup"><span data-stu-id="2b05e-361">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="2b05e-362">Por ejemplo, con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="2b05e-362">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="2b05e-363">O bien con anotaciones de datos:</span><span class="sxs-lookup"><span data-stu-id="2b05e-363">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="2b05e-364">Las eliminaciones en cascada ahora se realizan inmediatamente de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="2b05e-364">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="2b05e-365">Problema de seguimiento n.º 10114</span><span class="sxs-lookup"><span data-stu-id="2b05e-365">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="2b05e-366">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-366">**Old behavior**</span></span>

<span data-ttu-id="2b05e-367">Antes de la versión 3.0, en EF Core no se aplicaban acciones en cascada (eliminación de entidades dependientes cuando se eliminaba una entidad de seguridad obligatoria o cuando se rompía la relación con una entidad de seguridad obligatoria) hasta que se llamaba a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="2b05e-367">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="2b05e-368">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-368">**New behavior**</span></span>

<span data-ttu-id="2b05e-369">A partir de 3.0, en EF Core las acciones en cascada se aplican en cuanto se detecta la condición desencadenadora.</span><span class="sxs-lookup"><span data-stu-id="2b05e-369">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="2b05e-370">Por ejemplo, como resultado de la llamada a `context.Remove()` para eliminar una entidad de seguridad, todos los dependientes obligatorios relacionados de los que se realiza el seguimiento también se establecen en `Deleted` inmediatamente.</span><span class="sxs-lookup"><span data-stu-id="2b05e-370">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="2b05e-371">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-371">**Why**</span></span>

<span data-ttu-id="2b05e-372">Este cambio se ha realizado para mejorar la experiencia en escenarios de auditoría y enlace de datos, donde es importante comprender qué entidades se van a eliminar _antes de llamar a_  `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-372">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="2b05e-373">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-373">**Mitigations**</span></span>

<span data-ttu-id="2b05e-374">El comportamiento anterior se puede restaurar mediante opciones de `context.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-374">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="2b05e-375">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-375">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="2b05e-376">La carga diligente de entidades relacionadas ahora se realiza en una sola consulta</span><span class="sxs-lookup"><span data-stu-id="2b05e-376">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="2b05e-377">Problema de seguimiento n.º 18022</span><span class="sxs-lookup"><span data-stu-id="2b05e-377">Tracking issue #18022</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

<span data-ttu-id="2b05e-378">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-378">**Old behavior**</span></span>

<span data-ttu-id="2b05e-379">Antes de la versión 3.0, la carga diligente de navegaciones de colección a través de operadores `Include` provocaba la generación de varias consultas en la base de datos relacional, una para cada tipo de entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="2b05e-379">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

<span data-ttu-id="2b05e-380">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-380">**New behavior**</span></span>

<span data-ttu-id="2b05e-381">A partir de la versión 3.0, EF Core genera una sola consulta con operadores JOIN en las bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="2b05e-381">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

<span data-ttu-id="2b05e-382">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-382">**Why**</span></span>

<span data-ttu-id="2b05e-383">La emisión de varias consultas para implementar una única consulta LINQ provocaba numerosos problemas, incluido el rendimiento negativo, ya que se necesitaban varios recorridos de ida y vuelta a la base de datos, y problemas de coherencia de datos, ya que cada consulta podía observar un estado distinto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="2b05e-383">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

<span data-ttu-id="2b05e-384">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-384">**Mitigations**</span></span>

<span data-ttu-id="2b05e-385">Aunque técnicamente esto no es un cambio importante, podría tener un efecto considerable en el rendimiento de la aplicación cuando una sola consulta contiene un gran número de operadores `Include` en las navegaciones de la colección.</span><span class="sxs-lookup"><span data-stu-id="2b05e-385">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="2b05e-386">[Vea este comentario](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) para obtener más información y para volver a escribir las consultas de una manera más eficaz.</span><span class="sxs-lookup"><span data-stu-id="2b05e-386">[See this comment](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="2b05e-387">DeleteBehavior.Restrict tiene una semántica más limpia</span><span class="sxs-lookup"><span data-stu-id="2b05e-387">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="2b05e-388">Problema de seguimiento n.º 12661</span><span class="sxs-lookup"><span data-stu-id="2b05e-388">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="2b05e-389">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-389">**Old behavior**</span></span>

<span data-ttu-id="2b05e-390">Antes de la versión 3.0, `DeleteBehavior.Restrict` creaba claves externas en la base de datos con la semántica `Restrict`, pero también realizaba una corrección interna de manera no evidente.</span><span class="sxs-lookup"><span data-stu-id="2b05e-390">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="2b05e-391">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-391">**New behavior**</span></span>

<span data-ttu-id="2b05e-392">A partir de la versión 3.0, `DeleteBehavior.Restrict` garantiza que las claves externas se crean con la semántica `Restrict`, es decir, sin cascadas y realizando una infracción de restricción, sin llevar a cabo correcciones internas de EF.</span><span class="sxs-lookup"><span data-stu-id="2b05e-392">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="2b05e-393">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-393">**Why**</span></span>

<span data-ttu-id="2b05e-394">Este cambio se realizó para mejorar la experiencia de uso de `DeleteBehavior` de manera intuitiva sin efectos secundarios inesperados.</span><span class="sxs-lookup"><span data-stu-id="2b05e-394">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="2b05e-395">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-395">**Mitigations**</span></span>

<span data-ttu-id="2b05e-396">El comportamiento anterior se puede restaurar con `DeleteBehavior.ClientNoAction`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-396">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="2b05e-397">Los tipos de consulta se consolidan con tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="2b05e-397">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="2b05e-398">Problema de seguimiento n.º 14194</span><span class="sxs-lookup"><span data-stu-id="2b05e-398">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="2b05e-399">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-399">**Old behavior**</span></span>

<span data-ttu-id="2b05e-400">Antes de EF Core 3.0, los [tipos de consulta](xref:core/modeling/keyless-entity-types) eran un medio para consultar los datos que no definen una clave principal de una manera estructurada.</span><span class="sxs-lookup"><span data-stu-id="2b05e-400">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="2b05e-401">Es decir, un tipo de consulta se usaba para asignar tipos de entidad sin claves (más probablemente desde una vista, pero posiblemente desde una tabla), mientras que un tipo de entidad estándar se usaba cuando había una clave disponible (más probablemente desde una tabla, pero posiblemente desde una vista).</span><span class="sxs-lookup"><span data-stu-id="2b05e-401">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="2b05e-402">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-402">**New behavior**</span></span>

<span data-ttu-id="2b05e-403">Ahora un tipo de consulta se convierte en un tipo de entidad sin una clave principal.</span><span class="sxs-lookup"><span data-stu-id="2b05e-403">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="2b05e-404">Los tipos de entidad sin clave tienen la misma funcionalidad que los tipos de consulta de las versiones anteriores.</span><span class="sxs-lookup"><span data-stu-id="2b05e-404">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="2b05e-405">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-405">**Why**</span></span>

<span data-ttu-id="2b05e-406">Este cambio se ha realizado para reducir la confusión en torno a la finalidad de los tipos de consulta.</span><span class="sxs-lookup"><span data-stu-id="2b05e-406">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="2b05e-407">En concreto, son tipos de entidad sin clave y, por ello, intrínsecamente son de solo lectura, pero no se deben usar solo porque un tipo de entidad tenga que ser de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="2b05e-407">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="2b05e-408">Del mismo modo, se suelen asignar a vistas, pero solo porque las vistas no suelen definir claves.</span><span class="sxs-lookup"><span data-stu-id="2b05e-408">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="2b05e-409">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-409">**Mitigations**</span></span>

<span data-ttu-id="2b05e-410">Los elementos siguientes de la API ahora están obsoletos:</span><span class="sxs-lookup"><span data-stu-id="2b05e-410">The following parts of the API are now obsolete:</span></span>

* <span data-ttu-id="2b05e-411">**`ModelBuilder.Query<>()`** : en su lugar es necesario llamar a `ModelBuilder.Entity<>().HasNoKey()` para marcar un tipo de entidad como sin claves.</span><span class="sxs-lookup"><span data-stu-id="2b05e-411">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="2b05e-412">Esto todavía no se configurará por convención para evitar una configuración incorrecta cuando se espera una clave principal, pero no coincide con la convención.</span><span class="sxs-lookup"><span data-stu-id="2b05e-412">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="2b05e-413">**`DbQuery<>`** : en su lugar se debe usar `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-413">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="2b05e-414">**`DbContext.Query<>()`** : en su lugar se debe usar `DbContext.Set<>()`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-414">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>
* <span data-ttu-id="2b05e-415">**`IQueryTypeConfiguration<TQuery>`** : en su lugar se debe usar `IEntityTypeConfiguration<TEntity>`\*\*.</span><span class="sxs-lookup"><span data-stu-id="2b05e-415">**`IQueryTypeConfiguration<TQuery>`** - Instead `IEntityTypeConfiguration<TEntity>`\*\* should be used.</span></span>

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="2b05e-416">La API de configuración para las relaciones de tipo de propiedad ha cambiado</span><span class="sxs-lookup"><span data-stu-id="2b05e-416">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="2b05e-417">[Problema de seguimiento n.º 12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Problema de seguimiento n.º 9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Problema de seguimiento n.º 14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="2b05e-417">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="2b05e-418">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-418">**Old behavior**</span></span>

<span data-ttu-id="2b05e-419">Antes de EF Core 3.0, la configuración de la relación de propiedad se realizaba directamente después de la llamada a `OwnsOne` o `OwnsMany`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-419">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="2b05e-420">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-420">**New behavior**</span></span>

<span data-ttu-id="2b05e-421">A partir de EF Core 3.0, ahora hay una API fluida para configurar una propiedad de navegación para el propietario mediante `WithOwner()`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-421">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="2b05e-422">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-422">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="2b05e-423">La configuración relacionada con la relación entre el propietario y lo que se posee ahora se debe encadenar después de `WithOwner()`, de forma similar a cómo se configuran otras relaciones.</span><span class="sxs-lookup"><span data-stu-id="2b05e-423">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="2b05e-424">Pero la configuración del propio tipo de propiedad se seguirá encadenando después de `OwnsOne()/OwnsMany()`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-424">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="2b05e-425">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-425">For example:</span></span>

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

<span data-ttu-id="2b05e-426">Además, la llamada a `Entity()`, `HasOne()` o `Set()` con un tipo de propiedad de destino ahora iniciará una excepción.</span><span class="sxs-lookup"><span data-stu-id="2b05e-426">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="2b05e-427">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-427">**Why**</span></span>

<span data-ttu-id="2b05e-428">Este cambio se ha realizado para crear una separación más clara entre la configuración del propio tipo de propiedad y la _relación con_ el tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="2b05e-428">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="2b05e-429">A su vez, esto elimina la ambigüedad y la confusión de métodos como `HasForeignKey`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-429">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="2b05e-430">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-430">**Mitigations**</span></span>

<span data-ttu-id="2b05e-431">Cambie la configuración de las relaciones de tipo de propiedad para usar la nueva superficie de API, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="2b05e-431">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="2b05e-432">Ahora, las entidades dependientes que comparten la tabla con la entidad de seguridad son opcionales</span><span class="sxs-lookup"><span data-stu-id="2b05e-432">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="2b05e-433">Problema de seguimiento n.º 9005</span><span class="sxs-lookup"><span data-stu-id="2b05e-433">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="2b05e-434">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-434">**Old behavior**</span></span>

<span data-ttu-id="2b05e-435">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2b05e-435">Consider the following model:</span></span>
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
<span data-ttu-id="2b05e-436">Antes de EF Core 3.0, si `OrderDetails` era propiedad de `Order` o estaba asignado explícitamente a la misma tabla, siempre era necesaria una instancia de `OrderDetails` al agregar un elemento `Order` nuevo.</span><span class="sxs-lookup"><span data-stu-id="2b05e-436">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="2b05e-437">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-437">**New behavior**</span></span>

<span data-ttu-id="2b05e-438">A partir de la versión 3.0, EF Core permite agregar `Order` sin `OrderDetails` y asigna todas las propiedades `OrderDetails` a excepción de la clave principal a columnas que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="2b05e-438">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="2b05e-439">Al realizar consultas, EF Core establece `OrderDetails` en `null` si ninguna de las propiedades necesarias tiene un valor o si no tiene propiedades necesarias más allá de la clave principal y todas las propiedades son `null`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-439">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="2b05e-440">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-440">**Mitigations**</span></span>

<span data-ttu-id="2b05e-441">Si el modelo tiene una tabla que comparte dependencias con todas las columnas opcionales, pero la navegación que apunta a ella no se espera que sea `null`, la aplicación debería modificarse para controlar los casos en los que la navegación sea `null`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-441">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="2b05e-442">Si esto no es posible, debería agregarse una propiedad necesaria al tipo de entidad o, al menos, una entidad debería tener un valor distinto a `null` asignado.</span><span class="sxs-lookup"><span data-stu-id="2b05e-442">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="2b05e-443">Todas las entidades que compartan una tabla con una columna de token de simultaneidad tienen que asignarla a una propiedad</span><span class="sxs-lookup"><span data-stu-id="2b05e-443">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="2b05e-444">Problema de seguimiento n.º 14154</span><span class="sxs-lookup"><span data-stu-id="2b05e-444">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="2b05e-445">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-445">**Old behavior**</span></span>

<span data-ttu-id="2b05e-446">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2b05e-446">Consider the following model:</span></span>
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
<span data-ttu-id="2b05e-447">Antes de EF Core 3.0, si `OrderDetails` era propiedad de `Order` o estaba asignado explícitamente a la misma tabla, si solo se actualizaba `OrderDetails`, no se actualizaba el valor `Version` en el cliente y se producía un error en la próxima actualización.</span><span class="sxs-lookup"><span data-stu-id="2b05e-447">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="2b05e-448">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-448">**New behavior**</span></span>

<span data-ttu-id="2b05e-449">A partir de la versión 3.0, EF Core propaga el nuevo valor `Version` en `Order` si posee `OrderDetails`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-449">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="2b05e-450">En caso contrario, se produce una excepción durante la validación del modelo.</span><span class="sxs-lookup"><span data-stu-id="2b05e-450">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="2b05e-451">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-451">**Why**</span></span>

<span data-ttu-id="2b05e-452">Este cambio se realizó para evitar un valor de token de simultaneidad obsoleto cuando solo se actualiza una de las entidades asignadas a la misma tabla.</span><span class="sxs-lookup"><span data-stu-id="2b05e-452">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="2b05e-453">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-453">**Mitigations**</span></span>

<span data-ttu-id="2b05e-454">Todas las entidades que comparten la tabla deben incluir una propiedad que se asigna a la columna del token de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="2b05e-454">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="2b05e-455">Es posible crear una en estado reemplazado:</span><span class="sxs-lookup"><span data-stu-id="2b05e-455">It's possible the create one in shadow-state:</span></span>
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="2b05e-456">Las entidades en propiedad no se pueden consultar sin el propietario mediante una consulta de seguimiento</span><span class="sxs-lookup"><span data-stu-id="2b05e-456">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="2b05e-457">Problema de seguimiento n.º 18876</span><span class="sxs-lookup"><span data-stu-id="2b05e-457">Tracking Issue #18876</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18876)

<span data-ttu-id="2b05e-458">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-458">**Old behavior**</span></span>

<span data-ttu-id="2b05e-459">Antes de EF Core 3.0, las entidades en propiedad se podían consultar como cualquier otra navegación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-459">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

<span data-ttu-id="2b05e-460">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-460">**New behavior**</span></span>

<span data-ttu-id="2b05e-461">A partir de la versión 3.0, EF Core iniciará una excepción si una consulta de seguimiento proyecta una entidad en propiedad sin el propietario.</span><span class="sxs-lookup"><span data-stu-id="2b05e-461">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

<span data-ttu-id="2b05e-462">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-462">**Why**</span></span>

<span data-ttu-id="2b05e-463">Las entidades en propiedad no se pueden manipular sin el propietario, por lo que en la mayoría de los casos es un error consultarlas de esta manera.</span><span class="sxs-lookup"><span data-stu-id="2b05e-463">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

<span data-ttu-id="2b05e-464">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-464">**Mitigations**</span></span>

<span data-ttu-id="2b05e-465">Si se debe realizar el seguimiento de la entidad en propiedad para modificarla de cualquier manera posterior, el propietario se debe incluir en la consulta.</span><span class="sxs-lookup"><span data-stu-id="2b05e-465">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="2b05e-466">De lo contrario, agregue una llamada a `AsNoTracking()`:</span><span class="sxs-lookup"><span data-stu-id="2b05e-466">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="2b05e-467">Ahora, las propiedades heredadas de tipos sin asignar se asignan a una única columna para todos los tipos derivados</span><span class="sxs-lookup"><span data-stu-id="2b05e-467">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="2b05e-468">Problema de seguimiento n.º 13998</span><span class="sxs-lookup"><span data-stu-id="2b05e-468">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="2b05e-469">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-469">**Old behavior**</span></span>

<span data-ttu-id="2b05e-470">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2b05e-470">Consider the following model:</span></span>
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

<span data-ttu-id="2b05e-471">Antes de EF Core 3.0, la propiedad `ShippingAddress` se asignaba a columnas distintas para `BulkOrder` y `Order` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2b05e-471">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="2b05e-472">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-472">**New behavior**</span></span>

<span data-ttu-id="2b05e-473">A partir de la versión3.0, EF Core solo crea una columna para `ShippingAddress`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-473">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="2b05e-474">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-474">**Why**</span></span>

<span data-ttu-id="2b05e-475">El comportamiento anterior no era el esperado.</span><span class="sxs-lookup"><span data-stu-id="2b05e-475">The old behavoir was unexpected.</span></span>

<span data-ttu-id="2b05e-476">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-476">**Mitigations**</span></span>

<span data-ttu-id="2b05e-477">Todavía se puede asignar explícitamente la propiedad a columnas separadas en los tipos derivados:</span><span class="sxs-lookup"><span data-stu-id="2b05e-477">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="2b05e-478">La convención de propiedad de clave externa ya no coincide con el mismo nombre que la propiedad de entidad de seguridad</span><span class="sxs-lookup"><span data-stu-id="2b05e-478">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="2b05e-479">Problema de seguimiento n.º 13274</span><span class="sxs-lookup"><span data-stu-id="2b05e-479">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="2b05e-480">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-480">**Old behavior**</span></span>

<span data-ttu-id="2b05e-481">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="2b05e-481">Consider the following model:</span></span>
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
<span data-ttu-id="2b05e-482">Antes de EF Core 3.0, se podía usar la propiedad `CustomerId` para la clave externa por convención.</span><span class="sxs-lookup"><span data-stu-id="2b05e-482">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="2b05e-483">Pero si `Order` es un tipo de propiedad, entonces esto convertiría también a `CustomerId` en la clave principal, algo que no suele ser lo esperado.</span><span class="sxs-lookup"><span data-stu-id="2b05e-483">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="2b05e-484">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-484">**New behavior**</span></span>

<span data-ttu-id="2b05e-485">A partir de la versión 3.0, EF Core no intenta usar las propiedades de claves externas por convención si tienen el mismo nombre que la propiedad de entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="2b05e-485">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="2b05e-486">Los patrones de nombre de tipo de entidad de seguridad concatenado con el nombre de propiedad de la entidad de seguridad y de nombre de navegación concatenado con el nombre de propiedad de la entidad de seguridad todavía se hacen coincidir.</span><span class="sxs-lookup"><span data-stu-id="2b05e-486">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="2b05e-487">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-487">For example:</span></span>

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

<span data-ttu-id="2b05e-488">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-488">**Why**</span></span>

<span data-ttu-id="2b05e-489">Este cambio se ha realizado para evitar definir erróneamente una propiedad de clave principal en el tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="2b05e-489">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="2b05e-490">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-490">**Mitigations**</span></span>

<span data-ttu-id="2b05e-491">Si la propiedad se ha diseñado para ser la clave externa y, por tanto, parte de la clave principal, se debe configurar explícitamente como tal.</span><span class="sxs-lookup"><span data-stu-id="2b05e-491">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="2b05e-492">Ahora, la conexión de base de datos se cierra si ya no se usa antes de que se complete TransactionScope</span><span class="sxs-lookup"><span data-stu-id="2b05e-492">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="2b05e-493">Problema de seguimiento n.º 14218</span><span class="sxs-lookup"><span data-stu-id="2b05e-493">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="2b05e-494">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-494">**Old behavior**</span></span>

<span data-ttu-id="2b05e-495">Antes de EF Core 3.0, si el contexto abría la conexión dentro de `TransactionScope`, la conexión permanecía abierta mientras el ámbito actual `TransactionScope` estuviese activo.</span><span class="sxs-lookup"><span data-stu-id="2b05e-495">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

<span data-ttu-id="2b05e-496">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-496">**New behavior**</span></span>

<span data-ttu-id="2b05e-497">A partir de la versión 3.0, EF Core cierra la conexión en cuanto se deja de usar.</span><span class="sxs-lookup"><span data-stu-id="2b05e-497">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="2b05e-498">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-498">**Why**</span></span>

<span data-ttu-id="2b05e-499">Este cambio permite usar varios contextos en el mismo ámbito `TransactionScope`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-499">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="2b05e-500">El comportamiento nuevo también coincide con el de EF6.</span><span class="sxs-lookup"><span data-stu-id="2b05e-500">The new behavior also matches EF6.</span></span>

<span data-ttu-id="2b05e-501">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-501">**Mitigations**</span></span>

<span data-ttu-id="2b05e-502">Si la conexión debe permanecer abierta, una llamada explícita a `OpenConnection()` asegurará que EF Core no la cierre de forma prematura:</span><span class="sxs-lookup"><span data-stu-id="2b05e-502">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="2b05e-503">Cada propiedad usa la generación de claves enteras en memoria independiente</span><span class="sxs-lookup"><span data-stu-id="2b05e-503">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="2b05e-504">Problema de seguimiento n.º 6872</span><span class="sxs-lookup"><span data-stu-id="2b05e-504">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="2b05e-505">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-505">**Old behavior**</span></span>

<span data-ttu-id="2b05e-506">Antes de EF Core 3.0, se usaba un generador de valores compartidos para todas las propiedades de clave entera en memoria.</span><span class="sxs-lookup"><span data-stu-id="2b05e-506">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="2b05e-507">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-507">**New behavior**</span></span>

<span data-ttu-id="2b05e-508">A partir de EF Core 3.0, cada propiedad de clave entera obtiene su propio generador de valores cuando se usa la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="2b05e-508">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="2b05e-509">Además, si se elimina la base de datos, la generación de claves se restablece para todas las tablas.</span><span class="sxs-lookup"><span data-stu-id="2b05e-509">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="2b05e-510">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-510">**Why**</span></span>

<span data-ttu-id="2b05e-511">Este cambio se ha realizado para alinear la generación de claves en memoria más estrechamente a la generación de claves de base de datos reales y para mejorar la capacidad para aislar las pruebas entre sí cuando se usa la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="2b05e-511">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="2b05e-512">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-512">**Mitigations**</span></span>

<span data-ttu-id="2b05e-513">Esto puede interrumpir una aplicación que se base en el establecimiento de valores de clave específicos en memoria.</span><span class="sxs-lookup"><span data-stu-id="2b05e-513">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="2b05e-514">En su lugar, considere la posibilidad de no depender de valores de clave específicos, o bien de actualizar para que coincida con el comportamiento nuevo.</span><span class="sxs-lookup"><span data-stu-id="2b05e-514">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="2b05e-515">Los campos de respaldo se usan de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="2b05e-515">Backing fields are used by default</span></span>

[<span data-ttu-id="2b05e-516">Problema de seguimiento n.º 12430</span><span class="sxs-lookup"><span data-stu-id="2b05e-516">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="2b05e-517">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-517">**Old behavior**</span></span>

<span data-ttu-id="2b05e-518">Antes de la versión 3.0, incluso si se conocía el campo de respaldo de una propiedad, de forma predeterminada en EF Core se leía y escribía el valor de propiedad mediante los métodos captadores y establecedores de propiedades.</span><span class="sxs-lookup"><span data-stu-id="2b05e-518">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="2b05e-519">La excepción era la ejecución de consultas, donde el campo de respaldo se establecía directamente si se conocía.</span><span class="sxs-lookup"><span data-stu-id="2b05e-519">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="2b05e-520">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-520">**New behavior**</span></span>

<span data-ttu-id="2b05e-521">A partir de EF Core 3.0, si se conoce el campo de respaldo para una propiedad, EF Core siempre la leerá y escribirá mediante el campo de respaldo.</span><span class="sxs-lookup"><span data-stu-id="2b05e-521">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="2b05e-522">Esto podría provocar una interrupción de la aplicación si depende de un comportamiento adicional codificado en los métodos captadores o establecedores.</span><span class="sxs-lookup"><span data-stu-id="2b05e-522">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="2b05e-523">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-523">**Why**</span></span>

<span data-ttu-id="2b05e-524">Este cambio se ha realizado para evitar que EF Core desencadene erróneamente lógica de negocios de forma predeterminada al realizar operaciones de base de datos que implican entidades.</span><span class="sxs-lookup"><span data-stu-id="2b05e-524">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="2b05e-525">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-525">**Mitigations**</span></span>

<span data-ttu-id="2b05e-526">El comportamiento anterior a la versión 3.0 se puede restaurar mediante la configuración del modo de acceso de propiedad en `ModelBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-526">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="2b05e-527">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-527">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="2b05e-528">Inicio de excepciones si se encuentran varios campos de respaldo compatibles</span><span class="sxs-lookup"><span data-stu-id="2b05e-528">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="2b05e-529">Problema de seguimiento n.º 12523</span><span class="sxs-lookup"><span data-stu-id="2b05e-529">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="2b05e-530">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-530">**Old behavior**</span></span>

<span data-ttu-id="2b05e-531">Antes de EF Core 3.0, si varios campos coincidían con las reglas para buscar el campo de respaldo de una propiedad, se elegía un campo según un orden de prioridad.</span><span class="sxs-lookup"><span data-stu-id="2b05e-531">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="2b05e-532">Esto podía producir que, en caso de ambigüedad, se usara el campo incorrecto.</span><span class="sxs-lookup"><span data-stu-id="2b05e-532">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="2b05e-533">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-533">**New behavior**</span></span>

<span data-ttu-id="2b05e-534">A partir de EF Core 3.0, si varios campos coinciden con la misma propiedad, se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="2b05e-534">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="2b05e-535">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-535">**Why**</span></span>

<span data-ttu-id="2b05e-536">Este cambio se ha realizado para evitar de forma silenciosa el uso de un campo con respecto a otro cuando solo uno puede ser correcto.</span><span class="sxs-lookup"><span data-stu-id="2b05e-536">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="2b05e-537">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-537">**Mitigations**</span></span>

<span data-ttu-id="2b05e-538">En las propiedades con campos de respaldo ambiguos se debe especificar de forma explícita el campo que se va usar.</span><span class="sxs-lookup"><span data-stu-id="2b05e-538">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="2b05e-539">Por ejemplo, con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="2b05e-539">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="2b05e-540">Los nombres de propiedades de solo campo deben coincidir con el nombre del campo</span><span class="sxs-lookup"><span data-stu-id="2b05e-540">Field-only property names should match the field name</span></span>

<span data-ttu-id="2b05e-541">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-541">**Old behavior**</span></span>

<span data-ttu-id="2b05e-542">Antes de EF Core 3.0, una propiedad podía especificarse con un valor de cadena y, si no había ninguna propiedad con ese nombre en el tipo .NET, EF Core intentaba hacerla coincidir con un campo mediante reglas de convención.</span><span class="sxs-lookup"><span data-stu-id="2b05e-542">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

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

<span data-ttu-id="2b05e-543">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-543">**New behavior**</span></span>

<span data-ttu-id="2b05e-544">A partir de EF Core 3.0, una propiedad de solo campo debe coincidir exactamente con el nombre del campo.</span><span class="sxs-lookup"><span data-stu-id="2b05e-544">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="2b05e-545">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-545">**Why**</span></span>

<span data-ttu-id="2b05e-546">Este cambio se realizó para evitar el uso del mismo campo para dos propiedades con nombres similares. También hace que las reglas de coincidencia para propiedades solo de campo sean las mismas que para las propiedades asignadas a propiedades CLR.</span><span class="sxs-lookup"><span data-stu-id="2b05e-546">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="2b05e-547">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-547">**Mitigations**</span></span>

<span data-ttu-id="2b05e-548">Las propiedades solo de campo deberían tener el mismo nombre que el campo al que están asignadas.</span><span class="sxs-lookup"><span data-stu-id="2b05e-548">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="2b05e-549">En una próxima versión de EF Core 3.0 tenemos planeado volver a habilitar la configuración explícita de un nombre de campo distinto al nombre de la propiedad (vea el problema [n.° 15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span><span class="sxs-lookup"><span data-stu-id="2b05e-549">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="2b05e-550">AddDbContext/AddDbContextPool ya no llaman a AddLogging ni a AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="2b05e-550">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="2b05e-551">Problema de seguimiento n.º 14756</span><span class="sxs-lookup"><span data-stu-id="2b05e-551">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="2b05e-552">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-552">**Old behavior**</span></span>

<span data-ttu-id="2b05e-553">Antes de EF Core 3.0, la llamada a `AddDbContext` o `AddDbContextPool` también podría registrar los servicios de almacenamiento en caché y de registro con inserción de dependencias a través de llamadas a [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) y [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="2b05e-553">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="2b05e-554">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-554">**New behavior**</span></span>

<span data-ttu-id="2b05e-555">A partir de EF Core 3.0, `AddDbContext` y `AddDbContextPool` ya no registrarán estos servicios con inserción de dependencias (DI).</span><span class="sxs-lookup"><span data-stu-id="2b05e-555">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="2b05e-556">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-556">**Why**</span></span>

<span data-ttu-id="2b05e-557">EF Core 3.0 no requiere que estos servicios estén en el contenedor de inserción de dependencias de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-557">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="2b05e-558">Pero si `ILoggerFactory` se registra en el contenedor de DI de la aplicación, EF Core lo empezará a usar de todos modos.</span><span class="sxs-lookup"><span data-stu-id="2b05e-558">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="2b05e-559">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-559">**Mitigations**</span></span>

<span data-ttu-id="2b05e-560">Si la aplicación necesita estos servicios, regístrelos de manera explícita con el contenedor de DI mediante [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) o [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="2b05e-560">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="2b05e-561">AddEntityFramework\* agrega IMemoryCache con un límite de tamaño</span><span class="sxs-lookup"><span data-stu-id="2b05e-561">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="2b05e-562">Problema de seguimiento n.º 12905</span><span class="sxs-lookup"><span data-stu-id="2b05e-562">Tracking Issue #12905</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12905)

<span data-ttu-id="2b05e-563">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-563">**Old behavior**</span></span>

<span data-ttu-id="2b05e-564">Antes de EF Core 3.0, la llamada a los métodos `AddEntityFramework*` también registraba los servicios de almacenamiento en caché de memoria con inserción de dependencias sin límite de tamaño.</span><span class="sxs-lookup"><span data-stu-id="2b05e-564">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

<span data-ttu-id="2b05e-565">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-565">**New behavior**</span></span>

<span data-ttu-id="2b05e-566">A partir de EF Core 3.0, `AddEntityFramework*` registrará un servicio IMemoryCache con un límite de tamaño.</span><span class="sxs-lookup"><span data-stu-id="2b05e-566">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="2b05e-567">Si otros servicios agregados después dependen de IMemoryCache, pueden alcanzar rápidamente el límite predeterminado y provocar excepciones o un rendimiento degradado.</span><span class="sxs-lookup"><span data-stu-id="2b05e-567">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

<span data-ttu-id="2b05e-568">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-568">**Why**</span></span>

<span data-ttu-id="2b05e-569">El uso de IMemoryCache sin un límite podría dar lugar a un uso de memoria no controlado si hay un error en la lógica de almacenamiento en caché de las consultas o las consultas se generan de forma dinámica.</span><span class="sxs-lookup"><span data-stu-id="2b05e-569">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="2b05e-570">Tener un límite predeterminado mitiga un posible ataque DoS.</span><span class="sxs-lookup"><span data-stu-id="2b05e-570">Having a default limit mitigates a potential DoS attack.</span></span>

<span data-ttu-id="2b05e-571">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-571">**Mitigations**</span></span>

<span data-ttu-id="2b05e-572">En la mayoría de los casos, no es necesario llamar a `AddEntityFramework*` si también se llama a `AddDbContext` o `AddDbContextPool`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-572">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="2b05e-573">Por tanto, la mejor mitigación consiste en quitar la llamada a `AddEntityFramework*`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-573">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="2b05e-574">Si la aplicación necesita estos servicios, registre de forma explícita una implementación de IMemoryCache con el contenedor de DI por anticipado mediante [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="2b05e-574">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="2b05e-575">Ahora DbContext.Entry realiza una operación DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="2b05e-575">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="2b05e-576">Problema de seguimiento n.º 13552</span><span class="sxs-lookup"><span data-stu-id="2b05e-576">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="2b05e-577">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-577">**Old behavior**</span></span>

<span data-ttu-id="2b05e-578">Antes de EF Core 3.0, la llamada a `DbContext.Entry` provocaba que se detectaran cambios para todas las entidades con seguimiento.</span><span class="sxs-lookup"><span data-stu-id="2b05e-578">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="2b05e-579">Esto garantizaba que el estado expuesto en `EntityEntry` estuviera actualizado.</span><span class="sxs-lookup"><span data-stu-id="2b05e-579">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="2b05e-580">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-580">**New behavior**</span></span>

<span data-ttu-id="2b05e-581">A partir de EF Core 3.0, ahora la llamada a `DbContext.Entry` solo intenta detectar cambios en la entidad dada y cualquier entidad de seguridad relacionada con ella de la que se haya realizado el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="2b05e-581">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="2b05e-582">Esto significa que es posible que la llamada a este método no haya detectado otros cambios, lo que podría tener implicaciones en el estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-582">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="2b05e-583">Observe que si `ChangeTracker.AutoDetectChangesEnabled` se establece en `false` incluso esta detección de cambios local se deshabilitará.</span><span class="sxs-lookup"><span data-stu-id="2b05e-583">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="2b05e-584">Otros métodos que provocan la detección de cambios (como `ChangeTracker.Entries` y `SaveChanges`) siguen provocando una acción `DetectChanges` completa de todas las entidades de las que se realiza el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="2b05e-584">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="2b05e-585">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-585">**Why**</span></span>

<span data-ttu-id="2b05e-586">Este cambio se ha realizado para mejorar el rendimiento predeterminado del uso de `context.Entry`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-586">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="2b05e-587">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-587">**Mitigations**</span></span>

<span data-ttu-id="2b05e-588">Llame a `ChangeTracker.DetectChanges()` de forma explícita antes de llamar a `Entry` para garantizar el comportamiento anterior a la versión 3.0.</span><span class="sxs-lookup"><span data-stu-id="2b05e-588">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="2b05e-589">El cliente no genera las claves de matriz de cadena y byte de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="2b05e-589">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="2b05e-590">Problema de seguimiento n.º 14617</span><span class="sxs-lookup"><span data-stu-id="2b05e-590">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="2b05e-591">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-591">**Old behavior**</span></span>

<span data-ttu-id="2b05e-592">Antes de EF Core 3.0, se podían usar las propiedades de clave `string` y `byte[]` sin tener que establecer de forma explícita un valor distinto de NULL.</span><span class="sxs-lookup"><span data-stu-id="2b05e-592">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="2b05e-593">En ese caso, el valor de clave se generaba en el cliente como un GUID, que se serializaba en bytes para `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-593">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="2b05e-594">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-594">**New behavior**</span></span>

<span data-ttu-id="2b05e-595">A partir de EF Core 3.0, se iniciará una excepción en la que indica que no se ha establecido ningún valor de clave.</span><span class="sxs-lookup"><span data-stu-id="2b05e-595">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="2b05e-596">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-596">**Why**</span></span>

<span data-ttu-id="2b05e-597">Este cambio se ha realizado porque los valores `string`/`byte[]` generados por el cliente no suelen ser útiles, y el comportamiento predeterminado dificultaba razonar sobre los valores de clave generados de una forma habitual.</span><span class="sxs-lookup"><span data-stu-id="2b05e-597">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="2b05e-598">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-598">**Mitigations**</span></span>

<span data-ttu-id="2b05e-599">Se puede obtener el comportamiento anterior a la versión 3.0 si se especifica de forma explícita que las propiedades de clave deben usar los valores generados si no se establece ningún otro valor distinto de NULL.</span><span class="sxs-lookup"><span data-stu-id="2b05e-599">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="2b05e-600">Por ejemplo, con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="2b05e-600">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="2b05e-601">O bien con anotaciones de datos:</span><span class="sxs-lookup"><span data-stu-id="2b05e-601">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="2b05e-602">Ahora ILoggerFactory es un servicio con ámbito</span><span class="sxs-lookup"><span data-stu-id="2b05e-602">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="2b05e-603">Problema de seguimiento n.º 14698</span><span class="sxs-lookup"><span data-stu-id="2b05e-603">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="2b05e-604">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-604">**Old behavior**</span></span>

<span data-ttu-id="2b05e-605">Antes de EF Core 3.0, `ILoggerFactory` se registraba como un servicio de singleton.</span><span class="sxs-lookup"><span data-stu-id="2b05e-605">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="2b05e-606">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-606">**New behavior**</span></span>

<span data-ttu-id="2b05e-607">A partir de EF Core 3.0, `ILoggerFactory` ahora se registra como con ámbito.</span><span class="sxs-lookup"><span data-stu-id="2b05e-607">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="2b05e-608">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-608">**Why**</span></span>

<span data-ttu-id="2b05e-609">Este cambio se ha realizado para permitir la asociación de un registrador con una instancia de `DbContext`, lo que habilita otras funciones y quita algunos casos de comportamiento patológico como un aumento vertiginoso de los proveedores de servicios internos.</span><span class="sxs-lookup"><span data-stu-id="2b05e-609">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="2b05e-610">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-610">**Mitigations**</span></span>

<span data-ttu-id="2b05e-611">Este cambio no debería afectar al código de la aplicación a menos que registre y use servicios personalizados en el proveedor de servicios internos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="2b05e-611">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="2b05e-612">Esto no es habitual.</span><span class="sxs-lookup"><span data-stu-id="2b05e-612">This isn't common.</span></span>
<span data-ttu-id="2b05e-613">En estos casos, la mayoría de los elementos seguirá funcionando, pero cualquier servicio de singleton que dependiera de `ILoggerFactory` tendrá que cambiarse para obtener la interfaz `ILoggerFactory` de otra forma.</span><span class="sxs-lookup"><span data-stu-id="2b05e-613">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="2b05e-614">Si experimenta situaciones como esta, registre un problema en el [rastreador de problemas de GitHub de EF Core](https://github.com/aspnet/EntityFrameworkCore/issues) para hacernos saber cómo usa `ILoggerFactory`, para que podamos comprender mejor cómo evitar esta interrupción en el futuro.</span><span class="sxs-lookup"><span data-stu-id="2b05e-614">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="2b05e-615">En los proxies de carga diferida ya no se supone que las propiedades de navegación están totalmente cargadas</span><span class="sxs-lookup"><span data-stu-id="2b05e-615">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="2b05e-616">Problema de seguimiento n.º 12780</span><span class="sxs-lookup"><span data-stu-id="2b05e-616">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="2b05e-617">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-617">**Old behavior**</span></span>

<span data-ttu-id="2b05e-618">Antes de EF Core 3.0, una vez que se eliminaba `DbContext` no había ninguna forma de saber si una determinada propiedad de navegación de una entidad obtenida de ese contexto se había cargado completamente o no.</span><span class="sxs-lookup"><span data-stu-id="2b05e-618">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="2b05e-619">En su lugar, los proxies asumían que una navegación de referencia se cargaba si tenía un valor distinto de NULL, y que una navegación de colección se cargaba si no estaba vacía.</span><span class="sxs-lookup"><span data-stu-id="2b05e-619">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="2b05e-620">En estos casos, el intento de carga diferida era no operativo.</span><span class="sxs-lookup"><span data-stu-id="2b05e-620">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="2b05e-621">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-621">**New behavior**</span></span>

<span data-ttu-id="2b05e-622">A partir de EF Core 3.0, los proxies realizan el seguimiento de si una propiedad de navegación se carga o no.</span><span class="sxs-lookup"><span data-stu-id="2b05e-622">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="2b05e-623">Esto significa que el intento de acceder a una propiedad de navegación que se carga después de que se haya eliminado el contexto siempre será no operativo, incluso cuando la navegación cargada está vacía o es NULL.</span><span class="sxs-lookup"><span data-stu-id="2b05e-623">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="2b05e-624">Por el contrario, el intento de acceder a una propiedad de navegación que no está cargada iniciará una excepción si el contexto se ha eliminado, incluso si la propiedad de navegación es una colección no vacía.</span><span class="sxs-lookup"><span data-stu-id="2b05e-624">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="2b05e-625">Si se produce esta situación, significa que el código de aplicación está intentando usar la carga diferida en un momento no válido y que se debe cambiar la aplicación para que lo no haga.</span><span class="sxs-lookup"><span data-stu-id="2b05e-625">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="2b05e-626">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-626">**Why**</span></span>

<span data-ttu-id="2b05e-627">Este cambio se ha realizado para que el comportamiento sea coherente y correcto cuando se intenta la carga diferida de una instancia de `DbContext` eliminada.</span><span class="sxs-lookup"><span data-stu-id="2b05e-627">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="2b05e-628">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-628">**Mitigations**</span></span>

<span data-ttu-id="2b05e-629">Actualice el código de la aplicación para que no intente la carga diferida con un contexto eliminado, o bien configúrelo para que sea no operativo, como se describe en el mensaje de la excepción.</span><span class="sxs-lookup"><span data-stu-id="2b05e-629">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="2b05e-630">La creación excesiva de proveedores de servicios internos ahora es un error de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="2b05e-630">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="2b05e-631">Problema de seguimiento n.º 10236</span><span class="sxs-lookup"><span data-stu-id="2b05e-631">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="2b05e-632">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-632">**Old behavior**</span></span>

<span data-ttu-id="2b05e-633">Antes de EF Core 3.0, se registraba una advertencia para una aplicación que creaba un número patológico de proveedores de servicios internos.</span><span class="sxs-lookup"><span data-stu-id="2b05e-633">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="2b05e-634">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-634">**New behavior**</span></span>

<span data-ttu-id="2b05e-635">A partir de EF Core 3.0, ahora esta advertencia se considera un error y se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="2b05e-635">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="2b05e-636">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-636">**Why**</span></span>

<span data-ttu-id="2b05e-637">Este cambio se ha realizado para controlar mejor el código de la aplicación mediante la exposición de este caso patológico de una forma más explícita.</span><span class="sxs-lookup"><span data-stu-id="2b05e-637">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="2b05e-638">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-638">**Mitigations**</span></span>

<span data-ttu-id="2b05e-639">Cuando se produce este error, la acción más adecuada consiste en comprender la causa raíz y detener la creación de tantos proveedores de servicios internos.</span><span class="sxs-lookup"><span data-stu-id="2b05e-639">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="2b05e-640">Pero el error se puede convertir en una advertencia (u omitirse) mediante configuración en `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-640">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="2b05e-641">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-641">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="2b05e-642">Comportamiento nuevo de HasOne/HasMany llamado con una sola cadena</span><span class="sxs-lookup"><span data-stu-id="2b05e-642">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="2b05e-643">Problema de seguimiento n.º 9171</span><span class="sxs-lookup"><span data-stu-id="2b05e-643">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="2b05e-644">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-644">**Old behavior**</span></span>

<span data-ttu-id="2b05e-645">Antes de EF Core 3.0, el código para llamar a `HasOne` o `HasMany` con una cadena se interpretaba de manera confusa.</span><span class="sxs-lookup"><span data-stu-id="2b05e-645">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="2b05e-646">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-646">For example:</span></span>
```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="2b05e-647">El código parece relacionar `Samurai` con otro tipo de entidad mediante la propiedad de navegación `Entrance`, que puede ser privada.</span><span class="sxs-lookup"><span data-stu-id="2b05e-647">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="2b05e-648">En realidad, este código intenta crear una relación con algún tipo de entidad denominada `Entrance` sin ninguna propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-648">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="2b05e-649">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-649">**New behavior**</span></span>

<span data-ttu-id="2b05e-650">A partir de EF Core 3.0, el código anterior ahora hace lo que parecía que debía hacer antes.</span><span class="sxs-lookup"><span data-stu-id="2b05e-650">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="2b05e-651">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-651">**Why**</span></span>

<span data-ttu-id="2b05e-652">El comportamiento anterior era muy confuso, especialmente al leer el código de configuración y al buscar errores.</span><span class="sxs-lookup"><span data-stu-id="2b05e-652">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="2b05e-653">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-653">**Mitigations**</span></span>

<span data-ttu-id="2b05e-654">Esto solo interrumpirá las aplicaciones que configuran de manera explícita las relaciones con cadenas para nombres de tipos y sin especificar explícitamente la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-654">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="2b05e-655">Esto no es habitual.</span><span class="sxs-lookup"><span data-stu-id="2b05e-655">This is not common.</span></span>
<span data-ttu-id="2b05e-656">El comportamiento anterior se puede obtener al pasar de manera explícita `null` para el nombre de la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-656">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="2b05e-657">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-657">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="2b05e-658">El tipo de valor devuelto para varios métodos asincrónicos se ha cambiado de Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="2b05e-658">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="2b05e-659">Problema de seguimiento n.º 15184</span><span class="sxs-lookup"><span data-stu-id="2b05e-659">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="2b05e-660">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-660">**Old behavior**</span></span>

<span data-ttu-id="2b05e-661">Antes, los siguientes métodos asincrónicos devolvían `Task<T>`:</span><span class="sxs-lookup"><span data-stu-id="2b05e-661">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="2b05e-662">`ValueGenerator.NextValueAsync()` (y las clases derivadas)</span><span class="sxs-lookup"><span data-stu-id="2b05e-662">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="2b05e-663">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-663">**New behavior**</span></span>

<span data-ttu-id="2b05e-664">Dichos métodos ahora devuelven `ValueTask<T>` durante el mismo `T` que antes.</span><span class="sxs-lookup"><span data-stu-id="2b05e-664">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="2b05e-665">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-665">**Why**</span></span>

<span data-ttu-id="2b05e-666">Este cambio reduce el número de asignaciones de montones que se producen al invocar estos métodos, lo que mejora el rendimiento general.</span><span class="sxs-lookup"><span data-stu-id="2b05e-666">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="2b05e-667">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-667">**Mitigations**</span></span>

<span data-ttu-id="2b05e-668">Las aplicaciones que simplemente esperen las API anteriores solo necesitan recompilarse, sin que sea necesario realizar cambios en el código fuente.</span><span class="sxs-lookup"><span data-stu-id="2b05e-668">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="2b05e-669">Un uso más complejo (p. ej., pasar el valor `Task` devuelto a `Task.WhenAny()`) normalmente requiere que el valor `ValueTask<T>` devuelto se convierta en `Task<T>` mediante una llamada a `AsTask()` en él.</span><span class="sxs-lookup"><span data-stu-id="2b05e-669">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="2b05e-670">Tenga en cuenta que esto niega la reducción de asignación que implica este cambio.</span><span class="sxs-lookup"><span data-stu-id="2b05e-670">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="2b05e-671">La anotación Relational:TypeMapping ahora es simplemente TypeMapping</span><span class="sxs-lookup"><span data-stu-id="2b05e-671">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="2b05e-672">Problema de seguimiento n.º 9913</span><span class="sxs-lookup"><span data-stu-id="2b05e-672">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="2b05e-673">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-673">**Old behavior**</span></span>

<span data-ttu-id="2b05e-674">El nombre de anotación para las anotaciones de asignación de tipos era "Relational:TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="2b05e-674">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="2b05e-675">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-675">**New behavior**</span></span>

<span data-ttu-id="2b05e-676">Ahora, el nombre de anotación para las anotaciones de asignación de tipos es "TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="2b05e-676">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="2b05e-677">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-677">**Why**</span></span>

<span data-ttu-id="2b05e-678">Ahora, las asignaciones de tipos se usan para algo más que solo para proveedores de bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="2b05e-678">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="2b05e-679">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-679">**Mitigations**</span></span>

<span data-ttu-id="2b05e-680">Esto solo interrumpirá a las aplicaciones que acceden directamente a la asignación de tipos como una anotación, lo que no es habitual.</span><span class="sxs-lookup"><span data-stu-id="2b05e-680">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="2b05e-681">La acción más apropiada para corregir es usar la superficie de API para acceder a las asignaciones de tipos en lugar de usar directamente la anotación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-681">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="2b05e-682">ToTable en un tipo derivado inicia una excepción</span><span class="sxs-lookup"><span data-stu-id="2b05e-682">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="2b05e-683">Problema de seguimiento n.º 11811</span><span class="sxs-lookup"><span data-stu-id="2b05e-683">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="2b05e-684">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-684">**Old behavior**</span></span>

<span data-ttu-id="2b05e-685">Antes de EF Core 3.0, la llamada a `ToTable()` en un tipo derivado se omitía, ya que la única estrategia asignación de herencia era TPH, lo que no es válido.</span><span class="sxs-lookup"><span data-stu-id="2b05e-685">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="2b05e-686">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-686">**New behavior**</span></span>

<span data-ttu-id="2b05e-687">A partir de EF Core 3.0, y en preparación para agregar compatibilidad con TPT y TPC en una versión posterior, ahora la llamada a `ToTable()` en un tipo derivado iniciará una excepción para evitar un cambio de asignación inesperado en el futuro.</span><span class="sxs-lookup"><span data-stu-id="2b05e-687">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="2b05e-688">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-688">**Why**</span></span>

<span data-ttu-id="2b05e-689">En la actualidad no se considera válido asignar un tipo derivado a otra tabla.</span><span class="sxs-lookup"><span data-stu-id="2b05e-689">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="2b05e-690">Este cambio evita interrupciones en el futuro, cuando se convierta en una operación válida.</span><span class="sxs-lookup"><span data-stu-id="2b05e-690">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="2b05e-691">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-691">**Mitigations**</span></span>

<span data-ttu-id="2b05e-692">Quite todos los intentos de asignar tipos derivados a otras tablas.</span><span class="sxs-lookup"><span data-stu-id="2b05e-692">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="2b05e-693">ForSqlServerHasIndex se ha reemplazado por HasIndex</span><span class="sxs-lookup"><span data-stu-id="2b05e-693">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="2b05e-694">Problema de seguimiento n.º 12366</span><span class="sxs-lookup"><span data-stu-id="2b05e-694">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="2b05e-695">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-695">**Old behavior**</span></span>

<span data-ttu-id="2b05e-696">Antes de EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` proporcionaba una manera de configurar las columnas que se usaban con `INCLUDE`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-696">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="2b05e-697">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-697">**New behavior**</span></span>

<span data-ttu-id="2b05e-698">A partir de EF Core 3.0, ya se admite el uso de `Include` en un índice en el nivel relacional.</span><span class="sxs-lookup"><span data-stu-id="2b05e-698">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="2b05e-699">Use `HasIndex().ForSqlServerInclude()`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-699">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="2b05e-700">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-700">**Why**</span></span>

<span data-ttu-id="2b05e-701">Este cambio se ha realizado para consolidar la API para índices con `Include` en un mismo lugar para todos los proveedores de base de datos.</span><span class="sxs-lookup"><span data-stu-id="2b05e-701">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="2b05e-702">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-702">**Mitigations**</span></span>

<span data-ttu-id="2b05e-703">Use la API nueva, como se ha mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="2b05e-703">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="2b05e-704">Cambios en la API de metadatos</span><span class="sxs-lookup"><span data-stu-id="2b05e-704">Metadata API changes</span></span>

[<span data-ttu-id="2b05e-705">Problema de seguimiento n.º 214</span><span class="sxs-lookup"><span data-stu-id="2b05e-705">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="2b05e-706">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-706">**New behavior**</span></span>

<span data-ttu-id="2b05e-707">Las siguientes propiedades se han convertido en métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="2b05e-707">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="2b05e-708">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-708">**Why**</span></span>

<span data-ttu-id="2b05e-709">Este cambio simplifica la implementación de las interfaces mencionadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="2b05e-709">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="2b05e-710">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-710">**Mitigations**</span></span>

<span data-ttu-id="2b05e-711">Use los nuevos métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="2b05e-711">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="2b05e-712">Cambios en la API de metadatos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="2b05e-712">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="2b05e-713">Problema de seguimiento n.º 214</span><span class="sxs-lookup"><span data-stu-id="2b05e-713">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="2b05e-714">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-714">**New behavior**</span></span>

<span data-ttu-id="2b05e-715">Los métodos de extensión específicos del proveedor se simplificarán:</span><span class="sxs-lookup"><span data-stu-id="2b05e-715">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

<span data-ttu-id="2b05e-716">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-716">**Why**</span></span>

<span data-ttu-id="2b05e-717">Este cambio simplifica la implementación de los métodos de extensión mencionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="2b05e-717">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="2b05e-718">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-718">**Mitigations**</span></span>

<span data-ttu-id="2b05e-719">Use los nuevos métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="2b05e-719">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="2b05e-720">EF Core ya no envía pragma para el cumplimiento de SQLite FK</span><span class="sxs-lookup"><span data-stu-id="2b05e-720">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="2b05e-721">Problema de seguimiento n.º 12151</span><span class="sxs-lookup"><span data-stu-id="2b05e-721">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="2b05e-722">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-722">**Old behavior**</span></span>

<span data-ttu-id="2b05e-723">Antes de EF Core 3.0, EF Core enviaba `PRAGMA foreign_keys = 1` cuando se abría una conexión con SQLite.</span><span class="sxs-lookup"><span data-stu-id="2b05e-723">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="2b05e-724">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-724">**New behavior**</span></span>

<span data-ttu-id="2b05e-725">A partir de EF Core 3.0, EF Core ya no envía `PRAGMA foreign_keys = 1` cuando se abre una conexión con SQLite.</span><span class="sxs-lookup"><span data-stu-id="2b05e-725">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="2b05e-726">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-726">**Why**</span></span>

<span data-ttu-id="2b05e-727">Este cambio se ha realizado porque en EF Core se usa `SQLitePCLRaw.bundle_e_sqlite3` de forma predeterminada, lo que a su vez significa que el cumplimiento de CD está activado de forma predeterminada y no es necesario habilitarlo explícitamente cada vez que se abra una conexión.</span><span class="sxs-lookup"><span data-stu-id="2b05e-727">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="2b05e-728">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-728">**Mitigations**</span></span>

<span data-ttu-id="2b05e-729">Las claves externas se habilitan de forma predeterminada en SQLitePCLRaw.bundle_e_sqlite3, que en EF Core se usa de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="2b05e-729">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="2b05e-730">Para otros casos, las claves externas se pueden habilitar mediante la especificación de `Foreign Keys=True` en la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="2b05e-730">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="2b05e-731">Microsoft.EntityFrameworkCore.Sqlite ahora depende de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="2b05e-731">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="2b05e-732">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-732">**Old behavior**</span></span>

<span data-ttu-id="2b05e-733">Antes de EF Core 3.0, en EF Core se usaba `SQLitePCLRaw.bundle_green`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-733">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="2b05e-734">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-734">**New behavior**</span></span>

<span data-ttu-id="2b05e-735">A partir de EF Core 3.0, en EF Core se usa `SQLitePCLRaw.bundle_e_sqlite3`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-735">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="2b05e-736">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-736">**Why**</span></span>

<span data-ttu-id="2b05e-737">Este cambio se ha realizado para que la versión de SQLite que se usa en iOS sea coherente con otras plataformas.</span><span class="sxs-lookup"><span data-stu-id="2b05e-737">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="2b05e-738">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-738">**Mitigations**</span></span>

<span data-ttu-id="2b05e-739">Para usar la versión nativa de SQLite en iOS, configure `Microsoft.Data.Sqlite` para usar otra agrupación `SQLitePCLRaw`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-739">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="2b05e-740">Almacenamiento de valores GUID como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="2b05e-740">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="2b05e-741">Problema de seguimiento n.º 15078</span><span class="sxs-lookup"><span data-stu-id="2b05e-741">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="2b05e-742">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-742">**Old behavior**</span></span>

<span data-ttu-id="2b05e-743">Antes, los valores GUID se almacenaban como valores BLOB en SQLite.</span><span class="sxs-lookup"><span data-stu-id="2b05e-743">Guid values were previously stored as BLOB values on SQLite.</span></span>

<span data-ttu-id="2b05e-744">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-744">**New behavior**</span></span>

<span data-ttu-id="2b05e-745">Ahora, los valores GUID se almacenan como TEXT.</span><span class="sxs-lookup"><span data-stu-id="2b05e-745">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="2b05e-746">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-746">**Why**</span></span>

<span data-ttu-id="2b05e-747">El formato binario de los GUID no está normalizado.</span><span class="sxs-lookup"><span data-stu-id="2b05e-747">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="2b05e-748">El almacenamiento de los valores como TEXT mejora la compatibilidad de la base de datos con otras tecnologías.</span><span class="sxs-lookup"><span data-stu-id="2b05e-748">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="2b05e-749">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-749">**Mitigations**</span></span>

<span data-ttu-id="2b05e-750">Puede migrar las bases de datos existentes al nuevo formato ejecutando SQL de la siguiente forma.</span><span class="sxs-lookup"><span data-stu-id="2b05e-750">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

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

<span data-ttu-id="2b05e-751">En EF Core, también puede seguir usando el comportamiento anterior configurando un convertidor de valores en estas propiedades.</span><span class="sxs-lookup"><span data-stu-id="2b05e-751">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="2b05e-752">Microsoft.Data.Sqlite sigue siendo capaz de leer valores GUID de ambas columnas BLOB y TEXT. Sin embargo, dado que el formato predeterminado de los parámetros y las constantes ha cambiado, seguramente deberá realizar alguna acción en la mayoría de casos que impliquen el uso de valores GUID.</span><span class="sxs-lookup"><span data-stu-id="2b05e-752">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="2b05e-753">Ahora los valores char se almacenan como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="2b05e-753">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="2b05e-754">Problema de seguimiento n.º 15020</span><span class="sxs-lookup"><span data-stu-id="2b05e-754">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="2b05e-755">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-755">**Old behavior**</span></span>

<span data-ttu-id="2b05e-756">Anteriormente los valores char se almacenaban como valores INTEGER en SQLite.</span><span class="sxs-lookup"><span data-stu-id="2b05e-756">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="2b05e-757">Por ejemplo, un valor char de *A* se almacenaba como el valor entero 65.</span><span class="sxs-lookup"><span data-stu-id="2b05e-757">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="2b05e-758">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-758">**New behavior**</span></span>

<span data-ttu-id="2b05e-759">Ahora, los valores char se almacenan como TEXT.</span><span class="sxs-lookup"><span data-stu-id="2b05e-759">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="2b05e-760">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-760">**Why**</span></span>

<span data-ttu-id="2b05e-761">El almacenamiento de valores como TEXT es más natural y mejora la compatibilidad de la base de datos con otras tecnologías.</span><span class="sxs-lookup"><span data-stu-id="2b05e-761">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="2b05e-762">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-762">**Mitigations**</span></span>

<span data-ttu-id="2b05e-763">Puede migrar las bases de datos existentes al nuevo formato ejecutando SQL de la siguiente forma.</span><span class="sxs-lookup"><span data-stu-id="2b05e-763">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="2b05e-764">En EF Core, también puede seguir usando el comportamiento anterior configurando un convertidor de valores en estas propiedades.</span><span class="sxs-lookup"><span data-stu-id="2b05e-764">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="2b05e-765">Microsoft.Data.Sqlite también puede leer valores de caracteres tanto de columnas INTEGER como de columnas TEXT, por lo que es posible que no deba hacer nada dependiendo de su caso.</span><span class="sxs-lookup"><span data-stu-id="2b05e-765">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="2b05e-766">Ahora los id. de migración se generan usando el calendario de la referencia cultural invariable</span><span class="sxs-lookup"><span data-stu-id="2b05e-766">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="2b05e-767">Problema de seguimiento n.º 12978</span><span class="sxs-lookup"><span data-stu-id="2b05e-767">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="2b05e-768">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-768">**Old behavior**</span></span>

<span data-ttu-id="2b05e-769">Los identificadores de migración se generaban de forma involuntaria con el calendario de la referencia cultural actual.</span><span class="sxs-lookup"><span data-stu-id="2b05e-769">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="2b05e-770">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-770">**New behavior**</span></span>

<span data-ttu-id="2b05e-771">Ahora los id. de migración siempre se generan usando el calendario de la referencia cultural invariable (gregoriano).</span><span class="sxs-lookup"><span data-stu-id="2b05e-771">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="2b05e-772">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-772">**Why**</span></span>

<span data-ttu-id="2b05e-773">El orden de las migraciones es importante al actualizar la base de datos o al solucionar conflictos de combinación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-773">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="2b05e-774">Al usar el calendario invariable, se evitan problemas de ordenación que pueden producirse si los miembros del equipo tienen distintos calendarios del sistema.</span><span class="sxs-lookup"><span data-stu-id="2b05e-774">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="2b05e-775">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-775">**Mitigations**</span></span>

<span data-ttu-id="2b05e-776">Esta cambio afecta a todas las personas que usan un calendario no gregoriano en el que el año sea superior al del calendario gregoriano (como el calendario budista tailandés).</span><span class="sxs-lookup"><span data-stu-id="2b05e-776">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="2b05e-777">Los id. de migración existentes deberán actualizarse para que las migraciones nuevas se ordenen después de las existentes.</span><span class="sxs-lookup"><span data-stu-id="2b05e-777">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="2b05e-778">Puede ver el id. de migración en el atributo Migration de los archivos de diseñador de la migración.</span><span class="sxs-lookup"><span data-stu-id="2b05e-778">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="2b05e-779">También debe actualizarse la tabla de historial de migraciones.</span><span class="sxs-lookup"><span data-stu-id="2b05e-779">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="2b05e-780">Se ha quitado el elemento UseRowNumberForPaging</span><span class="sxs-lookup"><span data-stu-id="2b05e-780">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="2b05e-781">Problema de seguimiento n.º 16400</span><span class="sxs-lookup"><span data-stu-id="2b05e-781">Tracking Issue #16400</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

<span data-ttu-id="2b05e-782">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-782">**Old behavior**</span></span>

<span data-ttu-id="2b05e-783">Antes de EF Core 3.0, `UseRowNumberForPaging` se podía usar para generar SQL para la paginación de forma que fuera compatible con SQL Server 2008.</span><span class="sxs-lookup"><span data-stu-id="2b05e-783">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

<span data-ttu-id="2b05e-784">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-784">**New behavior**</span></span>

<span data-ttu-id="2b05e-785">A partir de EF Core 3.0, EF solo genera SQL para la paginación que únicamente es compatible con las versiones posteriores de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="2b05e-785">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span> 

<span data-ttu-id="2b05e-786">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-786">**Why**</span></span>

<span data-ttu-id="2b05e-787">El motivo de este cambio es que [SQL Server 2008 ya no se admite](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/). Además, la actualización de esta característica para que funcionase con los cambios en las consultas implementados en EF Core 3.0 llevaría mucho trabajo.</span><span class="sxs-lookup"><span data-stu-id="2b05e-787">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

<span data-ttu-id="2b05e-788">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-788">**Mitigations**</span></span>

<span data-ttu-id="2b05e-789">Se recomienda actualizar a una versión más reciente de SQL Server, o bien utilizar un nivel de compatibilidad superior, de modo que el SQL que se genere se admita.</span><span class="sxs-lookup"><span data-stu-id="2b05e-789">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="2b05e-790">Dicho esto, si no puede hacerlo, [escriba un comentario en el problema de seguimiento](https://github.com/aspnet/EntityFrameworkCore/issues/16400) con los detalles al respecto.</span><span class="sxs-lookup"><span data-stu-id="2b05e-790">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) with details.</span></span> <span data-ttu-id="2b05e-791">En función de los comentarios, es posible que volvamos a valorar esta decisión.</span><span class="sxs-lookup"><span data-stu-id="2b05e-791">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="2b05e-792">La información o metadatos de la extensión se han quitado de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="2b05e-792">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="2b05e-793">Problema de seguimiento n.º 16119</span><span class="sxs-lookup"><span data-stu-id="2b05e-793">Tracking Issue #16119</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

<span data-ttu-id="2b05e-794">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-794">**Old behavior**</span></span>

<span data-ttu-id="2b05e-795">`IDbContextOptionsExtension` incluía métodos para proporcionar metadatos sobre la extensión.</span><span class="sxs-lookup"><span data-stu-id="2b05e-795">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

<span data-ttu-id="2b05e-796">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-796">**New behavior**</span></span>

<span data-ttu-id="2b05e-797">Estos métodos se han movido a una nueva clase base abstracta `DbContextOptionsExtensionInfo`, que se devuelve desde una nueva propiedad `IDbContextOptionsExtension.Info`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-797">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

<span data-ttu-id="2b05e-798">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-798">**Why**</span></span>

<span data-ttu-id="2b05e-799">Al lanzarse las versiones 2.0 y 3.0, tuvimos que agregar o cambiar estos métodos varias veces.</span><span class="sxs-lookup"><span data-stu-id="2b05e-799">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="2b05e-800">Su división en una nueva clase base abstracta facilitará la realización de este tipo de cambios sin interrumpir las extensiones existentes.</span><span class="sxs-lookup"><span data-stu-id="2b05e-800">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

<span data-ttu-id="2b05e-801">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-801">**Mitigations**</span></span>

<span data-ttu-id="2b05e-802">Actualice las extensiones para seguir el nuevo patrón.</span><span class="sxs-lookup"><span data-stu-id="2b05e-802">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="2b05e-803">Encontrará ejemplos en las muchas implementaciones de `IDbContextOptionsExtension` para los diferentes tipos de extensiones en el código fuente de EF Core.</span><span class="sxs-lookup"><span data-stu-id="2b05e-803">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="2b05e-804">Cambio de nombre de LogQueryPossibleExceptionWithAggregateOperator</span><span class="sxs-lookup"><span data-stu-id="2b05e-804">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="2b05e-805">Problema de seguimiento n.º 10985</span><span class="sxs-lookup"><span data-stu-id="2b05e-805">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="2b05e-806">**Cambio**</span><span class="sxs-lookup"><span data-stu-id="2b05e-806">**Change**</span></span>

<span data-ttu-id="2b05e-807">Se ha cambiado el nombre de `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` a `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span><span class="sxs-lookup"><span data-stu-id="2b05e-807">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="2b05e-808">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-808">**Why**</span></span>

<span data-ttu-id="2b05e-809">Conviene alinear el nombre de este evento de advertencia con el del resto de eventos de advertencia.</span><span class="sxs-lookup"><span data-stu-id="2b05e-809">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="2b05e-810">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-810">**Mitigations**</span></span>

<span data-ttu-id="2b05e-811">Use el nuevo nombre.</span><span class="sxs-lookup"><span data-stu-id="2b05e-811">Use the new name.</span></span> <span data-ttu-id="2b05e-812">(Tenga en cuenta que el número de id. evento sigue siendo el mismo).</span><span class="sxs-lookup"><span data-stu-id="2b05e-812">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="2b05e-813">Clarificación de la API para nombres de restricciones de claves externas</span><span class="sxs-lookup"><span data-stu-id="2b05e-813">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="2b05e-814">Problema de seguimiento n.º 10730</span><span class="sxs-lookup"><span data-stu-id="2b05e-814">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="2b05e-815">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-815">**Old behavior**</span></span>

<span data-ttu-id="2b05e-816">Antes de EF Core 3.0, se utilizaba simplemente el término "nombre" para hacer referencia a los nombres de las restricciones de claves externas.</span><span class="sxs-lookup"><span data-stu-id="2b05e-816">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="2b05e-817">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-817">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="2b05e-818">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-818">**New behavior**</span></span>

<span data-ttu-id="2b05e-819">A partir de EF Core 3.0, el término con el que se hace referencia a los nombres de las restricciones de claves externas es "nombre de la restricción".</span><span class="sxs-lookup"><span data-stu-id="2b05e-819">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="2b05e-820">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-820">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="2b05e-821">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-821">**Why**</span></span>

<span data-ttu-id="2b05e-822">Este cambio permite mejorar la coherencia relativa a la nomenclatura en este aspecto y aclarar que se trata del nombre de una restricción de clave externa, y no del de la columna o propiedad en la que está definida la clave externa.</span><span class="sxs-lookup"><span data-stu-id="2b05e-822">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="2b05e-823">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-823">**Mitigations**</span></span>

<span data-ttu-id="2b05e-824">Use el nuevo nombre.</span><span class="sxs-lookup"><span data-stu-id="2b05e-824">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="2b05e-825">IRelationalDatabaseCreator.HasTables/HasTablesAsync se han hecho públicos</span><span class="sxs-lookup"><span data-stu-id="2b05e-825">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="2b05e-826">Problema de seguimiento n.° 15997</span><span class="sxs-lookup"><span data-stu-id="2b05e-826">Tracking Issue #15997</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

<span data-ttu-id="2b05e-827">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-827">**Old behavior**</span></span>

<span data-ttu-id="2b05e-828">Antes de EF Core 3.0, estos métodos estaban protegidos.</span><span class="sxs-lookup"><span data-stu-id="2b05e-828">Before EF Core 3.0, these methods were protected.</span></span>

<span data-ttu-id="2b05e-829">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-829">**New behavior**</span></span>

<span data-ttu-id="2b05e-830">Desde EF Core 3.0, estos métodos son públicos.</span><span class="sxs-lookup"><span data-stu-id="2b05e-830">Starting with EF Core 3.0, these methods are public.</span></span>

<span data-ttu-id="2b05e-831">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-831">**Why**</span></span>

<span data-ttu-id="2b05e-832">EF usa estos métodos para determinar si se ha creado una base de datos, pero está vacía.</span><span class="sxs-lookup"><span data-stu-id="2b05e-832">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="2b05e-833">Esto también puede resultar útil fuera de EF al determinar si se deben aplicar migraciones o no.</span><span class="sxs-lookup"><span data-stu-id="2b05e-833">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

<span data-ttu-id="2b05e-834">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-834">**Mitigations**</span></span>

<span data-ttu-id="2b05e-835">Cambie la accesibilidad de cualquier invalidación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-835">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="2b05e-836">Microsoft.EntityFrameworkCore.Design es ahora un paquete DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="2b05e-836">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="2b05e-837">Problema de seguimiento n.° 11506</span><span class="sxs-lookup"><span data-stu-id="2b05e-837">Tracking Issue #11506</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

<span data-ttu-id="2b05e-838">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-838">**Old behavior**</span></span>

<span data-ttu-id="2b05e-839">Antes de EF Core 3.0, Microsoft.EntityFrameworkCore.Design era un paquete NuGet regular con un ensamblado al que podían hacer referencia los proyectos que dependían de él.</span><span class="sxs-lookup"><span data-stu-id="2b05e-839">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

<span data-ttu-id="2b05e-840">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-840">**New behavior**</span></span>

<span data-ttu-id="2b05e-841">Desde EF Core 3.0, es un paquete DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="2b05e-841">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="2b05e-842">Esto significa que la dependencia no fluirá de manera transitiva en otros proyectos y que ya no puede, de forma predeterminada, hacer referencia a su ensamblado.</span><span class="sxs-lookup"><span data-stu-id="2b05e-842">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

<span data-ttu-id="2b05e-843">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-843">**Why**</span></span>

<span data-ttu-id="2b05e-844">Este paquete solo está destinado a usarse en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="2b05e-844">This package is only intended to be used at design time.</span></span> <span data-ttu-id="2b05e-845">Las aplicaciones implementadas no deben hacer referencia al mismo.</span><span class="sxs-lookup"><span data-stu-id="2b05e-845">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="2b05e-846">Hacer que el paquete sea DevelopmentDependency refuerza esta recomendación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-846">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

<span data-ttu-id="2b05e-847">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-847">**Mitigations**</span></span>

<span data-ttu-id="2b05e-848">Si tiene que hacer referencia a este paquete para invalidar el comportamiento en tiempo de diseño de EF Core, puede actualizar los metadatos de elementos PackageReference del proyecto.</span><span class="sxs-lookup"><span data-stu-id="2b05e-848">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="2b05e-849">Si se hace referencia al paquete de manera transitiva a través de Microsoft.EntityFrameworkCore.Tools, tendrá que agregar una PackageReference explícita al paquete para cambiar sus metadatos.</span><span class="sxs-lookup"><span data-stu-id="2b05e-849">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="2b05e-850">Este tipo de referencia explícita debe agregarse a cualquier proyecto que requiera los tipos de paquete.</span><span class="sxs-lookup"><span data-stu-id="2b05e-850">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="2b05e-851">SQLitePCL.raw se ha actualizado a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="2b05e-851">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="2b05e-852">Problema de seguimiento n.° 14824</span><span class="sxs-lookup"><span data-stu-id="2b05e-852">Tracking Issue #14824</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

<span data-ttu-id="2b05e-853">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-853">**Old behavior**</span></span>

<span data-ttu-id="2b05e-854">Microsoft.EntityFrameworkCore.Sqlite dependía anteriormente de la versión 1.1.12 de SQLitePCL.raw.</span><span class="sxs-lookup"><span data-stu-id="2b05e-854">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

<span data-ttu-id="2b05e-855">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-855">**New behavior**</span></span>

<span data-ttu-id="2b05e-856">Hemos actualizado nuestro paquete para depender de la versión 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="2b05e-856">We've updated our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="2b05e-857">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-857">**Why**</span></span>

<span data-ttu-id="2b05e-858">La versión 2.0.0 de SQLitePCL.raw selecciona .NET Standard 2.0 como destino.</span><span class="sxs-lookup"><span data-stu-id="2b05e-858">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="2b05e-859">Anteriormente seleccionaba .NET Standard 1.1 como destino, que requería el cierre a gran escala de paquetes transitivos para su funcionamiento.</span><span class="sxs-lookup"><span data-stu-id="2b05e-859">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

<span data-ttu-id="2b05e-860">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-860">**Mitigations**</span></span>

<span data-ttu-id="2b05e-861">En la versión 2.0.0 de SQLitePCL.raw se incluyen algunos cambios importantes.</span><span class="sxs-lookup"><span data-stu-id="2b05e-861">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="2b05e-862">Consulte las [notas de la versión](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) para obtener detalles.</span><span class="sxs-lookup"><span data-stu-id="2b05e-862">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="2b05e-863">NetTopologySuite se actualizó a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="2b05e-863">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="2b05e-864">Problema de seguimiento n.° 14825</span><span class="sxs-lookup"><span data-stu-id="2b05e-864">Tracking Issue #14825</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

<span data-ttu-id="2b05e-865">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-865">**Old behavior**</span></span>

<span data-ttu-id="2b05e-866">Los paquetes espaciales anteriormente dependían de la versión 1.15.1 de NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="2b05e-866">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

<span data-ttu-id="2b05e-867">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-867">**New behavior**</span></span>

<span data-ttu-id="2b05e-868">Hemos actualizado nuestro paquete para depender de la versión 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="2b05e-868">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="2b05e-869">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-869">**Why**</span></span>

<span data-ttu-id="2b05e-870">La versión 2.0.0 de NetTopologySuite pretende resolver varios problemas de usabilidad que encontraron los usuarios de EF Core.</span><span class="sxs-lookup"><span data-stu-id="2b05e-870">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

<span data-ttu-id="2b05e-871">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-871">**Mitigations**</span></span>

<span data-ttu-id="2b05e-872">En la versión 2.0.0 de NetTopologySuite se incluyen algunos cambios importantes.</span><span class="sxs-lookup"><span data-stu-id="2b05e-872">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="2b05e-873">Consulte las [notas de la versión](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) para obtener detalles.</span><span class="sxs-lookup"><span data-stu-id="2b05e-873">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="2b05e-874">Se usa Microsoft.Data.SqlClient en lugar de System.Data.SqlClient</span><span class="sxs-lookup"><span data-stu-id="2b05e-874">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="2b05e-875">Problema de seguimiento n.º 15636</span><span class="sxs-lookup"><span data-stu-id="2b05e-875">Tracking Issue #15636</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

<span data-ttu-id="2b05e-876">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-876">**Old behavior**</span></span>

<span data-ttu-id="2b05e-877">Microsoft.EntityFrameworkCore.SqlServer dependía anteriormente de la versión System.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="2b05e-877">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

<span data-ttu-id="2b05e-878">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-878">**New behavior**</span></span>

<span data-ttu-id="2b05e-879">Hemos actualizado nuestro paquete para que dependa de Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="2b05e-879">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="2b05e-880">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-880">**Why**</span></span>

<span data-ttu-id="2b05e-881">A partir de ahora, Microsoft.Data.SqlClient es el controlador de acceso a datos insignia para SQL Server y System.Data.SqlClient ya no es el centro de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="2b05e-881">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="2b05e-882">Algunas características importantes, como Always Encrypted, solo están disponibles en Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="2b05e-882">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="2b05e-883">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-883">**Mitigations**</span></span>

<span data-ttu-id="2b05e-884">Si el código toma una dependencia directa en System.Data.SqlClient, debe cambiarla para que haga referencia a Microsoft.Data.SqlClient en su lugar. Dado que los dos paquetes mantienen un grado muy alto de compatibilidad con la API, solo debería ser un paquete simple y un cambio de espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="2b05e-884">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="2b05e-885">Se deben configurar varias relaciones de referencia automática ambiguas</span><span class="sxs-lookup"><span data-stu-id="2b05e-885">Multiple ambiguous self-referencing relationships must be configured</span></span> 

[<span data-ttu-id="2b05e-886">Problema de seguimiento n.º 13573</span><span class="sxs-lookup"><span data-stu-id="2b05e-886">Tracking Issue #13573</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

<span data-ttu-id="2b05e-887">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-887">**Old behavior**</span></span>

<span data-ttu-id="2b05e-888">Un tipo de entidad con varias propiedades de navegación unidireccional de referencia automática y claves externas coincidentes se configuró incorrectamente como una única relación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-888">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="2b05e-889">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-889">For example:</span></span>

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

<span data-ttu-id="2b05e-890">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-890">**New behavior**</span></span>

<span data-ttu-id="2b05e-891">Este escenario se detecta ahora en la generación del modelo y se produce una excepción que indica que el modelo es ambiguo.</span><span class="sxs-lookup"><span data-stu-id="2b05e-891">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

<span data-ttu-id="2b05e-892">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-892">**Why**</span></span>

<span data-ttu-id="2b05e-893">El modelo resultante era ambiguo, y lo más probable es que sea incorrecto en este caso.</span><span class="sxs-lookup"><span data-stu-id="2b05e-893">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

<span data-ttu-id="2b05e-894">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-894">**Mitigations**</span></span>

<span data-ttu-id="2b05e-895">Utilice la configuración completa de la relación.</span><span class="sxs-lookup"><span data-stu-id="2b05e-895">Use full configuration of the relationship.</span></span> <span data-ttu-id="2b05e-896">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="2b05e-896">For example:</span></span>

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
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="2b05e-897">DbFunction.Schema es NULL o la cadena vacía lo configura para estar en el esquema predeterminado del modelo</span><span class="sxs-lookup"><span data-stu-id="2b05e-897">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="2b05e-898">Problema de seguimiento n.º 12757</span><span class="sxs-lookup"><span data-stu-id="2b05e-898">Tracking Issue #12757</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

<span data-ttu-id="2b05e-899">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="2b05e-899">**Old behavior**</span></span>

<span data-ttu-id="2b05e-900">Una función DbFunction configurada con el esquema como una cadena vacía se trataba como una función integrada sin un esquema.</span><span class="sxs-lookup"><span data-stu-id="2b05e-900">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="2b05e-901">Por ejemplo, el código siguiente asignará la función CLR `DatePart` a la función integrada `DATEPART` en SqlServer.</span><span class="sxs-lookup"><span data-stu-id="2b05e-901">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

<span data-ttu-id="2b05e-902">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="2b05e-902">**New behavior**</span></span>

<span data-ttu-id="2b05e-903">Todas las asignaciones de DbFunction se consideran asignadas a funciones definidas por el usuario.</span><span class="sxs-lookup"><span data-stu-id="2b05e-903">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="2b05e-904">Por lo tanto, el valor de cadena vacía colocaría la función dentro del esquema predeterminado del modelo,</span><span class="sxs-lookup"><span data-stu-id="2b05e-904">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="2b05e-905">que podría ser el esquema configurado de forma explícita mediante `modelBuilder.HasDefaultSchema()` de la API fluida o `dbo` en caso contrario.</span><span class="sxs-lookup"><span data-stu-id="2b05e-905">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

<span data-ttu-id="2b05e-906">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="2b05e-906">**Why**</span></span>

<span data-ttu-id="2b05e-907">Anteriormente, el esquema vacío era una manera de indicar que la función estaba integrada, pero esa lógica solo es aplicable a SqlServer, donde las funciones integradas no pertenecen a ningún esquema.</span><span class="sxs-lookup"><span data-stu-id="2b05e-907">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

<span data-ttu-id="2b05e-908">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="2b05e-908">**Mitigations**</span></span>

<span data-ttu-id="2b05e-909">Configure la traslación de DbFunction manualmente para asignarla a una función integrada.</span><span class="sxs-lookup"><span data-stu-id="2b05e-909">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```
