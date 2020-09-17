---
title: 'Guardado básico: EF Core'
description: Información básica sobre cómo agregar, actualizar y eliminar datos con Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/basic
ms.openlocfilehash: ea73ff86d8843acf8226f9d6696b79b99da9dab7
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070957"
---
# <a name="basic-save"></a><span data-ttu-id="c5e7d-103">Guardado básico</span><span class="sxs-lookup"><span data-stu-id="c5e7d-103">Basic Save</span></span>

<span data-ttu-id="c5e7d-104">Obtenga información sobre cómo agregar, modificar y quitar datos mediante las clases de entidad y contexto.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-104">Learn how to add, modify, and remove data using your context and entity classes.</span></span>

> [!TIP]  
> <span data-ttu-id="c5e7d-105">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-105">You can view this article's [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Basics/) on GitHub.</span></span>

## <a name="adding-data"></a><span data-ttu-id="c5e7d-106">Agregar datos</span><span class="sxs-lookup"><span data-stu-id="c5e7d-106">Adding Data</span></span>

<span data-ttu-id="c5e7d-107">Use el método *DbSet.Add* para agregar instancias nuevas de las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-107">Use the *DbSet.Add* method to add new instances of your entity classes.</span></span> <span data-ttu-id="c5e7d-108">Los datos se insertarán en la base de datos cuando llame a *SaveChanges*.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-108">The data will be inserted in the database when you call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> <span data-ttu-id="c5e7d-109">Los métodos Add, Attach y Update funcionan en todo el grafo de entidades que se pasaron a ellos, tal como se describe en la sección de [datos relacionados](xref:core/saving/related-data).</span><span class="sxs-lookup"><span data-stu-id="c5e7d-109">The Add, Attach, and Update methods all work on the full graph of entities passed to them, as described in the [Related Data](xref:core/saving/related-data) section.</span></span> <span data-ttu-id="c5e7d-110">También puede usar la propiedad EntityEntry.State para establecer el estado de una sola unidad.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-110">Alternately, the EntityEntry.State property can be used to set the state of just a single entity.</span></span> <span data-ttu-id="c5e7d-111">Por ejemplo, `context.Entry(blog).State = EntityState.Modified`.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-111">For example, `context.Entry(blog).State = EntityState.Modified`.</span></span>

## <a name="updating-data"></a><span data-ttu-id="c5e7d-112">Actualización de datos</span><span class="sxs-lookup"><span data-stu-id="c5e7d-112">Updating Data</span></span>

<span data-ttu-id="c5e7d-113">EF detectará automáticamente los cambios hechos en una entidad existente de la que hace seguimiento el contexto.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-113">EF will automatically detect changes made to an existing entity that is tracked by the context.</span></span> <span data-ttu-id="c5e7d-114">Esto incluye entidades que se cargan o consultan desde la base de datos y las entidades que se agregaron y guardaron anteriormente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-114">This includes entities that you load/query from the database, and entities that were previously added and saved to the database.</span></span>

<span data-ttu-id="c5e7d-115">Solo debe modificar los valores asignados a las propiedades y llamar a *SaveChanges*.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-115">Simply modify the values assigned to properties and then call *SaveChanges*.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a><span data-ttu-id="c5e7d-116">Eliminar datos</span><span class="sxs-lookup"><span data-stu-id="c5e7d-116">Deleting Data</span></span>

<span data-ttu-id="c5e7d-117">Use el método *DbSet.Remove* para eliminar las instancias de las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-117">Use the *DbSet.Remove* method to delete instances of your entity classes.</span></span>

<span data-ttu-id="c5e7d-118">Si la entidad ya existe en la base de datos, se eliminará durante *SaveChanges*.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-118">If the entity already exists in the database, it will be deleted during *SaveChanges*.</span></span> <span data-ttu-id="c5e7d-119">Si la entidad todavía no se guarda en la base de datos (es decir, si se hace seguimiento cuando se agrega), se quitará del contexto y ya no se insertará cuando se llame a *SaveChanges*.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-119">If the entity has not yet been saved to the database (that is, it is tracked as added) then it will be removed from the context and will no longer be inserted when *SaveChanges* is called.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a><span data-ttu-id="c5e7d-120">Varias operaciones en una única llamada a SaveChanges</span><span class="sxs-lookup"><span data-stu-id="c5e7d-120">Multiple Operations in a single SaveChanges</span></span>

<span data-ttu-id="c5e7d-121">Puede combinar varias operaciones Add, Update y Remove en una sola llamada a *SaveChanges*.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-121">You can combine multiple Add/Update/Remove operations into a single call to *SaveChanges*.</span></span>

> [!NOTE]  
> <span data-ttu-id="c5e7d-122">Para la mayoría de los proveedores de base de datos, *SaveChanges* es transaccional.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-122">For most database providers, *SaveChanges* is transactional.</span></span> <span data-ttu-id="c5e7d-123">Esto significa que todas las operaciones se realizarán correctamente o presentarán un error y que nunca se aplicarán de manera parcial.</span><span class="sxs-lookup"><span data-stu-id="c5e7d-123">This means  all the operations will either succeed or fail and the operations will never be left partially applied.</span></span>

[!code-csharp[Main](../../../samples/core/Saving/Basics/Sample.cs#MultipleOperations)]
