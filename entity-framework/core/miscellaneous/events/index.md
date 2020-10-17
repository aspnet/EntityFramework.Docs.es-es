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
# <a name="overview-of-logging-and-interception"></a>Información general sobre el registro y la interceptación

Entity Framework Core (EF Core) contiene varios mecanismos para generar registros, responder a eventos y obtener diagnósticos. Cada uno de ellos se adapta a diferentes situaciones, y es importante seleccionar el mecanismo adecuado para cada tarea, incluso cuando puedan funcionar varios. Por ejemplo, un interceptor de base de datos se podría usar para registrar SQL, pero sería más adecuado usar en este caso alguno de los mecanismos específicos para registro. En esta página, se presenta información general de cada uno de estos mecanismos y se describe en qué casos se debe usar cada uno de ellos.

## <a name="simple-logging"></a>Registro sencillo

> [!NOTE]
> Esta característica se agregó en EF Core 5.0.

Se puede acceder a los registros de EF Core desde cualquier tipo de aplicación mediante el uso de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> al [configurar una instancia de DbContext](xref:core/miscellaneous/configuring-dbcontext). Esta configuración se realiza normalmente en una invalidación de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>. Por ejemplo:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Este concepto es similar a <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> en EF6.

Vea [Registro simple](xref:core/miscellaneous/events/simple-logging) para más información.
