---
title: 'Code First a una nueva base de datos: EF6'
description: Code First a una nueva base de datos en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/new-database
ms.openlocfilehash: 96d76162383149c045178954196e1294331918a8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070372"
---
# <a name="code-first-to-a-new-database"></a><span data-ttu-id="1c33e-103">Code First en una nueva base de datos</span><span class="sxs-lookup"><span data-stu-id="1c33e-103">Code First to a New Database</span></span>
<span data-ttu-id="1c33e-104">Este vídeo y el tutorial paso a paso proporcionan una introducción al desarrollo de Code First que tiene como destino una nueva base de datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-104">This video and step-by-step walkthrough provide an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="1c33e-105">Este escenario incluye establecer como destino una base de datos que no existe y Code First creará, o una base de datos vacía a la que Code First agregará nuevas tablas.</span><span class="sxs-lookup"><span data-stu-id="1c33e-105">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="1c33e-106">Code First permite definir el modelo mediante \# las clases C o VB.net.</span><span class="sxs-lookup"><span data-stu-id="1c33e-106">Code First allows you to define your model using C\# or VB.Net classes.</span></span> <span data-ttu-id="1c33e-107">Opcionalmente, se puede realizar una configuración adicional mediante atributos en las clases y propiedades o mediante una API fluida.</span><span class="sxs-lookup"><span data-stu-id="1c33e-107">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

## <a name="watch-the-video"></a><span data-ttu-id="1c33e-108">Visualización del vídeo</span><span class="sxs-lookup"><span data-stu-id="1c33e-108">Watch the video</span></span>
<span data-ttu-id="1c33e-109">Este vídeo proporciona una introducción al desarrollo de Code First orientado a una nueva base de datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-109">This video provides an introduction to Code First development targeting a new database.</span></span> <span data-ttu-id="1c33e-110">Este escenario incluye establecer como destino una base de datos que no existe y Code First creará, o una base de datos vacía a la que Code First agregará nuevas tablas.</span><span class="sxs-lookup"><span data-stu-id="1c33e-110">This scenario includes targeting a database that doesn’t exist and Code First will create, or an empty database that Code First will add new tables to.</span></span> <span data-ttu-id="1c33e-111">Code First permite definir el modelo mediante clases de C# o VB.Net.</span><span class="sxs-lookup"><span data-stu-id="1c33e-111">Code First allows you to define your model using C# or VB.Net classes.</span></span> <span data-ttu-id="1c33e-112">Opcionalmente, se puede realizar una configuración adicional mediante atributos en las clases y propiedades o mediante una API fluida.</span><span class="sxs-lookup"><span data-stu-id="1c33e-112">Additional configuration can optionally be performed using attributes on your classes and properties or by using a fluent API.</span></span>

<span data-ttu-id="1c33e-113">**Presentado por**: [Rowan Miller](https://romiller.com/)</span><span class="sxs-lookup"><span data-stu-id="1c33e-113">**Presented By**: [Rowan Miller](https://romiller.com/)</span></span>

<span data-ttu-id="1c33e-114">**Vídeo**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv)  |  [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span><span class="sxs-lookup"><span data-stu-id="1c33e-114">**Video**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv) | [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v) | [WMV (ZIP)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)</span></span>

## <a name="pre-requisites"></a><span data-ttu-id="1c33e-115">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="1c33e-115">Pre-Requisites</span></span>

<span data-ttu-id="1c33e-116">Debe tener instalado al menos Visual Studio 2010 o Visual Studio 2012 para completar este tutorial.</span><span class="sxs-lookup"><span data-stu-id="1c33e-116">You will need to have at least Visual Studio 2010 or Visual Studio 2012 installed to complete this walkthrough.</span></span>

<span data-ttu-id="1c33e-117">Si usa Visual Studio 2010, también debe tener instalado [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) .</span><span class="sxs-lookup"><span data-stu-id="1c33e-117">If you are using Visual Studio 2010, you will also need to have [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) installed.</span></span>

## <a name="1-create-the-application"></a><span data-ttu-id="1c33e-118">1. crear la aplicación</span><span class="sxs-lookup"><span data-stu-id="1c33e-118">1. Create the Application</span></span>

<span data-ttu-id="1c33e-119">Para simplificar las cosas, vamos a crear una aplicación de consola básica que use Code First para realizar el acceso a los datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-119">To keep things simple we’re going to build a basic console application that uses Code First to perform data access.</span></span>

-   <span data-ttu-id="1c33e-120">Apertura de Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c33e-120">Open Visual Studio</span></span>
-   <span data-ttu-id="1c33e-121">**Archivo- &gt; nuevo- &gt; proyecto...**</span><span class="sxs-lookup"><span data-stu-id="1c33e-121">**File -&gt; New -&gt; Project…**</span></span>
-   <span data-ttu-id="1c33e-122">Seleccionar **ventanas** en el menú izquierdo y en la **aplicación de consola**</span><span class="sxs-lookup"><span data-stu-id="1c33e-122">Select **Windows** from the left menu and **Console Application**</span></span>
-   <span data-ttu-id="1c33e-123">Escriba **CodeFirstNewDatabaseSample** como nombre</span><span class="sxs-lookup"><span data-stu-id="1c33e-123">Enter **CodeFirstNewDatabaseSample** as the name</span></span>
-   <span data-ttu-id="1c33e-124">Seleccione **Aceptar**.</span><span class="sxs-lookup"><span data-stu-id="1c33e-124">Select **OK**</span></span>

## <a name="2-create-the-model"></a><span data-ttu-id="1c33e-125">2. crear el modelo</span><span class="sxs-lookup"><span data-stu-id="1c33e-125">2. Create the Model</span></span>

<span data-ttu-id="1c33e-126">Vamos a definir un modelo muy sencillo mediante clases.</span><span class="sxs-lookup"><span data-stu-id="1c33e-126">Let’s define a very simple model using classes.</span></span> <span data-ttu-id="1c33e-127">Simplemente los definimos en el archivo Program.cs, pero en una aplicación real dividiría las clases en archivos independientes y potencialmente un proyecto independiente.</span><span class="sxs-lookup"><span data-stu-id="1c33e-127">We’re just defining them in the Program.cs file but in a real world application you would split your classes out into separate files and potentially a separate project.</span></span>

<span data-ttu-id="1c33e-128">Debajo de la definición de clase de programa en Program.cs, agregue las dos clases siguientes.</span><span class="sxs-lookup"><span data-stu-id="1c33e-128">Below the Program class definition in Program.cs add the following two classes.</span></span>

``` csharp
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
```

<span data-ttu-id="1c33e-129">Observará que vamos a hacer que las dos propiedades de navegación (blog. posts y post. blog) sean virtuales.</span><span class="sxs-lookup"><span data-stu-id="1c33e-129">You’ll notice that we’re making the two navigation properties (Blog.Posts and Post.Blog) virtual.</span></span> <span data-ttu-id="1c33e-130">Esto habilita la característica de carga diferida de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="1c33e-130">This enables the Lazy Loading feature of Entity Framework.</span></span> <span data-ttu-id="1c33e-131">La carga diferida significa que el contenido de estas propiedades se cargará automáticamente desde la base de datos cuando intente obtener acceso a ellas.</span><span class="sxs-lookup"><span data-stu-id="1c33e-131">Lazy Loading means that the contents of these properties will be automatically loaded from the database when you try to access them.</span></span>

## <a name="3-create-a-context"></a><span data-ttu-id="1c33e-132">3. crear un contexto</span><span class="sxs-lookup"><span data-stu-id="1c33e-132">3. Create a Context</span></span>

<span data-ttu-id="1c33e-133">Ahora es el momento de definir un contexto derivado, que representa una sesión con la base de datos, lo que nos permite consultar y guardar datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-133">Now it’s time to define a derived context, which represents a session with the database, allowing us to query and save data.</span></span> <span data-ttu-id="1c33e-134">Definimos un contexto que se deriva de System. Data. Entity. DbContext y expone una carpa DbSet con tipo &lt; &gt; para cada clase de nuestro modelo.</span><span class="sxs-lookup"><span data-stu-id="1c33e-134">We define a context that derives from System.Data.Entity.DbContext and exposes a typed DbSet&lt;TEntity&gt; for each class in our model.</span></span>

<span data-ttu-id="1c33e-135">Ahora estamos empezando a usar los tipos del Entity Framework por lo que necesitamos agregar el paquete NuGet EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="1c33e-135">We’re now starting to use types from the Entity Framework so we need to add the EntityFramework NuGet package.</span></span>

-   <span data-ttu-id="1c33e-136">**Proyecto: &gt; administrar paquetes NuGet...**</span><span class="sxs-lookup"><span data-stu-id="1c33e-136">**Project –&gt; Manage NuGet Packages…**</span></span>
    <span data-ttu-id="1c33e-137">Nota: Si no tiene la **Administración de paquetes de NuGet..** .</span><span class="sxs-lookup"><span data-stu-id="1c33e-137">Note: If you don’t have the **Manage NuGet Packages…**</span></span> <span data-ttu-id="1c33e-138">opción debe instalar la [versión más reciente de NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span><span class="sxs-lookup"><span data-stu-id="1c33e-138">option you should install the [latest version of NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)</span></span>
-   <span data-ttu-id="1c33e-139">Seleccione la pestaña **en línea**</span><span class="sxs-lookup"><span data-stu-id="1c33e-139">Select the **Online** tab</span></span>
-   <span data-ttu-id="1c33e-140">Seleccione el paquete **EntityFramework**</span><span class="sxs-lookup"><span data-stu-id="1c33e-140">Select the **EntityFramework** package</span></span>
-   <span data-ttu-id="1c33e-141">Haz clic en **Instalar**</span><span class="sxs-lookup"><span data-stu-id="1c33e-141">Click **Install**</span></span>

<span data-ttu-id="1c33e-142">Agregue una instrucción using para System. Data. Entity en la parte superior de Program.cs.</span><span class="sxs-lookup"><span data-stu-id="1c33e-142">Add a using statement for System.Data.Entity at the top of Program.cs.</span></span>

``` csharp
using System.Data.Entity;
```

<span data-ttu-id="1c33e-143">Debajo de la clase post de Program.cs, agregue el siguiente contexto derivado.</span><span class="sxs-lookup"><span data-stu-id="1c33e-143">Below the Post class in Program.cs add the following derived context.</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

<span data-ttu-id="1c33e-144">Esta es una lista completa de lo que Program.cs debería contener ahora.</span><span class="sxs-lookup"><span data-stu-id="1c33e-144">Here is a complete listing of what Program.cs should now contain.</span></span>

``` csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data.Entity;

namespace CodeFirstNewDatabaseSample
{
    class Program
    {
        static void Main(string[] args)
        {
        }
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

    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
    }
}
```

<span data-ttu-id="1c33e-145">Eso es todo el código que necesitamos para empezar a almacenar y recuperar los datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-145">That is all the code we need to start storing and retrieving data.</span></span> <span data-ttu-id="1c33e-146">Obviamente, hay un poco más en segundo plano y echaremos un vistazo a eso en un momento, pero primero lo veremos en acción.</span><span class="sxs-lookup"><span data-stu-id="1c33e-146">Obviously there is quite a bit going on behind the scenes and we’ll take a look at that in a moment but first let’s see it in action.</span></span>

## <a name="4-reading--writing-data"></a><span data-ttu-id="1c33e-147">4. leer & escribir datos</span><span class="sxs-lookup"><span data-stu-id="1c33e-147">4. Reading & Writing Data</span></span>

<span data-ttu-id="1c33e-148">Implemente el método Main en Program.cs como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="1c33e-148">Implement the Main method in Program.cs as shown below.</span></span> <span data-ttu-id="1c33e-149">Este código crea una nueva instancia de nuestro contexto y, a continuación, la usa para insertar un nuevo blog.</span><span class="sxs-lookup"><span data-stu-id="1c33e-149">This code creates a new instance of our context and then uses it to insert a new Blog.</span></span> <span data-ttu-id="1c33e-150">A continuación, usa una consulta LINQ para recuperar todos los blogs de la base de datos ordenados alfabéticamente por título.</span><span class="sxs-lookup"><span data-stu-id="1c33e-150">Then it uses a LINQ query to retrieve all Blogs from the database ordered alphabetically by Title.</span></span>

``` csharp
class Program
{
    static void Main(string[] args)
    {
        using (var db = new BloggingContext())
        {
            // Create and save a new Blog
            Console.Write("Enter a name for a new Blog: ");
            var name = Console.ReadLine();

            var blog = new Blog { Name = name };
            db.Blogs.Add(blog);
            db.SaveChanges();

            // Display all Blogs from the database
            var query = from b in db.Blogs
                        orderby b.Name
                        select b;

            Console.WriteLine("All blogs in the database:");
            foreach (var item in query)
            {
                Console.WriteLine(item.Name);
            }

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
        }
    }
}
```

<span data-ttu-id="1c33e-151">Ahora puede ejecutar la aplicación y probarla.</span><span class="sxs-lookup"><span data-stu-id="1c33e-151">You can now run the application and test it out.</span></span>

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a><span data-ttu-id="1c33e-152">¿Dónde están mis datos?</span><span class="sxs-lookup"><span data-stu-id="1c33e-152">Where’s My Data?</span></span>

<span data-ttu-id="1c33e-153">Por Convención DbContext ha creado una base de datos automáticamente.</span><span class="sxs-lookup"><span data-stu-id="1c33e-153">By convention DbContext has created a database for you.</span></span>

-   <span data-ttu-id="1c33e-154">Si una instancia local de SQL Express está disponible (se instala de forma predeterminada con Visual Studio 2010), Code First ha creado la base de datos en esa instancia.</span><span class="sxs-lookup"><span data-stu-id="1c33e-154">If a local SQL Express instance is available (installed by default with Visual Studio 2010) then Code First has created the database on that instance</span></span>
-   <span data-ttu-id="1c33e-155">Si SQL Express no está disponible, Code First probará y usará [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (instalado de forma predeterminada con Visual Studio 2012).</span><span class="sxs-lookup"><span data-stu-id="1c33e-155">If SQL Express isn’t available then Code First will try and use [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (installed by default with Visual Studio 2012)</span></span>
-   <span data-ttu-id="1c33e-156">La base de datos se denomina después del nombre completo del contexto derivado, en nuestro caso, **CodeFirstNewDatabaseSample. BloggingContext**</span><span class="sxs-lookup"><span data-stu-id="1c33e-156">The database is named after the fully qualified name of the derived context, in our case that is **CodeFirstNewDatabaseSample.BloggingContext**</span></span>

<span data-ttu-id="1c33e-157">Estas son solo las convenciones predeterminadas y hay varias maneras de cambiar la base de datos que usa Code First. hay más información disponible en la sección **cómo el DbContext detecta el modelo y la conexión a la base de datos** .</span><span class="sxs-lookup"><span data-stu-id="1c33e-157">These are just the default conventions and there are various ways to change the database that Code First uses, more information is available in the **How DbContext Discovers the Model and Database Connection** topic.</span></span>
<span data-ttu-id="1c33e-158">Puede conectarse a esta base de datos mediante Explorador de servidores en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c33e-158">You can connect to this database using Server Explorer in Visual Studio</span></span>

-   <span data-ttu-id="1c33e-159">**Vista &gt; Explorador de servidores**</span><span class="sxs-lookup"><span data-stu-id="1c33e-159">**View -&gt; Server Explorer**</span></span>
-   <span data-ttu-id="1c33e-160">Haga clic con el botón derecho en **conexiones de datos** y seleccione **Agregar conexión..** .</span><span class="sxs-lookup"><span data-stu-id="1c33e-160">Right click on **Data Connections** and select **Add Connection…**</span></span>
-   <span data-ttu-id="1c33e-161">Si no se ha conectado a una base de datos desde Explorador de servidores antes de que tenga que seleccionar Microsoft SQL Server como origen de datos</span><span class="sxs-lookup"><span data-stu-id="1c33e-161">If you haven’t connected to a database from Server Explorer before you’ll need to select Microsoft SQL Server as the data source</span></span>

    ![Seleccionar origen de datos](~/ef6/media/selectdatasource.png)

-   <span data-ttu-id="1c33e-163">Conéctese a LocalDB o a SQL Express, en función de la que haya instalado.</span><span class="sxs-lookup"><span data-stu-id="1c33e-163">Connect to either LocalDB or SQL Express, depending on which one you have installed</span></span>

<span data-ttu-id="1c33e-164">Ahora podemos inspeccionar el esquema que Code First creado.</span><span class="sxs-lookup"><span data-stu-id="1c33e-164">We can now inspect the schema that Code First created.</span></span>

![Esquema inicial](~/ef6/media/schemainitial.png)

<span data-ttu-id="1c33e-166">DbContext ha trabajado en las clases que se van a incluir en el modelo examinando las propiedades de DbSet que hemos definido.</span><span class="sxs-lookup"><span data-stu-id="1c33e-166">DbContext worked out what classes to include in the model by looking at the DbSet properties that we defined.</span></span> <span data-ttu-id="1c33e-167">A continuación, usa el conjunto predeterminado de convenciones de Code First para determinar los nombres de tabla y columna, determinar los tipos de datos, buscar claves principales, etc.</span><span class="sxs-lookup"><span data-stu-id="1c33e-167">It then uses the default set of Code First conventions to determine table and column names, determine data types, find primary keys, etc.</span></span> <span data-ttu-id="1c33e-168">Más adelante en este tutorial veremos cómo puede invalidar estas convenciones.</span><span class="sxs-lookup"><span data-stu-id="1c33e-168">Later in this walkthrough we’ll look at how you can override these conventions.</span></span>

## <a name="5-dealing-with-model-changes"></a><span data-ttu-id="1c33e-169">5. tratar con cambios en el modelo</span><span class="sxs-lookup"><span data-stu-id="1c33e-169">5. Dealing with Model Changes</span></span>

<span data-ttu-id="1c33e-170">Ahora es el momento de realizar algunos cambios en nuestro modelo, cuando realizamos estos cambios también necesitamos actualizar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-170">Now it’s time to make some changes to our model, when we make these changes we also need to update the database schema.</span></span> <span data-ttu-id="1c33e-171">Para ello, vamos a usar una característica denominada Migraciones de Code First o migraciones para abreviar.</span><span class="sxs-lookup"><span data-stu-id="1c33e-171">To do this we are going to use a feature called Code First Migrations, or Migrations for short.</span></span>

<span data-ttu-id="1c33e-172">Las migraciones nos permiten tener un conjunto ordenado de pasos que describen cómo actualizar (y degradar) el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-172">Migrations allows us to have an ordered set of steps that describe how to upgrade (and downgrade) our database schema.</span></span> <span data-ttu-id="1c33e-173">Cada uno de estos pasos, conocido como migración, contiene código que describe los cambios que se van a aplicar.</span><span class="sxs-lookup"><span data-stu-id="1c33e-173">Each of these steps, known as a migration, contains some code that describes the changes to be applied.</span></span> 

<span data-ttu-id="1c33e-174">El primer paso es habilitar Migraciones de Code First para nuestro BloggingContext.</span><span class="sxs-lookup"><span data-stu-id="1c33e-174">The first step is to enable Code First Migrations for our BloggingContext.</span></span>

-   <span data-ttu-id="1c33e-175">**Herramientas- &gt; Administrador de paquetes de la biblioteca- &gt; consola del administrador de paquetes**</span><span class="sxs-lookup"><span data-stu-id="1c33e-175">**Tools -&gt; Library Package Manager -&gt; Package Manager Console**</span></span>
-   <span data-ttu-id="1c33e-176">Ejecute el comando **Enable-Migrations** en la consola del Administrador de paquetes</span><span class="sxs-lookup"><span data-stu-id="1c33e-176">Run the **Enable-Migrations** command in Package Manager Console</span></span>
-   <span data-ttu-id="1c33e-177">Se ha agregado una nueva carpeta Migrations al proyecto que contiene dos elementos:</span><span class="sxs-lookup"><span data-stu-id="1c33e-177">A new Migrations folder has been added to our project that contains two items:</span></span>
    -   <span data-ttu-id="1c33e-178">**Configuration.CS** : este archivo contiene la configuración que las migraciones usarán para migrar BloggingContext.</span><span class="sxs-lookup"><span data-stu-id="1c33e-178">**Configuration.cs** – This file contains the settings that Migrations will use for migrating BloggingContext.</span></span> <span data-ttu-id="1c33e-179">No es necesario cambiar nada para este tutorial, pero aquí es donde se pueden especificar los datos de inicialización, registrar proveedores para otras bases de datos, cambiar el espacio de nombres que se generan en las migraciones, etc.</span><span class="sxs-lookup"><span data-stu-id="1c33e-179">We don’t need to change anything for this walkthrough, but here is where you can specify seed data, register providers for other databases, changes the namespace that migrations are generated in etc.</span></span>
    -   <span data-ttu-id="1c33e-180">\*\* &lt; timestamp &gt; \_ InitialCreate.CS\*\* : esta es la primera migración, que representa los cambios que ya se han aplicado a la base de datos para que no sea una base de datos vacía a la que se incluyan las tablas blogs y posts.</span><span class="sxs-lookup"><span data-stu-id="1c33e-180">**&lt;timestamp&gt;\_InitialCreate.cs** – This is your first migration, it represents the changes that have already been applied to the database to take it from being an empty database to one that includes the Blogs and Posts tables.</span></span> <span data-ttu-id="1c33e-181">Aunque se permite a Code First crear automáticamente estas tablas, ahora que hemos optado por las migraciones que se han convertido en una migración.</span><span class="sxs-lookup"><span data-stu-id="1c33e-181">Although we let Code First automatically create these tables for us, now that we have opted in to Migrations they have been converted into a Migration.</span></span> <span data-ttu-id="1c33e-182">Code First también se ha registrado en la base de datos local que ya se ha aplicado esta migración.</span><span class="sxs-lookup"><span data-stu-id="1c33e-182">Code First has also recorded in our local database that this Migration has already been applied.</span></span> <span data-ttu-id="1c33e-183">La marca de tiempo en el nombre de archivo se usa para la ordenación.</span><span class="sxs-lookup"><span data-stu-id="1c33e-183">The timestamp on the filename is used for ordering purposes.</span></span>

    <span data-ttu-id="1c33e-184">Ahora vamos a hacer un cambio en nuestro modelo, agregaremos una propiedad de dirección URL a la clase de blog:</span><span class="sxs-lookup"><span data-stu-id="1c33e-184">Now let’s make a change to our model, add a Url property to the Blog class:</span></span>

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   <span data-ttu-id="1c33e-185">Ejecute el comando **Add-Migration AddUrl** en la consola del administrador de paquetes.</span><span class="sxs-lookup"><span data-stu-id="1c33e-185">Run the **Add-Migration AddUrl** command in Package Manager Console.</span></span>
    <span data-ttu-id="1c33e-186">El comando Add-Migration comprueba los cambios desde la última migración y scaffolding una nueva migración con los cambios que se hayan encontrado.</span><span class="sxs-lookup"><span data-stu-id="1c33e-186">The Add-Migration command checks for changes since your last migration and scaffolds a new migration with any changes that are found.</span></span> <span data-ttu-id="1c33e-187">Podemos dar un nombre a las migraciones. en este caso, se llama a la migración ' AddUrl '.</span><span class="sxs-lookup"><span data-stu-id="1c33e-187">We can give migrations a name; in this case we are calling the migration ‘AddUrl’.</span></span>
    <span data-ttu-id="1c33e-188">El código con scaffolding está diciendo que necesitamos agregar una columna de dirección URL, que puede contener datos de cadena, a DBO. Tabla de blogs.</span><span class="sxs-lookup"><span data-stu-id="1c33e-188">The scaffolded code is saying that we need to add a Url column, that can hold string data, to the dbo.Blogs table.</span></span> <span data-ttu-id="1c33e-189">Si es necesario, podríamos editar el código con scaffolding, pero esto no es necesario en este caso.</span><span class="sxs-lookup"><span data-stu-id="1c33e-189">If needed, we could edit the scaffolded code but that’s not required in this case.</span></span>

``` csharp
namespace CodeFirstNewDatabaseSample.Migrations
{
    using System;
    using System.Data.Entity.Migrations;

    public partial class AddUrl : DbMigration
    {
        public override void Up()
        {
            AddColumn("dbo.Blogs", "Url", c => c.String());
        }

        public override void Down()
        {
            DropColumn("dbo.Blogs", "Url");
        }
    }
}
```

-   <span data-ttu-id="1c33e-190">Ejecute el comando **Update-Database** en la consola del administrador de paquetes.</span><span class="sxs-lookup"><span data-stu-id="1c33e-190">Run the **Update-Database** command in Package Manager Console.</span></span> <span data-ttu-id="1c33e-191">Este comando aplicará las migraciones pendientes a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-191">This command will apply any pending migrations to the database.</span></span> <span data-ttu-id="1c33e-192">La migración de InitialCreate ya se ha aplicado, por lo que las migraciones solo aplicarán la nueva migración de AddUrl.</span><span class="sxs-lookup"><span data-stu-id="1c33e-192">Our InitialCreate migration has already been applied so migrations will just apply our new AddUrl migration.</span></span>
    <span data-ttu-id="1c33e-193">Sugerencia: puede usar el modificador **– verbose** al llamar a Update-Database para ver el SQL que se ejecuta en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-193">Tip: You can use the **–Verbose** switch when calling Update-Database to see the SQL that is being executed against the database.</span></span>

<span data-ttu-id="1c33e-194">La nueva columna URL se agrega ahora a la tabla blogs en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="1c33e-194">The new Url column is now added to the Blogs table in the database:</span></span>

![Esquema con dirección URL](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a><span data-ttu-id="1c33e-196">6. anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="1c33e-196">6. Data Annotations</span></span>

<span data-ttu-id="1c33e-197">Hasta ahora, simplemente se permite a EF detectar el modelo con sus convenciones predeterminadas, pero habrá ocasiones en las que nuestras clases no sigan las convenciones y necesitamos poder realizar una configuración adicional.</span><span class="sxs-lookup"><span data-stu-id="1c33e-197">So far we’ve just let EF discover the model using its default conventions, but there are going to be times when our classes don’t follow the conventions and we need to be able to perform further configuration.</span></span> <span data-ttu-id="1c33e-198">Hay dos opciones para ello: veremos las anotaciones de datos de esta sección y, a continuación, la API fluida en la sección siguiente.</span><span class="sxs-lookup"><span data-stu-id="1c33e-198">There are two options for this; we’ll look at Data Annotations in this section and then the fluent API in the next section.</span></span>

-   <span data-ttu-id="1c33e-199">Vamos a agregar una clase de usuario a nuestro modelo</span><span class="sxs-lookup"><span data-stu-id="1c33e-199">Let’s add a User class to our model</span></span>

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="1c33e-200">También necesitamos agregar un conjunto a nuestro contexto derivado.</span><span class="sxs-lookup"><span data-stu-id="1c33e-200">We also need to add a set to our derived context</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   <span data-ttu-id="1c33e-201">Si intentamos agregar una migración, se obtendría un error que indica que "el*usuario de EntityType" no tiene ninguna clave definida. Defina la clave para este EntityType ".*</span><span class="sxs-lookup"><span data-stu-id="1c33e-201">If we tried to add a migration we’d get an error saying “*EntityType ‘User’ has no key defined. Define the key for this EntityType.”*</span></span> <span data-ttu-id="1c33e-202">como EF no tiene forma de saber que el nombre de usuario debe ser la clave principal del usuario.</span><span class="sxs-lookup"><span data-stu-id="1c33e-202">because EF has no way of knowing that Username should be the primary key for User.</span></span>
-   <span data-ttu-id="1c33e-203">Ahora vamos a usar anotaciones de datos, por lo que necesitamos agregar una instrucción using en la parte superior de Program.cs</span><span class="sxs-lookup"><span data-stu-id="1c33e-203">We’re going to use Data Annotations now so we need to add a using statement at the top of Program.cs</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
```

-   <span data-ttu-id="1c33e-204">Ahora, anote la propiedad username para identificar que es la clave principal.</span><span class="sxs-lookup"><span data-stu-id="1c33e-204">Now annotate the Username property to identify that it is the primary key</span></span>

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   <span data-ttu-id="1c33e-205">Usar el comando **Add-Migration adduser** para aplicar scaffolding a una migración para aplicar estos cambios a la base de datos</span><span class="sxs-lookup"><span data-stu-id="1c33e-205">Use the **Add-Migration AddUser** command to scaffold a migration to apply these changes to the database</span></span>
-   <span data-ttu-id="1c33e-206">Ejecutar el comando **Update-Database** para aplicar la nueva migración a la base de datos</span><span class="sxs-lookup"><span data-stu-id="1c33e-206">Run the **Update-Database** command to apply the new migration to the database</span></span>

<span data-ttu-id="1c33e-207">La nueva tabla se agrega ahora a la base de datos:</span><span class="sxs-lookup"><span data-stu-id="1c33e-207">The new table is now added to the database:</span></span>

![Esquema con usuarios](~/ef6/media/schemawithusers.png)

<span data-ttu-id="1c33e-209">La lista completa de anotaciones admitidas por EF es:</span><span class="sxs-lookup"><span data-stu-id="1c33e-209">The full list of annotations supported by EF is:</span></span>

-   [<span data-ttu-id="1c33e-210">KeyAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-210">KeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [<span data-ttu-id="1c33e-211">StringLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-211">StringLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [<span data-ttu-id="1c33e-212">MaxLengthAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-212">MaxLengthAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [<span data-ttu-id="1c33e-213">ConcurrencyCheckAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-213">ConcurrencyCheckAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [<span data-ttu-id="1c33e-214">RequiredAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-214">RequiredAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [<span data-ttu-id="1c33e-215">TimestampAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-215">TimestampAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [<span data-ttu-id="1c33e-216">ComplexTypeAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-216">ComplexTypeAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [<span data-ttu-id="1c33e-217">ColumnAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-217">ColumnAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [<span data-ttu-id="1c33e-218">TableAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-218">TableAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [<span data-ttu-id="1c33e-219">InversePropertyAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-219">InversePropertyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [<span data-ttu-id="1c33e-220">ForeignKeyAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-220">ForeignKeyAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [<span data-ttu-id="1c33e-221">DatabaseGeneratedAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-221">DatabaseGeneratedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [<span data-ttu-id="1c33e-222">NotMappedAttribute</span><span class="sxs-lookup"><span data-stu-id="1c33e-222">NotMappedAttribute</span></span>](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a><span data-ttu-id="1c33e-223">7. API fluida</span><span class="sxs-lookup"><span data-stu-id="1c33e-223">7. Fluent API</span></span>

<span data-ttu-id="1c33e-224">En la sección anterior, analizamos el uso de anotaciones de datos para complementar o invalidar lo que detectó la Convención.</span><span class="sxs-lookup"><span data-stu-id="1c33e-224">In the previous section we looked at using Data Annotations to supplement or override what was detected by convention.</span></span> <span data-ttu-id="1c33e-225">La otra forma de configurar el modelo es a través de la API fluida de Code First.</span><span class="sxs-lookup"><span data-stu-id="1c33e-225">The other way to configure the model is via the Code First fluent API.</span></span>

<span data-ttu-id="1c33e-226">La mayoría de la configuración del modelo se puede realizar con anotaciones de datos simples.</span><span class="sxs-lookup"><span data-stu-id="1c33e-226">Most model configuration can be done using simple data annotations.</span></span> <span data-ttu-id="1c33e-227">La API fluida es una forma más avanzada de especificar la configuración del modelo que abarca todo lo que pueden hacer las anotaciones de datos además de algunas configuraciones más avanzadas que no son posibles con las anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-227">The fluent API is a more advanced way of specifying model configuration that covers everything that data annotations can do in addition to some more advanced configuration not possible with data annotations.</span></span> <span data-ttu-id="1c33e-228">Las anotaciones de datos y la API fluida se pueden usar juntas.</span><span class="sxs-lookup"><span data-stu-id="1c33e-228">Data annotations and the fluent API can be used together.</span></span>

<span data-ttu-id="1c33e-229">Para acceder a la API fluida, invalide el método OnModelCreating en DbContext.</span><span class="sxs-lookup"><span data-stu-id="1c33e-229">To access the fluent API you override the OnModelCreating method in DbContext.</span></span> <span data-ttu-id="1c33e-230">Supongamos que deseamos cambiar el nombre de la columna en la que se almacena User. DisplayName para mostrar el \_ nombre.</span><span class="sxs-lookup"><span data-stu-id="1c33e-230">Let’s say we wanted to rename the column that User.DisplayName is stored in to display\_name.</span></span>

-   <span data-ttu-id="1c33e-231">Invalide el método OnModelCreating en BloggingContext con el siguiente código</span><span class="sxs-lookup"><span data-stu-id="1c33e-231">Override the OnModelCreating method on BloggingContext with the following code</span></span>

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Entity<User>()
            .Property(u => u.DisplayName)
            .HasColumnName("display_name");
    }
}
```

-   <span data-ttu-id="1c33e-232">Use el comando **Add-Migration ChangeDisplayName** para aplicar scaffolding a una migración para aplicar estos cambios a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-232">Use the **Add-Migration ChangeDisplayName** command to scaffold a migration to apply these changes to the database.</span></span>
-   <span data-ttu-id="1c33e-233">Ejecute el comando **Update-Database** para aplicar la nueva migración a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-233">Run the **Update-Database** command to apply the new migration to the database.</span></span>

<span data-ttu-id="1c33e-234">Ahora se ha cambiado el nombre de la columna DisplayName a nombre para mostrar \_ :</span><span class="sxs-lookup"><span data-stu-id="1c33e-234">The DisplayName column is now renamed to display\_name:</span></span>

![Esquema con nombre para mostrar cambiado](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a><span data-ttu-id="1c33e-236">Resumen</span><span class="sxs-lookup"><span data-stu-id="1c33e-236">Summary</span></span>

<span data-ttu-id="1c33e-237">En este tutorial, hemos examinado Code First desarrollo con una nueva base de datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-237">In this walkthrough we looked at Code First development using a new database.</span></span> <span data-ttu-id="1c33e-238">Definimos un modelo mediante clases y luego usaba ese modelo para crear una base de datos y almacenar y recuperar datos.</span><span class="sxs-lookup"><span data-stu-id="1c33e-238">We defined a model using classes then used that model to create a database and store and retrieve data.</span></span> <span data-ttu-id="1c33e-239">Una vez creada la base de datos, usamos Migraciones de Code First para cambiar el esquema a medida que nuestro modelo evolucionó.</span><span class="sxs-lookup"><span data-stu-id="1c33e-239">Once the database was created we used Code First Migrations to change the schema as our model evolved.</span></span> <span data-ttu-id="1c33e-240">También vimos cómo configurar un modelo con anotaciones de datos y la API fluida.</span><span class="sxs-lookup"><span data-stu-id="1c33e-240">We also saw how to configure a model using Data Annotations and the Fluent API.</span></span>
