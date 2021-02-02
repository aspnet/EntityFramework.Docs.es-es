---
title: 'Herramienta de seguimiento de cambios: EF Core'
description: Información general sobre el seguimiento de cambios para EF Core
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/index
ms.openlocfilehash: 8cfa4590af07ec1715eb48ec0c7acb3426b6a6b4
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983266"
---
# <a name="change-tracking-in-ef-core"></a>Herramienta de seguimiento de cambios en EF Core

Cada instancia de <xref:Microsoft.EntityFrameworkCore.DbContext> realiza un seguimiento de los cambios realizados en las entidades. Estas entidades de las que se realiza un seguimiento, a su vez, impulsan los cambios en la base de datos cuando se llama a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.

En este documento se presenta información general sobre el seguimiento de cambios de Entity Framework Core (EF Core) y cómo se relaciona con las consultas y actualizaciones.

> [!TIP]
> Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).

> [!TIP]
> Para simplificar, este documento utiliza métodos sincrónicos como <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, y hace referencia a ellos, en lugar de sus equivalentes asincrónicos como <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>. La llamada al método asincrónico y su espera pueden sustituirse a menos que se indique lo contrario.

## <a name="how-to-track-entities"></a>Seguimiento de entidades

Se realiza un seguimiento de las instancias de entidad cuando se dan estos casos:

- Se devuelven de una consulta ejecutada en la base de datos.
- Se asocian explícitamente a DbContext por `Add`, `Attach`, `Update`o métodos similares.
- Se detecta como nuevas entidades conectadas a las entidades sometidas a seguimiento existentes

El seguimiento de las instancias de entidad deja de realizarse cuando se dan estos casos:

- DbContext se desecha.
- La herramienta de seguimiento de cambios está desactivada (EF Core 5.0 y versiones posteriores).
- Las entidades se desasocian explícitamente.

DbContext está diseñado para representar una unidad de trabajo de corta duración, como se describe en [Inicialización y configuración de DbContext](xref:core/dbcontext-configuration/index). Esto significa que desechar DbContext es _la forma normal_ detener el seguimiento de las entidades. En otras palabras, la vigencia de DbContext debe pasar por estos pasos:

1. Creación de la instancia de DbContext
2. Seguimiento de algunas entidades
3. Realización de algunos cambios en las entidades
4. Llamada a SaveChanges para actualizar la base de datos
5. Eliminación de la instancia de DbContext

> [!TIP]
> No es necesario borrar la herramienta de seguimiento de cambios ni desasociar explícitamente las instancias de la entidad al adoptar este enfoque. Sin embargo, si necesita desasociar entidades, llamar a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Clear%2A?displayProperty=nameWithType> es más eficaz que separar las entidades una por una.

## <a name="entity-states"></a>Estados de entidad

Cada entidad está asociada a un elemento <xref:Microsoft.EntityFrameworkCore.EntityState> determinado:

- <xref:Microsoft.EntityFrameworkCore.DbContext> ya no realiza el seguimiento de las entidades `Detached`.
- Las entidades `Added` son nuevas y aún no se han insertado en la base de datos. Esto significa que se insertarán cuando se llame a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.
- Las entidades`Unchanged` _no_ han cambiado desde que se consultaron desde la base de datos. Todas las entidades devueltas de las consultas se encuentran inicialmente en este estado.
- Las entidades `Modified` han cambiado desde que se consultaron desde la base de datos. Esto significa que se actualizarán cuando se llame a SaveChanges.
- Existen entidades `Deleted` en la base de datos, pero se marcan para eliminarse cuando se llama a SaveChanges.

EF Core realiza un seguimiento de los cambios en el nivel de propiedad. Por ejemplo, si solo se modifica un valor de propiedad único, una actualización de base de datos solo cambiará ese valor. Sin embargo, las propiedades solo se pueden marcar como modificadas cuando la propia entidad está en el estado de modificación. (O, desde una perspectiva alternativa, el estado de modificación significa que al menos un valor de propiedad se ha marcado como modificado).

En la tabla siguiente se resumen los diferentes estados:

| Estado de la entidad     | Seguido por DbContext | Existe en la base de datos | Propiedades modificadas | Acción en SaveChanges
|:-----------------|----------------------|--------------------|---------------------|-----------------------
| `Detached`       | No                   | -                  | -                   | -
| `Added`          | Sí                  | No                 | -                   | Insertar
| `Unchanged`      | Sí                  | Sí                | No                  | -
| `Modified`       | Sí                  | Sí                | Sí                 | Actualizar
| `Deleted`        | Sí                  | Sí                | -                   | Eliminar

> [!NOTE]
> Este texto utiliza términos de base de datos relacional para mayor claridad. Las bases de datos NoSQL suelen admitir operaciones similares pero posiblemente con nombres diferentes. Consulte la documentación del proveedor de bases de datos para obtener más información.

## <a name="tracking-from-queries"></a>Seguimiento de consultas

El seguimiento de cambios de EF Core funciona mejor cuando se utiliza la misma instancia de <xref:Microsoft.EntityFrameworkCore.DbContext> para consultar las entidades y actualizarlas mediante una llamada a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>. Esto se debe a que EF Core realiza un seguimiento automático del estado de las entidades consultadas y, a continuación, detecta los cambios realizados en estas entidades cuando se llama a SaveChanges.

Este enfoque tiene varias ventajas con respecto a [realizar un seguimiento explícito de las instancias de entidad](xref:core/change-tracking/explicit-tracking):

- Es sencilla. Los estados de la entidad rara vez tienen que manipularse explícitamente: EF Core se encarga de los cambios de estado.
- Las actualizaciones solo se limitan a aquellos valores que realmente han cambiado.
- Los valores de las [propiedades reemplazadas](xref:core/modeling/shadow-properties) se conservan y se usan según sea necesario. Esto es especialmente importante cuando las claves externas se almacenan en el estado reemplazado.
- Los valores originales de las propiedades se conservan automáticamente y se usan para mejorar la eficacia de las actualizaciones.

## <a name="simple-query-and-update"></a>Consulta y actualización simples

Por ejemplo, considere un modelo de blog o publicaciones sencillo:

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

Podemos usar este modelo para consultar blogs y publicaciones y luego realizar algunas actualizaciones en la base de datos:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            blog.Name = ".NET Blog (Updated!)";

            foreach (var post in blog.Posts.Where(e => !e.Title.Contains("5.0")))
            {
                post.Title = post.Title.Replace("5", "5.0");
            }

            context.SaveChanges();
-->
[!code-csharp[Simple_query_and_update_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_1)]

La llamada a SaveChanges da como resultado las siguientes actualizaciones de base de datos, con SQLite como base de datos de ejemplo:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0='Announcing F# 5.0' (Size = 17)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "Title" = @p0
WHERE "Id" = @p1;
SELECT changes();
```

La vista de depuración de la [herramienta de seguimiento de cambios](xref:core/change-tracking/debug-views) es una excelente manera de visualizar las entidades de las que se realiza el seguimiento y cuáles son sus estados. Por ejemplo, la inserción del código siguiente en el ejemplo anterior antes de llamar a SaveChanges:

<!--
                context.ChangeTracker.DetectChanges();
                Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Simple_query_and_update_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Simple_query_and_update_2)]

Se genera el siguiente código resultado:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'
  Blog: {Id: 1}
```

Observe específicamente:

- La propiedad `Blog.Name` se marca como modificada (`Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'`) y esto hace que el blog esté en el estado `Modified`.
- La propiedad `Post.Title` de la publicación 2 se marca como modificada (`Title: 'Announcing F# 5.0' Modified Originally 'Announcing F# 5'`) y esto hace que esta publicación esté en el estado `Modified`.
- Los demás valores de propiedad de la publicación 2 no han cambiado y, por lo tanto, no se marcan como modificados. Este es el motivo por el cual estos valores no se incluyen en la actualización de la base de datos.
- La otra publicación no se modificó de ningún modo. Esta es la razón por la que todavía está en el estado `Unchanged` y no se incluye en la actualización de la base de datos.

## <a name="query-then-insert-update-and-delete"></a>Consulta y luego inserción, actualización y eliminación

Las actualizaciones como las del ejemplo anterior se pueden combinar con inserciones y eliminaciones en la misma unidad de trabajo. Por ejemplo:

<!--
            using var context = new BlogsContext();

            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Modify property values
            blog.Name = ".NET Blog (Updated!)";

            // Insert a new Post
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            // Mark an existing Post as Deleted
            var postToDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            context.Remove(postToDelete);

            context.ChangeTracker.DetectChanges();
            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[Query_then_insert_update_and_delete_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Query_then_insert_update_and_delete_1)]

En este ejemplo:

- Se consultan un blog y publicaciones relacionadas desde la base de datos y se realiza su seguimiento.
- Se cambia la propiedad `Blog.Name`.
- Se agrega una nueva publicación a la colección de publicaciones existentes para el blog.
- Una publicación existente se marca para su eliminación mediante una llamada a <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>.

Al mirar de nuevo en la [vista de depuración de la herramienta de seguimiento de cambios](xref:core/change-tracking/debug-views) antes de llamar a SaveChanges se muestra cómo EF Core está realizando el seguimiento de estos cambios:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: 3}, {Id: -2147482638}]
Post {Id: -2147482638} Added
  Id: -2147482638 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Tenga en lo siguiente:

- El blog se marca como `Modified`. Se generará una actualización de base de datos.
- La publicación 2 se marca como `Deleted`. Se generará una eliminación de base de datos.
- Una nueva publicación con un identificador temporal se asocia al blog 1 y se marca como `Added`. Se generará una inserción de base de datos.

Esto da como resultado los siguientes comandos de base de datos (con SQLite) cuando se llama a SaveChanges:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog (Updated!)' (Size = 20)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 was released recently and has come with many...' (Size = 56), @p2='What's next for System.Text.Json?' (Size = 33)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Vea [Seguimiento explícito de las entidades](xref:core/change-tracking/explicit-tracking) para obtener más información sobre cómo insertar y eliminar entidades. Consulte [Detección y notificaciones de cambios](xref:core/change-tracking/change-detection) para obtener más información sobre cómo EF Core detecta automáticamente los cambios como este.

> [!TIP]
> Llame a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType> para determinar si se han realizado cambios que provocarán que SaveChanges realice actualizaciones en la base de datos. Si HasChanges devuelve false, SaveChanges será una operación inefectiva.
