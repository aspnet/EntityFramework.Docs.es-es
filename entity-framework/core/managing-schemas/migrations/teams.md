---
title: 'Migraciones en entornos de equipo: EF Core'
description: Prácticas recomendadas para administrar migraciones y resolver conflictos en entornos de equipo con Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 1fbb7173a52218a4d00780ebc76e33600f3558c1
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619194"
---
# <a name="migrations-in-team-environments"></a><span data-ttu-id="8c641-103">Migraciones en entornos de equipo</span><span class="sxs-lookup"><span data-stu-id="8c641-103">Migrations in Team Environments</span></span>

<span data-ttu-id="8c641-104">Al trabajar con migraciones en entornos de equipo, preste especial atención al archivo de instantáneas del modelo.</span><span class="sxs-lookup"><span data-stu-id="8c641-104">When working with Migrations in team environments, pay extra attention to the model snapshot file.</span></span> <span data-ttu-id="8c641-105">Este archivo puede indicarle si la migración de su compañero de equipo se combina correctamente con la suya o si necesita resolver un conflicto volviendo a crear la migración antes de compartirla.</span><span class="sxs-lookup"><span data-stu-id="8c641-105">This file can tell you if your teammate's migration merges cleanly with yours or if you need to resolve a conflict by re-creating your migration before sharing it.</span></span>

## <a name="merging"></a><span data-ttu-id="8c641-106">Combinación</span><span class="sxs-lookup"><span data-stu-id="8c641-106">Merging</span></span>

<span data-ttu-id="8c641-107">Al fusionar mediante combinación las migraciones de sus compañeros de equipo, puede obtener conflictos en el archivo de instantánea del modelo.</span><span class="sxs-lookup"><span data-stu-id="8c641-107">When you merge migrations from your teammates, you may get conflicts in your model snapshot file.</span></span> <span data-ttu-id="8c641-108">Si los dos cambios no están relacionados, la combinación es trivial y las dos migraciones pueden coexistir.</span><span class="sxs-lookup"><span data-stu-id="8c641-108">If both changes are unrelated, the merge is trivial and the two migrations can coexist.</span></span> <span data-ttu-id="8c641-109">Por ejemplo, puede obtener un conflicto de fusión mediante combinación en la configuración del tipo de entidad Customer, que tiene el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="8c641-109">For example, you may get a merge conflict in the customer entity type configuration that looks like this:</span></span>

``` output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

<span data-ttu-id="8c641-110">Puesto que ambas propiedades deben existir en el modelo final, complete la combinación agregando ambas propiedades.</span><span class="sxs-lookup"><span data-stu-id="8c641-110">Since both of these properties need to exist in the final model, complete the merge by adding both properties.</span></span> <span data-ttu-id="8c641-111">En muchos casos, es posible que el sistema de control de versiones combine automáticamente estos cambios.</span><span class="sxs-lookup"><span data-stu-id="8c641-111">In many cases, your version control system may automatically merge such changes for you.</span></span>

``` csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

<span data-ttu-id="8c641-112">En estos casos, la migración y la migración de su compañero son independientes entre sí.</span><span class="sxs-lookup"><span data-stu-id="8c641-112">In these cases, your migration and your teammate's migration are independent of each other.</span></span> <span data-ttu-id="8c641-113">Dado que cualquiera de ellas se podría aplicar en primer lugar, no es necesario realizar ningún cambio adicional en la migración antes de compartirla con el equipo.</span><span class="sxs-lookup"><span data-stu-id="8c641-113">Since either of them could be applied first, you don't need to make any additional changes to your migration before sharing it with your team.</span></span>

## <a name="resolving-conflicts"></a><span data-ttu-id="8c641-114">Resolución de conflictos</span><span class="sxs-lookup"><span data-stu-id="8c641-114">Resolving conflicts</span></span>

<span data-ttu-id="8c641-115">A veces se produce un conflicto real al combinar el modelo de instantánea de modelo.</span><span class="sxs-lookup"><span data-stu-id="8c641-115">Sometimes you encounter a true conflict when merging the model snapshot model.</span></span> <span data-ttu-id="8c641-116">Por ejemplo, usted y su compañero de equipo pueden cambiar el nombre de la misma propiedad.</span><span class="sxs-lookup"><span data-stu-id="8c641-116">For example, you and your teammate may each have renamed the same property.</span></span>

``` output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

<span data-ttu-id="8c641-117">Si encuentra este tipo de conflicto, resuélvalos volviendo a crear la migración.</span><span class="sxs-lookup"><span data-stu-id="8c641-117">If you encounter this kind of conflict, resolve it by re-creating your migration.</span></span> <span data-ttu-id="8c641-118">Siga estos pasos:</span><span class="sxs-lookup"><span data-stu-id="8c641-118">Follow these steps:</span></span>

1. <span data-ttu-id="8c641-119">Anular la combinación y revertir al directorio de trabajo antes de la fusión mediante combinación</span><span class="sxs-lookup"><span data-stu-id="8c641-119">Abort the merge and rollback to your working directory before the merge</span></span>
2. <span data-ttu-id="8c641-120">Quitar la migración (pero mantener los cambios del modelo)</span><span class="sxs-lookup"><span data-stu-id="8c641-120">Remove your migration (but keep your model changes)</span></span>
3. <span data-ttu-id="8c641-121">Combinar los cambios de su compañero en el directorio de trabajo</span><span class="sxs-lookup"><span data-stu-id="8c641-121">Merge your teammate's changes into your working directory</span></span>
4. <span data-ttu-id="8c641-122">Volver a agregar la migración</span><span class="sxs-lookup"><span data-stu-id="8c641-122">Re-add your migration</span></span>

<span data-ttu-id="8c641-123">Después de hacer esto, las dos migraciones se pueden aplicar en el orden correcto.</span><span class="sxs-lookup"><span data-stu-id="8c641-123">After doing this, the two migrations can be applied in the correct order.</span></span> <span data-ttu-id="8c641-124">En primer lugar, se aplica su migración, cambiando el nombre de la columna a *alias*y, a partir de ese momento, la migración lo cambia por nombre de *usuario*.</span><span class="sxs-lookup"><span data-stu-id="8c641-124">Their migration is applied first, renaming the column to *Alias*, thereafter your migration renames it to *Username*.</span></span>

<span data-ttu-id="8c641-125">La migración puede compartirse de forma segura con el resto del equipo.</span><span class="sxs-lookup"><span data-stu-id="8c641-125">Your migration can safely be shared with the rest of the team.</span></span>
