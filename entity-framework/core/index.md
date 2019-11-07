---
title: Información general de Entity Framework Core - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: e6127f775d6bbbdf81debf5519388fe252fe079d
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655622"
---
# <a name="entity-framework-core"></a><span data-ttu-id="b8dca-102">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="b8dca-102">Entity Framework Core</span></span>

<span data-ttu-id="b8dca-103">Entity Framework (EF) Core es una versión ligera, extensible, [de código abierto](https://github.com/aspnet/EntityFrameworkCore) y multiplataforma de la popular tecnología de acceso a datos Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="b8dca-103">Entity Framework (EF) Core is a lightweight, extensible, [open source](https://github.com/aspnet/EntityFrameworkCore) and cross-platform version of the popular Entity Framework data access technology.</span></span>

<span data-ttu-id="b8dca-104">EF Core puede servir como asignador relacional de objetos (O/RM), lo que permite a los desarrolladores de .NET trabajar con una base de datos mediante objetos .NET y eliminar la mayoría del código de acceso a los datos que normalmente deben escribir.</span><span class="sxs-lookup"><span data-stu-id="b8dca-104">EF Core can serve as an object-relational mapper (O/RM), enabling .NET developers to work with a database using .NET objects, and eliminating the need for most of the data-access code they usually need to write.</span></span>

<span data-ttu-id="b8dca-105">EF Core es compatible con muchos motores de base de datos; vea [Proveedores de bases de datos](providers/index.md) para más información.</span><span class="sxs-lookup"><span data-stu-id="b8dca-105">EF Core supports many database engines, see [Database Providers](providers/index.md) for details.</span></span>

## <a name="the-model"></a><span data-ttu-id="b8dca-106">El modelo</span><span class="sxs-lookup"><span data-stu-id="b8dca-106">The Model</span></span>

<span data-ttu-id="b8dca-107">Con EF Core, el acceso a datos se realiza mediante un modelo.</span><span class="sxs-lookup"><span data-stu-id="b8dca-107">With EF Core, data access is performed using a model.</span></span> <span data-ttu-id="b8dca-108">Un modelo se compone de clases de entidad y un objeto de contexto que representa una sesión con la base de datos, lo que permite consultar y guardar los datos.</span><span class="sxs-lookup"><span data-stu-id="b8dca-108">A model is made up of entity classes and a context object that represents a session with the database, allowing you to query and save data.</span></span> <span data-ttu-id="b8dca-109">Vea [Creación de un modelo](modeling/index.md) para más información.</span><span class="sxs-lookup"><span data-stu-id="b8dca-109">See [Creating a Model](modeling/index.md) to learn more.</span></span>

<span data-ttu-id="b8dca-110">Puede generar un modelo a partir de una base de datos existente, codificar manualmente un modelo para que coincida con la base de datos o usar [migraciones de EF](managing-schemas/migrations/index.md) para crear una base de datos a partir del modelo y que evolucione a medida que cambia el modelo.</span><span class="sxs-lookup"><span data-stu-id="b8dca-110">You can generate a model from an existing database, hand code a model to match your database, or use [EF Migrations](managing-schemas/migrations/index.md) to create a database from your model, and then evolve it as your model changes over time.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a><span data-ttu-id="b8dca-111">Consultas</span><span class="sxs-lookup"><span data-stu-id="b8dca-111">Querying</span></span>

<span data-ttu-id="b8dca-112">Las instancias de las clases de entidad se recuperan de la base de datos mediante Language Integrated Query (LINQ).</span><span class="sxs-lookup"><span data-stu-id="b8dca-112">Instances of your entity classes are retrieved from the database using Language Integrated Query (LINQ).</span></span> <span data-ttu-id="b8dca-113">Vea [Consulta de datos](querying/index.md) para más información.</span><span class="sxs-lookup"><span data-stu-id="b8dca-113">See [Querying Data](querying/index.md) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a><span data-ttu-id="b8dca-114">Guardado de datos</span><span class="sxs-lookup"><span data-stu-id="b8dca-114">Saving Data</span></span>

<span data-ttu-id="b8dca-115">Los datos se crean, se eliminan y se modifican en la base de datos mediante instancias de las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="b8dca-115">Data is created, deleted, and modified in the database using instances of your entity classes.</span></span> <span data-ttu-id="b8dca-116">Vea [Guardado de datos](saving/index.md) para más información.</span><span class="sxs-lookup"><span data-stu-id="b8dca-116">See [Saving Data](saving/index.md) to learn more.</span></span>

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a><span data-ttu-id="b8dca-117">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="b8dca-117">Next steps</span></span>

<span data-ttu-id="b8dca-118">Para consultar tutoriales de introducción, vea [Introducción a Entity Framework Core](get-started/index.md).</span><span class="sxs-lookup"><span data-stu-id="b8dca-118">For introductory tutorials, see [Getting Started with Entity Framework Core](get-started/index.md).</span></span>
