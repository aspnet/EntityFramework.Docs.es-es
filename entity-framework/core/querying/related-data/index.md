---
title: 'Carga de datos relacionados: EF Core'
description: Diferentes estrategias para cargar datos relacionados con Entity Framework Core
author: roji
ms.date: 9/11/2020
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 5c1addac6735de39d5c72f447d5d06280411a21e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078856"
---
# <a name="loading-related-data"></a><span data-ttu-id="ee7a8-103">Carga de datos relacionados</span><span class="sxs-lookup"><span data-stu-id="ee7a8-103">Loading Related Data</span></span>

<span data-ttu-id="ee7a8-104">Entity Framework Core permite usar las propiedades de navegación del modelo para cargar las entidades relacionados.</span><span class="sxs-lookup"><span data-stu-id="ee7a8-104">Entity Framework Core allows you to use the navigation properties in your model to load related entities.</span></span> <span data-ttu-id="ee7a8-105">Existen tres patrones de O/RM comunes que se usan para cargar los datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="ee7a8-105">There are three common O/RM patterns used to load related data.</span></span>

* <span data-ttu-id="ee7a8-106">**[Carga diligente](xref:core/querying/related-data/eager)** significa que los datos relacionados se cargan desde la base de datos como parte de la consulta inicial.</span><span class="sxs-lookup"><span data-stu-id="ee7a8-106">**[Eager loading](xref:core/querying/related-data/eager)** means that the related data is loaded from the database as part of the initial query.</span></span>
* <span data-ttu-id="ee7a8-107">**[Carga explícita](xref:core/querying/related-data/explicit)** significa que los datos relacionados se cargan de manera explícita desde la base de datos más adelante.</span><span class="sxs-lookup"><span data-stu-id="ee7a8-107">**[Explicit loading](xref:core/querying/related-data/explicit)** means that the related data is explicitly loaded from the database at a later time.</span></span>
* <span data-ttu-id="ee7a8-108">**[Carga diferida](xref:core/querying/related-data/lazy)** significa que los datos relacionados se cargan de manera transparente desde la base de datos cuando se accede a la propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="ee7a8-108">**[Lazy loading](xref:core/querying/related-data/lazy)** means that the related data is transparently loaded from the database when the navigation property is accessed.</span></span>

> [!TIP]
> <span data-ttu-id="ee7a8-109">Puede ver los [ejemplos](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) que hay en esta sección en GitHub.</span><span class="sxs-lookup"><span data-stu-id="ee7a8-109">You can view the [samples](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) under this section on GitHub.</span></span>
