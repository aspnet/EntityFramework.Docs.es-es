---
title: Proveedor de base de datos InMemory - EF Core
description: Información sobre el proveedor de base de datos InMemory de Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 1af75088ae892e3b428caf6bdb31dd2b750a05fe
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430175"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="ed4b5-103">Proveedor de base de datos InMemory para EF Core</span><span class="sxs-lookup"><span data-stu-id="ed4b5-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="ed4b5-104">Este proveedor de base de datos permite usar Entity Framework Core con una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="ed4b5-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="ed4b5-105">La base de datos en memoria puede resultar útil para realizar pruebas, aunque es posible que el proveedor SQLite del modo en memoria sea un reemplazo de pruebas más adecuado para bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="ed4b5-105">The in-memory database can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="ed4b5-106">La base de datos en memoria está diseñada únicamente para realizar pruebas.</span><span class="sxs-lookup"><span data-stu-id="ed4b5-106">The in-memory database is designed for testing only.</span></span> <span data-ttu-id="ed4b5-107">Este proveedor se mantiene como parte del [proyecto Entity Framework Core](https://github.com/dotnet/efcore).</span><span class="sxs-lookup"><span data-stu-id="ed4b5-107">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/dotnet/efcore).</span></span>

## <a name="install"></a><span data-ttu-id="ed4b5-108">Instalar</span><span class="sxs-lookup"><span data-stu-id="ed4b5-108">Install</span></span>

<span data-ttu-id="ed4b5-109">Instale el [paquete NuGet Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span><span class="sxs-lookup"><span data-stu-id="ed4b5-109">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="ed4b5-110">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="ed4b5-110">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="ed4b5-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ed4b5-111">Visual Studio</span></span>](#tab/vs)

```powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="ed4b5-112">Introducción</span><span class="sxs-lookup"><span data-stu-id="ed4b5-112">Get Started</span></span>

<span data-ttu-id="ed4b5-113">Los siguientes recursos le ayudarán a empezar a trabajar con este proveedor.</span><span class="sxs-lookup"><span data-stu-id="ed4b5-113">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="ed4b5-114">Pruebas con InMemory</span><span class="sxs-lookup"><span data-stu-id="ed4b5-114">Testing with InMemory</span></span>](xref:core/testing/in-memory)
* [<span data-ttu-id="ed4b5-115">Pruebas de la aplicación de ejemplo UnicornStore</span><span class="sxs-lookup"><span data-stu-id="ed4b5-115">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="ed4b5-116">Motores de base de datos compatibles</span><span class="sxs-lookup"><span data-stu-id="ed4b5-116">Supported Database Engines</span></span>

<span data-ttu-id="ed4b5-117">Base de datos de memoria en proceso, diseñada únicamente para realizar pruebas.</span><span class="sxs-lookup"><span data-stu-id="ed4b5-117">In-process memory database, designed for testing purposes only.</span></span>
