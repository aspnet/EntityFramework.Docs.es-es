---
title: Versiones y planeamiento de EF Core
description: Versiones actuales de EF Core y detalles de programación o planeación para próximas versiones
author: ajcvickers
ms.date: 10/11/2020
uid: core/what-is-new/index
ms.openlocfilehash: 983a8b21d353481df4f1db232d00761e070d893b
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503149"
---
# <a name="ef-core-releases-and-planning"></a>Versiones y planeamiento de EF Core

## <a name="stable-releases"></a>Versiones estables

| Release | Marco de trabajo de destino | Compatibilidad hasta | Vínculos
|:--------|------------------|-----------------|------
| [EF Core 5.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) | .NET Standard 2.1 | Mediados de febrero de 2022 | [Anuncio](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-ef-core-5-0/) / [Cambios importantes](xref:core/what-is-new/ef-core-5.0/breaking-changes)
| [EF Core 3.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.1.10) | .NET Standard 2.0 | 3 de diciembre de 2022 (LTS) | [Anuncio](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-3-1-and-entity-framework-6-4/)
| ~~[EF Core 3.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.0.3)~~ | .NET Standard 2.1 | Expiró el 3 de marzo de 2020 | [Anuncio](https://devblogs.microsoft.com/dotnet/announcing-ef-core-3-0-and-ef-6-3-general-availability/) / [Cambios importantes](xref:core/what-is-new/ef-core-3.x/breaking-changes)
| ~~[EF Core 2.2](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.2.6)~~ | .NET Standard 2.0 | Expiró el 23 de diciembre de 2019 | [Anuncio](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-2/)
| [EF Core 2.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.1.14) | .NET Standard 2.0 | 21 de agosto de 2021 (LTS) | [Anuncio](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-1/)
| ~~[EF Core 2.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.0.3)~~ | .NET Standard 2.0 | Expiró el 1 de octubre de 2018 | [Anuncio](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-0/)
| ~~[EF Core 1.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.1.6)~~ | .NET Standard 1.3 | Expiró el 27 de junio de 2019 | [Anuncio](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-1-1/)
| ~~[EF Core 1.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.0.6)~~ | .NET Standard 1.3 | Expiró el 27 de junio de 2019 | [Anuncio](https://devblogs.microsoft.com/dotnet/entity-framework-core-1-0-0-available/)

Consulte las [plataformas compatibles](xref:core/miscellaneous/platforms) para saber qué plataformas concretas se admiten en cada versión de EF Core.

Consulte la [Directiva de compatibilidad de .NET](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) para obtener información sobre la fecha de expiración de la compatibilidad y las versiones de compatibilidad a largo plazo (LTS).

## <a name="guidance-on-updating-to-new-releases"></a>Instrucciones para actualizar a nuevas versiones

* Las versiones admitidas se revisan por motivos de seguridad y para solucionar otros errores críticos. Use siempre el parche más reciente de una versión determinada. Por ejemplo, para EF Core 2.1, use 2.1.x para la "x" más elevada posible.
* Las actualizaciones de la versión principal (por ejemplo, de EF Core 2 a EF Core 3) suelen tener cambios importantes. Se recomienda realizar pruebas exhaustivas al cambiar de una versión principal a otra. Use los vínculos de cambios importantes anteriores para obtener información sobre cómo abordar los cambios importantes.
* Las actualizaciones de versiones secundarias no suelen contener cambios importantes. No obstante, sigue siendo aconsejable realizar pruebas exhaustivas, ya que las nuevas características pueden introducir regresiones.

## <a name="release-planning-and-schedules"></a>Programación y planeación de versiones

Las versiones de EF Core siguen la [programación de envío de .NET Core](https://github.com/dotnet/core/blob/master/roadmap.md).

Normalmente, las versiones de revisión se envían mensualmente, pero tienen un largo plazo.
Estamos trabajando para mejorar esto.

Vea el [proceso de planeamiento de versiones](xref:core/what-is-new/release-planning) para obtener más información sobre cómo decidimos qué enviar en cada versión.
Por lo general, no hacemos un planeamiento detallado más allá de la siguiente versión principal o secundaria.

## <a name="ef-core-60"></a>EF Core 6.0

La siguiente versión estable planeada es **EF Core 6.0** , programada para **noviembre de 2021**.

Estamos trabajando en un plan de alto nivel basado en el [proceso de planeamiento de versiones](xref:core/what-is-new/release-planning).

Sus comentarios sobre la planeación son importantes.
La mejor manera de indicar la importancia de un problema es votar (pulgar arriba 👍) por ese problema en GitHub.
Estos datos se introducen en el proceso de planeación de la próxima versión.

### <a name="get-it-now"></a>Obtenerlo ahora

Los paquetes de EF Core 6.0 están **disponibles ahora** como

* [Compilaciones diarias](https://github.com/dotnet/aspnetcore/blob/master/docs/DailyBuilds.md)
  * Todas las características y correcciones de errores más recientes. Normalmente muy estable; se ejecutan más de 57 000 pruebas en cada compilación.

Además, a medida que avanzamos, se enviarán versiones preliminares a NuGet con frecuencia. Tenga en cuenta que las versiones preliminares van a la zaga de las compilaciones diarias, pero están probadas para trabajar con las versiones preliminares de ASP.NET Core y .NET Core correspondientes.

Usar las versiones preliminares o las compilaciones diarias es una excelente manera de detectar problemas y proporcionar comentarios cuanto antes.
Cuanto antes recibamos esos comentarios, más probable será que puedan procesarse antes de la siguiente versión oficial.
