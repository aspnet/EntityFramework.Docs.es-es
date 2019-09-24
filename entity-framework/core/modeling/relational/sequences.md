---
title: 'Secuencias: EF Core'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 94f81a92-3c72-4e14-912a-f99310374e42
uid: core/modeling/relational/sequences
ms.openlocfilehash: ce02b9840e58102a60c1d8eacf6810365104d7d7
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/23/2019
ms.locfileid: "71196907"
---
# <a name="sequences"></a><span data-ttu-id="4be68-102">Secuencias</span><span class="sxs-lookup"><span data-stu-id="4be68-102">Sequences</span></span>

> [!NOTE]  
> <span data-ttu-id="4be68-103">La configuración de esta sección se aplica a bases de datos relacionales en general.</span><span class="sxs-lookup"><span data-stu-id="4be68-103">The configuration in this section is applicable to relational databases in general.</span></span> <span data-ttu-id="4be68-104">Los métodos de extensión que se muestran a continuación estarán disponibles cuando instale un proveedor de base de datos relacional (debido al paquete compartido *Microsoft.EntityFrameworkCore.Relational*).</span><span class="sxs-lookup"><span data-stu-id="4be68-104">The extension methods shown here will become available when you install a relational database provider (due to the shared *Microsoft.EntityFrameworkCore.Relational* package).</span></span>

<span data-ttu-id="4be68-105">Una secuencia genera valores numéricos secuenciales en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="4be68-105">A sequence generates a sequential numeric values in the database.</span></span> <span data-ttu-id="4be68-106">Las secuencias no están asociadas a una tabla específica.</span><span class="sxs-lookup"><span data-stu-id="4be68-106">Sequences are not associated with a specific table.</span></span>

## <a name="conventions"></a><span data-ttu-id="4be68-107">Convenciones</span><span class="sxs-lookup"><span data-stu-id="4be68-107">Conventions</span></span>

<span data-ttu-id="4be68-108">Por Convención, las secuencias no se incluyen en el modelo.</span><span class="sxs-lookup"><span data-stu-id="4be68-108">By convention, sequences are not introduced in to the model.</span></span>

## <a name="data-annotations"></a><span data-ttu-id="4be68-109">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="4be68-109">Data Annotations</span></span>

<span data-ttu-id="4be68-110">No se puede configurar una secuencia mediante anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="4be68-110">You can not configure a sequence using Data Annotations.</span></span>

## <a name="fluent-api"></a><span data-ttu-id="4be68-111">API fluida</span><span class="sxs-lookup"><span data-stu-id="4be68-111">Fluent API</span></span>

<span data-ttu-id="4be68-112">Puede usar la API fluida para crear una secuencia en el modelo.</span><span class="sxs-lookup"><span data-stu-id="4be68-112">You can use the Fluent API to create a sequence in the model.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/Sequence.cs?highlight=7)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasSequence<int>("OrderNumbers");
    }
}

public class Order
{
    public int OrderId { get; set; }
    public int OrderNo { get; set; }
    public string Url { get; set; }
}
```

<span data-ttu-id="4be68-113">También puede configurar el aspecto adicional de la secuencia, como su esquema, valor inicial e incremento.</span><span class="sxs-lookup"><span data-stu-id="4be68-113">You can also configure additional aspect of the sequence, such as its schema, start value, and increment.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/SequenceConfigured.cs?highlight=7,8,9)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasSequence<int>("OrderNumbers", schema: "shared")
            .StartsAt(1000)
            .IncrementsBy(5);
    }
}
```

<span data-ttu-id="4be68-114">Una vez que se introduce una secuencia, puede usarla para generar valores para las propiedades del modelo.</span><span class="sxs-lookup"><span data-stu-id="4be68-114">Once a sequence is introduced, you can use it to generate values for properties in your model.</span></span> <span data-ttu-id="4be68-115">Por ejemplo, puede usar [los valores predeterminados](default-values.md) para insertar el siguiente valor de la secuencia.</span><span class="sxs-lookup"><span data-stu-id="4be68-115">For example, you can use [Default Values](default-values.md) to insert the next value from the sequence.</span></span>

<!-- [!code-csharp[Main](samples/core/relational/Modeling/FluentAPI/Relational/SequenceUsed.cs?highlight=11,12,13)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Order> Orders { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.HasSequence<int>("OrderNumbers", schema: "shared")
            .StartsAt(1000)
            .IncrementsBy(5);

        modelBuilder.Entity<Order>()
            .Property(o => o.OrderNo)
            .HasDefaultValueSql("NEXT VALUE FOR shared.OrderNumbers");
    }
}

public class Order
{
    public int OrderId { get; set; }
    public int OrderNo { get; set; }
    public string Url { get; set; }
}
```
