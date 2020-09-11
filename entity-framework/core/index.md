---
title: Información general de Entity Framework Core - EF Core
description: Información general de Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: 4816f2d57590ddd2ad4807daa2cc1c5000cd5713
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619487"
---
# <a name="entity-framework-core"></a><span data-ttu-id="0d3fa-103">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="0d3fa-103">Entity Framework Core</span></span>

<span data-ttu-id="0d3fa-104">Entity Framework (EF) Core es una versión ligera, extensible, [de código abierto](https://github.com/aspnet/EntityFrameworkCore) y multiplataforma de la popular tecnología de acceso a datos Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="0d3fa-104">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="0d3fa-105">EF Core puede servir como asignador relacional de objetos (O/RM), lo que permite a los desarrolladores de .NET trabajar con una base de datos mediante objetos .NET y eliminar la mayoría del código de acceso a los datos que normalmente deben escribir.</span><span class="sxs-lookup"><span data-stu-id="0d3fa-105">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="0d3fa-106">EF Core es compatible con muchos motores de base de datos; vea [Proveedores de bases de datos](xref:core/providers/index) para más información.</span><span class="sxs-lookup"><span data-stu-id="0d3fa-106">EF Core supports many database engines, see [Database Providers](xref:core/providers/index) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="0d3fa-107">El modelo</span><span class="sxs-lookup"><span data-stu-id="0d3fa-107">The Model</span></span>

<span data-ttu-id="0d3fa-108">Con EF Core, el acceso a datos se realiza mediante un modelo.</span><span class="sxs-lookup"><span data-stu-id="0d3fa-108">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="0d3fa-109">Un modelo se compone de clases de entidad y un objeto de contexto que representa una sesión con la base de datos, lo que permite consultar y guardar los datos.</span><span class="sxs-lookup"><span data-stu-id="0d3fa-109">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="0d3fa-110">Vea [Creación de un modelo](xref:core/modeling/index) para más información.</span><span class="sxs-lookup"><span data-stu-id="0d3fa-110">See [Creating a Model](xref:core/modeling/index) to learn more.</span></span>

<span data-ttu-id="0d3fa-111">Puede generar un modelo a partir de una base de datos existente, codificar manualmente un modelo para que coincida con la base de datos o usar [migraciones de EF](xref:core/managing-schemas/migrations/index) para crear una base de datos a partir del modelo y que evolucione a medida que cambia el modelo.</span><span class="sxs-lookup"><span data-stu-id="0d3fa-111">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](xref:core/managing-schemas/migrations/index) to create a database from your model, and then evolve it as your model changes over time.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a><span data-ttu-id="0d3fa-112">Consultas</span><span class="sxs-lookup"><span data-stu-id="0d3fa-112">Querying</span></span>

<span data-ttu-id="0d3fa-113">Las instancias de las clases de entidad se recuperan de la base de datos mediante Language Integrated Query (LINQ).</span><span class="sxs-lookup"><span data-stu-id="0d3fa-113">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="0d3fa-114">Vea [Consulta de datos](xref:core/querying/index) para más información.</span><span class="sxs-lookup"><span data-stu-id="0d3fa-114">See [Querying Data](xref:core/querying/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a><span data-ttu-id="0d3fa-115">Guardado de datos</span><span class="sxs-lookup"><span data-stu-id="0d3fa-115">Saving Data</span></span>

<span data-ttu-id="0d3fa-116">Los datos se crean, se eliminan y se modifican en la base de datos mediante instancias de las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="0d3fa-116">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="0d3fa-117">Vea [Guardado de datos](xref:core/saving/index) para más información.</span><span class="sxs-lookup"><span data-stu-id="0d3fa-117">See [Saving Data](xref:core/saving/index) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a><span data-ttu-id="0d3fa-118">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="0d3fa-118">Next steps</span></span>

<span data-ttu-id="0d3fa-119">Para consultar tutoriales de introducción, vea [Introducción a Entity Framework Core](xref:core/get-started/index).</span><span class="sxs-lookup"><span data-stu-id="0d3fa-119">For introductory tutorials, see [Getting Started with Entity Framework Core](xref:core/get-started/index).</span></span>
