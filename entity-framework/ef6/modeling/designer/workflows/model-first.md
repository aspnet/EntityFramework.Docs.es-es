---
title: Model First EF6
description: Model First en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/workflows/model-first
ms.openlocfilehash: 13c5e173182ad0781a674ce3d10f7374c4443bf9
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073167"
---
# <a name="model-first"></a>Model First
Este vídeo y el tutorial paso a paso proporcionan una introducción al desarrollo de Model First mediante Entity Framework. Model First permite crear un nuevo modelo mediante el Entity Framework Designer y, a continuación, generar un esquema de base de datos a partir del modelo. El modelo se almacena en un archivo EDMX (extensión. EDMX) y se puede ver y editar en el Entity Framework Designer. Las clases con las que interactúa en la aplicación se generan automáticamente a partir del archivo EDMX.

## <a name="watch-the-video"></a>Visualización del vídeo
Este vídeo y el tutorial paso a paso proporcionan una introducción al desarrollo de Model First mediante Entity Framework. Model First permite crear un nuevo modelo mediante el Entity Framework Designer y, a continuación, generar un esquema de base de datos a partir del modelo. El modelo se almacena en un archivo EDMX (extensión. EDMX) y se puede ver y editar en el Entity Framework Designer. Las clases con las que interactúa en la aplicación se generan automáticamente a partir del archivo EDMX.

**Presentado por**: [Rowan Miller](https://romiller.com/)

**Vídeo**: [WMV](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.wmv)  |  [MP4](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-mp4video-modelfirst.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/5/B/1/5B1C338C-AFA7-4F68-B304-48BB008146EF/HDI-ITPro-MSDN-winvideo-modelfirst.zip)

## <a name="pre-requisites"></a>Requisitos previos

Para completar este tutorial, deberá tener instalados Visual Studio 2010 o Visual Studio 2012.

Si usa Visual Studio 2010, también debe tener instalado [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) .

## <a name="1-create-the-application"></a>1. crear la aplicación

Para simplificar las cosas, vamos a crear una aplicación de consola básica que use el Model First para realizar el acceso a los datos:

-   Apertura de Visual Studio
-   **Archivo- &gt; nuevo- &gt; proyecto...**
-   Seleccionar **ventanas** en el menú izquierdo y en la **aplicación de consola**
-   Escriba **ModelFirstSample** como nombre
-   Seleccione **Aceptar**.

## <a name="2-create-model"></a>2. crear modelo

Vamos a hacer uso de Entity Framework Designer, que se incluye como parte de Visual Studio, para crear nuestro modelo.

-   **Proyecto- &gt; Agregar nuevo elemento...**
-   Seleccione **datos** en el menú de la izquierda y, a continuación, **ADO.NET Entity Data Model**
-   Escriba **BloggingModel** como nombre y haga clic en **Aceptar**. Esto iniciará el Asistente para Entity Data Model
-   Seleccione **modelo vacío** y haga clic en **Finalizar** .

    ![Crear modelo vacío](~/ef6/media/createemptymodel.png)

El Entity Framework Designer se abre con un modelo en blanco. Ahora podemos empezar a agregar entidades, propiedades y asociaciones al modelo.

-   Haga clic con el botón derecho en la superficie de diseño y seleccione **propiedades** .
-   En el ventana Propiedades cambiar el **nombre del contenedor de entidades** a **BloggingContext** 
     *este es el nombre del contexto derivado que se generará automáticamente, el contexto representa una sesión con la base de datos, lo que nos permite consultar y guardar datos* .
-   Haga clic con el botón derecho en la superficie de diseño y seleccione **Agregar nueva &gt; entidad..** .
-   Escriba **blog** como el nombre de la entidad y **BlogId** como nombre de la clave y haga clic en **Aceptar** .

    ![Agregar entidad de blog](~/ef6/media/addblogentity.png)

-   Haga clic con el botón derecho en la nueva entidad en la superficie de diseño y seleccione **Agregar nueva &gt; propiedad-escalar** **, escriba el nombre de** la propiedad.
-   Repita este proceso para agregar una propiedad de **dirección URL** .
-   Haga clic con el botón secundario en la propiedad **dirección URL** en la superficie de diseño y seleccione **propiedades**, en el ventana Propiedades cambie la configuración que **acepta valores NULL** a **true** 
     *esto nos permite guardar un blog en la base de datos sin asignarle una dirección URL* .
-   Con las técnicas que acaba de aprender, agregue una entidad **post** con una propiedad de clave **PostId**
-   Agregar propiedades escalares de **título** y de **contenido** a la entidad **post**

Ahora que tenemos un par de entidades, es el momento de agregar una asociación (o relación) entre ellas.

-   Haga clic con el botón derecho en la superficie de diseño y seleccione **Agregar nueva- &gt; asociación..** .
-   Haga que un extremo del punto de relación se envíe a un **blog** con una multiplicidad de **uno** y el otro extremo para **publicar** con una multiplicidad de **muchos** 
     *significa que un blog tiene muchas publicaciones y una publicación pertenece a un blog* .
-   Asegúrese de que el cuadro de la **entidad agregar propiedades de clave externa al "post"** está activado y haga clic en **Aceptar** .

    ![Agregar Asociación MF](~/ef6/media/addassociationmf.png)

Ahora tenemos un modelo simple en el que se puede generar una base de datos y se usa para leer y escribir datos.

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

## <a name="3-generating-the-database"></a>3. generar la base de datos

Dado nuestro modelo, Entity Framework puede calcular un esquema de base de datos que nos permitirá almacenar y recuperar datos mediante el modelo.

El servidor de base de datos que se instala con Visual Studio es diferente en función de la versión de Visual Studio que haya instalado:

-   Si usa Visual Studio 2010, va a crear una base de datos de SQL Express.
-   Si usa Visual Studio 2012, va a crear una base de datos de [LocalDB](https://msdn.microsoft.com/library/hh510202(v=sql.110).aspx) .

Vamos a generar la base de datos.

-   Haga clic con el botón secundario en la superficie de diseño y seleccione **generar base de datos del modelo..** .
-   Haga clic en **nueva conexión..** . y especifique LocalDB o SQL Express, en función de la versión de Visual Studio que use, escriba **ModelFirst. blog** como nombre de la base de datos.

    ![Conexión de LocalDB MF](~/ef6/media/localdbconnectionmf.png)

    ![Conexión de SQL Express MF](~/ef6/media/sqlexpressconnectionmf.png)

-   Seleccione **Aceptar** y se le preguntará si desea crear una nueva base de datos, seleccione **sí** .
-   Seleccione **siguiente** y el Entity Framework Designer calculará un script para crear el esquema de la base de datos.
-   Una vez que se muestre el script, haga clic en **Finalizar** y el script se agregará al proyecto y se abrirá.
-   Haga clic con el botón derecho en el script y seleccione **Ejecutar**; se le pedirá que especifique la base de datos a la que se va a conectar, especifique LocalDB o SQL Server Express, en función de la versión de Visual Studio que use.

## <a name="4-reading--writing-data"></a>4. leer & escribir datos

Ahora que tenemos un modelo, es el momento de usarlo para tener acceso a algunos datos. Las clases que se van a usar para obtener acceso a los datos se generan automáticamente basándose en el archivo EDMX.

*Esta captura de pantalla es de Visual Studio 2012, si usa Visual Studio 2010, los archivos BloggingModel.tt y BloggingModel.Context.tt estarán directamente en el proyecto en lugar de anidados en el archivo EDMX.*

![Clases generadas](~/ef6/media/generatedclasses.png)

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

## <a name="5-dealing-with-model-changes"></a>5. tratar con cambios en el modelo

Ahora es el momento de realizar algunos cambios en nuestro modelo, cuando realizamos estos cambios también necesitamos actualizar el esquema de la base de datos.

Comenzaremos agregando una nueva entidad de usuario a nuestro modelo.

-   Agregue un nuevo nombre de entidad de **usuario** **con el** nombre de usuario como el nombre de clave y la **cadena** como el tipo de propiedad de la clave.

    ![Agregar entidad de usuario](~/ef6/media/adduserentity.png)

-   Haga clic con el botón derecho en la propiedad **username** en la superficie de diseño y seleccione **propiedades**, en el ventana Propiedades cambie la configuración de **MaxLength** a **50** 
     *esto restringe los datos que se pueden almacenar en el nombre de usuario a 50 caracteres* .
-   Agregar una propiedad escalar **displayName** a la entidad **User**

Ahora tenemos un modelo actualizado y estamos preparados para actualizar la base de datos para dar cabida a nuestro nuevo tipo de entidad de usuario.

-   Haga clic con el botón secundario en la superficie de diseño y seleccione **generar base de datos a partir del modelo...**, Entity Framework calculará un script para volver a crear un esquema basado en el modelo actualizado.
-   Haga clic en **Finish** (Finalizar).
-   Puede recibir advertencias sobre cómo sobrescribir el script DDL existente y las partes de asignación y almacenamiento del modelo; haga clic en **sí** para ambas advertencias.
-   El script SQL actualizado para crear la base de datos se abre automáticamente.  
    *El script que se genera quitará todas las tablas existentes y, a continuación, volverá a crear el esquema desde el principio. Esto puede funcionar para el desarrollo local, pero no es viable para insertar cambios en una base de datos que ya se ha implementado. Si necesita publicar cambios en una base de datos que ya se ha implementado, deberá editar el script o usar una herramienta de comparación de esquemas para calcular un script de migración.*
-   Haga clic con el botón derecho en el script y seleccione **Ejecutar**; se le pedirá que especifique la base de datos a la que se va a conectar, especifique LocalDB o SQL Server Express, en función de la versión de Visual Studio que use.

## <a name="summary"></a>Resumen

En este tutorial, hemos visto Model First desarrollo, que nos permitió crear un modelo en EF Designer y, a continuación, generar una base de datos a partir de ese modelo. Después usamos el modelo para leer y escribir algunos datos de la base de datos. Por último, hemos actualizado el modelo y, a continuación, hemos recreado el esquema de la base de datos para que coincida con el modelo.
