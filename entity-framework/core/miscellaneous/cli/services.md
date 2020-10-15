---
title: Servicios en tiempo de diseño-EF Core
description: Información sobre los servicios en tiempo de diseño de Entity Framework Core
author: bricelam
ms.date: 10/26/2017
uid: core/miscellaneous/cli/services
ms.openlocfilehash: 6778a1fba025e9fbce1bb3e43462b0ee7c5dcfc4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061949"
---
# <a name="design-time-services"></a><span data-ttu-id="0d7bc-103">Servicios en tiempo de diseño</span><span class="sxs-lookup"><span data-stu-id="0d7bc-103">Design-time services</span></span>

<span data-ttu-id="0d7bc-104">Algunos servicios usados por las herramientas solo se usan en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="0d7bc-104">Some services used by the tools are only used at design time.</span></span> <span data-ttu-id="0d7bc-105">Estos servicios se administran de forma independiente de los servicios en tiempo de ejecución de EF Core para evitar que se implementen con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="0d7bc-105">These services are managed separately from EF Core's runtime services to prevent them from being deployed with your app.</span></span> <span data-ttu-id="0d7bc-106">Para invalidar uno de estos servicios (por ejemplo, el servicio para generar archivos de migración), agregue una implementación de `IDesignTimeServices` al proyecto de inicio.</span><span class="sxs-lookup"><span data-stu-id="0d7bc-106">To override one of these services (for example the service to generate migration files), add an implementation of `IDesignTimeServices` to your startup project.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs)]
