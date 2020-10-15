---
title: 'Herencia: EF Core'
description: Cómo configurar la herencia de tipos de entidad mediante Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/inheritance
ms.openlocfilehash: 47aae0d57d7203f0e6da5868bdc082ad85d59620
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063873"
---
# <a name="inheritance"></a><span data-ttu-id="6cd11-103">Herencia</span><span class="sxs-lookup"><span data-stu-id="6cd11-103">Inheritance</span></span>

<span data-ttu-id="6cd11-104">EF puede asignar una jerarquía de tipos .NET a una base de datos.</span><span class="sxs-lookup"><span data-stu-id="6cd11-104">EF can map a .NET type hierarchy to a database.</span></span> <span data-ttu-id="6cd11-105">Esto le permite escribir las entidades .NET en el código como de costumbre, con los tipos base y derivados, y hacer que EF cree sin problemas el esquema de base de datos adecuado, las consultas de problemas, etc. Los detalles reales de cómo se asigna una jerarquía de tipos dependen del proveedor; en esta página se describe la compatibilidad de herencia en el contexto de una base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="6cd11-105">This allows you to write your .NET entities in code as usual, using base and derived types, and have EF seamlessly create the appropriate database schema, issue queries, etc. The actual details of how a type hierarchy is mapped are provider-dependent; this page describes inheritance support in the context of a relational database.</span></span>

## <a name="entity-type-hierarchy-mapping"></a><span data-ttu-id="6cd11-106">Asignación de jerarquía de tipos de entidad</span><span class="sxs-lookup"><span data-stu-id="6cd11-106">Entity type hierarchy mapping</span></span>

<span data-ttu-id="6cd11-107">Por Convención, EF no buscará automáticamente tipos base o derivados; Esto significa que si desea que se asigne un tipo CLR en la jerarquía, debe especificar explícitamente ese tipo en el modelo.</span><span class="sxs-lookup"><span data-stu-id="6cd11-107">By convention, EF will not automatically scan for base or derived types; this means that if you want a CLR type in your hierarchy to be mapped, you must explicitly specify that type on your model.</span></span> <span data-ttu-id="6cd11-108">Por ejemplo, si solo se especifica el tipo base de una jerarquía, EF Core incluirá implícitamente todos sus subtipos.</span><span class="sxs-lookup"><span data-stu-id="6cd11-108">For example, specifying only the base type of a hierarchy will not cause EF Core to implicitly include all of its sub-types.</span></span>

<span data-ttu-id="6cd11-109">En el ejemplo siguiente se expone un DbSet para `Blog` y su subclase `RssBlog` .</span><span class="sxs-lookup"><span data-stu-id="6cd11-109">The following sample exposes a DbSet for `Blog` and its subclass `RssBlog`.</span></span> <span data-ttu-id="6cd11-110">Si `Blog` tiene cualquier otra subclase, no se incluirá en el modelo.</span><span class="sxs-lookup"><span data-stu-id="6cd11-110">If `Blog` has any other subclass, it will not be included in the model.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/InheritanceDbSets.cs?name=InheritanceDbSets&highlight=3-4)]

> [!NOTE]
> <span data-ttu-id="6cd11-111">Las columnas de la base de datos se convierten en NULL automáticamente según sea necesario al usar la asignación TPH.</span><span class="sxs-lookup"><span data-stu-id="6cd11-111">Database columns are automatically made nullable as necessary when using TPH mapping.</span></span> <span data-ttu-id="6cd11-112">Por ejemplo, la `RssUrl` columna acepta valores NULL porque `Blog` las instancias normales no tienen esa propiedad.</span><span class="sxs-lookup"><span data-stu-id="6cd11-112">For example, the `RssUrl` column is nullable because regular `Blog` instances do not have that property.</span></span>

<span data-ttu-id="6cd11-113">Si no desea exponer un `DbSet` para una o más entidades de la jerarquía, también puede usar la API fluida para asegurarse de que se incluyen en el modelo.</span><span class="sxs-lookup"><span data-stu-id="6cd11-113">If you don't want to expose a `DbSet` for one or more entities in the hierarchy, you can also use the Fluent API to ensure they are included in the model.</span></span>

> [!TIP]
> <span data-ttu-id="6cd11-114">Si no se basa en las convenciones, puede especificar el tipo base explícitamente mediante `HasBaseType` .</span><span class="sxs-lookup"><span data-stu-id="6cd11-114">If you don't rely on conventions, you can specify the base type explicitly using `HasBaseType`.</span></span> <span data-ttu-id="6cd11-115">También puede usar `.HasBaseType((Type)null)` para quitar un tipo de entidad de la jerarquía.</span><span class="sxs-lookup"><span data-stu-id="6cd11-115">You can also use `.HasBaseType((Type)null)` to remove an entity type from the hierarchy.</span></span>

## <a name="table-per-hierarchy-and-discriminator-configuration"></a><span data-ttu-id="6cd11-116">Configuración de tabla por jerarquía y discriminador</span><span class="sxs-lookup"><span data-stu-id="6cd11-116">Table-per-hierarchy and discriminator configuration</span></span>

<span data-ttu-id="6cd11-117">De forma predeterminada, EF asigna la herencia mediante el patrón *de tabla por jerarquía* (TPH).</span><span class="sxs-lookup"><span data-stu-id="6cd11-117">By default, EF maps the inheritance using the *table-per-hierarchy* (TPH) pattern.</span></span> <span data-ttu-id="6cd11-118">TPH usa una sola tabla para almacenar los datos de todos los tipos de la jerarquía y se usa una columna de discriminador para identificar qué tipo representa cada fila.</span><span class="sxs-lookup"><span data-stu-id="6cd11-118">TPH uses a single table to store the data for all types in the hierarchy, and a discriminator column is used to identify which type each row represents.</span></span>

<span data-ttu-id="6cd11-119">El modelo anterior se asigna al siguiente esquema de la base de datos (tenga en cuenta la columna creado implícitamente `Discriminator` , que identifica qué tipo de `Blog` se almacena en cada fila).</span><span class="sxs-lookup"><span data-stu-id="6cd11-119">The model above is mapped to the following database schema (note the implicitly-created `Discriminator` column, which identifies which type of `Blog` is stored in each row).</span></span>

![imagen](_static/inheritance-tph-data.png)

<span data-ttu-id="6cd11-121">Puede configurar el nombre y el tipo de la columna discriminadora y los valores que se usan para identificar cada tipo en la jerarquía:</span><span class="sxs-lookup"><span data-stu-id="6cd11-121">You can configure the name and type of the discriminator column and the values that are used to identify each type in the hierarchy:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorConfiguration.cs?name=DiscriminatorConfiguration&highlight=4-6)]

<span data-ttu-id="6cd11-122">En los ejemplos anteriores, EF agregó el discriminador implícitamente como una [propiedad Shadow](xref:core/modeling/shadow-properties) en la entidad base de la jerarquía.</span><span class="sxs-lookup"><span data-stu-id="6cd11-122">In the examples above, EF added the discriminator implicitly as a [shadow property](xref:core/modeling/shadow-properties) on the base entity of the hierarchy.</span></span> <span data-ttu-id="6cd11-123">Esta propiedad se puede configurar como cualquier otra:</span><span class="sxs-lookup"><span data-stu-id="6cd11-123">This property can be configured like any other:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/DiscriminatorPropertyConfiguration.cs?name=DiscriminatorPropertyConfiguration&highlight=4-5)]

<span data-ttu-id="6cd11-124">Por último, el discriminador también se puede asignar a una propiedad .NET normal en la entidad:</span><span class="sxs-lookup"><span data-stu-id="6cd11-124">Finally, the discriminator can also be mapped to a regular .NET property in your entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/NonShadowDiscriminator.cs?name=NonShadowDiscriminator&highlight=4)]

### <a name="shared-columns"></a><span data-ttu-id="6cd11-125">Columnas compartidas</span><span class="sxs-lookup"><span data-stu-id="6cd11-125">Shared columns</span></span>

<span data-ttu-id="6cd11-126">De forma predeterminada, cuando dos tipos de entidad del mismo nivel en la jerarquía tienen una propiedad con el mismo nombre, se asignarán a dos columnas independientes.</span><span class="sxs-lookup"><span data-stu-id="6cd11-126">By default, when two sibling entity types in the hierarchy have a property with the same name, they will be mapped to two separate columns.</span></span> <span data-ttu-id="6cd11-127">Sin embargo, si su tipo es idéntico, se pueden asignar a la misma columna de base de datos:</span><span class="sxs-lookup"><span data-stu-id="6cd11-127">However, if their type is identical they can be mapped to the same database column:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedTPHColumns.cs?name=SharedTPHColumns&highlight=9,13)]

## <a name="table-per-type-configuration"></a><span data-ttu-id="6cd11-128">Configuración de tabla por tipo</span><span class="sxs-lookup"><span data-stu-id="6cd11-128">Table-per-type configuration</span></span>

> [!NOTE]
> <span data-ttu-id="6cd11-129">La tabla por tipo (TPT) es una nueva característica de EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="6cd11-129">The table-per-type (TPT) is a new feature in EF Core 5.0.</span></span> <span data-ttu-id="6cd11-130">La tabla por tipo específico (TPC) es compatible con EF6, pero aún no es compatible con EF Core.</span><span class="sxs-lookup"><span data-stu-id="6cd11-130">Table-per-concrete-type (TPC) is supported by EF6, but is not yet supported by EF Core.</span></span>

<span data-ttu-id="6cd11-131">En el patrón de asignación de TPT, todos los tipos se asignan a tablas individuales.</span><span class="sxs-lookup"><span data-stu-id="6cd11-131">In the TPT mapping pattern, all the types are mapped to individual tables.</span></span> <span data-ttu-id="6cd11-132">Las propiedades que pertenecen solamente a un tipo base o a un tipo derivado se almacenan en una tabla que se asigna a ese tipo.</span><span class="sxs-lookup"><span data-stu-id="6cd11-132">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="6cd11-133">Las tablas que se asignan a tipos derivados también almacenan una clave externa que une la tabla derivada con la tabla base.</span><span class="sxs-lookup"><span data-stu-id="6cd11-133">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=TPTConfiguration)]

<span data-ttu-id="6cd11-134">EF creará el siguiente esquema de la base de datos para el modelo anterior.</span><span class="sxs-lookup"><span data-stu-id="6cd11-134">EF will create the following database schema for the model above.</span></span>

```sql
CREATE TABLE [Blogs] (
    [BlogId] int NOT NULL IDENTITY,
    [Url] nvarchar(max) NULL,
    CONSTRAINT [PK_Blogs] PRIMARY KEY ([BlogId])
);

CREATE TABLE [RssBlogs] (
    [BlogId] int NOT NULL,
    [RssUrl] nvarchar(max) NULL,
    CONSTRAINT [PK_RssBlogs] PRIMARY KEY ([BlogId]),
    CONSTRAINT [FK_RssBlogs_Blogs_BlogId] FOREIGN KEY ([BlogId]) REFERENCES [Blogs] ([BlogId]) ON DELETE NO ACTION
);
```

> [!NOTE]
> <span data-ttu-id="6cd11-135">Si se cambia el nombre de la restricción primary key, el nuevo nombre se aplicará a todas las tablas asignadas a la jerarquía, las versiones futuras de EF permitirán cambiar el nombre de la restricción solo para una tabla determinada cuando se corrija el [problema 19970](https://github.com/dotnet/efcore/issues/19970) .</span><span class="sxs-lookup"><span data-stu-id="6cd11-135">If the primary key constraint is renamed the new name will be applied to all the tables mapped to the hierarchy, future EF versions will allow renaming the constraint only for a particular table when [issue 19970](https://github.com/dotnet/efcore/issues/19970) is fixed.</span></span>

<span data-ttu-id="6cd11-136">Si está empleando la configuración masiva, puede recuperar el nombre de columna de una tabla concreta mediante una llamada a <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A> .</span><span class="sxs-lookup"><span data-stu-id="6cd11-136">If you are employing bulk configuration you can retrieve the column name for a specific table by calling <xref:Microsoft.EntityFrameworkCore.RelationalPropertyExtensions.GetColumnName%2A>.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/TPTConfiguration.cs?name=Metadata&highlight=10)]
