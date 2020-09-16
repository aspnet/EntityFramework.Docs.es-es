---
title: 'Funcionamiento de las consultas: EF Core'
description: Información general sobre cómo Entity Framework Core compila y ejecuta consultas de forma interna
author: ajcvickers
ms.date: 03/17/2020
ms.assetid: de2e34cd-659b-4cab-b5ed-7a979c6bf120
uid: core/querying/how-query-works
ms.openlocfilehash: e0e489f5f797b6f7d8f4860539a538dba90bd1c2
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616370"
---
# <a name="how-queries-work"></a><span data-ttu-id="9b030-103">Funcionamiento de las consultas</span><span class="sxs-lookup"><span data-stu-id="9b030-103">How Queries Work</span></span>

<span data-ttu-id="9b030-104">Entity Framework Core usa Language Integrated Query (LINQ) para consultar datos de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9b030-104">Entity Framework Core uses Language Integrated Query (LINQ) to query data from the database.</span></span> <span data-ttu-id="9b030-105">LINQ permite usar C# (o el lenguaje .NET que prefiera) para escribir consultas fuertemente tipadas basadas en el contexto derivado y las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="9b030-105">LINQ allows you to use C# (or your .NET language of choice) to write strongly typed queries based on your derived context and entity classes.</span></span>

## <a name="the-life-of-a-query"></a><span data-ttu-id="9b030-106">La duración de una consulta</span><span class="sxs-lookup"><span data-stu-id="9b030-106">The life of a query</span></span>

<span data-ttu-id="9b030-107">La descripción siguiente es información general de alto nivel del proceso por el que pasa toda consulta.</span><span class="sxs-lookup"><span data-stu-id="9b030-107">The following description is a high-level overview of the process each query goes through.</span></span>

1. <span data-ttu-id="9b030-108">Entity Framework Core procesa la consulta LINQ para crear una representación que está lista para que el proveedor de base de datos la procese.</span><span class="sxs-lookup"><span data-stu-id="9b030-108">The LINQ query is processed by Entity Framework Core to build a representation that is ready to be processed by the database provider</span></span>
   1. <span data-ttu-id="9b030-109">El resultado se almacena en caché para que no sea necesario hacer este procesamiento cada vez que se ejecuta la consulta.</span><span class="sxs-lookup"><span data-stu-id="9b030-109">The result is cached so that this processing does not need to be done every time the query is executed</span></span>
2. <span data-ttu-id="9b030-110">El resultado se pasa al proveedor de base de datos.</span><span class="sxs-lookup"><span data-stu-id="9b030-110">The result is passed to the database provider</span></span>
   1. <span data-ttu-id="9b030-111">El proveedor de base de datos identifica qué partes de la consulta se pueden evaluar en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="9b030-111">The database provider identifies which parts of the query can be evaluated in the database</span></span>
   2. <span data-ttu-id="9b030-112">Estas partes de la consulta se traducen al lenguaje de la consulta específico de la base de datos (por ejemplo, SQL para una base de datos relacional).</span><span class="sxs-lookup"><span data-stu-id="9b030-112">These parts of the query are translated to database-specific query language (for example, SQL for a relational database)</span></span>
   3. <span data-ttu-id="9b030-113">Se envía una consulta a la base de datos y se devuelve el conjunto de resultados (los resultados son valores de la base de datos y no instancias de entidad).</span><span class="sxs-lookup"><span data-stu-id="9b030-113">A query is sent to the database and the result set returned (results are values from the database, not entity instances)</span></span>
3. <span data-ttu-id="9b030-114">Para cada elemento del conjunto de resultados</span><span class="sxs-lookup"><span data-stu-id="9b030-114">For each item in the result set</span></span>
   1. <span data-ttu-id="9b030-115">Si se trata de una consulta con seguimiento, EF comprueba si los datos representan una entidad que ya existe en la herramienta de seguimiento de cambios para la instancia de contexto.</span><span class="sxs-lookup"><span data-stu-id="9b030-115">If the query is a tracking query, EF checks if the data represents an entity already in the change tracker for the context instance</span></span>
      * <span data-ttu-id="9b030-116">Si es así, se devuelve la entidad existente.</span><span class="sxs-lookup"><span data-stu-id="9b030-116">If so, the existing entity is returned</span></span>
      * <span data-ttu-id="9b030-117">En caso contrario, se crea una entidad nueva, se configura el seguimiento de cambios y se devuelve la entidad nueva.</span><span class="sxs-lookup"><span data-stu-id="9b030-117">If not, a new entity is created, change tracking is set up, and the new entity is returned</span></span>
   2. <span data-ttu-id="9b030-118">Si se trata de una consulta que no es de seguimiento, siempre se crea y devuelve una entidad nueva.</span><span class="sxs-lookup"><span data-stu-id="9b030-118">If the query is a no-tracking query, then a new entity is always created and returned</span></span>

## <a name="when-queries-are-executed"></a><span data-ttu-id="9b030-119">Cuándo se ejecutan las consultas</span><span class="sxs-lookup"><span data-stu-id="9b030-119">When queries are executed</span></span>

<span data-ttu-id="9b030-120">Cuando llama a los operadores LINQ, simplemente crea una representación de la consulta en memoria.</span><span class="sxs-lookup"><span data-stu-id="9b030-120">When you call LINQ operators, you're simply building up an in-memory representation of the query.</span></span> <span data-ttu-id="9b030-121">La consulta solo se envía a la base de datos cuando se usan los resultados.</span><span class="sxs-lookup"><span data-stu-id="9b030-121">The query is only sent to the database when the results are consumed.</span></span>

<span data-ttu-id="9b030-122">Las operaciones más comunes que generan que la consulta se envíe a la base de datos son:</span><span class="sxs-lookup"><span data-stu-id="9b030-122">The most common operations that result in the query being sent to the database are:</span></span>

* <span data-ttu-id="9b030-123">La iteración de los resultados en un bucle `for`</span><span class="sxs-lookup"><span data-stu-id="9b030-123">Iterating the results in a `for` loop</span></span>
* <span data-ttu-id="9b030-124">Uso de operadores como `ToList`, `ToArray`, `Single` y `Count`, o sobrecargas asincrónicas equivalentes</span><span class="sxs-lookup"><span data-stu-id="9b030-124">Using an operator such as `ToList`, `ToArray`, `Single`, `Count`, or the equivalent async overloads</span></span>

> [!WARNING]  
> <span data-ttu-id="9b030-125">**Valide siempre la entrada del usuario:** aunque EF Core protege contra los ataques por inyección de código SQL con el uso de parámetros y el escape de cadenas literales en consultas, no valida las entradas.</span><span class="sxs-lookup"><span data-stu-id="9b030-125">**Always validate user input:** While EF Core protects against SQL injection attacks by using parameters and escaping literals in queries, it does not validate inputs.</span></span> <span data-ttu-id="9b030-126">Se debe realizar una validación apropiada, según los requisitos de la aplicación, antes de que los valores de los orígenes que no son de confianza se usen en consultas LINQ, se asignen a las propiedades de una entidad o se pasen a otras API de EF Core.</span><span class="sxs-lookup"><span data-stu-id="9b030-126">Appropriate validation, per the application's requirements, should be performed before values from un-trusted sources are used in LINQ queries, assigned to entity properties, or passed to other EF Core APIs.</span></span> <span data-ttu-id="9b030-127">Esto incluye cualquier intervención del usuario que se use para construir consultas de manera dinámica.</span><span class="sxs-lookup"><span data-stu-id="9b030-127">This includes any user input used to dynamically construct queries.</span></span> <span data-ttu-id="9b030-128">Incluso al usar LINQ, si acepta la intervención del usuario para crear expresiones, necesita garantizar que solo se pueden construir las expresiones previstas.</span><span class="sxs-lookup"><span data-stu-id="9b030-128">Even when using LINQ, if you are accepting user input to build expressions, you need to make sure that only intended expressions can be constructed.</span></span>
