---
title: 'Primeros pasos con EF Core y Xamarin: EF Core'
description: Tutorial de introducción al uso de Xamarin para crear aplicaciones móviles con Entity Framework Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: b4a7e2260337d74329d309e9db32fe97a2131d73
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619289"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="49dfe-103">Primeros pasos con EF Core y Xamarin</span><span class="sxs-lookup"><span data-stu-id="49dfe-103">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="49dfe-104">En este tutorial, crearemos una aplicación de consola de [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) que realiza el acceso a datos en una base de datos de SQLite mediante Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="49dfe-104">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="49dfe-105">Puede seguir el tutorial mediante Visual Studio en Windows o Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="49dfe-105">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="49dfe-106">Puede ver en [GitHub un ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin) de este artículo.</span><span class="sxs-lookup"><span data-stu-id="49dfe-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="49dfe-107">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="49dfe-107">Prerequisites</span></span>

<span data-ttu-id="49dfe-108">Instale la opción que corresponda:</span><span class="sxs-lookup"><span data-stu-id="49dfe-108">Install one of the below:</span></span>

* <span data-ttu-id="49dfe-109">[Visual Studio 2019, versión 16.3 o posterior](https://www.visualstudio.com/downloads/), con esta carga de trabajo:</span><span class="sxs-lookup"><span data-stu-id="49dfe-109">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="49dfe-110">**Desarrollo móvil con .NET**</span><span class="sxs-lookup"><span data-stu-id="49dfe-110">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="49dfe-111">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="49dfe-111">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="49dfe-112">En esta [documentación se proporcionan instrucciones de instalación paso a paso detalladas](/xamarin/get-started/installation) para cada plataforma.</span><span class="sxs-lookup"><span data-stu-id="49dfe-112">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="49dfe-113">Descarga y ejecución del proyecto de ejemplo</span><span class="sxs-lookup"><span data-stu-id="49dfe-113">Download and run the sample project</span></span>

<span data-ttu-id="49dfe-114">Para ejecutar y explorar esta aplicación de ejemplo, descargue el código disponible en GitHub.</span><span class="sxs-lookup"><span data-stu-id="49dfe-114">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="49dfe-115">Una vez descargado, abra el archivo de solución `EFGettingStarted.sln` en Visual Studio o Visual Studio para Mac y ejecute la aplicación en la plataforma de su elección.</span><span class="sxs-lookup"><span data-stu-id="49dfe-115">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="49dfe-116">Cuando se inicie la aplicación por primera vez, rellenará la base de datos de SQLite local con dos entradas que representan blogs.</span><span class="sxs-lookup"><span data-stu-id="49dfe-116">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![Captura de pantalla de la página en la que se enumeran todos los blogs](_static/xamarin-tutorial-1.png)

<span data-ttu-id="49dfe-118">Haga clic en el botón **Agregar** de la barra de herramientas.</span><span class="sxs-lookup"><span data-stu-id="49dfe-118">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="49dfe-119">Aparecerá una nueva página que le permitirá escribir información sobre un nuevo blog.</span><span class="sxs-lookup"><span data-stu-id="49dfe-119">A new page will appear that allows you to enter information about a new blog.</span></span>

![Captura de pantalla de la página de edición del nuevo blog](_static/xamarin-tutorial-2.png)

<span data-ttu-id="49dfe-121">Rellene toda la información y haga clic en **Guardar** en la barra de herramientas.</span><span class="sxs-lookup"><span data-stu-id="49dfe-121">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="49dfe-122">El nuevo blog se guardará en la base de datos de SQLite de la aplicación y se mostrará en la lista.</span><span class="sxs-lookup"><span data-stu-id="49dfe-122">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="49dfe-123">Puede hacer clic en una de las entradas de blog de la lista y ver cualquier publicación de dicho blog.</span><span class="sxs-lookup"><span data-stu-id="49dfe-123">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![Captura de pantalla de la página que enumera las publicaciones de blog](_static/xamarin-tutorial-3.png)

<span data-ttu-id="49dfe-125">En la barra de herramientas, haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="49dfe-125">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="49dfe-126">Aparecerá una página que le permitirá rellenar información sobre una nueva entrada de blog.</span><span class="sxs-lookup"><span data-stu-id="49dfe-126">A page then appears that allows you to fill out information about a new blog post.</span></span>

![Captura de pantalla de la página Agregar nueva publicación](_static/xamarin-tutorial-4.png)

<span data-ttu-id="49dfe-128">Rellene toda la información y haga clic en **Guardar** en la barra de herramientas.</span><span class="sxs-lookup"><span data-stu-id="49dfe-128">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="49dfe-129">La nueva publicación se asociará a la entrada de blog en la que hizo clic en el paso anterior, se guardará en la base de datos de SQLite de la aplicación y se mostrará en la lista.</span><span class="sxs-lookup"><span data-stu-id="49dfe-129">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="49dfe-130">Vuelva a la página de la lista de blogs.</span><span class="sxs-lookup"><span data-stu-id="49dfe-130">Go back to the blog list page.</span></span> <span data-ttu-id="49dfe-131">Haga clic en **Eliminar todo** en la barra de herramientas.</span><span class="sxs-lookup"><span data-stu-id="49dfe-131">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="49dfe-132">Todos los blogs y sus publicaciones correspondientes se eliminarán de la base de datos de SQLite de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="49dfe-132">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![Captura de pantalla de la aplicación con todos los blogs eliminados](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="49dfe-134">Exploración del código</span><span class="sxs-lookup"><span data-stu-id="49dfe-134">Explore the code</span></span>

<span data-ttu-id="49dfe-135">Las secciones siguientes le guiarán a través del código del proyecto de ejemplo que lee, crea, actualiza y elimina datos de una base de datos de SQLite mediante EF Core con Xamarin.Forms.</span><span class="sxs-lookup"><span data-stu-id="49dfe-135">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="49dfe-136">Se da por hecho que está familiarizado con los temas de Xamarin.Forms relacionados con la [visualización de datos](/xamarin/xamarin-forms/app-fundamentals/data-binding/) y la [navegación por las páginas](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span><span class="sxs-lookup"><span data-stu-id="49dfe-136">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="49dfe-137">Paquetes NuGet de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="49dfe-137">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="49dfe-138">Para crear aplicaciones de Xamarin.Forms con EF Core, instale el paquete de los proveedores de bases de datos de EF Core que quiera establecer como destino en todos los proyectos de la solución de Xamarin.Forms.</span><span class="sxs-lookup"><span data-stu-id="49dfe-138">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="49dfe-139">En este tutorial se usa el proveedor SqLite.</span><span class="sxs-lookup"><span data-stu-id="49dfe-139">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="49dfe-140">El siguiente paquete de NuGet es necesario en cada uno de los proyectos de la solución de Xamarin.Forms.</span><span class="sxs-lookup"><span data-stu-id="49dfe-140">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="49dfe-141">Clases de modelo</span><span class="sxs-lookup"><span data-stu-id="49dfe-141">Model classes</span></span>

<span data-ttu-id="49dfe-142">Cada tabla de la base de datos de SQLite a la que se tiene acceso a través de EF Core se modela en una clase.</span><span class="sxs-lookup"><span data-stu-id="49dfe-142">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="49dfe-143">En este ejemplo, se usan dos clases, `Blog` y `Post`, que se pueden encontrar en la carpeta `Models`.</span><span class="sxs-lookup"><span data-stu-id="49dfe-143">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="49dfe-144">Las clases de modelo se componen únicamente de propiedades, que modelan las columnas de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="49dfe-144">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="49dfe-145">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="49dfe-145">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="49dfe-146">La propiedad `Posts` permite definir una relación de elemento primario y secundario entre `Blog` y `Post`.</span><span class="sxs-lookup"><span data-stu-id="49dfe-146">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="49dfe-147">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="49dfe-147">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="49dfe-148">Las propiedades `BlogId` y `Blog` vuelven a relacionarse con el objeto `Blog` primario de la instancia de `Post`.</span><span class="sxs-lookup"><span data-stu-id="49dfe-148">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="49dfe-149">Contexto de datos</span><span class="sxs-lookup"><span data-stu-id="49dfe-149">Data context</span></span>

<span data-ttu-id="49dfe-150">La clase `BloggingContext` se encuentra en la carpeta `Services` y hereda de la clase EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="49dfe-150">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="49dfe-151">`DbContext` se utiliza para agrupar las consultas y los cambios de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="49dfe-151">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="49dfe-152">Ambas propiedades de esta clase de tipo `DbSet` se usan para operar en las tablas subyacentes que representan blogs y publicaciones.</span><span class="sxs-lookup"><span data-stu-id="49dfe-152">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="49dfe-153">`SQLitePCL.Batteries_V2.Init()` es necesario en el constructor para iniciar SQLite en iOS.</span><span class="sxs-lookup"><span data-stu-id="49dfe-153">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="49dfe-154">La función `OnConfiguring` configura la ubicación de la base de datos de SQLite en el dispositivo físico.</span><span class="sxs-lookup"><span data-stu-id="49dfe-154">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="49dfe-155">Creación, lectura, actualización y eliminación</span><span class="sxs-lookup"><span data-stu-id="49dfe-155">Create, read, update & delete</span></span>

<span data-ttu-id="49dfe-156">A continuación, se muestran algunas instancias de la aplicación en las que se usa EF Core para acceder a SQLite.</span><span class="sxs-lookup"><span data-stu-id="49dfe-156">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="49dfe-157">Leer</span><span class="sxs-lookup"><span data-stu-id="49dfe-157">Read</span></span>

* <span data-ttu-id="49dfe-158">Permite devolver todos los registros.</span><span class="sxs-lookup"><span data-stu-id="49dfe-158">Return all records.</span></span>
  * <span data-ttu-id="49dfe-159">La función `OnAppearing` de `BlogsPage.xaml.cs` devuelve todos los registros `Blog` y los almacena en una variable `List`.</span><span class="sxs-lookup"><span data-stu-id="49dfe-159">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="49dfe-160">Permite devolver registros específicos.</span><span class="sxs-lookup"><span data-stu-id="49dfe-160">Return specific records.</span></span>
  * <span data-ttu-id="49dfe-161">La función `OnAppearing` de `PostsPage.xaml.cs` devuelve registros `Post` que contienen un `BlogId` específico.</span><span class="sxs-lookup"><span data-stu-id="49dfe-161">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="49dfe-162">Crear</span><span class="sxs-lookup"><span data-stu-id="49dfe-162">Create</span></span>

* <span data-ttu-id="49dfe-163">Permite insertar un nuevo registro.</span><span class="sxs-lookup"><span data-stu-id="49dfe-163">Insert a new record.</span></span>
  * <span data-ttu-id="49dfe-164">La función `Save_Clicked` de `AddBlogPage.xaml.cs` inserta un nuevo objeto `Blog` en la base de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="49dfe-164">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="49dfe-165">Actualizar</span><span class="sxs-lookup"><span data-stu-id="49dfe-165">Update</span></span>

* <span data-ttu-id="49dfe-166">Permite actualizar un registro existente.</span><span class="sxs-lookup"><span data-stu-id="49dfe-166">Update an existing record.</span></span>
  * <span data-ttu-id="49dfe-167">La función `Save_Clicked` de `AddPostPage.xaml.cs` actualiza un objeto `Blog` existente con un `Post` nuevo.</span><span class="sxs-lookup"><span data-stu-id="49dfe-167">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
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

### <a name="delete"></a><span data-ttu-id="49dfe-168">Eliminar</span><span class="sxs-lookup"><span data-stu-id="49dfe-168">Delete</span></span>

* <span data-ttu-id="49dfe-169">Permite eliminar todos los registros en cascada en los registros secundarios.</span><span class="sxs-lookup"><span data-stu-id="49dfe-169">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="49dfe-170">La función `DeleteAll_Clicked` de `BlogsPage.xaml.cs` elimina todos los registros de `Blog` de la base de datos de SQLite y elimina en cascada todos los registros de `Post` secundarios de `Blog`.</span><span class="sxs-lookup"><span data-stu-id="49dfe-170">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="49dfe-171">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="49dfe-171">Next steps</span></span>

<span data-ttu-id="49dfe-172">En esta introducción, ha aprendido a usar una aplicación de Xamarin.Forms para tener acceso a una base de datos de SQLite mediante Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="49dfe-172">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="49dfe-173">Otros temas de Entity Framework Core de interés para los desarrolladores de Xamarin:</span><span class="sxs-lookup"><span data-stu-id="49dfe-173">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="49dfe-174">Configuración de `DbContext`</span><span class="sxs-lookup"><span data-stu-id="49dfe-174">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="49dfe-175">Obtenga más información sobre las [expresiones de consulta LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations).</span><span class="sxs-lookup"><span data-stu-id="49dfe-175">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="49dfe-176">[Configure su modelo](xref:core/modeling/index) para especificar aspectos como [requerido](xref:core/modeling/entity-properties#required-and-optional-properties) y [longitud máxima](xref:core/modeling/entity-properties#maximum-length).</span><span class="sxs-lookup"><span data-stu-id="49dfe-176">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
