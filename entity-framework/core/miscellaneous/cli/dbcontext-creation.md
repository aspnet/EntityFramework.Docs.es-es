---
title: 'Creación de DbContext en tiempo de diseño: EF Core'
description: Estrategias para crear un DbContext en tiempo de diseño con Entity Framework Core
author: bricelam
ms.date: 09/16/2019
uid: core/miscellaneous/cli/dbcontext-creation
ms.openlocfilehash: a9d7da2bb76d60ca63eb0dc189f924df125f0a7d
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062014"
---
# <a name="design-time-dbcontext-creation"></a><span data-ttu-id="8d7bb-103">Creación de DbContext en tiempo de diseño</span><span class="sxs-lookup"><span data-stu-id="8d7bb-103">Design-time DbContext Creation</span></span>

<span data-ttu-id="8d7bb-104">Algunos de los comandos de herramientas de EF Core (por ejemplo, los comandos [Migrations][1] ) requieren `DbContext` que se cree una instancia derivada en tiempo de diseño para recopilar detalles sobre los tipos de entidad de la aplicación y cómo se asignan a un esquema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="8d7bb-104">Some of the EF Core Tools commands (for example, the [Migrations][1] commands) require a derived `DbContext` instance to be created at design time in order to gather details about the application's entity types and how they map to a database schema.</span></span> <span data-ttu-id="8d7bb-105">En la mayoría de los casos, es conveniente que el `DbContext` creado por tanto se configure de forma similar a como se [configuraría en tiempo de ejecución][2].</span><span class="sxs-lookup"><span data-stu-id="8d7bb-105">In most cases, it is desirable that the `DbContext` thereby created is configured in a similar way to how it would be [configured at run time][2].</span></span>

<span data-ttu-id="8d7bb-106">Hay varias maneras en las que las herramientas intentan crear `DbContext` :</span><span class="sxs-lookup"><span data-stu-id="8d7bb-106">There are various ways the tools try to create the `DbContext`:</span></span>

## <a name="from-application-services"></a><span data-ttu-id="8d7bb-107">De servicios de aplicación</span><span class="sxs-lookup"><span data-stu-id="8d7bb-107">From application services</span></span>

<span data-ttu-id="8d7bb-108">Si el proyecto de inicio usa el host de [Web ASP.net Core][3] o el [host genérico de .net Core][4], las herramientas intentan obtener el objeto DbContext del proveedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8d7bb-108">If your startup project uses the [ASP.NET Core Web Host][3] or [.NET Core Generic Host][4], the tools try to obtain the DbContext object from the application's service provider.</span></span>

<span data-ttu-id="8d7bb-109">En primer lugar, las herramientas intentan obtener el proveedor de servicios invocando `Program.CreateHostBuilder()` , llamando a `Build()` y, a continuación, accediendo a la `Services` propiedad.</span><span class="sxs-lookup"><span data-stu-id="8d7bb-109">The tools first try to obtain the service provider by invoking `Program.CreateHostBuilder()`, calling `Build()`, then accessing the `Services` property.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/ApplicationService.cs)]

> [!NOTE]
> <span data-ttu-id="8d7bb-110">Cuando se crea una nueva aplicación de ASP.NET Core, este enlace se incluye de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="8d7bb-110">When you create a new ASP.NET Core application, this hook is included by default.</span></span>

<span data-ttu-id="8d7bb-111">El `DbContext` propio y las dependencias de su constructor deben registrarse como servicios en el proveedor de servicios de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="8d7bb-111">The `DbContext` itself and any dependencies in its constructor need to be registered as services in the application's service provider.</span></span> <span data-ttu-id="8d7bb-112">Esto se puede lograr fácilmente si se tiene [un constructor en `DbContext` que toma una instancia de `DbContextOptions<TContext>` como argumento][5] y usa el [ `AddDbContext<TContext>` método][6].</span><span class="sxs-lookup"><span data-stu-id="8d7bb-112">This can be easily achieved by having [a constructor on the `DbContext` that takes an instance of `DbContextOptions<TContext>` as an argument][5] and using the [`AddDbContext<TContext>` method][6].</span></span>

## <a name="using-a-constructor-with-no-parameters"></a><span data-ttu-id="8d7bb-113">Usar un constructor sin parámetros</span><span class="sxs-lookup"><span data-stu-id="8d7bb-113">Using a constructor with no parameters</span></span>

<span data-ttu-id="8d7bb-114">Si DbContext no se puede obtener del proveedor de servicios de aplicación, las herramientas buscan el `DbContext` tipo derivado dentro del proyecto.</span><span class="sxs-lookup"><span data-stu-id="8d7bb-114">If the DbContext can't be obtained from the application service provider, the tools look for the derived `DbContext` type inside the project.</span></span> <span data-ttu-id="8d7bb-115">A continuación, intentan crear una instancia mediante un constructor sin parámetros.</span><span class="sxs-lookup"><span data-stu-id="8d7bb-115">Then they try to create an instance using a constructor with no parameters.</span></span> <span data-ttu-id="8d7bb-116">Este puede ser el constructor predeterminado si `DbContext` se configura mediante el [`OnConfiguring`][7] método.</span><span class="sxs-lookup"><span data-stu-id="8d7bb-116">This can be the default constructor if the `DbContext` is configured using the [`OnConfiguring`][7] method.</span></span>

## <a name="from-a-design-time-factory"></a><span data-ttu-id="8d7bb-117">Desde un generador en tiempo de diseño</span><span class="sxs-lookup"><span data-stu-id="8d7bb-117">From a design-time factory</span></span>

<span data-ttu-id="8d7bb-118">También puede indicar a las herramientas cómo crear su DbContext implementando la `IDesignTimeDbContextFactory<TContext>` interfaz: Si una clase que implementa esta interfaz se encuentra en el mismo proyecto que el derivado `DbContext` o en el proyecto de inicio de la aplicación, las herramientas omiten las otras formas de crear el dbcontext y usar en su lugar el generador en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="8d7bb-118">You can also tell the tools how to create your DbContext by implementing the `IDesignTimeDbContextFactory<TContext>` interface: If a class implementing this interface is found in either the same project as the derived `DbContext` or in the application's startup project, the tools bypass the other ways of creating the DbContext and use the design-time factory instead.</span></span>

[!code-csharp[Main](../../../../samples/core/Miscellaneous/CommandLine/BloggingContextFactory.cs)]

> [!NOTE]
> <span data-ttu-id="8d7bb-119">Antes de EFCore 5,0 `args` , el parámetro no se usaba (vea [este problema][8]).</span><span class="sxs-lookup"><span data-stu-id="8d7bb-119">Prior to EFCore 5.0 the `args` parameter was unused (see [this issue][8]).</span></span>
> <span data-ttu-id="8d7bb-120">Esto se corrigió en EFCore 5,0 y cualquier argumento adicional en tiempo de diseño se pasa a la aplicación a través de ese parámetro.</span><span class="sxs-lookup"><span data-stu-id="8d7bb-120">This is fixed in EFCore 5.0 and any additional design-time arguments are passed into the application through that parameter.</span></span>

<span data-ttu-id="8d7bb-121">Un generador en tiempo de diseño puede ser especialmente útil si necesita configurar DbContext de manera diferente para el tiempo de diseño que en tiempo de ejecución, si el `DbContext` constructor toma parámetros adicionales que no están registrados en di, si no usa di en absoluto, o si por alguna razón prefiere no tener un `BuildWebHost` método en la clase de la aplicación ASP.net Core `Main` .</span><span class="sxs-lookup"><span data-stu-id="8d7bb-121">A design-time factory can be especially useful if you need to configure the DbContext differently for design time than at run time, if the `DbContext` constructor takes additional parameters are not registered in DI, if you are not using DI at all, or if for some reason you prefer not to have a `BuildWebHost` method in your ASP.NET Core application's `Main` class.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/miscellaneous/configuring-dbcontext
  [3]: /aspnet/core/fundamentals/host/web-host
  [4]: /aspnet/core/fundamentals/host/generic-host
  [5]: xref:core/miscellaneous/configuring-dbcontext#constructor-argument
  [6]: xref:core/miscellaneous/configuring-dbcontext#using-dbcontext-with-dependency-injection
  [7]: xref:core/miscellaneous/configuring-dbcontext#onconfiguring
  [8]: https://github.com/aspnet/EntityFrameworkCore/issues/8332
