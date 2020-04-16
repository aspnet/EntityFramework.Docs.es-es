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
# <a name="async-query-and-save"></a>Consulta asincrónica y guardar
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

EF6 introdujo compatibilidad con la consulta asincrónica y guardar mediante las [palabras clave async y await](https://msdn.microsoft.com/library/vstudio/hh191443.aspx) que se introdujeron en .NET 4.5. Aunque no todas las aplicaciones pueden beneficiarse de la asincronía, se puede utilizar para mejorar la capacidad de respuesta del cliente y la escalabilidad del servidor al controlar tareas de larga ejecución, de red o enlazadas a E/S.

## <a name="when-to-really-use-async"></a>Cuándo usar realmente async

El propósito de este tutorial es introducir los conceptos asincrónicos de una manera que facilite la observación de la diferencia entre la ejecución asincrónica y sincrónica del programa. Este tutorial no está pensado para ilustrar ninguno de los escenarios clave donde la programación asincrónica proporciona ventajas.

La programación asincrónica se centra principalmente en liberar el subproceso administrado actual (hilo que ejecuta código .NET) para realizar otro trabajo mientras espera una operación que no requiere ningún tiempo de proceso de un subproceso administrado. Por ejemplo, mientras el motor de base de datos está procesando una consulta, el código .NET no puede realizar nada.

En las aplicaciones cliente (WinForms, WPF, etc.) el subproceso actual se puede usar para mantener la interfaz de usuario sensible mientras se realiza la operación asincrónica. En las aplicaciones de servidor (ASP.NET etc.) el subproceso se puede utilizar para procesar otras solicitudes entrantes - esto puede reducir el uso de memoria o aumentar el rendimiento del servidor.

En la mayoría de las aplicaciones que utilizan asincrónico no tendrá beneficios notables e incluso podría ser perjudicial. Use pruebas, generación de perfiles y sentido común para medir el impacto de async en su escenario concreto antes de comprometerse con él.

Estos son algunos recursos más para aprender sobre async:

-   [Visión general de Brandon Bray de async/await en .NET 4.5](https://devblogs.microsoft.com/dotnet/async-in-4-5-worth-the-await/)
-   [Páginas](https://msdn.microsoft.com/library/hh191443.aspx) de programación asincrónica en MSDN Library
-   [Cómo crear ASP.NET aplicaciones web mediante Async](https://channel9.msdn.com/events/teched/northamerica/2013/dev-b337) (incluye una demostración del mayor rendimiento del servidor)

## <a name="create-the-model"></a>Creación del modelo

Usaremos el flujo de trabajo de [Code First](~/ef6/modeling/code-first/workflows/new-database.md) para crear nuestro modelo y generar la base de datos, sin embargo, la funcionalidad asincrónica funcionará con todos los modelos de EF, incluidos los creados con EF Designer.

-   Cree una aplicación de consola y llámela **AsyncDemo**
-   Agregue el paquete NuGet EntityFramework
    -   En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **AsyncDemo**
    -   Seleccione **Administrar paquetes NuGet...**
    -   En el cuadro de diálogo Administrar paquetes NuGet, seleccione la pestaña **Online** y elija el paquete **EntityFramework**
    -   Haga clic en **Instalar**
-   Agregue una clase **Model.cs** con la siguiente implementación

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

Ahora que tenemos un modelo DE EF, vamos a escribir código que lo usa para realizar cierto acceso a datos.

-   Reemplace el contenido de **Program.cs** por el código siguiente

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

Este código llama al método **PerformDatabaseOperations** que guarda un nuevo **blog** en la base de datos y, a continuación, recupera todos los **blogs** de la base de datos y los imprime en la **consola.** Después de esto, el programa escribe una cita del día en la **consola.**

Dado que el código es sincrónico, podemos observar el siguiente flujo de ejecución cuando ejecutamos el programa:

1.  **SaveChanges** comienza a insertar el nuevo blog en la base **de** datos
2.  **SaveChanges** completa
3.  La consulta para todos los **blogs** se envía a la base de datos
4.  Las devoluciones y los resultados de las consultas se escriben en **la consola**
5.  La cita del día está escrita en **Consola**

![Salida de sincronización](~/ef6/media/syncoutput.png) 

 

## <a name="making-it-asynchronous"></a>Haciéndolo asíncrono

Ahora que tenemos nuestro programa en funcionamiento, podemos comenzar a hacer uso de las nuevas palabras clave async y await. Hemos realizado los siguientes cambios en Program.cs

1.  Línea 2: la instrucción using para el espacio de nombres **System.Data.Entity** nos da acceso a los métodos de extensión asincrónica de EF.
2.  Línea 4: la instrucción using para el espacio de nombres **System.Threading.Tasks** nos permite usar el tipo **Task.**
3.  Línea 12 & 18: Estamos capturando como tarea que supervisa el progreso de **PerformSomeDatabaseOperations** (línea 12) y, a continuación, bloquealando la ejecución del programa para que esta tarea se complete una vez que se haya realizado todo el trabajo para el programa (línea 18).
4.  Línea 25: hemos actualizado **PerformSomeDatabaseOperations** para que se marque como **asincrónico** y devolver un **task**.
5.  Línea 35: Ahora estamos llamando a la versión asincrónica de SaveChanges y esperando su finalización.
6.  Línea 42: Ahora estamos llamando a la versión asincrónica de ToList y esperando el resultado.

Para obtener una lista completa de los métodos de extensión disponibles en el espacio de nombres System.Data.Entity, consulte la clase QueryableExtensions. *También deberá agregar "using System.Data.Entity" a las instrucciones using.*

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

Ahora que el código es asincrónico, podemos observar un flujo de ejecución diferente cuando ejecutamos el programa:

1. **SaveChanges** comienza a insertar el nuevo blog en la base **de** datos  
    *Una vez que el comando se envía a la base de datos no se necesita más tiempo de proceso en el subproceso administrado actual. El **PerformDatabaseOperations** método devuelve (aunque no ha terminado de ejecutarse) y el flujo de programa en el Main método continúa.*
2. **La cita del día está escrita en Consola**  
    *Puesto que no hay más trabajo que hacer en el Main método, el subproceso administrado se bloquea en la llamada Wait hasta que se complete la operación de base de datos. Una vez que se completa, se ejecutará el resto de **performDatabaseOperations.***
3.  **SaveChanges** completa  
4.  La consulta para todos los **blogs** se envía a la base de datos  
    *De nuevo, el subproceso administrado es libre de realizar otro trabajo mientras se procesa la consulta en la base de datos. Puesto que se ha completado el resto de la ejecución, el subproceso simplemente se detendrá en la llamada Wait aunque.*
5.  Las devoluciones y los resultados de las consultas se escriben en **la consola**  

![Salida asincrónica](~/ef6/media/asyncoutput.png) 

 

## <a name="the-takeaway"></a>La para llevar

Ahora vimos lo fácil que es hacer uso de los métodos asincrónicos de EF. Aunque las ventajas de async pueden no ser muy evidentes con una aplicación de consola simple, estas mismas estrategias se pueden aplicar en situaciones en las que las actividades de larga ejecución o enlazadas a la red podrían bloquear la aplicación o hacer que un gran número de subprocesos aumente la huella de memoria.
