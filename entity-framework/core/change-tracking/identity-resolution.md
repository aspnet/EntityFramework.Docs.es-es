---
title: 'Resolución de identidad: EF Core'
description: Resolución de varias instancias de entidad en una sola instancia mediante valores de clave principal
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/identity-resolution
ms.openlocfilehash: 24b2fbeea5f740dd2830676bfe8a49720c2b86a9
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024464"
---
# <a name="identity-resolution-in-ef-core"></a>Resolución de identidades en EF Core

Un <xref:Microsoft.EntityFrameworkCore.DbContext> solo puede realizar el seguimiento de una instancia de entidad con cualquier valor de clave principal determinado. Esto significa que se deben resolver varias instancias de una entidad con el mismo valor de clave en una sola instancia. Esto se denomina "resolución de identidad". La resolución de identidad garantiza Entity Framework Core (EF Core) realiza el seguimiento de un gráfico coherente sin ambigüedades sobre las relaciones o los valores de propiedad de las entidades.

> [!TIP]
> En este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de EF Core. Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información sobre estos temas.

> [!TIP]
> Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/IdentityResolutionInEFCore).

## <a name="introduction"></a>Introducción

En el código siguiente se consulta una entidad y, a continuación, se intenta adjuntar una instancia diferente con el mismo valor de clave principal:

<!--
            using var context = new BlogsContext();

            var blogA = context.Blogs.Single(e => e.Id == 1);
            var blogB = new Blog { Id = 1, Name = ".NET Blog (All new!)" };

            try
            {
                context.Update(blogB); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Identity_Resolution_in_EF_Core_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Identity_Resolution_in_EF_Core_1)]

La ejecución de este código produce la siguiente excepción:

> System. InvalidOperationException: no se puede realizar el seguimiento de la instancia del tipo de entidad ' blog ' porque ya se está realizando el seguimiento de otra instancia con el valor de clave ' {ID: 1} '. Al adjuntar entidades existentes, asegúrese de que solo se adjunta una instancia de entidad con un valor de clave determinado.

EF Core requiere una única instancia porque:

- Los valores de propiedad pueden ser diferentes en varias instancias. Al actualizar la base de datos, EF Core necesita saber qué valores de propiedad se deben usar.
- Las relaciones con otras entidades pueden ser diferentes entre varias instancias. Por ejemplo, "Bloga" puede estar relacionado con una colección diferente de entradas que "blogB".

La excepción anterior se encuentra normalmente en estas situaciones:

- Al intentar actualizar una entidad
- Al intentar realizar el seguimiento de un gráfico serializado de entidades
- Cuando no se puede establecer un valor de clave que no se genera automáticamente
- Cuando se reutiliza una instancia de DbContext para varias unidades de trabajo

En las secciones siguientes se describe cada una de estas situaciones.

## <a name="updating-an-entity"></a>Actualización de una entidad

Hay varios enfoques diferentes para actualizar una entidad con nuevos valores, como se describe en [Change Tracking en EF Core](xref:core/change-tracking/index) y [realizar un seguimiento explícito](xref:core/change-tracking/explicit-tracking)de las entidades. Estos enfoques se describen a continuación en el contexto de la resolución de identidades. Un aspecto importante que se debe tener en cuenta es que cada uno de los enfoques utiliza una consulta o una llamada a uno de `Update` o `Attach` , pero **_nunca a ambos_**.

### <a name="call-update"></a>Actualizar llamada

A menudo, la entidad que se va a actualizar no procede de una consulta en DbContext que vamos a usar para SaveChanges. Por ejemplo, en una aplicación Web, se puede crear una instancia de la entidad a partir de la información de una solicitud POST. La manera más sencilla de controlarlo es usar <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> o <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType> . Por ejemplo:

<!--
    public static void UpdateFromHttpPost1(Blog blog)
    {
        using var context = new BlogsContext();

        context.Update(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_1)]

En este caso:

- Solo se crea una instancia de la entidad.
- La instancia de la entidad **no** se consulta desde la base de datos como parte de la realización de la actualización.
- Todos los valores de propiedad se actualizarán en la base de datos, independientemente de si realmente han cambiado o no.
- Se realiza una operación de ida y vuelta de base de datos.

### <a name="query-then-apply-changes"></a>Después, la consulta aplica los cambios

Normalmente, no se sabe qué valores de propiedad se han cambiado realmente cuando se crea una entidad a partir de información en una solicitud POST o similar. A menudo, basta con actualizar todos los valores de la base de datos, como hicimos en el ejemplo anterior. Sin embargo, si la aplicación administra muchas entidades y solo un número pequeño de ellos tiene cambios reales, puede ser útil limitar las actualizaciones enviadas. Esto puede lograrse mediante la ejecución de una consulta para realizar el seguimiento de las entidades tal como existen actualmente en la base de datos y, a continuación, aplicar los cambios a estas entidades sometidas a seguimiento. Por ejemplo:

<!--
    public static void UpdateFromHttpPost2(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        trackedBlog.Name = blog.Name;
        trackedBlog.Summary = blog.Summary;

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_2)]

En este caso:

- Solo se realiza el seguimiento de una única instancia de la entidad; el devuelto por la consulta por la base de datos `Find` .
- `Update`, `Attach` ,, etc. **no** se usan.
- En la base de datos solo se actualizan los valores de propiedad que han cambiado realmente.
- Se realizan dos viajes de ida y vuelta de base de datos.

EF Core tiene algunas aplicaciones auxiliares para transferir valores de propiedad como esta. Por ejemplo, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> copiará todos los valores del objeto especificado y los establecerá en el objeto sometido a seguimiento:

<!--
    public static void UpdateFromHttpPost3(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_3)]

`SetValues` acepta varios tipos de objetos, incluidos los objetos de transferencia de datos (DTO) con nombres de propiedad que coinciden con las propiedades del tipo de entidad. Por ejemplo:

<!--
    public static void UpdateFromHttpPost4(BlogDto dto)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(dto.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(dto);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_4)]

O un diccionario con entradas de nombre/valor para los valores de propiedad:

<!--
    public static void UpdateFromHttpPost5(Dictionary<string, object> propertyValues)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(propertyValues["Id"]);

        context.Entry(trackedBlog).CurrentValues.SetValues(propertyValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_5)]

Vea obtener acceso a entidades de las que se ha [realizado un seguimiento](xref:core/change-tracking/entity-entries) para obtener más información sobre cómo trabajar con valores de propiedad como este.

### <a name="use-original-values"></a>Usar valores originales

Hasta ahora, cada enfoque ha ejecutado una consulta antes de realizar la actualización o ha actualizado todos los valores de propiedad independientemente de si han cambiado o no. Para actualizar solo los valores que han cambiado sin realizar consultas como parte de la actualización, se requiere información específica sobre los valores de propiedad que han cambiado. Una forma habitual de obtener esta información es devolver los valores actuales y originales en HTTP POST o similar. Por ejemplo:

<!--
    public static void UpdateFromHttpPost6(Blog blog, Dictionary<string, object> originalValues)
    {
        using var context = new BlogsContext();

        context.Attach(blog);
        context.Entry(blog).OriginalValues.SetValues(originalValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_6](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_6)]

En este código, la entidad con valores modificados se adjunta primero. Esto hace que EF Core realice el seguimiento de la entidad en el `Unchanged` Estado; es decir, sin valores de propiedad marcados como modificados. A continuación, el Diccionario de valores originales se aplica a esta entidad sometida a seguimiento. Esto marcará como propiedades modificadas con distintos valores actuales y originales. Las propiedades que tienen los mismos valores actual y original no se marcan como modificadas.

En este caso:

- Solo se realiza el seguimiento de una sola instancia de la entidad, mediante Attach.
- La instancia de la entidad **no** se consulta desde la base de datos como parte de la realización de la actualización.
- La aplicación de los valores originales garantiza que solo se actualicen en la base de datos los valores de propiedad que hayan cambiado realmente.
- Se realiza una operación de ida y vuelta de base de datos.

Al igual que con los ejemplos de la sección anterior, los valores originales no tienen que pasarse como un diccionario; también funcionará una instancia de entidad o DTO.

> [!TIP]
> Aunque este enfoque tiene características atractivas, requiere el envío de los valores originales de la entidad al cliente web y desde él. Considere detenidamente si esta complejidad adicional merece las ventajas; para muchas aplicaciones, uno de los enfoques más sencillos es más pragmático.

## <a name="attaching-a-serialized-graph"></a>Asociar un gráfico serializado

EF Core funciona con gráficos de entidades conectadas a través de claves externas y propiedades de navegación, como se describe en [cambiar las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes). Si estos gráficos se crean fuera de EF Core mediante, por ejemplo, de un archivo JSON, pueden tener varias instancias de la misma entidad. Estos duplicados deben resolverse en instancias únicas antes de que se pueda realizar el seguimiento del gráfico.

### <a name="graphs-with-no-duplicates"></a>Gráficos sin duplicados

Antes de continuar, es importante reconocer que:

- Los serializadores suelen tener opciones para administrar bucles e instancias duplicadas en el gráfico.
- La elección del objeto que se usa como la raíz del gráfico puede ayudar a reducir o quitar los duplicados.

Si es posible, use las opciones de serialización y elija raíces que no produzcan duplicados. Por ejemplo, el código siguiente usa [JSON.net](https://www.nuget.org/packages/Newtonsoft.Json/) para serializar una lista de blogs cada uno con sus entradas asociadas:

<!--
            using var context = new BlogsContext();

            var blogs = context.Blogs.Include(e => e.Posts).ToList();

            var serialized = JsonConvert.SerializeObject(
                blogs,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_1a](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1a)]

El JSON generado a partir de este código es:

```json
[
  {
    "Id": 1,
    "Name": ".NET Blog",
    "Summary": "Posts about .NET",
    "Posts": [
      {
        "Id": 1,
        "Title": "Announcing the Release of EF Core 5.0",
        "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
        "BlogId": 1
      },
      {
        "Id": 2,
        "Title": "Announcing F# 5",
        "Content": "F# 5 is the latest version of F#, the functional programming language...",
        "BlogId": 1
      }
    ]
  },
  {
    "Id": 2,
    "Name": "Visual Studio Blog",
    "Summary": "Posts about Visual Studio",
    "Posts": [
      {
        "Id": 3,
        "Title": "Disassembly improvements for optimized managed debugging",
        "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
        "BlogId": 2
      },
      {
        "Id": 4,
        "Title": "Database Profiling with Visual Studio",
        "Content": "Examine when database queries were executed and measure how long the take using...",
        "BlogId": 2
      }
    ]
  }
]
```

Tenga en cuenta que no hay ningún blog o publicación duplicados en el archivo JSON. Esto significa que las llamadas simples a `Update` funcionarán para actualizar estas entidades en la base de datos:

<!--
        public static void UpdateBlogsFromJson(string json)
        {
            using var context = new BlogsContext();

            var blogs = JsonConvert.DeserializeObject<List<Blog>>(json);

            foreach (var blog in blogs)
            {
                context.Update(blog);
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_1b](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1b)]

### <a name="handling-duplicates"></a>Administrar duplicados

El código del ejemplo anterior serializaba cada blog con sus entradas asociadas. Si se cambia para serializar cada publicación con su blog asociado, los duplicados se introducen en el JSON serializado. Por ejemplo:

<!--
            using var context = new BlogsContext();

            var posts = context.Posts.Include(e => e.Blog).ToList();

            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_2)]

El JSON serializado ahora tiene el siguiente aspecto:

```json
[
  {
    "Id": 1,
    "Title": "Announcing the Release of EF Core 5.0",
    "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 2,
          "Title": "Announcing F# 5",
          "Content": "F# 5 is the latest version of F#, the functional programming language...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 2,
    "Title": "Announcing F# 5",
    "Content": "F# 5 is the latest version of F#, the functional programming language...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 1,
          "Title": "Announcing the Release of EF Core 5.0",
          "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 3,
    "Title": "Disassembly improvements for optimized managed debugging",
    "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 4,
          "Title": "Database Profiling with Visual Studio",
          "Content": "Examine when database queries were executed and measure how long the take using...",
          "BlogId": 2
        }
      ]
    }
  },
  {
    "Id": 4,
    "Title": "Database Profiling with Visual Studio",
    "Content": "Examine when database queries were executed and measure how long the take using...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 3,
          "Title": "Disassembly improvements for optimized managed debugging",
          "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
          "BlogId": 2
        }
      ]
    }
  }
]
```

Observe que el gráfico ahora incluye varias instancias de blog con el mismo valor de clave, así como varias instancias posteriores con el mismo valor de clave. Al intentar realizar un seguimiento de este gráfico como hicimos en el ejemplo anterior, se producirá lo siguiente:

> System. InvalidOperationException: no se puede realizar el seguimiento de la instancia del tipo de entidad ' post ' porque ya se está realizando el seguimiento de otra instancia con el valor de clave ' {ID: 2} '. Al adjuntar entidades existentes, asegúrese de que solo se adjunta una instancia de entidad con un valor de clave determinado.

Podemos corregir esto de dos maneras:

- Usar opciones de serialización de JSON que conserven las referencias
- Realizar la resolución de identidad mientras se realiza el seguimiento del gráfico

#### <a name="preserve-references"></a>Conservación de las referencias

Json.NET ofrece la `PreserveReferencesHandling` opción de controlar esto. Por ejemplo:

<!--
            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    PreserveReferencesHandling = PreserveReferencesHandling.All,
                    Formatting = Formatting.Indented
                });
-->
[!code-csharp[Attaching_a_serialized_graph_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_3)]

El JSON resultante tiene ahora el siguiente aspecto:

```json
{
  "$id": "1",
  "$values": [
    {
      "$id": "2",
      "Id": 1,
      "Title": "Announcing the Release of EF Core 5.0",
      "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
      "BlogId": 1,
      "Blog": {
        "$id": "3",
        "Id": 1,
        "Name": ".NET Blog",
        "Summary": "Posts about .NET",
        "Posts": [
          {
            "$ref": "2"
          },
          {
            "$id": "4",
            "Id": 2,
            "Title": "Announcing F# 5",
            "Content": "F# 5 is the latest version of F#, the functional programming language...",
            "BlogId": 1,
            "Blog": {
              "$ref": "3"
            }
          }
        ]
      }
    },
    {
      "$ref": "4"
    },
    {
      "$id": "5",
      "Id": 3,
      "Title": "Disassembly improvements for optimized managed debugging",
      "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
      "BlogId": 2,
      "Blog": {
        "$id": "6",
        "Id": 2,
        "Name": "Visual Studio Blog",
        "Summary": "Posts about Visual Studio",
        "Posts": [
          {
            "$ref": "5"
          },
          {
            "$id": "7",
            "Id": 4,
            "Title": "Database Profiling with Visual Studio",
            "Content": "Examine when database queries were executed and measure how long the take using...",
            "BlogId": 2,
            "Blog": {
              "$ref": "6"
            }
          }
        ]
      }
    },
    {
      "$ref": "7"
    }
  ]
}
```

Tenga en cuenta que este JSON ha reemplazado duplicados con referencias como `"$ref": "5"` que hacen referencia a la instancia ya existente en el gráfico. Se puede realizar un seguimiento de este gráfico mediante las llamadas simples a `Update` , como se muestra anteriormente.

La <xref:System.Text.Json> compatibilidad de las bibliotecas de clases base (BCL) de .net tiene una opción similar que produce el mismo resultado. Por ejemplo:

<!--
            var serialized = System.Text.Json.JsonSerializer.Serialize(posts, new System.Text.Json.JsonSerializerOptions
            {
                ReferenceHandler = ReferenceHandler.Preserve,
                WriteIndented = true
            });
-->
[!code-csharp[Attaching_a_serialized_graph_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_4)]

#### <a name="resolve-duplicates"></a>Resolver duplicados

Si no es posible eliminar duplicados en el proceso de serialización, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> proporciona una manera de controlar esto. TrackGraph funciona como `Add` `Attach` y, `Update` salvo que genera una devolución de llamada para cada instancia de entidad antes de realizar el seguimiento. Esta devolución de llamada se puede usar para realizar un seguimiento de la entidad u omitirla. Por ejemplo:

<!--
        public static void UpdatePostsFromJsonWithIdentityResolution(string json)
        {
            using var context = new BlogsContext();

            var posts = JsonConvert.DeserializeObject<List<Post>>(json);

            foreach (var post in posts)
            {
                context.ChangeTracker.TrackGraph(
                    post, node =>
                        {
                            var keyValue = node.Entry.Property("Id").CurrentValue;
                            var entityType = node.Entry.Metadata;

                            var existingEntity = node.Entry.Context.ChangeTracker.Entries()
                                .FirstOrDefault(
                                    e => Equals(e.Metadata, entityType)
                                         && Equals(e.Property("Id").CurrentValue, keyValue));

                            if (existingEntity == null)
                            {
                                Console.WriteLine($"Tracking {entityType} entity with key value {keyValue}");

                                node.Entry.State = EntityState.Modified;
                            }
                            else
                            {
                                Console.WriteLine($"Discarding duplicate {entityType} entity with key value {keyValue}");
                            }
                        });
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_5)]

Para cada entidad del gráfico, este código hará lo siguiente:

- Buscar el tipo de entidad y el valor de clave de la entidad
- Buscar la entidad con esta clave en el seguimiento de cambios
  - Si se encuentra la entidad, no se realiza ninguna otra acción, ya que la entidad es un duplicado.
  - Si no se encuentra la entidad, se realiza el seguimiento estableciendo el estado en `Modified`

La salida de la ejecución de este código es:

```output
Tracking EntityType: Post entity with key value 1
Tracking EntityType: Blog entity with key value 1
Tracking EntityType: Post entity with key value 2
Discarding duplicate EntityType: Post entity with key value 2
Tracking EntityType: Post entity with key value 3
Tracking EntityType: Blog entity with key value 2
Tracking EntityType: Post entity with key value 4
Discarding duplicate EntityType: Post entity with key value 4
```

> [!IMPORTANT]
> En este código **se supone que todos los duplicados son idénticos**. Esto hace que sea seguro elegir arbitrariamente uno de los duplicados para realizar el seguimiento mientras se descartan los demás. Si los duplicados pueden diferir, el código deberá decidir cómo determinar cuál usar y cómo combinar los valores de propiedad y navegación juntos.

> [!NOTE]
> Para simplificar, en este código se supone que cada entidad tiene una propiedad de clave principal denominada `Id` . Esto se podría codificar en una interfaz o clase base abstracta. Como alternativa, la propiedad o propiedades de la clave principal se pueden obtener a partir de los <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadatos de modo que este código funcione con cualquier tipo de entidad.

## <a name="failing-to-set-key-values"></a>No se pueden establecer los valores de clave

Los tipos de entidad se configuran a menudo para usar [valores de clave generados automáticamente](xref:core/modeling/generated-properties). Este es el valor predeterminado para las propiedades Integer y GUID de las claves no compuestas. Sin embargo, si el tipo de entidad no está configurado para usar los valores de clave generados automáticamente, debe establecerse un valor de clave explícito antes de realizar el seguimiento de la entidad. Por ejemplo, con el siguiente tipo de entidad:

<!--
    public class Pet
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }
    }
-->
[!code-csharp[Pet](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Pet)]

Considere el código que intenta rastrear dos nuevas instancias de entidad sin establecer los valores de clave:

<!--
            using var context = new BlogsContext();

            context.Add(new Pet { Name = "Smokey" });

            try
            {
                context.Add(new Pet { Name = "Clippy" }); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Failing_to_set_key_values_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Failing_to_set_key_values_1)]

Este código producirá:

> System. InvalidOperationException: no se puede realizar el seguimiento de la instancia del tipo de entidad ' PET ' porque ya se está realizando el seguimiento de otra instancia con el valor de clave ' {ID: 0} '. Al adjuntar entidades existentes, asegúrese de que solo se adjunta una instancia de entidad con un valor de clave determinado.

La solución para esto es para establecer los valores de clave explícitamente o configurar la propiedad clave para usar los valores de clave generados. Vea [valores generados](xref:core/modeling/generated-properties) para obtener más información.

## <a name="overusing-a-single-dbcontext-instance"></a>Sobreutilización de una única instancia de DbContext

<xref:Microsoft.EntityFrameworkCore.DbContext> está diseñado para representar una unidad de trabajo de corta duración, como se describe en la [configuración y la inicialización de DbContext](xref:core/dbcontext-configuration/index), y se ha elaborado en [Change Tracking en EF Core](xref:core/change-tracking/index). No seguir esta guía facilita la ejecución en situaciones en las que se realiza un intento de realizar un seguimiento de varias instancias de la misma entidad. Los ejemplos comunes son:

- Usar la misma instancia de DbContext para configurar el estado de prueba y, a continuación, ejecutar la prueba. Esto suele hacer que DbContext siga realizando el seguimiento de una instancia de la entidad a partir de la configuración de pruebas, mientras que después intenta adjuntar una nueva instancia de la prueba adecuada. En su lugar, use una instancia de DbContext diferente para configurar el estado de prueba y el código de prueba adecuado.
- Usar una instancia compartida DbContext en un repositorio o código similar. En su lugar, asegúrese de que el repositorio usa una única instancia de DbContext para cada unidad de trabajo.

## <a name="identity-resolution-and-queries"></a>Resolución y consultas de identidad

La resolución de identidad se produce automáticamente cuando se realiza un seguimiento de las entidades desde una consulta. Esto significa que, si ya se ha realizado un seguimiento de una instancia de una entidad con un valor de clave determinado, se usará esta instancia de seguimiento existente en lugar de crear una nueva instancia. Esto tiene una consecuencia importante: si los datos han cambiado en la base de datos, no se reflejarán en los resultados de la consulta. Esta es una buena razón para usar una nueva instancia de DbContext para cada unidad de trabajo, como se describe en la [configuración y la inicialización de dbcontext](xref:core/dbcontext-configuration/index), y se ha elaborado en [Change Tracking en EF Core](xref:core/change-tracking/index).

> [!IMPORTANT]
> Es importante entender que EF Core siempre ejecuta una consulta LINQ en un DbSet en la base de datos y solo devuelve resultados en función de lo que hay en la base de datos. Sin embargo, para una consulta de seguimiento, si ya se realiza el seguimiento de las entidades devueltas, se utilizan las instancias de las que se ha realizado un seguimiento en lugar de crear instancias a partir de los datos de la base de datos.

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> o <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> se puede utilizar cuando es necesario actualizar las entidades de las que se realiza un seguimiento con los datos más recientes de la base de datos. Consulte [acceso a entidades](xref:core/change-tracking/entity-entries) de las que se ha realizado un seguimiento para obtener más información.

A diferencia de las consultas de seguimiento, las consultas sin seguimiento no realizan la resolución de identidades. Esto significa que las consultas sin seguimiento pueden devolver duplicados como en el caso de serialización de JSON descrito anteriormente. Esto no suele ser un problema si los resultados de la consulta se van a serializar y enviar al cliente.

> [!TIP]
> No realice habitualmente una consulta sin seguimiento y, a continuación, asocie las entidades devueltas al mismo contexto. Esto será más lento y más difícil de conseguir que el uso de una consulta de seguimiento.

Las consultas sin seguimiento no realizan la resolución de identidad porque esto afecta al rendimiento de la transmisión por secuencias de un gran número de entidades de una consulta. Esto se debe a que la resolución de identidad requiere realizar un seguimiento de cada instancia devuelta para que se pueda usar en lugar de crear posteriormente un duplicado.

A partir de EF Core 5,0, las consultas sin seguimiento se pueden forzar para realizar la resolución de identidades mediante <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})> . La consulta realizará un seguimiento de las instancias devueltas (sin realizar el seguimiento de forma normal) y garantiza que no se crean duplicados en los resultados de la consulta.

## <a name="overriding-object-equality"></a>Reemplazar la igualdad de objetos

EF Core usa la [igualdad de referencia](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) al comparar instancias de entidad. Este es el caso incluso si los tipos de entidad invalidan <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> o cambian la igualdad de objetos. Sin embargo, hay un lugar donde la igualdad de invalidación puede afectar al comportamiento de EF Core: cuando las navegaciones de la colección usan la igualdad invalidada en lugar de la igualdad de la referencia y, por lo tanto, notifican varias instancias como iguales.

Debido a esto, se recomienda evitar la igualdad de la entidad. Si se usa, asegúrese de crear navegaciones de colección que fuercen la igualdad de referencia. Por ejemplo, cree un comparador de igualdad que use la igualdad de referencia:

<!--
    public sealed class ReferenceEqualityComparer : IEqualityComparer<object>
    {
        private ReferenceEqualityComparer()
        {
        }

        public static ReferenceEqualityComparer Instance { get; } = new ReferenceEqualityComparer();

        bool IEqualityComparer<object>.Equals(object x, object y) => x == y;

        int IEqualityComparer<object>.GetHashCode(object obj) => RuntimeHelpers.GetHashCode(obj);
    }
-->
[!code-csharp[ReferenceEqualityComparer](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/ReferenceEqualityComparer.cs?name=ReferenceEqualityComparer)]

(A partir de .NET 5, esto se incluye en la BCL como <xref:System.Collections.Generic.ReferenceEqualityComparer> ).

Este comparador se puede usar al crear navegaciones de colección. Por ejemplo:

<!--
        public ICollection<Order> Orders { get; set; }
            = new HashSet<Order>(ReferenceEqualityComparer.Instance);
-->
[!code-csharp[OrdersCollection](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=OrdersCollection)]

### <a name="comparing-key-properties"></a>Comparar propiedades de clave

Además de las comparaciones de igualdad, también es necesario ordenar los valores de clave. Esto es importante para evitar los interbloqueos al actualizar varias entidades en una única llamada a SaveChanges. Todos los tipos que se usan para las propiedades principales, alternativas o de clave externa, así como los que se usan para los índices únicos, deben implementar <xref:System.IComparable%601> y <xref:System.IEquatable%601> . Los tipos que normalmente se usan como claves (int, GUID, String, etc.) ya admiten estas interfaces. Los tipos de clave personalizados pueden agregar estas interfaces.
