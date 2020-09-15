---
title: 'Tipos complejos: EF Designer: EF6'
description: 'Tipos complejos: EF Designer en Entity Framework 6'
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/data-types/complex-types
ms.openlocfilehash: 6ebba9ea36ddad1a25b21bf98a6ac3090c327144
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073518"
---
# <a name="complex-types---ef-designer"></a>Tipos complejos: EF Designer
En este tema se muestra cómo asignar tipos complejos con el Entity Framework Designer (EF Designer) y cómo consultar entidades que contienen propiedades de tipo complejo.

En la imagen siguiente se muestran las ventanas principales que se usan al trabajar con el diseñador de EF.

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> Al crear el modelo conceptual, es posible que aparezcan advertencias sobre entidades y asociaciones no asignadas en la Lista de errores. Puede omitir estas advertencias porque, después de elegir generar la base de datos a partir del modelo, los errores desaparecerán.

## <a name="what-is-a-complex-type"></a>Qué es un tipo complejo

Los tipos complejos son propiedades no escalares de tipos de entidad que permiten organizar las propiedades escalares dentro de las entidades. Al igual que las entidades, los tipos complejos están compuestos de propiedades escalares u otras propiedades de tipo complejo.

Al trabajar con objetos que representan tipos complejos, tenga en cuenta lo siguiente:

-   Los tipos complejos no tienen claves y, por lo tanto, no pueden existir de forma independiente. Los tipos complejos solo pueden existir como propiedades de tipos de entidad u otros tipos complejos.
-   Los tipos complejos no pueden participar en asociaciones y no pueden contener propiedades de navegación.
-   Las propiedades de tipo complejo no pueden ser **null**. Se produce una **excepción InvalidOperationException **cuando se llama a **DbContext. SaveChanges**   y se encuentra un objeto complejo null. Las propiedades escalares de objetos complejos pueden ser **null**.
-   Los tipos complejos no pueden heredar de otros tipos complejos.
-   Debe definir el tipo complejo como una **clase**. 
-   EF detecta los cambios en los miembros de un objeto de tipo complejo cuando se llama a **DbContext. DetectChanges** . Entity Framework llama automáticamente a **DetectChanges** cuando se llama a los siguientes miembros: **DbSet. Find**, **DbSet. local**, **DbSet. Remove**, **DbSet. Add**, **DbSet. Attach**, **dbcontext. SaveChanges**, **dbcontext. GetValidationErrors**, **dbcontext. entry**, **DbChangeTracker. Entries**.

## <a name="refactor-an-entitys-properties-into-new-complex-type"></a>Refactorizar las propiedades de una entidad en un nuevo tipo complejo

Si ya tiene una entidad en el modelo conceptual, puede que desee refactorizar algunas de las propiedades en una propiedad de tipo complejo.

En la superficie del diseñador, seleccione una o varias propiedades (excepto las propiedades de navegación) de una entidad, haga clic con el botón derecho y seleccione **refactorizar- &gt; desplazar al nuevo tipo complejo**.

![Refactorizar en nuevo tipo complejo](~/ef6/media/refactor.png)

Un nuevo tipo complejo con las propiedades seleccionadas se agrega al **Explorador de modelos**. Se asigna un nombre predeterminado al tipo complejo.

Una propiedad compleja del tipo que se acaba de crear reemplaza las propiedades seleccionadas. Se conservan todas las asignaciones de propiedades.

![Refactorizar en nuevo tipo complejo 2](~/ef6/media/refactor2.png)

## <a name="create-a-new-complex-type"></a>Crear un nuevo tipo complejo

También puede crear un nuevo tipo complejo que no contenga las propiedades de una entidad existente.

Haga clic con el botón secundario en la carpeta **tipos complejos** en el explorador de modelos, seleccione **AgregarNuevo tipo complejo..**.. Como alternativa, puede seleccionar la carpeta **tipos complejos** y presionar la tecla **Insertar**del   teclado.

![Agregar nuevo tipo complejo](~/ef6/media/addnewcomplextype.png)

Un nuevo tipo complejo se agrega a la carpeta con un nombre predeterminado. Ahora puede Agregar propiedades al tipo.

## <a name="add-properties-to-a-complex-type"></a>Agregar propiedades a un tipo complejo

Las propiedades de un tipo complejo pueden ser tipos escalares o los tipos complejos existentes. Sin embargo, las propiedades de tipo complejo no pueden tener las referencias circulares. Por ejemplo, un tipo complejo **OnsiteCourseDetails**   no puede tener una propiedad de tipo complejo **OnsiteCourseDetails**.

Puede agregar una propiedad a un tipo complejo de cualquiera de las formas enumeradas a continuación.

-   Haga clic con el botón secundario en un tipo complejo en el explorador de modelos, seleccione **Agregar**, seleccione **propiedad escalar**   o **propiedad compleja**y, a continuación, seleccione el tipo de propiedad que desee. Como alternativa, puede seleccionar un tipo complejo y, a continuación, presionar la tecla **Insertar**del   teclado.  

    ![Agregar propiedades a un tipo complejo](~/ef6/media/addpropertiestocomplextype.png)

    Se agrega una nueva propiedad al tipo complejo con un nombre predeterminado.

- O BIEN

-   Haga clic con el botón derecho en una propiedad de entidad en la superficie del **Diseñador de EF** y seleccione **copiar**, a continuación, haga clic con el botón secundario en el tipo complejo en el **Explorador de modelos** y seleccione **pegar**.

## <a name="rename-a-complex-type"></a>Cambiar el nombre de un tipo complejo

Al cambiar el nombre de un tipo complejo, todas las referencias al tipo se actualizan en el proyecto.

-   Haga doble clic lentamente en un tipo complejo en el **Explorador de modelos**.
    El nombre se seleccionará y se abrirá en modo de edición.

- O BIEN

-   Haga clic con el botón secundario en un tipo complejo en el **Explorador de modelos** y seleccione **cambiar nombre**.

- O BIEN

-   Seleccione un tipo complejo en el Explorador de modelos y presione la tecla F2.

- O BIEN

-   Haga clic con el botón secundario en un tipo complejo en el **Explorador de modelos** y seleccione **propiedades**. Edite el nombre en la ventana **propiedades**   .

## <a name="add-an-existing-complex-type-to-an-entity-and-map-its-properties-to-table-columns"></a>Agregar un tipo complejo existente a una entidad y asignar sus propiedades a las columnas de la tabla

1.  Haga clic con el botón secundario en una entidad, seleccione **Agregar nuevo**y seleccione **propiedad compleja**.
    Se agrega a la entidad una propiedad de tipo complejo con un nombre predeterminado. Se asigna un tipo predeterminado (que se elige entre los tipos complejos existentes) a la propiedad.
2.  Asigne el tipo deseado a la propiedad en la ventana **propiedades**   .
    Después de agregar una propiedad de tipo complejo a una entidad, debe asignar sus propiedades a las columnas de una tabla.
3.  Haga clic con el botón secundario en un tipo de entidad en la superficie de diseño o en el **Explorador de modelos**   y seleccione **asignaciones de tablas**.
    Las asignaciones de tabla se muestran en la ventana detalles de la **asignación**   .
4.  Expanda el nodo **asigna a &lt; nombre &gt; de tabla**   .
    Aparece un nodo **asignaciones de columnas**   .
5.  Expanda el nodo **asignaciones de columnas**   .
    Aparece una lista de todas las columnas de la tabla. Las propiedades predeterminadas (si existen) a las que se asignan las columnas aparecen bajo el encabezado **valor/propiedad**   .
6.  Seleccione la columna que desea asignar y, a continuación, haga clic con el botón secundario en el campo de **valor o propiedad**correspondiente   .
    Se muestra una lista desplegable de todas las propiedades escalares.
7.  Seleccione la propiedad apropiada.

    ![Asignar tipo complejo](~/ef6/media/mapcomplextype.png)

8.  Repita los pasos 6 y 7 para cada columna de tabla.

>[!NOTE]
> Para eliminar una asignación de columna, seleccione la columna que desea asignar y, a continuación, haga clic en el campo **valor/propiedad**   . A continuación, seleccione **eliminar** en la lista desplegable.

## <a name="map-a-function-import-to-a-complex-type"></a>Asignación de una importación de función a un tipo complejo

Las importaciones de función se basan en procedimientos almacenados. Para asignar una importación de función a un tipo complejo, las columnas devueltas por el procedimiento almacenado correspondiente deben coincidir con el número de propiedades del tipo complejo y deben tener tipos de almacenamiento compatibles con los tipos de propiedad.

-   Haga doble clic en una función importada que desee asignar a un tipo complejo.

    ![Importaciones de función](~/ef6/media/functionimports.png)

-   Rellene los valores para la nueva importación de función de la siguiente forma:
    -   Especifique el procedimiento almacenado para el que va a crear una importación de función en el campo **nombre del procedimiento almacenado**   . El campo es una lista desplegable que muestra todos los procedimientos almacenados del modelo de almacenamiento.
    -   Especifique el nombre de la importación de función en el campo Nombre de la **importación de función**   .
    -   Seleccione **complejo**   como el tipo de valor devuelto y, a continuación, especifique el tipo de valor devuelto complejo específico eligiendo el tipo adecuado en la lista desplegable.

        ![Editar importación de función](~/ef6/media/editfunctionimport.png)

-   Haga clic en  **Aceptar**.
    Se crea la entrada de importación de función en el modelo conceptual.

### <a name="customize-column-mapping-for-function-import"></a>Personalizar la asignación de columnas para la importación de función

-   Haga clic con el botón derecho en la importación de función en el explorador de modelos y seleccione **función importar asignación**.
    Aparece la ventana detalles de la **asignación**   y muestra la asignación predeterminada para la importación de función. Las flechas indican las asignaciones entre los valores de columna y de propiedad. De forma predeterminada, se presupone que los nombres de columna son los mismos que los nombres de propiedad del tipo complejo. Los nombres de columna predeterminados aparecen en texto gris.
-   Si es necesario, cambie los nombres de columna para que coincidan con los nombres de columna devueltos por el procedimiento almacenado que corresponden a la importación de función.

## <a name="delete-a-complex-type"></a>Eliminar un tipo complejo

Al eliminar un tipo complejo, se elimina el tipo del modelo conceptual, así como las asignaciones para todas las instancias del tipo. Sin embargo, no se actualizan las referencias al tipo. Por ejemplo, si una entidad tiene una propiedad de tipo complejo de tipo ComplexType1 y ComplexType1 se elimina en el **Explorador de modelos**, no se actualiza la propiedad de entidad correspondiente. El modelo no se validará porque contiene una entidad que hace referencia a un tipo complejo eliminado. Puede actualizar o eliminar referencias a los tipos complejos eliminados utilizando Entity Designer.

-   Haga clic con el botón secundario en un tipo complejo en el explorador de modelos y seleccione **eliminar**.

- O BIEN

-   Seleccione un tipo complejo en el Explorador de modelos y presione la tecla Suprimir del teclado.

## <a name="query-for-entities-containing-properties-of-complex-type"></a>Consultar las entidades que contienen propiedades de tipo complejo

En el código siguiente se muestra cómo ejecutar una consulta que devuelve una colección de objetos de tipo entidad que contienen una propiedad de tipo complejo.

``` csharp
    using (SchoolEntities context = new SchoolEntities())
    {
        var courses =
            from c in context.OnsiteCourses
            order by c.Details.Time
            select c;

        foreach (var c in courses)
        {
            Console.WriteLine("Time: " + c.Details.Time);
            Console.WriteLine("Days: " + c.Details.Days);
            Console.WriteLine("Location: " + c.Details.Location);
        }
    }
```
