---
title: Funciones con valores de tabla (TVF)-EF6
description: Funciones con valores de tabla (TVF) en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: f019c97b-87b0-4e93-98f4-2c539f77b2dc
uid: ef6/modeling/designer/advanced/tvfs
ms.openlocfilehash: 1575526fb46f9ddd3ad43c7c4ac0304aefa1d5d3
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620547"
---
# <a name="table-valued-functions-tvfs"></a>Funciones con valores de tabla (TVF)
> [!NOTE]
> **EF5** y versiones posteriores: las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 5. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

En el tutorial de vídeo y paso a paso se muestra cómo asignar funciones con valores de tabla (TVF) mediante el Entity Framework Designer. También se muestra cómo llamar a una TVF desde una consulta LINQ.

TVF actualmente solo se admiten en el flujo de trabajo Database First.

La compatibilidad con TVF se presentó en Entity Framework versión 5. Tenga en cuenta que para usar las nuevas características, como las funciones con valores de tabla, las enumeraciones y los tipos espaciales, debe tener como destino .NET Framework 4,5. Visual Studio 2012 tiene como destino .NET 4,5 de forma predeterminada.

TVF son muy similares a los procedimientos almacenados con una diferencia clave: el resultado de una TVF es ajustable. Esto significa que los resultados de una TVF se pueden usar en una consulta LINQ mientras que los resultados de un procedimiento almacenado no pueden.

## <a name="watch-the-video"></a>Visualización del vídeo

**Presentada por**: Julia Kornich

[WMV](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.wmv)  |  [MP4](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-mp4video-tvf.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/6/0/A/60A6E474-5EF3-4E1E-B9EA-F51D2DDB446A/HDI-ITPro-MSDN-winvideo-tvf.zip)

## <a name="pre-requisites"></a>Requisitos previos

Para completar este tutorial, necesitará:

- Instale la [base de datos School](xref:ef6/resources/school-database).

- Tener una versión reciente de Visual Studio

## <a name="set-up-the-project"></a>Configurar el proyecto

1.  Apertura de Visual Studio
2.  En el menú **archivo** , seleccione **nuevo**y, a continuación, haga clic en **proyecto** .
3.  En el panel izquierdo, haga clic en **Visual C \# **y, a continuación, seleccione la plantilla de **consola** .
4.  Escriba **TVF** como el nombre del proyecto y haga clic en **Aceptar** .

## <a name="add-a-tvf-to-the-database"></a>Agregar una función TVF a la base de datos

-   Seleccionar **vista- &gt; Explorador de objetos de SQL Server**
-   Si LocalDB no está en la lista de servidores: haga clic con el botón derecho en **SQL Server** y seleccione **Agregar SQL Server** usar la **autenticación de Windows** predeterminada para conectarse al servidor de LocalDB.
-   Expandir el nodo LocalDB
-   En el nodo bases de datos, haga clic con el botón secundario en el nodo de base de datos School y seleccione **nueva consulta.**
-   En el editor de T-SQL, pegue la siguiente definición de TVF

``` SQL
CREATE FUNCTION [dbo].[GetStudentGradesForCourse]

(@CourseID INT)

RETURNS TABLE

RETURN
    SELECT [EnrollmentID],
           [CourseID],
           [StudentID],
           [Grade]
    FROM   [dbo].[StudentGrade]
    WHERE  CourseID = @CourseID
```

-   Haga clic con el botón secundario del mouse en el editor de T-SQL y seleccione **Ejecutar** .
-   La función GetStudentGradesForCourse se agrega a la base de datos School

 

## <a name="create-a-model"></a>Creación de un modelo

1.  Haga clic con el botón secundario en el nombre del proyecto en Explorador de soluciones, seleccione **Agregar**y, a continuación, haga clic en **nuevo elemento** .
2.  Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel **plantillas** .
3.  Escriba **TVFModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar** .
4.  En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente** .
5.  Haga clic en **nueva conexión** entrar **(LocalDB) \\ mssqllocaldb** en el cuadro de texto nombre de servidor, escriba **School**   como nombre de la base de datos haga clic en **Aceptar** .
6.  En el cuadro de diálogo elija los objetos de base de datos, en el nodo **tablas**   , seleccione las tablas **Person**, **StudentGrade**y **Course**   .
7.  Seleccione la función **GetStudentGradesForCourse** que se encuentra en el nodo **procedimientos almacenados y funciones**.   tenga en cuenta que, a partir de Visual Studio 2012, Entity Designer le permite importar por lotes los procedimientos almacenados y las funciones.
8.  Haga clic en **Finalizar**
9.  Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo. Todos los objetos que seleccionó en el cuadro de diálogo **Elija los objetos de base de datos**   se agregan al modelo.
10. De forma predeterminada, la forma de resultado de cada procedimiento almacenado importado o función se convertirá automáticamente en un nuevo tipo complejo en el modelo de entidad. Pero queremos asignar los resultados de la función GetStudentGradesForCourse a la entidad StudentGrade: haga clic con el botón derecho en la superficie de diseño y seleccione **Explorador de modelos** en el explorador de modelos, seleccione **importaciones de función**y, a continuación, haga doble clic en la función **GetStudentGradesForCourse** en el cuadro de diálogo Editar importación de función, seleccione **entidades**   y elija **StudentGrade**

## <a name="persist-and-retrieve-data"></a>Conservar y recuperar datos

Abra el archivo donde se define el método Main. Agregue el código siguiente a la función main.

En el código siguiente se muestra cómo crear una consulta que utiliza una función con valores de tabla. La consulta proyecta los resultados en un tipo anónimo que contiene el título del curso relacionado y los estudiantes relacionados con un grado mayor o igual que 3,5.

``` csharp
using (var context = new SchoolEntities())
{
    var CourseID = 4022;
    var Grade = 3.5M;

    // Return all the best students in the Microeconomics class.
    var students = from s in context.GetStudentGradesForCourse(CourseID)
                            where s.Grade >= Grade
                            select new
                            {
                                s.Person,
                                s.Course.Title
                            };

    foreach (var result in students)
    {
        Console.WriteLine(
            "Couse: {0}, Student: {1} {2}",
            result.Title,  
            result.Person.FirstName,  
            result.Person.LastName);
    }
}
```

Compile y ejecute la aplicación. El programa produce el siguiente resultado:

```console
Couse: Microeconomics, Student: Arturo Anand
Couse: Microeconomics, Student: Carson Bryant
```

## <a name="summary"></a>Resumen

En este tutorial, hemos visto cómo asignar funciones con valores de tabla (TVF) mediante el Entity Framework Designer. También se muestra cómo llamar a una función TVF desde una consulta LINQ.
