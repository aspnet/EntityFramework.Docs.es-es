---
title: 'Claves: EF Core'
description: Cómo configurar claves para tipos de entidad al utilizar Entity Framework Core
author: AndriySvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: ea744315514b19465aba9a311055a9b5e49fa3f8
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063795"
---
# <a name="keys"></a><span data-ttu-id="bd4c4-103">Claves</span><span class="sxs-lookup"><span data-stu-id="bd4c4-103">Keys</span></span>

<span data-ttu-id="bd4c4-104">Una clave actúa como identificador único para cada instancia de la entidad.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-104">A key serves as a unique identifier for each entity instance.</span></span> <span data-ttu-id="bd4c4-105">La mayoría de las entidades de EF tienen una sola clave, que se asigna al concepto de una *clave principal* en las bases de datos relacionales (para entidades sin claves, vea [entidades](xref:core/modeling/keyless-entity-types)sin clave).</span><span class="sxs-lookup"><span data-stu-id="bd4c4-105">Most entities in EF have a single key, which maps to the concept of a *primary key* in relational databases (for entities without keys, see [Keyless entities](xref:core/modeling/keyless-entity-types)).</span></span> <span data-ttu-id="bd4c4-106">Las entidades pueden tener claves adicionales más allá de la clave principal (consulte [claves alternativas](#alternate-keys) para obtener más información).</span><span class="sxs-lookup"><span data-stu-id="bd4c4-106">Entities can have additional keys beyond the primary key (see [Alternate Keys](#alternate-keys) for more information).</span></span>

<span data-ttu-id="bd4c4-107">Por Convención, se `Id` configurará una propiedad denominada o `<type name>Id` como la clave principal de una entidad.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-107">By convention, a property named `Id` or `<type name>Id` will be configured as the primary key of an entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3,11)]

> [!NOTE]
> <span data-ttu-id="bd4c4-108">Los [tipos de entidad de propiedad](xref:core/modeling/owned-entities) usan reglas diferentes para definir claves.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-108">[Owned entity types](xref:core/modeling/owned-entities) use different rules to define keys.</span></span>

<span data-ttu-id="bd4c4-109">Puede configurar una única propiedad para que sea la clave principal de una entidad, como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="bd4c4-109">You can configure a single property to be the primary key of an entity as follows:</span></span>

## <a name="data-annotations"></a>[<span data-ttu-id="bd4c4-110">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="bd4c4-110">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?name=KeySingle&highlight=3)]

## <a name="fluent-api"></a>[<span data-ttu-id="bd4c4-111">API fluida</span><span class="sxs-lookup"><span data-stu-id="bd4c4-111">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?name=KeySingle&highlight=4)]

***

<span data-ttu-id="bd4c4-112">También puede configurar varias propiedades para que sean la clave de una entidad, lo que se conoce como clave compuesta.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-112">You can also configure multiple properties to be the key of an entity - this is known as a composite key.</span></span> <span data-ttu-id="bd4c4-113">Las claves compuestas solo se pueden configurar mediante la API fluida; las convenciones nunca configurarán una clave compuesta y no se pueden usar anotaciones de datos para configurar una.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-113">Composite keys can only be configured using the Fluent API; conventions will never setup a composite key, and you can not use Data Annotations to configure one.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?name=KeyComposite&highlight=4)]

## <a name="primary-key-name"></a><span data-ttu-id="bd4c4-114">Nombre de clave principal</span><span class="sxs-lookup"><span data-stu-id="bd4c4-114">Primary key name</span></span>

<span data-ttu-id="bd4c4-115">Por Convención, en las bases de datos relacionales, las claves principales se crean con el nombre `PK_<type name>` .</span><span class="sxs-lookup"><span data-stu-id="bd4c4-115">By convention, on relational databases primary keys are created with the name `PK_<type name>`.</span></span> <span data-ttu-id="bd4c4-116">Puede configurar el nombre de la restricción Primary Key de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="bd4c4-116">You can configure the name of the primary key constraint as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyName.cs?name=KeyName&highlight=5)]

## <a name="key-types-and-values"></a><span data-ttu-id="bd4c4-117">Tipos de clave y valores</span><span class="sxs-lookup"><span data-stu-id="bd4c4-117">Key types and values</span></span>

<span data-ttu-id="bd4c4-118">Aunque EF Core admite el uso de propiedades de cualquier tipo primitivo como clave principal, `string` incluidos `Guid` , `byte[]` y otros, no todas las bases de datos admiten todos los tipos como claves.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-118">While EF Core supports using properties of any primitive type as the primary key, including `string`, `Guid`, `byte[]` and others, not all databases support all types as keys.</span></span> <span data-ttu-id="bd4c4-119">En algunos casos, los valores de clave se pueden convertir automáticamente a un tipo compatible, de lo contrario, la conversión se debe [especificar manualmente](xref:core/modeling/value-conversions).</span><span class="sxs-lookup"><span data-stu-id="bd4c4-119">In some cases the key values can be converted to a supported type automatically, otherwise the conversion should be [specified manually](xref:core/modeling/value-conversions).</span></span>

<span data-ttu-id="bd4c4-120">Las propiedades de clave deben tener siempre un valor no predeterminado al agregar una nueva entidad al contexto, pero [la base de datos generará](xref:core/modeling/generated-properties)algunos tipos.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-120">Key properties must always have a non-default value when adding a new entity to the context, but some types will be [generated by the database](xref:core/modeling/generated-properties).</span></span> <span data-ttu-id="bd4c4-121">En ese caso, EF intentará generar un valor temporal cuando la entidad se agregue con fines de seguimiento.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-121">In that case EF will try to generate a temporary value when the entity is added for tracking purposes.</span></span> <span data-ttu-id="bd4c4-122">Después de llamar a [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) , el valor temporal se reemplazará por el valor generado por la base de datos.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-122">After [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges) is called the temporary value will be replaced by the value generated by the database.</span></span>

> [!Important]
> <span data-ttu-id="bd4c4-123">Si una propiedad de clave tiene su valor generado por la base de datos y se especifica un valor no predeterminado al agregar una entidad, EF asumirá que la entidad ya existe en la base de datos e intentará actualizarla en lugar de insertar una nueva.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-123">If a key property has its value generated by the database and a non-default value is specified when an entity is added, then EF will assume that the entity already exists in the database and will try to update it instead of inserting a new one.</span></span> <span data-ttu-id="bd4c4-124">Para evitar esto, desactive la generación de valores o vea [Cómo especificar valores explícitos para las propiedades generadas](xref:core/saving/explicit-values-generated-properties).</span><span class="sxs-lookup"><span data-stu-id="bd4c4-124">To avoid this turn off value generation or see [how to specify explicit values for generated properties](xref:core/saving/explicit-values-generated-properties).</span></span>

## <a name="alternate-keys"></a><span data-ttu-id="bd4c4-125">Claves alternativas</span><span class="sxs-lookup"><span data-stu-id="bd4c4-125">Alternate Keys</span></span>

<span data-ttu-id="bd4c4-126">Una clave alternativa actúa como identificador único alternativo para cada instancia de entidad además de la clave principal; se puede usar como destino de una relación.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-126">An alternate key serves as an alternate unique identifier for each entity instance in addition to the primary key; it can be used as the target of a relationship.</span></span> <span data-ttu-id="bd4c4-127">Al utilizar una base de datos relacional, se asigna al concepto de un índice o una restricción únicos en las columnas de clave alternativas y una o varias restricciones de clave externa que hacen referencia a las columnas.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-127">When using a relational database this maps to the concept of a unique index/constraint on the alternate key column(s) and one or more foreign key constraints that reference the column(s).</span></span>

> [!TIP]
> <span data-ttu-id="bd4c4-128">Si solo desea exigir la unicidad en una columna, defina un índice único en lugar de una clave alternativa (consulte [índices](xref:core/modeling/indexes)).</span><span class="sxs-lookup"><span data-stu-id="bd4c4-128">If you just want to enforce uniqueness on a column, define a unique index rather than an alternate key (see [Indexes](xref:core/modeling/indexes)).</span></span> <span data-ttu-id="bd4c4-129">En EF, las claves alternativas son de solo lectura y proporcionan una semántica adicional sobre índices únicos, ya que se pueden usar como destino de una clave externa.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-129">In EF, alternate keys are read-only and provide additional semantics over unique indexes because they can be used as the target of a foreign key.</span></span>

<span data-ttu-id="bd4c4-130">Normalmente, se introducen claves alternativas cuando sea necesario y no es necesario configurarlas manualmente.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-130">Alternate keys are typically introduced for you when needed and you do not need to manually configure them.</span></span> <span data-ttu-id="bd4c4-131">Por Convención, se introduce una clave alternativa cuando se identifica una propiedad que no es la clave principal como destino de una relación.</span><span class="sxs-lookup"><span data-stu-id="bd4c4-131">By convention, an alternate key is introduced for you when you identify a property which isn't the primary key as the target of a relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

<span data-ttu-id="bd4c4-132">También puede configurar una sola propiedad para que sea una clave alternativa:</span><span class="sxs-lookup"><span data-stu-id="bd4c4-132">You can also configure a single property to be an alternate key:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=4)]

<span data-ttu-id="bd4c4-133">También puede configurar varias propiedades para que sean una clave alternativa (conocida como clave alternativa compuesta):</span><span class="sxs-lookup"><span data-stu-id="bd4c4-133">You can also configure multiple properties to be an alternate key (known as a composite alternate key):</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=4)]

<span data-ttu-id="bd4c4-134">Por último, por Convención, el índice y la restricción que se introducen para una clave alternativa se denominarán `AK_<type name>_<property name>` (para las claves alternativas compuestas `<property name>` se convierte en una lista de nombres de propiedad separados por guiones bajos).</span><span class="sxs-lookup"><span data-stu-id="bd4c4-134">Finally, by convention, the index and constraint that are introduced for an alternate key will be named `AK_<type name>_<property name>` (for composite alternate keys `<property name>` becomes an underscore separated list of property names).</span></span> <span data-ttu-id="bd4c4-135">Puede configurar el nombre del índice de la clave alternativa y la restricción UNIQUE:</span><span class="sxs-lookup"><span data-stu-id="bd4c4-135">You can configure the name of the alternate key's index and unique constraint:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyName.cs?name=AlternateKeyName&highlight=5)]
