---
title: 'Especificación de CSDL: EF6'
description: Especificación de CSDL en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/csdl-spec
ms.openlocfilehash: 9fdd8fc5ed16f7ba7d11e79a9449f120f5d579c2
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066361"
---
# <a name="csdl-specification"></a>Especificación CSDL
El lenguaje de definición de esquemas conceptuales (CSDL) es un lenguaje basado en XML que describe las entidades, las relaciones y las funciones que conforman un modelo conceptual de una aplicación controlada por datos. Este modelo conceptual puede ser utilizado por el Entity Framework o WCF Data Services. Los metadatos que se describen con CSDL los utiliza el Entity Framework para asignar entidades y relaciones que se definen en un modelo conceptual a un origen de datos. Para obtener más información, vea [especificación de SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) y especificación de [MSL](xref:ef6/modeling/designer/advanced/edmx/msl-spec).

CSDL es la implementación del Entity Framework del Entity Data Model.

En una aplicación Entity Framework, los metadatos del modelo conceptual se cargan desde un archivo. CSDL (escrito en CSDL) en una instancia de System. Data. Metadata. Edm. EdmItemCollection y son accesibles mediante métodos en la clase System. Data. Metadata. Edm. MetadataWorkspace. Entity Framework utiliza los metadatos del modelo conceptual para traducir las consultas del modelo conceptual a comandos específicos del origen de datos.

El diseñador de EF almacena la información del modelo conceptual en un archivo. edmx en tiempo de diseño. En el momento de la compilación, el diseñador de EF usa información en un archivo. edmx para crear el archivo. CSDL que necesita Entity Framework en tiempo de ejecución.

Las versiones de CSDL se diferencian por los espacios de nombres XML.

| Versión de CSDL | Espacio de nombres XML                                |
|:-------------|:---------------------------------------------|
| CSDL v1      | https://schemas.microsoft.com/ado/2006/04/edm |
| CSDL V2      | https://schemas.microsoft.com/ado/2008/09/edm |
| CSDL V3      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a>Association (Elemento) (CSDL)

Un elemento **Association** define una relación entre dos tipos de entidad. Una asociación debe especificar los tipos de entidad que están implicados en la relación y el posible número de tipos de entidad en cada extremo de la relación, que se conoce como multiplicidad. La multiplicidad de un extremo de asociación puede tener un valor de uno (1), cero o uno (0.. 1) o varios ( \* ). Esta información se especifica en dos elementos End secundarios.

Es posible obtener acceso a las instancias de tipo de entidad situadas en un extremo de la asociación a través de las propiedades de navegación o las claves externas, si estas se exponen en un tipo de entidad.

En una aplicación, una instancia de una asociación representa una asociación concreta entre las instancias de tipos de entidad. Las instancias de asociación se agrupan de manera lógica en un conjunto de asociaciones.

Un elemento **Association** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   End (exactamente 2 elementos)
-   ReferentialConstraint (cero o un elemento)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Association** .

| Nombre del atributo | Es obligatorio | Value                        |
|:---------------|:------------|:-----------------------------|
| **Nombre**       | Sí         | Nombre de la asociación. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Association** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **Association** que define la Asociación **CustomerOrders** cuando las claves externas no se han expuesto en los tipos de entidad **Customer** y **Order** . Los valores de **multiplicidad** de cada **extremo** de la Asociación indican que se pueden asociar muchos **pedidos** a un **cliente**, pero solo un **cliente** puede asociarse a un **pedido**. Además, el elemento **aleliminar** indica que todos los **pedidos** relacionados con un **cliente** determinado y que se han cargado en el ObjectContext se eliminarán si se elimina el **cliente** .

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

En el ejemplo siguiente se muestra un elemento **Association** que define la Asociación **CustomerOrders** cuando las claves externas se han expuesto en los tipos de entidad **Customer** y **Order** . Con las claves externas expuestas, la relación entre las entidades se administra con un elemento **ReferentialConstraint** . Un elemento AssociationSetMapping correspondiente no es necesario para asignar esta asociación al origen de datos.

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
   <ReferentialConstraint>
        <Principal Role="Customer">
            <PropertyRef Name="Id" />
        </Principal>
        <Dependent Role="Order">
             <PropertyRef Name="CustomerId" />
         </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="associationset-element-csdl"></a>AssociationSet (Elemento) (CSDL)

El elemento **AssociationSet** en el lenguaje de definición de esquemas conceptuales (CSDL) es un contenedor lógico para las instancias de asociación del mismo tipo. Un conjunto de asociaciones proporciona una definición para agrupar las instancias de la asociación con objeto de que se puedan asignar a un origen de datos.  

El elemento **AssociationSet** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   End (se requieren exactamente dos elementos)
-   Elementos Annotation (cero o más elementos)

El atributo **Association** especifica el tipo de asociación que contiene un conjunto de asociaciones. Los conjuntos de entidades que componen los extremos de un conjunto de asociaciones se especifican con exactamente dos elementos **finales** secundarios.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **AssociationSet** .

| Nombre del atributo  | Es obligatorio | Value                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**        | Sí         | Nombre del conjunto de entidades. El valor del atributo **Name** no puede ser el mismo que el valor del atributo **Association** .                                 |
| **Asociación** | Sí         | Nombre completo de la asociación cuyas instancias contiene el conjunto de asociaciones. La asociación debe estar en el mismo espacio de nombres que el conjunto de asociaciones. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **AssociationSet** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityContainer** con dos elementos **AssociationSet** :

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="collectiontype-element-csdl"></a>CollectionType (Elemento de CSDL)

El elemento **CollectionType** del lenguaje de definición de esquemas conceptuales (CSDL) especifica que un parámetro de función o un tipo de valor devuelto de función es una colección. El elemento **CollectionType** puede ser un elemento secundario del elemento Parameter o del elemento ReturnType (function). El tipo de colección se puede especificar mediante el atributo **Type** o uno de los siguientes elementos secundarios:

-   **CollectionType**
-   ReferenceType
-   RowType
-   TypeRef

> [!NOTE]
> Un modelo no se validará si el tipo de una colección se especifica con el atributo **Type** y un elemento secundario.

 

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **CollectionType** . Tenga en cuenta que los atributos **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**y **collation** solo se aplican a las colecciones de **EDMSimpleTypes**.

| Nombre del atributo                                                          | Es obligatorio | Value                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Tipo**                                                                | No          | Tipo de la colección.                                                                                                                                                                                                      |
| **Admisión de valores NULL**                                                            | No          | **True** (el valor predeterminado) o **False**, según si la propiedad puede tener un valor nulo. <br/> [!NOTE]                                                                                                                 |
| > en CSDL v1, una propiedad de tipo complejo debe tener `Nullable="False"` . |             |                                                                                                                                                                                                                                  |
| **DefaultValue**                                                        | No          | Valor predeterminado de la propiedad.                                                                                                                                                                                               |
| **MaxLength**                                                           | No          | La longitud máxima del valor de la propiedad.                                                                                                                                                                                        |
| **FixedLength**                                                         | No          | **True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena de longitud fija.                                                                                                                           |
| **Precisión**                                                           | No          | Precisión del valor de propiedad.                                                                                                                                                                                             |
| **Escala**                                                               | No          | Escala del valor de propiedad.                                                                                                                                                                                                 |
| **SRID**                                                                | No          | Identificador de referencia del sistema espacial. Solo es válido para las propiedades de los tipos espaciales.Para obtener más información, consulte [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx) . |
| **Unicode**                                                             | No          | **True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena Unicode.                                                                                                                                |
| **Intercalación**                                                           | No          | Cadena que especifica la secuencia de intercalación que se usará en el origen de datos.                                                                                                                                                    |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **CollectionType** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra una función definida por el modelo que utiliza un elemento **CollectionType** para especificar que la función devuelve una colección de tipos de entidad **Person** (tal y como se especifica con el atributo **elementType** ).

``` xml
 <Function Name="LastNamesAfter">
        <Parameter Name="someString" Type="Edm.String"/>
        <ReturnType>
             <CollectionType  ElementType="SchoolModel.Person"/>
        </ReturnType>
        <DefiningExpression>
             SELECT VALUE p
             FROM SchoolEntities.People AS p
             WHERE p.LastName >= someString
        </DefiningExpression>
 </Function>
```
 

En el ejemplo siguiente se muestra una función definida por el modelo que utiliza un elemento **CollectionType** para especificar que la función devuelve una colección de filas (tal y como se especifica en el elemento **RowType** ).

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

En el ejemplo siguiente se muestra una función definida por el modelo que usa el elemento **CollectionType** para especificar que la función acepta como parámetro una colección de tipos de entidad **Department** .

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="complextype-element-csdl"></a>ComplexType (Elemento) (CSDL)

Un elemento **complexType** define una estructura de datos formada por propiedades **EdmSimpleType** u otros tipos complejos.Un tipo complejo puede ser una propiedad de un tipo de entidad o de otro tipo complejo. Un tipo complejo se parece a un tipo de entidad en que también define datos. Sin embargo, existen algunas diferencias clave entre los tipos complejos y los tipos de entidad:

-   Los tipos complejos no tienen identidades (o claves) y, por consiguiente, no pueden existir de forma independiente. Los tipos complejos solo pueden existir como propiedades de tipos de entidad u otros tipos complejos.
-   Los tipos complejos no pueden participar en asociaciones. Los extremos de una asociación no pueden ser tipos complejos y, por consiguiente, no se pueden definir propiedades de navegación para tipos complejos.
-   Una propiedad de tipo complejo no puede tener un valor nulo, aunque las propiedades escalares de un tipo complejo se pueden establecer cada una con el valor nulo.

Un elemento **complexType** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Property (cero o más elementos)
-   Elementos Annotation (cero o más elementos)

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **complexType** .

| Nombre del atributo                                                                                                 | Es obligatorio | Value                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nombre                                                                                                           | Sí         | El nombre del tipo complejo. El nombre de un tipo complejo no puede ser igual que el nombre de otro tipo complejo, tipo de entidad o asociación que esté dentro del ámbito del modelo. |
| BaseType                                                                                                       | No          | El nombre de otro tipo complejo que es el tipo base del tipo complejo que se define. <br/> [!NOTE]                                                                     |
| > este atributo no es aplicable en CSDL v1. La herencia de los tipos complejos no se admite en esa versión. |             |                                                                                                                                                                                     |
| Descripción breve                                                                                                       | No          | **True** o **false** (valor predeterminado) en función de si el tipo complejo es un tipo abstracto. <br/> [!NOTE]                                                                  |
| > este atributo no es aplicable en CSDL v1. Los tipos complejos de esa versión no pueden ser tipos abstractos.         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **complexType** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un tipo complejo, **Address**, con las propiedades **EdmSimpleType** **StreetAddress**, **City**, **StateOrProvince**, **Country**y **PostalCode**.

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

Para definir la **Dirección** de tipo complejo (anterior) como una propiedad de un tipo de entidad, debe declarar el tipo de propiedad en la definición de tipo de entidad. En el ejemplo siguiente se muestra la propiedad **Address** como un tipo complejo en un tipo de entidad (**publicador**):

``` xml
 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BooksModel.Address" Name="Address" Nullable="false" />
       <NavigationProperty Name="Books" Relationship="BooksModel.PublishedBy"
                           FromRole="Publisher" ToRole="Book" />
     </EntityType>
```
 

 

## <a name="definingexpression-element-csdl"></a>DefiningExpression (Elemento) (CSDL)

El elemento **DefiningExpression** en el lenguaje de definición de esquemas conceptuales (CSDL) contiene una expresión Entity SQL que define una función en el modelo conceptual.  

> [!NOTE]
> Para fines de validación, un elemento **DefiningExpression** puede contener contenido arbitrario. Sin embargo, Entity Framework producirá una excepción en tiempo de ejecución si un elemento **DefiningExpression** no contiene Entity SQL válidos.

 

### <a name="applicable-attributes"></a>Atributos aplicables

Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **DefiningExpression** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se usa un elemento **DefiningExpression** para definir una función que devuelve el número de años transcurridos desde que se publicó un libro. El contenido del elemento **DefiningExpression** se escribe en Entity SQL.

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a>Dependent (Elemento) (CSDL)

El elemento **dependiente** en el lenguaje de definición de esquemas conceptuales (CSDL) es un elemento secundario del elemento ReferentialConstraint y define el extremo dependiente de una restricción referencial. Un elemento **ReferentialConstraint** define una funcionalidad similar a una restricción de integridad referencial en una base de datos relacional. Del mismo modo que una columna (o columnas) de una tabla de base de datos puede hacer referencia a la clave principal de otra tabla, una propiedad (o propiedades) de un tipo de entidad puede hacer referencia a la clave de entidad de otro tipo de entidad. El tipo de entidad al que se hace referencia se denomina *extremo principal* de la restricción. El tipo de entidad que hace referencia al extremo principal se denomina *extremo dependiente* de la restricción. Los elementos **PropertyRef** se usan para especificar qué claves hacen referencia al extremo principal.

El elemento **dependiente** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   PropertyRef (uno o varios elementos)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **dependiente** .

| Nombre del atributo | Es obligatorio | Value                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **Rol**       | Sí         | Nombre del tipo de entidad del extremo dependiente de la asociación. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **dependiente** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **ReferentialConstraint** que se usa como parte de la definición de la Asociación **PublishedBy** . La propiedad **PublisherId** del tipo de entidad **book** constituye el extremo dependiente de la restricción referencial.

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="documentation-element-csdl"></a>Documentation (Elemento) (CSDL)

El elemento **Documentation** del lenguaje de definición de esquemas conceptuales (CSDL) se puede usar para proporcionar información sobre un objeto que se define en un elemento primario. En un archivo. edmx, cuando el elemento **Documentation** es un elemento secundario de un elemento que aparece como un objeto en la superficie de diseño del diseñador EF (como una entidad, asociación o propiedad), el contenido del elemento **Documentation** aparecerá en la ventana **propiedades** de Visual Studio para el objeto.

El elemento **Documentation** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   **Summary**: breve descripción del elemento primario. (cero o un elemento).
-   **LongDescription**: una descripción amplia del elemento primario. (cero o un elemento).
-   Elementos de anotación. (cero o más elementos).

### <a name="applicable-attributes"></a>Atributos aplicables

Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Documentation** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra el elemento **Documentation** como un elemento secundario de un elemento EntityType. Si el fragmento de código siguiente estaba en el contenido CSDL de un archivo. edmx, el contenido de los elementos **Summary** y **longDescription** aparecerá en la ventana **propiedades** de Visual Studio al hacer clic en el `Customer` tipo de entidad.

``` xml
 <EntityType Name="Customer">
    <Documentation>
      <Summary>Summary here.</Summary>
      <LongDescription>Long description here.</LongDescription>
    </Documentation>
    <Key>
      <PropertyRef Name="CustomerId" />
    </Key>
    <Property Type="Int32" Name="CustomerId" Nullable="false" />
    <Property Type="String" Name="Name" Nullable="false" />
 </EntityType>
```
 

 

## <a name="end-element-csdl"></a>End (Elemento) (CSDL)

El elemento **End** en el lenguaje de definición de esquemas conceptuales (CSDL) puede ser un elemento secundario del elemento Association o del elemento AssociationSet. En cada caso, el rol del elemento **final** es diferente y los atributos correspondientes son diferentes.

### <a name="end-element-as-a-child-of-the-association-element"></a>Elemento End como elemento secundario del elemento Association

Un elemento **End** (como elemento secundario del elemento **Association** ) identifica el tipo de entidad en un extremo de una asociación y el número de instancias de tipo de entidad que pueden existir en ese extremo de una asociación. Los extremos de asociación se definen como parte de una asociación, y esta debe tener exactamente dos extremos. Es posible obtener acceso a las instancias de tipo de entidad situadas en un extremo de la asociación a través de las propiedades de navegación o las claves externas si estas se exponen en un tipo de entidad.  

Un elemento **End** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   OnDelete (cero o un elemento)
-   Elementos Annotation (cero o más elementos)

#### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **End** cuando es el elemento secundario de un elemento **Association** .

| Nombre del atributo   | Es obligatorio | Value                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Tipo**         | Sí         | Nombre del tipo de entidad de un extremo de la asociación.                                                                                                                                                                                                                                                                                                                                                         |
| **Rol**         | No          | Nombre para el extremo de la asociación. Si no se proporciona ningún nombre, se usará el nombre del tipo de entidad del extremo de la asociación.                                                                                                                                                                                                                                                                                           |
| **Multiplicidad** | Sí         | **1**, **0.. 1**o, **\*** dependiendo del número de instancias de tipo de entidad que pueden estar al final de la asociación. <br/> **1** indica que existe exactamente una instancia de tipo de entidad en el extremo de la asociación. <br/> **0.. 1** indica que hay cero o una instancia de tipo de entidad en el extremo de la asociación. <br/> **\*** indica que hay cero, una o más instancias de tipo de entidad en el extremo de la asociación. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **final** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

#### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **Association** que define la Asociación **CustomerOrders** . Los valores de **multiplicidad** de cada **extremo** de la Asociación indican que se pueden asociar muchos **pedidos** a un **cliente**, pero solo un **cliente** puede asociarse a un **pedido**. Además, el elemento **aleliminar** indica que todos los **pedidos** relacionados con un **cliente** determinado y que se han cargado en el ObjectContext se eliminarán si se elimina el **cliente** .

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a>Elemento End como elemento secundario del elemento AssociationSet

El elemento **End** especifica un extremo de un conjunto de asociaciones. El elemento **AssociationSet** debe contener dos elementos **End** . La información contenida en un elemento **End** se utiliza para asignar un conjunto de asociaciones a un origen de datos.

Un elemento **End** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Elementos Annotation (cero o más elementos)

> [!NOTE]
> Los elementos de anotación deben aparecer después de todos los demás elementos secundarios. Los elementos Annotation solo se permiten en CSDL V2 y versiones posteriores.

 

#### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **End** cuando es el elemento secundario de un elemento **AssociationSet** .

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | Sí         | Nombre del elemento **EntitySet** que define un extremo del elemento primario **AssociationSet** . El elemento **EntitySet** debe definirse en el mismo contenedor de entidades que el elemento primario **AssociationSet** . |
| **Rol**       | No          | Nombre del extremo del conjunto de asociaciones. Si no se utiliza el atributo **role** , el nombre del extremo del conjunto de asociaciones será el nombre del conjunto de entidades.                                                                   |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **final** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

#### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityContainer** con dos elementos **AssociationSet** , cada uno con dos elementos **End** :

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitycontainer-element-csdl"></a>EntityContainer (Elemento) (CSDL)

El elemento **EntityContainer** en el lenguaje de definición de esquemas conceptuales (CSDL) es un contenedor lógico para conjuntos de entidades, conjuntos de asociaciones e importaciones de funciones. Un contenedor de entidades de modelo conceptual se asigna a un contenedor de entidades de modelo de almacenamiento a través del elemento EntityContainerMapping. Un contenedor de entidades de modelo de almacenamiento describe la estructura de la base de datos: los conjuntos de entidades describen las tablas, los conjuntos de asociaciones describen las restricciones de clave externa y las importaciones de función describen los procedimientos almacenados en una base de datos.

Un elemento **EntityContainer** puede tener cero o un elemento de documentación. Si hay un elemento de **documentación** , debe preceder a todos los elementos **EntitySet**, **AssociationSet**y **FunctionImport** .

Un elemento **EntityContainer** puede tener cero o más de los elementos secundarios siguientes (en el orden mostrado):

-   EntitySet
-   AssociationSet
-   FunctionImport
-   Elementos Annotation

Puede extender un elemento **EntityContainer** para incluir el contenido de otro **EntityContainer** que esté en el mismo espacio de nombres. Para incluir el contenido de otro **EntityContainer**, en el elemento **EntityContainer** de referencia, establezca el valor del atributo **extends** en el nombre del elemento **EntityContainer** que desea incluir. Todos los elementos secundarios del elemento **EntityContainer** incluido se tratarán como elementos secundarios del elemento **EntityContainer** que hace la referencia.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **using** .

| Nombre del atributo | Es obligatorio | Value                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| **Nombre**       | Sí         | Nombre del contenedor de entidades.                               |
| **Llegar**    | No          | Nombre de otro contenedor de entidades dentro del mismo espacio de nombres. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **EntityContainer** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityContainer** que define tres conjuntos de entidades y dos conjuntos de asociaciones.

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entityset-element-csdl"></a>EntitySet (Elemento) (CSDL)

El elemento **EntitySet** del lenguaje de definición de esquemas conceptuales es un contenedor lógico para las instancias de un tipo de entidad y las instancias de cualquier tipo que se deriva de ese tipo de entidad. La relación entre un tipo de entidad y un conjunto de entidades es análoga a la relación entre una fila y una tabla en una base de datos relacional. Igual que una fila, un tipo de entidad define un conjunto de datos relacionados y, lo mismo que una tabla, un conjunto de entidades contiene instancias de esa definición. Un conjunto de entidades proporciona una construcción para agrupar las instancias del tipo de entidad para que se pueden asignar a las estructuras de datos relacionadas en un origen de datos.  

Se pueden definir varios conjuntos de entidades para un tipo de entidad determinado.

> [!NOTE]
> El diseñador de EF no es compatible con los modelos conceptuales que contienen varios conjuntos de entidades por tipo.

 

El elemento **EntitySet** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Elemento Documentation (cero o más elementos)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntitySet** .

| Nombre del atributo | Es obligatorio | Value                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **Nombre**       | Sí         | Nombre del conjunto de entidades.                                                              |
| **EntityType** | Sí         | El nombre completo del tipo de entidad para el que el conjunto de entidades contiene las instancias. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **EntitySet** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityContainer** con tres elementos **EntitySet** :

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="WrittenBy" Association="BooksModel.WrittenBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

Es posible definir varios conjuntos de entidades por tipo (MEST). En el ejemplo siguiente se define un contenedor de entidades con dos conjuntos de entidades para el tipo de entidad **book** :

``` xml
 <EntityContainer Name="BooksContainer" >
   <EntitySet Name="Books" EntityType="BooksModel.Book" />
   <EntitySet Name="FictionBooks" EntityType="BooksModel.Book" />
   <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
   <EntitySet Name="Authors" EntityType="BooksModel.Author" />
   <AssociationSet Name="PublishedBy" Association="BooksModel.PublishedBy">
     <End Role="Book" EntitySet="Books" />
     <End Role="Publisher" EntitySet="Publishers" />
   </AssociationSet>
   <AssociationSet Name="BookAuthor" Association="BooksModel.BookAuthor">
     <End Role="Book" EntitySet="Books" />
     <End Role="Author" EntitySet="Authors" />
   </AssociationSet>
 </EntityContainer>
```
 

 

## <a name="entitytype-element-csdl"></a>EntityType (Elemento) (CSDL)

El elemento **EntityType** representa la estructura de un concepto de nivel superior, como un cliente o un pedido, en un modelo conceptual. Un tipo de entidad es una plantilla para las instancias de los tipos de entidad de una aplicación. Cada plantilla contiene la información siguiente:

-   Un nombre único. (Requerido)
-   Una clave de entidad definida por una o varias propiedades. (Requerido)
-   Propiedades para el almacenamiento de datos. (Opcional).
-   Propiedades de navegación que permiten desplazarse de un extremo de la asociación al otro. (Opcional).

En una aplicación, una instancia de un tipo de entidad representa un objeto específico (como un cliente o un pedido concreto). Cada una de las instancias de un tipo de entidad debe tener una clave de entidad única dentro de un conjunto de entidades.

Dos instancias de tipo de entidad se consideran iguales solo si son del mismo tipo y los valores de sus claves de entidad son idénticos.

Un elemento **EntityType** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Key (cero o un elemento)
-   Property (cero o más elementos)
-   NavigationProperty (cero o más elementos)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntityType** .

| Nombre del atributo                                                                                                                                  | Es obligatorio | Value                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| **Nombre**                                                                                                                                        | Sí         | El nombre del tipo de entidad.                                                                     |
| **BaseType**                                                                                                                                    | No          | El nombre de otro tipo de entidad que sea el tipo base del tipo de entidad que se define.  |
| **Descripción**                                                                                                                                    | No          | **True** o **false**, dependiendo de si el tipo de entidad es un tipo abstracto.                 |
| **OpenType**                                                                                                                                    | No          | **True** o **false** , dependiendo de si el tipo de entidad es un tipo de entidad abierto. <br/> [!NOTE] |
| > el atributo **OpenType** solo es aplicable a los tipos de entidad que se definen en los modelos conceptuales que se usan con el Data Services ADO.net. |             |                                                                                                  |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **EntityType** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityType** con tres elementos **Property** y dos elementos **NavigationProperty** :

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="enumtype-element-csdl"></a>Elemento EnumType (CSDL)

El elemento **enumType** representa un tipo enumerado.

Un elemento **enumType** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Miembro (cero o más elementos)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **enumType** .

| Nombre del atributo     | Es obligatorio | Value                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**           | Sí         | El nombre del tipo de entidad.                                                                                                                                                                  |
| **IsFlags**        | No          | **True** o **false**, dependiendo de si el tipo de enumeración se puede usar como un conjunto de marcas. El valor predeterminado es **false.**                                                                     |
| **UnderlyingType** | No          | **EDM. Byte**, **EDM. Int16**, **EDM. Int32**, **EDM. Int64** o **EDM. SByte** que define el intervalo de valores del tipo. El tipo subyacente predeterminado de los elementos de enumeración es **EDM. Int32.**. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **enumType** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **enumType** con tres elementos **member** :

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a>Function (Elemento) (CSDL)

El elemento **function** en el lenguaje de definición de esquemas conceptuales (CSDL) se utiliza para definir o declarar funciones en el modelo conceptual. Una función se define utilizando un elemento DefiningExpression.  

Un elemento **function** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Parameter (cero o más elementos)
-   DefiningExpression (cero o un elemento)
-   ReturnType (función) (cero o un elemento)
-   Elementos Annotation (cero o más elementos)

Un tipo de valor devuelto para una función debe especificarse con el elemento **ReturnType** (function) o el atributo **ReturnType** (consulte a continuación), pero no ambos. Los tipos de valores devueltos posibles son EdmSimpleType, tipo de entidad, tipo complejo, tipo de fila o tipo ref (o una colección de uno de estos tipos).

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento de **función** .

| Nombre del atributo | Es obligatorio | Value                              |
|:---------------|:------------|:-----------------------------------|
| **Nombre**       | Sí         | Nombre de la función.          |
| **ReturnType** | No          | El tipo devuelto por la función. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento de **función** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se usa un elemento **function** para definir una función que devuelve el número de años transcurridos desde que se contrató a un instructor.

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a>FunctionImport (Elemento) (CSDL)

El elemento **FunctionImport** del lenguaje de definición de esquemas conceptuales (CSDL) representa una función que se define en el origen de datos, pero que está disponible para los objetos a través del modelo conceptual. Por ejemplo, un elemento Function del modelo de almacenamiento se puede usar para representar un procedimiento almacenado en una base de datos. Un elemento **FunctionImport** del modelo conceptual representa la función correspondiente en una aplicación Entity Framework y se asigna a la función de modelo de almacenamiento mediante el elemento FunctionImportMapping. Cuando se llama a la función en la aplicación, el procedimiento almacenado correspondiente se ejecuta en la base de datos.

El elemento **FunctionImport** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Parameter (cero o más elementos)
-   Elementos Annotation (cero o más elementos)
-   ReturnType (FunctionImport) (cero o más elementos permitidos)

Debe definirse un elemento de **parámetro** para cada parámetro aceptado por la función.

Un tipo de valor devuelto para una función debe especificarse con el elemento **ReturnType** (FunctionImport) o el atributo **ReturnType** (consulte a continuación), pero no ambos. El valor de tipo devuelto debe ser una colección de EdmSimpleType, EntityType o ComplexType.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **FunctionImport** .

| Nombre del atributo   | Es obligatorio | Value                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**         | Sí         | Nombre de la función importada.                                                                                                                                                                    |
| **ReturnType**   | No          | Tipo devuelto por la función. No utilice este atributo si la función no devuelve un valor. De lo contrario, el valor debe ser una colección de ComplexType, EntityType o EDMSimpleType.        |
| **EntitySet**    | No          | Si la función devuelve una colección de tipos de entidad, el valor de **EntitySet** debe ser el conjunto de entidades al que pertenece la colección. De lo contrario, el atributo **EntitySet** no debe usarse. |
| **IsComposable** | No          | Si el valor se establece en true, la función es ajustable (función con valores de tabla) y se puede usar en una consulta LINQ.El valor predeterminado es **false**.                                                           |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **FunctionImport** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **FunctionImport** que acepta un parámetro y devuelve una colección de tipos de entidad:

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a>Key (Elemento) (CSDL)

El elemento **key** es un elemento secundario del elemento EntityType y define una *clave de entidad* (una propiedad o un conjunto de propiedades de un tipo de entidad que determinan la identidad). Las propiedades que constituyen una entidad se eligen en tiempo de diseño. Los valores de las propiedades de clave de entidad deben identificar de forma inequívoca en tiempo de ejecución una instancia de tipo de entidad dentro de un conjunto de entidades. Las propiedades que constituyen una clave de entidad se deben elegir de tal forma que garanticen la unicidad de las instancias de un conjunto de entidades. El elemento **key** define una clave de entidad haciendo referencia a una o más de las propiedades de un tipo de entidad.

El elemento **key** puede tener los siguientes elementos secundarios:

-   PropertyRef (uno o varios elementos)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **key** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se define un tipo de entidad denominado **book**. La clave de entidad se define haciendo referencia a la propiedad **ISBN** del tipo de entidad.

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

La propiedad **ISBN** es una buena elección para la clave de entidad porque un número de libro estándar internacional (ISBN) identifica de forma única un libro.

En el ejemplo siguiente se muestra un tipo de entidad (**autor**) que tiene una clave de entidad que consta de dos propiedades, **nombre** y **Dirección**.

``` xml
 <EntityType Name="Author">
   <Key>
     <PropertyRef Name="Name" />
     <PropertyRef Name="Address" />
   </Key>
   <Property Type="String" Name="Name" Nullable="false" />
   <Property Type="String" Name="Address" Nullable="false" />
   <NavigationProperty Name="Books" Relationship="BooksModel.WrittenBy"
                       FromRole="Author" ToRole="Book" />
 </EntityType>
```
 

Usar el **nombre** y la **Dirección** de la clave de entidad es una opción razonable, ya que es poco probable que dos autores del mismo nombre se encuentren en la misma dirección. Sin embargo, esta opción no garantiza por completo la existencia de claves de entidad únicas en un conjunto de entidades. En este caso, se recomienda agregar una propiedad, como **AuthorId**, que pueda usarse para identificar de forma única un autor.

 

## <a name="member-element-csdl"></a>Elemento Member (CSDL)

El elemento **member** es un elemento secundario del elemento enumType y define un miembro del tipo enumerado.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **FunctionImport** .

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**       | Sí         | Nombre del miembro.                                                                                                                                                                  |
| **Valor**      | No          | Valor del miembro. De forma predeterminada, el primer miembro tiene el valor 0 y el valor de cada enumerador sucesivo se incrementa en 1. Pueden existir varios miembros con los mismos valores. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **FunctionImport** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **enumType** con tres elementos **member** :

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a>NavigationProperty (Elemento) (CSDL)

Un elemento **NavigationProperty** define una propiedad de navegación, que proporciona una referencia al otro extremo de una asociación. A diferencia de las propiedades definidas con el elemento Property, las propiedades de navegación no definen la forma ni las características de los datos. Proporcionan una manera de desplazarse por una asociación entre dos tipos de entidad.

Las propiedades de navegación son opcionales en los dos tipos de entidad de los extremos de una asociación. Si define una propiedad de navegación en un tipo de entidad del extremo de una asociación, no tiene que definir una propiedad de navegación en el tipo de entidad del otro extremo de la asociación.

El tipo de datos devuelto por una propiedad de navegación viene determinado por la multiplicidad de su extremo remoto de la asociación. Por ejemplo, supongamos que existe una propiedad de navegación, **OrdersNavProp**, en un tipo de entidad **Customer** y navega por una asociación uno a varios entre **Customer** y **Order**. Dado que el extremo remoto de la Asociación para la propiedad de navegación tiene una multiplicidad de varios ( \* ), su tipo de datos es una colección (de **orden**). Del mismo modo, si una propiedad de navegación, **CustomerNavProp**, existe en el tipo de entidad **Order** , su tipo de datos sería **Customer** , ya que la multiplicidad del extremo remoto es uno (1).

Un elemento **NavigationProperty** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **NavigationProperty** .

| Nombre del atributo   | Es obligatorio | Value                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**         | Sí         | Nombre de la propiedad de navegación.                                                                                                                                                                                                             |
| **Relación** | Sí         | Nombre de una asociación que se encuentra dentro del ámbito del modelo.                                                                                                                                                                                |
| **ToRole**       | Sí         | Extremo de la asociación en el que finaliza la navegación. El valor del atributo **ToRole** debe ser el mismo que el valor de uno de los atributos de **rol** definidos en uno de los extremos de la Asociación (definidos en el elemento AssociationEnd).       |
| **FromRole**     | Sí         | Extremo de la asociación desde el que comienza la navegación. El valor del atributo **FromRole** debe ser el mismo que el valor de uno de los atributos de **rol** definidos en uno de los extremos de la Asociación (definidos en el elemento AssociationEnd). |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **NavigationProperty** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se define un tipo de entidad (**book**) con dos propiedades de navegación (**PublishedBy** y **WrittenBy**):

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

 

## <a name="ondelete-element-csdl"></a>OnDelete (Elemento) (CSDL)

El elemento **aldelete** en el lenguaje de definición de esquemas conceptuales (CSDL) define el comportamiento que está conectado a una asociación. Si el atributo **Action** se establece en **Cascade** en un extremo de una asociación, los tipos de entidad relacionados en el otro extremo de la asociación se eliminan cuando se elimina el tipo de entidad del primer extremo. Si la asociación entre dos tipos de entidad es una relación entre clave principal y clave principal, se elimina un objeto dependiente cargado cuando se elimina el objeto de entidad de seguridad del otro extremo de la asociación, independientemente de la especificación de la **eliminación** .  

> [!NOTE]
> El elemento **aldelete** solo afecta al comportamiento de tiempo de ejecución de una aplicación; no afecta al comportamiento en el origen de datos. El comportamiento definido en el origen de datos debe ser igual que el definido en la aplicación.

 

Un elemento **aldelete** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **aleliminar** .

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Acción**     | Sí         | **Cascade** o **None**. Si **en cascada**, los tipos de entidad dependientes se eliminarán cuando se elimine el tipo de entidad de entidad de seguridad. Si no hay **ninguno**, los tipos de entidad dependientes no se eliminarán cuando se elimine el tipo de entidad de entidad de seguridad. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Association** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **Association** que define la Asociación **CustomerOrders** . El elemento **aleliminar** indica que todos los **pedidos** relacionados con un **cliente** determinado y que se han cargado en el ObjectContext se eliminarán cuando se elimine el **cliente** .

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a>Parameter (Elemento) (CSDL)

El elemento **Parameter** en el lenguaje de definición de esquemas conceptuales (CSDL) puede ser un elemento secundario del elemento FunctionImport o del elemento function.

### <a name="functionimport-element-application"></a>Aplicación para el elemento FunctionImport

Un elemento **Parameter** (como elemento secundario del elemento **FunctionImport** ) se usa para definir los parámetros de entrada y salida para las importaciones de funciones que se declaran en CSDL.

El elemento **Parameter** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Elementos Annotation (cero o más elementos)

#### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Parameter** .

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**       | Sí         | El nombre del parámetro.                                                                                                                                                                                                      |
| **Tipo**       | Sí         | El tipo de parámetro. El valor tiene que ser un tipo **EDMSimpleType** o un tipo complejo que se encuentre dentro del ámbito del modelo.                                                                                                             |
| **Modo**       | No          | **In**, **out**o **INOUT** dependiendo de si el parámetro es un parámetro de entrada, de salida o de entrada/salida.                                                                                                                |
| **MaxLength**  | No          | La longitud máxima permitida del parámetro.                                                                                                                                                                                    |
| **Precisión**  | No          | La precisión del parámetro.                                                                                                                                                                                                 |
| **Escala**      | No          | La escala del parámetro.                                                                                                                                                                                                     |
| **SRID**       | No          | Identificador de referencia del sistema espacial. Válido solo para los parámetros de los tipos espaciales. Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Parameter** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

#### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **FunctionImport** con un elemento secundario **Parameter** . La función acepta un parámetro de entrada y devuelve una colección de tipos de entidad.

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a>Aplicación para el elemento Function

Un elemento **Parameter** (como elemento secundario del elemento **function** ) define los parámetros de las funciones que se definen o declaran en un modelo conceptual.

El elemento **Parameter** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   CollectionType (cero o un elemento)
-   ReferenceType (cero o un elemento)
-   RowType (cero o un elemento)

> [!NOTE]
> Solo uno de los elementos **CollectionType**, **referenceType**o **RowType** puede ser un elemento secundario de un elemento **Property** .

 

-   Elementos Annotation (cero o más elementos)

> [!NOTE]
> Los elementos de anotación deben aparecer después de todos los demás elementos secundarios. Los elementos Annotation solo se permiten en CSDL V2 y versiones posteriores.

 

#### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Parameter** .

| Nombre del atributo   | Es obligatorio | Value                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**         | Sí         | El nombre del parámetro.                                                                                                                                                                                                      |
| **Tipo**         | No          | El tipo de parámetro. Un parámetro puede ser de cualquiera de los siguientes tipos (o colecciones de estos tipos): <br/> **EdmSimpleType** <br/> tipo de entidad <br/> tipo complejo <br/> tipo de fila <br/> tipo de referencia                             |
| **Admisión de valores NULL**     | No          | **True** (valor predeterminado) o **false** , dependiendo de si la propiedad puede tener un valor **null** .                                                                                                                          |
| **DefaultValue** | No          | Valor predeterminado de la propiedad.                                                                                                                                                                                              |
| **MaxLength**    | No          | La longitud máxima del valor de la propiedad.                                                                                                                                                                                       |
| **FixedLength**  | No          | **True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena de longitud fija.                                                                                                                          |
| **Precisión**    | No          | Precisión del valor de propiedad.                                                                                                                                                                                            |
| **Escala**        | No          | Escala del valor de propiedad.                                                                                                                                                                                                |
| **SRID**         | No          | Identificador de referencia del sistema espacial. Solo es válido para las propiedades de los tipos espaciales. Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**      | No          | **True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena Unicode.                                                                                                                               |
| **Intercalación**    | No          | Cadena que especifica la secuencia de intercalación que se usará en el origen de datos.                                                                                                                                                   |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Parameter** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

#### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento de **función** que usa un elemento secundario **Parameter** para definir un parámetro de función.

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a>Principal (Elemento) (CSDL)

El elemento **principal** del lenguaje de definición de esquemas conceptuales (CSDL) es un elemento secundario del elemento ReferentialConstraint que define el extremo principal de una restricción referencial. Un elemento **ReferentialConstraint** define una funcionalidad similar a una restricción de integridad referencial en una base de datos relacional. Del mismo modo que una columna (o columnas) de una tabla de base de datos puede hacer referencia a la clave principal de otra tabla, una propiedad (o propiedades) de un tipo de entidad puede hacer referencia a la clave de entidad de otro tipo de entidad. El tipo de entidad al que se hace referencia se denomina *extremo principal* de la restricción. El tipo de entidad que hace referencia al extremo principal se denomina *extremo dependiente* de la restricción. Los elementos **PropertyRef** se utilizan para especificar las claves a las que hace referencia el extremo dependiente.

El elemento **principal** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   PropertyRef (uno o varios elementos)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **principal** .

| Nombre del atributo | Es obligatorio | Value                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| **Rol**       | Sí         | Nombre del tipo de entidad del extremo principal de la asociación. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **principal** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **ReferentialConstraint** que forma parte de la definición de la Asociación **PublishedBy** . La propiedad **ID** del tipo de entidad **Publisher** constituye el extremo principal de la restricción referencial.

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="property-element-csdl"></a>Property (Elemento) (CSDL)

El elemento **Property** del lenguaje de definición de esquemas conceptuales (CSDL) puede ser un elemento secundario del elemento EntityType, el elemento complexType o el elemento RowType.

### <a name="entitytype-and-complextype-element-applications"></a>Aplicaciones de elemento EntityType y ComplexType

Los elementos de **propiedad** (como elementos secundarios de los elementos **EntityType** o **complexType** ) definen la forma y las características de los datos que contendrá una instancia de tipo de entidad o una instancia de tipo complejo. Las propiedades en un modelo conceptual son análogas a las propiedades que se definen en una clase. Del mismo modo que las propiedades en una clase definen la forma de la clase y proporcionan información sobre los objetos, las propiedades en un modelo conceptual definen la forma de un tipo de entidad y proporcionan información sobre las instancias del tipo de entidad.

El elemento **Property** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Elemento Documentation (cero o más elementos)
-   Elementos Annotation (cero o más elementos)

Las siguientes caras se pueden aplicar a un elemento **Property** : **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **collation**, **ConcurrencyMode**. Las facetas son atributos XML que proporcionan información sobre cómo los valores de propiedad se almacenan en el almacén de datos.

> [!NOTE]
> Las caras solo se pueden aplicar a propiedades de tipo **EDMSimpleType**.

 

#### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Property** .

| Nombre del atributo                                                         | Es obligatorio | Value                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**                                                               | Sí         | El nombre de la propiedad.                                                                                                                                                                                                       |
| **Tipo**                                                               | Sí         | El tipo de valor de la propiedad. El tipo de valor de la propiedad debe ser un tipo **EDMSimpleType** o un tipo complejo (indicado mediante un nombre completo) que se encuentre dentro del ámbito del modelo.                                                 |
| **Admisión de valores NULL**                                                           | No          | **True** (el valor predeterminado) o <strong>False</strong>, según si la propiedad puede tener un valor nulo. <br/> [!NOTE]                                                                                                   |
| > en el CSDL v1, una propiedad de tipo complejo debe tener `Nullable="False"` . |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                       | No          | Valor predeterminado de la propiedad.                                                                                                                                                                                              |
| **MaxLength**                                                          | No          | La longitud máxima del valor de la propiedad.                                                                                                                                                                                       |
| **FixedLength**                                                        | No          | **True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena de longitud fija.                                                                                                                          |
| **Precisión**                                                          | No          | Precisión del valor de propiedad.                                                                                                                                                                                            |
| **Escala**                                                              | No          | Escala del valor de propiedad.                                                                                                                                                                                                |
| **SRID**                                                               | No          | Identificador de referencia del sistema espacial. Solo es válido para las propiedades de los tipos espaciales. Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**                                                            | No          | **True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena Unicode.                                                                                                                               |
| **Intercalación**                                                          | No          | Cadena que especifica la secuencia de intercalación que se usará en el origen de datos.                                                                                                                                                   |
| **ConcurrencyMode**                                                    | No          | **None** (el valor predeterminado) o **Fixed**. Si el valor se establece en **Fixed**, el valor de la propiedad se usará en las comprobaciones de simultaneidad optimista.                                                                                  |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento de **propiedad** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

#### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityType** con tres elementos **Property** :

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

En el ejemplo siguiente se muestra un elemento **complexType** con cinco elementos **Property** :

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a>Aplicación de elemento RowType

Los elementos de **propiedad** (como elementos secundarios de un elemento **RowType** ) definen la forma y las características de los datos que se pueden pasar o devolver desde una función definida por el modelo.  

El elemento **Property** puede tener exactamente uno de los siguientes elementos secundarios:

-   CollectionType
-   ReferenceType
-   RowType

El elemento **Property** puede tener cualquier número de elementos Annotation secundarios.

> [!NOTE]
> Los elementos Annotation solo se permiten en CSDL V2 y versiones posteriores.

 

#### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Property** .

| Nombre del atributo                                                     | Es obligatorio | Value                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**                                                           | Sí         | El nombre de la propiedad.                                                                                                                                                                                                       |
| **Tipo**                                                           | Sí         | El tipo de valor de la propiedad.                                                                                                                                                                                                 |
| **Admisión de valores NULL**                                                       | No          | **True** (el valor predeterminado) o **False**, según si la propiedad puede tener un valor nulo. <br/> [!NOTE]                                                                                                                |
| > en CSDL v1, una propiedad de tipo complejo debe tener `Nullable="False"` . |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | No          | Valor predeterminado de la propiedad.                                                                                                                                                                                              |
| **MaxLength**                                                      | No          | La longitud máxima del valor de la propiedad.                                                                                                                                                                                       |
| **FixedLength**                                                    | No          | **True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena de longitud fija.                                                                                                                          |
| **Precisión**                                                      | No          | Precisión del valor de propiedad.                                                                                                                                                                                            |
| **Escala**                                                          | No          | Escala del valor de propiedad.                                                                                                                                                                                                |
| **SRID**                                                           | No          | Identificador de referencia del sistema espacial. Solo es válido para las propiedades de los tipos espaciales. Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**                                                        | No          | **True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena Unicode.                                                                                                                               |
| **Intercalación**                                                      | No          | Cadena que especifica la secuencia de intercalación que se usará en el origen de datos.                                                                                                                                                   |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento de **propiedad** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

#### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestran los elementos de **propiedad** que se utilizan para definir la forma del tipo de valor devuelto de una función definida por el modelo.

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```
 

 

## <a name="propertyref-element-csdl"></a>PropertyRef (Elemento) (CSDL)

El elemento **PropertyRef** en el lenguaje de definición de esquemas conceptuales (CSDL) hace referencia a una propiedad de un tipo de entidad para indicar que la propiedad realizará uno de los roles siguientes:

-   Parte de la clave de la entidad (una propiedad o un conjunto de propiedades de un tipo de entidad que determinan la identidad). Se pueden usar uno o varios elementos **PropertyRef** para definir una clave de entidad.
-   El extremo dependiente o principal de una restricción referencial.

El elemento **PropertyRef** solo puede tener elementos Annotation (cero o más) como elementos secundarios.

> [!NOTE]
> Los elementos Annotation solo se permiten en CSDL V2 y versiones posteriores.

 

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **PropertyRef** .

| Nombre del atributo | Es obligatorio | Value                                |
|:---------------|:------------|:-------------------------------------|
| **Nombre**       | Sí         | Nombre de la propiedad a la que se hace referencia. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **PropertyRef** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se define un tipo de entidad (**book**). La clave de entidad se define haciendo referencia a la propiedad **ISBN** del tipo de entidad.

``` xml
 <EntityType Name="Book">
   <Key>
     <PropertyRef Name="ISBN" />
   </Key>
   <Property Type="String" Name="ISBN" Nullable="false" />
   <Property Type="String" Name="Title" Nullable="false" />
   <Property Type="Decimal" Name="Revision" Nullable="false" Precision="29" Scale="29" />
   <NavigationProperty Name="Publisher" Relationship="BooksModel.PublishedBy"
                       FromRole="Book" ToRole="Publisher" />
   <NavigationProperty Name="Authors" Relationship="BooksModel.WrittenBy"
                       FromRole="Book" ToRole="Author" />
 </EntityType>
```
 

En el ejemplo siguiente, se usan dos elementos **PropertyRef** para indicar que dos propiedades (**ID** y **PublisherId**) son los extremos principal y dependiente de una restricción referencial.

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="referencetype-element-csdl"></a>ReferenceType (Elemento) (CSDL)

El elemento **referenceType** en el lenguaje de definición de esquemas conceptuales (CSDL) especifica una referencia a un tipo de entidad. El elemento **referenceType** puede ser un elemento secundario de los siguientes elementos:

-   ReturnType (función)
-   Parámetro
-   CollectionType

El elemento **referenceType** se utiliza al definir un parámetro o un tipo de valor devuelto para una función.

Un elemento **referenceType** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **referenceType** .

| Nombre del atributo | Es obligatorio | Value                                         |
|:---------------|:------------|:----------------------------------------------|
| **Tipo**       | Sí         | Nombre del tipo de entidad al que se hace referencia. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **referenceType** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra el elemento **referenceType** que se usa como elemento secundario de un elemento **Parameter** en una función definida por el modelo que acepta una referencia a un tipo de entidad **Person** :

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
   <Parameter Name="instructor">
     <ReferenceType Type="SchoolModel.Person" />
   </Parameter>
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

En el ejemplo siguiente se muestra el elemento **referenceType** usado como elemento secundario de un elemento **ReturnType** (function) en una función definida por el modelo que devuelve una referencia a un tipo de entidad **Person** :

``` xml
 <Function Name="GetPersonReference">
     <Parameter Name="p" Type="SchoolModel.Person" />
     <ReturnType>
         <ReferenceType Type="SchoolModel.Person" />
     </ReturnType>
     <DefiningExpression>
           REF(p)
     </DefiningExpression>
 </Function>
```
 

 

## <a name="referentialconstraint-element-csdl"></a>ReferentialConstraint (Elemento) (CSDL)

Un elemento **ReferentialConstraint** en el lenguaje de definición de esquemas conceptuales (CSDL) define una funcionalidad similar a una restricción de integridad referencial en una base de datos relacional. Del mismo modo que una columna (o columnas) de una tabla de base de datos puede hacer referencia a la clave principal de otra tabla, una propiedad (o propiedades) de un tipo de entidad puede hacer referencia a la clave de entidad de otro tipo de entidad. El tipo de entidad al que se hace referencia se denomina *extremo principal* de la restricción. El tipo de entidad que hace referencia al extremo principal se denomina *extremo dependiente* de la restricción.

Si una clave externa que se expone en un tipo de entidad hace referencia a una propiedad en otro tipo de entidad, el elemento **ReferentialConstraint** define una asociación entre los dos tipos de entidad. Dado que el elemento **ReferentialConstraint** proporciona información sobre cómo se relacionan dos tipos de entidad, no es necesario ningún elemento AssociationSetMapping correspondiente en el lenguaje de especificación de asignaciones (MSL). Una asociación entre dos tipos de entidad que no tienen claves externas expuestas debe tener un elemento **AssociationSetMapping** correspondiente para asignar información de asociación al origen de datos.

Si una clave externa no se expone en un tipo de entidad, el elemento **ReferentialConstraint** solo puede definir una restricción PRIMARY KEY-PRIMARY KEY entre el tipo de entidad y otro tipo de entidad.

Un elemento **ReferentialConstraint** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Principal (exactamente un elemento)
-   Dependent (exactamente un elemento)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

El elemento **ReferentialConstraint** puede tener cualquier número de atributos de anotación (atributos XML personalizados). Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **ReferentialConstraint** que se usa como parte de la definición de la Asociación **PublishedBy** .

``` xml
 <Association Name="PublishedBy">
   <End Type="BooksModel.Book" Role="Book" Multiplicity="*" >
   </End>
   <End Type="BooksModel.Publisher" Role="Publisher" Multiplicity="1" />
   <ReferentialConstraint>
     <Principal Role="Publisher">
       <PropertyRef Name="Id" />
     </Principal>
     <Dependent Role="Book">
       <PropertyRef Name="PublisherId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```
 

 

## <a name="returntype-function-element-csdl"></a>ReturnType (function) (elemento) (CSDL)

El elemento **ReturnType** (function) en el lenguaje de definición de esquemas conceptuales (CSDL) especifica el tipo de valor devuelto para una función que se define en un elemento de función. Un tipo de valor devuelto de función también se puede especificar con un atributo **ReturnType** .

Los tipos de valor devuelto pueden ser cualquier **EdmSimpleType**, tipo de entidad, tipo complejo, tipo de fila, tipo de referencia o una colección de uno de estos tipos.

El tipo de valor devuelto de una función se puede especificar con el atributo **Type** del elemento **ReturnType** (function) o con uno de los siguientes elementos secundarios:

-   CollectionType
-   ReferenceType
-   RowType

> [!NOTE]
> Un modelo no se validará si especifica un tipo de valor devuelto de función con el atributo **Type** del elemento **ReturnType** (function) y uno de los elementos secundarios.

 

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **ReturnType** (function).

| Nombre del atributo | Es obligatorio | Value                              |
|:---------------|:------------|:-----------------------------------|
| **ReturnType** | No          | El tipo devuelto por la función. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **ReturnType** (function). Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se usa un elemento **function** para definir una función que devuelve el número de años que un libro se ha impreso. Tenga en cuenta que el tipo de valor devuelto se especifica mediante el atributo **Type** de un elemento **ReturnType** (function).

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a>ReturnType (FunctionImport) (elemento) (CSDL)

El elemento **ReturnType** (FunctionImport) en el lenguaje de definición de esquemas conceptuales (CSDL) especifica el tipo de valor devuelto para una función que se define en un elemento FunctionImport. Un tipo de valor devuelto de función también se puede especificar con un atributo **ReturnType** .

Los tipos devueltos pueden ser cualquier colección de tipo de entidad, tipo complejo o **EdmSimpleType**.

El tipo de valor devuelto de una función se especifica con el atributo **Type** del elemento **ReturnType** (FunctionImport).

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **ReturnType** (FunctionImport).

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Tipo**       | No          | Tipo devuelto por la función. El valor debe ser una colección de ComplexType, EntityType o EDMSimpleType.                                                                                      |
| **EntitySet**  | No          | Si la función devuelve una colección de tipos de entidad, el valor de **EntitySet** debe ser el conjunto de entidades al que pertenece la colección. De lo contrario, el atributo **EntitySet** no debe usarse. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **ReturnType** (FunctionImport). Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se usa una **FunctionImport** que devuelve libros y publicadores. Tenga en cuenta que la función devuelve dos conjuntos de resultados y, por tanto, se especifican dos elementos **ReturnType** (FunctionImport).

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a>RowType (Elemento) (CSDL)

Un elemento **RowType** en el lenguaje de definición de esquemas conceptuales (CSDL) define una estructura sin nombre como un parámetro o tipo de valor devuelto para una función definida en el modelo conceptual.

Un elemento **RowType** puede ser el elemento secundario de los siguientes elementos:

-   CollectionType
-   Parámetro
-   ReturnType (función)

Un elemento **RowType** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Property (uno o varios)
-   Elementos Annotation (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **RowType** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra una función definida por el modelo que utiliza un elemento **CollectionType** para especificar que la función devuelve una colección de filas (tal y como se especifica en el elemento **RowType** ).

``` xml
 <Function Name="LastNamesAfter">
   <Parameter Name="someString" Type="Edm.String" />
   <ReturnType>
    <CollectionType>
      <RowType>
        <Property Name="FirstName" Type="Edm.String" Nullable="false" />
        <Property Name="LastName" Type="Edm.String" Nullable="false" />
      </RowType>
    </CollectionType>
   </ReturnType>
   <DefiningExpression>
             SELECT VALUE ROW(p.FirstName, p.LastName)
             FROM SchoolEntities.People AS p
             WHERE p.LastName &gt;= somestring
   </DefiningExpression>
 </Function>
```

## <a name="schema-element-csdl"></a>Schema (Elemento) (CSDL)

El elemento **Schema** es el elemento raíz de una definición de modelo conceptual. Contiene las definiciones para los objetos, las funciones y los contenedores que conforman un modelo conceptual.

El elemento **Schema** puede contener cero o más de los siguientes elementos secundarios:

-   Uso
-   EntityContainer
-   EntityType
-   EnumType
-   Asociación
-   ComplexType
-   Función

Un elemento **Schema** puede contener cero o un elemento Annotation.

> [!NOTE]
> El elemento de **función** y los elementos de anotación solo se permiten en CSDL V2 y versiones posteriores.

 

El elemento **Schema** usa el atributo **namespace** para definir el espacio de nombres para el tipo de entidad, el tipo complejo y los objetos de Asociación de un modelo conceptual. Dentro de un espacio de nombres, no puede haber dos objetos con el mismo nombre. Los espacios de nombres pueden abarcar varios elementos de **esquema** y varios archivos. CSDL.

Un espacio de nombres del modelo conceptual es diferente del espacio de nombres XML del elemento **Schema** . Un espacio de nombres del modelo conceptual (tal y como se define en el atributo de **espacio de nombres** ) es un contenedor lógico para tipos de entidad, tipos complejos y tipos de asociación. El espacio de nombres XML (indicado por el atributo **xmlns** ) de un elemento **Schema** es el espacio de nombres predeterminado para los elementos secundarios y los atributos del elemento **Schema** . Los espacios de nombres XML del formulario https://schemas.microsoft.com/ado/YYYY/MM/edm (donde YYYY y mm representan un año y un mes respectivamente) se reservan para CSDL. No puede haber elementos y atributos personalizados en espacios de nombres que tengan este formato.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Schema** .

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Espacio de nombres**  | Sí         | El espacio de nombres del modelo conceptual. El valor del atributo **namespace** se usa para formar el nombre completo de un tipo. Por ejemplo, si un **EntityType** denominado *Customer* está en el espacio de nombres simple. example. Model, el nombre completo del **EntityType** es SimpleExampleModel. Customer. <br/> Las siguientes cadenas no se pueden usar como el valor del atributo **namespace** : **System**, **Transient**o **EDM**. El valor del atributo **namespace** no puede ser el mismo que el valor del atributo **namespace** del elemento Schema de SSDL. |
| **Alias**      | No          | Un identificador usado en lugar del nombre del espacio de nombres. Por ejemplo, si un **EntityType** denominado *Customer* está en el espacio de nombres simple. example. Model y el valor del atributo **alias** es *Model*, puede usar Model. Customer como nombre completo del **EntityType.**                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Schema** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **Schema** que contiene un elemento **EntityContainer** , dos elementos **EntityType** y un elemento **Association** .

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
      xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
      xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
       Namespace="ExampleModel" Alias="Self">
         <EntityContainer Name="ExampleModelContainer">
           <EntitySet Name="Customers"
                      EntityType="ExampleModel.Customer" />
           <EntitySet Name="Orders" EntityType="ExampleModel.Order" />
           <AssociationSet
                       Name="CustomerOrder"
                       Association="ExampleModel.CustomerOrders">
             <End Role="Customer" EntitySet="Customers" />
             <End Role="Order" EntitySet="Orders" />
           </AssociationSet>
         </EntityContainer>
         <EntityType Name="Customer">
           <Key>
             <PropertyRef Name="CustomerId" />
           </Key>
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
           <Property Type="String" Name="Name" Nullable="false" />
           <NavigationProperty
                    Name="Orders"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Customer" ToRole="Order" />
         </EntityType>
         <EntityType Name="Order">
           <Key>
             <PropertyRef Name="OrderId" />
           </Key>
           <Property Type="Int32" Name="OrderId" Nullable="false" />
           <Property Type="Int32" Name="ProductId" Nullable="false" />
           <Property Type="Int32" Name="Quantity" Nullable="false" />
           <NavigationProperty
                    Name="Customer"
                    Relationship="ExampleModel.CustomerOrders"
                    FromRole="Order" ToRole="Customer" />
           <Property Type="Int32" Name="CustomerId" Nullable="false" />
         </EntityType>
         <Association Name="CustomerOrders">
           <End Type="ExampleModel.Customer"
                Role="Customer" Multiplicity="1" />
           <End Type="ExampleModel.Order"
                Role="Order" Multiplicity="*" />
           <ReferentialConstraint>
             <Principal Role="Customer">
               <PropertyRef Name="CustomerId" />
             </Principal>
             <Dependent Role="Order">
               <PropertyRef Name="CustomerId" />
             </Dependent>
           </ReferentialConstraint>
         </Association>
       </Schema>
```
 

 

## <a name="typeref-element-csdl"></a>TypeRef (Elemento) (CSDL)

El elemento **TypeRef** en el lenguaje de definición de esquemas conceptuales (CSDL) proporciona una referencia a un tipo con nombre existente. El elemento **TypeRef** puede ser un elemento secundario del elemento CollectionType, que se usa para especificar que una función tiene una colección como parámetro o tipo de valor devuelto.

Un elemento **TypeRef** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **TypeRef** . Tenga en cuenta que los atributos **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**y **collation** solo se aplican a **EDMSimpleTypes**.

| Nombre del atributo                                                     | Es obligatorio | Value                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Tipo**                                                           | No          | El nombre del tipo al que se hace referencia.                                                                                                                                                                                          |
| **Admisión de valores NULL**                                                       | No          | **True** (el valor predeterminado) o **False**, según si la propiedad puede tener un valor nulo. <br/> [!NOTE]                                                                                                                |
| > en CSDL v1, una propiedad de tipo complejo debe tener `Nullable="False"` . |             |                                                                                                                                                                                                                                 |
| **DefaultValue**                                                   | No          | Valor predeterminado de la propiedad.                                                                                                                                                                                              |
| **MaxLength**                                                      | No          | La longitud máxima del valor de la propiedad.                                                                                                                                                                                       |
| **FixedLength**                                                    | No          | **True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena de longitud fija.                                                                                                                          |
| **Precisión**                                                      | No          | Precisión del valor de propiedad.                                                                                                                                                                                            |
| **Escala**                                                          | No          | Escala del valor de propiedad.                                                                                                                                                                                                |
| **SRID**                                                           | No          | Identificador de referencia del sistema espacial. Solo es válido para las propiedades de los tipos espaciales. Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **Unicode**                                                        | No          | **True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena Unicode.                                                                                                                               |
| **Intercalación**                                                      | No          | Cadena que especifica la secuencia de intercalación que se usará en el origen de datos.                                                                                                                                                   |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **CollectionType** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra una función definida por el modelo que usa el elemento **TypeRef** (como elemento secundario de un elemento **CollectionType** ) para especificar que la función acepta una colección de tipos de entidad **Department** .

``` xml
 <Function Name="GetAvgBudget">
      <Parameter Name="Departments">
          <CollectionType>
             <TypeRef Type="SchoolModel.Department"/>
          </CollectionType>
           </Parameter>
       <ReturnType Type="Collection(Edm.Decimal)"/>
       <DefiningExpression>
             SELECT VALUE AVG(d.Budget) FROM Departments AS d
       </DefiningExpression>
 </Function>
```
 

 

## <a name="using-element-csdl"></a>Using (Elemento) (CSDL)

El elemento **using** del lenguaje de definición de esquemas conceptuales (CSDL) importa el contenido de un modelo conceptual que existe en un espacio de nombres diferente. Al establecer el valor del atributo de **espacio de nombres** , puede hacer referencia a los tipos de entidad, tipos complejos y tipos de asociación que se definen en otro modelo conceptual. Más de un elemento **using** puede ser un elemento secundario de un elemento **Schema** .

> [!NOTE]
> El elemento **using** en CSDL no funciona exactamente igual que una instrucción **using** en un lenguaje de programación. Al importar un espacio de nombres con una instrucción **using** en un lenguaje de programación, no afecta a los objetos del espacio de nombres original. En CSDL, un espacio de nombres importado puede contener un tipo de entidad derivado de un tipo de entidad del espacio de nombres original. Esto puede afectar a los conjuntos de entidades declarados en el espacio de nombres original.

 

El elemento **using** puede tener los siguientes elementos secundarios:

-   Documentation (cero o un elemento)
-   Elementos Annotation (cero o más elementos)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **using** .

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Espacio de nombres**  | Sí         | Nombre del espacio de nombres importado.                                                                                                                                                |
| **Alias**      | Sí         | Un identificador usado en lugar del nombre del espacio de nombres. Aunque este atributo es obligatorio, no es necesario usarlo en lugar del nombre del espacio de nombres para calificar los nombres de los objetos. |

 

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **using** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

 

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra el elemento **using** que se usa para importar un espacio de nombres que se define en otro lugar. Tenga en cuenta que el espacio de nombres para el elemento de **esquema** mostrado es `BooksModel` . La `Address` propiedad del `Publisher` **EntityType** es un tipo complejo que se define en el `ExtendedBooksModel` espacio de nombres (importado con el elemento **using** ).

``` xml
 <Schema xmlns="https://schemas.microsoft.com/ado/2009/11/edm"
           xmlns:cg="https://schemas.microsoft.com/ado/2009/11/codegeneration"
           xmlns:store="https://schemas.microsoft.com/ado/2009/11/edm/EntityStoreSchemaGenerator"
           Namespace="BooksModel" Alias="Self">

     <Using Namespace="BooksModel.Extended" Alias="BMExt" />

 <EntityContainer Name="BooksContainer" >
       <EntitySet Name="Publishers" EntityType="BooksModel.Publisher" />
     </EntityContainer>

 <EntityType Name="Publisher">
       <Key>
         <PropertyRef Name="Id" />
       </Key>
       <Property Type="Int32" Name="Id" Nullable="false" />
       <Property Type="String" Name="Name" Nullable="false" />
       <Property Type="BMExt.Address" Name="Address" Nullable="false" />
     </EntityType>

 </Schema>
```
 

 

## <a name="annotation-attributes-csdl"></a>Atributos de anotación (CSDL)

En el lenguaje de definición de esquemas conceptuales (CSDL), los atributos de anotación son atributos XML personalizados del modelo conceptual. Además de tener una estructura XML válida, los atributos de anotación deben cumplir las condiciones siguientes:

-   Los atributos de anotación no deben estar en ningún espacio de nombres XML reservado para CSDL.
-   Se pueden aplicar varios atributos de anotación a un elemento CSDL determinado.
-   Dos atributos de anotación cualesquiera no pueden tener el mismo nombre completo.

Los atributos de anotación se pueden usar para proporcionar metadatos adicionales sobre los elementos en un modelo conceptual. Se puede tener acceso a los metadatos contenidos en los elementos Annotation en tiempo de ejecución mediante el uso de clases en el espacio de nombres System. Data. Metadata. Edm.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityType** con un atributo Annotation (**CustomAttribute**). El ejemplo también muestra un elemento Annotation aplicado al elemento de tipo de entidad.

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

El siguiente código recupera los metadatos del atributo Annotation y los escribe en la consola:

``` xml
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomAttribute"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomAttribute"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

El código anterior supone que el archivo `School.csdl` está en el directorio de resultados del proyecto y que se han agregado las siguientes instrucciones `Imports` y `Using` al proyecto:

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a>Elementos Annotation (CSDL)

Los elementos Annotation en el lenguaje de definición de esquemas conceptuales (CSDL) son los elementos XML personalizados del modelo conceptual. Además de tener una estructura XML válida, lo siguiente debe ser verdadero para los elementos Annotation:

-   Los elementos Annotation no deben estar en un espacio de nombres XML que esté reservado para CSDL.
-   Más de un elemento Annotation puede ser un elemento secundario de un elemento CSDL determinado.
-   Los nombres completos de dos elementos Annotation cualesquiera no deben ser los mismos.
-   Los elementos Annotation deben aparecer después de todos los demás elementos secundarios de un elemento CSDL determinado.

Los elementos Annotation pueden utilizarse para proporcionar metadatos adicionales sobre los elementos en un modelo conceptual. A partir de la .NET Framework versión 4, se puede tener acceso a los metadatos contenidos en los elementos Annotation en tiempo de ejecución mediante el uso de clases en el espacio de nombres System. Data. Metadata. Edm.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityType** con un elemento Annotation (**CustomElement**). El ejemplo también muestra un atributo de anotación aplicado al elemento de tipo de entidad.

``` xml
 <Schema Namespace="SchoolModel" Alias="Self"
         xmlns:annotation="https://schemas.microsoft.com/ado/2009/02/edm/annotation"
         xmlns="https://schemas.microsoft.com/ado/2009/11/edm">
   <EntityContainer Name="SchoolEntities" annotation:LazyLoadingEnabled="true">
     <EntitySet Name="People" EntityType="SchoolModel.Person" />
   </EntityContainer>
   <EntityType Name="Person" xmlns:p="http://CustomNamespace.com"
               p:CustomAttribute="Data here.">
     <Key>
       <PropertyRef Name="PersonID" />
     </Key>
     <Property Name="PersonID" Type="Int32" Nullable="false"
               annotation:StoreGeneratedPattern="Identity" />
     <Property Name="LastName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="FirstName" Type="String" Nullable="false"
               MaxLength="50" Unicode="true" FixedLength="false" />
     <Property Name="HireDate" Type="DateTime" />
     <Property Name="EnrollmentDate" Type="DateTime" />
     <p:CustomElement>
       Custom metadata.
     </p:CustomElement>
   </EntityType>
 </Schema>
```
 

El siguiente código recupera los metadatos del elemento de anotación y los escribe en la consola:

``` csharp
 EdmItemCollection collection = new EdmItemCollection("School.csdl");
 MetadataWorkspace workspace = new MetadataWorkspace();
 workspace.RegisterItemCollection(collection);
 EdmType contentType;
 workspace.TryGetType("Person", "SchoolModel", DataSpace.CSpace, out contentType);
 if (contentType.MetadataProperties.Contains("http://CustomNamespace.com:CustomElement"))
 {
     MetadataProperty annotationProperty =
         contentType.MetadataProperties["http://CustomNamespace.com:CustomElement"];
     object annotationValue = annotationProperty.Value;
     Console.WriteLine(annotationValue.ToString());
 }
```
 

El código anterior supone que el archivo School.csdl está en el directorio de resultados del proyecto y que se han agregado las siguientes instrucciones `Imports` y `Using` al proyecto:

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a>Tipos de modelos conceptuales (CSDL)

El lenguaje de definición de esquemas conceptuales (CSDL) admite un conjunto de tipos de datos primitivos abstractos, denominados **EDMSimpleTypes**, que definen propiedades en un modelo conceptual. Los **EDMSimpleTypes** son proxies para los tipos de datos primitivos que se admiten en el entorno de almacenamiento o de hospedaje.

En la tabla siguiente se enumeran los tipos de datos primitivos admitidos por CSDL. En la tabla también se enumeran las caras que se pueden aplicar a cada **EDMSimpleType**.

| EDMSimpleType                    | Descripción                                                | Facetas aplicables                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| **Edm.Binary**                   | Contiene datos binarios.                                      | MaxLength, FixedLength, Nullable, Default                                |
| **Edm.Boolean**                  | Contiene el valor **true** o **false**.                  | Nullable, Default                                                        |
| **Edm.Byte**                     | Contiene un valor entero de 8 bits sin signo.                  | Precision, Nullable, Default                                             |
| **Edm.DateTime**                 | Representa una fecha y hora.                                | Precision, Nullable, Default                                             |
| **Edm.DateTimeOffset**           | Contiene una fecha y hora como un desplazamiento en minutos con respecto a GMT. | Precision, Nullable, Default                                             |
| **Edm.Decimal**                  | Contiene un valor numérico con una precisión y escala fijas.   | Precision, Nullable, Default                                             |
| **Edm.Double**                   | Contiene un número de punto flotante con una precisión de 15 dígitos   | Precision, Nullable, Default                                             |
| **EDM. Float**                    | Contiene un número de punto flotante con una precisión de 7 dígitos.   | Precision, Nullable, Default                                             |
| **Edm.Guid**                     | Contiene un identificador único de 16 bytes.                      | Precision, Nullable, Default                                             |
| **Edm.Int16**                    | Contiene un valor entero de 16 bits con signo.                    | Precision, Nullable, Default                                             |
| **Edm.Int32**                    | Contiene un valor entero de 32 bits con signo.                    | Precision, Nullable, Default                                             |
| **Edm.Int64**                    | Contiene un valor entero de 64 bits con signo.                    | Precision, Nullable, Default                                             |
| **Edm.SByte**                    | Contiene un valor entero de 8 bits con signo.                     | Precision, Nullable, Default                                             |
| **Edm.String**                   | Contiene datos de caracteres.                                   | Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default |
| **Edm.Time**                     | Contiene una hora del día.                                    | Precision, Nullable, Default                                             |
| **EDM. Geography**                |                                                            | Nullable, default, SRID                                                  |
| **Edm.GeographyPoint**           |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeographyLineString**      |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeographyPolygon**         |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeographyMultiPoint**      |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeographyMultiLineString** |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeographyMultiPolygon**    |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeographyCollection**      |                                                            | Nullable, default, SRID                                                  |
| **EDM. Geometry**                 |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryPoint**            |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryLineString**       |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryPolygon**          |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryMultiPoint**       |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryMultiLineString**  |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryMultiPolygon**     |                                                            | Nullable, default, SRID                                                  |
| **EDM. GeometryCollection**       |                                                            | Nullable, default, SRID                                                  |

## <a name="facets-csdl"></a>Facets (CSDL)

En el lenguaje de definición de esquemas conceptuales (CSDL), las facetas representan restricciones en las propiedades de tipos de entidad y tipos complejos. Las facetas aparecen como atributos XML en los elementos CSDL siguientes:

-   Propiedad
-   TypeRef
-   Parámetro

En la tabla siguiente se describen las facetas que se admiten en CSDL. Todas las facetas son opcionales. El Entity Framework usa algunas de las caras que se indican a continuación al generar una base de datos a partir de un modelo conceptual.

> [!NOTE]
> Para obtener información sobre los tipos de datos de un modelo conceptual, vea tipos de modelos conceptuales (CSDL).

| Faceta               | Descripción                                                                                                                                                                                                                                                   | Se aplica a                                                                                                                                                                                                                                                                                                                                                                           | Se utiliza para la generación de la base de datos | La usa el runtime. |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| **Intercalación**       | Especifica la secuencia de intercalación (o secuencia de orden) que se va a usar cuando se realicen las operaciones de comparación y ordenación sobre los valores de la propiedad.                                                                                                               | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | Sí                              | No                  |
| **ConcurrencyMode** | Indica que el valor de propiedad se debería utilizar para las comprobaciones de la simultaneidad optimista.                                                                                                                                                                    | Todas las propiedades de **EDMSimpleType**                                                                                                                                                                                                                                                                                                                                                     | No                               | Sí                 |
| **Valor predeterminado**         | Especifica el valor predeterminado de la propiedad si no se proporciona ningún valor al crear las instancias.                                                                                                                                                                       | Todas las propiedades de **EDMSimpleType**                                                                                                                                                                                                                                                                                                                                                     | Sí                              | Sí                 |
| **FixedLength**     | Especifica si la longitud del valor de propiedad puede variar.                                                                                                                                                                                                  | **EDM. Binary**, **EDM. String**                                                                                                                                                                                                                                                                                                                                                       | Sí                              | No                  |
| **MaxLength**       | Especifica la longitud máxima del valor de propiedad.                                                                                                                                                                                                           | **EDM. Binary**, **EDM. String**                                                                                                                                                                                                                                                                                                                                                       | Sí                              | No                  |
| **Admisión de valores NULL**        | Especifica si la propiedad puede tener un valor **null** .                                                                                                                                                                                                     | Todas las propiedades de **EDMSimpleType**                                                                                                                                                                                                                                                                                                                                                     | Sí                              | Sí                 |
| **Precisión**       | Para las propiedades de tipo **decimal**, especifica el número de dígitos que puede tener un valor de propiedad. En el caso de las propiedades de tipo **Time**, **DateTime**y **DateTimeOffset**, especifica el número de dígitos para la parte fraccionaria de los segundos del valor de propiedad. | **EDM. DateTime**, **EDM. DateTimeOffset**, **EDM. decimal**, **EDM. Time**                                                                                                                                                                                                                                                                                                              | Sí                              | No                  |
| **Escala**           | Especifica el número de dígitos que puede haber a la derecha del separador decimal para el valor de propiedad.                                                                                                                                                                      | **Edm.Decimal**                                                                                                                                                                                                                                                                                                                                                                      | Sí                              | No                  |
| **SRID**            | Especifica el identificador del sistema espacial de referencia del sistema. Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).                                                              | **EDM. Geography, EDM. GeographyPoint, EDM. GeographyLineString, EDM. GeographyPolygon, EDM. GeographyMultiPoint, EDM. GeographyMultiLineString, EDM. GeographyMultiPolygon, EDM. GeographyCollection, EDM. Geometry, EDM. GeometryPoint, EDM. GeometryLineString, EDM. GeometryPolygon, EDM. GeometryMultiPoint, EDM. GeometryMultiLineString, EDM. GeometryMultiPolygon, EDM. GeometryCollection** | No                               | Sí                 |
| **Unicode**         | Indica si el valor de propiedad está almacenado como Unicode.                                                                                                                                                                                                    | **Edm.String**                                                                                                                                                                                                                                                                                                                                                                       | Sí                              | Sí                 |

>[!NOTE]
> Al generar una base de datos a partir de un modelo conceptual, el Asistente para generar base de datos reconocerá el valor del atributo **StoreGeneratedPattern** en un elemento **Property** si está en el siguiente espacio de nombres: https://schemas.microsoft.com/ado/2009/02/edm/annotation . Los valores admitidos para el atributo son **Identity** y **Computed**. Un valor de **Identity** producirá una columna de base de datos con un valor de identidad que se genera en la base de datos. Un valor de **calculado** producirá una columna con un valor que se calcula en la base de datos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestran facetas aplicadas a las propiedades de un tipo de entidad:

``` xml
 <EntityType Name="Product">
   <Key>
     <PropertyRef Name="ProductId" />
   </Key>
   <Property Type="Int32"
             Name="ProductId" Nullable="false"
             a:StoreGeneratedPattern="Identity"
    xmlns:a="https://schemas.microsoft.com/ado/2009/02/edm/annotation" />
   <Property Type="String"
             Name="ProductName"
             Nullable="false"
             MaxLength="50" />
   <Property Type="String"
             Name="Location"
             Nullable="true"
             MaxLength="25" />
 </EntityType>
```
