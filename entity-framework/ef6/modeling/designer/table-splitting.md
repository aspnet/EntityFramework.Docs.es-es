---
title: 'División de la tabla del diseñador: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: 452f17c3-9f26-4de4-9894-8bc036e23b0f
ms.openlocfilehash: f5e7532e6c0b473d8ce77cbd11e3e673b0af6cbe
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415014"
---
# <a name="designer-table-splitting"></a>División de tablas del diseñador
En este tutorial se muestra cómo asignar varios tipos de entidad a una sola tabla modificando un modelo con el Entity Framework Designer (EF Designer).

Uno de los motivos por los que puede querer usar la división de tablas es retrasar la carga de algunas propiedades al usar la carga diferida para cargar los objetos. Puede separar las propiedades que pueden contener una gran cantidad de datos en una entidad independiente y solo cargarlos cuando sea necesario.

En la imagen siguiente se muestran las ventanas principales que se usan al trabajar con el diseñador de EF.

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>Prerequisites

Para completar este tutorial, necesitará:

- Una versión reciente de Visual Studio.
- La [base de datos de ejemplo School](~/ef6/resources/school-database.md).

## <a name="set-up-the-project"></a>Configurar el proyecto

En este tutorial se usa Visual Studio 2012.

-   Abra Visual Studio 2012.
-   En el menú **Archivo** , seleccione **Nuevo**y haga clic en **Proyecto**.
-   En el panel izquierdo, haga clic en Visual C\#y, a continuación, seleccione la plantilla aplicación de consola.
-   Escriba **TableSplittingSample** como nombre del proyecto y haga clic en **Aceptar**.

## <a name="create-a-model-based-on-the-school-database"></a>Creación de un modelo basado en la base de datos School

-   Haga clic con el botón secundario en el nombre del proyecto en Explorador de soluciones, seleccione **Agregar**y, a continuación, haga clic en **nuevo elemento**.
-   Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.
-   Escriba **TableSplittingModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.
-   En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente.**
-   Haga clic en nueva conexión. En el cuadro de diálogo Propiedades de conexión, escriba el nombre del servidor (por ejemplo, **LocalDB)\\mssqllocaldb**), seleccione el método de autenticación, escriba **School** para el nombre de la base de datos y, a continuación, haga clic en **Aceptar**.
    El cuadro de diálogo elegir la conexión de datos se actualiza con la configuración de conexión de la base de datos.
-   En el cuadro de diálogo elija los objetos de base de datos, desactive las **tablas** nodo y Compruebe la tabla **Person** . Esto agregará la tabla especificada al modelo **School** .
-   Haga clic en **Finalizar**.

Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo. Todos los objetos que seleccionó en el cuadro de diálogo **Elija los objetos de base de datos** se agregan al modelo.

## <a name="map-two-entities-to-a-single-table"></a>Asignación de dos entidades a una sola tabla

En esta sección dividirá la entidad **Person** en dos entidades y, a continuación, las asignará a una sola tabla.

> [!NOTE]
> La entidad **Person** no contiene propiedades que puedan contener grandes cantidades de datos. simplemente se usa como ejemplo.

-   Haga clic con el botón secundario en un área vacía de la superficie de diseño, seleccione **Agregar nuevo**y haga clic en **entidad**.
    Aparecerá el cuadro de diálogo nuevo de **entidades** .
-   Escriba **HireInfo** para el **nombre de entidad** y **PersonID** para el nombre de la **propiedad de clave** .
-   Haga clic en  **Aceptar**.
-   Se crea y se muestra un nuevo tipo de entidad en la superficie de diseño.
-   Seleccione la propiedad **HireDate** del tipo de entidad **Person** y presione las teclas **Ctrl + X** .
-   Seleccione la entidad **HireInfo** y presione las teclas **Ctrl + V** .
-   Cree una asociación entre **Person** y **HireInfo**. Para ello, haga clic con el botón secundario en un área vacía de la superficie de diseño, seleccione **Agregar nuevo**y haga clic en **Asociación**.
-   Aparece el cuadro de diálogo Agregar de **Asociación** . El nombre de **PersonHireInfo** se proporciona de forma predeterminada.
-   Especifique Multiplicity **1 (uno)** en ambos extremos de la relación.
-   Presione **Aceptar**.

El paso siguiente requiere la ventana detalles de la **asignación** . Si no puede ver esta ventana, haga clic con el botón secundario en la superficie de diseño y seleccione detalles de la **asignación**.

-   Seleccione el tipo de entidad **HireInfo** y haga clic en **&lt;agregar una tabla o vista&gt;**  en la ventana detalles de la **asignación** .
-   Seleccione **persona** en la lista desplegable **&lt;agregar una tabla o vista&gt;** campo . La lista contiene tablas o vistas a las que se puede asignar la entidad seleccionada.
    Las propiedades adecuadas deben estar asignadas de forma predeterminada.

    ![Asignación](~/ef6/media/mapping.png)

-   Seleccione la Asociación **PersonHireInfo** en la superficie de diseño.
-   Haga clic con el botón secundario en la asociación en la superficie de diseño y seleccione **propiedades**.
-   En la ventana **propiedades** , seleccione la propiedad **restricciones referenciales** y haga clic en el botón de puntos suspensivos.
-   Seleccione **persona** en la lista desplegable **principal** .
-   Presione **Aceptar**.

 

## <a name="use-the-model"></a>Usar el modelo

-   Pegue el código siguiente en el método Main.

``` csharp
    using (var context = new SchoolEntities())
    {
        Person person = new Person()
        {
            FirstName = "Kimberly",
            LastName = "Morgan",
            Discriminator = "Instructor",
        };

        person.HireInfo = new HireInfo()
        {
            HireDate = DateTime.Now
        };

        // Add the new person to the context.
        context.People.Add(person);

        // Insert a row into the Person table.  
        context.SaveChanges();

        // Execute a query against the Person table.
        // The query returns columns that map to the Person entity.
        var existingPerson = context.People.FirstOrDefault();

        // Execute a query against the Person table.
        // The query returns columns that map to the Instructor entity.
        var hireInfo = existingPerson.HireInfo;

        Console.WriteLine("{0} was hired on {1}",
            existingPerson.LastName, hireInfo.HireDate);
    }
```
-   Compile y ejecute la aplicación.

Las siguientes instrucciones T-SQL se ejecutaron en la base de datos **School** como resultado de la ejecución de esta aplicación. 

-   La siguiente **inserción** se ejecutó como resultado de la ejecución del contexto. SaveChanges () y combina datos de las entidades **Person** y **HireInfo**

    ![Insertar](~/ef6/media/insert.png)

-   La siguiente **selección** se ejecutó como resultado de la ejecución del contexto. People. FirstOrDefault () y selecciona solo las columnas asignadas a **Person**

    ![Seleccione 1](~/ef6/media/select1.png)

-   La siguiente **selección** se ejecutó como resultado del acceso a la propiedad de navegación ExistingPerson. instructor y selecciona solo las columnas asignadas a **HireInfo**

    ![Seleccione 2](~/ef6/media/select2.png)
