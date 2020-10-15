---
title: 'Trabajar con valores de propiedad: EF6'
description: Trabajar con valores de propiedad en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/property-values
ms.openlocfilehash: deb110df1dbb2b433b54b98a684bc06e696c4351
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064450"
---
# <a name="working-with-property-values"></a>Trabajar con valores de propiedad
En la mayoría de los casos, Entity Framework se encargará del seguimiento del estado, los valores originales y los valores actuales de las propiedades de las instancias de la entidad. Sin embargo, puede haber algunos casos, como escenarios desconectados, donde desea ver o manipular la información que EF tiene sobre las propiedades. Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.  

Entity Framework realiza un seguimiento de dos valores para cada propiedad de una entidad de la que se ha realizado un seguimiento. El valor actual es, como el nombre indica, el valor actual de la propiedad en la entidad. El valor original es el valor que tenía la propiedad cuando la entidad se consultaba desde la base de datos o se adjuntó al contexto.  

Hay dos mecanismos generales para trabajar con valores de propiedad:  

- El valor de una propiedad única se puede obtener de una manera fuertemente tipada mediante el método de propiedad.  
- Los valores de todas las propiedades de una entidad se pueden leer en un objeto DbPropertyValues. A continuación, DbPropertyValues actúa como un objeto similar a un diccionario para permitir la lectura y el establecimiento de los valores de propiedad. Los valores de un objeto DbPropertyValues se pueden establecer a partir de los valores de otro objeto DbPropertyValues o de los valores de otro objeto, como otra copia de la entidad o un objeto de transferencia de datos simple (DTO).  

En las secciones siguientes se muestran ejemplos del uso de los dos mecanismos anteriores.  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a>Obtener y establecer el valor actual o original de una propiedad individual  

En el ejemplo siguiente se muestra cómo se puede leer el valor actual de una propiedad y establecerla en un nuevo valor:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(3);

    // Read the current value of the Name property
    string currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;

    // Set the Name property to a new value
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";

    // Read the current value of the Name property using a string for the property name
    object currentName2 = context.Entry(blog).Property("Name").CurrentValue;

    // Set the Name property to a new value using a string for the property name
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

Use la propiedad OriginalValue en lugar de la propiedad CurrentValue para leer o establecer el valor original.  

Tenga en cuenta que el valor devuelto se escribe como "objeto" cuando se utiliza una cadena para especificar el nombre de la propiedad. Por otro lado, el valor devuelto está fuertemente tipado si se utiliza una expresión lambda.  

Al establecer el valor de la propiedad de este modo, solo se marcará la propiedad como modificada si el nuevo valor es diferente del valor anterior.  

Cuando se establece un valor de propiedad de esta manera, el cambio se detecta automáticamente aunque AutoDetectChanges esté desactivado.  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a>Obtener y establecer el valor actual de una propiedad no asignada  

También se puede leer el valor actual de una propiedad que no está asignada a la base de datos. Un ejemplo de una propiedad no asignada podría ser una propiedad RssLink en el blog. Este valor se puede calcular en función de BlogId y, por tanto, no es necesario almacenarlo en la base de datos. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    // Read the current value of an unmapped property
    var rssLink = context.Entry(blog).Property(p => p.RssLink).CurrentValue;

    // Use a string to specify the property name
    var rssLinkAgain = context.Entry(blog).Property("RssLink").CurrentValue;
}
```  

También se puede establecer el valor actual si la propiedad expone un establecedor.  

Leer los valores de las propiedades sin asignar es útil al realizar Entity Framework la validación de las propiedades no asignadas. Por el mismo motivo, los valores actuales se pueden leer y establecer para las propiedades de entidades a las que el contexto no realiza un seguimiento actualmente. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    // Create an entity that is not being tracked
    var blog = new Blog { Name = "ADO.NET Blog" };

    // Read and set the current value of Name as before
    var currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";
    var currentName2 = context.Entry(blog).Property("Name").CurrentValue;
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

Tenga en cuenta que los valores originales no están disponibles para las propiedades no asignadas o para las propiedades de entidades de las que el contexto no realiza un seguimiento.  

## <a name="checking-whether-a-property-is-marked-as-modified"></a>Comprobar si una propiedad está marcada como modificada  

En el ejemplo siguiente se muestra cómo comprobar si una propiedad individual está marcada como modificada:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

Los valores de las propiedades modificadas se envían como actualizaciones a la base de datos cuando se llama a SaveChanges.  

##  <a name="marking-a-property-as-modified"></a>Marcar una propiedad como modificada  

En el ejemplo siguiente se muestra cómo forzar que una propiedad individual se marque como modificada:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

Marcar una propiedad como modificada obliga a que se envíe una actualización a la base de datos para la propiedad cuando se llama a SaveChanges incluso si el valor actual de la propiedad es el mismo que el valor original.  

Actualmente no es posible restablecer una propiedad individual para que no se modifique una vez que se ha marcado como modificada. Esto es algo que tenemos previsto admitir en una versión futura.  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a>Leer los valores actuales, originales y de base de datos para todas las propiedades de una entidad  

En el ejemplo siguiente se muestra cómo leer los valores actuales, los valores originales y los valores reales de la base de datos para todas las propiedades asignadas de una entidad.  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Make a modification to Name in the tracked entity
    blog.Name = "My Cool Blog";

    // Make a modification to Name in the database
    context.Database.SqlCommand("update dbo.Blogs set Name = 'My Boring Blog' where Id = 1");

    // Print out current, original, and database values
    Console.WriteLine("Current values:");
    PrintValues(context.Entry(blog).CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(context.Entry(blog).OriginalValues);

    Console.WriteLine("\nDatabase values:");
    PrintValues(context.Entry(blog).GetDatabaseValues());
}

public static void PrintValues(DbPropertyValues values)
{
    foreach (var propertyName in values.PropertyNames)
    {
        Console.WriteLine("Property {0} has value {1}",
                          propertyName, values[propertyName]);
    }
}
```  

Los valores actuales son los valores que las propiedades de la entidad contienen actualmente. Los valores originales son los valores que se leyeron de la base de datos cuando se realizó la consulta de la entidad. Los valores de la base de datos son los valores que están almacenados actualmente en la base de datos. Obtener los valores de la base de datos es útil cuando los valores de la base de datos pueden haber cambiado desde la consulta de la entidad, por ejemplo, cuando otro usuario ha realizado una edición simultánea en la base de datos.  

## <a name="setting-current-or-original-values-from-another-object"></a>Establecer valores actuales o originales de otro objeto  

Los valores actuales o originales de una entidad de la que se ha realizado un seguimiento se pueden actualizar mediante la copia de los valores de otro objeto. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var coolBlog = new Blog { Id = 1, Name = "My Cool Blog" };
    var boringBlog = new BlogDto { Id = 1, Name = "My Boring Blog" };

    // Change the current and original values by copying the values from other objects
    var entry = context.Entry(blog);
    entry.CurrentValues.SetValues(coolBlog);
    entry.OriginalValues.SetValues(boringBlog);

    // Print out current and original values
    Console.WriteLine("Current values:");
    PrintValues(entry.CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(entry.OriginalValues);
}

public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```  

La ejecución del código anterior se imprimirá:  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

Esta técnica se usa a veces al actualizar una entidad con valores obtenidos de una llamada de servicio o un cliente en una aplicación de n niveles. Tenga en cuenta que el objeto utilizado no tiene que ser del mismo tipo que la entidad, siempre y cuando tenga propiedades cuyos nombres coincidan con los de la entidad. En el ejemplo anterior, se usa una instancia de BlogDTO para actualizar los valores originales.  

Tenga en cuenta que solo las propiedades que se establecen en valores diferentes cuando se copian del otro objeto se marcan como modificadas.  

## <a name="setting-current-or-original-values-from-a-dictionary"></a>Establecer los valores actuales o originales de un diccionario  

Los valores actuales o originales de una entidad de la que se ha realizado un seguimiento se pueden actualizar mediante la copia de los valores de un diccionario u otra estructura de datos. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "The New ADO.NET Blog" },
        { "Url", "blogs.msdn.com/adonet" },
    };

    var currentValues = context.Entry(blog).CurrentValues;

    foreach (var propertyName in newValues.Keys)
    {
        currentValues[propertyName] = newValues[propertyName];
    }

    PrintValues(currentValues);
}
```  

Use la propiedad OriginalValues en lugar de la propiedad CurrentValues para establecer los valores originales.  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a>Establecer valores actuales o originales de un diccionario mediante la propiedad  

Una alternativa al uso de CurrentValues o OriginalValues, como se muestra arriba, es usar el método de propiedad para establecer el valor de cada propiedad. Esto puede ser preferible cuando es necesario establecer los valores de las propiedades complejas. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "John Doe" },
        { "Location.City", "Redmond" },
        { "Location.State.Name", "Washington" },
        { "Location.State.Code", "WA" },
    };

    var entry = context.Entry(user);

    foreach (var propertyName in newValues.Keys)
    {
        entry.Property(propertyName).CurrentValue = newValues[propertyName];
    }
}
```  

En el ejemplo anterior se tiene acceso a las propiedades complejas mediante nombres con puntos. Para conocer otras formas de obtener acceso a propiedades complejas, vea las dos secciones más adelante en este tema específicamente sobre las propiedades complejas.  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>Crear un objeto clonado que contenga valores actual, original o de base de datos  

El objeto DbPropertyValues devuelto de CurrentValues, OriginalValues o GetDatabaseValues se puede usar para crear un clon de la entidad. Este clon contendrá los valores de propiedad del objeto DbPropertyValues que se usa para crearlo. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

Tenga en cuenta que el objeto devuelto no es la entidad y el contexto no realiza su seguimiento. El objeto devuelto tampoco tiene ninguna relación establecida en otros objetos.  

El objeto clonado puede ser útil para resolver problemas relacionados con las actualizaciones simultáneas en la base de datos, especialmente cuando se usa una interfaz de usuario que implique el enlace de datos a objetos de un tipo determinado.  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a>Obtener y establecer los valores actuales o originales de propiedades complejas  

El valor de un objeto complejo completo se puede leer y establecer mediante el método de propiedad, tal como puede ser para una propiedad primitiva. Además, puede explorar en profundidad el objeto complejo y leer o establecer las propiedades de ese objeto, o incluso un objeto anidado. A continuación se muestran algunos ejemplos:  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    // Get the Location complex object
    var location = context.Entry(user)
                       .Property(u => u.Location)
                       .CurrentValue;

    // Get the nested State complex object using chained calls
    var state1 = context.Entry(user)
                     .ComplexProperty(u => u.Location)
                     .Property(l => l.State)
                     .CurrentValue;

    // Get the nested State complex object using a single lambda expression
    var state2 = context.Entry(user)
                     .Property(u => u.Location.State)
                     .CurrentValue;

    // Get the nested State complex object using a dotted string
    var state3 = context.Entry(user)
                     .Property("Location.State")
                     .CurrentValue;

    // Get the value of the Name property on the nested State complex object using chained calls
    var name1 = context.Entry(user)
                       .ComplexProperty(u => u.Location)
                       .ComplexProperty(l => l.State)
                       .Property(s => s.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a single lambda expression
    var name2 = context.Entry(user)
                       .Property(u => u.Location.State.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a dotted string
    var name3 = context.Entry(user)
                       .Property("Location.State.Name")
                       .CurrentValue;
}
```  

Use la propiedad OriginalValue en lugar de la propiedad CurrentValue para obtener o establecer un valor original.  

Tenga en cuenta que se puede utilizar la propiedad o el método ComplexProperty para tener acceso a una propiedad compleja. Sin embargo, se debe usar el método ComplexProperty si desea explorar en profundidad el objeto complejo con llamadas adicionales a propiedades o ComplexProperty.  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a>Usar DbPropertyValues para tener acceso a propiedades complejas  

Cuando se usa CurrentValues, OriginalValues o GetDatabaseValues para obtener todos los valores actuales, originales o de la base de datos de una entidad, los valores de las propiedades complejas se devuelven como objetos DbPropertyValues anidados. Estos objetos anidados se pueden usar para obtener valores del objeto complejo. Por ejemplo, el método siguiente imprimirá los valores de todas las propiedades, incluidos los valores de las propiedades complejas y las propiedades complejas anidadas.  

``` csharp
public static void WritePropertyValues(string parentPropertyName, DbPropertyValues propertyValues)
{
    foreach (var propertyName in propertyValues.PropertyNames)
    {
        var nestedValues = propertyValues[propertyName] as DbPropertyValues;
        if (nestedValues != null)
        {
            WritePropertyValues(parentPropertyName + propertyName + ".", nestedValues);
        }
        else
        {
            Console.WriteLine("Property {0}{1} has value {2}",
                              parentPropertyName, propertyName,
                              propertyValues[propertyName]);
        }
    }
}
```  

Para imprimir todos los valores de propiedad actuales, se llamaría al método de la siguiente manera:  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
