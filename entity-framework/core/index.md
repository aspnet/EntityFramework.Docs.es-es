---
title: Descripción general - EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: 0e35a2b3f89c92b717b8e05c8fa3ae5af5ce8fd3
ms.sourcegitcommit: 06073f8efde97dd5f540dbfb69f574d8380566fe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2019
ms.locfileid: "67333775"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core es una versión ligera, extensible, [de código abierto](https://github.com/aspnet/EntityFrameworkCore) y multiplataforma de la popular tecnología de acceso a datos Entity Framework.

EF Core puede servir como asignador relacional de objetos (O/RM), lo que permite a los desarrolladores de .NET trabajar con una base de datos mediante objetos .NET y eliminar la mayoría del código de acceso a los datos que normalmente deben escribir.

EF Core es compatible con muchos motores de base de datos; vea [Proveedores de bases de datos](providers/index.md) para más información.

## <a name="the-model"></a>El modelo

Con EF Core, el acceso a datos se realiza mediante un modelo. Un modelo se compone de clases de entidad y un objeto de contexto que representa una sesión con la base de datos, lo que permite consultar y guardar los datos. Vea [Creación de un modelo](modeling/index.md) para más información.

Puede generar un modelo a partir de una base de datos existente, codificar manualmente un modelo para que coincida con la base de datos o usar [migraciones de EF](managing-schemas/migrations/index.md) para crear una base de datos a partir del modelo y que evolucione a medida que cambia el modelo.

``` csharp
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;

namespace Intro
{
    public class BloggingContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }

        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        {
            optionsBuilder.UseSqlServer(
                @"Server=(localdb)\mssqllocaldb;Database=Blogging;Integrated Security=True");
        }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Url { get; set; }
        public int Rating { get; set; }
        public List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public Blog Blog { get; set; }
    }
}
```

## <a name="querying"></a>Consultas

Las instancias de las clases de entidad se recuperan de la base de datos mediante Language Integrated Query (LINQ). Vea [Consulta de datos](querying/index.md) para más información.

``` csharp
using (var db = new BloggingContext())
{
    var blogs = db.Blogs
        .Where(b => b.Rating > 3)
        .OrderBy(b => b.Url)
        .ToList();
}
```

## <a name="saving-data"></a>Guardado de datos

Los datos se crean, se eliminan y se modifican en la base de datos mediante instancias de las clases de entidad. Vea [Guardado de datos](saving/index.md) para más información.

``` csharp
using (var db = new BloggingContext())
{
    var blog = new Blog { Url = "http://sample.com" };
    db.Blogs.Add(blog);
    db.SaveChanges();
}
```

## <a name="next-steps"></a>Pasos siguientes

Para consultar tutoriales de introducción, vea [Introducción a Entity Framework Core](get-started/index.md).

