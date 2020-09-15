---
title: 'Herencia TPH del diseñador: EF6'
description: Herencia TPH del diseñador en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/inheritance/tph
ms.openlocfilehash: 39675f9533dfef0ddad1867e3b70cd13b30708ea
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073401"
---
# <a name="designer-tph-inheritance"></a>Herencia TPH del diseñador
En este tutorial paso a paso se muestra cómo implementar la herencia de tabla por jerarquía (TPH) en el modelo conceptual con el Entity Framework Designer (EF Designer). La herencia TPH utiliza una tabla de base de datos para mantener los datos de todos los tipos de entidad en una jerarquía de herencia.

En este tutorial asignaremos la tabla Person a tres tipos de entidad: person (el tipo base), Student (derive de person) y instructor (derive de person). Vamos a crear un modelo conceptual a partir de la base de datos (Database First) y, a continuación, modificar el modelo para implementar la herencia TPH mediante el diseñador de EF.

Es posible asignar una herencia TPH mediante Model First pero tendría que escribir su propio flujo de trabajo de generación de base de datos, que es complejo. A continuación, asignaría este flujo de trabajo a la propiedad **flujo de trabajo de generación de base de datos** en el diseñador de EF. Una alternativa más sencilla es usar Code First.

## <a name="other-inheritance-options"></a>Otras opciones de herencia

Tabla por tipo (TPT) es otro tipo de herencia en la que las tablas independientes de la base de datos se asignan a las entidades que participan en la herencia. Para obtener información sobre cómo asignar la herencia de tabla por tipo con el diseñador de EF, consulte [herencia del diseñador de EF](xref:ef6/modeling/designer/inheritance/tpt).

El tiempo de ejecución de Entity Framework admite la herencia de tipo de tabla por hormigón (TPC) y los modelos de herencia mixto, pero no son compatibles con el diseñador de EF. Si desea utilizar la herencia de TPC o mixta, tiene dos opciones: usar Code First o editar manualmente el archivo EDMX. Si decide trabajar con el archivo EDMX, la ventana detalles de la asignación se colocará en "modo seguro" y no podrá usar el diseñador para cambiar las asignaciones.

## <a name="prerequisites"></a>Prerrequisitos

Para completar este tutorial, necesitará:

- Una versión reciente de Visual Studio.
- La [base de datos de ejemplo School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurar el proyecto

-   Abra Visual Studio 2012.
-   Seleccionar **archivo- &gt; nuevo- &gt; proyecto**
-   En el panel izquierdo, haga clic en **Visual C \# **y, a continuación, seleccione la plantilla de **consola** .
-   Escriba **TPHDBFirstSample**   como nombre.
-   Seleccione  **Aceptar**.

## <a name="create-a-model"></a>Creación de un modelo

-   Haga clic con el botón derecho en el nombre del proyecto en Explorador de soluciones y seleccione **agregar &gt; nuevo elemento**.
-   Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.
-   Escriba **TPHModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.
-   En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente**.
-   Haga clic en **nueva conexión**.
    En el cuadro de diálogo Propiedades de conexión, escriba el nombre del servidor (por ejemplo, **(LocalDB) \\ mssqllocaldb**), seleccione el método de autenticación, escriba **School**   como nombre de la base de datos y, a continuación, haga clic en **Aceptar**.
    El cuadro de diálogo elegir la conexión de datos se actualiza con la configuración de conexión de la base de datos.
-   En el cuadro de diálogo elija los objetos de base de datos, en el nodo tablas, seleccione la tabla **Person** .
-   Haga clic en **Finalizar**.

Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo. Todos los objetos que seleccionó en el cuadro de diálogo elija los objetos de base de datos se agregan al modelo.

Este es el aspecto de la tabla **Person** en la base de datos.

![Tabla Person](~/ef6/media/persontable.png) 

## <a name="implement-table-per-hierarchy-inheritance"></a>Implementar la herencia de tabla por jerarquía

La tabla **Person** tiene la columna **Discriminator** , que puede tener uno de dos valores: "Student" y "instructor". En función del valor, la tabla **Person** se asignará a la entidad **Student** o a la entidad **instructor** . La tabla **Person** también tiene dos columnas, **HireDate**   y **EnrollmentDate**, que deben **admitir valores NULL** porque una persona no puede ser estudiante y un instructor al mismo tiempo (al menos no en este tutorial).

### <a name="add-new-entities"></a>Agregar nuevas entidades

-   Agregue una nueva entidad.
    Para ello, haga clic con el botón secundario en un espacio vacío de la superficie de diseño del Entity Framework Designer y seleccione **Add- &gt; Entity**.
-   Escriba **instructor**   como nombre de la **entidad**   y seleccione **persona**   en la lista desplegable para el **tipo base**.
-   Haga clic en  **Aceptar**.
-   Agregue otra entidad nueva. Escriba **Student**como   nombre de la **entidad**   y seleccione **persona**   en la lista desplegable para el **tipo base**.

Se han agregado dos nuevos tipos de entidad a la superficie de diseño. Una flecha señala desde los nuevos tipos de entidad hasta el tipo de entidad **Person**   ; Esto indica que **Person**   es el tipo base para los nuevos tipos de entidad.

-   Haga clic con el botón secundario en la propiedad **HireDate**   de la entidad **Person**   . Seleccione **cortar** (o use la tecla Ctrl-X).
-   Haga clic con el botón secundario en la entidad **instructor**   y seleccione **pegar** (o use la tecla Ctrl-V).
-   Haga clic con el botón secundario en la propiedad **HireDate**   y seleccione **propiedades**.
-   En la ventana **propiedades**   , establezca la propiedad **Nullable**   en **false**.
-   Haga clic con el botón secundario en la propiedad **EnrollmentDate**   de la entidad **Person**   . Seleccione **cortar** (o use la tecla Ctrl-X).
-   Haga clic con el botón secundario en la entidad **Student** y seleccione **pegar (o use la tecla Ctrl-V).**
-   Seleccione la propiedad **EnrollmentDate**   y establezca la propiedad **Nullable**   en **false**.
-   Seleccione el **Person**   tipo de entidad person. En la ventana **propiedades**   , establezca su propiedad **abstract**   en **true**.
-   Elimine la propiedad **Discriminator** de **Person**. La razón por la que se debe eliminar se explica en la sección siguiente.

### <a name="map-the-entities"></a>Asignación de las entidades

-   Haga clic con el botón secundario en el **instructor** y seleccione **asignación de tabla.**
    La entidad instructor está seleccionada en la ventana detalles de la asignación.
-   Haga clic en ** &lt; Agregar una tabla &gt; o una vista**   en la ventana detalles de la **asignación**   .
    El campo ** &lt; Agregar una tabla o &gt; vista**   se convierte en una lista desplegable de tablas o vistas a las que se puede asignar la entidad seleccionada.
-   Seleccione **persona**   en la lista desplegable.
-   La ventana detalles de la **asignación**   se actualiza con las asignaciones de columnas predeterminadas y una opción para agregar una condición.
-   Haga clic en ** &lt; Agregar una &gt; condición**.
    El campo ** &lt; Agregar una &gt; condición**   se convierte en una lista desplegable de columnas para las que se pueden establecer condiciones.
-   Seleccione **discriminador**   en la lista desplegable.
-   En la **Operator**   columna operador de la ventana detalles de la **asignación**   , seleccione = en la lista desplegable.
-   En la columna **valor/propiedad** , escriba **instructor**. El resultado final debería ser similar al siguiente:

    ![Detalles de la asignación](~/ef6/media/mappingdetails2.png)

-   Repita estos pasos para el **Student**   tipo de entidad Student, pero haga que la condición sea igual que el valor **Student** .  
    *La razón por la que queríamos quitar la propiedad **Discriminator** , es que no se puede asignar más de una vez una columna de tabla. Esta columna se utilizará para la asignación condicional, por lo que no se puede usar también para la asignación de propiedades. La única manera en que se puede usar para ambos, si una condición usa una comparación **is null**   o **is not null**   .*

Ahora se implementa la herencia de tabla por jerarquía.

![TPH final](~/ef6/media/finaltph.png)

## <a name="use-the-model"></a>Usar el modelo

Abra el archivo **Program.CS** en el que se define el método **Main** . Pegue el código siguiente en la función **Main** . El código ejecuta tres consultas. La primera consulta devuelve todos los objetos **Person** . La segunda consulta utiliza el método **Intype** para devolver los objetos **instructor** . La tercera consulta utiliza el método **Intype** para devolver objetos **Student** .

``` csharp
    using (var context = new SchoolEntities())
    {
        Console.WriteLine("All people:");
        foreach (var person in context.People)
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Instructors only: ");
        foreach (var person in context.People.OfType<Instructor>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }

        Console.WriteLine("Students only: ");
        foreach (var person in context.People.OfType<Student>())
        {
            Console.WriteLine("    {0} {1}", person.FirstName, person.LastName);
        }
    }
```
