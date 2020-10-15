---
title: Diseñador espacial-EF-EF6
description: Diseñador espacial-EF en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/spatial
ms.openlocfilehash: 298666e558ad5bb531948e6a0bbcf3d71141df0b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064918"
---
# <a name="spatial---ef-designer"></a>Diseñador espacial-EF
> [!NOTE]
> **EF5** y versiones posteriores: las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 5. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

En el tutorial de vídeo y paso a paso se muestra cómo asignar tipos espaciales con el Entity Framework Designer. También se muestra cómo usar una consulta LINQ para buscar una distancia entre dos ubicaciones.

En este tutorial se utilizará Model First para crear una nueva base de datos, pero el diseñador de EF también puede usarse con el flujo de trabajo de [Database First](xref:ef6/modeling/designer/workflows/database-first) para asignar a una base de datos existente.

La compatibilidad con tipos espaciales se presentó en Entity Framework 5. Tenga en cuenta que para usar las nuevas características, como el tipo espacial, las enumeraciones y las funciones con valores de tabla, debe tener como destino .NET Framework 4,5. Visual Studio 2012 tiene como destino .NET 4,5 de forma predeterminada.

Para usar los tipos de datos espaciales, también debe usar un proveedor de Entity Framework que tenga compatibilidad espacial. Consulte [compatibilidad con proveedores para tipos espaciales](xref:ef6/fundamentals/providers/spatial-support) para obtener más información.

Hay dos tipos de datos espaciales principales: Geography y Geometry. El tipo de datos Geography almacena los datos de datos elipsoidales (por ejemplo, las coordenadas de latitud y longitud de GPS). El tipo de datos Geometry representa el sistema de coordenadas euclidiana (plano).

## <a name="watch-the-video"></a>Visualización del vídeo
Este vídeo muestra cómo asignar tipos espaciales con el Entity Framework Designer. También se muestra cómo usar una consulta LINQ para buscar una distancia entre dos ubicaciones.

**Presentada por**: Julia Kornich

**Vídeo**: [WMV](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.wmv)  |  [MP4](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-mp4video-spatialwithdesigner.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/E/C/9/EC9E6547-8983-4C1F-A919-D33210E4B213/HDI-ITPro-MSDN-winvideo-spatialwithdesigner.zip)

## <a name="pre-requisites"></a>Requisitos previos

Deberá tener instalado Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition para completar este tutorial.

## <a name="set-up-the-project"></a>Configurar el proyecto

1.  Abra Visual Studio 2012
2.  En el menú **archivo** , seleccione **nuevo**y, a continuación, haga clic en **proyecto** .
3.  En el panel izquierdo, haga clic en **Visual C \# **y, a continuación, seleccione la plantilla de **consola** .
4.  Escriba **SpatialEFDesigner** como nombre del proyecto y haga clic en **Aceptar** .

## <a name="create-a-new-model-using-the-ef-designer"></a>Crear un nuevo modelo con EF Designer

1.  Haga clic con el botón secundario en el nombre del proyecto en Explorador de soluciones, seleccione **Agregar**y, a continuación, haga clic en **nuevo elemento** .
2.  Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.
3.  Escriba **UniversityModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar** .
4.  En la página del Asistente para Entity Data Model, seleccione **modelo vacío** en el cuadro de diálogo elegir contenido del modelo.
5.  Haga clic en **Finish** (Finalizar).

Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo.

El asistente realiza las siguientes acciones:

-   Genera el archivo EnumTestModel. edmx que define el modelo conceptual, el modelo de almacenamiento y la asignación entre ellos. Establece la propiedad de procesamiento de artefactos de metadatos del archivo. edmx que se va a incrustar en el ensamblado de salida para incrustar los archivos de metadatos generados en el ensamblado.
-   Agrega una referencia a los siguientes ensamblados: EntityFramework, System. ComponentModel. DataAnnotations y System. Data. Entity.
-   Crea archivos UniversityModel.tt y UniversityModel.Context.tt y los agrega en el archivo. edmx. Estos archivos de plantilla T4 generan el código que define el tipo derivado de DbContext y los tipos POCO que se asignan a las entidades en el modelo. edmx.

## <a name="add-a-new-entity-type"></a>Agregar un nuevo tipo de entidad

1.  Haga clic con el botón secundario en un área vacía de la superficie de diseño, seleccione **agregar- &gt; entidad**, aparecerá el cuadro de diálogo nueva entidad.
2.  Especifique **Universidad** como nombre de tipo y especifique **UniversityID** para el nombre de la propiedad de clave y deje el tipo como **Int32** .
3.  Haga clic en **Aceptar**
4.  Haga clic con el botón derecho en la entidad y seleccione **Agregar nueva &gt; propiedad-escalar** .
5.  Cambiar el nombre de la nueva propiedad a **nombre**
6.  Agregue otra propiedad escalar y cámbiela a **Ubicación** . abra el ventana Propiedades y cambie el tipo de la nueva propiedad a **geografía** .
7.  Guardar el modelo y compilar el proyecto
    > [!NOTE]
    > Al compilar, las advertencias sobre entidades y asociaciones no asignadas pueden aparecer en el Lista de errores. Puede omitir estas advertencias porque, después de elegir generar la base de datos a partir del modelo, los errores desaparecerán.

## <a name="generate-database-from-model"></a>Generar base de datos a partir del modelo

Ahora se puede generar una base de datos basada en el modelo.

1.  Haga clic con el botón secundario en un espacio vacío en la superficie de Entity Designer y seleccione **generar base de datos a partir del modelo** .
2.  Se muestra el cuadro de diálogo elegir la conexión de datos del Asistente para generar base de datos, haga clic en el botón **nueva conexión** especifique **(LocalDB) \\ mssqllocaldb** para el nombre del servidor y la **Universidad** para la base de datos y haga clic en **Aceptar** .
3.  Aparecerá un cuadro de diálogo en el que se le pregunta si desea crear una nueva base de datos, haga clic en **sí**.
4.  Haga clic en **siguiente** y el Asistente para crear bases de datos genera el lenguaje de definición de datos (DDL) para crear una base de datos. la DDL generada se muestra en el cuadro de diálogo Resumen y configuración. tenga en cuenta que el DDL no contiene una definición para una tabla que se asigna al tipo de enumeración
5.  Haga clic en **Finalizar** al hacer clic en finalizar no se ejecuta el script DDL.
6.  El Asistente para crear bases de datos hace lo siguiente: abre **UniversityModel. edmx. SQL** en el editor de T-SQL genera el esquema de almacenamiento y las secciones de asignación del archivo edmx agrega información de la cadena de conexión al archivo App.config
7.  Haga clic con el botón secundario del mouse en el editor de T-SQL y seleccione **Ejecutar** el cuadro de diálogo conectar con el servidor, escriba la información de conexión del paso 2 y haga clic en **conectar** .
8.  Para ver el esquema generado, haga clic con el botón derecho en el nombre de la base de datos en Explorador de objetos de SQL Server y seleccione **Actualizar** .

## <a name="persist-and-retrieve-data"></a>Conservar y recuperar datos

Abra el archivo Program.cs en el que se define el método Main. Agregue el código siguiente a la función main.

El código agrega dos nuevos objetos universitarios al contexto. Las propiedades espaciales se inicializan mediante el método DbGeography. FromText. El punto de geografía representado como WellKnownText se pasa al método. Después, el código guarda los datos. A continuación, se crea y se ejecuta la consulta LINQ que devuelve un objeto University donde su ubicación es más cercana a la ubicación especificada.

``` csharp
using (var context = new UniversityModelContainer())
{
    context.Universities.Add(new University()
    {
        Name = "Graphic Design Institute",
        Location = DbGeography.FromText("POINT(-122.336106 47.605049)"),
    });

    context.Universities.Add(new University()
    {
        Name = "School of Fine Art",
        Location = DbGeography.FromText("POINT(-122.335197 47.646711)"),
    });

    context.SaveChanges();

    var myLocation = DbGeography.FromText("POINT(-122.296623 47.640405)");

    var university = (from u in context.Universities
                                orderby u.Location.Distance(myLocation)
                                select u).FirstOrDefault();

    Console.WriteLine(
        "The closest University to you is: {0}.",
        university.Name);
}
```

Compile y ejecute la aplicación. El programa produce el siguiente resultado:

```console
The closest University to you is: School of Fine Art.
```

Para ver los datos de la base de datos, haga clic con el botón derecho en el nombre de la base de datos en Explorador de objetos de SQL Server y seleccione **Actualizar**. A continuación, haga clic con el botón secundario del mouse en la tabla y seleccione **ver datos**.

## <a name="summary"></a>Resumen

En este tutorial, hemos visto cómo asignar tipos espaciales mediante el Entity Framework Designer y cómo usar tipos espaciales en el código. 
