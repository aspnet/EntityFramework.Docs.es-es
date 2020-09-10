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
# <a name="the-load-method"></a>El método de carga
Hay varios escenarios en los que puede que desee cargar entidades de la base de datos en el contexto sin hacer nada inmediatamente con esas entidades. Un buen ejemplo es la carga de entidades para el enlace de datos como se describe en [datos locales](xref:ef6/querying/local-data). Una forma habitual de hacerlo es escribir una consulta LINQ y, a continuación, llamar a ToList en ella, solo para descartar inmediatamente la lista creada. El método de extensión de carga funciona igual que ToList, salvo que evita la creación de la lista por completo.  

Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.  

A continuación se muestran dos ejemplos del uso de load. La primera se toma de un Windows Forms aplicación de enlace de datos donde la carga se utiliza para consultar las entidades antes de enlazarse a la colección local, como se describe en [datos locales](xref:ef6/querying/local-data):  

``` csharp
protected override void OnLoad(EventArgs e)
{
    base.OnLoad(e);

    _context = new ProductContext();

    _context.Categories.Load();
    categoryBindingSource.DataSource = _context.Categories.Local.ToBindingList();
}
```  

En el segundo ejemplo se muestra el uso de LOAD para cargar una colección filtrada de entidades relacionadas, como se describe en [carga de entidades relacionadas](xref:ef6/querying/related-data):  

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
