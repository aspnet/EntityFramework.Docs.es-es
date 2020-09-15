---
title: Migraciones de Code First automática-EF6
description: Migraciones de Code First automática en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/automatic
ms.openlocfilehash: e7bd9ff7d9dcecb7fecf213306047a53fc04135c
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072569"
---
# <a name="automatic-code-first-migrations"></a>Migraciones de Code First automática
Las migraciones automáticas permiten usar Migraciones de Code First sin tener un archivo de código en el proyecto para cada cambio que realice. No todos los cambios se pueden aplicar automáticamente; por ejemplo, el cambio de nombre de columna requiere el uso de una migración basada en código.

> [!NOTE]
> En este artículo se supone que sabe cómo usar Migraciones de Code First en escenarios básicos. Si no lo hace, tendrá que leer [migraciones de Code First](xref:ef6/modeling/code-first/migrations/index) antes de continuar.

## <a name="recommendation-for-team-environments"></a>Recomendación para entornos de equipo

Puede intercalar migraciones automáticas y basadas en código, pero esto no se recomienda en escenarios de desarrollo en equipo. Si forma parte de un equipo de desarrolladores que usan el control de código fuente, debe usar migraciones automáticas o exclusivamente basadas en código. Dadas las limitaciones de las migraciones automáticas, se recomienda usar migraciones basadas en código en entornos de equipo.

## <a name="building-an-initial-model--database"></a>Creación de un modelo inicial y una base de datos

Antes de empezar a usar Migraciones, se necesitan un proyecto y un modelo de Code First con los que trabajar. En este tutorial se va a usar el modelo canónico **Blog** y **Post**.

-   Creación de una nueva aplicación de consola de **MigrationsAutomaticDemo**
-   Agregue la versión más reciente del paquete NuGet de **EntityFramework** al proyecto
    -   **Herramientas –&gt; Administrador de paquetes de biblioteca–&gt; Consola del Administrador de paquetes**
    -   Ejecute el comando **Install-Package EntityFramework**
-   Agregue un archivo **Model.cs** con el código que se muestra a continuación. Este código define una sola clase **Blog** que conforma el modelo de dominio y una clase **BlogContext** que es el contexto de EF Code First

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsAutomaticDemo
      {
          public class BlogContext : DbContext
          {
              public DbSet<Blog> Blogs { get; set; }
          }

          public class Blog
          {
              public int BlogId { get; set; }
              public string Name { get; set; }
          }
      }
  ```

-   Ahora que tenemos un modelo, es hora de usarlo para el acceso a los datos. Actualice el archivo **Program.cs** con el código que se muestra a continuación.

  ``` csharp
      using System;
      using System.Collections.Generic;
      using System.Linq;
      using System.Text;

      namespace MigrationsAutomaticDemo
      {
          class Program
          {
              static void Main(string[] args)
              {
                  using (var db = new BlogContext())
                  {
                      db.Blogs.Add(new Blog { Name = "Another Blog " });
                      db.SaveChanges();

                      foreach (var blog in db.Blogs)
                      {
                          Console.WriteLine(blog.Name);
                      }
                  }

                  Console.WriteLine("Press any key to exit...");
                  Console.ReadKey();
              }
          }
      }
  ```

-   Ejecute la aplicación y verá que se crea una base de datos **MigrationsAutomaticCodeDemo. BlogContext** .

    ![Base de datos LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>Habilitación de migraciones

Es hora de realizar más cambios en el modelo.

-   Vamos a introducir una propiedad Url en la clase Blog.

``` csharp
    public string Url { get; set; }
```

Si volviera a ejecutar la aplicación de nuevo, aparecería una excepción InvalidOperationException con el texto *El modelo que respalda el contexto 'BlogContext' ha cambiado desde que se creó la base de datos. Considere la posibilidad de usar Migraciones de Code First para actualizar la base de datos (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *).*

Como sugiere la excepción, es hora de empezar a usar Migraciones de Code First. Dado que queremos usar migraciones automáticas, vamos a especificar el modificador **– EnableAutomaticMigrations** .

-   Ejecute el comando **enable-Migrations – EnableAutomaticMigrations** en la consola del administrador de paquetes. este comando ha agregado una carpeta **Migrations** a nuestro proyecto. Esta nueva carpeta contiene un archivo:

-   **La clase Configuration.** Esta clase permite configurar el comportamiento de Migraciones en el contexto. En este tutorial se usa la configuración predeterminada.
    *Puesto que hay un solo contexto de Code First en el proyecto, Enable-Migrations ha rellenado automáticamente el tipo de contexto al que se aplica esta configuración.*

 

## <a name="your-first-automatic-migration"></a>La primera migración automática

Migraciones de Code First tiene dos comandos principales con los que se va a familiarizar.

-   **Add-Migration** aplica la técnica scaffolding a la siguiente migración en función de los cambios realizados en el modelo desde la creación de la última migración
-   **Update-Database** aplica las migraciones pendientes a la base de datos

Vamos a evitar el uso de Add-Migration (a menos que realmente sea necesario) y nos centraremos en permitir que Migraciones de Code First calcule y aplique los cambios automáticamente. Vamos a usar **Update-Database** para obtener migraciones de Code First para enviar los cambios a nuestro modelo (la nueva propiedad **blog. ur**l) a la base de datos.

-   Ejecute el comando **Update-Database** en la consola del administrador de paquetes.

La base de datos **MigrationsAutomaticDemo. BlogContext** se ha actualizado para incluir la columna **URL** en la tabla **blogs** .

 

## <a name="your-second-automatic-migration"></a>La segunda migración automática

Vamos a realizar otro cambio y dejar que Migraciones de Code First inserten automáticamente los cambios en la base de datos.

-   También vamos a agregar una nueva clase **Post**

``` csharp
    public class Post
    {
        public int PostId { get; set; }
        [MaxLength(200)]
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
```

-   Además agregaremos una colección **Posts** a la clase **Blog** para formar el otro extremo de la relación entre **Blog** y **Post**

``` csharp
    public virtual List<Post> Posts { get; set; }
```

Ahora use **Update-Database para actualizar** la base de datos. Esta vez vamos a especificar la marca **-Verbose** para que pueda ver el SQL que ejecuta Migraciones de Code First.

-   Ejecute el comando **Update-Database –Verbose** en la consola del Administrador de paquetes.

## <a name="adding-a-code-based-migration"></a>Agregar una migración basada en código

Ahora veamos algo que es posible que quieramos usar una migración basada en código para.

-   Vamos a agregar una propiedad **rating** a la clase **blog**

``` csharp
    public int Rating { get; set; }
```

Podríamos simplemente ejecutar **Update-Database** para realizar estos cambios en la base de datos. Sin embargo, vamos a agregar una columna **blogs. Rating** que no aceptan valores NULL, si hay datos existentes en la tabla, se asignará el valor predeterminado de CLR del tipo de datos de la nueva columna (la clasificación es Integer, por lo que sería **0**). Pero queremos especificar un valor predeterminado de **3**, para que las filas existentes en la tabla **Blogs** comiencen con una clasificación decente.
Vamos a usar el comando Add-Migration para escribir este cambio en una migración basada en código para poder editarlo. El comando **Add-Migration** nos permite dar un nombre a estas migraciones, simplemente vamos a llamar a nuestra **AddBlogRating**.

-   Ejecute el comando **Add-Migration AddBlogRating** en la consola del administrador de paquetes.
-   En la carpeta **migraciones** ahora tenemos una nueva migración de **AddBlogRating** . El nombre de archivo de la migración se ha fijado previamente con una marca de tiempo para ayudar con la ordenación. Vamos a editar el código generado para especificar un valor predeterminado de 3 para blog. Rating (línea 10 en el código siguiente)

*La migración también tiene un archivo de código subyacente que captura algunos metadatos. Estos metadatos permitirán que Migraciones de Code First repliquen las migraciones automáticas realizadas antes de esta migración basada en código. Esto es importante si otro desarrollador desea ejecutar nuestras migraciones o Cuándo es el momento de implementar la aplicación.*

``` csharp
    namespace MigrationsAutomaticDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogRating : DbMigration
        {
            public override void Up()
            {
                AddColumn("Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropColumn("Blogs", "Rating");
            }
        }
    }
```

La migración editada tiene buen aspecto, así que vamos a usar **Update-Database** para actualizar la base de datos.

-   Ejecute el comando **Update-Database** en la consola del administrador de paquetes.

## <a name="back-to-automatic-migrations"></a>Volver a migraciones automáticas

Ahora podemos volver a pasar a las migraciones automáticas para nuestros cambios más sencillos. Migraciones de Code First se encargará de realizar las migraciones automáticas y basadas en código en el orden correcto en función de los metadatos que se almacenan en el archivo de código subyacente para cada migración basada en código.

-   Vamos a agregar una propiedad post. Abstract a nuestro modelo

``` csharp
    public string Abstract { get; set; }
```

Ahora podemos usar **Update-Database** para obtener migraciones de Code First para realizar este cambio en la base de datos mediante una migración automática.

-   Ejecute el comando **Update-Database** en la consola del administrador de paquetes.

## <a name="summary"></a>Resumen

En este tutorial, ha visto cómo usar las migraciones automáticas para realizar cambios en el modelo en la base de datos. También ha visto cómo aplicar scaffolding y ejecutar migraciones basadas en código entre migraciones automáticas cuando necesite más control.
