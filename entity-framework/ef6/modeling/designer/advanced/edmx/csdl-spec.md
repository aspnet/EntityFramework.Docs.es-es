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
# <a name="csdl-specification"></a><span data-ttu-id="b588d-103">Especificación CSDL</span><span class="sxs-lookup"><span data-stu-id="b588d-103">CSDL Specification</span></span>
<span data-ttu-id="b588d-104">El lenguaje de definición de esquemas conceptuales (CSDL) es un lenguaje basado en XML que describe las entidades, las relaciones y las funciones que conforman un modelo conceptual de una aplicación controlada por datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-104">Conceptual schema definition language (CSDL) is an XML-based language that describes the entities, relationships, and functions that make up a conceptual model of a data-driven application.</span></span> <span data-ttu-id="b588d-105">Este modelo conceptual puede ser utilizado por el Entity Framework o WCF Data Services.</span><span class="sxs-lookup"><span data-stu-id="b588d-105">This conceptual model can be used by the Entity Framework or WCF Data Services.</span></span> <span data-ttu-id="b588d-106">Los metadatos que se describen con CSDL los utiliza el Entity Framework para asignar entidades y relaciones que se definen en un modelo conceptual a un origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-106">The metadata that is described with CSDL is used by the Entity Framework to map entities and relationships that are defined in a conceptual model to a data source.</span></span> <span data-ttu-id="b588d-107">Para obtener más información, vea [especificación de SSDL](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) y especificación de [MSL](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span><span class="sxs-lookup"><span data-stu-id="b588d-107">For more information, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec) and [MSL Specification](xref:ef6/modeling/designer/advanced/edmx/msl-spec).</span></span>

<span data-ttu-id="b588d-108">CSDL es la implementación del Entity Framework del Entity Data Model.</span><span class="sxs-lookup"><span data-stu-id="b588d-108">CSDL is the Entity Framework's implementation of the Entity Data Model.</span></span>

<span data-ttu-id="b588d-109">En una aplicación Entity Framework, los metadatos del modelo conceptual se cargan desde un archivo. CSDL (escrito en CSDL) en una instancia de System. Data. Metadata. Edm. EdmItemCollection y son accesibles mediante métodos en la clase System. Data. Metadata. Edm. MetadataWorkspace.</span><span class="sxs-lookup"><span data-stu-id="b588d-109">In an Entity Framework application, conceptual model metadata is loaded from a .csdl file (written in CSDL) into an instance of the System.Data.Metadata.Edm.EdmItemCollection and is accessible by using methods in the System.Data.Metadata.Edm.MetadataWorkspace class.</span></span> <span data-ttu-id="b588d-110">Entity Framework utiliza los metadatos del modelo conceptual para traducir las consultas del modelo conceptual a comandos específicos del origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-110">Entity Framework uses conceptual model metadata to translate queries against the conceptual model to data source-specific commands.</span></span>

<span data-ttu-id="b588d-111">El diseñador de EF almacena la información del modelo conceptual en un archivo. edmx en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="b588d-111">The EF Designer stores conceptual model information in an .edmx file at design time.</span></span> <span data-ttu-id="b588d-112">En el momento de la compilación, el diseñador de EF usa información en un archivo. edmx para crear el archivo. CSDL que necesita Entity Framework en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="b588d-112">At build time, the EF Designer uses information in an .edmx file to create the .csdl file that is needed by Entity Framework at runtime.</span></span>

<span data-ttu-id="b588d-113">Las versiones de CSDL se diferencian por los espacios de nombres XML.</span><span class="sxs-lookup"><span data-stu-id="b588d-113">Versions of CSDL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="b588d-114">Versión de CSDL</span><span class="sxs-lookup"><span data-stu-id="b588d-114">CSDL Version</span></span> | <span data-ttu-id="b588d-115">Espacio de nombres XML</span><span class="sxs-lookup"><span data-stu-id="b588d-115">XML Namespace</span></span>                                |
|:-------------|:---------------------------------------------|
| <span data-ttu-id="b588d-116">CSDL v1</span><span class="sxs-lookup"><span data-stu-id="b588d-116">CSDL v1</span></span>      | https://schemas.microsoft.com/ado/2006/04/edm |
| <span data-ttu-id="b588d-117">CSDL V2</span><span class="sxs-lookup"><span data-stu-id="b588d-117">CSDL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/edm |
| <span data-ttu-id="b588d-118">CSDL V3</span><span class="sxs-lookup"><span data-stu-id="b588d-118">CSDL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/edm |

 
## <a name="association-element-csdl"></a><span data-ttu-id="b588d-119">Association (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-119">Association Element (CSDL)</span></span>

<span data-ttu-id="b588d-120">Un elemento **Association** define una relación entre dos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-120">An **Association** element defines a relationship between two entity types.</span></span> <span data-ttu-id="b588d-121">Una asociación debe especificar los tipos de entidad que están implicados en la relación y el posible número de tipos de entidad en cada extremo de la relación, que se conoce como multiplicidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-121">An association must specify the entity types that are involved in the relationship and the possible number of entity types at each end of the relationship, which is known as the multiplicity.</span></span> <span data-ttu-id="b588d-122">La multiplicidad de un extremo de asociación puede tener un valor de uno (1), cero o uno (0.. 1) o varios ( \* ).</span><span class="sxs-lookup"><span data-stu-id="b588d-122">The multiplicity of an association end can have a value of one (1), zero or one (0..1), or many (\*).</span></span> <span data-ttu-id="b588d-123">Esta información se especifica en dos elementos End secundarios.</span><span class="sxs-lookup"><span data-stu-id="b588d-123">This information is specified in two child End elements.</span></span>

<span data-ttu-id="b588d-124">Es posible obtener acceso a las instancias de tipo de entidad situadas en un extremo de la asociación a través de las propiedades de navegación o las claves externas, si estas se exponen en un tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-124">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys, if they are exposed on an entity type.</span></span>

<span data-ttu-id="b588d-125">En una aplicación, una instancia de una asociación representa una asociación concreta entre las instancias de tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-125">In an application, an instance of an association represents a specific association between instances of entity types.</span></span> <span data-ttu-id="b588d-126">Las instancias de asociación se agrupan de manera lógica en un conjunto de asociaciones.</span><span class="sxs-lookup"><span data-stu-id="b588d-126">Association instances are logically grouped in an association set.</span></span>

<span data-ttu-id="b588d-127">Un elemento **Association** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-127">An **Association** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-128">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-128">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b588d-129">End (exactamente 2 elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-129">End (exactly 2 elements)</span></span>
-   <span data-ttu-id="b588d-130">ReferentialConstraint (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-130">ReferentialConstraint (zero or one element)</span></span>
-   <span data-ttu-id="b588d-131">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-131">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-132">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-132">Applicable Attributes</span></span>

<span data-ttu-id="b588d-133">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="b588d-133">The table below describes the attributes that can be applied to the **Association** element.</span></span>

| <span data-ttu-id="b588d-134">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-134">Attribute Name</span></span> | <span data-ttu-id="b588d-135">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-135">Is Required</span></span> | <span data-ttu-id="b588d-136">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-136">Value</span></span>                        |
|:---------------|:------------|:-----------------------------|
| <span data-ttu-id="b588d-137">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-137">**Name**</span></span>       | <span data-ttu-id="b588d-138">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-138">Yes</span></span>         | <span data-ttu-id="b588d-139">Nombre de la asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-139">The name of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-140">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="b588d-140">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="b588d-141">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-141">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-142">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-142">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-143">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-143">Example</span></span>

<span data-ttu-id="b588d-144">En el ejemplo siguiente se muestra un elemento **Association** que define la Asociación **CustomerOrders** cuando las claves externas no se han expuesto en los tipos de entidad **Customer** y **Order** .</span><span class="sxs-lookup"><span data-stu-id="b588d-144">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have not been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="b588d-145">Los valores de **multiplicidad** de cada **extremo** de la Asociación indican que se pueden asociar muchos **pedidos** a un **cliente**, pero solo un **cliente** puede asociarse a un **pedido**.</span><span class="sxs-lookup"><span data-stu-id="b588d-145">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="b588d-146">Además, el elemento **aleliminar** indica que todos los **pedidos** relacionados con un **cliente** determinado y que se han cargado en el ObjectContext se eliminarán si se elimina el **cliente** .</span><span class="sxs-lookup"><span data-stu-id="b588d-146">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" >
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

<span data-ttu-id="b588d-147">En el ejemplo siguiente se muestra un elemento **Association** que define la Asociación **CustomerOrders** cuando las claves externas se han expuesto en los tipos de entidad **Customer** y **Order** .</span><span class="sxs-lookup"><span data-stu-id="b588d-147">The following example shows an **Association** element that defines the **CustomerOrders** association when foreign keys have been exposed on the **Customer** and **Order** entity types.</span></span> <span data-ttu-id="b588d-148">Con las claves externas expuestas, la relación entre las entidades se administra con un elemento **ReferentialConstraint** .</span><span class="sxs-lookup"><span data-stu-id="b588d-148">With foreign keys exposed, the relationship between the entities is managed with a **ReferentialConstraint** element.</span></span> <span data-ttu-id="b588d-149">Un elemento AssociationSetMapping correspondiente no es necesario para asignar esta asociación al origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-149">A corresponding AssociationSetMapping element is not necessary to map this association to the data source.</span></span>

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
 

 

## <a name="associationset-element-csdl"></a><span data-ttu-id="b588d-150">AssociationSet (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-150">AssociationSet Element (CSDL)</span></span>

<span data-ttu-id="b588d-151">El elemento **AssociationSet** en el lenguaje de definición de esquemas conceptuales (CSDL) es un contenedor lógico para las instancias de asociación del mismo tipo.</span><span class="sxs-lookup"><span data-stu-id="b588d-151">The **AssociationSet** element in conceptual schema definition language (CSDL) is a logical container for association instances of the same type.</span></span> <span data-ttu-id="b588d-152">Un conjunto de asociaciones proporciona una definición para agrupar las instancias de la asociación con objeto de que se puedan asignar a un origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-152">An association set provides a definition for grouping association instances so that they can be mapped to a data source.</span></span>  

<span data-ttu-id="b588d-153">El elemento **AssociationSet** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-153">The **AssociationSet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-154">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-154">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="b588d-155">End (se requieren exactamente dos elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-155">End (exactly two elements required)</span></span>
-   <span data-ttu-id="b588d-156">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-156">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="b588d-157">El atributo **Association** especifica el tipo de asociación que contiene un conjunto de asociaciones.</span><span class="sxs-lookup"><span data-stu-id="b588d-157">The **Association** attribute specifies the type of association that an association set contains.</span></span> <span data-ttu-id="b588d-158">Los conjuntos de entidades que componen los extremos de un conjunto de asociaciones se especifican con exactamente dos elementos **finales** secundarios.</span><span class="sxs-lookup"><span data-stu-id="b588d-158">The entity sets that make up the ends of an association set are specified with exactly two child **End** elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-159">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-159">Applicable Attributes</span></span>

<span data-ttu-id="b588d-160">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="b588d-160">The table below describes the attributes that can be applied to the **AssociationSet** element.</span></span>

| <span data-ttu-id="b588d-161">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-161">Attribute Name</span></span>  | <span data-ttu-id="b588d-162">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-162">Is Required</span></span> | <span data-ttu-id="b588d-163">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-163">Value</span></span>                                                                                                                                                             |
|:----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-164">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-164">**Name**</span></span>        | <span data-ttu-id="b588d-165">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-165">Yes</span></span>         | <span data-ttu-id="b588d-166">Nombre del conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="b588d-166">The name of the entity set.</span></span> <span data-ttu-id="b588d-167">El valor del atributo **Name** no puede ser el mismo que el valor del atributo **Association** .</span><span class="sxs-lookup"><span data-stu-id="b588d-167">The value of the **Name** attribute cannot be the same as the value of the **Association** attribute.</span></span>                                 |
| <span data-ttu-id="b588d-168">**Asociación**</span><span class="sxs-lookup"><span data-stu-id="b588d-168">**Association**</span></span> | <span data-ttu-id="b588d-169">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-169">Yes</span></span>         | <span data-ttu-id="b588d-170">Nombre completo de la asociación cuyas instancias contiene el conjunto de asociaciones.</span><span class="sxs-lookup"><span data-stu-id="b588d-170">The fully-qualified name of the association that the association set contains instances of.</span></span> <span data-ttu-id="b588d-171">La asociación debe estar en el mismo espacio de nombres que el conjunto de asociaciones.</span><span class="sxs-lookup"><span data-stu-id="b588d-171">The association must be in the same namespace as the association set.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-172">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="b588d-172">Any number of annotation attributes (custom XML attributes) may be applied to the **AssociationSet** element.</span></span> <span data-ttu-id="b588d-173">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-173">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-174">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-174">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-175">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-175">Example</span></span>

<span data-ttu-id="b588d-176">En el ejemplo siguiente se muestra un elemento **EntityContainer** con dos elementos **AssociationSet** :</span><span class="sxs-lookup"><span data-stu-id="b588d-176">The following example shows an **EntityContainer** element with two **AssociationSet** elements:</span></span>

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
 

 

## <a name="collectiontype-element-csdl"></a><span data-ttu-id="b588d-177">CollectionType (Elemento de CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-177">CollectionType Element (CSDL)</span></span>

<span data-ttu-id="b588d-178">El elemento **CollectionType** del lenguaje de definición de esquemas conceptuales (CSDL) especifica que un parámetro de función o un tipo de valor devuelto de función es una colección.</span><span class="sxs-lookup"><span data-stu-id="b588d-178">The **CollectionType** element in conceptual schema definition language (CSDL) specifies that a function parameter or function return type is a collection.</span></span> <span data-ttu-id="b588d-179">El elemento **CollectionType** puede ser un elemento secundario del elemento Parameter o del elemento ReturnType (function).</span><span class="sxs-lookup"><span data-stu-id="b588d-179">The **CollectionType** element can be a child of the Parameter element or the ReturnType (Function) element.</span></span> <span data-ttu-id="b588d-180">El tipo de colección se puede especificar mediante el atributo **Type** o uno de los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="b588d-180">The type of collection can be specified by using either the **Type** attribute or one of the following child elements:</span></span>

-   <span data-ttu-id="b588d-181">**CollectionType**</span><span class="sxs-lookup"><span data-stu-id="b588d-181">**CollectionType**</span></span>
-   <span data-ttu-id="b588d-182">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="b588d-182">ReferenceType</span></span>
-   <span data-ttu-id="b588d-183">RowType</span><span class="sxs-lookup"><span data-stu-id="b588d-183">RowType</span></span>
-   <span data-ttu-id="b588d-184">TypeRef</span><span class="sxs-lookup"><span data-stu-id="b588d-184">TypeRef</span></span>

> [!NOTE]
> <span data-ttu-id="b588d-185">Un modelo no se validará si el tipo de una colección se especifica con el atributo **Type** y un elemento secundario.</span><span class="sxs-lookup"><span data-stu-id="b588d-185">A model will not validate if the type of a collection is specified with both the **Type** attribute and a child element.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-186">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-186">Applicable Attributes</span></span>

<span data-ttu-id="b588d-187">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-187">The following table describes the attributes that can be applied to the **CollectionType** element.</span></span> <span data-ttu-id="b588d-188">Tenga en cuenta que los atributos **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**y **collation** solo se aplican a las colecciones de **EDMSimpleTypes**.</span><span class="sxs-lookup"><span data-stu-id="b588d-188">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to collections of **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="b588d-189">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-189">Attribute Name</span></span>                                                          | <span data-ttu-id="b588d-190">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-190">Is Required</span></span> | <span data-ttu-id="b588d-191">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-191">Value</span></span>                                                                                                                                                                                                                            |
|:------------------------------------------------------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-192">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="b588d-192">**Type**</span></span>                                                                | <span data-ttu-id="b588d-193">No</span><span class="sxs-lookup"><span data-stu-id="b588d-193">No</span></span>          | <span data-ttu-id="b588d-194">Tipo de la colección.</span><span class="sxs-lookup"><span data-stu-id="b588d-194">The type of the collection.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="b588d-195">**Admisión de valores NULL**</span><span class="sxs-lookup"><span data-stu-id="b588d-195">**Nullable**</span></span>                                                            | <span data-ttu-id="b588d-196">No</span><span class="sxs-lookup"><span data-stu-id="b588d-196">No</span></span>          | <span data-ttu-id="b588d-197">**True** (el valor predeterminado) o **False**, según si la propiedad puede tener un valor nulo.</span><span class="sxs-lookup"><span data-stu-id="b588d-197">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                 |
| <span data-ttu-id="b588d-198">> en CSDL v1, una propiedad de tipo complejo debe tener `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="b588d-198">> In the CSDL v1, a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                  |
| <span data-ttu-id="b588d-199">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="b588d-199">**DefaultValue**</span></span>                                                        | <span data-ttu-id="b588d-200">No</span><span class="sxs-lookup"><span data-stu-id="b588d-200">No</span></span>          | <span data-ttu-id="b588d-201">Valor predeterminado de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-201">The default value of the property.</span></span>                                                                                                                                                                                               |
| <span data-ttu-id="b588d-202">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-202">**MaxLength**</span></span>                                                           | <span data-ttu-id="b588d-203">No</span><span class="sxs-lookup"><span data-stu-id="b588d-203">No</span></span>          | <span data-ttu-id="b588d-204">La longitud máxima del valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-204">The maximum length of the property value.</span></span>                                                                                                                                                                                        |
| <span data-ttu-id="b588d-205">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-205">**FixedLength**</span></span>                                                         | <span data-ttu-id="b588d-206">No</span><span class="sxs-lookup"><span data-stu-id="b588d-206">No</span></span>          | <span data-ttu-id="b588d-207">**True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena de longitud fija.</span><span class="sxs-lookup"><span data-stu-id="b588d-207">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                           |
| <span data-ttu-id="b588d-208">**Precisión**</span><span class="sxs-lookup"><span data-stu-id="b588d-208">**Precision**</span></span>                                                           | <span data-ttu-id="b588d-209">No</span><span class="sxs-lookup"><span data-stu-id="b588d-209">No</span></span>          | <span data-ttu-id="b588d-210">Precisión del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-210">The precision of the property value.</span></span>                                                                                                                                                                                             |
| <span data-ttu-id="b588d-211">**Escala**</span><span class="sxs-lookup"><span data-stu-id="b588d-211">**Scale**</span></span>                                                               | <span data-ttu-id="b588d-212">No</span><span class="sxs-lookup"><span data-stu-id="b588d-212">No</span></span>          | <span data-ttu-id="b588d-213">Escala del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-213">The scale of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="b588d-214">**SRID**</span><span class="sxs-lookup"><span data-stu-id="b588d-214">**SRID**</span></span>                                                                | <span data-ttu-id="b588d-215">No</span><span class="sxs-lookup"><span data-stu-id="b588d-215">No</span></span>          | <span data-ttu-id="b588d-216">Identificador de referencia del sistema espacial.</span><span class="sxs-lookup"><span data-stu-id="b588d-216">Spatial System Reference Identifier.</span></span> <span data-ttu-id="b588d-217">Solo es válido para las propiedades de los tipos espaciales.</span><span class="sxs-lookup"><span data-stu-id="b588d-217">Valid only for properties of spatial types.</span></span><span data-ttu-id="b588d-218">Para obtener más información, consulte [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx) .</span><span class="sxs-lookup"><span data-stu-id="b588d-218">   For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx)</span></span> |
| <span data-ttu-id="b588d-219">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="b588d-219">**Unicode**</span></span>                                                             | <span data-ttu-id="b588d-220">No</span><span class="sxs-lookup"><span data-stu-id="b588d-220">No</span></span>          | <span data-ttu-id="b588d-221">**True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena Unicode.</span><span class="sxs-lookup"><span data-stu-id="b588d-221">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                                |
| <span data-ttu-id="b588d-222">**Intercalación**</span><span class="sxs-lookup"><span data-stu-id="b588d-222">**Collation**</span></span>                                                           | <span data-ttu-id="b588d-223">No</span><span class="sxs-lookup"><span data-stu-id="b588d-223">No</span></span>          | <span data-ttu-id="b588d-224">Cadena que especifica la secuencia de intercalación que se usará en el origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-224">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                    |

 

> [!NOTE]
> <span data-ttu-id="b588d-225">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-225">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="b588d-226">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-226">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-227">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-227">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-228">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-228">Example</span></span>

<span data-ttu-id="b588d-229">En el ejemplo siguiente se muestra una función definida por el modelo que utiliza un elemento **CollectionType** para especificar que la función devuelve una colección de tipos de entidad **Person** (tal y como se especifica con el atributo **elementType** ).</span><span class="sxs-lookup"><span data-stu-id="b588d-229">The following example shows a model-defined function that that uses a **CollectionType** element to specify that the function returns a collection of **Person** entity types (as specified with the **ElementType** attribute).</span></span>

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
 

<span data-ttu-id="b588d-230">En el ejemplo siguiente se muestra una función definida por el modelo que utiliza un elemento **CollectionType** para especificar que la función devuelve una colección de filas (tal y como se especifica en el elemento **RowType** ).</span><span class="sxs-lookup"><span data-stu-id="b588d-230">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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
 

<span data-ttu-id="b588d-231">En el ejemplo siguiente se muestra una función definida por el modelo que usa el elemento **CollectionType** para especificar que la función acepta como parámetro una colección de tipos de entidad **Department** .</span><span class="sxs-lookup"><span data-stu-id="b588d-231">The following example shows a model-defined function that uses the **CollectionType** element to specify that the function accepts as a parameter a collection of **Department** entity types.</span></span>

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
 

 

## <a name="complextype-element-csdl"></a><span data-ttu-id="b588d-232">ComplexType (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-232">ComplexType Element (CSDL)</span></span>

<span data-ttu-id="b588d-233">Un elemento **complexType** define una estructura de datos formada por propiedades **EdmSimpleType** u otros tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="b588d-233">A **ComplexType** element defines a data structure composed of **EdmSimpleType** properties or other complex types.</span></span><span data-ttu-id="b588d-234">Un tipo complejo puede ser una propiedad de un tipo de entidad o de otro tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="b588d-234">  A complex type can be a property of an entity type or another complex type.</span></span> <span data-ttu-id="b588d-235">Un tipo complejo se parece a un tipo de entidad en que también define datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-235">A complex type is similar to an entity type in that a complex type defines data.</span></span> <span data-ttu-id="b588d-236">Sin embargo, existen algunas diferencias clave entre los tipos complejos y los tipos de entidad:</span><span class="sxs-lookup"><span data-stu-id="b588d-236">However, there are some key differences between complex types and entity types:</span></span>

-   <span data-ttu-id="b588d-237">Los tipos complejos no tienen identidades (o claves) y, por consiguiente, no pueden existir de forma independiente.</span><span class="sxs-lookup"><span data-stu-id="b588d-237">Complex types do not have identities (or keys) and therefore cannot exist independently.</span></span> <span data-ttu-id="b588d-238">Los tipos complejos solo pueden existir como propiedades de tipos de entidad u otros tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="b588d-238">Complex types can only exist as properties of entity types or other complex types.</span></span>
-   <span data-ttu-id="b588d-239">Los tipos complejos no pueden participar en asociaciones.</span><span class="sxs-lookup"><span data-stu-id="b588d-239">Complex types cannot participate in associations.</span></span> <span data-ttu-id="b588d-240">Los extremos de una asociación no pueden ser tipos complejos y, por consiguiente, no se pueden definir propiedades de navegación para tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="b588d-240">Neither end of an association can be a complex type, and therefore navigation properties cannot be defined for complex types.</span></span>
-   <span data-ttu-id="b588d-241">Una propiedad de tipo complejo no puede tener un valor nulo, aunque las propiedades escalares de un tipo complejo se pueden establecer cada una con el valor nulo.</span><span class="sxs-lookup"><span data-stu-id="b588d-241">A complex type property cannot have a null value, though the scalar properties of a complex type may each be set to null.</span></span>

<span data-ttu-id="b588d-242">Un elemento **complexType** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-242">A **ComplexType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-243">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-243">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b588d-244">Property (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-244">Property (zero or more elements)</span></span>
-   <span data-ttu-id="b588d-245">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-245">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="b588d-246">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **complexType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-246">The table below describes the attributes that can be applied to the **ComplexType** element.</span></span>

| <span data-ttu-id="b588d-247">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-247">Attribute Name</span></span>                                                                                                 | <span data-ttu-id="b588d-248">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-248">Is Required</span></span> | <span data-ttu-id="b588d-249">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-249">Value</span></span>                                                                                                                                                                               |
|:---------------------------------------------------------------------------------------------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-250">Nombre</span><span class="sxs-lookup"><span data-stu-id="b588d-250">Name</span></span>                                                                                                           | <span data-ttu-id="b588d-251">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-251">Yes</span></span>         | <span data-ttu-id="b588d-252">El nombre del tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="b588d-252">The name of the complex type.</span></span> <span data-ttu-id="b588d-253">El nombre de un tipo complejo no puede ser igual que el nombre de otro tipo complejo, tipo de entidad o asociación que esté dentro del ámbito del modelo.</span><span class="sxs-lookup"><span data-stu-id="b588d-253">The name of a complex type cannot be the same as the name of another complex type, entity type, or association that is within the scope of the model.</span></span> |
| <span data-ttu-id="b588d-254">BaseType</span><span class="sxs-lookup"><span data-stu-id="b588d-254">BaseType</span></span>                                                                                                       | <span data-ttu-id="b588d-255">No</span><span class="sxs-lookup"><span data-stu-id="b588d-255">No</span></span>          | <span data-ttu-id="b588d-256">El nombre de otro tipo complejo que es el tipo base del tipo complejo que se define.</span><span class="sxs-lookup"><span data-stu-id="b588d-256">The name of another complex type that is the base type of the complex type that is being defined.</span></span> <br/> [!NOTE]                                                                     |
| <span data-ttu-id="b588d-257">> este atributo no es aplicable en CSDL v1.</span><span class="sxs-lookup"><span data-stu-id="b588d-257">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="b588d-258">La herencia de los tipos complejos no se admite en esa versión.</span><span class="sxs-lookup"><span data-stu-id="b588d-258">Inheritance for complex types is not supported in that version.</span></span> |             |                                                                                                                                                                                     |
| <span data-ttu-id="b588d-259">Descripción breve</span><span class="sxs-lookup"><span data-stu-id="b588d-259">Abstract</span></span>                                                                                                       | <span data-ttu-id="b588d-260">No</span><span class="sxs-lookup"><span data-stu-id="b588d-260">No</span></span>          | <span data-ttu-id="b588d-261">**True** o **false** (valor predeterminado) en función de si el tipo complejo es un tipo abstracto.</span><span class="sxs-lookup"><span data-stu-id="b588d-261">**True** or **False** (the default value) depending on whether the complex type is an abstract type.</span></span> <br/> [!NOTE]                                                                  |
| <span data-ttu-id="b588d-262">> este atributo no es aplicable en CSDL v1.</span><span class="sxs-lookup"><span data-stu-id="b588d-262">> This attribute is not applicable in CSDL v1.</span></span> <span data-ttu-id="b588d-263">Los tipos complejos de esa versión no pueden ser tipos abstractos.</span><span class="sxs-lookup"><span data-stu-id="b588d-263">Complex types in that version cannot be abstract types.</span></span>         |             |                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="b588d-264">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **complexType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-264">Any number of annotation attributes (custom XML attributes) may be applied to the **ComplexType** element.</span></span> <span data-ttu-id="b588d-265">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-265">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-266">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-266">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-267">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-267">Example</span></span>

<span data-ttu-id="b588d-268">En el ejemplo siguiente se muestra un tipo complejo, **Address**, con las propiedades **EdmSimpleType** **StreetAddress**, **City**, **StateOrProvince**, **Country**y **PostalCode**.</span><span class="sxs-lookup"><span data-stu-id="b588d-268">The following example shows a complex type, **Address**, with the **EdmSimpleType** properties **StreetAddress**, **City**, **StateOrProvince**, **Country**, and **PostalCode**.</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

<span data-ttu-id="b588d-269">Para definir la **Dirección** de tipo complejo (anterior) como una propiedad de un tipo de entidad, debe declarar el tipo de propiedad en la definición de tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-269">To define the complex type **Address** (above) as a property of an entity type, you must declare the property type in the entity type definition.</span></span> <span data-ttu-id="b588d-270">En el ejemplo siguiente se muestra la propiedad **Address** como un tipo complejo en un tipo de entidad (**publicador**):</span><span class="sxs-lookup"><span data-stu-id="b588d-270">The following example shows the **Address** property as a complex type on an entity type (**Publisher**):</span></span>

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
 

 

## <a name="definingexpression-element-csdl"></a><span data-ttu-id="b588d-271">DefiningExpression (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-271">DefiningExpression Element (CSDL)</span></span>

<span data-ttu-id="b588d-272">El elemento **DefiningExpression** en el lenguaje de definición de esquemas conceptuales (CSDL) contiene una expresión Entity SQL que define una función en el modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-272">The **DefiningExpression** element in conceptual schema definition language (CSDL) contains an Entity SQL expression that defines a function in the conceptual model.</span></span>  

> [!NOTE]
> <span data-ttu-id="b588d-273">Para fines de validación, un elemento **DefiningExpression** puede contener contenido arbitrario.</span><span class="sxs-lookup"><span data-stu-id="b588d-273">For validation purposes, a **DefiningExpression** element can contain arbitrary content.</span></span> <span data-ttu-id="b588d-274">Sin embargo, Entity Framework producirá una excepción en tiempo de ejecución si un elemento **DefiningExpression** no contiene Entity SQL válidos.</span><span class="sxs-lookup"><span data-stu-id="b588d-274">However, Entity Framework will throw an exception at runtime if a **DefiningExpression** element does not contain valid Entity SQL.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-275">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-275">Applicable Attributes</span></span>

<span data-ttu-id="b588d-276">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **DefiningExpression** .</span><span class="sxs-lookup"><span data-stu-id="b588d-276">Any number of annotation attributes (custom XML attributes) may be applied to the **DefiningExpression** element.</span></span> <span data-ttu-id="b588d-277">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-277">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-278">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-278">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b588d-279">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-279">Example</span></span>

<span data-ttu-id="b588d-280">En el ejemplo siguiente se usa un elemento **DefiningExpression** para definir una función que devuelve el número de años transcurridos desde que se publicó un libro.</span><span class="sxs-lookup"><span data-stu-id="b588d-280">The following example uses a **DefiningExpression** element to define a function that returns the number of years since a book was published.</span></span> <span data-ttu-id="b588d-281">El contenido del elemento **DefiningExpression** se escribe en Entity SQL.</span><span class="sxs-lookup"><span data-stu-id="b588d-281">The content of the **DefiningExpression** element is written in Entity SQL.</span></span>

``` xml
 <Function Name="GetYearsInPrint" ReturnType="Edm.Int32" >
       <Parameter Name="book" Type="BooksModel.Book" />
       <DefiningExpression>
         Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
       </DefiningExpression>
     </Function>
```
 

 

## <a name="dependent-element-csdl"></a><span data-ttu-id="b588d-282">Dependent (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-282">Dependent Element (CSDL)</span></span>

<span data-ttu-id="b588d-283">El elemento **dependiente** en el lenguaje de definición de esquemas conceptuales (CSDL) es un elemento secundario del elemento ReferentialConstraint y define el extremo dependiente de una restricción referencial.</span><span class="sxs-lookup"><span data-stu-id="b588d-283">The **Dependent** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element and defines the dependent end of a referential constraint.</span></span> <span data-ttu-id="b588d-284">Un elemento **ReferentialConstraint** define una funcionalidad similar a una restricción de integridad referencial en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="b588d-284">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="b588d-285">Del mismo modo que una columna (o columnas) de una tabla de base de datos puede hacer referencia a la clave principal de otra tabla, una propiedad (o propiedades) de un tipo de entidad puede hacer referencia a la clave de entidad de otro tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-285">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="b588d-286">El tipo de entidad al que se hace referencia se denomina *extremo principal* de la restricción.</span><span class="sxs-lookup"><span data-stu-id="b588d-286">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="b588d-287">El tipo de entidad que hace referencia al extremo principal se denomina *extremo dependiente* de la restricción.</span><span class="sxs-lookup"><span data-stu-id="b588d-287">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="b588d-288">Los elementos **PropertyRef** se usan para especificar qué claves hacen referencia al extremo principal.</span><span class="sxs-lookup"><span data-stu-id="b588d-288">**PropertyRef** elements are used to specify which keys reference the principal end.</span></span>

<span data-ttu-id="b588d-289">El elemento **dependiente** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-289">The **Dependent** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-290">PropertyRef (uno o varios elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-290">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="b588d-291">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-291">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-292">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-292">Applicable Attributes</span></span>

<span data-ttu-id="b588d-293">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **dependiente** .</span><span class="sxs-lookup"><span data-stu-id="b588d-293">The table below describes the attributes that can be applied to the **Dependent** element.</span></span>

| <span data-ttu-id="b588d-294">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-294">Attribute Name</span></span> | <span data-ttu-id="b588d-295">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-295">Is Required</span></span> | <span data-ttu-id="b588d-296">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-296">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="b588d-297">**Rol**</span><span class="sxs-lookup"><span data-stu-id="b588d-297">**Role**</span></span>       | <span data-ttu-id="b588d-298">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-298">Yes</span></span>         | <span data-ttu-id="b588d-299">Nombre del tipo de entidad del extremo dependiente de la asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-299">The name of the entity type on the dependent end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-300">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **dependiente** .</span><span class="sxs-lookup"><span data-stu-id="b588d-300">Any number of annotation attributes (custom XML attributes) may be applied to the **Dependent** element.</span></span> <span data-ttu-id="b588d-301">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-301">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-302">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-302">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-303">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-303">Example</span></span>

<span data-ttu-id="b588d-304">En el ejemplo siguiente se muestra un elemento **ReferentialConstraint** que se usa como parte de la definición de la Asociación **PublishedBy** .</span><span class="sxs-lookup"><span data-stu-id="b588d-304">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="b588d-305">La propiedad **PublisherId** del tipo de entidad **book** constituye el extremo dependiente de la restricción referencial.</span><span class="sxs-lookup"><span data-stu-id="b588d-305">The **PublisherId** property of the **Book** entity type makes up the dependent end of the referential constraint.</span></span>

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
 

 

## <a name="documentation-element-csdl"></a><span data-ttu-id="b588d-306">Documentation (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-306">Documentation Element (CSDL)</span></span>

<span data-ttu-id="b588d-307">El elemento **Documentation** del lenguaje de definición de esquemas conceptuales (CSDL) se puede usar para proporcionar información sobre un objeto que se define en un elemento primario.</span><span class="sxs-lookup"><span data-stu-id="b588d-307">The **Documentation** element in conceptual schema definition language (CSDL) can be used to provide information about an object that is defined in a parent element.</span></span> <span data-ttu-id="b588d-308">En un archivo. edmx, cuando el elemento **Documentation** es un elemento secundario de un elemento que aparece como un objeto en la superficie de diseño del diseñador EF (como una entidad, asociación o propiedad), el contenido del elemento **Documentation** aparecerá en la ventana **propiedades** de Visual Studio para el objeto.</span><span class="sxs-lookup"><span data-stu-id="b588d-308">In an .edmx file, when the **Documentation** element is a child of an element that appears as an object on the design surface of the EF Designer  (such as an entity, association, or property), the contents of the **Documentation** element will appear in the Visual Studio **Properties** window for the object.</span></span>

<span data-ttu-id="b588d-309">El elemento **Documentation** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-309">The **Documentation** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-310">**Summary**: breve descripción del elemento primario.</span><span class="sxs-lookup"><span data-stu-id="b588d-310">**Summary**: A brief description of the parent element.</span></span> <span data-ttu-id="b588d-311">(cero o un elemento).</span><span class="sxs-lookup"><span data-stu-id="b588d-311">(zero or one element)</span></span>
-   <span data-ttu-id="b588d-312">**LongDescription**: una descripción amplia del elemento primario.</span><span class="sxs-lookup"><span data-stu-id="b588d-312">**LongDescription**: An extensive description of the parent element.</span></span> <span data-ttu-id="b588d-313">(cero o un elemento).</span><span class="sxs-lookup"><span data-stu-id="b588d-313">(zero or one element)</span></span>
-   <span data-ttu-id="b588d-314">Elementos de anotación.</span><span class="sxs-lookup"><span data-stu-id="b588d-314">Annotation elements.</span></span> <span data-ttu-id="b588d-315">(cero o más elementos).</span><span class="sxs-lookup"><span data-stu-id="b588d-315">(zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-316">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-316">Applicable Attributes</span></span>

<span data-ttu-id="b588d-317">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Documentation** .</span><span class="sxs-lookup"><span data-stu-id="b588d-317">Any number of annotation attributes (custom XML attributes) may be applied to the **Documentation** element.</span></span> <span data-ttu-id="b588d-318">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-318">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-319">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-319">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b588d-320">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-320">Example</span></span>

<span data-ttu-id="b588d-321">En el ejemplo siguiente se muestra el elemento **Documentation** como un elemento secundario de un elemento EntityType.</span><span class="sxs-lookup"><span data-stu-id="b588d-321">The following example shows the **Documentation** element as a child element of an EntityType element.</span></span> <span data-ttu-id="b588d-322">Si el fragmento de código siguiente estaba en el contenido CSDL de un archivo. edmx, el contenido de los elementos **Summary** y **longDescription** aparecerá en la ventana **propiedades** de Visual Studio al hacer clic en el `Customer` tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-322">If the snippet below were in the CSDL content of an .edmx file, the contents of the **Summary** and **LongDescription** elements would appear in the Visual Studio **Properties** window when you click on the `Customer` entity type.</span></span>

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
 

 

## <a name="end-element-csdl"></a><span data-ttu-id="b588d-323">End (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-323">End Element (CSDL)</span></span>

<span data-ttu-id="b588d-324">El elemento **End** en el lenguaje de definición de esquemas conceptuales (CSDL) puede ser un elemento secundario del elemento Association o del elemento AssociationSet.</span><span class="sxs-lookup"><span data-stu-id="b588d-324">The **End** element in conceptual schema definition language (CSDL) can be a child of the Association element or the AssociationSet element.</span></span> <span data-ttu-id="b588d-325">En cada caso, el rol del elemento **final** es diferente y los atributos correspondientes son diferentes.</span><span class="sxs-lookup"><span data-stu-id="b588d-325">In each case, the role of the **End** element is different and the applicable attributes are different.</span></span>

### <a name="end-element-as-a-child-of-the-association-element"></a><span data-ttu-id="b588d-326">Elemento End como elemento secundario del elemento Association</span><span class="sxs-lookup"><span data-stu-id="b588d-326">End Element as a Child of the Association Element</span></span>

<span data-ttu-id="b588d-327">Un elemento **End** (como elemento secundario del elemento **Association** ) identifica el tipo de entidad en un extremo de una asociación y el número de instancias de tipo de entidad que pueden existir en ese extremo de una asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-327">An **End** element (as a child of the **Association** element) identifies the entity type on one end of an association and the number of entity type instances that can exist at that end of an association.</span></span> <span data-ttu-id="b588d-328">Los extremos de asociación se definen como parte de una asociación, y esta debe tener exactamente dos extremos.</span><span class="sxs-lookup"><span data-stu-id="b588d-328">Association ends are defined as part of an association; an association must have exactly two association ends.</span></span> <span data-ttu-id="b588d-329">Es posible obtener acceso a las instancias de tipo de entidad situadas en un extremo de la asociación a través de las propiedades de navegación o las claves externas si estas se exponen en un tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-329">Entity type instances at one end of an association can be accessed through navigation properties or foreign keys if they are exposed on an entity type.</span></span>  

<span data-ttu-id="b588d-330">Un elemento **End** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-330">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-331">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-331">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b588d-332">OnDelete (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-332">OnDelete (zero or one element)</span></span>
-   <span data-ttu-id="b588d-333">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-333">Annotation elements (zero or more elements)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="b588d-334">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-334">Applicable Attributes</span></span>

<span data-ttu-id="b588d-335">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **End** cuando es el elemento secundario de un elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="b588d-335">The following table describes the attributes that can be applied to the **End** element when it is the child of an **Association** element.</span></span>

| <span data-ttu-id="b588d-336">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-336">Attribute Name</span></span>   | <span data-ttu-id="b588d-337">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-337">Is Required</span></span> | <span data-ttu-id="b588d-338">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-338">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                              |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-339">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="b588d-339">**Type**</span></span>         | <span data-ttu-id="b588d-340">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-340">Yes</span></span>         | <span data-ttu-id="b588d-341">Nombre del tipo de entidad de un extremo de la asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-341">The name of the entity type at one end of the association.</span></span>                                                                                                                                                                                                                                                                                                                                                         |
| <span data-ttu-id="b588d-342">**Rol**</span><span class="sxs-lookup"><span data-stu-id="b588d-342">**Role**</span></span>         | <span data-ttu-id="b588d-343">No</span><span class="sxs-lookup"><span data-stu-id="b588d-343">No</span></span>          | <span data-ttu-id="b588d-344">Nombre para el extremo de la asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-344">A name for the association end.</span></span> <span data-ttu-id="b588d-345">Si no se proporciona ningún nombre, se usará el nombre del tipo de entidad del extremo de la asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-345">If no name is provided, the name of the entity type on the association end will be used.</span></span>                                                                                                                                                                                                                                                                                           |
| <span data-ttu-id="b588d-346">**Multiplicidad**</span><span class="sxs-lookup"><span data-stu-id="b588d-346">**Multiplicity**</span></span> | <span data-ttu-id="b588d-347">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-347">Yes</span></span>         | <span data-ttu-id="b588d-348">**1**, **0.. 1**o, **\*** dependiendo del número de instancias de tipo de entidad que pueden estar al final de la asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-348">**1**, **0..1**, or **\*** depending on the number of entity type instances that can be at the end of the association.</span></span> <br/> <span data-ttu-id="b588d-349">**1** indica que existe exactamente una instancia de tipo de entidad en el extremo de la asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-349">**1** indicates that exactly one entity type instance exists at the association end.</span></span> <br/> <span data-ttu-id="b588d-350">**0.. 1** indica que hay cero o una instancia de tipo de entidad en el extremo de la asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-350">**0..1** indicates that zero or one entity type instances exist at the association end.</span></span> <br/> <span data-ttu-id="b588d-351">**\*** indica que hay cero, una o más instancias de tipo de entidad en el extremo de la asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-351">**\*** indicates that zero, one, or more entity type instances exist at the association end.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-352">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **final** .</span><span class="sxs-lookup"><span data-stu-id="b588d-352">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="b588d-353">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-353">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-354">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-354">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="b588d-355">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-355">Example</span></span>

<span data-ttu-id="b588d-356">En el ejemplo siguiente se muestra un elemento **Association** que define la Asociación **CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="b588d-356">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="b588d-357">Los valores de **multiplicidad** de cada **extremo** de la Asociación indican que se pueden asociar muchos **pedidos** a un **cliente**, pero solo un **cliente** puede asociarse a un **pedido**.</span><span class="sxs-lookup"><span data-stu-id="b588d-357">The **Multiplicity** values for each **End** of the association indicate that many **Orders** can be associated with a **Customer**, but only one **Customer** can be associated with an **Order**.</span></span> <span data-ttu-id="b588d-358">Además, el elemento **aleliminar** indica que todos los **pedidos** relacionados con un **cliente** determinado y que se han cargado en el ObjectContext se eliminarán si se elimina el **cliente** .</span><span class="sxs-lookup"><span data-stu-id="b588d-358">Additionally, the **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and that have been loaded into the ObjectContext will be deleted if the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1" />
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*">
         <OnDelete Action="Cascade" />
   </End>
 </Association>
```
 

### <a name="end-element-as-a-child-of-the-associationset-element"></a><span data-ttu-id="b588d-359">Elemento End como elemento secundario del elemento AssociationSet</span><span class="sxs-lookup"><span data-stu-id="b588d-359">End Element as a Child of the AssociationSet Element</span></span>

<span data-ttu-id="b588d-360">El elemento **End** especifica un extremo de un conjunto de asociaciones.</span><span class="sxs-lookup"><span data-stu-id="b588d-360">The **End** element specifies one end of an association set.</span></span> <span data-ttu-id="b588d-361">El elemento **AssociationSet** debe contener dos elementos **End** .</span><span class="sxs-lookup"><span data-stu-id="b588d-361">The **AssociationSet** element must contain two **End** elements.</span></span> <span data-ttu-id="b588d-362">La información contenida en un elemento **End** se utiliza para asignar un conjunto de asociaciones a un origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-362">The information contained in an **End** element is used in mapping an association set to a data source.</span></span>

<span data-ttu-id="b588d-363">Un elemento **End** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-363">An **End** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-364">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-364">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b588d-365">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-365">Annotation elements (zero or more elements)</span></span>

> [!NOTE]
> <span data-ttu-id="b588d-366">Los elementos de anotación deben aparecer después de todos los demás elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="b588d-366">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="b588d-367">Los elementos Annotation solo se permiten en CSDL V2 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="b588d-367">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="b588d-368">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-368">Applicable Attributes</span></span>

<span data-ttu-id="b588d-369">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **End** cuando es el elemento secundario de un elemento **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="b588d-369">The following table describes the attributes that can be applied to the **End** element when it is the child of an **AssociationSet** element.</span></span>

| <span data-ttu-id="b588d-370">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-370">Attribute Name</span></span> | <span data-ttu-id="b588d-371">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-371">Is Required</span></span> | <span data-ttu-id="b588d-372">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-372">Value</span></span>                                                                                                                                                                                                                 |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-373">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="b588d-373">**EntitySet**</span></span>  | <span data-ttu-id="b588d-374">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-374">Yes</span></span>         | <span data-ttu-id="b588d-375">Nombre del elemento **EntitySet** que define un extremo del elemento primario **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="b588d-375">The name of the **EntitySet** element that defines one end of the parent **AssociationSet** element.</span></span> <span data-ttu-id="b588d-376">El elemento **EntitySet** debe definirse en el mismo contenedor de entidades que el elemento primario **AssociationSet** .</span><span class="sxs-lookup"><span data-stu-id="b588d-376">The **EntitySet** element must be defined in the same entity container as the parent **AssociationSet** element.</span></span> |
| <span data-ttu-id="b588d-377">**Rol**</span><span class="sxs-lookup"><span data-stu-id="b588d-377">**Role**</span></span>       | <span data-ttu-id="b588d-378">No</span><span class="sxs-lookup"><span data-stu-id="b588d-378">No</span></span>          | <span data-ttu-id="b588d-379">Nombre del extremo del conjunto de asociaciones.</span><span class="sxs-lookup"><span data-stu-id="b588d-379">The name of the association set end.</span></span> <span data-ttu-id="b588d-380">Si no se utiliza el atributo **role** , el nombre del extremo del conjunto de asociaciones será el nombre del conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="b588d-380">If the **Role** attribute is not used, the name of the association set end will be the name of the entity set.</span></span>                                                                   |

 

> [!NOTE]
> <span data-ttu-id="b588d-381">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **final** .</span><span class="sxs-lookup"><span data-stu-id="b588d-381">Any number of annotation attributes (custom XML attributes) may be applied to the **End** element.</span></span> <span data-ttu-id="b588d-382">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-382">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-383">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-383">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="b588d-384">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-384">Example</span></span>

<span data-ttu-id="b588d-385">En el ejemplo siguiente se muestra un elemento **EntityContainer** con dos elementos **AssociationSet** , cada uno con dos elementos **End** :</span><span class="sxs-lookup"><span data-stu-id="b588d-385">The following example shows an **EntityContainer** element with two **AssociationSet** elements, each with two **End** elements:</span></span>

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
 

 

## <a name="entitycontainer-element-csdl"></a><span data-ttu-id="b588d-386">EntityContainer (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-386">EntityContainer Element (CSDL)</span></span>

<span data-ttu-id="b588d-387">El elemento **EntityContainer** en el lenguaje de definición de esquemas conceptuales (CSDL) es un contenedor lógico para conjuntos de entidades, conjuntos de asociaciones e importaciones de funciones.</span><span class="sxs-lookup"><span data-stu-id="b588d-387">The **EntityContainer** element in conceptual schema definition language (CSDL) is a logical container for entity sets, association sets, and function imports.</span></span> <span data-ttu-id="b588d-388">Un contenedor de entidades de modelo conceptual se asigna a un contenedor de entidades de modelo de almacenamiento a través del elemento EntityContainerMapping.</span><span class="sxs-lookup"><span data-stu-id="b588d-388">A conceptual model entity container maps to a storage model entity container through the EntityContainerMapping element.</span></span> <span data-ttu-id="b588d-389">Un contenedor de entidades de modelo de almacenamiento describe la estructura de la base de datos: los conjuntos de entidades describen las tablas, los conjuntos de asociaciones describen las restricciones de clave externa y las importaciones de función describen los procedimientos almacenados en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-389">A storage model entity container describes the structure of the database: entity sets describe tables, association sets describe foreign key constraints, and function imports describe stored procedures in a database.</span></span>

<span data-ttu-id="b588d-390">Un elemento **EntityContainer** puede tener cero o un elemento de documentación.</span><span class="sxs-lookup"><span data-stu-id="b588d-390">An **EntityContainer** element can have zero or one Documentation elements.</span></span> <span data-ttu-id="b588d-391">Si hay un elemento de **documentación** , debe preceder a todos los elementos **EntitySet**, **AssociationSet**y **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="b588d-391">If a **Documentation** element is present, it must precede all **EntitySet**, **AssociationSet**, and **FunctionImport** elements.</span></span>

<span data-ttu-id="b588d-392">Un elemento **EntityContainer** puede tener cero o más de los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-392">An **EntityContainer** element can have zero or more of the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-393">EntitySet</span><span class="sxs-lookup"><span data-stu-id="b588d-393">EntitySet</span></span>
-   <span data-ttu-id="b588d-394">AssociationSet</span><span class="sxs-lookup"><span data-stu-id="b588d-394">AssociationSet</span></span>
-   <span data-ttu-id="b588d-395">FunctionImport</span><span class="sxs-lookup"><span data-stu-id="b588d-395">FunctionImport</span></span>
-   <span data-ttu-id="b588d-396">Elementos Annotation</span><span class="sxs-lookup"><span data-stu-id="b588d-396">Annotation elements</span></span>

<span data-ttu-id="b588d-397">Puede extender un elemento **EntityContainer** para incluir el contenido de otro **EntityContainer** que esté en el mismo espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="b588d-397">You can extend an **EntityContainer** element to include the contents of another **EntityContainer** that is within the same namespace.</span></span> <span data-ttu-id="b588d-398">Para incluir el contenido de otro **EntityContainer**, en el elemento **EntityContainer** de referencia, establezca el valor del atributo **extends** en el nombre del elemento **EntityContainer** que desea incluir.</span><span class="sxs-lookup"><span data-stu-id="b588d-398">To include the contents of another **EntityContainer**, in the referencing **EntityContainer** element, set the value of the **Extends** attribute to the name of the **EntityContainer** element that you want to include.</span></span> <span data-ttu-id="b588d-399">Todos los elementos secundarios del elemento **EntityContainer** incluido se tratarán como elementos secundarios del elemento **EntityContainer** que hace la referencia.</span><span class="sxs-lookup"><span data-stu-id="b588d-399">All child elements of the included **EntityContainer** element will be treated as child elements of the referencing **EntityContainer** element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-400">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-400">Applicable Attributes</span></span>

<span data-ttu-id="b588d-401">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **using** .</span><span class="sxs-lookup"><span data-stu-id="b588d-401">The table below describes the attributes that can be applied to the **Using** element.</span></span>

| <span data-ttu-id="b588d-402">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-402">Attribute Name</span></span> | <span data-ttu-id="b588d-403">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-403">Is Required</span></span> | <span data-ttu-id="b588d-404">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-404">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="b588d-405">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-405">**Name**</span></span>       | <span data-ttu-id="b588d-406">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-406">Yes</span></span>         | <span data-ttu-id="b588d-407">Nombre del contenedor de entidades.</span><span class="sxs-lookup"><span data-stu-id="b588d-407">The name of the entity container.</span></span>                               |
| <span data-ttu-id="b588d-408">**Llegar**</span><span class="sxs-lookup"><span data-stu-id="b588d-408">**Extends**</span></span>    | <span data-ttu-id="b588d-409">No</span><span class="sxs-lookup"><span data-stu-id="b588d-409">No</span></span>          | <span data-ttu-id="b588d-410">Nombre de otro contenedor de entidades dentro del mismo espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="b588d-410">The name of another entity container within the same namespace.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-411">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **EntityContainer** .</span><span class="sxs-lookup"><span data-stu-id="b588d-411">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityContainer** element.</span></span> <span data-ttu-id="b588d-412">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-412">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-413">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-413">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-414">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-414">Example</span></span>

<span data-ttu-id="b588d-415">En el ejemplo siguiente se muestra un elemento **EntityContainer** que define tres conjuntos de entidades y dos conjuntos de asociaciones.</span><span class="sxs-lookup"><span data-stu-id="b588d-415">The following example shows an **EntityContainer** element that defines three entity sets and two association sets.</span></span>

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
 

 

## <a name="entityset-element-csdl"></a><span data-ttu-id="b588d-416">EntitySet (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-416">EntitySet Element (CSDL)</span></span>

<span data-ttu-id="b588d-417">El elemento **EntitySet** del lenguaje de definición de esquemas conceptuales es un contenedor lógico para las instancias de un tipo de entidad y las instancias de cualquier tipo que se deriva de ese tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-417">The **EntitySet** element in conceptual schema definition language is a logical container for instances of an entity type and instances of any type that is derived from that entity type.</span></span> <span data-ttu-id="b588d-418">La relación entre un tipo de entidad y un conjunto de entidades es análoga a la relación entre una fila y una tabla en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="b588d-418">The relationship between an entity type and an entity set is analogous to the relationship between a row and a table in a relational database.</span></span> <span data-ttu-id="b588d-419">Igual que una fila, un tipo de entidad define un conjunto de datos relacionados y, lo mismo que una tabla, un conjunto de entidades contiene instancias de esa definición.</span><span class="sxs-lookup"><span data-stu-id="b588d-419">Like a row, an entity type defines a set of related data, and, like a table, an entity set contains instances of that definition.</span></span> <span data-ttu-id="b588d-420">Un conjunto de entidades proporciona una construcción para agrupar las instancias del tipo de entidad para que se pueden asignar a las estructuras de datos relacionadas en un origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-420">An entity set provides a construct for grouping entity type instances so that they can be mapped to related data structures in a data source.</span></span>  

<span data-ttu-id="b588d-421">Se pueden definir varios conjuntos de entidades para un tipo de entidad determinado.</span><span class="sxs-lookup"><span data-stu-id="b588d-421">More than one entity set for a particular entity type may be defined.</span></span>

> [!NOTE]
> <span data-ttu-id="b588d-422">El diseñador de EF no es compatible con los modelos conceptuales que contienen varios conjuntos de entidades por tipo.</span><span class="sxs-lookup"><span data-stu-id="b588d-422">The EF Designer does not support conceptual models that contain multiple entity sets per type.</span></span>

 

<span data-ttu-id="b588d-423">El elemento **EntitySet** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-423">The **EntitySet** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-424">Elemento Documentation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-424">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="b588d-425">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-425">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-426">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-426">Applicable Attributes</span></span>

<span data-ttu-id="b588d-427">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="b588d-427">The table below describes the attributes that can be applied to the **EntitySet** element.</span></span>

| <span data-ttu-id="b588d-428">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-428">Attribute Name</span></span> | <span data-ttu-id="b588d-429">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-429">Is Required</span></span> | <span data-ttu-id="b588d-430">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-430">Value</span></span>                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-431">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-431">**Name**</span></span>       | <span data-ttu-id="b588d-432">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-432">Yes</span></span>         | <span data-ttu-id="b588d-433">Nombre del conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="b588d-433">The name of the entity set.</span></span>                                                              |
| <span data-ttu-id="b588d-434">**EntityType**</span><span class="sxs-lookup"><span data-stu-id="b588d-434">**EntityType**</span></span> | <span data-ttu-id="b588d-435">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-435">Yes</span></span>         | <span data-ttu-id="b588d-436">El nombre completo del tipo de entidad para el que el conjunto de entidades contiene las instancias.</span><span class="sxs-lookup"><span data-stu-id="b588d-436">The fully-qualified name of the entity type for which the entity set contains instances.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-437">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **EntitySet** .</span><span class="sxs-lookup"><span data-stu-id="b588d-437">Any number of annotation attributes (custom XML attributes) may be applied to the **EntitySet** element.</span></span> <span data-ttu-id="b588d-438">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-438">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-439">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-439">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-440">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-440">Example</span></span>

<span data-ttu-id="b588d-441">En el ejemplo siguiente se muestra un elemento **EntityContainer** con tres elementos **EntitySet** :</span><span class="sxs-lookup"><span data-stu-id="b588d-441">The following example shows an **EntityContainer** element with three **EntitySet** elements:</span></span>

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
 

<span data-ttu-id="b588d-442">Es posible definir varios conjuntos de entidades por tipo (MEST).</span><span class="sxs-lookup"><span data-stu-id="b588d-442">It is possible to define multiple entity sets per type (MEST).</span></span> <span data-ttu-id="b588d-443">En el ejemplo siguiente se define un contenedor de entidades con dos conjuntos de entidades para el tipo de entidad **book** :</span><span class="sxs-lookup"><span data-stu-id="b588d-443">The following example defines an entity container with two entity sets for the **Book** entity type:</span></span>

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
 

 

## <a name="entitytype-element-csdl"></a><span data-ttu-id="b588d-444">EntityType (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-444">EntityType Element (CSDL)</span></span>

<span data-ttu-id="b588d-445">El elemento **EntityType** representa la estructura de un concepto de nivel superior, como un cliente o un pedido, en un modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-445">The **EntityType** element represents the structure of a top-level concept, such as a customer or order, in a conceptual model.</span></span> <span data-ttu-id="b588d-446">Un tipo de entidad es una plantilla para las instancias de los tipos de entidad de una aplicación.</span><span class="sxs-lookup"><span data-stu-id="b588d-446">An entity type is a template for instances of entity types in an application.</span></span> <span data-ttu-id="b588d-447">Cada plantilla contiene la información siguiente:</span><span class="sxs-lookup"><span data-stu-id="b588d-447">Each template contains the following information:</span></span>

-   <span data-ttu-id="b588d-448">Un nombre único.</span><span class="sxs-lookup"><span data-stu-id="b588d-448">A unique name.</span></span> <span data-ttu-id="b588d-449">(Requerido)</span><span class="sxs-lookup"><span data-stu-id="b588d-449">(Required.)</span></span>
-   <span data-ttu-id="b588d-450">Una clave de entidad definida por una o varias propiedades.</span><span class="sxs-lookup"><span data-stu-id="b588d-450">An entity key that is defined by one or more properties.</span></span> <span data-ttu-id="b588d-451">(Requerido)</span><span class="sxs-lookup"><span data-stu-id="b588d-451">(Required.)</span></span>
-   <span data-ttu-id="b588d-452">Propiedades para el almacenamiento de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-452">Properties for containing data.</span></span> <span data-ttu-id="b588d-453">(Opcional).</span><span class="sxs-lookup"><span data-stu-id="b588d-453">(Optional.)</span></span>
-   <span data-ttu-id="b588d-454">Propiedades de navegación que permiten desplazarse de un extremo de la asociación al otro.</span><span class="sxs-lookup"><span data-stu-id="b588d-454">Navigation properties that allow for navigation from one end of an association to the other end.</span></span> <span data-ttu-id="b588d-455">(Opcional).</span><span class="sxs-lookup"><span data-stu-id="b588d-455">(Optional.)</span></span>

<span data-ttu-id="b588d-456">En una aplicación, una instancia de un tipo de entidad representa un objeto específico (como un cliente o un pedido concreto).</span><span class="sxs-lookup"><span data-stu-id="b588d-456">In an application, an instance of an entity type represents a specific object (such as a specific customer or order).</span></span> <span data-ttu-id="b588d-457">Cada una de las instancias de un tipo de entidad debe tener una clave de entidad única dentro de un conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="b588d-457">Each instance of an entity type must have a unique entity key within an entity set.</span></span>

<span data-ttu-id="b588d-458">Dos instancias de tipo de entidad se consideran iguales solo si son del mismo tipo y los valores de sus claves de entidad son idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-458">Two entity type instances are considered equal only if they are of the same type and the values of their entity keys are the same.</span></span>

<span data-ttu-id="b588d-459">Un elemento **EntityType** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-459">An **EntityType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-460">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-460">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b588d-461">Key (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-461">Key (zero or one element)</span></span>
-   <span data-ttu-id="b588d-462">Property (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-462">Property (zero or more elements)</span></span>
-   <span data-ttu-id="b588d-463">NavigationProperty (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-463">NavigationProperty (zero or more elements)</span></span>
-   <span data-ttu-id="b588d-464">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-464">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-465">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-465">Applicable Attributes</span></span>

<span data-ttu-id="b588d-466">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-466">The table below describes the attributes that can be applied to the **EntityType** element.</span></span>

| <span data-ttu-id="b588d-467">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-467">Attribute Name</span></span>                                                                                                                                  | <span data-ttu-id="b588d-468">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-468">Is Required</span></span> | <span data-ttu-id="b588d-469">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-469">Value</span></span>                                                                                            |
|:------------------------------------------------------------------------------------------------------------------------------------------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-470">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-470">**Name**</span></span>                                                                                                                                        | <span data-ttu-id="b588d-471">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-471">Yes</span></span>         | <span data-ttu-id="b588d-472">El nombre del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-472">The name of the entity type.</span></span>                                                                     |
| <span data-ttu-id="b588d-473">**BaseType**</span><span class="sxs-lookup"><span data-stu-id="b588d-473">**BaseType**</span></span>                                                                                                                                    | <span data-ttu-id="b588d-474">No</span><span class="sxs-lookup"><span data-stu-id="b588d-474">No</span></span>          | <span data-ttu-id="b588d-475">El nombre de otro tipo de entidad que sea el tipo base del tipo de entidad que se define.</span><span class="sxs-lookup"><span data-stu-id="b588d-475">The name of another entity type that is the base type of the entity type that is being defined.</span></span>  |
| <span data-ttu-id="b588d-476">**Descripción**</span><span class="sxs-lookup"><span data-stu-id="b588d-476">**Abstract**</span></span>                                                                                                                                    | <span data-ttu-id="b588d-477">No</span><span class="sxs-lookup"><span data-stu-id="b588d-477">No</span></span>          | <span data-ttu-id="b588d-478">**True** o **false**, dependiendo de si el tipo de entidad es un tipo abstracto.</span><span class="sxs-lookup"><span data-stu-id="b588d-478">**True** or **False**, depending on whether the entity type is an abstract type.</span></span>                 |
| <span data-ttu-id="b588d-479">**OpenType**</span><span class="sxs-lookup"><span data-stu-id="b588d-479">**OpenType**</span></span>                                                                                                                                    | <span data-ttu-id="b588d-480">No</span><span class="sxs-lookup"><span data-stu-id="b588d-480">No</span></span>          | <span data-ttu-id="b588d-481">**True** o **false** , dependiendo de si el tipo de entidad es un tipo de entidad abierto.</span><span class="sxs-lookup"><span data-stu-id="b588d-481">**True** or **False** depending on whether the entity type is an open entity type.</span></span> <br/> [!NOTE] |
| <span data-ttu-id="b588d-482">> el atributo **OpenType** solo es aplicable a los tipos de entidad que se definen en los modelos conceptuales que se usan con el Data Services ADO.net.</span><span class="sxs-lookup"><span data-stu-id="b588d-482">> The **OpenType** attribute is only applicable to entity types that are defined in conceptual models that are used with ADO.NET Data Services.</span></span> |             |                                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="b588d-483">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **EntityType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-483">Any number of annotation attributes (custom XML attributes) may be applied to the **EntityType** element.</span></span> <span data-ttu-id="b588d-484">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-484">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-485">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-485">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-486">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-486">Example</span></span>

<span data-ttu-id="b588d-487">En el ejemplo siguiente se muestra un elemento **EntityType** con tres elementos **Property** y dos elementos **NavigationProperty** :</span><span class="sxs-lookup"><span data-stu-id="b588d-487">The following example shows an **EntityType** element with three **Property** elements and two **NavigationProperty** elements:</span></span>

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
 

 

## <a name="enumtype-element-csdl"></a><span data-ttu-id="b588d-488">Elemento EnumType (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-488">EnumType Element (CSDL)</span></span>

<span data-ttu-id="b588d-489">El elemento **enumType** representa un tipo enumerado.</span><span class="sxs-lookup"><span data-stu-id="b588d-489">The **EnumType** element represents an enumerated type.</span></span>

<span data-ttu-id="b588d-490">Un elemento **enumType** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-490">An **EnumType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-491">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-491">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b588d-492">Miembro (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-492">Member (zero or more elements)</span></span>
-   <span data-ttu-id="b588d-493">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-493">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-494">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-494">Applicable Attributes</span></span>

<span data-ttu-id="b588d-495">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **enumType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-495">The table below describes the attributes that can be applied to the **EnumType** element.</span></span>

| <span data-ttu-id="b588d-496">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-496">Attribute Name</span></span>     | <span data-ttu-id="b588d-497">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-497">Is Required</span></span> | <span data-ttu-id="b588d-498">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-498">Value</span></span>                                                                                                                                                                                         |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-499">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-499">**Name**</span></span>           | <span data-ttu-id="b588d-500">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-500">Yes</span></span>         | <span data-ttu-id="b588d-501">El nombre del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-501">The name of the entity type.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="b588d-502">**IsFlags**</span><span class="sxs-lookup"><span data-stu-id="b588d-502">**IsFlags**</span></span>        | <span data-ttu-id="b588d-503">No</span><span class="sxs-lookup"><span data-stu-id="b588d-503">No</span></span>          | <span data-ttu-id="b588d-504">**True** o **false**, dependiendo de si el tipo de enumeración se puede usar como un conjunto de marcas.</span><span class="sxs-lookup"><span data-stu-id="b588d-504">**True** or **False**, depending on whether the enum type can be used as a set of flags.</span></span> <span data-ttu-id="b588d-505">El valor predeterminado es **false.**</span><span class="sxs-lookup"><span data-stu-id="b588d-505">The default value is **False.**.</span></span>                                                                     |
| <span data-ttu-id="b588d-506">**UnderlyingType**</span><span class="sxs-lookup"><span data-stu-id="b588d-506">**UnderlyingType**</span></span> | <span data-ttu-id="b588d-507">No</span><span class="sxs-lookup"><span data-stu-id="b588d-507">No</span></span>          | <span data-ttu-id="b588d-508">**EDM. Byte**, **EDM. Int16**, **EDM. Int32**, **EDM. Int64** o **EDM. SByte** que define el intervalo de valores del tipo.</span><span class="sxs-lookup"><span data-stu-id="b588d-508">**Edm.Byte**, **Edm.Int16**, **Edm.Int32**, **Edm.Int64** or **Edm.SByte** defining the range of values of the type.</span></span> <span data-ttu-id="b588d-509">El tipo subyacente predeterminado de los elementos de enumeración es **EDM. Int32.**.</span><span class="sxs-lookup"><span data-stu-id="b588d-509">  The default underlying type of enumeration elements is **Edm.Int32.**.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-510">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **enumType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-510">Any number of annotation attributes (custom XML attributes) may be applied to the **EnumType** element.</span></span> <span data-ttu-id="b588d-511">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-511">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-512">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-512">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-513">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-513">Example</span></span>

<span data-ttu-id="b588d-514">En el ejemplo siguiente se muestra un elemento **enumType** con tres elementos **member** :</span><span class="sxs-lookup"><span data-stu-id="b588d-514">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color" IsFlags=”false” UnderlyingTyp=”Edm.Byte”>
   <Member Name="Red" />
   <Member Name="Green" />
   <Member Name="Blue" />
 </EntityType>
```
 

 

## <a name="function-element-csdl"></a><span data-ttu-id="b588d-515">Function (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-515">Function Element (CSDL)</span></span>

<span data-ttu-id="b588d-516">El elemento **function** en el lenguaje de definición de esquemas conceptuales (CSDL) se utiliza para definir o declarar funciones en el modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-516">The **Function** element in conceptual schema definition language (CSDL) is used to define or declare functions in the conceptual model.</span></span> <span data-ttu-id="b588d-517">Una función se define utilizando un elemento DefiningExpression.</span><span class="sxs-lookup"><span data-stu-id="b588d-517">A function is defined by using a DefiningExpression element.</span></span>  

<span data-ttu-id="b588d-518">Un elemento **function** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-518">A **Function** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-519">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-519">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b588d-520">Parameter (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-520">Parameter (zero or more elements)</span></span>
-   <span data-ttu-id="b588d-521">DefiningExpression (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-521">DefiningExpression (zero or one element)</span></span>
-   <span data-ttu-id="b588d-522">ReturnType (función) (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-522">ReturnType (Function) (zero or one element)</span></span>
-   <span data-ttu-id="b588d-523">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-523">Annotation elements (zero or more elements)</span></span>

<span data-ttu-id="b588d-524">Un tipo de valor devuelto para una función debe especificarse con el elemento **ReturnType** (function) o el atributo **ReturnType** (consulte a continuación), pero no ambos.</span><span class="sxs-lookup"><span data-stu-id="b588d-524">A return type for a function must be specified with either the **ReturnType** (Function) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="b588d-525">Los tipos de valores devueltos posibles son EdmSimpleType, tipo de entidad, tipo complejo, tipo de fila o tipo ref (o una colección de uno de estos tipos).</span><span class="sxs-lookup"><span data-stu-id="b588d-525">The possible return types are any EdmSimpleType, entity type, complex type, row type, or ref type (or a collection of one of these types).</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-526">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-526">Applicable Attributes</span></span>

<span data-ttu-id="b588d-527">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento de **función** .</span><span class="sxs-lookup"><span data-stu-id="b588d-527">The table below describes the attributes that can be applied to the **Function** element.</span></span>

| <span data-ttu-id="b588d-528">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-528">Attribute Name</span></span> | <span data-ttu-id="b588d-529">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-529">Is Required</span></span> | <span data-ttu-id="b588d-530">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-530">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="b588d-531">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-531">**Name**</span></span>       | <span data-ttu-id="b588d-532">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-532">Yes</span></span>         | <span data-ttu-id="b588d-533">Nombre de la función.</span><span class="sxs-lookup"><span data-stu-id="b588d-533">The name of the function.</span></span>          |
| <span data-ttu-id="b588d-534">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="b588d-534">**ReturnType**</span></span> | <span data-ttu-id="b588d-535">No</span><span class="sxs-lookup"><span data-stu-id="b588d-535">No</span></span>          | <span data-ttu-id="b588d-536">El tipo devuelto por la función.</span><span class="sxs-lookup"><span data-stu-id="b588d-536">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-537">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento de **función** .</span><span class="sxs-lookup"><span data-stu-id="b588d-537">Any number of annotation attributes (custom XML attributes) may be applied to the **Function** element.</span></span> <span data-ttu-id="b588d-538">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-538">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-539">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-539">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-540">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-540">Example</span></span>

<span data-ttu-id="b588d-541">En el ejemplo siguiente se usa un elemento **function** para definir una función que devuelve el número de años transcurridos desde que se contrató a un instructor.</span><span class="sxs-lookup"><span data-stu-id="b588d-541">The following example uses a **Function** element to define a function that returns the number of years since an instructor was hired.</span></span>

``` xml
 <Function Name="YearsSince" ReturnType="Edm.Int32">
   <Parameter Name="date" Type="Edm.DateTime" />
   <DefiningExpression>
     Year(CurrentDateTime()) - Year(date)
   </DefiningExpression>
 </Function>
```
 

 

## <a name="functionimport-element-csdl"></a><span data-ttu-id="b588d-542">FunctionImport (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-542">FunctionImport Element (CSDL)</span></span>

<span data-ttu-id="b588d-543">El elemento **FunctionImport** del lenguaje de definición de esquemas conceptuales (CSDL) representa una función que se define en el origen de datos, pero que está disponible para los objetos a través del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-543">The **FunctionImport** element in conceptual schema definition language (CSDL) represents a function that is defined in the data source but available to objects through the conceptual model.</span></span> <span data-ttu-id="b588d-544">Por ejemplo, un elemento Function del modelo de almacenamiento se puede usar para representar un procedimiento almacenado en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-544">For example, a Function element in the storage model can be used to represent a stored procedure in a database.</span></span> <span data-ttu-id="b588d-545">Un elemento **FunctionImport** del modelo conceptual representa la función correspondiente en una aplicación Entity Framework y se asigna a la función de modelo de almacenamiento mediante el elemento FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="b588d-545">A **FunctionImport** element in the conceptual model represents the corresponding function in an Entity Framework application and is mapped to the storage model function by using the FunctionImportMapping element.</span></span> <span data-ttu-id="b588d-546">Cuando se llama a la función en la aplicación, el procedimiento almacenado correspondiente se ejecuta en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-546">When the function is called in the application, the corresponding stored procedure is executed in the database.</span></span>

<span data-ttu-id="b588d-547">El elemento **FunctionImport** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-547">The **FunctionImport** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-548">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-548">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="b588d-549">Parameter (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-549">Parameter (zero or more elements allowed)</span></span>
-   <span data-ttu-id="b588d-550">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-550">Annotation elements (zero or more elements allowed)</span></span>
-   <span data-ttu-id="b588d-551">ReturnType (FunctionImport) (cero o más elementos permitidos)</span><span class="sxs-lookup"><span data-stu-id="b588d-551">ReturnType (FunctionImport) (zero or more elements allowed)</span></span>

<span data-ttu-id="b588d-552">Debe definirse un elemento de **parámetro** para cada parámetro aceptado por la función.</span><span class="sxs-lookup"><span data-stu-id="b588d-552">One **Parameter** element should be defined for each parameter that the function accepts.</span></span>

<span data-ttu-id="b588d-553">Un tipo de valor devuelto para una función debe especificarse con el elemento **ReturnType** (FunctionImport) o el atributo **ReturnType** (consulte a continuación), pero no ambos.</span><span class="sxs-lookup"><span data-stu-id="b588d-553">A return type for a function must be specified with either the **ReturnType** (FunctionImport) element or the **ReturnType** attribute (see below), but not both.</span></span> <span data-ttu-id="b588d-554">El valor de tipo devuelto debe ser una colección de EdmSimpleType, EntityType o ComplexType.</span><span class="sxs-lookup"><span data-stu-id="b588d-554">The return type value must be a  collection of EdmSimpleType, EntityType, or ComplexType.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-555">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-555">Applicable Attributes</span></span>

<span data-ttu-id="b588d-556">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="b588d-556">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="b588d-557">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-557">Attribute Name</span></span>   | <span data-ttu-id="b588d-558">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-558">Is Required</span></span> | <span data-ttu-id="b588d-559">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-559">Value</span></span>                                                                                                                                                                                                 |
|:-----------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-560">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-560">**Name**</span></span>         | <span data-ttu-id="b588d-561">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-561">Yes</span></span>         | <span data-ttu-id="b588d-562">Nombre de la función importada.</span><span class="sxs-lookup"><span data-stu-id="b588d-562">The name of the imported function.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="b588d-563">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="b588d-563">**ReturnType**</span></span>   | <span data-ttu-id="b588d-564">No</span><span class="sxs-lookup"><span data-stu-id="b588d-564">No</span></span>          | <span data-ttu-id="b588d-565">Tipo devuelto por la función.</span><span class="sxs-lookup"><span data-stu-id="b588d-565">The type that the function returns.</span></span> <span data-ttu-id="b588d-566">No utilice este atributo si la función no devuelve un valor.</span><span class="sxs-lookup"><span data-stu-id="b588d-566">Do not use this attribute if the function does not return a value.</span></span> <span data-ttu-id="b588d-567">De lo contrario, el valor debe ser una colección de ComplexType, EntityType o EDMSimpleType.</span><span class="sxs-lookup"><span data-stu-id="b588d-567">Otherwise, the value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>        |
| <span data-ttu-id="b588d-568">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="b588d-568">**EntitySet**</span></span>    | <span data-ttu-id="b588d-569">No</span><span class="sxs-lookup"><span data-stu-id="b588d-569">No</span></span>          | <span data-ttu-id="b588d-570">Si la función devuelve una colección de tipos de entidad, el valor de **EntitySet** debe ser el conjunto de entidades al que pertenece la colección.</span><span class="sxs-lookup"><span data-stu-id="b588d-570">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="b588d-571">De lo contrario, el atributo **EntitySet** no debe usarse.</span><span class="sxs-lookup"><span data-stu-id="b588d-571">Otherwise, the **EntitySet** attribute must not be used.</span></span> |
| <span data-ttu-id="b588d-572">**IsComposable**</span><span class="sxs-lookup"><span data-stu-id="b588d-572">**IsComposable**</span></span> | <span data-ttu-id="b588d-573">No</span><span class="sxs-lookup"><span data-stu-id="b588d-573">No</span></span>          | <span data-ttu-id="b588d-574">Si el valor se establece en true, la función es ajustable (función con valores de tabla) y se puede usar en una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="b588d-574">If the value is set to true, the function is composable (Table-valued Function) and can be used in a LINQ query.</span></span><span data-ttu-id="b588d-575">El valor predeterminado es **false**.</span><span class="sxs-lookup"><span data-stu-id="b588d-575">  The default is **false**.</span></span>                                                           |

 

> [!NOTE]
> <span data-ttu-id="b588d-576">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="b588d-576">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="b588d-577">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-577">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-578">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-578">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-579">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-579">Example</span></span>

<span data-ttu-id="b588d-580">En el ejemplo siguiente se muestra un elemento **FunctionImport** que acepta un parámetro y devuelve una colección de tipos de entidad:</span><span class="sxs-lookup"><span data-stu-id="b588d-580">The following example shows a **FunctionImport** element that accepts one parameter and returns a collection of entity types:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

 

## <a name="key-element-csdl"></a><span data-ttu-id="b588d-581">Key (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-581">Key Element (CSDL)</span></span>

<span data-ttu-id="b588d-582">El elemento **key** es un elemento secundario del elemento EntityType y define una *clave de entidad* (una propiedad o un conjunto de propiedades de un tipo de entidad que determinan la identidad).</span><span class="sxs-lookup"><span data-stu-id="b588d-582">The **Key** element is a child element of the EntityType element and defines an *entity key* (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="b588d-583">Las propiedades que constituyen una entidad se eligen en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="b588d-583">The properties that make up an entity key are chosen at design time.</span></span> <span data-ttu-id="b588d-584">Los valores de las propiedades de clave de entidad deben identificar de forma inequívoca en tiempo de ejecución una instancia de tipo de entidad dentro de un conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="b588d-584">The values of entity key properties must uniquely identify an entity type instance within an entity set at run time.</span></span> <span data-ttu-id="b588d-585">Las propiedades que constituyen una clave de entidad se deben elegir de tal forma que garanticen la unicidad de las instancias de un conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="b588d-585">The properties that make up an entity key should be chosen to guarantee uniqueness of instances in an entity set.</span></span> <span data-ttu-id="b588d-586">El elemento **key** define una clave de entidad haciendo referencia a una o más de las propiedades de un tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-586">The **Key** element defines an entity key by referencing one or more of the properties of an entity type.</span></span>

<span data-ttu-id="b588d-587">El elemento **key** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="b588d-587">The **Key** element can have the following child elements:</span></span>

-   <span data-ttu-id="b588d-588">PropertyRef (uno o varios elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-588">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="b588d-589">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-589">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-590">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-590">Applicable Attributes</span></span>

<span data-ttu-id="b588d-591">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **key** .</span><span class="sxs-lookup"><span data-stu-id="b588d-591">Any number of annotation attributes (custom XML attributes) may be applied to the **Key** element.</span></span> <span data-ttu-id="b588d-592">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-592">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-593">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-593">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b588d-594">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-594">Example</span></span>

<span data-ttu-id="b588d-595">En el ejemplo siguiente se define un tipo de entidad denominado **book**.</span><span class="sxs-lookup"><span data-stu-id="b588d-595">The example below defines an entity type named **Book**.</span></span> <span data-ttu-id="b588d-596">La clave de entidad se define haciendo referencia a la propiedad **ISBN** del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-596">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="b588d-597">La propiedad **ISBN** es una buena elección para la clave de entidad porque un número de libro estándar internacional (ISBN) identifica de forma única un libro.</span><span class="sxs-lookup"><span data-stu-id="b588d-597">The **ISBN** property is a good choice for the entity key because an International Standard Book Number (ISBN) uniquely identifies a book.</span></span>

<span data-ttu-id="b588d-598">En el ejemplo siguiente se muestra un tipo de entidad (**autor**) que tiene una clave de entidad que consta de dos propiedades, **nombre** y **Dirección**.</span><span class="sxs-lookup"><span data-stu-id="b588d-598">The following example shows an entity type (**Author**) that has an entity key that consists of two properties, **Name** and **Address**.</span></span>

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
 

<span data-ttu-id="b588d-599">Usar el **nombre** y la **Dirección** de la clave de entidad es una opción razonable, ya que es poco probable que dos autores del mismo nombre se encuentren en la misma dirección.</span><span class="sxs-lookup"><span data-stu-id="b588d-599">Using **Name** and **Address** for the entity key is a reasonable choice, because two authors of the same name are unlikely to live at the same address.</span></span> <span data-ttu-id="b588d-600">Sin embargo, esta opción no garantiza por completo la existencia de claves de entidad únicas en un conjunto de entidades.</span><span class="sxs-lookup"><span data-stu-id="b588d-600">However, this choice for an entity key does not absolutely guarantee unique entity keys in an entity set.</span></span> <span data-ttu-id="b588d-601">En este caso, se recomienda agregar una propiedad, como **AuthorId**, que pueda usarse para identificar de forma única un autor.</span><span class="sxs-lookup"><span data-stu-id="b588d-601">Adding a property, such as **AuthorId**, that could be used to uniquely identify an author would be recommended in this case.</span></span>

 

## <a name="member-element-csdl"></a><span data-ttu-id="b588d-602">Elemento Member (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-602">Member Element (CSDL)</span></span>

<span data-ttu-id="b588d-603">El elemento **member** es un elemento secundario del elemento enumType y define un miembro del tipo enumerado.</span><span class="sxs-lookup"><span data-stu-id="b588d-603">The **Member** element is a child element of the EnumType element and defines a member of the enumerated type.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-604">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-604">Applicable Attributes</span></span>

<span data-ttu-id="b588d-605">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="b588d-605">The table below describes the attributes that can be applied to the **FunctionImport** element.</span></span>

| <span data-ttu-id="b588d-606">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-606">Attribute Name</span></span> | <span data-ttu-id="b588d-607">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-607">Is Required</span></span> | <span data-ttu-id="b588d-608">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-608">Value</span></span>                                                                                                                                                                                    |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-609">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-609">**Name**</span></span>       | <span data-ttu-id="b588d-610">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-610">Yes</span></span>         | <span data-ttu-id="b588d-611">Nombre del miembro.</span><span class="sxs-lookup"><span data-stu-id="b588d-611">The name of the member.</span></span>                                                                                                                                                                  |
| <span data-ttu-id="b588d-612">**Valor**</span><span class="sxs-lookup"><span data-stu-id="b588d-612">**Value**</span></span>      | <span data-ttu-id="b588d-613">No</span><span class="sxs-lookup"><span data-stu-id="b588d-613">No</span></span>          | <span data-ttu-id="b588d-614">Valor del miembro.</span><span class="sxs-lookup"><span data-stu-id="b588d-614">The value of the member.</span></span> <span data-ttu-id="b588d-615">De forma predeterminada, el primer miembro tiene el valor 0 y el valor de cada enumerador sucesivo se incrementa en 1.</span><span class="sxs-lookup"><span data-stu-id="b588d-615">By default, the first member has the value 0, and the value of each successive enumerator is incremented by 1.</span></span> <span data-ttu-id="b588d-616">Pueden existir varios miembros con los mismos valores.</span><span class="sxs-lookup"><span data-stu-id="b588d-616">Multiple members with the same values may exist.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-617">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **FunctionImport** .</span><span class="sxs-lookup"><span data-stu-id="b588d-617">Any number of annotation attributes (custom XML attributes) may be applied to the **FunctionImport** element.</span></span> <span data-ttu-id="b588d-618">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-618">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-619">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-619">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-620">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-620">Example</span></span>

<span data-ttu-id="b588d-621">En el ejemplo siguiente se muestra un elemento **enumType** con tres elementos **member** :</span><span class="sxs-lookup"><span data-stu-id="b588d-621">The following example shows an **EnumType** element with three **Member** elements:</span></span>

``` xml
 <EnumType Name="Color">
   <Member Name="Red" Value=”1”/>
   <Member Name="Green" Value=”3” />
   <Member Name="Blue" Value=”5”/>
 </EntityType>
```
 

 

## <a name="navigationproperty-element-csdl"></a><span data-ttu-id="b588d-622">NavigationProperty (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-622">NavigationProperty Element (CSDL)</span></span>

<span data-ttu-id="b588d-623">Un elemento **NavigationProperty** define una propiedad de navegación, que proporciona una referencia al otro extremo de una asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-623">A **NavigationProperty** element defines a navigation property, which provides a reference to the other end of an association.</span></span> <span data-ttu-id="b588d-624">A diferencia de las propiedades definidas con el elemento Property, las propiedades de navegación no definen la forma ni las características de los datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-624">Unlike properties defined with the Property element, navigation properties do not define the shape and characteristics of data.</span></span> <span data-ttu-id="b588d-625">Proporcionan una manera de desplazarse por una asociación entre dos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-625">They provide a way to navigate an association between two entity types.</span></span>

<span data-ttu-id="b588d-626">Las propiedades de navegación son opcionales en los dos tipos de entidad de los extremos de una asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-626">Note that navigation properties are optional on both entity types at the ends of an association.</span></span> <span data-ttu-id="b588d-627">Si define una propiedad de navegación en un tipo de entidad del extremo de una asociación, no tiene que definir una propiedad de navegación en el tipo de entidad del otro extremo de la asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-627">If you define a navigation property on one entity type at the end of an association, you do not have to define a navigation property on the entity type at the other end of the association.</span></span>

<span data-ttu-id="b588d-628">El tipo de datos devuelto por una propiedad de navegación viene determinado por la multiplicidad de su extremo remoto de la asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-628">The data type returned by a navigation property is determined by the multiplicity of its remote association end.</span></span> <span data-ttu-id="b588d-629">Por ejemplo, supongamos que existe una propiedad de navegación, **OrdersNavProp**, en un tipo de entidad **Customer** y navega por una asociación uno a varios entre **Customer** y **Order**.</span><span class="sxs-lookup"><span data-stu-id="b588d-629">For example, suppose a navigation property, **OrdersNavProp**, exists on a **Customer** entity type and navigates a one-to-many association between **Customer** and **Order**.</span></span> <span data-ttu-id="b588d-630">Dado que el extremo remoto de la Asociación para la propiedad de navegación tiene una multiplicidad de varios ( \* ), su tipo de datos es una colección (de **orden**).</span><span class="sxs-lookup"><span data-stu-id="b588d-630">Because the remote association end for the navigation property has multiplicity many (\*), its data type is a collection (of **Order**).</span></span> <span data-ttu-id="b588d-631">Del mismo modo, si una propiedad de navegación, **CustomerNavProp**, existe en el tipo de entidad **Order** , su tipo de datos sería **Customer** , ya que la multiplicidad del extremo remoto es uno (1).</span><span class="sxs-lookup"><span data-stu-id="b588d-631">Similarly, if a navigation property, **CustomerNavProp**, exists on the **Order** entity type, its data type would be **Customer** since the multiplicity of the remote end is one (1).</span></span>

<span data-ttu-id="b588d-632">Un elemento **NavigationProperty** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-632">A **NavigationProperty** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-633">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-633">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b588d-634">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-634">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-635">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-635">Applicable Attributes</span></span>

<span data-ttu-id="b588d-636">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **NavigationProperty** .</span><span class="sxs-lookup"><span data-stu-id="b588d-636">The table below describes the attributes that can be applied to the **NavigationProperty** element.</span></span>

| <span data-ttu-id="b588d-637">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-637">Attribute Name</span></span>   | <span data-ttu-id="b588d-638">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-638">Is Required</span></span> | <span data-ttu-id="b588d-639">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-639">Value</span></span>                                                                                                                                                                                                                                            |
|:-----------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-640">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-640">**Name**</span></span>         | <span data-ttu-id="b588d-641">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-641">Yes</span></span>         | <span data-ttu-id="b588d-642">Nombre de la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="b588d-642">The name of the navigation property.</span></span>                                                                                                                                                                                                             |
| <span data-ttu-id="b588d-643">**Relación**</span><span class="sxs-lookup"><span data-stu-id="b588d-643">**Relationship**</span></span> | <span data-ttu-id="b588d-644">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-644">Yes</span></span>         | <span data-ttu-id="b588d-645">Nombre de una asociación que se encuentra dentro del ámbito del modelo.</span><span class="sxs-lookup"><span data-stu-id="b588d-645">The name of an association that is within the scope of the model.</span></span>                                                                                                                                                                                |
| <span data-ttu-id="b588d-646">**ToRole**</span><span class="sxs-lookup"><span data-stu-id="b588d-646">**ToRole**</span></span>       | <span data-ttu-id="b588d-647">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-647">Yes</span></span>         | <span data-ttu-id="b588d-648">Extremo de la asociación en el que finaliza la navegación.</span><span class="sxs-lookup"><span data-stu-id="b588d-648">The end of the association at which navigation ends.</span></span> <span data-ttu-id="b588d-649">El valor del atributo **ToRole** debe ser el mismo que el valor de uno de los atributos de **rol** definidos en uno de los extremos de la Asociación (definidos en el elemento AssociationEnd).</span><span class="sxs-lookup"><span data-stu-id="b588d-649">The value of the **ToRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span>       |
| <span data-ttu-id="b588d-650">**FromRole**</span><span class="sxs-lookup"><span data-stu-id="b588d-650">**FromRole**</span></span>     | <span data-ttu-id="b588d-651">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-651">Yes</span></span>         | <span data-ttu-id="b588d-652">Extremo de la asociación desde el que comienza la navegación.</span><span class="sxs-lookup"><span data-stu-id="b588d-652">The end of the association from which navigation begins.</span></span> <span data-ttu-id="b588d-653">El valor del atributo **FromRole** debe ser el mismo que el valor de uno de los atributos de **rol** definidos en uno de los extremos de la Asociación (definidos en el elemento AssociationEnd).</span><span class="sxs-lookup"><span data-stu-id="b588d-653">The value of the **FromRole** attribute must be the same as the value of one of the **Role** attributes defined on one of the association ends (defined in the AssociationEnd element).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-654">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **NavigationProperty** .</span><span class="sxs-lookup"><span data-stu-id="b588d-654">Any number of annotation attributes (custom XML attributes) may be applied to the **NavigationProperty** element.</span></span> <span data-ttu-id="b588d-655">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-655">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-656">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-656">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-657">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-657">Example</span></span>

<span data-ttu-id="b588d-658">En el ejemplo siguiente se define un tipo de entidad (**book**) con dos propiedades de navegación (**PublishedBy** y **WrittenBy**):</span><span class="sxs-lookup"><span data-stu-id="b588d-658">The following example defines an entity type (**Book**) with two navigation properties (**PublishedBy** and **WrittenBy**):</span></span>

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
 

 

## <a name="ondelete-element-csdl"></a><span data-ttu-id="b588d-659">OnDelete (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-659">OnDelete Element (CSDL)</span></span>

<span data-ttu-id="b588d-660">El elemento **aldelete** en el lenguaje de definición de esquemas conceptuales (CSDL) define el comportamiento que está conectado a una asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-660">The **OnDelete** element in conceptual schema definition language (CSDL) defines behavior that is connected with an association.</span></span> <span data-ttu-id="b588d-661">Si el atributo **Action** se establece en **Cascade** en un extremo de una asociación, los tipos de entidad relacionados en el otro extremo de la asociación se eliminan cuando se elimina el tipo de entidad del primer extremo.</span><span class="sxs-lookup"><span data-stu-id="b588d-661">If the **Action** attribute is set to **Cascade** on one end of an association, related entity types on the other end of the association are deleted when the entity type on the first end is deleted.</span></span> <span data-ttu-id="b588d-662">Si la asociación entre dos tipos de entidad es una relación entre clave principal y clave principal, se elimina un objeto dependiente cargado cuando se elimina el objeto de entidad de seguridad del otro extremo de la asociación, independientemente de la especificación de la **eliminación** .</span><span class="sxs-lookup"><span data-stu-id="b588d-662">If the association between two entity types is a primary key-to-primary key relationship, then a loaded dependent object is deleted when the principal object on the other end of the association is deleted regardless of the **OnDelete** specification.</span></span>  

> [!NOTE]
> <span data-ttu-id="b588d-663">El elemento **aldelete** solo afecta al comportamiento de tiempo de ejecución de una aplicación; no afecta al comportamiento en el origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-663">The **OnDelete** element only affects the runtime behavior of an application; it does not affect behavior in the data source.</span></span> <span data-ttu-id="b588d-664">El comportamiento definido en el origen de datos debe ser igual que el definido en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b588d-664">The behavior defined in the data source should be the same as the behavior defined in the application.</span></span>

 

<span data-ttu-id="b588d-665">Un elemento **aldelete** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-665">An **OnDelete** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-666">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-666">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b588d-667">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-667">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-668">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-668">Applicable Attributes</span></span>

<span data-ttu-id="b588d-669">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **aleliminar** .</span><span class="sxs-lookup"><span data-stu-id="b588d-669">The table below describes the attributes that can be applied to the **OnDelete** element.</span></span>

| <span data-ttu-id="b588d-670">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-670">Attribute Name</span></span> | <span data-ttu-id="b588d-671">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-671">Is Required</span></span> | <span data-ttu-id="b588d-672">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-672">Value</span></span>                                                                                                                                                                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-673">**Acción**</span><span class="sxs-lookup"><span data-stu-id="b588d-673">**Action**</span></span>     | <span data-ttu-id="b588d-674">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-674">Yes</span></span>         | <span data-ttu-id="b588d-675">**Cascade** o **None**.</span><span class="sxs-lookup"><span data-stu-id="b588d-675">**Cascade** or **None**.</span></span> <span data-ttu-id="b588d-676">Si **en cascada**, los tipos de entidad dependientes se eliminarán cuando se elimine el tipo de entidad de entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="b588d-676">If **Cascade**, dependent entity types will be deleted when the principal entity type is deleted.</span></span> <span data-ttu-id="b588d-677">Si no hay **ninguno**, los tipos de entidad dependientes no se eliminarán cuando se elimine el tipo de entidad de entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="b588d-677">If **None**, dependent entity types will not be deleted when the principal entity type is deleted.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-678">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="b588d-678">Any number of annotation attributes (custom XML attributes) may be applied to the **Association** element.</span></span> <span data-ttu-id="b588d-679">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-679">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-680">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-680">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-681">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-681">Example</span></span>

<span data-ttu-id="b588d-682">En el ejemplo siguiente se muestra un elemento **Association** que define la Asociación **CustomerOrders** .</span><span class="sxs-lookup"><span data-stu-id="b588d-682">The following example shows an **Association** element that defines the **CustomerOrders** association.</span></span> <span data-ttu-id="b588d-683">El elemento **aleliminar** indica que todos los **pedidos** relacionados con un **cliente** determinado y que se han cargado en el ObjectContext se eliminarán cuando se elimine el **cliente** .</span><span class="sxs-lookup"><span data-stu-id="b588d-683">The **OnDelete** element indicates that all **Orders** that are related to a particular **Customer** and have been loaded into the ObjectContext will be deleted when the **Customer** is deleted.</span></span>

``` xml
 <Association Name="CustomerOrders">
   <End Type="ExampleModel.Customer" Role="Customer" Multiplicity="1">
         <OnDelete Action="Cascade" />
   </End>
   <End Type="ExampleModel.Order" Role="Order" Multiplicity="*" />
 </Association>
```
 

 

## <a name="parameter-element-csdl"></a><span data-ttu-id="b588d-684">Parameter (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-684">Parameter Element (CSDL)</span></span>

<span data-ttu-id="b588d-685">El elemento **Parameter** en el lenguaje de definición de esquemas conceptuales (CSDL) puede ser un elemento secundario del elemento FunctionImport o del elemento function.</span><span class="sxs-lookup"><span data-stu-id="b588d-685">The **Parameter** element in conceptual schema definition language (CSDL) can be a child of the FunctionImport element or the Function element.</span></span>

### <a name="functionimport-element-application"></a><span data-ttu-id="b588d-686">Aplicación para el elemento FunctionImport</span><span class="sxs-lookup"><span data-stu-id="b588d-686">FunctionImport Element Application</span></span>

<span data-ttu-id="b588d-687">Un elemento **Parameter** (como elemento secundario del elemento **FunctionImport** ) se usa para definir los parámetros de entrada y salida para las importaciones de funciones que se declaran en CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-687">A **Parameter** element (as a child of the **FunctionImport** element) is used to define input and output parameters for function imports that are declared in CSDL.</span></span>

<span data-ttu-id="b588d-688">El elemento **Parameter** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-688">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-689">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-689">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="b588d-690">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-690">Annotation elements (zero or more elements allowed)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="b588d-691">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-691">Applicable Attributes</span></span>

<span data-ttu-id="b588d-692">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="b588d-692">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="b588d-693">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-693">Attribute Name</span></span> | <span data-ttu-id="b588d-694">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-694">Is Required</span></span> | <span data-ttu-id="b588d-695">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-695">Value</span></span>                                                                                                                                                                                                                           |
|:---------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-696">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-696">**Name**</span></span>       | <span data-ttu-id="b588d-697">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-697">Yes</span></span>         | <span data-ttu-id="b588d-698">El nombre del parámetro.</span><span class="sxs-lookup"><span data-stu-id="b588d-698">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="b588d-699">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="b588d-699">**Type**</span></span>       | <span data-ttu-id="b588d-700">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-700">Yes</span></span>         | <span data-ttu-id="b588d-701">El tipo de parámetro.</span><span class="sxs-lookup"><span data-stu-id="b588d-701">The parameter type.</span></span> <span data-ttu-id="b588d-702">El valor tiene que ser un tipo **EDMSimpleType** o un tipo complejo que se encuentre dentro del ámbito del modelo.</span><span class="sxs-lookup"><span data-stu-id="b588d-702">The value must be an **EDMSimpleType** or a complex type that is within the scope of the model.</span></span>                                                                                                             |
| <span data-ttu-id="b588d-703">**Modo**</span><span class="sxs-lookup"><span data-stu-id="b588d-703">**Mode**</span></span>       | <span data-ttu-id="b588d-704">No</span><span class="sxs-lookup"><span data-stu-id="b588d-704">No</span></span>          | <span data-ttu-id="b588d-705">**In**, **out**o **INOUT** dependiendo de si el parámetro es un parámetro de entrada, de salida o de entrada/salida.</span><span class="sxs-lookup"><span data-stu-id="b588d-705">**In**, **Out**, or **InOut** depending on whether the parameter is an input, output, or input/output parameter.</span></span>                                                                                                                |
| <span data-ttu-id="b588d-706">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-706">**MaxLength**</span></span>  | <span data-ttu-id="b588d-707">No</span><span class="sxs-lookup"><span data-stu-id="b588d-707">No</span></span>          | <span data-ttu-id="b588d-708">La longitud máxima permitida del parámetro.</span><span class="sxs-lookup"><span data-stu-id="b588d-708">The maximum allowed length of the parameter.</span></span>                                                                                                                                                                                    |
| <span data-ttu-id="b588d-709">**Precisión**</span><span class="sxs-lookup"><span data-stu-id="b588d-709">**Precision**</span></span>  | <span data-ttu-id="b588d-710">No</span><span class="sxs-lookup"><span data-stu-id="b588d-710">No</span></span>          | <span data-ttu-id="b588d-711">La precisión del parámetro.</span><span class="sxs-lookup"><span data-stu-id="b588d-711">The precision of the parameter.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="b588d-712">**Escala**</span><span class="sxs-lookup"><span data-stu-id="b588d-712">**Scale**</span></span>      | <span data-ttu-id="b588d-713">No</span><span class="sxs-lookup"><span data-stu-id="b588d-713">No</span></span>          | <span data-ttu-id="b588d-714">La escala del parámetro.</span><span class="sxs-lookup"><span data-stu-id="b588d-714">The scale of the parameter.</span></span>                                                                                                                                                                                                     |
| <span data-ttu-id="b588d-715">**SRID**</span><span class="sxs-lookup"><span data-stu-id="b588d-715">**SRID**</span></span>       | <span data-ttu-id="b588d-716">No</span><span class="sxs-lookup"><span data-stu-id="b588d-716">No</span></span>          | <span data-ttu-id="b588d-717">Identificador de referencia del sistema espacial.</span><span class="sxs-lookup"><span data-stu-id="b588d-717">Spatial System Reference Identifier.</span></span> <span data-ttu-id="b588d-718">Válido solo para los parámetros de los tipos espaciales.</span><span class="sxs-lookup"><span data-stu-id="b588d-718">Valid only for parameters of spatial types.</span></span> <span data-ttu-id="b588d-719">Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="b588d-719">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-720">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="b588d-720">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="b588d-721">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-721">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-722">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-722">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="b588d-723">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-723">Example</span></span>

<span data-ttu-id="b588d-724">En el ejemplo siguiente se muestra un elemento **FunctionImport** con un elemento secundario **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="b588d-724">The following example shows a **FunctionImport** element with one **Parameter** child element.</span></span> <span data-ttu-id="b588d-725">La función acepta un parámetro de entrada y devuelve una colección de tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-725">The function accepts one input parameter and returns a collection of entity types.</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades"
                 EntitySet="StudentGrade"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
        <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```
 

### <a name="function-element-application"></a><span data-ttu-id="b588d-726">Aplicación para el elemento Function</span><span class="sxs-lookup"><span data-stu-id="b588d-726">Function Element Application</span></span>

<span data-ttu-id="b588d-727">Un elemento **Parameter** (como elemento secundario del elemento **function** ) define los parámetros de las funciones que se definen o declaran en un modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-727">A **Parameter** element (as a child of the **Function** element) defines parameters for functions that are defined or declared in a conceptual model.</span></span>

<span data-ttu-id="b588d-728">El elemento **Parameter** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-728">The **Parameter** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-729">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-729">Documentation (zero or one elements)</span></span>
-   <span data-ttu-id="b588d-730">CollectionType (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-730">CollectionType (zero or one elements)</span></span>
-   <span data-ttu-id="b588d-731">ReferenceType (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-731">ReferenceType (zero or one elements)</span></span>
-   <span data-ttu-id="b588d-732">RowType (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-732">RowType (zero or one elements)</span></span>

> [!NOTE]
> <span data-ttu-id="b588d-733">Solo uno de los elementos **CollectionType**, **referenceType**o **RowType** puede ser un elemento secundario de un elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="b588d-733">Only one of the **CollectionType**, **ReferenceType**, or **RowType** elements can be a child element of a **Property** element.</span></span>

 

-   <span data-ttu-id="b588d-734">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-734">Annotation elements (zero or more elements allowed)</span></span>

> [!NOTE]
> <span data-ttu-id="b588d-735">Los elementos de anotación deben aparecer después de todos los demás elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="b588d-735">Annotation elements must appear after all other child elements.</span></span> <span data-ttu-id="b588d-736">Los elementos Annotation solo se permiten en CSDL V2 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="b588d-736">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="b588d-737">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-737">Applicable Attributes</span></span>

<span data-ttu-id="b588d-738">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="b588d-738">The following table describes the attributes that can be applied to the **Parameter** element.</span></span>

| <span data-ttu-id="b588d-739">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-739">Attribute Name</span></span>   | <span data-ttu-id="b588d-740">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-740">Is Required</span></span> | <span data-ttu-id="b588d-741">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-741">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-742">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-742">**Name**</span></span>         | <span data-ttu-id="b588d-743">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-743">Yes</span></span>         | <span data-ttu-id="b588d-744">El nombre del parámetro.</span><span class="sxs-lookup"><span data-stu-id="b588d-744">The name of the parameter.</span></span>                                                                                                                                                                                                      |
| <span data-ttu-id="b588d-745">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="b588d-745">**Type**</span></span>         | <span data-ttu-id="b588d-746">No</span><span class="sxs-lookup"><span data-stu-id="b588d-746">No</span></span>          | <span data-ttu-id="b588d-747">El tipo de parámetro.</span><span class="sxs-lookup"><span data-stu-id="b588d-747">The parameter type.</span></span> <span data-ttu-id="b588d-748">Un parámetro puede ser de cualquiera de los siguientes tipos (o colecciones de estos tipos):</span><span class="sxs-lookup"><span data-stu-id="b588d-748">A parameter can be any of the following types (or collections of these types):</span></span> <br/> <span data-ttu-id="b588d-749">**EdmSimpleType**</span><span class="sxs-lookup"><span data-stu-id="b588d-749">**EdmSimpleType**</span></span> <br/> <span data-ttu-id="b588d-750">tipo de entidad</span><span class="sxs-lookup"><span data-stu-id="b588d-750">entity type</span></span> <br/> <span data-ttu-id="b588d-751">tipo complejo</span><span class="sxs-lookup"><span data-stu-id="b588d-751">complex type</span></span> <br/> <span data-ttu-id="b588d-752">tipo de fila</span><span class="sxs-lookup"><span data-stu-id="b588d-752">row type</span></span> <br/> <span data-ttu-id="b588d-753">tipo de referencia</span><span class="sxs-lookup"><span data-stu-id="b588d-753">reference type</span></span>                             |
| <span data-ttu-id="b588d-754">**Admisión de valores NULL**</span><span class="sxs-lookup"><span data-stu-id="b588d-754">**Nullable**</span></span>     | <span data-ttu-id="b588d-755">No</span><span class="sxs-lookup"><span data-stu-id="b588d-755">No</span></span>          | <span data-ttu-id="b588d-756">**True** (valor predeterminado) o **false** , dependiendo de si la propiedad puede tener un valor **null** .</span><span class="sxs-lookup"><span data-stu-id="b588d-756">**True** (the default value) or **False** depending on whether the property can have a **null** value.</span></span>                                                                                                                          |
| <span data-ttu-id="b588d-757">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="b588d-757">**DefaultValue**</span></span> | <span data-ttu-id="b588d-758">No</span><span class="sxs-lookup"><span data-stu-id="b588d-758">No</span></span>          | <span data-ttu-id="b588d-759">Valor predeterminado de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-759">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="b588d-760">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-760">**MaxLength**</span></span>    | <span data-ttu-id="b588d-761">No</span><span class="sxs-lookup"><span data-stu-id="b588d-761">No</span></span>          | <span data-ttu-id="b588d-762">La longitud máxima del valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-762">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="b588d-763">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-763">**FixedLength**</span></span>  | <span data-ttu-id="b588d-764">No</span><span class="sxs-lookup"><span data-stu-id="b588d-764">No</span></span>          | <span data-ttu-id="b588d-765">**True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena de longitud fija.</span><span class="sxs-lookup"><span data-stu-id="b588d-765">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="b588d-766">**Precisión**</span><span class="sxs-lookup"><span data-stu-id="b588d-766">**Precision**</span></span>    | <span data-ttu-id="b588d-767">No</span><span class="sxs-lookup"><span data-stu-id="b588d-767">No</span></span>          | <span data-ttu-id="b588d-768">Precisión del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-768">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="b588d-769">**Escala**</span><span class="sxs-lookup"><span data-stu-id="b588d-769">**Scale**</span></span>        | <span data-ttu-id="b588d-770">No</span><span class="sxs-lookup"><span data-stu-id="b588d-770">No</span></span>          | <span data-ttu-id="b588d-771">Escala del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-771">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="b588d-772">**SRID**</span><span class="sxs-lookup"><span data-stu-id="b588d-772">**SRID**</span></span>         | <span data-ttu-id="b588d-773">No</span><span class="sxs-lookup"><span data-stu-id="b588d-773">No</span></span>          | <span data-ttu-id="b588d-774">Identificador de referencia del sistema espacial.</span><span class="sxs-lookup"><span data-stu-id="b588d-774">Spatial System Reference Identifier.</span></span> <span data-ttu-id="b588d-775">Solo es válido para las propiedades de los tipos espaciales.</span><span class="sxs-lookup"><span data-stu-id="b588d-775">Valid only for properties of spatial types.</span></span> <span data-ttu-id="b588d-776">Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="b588d-776">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="b588d-777">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="b588d-777">**Unicode**</span></span>      | <span data-ttu-id="b588d-778">No</span><span class="sxs-lookup"><span data-stu-id="b588d-778">No</span></span>          | <span data-ttu-id="b588d-779">**True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena Unicode.</span><span class="sxs-lookup"><span data-stu-id="b588d-779">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="b588d-780">**Intercalación**</span><span class="sxs-lookup"><span data-stu-id="b588d-780">**Collation**</span></span>    | <span data-ttu-id="b588d-781">No</span><span class="sxs-lookup"><span data-stu-id="b588d-781">No</span></span>          | <span data-ttu-id="b588d-782">Cadena que especifica la secuencia de intercalación que se usará en el origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-782">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="b588d-783">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Parameter** .</span><span class="sxs-lookup"><span data-stu-id="b588d-783">Any number of annotation attributes (custom XML attributes) may be applied to the **Parameter** element.</span></span> <span data-ttu-id="b588d-784">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-784">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-785">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-785">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="b588d-786">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-786">Example</span></span>

<span data-ttu-id="b588d-787">En el ejemplo siguiente se muestra un elemento de **función** que usa un elemento secundario **Parameter** para definir un parámetro de función.</span><span class="sxs-lookup"><span data-stu-id="b588d-787">The following example shows a **Function** element that uses one **Parameter** child element to define a function parameter.</span></span>

``` xml
 <Function Name="GetYearsEmployed" ReturnType="Edm.Int32">
 <Parameter Name="Instructor" Type="SchoolModel.Person" />
   <DefiningExpression>
   Year(CurrentDateTime()) - Year(cast(Instructor.HireDate as DateTime))
   </DefiningExpression>
 </Function>
```

 

## <a name="principal-element-csdl"></a><span data-ttu-id="b588d-788">Principal (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-788">Principal Element (CSDL)</span></span>

<span data-ttu-id="b588d-789">El elemento **principal** del lenguaje de definición de esquemas conceptuales (CSDL) es un elemento secundario del elemento ReferentialConstraint que define el extremo principal de una restricción referencial.</span><span class="sxs-lookup"><span data-stu-id="b588d-789">The **Principal** element in conceptual schema definition language (CSDL) is a child element to the ReferentialConstraint element that defines the principal end of a referential constraint.</span></span> <span data-ttu-id="b588d-790">Un elemento **ReferentialConstraint** define una funcionalidad similar a una restricción de integridad referencial en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="b588d-790">A **ReferentialConstraint** element defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="b588d-791">Del mismo modo que una columna (o columnas) de una tabla de base de datos puede hacer referencia a la clave principal de otra tabla, una propiedad (o propiedades) de un tipo de entidad puede hacer referencia a la clave de entidad de otro tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-791">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="b588d-792">El tipo de entidad al que se hace referencia se denomina *extremo principal* de la restricción.</span><span class="sxs-lookup"><span data-stu-id="b588d-792">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="b588d-793">El tipo de entidad que hace referencia al extremo principal se denomina *extremo dependiente* de la restricción.</span><span class="sxs-lookup"><span data-stu-id="b588d-793">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span> <span data-ttu-id="b588d-794">Los elementos **PropertyRef** se utilizan para especificar las claves a las que hace referencia el extremo dependiente.</span><span class="sxs-lookup"><span data-stu-id="b588d-794">**PropertyRef** elements are used to specify which keys are referenced by the dependent end.</span></span>

<span data-ttu-id="b588d-795">El elemento **principal** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-795">The **Principal** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-796">PropertyRef (uno o varios elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-796">PropertyRef (one or more elements)</span></span>
-   <span data-ttu-id="b588d-797">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-797">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-798">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-798">Applicable Attributes</span></span>

<span data-ttu-id="b588d-799">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **principal** .</span><span class="sxs-lookup"><span data-stu-id="b588d-799">The table below describes the attributes that can be applied to the **Principal** element.</span></span>

| <span data-ttu-id="b588d-800">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-800">Attribute Name</span></span> | <span data-ttu-id="b588d-801">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-801">Is Required</span></span> | <span data-ttu-id="b588d-802">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-802">Value</span></span>                                                                |
|:---------------|:------------|:---------------------------------------------------------------------|
| <span data-ttu-id="b588d-803">**Rol**</span><span class="sxs-lookup"><span data-stu-id="b588d-803">**Role**</span></span>       | <span data-ttu-id="b588d-804">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-804">Yes</span></span>         | <span data-ttu-id="b588d-805">Nombre del tipo de entidad del extremo principal de la asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-805">The name of the entity type on the principal end of the association.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-806">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **principal** .</span><span class="sxs-lookup"><span data-stu-id="b588d-806">Any number of annotation attributes (custom XML attributes) may be applied to the **Principal** element.</span></span> <span data-ttu-id="b588d-807">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-807">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-808">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-808">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-809">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-809">Example</span></span>

<span data-ttu-id="b588d-810">En el ejemplo siguiente se muestra un elemento **ReferentialConstraint** que forma parte de la definición de la Asociación **PublishedBy** .</span><span class="sxs-lookup"><span data-stu-id="b588d-810">The following example shows a **ReferentialConstraint** element that is part of the definition of the **PublishedBy** association.</span></span> <span data-ttu-id="b588d-811">La propiedad **ID** del tipo de entidad **Publisher** constituye el extremo principal de la restricción referencial.</span><span class="sxs-lookup"><span data-stu-id="b588d-811">The **Id** property of the **Publisher** entity type makes up the principal end of the referential constraint.</span></span>

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
 

 

## <a name="property-element-csdl"></a><span data-ttu-id="b588d-812">Property (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-812">Property Element (CSDL)</span></span>

<span data-ttu-id="b588d-813">El elemento **Property** del lenguaje de definición de esquemas conceptuales (CSDL) puede ser un elemento secundario del elemento EntityType, el elemento complexType o el elemento RowType.</span><span class="sxs-lookup"><span data-stu-id="b588d-813">The **Property** element in conceptual schema definition language (CSDL) can be a child of the EntityType element, the ComplexType element, or the RowType element.</span></span>

### <a name="entitytype-and-complextype-element-applications"></a><span data-ttu-id="b588d-814">Aplicaciones de elemento EntityType y ComplexType</span><span class="sxs-lookup"><span data-stu-id="b588d-814">EntityType and ComplexType Element Applications</span></span>

<span data-ttu-id="b588d-815">Los elementos de **propiedad** (como elementos secundarios de los elementos **EntityType** o **complexType** ) definen la forma y las características de los datos que contendrá una instancia de tipo de entidad o una instancia de tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="b588d-815">**Property** elements (as children of **EntityType** or **ComplexType** elements) define the shape and characteristics of data that an entity type instance or complex type instance will contain.</span></span> <span data-ttu-id="b588d-816">Las propiedades en un modelo conceptual son análogas a las propiedades que se definen en una clase.</span><span class="sxs-lookup"><span data-stu-id="b588d-816">Properties in a conceptual model are analogous to properties that are defined on a class.</span></span> <span data-ttu-id="b588d-817">Del mismo modo que las propiedades en una clase definen la forma de la clase y proporcionan información sobre los objetos, las propiedades en un modelo conceptual definen la forma de un tipo de entidad y proporcionan información sobre las instancias del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-817">In the same way that properties on a class define the shape of the class and carry information about objects, properties in a conceptual model define the shape of an entity type and carry information about entity type instances.</span></span>

<span data-ttu-id="b588d-818">El elemento **Property** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-818">The **Property** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-819">Elemento Documentation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-819">Documentation Element (zero or one elements allowed)</span></span>
-   <span data-ttu-id="b588d-820">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-820">Annotation elements (zero or more elements allowed)</span></span>

<span data-ttu-id="b588d-821">Las siguientes caras se pueden aplicar a un elemento **Property** : **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **collation**, **ConcurrencyMode**.</span><span class="sxs-lookup"><span data-stu-id="b588d-821">The following facets can be applied to a **Property** element: **Nullable**, **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, **Collation**, **ConcurrencyMode**.</span></span> <span data-ttu-id="b588d-822">Las facetas son atributos XML que proporcionan información sobre cómo los valores de propiedad se almacenan en el almacén de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-822">Facets are XML attributes that provide information about how property values are stored in the data store.</span></span>

> [!NOTE]
> <span data-ttu-id="b588d-823">Las caras solo se pueden aplicar a propiedades de tipo **EDMSimpleType**.</span><span class="sxs-lookup"><span data-stu-id="b588d-823">Facets can only be applied to properties of type **EDMSimpleType**.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="b588d-824">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-824">Applicable Attributes</span></span>

<span data-ttu-id="b588d-825">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="b588d-825">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="b588d-826">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-826">Attribute Name</span></span>                                                         | <span data-ttu-id="b588d-827">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-827">Is Required</span></span> | <span data-ttu-id="b588d-828">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-828">Value</span></span>                                                                                                                                                                                                                           |
|:-----------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-829">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-829">**Name**</span></span>                                                               | <span data-ttu-id="b588d-830">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-830">Yes</span></span>         | <span data-ttu-id="b588d-831">El nombre de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-831">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="b588d-832">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="b588d-832">**Type**</span></span>                                                               | <span data-ttu-id="b588d-833">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-833">Yes</span></span>         | <span data-ttu-id="b588d-834">El tipo de valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-834">The type of the property value.</span></span> <span data-ttu-id="b588d-835">El tipo de valor de la propiedad debe ser un tipo **EDMSimpleType** o un tipo complejo (indicado mediante un nombre completo) que se encuentre dentro del ámbito del modelo.</span><span class="sxs-lookup"><span data-stu-id="b588d-835">The property value type must be an **EDMSimpleType** or a complex type (indicated by a fully-qualified name) that is within scope of the model.</span></span>                                                 |
| <span data-ttu-id="b588d-836">**Admisión de valores NULL**</span><span class="sxs-lookup"><span data-stu-id="b588d-836">**Nullable**</span></span>                                                           | <span data-ttu-id="b588d-837">No</span><span class="sxs-lookup"><span data-stu-id="b588d-837">No</span></span>          | <span data-ttu-id="b588d-838">**True** (el valor predeterminado) o <strong>False</strong>, según si la propiedad puede tener un valor nulo.</span><span class="sxs-lookup"><span data-stu-id="b588d-838">**True** (the default value) or <strong>False</strong> depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                   |
| <span data-ttu-id="b588d-839">> en el CSDL v1, una propiedad de tipo complejo debe tener `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="b588d-839">> In the CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="b588d-840">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="b588d-840">**DefaultValue**</span></span>                                                       | <span data-ttu-id="b588d-841">No</span><span class="sxs-lookup"><span data-stu-id="b588d-841">No</span></span>          | <span data-ttu-id="b588d-842">Valor predeterminado de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-842">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="b588d-843">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-843">**MaxLength**</span></span>                                                          | <span data-ttu-id="b588d-844">No</span><span class="sxs-lookup"><span data-stu-id="b588d-844">No</span></span>          | <span data-ttu-id="b588d-845">La longitud máxima del valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-845">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="b588d-846">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-846">**FixedLength**</span></span>                                                        | <span data-ttu-id="b588d-847">No</span><span class="sxs-lookup"><span data-stu-id="b588d-847">No</span></span>          | <span data-ttu-id="b588d-848">**True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena de longitud fija.</span><span class="sxs-lookup"><span data-stu-id="b588d-848">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="b588d-849">**Precisión**</span><span class="sxs-lookup"><span data-stu-id="b588d-849">**Precision**</span></span>                                                          | <span data-ttu-id="b588d-850">No</span><span class="sxs-lookup"><span data-stu-id="b588d-850">No</span></span>          | <span data-ttu-id="b588d-851">Precisión del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-851">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="b588d-852">**Escala**</span><span class="sxs-lookup"><span data-stu-id="b588d-852">**Scale**</span></span>                                                              | <span data-ttu-id="b588d-853">No</span><span class="sxs-lookup"><span data-stu-id="b588d-853">No</span></span>          | <span data-ttu-id="b588d-854">Escala del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-854">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="b588d-855">**SRID**</span><span class="sxs-lookup"><span data-stu-id="b588d-855">**SRID**</span></span>                                                               | <span data-ttu-id="b588d-856">No</span><span class="sxs-lookup"><span data-stu-id="b588d-856">No</span></span>          | <span data-ttu-id="b588d-857">Identificador de referencia del sistema espacial.</span><span class="sxs-lookup"><span data-stu-id="b588d-857">Spatial System Reference Identifier.</span></span> <span data-ttu-id="b588d-858">Solo es válido para las propiedades de los tipos espaciales.</span><span class="sxs-lookup"><span data-stu-id="b588d-858">Valid only for properties of spatial types.</span></span> <span data-ttu-id="b588d-859">Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="b588d-859">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="b588d-860">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="b588d-860">**Unicode**</span></span>                                                            | <span data-ttu-id="b588d-861">No</span><span class="sxs-lookup"><span data-stu-id="b588d-861">No</span></span>          | <span data-ttu-id="b588d-862">**True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena Unicode.</span><span class="sxs-lookup"><span data-stu-id="b588d-862">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="b588d-863">**Intercalación**</span><span class="sxs-lookup"><span data-stu-id="b588d-863">**Collation**</span></span>                                                          | <span data-ttu-id="b588d-864">No</span><span class="sxs-lookup"><span data-stu-id="b588d-864">No</span></span>          | <span data-ttu-id="b588d-865">Cadena que especifica la secuencia de intercalación que se usará en el origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-865">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |
| <span data-ttu-id="b588d-866">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="b588d-866">**ConcurrencyMode**</span></span>                                                    | <span data-ttu-id="b588d-867">No</span><span class="sxs-lookup"><span data-stu-id="b588d-867">No</span></span>          | <span data-ttu-id="b588d-868">**None** (el valor predeterminado) o **Fixed**.</span><span class="sxs-lookup"><span data-stu-id="b588d-868">**None** (the default value) or **Fixed**.</span></span> <span data-ttu-id="b588d-869">Si el valor se establece en **Fixed**, el valor de la propiedad se usará en las comprobaciones de simultaneidad optimista.</span><span class="sxs-lookup"><span data-stu-id="b588d-869">If the value is set to **Fixed**, the property value will be used in optimistic concurrency checks.</span></span>                                                                                  |

 

> [!NOTE]
> <span data-ttu-id="b588d-870">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento de **propiedad** .</span><span class="sxs-lookup"><span data-stu-id="b588d-870">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="b588d-871">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-871">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-872">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-872">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="b588d-873">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-873">Example</span></span>

<span data-ttu-id="b588d-874">En el ejemplo siguiente se muestra un elemento **EntityType** con tres elementos **Property** :</span><span class="sxs-lookup"><span data-stu-id="b588d-874">The following example shows an **EntityType** element with three **Property** elements:</span></span>

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
 

<span data-ttu-id="b588d-875">En el ejemplo siguiente se muestra un elemento **complexType** con cinco elementos **Property** :</span><span class="sxs-lookup"><span data-stu-id="b588d-875">The following example shows a **ComplexType** element with five **Property** elements:</span></span>

``` xml
 <ComplexType Name="Address" >
   <Property Type="String" Name="StreetAddress" Nullable="false" />
   <Property Type="String" Name="City" Nullable="false" />
   <Property Type="String" Name="StateOrProvince" Nullable="false" />
   <Property Type="String" Name="Country" Nullable="false" />
   <Property Type="String" Name="PostalCode" Nullable="false" />
 </ComplexType>
```
 

### <a name="rowtype-element-application"></a><span data-ttu-id="b588d-876">Aplicación de elemento RowType</span><span class="sxs-lookup"><span data-stu-id="b588d-876">RowType Element Application</span></span>

<span data-ttu-id="b588d-877">Los elementos de **propiedad** (como elementos secundarios de un elemento **RowType** ) definen la forma y las características de los datos que se pueden pasar o devolver desde una función definida por el modelo.</span><span class="sxs-lookup"><span data-stu-id="b588d-877">**Property** elements (as the children of a **RowType** element) define the shape and characteristics of data that can be passed to or returned from a model-defined function.</span></span>  

<span data-ttu-id="b588d-878">El elemento **Property** puede tener exactamente uno de los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="b588d-878">The **Property** element can have exactly one of the following child elements:</span></span>

-   <span data-ttu-id="b588d-879">CollectionType</span><span class="sxs-lookup"><span data-stu-id="b588d-879">CollectionType</span></span>
-   <span data-ttu-id="b588d-880">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="b588d-880">ReferenceType</span></span>
-   <span data-ttu-id="b588d-881">RowType</span><span class="sxs-lookup"><span data-stu-id="b588d-881">RowType</span></span>

<span data-ttu-id="b588d-882">El elemento **Property** puede tener cualquier número de elementos Annotation secundarios.</span><span class="sxs-lookup"><span data-stu-id="b588d-882">The **Property** element can have any number child annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="b588d-883">Los elementos Annotation solo se permiten en CSDL V2 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="b588d-883">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

#### <a name="applicable-attributes"></a><span data-ttu-id="b588d-884">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-884">Applicable Attributes</span></span>

<span data-ttu-id="b588d-885">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Property** .</span><span class="sxs-lookup"><span data-stu-id="b588d-885">The following table describes the attributes that can be applied to the **Property** element.</span></span>

| <span data-ttu-id="b588d-886">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-886">Attribute Name</span></span>                                                     | <span data-ttu-id="b588d-887">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-887">Is Required</span></span> | <span data-ttu-id="b588d-888">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-888">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-889">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-889">**Name**</span></span>                                                           | <span data-ttu-id="b588d-890">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-890">Yes</span></span>         | <span data-ttu-id="b588d-891">El nombre de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-891">The name of the property.</span></span>                                                                                                                                                                                                       |
| <span data-ttu-id="b588d-892">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="b588d-892">**Type**</span></span>                                                           | <span data-ttu-id="b588d-893">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-893">Yes</span></span>         | <span data-ttu-id="b588d-894">El tipo de valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-894">The type of the property value.</span></span>                                                                                                                                                                                                 |
| <span data-ttu-id="b588d-895">**Admisión de valores NULL**</span><span class="sxs-lookup"><span data-stu-id="b588d-895">**Nullable**</span></span>                                                       | <span data-ttu-id="b588d-896">No</span><span class="sxs-lookup"><span data-stu-id="b588d-896">No</span></span>          | <span data-ttu-id="b588d-897">**True** (el valor predeterminado) o **False**, según si la propiedad puede tener un valor nulo.</span><span class="sxs-lookup"><span data-stu-id="b588d-897">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="b588d-898">> en CSDL v1, una propiedad de tipo complejo debe tener `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="b588d-898">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="b588d-899">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="b588d-899">**DefaultValue**</span></span>                                                   | <span data-ttu-id="b588d-900">No</span><span class="sxs-lookup"><span data-stu-id="b588d-900">No</span></span>          | <span data-ttu-id="b588d-901">Valor predeterminado de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-901">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="b588d-902">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-902">**MaxLength**</span></span>                                                      | <span data-ttu-id="b588d-903">No</span><span class="sxs-lookup"><span data-stu-id="b588d-903">No</span></span>          | <span data-ttu-id="b588d-904">La longitud máxima del valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-904">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="b588d-905">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-905">**FixedLength**</span></span>                                                    | <span data-ttu-id="b588d-906">No</span><span class="sxs-lookup"><span data-stu-id="b588d-906">No</span></span>          | <span data-ttu-id="b588d-907">**True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena de longitud fija.</span><span class="sxs-lookup"><span data-stu-id="b588d-907">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="b588d-908">**Precisión**</span><span class="sxs-lookup"><span data-stu-id="b588d-908">**Precision**</span></span>                                                      | <span data-ttu-id="b588d-909">No</span><span class="sxs-lookup"><span data-stu-id="b588d-909">No</span></span>          | <span data-ttu-id="b588d-910">Precisión del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-910">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="b588d-911">**Escala**</span><span class="sxs-lookup"><span data-stu-id="b588d-911">**Scale**</span></span>                                                          | <span data-ttu-id="b588d-912">No</span><span class="sxs-lookup"><span data-stu-id="b588d-912">No</span></span>          | <span data-ttu-id="b588d-913">Escala del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-913">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="b588d-914">**SRID**</span><span class="sxs-lookup"><span data-stu-id="b588d-914">**SRID**</span></span>                                                           | <span data-ttu-id="b588d-915">No</span><span class="sxs-lookup"><span data-stu-id="b588d-915">No</span></span>          | <span data-ttu-id="b588d-916">Identificador de referencia del sistema espacial.</span><span class="sxs-lookup"><span data-stu-id="b588d-916">Spatial System Reference Identifier.</span></span> <span data-ttu-id="b588d-917">Solo es válido para las propiedades de los tipos espaciales.</span><span class="sxs-lookup"><span data-stu-id="b588d-917">Valid only for properties of spatial types.</span></span> <span data-ttu-id="b588d-918">Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="b588d-918">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="b588d-919">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="b588d-919">**Unicode**</span></span>                                                        | <span data-ttu-id="b588d-920">No</span><span class="sxs-lookup"><span data-stu-id="b588d-920">No</span></span>          | <span data-ttu-id="b588d-921">**True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena Unicode.</span><span class="sxs-lookup"><span data-stu-id="b588d-921">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="b588d-922">**Intercalación**</span><span class="sxs-lookup"><span data-stu-id="b588d-922">**Collation**</span></span>                                                      | <span data-ttu-id="b588d-923">No</span><span class="sxs-lookup"><span data-stu-id="b588d-923">No</span></span>          | <span data-ttu-id="b588d-924">Cadena que especifica la secuencia de intercalación que se usará en el origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-924">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="b588d-925">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento de **propiedad** .</span><span class="sxs-lookup"><span data-stu-id="b588d-925">Any number of annotation attributes (custom XML attributes) may be applied to the **Property** element.</span></span> <span data-ttu-id="b588d-926">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-926">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-927">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-927">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

#### <a name="example"></a><span data-ttu-id="b588d-928">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-928">Example</span></span>

<span data-ttu-id="b588d-929">En el ejemplo siguiente se muestran los elementos de **propiedad** que se utilizan para definir la forma del tipo de valor devuelto de una función definida por el modelo.</span><span class="sxs-lookup"><span data-stu-id="b588d-929">The following example shows **Property** elements used to define the shape of the return type of a model-defined function.</span></span>

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
 

 

## <a name="propertyref-element-csdl"></a><span data-ttu-id="b588d-930">PropertyRef (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-930">PropertyRef Element (CSDL)</span></span>

<span data-ttu-id="b588d-931">El elemento **PropertyRef** en el lenguaje de definición de esquemas conceptuales (CSDL) hace referencia a una propiedad de un tipo de entidad para indicar que la propiedad realizará uno de los roles siguientes:</span><span class="sxs-lookup"><span data-stu-id="b588d-931">The **PropertyRef** element in conceptual schema definition language (CSDL) references a property of an entity type to indicate that the property will perform one of the following roles:</span></span>

-   <span data-ttu-id="b588d-932">Parte de la clave de la entidad (una propiedad o un conjunto de propiedades de un tipo de entidad que determinan la identidad).</span><span class="sxs-lookup"><span data-stu-id="b588d-932">Part of the entity's key (a property or a set of properties of an entity type that determine identity).</span></span> <span data-ttu-id="b588d-933">Se pueden usar uno o varios elementos **PropertyRef** para definir una clave de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-933">One or more **PropertyRef** elements can be used to define an entity key.</span></span>
-   <span data-ttu-id="b588d-934">El extremo dependiente o principal de una restricción referencial.</span><span class="sxs-lookup"><span data-stu-id="b588d-934">The dependent or principal end of a referential constraint.</span></span>

<span data-ttu-id="b588d-935">El elemento **PropertyRef** solo puede tener elementos Annotation (cero o más) como elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="b588d-935">The **PropertyRef** element can only have annotation elements (zero or more) as child elements.</span></span>

> [!NOTE]
> <span data-ttu-id="b588d-936">Los elementos Annotation solo se permiten en CSDL V2 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="b588d-936">Annotation elements are only allowed in CSDL v2 and later.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-937">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-937">Applicable Attributes</span></span>

<span data-ttu-id="b588d-938">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="b588d-938">The table below describes the attributes that can be applied to the **PropertyRef** element.</span></span>

| <span data-ttu-id="b588d-939">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-939">Attribute Name</span></span> | <span data-ttu-id="b588d-940">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-940">Is Required</span></span> | <span data-ttu-id="b588d-941">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-941">Value</span></span>                                |
|:---------------|:------------|:-------------------------------------|
| <span data-ttu-id="b588d-942">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="b588d-942">**Name**</span></span>       | <span data-ttu-id="b588d-943">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-943">Yes</span></span>         | <span data-ttu-id="b588d-944">Nombre de la propiedad a la que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="b588d-944">The name of the referenced property.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-945">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **PropertyRef** .</span><span class="sxs-lookup"><span data-stu-id="b588d-945">Any number of annotation attributes (custom XML attributes) may be applied to the **PropertyRef** element.</span></span> <span data-ttu-id="b588d-946">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-946">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-947">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-947">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-948">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-948">Example</span></span>

<span data-ttu-id="b588d-949">En el ejemplo siguiente se define un tipo de entidad (**book**).</span><span class="sxs-lookup"><span data-stu-id="b588d-949">The example below defines an entity type (**Book**).</span></span> <span data-ttu-id="b588d-950">La clave de entidad se define haciendo referencia a la propiedad **ISBN** del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-950">The entity key is defined by referencing the **ISBN** property of the entity type.</span></span>

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
 

<span data-ttu-id="b588d-951">En el ejemplo siguiente, se usan dos elementos **PropertyRef** para indicar que dos propiedades (**ID** y **PublisherId**) son los extremos principal y dependiente de una restricción referencial.</span><span class="sxs-lookup"><span data-stu-id="b588d-951">In the next example, two **PropertyRef** elements are used to indicate that two properties (**Id** and **PublisherId**) are the principal and dependent ends of a referential constraint.</span></span>

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
 

 

## <a name="referencetype-element-csdl"></a><span data-ttu-id="b588d-952">ReferenceType (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-952">ReferenceType Element (CSDL)</span></span>

<span data-ttu-id="b588d-953">El elemento **referenceType** en el lenguaje de definición de esquemas conceptuales (CSDL) especifica una referencia a un tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-953">The **ReferenceType** element in conceptual schema definition language (CSDL) specifies a reference to an entity type.</span></span> <span data-ttu-id="b588d-954">El elemento **referenceType** puede ser un elemento secundario de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="b588d-954">The **ReferenceType** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="b588d-955">ReturnType (función)</span><span class="sxs-lookup"><span data-stu-id="b588d-955">ReturnType (Function)</span></span>
-   <span data-ttu-id="b588d-956">Parámetro</span><span class="sxs-lookup"><span data-stu-id="b588d-956">Parameter</span></span>
-   <span data-ttu-id="b588d-957">CollectionType</span><span class="sxs-lookup"><span data-stu-id="b588d-957">CollectionType</span></span>

<span data-ttu-id="b588d-958">El elemento **referenceType** se utiliza al definir un parámetro o un tipo de valor devuelto para una función.</span><span class="sxs-lookup"><span data-stu-id="b588d-958">The **ReferenceType** element is used when defining a parameter or return type for a function.</span></span>

<span data-ttu-id="b588d-959">Un elemento **referenceType** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-959">A **ReferenceType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-960">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-960">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b588d-961">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-961">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-962">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-962">Applicable Attributes</span></span>

<span data-ttu-id="b588d-963">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **referenceType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-963">The table below describes the attributes that can be applied to the **ReferenceType** element.</span></span>

| <span data-ttu-id="b588d-964">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-964">Attribute Name</span></span> | <span data-ttu-id="b588d-965">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-965">Is Required</span></span> | <span data-ttu-id="b588d-966">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-966">Value</span></span>                                         |
|:---------------|:------------|:----------------------------------------------|
| <span data-ttu-id="b588d-967">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="b588d-967">**Type**</span></span>       | <span data-ttu-id="b588d-968">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-968">Yes</span></span>         | <span data-ttu-id="b588d-969">Nombre del tipo de entidad al que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="b588d-969">The name of the entity type being referenced.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-970">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **referenceType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-970">Any number of annotation attributes (custom XML attributes) may be applied to the **ReferenceType** element.</span></span> <span data-ttu-id="b588d-971">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-971">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-972">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-972">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-973">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-973">Example</span></span>

<span data-ttu-id="b588d-974">En el ejemplo siguiente se muestra el elemento **referenceType** que se usa como elemento secundario de un elemento **Parameter** en una función definida por el modelo que acepta una referencia a un tipo de entidad **Person** :</span><span class="sxs-lookup"><span data-stu-id="b588d-974">The following example shows the **ReferenceType** element used as a child of a **Parameter** element in a model-defined function that accepts a reference to a **Person** entity type:</span></span>

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
 

<span data-ttu-id="b588d-975">En el ejemplo siguiente se muestra el elemento **referenceType** usado como elemento secundario de un elemento **ReturnType** (function) en una función definida por el modelo que devuelve una referencia a un tipo de entidad **Person** :</span><span class="sxs-lookup"><span data-stu-id="b588d-975">The following example shows the **ReferenceType** element used as a child of a **ReturnType** (Function) element in a model-defined function that returns a reference to a **Person** entity type:</span></span>

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
 

 

## <a name="referentialconstraint-element-csdl"></a><span data-ttu-id="b588d-976">ReferentialConstraint (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-976">ReferentialConstraint Element (CSDL)</span></span>

<span data-ttu-id="b588d-977">Un elemento **ReferentialConstraint** en el lenguaje de definición de esquemas conceptuales (CSDL) define una funcionalidad similar a una restricción de integridad referencial en una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="b588d-977">A **ReferentialConstraint** element in conceptual schema definition language (CSDL) defines functionality that is similar to a referential integrity constraint in a relational database.</span></span> <span data-ttu-id="b588d-978">Del mismo modo que una columna (o columnas) de una tabla de base de datos puede hacer referencia a la clave principal de otra tabla, una propiedad (o propiedades) de un tipo de entidad puede hacer referencia a la clave de entidad de otro tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-978">In the same way that a column (or columns) from a database table can reference the primary key of another table, a property (or properties) of an entity type can reference the entity key of another entity type.</span></span> <span data-ttu-id="b588d-979">El tipo de entidad al que se hace referencia se denomina *extremo principal* de la restricción.</span><span class="sxs-lookup"><span data-stu-id="b588d-979">The entity type that is referenced is called the *principal end* of the constraint.</span></span> <span data-ttu-id="b588d-980">El tipo de entidad que hace referencia al extremo principal se denomina *extremo dependiente* de la restricción.</span><span class="sxs-lookup"><span data-stu-id="b588d-980">The entity type that references the principal end is called the *dependent end* of the constraint.</span></span>

<span data-ttu-id="b588d-981">Si una clave externa que se expone en un tipo de entidad hace referencia a una propiedad en otro tipo de entidad, el elemento **ReferentialConstraint** define una asociación entre los dos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-981">If a foreign key that is exposed on one entity type references a property on another entity type, the **ReferentialConstraint** element defines an association between the two entity types.</span></span> <span data-ttu-id="b588d-982">Dado que el elemento **ReferentialConstraint** proporciona información sobre cómo se relacionan dos tipos de entidad, no es necesario ningún elemento AssociationSetMapping correspondiente en el lenguaje de especificación de asignaciones (MSL).</span><span class="sxs-lookup"><span data-stu-id="b588d-982">Because the **ReferentialConstraint** element provides information about how two entity types are related, no corresponding AssociationSetMapping element is necessary in the mapping specification language (MSL).</span></span> <span data-ttu-id="b588d-983">Una asociación entre dos tipos de entidad que no tienen claves externas expuestas debe tener un elemento **AssociationSetMapping** correspondiente para asignar información de asociación al origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-983">An association between two entity types that do not have foreign keys exposed must have a corresponding **AssociationSetMapping** element in order to map association information to the data source.</span></span>

<span data-ttu-id="b588d-984">Si una clave externa no se expone en un tipo de entidad, el elemento **ReferentialConstraint** solo puede definir una restricción PRIMARY KEY-PRIMARY KEY entre el tipo de entidad y otro tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-984">If a foreign key is not exposed on an entity type, the **ReferentialConstraint** element can only define a primary key-to-primary key constraint between the entity type and another entity type.</span></span>

<span data-ttu-id="b588d-985">Un elemento **ReferentialConstraint** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-985">A **ReferentialConstraint** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-986">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-986">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b588d-987">Principal (exactamente un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-987">Principal (exactly one element)</span></span>
-   <span data-ttu-id="b588d-988">Dependent (exactamente un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-988">Dependent (exactly one element)</span></span>
-   <span data-ttu-id="b588d-989">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-989">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-990">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-990">Applicable Attributes</span></span>

<span data-ttu-id="b588d-991">El elemento **ReferentialConstraint** puede tener cualquier número de atributos de anotación (atributos XML personalizados).</span><span class="sxs-lookup"><span data-stu-id="b588d-991">The **ReferentialConstraint** element can have any number of annotation attributes (custom XML attributes).</span></span> <span data-ttu-id="b588d-992">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-992">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-993">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-993">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b588d-994">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-994">Example</span></span>

<span data-ttu-id="b588d-995">En el ejemplo siguiente se muestra un elemento **ReferentialConstraint** que se usa como parte de la definición de la Asociación **PublishedBy** .</span><span class="sxs-lookup"><span data-stu-id="b588d-995">The following example shows a **ReferentialConstraint** element being used as part of the definition of the **PublishedBy** association.</span></span>

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
 

 

## <a name="returntype-function-element-csdl"></a><span data-ttu-id="b588d-996">ReturnType (function) (elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-996">ReturnType (Function) Element (CSDL)</span></span>

<span data-ttu-id="b588d-997">El elemento **ReturnType** (function) en el lenguaje de definición de esquemas conceptuales (CSDL) especifica el tipo de valor devuelto para una función que se define en un elemento de función.</span><span class="sxs-lookup"><span data-stu-id="b588d-997">The **ReturnType** (Function) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a Function element.</span></span> <span data-ttu-id="b588d-998">Un tipo de valor devuelto de función también se puede especificar con un atributo **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-998">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="b588d-999">Los tipos de valor devuelto pueden ser cualquier **EdmSimpleType**, tipo de entidad, tipo complejo, tipo de fila, tipo de referencia o una colección de uno de estos tipos.</span><span class="sxs-lookup"><span data-stu-id="b588d-999">Return types can be any **EdmSimpleType**, entity type, complex type, row type, ref type, or a collection of one of these types.</span></span>

<span data-ttu-id="b588d-1000">El tipo de valor devuelto de una función se puede especificar con el atributo **Type** del elemento **ReturnType** (function) o con uno de los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="b588d-1000">The return type of a function can be specified with either the **Type** attribute of the **ReturnType** (Function) element, or with one of the following child elements:</span></span>

-   <span data-ttu-id="b588d-1001">CollectionType</span><span class="sxs-lookup"><span data-stu-id="b588d-1001">CollectionType</span></span>
-   <span data-ttu-id="b588d-1002">ReferenceType</span><span class="sxs-lookup"><span data-stu-id="b588d-1002">ReferenceType</span></span>
-   <span data-ttu-id="b588d-1003">RowType</span><span class="sxs-lookup"><span data-stu-id="b588d-1003">RowType</span></span>

> [!NOTE]
> <span data-ttu-id="b588d-1004">Un modelo no se validará si especifica un tipo de valor devuelto de función con el atributo **Type** del elemento **ReturnType** (function) y uno de los elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="b588d-1004">A model will not validate if you specify a function return type with both the **Type** attribute of the **ReturnType** (Function) element and one of the child elements.</span></span>

 

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-1005">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-1005">Applicable Attributes</span></span>

<span data-ttu-id="b588d-1006">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **ReturnType** (function).</span><span class="sxs-lookup"><span data-stu-id="b588d-1006">The following table describes the attributes that can be applied to the **ReturnType** (Function) element.</span></span>

| <span data-ttu-id="b588d-1007">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-1007">Attribute Name</span></span> | <span data-ttu-id="b588d-1008">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-1008">Is Required</span></span> | <span data-ttu-id="b588d-1009">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-1009">Value</span></span>                              |
|:---------------|:------------|:-----------------------------------|
| <span data-ttu-id="b588d-1010">**ReturnType**</span><span class="sxs-lookup"><span data-stu-id="b588d-1010">**ReturnType**</span></span> | <span data-ttu-id="b588d-1011">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1011">No</span></span>          | <span data-ttu-id="b588d-1012">El tipo devuelto por la función.</span><span class="sxs-lookup"><span data-stu-id="b588d-1012">The type returned by the function.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-1013">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **ReturnType** (function).</span><span class="sxs-lookup"><span data-stu-id="b588d-1013">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (Function) element.</span></span> <span data-ttu-id="b588d-1014">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1014">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-1015">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1015">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-1016">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-1016">Example</span></span>

<span data-ttu-id="b588d-1017">En el ejemplo siguiente se usa un elemento **function** para definir una función que devuelve el número de años que un libro se ha impreso.</span><span class="sxs-lookup"><span data-stu-id="b588d-1017">The following example uses a **Function** element to define a function that returns the number of years a book has been in print.</span></span> <span data-ttu-id="b588d-1018">Tenga en cuenta que el tipo de valor devuelto se especifica mediante el atributo **Type** de un elemento **ReturnType** (function).</span><span class="sxs-lookup"><span data-stu-id="b588d-1018">Note that the return type is specified by the **Type** attribute of a **ReturnType** (Function) element.</span></span>

``` xml
 <Function Name="GetYearsInPrint">
   <ReturnType Type=="Edm.Int32">
   <Parameter Name="book" Type="BooksModel.Book" />
   <DefiningExpression>
    Year(CurrentDateTime()) - Year(cast(book.PublishedDate as DateTime))
   </DefiningExpression>
 </Function>
```
 

 

## <a name="returntype-functionimport-element-csdl"></a><span data-ttu-id="b588d-1019">ReturnType (FunctionImport) (elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-1019">ReturnType (FunctionImport) Element (CSDL)</span></span>

<span data-ttu-id="b588d-1020">El elemento **ReturnType** (FunctionImport) en el lenguaje de definición de esquemas conceptuales (CSDL) especifica el tipo de valor devuelto para una función que se define en un elemento FunctionImport.</span><span class="sxs-lookup"><span data-stu-id="b588d-1020">The **ReturnType** (FunctionImport) element in conceptual schema definition language (CSDL) specifies the return type for a function that is defined in a FunctionImport element.</span></span> <span data-ttu-id="b588d-1021">Un tipo de valor devuelto de función también se puede especificar con un atributo **ReturnType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1021">A function return type can also be specified with a **ReturnType** attribute.</span></span>

<span data-ttu-id="b588d-1022">Los tipos devueltos pueden ser cualquier colección de tipo de entidad, tipo complejo o **EdmSimpleType**.</span><span class="sxs-lookup"><span data-stu-id="b588d-1022">Return types can be any collection of entity type, complex type,or **EdmSimpleType**,</span></span>

<span data-ttu-id="b588d-1023">El tipo de valor devuelto de una función se especifica con el atributo **Type** del elemento **ReturnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="b588d-1023">The return type of a function is specified with the **Type** attribute of the **ReturnType** (FunctionImport) element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-1024">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-1024">Applicable Attributes</span></span>

<span data-ttu-id="b588d-1025">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **ReturnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="b588d-1025">The following table describes the attributes that can be applied to the **ReturnType** (FunctionImport) element.</span></span>

| <span data-ttu-id="b588d-1026">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-1026">Attribute Name</span></span> | <span data-ttu-id="b588d-1027">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-1027">Is Required</span></span> | <span data-ttu-id="b588d-1028">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-1028">Value</span></span>                                                                                                                                                                                                 |
|:---------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-1029">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="b588d-1029">**Type**</span></span>       | <span data-ttu-id="b588d-1030">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1030">No</span></span>          | <span data-ttu-id="b588d-1031">Tipo devuelto por la función.</span><span class="sxs-lookup"><span data-stu-id="b588d-1031">The type that the function returns.</span></span> <span data-ttu-id="b588d-1032">El valor debe ser una colección de ComplexType, EntityType o EDMSimpleType.</span><span class="sxs-lookup"><span data-stu-id="b588d-1032">The value must be a collection of ComplexType, EntityType, or EDMSimpleType.</span></span>                                                                                      |
| <span data-ttu-id="b588d-1033">**EntitySet**</span><span class="sxs-lookup"><span data-stu-id="b588d-1033">**EntitySet**</span></span>  | <span data-ttu-id="b588d-1034">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1034">No</span></span>          | <span data-ttu-id="b588d-1035">Si la función devuelve una colección de tipos de entidad, el valor de **EntitySet** debe ser el conjunto de entidades al que pertenece la colección.</span><span class="sxs-lookup"><span data-stu-id="b588d-1035">If the function returns a collection of entity types, the value of the **EntitySet** must be the entity set to which the collection belongs.</span></span> <span data-ttu-id="b588d-1036">De lo contrario, el atributo **EntitySet** no debe usarse.</span><span class="sxs-lookup"><span data-stu-id="b588d-1036">Otherwise, the **EntitySet** attribute must not be used.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-1037">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **ReturnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="b588d-1037">Any number of annotation attributes (custom XML attributes) may be applied to the **ReturnType** (FunctionImport) element.</span></span> <span data-ttu-id="b588d-1038">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1038">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-1039">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1039">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-1040">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-1040">Example</span></span>

<span data-ttu-id="b588d-1041">En el ejemplo siguiente se usa una **FunctionImport** que devuelve libros y publicadores.</span><span class="sxs-lookup"><span data-stu-id="b588d-1041">The following example uses a **FunctionImport** that returns books and publishers.</span></span> <span data-ttu-id="b588d-1042">Tenga en cuenta que la función devuelve dos conjuntos de resultados y, por tanto, se especifican dos elementos **ReturnType** (FunctionImport).</span><span class="sxs-lookup"><span data-stu-id="b588d-1042">Note that the function returns two result sets and therefore two **ReturnType** (FunctionImport) elements are specified.</span></span>

``` xml
 <FunctionImport Name="GetBooksAndPublishers">
   <ReturnType Type=="Collection(BooksModel.Book )" EntitySet=”Books”>
   <ReturnType Type=="Collection(BooksModel.Publisher)" EntitySet=”Publishers”>
 </FunctionImport>
```
 

 

## <a name="rowtype-element-csdl"></a><span data-ttu-id="b588d-1043">RowType (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-1043">RowType Element (CSDL)</span></span>

<span data-ttu-id="b588d-1044">Un elemento **RowType** en el lenguaje de definición de esquemas conceptuales (CSDL) define una estructura sin nombre como un parámetro o tipo de valor devuelto para una función definida en el modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-1044">A **RowType** element in conceptual schema definition language (CSDL) defines an unnamed structure as a parameter or return type for a function defined in the conceptual model.</span></span>

<span data-ttu-id="b588d-1045">Un elemento **RowType** puede ser el elemento secundario de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="b588d-1045">A **RowType** element can be the child of the following elements:</span></span>

-   <span data-ttu-id="b588d-1046">CollectionType</span><span class="sxs-lookup"><span data-stu-id="b588d-1046">CollectionType</span></span>
-   <span data-ttu-id="b588d-1047">Parámetro</span><span class="sxs-lookup"><span data-stu-id="b588d-1047">Parameter</span></span>
-   <span data-ttu-id="b588d-1048">ReturnType (función)</span><span class="sxs-lookup"><span data-stu-id="b588d-1048">ReturnType (Function)</span></span>

<span data-ttu-id="b588d-1049">Un elemento **RowType** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-1049">A **RowType** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-1050">Property (uno o varios)</span><span class="sxs-lookup"><span data-stu-id="b588d-1050">Property (one or more)</span></span>
-   <span data-ttu-id="b588d-1051">Elementos Annotation (cero o más)</span><span class="sxs-lookup"><span data-stu-id="b588d-1051">Annotation elements (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-1052">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-1052">Applicable Attributes</span></span>

<span data-ttu-id="b588d-1053">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **RowType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1053">Any number of annotation attributes (custom XML attributes) may be applied to the **RowType** element.</span></span> <span data-ttu-id="b588d-1054">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1054">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-1055">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1055">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

### <a name="example"></a><span data-ttu-id="b588d-1056">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-1056">Example</span></span>

<span data-ttu-id="b588d-1057">En el ejemplo siguiente se muestra una función definida por el modelo que utiliza un elemento **CollectionType** para especificar que la función devuelve una colección de filas (tal y como se especifica en el elemento **RowType** ).</span><span class="sxs-lookup"><span data-stu-id="b588d-1057">The following example shows a model-defined function that uses a **CollectionType** element to specify that the function returns a collection of rows (as specified in the **RowType** element).</span></span>

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

## <a name="schema-element-csdl"></a><span data-ttu-id="b588d-1058">Schema (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-1058">Schema Element (CSDL)</span></span>

<span data-ttu-id="b588d-1059">El elemento **Schema** es el elemento raíz de una definición de modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-1059">The **Schema** element is the root element of a conceptual model definition.</span></span> <span data-ttu-id="b588d-1060">Contiene las definiciones para los objetos, las funciones y los contenedores que conforman un modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-1060">It contains definitions for the objects, functions, and containers that make up a conceptual model.</span></span>

<span data-ttu-id="b588d-1061">El elemento **Schema** puede contener cero o más de los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="b588d-1061">The **Schema** element may contain zero or more of the following child elements:</span></span>

-   <span data-ttu-id="b588d-1062">Uso</span><span class="sxs-lookup"><span data-stu-id="b588d-1062">Using</span></span>
-   <span data-ttu-id="b588d-1063">EntityContainer</span><span class="sxs-lookup"><span data-stu-id="b588d-1063">EntityContainer</span></span>
-   <span data-ttu-id="b588d-1064">EntityType</span><span class="sxs-lookup"><span data-stu-id="b588d-1064">EntityType</span></span>
-   <span data-ttu-id="b588d-1065">EnumType</span><span class="sxs-lookup"><span data-stu-id="b588d-1065">EnumType</span></span>
-   <span data-ttu-id="b588d-1066">Asociación</span><span class="sxs-lookup"><span data-stu-id="b588d-1066">Association</span></span>
-   <span data-ttu-id="b588d-1067">ComplexType</span><span class="sxs-lookup"><span data-stu-id="b588d-1067">ComplexType</span></span>
-   <span data-ttu-id="b588d-1068">Función</span><span class="sxs-lookup"><span data-stu-id="b588d-1068">Function</span></span>

<span data-ttu-id="b588d-1069">Un elemento **Schema** puede contener cero o un elemento Annotation.</span><span class="sxs-lookup"><span data-stu-id="b588d-1069">A **Schema** element may contain zero or one Annotation elements.</span></span>

> [!NOTE]
> <span data-ttu-id="b588d-1070">El elemento de **función** y los elementos de anotación solo se permiten en CSDL V2 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="b588d-1070">The **Function** element and annotation elements are only allowed in CSDL v2 and later.</span></span>

 

<span data-ttu-id="b588d-1071">El elemento **Schema** usa el atributo **namespace** para definir el espacio de nombres para el tipo de entidad, el tipo complejo y los objetos de Asociación de un modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-1071">The **Schema** element uses the **Namespace** attribute to define the namespace for the entity type, complex type, and association objects in a conceptual model.</span></span> <span data-ttu-id="b588d-1072">Dentro de un espacio de nombres, no puede haber dos objetos con el mismo nombre.</span><span class="sxs-lookup"><span data-stu-id="b588d-1072">Within a namespace, no two objects can have the same name.</span></span> <span data-ttu-id="b588d-1073">Los espacios de nombres pueden abarcar varios elementos de **esquema** y varios archivos. CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1073">Namespaces can span multiple **Schema** elements and multiple .csdl files.</span></span>

<span data-ttu-id="b588d-1074">Un espacio de nombres del modelo conceptual es diferente del espacio de nombres XML del elemento **Schema** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1074">A conceptual model namespace is different from the XML namespace of the **Schema** element.</span></span> <span data-ttu-id="b588d-1075">Un espacio de nombres del modelo conceptual (tal y como se define en el atributo de **espacio de nombres** ) es un contenedor lógico para tipos de entidad, tipos complejos y tipos de asociación.</span><span class="sxs-lookup"><span data-stu-id="b588d-1075">A conceptual model namespace (as defined by the **Namespace** attribute) is a logical container for entity types, complex types, and association types.</span></span> <span data-ttu-id="b588d-1076">El espacio de nombres XML (indicado por el atributo **xmlns** ) de un elemento **Schema** es el espacio de nombres predeterminado para los elementos secundarios y los atributos del elemento **Schema** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1076">The XML namespace (indicated by the **xmlns** attribute) of a **Schema** element is the default namespace for child elements and attributes of the **Schema** element.</span></span> <span data-ttu-id="b588d-1077">Los espacios de nombres XML del formulario https://schemas.microsoft.com/ado/YYYY/MM/edm (donde YYYY y mm representan un año y un mes respectivamente) se reservan para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1077">XML namespaces of the form https://schemas.microsoft.com/ado/YYYY/MM/edm (where YYYY and MM represent a year and month respectively) are reserved for CSDL.</span></span> <span data-ttu-id="b588d-1078">No puede haber elementos y atributos personalizados en espacios de nombres que tengan este formato.</span><span class="sxs-lookup"><span data-stu-id="b588d-1078">Custom elements and attributes cannot be in namespaces that have this form.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-1079">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-1079">Applicable Attributes</span></span>

<span data-ttu-id="b588d-1080">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **Schema** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1080">The table below describes the attributes can be applied to the **Schema** element.</span></span>

| <span data-ttu-id="b588d-1081">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-1081">Attribute Name</span></span> | <span data-ttu-id="b588d-1082">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-1082">Is Required</span></span> | <span data-ttu-id="b588d-1083">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-1083">Value</span></span>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-1084">**Espacio de nombres**</span><span class="sxs-lookup"><span data-stu-id="b588d-1084">**Namespace**</span></span>  | <span data-ttu-id="b588d-1085">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1085">Yes</span></span>         | <span data-ttu-id="b588d-1086">El espacio de nombres del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-1086">The namespace of the conceptual model.</span></span> <span data-ttu-id="b588d-1087">El valor del atributo **namespace** se usa para formar el nombre completo de un tipo.</span><span class="sxs-lookup"><span data-stu-id="b588d-1087">The value of the **Namespace** attribute is used to form the fully qualified name of a type.</span></span> <span data-ttu-id="b588d-1088">Por ejemplo, si un **EntityType** denominado *Customer* está en el espacio de nombres simple. example. Model, el nombre completo del **EntityType** es SimpleExampleModel. Customer.</span><span class="sxs-lookup"><span data-stu-id="b588d-1088">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace, then the fully qualified name of the **EntityType** is SimpleExampleModel.Customer.</span></span> <br/> <span data-ttu-id="b588d-1089">Las siguientes cadenas no se pueden usar como el valor del atributo **namespace** : **System**, **Transient**o **EDM**.</span><span class="sxs-lookup"><span data-stu-id="b588d-1089">The following strings cannot be used as the value for the **Namespace** attribute: **System**, **Transient**, or **Edm**.</span></span> <span data-ttu-id="b588d-1090">El valor del atributo **namespace** no puede ser el mismo que el valor del atributo **namespace** del elemento Schema de SSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1090">The value for the **Namespace** attribute cannot be the same as the value for the **Namespace** attribute in the SSDL Schema element.</span></span> |
| <span data-ttu-id="b588d-1091">**Alias**</span><span class="sxs-lookup"><span data-stu-id="b588d-1091">**Alias**</span></span>      | <span data-ttu-id="b588d-1092">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1092">No</span></span>          | <span data-ttu-id="b588d-1093">Un identificador usado en lugar del nombre del espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="b588d-1093">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="b588d-1094">Por ejemplo, si un **EntityType** denominado *Customer* está en el espacio de nombres simple. example. Model y el valor del atributo **alias** es *Model*, puede usar Model. Customer como nombre completo del **EntityType.**</span><span class="sxs-lookup"><span data-stu-id="b588d-1094">For example, if an **EntityType** named *Customer* is in the Simple.Example.Model namespace and the value of the **Alias** attribute is *Model*, then you can use Model.Customer as the fully qualified name of the **EntityType.**</span></span>                                                                                                                                                                                                                                                                                                     |

 

> [!NOTE]
> <span data-ttu-id="b588d-1095">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **Schema** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1095">Any number of annotation attributes (custom XML attributes) may be applied to the **Schema** element.</span></span> <span data-ttu-id="b588d-1096">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1096">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-1097">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1097">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-1098">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-1098">Example</span></span>

<span data-ttu-id="b588d-1099">En el ejemplo siguiente se muestra un elemento **Schema** que contiene un elemento **EntityContainer** , dos elementos **EntityType** y un elemento **Association** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1099">The following example shows a **Schema** element that contains an **EntityContainer** element, two **EntityType** elements, and one **Association** element.</span></span>

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
 

 

## <a name="typeref-element-csdl"></a><span data-ttu-id="b588d-1100">TypeRef (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-1100">TypeRef Element (CSDL)</span></span>

<span data-ttu-id="b588d-1101">El elemento **TypeRef** en el lenguaje de definición de esquemas conceptuales (CSDL) proporciona una referencia a un tipo con nombre existente.</span><span class="sxs-lookup"><span data-stu-id="b588d-1101">The **TypeRef** element in conceptual schema definition language (CSDL) provides a reference to an existing named type.</span></span> <span data-ttu-id="b588d-1102">El elemento **TypeRef** puede ser un elemento secundario del elemento CollectionType, que se usa para especificar que una función tiene una colección como parámetro o tipo de valor devuelto.</span><span class="sxs-lookup"><span data-stu-id="b588d-1102">The **TypeRef** element can be a child of the CollectionType element, which is used to specify that a function has a collection as a parameter or return type.</span></span>

<span data-ttu-id="b588d-1103">Un elemento **TypeRef** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="b588d-1103">A **TypeRef** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="b588d-1104">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-1104">Documentation (zero or one element)</span></span>
-   <span data-ttu-id="b588d-1105">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-1105">Annotation elements (zero or more elements)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-1106">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-1106">Applicable Attributes</span></span>

<span data-ttu-id="b588d-1107">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **TypeRef** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1107">The following table describes the attributes that can be applied to the **TypeRef** element.</span></span> <span data-ttu-id="b588d-1108">Tenga en cuenta que los atributos **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**y **collation** solo se aplican a **EDMSimpleTypes**.</span><span class="sxs-lookup"><span data-stu-id="b588d-1108">Note that the **DefaultValue**, **MaxLength**, **FixedLength**, **Precision**, **Scale**, **Unicode**, and **Collation** attributes are only applicable to **EDMSimpleTypes**.</span></span>

| <span data-ttu-id="b588d-1109">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-1109">Attribute Name</span></span>                                                     | <span data-ttu-id="b588d-1110">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-1110">Is Required</span></span> | <span data-ttu-id="b588d-1111">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-1111">Value</span></span>                                                                                                                                                                                                                           |
|:-------------------------------------------------------------------|:------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-1112">**Tipo**</span><span class="sxs-lookup"><span data-stu-id="b588d-1112">**Type**</span></span>                                                           | <span data-ttu-id="b588d-1113">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1113">No</span></span>          | <span data-ttu-id="b588d-1114">El nombre del tipo al que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="b588d-1114">The name of the type being referenced.</span></span>                                                                                                                                                                                          |
| <span data-ttu-id="b588d-1115">**Admisión de valores NULL**</span><span class="sxs-lookup"><span data-stu-id="b588d-1115">**Nullable**</span></span>                                                       | <span data-ttu-id="b588d-1116">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1116">No</span></span>          | <span data-ttu-id="b588d-1117">**True** (el valor predeterminado) o **False**, según si la propiedad puede tener un valor nulo.</span><span class="sxs-lookup"><span data-stu-id="b588d-1117">**True** (the default value) or **False** depending on whether the property can have a null value.</span></span> <br/> [!NOTE]                                                                                                                |
| <span data-ttu-id="b588d-1118">> en CSDL v1, una propiedad de tipo complejo debe tener `Nullable="False"` .</span><span class="sxs-lookup"><span data-stu-id="b588d-1118">> In CSDL v1 a complex type property must have `Nullable="False"`.</span></span> |             |                                                                                                                                                                                                                                 |
| <span data-ttu-id="b588d-1119">**DefaultValue**</span><span class="sxs-lookup"><span data-stu-id="b588d-1119">**DefaultValue**</span></span>                                                   | <span data-ttu-id="b588d-1120">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1120">No</span></span>          | <span data-ttu-id="b588d-1121">Valor predeterminado de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-1121">The default value of the property.</span></span>                                                                                                                                                                                              |
| <span data-ttu-id="b588d-1122">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-1122">**MaxLength**</span></span>                                                      | <span data-ttu-id="b588d-1123">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1123">No</span></span>          | <span data-ttu-id="b588d-1124">La longitud máxima del valor de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-1124">The maximum length of the property value.</span></span>                                                                                                                                                                                       |
| <span data-ttu-id="b588d-1125">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-1125">**FixedLength**</span></span>                                                    | <span data-ttu-id="b588d-1126">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1126">No</span></span>          | <span data-ttu-id="b588d-1127">**True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena de longitud fija.</span><span class="sxs-lookup"><span data-stu-id="b588d-1127">**True** or **False** depending on whether the property value will be stored as a fixed length string.</span></span>                                                                                                                          |
| <span data-ttu-id="b588d-1128">**Precisión**</span><span class="sxs-lookup"><span data-stu-id="b588d-1128">**Precision**</span></span>                                                      | <span data-ttu-id="b588d-1129">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1129">No</span></span>          | <span data-ttu-id="b588d-1130">Precisión del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-1130">The precision of the property value.</span></span>                                                                                                                                                                                            |
| <span data-ttu-id="b588d-1131">**Escala**</span><span class="sxs-lookup"><span data-stu-id="b588d-1131">**Scale**</span></span>                                                          | <span data-ttu-id="b588d-1132">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1132">No</span></span>          | <span data-ttu-id="b588d-1133">Escala del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-1133">The scale of the property value.</span></span>                                                                                                                                                                                                |
| <span data-ttu-id="b588d-1134">**SRID**</span><span class="sxs-lookup"><span data-stu-id="b588d-1134">**SRID**</span></span>                                                           | <span data-ttu-id="b588d-1135">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1135">No</span></span>          | <span data-ttu-id="b588d-1136">Identificador de referencia del sistema espacial.</span><span class="sxs-lookup"><span data-stu-id="b588d-1136">Spatial System Reference Identifier.</span></span> <span data-ttu-id="b588d-1137">Solo es válido para las propiedades de los tipos espaciales.</span><span class="sxs-lookup"><span data-stu-id="b588d-1137">Valid only for properties of spatial types.</span></span> <span data-ttu-id="b588d-1138">Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="b588d-1138">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span> |
| <span data-ttu-id="b588d-1139">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="b588d-1139">**Unicode**</span></span>                                                        | <span data-ttu-id="b588d-1140">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1140">No</span></span>          | <span data-ttu-id="b588d-1141">**True** o **false** , dependiendo de si el valor de la propiedad se almacenará como una cadena Unicode.</span><span class="sxs-lookup"><span data-stu-id="b588d-1141">**True** or **False** depending on whether the property value will be stored as a Unicode string.</span></span>                                                                                                                               |
| <span data-ttu-id="b588d-1142">**Intercalación**</span><span class="sxs-lookup"><span data-stu-id="b588d-1142">**Collation**</span></span>                                                      | <span data-ttu-id="b588d-1143">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1143">No</span></span>          | <span data-ttu-id="b588d-1144">Cadena que especifica la secuencia de intercalación que se usará en el origen de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1144">A string that specifies the collating sequence to be used in the data source.</span></span>                                                                                                                                                   |

 

> [!NOTE]
> <span data-ttu-id="b588d-1145">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **CollectionType** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1145">Any number of annotation attributes (custom XML attributes) may be applied to the **CollectionType** element.</span></span> <span data-ttu-id="b588d-1146">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1146">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-1147">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1147">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-1148">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-1148">Example</span></span>

<span data-ttu-id="b588d-1149">En el ejemplo siguiente se muestra una función definida por el modelo que usa el elemento **TypeRef** (como elemento secundario de un elemento **CollectionType** ) para especificar que la función acepta una colección de tipos de entidad **Department** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1149">The following example shows a model-defined function that uses the **TypeRef** element (as a child of a **CollectionType** element) to specify that the function accepts a collection of **Department** entity types.</span></span>

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
 

 

## <a name="using-element-csdl"></a><span data-ttu-id="b588d-1150">Using (Elemento) (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-1150">Using Element (CSDL)</span></span>

<span data-ttu-id="b588d-1151">El elemento **using** del lenguaje de definición de esquemas conceptuales (CSDL) importa el contenido de un modelo conceptual que existe en un espacio de nombres diferente.</span><span class="sxs-lookup"><span data-stu-id="b588d-1151">The **Using** element in conceptual schema definition language (CSDL) imports the contents of a conceptual model that exists in a different namespace.</span></span> <span data-ttu-id="b588d-1152">Al establecer el valor del atributo de **espacio de nombres** , puede hacer referencia a los tipos de entidad, tipos complejos y tipos de asociación que se definen en otro modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-1152">By setting the value of the **Namespace** attribute, you can refer to entity types, complex types, and association types that are defined in another conceptual model.</span></span> <span data-ttu-id="b588d-1153">Más de un elemento **using** puede ser un elemento secundario de un elemento **Schema** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1153">More than one **Using** element can be a child of a **Schema** element.</span></span>

> [!NOTE]
> <span data-ttu-id="b588d-1154">El elemento **using** en CSDL no funciona exactamente igual que una instrucción **using** en un lenguaje de programación.</span><span class="sxs-lookup"><span data-stu-id="b588d-1154">The **Using** element in CSDL does not function exactly like a **using** statement in a programming language.</span></span> <span data-ttu-id="b588d-1155">Al importar un espacio de nombres con una instrucción **using** en un lenguaje de programación, no afecta a los objetos del espacio de nombres original.</span><span class="sxs-lookup"><span data-stu-id="b588d-1155">By importing a namespace with a **using** statement in a programming language, you do not affect objects in the original namespace.</span></span> <span data-ttu-id="b588d-1156">En CSDL, un espacio de nombres importado puede contener un tipo de entidad derivado de un tipo de entidad del espacio de nombres original.</span><span class="sxs-lookup"><span data-stu-id="b588d-1156">In CSDL, an imported namespace can contain an entity type that is derived from an entity type in the original namespace.</span></span> <span data-ttu-id="b588d-1157">Esto puede afectar a los conjuntos de entidades declarados en el espacio de nombres original.</span><span class="sxs-lookup"><span data-stu-id="b588d-1157">This can affect entity sets declared in the original namespace.</span></span>

 

<span data-ttu-id="b588d-1158">El elemento **using** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="b588d-1158">The **Using** element can have the following child elements:</span></span>

-   <span data-ttu-id="b588d-1159">Documentation (cero o un elemento)</span><span class="sxs-lookup"><span data-stu-id="b588d-1159">Documentation (zero or one elements allowed)</span></span>
-   <span data-ttu-id="b588d-1160">Elementos Annotation (cero o más elementos)</span><span class="sxs-lookup"><span data-stu-id="b588d-1160">Annotation elements (zero or more elements allowed)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="b588d-1161">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-1161">Applicable Attributes</span></span>

<span data-ttu-id="b588d-1162">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **using** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1162">The table below describes the attributes can be applied to the **Using** element.</span></span>

| <span data-ttu-id="b588d-1163">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="b588d-1163">Attribute Name</span></span> | <span data-ttu-id="b588d-1164">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="b588d-1164">Is Required</span></span> | <span data-ttu-id="b588d-1165">Value</span><span class="sxs-lookup"><span data-stu-id="b588d-1165">Value</span></span>                                                                                                                                                                              |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="b588d-1166">**Espacio de nombres**</span><span class="sxs-lookup"><span data-stu-id="b588d-1166">**Namespace**</span></span>  | <span data-ttu-id="b588d-1167">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1167">Yes</span></span>         | <span data-ttu-id="b588d-1168">Nombre del espacio de nombres importado.</span><span class="sxs-lookup"><span data-stu-id="b588d-1168">The name of the imported namespace.</span></span>                                                                                                                                                |
| <span data-ttu-id="b588d-1169">**Alias**</span><span class="sxs-lookup"><span data-stu-id="b588d-1169">**Alias**</span></span>      | <span data-ttu-id="b588d-1170">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1170">Yes</span></span>         | <span data-ttu-id="b588d-1171">Un identificador usado en lugar del nombre del espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="b588d-1171">An identifier used in place of the namespace name.</span></span> <span data-ttu-id="b588d-1172">Aunque este atributo es obligatorio, no es necesario usarlo en lugar del nombre del espacio de nombres para calificar los nombres de los objetos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1172">Although this attribute is required, it is not required that it be used in place of the namespace name to qualify object names.</span></span> |

 

> [!NOTE]
> <span data-ttu-id="b588d-1173">Se puede aplicar cualquier número de atributos de anotación (atributos XML personalizados) al elemento **using** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1173">Any number of annotation attributes (custom XML attributes) may be applied to the **Using** element.</span></span> <span data-ttu-id="b588d-1174">Sin embargo, es posible que los atributos personalizados no pertenezcan a ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1174">However, custom attributes may not belong to any XML namespace that is reserved for CSDL.</span></span> <span data-ttu-id="b588d-1175">Dos atributos personalizados cualesquiera no pueden tener nombres completos idénticos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1175">The fully-qualified names for any two custom attributes cannot be the same.</span></span>

 

### <a name="example"></a><span data-ttu-id="b588d-1176">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-1176">Example</span></span>

<span data-ttu-id="b588d-1177">En el ejemplo siguiente se muestra el elemento **using** que se usa para importar un espacio de nombres que se define en otro lugar.</span><span class="sxs-lookup"><span data-stu-id="b588d-1177">The following example demonstrates the **Using** element being used to import a namespace that is defined elsewhere.</span></span> <span data-ttu-id="b588d-1178">Tenga en cuenta que el espacio de nombres para el elemento de **esquema** mostrado es `BooksModel` .</span><span class="sxs-lookup"><span data-stu-id="b588d-1178">Note that the namespace for the **Schema** element shown is `BooksModel`.</span></span> <span data-ttu-id="b588d-1179">La `Address` propiedad del `Publisher` **EntityType** es un tipo complejo que se define en el `ExtendedBooksModel` espacio de nombres (importado con el elemento **using** ).</span><span class="sxs-lookup"><span data-stu-id="b588d-1179">The `Address` property on the `Publisher`**EntityType** is a complex type that is defined in the `ExtendedBooksModel` namespace (imported with the **Using** element).</span></span>

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
 

 

## <a name="annotation-attributes-csdl"></a><span data-ttu-id="b588d-1180">Atributos de anotación (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-1180">Annotation Attributes (CSDL)</span></span>

<span data-ttu-id="b588d-1181">En el lenguaje de definición de esquemas conceptuales (CSDL), los atributos de anotación son atributos XML personalizados del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-1181">Annotation attributes in conceptual schema definition language (CSDL) are custom XML attributes in the conceptual model.</span></span> <span data-ttu-id="b588d-1182">Además de tener una estructura XML válida, los atributos de anotación deben cumplir las condiciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="b588d-1182">In addition to having valid XML structure, the following must be true of annotation attributes:</span></span>

-   <span data-ttu-id="b588d-1183">Los atributos de anotación no deben estar en ningún espacio de nombres XML reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1183">Annotation attributes must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="b588d-1184">Se pueden aplicar varios atributos de anotación a un elemento CSDL determinado.</span><span class="sxs-lookup"><span data-stu-id="b588d-1184">More than one annotation attribute may be applied to a given CSDL element.</span></span>
-   <span data-ttu-id="b588d-1185">Dos atributos de anotación cualesquiera no pueden tener el mismo nombre completo.</span><span class="sxs-lookup"><span data-stu-id="b588d-1185">The fully-qualified names of any two annotation attributes must not be the same.</span></span>

<span data-ttu-id="b588d-1186">Los atributos de anotación se pueden usar para proporcionar metadatos adicionales sobre los elementos en un modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-1186">Annotation attributes can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="b588d-1187">Se puede tener acceso a los metadatos contenidos en los elementos Annotation en tiempo de ejecución mediante el uso de clases en el espacio de nombres System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="b588d-1187">Metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="b588d-1188">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-1188">Example</span></span>

<span data-ttu-id="b588d-1189">En el ejemplo siguiente se muestra un elemento **EntityType** con un atributo Annotation (**CustomAttribute**).</span><span class="sxs-lookup"><span data-stu-id="b588d-1189">The following example shows an **EntityType** element with an annotation attribute (**CustomAttribute**).</span></span> <span data-ttu-id="b588d-1190">El ejemplo también muestra un elemento Annotation aplicado al elemento de tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-1190">The example also shows an annotation element applied to the entity type element.</span></span>

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
 

<span data-ttu-id="b588d-1191">El siguiente código recupera los metadatos del atributo Annotation y los escribe en la consola:</span><span class="sxs-lookup"><span data-stu-id="b588d-1191">The following code retrieves the metadata in the annotation attribute and writes it to the console:</span></span>

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
 

<span data-ttu-id="b588d-1192">El código anterior supone que el archivo `School.csdl` está en el directorio de resultados del proyecto y que se han agregado las siguientes instrucciones `Imports` y `Using` al proyecto:</span><span class="sxs-lookup"><span data-stu-id="b588d-1192">The code above assumes that the `School.csdl` file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="annotation-elements-csdl"></a><span data-ttu-id="b588d-1193">Elementos Annotation (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-1193">Annotation Elements (CSDL)</span></span>

<span data-ttu-id="b588d-1194">Los elementos Annotation en el lenguaje de definición de esquemas conceptuales (CSDL) son los elementos XML personalizados del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-1194">Annotation elements in conceptual schema definition language (CSDL) are custom XML elements in the conceptual model.</span></span> <span data-ttu-id="b588d-1195">Además de tener una estructura XML válida, lo siguiente debe ser verdadero para los elementos Annotation:</span><span class="sxs-lookup"><span data-stu-id="b588d-1195">In addition to having valid XML structure, the following must be true of annotation elements:</span></span>

-   <span data-ttu-id="b588d-1196">Los elementos Annotation no deben estar en un espacio de nombres XML que esté reservado para CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1196">Annotation elements must not be in any XML namespace that is reserved for CSDL.</span></span>
-   <span data-ttu-id="b588d-1197">Más de un elemento Annotation puede ser un elemento secundario de un elemento CSDL determinado.</span><span class="sxs-lookup"><span data-stu-id="b588d-1197">More than one annotation element may be a child of a given CSDL element.</span></span>
-   <span data-ttu-id="b588d-1198">Los nombres completos de dos elementos Annotation cualesquiera no deben ser los mismos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1198">The fully-qualified names of any two annotation elements must not be the same.</span></span>
-   <span data-ttu-id="b588d-1199">Los elementos Annotation deben aparecer después de todos los demás elementos secundarios de un elemento CSDL determinado.</span><span class="sxs-lookup"><span data-stu-id="b588d-1199">Annotation elements must appear after all other child elements of a given CSDL element.</span></span>

<span data-ttu-id="b588d-1200">Los elementos Annotation pueden utilizarse para proporcionar metadatos adicionales sobre los elementos en un modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-1200">Annotation elements can be used to provide extra metadata about the elements in a conceptual model.</span></span> <span data-ttu-id="b588d-1201">A partir de la .NET Framework versión 4, se puede tener acceso a los metadatos contenidos en los elementos Annotation en tiempo de ejecución mediante el uso de clases en el espacio de nombres System. Data. Metadata. Edm.</span><span class="sxs-lookup"><span data-stu-id="b588d-1201">Starting with the .NET Framework version 4, metadata contained in annotation elements can be accessed at runtime by using classes in the System.Data.Metadata.Edm namespace.</span></span>

### <a name="example"></a><span data-ttu-id="b588d-1202">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-1202">Example</span></span>

<span data-ttu-id="b588d-1203">En el ejemplo siguiente se muestra un elemento **EntityType** con un elemento Annotation (**CustomElement**).</span><span class="sxs-lookup"><span data-stu-id="b588d-1203">The following example shows an **EntityType** element with an annotation element (**CustomElement**).</span></span> <span data-ttu-id="b588d-1204">El ejemplo también muestra un atributo de anotación aplicado al elemento de tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b588d-1204">The example also show an annotation attribute applied to the entity type element.</span></span>

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
 

<span data-ttu-id="b588d-1205">El siguiente código recupera los metadatos del elemento de anotación y los escribe en la consola:</span><span class="sxs-lookup"><span data-stu-id="b588d-1205">The following code retrieves the metadata in the annotation element and writes it to the console:</span></span>

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
 

<span data-ttu-id="b588d-1206">El código anterior supone que el archivo School.csdl está en el directorio de resultados del proyecto y que se han agregado las siguientes instrucciones `Imports` y `Using` al proyecto:</span><span class="sxs-lookup"><span data-stu-id="b588d-1206">The code above assumes that the School.csdl file is in the project's output directory and that you have added the following `Imports` and `Using` statements to your project:</span></span>

``` csharp
 using System.Data.Metadata.Edm;
```
 

 

## <a name="conceptual-model-types-csdl"></a><span data-ttu-id="b588d-1207">Tipos de modelos conceptuales (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-1207">Conceptual Model Types (CSDL)</span></span>

<span data-ttu-id="b588d-1208">El lenguaje de definición de esquemas conceptuales (CSDL) admite un conjunto de tipos de datos primitivos abstractos, denominados **EDMSimpleTypes**, que definen propiedades en un modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-1208">Conceptual schema definition language (CSDL) supports a set of abstract primitive data types, called **EDMSimpleTypes**, that define properties in a conceptual model.</span></span> <span data-ttu-id="b588d-1209">Los **EDMSimpleTypes** son proxies para los tipos de datos primitivos que se admiten en el entorno de almacenamiento o de hospedaje.</span><span class="sxs-lookup"><span data-stu-id="b588d-1209">**EDMSimpleTypes** are proxies for primitive data types that are supported in the storage or hosting environment.</span></span>

<span data-ttu-id="b588d-1210">En la tabla siguiente se enumeran los tipos de datos primitivos admitidos por CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1210">The table below lists the primitive data types that are supported by CSDL.</span></span> <span data-ttu-id="b588d-1211">En la tabla también se enumeran las caras que se pueden aplicar a cada **EDMSimpleType**.</span><span class="sxs-lookup"><span data-stu-id="b588d-1211">The table also lists the facets that can be applied to each **EDMSimpleType**.</span></span>

| <span data-ttu-id="b588d-1212">EDMSimpleType</span><span class="sxs-lookup"><span data-stu-id="b588d-1212">EDMSimpleType</span></span>                    | <span data-ttu-id="b588d-1213">Descripción</span><span class="sxs-lookup"><span data-stu-id="b588d-1213">Description</span></span>                                                | <span data-ttu-id="b588d-1214">Facetas aplicables</span><span class="sxs-lookup"><span data-stu-id="b588d-1214">Applicable Facets</span></span>                                                        |
|:---------------------------------|:-----------------------------------------------------------|:-------------------------------------------------------------------------|
| <span data-ttu-id="b588d-1215">**Edm.Binary**</span><span class="sxs-lookup"><span data-stu-id="b588d-1215">**Edm.Binary**</span></span>                   | <span data-ttu-id="b588d-1216">Contiene datos binarios.</span><span class="sxs-lookup"><span data-stu-id="b588d-1216">Contains binary data.</span></span>                                      | <span data-ttu-id="b588d-1217">MaxLength, FixedLength, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1217">MaxLength, FixedLength, Nullable, Default</span></span>                                |
| <span data-ttu-id="b588d-1218">**Edm.Boolean**</span><span class="sxs-lookup"><span data-stu-id="b588d-1218">**Edm.Boolean**</span></span>                  | <span data-ttu-id="b588d-1219">Contiene el valor **true** o **false**.</span><span class="sxs-lookup"><span data-stu-id="b588d-1219">Contains the value **true** or **false**.</span></span>                  | <span data-ttu-id="b588d-1220">Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1220">Nullable, Default</span></span>                                                        |
| <span data-ttu-id="b588d-1221">**Edm.Byte**</span><span class="sxs-lookup"><span data-stu-id="b588d-1221">**Edm.Byte**</span></span>                     | <span data-ttu-id="b588d-1222">Contiene un valor entero de 8 bits sin signo.</span><span class="sxs-lookup"><span data-stu-id="b588d-1222">Contains an unsigned 8-bit integer value.</span></span>                  | <span data-ttu-id="b588d-1223">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1223">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="b588d-1224">**Edm.DateTime**</span><span class="sxs-lookup"><span data-stu-id="b588d-1224">**Edm.DateTime**</span></span>                 | <span data-ttu-id="b588d-1225">Representa una fecha y hora.</span><span class="sxs-lookup"><span data-stu-id="b588d-1225">Represents a date and time.</span></span>                                | <span data-ttu-id="b588d-1226">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1226">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="b588d-1227">**Edm.DateTimeOffset**</span><span class="sxs-lookup"><span data-stu-id="b588d-1227">**Edm.DateTimeOffset**</span></span>           | <span data-ttu-id="b588d-1228">Contiene una fecha y hora como un desplazamiento en minutos con respecto a GMT.</span><span class="sxs-lookup"><span data-stu-id="b588d-1228">Contains a date and time as an offset in minutes from GMT.</span></span> | <span data-ttu-id="b588d-1229">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1229">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="b588d-1230">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="b588d-1230">**Edm.Decimal**</span></span>                  | <span data-ttu-id="b588d-1231">Contiene un valor numérico con una precisión y escala fijas.</span><span class="sxs-lookup"><span data-stu-id="b588d-1231">Contains a numeric value with fixed precision and scale.</span></span>   | <span data-ttu-id="b588d-1232">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1232">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="b588d-1233">**Edm.Double**</span><span class="sxs-lookup"><span data-stu-id="b588d-1233">**Edm.Double**</span></span>                   | <span data-ttu-id="b588d-1234">Contiene un número de punto flotante con una precisión de 15 dígitos</span><span class="sxs-lookup"><span data-stu-id="b588d-1234">Contains a floating point number with 15-digit precision</span></span>   | <span data-ttu-id="b588d-1235">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1235">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="b588d-1236">**EDM. Float**</span><span class="sxs-lookup"><span data-stu-id="b588d-1236">**Edm.Float**</span></span>                    | <span data-ttu-id="b588d-1237">Contiene un número de punto flotante con una precisión de 7 dígitos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1237">Contains a floating point number with 7-digit precision.</span></span>   | <span data-ttu-id="b588d-1238">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1238">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="b588d-1239">**Edm.Guid**</span><span class="sxs-lookup"><span data-stu-id="b588d-1239">**Edm.Guid**</span></span>                     | <span data-ttu-id="b588d-1240">Contiene un identificador único de 16 bytes.</span><span class="sxs-lookup"><span data-stu-id="b588d-1240">Contains a 16-byte unique identifier.</span></span>                      | <span data-ttu-id="b588d-1241">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1241">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="b588d-1242">**Edm.Int16**</span><span class="sxs-lookup"><span data-stu-id="b588d-1242">**Edm.Int16**</span></span>                    | <span data-ttu-id="b588d-1243">Contiene un valor entero de 16 bits con signo.</span><span class="sxs-lookup"><span data-stu-id="b588d-1243">Contains a signed 16-bit integer value.</span></span>                    | <span data-ttu-id="b588d-1244">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1244">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="b588d-1245">**Edm.Int32**</span><span class="sxs-lookup"><span data-stu-id="b588d-1245">**Edm.Int32**</span></span>                    | <span data-ttu-id="b588d-1246">Contiene un valor entero de 32 bits con signo.</span><span class="sxs-lookup"><span data-stu-id="b588d-1246">Contains a signed 32-bit integer value.</span></span>                    | <span data-ttu-id="b588d-1247">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1247">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="b588d-1248">**Edm.Int64**</span><span class="sxs-lookup"><span data-stu-id="b588d-1248">**Edm.Int64**</span></span>                    | <span data-ttu-id="b588d-1249">Contiene un valor entero de 64 bits con signo.</span><span class="sxs-lookup"><span data-stu-id="b588d-1249">Contains a signed 64-bit integer value.</span></span>                    | <span data-ttu-id="b588d-1250">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1250">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="b588d-1251">**Edm.SByte**</span><span class="sxs-lookup"><span data-stu-id="b588d-1251">**Edm.SByte**</span></span>                    | <span data-ttu-id="b588d-1252">Contiene un valor entero de 8 bits con signo.</span><span class="sxs-lookup"><span data-stu-id="b588d-1252">Contains a signed 8-bit integer value.</span></span>                     | <span data-ttu-id="b588d-1253">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1253">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="b588d-1254">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="b588d-1254">**Edm.String**</span></span>                   | <span data-ttu-id="b588d-1255">Contiene datos de caracteres.</span><span class="sxs-lookup"><span data-stu-id="b588d-1255">Contains character data.</span></span>                                   | <span data-ttu-id="b588d-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1256">Unicode, FixedLength, MaxLength, Collation, Precision, Nullable, Default</span></span> |
| <span data-ttu-id="b588d-1257">**Edm.Time**</span><span class="sxs-lookup"><span data-stu-id="b588d-1257">**Edm.Time**</span></span>                     | <span data-ttu-id="b588d-1258">Contiene una hora del día.</span><span class="sxs-lookup"><span data-stu-id="b588d-1258">Contains a time of day.</span></span>                                    | <span data-ttu-id="b588d-1259">Precision, Nullable, Default</span><span class="sxs-lookup"><span data-stu-id="b588d-1259">Precision, Nullable, Default</span></span>                                             |
| <span data-ttu-id="b588d-1260">**EDM. Geography**</span><span class="sxs-lookup"><span data-stu-id="b588d-1260">**Edm.Geography**</span></span>                |                                                            | <span data-ttu-id="b588d-1261">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1261">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1262">**Edm.GeographyPoint**</span><span class="sxs-lookup"><span data-stu-id="b588d-1262">**Edm.GeographyPoint**</span></span>           |                                                            | <span data-ttu-id="b588d-1263">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1263">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1264">**EDM. GeographyLineString**</span><span class="sxs-lookup"><span data-stu-id="b588d-1264">**Edm.GeographyLineString**</span></span>      |                                                            | <span data-ttu-id="b588d-1265">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1265">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1266">**EDM. GeographyPolygon**</span><span class="sxs-lookup"><span data-stu-id="b588d-1266">**Edm.GeographyPolygon**</span></span>         |                                                            | <span data-ttu-id="b588d-1267">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1267">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1268">**EDM. GeographyMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="b588d-1268">**Edm.GeographyMultiPoint**</span></span>      |                                                            | <span data-ttu-id="b588d-1269">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1269">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1270">**EDM. GeographyMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="b588d-1270">**Edm.GeographyMultiLineString**</span></span> |                                                            | <span data-ttu-id="b588d-1271">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1271">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1272">**EDM. GeographyMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="b588d-1272">**Edm.GeographyMultiPolygon**</span></span>    |                                                            | <span data-ttu-id="b588d-1273">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1273">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1274">**EDM. GeographyCollection**</span><span class="sxs-lookup"><span data-stu-id="b588d-1274">**Edm.GeographyCollection**</span></span>      |                                                            | <span data-ttu-id="b588d-1275">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1275">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1276">**EDM. Geometry**</span><span class="sxs-lookup"><span data-stu-id="b588d-1276">**Edm.Geometry**</span></span>                 |                                                            | <span data-ttu-id="b588d-1277">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1277">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1278">**EDM. GeometryPoint**</span><span class="sxs-lookup"><span data-stu-id="b588d-1278">**Edm.GeometryPoint**</span></span>            |                                                            | <span data-ttu-id="b588d-1279">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1279">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1280">**EDM. GeometryLineString**</span><span class="sxs-lookup"><span data-stu-id="b588d-1280">**Edm.GeometryLineString**</span></span>       |                                                            | <span data-ttu-id="b588d-1281">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1281">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1282">**EDM. GeometryPolygon**</span><span class="sxs-lookup"><span data-stu-id="b588d-1282">**Edm.GeometryPolygon**</span></span>          |                                                            | <span data-ttu-id="b588d-1283">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1283">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1284">**EDM. GeometryMultiPoint**</span><span class="sxs-lookup"><span data-stu-id="b588d-1284">**Edm.GeometryMultiPoint**</span></span>       |                                                            | <span data-ttu-id="b588d-1285">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1285">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1286">**EDM. GeometryMultiLineString**</span><span class="sxs-lookup"><span data-stu-id="b588d-1286">**Edm.GeometryMultiLineString**</span></span>  |                                                            | <span data-ttu-id="b588d-1287">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1287">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1288">**EDM. GeometryMultiPolygon**</span><span class="sxs-lookup"><span data-stu-id="b588d-1288">**Edm.GeometryMultiPolygon**</span></span>     |                                                            | <span data-ttu-id="b588d-1289">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1289">Nullable, Default, SRID</span></span>                                                  |
| <span data-ttu-id="b588d-1290">**EDM. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="b588d-1290">**Edm.GeometryCollection**</span></span>       |                                                            | <span data-ttu-id="b588d-1291">Nullable, default, SRID</span><span class="sxs-lookup"><span data-stu-id="b588d-1291">Nullable, Default, SRID</span></span>                                                  |

## <a name="facets-csdl"></a><span data-ttu-id="b588d-1292">Facets (CSDL)</span><span class="sxs-lookup"><span data-stu-id="b588d-1292">Facets (CSDL)</span></span>

<span data-ttu-id="b588d-1293">En el lenguaje de definición de esquemas conceptuales (CSDL), las facetas representan restricciones en las propiedades de tipos de entidad y tipos complejos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1293">Facets in conceptual schema definition language (CSDL) represent constraints on properties of entity types and complex types.</span></span> <span data-ttu-id="b588d-1294">Las facetas aparecen como atributos XML en los elementos CSDL siguientes:</span><span class="sxs-lookup"><span data-stu-id="b588d-1294">Facets appear as XML attributes on the following CSDL elements:</span></span>

-   <span data-ttu-id="b588d-1295">Propiedad</span><span class="sxs-lookup"><span data-stu-id="b588d-1295">Property</span></span>
-   <span data-ttu-id="b588d-1296">TypeRef</span><span class="sxs-lookup"><span data-stu-id="b588d-1296">TypeRef</span></span>
-   <span data-ttu-id="b588d-1297">Parámetro</span><span class="sxs-lookup"><span data-stu-id="b588d-1297">Parameter</span></span>

<span data-ttu-id="b588d-1298">En la tabla siguiente se describen las facetas que se admiten en CSDL.</span><span class="sxs-lookup"><span data-stu-id="b588d-1298">The following table describes the facets that are supported in CSDL.</span></span> <span data-ttu-id="b588d-1299">Todas las facetas son opcionales.</span><span class="sxs-lookup"><span data-stu-id="b588d-1299">All facets are optional.</span></span> <span data-ttu-id="b588d-1300">El Entity Framework usa algunas de las caras que se indican a continuación al generar una base de datos a partir de un modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="b588d-1300">Some facets listed below are used by the Entity Framework when generating a database from a conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="b588d-1301">Para obtener información sobre los tipos de datos de un modelo conceptual, vea tipos de modelos conceptuales (CSDL).</span><span class="sxs-lookup"><span data-stu-id="b588d-1301">For information about data types in a conceptual model, see Conceptual Model Types (CSDL).</span></span>

| <span data-ttu-id="b588d-1302">Faceta</span><span class="sxs-lookup"><span data-stu-id="b588d-1302">Facet</span></span>               | <span data-ttu-id="b588d-1303">Descripción</span><span class="sxs-lookup"><span data-stu-id="b588d-1303">Description</span></span>                                                                                                                                                                                                                                                   | <span data-ttu-id="b588d-1304">Se aplica a</span><span class="sxs-lookup"><span data-stu-id="b588d-1304">Applies to</span></span>                                                                                                                                                                                                                                                                                                                                                                           | <span data-ttu-id="b588d-1305">Se utiliza para la generación de la base de datos</span><span class="sxs-lookup"><span data-stu-id="b588d-1305">Used for the database generation</span></span> | <span data-ttu-id="b588d-1306">La usa el runtime.</span><span class="sxs-lookup"><span data-stu-id="b588d-1306">Used by the runtime</span></span> |
|:--------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------|:--------------------|
| <span data-ttu-id="b588d-1307">**Intercalación**</span><span class="sxs-lookup"><span data-stu-id="b588d-1307">**Collation**</span></span>       | <span data-ttu-id="b588d-1308">Especifica la secuencia de intercalación (o secuencia de orden) que se va a usar cuando se realicen las operaciones de comparación y ordenación sobre los valores de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-1308">Specifies the collating sequence (or sorting sequence) to be used when performing comparison and ordering operations on values of the property.</span></span>                                                                                                               | <span data-ttu-id="b588d-1309">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="b588d-1309">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="b588d-1310">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1310">Yes</span></span>                              | <span data-ttu-id="b588d-1311">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1311">No</span></span>                  |
| <span data-ttu-id="b588d-1312">**ConcurrencyMode**</span><span class="sxs-lookup"><span data-stu-id="b588d-1312">**ConcurrencyMode**</span></span> | <span data-ttu-id="b588d-1313">Indica que el valor de propiedad se debería utilizar para las comprobaciones de la simultaneidad optimista.</span><span class="sxs-lookup"><span data-stu-id="b588d-1313">Indicates that the value of the property should be used for optimistic concurrency checks.</span></span>                                                                                                                                                                    | <span data-ttu-id="b588d-1314">Todas las propiedades de **EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="b588d-1314">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="b588d-1315">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1315">No</span></span>                               | <span data-ttu-id="b588d-1316">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1316">Yes</span></span>                 |
| <span data-ttu-id="b588d-1317">**Valor predeterminado**</span><span class="sxs-lookup"><span data-stu-id="b588d-1317">**Default**</span></span>         | <span data-ttu-id="b588d-1318">Especifica el valor predeterminado de la propiedad si no se proporciona ningún valor al crear las instancias.</span><span class="sxs-lookup"><span data-stu-id="b588d-1318">Specifies the default value of the property if no value is supplied upon instantiation.</span></span>                                                                                                                                                                       | <span data-ttu-id="b588d-1319">Todas las propiedades de **EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="b588d-1319">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="b588d-1320">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1320">Yes</span></span>                              | <span data-ttu-id="b588d-1321">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1321">Yes</span></span>                 |
| <span data-ttu-id="b588d-1322">**FixedLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-1322">**FixedLength**</span></span>     | <span data-ttu-id="b588d-1323">Especifica si la longitud del valor de propiedad puede variar.</span><span class="sxs-lookup"><span data-stu-id="b588d-1323">Specifies whether the length of the property value can vary.</span></span>                                                                                                                                                                                                  | <span data-ttu-id="b588d-1324">**EDM. Binary**, **EDM. String**</span><span class="sxs-lookup"><span data-stu-id="b588d-1324">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="b588d-1325">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1325">Yes</span></span>                              | <span data-ttu-id="b588d-1326">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1326">No</span></span>                  |
| <span data-ttu-id="b588d-1327">**MaxLength**</span><span class="sxs-lookup"><span data-stu-id="b588d-1327">**MaxLength**</span></span>       | <span data-ttu-id="b588d-1328">Especifica la longitud máxima del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-1328">Specifies the maximum length of the property value.</span></span>                                                                                                                                                                                                           | <span data-ttu-id="b588d-1329">**EDM. Binary**, **EDM. String**</span><span class="sxs-lookup"><span data-stu-id="b588d-1329">**Edm.Binary**, **Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="b588d-1330">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1330">Yes</span></span>                              | <span data-ttu-id="b588d-1331">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1331">No</span></span>                  |
| <span data-ttu-id="b588d-1332">**Admisión de valores NULL**</span><span class="sxs-lookup"><span data-stu-id="b588d-1332">**Nullable**</span></span>        | <span data-ttu-id="b588d-1333">Especifica si la propiedad puede tener un valor **null** .</span><span class="sxs-lookup"><span data-stu-id="b588d-1333">Specifies whether the property can have a **null** value.</span></span>                                                                                                                                                                                                     | <span data-ttu-id="b588d-1334">Todas las propiedades de **EDMSimpleType**</span><span class="sxs-lookup"><span data-stu-id="b588d-1334">All **EDMSimpleType** properties</span></span>                                                                                                                                                                                                                                                                                                                                                     | <span data-ttu-id="b588d-1335">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1335">Yes</span></span>                              | <span data-ttu-id="b588d-1336">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1336">Yes</span></span>                 |
| <span data-ttu-id="b588d-1337">**Precisión**</span><span class="sxs-lookup"><span data-stu-id="b588d-1337">**Precision**</span></span>       | <span data-ttu-id="b588d-1338">Para las propiedades de tipo **decimal**, especifica el número de dígitos que puede tener un valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-1338">For properties of type **Decimal**, specifies the number of digits a property value can have.</span></span> <span data-ttu-id="b588d-1339">En el caso de las propiedades de tipo **Time**, **DateTime**y **DateTimeOffset**, especifica el número de dígitos para la parte fraccionaria de los segundos del valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-1339">For properties of type **Time**, **DateTime**, and **DateTimeOffset**, specifies the number of digits for the fractional part of seconds of the property value.</span></span> | <span data-ttu-id="b588d-1340">**EDM. DateTime**, **EDM. DateTimeOffset**, **EDM. decimal**, **EDM. Time**</span><span class="sxs-lookup"><span data-stu-id="b588d-1340">**Edm.DateTime**, **Edm.DateTimeOffset**, **Edm.Decimal**, **Edm.Time**</span></span>                                                                                                                                                                                                                                                                                                              | <span data-ttu-id="b588d-1341">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1341">Yes</span></span>                              | <span data-ttu-id="b588d-1342">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1342">No</span></span>                  |
| <span data-ttu-id="b588d-1343">**Escala**</span><span class="sxs-lookup"><span data-stu-id="b588d-1343">**Scale**</span></span>           | <span data-ttu-id="b588d-1344">Especifica el número de dígitos que puede haber a la derecha del separador decimal para el valor de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b588d-1344">Specifies the number of digits to the right of the decimal point for the property value.</span></span>                                                                                                                                                                      | <span data-ttu-id="b588d-1345">**Edm.Decimal**</span><span class="sxs-lookup"><span data-stu-id="b588d-1345">**Edm.Decimal**</span></span>                                                                                                                                                                                                                                                                                                                                                                      | <span data-ttu-id="b588d-1346">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1346">Yes</span></span>                              | <span data-ttu-id="b588d-1347">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1347">No</span></span>                  |
| <span data-ttu-id="b588d-1348">**SRID**</span><span class="sxs-lookup"><span data-stu-id="b588d-1348">**SRID**</span></span>            | <span data-ttu-id="b588d-1349">Especifica el identificador del sistema espacial de referencia del sistema.</span><span class="sxs-lookup"><span data-stu-id="b588d-1349">Specifies the Spatial System Reference System ID.</span></span> <span data-ttu-id="b588d-1350">Para obtener más información, vea [SRID](https://en.wikipedia.org/wiki/SRID) y [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span><span class="sxs-lookup"><span data-stu-id="b588d-1350">For more information, see [SRID](https://en.wikipedia.org/wiki/SRID) and [SRID (SQL Server)](https://msdn.microsoft.com/library/bb964707.aspx).</span></span>                                                              | <span data-ttu-id="b588d-1351">**EDM. Geography, EDM. GeographyPoint, EDM. GeographyLineString, EDM. GeographyPolygon, EDM. GeographyMultiPoint, EDM. GeographyMultiLineString, EDM. GeographyMultiPolygon, EDM. GeographyCollection, EDM. Geometry, EDM. GeometryPoint, EDM. GeometryLineString, EDM. GeometryPolygon, EDM. GeometryMultiPoint, EDM. GeometryMultiLineString, EDM. GeometryMultiPolygon, EDM. GeometryCollection**</span><span class="sxs-lookup"><span data-stu-id="b588d-1351">**Edm.Geography, Edm.GeographyPoint, Edm.GeographyLineString, Edm.GeographyPolygon, Edm.GeographyMultiPoint, Edm.GeographyMultiLineString, Edm.GeographyMultiPolygon, Edm.GeographyCollection, Edm.Geometry, Edm.GeometryPoint, Edm.GeometryLineString, Edm.GeometryPolygon, Edm.GeometryMultiPoint, Edm.GeometryMultiLineString, Edm.GeometryMultiPolygon, Edm.GeometryCollection**</span></span> | <span data-ttu-id="b588d-1352">No</span><span class="sxs-lookup"><span data-stu-id="b588d-1352">No</span></span>                               | <span data-ttu-id="b588d-1353">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1353">Yes</span></span>                 |
| <span data-ttu-id="b588d-1354">**Unicode**</span><span class="sxs-lookup"><span data-stu-id="b588d-1354">**Unicode**</span></span>         | <span data-ttu-id="b588d-1355">Indica si el valor de propiedad está almacenado como Unicode.</span><span class="sxs-lookup"><span data-stu-id="b588d-1355">Indicates whether the property value is stored as Unicode.</span></span>                                                                                                                                                                                                    | <span data-ttu-id="b588d-1356">**Edm.String**</span><span class="sxs-lookup"><span data-stu-id="b588d-1356">**Edm.String**</span></span>                                                                                                                                                                                                                                                                                                                                                                       | <span data-ttu-id="b588d-1357">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1357">Yes</span></span>                              | <span data-ttu-id="b588d-1358">Sí</span><span class="sxs-lookup"><span data-stu-id="b588d-1358">Yes</span></span>                 |

>[!NOTE]
> <span data-ttu-id="b588d-1359">Al generar una base de datos a partir de un modelo conceptual, el Asistente para generar base de datos reconocerá el valor del atributo **StoreGeneratedPattern** en un elemento **Property** si está en el siguiente espacio de nombres: https://schemas.microsoft.com/ado/2009/02/edm/annotation .</span><span class="sxs-lookup"><span data-stu-id="b588d-1359">When generating a database from a conceptual model, the Generate Database Wizard will recognize the value of the **StoreGeneratedPattern** attribute on a **Property** element if it is in the following namespace: https://schemas.microsoft.com/ado/2009/02/edm/annotation.</span></span> <span data-ttu-id="b588d-1360">Los valores admitidos para el atributo son **Identity** y **Computed**.</span><span class="sxs-lookup"><span data-stu-id="b588d-1360">The supported values for the attribute are **Identity** and **Computed**.</span></span> <span data-ttu-id="b588d-1361">Un valor de **Identity** producirá una columna de base de datos con un valor de identidad que se genera en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1361">A value of **Identity** will produce a database column with an identity value that is generated in the database.</span></span> <span data-ttu-id="b588d-1362">Un valor de **calculado** producirá una columna con un valor que se calcula en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b588d-1362">A value of **Computed** will produce a column with a value that is computed in the database.</span></span>

### <a name="example"></a><span data-ttu-id="b588d-1363">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="b588d-1363">Example</span></span>

<span data-ttu-id="b588d-1364">En el ejemplo siguiente se muestran facetas aplicadas a las propiedades de un tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="b588d-1364">The following example shows facets applied to the properties of an entity type:</span></span>

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
