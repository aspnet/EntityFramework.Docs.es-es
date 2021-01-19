---
title: 'Cambios importantes en EF Core 3.x: EF Core'
description: Lista completa de los cambios importantes introducidos en Entity Framework Core 3.x
author: ajcvickers
ms.date: 09/05/2020
uid: core/what-is-new/ef-core-3.x/breaking-changes
ms.openlocfilehash: 8c0be4193c79e838e40bfc2dc10c9d12b01381cd
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128776"
---
# <a name="breaking-changes-included-in-ef-core-3x"></a><span data-ttu-id="ae93f-103">Cambios importantes incluidos en EF Core 3.x</span><span class="sxs-lookup"><span data-stu-id="ae93f-103">Breaking changes included in EF Core 3.x</span></span>

<span data-ttu-id="ae93f-104">Es posible que los siguientes cambios de API y comportamiento interrumpan las aplicaciones existentes cuando se actualicen a las versiones 3.x.</span><span class="sxs-lookup"><span data-stu-id="ae93f-104">The following API and behavior changes have the potential to break existing applications when upgrading them to 3.x.</span></span>
<span data-ttu-id="ae93f-105">Los cambios que esperamos que solo afecten a proveedores de base de datos se documentan en [Cambios para proveedores](xref:core/providers/provider-log).</span><span class="sxs-lookup"><span data-stu-id="ae93f-105">Changes that we expect to only impact database providers are documented under [provider changes](xref:core/providers/provider-log).</span></span>

## <a name="summary"></a><span data-ttu-id="ae93f-106">Resumen</span><span class="sxs-lookup"><span data-stu-id="ae93f-106">Summary</span></span>

| <span data-ttu-id="ae93f-107">**Cambio importante**</span><span class="sxs-lookup"><span data-stu-id="ae93f-107">**Breaking change**</span></span>                                                                                               | <span data-ttu-id="ae93f-108">**Impacto**</span><span class="sxs-lookup"><span data-stu-id="ae93f-108">**Impact**</span></span> |
|:------------------------------------------------------------------------------------------------------------------|------------|
| [<span data-ttu-id="ae93f-109">Las consultas LINQ ya no se evalúan en el cliente</span><span class="sxs-lookup"><span data-stu-id="ae93f-109">LINQ queries are no longer evaluated on the client</span></span>](#linq-queries-are-no-longer-evaluated-on-the-client)         | <span data-ttu-id="ae93f-110">Alto</span><span class="sxs-lookup"><span data-stu-id="ae93f-110">High</span></span>       |
| [<span data-ttu-id="ae93f-111">La herramienta de línea de comandos de EF Core, dotnet ef, ya no forma parte del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="ae93f-111">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>](#dotnet-ef) | <span data-ttu-id="ae93f-112">Alto</span><span class="sxs-lookup"><span data-stu-id="ae93f-112">High</span></span>      |
| [<span data-ttu-id="ae93f-113">DetectChanges respeta los valores de clave generados por el almacén</span><span class="sxs-lookup"><span data-stu-id="ae93f-113">DetectChanges honors store-generated key values</span></span>](#dc) | <span data-ttu-id="ae93f-114">Alto</span><span class="sxs-lookup"><span data-stu-id="ae93f-114">High</span></span>      |
| [<span data-ttu-id="ae93f-115">FromSql, ExecuteSql y ExecuteSqlAsync han cambiado de nombre</span><span class="sxs-lookup"><span data-stu-id="ae93f-115">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>](#fromsql) | <span data-ttu-id="ae93f-116">Alto</span><span class="sxs-lookup"><span data-stu-id="ae93f-116">High</span></span>      |
| [<span data-ttu-id="ae93f-117">Los tipos de consulta se consolidan con tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="ae93f-117">Query types are consolidated with entity types</span></span>](#qt) | <span data-ttu-id="ae93f-118">Alto</span><span class="sxs-lookup"><span data-stu-id="ae93f-118">High</span></span>      |
| [<span data-ttu-id="ae93f-119">Entity Framework Core ya no forma parte del marco compartido ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae93f-119">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>](#no-longer) | <span data-ttu-id="ae93f-120">Media</span><span class="sxs-lookup"><span data-stu-id="ae93f-120">Medium</span></span>      |
| [<span data-ttu-id="ae93f-121">Las eliminaciones en cascada ahora se realizan inmediatamente de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="ae93f-121">Cascade deletions now happen immediately by default</span></span>](#cascade) | <span data-ttu-id="ae93f-122">Media</span><span class="sxs-lookup"><span data-stu-id="ae93f-122">Medium</span></span>      |
| [<span data-ttu-id="ae93f-123">La carga diligente de entidades relacionadas ahora se realiza en una sola consulta</span><span class="sxs-lookup"><span data-stu-id="ae93f-123">Eager loading of related entities now happens in a single query</span></span>](#eager-loading-single-query) | <span data-ttu-id="ae93f-124">Media</span><span class="sxs-lookup"><span data-stu-id="ae93f-124">Medium</span></span>      |
| [<span data-ttu-id="ae93f-125">DeleteBehavior.Restrict tiene una semántica más limpia</span><span class="sxs-lookup"><span data-stu-id="ae93f-125">DeleteBehavior.Restrict has cleaner semantics</span></span>](#deletebehavior) | <span data-ttu-id="ae93f-126">Media</span><span class="sxs-lookup"><span data-stu-id="ae93f-126">Medium</span></span>      |
| [<span data-ttu-id="ae93f-127">La API de configuración para las relaciones de tipo de propiedad ha cambiado</span><span class="sxs-lookup"><span data-stu-id="ae93f-127">Configuration API for owned type relationships has changed</span></span>](#config) | <span data-ttu-id="ae93f-128">Media</span><span class="sxs-lookup"><span data-stu-id="ae93f-128">Medium</span></span>      |
| [<span data-ttu-id="ae93f-129">Cada propiedad usa la generación de claves enteras en memoria independiente</span><span class="sxs-lookup"><span data-stu-id="ae93f-129">Each property uses independent in-memory integer key generation</span></span>](#each) | <span data-ttu-id="ae93f-130">Media</span><span class="sxs-lookup"><span data-stu-id="ae93f-130">Medium</span></span>      |
| [<span data-ttu-id="ae93f-131">Las consultas sin seguimiento ya no realizan la resolución de la identidad</span><span class="sxs-lookup"><span data-stu-id="ae93f-131">No-tracking queries no longer perform identity resolution</span></span>](#notrackingresolution) | <span data-ttu-id="ae93f-132">Media</span><span class="sxs-lookup"><span data-stu-id="ae93f-132">Medium</span></span>      |
| [<span data-ttu-id="ae93f-133">Cambios en la API de metadatos</span><span class="sxs-lookup"><span data-stu-id="ae93f-133">Metadata API changes</span></span>](#metadata-api-changes) | <span data-ttu-id="ae93f-134">Media</span><span class="sxs-lookup"><span data-stu-id="ae93f-134">Medium</span></span>      |
| [<span data-ttu-id="ae93f-135">Cambios en la API de metadatos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="ae93f-135">Provider-specific Metadata API changes</span></span>](#provider) | <span data-ttu-id="ae93f-136">Media</span><span class="sxs-lookup"><span data-stu-id="ae93f-136">Medium</span></span>      |
| [<span data-ttu-id="ae93f-137">Se ha quitado el elemento UseRowNumberForPaging</span><span class="sxs-lookup"><span data-stu-id="ae93f-137">UseRowNumberForPaging has been removed</span></span>](#urn) | <span data-ttu-id="ae93f-138">Media</span><span class="sxs-lookup"><span data-stu-id="ae93f-138">Medium</span></span>      |
| [<span data-ttu-id="ae93f-139">Cuando el método FromSql se usa con un procedimiento almacenado no se puede redactar</span><span class="sxs-lookup"><span data-stu-id="ae93f-139">FromSql method when used with stored procedure cannot be composed</span></span>](#fromsqlsproc) | <span data-ttu-id="ae93f-140">Media</span><span class="sxs-lookup"><span data-stu-id="ae93f-140">Medium</span></span>      |
| [<span data-ttu-id="ae93f-141">Solo se pueden especificar métodos de FromSql en raíces de consulta</span><span class="sxs-lookup"><span data-stu-id="ae93f-141">FromSql methods can only be specified on query roots</span></span>](#fromsql) | <span data-ttu-id="ae93f-142">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-142">Low</span></span>      |
| [<span data-ttu-id="ae93f-143">Los valores de clave temporal ya no se establecen en instancias de entidad</span><span class="sxs-lookup"><span data-stu-id="ae93f-143">Temporary key values are no longer set onto entity instances</span></span>](#tkv) | <span data-ttu-id="ae93f-144">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-144">Low</span></span>      |
| [<span data-ttu-id="ae93f-145">Ahora, las entidades dependientes que comparten la tabla con la entidad de seguridad son opcionales</span><span class="sxs-lookup"><span data-stu-id="ae93f-145">Dependent entities sharing the table with the principal are now optional</span></span>](#de) | <span data-ttu-id="ae93f-146">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-146">Low</span></span>      |
| [<span data-ttu-id="ae93f-147">Todas las entidades que compartan una tabla con una columna de token de simultaneidad tienen que asignarla a una propiedad</span><span class="sxs-lookup"><span data-stu-id="ae93f-147">All entities sharing a table with a concurrency token column have to map it to a property</span></span>](#aes) | <span data-ttu-id="ae93f-148">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-148">Low</span></span>      |
| [<span data-ttu-id="ae93f-149">Las entidades en propiedad no se pueden consultar sin el propietario mediante una consulta de seguimiento</span><span class="sxs-lookup"><span data-stu-id="ae93f-149">Owned entities cannot be queried without the owner using a tracking query</span></span>](#owned-query) | <span data-ttu-id="ae93f-150">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-150">Low</span></span>      |
| [<span data-ttu-id="ae93f-151">Ahora, las propiedades heredadas de tipos sin asignar se asignan a una única columna para todos los tipos derivados</span><span class="sxs-lookup"><span data-stu-id="ae93f-151">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>](#ip) | <span data-ttu-id="ae93f-152">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-152">Low</span></span>      |
| [<span data-ttu-id="ae93f-153">La convención de propiedad de clave externa ya no coincide con el mismo nombre que la propiedad de entidad de seguridad</span><span class="sxs-lookup"><span data-stu-id="ae93f-153">The foreign key property convention no longer matches same name as the principal property</span></span>](#fkp) | <span data-ttu-id="ae93f-154">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-154">Low</span></span>      |
| [<span data-ttu-id="ae93f-155">Ahora, la conexión de base de datos se cierra si ya no se usa antes de que se complete TransactionScope</span><span class="sxs-lookup"><span data-stu-id="ae93f-155">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>](#dbc) | <span data-ttu-id="ae93f-156">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-156">Low</span></span>      |
| [<span data-ttu-id="ae93f-157">Los campos de respaldo se usan de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="ae93f-157">Backing fields are used by default</span></span>](#backing-fields-are-used-by-default) | <span data-ttu-id="ae93f-158">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-158">Low</span></span>      |
| [<span data-ttu-id="ae93f-159">Inicio de excepciones si se encuentran varios campos de respaldo compatibles</span><span class="sxs-lookup"><span data-stu-id="ae93f-159">Throw if multiple compatible backing fields are found</span></span>](#throw-if-multiple-compatible-backing-fields-are-found) | <span data-ttu-id="ae93f-160">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-160">Low</span></span>      |
| [<span data-ttu-id="ae93f-161">Los nombres de propiedades de solo campo deben coincidir con el nombre del campo</span><span class="sxs-lookup"><span data-stu-id="ae93f-161">Field-only property names should match the field name</span></span>](#field-only-property-names-should-match-the-field-name) | <span data-ttu-id="ae93f-162">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-162">Low</span></span>      |
| [<span data-ttu-id="ae93f-163">AddDbContext/AddDbContextPool ya no llaman a AddLogging ni a AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="ae93f-163">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>](#adddbc) | <span data-ttu-id="ae93f-164">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-164">Low</span></span>      |
| [<span data-ttu-id="ae93f-165">AddEntityFramework\* agrega IMemoryCache con un límite de tamaño</span><span class="sxs-lookup"><span data-stu-id="ae93f-165">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>](#addentityframework-adds-imemorycache-with-a-size-limit) | <span data-ttu-id="ae93f-166">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-166">Low</span></span>      |
| [<span data-ttu-id="ae93f-167">Ahora DbContext.Entry realiza una operación DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="ae93f-167">DbContext.Entry now performs a local DetectChanges</span></span>](#dbe) | <span data-ttu-id="ae93f-168">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-168">Low</span></span>      |
| [<span data-ttu-id="ae93f-169">El cliente no genera las claves de matriz de cadena y byte de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="ae93f-169">String and byte array keys are not client-generated by default</span></span>](#string-and-byte-array-keys-are-not-client-generated-by-default) | <span data-ttu-id="ae93f-170">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-170">Low</span></span>      |
| [<span data-ttu-id="ae93f-171">Ahora ILoggerFactory es un servicio con ámbito</span><span class="sxs-lookup"><span data-stu-id="ae93f-171">ILoggerFactory is now a scoped service</span></span>](#ilf) | <span data-ttu-id="ae93f-172">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-172">Low</span></span>      |
| [<span data-ttu-id="ae93f-173">En los proxies de carga diferida ya no se supone que las propiedades de navegación están totalmente cargadas</span><span class="sxs-lookup"><span data-stu-id="ae93f-173">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>](#lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded) | <span data-ttu-id="ae93f-174">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-174">Low</span></span>      |
| [<span data-ttu-id="ae93f-175">La creación excesiva de proveedores de servicios internos ahora es un error de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="ae93f-175">Excessive creation of internal service providers is now an error by default</span></span>](#excessive-creation-of-internal-service-providers-is-now-an-error-by-default) | <span data-ttu-id="ae93f-176">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-176">Low</span></span>      |
| [<span data-ttu-id="ae93f-177">Comportamiento nuevo de HasOne/HasMany llamado con una sola cadena</span><span class="sxs-lookup"><span data-stu-id="ae93f-177">New behavior for HasOne/HasMany called with a single string</span></span>](#nbh) | <span data-ttu-id="ae93f-178">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-178">Low</span></span>      |
| [<span data-ttu-id="ae93f-179">El tipo de valor devuelto para varios métodos asincrónicos se ha cambiado de Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="ae93f-179">The return type for several async methods has been changed from Task to ValueTask</span></span>](#rtnt) | <span data-ttu-id="ae93f-180">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-180">Low</span></span>      |
| [<span data-ttu-id="ae93f-181">La anotación Relational:TypeMapping ahora es simplemente TypeMapping</span><span class="sxs-lookup"><span data-stu-id="ae93f-181">The Relational:TypeMapping annotation is now just TypeMapping</span></span>](#rtt) | <span data-ttu-id="ae93f-182">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-182">Low</span></span>      |
| [<span data-ttu-id="ae93f-183">ToTable en un tipo derivado produce una excepción</span><span class="sxs-lookup"><span data-stu-id="ae93f-183">ToTable on a derived type throws an exception</span></span>](#totable-on-a-derived-type-throws-an-exception) | <span data-ttu-id="ae93f-184">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-184">Low</span></span>      |
| [<span data-ttu-id="ae93f-185">EF Core ya no envía pragma para el cumplimiento de SQLite FK</span><span class="sxs-lookup"><span data-stu-id="ae93f-185">EF Core no longer sends pragma for SQLite FK enforcement</span></span>](#pragma) | <span data-ttu-id="ae93f-186">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-186">Low</span></span>      |
| [<span data-ttu-id="ae93f-187">Microsoft.EntityFrameworkCore.Sqlite ahora depende de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="ae93f-187">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>](#sqlite3) | <span data-ttu-id="ae93f-188">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-188">Low</span></span>      |
| [<span data-ttu-id="ae93f-189">Los valores GUID se almacenan ahora como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="ae93f-189">Guid values are now stored as TEXT on SQLite</span></span>](#guid) | <span data-ttu-id="ae93f-190">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-190">Low</span></span>      |
| [<span data-ttu-id="ae93f-191">Ahora los valores char se almacenan como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="ae93f-191">Char values are now stored as TEXT on SQLite</span></span>](#char) | <span data-ttu-id="ae93f-192">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-192">Low</span></span>      |
| [<span data-ttu-id="ae93f-193">Ahora los id. de migración se generan usando el calendario de la referencia cultural invariable</span><span class="sxs-lookup"><span data-stu-id="ae93f-193">Migration IDs are now generated using the invariant culture's calendar</span></span>](#migid) | <span data-ttu-id="ae93f-194">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-194">Low</span></span>      |
| [<span data-ttu-id="ae93f-195">La información o los metadatos de la extensión se han quitado de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="ae93f-195">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>](#xinfo) | <span data-ttu-id="ae93f-196">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-196">Low</span></span>      |
| [<span data-ttu-id="ae93f-197">LogQueryPossibleExceptionWithAggregateOperator ha cambiado de nombre</span><span class="sxs-lookup"><span data-stu-id="ae93f-197">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>](#lqpe) | <span data-ttu-id="ae93f-198">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-198">Low</span></span>      |
| [<span data-ttu-id="ae93f-199">Clarificación de la API para nombres de restricciones de claves externas</span><span class="sxs-lookup"><span data-stu-id="ae93f-199">Clarify API for foreign key constraint names</span></span>](#clarify) | <span data-ttu-id="ae93f-200">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-200">Low</span></span>      |
| [<span data-ttu-id="ae93f-201">IRelationalDatabaseCreator.HasTables/HasTablesAsync se han hecho públicos</span><span class="sxs-lookup"><span data-stu-id="ae93f-201">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>](#irdc2) | <span data-ttu-id="ae93f-202">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-202">Low</span></span>      |
| [<span data-ttu-id="ae93f-203">Microsoft.EntityFrameworkCore.Design es ahora un paquete DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="ae93f-203">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>](#dip) | <span data-ttu-id="ae93f-204">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-204">Low</span></span>      |
| [<span data-ttu-id="ae93f-205">SQLitePCL.raw se ha actualizado a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="ae93f-205">SQLitePCL.raw updated to version 2.0.0</span></span>](#SQLitePCL) | <span data-ttu-id="ae93f-206">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-206">Low</span></span>      |
| [<span data-ttu-id="ae93f-207">NetTopologySuite se actualizó a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="ae93f-207">NetTopologySuite updated to version 2.0.0</span></span>](#NetTopologySuite) | <span data-ttu-id="ae93f-208">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-208">Low</span></span>      |
| [<span data-ttu-id="ae93f-209">Se usa Microsoft.Data.SqlClient en lugar de System.Data.SqlClient</span><span class="sxs-lookup"><span data-stu-id="ae93f-209">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>](#SqlClient) | <span data-ttu-id="ae93f-210">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-210">Low</span></span>      |
| [<span data-ttu-id="ae93f-211">Se deben configurar varias relaciones de referencia automática ambiguas</span><span class="sxs-lookup"><span data-stu-id="ae93f-211">Multiple ambiguous self-referencing relationships must be configured</span></span>](#mersa) | <span data-ttu-id="ae93f-212">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-212">Low</span></span>      |
| [<span data-ttu-id="ae93f-213">DbFunction.Schema es NULL o la cadena vacía lo configura para estar en el esquema predeterminado del modelo</span><span class="sxs-lookup"><span data-stu-id="ae93f-213">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>](#udf-empty-string) | <span data-ttu-id="ae93f-214">Bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-214">Low</span></span>      |
| [<span data-ttu-id="ae93f-215">~~EF Core 3.0 tiene como destino .NET Standard 2.1, y no .NET Standard 2.0~~ Revertido</span><span class="sxs-lookup"><span data-stu-id="ae93f-215">~~EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0~~ Reverted</span></span>](#netstandard21) | |
| [<span data-ttu-id="ae93f-216">~~La ejecución de consultas se registra en el nivel de depuración~~ Revertido</span><span class="sxs-lookup"><span data-stu-id="ae93f-216">~~Query execution is logged at Debug level~~ Reverted</span></span>](#qe) | |

## <a name="high-impact-changes"></a><span data-ttu-id="ae93f-217">Cambios de impacto alto</span><span class="sxs-lookup"><span data-stu-id="ae93f-217">High-impact changes</span></span>

### <a name="linq-queries-are-no-longer-evaluated-on-the-client"></a><span data-ttu-id="ae93f-218">Las consultas LINQ ya no se evalúan en el cliente</span><span class="sxs-lookup"><span data-stu-id="ae93f-218">LINQ queries are no longer evaluated on the client</span></span>

<span data-ttu-id="ae93f-219">[Problema de seguimiento n.° 14935](https://github.com/dotnet/efcore/issues/14935)
[Consulte también el problema n.° 12795](https://github.com/dotnet/efcore/issues/12795)</span><span class="sxs-lookup"><span data-stu-id="ae93f-219">[Tracking Issue #14935](https://github.com/dotnet/efcore/issues/14935)
[Also see issue #12795](https://github.com/dotnet/efcore/issues/12795)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-220">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-220">Old behavior</span></span>

<span data-ttu-id="ae93f-221">Antes de 3.0, cuando en EF Core no se podía convertir una expresión que formaba parte de una consulta SQL o un parámetro, la expresión se evaluaba de forma automática en el cliente.</span><span class="sxs-lookup"><span data-stu-id="ae93f-221">Before 3.0, when EF Core couldn't convert an expression that was part of a query to either SQL or a parameter, it automatically evaluated the expression on the client.</span></span>
<span data-ttu-id="ae93f-222">De forma predeterminada, la evaluación de cliente de expresiones potencialmente costosas solo desencadenaba una advertencia.</span><span class="sxs-lookup"><span data-stu-id="ae93f-222">By default, client evaluation of potentially expensive expressions only triggered a warning.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-223">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-223">New behavior</span></span>

<span data-ttu-id="ae93f-224">A partir de 3.0, en EF Core solo se permite que se evalúen en el cliente las expresiones en la proyección de nivel superior (la última llamada a `Select()` de la consulta).</span><span class="sxs-lookup"><span data-stu-id="ae93f-224">Starting with 3.0, EF Core only allows expressions in the top-level projection (the last `Select()` call in the query) to be evaluated on the client.</span></span>
<span data-ttu-id="ae93f-225">Cuando las expresiones de otra parte de la consulta no se pueden convertir en SQL o un parámetro, se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="ae93f-225">When expressions in any other part of the query can't be converted to either SQL or a parameter, an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-226">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-226">Why</span></span>

<span data-ttu-id="ae93f-227">La evaluación de cliente automática de las consultas permite que se ejecuten muchas consultas incluso si no se pueden convertir elementos importantes de ellas.</span><span class="sxs-lookup"><span data-stu-id="ae93f-227">Automatic client evaluation of queries allows many queries to be executed even if important parts of them can't be translated.</span></span>
<span data-ttu-id="ae93f-228">Esto puede provocar un comportamiento inesperado y potencialmente dañino que es posible que solo sea evidente en entornos de producción.</span><span class="sxs-lookup"><span data-stu-id="ae93f-228">This behavior can result in unexpected and potentially damaging behavior that may only become evident in production.</span></span>
<span data-ttu-id="ae93f-229">Por ejemplo, una condición en una llamada a `Where()` que no se puede convertir puede provocar que todas las filas de la tabla se transfieran desde el servidor de base de datos y que el filtro se aplique en el cliente.</span><span class="sxs-lookup"><span data-stu-id="ae93f-229">For example, a condition in a `Where()` call which can't be translated can cause all rows from the table to be transferred from the database server, and the filter to be applied on the client.</span></span>
<span data-ttu-id="ae93f-230">Esta situación puede pasar desapercibida fácilmente si la tabla solo contiene algunas filas en la fase de desarrollo, pero ser más grave cuando la aplicación pase a producción, donde la tabla puede contener millones de filas.</span><span class="sxs-lookup"><span data-stu-id="ae93f-230">This situation can easily go undetected if the table contains only a few rows in development, but hit hard when the application moves to production, where the table may contain millions of rows.</span></span>
<span data-ttu-id="ae93f-231">Las advertencias de evaluación de cliente también se suelen pasar por alto durante el desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-231">Client evaluation warnings also proved too easy to ignore during development.</span></span>

<span data-ttu-id="ae93f-232">Además de esto, la evaluación de cliente automática puede causar problemas en los que la mejora de la traducción de consultas para expresiones específicas provocaba cambios importantes no deseados entre versiones.</span><span class="sxs-lookup"><span data-stu-id="ae93f-232">Besides this, automatic client evaluation can lead to issues in which improving query translation for specific expressions caused unintended breaking changes between releases.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-233">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-233">Mitigations</span></span>

<span data-ttu-id="ae93f-234">Si una consulta no se puede traducir totalmente, vuelva a escribirla en un formato que se pueda traducir, o bien use `AsEnumerable()`, `ToList()` o una función similar para devolver los datos al cliente de forma explícita, donde después se puedan seguir procesando mediante LINQ to Objects.</span><span class="sxs-lookup"><span data-stu-id="ae93f-234">If a query can't be fully translated, then either rewrite the query in a form that can be translated, or use `AsEnumerable()`, `ToList()`, or similar to explicitly bring data back to the client where it can then be further processed using LINQ-to-Objects.</span></span>

<a name="no-longer"></a>

## <a name="medium-impact-changes"></a><span data-ttu-id="ae93f-235">Cambios de impacto medio</span><span class="sxs-lookup"><span data-stu-id="ae93f-235">Medium-impact changes</span></span>

### <a name="entity-framework-core-is-no-longer-part-of-the-aspnet-core-shared-framework"></a><span data-ttu-id="ae93f-236">Entity Framework Core ya no forma parte del marco compartido ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae93f-236">Entity Framework Core is no longer part of the ASP.NET Core shared framework</span></span>

[<span data-ttu-id="ae93f-237">Anuncios del problema de seguimiento n.º 325</span><span class="sxs-lookup"><span data-stu-id="ae93f-237">Tracking Issue Announcements#325</span></span>](https://github.com/aspnet/Announcements/issues/325)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-238">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-238">Old behavior</span></span>

<span data-ttu-id="ae93f-239">Antes de ASP.NET Core 3.0, cuando se agregaba una referencia de paquete a `Microsoft.AspNetCore.App` o `Microsoft.AspNetCore.All`, se incluía EF Core y algunos de los proveedores de datos de EF Core, como el de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="ae93f-239">Before ASP.NET Core 3.0, when you added a package reference to `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All`, it would include EF Core and some of the EF Core data providers like the SQL Server provider.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-240">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-240">New behavior</span></span>

<span data-ttu-id="ae93f-241">A partir de la versión 3.0, el marco compartido ASP.NET Core no incluye EF Core ni ningún proveedor de datos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="ae93f-241">Starting in 3.0, the ASP.NET Core shared framework doesn't include EF Core or any EF Core data providers.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-242">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-242">Why</span></span>

<span data-ttu-id="ae93f-243">Antes de este cambio, para obtener EF Core se necesitaban varios pasos en función de si la aplicación se destinaba a ASP.NET Core y SQL Server o no.</span><span class="sxs-lookup"><span data-stu-id="ae93f-243">Before this change, getting EF Core required different steps depending on whether the application targeted ASP.NET Core and SQL Server or not.</span></span>
<span data-ttu-id="ae93f-244">Además, la actualización de ASP.NET Core forzaba la de EF Core y el proveedor de SQL Server, lo que no siempre es deseable.</span><span class="sxs-lookup"><span data-stu-id="ae93f-244">Also, upgrading ASP.NET Core forced the upgrade of EF Core and the SQL Server provider, which isn't always desirable.</span></span>

<span data-ttu-id="ae93f-245">Con este cambio, la experiencia de obtención de EF Core es la misma en todos los proveedores, implementaciones admitidas de .NET y tipos de aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-245">With this change, the experience of getting EF Core is the same across all providers, supported .NET implementations and application types.</span></span>
<span data-ttu-id="ae93f-246">Ahora los desarrolladores también pueden controlar exactamente cuándo se actualizan EF Core y los proveedores de datos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="ae93f-246">Developers can also now control exactly when EF Core and EF Core data providers are upgraded.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-247">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-247">Mitigations</span></span>

<span data-ttu-id="ae93f-248">Para usar EF Core en una aplicación ASP.NET Core 3.0 o cualquier otra aplicación compatible, debe agregar de forma explícita una referencia de paquete al proveedor de base de datos de EF Core que se va a usar en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-248">To use EF Core in an ASP.NET Core 3.0 application or any other supported application, explicitly add a package reference to the EF Core database provider that your application will use.</span></span>

<a name="dotnet-ef"></a>

### <a name="the-ef-core-command-line-tool-dotnet-ef-is-no-longer-part-of-the-net-core-sdk"></a><span data-ttu-id="ae93f-249">La herramienta de línea de comandos de EF Core, dotnet ef, ya no forma parte del SDK de .NET Core</span><span class="sxs-lookup"><span data-stu-id="ae93f-249">The EF Core command-line tool, dotnet ef, is no longer part of the .NET Core SDK</span></span>

[<span data-ttu-id="ae93f-250">Problema de seguimiento n.º 14016</span><span class="sxs-lookup"><span data-stu-id="ae93f-250">Tracking Issue #14016</span></span>](https://github.com/dotnet/efcore/issues/14016)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-251">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-251">Old behavior</span></span>

<span data-ttu-id="ae93f-252">Antes de 3.0, la herramienta `dotnet ef` se incluía en el SDK de .NET Core y estaba disponible para usarse desde la línea de comandos de cualquier proyecto sin necesidad de realizar pasos adicionales.</span><span class="sxs-lookup"><span data-stu-id="ae93f-252">Before 3.0, the `dotnet ef` tool was included in the .NET Core SDK and was readily available to use from the command line from any project without requiring extra steps.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-253">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-253">New behavior</span></span>

<span data-ttu-id="ae93f-254">A partir de la versión 3.0, el SDK de .NET no incluye la herramienta `dotnet ef`, por lo que antes de poder usarla tendrá que instalarla de forma explícita como una herramienta local o global.</span><span class="sxs-lookup"><span data-stu-id="ae93f-254">Starting in 3.0, the .NET SDK does not include the `dotnet ef` tool, so before you can use it you have to explicitly install it as a local or global tool.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-255">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-255">Why</span></span>

<span data-ttu-id="ae93f-256">Este cambio nos permite distribuir y actualizar `dotnet ef` como una herramienta convencional de la CLI de .NET en NuGet, coherente con el hecho de que la versión 3.0 de EF Core también se distribuye siempre como un paquete NuGet.</span><span class="sxs-lookup"><span data-stu-id="ae93f-256">This change allows us to distribute and update `dotnet ef` as a regular .NET CLI tool on NuGet, consistent with the fact that the EF Core 3.0 is also always distributed as a NuGet package.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-257">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-257">Mitigations</span></span>

<span data-ttu-id="ae93f-258">Para poder administrar las migraciones o aplicar scaffolding a `DbContext`, instale `dotnet-ef` como herramienta global:</span><span class="sxs-lookup"><span data-stu-id="ae93f-258">To be able to manage migrations or scaffold a `DbContext`, install `dotnet-ef` as a global tool:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="ae93f-259">También se puede obtener una herramienta local cuando se restauran las dependencias de un proyecto que la declara como una dependencia de herramientas mediante un [archivo de manifiesto de herramientas](/dotnet/core/tools/global-tools#install-a-local-tool).</span><span class="sxs-lookup"><span data-stu-id="ae93f-259">You can also obtain it a local tool when you restore the dependencies of a project that declares it as a tooling dependency using a [tool manifest file](/dotnet/core/tools/global-tools#install-a-local-tool).</span></span>

## <a name="low-impact-changes"></a><span data-ttu-id="ae93f-260">Cambios de impacto bajo</span><span class="sxs-lookup"><span data-stu-id="ae93f-260">Low-impact changes</span></span>

<a name="fromsql"></a>

### <a name="fromsql-executesql-and-executesqlasync-have-been-renamed"></a><span data-ttu-id="ae93f-261">FromSql, ExecuteSql y ExecuteSqlAsync han cambiado de nombre</span><span class="sxs-lookup"><span data-stu-id="ae93f-261">FromSql, ExecuteSql, and ExecuteSqlAsync have been renamed</span></span>

[<span data-ttu-id="ae93f-262">Problema de seguimiento n.º 10996</span><span class="sxs-lookup"><span data-stu-id="ae93f-262">Tracking Issue #10996</span></span>](https://github.com/dotnet/efcore/issues/10996)

> [!IMPORTANT]
> <span data-ttu-id="ae93f-263">`ExecuteSqlCommand` y `ExecuteSqlCommandAsync` están en desuso.</span><span class="sxs-lookup"><span data-stu-id="ae93f-263">`ExecuteSqlCommand` and `ExecuteSqlCommandAsync` are deprecated.</span></span> <span data-ttu-id="ae93f-264">En su lugar, use estos métodos.</span><span class="sxs-lookup"><span data-stu-id="ae93f-264">Use these methods instead.</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-265">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-265">Old behavior</span></span>

<span data-ttu-id="ae93f-266">Antes de EF Core 3.0, estos nombres de métodos se sobrecargaban para funcionar tanto con una cadena normal como con una cadena que se debería interpolar en SQL y parámetros.</span><span class="sxs-lookup"><span data-stu-id="ae93f-266">Before EF Core 3.0, these method names were overloaded to work with either a normal string or a string that should be interpolated into SQL and parameters.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-267">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-267">New behavior</span></span>

<span data-ttu-id="ae93f-268">A partir de la versión EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw` y `ExecuteSqlRawAsync` para crear una consulta con parámetros donde los parámetros se pasan por separado de la cadena de consulta.</span><span class="sxs-lookup"><span data-stu-id="ae93f-268">Starting with EF Core 3.0, use `FromSqlRaw`, `ExecuteSqlRaw`, and `ExecuteSqlRawAsync` to create a parameterized query where the parameters are passed separately from the query string.</span></span>
<span data-ttu-id="ae93f-269">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-269">For example:</span></span>

```csharp
context.Products.FromSqlRaw(
    "SELECT * FROM Products WHERE Name = {0}",
    product.Name);
```

<span data-ttu-id="ae93f-270">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated` y `ExecuteSqlInterpolatedAsync` para crear una consulta con parámetros donde los parámetros se pasan como parte de una cadena de consulta interpolada.</span><span class="sxs-lookup"><span data-stu-id="ae93f-270">Use `FromSqlInterpolated`, `ExecuteSqlInterpolated`, and `ExecuteSqlInterpolatedAsync` to create a parameterized query where the parameters are passed as part of an interpolated query string.</span></span>
<span data-ttu-id="ae93f-271">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-271">For example:</span></span>

```csharp
context.Products.FromSqlInterpolated(
    $"SELECT * FROM Products WHERE Name = {product.Name}");
```

<span data-ttu-id="ae93f-272">Tenga en cuenta que las dos consultas anteriores producirán el mismo código SQL parametrizado con los mismos parámetros SQL.</span><span class="sxs-lookup"><span data-stu-id="ae93f-272">Note that both of the queries above will produce the same parameterized SQL with the same SQL parameters.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-273">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-273">Why</span></span>

<span data-ttu-id="ae93f-274">Las sobrecargas del método como esta facilitan las llamadas accidentales al método de cadena sin procesar cuando la intención era llamar al método de cadena interpolada y viceversa.</span><span class="sxs-lookup"><span data-stu-id="ae93f-274">Method overloads like this make it very easy to accidentally call the raw string method when the intent was to call the interpolated string method, and the other way around.</span></span>
<span data-ttu-id="ae93f-275">Esto podría resultar en consultas que no se parametrizan cuando deberían.</span><span class="sxs-lookup"><span data-stu-id="ae93f-275">This could result in queries not being parameterized when they should have been.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-276">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-276">Mitigations</span></span>

<span data-ttu-id="ae93f-277">Haga el cambio para usar los nuevos nombres de métodos.</span><span class="sxs-lookup"><span data-stu-id="ae93f-277">Switch to use the new method names.</span></span>

<a name="fromsqlsproc"></a>

### <a name="fromsql-method-when-used-with-stored-procedure-cannot-be-composed"></a><span data-ttu-id="ae93f-278">Cuando el método FromSql se usa con un procedimiento almacenado no se puede redactar</span><span class="sxs-lookup"><span data-stu-id="ae93f-278">FromSql method when used with stored procedure cannot be composed</span></span>

[<span data-ttu-id="ae93f-279">Problema de seguimiento n.° 15392</span><span class="sxs-lookup"><span data-stu-id="ae93f-279">Tracking Issue #15392</span></span>](https://github.com/dotnet/efcore/issues/15392)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-280">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-280">Old behavior</span></span>

<span data-ttu-id="ae93f-281">Antes de EF Core 3.0, el método FromSql intentaba detectar si se podía redactar en el código SQL pasado.</span><span class="sxs-lookup"><span data-stu-id="ae93f-281">Before EF Core 3.0, FromSql method tried to detect if the passed SQL can be composed upon.</span></span> <span data-ttu-id="ae93f-282">Cuando el código SQL no se podía redactar, como un procedimiento almacenado, realizaba la evaluación de cliente.</span><span class="sxs-lookup"><span data-stu-id="ae93f-282">It did client evaluation when the SQL was non-composable like a stored procedure.</span></span> <span data-ttu-id="ae93f-283">La consulta siguiente funcionaba al ejecutar el procedimiento almacenado en el servidor y aplicar FirstOrDefault en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="ae93f-283">The following query worked by running the stored procedure on the server and doing FirstOrDefault on the client side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").FirstOrDefault();
```

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-284">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-284">New behavior</span></span>

<span data-ttu-id="ae93f-285">A partir de EF Core 3.0, EF Core no intentará analizar el código SQL.</span><span class="sxs-lookup"><span data-stu-id="ae93f-285">Starting with EF Core 3.0, EF Core will not try to parse the SQL.</span></span> <span data-ttu-id="ae93f-286">Por tanto, si va a redactar después de FromSqlRaw/FromSqlInterpolated, EF Core redactará el código SQL generando una subconsulta.</span><span class="sxs-lookup"><span data-stu-id="ae93f-286">So if you are composing after FromSqlRaw/FromSqlInterpolated, then EF Core will compose the SQL by causing sub query.</span></span> <span data-ttu-id="ae93f-287">Por tanto, si usa un procedimiento almacenado con la redacción, obtendrá una excepción de sintaxis de SQL no válida.</span><span class="sxs-lookup"><span data-stu-id="ae93f-287">So if you are using a stored procedure with composition then you will get an exception for invalid SQL syntax.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-288">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-288">Why</span></span>

<span data-ttu-id="ae93f-289">EF Core 3.0 no admite la evaluación automática de cliente, ya que era propenso a errores, como se explica [aquí](#linq-queries-are-no-longer-evaluated-on-the-client).</span><span class="sxs-lookup"><span data-stu-id="ae93f-289">EF Core 3.0 does not support automatic client evaluation, since it was error prone as explained [here](#linq-queries-are-no-longer-evaluated-on-the-client).</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-290">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-290">Mitigations</span></span>

<span data-ttu-id="ae93f-291">Si usa un procedimiento almacenado en FromSqlRaw/FromSqlInterpolated, sabe que no se puede redactar, por lo que puede agregar __AsEnumerable/AsAsyncEnumerable__ justo después de la llamada al método FromSql para evitar cualquier redacción en el lado servidor.</span><span class="sxs-lookup"><span data-stu-id="ae93f-291">If you are using a stored procedure in FromSqlRaw/FromSqlInterpolated, you know that it cannot be composed upon, so you can add __AsEnumerable/AsAsyncEnumerable__ right after the FromSql method call to avoid any composition on server side.</span></span>

```csharp
context.Products.FromSqlRaw("[dbo].[Ten Most Expensive Products]").AsEnumerable().FirstOrDefault();
```

<a name="fromsql"></a>

### <a name="fromsql-methods-can-only-be-specified-on-query-roots"></a><span data-ttu-id="ae93f-292">Solo se pueden especificar métodos de FromSql en raíces de consulta.</span><span class="sxs-lookup"><span data-stu-id="ae93f-292">FromSql methods can only be specified on query roots</span></span>

[<span data-ttu-id="ae93f-293">Problema de seguimiento n.° 15704</span><span class="sxs-lookup"><span data-stu-id="ae93f-293">Tracking Issue #15704</span></span>](https://github.com/dotnet/efcore/issues/15704)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-294">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-294">Old behavior</span></span>

<span data-ttu-id="ae93f-295">Antes de EF Core 3.0, el método `FromSql` podía especificarse en cualquier lugar en la consulta.</span><span class="sxs-lookup"><span data-stu-id="ae93f-295">Before EF Core 3.0, the `FromSql` method could be specified anywhere in the query.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-296">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-296">New behavior</span></span>

<span data-ttu-id="ae93f-297">A partir de EF Core 3.0, los nuevos métodos `FromSqlRaw` y `FromSqlInterpolated` (que reemplazan a `FromSql`) solo pueden especificarse en las raíces de la consulta, es decir, directamente en `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-297">Starting with EF Core 3.0, the new `FromSqlRaw` and `FromSqlInterpolated` methods (which replace `FromSql`) can only be specified on query roots, i.e. directly on the `DbSet<>`.</span></span> <span data-ttu-id="ae93f-298">Si intenta especificarlos en cualquier otro lugar se producirá un error de compilación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-298">Attempting to specify them anywhere else will result in a compilation error.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-299">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-299">Why</span></span>

<span data-ttu-id="ae93f-300">La especificación de `FromSql` en cualquier otro lugar diferente de `DbSet` no tenía un significado o valor agregado, y podría causar ambigüedad en ciertos escenarios.</span><span class="sxs-lookup"><span data-stu-id="ae93f-300">Specifying `FromSql` anywhere other than on a `DbSet` had no added meaning or added value, and could cause ambiguity in certain scenarios.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-301">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-301">Mitigations</span></span>

<span data-ttu-id="ae93f-302">Las invocaciones de `FromSql` se deben mover para que estén directamente en el `DbSet` al que se aplican.</span><span class="sxs-lookup"><span data-stu-id="ae93f-302">`FromSql` invocations should be moved to be directly on the `DbSet` to which they apply.</span></span>

<a name="notrackingresolution"></a>

### <a name="no-tracking-queries-no-longer-perform-identity-resolution"></a><span data-ttu-id="ae93f-303">Las consultas sin seguimiento ya no realizan la resolución de la identidad</span><span class="sxs-lookup"><span data-stu-id="ae93f-303">No-tracking queries no longer perform identity resolution</span></span>

[<span data-ttu-id="ae93f-304">Problema de seguimiento n.º 13518</span><span class="sxs-lookup"><span data-stu-id="ae93f-304">Tracking Issue #13518</span></span>](https://github.com/dotnet/efcore/issues/13518)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-305">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-305">Old behavior</span></span>

<span data-ttu-id="ae93f-306">Antes de EF Core 3.0, se usaba la misma instancia de la entidad para cada aparición de una entidad con un tipo e identificador determinados.</span><span class="sxs-lookup"><span data-stu-id="ae93f-306">Before EF Core 3.0, the same entity instance would be used for every occurrence of an entity with a given type and ID.</span></span> <span data-ttu-id="ae93f-307">Este comportamiento coincide con el de las consultas de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="ae93f-307">This matches the behavior of tracking queries.</span></span> <span data-ttu-id="ae93f-308">Fijémonos en esta consulta:</span><span class="sxs-lookup"><span data-stu-id="ae93f-308">For example, this query:</span></span>

```csharp
var results = context.Products.Include(e => e.Category).AsNoTracking().ToList();
```

<span data-ttu-id="ae93f-309">Esta consulta devolverá la misma instancia de `Category` para cada elemento `Product` asociado con la categoría determinada.</span><span class="sxs-lookup"><span data-stu-id="ae93f-309">would return the same `Category` instance for each `Product` that is associated with the given category.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-310">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-310">New behavior</span></span>

<span data-ttu-id="ae93f-311">A partir de EF Core 3.0, se crean distintas instancias de la entidad si se encuentra una entidad con un tipo e identificador determinados en varias ubicaciones del gráfico devuelto.</span><span class="sxs-lookup"><span data-stu-id="ae93f-311">Starting with EF Core 3.0, different entity instances will be created when an entity with a given type and ID is encountered at different places in the returned graph.</span></span> <span data-ttu-id="ae93f-312">Por ejemplo, la consulta anterior ahora devolverá una nueva instancia de `Category` para cada elemento `Product` cuando haya dos productos asociados a la misma categoría.</span><span class="sxs-lookup"><span data-stu-id="ae93f-312">For example, the query above will now return a new `Category` instance for each `Product` even when two products are associated with the same category.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-313">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-313">Why</span></span>

<span data-ttu-id="ae93f-314">La resolución de las identidades (es decir, el hecho de determinar que una entidad tiene los mismos tipo e identificador que la entidad encontrada) agrega más rendimiento y sobrecarga de memoria.</span><span class="sxs-lookup"><span data-stu-id="ae93f-314">Identity resolution (that is, determining that an entity has the same type and ID as a previously encountered entity) adds additional performance and memory overhead.</span></span> <span data-ttu-id="ae93f-315">Este enfoque suele ser contrario a por qué las consultas sin seguimiento se usan en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="ae93f-315">This usually runs counter to why no-tracking queries are used in the first place.</span></span> <span data-ttu-id="ae93f-316">Además, aunque la resolución de las identidades a veces puede resultar útil, no es necesaria si las entidades se van a serializar y enviar a un cliente, algo habitual para las consultas sin seguimiento.</span><span class="sxs-lookup"><span data-stu-id="ae93f-316">Also, while identity resolution can sometimes be useful, it is not needed if the entities are to be serialized and sent to a client, which is common for no-tracking queries.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-317">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-317">Mitigations</span></span>

<span data-ttu-id="ae93f-318">Si se requiere la resolución de identidad, use una consulta de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="ae93f-318">Use a tracking query if identity resolution is required.</span></span>

<a name="tkv"></a>

### <a name="temporary-key-values-are-no-longer-set-onto-entity-instances"></a><span data-ttu-id="ae93f-319">Los valores de clave temporal ya no se establecen en instancias de entidad</span><span class="sxs-lookup"><span data-stu-id="ae93f-319">Temporary key values are no longer set onto entity instances</span></span>

[<span data-ttu-id="ae93f-320">Problema de seguimiento n.º 12378</span><span class="sxs-lookup"><span data-stu-id="ae93f-320">Tracking Issue #12378</span></span>](https://github.com/dotnet/efcore/issues/12378)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-321">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-321">Old behavior</span></span>

<span data-ttu-id="ae93f-322">Antes de EF Core 3.0, los valores temporales se asignaban a todas las propiedades de clave para las que posteriormente la base de datos generaba un valor real.</span><span class="sxs-lookup"><span data-stu-id="ae93f-322">Before EF Core 3.0, temporary values were assigned to all key properties that would later have a real value generated by the database.</span></span>
<span data-ttu-id="ae93f-323">Normalmente, estos valores temporales eran números negativos grandes.</span><span class="sxs-lookup"><span data-stu-id="ae93f-323">Usually these temporary values were large negative numbers.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-324">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-324">New behavior</span></span>

<span data-ttu-id="ae93f-325">A partir de la versión 3.0, en EF Core se almacena el valor de clave temporal como parte de la información de seguimiento de la entidad y la propiedad clave en sí no se modifica.</span><span class="sxs-lookup"><span data-stu-id="ae93f-325">Starting with 3.0, EF Core stores the temporary key value as part of the entity's tracking information, and leaves the key property itself unchanged.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-326">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-326">Why</span></span>

<span data-ttu-id="ae93f-327">Este cambio se ha realizado para evitar que los valores de clave temporales se conviertan erróneamente en permanentes cuando una entidad de la que previamente una instancia de `DbContext` ha realizado el seguimiento se mueve a otra instancia de `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-327">This change was made to prevent temporary key values from erroneously becoming permanent when an entity that has been previously tracked by some `DbContext` instance is moved to a different `DbContext` instance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-328">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-328">Mitigations</span></span>

<span data-ttu-id="ae93f-329">Las aplicaciones que asignan valores de clave principal a claves externas para crear asociaciones entre entidades pueden depender del comportamiento anterior si las claves principales son generadas por el almacén y pertenecen a entidades en el estado `Added`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-329">Applications that assign primary key values onto foreign keys to form associations between entities may depend on the old behavior if the primary keys are store-generated and belong to entities in the `Added` state.</span></span>
<span data-ttu-id="ae93f-330">Esto se puede evitar con las siguientes situaciones:</span><span class="sxs-lookup"><span data-stu-id="ae93f-330">This can be avoided by:</span></span>

* <span data-ttu-id="ae93f-331">No se usan claves generadas por el almacén.</span><span class="sxs-lookup"><span data-stu-id="ae93f-331">Not using store-generated keys.</span></span>
* <span data-ttu-id="ae93f-332">Se establecen propiedades de navegación para crear relaciones en lugar de establecer valores de clave externa.</span><span class="sxs-lookup"><span data-stu-id="ae93f-332">Setting navigation properties to form relationships instead of setting foreign key values.</span></span>
* <span data-ttu-id="ae93f-333">Se obtienen los valores de clave temporal reales de la información de seguimiento de la entidad.</span><span class="sxs-lookup"><span data-stu-id="ae93f-333">Obtain the actual temporary key values from the entity's tracking information.</span></span>
<span data-ttu-id="ae93f-334">Por ejemplo, `context.Entry(blog).Property(e => e.Id).CurrentValue` devolverá el valor temporal aunque no se haya establecido `blog.Id`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-334">For example, `context.Entry(blog).Property(e => e.Id).CurrentValue` will return the temporary value even though `blog.Id` itself hasn't been set.</span></span>

<a name="dc"></a>

### <a name="detectchanges-honors-store-generated-key-values"></a><span data-ttu-id="ae93f-335">DetectChanges respeta los valores de clave generados por el almacén</span><span class="sxs-lookup"><span data-stu-id="ae93f-335">DetectChanges honors store-generated key values</span></span>

[<span data-ttu-id="ae93f-336">Problema de seguimiento n.º 14616</span><span class="sxs-lookup"><span data-stu-id="ae93f-336">Tracking Issue #14616</span></span>](https://github.com/dotnet/efcore/issues/14616)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-337">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-337">Old behavior</span></span>

<span data-ttu-id="ae93f-338">Antes de EF Core 3.0, se realizaba el seguimiento en el estado `Added` de las entidades sin seguimiento detectadas por `DetectChanges` y se insertaban como una fila nueva cuando se llamaba a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-338">Before EF Core 3.0, an untracked entity found by `DetectChanges` would be tracked in the `Added` state and inserted as a new row when `SaveChanges` is called.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-339">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-339">New behavior</span></span>

<span data-ttu-id="ae93f-340">A partir de EF Core 3.0, si una entidad usa valores de clave generados y se establece un valor de clave, se realizará el seguimiento de la entidad en el estado `Modified`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-340">Starting with EF Core 3.0, if an entity is using generated key values and some key value is set, then the entity will be tracked in the `Modified` state.</span></span>
<span data-ttu-id="ae93f-341">Esto significa que se supone que existe una fila para la entidad y que se actualizará cuando se llame a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-341">This means that a row for the entity is assumed to exist and it will be updated when `SaveChanges` is called.</span></span>
<span data-ttu-id="ae93f-342">Si no se establece el valor de clave, o bien si el tipo de entidad no usa claves generadas, se seguirá realizando el seguimiento de la entidad nueva como `Added` al igual que en las versiones anteriores.</span><span class="sxs-lookup"><span data-stu-id="ae93f-342">If the key value isn't set, or if the entity type isn't using generated keys, then the new entity will still be tracked as `Added` as in previous versions.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-343">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-343">Why</span></span>

<span data-ttu-id="ae93f-344">Este cambio se ha realizado para que sea más sencillo y coherente trabajar con gráficos de entidades desconectadas mientras se usan claves generadas por el almacén.</span><span class="sxs-lookup"><span data-stu-id="ae93f-344">This change was made to make it easier and more consistent to work with disconnected entity graphs while using store-generated keys.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-345">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-345">Mitigations</span></span>

<span data-ttu-id="ae93f-346">Este cambio puede interrumpir una aplicación si se configura un tipo de entidad para usar claves generadas, pero se establecen de forma explícita valores de clave para las instancias nuevas.</span><span class="sxs-lookup"><span data-stu-id="ae93f-346">This change can break an application if an entity type is configured to use generated keys but key values are explicitly set for new instances.</span></span>
<span data-ttu-id="ae93f-347">La solución consiste en configurar de forma explícita las propiedades de clave para que no usen valores generados.</span><span class="sxs-lookup"><span data-stu-id="ae93f-347">The fix is to explicitly configure the key properties to not use generated values.</span></span>
<span data-ttu-id="ae93f-348">Por ejemplo, con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="ae93f-348">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedNever();
```

<span data-ttu-id="ae93f-349">O bien con anotaciones de datos:</span><span class="sxs-lookup"><span data-stu-id="ae93f-349">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.None)]
public string Id { get; set; }
```

<a name="cascade"></a>

### <a name="cascade-deletions-now-happen-immediately-by-default"></a><span data-ttu-id="ae93f-350">Las eliminaciones en cascada ahora se realizan inmediatamente de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="ae93f-350">Cascade deletions now happen immediately by default</span></span>

[<span data-ttu-id="ae93f-351">Problema de seguimiento n.º 10114</span><span class="sxs-lookup"><span data-stu-id="ae93f-351">Tracking Issue #10114</span></span>](https://github.com/dotnet/efcore/issues/10114)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-352">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-352">Old behavior</span></span>

<span data-ttu-id="ae93f-353">Antes de la versión 3.0, en EF Core no se aplicaban acciones en cascada (eliminación de entidades dependientes cuando se eliminaba una entidad de seguridad obligatoria o cuando se rompía la relación con una entidad de seguridad obligatoria) hasta que se llamaba a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="ae93f-353">Before 3.0, EF Core applied cascading actions (deleting dependent entities when a required principal is deleted or when the relationship to a required principal is severed) did not happen until SaveChanges was called.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-354">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-354">New behavior</span></span>

<span data-ttu-id="ae93f-355">A partir de 3.0, en EF Core las acciones en cascada se aplican en cuanto se detecta la condición desencadenadora.</span><span class="sxs-lookup"><span data-stu-id="ae93f-355">Starting with 3.0, EF Core applies cascading actions as soon as the triggering condition is detected.</span></span>
<span data-ttu-id="ae93f-356">Por ejemplo, como resultado de la llamada a `context.Remove()` para eliminar una entidad de seguridad, todos los dependientes obligatorios relacionados de los que se realiza el seguimiento también se establecen en `Deleted` inmediatamente.</span><span class="sxs-lookup"><span data-stu-id="ae93f-356">For example, calling `context.Remove()` to delete a principal entity will result in all tracked related required dependents also being set to `Deleted` immediately.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-357">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-357">Why</span></span>

<span data-ttu-id="ae93f-358">Este cambio se ha realizado para mejorar la experiencia en escenarios de auditoría y enlace de datos, donde es importante comprender qué entidades se van a eliminar _antes de llamar a_  `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-358">This change was made to improve the experience for data binding and auditing scenarios where it is important to understand which entities will be deleted _before_ `SaveChanges` is called.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-359">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-359">Mitigations</span></span>

<span data-ttu-id="ae93f-360">El comportamiento anterior se puede restaurar mediante opciones de `context.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-360">The previous behavior can be restored through settings on `context.ChangeTracker`.</span></span>
<span data-ttu-id="ae93f-361">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-361">For example:</span></span>

```csharp
context.ChangeTracker.CascadeDeleteTiming = CascadeTiming.OnSaveChanges;
context.ChangeTracker.DeleteOrphansTiming = CascadeTiming.OnSaveChanges;
```

<a name="eager-loading-single-query"></a>

### <a name="eager-loading-of-related-entities-now-happens-in-a-single-query"></a><span data-ttu-id="ae93f-362">La carga diligente de entidades relacionadas ahora se realiza en una sola consulta</span><span class="sxs-lookup"><span data-stu-id="ae93f-362">Eager loading of related entities now happens in a single query</span></span>

[<span data-ttu-id="ae93f-363">Problema de seguimiento n.º 18022</span><span class="sxs-lookup"><span data-stu-id="ae93f-363">Tracking issue #18022</span></span>](https://github.com/dotnet/efcore/issues/18022)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-364">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-364">Old behavior</span></span>

<span data-ttu-id="ae93f-365">Antes de la versión 3.0, la carga diligente de navegaciones de colección a través de operadores `Include` provocaba la generación de varias consultas en la base de datos relacional, una para cada tipo de entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="ae93f-365">Before 3.0, eagerly loading collection navigations via `Include` operators caused multiple queries to be generated on relational database, one for each related entity type.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-366">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-366">New behavior</span></span>

<span data-ttu-id="ae93f-367">A partir de la versión 3.0, EF Core genera una sola consulta con operadores JOIN en las bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="ae93f-367">Starting with 3.0, EF Core generates a single query with JOINs on relational databases.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-368">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-368">Why</span></span>

<span data-ttu-id="ae93f-369">La emisión de varias consultas para implementar una única consulta LINQ provocaba numerosos problemas, incluido el rendimiento negativo, ya que se necesitaban varios recorridos de ida y vuelta a la base de datos, y problemas de coherencia de datos, ya que cada consulta podía observar un estado distinto de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="ae93f-369">Issuing multiple queries to implement a single LINQ query caused numerous issues, including negative performance as multiple database roundtrips were necessary, and data coherency issues as each query could observe a different state of the database.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-370">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-370">Mitigations</span></span>

<span data-ttu-id="ae93f-371">Aunque técnicamente esto no es un cambio importante, podría tener un efecto considerable en el rendimiento de la aplicación cuando una sola consulta contiene un gran número de operadores `Include` en las navegaciones de la colección.</span><span class="sxs-lookup"><span data-stu-id="ae93f-371">While technically this is not a breaking change, it could have a considerable effect on application performance when a single query contains a large number of `Include` operator on collection navigations.</span></span> <span data-ttu-id="ae93f-372">[Vea este comentario](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085) para obtener más información y para volver a escribir las consultas de una manera más eficaz.</span><span class="sxs-lookup"><span data-stu-id="ae93f-372">[See this comment](https://github.com/dotnet/efcore/issues/18022#issuecomment-542397085) for more information and for rewriting queries in a more efficient way.</span></span>

**

<a name="deletebehavior"></a>

### <a name="deletebehaviorrestrict-has-cleaner-semantics"></a><span data-ttu-id="ae93f-373">DeleteBehavior.Restrict tiene una semántica más limpia</span><span class="sxs-lookup"><span data-stu-id="ae93f-373">DeleteBehavior.Restrict has cleaner semantics</span></span>

[<span data-ttu-id="ae93f-374">Problema de seguimiento n.º 12661</span><span class="sxs-lookup"><span data-stu-id="ae93f-374">Tracking Issue #12661</span></span>](https://github.com/dotnet/efcore/issues/12661)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-375">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-375">Old behavior</span></span>

<span data-ttu-id="ae93f-376">Antes de la versión 3.0, `DeleteBehavior.Restrict` creaba claves externas en la base de datos con la semántica `Restrict`, pero también realizaba una corrección interna de manera no evidente.</span><span class="sxs-lookup"><span data-stu-id="ae93f-376">Before 3.0, `DeleteBehavior.Restrict` created foreign keys in the database with `Restrict` semantics, but also changed internal fixup in a non-obvious way.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-377">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-377">New behavior</span></span>

<span data-ttu-id="ae93f-378">A partir de la versión 3.0, `DeleteBehavior.Restrict` garantiza que las claves externas se crean con la semántica `Restrict`, es decir, sin cascadas y realizando una infracción de restricción, sin llevar a cabo correcciones internas de EF.</span><span class="sxs-lookup"><span data-stu-id="ae93f-378">Starting with 3.0, `DeleteBehavior.Restrict` ensures that foreign keys are created with `Restrict` semantics--that is, no cascades; throw on constraint violation--without also impacting EF internal fixup.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-379">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-379">Why</span></span>

<span data-ttu-id="ae93f-380">Este cambio se realizó para mejorar la experiencia de uso de `DeleteBehavior` de manera intuitiva sin efectos secundarios inesperados.</span><span class="sxs-lookup"><span data-stu-id="ae93f-380">This change was made to improve the experience for using `DeleteBehavior` in an intuitive manner, without unexpected side-effects.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-381">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-381">Mitigations</span></span>

<span data-ttu-id="ae93f-382">El comportamiento anterior se puede restaurar con `DeleteBehavior.ClientNoAction`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-382">The previous behavior can be restored by using `DeleteBehavior.ClientNoAction`.</span></span>

<a name="qt"></a>

### <a name="query-types-are-consolidated-with-entity-types"></a><span data-ttu-id="ae93f-383">Los tipos de consulta se consolidan con tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="ae93f-383">Query types are consolidated with entity types</span></span>

[<span data-ttu-id="ae93f-384">Problema de seguimiento n.º 14194</span><span class="sxs-lookup"><span data-stu-id="ae93f-384">Tracking Issue #14194</span></span>](https://github.com/dotnet/efcore/issues/14194)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-385">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-385">Old behavior</span></span>

<span data-ttu-id="ae93f-386">Antes de EF Core 3.0, los [tipos de consulta](xref:core/modeling/keyless-entity-types) eran un medio para consultar los datos que no definen una clave principal de una manera estructurada.</span><span class="sxs-lookup"><span data-stu-id="ae93f-386">Before EF Core 3.0, [query types](xref:core/modeling/keyless-entity-types) were a means to query data that doesn't define a primary key in a structured way.</span></span>
<span data-ttu-id="ae93f-387">Es decir, un tipo de consulta se usaba para asignar tipos de entidad sin claves (más probablemente desde una vista, pero posiblemente desde una tabla), mientras que un tipo de entidad estándar se usaba cuando había una clave disponible (más probablemente desde una tabla, pero posiblemente desde una vista).</span><span class="sxs-lookup"><span data-stu-id="ae93f-387">That is, a query type was used for mapping entity types without keys (more likely from a view, but possibly from a table) while a regular entity type was used when a key was available (more likely from a table, but possibly from a view).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-388">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-388">New behavior</span></span>

<span data-ttu-id="ae93f-389">Ahora un tipo de consulta se convierte en un tipo de entidad sin una clave principal.</span><span class="sxs-lookup"><span data-stu-id="ae93f-389">A query type now becomes just an entity type without a primary key.</span></span>
<span data-ttu-id="ae93f-390">Los tipos de entidad sin clave tienen la misma funcionalidad que los tipos de consulta de las versiones anteriores.</span><span class="sxs-lookup"><span data-stu-id="ae93f-390">Keyless entity types have the same functionality as query types in previous versions.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-391">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-391">Why</span></span>

<span data-ttu-id="ae93f-392">Este cambio se ha realizado para reducir la confusión en torno a la finalidad de los tipos de consulta.</span><span class="sxs-lookup"><span data-stu-id="ae93f-392">This change was made to reduce the confusion around the purpose of query types.</span></span>
<span data-ttu-id="ae93f-393">En concreto, son tipos de entidad sin clave y, por ello, intrínsecamente son de solo lectura, pero no se deben usar solo porque un tipo de entidad tenga que ser de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="ae93f-393">Specifically, they are keyless entity types and they are inherently read-only because of this, but they should not be used just because an entity type needs to be read-only.</span></span>
<span data-ttu-id="ae93f-394">Del mismo modo, se suelen asignar a vistas, pero solo porque las vistas no suelen definir claves.</span><span class="sxs-lookup"><span data-stu-id="ae93f-394">Likewise, they are often mapped to views, but this is only because views often don't define keys.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-395">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-395">Mitigations</span></span>

<span data-ttu-id="ae93f-396">Los elementos siguientes de la API ahora están obsoletos:</span><span class="sxs-lookup"><span data-stu-id="ae93f-396">The following parts of the API are now obsolete:</span></span>

* <span data-ttu-id="ae93f-397">**`ModelBuilder.Query<>()`** : en su lugar es necesario llamar a `ModelBuilder.Entity<>().HasNoKey()` para marcar un tipo de entidad como sin claves.</span><span class="sxs-lookup"><span data-stu-id="ae93f-397">**`ModelBuilder.Query<>()`** - Instead `ModelBuilder.Entity<>().HasNoKey()` needs to be called to mark an entity type as having no keys.</span></span>
<span data-ttu-id="ae93f-398">Esto todavía no se configurará por convención para evitar una configuración incorrecta cuando se espera una clave principal, pero no coincide con la convención.</span><span class="sxs-lookup"><span data-stu-id="ae93f-398">This would still not be configured by convention to avoid misconfiguration when a primary key is expected, but doesn't match the convention.</span></span>
* <span data-ttu-id="ae93f-399">**`DbQuery<>`** : en su lugar se debe usar `DbSet<>`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-399">**`DbQuery<>`** - Instead `DbSet<>` should be used.</span></span>
* <span data-ttu-id="ae93f-400">**`DbContext.Query<>()`** : en su lugar se debe usar `DbContext.Set<>()`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-400">**`DbContext.Query<>()`** - Instead `DbContext.Set<>()` should be used.</span></span>
* <span data-ttu-id="ae93f-401">**`IQueryTypeConfiguration<TQuery>`** : en su lugar se debe usar `IEntityTypeConfiguration<TEntity>`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-401">**`IQueryTypeConfiguration<TQuery>`** - Instead `IEntityTypeConfiguration<TEntity>` should be used.</span></span>

> [!NOTE]
> <span data-ttu-id="ae93f-402">Debido a [un problema en la versión 3.x](https://github.com/dotnet/efcore/issues/19537), al consultar entidades sin clave que tienen todas las propiedades establecidas en `null` se devolverá un `null` en lugar de una entidad, si este problema es aplicable a su escenario, agregue también lógica para administrar `null` en los resultados.</span><span class="sxs-lookup"><span data-stu-id="ae93f-402">Due to [an issue in 3.x](https://github.com/dotnet/efcore/issues/19537) when querying keyless entities that have all properties set to `null` a `null` will be returned instead of an entity, if this issue is applicable to your scenario also add logic to handle `null` in results.</span></span>

<a name="config"></a>

### <a name="configuration-api-for-owned-type-relationships-has-changed"></a><span data-ttu-id="ae93f-403">La API de configuración para las relaciones de tipo de propiedad ha cambiado</span><span class="sxs-lookup"><span data-stu-id="ae93f-403">Configuration API for owned type relationships has changed</span></span>

<span data-ttu-id="ae93f-404">[Problema de seguimiento n.º 12444](https://github.com/dotnet/efcore/issues/12444)
[Problema de seguimiento n.º 9148](https://github.com/dotnet/efcore/issues/9148)
[Problema de seguimiento n.º 14153](https://github.com/dotnet/efcore/issues/14153)</span><span class="sxs-lookup"><span data-stu-id="ae93f-404">[Tracking Issue #12444](https://github.com/dotnet/efcore/issues/12444)
[Tracking Issue #9148](https://github.com/dotnet/efcore/issues/9148)
[Tracking Issue #14153](https://github.com/dotnet/efcore/issues/14153)</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-405">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-405">Old behavior</span></span>

<span data-ttu-id="ae93f-406">Antes de EF Core 3.0, la configuración de la relación de propiedad se realizaba directamente después de la llamada a `OwnsOne` o `OwnsMany`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-406">Before EF Core 3.0, configuration of the owned relationship was performed directly after the `OwnsOne` or `OwnsMany` call.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-407">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-407">New behavior</span></span>

<span data-ttu-id="ae93f-408">A partir de EF Core 3.0, ahora hay una API fluida para configurar una propiedad de navegación para el propietario mediante `WithOwner()`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-408">Starting with EF Core 3.0, there is now fluent API to configure a navigation property to the owner using `WithOwner()`.</span></span>
<span data-ttu-id="ae93f-409">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-409">For example:</span></span>

```csharp
modelBuilder.Entity<Order>.OwnsOne(e => e.Details).WithOwner(e => e.Order);
```

<span data-ttu-id="ae93f-410">La configuración relacionada con la relación entre el propietario y lo que se posee ahora se debe encadenar después de `WithOwner()`, de forma similar a cómo se configuran otras relaciones.</span><span class="sxs-lookup"><span data-stu-id="ae93f-410">The configuration related to the relationship between owner and owned should now be chained after `WithOwner()` similarly to how other relationships are configured.</span></span>
<span data-ttu-id="ae93f-411">Pero la configuración del propio tipo de propiedad se seguirá encadenando después de `OwnsOne()/OwnsMany()`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-411">While the configuration for the owned type itself would still be chained after `OwnsOne()/OwnsMany()`.</span></span>
<span data-ttu-id="ae93f-412">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-412">For example:</span></span>

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

<span data-ttu-id="ae93f-413">Además, la llamada a `Entity()`, `HasOne()` o `Set()` con un tipo de propiedad de destino ahora iniciará una excepción.</span><span class="sxs-lookup"><span data-stu-id="ae93f-413">Additionally calling `Entity()`, `HasOne()`, or `Set()` with an owned type target will now throw an exception.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-414">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-414">Why</span></span>

<span data-ttu-id="ae93f-415">Este cambio se ha realizado para crear una separación más clara entre la configuración del propio tipo de propiedad y la _relación con_ el tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="ae93f-415">This change was made to create a cleaner separation between configuring the owned type itself and the _relationship to_ the owned type.</span></span>
<span data-ttu-id="ae93f-416">A su vez, esto elimina la ambigüedad y la confusión de métodos como `HasForeignKey`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-416">This in turn removes ambiguity and confusion around methods like `HasForeignKey`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-417">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-417">Mitigations</span></span>

<span data-ttu-id="ae93f-418">Cambie la configuración de las relaciones de tipo de propiedad para usar la nueva superficie de API, como se muestra en el ejemplo anterior.</span><span class="sxs-lookup"><span data-stu-id="ae93f-418">Change configuration of owned type relationships to use the new API surface as shown in the example above.</span></span>

<a name="de"></a>

### <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="ae93f-419">Ahora, las entidades dependientes que comparten la tabla con la entidad de seguridad son opcionales</span><span class="sxs-lookup"><span data-stu-id="ae93f-419">Dependent entities sharing the table with the principal are now optional</span></span>

[<span data-ttu-id="ae93f-420">Problema de seguimiento n.º 9005</span><span class="sxs-lookup"><span data-stu-id="ae93f-420">Tracking Issue #9005</span></span>](https://github.com/dotnet/efcore/issues/9005)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-421">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-421">Old behavior</span></span>

<span data-ttu-id="ae93f-422">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ae93f-422">Consider the following model:</span></span>

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

<span data-ttu-id="ae93f-423">Antes de EF Core 3.0, si `OrderDetails` era propiedad de `Order` o estaba asignado explícitamente a la misma tabla, siempre era necesaria una instancia de `OrderDetails` al agregar un elemento `Order` nuevo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-423">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then an `OrderDetails` instance was always required when adding a new `Order`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-424">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-424">New behavior</span></span>

<span data-ttu-id="ae93f-425">A partir de la versión 3.0, EF Core permite agregar `Order` sin `OrderDetails` y asigna todas las propiedades `OrderDetails` a excepción de la clave principal a columnas que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="ae93f-425">Starting with 3.0, EF Core allows to add an `Order` without an `OrderDetails` and maps all of the `OrderDetails` properties except the primary key to nullable columns.</span></span>
<span data-ttu-id="ae93f-426">Al realizar consultas, EF Core establece `OrderDetails` en `null` si ninguna de las propiedades necesarias tiene un valor o si no tiene propiedades necesarias más allá de la clave principal y todas las propiedades son `null`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-426">When querying EF Core sets `OrderDetails` to `null` if any of its required properties doesn't have a value or if it has no required properties besides the primary key and all properties are `null`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-427">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-427">Mitigations</span></span>

<span data-ttu-id="ae93f-428">Si el modelo tiene una tabla que comparte dependencias con todas las columnas opcionales, pero la navegación que apunta a ella no se espera que sea `null`, la aplicación debería modificarse para controlar los casos en los que la navegación sea `null`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-428">If your model has a table sharing dependent with all optional columns, but the navigation pointing to it is not expected to be `null` then the application should be modified to handle cases when the navigation is `null`.</span></span> <span data-ttu-id="ae93f-429">Si esto no es posible, debería agregarse una propiedad necesaria al tipo de entidad o, al menos, una entidad debería tener un valor distinto a `null` asignado.</span><span class="sxs-lookup"><span data-stu-id="ae93f-429">If this is not possible a required property should be added to the entity type or at least one property should have a non-`null` value assigned to it.</span></span>

<a name="aes"></a>

### <a name="all-entities-sharing-a-table-with-a-concurrency-token-column-have-to-map-it-to-a-property"></a><span data-ttu-id="ae93f-430">Todas las entidades que compartan una tabla con una columna de token de simultaneidad tienen que asignarla a una propiedad</span><span class="sxs-lookup"><span data-stu-id="ae93f-430">All entities sharing a table with a concurrency token column have to map it to a property</span></span>

[<span data-ttu-id="ae93f-431">Problema de seguimiento n.º 14154</span><span class="sxs-lookup"><span data-stu-id="ae93f-431">Tracking Issue #14154</span></span>](https://github.com/dotnet/efcore/issues/14154)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-432">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-432">Old behavior</span></span>

<span data-ttu-id="ae93f-433">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ae93f-433">Consider the following model:</span></span>

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

<span data-ttu-id="ae93f-434">Antes de EF Core 3.0, si `OrderDetails` era propiedad de `Order` o estaba asignado explícitamente a la misma tabla, si solo se actualizaba `OrderDetails`, no se actualizaba el valor `Version` en el cliente y se producía un error en la próxima actualización.</span><span class="sxs-lookup"><span data-stu-id="ae93f-434">Before EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table then updating just `OrderDetails` will not update `Version` value on client and the next update will fail.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-435">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-435">New behavior</span></span>

<span data-ttu-id="ae93f-436">A partir de la versión 3.0, EF Core propaga el nuevo valor `Version` en `Order` si posee `OrderDetails`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-436">Starting with 3.0, EF Core propagates the new `Version` value to `Order` if it owns `OrderDetails`.</span></span> <span data-ttu-id="ae93f-437">En caso contrario, se produce una excepción durante la validación del modelo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-437">Otherwise an exception is thrown during model validation.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-438">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-438">Why</span></span>

<span data-ttu-id="ae93f-439">Este cambio se realizó para evitar un valor de token de simultaneidad obsoleto cuando solo se actualiza una de las entidades asignadas a la misma tabla.</span><span class="sxs-lookup"><span data-stu-id="ae93f-439">This change was made to avoid a stale concurrency token value when only one of the entities mapped to the same table is updated.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-440">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-440">Mitigations</span></span>

<span data-ttu-id="ae93f-441">Todas las entidades que comparten la tabla deben incluir una propiedad que se asigna a la columna del token de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="ae93f-441">All entities sharing the table have to include a property that is mapped to the concurrency token column.</span></span> <span data-ttu-id="ae93f-442">Es posible crear una en estado reemplazado:</span><span class="sxs-lookup"><span data-stu-id="ae93f-442">It's possible the create one in shadow-state:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<OrderDetails>()
        .Property<byte[]>("Version").IsRowVersion().HasColumnName("Version");
}
```

<a name="owned-query"></a>

### <a name="owned-entities-cannot-be-queried-without-the-owner-using-a-tracking-query"></a><span data-ttu-id="ae93f-443">Las entidades en propiedad no se pueden consultar sin el propietario mediante una consulta de seguimiento</span><span class="sxs-lookup"><span data-stu-id="ae93f-443">Owned entities cannot be queried without the owner using a tracking query</span></span>

[<span data-ttu-id="ae93f-444">Problema de seguimiento n.º 18876</span><span class="sxs-lookup"><span data-stu-id="ae93f-444">Tracking Issue #18876</span></span>](https://github.com/dotnet/efcore/issues/18876)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-445">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-445">Old behavior</span></span>

<span data-ttu-id="ae93f-446">Antes de EF Core 3.0, las entidades en propiedad se podían consultar como cualquier otra navegación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-446">Before EF Core 3.0, the owned entities could be queried as any other navigation.</span></span>

```csharp
context.People.Select(p => p.Address);
```

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-447">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-447">New behavior</span></span>

<span data-ttu-id="ae93f-448">A partir de la versión 3.0, EF Core iniciará una excepción si una consulta de seguimiento proyecta una entidad en propiedad sin el propietario.</span><span class="sxs-lookup"><span data-stu-id="ae93f-448">Starting with 3.0, EF Core will throw if a tracking query projects an owned entity without the owner.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-449">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-449">Why</span></span>

<span data-ttu-id="ae93f-450">Las entidades en propiedad no se pueden manipular sin el propietario, por lo que en la mayoría de los casos es un error consultarlas de esta manera.</span><span class="sxs-lookup"><span data-stu-id="ae93f-450">Owned entities cannot be manipulated without the owner, so in the vast majority of cases querying them in this way is an error.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-451">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-451">Mitigations</span></span>

<span data-ttu-id="ae93f-452">Si se debe realizar el seguimiento de la entidad en propiedad para modificarla de cualquier manera posterior, el propietario se debe incluir en la consulta.</span><span class="sxs-lookup"><span data-stu-id="ae93f-452">If the owned entity should be tracked to be modified in any way later then the owner should be included in the query.</span></span>

<span data-ttu-id="ae93f-453">De lo contrario, agregue una llamada a `AsNoTracking()`:</span><span class="sxs-lookup"><span data-stu-id="ae93f-453">Otherwise add an `AsNoTracking()` call:</span></span>

```csharp
context.People.Select(p => p.Address).AsNoTracking();
```

<a name="ip"></a>

### <a name="inherited-properties-from-unmapped-types-are-now-mapped-to-a-single-column-for-all-derived-types"></a><span data-ttu-id="ae93f-454">Ahora, las propiedades heredadas de tipos sin asignar se asignan a una única columna para todos los tipos derivados</span><span class="sxs-lookup"><span data-stu-id="ae93f-454">Inherited properties from unmapped types are now mapped to a single column for all derived types</span></span>

[<span data-ttu-id="ae93f-455">Problema de seguimiento n.º 13998</span><span class="sxs-lookup"><span data-stu-id="ae93f-455">Tracking Issue #13998</span></span>](https://github.com/dotnet/efcore/issues/13998)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-456">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-456">Old behavior</span></span>

<span data-ttu-id="ae93f-457">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ae93f-457">Consider the following model:</span></span>

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

<span data-ttu-id="ae93f-458">Antes de EF Core 3.0, la propiedad `ShippingAddress` se asignaba a columnas distintas para `BulkOrder` y `Order` de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="ae93f-458">Before EF Core 3.0, the `ShippingAddress` property would be mapped to separate columns for `BulkOrder` and `Order` by default.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-459">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-459">New behavior</span></span>

<span data-ttu-id="ae93f-460">A partir de la versión3.0, EF Core solo crea una columna para `ShippingAddress`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-460">Starting with 3.0, EF Core only creates one column for `ShippingAddress`.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-461">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-461">Why</span></span>

<span data-ttu-id="ae93f-462">El comportamiento anterior no era el esperado.</span><span class="sxs-lookup"><span data-stu-id="ae93f-462">The old behavoir was unexpected.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-463">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-463">Mitigations</span></span>

<span data-ttu-id="ae93f-464">Todavía se puede asignar explícitamente la propiedad a columnas separadas en los tipos derivados:</span><span class="sxs-lookup"><span data-stu-id="ae93f-464">The property can still be explicitly mapped to separate column on the derived types:</span></span>

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

### <a name="the-foreign-key-property-convention-no-longer-matches-same-name-as-the-principal-property"></a><span data-ttu-id="ae93f-465">La convención de propiedad de clave externa ya no coincide con el mismo nombre que la propiedad de entidad de seguridad</span><span class="sxs-lookup"><span data-stu-id="ae93f-465">The foreign key property convention no longer matches same name as the principal property</span></span>

[<span data-ttu-id="ae93f-466">Problema de seguimiento n.º 13274</span><span class="sxs-lookup"><span data-stu-id="ae93f-466">Tracking Issue #13274</span></span>](https://github.com/dotnet/efcore/issues/13274)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-467">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-467">Old behavior</span></span>

<span data-ttu-id="ae93f-468">Considere el modelo siguiente:</span><span class="sxs-lookup"><span data-stu-id="ae93f-468">Consider the following model:</span></span>

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

<span data-ttu-id="ae93f-469">Antes de EF Core 3.0, se podía usar la propiedad `CustomerId` para la clave externa por convención.</span><span class="sxs-lookup"><span data-stu-id="ae93f-469">Before EF Core 3.0, the `CustomerId` property would be used for the foreign key by convention.</span></span>
<span data-ttu-id="ae93f-470">Pero si `Order` es un tipo de propiedad, entonces esto convertiría también a `CustomerId` en la clave principal, algo que no suele ser lo esperado.</span><span class="sxs-lookup"><span data-stu-id="ae93f-470">However, if `Order` is an owned type, then this would also make `CustomerId` the primary key and this isn't usually the expectation.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-471">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-471">New behavior</span></span>

<span data-ttu-id="ae93f-472">A partir de la versión 3.0, EF Core no intenta usar las propiedades de claves externas por convención si tienen el mismo nombre que la propiedad de entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="ae93f-472">Starting with 3.0, EF Core doesn't try to use properties for foreign keys by convention if they have the same name as the principal property.</span></span>
<span data-ttu-id="ae93f-473">Los patrones de nombre de tipo de entidad de seguridad concatenado con el nombre de propiedad de la entidad de seguridad y de nombre de navegación concatenado con el nombre de propiedad de la entidad de seguridad todavía se hacen coincidir.</span><span class="sxs-lookup"><span data-stu-id="ae93f-473">Principal type name concatenated with principal property name, and navigation name concatenated with principal property name patterns are still matched.</span></span>
<span data-ttu-id="ae93f-474">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-474">For example:</span></span>

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

#### <a name="why"></a><span data-ttu-id="ae93f-475">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-475">Why</span></span>

<span data-ttu-id="ae93f-476">Este cambio se ha realizado para evitar definir erróneamente una propiedad de clave principal en el tipo de propiedad.</span><span class="sxs-lookup"><span data-stu-id="ae93f-476">This change was made to avoid erroneously defining a primary key property on the owned type.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-477">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-477">Mitigations</span></span>

<span data-ttu-id="ae93f-478">Si la propiedad se ha diseñado para ser la clave externa y, por tanto, parte de la clave principal, se debe configurar explícitamente como tal.</span><span class="sxs-lookup"><span data-stu-id="ae93f-478">If the property was intended to be the foreign key, and hence part of the primary key, then explicitly configure it as such.</span></span>

<a name="dbc"></a>

### <a name="database-connection-is-now-closed-if-not-used-anymore-before-the-transactionscope-has-been-completed"></a><span data-ttu-id="ae93f-479">Ahora, la conexión de base de datos se cierra si ya no se usa antes de que se complete TransactionScope</span><span class="sxs-lookup"><span data-stu-id="ae93f-479">Database connection is now closed if not used anymore before the TransactionScope has been completed</span></span>

[<span data-ttu-id="ae93f-480">Problema de seguimiento n.º 14218</span><span class="sxs-lookup"><span data-stu-id="ae93f-480">Tracking Issue #14218</span></span>](https://github.com/dotnet/efcore/issues/14218)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-481">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-481">Old behavior</span></span>

<span data-ttu-id="ae93f-482">Antes de EF Core 3.0, si el contexto abría la conexión dentro de `TransactionScope`, la conexión permanecía abierta mientras el ámbito actual `TransactionScope` estuviese activo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-482">Before EF Core 3.0, if the context opens the connection inside a `TransactionScope`, the connection remains open while the current `TransactionScope` is active.</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-483">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-483">New behavior</span></span>

<span data-ttu-id="ae93f-484">A partir de la versión 3.0, EF Core cierra la conexión en cuanto se deja de usar.</span><span class="sxs-lookup"><span data-stu-id="ae93f-484">Starting with 3.0, EF Core closes the connection as soon as it's done using it.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-485">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-485">Why</span></span>

<span data-ttu-id="ae93f-486">Este cambio permite usar varios contextos en el mismo ámbito `TransactionScope`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-486">This change allows to use multiple contexts in the same `TransactionScope`.</span></span> <span data-ttu-id="ae93f-487">El comportamiento nuevo también coincide con el de EF6.</span><span class="sxs-lookup"><span data-stu-id="ae93f-487">The new behavior also matches EF6.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-488">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-488">Mitigations</span></span>

<span data-ttu-id="ae93f-489">Si la conexión debe permanecer abierta, una llamada explícita a `OpenConnection()` asegurará que EF Core no la cierre de forma prematura:</span><span class="sxs-lookup"><span data-stu-id="ae93f-489">If the connection needs to remain open explicit call to `OpenConnection()` will ensure that EF Core doesn't close it prematurely:</span></span>

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

### <a name="each-property-uses-independent-in-memory-integer-key-generation"></a><span data-ttu-id="ae93f-490">Cada propiedad usa la generación de claves enteras en memoria independiente</span><span class="sxs-lookup"><span data-stu-id="ae93f-490">Each property uses independent in-memory integer key generation</span></span>

[<span data-ttu-id="ae93f-491">Problema de seguimiento n.º 6872</span><span class="sxs-lookup"><span data-stu-id="ae93f-491">Tracking Issue #6872</span></span>](https://github.com/dotnet/efcore/issues/6872)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-492">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-492">Old behavior</span></span>

<span data-ttu-id="ae93f-493">Antes de EF Core 3.0, se usaba un generador de valores compartidos para todas las propiedades de clave entera en memoria.</span><span class="sxs-lookup"><span data-stu-id="ae93f-493">Before EF Core 3.0, one shared value generator was used for all in-memory integer key properties.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-494">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-494">New behavior</span></span>

<span data-ttu-id="ae93f-495">A partir de EF Core 3.0, cada propiedad de clave entera obtiene su propio generador de valores cuando se usa la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="ae93f-495">Starting with EF Core 3.0, each integer key property gets its own value generator when using the in-memory database.</span></span>
<span data-ttu-id="ae93f-496">Además, si se elimina la base de datos, la generación de claves se restablece para todas las tablas.</span><span class="sxs-lookup"><span data-stu-id="ae93f-496">Also, if the database is deleted, then key generation is reset for all tables.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-497">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-497">Why</span></span>

<span data-ttu-id="ae93f-498">Este cambio se ha realizado para alinear la generación de claves en memoria más estrechamente a la generación de claves de base de datos reales y para mejorar la capacidad para aislar las pruebas entre sí cuando se usa la base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="ae93f-498">This change was made to align in-memory key generation more closely to real database key generation and to improve the ability to isolate tests from each other when using the in-memory database.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-499">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-499">Mitigations</span></span>

<span data-ttu-id="ae93f-500">Esto puede interrumpir una aplicación que se base en el establecimiento de valores de clave específicos en memoria.</span><span class="sxs-lookup"><span data-stu-id="ae93f-500">This can break an application that is relying on specific in-memory key values to be set.</span></span>
<span data-ttu-id="ae93f-501">En su lugar, considere la posibilidad de no depender de valores de clave específicos, o bien de actualizar para que coincida con el comportamiento nuevo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-501">Consider instead not relying on specific key values, or updating to match the new behavior.</span></span>

### <a name="backing-fields-are-used-by-default"></a><span data-ttu-id="ae93f-502">Los campos de respaldo se usan de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="ae93f-502">Backing fields are used by default</span></span>

[<span data-ttu-id="ae93f-503">Problema de seguimiento n.º 12430</span><span class="sxs-lookup"><span data-stu-id="ae93f-503">Tracking Issue #12430</span></span>](https://github.com/dotnet/efcore/issues/12430)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-504">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-504">Old behavior</span></span>

<span data-ttu-id="ae93f-505">Antes de la versión 3.0, incluso si se conocía el campo de respaldo de una propiedad, de forma predeterminada en EF Core se leía y escribía el valor de propiedad mediante los métodos captadores y establecedores de propiedades.</span><span class="sxs-lookup"><span data-stu-id="ae93f-505">Before 3.0, even if the backing field for a property was known, EF Core would still by default read and write the property value using the property getter and setter methods.</span></span>
<span data-ttu-id="ae93f-506">La excepción era la ejecución de consultas, donde el campo de respaldo se establecía directamente si se conocía.</span><span class="sxs-lookup"><span data-stu-id="ae93f-506">The exception to this was query execution, where the backing field would be set directly if known.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-507">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-507">New behavior</span></span>

<span data-ttu-id="ae93f-508">A partir de EF Core 3.0, si se conoce el campo de respaldo para una propiedad, EF Core siempre la leerá y escribirá mediante el campo de respaldo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-508">Starting with EF Core 3.0, if the backing field for a property is known, then EF Core will always read and write that property using the backing field.</span></span>
<span data-ttu-id="ae93f-509">Esto podría provocar una interrupción de la aplicación si depende de un comportamiento adicional codificado en los métodos captadores o establecedores.</span><span class="sxs-lookup"><span data-stu-id="ae93f-509">This could cause an application break if the application is relying on additional behavior coded into the getter or setter methods.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-510">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-510">Why</span></span>

<span data-ttu-id="ae93f-511">Este cambio se ha realizado para evitar que EF Core desencadene erróneamente lógica de negocios de forma predeterminada al realizar operaciones de base de datos que implican entidades.</span><span class="sxs-lookup"><span data-stu-id="ae93f-511">This change was made to prevent EF Core from erroneously triggering business logic by default when performing database operations involving the entities.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-512">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-512">Mitigations</span></span>

<span data-ttu-id="ae93f-513">El comportamiento anterior a la versión 3.0 se puede restaurar mediante la configuración del modo de acceso de propiedad en `ModelBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-513">The pre-3.0 behavior can be restored through configuration of the property access mode on `ModelBuilder`.</span></span>
<span data-ttu-id="ae93f-514">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-514">For example:</span></span>

```csharp
modelBuilder.UsePropertyAccessMode(PropertyAccessMode.PreferFieldDuringConstruction);
```

### <a name="throw-if-multiple-compatible-backing-fields-are-found"></a><span data-ttu-id="ae93f-515">Inicio de excepciones si se encuentran varios campos de respaldo compatibles</span><span class="sxs-lookup"><span data-stu-id="ae93f-515">Throw if multiple compatible backing fields are found</span></span>

[<span data-ttu-id="ae93f-516">Problema de seguimiento n.º 12523</span><span class="sxs-lookup"><span data-stu-id="ae93f-516">Tracking Issue #12523</span></span>](https://github.com/dotnet/efcore/issues/12523)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-517">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-517">Old behavior</span></span>

<span data-ttu-id="ae93f-518">Antes de EF Core 3.0, si varios campos coincidían con las reglas para buscar el campo de respaldo de una propiedad, se elegía un campo según un orden de prioridad.</span><span class="sxs-lookup"><span data-stu-id="ae93f-518">Before EF Core 3.0, if multiple fields matched the rules for finding the backing field of a property, then one field would be chosen based on some precedence order.</span></span>
<span data-ttu-id="ae93f-519">Esto podía producir que, en caso de ambigüedad, se usara el campo incorrecto.</span><span class="sxs-lookup"><span data-stu-id="ae93f-519">This could cause the wrong field to be used in ambiguous cases.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-520">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-520">New behavior</span></span>

<span data-ttu-id="ae93f-521">A partir de EF Core 3.0, si varios campos coinciden con la misma propiedad, se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="ae93f-521">Starting with EF Core 3.0, if multiple fields are matched to the same property, then an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-522">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-522">Why</span></span>

<span data-ttu-id="ae93f-523">Este cambio se ha realizado para evitar de forma silenciosa el uso de un campo con respecto a otro cuando solo uno puede ser correcto.</span><span class="sxs-lookup"><span data-stu-id="ae93f-523">This change was made to avoid silently using one field over another when only one can be correct.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-524">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-524">Mitigations</span></span>

<span data-ttu-id="ae93f-525">En las propiedades con campos de respaldo ambiguos se debe especificar de forma explícita el campo que se va usar.</span><span class="sxs-lookup"><span data-stu-id="ae93f-525">Properties with ambiguous backing fields must have the field to use specified explicitly.</span></span>
<span data-ttu-id="ae93f-526">Por ejemplo, con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="ae93f-526">For example, using the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .HasField("_id");
```

### <a name="field-only-property-names-should-match-the-field-name"></a><span data-ttu-id="ae93f-527">Los nombres de propiedades de solo campo deben coincidir con el nombre del campo</span><span class="sxs-lookup"><span data-stu-id="ae93f-527">Field-only property names should match the field name</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-528">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-528">Old behavior</span></span>

<span data-ttu-id="ae93f-529">Antes de EF Core 3.0, una propiedad podía especificarse con un valor de cadena y, si no había ninguna propiedad con ese nombre en el tipo .NET, EF Core intentaba hacerla coincidir con un campo mediante reglas de convención.</span><span class="sxs-lookup"><span data-stu-id="ae93f-529">Before EF Core 3.0, a property could be specified by a string value and if no property with that name was found on the .NET type then EF Core would try to match it to a field using convention rules.</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-530">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-530">New behavior</span></span>

<span data-ttu-id="ae93f-531">A partir de EF Core 3.0, una propiedad de solo campo debe coincidir exactamente con el nombre del campo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-531">Starting with EF Core 3.0, a field-only property must match the field name exactly.</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("_id");
```

#### <a name="why"></a><span data-ttu-id="ae93f-532">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-532">Why</span></span>

<span data-ttu-id="ae93f-533">Este cambio se realizó para evitar el uso del mismo campo para dos propiedades con nombres similares. También hace que las reglas de coincidencia para propiedades solo de campo sean las mismas que para las propiedades asignadas a propiedades CLR.</span><span class="sxs-lookup"><span data-stu-id="ae93f-533">This change was made to avoid using the same field for two properties named similarly, it also makes the matching rules for field-only properties the same as for properties mapped to CLR properties.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-534">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-534">Mitigations</span></span>

<span data-ttu-id="ae93f-535">Las propiedades solo de campo deberían tener el mismo nombre que el campo al que están asignadas.</span><span class="sxs-lookup"><span data-stu-id="ae93f-535">Field-only properties must be named the same as the field they are mapped to.</span></span>
<span data-ttu-id="ae93f-536">En una próxima versión de EF Core 3.0 tenemos planeado volver a habilitar la configuración explícita de un nombre de campo distinto al nombre de la propiedad (vea el problema [n.° 15307](https://github.com/dotnet/efcore/issues/15307)):</span><span class="sxs-lookup"><span data-stu-id="ae93f-536">In a future release of EF Core after 3.0, we plan to re-enable explicitly configuring a field name that is different from the property name (see issue [#15307](https://github.com/dotnet/efcore/issues/15307)):</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property("Id")
    .HasField("_id");
```

<a name="adddbc"></a>

### <a name="adddbcontextadddbcontextpool-no-longer-call-addlogging-and-addmemorycache"></a><span data-ttu-id="ae93f-537">AddDbContext/AddDbContextPool ya no llaman a AddLogging ni a AddMemoryCache</span><span class="sxs-lookup"><span data-stu-id="ae93f-537">AddDbContext/AddDbContextPool no longer call AddLogging and AddMemoryCache</span></span>

[<span data-ttu-id="ae93f-538">Problema de seguimiento n.º 14756</span><span class="sxs-lookup"><span data-stu-id="ae93f-538">Tracking Issue #14756</span></span>](https://github.com/dotnet/efcore/issues/14756)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-539">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-539">Old behavior</span></span>

<span data-ttu-id="ae93f-540">Antes de EF Core 3.0, la llamada a `AddDbContext` o `AddDbContextPool` también podría registrar los servicios de almacenamiento en caché y de registro con inserción de dependencias a través de llamadas a [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) y [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="ae93f-540">Before EF Core 3.0, calling `AddDbContext` or `AddDbContextPool` would also register logging and memory caching services with DI through calls to [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) and [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-541">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-541">New behavior</span></span>

<span data-ttu-id="ae93f-542">A partir de EF Core 3.0, `AddDbContext` y `AddDbContextPool` ya no registrarán estos servicios con inserción de dependencias (DI).</span><span class="sxs-lookup"><span data-stu-id="ae93f-542">Starting with EF Core 3.0, `AddDbContext` and `AddDbContextPool` will no longer register these services with Dependency Injection (DI).</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-543">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-543">Why</span></span>

<span data-ttu-id="ae93f-544">EF Core 3.0 no requiere que estos servicios estén en el contenedor de inserción de dependencias de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-544">EF Core 3.0 does not require that these services are in the application's DI container.</span></span> <span data-ttu-id="ae93f-545">Pero si `ILoggerFactory` se registra en el contenedor de DI de la aplicación, EF Core lo empezará a usar de todos modos.</span><span class="sxs-lookup"><span data-stu-id="ae93f-545">However, if `ILoggerFactory` is registered in the application's DI container, then it will still be used by EF Core.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-546">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-546">Mitigations</span></span>

<span data-ttu-id="ae93f-547">Si la aplicación necesita estos servicios, regístrelos de manera explícita con el contenedor de DI mediante [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) o [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="ae93f-547">If your application needs these services, then register them explicitly with the DI container using  [AddLogging](/dotnet/api/microsoft.extensions.dependencyinjection.loggingservicecollectionextensions.addlogging) or [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

### <a name="addentityframework-adds-imemorycache-with-a-size-limit"></a><span data-ttu-id="ae93f-548">AddEntityFramework\* agrega IMemoryCache con un límite de tamaño</span><span class="sxs-lookup"><span data-stu-id="ae93f-548">AddEntityFramework\* adds IMemoryCache with a size limit</span></span>

[<span data-ttu-id="ae93f-549">Problema de seguimiento n.º 12905</span><span class="sxs-lookup"><span data-stu-id="ae93f-549">Tracking Issue #12905</span></span>](https://github.com/dotnet/efcore/issues/12905)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-550">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-550">Old behavior</span></span>

<span data-ttu-id="ae93f-551">Antes de EF Core 3.0, la llamada a los métodos `AddEntityFramework*` también registraba los servicios de almacenamiento en caché de memoria con inserción de dependencias sin límite de tamaño.</span><span class="sxs-lookup"><span data-stu-id="ae93f-551">Before EF Core 3.0, calling `AddEntityFramework*` methods would also register memory caching services with DI without a size limit.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-552">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-552">New behavior</span></span>

<span data-ttu-id="ae93f-553">A partir de EF Core 3.0, `AddEntityFramework*` registrará un servicio IMemoryCache con un límite de tamaño.</span><span class="sxs-lookup"><span data-stu-id="ae93f-553">Starting with EF Core 3.0, `AddEntityFramework*` will register an IMemoryCache service with a size limit.</span></span> <span data-ttu-id="ae93f-554">Si otros servicios agregados después dependen de IMemoryCache, pueden alcanzar rápidamente el límite predeterminado y provocar excepciones o un rendimiento degradado.</span><span class="sxs-lookup"><span data-stu-id="ae93f-554">If any other services added afterwards depend on IMemoryCache they can quickly reach the default limit causing exceptions or degraded performance.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-555">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-555">Why</span></span>

<span data-ttu-id="ae93f-556">El uso de IMemoryCache sin un límite podría dar lugar a un uso de memoria no controlado si hay un error en la lógica de almacenamiento en caché de las consultas o las consultas se generan de forma dinámica.</span><span class="sxs-lookup"><span data-stu-id="ae93f-556">Using IMemoryCache without a limit could result in uncontrolled memory usage if there is a bug in query caching logic or the queries are generated dynamically.</span></span> <span data-ttu-id="ae93f-557">Tener un límite predeterminado mitiga un posible ataque DoS.</span><span class="sxs-lookup"><span data-stu-id="ae93f-557">Having a default limit mitigates a potential DoS attack.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-558">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-558">Mitigations</span></span>

<span data-ttu-id="ae93f-559">En la mayoría de los casos, no es necesario llamar a `AddEntityFramework*` si también se llama a `AddDbContext` o `AddDbContextPool`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-559">In most cases calling `AddEntityFramework*` is not necessary if `AddDbContext` or `AddDbContextPool` is called as well.</span></span> <span data-ttu-id="ae93f-560">Por tanto, la mejor mitigación consiste en quitar la llamada a `AddEntityFramework*`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-560">Therefore, the best mitigation is to remove the `AddEntityFramework*` call.</span></span>

<span data-ttu-id="ae93f-561">Si la aplicación necesita estos servicios, registre de forma explícita una implementación de IMemoryCache con el contenedor de DI por anticipado mediante [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span><span class="sxs-lookup"><span data-stu-id="ae93f-561">If your application needs these services, then register a IMemoryCache implementation explicitly with the DI container beforehand using [AddMemoryCache](/dotnet/api/microsoft.extensions.dependencyinjection.memorycacheservicecollectionextensions.addmemorycache).</span></span>

<a name="dbe"></a>

### <a name="dbcontextentry-now-performs-a-local-detectchanges"></a><span data-ttu-id="ae93f-562">Ahora DbContext.Entry realiza una operación DetectChanges local</span><span class="sxs-lookup"><span data-stu-id="ae93f-562">DbContext.Entry now performs a local DetectChanges</span></span>

[<span data-ttu-id="ae93f-563">Problema de seguimiento n.º 13552</span><span class="sxs-lookup"><span data-stu-id="ae93f-563">Tracking Issue #13552</span></span>](https://github.com/dotnet/efcore/issues/13552)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-564">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-564">Old behavior</span></span>

<span data-ttu-id="ae93f-565">Antes de EF Core 3.0, la llamada a `DbContext.Entry` provocaba que se detectaran cambios para todas las entidades con seguimiento.</span><span class="sxs-lookup"><span data-stu-id="ae93f-565">Before EF Core 3.0, calling `DbContext.Entry` would cause changes to be detected for all tracked entities.</span></span>
<span data-ttu-id="ae93f-566">Esto garantizaba que el estado expuesto en `EntityEntry` estuviera actualizado.</span><span class="sxs-lookup"><span data-stu-id="ae93f-566">This ensured that the state exposed in the `EntityEntry` was up-to-date.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-567">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-567">New behavior</span></span>

<span data-ttu-id="ae93f-568">A partir de EF Core 3.0, ahora la llamada a `DbContext.Entry` solo intenta detectar cambios en la entidad dada y cualquier entidad de seguridad relacionada con ella de la que se haya realizado el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="ae93f-568">Starting with EF Core 3.0, calling `DbContext.Entry` will now only attempt to detect changes in the given entity and any tracked principal entities related to it.</span></span>
<span data-ttu-id="ae93f-569">Esto significa que es posible que la llamada a este método no haya detectado otros cambios, lo que podría tener implicaciones en el estado de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-569">This means that changes elsewhere may not have been detected by calling this method, which could have implications on application state.</span></span>

<span data-ttu-id="ae93f-570">Observe que si `ChangeTracker.AutoDetectChangesEnabled` se establece en `false` incluso esta detección de cambios local se deshabilitará.</span><span class="sxs-lookup"><span data-stu-id="ae93f-570">Note that if `ChangeTracker.AutoDetectChangesEnabled` is set to `false` then even this local change detection will be disabled.</span></span>

<span data-ttu-id="ae93f-571">Otros métodos que provocan la detección de cambios (como `ChangeTracker.Entries` y `SaveChanges`) siguen provocando una acción `DetectChanges` completa de todas las entidades de las que se realiza el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="ae93f-571">Other methods that cause change detection--for example `ChangeTracker.Entries` and `SaveChanges`--still cause a full `DetectChanges` of all tracked entities.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-572">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-572">Why</span></span>

<span data-ttu-id="ae93f-573">Este cambio se ha realizado para mejorar el rendimiento predeterminado del uso de `context.Entry`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-573">This change was made to improve the default performance of using `context.Entry`.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-574">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-574">Mitigations</span></span>

<span data-ttu-id="ae93f-575">Llame a `ChangeTracker.DetectChanges()` de forma explícita antes de llamar a `Entry` para garantizar el comportamiento anterior a la versión 3.0.</span><span class="sxs-lookup"><span data-stu-id="ae93f-575">Call `ChangeTracker.DetectChanges()` explicitly before calling `Entry` to ensure the pre-3.0 behavior.</span></span>

### <a name="string-and-byte-array-keys-are-not-client-generated-by-default"></a><span data-ttu-id="ae93f-576">El cliente no genera las claves de matriz de cadena y byte de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="ae93f-576">String and byte array keys are not client-generated by default</span></span>

[<span data-ttu-id="ae93f-577">Problema de seguimiento n.º 14617</span><span class="sxs-lookup"><span data-stu-id="ae93f-577">Tracking Issue #14617</span></span>](https://github.com/dotnet/efcore/issues/14617)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-578">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-578">Old behavior</span></span>

<span data-ttu-id="ae93f-579">Antes de EF Core 3.0, se podían usar las propiedades de clave `string` y `byte[]` sin tener que establecer de forma explícita un valor distinto de NULL.</span><span class="sxs-lookup"><span data-stu-id="ae93f-579">Before EF Core 3.0, `string` and `byte[]` key properties could be used without explicitly setting a non-null value.</span></span>
<span data-ttu-id="ae93f-580">En ese caso, el valor de clave se generaba en el cliente como un GUID, que se serializaba en bytes para `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-580">In such a case, the key value would be generated on the client as a GUID, serialized to bytes for `byte[]`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-581">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-581">New behavior</span></span>

<span data-ttu-id="ae93f-582">A partir de EF Core 3.0, se iniciará una excepción en la que indica que no se ha establecido ningún valor de clave.</span><span class="sxs-lookup"><span data-stu-id="ae93f-582">Starting with EF Core 3.0 an exception will be thrown indicating that no key value has been set.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-583">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-583">Why</span></span>

<span data-ttu-id="ae93f-584">Este cambio se ha realizado porque los valores `string`/`byte[]` generados por el cliente no suelen ser útiles, y el comportamiento predeterminado dificultaba razonar sobre los valores de clave generados de una forma habitual.</span><span class="sxs-lookup"><span data-stu-id="ae93f-584">This change was made because client-generated `string`/`byte[]` values generally aren't useful, and the default behavior made it hard to reason about generated key values in a common way.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-585">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-585">Mitigations</span></span>

<span data-ttu-id="ae93f-586">Se puede obtener el comportamiento anterior a la versión 3.0 si se especifica de forma explícita que las propiedades de clave deben usar los valores generados si no se establece ningún otro valor distinto de NULL.</span><span class="sxs-lookup"><span data-stu-id="ae93f-586">The pre-3.0 behavior can be obtained by explicitly specifying that the key properties should use generated values if no other non-null value is set.</span></span>
<span data-ttu-id="ae93f-587">Por ejemplo, con la API fluida:</span><span class="sxs-lookup"><span data-stu-id="ae93f-587">For example, with the fluent API:</span></span>

```csharp
modelBuilder
    .Entity<Blog>()
    .Property(e => e.Id)
    .ValueGeneratedOnAdd();
```

<span data-ttu-id="ae93f-588">O bien con anotaciones de datos:</span><span class="sxs-lookup"><span data-stu-id="ae93f-588">Or with data annotations:</span></span>

```csharp
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public string Id { get; set; }
```

<a name="ilf"></a>

### <a name="iloggerfactory-is-now-a-scoped-service"></a><span data-ttu-id="ae93f-589">Ahora ILoggerFactory es un servicio con ámbito</span><span class="sxs-lookup"><span data-stu-id="ae93f-589">ILoggerFactory is now a scoped service</span></span>

[<span data-ttu-id="ae93f-590">Problema de seguimiento n.º 14698</span><span class="sxs-lookup"><span data-stu-id="ae93f-590">Tracking Issue #14698</span></span>](https://github.com/dotnet/efcore/issues/14698)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-591">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-591">Old behavior</span></span>

<span data-ttu-id="ae93f-592">Antes de EF Core 3.0, `ILoggerFactory` se registraba como un servicio de singleton.</span><span class="sxs-lookup"><span data-stu-id="ae93f-592">Before EF Core 3.0, `ILoggerFactory` was registered as a singleton service.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-593">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-593">New behavior</span></span>

<span data-ttu-id="ae93f-594">A partir de EF Core 3.0, `ILoggerFactory` ahora se registra como con ámbito.</span><span class="sxs-lookup"><span data-stu-id="ae93f-594">Starting with EF Core 3.0, `ILoggerFactory` is now registered as scoped.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-595">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-595">Why</span></span>

<span data-ttu-id="ae93f-596">Este cambio se ha realizado para permitir la asociación de un registrador con una instancia de `DbContext`, lo que habilita otras funciones y quita algunos casos de comportamiento patológico como un aumento vertiginoso de los proveedores de servicios internos.</span><span class="sxs-lookup"><span data-stu-id="ae93f-596">This change was made to allow association of a logger with a `DbContext` instance, which enables other functionality and removes some cases of pathological behavior such as an explosion of internal service providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-597">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-597">Mitigations</span></span>

<span data-ttu-id="ae93f-598">Este cambio no debería afectar al código de la aplicación a menos que registre y use servicios personalizados en el proveedor de servicios internos de EF Core.</span><span class="sxs-lookup"><span data-stu-id="ae93f-598">This change should not impact application code unless it is registering and using custom services on the EF Core internal service provider.</span></span>
<span data-ttu-id="ae93f-599">Esto no es habitual.</span><span class="sxs-lookup"><span data-stu-id="ae93f-599">This isn't common.</span></span>
<span data-ttu-id="ae93f-600">En estos casos, la mayoría de los elementos seguirá funcionando, pero cualquier servicio de singleton que dependiera de `ILoggerFactory` tendrá que cambiarse para obtener la interfaz `ILoggerFactory` de otra forma.</span><span class="sxs-lookup"><span data-stu-id="ae93f-600">In these cases, most things will still work, but any singleton service that was depending on `ILoggerFactory` will need to be changed to obtain the `ILoggerFactory` in a different way.</span></span>

<span data-ttu-id="ae93f-601">Si experimenta situaciones como esta, registre un problema en el [rastreador de problemas de GitHub de EF Core](https://github.com/dotnet/efcore/issues) para hacernos saber cómo usa `ILoggerFactory`, para que podamos comprender mejor cómo evitar esta interrupción en el futuro.</span><span class="sxs-lookup"><span data-stu-id="ae93f-601">If you run into situations like this, please file an issue at on the [EF Core GitHub issue tracker](https://github.com/dotnet/efcore/issues) to let us know how you are using `ILoggerFactory` such that we can better understand how not to break this again in the future.</span></span>

### <a name="lazy-loading-proxies-no-longer-assume-navigation-properties-are-fully-loaded"></a><span data-ttu-id="ae93f-602">En los proxies de carga diferida ya no se supone que las propiedades de navegación están totalmente cargadas</span><span class="sxs-lookup"><span data-stu-id="ae93f-602">Lazy-loading proxies no longer assume navigation properties are fully loaded</span></span>

[<span data-ttu-id="ae93f-603">Problema de seguimiento n.º 12780</span><span class="sxs-lookup"><span data-stu-id="ae93f-603">Tracking Issue #12780</span></span>](https://github.com/dotnet/efcore/issues/12780)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-604">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-604">Old behavior</span></span>

<span data-ttu-id="ae93f-605">Antes de EF Core 3.0, una vez que se eliminaba `DbContext` no había ninguna forma de saber si una determinada propiedad de navegación de una entidad obtenida de ese contexto se había cargado completamente o no.</span><span class="sxs-lookup"><span data-stu-id="ae93f-605">Before EF Core 3.0, once a `DbContext` was disposed there was no way of knowing if a given navigation property on an entity obtained from that context was fully loaded or not.</span></span>
<span data-ttu-id="ae93f-606">En su lugar, los proxies asumían que una navegación de referencia se cargaba si tenía un valor distinto de NULL, y que una navegación de colección se cargaba si no estaba vacía.</span><span class="sxs-lookup"><span data-stu-id="ae93f-606">Proxies would instead assume that a reference navigation is loaded if it has a non-null value, and that a collection navigation is loaded if it isn't empty.</span></span>
<span data-ttu-id="ae93f-607">En estos casos, el intento de carga diferida era no operativo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-607">In these cases, attempting to lazy-load would be a no-op.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-608">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-608">New behavior</span></span>

<span data-ttu-id="ae93f-609">A partir de EF Core 3.0, los proxies realizan el seguimiento de si una propiedad de navegación se carga o no.</span><span class="sxs-lookup"><span data-stu-id="ae93f-609">Starting with EF Core 3.0, proxies keep track of whether or not a navigation property is loaded.</span></span>
<span data-ttu-id="ae93f-610">Esto significa que el intento de acceder a una propiedad de navegación que se carga después de que se haya eliminado el contexto siempre será no operativo, incluso cuando la navegación cargada está vacía o es NULL.</span><span class="sxs-lookup"><span data-stu-id="ae93f-610">This means attempting to access a navigation property that is loaded after the context has been disposed will always be a no-op, even when the loaded navigation is empty or null.</span></span>
<span data-ttu-id="ae93f-611">Por el contrario, el intento de acceder a una propiedad de navegación que no está cargada iniciará una excepción si el contexto se ha eliminado, incluso si la propiedad de navegación es una colección no vacía.</span><span class="sxs-lookup"><span data-stu-id="ae93f-611">Conversely, attempting to access a navigation property that isn't loaded will throw an exception if the context is disposed even if the navigation property is a non-empty collection.</span></span>
<span data-ttu-id="ae93f-612">Si se produce esta situación, significa que el código de aplicación está intentando usar la carga diferida en un momento no válido y que se debe cambiar la aplicación para que lo no haga.</span><span class="sxs-lookup"><span data-stu-id="ae93f-612">If this situation arises, it means the application code is attempting to use lazy-loading at an invalid time, and the application should be changed to not do this.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-613">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-613">Why</span></span>

<span data-ttu-id="ae93f-614">Este cambio se ha realizado para que el comportamiento sea coherente y correcto cuando se intenta la carga diferida de una instancia de `DbContext` eliminada.</span><span class="sxs-lookup"><span data-stu-id="ae93f-614">This change was made to make the behavior consistent and correct when attempting to lazy-load on a disposed `DbContext` instance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-615">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-615">Mitigations</span></span>

<span data-ttu-id="ae93f-616">Actualice el código de la aplicación para que no intente la carga diferida con un contexto eliminado, o bien configúrelo para que sea no operativo, como se describe en el mensaje de la excepción.</span><span class="sxs-lookup"><span data-stu-id="ae93f-616">Update application code to not attempt lazy-loading with a disposed context, or configure this to be a no-op as described in the exception message.</span></span>

### <a name="excessive-creation-of-internal-service-providers-is-now-an-error-by-default"></a><span data-ttu-id="ae93f-617">La creación excesiva de proveedores de servicios internos ahora es un error de forma predeterminada</span><span class="sxs-lookup"><span data-stu-id="ae93f-617">Excessive creation of internal service providers is now an error by default</span></span>

[<span data-ttu-id="ae93f-618">Problema de seguimiento n.º 10236</span><span class="sxs-lookup"><span data-stu-id="ae93f-618">Tracking Issue #10236</span></span>](https://github.com/dotnet/efcore/issues/10236)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-619">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-619">Old behavior</span></span>

<span data-ttu-id="ae93f-620">Antes de EF Core 3.0, se registraba una advertencia para una aplicación que creaba un número patológico de proveedores de servicios internos.</span><span class="sxs-lookup"><span data-stu-id="ae93f-620">Before EF Core 3.0, a warning would be logged for an application creating a pathological number of internal service providers.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-621">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-621">New behavior</span></span>

<span data-ttu-id="ae93f-622">A partir de EF Core 3.0, ahora esta advertencia se considera un error y se inicia una excepción.</span><span class="sxs-lookup"><span data-stu-id="ae93f-622">Starting with EF Core 3.0, this warning is now considered and error and an exception is thrown.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-623">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-623">Why</span></span>

<span data-ttu-id="ae93f-624">Este cambio se ha realizado para controlar mejor el código de la aplicación mediante la exposición de este caso patológico de una forma más explícita.</span><span class="sxs-lookup"><span data-stu-id="ae93f-624">This change was made to drive better application code through exposing this pathological case more explicitly.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-625">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-625">Mitigations</span></span>

<span data-ttu-id="ae93f-626">Cuando se produce este error, la acción más adecuada consiste en comprender la causa raíz y detener la creación de tantos proveedores de servicios internos.</span><span class="sxs-lookup"><span data-stu-id="ae93f-626">The most appropriate cause of action on encountering this error is to understand the root cause and stop creating so many internal service providers.</span></span>
<span data-ttu-id="ae93f-627">Pero el error se puede convertir en una advertencia (u omitirse) mediante configuración en `DbContextOptionsBuilder`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-627">However, the error can be converted back to a warning (or ignored) via configuration on the `DbContextOptionsBuilder`.</span></span>
<span data-ttu-id="ae93f-628">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-628">For example:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    optionsBuilder
        .ConfigureWarnings(w => w.Log(CoreEventId.ManyServiceProvidersCreatedWarning));
}
```

<a name="nbh"></a>

### <a name="new-behavior-for-hasonehasmany-called-with-a-single-string"></a><span data-ttu-id="ae93f-629">Comportamiento nuevo de HasOne/HasMany llamado con una sola cadena</span><span class="sxs-lookup"><span data-stu-id="ae93f-629">New behavior for HasOne/HasMany called with a single string</span></span>

[<span data-ttu-id="ae93f-630">Problema de seguimiento n.º 9171</span><span class="sxs-lookup"><span data-stu-id="ae93f-630">Tracking Issue #9171</span></span>](https://github.com/dotnet/efcore/issues/9171)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-631">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-631">Old behavior</span></span>

<span data-ttu-id="ae93f-632">Antes de EF Core 3.0, el código para llamar a `HasOne` o `HasMany` con una cadena se interpretaba de manera confusa.</span><span class="sxs-lookup"><span data-stu-id="ae93f-632">Before EF Core 3.0, code calling `HasOne` or `HasMany` with a single string was interpreted in a confusing way.</span></span>
<span data-ttu-id="ae93f-633">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-633">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Entrance").WithOne();
```

<span data-ttu-id="ae93f-634">El código parece relacionar `Samurai` con otro tipo de entidad mediante la propiedad de navegación `Entrance`, que puede ser privada.</span><span class="sxs-lookup"><span data-stu-id="ae93f-634">The code looks like it is relating `Samurai` to some other entity type using the `Entrance` navigation property, which may be private.</span></span>

<span data-ttu-id="ae93f-635">En realidad, este código intenta crear una relación con algún tipo de entidad denominada `Entrance` sin ninguna propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-635">In reality, this code attempts to create a relationship to some entity type called `Entrance` with no navigation property.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-636">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-636">New behavior</span></span>

<span data-ttu-id="ae93f-637">A partir de EF Core 3.0, el código anterior ahora hace lo que parecía que debía hacer antes.</span><span class="sxs-lookup"><span data-stu-id="ae93f-637">Starting with EF Core 3.0, the code above now does what it looked like it should have been doing before.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-638">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-638">Why</span></span>

<span data-ttu-id="ae93f-639">El comportamiento anterior era muy confuso, especialmente al leer el código de configuración y al buscar errores.</span><span class="sxs-lookup"><span data-stu-id="ae93f-639">The old behavior was very confusing, especially when reading the configuration code and looking for errors.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-640">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-640">Mitigations</span></span>

<span data-ttu-id="ae93f-641">Esto solo interrumpirá las aplicaciones que configuran de manera explícita las relaciones con cadenas para nombres de tipos y sin especificar explícitamente la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-641">This will only break applications that are explicitly configuring relationships using strings for type names and without specifying the navigation property explicitly.</span></span>
<span data-ttu-id="ae93f-642">Esto no es habitual.</span><span class="sxs-lookup"><span data-stu-id="ae93f-642">This is not common.</span></span>
<span data-ttu-id="ae93f-643">El comportamiento anterior se puede obtener al pasar de manera explícita `null` para el nombre de la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-643">The previous behavior can be obtained through explicitly passing `null` for the navigation property name.</span></span>
<span data-ttu-id="ae93f-644">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-644">For example:</span></span>

```csharp
modelBuilder.Entity<Samurai>().HasOne("Some.Entity.Type.Name", null).WithOne();
```

<a name="rtnt"></a>

### <a name="the-return-type-for-several-async-methods-has-been-changed-from-task-to-valuetask"></a><span data-ttu-id="ae93f-645">El tipo de valor devuelto para varios métodos asincrónicos se ha cambiado de Task a ValueTask</span><span class="sxs-lookup"><span data-stu-id="ae93f-645">The return type for several async methods has been changed from Task to ValueTask</span></span>

[<span data-ttu-id="ae93f-646">Problema de seguimiento n.º 15184</span><span class="sxs-lookup"><span data-stu-id="ae93f-646">Tracking Issue #15184</span></span>](https://github.com/dotnet/efcore/issues/15184)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-647">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-647">Old behavior</span></span>

<span data-ttu-id="ae93f-648">Antes, los siguientes métodos asincrónicos devolvían `Task<T>`:</span><span class="sxs-lookup"><span data-stu-id="ae93f-648">The following async methods previously returned a `Task<T>`:</span></span>

* `DbContext.FindAsync()`
* `DbSet.FindAsync()`
* `DbContext.AddAsync()`
* `DbSet.AddAsync()`
* <span data-ttu-id="ae93f-649">`ValueGenerator.NextValueAsync()` (y las clases derivadas)</span><span class="sxs-lookup"><span data-stu-id="ae93f-649">`ValueGenerator.NextValueAsync()` (and deriving classes)</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-650">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-650">New behavior</span></span>

<span data-ttu-id="ae93f-651">Dichos métodos ahora devuelven `ValueTask<T>` durante el mismo `T` que antes.</span><span class="sxs-lookup"><span data-stu-id="ae93f-651">The aforementioned methods now return a `ValueTask<T>` over the same `T` as before.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-652">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-652">Why</span></span>

<span data-ttu-id="ae93f-653">Este cambio reduce el número de asignaciones de montones que se producen al invocar estos métodos, lo que mejora el rendimiento general.</span><span class="sxs-lookup"><span data-stu-id="ae93f-653">This change reduces the number of heap allocations incurred when invoking these methods, improving general performance.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-654">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-654">Mitigations</span></span>

<span data-ttu-id="ae93f-655">Las aplicaciones que simplemente esperen las API anteriores solo necesitan recompilarse, sin que sea necesario realizar cambios en el código fuente.</span><span class="sxs-lookup"><span data-stu-id="ae93f-655">Applications simply awaiting the above APIs only need to be recompiled - no source changes are necessary.</span></span>
<span data-ttu-id="ae93f-656">Un uso más complejo (p. ej., pasar el valor `Task` devuelto a `Task.WhenAny()`) normalmente requiere que el valor `ValueTask<T>` devuelto se convierta en `Task<T>` mediante una llamada a `AsTask()` en él.</span><span class="sxs-lookup"><span data-stu-id="ae93f-656">A more complex usage (e.g. passing the returned `Task` to `Task.WhenAny()`) typically require that the returned `ValueTask<T>` be converted to a `Task<T>` by calling `AsTask()` on it.</span></span>
<span data-ttu-id="ae93f-657">Tenga en cuenta que esto niega la reducción de asignación que implica este cambio.</span><span class="sxs-lookup"><span data-stu-id="ae93f-657">Note that this negates the allocation reduction that this change brings.</span></span>

<a name="rtt"></a>

### <a name="the-relationaltypemapping-annotation-is-now-just-typemapping"></a><span data-ttu-id="ae93f-658">La anotación Relational:TypeMapping ahora es simplemente TypeMapping</span><span class="sxs-lookup"><span data-stu-id="ae93f-658">The Relational:TypeMapping annotation is now just TypeMapping</span></span>

[<span data-ttu-id="ae93f-659">Problema de seguimiento n.º 9913</span><span class="sxs-lookup"><span data-stu-id="ae93f-659">Tracking Issue #9913</span></span>](https://github.com/dotnet/efcore/issues/9913)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-660">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-660">Old behavior</span></span>

<span data-ttu-id="ae93f-661">El nombre de anotación para las anotaciones de asignación de tipos era "Relational:TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="ae93f-661">The annotation name for type mapping annotations was "Relational:TypeMapping".</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-662">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-662">New behavior</span></span>

<span data-ttu-id="ae93f-663">Ahora, el nombre de anotación para las anotaciones de asignación de tipos es "TypeMapping".</span><span class="sxs-lookup"><span data-stu-id="ae93f-663">The annotation name for type mapping annotations is now "TypeMapping".</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-664">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-664">Why</span></span>

<span data-ttu-id="ae93f-665">Ahora, las asignaciones de tipos se usan para algo más que solo para proveedores de bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="ae93f-665">Type mappings are now used for more than just relational database providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-666">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-666">Mitigations</span></span>

<span data-ttu-id="ae93f-667">Esto solo interrumpirá a las aplicaciones que acceden directamente a la asignación de tipos como una anotación, lo que no es habitual.</span><span class="sxs-lookup"><span data-stu-id="ae93f-667">This will only break applications that access the type mapping directly as an annotation, which isn't common.</span></span>
<span data-ttu-id="ae93f-668">La acción más apropiada para corregir es usar la superficie de API para acceder a las asignaciones de tipos en lugar de usar directamente la anotación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-668">The most appropriate action to fix is to use API surface to access type mappings rather than using the annotation directly.</span></span>

### <a name="totable-on-a-derived-type-throws-an-exception"></a><span data-ttu-id="ae93f-669">ToTable en un tipo derivado inicia una excepción</span><span class="sxs-lookup"><span data-stu-id="ae93f-669">ToTable on a derived type throws an exception</span></span>

[<span data-ttu-id="ae93f-670">Problema de seguimiento n.º 11811</span><span class="sxs-lookup"><span data-stu-id="ae93f-670">Tracking Issue #11811</span></span>](https://github.com/dotnet/efcore/issues/11811)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-671">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-671">Old behavior</span></span>

<span data-ttu-id="ae93f-672">Antes de EF Core 3.0, la llamada a `ToTable()` en un tipo derivado se omitía, ya que la única estrategia asignación de herencia era TPH, lo que no es válido.</span><span class="sxs-lookup"><span data-stu-id="ae93f-672">Before EF Core 3.0, `ToTable()` called on a derived type would be ignored since only inheritance mapping strategy was TPH where this isn't valid.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-673">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-673">New behavior</span></span>

<span data-ttu-id="ae93f-674">A partir de EF Core 3.0, y en preparación para agregar compatibilidad con TPT y TPC en una versión posterior, ahora la llamada a `ToTable()` en un tipo derivado iniciará una excepción para evitar un cambio de asignación inesperado en el futuro.</span><span class="sxs-lookup"><span data-stu-id="ae93f-674">Starting with EF Core 3.0 and in preparation for adding TPT and TPC support in a later release, `ToTable()` called on a derived type will now throw an exception to avoid an unexpected mapping change in the future.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-675">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-675">Why</span></span>

<span data-ttu-id="ae93f-676">En la actualidad no se considera válido asignar un tipo derivado a otra tabla.</span><span class="sxs-lookup"><span data-stu-id="ae93f-676">Currently it isn't valid to map a derived type to a different table.</span></span>
<span data-ttu-id="ae93f-677">Este cambio evita interrupciones en el futuro, cuando se convierta en una operación válida.</span><span class="sxs-lookup"><span data-stu-id="ae93f-677">This change avoids breaking in the future when it becomes a valid thing to do.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-678">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-678">Mitigations</span></span>

<span data-ttu-id="ae93f-679">Quite todos los intentos de asignar tipos derivados a otras tablas.</span><span class="sxs-lookup"><span data-stu-id="ae93f-679">Remove any attempts to map derived types to other tables.</span></span>

### <a name="forsqlserverhasindex-replaced-with-hasindex"></a><span data-ttu-id="ae93f-680">ForSqlServerHasIndex se ha reemplazado por HasIndex</span><span class="sxs-lookup"><span data-stu-id="ae93f-680">ForSqlServerHasIndex replaced with HasIndex</span></span>

[<span data-ttu-id="ae93f-681">Problema de seguimiento n.º 12366</span><span class="sxs-lookup"><span data-stu-id="ae93f-681">Tracking Issue #12366</span></span>](https://github.com/dotnet/efcore/issues/12366)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-682">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-682">Old behavior</span></span>

<span data-ttu-id="ae93f-683">Antes de EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` proporcionaba una manera de configurar las columnas que se usaban con `INCLUDE`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-683">Before EF Core 3.0, `ForSqlServerHasIndex().ForSqlServerInclude()` provided a way to configure columns used with `INCLUDE`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-684">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-684">New behavior</span></span>

<span data-ttu-id="ae93f-685">A partir de EF Core 3.0, ya se admite el uso de `Include` en un índice en el nivel relacional.</span><span class="sxs-lookup"><span data-stu-id="ae93f-685">Starting with EF Core 3.0, using `Include` on an index is now supported at the relational level.</span></span>
<span data-ttu-id="ae93f-686">Use `HasIndex().ForSqlServerInclude()`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-686">Use `HasIndex().ForSqlServerInclude()`.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-687">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-687">Why</span></span>

<span data-ttu-id="ae93f-688">Este cambio se ha realizado para consolidar la API para índices con `Include` en un mismo lugar para todos los proveedores de base de datos.</span><span class="sxs-lookup"><span data-stu-id="ae93f-688">This change was made to consolidate the API for indexes with `Include` into one place for all database providers.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-689">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-689">Mitigations</span></span>

<span data-ttu-id="ae93f-690">Use la API nueva, como se ha mostrado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ae93f-690">Use the new API, as shown above.</span></span>

### <a name="metadata-api-changes"></a><span data-ttu-id="ae93f-691">Cambios en la API de metadatos</span><span class="sxs-lookup"><span data-stu-id="ae93f-691">Metadata API changes</span></span>

[<span data-ttu-id="ae93f-692">Problema de seguimiento n.º 214</span><span class="sxs-lookup"><span data-stu-id="ae93f-692">Tracking Issue #214</span></span>](https://github.com/dotnet/efcore/issues/214)

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-693">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-693">New behavior</span></span>

<span data-ttu-id="ae93f-694">Las siguientes propiedades se han convertido en métodos de extensión:</span><span class="sxs-lookup"><span data-stu-id="ae93f-694">The following properties were converted to extension methods:</span></span>

* `IEntityType.QueryFilter` -> `GetQueryFilter()`
* `IEntityType.DefiningQuery` -> `GetDefiningQuery()`
* `IProperty.IsShadowProperty` -> `IsShadowProperty()`
* `IProperty.BeforeSaveBehavior` -> `GetBeforeSaveBehavior()`
* `IProperty.AfterSaveBehavior` -> `GetAfterSaveBehavior()`

#### <a name="why"></a><span data-ttu-id="ae93f-695">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-695">Why</span></span>

<span data-ttu-id="ae93f-696">Este cambio simplifica la implementación de las interfaces mencionadas anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ae93f-696">This change simplifies the implementation of the aforementioned interfaces.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-697">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-697">Mitigations</span></span>

<span data-ttu-id="ae93f-698">Use los nuevos métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="ae93f-698">Use the new extension methods.</span></span>

<a name="provider"></a>

### <a name="provider-specific-metadata-api-changes"></a><span data-ttu-id="ae93f-699">Cambios en la API de metadatos específicos del proveedor</span><span class="sxs-lookup"><span data-stu-id="ae93f-699">Provider-specific Metadata API changes</span></span>

[<span data-ttu-id="ae93f-700">Problema de seguimiento n.º 214</span><span class="sxs-lookup"><span data-stu-id="ae93f-700">Tracking Issue #214</span></span>](https://github.com/dotnet/efcore/issues/214)

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-701">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-701">New behavior</span></span>

<span data-ttu-id="ae93f-702">Los métodos de extensión específicos del proveedor se simplificarán:</span><span class="sxs-lookup"><span data-stu-id="ae93f-702">The provider-specific extension methods will be flattened out:</span></span>

* `IProperty.Relational().ColumnName` -> `IProperty.GetColumnName()`
* `IEntityType.SqlServer().IsMemoryOptimized` -> `IEntityType.IsMemoryOptimized()`
* `PropertyBuilder.UseSqlServerIdentityColumn()` -> `PropertyBuilder.UseIdentityColumn()`

#### <a name="why"></a><span data-ttu-id="ae93f-703">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-703">Why</span></span>

<span data-ttu-id="ae93f-704">Este cambio simplifica la implementación de los métodos de extensión mencionados anteriormente.</span><span class="sxs-lookup"><span data-stu-id="ae93f-704">This change simplifies the implementation of the aforementioned extension methods.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-705">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-705">Mitigations</span></span>

<span data-ttu-id="ae93f-706">Use los nuevos métodos de extensión.</span><span class="sxs-lookup"><span data-stu-id="ae93f-706">Use the new extension methods.</span></span>

<a name="pragma"></a>

### <a name="ef-core-no-longer-sends-pragma-for-sqlite-fk-enforcement"></a><span data-ttu-id="ae93f-707">EF Core ya no envía pragma para el cumplimiento de SQLite FK</span><span class="sxs-lookup"><span data-stu-id="ae93f-707">EF Core no longer sends pragma for SQLite FK enforcement</span></span>

[<span data-ttu-id="ae93f-708">Problema de seguimiento n.º 12151</span><span class="sxs-lookup"><span data-stu-id="ae93f-708">Tracking Issue #12151</span></span>](https://github.com/dotnet/efcore/issues/12151)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-709">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-709">Old behavior</span></span>

<span data-ttu-id="ae93f-710">Antes de EF Core 3.0, EF Core enviaba `PRAGMA foreign_keys = 1` cuando se abría una conexión con SQLite.</span><span class="sxs-lookup"><span data-stu-id="ae93f-710">Before EF Core 3.0, EF Core would send `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-711">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-711">New behavior</span></span>

<span data-ttu-id="ae93f-712">A partir de EF Core 3.0, EF Core ya no envía `PRAGMA foreign_keys = 1` cuando se abre una conexión con SQLite.</span><span class="sxs-lookup"><span data-stu-id="ae93f-712">Starting with EF Core 3.0, EF Core no longer sends `PRAGMA foreign_keys = 1` when a connection to SQLite is opened.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-713">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-713">Why</span></span>

<span data-ttu-id="ae93f-714">Este cambio se ha realizado porque en EF Core se usa `SQLitePCLRaw.bundle_e_sqlite3` de forma predeterminada, lo que a su vez significa que el cumplimiento de CD está activado de forma predeterminada y no es necesario habilitarlo explícitamente cada vez que se abra una conexión.</span><span class="sxs-lookup"><span data-stu-id="ae93f-714">This change was made because EF Core uses `SQLitePCLRaw.bundle_e_sqlite3` by default, which in turn means that FK enforcement is switched on by default and doesn't need to be explicitly enabled each time a connection is opened.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-715">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-715">Mitigations</span></span>

<span data-ttu-id="ae93f-716">Las claves externas se habilitan de forma predeterminada en SQLitePCLRaw.bundle_e_sqlite3, que en EF Core se usa de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="ae93f-716">Foreign keys are enabled by default in SQLitePCLRaw.bundle_e_sqlite3, which is used by default for EF Core.</span></span>
<span data-ttu-id="ae93f-717">Para otros casos, las claves externas se pueden habilitar mediante la especificación de `Foreign Keys=True` en la cadena de conexión.</span><span class="sxs-lookup"><span data-stu-id="ae93f-717">For other cases, foreign keys can be enabled by specifying `Foreign Keys=True` in your connection string.</span></span>

<a name="sqlite3"></a>

### <a name="microsoftentityframeworkcoresqlite-now-depends-on-sqlitepclrawbundle_e_sqlite3"></a><span data-ttu-id="ae93f-718">Microsoft.EntityFrameworkCore.Sqlite ahora depende de SQLitePCLRaw.bundle_e_sqlite3</span><span class="sxs-lookup"><span data-stu-id="ae93f-718">Microsoft.EntityFrameworkCore.Sqlite now depends on SQLitePCLRaw.bundle_e_sqlite3</span></span>

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-719">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-719">Old behavior</span></span>

<span data-ttu-id="ae93f-720">Antes de EF Core 3.0, en EF Core se usaba `SQLitePCLRaw.bundle_green`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-720">Before EF Core 3.0, EF Core used `SQLitePCLRaw.bundle_green`.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-721">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-721">New behavior</span></span>

<span data-ttu-id="ae93f-722">A partir de EF Core 3.0, en EF Core se usa `SQLitePCLRaw.bundle_e_sqlite3`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-722">Starting with EF Core 3.0, EF Core uses `SQLitePCLRaw.bundle_e_sqlite3`.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-723">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-723">Why</span></span>

<span data-ttu-id="ae93f-724">Este cambio se ha realizado para que la versión de SQLite que se usa en iOS sea coherente con otras plataformas.</span><span class="sxs-lookup"><span data-stu-id="ae93f-724">This change was made so that the version of SQLite used on iOS consistent with other platforms.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-725">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-725">Mitigations</span></span>

<span data-ttu-id="ae93f-726">Para usar la versión nativa de SQLite en iOS, configure `Microsoft.Data.Sqlite` para usar otra agrupación `SQLitePCLRaw`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-726">To use the native SQLite version on iOS, configure `Microsoft.Data.Sqlite` to use a different `SQLitePCLRaw` bundle.</span></span>

<a name="guid"></a>

### <a name="guid-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="ae93f-727">Almacenamiento de valores GUID como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="ae93f-727">Guid values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="ae93f-728">Problema de seguimiento n.º 15078</span><span class="sxs-lookup"><span data-stu-id="ae93f-728">Tracking Issue #15078</span></span>](https://github.com/dotnet/efcore/issues/15078)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-729">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-729">Old behavior</span></span>

<span data-ttu-id="ae93f-730">Antes, los valores GUID se almacenaban como valores BLOB en SQLite.</span><span class="sxs-lookup"><span data-stu-id="ae93f-730">Guid values were previously stored as BLOB values on SQLite.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-731">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-731">New behavior</span></span>

<span data-ttu-id="ae93f-732">Ahora, los valores GUID se almacenan como TEXT.</span><span class="sxs-lookup"><span data-stu-id="ae93f-732">Guid values are now stored as TEXT.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-733">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-733">Why</span></span>

<span data-ttu-id="ae93f-734">El formato binario de los GUID no está normalizado.</span><span class="sxs-lookup"><span data-stu-id="ae93f-734">The binary format of Guids is not standardized.</span></span> <span data-ttu-id="ae93f-735">El almacenamiento de los valores como TEXT mejora la compatibilidad de la base de datos con otras tecnologías.</span><span class="sxs-lookup"><span data-stu-id="ae93f-735">Storing the values as TEXT makes the database more compatible with other technologies.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-736">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-736">Mitigations</span></span>

<span data-ttu-id="ae93f-737">Puede migrar las bases de datos existentes al nuevo formato ejecutando SQL de la siguiente forma.</span><span class="sxs-lookup"><span data-stu-id="ae93f-737">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

```sql
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

<span data-ttu-id="ae93f-738">En EF Core, también puede seguir usando el comportamiento anterior configurando un convertidor de valores en estas propiedades.</span><span class="sxs-lookup"><span data-stu-id="ae93f-738">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.GuidProperty)
    .HasConversion(
        g => g.ToByteArray(),
        b => new Guid(b));
```

<span data-ttu-id="ae93f-739">Microsoft.Data.Sqlite sigue siendo capaz de leer valores GUID de ambas columnas BLOB y TEXT. Sin embargo, dado que el formato predeterminado de los parámetros y las constantes ha cambiado, seguramente deberá realizar alguna acción en la mayoría de casos que impliquen el uso de valores GUID.</span><span class="sxs-lookup"><span data-stu-id="ae93f-739">Microsoft.Data.Sqlite remains capable of reading Guid values from both BLOB and TEXT columns; however, since the default format for parameters and constants has changed you'll likely need to take action for most scenarios involving Guids.</span></span>

<a name="char"></a>

### <a name="char-values-are-now-stored-as-text-on-sqlite"></a><span data-ttu-id="ae93f-740">Ahora los valores char se almacenan como TEXT en SQLite</span><span class="sxs-lookup"><span data-stu-id="ae93f-740">Char values are now stored as TEXT on SQLite</span></span>

[<span data-ttu-id="ae93f-741">Problema de seguimiento n.º 15020</span><span class="sxs-lookup"><span data-stu-id="ae93f-741">Tracking Issue #15020</span></span>](https://github.com/dotnet/efcore/issues/15020)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-742">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-742">Old behavior</span></span>

<span data-ttu-id="ae93f-743">Anteriormente los valores char se almacenaban como valores INTEGER en SQLite.</span><span class="sxs-lookup"><span data-stu-id="ae93f-743">Char values were previously sored as INTEGER values on SQLite.</span></span> <span data-ttu-id="ae93f-744">Por ejemplo, un valor char de *A* se almacenaba como el valor entero 65.</span><span class="sxs-lookup"><span data-stu-id="ae93f-744">For example, a char value of *A* was stored as the integer value 65.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-745">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-745">New behavior</span></span>

<span data-ttu-id="ae93f-746">Ahora, los valores char se almacenan como TEXT.</span><span class="sxs-lookup"><span data-stu-id="ae93f-746">Char values are now stored as TEXT.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-747">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-747">Why</span></span>

<span data-ttu-id="ae93f-748">El almacenamiento de valores como TEXT es más natural y mejora la compatibilidad de la base de datos con otras tecnologías.</span><span class="sxs-lookup"><span data-stu-id="ae93f-748">Storing the values as TEXT is more natural and makes the database more compatible with other technologies.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-749">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-749">Mitigations</span></span>

<span data-ttu-id="ae93f-750">Puede migrar las bases de datos existentes al nuevo formato ejecutando SQL de la siguiente forma.</span><span class="sxs-lookup"><span data-stu-id="ae93f-750">You can migrate existing databases to the new format by executing SQL like the following.</span></span>

```sql
UPDATE MyTable
SET CharColumn = char(CharColumn)
WHERE typeof(CharColumn) = 'integer';
```

<span data-ttu-id="ae93f-751">En EF Core, también puede seguir usando el comportamiento anterior configurando un convertidor de valores en estas propiedades.</span><span class="sxs-lookup"><span data-stu-id="ae93f-751">In EF Core, you could also continue using the previous behavior by configuring a value converter on these properties.</span></span>

```csharp
modelBuilder
    .Entity<MyEntity>()
    .Property(e => e.CharProperty)
    .HasConversion(
        c => (long)c,
        i => (char)i);
```

<span data-ttu-id="ae93f-752">Microsoft.Data.Sqlite también puede leer valores de caracteres tanto de columnas INTEGER como de columnas TEXT, por lo que es posible que no deba hacer nada dependiendo de su caso.</span><span class="sxs-lookup"><span data-stu-id="ae93f-752">Microsoft.Data.Sqlite also remains capable of reading character values from both INTEGER and TEXT columns, so certain scenarios may not require any action.</span></span>

<a name="migid"></a>

### <a name="migration-ids-are-now-generated-using-the-invariant-cultures-calendar"></a><span data-ttu-id="ae93f-753">Ahora los id. de migración se generan usando el calendario de la referencia cultural invariable</span><span class="sxs-lookup"><span data-stu-id="ae93f-753">Migration IDs are now generated using the invariant culture's calendar</span></span>

[<span data-ttu-id="ae93f-754">Problema de seguimiento n.º 12978</span><span class="sxs-lookup"><span data-stu-id="ae93f-754">Tracking Issue #12978</span></span>](https://github.com/dotnet/efcore/issues/12978)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-755">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-755">Old behavior</span></span>

<span data-ttu-id="ae93f-756">Los identificadores de migración se generaban de forma involuntaria con el calendario de la referencia cultural actual.</span><span class="sxs-lookup"><span data-stu-id="ae93f-756">Migration IDs were inadvertently generated using the current culture's calendar.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-757">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-757">New behavior</span></span>

<span data-ttu-id="ae93f-758">Ahora los id. de migración siempre se generan usando el calendario de la referencia cultural invariable (gregoriano).</span><span class="sxs-lookup"><span data-stu-id="ae93f-758">Migration IDs are now always generated using the invariant culture's calendar (Gregorian).</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-759">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-759">Why</span></span>

<span data-ttu-id="ae93f-760">El orden de las migraciones es importante al actualizar la base de datos o al solucionar conflictos de combinación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-760">The order of migrations is important when updating the database or resolving merge conflicts.</span></span> <span data-ttu-id="ae93f-761">Al usar el calendario invariable, se evitan problemas de ordenación que pueden producirse si los miembros del equipo tienen distintos calendarios del sistema.</span><span class="sxs-lookup"><span data-stu-id="ae93f-761">Using the invariant calendar avoids ordering issues that can result from team members having different system calendars.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-762">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-762">Mitigations</span></span>

<span data-ttu-id="ae93f-763">Esta cambio afecta a todas las personas que usan un calendario no gregoriano en el que el año sea superior al del calendario gregoriano (como el calendario budista tailandés).</span><span class="sxs-lookup"><span data-stu-id="ae93f-763">This change affects anyone using a non-Gregorian calendar where the year is greater than the Gregorian calendar (like the Thai Buddhist calendar).</span></span> <span data-ttu-id="ae93f-764">Los id. de migración existentes deberán actualizarse para que las migraciones nuevas se ordenen después de las existentes.</span><span class="sxs-lookup"><span data-stu-id="ae93f-764">Existing migration IDs will need to be updated so that new migrations are ordered after existing migrations.</span></span>

<span data-ttu-id="ae93f-765">Puede ver el id. de migración en el atributo Migration de los archivos de diseñador de la migración.</span><span class="sxs-lookup"><span data-stu-id="ae93f-765">The migration ID can be found in the Migration attribute in the migrations' designer files.</span></span>

```diff
 [DbContext(typeof(MyDbContext))]
-[Migration("25620318122820_MyMigration")]
+[Migration("20190318122820_MyMigration")]
 partial class MyMigration
 {
```

<span data-ttu-id="ae93f-766">También debe actualizarse la tabla de historial de migraciones.</span><span class="sxs-lookup"><span data-stu-id="ae93f-766">The Migrations history table also needs to be updated.</span></span>

```sql
UPDATE __EFMigrationsHistory
SET MigrationId = CONCAT(LEFT(MigrationId, 4)  - 543, SUBSTRING(MigrationId, 4, 150))
```

<a name="urn"></a>

### <a name="userownumberforpaging-has-been-removed"></a><span data-ttu-id="ae93f-767">Se ha quitado el elemento UseRowNumberForPaging</span><span class="sxs-lookup"><span data-stu-id="ae93f-767">UseRowNumberForPaging has been removed</span></span>

[<span data-ttu-id="ae93f-768">Problema de seguimiento n.º 16400</span><span class="sxs-lookup"><span data-stu-id="ae93f-768">Tracking Issue #16400</span></span>](https://github.com/dotnet/efcore/issues/16400)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-769">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-769">Old behavior</span></span>

<span data-ttu-id="ae93f-770">Antes de EF Core 3.0, `UseRowNumberForPaging` se podía usar para generar SQL para la paginación de forma que fuera compatible con SQL Server 2008.</span><span class="sxs-lookup"><span data-stu-id="ae93f-770">Before EF Core 3.0, `UseRowNumberForPaging` could be used to generate SQL for paging that is compatible with SQL Server 2008.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-771">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-771">New behavior</span></span>

<span data-ttu-id="ae93f-772">A partir de EF Core 3.0, EF solo genera SQL para la paginación que únicamente es compatible con las versiones posteriores de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="ae93f-772">Starting with EF Core 3.0, EF will only generate SQL for paging that is only compatible with later SQL Server versions.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-773">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-773">Why</span></span>

<span data-ttu-id="ae93f-774">El motivo de este cambio es que [SQL Server 2008 ya no se admite](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/). Además, la actualización de esta característica para que funcionase con los cambios en las consultas implementados en EF Core 3.0 llevaría mucho trabajo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-774">We are making this change because [SQL Server 2008 is no longer a supported product](https://blogs.msdn.microsoft.com/sqlreleaseservices/end-of-mainstream-support-for-sql-server-2008-and-sql-server-2008-r2/) and updating this feature to work with the query changes made in EF Core 3.0 is significant work.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-775">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-775">Mitigations</span></span>

<span data-ttu-id="ae93f-776">Se recomienda actualizar a una versión más reciente de SQL Server, o bien utilizar un nivel de compatibilidad superior, de modo que el SQL que se genere se admita.</span><span class="sxs-lookup"><span data-stu-id="ae93f-776">We recommend updating to a newer version of SQL Server, or using a higher compatibility level, so that the generated SQL is supported.</span></span> <span data-ttu-id="ae93f-777">Dicho esto, si no puede hacerlo, [escriba un comentario en el problema de seguimiento](https://github.com/dotnet/efcore/issues/16400) con los detalles al respecto.</span><span class="sxs-lookup"><span data-stu-id="ae93f-777">That being said, if you are unable to do this, then please [comment on the tracking issue](https://github.com/dotnet/efcore/issues/16400) with details.</span></span> <span data-ttu-id="ae93f-778">En función de los comentarios, es posible que volvamos a valorar esta decisión.</span><span class="sxs-lookup"><span data-stu-id="ae93f-778">We may revisit this decision based on feedback.</span></span>

<a name="xinfo"></a>

### <a name="extension-infometadata-has-been-removed-from-idbcontextoptionsextension"></a><span data-ttu-id="ae93f-779">La información o metadatos de la extensión se han quitado de IDbContextOptionsExtension</span><span class="sxs-lookup"><span data-stu-id="ae93f-779">Extension info/metadata has been removed from IDbContextOptionsExtension</span></span>

[<span data-ttu-id="ae93f-780">Problema de seguimiento n.º 16119</span><span class="sxs-lookup"><span data-stu-id="ae93f-780">Tracking Issue #16119</span></span>](https://github.com/dotnet/efcore/issues/16119)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-781">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-781">Old behavior</span></span>

<span data-ttu-id="ae93f-782">`IDbContextOptionsExtension` incluía métodos para proporcionar metadatos sobre la extensión.</span><span class="sxs-lookup"><span data-stu-id="ae93f-782">`IDbContextOptionsExtension` contained methods for providing metadata about the extension.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-783">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-783">New behavior</span></span>

<span data-ttu-id="ae93f-784">Estos métodos se han movido a una nueva clase base abstracta `DbContextOptionsExtensionInfo`, que se devuelve desde una nueva propiedad `IDbContextOptionsExtension.Info`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-784">These methods have been moved onto a new `DbContextOptionsExtensionInfo` abstract base class, which is returned from a new `IDbContextOptionsExtension.Info` property.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-785">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-785">Why</span></span>

<span data-ttu-id="ae93f-786">Al lanzarse las versiones 2.0 y 3.0, tuvimos que agregar o cambiar estos métodos varias veces.</span><span class="sxs-lookup"><span data-stu-id="ae93f-786">Over the releases from 2.0 to 3.0 we needed to add to or change these methods several times.</span></span>
<span data-ttu-id="ae93f-787">Su división en una nueva clase base abstracta facilitará la realización de este tipo de cambios sin interrumpir las extensiones existentes.</span><span class="sxs-lookup"><span data-stu-id="ae93f-787">Breaking them out into a new abstract base class will make it easier to make these kind of changes without breaking existing extensions.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-788">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-788">Mitigations</span></span>

<span data-ttu-id="ae93f-789">Actualice las extensiones para seguir el nuevo patrón.</span><span class="sxs-lookup"><span data-stu-id="ae93f-789">Update extensions to follow the new pattern.</span></span>
<span data-ttu-id="ae93f-790">Encontrará ejemplos en las muchas implementaciones de `IDbContextOptionsExtension` para los diferentes tipos de extensiones en el código fuente de EF Core.</span><span class="sxs-lookup"><span data-stu-id="ae93f-790">Examples are found in the many implementations of `IDbContextOptionsExtension` for different kinds of extensions in the EF Core source code.</span></span>

<a name="lqpe"></a>

### <a name="logquerypossibleexceptionwithaggregateoperator-has-been-renamed"></a><span data-ttu-id="ae93f-791">Cambio de nombre de LogQueryPossibleExceptionWithAggregateOperator</span><span class="sxs-lookup"><span data-stu-id="ae93f-791">LogQueryPossibleExceptionWithAggregateOperator has been renamed</span></span>

[<span data-ttu-id="ae93f-792">Problema de seguimiento n.º 10985</span><span class="sxs-lookup"><span data-stu-id="ae93f-792">Tracking Issue #10985</span></span>](https://github.com/dotnet/efcore/issues/10985)

#### <a name="change"></a><span data-ttu-id="ae93f-793">Change</span><span class="sxs-lookup"><span data-stu-id="ae93f-793">Change</span></span>

<span data-ttu-id="ae93f-794">Se ha cambiado el nombre de `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` a `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span><span class="sxs-lookup"><span data-stu-id="ae93f-794">`RelationalEventId.LogQueryPossibleExceptionWithAggregateOperator` has been renamed to `RelationalEventId.LogQueryPossibleExceptionWithAggregateOperatorWarning`.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-795">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-795">Why</span></span>

<span data-ttu-id="ae93f-796">Conviene alinear el nombre de este evento de advertencia con el del resto de eventos de advertencia.</span><span class="sxs-lookup"><span data-stu-id="ae93f-796">Aligns the naming of this warning event with all other warning events.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-797">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-797">Mitigations</span></span>

<span data-ttu-id="ae93f-798">Use el nuevo nombre.</span><span class="sxs-lookup"><span data-stu-id="ae93f-798">Use the new name.</span></span> <span data-ttu-id="ae93f-799">(Tenga en cuenta que el número de id. evento sigue siendo el mismo).</span><span class="sxs-lookup"><span data-stu-id="ae93f-799">(Note that the event ID number has not changed.)</span></span>

<a name="clarify"></a>

### <a name="clarify-api-for-foreign-key-constraint-names"></a><span data-ttu-id="ae93f-800">Clarificación de la API para nombres de restricciones de claves externas</span><span class="sxs-lookup"><span data-stu-id="ae93f-800">Clarify API for foreign key constraint names</span></span>

[<span data-ttu-id="ae93f-801">Problema de seguimiento n.º 10730</span><span class="sxs-lookup"><span data-stu-id="ae93f-801">Tracking Issue #10730</span></span>](https://github.com/dotnet/efcore/issues/10730)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-802">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-802">Old behavior</span></span>

<span data-ttu-id="ae93f-803">Antes de EF Core 3.0, se utilizaba simplemente el término "nombre" para hacer referencia a los nombres de las restricciones de claves externas.</span><span class="sxs-lookup"><span data-stu-id="ae93f-803">Before EF Core 3.0, foreign key constraint names were referred to as simply the "name".</span></span> <span data-ttu-id="ae93f-804">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-804">For example:</span></span>

```csharp
var constraintName = myForeignKey.Name;
```

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-805">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-805">New behavior</span></span>

<span data-ttu-id="ae93f-806">A partir de EF Core 3.0, el término con el que se hace referencia a los nombres de las restricciones de claves externas es "nombre de la restricción".</span><span class="sxs-lookup"><span data-stu-id="ae93f-806">Starting with EF Core 3.0, foreign key constraint names are now referred to as the "constraint name".</span></span> <span data-ttu-id="ae93f-807">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-807">For example:</span></span>

```csharp
var constraintName = myForeignKey.ConstraintName;
```

#### <a name="why"></a><span data-ttu-id="ae93f-808">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-808">Why</span></span>

<span data-ttu-id="ae93f-809">Este cambio permite mejorar la coherencia relativa a la nomenclatura en este aspecto y aclarar que se trata del nombre de una restricción de clave externa, y no del de la columna o propiedad en la que está definida la clave externa.</span><span class="sxs-lookup"><span data-stu-id="ae93f-809">This change brings consistency to naming in this area, and also clarifies that this is the name of the foreign key constraint, and not the column or property name that the foreign key is defined on.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-810">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-810">Mitigations</span></span>

<span data-ttu-id="ae93f-811">Use el nuevo nombre.</span><span class="sxs-lookup"><span data-stu-id="ae93f-811">Use the new name.</span></span>

<a name="irdc2"></a>

### <a name="irelationaldatabasecreatorhastableshastablesasync-have-been-made-public"></a><span data-ttu-id="ae93f-812">IRelationalDatabaseCreator.HasTables/HasTablesAsync se han hecho públicos</span><span class="sxs-lookup"><span data-stu-id="ae93f-812">IRelationalDatabaseCreator.HasTables/HasTablesAsync have been made public</span></span>

[<span data-ttu-id="ae93f-813">Problema de seguimiento n.° 15997</span><span class="sxs-lookup"><span data-stu-id="ae93f-813">Tracking Issue #15997</span></span>](https://github.com/dotnet/efcore/issues/15997)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-814">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-814">Old behavior</span></span>

<span data-ttu-id="ae93f-815">Antes de EF Core 3.0, estos métodos estaban protegidos.</span><span class="sxs-lookup"><span data-stu-id="ae93f-815">Before EF Core 3.0, these methods were protected.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-816">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-816">New behavior</span></span>

<span data-ttu-id="ae93f-817">Desde EF Core 3.0, estos métodos son públicos.</span><span class="sxs-lookup"><span data-stu-id="ae93f-817">Starting with EF Core 3.0, these methods are public.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-818">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-818">Why</span></span>

<span data-ttu-id="ae93f-819">EF usa estos métodos para determinar si se ha creado una base de datos, pero está vacía.</span><span class="sxs-lookup"><span data-stu-id="ae93f-819">These methods are used by EF to determine if a database is created but empty.</span></span> <span data-ttu-id="ae93f-820">Esto también puede resultar útil fuera de EF al determinar si se deben aplicar migraciones o no.</span><span class="sxs-lookup"><span data-stu-id="ae93f-820">This can also be useful from outside EF when determining whether or not to apply migrations.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-821">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-821">Mitigations</span></span>

<span data-ttu-id="ae93f-822">Cambie la accesibilidad de cualquier invalidación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-822">Change the accessibility of any overrides.</span></span>

<a name="dip"></a>

### <a name="microsoftentityframeworkcoredesign-is-now-a-developmentdependency-package"></a><span data-ttu-id="ae93f-823">Microsoft.EntityFrameworkCore.Design es ahora un paquete DevelopmentDependency</span><span class="sxs-lookup"><span data-stu-id="ae93f-823">Microsoft.EntityFrameworkCore.Design is now a DevelopmentDependency package</span></span>

[<span data-ttu-id="ae93f-824">Problema de seguimiento n.° 11506</span><span class="sxs-lookup"><span data-stu-id="ae93f-824">Tracking Issue #11506</span></span>](https://github.com/dotnet/efcore/issues/11506)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-825">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-825">Old behavior</span></span>

<span data-ttu-id="ae93f-826">Antes de EF Core 3.0, Microsoft.EntityFrameworkCore.Design era un paquete NuGet regular con un ensamblado al que podían hacer referencia los proyectos que dependían de él.</span><span class="sxs-lookup"><span data-stu-id="ae93f-826">Before EF Core 3.0, Microsoft.EntityFrameworkCore.Design was a regular NuGet package whose assembly could be referenced by projects that depended on it.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-827">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-827">New behavior</span></span>

<span data-ttu-id="ae93f-828">Desde EF Core 3.0, es un paquete DevelopmentDependency.</span><span class="sxs-lookup"><span data-stu-id="ae93f-828">Starting with EF Core 3.0, it is a DevelopmentDependency package.</span></span> <span data-ttu-id="ae93f-829">Esto significa que la dependencia no fluirá de manera transitiva en otros proyectos y que ya no puede, de forma predeterminada, hacer referencia a su ensamblado.</span><span class="sxs-lookup"><span data-stu-id="ae93f-829">This means that the dependency won't flow transitively into other projects, and that you can no longer, by default, reference its assembly.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-830">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-830">Why</span></span>

<span data-ttu-id="ae93f-831">Este paquete solo está destinado a usarse en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="ae93f-831">This package is only intended to be used at design time.</span></span> <span data-ttu-id="ae93f-832">Las aplicaciones implementadas no deben hacer referencia al mismo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-832">Deployed applications shouldn't reference it.</span></span> <span data-ttu-id="ae93f-833">Hacer que el paquete sea DevelopmentDependency refuerza esta recomendación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-833">Making the package a DevelopmentDependency reinforces this recommendation.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-834">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-834">Mitigations</span></span>

<span data-ttu-id="ae93f-835">Si tiene que hacer referencia a este paquete para invalidar el comportamiento en tiempo de diseño de EF Core, puede actualizar los metadatos de elementos PackageReference del proyecto.</span><span class="sxs-lookup"><span data-stu-id="ae93f-835">If you need to reference this package to override EF Core's design-time behavior, then you can update PackageReference item metadata in your project.</span></span>

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.0.0">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

<span data-ttu-id="ae93f-836">Si se hace referencia al paquete de manera transitiva a través de Microsoft.EntityFrameworkCore.Tools, tendrá que agregar una PackageReference explícita al paquete para cambiar sus metadatos.</span><span class="sxs-lookup"><span data-stu-id="ae93f-836">If the package is being referenced transitively via Microsoft.EntityFrameworkCore.Tools, you will need to add an explicit PackageReference to the package to change its metadata.</span></span> <span data-ttu-id="ae93f-837">Este tipo de referencia explícita debe agregarse a cualquier proyecto que requiera los tipos de paquete.</span><span class="sxs-lookup"><span data-stu-id="ae93f-837">Such an explicit reference must be added to any project where the types from the package are needed.</span></span>

<a name="SQLitePCL"></a>

### <a name="sqlitepclraw-updated-to-version-200"></a><span data-ttu-id="ae93f-838">SQLitePCL.raw se ha actualizado a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="ae93f-838">SQLitePCL.raw updated to version 2.0.0</span></span>

[<span data-ttu-id="ae93f-839">Problema de seguimiento n.° 14824</span><span class="sxs-lookup"><span data-stu-id="ae93f-839">Tracking Issue #14824</span></span>](https://github.com/dotnet/efcore/issues/14824)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-840">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-840">Old behavior</span></span>

<span data-ttu-id="ae93f-841">Microsoft.EntityFrameworkCore.Sqlite dependía anteriormente de la versión 1.1.12 de SQLitePCL.raw.</span><span class="sxs-lookup"><span data-stu-id="ae93f-841">Microsoft.EntityFrameworkCore.Sqlite previously depended on version 1.1.12 of SQLitePCL.raw.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-842">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-842">New behavior</span></span>

<span data-ttu-id="ae93f-843">Hemos actualizado nuestro paquete para depender de la versión 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="ae93f-843">We've updated our package to depend on version 2.0.0.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-844">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-844">Why</span></span>

<span data-ttu-id="ae93f-845">La versión 2.0.0 de SQLitePCL.raw selecciona .NET Standard 2.0 como destino.</span><span class="sxs-lookup"><span data-stu-id="ae93f-845">Version 2.0.0 of SQLitePCL.raw targets .NET Standard 2.0.</span></span> <span data-ttu-id="ae93f-846">Anteriormente seleccionaba .NET Standard 1.1 como destino, que requería el cierre a gran escala de paquetes transitivos para su funcionamiento.</span><span class="sxs-lookup"><span data-stu-id="ae93f-846">It previously targeted .NET Standard 1.1 which required a large closure of transitive packages to work.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-847">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-847">Mitigations</span></span>

<span data-ttu-id="ae93f-848">En la versión 2.0.0 de SQLitePCL.raw se incluyen algunos cambios importantes.</span><span class="sxs-lookup"><span data-stu-id="ae93f-848">SQLitePCL.raw version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="ae93f-849">Consulte las [notas de la versión](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) para obtener detalles.</span><span class="sxs-lookup"><span data-stu-id="ae93f-849">See the [release notes](https://github.com/ericsink/SQLitePCL.raw/blob/v2/v2.md) for details.</span></span>

<a name="NetTopologySuite"></a>

### <a name="nettopologysuite-updated-to-version-200"></a><span data-ttu-id="ae93f-850">NetTopologySuite se actualizó a la versión 2.0.0</span><span class="sxs-lookup"><span data-stu-id="ae93f-850">NetTopologySuite updated to version 2.0.0</span></span>

[<span data-ttu-id="ae93f-851">Problema de seguimiento n.° 14825</span><span class="sxs-lookup"><span data-stu-id="ae93f-851">Tracking Issue #14825</span></span>](https://github.com/dotnet/efcore/issues/14825)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-852">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-852">Old behavior</span></span>

<span data-ttu-id="ae93f-853">Los paquetes espaciales anteriormente dependían de la versión 1.15.1 de NetTopologySuite.</span><span class="sxs-lookup"><span data-stu-id="ae93f-853">The spatial packages previously depended on version 1.15.1 of NetTopologySuite.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-854">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-854">New behavior</span></span>

<span data-ttu-id="ae93f-855">Hemos actualizado nuestro paquete para depender de la versión 2.0.0.</span><span class="sxs-lookup"><span data-stu-id="ae93f-855">We've update our package to depend on version 2.0.0.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-856">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-856">Why</span></span>

<span data-ttu-id="ae93f-857">La versión 2.0.0 de NetTopologySuite pretende resolver varios problemas de usabilidad que encontraron los usuarios de EF Core.</span><span class="sxs-lookup"><span data-stu-id="ae93f-857">Version 2.0.0 of NetTopologySuite aims to address several usability issues encountered by EF Core users.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-858">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-858">Mitigations</span></span>

<span data-ttu-id="ae93f-859">En la versión 2.0.0 de NetTopologySuite se incluyen algunos cambios importantes.</span><span class="sxs-lookup"><span data-stu-id="ae93f-859">NetTopologySuite version 2.0.0 includes some breaking changes.</span></span> <span data-ttu-id="ae93f-860">Consulte las [notas de la versión](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) para obtener detalles.</span><span class="sxs-lookup"><span data-stu-id="ae93f-860">See the [release notes](https://www.nuget.org/packages/NetTopologySuite/2.0.0-pre001) for details.</span></span>

<a name="SqlClient"></a>

### <a name="microsoftdatasqlclient-is-used-instead-of-systemdatasqlclient"></a><span data-ttu-id="ae93f-861">Se usa Microsoft.Data.SqlClient en lugar de System.Data.SqlClient</span><span class="sxs-lookup"><span data-stu-id="ae93f-861">Microsoft.Data.SqlClient is used instead of System.Data.SqlClient</span></span>

[<span data-ttu-id="ae93f-862">Problema de seguimiento n.º 15636</span><span class="sxs-lookup"><span data-stu-id="ae93f-862">Tracking Issue #15636</span></span>](https://github.com/dotnet/efcore/issues/15636)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-863">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-863">Old behavior</span></span>

<span data-ttu-id="ae93f-864">Microsoft.EntityFrameworkCore.SqlServer dependía anteriormente de la versión System.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="ae93f-864">Microsoft.EntityFrameworkCore.SqlServer previously depended on System.Data.SqlClient.</span></span>

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-865">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-865">New behavior</span></span>

<span data-ttu-id="ae93f-866">Hemos actualizado nuestro paquete para que dependa de Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="ae93f-866">We've updated our package to depend on Microsoft.Data.SqlClient.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-867">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-867">Why</span></span>

<span data-ttu-id="ae93f-868">A partir de ahora, Microsoft.Data.SqlClient es el controlador de acceso a datos insignia para SQL Server y System.Data.SqlClient ya no es el centro de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-868">Microsoft.Data.SqlClient is the flagship data access driver for SQL Server going forward, and System.Data.SqlClient no longer be the focus of development.</span></span>
<span data-ttu-id="ae93f-869">Algunas características importantes, como Always Encrypted, solo están disponibles en Microsoft.Data.SqlClient.</span><span class="sxs-lookup"><span data-stu-id="ae93f-869">Some important features, such as Always Encrypted, are only available on Microsoft.Data.SqlClient.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-870">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-870">Mitigations</span></span>

<span data-ttu-id="ae93f-871">Si el código toma una dependencia directa en System.Data.SqlClient, debe cambiarla para que haga referencia a Microsoft.Data.SqlClient en su lugar. Dado que los dos paquetes mantienen un grado muy alto de compatibilidad con la API, solo debería ser un paquete simple y un cambio de espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="ae93f-871">If your code takes a direct dependency on System.Data.SqlClient, you must change it to reference Microsoft.Data.SqlClient instead; as the two packages maintain a very high degree of API compatibility, this should only be a simple package and namespace change.</span></span>

<a name="mersa"></a>

### <a name="multiple-ambiguous-self-referencing-relationships-must-be-configured"></a><span data-ttu-id="ae93f-872">Se deben configurar varias relaciones de referencia automática ambiguas</span><span class="sxs-lookup"><span data-stu-id="ae93f-872">Multiple ambiguous self-referencing relationships must be configured</span></span>

[<span data-ttu-id="ae93f-873">Problema de seguimiento n.º 13573</span><span class="sxs-lookup"><span data-stu-id="ae93f-873">Tracking Issue #13573</span></span>](https://github.com/dotnet/efcore/issues/13573)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-874">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-874">Old behavior</span></span>

<span data-ttu-id="ae93f-875">Un tipo de entidad con varias propiedades de navegación unidireccional de referencia automática y claves externas coincidentes se configuró incorrectamente como una única relación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-875">An entity type with multiple self-referencing uni-directional navigation properties and matching FKs was incorrectly configured as a single relationship.</span></span> <span data-ttu-id="ae93f-876">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-876">For example:</span></span>

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

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-877">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-877">New behavior</span></span>

<span data-ttu-id="ae93f-878">Este escenario se detecta ahora en la generación del modelo y se produce una excepción que indica que el modelo es ambiguo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-878">This scenario is now detected in model building and an exception is thrown indicating that the model is ambiguous.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-879">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-879">Why</span></span>

<span data-ttu-id="ae93f-880">El modelo resultante era ambiguo, y lo más probable es que sea incorrecto en este caso.</span><span class="sxs-lookup"><span data-stu-id="ae93f-880">The resultant model was ambiguous and will likely usually be wrong for this case.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-881">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-881">Mitigations</span></span>

<span data-ttu-id="ae93f-882">Utilice la configuración completa de la relación.</span><span class="sxs-lookup"><span data-stu-id="ae93f-882">Use full configuration of the relationship.</span></span> <span data-ttu-id="ae93f-883">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="ae93f-883">For example:</span></span>

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

### <a name="dbfunctionschema-being-null-or-empty-string-configures-it-to-be-in-models-default-schema"></a><span data-ttu-id="ae93f-884">DbFunction.Schema es NULL o la cadena vacía lo configura para estar en el esquema predeterminado del modelo</span><span class="sxs-lookup"><span data-stu-id="ae93f-884">DbFunction.Schema being null or empty string configures it to be in model's default schema</span></span>

[<span data-ttu-id="ae93f-885">Problema de seguimiento n.º 12757</span><span class="sxs-lookup"><span data-stu-id="ae93f-885">Tracking Issue #12757</span></span>](https://github.com/dotnet/efcore/issues/12757)

#### <a name="old-behavior"></a><span data-ttu-id="ae93f-886">Comportamiento anterior</span><span class="sxs-lookup"><span data-stu-id="ae93f-886">Old behavior</span></span>

<span data-ttu-id="ae93f-887">Una función DbFunction configurada con el esquema como una cadena vacía se trataba como una función integrada sin un esquema.</span><span class="sxs-lookup"><span data-stu-id="ae93f-887">A DbFunction configured with schema as an empty string was treated as built-in function without a schema.</span></span> <span data-ttu-id="ae93f-888">Por ejemplo, el código siguiente asignará la función CLR `DatePart` a la función integrada `DATEPART` en SqlServer.</span><span class="sxs-lookup"><span data-stu-id="ae93f-888">For example following code will map `DatePart` CLR function to `DATEPART` built-in function on SqlServer.</span></span>

```csharp
[DbFunction("DATEPART", Schema = "")]
public static int? DatePart(string datePartArg, DateTime? date) => throw new Exception();

```

#### <a name="new-behavior"></a><span data-ttu-id="ae93f-889">Comportamiento nuevo</span><span class="sxs-lookup"><span data-stu-id="ae93f-889">New behavior</span></span>

<span data-ttu-id="ae93f-890">Todas las asignaciones de DbFunction se consideran asignadas a funciones definidas por el usuario.</span><span class="sxs-lookup"><span data-stu-id="ae93f-890">All DbFunction mappings are considered to be mapped to user defined functions.</span></span> <span data-ttu-id="ae93f-891">Por lo tanto, el valor de cadena vacía colocaría la función dentro del esquema predeterminado del modelo,</span><span class="sxs-lookup"><span data-stu-id="ae93f-891">Hence empty string value would put the function inside the default schema for the model.</span></span> <span data-ttu-id="ae93f-892">que podría ser el esquema configurado de forma explícita mediante `modelBuilder.HasDefaultSchema()` de la API fluida o `dbo` en caso contrario.</span><span class="sxs-lookup"><span data-stu-id="ae93f-892">Which could be the schema configured explicitly via fluent API `modelBuilder.HasDefaultSchema()` or `dbo` otherwise.</span></span>

#### <a name="why"></a><span data-ttu-id="ae93f-893">Por qué</span><span class="sxs-lookup"><span data-stu-id="ae93f-893">Why</span></span>

<span data-ttu-id="ae93f-894">Anteriormente, el esquema vacío era una manera de indicar que la función estaba integrada, pero esa lógica solo es aplicable a SqlServer, donde las funciones integradas no pertenecen a ningún esquema.</span><span class="sxs-lookup"><span data-stu-id="ae93f-894">Previously schema being empty was a way to treat that function is built-in but that logic is only applicable for SqlServer where built-in functions do not belong to any schema.</span></span>

#### <a name="mitigations"></a><span data-ttu-id="ae93f-895">Mitigaciones</span><span class="sxs-lookup"><span data-stu-id="ae93f-895">Mitigations</span></span>

<span data-ttu-id="ae93f-896">Configure la traslación de DbFunction manualmente para asignarla a una función integrada.</span><span class="sxs-lookup"><span data-stu-id="ae93f-896">Configure DbFunction's translation manually to map it to a built-in function.</span></span>

```csharp
modelBuilder
    .HasDbFunction(typeof(MyContext).GetMethod(nameof(MyContext.DatePart)))
    .HasTranslation(args => SqlFunctionExpression.Create("DatePart", args, typeof(int?), null));
```

<a name="netstandard21"></a>

### <a name="ef-core-30-targets-net-standard-21-rather-than-net-standard-20-reverted"></a><span data-ttu-id="ae93f-897">~~EF Core 3.0 tiene como destino .NET Standard 2.1, y no .NET Standard 2.0~~ Revertido</span><span class="sxs-lookup"><span data-stu-id="ae93f-897">~~EF Core 3.0 targets .NET Standard 2.1 rather than .NET Standard 2.0~~ Reverted</span></span>

[<span data-ttu-id="ae93f-898">Problema de seguimiento n.º 15498</span><span class="sxs-lookup"><span data-stu-id="ae93f-898">Tracking Issue #15498</span></span>](https://github.com/dotnet/efcore/issues/15498)

<span data-ttu-id="ae93f-899">EF Core 3.0 tiene como destino .NET Standard 2.1, que es un cambio importante que excluye las aplicaciones de .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="ae93f-899">EF Core 3.0 targets .NET Standard 2.1, which is a breaking change which excludes .NET Framework applications.</span></span> <span data-ttu-id="ae93f-900">En EF Core 3.1 se revirtió esto y ahora tiene como destino .NET Standard 2.0 de nuevo.</span><span class="sxs-lookup"><span data-stu-id="ae93f-900">EF Core 3.1 reverted this and targets .NET Standard 2.0 again.</span></span>

<a name="qe"></a>

### <a name="query-execution-is-logged-at-debug-level-reverted"></a><span data-ttu-id="ae93f-901">~~La ejecución de consultas se registra en el nivel de depuración~~ Revertido</span><span class="sxs-lookup"><span data-stu-id="ae93f-901">~~Query execution is logged at Debug level~~ Reverted</span></span>

[<span data-ttu-id="ae93f-902">Problema de seguimiento n.º 14523</span><span class="sxs-lookup"><span data-stu-id="ae93f-902">Tracking Issue #14523</span></span>](https://github.com/dotnet/efcore/issues/14523)

<span data-ttu-id="ae93f-903">Revertimos este cambio porque la nueva configuración de EF Core 3.0 permite a la aplicación especificar el nivel de registro para cualquier evento.</span><span class="sxs-lookup"><span data-stu-id="ae93f-903">We reverted this change because new configuration in EF Core 3.0 allows the log level for any event to be specified by the application.</span></span> <span data-ttu-id="ae93f-904">Por ejemplo, para cambiar el registro de SQL a `Debug`, configure el nivel de forma explícita en `OnConfiguring` o `AddDbContext`:</span><span class="sxs-lookup"><span data-stu-id="ae93f-904">For example, to switch logging of SQL to `Debug`, explicitly configure the level in `OnConfiguring` or `AddDbContext`:</span></span>

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    => optionsBuilder
        .UseSqlServer(connectionString)
        .ConfigureWarnings(c => c.Log((RelationalEventId.CommandExecuting, LogLevel.Debug)));
```
