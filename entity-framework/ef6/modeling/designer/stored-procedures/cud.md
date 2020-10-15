---
title: 'Procedimientos almacenados del diseñador CUD: EF6'
description: Procedimientos almacenados CUD del diseñador en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/stored-procedures/cud
ms.openlocfilehash: 167504d3abbd156f426c481c410a1953bc047de4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066127"
---
# <a name="designer-cud-stored-procedures"></a>Procedimientos almacenados CUD del diseñador

En este tutorial paso a paso se muestra cómo asignar las \\ operaciones de creación de inserción, actualización y eliminación (CUD) de un tipo de entidad a procedimientos almacenados mediante el Entity Framework Designer (EF Designer). De forma predeterminada, el Entity Framework genera automáticamente las instrucciones SQL para las operaciones CUD, pero también puede asignar procedimientos almacenados a estas operaciones.  

Tenga en cuenta que Code First no admite la asignación a funciones o procedimientos almacenados. Sin embargo, puede llamar a procedimientos almacenados o funciones mediante el método System. Data. Entity. DbSet. SqlQuery. Por ejemplo:

``` csharp
var query = context.Products.SqlQuery("EXECUTE [dbo].[GetAllProducts]");
```

## <a name="considerations-when-mapping-the-cud-operations-to-stored-procedures"></a>Consideraciones al asignar las operaciones CUD a procedimientos almacenados

Al asignar las operaciones CUD a los procedimientos almacenados, se aplican las consideraciones siguientes:

- Si va a asignar una de las operaciones de CUD a un procedimiento almacenado, asígnela todas. Si no asigna los tres, se producirá un error en las operaciones no asignadas si se ejecuta y se producirá una **UpdateException**   .
- Debe asignar todos los parámetros del procedimiento almacenado a las propiedades de la entidad.
- Si el servidor genera el valor de clave principal para la fila insertada, debe volver a asignar este valor a la propiedad clave de la entidad. En el ejemplo siguiente, el **InsertPerson**   procedimiento almacenado InsertPerson devuelve la clave principal recién creada como parte del conjunto de resultados del procedimiento almacenado. La clave principal se asigna a la clave de entidad (**PersonID**) mediante la característica ** &lt; Agregar enlaces &gt; de resultados**   del diseñador de EF.
- Las llamadas a procedimientos almacenados se asignan 1:1 con las entidades del modelo conceptual. Por ejemplo, si implementa una jerarquía de herencia en el modelo conceptual y, a continuación, asigna los procedimientos almacenados CUD para las entidades **primaria** (base) y **secundaria** (derivada), al guardar los cambios **secundarios** solo se llamará a los procedimientos almacenados del **elemento secundario**, no se desencadenarán las llamadas a procedimientos almacenados del **elemento primario**.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará:

- Una versión reciente de Visual Studio.
- La [base de datos de ejemplo School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurar el proyecto

- Abra Visual Studio 2012.
- Seleccionar **archivo- &gt; nuevo- &gt; proyecto**
- En el panel izquierdo, haga clic en **Visual C \# **y, a continuación, seleccione la plantilla de **consola** .
- Escriba **CUDSProcsSample**   como nombre.
- Seleccione  **Aceptar**.

## <a name="create-a-model"></a>Creación de un modelo

- Haga clic con el botón derecho en el nombre del proyecto en Explorador de soluciones y seleccione **agregar &gt; nuevo elemento**.
- Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.
- Escriba **CUDSProcs. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.
- En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente**.
- Haga clic en **nueva conexión**. En el cuadro de diálogo Propiedades de conexión, escriba el nombre del servidor (por ejemplo, **(LocalDB) \\ mssqllocaldb**), seleccione el método de autenticación, escriba **School**   como nombre de la base de datos y, a continuación, haga clic en **Aceptar**.
    El cuadro de diálogo elegir la conexión de datos se actualiza con la configuración de conexión de la base de datos.
- En el cuadro de diálogo elija los objetos de base de datos, en el nodo **tablas**   , seleccione la tabla **Person** .
- Además, seleccione los siguientes procedimientos almacenados en el nodo **procedimientos almacenados y funciones** : **DeletePerson**, **InsertPerson**y **UpdatePerson**.
- A partir de Visual Studio 2012, el diseñador de EF admite la importación masiva de procedimientos almacenados. La **importación de funciones y procedimientos almacenados seleccionados en el modelo de entidad** está activada de forma predeterminada. Como en este ejemplo tenemos procedimientos almacenados que insertan, actualizan y eliminan tipos de entidad, no queremos importarlos y desactivarán esta casilla.

    ![Importar procedimientos de S](~/ef6/media/importsprocs.jpg)

- Haga clic en **Finalizar**.
    Se muestra el diseñador de EF, que proporciona una superficie de diseño para editar el modelo.

## <a name="map-the-person-entity-to-stored-procedures"></a>Asignar la entidad Person a procedimientos almacenados

- Haga clic con el botón secundario en el tipo de entidad **Person**   y seleccione **asignación de procedimiento almacenado**.
- Las asignaciones de procedimientos almacenados aparecen en la ventana detalles de la **asignación**   .
- Haga clic en ** &lt; seleccionar &gt; Insertar función**.
    El campo se convierte en una lista desplegable de los procedimientos almacenados del modelo de almacenamiento que se puede asignar a tipos de entidad del modelo conceptual.
    Seleccione **InsertPerson**   en la lista desplegable.
- Aparecen las asignaciones predeterminadas entre los parámetros de procedimiento almacenado y las propiedades de entidad. Observe que las flechas indican la dirección de la asignación: se proporcionan valores de propiedad como parámetros de procedimiento almacenado.
- Haga clic en ** &lt; agregar &gt; enlace de resultados**.
- Escriba **NewPersonID**, el nombre del parámetro devuelto por el **InsertPerson**   procedimiento almacenado InsertPerson. Asegúrese de no escribir espacios iniciales ni finales.
- Presione **entrar**.
- De forma predeterminada, **NewPersonID**   se asigna a la clave de entidad **PersonID**. Observe que una flecha indica la dirección de la asignación: el valor de la columna de resultado se proporciona para la propiedad.

    ![Detalles de la asignación](~/ef6/media/mappingdetails.png)

- Haga clic en ** &lt; seleccionar &gt; función de actualización**   y seleccione **UpdatePerson**   en la lista desplegable resultante.
- Aparecen las asignaciones predeterminadas entre los parámetros de procedimiento almacenado y las propiedades de entidad.
- Haga clic en ** &lt; seleccionar &gt; eliminar función**   y seleccione **DeletePerson**   en la lista desplegable resultante.
- Aparecen las asignaciones predeterminadas entre los parámetros de procedimiento almacenado y las propiedades de entidad.

Las operaciones de inserción, actualización y eliminación del tipo de entidad **Person**   ahora están asignadas a procedimientos almacenados.

Si desea habilitar la comprobación de simultaneidad al actualizar o eliminar una entidad con procedimientos almacenados, use una de las siguientes opciones:

- Use un parámetro de **salida** para devolver el número de filas afectadas desde el procedimiento almacenado y active la casilla del **parámetro filas afectadas**   junto al nombre del parámetro. Si el valor devuelto es cero cuando se llama a la operación, se producirá una  [**OptimisticConcurrencyException**](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx)   .
- Active la casilla **usar valor original** junto a una propiedad que quiera usar para la comprobación de simultaneidad. Cuando se intenta realizar una actualización, se usará el valor de la propiedad que se leyó originalmente de la base de datos al escribir datos de nuevo en la base de datos. Si el valor no coincide con el valor de la base de datos, se producirá una **OptimisticConcurrencyException**   .

## <a name="use-the-model"></a>Usar el modelo

Abra el archivo **Program.CS** en el que se define el método **Main** . Agregue el código siguiente a la función main.

El código crea un nuevo objeto **Person** y, a continuación, actualiza el objeto y, por último, elimina el objeto.

``` csharp
    using (var context = new SchoolEntities())
    {
        var newInstructor = new Person
        {
            FirstName = "Robyn",
            LastName = "Martin",
            HireDate = DateTime.Now,
            Discriminator = "Instructor"
        }

        // Add the new object to the context.
        context.People.Add(newInstructor);

        Console.WriteLine("Added {0} {1} to the context.",
            newInstructor.FirstName, newInstructor.LastName);

        Console.WriteLine("Before SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // SaveChanges will call the InsertPerson sproc.  
        // The PersonID property will be assigned the value
        // returned by the sproc.
        context.SaveChanges();

        Console.WriteLine("After SaveChanges, the PersonID is: {0}",
            newInstructor.PersonID);

        // Modify the object and call SaveChanges.
        // This time, the UpdatePerson will be called.
        newInstructor.FirstName = "Rachel";
        context.SaveChanges();

        // Remove the object from the context and call SaveChanges.
        // The DeletePerson sproc will be called.
        context.People.Remove(newInstructor);
        context.SaveChanges();

        Person deletedInstructor = context.People.
            Where(p => p.PersonID == newInstructor.PersonID).
            FirstOrDefault();

        if (deletedInstructor == null)
            Console.WriteLine("A person with PersonID {0} was deleted.",
                newInstructor.PersonID);
    }
```

- Compile y ejecute la aplicación. El programa genera el siguiente resultado *

> [!NOTE]
> El servidor genera automáticamente PersonID, por lo que probablemente verá un número diferente *

``` Output
Added Robyn Martin to the context.
Before SaveChanges, the PersonID is: 0
After SaveChanges, the PersonID is: 51
A person with PersonID 51 was deleted.
```

Si está trabajando con la versión Ultimate de Visual Studio, puede usar IntelliTrace con el depurador para ver las instrucciones SQL que se ejecutan.

![Depurar con IntelliTrace](~/ef6/media/intellitrace.png)
