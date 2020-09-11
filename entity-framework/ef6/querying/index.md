---
title: 'Consulta y búsqueda de entidades: EF6'
description: Consulta y búsqueda de entidades en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 65bb3db2-2226-44af-8864-caa575cf1b46
uid: ef6/querying/index
ms.openlocfilehash: 4bfdc59527ab75e7a2e08dc743a1974246643aa3
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620329"
---
# <a name="querying-and-finding-entities"></a><span data-ttu-id="4e745-103">Consulta y búsqueda de entidades</span><span class="sxs-lookup"><span data-stu-id="4e745-103">Querying and Finding Entities</span></span>
<span data-ttu-id="4e745-104">En este tema se tratan las distintas formas de consultar datos mediante Entity Framework, incluidos LINQ y el método Find.</span><span class="sxs-lookup"><span data-stu-id="4e745-104">This topic covers the various ways you can query for data using Entity Framework, including LINQ and the Find method.</span></span> <span data-ttu-id="4e745-105">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="4e745-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="finding-entities-using-a-query"></a><span data-ttu-id="4e745-106">Búsqueda de entidades mediante una consulta</span><span class="sxs-lookup"><span data-stu-id="4e745-106">Finding entities using a query</span></span>  

<span data-ttu-id="4e745-107">DbSet e IDbSet implementan IQueryable, por lo que pueden usarse como punto de partida para escribir una consulta LINQ en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4e745-107">DbSet and IDbSet implement IQueryable and so can be used as the starting point for writing a LINQ query against the database.</span></span> <span data-ttu-id="4e745-108">Este no es el lugar adecuado para una explicación detallada sobre LINQ, pero aquí tiene un par de ejemplos sencillos:</span><span class="sxs-lookup"><span data-stu-id="4e745-108">This is not the appropriate place for an in-depth discussion of LINQ, but here are a couple of simple examples:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs with names starting with B
    var blogs = from b in context.Blogs
                   where b.Name.StartsWith("B")
                   select b;

    // Query for the Blog named ADO.NET Blog
    var blog = context.Blogs
                    .Where(b => b.Name == "ADO.NET Blog")
                    .FirstOrDefault();
}
```  

<span data-ttu-id="4e745-109">Tenga en cuenta que DbSet e IDbSet siempre crean las consultas en la base de datos, lo que siempre conlleva un viaje de ida y vuelta a la base de datos, aun cuando las entidades devueltas ya existan en el contexto.</span><span class="sxs-lookup"><span data-stu-id="4e745-109">Note that DbSet and IDbSet always create queries against the database and will always involve a round trip to the database even if the entities returned already exist in the context.</span></span> <span data-ttu-id="4e745-110">Una consulta se ejecuta en la base de datos cuando:</span><span class="sxs-lookup"><span data-stu-id="4e745-110">A query is executed against the database when:</span></span>  

- <span data-ttu-id="4e745-111">Se enumera mediante una instrucción **foreach** (C#) o **For Each** (Visual Basic).</span><span class="sxs-lookup"><span data-stu-id="4e745-111">It is enumerated by a **foreach** (C#) or **For Each** (Visual Basic) statement.</span></span>  
- <span data-ttu-id="4e745-112">Se enumera mediante una operación de recopilación como [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary) o [ToList](https://msdn.microsoft.com/library/bb342261).</span><span class="sxs-lookup"><span data-stu-id="4e745-112">It is enumerated by a collection operation such as [ToArray](https://msdn.microsoft.com/library/bb298736), [ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary), or [ToList](https://msdn.microsoft.com/library/bb342261).</span></span>  
- <span data-ttu-id="4e745-113">Los operadores LINQ, como [First](https://msdn.microsoft.com/library/bb291976) o [Any](https://msdn.microsoft.com/library/bb337697), se especifican en la parte más externa de la consulta.</span><span class="sxs-lookup"><span data-stu-id="4e745-113">LINQ operators such as [First](https://msdn.microsoft.com/library/bb291976) or [Any](https://msdn.microsoft.com/library/bb337697) are specified in the outermost part of the query.</span></span>  
- <span data-ttu-id="4e745-114">Se llama a los métodos siguientes: el método de extensión [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) en DbSet, [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx) y Database.ExecuteSqlCommand.</span><span class="sxs-lookup"><span data-stu-id="4e745-114">The following methods are called: the [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) extension method on a DbSet, [DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx), and Database.ExecuteSqlCommand.</span></span>  

<span data-ttu-id="4e745-115">Cuando se devuelven los resultados de la base de datos, los objetos que no existen en el contexto se adjuntan a él.</span><span class="sxs-lookup"><span data-stu-id="4e745-115">When results are returned from the database, objects that do not exist in the context are attached to the context.</span></span> <span data-ttu-id="4e745-116">Si un objeto ya está en el contexto, se devuelve el objeto existente (los valores actual y original de las propiedades del objeto en la entrada **no** se sobrescriben con valores de la base de datos).</span><span class="sxs-lookup"><span data-stu-id="4e745-116">If an object is already in the context, the existing object is returned (the current and original values of the object's properties in the entry are **not** overwritten with database values).</span></span>  

<span data-ttu-id="4e745-117">Cuando se realiza una consulta, las entidades que se han agregado al contexto pero aún no se han guardado en la base de datos no se devuelven como parte del conjunto de resultados.</span><span class="sxs-lookup"><span data-stu-id="4e745-117">When you perform a query, entities that have been added to the context but have not yet been saved to the database are not returned as part of the result set.</span></span> <span data-ttu-id="4e745-118">Para obtener los datos que se encuentran en el contexto, vea [Datos locales](xref:ef6/querying/local-data).</span><span class="sxs-lookup"><span data-stu-id="4e745-118">To get the data that is in the context, see [Local Data](xref:ef6/querying/local-data).</span></span>  

<span data-ttu-id="4e745-119">Si una consulta no devuelve filas de la base de datos, el resultado será una colección vacía, en lugar de **null**.</span><span class="sxs-lookup"><span data-stu-id="4e745-119">If a query returns no rows from the database, the result will be an empty collection, rather than **null**.</span></span>  

## <a name="finding-entities-using-primary-keys"></a><span data-ttu-id="4e745-120">Búsqueda de entidades mediante claves principales</span><span class="sxs-lookup"><span data-stu-id="4e745-120">Finding entities using primary keys</span></span>  

<span data-ttu-id="4e745-121">El método Find de DbSet usa el valor de clave principal para intentar buscar una entidad cuyo seguimiento realiza el contexto.</span><span class="sxs-lookup"><span data-stu-id="4e745-121">The Find method on DbSet uses the primary key value to attempt to find an entity tracked by the context.</span></span> <span data-ttu-id="4e745-122">Si la entidad no se encuentra en el contexto, se envía una consulta a la base de datos para buscar la entidad allí.</span><span class="sxs-lookup"><span data-stu-id="4e745-122">If the entity is not found in the context then a query will be sent to the database to find the entity there.</span></span> <span data-ttu-id="4e745-123">Si la entidad no se encuentra en el contexto ni en la base de datos, se devuelve null.</span><span class="sxs-lookup"><span data-stu-id="4e745-123">Null is returned if the entity is not found in the context or in the database.</span></span>  

<span data-ttu-id="4e745-124">Find se diferencia del uso de una consulta en dos aspectos importantes:</span><span class="sxs-lookup"><span data-stu-id="4e745-124">Find is different from using a query in two significant ways:</span></span>  

- <span data-ttu-id="4e745-125">Solo se realiza un viaje de ida y vuelta a la base de datos si la entidad con la clave especificada no se encuentra en el contexto.</span><span class="sxs-lookup"><span data-stu-id="4e745-125">A round-trip to the database will only be made if the entity with the given key is not found in the context.</span></span>  
- <span data-ttu-id="4e745-126">Find devuelve entidades que están en estado Added.</span><span class="sxs-lookup"><span data-stu-id="4e745-126">Find will return entities that are in the Added state.</span></span> <span data-ttu-id="4e745-127">Es decir, Find devuelve entidades que se han agregado al contexto pero que aún no se han guardado en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4e745-127">That is, Find will return entities that have been added to the context but have not yet been saved to the database.</span></span>  
### <a name="finding-an-entity-by-primary-key"></a><span data-ttu-id="4e745-128">Búsqueda de una entidad por clave principal</span><span class="sxs-lookup"><span data-stu-id="4e745-128">Finding an entity by primary key</span></span>  

<span data-ttu-id="4e745-129">En el código siguiente se muestran algunos usos de Find:</span><span class="sxs-lookup"><span data-stu-id="4e745-129">The following code shows some uses of Find:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Will hit the database
    var blog = context.Blogs.Find(3);

    // Will return the same instance without hitting the database
    var blogAgain = context.Blogs.Find(3);

    context.Blogs.Add(new Blog { Id = -1 });

    // Will find the new blog even though it does not exist in the database
    var newBlog = context.Blogs.Find(-1);

    // Will find a User which has a string primary key
    var user = context.Users.Find("johndoe1987");
}
```  

### <a name="finding-an-entity-by-composite-primary-key"></a><span data-ttu-id="4e745-130">Búsqueda de una entidad por clave principal compuesta</span><span class="sxs-lookup"><span data-stu-id="4e745-130">Finding an entity by composite primary key</span></span>  

<span data-ttu-id="4e745-131">Entity Framework permite que las entidades tengan claves compuestas, que son claves que se componen de más de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="4e745-131">Entity Framework allows your entities to have composite keys - that's a key that is made up of more than one property.</span></span> <span data-ttu-id="4e745-132">Por ejemplo, podría tener una entidad BlogSettings que representara la configuración de un usuario para un blog determinado.</span><span class="sxs-lookup"><span data-stu-id="4e745-132">For example, you could have a BlogSettings entity that represents a users settings for a particular blog.</span></span> <span data-ttu-id="4e745-133">Dado que un usuario solo tendría una entidad BlogSettings para cada blog, podría optar por convertir la clave principal de BlogSettings en una combinación de BlogId y Username.</span><span class="sxs-lookup"><span data-stu-id="4e745-133">Because a user would only ever have one BlogSettings for each blog you could chose to make the primary key of BlogSettings a combination of BlogId and Username.</span></span> <span data-ttu-id="4e745-134">El código siguiente intenta encontrar la entidad BlogSettings con BlogId = 3 y Username = "johndoe1987":</span><span class="sxs-lookup"><span data-stu-id="4e745-134">The following code attempts to find the BlogSettings with BlogId = 3 and Username = "johndoe1987":</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var settings = context.BlogSettings.Find(3, "johndoe1987");
}
```  

<span data-ttu-id="4e745-135">Observe que con las claves compuestas hay que usar ColumnAttribute o la API fluida para especificar un orden de las propiedades de la clave compuesta.</span><span class="sxs-lookup"><span data-stu-id="4e745-135">Note that when you have composite keys you need to use ColumnAttribute or the fluent API to specify an ordering for the properties of the composite key.</span></span> <span data-ttu-id="4e745-136">La llamada a Find debe usar este orden al especificar los valores que forman la clave.</span><span class="sxs-lookup"><span data-stu-id="4e745-136">The call to Find must use this order when specifying the values that form the key.</span></span>  
