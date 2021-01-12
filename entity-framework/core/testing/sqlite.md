---
title: Pruebas con SQLite-EF Core
description: Uso de SQLite para probar una aplicación Entity Framework Core
author: ajcvickers
ms.date: 04/24/2020
uid: core/testing/sqlite
ms.openlocfilehash: da2504cfe7997a10a5ee8c447b1c6ef00dd02369
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129062"
---
# <a name="using-sqlite-to-test-an-ef-core-application"></a><span data-ttu-id="3e821-103">Uso de SQLite para probar una aplicación EF Core</span><span class="sxs-lookup"><span data-stu-id="3e821-103">Using SQLite to test an EF Core application</span></span>

> [!WARNING]
> <span data-ttu-id="3e821-104">El uso de SQLite puede ser una manera eficaz de probar una aplicación EF Core.</span><span class="sxs-lookup"><span data-stu-id="3e821-104">Using SQLite can be an effective way to test an EF Core application.</span></span>
> <span data-ttu-id="3e821-105">Sin embargo, pueden surgir problemas en los que SQLite se comporta de forma diferente a otros sistemas de base de datos.</span><span class="sxs-lookup"><span data-stu-id="3e821-105">However, problems can arise where SQLite behaves differently from other database systems.</span></span>
> <span data-ttu-id="3e821-106">Vea [código de prueba que usa EF Core](xref:core/testing/index) para obtener una explicación de los problemas y las ventajas.</span><span class="sxs-lookup"><span data-stu-id="3e821-106">See [Testing code that uses EF Core](xref:core/testing/index) for a discussion of the issues and trade-offs.</span></span>

<span data-ttu-id="3e821-107">Este documento se basa en los conceptos presentados en el [ejemplo que muestra cómo probar las aplicaciones que usan EF Core](xref:core/testing/testing-sample).</span><span class="sxs-lookup"><span data-stu-id="3e821-107">This document builds uses on the concepts introduced in [Sample showing how to test applications that use EF Core](xref:core/testing/testing-sample).</span></span>
<span data-ttu-id="3e821-108">Los ejemplos de código que se muestran aquí provienen de este ejemplo.</span><span class="sxs-lookup"><span data-stu-id="3e821-108">The code examples shown here come from this sample.</span></span>

## <a name="using-sqlite-in-memory-databases"></a><span data-ttu-id="3e821-109">Usar bases de datos en memoria de SQLite</span><span class="sxs-lookup"><span data-stu-id="3e821-109">Using SQLite in-memory databases</span></span>

<span data-ttu-id="3e821-110">Normalmente, SQLite crea bases de datos como archivos simples y accede al archivo en proceso con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="3e821-110">Normally, SQLite creates databases as simple files and accesses the file in-process with your application.</span></span>
<span data-ttu-id="3e821-111">Esto es muy rápido, especialmente cuando se usa una [SSD](https://en.wikipedia.org/wiki/Solid-state_drive)rápida.</span><span class="sxs-lookup"><span data-stu-id="3e821-111">This is very fast, especially when using a fast [SSD](https://en.wikipedia.org/wiki/Solid-state_drive).</span></span>

<span data-ttu-id="3e821-112">SQLite también puede usar bases de datos creadas exclusivamente en memoria.</span><span class="sxs-lookup"><span data-stu-id="3e821-112">SQLite can also use databases created purely in-memory.</span></span>
<span data-ttu-id="3e821-113">Esto es fácil de usar con EF Core siempre que comprenda la duración de la base de datos en memoria:</span><span class="sxs-lookup"><span data-stu-id="3e821-113">This is easy to use with EF Core as long as you understand the in-memory database lifetime:</span></span>

* <span data-ttu-id="3e821-114">La base de datos se crea cuando se abre la conexión con ella</span><span class="sxs-lookup"><span data-stu-id="3e821-114">The database is created when the connection to it is opened</span></span>
* <span data-ttu-id="3e821-115">La base de datos se elimina cuando se cierra la conexión con ella.</span><span class="sxs-lookup"><span data-stu-id="3e821-115">The database is deleted when the connection to it is closed</span></span>

<span data-ttu-id="3e821-116">EF Core usará una conexión que ya está abierta cuando se le proporcione una y nunca intentará cerrarla.</span><span class="sxs-lookup"><span data-stu-id="3e821-116">EF Core will use an already open connection when given one, and will never attempt to close it.</span></span>
<span data-ttu-id="3e821-117">Por lo tanto, la clave para usar EF Core con una base de datos SQLite en memoria es abrir la conexión antes de pasarla a EF.</span><span class="sxs-lookup"><span data-stu-id="3e821-117">So the key to using EF Core with an in-memory SQLite database is to open the connection before passing it to EF.</span></span>

<span data-ttu-id="3e821-118">En el [ejemplo](xref:core/testing/testing-sample) se consigue con el código siguiente:</span><span class="sxs-lookup"><span data-stu-id="3e821-118">The [sample](xref:core/testing/testing-sample) achieves this with the following code:</span></span>

[!code-csharp[SqliteInMemory](../../../samples/core/Miscellaneous/Testing/ItemsWebApi/Tests/SqliteInMemoryItemsControllerTest.cs?name=SqliteInMemory)]

<span data-ttu-id="3e821-119">Aviso:</span><span class="sxs-lookup"><span data-stu-id="3e821-119">Notice:</span></span>

* <span data-ttu-id="3e821-120">El `CreateInMemoryDatabase` método crea una base de datos en memoria de SQLite y abre la conexión con ella.</span><span class="sxs-lookup"><span data-stu-id="3e821-120">The `CreateInMemoryDatabase` method creates a SQLite in-memory database and opens the connection to it.</span></span>
* <span data-ttu-id="3e821-121">El creado `DbConnection` se extrae de `ContextOptions` y se guarda.</span><span class="sxs-lookup"><span data-stu-id="3e821-121">The created `DbConnection` is extracted from the `ContextOptions` and saved.</span></span>
* <span data-ttu-id="3e821-122">La conexión se elimina cuando se elimina la prueba para que no se pierdan los recursos.</span><span class="sxs-lookup"><span data-stu-id="3e821-122">The connection is disposed when the test is disposed so that resources are not leaked.</span></span>

> [!NOTE]
> <span data-ttu-id="3e821-123">[Problema #16103](https://github.com/dotnet/efcore/issues/16103) es el seguimiento de las formas de facilitar esta administración de conexiones.</span><span class="sxs-lookup"><span data-stu-id="3e821-123">[Issue #16103](https://github.com/dotnet/efcore/issues/16103) is tracking ways to make this connection management easier.</span></span>
