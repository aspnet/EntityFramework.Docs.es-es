---
title: 'Controlar los conflictos de simultaneidad: EF6'
description: Controlar los conflictos de simultaneidad en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
uid: ef6/saving/concurrency
ms.openlocfilehash: 1cec47ce346e8a6c86338747c01fba4d030e7388
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619879"
---
# <a name="handling-concurrency-conflicts-ef6"></a><span data-ttu-id="72d4e-103">Controlar los conflictos de simultaneidad (EF6)</span><span class="sxs-lookup"><span data-stu-id="72d4e-103">Handling Concurrency Conflicts (EF6)</span></span>

<span data-ttu-id="72d4e-104">La simultaneidad optimista implica un intento optimista de guardar la entidad en la base de datos, con la esperanza de que los datos no hayan cambiado desde que se cargó la entidad.</span><span class="sxs-lookup"><span data-stu-id="72d4e-104">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="72d4e-105">Si se da cuenta de que los datos han cambiado, se produce una excepción y debe resolver el conflicto antes de intentar volver a guardar.</span><span class="sxs-lookup"><span data-stu-id="72d4e-105">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="72d4e-106">En este tema se explica cómo controlar dichas excepciones en Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="72d4e-106">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="72d4e-107">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="72d4e-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="72d4e-108">Esta publicación no es el lugar adecuado para una descripción completa de la simultaneidad optimista.</span><span class="sxs-lookup"><span data-stu-id="72d4e-108">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="72d4e-109">En las secciones siguientes se presupone cierto conocimiento de la resolución de simultaneidad y se muestran patrones para tareas comunes.</span><span class="sxs-lookup"><span data-stu-id="72d4e-109">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="72d4e-110">Muchos de estos patrones hacen uso de los temas que se describen en [trabajar con valores de propiedad](xref:ef6/saving/change-tracking/property-values).</span><span class="sxs-lookup"><span data-stu-id="72d4e-110">Many of these patterns make use of the topics discussed in [Working with Property Values](xref:ef6/saving/change-tracking/property-values).</span></span>  

<span data-ttu-id="72d4e-111">La resolución de problemas de simultaneidad cuando se usan asociaciones independientes (donde la clave externa no está asignada a una propiedad de la entidad) es mucho más difícil que cuando se usan asociaciones de clave externa.</span><span class="sxs-lookup"><span data-stu-id="72d4e-111">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="72d4e-112">Por lo tanto, si va a realizar la resolución de simultaneidad en la aplicación, se recomienda que asigne siempre las claves externas a las entidades.</span><span class="sxs-lookup"><span data-stu-id="72d4e-112">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="72d4e-113">Todos los ejemplos siguientes suponen que está usando asociaciones de clave externa.</span><span class="sxs-lookup"><span data-stu-id="72d4e-113">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="72d4e-114">SaveChanges genera una DbUpdateConcurrencyException cuando se detecta una excepción de simultaneidad optimista al intentar guardar una entidad que utiliza asociaciones de clave externa.</span><span class="sxs-lookup"><span data-stu-id="72d4e-114">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="72d4e-115">Resolver excepciones de simultaneidad optimista con Reload (base de datos gana)</span><span class="sxs-lookup"><span data-stu-id="72d4e-115">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="72d4e-116">El método Reload se puede usar para sobrescribir los valores actuales de la entidad con los valores ahora en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="72d4e-116">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="72d4e-117">La entidad se suele devolver al usuario de alguna forma y debe intentar realizar de nuevo los cambios y volver a guardar.</span><span class="sxs-lookup"><span data-stu-id="72d4e-117">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="72d4e-118">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="72d4e-118">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;

        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update the values of the entity that failed to save from the store
            ex.Entries.Single().Reload();
        }

    } while (saveFailed);
}
```  

<span data-ttu-id="72d4e-119">Una buena forma de simular una excepción de simultaneidad es establecer un punto de interrupción en la llamada a SaveChanges y, a continuación, modificar una entidad que se guarda en la base de datos mediante otra herramienta como SQL Server Management Studio.</span><span class="sxs-lookup"><span data-stu-id="72d4e-119">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Server Management Studio.</span></span> <span data-ttu-id="72d4e-120">También puede insertar una línea antes de SaveChanges para actualizar la base de datos directamente mediante SqlCommand.</span><span class="sxs-lookup"><span data-stu-id="72d4e-120">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="72d4e-121">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="72d4e-121">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="72d4e-122">El método de entradas de DbUpdateConcurrencyException devuelve las instancias de DbEntityEntry para las entidades que no se pudieron actualizar.</span><span class="sxs-lookup"><span data-stu-id="72d4e-122">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="72d4e-123">(Actualmente, esta propiedad siempre devuelve un valor único para los problemas de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="72d4e-123">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="72d4e-124">Puede devolver varios valores para las excepciones de actualización generales). Una alternativa para algunas situaciones podría ser obtener entradas para todas las entidades que pueden tener que volver a cargarse desde la base de datos y llamar a recargar para cada una de ellas.</span><span class="sxs-lookup"><span data-stu-id="72d4e-124">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="72d4e-125">Resolver excepciones de simultaneidad optimista como cliente WINS</span><span class="sxs-lookup"><span data-stu-id="72d4e-125">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="72d4e-126">El ejemplo anterior que usa la recarga se denomina a veces Database WINS o Store WINS porque los valores de la entidad se sobrescriben con los valores de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="72d4e-126">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="72d4e-127">En ocasiones, es posible que desee hacer lo contrario y sobrescribir los valores de la base de datos con los valores actualmente en la entidad.</span><span class="sxs-lookup"><span data-stu-id="72d4e-127">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="72d4e-128">Esto se denomina a veces cliente WINS y se puede hacer obteniendo los valores de la base de datos actual y estableciéndolo como valores originales de la entidad.</span><span class="sxs-lookup"><span data-stu-id="72d4e-128">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="72d4e-129">(Vea [trabajar con valores de propiedad](xref:ef6/saving/change-tracking/property-values) para obtener información sobre los valores actuales y originales). Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="72d4e-129">(See [Working with Property Values](xref:ef6/saving/change-tracking/property-values) for information on current and original values.) For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update original values from the database
            var entry = ex.Entries.Single();
            entry.OriginalValues.SetValues(entry.GetDatabaseValues());
        }

    } while (saveFailed);
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="72d4e-130">Resolución personalizada de excepciones de simultaneidad optimista</span><span class="sxs-lookup"><span data-stu-id="72d4e-130">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="72d4e-131">En ocasiones, es posible que desee combinar los valores que hay actualmente en la base de datos con los valores de la entidad.</span><span class="sxs-lookup"><span data-stu-id="72d4e-131">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="72d4e-132">Normalmente, esto requiere una lógica personalizada o una interacción del usuario.</span><span class="sxs-lookup"><span data-stu-id="72d4e-132">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="72d4e-133">Por ejemplo, puede presentar un formulario al usuario que contiene los valores actuales, los valores de la base de datos y un conjunto predeterminado de valores resueltos.</span><span class="sxs-lookup"><span data-stu-id="72d4e-133">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="72d4e-134">Después, el usuario editaría los valores resueltos según sea necesario y serían estos valores resueltos que se guardan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="72d4e-134">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="72d4e-135">Esto se puede hacer mediante los objetos DbPropertyValues devueltos desde CurrentValues y GetDatabaseValues en la entrada de la entidad.</span><span class="sxs-lookup"><span data-stu-id="72d4e-135">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="72d4e-136">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="72d4e-136">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            var entry = ex.Entries.Single();
            var currentValues = entry.CurrentValues;
            var databaseValues = entry.GetDatabaseValues();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValues = databaseValues.Clone();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency(currentValues, databaseValues, resolvedValues);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValues);
        }
    } while (saveFailed);
}

public void HaveUserResolveConcurrency(DbPropertyValues currentValues,
                                       DbPropertyValues databaseValues,
                                       DbPropertyValues resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them edit the resolved values to get the correct resolution.
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="72d4e-137">Resolución personalizada de excepciones de simultaneidad optimista mediante objetos</span><span class="sxs-lookup"><span data-stu-id="72d4e-137">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="72d4e-138">El código anterior usa instancias de DbPropertyValues para pasar los valores actuales, de base de datos y resueltos.</span><span class="sxs-lookup"><span data-stu-id="72d4e-138">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="72d4e-139">A veces puede ser más fácil usar instancias de su tipo de entidad para esto.</span><span class="sxs-lookup"><span data-stu-id="72d4e-139">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="72d4e-140">Esto se puede hacer mediante los métodos ToObject y SetValues de DbPropertyValues.</span><span class="sxs-lookup"><span data-stu-id="72d4e-140">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="72d4e-141">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="72d4e-141">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            // as instances of the entity type
            var entry = ex.Entries.Single();
            var databaseValues = entry.GetDatabaseValues();
            var databaseValuesAsBlog = (Blog)databaseValues.ToObject();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValuesAsBlog = (Blog)databaseValues.ToObject();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency((Blog)entry.Entity,
                                       databaseValuesAsBlog,
                                       resolvedValuesAsBlog);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValuesAsBlog);
        }

    } while (saveFailed);
}

public void HaveUserResolveConcurrency(Blog entity,
                                       Blog databaseValues,
                                       Blog resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them update the resolved values to get the correct resolution.
}
```  
