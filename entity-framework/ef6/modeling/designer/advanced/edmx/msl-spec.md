---
title: 'Especificación de MSL: EF6'
description: Especificación de MSL en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/edmx/msl-spec
ms.openlocfilehash: 36527b9827a7576f73c9ef476462d9392b601984
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070118"
---
# <a name="msl-specification"></a><span data-ttu-id="958d0-103">Especificación MSL</span><span class="sxs-lookup"><span data-stu-id="958d0-103">MSL Specification</span></span>
<span data-ttu-id="958d0-104">El lenguaje de especificación de asignaciones (MSL) es un lenguaje basado en XML que describe la asignación entre el modelo conceptual y el modelo de almacenamiento de una aplicación Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="958d0-104">Mapping specification language (MSL) is an XML-based language that describes the mapping between the conceptual model and storage model of an Entity Framework application.</span></span>

<span data-ttu-id="958d0-105">En una aplicación Entity Framework, la asignación de metadatos se carga desde un archivo. MSL (escrito en MSL) en tiempo de compilación.</span><span class="sxs-lookup"><span data-stu-id="958d0-105">In an Entity Framework application, mapping metadata is loaded from an .msl file (written in MSL) at build time.</span></span> <span data-ttu-id="958d0-106">Entity Framework usa la asignación de metadatos en tiempo de ejecución para traducir las consultas en el modelo conceptual para almacenar comandos específicos de.</span><span class="sxs-lookup"><span data-stu-id="958d0-106">Entity Framework uses mapping metadata at runtime to translate queries against the conceptual model to store-specific commands.</span></span>

<span data-ttu-id="958d0-107">El Entity Framework Designer (EF Designer) almacena la información de asignación en un archivo. edmx en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="958d0-107">The Entity Framework Designer (EF Designer) stores mapping information in an .edmx file at design time.</span></span> <span data-ttu-id="958d0-108">En tiempo de compilación, Entity Designer usa información en un archivo. edmx para crear el archivo. MSL que Entity Framework necesita en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="958d0-108">At build time, the Entity Designer uses information in an .edmx file to create the .msl file that is needed by Entity Framework at runtime</span></span>

<span data-ttu-id="958d0-109">Los nombres de todos los tipos de modelos conceptuales o de almacenamiento a los que se hace referencia en MSL deben estar calificados con sus respectivos nombres de espacios de nombres.</span><span class="sxs-lookup"><span data-stu-id="958d0-109">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="958d0-110">Para obtener información sobre el nombre del espacio de nombres del modelo conceptual, vea [especificación de CSDL](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span><span class="sxs-lookup"><span data-stu-id="958d0-110">For information about the conceptual model namespace name, see [CSDL Specification](xref:ef6/modeling/designer/advanced/edmx/csdl-spec).</span></span> <span data-ttu-id="958d0-111">Para obtener información sobre el nombre del espacio de nombres del modelo de almacenamiento, vea [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span><span class="sxs-lookup"><span data-stu-id="958d0-111">For information about the storage model namespace name, see [SSDL Specification](xref:ef6/modeling/designer/advanced/edmx/ssdl-spec).</span></span>

<span data-ttu-id="958d0-112">Las versiones de MSL se diferencian en los espacios de nombres XML.</span><span class="sxs-lookup"><span data-stu-id="958d0-112">Versions of MSL are differentiated by XML namespaces.</span></span>

| <span data-ttu-id="958d0-113">Versión de MSL</span><span class="sxs-lookup"><span data-stu-id="958d0-113">MSL Version</span></span> | <span data-ttu-id="958d0-114">Espacio de nombres XML</span><span class="sxs-lookup"><span data-stu-id="958d0-114">XML Namespace</span></span>                                        |
|:------------|:-----------------------------------------------------|
| <span data-ttu-id="958d0-115">MSL v1</span><span class="sxs-lookup"><span data-stu-id="958d0-115">MSL v1</span></span>      | <span data-ttu-id="958d0-116">urn: schemas-microsoft-com: Windows: Storage: asignación: CS</span><span class="sxs-lookup"><span data-stu-id="958d0-116">urn:schemas-microsoft-com:windows:storage:mapping:CS</span></span> |
| <span data-ttu-id="958d0-117">MSL V2</span><span class="sxs-lookup"><span data-stu-id="958d0-117">MSL v2</span></span>      | https://schemas.microsoft.com/ado/2008/09/mapping/cs |
| <span data-ttu-id="958d0-118">MSL V3</span><span class="sxs-lookup"><span data-stu-id="958d0-118">MSL v3</span></span>      | https://schemas.microsoft.com/ado/2009/11/mapping/cs  |

## <a name="alias-element-msl"></a><span data-ttu-id="958d0-119">Alias (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-119">Alias Element (MSL)</span></span>

<span data-ttu-id="958d0-120">El elemento **alias** del lenguaje de especificación de asignaciones (MSL) es un elemento secundario del elemento mapping que se utiliza para definir los alias para los espacios de nombres de modelos conceptuales y de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-120">The **Alias** element in mapping specification language (MSL) is a child of the Mapping element that is used to define aliases for conceptual model and storage model namespaces.</span></span> <span data-ttu-id="958d0-121">Los nombres de todos los tipos de modelos conceptuales o de almacenamiento a los que se hace referencia en MSL deben estar calificados con sus respectivos nombres de espacios de nombres.</span><span class="sxs-lookup"><span data-stu-id="958d0-121">Names of all conceptual or storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="958d0-122">Para obtener información sobre el nombre del espacio de nombres del modelo conceptual, vea elemento Schema (CSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-122">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="958d0-123">Para obtener información sobre el nombre del espacio de nombres del modelo de almacenamiento, vea schema (elemento) (SSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-123">For information about the storage model namespace name, see Schema Element (SSDL).</span></span>

<span data-ttu-id="958d0-124">El elemento **alias** no puede tener elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="958d0-124">The **Alias** element cannot have child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-125">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-125">Applicable Attributes</span></span>

<span data-ttu-id="958d0-126">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **alias** .</span><span class="sxs-lookup"><span data-stu-id="958d0-126">The table below describes the attributes that can be applied to the **Alias** element.</span></span>

| <span data-ttu-id="958d0-127">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-127">Attribute Name</span></span> | <span data-ttu-id="958d0-128">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-128">Is Required</span></span> | <span data-ttu-id="958d0-129">Valor</span><span class="sxs-lookup"><span data-stu-id="958d0-129">Value</span></span>                                                                     |
|:---------------|:------------|:--------------------------------------------------------------------------|
| <span data-ttu-id="958d0-130">**Clave**</span><span class="sxs-lookup"><span data-stu-id="958d0-130">**Key**</span></span>        | <span data-ttu-id="958d0-131">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-131">Yes</span></span>         | <span data-ttu-id="958d0-132">El alias para el espacio de nombres especificado por el atributo de **valor** .</span><span class="sxs-lookup"><span data-stu-id="958d0-132">The alias for the namespace that is specified by the **Value** attribute.</span></span> |
| <span data-ttu-id="958d0-133">**Valor**</span><span class="sxs-lookup"><span data-stu-id="958d0-133">**Value**</span></span>      | <span data-ttu-id="958d0-134">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-134">Yes</span></span>         | <span data-ttu-id="958d0-135">Espacio de nombres para el que el valor del elemento **key** es un alias.</span><span class="sxs-lookup"><span data-stu-id="958d0-135">The namespace for which the value of the **Key** element is an alias.</span></span>     |

### <a name="example"></a><span data-ttu-id="958d0-136">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-136">Example</span></span>

<span data-ttu-id="958d0-137">En el ejemplo siguiente se muestra un elemento **alias** que define un alias, `c` , para los tipos que se definen en el modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="958d0-137">The following example shows an **Alias** element that defines an alias, `c`, for types that are defined in the conceptual model.</span></span>

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

## <a name="associationend-element-msl"></a><span data-ttu-id="958d0-138">AssociationEnd (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-138">AssociationEnd Element (MSL)</span></span>

<span data-ttu-id="958d0-139">El elemento **AssociationEnd** del lenguaje de especificación de asignaciones (MSL) se utiliza cuando las funciones de modificación de un tipo de entidad en el modelo conceptual se asignan a los procedimientos almacenados en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-139">The **AssociationEnd** element in mapping specification language (MSL) is used when the modification functions of an entity type in the conceptual model are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="958d0-140">Si un procedimiento almacenado de modificación toma un parámetro cuyo valor se mantiene en una propiedad de asociación, el elemento **AssociationEnd** asigna el valor de la propiedad al parámetro.</span><span class="sxs-lookup"><span data-stu-id="958d0-140">If a modification stored procedure takes a parameter whose value is held in an association property, the **AssociationEnd** element maps the property value to the parameter.</span></span> <span data-ttu-id="958d0-141">Para obtener más información, vea el ejemplo siguiente.</span><span class="sxs-lookup"><span data-stu-id="958d0-141">For more information, see the example below.</span></span>

<span data-ttu-id="958d0-142">Para obtener más información sobre la asignación de funciones de modificación de tipos de entidad a procedimientos almacenados, vea ModificationFunctionMapping (elemento) (MSL) y Walkthrough: asignar una entidad a procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="958d0-142">For more information about mapping modification functions of entity types to stored procedures, see ModificationFunctionMapping Element (MSL) and Walkthrough: Mapping an Entity to Stored Procedures.</span></span>

<span data-ttu-id="958d0-143">El elemento **AssociationEnd** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="958d0-143">The **AssociationEnd** element can have the following child elements:</span></span>

-   <span data-ttu-id="958d0-144">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="958d0-144">ScalarProperty</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-145">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-145">Applicable Attributes</span></span>

<span data-ttu-id="958d0-146">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **AssociationEnd** .</span><span class="sxs-lookup"><span data-stu-id="958d0-146">The following table describes the attributes that are applicable to the **AssociationEnd** element.</span></span>

| <span data-ttu-id="958d0-147">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-147">Attribute Name</span></span>     | <span data-ttu-id="958d0-148">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-148">Is Required</span></span> | <span data-ttu-id="958d0-149">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-149">Value</span></span>                                                                                                                                                                             |
|:-------------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-150">**AssociationSet**</span><span class="sxs-lookup"><span data-stu-id="958d0-150">**AssociationSet**</span></span> | <span data-ttu-id="958d0-151">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-151">Yes</span></span>         | <span data-ttu-id="958d0-152">El nombre de la asociación que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-152">The name of the association that is being mapped.</span></span>                                                                                                                                 |
| <span data-ttu-id="958d0-153">**From**</span><span class="sxs-lookup"><span data-stu-id="958d0-153">**From**</span></span>           | <span data-ttu-id="958d0-154">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-154">Yes</span></span>         | <span data-ttu-id="958d0-155">Valor del atributo **FromRole** de la propiedad de navegación que corresponde a la asociación que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-155">The value of the **FromRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="958d0-156">Para obtener más información, vea elemento NavigationProperty (CSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-156">For more information, see NavigationProperty Element (CSDL).</span></span> |
| <span data-ttu-id="958d0-157">**To**</span><span class="sxs-lookup"><span data-stu-id="958d0-157">**To**</span></span>             | <span data-ttu-id="958d0-158">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-158">Yes</span></span>         | <span data-ttu-id="958d0-159">Valor del atributo **ToRole** de la propiedad de navegación que corresponde a la asociación que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-159">The value of the **ToRole** attribute of the navigation property that corresponds to the association being mapped.</span></span> <span data-ttu-id="958d0-160">Para obtener más información, vea elemento NavigationProperty (CSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-160">For more information, see NavigationProperty Element (CSDL).</span></span>   |

### <a name="example"></a><span data-ttu-id="958d0-161">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-161">Example</span></span>

<span data-ttu-id="958d0-162">Considere el siguiente tipo de entidad del modelo conceptual:</span><span class="sxs-lookup"><span data-stu-id="958d0-162">Consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="958d0-163">Considere también el siguiente procedimiento almacenado:</span><span class="sxs-lookup"><span data-stu-id="958d0-163">Also consider the following stored procedure:</span></span>

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

<span data-ttu-id="958d0-164">Para asignar la función de actualización de la `Course` entidad a este procedimiento almacenado, debe proporcionar un valor al parámetro **departmentId** .</span><span class="sxs-lookup"><span data-stu-id="958d0-164">In order to map the update function of the `Course` entity to this stored procedure, you must supply a value to the **DepartmentID** parameter.</span></span> <span data-ttu-id="958d0-165">El valor para `DepartmentID` no corresponde a una propiedad del tipo de entidad; está contenido en una asociación independiente cuya asignación se muestra aquí:</span><span class="sxs-lookup"><span data-stu-id="958d0-165">The value for `DepartmentID` does not correspond to a property on the entity type; it is contained in an independent association whose mapping is shown here:</span></span>

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

<span data-ttu-id="958d0-166">En el código siguiente se muestra el elemento **AssociationEnd** que se usa para asignar la propiedad **departmentId** de la Asociación de \*\*Departamento de FK \_ Course \_ \*\* al procedimiento almacenado **UpdateCourse** (al que se asigna la función de actualización del tipo de entidad **Course** ):</span><span class="sxs-lookup"><span data-stu-id="958d0-166">The following code shows the **AssociationEnd** element used to map the **DepartmentID** property of the **FK\_Course\_Department** association to the **UpdateCourse** stored procedure (to which the update function of the **Course** entity type is mapped):</span></span>

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

## <a name="associationsetmapping-element-msl"></a><span data-ttu-id="958d0-167">AssociationSetMapping (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-167">AssociationSetMapping Element (MSL)</span></span>

<span data-ttu-id="958d0-168">El elemento **AssociationSetMapping** del lenguaje de especificación de asignaciones (MSL) define la asignación entre una asociación en el modelo conceptual y las columnas de tabla en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-168">The **AssociationSetMapping** element in mapping specification language (MSL) defines the mapping between an association in the conceptual model and table columns in the underlying database.</span></span>

<span data-ttu-id="958d0-169">Las asociaciones del modelo conceptual son tipos cuyas propiedades representan columnas de clave primaria y clave externa de la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-169">Associations in the conceptual model are types whose properties represent primary and foreign key columns in the underlying database.</span></span> <span data-ttu-id="958d0-170">El elemento **AssociationSetMapping** utiliza dos elementos EndProperty para definir las asignaciones entre las propiedades de tipo de asociación y las columnas de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="958d0-170">The **AssociationSetMapping** element uses two EndProperty elements to define the mappings between association type properties and columns in the database.</span></span> <span data-ttu-id="958d0-171">Puede definir condiciones en estas asignaciones con el elemento Condition.</span><span class="sxs-lookup"><span data-stu-id="958d0-171">You can place conditions on these mappings with the Condition element.</span></span> <span data-ttu-id="958d0-172">Asigne las funciones de inserción, actualización y eliminación para las asociaciones a procedimientos almacenados de la base de datos mediante el elemento ModificationFunctionMapping.</span><span class="sxs-lookup"><span data-stu-id="958d0-172">Map the insert, update, and delete functions for associations to stored procedures in the database with the ModificationFunctionMapping element.</span></span> <span data-ttu-id="958d0-173">Defina las asignaciones de solo lectura entre las asociaciones y las columnas de la tabla mediante una Entity SQL cadena en un elemento QueryView.</span><span class="sxs-lookup"><span data-stu-id="958d0-173">Define read-only mappings between associations and table columns by using an Entity SQL string in a QueryView element.</span></span>

> [!NOTE]
> <span data-ttu-id="958d0-174">Si se define una restricción referencial para una asociación en el modelo conceptual, no es necesario asignar la asociación con un elemento **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-174">If a referential constraint is defined for an association in the conceptual model, the association does not need to be mapped with an **AssociationSetMapping** element.</span></span> <span data-ttu-id="958d0-175">Si hay un elemento **AssociationSetMapping** para una asociación que tiene una restricción referencial, se omitirán las asignaciones definidas en el elemento **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-175">If an **AssociationSetMapping** element is present for an association that has a referential constraint, the mappings defined in the **AssociationSetMapping** element will be ignored.</span></span> <span data-ttu-id="958d0-176">Para obtener más información, vea ReferentialConstraint (elemento) (CSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-176">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="958d0-177">El elemento **AssociationSetMapping** puede tener los siguientes elementos secundarios</span><span class="sxs-lookup"><span data-stu-id="958d0-177">The **AssociationSetMapping** element can have the following child elements</span></span>

-   <span data-ttu-id="958d0-178">QueryView (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="958d0-178">QueryView (zero or one)</span></span>
-   <span data-ttu-id="958d0-179">EndProperty (cero o dos)</span><span class="sxs-lookup"><span data-stu-id="958d0-179">EndProperty (zero or two)</span></span>
-   <span data-ttu-id="958d0-180">Condition (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-180">Condition (zero or more)</span></span>
-   <span data-ttu-id="958d0-181">ModificationFunctionMapping (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="958d0-181">ModificationFunctionMapping (zero or one)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-182">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-182">Applicable Attributes</span></span>

<span data-ttu-id="958d0-183">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-183">The following table describes the attributes that can be applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="958d0-184">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-184">Attribute Name</span></span>     | <span data-ttu-id="958d0-185">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-185">Is Required</span></span> | <span data-ttu-id="958d0-186">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-186">Value</span></span>                                                                                       |
|:-------------------|:------------|:--------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-187">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="958d0-187">**Name**</span></span>           | <span data-ttu-id="958d0-188">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-188">Yes</span></span>         | <span data-ttu-id="958d0-189">El nombre del conjunto de asociaciones del modelo conceptual que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-189">The name of the conceptual model association set that is being mapped.</span></span>                      |
| <span data-ttu-id="958d0-190">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="958d0-190">**TypeName**</span></span>       | <span data-ttu-id="958d0-191">No</span><span class="sxs-lookup"><span data-stu-id="958d0-191">No</span></span>          | <span data-ttu-id="958d0-192">El nombre completo, calificado con el espacio de nombres, del tipo de asociación del modelo conceptual que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-192">The namespace-qualified name of the conceptual model association type that is being mapped.</span></span> |
| <span data-ttu-id="958d0-193">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="958d0-193">**StoreEntitySet**</span></span> | <span data-ttu-id="958d0-194">No</span><span class="sxs-lookup"><span data-stu-id="958d0-194">No</span></span>          | <span data-ttu-id="958d0-195">El nombre de la tabla que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-195">The name of the table that is being mapped.</span></span>                                                 |

### <a name="example"></a><span data-ttu-id="958d0-196">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-196">Example</span></span>

<span data-ttu-id="958d0-197">En el ejemplo siguiente se muestra un elemento **AssociationSetMapping** en el que el conjunto de asociaciones del **Departamento del \_ curso \_ de FK** en el modelo conceptual se asigna a la tabla **Course** en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="958d0-197">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association set in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="958d0-198">Las asignaciones entre las propiedades de tipo de asociación y las columnas de tabla se especifican en los elementos secundarios **EndProperty** .</span><span class="sxs-lookup"><span data-stu-id="958d0-198">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

## <a name="complexproperty-element-msl"></a><span data-ttu-id="958d0-199">ComplexProperty (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-199">ComplexProperty Element (MSL)</span></span>

<span data-ttu-id="958d0-200">Un elemento **ComplexProperty** en el lenguaje de especificación de asignaciones (MSL) define la asignación entre una propiedad de tipo complejo en un tipo de entidad del modelo conceptual y las columnas de la tabla en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-200">A **ComplexProperty** element in mapping specification language (MSL) defines the mapping between a complex type property on a conceptual model entity type and table columns in the underlying database.</span></span> <span data-ttu-id="958d0-201">Las asignaciones entre columnas y propiedades se especifican en elementos secundarios ScalarProperty.</span><span class="sxs-lookup"><span data-stu-id="958d0-201">The property-column mappings are specified in child ScalarProperty elements.</span></span>

<span data-ttu-id="958d0-202">El elemento de propiedad **complexType** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="958d0-202">The **ComplexType** property element can have the following child elements:</span></span>

-   <span data-ttu-id="958d0-203">ScalarProperty (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-203">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="958d0-204">**ComplexProperty** (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-204">**ComplexProperty** (zero or more)</span></span>
-   <span data-ttu-id="958d0-205">ComplextTypeMapping (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-205">ComplextTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="958d0-206">Condition (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-206">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-207">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-207">Applicable Attributes</span></span>

<span data-ttu-id="958d0-208">En la tabla siguiente se describen los atributos aplicables al elemento **ComplexProperty** :</span><span class="sxs-lookup"><span data-stu-id="958d0-208">The following table describes the attributes that are applicable to the **ComplexProperty** element:</span></span>

| <span data-ttu-id="958d0-209">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-209">Attribute Name</span></span> | <span data-ttu-id="958d0-210">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-210">Is Required</span></span> | <span data-ttu-id="958d0-211">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-211">Value</span></span>                                                                                            |
|:---------------|:------------|:-------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-212">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="958d0-212">**Name**</span></span>       | <span data-ttu-id="958d0-213">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-213">Yes</span></span>         | <span data-ttu-id="958d0-214">El nombre de la propiedad compleja de un tipo de entidad del modelo conceptual que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-214">The name of the complex property of an entity type in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="958d0-215">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="958d0-215">**TypeName**</span></span>   | <span data-ttu-id="958d0-216">No</span><span class="sxs-lookup"><span data-stu-id="958d0-216">No</span></span>          | <span data-ttu-id="958d0-217">El nombre completo, calificado con el espacio de nombres, del tipo de propiedad del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="958d0-217">The namespace-qualified name of the conceptual model property type.</span></span>                              |

### <a name="example"></a><span data-ttu-id="958d0-218">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-218">Example</span></span>

<span data-ttu-id="958d0-219">El siguiente ejemplo se basa en el modelo School.</span><span class="sxs-lookup"><span data-stu-id="958d0-219">The following example is based on the School model.</span></span> <span data-ttu-id="958d0-220">El siguiente tipo complejo se ha agregado al modelo conceptual:</span><span class="sxs-lookup"><span data-stu-id="958d0-220">The following complex type has been added to the conceptual model:</span></span>

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

<span data-ttu-id="958d0-221">Las propiedades **LastName** y **FirstName** del tipo de entidad **Person** se han reemplazado por una propiedad compleja, **Name**:</span><span class="sxs-lookup"><span data-stu-id="958d0-221">The **LastName** and **FirstName** properties of the **Person** entity type have been replaced with one complex property, **Name**:</span></span>

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

<span data-ttu-id="958d0-222">En el siguiente MSL se muestra el elemento **ComplexProperty** que se usa para asignar la propiedad **Name** a las columnas de la base de datos subyacente:</span><span class="sxs-lookup"><span data-stu-id="958d0-222">The following MSL shows the **ComplexProperty** element used to map the **Name** property to columns in the underlying database:</span></span>

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

## <a name="complextypemapping-element-msl"></a><span data-ttu-id="958d0-223">ComplexTypeMapping (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-223">ComplexTypeMapping Element (MSL)</span></span>

<span data-ttu-id="958d0-224">El elemento **ComplexTypeMapping** del lenguaje de especificación de asignaciones (MSL) es un elemento secundario del elemento ResultMapping y define la asignación entre una importación de función en el modelo conceptual y un procedimiento almacenado en la base de datos subyacente cuando se cumplen las condiciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="958d0-224">The **ComplexTypeMapping** element in mapping specification language (MSL) is a child of the ResultMapping element and defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="958d0-225">La importación de función devuelve un tipo complejo conceptual.</span><span class="sxs-lookup"><span data-stu-id="958d0-225">The function import returns a conceptual complex type.</span></span>
-   <span data-ttu-id="958d0-226">Los nombres de las columnas devueltas por el procedimiento almacenado no coinciden exactamente con los nombres de las propiedades en el tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="958d0-226">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the complex type.</span></span>

<span data-ttu-id="958d0-227">De forma predeterminada, la asignación entre las columnas devueltas por un procedimiento almacenado y un tipo complejo se basa en los nombres de las propiedades y de las columnas.</span><span class="sxs-lookup"><span data-stu-id="958d0-227">By default, the mapping between the columns returned by a stored procedure and a complex type is based on column and property names.</span></span> <span data-ttu-id="958d0-228">Si los nombres de columna no coinciden exactamente con los nombres de propiedad, debe utilizar el elemento **ComplexTypeMapping** para definir la asignación.</span><span class="sxs-lookup"><span data-stu-id="958d0-228">If column names do not exactly match property names, you must use the **ComplexTypeMapping** element to define the mapping.</span></span> <span data-ttu-id="958d0-229">Para obtener un ejemplo de la asignación predeterminada, vea FunctionImportMapping (elemento) (MSL).</span><span class="sxs-lookup"><span data-stu-id="958d0-229">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="958d0-230">El elemento **ComplexTypeMapping** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="958d0-230">The **ComplexTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="958d0-231">ScalarProperty (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-231">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-232">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-232">Applicable Attributes</span></span>

<span data-ttu-id="958d0-233">En la tabla siguiente se describen los atributos aplicables al elemento **ComplexTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-233">The following table describes the attributes that are applicable to the **ComplexTypeMapping** element.</span></span>

| <span data-ttu-id="958d0-234">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-234">Attribute Name</span></span> | <span data-ttu-id="958d0-235">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-235">Is Required</span></span> | <span data-ttu-id="958d0-236">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-236">Value</span></span>                                                                  |
|:---------------|:------------|:-----------------------------------------------------------------------|
| <span data-ttu-id="958d0-237">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="958d0-237">**TypeName**</span></span>   | <span data-ttu-id="958d0-238">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-238">Yes</span></span>         | <span data-ttu-id="958d0-239">El nombre completo, incluido el espacio de nombres, del tipo complejo que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-239">The namespace-qualified name of the complex type that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="958d0-240">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-240">Example</span></span>

<span data-ttu-id="958d0-241">Observe el siguiente procedimiento almacenado:</span><span class="sxs-lookup"><span data-stu-id="958d0-241">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="958d0-242">Considere también el siguiente tipo complejo del modelo conceptual:</span><span class="sxs-lookup"><span data-stu-id="958d0-242">Also consider the following conceptual model complex type:</span></span>

``` xml
 <ComplexType Name="GradeInfo">
   <Property Type="Int32" Name="EnrollmentID" Nullable="false" />
   <Property Type="Decimal" Name="Grade" Nullable="true"
             Precision="3" Scale="2" />
   <Property Type="Int32" Name="CourseID" Nullable="false" />
   <Property Type="Int32" Name="StudentID" Nullable="false" />
 </ComplexType>
```

<span data-ttu-id="958d0-243">Para crear una importación de función que devuelva instancias del tipo complejo anterior, la asignación entre las columnas devueltas por el procedimiento almacenado y el tipo de entidad debe definirse en un elemento **ComplexTypeMapping** :</span><span class="sxs-lookup"><span data-stu-id="958d0-243">In order to create a function import that returns instances of the previous complex type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ComplexTypeMapping** element:</span></span>

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

## <a name="condition-element-msl"></a><span data-ttu-id="958d0-244">Condition (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-244">Condition Element (MSL)</span></span>

<span data-ttu-id="958d0-245">El elemento **Condition** del lenguaje de especificación de asignaciones (MSL) coloca condiciones en las asignaciones entre el modelo conceptual y la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-245">The **Condition** element in mapping specification language (MSL) places conditions on mappings between the conceptual model and the underlying database.</span></span> <span data-ttu-id="958d0-246">La asignación que se define dentro de un nodo XML es válida si se cumplen todas las condiciones, como se especifica en los elementos de **condición** secundarios.</span><span class="sxs-lookup"><span data-stu-id="958d0-246">The mapping that is defined within an XML node is valid if all conditions, as specified in child **Condition** elements, are met.</span></span> <span data-ttu-id="958d0-247">De lo contrario, la asignación no es válida.</span><span class="sxs-lookup"><span data-stu-id="958d0-247">Otherwise, the mapping is not valid.</span></span> <span data-ttu-id="958d0-248">Por ejemplo, si un elemento MappingFragment contiene uno o varios elementos secundarios **Condition** , la asignación definida dentro del nodo **MappingFragment** solo será válida si se cumplen todas las condiciones de los elementos de **condición** secundarios.</span><span class="sxs-lookup"><span data-stu-id="958d0-248">For example, if a MappingFragment element contains one or more **Condition** child elements, the mapping defined within the **MappingFragment** node will only be valid if all the conditions of the child **Condition** elements are met.</span></span>

<span data-ttu-id="958d0-249">Cada condición se puede aplicar a un **nombre** (el nombre de una propiedad de entidad del modelo conceptual, especificado por el atributo de **nombre** ) o un **columnName** (el nombre de una columna de la base de datos, especificado por el atributo **columnName** ).</span><span class="sxs-lookup"><span data-stu-id="958d0-249">Each condition can apply to either a **Name** (the name of a conceptual model entity property, specified by the **Name** attribute), or a **ColumnName** (the name of a column in the database, specified by the **ColumnName** attribute).</span></span> <span data-ttu-id="958d0-250">Cuando se establece el atributo **Name** , la condición se comprueba con respecto a un valor de propiedad de entidad.</span><span class="sxs-lookup"><span data-stu-id="958d0-250">When the **Name** attribute is set, the condition is checked against an entity property value.</span></span> <span data-ttu-id="958d0-251">Cuando se establece el atributo **columnName** , la condición se comprueba con respecto a un valor de columna.</span><span class="sxs-lookup"><span data-stu-id="958d0-251">When the **ColumnName** attribute is set, the condition is checked against a column value.</span></span> <span data-ttu-id="958d0-252">Solo se puede especificar uno de los atributos **Name** o **columnName** en un elemento **Condition** .</span><span class="sxs-lookup"><span data-stu-id="958d0-252">Only one of the **Name** or **ColumnName** attribute can be specified in a **Condition** element.</span></span>

> [!NOTE]
> <span data-ttu-id="958d0-253">Cuando el elemento **Condition** se usa dentro de un elemento FunctionImportMapping, solo el atributo **Name** no es aplicable.</span><span class="sxs-lookup"><span data-stu-id="958d0-253">When the **Condition** element is used within a FunctionImportMapping element, only the **Name** attribute is not applicable.</span></span>

<span data-ttu-id="958d0-254">El elemento **Condition** puede ser un elemento secundario de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="958d0-254">The **Condition** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="958d0-255">AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="958d0-255">AssociationSetMapping</span></span>
-   <span data-ttu-id="958d0-256">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="958d0-256">ComplexProperty</span></span>
-   <span data-ttu-id="958d0-257">EntitySetMapping</span><span class="sxs-lookup"><span data-stu-id="958d0-257">EntitySetMapping</span></span>
-   <span data-ttu-id="958d0-258">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="958d0-258">MappingFragment</span></span>
-   <span data-ttu-id="958d0-259">EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="958d0-259">EntityTypeMapping</span></span>

<span data-ttu-id="958d0-260">El elemento **Condition** no puede tener elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="958d0-260">The **Condition** element can have no child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-261">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-261">Applicable Attributes</span></span>

<span data-ttu-id="958d0-262">En la tabla siguiente se describen los atributos aplicables al elemento **Condition** :</span><span class="sxs-lookup"><span data-stu-id="958d0-262">The following table describes the attributes that are applicable to the **Condition** element:</span></span>

| <span data-ttu-id="958d0-263">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-263">Attribute Name</span></span> | <span data-ttu-id="958d0-264">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-264">Is Required</span></span> | <span data-ttu-id="958d0-265">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-265">Value</span></span>                                                                                                                                                                                                                                                                                         |
|:---------------|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-266">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="958d0-266">**ColumnName**</span></span> | <span data-ttu-id="958d0-267">No</span><span class="sxs-lookup"><span data-stu-id="958d0-267">No</span></span>          | <span data-ttu-id="958d0-268">El nombre de la columna de la tabla cuyo valor se utiliza para evaluar la condición.</span><span class="sxs-lookup"><span data-stu-id="958d0-268">The name of the table column whose value is used to evaluate the condition.</span></span>                                                                                                                                                                                                                   |
| <span data-ttu-id="958d0-269">**IsNull**</span><span class="sxs-lookup"><span data-stu-id="958d0-269">**IsNull**</span></span>     | <span data-ttu-id="958d0-270">No</span><span class="sxs-lookup"><span data-stu-id="958d0-270">No</span></span>          | <span data-ttu-id="958d0-271">**True** o **False**.</span><span class="sxs-lookup"><span data-stu-id="958d0-271">**True** or **False**.</span></span> <span data-ttu-id="958d0-272">Si el valor es **true** y el valor de la columna es **null**, o si el valor es **false** y el valor de la columna no es **null**, la condición es true.</span><span class="sxs-lookup"><span data-stu-id="958d0-272">If the value is **True** and the column value is **null**, or if the value is **False** and the column value is not **null**, the condition is true.</span></span> <span data-ttu-id="958d0-273">De lo contrario, la condición es falsa.</span><span class="sxs-lookup"><span data-stu-id="958d0-273">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="958d0-274">Los atributos **IsNull** y **Value** no se pueden usar al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="958d0-274">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span> |
| <span data-ttu-id="958d0-275">**Valor**</span><span class="sxs-lookup"><span data-stu-id="958d0-275">**Value**</span></span>      | <span data-ttu-id="958d0-276">No</span><span class="sxs-lookup"><span data-stu-id="958d0-276">No</span></span>          | <span data-ttu-id="958d0-277">El valor con el que se compara el valor de la columna.</span><span class="sxs-lookup"><span data-stu-id="958d0-277">The value with which the column value is compared.</span></span> <span data-ttu-id="958d0-278">Si los valores son los mismos, la condición es verdadera.</span><span class="sxs-lookup"><span data-stu-id="958d0-278">If the values are the same, the condition is true.</span></span> <span data-ttu-id="958d0-279">De lo contrario, la condición es falsa.</span><span class="sxs-lookup"><span data-stu-id="958d0-279">Otherwise, the condition is false.</span></span> <br/> <span data-ttu-id="958d0-280">Los atributos **IsNull** y **Value** no se pueden usar al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="958d0-280">The **IsNull** and **Value** attributes cannot be used at the same time.</span></span>                                                                       |
| <span data-ttu-id="958d0-281">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="958d0-281">**Name**</span></span>       | <span data-ttu-id="958d0-282">No</span><span class="sxs-lookup"><span data-stu-id="958d0-282">No</span></span>          | <span data-ttu-id="958d0-283">El nombre de la propiedad de entidad del modelo conceptual cuyo valor se utiliza para evaluar la condición.</span><span class="sxs-lookup"><span data-stu-id="958d0-283">The name of the conceptual model entity property whose value is used to evaluate the condition.</span></span> <br/> <span data-ttu-id="958d0-284">Este atributo no es aplicable si el elemento **Condition** se usa dentro de un elemento FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="958d0-284">This attribute is not applicable if the **Condition** element is used within a FunctionImportMapping element.</span></span>                                                                           |

### <a name="example"></a><span data-ttu-id="958d0-285">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-285">Example</span></span>

<span data-ttu-id="958d0-286">En el ejemplo siguiente se muestran los elementos **Condition** como elementos secundarios de los elementos **MappingFragment** .</span><span class="sxs-lookup"><span data-stu-id="958d0-286">The following example shows **Condition** elements as children of **MappingFragment** elements.</span></span> <span data-ttu-id="958d0-287">Cuando **HireDate** no es NULL y **EnrollmentDate** es null, los datos se asignan entre el tipo **SchoolModel. instructor** y las columnas **PersonID** e **HireDate** de la tabla **Person** .</span><span class="sxs-lookup"><span data-stu-id="958d0-287">When **HireDate** is not null and **EnrollmentDate** is null, data is mapped between the **SchoolModel.Instructor** type and the **PersonID** and **HireDate** columns of the **Person** table.</span></span> <span data-ttu-id="958d0-288">Cuando **EnrollmentDate** no es NULL y **HireDate** es null, los datos se asignan entre el tipo **SchoolModel. Student** y las columnas **PersonID** e **Enrollment** de la tabla **Person** .</span><span class="sxs-lookup"><span data-stu-id="958d0-288">When **EnrollmentDate** is not null and **HireDate** is null, data is mapped between the **SchoolModel.Student** type and the **PersonID** and **Enrollment** columns of the **Person** table.</span></span>

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

## <a name="deletefunction-element-msl"></a><span data-ttu-id="958d0-289">DeleteFunction (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-289">DeleteFunction Element (MSL)</span></span>

<span data-ttu-id="958d0-290">El elemento **InsertFunction** del lenguaje de especificación de asignaciones (MSL) asigna la función de eliminación de un tipo de entidad o asociación del modelo conceptual a un procedimiento almacenado en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-290">The **DeleteFunction** element in mapping specification language (MSL) maps the delete function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="958d0-291">Los procedimientos almacenados a los que están asignados las funciones de modificación se deben declarar en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-291">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="958d0-292">Para obtener más información, vea elemento function (SSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-292">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="958d0-293">Si no asigna las tres operaciones de inserción, actualización o eliminación de un tipo de entidad a los procedimientos almacenados, se producirá un error en las operaciones no asignadas si se ejecutan en tiempo de ejecución y se genera un UpdateException.</span><span class="sxs-lookup"><span data-stu-id="958d0-293">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

### <a name="deletefunction-applied-to-entitytypemapping"></a><span data-ttu-id="958d0-294">DeleteFunction aplicado a EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="958d0-294">DeleteFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="958d0-295">Cuando se aplica al elemento EntityTypeMapping, el elemento **DeleteFunction** asigna la función de eliminación de un tipo de entidad del modelo conceptual a un procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="958d0-295">When applied to the EntityTypeMapping element, the **DeleteFunction** element maps the delete function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="958d0-296">El elemento **DeleteFunction** puede tener los elementos secundarios siguientes cuando se aplica a un elemento **EntityTypeMapping** :</span><span class="sxs-lookup"><span data-stu-id="958d0-296">The **DeleteFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="958d0-297">AssociationEnd (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-297">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="958d0-298">ComplexProperty (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-298">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="958d0-299">ScalarProperty (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-299">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="958d0-300">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-300">Applicable Attributes</span></span>

<span data-ttu-id="958d0-301">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **DeleteFunction** cuando se aplica a un elemento **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-301">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="958d0-302">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-302">Attribute Name</span></span>            | <span data-ttu-id="958d0-303">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-303">Is Required</span></span> | <span data-ttu-id="958d0-304">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-304">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-305">**Nombrefunción**</span><span class="sxs-lookup"><span data-stu-id="958d0-305">**FunctionName**</span></span>          | <span data-ttu-id="958d0-306">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-306">Yes</span></span>         | <span data-ttu-id="958d0-307">El nombre completo, calificado con el espacio de nombres, del procedimiento almacenado al que la función de eliminación está asignada.</span><span class="sxs-lookup"><span data-stu-id="958d0-307">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="958d0-308">El procedimiento almacenado se debe declarar en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-308">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="958d0-309">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="958d0-309">**RowsAffectedParameter**</span></span> | <span data-ttu-id="958d0-310">No</span><span class="sxs-lookup"><span data-stu-id="958d0-310">No</span></span>          | <span data-ttu-id="958d0-311">El nombre del parámetro de salida que devuelve el número de filas afectadas.</span><span class="sxs-lookup"><span data-stu-id="958d0-311">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="958d0-312">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-312">Example</span></span>

<span data-ttu-id="958d0-313">El ejemplo siguiente se basa en el modelo School y muestra el elemento **DeleteFunction** que asigna la función Delete del tipo de entidad **Person** al procedimiento almacenado **DeletePerson** .</span><span class="sxs-lookup"><span data-stu-id="958d0-313">The following example is based on the School model and shows the **DeleteFunction** element mapping the delete function of the **Person** entity type to the **DeletePerson** stored procedure.</span></span> <span data-ttu-id="958d0-314">El procedimiento almacenado **DeletePerson** se declara en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-314">The **DeletePerson** stored procedure is declared in the storage model.</span></span>

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

### <a name="deletefunction-applied-to-associationsetmapping"></a><span data-ttu-id="958d0-315">DeleteFunction aplicado a AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="958d0-315">DeleteFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="958d0-316">Cuando se aplica al elemento AssociationSetMapping, el elemento **DeleteFunction** asigna la función de eliminación de una asociación del modelo conceptual a un procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="958d0-316">When applied to the AssociationSetMapping element, the **DeleteFunction** element maps the delete function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="958d0-317">El elemento **DeleteFunction** puede tener los elementos secundarios siguientes cuando se aplica al elemento **AssociationSetMapping** :</span><span class="sxs-lookup"><span data-stu-id="958d0-317">The **DeleteFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="958d0-318">EndProperty</span><span class="sxs-lookup"><span data-stu-id="958d0-318">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="958d0-319">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-319">Applicable Attributes</span></span>

<span data-ttu-id="958d0-320">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **DeleteFunction** cuando se aplica al elemento **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-320">The following table describes the attributes that can be applied to the **DeleteFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="958d0-321">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-321">Attribute Name</span></span>            | <span data-ttu-id="958d0-322">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-322">Is Required</span></span> | <span data-ttu-id="958d0-323">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-323">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-324">**Nombrefunción**</span><span class="sxs-lookup"><span data-stu-id="958d0-324">**FunctionName**</span></span>          | <span data-ttu-id="958d0-325">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-325">Yes</span></span>         | <span data-ttu-id="958d0-326">El nombre completo, calificado con el espacio de nombres, del procedimiento almacenado al que la función de eliminación está asignada.</span><span class="sxs-lookup"><span data-stu-id="958d0-326">The namespace-qualified name of the stored procedure to which the delete function is mapped.</span></span> <span data-ttu-id="958d0-327">El procedimiento almacenado se debe declarar en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-327">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="958d0-328">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="958d0-328">**RowsAffectedParameter**</span></span> | <span data-ttu-id="958d0-329">No</span><span class="sxs-lookup"><span data-stu-id="958d0-329">No</span></span>          | <span data-ttu-id="958d0-330">El nombre del parámetro de salida que devuelve el número de filas afectadas.</span><span class="sxs-lookup"><span data-stu-id="958d0-330">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="958d0-331">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-331">Example</span></span>

<span data-ttu-id="958d0-332">El ejemplo siguiente se basa en el modelo School y muestra el elemento **DeleteFunction** que se usa para asignar la función de eliminación de la Asociación **CourseInstructor** al procedimiento almacenado **DeleteCourseInstructor** .</span><span class="sxs-lookup"><span data-stu-id="958d0-332">The following example is based on the School model and shows the **DeleteFunction** element used to map delete function of the **CourseInstructor** association to the **DeleteCourseInstructor** stored procedure.</span></span> <span data-ttu-id="958d0-333">El procedimiento almacenado **DeleteCourseInstructor** se declara en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-333">The **DeleteCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="endproperty-element-msl"></a><span data-ttu-id="958d0-334">EndProperty (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-334">EndProperty Element (MSL)</span></span>

<span data-ttu-id="958d0-335">El elemento **EndProperty** del lenguaje de especificación de asignaciones (MSL) define la asignación entre un extremo o una función de modificación de una asociación del modelo conceptual y la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-335">The **EndProperty** element in mapping specification language (MSL) defines the mapping between an end or a modification function of a conceptual model association and the underlying database.</span></span> <span data-ttu-id="958d0-336">La asignación entre columnas y propiedades se especifica en un elemento secundario ScalarProperty.</span><span class="sxs-lookup"><span data-stu-id="958d0-336">The property-column mapping is specified in a child ScalarProperty element.</span></span>

<span data-ttu-id="958d0-337">Cuando se utiliza un elemento **EndProperty** para definir la asignación para el final de una asociación del modelo conceptual, es un elemento secundario de un elemento AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="958d0-337">When an **EndProperty** element is used to define the mapping for the end of a conceptual model association, it is a child of an AssociationSetMapping element.</span></span> <span data-ttu-id="958d0-338">Cuando el elemento **EndProperty** se utiliza para definir la asignación para una función de modificación de una asociación del modelo conceptual, es un elemento secundario de un elemento InsertFunction o un elemento InsertFunction.</span><span class="sxs-lookup"><span data-stu-id="958d0-338">When the **EndProperty** element is used to define the mapping for a modification function of a conceptual model association, it is a child of an InsertFunction element or DeleteFunction element.</span></span>

<span data-ttu-id="958d0-339">El elemento **EndProperty** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="958d0-339">The **EndProperty** element can have the following child elements:</span></span>

-   <span data-ttu-id="958d0-340">ScalarProperty (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-340">ScalarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-341">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-341">Applicable Attributes</span></span>

<span data-ttu-id="958d0-342">En la tabla siguiente se describen los atributos aplicables al elemento **EndProperty** :</span><span class="sxs-lookup"><span data-stu-id="958d0-342">The following table describes the attributes that are applicable to the **EndProperty** element:</span></span>

| <span data-ttu-id="958d0-343">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-343">Attribute Name</span></span> | <span data-ttu-id="958d0-344">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-344">Is Required</span></span> | <span data-ttu-id="958d0-345">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-345">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="958d0-346">Nombre</span><span class="sxs-lookup"><span data-stu-id="958d0-346">Name</span></span>           | <span data-ttu-id="958d0-347">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-347">Yes</span></span>         | <span data-ttu-id="958d0-348">El nombre del extremo de la asociación que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-348">The name of the association end that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="958d0-349">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-349">Example</span></span>

<span data-ttu-id="958d0-350">En el ejemplo siguiente se muestra un elemento **AssociationSetMapping** en el que la Asociación de **Departamento de FK \_ \_ Course** en el modelo conceptual se asigna a la tabla **Course** de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="958d0-350">The following example shows an **AssociationSetMapping** element in which the **FK\_Course\_Department** association in the conceptual model is mapped to the **Course** table in the database.</span></span> <span data-ttu-id="958d0-351">Las asignaciones entre las propiedades de tipo de asociación y las columnas de tabla se especifican en los elementos secundarios **EndProperty** .</span><span class="sxs-lookup"><span data-stu-id="958d0-351">Mappings between association type properties and table columns are specified in child **EndProperty** elements.</span></span>

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

### <a name="example"></a><span data-ttu-id="958d0-352">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-352">Example</span></span>

<span data-ttu-id="958d0-353">En el ejemplo siguiente se muestra el elemento **EndProperty** que asigna las funciones de inserción y eliminación de una asociación (**CourseInstructor**) a los procedimientos almacenados en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-353">The following example shows the **EndProperty** element mapping the insert and delete functions of an association (**CourseInstructor**) to stored procedures in the underlying database.</span></span> <span data-ttu-id="958d0-354">Las funciones asignadas se declaran en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-354">The functions that are mapped to are declared in the storage model.</span></span>

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

## <a name="entitycontainermapping-element-msl"></a><span data-ttu-id="958d0-355">EntityContainerMapping (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-355">EntityContainerMapping Element (MSL)</span></span>

<span data-ttu-id="958d0-356">El elemento **EntityContainerMapping** del lenguaje de especificación de asignaciones (MSL) asigna el contenedor de entidades del modelo conceptual al contenedor de entidades en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-356">The **EntityContainerMapping** element in mapping specification language (MSL) maps the entity container in the conceptual model to the entity container in the storage model.</span></span> <span data-ttu-id="958d0-357">El elemento **EntityContainerMapping** es un elemento secundario del elemento Mapping.</span><span class="sxs-lookup"><span data-stu-id="958d0-357">The **EntityContainerMapping** element is a child of the Mapping element.</span></span>

<span data-ttu-id="958d0-358">El elemento **EntityContainerMapping** puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="958d0-358">The **EntityContainerMapping** element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="958d0-359">EntitySetMapping (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-359">EntitySetMapping (zero or more)</span></span>
-   <span data-ttu-id="958d0-360">AssociationSetMapping (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-360">AssociationSetMapping (zero or more)</span></span>
-   <span data-ttu-id="958d0-361">FunctionImportMapping (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-361">FunctionImportMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-362">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-362">Applicable Attributes</span></span>

<span data-ttu-id="958d0-363">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntityContainerMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-363">The following table describes the attributes that can be applied to the **EntityContainerMapping** element.</span></span>

| <span data-ttu-id="958d0-364">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-364">Attribute Name</span></span>            | <span data-ttu-id="958d0-365">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-365">Is Required</span></span> | <span data-ttu-id="958d0-366">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-366">Value</span></span>                                                                                                                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-367">**StorageModelContainer**</span><span class="sxs-lookup"><span data-stu-id="958d0-367">**StorageModelContainer**</span></span> | <span data-ttu-id="958d0-368">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-368">Yes</span></span>         | <span data-ttu-id="958d0-369">El nombre del contenedor de entidades del modelo de almacenamiento que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-369">The name of the storage model entity container that is being mapped.</span></span>                                                                                                                                                                                     |
| <span data-ttu-id="958d0-370">**CdmEntityContainer**</span><span class="sxs-lookup"><span data-stu-id="958d0-370">**CdmEntityContainer**</span></span>    | <span data-ttu-id="958d0-371">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-371">Yes</span></span>         | <span data-ttu-id="958d0-372">El nombre del contenedor de entidades del modelo conceptual que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-372">The name of the conceptual model entity container that is being mapped.</span></span>                                                                                                                                                                                  |
| <span data-ttu-id="958d0-373">**GenerateUpdateViews**</span><span class="sxs-lookup"><span data-stu-id="958d0-373">**GenerateUpdateViews**</span></span>   | <span data-ttu-id="958d0-374">No</span><span class="sxs-lookup"><span data-stu-id="958d0-374">No</span></span>          | <span data-ttu-id="958d0-375">**True** o **False**.</span><span class="sxs-lookup"><span data-stu-id="958d0-375">**True** or **False**.</span></span> <span data-ttu-id="958d0-376">Si **es false**, no se genera ninguna vista de actualización.</span><span class="sxs-lookup"><span data-stu-id="958d0-376">If **False**, no update views are generated.</span></span> <span data-ttu-id="958d0-377">Este atributo debe establecerse en **false** si tiene una asignación de solo lectura que no sería válida porque los datos no pueden realizar una operación de ida y vuelta correctamente.</span><span class="sxs-lookup"><span data-stu-id="958d0-377">This attribute should be set to **False** when you have a read-only mapping that would be invalid because data may not round-trip successfully.</span></span> <br/> <span data-ttu-id="958d0-378">El valor predeterminado es **True**.</span><span class="sxs-lookup"><span data-stu-id="958d0-378">The default value is **True**.</span></span> |

### <a name="example"></a><span data-ttu-id="958d0-379">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-379">Example</span></span>

<span data-ttu-id="958d0-380">En el ejemplo siguiente se muestra un elemento **EntityContainerMapping** que asigna el contenedor **SchoolModelEntities** (el contenedor de entidades del modelo conceptual) al contenedor **SchoolModelStoreContainer** (el contenedor de entidades del modelo de almacenamiento):</span><span class="sxs-lookup"><span data-stu-id="958d0-380">The following example shows an **EntityContainerMapping** element that maps the **SchoolModelEntities** container (the conceptual model entity container) to the **SchoolModelStoreContainer** container (the storage model entity container):</span></span>

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

## <a name="entitysetmapping-element-msl"></a><span data-ttu-id="958d0-381">EntitySetMapping (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-381">EntitySetMapping Element (MSL)</span></span>

<span data-ttu-id="958d0-382">El elemento **EntitySetMapping** del lenguaje de especificación de asignaciones (MSL) asigna todos los tipos de un conjunto de entidades del modelo conceptual a los conjuntos de entidades del modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-382">The **EntitySetMapping** element in mapping specification language (MSL) maps all types in a conceptual model entity set to entity sets in the storage model.</span></span> <span data-ttu-id="958d0-383">Un conjunto de entidades del modelo conceptual es un contenedor lógico para instancias de entidades del mismo tipo (y tipos derivados).</span><span class="sxs-lookup"><span data-stu-id="958d0-383">An entity set in the conceptual model is a logical container for instances of entities of the same type (and derived types).</span></span> <span data-ttu-id="958d0-384">Un conjunto de entidades del modelo de almacenamiento representa una tabla o vista de la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-384">An entity set in the storage model represents a table or view in the underlying database.</span></span> <span data-ttu-id="958d0-385">El conjunto de entidades del modelo conceptual se especifica mediante el valor del atributo **Name** del elemento **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-385">The conceptual model entity set is specified by the value of the **Name** attribute of the **EntitySetMapping** element.</span></span> <span data-ttu-id="958d0-386">La tabla o vista asignada a se especifica mediante el atributo **StoreEntitySet** en cada elemento MappingFragment secundario o en el propio elemento **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-386">The mapped-to table or view is specified by the **StoreEntitySet** attribute in each child MappingFragment element or in the **EntitySetMapping** element itself.</span></span>

<span data-ttu-id="958d0-387">El elemento **EntitySetMapping** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="958d0-387">The **EntitySetMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="958d0-388">EntityTypeMapping (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-388">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="958d0-389">QueryView (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="958d0-389">QueryView (zero or one)</span></span>
-   <span data-ttu-id="958d0-390">MappingFragment (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-390">MappingFragment (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-391">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-391">Applicable Attributes</span></span>

<span data-ttu-id="958d0-392">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-392">The following table describes the attributes that can be applied to the **EntitySetMapping** element.</span></span>

| <span data-ttu-id="958d0-393">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-393">Attribute Name</span></span>           | <span data-ttu-id="958d0-394">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-394">Is Required</span></span> | <span data-ttu-id="958d0-395">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-395">Value</span></span>                                                                                                                                                                                                                         |
|:-------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-396">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="958d0-396">**Name**</span></span>                 | <span data-ttu-id="958d0-397">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-397">Yes</span></span>         | <span data-ttu-id="958d0-398">El nombre del conjunto de entidades del modelo conceptual que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-398">The name of the conceptual model entity set that is being mapped.</span></span>                                                                                                                                                             |
| <span data-ttu-id="958d0-399">**TypeName** **1**</span><span class="sxs-lookup"><span data-stu-id="958d0-399">**TypeName** **1**</span></span>       | <span data-ttu-id="958d0-400">No</span><span class="sxs-lookup"><span data-stu-id="958d0-400">No</span></span>          | <span data-ttu-id="958d0-401">El nombre del tipo de entidad del modelo conceptual que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-401">The name of the conceptual model entity type that is being mapped.</span></span>                                                                                                                                                            |
| <span data-ttu-id="958d0-402">**StoreEntitySet** **1**</span><span class="sxs-lookup"><span data-stu-id="958d0-402">**StoreEntitySet** **1**</span></span> | <span data-ttu-id="958d0-403">No</span><span class="sxs-lookup"><span data-stu-id="958d0-403">No</span></span>          | <span data-ttu-id="958d0-404">El nombre del conjunto de entidades del modelo de almacenamiento al que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-404">The name of the storage model entity set that is being mapped to.</span></span>                                                                                                                                                             |
| <span data-ttu-id="958d0-405">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="958d0-405">**MakeColumnsDistinct**</span></span>  | <span data-ttu-id="958d0-406">No</span><span class="sxs-lookup"><span data-stu-id="958d0-406">No</span></span>          | <span data-ttu-id="958d0-407">**True** o **false** , dependiendo de si solo se devuelven filas distintas.</span><span class="sxs-lookup"><span data-stu-id="958d0-407">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="958d0-408">Si este atributo se establece en **true**, el atributo **GenerateUpdateViews** del elemento EntityContainerMapping debe establecerse en **false**.</span><span class="sxs-lookup"><span data-stu-id="958d0-408">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

 

<span data-ttu-id="958d0-409">**1** los atributos **TypeName** y **StoreEntitySet** se pueden usar en lugar de los elementos secundarios EntityTypeMapping y MappingFragment para asignar un tipo de entidad único a una sola tabla.</span><span class="sxs-lookup"><span data-stu-id="958d0-409">**1** The **TypeName** and **StoreEntitySet** attributes can be used in place of the EntityTypeMapping and MappingFragment child elements to map a single entity type to a single table.</span></span>

### <a name="example"></a><span data-ttu-id="958d0-410">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-410">Example</span></span>

<span data-ttu-id="958d0-411">En el ejemplo siguiente se muestra un elemento **EntitySetMapping** que asigna tres tipos (un tipo base y dos tipos derivados) en el conjunto de entidades **Courses** del modelo conceptual a tres tablas diferentes en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-411">The following example shows an **EntitySetMapping** element that maps three types (a base type and two derived types) in the **Courses** entity set of the conceptual model to three different tables in the underlying database.</span></span> <span data-ttu-id="958d0-412">Las tablas se especifican mediante el atributo **StoreEntitySet** en cada elemento **MappingFragment** .</span><span class="sxs-lookup"><span data-stu-id="958d0-412">The tables are specified by the **StoreEntitySet** attribute in each **MappingFragment** element.</span></span>

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

## <a name="entitytypemapping-element-msl"></a><span data-ttu-id="958d0-413">EntityTypeMapping (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-413">EntityTypeMapping Element (MSL)</span></span>

<span data-ttu-id="958d0-414">El elemento **EntityTypeMapping** del lenguaje de especificación de asignaciones (MSL) define la asignación entre un tipo de entidad en el modelo conceptual y las tablas o vistas de la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-414">The **EntityTypeMapping** element in mapping specification language (MSL) defines the mapping between an entity type in the conceptual model and tables or views in the underlying database.</span></span> <span data-ttu-id="958d0-415">Para obtener información sobre los tipos de entidad del modelo conceptual y las tablas o vistas de la base de datos subyacente, vea EntityType (Elemento) (CSDL) y EntitySet (Elemento) (SSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-415">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="958d0-416">El tipo de entidad del modelo conceptual que se está asignando se especifica mediante el atributo **TypeName** del elemento **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-416">The conceptual model entity type that is being mapped is specified by the **TypeName** attribute of the **EntityTypeMapping** element.</span></span> <span data-ttu-id="958d0-417">La tabla o vista que se está asignando se especifica mediante el atributo **StoreEntitySet** del elemento MappingFragment secundario.</span><span class="sxs-lookup"><span data-stu-id="958d0-417">The table or view that is being mapped is specified by the **StoreEntitySet** attribute of the child MappingFragment element.</span></span>

<span data-ttu-id="958d0-418">El elemento secundario ModificationFunctionMapping se puede utilizar para asignar las funciones de inserción, actualización o eliminación de tipos de entidad a procedimientos almacenados de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="958d0-418">The ModificationFunctionMapping child element can be used to map the insert, update, or delete functions of entity types to stored procedures in the database.</span></span>

<span data-ttu-id="958d0-419">El elemento **EntityTypeMapping** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="958d0-419">The **EntityTypeMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="958d0-420">MappingFragment (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-420">MappingFragment (zero or more)</span></span>
-   <span data-ttu-id="958d0-421">ModificationFunctionMapping (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="958d0-421">ModificationFunctionMapping (zero or one)</span></span>
-   <span data-ttu-id="958d0-422">ScalarProperty</span><span class="sxs-lookup"><span data-stu-id="958d0-422">ScalarProperty</span></span>
-   <span data-ttu-id="958d0-423">Condición</span><span class="sxs-lookup"><span data-stu-id="958d0-423">Condition</span></span>

> [!NOTE]
> <span data-ttu-id="958d0-424">Los elementos **MappingFragment** y **ModificationFunctionMapping** no pueden ser elementos secundarios del elemento **EntityTypeMapping** al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="958d0-424">**MappingFragment** and **ModificationFunctionMapping** elements cannot be child elements of the **EntityTypeMapping** element at the same time.</span></span>


> [!NOTE]
> <span data-ttu-id="958d0-425">Los elementos **ScalarProperty** y **Condition** solo pueden ser elementos secundarios del elemento **EntityTypeMapping** cuando se usa dentro de un elemento FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="958d0-425">The **ScalarProperty** and **Condition** elements can only be child elements of the **EntityTypeMapping** element when it is used within a FunctionImportMapping element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-426">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-426">Applicable Attributes</span></span>

<span data-ttu-id="958d0-427">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-427">The following table describes the attributes that can be applied to the **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="958d0-428">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-428">Attribute Name</span></span> | <span data-ttu-id="958d0-429">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-429">Is Required</span></span> | <span data-ttu-id="958d0-430">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-430">Value</span></span>                                                                                                                                                                                                |
|:---------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-431">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="958d0-431">**TypeName**</span></span>   | <span data-ttu-id="958d0-432">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-432">Yes</span></span>         | <span data-ttu-id="958d0-433">El nombre completo, calificado con el espacio de nombres, del tipo de entidad del modelo conceptual que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-433">The namespace-qualified name of the conceptual model entity type that is being mapped.</span></span> <br/> <span data-ttu-id="958d0-434">Si el tipo es abstracto o un tipo derivado, el valor debe ser `IsOfType(Namespace-qualified_type_name)`.</span><span class="sxs-lookup"><span data-stu-id="958d0-434">If the type is abstract or a derived type, the value must be `IsOfType(Namespace-qualified_type_name)`.</span></span> |

### <a name="example"></a><span data-ttu-id="958d0-435">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-435">Example</span></span>

<span data-ttu-id="958d0-436">En el ejemplo siguiente se muestra un elemento EntitySetMapping con dos elementos **EntityTypeMapping** secundarios.</span><span class="sxs-lookup"><span data-stu-id="958d0-436">The following example shows an EntitySetMapping element with two child **EntityTypeMapping** elements.</span></span> <span data-ttu-id="958d0-437">En el primer elemento **EntityTypeMapping** , el tipo de entidad **SchoolModel. person** se asigna a la tabla **Person** .</span><span class="sxs-lookup"><span data-stu-id="958d0-437">In the first **EntityTypeMapping** element, the **SchoolModel.Person** entity type is mapped to the **Person** table.</span></span> <span data-ttu-id="958d0-438">En el segundo elemento **EntityTypeMapping** , la funcionalidad de actualización del tipo **SchoolModel. person** se asigna a un procedimiento almacenado, **UpdatePerson**, en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="958d0-438">In the second **EntityTypeMapping** element, the update functionality of the **SchoolModel.Person** type is mapped to a stored procedure, **UpdatePerson**, in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="958d0-439">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-439">Example</span></span>

<span data-ttu-id="958d0-440">En el ejemplo siguiente se muestra la asignación de una jerarquía de tipos en la que el tipo raíz es abstracto.</span><span class="sxs-lookup"><span data-stu-id="958d0-440">The next example shows the mapping of a type hierarchy in which the root type is abstract.</span></span> <span data-ttu-id="958d0-441">Tenga en cuenta el uso de la `IsOfType` sintaxis para los atributos **TypeName** .</span><span class="sxs-lookup"><span data-stu-id="958d0-441">Note the use of the `IsOfType` syntax for the **TypeName** attributes.</span></span>

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

## <a name="functionimportmapping-element-msl"></a><span data-ttu-id="958d0-442">FunctionImportMapping (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-442">FunctionImportMapping Element (MSL)</span></span>

<span data-ttu-id="958d0-443">El elemento **FunctionImportMapping** del lenguaje de especificación de asignaciones (MSL) define la asignación entre una importación de función en el modelo conceptual y un procedimiento almacenado o una función en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-443">The **FunctionImportMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure or function in the underlying database.</span></span> <span data-ttu-id="958d0-444">Las importaciones de función se deben declarar en el modelo conceptual, mientras que los procedimientos almacenados se deben declarar en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-444">Function imports must be declared in the conceptual model and stored procedures must be declared in the storage model.</span></span> <span data-ttu-id="958d0-445">Para obtener más información, vea elemento FunctionImport (CSDL) y elemento function (SSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-445">For more information, see FunctionImport Element (CSDL) and Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="958d0-446">De forma predeterminada, si una importación de función devuelve un tipo de entidad del modelo conceptual o un tipo complejo, entonces los nombres de las columnas devueltas por el procedimiento almacenado subyacente deben coincidir exactamente con los nombres de las propiedades del tipo del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="958d0-446">By default, if a function import returns a conceptual model entity type or complex type, then the names of the columns returned by the underlying stored procedure must exactly match the names of the properties on the conceptual model type.</span></span> <span data-ttu-id="958d0-447">Si los nombres de columna no coinciden exactamente con los nombres de propiedad, la asignación se debe definir en un elemento ResultMapping.</span><span class="sxs-lookup"><span data-stu-id="958d0-447">If the column names do not exactly match the property names, the mapping must be defined in a ResultMapping element.</span></span>

<span data-ttu-id="958d0-448">El elemento **FunctionImportMapping** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="958d0-448">The **FunctionImportMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="958d0-449">ResultMapping (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-449">ResultMapping (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-450">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-450">Applicable Attributes</span></span>

<span data-ttu-id="958d0-451">En la tabla siguiente se describen los atributos aplicables al elemento **FunctionImportMapping** :</span><span class="sxs-lookup"><span data-stu-id="958d0-451">The following table describes the attributes that are applicable to the **FunctionImportMapping** element:</span></span>

| <span data-ttu-id="958d0-452">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-452">Attribute Name</span></span>         | <span data-ttu-id="958d0-453">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-453">Is Required</span></span> | <span data-ttu-id="958d0-454">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-454">Value</span></span>                                                                                   |
|:-----------------------|:------------|:----------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-455">**FunctionImportName**</span><span class="sxs-lookup"><span data-stu-id="958d0-455">**FunctionImportName**</span></span> | <span data-ttu-id="958d0-456">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-456">Yes</span></span>         | <span data-ttu-id="958d0-457">El nombre de la importación de función del modelo conceptual que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-457">The name of the function import in the conceptual model that is being mapped.</span></span>           |
| <span data-ttu-id="958d0-458">**Nombrefunción**</span><span class="sxs-lookup"><span data-stu-id="958d0-458">**FunctionName**</span></span>       | <span data-ttu-id="958d0-459">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-459">Yes</span></span>         | <span data-ttu-id="958d0-460">El nombre, calificado con el espacio de nombres, de la función del modelo de almacenamiento que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-460">The namespace-qualified name of the function in the storage model that is being mapped.</span></span> |

### <a name="example"></a><span data-ttu-id="958d0-461">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-461">Example</span></span>

<span data-ttu-id="958d0-462">El siguiente ejemplo se basa en el modelo School.</span><span class="sxs-lookup"><span data-stu-id="958d0-462">The following example is based on the School model.</span></span> <span data-ttu-id="958d0-463">Considere la siguiente función en el modelo de almacenamiento:</span><span class="sxs-lookup"><span data-stu-id="958d0-463">Consider the following function in the storage model:</span></span>

``` xml
 <Function Name="GetStudentGrades" Aggregate="false"
           BuiltIn="false" NiladicFunction="false"
           IsComposable="false" ParameterTypeSemantics="AllowImplicitConversion"
           Schema="dbo">
   <Parameter Name="StudentID" Type="int" Mode="In" />
 </Function>
```

<span data-ttu-id="958d0-464">También considere esta importación de función en el modelo conceptual:</span><span class="sxs-lookup"><span data-stu-id="958d0-464">Also consider this function import in the conceptual model:</span></span>

``` xml
 <FunctionImport Name="GetStudentGrades" EntitySet="StudentGrades"
                 ReturnType="Collection(SchoolModel.StudentGrade)">
   <Parameter Name="StudentID" Mode="In" Type="Int32" />
 </FunctionImport>
```

<span data-ttu-id="958d0-465">En el ejemplo siguiente se muestra un elemento **FunctionImportMapping** que se usa para asignar la función y la importación de funciones anteriores entre sí:</span><span class="sxs-lookup"><span data-stu-id="958d0-465">The following example show a **FunctionImportMapping** element used to map the function and function import above to each other:</span></span>

``` xml
 <FunctionImportMapping FunctionImportName="GetStudentGrades"
                        FunctionName="SchoolModel.Store.GetStudentGrades" />
```
 
## <a name="insertfunction-element-msl"></a><span data-ttu-id="958d0-466">InsertFunction (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-466">InsertFunction Element (MSL)</span></span>

<span data-ttu-id="958d0-467">El elemento **InsertFunction** del lenguaje de especificación de asignaciones (MSL) asigna la función de inserción de un tipo de entidad o asociación del modelo conceptual a un procedimiento almacenado en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-467">The **InsertFunction** element in mapping specification language (MSL) maps the insert function of an entity type or association in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="958d0-468">Los procedimientos almacenados a los que están asignados las funciones de modificación se deben declarar en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-468">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="958d0-469">Para obtener más información, vea elemento function (SSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-469">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="958d0-470">Si no asigna las tres operaciones de inserción, actualización o eliminación de un tipo de entidad a los procedimientos almacenados, se producirá un error en las operaciones no asignadas si se ejecutan en tiempo de ejecución y se genera un UpdateException.</span><span class="sxs-lookup"><span data-stu-id="958d0-470">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="958d0-471">El elemento **InsertFunction** puede ser un elemento secundario del elemento ModificationFunctionMapping y aplicarse al elemento EntityTypeMapping o al elemento AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="958d0-471">The **InsertFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

### <a name="insertfunction-applied-to-entitytypemapping"></a><span data-ttu-id="958d0-472">InsertFunction aplicado a EntityTypeMapping</span><span class="sxs-lookup"><span data-stu-id="958d0-472">InsertFunction Applied to EntityTypeMapping</span></span>

<span data-ttu-id="958d0-473">Cuando se aplica al elemento EntityTypeMapping, el elemento **InsertFunction** asigna la función de inserción de un tipo de entidad del modelo conceptual a un procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="958d0-473">When applied to the EntityTypeMapping element, the **InsertFunction** element maps the insert function of an entity type in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="958d0-474">El elemento **InsertFunction** puede tener los elementos secundarios siguientes cuando se aplica a un elemento **EntityTypeMapping** :</span><span class="sxs-lookup"><span data-stu-id="958d0-474">The **InsertFunction** element can have the following child elements when applied to an **EntityTypeMapping** element:</span></span>

-   <span data-ttu-id="958d0-475">AssociationEnd (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-475">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="958d0-476">ComplexProperty (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-476">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="958d0-477">ResultBinding (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="958d0-477">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="958d0-478">ScalarProperty (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-478">ScarlarProperty (zero or more)</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="958d0-479">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-479">Applicable Attributes</span></span>

<span data-ttu-id="958d0-480">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **InsertFunction** cuando se aplican a un elemento **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-480">The following table describes the attributes that can be applied to the **InsertFunction** element when applied to an **EntityTypeMapping** element.</span></span>

| <span data-ttu-id="958d0-481">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-481">Attribute Name</span></span>            | <span data-ttu-id="958d0-482">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-482">Is Required</span></span> | <span data-ttu-id="958d0-483">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-483">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-484">**Nombrefunción**</span><span class="sxs-lookup"><span data-stu-id="958d0-484">**FunctionName**</span></span>          | <span data-ttu-id="958d0-485">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-485">Yes</span></span>         | <span data-ttu-id="958d0-486">El nombre completo, calificado con el espacio de nombres, del procedimiento almacenado al que la función de inserción está asignada.</span><span class="sxs-lookup"><span data-stu-id="958d0-486">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="958d0-487">El procedimiento almacenado se debe declarar en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-487">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="958d0-488">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="958d0-488">**RowsAffectedParameter**</span></span> | <span data-ttu-id="958d0-489">No</span><span class="sxs-lookup"><span data-stu-id="958d0-489">No</span></span>          | <span data-ttu-id="958d0-490">El nombre del parámetro de salida que devuelve el número de filas afectadas.</span><span class="sxs-lookup"><span data-stu-id="958d0-490">The name of the output parameter that returns the number of affected rows.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="958d0-491">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-491">Example</span></span>

<span data-ttu-id="958d0-492">El ejemplo siguiente se basa en el modelo School y muestra el elemento **InsertFunction** que se usa para asignar la función de inserción del tipo de entidad person al procedimiento almacenado **InsertPerson** .</span><span class="sxs-lookup"><span data-stu-id="958d0-492">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the Person entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="958d0-493">El procedimiento almacenado **InsertPerson** se declara en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-493">The **InsertPerson** stored procedure is declared in the storage model.</span></span>

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
### <a name="insertfunction-applied-to-associationsetmapping"></a><span data-ttu-id="958d0-494">InsertFunction aplicado a AssociationSetMapping</span><span class="sxs-lookup"><span data-stu-id="958d0-494">InsertFunction Applied to AssociationSetMapping</span></span>

<span data-ttu-id="958d0-495">Cuando se aplica al elemento AssociationSetMapping, el elemento **InsertFunction** asigna la función de inserción de una asociación del modelo conceptual a un procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="958d0-495">When applied to the AssociationSetMapping element, the **InsertFunction** element maps the insert function of an association in the conceptual model to a stored procedure.</span></span>

<span data-ttu-id="958d0-496">El elemento **InsertFunction** puede tener los elementos secundarios siguientes cuando se aplica al elemento **AssociationSetMapping** :</span><span class="sxs-lookup"><span data-stu-id="958d0-496">The **InsertFunction** element can have the following child elements when applied to the **AssociationSetMapping** element:</span></span>

-   <span data-ttu-id="958d0-497">EndProperty</span><span class="sxs-lookup"><span data-stu-id="958d0-497">EndProperty</span></span>

#### <a name="applicable-attributes"></a><span data-ttu-id="958d0-498">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-498">Applicable Attributes</span></span>

<span data-ttu-id="958d0-499">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **InsertFunction** cuando se aplica al elemento **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-499">The following table describes the attributes that can be applied to the **InsertFunction** element when it is applied to the **AssociationSetMapping** element.</span></span>

| <span data-ttu-id="958d0-500">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-500">Attribute Name</span></span>            | <span data-ttu-id="958d0-501">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-501">Is Required</span></span> | <span data-ttu-id="958d0-502">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-502">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-503">**Nombrefunción**</span><span class="sxs-lookup"><span data-stu-id="958d0-503">**FunctionName**</span></span>          | <span data-ttu-id="958d0-504">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-504">Yes</span></span>         | <span data-ttu-id="958d0-505">El nombre completo, calificado con el espacio de nombres, del procedimiento almacenado al que la función de inserción está asignada.</span><span class="sxs-lookup"><span data-stu-id="958d0-505">The namespace-qualified name of the stored procedure to which the insert function is mapped.</span></span> <span data-ttu-id="958d0-506">El procedimiento almacenado se debe declarar en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-506">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="958d0-507">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="958d0-507">**RowsAffectedParameter**</span></span> | <span data-ttu-id="958d0-508">No</span><span class="sxs-lookup"><span data-stu-id="958d0-508">No</span></span>          | <span data-ttu-id="958d0-509">El nombre del parámetro de salida que devuelve el número de filas afectadas.</span><span class="sxs-lookup"><span data-stu-id="958d0-509">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

#### <a name="example"></a><span data-ttu-id="958d0-510">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-510">Example</span></span>

<span data-ttu-id="958d0-511">El ejemplo siguiente se basa en el modelo School y muestra el elemento **InsertFunction** que se usa para asignar la función de inserción de la Asociación **CourseInstructor** al procedimiento almacenado **InsertCourseInstructor** .</span><span class="sxs-lookup"><span data-stu-id="958d0-511">The following example is based on the School model and shows the **InsertFunction** element used to map insert function of the **CourseInstructor** association to the **InsertCourseInstructor** stored procedure.</span></span> <span data-ttu-id="958d0-512">El procedimiento almacenado **InsertCourseInstructor** se declara en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-512">The **InsertCourseInstructor** stored procedure is declared in the storage model.</span></span>

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

## <a name="mapping-element-msl"></a><span data-ttu-id="958d0-513">Mapping (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-513">Mapping Element (MSL)</span></span>

<span data-ttu-id="958d0-514">El elemento **mapping** del lenguaje de especificación de asignaciones (MSL) contiene información para la asignación de objetos que se definen en un modelo conceptual a una base de datos (tal y como se describe en un modelo de almacenamiento).</span><span class="sxs-lookup"><span data-stu-id="958d0-514">The **Mapping** element in mapping specification language (MSL) contains information for mapping objects that are defined in a conceptual model to a database (as described in a storage model).</span></span> <span data-ttu-id="958d0-515">Para obtener más información, vea especificación de CSDL y especificación de SSDL.</span><span class="sxs-lookup"><span data-stu-id="958d0-515">For more information, see CSDL Specification and SSDL Specification.</span></span>

<span data-ttu-id="958d0-516">El elemento **mapping** es el elemento raíz de una especificación de asignación.</span><span class="sxs-lookup"><span data-stu-id="958d0-516">The **Mapping** element is the root element for a mapping specification.</span></span> <span data-ttu-id="958d0-517">El espacio de nombres XML para las especificaciones de asignación es https://schemas.microsoft.com/ado/2009/11/mapping/cs .</span><span class="sxs-lookup"><span data-stu-id="958d0-517">The XML namespace for mapping specifications is https://schemas.microsoft.com/ado/2009/11/mapping/cs.</span></span>

<span data-ttu-id="958d0-518">El elemento de asignación puede tener los elementos secundarios siguientes (en el orden mostrado):</span><span class="sxs-lookup"><span data-stu-id="958d0-518">The mapping element can have the following child elements (in the order listed):</span></span>

-   <span data-ttu-id="958d0-519">Alias (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-519">Alias (zero or more)</span></span>
-   <span data-ttu-id="958d0-520">EntityContainerMapping (exactamente uno)</span><span class="sxs-lookup"><span data-stu-id="958d0-520">EntityContainerMapping (exactly one)</span></span>

<span data-ttu-id="958d0-521">Los nombres de los tipos de modelos conceptuales y de almacenamiento a los que se hace referencia en MSL deben estar calificados con sus respectivos nombres de espacios de nombres.</span><span class="sxs-lookup"><span data-stu-id="958d0-521">Names of conceptual and storage model types that are referenced in MSL must be qualified by their respective namespace names.</span></span> <span data-ttu-id="958d0-522">Para obtener información sobre el nombre del espacio de nombres del modelo conceptual, vea elemento Schema (CSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-522">For information about the conceptual model namespace name, see Schema Element (CSDL).</span></span> <span data-ttu-id="958d0-523">Para obtener información sobre el nombre del espacio de nombres del modelo de almacenamiento, vea schema (elemento) (SSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-523">For information about the storage model namespace name, see Schema Element (SSDL).</span></span> <span data-ttu-id="958d0-524">Los alias para los espacios de nombres que se utilizan en MSL se pueden definir con el elemento Alias.</span><span class="sxs-lookup"><span data-stu-id="958d0-524">Aliases for namespaces that are used in MSL can be defined with the Alias element.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-525">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-525">Applicable Attributes</span></span>

<span data-ttu-id="958d0-526">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento de **asignación** .</span><span class="sxs-lookup"><span data-stu-id="958d0-526">The table below describes the attributes that can be applied to the **Mapping** element.</span></span>

| <span data-ttu-id="958d0-527">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-527">Attribute Name</span></span> | <span data-ttu-id="958d0-528">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-528">Is Required</span></span> | <span data-ttu-id="958d0-529">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-529">Value</span></span>                                                 |
|:---------------|:------------|:------------------------------------------------------|
| <span data-ttu-id="958d0-530">**Espacia**</span><span class="sxs-lookup"><span data-stu-id="958d0-530">**Space**</span></span>      | <span data-ttu-id="958d0-531">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-531">Yes</span></span>         | <span data-ttu-id="958d0-532">**C-S**.</span><span class="sxs-lookup"><span data-stu-id="958d0-532">**C-S**.</span></span> <span data-ttu-id="958d0-533">Este es un valor fijo y no se puede cambiar.</span><span class="sxs-lookup"><span data-stu-id="958d0-533">This is a fixed value and cannot be changed.</span></span> |

### <a name="example"></a><span data-ttu-id="958d0-534">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-534">Example</span></span>

<span data-ttu-id="958d0-535">En el ejemplo siguiente se muestra un elemento de **asignación** basado en parte del modelo School.</span><span class="sxs-lookup"><span data-stu-id="958d0-535">The following example shows a **Mapping** element that is based on part of the School model.</span></span> <span data-ttu-id="958d0-536">Para obtener más información sobre el modelo School, consulte Inicio rápido (Entity Framework):</span><span class="sxs-lookup"><span data-stu-id="958d0-536">For more information about the School model, see Quickstart (Entity Framework):</span></span>

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

## <a name="mappingfragment-element-msl"></a><span data-ttu-id="958d0-537">MappingFragment (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-537">MappingFragment Element (MSL)</span></span>

<span data-ttu-id="958d0-538">El elemento **MappingFragment** del lenguaje de especificación de asignaciones (MSL) define la asignación entre las propiedades de un tipo de entidad del modelo conceptual y una tabla o vista en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="958d0-538">The **MappingFragment** element in mapping specification language (MSL) defines the mapping between the properties of a conceptual model entity type and a table or view in the database.</span></span> <span data-ttu-id="958d0-539">Para obtener información sobre los tipos de entidad del modelo conceptual y las tablas o vistas de la base de datos subyacente, vea EntityType (Elemento) (CSDL) y EntitySet (Elemento) (SSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-539">For information about conceptual model entity types and underlying database tables or views, see EntityType Element (CSDL) and EntitySet Element (SSDL).</span></span> <span data-ttu-id="958d0-540">El **MappingFragment** puede ser un elemento secundario del elemento EntityTypeMapping o el elemento EntitySetMapping.</span><span class="sxs-lookup"><span data-stu-id="958d0-540">The **MappingFragment** can be a child element of the EntityTypeMapping element or the EntitySetMapping element.</span></span>

<span data-ttu-id="958d0-541">El elemento **MappingFragment** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="958d0-541">The **MappingFragment** element can have the following child elements:</span></span>

-   <span data-ttu-id="958d0-542">ComplexType (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-542">ComplexType (zero or more)</span></span>
-   <span data-ttu-id="958d0-543">ScalarProperty (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-543">ScalarProperty (zero or more)</span></span>
-   <span data-ttu-id="958d0-544">Condition (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-544">Condition (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-545">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-545">Applicable Attributes</span></span>

<span data-ttu-id="958d0-546">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **MappingFragment** .</span><span class="sxs-lookup"><span data-stu-id="958d0-546">The following table describes the attributes that can be applied to the **MappingFragment** element.</span></span>

| <span data-ttu-id="958d0-547">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-547">Attribute Name</span></span>          | <span data-ttu-id="958d0-548">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-548">Is Required</span></span> | <span data-ttu-id="958d0-549">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-549">Value</span></span>                                                                                                                                                                                                                         |
|:------------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-550">**StoreEntitySet**</span><span class="sxs-lookup"><span data-stu-id="958d0-550">**StoreEntitySet**</span></span>      | <span data-ttu-id="958d0-551">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-551">Yes</span></span>         | <span data-ttu-id="958d0-552">El nombre de la tabla o vista que se está asociando.</span><span class="sxs-lookup"><span data-stu-id="958d0-552">The name of the table or view that is being mapped.</span></span>                                                                                                                                                                           |
| <span data-ttu-id="958d0-553">**MakeColumnsDistinct**</span><span class="sxs-lookup"><span data-stu-id="958d0-553">**MakeColumnsDistinct**</span></span> | <span data-ttu-id="958d0-554">No</span><span class="sxs-lookup"><span data-stu-id="958d0-554">No</span></span>          | <span data-ttu-id="958d0-555">**True** o **false** , dependiendo de si solo se devuelven filas distintas.</span><span class="sxs-lookup"><span data-stu-id="958d0-555">**True** or **False** depending on whether only distinct rows are returned.</span></span> <br/> <span data-ttu-id="958d0-556">Si este atributo se establece en **true**, el atributo **GenerateUpdateViews** del elemento EntityContainerMapping debe establecerse en **false**.</span><span class="sxs-lookup"><span data-stu-id="958d0-556">If this attribute is set to **True**, the **GenerateUpdateViews** attribute of the EntityContainerMapping element must be set to **False**.</span></span> |

### <a name="example"></a><span data-ttu-id="958d0-557">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-557">Example</span></span>

<span data-ttu-id="958d0-558">En el ejemplo siguiente se muestra un elemento **MappingFragment** como elemento secundario de un elemento **EntityTypeMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-558">The following example shows a **MappingFragment** element as the child of an **EntityTypeMapping** element.</span></span> <span data-ttu-id="958d0-559">En este ejemplo, las propiedades del tipo de **curso** en el modelo conceptual se asignan a las columnas de la tabla **Course** en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="958d0-559">In this example, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

### <a name="example"></a><span data-ttu-id="958d0-560">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-560">Example</span></span>

<span data-ttu-id="958d0-561">En el ejemplo siguiente se muestra un elemento **MappingFragment** como el elemento secundario de un elemento **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-561">The following example shows a **MappingFragment** element as the child of an **EntitySetMapping** element.</span></span> <span data-ttu-id="958d0-562">Como en el ejemplo anterior, las propiedades del tipo de **curso** en el modelo conceptual se asignan a las columnas de la tabla **Course** en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="958d0-562">As in the example above, properties of the **Course** type in the conceptual model are mapped to columns of the **Course** table in the database.</span></span>

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

## <a name="modificationfunctionmapping-element-msl"></a><span data-ttu-id="958d0-563">ModificationFunctionMapping (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-563">ModificationFunctionMapping Element (MSL)</span></span>

<span data-ttu-id="958d0-564">El elemento **ModificationFunctionMapping** del lenguaje de especificación de asignaciones (MSL) asigna las funciones de inserción, actualización y eliminación de un tipo de entidad del modelo conceptual a los procedimientos almacenados en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-564">The **ModificationFunctionMapping** element in mapping specification language (MSL) maps the insert, update, and delete functions of a conceptual model entity type to stored procedures in the underlying database.</span></span> <span data-ttu-id="958d0-565">El elemento **ModificationFunctionMapping** también puede asignar las funciones de inserción y eliminación para las asociaciones de varios a varios del modelo conceptual a los procedimientos almacenados en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-565">The **ModificationFunctionMapping** element can also map the insert and delete functions for many-to-many associations in the conceptual model to stored procedures in the underlying database.</span></span> <span data-ttu-id="958d0-566">Los procedimientos almacenados a los que están asignados las funciones de modificación se deben declarar en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-566">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="958d0-567">Para obtener más información, vea elemento function (SSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-567">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
> <span data-ttu-id="958d0-568">Si no asigna las tres operaciones de inserción, actualización o eliminación de un tipo de entidad a los procedimientos almacenados, se producirá un error en las operaciones no asignadas si se ejecutan en tiempo de ejecución y se genera un UpdateException.</span><span class="sxs-lookup"><span data-stu-id="958d0-568">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>


> [!NOTE]
> <span data-ttu-id="958d0-569">Si las funciones de modificación para una entidad de una jerarquía de herencia están asignadas a procedimientos almacenados, entonces las funciones de modificación para todos los tipos de la jerarquía deben estar asignadas a procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="958d0-569">If the modification functions for one entity in an inheritance hierarchy are mapped to stored procedures, then modification functions for all types in the hierarchy must be mapped to stored procedures.</span></span>

<span data-ttu-id="958d0-570">El elemento **ModificationFunctionMapping** puede ser un elemento secundario del elemento EntityTypeMapping o el elemento AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="958d0-570">The **ModificationFunctionMapping** element can be a child of the EntityTypeMapping element or the AssociationSetMapping element.</span></span>

<span data-ttu-id="958d0-571">El elemento **ModificationFunctionMapping** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="958d0-571">The **ModificationFunctionMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="958d0-572">DeleteFunction (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="958d0-572">DeleteFunction (zero or one)</span></span>
-   <span data-ttu-id="958d0-573">InsertFunction (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="958d0-573">InsertFunction (zero or one)</span></span>
-   <span data-ttu-id="958d0-574">UpdateFunction (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="958d0-574">UpdateFunction (zero or one)</span></span>

<span data-ttu-id="958d0-575">No hay atributos aplicables al elemento **ModificationFunctionMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-575">No attributes are applicable to the **ModificationFunctionMapping** element.</span></span>

### <a name="example"></a><span data-ttu-id="958d0-576">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-576">Example</span></span>

<span data-ttu-id="958d0-577">En el ejemplo siguiente se muestra la asignación de conjunto de entidades para el conjunto de entidades **People** del modelo School.</span><span class="sxs-lookup"><span data-stu-id="958d0-577">The following example shows the entity set mapping for the **People** entity set in the School model.</span></span> <span data-ttu-id="958d0-578">Además de la asignación de columnas para el tipo de entidad **Person** , se muestra la asignación de las funciones INSERT, Update y DELETE del tipo **Person** .</span><span class="sxs-lookup"><span data-stu-id="958d0-578">In addition to the column mapping for the **Person** entity type, the mapping of the insert, update, and delete functions of the **Person** type are shown.</span></span> <span data-ttu-id="958d0-579">Las funciones asignadas se declaran en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-579">The functions that are mapped to are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="958d0-580">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-580">Example</span></span>

<span data-ttu-id="958d0-581">En el ejemplo siguiente se muestra la asignación del conjunto de asociaciones para el conjunto de asociaciones **CourseInstructor** en el modelo School.</span><span class="sxs-lookup"><span data-stu-id="958d0-581">The following example shows the association set mapping for the **CourseInstructor** association set in the School model.</span></span> <span data-ttu-id="958d0-582">Además de la asignación de columnas para la Asociación **CourseInstructor** , se muestra la asignación de las funciones de inserción y eliminación de la Asociación **CourseInstructor** .</span><span class="sxs-lookup"><span data-stu-id="958d0-582">In addition to the column mapping for the **CourseInstructor** association, the mapping of the insert and delete functions of the **CourseInstructor** association are shown.</span></span> <span data-ttu-id="958d0-583">Las funciones asignadas se declaran en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-583">The functions that are mapped to are declared in the storage model.</span></span>

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
 

 

## <a name="queryview-element-msl"></a><span data-ttu-id="958d0-584">QueryView (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-584">QueryView Element (MSL)</span></span>

<span data-ttu-id="958d0-585">El elemento **QueryView** del lenguaje de especificación de asignaciones (MSL) define una asignación de solo lectura entre un tipo de entidad o una asociación en el modelo conceptual y una tabla en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-585">The **QueryView** element in mapping specification language (MSL) defines a read-only mapping between an entity type or association in the conceptual model and a table in the underlying database.</span></span> <span data-ttu-id="958d0-586">La asignación se define con una consulta Entity SQL que se evalúa con el modelo de almacenamiento y se expresa el conjunto de resultados en términos de una entidad o asociación en el modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="958d0-586">The mapping is defined with an Entity SQL query that is evaluated against the storage model, and you express the result set in terms of an entity or association in the conceptual model.</span></span> <span data-ttu-id="958d0-587">Dado que las vistas de consulta son de solo lectura, no puede utilizar los comandos de actualización estándar para actualizar los tipos que se definen mediante vistas de consulta.</span><span class="sxs-lookup"><span data-stu-id="958d0-587">Because query views are read-only, you cannot use standard update commands to update types that are defined by query views.</span></span> <span data-ttu-id="958d0-588">Puede realizar las actualizaciones de estos tipos utilizando funciones de modificación.</span><span class="sxs-lookup"><span data-stu-id="958d0-588">You can make updates to these types by using modification functions.</span></span> <span data-ttu-id="958d0-589">Para obtener más información, vea cómo: asignar funciones de modificación a procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="958d0-589">For more information, see How to: Map Modification Functions to Stored Procedures.</span></span>

> [!NOTE]
> <span data-ttu-id="958d0-590">En el elemento **QueryView** , no se admiten Entity SQL expresiones que contengan **GroupBy**, agregados de grupo o propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="958d0-590">In the **QueryView** element, Entity SQL expressions that contain **GroupBy**, group aggregates, or navigation properties are not supported.</span></span>

 

<span data-ttu-id="958d0-591">El elemento **QueryView** puede ser un elemento secundario del elemento EntitySetMapping o del elemento AssociationSetMapping.</span><span class="sxs-lookup"><span data-stu-id="958d0-591">The **QueryView** element can be a child of the EntitySetMapping element or the AssociationSetMapping element.</span></span> <span data-ttu-id="958d0-592">En el primer caso, la vista de consulta define una asignación de solo lectura para una entidad del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="958d0-592">In the former case, the query view defines a read-only mapping for an entity in the conceptual model.</span></span> <span data-ttu-id="958d0-593">En el último caso, la vista de consulta define una asignación de solo lectura para una asociación del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="958d0-593">In the latter case, the query view defines a read-only mapping for an association in the conceptual model.</span></span>

> [!NOTE]
> <span data-ttu-id="958d0-594">Si el elemento **AssociationSetMapping** es para una asociación con una restricción referencial, se omite el elemento **AssociationSetMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-594">If the **AssociationSetMapping** element is for an association with a referential constraint, the **AssociationSetMapping** element is ignored.</span></span> <span data-ttu-id="958d0-595">Para obtener más información, vea ReferentialConstraint (elemento) (CSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-595">For more information, see ReferentialConstraint Element (CSDL).</span></span>

<span data-ttu-id="958d0-596">El elemento **QueryView** no puede tener elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="958d0-596">The **QueryView** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-597">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-597">Applicable Attributes</span></span>

<span data-ttu-id="958d0-598">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **QueryView** .</span><span class="sxs-lookup"><span data-stu-id="958d0-598">The following table describes the attributes that can be applied to the **QueryView** element.</span></span>

| <span data-ttu-id="958d0-599">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-599">Attribute Name</span></span> | <span data-ttu-id="958d0-600">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-600">Is Required</span></span> | <span data-ttu-id="958d0-601">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-601">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-602">**TypeName**</span><span class="sxs-lookup"><span data-stu-id="958d0-602">**TypeName**</span></span>   | <span data-ttu-id="958d0-603">No</span><span class="sxs-lookup"><span data-stu-id="958d0-603">No</span></span>          | <span data-ttu-id="958d0-604">El nombre del tipo de modelo conceptual que se está asignando mediante la vista de consulta.</span><span class="sxs-lookup"><span data-stu-id="958d0-604">The name of the conceptual model type that is being mapped by the query view.</span></span> |

### <a name="example"></a><span data-ttu-id="958d0-605">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-605">Example</span></span>

<span data-ttu-id="958d0-606">En el ejemplo siguiente se muestra el elemento **QueryView** como elemento secundario del elemento **EntitySetMapping** y se define una asignación de vista de consulta para el tipo de entidad **Department** en el modelo School.</span><span class="sxs-lookup"><span data-stu-id="958d0-606">The following example shows the **QueryView** element as a child of the **EntitySetMapping** element and defines a query view mapping for the **Department** entity type in the School Model.</span></span>

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

<span data-ttu-id="958d0-607">Dado que la consulta solo devuelve un subconjunto de los miembros del tipo **Department** en el modelo de almacenamiento, el tipo **Department** del modelo School se ha modificado según esta asignación de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="958d0-607">Because the query only returns a subset of the members of the **Department** type in the storage model, the **Department** type in the School model has been modified based on this mapping as follows:</span></span>

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

### <a name="example"></a><span data-ttu-id="958d0-608">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-608">Example</span></span>

<span data-ttu-id="958d0-609">En el ejemplo siguiente se muestra el elemento **QueryView** como elemento secundario de un elemento **AssociationSetMapping** y se define una asignación de solo lectura para la `FK_Course_Department` asociación en el modelo School.</span><span class="sxs-lookup"><span data-stu-id="958d0-609">The next example shows the **QueryView** element as the child of an **AssociationSetMapping** element and defines a read-only mapping for the `FK_Course_Department` association in the School model.</span></span>

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
 
### <a name="comments"></a><span data-ttu-id="958d0-610">Comentarios</span><span class="sxs-lookup"><span data-stu-id="958d0-610">Comments</span></span>

<span data-ttu-id="958d0-611">Puede definir vistas de consulta para habilitar los escenarios siguientes:</span><span class="sxs-lookup"><span data-stu-id="958d0-611">You can define query views to enable the following scenarios:</span></span>

-   <span data-ttu-id="958d0-612">Defina una entidad en el modelo conceptual que no incluya todas las propiedades de la entidad en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-612">Define an entity in the conceptual model that doesn't include all the properties of the entity in the storage model.</span></span> <span data-ttu-id="958d0-613">Esto incluye propiedades que no tienen valores predeterminados y no admiten valores **null** .</span><span class="sxs-lookup"><span data-stu-id="958d0-613">This includes properties that do not have default values and do not support **null** values.</span></span>
-   <span data-ttu-id="958d0-614">Asigne las columnas calculadas del modelo de almacenamiento a las propiedades de los tipos de entidad del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="958d0-614">Map computed columns in the storage model to properties of entity types in the conceptual model.</span></span>
-   <span data-ttu-id="958d0-615">Defina una asignación en la que las condiciones utilizadas para dividir las entidades del modelo conceptual no se basen en la igualdad.</span><span class="sxs-lookup"><span data-stu-id="958d0-615">Define a mapping where conditions used to partition entities in the conceptual model are not based on equality.</span></span> <span data-ttu-id="958d0-616">Cuando se especifica una asignación condicional mediante el elemento **Condition** , la condición proporcionada debe ser igual al valor especificado.</span><span class="sxs-lookup"><span data-stu-id="958d0-616">When you specify a conditional mapping using the **Condition** element, the supplied condition must equal the specified value.</span></span> <span data-ttu-id="958d0-617">Para obtener más información, vea condition (elemento) (MSL).</span><span class="sxs-lookup"><span data-stu-id="958d0-617">For more information, see Condition Element (MSL).</span></span>
-   <span data-ttu-id="958d0-618">Asigne la misma columna en el modelo de almacenamiento a varios tipos en el modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="958d0-618">Map the same column in the storage model to multiple types in the conceptual model.</span></span>
-   <span data-ttu-id="958d0-619">Asigne varios tipos a la misma tabla.</span><span class="sxs-lookup"><span data-stu-id="958d0-619">Map multiple types to the same table.</span></span>
-   <span data-ttu-id="958d0-620">Defina asociaciones en el modelo conceptual que no se basen en claves externas en el esquema relacional.</span><span class="sxs-lookup"><span data-stu-id="958d0-620">Define associations in the conceptual model that are not based on foreign keys in the relational schema.</span></span>
-   <span data-ttu-id="958d0-621">Utilice la lógica de negocios personalizada para establecer el valor de las propiedades del modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="958d0-621">Use custom business logic to set the value of properties in the conceptual model.</span></span> <span data-ttu-id="958d0-622">Por ejemplo, podría asignar el valor de cadena "T" en el origen de datos a un valor **true**, un valor booleano, en el modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="958d0-622">For example, you could map the string value "T" in the data source to a value of **true**, a Boolean, in the conceptual model.</span></span>
-   <span data-ttu-id="958d0-623">Defina filtros condicionales para los resultados de la consulta.</span><span class="sxs-lookup"><span data-stu-id="958d0-623">Define conditional filters for query results.</span></span>
-   <span data-ttu-id="958d0-624">Aplique menos restricciones en los datos del modelo conceptual que en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-624">Enforce fewer restrictions on data in the conceptual model than in the storage model.</span></span> <span data-ttu-id="958d0-625">Por ejemplo, podría hacer que una propiedad en el modelo conceptual acepte valores NULL incluso si la columna a la que está asignada no admite valores **null**.</span><span class="sxs-lookup"><span data-stu-id="958d0-625">For example, you could make a property in the conceptual model nullable even if the column to which it is mapped does not support **null**values.</span></span>

<span data-ttu-id="958d0-626">Las consideraciones siguientes se aplican al definir las vistas de consulta para las entidades:</span><span class="sxs-lookup"><span data-stu-id="958d0-626">The following considerations apply when you define query views for entities:</span></span>

-   <span data-ttu-id="958d0-627">Las vistas de consulta son de solo lectura.</span><span class="sxs-lookup"><span data-stu-id="958d0-627">Query views are read-only.</span></span> <span data-ttu-id="958d0-628">Solo puede realizar las actualizaciones a las entidades utilizando las funciones de modificación.</span><span class="sxs-lookup"><span data-stu-id="958d0-628">You can only make updates to entities by using modification functions.</span></span>
-   <span data-ttu-id="958d0-629">Al definir un tipo de entidad mediante una vista de consulta, también debe definir todas las entidades relacionadas mediante vistas de consulta.</span><span class="sxs-lookup"><span data-stu-id="958d0-629">When you define an entity type by a query view, you must also define all related entities by query views.</span></span>
-   <span data-ttu-id="958d0-630">Al asignar una asociación varios a varios a una entidad en el modelo de almacenamiento que representa una tabla de vínculos en el esquema relacional, debe definir un elemento **QueryView** en el elemento **AssociationSetMapping** para esta tabla de vínculos.</span><span class="sxs-lookup"><span data-stu-id="958d0-630">When you map a many-to-many association to an entity in the storage model that represents a link table in the relational schema, you must define a **QueryView** element in the **AssociationSetMapping** element for this link table.</span></span>
-   <span data-ttu-id="958d0-631">Las vistas de consulta se deben definir para todos los tipos de una jerarquía de tipos.</span><span class="sxs-lookup"><span data-stu-id="958d0-631">Query views must be defined for all types in a type hierarchy.</span></span> <span data-ttu-id="958d0-632">Puede hacer esto de las siguientes maneras:</span><span class="sxs-lookup"><span data-stu-id="958d0-632">You can do this in the following ways:</span></span>
-   -   <span data-ttu-id="958d0-633">Con un único elemento **QueryView** que especifica una única Entity SQL consulta que devuelve una Unión de todos los tipos de entidad de la jerarquía.</span><span class="sxs-lookup"><span data-stu-id="958d0-633">With a single **QueryView** element that specifies a single Entity SQL query that returns a union of all of the entity types in the hierarchy.</span></span>
    -   <span data-ttu-id="958d0-634">Con un único elemento **QueryView** que especifica una única Entity SQL consulta que usa el operador Case para devolver un tipo de entidad específico en la jerarquía basándose en una condición específica.</span><span class="sxs-lookup"><span data-stu-id="958d0-634">With a single **QueryView** element that specifies a single Entity SQL query that uses the CASE operator to return a specific entity type in the hierarchy based on a specific condition.</span></span>
    -   <span data-ttu-id="958d0-635">Con un elemento **QueryView** adicional para un tipo específico en la jerarquía.</span><span class="sxs-lookup"><span data-stu-id="958d0-635">With an additional **QueryView** element for a specific type in the hierarchy.</span></span> <span data-ttu-id="958d0-636">En este caso, use el atributo **TypeName** del elemento **QueryView** para especificar el tipo de entidad para cada vista.</span><span class="sxs-lookup"><span data-stu-id="958d0-636">In this case, use the **TypeName** attribute of the **QueryView** element to specify the entity type for each view.</span></span>
-   <span data-ttu-id="958d0-637">Cuando se define una vista de consulta, no se puede especificar el atributo **StorageSetName** en el elemento **EntitySetMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-637">When a query view is defined, you cannot specify the **StorageSetName** attribute on the **EntitySetMapping** element.</span></span>
-   <span data-ttu-id="958d0-638">Cuando se define una vista de consulta, el elemento **EntitySetMapping**no puede contener también asignaciones de **propiedades** .</span><span class="sxs-lookup"><span data-stu-id="958d0-638">When a query view is defined, the **EntitySetMapping**element cannot also contain **Property** mappings.</span></span>

## <a name="resultbinding-element-msl"></a><span data-ttu-id="958d0-639">ResultBinding (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-639">ResultBinding Element (MSL)</span></span>

<span data-ttu-id="958d0-640">El elemento **ResultBinding** del lenguaje de especificación de asignaciones (MSL) asigna los valores de columna devueltos por los procedimientos almacenados a las propiedades de entidad del modelo conceptual cuando las funciones de modificación de tipo de entidad se asignan a los procedimientos almacenados en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-640">The **ResultBinding** element in mapping specification language (MSL) maps column values that are returned by stored procedures to entity properties in the conceptual model when entity type modification functions are mapped to stored procedures in the underlying database.</span></span> <span data-ttu-id="958d0-641">Por ejemplo, cuando un procedimiento almacenado de inserción devuelve el valor de una columna de identidad, el elemento **ResultBinding** asigna el valor devuelto a una propiedad de tipo de entidad en el modelo conceptual.</span><span class="sxs-lookup"><span data-stu-id="958d0-641">For example, when the value of an identity column is returned by an insert stored procedure, the **ResultBinding** element maps the returned value to an entity type property in the conceptual model.</span></span>

<span data-ttu-id="958d0-642">El elemento **ResultBinding** puede ser secundario del elemento InsertFunction o del elemento UpdateFunction.</span><span class="sxs-lookup"><span data-stu-id="958d0-642">The **ResultBinding** element can be child of the InsertFunction element or the UpdateFunction element.</span></span>

<span data-ttu-id="958d0-643">El elemento **ResultBinding** no puede tener elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="958d0-643">The **ResultBinding** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-644">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-644">Applicable Attributes</span></span>

<span data-ttu-id="958d0-645">En la tabla siguiente se describen los atributos aplicables al elemento **ResultBinding** :</span><span class="sxs-lookup"><span data-stu-id="958d0-645">The following table describes the attributes that are applicable to the **ResultBinding** element:</span></span>

| <span data-ttu-id="958d0-646">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-646">Attribute Name</span></span> | <span data-ttu-id="958d0-647">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-647">Is Required</span></span> | <span data-ttu-id="958d0-648">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-648">Value</span></span>                                                                         |
|:---------------|:------------|:------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-649">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="958d0-649">**Name**</span></span>       | <span data-ttu-id="958d0-650">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-650">Yes</span></span>         | <span data-ttu-id="958d0-651">El nombre de la propiedad de entidad del modelo conceptual que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-651">The name of the entity property in the conceptual model that is being mapped.</span></span> |
| <span data-ttu-id="958d0-652">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="958d0-652">**ColumnName**</span></span> | <span data-ttu-id="958d0-653">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-653">Yes</span></span>         | <span data-ttu-id="958d0-654">El nombre de la columna que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-654">The name of the column being mapped.</span></span>                                          |

### <a name="example"></a><span data-ttu-id="958d0-655">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-655">Example</span></span>

<span data-ttu-id="958d0-656">El ejemplo siguiente se basa en el modelo School y muestra un elemento **InsertFunction** que se usa para asignar la función de inserción del tipo de entidad **Person** al procedimiento almacenado **InsertPerson** .</span><span class="sxs-lookup"><span data-stu-id="958d0-656">The following example is based on the School model and shows an **InsertFunction** element used to map the insert function of the **Person** entity type to the **InsertPerson** stored procedure.</span></span> <span data-ttu-id="958d0-657">(El procedimiento almacenado **InsertPerson** se muestra a continuación y se declara en el modelo de almacenamiento). Se usa un elemento **ResultBinding** para asignar un valor de columna devuelto por el procedimiento almacenado (**NewPersonID**) a una propiedad de tipo de entidad (**PersonID**).</span><span class="sxs-lookup"><span data-stu-id="958d0-657">(The **InsertPerson** stored procedure is shown below and is declared in the storage model.) A **ResultBinding** element is used to map a column value that is returned by the stored procedure (**NewPersonID**) to an entity type property (**PersonID**).</span></span>

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

<span data-ttu-id="958d0-658">En el siguiente código de Transact-SQL se describe el procedimiento almacenado **InsertPerson** :</span><span class="sxs-lookup"><span data-stu-id="958d0-658">The following Transact-SQL describes the **InsertPerson** stored procedure:</span></span>

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

## <a name="resultmapping-element-msl"></a><span data-ttu-id="958d0-659">ResultMapping (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-659">ResultMapping Element (MSL)</span></span>

<span data-ttu-id="958d0-660">El elemento **ResultMapping** del lenguaje de especificación de asignaciones (MSL) define la asignación entre una importación de función en el modelo conceptual y un procedimiento almacenado en la base de datos subyacente cuando se cumplen las condiciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="958d0-660">The **ResultMapping** element in mapping specification language (MSL) defines the mapping between a function import in the conceptual model and a stored procedure in the underlying database when the following are true:</span></span>

-   <span data-ttu-id="958d0-661">La importación de función devuelve un tipo de entidad del modelo conceptual o un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="958d0-661">The function import returns a conceptual model entity type or complex type.</span></span>
-   <span data-ttu-id="958d0-662">Los nombres de las columnas devueltas por el procedimiento almacenado no coinciden exactamente con los nombres de las propiedades del tipo de entidad o el tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="958d0-662">The names of the columns returned by the stored procedure do not exactly match the names of the properties on the entity type or complex type.</span></span>

<span data-ttu-id="958d0-663">De forma predeterminada, la asignación entre las columnas devueltas por un procedimiento almacenado y un tipo de entidad o un tipo complejo se basa en los nombres de las propiedades y de las columnas.</span><span class="sxs-lookup"><span data-stu-id="958d0-663">By default, the mapping between the columns returned by a stored procedure and an entity type or complex type is based on column and property names.</span></span> <span data-ttu-id="958d0-664">Si los nombres de columna no coinciden exactamente con los nombres de propiedad, debe utilizar el elemento **ResultMapping** para definir la asignación.</span><span class="sxs-lookup"><span data-stu-id="958d0-664">If column names do not exactly match property names, you must use the **ResultMapping** element to define the mapping.</span></span> <span data-ttu-id="958d0-665">Para obtener un ejemplo de la asignación predeterminada, vea FunctionImportMapping (elemento) (MSL).</span><span class="sxs-lookup"><span data-stu-id="958d0-665">For an example of the default mapping, see FunctionImportMapping Element (MSL).</span></span>

<span data-ttu-id="958d0-666">El elemento **ResultMapping** es un elemento secundario del elemento FunctionImportMapping.</span><span class="sxs-lookup"><span data-stu-id="958d0-666">The **ResultMapping** element is a child element of the FunctionImportMapping element.</span></span>

<span data-ttu-id="958d0-667">El elemento **ResultMapping** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="958d0-667">The **ResultMapping** element can have the following child elements:</span></span>

-   <span data-ttu-id="958d0-668">EntityTypeMapping (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-668">EntityTypeMapping (zero or more)</span></span>
-   <span data-ttu-id="958d0-669">ComplexTypeMapping</span><span class="sxs-lookup"><span data-stu-id="958d0-669">ComplexTypeMapping</span></span>

<span data-ttu-id="958d0-670">No hay atributos aplicables al elemento **ResultMapping** .</span><span class="sxs-lookup"><span data-stu-id="958d0-670">No attributes are applicable to the **ResultMapping** Element.</span></span>

### <a name="example"></a><span data-ttu-id="958d0-671">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-671">Example</span></span>

<span data-ttu-id="958d0-672">Observe el siguiente procedimiento almacenado:</span><span class="sxs-lookup"><span data-stu-id="958d0-672">Consider the following stored procedure:</span></span>

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

<span data-ttu-id="958d0-673">Considere también el siguiente tipo de entidad del modelo conceptual:</span><span class="sxs-lookup"><span data-stu-id="958d0-673">Also consider the following conceptual model entity type:</span></span>

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

<span data-ttu-id="958d0-674">Para crear una importación de función que devuelva instancias del tipo de entidad anterior, la asignación entre las columnas devueltas por el procedimiento almacenado y el tipo de entidad debe definirse en un elemento **ResultMapping** :</span><span class="sxs-lookup"><span data-stu-id="958d0-674">In order to create a function import that returns instances of the previous entity type, the mapping between the columns returned by the stored procedure and the entity type must be defined in a **ResultMapping** element:</span></span>

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

## <a name="scalarproperty-element-msl"></a><span data-ttu-id="958d0-675">ScalarProperty (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-675">ScalarProperty Element (MSL)</span></span>

<span data-ttu-id="958d0-676">El elemento **ScalarProperty** del lenguaje de especificación de asignaciones (MSL) asigna una propiedad en un tipo de entidad del modelo conceptual, un tipo complejo o una asociación a una columna de tabla o a un parámetro de procedimiento almacenado en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-676">The **ScalarProperty** element in mapping specification language (MSL) maps a property on a conceptual model entity type, complex type, or association to a table column or stored procedure parameter in the underlying database.</span></span>

> [!NOTE]
> <span data-ttu-id="958d0-677">Los procedimientos almacenados a los que están asignados las funciones de modificación se deben declarar en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-677">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="958d0-678">Para obtener más información, vea elemento function (SSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-678">For more information, see Function Element (SSDL).</span></span>

<span data-ttu-id="958d0-679">El elemento **ScalarProperty** puede ser un elemento secundario de los siguientes elementos:</span><span class="sxs-lookup"><span data-stu-id="958d0-679">The **ScalarProperty** element can be a child of the following elements:</span></span>

-   <span data-ttu-id="958d0-680">MappingFragment</span><span class="sxs-lookup"><span data-stu-id="958d0-680">MappingFragment</span></span>
-   <span data-ttu-id="958d0-681">InsertFunction</span><span class="sxs-lookup"><span data-stu-id="958d0-681">InsertFunction</span></span>
-   <span data-ttu-id="958d0-682">UpdateFunction</span><span class="sxs-lookup"><span data-stu-id="958d0-682">UpdateFunction</span></span>
-   <span data-ttu-id="958d0-683">DeleteFunction</span><span class="sxs-lookup"><span data-stu-id="958d0-683">DeleteFunction</span></span>
-   <span data-ttu-id="958d0-684">EndProperty</span><span class="sxs-lookup"><span data-stu-id="958d0-684">EndProperty</span></span>
-   <span data-ttu-id="958d0-685">ComplexProperty</span><span class="sxs-lookup"><span data-stu-id="958d0-685">ComplexProperty</span></span>
-   <span data-ttu-id="958d0-686">ResultMapping</span><span class="sxs-lookup"><span data-stu-id="958d0-686">ResultMapping</span></span>

<span data-ttu-id="958d0-687">Como elemento secundario del elemento **MappingFragment**, **ComplexProperty**o **EndProperty** , el elemento **ScalarProperty** asigna una propiedad del modelo conceptual a una columna de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="958d0-687">As a child of the **MappingFragment**, **ComplexProperty**, or **EndProperty** element, the **ScalarProperty** element maps a property in the conceptual model to a column in the database.</span></span> <span data-ttu-id="958d0-688">Como elemento secundario del elemento **InsertFunction**, **UpdateFunction**o **DeleteFunction** , el elemento **ScalarProperty** asigna una propiedad del modelo conceptual a un parámetro de procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="958d0-688">As a child of the **InsertFunction**, **UpdateFunction**, or **DeleteFunction** element, the **ScalarProperty** element maps a property in the conceptual model to a stored procedure parameter.</span></span>

<span data-ttu-id="958d0-689">El elemento **ScalarProperty** no puede tener elementos secundarios.</span><span class="sxs-lookup"><span data-stu-id="958d0-689">The **ScalarProperty** element cannot have any child elements.</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-690">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-690">Applicable Attributes</span></span>

<span data-ttu-id="958d0-691">Los atributos que se aplican al elemento **ScalarProperty** difieren en función del rol del elemento.</span><span class="sxs-lookup"><span data-stu-id="958d0-691">The attributes that apply to the **ScalarProperty** element differ depending on the role of the element.</span></span>

<span data-ttu-id="958d0-692">En la tabla siguiente se describen los atributos que se pueden aplicar cuando el elemento **ScalarProperty** se utiliza para asignar una propiedad del modelo conceptual a una columna de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="958d0-692">The following table describes the attributes that are applicable when the **ScalarProperty** element is used to map a conceptual model property to a column in the database:</span></span>

| <span data-ttu-id="958d0-693">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-693">Attribute Name</span></span> | <span data-ttu-id="958d0-694">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-694">Is Required</span></span> | <span data-ttu-id="958d0-695">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-695">Value</span></span>                                                           |
|:---------------|:------------|:----------------------------------------------------------------|
| <span data-ttu-id="958d0-696">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="958d0-696">**Name**</span></span>       | <span data-ttu-id="958d0-697">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-697">Yes</span></span>         | <span data-ttu-id="958d0-698">El nombre de la propiedad del modelo conceptual que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-698">The name of the conceptual model property that is being mapped.</span></span> |
| <span data-ttu-id="958d0-699">**ColumnName**</span><span class="sxs-lookup"><span data-stu-id="958d0-699">**ColumnName**</span></span> | <span data-ttu-id="958d0-700">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-700">Yes</span></span>         | <span data-ttu-id="958d0-701">El nombre de la columna de tabla que se está asociando.</span><span class="sxs-lookup"><span data-stu-id="958d0-701">The name of the table column that is being mapped.</span></span>              |

<span data-ttu-id="958d0-702">En la tabla siguiente se describen los atributos aplicables al elemento **ScalarProperty** cuando se utiliza para asignar una propiedad del modelo conceptual a un parámetro de procedimiento almacenado:</span><span class="sxs-lookup"><span data-stu-id="958d0-702">The following table describes the attributes that are applicable to the **ScalarProperty** element when it is used to map a conceptual model property to a stored procedure parameter:</span></span>

| <span data-ttu-id="958d0-703">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-703">Attribute Name</span></span>    | <span data-ttu-id="958d0-704">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-704">Is Required</span></span> | <span data-ttu-id="958d0-705">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-705">Value</span></span>                                                                                                                                           |
|:------------------|:------------|:------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-706">**Nombre**</span><span class="sxs-lookup"><span data-stu-id="958d0-706">**Name**</span></span>          | <span data-ttu-id="958d0-707">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-707">Yes</span></span>         | <span data-ttu-id="958d0-708">El nombre de la propiedad del modelo conceptual que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-708">The name of the conceptual model property that is being mapped.</span></span>                                                                                 |
| <span data-ttu-id="958d0-709">**ParameterName**</span><span class="sxs-lookup"><span data-stu-id="958d0-709">**ParameterName**</span></span> | <span data-ttu-id="958d0-710">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-710">Yes</span></span>         | <span data-ttu-id="958d0-711">El nombre del parámetro que se está asignando.</span><span class="sxs-lookup"><span data-stu-id="958d0-711">The name of the parameter that is being mapped.</span></span>                                                                                                 |
| <span data-ttu-id="958d0-712">**Versión**</span><span class="sxs-lookup"><span data-stu-id="958d0-712">**Version**</span></span>       | <span data-ttu-id="958d0-713">No</span><span class="sxs-lookup"><span data-stu-id="958d0-713">No</span></span>          | <span data-ttu-id="958d0-714">**Actual** o **original** , dependiendo de si el valor actual o el valor original de la propiedad se deben usar para las comprobaciones de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="958d0-714">**Current** or **Original** depending on whether the current value or the original value of the property should be used for concurrency checks.</span></span> |

### <a name="example"></a><span data-ttu-id="958d0-715">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-715">Example</span></span>

<span data-ttu-id="958d0-716">En el ejemplo siguiente se muestra el elemento **ScalarProperty** utilizado de dos maneras:</span><span class="sxs-lookup"><span data-stu-id="958d0-716">The following example shows the **ScalarProperty** element used in two ways:</span></span>

-   <span data-ttu-id="958d0-717">Para asignar las propiedades del tipo de entidad **Person** a las columnas de la tabla **Person**.</span><span class="sxs-lookup"><span data-stu-id="958d0-717">To map the properties of the **Person** entity type to the columns of the **Person**table.</span></span>
-   <span data-ttu-id="958d0-718">Para asignar las propiedades del tipo de entidad **Person** a los parámetros del procedimiento almacenado **UpdatePerson** .</span><span class="sxs-lookup"><span data-stu-id="958d0-718">To map the properties of the **Person** entity type to the parameters of the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="958d0-719">Los procedimientos almacenados se declaran en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-719">The stored procedures are declared in the storage model.</span></span>

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

### <a name="example"></a><span data-ttu-id="958d0-720">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-720">Example</span></span>

<span data-ttu-id="958d0-721">En el ejemplo siguiente se muestra el elemento **ScalarProperty** que se usa para asignar las funciones de inserción y eliminación de una asociación del modelo conceptual a los procedimientos almacenados en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="958d0-721">The next example shows the **ScalarProperty** element used to map the insert and delete functions of a conceptual model association to stored procedures in the database.</span></span> <span data-ttu-id="958d0-722">Los procedimientos almacenados se declaran en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-722">The stored procedures are declared in the storage model.</span></span>

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

## <a name="updatefunction-element-msl"></a><span data-ttu-id="958d0-723">UpdateFunction (Elemento) (MSL)</span><span class="sxs-lookup"><span data-stu-id="958d0-723">UpdateFunction Element (MSL)</span></span>

<span data-ttu-id="958d0-724">El elemento **UpdateFunction** del lenguaje de especificación de asignaciones (MSL) asigna la función de actualización de un tipo de entidad del modelo conceptual a un procedimiento almacenado en la base de datos subyacente.</span><span class="sxs-lookup"><span data-stu-id="958d0-724">The **UpdateFunction** element in mapping specification language (MSL) maps the update function of an entity type in the conceptual model to a stored procedure in the underlying database.</span></span> <span data-ttu-id="958d0-725">Los procedimientos almacenados a los que están asignados las funciones de modificación se deben declarar en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-725">Stored procedures to which modification functions are mapped must be declared in the storage model.</span></span> <span data-ttu-id="958d0-726">Para obtener más información, vea elemento function (SSDL).</span><span class="sxs-lookup"><span data-stu-id="958d0-726">For more information, see Function Element (SSDL).</span></span>

> [!NOTE]
>  <span data-ttu-id="958d0-727">Si no asigna las tres operaciones de inserción, actualización o eliminación de un tipo de entidad a los procedimientos almacenados, se producirá un error en las operaciones no asignadas si se ejecutan en tiempo de ejecución y se genera un UpdateException.</span><span class="sxs-lookup"><span data-stu-id="958d0-727">If you do not map all three of the insert, update, or delete operations of a entity type to stored procedures, the unmapped operations will fail if executed at runtime and an UpdateException is thrown.</span></span>

<span data-ttu-id="958d0-728">El elemento **UpdateFunction** puede ser un elemento secundario del elemento ModificationFunctionMapping y aplicarse al elemento EntityTypeMapping.</span><span class="sxs-lookup"><span data-stu-id="958d0-728">The **UpdateFunction** element can be a child of the ModificationFunctionMapping element and applied to the EntityTypeMapping element.</span></span>

<span data-ttu-id="958d0-729">El elemento **UpdateFunction** puede tener los siguientes elementos secundarios:</span><span class="sxs-lookup"><span data-stu-id="958d0-729">The **UpdateFunction** element can have the following child elements:</span></span>

-   <span data-ttu-id="958d0-730">AssociationEnd (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-730">AssociationEnd (zero or more)</span></span>
-   <span data-ttu-id="958d0-731">ComplexProperty (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-731">ComplexProperty (zero or more)</span></span>
-   <span data-ttu-id="958d0-732">ResultBinding (cero o uno)</span><span class="sxs-lookup"><span data-stu-id="958d0-732">ResultBinding (zero or one)</span></span>
-   <span data-ttu-id="958d0-733">ScalarProperty (cero o más)</span><span class="sxs-lookup"><span data-stu-id="958d0-733">ScarlarProperty (zero or more)</span></span>

### <a name="applicable-attributes"></a><span data-ttu-id="958d0-734">Atributos aplicables</span><span class="sxs-lookup"><span data-stu-id="958d0-734">Applicable Attributes</span></span>

<span data-ttu-id="958d0-735">En la tabla siguiente se describen los atributos que se pueden aplicar al elemento **UpdateFunction** .</span><span class="sxs-lookup"><span data-stu-id="958d0-735">The following table describes the attributes that can be applied to the **UpdateFunction** element.</span></span>

| <span data-ttu-id="958d0-736">Nombre del atributo</span><span class="sxs-lookup"><span data-stu-id="958d0-736">Attribute Name</span></span>            | <span data-ttu-id="958d0-737">Es obligatorio</span><span class="sxs-lookup"><span data-stu-id="958d0-737">Is Required</span></span> | <span data-ttu-id="958d0-738">Value</span><span class="sxs-lookup"><span data-stu-id="958d0-738">Value</span></span>                                                                                                                                                    |
|:--------------------------|:------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="958d0-739">**Nombrefunción**</span><span class="sxs-lookup"><span data-stu-id="958d0-739">**FunctionName**</span></span>          | <span data-ttu-id="958d0-740">Sí</span><span class="sxs-lookup"><span data-stu-id="958d0-740">Yes</span></span>         | <span data-ttu-id="958d0-741">El nombre completo, calificado con el espacio de nombres, del procedimiento almacenado al que la función de actualización está asignada.</span><span class="sxs-lookup"><span data-stu-id="958d0-741">The namespace-qualified name of the stored procedure to which the update function is mapped.</span></span> <span data-ttu-id="958d0-742">El procedimiento almacenado se debe declarar en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-742">The stored procedure must be declared in the storage model.</span></span> |
| <span data-ttu-id="958d0-743">**RowsAffectedParameter**</span><span class="sxs-lookup"><span data-stu-id="958d0-743">**RowsAffectedParameter**</span></span> | <span data-ttu-id="958d0-744">No</span><span class="sxs-lookup"><span data-stu-id="958d0-744">No</span></span>          | <span data-ttu-id="958d0-745">El nombre del parámetro de salida que devuelve el número de filas afectadas.</span><span class="sxs-lookup"><span data-stu-id="958d0-745">The name of the output parameter that returns the number of rows affected.</span></span>                                                                               |

### <a name="example"></a><span data-ttu-id="958d0-746">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="958d0-746">Example</span></span>

<span data-ttu-id="958d0-747">El ejemplo siguiente se basa en el modelo School y muestra el elemento **UpdateFunction** que se usa para asignar la función de actualización del tipo de entidad **Person** al procedimiento almacenado **UpdatePerson** .</span><span class="sxs-lookup"><span data-stu-id="958d0-747">The following example is based on the School model and shows the **UpdateFunction** element used to map update function of the **Person** entity type to the **UpdatePerson** stored procedure.</span></span> <span data-ttu-id="958d0-748">El procedimiento almacenado **UpdatePerson** se declara en el modelo de almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="958d0-748">The **UpdatePerson** stored procedure is declared in the storage model.</span></span>

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
