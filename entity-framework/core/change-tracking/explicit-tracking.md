---
title: 'Seguimiento explícito de entidades: EF Core'
description: Seguimiento explícito de las entidades con DbContext mediante agregar, adjuntar, actualizar y quitar
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/explicit-tracking
ms.openlocfilehash: 28a6ec3e3c25dad70882b8681f78744a5979efe6
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129649"
---
# <a name="explicitly-tracking-entities"></a><span data-ttu-id="1e9c8-103">Seguimiento explícito de entidades</span><span class="sxs-lookup"><span data-stu-id="1e9c8-103">Explicitly Tracking Entities</span></span>

<span data-ttu-id="1e9c8-104">Cada <xref:Microsoft.EntityFrameworkCore.DbContext> instancia realiza un seguimiento de los cambios realizados en las entidades.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-104">Each <xref:Microsoft.EntityFrameworkCore.DbContext> instance tracks changes made to entities.</span></span> <span data-ttu-id="1e9c8-105">Estas entidades de las que se realiza un seguimiento convierten los cambios en la base de datos cuando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> se llama a.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-105">These tracked entities in turn drive the changes to the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="1e9c8-106">El seguimiento de cambios de Entity Framework Core (EF Core) funciona mejor cuando <xref:Microsoft.EntityFrameworkCore.DbContext> se utiliza la misma instancia para consultar las entidades y actualizarlas mediante una llamada a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-106">Entity Framework Core (EF Core) change tracking works best when the same <xref:Microsoft.EntityFrameworkCore.DbContext> instance is used to both query for entities and update them by calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="1e9c8-107">Esto se debe a que EF Core realiza un seguimiento automático del estado de las entidades consultadas y, a continuación, detecta los cambios realizados en estas entidades cuando se llama a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-107">This is because EF Core automatically tracks the state of queried entities and then detects any changes made to these entities when SaveChanges is called.</span></span> <span data-ttu-id="1e9c8-108">Este enfoque se trata en [Change Tracking en EF Core](xref:core/change-tracking/index).</span><span class="sxs-lookup"><span data-stu-id="1e9c8-108">This approach is covered in [Change Tracking in EF Core](xref:core/change-tracking/index).</span></span>

> [!TIP]
> <span data-ttu-id="1e9c8-109">En este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de EF Core.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-109">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="1e9c8-110">Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información sobre estos temas.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-110">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="1e9c8-111">Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo desde GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span><span class="sxs-lookup"><span data-stu-id="1e9c8-111">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore).</span></span>

> [!TIP]
> <span data-ttu-id="1e9c8-112">Para simplificar, en este documento se usan métodos sincrónicos y referencias como, por ejemplo, <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> sus equivalentes asincrónicos como <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-112">For simplicity, this document uses and references synchronous methods such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> rather their async equivalents such as <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A>.</span></span> <span data-ttu-id="1e9c8-113">Llamar a y esperar el método asincrónico se puede sustituir a menos que se indique lo contrario.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-113">Calling and awaiting the async method can be substituted unless otherwise noted.</span></span>

## <a name="introduction"></a><span data-ttu-id="1e9c8-114">Introducción</span><span class="sxs-lookup"><span data-stu-id="1e9c8-114">Introduction</span></span>

<span data-ttu-id="1e9c8-115">Las entidades se pueden "asociar" explícitamente a un de <xref:Microsoft.EntityFrameworkCore.DbContext> tal forma que el contexto realice un seguimiento de esas entidades.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-115">Entities can be explicitly "attached" to a <xref:Microsoft.EntityFrameworkCore.DbContext> such that the context then tracks those entities.</span></span> <span data-ttu-id="1e9c8-116">Esto es especialmente útil cuando:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-116">This is primarily useful when:</span></span>

1. <span data-ttu-id="1e9c8-117">Crear nuevas entidades que se van a insertar en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-117">Creating new entities that will be inserted into the database.</span></span>
2. <span data-ttu-id="1e9c8-118">Volver a adjuntar entidades desconectadas que se consultaron previamente mediante una instancia de DbContext _diferente_ .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-118">Re-attaching disconnected entities that were previously queried by a _different_ DbContext instance.</span></span>

<span data-ttu-id="1e9c8-119">La primera de ellas será necesaria para la mayoría de las aplicaciones y la principal la controlan los <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> métodos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-119">The first of these will be needed by most applications, and is primary handled by the <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> methods.</span></span>

<span data-ttu-id="1e9c8-120">La segunda solo es necesaria para las aplicaciones que cambian entidades o sus relaciones **_mientras no se realiza el seguimiento de las entidades_**.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-120">The second is only needed by applications that change entities or their relationships **_while the entities are not being tracked_**.</span></span> <span data-ttu-id="1e9c8-121">Por ejemplo, una aplicación web puede enviar entidades al cliente web donde el usuario realiza cambios y envía las entidades de nuevo.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-121">For example, a web application may send entities to the web client where the user makes changes and sends the entities back.</span></span> <span data-ttu-id="1e9c8-122">Estas entidades se conocen como "desconectadas", ya que originalmente se consultaron desde DbContext, pero se desconectaron desde ese contexto cuando se enviaron al cliente.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-122">These entities are referred to as "disconnected" since they were originally queried from a DbContext, but were then disconnected from that context when sent to the client.</span></span>

<span data-ttu-id="1e9c8-123">La aplicación web ahora debe volver a adjuntar estas entidades para que vuelvan a realizar el seguimiento e indiquen los cambios que se han realizado de forma que <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> puedan realizar las actualizaciones adecuadas en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-123">The web application must now re-attach these entities so that they are again tracked and indicate the changes that have been made such that <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> can make appropriate updates to the database.</span></span> <span data-ttu-id="1e9c8-124">Lo controlan principalmente los <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> métodos y <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-124">This is primarily handled by the <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> methods.</span></span>

> [!TIP]
> <span data-ttu-id="1e9c8-125">Normalmente no es necesario adjuntar entidades a la _misma instancia de DbContext_ de la que se han consultado.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-125">Attaching entities to the _same DbContext instance_ that they were queried from should not normally be needed.</span></span> <span data-ttu-id="1e9c8-126">No realice habitualmente una consulta sin seguimiento y, a continuación, asocie las entidades devueltas al mismo contexto.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-126">Do not routinely perform a no-tracking query and then attach the returned entities to the same context.</span></span> <span data-ttu-id="1e9c8-127">Esto será más lento que el uso de una consulta de seguimiento y puede dar lugar a problemas como los valores de las propiedades de sombra que faltan, lo que dificulta la obtención de derechos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-127">This will be slower than using a tracking query, and may also result in issues such as missing shadow property values, making it harder to get right.</span></span>

### <a name="generated-verses-explicit-key-values"></a><span data-ttu-id="1e9c8-128">Se generan valores de clave explícitos</span><span class="sxs-lookup"><span data-stu-id="1e9c8-128">Generated verses explicit key values</span></span>

<span data-ttu-id="1e9c8-129">De forma predeterminada, [las propiedades](xref:core/modeling/keys) Integer y de clave GUID están configuradas para utilizar [valores de clave generados automáticamente](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="1e9c8-129">By default, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="1e9c8-130">Esto tiene una **ventaja importante para el seguimiento de cambios: un valor de clave no establecido indica que la entidad es "nueva"**.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-130">This has a **major advantage for change tracking: an unset key value indicates that the entity is "new"**.</span></span> <span data-ttu-id="1e9c8-131">Por "nuevo", nos referimos a que todavía no se ha insertado en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-131">By "new", we mean that it has not yet been inserted into the database.</span></span>

<span data-ttu-id="1e9c8-132">En las secciones siguientes se usan dos modelos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-132">Two models are used in the following sections.</span></span> <span data-ttu-id="1e9c8-133">El primero se configura para **no** usar los valores de clave generados:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-133">The first is configured to **not** use generated key values:</span></span>

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

<span data-ttu-id="1e9c8-134">Los valores de clave no generados (es decir, establecidos explícitamente) se muestran en primer lugar en cada ejemplo porque todo es muy explícito y fácil de seguir.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-134">Non-generated (i.e. explicitly set) key values are shown first in each example because everything is very explicit and easy to follow.</span></span> <span data-ttu-id="1e9c8-135">A continuación, se sigue un ejemplo en el que se usan los valores de clave generados:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-135">This is then followed by an example where generated key values are used:</span></span>

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

<span data-ttu-id="1e9c8-136">Tenga en cuenta que las propiedades clave de este modelo no necesitan ninguna configuración adicional, ya que el uso de los valores de clave generados es el [valor predeterminado para las claves enteras simples](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="1e9c8-136">Notice that the key properties in this model need no additional configuration here since using generated key values is the [default for simple integer keys](xref:core/modeling/generated-properties).</span></span>

## <a name="inserting-new-entities"></a><span data-ttu-id="1e9c8-137">Insertar nuevas entidades</span><span class="sxs-lookup"><span data-stu-id="1e9c8-137">Inserting new entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="1e9c8-138">Valores de clave explícitos</span><span class="sxs-lookup"><span data-stu-id="1e9c8-138">Explicit key values</span></span>

<span data-ttu-id="1e9c8-139">Se debe realizar un seguimiento de una entidad en el `Added` Estado que va a insertar <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-139">An entity must be tracked in the `Added` state to be inserted by <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>.</span></span> <span data-ttu-id="1e9c8-140">Las entidades normalmente se colocan en el estado agregado mediante una llamada a uno de los <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType> métodos equivalentes de,,, <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType> o <xref:Microsoft.EntityFrameworkCore.DbSet%601> .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-140">Entities are typically put in the Added state by calling one of <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span>

> [!TIP]
> <span data-ttu-id="1e9c8-141">Estos métodos funcionan de la misma manera en el contexto del seguimiento de cambios.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-141">These methods all work in the same way in the context of change tracking.</span></span> <span data-ttu-id="1e9c8-142">Consulte [características adicionales de Change Tracking](xref:core/change-tracking/miscellaneous) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-142">See [Additional Change Tracking Features](xref:core/change-tracking/miscellaneous) for more information.</span></span>

<span data-ttu-id="1e9c8-143">Por ejemplo, para empezar a realizar el seguimiento de un nuevo blog:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-143">For example, to start tracking a new blog:</span></span>

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Inserting_new_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_1)]

<span data-ttu-id="1e9c8-144">Al inspeccionar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de esta llamada, se muestra que el contexto está realizando el seguimiento de la nueva entidad en el `Added` Estado:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-144">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking the new entity in the `Added` state:</span></span>

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

<span data-ttu-id="1e9c8-145">Sin embargo, los métodos Add no solo funcionan en una entidad individual.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-145">However, the Add methods don't just work on an individual entity.</span></span> <span data-ttu-id="1e9c8-146">En realidad, empiezan a realizar el seguimiento _de un gráfico completo de entidades relacionadas_, poniéndolos al `Added` Estado.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-146">They actually start tracking an _entire graph of related entities_, putting them all to the `Added` state.</span></span> <span data-ttu-id="1e9c8-147">Por ejemplo, para insertar un nuevo blog y las nuevas entradas asociadas:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-147">For example, to insert a new blog and associated new posts:</span></span>

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

<span data-ttu-id="1e9c8-148">Ahora el contexto está realizando el seguimiento de todas estas entidades como `Added` :</span><span class="sxs-lookup"><span data-stu-id="1e9c8-148">The context is now tracking all these entities as `Added`:</span></span>

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

<span data-ttu-id="1e9c8-149">Observe que se han establecido valores explícitos para las `Id` propiedades de clave en los ejemplos anteriores.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-149">Notice that explicit values have been set for the `Id` key properties in the examples above.</span></span> <span data-ttu-id="1e9c8-150">Esto se debe a que el modelo se ha configurado para utilizar valores de clave establecidos explícitamente, en lugar de valores de clave generados automáticamente.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-150">This is because the model here has been configured to use explicitly set key values, rather than automatically generated key values.</span></span> <span data-ttu-id="1e9c8-151">Cuando no se usan claves generadas, las propiedades de clave se deben establecer explícitamente _antes_ de llamar a `Add` .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-151">When not using generated keys, the key properties must be explicitly set _before_ calling `Add`.</span></span> <span data-ttu-id="1e9c8-152">Estos valores de clave se insertan después cuando se llama a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-152">These key values are then inserted when SaveChanges is called.</span></span> <span data-ttu-id="1e9c8-153">Por ejemplo, al usar SQLite:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-153">For example, when using SQLite:</span></span>

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

<span data-ttu-id="1e9c8-154">Se realiza un seguimiento de todas estas entidades en el `Unchanged` estado después de que se Complete SaveChanges, ya que estas entidades ahora existen en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-154">All of these entities are tracked in the `Unchanged` state after SaveChanges completes, since these entities now exist in the database:</span></span>

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

### <a name="generated-key-values"></a><span data-ttu-id="1e9c8-155">Valores de clave generados</span><span class="sxs-lookup"><span data-stu-id="1e9c8-155">Generated key values</span></span>

<span data-ttu-id="1e9c8-156">Como se mencionó anteriormente, [las propiedades de clave](xref:core/modeling/keys) de GUID y de entero se configuran para utilizar [los valores de clave generados automáticamente](xref:core/modeling/generated-properties) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-156">As mentioned above, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties) by default.</span></span> <span data-ttu-id="1e9c8-157">Esto significa que la aplicación _no debe establecer ningún valor de clave explícitamente_.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-157">This means that the application _must not set any key value explicitly_.</span></span> <span data-ttu-id="1e9c8-158">Por ejemplo, para insertar un nuevo blog y exponer todos los valores de clave generados:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-158">For example, to insert a new blog and posts all with generated key values:</span></span>

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

<span data-ttu-id="1e9c8-159">Al igual que con los valores de clave explícitos, el contexto está realizando el seguimiento de todas estas entidades como `Added` :</span><span class="sxs-lookup"><span data-stu-id="1e9c8-159">As with explicit key values, the context is now tracking all these entities as `Added`:</span></span>

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

<span data-ttu-id="1e9c8-160">Observe que, en este caso, se han generado [valores de clave temporales](xref:core/change-tracking/miscellaneous#temporary-values) para cada entidad.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-160">Notice in this case that [temporary key values](xref:core/change-tracking/miscellaneous#temporary-values) have been generated for each entity.</span></span> <span data-ttu-id="1e9c8-161">EF Core utilizan estos valores hasta que se llama a SaveChanges, en cuyo punto se leen los valores de clave reales de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-161">These values are used by EF Core until SaveChanges is called, at which point real key values are read back from the database.</span></span> <span data-ttu-id="1e9c8-162">Por ejemplo, al usar SQLite:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-162">For example, when using SQLite:</span></span>

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

<span data-ttu-id="1e9c8-163">Una vez completado SaveChanges, todas las entidades se han actualizado con sus valores de clave reales y se realiza el seguimiento en el `Unchanged` Estado, ya que ahora coinciden con el estado de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-163">After SaveChanges completes, all of the entities have been updated with their real key values and are tracked in the `Unchanged` state since they now match the state in the database:</span></span>

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

<span data-ttu-id="1e9c8-164">Este es exactamente el mismo estado final que el ejemplo anterior que usaba valores de clave explícitos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-164">This is exactly the same end-state as the previous example that used explicit key values.</span></span>

> [!TIP]
> <span data-ttu-id="1e9c8-165">Todavía se puede establecer un valor de clave explícito incluso cuando se usan valores de clave generados.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-165">An explicit key value can still be set even when using generated key values.</span></span> <span data-ttu-id="1e9c8-166">A continuación, EF Core intentará insertar con este valor de clave.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-166">EF Core will then attempt to insert using this key value.</span></span> <span data-ttu-id="1e9c8-167">Algunas configuraciones de base de datos, incluidas SQL Server con columnas de identidad, no admiten estas inserciones y se producirán.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-167">Some database configurations, including SQL Server with Identity columns, do not support such inserts and will throw.</span></span>

## <a name="attaching-existing-entities"></a><span data-ttu-id="1e9c8-168">Asociar entidades existentes</span><span class="sxs-lookup"><span data-stu-id="1e9c8-168">Attaching existing entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="1e9c8-169">Valores de clave explícitos</span><span class="sxs-lookup"><span data-stu-id="1e9c8-169">Explicit key values</span></span>

<span data-ttu-id="1e9c8-170">Se realiza un seguimiento de las entidades devueltas de las consultas en el `Unchanged` Estado.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-170">Entities returned from queries are tracked in the `Unchanged` state.</span></span> <span data-ttu-id="1e9c8-171">El `Unchanged` Estado significa que la entidad no se ha modificado desde que se realizó la consulta.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-171">The `Unchanged` state means that the entity has not been modified since it was queried.</span></span> <span data-ttu-id="1e9c8-172">Una entidad desconectada, tal vez que se devuelve desde un cliente web en una solicitud HTTP, se puede poner en este estado mediante <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> , <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType> o los métodos equivalentes en <xref:Microsoft.EntityFrameworkCore.DbSet%601> .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-172">A disconnected entity, perhaps returned from a web client in an HTTP request, can be put into this state using either <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span> <span data-ttu-id="1e9c8-173">Por ejemplo, para empezar a realizar el seguimiento de un blog existente:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-173">For example, to start tracking an existing blog:</span></span>

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
> <span data-ttu-id="1e9c8-174">Los ejemplos siguientes son crear entidades explícitamente con `new` para simplificar.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-174">The examples here are creating entities explicitly with `new` for simplicity.</span></span> <span data-ttu-id="1e9c8-175">Normalmente, las instancias de la entidad procederán de otro origen, como la deserialización de un cliente o la creación a partir de datos en un HTTP POST.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-175">Normally the entity instances will have come from another source, such as being deserialized from a client, or being created from data in an HTTP Post.</span></span>

<span data-ttu-id="1e9c8-176">Al inspeccionar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de esta llamada, se muestra que se realiza un seguimiento de la entidad en el `Unchanged` Estado:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-176">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the entity is tracked in the `Unchanged` state:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

<span data-ttu-id="1e9c8-177">Del mismo modo `Add` , `Attach` establece realmente un grafo completo de entidades conectadas en el `Unchanged` Estado.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-177">Just like `Add`, `Attach` actually sets an entire graph of connected entities to the `Unchanged` state.</span></span> <span data-ttu-id="1e9c8-178">Por ejemplo, para adjuntar un blog existente y las publicaciones existentes asociadas:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-178">For example, to attach an existing blog and associated existing posts:</span></span>

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

<span data-ttu-id="1e9c8-179">Ahora el contexto está realizando el seguimiento de todas estas entidades como `Unchanged` :</span><span class="sxs-lookup"><span data-stu-id="1e9c8-179">The context is now tracking all these entities as `Unchanged`:</span></span>

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

<span data-ttu-id="1e9c8-180">La llamada a SaveChanges en este momento no tendrá ningún efecto.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-180">Calling SaveChanges at this point will have no effect.</span></span> <span data-ttu-id="1e9c8-181">Todas las entidades se marcan como `Unchanged` , por lo que no hay nada que actualizar en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-181">All the entities are marked as `Unchanged`, so there is nothing to update in the database.</span></span>

### <a name="generated-key-values"></a><span data-ttu-id="1e9c8-182">Valores de clave generados</span><span class="sxs-lookup"><span data-stu-id="1e9c8-182">Generated key values</span></span>

<span data-ttu-id="1e9c8-183">Como se mencionó anteriormente, [las propiedades de clave](xref:core/modeling/keys) de GUID y de entero se configuran para utilizar [los valores de clave generados automáticamente](xref:core/modeling/generated-properties) de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-183">As mentioned above, integer and GUID [key properties](xref:core/modeling/keys) are configured to use [automatically generated key values](xref:core/modeling/generated-properties) by default.</span></span> <span data-ttu-id="1e9c8-184">Esto tiene una ventaja importante al trabajar con entidades desconectadas: un valor de clave no establecido indica que la entidad no se ha insertado todavía en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-184">This has a major advantage when working with disconnected entities: an unset key value indicates that the entity has not yet been inserted into the database.</span></span> <span data-ttu-id="1e9c8-185">Esto permite al seguimiento de cambios detectar automáticamente nuevas entidades y ponerlas en el `Added` Estado.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-185">This allows the change tracker to automatically detect new entities and put them in the `Added` state.</span></span> <span data-ttu-id="1e9c8-186">Por ejemplo, considere la posibilidad de adjuntar este gráfico de un blog y publicaciones:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-186">For example, consider attaching this graph of a blog and posts:</span></span>

```c#
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
```

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

<span data-ttu-id="1e9c8-187">El blog tiene un valor de clave de 1, que indica que ya existe en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-187">The blog has a key value of 1, indicating that it already exists in the database.</span></span> <span data-ttu-id="1e9c8-188">Dos de las publicaciones también tienen valores de clave establecidos, pero el tercero no los tiene.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-188">Two of the posts also have key values set, but the third does not.</span></span> <span data-ttu-id="1e9c8-189">EF Core verá este valor de clave como 0, el valor predeterminado de CLR para un entero.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-189">EF Core will see this key value as 0, the CLR default for an integer.</span></span> <span data-ttu-id="1e9c8-190">Esto da como resultado EF Core marcar la nueva entidad como `Added` en lugar de `Unchanged` :</span><span class="sxs-lookup"><span data-stu-id="1e9c8-190">This results in EF Core marking the new entity as `Added` instead of `Unchanged`:</span></span>

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

<span data-ttu-id="1e9c8-191">La llamada a SaveChanges en este momento no hace nada con las `Unchanged` entidades, sino que inserta la nueva entidad en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-191">Calling SaveChanges at this point does nothing with the `Unchanged` entities, but inserts the new entity into the database.</span></span> <span data-ttu-id="1e9c8-192">Por ejemplo, al usar SQLite:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-192">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

<span data-ttu-id="1e9c8-193">Lo importante que se debe tener en cuenta es que, con los valores de clave generados, EF Core es capaz de **distinguir automáticamente los nuevos de las entidades existentes en un gráfico desconectado**.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-193">The important point to notice here is that, with generated key values, EF Core is able to **automatically distinguish new from existing entities in a disconnected graph**.</span></span> <span data-ttu-id="1e9c8-194">En pocas palabras, cuando se usan claves generadas, EF Core siempre insertará una entidad cuando esa entidad no tenga establecido ningún valor de clave.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-194">In a nutshell, when using generated keys, EF Core will always insert an entity when that entity has no key value set.</span></span>

## <a name="updating-existing-entities"></a><span data-ttu-id="1e9c8-195">Actualización de entidades existentes</span><span class="sxs-lookup"><span data-stu-id="1e9c8-195">Updating existing entities</span></span>

### <a name="explicit-key-values"></a><span data-ttu-id="1e9c8-196">Valores de clave explícitos</span><span class="sxs-lookup"><span data-stu-id="1e9c8-196">Explicit key values</span></span>

<span data-ttu-id="1e9c8-197"><xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType> y los métodos equivalentes en <xref:Microsoft.EntityFrameworkCore.DbSet%601> se comportan exactamente igual que los `Attach` métodos descritos anteriormente, salvo que las entidades se colocan en `Modfied` en lugar de en el `Unchanged` Estado.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-197"><xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType>, and the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601> behave exactly as the `Attach` methods described above, except that entities are put into the `Modfied` instead of the `Unchanged` state.</span></span> <span data-ttu-id="1e9c8-198">Por ejemplo, para empezar a realizar el seguimiento de un blog existente como `Modified` :</span><span class="sxs-lookup"><span data-stu-id="1e9c8-198">For example, to start tracking an existing blog as `Modified`:</span></span>

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Updating_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_1)]

<span data-ttu-id="1e9c8-199">Al inspeccionar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de esta llamada, se muestra que el contexto está realizando el seguimiento de esta entidad en el `Modified` Estado:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-199">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking this entity in the `Modified` state:</span></span>

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: []
```

<span data-ttu-id="1e9c8-200">Al igual `Add` que con y `Attach` , en `Update` realidad marca un _grafo completo_ de entidades relacionadas como `Modified` .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-200">Just like with `Add` and `Attach`, `Update` actually marks an _entire graph_ of related entities as `Modified`.</span></span> <span data-ttu-id="1e9c8-201">Por ejemplo, para adjuntar un blog existente y los envíos existentes asociados como `Modified` :</span><span class="sxs-lookup"><span data-stu-id="1e9c8-201">For example, to attach an existing blog and associated existing posts as `Modified`:</span></span>

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

<span data-ttu-id="1e9c8-202">Ahora el contexto está realizando el seguimiento de todas estas entidades como `Modified` :</span><span class="sxs-lookup"><span data-stu-id="1e9c8-202">The context is now tracking all these entities as `Modified`:</span></span>

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

<span data-ttu-id="1e9c8-203">La llamada a SaveChanges en este punto hará que las actualizaciones se envíen a la base de datos para todas estas entidades.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-203">Calling SaveChanges at this point will cause updates to be sent to the database for all these entities.</span></span> <span data-ttu-id="1e9c8-204">Por ejemplo, al usar SQLite:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-204">For example, when using SQLite:</span></span>

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

### <a name="generated-key-values"></a><span data-ttu-id="1e9c8-205">Valores de clave generados</span><span class="sxs-lookup"><span data-stu-id="1e9c8-205">Generated key values</span></span>

<span data-ttu-id="1e9c8-206">Al igual que con `Attach` , los valores de clave generados tienen la misma ventaja principal para `Update` : un valor de clave no establecido indica que la entidad es nueva y aún no se ha insertado en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-206">As with `Attach`, generated key values have the same major benefit for `Update`: an unset key value indicates that the entity is new and has not yet been inserted into the database.</span></span> <span data-ttu-id="1e9c8-207">Como con `Attach` , esto permite que DbContext detecte automáticamente las nuevas entidades y las ponga en el `Added` Estado.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-207">As with `Attach`, this allows the DbContext to automatically detect new entities and put them in the `Added` state.</span></span> <span data-ttu-id="1e9c8-208">Por ejemplo, considere la posibilidad de llamar a `Update` con este gráfico de un blog y publicaciones:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-208">For example, consider calling `Update` with this graph of a blog and posts:</span></span>

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

<span data-ttu-id="1e9c8-209">Como en el `Attach` ejemplo, la publicación sin valor de clave se detecta como nueva y se establece en el `Added` Estado.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-209">As with the `Attach` example, the post with no key value is detected as new and set to the `Added` state.</span></span> <span data-ttu-id="1e9c8-210">Las otras entidades se marcan como `Modified` :</span><span class="sxs-lookup"><span data-stu-id="1e9c8-210">The other entities are marked as `Modified`:</span></span>

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

<span data-ttu-id="1e9c8-211">Llamar a `SaveChanges` en este punto hará que se envíen actualizaciones a la base de datos para todas las entidades existentes, mientras se inserta la nueva entidad.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-211">Calling `SaveChanges` at this point will cause updates to be sent to the database for all the existing entities, while the new entity is inserted.</span></span> <span data-ttu-id="1e9c8-212">Por ejemplo, al usar SQLite:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-212">For example, when using SQLite:</span></span>

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

<span data-ttu-id="1e9c8-213">Se trata de una forma muy sencilla de generar actualizaciones e inserciones desde un grafo desconectado.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-213">This is a very easy way to generate updates and inserts from a disconnected graph.</span></span> <span data-ttu-id="1e9c8-214">Sin embargo, se generan actualizaciones o inserciones en la base de datos para cada propiedad de cada entidad de la que se realiza el seguimiento, incluso cuando es posible que no se hayan cambiado algunos valores de propiedad.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-214">However, it results in updates or inserts being sent to the database for every property of every tracked entity, even when some property values may not have been changed.</span></span> <span data-ttu-id="1e9c8-215">Esto no es demasiado mezclarlas; para muchas aplicaciones con gráficos pequeños, esto puede ser una forma sencilla y pragmática de generar actualizaciones.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-215">Don't be too scared by this; for many applications with small graphs, this can be an easy and pragmatic way of generating updates.</span></span> <span data-ttu-id="1e9c8-216">Dicho esto, a veces otros patrones más complejos pueden dar lugar a actualizaciones más eficaces, como se describe en [resolución de identidades en EF Core](xref:core/change-tracking/identity-resolution).</span><span class="sxs-lookup"><span data-stu-id="1e9c8-216">That being said, other more complex patterns can sometimes result in more efficient updates, as described in [Identity Resolution in EF Core](xref:core/change-tracking/identity-resolution).</span></span>

## <a name="deleting-existing-entities"></a><span data-ttu-id="1e9c8-217">Eliminar entidades existentes</span><span class="sxs-lookup"><span data-stu-id="1e9c8-217">Deleting existing entities</span></span>

<span data-ttu-id="1e9c8-218">Para que SaveChanges elimine una entidad, debe realizar el seguimiento en el `Deleted` Estado.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-218">For an entity to be deleted by SaveChanges it must be tracked in the `Deleted` state.</span></span> <span data-ttu-id="1e9c8-219">Las entidades normalmente se colocan en el `Deleted` estado llamando a uno de <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType> los métodos equivalentes en <xref:Microsoft.EntityFrameworkCore.DbSet%601> .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-219">Entities are typically put in the `Deleted` state by calling one of <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType>, <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType>, or the equivalent methods on <xref:Microsoft.EntityFrameworkCore.DbSet%601>.</span></span> <span data-ttu-id="1e9c8-220">Por ejemplo, para marcar un post existente como `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="1e9c8-220">For example, to mark an existing post as `Deleted`:</span></span>

<!--
            context.Remove(
                new Post
                {
                    Id = 2
                });
-->
[!code-csharp[Deleting_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_existing_entities_1)]

<span data-ttu-id="1e9c8-221">Al inspeccionar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de esta llamada, se muestra que el contexto está realizando el seguimiento de la entidad en el `Deleted` Estado:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-221">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following this call shows that the context is tracking the entity in the `Deleted` state:</span></span>

```output
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: <null> FK
  Content: <null>
  Title: <null>
  Blog: <null>
```

<span data-ttu-id="1e9c8-222">Esta entidad se eliminará cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-222">This entity will be deleted when SaveChanges is called.</span></span> <span data-ttu-id="1e9c8-223">Por ejemplo, al usar SQLite:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-223">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

<span data-ttu-id="1e9c8-224">Una vez completado SaveChanges, la entidad eliminada se desasocia de DbContext, ya que ya no existe en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-224">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="1e9c8-225">Por lo tanto, la vista de depuración está vacía porque no se realiza el seguimiento de ninguna entidad.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-225">The debug view is therefore empty because no entities are being tracked.</span></span>

## <a name="deleting-dependentchild-entities"></a><span data-ttu-id="1e9c8-226">Eliminar entidades dependientes o secundarias</span><span class="sxs-lookup"><span data-stu-id="1e9c8-226">Deleting dependent/child entities</span></span>

<span data-ttu-id="1e9c8-227">La eliminación de entidades dependientes o secundarias de un gráfico es más sencilla que la eliminación de entidades principales/primarias.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-227">Deleting dependent/child entities from a graph is more straightforward than deleting principal/parent entities.</span></span> <span data-ttu-id="1e9c8-228">Vea la sección siguiente y el [cambio de claves externas y navegaciones](xref:core/change-tracking/relationship-changes) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-228">See the next section and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information.</span></span>

<span data-ttu-id="1e9c8-229">No es habitual llamar a `Remove` en una entidad creada con `new` .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-229">It is unusual to call `Remove` on an entity created with `new`.</span></span> <span data-ttu-id="1e9c8-230">Además, a diferencia `Add` de `Attach` y `Update` , no es habitual llamar a `Remove` en una entidad de la que aún no se ha realizado un seguimiento en el `Unchanged` `Modified` Estado o.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-230">Further, unlike `Add`, `Attach` and `Update`, it is uncommon to call `Remove` on an entity that isn't already tracked in the `Unchanged` or `Modified` state.</span></span> <span data-ttu-id="1e9c8-231">En su lugar, es habitual realizar un seguimiento de una única entidad o gráfico de entidades relacionadas y, a continuación, llamar a `Remove` en las entidades que deben eliminarse.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-231">Instead it is typical to track a single entity or graph of related entities, and then call `Remove` on the entities that should be deleted.</span></span> <span data-ttu-id="1e9c8-232">Este gráfico de entidades de las que se ha realizado un seguimiento se crea normalmente mediante:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-232">This graph of tracked entities is typically created by either:</span></span>

1. <span data-ttu-id="1e9c8-233">Ejecutar una consulta para las entidades</span><span class="sxs-lookup"><span data-stu-id="1e9c8-233">Running a query for the entities</span></span>
2. <span data-ttu-id="1e9c8-234">Usar los `Attach` `Update` métodos o en un gráfico de entidades desconectadas, tal y como se describe en las secciones anteriores.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-234">Using the `Attach` or `Update` methods on a graph of disconnected entities, as described in the preceding sections.</span></span>

<span data-ttu-id="1e9c8-235">Por ejemplo, el código de la sección anterior es más probable que obtenga una publicación de un cliente y, a continuación, haga algo parecido a esto:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-235">For example, the code in the previous section is more likely obtain a post from a client and then do something like this:</span></span>

<!--
            context.Attach(post);
            context.Remove(post);
-->
[!code-csharp[Deleting_dependent_child_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_1)]

<span data-ttu-id="1e9c8-236">Esto se comporta exactamente de la misma manera que en el ejemplo anterior, ya que llamar a `Remove` en una entidad sin seguimiento hace que se adjunte primero y se marque como `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-236">This behaves exactly the same way as the previous example, since calling `Remove` on an un-tracked entity causes it to first be attached and then marked as `Deleted`.</span></span>

<span data-ttu-id="1e9c8-237">En ejemplos más realistas, un grafo de entidades se adjunta primero y, a continuación, algunas de esas entidades se marcan como eliminadas.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-237">In more realistic examples, a graph of entities is first attached, and then some of those entities are marked as deleted.</span></span> <span data-ttu-id="1e9c8-238">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-238">For example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_dependent_child_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_2)]

<span data-ttu-id="1e9c8-239">Todas las entidades se marcan como `Unchanged` , excepto aquella en la que `Remove` se llamó:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-239">All entities are marked as `Unchanged`, except the one on which `Remove` was called:</span></span>

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

<span data-ttu-id="1e9c8-240">Esta entidad se eliminará cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-240">This entity will be deleted when SaveChanges is called.</span></span> <span data-ttu-id="1e9c8-241">Por ejemplo, al usar SQLite:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-241">For example, when using SQLite:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

<span data-ttu-id="1e9c8-242">Una vez completado SaveChanges, la entidad eliminada se desasocia de DbContext, ya que ya no existe en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-242">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="1e9c8-243">Otras entidades permanecen en el `Unchanged` Estado:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-243">Other entities remain in the `Unchanged` state:</span></span>

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

## <a name="deleting-principalparent-entities"></a><span data-ttu-id="1e9c8-244">Eliminar entidades principales/primarias</span><span class="sxs-lookup"><span data-stu-id="1e9c8-244">Deleting principal/parent entities</span></span>

<span data-ttu-id="1e9c8-245">Cada relación que conecta dos tipos de entidad tiene un extremo principal o primario, y un extremo dependiente o secundario.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-245">Each relationship that connects two entity types has a principal or parent end, and a dependent or child end.</span></span> <span data-ttu-id="1e9c8-246">La entidad dependiente/secundaria es la que tiene la propiedad de clave externa.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-246">The dependent/child entity is the one with the foreign key property.</span></span> <span data-ttu-id="1e9c8-247">En una relación de uno a varios, la entidad de seguridad principal se encuentra en el lado "uno" y el dependiente/secundario está en el lado "varios".</span><span class="sxs-lookup"><span data-stu-id="1e9c8-247">In a one-to-many relationship, the principal/parent is on the "one" side, and the dependent/child is on the "many" side.</span></span> <span data-ttu-id="1e9c8-248">Vea [relaciones](xref:core/modeling/relationships) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-248">See [Relationships](xref:core/modeling/relationships) for more information.</span></span>

<span data-ttu-id="1e9c8-249">En los ejemplos anteriores se eliminaba una publicación, que es una entidad dependiente/secundaria en el blog, que publica una relación de uno a varios.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-249">In the preceding examples we were deleting a post, which is a dependent/child entity in the blog-posts one-to-many relationship.</span></span> <span data-ttu-id="1e9c8-250">Esto es relativamente sencillo, ya que la eliminación de una entidad dependiente o secundaria no afecta a otras entidades.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-250">This is relatively straightforward since removal of a dependent/child entity does not have any impact on other entities.</span></span> <span data-ttu-id="1e9c8-251">Por otro lado, la eliminación de una entidad principal/primaria también debe afectar a las entidades dependientes o secundarias.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-251">On the other hand, deleting a principal/parent entity must also impact any dependent/child entities.</span></span> <span data-ttu-id="1e9c8-252">Si no lo hace, dejará un valor de clave externa que haga referencia a un valor de clave principal que ya no existe.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-252">Not doing so would leave a foreign key value referencing a primary key value that no longer exists.</span></span> <span data-ttu-id="1e9c8-253">Se trata de un estado de modelo no válido y genera un error de restricción referencial en la mayoría de las bases de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-253">This is an invalid model state and results in a referential constraint error in most databases.</span></span>

<span data-ttu-id="1e9c8-254">Este estado de modelo no válido se puede controlar de dos maneras:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-254">This invalid model state can be handled in two ways:</span></span>

1. <span data-ttu-id="1e9c8-255">Estableciendo valores de FK en NULL.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-255">Setting FK values to null.</span></span> <span data-ttu-id="1e9c8-256">Esto indica que los elementos secundarios o dependientes ya no están relacionados con ninguna entidad de seguridad o elemento primario.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-256">This indicates that the dependents/children are no longer related to any principal/parent.</span></span> <span data-ttu-id="1e9c8-257">Este es el valor predeterminado para las relaciones opcionales en las que la clave externa debe admitir valores NULL.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-257">This is the default for optional relationships where the foreign key must be nullable.</span></span> <span data-ttu-id="1e9c8-258">Establecer el valor de FK en NULL no es válido para las relaciones necesarias, donde la clave externa normalmente no acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-258">Setting the FK to null is not valid for required relationships, where the foreign key is typically non-nullable.</span></span>
2. <span data-ttu-id="1e9c8-259">Eliminar los elementos dependientes o secundarios.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-259">Deleting the the dependents/children.</span></span> <span data-ttu-id="1e9c8-260">Este es el valor predeterminado para las relaciones necesarias y también es válido para las relaciones opcionales.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-260">This is the default for required relationships, and is also valid for optional relationships.</span></span>

<span data-ttu-id="1e9c8-261">Consulte [cambio de las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes) para obtener información detallada sobre el seguimiento de cambios y las relaciones.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-261">See [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for detailed information on change tracking and relationships.</span></span>

### <a name="optional-relationships"></a><span data-ttu-id="1e9c8-262">Relaciones opcionales</span><span class="sxs-lookup"><span data-stu-id="1e9c8-262">Optional relationships</span></span>

<span data-ttu-id="1e9c8-263">La `Post.BlogId` propiedad de clave externa admite valores NULL en el modelo que se ha usado.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-263">The `Post.BlogId` foreign key property is nullable in the model we have been using.</span></span> <span data-ttu-id="1e9c8-264">Esto significa que la relación es opcional y, por lo tanto, el comportamiento predeterminado de EF Core consiste en establecer `BlogId` las propiedades de clave externa en NULL cuando se elimine el blog.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-264">This means the relationship is optional, and hence the default behavior of EF Core is to set `BlogId` foreign key properties to null when the blog is deleted.</span></span> <span data-ttu-id="1e9c8-265">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-265">For example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_principal_parent_entities_1)]

<span data-ttu-id="1e9c8-266">Al inspeccionar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de la llamada a `Remove` , se muestra que, como se esperaba, el blog ahora está marcado como `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="1e9c8-266">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following the call to `Remove` shows that, as expected, the blog is now marked as `Deleted`:</span></span>

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

<span data-ttu-id="1e9c8-267">Lo más interesante es que todos los comentarios relacionados se marcan ahora como `Modified` .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-267">More interestingly, all the related posts are now marked as `Modified`.</span></span> <span data-ttu-id="1e9c8-268">Esto se debe a que la propiedad de clave externa de cada entidad se ha establecido en NULL.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-268">This is because the foreign key property in each entity has been set to null.</span></span> <span data-ttu-id="1e9c8-269">La llamada a SaveChanges actualiza el valor de clave externa de cada post a NULL en la base de datos, antes de eliminar el blog:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-269">Calling SaveChanges updates the foreign key value for each post to null in the database, before then deleting the blog:</span></span>

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

<span data-ttu-id="1e9c8-270">Una vez completado SaveChanges, la entidad eliminada se desasocia de DbContext, ya que ya no existe en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-270">After SaveChanges completes, the deleted entity is detached from the DbContext since it no longer exists in the database.</span></span> <span data-ttu-id="1e9c8-271">Otras entidades se marcan ahora como `Unchanged` con valores de clave externa null, que coincide con el estado de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-271">Other entities are now marked as `Unchanged` with null foreign key values, which matches the state of the database:</span></span>

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

### <a name="required-relationships"></a><span data-ttu-id="1e9c8-272">Relaciones obligatorias</span><span class="sxs-lookup"><span data-stu-id="1e9c8-272">Required relationships</span></span>

<span data-ttu-id="1e9c8-273">Si la `Post.BlogId` propiedad de clave externa no acepta valores NULL, la relación entre blogs y entradas se convierte en "Required".</span><span class="sxs-lookup"><span data-stu-id="1e9c8-273">If the `Post.BlogId` foreign key property is non-nullable, then the relationship between blogs and posts becomes "required".</span></span> <span data-ttu-id="1e9c8-274">En esta situación, EF Core eliminará, de forma predeterminada, las entidades dependientes o secundarias cuando se elimine la entidad de seguridad/primaria.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-274">In this situation, EF Core will, by default, delete dependent/child entities when the principal/parent is deleted.</span></span> <span data-ttu-id="1e9c8-275">Por ejemplo, la eliminación de un blog con publicaciones relacionadas como en el ejemplo anterior:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-275">For example, deleting a blog with related posts as in the previous example:</span></span>

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysRequiredSamples.cs?name=Deleting_principal_parent_entities_1)]

<span data-ttu-id="1e9c8-276">Al inspeccionar la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) después de la llamada a `Remove` , se muestra que, como se esperaba, el blog se marca de nuevo como `Deleted` :</span><span class="sxs-lookup"><span data-stu-id="1e9c8-276">Inspecting the [change tracker debug view](xref:core/change-tracking/debug-views) following the call to `Remove` shows that, as expected, the blog is again marked as `Deleted`:</span></span>

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

<span data-ttu-id="1e9c8-277">En este caso, lo más interesante es que todos los comentarios relacionados también se han marcado como `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-277">More interestingly in this case is that all related posts have also been marked as `Deleted`.</span></span> <span data-ttu-id="1e9c8-278">La llamada a SaveChanges hace que el blog y todos los envíos relacionados se eliminen de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-278">Calling SaveChanges causes the blog and all related posts to be deleted from the database:</span></span>

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

<span data-ttu-id="1e9c8-279">Una vez completado SaveChanges, todas las entidades eliminadas se desasocian de DbContext, ya que ya no existen en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-279">After SaveChanges completes, all the deleted entities are detached from the DbContext since they no longer exist in the database.</span></span> <span data-ttu-id="1e9c8-280">Por lo tanto, la salida de la vista de depuración está vacía.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-280">Output from the debug view is therefore empty.</span></span>

> [!NOTE]
> <span data-ttu-id="1e9c8-281">Este documento solo borra la superficie del trabajo con relaciones en EF Core.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-281">This document only scratches the surface on working with relationships in EF Core.</span></span> <span data-ttu-id="1e9c8-282">Vea [relaciones](xref:core/modeling/relationships) para obtener más información sobre las relaciones de modelado y el [cambio de las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes) para obtener más información sobre cómo actualizar o eliminar entidades dependientes o secundarias al llamar a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-282">See [Relationships](xref:core/modeling/relationships) for more information on modeling relationships, and [Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) for more information on updating/deleting dependent/child entities when calling SaveChanges.</span></span>

## <a name="custom-tracking-with-trackgraph"></a><span data-ttu-id="1e9c8-283">Seguimiento personalizado con TrackGraph</span><span class="sxs-lookup"><span data-stu-id="1e9c8-283">Custom tracking with TrackGraph</span></span>

<span data-ttu-id="1e9c8-284"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> funciona como `Add` `Attach` y, `Update` salvo que genera una devolución de llamada para cada instancia de entidad antes de realizar el seguimiento.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-284"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> works like `Add`, `Attach` and `Update` except that it generates a callback for every entity instance before tracking it.</span></span> <span data-ttu-id="1e9c8-285">Esto permite usar la lógica personalizada para determinar cómo realizar un seguimiento de las entidades individuales de un gráfico.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-285">This allows custom logic to be used when determining how to track individual entities in a graph.</span></span>

<span data-ttu-id="1e9c8-286">Por ejemplo, considere la regla EF Core usa al realizar el seguimiento de las entidades con valores de clave generados: Si el valor de KYE es cero, la entidad es nueva y debe insertarse.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-286">For example, consider the rule EF Core uses when tracking entities with generated key values: if the kye value is zero, then the entity is new and should be inserted.</span></span> <span data-ttu-id="1e9c8-287">Vamos a ampliar esta regla para indicar si el valor de clave es negativo, se debe eliminar la entidad.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-287">Let's extend this rule to say if the key value is negative, then the entity should be deleted.</span></span> <span data-ttu-id="1e9c8-288">Esto nos permite cambiar los valores de la clave principal en las entidades de un grafo desconectado para marcar las entidades eliminadas:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-288">This allows us to change the primary key values in entities of a disconnected graph to mark deleted entities:</span></span>

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

<span data-ttu-id="1e9c8-289">A continuación, se puede realizar el seguimiento de este gráfico desconectado mediante TrackGraph:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-289">This disconnected graph can then be tracked using TrackGraph:</span></span>

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

<span data-ttu-id="1e9c8-290">Para cada entidad del gráfico, el código anterior comprueba el valor de la clave principal _antes de realizar el seguimiento de la entidad_.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-290">For each entity in the graph, the code above checks the primary key value _before tracking the entity_.</span></span> <span data-ttu-id="1e9c8-291">En el caso de los valores de clave ununset (cero), el código hace lo que EF Core haría normalmente.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-291">For unset (zero) key values, the code does what EF Core would normally do.</span></span> <span data-ttu-id="1e9c8-292">Es decir, si no se establece la clave, la entidad se marca como `Added` .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-292">That is, if the key is not set, then the entity is marked as `Added`.</span></span> <span data-ttu-id="1e9c8-293">Si se establece la clave y el valor no es negativo, la entidad se marca como `Modified` .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-293">If the key is set and the value is non-negative, then the entity is marked as `Modified`.</span></span> <span data-ttu-id="1e9c8-294">Sin embargo, si se encuentra un valor de clave negativo, se restaura su valor real, no negativo y se realiza el seguimiento de la entidad como `Deleted` .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-294">However, if a negative key value is found, then its real, non-negative value is restored and the entity is tracked as `Deleted`.</span></span>

<span data-ttu-id="1e9c8-295">La salida de la ejecución de este código es:</span><span class="sxs-lookup"><span data-stu-id="1e9c8-295">The output from running this code is:</span></span>

```output
Tracking Blog with key value 1 as Modified
Tracking Post with key value 1 as Modified
Tracking Post with key value -2 as Deleted
Tracking Post with key value 0 as Added
```

> [!NOTE]
> <span data-ttu-id="1e9c8-296">Para simplificar, en este código se supone que cada entidad tiene una propiedad de clave principal de entero denominada `Id` .</span><span class="sxs-lookup"><span data-stu-id="1e9c8-296">For simplicity, this code assumes each entity has an integer primary key property called `Id`.</span></span> <span data-ttu-id="1e9c8-297">Esto se podría codificar en una interfaz o clase base abstracta.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-297">This could be codified into an abstract base class or interface.</span></span> <span data-ttu-id="1e9c8-298">Como alternativa, la propiedad o propiedades de la clave principal se pueden obtener a partir de los <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadatos de modo que este código funcione con cualquier tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-298">Alternately, the primary key property or properties could be obtained from the <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> metadata such that this code would work with any type of entity.</span></span>

<span data-ttu-id="1e9c8-299">TrackGraph tiene dos sobrecargas.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-299">TrackGraph has two overloads.</span></span> <span data-ttu-id="1e9c8-300">En la sobrecarga simple utilizada anteriormente, EF Core determina cuándo detener el recorrido del gráfico.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-300">In the simple overload used above, EF Core determines when to stop traversing the graph.</span></span> <span data-ttu-id="1e9c8-301">En concreto, deja de visitar nuevas entidades relacionadas de una entidad determinada cuando ya se ha realizado el seguimiento de esa entidad o cuando la devolución de llamada no inicia el seguimiento de la entidad.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-301">Specifically, it stops visiting new related entities from a given entity when that entity is either already tracked, or when the callback does not start tracking the entity.</span></span>

<span data-ttu-id="1e9c8-302">La sobrecarga avanzada, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType> , tiene una devolución de llamada que devuelve un booleano.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-302">The advanced overload, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType>, has a callback that returns a bool.</span></span> <span data-ttu-id="1e9c8-303">Si la devolución de llamada devuelve false, el recorrido del gráfico se detiene; en caso contrario, continúa.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-303">If the callback returns false, then graph traversal stops, otherwise it continues.</span></span> <span data-ttu-id="1e9c8-304">Se debe tener cuidado para evitar bucles infinitos al utilizar esta sobrecarga.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-304">Care must be taken to avoid infinite loops when using this overload.</span></span>

<span data-ttu-id="1e9c8-305">La sobrecarga avanzada también permite proporcionar el estado a TrackGraph y, a continuación, este estado se pasa a cada devolución de llamada.</span><span class="sxs-lookup"><span data-stu-id="1e9c8-305">The advanced overload also allows state to be supplied to TrackGraph and this state is then passed to each callback.</span></span>
