---
title: 'Code First a una nueva base de datos: EF6'
description: Code First a una nueva base de datos en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/workflows/new-database
ms.openlocfilehash: 9d0082ac5226fff066d3e18c9164e2230c84b285
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065024"
---
# <a name="code-first-to-a-new-database"></a>Code First en una nueva base de datos
Este vídeo y el tutorial paso a paso proporcionan una introducción al desarrollo de Code First que tiene como destino una nueva base de datos. Este escenario incluye establecer como destino una base de datos que no existe y Code First creará, o una base de datos vacía a la que Code First agregará nuevas tablas. Code First permite definir el modelo mediante \# las clases C o VB.net. Opcionalmente, se puede realizar una configuración adicional mediante atributos en las clases y propiedades o mediante una API fluida.

## <a name="watch-the-video"></a>Visualización del vídeo
Este vídeo proporciona una introducción al desarrollo de Code First orientado a una nueva base de datos. Este escenario incluye establecer como destino una base de datos que no existe y Code First creará, o una base de datos vacía a la que Code First agregará nuevas tablas. Code First permite definir el modelo mediante clases de C# o VB.Net. Opcionalmente, se puede realizar una configuración adicional mediante atributos en las clases y propiedades o mediante una API fluida.

**Presentado por**: [Rowan Miller](https://romiller.com/)

**Vídeo**: [WMV](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.wmv)  |  [MP4](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-mp4Video-CodeFirstNewDatabase.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/B/A/5/BA57BADE-D558-4693-8F82-29E64E4084AB/HDI-ITPro-MSDN-winvideo-CodeFirstNewDatabase.zip)

## <a name="pre-requisites"></a>Requisitos previos

Debe tener instalado al menos Visual Studio 2010 o Visual Studio 2012 para completar este tutorial.

Si usa Visual Studio 2010, también debe tener instalado [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) .

## <a name="1-create-the-application"></a>1. crear la aplicación

Para simplificar las cosas, vamos a crear una aplicación de consola básica que use Code First para realizar el acceso a los datos.

-   Apertura de Visual Studio
-   **Archivo- &gt; nuevo- &gt; proyecto...**
-   Seleccionar **ventanas** en el menú izquierdo y en la **aplicación de consola**
-   Escriba **CodeFirstNewDatabaseSample** como nombre
-   Seleccione **Aceptar**.

## <a name="2-create-the-model"></a>2. crear el modelo

Vamos a definir un modelo muy sencillo mediante clases. Simplemente los definimos en el archivo Program.cs, pero en una aplicación real dividiría las clases en archivos independientes y potencialmente un proyecto independiente.

Debajo de la definición de clase de programa en Program.cs, agregue las dos clases siguientes.

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

Observará que vamos a hacer que las dos propiedades de navegación (blog. posts y post. blog) sean virtuales. Esto habilita la característica de carga diferida de Entity Framework. La carga diferida significa que el contenido de estas propiedades se cargará automáticamente desde la base de datos cuando intente obtener acceso a ellas.

## <a name="3-create-a-context"></a>3. crear un contexto

Ahora es el momento de definir un contexto derivado, que representa una sesión con la base de datos, lo que nos permite consultar y guardar datos. Definimos un contexto que se deriva de System. Data. Entity. DbContext y expone una carpa DbSet con tipo &lt; &gt; para cada clase de nuestro modelo.

Ahora estamos empezando a usar los tipos del Entity Framework por lo que necesitamos agregar el paquete NuGet EntityFramework.

-   **Proyecto: &gt; administrar paquetes NuGet...**
    Nota: Si no tiene la **Administración de paquetes de NuGet..** . opción debe instalar la [versión más reciente de NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)
-   Seleccione la pestaña **en línea**
-   Seleccione el paquete **EntityFramework**
-   Haz clic en **Instalar**

Agregue una instrucción using para System. Data. Entity en la parte superior de Program.cs.

``` csharp
using System.Data.Entity;
```

Debajo de la clase post de Program.cs, agregue el siguiente contexto derivado.

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```

Esta es una lista completa de lo que Program.cs debería contener ahora.

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

Eso es todo el código que necesitamos para empezar a almacenar y recuperar los datos. Obviamente, hay un poco más en segundo plano y echaremos un vistazo a eso en un momento, pero primero lo veremos en acción.

## <a name="4-reading--writing-data"></a>4. leer & escribir datos

Implemente el método Main en Program.cs como se muestra a continuación. Este código crea una nueva instancia de nuestro contexto y, a continuación, la usa para insertar un nuevo blog. A continuación, usa una consulta LINQ para recuperar todos los blogs de la base de datos ordenados alfabéticamente por título.

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

Ahora puede ejecutar la aplicación y probarla.

```console
Enter a name for a new Blog: ADO.NET Blog
All blogs in the database:
ADO.NET Blog
Press any key to exit...
```
### <a name="wheres-my-data"></a>¿Dónde están mis datos?

Por Convención DbContext ha creado una base de datos automáticamente.

-   Si una instancia local de SQL Express está disponible (se instala de forma predeterminada con Visual Studio 2010), Code First ha creado la base de datos en esa instancia.
-   Si SQL Express no está disponible, Code First probará y usará [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) (instalado de forma predeterminada con Visual Studio 2012).
-   La base de datos se denomina después del nombre completo del contexto derivado, en nuestro caso, **CodeFirstNewDatabaseSample. BloggingContext**

Estas son solo las convenciones predeterminadas y hay varias maneras de cambiar la base de datos que usa Code First. hay más información disponible en la sección **cómo el DbContext detecta el modelo y la conexión a la base de datos** .
Puede conectarse a esta base de datos mediante Explorador de servidores en Visual Studio

-   **Vista &gt; Explorador de servidores**
-   Haga clic con el botón derecho en **conexiones de datos** y seleccione **Agregar conexión..** .
-   Si no se ha conectado a una base de datos desde Explorador de servidores antes de que tenga que seleccionar Microsoft SQL Server como origen de datos

    ![Seleccionar origen de datos](~/ef6/media/selectdatasource.png)

-   Conéctese a LocalDB o a SQL Express, en función de la que haya instalado.

Ahora podemos inspeccionar el esquema que Code First creado.

![Esquema inicial](~/ef6/media/schemainitial.png)

DbContext ha trabajado en las clases que se van a incluir en el modelo examinando las propiedades de DbSet que hemos definido. A continuación, usa el conjunto predeterminado de convenciones de Code First para determinar los nombres de tabla y columna, determinar los tipos de datos, buscar claves principales, etc. Más adelante en este tutorial veremos cómo puede invalidar estas convenciones.

## <a name="5-dealing-with-model-changes"></a>5. tratar con cambios en el modelo

Ahora es el momento de realizar algunos cambios en nuestro modelo, cuando realizamos estos cambios también necesitamos actualizar el esquema de la base de datos. Para ello, vamos a usar una característica denominada Migraciones de Code First o migraciones para abreviar.

Las migraciones nos permiten tener un conjunto ordenado de pasos que describen cómo actualizar (y degradar) el esquema de la base de datos. Cada uno de estos pasos, conocido como migración, contiene código que describe los cambios que se van a aplicar. 

El primer paso es habilitar Migraciones de Code First para nuestro BloggingContext.

-   **Herramientas- &gt; Administrador de paquetes de la biblioteca- &gt; consola del administrador de paquetes**
-   Ejecute el comando **Enable-Migrations** en la consola del Administrador de paquetes
-   Se ha agregado una nueva carpeta Migrations al proyecto que contiene dos elementos:
    -   **Configuration.CS** : este archivo contiene la configuración que las migraciones usarán para migrar BloggingContext. No es necesario cambiar nada para este tutorial, pero aquí es donde se pueden especificar los datos de inicialización, registrar proveedores para otras bases de datos, cambiar el espacio de nombres que se generan en las migraciones, etc.
    -   ** &lt; timestamp &gt; \_ InitialCreate.CS** : esta es la primera migración, que representa los cambios que ya se han aplicado a la base de datos para que no sea una base de datos vacía a la que se incluyan las tablas blogs y posts. Aunque se permite a Code First crear automáticamente estas tablas, ahora que hemos optado por las migraciones que se han convertido en una migración. Code First también se ha registrado en la base de datos local que ya se ha aplicado esta migración. La marca de tiempo en el nombre de archivo se usa para la ordenación.

    Ahora vamos a hacer un cambio en nuestro modelo, agregaremos una propiedad de dirección URL a la clase de blog:

``` csharp
public class Blog
{
    public int BlogId { get; set; }
    public string Name { get; set; }
    public string Url { get; set; }

    public virtual List<Post> Posts { get; set; }
}
```

-   Ejecute el comando **Add-Migration AddUrl** en la consola del administrador de paquetes.
    El comando Add-Migration comprueba los cambios desde la última migración y scaffolding una nueva migración con los cambios que se hayan encontrado. Podemos dar un nombre a las migraciones. en este caso, se llama a la migración ' AddUrl '.
    El código con scaffolding está diciendo que necesitamos agregar una columna de dirección URL, que puede contener datos de cadena, a DBO. Tabla de blogs. Si es necesario, podríamos editar el código con scaffolding, pero esto no es necesario en este caso.

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

-   Ejecute el comando **Update-Database** en la consola del administrador de paquetes. Este comando aplicará las migraciones pendientes a la base de datos. La migración de InitialCreate ya se ha aplicado, por lo que las migraciones solo aplicarán la nueva migración de AddUrl.
    Sugerencia: puede usar el modificador **– verbose** al llamar a Update-Database para ver el SQL que se ejecuta en la base de datos.

La nueva columna URL se agrega ahora a la tabla blogs en la base de datos:

![Esquema con dirección URL](~/ef6/media/schemawithurl.png)

## <a name="6-data-annotations"></a>6. anotaciones de datos

Hasta ahora, simplemente se permite a EF detectar el modelo con sus convenciones predeterminadas, pero habrá ocasiones en las que nuestras clases no sigan las convenciones y necesitamos poder realizar una configuración adicional. Hay dos opciones para ello: veremos las anotaciones de datos de esta sección y, a continuación, la API fluida en la sección siguiente.

-   Vamos a agregar una clase de usuario a nuestro modelo

``` csharp
public class User
{
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   También necesitamos agregar un conjunto a nuestro contexto derivado.

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    public DbSet<User> Users { get; set; }
}
```

-   Si intentamos agregar una migración, se obtendría un error que indica que "el*usuario de EntityType" no tiene ninguna clave definida. Defina la clave para este EntityType ".* como EF no tiene forma de saber que el nombre de usuario debe ser la clave principal del usuario.
-   Ahora vamos a usar anotaciones de datos, por lo que necesitamos agregar una instrucción using en la parte superior de Program.cs

```csharp
using System.ComponentModel.DataAnnotations;
```

-   Ahora, anote la propiedad username para identificar que es la clave principal.

``` csharp
public class User
{
    [Key]
    public string Username { get; set; }
    public string DisplayName { get; set; }
}
```

-   Usar el comando **Add-Migration adduser** para aplicar scaffolding a una migración para aplicar estos cambios a la base de datos
-   Ejecutar el comando **Update-Database** para aplicar la nueva migración a la base de datos

La nueva tabla se agrega ahora a la base de datos:

![Esquema con usuarios](~/ef6/media/schemawithusers.png)

La lista completa de anotaciones admitidas por EF es:

-   [KeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.keyattribute)
-   [StringLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute)
-   [MaxLengthAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.maxlengthattribute)
-   [ConcurrencyCheckAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute)
-   [RequiredAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute)
-   [TimestampAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute)
-   [ComplexTypeAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.complextypeattribute)
-   [ColumnAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute)
-   [TableAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.tableattribute)
-   [InversePropertyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.inversepropertyattribute)
-   [ForeignKeyAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute)
-   [DatabaseGeneratedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute)
-   [NotMappedAttribute](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.notmappedattribute)

## <a name="7-fluent-api"></a>7. API fluida

En la sección anterior, analizamos el uso de anotaciones de datos para complementar o invalidar lo que detectó la Convención. La otra forma de configurar el modelo es a través de la API fluida de Code First.

La mayoría de la configuración del modelo se puede realizar con anotaciones de datos simples. La API fluida es una forma más avanzada de especificar la configuración del modelo que abarca todo lo que pueden hacer las anotaciones de datos además de algunas configuraciones más avanzadas que no son posibles con las anotaciones de datos. Las anotaciones de datos y la API fluida se pueden usar juntas.

Para acceder a la API fluida, invalide el método OnModelCreating en DbContext. Supongamos que deseamos cambiar el nombre de la columna en la que se almacena User. DisplayName para mostrar el \_ nombre.

-   Invalide el método OnModelCreating en BloggingContext con el siguiente código

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

-   Use el comando **Add-Migration ChangeDisplayName** para aplicar scaffolding a una migración para aplicar estos cambios a la base de datos.
-   Ejecute el comando **Update-Database** para aplicar la nueva migración a la base de datos.

Ahora se ha cambiado el nombre de la columna DisplayName a nombre para mostrar \_ :

![Esquema con nombre para mostrar cambiado](~/ef6/media/schemawithdisplaynamerenamed.png)

## <a name="summary"></a>Resumen

En este tutorial, hemos examinado Code First desarrollo con una nueva base de datos. Definimos un modelo mediante clases y luego usaba ese modelo para crear una base de datos y almacenar y recuperar datos. Una vez creada la base de datos, usamos Migraciones de Code First para cambiar el esquema a medida que nuestro modelo evolucionó. También vimos cómo configurar un modelo con anotaciones de datos y la API fluida.
