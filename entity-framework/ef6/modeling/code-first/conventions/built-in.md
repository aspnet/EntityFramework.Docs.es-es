---
title: 'Convenciones de Code First: EF6'
description: Convenciones de Code First en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: bc644573-c2b2-4ed7-8745-3c37c41058ad
uid: ef6/modeling/code-first/conventions/built-in
ms.openlocfilehash: 78471a1d4925e57146b8e9f2f43e57b626d164b6
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617194"
---
# <a name="code-first-conventions"></a><span data-ttu-id="3fe81-103">Convenciones de Code First</span><span class="sxs-lookup"><span data-stu-id="3fe81-103">Code First Conventions</span></span>
<span data-ttu-id="3fe81-104">Code First le permite describir un modelo mediante el uso de clases de C# o Visual Basic .NET.</span><span class="sxs-lookup"><span data-stu-id="3fe81-104">Code First enables you to describe a model by using C# or Visual Basic .NET classes.</span></span> <span data-ttu-id="3fe81-105">La forma básica del modelo se detecta mediante el uso de convenciones.</span><span class="sxs-lookup"><span data-stu-id="3fe81-105">The basic shape of the model is detected by using conventions.</span></span> <span data-ttu-id="3fe81-106">Las convenciones son conjuntos de reglas que se utilizan para configurar automáticamente un modelo conceptual basado en definiciones de clase al trabajar con Code First.</span><span class="sxs-lookup"><span data-stu-id="3fe81-106">Conventions are sets of rules that are used to automatically configure a conceptual model based on class definitions when working with Code First.</span></span> <span data-ttu-id="3fe81-107">Las convenciones se definen en el espacio de nombres System. Data. Entity. ModelConfiguration. Conventions.</span><span class="sxs-lookup"><span data-stu-id="3fe81-107">The conventions are defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>  

<span data-ttu-id="3fe81-108">Puede seguir configurando el modelo con anotaciones de datos o la API fluida.</span><span class="sxs-lookup"><span data-stu-id="3fe81-108">You can further configure your model by using data annotations or the fluent API.</span></span> <span data-ttu-id="3fe81-109">Se da prioridad a la configuración a través de la API fluida, seguida de las anotaciones de datos y, a continuación, las convenciones.</span><span class="sxs-lookup"><span data-stu-id="3fe81-109">Precedence is given to configuration through the fluent API followed by data annotations and then conventions.</span></span> <span data-ttu-id="3fe81-110">Para obtener más información, vea [anotaciones de datos](xref:ef6/modeling/code-first/data-annotations), [API fluidas, relaciones](xref:ef6/modeling/code-first/fluent/relationships), [tipos de API fluidas & propiedades](xref:ef6/modeling/code-first/fluent/types-and-properties) y [API fluida con VB.net](xref:ef6/modeling/code-first/fluent/vb).</span><span class="sxs-lookup"><span data-stu-id="3fe81-110">For more information see [Data Annotations](xref:ef6/modeling/code-first/data-annotations), [Fluent API - Relationships](xref:ef6/modeling/code-first/fluent/relationships), [Fluent API - Types & Properties](xref:ef6/modeling/code-first/fluent/types-and-properties) and [Fluent API with VB.NET](xref:ef6/modeling/code-first/fluent/vb).</span></span>  

<span data-ttu-id="3fe81-111">Puede encontrar una lista detallada de las convenciones de Code First en la documentación de la [API](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span><span class="sxs-lookup"><span data-stu-id="3fe81-111">A detailed list of Code First conventions is available in the [API Documentation](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span> <span data-ttu-id="3fe81-112">En este tema se proporciona información general sobre las convenciones utilizadas por Code First.</span><span class="sxs-lookup"><span data-stu-id="3fe81-112">This topic provides an overview of the conventions used by Code First.</span></span>  

## <a name="type-discovery"></a><span data-ttu-id="3fe81-113">Detección de tipos</span><span class="sxs-lookup"><span data-stu-id="3fe81-113">Type Discovery</span></span>  

<span data-ttu-id="3fe81-114">Al usar Code First desarrollo, normalmente comienza por escribir .NET Framework clases que definen el modelo conceptual (de dominio).</span><span class="sxs-lookup"><span data-stu-id="3fe81-114">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="3fe81-115">Además de definir las clases, también debe dejar que **DbContext** sepa qué tipos quiere incluir en el modelo.</span><span class="sxs-lookup"><span data-stu-id="3fe81-115">In addition to defining the classes, you also need to let **DbContext** know which types you want to include in the model.</span></span> <span data-ttu-id="3fe81-116">Para ello, defina una clase de contexto que derive de **DbContext** y exponga propiedades **DbSet** para los tipos que desea que formen parte del modelo.</span><span class="sxs-lookup"><span data-stu-id="3fe81-116">To do this, you define a context class that derives from **DbContext** and exposes **DbSet** properties for the types that you want to be part of the model.</span></span> <span data-ttu-id="3fe81-117">Code First incluirá estos tipos y también extraerá todos los tipos a los que se hace referencia, aunque los tipos a los que se hace referencia se hayan definido en un ensamblado diferente.</span><span class="sxs-lookup"><span data-stu-id="3fe81-117">Code First will include these types and also will pull in any referenced types, even if the referenced types are defined in a different assembly.</span></span>  

<span data-ttu-id="3fe81-118">Si los tipos participan en una jerarquía de herencia, basta con definir una propiedad **DbSet** para la clase base y los tipos derivados se incluirán automáticamente, si están en el mismo ensamblado que la clase base.</span><span class="sxs-lookup"><span data-stu-id="3fe81-118">If your types participate in an inheritance hierarchy, it is enough to define a **DbSet** property for the base class, and the derived types will be automatically included, if they are in the same assembly as the base class.</span></span>  

<span data-ttu-id="3fe81-119">En el ejemplo siguiente, solo hay una propiedad **DbSet** definida en la clase **SchoolEntities** (**departments**).</span><span class="sxs-lookup"><span data-stu-id="3fe81-119">In the following example, there is only one **DbSet** property defined on the **SchoolEntities** class (**Departments**).</span></span> <span data-ttu-id="3fe81-120">Code First usa esta propiedad para detectar y extraer todos los tipos a los que se hace referencia.</span><span class="sxs-lookup"><span data-stu-id="3fe81-120">Code First uses this property to discover and pull in any referenced types.</span></span>  

``` csharp
public class SchoolEntities : DbContext
{
    public DbSet<Department> Departments { get; set; }
}

public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public string Location { get; set; }
    public string Days { get; set; }
    public System.DateTime Time { get; set; }
}
```  

<span data-ttu-id="3fe81-121">Si desea excluir un tipo del modelo, use el atributo **NotMapped** o la API fluida **DbModelBuilder. ignore** .</span><span class="sxs-lookup"><span data-stu-id="3fe81-121">If you want to exclude a type from the model, use the **NotMapped** attribute or the **DbModelBuilder.Ignore** fluent API.</span></span>  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a><span data-ttu-id="3fe81-122">Convención de clave principal</span><span class="sxs-lookup"><span data-stu-id="3fe81-122">Primary Key Convention</span></span>  

<span data-ttu-id="3fe81-123">Code First infiere que una propiedad es una clave principal si una propiedad de una clase se denomina "ID" (no distingue mayúsculas de minúsculas) o el nombre de clase seguido de "ID".</span><span class="sxs-lookup"><span data-stu-id="3fe81-123">Code First infers that a property is a primary key if a property on a class is named “ID” (not case sensitive), or the class name followed by "ID".</span></span> <span data-ttu-id="3fe81-124">Si el tipo de la propiedad de clave principal es numérico o GUID, se configurará como una columna de identidad.</span><span class="sxs-lookup"><span data-stu-id="3fe81-124">If the type of the primary key property is numeric or GUID it will be configured as an identity column.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a><span data-ttu-id="3fe81-125">Convención de relación</span><span class="sxs-lookup"><span data-stu-id="3fe81-125">Relationship Convention</span></span>  

<span data-ttu-id="3fe81-126">En Entity Framework, las propiedades de navegación proporcionan una manera de navegar por una relación entre dos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="3fe81-126">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span> <span data-ttu-id="3fe81-127">Cada objeto puede tener una propiedad de navegación para cada relación en la que participa.</span><span class="sxs-lookup"><span data-stu-id="3fe81-127">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="3fe81-128">Las propiedades de navegación permiten navegar y administrar las relaciones en ambas direcciones, devolviendo un objeto de referencia (si la multiplicidad es uno o cero o uno) o una colección (si la multiplicidad es muchas).</span><span class="sxs-lookup"><span data-stu-id="3fe81-128">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="3fe81-129">Code First deduce las relaciones basadas en las propiedades de navegación definidas en los tipos.</span><span class="sxs-lookup"><span data-stu-id="3fe81-129">Code First infers relationships based on the navigation properties defined on your types.</span></span>  

<span data-ttu-id="3fe81-130">Además de las propiedades de navegación, se recomienda incluir las propiedades de clave externa en los tipos que representan los objetos dependientes.</span><span class="sxs-lookup"><span data-stu-id="3fe81-130">In addition to navigation properties, we recommend that you include foreign key properties on the types that represent dependent objects.</span></span> <span data-ttu-id="3fe81-131">Cualquier propiedad con el mismo tipo de datos que la propiedad de clave principal principal y con un nombre que sigue a uno de los siguientes formatos representa una clave externa para la relación: ' \<navigation property name\> \<principal primary key property name\> ', ' \<principal class name\> \<primary key property name\> ' o ' \<principal primary key property name\> '.</span><span class="sxs-lookup"><span data-stu-id="3fe81-131">Any property with the same data type as the principal primary key property and with a name that follows one of the following formats represents a foreign key for the relationship: '\<navigation property name\>\<principal primary key property name\>', '\<principal class name\>\<primary key property name\>', or '\<principal primary key property name\>'.</span></span> <span data-ttu-id="3fe81-132">Si se encuentran varias coincidencias, se da prioridad en el orden indicado anteriormente.</span><span class="sxs-lookup"><span data-stu-id="3fe81-132">If multiple matches are found then precedence is given in the order listed above.</span></span> <span data-ttu-id="3fe81-133">La detección de clave externa no distingue entre mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="3fe81-133">Foreign key detection is not case sensitive.</span></span> <span data-ttu-id="3fe81-134">Cuando se detecta una propiedad de clave externa, Code First deduce la multiplicidad de la relación en función de la nulabilidad de la clave externa.</span><span class="sxs-lookup"><span data-stu-id="3fe81-134">When a foreign key property is detected, Code First infers the multiplicity of the relationship based on the nullability of the foreign key.</span></span> <span data-ttu-id="3fe81-135">Si la propiedad admite valores NULL, la relación se registra como opcional; de lo contrario, la relación se registra según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="3fe81-135">If the property is nullable then the relationship is registered as optional; otherwise the relationship is registered as required.</span></span>  

<span data-ttu-id="3fe81-136">Si una clave externa de la entidad dependiente no admite valores NULL, Code First establece Cascade delete en la relación.</span><span class="sxs-lookup"><span data-stu-id="3fe81-136">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="3fe81-137">Si una clave externa de la entidad dependiente admite valores NULL, Code First no establece Cascade delete en la relación y, cuando se elimina la entidad de seguridad, la clave externa se establecerá en NULL.</span><span class="sxs-lookup"><span data-stu-id="3fe81-137">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span> <span data-ttu-id="3fe81-138">El comportamiento de la multiplicidad y la eliminación en cascada detectados por la Convención se pueden invalidar mediante la API fluida.</span><span class="sxs-lookup"><span data-stu-id="3fe81-138">The multiplicity and cascade delete behavior detected by convention can be overridden by using the fluent API.</span></span>  

<span data-ttu-id="3fe81-139">En el ejemplo siguiente se usan las propiedades de navegación y una clave externa para definir la relación entre las clases Department y Course.</span><span class="sxs-lookup"><span data-stu-id="3fe81-139">In the following example the navigation properties and a foreign key are used to define the relationship between the Department and Course classes.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}
```  

> [!NOTE]
> <span data-ttu-id="3fe81-140">Si tiene varias relaciones entre los mismos tipos (por ejemplo, supongamos que define las clases **Person** y **book** , donde la clase **Person** contiene las propiedades de navegación **ReviewedBooks** y **AuthoredBooks** y la clase **book** contiene las propiedades de navegación **autor** y **Revisor** ), debe configurar manualmente las relaciones mediante anotaciones de datos o la API fluida.</span><span class="sxs-lookup"><span data-stu-id="3fe81-140">If you have multiple relationships between the same types (for example, suppose you define the **Person** and **Book** classes, where the **Person** class contains the **ReviewedBooks** and **AuthoredBooks** navigation properties and the **Book** class contains the **Author** and **Reviewer** navigation properties) you need to manually configure the relationships by using Data Annotations or the fluent API.</span></span> <span data-ttu-id="3fe81-141">Para obtener más información, vea [anotaciones de datos: relaciones](xref:ef6/modeling/code-first/data-annotations) y [API fluida](xref:ef6/modeling/code-first/fluent/relationships).</span><span class="sxs-lookup"><span data-stu-id="3fe81-141">For more information, see [Data Annotations - Relationships](xref:ef6/modeling/code-first/data-annotations) and [Fluent API - Relationships](xref:ef6/modeling/code-first/fluent/relationships).</span></span>  

## <a name="complex-types-convention"></a><span data-ttu-id="3fe81-142">Convención de tipos complejos</span><span class="sxs-lookup"><span data-stu-id="3fe81-142">Complex Types Convention</span></span>  

<span data-ttu-id="3fe81-143">Cuando Code First detecta una definición de clase en la que no se puede inferir una clave principal y no se registra ninguna clave principal a través de anotaciones de datos o la API fluida, el tipo se registra automáticamente como un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="3fe81-143">When Code First discovers a class definition where a primary key cannot be inferred, and no primary key is registered through data annotations or the fluent API, then the type is automatically registered as a complex type.</span></span> <span data-ttu-id="3fe81-144">La detección de tipos complejos también requiere que el tipo no tenga propiedades que hagan referencia a los tipos de entidad y no se haga referencia a ellos desde una propiedad de colección en otro tipo.</span><span class="sxs-lookup"><span data-stu-id="3fe81-144">Complex type detection also requires that the type does not have properties that reference entity types and is not referenced from a collection property on another type.</span></span> <span data-ttu-id="3fe81-145">Dadas las siguientes definiciones de clase Code First deduciría que los **detalles** son un tipo complejo porque no tiene ninguna clave principal.</span><span class="sxs-lookup"><span data-stu-id="3fe81-145">Given the following class definitions Code First would infer that **Details** is a complex type because it has no primary key.</span></span>  

``` csharp
public partial class OnsiteCourse : Course
{
    public OnsiteCourse()
    {
        Details = new Details();
    }

    public Details Details { get; set; }
}

public class Details
{
    public System.DateTime Time { get; set; }
    public string Location { get; set; }
    public string Days { get; set; }
}
```  

## <a name="connection-string-convention"></a><span data-ttu-id="3fe81-146">Convención de cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="3fe81-146">Connection String Convention</span></span>  

<span data-ttu-id="3fe81-147">Para obtener información sobre las convenciones que usa DbContext para detectar la conexión que se va a usar [, consulte conexiones y modelos](xref:ef6/fundamentals/configuring/connection-strings).</span><span class="sxs-lookup"><span data-stu-id="3fe81-147">To learn about the conventions that DbContext uses to discover the connection to use see [Connections and Models](xref:ef6/fundamentals/configuring/connection-strings).</span></span>  

## <a name="removing-conventions"></a><span data-ttu-id="3fe81-148">Quitar convenciones</span><span class="sxs-lookup"><span data-stu-id="3fe81-148">Removing Conventions</span></span>  

<span data-ttu-id="3fe81-149">Puede quitar cualquiera de las convenciones definidas en el espacio de nombres System. Data. Entity. ModelConfiguration. Conventions.</span><span class="sxs-lookup"><span data-stu-id="3fe81-149">You can remove any of the conventions defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span> <span data-ttu-id="3fe81-150">En el ejemplo siguiente se quita **PluralizingTableNameConvention**.</span><span class="sxs-lookup"><span data-stu-id="3fe81-150">The following example removes **PluralizingTableNameConvention**.</span></span>  

``` csharp
public class SchoolEntities : DbContext
{
     . . .

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention, the generated tables  
        // will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}
```  

## <a name="custom-conventions"></a><span data-ttu-id="3fe81-151">Convenciones personalizadas</span><span class="sxs-lookup"><span data-stu-id="3fe81-151">Custom Conventions</span></span>  

<span data-ttu-id="3fe81-152">Las convenciones personalizadas se admiten en EF6 en adelante.</span><span class="sxs-lookup"><span data-stu-id="3fe81-152">Custom conventions are supported in EF6 onwards.</span></span> <span data-ttu-id="3fe81-153">Para obtener más información, vea [convenciones de Code First personalizadas](xref:ef6/modeling/code-first/conventions/custom).</span><span class="sxs-lookup"><span data-stu-id="3fe81-153">For more information see [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span></span>
