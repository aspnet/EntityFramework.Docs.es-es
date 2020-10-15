---
title: 'Especificación de MSL: EF6'
description: Especificación de MSL en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/msl-spec
ms.openlocfilehash: 0780a549d4cbb60bf84bbed927d52588e99148e1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066348"
---
# <a name="msl-specification"></a>Especificación MSL
El lenguaje de especificación de asignaciones (MSL) es un lenguaje basado en XML que describe la asignación entre el modelo conceptual y el modelo de almacenamiento de una aplicación Entity Framework.

En una aplicación Entity Framework, la asignación de metadatos se carga desde un archivo. MSL (escrito en MSL) en tiempo de compilación. Entity Framework usa la asignación de metadatos en tiempo de ejecución para traducir las consultas en el modelo conceptual para almacenar comandos específicos de.

El Entity Framework Designer (EF Designer) almacena la información de asignación en un archivo. edmx en tiempo de diseño. En tiempo de compilación, Entity Designer usa información en un archivo. edmx para crear el archivo. MSL que Entity Framework necesita en tiempo de ejecución.

Los nombres de todos los tipos de modelos conceptuales o de almacenamiento a los que se hace referencia en MSL deben estar calificados con sus respectivos nombres de espacios de nombres. Para obtener información sobre el nombre del espacio de nombres del modelo conceptual, vea [especificación de CSDL](xref:ef6/modeling/designer/advanced/edmx/csdl-spec). Para obtener información sobre el nombre del espacio de nombres del modelo de almacenamiento, vea [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).

Las versiones de MSL se diferencian en los espacios de nombres XML.

| Versión de MSL | Espacio de nombres XML                                        |
|:------------|:-----------------------------------------------------|
| MSL v1      | urn: schemas-microsoft-com: Windows: Storage: asignación: CS |
| MSL V2      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| MSL V3      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a>Alias (Elemento) (MSL)

El elemento **alias** del lenguaje de especificación de asignaciones (MSL) es un elemento secundario del elemento mapping que se utiliza para definir los alias para los espacios de nombres de modelos conceptuales y de almacenamiento. Los nombres de todos los tipos de modelos conceptuales o de almacenamiento a los que se hace referencia en MSL deben estar calificados con sus respectivos nombres de espacios de nombres. Para obtener información sobre el nombre del espacio de nombres del modelo conceptual, vea elemento Schema (CSDL). Para obtener información sobre el nombre del espacio de nombres del modelo de almacenamiento, vea schema (elemento) (SSDL).

El elemento **alias** no puede tener elementos secundarios.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **alias** .

| Nombre del atributo | Es obligatorio | Valor                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| **Clave**        | Sí         | El alias para el espacio de nombres especificado por el atributo de **valor** . |
| **Valor**      | Sí         | Espacio de nombres para el que el valor del elemento **key** es un alias.     |

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **alias** que define un alias, `c` , para los tipos que se definen en el modelo conceptual.

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="associationend-element-msl"></a>AssociationEnd (Elemento) (MSL)

El elemento **AssociationEnd** del lenguaje de especificación de asignaciones (MSL) se utiliza cuando las funciones de modificación de un tipo de entidad en el modelo conceptual se asignan a los procedimientos almacenados en la base de datos subyacente. Si un procedimiento almacenado de modificación toma un parámetro cuyo valor se mantiene en una propiedad de asociación, el elemento **AssociationEnd** asigna el valor de la propiedad al parámetro. Para obtener más información, vea el ejemplo siguiente.

Para obtener más información sobre la asignación de funciones de modificación de tipos de entidad a procedimientos almacenados, vea ModificationFunctionMapping (elemento) (MSL) y Walkthrough: asignar una entidad a procedimientos almacenados.

El elemento **AssociationEnd** puede tener los siguientes elementos secundarios:

-   ScalarProperty

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **AssociationEnd** .

| Nombre del atributo     | Es obligatorio | Value                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **AssociationSet** | Sí         | El nombre de la asociación que se está asignando.                                                                                                                                 |
| **From**           | Sí         | Valor del atributo **FromRole** de la propiedad de navegación que corresponde a la asociación que se está asignando. Para obtener más información, vea elemento NavigationProperty (CSDL). |
| **To**             | Sí         | Valor del atributo **ToRole** de la propiedad de navegación que corresponde a la asociación que se está asignando. Para obtener más información, vea elemento NavigationProperty (CSDL).   |

### <a name="example"></a>Ejemplo

Considere el siguiente tipo de entidad del modelo conceptual:

``` xml
 <EntityType Name="Course">
   <Key>
     <PropertyRef Name="CourseID" />
   </Key>
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" MaxLength="100"
             FixedLength="false" Unicode="true" />
   <Property Type="Int32" Name="Credits" Nullable="false" />
   <NavigationProperty Name="Department"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Course" ToRole="Department" />
 </EntityType>
```

Considere también el siguiente procedimiento almacenado:

``` SQL
 CREATE PROCEDURE [dbo].[UpdateCourse]
                                @CourseID int,
                                @Title nvarchar(50),
                                @Credits int,
                                @DepartmentID int
                                AS
                                UPDATE Course SET Title=@Title,
                                                              Credits=@Credits,
                                                              DepartmentID=@DepartmentID
                                WHERE CourseID=@CourseID;
```

Para asignar la función de actualización de la `Course` entidad a este procedimiento almacenado, debe proporcionar un valor al parámetro **departmentId** . El valor para `DepartmentID` no corresponde a una propiedad del tipo de entidad; está contenido en una asociación independiente cuya asignación se muestra aquí:

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
 </AssociationSetMapping>
```

En el código siguiente se muestra el elemento **AssociationEnd** que se usa para asignar la propiedad **departmentId** de la Asociación de **Departamento de FK \_ Course \_ ** al procedimiento almacenado **UpdateCourse** (al que se asigna la función de actualización del tipo de entidad **Course** ):

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdateCourse">
         <AssociationEnd AssociationSet="FK_Course_Department"
                         From="Course" To="Department">
           <ScalarProperty Name="DepartmentID"
                           ParameterName="DepartmentID"
                           Version="Current" />
         </AssociationEnd>
         <ScalarProperty Name="Credits" ParameterName="Credits"
                         Version="Current" />
         <ScalarProperty Name="Title" ParameterName="Title"
                         Version="Current" />
         <ScalarProperty Name="CourseID" ParameterName="CourseID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="associationsetmapping-element-msl"></a>AssociationSetMapping (Elemento) (MSL)

El elemento **AssociationSetMapping** del lenguaje de especificación de asignaciones (MSL) define la asignación entre una asociación en el modelo conceptual y las columnas de tabla en la base de datos subyacente.

Las asociaciones del modelo conceptual son tipos cuyas propiedades representan columnas de clave primaria y clave externa de la base de datos subyacente. El elemento **AssociationSetMapping** utiliza dos elementos EndProperty para definir las asignaciones entre las propiedades de tipo de asociación y las columnas de la base de datos. Puede definir condiciones en estas asignaciones con el elemento Condition. Asigne las funciones de inserción, actualización y eliminación para las asociaciones a procedimientos almacenados de la base de datos mediante el elemento ModificationFunctionMapping. Defina las asignaciones de solo lectura entre las asociaciones y las columnas de la tabla mediante una Entity SQL cadena en un elemento QueryView.

> [!NOTE]
> Si se define una restricción referencial para una asociación en el modelo conceptual, no es necesario asignar la asociación con un elemento **AssociationSetMapping** . Si hay un elemento **AssociationSetMapping** para una asociación que tiene una restricción referencial, se omitirán las asignaciones definidas en el elemento **AssociationSetMapping** . Para obtener más información, vea ReferentialConstraint (elemento) (CSDL).

El elemento **AssociationSetMapping** puede tener los siguientes elementos secundarios

-   QueryView (cero o uno)
-   EndProperty (cero o dos)
-   Condition (cero o más)
-   ModificationFunctionMapping (cero o uno)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **AssociationSetMapping** .

| Nombre del atributo     | Es obligatorio | Value                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| **Nombre**           | Sí         | El nombre del conjunto de asociaciones del modelo conceptual que se está asignando.                      |
| **TypeName**       | No          | El nombre completo, calificado con el espacio de nombres, del tipo de asociación del modelo conceptual que se está asignando. |
| **StoreEntitySet** | No          | El nombre de la tabla que se está asignando.                                                 |

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **AssociationSetMapping** en el que el conjunto de asociaciones del **Departamento del \_ curso \_ de FK** en el modelo conceptual se asigna a la tabla **Course** en la base de datos. Las asignaciones entre las propiedades de tipo de asociación y las columnas de tabla se especifican en los elementos secundarios **EndProperty** .

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

## <a name="complexproperty-element-msl"></a>ComplexProperty (Elemento) (MSL)

Un elemento **ComplexProperty** en el lenguaje de especificación de asignaciones (MSL) define la asignación entre una propiedad de tipo complejo en un tipo de entidad del modelo conceptual y las columnas de la tabla en la base de datos subyacente. Las asignaciones entre columnas y propiedades se especifican en elementos secundarios ScalarProperty.

El elemento de propiedad **complexType** puede tener los siguientes elementos secundarios:

-   ScalarProperty (cero o más)
-   **ComplexProperty** (cero o más)
-   ComplextTypeMapping (cero o más)
-   Condition (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos aplicables al elemento **ComplexProperty** :

| Nombre del atributo | Es obligatorio | Value                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| **Nombre**       | Sí         | El nombre de la propiedad compleja de un tipo de entidad del modelo conceptual que se está asignando. |
| **TypeName**   | No          | El nombre completo, calificado con el espacio de nombres, del tipo de propiedad del modelo conceptual.                              |

### <a name="example"></a>Ejemplo

El siguiente ejemplo se basa en el modelo School. El siguiente tipo complejo se ha agregado al modelo conceptual:

``` xml
 <ComplexType Name="FullName">
   <Property Type="String" Name="LastName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
   <Property Type="String" Name="FirstName"
             Nullable="false" MaxLength="50"
             FixedLength="false" Unicode="true" />
 </ComplexType>
```

Las propiedades **LastName** y **FirstName** del tipo de entidad **Person** se han reemplazado por una propiedad compleja, **Name**:

``` xml
 <EntityType Name="Person">
   <Key>
     <PropertyRef Name="PersonID" />
   </Key>
   <Property Name="PersonID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="HireDate" Type="DateTime" />
   <Property Name="EnrollmentDate" Type="DateTime" />
   <Property Name="Name" Type="SchoolModel.FullName" Nullable="false" />
 </EntityType>
```

En el siguiente MSL se muestra el elemento **ComplexProperty** que se usa para asignar la propiedad **Name** a las columnas de la base de datos subyacente:

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
       <ComplexProperty Name="Name" TypeName="SchoolModel.FullName">
         <ScalarProperty Name="FirstName" ColumnName="FirstName" />
         <ScalarProperty Name="LastName" ColumnName="LastName" />  
       </ComplexProperty>
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="complextypemapping-element-msl"></a>ComplexTypeMapping (Elemento) (MSL)

El elemento **ComplexTypeMapping** del lenguaje de especificación de asignaciones (MSL) es un elemento secundario del elemento ResultMapping y define la asignación entre una importación de función en el modelo conceptual y un procedimiento almacenado en la base de datos subyacente cuando se cumplen las condiciones siguientes:

-   La importación de función devuelve un tipo complejo conceptual.
-   Los nombres de las columnas devueltas por el procedimiento almacenado no coinciden exactamente con los nombres de las propiedades en el tipo complejo.

De forma predeterminada, la asignación entre las columnas devueltas por un procedimiento almacenado y un tipo complejo se basa en los nombres de las propiedades y de las columnas. Si los nombres de columna no coinciden exactamente con los nombres de propiedad, debe utilizar el elemento **ComplexTypeMapping** para definir la asignación. Para obtener un ejemplo de la asignación predeterminada, vea FunctionImportMapping (elemento) (MSL).

El elemento **ComplexTypeMapping** puede tener los siguientes elementos secundarios:

-   ScalarProperty (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos aplicables al elemento **ComplexTypeMapping** .

| Nombre del atributo | Es obligatorio | Value                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| **TypeName**   | Sí         | El nombre completo, incluido el espacio de nombres, del tipo complejo que se está asignando. |

### <a name="example"></a>Ejemplo

Observe el siguiente procedimiento almacenado:

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

Considere también el siguiente tipo complejo del modelo conceptual:

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

Para crear una importación de función que devuelva instancias del tipo complejo anterior, la asignación entre las columnas devueltas por el procedimiento almacenado y el tipo de entidad debe definirse en un elemento **ComplexTypeMapping** :

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <ComplexTypeMapping TypeName="SchoolModel.GradeInfo">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </ComplexTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="condition-element-msl"></a>Condition (Elemento) (MSL)

El elemento **Condition** del lenguaje de especificación de asignaciones (MSL) coloca condiciones en las asignaciones entre el modelo conceptual y la base de datos subyacente. La asignación que se define dentro de un nodo XML es válida si se cumplen todas las condiciones, como se especifica en los elementos de **condición** secundarios. De lo contrario, la asignación no es válida. Por ejemplo, si un elemento MappingFragment contiene uno o varios elementos secundarios **Condition** , la asignación definida dentro del nodo **MappingFragment** solo será válida si se cumplen todas las condiciones de los elementos de **condición** secundarios.

Cada condición se puede aplicar a un **nombre** (el nombre de una propiedad de entidad del modelo conceptual, especificado por el atributo de **nombre** ) o un **columnName** (el nombre de una columna de la base de datos, especificado por el atributo **columnName** ). Cuando se establece el atributo **Name** , la condición se comprueba con respecto a un valor de propiedad de entidad. Cuando se establece el atributo **columnName** , la condición se comprueba con respecto a un valor de columna. Solo se puede especificar uno de los atributos **Name** o **columnName** en un elemento **Condition** .

> [!NOTE]
> Cuando el elemento **Condition** se usa dentro de un elemento FunctionImportMapping, solo el atributo **Name** no es aplicable.

El elemento **Condition** puede ser un elemento secundario de los siguientes elementos:

-   AssociationSetMapping
-   ComplexProperty
-   EntitySetMapping
-   MappingFragment
-   EntityTypeMapping

El elemento **Condition** no puede tener elementos secundarios.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos aplicables al elemento **Condition** :

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **ColumnName** | No          | El nombre de la columna de la tabla cuyo valor se utiliza para evaluar la condición.                                                                                                                                                                                                                   |
| **IsNull**     | No          | **True** o **False**. Si el valor es **true** y el valor de la columna es **null**, o si el valor es **false** y el valor de la columna no es **null**, la condición es true. De lo contrario, la condición es falsa. <br/> Los atributos **IsNull** y **Value** no se pueden usar al mismo tiempo. |
| **Valor**      | No          | El valor con el que se compara el valor de la columna. Si los valores son los mismos, la condición es verdadera. De lo contrario, la condición es falsa. <br/> Los atributos **IsNull** y **Value** no se pueden usar al mismo tiempo.                                                                       |
| **Nombre**       | No          | El nombre de la propiedad de entidad del modelo conceptual cuyo valor se utiliza para evaluar la condición. <br/> Este atributo no es aplicable si el elemento **Condition** se usa dentro de un elemento FunctionImportMapping.                                                                           |

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestran los elementos **Condition** como elementos secundarios de los elementos **MappingFragment** . Cuando **HireDate** no es NULL y **EnrollmentDate** es null, los datos se asignan entre el tipo **SchoolModel. instructor** y las columnas **PersonID** e **HireDate** de la tabla **Person** . Cuando **EnrollmentDate** no es NULL y **HireDate** es null, los datos se asignan entre el tipo **SchoolModel. Student** y las columnas **PersonID** e **Enrollment** de la tabla **Person** .

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="deletefunction-element-msl"></a>DeleteFunction (Elemento) (MSL)

El elemento **InsertFunction** del lenguaje de especificación de asignaciones (MSL) asigna la función de eliminación de un tipo de entidad o asociación del modelo conceptual a un procedimiento almacenado en la base de datos subyacente. Los procedimientos almacenados a los que están asignados las funciones de modificación se deben declarar en el modelo de almacenamiento. Para obtener más información, vea elemento function (SSDL).

> [!NOTE]
> Si no asigna las tres operaciones de inserción, actualización o eliminación de un tipo de entidad a los procedimientos almacenados, se producirá un error en las operaciones no asignadas si se ejecutan en tiempo de ejecución y se genera un UpdateException.

### <a name="deletefunction-applied-to-entitytypemapping"></a>DeleteFunction aplicado a EntityTypeMapping

Cuando se aplica al elemento EntityTypeMapping, el elemento **DeleteFunction** asigna la función de eliminación de un tipo de entidad del modelo conceptual a un procedimiento almacenado.

El elemento **DeleteFunction** puede tener los elementos secundarios siguientes cuando se aplica a un elemento **EntityTypeMapping** :

-   AssociationEnd (cero o más)
-   ComplexProperty (cero o más)
-   ScalarProperty (cero o más)

#### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **DeleteFunction** cuando se aplica a un elemento **EntityTypeMapping** .

| Nombre del atributo            | Es obligatorio | Value                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombrefunción**          | Sí         | El nombre completo, calificado con el espacio de nombres, del procedimiento almacenado al que la función de eliminación está asignada. El procedimiento almacenado se debe declarar en el modelo de almacenamiento. |
| **RowsAffectedParameter** | No          | El nombre del parámetro de salida que devuelve el número de filas afectadas.                                                                               |

#### <a name="example"></a>Ejemplo

El ejemplo siguiente se basa en el modelo School y muestra el elemento **DeleteFunction** que asigna la función Delete del tipo de entidad **Person** al procedimiento almacenado **DeletePerson** . El procedimiento almacenado **DeletePerson** se declara en el modelo de almacenamiento.

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="deletefunction-applied-to-associationsetmapping"></a>DeleteFunction aplicado a AssociationSetMapping

Cuando se aplica al elemento AssociationSetMapping, el elemento **DeleteFunction** asigna la función de eliminación de una asociación del modelo conceptual a un procedimiento almacenado.

El elemento **DeleteFunction** puede tener los elementos secundarios siguientes cuando se aplica al elemento **AssociationSetMapping** :

-   EndProperty

#### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **DeleteFunction** cuando se aplica al elemento **AssociationSetMapping** .

| Nombre del atributo            | Es obligatorio | Value                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombrefunción**          | Sí         | El nombre completo, calificado con el espacio de nombres, del procedimiento almacenado al que la función de eliminación está asignada. El procedimiento almacenado se debe declarar en el modelo de almacenamiento. |
| **RowsAffectedParameter** | No          | El nombre del parámetro de salida que devuelve el número de filas afectadas.                                                                               |

#### <a name="example"></a>Ejemplo

El ejemplo siguiente se basa en el modelo School y muestra el elemento **DeleteFunction** que se usa para asignar la función de eliminación de la Asociación **CourseInstructor** al procedimiento almacenado **DeleteCourseInstructor** . El procedimiento almacenado **DeleteCourseInstructor** se declara en el modelo de almacenamiento.

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="endproperty-element-msl"></a>EndProperty (Elemento) (MSL)

El elemento **EndProperty** del lenguaje de especificación de asignaciones (MSL) define la asignación entre un extremo o una función de modificación de una asociación del modelo conceptual y la base de datos subyacente. La asignación entre columnas y propiedades se especifica en un elemento secundario ScalarProperty.

Cuando se utiliza un elemento **EndProperty** para definir la asignación para el final de una asociación del modelo conceptual, es un elemento secundario de un elemento AssociationSetMapping. Cuando el elemento **EndProperty** se utiliza para definir la asignación para una función de modificación de una asociación del modelo conceptual, es un elemento secundario de un elemento InsertFunction o un elemento InsertFunction.

El elemento **EndProperty** puede tener los siguientes elementos secundarios:

-   ScalarProperty (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos aplicables al elemento **EndProperty** :

| Nombre del atributo | Es obligatorio | Value                                                 |
|:---------------|:------------|:------------------------------------------------------|
| Nombre           | Sí         | El nombre del extremo de la asociación que se está asignando. |

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **AssociationSetMapping** en el que la Asociación de **Departamento de FK \_ \_ Course** en el modelo conceptual se asigna a la tabla **Course** de la base de datos. Las asignaciones entre las propiedades de tipo de asociación y las columnas de tabla se especifican en los elementos secundarios **EndProperty** .

``` xml
 <AssociationSetMapping Name="FK_Course_Department"
                        TypeName="SchoolModel.FK_Course_Department"
                        StoreEntitySet="Course">
   <EndProperty Name="Department">
     <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
 </AssociationSetMapping>
```

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra el elemento **EndProperty** que asigna las funciones de inserción y eliminación de una asociación (**CourseInstructor**) a los procedimientos almacenados en la base de datos subyacente. Las funciones asignadas se declaran en el modelo de almacenamiento.

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="entitycontainermapping-element-msl"></a>EntityContainerMapping (Elemento) (MSL)

El elemento **EntityContainerMapping** del lenguaje de especificación de asignaciones (MSL) asigna el contenedor de entidades del modelo conceptual al contenedor de entidades en el modelo de almacenamiento. El elemento **EntityContainerMapping** es un elemento secundario del elemento Mapping.

El elemento **EntityContainerMapping** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   EntitySetMapping (cero o más)
-   AssociationSetMapping (cero o más)
-   FunctionImportMapping (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntityContainerMapping** .

| Nombre del atributo            | Es obligatorio | Value                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StorageModelContainer** | Sí         | El nombre del contenedor de entidades del modelo de almacenamiento que se está asignando.                                                                                                                                                                                     |
| **CdmEntityContainer**    | Sí         | El nombre del contenedor de entidades del modelo conceptual que se está asignando.                                                                                                                                                                                  |
| **GenerateUpdateViews**   | No          | **True** o **False**. Si **es false**, no se genera ninguna vista de actualización. Este atributo debe establecerse en **false** si tiene una asignación de solo lectura que no sería válida porque los datos no pueden realizar una operación de ida y vuelta correctamente. <br/> El valor predeterminado es **True**. |

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityContainerMapping** que asigna el contenedor **SchoolModelEntities** (el contenedor de entidades del modelo conceptual) al contenedor **SchoolModelStoreContainer** (el contenedor de entidades del modelo de almacenamiento):

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolModelEntities">
   <EntitySetMapping Name="Courses">
     <EntityTypeMapping TypeName="c.Course">
       <MappingFragment StoreEntitySet="Course">
         <ScalarProperty Name="CourseID" ColumnName="CourseID" />
         <ScalarProperty Name="Title" ColumnName="Title" />
         <ScalarProperty Name="Credits" ColumnName="Credits" />
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments">
     <EntityTypeMapping TypeName="c.Department">
       <MappingFragment StoreEntitySet="Department">
         <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         <ScalarProperty Name="Name" ColumnName="Name" />
         <ScalarProperty Name="Budget" ColumnName="Budget" />
         <ScalarProperty Name="StartDate" ColumnName="StartDate" />
         <ScalarProperty Name="Administrator" ColumnName="Administrator" />
       </MappingFragment>
     </EntityTypeMapping>
   </EntitySetMapping>
 </EntityContainerMapping>
```

## <a name="entitysetmapping-element-msl"></a>EntitySetMapping (Elemento) (MSL)

El elemento **EntitySetMapping** del lenguaje de especificación de asignaciones (MSL) asigna todos los tipos de un conjunto de entidades del modelo conceptual a los conjuntos de entidades del modelo de almacenamiento. Un conjunto de entidades del modelo conceptual es un contenedor lógico para instancias de entidades del mismo tipo (y tipos derivados). Un conjunto de entidades del modelo de almacenamiento representa una tabla o vista de la base de datos subyacente. El conjunto de entidades del modelo conceptual se especifica mediante el valor del atributo **Name** del elemento **EntitySetMapping** . La tabla o vista asignada a se especifica mediante el atributo **StoreEntitySet** en cada elemento MappingFragment secundario o en el propio elemento **EntitySetMapping** .

El elemento **EntitySetMapping** puede tener los siguientes elementos secundarios:

-   EntityTypeMapping (cero o más)
-   QueryView (cero o uno)
-   MappingFragment (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntitySetMapping** .

| Nombre del atributo           | Es obligatorio | Value                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**                 | Sí         | El nombre del conjunto de entidades del modelo conceptual que se está asignando.                                                                                                                                                             |
| **TypeName** **1**       | No          | El nombre del tipo de entidad del modelo conceptual que se está asignando.                                                                                                                                                            |
| **StoreEntitySet** **1** | No          | El nombre del conjunto de entidades del modelo de almacenamiento al que se está asignando.                                                                                                                                                             |
| **MakeColumnsDistinct**  | No          | **True** o **false** , dependiendo de si solo se devuelven filas distintas. <br/> Si este atributo se establece en **true**, el atributo **GenerateUpdateViews** del elemento EntityContainerMapping debe establecerse en **false**. |

 

**1** los atributos **TypeName** y **StoreEntitySet** se pueden usar en lugar de los elementos secundarios EntityTypeMapping y MappingFragment para asignar un tipo de entidad único a una sola tabla.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntitySetMapping** que asigna tres tipos (un tipo base y dos tipos derivados) en el conjunto de entidades **Courses** del modelo conceptual a tres tablas diferentes en la base de datos subyacente. Las tablas se especifican mediante el atributo **StoreEntitySet** en cada elemento **MappingFragment** .

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.Course)">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="Title" ColumnName="Title" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnlineCourse)">
     <MappingFragment StoreEntitySet="OnlineCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="URL" ColumnName="URL" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel1.OnsiteCourse)">
     <MappingFragment StoreEntitySet="OnsiteCourse">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Time" ColumnName="Time" />
       <ScalarProperty Name="Days" ColumnName="Days" />
       <ScalarProperty Name="Location" ColumnName="Location" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="entitytypemapping-element-msl"></a>EntityTypeMapping (Elemento) (MSL)

El elemento **EntityTypeMapping** del lenguaje de especificación de asignaciones (MSL) define la asignación entre un tipo de entidad en el modelo conceptual y las tablas o vistas de la base de datos subyacente. Para obtener información sobre los tipos de entidad del modelo conceptual y las tablas o vistas de la base de datos subyacente, vea EntityType (Elemento) (CSDL) y EntitySet (Elemento) (SSDL). El tipo de entidad del modelo conceptual que se está asignando se especifica mediante el atributo **TypeName** del elemento **EntityTypeMapping** . La tabla o vista que se está asignando se especifica mediante el atributo **StoreEntitySet** del elemento MappingFragment secundario.

El elemento secundario ModificationFunctionMapping se puede utilizar para asignar las funciones de inserción, actualización o eliminación de tipos de entidad a procedimientos almacenados de la base de datos.

El elemento **EntityTypeMapping** puede tener los siguientes elementos secundarios:

-   MappingFragment (cero o más)
-   ModificationFunctionMapping (cero o uno)
-   ScalarProperty
-   Condición

> [!NOTE]
> Los elementos **MappingFragment** y **ModificationFunctionMapping** no pueden ser elementos secundarios del elemento **EntityTypeMapping** al mismo tiempo.


> [!NOTE]
> Los elementos **ScalarProperty** y **Condition** solo pueden ser elementos secundarios del elemento **EntityTypeMapping** cuando se usa dentro de un elemento FunctionImportMapping.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntityTypeMapping** .

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **TypeName**   | Sí         | El nombre completo, calificado con el espacio de nombres, del tipo de entidad del modelo conceptual que se está asignando. <br/> Si el tipo es abstracto o un tipo derivado, el valor debe ser `IsOfType(Namespace-qualified_type_name)`. |

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento EntitySetMapping con dos elementos **EntityTypeMapping** secundarios. En el primer elemento **EntityTypeMapping** , el tipo de entidad **SchoolModel. person** se asigna a la tabla **Person** . En el segundo elemento **EntityTypeMapping** , la funcionalidad de actualización del tipo **SchoolModel. person** se asigna a un procedimiento almacenado, **UpdatePerson**, en la base de datos.

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate" ColumnName="EnrollmentDate" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate" ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra la asignación de una jerarquía de tipos en la que el tipo raíz es abstracto. Tenga en cuenta el uso de la `IsOfType` sintaxis para los atributos **TypeName** .

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Person)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Instructor)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <Condition ColumnName="HireDate" IsNull="false" />
       <Condition ColumnName="EnrollmentDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
   <EntityTypeMapping TypeName="IsTypeOf(SchoolModel.Student)">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
       <Condition ColumnName="EnrollmentDate" IsNull="false" />
       <Condition ColumnName="HireDate" IsNull="true" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

## <a name="functionimportmapping-element-msl"></a>FunctionImportMapping (Elemento) (MSL)

El elemento **FunctionImportMapping** del lenguaje de especificación de asignaciones (MSL) define la asignación entre una importación de función en el modelo conceptual y un procedimiento almacenado o una función en la base de datos subyacente. Las importaciones de función se deben declarar en el modelo conceptual, mientras que los procedimientos almacenados se deben declarar en el modelo de almacenamiento. Para obtener más información, vea elemento FunctionImport (CSDL) y elemento function (SSDL).

> [!NOTE]
> De forma predeterminada, si una importación de función devuelve un tipo de entidad del modelo conceptual o un tipo complejo, entonces los nombres de las columnas devueltas por el procedimiento almacenado subyacente deben coincidir exactamente con los nombres de las propiedades del tipo del modelo conceptual. Si los nombres de columna no coinciden exactamente con los nombres de propiedad, la asignación se debe definir en un elemento ResultMapping.

El elemento **FunctionImportMapping** puede tener los siguientes elementos secundarios:

-   ResultMapping (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos aplicables al elemento **FunctionImportMapping** :

| Nombre del atributo         | Es obligatorio | Value                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| **FunctionImportName** | Sí         | El nombre de la importación de función del modelo conceptual que se está asignando.           |
| **Nombrefunción**       | Sí         | El nombre, calificado con el espacio de nombres, de la función del modelo de almacenamiento que se está asignando. |

### <a name="example"></a>Ejemplo

El siguiente ejemplo se basa en el modelo School. Considere la siguiente función en el modelo de almacenamiento:

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

También considere esta importación de función en el modelo conceptual:

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

En el ejemplo siguiente se muestra un elemento **FunctionImportMapping** que se usa para asignar la función y la importación de funciones anteriores entre sí:

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a>InsertFunction (Elemento) (MSL)

El elemento **InsertFunction** del lenguaje de especificación de asignaciones (MSL) asigna la función de inserción de un tipo de entidad o asociación del modelo conceptual a un procedimiento almacenado en la base de datos subyacente. Los procedimientos almacenados a los que están asignados las funciones de modificación se deben declarar en el modelo de almacenamiento. Para obtener más información, vea elemento function (SSDL).

> [!NOTE]
> Si no asigna las tres operaciones de inserción, actualización o eliminación de un tipo de entidad a los procedimientos almacenados, se producirá un error en las operaciones no asignadas si se ejecutan en tiempo de ejecución y se genera un UpdateException.

El elemento **InsertFunction** puede ser un elemento secundario del elemento ModificationFunctionMapping y aplicarse al elemento EntityTypeMapping o al elemento AssociationSetMapping.

### <a name="insertfunction-applied-to-entitytypemapping"></a>InsertFunction aplicado a EntityTypeMapping

Cuando se aplica al elemento EntityTypeMapping, el elemento **InsertFunction** asigna la función de inserción de un tipo de entidad del modelo conceptual a un procedimiento almacenado.

El elemento **InsertFunction** puede tener los elementos secundarios siguientes cuando se aplica a un elemento **EntityTypeMapping** :

-   AssociationEnd (cero o más)
-   ComplexProperty (cero o más)
-   ResultBinding (cero o uno)
-   ScalarProperty (cero o más)

#### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **InsertFunction** cuando se aplican a un elemento **EntityTypeMapping** .

| Nombre del atributo            | Es obligatorio | Value                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombrefunción**          | Sí         | El nombre completo, calificado con el espacio de nombres, del procedimiento almacenado al que la función de inserción está asignada. El procedimiento almacenado se debe declarar en el modelo de almacenamiento. |
| **RowsAffectedParameter** | No          | El nombre del parámetro de salida que devuelve el número de filas afectadas.                                                                               |

#### <a name="example"></a>Ejemplo

El ejemplo siguiente se basa en el modelo School y muestra el elemento **InsertFunction** que se usa para asignar la función de inserción del tipo de entidad person al procedimiento almacenado **InsertPerson** . El procedimiento almacenado **InsertPerson** se declara en el modelo de almacenamiento.

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
### <a name="insertfunction-applied-to-associationsetmapping"></a>InsertFunction aplicado a AssociationSetMapping

Cuando se aplica al elemento AssociationSetMapping, el elemento **InsertFunction** asigna la función de inserción de una asociación del modelo conceptual a un procedimiento almacenado.

El elemento **InsertFunction** puede tener los elementos secundarios siguientes cuando se aplica al elemento **AssociationSetMapping** :

-   EndProperty

#### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **InsertFunction** cuando se aplica al elemento **AssociationSetMapping** .

| Nombre del atributo            | Es obligatorio | Value                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombrefunción**          | Sí         | El nombre completo, calificado con el espacio de nombres, del procedimiento almacenado al que la función de inserción está asignada. El procedimiento almacenado se debe declarar en el modelo de almacenamiento. |
| **RowsAffectedParameter** | No          | El nombre del parámetro de salida que devuelve el número de filas afectadas.                                                                               |

#### <a name="example"></a>Ejemplo

El ejemplo siguiente se basa en el modelo School y muestra el elemento **InsertFunction** que se usa para asignar la función de inserción de la Asociación **CourseInstructor** al procedimiento almacenado **InsertCourseInstructor** . El procedimiento almacenado **InsertCourseInstructor** se declara en el modelo de almacenamiento.

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="mapping-element-msl"></a>Mapping (Elemento) (MSL)

El elemento **mapping** del lenguaje de especificación de asignaciones (MSL) contiene información para la asignación de objetos que se definen en un modelo conceptual a una base de datos (tal y como se describe en un modelo de almacenamiento). Para obtener más información, vea especificación de CSDL y especificación de SSDL.

El elemento **mapping** es el elemento raíz de una especificación de asignación. El espacio de nombres XML para las especificaciones de asignación es https://schemas.microsoft.com/ado/2009/11/mapping/cs .

El elemento de asignación puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Alias (cero o más)
-   EntityContainerMapping (exactamente uno)

Los nombres de los tipos de modelos conceptuales y de almacenamiento a los que se hace referencia en MSL deben estar calificados con sus respectivos nombres de espacios de nombres. Para obtener información sobre el nombre del espacio de nombres del modelo conceptual, vea elemento Schema (CSDL). Para obtener información sobre el nombre del espacio de nombres del modelo de almacenamiento, vea schema (elemento) (SSDL). Los alias para los espacios de nombres que se utilizan en MSL se pueden definir con el elemento Alias.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento de **asignación** .

| Nombre del atributo | Es obligatorio | Value                                                 |
|:---------------|:------------|:------------------------------------------------------|
| **Espacia**      | Sí         | **C-S**. Este es un valor fijo y no se puede cambiar. |

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento de **asignación** basado en parte del modelo School. Para obtener más información sobre el modelo School, consulte Inicio rápido (Entity Framework):

``` xml
 <Mapping Space="C-S"
          xmlns="https://schemas.microsoft.com/ado/2009/11/mapping/cs">
   <Alias Key="c" Value="SchoolModel"/>
   <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                           CdmEntityContainer="SchoolModelEntities">
     <EntitySetMapping Name="Courses">
       <EntityTypeMapping TypeName="c.Course">
         <MappingFragment StoreEntitySet="Course">
           <ScalarProperty Name="CourseID" ColumnName="CourseID" />
           <ScalarProperty Name="Title" ColumnName="Title" />
           <ScalarProperty Name="Credits" ColumnName="Credits" />
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
     <EntitySetMapping Name="Departments">
       <EntityTypeMapping TypeName="c.Department">
         <MappingFragment StoreEntitySet="Department">
           <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
           <ScalarProperty Name="Name" ColumnName="Name" />
           <ScalarProperty Name="Budget" ColumnName="Budget" />
           <ScalarProperty Name="StartDate" ColumnName="StartDate" />
           <ScalarProperty Name="Administrator" ColumnName="Administrator" />
         </MappingFragment>
       </EntityTypeMapping>
     </EntitySetMapping>
   </EntityContainerMapping>
 </Mapping>
```

## <a name="mappingfragment-element-msl"></a>MappingFragment (Elemento) (MSL)

El elemento **MappingFragment** del lenguaje de especificación de asignaciones (MSL) define la asignación entre las propiedades de un tipo de entidad del modelo conceptual y una tabla o vista en la base de datos. Para obtener información sobre los tipos de entidad del modelo conceptual y las tablas o vistas de la base de datos subyacente, vea EntityType (Elemento) (CSDL) y EntitySet (Elemento) (SSDL). El **MappingFragment** puede ser un elemento secundario del elemento EntityTypeMapping o el elemento EntitySetMapping.

El elemento **MappingFragment** puede tener los siguientes elementos secundarios:

-   ComplexType (cero o más)
-   ScalarProperty (cero o más)
-   Condition (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **MappingFragment** .

| Nombre del atributo          | Es obligatorio | Value                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **StoreEntitySet**      | Sí         | El nombre de la tabla o vista que se está asociando.                                                                                                                                                                           |
| **MakeColumnsDistinct** | No          | **True** o **false** , dependiendo de si solo se devuelven filas distintas. <br/> Si este atributo se establece en **true**, el atributo **GenerateUpdateViews** del elemento EntityContainerMapping debe establecerse en **false**. |

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **MappingFragment** como elemento secundario de un elemento **EntityTypeMapping** . En este ejemplo, las propiedades del tipo de **curso** en el modelo conceptual se asignan a las columnas de la tabla **Course** en la base de datos.

``` xml
 <EntitySetMapping Name="Courses">
   <EntityTypeMapping TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **MappingFragment** como el elemento secundario de un elemento **EntitySetMapping** . Como en el ejemplo anterior, las propiedades del tipo de **curso** en el modelo conceptual se asignan a las columnas de la tabla **Course** en la base de datos.

``` xml
 <EntitySetMapping Name="Courses" TypeName="SchoolModel.Course">
     <MappingFragment StoreEntitySet="Course">
       <ScalarProperty Name="CourseID" ColumnName="CourseID" />
       <ScalarProperty Name="Title" ColumnName="Title" />
       <ScalarProperty Name="Credits" ColumnName="Credits" />
       <ScalarProperty Name="DepartmentID" ColumnName="DepartmentID" />
     </MappingFragment>
 </EntitySetMapping>
```

## <a name="modificationfunctionmapping-element-msl"></a>ModificationFunctionMapping (Elemento) (MSL)

El elemento **ModificationFunctionMapping** del lenguaje de especificación de asignaciones (MSL) asigna las funciones de inserción, actualización y eliminación de un tipo de entidad del modelo conceptual a los procedimientos almacenados en la base de datos subyacente. El elemento **ModificationFunctionMapping** también puede asignar las funciones de inserción y eliminación para las asociaciones de varios a varios del modelo conceptual a los procedimientos almacenados en la base de datos subyacente. Los procedimientos almacenados a los que están asignados las funciones de modificación se deben declarar en el modelo de almacenamiento. Para obtener más información, vea elemento function (SSDL).

> [!NOTE]
> Si no asigna las tres operaciones de inserción, actualización o eliminación de un tipo de entidad a los procedimientos almacenados, se producirá un error en las operaciones no asignadas si se ejecutan en tiempo de ejecución y se genera un UpdateException.


> [!NOTE]
> Si las funciones de modificación para una entidad de una jerarquía de herencia están asignadas a procedimientos almacenados, entonces las funciones de modificación para todos los tipos de la jerarquía deben estar asignadas a procedimientos almacenados.

El elemento **ModificationFunctionMapping** puede ser un elemento secundario del elemento EntityTypeMapping o el elemento AssociationSetMapping.

El elemento **ModificationFunctionMapping** puede tener los siguientes elementos secundarios:

-   DeleteFunction (cero o uno)
-   InsertFunction (cero o uno)
-   UpdateFunction (cero o uno)

No hay atributos aplicables al elemento **ModificationFunctionMapping** .

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra la asignación de conjunto de entidades para el conjunto de entidades **People** del modelo School. Además de la asignación de columnas para el tipo de entidad **Person** , se muestra la asignación de las funciones INSERT, Update y DELETE del tipo **Person** . Las funciones asignadas se declaran en el modelo de almacenamiento.

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra la asignación del conjunto de asociaciones para el conjunto de asociaciones **CourseInstructor** en el modelo School. Además de la asignación de columnas para la Asociación **CourseInstructor** , se muestra la asignación de las funciones de inserción y eliminación de la Asociación **CourseInstructor** . Las funciones asignadas se declaran en el modelo de almacenamiento.

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```
 

 

## <a name="queryview-element-msl"></a>QueryView (Elemento) (MSL)

El elemento **QueryView** del lenguaje de especificación de asignaciones (MSL) define una asignación de solo lectura entre un tipo de entidad o una asociación en el modelo conceptual y una tabla en la base de datos subyacente. La asignación se define con una consulta Entity SQL que se evalúa con el modelo de almacenamiento y se expresa el conjunto de resultados en términos de una entidad o asociación en el modelo conceptual. Dado que las vistas de consulta son de solo lectura, no puede utilizar los comandos de actualización estándar para actualizar los tipos que se definen mediante vistas de consulta. Puede realizar las actualizaciones de estos tipos utilizando funciones de modificación. Para obtener más información, vea cómo: asignar funciones de modificación a procedimientos almacenados.

> [!NOTE]
> En el elemento **QueryView** , no se admiten Entity SQL expresiones que contengan **GroupBy**, agregados de grupo o propiedades de navegación.

 

El elemento **QueryView** puede ser un elemento secundario del elemento EntitySetMapping o del elemento AssociationSetMapping. En el primer caso, la vista de consulta define una asignación de solo lectura para una entidad del modelo conceptual. En el último caso, la vista de consulta define una asignación de solo lectura para una asociación del modelo conceptual.

> [!NOTE]
> Si el elemento **AssociationSetMapping** es para una asociación con una restricción referencial, se omite el elemento **AssociationSetMapping** . Para obtener más información, vea ReferentialConstraint (elemento) (CSDL).

El elemento **QueryView** no puede tener elementos secundarios.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **QueryView** .

| Nombre del atributo | Es obligatorio | Value                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **TypeName**   | No          | El nombre del tipo de modelo conceptual que se está asignando mediante la vista de consulta. |

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra el elemento **QueryView** como elemento secundario del elemento **EntitySetMapping** y se define una asignación de vista de consulta para el tipo de entidad **Department** en el modelo School.

``` xml
 <EntitySetMapping Name="Departments" >
   <QueryView>
     SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                         d.Name,
                                         d.Budget,
                                         d.StartDate)
     FROM SchoolModelStoreContainer.Department AS d
     WHERE d.Budget > 150000
   </QueryView>
 </EntitySetMapping>
```

Dado que la consulta solo devuelve un subconjunto de los miembros del tipo **Department** en el modelo de almacenamiento, el tipo **Department** del modelo School se ha modificado según esta asignación de la manera siguiente:

``` xml
 <EntityType Name="Department">
   <Key>
     <PropertyRef Name="DepartmentID" />
   </Key>
   <Property Type="Int32" Name="DepartmentID" Nullable="false" />
   <Property Type="String" Name="Name" Nullable="false"
             MaxLength="50" FixedLength="false" Unicode="true" />
   <Property Type="Decimal" Name="Budget" Nullable="false"
             Precision="19" Scale="4" />
   <Property Type="DateTime" Name="StartDate" Nullable="false" />
   <NavigationProperty Name="Courses"
                       Relationship="SchoolModel.FK_Course_Department"
                       FromRole="Department" ToRole="Course" />
 </EntityType>
```

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra el elemento **QueryView** como elemento secundario de un elemento **AssociationSetMapping** y se define una asignación de solo lectura para la `FK_Course_Department` asociación en el modelo School.

``` xml
 <EntityContainerMapping StorageEntityContainer="SchoolModelStoreContainer"
                         CdmEntityContainer="SchoolEntities">
   <EntitySetMapping Name="Courses" >
     <QueryView>
       SELECT VALUE SchoolModel.Course(c.CourseID,
                                       c.Title,
                                       c.Credits)
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </EntitySetMapping>
   <EntitySetMapping Name="Departments" >
     <QueryView>
       SELECT VALUE SchoolModel.Department(d.DepartmentID,
                                           d.Name,
                                           d.Budget,
                                           d.StartDate)
       FROM SchoolModelStoreContainer.Department AS d
       WHERE d.Budget > 150000
     </QueryView>
   </EntitySetMapping>
   <AssociationSetMapping Name="FK_Course_Department" >
     <QueryView>
       SELECT VALUE SchoolModel.FK_Course_Department(
         CREATEREF(SchoolEntities.Departments, row(c.DepartmentID), SchoolModel.Department),
         CREATEREF(SchoolEntities.Courses, row(c.CourseID)) )
       FROM SchoolModelStoreContainer.Course AS c
     </QueryView>
   </AssociationSetMapping>
 </EntityContainerMapping>
```
 
### <a name="comments"></a>Comentarios

Puede definir vistas de consulta para habilitar los escenarios siguientes:

-   Defina una entidad en el modelo conceptual que no incluya todas las propiedades de la entidad en el modelo de almacenamiento. Esto incluye propiedades que no tienen valores predeterminados y no admiten valores **null** .
-   Asigne las columnas calculadas del modelo de almacenamiento a las propiedades de los tipos de entidad del modelo conceptual.
-   Defina una asignación en la que las condiciones utilizadas para dividir las entidades del modelo conceptual no se basen en la igualdad. Cuando se especifica una asignación condicional mediante el elemento **Condition** , la condición proporcionada debe ser igual al valor especificado. Para obtener más información, vea condition (elemento) (MSL).
-   Asigne la misma columna en el modelo de almacenamiento a varios tipos en el modelo conceptual.
-   Asigne varios tipos a la misma tabla.
-   Defina asociaciones en el modelo conceptual que no se basen en claves externas en el esquema relacional.
-   Utilice la lógica de negocios personalizada para establecer el valor de las propiedades del modelo conceptual. Por ejemplo, podría asignar el valor de cadena "T" en el origen de datos a un valor **true**, un valor booleano, en el modelo conceptual.
-   Defina filtros condicionales para los resultados de la consulta.
-   Aplique menos restricciones en los datos del modelo conceptual que en el modelo de almacenamiento. Por ejemplo, podría hacer que una propiedad en el modelo conceptual acepte valores NULL incluso si la columna a la que está asignada no admite valores **null**.

Las consideraciones siguientes se aplican al definir las vistas de consulta para las entidades:

-   Las vistas de consulta son de solo lectura. Solo puede realizar las actualizaciones a las entidades utilizando las funciones de modificación.
-   Al definir un tipo de entidad mediante una vista de consulta, también debe definir todas las entidades relacionadas mediante vistas de consulta.
-   Al asignar una asociación varios a varios a una entidad en el modelo de almacenamiento que representa una tabla de vínculos en el esquema relacional, debe definir un elemento **QueryView** en el elemento **AssociationSetMapping** para esta tabla de vínculos.
-   Las vistas de consulta se deben definir para todos los tipos de una jerarquía de tipos. Puede hacer esto de las siguientes maneras:
-   -   Con un único elemento **QueryView** que especifica una única Entity SQL consulta que devuelve una Unión de todos los tipos de entidad de la jerarquía.
    -   Con un único elemento **QueryView** que especifica una única Entity SQL consulta que usa el operador Case para devolver un tipo de entidad específico en la jerarquía basándose en una condición específica.
    -   Con un elemento **QueryView** adicional para un tipo específico en la jerarquía. En este caso, use el atributo **TypeName** del elemento **QueryView** para especificar el tipo de entidad para cada vista.
-   Cuando se define una vista de consulta, no se puede especificar el atributo **StorageSetName** en el elemento **EntitySetMapping** .
-   Cuando se define una vista de consulta, el elemento **EntitySetMapping**no puede contener también asignaciones de **propiedades** .

## <a name="resultbinding-element-msl"></a>ResultBinding (Elemento) (MSL)

El elemento **ResultBinding** del lenguaje de especificación de asignaciones (MSL) asigna los valores de columna devueltos por los procedimientos almacenados a las propiedades de entidad del modelo conceptual cuando las funciones de modificación de tipo de entidad se asignan a los procedimientos almacenados en la base de datos subyacente. Por ejemplo, cuando un procedimiento almacenado de inserción devuelve el valor de una columna de identidad, el elemento **ResultBinding** asigna el valor devuelto a una propiedad de tipo de entidad en el modelo conceptual.

El elemento **ResultBinding** puede ser secundario del elemento InsertFunction o del elemento UpdateFunction.

El elemento **ResultBinding** no puede tener elementos secundarios.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos aplicables al elemento **ResultBinding** :

| Nombre del atributo | Es obligatorio | Value                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| **Nombre**       | Sí         | El nombre de la propiedad de entidad del modelo conceptual que se está asignando. |
| **ColumnName** | Sí         | El nombre de la columna que se está asignando.                                          |

### <a name="example"></a>Ejemplo

El ejemplo siguiente se basa en el modelo School y muestra un elemento **InsertFunction** que se usa para asignar la función de inserción del tipo de entidad **Person** al procedimiento almacenado **InsertPerson** . (El procedimiento almacenado **InsertPerson** se muestra a continuación y se declara en el modelo de almacenamiento). Se usa un elemento **ResultBinding** para asignar un valor de columna devuelto por el procedimiento almacenado (**NewPersonID**) a una propiedad de tipo de entidad (**PersonID**).

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```

En el siguiente código de Transact-SQL se describe el procedimiento almacenado **InsertPerson** :

``` SQL
 CREATE PROCEDURE [dbo].[InsertPerson]
                                @LastName nvarchar(50),
                                @FirstName nvarchar(50),
                                @HireDate datetime,
                                @EnrollmentDate datetime
                                AS
                                INSERT INTO dbo.Person (LastName,
                                                                             FirstName,
                                                                             HireDate,
                                                                             EnrollmentDate)
                                VALUES (@LastName,
                                               @FirstName,
                                               @HireDate,
                                               @EnrollmentDate);
                                SELECT SCOPE_IDENTITY() as NewPersonID;
```

## <a name="resultmapping-element-msl"></a>ResultMapping (Elemento) (MSL)

El elemento **ResultMapping** del lenguaje de especificación de asignaciones (MSL) define la asignación entre una importación de función en el modelo conceptual y un procedimiento almacenado en la base de datos subyacente cuando se cumplen las condiciones siguientes:

-   La importación de función devuelve un tipo de entidad del modelo conceptual o un tipo complejo.
-   Los nombres de las columnas devueltas por el procedimiento almacenado no coinciden exactamente con los nombres de las propiedades del tipo de entidad o el tipo complejo.

De forma predeterminada, la asignación entre las columnas devueltas por un procedimiento almacenado y un tipo de entidad o un tipo complejo se basa en los nombres de las propiedades y de las columnas. Si los nombres de columna no coinciden exactamente con los nombres de propiedad, debe utilizar el elemento **ResultMapping** para definir la asignación. Para obtener un ejemplo de la asignación predeterminada, vea FunctionImportMapping (elemento) (MSL).

El elemento **ResultMapping** es un elemento secundario del elemento FunctionImportMapping.

El elemento **ResultMapping** puede tener los siguientes elementos secundarios:

-   EntityTypeMapping (cero o más)
-   ComplexTypeMapping

No hay atributos aplicables al elemento **ResultMapping** .

### <a name="example"></a>Ejemplo

Observe el siguiente procedimiento almacenado:

``` SQL
 CREATE PROCEDURE [dbo].[GetGrades]
             @student_Id int
             AS
             SELECT     EnrollmentID as enroll_id,
                                                                             Grade as grade,
                                                                             CourseID as course_id,
                                                                             StudentID as student_id
                                               FROM dbo.StudentGrade
             WHERE StudentID = @student_Id
```

Considere también el siguiente tipo de entidad del modelo conceptual:

``` xml
 <EntityType Name="StudentGrade">
   <Key>
     <PropertyRef Name="EnrollmentID" />
   </Key>
   <Property Name="EnrollmentID" Type="Int32" Nullable="false"
             annotation:StoreGeneratedPattern="Identity" />
   <Property Name="CourseID" Type="Int32" Nullable="false" />
   <Property Name="StudentID" Type="Int32" Nullable="false" />
   <Property Name="Grade" Type="Decimal" Precision="3" Scale="2" />
 </EntityType>
```

Para crear una importación de función que devuelva instancias del tipo de entidad anterior, la asignación entre las columnas devueltas por el procedimiento almacenado y el tipo de entidad debe definirse en un elemento **ResultMapping** :

``` xml
 <FunctionImportMapping FunctionImportName="GetGrades"
                        FunctionName="SchoolModel.Store.GetGrades" >
   <ResultMapping>
     <EntityTypeMapping TypeName="SchoolModel.StudentGrade">
       <ScalarProperty Name="EnrollmentID" ColumnName="enroll_id"/>
       <ScalarProperty Name="CourseID" ColumnName="course_id"/>
       <ScalarProperty Name="StudentID" ColumnName="student_id"/>
       <ScalarProperty Name="Grade" ColumnName="grade"/>
     </EntityTypeMapping>
   </ResultMapping>
 </FunctionImportMapping>
```

## <a name="scalarproperty-element-msl"></a>ScalarProperty (Elemento) (MSL)

El elemento **ScalarProperty** del lenguaje de especificación de asignaciones (MSL) asigna una propiedad en un tipo de entidad del modelo conceptual, un tipo complejo o una asociación a una columna de tabla o a un parámetro de procedimiento almacenado en la base de datos subyacente.

> [!NOTE]
> Los procedimientos almacenados a los que están asignados las funciones de modificación se deben declarar en el modelo de almacenamiento. Para obtener más información, vea elemento function (SSDL).

El elemento **ScalarProperty** puede ser un elemento secundario de los siguientes elementos:

-   MappingFragment
-   InsertFunction
-   UpdateFunction
-   DeleteFunction
-   EndProperty
-   ComplexProperty
-   ResultMapping

Como elemento secundario del elemento **MappingFragment**, **ComplexProperty**o **EndProperty** , el elemento **ScalarProperty** asigna una propiedad del modelo conceptual a una columna de la base de datos. Como elemento secundario del elemento **InsertFunction**, **UpdateFunction**o **DeleteFunction** , el elemento **ScalarProperty** asigna una propiedad del modelo conceptual a un parámetro de procedimiento almacenado.

El elemento **ScalarProperty** no puede tener elementos secundarios.

### <a name="applicable-attributes"></a>Atributos aplicables

Los atributos que se aplican al elemento **ScalarProperty** difieren en función del rol del elemento.

En la tabla siguiente se describen los atributos que se pueden aplicar cuando el elemento **ScalarProperty** se utiliza para asignar una propiedad del modelo conceptual a una columna de la base de datos:

| Nombre del atributo | Es obligatorio | Value                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **Nombre**       | Sí         | El nombre de la propiedad del modelo conceptual que se está asignando. |
| **ColumnName** | Sí         | El nombre de la columna de tabla que se está asociando.              |

En la tabla siguiente se describen los atributos aplicables al elemento **ScalarProperty** cuando se utiliza para asignar una propiedad del modelo conceptual a un parámetro de procedimiento almacenado:

| Nombre del atributo    | Es obligatorio | Value                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**          | Sí         | El nombre de la propiedad del modelo conceptual que se está asignando.                                                                                 |
| **ParameterName** | Sí         | El nombre del parámetro que se está asignando.                                                                                                 |
| **Versión**       | No          | **Actual** o **original** , dependiendo de si el valor actual o el valor original de la propiedad se deben usar para las comprobaciones de simultaneidad. |

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra el elemento **ScalarProperty** utilizado de dos maneras:

-   Para asignar las propiedades del tipo de entidad **Person** a las columnas de la tabla **Person**.
-   Para asignar las propiedades del tipo de entidad **Person** a los parámetros del procedimiento almacenado **UpdatePerson** . Los procedimientos almacenados se declaran en el modelo de almacenamiento.

``` xml
 <EntitySetMapping Name="People">
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <MappingFragment StoreEntitySet="Person">
       <ScalarProperty Name="PersonID" ColumnName="PersonID" />
       <ScalarProperty Name="LastName" ColumnName="LastName" />
       <ScalarProperty Name="FirstName" ColumnName="FirstName" />
       <ScalarProperty Name="HireDate" ColumnName="HireDate" />
       <ScalarProperty Name="EnrollmentDate"
                       ColumnName="EnrollmentDate" />
     </MappingFragment>
 </EntityTypeMapping>
   <EntityTypeMapping TypeName="SchoolModel.Person">
     <ModificationFunctionMapping>
       <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName" />
         <ScalarProperty Name="LastName" ParameterName="LastName" />
         <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
       </InsertFunction>
       <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
         <ScalarProperty Name="EnrollmentDate"
                         ParameterName="EnrollmentDate"
                         Version="Current" />
         <ScalarProperty Name="HireDate" ParameterName="HireDate"
                         Version="Current" />
         <ScalarProperty Name="FirstName" ParameterName="FirstName"
                         Version="Current" />
         <ScalarProperty Name="LastName" ParameterName="LastName"
                         Version="Current" />
         <ScalarProperty Name="PersonID" ParameterName="PersonID"
                         Version="Current" />
       </UpdateFunction>
       <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
         <ScalarProperty Name="PersonID" ParameterName="PersonID" />
       </DeleteFunction>
     </ModificationFunctionMapping>
   </EntityTypeMapping>
 </EntitySetMapping>
```

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra el elemento **ScalarProperty** que se usa para asignar las funciones de inserción y eliminación de una asociación del modelo conceptual a los procedimientos almacenados en la base de datos. Los procedimientos almacenados se declaran en el modelo de almacenamiento.

``` xml
 <AssociationSetMapping Name="CourseInstructor"
                        TypeName="SchoolModel.CourseInstructor"
                        StoreEntitySet="CourseInstructor">
   <EndProperty Name="Person">
     <ScalarProperty Name="PersonID" ColumnName="PersonID" />
   </EndProperty>
   <EndProperty Name="Course">
     <ScalarProperty Name="CourseID" ColumnName="CourseID" />
   </EndProperty>
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertCourseInstructor" >   
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </InsertFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeleteCourseInstructor">
       <EndProperty Name="Course">
         <ScalarProperty Name="CourseID" ParameterName="courseId"/>
       </EndProperty>
       <EndProperty Name="Person">
         <ScalarProperty Name="PersonID" ParameterName="instructorId"/>
       </EndProperty>
     </DeleteFunction>
   </ModificationFunctionMapping>
 </AssociationSetMapping>
```

## <a name="updatefunction-element-msl"></a>UpdateFunction (Elemento) (MSL)

El elemento **UpdateFunction** del lenguaje de especificación de asignaciones (MSL) asigna la función de actualización de un tipo de entidad del modelo conceptual a un procedimiento almacenado en la base de datos subyacente. Los procedimientos almacenados a los que están asignados las funciones de modificación se deben declarar en el modelo de almacenamiento. Para obtener más información, vea elemento function (SSDL).

> [!NOTE]
>  Si no asigna las tres operaciones de inserción, actualización o eliminación de un tipo de entidad a los procedimientos almacenados, se producirá un error en las operaciones no asignadas si se ejecutan en tiempo de ejecución y se genera un UpdateException.

El elemento **UpdateFunction** puede ser un elemento secundario del elemento ModificationFunctionMapping y aplicarse al elemento EntityTypeMapping.

El elemento **UpdateFunction** puede tener los siguientes elementos secundarios:

-   AssociationEnd (cero o más)
-   ComplexProperty (cero o más)
-   ResultBinding (cero o uno)
-   ScalarProperty (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **UpdateFunction** .

| Nombre del atributo            | Es obligatorio | Value                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombrefunción**          | Sí         | El nombre completo, calificado con el espacio de nombres, del procedimiento almacenado al que la función de actualización está asignada. El procedimiento almacenado se debe declarar en el modelo de almacenamiento. |
| **RowsAffectedParameter** | No          | El nombre del parámetro de salida que devuelve el número de filas afectadas.                                                                               |

### <a name="example"></a>Ejemplo

El ejemplo siguiente se basa en el modelo School y muestra el elemento **UpdateFunction** que se usa para asignar la función de actualización del tipo de entidad **Person** al procedimiento almacenado **UpdatePerson** . El procedimiento almacenado **UpdatePerson** se declara en el modelo de almacenamiento.

``` xml
 <EntityTypeMapping TypeName="SchoolModel.Person">
   <ModificationFunctionMapping>
     <InsertFunction FunctionName="SchoolModel.Store.InsertPerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName" />
       <ScalarProperty Name="LastName" ParameterName="LastName" />
       <ResultBinding Name="PersonID" ColumnName="NewPersonID" />
     </InsertFunction>
     <UpdateFunction FunctionName="SchoolModel.Store.UpdatePerson">
       <ScalarProperty Name="EnrollmentDate"
                       ParameterName="EnrollmentDate"
                       Version="Current" />
       <ScalarProperty Name="HireDate" ParameterName="HireDate"
                       Version="Current" />
       <ScalarProperty Name="FirstName" ParameterName="FirstName"
                       Version="Current" />
       <ScalarProperty Name="LastName" ParameterName="LastName"
                       Version="Current" />
       <ScalarProperty Name="PersonID" ParameterName="PersonID"
                       Version="Current" />
     </UpdateFunction>
     <DeleteFunction FunctionName="SchoolModel.Store.DeletePerson">
       <ScalarProperty Name="PersonID" ParameterName="PersonID" />
     </DeleteFunction>
   </ModificationFunctionMapping>
 </EntityTypeMapping>
```
