---
title: 'Primeros pasos con EF Core y Xamarin: EF Core'
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: 14fe61e0ddd648f824bfe3c1c91a58af91b1c2d4
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/17/2020
ms.locfileid: "86453011"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="fc528-102">Primeros pasos con EF Core y Xamarin</span><span class="sxs-lookup"><span data-stu-id="fc528-102">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="fc528-103">En este tutorial, crearemos una aplicación de consola de [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) que realiza el acceso a datos en una base de datos de SQLite mediante Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="fc528-103">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="fc528-104">Puede seguir el tutorial mediante Visual Studio en Windows o Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="fc528-104">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="fc528-105">Puede ver en [GitHub un ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin) de este artículo.</span><span class="sxs-lookup"><span data-stu-id="fc528-105">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fc528-106">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="fc528-106">Prerequisites</span></span>

<span data-ttu-id="fc528-107">Instale la opción que corresponda:</span><span class="sxs-lookup"><span data-stu-id="fc528-107">Install one of the below:</span></span>

* <span data-ttu-id="fc528-108">[Visual Studio 2019, versión 16.3 o posterior](https://www.visualstudio.com/downloads/), con esta carga de trabajo:</span><span class="sxs-lookup"><span data-stu-id="fc528-108">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="fc528-109">**Desarrollo móvil con .NET**</span><span class="sxs-lookup"><span data-stu-id="fc528-109">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="fc528-110">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="fc528-110">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="fc528-111">En esta [documentación se proporcionan instrucciones de instalación paso a paso detalladas](/xamarin/get-started/installation) para cada plataforma.</span><span class="sxs-lookup"><span data-stu-id="fc528-111">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="fc528-112">Descarga y ejecución del proyecto de ejemplo</span><span class="sxs-lookup"><span data-stu-id="fc528-112">Download and run the sample project</span></span>

<span data-ttu-id="fc528-113">Para ejecutar y explorar esta aplicación de ejemplo, descargue el código disponible en GitHub.</span><span class="sxs-lookup"><span data-stu-id="fc528-113">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="fc528-114">Una vez descargado, abra el archivo de solución `EFGettingStarted.sln` en Visual Studio o Visual Studio para Mac y ejecute la aplicación en la plataforma de su elección.</span><span class="sxs-lookup"><span data-stu-id="fc528-114">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="fc528-115">Cuando se inicie la aplicación por primera vez, rellenará la base de datos de SQLite local con dos entradas que representan blogs.</span><span class="sxs-lookup"><span data-stu-id="fc528-115">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![Captura de pantalla de la página en la que se enumeran todos los blogs](_static/xamarin-tutorial-1.png)

<span data-ttu-id="fc528-117">Haga clic en el botón **Agregar** de la barra de herramientas.</span><span class="sxs-lookup"><span data-stu-id="fc528-117">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="fc528-118">Aparecerá una nueva página que le permitirá escribir información sobre un nuevo blog.</span><span class="sxs-lookup"><span data-stu-id="fc528-118">A new page will appear that allows you to enter information about a new blog.</span></span>

![Captura de pantalla de la página de edición del nuevo blog](_static/xamarin-tutorial-2.png)

<span data-ttu-id="fc528-120">Rellene toda la información y haga clic en **Guardar** en la barra de herramientas.</span><span class="sxs-lookup"><span data-stu-id="fc528-120">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="fc528-121">El nuevo blog se guardará en la base de datos de SQLite de la aplicación y se mostrará en la lista.</span><span class="sxs-lookup"><span data-stu-id="fc528-121">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="fc528-122">Puede hacer clic en una de las entradas de blog de la lista y ver cualquier publicación de dicho blog.</span><span class="sxs-lookup"><span data-stu-id="fc528-122">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![Captura de pantalla de la página que enumera las publicaciones de blog](_static/xamarin-tutorial-3.png)

<span data-ttu-id="fc528-124">En la barra de herramientas, haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="fc528-124">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="fc528-125">Aparecerá una página que le permitirá rellenar información sobre una nueva entrada de blog.</span><span class="sxs-lookup"><span data-stu-id="fc528-125">A page then appears that allows you to fill out information about a new blog post.</span></span>

![Captura de pantalla de la página Agregar nueva publicación](_static/xamarin-tutorial-4.png)

<span data-ttu-id="fc528-127">Rellene toda la información y haga clic en **Guardar** en la barra de herramientas.</span><span class="sxs-lookup"><span data-stu-id="fc528-127">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="fc528-128">La nueva publicación se asociará a la entrada de blog en la que hizo clic en el paso anterior, se guardará en la base de datos de SQLite de la aplicación y se mostrará en la lista.</span><span class="sxs-lookup"><span data-stu-id="fc528-128">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="fc528-129">Vuelva a la página de la lista de blogs.</span><span class="sxs-lookup"><span data-stu-id="fc528-129">Go back to the blog list page.</span></span> <span data-ttu-id="fc528-130">Haga clic en **Eliminar todo** en la barra de herramientas.</span><span class="sxs-lookup"><span data-stu-id="fc528-130">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="fc528-131">Todos los blogs y sus publicaciones correspondientes se eliminarán de la base de datos de SQLite de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="fc528-131">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![Captura de pantalla de la aplicación con todos los blogs eliminados](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="fc528-133">Exploración del código</span><span class="sxs-lookup"><span data-stu-id="fc528-133">Explore the code</span></span>

<span data-ttu-id="fc528-134">Las secciones siguientes le guiarán a través del código del proyecto de ejemplo que lee, crea, actualiza y elimina datos de una base de datos de SQLite mediante EF Core con Xamarin.Forms.</span><span class="sxs-lookup"><span data-stu-id="fc528-134">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="fc528-135">Se da por hecho que está familiarizado con los temas de Xamarin.Forms relacionados con la [visualización de datos](/xamarin/xamarin-forms/app-fundamentals/data-binding/) y la [navegación por las páginas](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span><span class="sxs-lookup"><span data-stu-id="fc528-135">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="fc528-136">Paquetes NuGet de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="fc528-136">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="fc528-137">Para crear aplicaciones de Xamarin.Forms con EF Core, instale el paquete de los proveedores de bases de datos de EF Core que quiera establecer como destino en todos los proyectos de la solución de Xamarin.Forms.</span><span class="sxs-lookup"><span data-stu-id="fc528-137">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="fc528-138">En este tutorial se usa el proveedor SqLite.</span><span class="sxs-lookup"><span data-stu-id="fc528-138">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="fc528-139">El siguiente paquete de NuGet es necesario en cada uno de los proyectos de la solución de Xamarin.Forms.</span><span class="sxs-lookup"><span data-stu-id="fc528-139">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="fc528-140">Clases de modelo</span><span class="sxs-lookup"><span data-stu-id="fc528-140">Model classes</span></span>

<span data-ttu-id="fc528-141">Cada tabla de la base de datos de SQLite a la que se tiene acceso a través de EF Core se modela en una clase.</span><span class="sxs-lookup"><span data-stu-id="fc528-141">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="fc528-142">En este ejemplo, se usan dos clases, `Blog` y `Post`, que se pueden encontrar en la carpeta `Models`.</span><span class="sxs-lookup"><span data-stu-id="fc528-142">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="fc528-143">Las clases de modelo se componen únicamente de propiedades, que modelan las columnas de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="fc528-143">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="fc528-144">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="fc528-144">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="fc528-145">La propiedad `Posts` permite definir una relación de elemento primario y secundario entre `Blog` y `Post`.</span><span class="sxs-lookup"><span data-stu-id="fc528-145">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="fc528-146">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="fc528-146">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="fc528-147">Las propiedades `BlogId` y `Blog` vuelven a relacionarse con el objeto `Blog` primario de la instancia de `Post`.</span><span class="sxs-lookup"><span data-stu-id="fc528-147">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="fc528-148">Contexto de datos</span><span class="sxs-lookup"><span data-stu-id="fc528-148">Data context</span></span>

<span data-ttu-id="fc528-149">La clase `BloggingContext` se encuentra en la carpeta `Services` y hereda de la clase EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="fc528-149">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="fc528-150">`DbContext` se utiliza para agrupar las consultas y los cambios de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="fc528-150">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="fc528-151">Ambas propiedades de esta clase de tipo `DbSet` se usan para operar en las tablas subyacentes que representan blogs y publicaciones.</span><span class="sxs-lookup"><span data-stu-id="fc528-151">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="fc528-152">`SQLitePCL.Batteries_V2.Init()` es necesario en el constructor para iniciar SQLite en iOS.</span><span class="sxs-lookup"><span data-stu-id="fc528-152">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="fc528-153">La función `OnConfiguring` configura la ubicación de la base de datos de SQLite en el dispositivo físico.</span><span class="sxs-lookup"><span data-stu-id="fc528-153">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="fc528-154">Creación, lectura, actualización y eliminación</span><span class="sxs-lookup"><span data-stu-id="fc528-154">Create, read, update & delete</span></span>

<span data-ttu-id="fc528-155">A continuación, se muestran algunas instancias de la aplicación en las que se usa EF Core para acceder a SQLite.</span><span class="sxs-lookup"><span data-stu-id="fc528-155">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="fc528-156">Leer</span><span class="sxs-lookup"><span data-stu-id="fc528-156">Read</span></span>

* <span data-ttu-id="fc528-157">Permite devolver todos los registros.</span><span class="sxs-lookup"><span data-stu-id="fc528-157">Return all records.</span></span>
  * <span data-ttu-id="fc528-158">La función `OnAppearing` de `BlogsPage.xaml.cs` devuelve todos los registros `Blog` y los almacena en una variable `List`.</span><span class="sxs-lookup"><span data-stu-id="fc528-158">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="fc528-159">Permite devolver registros específicos.</span><span class="sxs-lookup"><span data-stu-id="fc528-159">Return specific records.</span></span>
  * <span data-ttu-id="fc528-160">La función `OnAppearing` de `PostsPage.xaml.cs` devuelve registros `Post` que contienen un `BlogId` específico.</span><span class="sxs-lookup"><span data-stu-id="fc528-160">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="fc528-161">Crear</span><span class="sxs-lookup"><span data-stu-id="fc528-161">Create</span></span>

* <span data-ttu-id="fc528-162">Permite insertar un nuevo registro.</span><span class="sxs-lookup"><span data-stu-id="fc528-162">Insert a new record.</span></span>
  * <span data-ttu-id="fc528-163">La función `Save_Clicked` de `AddBlogPage.xaml.cs` inserta un nuevo objeto `Blog` en la base de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="fc528-163">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="fc528-164">Actualizar</span><span class="sxs-lookup"><span data-stu-id="fc528-164">Update</span></span>

* <span data-ttu-id="fc528-165">Permite actualizar un registro existente.</span><span class="sxs-lookup"><span data-stu-id="fc528-165">Update an existing record.</span></span>
  * <span data-ttu-id="fc528-166">La función `Save_Clicked` de `AddPostPage.xaml.cs` actualiza un objeto `Blog` existente con un `Post` nuevo.</span><span class="sxs-lookup"><span data-stu-id="fc528-166">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
```csharp
var newPost = new Post
{
    BlogId = BlogId,
    Content = postCell.Text,
    Title = titleCell.Text
};

using (var blogContext = new BloggingContext())
{
    var blog = await blogContext
        .Blogs
        .FirstAsync(b => b.BlogId == BlogId);

    blog.Posts.Add(newPost);

    await blogContext.SaveChangesAsync();
}
```

### <a name="delete"></a><span data-ttu-id="fc528-167">Eliminar</span><span class="sxs-lookup"><span data-stu-id="fc528-167">Delete</span></span>

* <span data-ttu-id="fc528-168">Permite eliminar todos los registros en cascada en los registros secundarios.</span><span class="sxs-lookup"><span data-stu-id="fc528-168">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="fc528-169">La función `DeleteAll_Clicked` de `BlogsPage.xaml.cs` elimina todos los registros de `Blog` de la base de datos de SQLite y elimina en cascada todos los registros de `Post` secundarios de `Blog`.</span><span class="sxs-lookup"><span data-stu-id="fc528-169">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="fc528-170">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="fc528-170">Next steps</span></span>

<span data-ttu-id="fc528-171">En esta introducción, ha aprendido a usar una aplicación de Xamarin.Forms para tener acceso a una base de datos de SQLite mediante Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="fc528-171">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="fc528-172">Otros temas de Entity Framework Core de interés para los desarrolladores de Xamarin:</span><span class="sxs-lookup"><span data-stu-id="fc528-172">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="fc528-173">Configuración de `DbContext`</span><span class="sxs-lookup"><span data-stu-id="fc528-173">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="fc528-174">Obtenga más información sobre las [expresiones de consulta LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations).</span><span class="sxs-lookup"><span data-stu-id="fc528-174">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="fc528-175">[Configure su modelo](xref:core/modeling/index) para especificar aspectos como [requerido](xref:core/modeling/entity-properties#required-and-optional-properties) y [longitud máxima](xref:core/modeling/entity-properties#maximum-length).</span><span class="sxs-lookup"><span data-stu-id="fc528-175">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
