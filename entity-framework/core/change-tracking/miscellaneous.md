---
title: 'Características de Change Tracking adicionales: EF Core'
description: Varias características y escenarios que implican EF Core el seguimiento de cambios
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/miscellaneous
ms.openlocfilehash: 63d96227b6862e920d900a5cc3f1f85d7c6d85ac
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024438"
---
# <a name="additional-change-tracking-features"></a>Características de Change Tracking adicionales

En este documento se tratan varias características y escenarios que implican el seguimiento de cambios.

> [!TIP]
> En este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de EF Core. Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información sobre estos temas.

> [!TIP]
> Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).

## <a name="add-versus-addasync"></a>Diferencias entre `Add` y `AddAsync`

Entity Framework Core (EF Core) proporciona métodos asincrónicos cada vez que se usa ese método, se puede producir una interacción con la base de datos. También se proporcionan métodos sincrónicos para evitar la sobrecarga cuando se usan bases de datos que no admiten el acceso asincrónico de alto rendimiento.

<xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> normalmente no tienen acceso a la base de datos, ya que estos métodos solo inician el seguimiento de las entidades. Sin embargo, algunas formas de generación de valores _pueden_ tener acceso a la base de datos para generar un valor de clave. El único generador de valores que realiza esta y se distribuye con EF Core es <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601> . No es habitual utilizar este generador. nunca se configura de forma predeterminada. Esto significa que la mayoría de las aplicaciones deben usar `Add` , y no `AddAsync` .

Otros métodos similares como `Update` , `Attach` y `Remove` no tienen sobrecargas asincrónicas porque nunca generan nuevos valores de clave y, por lo tanto, no necesitan tener acceso a la base de datos.

## <a name="addrange-updaterange-attachrange-and-removerange"></a>`AddRange`, `UpdateRange`, `AttachRange` y `RemoveRange`.

<xref:Microsoft.EntityFrameworkCore.DbSet%601> y <xref:Microsoft.EntityFrameworkCore.DbContext> proporcionan versiones alternativas de `Add` , `Update` , `Attach` y `Remove` que aceptan varias instancias en una sola llamada. Estos métodos son <xref:Microsoft.EntityFrameworkCore.DbSet%601.AddRange%2A> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.UpdateRange%2A> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.AttachRange%2A> y, <xref:Microsoft.EntityFrameworkCore.DbSet%601.RemoveRange%2A> respectivamente.

Estos métodos se proporcionan por comodidad. El uso de un método "Range" tiene la misma funcionalidad que varias llamadas al método equivalente que no es de intervalo. No hay ninguna diferencia de rendimiento significativa entre los dos enfoques.

> [!NOTE]
> Esto es diferente de EF6, donde se `AddRange` llama a y a `Add` ambos automáticamente `DetectChanges` , pero la llamada `Add` varias veces hizo que DetectChanges se llamara varias veces en lugar de una vez. Esto hace que sea `AddRange` más eficaz en EF6. En EF Core, ninguno de estos métodos llama automáticamente a `DetectChanges` .

## <a name="dbcontext-versus-dbset-methods"></a>DbContext frente a métodos DbSet

Muchos métodos, incluidos `Add` , `Update` , `Attach` y `Remove` , tienen implementaciones en <xref:Microsoft.EntityFrameworkCore.DbSet%601> y <xref:Microsoft.EntityFrameworkCore.DbContext> . Estos métodos tienen _exactamente el mismo comportamiento para los_ tipos de entidad normales. Esto se debe a que el tipo CLR de la entidad se asigna a un solo tipo de entidad del modelo de EF Core. Por lo tanto, el tipo CLR define totalmente Dónde encaja la entidad en el modelo, por lo que el DbSet que se va a usar se puede determinar de forma implícita.

La excepción a esta regla es cuando se usan tipos de entidad de tipo compartido, que se introdujeron en EF Core 5,0, principalmente para entidades de combinación de varios a varios. Cuando se usa un tipo de entidad de tipo compartido, primero se debe crear un DbSet para el tipo de modelo de EF Core que se está usando. Los métodos como `Add` , `Update` , `Attach` y `Remove` se pueden usar en DbSet sin ninguna ambigüedad en cuanto a qué tipo de modelo de EF Core se está usando.

Los tipos de entidad de tipo compartido se usan de forma predeterminada para las entidades de combinación en las relaciones de varios a varios. Un tipo de entidad de tipo compartido también puede configurarse explícitamente para su uso en una relación de varios a varios. Por ejemplo, el código siguiente configura `Dictionary<string, int>` como un tipo de entidad de combinación:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .SharedTypeEntity<Dictionary<string, int>>(
                    "PostTag",
                    b =>
                        {
                            b.IndexerProperty<int>("TagId");
                            b.IndexerProperty<int>("PostId");
                        });

            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<Dictionary<string, int>>(
                    "PostTag",
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany());
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=OnModelCreating)]

Al [cambiar las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes) , se muestra cómo asociar dos entidades mediante el seguimiento de una nueva instancia de entidad join. El código siguiente hace esto para el `Dictionary<string, int>` tipo de entidad de tipo compartido que se usa para la entidad de combinación:

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            var joinEntitySet = context.Set<Dictionary<string, int>>("PostTag");
            var joinEntity = new Dictionary<string, int>
            {
                ["PostId"] = post.Id,
                ["TagId"] = tag.Id
            };
            joinEntitySet.Add(joinEntity);

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[DbContext_versus_DbSet_methods_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=DbContext_versus_DbSet_methods_1)]

Observe que <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> se usa para crear un DbSet para el `PostTag` tipo de entidad. Este DbSet se puede usar para llamar a `Add` con la nueva instancia de la entidad join.

> [!IMPORTANT]
> El tipo CLR que se usa para los tipos de entidad de combinación por Convención puede cambiar en futuras versiones para mejorar el rendimiento. No dependa de ningún tipo de entidad de combinación concreta, a menos que se haya configurado explícitamente como se hace `Dictionary<string, int>` en el código anterior.

## <a name="property-versus-field-access"></a>Propiedad frente al acceso a campos

A partir de EF Core 3,0, el acceso a las propiedades de la entidad usa el campo de respaldo de la propiedad de forma predeterminada. Esto es eficaz y evita que se desencadenen efectos secundarios al llamar a captadores y establecedores de propiedad. Por ejemplo, este es el modo en que la carga diferida puede evitar desencadenar bucles infinitos. Vea [campos de respaldo](xref:core/modeling/backing-field) para obtener más información sobre cómo configurar los campos de respaldo en el modelo.

A veces puede ser deseable que EF Core genere efectos secundarios al modificar los valores de propiedad. Por ejemplo, cuando se enlazan datos a entidades, el establecimiento de una propiedad puede generar notificaciones al U.I. que no se producen al establecer el campo directamente. Esto se puede lograr cambiando el <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> para:

- Todos los tipos de entidad del modelo mediante <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- Todas las propiedades y navegaciones de un tipo de entidad específico mediante <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- Una propiedad específica mediante <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>
- Una navegación específica mediante <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType>

Los modos de acceso de propiedad y harán que `Field` `PreferField` EF Core tenga acceso al valor de propiedad a través de su campo de respaldo. Del mismo modo, y hará que `Property` `PreferProperty` EF Core tenga acceso al valor de propiedad a través de su captador y establecedor.

Si `Field` `Property` se usan o y EF Core no pueden tener acceso al valor mediante el campo o captador/establecedor de propiedad respectivamente, EF Core producirá una excepción. Esto garantiza que EF Core siempre use el acceso de campo o propiedad cuando cree que es.

Por otro lado, los `PreferField` modos y revertirán `PreferProperty` al uso de la propiedad o el campo de respaldo, respectivamente, si no es posible usar el acceso preferido. `PreferField` es el valor predeterminado de EF Core 3,0 en adelante. Esto significa que EF Core usará los campos siempre que pueda, pero no producirá un error si se debe tener acceso a una propiedad a través de su captador o establecedor en su lugar.

`FieldDuringConstruction` y `PreferFieldDuringConstruction` configure EF Core para usar los campos de respaldo _solo al crear instancias de entidad_. Esto permite que las consultas se ejecuten sin efectos secundarios de captador y establecedor, mientras que los cambios de propiedad posteriores de EF Core provocarán estos efectos secundarios. `PreferFieldDuringConstruction` era el valor predeterminado antes de EF Core 3,0.

Los diferentes modos de acceso a propiedades se resumen en la tabla siguiente:

| PropertyAccessMode              | Referencia | Preferencia de creación de entidades | Reserva | Creación de entidades de reserva
|:--------------------------------|------------|------------------------------|----------|---------------------------
| `Field`                         | Campo      | Campo                        | Produce   | Produce
| `Property`                      | Propiedad   | Propiedad                     | Produce   | Produce
| `PreferField`                   | Campo      | Campo                        | Propiedad | Propiedad
| `PreferProperty`                | Propiedad   | Propiedad                     | Campo    | Campo
| `FieldDuringConstruction`       | Propiedad   | Campo                        | Campo    | Produce
| `PreferFieldDuringConstruction` | Propiedad   | Campo                        | Campo    | Propiedad

## <a name="temporary-values"></a>Valores temporales

EF Core crea valores de clave temporales al realizar el seguimiento de nuevas entidades que tendrán valores de clave reales generados por la base de datos cuando se llame a SaveChanges. Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener información general sobre cómo se usan estos valores temporales.

### <a name="accessing-temporary-values"></a>Obtener acceso a valores temporales

A partir de EF Core 3,0, los valores temporales se almacenan en el seguimiento de cambios y no se establecen directamente en instancias de entidad. Sin embargo, estos valores temporales _se_ exponen al usar los distintos mecanismos para [tener acceso a las entidades](xref:core/change-tracking/entity-entries)de las que se ha realizado un seguimiento. Por ejemplo, el código siguiente obtiene acceso a un valor temporal mediante <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType> :

<!--
        using var context = new BlogsContext();

        var blog = new Blog { Name = ".NET Blog" };

        context.Add(blog);

        Console.WriteLine($"Blog.Id set on entity is {blog.Id}");
        Console.WriteLine($"Blog.Id tracked by EF is {context.Entry(blog).Property(e => e.Id).CurrentValue}");
-->
[!code-csharp[Temporary_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_1)]

El resultado de este código es:

```output
Blog.Id set on entity is 0
Blog.Id tracked by EF is -2147482643
```

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> se puede usar para comprobar los valores temporales.

### <a name="manipulating-temporary-values"></a>Manipular valores temporales

A veces resulta útil trabajar explícitamente con valores temporales. Por ejemplo, se puede crear una colección de nuevas entidades en un cliente web y, a continuación, volver a serializarse en el servidor. Los valores de clave externa son una manera de configurar relaciones entre estas entidades. En el código siguiente se usa este enfoque para asociar un gráfico de nuevas entidades por clave externa, a la vez que se permite que se generen valores de clave reales cuando se llama a SaveChanges.

<!--
            var blogs = new List<Blog>
            {
                new Blog { Id = -1, Name = ".NET Blog" },
                new Blog { Id = -2, Name = "Visual Studio Blog" }
            };

            var posts = new List<Post>()
            {
                new Post
                {
                    Id = -1,
                    BlogId = -1,
                    Title = "Announcing the Release of EF Core 5.0",
                    Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                },
                new Post
                {
                    Id = -2,
                    BlogId = -2,
                    Title = "Disassembly improvements for optimized managed debugging",
                    Content = "If you are focused on squeezing out the last bits of performance for your .NET service or..."
                }
            };

            using var context = new BlogsContext();

            foreach (var blog in blogs)
            {
                context.Add(blog).Property(e => e.Id).IsTemporary = true;
            }

            foreach (var post in posts)
            {
                context.Add(post).Property(e => e.Id).IsTemporary = true;
            }

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Temporary_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_2)]

Tenga en lo siguiente:

- Los números negativos se utilizan como valores de clave temporales; Esto no es necesario, pero es una Convención común para evitar conflictos de claves.
- La `Post.BlogId` propiedad FK tiene asignado el mismo valor negativo que la clave principal del blog asociado.
- Los valores PK se marcan como temporales estableciendo <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> una vez que se realiza el seguimiento de cada entidad. Esto es necesario porque se supone que cualquier valor de clave proporcionado por la aplicación es un valor de clave real.

Al examinar la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) antes de llamar a SaveChanges, se muestra que los valores PK están marcados como temporales y que las publicaciones están asociadas a los blogs correctos, incluida la corrección de las navegaciones:

```output
Blog {Id: -2} Added
  Id: -2 PK Temporary
  Name: 'Visual Studio Blog'
  Posts: [{Id: -2}]
Blog {Id: -1} Added
  Id: -1 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -1}]
Post {Id: -2} Added
  Id: -2 PK Temporary
  BlogId: -2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: -2}
  Tags: []
Post {Id: -1} Added
  Id: -1 PK Temporary
  BlogId: -1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -1}
```

Después de llamar a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> , estos valores temporales se han reemplazado por valores reales generados por la base de datos:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Posts: [{Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
```

## <a name="working-with-default-values"></a>Trabajar con valores predeterminados

EF Core permite que una propiedad obtenga su valor predeterminado de la base de datos cuando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> se llama a. Al igual que con los valores de clave generados, EF Core solo usará un valor predeterminado de la base de datos si no se ha establecido explícitamente ningún valor. Por ejemplo, considere el siguiente tipo de entidad:

<!--
    public class Token
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime ValidFrom { get; set; }
    }
-->
[!code-csharp[Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Token)]

La `ValidFrom` propiedad está configurada para obtener un valor predeterminado de la base de datos:

<!--
        modelBuilder
            .Entity<Token>()
            .Property(e => e.ValidFrom)
            .HasDefaultValueSql("CURRENT_TIMESTAMP");
-->
[!code-csharp[OnModelCreating_Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Token)]

Al insertar una entidad de este tipo, EF Core permitirá a la base de datos generar el valor a menos que se haya establecido un valor explícito en su lugar. Por ejemplo:

<!--
            using var context = new BlogsContext();

            context.AddRange(
                new Token { Name = "A" },
                new Token { Name = "B", ValidFrom = new DateTime(1111, 11, 11, 11, 11, 11)});

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Working_with_default_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_1)]

En la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) se muestra que el primer token fue `ValidFrom` generado por la base de datos, mientras que el segundo token usó el valor establecido explícitamente:

```output
Token {Id: 1} Unchanged
  Id: 1 PK
  Name: 'A'
  ValidFrom: '12/30/2020 6:36:06 PM'
Token {Id: 2} Unchanged
  Id: 2 PK
  Name: 'B'
  ValidFrom: '11/11/1111 11:11:11 AM'
```

> [!NOTE]
> El uso de valores predeterminados de base de datos requiere que la columna de base de datos tenga una restricción de valor predeterminado. Esto lo hace automáticamente EF Core migraciones cuando se usa <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> o <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A> . Asegúrese de crear la restricción predeterminada en la columna de otra manera cuando no use migraciones de EF Core.

### <a name="using-nullable-properties"></a>Usar propiedades que aceptan valores NULL

EF Core puede determinar si se ha establecido una propiedad comparando el valor de la propiedad con el valor predeterminado de CLR para ese tipo. Esto funciona bien en la mayoría de los casos, pero significa que el valor predeterminado de CLR no se puede insertar explícitamente en la base de datos. Por ejemplo, considere una entidad con una propiedad de entero:

<!--
public class Foo1
{
    public int Id { get; set; }
    public int Count { get; set; }
}
-->
[!code-csharp[Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo1)]

Donde esa propiedad está configurada para tener una base de datos predeterminada de-1:

<!--
        modelBuilder
            .Entity<Foo1>()
            .Property(e => e.Count)
            .HasDefaultValue(-1);
-->
[!code-csharp[OnModelCreating_Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Foo1)]

La intención es que se use el valor predeterminado de-1 siempre que no se establezca un valor explícito. Sin embargo, no se puede distinguir si se establece el valor en 0 (el valor predeterminado de CLR para enteros) en EF Core no se establece ningún valor, lo que significa que no es posible insertar 0 para esta propiedad. Por ejemplo:

<!--
        using var context = new BlogsContext();

        var fooA = new Foo1 { Count = 10 };
        var fooB = new Foo1 { Count = 0 };
        var fooC = new Foo1 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == -1); // Not what we want!
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_2)]

Tenga en cuenta que la instancia en la que `Count` se estableció explícitamente en 0 todavía obtiene el valor predeterminado de la base de datos, que no es lo que se pretendía. Una manera fácil de solucionarlo es hacer que la `Count` propiedad acepte valores NULL:

<!--
public class Foo2
{
    public int Id { get; set; }
    public int? Count { get; set; }
}
-->
[!code-csharp[Foo2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo2)]

Esto hace que el valor predeterminado de CLR sea NULL, en lugar de 0, lo que significa que se insertará 0 cuando se establezca explícitamente:

<!--
        using var context = new BlogsContext();

        var fooA = new Foo2 { Count = 10 };
        var fooB = new Foo2 { Count = 0 };
        var fooC = new Foo2 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == 0);
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_3)]

### <a name="using-nullable-backing-fields"></a>Usar campos de respaldo que aceptan valores NULL

> [!NOTE]
> Este patrón de campo de respaldo que acepta valores NULL es compatible con EF Core 5,0 y versiones posteriores.

El problema de hacer que la propiedad acepte valores NULL, por lo que puede que no sea conceptualmente aceptable en el modelo de dominio. Forzar que la propiedad acepte valores NULL, por tanto, pone en peligro el modelo.

A partir de EF Core 5,0, la propiedad puede dejarse que no admita valores NULL, y solo el campo de respaldo acepta valores NULL. Por ejemplo:

<!--
public class Foo3
{
    public int Id { get; set; }

    private int? _count;
    public int Count
    {
        get => _count ?? -1;
        set => _count = value;
    }
}
-->
[!code-csharp[Foo3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo3)]

Esto permite que se inserte el valor predeterminado de CLR (0) si la propiedad se establece explícitamente en 0, aunque no es necesario exponer la propiedad como que acepta valores NULL en el modelo de dominio. Por ejemplo:

<!--
            using var context = new BlogsContext();

            var fooA = new Foo3 { Count = 10 };
            var fooB = new Foo3 { Count = 0 };
            var fooC = new Foo3 { };

            context.AddRange(fooA, fooB, fooC);
            context.SaveChanges();

            Debug.Assert(fooA.Count == 10);
            Debug.Assert(fooB.Count == 0);
            Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_4](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_4)]

#### <a name="nullable-backing-fields-for-bool-properties"></a>Campos de respaldo que aceptan valores NULL para las propiedades bool

Este patrón es especialmente útil cuando se usan propiedades bool con valores predeterminados generados por el almacén. Dado que el valor predeterminado de CLR para `bool` es "false", significa que "false" no se puede insertar explícitamente mediante el patrón normal. Por ejemplo, considere un `User` tipo de entidad:

<!--
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    private bool? _isAuthorized;
    public bool IsAuthorized
    {
        get => _isAuthorized ?? true;
        set => _isAuthorized = value;
    }
}
-->
[!code-csharp[User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=User)]

La `IsAuthorized` propiedad se configura con un valor predeterminado de la base de datos "true":

<!--
        modelBuilder
            .Entity<User>()
            .Property(e => e.IsAuthorized)
            .HasDefaultValue(true);
-->
[!code-csharp[OnModelCreating_User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_User)]

La `IsAuthorized` propiedad se puede establecer en "true" o "false" explícitamente antes de la inserción, o bien se puede dejar sin establecer, en cuyo caso se usará el valor predeterminado de la base de datos:

<!--
        using var context = new BlogsContext();

        var userA = new User { Name = "Mac" };
        var userB = new User { Name = "Alice", IsAuthorized = true };
        var userC = new User { Name = "Baxter", IsAuthorized = false }; // Always deny Baxter access!

        context.AddRange(userA, userB, userC);

        context.SaveChanges();
-->
[!code-csharp[Working_with_default_values_5](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_5)]

La salida de SaveChanges cuando se usa SQLite muestra que el valor predeterminado de la base de datos se usa para Mac, mientras que los valores explícitos están establecidos para Alice y Baxter:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='Mac' (Size = 3)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("Name")
VALUES (@p0);
SELECT "Id", "IsAuthorized"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='True' (DbType = String), @p1='Alice' (Size = 5)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='False' (DbType = String), @p1='Baxter' (Size = 6)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

### <a name="schema-defaults-only"></a>Solo valores predeterminados de esquema

A veces resulta útil tener valores predeterminados en el esquema de la base de datos creado por EF Core migraciones sin EF Core que nunca use estos valores para las inserciones. Esto puede lograrse configurando la propiedad como, <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> por ejemplo:

<!--
        modelBuilder
            .Entity<Bar>()
            .Property(e => e.Count)
            .HasDefaultValue(-1)
            .ValueGeneratedNever();
-->
[!code-csharp[OnModelCreating_Bar](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Bar)]
