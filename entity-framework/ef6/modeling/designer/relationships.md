---
title: 'Relaciones: EF Designer: EF6'
description: 'Relaciones: EF Designer en Entity Framework 6'
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/relationships
ms.openlocfilehash: 0211eb1916a7372874f0b7812c512e6eeba0974f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066140"
---
# <a name="relationships---ef-designer"></a>Relaciones: EF Designer
> [!NOTE]
> En esta página se proporciona información sobre cómo configurar las relaciones en el modelo mediante el diseñador de EF. Para obtener información general sobre las relaciones en EF y cómo obtener acceso a los datos y manipularlos mediante relaciones, vea [relaciones & propiedades de navegación](xref:ef6/fundamentals/relationships).

Las asociaciones definen las relaciones entre los tipos de entidad de un modelo. En este tema se muestra cómo asignar asociaciones con el Entity Framework Designer (EF Designer). En la imagen siguiente se muestran las ventanas principales que se usan al trabajar con el diseñador de EF.

![EF Designer](~/ef6/media/efdesigner.png)

> [!NOTE]
> Al crear el modelo conceptual, es posible que aparezcan advertencias sobre entidades y asociaciones no asignadas en la Lista de errores. Puede omitir estas advertencias porque, después de elegir generar la base de datos a partir del modelo, los errores desaparecerán.

## <a name="associations-overview"></a>Información general sobre asociaciones

Al diseñar el modelo mediante el diseñador de EF, un archivo. edmx representa el modelo. En el archivo. edmx, un elemento **Association** define una relación entre dos tipos de entidad. Una asociación debe especificar los tipos de entidad que están implicados en la relación y el posible número de tipos de entidad en cada extremo de la relación, que se conoce como multiplicidad. La multiplicidad de un extremo de asociación puede tener un valor de uno (1), cero o uno (0.. 1) o varios ( \* ). Esta información se especifica en dos elementos **End** secundarios.

En tiempo de ejecución, se puede tener acceso a las instancias de tipo de entidad en un extremo de una asociación a través de las propiedades de navegación o las claves externas (si elige exponer las claves externas en las entidades). Con las claves externas expuestas, la relación entre las entidades se administra con un elemento **ReferentialConstraint** (un elemento secundario del elemento **Association** ). Se recomienda exponer siempre las claves externas para las relaciones de las entidades.

> [!NOTE]
> En varios a varios ( \* : \* ) no se pueden agregar claves externas a las entidades. En una \* relación de: \* , la información de asociación se administra con un objeto independiente.

Para obtener información sobre los elementos CSDL (**ReferentialConstraint**, **Association**, etc.), vea la [especificación de CSDL](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).

## <a name="create-and-delete-associations"></a>Crear y eliminar asociaciones

La creación de una asociación con el diseñador de EF actualiza el contenido del modelo del archivo. edmx. Después de crear una asociación, debe crear las asignaciones para la Asociación (que se describen más adelante en este tema).

> [!NOTE]
> En esta sección se supone que ya ha agregado las entidades con las que desea crear una asociación entre el modelo.

### <a name="to-create-an-association"></a>Para crear una asociación

1.  Haga clic con el botón secundario en un área vacía de la superficie de diseño, seleccione **Agregar nuevo**y seleccione **asociación..**..
2.  Rellene la configuración de la asociación en el cuadro de diálogo **Agregar Asociación** .

    ![Agregar Asociación](~/ef6/media/addassociation.png)

    > [!NOTE]
    > Puede optar por no agregar propiedades de navegación ni propiedades de clave externa a las entidades en los extremos de la asociación si desactiva las casillas **propiedad de navegación **y **Agregar propiedades de clave externa a la &lt; &gt; entidad nombre de tipo de entidad **. Si agrega solo una propiedad de navegación, la asociación se podrá recorrer en una única dirección. Si no agrega ninguna propiedad de navegación, deberá agregar propiedades de clave externa para poder tener acceso a las entidades situadas en los extremos de la asociación.
    
3.  Haga clic en  **Aceptar**.

### <a name="to-delete-an-association"></a>Para eliminar una asociación

Para eliminar una asociación, realice una de las acciones siguientes:

-   Haga clic con el botón derecho en la asociación en la superficie del diseñador EF y seleccione **eliminar**.

- O BIEN

-   Seleccione una o más asociaciones y presione la tecla Supr.

## <a name="include-foreign-key-properties-in-your-entities-referential-constraints"></a>Incluir propiedades de clave externa en las entidades (restricciones referenciales)

Se recomienda exponer siempre las claves externas para las relaciones de las entidades. Entity Framework usa una restricción referencial para identificar que una propiedad actúa como clave externa de una relación.

Si ha activado la casilla de verificación ***Agregar propiedades de clave externa al &lt; nombre de tipo de entidad &gt; *** al crear una relación, se le ha agregado esta restricción referencial.

Cuando se usa EF Designer para agregar o editar una restricción referencial, el diseñador EF agrega o modifica un elemento **ReferentialConstraint**   en el contenido CSDL del archivo. edmx.

-   Haga doble clic en la asociación que desee editar.
    Aparecerá el cuadro de diálogo **restricción referencial**   .
-   En la **Principal**   lista desplegable principal, seleccione la entidad de entidad de seguridad en la restricción referencial.
    Las propiedades de clave de la entidad se agregan a la lista de **claves principales**   en el cuadro de diálogo.
-   En la **Dependent**   lista desplegable dependiente, seleccione la entidad dependiente en la restricción referencial.
-   Para cada clave principal que tenga una clave dependiente, seleccione una clave dependiente correspondiente en las listas desplegables de la columna de **clave dependiente**   .

    ![Restricción de referencia](~/ef6/media/refconstraint.png)

-   Haga clic en  **Aceptar**.

## <a name="create-and-edit-association-mappings"></a>Crear y editar asignaciones de asociación

Puede especificar cómo se asigna una asociación a la base de datos en la ventana detalles de la **asignación**   del diseñador de EF.

> [!NOTE]
> Solo puede asignar los detalles de las asociaciones que no tienen una restricción referencial especificada. Si se especifica una restricción referencial, se incluye una propiedad de clave externa en la entidad y se pueden usar los detalles de asignación de la entidad para controlar a qué columna se asigna la clave externa.

### <a name="create-an-association-mapping"></a>Crear una asignación de asociación

-   Haga clic con el botón secundario en una asociación en la superficie de diseño y seleccione **asignación de tabla**.
    Esto muestra la asignación de asociación en la ventana detalles de la **asignación**   .
-   Haga clic en **Agregar una tabla o vista**.
    Aparece una lista desplegable que incluye todas las tablas del modelo de almacenamiento. 
-   Seleccione la tabla a la que se asignará la asociación. 
    La ventana detalles de la **asignación**   muestra los extremos de la asociación y las propiedades clave del tipo de entidad en cada **extremo**.
-   Para cada propiedad de clave, haga clic en el campo **columna**   y seleccione la columna a la que se asignará la propiedad.

    ![Detalles de la asignación 4](~/ef6/media/mappingdetails4.png)

### <a name="edit-an-association-mapping"></a>Editar una asignación de asociación

-   Haga clic con el botón secundario en una asociación en la superficie de diseño y seleccione **asignación de tabla**.
    Esto muestra la asignación de asociación en la ventana detalles de la **asignación**   .
-   Haga clic en **maps to &lt; TABLE name &gt; **.
    Aparece una lista desplegable que incluye todas las tablas del modelo de almacenamiento. 
-   Seleccione la tabla a la que se asignará la asociación. 
    La ventana detalles de la **asignación**   muestra los extremos de la asociación y las propiedades clave del tipo de entidad en cada extremo.
-   Para cada propiedad de clave, haga clic en el campo **columna**   y seleccione la columna a la que se asignará la propiedad.

## <a name="edit-and-delete-navigation-properties"></a>Editar y eliminar propiedades de navegación

Las propiedades de navegación son propiedades de acceso directo que se usan para buscar las entidades en los extremos de una asociación en un modelo. Las propiedades de navegación se pueden crear al definir una asociación entre dos tipos de entidad.

#### <a name="to-edit-navigation-properties"></a>Para editar las propiedades de navegación

-   Seleccione una propiedad de navegación en la superficie del diseñador de EF.
    En la ventana **propiedades**de Visual Studio se muestra información sobre la propiedad de navegación   .
-   Cambie la configuración de las propiedades en la ventana **propiedades**   .

#### <a name="to-delete-navigation-properties"></a>Para eliminar propiedades de navegación

-   Si las claves externas no se exponen en los tipos de entidad del modelo conceptual, la eliminación de una propiedad de navegación puede provocar que la asociación correspondiente solo se pueda recorrer en una dirección o incluso en ninguna.
-   Haga clic con el botón derecho en una propiedad de navegación en la superficie del diseñador EF y seleccione **eliminar**.
