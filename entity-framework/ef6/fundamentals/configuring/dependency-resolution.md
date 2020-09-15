---
title: 'Resolución de dependencias: EF6'
description: Resolución de dependencias en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/dependency-resolution
ms.openlocfilehash: 24e932646f0c44f642bff3f6db321590abae1c8d
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070554"
---
# <a name="dependency-resolution"></a><span data-ttu-id="a2c20-103">Resolución de dependencias</span><span class="sxs-lookup"><span data-stu-id="a2c20-103">Dependency resolution</span></span>
> [!NOTE]
> <span data-ttu-id="a2c20-104">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="a2c20-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="a2c20-105">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="a2c20-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="a2c20-106">A partir de EF6, Entity Framework contiene un mecanismo de uso general para obtener implementaciones de servicios que requiere.</span><span class="sxs-lookup"><span data-stu-id="a2c20-106">Starting with EF6, Entity Framework contains a general-purpose mechanism for obtaining implementations of services that it requires.</span></span> <span data-ttu-id="a2c20-107">Es decir, cuando EF usa una instancia de algunas interfaces o clases base, solicitará una implementación concreta de la interfaz o la clase base que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="a2c20-107">That is, when EF uses an instance of some interfaces or base classes it will ask for a concrete implementation of the interface or base class to use.</span></span> <span data-ttu-id="a2c20-108">Esto se logra mediante el uso de la interfaz IDbDependencyResolver:</span><span class="sxs-lookup"><span data-stu-id="a2c20-108">This is achieved through use of the IDbDependencyResolver interface:</span></span>  

``` csharp
public interface IDbDependencyResolver
{
    object GetService(Type type, object key);
}
```  

<span data-ttu-id="a2c20-109">EF normalmente llama al método GetService y se controla mediante una implementación de IDbDependencyResolver proporcionada por EF o por la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2c20-109">The GetService method is typically called by EF and is handled by an implementation of IDbDependencyResolver provided either by EF or by the application.</span></span> <span data-ttu-id="a2c20-110">Cuando se llama, el argumento de tipo es la interfaz o el tipo de clase base del servicio que se solicita, y el objeto de clave es null o un objeto que proporciona información contextual sobre el servicio solicitado.</span><span class="sxs-lookup"><span data-stu-id="a2c20-110">When called, the type argument is the interface or base class type of the service being requested, and the key object is either null or an object providing contextual information about the requested service.</span></span>  

<span data-ttu-id="a2c20-111">A menos que se indique lo contrario, cualquier objeto devuelto debe ser seguro para subprocesos, ya que se puede utilizar como singleton.</span><span class="sxs-lookup"><span data-stu-id="a2c20-111">Unless otherwise stated any object returned must be thread-safe since it can be used as a singleton.</span></span> <span data-ttu-id="a2c20-112">En muchos casos, el objeto devuelto es un generador, en cuyo caso el propio generador debe ser seguro para subprocesos, pero no es necesario que el objeto devuelto por el generador sea seguro para subprocesos, ya que se solicita una nueva instancia desde el generador para cada uso.</span><span class="sxs-lookup"><span data-stu-id="a2c20-112">In many cases the object returned is a factory in which case the factory itself must be thread-safe but the object returned from the factory does not need to be thread-safe since a new instance is requested from the factory for each use.</span></span>

<span data-ttu-id="a2c20-113">En este artículo no se incluyen detalles completos sobre cómo implementar IDbDependencyResolver, sino que actúa como referencia para los tipos de servicio (es decir, la interfaz y los tipos de clase base) para los que EF llama a GetService y la semántica del objeto de clave para cada una de estas llamadas.</span><span class="sxs-lookup"><span data-stu-id="a2c20-113">This article does not contain full details on how to implement IDbDependencyResolver, but instead acts as a reference for the service types (that is, the interface and base class types) for which EF calls GetService and the semantics of the key object for each of these calls.</span></span>

## <a name="systemdataentityidatabaseinitializertcontext"></a><span data-ttu-id="a2c20-114">System. Data. Entity. IDatabaseInitializer<TContext\></span><span class="sxs-lookup"><span data-stu-id="a2c20-114">System.Data.Entity.IDatabaseInitializer<TContext\></span></span>  

<span data-ttu-id="a2c20-115">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-115">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-116">**Objeto devuelto**: inicializador de base de datos para el tipo de contexto especificado</span><span class="sxs-lookup"><span data-stu-id="a2c20-116">**Object returned**: A database initializer for the given context type</span></span>  

<span data-ttu-id="a2c20-117">**Clave**: no se utiliza; será null</span><span class="sxs-lookup"><span data-stu-id="a2c20-117">**Key**: Not used; will be null</span></span>  

## <a name="funcsystemdataentitymigrationssqlmigrationsqlgenerator"></a><span data-ttu-id="a2c20-118">FUNC<System. Data. Entity. Migrations. SQL. MigrationSqlGenerator\></span><span class="sxs-lookup"><span data-stu-id="a2c20-118">Func<System.Data.Entity.Migrations.Sql.MigrationSqlGenerator\></span></span>  

<span data-ttu-id="a2c20-119">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-119">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="a2c20-120">**Objeto devuelto**: generador para crear un generador de SQL que se puede usar para las migraciones y otras acciones que hacen que se cree una base de datos, como la creación de bases de datos con inicializadores de base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2c20-120">**Object returned**: A factory to create a SQL generator that can be used for Migrations and other actions that cause a database to be created, such as database creation with database initializers.</span></span>  

<span data-ttu-id="a2c20-121">**Key**: una cadena que contiene el nombre invariable del proveedor ADO.net que especifica el tipo de base de datos para el que se generará SQL.</span><span class="sxs-lookup"><span data-stu-id="a2c20-121">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which SQL will be generated.</span></span> <span data-ttu-id="a2c20-122">Por ejemplo, se devuelve el generador de SQL Server SQL para la clave "System. Data. SqlClient".</span><span class="sxs-lookup"><span data-stu-id="a2c20-122">For example, the SQL Server SQL generator is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="a2c20-123">Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="a2c20-123">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycorecommondbproviderservices"></a><span data-ttu-id="a2c20-124">System. Data. Entity. Core. Common. DbProviderServices</span><span class="sxs-lookup"><span data-stu-id="a2c20-124">System.Data.Entity.Core.Common.DbProviderServices</span></span>  

<span data-ttu-id="a2c20-125">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-125">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-126">**Objeto devuelto**: el proveedor EF que se va a usar para un nombre invariable de proveedor determinado</span><span class="sxs-lookup"><span data-stu-id="a2c20-126">**Object returned**: The EF provider to use for a given provider invariant name</span></span>  

<span data-ttu-id="a2c20-127">**Key**: una cadena que contiene el nombre invariable del proveedor ADO.net que especifica el tipo de base de datos para el que se necesita un proveedor.</span><span class="sxs-lookup"><span data-stu-id="a2c20-127">**Key**: A string containing the ADO.NET provider invariant name specifying the type of database for which a provider is needed.</span></span> <span data-ttu-id="a2c20-128">Por ejemplo, se devuelve el proveedor de SQL Server para la clave "System. Data. SqlClient".</span><span class="sxs-lookup"><span data-stu-id="a2c20-128">For example, the SQL Server provider is returned for the key "System.Data.SqlClient".</span></span>  

>[!NOTE]
> <span data-ttu-id="a2c20-129">Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="a2c20-129">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureidbconnectionfactory"></a><span data-ttu-id="a2c20-130">System. Data. Entity. Infrastructure. IDbConnectionFactory</span><span class="sxs-lookup"><span data-stu-id="a2c20-130">System.Data.Entity.Infrastructure.IDbConnectionFactory</span></span>  

<span data-ttu-id="a2c20-131">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-131">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-132">**Objeto devuelto**: el generador de conexión que se usará cuando EF cree una conexión de base de datos por Convención.</span><span class="sxs-lookup"><span data-stu-id="a2c20-132">**Object returned**: The connection factory that will be used when EF creates a database connection by convention.</span></span> <span data-ttu-id="a2c20-133">Es decir, cuando no se proporciona ninguna conexión o cadena de conexión a EF, y no se encuentra ninguna cadena de conexión en el app.config o web.config, este servicio se usa para crear una conexión por Convención.</span><span class="sxs-lookup"><span data-stu-id="a2c20-133">That is, when no connection or connection string is given to EF, and no connection string can be found in the app.config or web.config, then this service is used to create a connection by convention.</span></span> <span data-ttu-id="a2c20-134">Cambiar el generador de conexiones puede permitir que EF use otro tipo de base de datos (por ejemplo, SQL Server Compact Edition) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="a2c20-134">Changing the connection factory can allow EF to use a different type of database (for example, SQL Server Compact Edition) by default.</span></span>  

<span data-ttu-id="a2c20-135">**Clave**: no se utiliza; será null</span><span class="sxs-lookup"><span data-stu-id="a2c20-135">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="a2c20-136">Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="a2c20-136">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureimanifesttokenservice"></a><span data-ttu-id="a2c20-137">System. Data. Entity. Infrastructure. IManifestTokenService</span><span class="sxs-lookup"><span data-stu-id="a2c20-137">System.Data.Entity.Infrastructure.IManifestTokenService</span></span>  

<span data-ttu-id="a2c20-138">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-138">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-139">**Objeto devuelto**: un servicio que puede generar un token del manifiesto del proveedor a partir de una conexión.</span><span class="sxs-lookup"><span data-stu-id="a2c20-139">**Object returned**: A service that can generate a provider manifest token from a connection.</span></span> <span data-ttu-id="a2c20-140">Este servicio se usa normalmente de dos maneras.</span><span class="sxs-lookup"><span data-stu-id="a2c20-140">This service is typically used in two ways.</span></span> <span data-ttu-id="a2c20-141">En primer lugar, se puede usar para evitar Code First conectarse a la base de datos al compilar un modelo.</span><span class="sxs-lookup"><span data-stu-id="a2c20-141">First, it can be used to avoid Code First connecting to the database when building a model.</span></span> <span data-ttu-id="a2c20-142">En segundo lugar, se puede usar para forzar Code First para generar un modelo para una versión de base de datos específica, por ejemplo, para forzar un modelo para SQL Server 2005 incluso si se usa a veces SQL Server 2008.</span><span class="sxs-lookup"><span data-stu-id="a2c20-142">Second, it can be used to force Code First to build a model for a specific database version -- for example, to force a model for SQL Server 2005 even if sometimes SQL Server 2008 is used.</span></span>  

<span data-ttu-id="a2c20-143">**Duración del objeto**: singleton: el mismo objeto se puede usar varias veces y simultáneamente en diferentes subprocesos</span><span class="sxs-lookup"><span data-stu-id="a2c20-143">**Object lifetime**: Singleton -- the same object may be used multiple times and concurrently by different threads</span></span>  

<span data-ttu-id="a2c20-144">**Clave**: no se utiliza; será null</span><span class="sxs-lookup"><span data-stu-id="a2c20-144">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureidbproviderfactoryservice"></a><span data-ttu-id="a2c20-145">System. Data. Entity. Infrastructure. IDbProviderFactoryService</span><span class="sxs-lookup"><span data-stu-id="a2c20-145">System.Data.Entity.Infrastructure.IDbProviderFactoryService</span></span>  

<span data-ttu-id="a2c20-146">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-146">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-147">**Objeto devuelto**: un servicio que puede obtener un generador de proveedores de una conexión determinada.</span><span class="sxs-lookup"><span data-stu-id="a2c20-147">**Object returned**: A service that can obtain a provider factory from a given connection.</span></span> <span data-ttu-id="a2c20-148">En .NET 4,5, el proveedor es accesible públicamente desde la conexión.</span><span class="sxs-lookup"><span data-stu-id="a2c20-148">On .NET 4.5 the provider is publicly accessible from the connection.</span></span> <span data-ttu-id="a2c20-149">En .NET 4, la implementación predeterminada de este servicio usa algunas heurísticas para encontrar el proveedor coincidente.</span><span class="sxs-lookup"><span data-stu-id="a2c20-149">On .NET 4 the default implementation of this service uses some heuristics to find the matching provider.</span></span> <span data-ttu-id="a2c20-150">Si se produce un error, se puede registrar una nueva implementación de este servicio para proporcionar una solución adecuada.</span><span class="sxs-lookup"><span data-stu-id="a2c20-150">If these fail then a new implementation of this service can be registered to provide an appropriate resolution.</span></span>  

<span data-ttu-id="a2c20-151">**Clave**: no se utiliza; será null</span><span class="sxs-lookup"><span data-stu-id="a2c20-151">**Key**: Not used; will be null</span></span>  

## <a name="funcdbcontext-systemdataentityinfrastructureidbmodelcachekey"></a><span data-ttu-id="a2c20-152">FUNC<DbContext, System. Data. Entity. Infrastructure. IDbModelCacheKey\></span><span class="sxs-lookup"><span data-stu-id="a2c20-152">Func<DbContext, System.Data.Entity.Infrastructure.IDbModelCacheKey\></span></span>  

<span data-ttu-id="a2c20-153">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-153">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-154">**Objeto devuelto**: un generador que generará una clave de caché del modelo para un contexto determinado.</span><span class="sxs-lookup"><span data-stu-id="a2c20-154">**Object returned**: A factory that will generate a model cache key for a given context.</span></span> <span data-ttu-id="a2c20-155">De forma predeterminada, EF almacena en caché un modelo por tipo DbContext por proveedor.</span><span class="sxs-lookup"><span data-stu-id="a2c20-155">By default, EF caches one model per DbContext type per provider.</span></span> <span data-ttu-id="a2c20-156">Se puede usar una implementación diferente de este servicio para agregar otra información, como el nombre de esquema, a la clave de caché.</span><span class="sxs-lookup"><span data-stu-id="a2c20-156">A different implementation of this service can be used to add other information, such as schema name, to the cache key.</span></span>  

<span data-ttu-id="a2c20-157">**Clave**: no se utiliza; será null</span><span class="sxs-lookup"><span data-stu-id="a2c20-157">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityspatialdbspatialservices"></a><span data-ttu-id="a2c20-158">System. Data. Entity. Spatial. DbSpatialServices</span><span class="sxs-lookup"><span data-stu-id="a2c20-158">System.Data.Entity.Spatial.DbSpatialServices</span></span>  

<span data-ttu-id="a2c20-159">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-159">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-160">**Objeto devuelto**: un proveedor espacial de EF que agrega compatibilidad con el proveedor de EF básico para los tipos espaciales Geography y Geometry.</span><span class="sxs-lookup"><span data-stu-id="a2c20-160">**Object returned**: An EF spatial provider that adds support to the basic EF provider for geography and geometry spatial types.</span></span>  

<span data-ttu-id="a2c20-161">**Clave**: DbSptialServices se solicita de dos maneras.</span><span class="sxs-lookup"><span data-stu-id="a2c20-161">**Key**: DbSptialServices is asked for in two ways.</span></span> <span data-ttu-id="a2c20-162">En primer lugar, se solicitan servicios espaciales específicos del proveedor mediante un objeto DbProviderInfo (que contiene el nombre invariable y el token del manifiesto) como clave.</span><span class="sxs-lookup"><span data-stu-id="a2c20-162">First, provider-specific spatial services are requested using a DbProviderInfo object (which contains invariant name and manifest token) as the key.</span></span> <span data-ttu-id="a2c20-163">En segundo lugar, se puede solicitar a DbSpatialServices sin clave.</span><span class="sxs-lookup"><span data-stu-id="a2c20-163">Second, DbSpatialServices can be asked for with no key.</span></span> <span data-ttu-id="a2c20-164">Se usa para resolver el "proveedor espacial global", que se usa al crear tipos independientes de DbGeography o DbGeometry.</span><span class="sxs-lookup"><span data-stu-id="a2c20-164">This is used to resolve the "global spatial provider" which is used when creating stand-alone DbGeography or DbGeometry types.</span></span>  

>[!NOTE]
> <span data-ttu-id="a2c20-165">Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="a2c20-165">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcsystemdataentityinfrastructureidbexecutionstrategy"></a><span data-ttu-id="a2c20-166">FUNC<System. Data. Entity. Infrastructure. IDbExecutionStrategy\></span><span class="sxs-lookup"><span data-stu-id="a2c20-166">Func<System.Data.Entity.Infrastructure.IDbExecutionStrategy\></span></span>  

<span data-ttu-id="a2c20-167">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-167">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-168">**Objeto devuelto**: generador para crear un servicio que permite a un proveedor implementar reintentos u otro comportamiento cuando las consultas y los comandos se ejecutan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a2c20-168">**Object returned**: A factory to create a service that allows a provider to implement retries or other behavior when queries and commands are executed against the database.</span></span> <span data-ttu-id="a2c20-169">Si no se proporciona ninguna implementación, EF simplemente ejecutará los comandos y propagará las excepciones que se produzcan.</span><span class="sxs-lookup"><span data-stu-id="a2c20-169">If no implementation is provided, then EF will simply execute the commands and propagate any exceptions thrown.</span></span> <span data-ttu-id="a2c20-170">Por SQL Server este servicio se utiliza para proporcionar una directiva de reintentos que es especialmente útil cuando se ejecuta en servidores de bases de datos basados en la nube, como SQL Azure.</span><span class="sxs-lookup"><span data-stu-id="a2c20-170">For SQL Server this service is used to provide a retry policy which is especially useful when running against cloud-based database servers such as SQL Azure.</span></span>  

<span data-ttu-id="a2c20-171">**Key**: un objeto ExecutionStrategyKey que contiene el nombre invariable del proveedor y, opcionalmente, un nombre de servidor para el que se usará la estrategia de ejecución.</span><span class="sxs-lookup"><span data-stu-id="a2c20-171">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the execution strategy will be used.</span></span>  

>[!NOTE]
> <span data-ttu-id="a2c20-172">Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="a2c20-172">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="funcdbconnection-string-systemdataentitymigrationshistoryhistorycontext"></a><span data-ttu-id="a2c20-173">FUNC<DbConnection, String, System. Data. Entity. Migrations. History. HistoryContext\></span><span class="sxs-lookup"><span data-stu-id="a2c20-173">Func<DbConnection, string, System.Data.Entity.Migrations.History.HistoryContext\></span></span>  

<span data-ttu-id="a2c20-174">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-174">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-175">**Objeto devuelto**: un generador que permite a un proveedor configurar la asignación de HistoryContext a la `__MigrationHistory` tabla usada por las migraciones de EF.</span><span class="sxs-lookup"><span data-stu-id="a2c20-175">**Object returned**: A factory that allows a provider to configure the mapping of the HistoryContext to the `__MigrationHistory` table used by EF Migrations.</span></span> <span data-ttu-id="a2c20-176">HistoryContext es un DbContext de Code First y se puede configurar mediante la API fluida normal para cambiar aspectos como el nombre de la tabla y las especificaciones de asignación de columnas.</span><span class="sxs-lookup"><span data-stu-id="a2c20-176">The HistoryContext is a Code First DbContext and can be configured using the normal fluent API to change things like the name of the table and the column mapping specifications.</span></span>  

<span data-ttu-id="a2c20-177">**Clave**: no se utiliza; será null</span><span class="sxs-lookup"><span data-stu-id="a2c20-177">**Key**: Not used; will be null</span></span>  

>[!NOTE]
> <span data-ttu-id="a2c20-178">Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="a2c20-178">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdatacommondbproviderfactory"></a><span data-ttu-id="a2c20-179">System. Data. Common. DbProviderFactory</span><span class="sxs-lookup"><span data-stu-id="a2c20-179">System.Data.Common.DbProviderFactory</span></span>  

<span data-ttu-id="a2c20-180">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-180">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-181">**Objeto devuelto**: el proveedor ADO.net que se va a usar para un nombre invariable de proveedor determinado.</span><span class="sxs-lookup"><span data-stu-id="a2c20-181">**Object returned**: The ADO.NET provider to use for a given provider invariant name.</span></span>  

<span data-ttu-id="a2c20-182">**Key**: una cadena que contiene el nombre invariable del proveedor ADO.net</span><span class="sxs-lookup"><span data-stu-id="a2c20-182">**Key**: A string containing the ADO.NET provider invariant name</span></span>  

>[!NOTE]
> <span data-ttu-id="a2c20-183">Normalmente, este servicio no se cambia directamente, ya que la implementación predeterminada usa el registro del proveedor ADO.NET normal.</span><span class="sxs-lookup"><span data-stu-id="a2c20-183">This service is not usually changed directly since the default implementation uses the normal ADO.NET provider registration.</span></span> <span data-ttu-id="a2c20-184">Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="a2c20-184">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentityinfrastructureiproviderinvariantname"></a><span data-ttu-id="a2c20-185">System. Data. Entity. Infrastructure. IProviderInvariantName</span><span class="sxs-lookup"><span data-stu-id="a2c20-185">System.Data.Entity.Infrastructure.IProviderInvariantName</span></span>  

<span data-ttu-id="a2c20-186">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-186">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-187">**Objeto devuelto**: un servicio que se utiliza para determinar un nombre invariable de proveedor para un tipo determinado de DbProviderFactory.</span><span class="sxs-lookup"><span data-stu-id="a2c20-187">**Object returned**: a service that is used to determine a provider invariant name for a given type of DbProviderFactory.</span></span> <span data-ttu-id="a2c20-188">La implementación predeterminada de este servicio utiliza el registro del proveedor ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="a2c20-188">The default implementation of this service uses the ADO.NET provider registration.</span></span> <span data-ttu-id="a2c20-189">Esto significa que si el proveedor ADO.NET no está registrado de la manera normal porque EF está resolviendo DbProviderFactory, también será necesario para resolver este servicio.</span><span class="sxs-lookup"><span data-stu-id="a2c20-189">This means that if the ADO.NET provider is not registered in the normal way because DbProviderFactory is being resolved by EF, then it will also be necessary to resolve this service.</span></span>  

<span data-ttu-id="a2c20-190">**Key**: la instancia de DbProviderFactory para la que se requiere un nombre invariable.</span><span class="sxs-lookup"><span data-stu-id="a2c20-190">**Key**: The DbProviderFactory instance for which an invariant name is required.</span></span>  

>[!NOTE]
> <span data-ttu-id="a2c20-191">Para obtener más información sobre los servicios relacionados con el proveedor en EF6, consulte la sección [modelo de proveedor EF6](xref:ef6/fundamentals/providers/provider-model) .</span><span class="sxs-lookup"><span data-stu-id="a2c20-191">For more details on provider-related services in EF6 see the [EF6 provider model](xref:ef6/fundamentals/providers/provider-model) section.</span></span>  

## <a name="systemdataentitycoremappingviewgenerationiviewassemblycache"></a><span data-ttu-id="a2c20-192">System. Data. Entity. Core. Mapping. ViewGeneration. IViewAssemblyCache</span><span class="sxs-lookup"><span data-stu-id="a2c20-192">System.Data.Entity.Core.Mapping.ViewGeneration.IViewAssemblyCache</span></span>  

<span data-ttu-id="a2c20-193">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-193">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-194">**Objeto devuelto**: memoria caché de los ensamblados que contienen las vistas generadas previamente.</span><span class="sxs-lookup"><span data-stu-id="a2c20-194">**Object returned**: a cache of the assemblies that contain pre-generated views.</span></span> <span data-ttu-id="a2c20-195">Un reemplazo se usa normalmente para permitir que EF sepa qué ensamblados contienen vistas generadas previamente sin realizar ninguna detección.</span><span class="sxs-lookup"><span data-stu-id="a2c20-195">A replacement is typically used to let EF know which assemblies contain pre-generated views without doing any discovery.</span></span>  

<span data-ttu-id="a2c20-196">**Clave**: no se utiliza; será null</span><span class="sxs-lookup"><span data-stu-id="a2c20-196">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructurepluralizationipluralizationservice"></a><span data-ttu-id="a2c20-197">System. Data. Entity. Infrastructure. pluralización. IPluralizationService</span><span class="sxs-lookup"><span data-stu-id="a2c20-197">System.Data.Entity.Infrastructure.Pluralization.IPluralizationService</span></span>

<span data-ttu-id="a2c20-198">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-198">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-199">**Objeto devuelto**: un servicio utilizado por EF para pluralar y singularar los nombres.</span><span class="sxs-lookup"><span data-stu-id="a2c20-199">**Object returned**: a service used by EF to pluralize and singularize names.</span></span> <span data-ttu-id="a2c20-200">De forma predeterminada, se usa un servicio de pluralización en inglés.</span><span class="sxs-lookup"><span data-stu-id="a2c20-200">By default an English pluralization service is used.</span></span>  

<span data-ttu-id="a2c20-201">**Clave**: no se utiliza; será null</span><span class="sxs-lookup"><span data-stu-id="a2c20-201">**Key**: Not used; will be null</span></span>  

## <a name="systemdataentityinfrastructureinterceptionidbinterceptor"></a><span data-ttu-id="a2c20-202">System. Data. Entity. Infrastructure. intercepción. IDbInterceptor</span><span class="sxs-lookup"><span data-stu-id="a2c20-202">System.Data.Entity.Infrastructure.Interception.IDbInterceptor</span></span>  

<span data-ttu-id="a2c20-203">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-203">**Version introduced**: EF6.0.0</span></span>

<span data-ttu-id="a2c20-204">**Objetos devueltos**: todos los interceptores que se deben registrar cuando se inicia la aplicación.</span><span class="sxs-lookup"><span data-stu-id="a2c20-204">**Objects returned**: Any interceptors that should be registered when the application starts.</span></span> <span data-ttu-id="a2c20-205">Tenga en cuenta que estos objetos se solicitan mediante la llamada a GetServices y todos los interceptores devueltos por cualquier solucionador de dependencias se registrarán.</span><span class="sxs-lookup"><span data-stu-id="a2c20-205">Note that these objects are requested using the GetServices call and all interceptors returned by any dependency resolver will registered.</span></span>

<span data-ttu-id="a2c20-206">**Clave**: no se utiliza; será null.</span><span class="sxs-lookup"><span data-stu-id="a2c20-206">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext-actionstring-systemdataentityinfrastructureinterceptiondatabaselogformatter"></a><span data-ttu-id="a2c20-207">FUNC<System. Data. Entity. DbContext, Action<String \> , System. Data. Entity. Infrastructure. intercepción. DatabaseLogFormatter\></span><span class="sxs-lookup"><span data-stu-id="a2c20-207">Func<System.Data.Entity.DbContext, Action<string\>, System.Data.Entity.Infrastructure.Interception.DatabaseLogFormatter\></span></span>  

<span data-ttu-id="a2c20-208">**Versión introducida**: EF 6.0.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-208">**Version introduced**: EF6.0.0</span></span>  

<span data-ttu-id="a2c20-209">**Objeto devuelto**: un generador que se usará para crear el formateador del registro de base de datos que se utilizará cuando el contexto. La propiedad Database. log está establecida en el contexto especificado.</span><span class="sxs-lookup"><span data-stu-id="a2c20-209">**Object returned**: A factory that will be used to create the database log formatter that will be used when the context.Database.Log property is set on the given context.</span></span>  

<span data-ttu-id="a2c20-210">**Clave**: no se utiliza; será null.</span><span class="sxs-lookup"><span data-stu-id="a2c20-210">**Key**: Not used; will be null.</span></span>  

## <a name="funcsystemdataentitydbcontext"></a><span data-ttu-id="a2c20-211">FUNC<System. Data. Entity. DbContext\></span><span class="sxs-lookup"><span data-stu-id="a2c20-211">Func<System.Data.Entity.DbContext\></span></span>  

<span data-ttu-id="a2c20-212">**Versión introducida**: EF 6.1.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-212">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="a2c20-213">**Objeto devuelto**: un generador que se usará para crear instancias de contexto para las migraciones cuando el contexto no tenga un constructor sin parámetros accesible.</span><span class="sxs-lookup"><span data-stu-id="a2c20-213">**Object returned**: A factory that will be used to create context instances for Migrations when the context does not have an accessible parameterless constructor.</span></span>  

<span data-ttu-id="a2c20-214">**Key**: el objeto de tipo para el tipo de DbContext derivado para el que se necesita un generador.</span><span class="sxs-lookup"><span data-stu-id="a2c20-214">**Key**: The Type object for the type of the derived DbContext for which a factory is needed.</span></span>  

## <a name="funcsystemdataentitycoremetadataedmimetadataannotationserializer"></a><span data-ttu-id="a2c20-215">FUNC<System. Data. Entity. Core. Metadata. Edm. IMetadataAnnotationSerializer\></span><span class="sxs-lookup"><span data-stu-id="a2c20-215">Func<System.Data.Entity.Core.Metadata.Edm.IMetadataAnnotationSerializer\></span></span>  

<span data-ttu-id="a2c20-216">**Versión introducida**: EF 6.1.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-216">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="a2c20-217">**Objeto devuelto**: un generador que se utilizará para crear serializadores para la serialización de anotaciones personalizadas fuertemente tipadas de modo que se puedan serializar y desesterilizar en XML para su uso en migraciones de Code First.</span><span class="sxs-lookup"><span data-stu-id="a2c20-217">**Object returned**: A factory that will be used to create serializers for serialization of strongly-typed custom annotations such that they can be serialized and desterilized into XML for use in Code First Migrations.</span></span>  

<span data-ttu-id="a2c20-218">**Key**: nombre de la anotación que se va a serializar o deserializar.</span><span class="sxs-lookup"><span data-stu-id="a2c20-218">**Key**: The name of the annotation that is being serialized or deserialized.</span></span>  

## <a name="funcsystemdataentityinfrastructuretransactionhandler"></a><span data-ttu-id="a2c20-219">FUNC<System. Data. Entity. Infrastructure. TransactionHandler\></span><span class="sxs-lookup"><span data-stu-id="a2c20-219">Func<System.Data.Entity.Infrastructure.TransactionHandler\></span></span>  

<span data-ttu-id="a2c20-220">**Versión introducida**: EF 6.1.0</span><span class="sxs-lookup"><span data-stu-id="a2c20-220">**Version introduced**: EF6.1.0</span></span>  

<span data-ttu-id="a2c20-221">**Objeto devuelto**: un generador que se usará para crear controladores para las transacciones, de modo que se pueda aplicar un control especial para situaciones como el control de errores de confirmación.</span><span class="sxs-lookup"><span data-stu-id="a2c20-221">**Object returned**: A factory that will be used to create handlers for transactions so that special handling can be applied for situations such as handling commit failures.</span></span>  

<span data-ttu-id="a2c20-222">**Key**: un objeto ExecutionStrategyKey que contiene el nombre invariable del proveedor y, opcionalmente, un nombre de servidor para el que se usará el controlador de transacciones.</span><span class="sxs-lookup"><span data-stu-id="a2c20-222">**Key**: An ExecutionStrategyKey object that contains the provider invariant name and optionally a server name for which the transaction handler will be used.</span></span>  
