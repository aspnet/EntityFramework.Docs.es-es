---
title: 'Herencia de diseñador TPT: EF6'
description: Herencia de diseñador TPT en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: efc78c31-b4ea-4ea3-a0cd-c69eb507020e
uid: ef6/modeling/designer/inheritance/tpt
ms.openlocfilehash: cb686a9f51b14651c210102845de495906318747
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620469"
---
# <a name="designer-tpt-inheritance"></a>Herencia de diseñador TPT
En este tutorial paso a paso se muestra cómo implementar la herencia de tabla por tipo (TPT) en el modelo mediante el Entity Framework Designer (EF Designer). La herencia de tabla por tipo utiliza una tabla independiente de la base de datos para mantener los datos de las propiedades no heredadas y de las propiedades de clave para cada tipo de la jerarquía de herencia.

En este tutorial, asignaremos las entidades **Course** (tipo base), **OnlineCourse** (se deriva de Course) y **OnsiteCourse**   (deriva de **Course**) a tablas con los mismos nombres. Vamos a crear un modelo a partir de la base de datos y, a continuación, modificaremos el modelo para implementar la herencia de TPT.

También puede empezar con el Model First y, a continuación, generar la base de datos a partir del modelo. El diseñador de EF usa la estrategia TPT de forma predeterminada, por lo que cualquier herencia del modelo se asignará a tablas independientes.

## <a name="other-inheritance-options"></a>Otras opciones de herencia

Tabla por jerarquía (TPH) es otro tipo de herencia en la que se utiliza una tabla de base de datos para mantener los datos de todos los tipos de entidad en una jerarquía de herencia.Para obtener información sobre cómo asignar la herencia de tabla por jerarquía con Entity Designer, vea la [herencia TPH del diseñador de EF](xref:ef6/modeling/designer/inheritance/tph). 

Tenga en cuenta que, la herencia de tipos por hormigón (TPC) y los modelos de herencia mixtos son compatibles con el tiempo de ejecución de Entity Framework pero no son compatibles con el diseñador de EF. Si desea utilizar la herencia de TPC o mixta, tiene dos opciones: usar Code First o editar manualmente el archivo EDMX. Si decide trabajar con el archivo EDMX, la ventana detalles de la asignación se colocará en "modo seguro" y no podrá usar el diseñador para cambiar las asignaciones.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará:

- Una versión reciente de Visual Studio.
- La [base de datos de ejemplo School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurar el proyecto

-   Abra Visual Studio 2012.
-   Seleccionar **archivo- &gt; nuevo- &gt; proyecto**
-   En el panel izquierdo, haga clic en **Visual C \# **y, a continuación, seleccione la plantilla de **consola** .
-   Escriba **TPTDBFirstSample**   como nombre.
-   Seleccione  **Aceptar**.

## <a name="create-a-model"></a>Creación de un modelo

-   Haga clic con el botón derecho en el proyecto en Explorador de soluciones y seleccione **agregar &gt; nuevo elemento**.
-   Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.
-   Escriba **TPTModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.
-   En el cuadro de diálogo elegir contenido del modelo, seleccione**   generar desde la base de datos**y, a continuación, haga clic en **siguiente**.
-   Haga clic en **nueva conexión**.
    En el cuadro de diálogo Propiedades de conexión, escriba el nombre del servidor (por ejemplo, **(LocalDB) \\ mssqllocaldb**), seleccione el método de autenticación, escriba **School**   como nombre de la base de datos y, a continuación, haga clic en **Aceptar**.
    El cuadro de diálogo elegir la conexión de datos se actualiza con la configuración de conexión de la base de datos.
-   En el cuadro de diálogo elija los objetos de base de datos, en el nodo tablas, seleccione las tablas **Department**, **Course, OnlineCourse y OnsiteCourse** .
-   Haga clic en **Finalizar**.

Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo. Todos los objetos que seleccionó en el cuadro de diálogo elija los objetos de base de datos se agregan al modelo.

## <a name="implement-table-per-type-inheritance"></a>Implementar la herencia de tabla por tipo

-   En la superficie de diseño, haga clic con el botón secundario en el tipo de entidad **OnlineCourse** y seleccione **propiedades**.
-   En la ventana **propiedades** , establezca la propiedad tipo base en **Course**.
-   Haga clic con el botón secundario en el tipo de entidad **OnsiteCourse** y seleccione **propiedades**.
-   En la ventana **propiedades** , establezca la propiedad tipo base en **Course**.
-   Haga clic con el botón secundario en la Asociación (la línea) entre los tipos de entidad **OnlineCourse** y **Course** .
    Seleccione **eliminar del modelo**.
-   Haga clic con el botón secundario en la asociación entre los tipos de entidad **OnsiteCourse** y **Course** .
    Seleccione **eliminar del modelo**.

Ahora eliminaremos la propiedad **CourseID** de **OnlineCourse** y **OnsiteCourse** , ya que estas clases heredan **courseid** del tipo base **Course** .

-   Haga clic con el botón secundario en la propiedad **CourseID** del tipo de entidad **OnlineCourse** y, a continuación, seleccione **eliminar del modelo**.
-   Haga clic con el botón secundario en la propiedad **CourseID** del tipo de entidad **OnsiteCourse** y, a continuación, seleccione **eliminar del modelo** .
-   Ya está implementada la herencia de tabla por tipo.

![Tabla por tipo](~/ef6/media/tpt.png)

## <a name="use-the-model"></a>Usar el modelo

Abra el archivo **Program.CS** en el que se define el método **Main** . Pegue el código siguiente en la función **Main** . El código ejecuta tres consultas. La primera consulta devuelve todos los **cursos** relacionados con el Departamento especificado. La segunda consulta utiliza el método **Intype** para devolver **OnlineCourses** relacionado con el Departamento especificado. La tercera consulta devuelve **OnsiteCourses**.

``` csharp
    using (var context = new SchoolEntities())
    {
        foreach (var department in context.Departments)
        {
            Console.WriteLine("The {0} department has the following courses:",
                               department.Name);

            Console.WriteLine("   All courses");
            foreach (var course in department.Courses )
            {
                Console.WriteLine("     {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnlineCourse>())
            {
                Console.WriteLine("   Online - {0}", course.Title);
            }

            foreach (var course in department.Courses.
                OfType<OnsiteCourse>())
            {
                Console.WriteLine("   Onsite - {0}", course.Title);
            }
        }
    }
```
