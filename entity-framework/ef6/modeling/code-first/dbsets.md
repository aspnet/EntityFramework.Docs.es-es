---
title: Definición de DbSets-EF6
description: Definición de DbSets en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 4528a509-ace7-4dfb-8065-1b833f5e03a0
uid: ef6/modeling/code-first/dbsets
ms.openlocfilehash: 55742c507e0d6b898a0cac18f34af84e736d771d
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617052"
---
# <a name="defining-dbsets"></a><span data-ttu-id="9c186-103">Definir DbSets</span><span class="sxs-lookup"><span data-stu-id="9c186-103">Defining DbSets</span></span>
<span data-ttu-id="9c186-104">Al desarrollar con el flujo de trabajo de Code First se define un DbContext derivado que representa la sesión con la base de datos y expone un DbSet para cada tipo del modelo.</span><span class="sxs-lookup"><span data-stu-id="9c186-104">When developing with the Code First workflow you define a derived DbContext that represents your session with the database and exposes a DbSet for each type in your model.</span></span> <span data-ttu-id="9c186-105">En este tema se tratan las distintas formas en que se pueden definir las propiedades de DbSet.</span><span class="sxs-lookup"><span data-stu-id="9c186-105">This topic covers the various ways you can define the DbSet properties.</span></span>  

## <a name="dbcontext-with-dbset-properties"></a><span data-ttu-id="9c186-106">DbContext con propiedades DbSet</span><span class="sxs-lookup"><span data-stu-id="9c186-106">DbContext with DbSet properties</span></span>  

<span data-ttu-id="9c186-107">El caso común que se muestra en Code First ejemplos es tener un DbContext con propiedades DbSet automáticas públicas para los tipos de entidad del modelo.</span><span class="sxs-lookup"><span data-stu-id="9c186-107">The common case shown in Code First examples is to have a DbContext with public automatic DbSet properties for the entity types of your model.</span></span> <span data-ttu-id="9c186-108">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9c186-108">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="9c186-109">Cuando se usa en modo de Code First, se configurarán los blogs y los envíos como tipos de entidad, así como la configuración de otros tipos accesibles desde ellos.</span><span class="sxs-lookup"><span data-stu-id="9c186-109">When used in Code First mode, this will configure Blogs and Posts as entity types, as well as configuring other types reachable from these.</span></span> <span data-ttu-id="9c186-110">Además, DbContext llamará automáticamente al establecedor para cada una de estas propiedades con el fin de establecer una instancia del DbSet adecuado.</span><span class="sxs-lookup"><span data-stu-id="9c186-110">In addition DbContext will automatically call the setter for each of these properties to set an instance of the appropriate DbSet.</span></span>  

## <a name="dbcontext-with-idbset-properties"></a><span data-ttu-id="9c186-111">DbContext con propiedades IDbSet</span><span class="sxs-lookup"><span data-stu-id="9c186-111">DbContext with IDbSet properties</span></span>  

<span data-ttu-id="9c186-112">Existen situaciones, como cuando se crean simulacros o simulaciones, donde resulta más útil declarar las propiedades set mediante una interfaz.</span><span class="sxs-lookup"><span data-stu-id="9c186-112">There are situations, such as when creating mocks or fakes, where it is more useful to declare your set properties using an interface.</span></span> <span data-ttu-id="9c186-113">En tales casos, se puede usar la interfaz IDbSet en lugar de DbSet.</span><span class="sxs-lookup"><span data-stu-id="9c186-113">In such cases the IDbSet interface can be used in place of DbSet.</span></span> <span data-ttu-id="9c186-114">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9c186-114">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

<span data-ttu-id="9c186-115">Este contexto funciona exactamente de la misma manera que el contexto que usa la clase DbSet para sus propiedades set.</span><span class="sxs-lookup"><span data-stu-id="9c186-115">This context works in exactly the same way as the context that uses the DbSet class for its set properties.</span></span>  

## <a name="dbcontext-with-read-only-set-properties"></a><span data-ttu-id="9c186-116">DbContext con propiedades set de solo lectura</span><span class="sxs-lookup"><span data-stu-id="9c186-116">DbContext with read-only set properties</span></span>  

<span data-ttu-id="9c186-117">Si no desea exponer establecedores públicos para las propiedades DbSet o IDbSet, puede crear propiedades de solo lectura y crear las instancias de conjunto.</span><span class="sxs-lookup"><span data-stu-id="9c186-117">If you do not wish to expose public setters for your DbSet or IDbSet properties you can instead create read-only properties and create the set instances yourself.</span></span> <span data-ttu-id="9c186-118">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="9c186-118">For example:</span></span>  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs
    {
        get { return Set<Blog>(); }
    }

    public DbSet<Post> Posts
    {
        get { return Set<Post>(); }
    }
}
```  

<span data-ttu-id="9c186-119">Tenga en cuenta que DbContext almacena en caché la instancia de DbSet devuelta por el método Set para que cada una de estas propiedades devuelva la misma instancia cada vez que se llame a.</span><span class="sxs-lookup"><span data-stu-id="9c186-119">Note that DbContext caches the instance of DbSet returned from the Set method so that each of these properties will return the same instance every time it is called.</span></span>  

<span data-ttu-id="9c186-120">La detección de tipos de entidad para Code First funciona de la misma manera que en las propiedades con captadores y establecedores públicos.</span><span class="sxs-lookup"><span data-stu-id="9c186-120">Discovery of entity types for Code First works in the same way here as it does for properties with public getters and setters.</span></span>  
