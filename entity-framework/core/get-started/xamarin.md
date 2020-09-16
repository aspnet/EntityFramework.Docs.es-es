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
# <a name="getting-started-with-ef-core-and-xamarin"></a>Primeros pasos con EF Core y Xamarin

En este tutorial, crearemos una aplicación de consola de [Xamarin.Forms](/xamarin/get-started/what-is-xamarin-forms) que realiza el acceso a datos en una base de datos de SQLite mediante Entity Framework Core.

Puede seguir el tutorial mediante Visual Studio en Windows o Visual Studio para Mac.

> [!TIP]
> Puede ver en [GitHub un ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Xamarin) de este artículo.

## <a name="prerequisites"></a>Requisitos previos

Instale la opción que corresponda:

* [Visual Studio 2019, versión 16.3 o posterior](https://www.visualstudio.com/downloads/), con esta carga de trabajo:
  * **Desarrollo móvil con .NET**
* [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/)

En esta [documentación se proporcionan instrucciones de instalación paso a paso detalladas](/xamarin/get-started/installation) para cada plataforma.

## <a name="download-and-run-the-sample-project"></a>Descarga y ejecución del proyecto de ejemplo

Para ejecutar y explorar esta aplicación de ejemplo, descargue el código disponible en GitHub.

Una vez descargado, abra el archivo de solución `EFGettingStarted.sln` en Visual Studio o Visual Studio para Mac y ejecute la aplicación en la plataforma de su elección.

Cuando se inicie la aplicación por primera vez, rellenará la base de datos de SQLite local con dos entradas que representan blogs.

![Captura de pantalla de la página en la que se enumeran todos los blogs](_static/xamarin-tutorial-1.png)

Haga clic en el botón **Agregar** de la barra de herramientas.

Aparecerá una nueva página que le permitirá escribir información sobre un nuevo blog.

![Captura de pantalla de la página de edición del nuevo blog](_static/xamarin-tutorial-2.png)

Rellene toda la información y haga clic en **Guardar** en la barra de herramientas. El nuevo blog se guardará en la base de datos de SQLite de la aplicación y se mostrará en la lista.

Puede hacer clic en una de las entradas de blog de la lista y ver cualquier publicación de dicho blog.

![Captura de pantalla de la página que enumera las publicaciones de blog](_static/xamarin-tutorial-3.png)

En la barra de herramientas, haga clic en **Agregar**.

Aparecerá una página que le permitirá rellenar información sobre una nueva entrada de blog.

![Captura de pantalla de la página Agregar nueva publicación](_static/xamarin-tutorial-4.png)

Rellene toda la información y haga clic en **Guardar** en la barra de herramientas.

La nueva publicación se asociará a la entrada de blog en la que hizo clic en el paso anterior, se guardará en la base de datos de SQLite de la aplicación y se mostrará en la lista.

Vuelva a la página de la lista de blogs. Haga clic en **Eliminar todo** en la barra de herramientas. Todos los blogs y sus publicaciones correspondientes se eliminarán de la base de datos de SQLite de la aplicación.

![Captura de pantalla de la aplicación con todos los blogs eliminados](_static/xamarin-tutorial-5.png)

## <a name="explore-the-code"></a>Exploración del código

Las secciones siguientes le guiarán a través del código del proyecto de ejemplo que lee, crea, actualiza y elimina datos de una base de datos de SQLite mediante EF Core con Xamarin.Forms.

Se da por hecho que está familiarizado con los temas de Xamarin.Forms relacionados con la [visualización de datos](/xamarin/xamarin-forms/app-fundamentals/data-binding/) y la [navegación por las páginas](/xamarin/xamarin-forms/app-fundamentals/navigation/).

## <a name="entity-framework-core-nuget-packages"></a>Paquetes NuGet de Entity Framework Core

Para crear aplicaciones de Xamarin.Forms con EF Core, instale el paquete de los proveedores de bases de datos de EF Core que quiera establecer como destino en todos los proyectos de la solución de Xamarin.Forms. En este tutorial se usa el proveedor SqLite.

El siguiente paquete de NuGet es necesario en cada uno de los proyectos de la solución de Xamarin.Forms.

* `Microsoft.EntityFrameworkCore.Sqlite`

## <a name="model-classes"></a>Clases de modelo

Cada tabla de la base de datos de SQLite a la que se tiene acceso a través de EF Core se modela en una clase. En este ejemplo, se usan dos clases, `Blog` y `Post`, que se pueden encontrar en la carpeta `Models`.

Las clases de modelo se componen únicamente de propiedades, que modelan las columnas de la base de datos.

* **Blog.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Blog.cs)]

* La propiedad `Posts` permite definir una relación de elemento primario y secundario entre `Blog` y `Post`.

* **Post.cs**

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Models/Post.cs)]

* Las propiedades `BlogId` y `Blog` vuelven a relacionarse con el objeto `Blog` primario de la instancia de `Post`.

## <a name="data-context"></a>Contexto de datos

La clase `BloggingContext` se encuentra en la carpeta `Services` y hereda de la clase EF Core `DbContext`. `DbContext` se utiliza para agrupar las consultas y los cambios de la base de datos.

  [!code-csharp[](../../../samples/core/Xamarin/EFGetStarted/Services/BloggingContext.cs)]

* Ambas propiedades de esta clase de tipo `DbSet` se usan para operar en las tablas subyacentes que representan blogs y publicaciones.
* `SQLitePCL.Batteries_V2.Init()` es necesario en el constructor para iniciar SQLite en iOS.
* La función `OnConfiguring` configura la ubicación de la base de datos de SQLite en el dispositivo físico.

## <a name="create-read-update--delete"></a>Creación, lectura, actualización y eliminación

A continuación, se muestran algunas instancias de la aplicación en las que se usa EF Core para acceder a SQLite.

### <a name="read"></a>Leer

* Permite devolver todos los registros.
  * La función `OnAppearing` de `BlogsPage.xaml.cs` devuelve todos los registros `Blog` y los almacena en una variable `List`.

```csharp
using (var blogContext = new BloggingContext())
{
    var theBlogs = blogContext.Blogs.ToList();
}
```

* Permite devolver registros específicos.
  * La función `OnAppearing` de `PostsPage.xaml.cs` devuelve registros `Post` que contienen un `BlogId` específico.

```csharp
using (var blogContext = new BloggingContext())
{
    var postList = blogContext.Posts
        .Where(p => p.BlogId == BlogId)
        .ToList();
}
```

### <a name="create"></a>Crear

* Permite insertar un nuevo registro.
  * La función `Save_Clicked` de `AddBlogPage.xaml.cs` inserta un nuevo objeto `Blog` en la base de datos de SQLite.

```csharp
var blog = new Blog { Url = blogUrl.Text };

using (var blogContext = new BloggingContext())
{
    blogContext.Add(blog);

    await blogContext.SaveChangesAsync();
}
```

### <a name="update"></a>Actualizar

* Permite actualizar un registro existente.
  * La función `Save_Clicked` de `AddPostPage.xaml.cs` actualiza un objeto `Blog` existente con un `Post` nuevo.
  
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

### <a name="delete"></a>Eliminar

* Permite eliminar todos los registros en cascada en los registros secundarios.
  * La función `DeleteAll_Clicked` de `BlogsPage.xaml.cs` elimina todos los registros de `Blog` de la base de datos de SQLite y elimina en cascada todos los registros de `Post` secundarios de `Blog`.

```csharp
using (var blogContext = new BloggingContext())
{
    blogContext.RemoveRange(blogContext.Blogs);

    await blogContext.SaveChangesAsync();
}
```

## <a name="next-steps"></a>Pasos siguientes

En esta introducción, ha aprendido a usar una aplicación de Xamarin.Forms para tener acceso a una base de datos de SQLite mediante Entity Framework Core.

Otros temas de Entity Framework Core de interés para los desarrolladores de Xamarin:

* [Configuración de `DbContext`](xref:core/miscellaneous/configuring-dbcontext)
* Obtenga más información sobre las [expresiones de consulta LINQ](/dotnet/csharp/programming-guide/concepts/linq/basic-linq-query-operations).
* [Configure su modelo](xref:core/modeling/index) para especificar aspectos como [requerido](xref:core/modeling/entity-properties#required-and-optional-properties) y [longitud máxima](xref:core/modeling/entity-properties#maximum-length).
