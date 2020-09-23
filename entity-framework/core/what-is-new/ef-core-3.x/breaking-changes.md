---
title: 'Cambios importantes en EF Core 3.x: EF Core'
description: Lista completa de los cambios importantes introducidos en Entity Framework Core 3.x
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: e348cb630d91ebe4536b73b9a7bd9a7b6a46db79
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072244"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a><span data-ttu-id="57ff5-103">Cambios importantes incluidos en EF Core 3.x</span><span class="sxs-lookup"><span data-stu-id="57ff5-103">Breaking changes included in EF Core 3.x</span></span>

<span data-ttu-id="57ff5-104">Es posible que los siguientes cambios de API y comportamiento interrumpan las aplicaciones existentes cuando se actualicen a las versiones 3.x.</span><span class="sxs-lookup"><span data-stu-id="57ff5-104">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.x.</span></span>
<span data-ttu-id="57ff5-105">Los cambios que esperamos que solo afecten a proveedores de base de datos se documentan en [Cambios para proveedores](xref:core/providers/provider-log).</span><span class="sxs-lookup"><span data-stu-id="57ff5-105">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="57ff5-106">Resumen</span><span class="sxs-lookup"><span data-stu-id="57ff5-106">Summary</span></span>

| <span data-ttu-id="57ff5-107">**Cambio importante**</span><span class="sxs-lookup"><span data-stu-id="57ff5-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="57ff5-108">**Impacto**</span><span class="sxs-lookup"><span data-stu-id="57ff5-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="57ff5-109">Las consultas LINQ ya no se evalúan en el cliente</span><span class="sxs-lookup"><span data-stu-id="57ff5-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="57ff5-110">Alto</span><span class="sxs-lookup"><span data-stu-id="57ff5-110">High</span></span>       |
| [<span data-ttu-id="57ff5-111">EF Core 3.0 tiene como destino .NET Standard 2.1, y no .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="57ff5-111">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>](#netstandard21) | <span data-ttu-id="57ff5-112">Alto</span><span class="sxs-lookup"><span data-stu-id="57ff5-112">High</span></span>      |
| [<span data-ttu-id="57ff5-113">La herramienta de línea de comandos de EF Core, dotnet ef, ya no forma parte del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="57ff5-113">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="57ff5-114">Alto</span><span class="sxs-lookup"><span data-stu-id="57ff5-114">High</span></span>      |
| [<span data-ttu-id="57ff5-115">DetectChanges respeta los valores de clave generados por el almacén</span><span class="sxs-lookup"><span data-stu-id="57ff5-115">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="57ff5-116">Alto</span><span class="sxs-lookup"><span data-stu-id="57ff5-116">High</span></span>      |
| [<span data-ttu-id="57ff5-117">FromSql, ExecuteSql y ExecuteSqlAsync han cambiado de nombre</span><span class="sxs-lookup"><span data-stu-id="57ff5-117">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="57ff5-118">Alto</span><span class="sxs-lookup"><span data-stu-id="57ff5-118">High</span></span>      |
| [<span data-ttu-id="57ff5-119">Los tipos de consulta se consolidan con tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="57ff5-119">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="57ff5-120">Alto</span><span class="sxs-lookup"><span data-stu-id="57ff5-120">High</span></span>      |
| [<span data-ttu-id="57ff5-121">Entity Framework Core ya no forma parte del marco compartido ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57ff5-121">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="57ff5-122">Media</span><span class="sxs-lookup"><span data-stu-id="57ff5-122">Medium</span></span>      |
| [<span data-ttu-id="57ff5-123">Las eliminaciones en cascada ahora se realizan inmediatamente de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="57ff5-123">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="57ff5-124">Media</span><span class="sxs-lookup"><span data-stu-id="57ff5-124">Medium</span></span>      |
| [<span data-ttu-id="57ff5-125">La carga diligente de entidades relacionadas ahora se realiza en una sola consulta</span><span class="sxs-lookup"><span data-stu-id="57ff5-125">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="57ff5-126">Media</span><span class="sxs-lookup"><span data-stu-id="57ff5-126">Medium</span></span>      |
| [<span data-ttu-id="57ff5-127">DeleteBehavior.Restrict tiene una semántica más limpia</span><span class="sxs-lookup"><span data-stu-id="57ff5-127">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="57ff5-128">Media</span><span class="sxs-lookup"><span data-stu-id="57ff5-128">Medium</span></span>      |
| [<span data-ttu-id="57ff5-129">La API de configuración para las relaciones de tipo de propiedad ha cambiado</span><span class="sxs-lookup"><span data-stu-id="57ff5-129">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="57ff5-130">Media</span><span class="sxs-lookup"><span data-stu-id="57ff5-130">Medium</span></span>      |
| [<span data-ttu-id="57ff5-131">Cada propiedad usa la generación de claves enteras en memoria independiente</span><span class="sxs-lookup"><span data-stu-id="57ff5-131">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="57ff5-132">Media</span><span class="sxs-lookup"><span data-stu-id="57ff5-132">Medium</span></span>      |
| [<span data-ttu-id="57ff5-133">Las consultas sin seguimiento ya no realizan la resolución de la identidad</span><span class="sxs-lookup"><span data-stu-id="57ff5-133">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="57ff5-134">Media</span><span class="sxs-lookup"><span data-stu-id="57ff5-134">Medium</span></span>      |
| [<span data-ttu-id="57ff5-135">Cambios en la API de metadatos</span><span class="sxs-lookup"><span data-stu-id="57ff5-135">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="57ff5-136">Media</span><span class="sxs-lookup"><span data-stu-id="57ff5-136">Medium</span></span>      |
| [<span data-ttu-id="57ff5-137">Cambios en la API de metadatos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="57ff5-137">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="57ff5-138">Media</span><span class="sxs-lookup"><span data-stu-id="57ff5-138">Medium</span></span>      |
| [<span data-ttu-id="57ff5-139">Se ha quitado el elemento UseRowNumberForPaging</span><span class="sxs-lookup"><span data-stu-id="57ff5-139">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="57ff5-140">Media</span><span class="sxs-lookup"><span data-stu-id="57ff5-140">Medium</span></span>      |
| [<span data-ttu-id="57ff5-141">Cuando el método FromSql se usa con un procedimiento almacenado no se puede redactar</span><span class="sxs-lookup"><span data-stu-id="57ff5-141">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="57ff5-142">Media</span><span class="sxs-lookup"><span data-stu-id="57ff5-142">Medium</span></span>      |
| [<span data-ttu-id="57ff5-143">Solo se pueden especificar métodos de FromSql en raíces de consulta</span><span class="sxs-lookup"><span data-stu-id="57ff5-143">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="57ff5-144">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-144">Low</span></span>      |
| [<span data-ttu-id="57ff5-145">~~La ejecución de consultas se registra en el nivel de depuración~~ Revertido</span><span class="sxs-lookup"><span data-stu-id="57ff5-145">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | <span data-ttu-id="57ff5-146">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-146">Low</span></span>      |
| [<span data-ttu-id="57ff5-147">Los valores de clave temporal ya no se establecen en instancias de entidad</span><span class="sxs-lookup"><span data-stu-id="57ff5-147">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="57ff5-148">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-148">Low</span></span>      |
| [<span data-ttu-id="57ff5-149">Ahora, las entidades dependientes que comparten la tabla con la entidad de seguridad son opcionales</span><span class="sxs-lookup"><span data-stu-id="57ff5-149">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="57ff5-150">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-150">Low</span></span>      |
| [<span data-ttu-id="57ff5-151">Todas las entidades que compartan una tabla con una columna de token de simultaneidad tienen que asignarla a una propiedad</span><span class="sxs-lookup"><span data-stu-id="57ff5-151">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="57ff5-152">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-152">Low</span></span>      |
| [<span data-ttu-id="57ff5-153">Las entidades en propiedad no se pueden consultar sin el propietario mediante una consulta de seguimiento</span><span class="sxs-lookup"><span data-stu-id="57ff5-153">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="57ff5-154">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-154">Low</span></span>      |
| [<span data-ttu-id="57ff5-155">Ahora, las propiedades heredadas de tipos sin asignar se asignan a una única columna para todos los tipos derivados</span><span class="sxs-lookup"><span data-stu-id="57ff5-155">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="57ff5-156">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-156">Low</span></span>      |
| [<span data-ttu-id="57ff5-157">La convención de propiedad de clave externa ya no coincide con el mismo nombre que la propiedad de entidad de seguridad</span><span class="sxs-lookup"><span data-stu-id="57ff5-157">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="57ff5-158">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-158">Low</span></span>      |
| [<span data-ttu-id="57ff5-159">Ahora, la conexión de base de datos se cierra si ya no se usa antes de que se complete TransactionScope</span><span class="sxs-lookup"><span data-stu-id="57ff5-159">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="57ff5-160">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-160">Low</span></span>      |
| [<span data-ttu-id="57ff5-161">Los campos de respaldo se usan de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="57ff5-161">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="57ff5-162">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-162">Low</span></span>      |
| [<span data-ttu-id="57ff5-163">Inicio de excepciones si se encuentran varios campos de respaldo compatibles</span><span class="sxs-lookup"><span data-stu-id="57ff5-163">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="57ff5-164">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-164">Low</span></span>      |
| [<span data-ttu-id="57ff5-165">Los nombres de propiedades de solo campo deben coincidir con el nombre del campo</span><span class="sxs-lookup"><span data-stu-id="57ff5-165">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="57ff5-166">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-166">Low</span></span>      |
| [<span data-ttu-id="57ff5-167">AddDbContext/AddDbContextPool ya no llaman a AddLogging ni a AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="57ff5-167">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="57ff5-168">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-168">Low</span></span>      |
| [<span data-ttu-id="57ff5-169">AddEntityFramework\* agrega IMemoryCache con un límite de tamaño</span><span class="sxs-lookup"><span data-stu-id="57ff5-169">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="57ff5-170">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-170">Low</span></span>      |
| [<span data-ttu-id="57ff5-171">Ahora DbContext.Entry realiza una operación DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="57ff5-171">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="57ff5-172">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-172">Low</span></span>      |
| [<span data-ttu-id="57ff5-173">El cliente no genera las claves de matriz de cadena y byte de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="57ff5-173">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="57ff5-174">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-174">Low</span></span>      |
| [<span data-ttu-id="57ff5-175">Ahora ILoggerFactory es un servicio con ámbito</span><span class="sxs-lookup"><span data-stu-id="57ff5-175">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="57ff5-176">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-176">Low</span></span>      |
| [<span data-ttu-id="57ff5-177">En los proxies de carga diferida ya no se supone que las propiedades de navegación están totalmente cargadas</span><span class="sxs-lookup"><span data-stu-id="57ff5-177">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="57ff5-178">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-178">Low</span></span>      |
| [<span data-ttu-id="57ff5-179">La creación excesiva de proveedores de servicios internos ahora es un error de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="57ff5-179">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="57ff5-180">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-180">Low</span></span>      |
| [<span data-ttu-id="57ff5-181">Comportamiento nuevo de HasOne/HasMany llamado con una sola cadena</span><span class="sxs-lookup"><span data-stu-id="57ff5-181">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="57ff5-182">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-182">Low</span></span>      |
| [<span data-ttu-id="57ff5-183">El tipo de valor devuelto para varios métodos asincrónicos se ha cambiado de Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="57ff5-183">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="57ff5-184">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-184">Low</span></span>      |
| [<span data-ttu-id="57ff5-185">La anotación Relational:TypeMapping ahora es simplemente TypeMapping</span><span class="sxs-lookup"><span data-stu-id="57ff5-185">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="57ff5-186">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-186">Low</span></span>      |
| [<span data-ttu-id="57ff5-187">ToTable en un tipo derivado produce una excepción</span><span class="sxs-lookup"><span data-stu-id="57ff5-187">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="57ff5-188">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-188">Low</span></span>      |
| [<span data-ttu-id="57ff5-189">EF Core ya no envía pragma para el cumplimiento de SQLite FK</span><span class="sxs-lookup"><span data-stu-id="57ff5-189">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="57ff5-190">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-190">Low</span></span>      |
| [<span data-ttu-id="57ff5-191">Microsoft.EntityFrameworkCore.Sqlite ahora depende de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="57ff5-191">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="57ff5-192">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-192">Low</span></span>      |
| [<span data-ttu-id="57ff5-193">Los valores GUID se almacenan ahora como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="57ff5-193">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="57ff5-194">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-194">Low</span></span>      |
| [<span data-ttu-id="57ff5-195">Ahora los valores char se almacenan como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="57ff5-195">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="57ff5-196">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-196">Low</span></span>      |
| [<span data-ttu-id="57ff5-197">Ahora los id. de migración se generan usando el calendario de la referencia cultural invariable</span><span class="sxs-lookup"><span data-stu-id="57ff5-197">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="57ff5-198">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-198">Low</span></span>      |
| [<span data-ttu-id="57ff5-199">La información o los metadatos de la extensión se han quitado de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="57ff5-199">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="57ff5-200">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-200">Low</span></span>      |
| [<span data-ttu-id="57ff5-201">LogQueryPossibleExceptionWithAggregateOperator ha cambiado de nombre</span><span class="sxs-lookup"><span data-stu-id="57ff5-201">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="57ff5-202">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-202">Low</span></span>      |
| [<span data-ttu-id="57ff5-203">Clarificación de la API para nombres de restricciones de claves externas</span><span class="sxs-lookup"><span data-stu-id="57ff5-203">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="57ff5-204">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-204">Low</span></span>      |
| [<span data-ttu-id="57ff5-205">IRelationalDatabaseCreator.HasTables/HasTablesAsync se han hecho públicos</span><span class="sxs-lookup"><span data-stu-id="57ff5-205">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="57ff5-206">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-206">Low</span></span>      |
| [<span data-ttu-id="57ff5-207">Microsoft.EntityFrameworkCore.Design es ahora un paquete DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="57ff5-207">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="57ff5-208">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-208">Low</span></span>      |
| [<span data-ttu-id="57ff5-209">SQLitePCL.raw se ha actualizado a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="57ff5-209">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="57ff5-210">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-210">Low</span></span>      |
| [<span data-ttu-id="57ff5-211">NetTopologySuite se actualizó a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="57ff5-211">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="57ff5-212">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-212">Low</span></span>      |
| [<span data-ttu-id="57ff5-213">Se usa Microsoft.Data.SqlClient en lugar de System.Data.SqlClient</span><span class="sxs-lookup"><span data-stu-id="57ff5-213">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="57ff5-214">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-214">Low</span></span>      |
| [<span data-ttu-id="57ff5-215">Se deben configurar varias relaciones de referencia automática ambiguas</span><span class="sxs-lookup"><span data-stu-id="57ff5-215">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="57ff5-216">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-216">Low</span></span>      |
| [<span data-ttu-id="57ff5-217">DbFunction.Schema es NULL o la cadena vacía lo configura para estar en el esquema predeterminado del modelo</span><span class="sxs-lookup"><span data-stu-id="57ff5-217">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="57ff5-218">Bajo</span><span class="sxs-lookup"><span data-stu-id="57ff5-218">Low</span></span>      |

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="57ff5-219">Las consultas LINQ ya no se evalúan en el cliente</span><span class="sxs-lookup"><span data-stu-id="57ff5-219">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="57ff5-220">[Problema de seguimiento n.° 14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Consulte también el problema n.° 12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="57ff5-220">[Tracking Issue #14935](https://github.com/aspnet/EntityFrameworkCore/issues/14935)
[Also see issue #12795](https://github.com/aspnet/EntityFrameworkCore/issues/12795)</span></span>

<span data-ttu-id="57ff5-221">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-221">**Old behavior**</span></span>

<span data-ttu-id="57ff5-222">Antes de 3.0, cuando en EF Core no se podía convertir una expresión que formaba parte de una consulta SQL o un parámetro, la expresión se evaluaba de forma automática en el cliente.</span><span class="sxs-lookup"><span data-stu-id="57ff5-222">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="57ff5-223">De forma predeterminada, la evaluación de cliente de expresiones potencialmente costosas solo desencadenaba una advertencia.</span><span class="sxs-lookup"><span data-stu-id="57ff5-223">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

<span data-ttu-id="57ff5-224">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-224">**New behavior**</span></span>

<span data-ttu-id="57ff5-225">A partir de 3.0, en EF Core solo se permite que se evalúen en el cliente las expresiones en la proyección de nivel superior (la última llamada a `Select()` de la consulta).</span><span class="sxs-lookup"><span data-stu-id="57ff5-225">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="57ff5-226">Cuando las expresiones de otra parte de la consulta no se pueden convertir en SQL o un parámetro, se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="57ff5-226">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

<span data-ttu-id="57ff5-227">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-227">**Why**</span></span>

<span data-ttu-id="57ff5-228">La evaluación de cliente automática de las consultas permite que se ejecuten muchas consultas incluso si no se pueden convertir elementos importantes de ellas.</span><span class="sxs-lookup"><span data-stu-id="57ff5-228">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="57ff5-229">Esto puede provocar un comportamiento inesperado y potencialmente dañino que es posible que solo sea evidente en entornos de producción.</span><span class="sxs-lookup"><span data-stu-id="57ff5-229">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="57ff5-230">Por ejemplo, una condición en una llamada a `Where()` que no se puede convertir puede provocar que todas las filas de la tabla se transfieran desde el servidor de base de datos y que el filtro se aplique en el cliente.</span><span class="sxs-lookup"><span data-stu-id="57ff5-230">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="57ff5-231">Esta situación puede pasar desapercibida fácilmente si la tabla solo contiene algunas filas en la fase de desarrollo, pero ser más grave cuando la aplicación pase a producción, donde la tabla puede contener millones de filas.</span><span class="sxs-lookup"><span data-stu-id="57ff5-231">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="57ff5-232">Las advertencias de evaluación de cliente también se suelen pasar por alto durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="57ff5-232">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="57ff5-233">Además de esto, la evaluación de cliente automática puede causar problemas en los que la mejora de la traducción de consultas para expresiones específicas provocaba cambios importantes no deseados entre versiones.</span><span class="sxs-lookup"><span data-stu-id="57ff5-233">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

<span data-ttu-id="57ff5-234">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-234">**Mitigations**</span></span>

<span data-ttu-id="57ff5-235">Si una consulta no se puede traducir totalmente, vuelva a escribirla en un formato que se pueda traducir, o bien use `AsEnumerable()`, `ToList()` o una función similar para devolver los datos al cliente de forma explícita, donde después se puedan seguir procesando mediante LINQ to Objects.</span><span class="sxs-lookup"><span data-stu-id="57ff5-235">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="netstandard21"></a>
### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20"></a><span data-ttu-id="57ff5-236">EF Core 3.0 tiene como destino .NET Standard 2.1, y no .NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="57ff5-236">EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0</span></span>

[<span data-ttu-id="57ff5-237">Problema de seguimiento n.º 15498</span><span class="sxs-lookup"><span data-stu-id="57ff5-237">Tracking Issue #15498</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15498)

> [!IMPORTANT] 
> <span data-ttu-id="57ff5-238">EF Core 3.1 vuelve a tener como objetivo a .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="57ff5-238">EF Core 3.1 targets .NET Standard 2.0 again.</span></span> <span data-ttu-id="57ff5-239">Esto reincorpora la compatibilidad con .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="57ff5-239">This brings back support for .NET Framework.</span></span>

<span data-ttu-id="57ff5-240">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-240">**Old behavior**</span></span>

<span data-ttu-id="57ff5-241">Antes de la versión 3.0, EF Core tenía como destino .NET Standard 2.0 y se podía ejecutar en todas las plataformas que admitieran dicho estándar, incluido .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="57ff5-241">Before 3.0, EF Core targeted .NET Standard 2.0 and would run on all platforms that support that standard, including .NET Framework.</span></span>

<span data-ttu-id="57ff5-242">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-242">**New behavior**</span></span>

<span data-ttu-id="57ff5-243">A partir de la versión 3.0, EF Core tiene como destino .NET Standard 2.1 y se puede ejecutar en todas las plataformas que admitan dicho estándar.</span><span class="sxs-lookup"><span data-stu-id="57ff5-243">Starting with 3.0, EF Core targets .NET Standard 2.1 and will run on all platforms that support this standard.</span></span> <span data-ttu-id="57ff5-244">Esto no incluye .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="57ff5-244">This does not include .NET Framework.</span></span>

<span data-ttu-id="57ff5-245">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-245">**Why**</span></span>

<span data-ttu-id="57ff5-246">Esto forma parte de una decisión estratégica para todas las tecnologías de .NET que tiene como objetivo centrar los esfuerzos en .NET Core y otras plataformas modernas de .NET, como Xamarin.</span><span class="sxs-lookup"><span data-stu-id="57ff5-246">This is part of a strategic decision across .NET technologies to focus energy on .NET Core and other modern .NET platforms, such as Xamarin.</span></span>

<span data-ttu-id="57ff5-247">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-247">**Mitigations**</span></span>

<span data-ttu-id="57ff5-248">Use EF Core 3.1.</span><span class="sxs-lookup"><span data-stu-id="57ff5-248">Use EF Core 3.1.</span></span>

<a name="no-longer"></a>
### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="57ff5-249">Entity Framework Core ya no forma parte del marco compartido ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57ff5-249">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="57ff5-250">Anuncios del problema de seguimiento n.º 325</span><span class="sxs-lookup"><span data-stu-id="57ff5-250">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

<span data-ttu-id="57ff5-251">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-251">**Old behavior**</span></span>

<span data-ttu-id="57ff5-252">Antes de ASP.NET Core 3.0, cuando se agregaba una referencia de paquete a `Microsoft.AspNetCore.App` o `Microsoft.AspNetCore.All`, se incluía EF Core y algunos de los proveedores de datos de EF Core, como el de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="57ff5-252">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

<span data-ttu-id="57ff5-253">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-253">**New behavior**</span></span>

<span data-ttu-id="57ff5-254">A partir de la versión 3.0, el marco compartido ASP.NET Core no incluye EF Core ni ningún proveedor de datos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="57ff5-254">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

<span data-ttu-id="57ff5-255">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-255">**Why**</span></span>

<span data-ttu-id="57ff5-256">Antes de este cambio, para obtener EF Core se necesitaban varios pasos en función de si la aplicación se destinaba a ASP.NET Core y SQL Server o no.</span><span class="sxs-lookup"><span data-stu-id="57ff5-256">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span> <span data-ttu-id="57ff5-257">Además, la actualización de ASP.NET Core forzaba la de EF Core y el proveedor de SQL Server, lo que no siempre es deseable.</span><span class="sxs-lookup"><span data-stu-id="57ff5-257">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="57ff5-258">Con este cambio, la experiencia de obtención de EF Core es la misma en todos los proveedores, implementaciones admitidas de .NET y tipos de aplicación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-258">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="57ff5-259">Ahora los desarrolladores también pueden controlar exactamente cuándo se actualizan EF Core y los proveedores de datos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="57ff5-259">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

<span data-ttu-id="57ff5-260">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-260">**Mitigations**</span></span>

<span data-ttu-id="57ff5-261">Para usar EF Core en una aplicación ASP.NET Core 3.0 o cualquier otra aplicación compatible, debe agregar de forma explícita una referencia de paquete al proveedor de base de datos de EF Core que se va a usar en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-261">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>
### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="57ff5-262">La herramienta de línea de comandos de EF Core, dotnet ef, ya no forma parte del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="57ff5-262">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="57ff5-263">Problema de seguimiento n.º 14016</span><span class="sxs-lookup"><span data-stu-id="57ff5-263">Tracking Issue #14016</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14016)

<span data-ttu-id="57ff5-264">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-264">**Old behavior**</span></span>

<span data-ttu-id="57ff5-265">Antes de 3.0, la herramienta `dotnet ef` se incluía en el SDK de .NET Core y estaba disponible para usarse desde la línea de comandos de cualquier proyecto sin necesidad de realizar pasos adicionales.</span><span class="sxs-lookup"><span data-stu-id="57ff5-265">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span> 

<span data-ttu-id="57ff5-266">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-266">**New behavior**</span></span>

<span data-ttu-id="57ff5-267">A partir de la versión 3.0, el SDK de .NET no incluye la herramienta `dotnet ef`, por lo que antes de poder usarla tendrá que instalarla de forma explícita como una herramienta local o global.</span><span class="sxs-lookup"><span data-stu-id="57ff5-267">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span> 

<span data-ttu-id="57ff5-268">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-268">**Why**</span></span>

<span data-ttu-id="57ff5-269">Este cambio nos permite distribuir y actualizar `dotnet ef` como una herramienta convencional de la CLI de .NET en NuGet, coherente con el hecho de que la versión 3.0 de EF Core también se distribuye siempre como un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="57ff5-269">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

<span data-ttu-id="57ff5-270">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-270">**Mitigations**</span></span>

<span data-ttu-id="57ff5-271">Para poder administrar las migraciones o aplicar scaffolding a `DbContext`, instale `dotnet-ef` como herramienta global:</span><span class="sxs-lookup"><span data-stu-id="57ff5-271">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

  ``` console
    $ dotnet tool install --global dotnet-ef
  ```

<span data-ttu-id="57ff5-272">También se puede obtener una herramienta local cuando se restauran las dependencias de un proyecto que la declara como una dependencia de herramientas mediante un [archivo de manifiesto de herramientas](/dotnet/core/tools/global-tools#install-a-local-tool).</span><span class="sxs-lookup"><span data-stu-id="57ff5-272">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

<a name="fromsql"></a>
### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="57ff5-273">FromSql, ExecuteSql y ExecuteSqlAsync han cambiado de nombre</span><span class="sxs-lookup"><span data-stu-id="57ff5-273">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="57ff5-274">Problema de seguimiento n.º 10996</span><span class="sxs-lookup"><span data-stu-id="57ff5-274">Tracking Issue #10996</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10996)

<span data-ttu-id="57ff5-275">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-275">**Old behavior**</span></span>

<span data-ttu-id="57ff5-276">Antes de EF Core 3.0, estos nombres de métodos se sobrecargaban para funcionar tanto con una cadena normal como con una cadena que se debería interpolar en SQL y parámetros.</span><span class="sxs-lookup"><span data-stu-id="57ff5-276">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

<span data-ttu-id="57ff5-277">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-277">**New behavior**</span></span>

<span data-ttu-id="57ff5-278">A partir de la versión EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw` y `ExecuteSqlRawAsync` para crear una consulta con parámetros donde los parámetros se pasan por separado de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="57ff5-278">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="57ff5-279">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-279">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="57ff5-280">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated` y `ExecuteSqlInterpolatedAsync` para crear una consulta con parámetros donde los parámetros se pasan como parte de una cadena de consulta interpolada.</span><span class="sxs-lookup"><span data-stu-id="57ff5-280">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="57ff5-281">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-281">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="57ff5-282">Tenga en cuenta que las dos consultas anteriores producirán el mismo código SQL parametrizado con los mismos parámetros SQL.</span><span class="sxs-lookup"><span data-stu-id="57ff5-282">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

<span data-ttu-id="57ff5-283">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-283">**Why**</span></span>

<span data-ttu-id="57ff5-284">Las sobrecargas del método como esta facilitan las llamadas accidentales al método de cadena sin procesar cuando la intención era llamar al método de cadena interpolada y viceversa.</span><span class="sxs-lookup"><span data-stu-id="57ff5-284">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="57ff5-285">Esto podría resultar en consultas que no se parametrizan cuando deberían.</span><span class="sxs-lookup"><span data-stu-id="57ff5-285">This could result in queries not being parameterized when they should have been.</span></span>

<span data-ttu-id="57ff5-286">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-286">**Mitigations**</span></span>

<span data-ttu-id="57ff5-287">Haga el cambio para usar los nuevos nombres de métodos.</span><span class="sxs-lookup"><span data-stu-id="57ff5-287">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>
### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="57ff5-288">Cuando el método FromSql se usa con un procedimiento almacenado no se puede redactar</span><span class="sxs-lookup"><span data-stu-id="57ff5-288">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="57ff5-289">Problema de seguimiento n.° 15392</span><span class="sxs-lookup"><span data-stu-id="57ff5-289">Tracking Issue #15392</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15392)

<span data-ttu-id="57ff5-290">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-290">**Old behavior**</span></span>

<span data-ttu-id="57ff5-291">Antes de EF Core 3.0, el método FromSql intentaba detectar si se podía redactar en el código SQL pasado.</span><span class="sxs-lookup"><span data-stu-id="57ff5-291">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="57ff5-292">Cuando el código SQL no se podía redactar, como un procedimiento almacenado, realizaba la evaluación de cliente.</span><span class="sxs-lookup"><span data-stu-id="57ff5-292">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="57ff5-293">La consulta siguiente funcionaba al ejecutar el procedimiento almacenado en el servidor y aplicar FirstOrDefault en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="57ff5-293">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

<span data-ttu-id="57ff5-294">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-294">**New behavior**</span></span>

<span data-ttu-id="57ff5-295">A partir de EF Core 3.0, EF Core no intentará analizar el código SQL.</span><span class="sxs-lookup"><span data-stu-id="57ff5-295">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="57ff5-296">Por tanto, si va a redactar después de FromSqlRaw/FromSqlInterpolated, EF Core redactará el código SQL generando una subconsulta.</span><span class="sxs-lookup"><span data-stu-id="57ff5-296">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="57ff5-297">Por tanto, si usa un procedimiento almacenado con la redacción, obtendrá una excepción de sintaxis de SQL no válida.</span><span class="sxs-lookup"><span data-stu-id="57ff5-297">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

<span data-ttu-id="57ff5-298">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-298">**Why**</span></span>

<span data-ttu-id="57ff5-299">EF Core 3.0 no admite la evaluación automática de cliente, ya que era propenso a errores, como se explica [aquí](#linq-queries-are-no-longer-evaluated-on-the-client).</span><span class="sxs-lookup"><span data-stu-id="57ff5-299">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

<span data-ttu-id="57ff5-300">**Mitigación**</span><span class="sxs-lookup"><span data-stu-id="57ff5-300">**Mitigation**</span></span>

<span data-ttu-id="57ff5-301">Si usa un procedimiento almacenado en FromSqlRaw/FromSqlInterpolated, sabe que no se puede redactar, por lo que puede agregar __AsEnumerable/AsAsyncEnumerable__ justo después de la llamada al método FromSql para evitar cualquier redacción en el lado servidor.</span><span class="sxs-lookup"><span data-stu-id="57ff5-301">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="57ff5-302">Solo se pueden especificar métodos de FromSql en raíces de consulta.</span><span class="sxs-lookup"><span data-stu-id="57ff5-302">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="57ff5-303">Problema de seguimiento n.° 15704</span><span class="sxs-lookup"><span data-stu-id="57ff5-303">Tracking Issue #15704</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15704)

<span data-ttu-id="57ff5-304">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-304">**Old behavior**</span></span>

<span data-ttu-id="57ff5-305">Antes de EF Core 3.0, el método `FromSql` podía especificarse en cualquier lugar en la consulta.</span><span class="sxs-lookup"><span data-stu-id="57ff5-305">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

<span data-ttu-id="57ff5-306">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-306">**New behavior**</span></span>

<span data-ttu-id="57ff5-307">A partir de EF Core 3.0, los nuevos métodos `FromSqlRaw` y `FromSqlInterpolated` (que reemplazan a `FromSql`) solo pueden especificarse en las raíces de la consulta, es decir, directamente en `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-307">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="57ff5-308">Si intenta especificarlos en cualquier otro lugar se producirá un error de compilación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-308">Attempting to specify them anywhere else will result in a compilation error.</span></span>

<span data-ttu-id="57ff5-309">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-309">**Why**</span></span>

<span data-ttu-id="57ff5-310">La especificación de `FromSql` en cualquier otro lugar diferente de `DbSet` no tenía un significado o valor agregado, y podría causar ambigüedad en ciertos escenarios.</span><span class="sxs-lookup"><span data-stu-id="57ff5-310">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

<span data-ttu-id="57ff5-311">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-311">**Mitigations**</span></span>

<span data-ttu-id="57ff5-312">Las invocaciones de `FromSql` se deben mover para que estén directamente en el `DbSet` al que se aplican.</span><span class="sxs-lookup"><span data-stu-id="57ff5-312">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>
### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="57ff5-313">Las consultas sin seguimiento ya no realizan la resolución de la identidad</span><span class="sxs-lookup"><span data-stu-id="57ff5-313">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="57ff5-314">Problema de seguimiento n.º 13518</span><span class="sxs-lookup"><span data-stu-id="57ff5-314">Tracking Issue #13518</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13518)

<span data-ttu-id="57ff5-315">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-315">**Old behavior**</span></span>

<span data-ttu-id="57ff5-316">Antes de EF Core 3.0, se usaba la misma instancia de la entidad para cada aparición de una entidad con un tipo e identificador determinados.</span><span class="sxs-lookup"><span data-stu-id="57ff5-316">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="57ff5-317">Este comportamiento coincide con el de las consultas de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="57ff5-317">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="57ff5-318">Fijémonos en esta consulta:</span><span class="sxs-lookup"><span data-stu-id="57ff5-318">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```
<span data-ttu-id="57ff5-319">Esta consulta devolverá la misma instancia de `Category` para cada elemento `Product` asociado con la categoría determinada.</span><span class="sxs-lookup"><span data-stu-id="57ff5-319">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

<span data-ttu-id="57ff5-320">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-320">**New behavior**</span></span>

<span data-ttu-id="57ff5-321">A partir de EF Core 3.0, se crean distintas instancias de la entidad si se encuentra una entidad con un tipo e identificador determinados en varias ubicaciones del gráfico devuelto.</span><span class="sxs-lookup"><span data-stu-id="57ff5-321">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="57ff5-322">Por ejemplo, la consulta anterior ahora devolverá una nueva instancia de `Category` para cada elemento `Product` cuando haya dos productos asociados a la misma categoría.</span><span class="sxs-lookup"><span data-stu-id="57ff5-322">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

<span data-ttu-id="57ff5-323">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-323">**Why**</span></span>

<span data-ttu-id="57ff5-324">La resolución de las identidades (es decir, el hecho de determinar que una entidad tiene los mismos tipo e identificador que la entidad encontrada) agrega más rendimiento y sobrecarga de memoria.</span><span class="sxs-lookup"><span data-stu-id="57ff5-324">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="57ff5-325">Este enfoque suele ser contrario a por qué las consultas sin seguimiento se usan en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="57ff5-325">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="57ff5-326">Además, aunque la resolución de las identidades a veces puede resultar útil, no es necesaria si las entidades se van a serializar y enviar a un cliente, algo habitual para las consultas sin seguimiento.</span><span class="sxs-lookup"><span data-stu-id="57ff5-326">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

<span data-ttu-id="57ff5-327">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-327">**Mitigations**</span></span>

<span data-ttu-id="57ff5-328">Si se requiere la resolución de identidad, use una consulta de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="57ff5-328">Use a tracking query if identity resolution is required.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="57ff5-329">~~La ejecución de consultas se registra en el nivel de depuración~~ Revertido</span><span class="sxs-lookup"><span data-stu-id="57ff5-329">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="57ff5-330">Problema de seguimiento n.º 14523</span><span class="sxs-lookup"><span data-stu-id="57ff5-330">Tracking Issue #14523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14523)

<span data-ttu-id="57ff5-331">Revertimos este cambio porque la nueva configuración de EF Core 3.0 permite a la aplicación especificar el nivel de registro para cualquier evento.</span><span class="sxs-lookup"><span data-stu-id="57ff5-331">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="57ff5-332">Por ejemplo, para cambiar el registro de SQL a `Debug`, configure el nivel de forma explícita en `OnConfiguring` o `AddDbContext`:</span><span class="sxs-lookup"><span data-stu-id="57ff5-332">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>
```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="57ff5-333">Los valores de clave temporal ya no se establecen en instancias de entidad</span><span class="sxs-lookup"><span data-stu-id="57ff5-333">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="57ff5-334">Problema de seguimiento n.º 12378</span><span class="sxs-lookup"><span data-stu-id="57ff5-334">Tracking Issue #12378</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12378)

<span data-ttu-id="57ff5-335">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-335">**Old behavior**</span></span>

<span data-ttu-id="57ff5-336">Antes de EF Core 3.0, los valores temporales se asignaban a todas las propiedades de clave para las que posteriormente la base de datos generaba un valor real.</span><span class="sxs-lookup"><span data-stu-id="57ff5-336">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="57ff5-337">Normalmente, estos valores temporales eran números negativos grandes.</span><span class="sxs-lookup"><span data-stu-id="57ff5-337">Usually these temporary values were large negative numbers.</span></span>

<span data-ttu-id="57ff5-338">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-338">**New behavior**</span></span>

<span data-ttu-id="57ff5-339">A partir de la versión 3.0, en EF Core se almacena el valor de clave temporal como parte de la información de seguimiento de la entidad y la propiedad clave en sí no se modifica.</span><span class="sxs-lookup"><span data-stu-id="57ff5-339">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

<span data-ttu-id="57ff5-340">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-340">**Why**</span></span>

<span data-ttu-id="57ff5-341">Este cambio se ha realizado para evitar que los valores de clave temporales se conviertan erróneamente en permanentes cuando una entidad de la que previamente una instancia de `DbContext` ha realizado el seguimiento se mueve a otra instancia de `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-341">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span> 

<span data-ttu-id="57ff5-342">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-342">**Mitigations**</span></span>

<span data-ttu-id="57ff5-343">Las aplicaciones que asignan valores de clave principal a claves externas para crear asociaciones entre entidades pueden depender del comportamiento anterior si las claves principales son generadas por el almacén y pertenecen a entidades en el estado `Added`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-343">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="57ff5-344">Esto se puede evitar con las siguientes situaciones:</span><span class="sxs-lookup"><span data-stu-id="57ff5-344">This can be avoided by:</span></span>
* <span data-ttu-id="57ff5-345">No se usan claves generadas por el almacén.</span><span class="sxs-lookup"><span data-stu-id="57ff5-345">Not using store-generated keys.</span></span>
* <span data-ttu-id="57ff5-346">Se establecen propiedades de navegación para crear relaciones en lugar de establecer valores de clave externa.</span><span class="sxs-lookup"><span data-stu-id="57ff5-346">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="57ff5-347">Se obtienen los valores de clave temporal reales de la información de seguimiento de la entidad.</span><span class="sxs-lookup"><span data-stu-id="57ff5-347">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="57ff5-348">Por ejemplo, `context.Entry(blog).Property(e => e.Id).CurrentValue` devolverá el valor temporal aunque no se haya establecido `blog.Id`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-348">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="57ff5-349">DetectChanges respeta los valores de clave generados por el almacén</span><span class="sxs-lookup"><span data-stu-id="57ff5-349">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="57ff5-350">Problema de seguimiento n.º 14616</span><span class="sxs-lookup"><span data-stu-id="57ff5-350">Tracking Issue #14616</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14616)

<span data-ttu-id="57ff5-351">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-351">**Old behavior**</span></span>

<span data-ttu-id="57ff5-352">Antes de EF Core 3.0, se realizaba el seguimiento en el estado `Added` de las entidades sin seguimiento detectadas por `DetectChanges` y se insertaban como una fila nueva cuando se llamaba a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-352">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

<span data-ttu-id="57ff5-353">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-353">**New behavior**</span></span>

<span data-ttu-id="57ff5-354">A partir de EF Core 3.0, si una entidad usa valores de clave generados y se establece un valor de clave, se realizará el seguimiento de la entidad en el estado `Modified`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-354">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="57ff5-355">Esto significa que se supone que existe una fila para la entidad y que se actualizará cuando se llame a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-355">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="57ff5-356">Si no se establece el valor de clave, o bien si el tipo de entidad no usa claves generadas, se seguirá realizando el seguimiento de la entidad nueva como `Added` al igual que en las versiones anteriores.</span><span class="sxs-lookup"><span data-stu-id="57ff5-356">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

<span data-ttu-id="57ff5-357">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-357">**Why**</span></span>

<span data-ttu-id="57ff5-358">Este cambio se ha realizado para que sea más sencillo y coherente trabajar con gráficos de entidades desconectadas mientras se usan claves generadas por el almacén.</span><span class="sxs-lookup"><span data-stu-id="57ff5-358">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

<span data-ttu-id="57ff5-359">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-359">**Mitigations**</span></span>

<span data-ttu-id="57ff5-360">Este cambio puede interrumpir una aplicación si se configura un tipo de entidad para usar claves generadas, pero se establecen de forma explícita valores de clave para las instancias nuevas.</span><span class="sxs-lookup"><span data-stu-id="57ff5-360">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="57ff5-361">La solución consiste en configurar de forma explícita las propiedades de clave para que no usen valores generados.</span><span class="sxs-lookup"><span data-stu-id="57ff5-361">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="57ff5-362">Por ejemplo, con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="57ff5-362">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="57ff5-363">O bien con anotaciones de datos:</span><span class="sxs-lookup"><span data-stu-id="57ff5-363">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```
<a name="cascade"></a>
### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="57ff5-364">Las eliminaciones en cascada ahora se realizan inmediatamente de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="57ff5-364">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="57ff5-365">Problema de seguimiento n.º 10114</span><span class="sxs-lookup"><span data-stu-id="57ff5-365">Tracking Issue #10114</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10114)

<span data-ttu-id="57ff5-366">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-366">**Old behavior**</span></span>

<span data-ttu-id="57ff5-367">Antes de la versión 3.0, en EF Core no se aplicaban acciones en cascada (eliminación de entidades dependientes cuando se eliminaba una entidad de seguridad obligatoria o cuando se rompía la relación con una entidad de seguridad obligatoria) hasta que se llamaba a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="57ff5-367">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

<span data-ttu-id="57ff5-368">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-368">**New behavior**</span></span>

<span data-ttu-id="57ff5-369">A partir de 3.0, en EF Core las acciones en cascada se aplican en cuanto se detecta la condición desencadenadora.</span><span class="sxs-lookup"><span data-stu-id="57ff5-369">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="57ff5-370">Por ejemplo, como resultado de la llamada a `context.Remove()` para eliminar una entidad de seguridad, todos los dependientes obligatorios relacionados de los que se realiza el seguimiento también se establecen en `Deleted` inmediatamente.</span><span class="sxs-lookup"><span data-stu-id="57ff5-370">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

<span data-ttu-id="57ff5-371">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-371">**Why**</span></span>

<span data-ttu-id="57ff5-372">Este cambio se ha realizado para mejorar la experiencia en escenarios de auditoría y enlace de datos, donde es importante comprender qué entidades se van a eliminar _antes de llamar a_  `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-372">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

<span data-ttu-id="57ff5-373">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-373">**Mitigations**</span></span>

<span data-ttu-id="57ff5-374">El comportamiento anterior se puede restaurar mediante opciones de `context.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-374">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="57ff5-375">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-375">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```
<a name="eager-loading-single-query"></a>
### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="57ff5-376">La carga diligente de entidades relacionadas ahora se realiza en una sola consulta</span><span class="sxs-lookup"><span data-stu-id="57ff5-376">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="57ff5-377">Problema de seguimiento n.º 18022</span><span class="sxs-lookup"><span data-stu-id="57ff5-377">Tracking issue #18022</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18022)

<span data-ttu-id="57ff5-378">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-378">**Old behavior**</span></span>

<span data-ttu-id="57ff5-379">Antes de la versión 3.0, la carga diligente de navegaciones de colección a través de operadores `Include` provocaba la generación de varias consultas en la base de datos relacional, una para cada tipo de entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="57ff5-379">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

<span data-ttu-id="57ff5-380">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-380">**New behavior**</span></span>

<span data-ttu-id="57ff5-381">A partir de la versión 3.0, EF Core genera una sola consulta con operadores JOIN en las bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="57ff5-381">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

<span data-ttu-id="57ff5-382">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-382">**Why**</span></span>

<span data-ttu-id="57ff5-383">La emisión de varias consultas para implementar una única consulta LINQ provocaba numerosos problemas, incluido el rendimiento negativo, ya que se necesitaban varios recorridos de ida y vuelta a la base de datos, y problemas de coherencia de datos, ya que cada consulta podía observar un estado distinto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="57ff5-383">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

<span data-ttu-id="57ff5-384">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-384">**Mitigations**</span></span>

<span data-ttu-id="57ff5-385">Aunque técnicamente esto no es un cambio importante, podría tener un efecto considerable en el rendimiento de la aplicación cuando una sola consulta contiene un gran número de operadores `Include` en las navegaciones de la colección.</span><span class="sxs-lookup"><span data-stu-id="57ff5-385">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="57ff5-386">[Vea este comentario](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) para obtener más información y para volver a escribir las consultas de una manera más eficaz.</span><span class="sxs-lookup"><span data-stu-id="57ff5-386">[See this comment](https://github.com/aspnet/EntityFrameworkCore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>
### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="57ff5-387">DeleteBehavior.Restrict tiene una semántica más limpia</span><span class="sxs-lookup"><span data-stu-id="57ff5-387">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="57ff5-388">Problema de seguimiento n.º 12661</span><span class="sxs-lookup"><span data-stu-id="57ff5-388">Tracking Issue #12661</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12661)

<span data-ttu-id="57ff5-389">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-389">**Old behavior**</span></span>

<span data-ttu-id="57ff5-390">Antes de la versión 3.0, `DeleteBehavior.Restrict` creaba claves externas en la base de datos con la semántica `Restrict`, pero también realizaba una corrección interna de manera no evidente.</span><span class="sxs-lookup"><span data-stu-id="57ff5-390">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

<span data-ttu-id="57ff5-391">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-391">**New behavior**</span></span>

<span data-ttu-id="57ff5-392">A partir de la versión 3.0, `DeleteBehavior.Restrict` garantiza que las claves externas se crean con la semántica `Restrict`, es decir, sin cascadas y realizando una infracción de restricción, sin llevar a cabo correcciones internas de EF.</span><span class="sxs-lookup"><span data-stu-id="57ff5-392">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

<span data-ttu-id="57ff5-393">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-393">**Why**</span></span>

<span data-ttu-id="57ff5-394">Este cambio se realizó para mejorar la experiencia de uso de `DeleteBehavior` de manera intuitiva sin efectos secundarios inesperados.</span><span class="sxs-lookup"><span data-stu-id="57ff5-394">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

<span data-ttu-id="57ff5-395">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-395">**Mitigations**</span></span>

<span data-ttu-id="57ff5-396">El comportamiento anterior se puede restaurar con `DeleteBehavior.ClientNoAction`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-396">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>
### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="57ff5-397">Los tipos de consulta se consolidan con tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="57ff5-397">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="57ff5-398">Problema de seguimiento n.º 14194</span><span class="sxs-lookup"><span data-stu-id="57ff5-398">Tracking Issue #14194</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14194)

<span data-ttu-id="57ff5-399">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-399">**Old behavior**</span></span>

<span data-ttu-id="57ff5-400">Antes de EF Core 3.0, los [tipos de consulta](xref:core/modeling/keyless-entity-types) eran un medio para consultar los datos que no definen una clave principal de una manera estructurada.</span><span class="sxs-lookup"><span data-stu-id="57ff5-400">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="57ff5-401">Es decir, un tipo de consulta se usaba para asignar tipos de entidad sin claves (más probablemente desde una vista, pero posiblemente desde una tabla), mientras que un tipo de entidad estándar se usaba cuando había una clave disponible (más probablemente desde una tabla, pero posiblemente desde una vista).</span><span class="sxs-lookup"><span data-stu-id="57ff5-401">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

<span data-ttu-id="57ff5-402">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-402">**New behavior**</span></span>

<span data-ttu-id="57ff5-403">Ahora un tipo de consulta se convierte en un tipo de entidad sin una clave principal.</span><span class="sxs-lookup"><span data-stu-id="57ff5-403">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="57ff5-404">Los tipos de entidad sin clave tienen la misma funcionalidad que los tipos de consulta de las versiones anteriores.</span><span class="sxs-lookup"><span data-stu-id="57ff5-404">Keyless entity types have the same functionality as query types in previous versions.</span></span>

<span data-ttu-id="57ff5-405">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-405">**Why**</span></span>

<span data-ttu-id="57ff5-406">Este cambio se ha realizado para reducir la confusión en torno a la finalidad de los tipos de consulta.</span><span class="sxs-lookup"><span data-stu-id="57ff5-406">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="57ff5-407">En concreto, son tipos de entidad sin clave y, por ello, intrínsecamente son de solo lectura, pero no se deben usar solo porque un tipo de entidad tenga que ser de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="57ff5-407">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="57ff5-408">Del mismo modo, se suelen asignar a vistas, pero solo porque las vistas no suelen definir claves.</span><span class="sxs-lookup"><span data-stu-id="57ff5-408">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

<span data-ttu-id="57ff5-409">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-409">**Mitigations**</span></span>

<span data-ttu-id="57ff5-410">Los elementos siguientes de la API ahora están obsoletos:</span><span class="sxs-lookup"><span data-stu-id="57ff5-410">The following parts of the API are now obsolete:</span></span>

* <span data-ttu-id="57ff5-411">**`ModelBuilder.Query<>()`** : en su lugar es necesario llamar a `ModelBuilder.Entity<>().HasNoKey()` para marcar un tipo de entidad como sin claves.</span><span class="sxs-lookup"><span data-stu-id="57ff5-411">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="57ff5-412">Esto todavía no se configurará por convención para evitar una configuración incorrecta cuando se espera una clave principal, pero no coincide con la convención.</span><span class="sxs-lookup"><span data-stu-id="57ff5-412">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="57ff5-413">**`DbQuery<>`** : en su lugar se debe usar `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-413">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="57ff5-414">**`DbContext.Query<>()`** : en su lugar se debe usar `DbContext.Set<>()`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-414">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>
* <span data-ttu-id="57ff5-415">**`IQueryTypeConfiguration<TQuery>`** : en su lugar se debe usar `IEntityTypeConfiguration<TEntity>`\*\*.</span><span class="sxs-lookup"><span data-stu-id="57ff5-415">**`IQueryTypeConfiguration<TQuery>`** - Instead `IEntityTypeConfiguration<TEntity>`\*\* should be used.</span></span>

> [!NOTE]
> <span data-ttu-id="57ff5-416">Debido a [un problema en la versión 3.x](https://github.com/dotnet/efcore/issues/19537), al consultar entidades sin clave que tienen todas las propiedades establecidas en `null` se devolverá un `null` en lugar de una entidad, si este problema es aplicable a su escenario, agregue también lógica para administrar `null` en los resultados.</span><span class="sxs-lookup"><span data-stu-id="57ff5-416">Due to [an issue in 3.x](https://github.com/dotnet/efcore/issues/19537) when querying keyless entities that have all properties set to `null` a `null` will be returned instead of an entity, if this issue is applicable to your scenario also add logic to handle `null` in results.</span></span>

<a name="config"></a>
### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="57ff5-417">La API de configuración para las relaciones de tipo de propiedad ha cambiado</span><span class="sxs-lookup"><span data-stu-id="57ff5-417">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="57ff5-418">[Problema de seguimiento n.º 12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Problema de seguimiento n.º 9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Problema de seguimiento n.º 14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="57ff5-418">[Tracking Issue #12444](https://github.com/aspnet/EntityFrameworkCore/issues/12444)
[Tracking Issue #9148](https://github.com/aspnet/EntityFrameworkCore/issues/9148)
[Tracking Issue #14153](https://github.com/aspnet/EntityFrameworkCore/issues/14153)</span></span>

<span data-ttu-id="57ff5-419">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-419">**Old behavior**</span></span>

<span data-ttu-id="57ff5-420">Antes de EF Core 3.0, la configuración de la relación de propiedad se realizaba directamente después de la llamada a `OwnsOne` o `OwnsMany`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-420">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span> 

<span data-ttu-id="57ff5-421">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-421">**New behavior**</span></span>

<span data-ttu-id="57ff5-422">A partir de EF Core 3.0, ahora hay una API fluida para configurar una propiedad de navegación para el propietario mediante `WithOwner()`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-422">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="57ff5-423">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-423">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="57ff5-424">La configuración relacionada con la relación entre el propietario y lo que se posee ahora se debe encadenar después de `WithOwner()`, de forma similar a cómo se configuran otras relaciones.</span><span class="sxs-lookup"><span data-stu-id="57ff5-424">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="57ff5-425">Pero la configuración del propio tipo de propiedad se seguirá encadenando después de `OwnsOne()/OwnsMany()`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-425">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="57ff5-426">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-426">For example:</span></span>

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

<span data-ttu-id="57ff5-427">Además, la llamada a `Entity()`, `HasOne()` o `Set()` con un tipo de propiedad de destino ahora iniciará una excepción.</span><span class="sxs-lookup"><span data-stu-id="57ff5-427">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

<span data-ttu-id="57ff5-428">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-428">**Why**</span></span>

<span data-ttu-id="57ff5-429">Este cambio se ha realizado para crear una separación más clara entre la configuración del propio tipo de propiedad y la _relación con_ el tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="57ff5-429">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="57ff5-430">A su vez, esto elimina la ambigüedad y la confusión de métodos como `HasForeignKey`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-430">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

<span data-ttu-id="57ff5-431">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-431">**Mitigations**</span></span>

<span data-ttu-id="57ff5-432">Cambie la configuración de las relaciones de tipo de propiedad para usar la nueva superficie de API, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="57ff5-432">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="57ff5-433">Ahora, las entidades dependientes que comparten la tabla con la entidad de seguridad son opcionales</span><span class="sxs-lookup"><span data-stu-id="57ff5-433">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="57ff5-434">Problema de seguimiento n.º 9005</span><span class="sxs-lookup"><span data-stu-id="57ff5-434">Tracking Issue #9005</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9005)

<span data-ttu-id="57ff5-435">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-435">**Old behavior**</span></span>

<span data-ttu-id="57ff5-436">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="57ff5-436">Consider the following model:</span></span>
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
<span data-ttu-id="57ff5-437">Antes de EF Core 3.0, si `OrderDetails` era propiedad de `Order` o estaba asignado explícitamente a la misma tabla, siempre era necesaria una instancia de `OrderDetails` al agregar un elemento `Order` nuevo.</span><span class="sxs-lookup"><span data-stu-id="57ff5-437">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>


<span data-ttu-id="57ff5-438">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-438">**New behavior**</span></span>

<span data-ttu-id="57ff5-439">A partir de la versión 3.0, EF Core permite agregar `Order` sin `OrderDetails` y asigna todas las propiedades `OrderDetails` a excepción de la clave principal a columnas que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="57ff5-439">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="57ff5-440">Al realizar consultas, EF Core establece `OrderDetails` en `null` si ninguna de las propiedades necesarias tiene un valor o si no tiene propiedades necesarias más allá de la clave principal y todas las propiedades son `null`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-440">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

<span data-ttu-id="57ff5-441">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-441">**Mitigations**</span></span>

<span data-ttu-id="57ff5-442">Si el modelo tiene una tabla que comparte dependencias con todas las columnas opcionales, pero la navegación que apunta a ella no se espera que sea `null`, la aplicación debería modificarse para controlar los casos en los que la navegación sea `null`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-442">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="57ff5-443">Si esto no es posible, debería agregarse una propiedad necesaria al tipo de entidad o, al menos, una entidad debería tener un valor distinto a `null` asignado.</span><span class="sxs-lookup"><span data-stu-id="57ff5-443">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="57ff5-444">Todas las entidades que compartan una tabla con una columna de token de simultaneidad tienen que asignarla a una propiedad</span><span class="sxs-lookup"><span data-stu-id="57ff5-444">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="57ff5-445">Problema de seguimiento n.º 14154</span><span class="sxs-lookup"><span data-stu-id="57ff5-445">Tracking Issue #14154</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14154)

<span data-ttu-id="57ff5-446">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-446">**Old behavior**</span></span>

<span data-ttu-id="57ff5-447">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="57ff5-447">Consider the following model:</span></span>
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
<span data-ttu-id="57ff5-448">Antes de EF Core 3.0, si `OrderDetails` era propiedad de `Order` o estaba asignado explícitamente a la misma tabla, si solo se actualizaba `OrderDetails`, no se actualizaba el valor `Version` en el cliente y se producía un error en la próxima actualización.</span><span class="sxs-lookup"><span data-stu-id="57ff5-448">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>


<span data-ttu-id="57ff5-449">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-449">**New behavior**</span></span>

<span data-ttu-id="57ff5-450">A partir de la versión 3.0, EF Core propaga el nuevo valor `Version` en `Order` si posee `OrderDetails`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-450">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="57ff5-451">En caso contrario, se produce una excepción durante la validación del modelo.</span><span class="sxs-lookup"><span data-stu-id="57ff5-451">Otherwise an exception is thrown during model validation.</span></span>

<span data-ttu-id="57ff5-452">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-452">**Why**</span></span>

<span data-ttu-id="57ff5-453">Este cambio se realizó para evitar un valor de token de simultaneidad obsoleto cuando solo se actualiza una de las entidades asignadas a la misma tabla.</span><span class="sxs-lookup"><span data-stu-id="57ff5-453">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

<span data-ttu-id="57ff5-454">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-454">**Mitigations**</span></span>

<span data-ttu-id="57ff5-455">Todas las entidades que comparten la tabla deben incluir una propiedad que se asigna a la columna del token de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="57ff5-455">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="57ff5-456">Es posible crear una en estado reemplazado:</span><span class="sxs-lookup"><span data-stu-id="57ff5-456">It's possible the create one in shadow-state:</span></span>
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="57ff5-457">Las entidades en propiedad no se pueden consultar sin el propietario mediante una consulta de seguimiento</span><span class="sxs-lookup"><span data-stu-id="57ff5-457">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="57ff5-458">Problema de seguimiento n.º 18876</span><span class="sxs-lookup"><span data-stu-id="57ff5-458">Tracking Issue #18876</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/18876)

<span data-ttu-id="57ff5-459">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-459">**Old behavior**</span></span>

<span data-ttu-id="57ff5-460">Antes de EF Core 3.0, las entidades en propiedad se podían consultar como cualquier otra navegación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-460">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

<span data-ttu-id="57ff5-461">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-461">**New behavior**</span></span>

<span data-ttu-id="57ff5-462">A partir de la versión 3.0, EF Core iniciará una excepción si una consulta de seguimiento proyecta una entidad en propiedad sin el propietario.</span><span class="sxs-lookup"><span data-stu-id="57ff5-462">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

<span data-ttu-id="57ff5-463">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-463">**Why**</span></span>

<span data-ttu-id="57ff5-464">Las entidades en propiedad no se pueden manipular sin el propietario, por lo que en la mayoría de los casos es un error consultarlas de esta manera.</span><span class="sxs-lookup"><span data-stu-id="57ff5-464">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

<span data-ttu-id="57ff5-465">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-465">**Mitigations**</span></span>

<span data-ttu-id="57ff5-466">Si se debe realizar el seguimiento de la entidad en propiedad para modificarla de cualquier manera posterior, el propietario se debe incluir en la consulta.</span><span class="sxs-lookup"><span data-stu-id="57ff5-466">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="57ff5-467">De lo contrario, agregue una llamada a `AsNoTracking()`:</span><span class="sxs-lookup"><span data-stu-id="57ff5-467">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="57ff5-468">Ahora, las propiedades heredadas de tipos sin asignar se asignan a una única columna para todos los tipos derivados</span><span class="sxs-lookup"><span data-stu-id="57ff5-468">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="57ff5-469">Problema de seguimiento n.º 13998</span><span class="sxs-lookup"><span data-stu-id="57ff5-469">Tracking Issue #13998</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13998)

<span data-ttu-id="57ff5-470">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-470">**Old behavior**</span></span>

<span data-ttu-id="57ff5-471">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="57ff5-471">Consider the following model:</span></span>
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

<span data-ttu-id="57ff5-472">Antes de EF Core 3.0, la propiedad `ShippingAddress` se asignaba a columnas distintas para `BulkOrder` y `Order` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="57ff5-472">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

<span data-ttu-id="57ff5-473">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-473">**New behavior**</span></span>

<span data-ttu-id="57ff5-474">A partir de la versión3.0, EF Core solo crea una columna para `ShippingAddress`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-474">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

<span data-ttu-id="57ff5-475">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-475">**Why**</span></span>

<span data-ttu-id="57ff5-476">El comportamiento anterior no era el esperado.</span><span class="sxs-lookup"><span data-stu-id="57ff5-476">The old behavoir was unexpected.</span></span>

<span data-ttu-id="57ff5-477">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-477">**Mitigations**</span></span>

<span data-ttu-id="57ff5-478">Todavía se puede asignar explícitamente la propiedad a columnas separadas en los tipos derivados:</span><span class="sxs-lookup"><span data-stu-id="57ff5-478">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="57ff5-479">La convención de propiedad de clave externa ya no coincide con el mismo nombre que la propiedad de entidad de seguridad</span><span class="sxs-lookup"><span data-stu-id="57ff5-479">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="57ff5-480">Problema de seguimiento n.º 13274</span><span class="sxs-lookup"><span data-stu-id="57ff5-480">Tracking Issue #13274</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13274)

<span data-ttu-id="57ff5-481">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-481">**Old behavior**</span></span>

<span data-ttu-id="57ff5-482">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="57ff5-482">Consider the following model:</span></span>
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
<span data-ttu-id="57ff5-483">Antes de EF Core 3.0, se podía usar la propiedad `CustomerId` para la clave externa por convención.</span><span class="sxs-lookup"><span data-stu-id="57ff5-483">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="57ff5-484">Pero si `Order` es un tipo de propiedad, entonces esto convertiría también a `CustomerId` en la clave principal, algo que no suele ser lo esperado.</span><span class="sxs-lookup"><span data-stu-id="57ff5-484">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

<span data-ttu-id="57ff5-485">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-485">**New behavior**</span></span>

<span data-ttu-id="57ff5-486">A partir de la versión 3.0, EF Core no intenta usar las propiedades de claves externas por convención si tienen el mismo nombre que la propiedad de entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="57ff5-486">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="57ff5-487">Los patrones de nombre de tipo de entidad de seguridad concatenado con el nombre de propiedad de la entidad de seguridad y de nombre de navegación concatenado con el nombre de propiedad de la entidad de seguridad todavía se hacen coincidir.</span><span class="sxs-lookup"><span data-stu-id="57ff5-487">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="57ff5-488">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-488">For example:</span></span>

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

<span data-ttu-id="57ff5-489">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-489">**Why**</span></span>

<span data-ttu-id="57ff5-490">Este cambio se ha realizado para evitar definir erróneamente una propiedad de clave principal en el tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="57ff5-490">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

<span data-ttu-id="57ff5-491">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-491">**Mitigations**</span></span>

<span data-ttu-id="57ff5-492">Si la propiedad se ha diseñado para ser la clave externa y, por tanto, parte de la clave principal, se debe configurar explícitamente como tal.</span><span class="sxs-lookup"><span data-stu-id="57ff5-492">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="57ff5-493">Ahora, la conexión de base de datos se cierra si ya no se usa antes de que se complete TransactionScope</span><span class="sxs-lookup"><span data-stu-id="57ff5-493">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="57ff5-494">Problema de seguimiento n.º 14218</span><span class="sxs-lookup"><span data-stu-id="57ff5-494">Tracking Issue #14218</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14218)

<span data-ttu-id="57ff5-495">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-495">**Old behavior**</span></span>

<span data-ttu-id="57ff5-496">Antes de EF Core 3.0, si el contexto abría la conexión dentro de `TransactionScope`, la conexión permanecía abierta mientras el ámbito actual `TransactionScope` estuviese activo.</span><span class="sxs-lookup"><span data-stu-id="57ff5-496">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

<span data-ttu-id="57ff5-497">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-497">**New behavior**</span></span>

<span data-ttu-id="57ff5-498">A partir de la versión 3.0, EF Core cierra la conexión en cuanto se deja de usar.</span><span class="sxs-lookup"><span data-stu-id="57ff5-498">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

<span data-ttu-id="57ff5-499">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-499">**Why**</span></span>

<span data-ttu-id="57ff5-500">Este cambio permite usar varios contextos en el mismo ámbito `TransactionScope`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-500">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="57ff5-501">El comportamiento nuevo también coincide con el de EF6.</span><span class="sxs-lookup"><span data-stu-id="57ff5-501">The new behavior also matches EF6.</span></span>

<span data-ttu-id="57ff5-502">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-502">**Mitigations**</span></span>

<span data-ttu-id="57ff5-503">Si la conexión debe permanecer abierta, una llamada explícita a `OpenConnection()` asegurará que EF Core no la cierre de forma prematura:</span><span class="sxs-lookup"><span data-stu-id="57ff5-503">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="57ff5-504">Cada propiedad usa la generación de claves enteras en memoria independiente</span><span class="sxs-lookup"><span data-stu-id="57ff5-504">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="57ff5-505">Problema de seguimiento n.º 6872</span><span class="sxs-lookup"><span data-stu-id="57ff5-505">Tracking Issue #6872</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/6872)

<span data-ttu-id="57ff5-506">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-506">**Old behavior**</span></span>

<span data-ttu-id="57ff5-507">Antes de EF Core 3.0, se usaba un generador de valores compartidos para todas las propiedades de clave entera en memoria.</span><span class="sxs-lookup"><span data-stu-id="57ff5-507">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

<span data-ttu-id="57ff5-508">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-508">**New behavior**</span></span>

<span data-ttu-id="57ff5-509">A partir de EF Core 3.0, cada propiedad de clave entera obtiene su propio generador de valores cuando se usa la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="57ff5-509">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="57ff5-510">Además, si se elimina la base de datos, la generación de claves se restablece para todas las tablas.</span><span class="sxs-lookup"><span data-stu-id="57ff5-510">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

<span data-ttu-id="57ff5-511">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-511">**Why**</span></span>

<span data-ttu-id="57ff5-512">Este cambio se ha realizado para alinear la generación de claves en memoria más estrechamente a la generación de claves de base de datos reales y para mejorar la capacidad para aislar las pruebas entre sí cuando se usa la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="57ff5-512">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

<span data-ttu-id="57ff5-513">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-513">**Mitigations**</span></span>

<span data-ttu-id="57ff5-514">Esto puede interrumpir una aplicación que se base en el establecimiento de valores de clave específicos en memoria.</span><span class="sxs-lookup"><span data-stu-id="57ff5-514">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="57ff5-515">En su lugar, considere la posibilidad de no depender de valores de clave específicos, o bien de actualizar para que coincida con el comportamiento nuevo.</span><span class="sxs-lookup"><span data-stu-id="57ff5-515">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="57ff5-516">Los campos de respaldo se usan de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="57ff5-516">Backing fields are used by default</span></span>

[<span data-ttu-id="57ff5-517">Problema de seguimiento n.º 12430</span><span class="sxs-lookup"><span data-stu-id="57ff5-517">Tracking Issue #12430</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12430)

<span data-ttu-id="57ff5-518">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-518">**Old behavior**</span></span>

<span data-ttu-id="57ff5-519">Antes de la versión 3.0, incluso si se conocía el campo de respaldo de una propiedad, de forma predeterminada en EF Core se leía y escribía el valor de propiedad mediante los métodos captadores y establecedores de propiedades.</span><span class="sxs-lookup"><span data-stu-id="57ff5-519">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="57ff5-520">La excepción era la ejecución de consultas, donde el campo de respaldo se establecía directamente si se conocía.</span><span class="sxs-lookup"><span data-stu-id="57ff5-520">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

<span data-ttu-id="57ff5-521">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-521">**New behavior**</span></span>

<span data-ttu-id="57ff5-522">A partir de EF Core 3.0, si se conoce el campo de respaldo para una propiedad, EF Core siempre la leerá y escribirá mediante el campo de respaldo.</span><span class="sxs-lookup"><span data-stu-id="57ff5-522">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="57ff5-523">Esto podría provocar una interrupción de la aplicación si depende de un comportamiento adicional codificado en los métodos captadores o establecedores.</span><span class="sxs-lookup"><span data-stu-id="57ff5-523">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

<span data-ttu-id="57ff5-524">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-524">**Why**</span></span>

<span data-ttu-id="57ff5-525">Este cambio se ha realizado para evitar que EF Core desencadene erróneamente lógica de negocios de forma predeterminada al realizar operaciones de base de datos que implican entidades.</span><span class="sxs-lookup"><span data-stu-id="57ff5-525">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

<span data-ttu-id="57ff5-526">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-526">**Mitigations**</span></span>

<span data-ttu-id="57ff5-527">El comportamiento anterior a la versión 3.0 se puede restaurar mediante la configuración del modo de acceso de propiedad en `ModelBuilder`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-527">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="57ff5-528">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-528">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="57ff5-529">Inicio de excepciones si se encuentran varios campos de respaldo compatibles</span><span class="sxs-lookup"><span data-stu-id="57ff5-529">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="57ff5-530">Problema de seguimiento n.º 12523</span><span class="sxs-lookup"><span data-stu-id="57ff5-530">Tracking Issue #12523</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12523)

<span data-ttu-id="57ff5-531">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-531">**Old behavior**</span></span>

<span data-ttu-id="57ff5-532">Antes de EF Core 3.0, si varios campos coincidían con las reglas para buscar el campo de respaldo de una propiedad, se elegía un campo según un orden de prioridad.</span><span class="sxs-lookup"><span data-stu-id="57ff5-532">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="57ff5-533">Esto podía producir que, en caso de ambigüedad, se usara el campo incorrecto.</span><span class="sxs-lookup"><span data-stu-id="57ff5-533">This could cause the wrong field to be used in ambiguous cases.</span></span>

<span data-ttu-id="57ff5-534">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-534">**New behavior**</span></span>

<span data-ttu-id="57ff5-535">A partir de EF Core 3.0, si varios campos coinciden con la misma propiedad, se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="57ff5-535">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

<span data-ttu-id="57ff5-536">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-536">**Why**</span></span>

<span data-ttu-id="57ff5-537">Este cambio se ha realizado para evitar de forma silenciosa el uso de un campo con respecto a otro cuando solo uno puede ser correcto.</span><span class="sxs-lookup"><span data-stu-id="57ff5-537">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

<span data-ttu-id="57ff5-538">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-538">**Mitigations**</span></span>

<span data-ttu-id="57ff5-539">En las propiedades con campos de respaldo ambiguos se debe especificar de forma explícita el campo que se va usar.</span><span class="sxs-lookup"><span data-stu-id="57ff5-539">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="57ff5-540">Por ejemplo, con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="57ff5-540">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="57ff5-541">Los nombres de propiedades de solo campo deben coincidir con el nombre del campo</span><span class="sxs-lookup"><span data-stu-id="57ff5-541">Field-only property names should match the field name</span></span>

<span data-ttu-id="57ff5-542">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-542">**Old behavior**</span></span>

<span data-ttu-id="57ff5-543">Antes de EF Core 3.0, una propiedad podía especificarse con un valor de cadena y, si no había ninguna propiedad con ese nombre en el tipo .NET, EF Core intentaba hacerla coincidir con un campo mediante reglas de convención.</span><span class="sxs-lookup"><span data-stu-id="57ff5-543">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

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

<span data-ttu-id="57ff5-544">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-544">**New behavior**</span></span>

<span data-ttu-id="57ff5-545">A partir de EF Core 3.0, una propiedad de solo campo debe coincidir exactamente con el nombre del campo.</span><span class="sxs-lookup"><span data-stu-id="57ff5-545">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

<span data-ttu-id="57ff5-546">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-546">**Why**</span></span>

<span data-ttu-id="57ff5-547">Este cambio se realizó para evitar el uso del mismo campo para dos propiedades con nombres similares. También hace que las reglas de coincidencia para propiedades solo de campo sean las mismas que para las propiedades asignadas a propiedades CLR.</span><span class="sxs-lookup"><span data-stu-id="57ff5-547">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

<span data-ttu-id="57ff5-548">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-548">**Mitigations**</span></span>

<span data-ttu-id="57ff5-549">Las propiedades solo de campo deberían tener el mismo nombre que el campo al que están asignadas.</span><span class="sxs-lookup"><span data-stu-id="57ff5-549">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="57ff5-550">En una próxima versión de EF Core 3.0 tenemos planeado volver a habilitar la configuración explícita de un nombre de campo distinto al nombre de la propiedad (vea el problema [n.° 15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span><span class="sxs-lookup"><span data-stu-id="57ff5-550">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/aspnet/EntityFrameworkCore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="57ff5-551">AddDbContext/AddDbContextPool ya no llaman a AddLogging ni a AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="57ff5-551">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="57ff5-552">Problema de seguimiento n.º 14756</span><span class="sxs-lookup"><span data-stu-id="57ff5-552">Tracking Issue #14756</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14756)

<span data-ttu-id="57ff5-553">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-553">**Old behavior**</span></span>

<span data-ttu-id="57ff5-554">Antes de EF Core 3.0, la llamada a `AddDbContext` o `AddDbContextPool` también podría registrar los servicios de almacenamiento en caché y de registro con inserción de dependencias a través de llamadas a [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) y [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="57ff5-554">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<span data-ttu-id="57ff5-555">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-555">**New behavior**</span></span>

<span data-ttu-id="57ff5-556">A partir de EF Core 3.0, `AddDbContext` y `AddDbContextPool` ya no registrarán estos servicios con inserción de dependencias (DI).</span><span class="sxs-lookup"><span data-stu-id="57ff5-556">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

<span data-ttu-id="57ff5-557">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-557">**Why**</span></span>

<span data-ttu-id="57ff5-558">EF Core 3.0 no requiere que estos servicios estén en el contenedor de inserción de dependencias de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-558">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="57ff5-559">Pero si `ILoggerFactory` se registra en el contenedor de DI de la aplicación, EF Core lo empezará a usar de todos modos.</span><span class="sxs-lookup"><span data-stu-id="57ff5-559">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

<span data-ttu-id="57ff5-560">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-560">**Mitigations**</span></span>

<span data-ttu-id="57ff5-561">Si la aplicación necesita estos servicios, regístrelos de manera explícita con el contenedor de DI mediante [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) o [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="57ff5-561">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="57ff5-562">AddEntityFramework\* agrega IMemoryCache con un límite de tamaño</span><span class="sxs-lookup"><span data-stu-id="57ff5-562">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="57ff5-563">Problema de seguimiento n.º 12905</span><span class="sxs-lookup"><span data-stu-id="57ff5-563">Tracking Issue #12905</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12905)

<span data-ttu-id="57ff5-564">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-564">**Old behavior**</span></span>

<span data-ttu-id="57ff5-565">Antes de EF Core 3.0, la llamada a los métodos `AddEntityFramework*` también registraba los servicios de almacenamiento en caché de memoria con inserción de dependencias sin límite de tamaño.</span><span class="sxs-lookup"><span data-stu-id="57ff5-565">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

<span data-ttu-id="57ff5-566">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-566">**New behavior**</span></span>

<span data-ttu-id="57ff5-567">A partir de EF Core 3.0, `AddEntityFramework*` registrará un servicio IMemoryCache con un límite de tamaño.</span><span class="sxs-lookup"><span data-stu-id="57ff5-567">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="57ff5-568">Si otros servicios agregados después dependen de IMemoryCache, pueden alcanzar rápidamente el límite predeterminado y provocar excepciones o un rendimiento degradado.</span><span class="sxs-lookup"><span data-stu-id="57ff5-568">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

<span data-ttu-id="57ff5-569">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-569">**Why**</span></span>

<span data-ttu-id="57ff5-570">El uso de IMemoryCache sin un límite podría dar lugar a un uso de memoria no controlado si hay un error en la lógica de almacenamiento en caché de las consultas o las consultas se generan de forma dinámica.</span><span class="sxs-lookup"><span data-stu-id="57ff5-570">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="57ff5-571">Tener un límite predeterminado mitiga un posible ataque DoS.</span><span class="sxs-lookup"><span data-stu-id="57ff5-571">Having a default limit mitigates a potential DoS attack.</span></span>

<span data-ttu-id="57ff5-572">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-572">**Mitigations**</span></span>

<span data-ttu-id="57ff5-573">En la mayoría de los casos, no es necesario llamar a `AddEntityFramework*` si también se llama a `AddDbContext` o `AddDbContextPool`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-573">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="57ff5-574">Por tanto, la mejor mitigación consiste en quitar la llamada a `AddEntityFramework*`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-574">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="57ff5-575">Si la aplicación necesita estos servicios, registre de forma explícita una implementación de IMemoryCache con el contenedor de DI por anticipado mediante [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="57ff5-575">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="57ff5-576">Ahora DbContext.Entry realiza una operación DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="57ff5-576">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="57ff5-577">Problema de seguimiento n.º 13552</span><span class="sxs-lookup"><span data-stu-id="57ff5-577">Tracking Issue #13552</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13552)

<span data-ttu-id="57ff5-578">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-578">**Old behavior**</span></span>

<span data-ttu-id="57ff5-579">Antes de EF Core 3.0, la llamada a `DbContext.Entry` provocaba que se detectaran cambios para todas las entidades con seguimiento.</span><span class="sxs-lookup"><span data-stu-id="57ff5-579">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="57ff5-580">Esto garantizaba que el estado expuesto en `EntityEntry` estuviera actualizado.</span><span class="sxs-lookup"><span data-stu-id="57ff5-580">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

<span data-ttu-id="57ff5-581">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-581">**New behavior**</span></span>

<span data-ttu-id="57ff5-582">A partir de EF Core 3.0, ahora la llamada a `DbContext.Entry` solo intenta detectar cambios en la entidad dada y cualquier entidad de seguridad relacionada con ella de la que se haya realizado el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="57ff5-582">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="57ff5-583">Esto significa que es posible que la llamada a este método no haya detectado otros cambios, lo que podría tener implicaciones en el estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-583">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="57ff5-584">Observe que si `ChangeTracker.AutoDetectChangesEnabled` se establece en `false` incluso esta detección de cambios local se deshabilitará.</span><span class="sxs-lookup"><span data-stu-id="57ff5-584">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="57ff5-585">Otros métodos que provocan la detección de cambios (como `ChangeTracker.Entries` y `SaveChanges`) siguen provocando una acción `DetectChanges` completa de todas las entidades de las que se realiza el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="57ff5-585">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

<span data-ttu-id="57ff5-586">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-586">**Why**</span></span>

<span data-ttu-id="57ff5-587">Este cambio se ha realizado para mejorar el rendimiento predeterminado del uso de `context.Entry`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-587">This change was made to improve the default performance of using `context.Entry`.</span></span>

<span data-ttu-id="57ff5-588">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-588">**Mitigations**</span></span>

<span data-ttu-id="57ff5-589">Llame a `ChangeTracker.DetectChanges()` de forma explícita antes de llamar a `Entry` para garantizar el comportamiento anterior a la versión 3.0.</span><span class="sxs-lookup"><span data-stu-id="57ff5-589">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="57ff5-590">El cliente no genera las claves de matriz de cadena y byte de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="57ff5-590">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="57ff5-591">Problema de seguimiento n.º 14617</span><span class="sxs-lookup"><span data-stu-id="57ff5-591">Tracking Issue #14617</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14617)

<span data-ttu-id="57ff5-592">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-592">**Old behavior**</span></span>

<span data-ttu-id="57ff5-593">Antes de EF Core 3.0, se podían usar las propiedades de clave `string` y `byte[]` sin tener que establecer de forma explícita un valor distinto de NULL.</span><span class="sxs-lookup"><span data-stu-id="57ff5-593">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="57ff5-594">En ese caso, el valor de clave se generaba en el cliente como un GUID, que se serializaba en bytes para `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-594">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

<span data-ttu-id="57ff5-595">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-595">**New behavior**</span></span>

<span data-ttu-id="57ff5-596">A partir de EF Core 3.0, se iniciará una excepción en la que indica que no se ha establecido ningún valor de clave.</span><span class="sxs-lookup"><span data-stu-id="57ff5-596">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

<span data-ttu-id="57ff5-597">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-597">**Why**</span></span>

<span data-ttu-id="57ff5-598">Este cambio se ha realizado porque los valores `string`/`byte[]` generados por el cliente no suelen ser útiles, y el comportamiento predeterminado dificultaba razonar sobre los valores de clave generados de una forma habitual.</span><span class="sxs-lookup"><span data-stu-id="57ff5-598">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

<span data-ttu-id="57ff5-599">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-599">**Mitigations**</span></span>

<span data-ttu-id="57ff5-600">Se puede obtener el comportamiento anterior a la versión 3.0 si se especifica de forma explícita que las propiedades de clave deben usar los valores generados si no se establece ningún otro valor distinto de NULL.</span><span class="sxs-lookup"><span data-stu-id="57ff5-600">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="57ff5-601">Por ejemplo, con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="57ff5-601">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="57ff5-602">O bien con anotaciones de datos:</span><span class="sxs-lookup"><span data-stu-id="57ff5-602">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="57ff5-603">Ahora ILoggerFactory es un servicio con ámbito</span><span class="sxs-lookup"><span data-stu-id="57ff5-603">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="57ff5-604">Problema de seguimiento n.º 14698</span><span class="sxs-lookup"><span data-stu-id="57ff5-604">Tracking Issue #14698</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14698)

<span data-ttu-id="57ff5-605">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-605">**Old behavior**</span></span>

<span data-ttu-id="57ff5-606">Antes de EF Core 3.0, `ILoggerFactory` se registraba como un servicio de singleton.</span><span class="sxs-lookup"><span data-stu-id="57ff5-606">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

<span data-ttu-id="57ff5-607">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-607">**New behavior**</span></span>

<span data-ttu-id="57ff5-608">A partir de EF Core 3.0, `ILoggerFactory` ahora se registra como con ámbito.</span><span class="sxs-lookup"><span data-stu-id="57ff5-608">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

<span data-ttu-id="57ff5-609">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-609">**Why**</span></span>

<span data-ttu-id="57ff5-610">Este cambio se ha realizado para permitir la asociación de un registrador con una instancia de `DbContext`, lo que habilita otras funciones y quita algunos casos de comportamiento patológico como un aumento vertiginoso de los proveedores de servicios internos.</span><span class="sxs-lookup"><span data-stu-id="57ff5-610">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

<span data-ttu-id="57ff5-611">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-611">**Mitigations**</span></span>

<span data-ttu-id="57ff5-612">Este cambio no debería afectar al código de la aplicación a menos que registre y use servicios personalizados en el proveedor de servicios internos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="57ff5-612">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="57ff5-613">Esto no es habitual.</span><span class="sxs-lookup"><span data-stu-id="57ff5-613">This isn't common.</span></span>
<span data-ttu-id="57ff5-614">En estos casos, la mayoría de los elementos seguirá funcionando, pero cualquier servicio de singleton que dependiera de `ILoggerFactory` tendrá que cambiarse para obtener la interfaz `ILoggerFactory` de otra forma.</span><span class="sxs-lookup"><span data-stu-id="57ff5-614">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="57ff5-615">Si experimenta situaciones como esta, registre un problema en el [rastreador de problemas de GitHub de EF Core](https://github.com/aspnet/EntityFrameworkCore/issues) para hacernos saber cómo usa `ILoggerFactory`, para que podamos comprender mejor cómo evitar esta interrupción en el futuro.</span><span class="sxs-lookup"><span data-stu-id="57ff5-615">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/aspnet/EntityFrameworkCore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="57ff5-616">En los proxies de carga diferida ya no se supone que las propiedades de navegación están totalmente cargadas</span><span class="sxs-lookup"><span data-stu-id="57ff5-616">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="57ff5-617">Problema de seguimiento n.º 12780</span><span class="sxs-lookup"><span data-stu-id="57ff5-617">Tracking Issue #12780</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12780)

<span data-ttu-id="57ff5-618">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-618">**Old behavior**</span></span>

<span data-ttu-id="57ff5-619">Antes de EF Core 3.0, una vez que se eliminaba `DbContext` no había ninguna forma de saber si una determinada propiedad de navegación de una entidad obtenida de ese contexto se había cargado completamente o no.</span><span class="sxs-lookup"><span data-stu-id="57ff5-619">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="57ff5-620">En su lugar, los proxies asumían que una navegación de referencia se cargaba si tenía un valor distinto de NULL, y que una navegación de colección se cargaba si no estaba vacía.</span><span class="sxs-lookup"><span data-stu-id="57ff5-620">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="57ff5-621">En estos casos, el intento de carga diferida era no operativo.</span><span class="sxs-lookup"><span data-stu-id="57ff5-621">In these cases, attempting to lazy-load would be a no-op.</span></span>

<span data-ttu-id="57ff5-622">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-622">**New behavior**</span></span>

<span data-ttu-id="57ff5-623">A partir de EF Core 3.0, los proxies realizan el seguimiento de si una propiedad de navegación se carga o no.</span><span class="sxs-lookup"><span data-stu-id="57ff5-623">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="57ff5-624">Esto significa que el intento de acceder a una propiedad de navegación que se carga después de que se haya eliminado el contexto siempre será no operativo, incluso cuando la navegación cargada está vacía o es NULL.</span><span class="sxs-lookup"><span data-stu-id="57ff5-624">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="57ff5-625">Por el contrario, el intento de acceder a una propiedad de navegación que no está cargada iniciará una excepción si el contexto se ha eliminado, incluso si la propiedad de navegación es una colección no vacía.</span><span class="sxs-lookup"><span data-stu-id="57ff5-625">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="57ff5-626">Si se produce esta situación, significa que el código de aplicación está intentando usar la carga diferida en un momento no válido y que se debe cambiar la aplicación para que lo no haga.</span><span class="sxs-lookup"><span data-stu-id="57ff5-626">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

<span data-ttu-id="57ff5-627">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-627">**Why**</span></span>

<span data-ttu-id="57ff5-628">Este cambio se ha realizado para que el comportamiento sea coherente y correcto cuando se intenta la carga diferida de una instancia de `DbContext` eliminada.</span><span class="sxs-lookup"><span data-stu-id="57ff5-628">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

<span data-ttu-id="57ff5-629">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-629">**Mitigations**</span></span>

<span data-ttu-id="57ff5-630">Actualice el código de la aplicación para que no intente la carga diferida con un contexto eliminado, o bien configúrelo para que sea no operativo, como se describe en el mensaje de la excepción.</span><span class="sxs-lookup"><span data-stu-id="57ff5-630">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="57ff5-631">La creación excesiva de proveedores de servicios internos ahora es un error de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="57ff5-631">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="57ff5-632">Problema de seguimiento n.º 10236</span><span class="sxs-lookup"><span data-stu-id="57ff5-632">Tracking Issue #10236</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10236)

<span data-ttu-id="57ff5-633">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-633">**Old behavior**</span></span>

<span data-ttu-id="57ff5-634">Antes de EF Core 3.0, se registraba una advertencia para una aplicación que creaba un número patológico de proveedores de servicios internos.</span><span class="sxs-lookup"><span data-stu-id="57ff5-634">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

<span data-ttu-id="57ff5-635">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-635">**New behavior**</span></span>

<span data-ttu-id="57ff5-636">A partir de EF Core 3.0, ahora esta advertencia se considera un error y se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="57ff5-636">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span> 

<span data-ttu-id="57ff5-637">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-637">**Why**</span></span>

<span data-ttu-id="57ff5-638">Este cambio se ha realizado para controlar mejor el código de la aplicación mediante la exposición de este caso patológico de una forma más explícita.</span><span class="sxs-lookup"><span data-stu-id="57ff5-638">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

<span data-ttu-id="57ff5-639">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-639">**Mitigations**</span></span>

<span data-ttu-id="57ff5-640">Cuando se produce este error, la acción más adecuada consiste en comprender la causa raíz y detener la creación de tantos proveedores de servicios internos.</span><span class="sxs-lookup"><span data-stu-id="57ff5-640">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="57ff5-641">Pero el error se puede convertir en una advertencia (u omitirse) mediante configuración en `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-641">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="57ff5-642">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-642">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="57ff5-643">Comportamiento nuevo de HasOne/HasMany llamado con una sola cadena</span><span class="sxs-lookup"><span data-stu-id="57ff5-643">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="57ff5-644">Problema de seguimiento n.º 9171</span><span class="sxs-lookup"><span data-stu-id="57ff5-644">Tracking Issue #9171</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9171)

<span data-ttu-id="57ff5-645">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-645">**Old behavior**</span></span>

<span data-ttu-id="57ff5-646">Antes de EF Core 3.0, el código para llamar a `HasOne` o `HasMany` con una cadena se interpretaba de manera confusa.</span><span class="sxs-lookup"><span data-stu-id="57ff5-646">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="57ff5-647">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-647">For example:</span></span>
```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="57ff5-648">El código parece relacionar `Samurai` con otro tipo de entidad mediante la propiedad de navegación `Entrance`, que puede ser privada.</span><span class="sxs-lookup"><span data-stu-id="57ff5-648">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="57ff5-649">En realidad, este código intenta crear una relación con algún tipo de entidad denominada `Entrance` sin ninguna propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-649">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

<span data-ttu-id="57ff5-650">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-650">**New behavior**</span></span>

<span data-ttu-id="57ff5-651">A partir de EF Core 3.0, el código anterior ahora hace lo que parecía que debía hacer antes.</span><span class="sxs-lookup"><span data-stu-id="57ff5-651">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

<span data-ttu-id="57ff5-652">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-652">**Why**</span></span>

<span data-ttu-id="57ff5-653">El comportamiento anterior era muy confuso, especialmente al leer el código de configuración y al buscar errores.</span><span class="sxs-lookup"><span data-stu-id="57ff5-653">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

<span data-ttu-id="57ff5-654">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-654">**Mitigations**</span></span>

<span data-ttu-id="57ff5-655">Esto solo interrumpirá las aplicaciones que configuran de manera explícita las relaciones con cadenas para nombres de tipos y sin especificar explícitamente la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-655">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="57ff5-656">Esto no es habitual.</span><span class="sxs-lookup"><span data-stu-id="57ff5-656">This is not common.</span></span>
<span data-ttu-id="57ff5-657">El comportamiento anterior se puede obtener al pasar de manera explícita `null` para el nombre de la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-657">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="57ff5-658">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-658">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="57ff5-659">El tipo de valor devuelto para varios métodos asincrónicos se ha cambiado de Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="57ff5-659">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="57ff5-660">Problema de seguimiento n.º 15184</span><span class="sxs-lookup"><span data-stu-id="57ff5-660">Tracking Issue #15184</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15184)

<span data-ttu-id="57ff5-661">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-661">**Old behavior**</span></span>

<span data-ttu-id="57ff5-662">Antes, los siguientes métodos asincrónicos devolvían `Task<T>`:</span><span class="sxs-lookup"><span data-stu-id="57ff5-662">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="57ff5-663">`ValueGenerator.NextValueAsync()` (y las clases derivadas)</span><span class="sxs-lookup"><span data-stu-id="57ff5-663">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

<span data-ttu-id="57ff5-664">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-664">**New behavior**</span></span>

<span data-ttu-id="57ff5-665">Dichos métodos ahora devuelven `ValueTask<T>` durante el mismo `T` que antes.</span><span class="sxs-lookup"><span data-stu-id="57ff5-665">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

<span data-ttu-id="57ff5-666">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-666">**Why**</span></span>

<span data-ttu-id="57ff5-667">Este cambio reduce el número de asignaciones de montones que se producen al invocar estos métodos, lo que mejora el rendimiento general.</span><span class="sxs-lookup"><span data-stu-id="57ff5-667">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

<span data-ttu-id="57ff5-668">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-668">**Mitigations**</span></span>

<span data-ttu-id="57ff5-669">Las aplicaciones que simplemente esperen las API anteriores solo necesitan recompilarse, sin que sea necesario realizar cambios en el código fuente.</span><span class="sxs-lookup"><span data-stu-id="57ff5-669">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="57ff5-670">Un uso más complejo (p. ej., pasar el valor `Task` devuelto a `Task.WhenAny()`) normalmente requiere que el valor `ValueTask<T>` devuelto se convierta en `Task<T>` mediante una llamada a `AsTask()` en él.</span><span class="sxs-lookup"><span data-stu-id="57ff5-670">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="57ff5-671">Tenga en cuenta que esto niega la reducción de asignación que implica este cambio.</span><span class="sxs-lookup"><span data-stu-id="57ff5-671">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="57ff5-672">La anotación Relational:TypeMapping ahora es simplemente TypeMapping</span><span class="sxs-lookup"><span data-stu-id="57ff5-672">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="57ff5-673">Problema de seguimiento n.º 9913</span><span class="sxs-lookup"><span data-stu-id="57ff5-673">Tracking Issue #9913</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/9913)

<span data-ttu-id="57ff5-674">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-674">**Old behavior**</span></span>

<span data-ttu-id="57ff5-675">El nombre de anotación para las anotaciones de asignación de tipos era "Relational:TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="57ff5-675">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

<span data-ttu-id="57ff5-676">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-676">**New behavior**</span></span>

<span data-ttu-id="57ff5-677">Ahora, el nombre de anotación para las anotaciones de asignación de tipos es "TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="57ff5-677">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

<span data-ttu-id="57ff5-678">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-678">**Why**</span></span>

<span data-ttu-id="57ff5-679">Ahora, las asignaciones de tipos se usan para algo más que solo para proveedores de bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="57ff5-679">Type mappings are now used for more than just relational database providers.</span></span>

<span data-ttu-id="57ff5-680">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-680">**Mitigations**</span></span>

<span data-ttu-id="57ff5-681">Esto solo interrumpirá a las aplicaciones que acceden directamente a la asignación de tipos como una anotación, lo que no es habitual.</span><span class="sxs-lookup"><span data-stu-id="57ff5-681">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="57ff5-682">La acción más apropiada para corregir es usar la superficie de API para acceder a las asignaciones de tipos en lugar de usar directamente la anotación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-682">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="57ff5-683">ToTable en un tipo derivado inicia una excepción</span><span class="sxs-lookup"><span data-stu-id="57ff5-683">ToTable on a derived type throws an exception</span></span> 

[<span data-ttu-id="57ff5-684">Problema de seguimiento n.º 11811</span><span class="sxs-lookup"><span data-stu-id="57ff5-684">Tracking Issue #11811</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11811)

<span data-ttu-id="57ff5-685">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-685">**Old behavior**</span></span>

<span data-ttu-id="57ff5-686">Antes de EF Core 3.0, la llamada a `ToTable()` en un tipo derivado se omitía, ya que la única estrategia asignación de herencia era TPH, lo que no es válido.</span><span class="sxs-lookup"><span data-stu-id="57ff5-686">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span> 

<span data-ttu-id="57ff5-687">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-687">**New behavior**</span></span>

<span data-ttu-id="57ff5-688">A partir de EF Core 3.0, y en preparación para agregar compatibilidad con TPT y TPC en una versión posterior, ahora la llamada a `ToTable()` en un tipo derivado iniciará una excepción para evitar un cambio de asignación inesperado en el futuro.</span><span class="sxs-lookup"><span data-stu-id="57ff5-688">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

<span data-ttu-id="57ff5-689">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-689">**Why**</span></span>

<span data-ttu-id="57ff5-690">En la actualidad no se considera válido asignar un tipo derivado a otra tabla.</span><span class="sxs-lookup"><span data-stu-id="57ff5-690">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="57ff5-691">Este cambio evita interrupciones en el futuro, cuando se convierta en una operación válida.</span><span class="sxs-lookup"><span data-stu-id="57ff5-691">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

<span data-ttu-id="57ff5-692">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-692">**Mitigations**</span></span>

<span data-ttu-id="57ff5-693">Quite todos los intentos de asignar tipos derivados a otras tablas.</span><span class="sxs-lookup"><span data-stu-id="57ff5-693">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="57ff5-694">ForSqlServerHasIndex se ha reemplazado por HasIndex</span><span class="sxs-lookup"><span data-stu-id="57ff5-694">ForSqlServerHasIndex replaced with HasIndex</span></span> 

[<span data-ttu-id="57ff5-695">Problema de seguimiento n.º 12366</span><span class="sxs-lookup"><span data-stu-id="57ff5-695">Tracking Issue #12366</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12366)

<span data-ttu-id="57ff5-696">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-696">**Old behavior**</span></span>

<span data-ttu-id="57ff5-697">Antes de EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` proporcionaba una manera de configurar las columnas que se usaban con `INCLUDE`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-697">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

<span data-ttu-id="57ff5-698">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-698">**New behavior**</span></span>

<span data-ttu-id="57ff5-699">A partir de EF Core 3.0, ya se admite el uso de `Include` en un índice en el nivel relacional.</span><span class="sxs-lookup"><span data-stu-id="57ff5-699">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="57ff5-700">Use `HasIndex().ForSqlServerInclude()`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-700">Use `HasIndex().ForSqlServerInclude()`.</span></span>

<span data-ttu-id="57ff5-701">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-701">**Why**</span></span>

<span data-ttu-id="57ff5-702">Este cambio se ha realizado para consolidar la API para índices con `Include` en un mismo lugar para todos los proveedores de base de datos.</span><span class="sxs-lookup"><span data-stu-id="57ff5-702">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

<span data-ttu-id="57ff5-703">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-703">**Mitigations**</span></span>

<span data-ttu-id="57ff5-704">Use la API nueva, como se ha mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="57ff5-704">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="57ff5-705">Cambios en la API de metadatos</span><span class="sxs-lookup"><span data-stu-id="57ff5-705">Metadata API changes</span></span>

[<span data-ttu-id="57ff5-706">Problema de seguimiento n.º 214</span><span class="sxs-lookup"><span data-stu-id="57ff5-706">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="57ff5-707">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-707">**New behavior**</span></span>

<span data-ttu-id="57ff5-708">Las siguientes propiedades se han convertido en métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="57ff5-708">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

<span data-ttu-id="57ff5-709">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-709">**Why**</span></span>

<span data-ttu-id="57ff5-710">Este cambio simplifica la implementación de las interfaces mencionadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="57ff5-710">This change simplifies the implementation of the aforementioned interfaces.</span></span>

<span data-ttu-id="57ff5-711">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-711">**Mitigations**</span></span>

<span data-ttu-id="57ff5-712">Use los nuevos métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="57ff5-712">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="57ff5-713">Cambios en la API de metadatos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="57ff5-713">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="57ff5-714">Problema de seguimiento n.º 214</span><span class="sxs-lookup"><span data-stu-id="57ff5-714">Tracking Issue #214</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/214)

<span data-ttu-id="57ff5-715">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-715">**New behavior**</span></span>

<span data-ttu-id="57ff5-716">Los métodos de extensión específicos del proveedor se simplificarán:</span><span class="sxs-lookup"><span data-stu-id="57ff5-716">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

<span data-ttu-id="57ff5-717">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-717">**Why**</span></span>

<span data-ttu-id="57ff5-718">Este cambio simplifica la implementación de los métodos de extensión mencionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="57ff5-718">This change simplifies the implementation of the aforementioned extension methods.</span></span>

<span data-ttu-id="57ff5-719">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-719">**Mitigations**</span></span>

<span data-ttu-id="57ff5-720">Use los nuevos métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="57ff5-720">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="57ff5-721">EF Core ya no envía pragma para el cumplimiento de SQLite FK</span><span class="sxs-lookup"><span data-stu-id="57ff5-721">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="57ff5-722">Problema de seguimiento n.º 12151</span><span class="sxs-lookup"><span data-stu-id="57ff5-722">Tracking Issue #12151</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12151)

<span data-ttu-id="57ff5-723">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-723">**Old behavior**</span></span>

<span data-ttu-id="57ff5-724">Antes de EF Core 3.0, EF Core enviaba `PRAGMA foreign_keys = 1` cuando se abría una conexión con SQLite.</span><span class="sxs-lookup"><span data-stu-id="57ff5-724">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="57ff5-725">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-725">**New behavior**</span></span>

<span data-ttu-id="57ff5-726">A partir de EF Core 3.0, EF Core ya no envía `PRAGMA foreign_keys = 1` cuando se abre una conexión con SQLite.</span><span class="sxs-lookup"><span data-stu-id="57ff5-726">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

<span data-ttu-id="57ff5-727">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-727">**Why**</span></span>

<span data-ttu-id="57ff5-728">Este cambio se ha realizado porque en EF Core se usa `SQLitePCLRaw.bundle_e_sqlite3` de forma predeterminada, lo que a su vez significa que el cumplimiento de CD está activado de forma predeterminada y no es necesario habilitarlo explícitamente cada vez que se abra una conexión.</span><span class="sxs-lookup"><span data-stu-id="57ff5-728">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

<span data-ttu-id="57ff5-729">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-729">**Mitigations**</span></span>

<span data-ttu-id="57ff5-730">Las claves externas se habilitan de forma predeterminada en SQLitePCLRaw.bundle_e_sqlite3, que en EF Core se usa de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="57ff5-730">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="57ff5-731">Para otros casos, las claves externas se pueden habilitar mediante la especificación de `Foreign Keys=True` en la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="57ff5-731">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="57ff5-732">Microsoft.EntityFrameworkCore.Sqlite ahora depende de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="57ff5-732">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

<span data-ttu-id="57ff5-733">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-733">**Old behavior**</span></span>

<span data-ttu-id="57ff5-734">Antes de EF Core 3.0, en EF Core se usaba `SQLitePCLRaw.bundle_green`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-734">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

<span data-ttu-id="57ff5-735">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-735">**New behavior**</span></span>

<span data-ttu-id="57ff5-736">A partir de EF Core 3.0, en EF Core se usa `SQLitePCLRaw.bundle_e_sqlite3`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-736">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

<span data-ttu-id="57ff5-737">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-737">**Why**</span></span>

<span data-ttu-id="57ff5-738">Este cambio se ha realizado para que la versión de SQLite que se usa en iOS sea coherente con otras plataformas.</span><span class="sxs-lookup"><span data-stu-id="57ff5-738">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

<span data-ttu-id="57ff5-739">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-739">**Mitigations**</span></span>

<span data-ttu-id="57ff5-740">Para usar la versión nativa de SQLite en iOS, configure `Microsoft.Data.Sqlite` para usar otra agrupación `SQLitePCLRaw`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-740">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="57ff5-741">Almacenamiento de valores GUID como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="57ff5-741">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="57ff5-742">Problema de seguimiento n.º 15078</span><span class="sxs-lookup"><span data-stu-id="57ff5-742">Tracking Issue #15078</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15078)

<span data-ttu-id="57ff5-743">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-743">**Old behavior**</span></span>

<span data-ttu-id="57ff5-744">Antes, los valores GUID se almacenaban como valores BLOB en SQLite.</span><span class="sxs-lookup"><span data-stu-id="57ff5-744">Guid values were previously stored as BLOB values on SQLite.</span></span>

<span data-ttu-id="57ff5-745">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-745">**New behavior**</span></span>

<span data-ttu-id="57ff5-746">Ahora, los valores GUID se almacenan como TEXT.</span><span class="sxs-lookup"><span data-stu-id="57ff5-746">Guid values are now stored as TEXT.</span></span>

<span data-ttu-id="57ff5-747">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-747">**Why**</span></span>

<span data-ttu-id="57ff5-748">El formato binario de los GUID no está normalizado.</span><span class="sxs-lookup"><span data-stu-id="57ff5-748">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="57ff5-749">El almacenamiento de los valores como TEXT mejora la compatibilidad de la base de datos con otras tecnologías.</span><span class="sxs-lookup"><span data-stu-id="57ff5-749">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="57ff5-750">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-750">**Mitigations**</span></span>

<span data-ttu-id="57ff5-751">Puede migrar las bases de datos existentes al nuevo formato ejecutando SQL de la siguiente forma.</span><span class="sxs-lookup"><span data-stu-id="57ff5-751">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

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

<span data-ttu-id="57ff5-752">En EF Core, también puede seguir usando el comportamiento anterior configurando un convertidor de valores en estas propiedades.</span><span class="sxs-lookup"><span data-stu-id="57ff5-752">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="57ff5-753">Microsoft.Data.Sqlite sigue siendo capaz de leer valores GUID de ambas columnas BLOB y TEXT. Sin embargo, dado que el formato predeterminado de los parámetros y las constantes ha cambiado, seguramente deberá realizar alguna acción en la mayoría de casos que impliquen el uso de valores GUID.</span><span class="sxs-lookup"><span data-stu-id="57ff5-753">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="57ff5-754">Ahora los valores char se almacenan como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="57ff5-754">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="57ff5-755">Problema de seguimiento n.º 15020</span><span class="sxs-lookup"><span data-stu-id="57ff5-755">Tracking Issue #15020</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15020)

<span data-ttu-id="57ff5-756">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-756">**Old behavior**</span></span>

<span data-ttu-id="57ff5-757">Anteriormente los valores char se almacenaban como valores INTEGER en SQLite.</span><span class="sxs-lookup"><span data-stu-id="57ff5-757">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="57ff5-758">Por ejemplo, un valor char de *A* se almacenaba como el valor entero 65.</span><span class="sxs-lookup"><span data-stu-id="57ff5-758">For example, a char value of *A* was stored as the integer value 65.</span></span>

<span data-ttu-id="57ff5-759">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-759">**New behavior**</span></span>

<span data-ttu-id="57ff5-760">Ahora, los valores char se almacenan como TEXT.</span><span class="sxs-lookup"><span data-stu-id="57ff5-760">Char values are now stored as TEXT.</span></span>

<span data-ttu-id="57ff5-761">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-761">**Why**</span></span>

<span data-ttu-id="57ff5-762">El almacenamiento de valores como TEXT es más natural y mejora la compatibilidad de la base de datos con otras tecnologías.</span><span class="sxs-lookup"><span data-stu-id="57ff5-762">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

<span data-ttu-id="57ff5-763">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-763">**Mitigations**</span></span>

<span data-ttu-id="57ff5-764">Puede migrar las bases de datos existentes al nuevo formato ejecutando SQL de la siguiente forma.</span><span class="sxs-lookup"><span data-stu-id="57ff5-764">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

``` sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="57ff5-765">En EF Core, también puede seguir usando el comportamiento anterior configurando un convertidor de valores en estas propiedades.</span><span class="sxs-lookup"><span data-stu-id="57ff5-765">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

``` csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="57ff5-766">Microsoft.Data.Sqlite también puede leer valores de caracteres tanto de columnas INTEGER como de columnas TEXT, por lo que es posible que no deba hacer nada dependiendo de su caso.</span><span class="sxs-lookup"><span data-stu-id="57ff5-766">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="57ff5-767">Ahora los id. de migración se generan usando el calendario de la referencia cultural invariable</span><span class="sxs-lookup"><span data-stu-id="57ff5-767">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="57ff5-768">Problema de seguimiento n.º 12978</span><span class="sxs-lookup"><span data-stu-id="57ff5-768">Tracking Issue #12978</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12978)

<span data-ttu-id="57ff5-769">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-769">**Old behavior**</span></span>

<span data-ttu-id="57ff5-770">Los identificadores de migración se generaban de forma involuntaria con el calendario de la referencia cultural actual.</span><span class="sxs-lookup"><span data-stu-id="57ff5-770">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

<span data-ttu-id="57ff5-771">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-771">**New behavior**</span></span>

<span data-ttu-id="57ff5-772">Ahora los id. de migración siempre se generan usando el calendario de la referencia cultural invariable (gregoriano).</span><span class="sxs-lookup"><span data-stu-id="57ff5-772">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

<span data-ttu-id="57ff5-773">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-773">**Why**</span></span>

<span data-ttu-id="57ff5-774">El orden de las migraciones es importante al actualizar la base de datos o al solucionar conflictos de combinación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-774">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="57ff5-775">Al usar el calendario invariable, se evitan problemas de ordenación que pueden producirse si los miembros del equipo tienen distintos calendarios del sistema.</span><span class="sxs-lookup"><span data-stu-id="57ff5-775">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

<span data-ttu-id="57ff5-776">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-776">**Mitigations**</span></span>

<span data-ttu-id="57ff5-777">Esta cambio afecta a todas las personas que usan un calendario no gregoriano en el que el año sea superior al del calendario gregoriano (como el calendario budista tailandés).</span><span class="sxs-lookup"><span data-stu-id="57ff5-777">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="57ff5-778">Los id. de migración existentes deberán actualizarse para que las migraciones nuevas se ordenen después de las existentes.</span><span class="sxs-lookup"><span data-stu-id="57ff5-778">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="57ff5-779">Puede ver el id. de migración en el atributo Migration de los archivos de diseñador de la migración.</span><span class="sxs-lookup"><span data-stu-id="57ff5-779">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

``` diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="57ff5-780">También debe actualizarse la tabla de historial de migraciones.</span><span class="sxs-lookup"><span data-stu-id="57ff5-780">The Migrations history table also needs to be updated.</span></span>

``` sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="57ff5-781">Se ha quitado el elemento UseRowNumberForPaging</span><span class="sxs-lookup"><span data-stu-id="57ff5-781">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="57ff5-782">Problema de seguimiento n.º 16400</span><span class="sxs-lookup"><span data-stu-id="57ff5-782">Tracking Issue #16400</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16400)

<span data-ttu-id="57ff5-783">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-783">**Old behavior**</span></span>

<span data-ttu-id="57ff5-784">Antes de EF Core 3.0, `UseRowNumberForPaging` se podía usar para generar SQL para la paginación de forma que fuera compatible con SQL Server 2008.</span><span class="sxs-lookup"><span data-stu-id="57ff5-784">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

<span data-ttu-id="57ff5-785">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-785">**New behavior**</span></span>

<span data-ttu-id="57ff5-786">A partir de EF Core 3.0, EF solo genera SQL para la paginación que únicamente es compatible con las versiones posteriores de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="57ff5-786">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span> 

<span data-ttu-id="57ff5-787">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-787">**Why**</span></span>

<span data-ttu-id="57ff5-788">El motivo de este cambio es que [SQL Server 2008 ya no se admite](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/). Además, la actualización de esta característica para que funcionase con los cambios en las consultas implementados en EF Core 3.0 llevaría mucho trabajo.</span><span class="sxs-lookup"><span data-stu-id="57ff5-788">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

<span data-ttu-id="57ff5-789">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-789">**Mitigations**</span></span>

<span data-ttu-id="57ff5-790">Se recomienda actualizar a una versión más reciente de SQL Server, o bien utilizar un nivel de compatibilidad superior, de modo que el SQL que se genere se admita.</span><span class="sxs-lookup"><span data-stu-id="57ff5-790">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="57ff5-791">Dicho esto, si no puede hacerlo, [escriba un comentario en el problema de seguimiento](https://github.com/aspnet/EntityFrameworkCore/issues/16400) con los detalles al respecto.</span><span class="sxs-lookup"><span data-stu-id="57ff5-791">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/aspnet/EntityFrameworkCore/issues/16400) with details.</span></span> <span data-ttu-id="57ff5-792">En función de los comentarios, es posible que volvamos a valorar esta decisión.</span><span class="sxs-lookup"><span data-stu-id="57ff5-792">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="57ff5-793">La información o metadatos de la extensión se han quitado de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="57ff5-793">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="57ff5-794">Problema de seguimiento n.º 16119</span><span class="sxs-lookup"><span data-stu-id="57ff5-794">Tracking Issue #16119</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/16119)

<span data-ttu-id="57ff5-795">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-795">**Old behavior**</span></span>

<span data-ttu-id="57ff5-796">`IDbContextOptionsExtension` incluía métodos para proporcionar metadatos sobre la extensión.</span><span class="sxs-lookup"><span data-stu-id="57ff5-796">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

<span data-ttu-id="57ff5-797">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-797">**New behavior**</span></span>

<span data-ttu-id="57ff5-798">Estos métodos se han movido a una nueva clase base abstracta `DbContextOptionsExtensionInfo`, que se devuelve desde una nueva propiedad `IDbContextOptionsExtension.Info`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-798">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

<span data-ttu-id="57ff5-799">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-799">**Why**</span></span>

<span data-ttu-id="57ff5-800">Al lanzarse las versiones 2.0 y 3.0, tuvimos que agregar o cambiar estos métodos varias veces.</span><span class="sxs-lookup"><span data-stu-id="57ff5-800">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="57ff5-801">Su división en una nueva clase base abstracta facilitará la realización de este tipo de cambios sin interrumpir las extensiones existentes.</span><span class="sxs-lookup"><span data-stu-id="57ff5-801">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

<span data-ttu-id="57ff5-802">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-802">**Mitigations**</span></span>

<span data-ttu-id="57ff5-803">Actualice las extensiones para seguir el nuevo patrón.</span><span class="sxs-lookup"><span data-stu-id="57ff5-803">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="57ff5-804">Encontrará ejemplos en las muchas implementaciones de `IDbContextOptionsExtension` para los diferentes tipos de extensiones en el código fuente de EF Core.</span><span class="sxs-lookup"><span data-stu-id="57ff5-804">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="57ff5-805">Cambio de nombre de LogQueryPossibleExceptionWithAggregateOperator</span><span class="sxs-lookup"><span data-stu-id="57ff5-805">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="57ff5-806">Problema de seguimiento n.º 10985</span><span class="sxs-lookup"><span data-stu-id="57ff5-806">Tracking Issue #10985</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10985)

<span data-ttu-id="57ff5-807">**Cambio**</span><span class="sxs-lookup"><span data-stu-id="57ff5-807">**Change**</span></span>

<span data-ttu-id="57ff5-808">Se ha cambiado el nombre de `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` a `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span><span class="sxs-lookup"><span data-stu-id="57ff5-808">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

<span data-ttu-id="57ff5-809">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-809">**Why**</span></span>

<span data-ttu-id="57ff5-810">Conviene alinear el nombre de este evento de advertencia con el del resto de eventos de advertencia.</span><span class="sxs-lookup"><span data-stu-id="57ff5-810">Aligns the naming of this warning event with all other warning events.</span></span>

<span data-ttu-id="57ff5-811">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-811">**Mitigations**</span></span>

<span data-ttu-id="57ff5-812">Use el nuevo nombre.</span><span class="sxs-lookup"><span data-stu-id="57ff5-812">Use the new name.</span></span> <span data-ttu-id="57ff5-813">(Tenga en cuenta que el número de id. evento sigue siendo el mismo).</span><span class="sxs-lookup"><span data-stu-id="57ff5-813">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="57ff5-814">Clarificación de la API para nombres de restricciones de claves externas</span><span class="sxs-lookup"><span data-stu-id="57ff5-814">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="57ff5-815">Problema de seguimiento n.º 10730</span><span class="sxs-lookup"><span data-stu-id="57ff5-815">Tracking Issue #10730</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/10730)

<span data-ttu-id="57ff5-816">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-816">**Old behavior**</span></span>

<span data-ttu-id="57ff5-817">Antes de EF Core 3.0, se utilizaba simplemente el término "nombre" para hacer referencia a los nombres de las restricciones de claves externas.</span><span class="sxs-lookup"><span data-stu-id="57ff5-817">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="57ff5-818">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-818">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

<span data-ttu-id="57ff5-819">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-819">**New behavior**</span></span>

<span data-ttu-id="57ff5-820">A partir de EF Core 3.0, el término con el que se hace referencia a los nombres de las restricciones de claves externas es "nombre de la restricción".</span><span class="sxs-lookup"><span data-stu-id="57ff5-820">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="57ff5-821">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-821">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

<span data-ttu-id="57ff5-822">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-822">**Why**</span></span>

<span data-ttu-id="57ff5-823">Este cambio permite mejorar la coherencia relativa a la nomenclatura en este aspecto y aclarar que se trata del nombre de una restricción de clave externa, y no del de la columna o propiedad en la que está definida la clave externa.</span><span class="sxs-lookup"><span data-stu-id="57ff5-823">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

<span data-ttu-id="57ff5-824">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-824">**Mitigations**</span></span>

<span data-ttu-id="57ff5-825">Use el nuevo nombre.</span><span class="sxs-lookup"><span data-stu-id="57ff5-825">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="57ff5-826">IRelationalDatabaseCreator.HasTables/HasTablesAsync se han hecho públicos</span><span class="sxs-lookup"><span data-stu-id="57ff5-826">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="57ff5-827">Problema de seguimiento n.° 15997</span><span class="sxs-lookup"><span data-stu-id="57ff5-827">Tracking Issue #15997</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15997)

<span data-ttu-id="57ff5-828">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-828">**Old behavior**</span></span>

<span data-ttu-id="57ff5-829">Antes de EF Core 3.0, estos métodos estaban protegidos.</span><span class="sxs-lookup"><span data-stu-id="57ff5-829">Before EF Core 3.0, these methods were protected.</span></span>

<span data-ttu-id="57ff5-830">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-830">**New behavior**</span></span>

<span data-ttu-id="57ff5-831">Desde EF Core 3.0, estos métodos son públicos.</span><span class="sxs-lookup"><span data-stu-id="57ff5-831">Starting with EF Core 3.0, these methods are public.</span></span>

<span data-ttu-id="57ff5-832">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-832">**Why**</span></span>

<span data-ttu-id="57ff5-833">EF usa estos métodos para determinar si se ha creado una base de datos, pero está vacía.</span><span class="sxs-lookup"><span data-stu-id="57ff5-833">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="57ff5-834">Esto también puede resultar útil fuera de EF al determinar si se deben aplicar migraciones o no.</span><span class="sxs-lookup"><span data-stu-id="57ff5-834">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

<span data-ttu-id="57ff5-835">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-835">**Mitigations**</span></span>

<span data-ttu-id="57ff5-836">Cambie la accesibilidad de cualquier invalidación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-836">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="57ff5-837">Microsoft.EntityFrameworkCore.Design es ahora un paquete DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="57ff5-837">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="57ff5-838">Problema de seguimiento n.° 11506</span><span class="sxs-lookup"><span data-stu-id="57ff5-838">Tracking Issue #11506</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/11506)

<span data-ttu-id="57ff5-839">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-839">**Old behavior**</span></span>

<span data-ttu-id="57ff5-840">Antes de EF Core 3.0, Microsoft.EntityFrameworkCore.Design era un paquete NuGet regular con un ensamblado al que podían hacer referencia los proyectos que dependían de él.</span><span class="sxs-lookup"><span data-stu-id="57ff5-840">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

<span data-ttu-id="57ff5-841">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-841">**New behavior**</span></span>

<span data-ttu-id="57ff5-842">Desde EF Core 3.0, es un paquete DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="57ff5-842">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="57ff5-843">Esto significa que la dependencia no fluirá de manera transitiva en otros proyectos y que ya no puede, de forma predeterminada, hacer referencia a su ensamblado.</span><span class="sxs-lookup"><span data-stu-id="57ff5-843">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

<span data-ttu-id="57ff5-844">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-844">**Why**</span></span>

<span data-ttu-id="57ff5-845">Este paquete solo está destinado a usarse en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="57ff5-845">This package is only intended to be used at design time.</span></span> <span data-ttu-id="57ff5-846">Las aplicaciones implementadas no deben hacer referencia al mismo.</span><span class="sxs-lookup"><span data-stu-id="57ff5-846">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="57ff5-847">Hacer que el paquete sea DevelopmentDependency refuerza esta recomendación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-847">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

<span data-ttu-id="57ff5-848">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-848">**Mitigations**</span></span>

<span data-ttu-id="57ff5-849">Si tiene que hacer referencia a este paquete para invalidar el comportamiento en tiempo de diseño de EF Core, puede actualizar los metadatos de elementos PackageReference del proyecto.</span><span class="sxs-lookup"><span data-stu-id="57ff5-849">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

``` xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="57ff5-850">Si se hace referencia al paquete de manera transitiva a través de Microsoft.EntityFrameworkCore.Tools, tendrá que agregar una PackageReference explícita al paquete para cambiar sus metadatos.</span><span class="sxs-lookup"><span data-stu-id="57ff5-850">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="57ff5-851">Este tipo de referencia explícita debe agregarse a cualquier proyecto que requiera los tipos de paquete.</span><span class="sxs-lookup"><span data-stu-id="57ff5-851">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="57ff5-852">SQLitePCL.raw se ha actualizado a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="57ff5-852">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="57ff5-853">Problema de seguimiento n.° 14824</span><span class="sxs-lookup"><span data-stu-id="57ff5-853">Tracking Issue #14824</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14824)

<span data-ttu-id="57ff5-854">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-854">**Old behavior**</span></span>

<span data-ttu-id="57ff5-855">Microsoft.EntityFrameworkCore.Sqlite dependía anteriormente de la versión 1.1.12 de SQLitePCL.raw.</span><span class="sxs-lookup"><span data-stu-id="57ff5-855">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

<span data-ttu-id="57ff5-856">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-856">**New behavior**</span></span>

<span data-ttu-id="57ff5-857">Hemos actualizado nuestro paquete para depender de la versión 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="57ff5-857">We've updated our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="57ff5-858">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-858">**Why**</span></span>

<span data-ttu-id="57ff5-859">La versión 2.0.0 de SQLitePCL.raw selecciona .NET Standard 2.0 como destino.</span><span class="sxs-lookup"><span data-stu-id="57ff5-859">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="57ff5-860">Anteriormente seleccionaba .NET Standard 1.1 como destino, que requería el cierre a gran escala de paquetes transitivos para su funcionamiento.</span><span class="sxs-lookup"><span data-stu-id="57ff5-860">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

<span data-ttu-id="57ff5-861">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-861">**Mitigations**</span></span>

<span data-ttu-id="57ff5-862">En la versión 2.0.0 de SQLitePCL.raw se incluyen algunos cambios importantes.</span><span class="sxs-lookup"><span data-stu-id="57ff5-862">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="57ff5-863">Consulte las [notas de la versión](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) para obtener detalles.</span><span class="sxs-lookup"><span data-stu-id="57ff5-863">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="57ff5-864">NetTopologySuite se actualizó a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="57ff5-864">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="57ff5-865">Problema de seguimiento n.° 14825</span><span class="sxs-lookup"><span data-stu-id="57ff5-865">Tracking Issue #14825</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/14825)

<span data-ttu-id="57ff5-866">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-866">**Old behavior**</span></span>

<span data-ttu-id="57ff5-867">Los paquetes espaciales anteriormente dependían de la versión 1.15.1 de NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="57ff5-867">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

<span data-ttu-id="57ff5-868">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-868">**New behavior**</span></span>

<span data-ttu-id="57ff5-869">Hemos actualizado nuestro paquete para depender de la versión 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="57ff5-869">We've update our package to depend on version 2.0.0.</span></span>

<span data-ttu-id="57ff5-870">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-870">**Why**</span></span>

<span data-ttu-id="57ff5-871">La versión 2.0.0 de NetTopologySuite pretende resolver varios problemas de usabilidad que encontraron los usuarios de EF Core.</span><span class="sxs-lookup"><span data-stu-id="57ff5-871">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

<span data-ttu-id="57ff5-872">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-872">**Mitigations**</span></span>

<span data-ttu-id="57ff5-873">En la versión 2.0.0 de NetTopologySuite se incluyen algunos cambios importantes.</span><span class="sxs-lookup"><span data-stu-id="57ff5-873">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="57ff5-874">Consulte las [notas de la versión](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) para obtener detalles.</span><span class="sxs-lookup"><span data-stu-id="57ff5-874">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="57ff5-875">Se usa Microsoft.Data.SqlClient en lugar de System.Data.SqlClient</span><span class="sxs-lookup"><span data-stu-id="57ff5-875">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="57ff5-876">Problema de seguimiento n.º 15636</span><span class="sxs-lookup"><span data-stu-id="57ff5-876">Tracking Issue #15636</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/15636)

<span data-ttu-id="57ff5-877">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-877">**Old behavior**</span></span>

<span data-ttu-id="57ff5-878">Microsoft.EntityFrameworkCore.SqlServer dependía anteriormente de la versión System.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="57ff5-878">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

<span data-ttu-id="57ff5-879">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-879">**New behavior**</span></span>

<span data-ttu-id="57ff5-880">Hemos actualizado nuestro paquete para que dependa de Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="57ff5-880">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="57ff5-881">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-881">**Why**</span></span>

<span data-ttu-id="57ff5-882">A partir de ahora, Microsoft.Data.SqlClient es el controlador de acceso a datos insignia para SQL Server y System.Data.SqlClient ya no es el centro de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="57ff5-882">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="57ff5-883">Algunas características importantes, como Always Encrypted, solo están disponibles en Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="57ff5-883">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

<span data-ttu-id="57ff5-884">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-884">**Mitigations**</span></span>

<span data-ttu-id="57ff5-885">Si el código toma una dependencia directa en System.Data.SqlClient, debe cambiarla para que haga referencia a Microsoft.Data.SqlClient en su lugar. Dado que los dos paquetes mantienen un grado muy alto de compatibilidad con la API, solo debería ser un paquete simple y un cambio de espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="57ff5-885">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="57ff5-886">Se deben configurar varias relaciones de referencia automática ambiguas</span><span class="sxs-lookup"><span data-stu-id="57ff5-886">Multiple ambiguous self-referencing relationships must be configured</span></span> 

[<span data-ttu-id="57ff5-887">Problema de seguimiento n.º 13573</span><span class="sxs-lookup"><span data-stu-id="57ff5-887">Tracking Issue #13573</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/13573)

<span data-ttu-id="57ff5-888">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-888">**Old behavior**</span></span>

<span data-ttu-id="57ff5-889">Un tipo de entidad con varias propiedades de navegación unidireccional de referencia automática y claves externas coincidentes se configuró incorrectamente como una única relación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-889">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="57ff5-890">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-890">For example:</span></span>

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

<span data-ttu-id="57ff5-891">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-891">**New behavior**</span></span>

<span data-ttu-id="57ff5-892">Este escenario se detecta ahora en la generación del modelo y se produce una excepción que indica que el modelo es ambiguo.</span><span class="sxs-lookup"><span data-stu-id="57ff5-892">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

<span data-ttu-id="57ff5-893">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-893">**Why**</span></span>

<span data-ttu-id="57ff5-894">El modelo resultante era ambiguo, y lo más probable es que sea incorrecto en este caso.</span><span class="sxs-lookup"><span data-stu-id="57ff5-894">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

<span data-ttu-id="57ff5-895">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-895">**Mitigations**</span></span>

<span data-ttu-id="57ff5-896">Utilice la configuración completa de la relación.</span><span class="sxs-lookup"><span data-stu-id="57ff5-896">Use full configuration of the relationship.</span></span> <span data-ttu-id="57ff5-897">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="57ff5-897">For example:</span></span>

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
### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="57ff5-898">DbFunction.Schema es NULL o la cadena vacía lo configura para estar en el esquema predeterminado del modelo</span><span class="sxs-lookup"><span data-stu-id="57ff5-898">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="57ff5-899">Problema de seguimiento n.º 12757</span><span class="sxs-lookup"><span data-stu-id="57ff5-899">Tracking Issue #12757</span></span>](https://github.com/aspnet/EntityFrameworkCore/issues/12757)

<span data-ttu-id="57ff5-900">**Comportamiento anterior**</span><span class="sxs-lookup"><span data-stu-id="57ff5-900">**Old behavior**</span></span>

<span data-ttu-id="57ff5-901">Una función DbFunction configurada con el esquema como una cadena vacía se trataba como una función integrada sin un esquema.</span><span class="sxs-lookup"><span data-stu-id="57ff5-901">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="57ff5-902">Por ejemplo, el código siguiente asignará la función CLR `DatePart` a la función integrada `DATEPART` en SqlServer.</span><span class="sxs-lookup"><span data-stu-id="57ff5-902">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

<span data-ttu-id="57ff5-903">**Comportamiento nuevo**</span><span class="sxs-lookup"><span data-stu-id="57ff5-903">**New behavior**</span></span>

<span data-ttu-id="57ff5-904">Todas las asignaciones de DbFunction se consideran asignadas a funciones definidas por el usuario.</span><span class="sxs-lookup"><span data-stu-id="57ff5-904">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="57ff5-905">Por lo tanto, el valor de cadena vacía colocaría la función dentro del esquema predeterminado del modelo,</span><span class="sxs-lookup"><span data-stu-id="57ff5-905">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="57ff5-906">que podría ser el esquema configurado de forma explícita mediante `modelBuilder.HasDefaultSchema()` de la API fluida o `dbo` en caso contrario.</span><span class="sxs-lookup"><span data-stu-id="57ff5-906">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

<span data-ttu-id="57ff5-907">**Por qué**</span><span class="sxs-lookup"><span data-stu-id="57ff5-907">**Why**</span></span>

<span data-ttu-id="57ff5-908">Anteriormente, el esquema vacío era una manera de indicar que la función estaba integrada, pero esa lógica solo es aplicable a SqlServer, donde las funciones integradas no pertenecen a ningún esquema.</span><span class="sxs-lookup"><span data-stu-id="57ff5-908">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

<span data-ttu-id="57ff5-909">**Mitigaciones**</span><span class="sxs-lookup"><span data-stu-id="57ff5-909">**Mitigations**</span></span>

<span data-ttu-id="57ff5-910">Configure la traslación de DbFunction manualmente para asignarla a una función integrada.</span><span class="sxs-lookup"><span data-stu-id="57ff5-910">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```
