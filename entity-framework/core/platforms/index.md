---
title: Implementaciones de .NET compatibles - EF Core
author: bricelam
ms.date: 03/03/2020
uid: core/platforms/index
ms.openlocfilehash: 2738645596a325a1a43fd5b2164e370dd8c5893e
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434128"
---
# <a name="net-implementations-supported-by-ef-core"></a><span data-ttu-id="9c632-102">Implementaciones de .NET compatibles con EF Core</span><span class="sxs-lookup"><span data-stu-id="9c632-102">.NET implementations supported by EF Core</span></span>

<span data-ttu-id="9c632-103">Queremos que EF Core esté disponible para los desarrolladores en todas las implementaciones de .NET modernas y seguimos trabajando para alcanzar ese objetivo.</span><span class="sxs-lookup"><span data-stu-id="9c632-103">We want EF Core to be available to developers on all modern .NET implementations, and we're still working towards that goal.</span></span> <span data-ttu-id="9c632-104">Aunque la compatibilidad de EF Core en .NET Core está cubierta por pruebas automatizadas y se sabe que muchas aplicaciones van a usarlo correctamente, Mono, Xamarin y UWP presentan algunos problemas.</span><span class="sxs-lookup"><span data-stu-id="9c632-104">While EF Core's support on .NET Core is covered by automated testing and many applications known to be using it successfully, Mono, Xamarin and UWP have some issues.</span></span>

## <a name="overview"></a><span data-ttu-id="9c632-105">Información general</span><span class="sxs-lookup"><span data-stu-id="9c632-105">Overview</span></span>

<span data-ttu-id="9c632-106">En la siguiente tabla se ofrecen instrucciones para cada implementación de .NET:</span><span class="sxs-lookup"><span data-stu-id="9c632-106">The following table provides guidance for each .NET implementation:</span></span>

| <span data-ttu-id="9c632-107">EF Core</span><span class="sxs-lookup"><span data-stu-id="9c632-107">EF Core</span></span>                       | <span data-ttu-id="9c632-108">2.1 y 3.1</span><span class="sxs-lookup"><span data-stu-id="9c632-108">2.1 and 3.1</span></span> |
|:------------------------------|:------------|
| <span data-ttu-id="9c632-109">.NET Standard</span><span class="sxs-lookup"><span data-stu-id="9c632-109">.NET Standard</span></span>                 | <span data-ttu-id="9c632-110">2.0</span><span class="sxs-lookup"><span data-stu-id="9c632-110">2.0</span></span>         |
| <span data-ttu-id="9c632-111">.NET Core</span><span class="sxs-lookup"><span data-stu-id="9c632-111">.NET Core</span></span>                     | <span data-ttu-id="9c632-112">2.0</span><span class="sxs-lookup"><span data-stu-id="9c632-112">2.0</span></span>         |
| <span data-ttu-id="9c632-113">.NET Framework<sup>(1)</sup></span><span class="sxs-lookup"><span data-stu-id="9c632-113">.NET Framework<sup>(1)</sup></span></span>  | <span data-ttu-id="9c632-114">4.7.2</span><span class="sxs-lookup"><span data-stu-id="9c632-114">4.7.2</span></span>       |
| <span data-ttu-id="9c632-115">Mono</span><span class="sxs-lookup"><span data-stu-id="9c632-115">Mono</span></span>                          | <span data-ttu-id="9c632-116">5.4</span><span class="sxs-lookup"><span data-stu-id="9c632-116">5.4</span></span>         |
| <span data-ttu-id="9c632-117">Xamarin.iOS<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="9c632-117">Xamarin.iOS<sup>(2)</sup></span></span>     | <span data-ttu-id="9c632-118">10.14</span><span class="sxs-lookup"><span data-stu-id="9c632-118">10.14</span></span>       |
| <span data-ttu-id="9c632-119">Xamarin.Android<sup>(2)</sup></span><span class="sxs-lookup"><span data-stu-id="9c632-119">Xamarin.Android<sup>(2)</sup></span></span> | <span data-ttu-id="9c632-120">8.0</span><span class="sxs-lookup"><span data-stu-id="9c632-120">8.0</span></span>         |
| <span data-ttu-id="9c632-121">UWP<sup>(3)</sup></span><span class="sxs-lookup"><span data-stu-id="9c632-121">UWP<sup>(3)</sup></span></span>             | <span data-ttu-id="9c632-122">10.0.16299</span><span class="sxs-lookup"><span data-stu-id="9c632-122">10.0.16299</span></span>  |
| <span data-ttu-id="9c632-123">Unity<sup>(4)</sup></span><span class="sxs-lookup"><span data-stu-id="9c632-123">Unity<sup>(4)</sup></span></span>           | <span data-ttu-id="9c632-124">2018.1</span><span class="sxs-lookup"><span data-stu-id="9c632-124">2018.1</span></span>      |

<span data-ttu-id="9c632-125"><sup>(1)</sup> Consulte la sección [.NET Framework](#net-framework) a continuación.</span><span class="sxs-lookup"><span data-stu-id="9c632-125"><sup>(1)</sup> See the [.NET Framework](#net-framework) section below.</span></span>

<span data-ttu-id="9c632-126"><sup>(2)</sup> Existen problemas y limitaciones conocidas con Xamarin que pueden impedir que algunas aplicaciones desarrolladas con EF Core funcionen correctamente.</span><span class="sxs-lookup"><span data-stu-id="9c632-126"><sup>(2)</sup> There are issues and known limitations with Xamarin which may prevent some applications developed using EF Core from working correctly.</span></span> <span data-ttu-id="9c632-127">Compruebe la lista de [problemas activos](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) para ver soluciones alternativas.</span><span class="sxs-lookup"><span data-stu-id="9c632-127">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin) for workarounds.</span></span>

<span data-ttu-id="9c632-128"><sup>(3)</sup> Se recomienda EF Core 2.0.1 y versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="9c632-128"><sup>(3)</sup> EF Core 2.0.1 and newer recommended.</span></span> <span data-ttu-id="9c632-129">Instale el paquete [.NET Core UWP 6.x](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/).</span><span class="sxs-lookup"><span data-stu-id="9c632-129">Install the [.NET Core UWP 6.x package](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/).</span></span> <span data-ttu-id="9c632-130">Consulte la sección [Plataforma universal de Windows](#universal-windows-platform) de este artículo.</span><span class="sxs-lookup"><span data-stu-id="9c632-130">See the [Universal Windows Platform](#universal-windows-platform) section of this article.</span></span>

<span data-ttu-id="9c632-131"><sup>(4)</sup> Hay problemas y limitaciones conocidas con Unity.</span><span class="sxs-lookup"><span data-stu-id="9c632-131"><sup>(4)</sup> There are issues and known limitations with Unity.</span></span> <span data-ttu-id="9c632-132">Revise la lista de [problemas activos](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).</span><span class="sxs-lookup"><span data-stu-id="9c632-132">Check the list of [active issues](https://github.com/aspnet/entityframeworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity).</span></span>

## <a name="net-framework"></a><span data-ttu-id="9c632-133">.NET Framework</span><span class="sxs-lookup"><span data-stu-id="9c632-133">.NET Framework</span></span>

<span data-ttu-id="9c632-134">Es posible que las aplicaciones que tengan como destino .NET Framework deban modificarse para poder trabajar con bibliotecas de .NET Standard:</span><span class="sxs-lookup"><span data-stu-id="9c632-134">Applications that target .NET Framework may need changes to work with .NET Standard libraries:</span></span>

<span data-ttu-id="9c632-135">Edite el archivo de proyecto y asegúrese de que la siguiente entrada aparece en el grupo de propiedades inicial:</span><span class="sxs-lookup"><span data-stu-id="9c632-135">Edit the project file and make sure the following entry appears in the initial property group:</span></span>

``` xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

<span data-ttu-id="9c632-136">En los proyectos de prueba, asegúrese también de que la entrada siguiente está presente:</span><span class="sxs-lookup"><span data-stu-id="9c632-136">For test projects, also make sure the following entry is present:</span></span>

``` xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

<span data-ttu-id="9c632-137">Si quiere usar una versión anterior de Visual Studio, asegúrese de que [actualiza el cliente de NuGet a la versión 3.6.0](https://www.nuget.org/downloads) para poder trabajar con bibliotecas de .NET Standard 2.0.</span><span class="sxs-lookup"><span data-stu-id="9c632-137">If you want to use an older version of Visual Studio, make sure you [upgrade the NuGet client to version 3.6.0](https://www.nuget.org/downloads) to work with .NET Standard 2.0 libraries.</span></span>

<span data-ttu-id="9c632-138">Si es posible, también se recomienda migrar de packages.config de NuGet a PackageReference.</span><span class="sxs-lookup"><span data-stu-id="9c632-138">We also recommend migrating from NuGet packages.config to PackageReference if possible.</span></span> <span data-ttu-id="9c632-139">Agregue la propiedad siguiente al archivo del proyecto:</span><span class="sxs-lookup"><span data-stu-id="9c632-139">Add the following property to your project file:</span></span>

``` xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a><span data-ttu-id="9c632-140">Plataforma universal de Windows</span><span class="sxs-lookup"><span data-stu-id="9c632-140">Universal Windows Platform</span></span>

<span data-ttu-id="9c632-141">Las versiones anteriores de EF Core y .NET UWP tuvieron numerosos problemas de compatibilidad, especialmente con aplicaciones compiladas con la cadena de herramientas de .NET Native.</span><span class="sxs-lookup"><span data-stu-id="9c632-141">Earlier versions of EF Core and .NET UWP had numerous compatibility issues, especially with applications compiled with the .NET Native toolchain.</span></span> <span data-ttu-id="9c632-142">La nueva versión de .NET UWP agrega compatibilidad con .NET Standard 2.0 y contiene .NET Native 2.0, que soluciona la mayoría de los problemas de compatibilidad que se notificaban anteriormente.</span><span class="sxs-lookup"><span data-stu-id="9c632-142">The new .NET UWP version adds support for .NET Standard 2.0 and contains .NET Native 2.0, which fixes most of the compatibility issues previously reported.</span></span> <span data-ttu-id="9c632-143">EF Core 2.0.1 se ha probado más exhaustivamente con UWP pero la prueba no está automatizada.</span><span class="sxs-lookup"><span data-stu-id="9c632-143">EF Core 2.0.1 has been tested more thoroughly with UWP but testing is not automated.</span></span>

<span data-ttu-id="9c632-144">Al usar EF Core en UWP:</span><span class="sxs-lookup"><span data-stu-id="9c632-144">When using EF Core on UWP:</span></span>

* <span data-ttu-id="9c632-145">Para optimizar el rendimiento de las consultas, evite tipos anónimos en las consultas LINQ.</span><span class="sxs-lookup"><span data-stu-id="9c632-145">To optimize query performance, avoid anonymous types in LINQ queries.</span></span> <span data-ttu-id="9c632-146">Para implementar una aplicación de UWP en la tienda de aplicaciones, la aplicación debe estar compilada con .NET Native.</span><span class="sxs-lookup"><span data-stu-id="9c632-146">Deploying a UWP application to the app store requires an application to be compiled with .NET Native.</span></span> <span data-ttu-id="9c632-147">Las consultas con tipos anónimos tienen un menor rendimiento en .NET Native.</span><span class="sxs-lookup"><span data-stu-id="9c632-147">Queries with anonymous types have worse performance on .NET Native.</span></span>

* <span data-ttu-id="9c632-148">Para optimizar el rendimiento de `SaveChanges()`, use [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) e implemente [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx) y [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) en los tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="9c632-148">To optimize `SaveChanges()` performance, use [ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) and implement [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx), [INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx), and [INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) in your entity types.</span></span>

## <a name="report-issues"></a><span data-ttu-id="9c632-149">Problemas de informes</span><span class="sxs-lookup"><span data-stu-id="9c632-149">Report issues</span></span>

<span data-ttu-id="9c632-150">En el caso de cualquier combinación que no funcione según lo esperado, se recomienda crear nuevos problemas en el [seguimiento de problemas de EF Core](https://github.com/aspnet/entityframeworkcore/issues/new).</span><span class="sxs-lookup"><span data-stu-id="9c632-150">For any combination that doesn't work as expected, we encourage creating new issues on the [EF Core issue tracker](https://github.com/aspnet/entityframeworkcore/issues/new).</span></span> <span data-ttu-id="9c632-151">En el caso de problemas relacionados con Xamarin, use el seguimiento de problemas de [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) o de [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).</span><span class="sxs-lookup"><span data-stu-id="9c632-151">For Xamarin-specific issues use the issue tracker for [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) or [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new).</span></span>
