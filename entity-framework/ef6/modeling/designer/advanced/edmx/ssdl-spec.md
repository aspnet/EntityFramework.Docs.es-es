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
# <a name="ssdl-specification"></a><span data-ttu-id="1be6f-103">Especificación SSDL</span><span class="sxs-lookup"><span data-stu-id="1be6f-103">SSDL Specification</span></span>
<span data-ttu-id="1be6f-104">El lenguaje de definición de esquemas de almacenamiento (SSDL) es un lenguaje basado en XML que describe el modelo de almacenamiento de una aplicación Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="1be6f-104">Store schema definition language (SSDL) is an XML-based language that describes the storage model of an Entity Framework application.</span></span>

<span data-ttu-id="1be6f-105">En una aplicación Entity Framework, los metadatos del modelo de almacenamiento se cargan desde un archivo. SSDL (escrito en SSDL) en una instancia de System. Data. Metadata. Edm. StoreItemCollection y es accesible mediante métodos en la clase System. Data. Metadata. Edm. MetadataWorkspace.</span><span class="sxs-lookup"><span data-stu-id="1be6f-105">In an Entity Framework application, storage model metadata is loaded from a .ssdl file (written in SSDL) into an instance of the System.Data.Metadata.Edm.StoreItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="1be6f-106">Entity Framework usa los metadatos del modelo de almacenamiento para traducir las consultas en el modelo conceptual para almacenar comandos específicos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-106">Entity Framework uses storage model metadata to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="1be6f-107">El Entity Framework Designer (EF Designer) almacena la información del modelo de almacenamiento en un archivo. edmx en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="1be6f-107">The Entity Framework Designer (EF Designer) stores storage model information in an .edmx file at design time.</span></span> <span data-ttu-id="1be6f-108">En tiempo de compilación, Entity Designer usa información en un archivo. edmx para crear el archivo. SSDL que Entity Framework necesita en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="1be6f-108">At build time the Entity Designer uses information in an .edmx file to create the .ssdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="1be6f-109">Las versiones de SSDL se diferencian por los espacios de nombres XML.</span><span class="sxs-lookup"><span data-stu-id="1be6f-109">Versions of SSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="1be6f-110">Versión de SSDL</span><span class="sxs-lookup"><span data-stu-id="1be6f-110">SSDL Version</span></span> | <span data-ttu-id="1be6f-111">Espacio de nombres XML</span><span class="sxs-lookup"><span data-stu-id="1be6f-111">XML Namespace</span></span>                                     |
|:-------------|:--------------------------------------------------|
| <span data-ttu-id="1be6f-112">SSDL v1</span><span class="sxs-lookup"><span data-stu-id="1be6f-112">SSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm/ssdl |
| <span data-ttu-id="1be6f-113">SSDL V2</span><span class="sxs-lookup"><span data-stu-id="1be6f-113">SSDL v2</span></span>      | https://schemas.microsoft.com/ado/2009/02/edm/ssdl |
| <span data-ttu-id="1be6f-114">SSDL V3</span><span class="sxs-lookup"><span data-stu-id="1be6f-114">SSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm/ssdl |

## <a name="association-element-ssdl"></a><span data-ttu-id="1be6f-115">Association (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-115">Association Element (SSDL)</span></span>

<span data-ttu-id="1be6f-116">Un elemento **Association** en el lenguaje de definición de esquemas de almacenamiento (SSDL) especifica las columnas de tabla que participan en una restricción FOREIGN KEY en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-116">An **Association** element in store schema definition language (SSDL) specifies table columns that participate in a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="1be6f-117">Dos elementos End secundarios necesarios especifican las tablas de los extremos de la asociación y la multiplicidad en cada extremo.</span><span class="sxs-lookup"><span data-stu-id="1be6f-117">Two required child End elements specify tables at the ends of the association and the multiplicity at each end.</span></span> <span data-ttu-id="1be6f-118">Un elemento ReferentialConstraint opcional especifica los extremos principal y dependiente de la asociación así como las columnas participantes.</span><span class="sxs-lookup"><span data-stu-id="1be6f-118">An optional ReferentialConstraint element specifies the principal and dependent ends of the association as well as the participating columns.</span></span> <span data-ttu-id="1be6f-119">Si no hay ningún elemento **ReferentialConstraint** , se debe usar un elemento AssociationSetMapping para especificar las asignaciones de columnas para la asociación.</span><span class="sxs-lookup"><span data-stu-id="1be6f-119">If no **ReferentialConstraint** element is present, an AssociationSetMapping element must be used to specify the column mappings for the association.</span></span>

<span data-ttu-id="1be6f-120">El elemento **Association** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-120">The **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-121">Documentation (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-121">Documentation (zero or one)</span></span>
-   <span data-ttu-id="1be6f-122">End (exactamente dos)</span><span class="sxs-lookup"><span data-stu-id="1be6f-122">End (exactly two)</span></span>
-   <span data-ttu-id="1be6f-123">ReferentialConstraint (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-123">ReferentialConstraint (zero or one)</span></span>
-   <span data-ttu-id="1be6f-124">Elementos Annotation (cero o más)</span><span class="sxs-lookup"><span data-stu-id="1be6f-124">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-125">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-125">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-126">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-126">The following table describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="1be6f-127">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-127">Attribute Name</span></span> | <span data-ttu-id="1be6f-128">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-128">Is Required</span></span> | <span data-ttu-id="1be6f-129">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-129">Value</span></span>                                                                            |
|:---------------|:------------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-130">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="1be6f-130">**Name**</span></span>       | <span data-ttu-id="1be6f-131">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-131">Yes</span></span>         | <span data-ttu-id="1be6f-132">El nombre de la restricción de clave externa correspondiente de la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-132">The name of the corresponding foreign key constraint in the underlying database.</span></span> |

> [!NOTE]
> <span data-ttu-id="1be6f-133">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-133">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="1be6f-134">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-134">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="1be6f-135">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-135">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-136">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-136">Example</span></span>

<span data-ttu-id="1be6f-137">En el ejemplo siguiente se muestra un elemento **Association** que utiliza un elemento **ReferentialConstraint** para especificar las columnas que participan en la restricción FOREIGN KEY externa de \*\*FK \_ \*\* :</span><span class="sxs-lookup"><span data-stu-id="1be6f-137">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="associationset-element-ssdl"></a><span data-ttu-id="1be6f-138">AssociationSet (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-138">AssociationSet Element (SSDL)</span></span>

<span data-ttu-id="1be6f-139">El elemento **AssociationSet** en el lenguaje de definición de esquemas de almacenamiento (SSDL) representa una restricción de clave externa entre dos tablas en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-139">The **AssociationSet** element in store schema definition language (SSDL) represents a foreign key constraint between two tables in the underlying database.</span></span> <span data-ttu-id="1be6f-140">Las columnas de la tabla que participan en la restricción de clave externa se especifican en un elemento Association.</span><span class="sxs-lookup"><span data-stu-id="1be6f-140">The table columns that participate in the foreign key constraint are specified in an Association element.</span></span> <span data-ttu-id="1be6f-141">El elemento **Association** que corresponde a un elemento **AssociationSet** determinado se especifica en el atributo **Association** del elemento **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-141">The **Association** element that corresponds to a given **AssociationSet** element is specified in the **Association** attribute of the **AssociationSet** element.</span></span>

<span data-ttu-id="1be6f-142">Los conjuntos de asociaciones SSDL están asignados a conjuntos de asociaciones CSDL mediante un elemento AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="1be6f-142">SSDL association sets are mapped to CSDL association sets by an AssociationSetMapping element.</span></span> <span data-ttu-id="1be6f-143">Sin embargo, si la Asociación CSDL para un conjunto de asociaciones CSDL determinado se define mediante un elemento ReferentialConstraint, no es necesario ningún elemento **AssociationSetMapping** correspondiente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-143">However, if the CSDL association for a given CSDL association set is defined by using a ReferentialConstraint element , no corresponding **AssociationSetMapping** element is necessary.</span></span> <span data-ttu-id="1be6f-144">En este caso, si hay un elemento **AssociationSetMapping** , las asignaciones que define se reemplazarán por el elemento **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-144">In this case, if an **AssociationSetMapping** element is present, the mappings it defines will be overridden by the **ReferentialConstraint** element.</span></span>

<span data-ttu-id="1be6f-145">El elemento **AssociationSet** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-145">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-146">Documentation (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-146">Documentation (zero or one)</span></span>
-   <span data-ttu-id="1be6f-147">End (cero o dos)</span><span class="sxs-lookup"><span data-stu-id="1be6f-147">End (zero or two)</span></span>
-   <span data-ttu-id="1be6f-148">Elementos Annotation (cero o más)</span><span class="sxs-lookup"><span data-stu-id="1be6f-148">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-149">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-149">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-150">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-150">The following table describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="1be6f-151">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-151">Attribute Name</span></span>  | <span data-ttu-id="1be6f-152">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-152">Is Required</span></span> | <span data-ttu-id="1be6f-153">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-153">Value</span></span>                                                                                                |
|:----------------|:------------|:-----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-154">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="1be6f-154">**Name**</span></span>        | <span data-ttu-id="1be6f-155">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-155">Yes</span></span>         | <span data-ttu-id="1be6f-156">El nombre de la restricción de clave externa representada por el conjunto de asociaciones.</span><span class="sxs-lookup"><span data-stu-id="1be6f-156">The name of the foreign key constraint that the association set represents.</span></span>                          |
| <span data-ttu-id="1be6f-157">**Asociación**</span><span class="sxs-lookup"><span data-stu-id="1be6f-157">**Association**</span></span> | <span data-ttu-id="1be6f-158">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-158">Yes</span></span>         | <span data-ttu-id="1be6f-159">El nombre de la asociación que define las columnas que participan en la restricción de clave externa.</span><span class="sxs-lookup"><span data-stu-id="1be6f-159">The name of the association that defines the columns that participate in the foreign key constraint.</span></span> |

> [!NOTE]
> <span data-ttu-id="1be6f-160">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-160">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="1be6f-161">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-161">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="1be6f-162">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-162">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-163">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-163">Example</span></span>

<span data-ttu-id="1be6f-164">En el ejemplo siguiente se muestra un elemento **AssociationSet** que representa la `FK_CustomerOrders` restricción FOREIGN KEY en la base de datos subyacente:</span><span class="sxs-lookup"><span data-stu-id="1be6f-164">The following example shows an **AssociationSet** element that represents the `FK_CustomerOrders` foreign key constraint in the underlying database:</span></span>

``` xml
 <AssociationSet Name="FK_CustomerOrders"
                 Association="ExampleModel.Store.FK_CustomerOrders">
   <End Role="Customers" EntitySet="Customers" />
   <End Role="Orders" EntitySet="Orders" />
 </AssociationSet>
```

## <a name="collectiontype-element-ssdl"></a><span data-ttu-id="1be6f-165">CollectionType (elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-165">CollectionType Element (SSDL)</span></span>

<span data-ttu-id="1be6f-166">El elemento **CollectionType** del lenguaje de definición de esquemas de almacenamiento (SSDL) especifica que el tipo de valor devuelto de una función es una colección.</span><span class="sxs-lookup"><span data-stu-id="1be6f-166">The **CollectionType** element in store schema definition language (SSDL) specifies that a function’s return type is a collection.</span></span> <span data-ttu-id="1be6f-167">El elemento **CollectionType** es un elemento secundario del elemento ReturnType.</span><span class="sxs-lookup"><span data-stu-id="1be6f-167">The **CollectionType** element is a child of the ReturnType element.</span></span> <span data-ttu-id="1be6f-168">El tipo de colección se especifica mediante el elemento secundario RowType:</span><span class="sxs-lookup"><span data-stu-id="1be6f-168">The type of collection is specified by using the RowType child element:</span></span>

> [!NOTE]
> <span data-ttu-id="1be6f-169">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-169">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="1be6f-170">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-170">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="1be6f-171">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-171">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-172">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-172">Example</span></span>

<span data-ttu-id="1be6f-173">En el ejemplo siguiente se muestra una función que usa un elemento **CollectionType** para especificar que la función devuelve una colección de filas.</span><span class="sxs-lookup"><span data-stu-id="1be6f-173">The following example shows a function that uses a **CollectionType** element to specify that the function returns a collection of rows.</span></span>

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

## <a name="commandtext-element-ssdl"></a><span data-ttu-id="1be6f-174">CommandText (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-174">CommandText Element (SSDL)</span></span>

<span data-ttu-id="1be6f-175">El elemento **CommandText** del lenguaje de definición de esquemas de almacenamiento (SSDL) es un elemento secundario del elemento function que permite definir una instrucción SQL que se ejecuta en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-175">The **CommandText** element in store schema definition language (SSDL) is a child of the Function element that allows you to define a SQL statement that is executed at the database.</span></span> <span data-ttu-id="1be6f-176">El elemento **CommandText** permite agregar funcionalidad similar a un procedimiento almacenado en la base de datos, pero se define el elemento **CommandText** en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1be6f-176">The **CommandText** element allows you to add functionality that is similar to a stored procedure in the database, but you define the **CommandText** element in the storage model.</span></span>

<span data-ttu-id="1be6f-177">El elemento **CommandText** no puede tener elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="1be6f-177">The **CommandText** element cannot have child elements.</span></span> <span data-ttu-id="1be6f-178">El cuerpo del elemento **CommandText** debe ser una instrucción SQL válida para la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-178">The body of the **CommandText** element must be a valid SQL statement for the underlying database.</span></span>

<span data-ttu-id="1be6f-179">No hay atributos aplicables al elemento **CommandText** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-179">No attributes are applicable to the **CommandText** element.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-180">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-180">Example</span></span>

<span data-ttu-id="1be6f-181">En el ejemplo siguiente se muestra un elemento **function** con un elemento **CommandText** secundario.</span><span class="sxs-lookup"><span data-stu-id="1be6f-181">The following example shows a **Function** element with a child **CommandText** element.</span></span> <span data-ttu-id="1be6f-182">Exponga la función **UpdateProductInOrder** como un método en el ObjectContext importándolos en el modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="1be6f-182">Expose the **UpdateProductInOrder** function as a method on the ObjectContext by importing it into the conceptual model.</span></span>  

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

## <a name="definingquery-element-ssdl"></a><span data-ttu-id="1be6f-183">DefiningQuery (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-183">DefiningQuery Element (SSDL)</span></span>

<span data-ttu-id="1be6f-184">El elemento **DefiningQuery** del lenguaje de definición de esquemas de almacenamiento (SSDL) permite ejecutar una instrucción SQL directamente en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-184">The **DefiningQuery** element in store schema definition language (SSDL) allows you to execute a SQL statement directly in the underlying database.</span></span> <span data-ttu-id="1be6f-185">El elemento **DefiningQuery** se utiliza normalmente como una vista de base de datos, pero la vista se define en el modelo de almacenamiento en lugar de en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-185">The **DefiningQuery** element is commonly used like a database view, but the view is defined in the storage model instead of the database.</span></span> <span data-ttu-id="1be6f-186">La vista definida en un elemento **DefiningQuery** se puede asignar a un tipo de entidad en el modelo conceptual a través de un elemento EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="1be6f-186">The view defined in a **DefiningQuery** element can be mapped to an entity type in the conceptual model through an EntitySetMapping element.</span></span> <span data-ttu-id="1be6f-187">Estas asignaciones son de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="1be6f-187">These mappings are read-only.</span></span>  

<span data-ttu-id="1be6f-188">La sintaxis de SSDL siguiente muestra la declaración de un **EntitySet** seguido del elemento **DefiningQuery** que contiene una consulta utilizada para recuperar la vista.</span><span class="sxs-lookup"><span data-stu-id="1be6f-188">The following SSDL syntax shows the declaration of an **EntitySet** followed by the **DefiningQuery** element that contains a query used to retrieve the view.</span></span>

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

<span data-ttu-id="1be6f-189">Puede usar procedimientos almacenados en el Entity Framework para habilitar escenarios de lectura y escritura en las vistas.</span><span class="sxs-lookup"><span data-stu-id="1be6f-189">You can use stored procedures in the Entity Framework to enable read-write scenarios over views.</span></span><span data-ttu-id="1be6f-190">Puede usar una vista del origen de datos o una vista de Entity SQL como tabla base para recuperar datos y para el procesamiento de cambios por parte de los procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="1be6f-190"> You can use either a data source view or an Entity SQL view as the base table for retrieving data and for change processing by stored procedures.</span></span>

<span data-ttu-id="1be6f-191">Puede usar el elemento **DefiningQuery** para tener como destino Microsoft SQL Server Compact 3,5.</span><span class="sxs-lookup"><span data-stu-id="1be6f-191">You can use the **DefiningQuery** element to target Microsoft SQL Server Compact 3.5.</span></span> <span data-ttu-id="1be6f-192">Aunque SQL Server Compact 3,5 no admite procedimientos almacenados, puede implementar una funcionalidad similar con el elemento **DefiningQuery** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-192">Though SQL Server Compact 3.5 does not support stored procedures, you can implement similar functionality with the **DefiningQuery** element.</span></span> <span data-ttu-id="1be6f-193">Otro caso donde puede ser útil es en la creación de procedimientos almacenados para resolver una desigualdad entre los tipos de datos utilizados en el lenguaje de programación y los del origen de datos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-193">Another place where it can be useful is in creating stored procedures to overcome a mismatch between the data types used in the programming language and those of the data source.</span></span> <span data-ttu-id="1be6f-194">Podría escribir una **DefiningQuery** que toma un determinado conjunto de parámetros y, a continuación, llama a un procedimiento almacenado con un conjunto diferente de parámetros, por ejemplo, un procedimiento almacenado que elimina datos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-194">You could write a **DefiningQuery** that takes a certain set of parameters and then calls a stored procedure with a different set of parameters, for example, a stored procedure that deletes data.</span></span>

## <a name="dependent-element-ssdl"></a><span data-ttu-id="1be6f-195">Dependent (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-195">Dependent Element (SSDL)</span></span>

<span data-ttu-id="1be6f-196">El elemento **dependiente** del lenguaje de definición de esquemas de almacenamiento (SSDL) es un elemento secundario del elemento ReferentialConstraint que define el extremo dependiente de una restricción FOREIGN KEY (también denominada restricción referencial).</span><span class="sxs-lookup"><span data-stu-id="1be6f-196">The **Dependent** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the dependent end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="1be6f-197">El elemento **dependiente** especifica la columna (o columnas) de una tabla que hace referencia a una columna de clave principal (o columnas).</span><span class="sxs-lookup"><span data-stu-id="1be6f-197">The **Dependent** element specifies the column (or columns) in a table that reference a primary key column (or columns).</span></span> <span data-ttu-id="1be6f-198">Los elementos **PropertyRef** especifican a qué columnas se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="1be6f-198">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="1be6f-199">El elemento principal especifica las columnas de clave principal a las que hacen referencia las columnas que se especifican en el elemento **dependiente** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-199">The Principal element specifies the primary key columns that are referenced by columns that are specified in the **Dependent** element.</span></span>

<span data-ttu-id="1be6f-200">El elemento **dependiente** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-200">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-201">PropertyRef (uno o varios)</span><span class="sxs-lookup"><span data-stu-id="1be6f-201">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="1be6f-202">Elementos Annotation (cero o más)</span><span class="sxs-lookup"><span data-stu-id="1be6f-202">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-203">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-203">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-204">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **dependiente** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-204">The following table describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="1be6f-205">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-205">Attribute Name</span></span> | <span data-ttu-id="1be6f-206">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-206">Is Required</span></span> | <span data-ttu-id="1be6f-207">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-207">Value</span></span>                                                                                                                                                       |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-208">**Rol**</span><span class="sxs-lookup"><span data-stu-id="1be6f-208">**Role**</span></span>       | <span data-ttu-id="1be6f-209">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-209">Yes</span></span>         | <span data-ttu-id="1be6f-210">El mismo valor que el atributo **role** (si se usa) del elemento end correspondiente; de lo contrario, el nombre de la tabla que contiene la columna de referencia.</span><span class="sxs-lookup"><span data-stu-id="1be6f-210">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referencing column.</span></span> |

> [!NOTE]
> <span data-ttu-id="1be6f-211">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **dependiente** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-211">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="1be6f-212">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-212">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="1be6f-213">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-213">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-214">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-214">Example</span></span>

<span data-ttu-id="1be6f-215">En el ejemplo siguiente se muestra un elemento Association que utiliza un elemento **ReferentialConstraint** para especificar las columnas que participan en la restricción de clave externa de **FK \_ CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-215">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="1be6f-216">El elemento **dependiente** especifica la columna **CustomerID** de la tabla **Order** como el extremo dependiente de la restricción.</span><span class="sxs-lookup"><span data-stu-id="1be6f-216">The **Dependent** element specifies the **CustomerId** column of the **Order** table as the dependent end of the constraint.</span></span>

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

## <a name="documentation-element-ssdl"></a><span data-ttu-id="1be6f-217">Documentation (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-217">Documentation Element (SSDL)</span></span>

<span data-ttu-id="1be6f-218">El elemento **Documentation** del lenguaje de definición de esquemas de almacenamiento (SSDL) se puede usar para proporcionar información sobre un objeto que se define en un elemento primario.</span><span class="sxs-lookup"><span data-stu-id="1be6f-218">The **Documentation** element in store schema definition language (SSDL) can be used to provide information about an object that is defined in a parent element.</span></span>

<span data-ttu-id="1be6f-219">El elemento **Documentation** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-219">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-220">**Summary**: breve descripción del elemento primario.</span><span class="sxs-lookup"><span data-stu-id="1be6f-220">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="1be6f-221">(cero o un elemento).</span><span class="sxs-lookup"><span data-stu-id="1be6f-221">(zero or one element)</span></span>
-   <span data-ttu-id="1be6f-222">**LongDescription**: una descripción amplia del elemento primario.</span><span class="sxs-lookup"><span data-stu-id="1be6f-222">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="1be6f-223">(cero o un elemento).</span><span class="sxs-lookup"><span data-stu-id="1be6f-223">(zero or one element)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-224">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-224">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-225">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Documentation** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-225">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="1be6f-226">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="1be6f-227">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-228">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-228">Example</span></span>

<span data-ttu-id="1be6f-229">En el ejemplo siguiente se muestra el elemento **Documentation** como un elemento secundario de un elemento EntityType.</span><span class="sxs-lookup"><span data-stu-id="1be6f-229">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span>

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

## <a name="end-element-ssdl"></a><span data-ttu-id="1be6f-230">End (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-230">End Element (SSDL)</span></span>

<span data-ttu-id="1be6f-231">El elemento **End** en el lenguaje de definición de esquemas de almacenamiento (SSDL) especifica la tabla y el número de filas de un extremo de una restricción FOREIGN KEY en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-231">The **End** element in store schema definition language (SSDL) specifies the table and number of rows at one end of a foreign key constraint in the underlying database.</span></span> <span data-ttu-id="1be6f-232">El elemento **End** puede ser un elemento secundario del elemento Association o del elemento AssociationSet.</span><span class="sxs-lookup"><span data-stu-id="1be6f-232">The **End** element can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="1be6f-233">En cada caso, los posibles elementos secundarios y atributos aplicables son diferentes.</span><span class="sxs-lookup"><span data-stu-id="1be6f-233">In each case, the possible child elements and applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="1be6f-234">Elemento End como elemento secundario del elemento Association</span><span class="sxs-lookup"><span data-stu-id="1be6f-234">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="1be6f-235">Un elemento **End** (como elemento secundario del elemento **Association** ) especifica la tabla y el número de filas al final de una restricción FOREIGN KEY con los atributos **Type** y **Multiplicity** , respectivamente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-235">An **End** element (as a child of the **Association** element) specifies the table and number of rows at the end of a foreign key constraint with the **Type** and **Multiplicity** attributes respectively.</span></span> <span data-ttu-id="1be6f-236">Los extremos de una restricción de clave externa se definen como parte de una asociación SSDL, la cual debe tener exactamente dos extremos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-236">Ends of a foreign key constraint are defined as part of an SSDL association; an SSDL association must have exactly two ends.</span></span>

<span data-ttu-id="1be6f-237">Un elemento **End** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-237">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-238">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="1be6f-238">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="1be6f-239">OnDelete (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="1be6f-239">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="1be6f-240">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="1be6f-240">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-241">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-241">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-242">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **End** cuando es el elemento secundario de un elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-242">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="1be6f-243">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-243">Attribute Name</span></span>   | <span data-ttu-id="1be6f-244">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-244">Is Required</span></span> | <span data-ttu-id="1be6f-245">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-245">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                      |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-246">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="1be6f-246">**Type**</span></span>         | <span data-ttu-id="1be6f-247">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-247">Yes</span></span>         | <span data-ttu-id="1be6f-248">El nombre completo del conjunto de entidades SSDL que está en el extremo de la restricción de clave externa.</span><span class="sxs-lookup"><span data-stu-id="1be6f-248">The fully qualified name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                                                                                                                                                                                                                                                                          |
| <span data-ttu-id="1be6f-249">**Rol**</span><span class="sxs-lookup"><span data-stu-id="1be6f-249">**Role**</span></span>         | <span data-ttu-id="1be6f-250">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-250">No</span></span>          | <span data-ttu-id="1be6f-251">El valor del atributo **role** en el elemento principal o dependiente del elemento ReferentialConstraint correspondiente (si se usa).</span><span class="sxs-lookup"><span data-stu-id="1be6f-251">The value of the **Role** attribute in either the Principal or Dependent element of the corresponding ReferentialConstraint element (if used).</span></span>                                                                                                                                                                                                                                             |
| <span data-ttu-id="1be6f-252">**Multiplicidad**</span><span class="sxs-lookup"><span data-stu-id="1be6f-252">**Multiplicity**</span></span> | <span data-ttu-id="1be6f-253">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-253">Yes</span></span>         | <span data-ttu-id="1be6f-254">**1**, **0.. 1**o, **\*** dependiendo del número de filas que puedan estar al final de la restricción FOREIGN KEY.</span><span class="sxs-lookup"><span data-stu-id="1be6f-254">**1**, **0..1**, or **\*** depending on the number of rows that can be at the end of the foreign key constraint.</span></span> <br/> <span data-ttu-id="1be6f-255">**1** indica que existe exactamente una fila en el extremo de la restricción de clave externa.</span><span class="sxs-lookup"><span data-stu-id="1be6f-255">**1** indicates that exactly one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="1be6f-256">**0.. 1** indica que hay cero o una fila en el extremo de la restricción de clave externa.</span><span class="sxs-lookup"><span data-stu-id="1be6f-256">**0..1** indicates that zero or one row exists at the foreign key constraint end.</span></span> <br/> <span data-ttu-id="1be6f-257">**\*** indica que hay cero, una o más filas en el extremo de la restricción de clave externa.</span><span class="sxs-lookup"><span data-stu-id="1be6f-257">**\*** indicates that zero, one, or more rows exist at the foreign key constraint end.</span></span> |

> [!NOTE]
> <span data-ttu-id="1be6f-258">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **final** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-258">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="1be6f-259">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-259">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="1be6f-260">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-260">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="1be6f-261">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-261">Example</span></span>

<span data-ttu-id="1be6f-262">En el ejemplo siguiente se muestra un elemento **Association** que define la restricción de clave externa de **FK \_ CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-262">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="1be6f-263">Los valores de **multiplicidad** especificados en cada elemento **final** indican que muchas filas de la tabla **Orders** se pueden asociar a una fila de la tabla **Customers** , pero solo se puede asociar una fila de la tabla **Customers** a una fila de la tabla **Orders** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-263">The **Multiplicity** values specified on each **End** element indicate that many rows in the **Orders** table can be associated with a row in the **Customers** table, but only one row in the **Customers** table can be associated with a row in the **Orders** table.</span></span> <span data-ttu-id="1be6f-264">Además, el elemento **aldelete** indica que todas las filas de la tabla **Orders** que hacen referencia a una fila determinada de la tabla **Customers** se eliminarán si se elimina la fila de la tabla **Customers** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-264">Additionally, the **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="1be6f-265">Elemento End como elemento secundario del elemento AssociationSet</span><span class="sxs-lookup"><span data-stu-id="1be6f-265">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="1be6f-266">El elemento **End** (como elemento secundario del elemento **AssociationSet** ) especifica una tabla en un extremo de una restricción FOREIGN KEY en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-266">The **End** element (as a child of the **AssociationSet** element) specifies a table at one end of a foreign key constraint in the underlying database.</span></span>

<span data-ttu-id="1be6f-267">Un elemento **End** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-267">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-268">Documentation (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-268">Documentation (zero or one)</span></span>
-   <span data-ttu-id="1be6f-269">Elementos Annotation (cero o más)</span><span class="sxs-lookup"><span data-stu-id="1be6f-269">Annotation elements (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-270">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-270">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-271">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **End** cuando es el elemento secundario de un elemento **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-271">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="1be6f-272">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-272">Attribute Name</span></span> | <span data-ttu-id="1be6f-273">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-273">Is Required</span></span> | <span data-ttu-id="1be6f-274">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-274">Value</span></span>                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-275">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="1be6f-275">**EntitySet**</span></span>  | <span data-ttu-id="1be6f-276">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-276">Yes</span></span>         | <span data-ttu-id="1be6f-277">El nombre del conjunto de entidades SSDL que está en el extremo de la restricción de clave externa.</span><span class="sxs-lookup"><span data-stu-id="1be6f-277">The name of the SSDL entity set that is at the end of the foreign key constraint.</span></span>                                      |
| <span data-ttu-id="1be6f-278">**Rol**</span><span class="sxs-lookup"><span data-stu-id="1be6f-278">**Role**</span></span>       | <span data-ttu-id="1be6f-279">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-279">No</span></span>          | <span data-ttu-id="1be6f-280">El valor de uno de los atributos de **rol** especificados en un elemento **final** del elemento Association correspondiente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-280">The value of one of the **Role** attributes specified on one **End** element of the corresponding Association element.</span></span> |

> [!NOTE]
> <span data-ttu-id="1be6f-281">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **final** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-281">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="1be6f-282">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-282">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="1be6f-283">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-283">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

#### <a name="example"></a><span data-ttu-id="1be6f-284">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-284">Example</span></span>

<span data-ttu-id="1be6f-285">En el ejemplo siguiente se muestra un elemento **EntityContainer** con un elemento **AssociationSet** con dos elementos **End** :</span><span class="sxs-lookup"><span data-stu-id="1be6f-285">The following example shows an **EntityContainer** element with an **AssociationSet** element with two **End** elements:</span></span>

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

## <a name="entitycontainer-element-ssdl"></a><span data-ttu-id="1be6f-286">EntityContainer (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-286">EntityContainer Element (SSDL)</span></span>

<span data-ttu-id="1be6f-287">Un elemento **EntityContainer** en el lenguaje de definición de esquemas de almacenamiento (SSDL) describe la estructura del origen de datos subyacente en una aplicación Entity Framework: los conjuntos de entidades SSDL (definidos en elementos EntitySet) representan las tablas de una base de datos, los tipos de entidad SSDL (definidos en los elementos EntityType) representan las filas de una tabla, y los conjuntos de asociaciones (definidos en los elementos AssociationSet)</span><span class="sxs-lookup"><span data-stu-id="1be6f-287">An **EntityContainer** element in store schema definition language (SSDL) describes the structure of the underlying data source in an Entity Framework application: SSDL entity sets (defined in EntitySet elements) represent tables in a database, SSDL entity types (defined in EntityType elements) represent rows in a table, and association sets (defined in AssociationSet elements) represent foreign key constraints in a database.</span></span> <span data-ttu-id="1be6f-288">Un contenedor de entidades del modelo de almacenamiento se asigna a un contenedor de entidades del modelo de conceptual a través del elemento EntityContainerMapping.</span><span class="sxs-lookup"><span data-stu-id="1be6f-288">A storage model entity container maps to a conceptual model entity container through the EntityContainerMapping element.</span></span>

<span data-ttu-id="1be6f-289">Un elemento **EntityContainer** puede tener cero o un elemento de documentación.</span><span class="sxs-lookup"><span data-stu-id="1be6f-289">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="1be6f-290">Si hay un elemento de **documentación** , debe preceder a todos los demás elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="1be6f-290">If a **Documentation** element is present, it must precede all other child elements.</span></span>

<span data-ttu-id="1be6f-291">Un elemento **EntityContainer** puede tener cero o más de los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-291">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-292">EntitySet</span><span class="sxs-lookup"><span data-stu-id="1be6f-292">EntitySet</span></span>
-   <span data-ttu-id="1be6f-293">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="1be6f-293">AssociationSet</span></span>
-   <span data-ttu-id="1be6f-294">Elementos Annotation</span><span class="sxs-lookup"><span data-stu-id="1be6f-294">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-295">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-295">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-296">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-296">The table below describes the attributes that can be applied to the **EntityContainer** element.</span></span>

| <span data-ttu-id="1be6f-297">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-297">Attribute Name</span></span> | <span data-ttu-id="1be6f-298">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-298">Is Required</span></span> | <span data-ttu-id="1be6f-299">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-299">Value</span></span>                                                                   |
|:---------------|:------------|:------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-300">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="1be6f-300">**Name**</span></span>       | <span data-ttu-id="1be6f-301">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-301">Yes</span></span>         | <span data-ttu-id="1be6f-302">Nombre del contenedor de entidades.</span><span class="sxs-lookup"><span data-stu-id="1be6f-302">The name of the entity container.</span></span> <span data-ttu-id="1be6f-303">Este nombre no puede contener puntos (.).</span><span class="sxs-lookup"><span data-stu-id="1be6f-303">This name cannot contain periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="1be6f-304">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-304">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="1be6f-305">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-305">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="1be6f-306">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-306">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-307">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-307">Example</span></span>

<span data-ttu-id="1be6f-308">En el ejemplo siguiente se muestra un elemento **EntityContainer** que define dos conjuntos de entidades y un conjunto de asociaciones.</span><span class="sxs-lookup"><span data-stu-id="1be6f-308">The following example shows an **EntityContainer** element that defines two entity sets and one association set.</span></span> <span data-ttu-id="1be6f-309">Observe que los nombres de los tipos de entidad y tipos de asociación están calificados mediante el nombre del espacio de nombres del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="1be6f-309">Note that entity type and association type names are qualified by the conceptual model namespace name.</span></span>

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

## <a name="entityset-element-ssdl"></a><span data-ttu-id="1be6f-310">EntitySet (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-310">EntitySet Element (SSDL)</span></span>

<span data-ttu-id="1be6f-311">Un elemento **EntitySet** en el lenguaje de definición de esquemas de almacenamiento (SSDL) representa una tabla o una vista en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-311">An **EntitySet** element in store schema definition language (SSDL) represents a table or view in the underlying database.</span></span> <span data-ttu-id="1be6f-312">Un elemento EntityType de SSDL representa una fila de la tabla o vista.</span><span class="sxs-lookup"><span data-stu-id="1be6f-312">An EntityType element in SSDL represents a row in the table or view.</span></span> <span data-ttu-id="1be6f-313">El atributo **EntityType** de un elemento **EntitySet** especifica el tipo de entidad SSDL determinado que representa las filas de un conjunto de entidades SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-313">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="1be6f-314">La asignación entre un conjunto de entidades CSDL y un conjunto de entidades SSDL se especifica en un elemento EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="1be6f-314">The mapping between a CSDL entity set and an SSDL entity set is specified in an EntitySetMapping element.</span></span>

<span data-ttu-id="1be6f-315">El elemento **EntitySet** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-315">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-316">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="1be6f-316">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="1be6f-317">DefiningQuery (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="1be6f-317">DefiningQuery (zero or one element)</span></span>
-   <span data-ttu-id="1be6f-318">Elementos Annotation</span><span class="sxs-lookup"><span data-stu-id="1be6f-318">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-319">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-319">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-320">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-320">The following table describes the attributes that can be applied to the **EntitySet** element.</span></span>

> [!NOTE]
> <span data-ttu-id="1be6f-321">Algunos atributos (que no se enumeran aquí) pueden estar calificados con el alias de **almacén** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-321">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="1be6f-322">El Asistente para actualizar modelo utiliza estos atributos al actualizar un modelo.</span><span class="sxs-lookup"><span data-stu-id="1be6f-322">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="1be6f-323">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-323">Attribute Name</span></span> | <span data-ttu-id="1be6f-324">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-324">Is Required</span></span> | <span data-ttu-id="1be6f-325">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-325">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-326">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="1be6f-326">**Name**</span></span>       | <span data-ttu-id="1be6f-327">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-327">Yes</span></span>         | <span data-ttu-id="1be6f-328">Nombre del conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="1be6f-328">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="1be6f-329">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="1be6f-329">**EntityType**</span></span> | <span data-ttu-id="1be6f-330">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-330">Yes</span></span>         | <span data-ttu-id="1be6f-331">El nombre completo del tipo de entidad para el que el conjunto de entidades contiene las instancias.</span><span class="sxs-lookup"><span data-stu-id="1be6f-331">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |
| <span data-ttu-id="1be6f-332">**Esquema**</span><span class="sxs-lookup"><span data-stu-id="1be6f-332">**Schema**</span></span>     | <span data-ttu-id="1be6f-333">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-333">No</span></span>          | <span data-ttu-id="1be6f-334">El esquema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-334">The database schema.</span></span>                                                                     |
| <span data-ttu-id="1be6f-335">**Table**</span><span class="sxs-lookup"><span data-stu-id="1be6f-335">**Table**</span></span>      | <span data-ttu-id="1be6f-336">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-336">No</span></span>          | <span data-ttu-id="1be6f-337">La tabla de base de datos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-337">The database table.</span></span>                                                                      |

> [!NOTE]
> <span data-ttu-id="1be6f-338">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-338">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="1be6f-339">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-339">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="1be6f-340">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-340">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-341">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-341">Example</span></span>

<span data-ttu-id="1be6f-342">En el ejemplo siguiente se muestra un elemento **EntityContainer** que tiene dos elementos **EntitySet** y un elemento **AssociationSet** :</span><span class="sxs-lookup"><span data-stu-id="1be6f-342">The following example shows an **EntityContainer** element that has two **EntitySet** elements and one **AssociationSet** element:</span></span>

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

## <a name="entitytype-element-ssdl"></a><span data-ttu-id="1be6f-343">EntityType (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-343">EntityType Element (SSDL)</span></span>

<span data-ttu-id="1be6f-344">Un elemento **EntityType** en el lenguaje de definición de esquemas de almacenamiento (SSDL) representa una fila de una tabla o vista de la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-344">An **EntityType** element in store schema definition language (SSDL) represents a row in a table or view of the underlying database.</span></span> <span data-ttu-id="1be6f-345">Un elemento EntitySet de SSDL representa la tabla o vista donde están las filas.</span><span class="sxs-lookup"><span data-stu-id="1be6f-345">An EntitySet element in SSDL represents the table or view in which rows occur.</span></span> <span data-ttu-id="1be6f-346">El atributo **EntityType** de un elemento **EntitySet** especifica el tipo de entidad SSDL determinado que representa las filas de un conjunto de entidades SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-346">The **EntityType** attribute of an **EntitySet** element specifies the particular SSDL entity type that represents rows in an SSDL entity set.</span></span> <span data-ttu-id="1be6f-347">La asignación entre un tipo de entidad SSDL y un tipo de entidad CSDL se especifica en un elemento EntityTypeMapping.</span><span class="sxs-lookup"><span data-stu-id="1be6f-347">The mapping between an SSDL entity type and a CSDL entity type is specified in an EntityTypeMapping element.</span></span>

<span data-ttu-id="1be6f-348">El elemento **EntityType** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-348">The **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-349">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="1be6f-349">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="1be6f-350">Key (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="1be6f-350">Key (zero or one element)</span></span>
-   <span data-ttu-id="1be6f-351">Elementos Annotation</span><span class="sxs-lookup"><span data-stu-id="1be6f-351">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-352">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-352">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-353">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-353">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="1be6f-354">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-354">Attribute Name</span></span> | <span data-ttu-id="1be6f-355">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-355">Is Required</span></span> | <span data-ttu-id="1be6f-356">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-356">Value</span></span>                                                                                                                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-357">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="1be6f-357">**Name**</span></span>       | <span data-ttu-id="1be6f-358">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-358">Yes</span></span>         | <span data-ttu-id="1be6f-359">El nombre del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="1be6f-359">The name of the entity type.</span></span> <span data-ttu-id="1be6f-360">Este valor normalmente es igual que el nombre de la tabla en la que el tipo de entidad representa una fila.</span><span class="sxs-lookup"><span data-stu-id="1be6f-360">This value is usually the same as the name of the table in which the entity type represents a row.</span></span> <span data-ttu-id="1be6f-361">Este valor no puede contener ningún punto (.).</span><span class="sxs-lookup"><span data-stu-id="1be6f-361">This value can contain no periods (.).</span></span> |

> [!NOTE]
> <span data-ttu-id="1be6f-362">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-362">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="1be6f-363">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-363">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="1be6f-364">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-364">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-365">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-365">Example</span></span>

<span data-ttu-id="1be6f-366">En el ejemplo siguiente se muestra un elemento **EntityType** con dos propiedades:</span><span class="sxs-lookup"><span data-stu-id="1be6f-366">The following example shows an **EntityType** element with two properties:</span></span>

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

## <a name="function-element-ssdl"></a><span data-ttu-id="1be6f-367">Function (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-367">Function Element (SSDL)</span></span>

<span data-ttu-id="1be6f-368">El elemento **function** del lenguaje de definición de esquemas de almacenamiento (SSDL) especifica un procedimiento almacenado que existe en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-368">The **Function** element in store schema definition language (SSDL) specifies a stored procedure that exists in the underlying database.</span></span>

<span data-ttu-id="1be6f-369">El elemento **function** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-369">The **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-370">Documentation (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-370">Documentation (zero or one)</span></span>
-   <span data-ttu-id="1be6f-371">Parámetro (cero o más)</span><span class="sxs-lookup"><span data-stu-id="1be6f-371">Parameter (zero or more)</span></span>
-   <span data-ttu-id="1be6f-372">CommandText (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-372">CommandText (zero or one)</span></span>
-   <span data-ttu-id="1be6f-373">ReturnType (cero o más)</span><span class="sxs-lookup"><span data-stu-id="1be6f-373">ReturnType (zero or more)</span></span>
-   <span data-ttu-id="1be6f-374">Elementos Annotation (cero o más)</span><span class="sxs-lookup"><span data-stu-id="1be6f-374">Annotation elements (zero or more)</span></span>

<span data-ttu-id="1be6f-375">Un tipo de valor devuelto para una función debe especificarse con el elemento **ReturnType** o el atributo **ReturnType** (consulte a continuación), pero no ambos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-375">A return type for a function must be specified with either the **ReturnType** element or the **ReturnType** attribute (see below), but not both.</span></span>

<span data-ttu-id="1be6f-376">Los procedimientos almacenados que se especifican en el modelo de almacenamiento se pueden importar en el modelo conceptual de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="1be6f-376">Stored procedures that are specified in the storage model can be imported into the conceptual model of an application.</span></span> <span data-ttu-id="1be6f-377">Para obtener más información, vea [consultar con procedimientos almacenados](xref:ef6/modeling/designer/stored-procedures/query).</span><span class="sxs-lookup"><span data-stu-id="1be6f-377">For more information, see [Querying with Stored Procedures](xref:ef6/modeling/designer/stored-procedures/query).</span></span> <span data-ttu-id="1be6f-378">El elemento de **función** también se puede utilizar para definir funciones personalizadas en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1be6f-378">The **Function** element can also be used to define custom functions in the storage model.</span></span>  

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-379">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-379">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-380">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento de **función** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-380">The following table describes the attributes that can be applied to the **Function** element.</span></span>

> [!NOTE]
> <span data-ttu-id="1be6f-381">Algunos atributos (que no se enumeran aquí) pueden estar calificados con el alias de **almacén** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-381">Some attributes (not listed here) may be qualified with the **store** alias.</span></span> <span data-ttu-id="1be6f-382">El Asistente para actualizar modelo utiliza estos atributos al actualizar un modelo.</span><span class="sxs-lookup"><span data-stu-id="1be6f-382">These attributes are used by the Update Model Wizard when updating a model.</span></span>

| <span data-ttu-id="1be6f-383">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-383">Attribute Name</span></span>             | <span data-ttu-id="1be6f-384">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-384">Is Required</span></span> | <span data-ttu-id="1be6f-385">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-385">Value</span></span>                                                                                                                                                                                                              |
|:---------------------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-386">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="1be6f-386">**Name**</span></span>                   | <span data-ttu-id="1be6f-387">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-387">Yes</span></span>         | <span data-ttu-id="1be6f-388">Nombre del procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="1be6f-388">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="1be6f-389">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="1be6f-389">**ReturnType**</span></span>             | <span data-ttu-id="1be6f-390">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-390">No</span></span>          | <span data-ttu-id="1be6f-391">El tipo de valor devuelto del procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="1be6f-391">The return type of the stored procedure.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="1be6f-392">**Agregada**</span><span class="sxs-lookup"><span data-stu-id="1be6f-392">**Aggregate**</span></span>              | <span data-ttu-id="1be6f-393">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-393">No</span></span>          | <span data-ttu-id="1be6f-394">**True** si el procedimiento almacenado devuelve un valor agregado; en caso contrario, **false**.</span><span class="sxs-lookup"><span data-stu-id="1be6f-394">**True** if the stored procedure returns an aggregate value; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="1be6f-395">**BuiltIn**</span><span class="sxs-lookup"><span data-stu-id="1be6f-395">**BuiltIn**</span></span>                | <span data-ttu-id="1be6f-396">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-396">No</span></span>          | <span data-ttu-id="1be6f-397">**True** si la función es una función integrada<sup>1</sup> ; en caso contrario, **false**.</span><span class="sxs-lookup"><span data-stu-id="1be6f-397">**True** if the function is a built-in<sup>1</sup> function; otherwise **False**.</span></span>                                                                                                                                  |
| <span data-ttu-id="1be6f-398">**StoreFunctionName**</span><span class="sxs-lookup"><span data-stu-id="1be6f-398">**StoreFunctionName**</span></span>      | <span data-ttu-id="1be6f-399">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-399">No</span></span>          | <span data-ttu-id="1be6f-400">Nombre del procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="1be6f-400">The name of the stored procedure.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="1be6f-401">**NiladicFunction**</span><span class="sxs-lookup"><span data-stu-id="1be6f-401">**NiladicFunction**</span></span>        | <span data-ttu-id="1be6f-402">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-402">No</span></span>          | <span data-ttu-id="1be6f-403">**True** si la función es una función niládicas<sup>2</sup> ; De lo contrario, **false** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-403">**True** if the function is a niladic<sup>2</sup> function; **False** otherwise.</span></span>                                                                                                                                   |
| <span data-ttu-id="1be6f-404">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="1be6f-404">**IsComposable**</span></span>           | <span data-ttu-id="1be6f-405">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-405">No</span></span>          | <span data-ttu-id="1be6f-406">**True** si la función es una función que admite composición<sup>3</sup> ; De lo contrario, **false** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-406">**True** if the function is a composable<sup>3</sup> function; **False** otherwise.</span></span>                                                                                                                                |
| <span data-ttu-id="1be6f-407">**ParameterTypeSemantics**</span><span class="sxs-lookup"><span data-stu-id="1be6f-407">**ParameterTypeSemantics**</span></span> | <span data-ttu-id="1be6f-408">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-408">No</span></span>          | <span data-ttu-id="1be6f-409">La enumeración que define la semántica de tipos que se utiliza para resolver sobrecargas de función.</span><span class="sxs-lookup"><span data-stu-id="1be6f-409">The enumeration that defines the type semantics used to resolve function overloads.</span></span> <span data-ttu-id="1be6f-410">La enumeración se define en el manifiesto del proveedor por cada definición de función.</span><span class="sxs-lookup"><span data-stu-id="1be6f-410">The enumeration is defined in the provider manifest per function definition.</span></span> <span data-ttu-id="1be6f-411">El valor predeterminado es **AllowImplicitConversion**.</span><span class="sxs-lookup"><span data-stu-id="1be6f-411">The default value is **AllowImplicitConversion**.</span></span> |
| <span data-ttu-id="1be6f-412">**Esquema**</span><span class="sxs-lookup"><span data-stu-id="1be6f-412">**Schema**</span></span>                 | <span data-ttu-id="1be6f-413">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-413">No</span></span>          | <span data-ttu-id="1be6f-414">El nombre del esquema donde se define el procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="1be6f-414">The name of the schema in which the stored procedure is defined.</span></span>                                                                                                                                                   |

<span data-ttu-id="1be6f-415"><sup>1</sup> una función integrada es una función que se define en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-415"><sup>1</sup> A built-in function is a function that is defined in the database.</span></span> <span data-ttu-id="1be6f-416">Para obtener información sobre las funciones que se definen en el modelo de almacenamiento, vea CommandText (elemento) (SSDL).</span><span class="sxs-lookup"><span data-stu-id="1be6f-416">For information about functions that are defined in the storage model, see CommandText Element (SSDL).</span></span>

<span data-ttu-id="1be6f-417"><sup>2</sup> una función niládicas es una función que no acepta parámetros y, cuando se llama, no requiere paréntesis.</span><span class="sxs-lookup"><span data-stu-id="1be6f-417"><sup>2</sup> A niladic function is a function that accepts no parameters and, when called, does not require parentheses.</span></span>

<span data-ttu-id="1be6f-418"><sup>3</sup> dos funciones son comparables si la salida de una función puede ser la entrada para la otra función.</span><span class="sxs-lookup"><span data-stu-id="1be6f-418"><sup>3</sup> Two functions are composable if the output of one function can be the input for the other function.</span></span>

> [!NOTE]
> <span data-ttu-id="1be6f-419">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento de **función** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-419">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="1be6f-420">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-420">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="1be6f-421">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-421">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-422">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-422">Example</span></span>

<span data-ttu-id="1be6f-423">En el ejemplo siguiente se muestra un elemento **function** que corresponde al procedimiento almacenado **UpdateOrderQuantity** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-423">The following example shows a **Function** element that corresponds to the **UpdateOrderQuantity** stored procedure.</span></span> <span data-ttu-id="1be6f-424">El procedimiento almacenado acepta dos parámetros y no devuelve ningún valor.</span><span class="sxs-lookup"><span data-stu-id="1be6f-424">The stored procedure accepts two parameters and does not return a value.</span></span>

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

## <a name="key-element-ssdl"></a><span data-ttu-id="1be6f-425">Key (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-425">Key Element (SSDL)</span></span>

<span data-ttu-id="1be6f-426">El elemento **key** del lenguaje de definición de esquemas de almacenamiento (SSDL) representa la clave principal de una tabla en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-426">The **Key** element in store schema definition language (SSDL) represents the primary key of a table in the underlying database.</span></span> <span data-ttu-id="1be6f-427">**Key** es un elemento secundario de un elemento EntityType, que representa una fila de una tabla.</span><span class="sxs-lookup"><span data-stu-id="1be6f-427">**Key** is a child element of an EntityType element, which represents a row in a table.</span></span> <span data-ttu-id="1be6f-428">La clave principal se define en el elemento **key** haciendo referencia a uno o varios elementos de propiedad que se definen en el elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-428">The primary key is defined in the **Key** element by referencing one or more Property elements that are defined on the **EntityType** element.</span></span>

<span data-ttu-id="1be6f-429">El elemento **key** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-429">The **Key** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-430">PropertyRef (uno o varios)</span><span class="sxs-lookup"><span data-stu-id="1be6f-430">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="1be6f-431">Elementos Annotation</span><span class="sxs-lookup"><span data-stu-id="1be6f-431">Annotation elements</span></span>

<span data-ttu-id="1be6f-432">No hay atributos aplicables al elemento **key** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-432">No attributes are applicable to the **Key** element.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-433">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-433">Example</span></span>

<span data-ttu-id="1be6f-434">En el ejemplo siguiente se muestra un elemento **EntityType** con una clave que hace referencia a una propiedad:</span><span class="sxs-lookup"><span data-stu-id="1be6f-434">The following example shows an **EntityType** element with a key that references one property:</span></span>

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

## <a name="ondelete-element-ssdl"></a><span data-ttu-id="1be6f-435">OnDelete (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-435">OnDelete Element (SSDL)</span></span>

<span data-ttu-id="1be6f-436">El elemento **aldelete** del lenguaje de definición de esquemas de almacenamiento (SSDL) refleja el comportamiento de la base de datos cuando se elimina una fila que participa en una restricción FOREIGN KEY.</span><span class="sxs-lookup"><span data-stu-id="1be6f-436">The **OnDelete** element in store schema definition language (SSDL) reflects the database behavior when a row that participates in a foreign key constraint is deleted.</span></span> <span data-ttu-id="1be6f-437">Si la acción se establece en **Cascade**, también se eliminarán las filas que hacen referencia a una fila que se va a eliminar.</span><span class="sxs-lookup"><span data-stu-id="1be6f-437">If the action is set to **Cascade**, then rows that reference a row that is being deleted will also be deleted.</span></span> <span data-ttu-id="1be6f-438">Si la acción se establece en **None**, las filas que hacen referencia a una fila que se va a eliminar también no se eliminan.</span><span class="sxs-lookup"><span data-stu-id="1be6f-438">If the action is set to **None**, then rows that reference a row that is being deleted are not also deleted.</span></span> <span data-ttu-id="1be6f-439">Un elemento **aldelete** es un elemento secundario de un elemento end.</span><span class="sxs-lookup"><span data-stu-id="1be6f-439">An **OnDelete** element is a child element of an End element.</span></span>

<span data-ttu-id="1be6f-440">Un elemento **aldelete** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-440">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-441">Documentation (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-441">Documentation (zero or one)</span></span>
-   <span data-ttu-id="1be6f-442">Elementos Annotation (cero o más)</span><span class="sxs-lookup"><span data-stu-id="1be6f-442">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-443">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-443">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-444">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **aleliminar** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-444">The following table describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="1be6f-445">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-445">Attribute Name</span></span> | <span data-ttu-id="1be6f-446">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-446">Is Required</span></span> | <span data-ttu-id="1be6f-447">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-447">Value</span></span>                                                                                               |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-448">**Acción**</span><span class="sxs-lookup"><span data-stu-id="1be6f-448">**Action**</span></span>     | <span data-ttu-id="1be6f-449">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-449">Yes</span></span>         | <span data-ttu-id="1be6f-450">**Cascade** o **None**.</span><span class="sxs-lookup"><span data-stu-id="1be6f-450">**Cascade** or **None**.</span></span> <span data-ttu-id="1be6f-451">(El valor **restringido** es válido, pero tiene el mismo comportamiento que **ninguno**).</span><span class="sxs-lookup"><span data-stu-id="1be6f-451">(The value **Restricted** is valid but has the same behavior as **None**.)</span></span> |

> [!NOTE]
> <span data-ttu-id="1be6f-452">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **aleliminar** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-452">Any number of annotation attributes (custom XML attributes) may be applied to the **OnDelete** element.</span></span> <span data-ttu-id="1be6f-453">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-453">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="1be6f-454">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-454">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-455">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-455">Example</span></span>

<span data-ttu-id="1be6f-456">En el ejemplo siguiente se muestra un elemento **Association** que define la restricción de clave externa de **FK \_ CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-456">The following example shows an **Association** element that defines the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="1be6f-457">El elemento **aldelete** indica que se eliminarán todas las filas de la tabla **Orders** que hagan referencia a una fila determinada de la tabla **Customers** si se elimina la fila de la tabla **Customers** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-457">The **OnDelete** element indicates that all rows in the **Orders** table that reference a particular row in the **Customers** table will be deleted if the row in the **Customers** table is deleted.</span></span>

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

## <a name="parameter-element-ssdl"></a><span data-ttu-id="1be6f-458">Parameter (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-458">Parameter Element (SSDL)</span></span>

<span data-ttu-id="1be6f-459">El elemento **Parameter** del lenguaje de definición de esquemas de almacenamiento (SSDL) es un elemento secundario del elemento function que especifica los parámetros de un procedimiento almacenado en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-459">The **Parameter** element in store schema definition language (SSDL) is a child of the Function element that specifies parameters for a stored procedure in the database.</span></span>

<span data-ttu-id="1be6f-460">El elemento **Parameter** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-460">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-461">Documentation (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-461">Documentation (zero or one)</span></span>
-   <span data-ttu-id="1be6f-462">Elementos Annotation (cero o más)</span><span class="sxs-lookup"><span data-stu-id="1be6f-462">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-463">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-463">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-464">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-464">The table below describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="1be6f-465">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-465">Attribute Name</span></span> | <span data-ttu-id="1be6f-466">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-466">Is Required</span></span> | <span data-ttu-id="1be6f-467">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-467">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-468">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="1be6f-468">**Name**</span></span>       | <span data-ttu-id="1be6f-469">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-469">Yes</span></span>         | <span data-ttu-id="1be6f-470">Nombre del parámetro.</span><span class="sxs-lookup"><span data-stu-id="1be6f-470">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="1be6f-471">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="1be6f-471">**Type**</span></span>       | <span data-ttu-id="1be6f-472">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-472">Yes</span></span>         | <span data-ttu-id="1be6f-473">El tipo de parámetro.</span><span class="sxs-lookup"><span data-stu-id="1be6f-473">The parameter type.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="1be6f-474">**Modo**</span><span class="sxs-lookup"><span data-stu-id="1be6f-474">**Mode**</span></span>       | <span data-ttu-id="1be6f-475">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-475">No</span></span>          | <span data-ttu-id="1be6f-476">**In**, **out**o **INOUT** dependiendo de si el parámetro es un parámetro de entrada, de salida o de entrada/salida.</span><span class="sxs-lookup"><span data-stu-id="1be6f-476">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="1be6f-477">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="1be6f-477">**MaxLength**</span></span>  | <span data-ttu-id="1be6f-478">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-478">No</span></span>          | <span data-ttu-id="1be6f-479">Longitud máxima permitida del parámetro.</span><span class="sxs-lookup"><span data-stu-id="1be6f-479">The maximum length of the parameter.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="1be6f-480">**Precisión**</span><span class="sxs-lookup"><span data-stu-id="1be6f-480">**Precision**</span></span>  | <span data-ttu-id="1be6f-481">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-481">No</span></span>          | <span data-ttu-id="1be6f-482">La precisión del parámetro.</span><span class="sxs-lookup"><span data-stu-id="1be6f-482">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="1be6f-483">**Escala**</span><span class="sxs-lookup"><span data-stu-id="1be6f-483">**Scale**</span></span>      | <span data-ttu-id="1be6f-484">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-484">No</span></span>          | <span data-ttu-id="1be6f-485">La escala del parámetro.</span><span class="sxs-lookup"><span data-stu-id="1be6f-485">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="1be6f-486">**SRID**</span><span class="sxs-lookup"><span data-stu-id="1be6f-486">**SRID**</span></span>       | <span data-ttu-id="1be6f-487">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-487">No</span></span>          | <span data-ttu-id="1be6f-488">Identificador de referencia del sistema espacial.</span><span class="sxs-lookup"><span data-stu-id="1be6f-488">Spatial System Reference Identifier.</span></span> <span data-ttu-id="1be6f-489">Válido solo para los parámetros de los tipos espaciales.</span><span class="sxs-lookup"><span data-stu-id="1be6f-489">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="1be6f-490">Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="1be6f-490">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

> [!NOTE]
> <span data-ttu-id="1be6f-491">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-491">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="1be6f-492">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-492">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="1be6f-493">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-493">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-494">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-494">Example</span></span>

<span data-ttu-id="1be6f-495">En el ejemplo siguiente se muestra un elemento **function** que tiene dos elementos **Parameter** que especifican parámetros de entrada:</span><span class="sxs-lookup"><span data-stu-id="1be6f-495">The following example shows a **Function** element that has two **Parameter** elements that specify input parameters:</span></span>

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

## <a name="principal-element-ssdl"></a><span data-ttu-id="1be6f-496">Principal (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-496">Principal Element (SSDL)</span></span>

<span data-ttu-id="1be6f-497">El elemento **principal** del lenguaje de definición de esquemas de almacenamiento (SSDL) es un elemento secundario del elemento ReferentialConstraint que define el extremo principal de una restricción FOREIGN KEY (también denominada restricción referencial).</span><span class="sxs-lookup"><span data-stu-id="1be6f-497">The **Principal** element in store schema definition language (SSDL) is a child element to the ReferentialConstraint element that defines the principal end of a foreign key constraint (also called a referential constraint).</span></span> <span data-ttu-id="1be6f-498">El elemento **principal** especifica la columna (o columnas) de clave principal de una tabla a la que hace referencia otra columna (o columnas).</span><span class="sxs-lookup"><span data-stu-id="1be6f-498">The **Principal** element specifies the primary key column (or columns) in a table that is referenced by another column (or columns).</span></span> <span data-ttu-id="1be6f-499">Los elementos **PropertyRef** especifican a qué columnas se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="1be6f-499">**PropertyRef** elements specify which columns are referenced.</span></span> <span data-ttu-id="1be6f-500">El elemento dependiente especifica las columnas que hacen referencia a las columnas de clave principal que se especifican en el elemento **principal** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-500">The Dependent element specifies columns that reference the primary key columns that are specified in the **Principal** element.</span></span>

<span data-ttu-id="1be6f-501">El elemento **principal** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="1be6f-501">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="1be6f-502">PropertyRef (uno o varios)</span><span class="sxs-lookup"><span data-stu-id="1be6f-502">PropertyRef (one or more)</span></span>
-   <span data-ttu-id="1be6f-503">Elementos Annotation (cero o más)</span><span class="sxs-lookup"><span data-stu-id="1be6f-503">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-504">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-504">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-505">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **principal** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-505">The following table describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="1be6f-506">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-506">Attribute Name</span></span> | <span data-ttu-id="1be6f-507">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-507">Is Required</span></span> | <span data-ttu-id="1be6f-508">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-508">Value</span></span>                                                                                                                                                      |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-509">**Rol**</span><span class="sxs-lookup"><span data-stu-id="1be6f-509">**Role**</span></span>       | <span data-ttu-id="1be6f-510">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-510">Yes</span></span>         | <span data-ttu-id="1be6f-511">El mismo valor que el atributo **role** (si se usa) del elemento end correspondiente; de lo contrario, el nombre de la tabla que contiene la columna a la que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="1be6f-511">The same value as the **Role** attribute (if used) of the corresponding End element; otherwise, the name of the table that contains the referenced column.</span></span> |

> [!NOTE]
> <span data-ttu-id="1be6f-512">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **principal** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-512">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="1be6f-513">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-513">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="1be6f-514">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-514">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-515">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-515">Example</span></span>

<span data-ttu-id="1be6f-516">En el ejemplo siguiente se muestra un elemento Association que utiliza un elemento **ReferentialConstraint** para especificar las columnas que participan en la restricción de clave externa de **FK \_ CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-516">The following example shows an Association element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint.</span></span> <span data-ttu-id="1be6f-517">El elemento **principal** especifica la columna **CustomerID** de la tabla **Customer** como el extremo principal de la restricción.</span><span class="sxs-lookup"><span data-stu-id="1be6f-517">The **Principal** element specifies the **CustomerId** column of the **Customer** table as the principal end of the constraint.</span></span>

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

## <a name="property-element-ssdl"></a><span data-ttu-id="1be6f-518">Property (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-518">Property Element (SSDL)</span></span>

<span data-ttu-id="1be6f-519">El elemento **Property** del lenguaje de definición de esquemas de almacenamiento (SSDL) representa una columna de una tabla en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-519">The **Property** element in store schema definition language (SSDL) represents a column in a table in the underlying database.</span></span> <span data-ttu-id="1be6f-520">Los elementos de **propiedad** son elementos secundarios de elementos EntityType, que representan las filas de una tabla.</span><span class="sxs-lookup"><span data-stu-id="1be6f-520">**Property** elements are children of EntityType elements, which represent rows in a table.</span></span> <span data-ttu-id="1be6f-521">Cada elemento de **propiedad** definido en un elemento **EntityType** representa una columna.</span><span class="sxs-lookup"><span data-stu-id="1be6f-521">Each **Property** element defined on an **EntityType** element represents a column.</span></span>

<span data-ttu-id="1be6f-522">Un elemento **Property** no puede tener elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="1be6f-522">A **Property** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-523">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-523">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-524">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-524">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="1be6f-525">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-525">Attribute Name</span></span>            | <span data-ttu-id="1be6f-526">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-526">Is Required</span></span> | <span data-ttu-id="1be6f-527">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-527">Value</span></span>                                                                                                                                                                                                                           |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-528">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="1be6f-528">**Name**</span></span>                  | <span data-ttu-id="1be6f-529">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-529">Yes</span></span>         | <span data-ttu-id="1be6f-530">El nombre de la columna correspondiente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-530">The name of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="1be6f-531">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="1be6f-531">**Type**</span></span>                  | <span data-ttu-id="1be6f-532">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-532">Yes</span></span>         | <span data-ttu-id="1be6f-533">El tipo de la columna correspondiente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-533">The type of the corresponding column.</span></span>                                                                                                                                                                                           |
| <span data-ttu-id="1be6f-534">**Admisión de valores NULL**</span><span class="sxs-lookup"><span data-stu-id="1be6f-534">**Nullable**</span></span>              | <span data-ttu-id="1be6f-535">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-535">No</span></span>          | <span data-ttu-id="1be6f-536">**True** (valor predeterminado) o **false** , dependiendo de si la columna correspondiente puede tener un valor null.</span><span class="sxs-lookup"><span data-stu-id="1be6f-536">**True** (the default value) or **False** depending on whether the corresponding column can have a null value.</span></span>                                                                                                                  |
| <span data-ttu-id="1be6f-537">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="1be6f-537">**DefaultValue**</span></span>          | <span data-ttu-id="1be6f-538">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-538">No</span></span>          | <span data-ttu-id="1be6f-539">El valor predeterminado de la columna correspondiente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-539">The default value of the corresponding column.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="1be6f-540">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="1be6f-540">**MaxLength**</span></span>             | <span data-ttu-id="1be6f-541">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-541">No</span></span>          | <span data-ttu-id="1be6f-542">La longitud máxima de la columna correspondiente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-542">The maximum length of the corresponding column.</span></span>                                                                                                                                                                                 |
| <span data-ttu-id="1be6f-543">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="1be6f-543">**FixedLength**</span></span>           | <span data-ttu-id="1be6f-544">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-544">No</span></span>          | <span data-ttu-id="1be6f-545">**True** o **false** , dependiendo de si el valor de columna correspondiente se almacenará como una cadena de longitud fija.</span><span class="sxs-lookup"><span data-stu-id="1be6f-545">**True** or **False** depending on whether the corresponding column value will be stored as a fixed length string.</span></span>                                                                                                              |
| <span data-ttu-id="1be6f-546">**Precisión**</span><span class="sxs-lookup"><span data-stu-id="1be6f-546">**Precision**</span></span>             | <span data-ttu-id="1be6f-547">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-547">No</span></span>          | <span data-ttu-id="1be6f-548">La precisión de la columna correspondiente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-548">The precision of the corresponding column.</span></span>                                                                                                                                                                                      |
| <span data-ttu-id="1be6f-549">**Escala**</span><span class="sxs-lookup"><span data-stu-id="1be6f-549">**Scale**</span></span>                 | <span data-ttu-id="1be6f-550">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-550">No</span></span>          | <span data-ttu-id="1be6f-551">La escala de la columna correspondiente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-551">The scale of the corresponding column.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="1be6f-552">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="1be6f-552">**Unicode**</span></span>               | <span data-ttu-id="1be6f-553">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-553">No</span></span>          | <span data-ttu-id="1be6f-554">**True** o **false** , dependiendo de si el valor de columna correspondiente se almacenará como una cadena Unicode.</span><span class="sxs-lookup"><span data-stu-id="1be6f-554">**True** or **False** depending on whether the corresponding column value will be stored as a Unicode string.</span></span>                                                                                                                   |
| <span data-ttu-id="1be6f-555">**Intercalación**</span><span class="sxs-lookup"><span data-stu-id="1be6f-555">**Collation**</span></span>             | <span data-ttu-id="1be6f-556">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-556">No</span></span>          | <span data-ttu-id="1be6f-557">Cadena que especifica la secuencia de intercalación que se usará en el origen de datos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-557">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="1be6f-558">**SRID**</span><span class="sxs-lookup"><span data-stu-id="1be6f-558">**SRID**</span></span>                  | <span data-ttu-id="1be6f-559">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-559">No</span></span>          | <span data-ttu-id="1be6f-560">Identificador de referencia del sistema espacial.</span><span class="sxs-lookup"><span data-stu-id="1be6f-560">Spatial System Reference Identifier.</span></span> <span data-ttu-id="1be6f-561">Solo es válido para las propiedades de los tipos espaciales.</span><span class="sxs-lookup"><span data-stu-id="1be6f-561">Valid only for properties of spatial types.</span></span> <span data-ttu-id="1be6f-562">Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="1be6f-562">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="1be6f-563">**StoreGeneratedPattern**</span><span class="sxs-lookup"><span data-stu-id="1be6f-563">**StoreGeneratedPattern**</span></span> | <span data-ttu-id="1be6f-564">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-564">No</span></span>          | <span data-ttu-id="1be6f-565">**None**, **Identity** (si el valor de la columna correspondiente es una identidad que se genera en la base de datos) o **calculado** (si el valor de la columna correspondiente se calcula en la base de datos).</span><span class="sxs-lookup"><span data-stu-id="1be6f-565">**None**, **Identity** (if the corresponding column value is an identity that is generated in the database), or **Computed** (if the corresponding column value is computed in the database).</span></span> <span data-ttu-id="1be6f-566">No es válido para las propiedades RowType.</span><span class="sxs-lookup"><span data-stu-id="1be6f-566">Not Valid for RowType properties.</span></span> |

> [!NOTE]
> <span data-ttu-id="1be6f-567">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento de **propiedad** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-567">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="1be6f-568">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-568">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="1be6f-569">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-569">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-570">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-570">Example</span></span>

<span data-ttu-id="1be6f-571">En el ejemplo siguiente se muestra un elemento **EntityType** con dos elementos **Property** secundarios:</span><span class="sxs-lookup"><span data-stu-id="1be6f-571">The following example shows an **EntityType** element with two child **Property** elements:</span></span>

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

## <a name="propertyref-element-ssdl"></a><span data-ttu-id="1be6f-572">PropertyRef (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-572">PropertyRef Element (SSDL)</span></span>

<span data-ttu-id="1be6f-573">El elemento **PropertyRef** en el lenguaje de definición de esquemas de almacenamiento (SSDL) hace referencia a una propiedad definida en un elemento EntityType para indicar que la propiedad realizará uno de los roles siguientes:</span><span class="sxs-lookup"><span data-stu-id="1be6f-573">The **PropertyRef** element in store schema definition language (SSDL) references a property defined on an EntityType element to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="1be6f-574">Formar parte de la clave principal de la tabla que el **EntityType** representa.</span><span class="sxs-lookup"><span data-stu-id="1be6f-574">Be part of the primary key of the table that the **EntityType** represents.</span></span> <span data-ttu-id="1be6f-575">Se pueden usar uno o varios elementos **PropertyRef** para definir una clave principal.</span><span class="sxs-lookup"><span data-stu-id="1be6f-575">One or more **PropertyRef** elements can be used to define a primary key.</span></span> <span data-ttu-id="1be6f-576">Para obtener más información, vea Key (Elemento).</span><span class="sxs-lookup"><span data-stu-id="1be6f-576">For more information, see Key element.</span></span>
-   <span data-ttu-id="1be6f-577">Ser el extremo dependiente o principal de una restricción referencial.</span><span class="sxs-lookup"><span data-stu-id="1be6f-577">Be the dependent or principal end of a referential constraint.</span></span> <span data-ttu-id="1be6f-578">Para obtener más información, vea ReferentialConstraint (Elemento).</span><span class="sxs-lookup"><span data-stu-id="1be6f-578">For more information, see ReferentialConstraint element.</span></span>

<span data-ttu-id="1be6f-579">El elemento **PropertyRef** solo puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="1be6f-579">The **PropertyRef** element can only have the following child elements:</span></span>

-   <span data-ttu-id="1be6f-580">Documentation (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-580">Documentation (zero or one)</span></span>
-   <span data-ttu-id="1be6f-581">Elementos Annotation</span><span class="sxs-lookup"><span data-stu-id="1be6f-581">Annotation elements</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-582">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-582">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-583">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-583">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="1be6f-584">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-584">Attribute Name</span></span> | <span data-ttu-id="1be6f-585">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-585">Is Required</span></span> | <span data-ttu-id="1be6f-586">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-586">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="1be6f-587">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="1be6f-587">**Name**</span></span>       | <span data-ttu-id="1be6f-588">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-588">Yes</span></span>         | <span data-ttu-id="1be6f-589">Nombre de la propiedad a la que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="1be6f-589">The name of the referenced property.</span></span> |

> [!NOTE]
> <span data-ttu-id="1be6f-590">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-590">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="1be6f-591">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-591">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="1be6f-592">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-592">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-593">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-593">Example</span></span>

<span data-ttu-id="1be6f-594">En el ejemplo siguiente se muestra un elemento **PropertyRef** que se usa para definir una clave principal haciendo referencia a una propiedad que se define en un elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-594">The following example shows a **PropertyRef** element used to define a primary key by referencing a property that is defined on an **EntityType** element.</span></span>

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

## <a name="referentialconstraint-element-ssdl"></a><span data-ttu-id="1be6f-595">ReferentialConstraint (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-595">ReferentialConstraint Element (SSDL)</span></span>

<span data-ttu-id="1be6f-596">El elemento **ReferentialConstraint** del lenguaje de definición de esquemas de almacenamiento (SSDL) representa una restricción de clave externa (también denominada restricción de integridad referencial) en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-596">The **ReferentialConstraint** element in store schema definition language (SSDL) represents a foreign key constraint (also called a referential integrity constraint) in the underlying database.</span></span> <span data-ttu-id="1be6f-597">Los extremos principal y dependiente de la restricción se especifican mediante los elementos secundarios Principal y Dependent, respectivamente.</span><span class="sxs-lookup"><span data-stu-id="1be6f-597">The principal and dependent ends of the constraint are specified by the Principal and Dependent child elements, respectively.</span></span> <span data-ttu-id="1be6f-598">La referencia a las columnas que participan en los extremos principal y dependiente se realiza mediante elementos PropertyRef.</span><span class="sxs-lookup"><span data-stu-id="1be6f-598">Columns that participate in the principal and dependent ends are referenced with PropertyRef elements.</span></span>

<span data-ttu-id="1be6f-599">El elemento **ReferentialConstraint** es un elemento secundario opcional del elemento Association.</span><span class="sxs-lookup"><span data-stu-id="1be6f-599">The **ReferentialConstraint** element is an optional child element of the Association element.</span></span> <span data-ttu-id="1be6f-600">Si no se utiliza un elemento **ReferentialConstraint** para asignar la restricción FOREIGN KEY especificada en el elemento **Association** , se debe usar un elemento AssociationSetMapping para hacerlo.</span><span class="sxs-lookup"><span data-stu-id="1be6f-600">If a **ReferentialConstraint** element is not used to map the foreign key constraint that is specified in the **Association** element, an AssociationSetMapping element must be used to do this.</span></span>

<span data-ttu-id="1be6f-601">El elemento **ReferentialConstraint** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="1be6f-601">The **ReferentialConstraint** element can have the following child elements:</span></span>

-   <span data-ttu-id="1be6f-602">Documentation (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-602">Documentation (zero or one)</span></span>
-   <span data-ttu-id="1be6f-603">Principal (exactamente uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-603">Principal (exactly one)</span></span>
-   <span data-ttu-id="1be6f-604">Dependent (exactamente uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-604">Dependent (exactly one)</span></span>
-   <span data-ttu-id="1be6f-605">Elementos Annotation (cero o más)</span><span class="sxs-lookup"><span data-stu-id="1be6f-605">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-606">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-606">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-607">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-607">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferentialConstraint** element.</span></span> <span data-ttu-id="1be6f-608">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-608">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="1be6f-609">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-609">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-610">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-610">Example</span></span>

<span data-ttu-id="1be6f-611">En el ejemplo siguiente se muestra un elemento **Association** que utiliza un elemento **ReferentialConstraint** para especificar las columnas que participan en la restricción FOREIGN KEY externa de \*\*FK \_ \*\* :</span><span class="sxs-lookup"><span data-stu-id="1be6f-611">The following example shows an **Association** element that uses a **ReferentialConstraint** element to specify the columns that participate in the **FK\_CustomerOrders** foreign key constraint:</span></span>

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

## <a name="returntype-element-ssdl"></a><span data-ttu-id="1be6f-612">ReturnType (elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-612">ReturnType Element (SSDL)</span></span>

<span data-ttu-id="1be6f-613">El elemento **ReturnType** del lenguaje de definición de esquemas de almacenamiento (SSDL) especifica el tipo de valor devuelto para una función que se define en un elemento de **función** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-613">The **ReturnType** element in store schema definition language (SSDL) specifies the return type for a function that is defined in a **Function** element.</span></span> <span data-ttu-id="1be6f-614">Un tipo de valor devuelto de función también se puede especificar con un atributo **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-614">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="1be6f-615">El tipo de valor devuelto de una función se especifica con el atributo **Type** o el elemento **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-615">The return type of a function is specified with the **Type** attribute or the **ReturnType** element.</span></span>

<span data-ttu-id="1be6f-616">El elemento **ReturnType** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="1be6f-616">The **ReturnType** element can have the following child elements:</span></span>

- <span data-ttu-id="1be6f-617">CollectionType (uno)</span><span class="sxs-lookup"><span data-stu-id="1be6f-617">CollectionType (one)</span></span>  

> [!NOTE]
> <span data-ttu-id="1be6f-618">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-618">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** element.</span></span> <span data-ttu-id="1be6f-619">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-619">However, custom attributes may not belong to any XML namespace that is reserved for SSDL.</span></span> <span data-ttu-id="1be6f-620">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-620">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-621">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-621">Example</span></span>

<span data-ttu-id="1be6f-622">En el ejemplo siguiente se utiliza una **función** que devuelve una colección de filas.</span><span class="sxs-lookup"><span data-stu-id="1be6f-622">The following example uses a **Function** that returns a collection of rows.</span></span>

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


## <a name="rowtype-element-ssdl"></a><span data-ttu-id="1be6f-623">RowType (elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-623">RowType Element (SSDL)</span></span>

<span data-ttu-id="1be6f-624">Un elemento **RowType** en el lenguaje de definición de esquemas de almacenamiento (SSDL) define una estructura sin nombre como un tipo de valor devuelto para una función definida en el almacén.</span><span class="sxs-lookup"><span data-stu-id="1be6f-624">A **RowType** element in store schema definition language (SSDL) defines an unnamed structure as a return type for a function defined in the store.</span></span>

<span data-ttu-id="1be6f-625">Un elemento **RowType** es el elemento secundario del elemento **CollectionType** :</span><span class="sxs-lookup"><span data-stu-id="1be6f-625">A **RowType** element is the child element of **CollectionType** element:</span></span>

<span data-ttu-id="1be6f-626">Un elemento **RowType** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="1be6f-626">A **RowType** element can have the following child elements:</span></span>

- <span data-ttu-id="1be6f-627">Property (uno o varios)</span><span class="sxs-lookup"><span data-stu-id="1be6f-627">Property (one or more)</span></span>  

### <a name="example"></a><span data-ttu-id="1be6f-628">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-628">Example</span></span>

<span data-ttu-id="1be6f-629">En el ejemplo siguiente se muestra una función de almacenamiento que usa un elemento **CollectionType** para especificar que la función devuelve una colección de filas (tal y como se especifica en el elemento **RowType** ).</span><span class="sxs-lookup"><span data-stu-id="1be6f-629">The following example shows a store function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>


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

## <a name="schema-element-ssdl"></a><span data-ttu-id="1be6f-630">Schema (Elemento) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-630">Schema Element (SSDL)</span></span>

<span data-ttu-id="1be6f-631">El elemento **Schema** en el lenguaje de definición de esquemas de almacenamiento (SSDL) es el elemento raíz de una definición de modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1be6f-631">The **Schema** element in store schema definition language (SSDL) is the root element of a storage model definition.</span></span> <span data-ttu-id="1be6f-632">Contiene las definiciones para los objetos, las funciones y los contenedores que conforman un modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1be6f-632">It contains definitions for the objects, functions, and containers that make up a storage model.</span></span>

<span data-ttu-id="1be6f-633">El elemento **Schema** puede contener cero o más de los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="1be6f-633">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="1be6f-634">Asociación</span><span class="sxs-lookup"><span data-stu-id="1be6f-634">Association</span></span>
-   <span data-ttu-id="1be6f-635">EntityType</span><span class="sxs-lookup"><span data-stu-id="1be6f-635">EntityType</span></span>
-   <span data-ttu-id="1be6f-636">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="1be6f-636">EntityContainer</span></span>
-   <span data-ttu-id="1be6f-637">Función</span><span class="sxs-lookup"><span data-stu-id="1be6f-637">Function</span></span>

<span data-ttu-id="1be6f-638">El elemento **Schema** usa el atributo **namespace** para definir el espacio de nombres para el tipo de entidad y los objetos de asociación en un modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1be6f-638">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type and association objects in a storage model.</span></span> <span data-ttu-id="1be6f-639">Dentro de un espacio de nombres, no puede haber dos objetos con el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="1be6f-639">Within a namespace, no two objects can have the same name.</span></span>

<span data-ttu-id="1be6f-640">Un espacio de nombres del modelo de almacenamiento es diferente del espacio de nombres XML del elemento **Schema** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-640">A storage model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="1be6f-641">Un espacio de nombres del modelo de almacenamiento (tal y como se define en el atributo de **espacio de nombres** ) es un contenedor lógico para tipos de entidad y tipos de asociación.</span><span class="sxs-lookup"><span data-stu-id="1be6f-641">A storage model namespace (as defined by the **Namespace** attribute) is a logical container for entity types and association types.</span></span> <span data-ttu-id="1be6f-642">El espacio de nombres XML (indicado por el atributo **xmlns** ) de un elemento **Schema** es el espacio de nombres predeterminado para los elementos secundarios y los atributos del elemento **Schema** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-642">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="1be6f-643">Los espacios de nombres XML del formulario https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (donde YYYY y mm representan un año y un mes respectivamente) se reservan para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-643">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm/ssdl (where YYYY and MM represent a year and month respectively) are reserved for SSDL.</span></span> <span data-ttu-id="1be6f-644">No puede haber elementos y atributos personalizados en espacios de nombres que tengan este formato.</span><span class="sxs-lookup"><span data-stu-id="1be6f-644">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="1be6f-645">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="1be6f-645">Applicable Attributes</span></span>

<span data-ttu-id="1be6f-646">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Schema** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-646">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="1be6f-647">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="1be6f-647">Attribute Name</span></span>            | <span data-ttu-id="1be6f-648">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="1be6f-648">Is Required</span></span> | <span data-ttu-id="1be6f-649">Value</span><span class="sxs-lookup"><span data-stu-id="1be6f-649">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:--------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-650">**Espacio de nombres**</span><span class="sxs-lookup"><span data-stu-id="1be6f-650">**Namespace**</span></span>             | <span data-ttu-id="1be6f-651">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-651">Yes</span></span>         | <span data-ttu-id="1be6f-652">El espacio de nombres del modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1be6f-652">The namespace of the storage model.</span></span> <span data-ttu-id="1be6f-653">El valor del atributo **namespace** se usa para formar el nombre completo de un tipo.</span><span class="sxs-lookup"><span data-stu-id="1be6f-653">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="1be6f-654">Por ejemplo, si un **EntityType** denominado *Customer* está en el espacio de nombres ExampleModel. Store, el nombre completo del **EntityType** es ExampleModel. Store. Customer.</span><span class="sxs-lookup"><span data-stu-id="1be6f-654">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace, then the fully qualified name of the **EntityType** is ExampleModel.Store.Customer.</span></span> <br/> <span data-ttu-id="1be6f-655">Las siguientes cadenas no se pueden usar como el valor del atributo **namespace** : **System**, **Transient**o **EDM**.</span><span class="sxs-lookup"><span data-stu-id="1be6f-655">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="1be6f-656">El valor del atributo **namespace** no puede ser el mismo que el valor del atributo **namespace** del elemento Schema de CSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-656">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the CSDL Schema element.</span></span> |
| <span data-ttu-id="1be6f-657">**Alias**</span><span class="sxs-lookup"><span data-stu-id="1be6f-657">**Alias**</span></span>                 | <span data-ttu-id="1be6f-658">No</span><span class="sxs-lookup"><span data-stu-id="1be6f-658">No</span></span>          | <span data-ttu-id="1be6f-659">Un identificador usado en lugar del nombre del espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="1be6f-659">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="1be6f-660">Por ejemplo, si un **EntityType** denominado *Customer* está en el espacio de nombres ExampleModel. Store y el valor del atributo **alias** es *StorageModel*, puede usar StorageModel. Customer como nombre completo del **EntityType.**</span><span class="sxs-lookup"><span data-stu-id="1be6f-660">For example, if an **EntityType** named *Customer* is in the ExampleModel.Store namespace and the value of the **Alias** attribute is *StorageModel*, then you can use StorageModel.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="1be6f-661">**Proveedor**</span><span class="sxs-lookup"><span data-stu-id="1be6f-661">**Provider**</span></span>              | <span data-ttu-id="1be6f-662">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-662">Yes</span></span>         | <span data-ttu-id="1be6f-663">El proveedor de datos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-663">The data provider.</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| <span data-ttu-id="1be6f-664">**ProviderManifestToken**</span><span class="sxs-lookup"><span data-stu-id="1be6f-664">**ProviderManifestToken**</span></span> | <span data-ttu-id="1be6f-665">Sí</span><span class="sxs-lookup"><span data-stu-id="1be6f-665">Yes</span></span>         | <span data-ttu-id="1be6f-666">Un token que indica al proveedor qué manifiesto del proveedor debe devolver.</span><span class="sxs-lookup"><span data-stu-id="1be6f-666">A token that indicates to the provider which provider manifest to return.</span></span> <span data-ttu-id="1be6f-667">No se define ningún formato para el token.</span><span class="sxs-lookup"><span data-stu-id="1be6f-667">No format for the token is defined.</span></span> <span data-ttu-id="1be6f-668">El proveedor define los valores para el token.</span><span class="sxs-lookup"><span data-stu-id="1be6f-668">Values for the token are defined by the provider.</span></span> <span data-ttu-id="1be6f-669">Para obtener información sobre los tokens del manifiesto del proveedor de SQL Server, vea SqlClient para Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="1be6f-669">For information about SQL Server provider manifest tokens, see SqlClient for Entity Framework.</span></span>                                                                                                                                                                                                                                                                                                                        |

### <a name="example"></a><span data-ttu-id="1be6f-670">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-670">Example</span></span>

<span data-ttu-id="1be6f-671">En el ejemplo siguiente se muestra un elemento **Schema** que contiene un elemento **EntityContainer** , dos elementos **EntityType** y un elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-671">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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

## <a name="annotation-attributes"></a><span data-ttu-id="1be6f-672">Atributos de anotación</span><span class="sxs-lookup"><span data-stu-id="1be6f-672">Annotation Attributes</span></span>

<span data-ttu-id="1be6f-673">Los atributos de anotación (Annotation) del lenguaje de definición de esquemas de almacenamiento (SSDL) son atributos XML personalizados del modelo de almacenamiento que proporcionan metadatos adicionales sobre los elementos del modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1be6f-673">Annotation attributes in store schema definition language (SSDL) are custom XML attributes in the storage model that provide extra metadata about the elements in the storage model.</span></span> <span data-ttu-id="1be6f-674">Además de tener una estructura XML válida, las siguientes restricciones se aplican a los atributos de anotación:</span><span class="sxs-lookup"><span data-stu-id="1be6f-674">In addition to having valid XML structure, the following constraints apply to annotation attributes:</span></span>

-   <span data-ttu-id="1be6f-675">Los atributos de anotación no deben estar en ningún espacio de nombres XML reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-675">Annotation attributes must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="1be6f-676">Dos atributos de anotación cualesquiera no pueden tener el mismo nombre completo.</span><span class="sxs-lookup"><span data-stu-id="1be6f-676">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="1be6f-677">Se pueden aplicar varios atributos de anotación a un elemento SSDL determinado.</span><span class="sxs-lookup"><span data-stu-id="1be6f-677">More than one annotation attribute may be applied to a given SSDL element.</span></span> <span data-ttu-id="1be6f-678">Se puede tener acceso a los metadatos contenidos en los elementos Annotation en tiempo de ejecución mediante el uso de clases en el espacio de nombres System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="1be6f-678">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-679">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-679">Example</span></span>

<span data-ttu-id="1be6f-680">En el ejemplo siguiente se muestra un elemento EntityType que tiene un atributo Annotation aplicado a la propiedad **OrderID** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-680">The following example shows an EntityType element that has an annotation attribute applied to the **OrderId** property.</span></span> <span data-ttu-id="1be6f-681">En el ejemplo también se muestra un elemento Annotation agregado al elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-681">The example also show an annotation element added to the **EntityType** element.</span></span>

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

## <a name="annotation-elements-ssdl"></a><span data-ttu-id="1be6f-682">Annotation (Elementos) (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-682">Annotation Elements (SSDL)</span></span>

<span data-ttu-id="1be6f-683">Los elementos Annotation del lenguaje de definición de esquemas de almacenamiento (SSDL) son elementos XML personalizados del modelo de almacenamiento que proporcionan metadatos adicionales sobre el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="1be6f-683">Annotation elements in store schema definition language (SSDL) are custom XML elements in the storage model that provide extra metadata about the storage model.</span></span> <span data-ttu-id="1be6f-684">Además de tener una estructura XML válida, las siguientes restricciones se aplican a los elementos Annotation:</span><span class="sxs-lookup"><span data-stu-id="1be6f-684">In addition to having valid XML structure, the following constraints apply to annotation elements:</span></span>

-   <span data-ttu-id="1be6f-685">Los elementos Annotation no deben estar en un espacio de nombres de XML que esté reservado para SSDL.</span><span class="sxs-lookup"><span data-stu-id="1be6f-685">Annotation elements must not be in any XML namespace that is reserved for SSDL.</span></span>
-   <span data-ttu-id="1be6f-686">Los nombres completos de dos elementos Annotation cualesquiera no deben ser los mismos.</span><span class="sxs-lookup"><span data-stu-id="1be6f-686">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="1be6f-687">Los elementos Annotation deben aparecer después de todos los demás elementos secundarios de un elemento SSDL determinado.</span><span class="sxs-lookup"><span data-stu-id="1be6f-687">Annotation elements must appear after all other child elements of a given SSDL element.</span></span>

<span data-ttu-id="1be6f-688">Más de un elemento Annotation puede ser un elemento secundario de un elemento SSDL determinado.</span><span class="sxs-lookup"><span data-stu-id="1be6f-688">More than one annotation element may be a child of a given SSDL element.</span></span> <span data-ttu-id="1be6f-689">A partir de la .NET Framework versión 4, se puede tener acceso a los metadatos contenidos en los elementos Annotation en tiempo de ejecución mediante el uso de clases en el espacio de nombres System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="1be6f-689">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="1be6f-690">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="1be6f-690">Example</span></span>

<span data-ttu-id="1be6f-691">En el ejemplo siguiente se muestra un elemento EntityType que tiene un elemento Annotation (**CustomElement**).</span><span class="sxs-lookup"><span data-stu-id="1be6f-691">The following example shows an EntityType element that has an annotation element (**CustomElement**).</span></span> <span data-ttu-id="1be6f-692">En el ejemplo también se muestra un atributo de anotación aplicado a la propiedad **OrderID** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-692">The example also shows an annotation attribute applied to the **OrderId** property.</span></span>

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

## <a name="facets-ssdl"></a><span data-ttu-id="1be6f-693">Facetas (SSDL)</span><span class="sxs-lookup"><span data-stu-id="1be6f-693">Facets (SSDL)</span></span>

<span data-ttu-id="1be6f-694">Las facetas del lenguaje de definición de esquema de almacenamiento (SSDL) representan restricciones sobre los tipos de columna que se especifican en elementos Property.</span><span class="sxs-lookup"><span data-stu-id="1be6f-694">Facets in store schema definition language (SSDL) represent constraints on column types that are specified in Property elements.</span></span> <span data-ttu-id="1be6f-695">Las caras se implementan como atributos XML en los elementos de **propiedad** .</span><span class="sxs-lookup"><span data-stu-id="1be6f-695">Facets are implemented as XML attributes on **Property** elements.</span></span>

<span data-ttu-id="1be6f-696">En la tabla siguiente se describen las facetas que se admiten en SSDL:</span><span class="sxs-lookup"><span data-stu-id="1be6f-696">The following table describes the facets that are supported in SSDL:</span></span>

| <span data-ttu-id="1be6f-697">Faceta</span><span class="sxs-lookup"><span data-stu-id="1be6f-697">Facet</span></span>           | <span data-ttu-id="1be6f-698">Descripción</span><span class="sxs-lookup"><span data-stu-id="1be6f-698">Description</span></span>                                                                                                                                                                                                                                                 |
|:----------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="1be6f-699">**Intercalación**</span><span class="sxs-lookup"><span data-stu-id="1be6f-699">**Collation**</span></span>   | <span data-ttu-id="1be6f-700">Especifica la secuencia de intercalación (o secuencia de orden) que se va a usar cuando se realicen las operaciones de comparación y ordenación sobre los valores de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="1be6f-700">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                             |
| <span data-ttu-id="1be6f-701">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="1be6f-701">**FixedLength**</span></span> | <span data-ttu-id="1be6f-702">Especifica si la longitud del valor de la columna puede variar.</span><span class="sxs-lookup"><span data-stu-id="1be6f-702">Specifies whether the length of the column value can vary.</span></span>                                                                                                                                                                                                  |
| <span data-ttu-id="1be6f-703">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="1be6f-703">**MaxLength**</span></span>   | <span data-ttu-id="1be6f-704">Especifica la longitud máxima del valor de la columna.</span><span class="sxs-lookup"><span data-stu-id="1be6f-704">Specifies the maximum length of the column value.</span></span>                                                                                                                                                                                                           |
| <span data-ttu-id="1be6f-705">**Precisión**</span><span class="sxs-lookup"><span data-stu-id="1be6f-705">**Precision**</span></span>   | <span data-ttu-id="1be6f-706">Para las propiedades de tipo **decimal**, especifica el número de dígitos que puede tener un valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="1be6f-706">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="1be6f-707">Para las propiedades de tipo **Time**, **DateTime**y **DateTimeOffset**, especifica el número de dígitos para la parte fraccionaria de los segundos del valor de la columna.</span><span class="sxs-lookup"><span data-stu-id="1be6f-707">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the column value.</span></span> |
| <span data-ttu-id="1be6f-708">**Escala**</span><span class="sxs-lookup"><span data-stu-id="1be6f-708">**Scale**</span></span>       | <span data-ttu-id="1be6f-709">Especifica el número de dígitos que puede haber a la derecha del separador de decimales para el valor de la columna.</span><span class="sxs-lookup"><span data-stu-id="1be6f-709">Specifies the number of digits to the right of the decimal point for the column value.</span></span>                                                                                                                                                                      |
| <span data-ttu-id="1be6f-710">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="1be6f-710">**Unicode**</span></span>     | <span data-ttu-id="1be6f-711">Indica si el valor de la columna está almacenado como Unicode.</span><span class="sxs-lookup"><span data-stu-id="1be6f-711">Indicates whether the column value is stored as Unicode.</span></span>                                                                                                                                                                                                    |
