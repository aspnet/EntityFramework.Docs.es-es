---
title: Implementaciones de .NET compatibles - EF Core
description: Información sobre las plataformas admitidas en las versiones de Entity Framework Core
author: bricelam
ms.date: 06/26/2020
uid: core/miscellaneous/platforms
ms.openlocfilehash: 6b888843d491805e735fd903253d7f10c028dd9f
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431340"
---
# <a name="net-implementations-supported-by-ef-core"></a>Implementaciones de .NET compatibles con EF Core

Queremos que EF Core esté disponible para los desarrolladores en todas las implementaciones de .NET modernas y seguimos trabajando para alcanzar ese objetivo. Aunque la compatibilidad de EF Core en .NET Core está cubierta por pruebas automatizadas y se sabe que muchas aplicaciones van a usarlo correctamente, Mono, Xamarin y UWP presentan algunos problemas.

## <a name="overview"></a>Información general

En la siguiente tabla se ofrecen instrucciones para cada implementación de .NET:

| EF Core                       | 2.1 y 3.1 | 5.0             |
|:------------------------------|:------------|:----------------|
| .NET Standard                 | 2.0         | 2.1             |
| .NET Core                     | 2.0         | 3.0             |
| .NET Framework<sup>(1)</sup>  | 4.7.2       | (no se admite) |
| Mono                          | 5.4         | 6.4             |
| Xamarin.iOS<sup>(2)</sup>     | 10.14       | 12.16           |
| Xamarin.Mac<sup>(2)</sup>     | 3.8         | 5.16            |
| Xamarin.Android<sup>(2)</sup> | 8.0         | 10.0            |
| UWP<sup>(3)</sup>             | 10.0.16299  | TBD             |
| Unity<sup>(4)</sup>           | 2018.1      | TBD             |

<sup>(1)</sup> Consulte la sección [.NET Framework](#net-framework) a continuación.

<sup>(2)</sup> Existen problemas y limitaciones conocidas con Xamarin que pueden impedir que algunas aplicaciones desarrolladas con EF Core funcionen correctamente. Compruebe la lista de [problemas activos](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) para ver soluciones alternativas.

<sup>(3)</sup> Se recomienda EF Core 2.0.1 y versiones más recientes. Instale el paquete [.NET Core UWP 6.x](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/). Consulte la sección [Plataforma universal de Windows](#universal-windows-platform) de este artículo.

<sup>(4)</sup> Hay problemas y limitaciones conocidas con Unity. Revise la lista de [problemas activos](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).

## <a name="net-framework"></a>.NET Framework

Es posible que las aplicaciones que tengan como destino .NET Framework deban modificarse para poder trabajar con bibliotecas de .NET Standard:

Edite el archivo de proyecto y asegúrese de que la siguiente entrada aparece en el grupo de propiedades inicial:

```xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

En los proyectos de prueba, asegúrese también de que la entrada siguiente está presente:

```xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

Si quiere usar una versión anterior de Visual Studio, asegúrese de que [actualiza el cliente de NuGet a la versión 3.6.0](https://www.nuget.org/downloads) para poder trabajar con bibliotecas de .NET Standard 2.0.

Si es posible, también se recomienda migrar de packages.config de NuGet a PackageReference. Agregue la propiedad siguiente al archivo del proyecto:

```xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a>Plataforma universal de Windows

Las versiones anteriores de EF Core y .NET UWP tuvieron numerosos problemas de compatibilidad, especialmente con aplicaciones compiladas con la cadena de herramientas de .NET Native. La nueva versión de .NET UWP agrega compatibilidad con .NET Standard 2.0 y contiene .NET Native 2.0, que soluciona la mayoría de los problemas de compatibilidad que se notificaban anteriormente. EF Core 2.0.1 se ha probado más exhaustivamente con UWP pero la prueba no está automatizada.

Al usar EF Core en UWP:

* Para optimizar el rendimiento de las consultas, evite tipos anónimos en las consultas LINQ. Para implementar una aplicación de UWP en la tienda de aplicaciones, la aplicación debe estar compilada con .NET Native. Las consultas con tipos anónimos tienen un menor rendimiento en .NET Native.

* Para optimizar el rendimiento de `SaveChanges()`, use [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) e implemente [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx) y [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) en los tipos de entidad.

## <a name="report-issues"></a>Problemas de informes

En el caso de cualquier combinación que no funcione según lo esperado, se recomienda crear nuevos problemas en el [seguimiento de problemas de EF Core](https://github.com/dotnet/efcore/issues/new). En el caso de problemas relacionados con Xamarin, use el seguimiento de problemas de [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) o de [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).
