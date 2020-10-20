---
title: 'Primeros pasos con EF Core y Xamarin: EF Core'
description: Tutorial de introducción al uso de Xamarin para crear aplicaciones móviles con Entity Framework Core
author: codemillmatt
ms.date: 07/07/2020
ms.author: masoucou
uid: core/get-started/xamarin
ms.openlocfilehash: 0552038d471e294834bed9e3bf1f05fd74c39192
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062547"
---
# <a name="getting-started-with-ef-core-and-xamarin"></a><span data-ttu-id="b71a3-103">Primeros pasos con EF Core y Xamarin</span><span class="sxs-lookup"><span data-stu-id="b71a3-103">Getting Started with EF Core and Xamarin</span></span>

<span data-ttu-id="b71a3-104">En este tutorial, crearemos una aplicación de consola de [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) que realiza el acceso a datos en una base de datos de SQLite mediante Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b71a3-104">In this tutorial, you create a [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) application that performs data access against a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="b71a3-105">Puede seguir el tutorial mediante Visual Studio en Windows o Visual Studio para Mac.</span><span class="sxs-lookup"><span data-stu-id="b71a3-105">You can follow the tutorial by using Visual Studio on Windows or Visual Studio for Mac.</span></span>

> [!TIP]
> <span data-ttu-id="b71a3-106">Puede ver en [GitHub un ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin) de este artículo.</span><span class="sxs-lookup"><span data-stu-id="b71a3-106">You can view this article's [sample on GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b71a3-107">Requisitos previos</span><span class="sxs-lookup"><span data-stu-id="b71a3-107">Prerequisites</span></span>

<span data-ttu-id="b71a3-108">Instale la opción que corresponda:</span><span class="sxs-lookup"><span data-stu-id="b71a3-108">Install one of the below:</span></span>

* <span data-ttu-id="b71a3-109">[Visual Studio 2019, versión 16.3 o posterior](https://www.visualstudio.com/downloads/), con esta carga de trabajo:</span><span class="sxs-lookup"><span data-stu-id="b71a3-109">[Visual Studio 2019 version 16.3 or later](https://www.visualstudio.com/downloads/) with this workload:</span></span>
  * <span data-ttu-id="b71a3-110">**Desarrollo móvil con .NET**</span><span class="sxs-lookup"><span data-stu-id="b71a3-110">**Mobile Development with .NET**</span></span>
* [<span data-ttu-id="b71a3-111">Visual Studio para Mac</span><span class="sxs-lookup"><span data-stu-id="b71a3-111">Visual Studio for Mac</span></span>](https://visualstudio.microsoft.com/vs/mac/)

<span data-ttu-id="b71a3-112">En esta [documentación se proporcionan instrucciones de instalación paso a paso detalladas](/xamarin/get-started/installation) para cada plataforma.</span><span class="sxs-lookup"><span data-stu-id="b71a3-112">This [documentation provides detailed step-by-step installation instructions](/xamarin/get-started/installation) for each platform.</span></span>

## <a name="download-and-run-the-sample-project"></a><span data-ttu-id="b71a3-113">Descarga y ejecución del proyecto de ejemplo</span><span class="sxs-lookup"><span data-stu-id="b71a3-113">Download and run the sample project</span></span>

<span data-ttu-id="b71a3-114">Para ejecutar y explorar esta aplicación de ejemplo, descargue el código disponible en GitHub.</span><span class="sxs-lookup"><span data-stu-id="b71a3-114">To run and explore this sample application, download the code on GitHub.</span></span>

<span data-ttu-id="b71a3-115">Una vez descargado, abra el archivo de solución `EFGettingStarted.sln` en Visual Studio o Visual Studio para Mac y ejecute la aplicación en la plataforma de su elección.</span><span class="sxs-lookup"><span data-stu-id="b71a3-115">Once downloaded, open the solution file `EFGettingStarted.sln` in Visual Studio or Visual Studio for Mac and run the application on the platform of your choice.</span></span>

<span data-ttu-id="b71a3-116">Cuando se inicie la aplicación por primera vez, rellenará la base de datos de SQLite local con dos entradas que representan blogs.</span><span class="sxs-lookup"><span data-stu-id="b71a3-116">When the app first starts, it will populate the local SQLite database with two entries representing blogs.</span></span>

![Captura de pantalla de la página en la que se enumeran todos los blogs](_static/xamarin-tutorial-1.png)

<span data-ttu-id="b71a3-118">Haga clic en el botón **Agregar** de la barra de herramientas.</span><span class="sxs-lookup"><span data-stu-id="b71a3-118">Click the **Add** button in the toolbar.</span></span>

<span data-ttu-id="b71a3-119">Aparecerá una nueva página que le permitirá escribir información sobre un nuevo blog.</span><span class="sxs-lookup"><span data-stu-id="b71a3-119">A new page will appear that allows you to enter information about a new blog.</span></span>

![Captura de pantalla de la página de edición del nuevo blog](_static/xamarin-tutorial-2.png)

<span data-ttu-id="b71a3-121">Rellene toda la información y haga clic en **Guardar** en la barra de herramientas.</span><span class="sxs-lookup"><span data-stu-id="b71a3-121">Fill out all the info and click **Save** from the toolbar.</span></span> <span data-ttu-id="b71a3-122">El nuevo blog se guardará en la base de datos de SQLite de la aplicación y se mostrará en la lista.</span><span class="sxs-lookup"><span data-stu-id="b71a3-122">The new blog will save to the app's SQLite database and will show in the list.</span></span>

<span data-ttu-id="b71a3-123">Puede hacer clic en una de las entradas de blog de la lista y ver cualquier publicación de dicho blog.</span><span class="sxs-lookup"><span data-stu-id="b71a3-123">You can click on one of the blog entries in the list and see any posts for that blog.</span></span>

![Captura de pantalla de la página que enumera las publicaciones de blog](_static/xamarin-tutorial-3.png)

<span data-ttu-id="b71a3-125">En la barra de herramientas, haga clic en **Agregar**.</span><span class="sxs-lookup"><span data-stu-id="b71a3-125">Click **Add** in the toolbar.</span></span>

<span data-ttu-id="b71a3-126">Aparecerá una página que le permitirá rellenar información sobre una nueva entrada de blog.</span><span class="sxs-lookup"><span data-stu-id="b71a3-126">A page then appears that allows you to fill out information about a new blog post.</span></span>

![Captura de pantalla de la página Agregar nueva publicación](_static/xamarin-tutorial-4.png)

<span data-ttu-id="b71a3-128">Rellene toda la información y haga clic en **Guardar** en la barra de herramientas.</span><span class="sxs-lookup"><span data-stu-id="b71a3-128">Fill out all the information and click **Save** in the toolbar.</span></span>

<span data-ttu-id="b71a3-129">La nueva publicación se asociará a la entrada de blog en la que hizo clic en el paso anterior, se guardará en la base de datos de SQLite de la aplicación y se mostrará en la lista.</span><span class="sxs-lookup"><span data-stu-id="b71a3-129">The new post will be associated to the blog post you clicked on in a previous step and will save to the app's SQLite database and show in the list.</span></span>

<span data-ttu-id="b71a3-130">Vuelva a la página de la lista de blogs.</span><span class="sxs-lookup"><span data-stu-id="b71a3-130">Go back to the blog list page.</span></span> <span data-ttu-id="b71a3-131">Haga clic en **Eliminar todo** en la barra de herramientas.</span><span class="sxs-lookup"><span data-stu-id="b71a3-131">And click **Delete All** in the toolbar.</span></span> <span data-ttu-id="b71a3-132">Todos los blogs y sus publicaciones correspondientes se eliminarán de la base de datos de SQLite de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b71a3-132">All blogs and their corresponding posts will then be deleted from the app's SQLite database.</span></span>

![Captura de pantalla de la aplicación con todos los blogs eliminados](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a><span data-ttu-id="b71a3-134">Exploración del código</span><span class="sxs-lookup"><span data-stu-id="b71a3-134">Explore the code</span></span>

<span data-ttu-id="b71a3-135">Las secciones siguientes le guiarán a través del código del proyecto de ejemplo que lee, crea, actualiza y elimina datos de una base de datos de SQLite mediante EF Core con Xamarin.Forms.</span><span class="sxs-lookup"><span data-stu-id="b71a3-135">The following sections will walk you through the code in the sample project that reads, creates, updates, and deletes data from a SQLite database using EF Core with Xamarin.Forms.</span></span>

<span data-ttu-id="b71a3-136">Se da por hecho que está familiarizado con los temas de Xamarin.Forms relacionados con la [visualización de datos](/xamarin/xamarin-forms/app-fundamentals/data-binding/) y la [navegación por las páginas](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span><span class="sxs-lookup"><span data-stu-id="b71a3-136">It is assumed that you are familiar with the Xamarin.Forms topics of [displaying data](/xamarin/xamarin-forms/app-fundamentals/data-binding/) and [navigating between pages](/xamarin/xamarin-forms/app-fundamentals/navigation/).</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b71a3-137">Entity Framework Core usa la reflexión para invocar funciones que el enlazador de Xamarin.iOS puede quitar mientras está en las configuraciones de modo de **versión**.</span><span class="sxs-lookup"><span data-stu-id="b71a3-137">Entity Framework Core uses reflection to invoke functions which the Xamarin.iOS linker may strip out while in **Release** mode configurations.</span></span> <span data-ttu-id="b71a3-138">Puede evitarlo de dos maneras.</span><span class="sxs-lookup"><span data-stu-id="b71a3-138">You can avoid that in one of two ways.</span></span>
> 
> * <span data-ttu-id="b71a3-139">La primera es agregar `--linkskip System.Core` a los **argumentos de mtouch adicionales** en las opciones de **compilación de iOS**.</span><span class="sxs-lookup"><span data-stu-id="b71a3-139">The first is to add `--linkskip System.Core` to the **Additional mtouch arguments** in the **iOS Build** options.</span></span>
> * <span data-ttu-id="b71a3-140">También puede establecer el **comportamiento del enlazador** de Xamarin.iOS en `Don't Link` en las opciones de **compilación de iOS**.</span><span class="sxs-lookup"><span data-stu-id="b71a3-140">Alternatively set the Xamarin.iOS **Linker behavior** to `Don't Link` in the **iOS Build** options.</span></span>
> <span data-ttu-id="b71a3-141">[En este artículo se explica más sobre el enlazador de Xamarin.iOS](/xamarin/ios/deploy-test/linker), incluido cómo establecer el comportamiento en Xamarin.iOS.</span><span class="sxs-lookup"><span data-stu-id="b71a3-141">[This article explains more about the Xamarin.iOS linker](/xamarin/ios/deploy-test/linker) including how to set the behavior on Xamarin.iOS.</span></span>
> 

## <a name="entity-framework-core-nuget-packages"></a><span data-ttu-id="b71a3-142">Paquetes NuGet de Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b71a3-142">Entity Framework Core NuGet packages</span></span>

<span data-ttu-id="b71a3-143">Para crear aplicaciones de Xamarin.Forms con EF Core, instale el paquete de los proveedores de bases de datos de EF Core que quiera establecer como destino en todos los proyectos de la solución de Xamarin.Forms.</span><span class="sxs-lookup"><span data-stu-id="b71a3-143">To create Xamarin.Forms apps with EF Core, you install the package for the EF Core database provider(s) you want to target into all of the projects in the Xamarin.Forms solution.</span></span> <span data-ttu-id="b71a3-144">En este tutorial se usa el proveedor SqLite.</span><span class="sxs-lookup"><span data-stu-id="b71a3-144">This tutorial uses the SQLite provider.</span></span>

<span data-ttu-id="b71a3-145">El siguiente paquete de NuGet es necesario en cada uno de los proyectos de la solución de Xamarin.Forms.</span><span class="sxs-lookup"><span data-stu-id="b71a3-145">The following NuGet package is needed in each of the projects in the Xamarin.Forms solution.</span></span>

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a><span data-ttu-id="b71a3-146">Clases de modelo</span><span class="sxs-lookup"><span data-stu-id="b71a3-146">Model classes</span></span>

<span data-ttu-id="b71a3-147">Cada tabla de la base de datos de SQLite a la que se tiene acceso a través de EF Core se modela en una clase.</span><span class="sxs-lookup"><span data-stu-id="b71a3-147">Each table in the SQLite database accessed through EF Core is modeled in a class.</span></span> <span data-ttu-id="b71a3-148">En este ejemplo, se usan dos clases, `Blog` y `Post`, que se pueden encontrar en la carpeta `Models`.</span><span class="sxs-lookup"><span data-stu-id="b71a3-148">In this sample, two classes are used: `Blog` and `Post` which can be found in the `Models` folder.</span></span>

<span data-ttu-id="b71a3-149">Las clases de modelo se componen únicamente de propiedades, que modelan las columnas de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b71a3-149">The model classes are composed only of properties, which model columns in the database.</span></span>

* <span data-ttu-id="b71a3-150">**Blog.cs**</span><span class="sxs-lookup"><span data-stu-id="b71a3-150">**Blog.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* <span data-ttu-id="b71a3-151">La propiedad `Posts` permite definir una relación de elemento primario y secundario entre `Blog` y `Post`.</span><span class="sxs-lookup"><span data-stu-id="b71a3-151">The `Posts` property defines a parent-child relationship between `Blog` and `Post`.</span></span>

* <span data-ttu-id="b71a3-152">**Post.cs**</span><span class="sxs-lookup"><span data-stu-id="b71a3-152">**Post.cs**</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* <span data-ttu-id="b71a3-153">Las propiedades `BlogId` y `Blog` vuelven a relacionarse con el objeto `Blog` primario de la instancia de `Post`.</span><span class="sxs-lookup"><span data-stu-id="b71a3-153">The `BlogId` and `Blog` properties relate back to the parent `Blog` object for the instance of the `Post`.</span></span>

## <a name="data-context"></a><span data-ttu-id="b71a3-154">Contexto de datos</span><span class="sxs-lookup"><span data-stu-id="b71a3-154">Data context</span></span>

<span data-ttu-id="b71a3-155">La clase `BloggingContext` se encuentra en la carpeta `Services` y hereda de la clase EF Core `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="b71a3-155">The `BloggingContext` class is located in the `Services` folder and inherits from the EF Core `DbContext` class.</span></span> <span data-ttu-id="b71a3-156">`DbContext` se utiliza para agrupar las consultas y los cambios de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b71a3-156">A `DbContext` is used to group together database queries and changes.</span></span>

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* <span data-ttu-id="b71a3-157">Ambas propiedades de esta clase de tipo `DbSet` se usan para operar en las tablas subyacentes que representan blogs y publicaciones.</span><span class="sxs-lookup"><span data-stu-id="b71a3-157">Both properties in this class of type `DbSet` are used to operate on the underlying tables representing Blogs and Posts.</span></span>
* <span data-ttu-id="b71a3-158">`SQLitePCL.Batteries_V2.Init()` es necesario en el constructor para iniciar SQLite en iOS.</span><span class="sxs-lookup"><span data-stu-id="b71a3-158">The `SQLitePCL.Batteries_V2.Init()` is needed in the constructor to initiate SQLite on iOS.</span></span>
* <span data-ttu-id="b71a3-159">La función `OnConfiguring` configura la ubicación de la base de datos de SQLite en el dispositivo físico.</span><span class="sxs-lookup"><span data-stu-id="b71a3-159">The `OnConfiguring` function sets up the location of the SQLite database on the physical device.</span></span>

## <a name="create-read-update--delete"></a><span data-ttu-id="b71a3-160">Creación, lectura, actualización y eliminación</span><span class="sxs-lookup"><span data-stu-id="b71a3-160">Create, read, update & delete</span></span>

<span data-ttu-id="b71a3-161">A continuación, se muestran algunas instancias de la aplicación en las que se usa EF Core para acceder a SQLite.</span><span class="sxs-lookup"><span data-stu-id="b71a3-161">The following are some instances in the app where EF Core is used to access SQLite.</span></span>

### <a name="read"></a><span data-ttu-id="b71a3-162">Leer</span><span class="sxs-lookup"><span data-stu-id="b71a3-162">Read</span></span>

* <span data-ttu-id="b71a3-163">Permite devolver todos los registros.</span><span class="sxs-lookup"><span data-stu-id="b71a3-163">Return all records.</span></span>
  * <span data-ttu-id="b71a3-164">La función `OnAppearing` de `BlogsPage.xaml.cs` devuelve todos los registros `Blog` y los almacena en una variable `List`.</span><span class="sxs-lookup"><span data-stu-id="b71a3-164">The `OnAppearing` function of `BlogsPage.xaml.cs` returns all `Blog` records and stores them into a `List` variable.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* <span data-ttu-id="b71a3-165">Permite devolver registros específicos.</span><span class="sxs-lookup"><span data-stu-id="b71a3-165">Return specific records.</span></span>
  * <span data-ttu-id="b71a3-166">La función `OnAppearing` de `PostsPage.xaml.cs` devuelve registros `Post` que contienen un `BlogId` específico.</span><span class="sxs-lookup"><span data-stu-id="b71a3-166">The `OnAppearing` function of `PostsPage.xaml.cs` returns `Post` records that contain a specific `BlogId`.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a><span data-ttu-id="b71a3-167">Crear</span><span class="sxs-lookup"><span data-stu-id="b71a3-167">Create</span></span>

* <span data-ttu-id="b71a3-168">Permite insertar un nuevo registro.</span><span class="sxs-lookup"><span data-stu-id="b71a3-168">Insert a new record.</span></span>
  * <span data-ttu-id="b71a3-169">La función `Save_Clicked` de `AddBlogPage.xaml.cs` inserta un nuevo objeto `Blog` en la base de datos de SQLite.</span><span class="sxs-lookup"><span data-stu-id="b71a3-169">The `Save_Clicked` function of `AddBlogPage.xaml.cs` inserts a new `Blog` object into the SQLite database.</span></span>

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a><span data-ttu-id="b71a3-170">Actualizar</span><span class="sxs-lookup"><span data-stu-id="b71a3-170">Update</span></span>

* <span data-ttu-id="b71a3-171">Permite actualizar un registro existente.</span><span class="sxs-lookup"><span data-stu-id="b71a3-171">Update an existing record.</span></span>
  * <span data-ttu-id="b71a3-172">La función `Save_Clicked` de `AddPostPage.xaml.cs` actualiza un objeto `Blog` existente con un `Post` nuevo.</span><span class="sxs-lookup"><span data-stu-id="b71a3-172">The `Save_Clicked` function of `AddPostPage.xaml.cs` updates an existing `Blog` object with a new `Post`.</span></span>
  
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

### <a name="delete"></a><span data-ttu-id="b71a3-173">Eliminar</span><span class="sxs-lookup"><span data-stu-id="b71a3-173">Delete</span></span>

* <span data-ttu-id="b71a3-174">Permite eliminar todos los registros en cascada en los registros secundarios.</span><span class="sxs-lookup"><span data-stu-id="b71a3-174">Delete all records with cascade to child records.</span></span>
  * <span data-ttu-id="b71a3-175">La función `DeleteAll_Clicked` de `BlogsPage.xaml.cs` elimina todos los registros de `Blog` de la base de datos de SQLite y elimina en cascada todos los registros de `Post` secundarios de `Blog`.</span><span class="sxs-lookup"><span data-stu-id="b71a3-175">The `DeleteAll_Clicked` function of `BlogsPage.xaml.cs` deletes all the `Blog` records in the SQLite database and cascades the deletes to all of the `Blog` child `Post` records.</span></span>

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a><span data-ttu-id="b71a3-176">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="b71a3-176">Next steps</span></span>

<span data-ttu-id="b71a3-177">En esta introducción, ha aprendido a usar una aplicación de Xamarin.Forms para tener acceso a una base de datos de SQLite mediante Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b71a3-177">In this getting started you have learned how to use a Xamarin.Forms application to access a SQLite database using Entity Framework Core.</span></span>

<span data-ttu-id="b71a3-178">Otros temas de Entity Framework Core de interés para los desarrolladores de Xamarin:</span><span class="sxs-lookup"><span data-stu-id="b71a3-178">Other Entity Framework Core topics of interest to Xamarin developers:</span></span>

* [<span data-ttu-id="b71a3-179">Configuración de `DbContext`</span><span class="sxs-lookup"><span data-stu-id="b71a3-179">Configuring a `DbContext`</span></span>](xref:core/miscellaneous/configuring-dbcontext)
* <span data-ttu-id="b71a3-180">Obtenga más información sobre las [expresiones de consulta LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations).</span><span class="sxs-lookup"><span data-stu-id="b71a3-180">Learn more about [LINQ query expressions](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations)</span></span>
* <span data-ttu-id="b71a3-181">[Configure su modelo](xref:core/modeling/index) para especificar aspectos como [requerido](xref:core/modeling/entity-properties#required-and-optional-properties) y [longitud máxima](xref:core/modeling/entity-properties#maximum-length).</span><span class="sxs-lookup"><span data-stu-id="b71a3-181">[Configure your model](xref:core/modeling/index) to specify things like [required](xref:core/modeling/entity-properties#required-and-optional-properties) and [maximum length](xref:core/modeling/entity-properties#maximum-length)</span></span>
