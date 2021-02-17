---
title: 'Seguimiento explícito de entidades: EF Core'
description: Seguimiento explícito de las entidades con DbContext mediante agregar, adjuntar, actualizar y quitar
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/explicit-tracking
ms.openlocfilehash: 3d9142cecf272c635c3a041fe6c5d9c49a26c33d
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543190"
---
# <a name="explicitly-tracking-entities"></a>Seguimiento explícito de entidades

Cada instancia de <xref:Microsoft.EntityFrameworkCore.DbContext> realiza un seguimiento de los cambios realizados en las entidades. Estas entidades de las que se realiza un seguimiento, a su vez, impulsan los cambios en la base de datos cuando se llama a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.

El seguimiento de cambios de Entity Framework Core (EF Core) funciona mejor cuando <xref:Microsoft.EntityFrameworkCore.DbContext> se utiliza la misma instancia para consultar las entidades y actualizarlas mediante una llamada a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> . Esto se debe a que EF Core realiza un seguimiento automático del estado de las entidades consultadas y, a continuación, detecta los cambios realizados en estas entidades cuando se llama a SaveChanges. Este enfoque se trata en [Change Tracking en EF Core](xref:core/change-tracking/index).

> [!TIP]
> En este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de EF Core. Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información sobre estos temas.

> [!TIP]
> Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).

> [!TIP]
> Para simplificar, este documento utiliza métodos sincrónicos como <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, y hace referencia a ellos, en lugar de sus equivalentes asincrónicos como <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>. La llamada al método asincrónico y su espera pueden sustituirse a menos que se indique lo contrario.

## <a name="introduction"></a>Introducción

Las entidades se pueden "asociar" explícitamente a un de <xref:Microsoft.EntityFrameworkCore.DbContext> tal forma que el contexto realice un seguimiento de esas entidades. Esto es especialmente útil cuando:

1. Crear nuevas entidades que se van a insertar en la base de datos.
2. Volver a adjuntar entidades desconectadas que se consultaron previamente mediante una instancia de DbContext _diferente_ .

La primera de ellas será necesaria para la mayoría de las aplicaciones y la principal la controlan los <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> métodos.

La segunda solo es necesaria para las aplicaciones que cambian entidades o sus relaciones **_mientras no se realiza el seguimiento de las entidades_**. Por ejemplo, una aplicación web puede enviar entidades al cliente web donde el usuario realiza cambios y envía las entidades de nuevo. Estas entidades se conocen como "desconectadas", ya que originalmente se consultaron desde DbContext, pero se desconectaron desde ese contexto cuando se enviaron al cliente.

La aplicación web ahora debe volver a adjuntar estas entidades para que vuelvan a realizar el seguimiento e indiquen los cambios que se han realizado de forma que <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> puedan realizar las actualizaciones adecuadas en la base de datos. Lo controlan principalmente los <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> métodos y <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> .

> [!TIP]
> Normalmente no es necesario adjuntar entidades a la _misma instancia de DbContext_ de la que se han consultado. No realice habitualmente una consulta sin seguimiento y, a continuación, asocie las entidades devueltas al mismo contexto. Esto será más lento que el uso de una consulta de seguimiento y puede dar lugar a problemas como los valores de las propiedades de sombra que faltan, lo que dificulta la obtención de derechos.

### <a name="generated-versus-explicit-key-values"></a>Valores de clave generados frente a explícitos

De forma predeterminada, [las propiedades](xref:core/modeling/keys) Integer y de clave GUID están configuradas para utilizar [valores de clave generados automáticamente](xref:core/modeling/generated-properties). Esto tiene una **ventaja importante para el seguimiento de cambios: un valor de clave no establecido indica que la entidad es "nueva"**. Por "nuevo", nos referimos a que todavía no se ha insertado en la base de datos.

En las secciones siguientes se usan dos modelos. El primero se configura para **no** usar los valores de clave generados:

<!--
    public class Blog
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Model)]

Los valores de clave no generados (es decir, establecidos explícitamente) se muestran en primer lugar en cada ejemplo porque todo es muy explícito y fácil de seguir. A continuación, se sigue un ejemplo en el que se usan los valores de clave generados:

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

Tenga en cuenta que las propiedades clave de este modelo no necesitan ninguna configuración adicional, ya que el uso de los valores de clave generados es el [valor predeterminado para las claves enteras simples](xref:core/modeling/generated-properties).

## <a name="inserting-new-entities"></a>Insertar nuevas entidades

### <a name="explicit-key-values"></a>Valores de clave explícitos

Se debe realizar un seguimiento de una entidad en el `Added` Estado que va a insertar <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> . Las entidades normalmente se colocan en el estado agregado mediante una llamada a uno de los <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType> métodos equivalentes de,,, <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType> o <xref:Microsoft.EntityFrameworkCore.DbSet%601> .

> [!TIP]
> Estos métodos funcionan de la misma manera en el contexto del seguimiento de cambios. Consulte [características adicionales de Change Tracking](xref:core/change-tracking/miscellaneous) para obtener más información.

Por ejemplo, para empezar a realizar el seguimiento de un nuevo blog:

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Inserting_new_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_1)]

Al inspeccionar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de esta llamada, se muestra que el contexto está realizando el seguimiento de la nueva entidad en el `Added` Estado:

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

Sin embargo, los métodos Add no solo funcionan en una entidad individual. En realidad, empiezan a realizar el seguimiento _de un gráfico completo de entidades relacionadas_, poniéndolos al `Added` Estado. Por ejemplo, para insertar un nuevo blog y las nuevas entradas asociadas:

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_2)]

Ahora el contexto está realizando el seguimiento de todas estas entidades como `Added` :

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Added
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Added
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Observe que se han establecido valores explícitos para las `Id` propiedades de clave en los ejemplos anteriores. Esto se debe a que el modelo se ha configurado para utilizar valores de clave establecidos explícitamente, en lugar de valores de clave generados automáticamente. Cuando no se usan claves generadas, las propiedades de clave se deben establecer explícitamente _antes_ de llamar a `Add` . Estos valores de clave se insertan después cuando se llama a SaveChanges. Por ejemplo, al usar SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Id", "Name")
VALUES (@p0, @p1);

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String), @p3='1' (DbType = String), @p4='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p5='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p2, @p3, @p4, @p5);

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String), @p1='1' (DbType = String), @p2='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p3='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2, @p3);
```

Se realiza un seguimiento de todas estas entidades en el `Unchanged` estado después de que se Complete SaveChanges, ya que estas entidades ahora existen en la base de datos:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

### <a name="generated-key-values"></a>Valores de clave generados

Como se mencionó anteriormente, [las propiedades de clave](xref:core/modeling/keys) de GUID y de entero se configuran para utilizar [los valores de clave generados automáticamente](xref:core/modeling/generated-properties) de forma predeterminada. Esto significa que la aplicación _no debe establecer ningún valor de clave explícitamente_. Por ejemplo, para insertar un nuevo blog y exponer todos los valores de clave generados:

<!--
            context.Add(
                new Blog
                {
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Inserting_new_entities_3)]

Al igual que con los valores de clave explícitos, el contexto está realizando el seguimiento de todas estas entidades como `Added` :

```output
Blog {Id: -2147482644} Added
  Id: -2147482644 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -2147482637}, {Id: -2147482636}]
Post {Id: -2147482637} Added
  Id: -2147482637 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -2147482644}
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: -2147482644}
```

Observe que, en este caso, se han generado [valores de clave temporales](xref:core/change-tracking/miscellaneous#temporary-values) para cada entidad. EF Core utilizan estos valores hasta que se llama a SaveChanges, en cuyo punto se leen los valores de clave reales de la base de datos. Por ejemplo, al usar SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Name")
VALUES (@p0);
SELECT "Id"
FROM "Blogs"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p2='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p3='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p1, @p2, @p3);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Una vez completado SaveChanges, todas las entidades se han actualizado con sus valores de clave reales y se realiza el seguimiento en el `Unchanged` Estado, ya que ahora coinciden con el estado de la base de datos:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Este es exactamente el mismo estado final que el ejemplo anterior que usaba valores de clave explícitos.

> [!TIP]
> Todavía se puede establecer un valor de clave explícito incluso cuando se usan valores de clave generados. A continuación, EF Core intentará insertar con este valor de clave. Algunas configuraciones de base de datos, incluidas SQL Server con columnas de identidad, no admiten estas inserciones y se producirán ([vea estos documentos para obtener una solución alternativa](xref:core/providers/sql-server/value-generation#inserting-explicit-values-into-identity-columns)).

## <a name="attaching-existing-entities"></a>Asociar entidades existentes

### <a name="explicit-key-values"></a>Valores de clave explícitos

Se realiza un seguimiento de las entidades devueltas de las consultas en el `Unchanged` Estado. El `Unchanged` Estado significa que la entidad no se ha modificado desde que se realizó la consulta. Una entidad desconectada, tal vez que se devuelve desde un cliente web en una solicitud HTTP, se puede poner en este estado mediante <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType> o los métodos equivalentes en <xref:Microsoft.EntityFrameworkCore.DbSet%601> . Por ejemplo, para empezar a realizar el seguimiento de un blog existente:

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Attaching_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_1)]

> [!NOTE]
> Los ejemplos siguientes son crear entidades explícitamente con `new` para simplificar. Normalmente, las instancias de la entidad procederán de otro origen, como la deserialización de un cliente o la creación a partir de datos en un HTTP POST.

Al inspeccionar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de esta llamada, se muestra que se realiza un seguimiento de la entidad en el `Unchanged` Estado:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

Del mismo modo `Add` , `Attach` establece realmente un grafo completo de entidades conectadas en el `Unchanged` Estado. Por ejemplo, para adjuntar un blog existente y las publicaciones existentes asociadas:

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_2)]

Ahora el contexto está realizando el seguimiento de todas estas entidades como `Unchanged` :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

La llamada a SaveChanges en este momento no tendrá ningún efecto. Todas las entidades se marcan como `Unchanged` , por lo que no hay nada que actualizar en la base de datos.

### <a name="generated-key-values"></a>Valores de clave generados

Como se mencionó anteriormente, [las propiedades de clave](xref:core/modeling/keys) de GUID y de entero se configuran para utilizar [los valores de clave generados automáticamente](xref:core/modeling/generated-properties) de forma predeterminada. Esto tiene una ventaja importante al trabajar con entidades desconectadas: un valor de clave no establecido indica que la entidad no se ha insertado todavía en la base de datos. Esto permite al seguimiento de cambios detectar automáticamente nuevas entidades y ponerlas en el `Added` Estado. Por ejemplo, considere la posibilidad de adjuntar este gráfico de un blog y publicaciones:

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Attaching_existing_entities_3)]

El blog tiene un valor de clave de 1, que indica que ya existe en la base de datos. Dos de las publicaciones también tienen valores de clave establecidos, pero el tercero no los tiene. EF Core verá este valor de clave como 0, el valor predeterminado de CLR para un entero. Esto da como resultado EF Core marcar la nueva entidad como `Added` en lugar de `Unchanged` :

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482636}]
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
```

La llamada a SaveChanges en este momento no hace nada con las `Unchanged` entidades, sino que inserta la nueva entidad en la base de datos. Por ejemplo, al usar SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Lo importante que se debe tener en cuenta es que, con los valores de clave generados, EF Core es capaz de **distinguir automáticamente los nuevos de las entidades existentes en un gráfico desconectado**. En pocas palabras, cuando se usan claves generadas, EF Core siempre insertará una entidad cuando esa entidad no tenga establecido ningún valor de clave.

## <a name="updating-existing-entities"></a>Actualización de entidades existentes

### <a name="explicit-key-values"></a>Valores de clave explícitos

<xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType> y los métodos equivalentes en <xref:Microsoft.EntityFrameworkCore.DbSet%601> se comportan exactamente igual que los `Attach` métodos descritos anteriormente, salvo que las entidades se colocan en `Modfied` en lugar de en el `Unchanged` Estado. Por ejemplo, para empezar a realizar el seguimiento de un blog existente como `Modified` :

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Updating_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_1)]

Al inspeccionar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de esta llamada, se muestra que el contexto está realizando el seguimiento de esta entidad en el `Modified` Estado:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: []
```

Al igual `Add` que con y `Attach` , en `Update` realidad marca un _grafo completo_ de entidades relacionadas como `Modified` . Por ejemplo, para adjuntar un blog existente y los envíos existentes asociados como `Modified` :

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_2)]

Ahora el contexto está realizando el seguimiento de todas estas entidades como `Modified` :

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

La llamada a SaveChanges en este punto hará que las actualizaciones se envíen a la base de datos para todas estas entidades. Por ejemplo, al usar SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();
```

### <a name="generated-key-values"></a>Valores de clave generados

Al igual que con `Attach` , los valores de clave generados tienen la misma ventaja principal para `Update` : un valor de clave no establecido indica que la entidad es nueva y aún no se ha insertado en la base de datos. Como con `Attach` , esto permite que DbContext detecte automáticamente las nuevas entidades y las ponga en el `Added` Estado. Por ejemplo, considere la posibilidad de llamar a `Update` con este gráfico de un blog y publicaciones:

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Updating_existing_entities_3)]

Como en el `Attach` ejemplo, la publicación sin valor de clave se detecta como nueva y se establece en el `Added` Estado. Las otras entidades se marcan como `Modified` :

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482633}]
Post {Id: -2147482633} Added
  Id: -2147482633 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

Llamar a `SaveChanges` en este punto hará que se envíen actualizaciones a la base de datos para todas las entidades existentes, mientras se inserta la nueva entidad. Por ejemplo, al usar SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

Se trata de una forma muy sencilla de generar actualizaciones e inserciones desde un grafo desconectado. Sin embargo, se generan actualizaciones o inserciones en la base de datos para cada propiedad de cada entidad de la que se realiza el seguimiento, incluso cuando es posible que no se hayan cambiado algunos valores de propiedad. Esto no es demasiado mezclarlas; para muchas aplicaciones con gráficos pequeños, esto puede ser una forma sencilla y pragmática de generar actualizaciones. Dicho esto, a veces otros patrones más complejos pueden dar lugar a actualizaciones más eficaces, como se describe en [resolución de identidades en EF Core](xref:core/change-tracking/identity-resolution).

## <a name="deleting-existing-entities"></a>Eliminar entidades existentes

Para que SaveChanges elimine una entidad, debe realizar el seguimiento en el `Deleted` Estado. Las entidades normalmente se colocan en el `Deleted` estado llamando a uno de <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType> los métodos equivalentes en <xref:Microsoft.EntityFrameworkCore.DbSet%601> . Por ejemplo, para marcar un post existente como `Deleted` :

<!--
            context.Remove(
                new Post
                {
                    Id = 2
                });
-->
[!code-csharp[Deleting_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_existing_entities_1)]

Al inspeccionar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de esta llamada, se muestra que el contexto está realizando el seguimiento de la entidad en el `Deleted` Estado:

```output
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: <null> FK
  Content: <null>
  Title: <null>
  Blog: <null>
```

Esta entidad se eliminará cuando se llame a SaveChanges. Por ejemplo, al usar SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

Una vez completado SaveChanges, la entidad eliminada se desasocia de DbContext, ya que ya no existe en la base de datos. Por lo tanto, la vista de depuración está vacía porque no se realiza el seguimiento de ninguna entidad.

## <a name="deleting-dependentchild-entities"></a>Eliminar entidades dependientes o secundarias

La eliminación de entidades dependientes o secundarias de un gráfico es más sencilla que la eliminación de entidades principales/primarias. Vea la sección siguiente y el [cambio de claves externas y navegaciones](xref:core/change-tracking/relationship-changes) para obtener más información.

No es habitual llamar a `Remove` en una entidad creada con `new` . Además, a diferencia `Add` de `Attach` y `Update` , no es habitual llamar a `Remove` en una entidad de la que aún no se ha realizado un seguimiento en el `Unchanged` `Modified` Estado o. En su lugar, es habitual realizar un seguimiento de una única entidad o gráfico de entidades relacionadas y, a continuación, llamar a `Remove` en las entidades que deben eliminarse. Este gráfico de entidades de las que se ha realizado un seguimiento se crea normalmente mediante:

1. Ejecutar una consulta para las entidades
2. Usar los `Attach` `Update` métodos o en un gráfico de entidades desconectadas, tal y como se describe en las secciones anteriores.

Por ejemplo, el código de la sección anterior es más probable que obtenga una publicación de un cliente y, a continuación, haga algo parecido a esto:

<!--
            context.Attach(post);
            context.Remove(post);
-->
[!code-csharp[Deleting_dependent_child_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_1)]

Esto se comporta exactamente de la misma manera que en el ejemplo anterior, ya que llamar a `Remove` en una entidad sin seguimiento hace que se adjunte primero y se marque como `Deleted` .

En ejemplos más realistas, un grafo de entidades se adjunta primero y, a continuación, algunas de esas entidades se marcan como eliminadas. Por ejemplo:

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_dependent_child_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_2)]

Todas las entidades se marcan como `Unchanged` , excepto aquella en la que `Remove` se llamó:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
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

Esta entidad se eliminará cuando se llame a SaveChanges. Por ejemplo, al usar SQLite:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

Una vez completado SaveChanges, la entidad eliminada se desasocia de DbContext, ya que ya no existe en la base de datos. Otras entidades permanecen en el `Unchanged` Estado:

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
```

## <a name="deleting-principalparent-entities"></a>Eliminar entidades principales/primarias

Cada relación que conecta dos tipos de entidad tiene un extremo principal o primario, y un extremo dependiente o secundario. La entidad dependiente/secundaria es la que tiene la propiedad de clave externa. En una relación de uno a varios, la entidad de seguridad principal se encuentra en el lado "uno" y el dependiente/secundario está en el lado "varios". Vea [relaciones](xref:core/modeling/relationships) para obtener más información.

En los ejemplos anteriores se eliminaba una publicación, que es una entidad dependiente/secundaria en el blog, que publica una relación de uno a varios. Esto es relativamente sencillo, ya que la eliminación de una entidad dependiente o secundaria no afecta a otras entidades. Por otro lado, la eliminación de una entidad principal/primaria también debe afectar a las entidades dependientes o secundarias. Si no lo hace, dejará un valor de clave externa que haga referencia a un valor de clave principal que ya no existe. Se trata de un estado de modelo no válido y genera un error de restricción referencial en la mayoría de las bases de datos.

Este estado de modelo no válido se puede controlar de dos maneras:

1. Estableciendo valores de FK en NULL. Esto indica que los elementos secundarios o dependientes ya no están relacionados con ninguna entidad de seguridad o elemento primario. Este es el valor predeterminado para las relaciones opcionales en las que la clave externa debe admitir valores NULL. Establecer el valor de FK en NULL no es válido para las relaciones necesarias, donde la clave externa normalmente no acepta valores NULL.
2. Eliminar los elementos dependientes o secundarios. Este es el valor predeterminado para las relaciones necesarias y también es válido para las relaciones opcionales.

Consulte [cambio de las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes) para obtener información detallada sobre el seguimiento de cambios y las relaciones.

### <a name="optional-relationships"></a>Relaciones opcionales

La `Post.BlogId` propiedad de clave externa admite valores NULL en el modelo que se ha usado. Esto significa que la relación es opcional y, por lo tanto, el comportamiento predeterminado de EF Core consiste en establecer `BlogId` las propiedades de clave externa en NULL cuando se elimine el blog. Por ejemplo:

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_principal_parent_entities_1)]

Al inspeccionar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de la llamada a `Remove` , se muestra que, como se esperaba, el blog ahora está marcado como `Deleted` :

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

Lo más interesante es que todos los comentarios relacionados se marcan ahora como `Modified` . Esto se debe a que la propiedad de clave externa de cada entidad se ha establecido en NULL. La llamada a SaveChanges actualiza el valor de clave externa de cada post a NULL en la base de datos, antes de eliminar el blog:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p2;
SELECT changes();
```

Una vez completado SaveChanges, la entidad eliminada se desasocia de DbContext, ya que ya no existe en la base de datos. Otras entidades se marcan ahora como `Unchanged` con valores de clave externa null, que coincide con el estado de la base de datos:

```output
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: <null> FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: <null> FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

### <a name="required-relationships"></a>Relaciones obligatorias

Si la `Post.BlogId` propiedad de clave externa no acepta valores NULL, la relación entre blogs y entradas se convierte en "Required". En esta situación, EF Core eliminará, de forma predeterminada, las entidades dependientes o secundarias cuando se elimine la entidad de seguridad/primaria. Por ejemplo, la eliminación de un blog con publicaciones relacionadas como en el ejemplo anterior:

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysRequiredSamples.cs?name=Deleting_principal_parent_entities_1)]

Al inspeccionar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de la llamada a `Remove` , se muestra que, como se esperaba, el blog se marca de nuevo como `Deleted` :

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Deleted
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

En este caso, lo más interesante es que todos los comentarios relacionados también se han marcado como `Deleted` . La llamada a SaveChanges hace que el blog y todos los envíos relacionados se eliminen de la base de datos:

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p1;
```

Una vez completado SaveChanges, todas las entidades eliminadas se desasocian de DbContext, ya que ya no existen en la base de datos. Por lo tanto, la salida de la vista de depuración está vacía.

> [!NOTE]
> Este documento solo borra la superficie del trabajo con relaciones en EF Core. Vea [relaciones](xref:core/modeling/relationships) para obtener más información sobre las relaciones de modelado y el [cambio de las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes) para obtener más información sobre cómo actualizar o eliminar entidades dependientes o secundarias al llamar a SaveChanges.

## <a name="custom-tracking-with-trackgraph"></a>Seguimiento personalizado con TrackGraph

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> funciona como `Add` `Attach` y, `Update` salvo que genera una devolución de llamada para cada instancia de entidad antes de realizar el seguimiento. Esto permite usar la lógica personalizada para determinar cómo realizar un seguimiento de las entidades individuales de un gráfico.

Por ejemplo, considere la regla EF Core usa al realizar el seguimiento de las entidades con valores de clave generados: Si el valor de clave es cero, la entidad es nueva y debe insertarse. Vamos a ampliar esta regla para indicar si el valor de clave es negativo, se debe eliminar la entidad. Esto nos permite cambiar los valores de la clave principal en las entidades de un grafo desconectado para marcar las entidades eliminadas:

<!--
            blog.Posts.Add(
                new Post
                {
                    Title = "Announcing .NET 5.0",
                    Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                }
            );

            var toDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            toDelete.Id = -toDelete.Id;
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1a](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1a)]

A continuación, se puede realizar el seguimiento de este gráfico desconectado mediante TrackGraph:

<!--
        public static void UpdateBlog(Blog blog)
        {
            using var context = new BlogsContext();

            context.ChangeTracker.TrackGraph(
                blog, node =>
                    {
                        var propertyEntry = node.Entry.Property("Id");
                        var keyValue = (int)propertyEntry.CurrentValue;

                        if (keyValue == 0)
                        {
                            node.Entry.State = EntityState.Added;
                        }
                        else if (keyValue < 0)
                        {
                            propertyEntry.CurrentValue = -keyValue;
                            node.Entry.State = EntityState.Deleted;
                        }
                        else
                        {
                            node.Entry.State = EntityState.Modified;
                        }

                        Console.WriteLine($"Tracking {node.Entry.Metadata.DisplayName()} with key value {keyValue} as {node.Entry.State}");

                    });

            context.SaveChanges();
        }
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1b](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1b)]

Para cada entidad del gráfico, el código anterior comprueba el valor de la clave principal _antes de realizar el seguimiento de la entidad_. En el caso de los valores de clave ununset (cero), el código hace lo que EF Core haría normalmente. Es decir, si no se establece la clave, la entidad se marca como `Added` . Si se establece la clave y el valor no es negativo, la entidad se marca como `Modified` . Sin embargo, si se encuentra un valor de clave negativo, se restaura su valor real, no negativo y se realiza el seguimiento de la entidad como `Deleted` .

La salida de la ejecución de este código es:

```output
Tracking Blog with key value 1 as Modified
Tracking Post with key value 1 as Modified
Tracking Post with key value -2 as Deleted
Tracking Post with key value 0 as Added
```

> [!NOTE]
> Para simplificar, en este código se supone que cada entidad tiene una propiedad de clave principal de entero denominada `Id` . Esto se podría codificar en una interfaz o clase base abstracta. Como alternativa, la propiedad o propiedades de la clave principal se pueden obtener a partir de los <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadatos de modo que este código funcione con cualquier tipo de entidad.

TrackGraph tiene dos sobrecargas. En la sobrecarga simple utilizada anteriormente, EF Core determina cuándo detener el recorrido del gráfico. En concreto, deja de visitar nuevas entidades relacionadas de una entidad determinada cuando ya se ha realizado el seguimiento de esa entidad o cuando la devolución de llamada no inicia el seguimiento de la entidad.

La sobrecarga avanzada, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType> , tiene una devolución de llamada que devuelve un booleano. Si la devolución de llamada devuelve false, el recorrido del gráfico se detiene; en caso contrario, continúa. Se debe tener cuidado para evitar bucles infinitos al utilizar esta sobrecarga.

La sobrecarga avanzada también permite proporcionar el estado a TrackGraph y, a continuación, este estado se pasa a cada devolución de llamada.
