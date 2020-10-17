---
title: 'Información general sobre el registro y la interceptación: EF Core'
description: Información general sobre el registro, los eventos, los interceptores y el diagnóstico de EF Core
author: ajcvickers
ms.date: 10/01/2020
uid: core/miscellaneous/events/index
ms.openlocfilehash: 8c46b4efb205c60be51529a89cd9dd8fb5268156
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066501"
---
# <a name="overview-of-logging-and-interception"></a><span data-ttu-id="980c0-103">Información general sobre el registro y la interceptación</span><span class="sxs-lookup"><span data-stu-id="980c0-103">Overview of logging and interception</span></span>

<span data-ttu-id="980c0-104">Entity Framework Core (EF Core) contiene varios mecanismos para generar registros, responder a eventos y obtener diagnósticos.</span><span class="sxs-lookup"><span data-stu-id="980c0-104">Entity Framework Core (EF Core) contains several mechanisms for generating logs, responding to events, and obtaining diagnostics.</span></span> <span data-ttu-id="980c0-105">Cada uno de ellos se adapta a diferentes situaciones, y es importante seleccionar el mecanismo adecuado para cada tarea, incluso cuando puedan funcionar varios.</span><span class="sxs-lookup"><span data-stu-id="980c0-105">Each of these are tailored to different situations, and it is important to select the best mechanism for the task in hand, even when multiple mechanisms could work.</span></span> <span data-ttu-id="980c0-106">Por ejemplo, un interceptor de base de datos se podría usar para registrar SQL, pero sería más adecuado usar en este caso alguno de los mecanismos específicos para registro.</span><span class="sxs-lookup"><span data-stu-id="980c0-106">For example, a database interceptor could be used to log SQL, but this is better handled by one of the logging-specific mechanisms.</span></span> <span data-ttu-id="980c0-107">En esta página, se presenta información general de cada uno de estos mecanismos y se describe en qué casos se debe usar cada uno de ellos.</span><span class="sxs-lookup"><span data-stu-id="980c0-107">This page presents an overview of each of these mechanisms and describes when each should be used.</span></span>

## <a name="simple-logging"></a><span data-ttu-id="980c0-108">Registro sencillo</span><span class="sxs-lookup"><span data-stu-id="980c0-108">Simple logging</span></span>

> [!NOTE]
> <span data-ttu-id="980c0-109">Esta característica se agregó en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="980c0-109">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="980c0-110">Se puede acceder a los registros de EF Core desde cualquier tipo de aplicación mediante el uso de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span><span class="sxs-lookup"><span data-stu-id="980c0-110">EF Core logs can be accessed from any type of application through use of [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135)</span></span> <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> <span data-ttu-id="980c0-111">al [configurar una instancia de DbContext](xref:core/miscellaneous/configuring-dbcontext).</span><span class="sxs-lookup"><span data-stu-id="980c0-111">when [configuring a DbContext instance](xref:core/miscellaneous/configuring-dbcontext).</span></span> <span data-ttu-id="980c0-112">Esta configuración se realiza normalmente en una invalidación de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span><span class="sxs-lookup"><span data-stu-id="980c0-112">This configuration is commonly done in an override of <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="980c0-113">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="980c0-113">For example:</span></span>

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

<span data-ttu-id="980c0-114">Este concepto es similar a <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> en EF6.</span><span class="sxs-lookup"><span data-stu-id="980c0-114">This concept is similar to <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> in EF6.</span></span>

<span data-ttu-id="980c0-115">Vea [Registro simple](xref:core/miscellaneous/events/simple-logging) para más información.</span><span class="sxs-lookup"><span data-stu-id="980c0-115">See [Simple Logging](xref:core/miscellaneous/events/simple-logging) for more information.</span></span>
