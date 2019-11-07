---
title: 'Convenciones basadas en modelos: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: 0fc4eef8-29b8-4192-9c77-08fd33d3db3a
ms.openlocfilehash: c873e9a216bd9bd1934f2149ae6af602072f3608
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73656168"
---
# <a name="model-based-conventions"></a><span data-ttu-id="0453b-102">Convenciones basadas en modelos</span><span class="sxs-lookup"><span data-stu-id="0453b-102">Model-Based Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="0453b-103">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="0453b-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="0453b-104">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="0453b-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="0453b-105">Las convenciones basadas en modelos son un método avanzado de configuración del modelo basado en convenciones.</span><span class="sxs-lookup"><span data-stu-id="0453b-105">Model based conventions are an advanced method of convention based model configuration.</span></span> <span data-ttu-id="0453b-106">En la mayoría de los casos, se debe usar la API de la [Convención de Code First personalizada en DbModelBuilder](~/ef6/modeling/code-first/conventions/custom.md) .</span><span class="sxs-lookup"><span data-stu-id="0453b-106">For most scenarios the [Custom Code First Convention API on DbModelBuilder](~/ef6/modeling/code-first/conventions/custom.md) should be used.</span></span> <span data-ttu-id="0453b-107">Antes de usar las convenciones basadas en modelos, se recomienda comprender la API de DbModelBuilder para las convenciones.</span><span class="sxs-lookup"><span data-stu-id="0453b-107">An understanding of the DbModelBuilder API for conventions is recommended before using model based conventions.</span></span>  

<span data-ttu-id="0453b-108">Las convenciones basadas en modelos permiten la creación de convenciones que afectan a las propiedades y tablas que no se pueden configurar a través de convenciones estándar.</span><span class="sxs-lookup"><span data-stu-id="0453b-108">Model based conventions allow the creation of conventions that affect properties and tables which are not configurable through standard conventions.</span></span> <span data-ttu-id="0453b-109">Algunos ejemplos son las columnas de discriminador en los modelos de tabla por jerarquía y las columnas de asociación independientes.</span><span class="sxs-lookup"><span data-stu-id="0453b-109">Examples of these are discriminator columns in table per hierarchy models and Independent Association columns.</span></span>  

## <a name="creating-a-convention"></a><span data-ttu-id="0453b-110">Crear una Convención</span><span class="sxs-lookup"><span data-stu-id="0453b-110">Creating a Convention</span></span>   

<span data-ttu-id="0453b-111">El primer paso para crear una Convención basada en el modelo es elegir cuándo debe aplicarse la Convención en el modelo.</span><span class="sxs-lookup"><span data-stu-id="0453b-111">The first step in creating a model based convention is choosing when in the pipeline the convention needs to be applied to the model.</span></span> <span data-ttu-id="0453b-112">Hay dos tipos de convenciones del modelo: conceptual (espacio C) y almacén (espacio de S).</span><span class="sxs-lookup"><span data-stu-id="0453b-112">There are two types of model conventions, Conceptual (C-Space) and Store (S-Space).</span></span> <span data-ttu-id="0453b-113">Se aplica una Convención de espacio C al modelo que se compila en la aplicación, mientras que una Convención de espacio S se aplica a la versión del modelo que representa la base de datos y controla aspectos como el nombre de las columnas generadas automáticamente.</span><span class="sxs-lookup"><span data-stu-id="0453b-113">A C-Space convention is applied to the model that the application builds, whereas an S-Space convention is applied to the version of the model that represents the database and controls things such as how automatically-generated columns are named.</span></span>  

<span data-ttu-id="0453b-114">Una Convención de modelo es una clase que se extiende desde IConceptualModelConvention o IStoreModelConvention.</span><span class="sxs-lookup"><span data-stu-id="0453b-114">A model convention is a class that extends from either IConceptualModelConvention or IStoreModelConvention.</span></span>  <span data-ttu-id="0453b-115">Estas interfaces aceptan un tipo genérico que puede ser de tipo MetadataItem, que se usa para filtrar el tipo de datos al que se aplica la Convención.</span><span class="sxs-lookup"><span data-stu-id="0453b-115">These interfaces both accept a generic type that can be of type MetadataItem which is used to filter the data type that the convention applies to.</span></span>  

## <a name="adding-a-convention"></a><span data-ttu-id="0453b-116">Agregar una Convención</span><span class="sxs-lookup"><span data-stu-id="0453b-116">Adding a Convention</span></span>   

<span data-ttu-id="0453b-117">Las convenciones de modelo se agregan de la misma manera que las clases de convenciones normales.</span><span class="sxs-lookup"><span data-stu-id="0453b-117">Model conventions are added in the same way as regular conventions classes.</span></span> <span data-ttu-id="0453b-118">En el método **OnModelCreating** , agregue la Convención a la lista de convenciones de un modelo.</span><span class="sxs-lookup"><span data-stu-id="0453b-118">In the **OnModelCreating** method, add the convention to the list of conventions for a model.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

public class BlogContext : DbContext  
{  
    public DbSet<Post> Posts { get; set; }  
    public DbSet<Comment> Comments { get; set; }  

    protected override void OnModelCreating(DbModelBuilder modelBuilder)  
    {  
        modelBuilder.Conventions.Add<MyModelBasedConvention>();  
    }  
}
```  

<span data-ttu-id="0453b-119">También se puede Agregar una Convención en relación con otra Convención mediante los métodos Conventions. AddBefore\<\> o Conventions. AddAfter\<\>.</span><span class="sxs-lookup"><span data-stu-id="0453b-119">A convention can also be added in relation to another convention using the Conventions.AddBefore\<\> or Conventions.AddAfter\<\> methods.</span></span> <span data-ttu-id="0453b-120">Para obtener más información acerca de las convenciones que se aplican Entity Framework consulte la sección Notas.</span><span class="sxs-lookup"><span data-stu-id="0453b-120">For more information about the conventions that Entity Framework applies see the notes section.</span></span>  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a><span data-ttu-id="0453b-121">Ejemplo: Convención del modelo de discriminador</span><span class="sxs-lookup"><span data-stu-id="0453b-121">Example: Discriminator Model Convention</span></span>  

<span data-ttu-id="0453b-122">Cambiar el nombre de las columnas generadas por EF es un ejemplo de algo que no se puede hacer con las otras API de convenciones.</span><span class="sxs-lookup"><span data-stu-id="0453b-122">Renaming columns generated by EF is an example of something that you can’t do with the other conventions APIs.</span></span>  <span data-ttu-id="0453b-123">Se trata de una situación en la que el uso de convenciones de modelos es la única opción.</span><span class="sxs-lookup"><span data-stu-id="0453b-123">This is a situation where using model conventions is your only option.</span></span>  

<span data-ttu-id="0453b-124">Un ejemplo de cómo usar una Convención basada en modelo para configurar las columnas generadas es personalizar la forma en que se denominan las columnas de discriminador.</span><span class="sxs-lookup"><span data-stu-id="0453b-124">An example of how to use a model based convention to configure generated columns is customizing the way discriminator columns are named.</span></span>  <span data-ttu-id="0453b-125">A continuación se muestra un ejemplo de una Convención basada en un modelo simple que cambia el nombre de todas las columnas del modelo denominado "Discriminator" a "EntityType".</span><span class="sxs-lookup"><span data-stu-id="0453b-125">Below is an example of a simple model based convention that renames every column in the model named “Discriminator” to “EntityType”.</span></span>  <span data-ttu-id="0453b-126">Esto incluye las columnas que el programador simplemente denomina "Discriminator".</span><span class="sxs-lookup"><span data-stu-id="0453b-126">This includes columns that the developer simply named “Discriminator”.</span></span> <span data-ttu-id="0453b-127">Dado que la columna "discriminadora" es una columna generada, esto debe ejecutarse en el espacio de S.</span><span class="sxs-lookup"><span data-stu-id="0453b-127">Since the “Discriminator” column is a generated column this needs to run in S-Space.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

class DiscriminatorRenamingConvention : IStoreModelConvention<EdmProperty>  
{  
    public void Apply(EdmProperty property, DbModel model)  
    {            
        if (property.Name == "Discriminator")  
        {  
            property.Name = "EntityType";  
        }  
    }  
}
```  

## <a name="example-general-ia-renaming-convention"></a><span data-ttu-id="0453b-128">Ejemplo: Convención de cambio de nombre general de IA</span><span class="sxs-lookup"><span data-stu-id="0453b-128">Example: General IA Renaming Convention</span></span>   

<span data-ttu-id="0453b-129">Otro ejemplo más complicado de convenciones basadas en modelos en acción consiste en configurar el modo en que se asignan nombres a las asociaciones independientes (IAs).</span><span class="sxs-lookup"><span data-stu-id="0453b-129">Another more complicated example of model based conventions in action is to configure the way that Independent Associations (IAs) are named.</span></span>  <span data-ttu-id="0453b-130">Se trata de una situación en la que se pueden aplicar las convenciones de modelo, ya que las generadas por EF y no están presentes en el modelo al que puede tener acceso la API de DbModelBuilder.</span><span class="sxs-lookup"><span data-stu-id="0453b-130">This is a situation where Model conventions are applicable because IAs are generated by EF and aren’t present in the model that the DbModelBuilder API can access.</span></span>  

<span data-ttu-id="0453b-131">Cuando EF genera un IA, crea una columna denominada EntityType_KeyName.</span><span class="sxs-lookup"><span data-stu-id="0453b-131">When EF generates an IA, it creates a column named EntityType_KeyName.</span></span> <span data-ttu-id="0453b-132">Por ejemplo, para una asociación denominada Customer con una columna de clave denominada CustomerId, se generaría una columna denominada Customer_CustomerId.</span><span class="sxs-lookup"><span data-stu-id="0453b-132">For example, for an association named Customer with a key column named CustomerId it would generate a column named Customer_CustomerId.</span></span> <span data-ttu-id="0453b-133">La Convención siguiente quita el carácter '\_' del nombre de columna que se genera para el IA.</span><span class="sxs-lookup"><span data-stu-id="0453b-133">The following convention strips the ‘\_’ character out of the column name that is generated for the IA.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

// Provides a convention for fixing the independent association (IA) foreign key column names.  
public class ForeignKeyNamingConvention : IStoreModelConvention<AssociationType>
{

    public void Apply(AssociationType association, DbModel model)
    {
        // Identify ForeignKey properties (including IAs)  
        if (association.IsForeignKey)
        {
            // rename FK columns  
            var constraint = association.Constraint;
            if (DoPropertiesHaveDefaultNames(constraint.FromProperties, constraint.ToRole.Name, constraint.ToProperties))
            {
                NormalizeForeignKeyProperties(constraint.FromProperties);
            }
            if (DoPropertiesHaveDefaultNames(constraint.ToProperties, constraint.FromRole.Name, constraint.FromProperties))
            {
                NormalizeForeignKeyProperties(constraint.ToProperties);
            }
        }
    }

    private bool DoPropertiesHaveDefaultNames(ReadOnlyMetadataCollection<EdmProperty> properties, string roleName, ReadOnlyMetadataCollection<EdmProperty> otherEndProperties)
    {
        if (properties.Count != otherEndProperties.Count)
        {
            return false;
        }

        for (int i = 0; i < properties.Count; ++i)
        {
            if (!properties[i].Name.EndsWith("_" + otherEndProperties[i].Name))
            {
                return false;
            }
        }
        return true;
    }

    private void NormalizeForeignKeyProperties(ReadOnlyMetadataCollection<EdmProperty> properties)
    {
        for (int i = 0; i < properties.Count; ++i)
        {
            int underscoreIndex = properties[i].Name.IndexOf('_');
            if (underscoreIndex > 0)
            {
                properties[i].Name = properties[i].Name.Remove(underscoreIndex, 1);
            }                 
        }
    }
}
```  

## <a name="extending-existing-conventions"></a><span data-ttu-id="0453b-134">Extender las convenciones existentes</span><span class="sxs-lookup"><span data-stu-id="0453b-134">Extending Existing Conventions</span></span>   

<span data-ttu-id="0453b-135">Si tiene que escribir una Convención similar a una de las convenciones que Entity Framework ya se aplican a su modelo, siempre puede ampliar esa Convención para evitar tener que volver a escribirla desde el principio.</span><span class="sxs-lookup"><span data-stu-id="0453b-135">If you need to write a convention that is similar to one of the conventions that Entity Framework already applies to your model you can always extend that convention to avoid having to rewrite it from scratch.</span></span>  <span data-ttu-id="0453b-136">Un ejemplo de esto es reemplazar la Convención de coincidencia de ID. existente por una personalizada.</span><span class="sxs-lookup"><span data-stu-id="0453b-136">An example of this is to replace the existing Id matching convention with a custom one.</span></span>   <span data-ttu-id="0453b-137">Una ventaja adicional para reemplazar la Convención de claves es que solo se llamará al método invalidado si no hay ninguna clave ya detectada o configurada explícitamente.</span><span class="sxs-lookup"><span data-stu-id="0453b-137">An added benefit to overriding the key convention is that the overridden method will get called only if there is no key already detected or explicitly configured.</span></span> <span data-ttu-id="0453b-138">Aquí encontrará una lista de las convenciones utilizadas por Entity Framework: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span><span class="sxs-lookup"><span data-stu-id="0453b-138">A list of conventions that used by Entity Framework is available here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;
using System.Linq;  

// Convention to detect primary key properties.
// Recognized naming patterns in order of precedence are:
// 1. 'Key'
// 2. [type name]Key
// Primary key detection is case insensitive.
public class CustomKeyDiscoveryConvention : KeyDiscoveryConvention
{
    private const string Id = "Key";

    protected override IEnumerable<EdmProperty> MatchKeyProperty(
        EntityType entityType, IEnumerable<EdmProperty> primitiveProperties)
    {
        Debug.Assert(entityType != null);
        Debug.Assert(primitiveProperties != null);

        var matches = primitiveProperties
            .Where(p => Id.Equals(p.Name, StringComparison.OrdinalIgnoreCase));

        if (!matches.Any())
       {
            matches = primitiveProperties
                .Where(p => (entityType.Name + Id).Equals(p.Name, StringComparison.OrdinalIgnoreCase));
        }

        // If the number of matches is more than one, then multiple properties matched differing only by
        // case--for example, "Key" and "key".  
        if (matches.Count() > 1)
        {
            throw new InvalidOperationException("Multiple properties match the key convention");
        }

        return matches;
    }
}
```  

<span data-ttu-id="0453b-139">A continuación, necesitamos agregar la nueva Convención antes de la Convención de claves existente.</span><span class="sxs-lookup"><span data-stu-id="0453b-139">We then need to add our new convention before the existing key convention.</span></span> <span data-ttu-id="0453b-140">Después de agregar el CustomKeyDiscoveryConvention, podemos quitar el IdKeyDiscoveryConvention.</span><span class="sxs-lookup"><span data-stu-id="0453b-140">After we add the CustomKeyDiscoveryConvention, we can remove the IdKeyDiscoveryConvention.</span></span>  <span data-ttu-id="0453b-141">Si no quitamos el IdKeyDiscoveryConvention existente, esta Convención tendría prioridad sobre la Convención de detección de identificadores, ya que se ejecuta en primer lugar, pero en caso de que no se encuentre ninguna propiedad "clave", se ejecutará la Convención "ID".</span><span class="sxs-lookup"><span data-stu-id="0453b-141">If we didn’t remove the existing IdKeyDiscoveryConvention this convention would still take precedence over the Id discovery convention since it is run first, but in the case where no “key” property is found, the “id” convention will run.</span></span>  <span data-ttu-id="0453b-142">Vemos este comportamiento porque cada Convención Ve el modelo tal y como lo ha actualizado la Convención anterior (en lugar de trabajar en él de forma independiente y todo combinado) para que, por ejemplo, una Convención anterior actualizara un nombre de columna para que coincida con algo de le interesa su convención personalizada (cuando antes de que el nombre no sea de interés) se aplique a esa columna.</span><span class="sxs-lookup"><span data-stu-id="0453b-142">We see this behavior because each convention sees the model as updated by the previous convention (rather than operating on it independently and all being combined together) so that if for example, a previous convention updated a column name to match something of interest to your custom convention (when before that the name was not of interest) then it will apply to that column.</span></span>  

``` csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Comment> Comments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new CustomKeyDiscoveryConvention());
        modelBuilder.Conventions.Remove<IdKeyDiscoveryConvention>();
    }
}
```  

## <a name="notes"></a><span data-ttu-id="0453b-143">Notas</span><span class="sxs-lookup"><span data-stu-id="0453b-143">Notes</span></span>  

<span data-ttu-id="0453b-144">Una lista de las convenciones aplicadas actualmente por Entity Framework está disponible en la documentación de MSDN aquí: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span><span class="sxs-lookup"><span data-stu-id="0453b-144">A list of conventions that are currently applied by Entity Framework is available in the MSDN documentation here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  <span data-ttu-id="0453b-145">Esta lista se extrae directamente del código fuente.</span><span class="sxs-lookup"><span data-stu-id="0453b-145">This list is pulled directly from our source code.</span></span>  <span data-ttu-id="0453b-146">El código fuente de Entity Framework 6 está disponible en [GitHub](https://github.com/aspnet/entityframework6/) y muchas de las convenciones utilizadas por Entity Framework son buenos puntos de partida para las convenciones basadas en modelos personalizados.</span><span class="sxs-lookup"><span data-stu-id="0453b-146">The source code for Entity Framework 6 is available on [GitHub](https://github.com/aspnet/entityframework6/) and many of the conventions used by Entity Framework are good starting points for custom model based conventions.</span></span>  
