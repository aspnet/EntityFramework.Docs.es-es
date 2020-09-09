---
title: Servicios en tiempo de diseño-EF Core
description: Información sobre los servicios en tiempo de diseño de Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: aa761c4a20e0848a77aa7b4ad625124a1d372a70
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617849"
---
# <a name="design-time-services"></a><span data-ttu-id="e852c-103">Servicios en tiempo de diseño</span><span class="sxs-lookup"><span data-stu-id="e852c-103">Design-time services</span></span>

<span data-ttu-id="e852c-104">Algunos servicios usados por las herramientas solo se usan en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="e852c-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="e852c-105">Estos servicios se administran de forma independiente de los servicios en tiempo de ejecución de EF Core para evitar que se implementen con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="e852c-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="e852c-106">Para invalidar uno de estos servicios (por ejemplo, el servicio para generar archivos de migración), agregue una implementación de `IDesignTimeServices` al proyecto de inicio.</span><span class="sxs-lookup"><span data-stu-id="e852c-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
