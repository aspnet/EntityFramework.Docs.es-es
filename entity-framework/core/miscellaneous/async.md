---
title: Programación asincrónica-EF Core
description: Consulta y almacenamiento de datos de forma asincrónica con Entity Framework Core
author: roji
ms.date: 9/2/2020
uid: core/miscellaneous/async
ms.openlocfilehash: cefbe32b34a38ed6d749ef3ddfff210d5db12332
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071905"
---
# <a name="asynchronous-programming"></a><span data-ttu-id="eba8d-103">Programación asincrónica</span><span class="sxs-lookup"><span data-stu-id="eba8d-103">Asynchronous Programming</span></span>

<span data-ttu-id="eba8d-104">Las operaciones asincrónicas evitan el bloqueo de un subproceso mientras la consulta se ejecuta en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="eba8d-104">Asynchronous operations avoid blocking a thread while the query is executed in the database.</span></span> <span data-ttu-id="eba8d-105">Las operaciones asincrónicas son importantes para mantener una interfaz de usuario receptiva en las aplicaciones cliente enriquecidas y también pueden aumentar el rendimiento en las aplicaciones web donde liberan el subproceso para atender otras solicitudes en aplicaciones Web.</span><span class="sxs-lookup"><span data-stu-id="eba8d-105">Async operations are important for keeping a responsive UI in rich client applications, and can also increase throughput in web applications where they free up the thread to service other requests in web applications.</span></span>

<span data-ttu-id="eba8d-106">A continuación de .NET Standard, EF Core proporciona homólogos asincrónicos a todos los métodos sincrónicos que realizan operaciones de e/s.</span><span class="sxs-lookup"><span data-stu-id="eba8d-106">Following the .NET standard, EF Core provides asynchronous counterparts to all synchronous methods which perform I/O.</span></span> <span data-ttu-id="eba8d-107">Tienen los mismos efectos que los métodos de sincronización y se pueden usar con las `async` `await` palabras clave y C#.</span><span class="sxs-lookup"><span data-stu-id="eba8d-107">These have the same effects as the sync methods, and can be used with the C# `async` and `await` keywords.</span></span> <span data-ttu-id="eba8d-108">Por ejemplo, en lugar de usar DbContext. SaveChanges, que bloqueará un subproceso mientras se realiza la e/s de la base de datos, se puede usar DbContext. SaveChangesAsync:</span><span class="sxs-lookup"><span data-stu-id="eba8d-108">For example, instead of using DbContext.SaveChanges, which will block a thread while database I/O is performed, DbContext.SaveChangesAsync can be used:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#SaveChangesAsync)]

<span data-ttu-id="eba8d-109">Para obtener más información, vea [los documentos generales de programación asincrónica de C#](/dotnet/csharp/async).</span><span class="sxs-lookup"><span data-stu-id="eba8d-109">For more information, see [the general C# asynchronous programming docs](/dotnet/csharp/async).</span></span>

> [!WARNING]
> <span data-ttu-id="eba8d-110">EF Core no admite que varias operaciones en paralelo se ejecuten en la misma instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="eba8d-110">EF Core doesn't support multiple parallel operations being run on the same context instance.</span></span> <span data-ttu-id="eba8d-111">Siempre debe esperar que se complete una operación antes de iniciar la siguiente.</span><span class="sxs-lookup"><span data-stu-id="eba8d-111">You should always wait for an operation to complete before beginning the next operation.</span></span> <span data-ttu-id="eba8d-112">Habitualmente, para esto se usa la palabra clave `await` en cada una de las operaciones asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="eba8d-112">This is typically done by using the `await` keyword on each async operation.</span></span>

> [!NOTE]
> <span data-ttu-id="eba8d-113">EF Core pasa los tokens de cancelación al proveedor de base de datos subyacente en uso (por ejemplo, Microsoft. Data. SqlClient).</span><span class="sxs-lookup"><span data-stu-id="eba8d-113">EF Core passes cancellation tokens down to the underlying database provider in use (e.g. Microsoft.Data.SqlClient).</span></span> <span data-ttu-id="eba8d-114">Estos tokens pueden o no ser respetados; consulte la documentación del proveedor de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="eba8d-114">These tokens may or may not be honored - consult your database provider's documentation.</span></span>  

## <a name="async-linq-operators"></a><span data-ttu-id="eba8d-115">Operadores LINQ asincrónicos</span><span class="sxs-lookup"><span data-stu-id="eba8d-115">Async LINQ operators</span></span>

<span data-ttu-id="eba8d-116">Para permitir la ejecución de consultas LINQ de forma asincrónica, EF Core proporciona un conjunto de métodos de extensión Async que ejecutan la consulta y devuelven resultados.</span><span class="sxs-lookup"><span data-stu-id="eba8d-116">In order to support executing LINQ queries asynchronously, EF Core provides a set of async extension methods which execute the query and return results.</span></span> <span data-ttu-id="eba8d-117">Estos homólogos a los operadores de LINQ estándar y sincrónicos son ToListAsync, SingleAsync, AsAsyncEnumerable, etc.:</span><span class="sxs-lookup"><span data-stu-id="eba8d-117">These counterparts to the standard, synchronous LINQ operators include ToListAsync, SingleAsync, AsAsyncEnumerable, etc.:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Async/Program.cs#ToListAsync)]

<span data-ttu-id="eba8d-118">Tenga en cuenta que no hay versiones asincrónicas de algunos operadores LINQ, como Where o OrderBy, porque solo compilan el árbol de expresión LINQ y no hacen que la consulta se ejecute en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="eba8d-118">Note that there are no async versions of some LINQ operators such as Where or OrderBy, because these only build up the LINQ expression tree and don't cause the query to be executed in the database.</span></span> <span data-ttu-id="eba8d-119">Solo los operadores que causan la ejecución de la consulta tienen homólogos asincrónicos.</span><span class="sxs-lookup"><span data-stu-id="eba8d-119">Only operators which cause query execution have async counterparts.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="eba8d-120">Los métodos de extensión asincrónicos de EF Core se define en el espacio de nombres `Microsoft.EntityFrameworkCore`.</span><span class="sxs-lookup"><span data-stu-id="eba8d-120">The EF Core async extension methods are defined in the `Microsoft.EntityFrameworkCore` namespace.</span></span> <span data-ttu-id="eba8d-121">Es necesario importar este espacio de nombres para que los métodos estén disponibles.</span><span class="sxs-lookup"><span data-stu-id="eba8d-121">This namespace must be imported for the methods to be available.</span></span>

## <a name="client-side-async-linq-operators"></a><span data-ttu-id="eba8d-122">Operadores LINQ asincrónicos del lado cliente</span><span class="sxs-lookup"><span data-stu-id="eba8d-122">Client-side async LINQ operators</span></span>

<span data-ttu-id="eba8d-123">Los operadores LINQ Async descritos anteriormente solo se pueden usar en consultas EF: no se pueden usar con consultas LINQ to Objects del lado cliente.</span><span class="sxs-lookup"><span data-stu-id="eba8d-123">The async LINQ operators discussed above can only be used on EF queries - you cannot use them with client-side LINQ to Objects query.</span></span> <span data-ttu-id="eba8d-124">Para realizar operaciones LINQ asincrónicas en el lado cliente fuera de EF, use el [paquete System. Interactive. Async](https://www.nuget.org/packages/System.Interactive.Async); Este paquete puede ser especialmente útil para realizar operaciones en el cliente que no se pueden traducir para su evaluación en el servidor.</span><span class="sxs-lookup"><span data-stu-id="eba8d-124">To perform client-side async LINQ operations outside of EF, use the [System.Interactive.Async package](https://www.nuget.org/packages/System.Interactive.Async); this package can be especially useful for performing operations on the client that cannot be translated for evaluation at the server.</span></span>

<span data-ttu-id="eba8d-125">Desafortunadamente, la referencia a System. Interactive. Async produce errores de compilación de invocación ambigua en los operadores de LINQ aplicados al DbSets de EF; Esto hace que sea difícil usar tanto EF como System. Interactive. Async en el mismo proyecto.</span><span class="sxs-lookup"><span data-stu-id="eba8d-125">Unfortunately, referencing System.Interactive.Async causes ambiguous invocation compilation errors on LINQ operators applied to EF's DbSets; this makes it hard to use both EF and System.Interactive.Async in the same project.</span></span> <span data-ttu-id="eba8d-126">Para solucionar este problema, agregue la función de consulta a su DbSet:</span><span class="sxs-lookup"><span data-stu-id="eba8d-126">To work around this issue, add AsQueryable to your DbSet:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/AsyncWithSystemInteractive/Program.cs#SystemInteractiveAsync)]
