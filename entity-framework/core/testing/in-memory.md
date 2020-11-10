---
title: Pruebas con la base de datos de EF In-Memory-EF Core
description: Uso de la base de datos de EF en memoria para probar una aplicación Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: db91570dc9d5a4b95d513df509867e9bca406356
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431337"
---
# <a name="testing-with-the-ef-in-memory-database"></a><span data-ttu-id="23f50-103">Pruebas con la base de datos de EF In-Memory</span><span class="sxs-lookup"><span data-stu-id="23f50-103">Testing with the EF In-Memory Database</span></span>

> [!WARNING]
> <span data-ttu-id="23f50-104">La base de datos en memoria de EF a menudo se comporta de forma diferente a las bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="23f50-104">The EF in-memory database often behaves differently than relational databases.</span></span>
> <span data-ttu-id="23f50-105">Use la base de datos en memoria de EF solo después de comprender totalmente los problemas y las ventajas y desventajas que conlleva, como se describe en [probar el código que usa EF Core](xref:core/testing/index).</span><span class="sxs-lookup"><span data-stu-id="23f50-105">Only use the EF in-memory database after fully understanding the issues and trade-offs involved, as discussed in [Testing code that uses EF Core](xref:core/testing/index).</span></span>  

> [!TIP]
> <span data-ttu-id="23f50-106">SQLite es un proveedor relacional y también puede usar bases de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="23f50-106">SQLite is a relational provider and can also use in-memory databases.</span></span>
> <span data-ttu-id="23f50-107">Considere la posibilidad de utilizarlo para realizar pruebas con el fin de encontrar más coincidencia con los comportamientos comunes de la base de datos</span><span class="sxs-lookup"><span data-stu-id="23f50-107">Consider using this for testing to more closely match common relational database behaviors.</span></span>
> <span data-ttu-id="23f50-108">Esto se trata en el [uso de SQLite para probar una aplicación EF Core](xref:core/testing/sqlite).</span><span class="sxs-lookup"><span data-stu-id="23f50-108">This is covered in [Using SQLite to test an EF Core application](xref:core/testing/sqlite).</span></span>

<span data-ttu-id="23f50-109">La información de esta página se encuentra ahora en otras ubicaciones:</span><span class="sxs-lookup"><span data-stu-id="23f50-109">The information on this page now lives in other locations:</span></span>

* <span data-ttu-id="23f50-110">Vea [código de prueba que usa EF Core](xref:core/testing/index) para obtener información general sobre las pruebas con la base de datos en memoria de EF.</span><span class="sxs-lookup"><span data-stu-id="23f50-110">See [Testing code that uses EF Core](xref:core/testing/index) for general information on testing with the EF in-memory database.</span></span>
* <span data-ttu-id="23f50-111">Vea el [ejemplo que muestra cómo probar las aplicaciones que usan EF Core](xref:core/testing/testing-sample) para obtener un ejemplo que usa la base de datos en memoria de EF.</span><span class="sxs-lookup"><span data-stu-id="23f50-111">See [Sample showing how to test applications that use EF Core](xref:core/testing/testing-sample) for a sample using the EF in-memory database.</span></span>
* <span data-ttu-id="23f50-112">Consulte [el proveedor de base de datos de EF en memoria](xref:core/providers/in-memory/index) para obtener información general sobre la base de datos en memoria de EF.</span><span class="sxs-lookup"><span data-stu-id="23f50-112">See [The EF in-memory database provider](xref:core/providers/in-memory/index) for general information about the EF in-memory database.</span></span>
