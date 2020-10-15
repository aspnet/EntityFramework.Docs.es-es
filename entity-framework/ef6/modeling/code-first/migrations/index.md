---
title: 'Migraciones de Code First: EF6'
description: Migraciones de Code First en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/migrations/index
ms.openlocfilehash: 7b58ed82202e5a0b7e8676020e32deaac39bcf70
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064983"
---
# <a name="code-first-migrations"></a>Migraciones de Code First
Migraciones de Code First es la manera recomendada de desarrollar el esquema de base de datos de la aplicación si usa el flujo de trabajo de Code First. Migraciones proporciona un conjunto de herramientas que permiten:

1. Crear una base de datos inicial que funciona con el modelo de EF
2. Generar migraciones para realizar un seguimiento de los cambios realizados en el modelo de EF
2. Mantener actualizada la base de datos con esos cambios

En el siguiente tutorial se proporciona información general sobre Migraciones de Code First en Entity Framework. Puede realizar el tutorial completo o ir al tema que le interesa. Se tratan los siguientes temas:

## <a name="building-an-initial-model--database"></a>Creación de un modelo inicial y una base de datos

Antes de empezar a usar Migraciones, se necesitan un proyecto y un modelo de Code First con los que trabajar. En este tutorial se va a usar el modelo canónico **Blog** y **Post**.

-   Cree una nueva aplicación de consola **MigrationsDemo**
-   Agregue la versión más reciente del paquete NuGet de **EntityFramework** al proyecto
    -   **Herramientas –&gt; Administrador de paquetes de biblioteca–&gt; Consola del Administrador de paquetes**
    -   Ejecute el comando **Install-Package EntityFramework**
-   Agregue un archivo **Model.cs** con el código que se muestra a continuación. Este código define una sola clase **Blog** que conforma el modelo de dominio y una clase **BlogContext** que es el contexto de EF Code First

  ``` csharp
      using System.Data.Entity;
      using System.Collections.Generic;
      using System.ComponentModel.DataAnnotations;
      using System.Data.Entity.Infrastructure;

      namespace MigrationsDemo
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

      namespace MigrationsDemo
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

-   Ejecute la aplicación y verá que se ha creado automáticamente una base de datos **MigrationsCodeDemo.BlogContext**.

    ![Base de datos LocalDB](~/ef6/media/databaselocaldb.png)

## <a name="enabling-migrations"></a>Habilitación de migraciones

Es hora de realizar más cambios en el modelo.

-   Vamos a introducir una propiedad Url en la clase Blog.

``` csharp
    public string Url { get; set; }
```

Si volviera a ejecutar la aplicación de nuevo, aparecería una excepción InvalidOperationException con el texto *El modelo que respalda el contexto 'BlogContext' ha cambiado desde que se creó la base de datos. Considere la posibilidad de usar Migraciones de Code First para actualizar la base de datos (* [ *http://go.microsoft.com/fwlink/?LinkId=238269* ](https://go.microsoft.com/fwlink/?LinkId=238269) *).*

Como sugiere la excepción, es hora de empezar a usar Migraciones de Code First. El primer paso es habilitar las migraciones para el contexto.

-   Ejecute el comando **Enable-Migrations** en la consola del Administrador de paquetes

    Este comando ha agregado una carpeta **Migraciones** al proyecto. Esta nueva carpeta contiene dos archivos:

-   **La clase Configuration.** Esta clase permite configurar el comportamiento de Migraciones en el contexto. En este tutorial se usa la configuración predeterminada.
    *Puesto que hay un solo contexto de Code First en el proyecto, Enable-Migrations ha rellenado automáticamente el tipo de contexto al que se aplica esta configuración.*
-   **Una migración InitialCreate**. Esta migración se ha generado porque Code First ya había creado automáticamente una base de datos antes de que se habilitaran las migraciones. El código de esta migración con scaffolding representa los objetos que ya se han creado en la base de datos. En nuestro caso, es la tabla **Blog** con las columnas **BlogId** y **Name**. El nombre de archivo incluye una marca de tiempo para ayudar con el orden.
    *Si la base de datos aún no se hubiera creado, esta migración InitialCreate no se hubiera agregado al proyecto. Por el contrario, la primera vez que se llamara a Add-Migration, al código para crear estas tablas se le aplicaría scaffolding para una nueva migración.*

### <a name="multiple-models-targeting-the-same-database"></a>Varios modelos con la misma base de datos como destino

Al usar versiones anteriores a EF6, solo se puede usar un modelo de Code First para generar o administrar el esquema de una base de datos. Este es el resultado de una sola tabla **\_\_MigrationsHistory** por base de datos sin forma de identificar qué entradas pertenecen a qué modelo.

A partir de EF6, la clase **Configuration** incluye una propiedad **ContextKey**. Esta actúa como identificador único para cada modelo de Code First. Una columna correspondiente de la tabla **\_\_MigrationsHistory** permite que entradas de varios modelos compartan la tabla. De forma predeterminada, esta propiedad se establece en el nombre completo del contexto.

## <a name="generating--running-migrations"></a>Generación y ejecución de migraciones

Migraciones de Code First tiene dos comandos principales con los que se va a familiarizar.

-   **Add-Migration** aplica la técnica scaffolding a la siguiente migración en función de los cambios realizados en el modelo desde la creación de la última migración
-   **Update-Database** aplica las migraciones pendientes a la base de datos

Es necesario aplicar scaffolding a una migración para encargarse de la nueva propiedad Url que se ha agregado. El comando **Add-Migration** permite poner un nombre a estas migraciones; a la nuestra la llamaremos **AddBlogUrl**.

-   Ejecute el comando **Add-Migration AddBlogUrl** en la consola del Administrador de paquetes
-   En la carpeta **Migraciones** ahora tenemos una nueva migración **AddBlogUrl**. El nombre de archivo de la migración lleva una marca de tiempo para ayudar con el orden

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddBlogUrl : DbMigration
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

Ahora se podría editar esta migración o agregarle elementos, pero todo tiene bastante buen aspecto. Vamos a usar **Update-Database** para aplicar esta migración a la base de datos.

-   Ejecute el comando **Update-Database** en la consola del Administrador de paquetes
-   Migraciones de Code First compara las migraciones de la carpeta **Migraciones** con las que se han aplicado a la base de datos. Verá que es necesario aplicar la migración **AddBlogUrl** y ejecutarla.

La base de datos **MigrationsDemo.BlogContext** se ha actualizado para incluir la columna **Url** en la tabla **Blogs**.

## <a name="customizing-migrations"></a>Personalización de migraciones

Hasta ahora hemos generado y ejecutado una migración sin realizar ningún cambio. Ahora vamos a editar el código que se genera de forma predeterminada.

-   Es hora de realizar algunos cambios más en el modelo: vamos a agregar una nueva propiedad **Rating** a la clase **Blog**

``` csharp
    public int Rating { get; set; }
```

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

Vamos a usar el comando **Add-Migration** para permitir que Migraciones de Code First aplique scaffolding de su mejor estimación en la migración. Vamos a llamar a esta migración **AddPostClass**.

-   Ejecute el comando **Add-Migration AddPostClass** en la consola del Administrador de paquetes.

Migraciones de Code First hizo un muy buen trabajo de scaffolding de estos cambios, pero hay algunas cosas que es posible que queramos cambiar:

1.  En primer lugar, vamos a agregar un índice único a la columna **Posts.Title** (adición en la línea 22 y 29 del código siguiente).
2.  También vamos a agregar una columna **Blogs.Rating** que no admite valores null. Si hay datos existentes en la tabla, se les asigna el valor predeterminado CLR del tipo de datos para la nueva columna (Rating es entero, por lo que sería **0**). Pero queremos especificar un valor predeterminado de **3**, para que las filas existentes en la tabla **Blogs** comiencen con una clasificación decente.
    (Puede ver el valor predeterminado especificado en la línea 24 del código siguiente)

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostClass : DbMigration
        {
            public override void Up()
            {
                CreateTable(
                    "dbo.Posts",
                    c => new
                        {
                            PostId = c.Int(nullable: false, identity: true),
                            Title = c.String(maxLength: 200),
                            Content = c.String(),
                            BlogId = c.Int(nullable: false),
                        })
                    .PrimaryKey(t => t.PostId)
                    .ForeignKey("dbo.Blogs", t => t.BlogId, cascadeDelete: true)
                    .Index(t => t.BlogId)
                    .Index(p => p.Title, unique: true);

                AddColumn("dbo.Blogs", "Rating", c => c.Int(nullable: false, defaultValue: 3));
            }

            public override void Down()
            {
                DropIndex("dbo.Posts", new[] { "Title" });
                DropIndex("dbo.Posts", new[] { "BlogId" });
                DropForeignKey("dbo.Posts", "BlogId", "dbo.Blogs");
                DropColumn("dbo.Blogs", "Rating");
                DropTable("dbo.Posts");
            }
        }
    }
```

La migración editada ya está lista, así que vamos a usar **Update-Database** para actualizar la base de datos. Esta vez vamos a especificar la marca **-Verbose** para que pueda ver el SQL que ejecuta Migraciones de Code First.

-   Ejecute el comando **Update-Database –Verbose** en la consola del Administrador de paquetes.

## <a name="data-motion--custom-sql"></a>Movimiento de datos o SQL personalizado

Hasta ahora hemos examinado las operaciones de migración que no cambian ni mueven datos, y ahora vamos a ver algo que necesita mover algunos datos. Todavía no hay compatibilidad nativa con el movimiento de datos, pero podemos ejecutar algunos comandos SQL arbitrarios en cualquier punto del script.

-   Vamos a agregar una propiedad **Post.Abstract** al modelo. Luego, vamos a rellenar previamente el elemento **Abstract** de publicaciones existentes con algún texto del inicio de la columna **Content**.

``` csharp
    public string Abstract { get; set; }
```

Vamos a usar el comando **Add-Migration** para permitir que Migraciones de Code First aplique scaffolding de su mejor estimación en la migración.

-   Ejecute el comando **Add-Migration AddPostAbstract** en la consola del Administrador de paquetes.
-   La migración generada se encarga de los cambios de esquema, pero además queremos rellenar previamente la columna **Abstract** con los 100 primeros caracteres del contenido de cada publicación. Lo podemos hacer si recurrimos a SQL y ejecutamos una instrucción **UPDATE** después de agregar la columna.
    (Adición en la línea 12 del código siguiente)

``` csharp
    namespace MigrationsDemo.Migrations
    {
        using System;
        using System.Data.Entity.Migrations;

        public partial class AddPostAbstract : DbMigration
        {
            public override void Up()
            {
                AddColumn("dbo.Posts", "Abstract", c => c.String());

                Sql("UPDATE dbo.Posts SET Abstract = LEFT(Content, 100) WHERE Abstract IS NULL");
            }

            public override void Down()
            {
                DropColumn("dbo.Posts", "Abstract");
            }
        }
    }
```

La migración editada tiene buen aspecto, así que vamos a usar **Update-Database** para actualizar la base de datos. Especificamos la marca **–Verbose** para poder ver el SQL que se ejecuta en la base de datos.

-   Ejecute el comando **Update-Database –Verbose** en la consola del Administrador de paquetes.

## <a name="migrate-to-a-specific-version-including-downgrade"></a>Migrar a una versión determinada (incluido un cambio a una versión anterior)

Hasta ahora siempre hemos actualizado a la migración más reciente, pero puede haber ocasiones en que quiera cambiar a una migración anterior o posterior.

Supongamos que queremos migrar la base de datos al estado en que estaba después de ejecutar la migración **AddBlogUrl**. Podemos usar el modificador **–TargetMigration** para cambiar a esta migración anterior.

-   Ejecute el comando **Update-Database –TargetMigration: AddBlogUrl** en la consola del Administrador de paquetes.

Este comando ejecuta el script Down de las migraciones **AddBlogAbstract** y **AddPostClass**.

Si quiere revertir a una base de datos vacía, puede usar el comando **Update-Database –TargetMigration: $InitialDatabase**.

## <a name="getting-a-sql-script"></a>Obtención de un script SQL

Si otro desarrollador quiere estos cambios en su equipo, puede sincronizar una vez que se protejan los cambios en el control de código fuente. Cuando tenga las nuevas migraciones, puede ejecutar el comando Update-Database para que los cambios se apliquen localmente. Pero si queremos enviar estos cambios a un servidor de prueba y, finalmente, a producción, probablemente querremos un script SQL que podamos pasar al DBA.

-   Ejecute el comando **Update-Database**, pero esta vez especifique la marca **–Script** para que los cambios se escriban en un script en lugar de aplicarse. También se especifican una migración de origen y de destino para las que generar el script. Queremos un script que abarque desde una base de datos vacía (**$InitialDatabase**) a la versión más reciente (migración **AddPostAbstract**).
    *Si no se especifica una migración de destino, Migraciones usa la última migración como destino. Si no se especifica una migración de origen, Migraciones usa el estado actual de la base de datos.*
-   Ejecute el comando **Update-Database -Script -SourceMigration: $InitialDatabase -TargetMigration: AddPostAbstract** en la consola del Administrador de paquetes

Migraciones de Code First ejecuta la canalización de migración, pero en lugar de aplicar los cambios, los escribe en un archivo .sql. Una vez que se ha generado el script, se abre automáticamente en Visual Studio, listo para verse o guardarse.

### <a name="generating-idempotent-scripts"></a>Generación de scripts idempotentes

A partir de EF6, si especifica **–SourceMigration $InitialDatabase**, el script generado será "idempotente". Los scripts idempotentes pueden actualizar una base de datos de cualquier versión a la versión más reciente (o la versión especificada si se usa **–TargetMigration**). El script generado incluye lógica para comprobar la tabla **\_\_MigrationsHistory** y aplicar solo los cambios que no se han aplicado anteriormente.

## <a name="automatically-upgrading-on-application-startup-migratedatabasetolatestversion-initializer"></a>Actualización automática al iniciar la aplicación (inicializador MigrateDatabaseToLatestVersion)

Si va a implementar la aplicación, es posible que quiera que actualice la base de datos automáticamente (al aplicar las migraciones pendientes) cuando se inicie. Puede hacerlo si registra el inicializador de base de datos **MigrateDatabaseToLatestVersion**. Un inicializador de base de datos simplemente contiene alguna lógica que se usa para asegurarse de que la base de datos se ha configurado correctamente. Esta lógica se ejecuta la primera vez que se usa el contexto dentro del proceso de aplicación (**AppDomain**).

Se puede actualizar el archivo **Program.cs**, como se muestra a continuación, para establecer el inicializador **MigrateDatabaseToLatestVersion** para BlogContext antes de usar el contexto (línea 14). Tenga en cuenta que también debe agregar una instrucción using para el espacio de nombres **System.Data.Entity** (línea 5).

*Cuando se crea una instancia de este inicializador, se debe especificar el tipo de contexto (**BlogContext**) y la configuración de las migraciones (**Configuration**): la configuración de las migraciones es la clase que se ha agregado a la carpeta **Migraciones** al habilitar Migraciones.*

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Data.Entity;
    using MigrationsDemo.Migrations;

    namespace MigrationsDemo
    {
        class Program
        {
            static void Main(string[] args)
            {
                Database.SetInitializer(new MigrateDatabaseToLatestVersion<BlogContext, Configuration>());

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

Ahora, siempre que se ejecute la aplicación, en primer lugar comprobará si la base de datos de destino está actualizada y, si no lo está, aplicará las migraciones pendientes.
