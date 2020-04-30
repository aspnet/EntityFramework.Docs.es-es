---
title: Pruebas con SQLite-EF Core
description: Uso de SQLite para probar una aplicación EF Core
author: ajcvickers
ms.date: 04/24/2020
uid: core/miscellaneous/testing/sqlite
ms.openlocfilehash: 327fdc230df2a3b4094accf93fffa81f92e0a931
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538282"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a><span data-ttu-id="ffe4e-103">Uso de SQLite para probar una aplicación EF Core</span><span class="sxs-lookup"><span data-stu-id="ffe4e-103">Using SQLite to test an EF Core application</span></span>

> [!WARNING]
> <span data-ttu-id="ffe4e-104">El uso de SQLite puede ser una manera eficaz de probar una aplicación EF Core.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-104">Using SQLite can be an effective way to test an EF Core application.</span></span>
> <span data-ttu-id="ffe4e-105">Sin embargo, pueden surgir problemas en los que SQLite se comporta de forma diferente a otros sistemas de base de datos.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-105">However, problems can arise where SQLite behaves differently from other database systems.</span></span> <span data-ttu-id="ffe4e-106">Vea [código de prueba que usa EF Core](xref:core/miscellaneous/testing/index) para obtener una explicación de los problemas y las ventajas.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-106">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for a discussion of the issues and trade-offs.</span></span>  

<span data-ttu-id="ffe4e-107">Este documento se basa en los conceptos presentados en el [ejemplo que muestra cómo probar las aplicaciones que usan EF Core](xref:core/miscellaneous/testing/testing-sample).</span><span class="sxs-lookup"><span data-stu-id="ffe4e-107">This document builds uses on the concepts introduced in [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample).</span></span>
<span data-ttu-id="ffe4e-108">Los ejemplos de código que se muestran aquí provienen de este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-108">The code examples shown here come from this sample.</span></span>

## <a name="using-sqlite-in-memory-databases"></a><span data-ttu-id="ffe4e-109">Usar bases de datos en memoria de SQLite</span><span class="sxs-lookup"><span data-stu-id="ffe4e-109">Using SQLite in-memory databases</span></span>

<span data-ttu-id="ffe4e-110">Normalmente, SQLite crea bases de datos como archivos simples y accede al archivo en proceso con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-110">Normally, SQLite creates databases as simple files and accesses the file in-process with your application.</span></span>
<span data-ttu-id="ffe4e-111">Esto es muy rápido, especialmente cuando se usa una [SSD](https://en.wikipedia.org/wiki/Solid-state_drive)rápida.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-111">This is very fast, especially when using a fast [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).</span></span> 

<span data-ttu-id="ffe4e-112">SQLite también puede usar bases de datos creadas exclusivamente en memoria.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-112">SQLite can also use databases created purely in-memory.</span></span>
<span data-ttu-id="ffe4e-113">Esto es fácil de usar con EF Core siempre que comprenda la duración de la base de datos en memoria:</span><span class="sxs-lookup"><span data-stu-id="ffe4e-113">This is easy to use with EF Core as long as you understand the in-memory database lifetime:</span></span>
* <span data-ttu-id="ffe4e-114">La base de datos se crea cuando se abre la conexión con ella</span><span class="sxs-lookup"><span data-stu-id="ffe4e-114">The database is created when the connection to it is opened</span></span>
* <span data-ttu-id="ffe4e-115">La base de datos se elimina cuando se cierra la conexión con ella.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-115">The database is deleted when the connection to it is closed</span></span>

<span data-ttu-id="ffe4e-116">EF Core usará una conexión que ya está abierta cuando se le proporcione una y nunca intentará cerrarla.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-116">EF Core will use an already open connection when given one, and will never attempt to close it.</span></span>
<span data-ttu-id="ffe4e-117">Por lo tanto, la clave para usar EF Core con una base de datos SQLite en memoria es abrir la conexión antes de pasarla a EF.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-117">So the key to using EF Core with an in-memory SQLite database is to open the connection before passing it to EF.</span></span>  

<span data-ttu-id="ffe4e-118">En el [ejemplo](xref:core/miscellaneous/testing/testing-sample) se consigue con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="ffe4e-118">The [sample](xref:core/miscellaneous/testing/testing-sample) achieves this with the following code:</span></span>

[!code-csharp[SqliteInMemory](../../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

<span data-ttu-id="ffe4e-119">Aviso:</span><span class="sxs-lookup"><span data-stu-id="ffe4e-119">Notice:</span></span>
* <span data-ttu-id="ffe4e-120">El `CreateInMemoryDatabase` método crea una base de datos en memoria de SQLite y abre la conexión con ella.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-120">The `CreateInMemoryDatabase` method creates a SQLite in-memory database and opens the connection to it.</span></span>
* <span data-ttu-id="ffe4e-121">El creado `DbConnection` se extrae de `ContextOptions` y se guarda.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-121">The created `DbConnection` is extracted from the `ContextOptions` and saved.</span></span>
* <span data-ttu-id="ffe4e-122">La conexión se elimina cuando se elimina la prueba para que no se pierdan los recursos.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-122">The connection is disposed when the test is disposed so that resources are not leaked.</span></span> 

> [!NOTE]
> <span data-ttu-id="ffe4e-123">[Problema #16103](https://github.com/dotnet/efcore/issues/16103) es el seguimiento de las formas de facilitar esta administración de conexiones.</span><span class="sxs-lookup"><span data-stu-id="ffe4e-123">[Issue #16103](https://github.com/dotnet/efcore/issues/16103) is tracking ways to make this connection management easier.</span></span> 
