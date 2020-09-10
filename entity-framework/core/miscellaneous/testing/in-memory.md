---
title: 'Prueba con la base de datos de EF en memoria: EF Core'
description: Uso de la base de datos de EF en memoria para probar una aplicación Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 353f67fb0e78fefa74fc77d302e505bacb692ed4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619401"
---
# <a name="testing-with-the-ef-in-memory-database"></a><span data-ttu-id="b6d15-103">Pruebas con la base de datos en memoria de EF</span><span class="sxs-lookup"><span data-stu-id="b6d15-103">Testing with the EF In-Memory Database</span></span>

> [!WARNING]
> <span data-ttu-id="b6d15-104">La base de datos en memoria de EF a menudo se comporta de forma diferente a las bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="b6d15-104">The EF in-memory database often behaves differently than relational databases.</span></span>
> <span data-ttu-id="b6d15-105">Use la base de datos en memoria de EF solo después de comprender totalmente los problemas y las ventajas y desventajas que conlleva, como se describe en [probar el código que usa EF Core](xref:core/miscellaneous/testing/index).</span><span class="sxs-lookup"><span data-stu-id="b6d15-105">Only use the EF in-memory database after fully understanding the issues and trade-offs involved, as discussed in [Testing code that uses EF Core](xref:core/miscellaneous/testing/index).</span></span>  

> [!TIP]
> <span data-ttu-id="b6d15-106">SQLite es un proveedor relacional y también puede usar bases de datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="b6d15-106">SQLite is a relational provider and can also use in-memory databases.</span></span>
> <span data-ttu-id="b6d15-107">Considere la posibilidad de utilizarlo para realizar pruebas con el fin de encontrar más coincidencia con los comportamientos comunes de la base de datos</span><span class="sxs-lookup"><span data-stu-id="b6d15-107">Consider using this for testing to more closely match common relational database behaviors.</span></span>
> <span data-ttu-id="b6d15-108">Esto se trata en el [uso de SQLite para probar una aplicación EF Core](xref:core/miscellaneous/testing/sqlite).</span><span class="sxs-lookup"><span data-stu-id="b6d15-108">This is covered in [Using SQLite to test an EF Core application](xref:core/miscellaneous/testing/sqlite).</span></span>   

<span data-ttu-id="b6d15-109">La información de esta página se encuentra ahora en otras ubicaciones:</span><span class="sxs-lookup"><span data-stu-id="b6d15-109">The information on this page now lives in other locations:</span></span>
* <span data-ttu-id="b6d15-110">Vea [código de prueba que usa EF Core](xref:core/miscellaneous/testing/index) para obtener información general sobre las pruebas con la base de datos en memoria de EF.</span><span class="sxs-lookup"><span data-stu-id="b6d15-110">See [Testing code that uses EF Core](xref:core/miscellaneous/testing/index) for general information on testing with the EF in-memory database.</span></span>
* <span data-ttu-id="b6d15-111">Vea el [ejemplo que muestra cómo probar las aplicaciones que usan EF Core](xref:core/miscellaneous/testing/testing-sample) para obtener un ejemplo que usa la base de datos en memoria de EF.</span><span class="sxs-lookup"><span data-stu-id="b6d15-111">See [Sample showing how to test applications that use EF Core](xref:core/miscellaneous/testing/testing-sample) for a sample using the EF in-memory database.</span></span>
* <span data-ttu-id="b6d15-112">Consulte [el proveedor de base de datos de EF en memoria](xref:core/providers/in-memory/index) para obtener información general sobre la base de datos en memoria de EF.</span><span class="sxs-lookup"><span data-stu-id="b6d15-112">See [The EF in-memory database provider](xref:core/providers/in-memory/index) for general information about the EF in-memory database.</span></span>
