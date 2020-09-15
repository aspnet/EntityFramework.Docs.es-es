---
title: Proveedor de base de datos InMemory - EF Core
description: Información sobre el proveedor de base de datos InMemory de Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/providers/in-memory/index
ms.openlocfilehash: 9d0a5d14cdb047b80788fbe4d9d34deccdbd4ce1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071360"
---
# <a name="ef-core-in-memory-database-provider"></a><span data-ttu-id="c4a6b-103">Proveedor de base de datos InMemory para EF Core</span><span class="sxs-lookup"><span data-stu-id="c4a6b-103">EF Core In-Memory Database Provider</span></span>

<span data-ttu-id="c4a6b-104">Este proveedor de base de datos permite usar Entity Framework Core con una base de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="c4a6b-104">This database provider allows Entity Framework Core to be used with an in-memory database.</span></span> <span data-ttu-id="c4a6b-105">Puede resultar útil en el caso de pruebas, aunque es posible que el proveedor SQLite del modo en memoria sea un reemplazo de pruebas más adecuado para bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="c4a6b-105">This can be useful for testing, although the SQLite provider in in-memory mode may be a more appropriate test replacement for relational databases.</span></span> <span data-ttu-id="c4a6b-106">Este proveedor se mantiene como parte del [proyecto Entity Framework Core](https://github.com/aspnet/EntityFrameworkCore).</span><span class="sxs-lookup"><span data-stu-id="c4a6b-106">The provider is maintained as part of the [Entity Framework Core Project](https://github.com/aspnet/EntityFrameworkCore).</span></span>

## <a name="install"></a><span data-ttu-id="c4a6b-107">Instalar</span><span class="sxs-lookup"><span data-stu-id="c4a6b-107">Install</span></span>

<span data-ttu-id="c4a6b-108">Instale el [paquete NuGet Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span><span class="sxs-lookup"><span data-stu-id="c4a6b-108">Install the [Microsoft.EntityFrameworkCore.InMemory NuGet package](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/).</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="c4a6b-109">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="c4a6b-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

### <a name="visual-studio"></a>[<span data-ttu-id="c4a6b-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c4a6b-110">Visual Studio</span></span>](#tab/vs)

``` powershell
Install-Package Microsoft.EntityFrameworkCore.InMemory
```

***

## <a name="get-started"></a><span data-ttu-id="c4a6b-111">Introducción</span><span class="sxs-lookup"><span data-stu-id="c4a6b-111">Get Started</span></span>

<span data-ttu-id="c4a6b-112">Los siguientes recursos le ayudarán a empezar a trabajar con este proveedor.</span><span class="sxs-lookup"><span data-stu-id="c4a6b-112">The following resources will help you get started with this provider.</span></span>

* [<span data-ttu-id="c4a6b-113">Pruebas con InMemory</span><span class="sxs-lookup"><span data-stu-id="c4a6b-113">Testing with InMemory</span></span>](xref:core/miscellaneous/testing/in-memory)
* [<span data-ttu-id="c4a6b-114">Pruebas de la aplicación de ejemplo UnicornStore</span><span class="sxs-lookup"><span data-stu-id="c4a6b-114">UnicornStore Sample Application Tests</span></span>](https://github.com/rowanmiller/UnicornStore/blob/master/UnicornStore/src/UnicornStore.Tests/Controllers/ShippingControllerTests.cs)

## <a name="supported-database-engines"></a><span data-ttu-id="c4a6b-115">Motores de base de datos compatibles</span><span class="sxs-lookup"><span data-stu-id="c4a6b-115">Supported Database Engines</span></span>

<span data-ttu-id="c4a6b-116">Base de datos de memoria en proceso (diseñada para pruebas únicamente)</span><span class="sxs-lookup"><span data-stu-id="c4a6b-116">In-process memory database (designed for testing purposes only)</span></span>
