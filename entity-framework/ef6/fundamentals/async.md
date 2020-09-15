---
title: Consulta asincrónica y Save-EF6
description: Consulta asincrónica y guardar en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/async
ms.openlocfilehash: 576da2d2eaa292e46a8d0a913851ba93e6f72587
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070814"
---
# <a name="async-query-and-save"></a>Consulta asincrónica y guardar
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

EF6 presentó la compatibilidad con la consulta asincrónica y la guarda con las [palabras clave Async y Await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) que se introdujeron en .net 4,5. Aunque no todas las aplicaciones pueden beneficiarse de asincronía, se puede usar para mejorar la capacidad de respuesta del cliente y la escalabilidad del servidor al administrar tareas de ejecución prolongada, de red o enlazadas a e/s.

## <a name="when-to-really-use-async"></a>Cuándo usar realmente Async

El propósito de este tutorial es presentar los conceptos de Async de una manera que facilite la observación de la diferencia entre la ejecución de programas asincrónica y sincrónica. Este tutorial no pretende ilustrar ninguno de los escenarios clave donde la programación asincrónica proporciona ventajas.

La programación asincrónica se centra principalmente en liberar el subproceso administrado actual (subproceso que ejecuta código .NET) para realizar otro trabajo mientras espera una operación que no requiere tiempo de proceso de un subproceso administrado. Por ejemplo, mientras el motor de base de datos está procesando una consulta, no hay nada que hacer por código .NET.

En las aplicaciones cliente (WinForms, WPF, etc.), el subproceso actual se puede usar para mantener la capacidad de respuesta de la interfaz de usuario mientras se realiza la operación asincrónica. En las aplicaciones de servidor (ASP.NET etc.), el subproceso se puede usar para procesar otras solicitudes entrantes; esto puede reducir el uso de memoria o aumentar el rendimiento del servidor.

En la mayoría de las aplicaciones que usan Async no tendrá ventajas apreciables e incluso podría ser perjudicial. Use las pruebas, la generación de perfiles y el sentido común para medir el impacto de Async en su escenario concreto antes de confirmarlo.

Estos son algunos recursos adicionales para obtener información sobre Async:

-   [Información general de Brandon Bray de Async/Await en .NET 4,5](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   Páginas de [programación asincrónicas](https://msdn.microsoft.com/library/hh191443.aspx) en MSDN Library
-   [Cómo crear aplicaciones Web de ASP.net mediante Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (incluye una demostración del aumento del rendimiento del servidor)

## <a name="create-the-model"></a>Creación del modelo

Usaremos el flujo de [trabajo Code First](xref:ef6/modeling/code-first/workflows/new-database) para crear nuestro modelo y generar la base de datos; sin embargo, la funcionalidad asincrónica funcionará con todos los modelos EF, incluidos los creados con el diseñador EF.

-   Crear una aplicación de consola y llamarla **AsyncDemo**
-   Adición del paquete NuGet de EntityFramework
    -   En Explorador de soluciones, haga clic con el botón derecho en el proyecto **AsyncDemo**
    -   Seleccione **administrar paquetes NuGet..** .
    -   En el cuadro de diálogo administrar paquetes NuGet, seleccione la pestaña **en línea** y elija el paquete **EntityFramework**
    -   Haz clic en **Instalar**
-   Agregue una clase **Model.CS** con la siguiente implementación

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

 

## <a name="create-a-synchronous-program"></a>Crear un programa sincrónico

Ahora que tenemos un modelo EF, vamos a escribir código que lo usa para realizar algún acceso a los datos.

-   Reemplace el contenido de **Program.CS** por el código siguiente.

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

Este código llama al método **PerformDatabaseOperations** , que guarda un nuevo **blog** en la base de datos y, a continuación, recupera todos los **blogs** de la base de datos y los imprime en la **consola**. Después, el programa escribe una comilla del día en la **consola**.

Dado que el código es sincrónico, podemos observar el siguiente flujo de ejecución al ejecutar el programa:

1.  **SaveChanges** comienza a introducir el nuevo **blog** en la base de datos
2.  **SaveChanges** completado
3.  La consulta de todos los **blogs** se envía a la base de datos
4.  Las devoluciones de consultas y los resultados se escriben en la **consola**
5.  La oferta del día se escribe en la **consola**

![Salida de sincronización](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>Convertirlo en asincrónico

Ahora que tenemos nuestro programa en funcionamiento, podemos empezar a usar las nuevas palabras clave Async y Await. Hemos realizado los siguientes cambios en Program.cs

1.  Línea 2: la instrucción using del espacio de nombres **System. Data. Entity** nos permite acceder a los métodos de extensión de EF Async.
2.  Línea 4: la instrucción using del espacio de nombres **System. Threading. Tasks** nos permite usar el tipo de **tarea** .
3.  Línea 12 & 18: estamos capturando como tarea que supervisa el progreso de **PerformSomeDatabaseOperations** (línea 12) y, después, bloqueando la ejecución del programa para que esta tarea se complete una vez que todo el trabajo para el programa se haya realizado (línea 18).
4.  Línea 25: hemos actualizado **PerformSomeDatabaseOperations** para que se marque como **Async** y devuelva una **tarea**.
5.  Línea 35: ahora se llama a la versión asincrónica de SaveChanges y se espera su finalización.
6.  Línea 42: ahora se llama a la versión asincrónica de ToList y se espera en el resultado.

Para obtener una lista completa de los métodos de extensión disponibles en el espacio de nombres System. Data. Entity, consulte la clase QueryableExtensions. *También deberá agregar "Using System. Data. Entity" a las instrucciones using.*

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

Ahora que el código es asincrónico, podemos observar otro flujo de ejecución cuando se ejecuta el programa:

1. **SaveChanges** comienza a introducir el nuevo **blog** en la base de datos  
    *Una vez que el comando se envía a la base de datos, no se necesita más tiempo de proceso en el subproceso administrado actual. El método **PerformDatabaseOperations** devuelve (aunque no ha terminado de ejecutarse) y continúa el flujo del programa en el método Main.*
2. **La oferta del día se escribe en la consola**  
    *Dado que no hay más trabajo que hacer en el método Main, el subproceso administrado se bloquea en la llamada de espera hasta que se completa la operación de base de datos. Una vez que se completa, se ejecutará el resto de **PerformDatabaseOperations** .*
3.  **SaveChanges** completado  
4.  La consulta de todos los **blogs** se envía a la base de datos  
    *De nuevo, el subproceso administrado es gratuito para realizar otro trabajo mientras la consulta se procesa en la base de datos. Dado que el resto de la ejecución se ha completado, el subproceso se detendrá simplemente en la llamada de espera.*
5.  Las devoluciones de consultas y los resultados se escriben en la **consola**  

![Salida asincrónica](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>La ventaja

Ahora hemos visto lo fácil que es hacer uso de los métodos asincrónicos de EF. Aunque es posible que las ventajas de Async no sean muy evidentes con una aplicación de consola simple, se pueden aplicar estas mismas estrategias en situaciones en las que las actividades de ejecución prolongada o enlazadas a la red podrían bloquear la aplicación, o hacer que un gran número de subprocesos aumente la superficie de memoria.
