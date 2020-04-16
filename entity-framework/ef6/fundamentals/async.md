---
title: Consulta asincrónica y guardar - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d56e6f1d-4bd1-4b50-9558-9a30e04a8ec3
ms.openlocfilehash: a001df726b7cd3342fcca566c1373f452811d07d
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434245"
---
# <a name="async-query-and-save"></a><span data-ttu-id="7534f-102">Consulta asincrónica y guardar</span><span class="sxs-lookup"><span data-stu-id="7534f-102">Async query and save</span></span>
> [!NOTE]
> <span data-ttu-id="7534f-103">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="7534f-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="7534f-104">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="7534f-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="7534f-105">EF6 introdujo compatibilidad con la consulta asincrónica y guardar mediante las [palabras clave async y await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) que se introdujeron en .NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="7534f-105">EF6 introduced support for asynchronous query and save using the [async and await keywords](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) that were introduced in .NET 4.5.</span></span> <span data-ttu-id="7534f-106">Aunque no todas las aplicaciones pueden beneficiarse de la asincronía, se puede utilizar para mejorar la capacidad de respuesta del cliente y la escalabilidad del servidor al controlar tareas de larga ejecución, de red o enlazadas a E/S.</span><span class="sxs-lookup"><span data-stu-id="7534f-106">While not all applications may benefit from asynchrony, it can be used to improve client responsiveness and server scalability when handling long-running, network or I/O-bound tasks.</span></span>

## <a name="when-to-really-use-async"></a><span data-ttu-id="7534f-107">Cuándo usar realmente async</span><span class="sxs-lookup"><span data-stu-id="7534f-107">When to really use async</span></span>

<span data-ttu-id="7534f-108">El propósito de este tutorial es introducir los conceptos asincrónicos de una manera que facilite la observación de la diferencia entre la ejecución asincrónica y sincrónica del programa.</span><span class="sxs-lookup"><span data-stu-id="7534f-108">The purpose of this walkthrough is to introduce the async concepts in a way that makes it easy to observe the difference between asynchronous and synchronous program execution.</span></span> <span data-ttu-id="7534f-109">Este tutorial no está pensado para ilustrar ninguno de los escenarios clave donde la programación asincrónica proporciona ventajas.</span><span class="sxs-lookup"><span data-stu-id="7534f-109">This walkthrough is not intended to illustrate any of the key scenarios where async programming provides benefits.</span></span>

<span data-ttu-id="7534f-110">La programación asincrónica se centra principalmente en liberar el subproceso administrado actual (hilo que ejecuta código .NET) para realizar otro trabajo mientras espera una operación que no requiere ningún tiempo de proceso de un subproceso administrado.</span><span class="sxs-lookup"><span data-stu-id="7534f-110">Async programming is primarily focused on freeing up the current managed thread (thread running .NET code) to do other work while it waits for an operation that does not require any compute time from a managed thread.</span></span> <span data-ttu-id="7534f-111">Por ejemplo, mientras el motor de base de datos está procesando una consulta, el código .NET no puede realizar nada.</span><span class="sxs-lookup"><span data-stu-id="7534f-111">For example, whilst the database engine is processing a query there is nothing to be done by .NET code.</span></span>

<span data-ttu-id="7534f-112">En las aplicaciones cliente (WinForms, WPF, etc.) el subproceso actual se puede usar para mantener la interfaz de usuario sensible mientras se realiza la operación asincrónica.</span><span class="sxs-lookup"><span data-stu-id="7534f-112">In client applications (WinForms, WPF, etc.) the current thread can be used to keep the UI responsive while the async operation is performed.</span></span> <span data-ttu-id="7534f-113">En las aplicaciones de servidor (ASP.NET etc.) el subproceso se puede utilizar para procesar otras solicitudes entrantes - esto puede reducir el uso de memoria o aumentar el rendimiento del servidor.</span><span class="sxs-lookup"><span data-stu-id="7534f-113">In server applications (ASP.NET etc.) the thread can be used to process other incoming requests - this can reduce memory usage and/or increase throughput of the server.</span></span>

<span data-ttu-id="7534f-114">En la mayoría de las aplicaciones que utilizan asincrónico no tendrá beneficios notables e incluso podría ser perjudicial.</span><span class="sxs-lookup"><span data-stu-id="7534f-114">In most applications using async will have no noticeable benefits and even could be detrimental.</span></span> <span data-ttu-id="7534f-115">Use pruebas, generación de perfiles y sentido común para medir el impacto de async en su escenario concreto antes de comprometerse con él.</span><span class="sxs-lookup"><span data-stu-id="7534f-115">Use tests, profiling and common sense to measure the impact of async in your particular scenario before committing to it.</span></span>

<span data-ttu-id="7534f-116">Estos son algunos recursos más para aprender sobre async:</span><span class="sxs-lookup"><span data-stu-id="7534f-116">Here are some more resources to learn about async:</span></span>

-   [<span data-ttu-id="7534f-117">Visión general de Brandon Bray de async/await en .NET 4.5</span><span class="sxs-lookup"><span data-stu-id="7534f-117">Brandon Bray’s overview of async/await in .NET 4.5</span></span>](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   <span data-ttu-id="7534f-118">[Páginas](https://msdn.microsoft.com/library/hh191443.aspx) de programación asincrónica en MSDN Library</span><span class="sxs-lookup"><span data-stu-id="7534f-118">[Asynchronous Programming](https://msdn.microsoft.com/library/hh191443.aspx) pages in the MSDN Library</span></span>
-   <span data-ttu-id="7534f-119">[Cómo crear ASP.NET aplicaciones web mediante Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (incluye una demostración del mayor rendimiento del servidor)</span><span class="sxs-lookup"><span data-stu-id="7534f-119">[How to Build ASP.NET Web Applications Using Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (includes a demo of increased server throughput)</span></span>

## <a name="create-the-model"></a><span data-ttu-id="7534f-120">Creación del modelo</span><span class="sxs-lookup"><span data-stu-id="7534f-120">Create the model</span></span>

<span data-ttu-id="7534f-121">Usaremos el flujo de trabajo de [Code First](~/ef6/modeling/code-first/workflows/new-database.md) para crear nuestro modelo y generar la base de datos, sin embargo, la funcionalidad asincrónica funcionará con todos los modelos de EF, incluidos los creados con EF Designer.</span><span class="sxs-lookup"><span data-stu-id="7534f-121">We’ll be using the [Code First workflow](~/ef6/modeling/code-first/workflows/new-database.md) to create our model and generate the database, however the asynchronous functionality will work with all EF models including those created with the EF Designer.</span></span>

-   <span data-ttu-id="7534f-122">Cree una aplicación de consola y llámela **AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="7534f-122">Create a Console Application and call it **AsyncDemo**</span></span>
-   <span data-ttu-id="7534f-123">Agregue el paquete NuGet EntityFramework</span><span class="sxs-lookup"><span data-stu-id="7534f-123">Add the EntityFramework NuGet package</span></span>
    -   <span data-ttu-id="7534f-124">En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **AsyncDemo**</span><span class="sxs-lookup"><span data-stu-id="7534f-124">In Solution Explorer, right-click on the **AsyncDemo** project</span></span>
    -   <span data-ttu-id="7534f-125">Seleccione **Administrar paquetes NuGet...**</span><span class="sxs-lookup"><span data-stu-id="7534f-125">Select **Manage NuGet Packages…**</span></span>
    -   <span data-ttu-id="7534f-126">En el cuadro de diálogo Administrar paquetes NuGet, seleccione la pestaña **Online** y elija el paquete **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="7534f-126">In the Manage NuGet Packages dialog, Select the **Online** tab and choose the **EntityFramework** package</span></span>
    -   <span data-ttu-id="7534f-127">Haga clic en **Instalar**</span><span class="sxs-lookup"><span data-stu-id="7534f-127">Click **Install**</span></span>
-   <span data-ttu-id="7534f-128">Agregue una clase **Model.cs** con la siguiente implementación</span><span class="sxs-lookup"><span data-stu-id="7534f-128">Add a **Model.cs** class with the following implementation</span></span>

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

 

## <a name="create-a-synchronous-program"></a><span data-ttu-id="7534f-129">Crear un programa sincrónico</span><span class="sxs-lookup"><span data-stu-id="7534f-129">Create a synchronous program</span></span>

<span data-ttu-id="7534f-130">Ahora que tenemos un modelo DE EF, vamos a escribir código que lo usa para realizar cierto acceso a datos.</span><span class="sxs-lookup"><span data-stu-id="7534f-130">Now that we have an EF model, let's write some code that uses it to perform some data access.</span></span>

-   <span data-ttu-id="7534f-131">Reemplace el contenido de **Program.cs** por el código siguiente</span><span class="sxs-lookup"><span data-stu-id="7534f-131">Replace the contents of **Program.cs** with the following code</span></span>

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

<span data-ttu-id="7534f-132">Este código llama al método **PerformDatabaseOperations** que guarda un nuevo **blog** en la base de datos y, a continuación, recupera todos los **blogs** de la base de datos y los imprime en la **consola.**</span><span class="sxs-lookup"><span data-stu-id="7534f-132">This code calls the **PerformDatabaseOperations** method which saves a new **Blog** to the database and then retrieves all **Blogs** from the database and prints them to the **Console**.</span></span> <span data-ttu-id="7534f-133">Después de esto, el programa escribe una cita del día en la **consola.**</span><span class="sxs-lookup"><span data-stu-id="7534f-133">After this, the program writes a quote of the day to the **Console**.</span></span>

<span data-ttu-id="7534f-134">Dado que el código es sincrónico, podemos observar el siguiente flujo de ejecución cuando ejecutamos el programa:</span><span class="sxs-lookup"><span data-stu-id="7534f-134">Since the code is synchronous, we can observe the following execution flow when we run the program:</span></span>

1.  <span data-ttu-id="7534f-135">**SaveChanges** comienza a insertar el nuevo blog en la base **de** datos</span><span class="sxs-lookup"><span data-stu-id="7534f-135">**SaveChanges** begins to push the new **Blog** to the database</span></span>
2.  <span data-ttu-id="7534f-136">**SaveChanges** completa</span><span class="sxs-lookup"><span data-stu-id="7534f-136">**SaveChanges** completes</span></span>
3.  <span data-ttu-id="7534f-137">La consulta para todos los **blogs** se envía a la base de datos</span><span class="sxs-lookup"><span data-stu-id="7534f-137">Query for all **Blogs** is sent to the database</span></span>
4.  <span data-ttu-id="7534f-138">Las devoluciones y los resultados de las consultas se escriben en **la consola**</span><span class="sxs-lookup"><span data-stu-id="7534f-138">Query returns and results are written to **Console**</span></span>
5.  <span data-ttu-id="7534f-139">La cita del día está escrita en **Consola**</span><span class="sxs-lookup"><span data-stu-id="7534f-139">Quote of the day is written to **Console**</span></span>

![Salida de sincronización](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a><span data-ttu-id="7534f-141">Haciéndolo asíncrono</span><span class="sxs-lookup"><span data-stu-id="7534f-141">Making it asynchronous</span></span>

<span data-ttu-id="7534f-142">Ahora que tenemos nuestro programa en funcionamiento, podemos comenzar a hacer uso de las nuevas palabras clave async y await.</span><span class="sxs-lookup"><span data-stu-id="7534f-142">Now that we have our program up and running, we can begin making use of the new async and await keywords.</span></span> <span data-ttu-id="7534f-143">Hemos realizado los siguientes cambios en Program.cs</span><span class="sxs-lookup"><span data-stu-id="7534f-143">We've made the following changes to Program.cs</span></span>

1.  <span data-ttu-id="7534f-144">Línea 2: la instrucción using para el espacio de nombres **System.Data.Entity** nos da acceso a los métodos de extensión asincrónica de EF.</span><span class="sxs-lookup"><span data-stu-id="7534f-144">Line 2: The using statement for the **System.Data.Entity** namespace gives us access to the EF async extension methods.</span></span>
2.  <span data-ttu-id="7534f-145">Línea 4: la instrucción using para el espacio de nombres **System.Threading.Tasks** nos permite usar el tipo **Task.**</span><span class="sxs-lookup"><span data-stu-id="7534f-145">Line 4: The using statement for the **System.Threading.Tasks** namespace allows us to use the **Task** type.</span></span>
3.  <span data-ttu-id="7534f-146">Línea 12 & 18: Estamos capturando como tarea que supervisa el progreso de **PerformSomeDatabaseOperations** (línea 12) y, a continuación, bloquealando la ejecución del programa para que esta tarea se complete una vez que se haya realizado todo el trabajo para el programa (línea 18).</span><span class="sxs-lookup"><span data-stu-id="7534f-146">Line 12 & 18: We are capturing as task that monitors the progress of **PerformSomeDatabaseOperations** (line 12) and then blocking program execution for this task to complete once all the work for the program is done (line 18).</span></span>
4.  <span data-ttu-id="7534f-147">Línea 25: hemos actualizado **PerformSomeDatabaseOperations** para que se marque como **asincrónico** y devolver un **task**.</span><span class="sxs-lookup"><span data-stu-id="7534f-147">Line 25: We've update **PerformSomeDatabaseOperations** to be marked as **async** and return a **Task**.</span></span>
5.  <span data-ttu-id="7534f-148">Línea 35: Ahora estamos llamando a la versión asincrónica de SaveChanges y esperando su finalización.</span><span class="sxs-lookup"><span data-stu-id="7534f-148">Line 35: We're now calling the Async version of SaveChanges and awaiting it's completion.</span></span>
6.  <span data-ttu-id="7534f-149">Línea 42: Ahora estamos llamando a la versión asincrónica de ToList y esperando el resultado.</span><span class="sxs-lookup"><span data-stu-id="7534f-149">Line 42: We're now calling the Async version of ToList and awaiting on the result.</span></span>

<span data-ttu-id="7534f-150">Para obtener una lista completa de los métodos de extensión disponibles en el espacio de nombres System.Data.Entity, consulte la clase QueryableExtensions.</span><span class="sxs-lookup"><span data-stu-id="7534f-150">For a comprehensive list of available extension methods in the System.Data.Entity namespace, refer to the QueryableExtensions class.</span></span> <span data-ttu-id="7534f-151">*También deberá agregar "using System.Data.Entity" a las instrucciones using.*</span><span class="sxs-lookup"><span data-stu-id="7534f-151">*You’ll also need to add “using System.Data.Entity” to your using statements.*</span></span>

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

<span data-ttu-id="7534f-152">Ahora que el código es asincrónico, podemos observar un flujo de ejecución diferente cuando ejecutamos el programa:</span><span class="sxs-lookup"><span data-stu-id="7534f-152">Now that the code is asynchronous, we can observe a different execution flow when we run the program:</span></span>

1. <span data-ttu-id="7534f-153">**SaveChanges** comienza a insertar el nuevo blog en la base **de** datos</span><span class="sxs-lookup"><span data-stu-id="7534f-153">**SaveChanges** begins to push the new **Blog** to the database</span></span>  
    <span data-ttu-id="7534f-154">*Una vez que el comando se envía a la base de datos no se necesita más tiempo de proceso en el subproceso administrado actual. El **PerformDatabaseOperations** método devuelve (aunque no ha terminado de ejecutarse) y el flujo de programa en el Main método continúa.*</span><span class="sxs-lookup"><span data-stu-id="7534f-154">*Once the command is sent to the database no more compute time is needed on the current managed thread. The **PerformDatabaseOperations** method returns (even though it hasn't finished executing) and program flow in the Main method continues.*</span></span>
2. <span data-ttu-id="7534f-155">**La cita del día está escrita en Consola**</span><span class="sxs-lookup"><span data-stu-id="7534f-155">**Quote of the day is written to Console**</span></span>  
    <span data-ttu-id="7534f-156">*Puesto que no hay más trabajo que hacer en el Main método, el subproceso administrado se bloquea en la llamada Wait hasta que se complete la operación de base de datos. Una vez que se completa, se ejecutará el resto de **performDatabaseOperations.***</span><span class="sxs-lookup"><span data-stu-id="7534f-156">*Since there is no more work to do in the Main method, the managed thread is blocked on the Wait call until the database operation completes. Once it completes, the remainder of our **PerformDatabaseOperations** will be executed.*</span></span>
3.  <span data-ttu-id="7534f-157">**SaveChanges** completa</span><span class="sxs-lookup"><span data-stu-id="7534f-157">**SaveChanges** completes</span></span>  
4.  <span data-ttu-id="7534f-158">La consulta para todos los **blogs** se envía a la base de datos</span><span class="sxs-lookup"><span data-stu-id="7534f-158">Query for all **Blogs** is sent to the database</span></span>  
    <span data-ttu-id="7534f-159">*De nuevo, el subproceso administrado es libre de realizar otro trabajo mientras se procesa la consulta en la base de datos. Puesto que se ha completado el resto de la ejecución, el subproceso simplemente se detendrá en la llamada Wait aunque.*</span><span class="sxs-lookup"><span data-stu-id="7534f-159">*Again, the managed thread is free to do other work while the query is processed in the database. Since all other execution has completed, the thread will just halt on the Wait call though.*</span></span>
5.  <span data-ttu-id="7534f-160">Las devoluciones y los resultados de las consultas se escriben en **la consola**</span><span class="sxs-lookup"><span data-stu-id="7534f-160">Query returns and results are written to **Console**</span></span>  

![Salida asincrónica](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a><span data-ttu-id="7534f-162">La para llevar</span><span class="sxs-lookup"><span data-stu-id="7534f-162">The takeaway</span></span>

<span data-ttu-id="7534f-163">Ahora vimos lo fácil que es hacer uso de los métodos asincrónicos de EF.</span><span class="sxs-lookup"><span data-stu-id="7534f-163">We now saw how easy it is to make use of EF’s asynchronous methods.</span></span> <span data-ttu-id="7534f-164">Aunque las ventajas de async pueden no ser muy evidentes con una aplicación de consola simple, estas mismas estrategias se pueden aplicar en situaciones en las que las actividades de larga ejecución o enlazadas a la red podrían bloquear la aplicación o hacer que un gran número de subprocesos aumente la huella de memoria.</span><span class="sxs-lookup"><span data-stu-id="7534f-164">Although the advantages of async may not be very apparent with a simple console app, these same strategies can be applied in situations where long-running or network-bound activities might otherwise block the application, or cause a large number of threads to increase the memory footprint.</span></span>
