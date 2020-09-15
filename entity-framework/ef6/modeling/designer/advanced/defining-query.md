---
title: Definición de Query-EF Designer-EF6
description: Definir el diseñador de Query-EF en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/defining-query
ms.openlocfilehash: b5d24b9f6c047eb8681a0343c768d1ffa70ca6b8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070346"
---
# <a name="defining-query---ef-designer"></a>Definir el diseñador de consultas-EF
En este tutorial se muestra cómo agregar una consulta de definición y un tipo de entidad correspondiente a un modelo mediante el diseñador de EF. Una consulta de definición se usa normalmente para proporcionar una funcionalidad similar a la que proporciona una vista de base de datos, pero la vista se define en el modelo, no en la base de datos. Una consulta de definición permite ejecutar una instrucción SQL que se especifica en el elemento **DefiningQuery**   de un archivo. edmx. Para obtener más información, vea **DefiningQuery** en la [especificación de SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).

Al utilizar las consultas de definición, también debe definir un tipo de entidad en el modelo. El tipo de entidad se utiliza para exponer los datos expuestos por la consulta de definición. Tenga en cuenta que los datos que se exponen a través de este tipo de entidad son de solo lectura.

Las consultas con parámetros no se pueden ejecutar como consultas de definición. Sin embargo, los datos se pueden actualizar asignando las funciones de inserción, actualización y eliminación del tipo de entidad que los muestra a los procedimientos almacenados. Para obtener más información, vea [Insert, Update y DELETE con procedimientos almacenados](xref:ef6/modeling/designer/stored-procedures/cud).

En este tema se muestra cómo realizar las siguientes tareas.

-   Agregar una consulta de definición
-   Agregar un tipo de entidad al modelo
-   Asignar la consulta de definición al tipo de entidad

## <a name="prerequisites"></a>Prerrequisitos

Para completar este tutorial, necesitará:

- Una versión reciente de Visual Studio.
- La [base de datos de ejemplo School](xref:ef6/resources/school-database).

## <a name="set-up-the-project"></a>Configurar el proyecto

En este tutorial se usa Visual Studio 2012 o una versión más reciente.

-   Abra Visual Studio.
-   En el menú **Archivo** , seleccione **Nuevo**y haga clic en **Proyecto**.
-   En el panel izquierdo, haga clic en **Visual \# C**y, a continuación, seleccione la plantilla **aplicación de consola** .
-   Escriba **DefiningQuerySample** como nombre del proyecto y haga clic en **Aceptar**.

 

## <a name="create-a-model-based-on-the-school-database"></a>Creación de un modelo basado en la base de datos School

-   Haga clic con el botón secundario en el nombre del proyecto en Explorador de soluciones, seleccione **Agregar**y, a continuación, haga clic en **nuevo elemento**.
-   Seleccione **datos** en el menú de la izquierda y, a continuación, seleccione **ADO.NET Entity Data Model** en el panel Plantillas.
-   Escriba **DefiningQueryModel. edmx** como nombre de archivo y, a continuación, haga clic en **Agregar**.
-   En el cuadro de diálogo elegir contenido del modelo, seleccione **generar desde la base de datos**y, a continuación, haga clic en **siguiente**.
-   Haga clic en nueva conexión. En el cuadro de diálogo Propiedades de conexión, escriba el nombre del servidor (por ejemplo, **(LocalDB) \\ mssqllocaldb**), seleccione el método de autenticación, escriba **School**   como nombre de la base de datos y, a continuación, haga clic en **Aceptar**.
    El cuadro de diálogo elegir la conexión de datos se actualiza con la configuración de conexión de la base de datos.
-   En el cuadro de diálogo elija los objetos de base de datos, compruebe el nodo **tablas**   . Se agregarán todas las tablas al modelo **School** .
-   Haga clic en **Finalizar**.
-   En Explorador de soluciones, haga clic con el botón secundario en el archivo **DefiningQueryModel. edmx** y seleccione **abrir con.**...
-   Seleccione **Editor XML (texto)**.

    ![Editor XML](~/ef6/media/xmleditor.png)

-   Haga clic en **sí** si se le solicita el mensaje siguiente:

    ![ADVERTENCIA 2](~/ef6/media/warning2.png)

 

## <a name="add-a-defining-query"></a>Agregar una consulta de definición

En este paso, usaremos el editor XML para agregar una consulta de definición y un tipo de entidad a la sección SSDL del archivo. edmx. 

-   Agregue un elemento **EntitySet**   a la sección SSDL del archivo. edmx (línea 5 a 13). Especifique lo siguiente:
    -   Solo **Name**   se especifican los atributos name y **EntityType**   del elemento **EntitySet**   .
    -   El nombre completo del tipo de entidad se usa en el atributo **EntityType**   .
    -   La instrucción SQL que se va a ejecutar se especifica en el elemento **DefiningQuery**   .

``` xml
    <!-- SSDL content -->
    <edmx:StorageModels>
      <Schema Namespace="SchoolModel.Store" Alias="Self" Provider="System.Data.SqlClient" ProviderManifestToken="2008" xmlns:store="http://schemas.microsoft.com/ado/2007/12/edm/EntityStoreSchemaGenerator" xmlns="http://schemas.microsoft.com/ado/2009/11/edm/ssdl">
        <EntityContainer Name="SchoolModelStoreContainer">
           <EntitySet Name="GradeReport" EntityType="SchoolModel.Store.GradeReport">
              <DefiningQuery>
                SELECT CourseID, Grade, FirstName, LastName
                FROM StudentGrade
                JOIN
                (SELECT * FROM Person WHERE EnrollmentDate IS NOT NULL) AS p
                ON StudentID = p.PersonID
              </DefiningQuery>
          </EntitySet>
          <EntitySet Name="Course" EntityType="SchoolModel.Store.Course" store:Type="Tables" Schema="dbo" />
```

-   Agregue el elemento **EntityType** a la sección SSDL del archivo. edmx. como se muestra a continuación. Tenga en cuenta lo siguiente:
    -   El valor del atributo **Name** corresponde al valor del atributo **EntityType** en el elemento **EntitySet** anterior, aunque el nombre completo del tipo de entidad se usa en el atributo **EntityType** .
    -   Los nombres de propiedad corresponden a los nombres de columna devueltos por la instrucción SQL en el elemento **DefiningQuery** (arriba).
    -   En este ejemplo, la clave de entidad consta de tres propiedades para garantizar un valor de clave único.

``` xml
    <EntityType Name="GradeReport">
      <Key>
        <PropertyRef Name="CourseID" />
        <PropertyRef Name="FirstName" />
        <PropertyRef Name="LastName" />
      </Key>
      <Property Name="CourseID"
                Type="int"
                Nullable="false" />
      <Property Name="Grade"
                Type="decimal"
                Precision="3"
                Scale="2" />
      <Property Name="FirstName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
      <Property Name="LastName"
                Type="nvarchar"
                Nullable="false"
                MaxLength="50" />
    </EntityType>
```

>[!NOTE]
> Si posteriormente ejecuta el cuadro de diálogo **Asistente para actualizar modelo** , se sobrescribirán los cambios realizados en el modelo de almacenamiento, incluida la definición de las consultas.

 

## <a name="add-an-entity-type-to-the-model"></a>Agregar un tipo de entidad al modelo

En este paso se agregará el tipo de entidad al modelo conceptual mediante el diseñador de EF. Tenga en cuenta lo siguiente:

-   El **nombre** de la entidad corresponde al valor del atributo **EntityType** en el elemento **EntitySet** anterior.
-   Los nombres de propiedad corresponden a los nombres de columna devueltos por la instrucción SQL en el elemento **DefiningQuery** anterior.
-   En este ejemplo, la clave de entidad consta de tres propiedades para garantizar un valor de clave único.

Abra el modelo en el diseñador de EF.

-   Haga doble clic en DefiningQueryModel. edmx.
-   Por **ejemplo** , en el siguiente mensaje:

    ![ADVERTENCIA 2](~/ef6/media/warning2.png)

 

Se muestra el diseñador de entidades, que proporciona una superficie de diseño para editar el modelo.

-   Haga clic con el botón secundario en la superficie del diseñador y seleccione **Agregar nueva** - &gt; **entidad.**...
-   Especifique **GradeReport** para el nombre de entidad y el **CourseID** para la **propiedad de clave**.
-   Haga clic con el botón secundario en la entidad **GradeReport** y seleccione **Agregar nueva** - &gt; **propiedad escalar**.
-   Cambie el nombre predeterminado de la propiedad a **FirstName**.
-   Agregue otra propiedad escalar y especifique **LastName** como nombre.
-   Agregue otra propiedad escalar y especifique **grade** como el nombre.
-   En la ventana **propiedades** , cambie la propiedad **tipo** de **grado**a **decimal**.
-   Seleccione las propiedades **FirstName** y **LastName** .
-   En la ventana **propiedades** , cambie el valor de la propiedad **EntityKey** a **true**.

Como resultado, se han agregado los siguientes elementos a la sección **CSDL** del archivo. edmx.

``` xml
    <EntitySet Name="GradeReport" EntityType="SchoolModel.GradeReport" />

    <EntityType Name="GradeReport">
    . . .
    </EntityType>
```

 

## <a name="map-the-defining-query-to-the-entity-type"></a>Asignar la consulta de definición al tipo de entidad

En este paso, usaremos la ventana detalles de la asignación para asignar los tipos de entidad conceptual y de almacenamiento.

-   Haga clic con el botón secundario en la entidad **GradeReport** en la superficie de diseño y seleccione **asignación de tabla**.  
    Se muestra la ventana detalles de la **asignación** .
-   Seleccione **GradeReport** en la lista desplegable ** &lt; Agregar una &gt; tabla o vista** (que se encuentra en la **tabla**s).  
    Aparecen las asignaciones predeterminadas entre el tipo de entidad conceptual y Storage **GradeReport** .  
    ![Asignación de Details3](~/ef6/media/mappingdetails.png)

Como resultado, el elemento **EntitySetMapping**   se agrega a la sección de asignación del archivo. edmx. 

``` xml
    <EntitySetMapping Name="GradeReports">
      <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.GradeReport)">
        <MappingFragment StoreEntitySet="GradeReport">
          <ScalarProperty Name="LastName" ColumnName="LastName" />
          <ScalarProperty Name="FirstName" ColumnName="FirstName" />
          <ScalarProperty Name="Grade" ColumnName="Grade" />
          <ScalarProperty Name="CourseID" ColumnName="CourseID" />
        </MappingFragment>
      </EntityTypeMapping>
    </EntitySetMapping>
```

-   Realice la compilación de la aplicación.

 

## <a name="call-the-defining-query-in-your-code"></a>Llamar a la consulta de definición en el código

Ahora puede ejecutar la consulta de definición mediante el tipo de entidad **GradeReport** . 

``` csharp
    using (var context = new SchoolEntities())
    {
        var report = context.GradeReports.FirstOrDefault();
        Console.WriteLine("{0} {1} got {2}",
            report.FirstName, report.LastName, report.Grade);
    }
```
