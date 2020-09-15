---
title: Database First EF6
description: Database First en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/workflows/database-first
ms.openlocfilehash: 412d83b37ed5f3e2b863dca0913746e61ecdef76
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073180"
---
# <a name="database-first"></a>Database First
Este vídeo y el tutorial paso a paso proporcionan una introducción al desarrollo de Database First mediante Entity Framework. Database First permite aplicar ingeniería inversa a un modelo a partir de una base de datos existente. El modelo se almacena en un archivo EDMX (extensión. EDMX) y se puede ver y editar en el Entity Framework Designer. Las clases con las que interactúa en la aplicación se generan automáticamente a partir del archivo EDMX.

## <a name="watch-the-video"></a>Visualización del vídeo
Este vídeo proporciona una introducción al desarrollo de Database First mediante Entity Framework. Database First permite aplicar ingeniería inversa a un modelo a partir de una base de datos existente. El modelo se almacena en un archivo EDMX (extensión. EDMX) y se puede ver y editar en el Entity Framework Designer. Las clases con las que interactúa en la aplicación se generan automáticamente a partir del archivo EDMX.

**Presentado por**: [Rowan Miller](https://romiller.com/)

**Vídeo**: [WMV](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.wmv)  |  [MP4](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-mp4video-databasefirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/8/F/0/8F0B5F63-4939-4DC8-A726-FF139B37F8D8/HDI-ITPro-MSDN-winvideo-databasefirst.zip)

## <a name="pre-requisites"></a>Requisitos previos

Debe tener instalado al menos Visual Studio 2010 o Visual Studio 2012 para completar este tutorial.

Si usa Visual Studio 2010, también debe tener instalado [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) .

 

## <a name="1-create-an-existing-database"></a>1. crear una base de datos existente

Normalmente, cuando el destino es una base de datos existente, ya se creará, pero para este tutorial es necesario crear una base de datos para tener acceso a.

El servidor de base de datos que se instala con Visual Studio es diferente en función de la versión de Visual Studio que haya instalado:

-   Si usa Visual Studio 2010, va a crear una base de datos de SQL Express.
-   Si usa Visual Studio 2012, va a crear una base de datos de [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) .

 

Vamos a generar la base de datos.

-   Apertura de Visual Studio
-   **Vista &gt; Explorador de servidores**
-   Haga clic con el botón derecho en **conexiones de datos: &gt; Agregar conexión...**
-   Si no se ha conectado a una base de datos desde Explorador de servidores antes de que tenga que seleccionar Microsoft SQL Server como origen de datos

    ![Seleccionar origen de datos](~/ef6/media/selectdatasource.png)

-   Conéctese a LocalDB o a SQL Express, en función de la que haya instalado y escriba **DatabaseFirst. blog** como nombre de la base de datos.

    ![Conexión de SQL Express DF](~/ef6/media/sqlexpressconnectiondf.png)

    ![DF de conexión de LocalDB](~/ef6/media/localdbconnectiondf.png)

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
```

## <a name="2-create-the-application"></a>2. crear la aplicación

Para simplificar las cosas, vamos a crear una aplicación de consola básica que use el Database First para realizar el acceso a los datos:

-   Apertura de Visual Studio
-   **Archivo- &gt; nuevo- &gt; proyecto...**
-   Seleccionar **ventanas** en el menú izquierdo y en la **aplicación de consola**
-   Escriba **DatabaseFirstSample** como nombre
-   Seleccione **Aceptar**.

 

## <a name="3-reverse-engineer-model"></a>3. modelo de ingeniería inversa

Vamos a hacer uso de Entity Framework Designer, que se incluye como parte de Visual Studio, para crear nuestro modelo.

-   **Proyecto- &gt; Agregar nuevo elemento...**
-   Seleccione **datos** en el menú de la izquierda y, a continuación, **ADO.NET Entity Data Model**
-   Escriba **BloggingModel** como nombre y haga clic en **Aceptar** .
-   Se iniciará el **Asistente para Entity Data Model**
-   Seleccione **generar desde la base de datos** y haga clic en **siguiente** .

    ![Asistente paso 1](~/ef6/media/wizardstep1.png)

-   Seleccione la conexión a la base de datos creada en la primera sección, escriba **BloggingContext** como nombre de la cadena de conexión y haga clic en **siguiente** .

    ![Asistente paso 2](~/ef6/media/wizardstep2.png)

-   Haga clic en la casilla situada junto a "tablas" para importar todas las tablas y haga clic en "finalizar".

    ![Paso 3 del asistente](~/ef6/media/wizardstep3.png)

 

Una vez que se completa el proceso de ingeniería inversa, el nuevo modelo se agrega al proyecto y se abre para que pueda verlo en el Entity Framework Designer. También se ha agregado un archivo App.config al proyecto con los detalles de conexión de la base de datos.

![Modelo inicial](~/ef6/media/modelinitial.png)

### <a name="additional-steps-in-visual-studio-2010"></a>Pasos adicionales en Visual Studio 2010

Si está trabajando en Visual Studio 2010, hay algunos pasos adicionales que debe seguir para actualizar a la versión más reciente de Entity Framework. La actualización es importante porque le proporciona acceso a una superficie de API mejorada, que es mucho más fácil de usar, así como las correcciones de errores más recientes.

En primer lugar, necesitamos obtener la versión más reciente de Entity Framework de NuGet.

-   **Proyecto: &gt; Administrar paquetes de NuGet...** 
     *Si no tiene la opción **administrar paquetes Nuget...** , debe instalar la [versión más reciente de Nuget](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) .*
-   Seleccione la pestaña **en línea**
-   Seleccione el paquete **EntityFramework**
-   Haz clic en **Instalar**

A continuación, necesitamos cambiar nuestro modelo para generar código que haga uso de la API de DbContext, que se presentó en versiones posteriores de Entity Framework.

-   Haga clic con el botón derecho en una zona vacía del modelo en el diseñador de EF y seleccione **Agregar elemento de generación de código..** .
-   Seleccione **plantillas en línea** en el menú de la izquierda y busque **DbContext**
-   Seleccione el **generador de DbContext de EF 5. x \# para C**, escriba **BloggingModel** como nombre y haga clic en **Agregar** .

    ![Plantilla DbContext](~/ef6/media/dbcontexttemplate.png)

 

## <a name="4-reading--writing-data"></a>4. leer & escribir datos

Ahora que tenemos un modelo, es el momento de usarlo para tener acceso a algunos datos. Las clases que se van a usar para obtener acceso a los datos se generan automáticamente basándose en el archivo EDMX.

*Esta captura de pantalla es de Visual Studio 2012, si usa Visual Studio 2010, los archivos BloggingModel.tt y BloggingModel.Context.tt estarán directamente en el proyecto en lugar de anidados en el archivo EDMX.*

![Clases generadas DF](~/ef6/media/generatedclassesdf.png)

 

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
 

## <a name="5-dealing-with-database-changes"></a>5. trabajar con cambios en la base de datos

Ahora es el momento de realizar algunos cambios en el esquema de la base de datos, cuando realizamos estos cambios también necesitamos actualizar nuestro modelo para reflejar los cambios.

El primer paso consiste en realizar algunos cambios en el esquema de la base de datos. Vamos a agregar una tabla de usuarios al esquema.

-   Haga clic con el botón derecho en la base de datos **DatabaseFirst. blogs** en explorador de servidores y seleccione **nueva consulta** .
-   Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **Ejecutar** .

``` SQL
CREATE TABLE [dbo].[Users]
(
    [Username] NVARCHAR(50) NOT NULL PRIMARY KEY,  
    [DisplayName] NVARCHAR(MAX) NULL
)
```

Ahora que se ha actualizado el esquema, es el momento de actualizar el modelo con esos cambios.

-   Haga clic con el botón derecho en una zona vacía del modelo en el diseñador de EF y seleccione "actualizar modelo desde base de datos". se iniciará el Asistente para actualización.
-   En la pestaña agregar del Asistente para actualización, active la casilla situada junto a tablas, lo que indica que queremos agregar nuevas tablas del esquema.
    *La pestaña actualizar muestra las tablas existentes en el modelo en las que se comprobarán los cambios durante la actualización. Las pestañas eliminar muestran las tablas que se han quitado del esquema y que también se quitarán del modelo como parte de la actualización. La información de estas dos pestañas se detecta automáticamente y se proporciona solo con fines informativos, no se puede cambiar la configuración.*

    ![Asistente para actualizar](~/ef6/media/refreshwizard.png)

-   Haga clic en finalizar en el Asistente para actualización

 

El modelo se ha actualizado para incluir una nueva entidad de usuario que se asigna a la tabla de usuarios que se agregó a la base de datos.

![Modelo actualizado](~/ef6/media/modelupdated.png)

## <a name="summary"></a>Resumen

En este tutorial, hemos visto Database First desarrollo, que nos permitió crear un modelo en EF Designer basado en una base de datos existente. Después usamos ese modelo para leer y escribir algunos datos de la base de datos. Por último, se actualizó el modelo para reflejar los cambios realizados en el esquema de la base de datos.
