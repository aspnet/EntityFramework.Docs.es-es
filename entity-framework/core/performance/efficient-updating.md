---
title: 'Actualización eficaz: EF Core'
description: Guía de rendimiento para una actualización eficaz mediante Entity Framework Core
author: roji
ms.date: 12/1/2020
uid: core/performance/efficient-updating
ms.openlocfilehash: f8e222991af52cd7cae6089e95ad6634b6b949f8
ms.sourcegitcommit: 704240349e18b6404e5a809f5b7c9d365b152e2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/16/2021
ms.locfileid: "100543138"
---
# <a name="efficient-updating"></a><span data-ttu-id="9c102-103">Actualización eficaz</span><span class="sxs-lookup"><span data-stu-id="9c102-103">Efficient Updating</span></span>

## <a name="batching"></a><span data-ttu-id="9c102-104">Lotes</span><span class="sxs-lookup"><span data-stu-id="9c102-104">Batching</span></span>

<span data-ttu-id="9c102-105">EF Core ayuda a minimizar los viajes de ida y vuelta agrupando automáticamente todas las actualizaciones en un solo viaje de ida y vuelta.</span><span class="sxs-lookup"><span data-stu-id="9c102-105">EF Core helps minimize roundtrips by automatically batching together all updates in a single roundtrip.</span></span> <span data-ttu-id="9c102-106">Tenga en cuenta lo siguiente.</span><span class="sxs-lookup"><span data-stu-id="9c102-106">Consider the following:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#SaveChangesBatching)]

<span data-ttu-id="9c102-107">Lo anterior carga un blog de la base de datos, cambia su dirección URL y, a continuación, agrega dos blogs nuevos; para aplicar esto, se envían a la base de datos dos instrucciones INSERT de SQL y una instrucción UPDATE.</span><span class="sxs-lookup"><span data-stu-id="9c102-107">The above loads a blog from the database, changes its URL, and then adds two new blogs; to apply this, two SQL INSERT statements and one UPDATE statement are sent to the database.</span></span> <span data-ttu-id="9c102-108">En lugar de enviarlos uno a uno, a medida que se agregan instancias de blog, EF Core realiza un seguimiento de estos cambios internamente y los ejecuta en un solo ida y vuelta cuando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> se llama a.</span><span class="sxs-lookup"><span data-stu-id="9c102-108">Rather than sending them one by one, as Blog instances are added, EF Core tracks these changes internally, and executes them in a single roundtrip when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span>

<span data-ttu-id="9c102-109">El número de instrucciones que EF procesa por lotes en un único viaje de ida y vuelta depende del proveedor de base de datos utilizado.</span><span class="sxs-lookup"><span data-stu-id="9c102-109">The number of statements that EF batches in a single roundtrip depends on the database provider being used.</span></span> <span data-ttu-id="9c102-110">Por ejemplo, el análisis de rendimiento ha mostrado que el procesamiento por lotes sea menos eficaz para SQL Server cuando hay menos de 4 instrucciones implicadas.</span><span class="sxs-lookup"><span data-stu-id="9c102-110">For example, performance analysis has shown batching to be generally less efficient for SQL Server when less than 4 statements are involved.</span></span> <span data-ttu-id="9c102-111">Del mismo modo, las ventajas del procesamiento por lotes se degradan después de alrededor de 40 instrucciones para SQL Server, por lo que EF Core solo ejecutará de forma predeterminada hasta 42 instrucciones en un único lote y ejecutará instrucciones adicionales en viajes de ida y vuelta independientes.</span><span class="sxs-lookup"><span data-stu-id="9c102-111">Similarly, the benefits of batching degrade after around 40 statements for SQL Server, so EF Core will by default only execute up to 42 statements in a single batch, and execute additional statements in separate roundtrips.</span></span>

<span data-ttu-id="9c102-112">Los usuarios también pueden retocar estos umbrales para lograr un rendimiento potencialmente superior, pero con cuidado antes de modificarlos:</span><span class="sxs-lookup"><span data-stu-id="9c102-112">Users can also tweak these thresholds to achieve potentially higher performance - but benchmark carefully before modifying these:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/BatchTweakingContext.cs#BatchTweaking)]

## <a name="bulk-updates"></a><span data-ttu-id="9c102-113">Actualizaciones masivas</span><span class="sxs-lookup"><span data-stu-id="9c102-113">Bulk updates</span></span>

<span data-ttu-id="9c102-114">Supongamos que desea dar una elevación a todos los empleados.</span><span class="sxs-lookup"><span data-stu-id="9c102-114">Let's assume you want to give all your employees a raise.</span></span> <span data-ttu-id="9c102-115">Una implementación típica para esto en EF Core sería similar a la siguiente:</span><span class="sxs-lookup"><span data-stu-id="9c102-115">A typical implementation for this in EF Core would look like the following:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithoutBulk)]

<span data-ttu-id="9c102-116">Aunque se trata de código perfectamente válido, vamos a analizar lo que hace desde la perspectiva del rendimiento:</span><span class="sxs-lookup"><span data-stu-id="9c102-116">While this is perfectly valid code, let's analyze what it does from a performance perspective:</span></span>

* <span data-ttu-id="9c102-117">Se realiza un ida y vuelta de base de datos para cargar todos los empleados pertinentes. Tenga en cuenta que esto pone todos los datos de fila de los empleados en el cliente, incluso si solo se necesita el salario.</span><span class="sxs-lookup"><span data-stu-id="9c102-117">A database roundtrip is performed, to load all the relevant employees; note that this brings all the Employees' row data to the client, even if only the salary will be needed.</span></span>
* <span data-ttu-id="9c102-118">El seguimiento de cambios de EF Core crea instantáneas al cargar las entidades y, a continuación, compara esas instantáneas con las instancias para averiguar qué propiedades han cambiado.</span><span class="sxs-lookup"><span data-stu-id="9c102-118">EF Core's change tracking creates snapshots when loading the entities, and then compares those snapshots to the instances to find out which properties changed.</span></span>
* <span data-ttu-id="9c102-119">Se realiza un segundo viaje de base de datos para guardar todos los cambios.</span><span class="sxs-lookup"><span data-stu-id="9c102-119">A second database roundtrip is performed to save all the changes.</span></span> <span data-ttu-id="9c102-120">Aunque todos los cambios se realizan en un único viaje de ida y vuelta, el procesamiento por lotes EF Core sigue enviando una instrucción UPDATE por empleado, que debe ser ejecutada por la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9c102-120">While all changes are done in a single roundtrip thanks to batching, EF Core still sends an UPDATE statement per employee, which must be executed by the database.</span></span>

<span data-ttu-id="9c102-121">Las bases de datos relacionales también admiten *actualizaciones masivas*, por lo que lo anterior podría volver a escribirse como la siguiente instrucción SQL:</span><span class="sxs-lookup"><span data-stu-id="9c102-121">Relational databases also support *bulk updates*, so the above could be rewritten as the following single SQL statement:</span></span>

```sql
UPDATE [Employees] SET [Salary] = [Salary] + 1000;
```

<span data-ttu-id="9c102-122">Esto realiza la operación completa en un único ciclo de ida y vuelta, sin cargar ni enviar ningún dato real a la base de datos, y sin hacer uso de la maquinaria de seguimiento de cambios de EF, que impone una sobrecarga adicional.</span><span class="sxs-lookup"><span data-stu-id="9c102-122">This performs the entire operation in a single roundtrip, without loading or sending any actual data to the database, and without making use of EF's change tracking machinery, which imposes an additional overhead.</span></span>

<span data-ttu-id="9c102-123">Desafortunadamente, EF no proporciona actualmente las API para realizar actualizaciones masivas.</span><span class="sxs-lookup"><span data-stu-id="9c102-123">Unfortunately, EF doesn't currently provide APIs for performing bulk updates.</span></span> <span data-ttu-id="9c102-124">Hasta que se introduzcan, puede usar SQL sin procesar para realizar la operación en la que el rendimiento es sensible:</span><span class="sxs-lookup"><span data-stu-id="9c102-124">Until these are introduced, you can use raw SQL to perform the operation where performance is sensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Performance/Program.cs#UpdateWithBulk)]
