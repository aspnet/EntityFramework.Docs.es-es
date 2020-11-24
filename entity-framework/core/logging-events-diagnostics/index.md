---
title: 'Información general sobre el registro y la interceptación: EF Core'
description: Información general sobre el registro, los eventos, los interceptores y el diagnóstico de EF Core
author: ajcvickers
ms.date: 10/01/2020
uid: core/logging-events-diagnostics/index
ms.openlocfilehash: 2c44772b22112645f85cf0bffa680bc510ea5afb
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003528"
---
# <a name="overview-of-logging-and-interception"></a>Información general sobre el registro y la interceptación

Entity Framework Core (EF Core) contiene varios mecanismos para generar registros, responder a eventos y obtener diagnósticos. Cada uno de ellos se adapta a diferentes situaciones, y es importante seleccionar el mecanismo adecuado para cada tarea, incluso cuando puedan funcionar varios. Por ejemplo, un interceptor de base de datos se podría usar para registrar SQL, pero sería más adecuado usar en este caso alguno de los mecanismos adaptados al registro. En esta página, se presenta información general de cada uno de estos mecanismos y se describe en qué casos se debe usar cada uno de ellos.

## <a name="quick-reference"></a>Referencia rápida

En la siguiente tabla se proporciona una referencia rápida para las diferencias entre los mecanismos descritos aquí.

| Mechanism |  Async | Ámbito | Registrado | Uso previsto
|:----------|--------|-------|------------|-------------
| Registro sencillo | No | Por contexto | Configuración en contexto | Registro del tiempo de desarrollo
| Microsoft.Extensions.Logging | No | Por contexto* | D.I. o configuración en contexto | Registro de producción
| Eventos | No | Por contexto | Cualquier momento | Reacción a eventos de EF
| Interceptores | Sí | Por contexto | Configuración en contexto | Manipulación de operaciones EF
| Escuchas de diagnóstico | No | Proceso | Globalmente | Diagnósticos de aplicaciones

*Normalmente `Microsoft.Extensions.Logging` se configura por aplicación a través de la inserción de dependencias. Sin embargo, en el nivel de EF, cada contexto se _puede_ configurar con un registrador diferente en caso necesario.

## <a name="simple-logging"></a>Registro sencillo

> [!NOTE]
> Esta característica se incluyó por primera vez en EF Core 5.0.

Se puede acceder a los registros de EF Core desde cualquier tipo de aplicación mediante el uso de [LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> al [configurar una instancia de DbContext](xref:core/dbcontext-configuration/index). Esta configuración se realiza normalmente en una invalidación de <xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType>. Por ejemplo:

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

Este concepto es similar a <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> en EF6.

Vea [Registro simple](xref:core/logging-events-diagnostics/simple-logging) para más información.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) es un mecanismo de registro extensible con proveedores de complementos para muchos sistemas de registro comunes. EF Core se integra totalmente con `Microsoft.Extensions.Logging` y esta forma de registro se usa de forma predeterminada para las aplicaciones ASP.NET Core.

Consulte [Usar Microsoft.Extensions.Logging en EF Core](xref:core/logging-events-diagnostics/extensions-logging) para obtener más información.

## <a name="events"></a>Eventos

> [!NOTE]
> Se incluyeron eventos adicionales en EF Core 5.0.

EF Core expone [eventos de .NET](/dotnet/standard/events/) para que actúen como devoluciones de llamada cuando ocurran ciertas cosas en el código EF Core. Los eventos son más sencillos que los interceptores y permiten un registro más flexible. Sin embargo, solo son sincrónicos y, por tanto, no pueden realizar operaciones de E/S asincrónicas sin bloqueo.

Los eventos se registran por instancia de DbContext y este registro se puede realizar en cualquier momento. Use una [escucha de diagnóstico](xref:core/logging-events-diagnostics/diagnostic-listeners) para obtener la misma información, pero para todas las instancias de DbContext del proceso.

Consulte [Eventos de .NET en EF Core](xref:core/logging-events-diagnostics/events) para obtener más información.

## <a name="interception"></a>Interception

> [!NOTE]
> Esta característica se incluyó por primera vez en EF Core 3.0. Los interceptores adicionales se incluyeron por primera vez en EF Core 5.0.

Los interceptores de EF Core habilitan la intercepción, la modificación o la supresión de operaciones de EF Core. Esto incluye operaciones de base de datos de bajo nivel tales como ejecutar un comando, así como operaciones de nivel superior tales como llamadas a SaveChanges.

Los interceptores son distintos del registro y el diagnóstico en que permiten la modificación o supresión de la operación que se intercepta. El [registro sencillo](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) son mejores opciones de registro.

Los interceptores se registran por instancia de DbContext al configurarse el contexto. Use una [escucha de diagnóstico](xref:core/logging-events-diagnostics/diagnostic-listeners) para obtener la misma información, pero para todas las instancias de DbContext del proceso.

Consulte [Interceptación](xref:core/logging-events-diagnostics/interceptors) para obtener más información.

## <a name="diagnostic-listeners"></a>Escuchas de diagnóstico

Las escuchas de diagnóstico permiten escuchar cualquier evento de EF Core que se produzca en el proceso de .NET actual.

Las escuchas de diagnóstico no son adecuadas para obtener eventos de una sola instancia de DbContext. Los interceptores de EF Core proporcionan acceso a los mismos eventos con el registro por contexto.

Las escuchas de diagnóstico no están diseñadas para el registro. El [registro sencillo](xref:core/logging-events-diagnostics/simple-logging) o [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) son mejores opciones de registro.

Consulte el artículo sobre cómo [Usar escuchas de diagnóstico en EF Core](xref:core/logging-events-diagnostics/diagnostic-listeners) para obtener más información.
