---
title: Novedades de EF Core 2.0 - EF Core
author: divega
ms.author: divega
ms.date: 02/20/2018
ms.assetid: 2CB5809E-0EFB-44F6-AF14-9D5BFFFBFF9D
ms.technology: entity-framework-core
uid: core/what-is-new/ef-core-2.0
ms.openlocfilehash: 02d0b6fe2956e819e08e08c9a0658008abd36c34
ms.sourcegitcommit: b2d94cebdc32edad4fecb07e53fece66437d1b04
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="new-features-in-ef-core-20"></a><span data-ttu-id="be6d9-102">Nuevas características de EF Core 2.0</span><span class="sxs-lookup"><span data-stu-id="be6d9-102">New features in EF Core 2.0</span></span>

## <a name="net-standard-20"></a><span data-ttu-id="be6d9-103">.NET Standard 2.0</span><span class="sxs-lookup"><span data-stu-id="be6d9-103">.NET Standard 2.0</span></span>
<span data-ttu-id="be6d9-104">EF Core tiene ahora como destino .NET Standard 2.0, lo que significa que puede trabajar con .NET Core 2.0, .NET Framework 4.6.1 y otras bibliotecas que implementan .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="be6d9-104">EF Core now targets .NET Standard 2.0, which means it can work with .NET Core 2.0, .NET Framework 4.6.1, and other libraries that implement .NET Standard 2.0.</span></span>
<span data-ttu-id="be6d9-105">Vea [Implementaciones de .NET compatibles](../platforms/index.md) para obtener más detalles sobre lo que se admite.</span><span class="sxs-lookup"><span data-stu-id="be6d9-105">See [Supported .NET Implementations](../platforms/index.md) for more details on what is supported.</span></span>

## <a name="modeling"></a><span data-ttu-id="be6d9-106">Modelado</span><span class="sxs-lookup"><span data-stu-id="be6d9-106">Modeling</span></span>

### <a name="table-splitting"></a><span data-ttu-id="be6d9-107">División de tablas</span><span class="sxs-lookup"><span data-stu-id="be6d9-107">Table splitting</span></span>

<span data-ttu-id="be6d9-108">Ahora es posible asignar dos o más tipos de entidad a la misma tabla en la que se van a compartir las columnas de clave principal y cada fila va a corresponder a dos o más entidades.</span><span class="sxs-lookup"><span data-stu-id="be6d9-108">It is now possible to map two or more entity types to the same table where the primary key column(s) will be shared and each row will correspond to two or more entities.</span></span>

<span data-ttu-id="be6d9-109">Para usar la división de tabla, debe configurarse una relación de identificación (donde las propiedades de clave externa forman la clave principal) entre todos los tipos de entidad que comparten la tabla:</span><span class="sxs-lookup"><span data-stu-id="be6d9-109">To use table splitting an identifying relationship (where foreign key properties form the primary key) must be configured between all of the entity types sharing the table:</span></span>

``` csharp
modelBuilder.Entity<Product>()
    .HasOne(e => e.Details).WithOne(e => e.Product)
    .HasForeignKey<ProductDetails>(e => e.Id);
modelBuilder.Entity<Product>().ToTable("Products");
modelBuilder.Entity<ProductDetails>().ToTable("Products");
```

### <a name="owned-types"></a><span data-ttu-id="be6d9-110">Tipos de propiedad</span><span class="sxs-lookup"><span data-stu-id="be6d9-110">Owned types</span></span>

<span data-ttu-id="be6d9-111">Un tipo de entidad de propiedad puede compartir el mismo tipo CLR con otro tipo de entidad de propiedad, pero dado que no se puede identificar simplemente por el tipo CLR, debe haber una navegación a él desde otro tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="be6d9-111">An owned entity type can share the same CLR type with another owned entity type, but since it cannot be identified just by the CLR type there must be a navigation to it from another entity type.</span></span> <span data-ttu-id="be6d9-112">La entidad que contiene la navegación definitoria es el propietario.</span><span class="sxs-lookup"><span data-stu-id="be6d9-112">The entity containing the defining navigation is the owner.</span></span> <span data-ttu-id="be6d9-113">Al consultar al propietario, los tipos de propiedad se incluyen de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="be6d9-113">When querying the owner the owned types will be included by default.</span></span>

<span data-ttu-id="be6d9-114">Por convención, se crea una clave principal paralela para el tipo de propiedad y se asigna a la misma tabla que el propietario mediante la división de tabla.</span><span class="sxs-lookup"><span data-stu-id="be6d9-114">By convention a shadow primary key will be created for the owned type and it will be mapped to the same table as the owner by using table splitting.</span></span> <span data-ttu-id="be6d9-115">Esto permite usar tipos de propiedad de forma similar al modo en que se usan los tipos complejos en EF6:</span><span class="sxs-lookup"><span data-stu-id="be6d9-115">This allows to use owned types similarly to how complex types are used in EF6:</span></span>

``` csharp
modelBuilder.Entity<Order>().OwnsOne(p => p.OrderDetails, cb =>
    {
        cb.OwnsOne(c => c.BillingAddress);
        cb.OwnsOne(c => c.ShippingAddress);
    });

public class Order
{
    public int Id { get; set; }
    public OrderDetails OrderDetails { get; set; }
}

public class OrderDetails
{
    public StreetAddress BillingAddress { get; set; }
    public StreetAddress ShippingAddress { get; set; }
}

public class StreetAddress
{
    public string Street { get; set; }
    public string City { get; set; }
}
```
<span data-ttu-id="be6d9-116">Consulte la [sección sobre tipos de entidad en propiedad](xref:core/modeling/owned-entities) para obtener más información sobre esta característica.</span><span class="sxs-lookup"><span data-stu-id="be6d9-116">Read the [section on owned entity types](xref:core/modeling/owned-entities) for more information on this feature.</span></span>

### <a name="model-level-query-filters"></a><span data-ttu-id="be6d9-117">Filtros de consulta de nivel de modelo</span><span class="sxs-lookup"><span data-stu-id="be6d9-117">Model-level query filters</span></span>

<span data-ttu-id="be6d9-118">EF Core 2.0 incluye una nueva característica que se denomina filtros de consulta de nivel de modelo.</span><span class="sxs-lookup"><span data-stu-id="be6d9-118">EF Core 2.0 includes a new feature we call Model-level query filters.</span></span> <span data-ttu-id="be6d9-119">Esta característica permite que los predicados de consulta LINQ (una expresión booleana que normalmente se pasa al operador de consulta Where de LINQ) se definan directamente en tipos de entidad del modelo de metadatos (normalmente en OnModelCreating).</span><span class="sxs-lookup"><span data-stu-id="be6d9-119">This feature allows LINQ query predicates (a boolean expression typically passed to the LINQ Where query operator) to be defined directly on Entity Types in the metadata model (usually in OnModelCreating).</span></span> <span data-ttu-id="be6d9-120">Estos filtros se aplican automáticamente a las consultas LINQ que implican a esos tipos de entidad, incluidos aquellos a los que se hace referencia de forma indirecta, por ejemplo mediante el uso de Include o de referencias de propiedad de navegación directas.</span><span class="sxs-lookup"><span data-stu-id="be6d9-120">Such filters are automatically applied to any LINQ queries involving those Entity Types, including Entity Types referenced indirectly, such as through the use of Include or direct navigation property references.</span></span> <span data-ttu-id="be6d9-121">Algunas aplicaciones comunes de esta característica son:</span><span class="sxs-lookup"><span data-stu-id="be6d9-121">Some common applications of this feature are:</span></span>

- <span data-ttu-id="be6d9-122">Eliminación temporal: un tipo de entidad define una propiedad IsDeleted.</span><span class="sxs-lookup"><span data-stu-id="be6d9-122">Soft delete - An Entity Types defines an IsDeleted property.</span></span>
- <span data-ttu-id="be6d9-123">Servicios multiinquilino: un tipo de entidad define una propiedad TenantId.</span><span class="sxs-lookup"><span data-stu-id="be6d9-123">Multi-tenancy - An Entity Type defines a TenantId property.</span></span>

<span data-ttu-id="be6d9-124">Este es un ejemplo sencillo que muestra la característica para los dos escenarios mencionados arriba:</span><span class="sxs-lookup"><span data-stu-id="be6d9-124">Here is a simple example demonstrating the feature for the two scenarios listed above:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    public int TenantId { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>().HasQueryFilter(
            p => !p.IsDeleted
            && p.TenantId == this.TenantId );
    }
}
```
<span data-ttu-id="be6d9-125">Se define un filtro de nivel de modelo que implementa los servicios multiinquilino y la eliminación temporal para instancias del tipo de entidad ```Post```.</span><span class="sxs-lookup"><span data-stu-id="be6d9-125">We define a model-level filter that implements multi-tenancy and soft-delete for instances of the ```Post``` Entity Type.</span></span> <span data-ttu-id="be6d9-126">Observe el uso de una propiedad de nivel de instancia DbContext: ```TenantId```.</span><span class="sxs-lookup"><span data-stu-id="be6d9-126">Note the use of a DbContext instance level property: ```TenantId```.</span></span> <span data-ttu-id="be6d9-127">Los filtros de nivel de modelo usan el valor de la instancia de contexto correcta.</span><span class="sxs-lookup"><span data-stu-id="be6d9-127">Model-level filters will use the value from the correct context instance.</span></span> <span data-ttu-id="be6d9-128">Es decir, la que está ejecutando la consulta.</span><span class="sxs-lookup"><span data-stu-id="be6d9-128">I.e. the one that is executing the query.</span></span>

<span data-ttu-id="be6d9-129">Los filtros se pueden deshabilitar para consultas LINQ individuales mediante el operador IgnoreQueryFilters().</span><span class="sxs-lookup"><span data-stu-id="be6d9-129">Filters may be disabled for individual LINQ queries using the IgnoreQueryFilters() operator.</span></span>

#### <a name="limitations"></a><span data-ttu-id="be6d9-130">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="be6d9-130">Limitations</span></span>

- <span data-ttu-id="be6d9-131">No se permiten las referencias de navegación.</span><span class="sxs-lookup"><span data-stu-id="be6d9-131">Navigation references are not allowed.</span></span> <span data-ttu-id="be6d9-132">Esta característica se puede agregar en función de los comentarios.</span><span class="sxs-lookup"><span data-stu-id="be6d9-132">This feature may be added based on feedback.</span></span>
- <span data-ttu-id="be6d9-133">Solo se pueden definir filtros en el tipo de entidad raíz de una jerarquía.</span><span class="sxs-lookup"><span data-stu-id="be6d9-133">Filters can only be defined on the root Entity Type of a hierarchy.</span></span>

### <a name="database-scalar-function-mapping"></a><span data-ttu-id="be6d9-134">Asignación de función escalar de base de datos</span><span class="sxs-lookup"><span data-stu-id="be6d9-134">Database scalar function mapping</span></span>

<span data-ttu-id="be6d9-135">EF Core 2.0 incluye una importante contribución de [Paul Middleton](https://github.com/pmiddleton) que permite la asignación de funciones escalares de base de datos a stubs de método para que puedan usarse en consultas LINQ y trasladarse a SQL.</span><span class="sxs-lookup"><span data-stu-id="be6d9-135">EF Core 2.0 includes an important contribution from [Paul Middleton](https://github.com/pmiddleton) which enables mapping database scalar functions to method stubs so that they can be used in LINQ queries and translated to SQL.</span></span>

<span data-ttu-id="be6d9-136">Esta es una breve descripción de cómo se puede usar la característica:</span><span class="sxs-lookup"><span data-stu-id="be6d9-136">Here is a brief description of how the feature can be used:</span></span>

<span data-ttu-id="be6d9-137">Declare un método estático en `DbContext` y anótelo con `DbFunctionAttribute`:</span><span class="sxs-lookup"><span data-stu-id="be6d9-137">Declare a static method on your `DbContext` and annotate it with `DbFunctionAttribute`:</span></span>

``` csharp
public class BloggingContext : DbContext
{
    [DbFunction]
    public static int PostReadCount(int blogId)
    {
        throw new Exception();
    }
}
```

<span data-ttu-id="be6d9-138">Los métodos como este se registran automáticamente.</span><span class="sxs-lookup"><span data-stu-id="be6d9-138">Methods like this are automatically registered.</span></span> <span data-ttu-id="be6d9-139">Una vez registrados, las llamadas al método de una consulta LINQ pueden trasladarse a llamadas a funciones de SQL:</span><span class="sxs-lookup"><span data-stu-id="be6d9-139">Once registered, calls to the method in a LINQ query can be translated to function calls in SQL:</span></span>

``` csharp
var query =
    from p in context.Posts
    where BloggingContext.PostReadCount(p.Id) > 5
    select p;
```

<span data-ttu-id="be6d9-140">Algunas observaciones:</span><span class="sxs-lookup"><span data-stu-id="be6d9-140">A few things to note:</span></span>

- <span data-ttu-id="be6d9-141">Por convención, el nombre del método se usa como nombre de una función (en este caso una función definida por el usuario) al generar el SQL, pero puede invalidar el nombre y el esquema durante el registro del método.</span><span class="sxs-lookup"><span data-stu-id="be6d9-141">By convention the name of the method is used as the name of a function (in this case a user defined function) when generating the SQL, but you can override the name and schema during method registration</span></span>
- <span data-ttu-id="be6d9-142">Actualmente solo se admiten las funciones escalares.</span><span class="sxs-lookup"><span data-stu-id="be6d9-142">Currently only scalar functions are supported</span></span>
- <span data-ttu-id="be6d9-143">Debe crear la función asignada en la base de datos, por ejemplo, las migraciones de EF Core no se encargan de crearla.</span><span class="sxs-lookup"><span data-stu-id="be6d9-143">You must create the mapped function in the database, e.g. EF Core migrations will not take care of creating it</span></span>

### <a name="self-contained-type-configuration-for-code-first"></a><span data-ttu-id="be6d9-144">Configuración de tipo independiente para Code First</span><span class="sxs-lookup"><span data-stu-id="be6d9-144">Self-contained type configuration for code first</span></span>

<span data-ttu-id="be6d9-145">En EF6 era posible encapsular la configuración de Code First de un tipo de entidad concreto al derivarlo de *EntityTypeConfiguration*.</span><span class="sxs-lookup"><span data-stu-id="be6d9-145">In EF6 it was possible to encapsulate the code first configuration of a specific entity type by deriving from *EntityTypeConfiguration*.</span></span> <span data-ttu-id="be6d9-146">En EF Core 2.0 se vuelve a incluir este patrón:</span><span class="sxs-lookup"><span data-stu-id="be6d9-146">In EF Core 2.0 we are bringing this pattern back:</span></span>

``` csharp
class CustomerConfiguration : IEntityTypeConfiguration<Customer>
{
  public void Configure(EntityTypeBuilder<Customer> builder)
  {
     builder.HasKey(c => c.AlternateKey);
     builder.Property(c => c.Name).HasMaxLength(200);
   }
}

...
// OnModelCreating
builder.ApplyConfiguration(new CustomerConfiguration());
```

## <a name="high-performance"></a><span data-ttu-id="be6d9-147">Alto rendimiento</span><span class="sxs-lookup"><span data-stu-id="be6d9-147">High Performance</span></span>

### <a name="dbcontext-pooling"></a><span data-ttu-id="be6d9-148">Agrupación de DbContext</span><span class="sxs-lookup"><span data-stu-id="be6d9-148">DbContext pooling</span></span>

<span data-ttu-id="be6d9-149">El patrón básico para usar EF Core en una aplicación de ASP.NET Core normalmente implica el registro de un tipo de DbContext personalizado en el sistema de inserción de dependencias y la posterior obtención de instancias de ese tipo a través de los parámetros del constructor de los controladores.</span><span class="sxs-lookup"><span data-stu-id="be6d9-149">The basic pattern for using EF Core in an ASP.NET Core application usually involves registering a custom DbContext type into the dependency injection system and later obtaining instances of that type through constructor parameters in controllers.</span></span> <span data-ttu-id="be6d9-150">Esto significa que se crea una nueva instancia de DbContext para cada solicitud.</span><span class="sxs-lookup"><span data-stu-id="be6d9-150">This means a new instance of the DbContext is created for each requests.</span></span>

<span data-ttu-id="be6d9-151">En la versión 2.0 se incorpora una nueva manera de registrar tipos de DbContext personalizados en la inserción de dependencias que presenta un grupo de instancias de DbContext reutilizables de forma transparente.</span><span class="sxs-lookup"><span data-stu-id="be6d9-151">In version 2.0 we are introducing a new way to register custom DbContext types in dependency injection which transparently introduces a pool of reusable DbContext instances.</span></span> <span data-ttu-id="be6d9-152">Para usar la agrupación de DbContext, use ```AddDbContextPool``` en lugar de ```AddDbContext``` durante el registro del servicio:</span><span class="sxs-lookup"><span data-stu-id="be6d9-152">To use DbContext pooling, use the ```AddDbContextPool``` instead of ```AddDbContext``` during service registration:</span></span>

``` csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

<span data-ttu-id="be6d9-153">Si se usa este método, en el momento en que un controlador solicita una instancia de DbContext, primero se comprueba si hay una disponible en el grupo.</span><span class="sxs-lookup"><span data-stu-id="be6d9-153">If this method is used, at the time a DbContext instance is requested by a controller we will first check if there is an instance available in the pool.</span></span> <span data-ttu-id="be6d9-154">Una vez que termina el procesamiento de la solicitud, se restablece cualquier estado en la instancia y la propia instancia se devuelve al grupo.</span><span class="sxs-lookup"><span data-stu-id="be6d9-154">Once the request processing finalizes, any state on the instance is reset and the instance is itself returned to the pool.</span></span>

<span data-ttu-id="be6d9-155">Esto es conceptualmente similar a la forma en que funciona la agrupación de conexiones en los proveedores de ADO.NET y tiene la ventaja de ahorrar algunos de los costos de inicialización de la instancia de DbContext.</span><span class="sxs-lookup"><span data-stu-id="be6d9-155">This is conceptually similar to how connection pooling operates in ADO.NET providers and has the advantage of saving some of the cost of initialization of DbContext instance.</span></span>

### <a name="limitations"></a><span data-ttu-id="be6d9-156">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="be6d9-156">Limitations</span></span>

<span data-ttu-id="be6d9-157">El nuevo método presenta algunas limitaciones con respecto a lo que se puede hacer en el método ```OnConfiguring()``` de DbContext.</span><span class="sxs-lookup"><span data-stu-id="be6d9-157">The new method introduces a few limitations on what can be done in the ```OnConfiguring()``` method of the DbContext.</span></span>

> [!WARNING]  
> <span data-ttu-id="be6d9-158">Evite el uso de la agrupación de DbContext si mantiene su propio estado (por ejemplo, campos privados) en la clase derivada DbContext que no debe compartirse con otras solicitudes.</span><span class="sxs-lookup"><span data-stu-id="be6d9-158">Avoid using DbContext Pooling if you maintain your own state (e.g. private fields) in your derived DbContext class that should not be shared across requests.</span></span> <span data-ttu-id="be6d9-159">EF Core solo restablece el estado del que es consciente antes de agregar una instancia de DbContext al grupo.</span><span class="sxs-lookup"><span data-stu-id="be6d9-159">EF Core will only reset the state that is aware of before adding a DbContext instance to the pool.</span></span>

### <a name="explicitly-compiled-queries"></a><span data-ttu-id="be6d9-160">Consultas compiladas de manera explícita</span><span class="sxs-lookup"><span data-stu-id="be6d9-160">Explicitly compiled queries</span></span>

<span data-ttu-id="be6d9-161">Esta es la segunda característica opcional diseñada para ofrecer ventajas en escenarios de gran escala.</span><span class="sxs-lookup"><span data-stu-id="be6d9-161">This is the second opt-in performance features designed to offer benefits in high-scale scenarios.</span></span>

<span data-ttu-id="be6d9-162">Las API de consulta compiladas de forma manual o explícita han estado disponibles en versiones anteriores de EF y también en LINQ to SQL para permitir que las aplicaciones almacenen en caché la traducción de consultas de modo que se puedan calcular una sola vez y ejecutarse muchas veces.</span><span class="sxs-lookup"><span data-stu-id="be6d9-162">Manual or explicitly compiled query APIs have been available in previous versions of EF and also in LINQ to SQL to allow applications to cache the translation of queries so that they can be computed only once and executed many times.</span></span>

<span data-ttu-id="be6d9-163">Aunque en general EF Core puede compilar y almacenar en caché automáticamente las consultas en función de una representación con hash de las expresiones de consulta, este mecanismo puede usarse para obtener una pequeña mejora de rendimiento al omitir el cálculo del hash y la búsqueda en caché, lo que permite que la aplicación use una consulta ya compilada mediante la invocación de un delegado.</span><span class="sxs-lookup"><span data-stu-id="be6d9-163">Although in general EF Core can automatically compile and cache queries based on a hashed representation of the query expressions, this mechanism can be used to obtain a small performance gain by bypassing the computation of the hash and the cache lookup, allowing the application to use an already compiled query through the invocation of a delegate.</span></span>

``` csharp
// Create an explicitly compiled query
private static Func<CustomerContext, int, Customer> _customerById =
    EF.CompileQuery((CustomerContext db, int id) =>
        db.Customers
            .Include(c => c.Address)
            .Single(c => c.Id == id));

// Use the compiled query by invoking it
using (var db = new CustomerContext())
{
   var customer = _customerById(db, 147);
}
```

## <a name="change-tracking"></a><span data-ttu-id="be6d9-164">Seguimiento de cambios</span><span class="sxs-lookup"><span data-stu-id="be6d9-164">Change Tracking</span></span>

### <a name="attach-can-track-a-graph-of-new-and-existing-entities"></a><span data-ttu-id="be6d9-165">La asociación permite realizar un seguimiento de un gráfico de entidades nuevas y existentes.</span><span class="sxs-lookup"><span data-stu-id="be6d9-165">Attach can track a graph of new and existing entities</span></span>

<span data-ttu-id="be6d9-166">EF Core admite la generación automática de valores de clave a través de una serie de mecanismos.</span><span class="sxs-lookup"><span data-stu-id="be6d9-166">EF Core supports automatic generation of key values through a variety of mechanisms.</span></span> <span data-ttu-id="be6d9-167">Al usar esta característica, se genera un valor si la propiedad de clave es el valor predeterminado de CLR, normalmente cero o null.</span><span class="sxs-lookup"><span data-stu-id="be6d9-167">When using this feature, a value is generated if the key property is the CLR default--usually zero or null.</span></span> <span data-ttu-id="be6d9-168">Esto significa que se puede pasar un gráfico de entidades a `DbContext.Attach` o `DbSet.Attach` y que EF Core marca aquellas entidades que tienen una clave ya establecida como `Unchanged`, mientras que las que no tienen establecida una clave se marcan como `Added`.</span><span class="sxs-lookup"><span data-stu-id="be6d9-168">This means that a graph of entities can be passed to `DbContext.Attach` or `DbSet.Attach` and EF Core will mark those entities that have a key already set as `Unchanged` while those entities that do not have a key set will be marked as `Added`.</span></span> <span data-ttu-id="be6d9-169">Esto facilita la tarea de asociar un gráfico de entidades mixtas nuevas y existentes al usar claves generadas.</span><span class="sxs-lookup"><span data-stu-id="be6d9-169">This makes it easy to attach a graph of mixed new and existing entities when using generated keys.</span></span> <span data-ttu-id="be6d9-170">`DbContext.Update` y `DbSet.Update` funcionan de la misma manera, salvo que las entidades con una clave establecida se marcan como `Modified` en lugar de `Unchanged`.</span><span class="sxs-lookup"><span data-stu-id="be6d9-170">`DbContext.Update` and `DbSet.Update` work in the same way, except that entities with a key set are marked as `Modified` instead of `Unchanged`.</span></span>

## <a name="query"></a><span data-ttu-id="be6d9-171">Consulta</span><span class="sxs-lookup"><span data-stu-id="be6d9-171">Query</span></span>

### <a name="improved-linq-translation"></a><span data-ttu-id="be6d9-172">Traducción de LINQ mejorada</span><span class="sxs-lookup"><span data-stu-id="be6d9-172">Improved LINQ Translation</span></span>

<span data-ttu-id="be6d9-173">Permite que más consultas se ejecuten correctamente, con más lógica evaluada en la base de datos (en lugar de en memoria) y menos datos innecesariamente recuperados de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="be6d9-173">Enables more queries to successfully execute, with more logic being evaluated in the database (rather than in-memory) and less data unnecessarily being retrieved from the database.</span></span>

### <a name="groupjoin-improvements"></a><span data-ttu-id="be6d9-174">Mejoras de GroupJoin</span><span class="sxs-lookup"><span data-stu-id="be6d9-174">GroupJoin improvements</span></span>

<span data-ttu-id="be6d9-175">Este trabajo mejora el SQL que se genera para las combinaciones agrupadas.</span><span class="sxs-lookup"><span data-stu-id="be6d9-175">This work improves the SQL that is generated for group joins.</span></span> <span data-ttu-id="be6d9-176">Las combinaciones agrupadas suelen ser un resultado de subconsultas en propiedades de navegación opcionales.</span><span class="sxs-lookup"><span data-stu-id="be6d9-176">Group joins are most often a result of sub-queries on optional navigation properties.</span></span>

### <a name="string-interpolation-in-fromsql-and-executesqlcommand"></a><span data-ttu-id="be6d9-177">Interpolación de cadenas en FromSql y ExecuteSqlCommand</span><span class="sxs-lookup"><span data-stu-id="be6d9-177">String interpolation in FromSql and ExecuteSqlCommand</span></span>

<span data-ttu-id="be6d9-178">C# 6 presentó la interpolación de cadenas, una característica que permite insertar expresiones de C# directamente en literales de cadena, lo que proporciona una forma útil de compilar cadenas en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="be6d9-178">C# 6 introduced String Interpolation, a feature that allows C# expressions to be directly embedded in string literals, providing a nice way of building strings at runtime.</span></span> <span data-ttu-id="be6d9-179">En EF Core 2.0 se ha agregado compatibilidad especial con las cadenas interpoladas a las dos API principales que aceptan cadenas SQL sin formato: ```FromSql``` y ```ExecuteSqlCommand```.</span><span class="sxs-lookup"><span data-stu-id="be6d9-179">In EF Core 2.0 we added special support for interpolated strings to our two primary APIs that accept raw SQL strings: ```FromSql``` and ```ExecuteSqlCommand```.</span></span> <span data-ttu-id="be6d9-180">Esta nueva compatibilidad permite que la interpolación de cadenas de C# se use de forma "segura".</span><span class="sxs-lookup"><span data-stu-id="be6d9-180">This new support allows C# string interpolation to be used in a 'safe' manner.</span></span> <span data-ttu-id="be6d9-181">Es decir, de una forma que protege frente a errores de inserción de SQL comunes que pueden producirse al crear SQL de forma dinámica en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="be6d9-181">I.e. in a way that protects against common SQL injection mistakes that can occur when dynamically constructing SQL at runtime.</span></span>

<span data-ttu-id="be6d9-182">A continuación se muestra un ejemplo:</span><span class="sxs-lookup"><span data-stu-id="be6d9-182">Here is an example:</span></span>

``` csharp
var city = "London";
var contactTitle = "Sales Representative";

using (var context = CreateContext())
{
    context.Set<Customer>()
        .FromSql($@"
            SELECT *
            FROM ""Customers""
            WHERE ""City"" = {city} AND
                ""ContactTitle"" = {contactTitle}")
            .ToArray();
  }
```

<span data-ttu-id="be6d9-183">En este ejemplo hay dos variables insertadas en la cadena de formato SQL.</span><span class="sxs-lookup"><span data-stu-id="be6d9-183">In this example, there are two variables embedded in the SQL format string.</span></span> <span data-ttu-id="be6d9-184">EF Core genera el SQL siguiente:</span><span class="sxs-lookup"><span data-stu-id="be6d9-184">EF Core will produce the following SQL:</span></span>

```sql
@p0='London' (Size = 4000)
@p1='Sales Representative' (Size = 4000)

SELECT *
FROM ""Customers""
WHERE ""City"" = @p0
    AND ""ContactTitle"" = @p1
```

### <a name="effunctionslike"></a><span data-ttu-id="be6d9-185">EF.Functions.Like()</span><span class="sxs-lookup"><span data-stu-id="be6d9-185">EF.Functions.Like()</span></span>

<span data-ttu-id="be6d9-186">Se ha agregado la propiedad EF.Functions, que EF Core o los proveedores pueden usar para definir métodos que se asignen a los operadores o a las funciones de base de datos de forma que se puedan invocar en consultas LINQ.</span><span class="sxs-lookup"><span data-stu-id="be6d9-186">We have added the EF.Functions property which can be used by EF Core or providers to define methods that map to database functions or operators so that those can be invoked in LINQ queries.</span></span> <span data-ttu-id="be6d9-187">El primer ejemplo de este método es Like():</span><span class="sxs-lookup"><span data-stu-id="be6d9-187">The first example of such a method is Like():</span></span>

``` csharp
var aCustomers =
    from c in context.Customers
    where EF.Functions.Like(c.Name, "a%");
    select c;
```

<span data-ttu-id="be6d9-188">Observe que Like() incluye una implementación en memoria, lo que puede resultar útil al trabajar en una base de datos en memoria o cuando es necesario evaluar el predicado en el lado cliente.</span><span class="sxs-lookup"><span data-stu-id="be6d9-188">Note that Like() comes with an in-memory implementation, which can be handy when working against an in-memory database or when evaluation of the predicate needs to occur on the client side.</span></span>

## <a name="database-management"></a><span data-ttu-id="be6d9-189">Administración de bases de datos</span><span class="sxs-lookup"><span data-stu-id="be6d9-189">Database management</span></span>

### <a name="pluralization-hook-for-dbcontext-scaffolding"></a><span data-ttu-id="be6d9-190">Enlace de pluralización para scaffolding de DbContext</span><span class="sxs-lookup"><span data-stu-id="be6d9-190">Pluralization hook for DbContext Scaffolding</span></span>

<span data-ttu-id="be6d9-191">EF Core 2.0 presenta un nuevo servicio *IPluralizer* que se usa para singularizar nombres de tipo de entidad y pluralizar nombres DbSet.</span><span class="sxs-lookup"><span data-stu-id="be6d9-191">EF Core 2.0 introduces a new *IPluralizer* service that is used to singularize entity type names and pluralize DbSet names.</span></span> <span data-ttu-id="be6d9-192">La implementación predeterminada no está operativa, por lo que simplemente se trata de un enlace en el que los usuarios pueden conectar fácilmente su propio pluralizador.</span><span class="sxs-lookup"><span data-stu-id="be6d9-192">The default implementation is a no-op, so this is just a hook where folks can easily plug in their own pluralizer.</span></span>

<span data-ttu-id="be6d9-193">Este es el aspecto del enlace de un desarrollador de su propio pluralizador:</span><span class="sxs-lookup"><span data-stu-id="be6d9-193">Here is what it looks like for a developer to hook in their own pluralizer:</span></span>

``` csharp
public class MyDesignTimeServices : IDesignTimeServices
{
    public void ConfigureDesignTimeServices(IServiceCollection services)
    {
        services.AddSingleton<IPluralizer, MyPluralizer>();
    }
}

public class MyPluralizer : IPluralizer
{
    public string Pluralize(string name)
    {
        return Inflector.Inflector.Pluralize(name) ?? name;
    }

    public string Singularize(string name)
    {
        return Inflector.Inflector.Singularize(name) ?? name;
    }
}
```

## <a name="others"></a><span data-ttu-id="be6d9-194">Otros</span><span class="sxs-lookup"><span data-stu-id="be6d9-194">Others</span></span>

### <a name="move-adonet-sqlite-provider-to-sqlitepclraw"></a><span data-ttu-id="be6d9-195">Traslado del proveedor de SQLite de ADO.NET a SQLitePCL.raw</span><span class="sxs-lookup"><span data-stu-id="be6d9-195">Move ADO.NET SQLite provider to SQLitePCL.raw</span></span>
<span data-ttu-id="be6d9-196">Esto proporciona una solución más robusta en Microsoft.Data.Sqlite para distribuir archivos binarios nativos de SQLite en distintas plataformas.</span><span class="sxs-lookup"><span data-stu-id="be6d9-196">This gives us a more robust solution in Microsoft.Data.Sqlite for distributing native SQLite binaries on different platforms.</span></span>

### <a name="only-one-provider-per-model"></a><span data-ttu-id="be6d9-197">Solo un proveedor por modelo</span><span class="sxs-lookup"><span data-stu-id="be6d9-197">Only one provider per model</span></span>
<span data-ttu-id="be6d9-198">Mejora considerablemente la forma en que los proveedores pueden interactuar con el modelo y simplifica el funcionamiento de las convenciones, las anotaciones y las API fluidas con distintos proveedores.</span><span class="sxs-lookup"><span data-stu-id="be6d9-198">Significantly augments how providers can interact with the model and simplifies how conventions, annotations and fluent APIs work with different providers.</span></span>

<span data-ttu-id="be6d9-199">EF Core 2.0 ahora compila un elemento [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) diferente para cada proveedor que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="be6d9-199">EF Core 2.0 will now build a different [IModel](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/Metadata/IModel.cs) for each different provider being used.</span></span> <span data-ttu-id="be6d9-200">Esto suele ser transparente para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="be6d9-200">This is usually transparent to the application.</span></span> <span data-ttu-id="be6d9-201">Esto ha permitido una simplificación de las API de metadatos de nivel inferior, de modo que cualquier acceso a *conceptos de metadatos relacionales comunes* siempre se realiza mediante una llamada a `.Relational` en lugar de a `.SqlServer`, `.Sqlite`, etc.</span><span class="sxs-lookup"><span data-stu-id="be6d9-201">This has facilitated a simplification of lower-level metadata APIs such that any access to *common relational metadata concepts* is always made through a call to `.Relational` instead of `.SqlServer`, `.Sqlite`, etc.</span></span>

### <a name="consolidated-logging-and-diagnostics"></a><span data-ttu-id="be6d9-202">Registro y diagnóstico consolidados</span><span class="sxs-lookup"><span data-stu-id="be6d9-202">Consolidated Logging and Diagnostics</span></span>

<span data-ttu-id="be6d9-203">Los mecanismos de registro (basados en ILogger) y diagnóstico (basados en DiagnosticSource) ahora comparten más código.</span><span class="sxs-lookup"><span data-stu-id="be6d9-203">Logging (based on ILogger) and Diagnostics (based on DiagnosticSource) mechanisms now share more code.</span></span>

<span data-ttu-id="be6d9-204">Los identificadores de evento de los mensajes enviados a un elemento ILogger han cambiado en 2.0.</span><span class="sxs-lookup"><span data-stu-id="be6d9-204">The event IDs for messages sent to an ILogger have changed in 2.0.</span></span> <span data-ttu-id="be6d9-205">Los identificadores de evento ahora son únicos en el código de EF Core.</span><span class="sxs-lookup"><span data-stu-id="be6d9-205">The event IDs are now unique across EF Core code.</span></span> <span data-ttu-id="be6d9-206">Ahora, estos mensajes también siguen el patrón estándar de registro estructurado que usa, por ejemplo, MVC.</span><span class="sxs-lookup"><span data-stu-id="be6d9-206">These messages now also follow the standard pattern for structured logging used by, for example, MVC.</span></span>

<span data-ttu-id="be6d9-207">Las categorías de registrador también han cambiado.</span><span class="sxs-lookup"><span data-stu-id="be6d9-207">Logger categories have also changed.</span></span> <span data-ttu-id="be6d9-208">Ahora hay un conjunto conocido de categorías a las que se accede a través de [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs).</span><span class="sxs-lookup"><span data-stu-id="be6d9-208">There is now a well-known set of categories accessed through [DbLoggerCategory](https://github.com/aspnet/EntityFramework/blob/dev/src/EFCore/DbLoggerCategory.cs).</span></span>

<span data-ttu-id="be6d9-209">Los eventos de DiagnosticSource ahora usan los mismos nombres de identificador de evento que los mensajes de `ILogger` correspondientes.</span><span class="sxs-lookup"><span data-stu-id="be6d9-209">DiagnosticSource events now use the same event ID names as the corresponding `ILogger` messages.</span></span>