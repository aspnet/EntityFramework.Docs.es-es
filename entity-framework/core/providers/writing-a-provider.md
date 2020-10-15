---
title: Escribir un proveedor de base de datos-EF Core
description: Información sobre cómo escribir un nuevo proveedor de Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/writing-a-provider
ms.openlocfilehash: db02402c6651401aacaca9c3538c29f526c6720a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062703"
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="d6aaa-103">Escritura de un proveedor de base de datos</span><span class="sxs-lookup"><span data-stu-id="d6aaa-103">Writing a Database Provider</span></span>

<span data-ttu-id="d6aaa-104">Para obtener información sobre cómo escribir un proveedor de bases de datos de Entity Framework Core, consulte para [que pueda escribir un proveedor de EF Core](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) por [Arthur Vickers](https://github.com/ajcvickers).</span><span class="sxs-lookup"><span data-stu-id="d6aaa-104">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

> [!NOTE]
> <span data-ttu-id="d6aaa-105">Estas publicaciones no se han actualizado desde EF Core 1,1 y ha habido cambios significativos desde ese momento.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-105">These posts have not been updated since EF Core 1.1 and there have been significant changes since that time.</span></span>  
<span data-ttu-id="d6aaa-106">El [problema 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) está realizando el seguimiento de las actualizaciones de esta documentación.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-106">[Issue 681](https://github.com/dotnet/EntityFramework.Docs/issues/681) is tracking updates to this documentation.</span></span>

<span data-ttu-id="d6aaa-107">La EF Core código base es de código abierto y contiene varios proveedores de bases de datos que se pueden utilizar como referencia.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-107">The EF Core codebase is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="d6aaa-108">Puede encontrar el código fuente en <https://github.com/aspnet/EntityFrameworkCore> .</span><span class="sxs-lookup"><span data-stu-id="d6aaa-108">You can find the source code at <https://github.com/aspnet/EntityFrameworkCore>.</span></span> <span data-ttu-id="d6aaa-109">También puede ser útil examinar el código para proveedores de terceros de uso frecuente, como [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql)y [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span><span class="sxs-lookup"><span data-stu-id="d6aaa-109">It may also be helpful to look at the code for commonly used third-party providers, such as [Npgsql](https://github.com/npgsql/Npgsql.EntityFrameworkCore.PostgreSQL), [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and [SQL Server Compact](https://github.com/ErikEJ/EntityFramework.SqlServerCompact).</span></span> <span data-ttu-id="d6aaa-110">En concreto, estos proyectos se configuran para extender desde y ejecutar pruebas funcionales que publicamos en NuGet.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-110">In particular, these projects are setup to extend from and run functional tests that we publish on NuGet.</span></span> <span data-ttu-id="d6aaa-111">Se recomienda encarecidamente este tipo de instalación.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-111">This kind of setup is strongly recommended.</span></span>

## <a name="keeping-up-to-date-with-provider-changes"></a><span data-ttu-id="d6aaa-112">Mantenerse al día con los cambios del proveedor</span><span class="sxs-lookup"><span data-stu-id="d6aaa-112">Keeping up-to-date with provider changes</span></span>

<span data-ttu-id="d6aaa-113">A partir del trabajo después de la versión 2,1, hemos creado un [registro de cambios](xref:core/providers/provider-log) que pueden necesitar cambios correspondientes en el código del proveedor.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-113">Starting with work after the 2.1 release, we have created a [log of changes](xref:core/providers/provider-log) that may need corresponding changes in provider code.</span></span> <span data-ttu-id="d6aaa-114">Esto está pensado para ayudarle a actualizar un proveedor existente para que funcione con una nueva versión de EF Core.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-114">This is intended to help when updating an existing provider to work with a new version of EF Core.</span></span>

<span data-ttu-id="d6aaa-115">Antes de 2,1, usamos las [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) etiquetas y [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) en los problemas de Github y las solicitudes de incorporación de cambios para un propósito similar.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-115">Prior to 2.1, we used the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) and [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) labels on our GitHub issues and pull requests for a similar purpose.</span></span> <span data-ttu-id="d6aaa-116">Vamos a usar estas etiquetas en los problemas para proporcionar una indicación de qué elementos de trabajo de una versión determinada también pueden requerir que el trabajo se realice en los proveedores.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-116">We will continiue to use these lables on issues to give an indication which work items in a given release may also require work to be done in providers.</span></span> <span data-ttu-id="d6aaa-117">Una `providers-beware` etiqueta normalmente significa que la implementación de un elemento de trabajo puede interrumpir los proveedores, mientras `providers-fyi` que una etiqueta normalmente significa que los proveedores no se interrumpirán, pero puede que sea necesario cambiar el código, por ejemplo, para habilitar la nueva funcionalidad.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-117">A `providers-beware` label typically means that the implementation of an work item may break providers, while a `providers-fyi` label typically means that providers will not be broken, but code may need to be changed anyway, for example, to enable new functionality.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="d6aaa-118">Nombres sugeridos de proveedores de terceros</span><span class="sxs-lookup"><span data-stu-id="d6aaa-118">Suggested naming of third party providers</span></span>

<span data-ttu-id="d6aaa-119">Se recomienda usar la siguiente nomenclatura para los paquetes NuGet.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-119">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="d6aaa-120">Esto es coherente con los nombres de los paquetes proporcionados por el equipo de EF Core.</span><span class="sxs-lookup"><span data-stu-id="d6aaa-120">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="d6aaa-121">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="d6aaa-121">For example:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`
