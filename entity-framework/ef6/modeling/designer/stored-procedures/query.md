---
title: 'Procedimientos almacenados de consulta del diseñador: EF6'
description: Procedimientos almacenados de consulta del diseñador en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/query
ms.openlocfilehash: d88bf70e232f5245023ab6683290503822d07463
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066023"
---
# <a name="designer-query-stored-procedures"></a>Procedimientos almacenados de consulta del diseñador
En este tutorial paso a paso se muestra cómo usar el Entity Framework Designer (EF Designer) para importar procedimientos almacenados en un modelo y, a continuación, llamar a los procedimientos almacenados importados para recuperar los resultados. 

Tenga en cuenta que Code First no admite la asignación a funciones o procedimientos almacenados. Sin embargo, puede llamar a procedimientos almacenados o funciones mediante el método System. Data. Entity. DbSet. SqlQuery. Por ejemplo:
``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]")`;
```

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará:

- Una versión reciente de Visual Studio.
- La [base de datos de ejemplo School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurar el proyecto

-   Abra Visual Studio 2012.
-   Seleccionar **archivo- &gt; nuevo- &gt; proyecto**
-   En el panel izquierdo, haga clic en **Visual C \# **y, a continuación, seleccione la plantilla de **consola** .
-   Escriba **EFwithSProcsSample**   como nombre.
-   Seleccione  **Aceptar**.

## <a name="create-a-model"></a>Creación de un modelo

-   Haga clic con el botón derecho en el proyecto en Explorador de soluciones y seleccione **agregar &gt; nuevo elemento**.
-   Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.
-   Escriba **EFwithSProcsModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.
-   En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente**.
-   Haga clic en **nueva conexión**.  
    En el cuadro de diálogo Propiedades de conexión, escriba el nombre del servidor (por ejemplo, **(LocalDB) \\ mssqllocaldb**), seleccione el método de autenticación, escriba **School**   como nombre de la base de datos y, a continuación, haga clic en **Aceptar**.  
    El cuadro de diálogo elegir la conexión de datos se actualiza con la configuración de conexión de la base de datos.
-   En el cuadro de diálogo elija los objetos de base de datos, active la casilla **tablas**   para seleccionar todas las tablas.  
    Además, seleccione los siguientes procedimientos almacenados en el nodo **procedimientos almacenados y funciones** : **GetStudentGrades** y **GetDepartmentName**. 

    ![Importar procedimientos almacenados](~/ef6/media/import.jpg)

    *A partir de Visual Studio 2012, el diseñador de EF admite la importación masiva de procedimientos almacenados. La **importación de funciones y procedimientos almacenados seleccionados en el modelo de entidad** está activada de forma predeterminada.*
-   Haga clic en **Finalizar**.

De forma predeterminada, la forma de resultado de cada procedimiento almacenado importado o función que devuelve más de una columna se convierte automáticamente en un nuevo tipo complejo. En este ejemplo, queremos asignar los resultados de la función **GetStudentGrades** a la entidad **StudentGrade** y los resultados de **GetDepartmentName** a **None** (**ninguno** es el valor predeterminado).

Para que una importación de función devuelva un tipo de entidad, las columnas devueltas por el procedimiento almacenado correspondiente deben coincidir exactamente con las propiedades escalares del tipo de entidad devuelto. Una importación de función también puede devolver colecciones de tipos simples, tipos complejos o ningún valor.

-   Haga clic con el botón secundario en la superficie de diseño y seleccione **Explorador de modelos**.
-   En el **Explorador de modelos**, seleccione **importaciones de función**y, a continuación, haga doble clic en la función **GetStudentGrades** .
-   En el cuadro de diálogo Editar importación de función, seleccione **entidades**   y elija **StudentGrade**.  
    *La casilla **importación de función con composición** en la parte superior del cuadro de diálogo de **importaciones de funciones** le permitirá asignar funciones que admiten composición. Si activa esta casilla, solo aparecerán las funciones que admiten composición (funciones con valores de tabla) en la lista desplegable **procedimiento almacenado o nombre de función** . Si no activa esta casilla, solo se mostrarán en la lista las funciones que no admiten composición.*

## <a name="use-the-model"></a>Usar el modelo

Abra el archivo **Program.CS** en el que se define el método **Main** . Agregue el código siguiente a la función main.

El código llama a dos procedimientos almacenados: **GetStudentGrades** (devuelve **StudentGrades** para el *StudentId*especificado) y **GetDepartmentName** (devuelve el nombre del Departamento en el parámetro de salida).  

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        // Specify the Student ID.
        int studentId = 2;

        // Call GetStudentGrades and iterate through the returned collection.
        foreach (StudentGrade grade in context.GetStudentGrades(studentId))
        {
            Console.WriteLine("StudentID: {0}\tSubject={1}", studentId, grade.Subject);
            Console.WriteLine("Student grade: " + grade.Grade);
        }

        // Call GetDepartmentName.
        // Declare the name variable that will contain the value returned by the output parameter.
        ObjectParameter name = new ObjectParameter("Name", typeof(String));
        context.GetDepartmentName(1, name);
        Console.WriteLine("The department name is {0}", name.Value);

    }
```

Compile y ejecute la aplicación. El programa produce el siguiente resultado:

```console
StudentID: 2
Student grade: 4.00
StudentID: 2
Student grade: 3.50
The department name is Engineering
```

<a name="output-parameters"></a>Parámetros de salida
-----------------

Si se utilizan parámetros de salida, sus valores no estarán disponibles hasta que los resultados se hayan leído por completo. Esto se debe al comportamiento subyacente de DbDataReader, consulte [recuperación de datos mediante un DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) para obtener más detalles.
