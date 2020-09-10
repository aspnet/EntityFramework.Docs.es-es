---
title: 'Detección automática de cambios: EF6'
description: Detección automática de cambios en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: a8d1488d-9a54-4623-a76b-e81329ff2756
uid: ef6/saving/change-tracking/auto-detect-changes
ms.openlocfilehash: 938af809ac89d3490da9885497fc5601453e1c34
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619989"
---
# <a name="automatic-detect-changes"></a><span data-ttu-id="8dae0-103">Detección automática de cambios</span><span class="sxs-lookup"><span data-stu-id="8dae0-103">Automatic detect changes</span></span>
<span data-ttu-id="8dae0-104">Al usar la mayoría de las entidades POCO, la determinación de cómo ha cambiado una entidad (y, por lo tanto, las actualizaciones que se deben enviar a la base de datos) se controla mediante el algoritmo de detección de cambios.</span><span class="sxs-lookup"><span data-stu-id="8dae0-104">When using most POCO entities the determination of how an entity has changed (and therefore which updates need to be sent to the database) is handled by the Detect Changes algorithm.</span></span> <span data-ttu-id="8dae0-105">Detectar cambios funciona detectando las diferencias entre los valores de propiedad actuales de la entidad y los valores de propiedad originales que se almacenan en una instantánea cuando se consulta o se adjunta la entidad.</span><span class="sxs-lookup"><span data-stu-id="8dae0-105">Detect Changes works by detecting the differences between the current property values of the entity and the original property values that are stored in a snapshot when the entity was queried or attached.</span></span> <span data-ttu-id="8dae0-106">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="8dae0-106">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="8dae0-107">De forma predeterminada, Entity Framework realiza la detección automática de cambios cuando se llama a los métodos siguientes:</span><span class="sxs-lookup"><span data-stu-id="8dae0-107">By default, Entity Framework performs Detect Changes automatically when the following methods are called:</span></span>  

- <span data-ttu-id="8dae0-108">DbSet.Find</span><span class="sxs-lookup"><span data-stu-id="8dae0-108">DbSet.Find</span></span>  
- <span data-ttu-id="8dae0-109">DbSet. local</span><span class="sxs-lookup"><span data-stu-id="8dae0-109">DbSet.Local</span></span>  
- <span data-ttu-id="8dae0-110">DbSet. Add</span><span class="sxs-lookup"><span data-stu-id="8dae0-110">DbSet.Add</span></span>  
- <span data-ttu-id="8dae0-111">DbSet. AddRange</span><span class="sxs-lookup"><span data-stu-id="8dae0-111">DbSet.AddRange</span></span>
- <span data-ttu-id="8dae0-112">DbSet. Remove</span><span class="sxs-lookup"><span data-stu-id="8dae0-112">DbSet.Remove</span></span>  
- <span data-ttu-id="8dae0-113">DbSet. RemoveRange</span><span class="sxs-lookup"><span data-stu-id="8dae0-113">DbSet.RemoveRange</span></span>
- <span data-ttu-id="8dae0-114">DbSet. Attach</span><span class="sxs-lookup"><span data-stu-id="8dae0-114">DbSet.Attach</span></span>  
- <span data-ttu-id="8dae0-115">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="8dae0-115">DbContext.SaveChanges</span></span>  
- <span data-ttu-id="8dae0-116">DbContext. GetValidationErrors</span><span class="sxs-lookup"><span data-stu-id="8dae0-116">DbContext.GetValidationErrors</span></span>  
- <span data-ttu-id="8dae0-117">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="8dae0-117">DbContext.Entry</span></span>  
- <span data-ttu-id="8dae0-118">DbChangeTracker. entradas</span><span class="sxs-lookup"><span data-stu-id="8dae0-118">DbChangeTracker.Entries</span></span>  

## <a name="disabling-automatic-detection-of-changes"></a><span data-ttu-id="8dae0-119">Deshabilitación de la detección automática de cambios</span><span class="sxs-lookup"><span data-stu-id="8dae0-119">Disabling automatic detection of changes</span></span>  

<span data-ttu-id="8dae0-120">Si está realizando un seguimiento de muchas entidades en el contexto y llama a uno de estos métodos muchas veces en un bucle, puede obtener mejoras de rendimiento significativas desactivando la detección de cambios durante el bucle.</span><span class="sxs-lookup"><span data-stu-id="8dae0-120">If you are tracking a lot of entities in your context and you call one of these methods many times in a loop, then you may get significant performance improvements by turning off detection of changes for the duration of the loop.</span></span> <span data-ttu-id="8dae0-121">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="8dae0-121">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    try
    {
        context.Configuration.AutoDetectChangesEnabled = false;

        // Make many calls in a loop
        foreach (var blog in aLotOfBlogs)
        {
            context.Blogs.Add(blog);
        }
    }
    finally
    {
        context.Configuration.AutoDetectChangesEnabled = true;
    }
}
```  

<span data-ttu-id="8dae0-122">No olvide volver a habilitar la detección de cambios después del bucle: hemos usado try/finally para asegurarse de que siempre se vuelve a habilitar aunque el código del bucle produzca una excepción.</span><span class="sxs-lookup"><span data-stu-id="8dae0-122">Don’t forget to re-enable detection of changes after the loop — We've used a try/finally to ensure it is always re-enabled even if code in the loop throws an exception.</span></span>  

<span data-ttu-id="8dae0-123">Una alternativa a deshabilitar y volver a habilitar consiste en dejar la detección automática de los cambios desactivados en todo momento y en el contexto de llamada. ChangeTracker. DetectChanges explícitamente o usar los proxies de seguimiento de cambios diligentemente.</span><span class="sxs-lookup"><span data-stu-id="8dae0-123">An alternative to disabling and re-enabling is to leave automatic detection of changes turned off at all times and either call context.ChangeTracker.DetectChanges explicitly or use change tracking proxies diligently.</span></span> <span data-ttu-id="8dae0-124">Ambas opciones están avanzadas y pueden introducir fácilmente errores sutiles en la aplicación, por lo que deben usarse con cuidado.</span><span class="sxs-lookup"><span data-stu-id="8dae0-124">Both of these options are advanced and can easily introduce subtle bugs into your application so use them with care.</span></span>  

<span data-ttu-id="8dae0-125">Si necesita agregar o quitar muchos objetos de un contexto, considere la posibilidad de usar DbSet. AddRange y DbSet. RemoveRange.</span><span class="sxs-lookup"><span data-stu-id="8dae0-125">If you need to add or remove many objects from a context, consider using DbSet.AddRange and DbSet.RemoveRange.</span></span> <span data-ttu-id="8dae0-126">Estos métodos detectan automáticamente los cambios solo una vez después de que se completen las operaciones de agregar o quitar.</span><span class="sxs-lookup"><span data-stu-id="8dae0-126">This methods automatically detect changes only once after the add or remove operations are completed.</span></span> 
