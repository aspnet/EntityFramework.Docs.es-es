---
title: 'Acceso a entidades de las que se ha realizado un seguimiento: EF Core'
description: Usar EntityEntry, DbContext. Entries y DbSet. local para tener acceso a las entidades sometidas a seguimiento
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/entity-entries
ms.openlocfilehash: f385016aba61535f33e34c622dd43ce6dc823fc5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129641"
---
# <a name="accessing-tracked-entities"></a>Acceso a entidades sometidas a seguimiento

Hay cuatro API principales para tener acceso a las entidades a las que realiza un seguimiento <xref:Microsoft.EntityFrameworkCore.DbContext> :

- <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> Devuelve una <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instancia de para una instancia de entidad determinada.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType> Devuelve <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> instancias para todas las entidades de las que se ha realizado un seguimiento o para todas las entidades de las que se ha realizado un seguimiento de un tipo determinado.
- <xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> y buscan <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> una sola entidad por clave principal, buscando primero las entidades de las que se realiza un seguimiento y, a continuación, consultando la base de datos si es necesario.
- <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> Devuelve las entidades reales (no las instancias de EntityEntry) para las entidades del tipo de entidad representadas por DbSet.

Cada una de ellas se describe con más detalle en las secciones siguientes.

> [!TIP]
> En este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de EF Core. Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información sobre estos temas.

> [!TIP]
> Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo desde GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities).

## <a name="using-dbcontextentry-and-entityentry-instances"></a>Usar instancias de DbContext. entry e EntityEntry

Para cada entidad de la que se realiza un seguimiento, Entity Framework Core (EF Core) realiza un seguimiento de:

- El estado general de la entidad. Este es uno de `Unchanged` , `Modified` , `Added` o `Deleted` ; consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información.
- Las relaciones entre las entidades de las que se realiza un seguimiento. Por ejemplo, el blog al que pertenece un envío.
- "Valores actuales" de las propiedades.
- Los "valores originales" de las propiedades, cuando esta información está disponible. Los valores originales son los valores de propiedad que existían al consultar la entidad desde la base de datos.
- Los valores de propiedad que se han modificado desde que se consultaron.
- Otra información sobre los valores de propiedad, como si el valor es [temporal](xref:core/change-tracking/miscellaneous#temporary-values)o no.

Al pasar una instancia de entidad a, se obtiene <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> un que <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> proporciona acceso a esta información para la entidad determinada. Por ejemplo:

<!--
        using var context = new BlogsContext();

        var blog = context.Blogs.Single(e => e.Id == 1);
        var entityEntry = context.Entry(blog);

-->
[!code-csharp[Using_DbContext_Entry_and_EntityEntry_instances_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbContext_Entry_and_EntityEntry_instances_1)]

En las secciones siguientes se muestra cómo usar EntityEntry para tener acceso y manipular el estado de la entidad, así como el estado de las propiedades y las navegaciones de la entidad.

### <a name="working-with-the-entity"></a>Trabajar con la entidad

El uso más común de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> es el acceso al actual <xref:Microsoft.EntityFrameworkCore.EntityState> de una entidad. Por ejemplo:

<!--
        var currentState = context.Entry(blog).State;
        if (currentState == EntityState.Unchanged)
        {
            context.Entry(blog).State = EntityState.Modified;
        }
-->
[!code-csharp[Work_with_the_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_1)]

El método entry también se puede usar en entidades que todavía no se han controlado. Esto _no inicia el seguimiento de la entidad_; el estado de la entidad sigue siendo `Detatched` . Sin embargo, el EntityEntry devuelto se puede usar para cambiar el estado de la entidad, momento en el que se realizará el seguimiento de la entidad en el estado especificado. Por ejemplo, el código siguiente comenzará el seguimiento de una instancia de blog como `Added` :

<!--
        var newBlog = new Blog();
        Debug.Assert(context.Entry(newBlog).State == EntityState.Detached);

        context.Entry(newBlog).State = EntityState.Added;
        Debug.Assert(context.Entry(newBlog).State == EntityState.Added);
-->
[!code-csharp[Work_with_the_entity_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_2)]

> [!TIP]
> A diferencia de EF6, establecer el estado de una entidad individual no hará que se realice el seguimiento de todas las entidades conectadas. Esto hace que se establezca el estado de esta manera como una operación de nivel inferior que llamar a `Add` , `Attach` o `Update` , que operan en un grafo completo de entidades.

En la tabla siguiente se resumen las maneras de usar una EntityEntry para trabajar con una entidad completa:

| Miembro EntityEntry                                                                                         | Descripción
|:-----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.State?displayProperty=nameWithType>         | Obtiene y establece el <xref:Microsoft.EntityFrameworkCore.EntityState> de la entidad.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Entity?displayProperty=nameWithType>        | Obtiene la instancia de la entidad.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Context?displayProperty=nameWithType>       | <xref:Microsoft.EntityFrameworkCore.DbContext>Que está realizando el seguimiento de esta entidad.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Metadata?displayProperty=nameWithType>      | <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadatos para el tipo de entidad.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.IsKeySet?displayProperty=nameWithType>      | Indica si se ha establecido o no el valor de clave de la entidad.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload?displayProperty=nameWithType>        | Sobrescribe los valores de propiedad con los valores leídos de la base de datos.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> | Fuerza la detección de cambios solo para esta entidad; consulte [detección y notificaciones de cambios](xref:core/change-tracking/change-detection).

### <a name="working-with-a-single-property"></a>Trabajar con una sola propiedad

Varias sobrecargas de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> permiten el acceso a información sobre una propiedad individual de una entidad. Por ejemplo, mediante una API de tipo "fuertemente tipada":

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property(e => e.Name);
-->
[!code-csharp[Work_with_a_single_property_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1a)]

En su lugar, se puede pasar el nombre de la propiedad como una cadena. Por ejemplo:

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property<string>("Name");
-->
[!code-csharp[Work_with_a_single_property_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1b)]

A continuación, el devuelto <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> se puede utilizar para tener acceso a la información sobre la propiedad. Por ejemplo, se puede usar para obtener y establecer el valor actual de la propiedad en esta entidad:

<!--
            string currentValue = context.Entry(blog).Property(e => e.Name).CurrentValue;
            context.Entry(blog).Property(e => e.Name).CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1d)]

Los dos métodos de propiedad usados anteriormente devuelven una instancia genérica fuertemente tipada <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> . Se prefiere el uso de este tipo genérico porque permite el acceso a los valores de propiedad sin [tipos de valor de conversión boxing](/dotnet/csharp/programming-guide/types/boxing-and-unboxing). Sin embargo, si no se conoce el tipo de entidad o propiedad en tiempo de compilación, en <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> su lugar se puede obtener un no genérico:

<!--
            var propertyEntry = context.Entry(blog).Property("Name");
-->
[!code-csharp[Work_with_a_single_property_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1c)]

Esto permite el acceso a la información de propiedades de cualquier propiedad, independientemente de su tipo, a costa de los tipos de valor de conversión boxing. Por ejemplo:

<!--
            object blog = context.Blogs.Single(e => e.Id == 1);

            object currentValue = context.Entry(blog).Property("Name").CurrentValue;
            context.Entry(blog).Property("Name").CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1e)]

En la tabla siguiente se resume la información de propiedades expuesta por PropertyEntry:

| Miembro PropertyEntry                               | Descripción
|:-------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.CurrentValue?displayProperty=nameWithType>  | Obtiene y establece el valor actual de la propiedad.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.OriginalValue?displayProperty=nameWithType> | Obtiene y establece el valor original de la propiedad, si está disponible.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.EntityEntry?displayProperty=nameWithType>   | Referencia inversa a la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> de la entidad.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.Metadata?displayProperty=nameWithType>          | <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> metadatos de la propiedad.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified?displayProperty=nameWithType>        | Indica si esta propiedad está marcada como modificada y permite cambiar este estado.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary?displayProperty=nameWithType>       | Indica si esta propiedad está marcada como [temporal](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values)y permite cambiar este estado.

Notas:

- El valor original de una propiedad es el valor que tenía la propiedad cuando se realizó una consulta a la entidad desde la base de datos. Sin embargo, los valores originales no están disponibles si la entidad se desconectó y, a continuación, se asocia explícitamente a otro DbContext, por ejemplo, con `Attach` o `Update` . En este caso, el valor original devuelto será el mismo que el valor actual.
- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> solo actualizará las propiedades marcadas como modificadas. Establézcalo en <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified> true para forzar que EF Core actualice un valor de propiedad determinado, o establézcalo en false para impedir que EF Core actualice el valor de propiedad.
- Los [generadores](xref:core/modeling/generated-properties)de valores EF Core generan normalmente [valores temporales](xref:core/change-tracking/miscellaneous) . Al establecer el valor actual de una propiedad, se reemplazará el valor temporal por el valor especificado y se marcará la propiedad como no temporal. Establézcalo <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary> en true para forzar que un valor sea temporal incluso después de que se haya establecido explícitamente.

### <a name="working-with-a-single-navigation"></a>Trabajar con una sola navegación

Varias sobrecargas de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> permiten el acceso a información sobre una navegación individual.

Se tiene acceso a las navegaciones de referencia a una sola entidad relacionada a través de los <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> métodos. Las navegaciones de referencia apuntan a los lados "uno" de las relaciones uno a varios y ambos lados de las relaciones uno a uno. Por ejemplo:

<!--
        ReferenceEntry<Post, Blog> referenceEntry1 = context.Entry(post).Reference(e => e.Blog);
        ReferenceEntry<Post, Blog> referenceEntry2 = context.Entry(post).Reference<Blog>("Blog");
        ReferenceEntry referenceEntry3 = context.Entry(post).Reference("Blog");
-->
[!code-csharp[Work_with_a_single_navigation_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_1)]

Las navegaciones también pueden ser colecciones de entidades relacionadas cuando se usan para los lados "varios" de las relaciones uno a varios y varios a varios. Los <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> métodos se usan para tener acceso a las navegaciones de la colección. Por ejemplo:

<!--
        CollectionEntry<Blog, Post> collectionEntry1 = context.Entry(blog).Collection(e => e.Posts);
        CollectionEntry<Blog, Post> collectionEntry2 = context.Entry(blog).Collection<Post>("Posts");
        CollectionEntry collectionEntry3 = context.Entry(blog).Collection("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2a)]

Algunas operaciones son comunes para todas las navegaciones. Se puede tener acceso a estas para las referencias y a las navegaciones de la colección mediante el <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> método. Tenga en cuenta que solo está disponible el acceso no genérico cuando se tiene acceso a todas las navegaciones juntas. Por ejemplo:

<!--
        NavigationEntry navigationEntry = context.Entry(blog).Navigation("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2b)]

En la tabla siguiente se resumen las distintas formas de usar <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602> , <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602> y <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> :

| Miembro NavigationEntry                                                                                    | Descripción
|:----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.MemberEntry.CurrentValue?displayProperty=nameWithType> | Obtiene y establece el valor actual de la navegación. Se trata de la colección completa para las navegaciones de la colección.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Metadata?displayProperty=nameWithType> | <xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> metadatos para la navegación.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.IsLoaded?displayProperty=nameWithType> | Obtiene o establece un valor que indica si la entidad o colección relacionada se ha cargado completamente desde la base de datos.
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Load?displayProperty=nameWithType>     | Carga la entidad o colección relacionada desde la base de datos; Vea la [carga explícita de datos relacionados](xref:core/querying/related-data/explicit).
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Query?displayProperty=nameWithType>    | La consulta EF Core utilizaría para cargar esta navegación como un `IQueryable` que se puede componer más; vea la [carga explícita de datos relacionados](xref:core/querying/related-data/explicit).

### <a name="working-with-all-properties-of-an-entity"></a>Trabajar con todas las propiedades de una entidad

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType> Devuelve un <xref:System.Collections.Generic.IEnumerable%601> de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> para cada propiedad de la entidad. Se puede usar para realizar una acción para cada propiedad de la entidad. Por ejemplo, para establecer cualquier propiedad DateTime en `DateTime.Now` :

<!--
        foreach (var propertyEntry in context.Entry(blog).Properties)
        {
            if (propertyEntry.Metadata.ClrType == typeof(DateTime))
            {
                propertyEntry.CurrentValue = DateTime.Now;
            }
        }
-->
[!code-csharp[Work_with_all_properties_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_1)]

Además, EntityEntry contiene varios métodos para obtener y establecer todos los valores de propiedad al mismo tiempo. Estos métodos usan la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> clase, que representa una colección de propiedades y sus valores. PropertyValues se puede obtener para los valores actuales o originales, o para los valores que están almacenados actualmente en la base de datos. Por ejemplo:

<!--
        var currentValues = context.Entry(blog).CurrentValues;
        var originalValues = context.Entry(blog).OriginalValues;
        var databaseValues = context.Entry(blog).GetDatabaseValues();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2a)]

Estos objetos PropertyValues no son muy útiles por sí solos. Sin embargo, se pueden combinar para realizar operaciones comunes necesarias al manipular entidades. Esto resulta útil cuando se trabaja con objetos de transferencia de datos y cuando se resuelven [conflictos de simultaneidad optimista](xref:core/saving/concurrency). En las secciones siguientes se muestran algunos ejemplos.

#### <a name="setting-current-or-original-values-from-an-entity-or-dto"></a>Establecimiento de los valores actuales o originales de una entidad o DTO

Los valores actuales o originales de una entidad se pueden actualizar mediante la copia de los valores de otro objeto. Por ejemplo, considere un `BlogDto` objeto de transferencia de datos (DTO) con las mismas propiedades que el tipo de entidad:

<!--
public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
-->
[!code-csharp[BlogDto](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=BlogDto)]

Se puede usar para establecer los valores actuales de una entidad de la que se ha realizado un seguimiento mediante <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> :

<!--
        var blogDto = new BlogDto { Id = 1, Name = "1unicorn2" };

        context.Entry(blog).CurrentValues.SetValues(blogDto);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2b)]

Esta técnica se usa a veces al actualizar una entidad con valores obtenidos de una llamada de servicio o un cliente en una aplicación de n niveles. Tenga en cuenta que el objeto utilizado no tiene que ser del mismo tipo que la entidad, siempre y cuando tenga propiedades cuyos nombres coincidan con los de la entidad. En el ejemplo anterior, se usa una instancia de DTO `BlogDto` para establecer los valores actuales de una entidad de la que se ha realizado un seguimiento `Blog` .

Tenga en cuenta que las propiedades solo se marcarán como modificadas si el conjunto de valores difiere del valor actual.

#### <a name="setting-current-or-original-values-from-a-dictionary"></a>Establecer los valores actuales o originales de un diccionario

En el ejemplo anterior se establecen valores de una instancia de Entity o DTO. El mismo comportamiento está disponible cuando los valores de propiedad se almacenan como pares de nombre y valor en un diccionario. Por ejemplo:

<!--
        var blogDictionary = new Dictionary<string, object>
        {
            ["Id"] = 1,
            ["Name"] = "1unicorn2"
        };

        context.Entry(blog).CurrentValues.SetValues(blogDictionary);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2d)]

#### <a name="setting-current-or-original-values-from-the-database"></a>Establecer valores actuales o originales de la base de datos

Los valores actuales o originales de una entidad se pueden actualizar con los valores más recientes de la base de datos llamando a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> o <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> y usando el objeto devuelto para establecer los valores actuales o originales, o ambos. Por ejemplo:

<!--
        var databaseValues = context.Entry(blog).GetDatabaseValues();
        context.Entry(blog).CurrentValues.SetValues(databaseValues);
        context.Entry(blog).OriginalValues.SetValues(databaseValues);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2c)]

#### <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>Crear un objeto clonado que contenga valores actual, original o de base de datos

El objeto PropertyValues devuelto de CurrentValues, OriginalValues o GetDatabaseValues se puede usar para crear un clon de la entidad mediante <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType> . Por ejemplo:

<!--
var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2e)]

Tenga en cuenta que `ToObject` devuelve una nueva instancia de la que el DbContext no realiza un seguimiento. El objeto devuelto tampoco tiene ninguna relación establecida con otras entidades.

El objeto clonado puede ser útil para resolver problemas relacionados con las actualizaciones simultáneas en la base de datos, especialmente cuando se enlazan datos a objetos de un tipo determinado. Vea [simultaneidad optimista](xref:core/saving/concurrency) para obtener más información.

### <a name="working-with-all-navigations-of-an-entity"></a>Trabajar con todas las navegaciones de una entidad

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType> Devuelve un <xref:System.Collections.Generic.IEnumerable%601> de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> para cada navegación de la entidad. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> hacen lo mismo, pero se restringen a las navegaciones de referencia o de colección, respectivamente. Se puede usar para realizar una acción para cada navegación de la entidad. Por ejemplo, para forzar la carga de todas las entidades relacionadas:

<!--
        foreach (var navigationEntry in context.Entry(blog).Navigations)
        {
            navigationEntry.Load();
        }
-->
[!code-csharp[Work_with_all_navigations_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_navigations_of_an_entity_1)]

### <a name="working-with-all-members-of-an-entity"></a>Trabajar con todos los miembros de una entidad

Las propiedades normales y las propiedades de navegación tienen un estado y un comportamiento diferentes. Por lo tanto, es habitual procesar las navegaciones y las no navegaciones por separado, tal como se muestra en las secciones anteriores. Sin embargo, a veces puede resultar útil hacer algo con cualquier miembro de la entidad, independientemente de si se trata de una propiedad o navegación normal. <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> se proporcionan para este fin. Por ejemplo:

<!--
        foreach (var memberEntry in context.Entry(blog).Members)
        {
            Console.WriteLine(
                $"Member {memberEntry.Metadata.Name} is of type {memberEntry.Metadata.ClrType.ShortDisplayName()} and has value {memberEntry.CurrentValue}");
        }
-->
[!code-csharp[Work_with_all_members_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_members_of_an_entity_1)]

La ejecución de este código en un blog del ejemplo genera el siguiente resultado:

```output
Member Id is of type int and has value 1
Member Name is of type string and has value .NET Blog
Member Posts is of type IList<Post> and has value System.Collections.Generic.List`1[Post]
```

> [!TIP]
> La [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) muestra información como esta. La vista de depuración para todo el seguimiento de cambios se genera a partir del individuo <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> de cada entidad de la que se realiza el seguimiento.

## <a name="find-and-findasync"></a>Buscar y FindAsync

<xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> están diseñados para una búsqueda eficaz de una sola entidad cuando se conoce su clave principal. Buscar primero comprueba si ya se ha realizado el seguimiento de la entidad y, si es así, devuelve la entidad inmediatamente. Solo se realiza una consulta de base de datos si no se realiza un seguimiento de la entidad localmente. Por ejemplo, considere este código que llama a buscar dos veces para la misma entidad:

<!--
        using var context = new BlogsContext();

        Console.WriteLine("First call to Find...");
        var blog1 = context.Blogs.Find(1);

        Console.WriteLine($"...found blog {blog1.Name}");

        Console.WriteLine();
        Console.WriteLine("Second call to Find...");
        var blog2 = context.Blogs.Find(1);
        Debug.Assert(blog1 == blog2);

        Console.WriteLine("...returned the same instance without executing a query.");
-->
[!code-csharp[Find_and_FindAsync_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_1)]

La salida de este código (incluido el registro de EF Core) cuando se usa SQLite es la siguiente:

```output
First call to Find...
info: 12/29/2020 07:45:53.682 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (1ms) [Parameters=[@__p_0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
      SELECT "b"."Id", "b"."Name"
      FROM "Blogs" AS "b"
      WHERE "b"."Id" = @__p_0
      LIMIT 1
...found blog .NET Blog

Second call to Find...
...returned the same instance without executing a query.
```

Observe que la primera llamada no encuentra la entidad localmente y ejecuta una consulta de base de datos. Por el contrario, la segunda llamada devuelve la misma instancia sin consultar la base de datos porque ya se está realizando el seguimiento.

Find devuelve NULL si no se realiza un seguimiento de una entidad con la clave especificada localmente y no existe en la base de datos.

### <a name="composite-keys"></a>Claves compuestas

La búsqueda también se puede usar con claves compuestas. Por ejemplo, considere una `OrderLine` entidad con una clave compuesta que consta del identificador de pedido y el ID. de producto:

<!--
public class OrderLine
{
    public int OrderId { get; set; }
    public int ProductId { get; set; }

    //...
}
-->
[!code-csharp[OrderLine](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OrderLine)]

La clave compuesta debe estar configurada en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> para definir las partes clave _y su orden_. Por ejemplo:

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<OrderLine>()
            .HasKey(e => new { e.OrderId, e.ProductId });
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OnModelCreating)]

Observe que `OrderId` es la primera parte de la clave y `ProductId` es la segunda parte de la clave. Este orden debe utilizarse al pasar los valores de clave que se van a buscar. Por ejemplo:

<!--
        var orderline = context.OrderLines.Find(orderId, productId);
-->
[!code-csharp[Find_and_FindAsync_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_2)]

## <a name="using-changetrackerentries-to-access-all-tracked-entities"></a>Usar ChangeTracker. Entries para tener acceso a todas las entidades sometidas a seguimiento

Hasta ahora solo hemos tenido acceso a una sola vez <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> . <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> Devuelve un EntityEntry para cada entidad de la que el DbContext realiza un seguimiento actualmente. Por ejemplo:

<!--
        using var context = new BlogsContext();
        var blogs = context.Blogs.Include(e => e.Posts).ToList();

        foreach (var entityEntry in context.ChangeTracker.Entries())
        {
            Console.WriteLine($"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property("Id").CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a)]

Este código genera el siguiente resultado:

```output
Found Blog entity with ID 1
Found Post entity with ID 1
Found Post entity with ID 2
```

Observe que se devuelven entradas para blogs y publicaciones. En su lugar, los resultados se pueden filtrar a un tipo de entidad específico mediante la <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> sobrecarga genérica:

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<Post>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b)]

La salida de este código muestra que solo se devuelven publicaciones:

```output
Found Post entity with ID 1
Found Post entity with ID 2
```

Además, el uso de la sobrecarga genérica devuelve instancias genéricas <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> . Esto es lo que permite el acceso de tipo fluida a la `Id` propiedad en este ejemplo.

No es necesario que el tipo genérico usado para filtrar sea un tipo de entidad asignado; en su lugar, se puede usar un tipo base o una interfaz sin asignar. Por ejemplo, si todos los tipos de entidad del modelo implementan una interfaz que define su propiedad de clave:

<!--
public interface IEntityWithKey
{
    int Id { get; set; }
}
-->
[!code-csharp[IEntityWithKey](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=IEntityWithKey)]

Después, esta interfaz se puede usar para trabajar con la clave de cualquier entidad de la que se ha realizado un seguimiento de forma fuertemente tipada. Por ejemplo:

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<IEntityWithKey>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c)]

## <a name="using-dbsetlocal-to-query-tracked-entities"></a>Usar DbSet. local para consultar las entidades sometidas a seguimiento

Las consultas de EF Core se ejecutan siempre en la base de datos y solo devuelven las entidades que se han guardado en la base de datos. <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> proporciona un mecanismo para consultar a DbContext para entidades de las que se ha realizado un seguimiento local.

Dado que `DbSet.Local` se utiliza para consultar las entidades de las que se realiza un seguimiento, es habitual cargar entidades en DbContext y, después, trabajar con esas entidades cargadas. Esto es especialmente cierto para el enlace de datos, pero también puede ser útil en otras situaciones. Por ejemplo, en el código siguiente, la base de datos se consulta primero para todos los blogs y publicaciones. El <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A> método de extensión se utiliza para ejecutar esta consulta con los resultados seguidos por el contexto sin devolverse directamente a la aplicación. (El uso de `ToList` o similar tiene el mismo efecto, pero con la sobrecarga de crear la lista devuelta, que no se necesita aquí). A continuación, en el ejemplo `DbSet.Local` se usa para acceder a las entidades de las que se realiza un seguimiento local:

<!--
        using var context = new BlogsContext();

        context.Blogs.Include(e => e.Posts).Load();

        foreach (var blog in context.Blogs.Local)
        {
            Console.WriteLine($"Blog: {blog.Name}");
        }

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_1)]

Tenga en cuenta que, a diferencia <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> de, `DbSet.Local` devuelve directamente las instancias de la entidad. Por supuesto, una EntityEntry puede obtenerse siempre para la entidad devuelta mediante una llamada a <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> .

### <a name="the-local-view"></a>La vista local

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> Devuelve una vista de entidades de las que se realiza un seguimiento local que refleja el actual <xref:Microsoft.EntityFrameworkCore.EntityState> de esas entidades. En concreto, esto significa que:

- `Added` se incluyen las entidades. Tenga en cuenta que este no es el caso de las consultas de EF Core normales, ya que las `Added` entidades aún no existen en la base de datos y, por tanto, nunca se devuelven en una consulta de base de datos.
- `Deleted` se excluyen las entidades. Tenga en cuenta que esto no es el caso de las consultas de EF Core normales, ya que las `Deleted` entidades todavía existen en la base de datos y _, por_ lo tanto, las consultas de base de datos las devuelven.

Todo esto significa que `DbSet.Local` es una vista sobre los datos que refleja el estado conceptual actual del gráfico de entidades, con las `Added` entidades incluidas y las `Deleted` entidades excluidas. Esto coincide con el estado de la base de datos que se espera que sea después de llamar a SaveChanges.

Esta suele ser la vista ideal para el enlace de datos, ya que presenta al usuario los datos a medida que los entienden en función de los cambios realizados por la aplicación.

En el código siguiente se muestra el marcado de una publicación como `Deleted` y, a continuación, la adición de una nueva publicación, que se marca como `Added` :

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Remove(posts[1]);

        context.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_2)]

El resultado de este código es:

```output
Local view after loading posts:
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing F# 5
  Post: Announcing .NET 5.0
Local view after adding and deleting posts:
  Post: What’s next for System.Text.Json?
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing .NET 5.0
```

Tenga en cuenta que la publicación eliminada se quita de la vista local y se incluye la publicación agregada.

### <a name="using-local-to-add-and-remove-entities"></a>Usar local para agregar y quitar entidades

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> devuelve una instancia de <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601>. Se trata de una implementación de <xref:System.Collections.Generic.ICollection%601> que genera y responde a las notificaciones cuando se agregan y quitan entidades de la colección. (Este es el mismo concepto que <xref:System.Collections.ObjectModel.ObservableCollection%601> , pero se implementa como una proyección sobre las entradas de seguimiento de cambios de EF Core existentes, en lugar de como una colección independiente).

Las notificaciones de la vista local se enlazan al seguimiento de cambios de DbContext de modo que la vista local permanece sincronizada con DbContext. Concretamente:

- Agregar una nueva entidad a `DbSet.Local` hace que el DbContext realice el seguimiento, normalmente en el `Added` Estado. (Si la entidad ya tiene un valor de clave generado, se realiza el seguimiento como `Unchanged` en su lugar).
- Al quitar una entidad de `DbSet.Local` , se marca como `Deleted` .
- Una entidad que se convierte en el seguimiento por DbContext aparecerá automáticamente en la `DbSet.Local` colección. Por ejemplo, la ejecución de una consulta para incorporar más entidades automáticamente hace que se actualice la vista local.
- Una entidad marcada como se `Deleted` quitará automáticamente de la colección local.

Esto significa que la vista local se puede usar para manipular las entidades de las que se ha realizado un seguimiento agregando y quitando de la colección. Por ejemplo, permite modificar el código de ejemplo anterior para agregar y quitar entradas de la colección local:

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Posts.Local.Remove(posts[1]);

        context.Posts.Local.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_3](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_3)]

La salida permanece sin cambios en el ejemplo anterior porque los cambios realizados en la vista local se sincronizan con DbContext.

### <a name="using-the-local-view-for-windows-forms-or-wpf-data-binding"></a>Usar la vista local para el enlace de datos de Windows Forms o WPF

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> constituye la base para el enlace de datos a entidades EF Core. Sin embargo, tanto Windows Forms como WPF funcionan mejor cuando se usan con el tipo específico de colección de notificaciones que esperan. La vista local admite la creación de estos tipos de colección específicos:

- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType> Devuelve un <xref:System.Collections.ObjectModel.ObservableCollection%601> para el enlace de datos de WPF.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType> Devuelve un <xref:System.ComponentModel.BindingList%601> para el enlace de datos Windows Forms.

Por ejemplo:

<!--
        ObservableCollection<Post> observableCollection = context.Posts.Local.ToObservableCollection();
        BindingList<Post> bindingList = context.Posts.Local.ToBindingList();
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_4](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_4)]

Vea Introducción [a WPF](xref:core/get-started/wpf) para obtener más información sobre el enlace de datos de wpf con EF Core.

> [!TIP]
> La vista local de una instancia de DbSet determinada se crea de forma diferida cuando se obtiene acceso por primera vez y después se almacena en caché. La creación de LocalView es rápida y no usa mucha memoria. Sin embargo, llama a [DetectChanges](xref:core/change-tracking/change-detection), que puede ser lento para un gran número de entidades. Las colecciones creadas por `ToObservableCollection` y `ToBindingList` también se crean de forma diferida y, a continuación, se almacenan en caché. Ambos métodos crean colecciones nuevas, que pueden ser lentas y usar una gran cantidad de memoria cuando se usan miles de entidades.
