---
title: 'Convenciones de Model-Based: EF6'
description: Convenciones de Model-Based en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/model
ms.openlocfilehash: c4978277ced72cc5c017af5f69b8760f316087d5
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066439"
---
# <a name="model-based-conventions"></a>Convenciones de Model-Based
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.  

Las convenciones basadas en modelos son un método avanzado de configuración del modelo basado en convenciones. En la mayoría de los casos, se debe usar la API de la [Convención de Code First personalizada en DbModelBuilder](xref:ef6/modeling/code-first/conventions/custom) . Antes de usar las convenciones basadas en modelos, se recomienda comprender la API de DbModelBuilder para las convenciones.  

Las convenciones basadas en modelos permiten la creación de convenciones que afectan a las propiedades y tablas que no se pueden configurar a través de convenciones estándar. Algunos ejemplos son las columnas de discriminador en los modelos de tabla por jerarquía y las columnas de asociación independientes.  

## <a name="creating-a-convention"></a>Crear una Convención   

El primer paso para crear una Convención basada en el modelo es elegir cuándo debe aplicarse la Convención en el modelo. Hay dos tipos de convenciones del modelo: conceptual (espacio C) y almacén (espacio de S). Se aplica una Convención de espacio C al modelo que se compila en la aplicación, mientras que una Convención de espacio S se aplica a la versión del modelo que representa la base de datos y controla aspectos como el nombre de las columnas generadas automáticamente.  

Una Convención de modelo es una clase que se extiende desde IConceptualModelConvention o IStoreModelConvention.  Estas interfaces aceptan un tipo genérico que puede ser de tipo MetadataItem, que se usa para filtrar el tipo de datos al que se aplica la Convención.  

## <a name="adding-a-convention"></a>Agregar una Convención   

Las convenciones de modelo se agregan de la misma manera que las clases de convenciones normales. En el método **OnModelCreating** , agregue la Convención a la lista de convenciones de un modelo.  

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

También se puede Agregar una Convención en relación con otra Convención mediante los métodos Conventions. AddBefore \<\> o Conventions. AddAfter \<\> . Para obtener más información acerca de las convenciones que se aplican Entity Framework consulte la sección Notas.  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a>Ejemplo: Convención del modelo de discriminador  

Cambiar el nombre de las columnas generadas por EF es un ejemplo de algo que no se puede hacer con las otras API de convenciones.  Se trata de una situación en la que el uso de convenciones de modelos es la única opción.  

Un ejemplo de cómo usar una Convención basada en modelo para configurar las columnas generadas es personalizar la forma en que se denominan las columnas de discriminador.  A continuación se muestra un ejemplo de una Convención basada en un modelo simple que cambia el nombre de todas las columnas del modelo denominado "Discriminator" a "EntityType".  Esto incluye las columnas que el programador simplemente denomina "Discriminator". Dado que la columna "discriminadora" es una columna generada, esto debe ejecutarse en el espacio de S.  

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

## <a name="example-general-ia-renaming-convention"></a>Ejemplo: Convención de cambio de nombre general de IA   

Otro ejemplo más complicado de convenciones basadas en modelos en acción consiste en configurar el modo en que se asignan nombres a las asociaciones independientes (IAs).  Se trata de una situación en la que se pueden aplicar las convenciones de modelo, ya que las generadas por EF y no están presentes en el modelo al que puede tener acceso la API de DbModelBuilder.  

Cuando EF genera un IA, crea una columna denominada EntityType_KeyName. Por ejemplo, para una asociación denominada Customer con una columna de clave denominada CustomerId, se generaría una columna denominada Customer_CustomerId. La Convención siguiente quita el \_ carácter ' ' del nombre de columna que se genera para el IA.  

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

## <a name="extending-existing-conventions"></a>Extender las convenciones existentes   

Si tiene que escribir una Convención similar a una de las convenciones que Entity Framework ya se aplican a su modelo, siempre puede ampliar esa Convención para evitar tener que volver a escribirla desde el principio.  Un ejemplo de esto es reemplazar la Convención de coincidencia de ID. existente por una personalizada.   Una ventaja adicional para reemplazar la Convención de claves es que solo se llamará al método invalidado si no hay ninguna clave ya detectada o configurada explícitamente. Aquí encontrará una lista de las convenciones utilizadas por Entity Framework: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .  

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

A continuación, necesitamos agregar la nueva Convención antes de la Convención de claves existente. Después de agregar el CustomKeyDiscoveryConvention, podemos quitar el IdKeyDiscoveryConvention.  Si no quitamos el IdKeyDiscoveryConvention existente, esta Convención tendría prioridad sobre la Convención de detección de identificadores, ya que se ejecuta en primer lugar, pero en caso de que no se encuentre ninguna propiedad "clave", se ejecutará la Convención "ID".  Vemos este comportamiento porque cada Convención Ve el modelo tal y como lo ha actualizado la Convención anterior (en lugar de trabajar en él de forma independiente y todo combinado conjuntamente) de modo que, si, por ejemplo, una Convención anterior actualizara un nombre de columna para que coincida con algo de interés con la Convención personalizada (cuando antes de que el nombre no fuera de interés) se aplique a esa columna  

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

## <a name="notes"></a>Notas  

Una lista de las convenciones aplicadas actualmente por Entity Framework está disponible en la documentación de MSDN aquí: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) .  Esta lista se extrae directamente del código fuente.  El código fuente de Entity Framework 6 está disponible en [GitHub](https://github.com/aspnet/entityframework6/) y muchas de las convenciones utilizadas por Entity Framework son buenos puntos de partida para las convenciones basadas en modelos personalizados.  
