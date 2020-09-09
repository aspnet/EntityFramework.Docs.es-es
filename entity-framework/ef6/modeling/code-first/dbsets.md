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
# <a name="defining-dbsets"></a>Definir DbSets
Al desarrollar con el flujo de trabajo de Code First se define un DbContext derivado que representa la sesión con la base de datos y expone un DbSet para cada tipo del modelo. En este tema se tratan las distintas formas en que se pueden definir las propiedades de DbSet.  

## <a name="dbcontext-with-dbset-properties"></a>DbContext con propiedades DbSet  

El caso común que se muestra en Code First ejemplos es tener un DbContext con propiedades DbSet automáticas públicas para los tipos de entidad del modelo. Por ejemplo:  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

Cuando se usa en modo de Code First, se configurarán los blogs y los envíos como tipos de entidad, así como la configuración de otros tipos accesibles desde ellos. Además, DbContext llamará automáticamente al establecedor para cada una de estas propiedades con el fin de establecer una instancia del DbSet adecuado.  

## <a name="dbcontext-with-idbset-properties"></a>DbContext con propiedades IDbSet  

Existen situaciones, como cuando se crean simulacros o simulaciones, donde resulta más útil declarar las propiedades set mediante una interfaz. En tales casos, se puede usar la interfaz IDbSet en lugar de DbSet. Por ejemplo:  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

Este contexto funciona exactamente de la misma manera que el contexto que usa la clase DbSet para sus propiedades set.  

## <a name="dbcontext-with-read-only-set-properties"></a>DbContext con propiedades set de solo lectura  

Si no desea exponer establecedores públicos para las propiedades DbSet o IDbSet, puede crear propiedades de solo lectura y crear las instancias de conjunto. Por ejemplo:  

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

Tenga en cuenta que DbContext almacena en caché la instancia de DbSet devuelta por el método Set para que cada una de estas propiedades devuelva la misma instancia cada vez que se llame a.  

La detección de tipos de entidad para Code First funciona de la misma manera que en las propiedades con captadores y establecedores públicos.  
