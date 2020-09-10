---
title: 'Trabajo con DbContext: EF6'
description: Trabajo con DbContext en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: b0e6bddc-8a87-4d51-b1cb-7756df938c23
uid: ef6/fundamentals/working-with-dbcontext
ms.openlocfilehash: 7845d401cb0b8910cbfbba80eca2fd098c051b7d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618212"
---
# <a name="working-with-dbcontext"></a><span data-ttu-id="9af19-103">Trabajar con DbContext</span><span class="sxs-lookup"><span data-stu-id="9af19-103">Working with DbContext</span></span>

<span data-ttu-id="9af19-104">Para usar Entity Framework para consultar, insertar, actualizar y eliminar datos mediante objetos .NET, primero debe [crear un modelo](xref:ef6/modeling/index) que asigne las entidades y relaciones que se definen en el modelo a las tablas de una base de datos.</span><span class="sxs-lookup"><span data-stu-id="9af19-104">In order to use Entity Framework to query, insert, update, and delete data using .NET objects, you first need to [Create a Model](xref:ef6/modeling/index) which maps the entities and relationships that are defined in your model to tables in a database.</span></span>

<span data-ttu-id="9af19-105">Una vez que tiene un modelo, la clase principal con la que interactúa la aplicación es `System.Data.Entity.DbContext` (a menudo se conoce como clase de contexto).</span><span class="sxs-lookup"><span data-stu-id="9af19-105">Once you have a model, the primary class your application interacts with is `System.Data.Entity.DbContext` (often referred to as the context class).</span></span> <span data-ttu-id="9af19-106">Puede usar un DbContext asociado a un modelo para:</span><span class="sxs-lookup"><span data-stu-id="9af19-106">You can use a DbContext associated to a model to:</span></span>
- <span data-ttu-id="9af19-107">Escribir y ejecutar consultas</span><span class="sxs-lookup"><span data-stu-id="9af19-107">Write and execute queries</span></span>   
- <span data-ttu-id="9af19-108">Materializar los resultados de la consulta como objetos entidad</span><span class="sxs-lookup"><span data-stu-id="9af19-108">Materialize query results as entity objects</span></span>
- <span data-ttu-id="9af19-109">Realizar un seguimiento de los cambios que se realizan en esos objetos</span><span class="sxs-lookup"><span data-stu-id="9af19-109">Track changes that are made to those objects</span></span>
- <span data-ttu-id="9af19-110">Volver a guardar los cambios de objetos en la base de datos</span><span class="sxs-lookup"><span data-stu-id="9af19-110">Persist object changes back on the database</span></span>
- <span data-ttu-id="9af19-111">Enlazar objetos en memoria a controles de interfaz de usuario</span><span class="sxs-lookup"><span data-stu-id="9af19-111">Bind objects in memory to UI controls</span></span>

<span data-ttu-id="9af19-112">En esta página se proporcionan instrucciones sobre cómo administrar la clase de contexto.</span><span class="sxs-lookup"><span data-stu-id="9af19-112">This page gives some guidance on how to manage the context class.</span></span>  

## <a name="defining-a-dbcontext-derived-class"></a><span data-ttu-id="9af19-113">Definir una clase derivada de DbContext</span><span class="sxs-lookup"><span data-stu-id="9af19-113">Defining a DbContext derived class</span></span>  

<span data-ttu-id="9af19-114">La manera recomendada de trabajar con el contexto es definir una clase que derive de DbContext y exponga propiedades DbSet que representen colecciones de las entidades especificadas en el contexto.</span><span class="sxs-lookup"><span data-stu-id="9af19-114">The recommended way to work with context is to define a class that derives from DbContext and exposes DbSet properties that represent collections of the specified entities in the context.</span></span> <span data-ttu-id="9af19-115">Si está trabajando con el diseñador de EF, el contexto se generará automáticamente.</span><span class="sxs-lookup"><span data-stu-id="9af19-115">If you are working with the EF Designer, the context will be generated for you.</span></span> <span data-ttu-id="9af19-116">Si está trabajando con Code First, normalmente escribirá el contexto.</span><span class="sxs-lookup"><span data-stu-id="9af19-116">If you are working with Code First, you will typically write the context yourself.</span></span>  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

<span data-ttu-id="9af19-117">Una vez que tenga un contexto, debe consultar, agregar (mediante `Add` métodos o `Attach` ) o quitar (mediante `Remove` ) entidades en el contexto mediante estas propiedades.</span><span class="sxs-lookup"><span data-stu-id="9af19-117">Once you have a context, you would query for, add (using `Add` or `Attach` methods ) or remove (using `Remove`) entities in the context through these properties.</span></span> <span data-ttu-id="9af19-118">El acceso a una `DbSet` propiedad en un objeto de contexto representa una consulta de inicio que devuelve todas las entidades del tipo especificado.</span><span class="sxs-lookup"><span data-stu-id="9af19-118">Accessing a `DbSet` property on a context object represent a starting query that returns all entities of the specified type.</span></span> <span data-ttu-id="9af19-119">Tenga en cuenta que, al tener acceso a una propiedad, no se ejecutará la consulta.</span><span class="sxs-lookup"><span data-stu-id="9af19-119">Note that just accessing a property will not execute the query.</span></span> <span data-ttu-id="9af19-120">Una consulta se ejecuta cuando:</span><span class="sxs-lookup"><span data-stu-id="9af19-120">A query is executed when:</span></span>  

- <span data-ttu-id="9af19-121">Se enumera mediante una instrucción `foreach` (C#) o `For Each` (Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="9af19-121">It is enumerated by a `foreach` (C#) or `For Each` (Visual Basic) statement.</span></span>  
- <span data-ttu-id="9af19-122">Se enumera mediante una operación de colección como `ToArray` , `ToDictionary` o `ToList` .</span><span class="sxs-lookup"><span data-stu-id="9af19-122">It is enumerated by a collection operation such as `ToArray`, `ToDictionary`, or `ToList`.</span></span>  
- <span data-ttu-id="9af19-123">Los operadores de LINQ como `First` o `Any` se especifican en la parte más externa de la consulta.</span><span class="sxs-lookup"><span data-stu-id="9af19-123">LINQ operators such as `First` or `Any` are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="9af19-124">Se llama a uno de los métodos siguientes: el `Load` método de extensión, `DbEntityEntry.Reload` ,  `Database.ExecuteSqlCommand` y `DbSet<T>.Find` , si una entidad con la clave especificada no se encuentra ya cargada en el contexto.</span><span class="sxs-lookup"><span data-stu-id="9af19-124">One of the following methods are called: the `Load` extension method, `DbEntityEntry.Reload`,  `Database.ExecuteSqlCommand`, and `DbSet<T>.Find`, if an entity with the specified key is not found already loaded in the context.</span></span>  

## <a name="lifetime"></a><span data-ttu-id="9af19-125">Período de duración</span><span class="sxs-lookup"><span data-stu-id="9af19-125">Lifetime</span></span>  

<span data-ttu-id="9af19-126">La duración del contexto comienza cuando se crea la instancia y finaliza cuando la instancia se desecha o se recolecta como elemento no utilizado.</span><span class="sxs-lookup"><span data-stu-id="9af19-126">The lifetime of the context begins when the instance is created and ends when the instance is either disposed or garbage-collected.</span></span> <span data-ttu-id="9af19-127">Use el **uso** de si desea que todos los recursos que controla el contexto se eliminen al final del bloque.</span><span class="sxs-lookup"><span data-stu-id="9af19-127">Use **using** if you want all the resources that the context controls to be disposed at the end of the block.</span></span> <span data-ttu-id="9af19-128">Cuando se usa **con**, el compilador crea automáticamente un bloque try/finally y llama a Dispose en el bloque **Finally** .</span><span class="sxs-lookup"><span data-stu-id="9af19-128">When you use **using**, the compiler automatically creates a try/finally block and calls dispose in the **finally** block.</span></span>  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

<span data-ttu-id="9af19-129">Estas son algunas directrices generales a la hora de decidir la duración del contexto:</span><span class="sxs-lookup"><span data-stu-id="9af19-129">Here are some general guidelines when deciding on the lifetime of the context:</span></span>  

- <span data-ttu-id="9af19-130">Al trabajar con aplicaciones Web, use una instancia de contexto por solicitud.</span><span class="sxs-lookup"><span data-stu-id="9af19-130">When working with Web applications, use a context instance per request.</span></span>  
- <span data-ttu-id="9af19-131">Al trabajar con Windows Presentation Foundation (WPF) o Windows Forms, utilice una instancia de contexto por formulario.</span><span class="sxs-lookup"><span data-stu-id="9af19-131">When working with Windows Presentation Foundation (WPF) or Windows Forms, use a context instance per form.</span></span> <span data-ttu-id="9af19-132">Esto le permite usar la funcionalidad de seguimiento de cambios que proporciona el contexto.</span><span class="sxs-lookup"><span data-stu-id="9af19-132">This lets you use change-tracking functionality that context provides.</span></span>  
- <span data-ttu-id="9af19-133">Si la instancia de contexto la crea un contenedor de inserción de dependencias, suele ser responsabilidad del contenedor desechar el contexto.</span><span class="sxs-lookup"><span data-stu-id="9af19-133">If the context instance is created by a dependency injection container, it is usually the responsibility of the container to dispose the context.</span></span>
- <span data-ttu-id="9af19-134">Si el contexto se crea en el código de la aplicación, no olvide eliminar el contexto cuando ya no sea necesario.</span><span class="sxs-lookup"><span data-stu-id="9af19-134">If the context is created in application code, remember to dispose of the context when it is no longer required.</span></span>  
- <span data-ttu-id="9af19-135">Al trabajar con un contexto de ejecución prolongada, tenga en cuenta lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="9af19-135">When working with long-running context consider the following:</span></span>  
    - <span data-ttu-id="9af19-136">A medida que se cargan más objetos y sus referencias en la memoria, el consumo de memoria del contexto puede aumentar rápidamente.</span><span class="sxs-lookup"><span data-stu-id="9af19-136">As you load more objects and their references into memory, the memory consumption of the context may increase rapidly.</span></span> <span data-ttu-id="9af19-137">lo que puede ocasionar problemas de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="9af19-137">This may cause performance issues.</span></span>  
    - <span data-ttu-id="9af19-138">El contexto no es seguro para subprocesos, por lo que no debe compartirse entre varios subprocesos que realizan trabajo en él simultáneamente.</span><span class="sxs-lookup"><span data-stu-id="9af19-138">The context is not thread-safe, therefore it should not be shared across multiple threads doing work on it concurrently.</span></span>
    - <span data-ttu-id="9af19-139">Si una excepción hace que el contexto esté en un estado irrecuperable, toda la aplicación puede finalizar.</span><span class="sxs-lookup"><span data-stu-id="9af19-139">If an exception causes the context to be in an unrecoverable state, the whole application may terminate.</span></span>  
    - <span data-ttu-id="9af19-140">Las posibilidades de que haya problemas relacionados con la simultaneidad se incrementan a medida que aumenta la distancia entre el momento en que se consultan los datos y el momento en que se actualizan.</span><span class="sxs-lookup"><span data-stu-id="9af19-140">The chances of running into concurrency-related issues increase as the gap between the time when the data is queried and updated grows.</span></span>  

## <a name="connections"></a><span data-ttu-id="9af19-141">Conexiones</span><span class="sxs-lookup"><span data-stu-id="9af19-141">Connections</span></span>  

<span data-ttu-id="9af19-142">De forma predeterminada, el contexto administra las conexiones a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9af19-142">By default, the context manages connections to the database.</span></span> <span data-ttu-id="9af19-143">El contexto abre y cierra las conexiones según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="9af19-143">The context opens and closes connections as needed.</span></span> <span data-ttu-id="9af19-144">Por ejemplo, el contexto abre una conexión para ejecutar una consulta y, a continuación, cierra la conexión cuando se han procesado todos los conjuntos de resultados.</span><span class="sxs-lookup"><span data-stu-id="9af19-144">For example, the context opens a connection to execute a query, and then closes the connection when all the result sets have been processed.</span></span>  

<span data-ttu-id="9af19-145">Hay casos en los que se desea más control sobre el momento en que se abre y cierra la conexión.</span><span class="sxs-lookup"><span data-stu-id="9af19-145">There are cases when you want to have more control over when the connection opens and closes.</span></span> <span data-ttu-id="9af19-146">Por ejemplo, al trabajar con SQL Server Compact, a menudo se recomienda mantener una conexión abierta independiente con la base de datos mientras dure la aplicación para mejorar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="9af19-146">For example, when working with SQL Server Compact, it is often recommended to maintain a separate open connection to the database for the lifetime of the application to improve performance.</span></span> <span data-ttu-id="9af19-147">Puede administrar este proceso manualmente mediante la propiedad `Connection`.</span><span class="sxs-lookup"><span data-stu-id="9af19-147">You can manage this process manually by using the `Connection` property.</span></span>  
