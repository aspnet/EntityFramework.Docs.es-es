---
title: Novedades de EF Core 1.0 - EF Core
author: divega
ms.date: 10/27/2016
ms.assetid: 20A25111-AEBE-4BC2-83A5-3F651952DF72
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: 2cd2a54d75ed3f0caa8b674dfb56babcfcc13592
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655853"
---
# <a name="features-included-in-ef-core-10"></a><span data-ttu-id="b8b7e-102">Características incluidas en EF Core 1.0</span><span class="sxs-lookup"><span data-stu-id="b8b7e-102">Features included in EF Core 1.0</span></span>

## <a name="platforms"></a><span data-ttu-id="b8b7e-103">Plataformas</span><span class="sxs-lookup"><span data-stu-id="b8b7e-103">Platforms</span></span>

### <a name="net-framework-451"></a><span data-ttu-id="b8b7e-104">.NET Framework 4.5.1</span><span class="sxs-lookup"><span data-stu-id="b8b7e-104">.NET Framework 4.5.1</span></span>

<span data-ttu-id="b8b7e-105">Incluye la consola, WPF, WinForms, ASP.NET 4, etc.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-105">Includes Console, WPF, WinForms, ASP.NET 4, etc.</span></span>

### <a name="net-standard-13"></a><span data-ttu-id="b8b7e-106">.NET Standard 1.3</span><span class="sxs-lookup"><span data-stu-id="b8b7e-106">.NET Standard 1.3</span></span>

<span data-ttu-id="b8b7e-107">Incluye ASP.NET Core que tiene como destino tanto .NET Framework como .NET Core en Windows, OSX y Linux.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-107">Including ASP.NET Core targeting both .NET Framework and .NET Core on Windows, OSX, and Linux.</span></span>

## <a name="modelling"></a><span data-ttu-id="b8b7e-108">Modelado</span><span class="sxs-lookup"><span data-stu-id="b8b7e-108">Modelling</span></span>

### <a name="basic-modelling"></a><span data-ttu-id="b8b7e-109">Modelado básico</span><span class="sxs-lookup"><span data-stu-id="b8b7e-109">Basic modelling</span></span>

<span data-ttu-id="b8b7e-110">Según las entidades POCO con las propiedades get/set de tipos escalares comunes (`int`, `string`, etc.).</span><span class="sxs-lookup"><span data-stu-id="b8b7e-110">Based on POCO entities with get/set properties of common scalar types (`int`, `string`, etc.).</span></span>

### <a name="relationships-and-navigation-properties"></a><span data-ttu-id="b8b7e-111">Relaciones y propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="b8b7e-111">Relationships and navigation properties</span></span>

<span data-ttu-id="b8b7e-112">Las relaciones uno a varios y uno a cero se pueden especificar en el modelo en función de una clave externa.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-112">One-to-many and One-to-zero-or-one relationships can be specified in the model based on a foreign key.</span></span> <span data-ttu-id="b8b7e-113">Las propiedades de navegación de tipos de referencia o colección simple se pueden asociar con estas relaciones.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-113">Navigation properties of simple collection or reference types can be associated with these relationships.</span></span>

### <a name="built-in-conventions"></a><span data-ttu-id="b8b7e-114">Convenciones integradas</span><span class="sxs-lookup"><span data-stu-id="b8b7e-114">Built-in conventions</span></span>

<span data-ttu-id="b8b7e-115">Construyen un modelo inicial en función de la forma de las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-115">These build an initial model based on the shape of the entity classes.</span></span>

### <a name="fluent-api"></a><span data-ttu-id="b8b7e-116">API fluida</span><span class="sxs-lookup"><span data-stu-id="b8b7e-116">Fluent API</span></span>

<span data-ttu-id="b8b7e-117">Permite reemplazar el método `OnModelCreating` en el contexto para seguir configurando el modelo que la convención detectó.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-117">Allows you to override the `OnModelCreating` method on your context to further configure the model that was discovered by convention.</span></span>

### <a name="data-annotations"></a><span data-ttu-id="b8b7e-118">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="b8b7e-118">Data annotations</span></span>

<span data-ttu-id="b8b7e-119">Son atributos que se pueden agregar a las propiedades o clases de entidad y que influyen en el modelo de EF.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-119">Are attributes that can be added to your entity classes/properties and will influence the EF model.</span></span> <span data-ttu-id="b8b7e-120">Por ejemplo, al agregar `[Required]` se indica a EF que una propiedad es obligatoria.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-120">For example, adding `[Required]` will let EF know that a property is required.</span></span>

### <a name="relational-table-mapping"></a><span data-ttu-id="b8b7e-121">Asignación de tabla relacional</span><span class="sxs-lookup"><span data-stu-id="b8b7e-121">Relational Table mapping</span></span>

<span data-ttu-id="b8b7e-122">Permite asignar las entidades a tablas o columnas.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-122">Allows entities to be mapped to tables/columns.</span></span>

### <a name="key-value-generation"></a><span data-ttu-id="b8b7e-123">Generación de valor de clave</span><span class="sxs-lookup"><span data-stu-id="b8b7e-123">Key value generation</span></span>

<span data-ttu-id="b8b7e-124">Incluye la generación de bases de datos y la generación del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-124">Including client-side generation and database generation.</span></span>

### <a name="database-generated-values"></a><span data-ttu-id="b8b7e-125">Valores generados por la base de datos</span><span class="sxs-lookup"><span data-stu-id="b8b7e-125">Database generated values</span></span>

<span data-ttu-id="b8b7e-126">Permite que la base de datos genere los valores en la inserción (valores predeterminados) o la actualización (columnas calculadas).</span><span class="sxs-lookup"><span data-stu-id="b8b7e-126">Allows for values to be generated by the database on insert (default values) or update (computed columns).</span></span>

### <a name="sequences-in-sql-server"></a><span data-ttu-id="b8b7e-127">Secuencias en SQL Server</span><span class="sxs-lookup"><span data-stu-id="b8b7e-127">Sequences in SQL Server</span></span>

<span data-ttu-id="b8b7e-128">Permite definir los objetos de secuencia en el modelo.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-128">Allows for sequence objects to be defined in the model.</span></span>

### <a name="unique-constraints"></a><span data-ttu-id="b8b7e-129">Restricciones UNIQUE</span><span class="sxs-lookup"><span data-stu-id="b8b7e-129">Unique constraints</span></span>

<span data-ttu-id="b8b7e-130">Permite la definición de las claves alternativas y la capacidad de definir las relaciones que se dirigen a esa clave.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-130">Allows for the definition of alternate keys and the ability to define relationships that target that key.</span></span>

### <a name="indexes"></a><span data-ttu-id="b8b7e-131">Índices</span><span class="sxs-lookup"><span data-stu-id="b8b7e-131">Indexes</span></span>

<span data-ttu-id="b8b7e-132">La definición de índices en el modelo introduce automáticamente índices en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-132">Defining indexes in the model automatically introduces indexes in the database.</span></span> <span data-ttu-id="b8b7e-133">También se admiten los índices únicos.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-133">Unique indexes are also supported.</span></span>

### <a name="shadow-state-properties"></a><span data-ttu-id="b8b7e-134">Propiedades de estado reemplazadas</span><span class="sxs-lookup"><span data-stu-id="b8b7e-134">Shadow state properties</span></span>

<span data-ttu-id="b8b7e-135">Permite que las propiedades que se definen en el modelo no se declaren ni almacenen en la clase .NET, pero EF Core sí puede hacer un seguimiento de ellas y actualizarlas.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-135">Allows for properties to be defined in the model that are not declared and are not stored in the .NET class but can be tracked and updated by EF Core.</span></span> <span data-ttu-id="b8b7e-136">Suele usarse para las propiedades de clave externa cuando no se desea exponerlas en el objeto.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-136">Commonly used for foreign key properties when exposing these in the object is not desired.</span></span>

### <a name="table-per-hierarchy-inheritance-pattern"></a><span data-ttu-id="b8b7e-137">Patrón de herencia de tabla por jerarquía</span><span class="sxs-lookup"><span data-stu-id="b8b7e-137">Table-Per-Hierarchy inheritance pattern</span></span>

<span data-ttu-id="b8b7e-138">Permite que las entidades de una jerarquía de herencia se guarde en una sola tabla a través de una columna de discriminador para identificar el tipo de entidad de un registro determinado en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-138">Allows entities in an inheritance hierarchy to be saved to a single table using a discriminator column to identify they entity type for a given record in the database.</span></span>

### <a name="model-validation"></a><span data-ttu-id="b8b7e-139">Validación de modelos</span><span class="sxs-lookup"><span data-stu-id="b8b7e-139">Model validation</span></span>

<span data-ttu-id="b8b7e-140">Detecta los patrones no válidos del modelo y proporciona mensajes de error útiles.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-140">Detects invalid patterns in the model and provides helpful error messages.</span></span>

## <a name="change-tracking"></a><span data-ttu-id="b8b7e-141">Seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="b8b7e-141">Change tracking</span></span>

### <a name="snapshot-change-tracking"></a><span data-ttu-id="b8b7e-142">Seguimiento de cambios de instantánea</span><span class="sxs-lookup"><span data-stu-id="b8b7e-142">Snapshot change tracking</span></span>

<span data-ttu-id="b8b7e-143">Permite detectar automáticamente los cambios en las entidades a través de la comparación del estado actual con una copia (instantánea) del estado original.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-143">Allows changes in entities to be detected automatically by comparing current state against a copy (snapshot) of the original state.</span></span>

### <a name="notification-change-tracking"></a><span data-ttu-id="b8b7e-144">Seguimiento de cambios de notificación</span><span class="sxs-lookup"><span data-stu-id="b8b7e-144">Notification change tracking</span></span>

<span data-ttu-id="b8b7e-145">Permite que las entidades notifiquen a la herramienta de seguimiento de cambios cuando se modifiquen los valores de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-145">Allows your entities to notify the change tracker when property values are modified.</span></span>

### <a name="accessing-tracked-state"></a><span data-ttu-id="b8b7e-146">Acceso al estado con seguimiento</span><span class="sxs-lookup"><span data-stu-id="b8b7e-146">Accessing tracked state</span></span>

<span data-ttu-id="b8b7e-147">A través de `DbContext.Entry` y `DbContext.ChangeTracker`.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-147">Via `DbContext.Entry` and `DbContext.ChangeTracker`.</span></span>

### <a name="attaching-detached-entitiesgraphs"></a><span data-ttu-id="b8b7e-148">Adjuntar grafos o entidades desasociados</span><span class="sxs-lookup"><span data-stu-id="b8b7e-148">Attaching detached entities/graphs</span></span>

<span data-ttu-id="b8b7e-149">La nueva API `DbContext.AttachGraph` ayuda a volver a adjuntar entidades a un contexto para guardar las entidades nuevas o modificadas.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-149">The new `DbContext.AttachGraph` API helps re-attach entities to a context in order to save new/modified entities.</span></span>

## <a name="saving-data"></a><span data-ttu-id="b8b7e-150">Guardado de datos</span><span class="sxs-lookup"><span data-stu-id="b8b7e-150">Saving data</span></span>

### <a name="basic-save-functionality"></a><span data-ttu-id="b8b7e-151">Funcionalidad básica de guardado</span><span class="sxs-lookup"><span data-stu-id="b8b7e-151">Basic save functionality</span></span>

<span data-ttu-id="b8b7e-152">Permite que los cambios en las instancias de la entidad se conserven en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-152">Allows changes to entity instances to be persisted to the database.</span></span>

### <a name="optimistic-concurrency"></a><span data-ttu-id="b8b7e-153">Simultaneidad optimista</span><span class="sxs-lookup"><span data-stu-id="b8b7e-153">Optimistic Concurrency</span></span>

<span data-ttu-id="b8b7e-154">Impide sobrescribir los cambios realizados por otro usuario desde que se capturaron de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-154">Protects against overwriting changes made by another user since data was fetched from the database.</span></span>

### <a name="async-savechanges"></a><span data-ttu-id="b8b7e-155">Característica SaveChanges asincrónica</span><span class="sxs-lookup"><span data-stu-id="b8b7e-155">Async SaveChanges</span></span>

<span data-ttu-id="b8b7e-156">Puede liberar el subproceso actual para que procese otras solicitudes mientras la base de datos procesa los comandos que se emiten desde `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-156">Can free up the current thread to process other requests while the database processes the commands issued from `SaveChanges`.</span></span>

### <a name="database-transactions"></a><span data-ttu-id="b8b7e-157">Transacciones de bases de datos</span><span class="sxs-lookup"><span data-stu-id="b8b7e-157">Database Transactions</span></span>

<span data-ttu-id="b8b7e-158">Es decir, `SaveChanges` siempre es atómica (lo que significa que siempre se completa correctamente o que no se realiza ningún cambio en la base de datos).</span><span class="sxs-lookup"><span data-stu-id="b8b7e-158">Means that `SaveChanges` is always atomic (meaning it either completely succeeds, or no changes are made to the database).</span></span> <span data-ttu-id="b8b7e-159">También hay API relacionadas con transacciones que permiten compartir las transacciones entre las instancias de contexto, etc.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-159">There are also transaction related APIs to allow sharing transactions between context instances etc.</span></span>

### <a name="relational-batching-of-statements"></a><span data-ttu-id="b8b7e-160">Relacional: Procesamiento de instrucciones por lotes</span><span class="sxs-lookup"><span data-stu-id="b8b7e-160">Relational: Batching of statements</span></span>

<span data-ttu-id="b8b7e-161">Proporciona un mejor rendimiento mediante el procesamiento por lotes de varios comandos INSERT/UPDATE/DELETE en un solo ciclo de ida y vuelta a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-161">Provides better performance by batching up multiple INSERT/UPDATE/DELETE commands into a single roundtrip to the database.</span></span>

## <a name="query"></a><span data-ttu-id="b8b7e-162">Consulta</span><span class="sxs-lookup"><span data-stu-id="b8b7e-162">Query</span></span>

### <a name="basic-linq-support"></a><span data-ttu-id="b8b7e-163">Compatibilidad básica con LINQ</span><span class="sxs-lookup"><span data-stu-id="b8b7e-163">Basic LINQ support</span></span>

<span data-ttu-id="b8b7e-164">Proporciona la capacidad de usar LINQ para recuperar datos de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-164">Provides the ability to use LINQ to retrieve data from the database.</span></span>

### <a name="mixed-clientserver-evaluation"></a><span data-ttu-id="b8b7e-165">Evaluación combinada de cliente/servidor</span><span class="sxs-lookup"><span data-stu-id="b8b7e-165">Mixed client/server evaluation</span></span>

<span data-ttu-id="b8b7e-166">Permite que las consultas contengan una lógica que no se puede evaluar en la base de datos y, por lo tanto, se debe evaluar después de que los datos se recuperan en la memoria.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-166">Enables queries to contain logic that cannot be evaluated in the database, and must therefore be evaluated after the data is retrieved into memory.</span></span>

### <a name="notracking"></a><span data-ttu-id="b8b7e-167">NoTracking</span><span class="sxs-lookup"><span data-stu-id="b8b7e-167">NoTracking</span></span>

<span data-ttu-id="b8b7e-168">Las consultas permiten ejecutar más rápido las consultas cuando el contexto no necesita supervisar los cambios realizados en las instancias de entidad (esto es útil si los resultados son de solo lectura).</span><span class="sxs-lookup"><span data-stu-id="b8b7e-168">Queries enables quicker query execution when the context does not need to monitor for changes to the entity instances (this is useful if the results are read-only).</span></span>

### <a name="eager-loading"></a><span data-ttu-id="b8b7e-169">Carga diligente</span><span class="sxs-lookup"><span data-stu-id="b8b7e-169">Eager loading</span></span>

<span data-ttu-id="b8b7e-170">Proporciona los métodos `Include` y `ThenInclude` para identificar los datos relacionados que se deben capturar cuando se realizan las consultas.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-170">Provides the `Include` and `ThenInclude` methods to identify related data that should also be fetched when querying.</span></span>

### <a name="async-query"></a><span data-ttu-id="b8b7e-171">Consulta asincrónica</span><span class="sxs-lookup"><span data-stu-id="b8b7e-171">Async query</span></span>

<span data-ttu-id="b8b7e-172">Puede liberar el subproceso actual (y los recursos asociados) para que procese otras solicitudes mientras la base de datos procesa la consulta.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-172">Can free up the current thread (and it's associated resources) to process other requests while the database processes the query.</span></span>

### <a name="raw-sql-queries"></a><span data-ttu-id="b8b7e-173">Consultas SQL sin formato</span><span class="sxs-lookup"><span data-stu-id="b8b7e-173">Raw SQL queries</span></span>

<span data-ttu-id="b8b7e-174">Proporciona el método `DbSet.FromSql` para usar consultas SQL sin procesar para capturar datos.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-174">Provides the `DbSet.FromSql` method to use raw SQL queries to fetch data.</span></span> <span data-ttu-id="b8b7e-175">Estas consultas también se pueden componer mediante LINQ.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-175">These queries can also be composed on using LINQ.</span></span>

## <a name="database-schema-management"></a><span data-ttu-id="b8b7e-176">Administración de esquemas de la base de datos</span><span class="sxs-lookup"><span data-stu-id="b8b7e-176">Database schema management</span></span>

### <a name="database-creationdeletion-apis"></a><span data-ttu-id="b8b7e-177">API de creación o eliminación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="b8b7e-177">Database creation/deletion APIs</span></span>

<span data-ttu-id="b8b7e-178">Diseñadas principalmente para realizar pruebas en las que desea crear o eliminar rápidamente la base de datos sin usar migraciones.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-178">Are mostly designed for testing where you want to quickly create/delete the database without using migrations.</span></span>

### <a name="relational-database-migrations"></a><span data-ttu-id="b8b7e-179">Migraciones de la base de datos relacional</span><span class="sxs-lookup"><span data-stu-id="b8b7e-179">Relational database migrations</span></span>

<span data-ttu-id="b8b7e-180">Permiten que un esquema de la base de datos relacional evolucione en el tiempo a medida que cambia el modelo.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-180">Allow a relational database schema to evolve overtime as your model changes.</span></span>

### <a name="reverse-engineer-from-database"></a><span data-ttu-id="b8b7e-181">Ingeniería inversa desde la base de datos</span><span class="sxs-lookup"><span data-stu-id="b8b7e-181">Reverse engineer from database</span></span>

<span data-ttu-id="b8b7e-182">Aplica scaffolding a un modelo de EF en función de un esquema de la base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-182">Scaffolds an EF model based on an existing relational database schema.</span></span>

## <a name="database-providers"></a><span data-ttu-id="b8b7e-183">Proveedores de bases de datos</span><span class="sxs-lookup"><span data-stu-id="b8b7e-183">Database providers</span></span>

### <a name="sql-server"></a><span data-ttu-id="b8b7e-184">SQL Server</span><span class="sxs-lookup"><span data-stu-id="b8b7e-184">SQL Server</span></span>

<span data-ttu-id="b8b7e-185">Se conecta a Microsoft SQL Server 2008 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-185">Connects to Microsoft SQL Server 2008 onwards.</span></span>

### <a name="sqlite"></a><span data-ttu-id="b8b7e-186">SQLite</span><span class="sxs-lookup"><span data-stu-id="b8b7e-186">SQLite</span></span>

<span data-ttu-id="b8b7e-187">Se conecta a una base de datos SQLite 3.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-187">Connects to a SQLite 3 database.</span></span>

### <a name="in-memory"></a><span data-ttu-id="b8b7e-188">In-Memory</span><span class="sxs-lookup"><span data-stu-id="b8b7e-188">In-Memory</span></span>

<span data-ttu-id="b8b7e-189">Diseñado para habilitar fácilmente la realización de pruebas sin conectarse a una base de datos real.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-189">Is designed to easily enable testing without connecting to a real database.</span></span>

### <a name="3rd-party-providers"></a><span data-ttu-id="b8b7e-190">Proveedores de terceros</span><span class="sxs-lookup"><span data-stu-id="b8b7e-190">3rd party providers</span></span>

<span data-ttu-id="b8b7e-191">Existen varios proveedores disponibles para otros motores de base de datos.</span><span class="sxs-lookup"><span data-stu-id="b8b7e-191">Several providers are available for other database engines.</span></span> <span data-ttu-id="b8b7e-192">Para una lista completa, consulte [Proveedores de bases de datos](../providers/index.md).</span><span class="sxs-lookup"><span data-stu-id="b8b7e-192">See [Database Providers](../providers/index.md) for a complete list.</span></span>
