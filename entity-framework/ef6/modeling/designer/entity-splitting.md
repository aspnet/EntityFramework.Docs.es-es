---
title: 'División de entidades del diseñador: EF6'
description: División de entidades del diseñador en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: aa2dd48a-1f0e-49dd-863d-d6b4f5834832
uid: ef6/modeling/designer/entity-splitting
ms.openlocfilehash: d3be4e54d4bcd3ca253d8970c612acddf48dbaf4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620525"
---
# <a name="designer-entity-splitting"></a>División de entidades del diseñador
En este tutorial se muestra cómo asignar un tipo de entidad a dos tablas modificando un modelo con el Entity Framework Designer (EF Designer). Puede asignar una entidad a varias tablas cuando estas comparten una clave común. Los conceptos que se aplican en la asignación de un tipo de entidad a dos tablas se extienden con facilidad a la asignación de un tipo de entidad a más de dos tablas.

En la imagen siguiente se muestran las ventanas principales que se usan al trabajar con el diseñador de EF.

![EF Designer](~/ef6/media/efdesigner.png)

## <a name="prerequisites"></a>Requisitos previos

Visual Studio 2012 o Visual Studio 2010, Ultimate, Premium, Professional o Web Express.

## <a name="create-the-database"></a>Crear la base de datos

El servidor de base de datos que se instala con Visual Studio es diferente en función de la versión de Visual Studio que haya instalado:

-   Si usa Visual Studio 2012, va a crear una base de datos de LocalDB.
-   Si usa Visual Studio 2010, va a crear una base de datos de SQL Express.

En primer lugar, vamos a crear una base de datos con dos tablas que se van a combinar en una sola entidad.

-   Apertura de Visual Studio
-   **Vista &gt; Explorador de servidores**
-   Haga clic con el botón derecho en **conexiones de datos: &gt; Agregar conexión...**
-   Si no se ha conectado a una base de datos desde Explorador de servidores antes de que tenga que seleccionar **Microsoft SQL Server** como origen de datos
-   Conéctese a LocalDB o a SQL Express, en función de la que haya instalado.
-   Escriba **EntitySplitting** como nombre de la base de datos
-   Seleccione **Aceptar** y se le preguntará si desea crear una nueva base de datos, seleccione **sí** .
-   La nueva base de datos aparecerá ahora en Explorador de servidores
-   Si usa Visual Studio 2012
    -   Haga clic con el botón derecho en la base de datos en el Explorador de servidores y seleccione **Nueva consulta**
    -   Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **Ejecutar** .
-   Si usa Visual Studio 2010
    -   Seleccionar **datos: &gt; Editor de Transact SQL: &gt; nueva conexión de consulta...**
    -   Escriba **. \\ SQLEXPRESS** como nombre del servidor y haga clic en **Aceptar**
    -   Seleccione la base de datos **EntitySplitting** en la lista desplegable de la parte superior del editor de consultas.
    -   Copie el siguiente código SQL en la nueva consulta, haga clic con el botón derecho en la consulta y seleccione **ejecutar SQL** .

``` SQL
CREATE TABLE [dbo].[Person] (
[PersonId] INT IDENTITY (1, 1) NOT NULL,
[FirstName] NVARCHAR (200) NULL,
[LastName] NVARCHAR (200) NULL,
CONSTRAINT [PK_Person] PRIMARY KEY CLUSTERED ([PersonId] ASC)
);

CREATE TABLE [dbo].[PersonInfo] (
[PersonId] INT NOT NULL,
[Email] NVARCHAR (200) NULL,
[Phone] NVARCHAR (50) NULL,
CONSTRAINT [PK_PersonInfo] PRIMARY KEY CLUSTERED ([PersonId] ASC),
CONSTRAINT [FK_Person_PersonInfo] FOREIGN KEY ([PersonId]) REFERENCES [dbo].[Person] ([PersonId]) ON DELETE CASCADE
);
```

## <a name="create-the-project"></a>Crear el proyecto

-   En el menú **Archivo** , seleccione **Nuevo**y haga clic en **Proyecto**.
-   En el panel izquierdo, haga clic en **Visual \# C**y, a continuación, seleccione la plantilla **aplicación de consola** .
-   Escriba **MapEntityToTablesSample** como nombre del proyecto y haga clic en **Aceptar**.
-   Haga clic en **no** si se le pide que guarde la consulta SQL creada en la primera sección.

## <a name="create-a-model-based-on-the-database"></a>Crear un modelo basado en la base de datos

-   Haga clic con el botón secundario en el nombre del proyecto en Explorador de soluciones, seleccione **Agregar**y, a continuación, haga clic en **nuevo elemento**.
-   Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.
-   Escriba **MapEntityToTablesModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.
-   En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente.**
-   Seleccione la conexión **EntitySplitting** en el menú desplegable y haga clic en **siguiente**.
-   En el cuadro de diálogo elija los objetos de base de datos, active la casilla situada junto al nodo **tablas**   .
    Se agregarán todas las tablas de la base de datos **EntitySplitting** al modelo.
-   Haga clic en **Finalizar**.

Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo.

## <a name="map-an-entity-to-two-tables"></a>Asignación de una entidad a dos tablas

En este paso, actualizaremos el tipo de entidad **Person** para combinar datos de las tablas **Person** y **PersonInfo** .

-   Seleccione las propiedades de **correo electrónico**   y **teléfono** de la entidad **PersonInfo **y presione las teclas **Ctrl + X** .
-   Seleccione la entidad **Person **y presione las teclas **Ctrl + V** .
-   En la superficie de diseño, seleccione la entidad **PersonInfo**   y presione el botón **eliminar** en el teclado.
-   Haga clic en **no** cuando se le pregunte si desea quitar la tabla **PersonInfo** del modelo, vamos a asignarla a la entidad **Person** .

    ![Eliminar tablas](~/ef6/media/deletetables.png)

Los pasos siguientes requieren la ventana detalles de la **asignación**   . Si no puede ver esta ventana, haga clic con el botón secundario en la superficie de diseño y seleccione detalles de la **asignación**.

-   Seleccione el **Person**   tipo de entidad person y haga clic en ** &lt; Agregar una &gt; tabla o vista**   en la ventana detalles de la **asignación**   .
-   Seleccione **PersonInfo **   en la lista desplegable.
    La ventana detalles de la **asignación**   se actualiza con las asignaciones de columnas predeterminadas, que son correctas para nuestro escenario.

El **Person**   tipo de entidad Person ahora se asigna a las tablas **Person**   y **PersonInfo**   .

![Asignación 2](~/ef6/media/mapping2.png)

## <a name="use-the-model"></a>Usar el modelo

-   Pegue el código siguiente en el método Main.

``` csharp
    using (var context = new EntitySplittingEntities())
    {
        var person = new Person
        {
            FirstName = "John",
            LastName = "Doe",
            Email = "john@example.com",
            Phone = "555-555-5555"
        };

        context.People.Add(person);
        context.SaveChanges();

        foreach (var item in context.People)
        {
            Console.WriteLine(item.FirstName);
        }
    }
```

-   Compile y ejecute la aplicación.

Las siguientes instrucciones T-SQL se ejecutaron en la base de datos como resultado de la ejecución de esta aplicación. 

-   Las dos instrucciones **Insert** siguientes se ejecutaron como resultado de la ejecución del contexto. SaveChanges (). Toman los datos de la entidad **Person** y los dividen entre las tablas **Person** y **PersonInfo** .

    ![Insertar 1](~/ef6/media/insert1.png)

    ![Insertar 2](~/ef6/media/insert2.png)
-   La siguiente **selección** se ejecutó como resultado de enumerar las personas en la base de datos. Combina los datos de la tabla **Person** y **PersonInfo** .

    ![Selección de la combinación de datos person y PersonInfo](~/ef6/media/select.png)
