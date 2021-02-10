---
title: 'Consultas únicas frente a consultas divididas: EF Core'
description: Traducción de consultas en consultas únicas y divididas en SQL con Entity Framework Core
author: smitpatel
ms.date: 10/03/2019
uid: core/querying/single-split-queries
ms.openlocfilehash: 1857e05d45979c5530a349b9c7470407e7574fdf
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983435"
---
# <a name="split-queries"></a><span data-ttu-id="552eb-103">Consultas divididas</span><span class="sxs-lookup"><span data-stu-id="552eb-103">Split queries</span></span>

## <a name="single-queries"></a><span data-ttu-id="552eb-104">Consultas únicas</span><span class="sxs-lookup"><span data-stu-id="552eb-104">Single queries</span></span>

<span data-ttu-id="552eb-105">En las bases de datos relacionales, todas las entidades relacionadas se cargan mediante la introducción de instrucciones JOIN en consultas únicas.</span><span class="sxs-lookup"><span data-stu-id="552eb-105">In relational databases, all related entities are loaded by introducing JOINs in single query.</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

<span data-ttu-id="552eb-106">Si un blog típico tiene varias entradas relacionadas, las filas de estas entradas duplicarán la información del blog,</span><span class="sxs-lookup"><span data-stu-id="552eb-106">If a typical blog has multiple related posts, rows for these posts will duplicate the blog's information.</span></span> <span data-ttu-id="552eb-107">lo que genera un problema conocido como "explosión cartesiana".</span><span class="sxs-lookup"><span data-stu-id="552eb-107">This duplication leads to the so-called "cartesian explosion" problem.</span></span> <span data-ttu-id="552eb-108">A medida que se cargan más relaciones uno a varios, la cantidad de datos duplicados puede crecer y afectar negativamente al rendimiento de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="552eb-108">As more one-to-many relationships are loaded, the amount of duplicated data may grow and adversely affect the performance of your application.</span></span>

## <a name="split-queries"></a><span data-ttu-id="552eb-109">Consultas divididas</span><span class="sxs-lookup"><span data-stu-id="552eb-109">Split queries</span></span>

> [!NOTE]
> <span data-ttu-id="552eb-110">Esta característica se incluyó por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="552eb-110">This feature was introduced in EF Core 5.0.</span></span> <span data-ttu-id="552eb-111">Solo funciona cuando se usa `Include`.</span><span class="sxs-lookup"><span data-stu-id="552eb-111">It only works when using `Include`.</span></span> <span data-ttu-id="552eb-112">[Este problema](https://github.com/dotnet/efcore/issues/21234) realiza un seguimiento de la compatibilidad con la consulta dividida al cargar datos relacionados en la proyección sin `Include`.</span><span class="sxs-lookup"><span data-stu-id="552eb-112">[This issue](https://github.com/dotnet/efcore/issues/21234) is tracking support for split query when loading related data in projection without `Include`.</span></span>

<span data-ttu-id="552eb-113">EF le permite especificar que una consulta LINQ determinada se debe *dividir* en varias consultas SQL.</span><span class="sxs-lookup"><span data-stu-id="552eb-113">EF allows you to specify that a given LINQ query should be *split* into multiple SQL queries.</span></span> <span data-ttu-id="552eb-114">En lugar de instrucciones JOIN, las consultas divididas generan una consulta SQL adicional por cada navegación de colección incluida:</span><span class="sxs-lookup"><span data-stu-id="552eb-114">Instead of JOINs, split queries generate an additional SQL query for each included collection navigation:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

<span data-ttu-id="552eb-115">Generará la consulta SQL siguiente:</span><span class="sxs-lookup"><span data-stu-id="552eb-115">It will produce the following SQL:</span></span>

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> <span data-ttu-id="552eb-116">Las entidades relacionadas uno a uno se cargan siempre mediante instrucciones JOIN en la misma consulta, ya que esto no afecta al rendimiento.</span><span class="sxs-lookup"><span data-stu-id="552eb-116">One-to-one related entities are always loaded via JOINs in the same query, as it has no performance impact.</span></span>

## <a name="enabling-split-queries-globally"></a><span data-ttu-id="552eb-117">Habilitación de consultas divididas globalmente</span><span class="sxs-lookup"><span data-stu-id="552eb-117">Enabling split queries globally</span></span>

<span data-ttu-id="552eb-118">También puede configurar consultas divididas como el valor predeterminado para el contexto de la aplicación:</span><span class="sxs-lookup"><span data-stu-id="552eb-118">You can also configure split queries as the default for your application's context:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

<span data-ttu-id="552eb-119">Cuando las consultas divididas están configuradas como valor predeterminado, todavía es posible configurar consultas específicas para que se ejecuten como consultas únicas:</span><span class="sxs-lookup"><span data-stu-id="552eb-119">When split queries are configured as the default, it's still possible to configure specific queries to execute as single queries:</span></span>

[!code-csharp[Main](../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

<span data-ttu-id="552eb-120">EF Core usa el modo de consulta única de forma predeterminada si no existe ninguna configuración.</span><span class="sxs-lookup"><span data-stu-id="552eb-120">EF Core uses single query mode by default in the absence of any configuration.</span></span> <span data-ttu-id="552eb-121">Dado que puede producir problemas de rendimiento, EF Core genera una advertencia cuando se cumplen las condiciones siguientes:</span><span class="sxs-lookup"><span data-stu-id="552eb-121">Since it may cause performance issues, EF Core generates a warning whenever following conditions are met:</span></span>

- <span data-ttu-id="552eb-122">EF Core detecta que la consulta carga varias colecciones.</span><span class="sxs-lookup"><span data-stu-id="552eb-122">EF Core detects that the query loads multiple collections.</span></span>
- <span data-ttu-id="552eb-123">El usuario no ha configurado el modo de división de consultas globalmente.</span><span class="sxs-lookup"><span data-stu-id="552eb-123">User hasn't configured query splitting mode globally.</span></span>
- <span data-ttu-id="552eb-124">El usuario no ha usado el operador `AsSingleQuery`/`AsSplitQuery` en la consulta.</span><span class="sxs-lookup"><span data-stu-id="552eb-124">User hasn't used `AsSingleQuery`/`AsSplitQuery` operator on the query.</span></span>

<span data-ttu-id="552eb-125">Para desactivar la advertencia, configure el modo de división de consultas globalmente o en el nivel de consulta en un valor adecuado.</span><span class="sxs-lookup"><span data-stu-id="552eb-125">To turn off the warning, configure query splitting mode globally or at the query level to an appropriate value.</span></span>

## <a name="characteristics-of-split-queries"></a><span data-ttu-id="552eb-126">Características de las consultas divididas</span><span class="sxs-lookup"><span data-stu-id="552eb-126">Characteristics of split queries</span></span>

<span data-ttu-id="552eb-127">Si bien las consultas divididas evitan las incidencias de rendimiento asociadas a las instrucciones JOIN y la explosión cartesiana, también existen algunas desventajas:</span><span class="sxs-lookup"><span data-stu-id="552eb-127">While split query avoids the performance issues associated with JOINs and cartesian explosion, it also has some drawbacks:</span></span>

- <span data-ttu-id="552eb-128">Aunque la mayoría de las bases de datos garantizan la coherencia de los datos en las consultas únicas, no sucede lo mismo en el caso de varias consultas.</span><span class="sxs-lookup"><span data-stu-id="552eb-128">While most databases guarantee data consistency for single queries, no such guarantees exist for multiple queries.</span></span> <span data-ttu-id="552eb-129">Si la base de datos se actualiza de forma simultánea al ejecutar las consultas, es posible que los datos resultantes no sean coherentes.</span><span class="sxs-lookup"><span data-stu-id="552eb-129">If the database is updated concurrently when executing your queries, resulting data may not be consistent.</span></span> <span data-ttu-id="552eb-130">Esto se puede mitigar mediante el encapsulado de las consultas en una transacción serializable o de instantáneas, aunque esto puede generar incidencias propias de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="552eb-130">You can mitigate it by wrapping the queries in a serializable or snapshot transaction, although doing so may create performance issues of its own.</span></span> <span data-ttu-id="552eb-131">Para obtener más información, vea la documentación de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="552eb-131">For more information, see your database's documentation.</span></span>
- <span data-ttu-id="552eb-132">Cada consulta implica actualmente un ciclo adicional de ida y vuelta de red a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="552eb-132">Each query currently implies an additional network roundtrip to your database.</span></span> <span data-ttu-id="552eb-133">Varios ciclos de ida y vuelta de red pueden degradar el rendimiento, sobre todo si la latencia en la base de datos es alta (por ejemplo, servicios en la nube).</span><span class="sxs-lookup"><span data-stu-id="552eb-133">Multiple network roundtrips can degrade performance, especially where latency to the database is high (for example, cloud services).</span></span>
- <span data-ttu-id="552eb-134">Aunque algunas bases de datos permiten el consumo de los resultados de varias consultas al mismo tiempo (SQL Server con MARS, SQLite), la mayoría de ellas solo permiten sola consulta activa en un momento dado.</span><span class="sxs-lookup"><span data-stu-id="552eb-134">While some databases allow consuming the results of multiple queries at the same time (SQL Server with MARS, Sqlite), most allow only a single query to be active at any given point.</span></span> <span data-ttu-id="552eb-135">Por lo tanto, todos los resultados de las consultas anteriores deben almacenarse en búfer en la memoria de la aplicación antes de ejecutar consultas posteriores, lo que llevará a un aumento en los requisitos de memoria.</span><span class="sxs-lookup"><span data-stu-id="552eb-135">So all results from earlier queries must be buffered in your application's memory before executing later queries, which leads to increased memory requirements.</span></span>

<span data-ttu-id="552eb-136">Por desgracia, no hay una estrategia para cargar entidades relacionadas que se ajuste a todos los escenarios.</span><span class="sxs-lookup"><span data-stu-id="552eb-136">Unfortunately, there isn't one strategy for loading related entities that fits all scenarios.</span></span> <span data-ttu-id="552eb-137">Tenga en cuenta las ventajas y desventajas de las consultas únicas y divididas, para seleccionar la que mejor se ajuste a sus necesidades.</span><span class="sxs-lookup"><span data-stu-id="552eb-137">Carefully consider the advantages and disadvantages of single and split queries to select the one that fits your needs.</span></span>
