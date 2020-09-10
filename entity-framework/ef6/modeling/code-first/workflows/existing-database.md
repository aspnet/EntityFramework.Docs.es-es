---
title: 'Code First a una base de datos existente: EF6'
description: Code First a una base de datos existente en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: a7e60b74-973d-4480-868f-500a3899932e
uid: ef6/modeling/code-first/workflows/existing-database
ms.openlocfilehash: 7cb58495ea287c59f46e804882cdc089d0f09e7b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616921"
---
# <a name="code-first-to-an-existing-database"></a>Code First a una base de datos existente
Este vídeo y el tutorial paso a paso proporcionan una introducción al desarrollo de Code First que tiene como destino una base de datos existente. Code First permite definir el modelo mediante \# las clases C o VB.net. Opcionalmente, se puede realizar una configuración adicional mediante atributos en las clases y propiedades o mediante una API fluida.

## <a name="watch-the-video"></a>Visualización del vídeo
Este vídeo [ahora está disponible en Channel 9](https://channel9.msdn.com/blogs/ef/code-first-to-existing-database-ef6-1-onwards-).

## <a name="pre-requisites"></a>Requisitos previos

Para completar este tutorial, necesitará tener **Visual Studio 2012** o **Visual Studio 2013** instalado.

También necesitará la versión **6,1** (o posterior) de la **Entity Framework Tools para Visual Studio** instalado. Consulte [obtener Entity Framework](xref:ef6/fundamentals/install) para obtener información sobre la instalación de la versión más reciente del Entity Framework Tools.

## <a name="1-create-an-existing-database"></a>1. crear una base de datos existente

Normalmente, cuando el destino es una base de datos existente, ya se creará, pero para este tutorial es necesario crear una base de datos para tener acceso a.

Vamos a generar la base de datos.

-   Apertura de Visual Studio
-   **Vista &gt; Explorador de servidores**
-   Haga clic con el botón derecho en **conexiones de datos: &gt; Agregar conexión...**
-   Si no se ha conectado a una base de datos desde **Explorador de servidores** antes de que tenga que seleccionar **Microsoft SQL Server** como origen de datos

    ![Seleccionar origen de datos](~/ef6/media/selectdatasource.png)

-   Conéctese a la instancia de LocalDB y escriba **blog** como nombre de la base de datos.

    ![Conexión de LocalDB](~/ef6/media/localdbconnection.png)

-   Seleccione **Aceptar** y se le preguntará si desea crear una nueva base de datos, seleccione **sí** .

    ![Cuadro de diálogo crear base de datos](~/ef6/media/createdatabasedialog.png)

-   La nueva base de datos aparecerá ahora en Explorador de servidores, haga clic con el botón derecho en ella y seleccione **nueva consulta** .
-   Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **Ejecutar** .

``` SQL
CREATE TABLE [dbo].[Blogs] (
    [BlogId] INT IDENTITY (1, 1) NOT NULL,
    [Name] NVARCHAR (200) NULL,
    [Url]  NVARCHAR (200) NULL,
    CONSTRAINT [PK_dbo.Blogs] PRIMARY KEY CLUSTERED ([BlogId] ASC)
);

CREATE TABLE [dbo].[Posts] (
    [PostId] INT IDENTITY (1, 1) NOT NULL,
    [Title] NVARCHAR (200) NULL,
    [Content] NTEXT NULL,
    [BlogId] INT NOT NULL,
    CONSTRAINT [PK_dbo.Posts] PRIMARY KEY CLUSTERED ([PostId] ASC),
    CONSTRAINT [FK_dbo.Posts_dbo.Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [dbo].[Blogs] ([BlogId]) ON DELETE CASCADE
);

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('The Visual Studio Blog', 'http://blogs.msdn.com/visualstudio/')

INSERT INTO [dbo].[Blogs] ([Name],[Url])
VALUES ('.NET Framework Blog', 'http://blogs.msdn.com/dotnet/')
```

## <a name="2-create-the-application"></a>2. crear la aplicación

Para simplificar las cosas, crearemos una aplicación de consola básica que use Code First para realizar el acceso a los datos:

-   Apertura de Visual Studio
-   **Archivo- &gt; nuevo- &gt; proyecto...**
-   Seleccionar **ventanas** en el menú izquierdo y en la **aplicación de consola**
-   Escriba **CodeFirstExistingDatabaseSample** como nombre
-   Seleccione **Aceptar**.

 

## <a name="3-reverse-engineer-model"></a>3. modelo de ingeniería inversa

Usaremos el Entity Framework Tools para Visual Studio, que nos ayudará a generar código inicial para asignarlo a la base de datos. Estas herramientas solo generan código que también podría escribir manualmente si lo prefiere.

-   **Proyecto- &gt; Agregar nuevo elemento...**
-   Seleccione **datos** en el menú de la izquierda y, a continuación, **ADO.NET Entity Data Model**
-   Escriba **BloggingContext** como nombre y haga clic en **Aceptar** .
-   Se iniciará el **Asistente para Entity Data Model**
-   Seleccione **code First desde la base de datos** y haga clic en **siguiente** .

    ![Asistente One CFE](~/ef6/media/wizardonecfe.png)

-   Seleccione la conexión a la base de datos creada en la primera sección y haga clic en **siguiente** .

    ![Asistente dos CFE](~/ef6/media/wizardtwocfe.png)

-   Haga clic en la casilla situada junto a **tablas** para importar todas las tablas y haga clic en **Finalizar** .

    ![Asistente tres CFE](~/ef6/media/wizardthreecfe.png)

Una vez que se complete el proceso de ingeniería inversa, se agregará al proyecto un número de elementos, echemos un vistazo a lo que se ha agregado.

### <a name="configuration-file"></a>Archivo de configuración

Se ha agregado un archivo de App.config al proyecto, este archivo contiene la cadena de conexión a la base de datos existente.

``` xml
<connectionStrings>
  <add  
    name="BloggingContext"  
    connectionString="data source=(localdb)\mssqllocaldb;initial catalog=Blogging;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework"  
    providerName="System.Data.SqlClient" />
</connectionStrings>
```

*Observará también algunas otras opciones del archivo de configuración, que son valores de EF predeterminados que indican Code First dónde crear las bases de datos. Dado que estamos asignando a una base de datos existente, esta configuración se omitirá en nuestra aplicación.*

### <a name="derived-context"></a>Contexto derivado

Se ha agregado una clase **BloggingContext** al proyecto. El contexto representa una sesión con la base de datos, lo que nos permite consultar y guardar datos.
El contexto expone una **DbSet &lt; &gt; para cada** tipo de nuestro modelo. También observará que el constructor predeterminado llama a un constructor base mediante la sintaxis **Name =** . Esto indica Code First que la cadena de conexión que se va a utilizar para este contexto se debe cargar desde el archivo de configuración.

``` csharp
public partial class BloggingContext : DbContext
    {
        public BloggingContext()
            : base("name=BloggingContext")
        {
        }

        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
        }
    }
```

*Siempre debe usar la sintaxis **Name =** cuando se usa una cadena de conexión en el archivo de configuración. Esto garantiza que, si la cadena de conexión no está presente, se producirá Entity Framework en lugar de crear una nueva base de datos por Convención.*

### <a name="model-classes"></a>Clases de modelo

Por último, también se han agregado al proyecto una clase de **blog** y **publicación** . Estas son las clases de dominio que componen nuestro modelo. Verá que las anotaciones de datos se aplican a las clases para especificar la configuración en la que las convenciones de Code First no se alinearán con la estructura de la base de datos existente. Por ejemplo, verá la anotación **StringLength** en **blog.Name** y **blog. URL** , ya que tienen una longitud máxima de **200** en la base de datos (la Code First predeterminada es usar la longitud máximo que admite el proveedor de base de datos- **nvarchar (Max)** en SQL Server).

``` csharp
public partial class Blog
{
    public Blog()
    {
        Posts = new HashSet<Post>();
    }

    public int BlogId { get; set; }

    [StringLength(200)]
    public string Name { get; set; }

    [StringLength(200)]
    public string Url { get; set; }

    public virtual ICollection<Post> Posts { get; set; }
}
```

## <a name="4-reading--writing-data"></a>4. leer & escribir datos

Ahora que tenemos un modelo, es el momento de usarlo para tener acceso a algunos datos. Implemente el método **Main** en **Program.CS** como se muestra a continuación. Este código crea una nueva instancia de nuestro contexto y, a continuación, la usa para insertar un nuevo **blog**. A continuación, usa una consulta LINQ para recuperar todos los **blogs** de la base de datos ordenados alfabéticamente por **título**.

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
.NET Framework Blog
ADO.NET Blog
The Visual Studio Blog
Press any key to exit...
```
 
## <a name="what-if-my-database-changes"></a>¿Qué ocurre si mi base de datos cambia?

El Asistente para Code First a base de datos está diseñado para generar un conjunto de puntos de partida de clases que se pueden retocar y modificar. Si cambia el esquema de la base de datos, puede editar manualmente las clases o realizar otro ingeniero inverso para sobrescribir las clases.

## <a name="using-code-first-migrations-to-an-existing-database"></a>Usar Migraciones de Code First en una base de datos existente

Si desea usar Migraciones de Code First con una base de datos existente, vea [migraciones de Code First a una base de datos existente](xref:ef6/modeling/code-first/migrations/existing-database).

## <a name="summary"></a>Resumen

En este tutorial, hemos examinado Code First desarrollo con una base de datos existente. Usamos el Entity Framework Tools de Visual Studio para aplicar ingeniería inversa a un conjunto de clases que se asignan a la base de datos y que podrían usarse para almacenar y recuperar datos.
