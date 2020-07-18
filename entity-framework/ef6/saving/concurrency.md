---
title: 'Controlar los conflictos de simultaneidad: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: 4d29fd7a4d9b6003f71bc8411cea2d863a4c5429
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451247"
---
# <a name="handling-concurrency-conflicts-ef6"></a><span data-ttu-id="45847-102">Controlar los conflictos de simultaneidad (EF6)</span><span class="sxs-lookup"><span data-stu-id="45847-102">Handling Concurrency Conflicts (EF6)</span></span>

<span data-ttu-id="45847-103">La simultaneidad optimista implica un intento optimista de guardar la entidad en la base de datos, con la esperanza de que los datos no hayan cambiado desde que se cargó la entidad.</span><span class="sxs-lookup"><span data-stu-id="45847-103">Optimistic concurrency involves optimistically attempting to save your entity to the database in the hope that the data there has not changed since the entity was loaded.</span></span> <span data-ttu-id="45847-104">Si se da cuenta de que los datos han cambiado, se produce una excepción y debe resolver el conflicto antes de intentar volver a guardar.</span><span class="sxs-lookup"><span data-stu-id="45847-104">If it turns out that the data has changed then an exception is thrown and you must resolve the conflict before attempting to save again.</span></span> <span data-ttu-id="45847-105">En este tema se explica cómo controlar dichas excepciones en Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="45847-105">This topic covers how to handle such exceptions in Entity Framework.</span></span> <span data-ttu-id="45847-106">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="45847-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="45847-107">Esta publicación no es el lugar adecuado para una descripción completa de la simultaneidad optimista.</span><span class="sxs-lookup"><span data-stu-id="45847-107">This post is not the appropriate place for a full discussion of optimistic concurrency.</span></span> <span data-ttu-id="45847-108">En las secciones siguientes se presupone cierto conocimiento de la resolución de simultaneidad y se muestran patrones para tareas comunes.</span><span class="sxs-lookup"><span data-stu-id="45847-108">The sections below assume some knowledge of concurrency resolution and show patterns for common tasks.</span></span>  

<span data-ttu-id="45847-109">Muchos de estos patrones hacen uso de los temas que se describen en [trabajar con valores de propiedad](~/ef6/saving/change-tracking/property-values.md).</span><span class="sxs-lookup"><span data-stu-id="45847-109">Many of these patterns make use of the topics discussed in [Working with Property Values](~/ef6/saving/change-tracking/property-values.md).</span></span>  

<span data-ttu-id="45847-110">La resolución de problemas de simultaneidad cuando se usan asociaciones independientes (donde la clave externa no está asignada a una propiedad de la entidad) es mucho más difícil que cuando se usan asociaciones de clave externa.</span><span class="sxs-lookup"><span data-stu-id="45847-110">Resolving concurrency issues when you are using independent associations (where the foreign key is not mapped to a property in your entity) is much more difficult than when you are using foreign key associations.</span></span> <span data-ttu-id="45847-111">Por lo tanto, si va a realizar la resolución de simultaneidad en la aplicación, se recomienda que asigne siempre las claves externas a las entidades.</span><span class="sxs-lookup"><span data-stu-id="45847-111">Therefore if you are going to do concurrency resolution in your application it is advised that you always map foreign keys into your entities.</span></span> <span data-ttu-id="45847-112">Todos los ejemplos siguientes suponen que está usando asociaciones de clave externa.</span><span class="sxs-lookup"><span data-stu-id="45847-112">All the examples below assume that you are using foreign key associations.</span></span>  

<span data-ttu-id="45847-113">SaveChanges genera una DbUpdateConcurrencyException cuando se detecta una excepción de simultaneidad optimista al intentar guardar una entidad que utiliza asociaciones de clave externa.</span><span class="sxs-lookup"><span data-stu-id="45847-113">A DbUpdateConcurrencyException is thrown by SaveChanges when an optimistic concurrency exception is detected while attempting to save an entity that uses foreign key associations.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a><span data-ttu-id="45847-114">Resolver excepciones de simultaneidad optimista con Reload (base de datos gana)</span><span class="sxs-lookup"><span data-stu-id="45847-114">Resolving optimistic concurrency exceptions with Reload (database wins)</span></span>  

<span data-ttu-id="45847-115">El método Reload se puede usar para sobrescribir los valores actuales de la entidad con los valores ahora en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="45847-115">The Reload method can be used to overwrite the current values of the entity with the values now in the database.</span></span> <span data-ttu-id="45847-116">La entidad se suele devolver al usuario de alguna forma y debe intentar realizar de nuevo los cambios y volver a guardar.</span><span class="sxs-lookup"><span data-stu-id="45847-116">The entity is then typically given back to the user in some form and they must try to make their changes again and re-save.</span></span> <span data-ttu-id="45847-117">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="45847-117">For example:</span></span>  

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

<span data-ttu-id="45847-118">Una buena forma de simular una excepción de simultaneidad es establecer un punto de interrupción en la llamada a SaveChanges y, a continuación, modificar una entidad que se guarda en la base de datos mediante otra herramienta como SQL Server Management Studio.</span><span class="sxs-lookup"><span data-stu-id="45847-118">A good way to simulate a concurrency exception is to set a breakpoint on the SaveChanges call and then modify an entity that is being saved in the database using another tool such as SQL Server Management Studio.</span></span> <span data-ttu-id="45847-119">También puede insertar una línea antes de SaveChanges para actualizar la base de datos directamente mediante SqlCommand.</span><span class="sxs-lookup"><span data-stu-id="45847-119">You could also insert a line before SaveChanges to update the database directly using SqlCommand.</span></span> <span data-ttu-id="45847-120">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="45847-120">For example:</span></span>  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

<span data-ttu-id="45847-121">El método de entradas de DbUpdateConcurrencyException devuelve las instancias de DbEntityEntry para las entidades que no se pudieron actualizar.</span><span class="sxs-lookup"><span data-stu-id="45847-121">The Entries method on DbUpdateConcurrencyException returns the DbEntityEntry instances for the entities that failed to update.</span></span> <span data-ttu-id="45847-122">(Actualmente, esta propiedad siempre devuelve un valor único para los problemas de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="45847-122">(This property currently always returns a single value for concurrency issues.</span></span> <span data-ttu-id="45847-123">Puede devolver varios valores para las excepciones de actualización generales). Una alternativa para algunas situaciones podría ser obtener entradas para todas las entidades que pueden tener que volver a cargarse desde la base de datos y llamar a recargar para cada una de ellas.</span><span class="sxs-lookup"><span data-stu-id="45847-123">It may return multiple values for general update exceptions.) An alternative for some situations might be to get entries for all entities that may need to be reloaded from the database and call reload for each of these.</span></span>  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a><span data-ttu-id="45847-124">Resolver excepciones de simultaneidad optimista como cliente WINS</span><span class="sxs-lookup"><span data-stu-id="45847-124">Resolving optimistic concurrency exceptions as client wins</span></span>  

<span data-ttu-id="45847-125">El ejemplo anterior que usa la recarga se denomina a veces Database WINS o Store WINS porque los valores de la entidad se sobrescriben con los valores de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="45847-125">The example above that uses Reload is sometimes called database wins or store wins because the values in the entity are overwritten by values from the database.</span></span> <span data-ttu-id="45847-126">En ocasiones, es posible que desee hacer lo contrario y sobrescribir los valores de la base de datos con los valores actualmente en la entidad.</span><span class="sxs-lookup"><span data-stu-id="45847-126">Sometimes you may wish to do the opposite and overwrite the values in the database with the values currently in the entity.</span></span> <span data-ttu-id="45847-127">Esto se denomina a veces cliente WINS y se puede hacer obteniendo los valores de la base de datos actual y estableciéndolo como valores originales de la entidad.</span><span class="sxs-lookup"><span data-stu-id="45847-127">This is sometimes called client wins and can be done by getting the current database values and setting them as the original values for the entity.</span></span> <span data-ttu-id="45847-128">(Vea [trabajar con valores de propiedad](~/ef6/saving/change-tracking/property-values.md) para obtener información sobre los valores actuales y originales). Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="45847-128">(See [Working with Property Values](~/ef6/saving/change-tracking/property-values.md) for information on current and original values.) For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a><span data-ttu-id="45847-129">Resolución personalizada de excepciones de simultaneidad optimista</span><span class="sxs-lookup"><span data-stu-id="45847-129">Custom resolution of optimistic concurrency exceptions</span></span>  

<span data-ttu-id="45847-130">En ocasiones, es posible que desee combinar los valores que hay actualmente en la base de datos con los valores de la entidad.</span><span class="sxs-lookup"><span data-stu-id="45847-130">Sometimes you may want to combine the values currently in the database with the values currently in the entity.</span></span> <span data-ttu-id="45847-131">Normalmente, esto requiere una lógica personalizada o una interacción del usuario.</span><span class="sxs-lookup"><span data-stu-id="45847-131">This usually requires some custom logic or user interaction.</span></span> <span data-ttu-id="45847-132">Por ejemplo, puede presentar un formulario al usuario que contiene los valores actuales, los valores de la base de datos y un conjunto predeterminado de valores resueltos.</span><span class="sxs-lookup"><span data-stu-id="45847-132">For example, you might present a form to the user containing the current values, the values in the database, and a default set of resolved values.</span></span> <span data-ttu-id="45847-133">Después, el usuario editaría los valores resueltos según sea necesario y serían estos valores resueltos que se guardan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="45847-133">The user would then edit the resolved values as necessary and it would be these resolved values that get saved to the database.</span></span> <span data-ttu-id="45847-134">Esto se puede hacer mediante los objetos DbPropertyValues devueltos desde CurrentValues y GetDatabaseValues en la entrada de la entidad.</span><span class="sxs-lookup"><span data-stu-id="45847-134">This can be done using the DbPropertyValues objects returned from CurrentValues and GetDatabaseValues on the entity’s entry.</span></span> <span data-ttu-id="45847-135">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="45847-135">For example:</span></span>  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a><span data-ttu-id="45847-136">Resolución personalizada de excepciones de simultaneidad optimista mediante objetos</span><span class="sxs-lookup"><span data-stu-id="45847-136">Custom resolution of optimistic concurrency exceptions using objects</span></span>  

<span data-ttu-id="45847-137">El código anterior usa instancias de DbPropertyValues para pasar los valores actuales, de base de datos y resueltos.</span><span class="sxs-lookup"><span data-stu-id="45847-137">The code above uses DbPropertyValues instances for passing around current, database, and resolved values.</span></span> <span data-ttu-id="45847-138">A veces puede ser más fácil usar instancias de su tipo de entidad para esto.</span><span class="sxs-lookup"><span data-stu-id="45847-138">Sometimes it may be easier to use instances of your entity type for this.</span></span> <span data-ttu-id="45847-139">Esto se puede hacer mediante los métodos ToObject y SetValues de DbPropertyValues.</span><span class="sxs-lookup"><span data-stu-id="45847-139">This can be done using the ToObject and SetValues methods of DbPropertyValues.</span></span> <span data-ttu-id="45847-140">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="45847-140">For example:</span></span>  

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
