---
title: 'Especificación de SSDL: EF6'
description: Especificación de SSDL en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: a4af4b1a-40f4-48cc-b2e0-fa8f5d9d5419
uid: ef6/modeling/designer/advanced/edmx/ssdl-spec
ms.openlocfilehash: ab50579649c2e1b19d113cd127e52be995516e27
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620592"
---
# <a name="ssdl-specification"></a>Especificación SSDL
El lenguaje de definición de esquemas de almacenamiento (SSDL) es un lenguaje basado en XML que describe el modelo de almacenamiento de una aplicación Entity Framework.

En una aplicación Entity Framework, los metadatos del modelo de almacenamiento se cargan desde un archivo. SSDL (escrito en SSDL) en una instancia de System. Data. Metadata. Edm. StoreItemCollection y es accesible mediante métodos en la clase System. Data. Metadata. Edm. MetadataWorkspace. Entity Framework usa los metadatos del modelo de almacenamiento para traducir las consultas en el modelo conceptual para almacenar comandos específicos.

El Entity Framework Designer (EF Designer) almacena la información del modelo de almacenamiento en un archivo. edmx en tiempo de diseño. En tiempo de compilación, Entity Designer usa información en un archivo. edmx para crear el archivo. SSDL que Entity Framework necesita en tiempo de ejecución.

Las versiones de SSDL se diferencian por los espacios de nombres XML.

| Versión de SSDL | Espacio de nombres XML                                     |
|:-------------|:--------------------------------------------------|
| SSDL v1      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| SSDL V2      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| SSDL V3      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a>Association (Elemento) (SSDL)

Un elemento **Association** en el lenguaje de definición de esquemas de almacenamiento (SSDL) especifica las columnas de tabla que participan en una restricción FOREIGN KEY en la base de datos subyacente. Dos elementos End secundarios necesarios especifican las tablas de los extremos de la asociación y la multiplicidad en cada extremo. Un elemento ReferentialConstraint opcional especifica los extremos principal y dependiente de la asociación así como las columnas participantes. Si no hay ningún elemento **ReferentialConstraint** , se debe usar un elemento AssociationSetMapping para especificar las asignaciones de columnas para la asociación.

El elemento **Association** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o uno)
-   End (exactamente dos)
-   ReferentialConstraint (cero o uno)
-   Elementos Annotation (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Association** .

| Nombre del atributo | Es obligatorio | Value                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| **Nombre**       | Sí         | El nombre de la restricción de clave externa correspondiente de la base de datos subyacente. |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Association** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **Association** que utiliza un elemento **ReferentialConstraint** para especificar las columnas que participan en la restricción FOREIGN KEY externa de **FK \_ ** :

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="associationset-element-ssdl"></a>AssociationSet (Elemento) (SSDL)

El elemento **AssociationSet** en el lenguaje de definición de esquemas de almacenamiento (SSDL) representa una restricción de clave externa entre dos tablas en la base de datos subyacente. Las columnas de la tabla que participan en la restricción de clave externa se especifican en un elemento Association. El elemento **Association** que corresponde a un elemento **AssociationSet** determinado se especifica en el atributo **Association** del elemento **AssociationSet** .

Los conjuntos de asociaciones SSDL están asignados a conjuntos de asociaciones CSDL mediante un elemento AssociationSetMapping. Sin embargo, si la Asociación CSDL para un conjunto de asociaciones CSDL determinado se define mediante un elemento ReferentialConstraint, no es necesario ningún elemento **AssociationSetMapping** correspondiente. En este caso, si hay un elemento **AssociationSetMapping** , las asignaciones que define se reemplazarán por el elemento **ReferentialConstraint** .

El elemento **AssociationSet** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o uno)
-   End (cero o dos)
-   Elementos Annotation (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **AssociationSet** .

| Nombre del atributo  | Es obligatorio | Value                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| **Nombre**        | Sí         | El nombre de la restricción de clave externa representada por el conjunto de asociaciones.                          |
| **Asociación** | Sí         | El nombre de la asociación que define las columnas que participan en la restricción de clave externa. |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **AssociationSet** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **AssociationSet** que representa la `FK_CustomerOrders` restricción FOREIGN KEY en la base de datos subyacente:

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a>CollectionType (elemento) (SSDL)

El elemento **CollectionType** del lenguaje de definición de esquemas de almacenamiento (SSDL) especifica que el tipo de valor devuelto de una función es una colección. El elemento **CollectionType** es un elemento secundario del elemento ReturnType. El tipo de colección se especifica mediante el elemento secundario RowType:

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **CollectionType** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra una función que usa un elemento **CollectionType** para especificar que la función devuelve una colección de filas.

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="commandtext-element-ssdl"></a>CommandText (Elemento) (SSDL)

El elemento **CommandText** del lenguaje de definición de esquemas de almacenamiento (SSDL) es un elemento secundario del elemento function que permite definir una instrucción SQL que se ejecuta en la base de datos. El elemento **CommandText** permite agregar funcionalidad similar a un procedimiento almacenado en la base de datos, pero se define el elemento **CommandText** en el modelo de almacenamiento.

El elemento **CommandText** no puede tener elementos secundarios. El cuerpo del elemento **CommandText** debe ser una instrucción SQL válida para la base de datos subyacente.

No hay atributos aplicables al elemento **CommandText** .

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **function** con un elemento **CommandText** secundario. Exponga la función **UpdateProductInOrder** como un método en el ObjectContext importándolos en el modelo conceptual.  

``` xml
 <Function Name="UpdateProductInOrder" IsComposable="false">
   <CommandText>
     UPDATE Orders
     SET ProductId = @productId
     WHERE OrderId = @orderId;
   </CommandText>
   <Parameter Name="productId"
              Mode="In"
              Type="int"/>
   <Parameter Name="orderId"
              Mode="In"
              Type="int"/>
 </Function>
```

## <a name="definingquery-element-ssdl"></a>DefiningQuery (Elemento) (SSDL)

El elemento **DefiningQuery** del lenguaje de definición de esquemas de almacenamiento (SSDL) permite ejecutar una instrucción SQL directamente en la base de datos subyacente. El elemento **DefiningQuery** se utiliza normalmente como una vista de base de datos, pero la vista se define en el modelo de almacenamiento en lugar de en la base de datos. La vista definida en un elemento **DefiningQuery** se puede asignar a un tipo de entidad en el modelo conceptual a través de un elemento EntitySetMapping. Estas asignaciones son de solo lectura.  

La sintaxis de SSDL siguiente muestra la declaración de un **EntitySet** seguido del elemento **DefiningQuery** que contiene una consulta utilizada para recuperar la vista.

``` xml
 <Schema>
     <EntitySet Name="Tables" EntityType="Self.STable">
         <DefiningQuery>
           SELECT  TABLE_CATALOG,
                   'test' as TABLE_SCHEMA,
                   TABLE_NAME
           FROM    INFORMATION_SCHEMA.TABLES
         </DefiningQuery>
     </EntitySet>
 </Schema>
```

Puede usar procedimientos almacenados en el Entity Framework para habilitar escenarios de lectura y escritura en las vistas.Puede usar una vista del origen de datos o una vista de Entity SQL como tabla base para recuperar datos y para el procesamiento de cambios por parte de los procedimientos almacenados.

Puede usar el elemento **DefiningQuery** para tener como destino Microsoft SQL Server Compact 3,5. Aunque SQL Server Compact 3,5 no admite procedimientos almacenados, puede implementar una funcionalidad similar con el elemento **DefiningQuery** . Otro caso donde puede ser útil es en la creación de procedimientos almacenados para resolver una desigualdad entre los tipos de datos utilizados en el lenguaje de programación y los del origen de datos. Podría escribir una **DefiningQuery** que toma un determinado conjunto de parámetros y, a continuación, llama a un procedimiento almacenado con un conjunto diferente de parámetros, por ejemplo, un procedimiento almacenado que elimina datos.

## <a name="dependent-element-ssdl"></a>Dependent (Elemento) (SSDL)

El elemento **dependiente** del lenguaje de definición de esquemas de almacenamiento (SSDL) es un elemento secundario del elemento ReferentialConstraint que define el extremo dependiente de una restricción FOREIGN KEY (también denominada restricción referencial). El elemento **dependiente** especifica la columna (o columnas) de una tabla que hace referencia a una columna de clave principal (o columnas). Los elementos **PropertyRef** especifican a qué columnas se hace referencia. El elemento principal especifica las columnas de clave principal a las que hacen referencia las columnas que se especifican en el elemento **dependiente** .

El elemento **dependiente** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   PropertyRef (uno o varios)
-   Elementos Annotation (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **dependiente** .

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Rol**       | Sí         | El mismo valor que el atributo **role** (si se usa) del elemento end correspondiente; de lo contrario, el nombre de la tabla que contiene la columna de referencia. |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **dependiente** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento Association que utiliza un elemento **ReferentialConstraint** para especificar las columnas que participan en la restricción de clave externa de **FK \_ CustomerOrders** . El elemento **dependiente** especifica la columna **CustomerID** de la tabla **Order** como el extremo dependiente de la restricción.

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="documentation-element-ssdl"></a>Documentation (Elemento) (SSDL)

El elemento **Documentation** del lenguaje de definición de esquemas de almacenamiento (SSDL) se puede usar para proporcionar información sobre un objeto que se define en un elemento primario.

El elemento **Documentation** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   **Summary**: breve descripción del elemento primario. (cero o un elemento).
-   **LongDescription**: una descripción amplia del elemento primario. (cero o un elemento).

### <a name="applicable-attributes"></a>Atributos aplicables

Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Documentation** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra el elemento **Documentation** como un elemento secundario de un elemento EntityType.

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="end-element-ssdl"></a>End (Elemento) (SSDL)

El elemento **End** en el lenguaje de definición de esquemas de almacenamiento (SSDL) especifica la tabla y el número de filas de un extremo de una restricción FOREIGN KEY en la base de datos subyacente. El elemento **End** puede ser un elemento secundario del elemento Association o del elemento AssociationSet. En cada caso, los posibles elementos secundarios y atributos aplicables son diferentes.

### <a name="end-element-as-a-child-of-the-association-element"></a>Elemento End como elemento secundario del elemento Association

Un elemento **End** (como elemento secundario del elemento **Association** ) especifica la tabla y el número de filas al final de una restricción FOREIGN KEY con los atributos **Type** y **Multiplicity** , respectivamente. Los extremos de una restricción de clave externa se definen como parte de una asociación SSDL, la cual debe tener exactamente dos extremos.

Un elemento **End** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   OnDelete (cero o un elemento)
-   Elementos Annotation (cero o más elementos)

#### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **End** cuando es el elemento secundario de un elemento **Association** .

| Nombre del atributo   | Es obligatorio | Value                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Tipo**         | Sí         | El nombre completo del conjunto de entidades SSDL que está en el extremo de la restricción de clave externa.                                                                                                                                                                                                                                                                                          |
| **Rol**         | No          | El valor del atributo **role** en el elemento principal o dependiente del elemento ReferentialConstraint correspondiente (si se usa).                                                                                                                                                                                                                                             |
| **Multiplicidad** | Sí         | **1**, **0.. 1**o, **\*** dependiendo del número de filas que puedan estar al final de la restricción FOREIGN KEY. <br/> **1** indica que existe exactamente una fila en el extremo de la restricción de clave externa. <br/> **0.. 1** indica que hay cero o una fila en el extremo de la restricción de clave externa. <br/> **\*** indica que hay cero, una o más filas en el extremo de la restricción de clave externa. |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **final** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

#### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **Association** que define la restricción de clave externa de **FK \_ CustomerOrders** . Los valores de **multiplicidad** especificados en cada elemento **final** indican que muchas filas de la tabla **Orders** se pueden asociar a una fila de la tabla **Customers** , pero solo se puede asociar una fila de la tabla **Customers** a una fila de la tabla **Orders** . Además, el elemento **aldelete** indica que todas las filas de la tabla **Orders** que hacen referencia a una fila determinada de la tabla **Customers** se eliminarán si se elimina la fila de la tabla **Customers** .

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

### <a name="end-element-as-a-child-of-the-associationset-element"></a>Elemento End como elemento secundario del elemento AssociationSet

El elemento **End** (como elemento secundario del elemento **AssociationSet** ) especifica una tabla en un extremo de una restricción FOREIGN KEY en la base de datos subyacente.

Un elemento **End** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o uno)
-   Elementos Annotation (cero o más)

#### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **End** cuando es el elemento secundario de un elemento **AssociationSet** .

| Nombre del atributo | Es obligatorio | Value                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| **EntitySet**  | Sí         | El nombre del conjunto de entidades SSDL que está en el extremo de la restricción de clave externa.                                      |
| **Rol**       | No          | El valor de uno de los atributos de **rol** especificados en un elemento **final** del elemento Association correspondiente. |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **final** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

#### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityContainer** con un elemento **AssociationSet** con dos elementos **End** :

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitycontainer-element-ssdl"></a>EntityContainer (Elemento) (SSDL)

Un elemento **EntityContainer** en el lenguaje de definición de esquemas de almacenamiento (SSDL) describe la estructura del origen de datos subyacente en una aplicación Entity Framework: los conjuntos de entidades SSDL (definidos en elementos EntitySet) representan las tablas de una base de datos, los tipos de entidad SSDL (definidos en los elementos EntityType) representan las filas de una tabla, y los conjuntos de asociaciones (definidos en los elementos AssociationSet) Un contenedor de entidades del modelo de almacenamiento se asigna a un contenedor de entidades del modelo de conceptual a través del elemento EntityContainerMapping.

Un elemento **EntityContainer** puede tener cero o un elemento de documentación. Si hay un elemento de **documentación** , debe preceder a todos los demás elementos secundarios.

Un elemento **EntityContainer** puede tener cero o más de los elementos secundarios siguientes (en el orden mostrado):

-   EntitySet
-   AssociationSet
-   Elementos Annotation

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntityContainer** .

| Nombre del atributo | Es obligatorio | Value                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| **Nombre**       | Sí         | Nombre del contenedor de entidades. Este nombre no puede contener puntos (.). |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **EntityContainer** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityContainer** que define dos conjuntos de entidades y un conjunto de asociaciones. Observe que los nombres de los tipos de entidad y tipos de asociación están calificados mediante el nombre del espacio de nombres del modelo conceptual.

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entityset-element-ssdl"></a>EntitySet (Elemento) (SSDL)

Un elemento **EntitySet** en el lenguaje de definición de esquemas de almacenamiento (SSDL) representa una tabla o una vista en la base de datos subyacente. Un elemento EntityType de SSDL representa una fila de la tabla o vista. El atributo **EntityType** de un elemento **EntitySet** especifica el tipo de entidad SSDL determinado que representa las filas de un conjunto de entidades SSDL. La asignación entre un conjunto de entidades CSDL y un conjunto de entidades SSDL se especifica en un elemento EntitySetMapping.

El elemento **EntitySet** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   DefiningQuery (cero o un elemento)
-   Elementos Annotation

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntitySet** .

> [!NOTE]
> Algunos atributos (que no se enumeran aquí) pueden estar calificados con el alias de **almacén** . El Asistente para actualizar modelo utiliza estos atributos al actualizar un modelo.

| Nombre del atributo | Es obligatorio | Value                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| **Nombre**       | Sí         | Nombre del conjunto de entidades.                                                              |
| **EntityType** | Sí         | El nombre completo del tipo de entidad para el que el conjunto de entidades contiene las instancias. |
| **Esquema**     | No          | El esquema de base de datos.                                                                     |
| **Table**      | No          | La tabla de base de datos.                                                                      |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **EntitySet** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityContainer** que tiene dos elementos **EntitySet** y un elemento **AssociationSet** :

``` xml
 <EntityContainer Name="ExampleModelStoreContainer">
   <EntitySet Name="Customers"
              EntityType="ExampleModel.Store.Customers"
              Schema="dbo" />
   <EntitySet Name="Orders"
              EntityType="ExampleModel.Store.Orders"
              Schema="dbo" />
   <AssociationSet Name="FK_CustomerOrders"
                   Association="ExampleModel.Store.FK_CustomerOrders">
     <End Role="Customers" EntitySet="Customers" />
     <End Role="Orders" EntitySet="Orders" />
   </AssociationSet>
 </EntityContainer>
```

## <a name="entitytype-element-ssdl"></a>EntityType (Elemento) (SSDL)

Un elemento **EntityType** en el lenguaje de definición de esquemas de almacenamiento (SSDL) representa una fila de una tabla o vista de la base de datos subyacente. Un elemento EntitySet de SSDL representa la tabla o vista donde están las filas. El atributo **EntityType** de un elemento **EntitySet** especifica el tipo de entidad SSDL determinado que representa las filas de un conjunto de entidades SSDL. La asignación entre un tipo de entidad SSDL y un tipo de entidad CSDL se especifica en un elemento EntityTypeMapping.

El elemento **EntityType** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o un elemento)
-   Key (cero o un elemento)
-   Elementos Annotation

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntityType** .

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**       | Sí         | El nombre del tipo de entidad. Este valor normalmente es igual que el nombre de la tabla en la que el tipo de entidad representa una fila. Este valor no puede contener ningún punto (.). |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **EntityType** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityType** con dos propiedades:

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="function-element-ssdl"></a>Function (Elemento) (SSDL)

El elemento **function** del lenguaje de definición de esquemas de almacenamiento (SSDL) especifica un procedimiento almacenado que existe en la base de datos subyacente.

El elemento **function** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o uno)
-   Parámetro (cero o más)
-   CommandText (cero o uno)
-   ReturnType (cero o más)
-   Elementos Annotation (cero o más)

Un tipo de valor devuelto para una función debe especificarse con el elemento **ReturnType** o el atributo **ReturnType** (consulte a continuación), pero no ambos.

Los procedimientos almacenados que se especifican en el modelo de almacenamiento se pueden importar en el modelo conceptual de una aplicación. Para obtener más información, vea [consultar con procedimientos almacenados](xref:ef6/modeling/designer/stored-procedures/query). El elemento de **función** también se puede utilizar para definir funciones personalizadas en el modelo de almacenamiento.  

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento de **función** .

> [!NOTE]
> Algunos atributos (que no se enumeran aquí) pueden estar calificados con el alias de **almacén** . El Asistente para actualizar modelo utiliza estos atributos al actualizar un modelo.

| Nombre del atributo             | Es obligatorio | Value                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**                   | Sí         | Nombre del procedimiento almacenado.                                                                                                                                                                                  |
| **ReturnType**             | No          | El tipo de valor devuelto del procedimiento almacenado.                                                                                                                                                                           |
| **Agregada**              | No          | **True** si el procedimiento almacenado devuelve un valor agregado; en caso contrario, **false**.                                                                                                                                  |
| **BuiltIn**                | No          | **True** si la función es una función integrada<sup>1</sup> ; en caso contrario, **false**.                                                                                                                                  |
| **StoreFunctionName**      | No          | Nombre del procedimiento almacenado.                                                                                                                                                                                  |
| **NiladicFunction**        | No          | **True** si la función es una función niládicas<sup>2</sup> ; De lo contrario, **false** .                                                                                                                                   |
| **IsComposable**           | No          | **True** si la función es una función que admite composición<sup>3</sup> ; De lo contrario, **false** .                                                                                                                                |
| **ParameterTypeSemantics** | No          | La enumeración que define la semántica de tipos que se utiliza para resolver sobrecargas de función. La enumeración se define en el manifiesto del proveedor por cada definición de función. El valor predeterminado es **AllowImplicitConversion**. |
| **Esquema**                 | No          | El nombre del esquema donde se define el procedimiento almacenado.                                                                                                                                                   |

<sup>1</sup> una función integrada es una función que se define en la base de datos. Para obtener información sobre las funciones que se definen en el modelo de almacenamiento, vea CommandText (elemento) (SSDL).

<sup>2</sup> una función niládicas es una función que no acepta parámetros y, cuando se llama, no requiere paréntesis.

<sup>3</sup> dos funciones son comparables si la salida de una función puede ser la entrada para la otra función.

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento de **función** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **function** que corresponde al procedimiento almacenado **UpdateOrderQuantity** . El procedimiento almacenado acepta dos parámetros y no devuelve ningún valor.

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="key-element-ssdl"></a>Key (Elemento) (SSDL)

El elemento **key** del lenguaje de definición de esquemas de almacenamiento (SSDL) representa la clave principal de una tabla en la base de datos subyacente. **Key** es un elemento secundario de un elemento EntityType, que representa una fila de una tabla. La clave principal se define en el elemento **key** haciendo referencia a uno o varios elementos de propiedad que se definen en el elemento **EntityType** .

El elemento **key** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   PropertyRef (uno o varios)
-   Elementos Annotation

No hay atributos aplicables al elemento **key** .

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityType** con una clave que hace referencia a una propiedad:

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="ondelete-element-ssdl"></a>OnDelete (Elemento) (SSDL)

El elemento **aldelete** del lenguaje de definición de esquemas de almacenamiento (SSDL) refleja el comportamiento de la base de datos cuando se elimina una fila que participa en una restricción FOREIGN KEY. Si la acción se establece en **Cascade**, también se eliminarán las filas que hacen referencia a una fila que se va a eliminar. Si la acción se establece en **None**, las filas que hacen referencia a una fila que se va a eliminar también no se eliminan. Un elemento **aldelete** es un elemento secundario de un elemento end.

Un elemento **aldelete** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o uno)
-   Elementos Annotation (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **aleliminar** .

| Nombre del atributo | Es obligatorio | Value                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| **Acción**     | Sí         | **Cascade** o **None**. (El valor **restringido** es válido, pero tiene el mismo comportamiento que **ninguno**). |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **aleliminar** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **Association** que define la restricción de clave externa de **FK \_ CustomerOrders** . El elemento **aldelete** indica que se eliminarán todas las filas de la tabla **Orders** que hagan referencia a una fila determinada de la tabla **Customers** si se elimina la fila de la tabla **Customers** .

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="parameter-element-ssdl"></a>Parameter (Elemento) (SSDL)

El elemento **Parameter** del lenguaje de definición de esquemas de almacenamiento (SSDL) es un elemento secundario del elemento function que especifica los parámetros de un procedimiento almacenado en la base de datos.

El elemento **Parameter** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   Documentation (cero o uno)
-   Elementos Annotation (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Parameter** .

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**       | Sí         | Nombre del parámetro.                                                                                                                                                                                                      |
| **Tipo**       | Sí         | El tipo de parámetro.                                                                                                                                                                                                             |
| **Modo**       | No          | **In**, **out**o **INOUT** dependiendo de si el parámetro es un parámetro de entrada, de salida o de entrada/salida.                                                                                                                |
| **MaxLength**  | No          | Longitud máxima permitida del parámetro.                                                                                                                                                                                            |
| **Precisión**  | No          | La precisión del parámetro.                                                                                                                                                                                                 |
| **Escala**      | No          | La escala del parámetro.                                                                                                                                                                                                     |
| **SRID**       | No          | Identificador de referencia del sistema espacial. Válido solo para los parámetros de los tipos espaciales. Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Parameter** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **function** que tiene dos elementos **Parameter** que especifican parámetros de entrada:

``` xml
 <Function Name="UpdateOrderQuantity"
           Aggregate="false"
           BuiltIn="false"
           NiladicFunction="false"
           IsComposable="false"
           ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="orderId" Type="int" Mode="In" />
   <Parameter Name="newQuantity" Type="int" Mode="In" />
 </Function>
```

## <a name="principal-element-ssdl"></a>Principal (Elemento) (SSDL)

El elemento **principal** del lenguaje de definición de esquemas de almacenamiento (SSDL) es un elemento secundario del elemento ReferentialConstraint que define el extremo principal de una restricción FOREIGN KEY (también denominada restricción referencial). El elemento **principal** especifica la columna (o columnas) de clave principal de una tabla a la que hace referencia otra columna (o columnas). Los elementos **PropertyRef** especifican a qué columnas se hace referencia. El elemento dependiente especifica las columnas que hacen referencia a las columnas de clave principal que se especifican en el elemento **principal** .

El elemento **principal** puede tener los elementos secundarios siguientes (en el orden mostrado):

-   PropertyRef (uno o varios)
-   Elementos Annotation (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **principal** .

| Nombre del atributo | Es obligatorio | Value                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Rol**       | Sí         | El mismo valor que el atributo **role** (si se usa) del elemento end correspondiente; de lo contrario, el nombre de la tabla que contiene la columna a la que se hace referencia. |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **principal** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento Association que utiliza un elemento **ReferentialConstraint** para especificar las columnas que participan en la restricción de clave externa de **FK \_ CustomerOrders** . El elemento **principal** especifica la columna **CustomerID** de la tabla **Customer** como el extremo principal de la restricción.

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="property-element-ssdl"></a>Property (Elemento) (SSDL)

El elemento **Property** del lenguaje de definición de esquemas de almacenamiento (SSDL) representa una columna de una tabla en la base de datos subyacente. Los elementos de **propiedad** son elementos secundarios de elementos EntityType, que representan las filas de una tabla. Cada elemento de **propiedad** definido en un elemento **EntityType** representa una columna.

Un elemento **Property** no puede tener elementos secundarios.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Property** .

| Nombre del atributo            | Es obligatorio | Value                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nombre**                  | Sí         | El nombre de la columna correspondiente.                                                                                                                                                                                           |
| **Tipo**                  | Sí         | El tipo de la columna correspondiente.                                                                                                                                                                                           |
| **Admisión de valores NULL**              | No          | **True** (valor predeterminado) o **false** , dependiendo de si la columna correspondiente puede tener un valor null.                                                                                                                  |
| **DefaultValue**          | No          | El valor predeterminado de la columna correspondiente.                                                                                                                                                                                  |
| **MaxLength**             | No          | La longitud máxima de la columna correspondiente.                                                                                                                                                                                 |
| **FixedLength**           | No          | **True** o **false** , dependiendo de si el valor de columna correspondiente se almacenará como una cadena de longitud fija.                                                                                                              |
| **Precisión**             | No          | La precisión de la columna correspondiente.                                                                                                                                                                                      |
| **Escala**                 | No          | La escala de la columna correspondiente.                                                                                                                                                                                          |
| **Unicode**               | No          | **True** o **false** , dependiendo de si el valor de columna correspondiente se almacenará como una cadena Unicode.                                                                                                                   |
| **Intercalación**             | No          | Cadena que especifica la secuencia de intercalación que se usará en el origen de datos.                                                                                                                                                   |
| **SRID**                  | No          | Identificador de referencia del sistema espacial. Solo es válido para las propiedades de los tipos espaciales. Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx). |
| **StoreGeneratedPattern** | No          | **None**, **Identity** (si el valor de la columna correspondiente es una identidad que se genera en la base de datos) o **calculado** (si el valor de la columna correspondiente se calcula en la base de datos). No es válido para las propiedades RowType. |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento de **propiedad** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **EntityType** con dos elementos **Property** secundarios:

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="propertyref-element-ssdl"></a>PropertyRef (Elemento) (SSDL)

El elemento **PropertyRef** en el lenguaje de definición de esquemas de almacenamiento (SSDL) hace referencia a una propiedad definida en un elemento EntityType para indicar que la propiedad realizará uno de los roles siguientes:

-   Formar parte de la clave principal de la tabla que el **EntityType** representa. Se pueden usar uno o varios elementos **PropertyRef** para definir una clave principal. Para obtener más información, vea Key (Elemento).
-   Ser el extremo dependiente o principal de una restricción referencial. Para obtener más información, vea ReferentialConstraint (Elemento).

El elemento **PropertyRef** solo puede tener los siguientes elementos secundarios:

-   Documentation (cero o uno)
-   Elementos Annotation

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **PropertyRef** .

| Nombre del atributo | Es obligatorio | Value                                |
|:---------------|:------------|:-------------------------------------|
| **Nombre**       | Sí         | Nombre de la propiedad a la que se hace referencia. |

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **PropertyRef** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **PropertyRef** que se usa para definir una clave principal haciendo referencia a una propiedad que se define en un elemento **EntityType** .

``` xml
 <EntityType Name="Customers">
   <Documentation>
     <Summary>Summary here.</Summary>
     <LongDescription>Long description here.</LongDescription>
   </Documentation>
   <Key>
     <PropertyRef Name="CustomerId" />
   </Key>
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
 </EntityType>
```

## <a name="referentialconstraint-element-ssdl"></a>ReferentialConstraint (Elemento) (SSDL)

El elemento **ReferentialConstraint** del lenguaje de definición de esquemas de almacenamiento (SSDL) representa una restricción de clave externa (también denominada restricción de integridad referencial) en la base de datos subyacente. Los extremos principal y dependiente de la restricción se especifican mediante los elementos secundarios Principal y Dependent, respectivamente. La referencia a las columnas que participan en los extremos principal y dependiente se realiza mediante elementos PropertyRef.

El elemento **ReferentialConstraint** es un elemento secundario opcional del elemento Association. Si no se utiliza un elemento **ReferentialConstraint** para asignar la restricción FOREIGN KEY especificada en el elemento **Association** , se debe usar un elemento AssociationSetMapping para hacerlo.

El elemento **ReferentialConstraint** puede tener los siguientes elementos secundarios:

-   Documentation (cero o uno)
-   Principal (exactamente uno)
-   Dependent (exactamente uno)
-   Elementos Annotation (cero o más)

### <a name="applicable-attributes"></a>Atributos aplicables

Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **ReferentialConstraint** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **Association** que utiliza un elemento **ReferentialConstraint** para especificar las columnas que participan en la restricción FOREIGN KEY externa de **FK \_ ** :

``` xml
 <Association Name="FK_CustomerOrders">
   <End Role="Customers"
        Type="ExampleModel.Store.Customers" Multiplicity="1">
     <OnDelete Action="Cascade" />
   </End>
   <End Role="Orders"
        Type="ExampleModel.Store.Orders" Multiplicity="*" />
   <ReferentialConstraint>
     <Principal Role="Customers">
       <PropertyRef Name="CustomerId" />
     </Principal>
     <Dependent Role="Orders">
       <PropertyRef Name="CustomerId" />
     </Dependent>
   </ReferentialConstraint>
 </Association>
```

## <a name="returntype-element-ssdl"></a>ReturnType (elemento) (SSDL)

El elemento **ReturnType** del lenguaje de definición de esquemas de almacenamiento (SSDL) especifica el tipo de valor devuelto para una función que se define en un elemento de **función** . Un tipo de valor devuelto de función también se puede especificar con un atributo **ReturnType** .

El tipo de valor devuelto de una función se especifica con el atributo **Type** o el elemento **ReturnType** .

El elemento **ReturnType** puede tener los siguientes elementos secundarios:

- CollectionType (uno)  

> [!NOTE]
> Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **ReturnType** . Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL. Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se utiliza una **función** que devuelve una colección de filas.

``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```


## <a name="rowtype-element-ssdl"></a>RowType (elemento) (SSDL)

Un elemento **RowType** en el lenguaje de definición de esquemas de almacenamiento (SSDL) define una estructura sin nombre como un tipo de valor devuelto para una función definida en el almacén.

Un elemento **RowType** es el elemento secundario del elemento **CollectionType** :

Un elemento **RowType** puede tener los siguientes elementos secundarios:

- Property (uno o varios)  

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra una función de almacenamiento que usa un elemento **CollectionType** para especificar que la función devuelve una colección de filas (tal y como se especifica en el elemento **RowType** ).


``` xml
   <Function Name="GetProducts" IsComposable="true" Schema="dbo">
     <ReturnType>
       <CollectionType>
         <RowType>
           <Property Name="ProductID" Type="int" Nullable="false" />
           <Property Name="CategoryID" Type="bigint" Nullable="false" />
           <Property Name="ProductName" Type="nvarchar" MaxLength="40" Nullable="false" />
           <Property Name="UnitPrice" Type="money" />
           <Property Name="Discontinued" Type="bit" />
         </RowType>
       </CollectionType>
     </ReturnType>
   </Function>
```

## <a name="schema-element-ssdl"></a>Schema (Elemento) (SSDL)

El elemento **Schema** en el lenguaje de definición de esquemas de almacenamiento (SSDL) es el elemento raíz de una definición de modelo de almacenamiento. Contiene las definiciones para los objetos, las funciones y los contenedores que conforman un modelo de almacenamiento.

El elemento **Schema** puede contener cero o más de los siguientes elementos secundarios:

-   Asociación
-   EntityType
-   EntityContainer
-   Función

El elemento **Schema** usa el atributo **namespace** para definir el espacio de nombres para el tipo de entidad y los objetos de asociación en un modelo de almacenamiento. Dentro de un espacio de nombres, no puede haber dos objetos con el mismo nombre.

Un espacio de nombres del modelo de almacenamiento es diferente del espacio de nombres XML del elemento **Schema** . Un espacio de nombres del modelo de almacenamiento (tal y como se define en el atributo de **espacio de nombres** ) es un contenedor lógico para tipos de entidad y tipos de asociación. El espacio de nombres XML (indicado por el atributo **xmlns** ) de un elemento **Schema** es el espacio de nombres predeterminado para los elementos secundarios y los atributos del elemento **Schema** . Los espacios de nombres XML del formulario https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (donde YYYY y mm representan un año y un mes respectivamente) se reservan para SSDL. No puede haber elementos y atributos personalizados en espacios de nombres que tengan este formato.

### <a name="applicable-attributes"></a>Atributos aplicables

En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Schema** .

| Nombre del atributo            | Es obligatorio | Value                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Espacio de nombres**             | Sí         | El espacio de nombres del modelo de almacenamiento. El valor del atributo **namespace** se usa para formar el nombre completo de un tipo. Por ejemplo, si un **EntityType** denominado *Customer* está en el espacio de nombres ExampleModel. Store, el nombre completo del **EntityType** es ExampleModel. Store. Customer. <br/> Las siguientes cadenas no se pueden usar como el valor del atributo **namespace** : **System**, **Transient**o **EDM**. El valor del atributo **namespace** no puede ser el mismo que el valor del atributo **namespace** del elemento Schema de CSDL. |
| **Alias**                 | No          | Un identificador usado en lugar del nombre del espacio de nombres. Por ejemplo, si un **EntityType** denominado *Customer* está en el espacio de nombres ExampleModel. Store y el valor del atributo **alias** es *StorageModel*, puede usar StorageModel. Customer como nombre completo del **EntityType.**                                                                                                                                                                                                                                                                                    |
| **Proveedor**              | Sí         | El proveedor de datos.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| **ProviderManifestToken** | Sí         | Un token que indica al proveedor qué manifiesto del proveedor debe devolver. No se define ningún formato para el token. El proveedor define los valores para el token. Para obtener información sobre los tokens del manifiesto del proveedor de SQL Server, vea SqlClient para Entity Framework.                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento **Schema** que contiene un elemento **EntityContainer** , dos elementos **EntityType** y un elemento **Association** .

``` xml
 <Schema Namespace="ExampleModel.Store"
       Alias="Self" Provider="System.Data.SqlClient"
       ProviderManifestToken="2008"
       xmlns="https://schemas.microsoft.com/ado/2009/11/edm/ssdl">
   <EntityContainer Name="ExampleModelStoreContainer">
     <EntitySet Name="Customers"
                EntityType="ExampleModel.Store.Customers"
                Schema="dbo" />
     <EntitySet Name="Orders"
                EntityType="ExampleModel.Store.Orders"
                Schema="dbo" />
     <AssociationSet Name="FK_CustomerOrders"
                     Association="ExampleModel.Store.FK_CustomerOrders">
       <End Role="Customers" EntitySet="Customers" />
       <End Role="Orders" EntitySet="Orders" />
     </AssociationSet>
   </EntityContainer>
   <EntityType Name="Customers">
     <Documentation>
       <Summary>Summary here.</Summary>
       <LongDescription>Long description here.</LongDescription>
     </Documentation>
     <Key>
       <PropertyRef Name="CustomerId" />
     </Key>
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <Property Name="Name" Type="nvarchar(max)" Nullable="false" />
   </EntityType>
   <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
     <Key>
       <PropertyRef Name="OrderId" />
     </Key>
     <Property Name="OrderId" Type="int" Nullable="false"
               c:CustomAttribute="someValue"/>
     <Property Name="ProductId" Type="int" Nullable="false" />
     <Property Name="Quantity" Type="int" Nullable="false" />
     <Property Name="CustomerId" Type="int" Nullable="false" />
     <c:CustomElement>
       Custom data here.
     </c:CustomElement>
   </EntityType>
   <Association Name="FK_CustomerOrders">
     <End Role="Customers"
          Type="ExampleModel.Store.Customers" Multiplicity="1">
       <OnDelete Action="Cascade" />
     </End>
     <End Role="Orders"
          Type="ExampleModel.Store.Orders" Multiplicity="*" />
     <ReferentialConstraint>
       <Principal Role="Customers">
         <PropertyRef Name="CustomerId" />
       </Principal>
       <Dependent Role="Orders">
         <PropertyRef Name="CustomerId" />
       </Dependent>
     </ReferentialConstraint>
   </Association>
   <Function Name="UpdateOrderQuantity"
             Aggregate="false"
             BuiltIn="false"
             NiladicFunction="false"
             IsComposable="false"
             ParameterTypeSemantics="AllowImplicitConversion"
             Schema="dbo">
     <Parameter Name="orderId" Type="int" Mode="In" />
     <Parameter Name="newQuantity" Type="int" Mode="In" />
   </Function>
   <Function Name="UpdateProductInOrder" IsComposable="false">
     <CommandText>
       UPDATE Orders
       SET ProductId = @productId
       WHERE OrderId = @orderId;
     </CommandText>
     <Parameter Name="productId"
                Mode="In"
                Type="int"/>
     <Parameter Name="orderId"
                Mode="In"
                Type="int"/>
   </Function>
 </Schema>
```

## <a name="annotation-attributes"></a>Atributos de anotación

Los atributos de anotación (Annotation) del lenguaje de definición de esquemas de almacenamiento (SSDL) son atributos XML personalizados del modelo de almacenamiento que proporcionan metadatos adicionales sobre los elementos del modelo de almacenamiento. Además de tener una estructura XML válida, las siguientes restricciones se aplican a los atributos de anotación:

-   Los atributos de anotación no deben estar en ningún espacio de nombres XML reservado para SSDL.
-   Dos atributos de anotación cualesquiera no pueden tener el mismo nombre completo.

Se pueden aplicar varios atributos de anotación a un elemento SSDL determinado. Se puede tener acceso a los metadatos contenidos en los elementos Annotation en tiempo de ejecución mediante el uso de clases en el espacio de nombres System. Data. Metadata. Edm.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento EntityType que tiene un atributo Annotation aplicado a la propiedad **OrderID** . En el ejemplo también se muestra un elemento Annotation agregado al elemento **EntityType** .

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="annotation-elements-ssdl"></a>Annotation (Elementos) (SSDL)

Los elementos Annotation del lenguaje de definición de esquemas de almacenamiento (SSDL) son elementos XML personalizados del modelo de almacenamiento que proporcionan metadatos adicionales sobre el modelo de almacenamiento. Además de tener una estructura XML válida, las siguientes restricciones se aplican a los elementos Annotation:

-   Los elementos Annotation no deben estar en un espacio de nombres de XML que esté reservado para SSDL.
-   Los nombres completos de dos elementos Annotation cualesquiera no deben ser los mismos.
-   Los elementos Annotation deben aparecer después de todos los demás elementos secundarios de un elemento SSDL determinado.

Más de un elemento Annotation puede ser un elemento secundario de un elemento SSDL determinado. A partir de la .NET Framework versión 4, se puede tener acceso a los metadatos contenidos en los elementos Annotation en tiempo de ejecución mediante el uso de clases en el espacio de nombres System. Data. Metadata. Edm.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra un elemento EntityType que tiene un elemento Annotation (**CustomElement**). En el ejemplo también se muestra un atributo de anotación aplicado a la propiedad **OrderID** .

``` xml
 <EntityType Name="Orders" xmlns:c="http://CustomNamespace">
   <Key>
     <PropertyRef Name="OrderId" />
   </Key>
   <Property Name="OrderId" Type="int" Nullable="false"
             c:CustomAttribute="someValue"/>
   <Property Name="ProductId" Type="int" Nullable="false" />
   <Property Name="Quantity" Type="int" Nullable="false" />
   <Property Name="CustomerId" Type="int" Nullable="false" />
   <c:CustomElement>
     Custom data here.
   </c:CustomElement>
 </EntityType>
```

## <a name="facets-ssdl"></a>Facetas (SSDL)

Las facetas del lenguaje de definición de esquema de almacenamiento (SSDL) representan restricciones sobre los tipos de columna que se especifican en elementos Property. Las caras se implementan como atributos XML en los elementos de **propiedad** .

En la tabla siguiente se describen las facetas que se admiten en SSDL:

| Faceta           | Descripción                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Intercalación**   | Especifica la secuencia de intercalación (o secuencia de orden) que se va a usar cuando se realicen las operaciones de comparación y ordenación sobre los valores de la propiedad.                                                                                                             |
| **FixedLength** | Especifica si la longitud del valor de la columna puede variar.                                                                                                                                                                                                  |
| **MaxLength**   | Especifica la longitud máxima del valor de la columna.                                                                                                                                                                                                           |
| **Precisión**   | Para las propiedades de tipo **decimal**, especifica el número de dígitos que puede tener un valor de propiedad. Para las propiedades de tipo **Time**, **DateTime**y **DateTimeOffset**, especifica el número de dígitos para la parte fraccionaria de los segundos del valor de la columna. |
| **Escala**       | Especifica el número de dígitos que puede haber a la derecha del separador de decimales para el valor de la columna.                                                                                                                                                                      |
| **Unicode**     | Indica si el valor de la columna está almacenado como Unicode.                                                                                                                                                                                                    |
