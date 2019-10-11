---
title: Características nuevas de Entity Framework Core 3.0 - EF Core
author: divega
ms.date: 02/19/2019
ms.assetid: 2EBE2CCC-E52D-483F-834C-8877F5EB0C0C
uid: core/what-is-new/ef-core-3.0/index
ms.openlocfilehash: ccfb8259c70cf8706a06eb3b22b9541224c3b9bb
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182079"
---
# <a name="new-features-in-entity-framework-core-30"></a><span data-ttu-id="51a54-102">Características nuevas de Entity Framework Core 3.0</span><span class="sxs-lookup"><span data-stu-id="51a54-102">New features in Entity Framework Core 3.0</span></span>

<span data-ttu-id="51a54-103">En la lista siguiente se incluyen las principales características nuevas de EF Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="51a54-103">The following list includes the major new features in EF Core 3.0.</span></span>

<span data-ttu-id="51a54-104">Como versión principal, EF Core 3.0 también presenta varios [cambios importantes](xref:core/what-is-new/ef-core-3.0/breaking-changes), que son mejoras en la API que podrían afectar negativamente a las aplicaciones existentes.</span><span class="sxs-lookup"><span data-stu-id="51a54-104">As a major release, EF Core 3.0 also contains several [breaking changes](xref:core/what-is-new/ef-core-3.0/breaking-changes), which are API improvements that may have negative impact on existing applications.</span></span>  

## <a name="linq-overhaul"></a><span data-ttu-id="51a54-105">Revisión de LINQ</span><span class="sxs-lookup"><span data-stu-id="51a54-105">LINQ overhaul</span></span> 

<span data-ttu-id="51a54-106">LINQ permite escribir consultas a la base de datos en el lenguaje .NET que prefiera, con lo que se aprovecha la información de tipo enriquecido para ofrecer la comprobación de IntelliSense y de tipos en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="51a54-106">LINQ enables you to write database queries using your .NET language of choice, taking advantage of rich type information to offer IntelliSense and compile-time type checking.</span></span>
<span data-ttu-id="51a54-107">Pero LINQ también permite escribir un número ilimitado de consultas complicadas que contienen expresiones arbitrarias (llamadas a métodos u operaciones).</span><span class="sxs-lookup"><span data-stu-id="51a54-107">But LINQ also enables you to write an unlimited number of complicated queries containing arbitrary expressions (method calls or operations).</span></span>
<span data-ttu-id="51a54-108">Cómo controlar todas esas combinaciones es el principal desafío para los proveedores LINQ.</span><span class="sxs-lookup"><span data-stu-id="51a54-108">How to handle all those combinations is the main challenge for LINQ providers.</span></span>

<span data-ttu-id="51a54-109">En EF Core 3,0, hemos rediseñado nuestro proveedor LINQ para habilitar la conversión de más patrones de consulta en SQL, la generación de consultas eficientes en más casos y la prevención de que las consultas ineficaces no se detecten.</span><span class="sxs-lookup"><span data-stu-id="51a54-109">In EF Core 3.0, we rearchitected our LINQ provider to enable translating more query patterns into SQL, generating efficient queries in more cases, and preventing inefficient queries from going undetected.</span></span> <span data-ttu-id="51a54-110">El nuevo proveedor LINQ es la base sobre la que podremos ofrecer nuevas funcionalidades de consulta y mejoras de rendimiento en futuras versiones, sin interrumpir las aplicaciones y los proveedores de datos existentes.</span><span class="sxs-lookup"><span data-stu-id="51a54-110">The new LINQ provider is the foundation over which we'll be able to offer new query capabilities and performance improvements in future releases, without breaking existing applications and data providers.</span></span>

### <a name="restricted-client-evaluation"></a><span data-ttu-id="51a54-111">Evaluación de cliente restringida</span><span class="sxs-lookup"><span data-stu-id="51a54-111">Restricted client evaluation</span></span>
<span data-ttu-id="51a54-112">El cambio de diseño más importante tiene que ver con la forma en que manejamos las expresiones LINQ que no se pueden convertir a parámetros ni traducir a SQL.</span><span class="sxs-lookup"><span data-stu-id="51a54-112">The most important design change has to do with how we handle LINQ expressions that cannot be converted to parameters or translated to SQL.</span></span>

<span data-ttu-id="51a54-113">En las versiones anteriores, EF Core identificada qué partes de una consulta se podían traducir a SQL y ejecutaba el resto de la consulta en el cliente.</span><span class="sxs-lookup"><span data-stu-id="51a54-113">In previous versions, EF Core identified what portions of a query could be translated to SQL, and executed the rest of the query on the client.</span></span>
<span data-ttu-id="51a54-114">Este tipo de ejecución en el lado cliente es una opción interesante en algunas situaciones, pero en muchos otros casos puede dar lugar a consultas ineficaces.</span><span class="sxs-lookup"><span data-stu-id="51a54-114">This type of client-side execution is desirable in some situations, but in many other cases it can result in inefficient queries.</span></span>

<span data-ttu-id="51a54-115">Por ejemplo, si EF Core 2.2 no podía traducir un predicado en una llamada a `Where()`, ejecutaba una instrucción SQL sin filtro, transfería todas las filas de la base de datos y luego las filtraba en memoria:</span><span class="sxs-lookup"><span data-stu-id="51a54-115">For example, if EF Core 2.2 couldn't translate a predicate in a `Where()` call, it executed an SQL statement without a filter, transferred all the rows from the database, and then filtered them in-memory:</span></span>

``` csharp
var specialCustomers = 
  context.Customers
    .Where(c => c.Name.StartsWith(n) && IsSpecialCustomer(c));
```

<span data-ttu-id="51a54-116">Esta operación puede ser aceptable si la base de datos contiene pocas filas, pero puede dar lugar a problemas de rendimiento considerables o incluso errores en la aplicación si la base de datos contiene muchas filas.</span><span class="sxs-lookup"><span data-stu-id="51a54-116">That may be acceptable if the database contains a small number of rows but can result in significant performance issues or even application failure if the database contains a large number or rows.</span></span>

<span data-ttu-id="51a54-117">En EF Core 3.0 hemos restringido la evaluación de cliente para que solo suceda en la proyección de nivel superior (fundamentalmente, la última llamada a `Select()`).</span><span class="sxs-lookup"><span data-stu-id="51a54-117">In EF Core 3.0, we've restricted client evaluation to only happen on the top-level projection (essentially, the last call to `Select()`).</span></span>
<span data-ttu-id="51a54-118">Cuando EF Core 3.0 detecta expresiones que no se pueden traducir en ningún otro lugar de la consulta, produce una excepción en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="51a54-118">When EF Core 3.0 detects expressions that can't be translated anywhere else in the query, it throws a runtime exception.</span></span>

<span data-ttu-id="51a54-119">Para evaluar una condición de predicado en el cliente como en el ejemplo anterior, los desarrolladores ahora tienen que cambiar explícitamente la evaluación de la consulta a LINQ to Objects:</span><span class="sxs-lookup"><span data-stu-id="51a54-119">To evaluate a predicate condition on the client as in the previous example, developers now need to explicitly switch evaluation of the query to LINQ to Objects:</span></span> 

``` csharp
var specialCustomers =
  context.Customers
    .Where(c => c.Name.StartsWith(n)) 
    .AsEnumerable() // switches to LINQ to Objects
    .Where(c => IsSpecialCustomer(c));
```

<span data-ttu-id="51a54-120">Consulte la [documentación sobre cambios importantes](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) para más detalles sobre cómo esto puede afectar a las aplicaciones existentes.</span><span class="sxs-lookup"><span data-stu-id="51a54-120">See the [breaking changes documentation](xref:core/what-is-new/ef-core-3.0/breaking-changes#linq-queries-are-no-longer-evaluated-on-the-client) for more details about how this can affect existing applications.</span></span>

### <a name="single-sql-statement-per-linq-query"></a><span data-ttu-id="51a54-121">Instrucción SQL única por consulta LINQ</span><span class="sxs-lookup"><span data-stu-id="51a54-121">Single SQL statement per LINQ query</span></span>

<span data-ttu-id="51a54-122">Otro aspecto del diseño que cambió significativamente en la versión 3.0 es que ahora siempre se genera una única instrucción SQL por cada consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="51a54-122">Another aspect of the design that changed significantly in 3.0 is that we now always generate a single SQL statement per LINQ query.</span></span> <span data-ttu-id="51a54-123">En versiones anteriores, se usaba para generar varias instrucciones SQL en ciertos casos, como para traducir llamadas `Include()` en las propiedades de navegación de la colección, y para traducir las consultas que seguían determinados patrones con subconsultas.</span><span class="sxs-lookup"><span data-stu-id="51a54-123">In previous versions, we used to generate multiple SQL statements in certain cases, like to translate `Include()` calls on collection navigation properties and to translate queries that followed certain patterns with subqueries.</span></span> <span data-ttu-id="51a54-124">Aunque en algunos casos esto era conveniente, y para `Include()` incluso ayudaba a evitar el envío de datos redundantes a través de la conexión, la implementación era compleja, daba lugar a algunos comportamientos extremadamente ineficientes (consultas N+1) y había situaciones en las que el los datos devueltos a través de múltiples consultas podían ser incoherentes.</span><span class="sxs-lookup"><span data-stu-id="51a54-124">Although this was in some cases convenient, and for `Include()` it even helped avoid sending redundant data over the wire, the implementation was complex, it resulted in some extremely inefficient behaviors (N+1 queries), and there was situations in which the data returned across multiple queries could be inconsistent.</span></span>

<span data-ttu-id="51a54-125">De forma similar a la evaluación del cliente, si EF Core 3.0 no puede convertir una consulta LINQ en una única instrucción SQL, se inicia una excepción en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="51a54-125">Similarly to client evaluation, if EF Core 3.0 can't translate a LINQ query into a single SQL statement, it throws a runtime exception.</span></span> <span data-ttu-id="51a54-126">Pero hicimos que EF Core fuera capaz de traducir muchos de los patrones comunes que solían generar varias consultas en una sola consulta con JOIN.</span><span class="sxs-lookup"><span data-stu-id="51a54-126">But we made EF Core capable of translating many of the common patterns that used to generate multiple queries to a single query with JOINs.</span></span>

## <a name="cosmos-db-support"></a><span data-ttu-id="51a54-127">Compatibilidad con Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="51a54-127">Cosmos DB support</span></span> 

<span data-ttu-id="51a54-128">Con el proveedor de Cosmos DB para EF Core, los desarrolladores que están familiarizados con el modelo de programación de EF puedan usar fácilmente Azure Cosmos DB como base de datos de aplicación.</span><span class="sxs-lookup"><span data-stu-id="51a54-128">The Cosmos DB provider for EF Core enables developers familiar with the EF programing model to easily target Azure Cosmos DB as an application database.</span></span> <span data-ttu-id="51a54-129">El objetivo es hacer que algunas de las ventajas de Cosmos DB, como la distribución global, la disponibilidad "AlwaysOn", la escalabilidad elástica y la baja latencia, sean aún más accesibles para los desarrolladores de .NET.</span><span class="sxs-lookup"><span data-stu-id="51a54-129">The goal is to make some of the advantages of Cosmos DB, like global distribution, "always on" availability, elastic scalability, and low latency, even more accessible to .NET developers.</span></span> <span data-ttu-id="51a54-130">El proveedor habilita la mayoría de las características de EF Core, como el seguimiento automático de cambios, LINQ y conversiones de valores, en comparación con SQL API de Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="51a54-130">The provider enables most EF Core features, like automatic change tracking, LINQ, and value conversions, against the SQL API in Cosmos DB.</span></span>

<span data-ttu-id="51a54-131">Consulte la [documentación del proveedor Cosmos DB](xref:core/providers/cosmos/index) para más detalles.</span><span class="sxs-lookup"><span data-stu-id="51a54-131">See the [Cosmos DB provider documentation](xref:core/providers/cosmos/index) for more details.</span></span>

## <a name="c-80-support"></a><span data-ttu-id="51a54-132">Compatibilidad con C# 8.0</span><span class="sxs-lookup"><span data-stu-id="51a54-132">C# 8.0 support</span></span>

<span data-ttu-id="51a54-133">EF Core 3.0 aprovecha varias [características nuevas de C# 8.0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8):</span><span class="sxs-lookup"><span data-stu-id="51a54-133">EF Core 3.0 takes advantage of a couple of the [new features in C# 8.0](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8):</span></span>

### <a name="asynchronous-streams"></a><span data-ttu-id="51a54-134">Secuencias asincrónicas</span><span class="sxs-lookup"><span data-stu-id="51a54-134">Asynchronous streams</span></span>

<span data-ttu-id="51a54-135">Los resultados de la consulta asincrónica se exponen ahora mediante la nueva interfaz de `await foreach` estándar y se pueden usar con `IAsyncEnumerable<T>`.</span><span class="sxs-lookup"><span data-stu-id="51a54-135">Asynchronous query results are now exposed using the new standard `IAsyncEnumerable<T>` interface and can be consumed using `await foreach`.</span></span>

``` csharp
var orders = 
  from o in context.Orders
  where o.Status == OrderStatus.Pending
  select o;

await foreach(var o in orders.AsAsyncEnumerable())
{
  Process(o);
} 
```

<span data-ttu-id="51a54-136">Consulte las [transmisiones asíncronas en la documentación de C#](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) para más detalles.</span><span class="sxs-lookup"><span data-stu-id="51a54-136">See the [asynchronous streams in the C# documentation](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-8#asynchronous-streams) for more details.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="51a54-137">Tipos de referencia que aceptan valores NULL</span><span class="sxs-lookup"><span data-stu-id="51a54-137">Nullable reference types</span></span> 

<span data-ttu-id="51a54-138">Cuando esta nueva característica está habilitada en el código, EF Core examina la nulabilidad de las propiedades de tipo de referencia y la aplica a las columnas y relaciones correspondientes en la base de datos: las propiedades de los tipos de referencia no anulables se tratan como si tuvieran el atributo de anotación de datos `[Required]`.</span><span class="sxs-lookup"><span data-stu-id="51a54-138">When this new feature is enabled in your code, EF Core examines the nullability of reference type properties and applies it to corresponding columns and relationships in the database: properties of non-nullable references types are treated as if they had the `[Required]` data annotation attribute.</span></span>

<span data-ttu-id="51a54-139">Por ejemplo, en la clase siguiente, las propiedades marcadas como de tipo `string?` se configurarán como opcionales, mientras que `string` se configurará según sea necesario:</span><span class="sxs-lookup"><span data-stu-id="51a54-139">For example, in the following class, properties marked as of type `string?` will be configured as optional, whereas `string` will be configured as required:</span></span>

``` csharp
public class Customer
{
  public int Id { get; set; }
  public string FirstName { get; set; }
  public string LastName { get; set; }
  public string? MiddleName { get; set; }
}
```

<span data-ttu-id="51a54-140">Consulte [Trabajar con tipos de referencia que aceptan valores NULL](xref:core/miscellaneous/nullable-reference-types) en la documentación de EF Core para más detalles.</span><span class="sxs-lookup"><span data-stu-id="51a54-140">See [Working with nullable reference types](xref:core/miscellaneous/nullable-reference-types) in the EF Core documentation for more details.</span></span>

## <a name="interception-of-database-operations"></a><span data-ttu-id="51a54-141">Intercepción de operaciones de bases de datos</span><span class="sxs-lookup"><span data-stu-id="51a54-141">Interception of database operations</span></span>

<span data-ttu-id="51a54-142">La nueva API de intercepción en EF Core 3.0 permite proporcionar una lógica personalizada que se invoca automáticamente cada vez que se producen operaciones de base de datos de bajo nivel como parte del funcionamiento normal de EF Core.</span><span class="sxs-lookup"><span data-stu-id="51a54-142">The new interception API in EF Core 3.0 allows providing custom logic to be invoked automatically whenever low-level database operations occur as part of the normal operation of EF Core.</span></span> <span data-ttu-id="51a54-143">Por ejemplo, al abrir conexiones, confirmar transacciones o ejecutar comandos.</span><span class="sxs-lookup"><span data-stu-id="51a54-143">For example, when opening connections, committing transactions, or executing commands.</span></span> 

<span data-ttu-id="51a54-144">De manera similar a las características de intercepción que existían en EF 6, los interceptores le permiten interceptar operaciones antes o después de que sucedan.</span><span class="sxs-lookup"><span data-stu-id="51a54-144">Similarly to the interception features that existed in EF 6, interceptors allow you to intercept operations before or after they happen.</span></span> <span data-ttu-id="51a54-145">Cuando las intercepta antes de que sucedan, puede omitir la ejecución y proporcionar resultados alternativos de la lógica de intercepción.</span><span class="sxs-lookup"><span data-stu-id="51a54-145">When you intercept them before they happen, you are allowed to by-pass execution and supply alternate results from the interception logic.</span></span> 

<span data-ttu-id="51a54-146">Por ejemplo, para manipular el texto del comando, puede crear `IDbCommandInterceptor`:</span><span class="sxs-lookup"><span data-stu-id="51a54-146">For example, to manipulate command text, you can create an `IDbCommandInterceptor`:</span></span>

``` csharp 
public class HintCommandInterceptor : DbCommandInterceptor
{
  public override InterceptionResult ReaderExecuting(
    DbCommand command, 
    CommandEventData eventData, 
    InterceptionResult result)
  {
    // Manipulate the command text, etc. here...
    command.CommandText += " OPTION (OPTIMIZE FOR UNKNOWN)";
    return result;
  }
}
``` 

<span data-ttu-id="51a54-147">Y registrarlo con su  `DbContext`:</span><span class="sxs-lookup"><span data-stu-id="51a54-147">And register it with your `DbContext`:</span></span>

``` csharp
services.AddDbContext(b => b
  .UseSqlServer(connectionString)
  .AddInterceptors(new HintCommandInterceptor()));
```

## <a name="reverse-engineering-of-database-views"></a><span data-ttu-id="51a54-148">Ingeniería inversa de vistas de base de datos</span><span class="sxs-lookup"><span data-stu-id="51a54-148">Reverse engineering of database views</span></span>

<span data-ttu-id="51a54-149">El nombre de los tipos de consulta, que representan datos que pueden leerse de la base de datos pero no actualizarse, se ha cambiado a [tipos de entidad sin clave](xref:core/modeling/keyless-entity-types).</span><span class="sxs-lookup"><span data-stu-id="51a54-149">Query types, which represent data that can be read from the database but not updated, have been renamed to [keyless entity types](xref:core/modeling/keyless-entity-types).</span></span> <span data-ttu-id="51a54-150">Como son una excelente opción para asignar vistas de bases de datos en la mayoría de los escenarios, EF Core ahora crea automáticamente tipos de entidades sin clave cuando se invierten las vistas de bases de datos de ingeniería.</span><span class="sxs-lookup"><span data-stu-id="51a54-150">As they are an excellent fit for mapping database views in most scenarios, EF Core now automatically creates keyless entity types when reverse engineering database views.</span></span>

<span data-ttu-id="51a54-151">Por ejemplo, con la [herramienta de línea de comandos dotnet ef](xref:core/miscellaneous/cli/dotnet), puede escribir:</span><span class="sxs-lookup"><span data-stu-id="51a54-151">For example, using the [dotnet ef command-line tool](xref:core/miscellaneous/cli/dotnet) you can type:</span></span>

``` console
dotnet ef dbcontext scaffold "Server=(localdb)\mssqllocaldb;Database=Blogging;Trusted_Connection=True;" Microsoft.EntityFrameworkCore.SqlServer
```

<span data-ttu-id="51a54-152">Y la herramienta ahora anulará automáticamente los tipos de scaffold para vistas y tablas sin claves:</span><span class="sxs-lookup"><span data-stu-id="51a54-152">And the tool will now automatically scaffold types for views and tables without keys:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
  modelBuilder.Entity<Names>(entity =>
  {
    entity.HasNoKey();
    entity.ToView("Names");
  });

  modelBuilder.Entity<Things>(entity =>
  {
    entity.HasNoKey();
  });
}
```

## <a name="dependent-entities-sharing-the-table-with-the-principal-are-now-optional"></a><span data-ttu-id="51a54-153">Ahora, las entidades dependientes que comparten la tabla con la entidad de seguridad son opcionales</span><span class="sxs-lookup"><span data-stu-id="51a54-153">Dependent entities sharing the table with the principal are now optional</span></span>

<span data-ttu-id="51a54-154">A partir de la versión EF Core 3.0, si `OrderDetails` pertenece a `Order` o está asignado a la misma tabla explícitamente, será posible agregar `Order` sin `OrderDetails`; todas las propiedades `OrderDetails`, salvo la clave principal, se asignarán a columnas que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="51a54-154">Starting with EF Core 3.0, if `OrderDetails` is owned by `Order` or explicitly mapped to the same table, it will be possible to add an `Order` without an `OrderDetails` and all of the `OrderDetails` properties, except the primary key will be mapped to nullable columns.</span></span>

<span data-ttu-id="51a54-155">Al realizar consultas, EF Core establecerá `OrderDetails` en `null` si ninguna de las propiedades necesarias tiene un valor, o bien no tiene las propiedades necesarias más allá de la clave principal y todas las propiedades son `null`.</span><span class="sxs-lookup"><span data-stu-id="51a54-155">When querying, EF Core will set `OrderDetails` to `null` if any of its required properties doesn't have a value, or if it has no required properties besides the primary key and all properties are `null`.</span></span>

``` csharp
public class Order
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public OrderDetails Details { get; set; }
}

[Owned]
public class OrderDetails
{
    public int Id { get; set; }
    public string ShippingAddress { get; set; }
}
```

## <a name="ef-63-on-net-core"></a><span data-ttu-id="51a54-156">EF 6.3 en .NET Core</span><span class="sxs-lookup"><span data-stu-id="51a54-156">EF 6.3 on .NET Core</span></span>

<span data-ttu-id="51a54-157">Esta no es realmente una característica de EF Core 3.0, pero creemos que es importante para muchos de nuestros clientes actuales.</span><span class="sxs-lookup"><span data-stu-id="51a54-157">This isn't really an EF Core 3.0 feature, but we think it is important to many of our current customers.</span></span> 

<span data-ttu-id="51a54-158">Entendemos que muchas aplicaciones existentes utilizan versiones anteriores de EF y que portarlas a EF Core solo para aprovechar las ventajas de .NET Core puede requerir un esfuerzo considerable.</span><span class="sxs-lookup"><span data-stu-id="51a54-158">We understand that many existing applications use previous versions of EF, and that porting them to EF Core only to take advantage of .NET Core can require a significant effort.</span></span> <span data-ttu-id="51a54-159">Por ese motivo, decidimos portar a la versión más reciente de EF 6 para que se ejecute en .NET Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="51a54-159">For that reason, we decided to port the newest version of EF 6 to run on .NET Core 3.0.</span></span> 

<span data-ttu-id="51a54-160">Para más detalles, consulte [Novedades de EF 6](xref:ef6/what-is-new/index).</span><span class="sxs-lookup"><span data-stu-id="51a54-160">For more details, see [what's new in EF 6](xref:ef6/what-is-new/index).</span></span>

## <a name="postponed-features"></a><span data-ttu-id="51a54-161">Características pospuestas</span><span class="sxs-lookup"><span data-stu-id="51a54-161">Postponed features</span></span>

<span data-ttu-id="51a54-162">Algunas características planeadas originalmente para EF Core 3.0 se pospusieron para versiones futuras:</span><span class="sxs-lookup"><span data-stu-id="51a54-162">Some features originally planned for EF Core 3.0 were postponed to future releases:</span></span>

- <span data-ttu-id="51a54-163">Capacidad de omitir partes de un modelo en migraciones, con seguimiento realizado a través del problema [nº 2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span><span class="sxs-lookup"><span data-stu-id="51a54-163">Ability to ignore parts of a model in migrations, tracked as [#2725](https://github.com/aspnet/EntityFrameworkCore/issues/2725).</span></span>
- <span data-ttu-id="51a54-164">Entidades contenedoras de propiedades, de las que se realiza un seguimiento a través de dos problemas independientes: [nº 9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) sobre las entidades de tipo compartido y [nº 13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) sobre la compatibilidad con la asignación de propiedades indizadas.</span><span class="sxs-lookup"><span data-stu-id="51a54-164">Property bag entities, tracked as two separate issues: [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914) about shared-type entities and [#13610](https://github.com/aspnet/EntityFrameworkCore/issues/13610) about indexed property mapping support.</span></span>
