---
title: Consulta asincrónica y Save-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: 0642dc13e7aa3906fa1495031c62701fc16f0192
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181845"
---
# <a name="async-query-and-save"></a><span data-ttu-id="d01fb-102">Consulta asincrónica y guardar</span><span class="sxs-lookup"><span data-stu-id="d01fb-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="d01fb-103">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="d01fb-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="d01fb-104">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="d01fb-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="d01fb-105">EF6 presentó la compatibilidad con la consulta asincrónica y la guarda con las [palabras clave Async y Await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) que se introdujeron en .net 4,5.</span><span class="sxs-lookup"><span data-stu-id="d01fb-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="d01fb-106">Aunque no todas las aplicaciones pueden beneficiarse de asincronía, se puede usar para mejorar la capacidad de respuesta del cliente y la escalabilidad del servidor al administrar tareas de ejecución prolongada, de red o enlazadas a e/s.</span><span class="sxs-lookup"><span data-stu-id="d01fb-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="d01fb-107">Cuándo usar realmente Async</span><span class="sxs-lookup"><span data-stu-id="d01fb-107">When to really use async</span></span>

<span data-ttu-id="d01fb-108">El propósito de este tutorial es presentar los conceptos de Async de una manera que facilite la observación de la diferencia entre la ejecución de programas asincrónica y sincrónica.</span><span class="sxs-lookup"><span data-stu-id="d01fb-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="d01fb-109">Este tutorial no pretende ilustrar ninguno de los escenarios clave donde la programación asincrónica proporciona ventajas.</span><span class="sxs-lookup"><span data-stu-id="d01fb-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="d01fb-110">La programación asincrónica se centra principalmente en liberar el subproceso administrado actual (subproceso que ejecuta código .NET) para realizar otro trabajo mientras espera una operación que no requiere tiempo de proceso de un subproceso administrado.</span><span class="sxs-lookup"><span data-stu-id="d01fb-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="d01fb-111">Por ejemplo, mientras el motor de base de datos está procesando una consulta, no hay nada que hacer por código .NET.</span><span class="sxs-lookup"><span data-stu-id="d01fb-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="d01fb-112">En las aplicaciones cliente (WinForms, WPF, etc.), el subproceso actual se puede usar para mantener la capacidad de respuesta de la interfaz de usuario mientras se realiza la operación asincrónica.</span><span class="sxs-lookup"><span data-stu-id="d01fb-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="d01fb-113">En las aplicaciones de servidor (ASP.NET etc.), el subproceso se puede usar para procesar otras solicitudes entrantes; esto puede reducir el uso de memoria o aumentar el rendimiento del servidor.</span><span class="sxs-lookup"><span data-stu-id="d01fb-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="d01fb-114">En la mayoría de las aplicaciones que usan Async no tendrá ventajas apreciables e incluso podría ser perjudicial.</span><span class="sxs-lookup"><span data-stu-id="d01fb-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="d01fb-115">Use las pruebas, la generación de perfiles y el sentido común para medir el impacto de Async en su escenario concreto antes de confirmarlo.</span><span class="sxs-lookup"><span data-stu-id="d01fb-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="d01fb-116">Estos son algunos recursos adicionales para obtener información sobre Async:</span><span class="sxs-lookup"><span data-stu-id="d01fb-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="d01fb-117">Información general de Brandon Bray de Async/Await en .NET 4,5</span><span class="sxs-lookup"><span data-stu-id="d01fb-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://blogs.msdn.com/b/dotnet/archive/2012/04/03/async-in-4-5-worth-the-await.aspx)
-   <span data-ttu-id="d01fb-118">Páginas de [programación asincrónicas](https://msdn.microsoft.com/library/hh191443.aspx) en MSDN Library</span><span class="sxs-lookup"><span data-stu-id="d01fb-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="d01fb-119">[Cómo crear aplicaciones Web de ASP.net mediante Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (incluye una demostración del aumento del rendimiento del servidor)</span><span class="sxs-lookup"><span data-stu-id="d01fb-119">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="d01fb-120">Creación del modelo</span><span class="sxs-lookup"><span data-stu-id="d01fb-120">Create the model</span></span>

<span data-ttu-id="d01fb-121">Usaremos el flujo de [trabajo Code First](~/ef6/modeling/code-first/workflows/new-database.md) para crear nuestro modelo y generar la base de datos; sin embargo, la funcionalidad asincrónica funcionará con todos los modelos EF, incluidos los creados con el diseñador EF.</span><span class="sxs-lookup"><span data-stu-id="d01fb-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="d01fb-122">Crear una aplicación de consola y llamarla **AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="d01fb-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="d01fb-123">Adición del paquete NuGet de EntityFramework</span><span class="sxs-lookup"><span data-stu-id="d01fb-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="d01fb-124">En Explorador de soluciones, haga clic con el botón derecho en el proyecto **AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="d01fb-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="d01fb-125">Seleccione **administrar paquetes NuGet..** .</span><span class="sxs-lookup"><span data-stu-id="d01fb-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="d01fb-126">En el cuadro de diálogo administrar paquetes NuGet, seleccione la pestaña **en línea** y elija el paquete **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="d01fb-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="d01fb-127">Haga clic en **instalar**</span><span class="sxs-lookup"><span data-stu-id="d01fb-127">Click **Install**</span></span>
-   <span data-ttu-id="d01fb-128">Agregue una clase **Model.CS** con la siguiente implementación</span><span class="sxs-lookup"><span data-stu-id="d01fb-128">Add a **Model.cs** class with the following implementation</span></span>

``` csharp
    using System.Collections.Generic;
    using System.Data.Entity;

    namespace AsyncDemo
    {
        public class BloggingContext : DbContext
        {
            public DbSet<Blog> Blogs { get; set; }
            public DbSet<Post> Posts { get; set; }
        }

        public class Blog
        {
            public int BlogId { get; set; }
            public string Name { get; set; }

            public virtual List<Post> Posts { get; set; }
        }

        public class Post
        {
            public int PostId { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }

            public int BlogId { get; set; }
            public virtual Blog Blog { get; set; }
        }
    }
```

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="d01fb-129">Crear un programa sincrónico</span><span class="sxs-lookup"><span data-stu-id="d01fb-129">Create a synchronous program</span></span>

<span data-ttu-id="d01fb-130">Ahora que tenemos un modelo EF, vamos a escribir código que lo usa para realizar algún acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="d01fb-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="d01fb-131">Reemplace el contenido de **Program.CS** por el código siguiente.</span><span class="sxs-lookup"><span data-stu-id="d01fb-131">Replace the contents of **Program.cs** with the following code</span></span>

``` csharp
    using System;
    using System.Linq;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static void PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    db.SaveChanges();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = (from b in db.Blogs
                                orderby b.Name
                                select b).ToList();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" " + blog.Name);
                    }
                }
            }
        }
    }
```

<span data-ttu-id="d01fb-132">Este código llama al método **PerformDatabaseOperations** , que guarda un nuevo **blog** en la base de datos y, a continuación, recupera todos los **blogs** de la base de datos y los imprime en la **consola**.</span><span class="sxs-lookup"><span data-stu-id="d01fb-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="d01fb-133">Después, el programa escribe una comilla del día en la **consola**.</span><span class="sxs-lookup"><span data-stu-id="d01fb-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="d01fb-134">Dado que el código es sincrónico, podemos observar el siguiente flujo de ejecución al ejecutar el programa:</span><span class="sxs-lookup"><span data-stu-id="d01fb-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="d01fb-135">**SaveChanges** comienza a introducir el nuevo **blog** en la base de datos</span><span class="sxs-lookup"><span data-stu-id="d01fb-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="d01fb-136">**SaveChanges** completado</span><span class="sxs-lookup"><span data-stu-id="d01fb-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="d01fb-137">La consulta de todos los **blogs** se envía a la base de datos</span><span class="sxs-lookup"><span data-stu-id="d01fb-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="d01fb-138">Las devoluciones de consultas y los resultados se escriben en la **consola**</span><span class="sxs-lookup"><span data-stu-id="d01fb-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="d01fb-139">La oferta del día se escribe en la **consola**</span><span class="sxs-lookup"><span data-stu-id="d01fb-139">Quote of the day is written to **Console**</span></span>

![Salida de sincronización](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="d01fb-141">Convertirlo en asincrónico</span><span class="sxs-lookup"><span data-stu-id="d01fb-141">Making it asynchronous</span></span>

<span data-ttu-id="d01fb-142">Ahora que tenemos nuestro programa en funcionamiento, podemos empezar a usar las nuevas palabras clave Async y Await.</span><span class="sxs-lookup"><span data-stu-id="d01fb-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="d01fb-143">Hemos realizado los siguientes cambios en Program.cs</span><span class="sxs-lookup"><span data-stu-id="d01fb-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="d01fb-144">Línea 2: La instrucción using del espacio de nombres **System. Data. Entity** nos permite acceder a los métodos de extensión de EF Async.</span><span class="sxs-lookup"><span data-stu-id="d01fb-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="d01fb-145">Línea 4: La instrucción using del espacio de nombres **System. Threading. Tasks** nos permite usar el tipo de **tarea** .</span><span class="sxs-lookup"><span data-stu-id="d01fb-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="d01fb-146">Línea 12 & 18: Estamos capturando como tarea que supervisa el progreso de **PerformSomeDatabaseOperations** (línea 12) y, después, bloqueando la ejecución del programa para que esta tarea se complete una vez que todo el trabajo para el programa se haya realizado (línea 18).</span><span class="sxs-lookup"><span data-stu-id="d01fb-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="d01fb-147">Línea 25: Hemos actualizado **PerformSomeDatabaseOperations** para que se marque como **Async** y devuelva una **tarea**.</span><span class="sxs-lookup"><span data-stu-id="d01fb-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="d01fb-148">Línea 35: Ahora se llama a la versión asincrónica de SaveChanges y se espera su finalización.</span><span class="sxs-lookup"><span data-stu-id="d01fb-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="d01fb-149">Línea 42: Ahora se llama a la versión asincrónica de ToList y se espera en el resultado.</span><span class="sxs-lookup"><span data-stu-id="d01fb-149">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="d01fb-150">Para obtener una lista completa de los métodos de extensión disponibles en el espacio de nombres System. Data. Entity, consulte la clase QueryableExtensions.</span><span class="sxs-lookup"><span data-stu-id="d01fb-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="d01fb-151">*También deberá agregar "Using System. Data. Entity" a las instrucciones using.*</span><span class="sxs-lookup"><span data-stu-id="d01fb-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

``` csharp
    using System;
    using System.Data.Entity;
    using System.Linq;
    using System.Threading.Tasks;

    namespace AsyncDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                var task = PerformDatabaseOperations();

                Console.WriteLine("Quote of the day");
                Console.WriteLine(" Don't worry about the world coming to an end today... ");
                Console.WriteLine(" It's already tomorrow in Australia.");

                task.Wait();

                Console.WriteLine();
                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

            public static async Task PerformDatabaseOperations()
            {
                using (var db = new BloggingContext())
                {
                    // Create a new blog and save it
                    db.Blogs.Add(new Blog
                    {
                        Name = "Test Blog #" + (db.Blogs.Count() + 1)
                    });
                    Console.WriteLine("Calling SaveChanges.");
                    await db.SaveChangesAsync();
                    Console.WriteLine("SaveChanges completed.");

                    // Query for all blogs ordered by name
                    Console.WriteLine("Executing query.");
                    var blogs = await (from b in db.Blogs
                                orderby b.Name
                                select b).ToListAsync();

                    // Write all blogs out to Console
                    Console.WriteLine("Query completed with following results:");
                    foreach (var blog in blogs)
                    {
                        Console.WriteLine(" - " + blog.Name);
                    }
                }
            }
        }
    }
```

<span data-ttu-id="d01fb-152">Ahora que el código es asincrónico, podemos observar otro flujo de ejecución cuando se ejecuta el programa:</span><span class="sxs-lookup"><span data-stu-id="d01fb-152">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="d01fb-153">**SaveChanges** comienza a introducir el nuevo **blog** en la base de datos</span><span class="sxs-lookup"><span data-stu-id="d01fb-153">**SaveChanges** begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="d01fb-154">*Once el comando se envía a la base de datos. no se necesita más tiempo de proceso en el subproceso administrado actual. El método **PerformDatabaseOperations** devuelve (aunque no ha terminado de ejecutarse) y continúa el flujo del programa en el método Main.*</span><span class="sxs-lookup"><span data-stu-id="d01fb-154">*Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="d01fb-155">**La oferta del día se escribe en la consola**</span><span class="sxs-lookup"><span data-stu-id="d01fb-155">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="d01fb-156">*Since no hay más trabajo que hacer en el método Main, el subproceso administrado se bloquea en la llamada de espera hasta que se completa la operación de base de datos. Una vez que se completa, se ejecutará el resto de la **PerformDatabaseOperations** .*</span><span class="sxs-lookup"><span data-stu-id="d01fb-156">*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="d01fb-157">**SaveChanges** completado</span><span class="sxs-lookup"><span data-stu-id="d01fb-157">**SaveChanges** completes</span></span>  
4.  <span data-ttu-id="d01fb-158">La consulta de todos los **blogs** se envía a la base de datos</span><span class="sxs-lookup"><span data-stu-id="d01fb-158">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="d01fb-159">*Again, el subproceso administrado es gratuito para realizar otro trabajo mientras la consulta se procesa en la base de datos. Dado que el resto de la ejecución se ha completado, el subproceso se detendrá simplemente en la llamada de espera.*</span><span class="sxs-lookup"><span data-stu-id="d01fb-159">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="d01fb-160">Las devoluciones de consultas y los resultados se escriben en la **consola**</span><span class="sxs-lookup"><span data-stu-id="d01fb-160">Query returns and results are written to **Console**</span></span>  

![Salida asincrónica](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="d01fb-162">La ventaja</span><span class="sxs-lookup"><span data-stu-id="d01fb-162">The takeaway</span></span>

<span data-ttu-id="d01fb-163">Ahora hemos visto lo fácil que es hacer uso de los métodos asincrónicos de EF.</span><span class="sxs-lookup"><span data-stu-id="d01fb-163">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="d01fb-164">Aunque es posible que las ventajas de Async no sean muy evidentes con una aplicación de consola sencilla, se pueden aplicar estas mismas estrategias en situaciones en las que las actividades de ejecución prolongada o enlazadas a la red podrían bloquear la aplicación, o provocar que un gran número de subprocesos aumentar la superficie de memoria.</span><span class="sxs-lookup"><span data-stu-id="d01fb-164">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
