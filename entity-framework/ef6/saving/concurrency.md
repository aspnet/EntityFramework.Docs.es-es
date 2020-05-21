---
title: 'Controlar los conflictos de simultaneidad: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: a99f824fe256a10b84f539a5339a09624315efa4
ms.sourcegitcommit: 59e3d5ce7dfb284457cf1c991091683b2d1afe9d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/20/2020
ms.locfileid: "83672711"
---
# <a name="handling-concurrency-conflicts"></a>Administrar los conflictos de simultaneidad
La simultaneidad optimista implica un intento optimista de guardar la entidad en la base de datos, con la esperanza de que los datos no hayan cambiado desde que se cargó la entidad. Si se da cuenta de que los datos han cambiado, se produce una excepción y debe resolver el conflicto antes de intentar volver a guardar. En este tema se explica cómo controlar dichas excepciones en Entity Framework. Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.  

Esta publicación no es el lugar adecuado para una descripción completa de la simultaneidad optimista. En las secciones siguientes se presupone cierto conocimiento de la resolución de simultaneidad y se muestran patrones para tareas comunes.  

Muchos de estos patrones hacen uso de los temas que se describen en [trabajar con valores de propiedad](~/ef6/saving/change-tracking/property-values.md).  

La resolución de problemas de simultaneidad cuando se usan asociaciones independientes (donde la clave externa no está asignada a una propiedad de la entidad) es mucho más difícil que cuando se usan asociaciones de clave externa. Por lo tanto, si va a realizar la resolución de simultaneidad en la aplicación, se recomienda que asigne siempre las claves externas a las entidades. Todos los ejemplos siguientes suponen que está usando asociaciones de clave externa.  

SaveChanges genera una DbUpdateConcurrencyException cuando se detecta una excepción de simultaneidad optimista al intentar guardar una entidad que utiliza asociaciones de clave externa.  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a>Resolver excepciones de simultaneidad optimista con Reload (base de datos gana)  

El método Reload se puede usar para sobrescribir los valores actuales de la entidad con los valores ahora en la base de datos. La entidad se suele devolver al usuario de alguna forma y debe intentar realizar de nuevo los cambios y volver a guardar. Por ejemplo:  

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

Una buena forma de simular una excepción de simultaneidad es establecer un punto de interrupción en la llamada a SaveChanges y, a continuación, modificar una entidad que se guarda en la base de datos mediante otra herramienta como SQL Server Management Studio. También puede insertar una línea antes de SaveChanges para actualizar la base de datos directamente mediante SqlCommand. Por ejemplo:  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

El método de entradas de DbUpdateConcurrencyException devuelve las instancias de DbEntityEntry para las entidades que no se pudieron actualizar. (Actualmente, esta propiedad siempre devuelve un valor único para los problemas de simultaneidad. Puede devolver varios valores para las excepciones de actualización generales). Una alternativa para algunas situaciones podría ser obtener entradas para todas las entidades que pueden tener que volver a cargarse desde la base de datos y llamar a recargar para cada una de ellas.  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a>Resolver excepciones de simultaneidad optimista como cliente WINS  

El ejemplo anterior que usa la recarga se denomina a veces Database WINS o Store WINS porque los valores de la entidad se sobrescriben con los valores de la base de datos. En ocasiones, es posible que desee hacer lo contrario y sobrescribir los valores de la base de datos con los valores actualmente en la entidad. Esto se denomina a veces cliente WINS y se puede hacer obteniendo los valores de la base de datos actual y estableciéndolo como valores originales de la entidad. (Vea [trabajar con valores de propiedad](~/ef6/saving/change-tracking/property-values.md) para obtener información sobre los valores actuales y originales). Por ejemplo:  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a>Resolución personalizada de excepciones de simultaneidad optimista  

En ocasiones, es posible que desee combinar los valores que hay actualmente en la base de datos con los valores de la entidad. Normalmente, esto requiere una lógica personalizada o una interacción del usuario. Por ejemplo, puede presentar un formulario al usuario que contiene los valores actuales, los valores de la base de datos y un conjunto predeterminado de valores resueltos. Después, el usuario editaría los valores resueltos según sea necesario y serían estos valores resueltos que se guardan en la base de datos. Esto se puede hacer mediante los objetos DbPropertyValues devueltos desde CurrentValues y GetDatabaseValues en la entrada de la entidad. Por ejemplo:  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a>Resolución personalizada de excepciones de simultaneidad optimista mediante objetos  

El código anterior usa instancias de DbPropertyValues para pasar los valores actuales, de base de datos y resueltos. A veces puede ser más fácil usar instancias de su tipo de entidad para esto. Esto se puede hacer mediante los métodos ToObject y SetValues de DbPropertyValues. Por ejemplo:  

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
