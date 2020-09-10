---
title: 'Compatibilidad con la enumeración: EF Designer-EF6'
description: 'Compatibilidad con la enumeración: EF Designer en Entity Framework 6'
author: divega
ms.date: 10/23/2016
ms.assetid: c6ae6d8f-1ace-47db-ad47-b1718f1ba082
uid: ef6/modeling/designer/data-types/enums
ms.openlocfilehash: 316e8160cfd69d9912d63260471eee3df0eb58f6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620510"
---
# <a name="enum-support---ef-designer"></a>Compatibilidad con la enumeración: EF Designer
> [!NOTE]
> **EF5** y versiones posteriores: las características, las API, etc. que se describen en esta página se introdujeron en Entity Framework 5. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

Este tutorial de vídeo y paso a paso muestra cómo utilizar tipos de enumeración con el Entity Framework Designer. También se muestra cómo usar las enumeraciones en una consulta LINQ.

En este tutorial se utilizará Model First para crear una nueva base de datos, pero el diseñador de EF también puede usarse con el flujo de trabajo de [Database First](xref:ef6/modeling/designer/workflows/database-first) para asignar a una base de datos existente.

La compatibilidad con la enumeración se presentó en Entity Framework 5. Para usar las nuevas características, como las enumeraciones, los tipos de datos espaciales y las funciones con valores de tabla, debe tener como destino .NET Framework 4,5. Visual Studio 2012 tiene como destino .NET 4,5 de forma predeterminada.

En Entity Framework, una enumeración puede tener los siguientes tipos subyacentes: **byte**, **Int16**, **Int32**, **Int64** o **SByte**.

## <a name="watch-the-video"></a>Ver el vídeo
En este vídeo se muestra cómo utilizar tipos de enumeración con el Entity Framework Designer. También se muestra cómo usar las enumeraciones en una consulta LINQ.

**Presentada por**: Julia Kornich

**Vídeo**: [WMV](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.wmv)  |  [MP4](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-mp4video-enumwithdesiger.m4v)  |  [WMV (zip)](https://download.microsoft.com/download/0/7/A/07ADECC9-7893-415D-9F20-8B97D46A37EC/HDI-ITPro-MSDN-winvideo-enumwithdesiger.zip)

## <a name="pre-requisites"></a>Requisitos previos

Deberá tener instalado Visual Studio 2012, Ultimate, Premium, Professional o Web Express Edition para completar este tutorial.

## <a name="set-up-the-project"></a>Configurar el proyecto

1.  Abra Visual Studio 2012
2.  En el menú **archivo** , seleccione **nuevo**y, a continuación, haga clic en **proyecto** .
3.  En el panel izquierdo, haga clic en **Visual C \# **y, a continuación, seleccione la plantilla de **consola** .
4.  Escriba **EnumEFDesigner** como nombre del proyecto y haga clic en **Aceptar** .

## <a name="create-a-new-model-using-the-ef-designer"></a>Crear un nuevo modelo con EF Designer

1.  Haga clic con el botón secundario en el nombre del proyecto en Explorador de soluciones, seleccione **Agregar**y, a continuación, haga clic en **nuevo elemento** .
2.  Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.
3.  Escriba **EnumTestModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar** .
4.  En la página del Asistente para Entity Data Model, seleccione **modelo vacío** en el cuadro de diálogo elegir contenido del modelo.
5.  Haga clic en **Finish** (Finalizar).

Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo.

El asistente realiza las siguientes acciones:

-   Genera el archivo EnumTestModel. edmx que define el modelo conceptual, el modelo de almacenamiento y la asignación entre ellos. Establece la propiedad de procesamiento de artefactos de metadatos del archivo. edmx que se va a incrustar en el ensamblado de salida para incrustar los archivos de metadatos generados en el ensamblado.
-   Agrega una referencia a los siguientes ensamblados: EntityFramework, System. ComponentModel. DataAnnotations y System. Data. Entity.
-   Crea archivos EnumTestModel.tt y EnumTestModel.Context.tt y los agrega en el archivo. edmx. Estos archivos de plantilla T4 generan el código que define el tipo derivado de DbContext y los tipos POCO que se asignan a las entidades en el modelo. edmx.

## <a name="add-a-new-entity-type"></a>Agregar un nuevo tipo de entidad

1.  Haga clic con el botón secundario en un área vacía de la superficie de diseño, seleccione **agregar- &gt; entidad**, aparecerá el cuadro de diálogo nueva entidad.
2.  Especifique **Department** como nombre de tipo y especifique **departmentId** como nombre de la propiedad de clave y deje el tipo como **Int32** .
3.  Haga clic en **Aceptar**
4.  Haga clic con el botón derecho en la entidad y seleccione **Agregar nueva &gt; propiedad-escalar** .
5.  Cambiar el nombre de la nueva propiedad a **nombre**
6.  Cambie el tipo de la nueva propiedad a **Int32** (de forma predeterminada, la nueva propiedad es de tipo cadena) para cambiar el tipo, abra el ventana Propiedades y cambie la propiedad Type a **Int32** .
7.  Agregue otra propiedad escalar y cambie su nombre a **presupuesto**, cambie el tipo a **decimal** .

## <a name="add-an-enum-type"></a>Agregar un tipo de enumeración

1.  En el Entity Framework Designer, haga clic con el botón secundario en la propiedad nombre, seleccione **convertir en enumeración** .

    ![Convertir en enumeración](~/ef6/media/converttoenum.png)

2.  En el cuadro de diálogo **Agregar enumeración** , escriba **DepartmentNames** para el nombre del tipo de enumeración, cambie el tipo subyacente a **Int32**y, a continuación, agregue los siguientes miembros al tipo: Inglés, matemáticas y economía.

    ![Agregar tipo de enumeración](~/ef6/media/addenumtype.png)

3.  Presione **Aceptar**
4.  Guardar el modelo y compilar el proyecto
    > [!NOTE]
    > Al compilar, las advertencias sobre entidades y asociaciones no asignadas pueden aparecer en el Lista de errores. Puede omitir estas advertencias porque, después de elegir generar la base de datos a partir del modelo, los errores desaparecerán.

Si observa el ventana Propiedades, observará que el tipo de la propiedad Name se ha cambiado a **DepartmentNames** y que el tipo de enumeración recién agregado se ha agregado a la lista de tipos.

Si cambia a la ventana Explorador de modelos, verá que el tipo también se agregó al nodo tipos de enumeración.

![Explorador de modelos](~/ef6/media/modelbrowser.png)

>[!NOTE]
> También puede agregar nuevos tipos de enumeración desde esta ventana haciendo clic con el botón secundario del mouse y seleccionando **Agregar tipo de enumeración**. Una vez creado el tipo, aparecerá en la lista de tipos y podrá asociarlo a una propiedad.

## <a name="generate-database-from-model"></a>Generar base de datos a partir del modelo

Ahora se puede generar una base de datos basada en el modelo.

1.  Haga clic con el botón secundario en un espacio vacío en la superficie de Entity Designer y seleccione **generar base de datos a partir del modelo** .
2.  Aparece el cuadro de diálogo elegir la conexión de datos del Asistente para generar base de datos, haga clic en el botón **nueva conexión** especifique **(LocalDB) \\ mssqllocaldb** para el nombre del servidor y **EnumTest** para la base de datos y haga clic en **Aceptar** .
3.  Aparecerá un cuadro de diálogo en el que se le pregunta si desea crear una nueva base de datos, haga clic en **sí**.
4.  Haga clic en **siguiente** y el Asistente para crear bases de datos genera el lenguaje de definición de datos (DDL) para crear una base de datos. la DDL generada se muestra en el cuadro de diálogo Resumen y configuración. tenga en cuenta que el DDL no contiene una definición para una tabla que se asigna al tipo de enumeración
5.  Haga clic en **Finalizar** al hacer clic en finalizar no se ejecuta el script DDL.
6.  El Asistente para crear bases de datos hace lo siguiente: abre **EnumTest. edmx. SQL** en el editor de T-SQL genera el esquema de almacenamiento y las secciones de asignación del archivo edmx agrega información de la cadena de conexión al archivo App.config
7.  Haga clic con el botón secundario del mouse en el editor de T-SQL y seleccione **Ejecutar** el cuadro de diálogo conectar con el servidor, escriba la información de conexión del paso 2 y haga clic en **conectar** .
8.  Para ver el esquema generado, haga clic con el botón derecho en el nombre de la base de datos en Explorador de objetos de SQL Server y seleccione **Actualizar** .

## <a name="persist-and-retrieve-data"></a>Conservar y recuperar datos

Abra el archivo Program.cs en el que se define el método Main. Agregue el código siguiente a la función main. El código agrega un nuevo objeto Department al contexto. Después guarda los datos. El código también ejecuta una consulta LINQ que devuelve un departamento en el que el nombre es DepartmentNames. English.

``` csharp
using (var context = new EnumTestModelContainer())
{
    context.Departments.Add(new Department{ Name = DepartmentNames.English });

    context.SaveChanges();

    var department = (from d in context.Departments
                        where d.Name == DepartmentNames.English
                        select d).FirstOrDefault();

    Console.WriteLine(
        "DepartmentID: {0} and Name: {1}",
        department.DepartmentID,  
        department.Name);
}
```

Compile y ejecute la aplicación. El programa produce el siguiente resultado:

```console
DepartmentID: 1 Name: English
```

Para ver los datos de la base de datos, haga clic con el botón derecho en el nombre de la base de datos en Explorador de objetos de SQL Server y seleccione **Actualizar**. A continuación, haga clic con el botón secundario del mouse en la tabla y seleccione **ver datos**.

## <a name="summary"></a>Resumen

En este tutorial, hemos visto cómo asignar tipos de enumeración mediante el Entity Framework Designer y cómo usar las enumeraciones en el código. 
