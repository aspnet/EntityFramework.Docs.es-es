---
title: El método Load-EF6
description: El método Load en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 03c5a069-b7b4-455f-a16f-ee3b96cc4e28
uid: ef6/querying/load-method
ms.openlocfilehash: 5fbb55b899ae0ee026d42df90f80cc18fe95bfa2
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620319"
---
# <a name="the-load-method"></a><span data-ttu-id="84659-103">El método de carga</span><span class="sxs-lookup"><span data-stu-id="84659-103">The Load Method</span></span>
<span data-ttu-id="84659-104">Hay varios escenarios en los que puede que desee cargar entidades de la base de datos en el contexto sin hacer nada inmediatamente con esas entidades.</span><span class="sxs-lookup"><span data-stu-id="84659-104">There are several scenarios where you may want to load entities from the database into the context without immediately doing anything with those entities.</span></span> <span data-ttu-id="84659-105">Un buen ejemplo es la carga de entidades para el enlace de datos como se describe en [datos locales](xref:ef6/querying/local-data).</span><span class="sxs-lookup"><span data-stu-id="84659-105">A good example of this is loading entities for data binding as described in [Local Data](xref:ef6/querying/local-data).</span></span> <span data-ttu-id="84659-106">Una forma habitual de hacerlo es escribir una consulta LINQ y, a continuación, llamar a ToList en ella, solo para descartar inmediatamente la lista creada.</span><span class="sxs-lookup"><span data-stu-id="84659-106">One common way to do this is to write a LINQ query and then call ToList on it, only to immediately discard the created list.</span></span> <span data-ttu-id="84659-107">El método de extensión de carga funciona igual que ToList, salvo que evita la creación de la lista por completo.</span><span class="sxs-lookup"><span data-stu-id="84659-107">The Load extension method works just like ToList except that it avoids the creation of the list altogether.</span></span>  

<span data-ttu-id="84659-108">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="84659-108">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="84659-109">A continuación se muestran dos ejemplos del uso de load.</span><span class="sxs-lookup"><span data-stu-id="84659-109">Here are two examples of using Load.</span></span> <span data-ttu-id="84659-110">La primera se toma de un Windows Forms aplicación de enlace de datos donde la carga se utiliza para consultar las entidades antes de enlazarse a la colección local, como se describe en [datos locales](xref:ef6/querying/local-data):</span><span class="sxs-lookup"><span data-stu-id="84659-110">The first is taken from a Windows Forms data binding application where Load is used to query for entities before binding to the local collection, as described in [Local Data](xref:ef6/querying/local-data):</span></span>  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

<span data-ttu-id="84659-111">En el segundo ejemplo se muestra el uso de LOAD para cargar una colección filtrada de entidades relacionadas, como se describe en [carga de entidades relacionadas](xref:ef6/querying/related-data):</span><span class="sxs-lookup"><span data-stu-id="84659-111">The second example shows using Load to load a filtered collection of related entities, as described in [Loading Related Entities](xref:ef6/querying/related-data):</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Load the posts with the 'entity-framework' tag related to a given blog
    context.Entry(blog)
        .Collection(b => b.Posts)
        .Query()
        .Where(p => p.Tags.Contains("entity-framework"))
        .Load();
}
```  
