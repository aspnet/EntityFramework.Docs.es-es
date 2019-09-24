---
title: 'Restricciones Foreign Key: EF Core'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: dbaf4bac-1fd5-46c0-ac57-64d7153bc574
uid: core/modeling/relational/fk-constraints
ms.openlocfilehash: d7ed4466f4df9ec01267b048ba1bbcc6e8bbdad5
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197062"
---
# <a name="foreign-key-constraints"></a><span data-ttu-id="05793-102">Restricciones Foreign Key</span><span class="sxs-lookup"><span data-stu-id="05793-102">Foreign Key Constraints</span></span>

> [!NOTE]  
> <span data-ttu-id="05793-103">La configuración de esta sección se aplica a bases de datos relacionales en general.</span><span class="sxs-lookup"><span data-stu-id="05793-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="05793-104">Los métodos de extensión que se muestran a continuación estarán disponibles cuando instale un proveedor de base de datos relacional (debido al paquete compartido *Microsoft.EntityFrameworkCore.Relational*).</span><span class="sxs-lookup"><span data-stu-id="05793-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="05793-105">Se introduce una restricción FOREIGN KEY para cada relación del modelo.</span><span class="sxs-lookup"><span data-stu-id="05793-105">A foreign key constraint is introduced for each relationship in the model.</span></span>

## <a name="conventions"></a><span data-ttu-id="05793-106">Convenciones</span><span class="sxs-lookup"><span data-stu-id="05793-106">Conventions</span></span>

<span data-ttu-id="05793-107">Por Convención, las restricciones Foreign Key se denominan `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span><span class="sxs-lookup"><span data-stu-id="05793-107">By convention, foreign key constraints are named `FK_<dependent type name>_<principal type name>_<foreign key property name>`.</span></span> <span data-ttu-id="05793-108">En el caso de `<foreign key property name>` las claves externas compuestas se convierte en una lista de nombres de propiedades de clave externa separadas por guiones bajos.</span><span class="sxs-lookup"><span data-stu-id="05793-108">For composite foreign keys `<foreign key property name>` becomes an underscore separated list of foreign key property names.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="05793-109">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="05793-109">Data Annotations</span></span>

<span data-ttu-id="05793-110">Los nombres de restricción de clave externa no se pueden configurar con anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="05793-110">Foreign key constraint names cannot be configured using data annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="05793-111">API fluida</span><span class="sxs-lookup"><span data-stu-id="05793-111">Fluent API</span></span>

<span data-ttu-id="05793-112">Puede usar la API fluida para configurar el nombre de la restricción de clave externa para una relación.</span><span class="sxs-lookup"><span data-stu-id="05793-112">You can use the Fluent API to configure the foreign key constraint name for a relationship.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/RelationshipConstraintName.cs?highlight=12)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasOne(p => p.Blog)
            .WithMany(b => b.Posts)
            .HasForeignKey(p => p.BlogId)
            .HasConstraintName("ForeignKey_Post_Blog");
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

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
```
