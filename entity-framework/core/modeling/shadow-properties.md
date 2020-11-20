---
title: 'Propiedades de sombra e indexador: EF Core'
description: Configurar las propiedades de sombra e indexador en un modelo de Entity Framework Core
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: d419de2da2a9fc29e675dde76e824217347d2e9c
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003437"
---
# <a name="shadow-and-indexer-properties"></a><span data-ttu-id="aae7e-103">Propiedades de sombra e indexador</span><span class="sxs-lookup"><span data-stu-id="aae7e-103">Shadow and Indexer Properties</span></span>

<span data-ttu-id="aae7e-104">Las propiedades de sombra son propiedades que no se definen en la clase de entidad de .NET pero que se definen para ese tipo de entidad en el modelo de EF Core.</span><span class="sxs-lookup"><span data-stu-id="aae7e-104">Shadow properties are properties that aren't defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="aae7e-105">El valor y el estado de estas propiedades se mantienen únicamente en el seguimiento de cambios.</span><span class="sxs-lookup"><span data-stu-id="aae7e-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="aae7e-106">Las propiedades de sombra son útiles cuando hay datos en la base de datos que no deben exponerse en los tipos de entidad asignados.</span><span class="sxs-lookup"><span data-stu-id="aae7e-106">Shadow properties are useful when there's data in the database that shouldn't be exposed on the mapped entity types.</span></span>

<span data-ttu-id="aae7e-107">Las propiedades del indexador son propiedades de tipo de entidad, que están respaldadas por un [indexador](/dotnet/csharp/programming-guide/indexers/) en la clase de entidad de .net.</span><span class="sxs-lookup"><span data-stu-id="aae7e-107">Indexer properties are entity type properties, which are backed by an [indexer](/dotnet/csharp/programming-guide/indexers/) in .NET entity class.</span></span> <span data-ttu-id="aae7e-108">Se puede tener acceso a ellos mediante el indizador en las instancias de clase .NET.</span><span class="sxs-lookup"><span data-stu-id="aae7e-108">They can be accessed using the indexer on the .NET class instances.</span></span> <span data-ttu-id="aae7e-109">También permite agregar propiedades adicionales al tipo de entidad sin cambiar la clase de CLR.</span><span class="sxs-lookup"><span data-stu-id="aae7e-109">It also allows you to add additional properties to the entity type without changing the CLR class.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="aae7e-110">Propiedades de sombra de clave externa</span><span class="sxs-lookup"><span data-stu-id="aae7e-110">Foreign key shadow properties</span></span>

<span data-ttu-id="aae7e-111">Las propiedades de sombra se utilizan con más frecuencia para las propiedades de clave externa, donde la relación entre dos entidades se representa mediante un valor de clave externa en la base de datos, pero la relación se administra en los tipos de entidad mediante propiedades de navegación entre los tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="aae7e-111">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="aae7e-112">Por Convención, EF introducirá una propiedad Shadow cuando se detecte una relación, pero no se encuentra ninguna propiedad de clave externa en la clase de entidad dependiente.</span><span class="sxs-lookup"><span data-stu-id="aae7e-112">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="aae7e-113">La propiedad se denominará `<navigation property name><principal key property name>` (la navegación en la entidad dependiente, que apunta a la entidad principal, se usa para la nomenclatura).</span><span class="sxs-lookup"><span data-stu-id="aae7e-113">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="aae7e-114">Si el nombre de la propiedad de clave principal incluye el nombre de la propiedad de navegación, el nombre será simplemente `<principal key property name>` .</span><span class="sxs-lookup"><span data-stu-id="aae7e-114">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="aae7e-115">Si no hay ninguna propiedad de navegación en la entidad dependiente, el nombre del tipo de entidad de seguridad se usa en su lugar.</span><span class="sxs-lookup"><span data-stu-id="aae7e-115">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="aae7e-116">Por ejemplo, la siguiente lista de código dará como resultado la inclusión de una `BlogId` propiedad Shadow en la `Post` entidad:</span><span class="sxs-lookup"><span data-stu-id="aae7e-116">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="aae7e-117">Configurar propiedades de instantáneas</span><span class="sxs-lookup"><span data-stu-id="aae7e-117">Configuring shadow properties</span></span>

<span data-ttu-id="aae7e-118">Puede usar la API fluida para configurar las propiedades de las instantáneas.</span><span class="sxs-lookup"><span data-stu-id="aae7e-118">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="aae7e-119">Una vez que haya llamado a la sobrecarga de la cadena de `Property` , puede encadenar cualquiera de las llamadas de configuración que desee para otras propiedades.</span><span class="sxs-lookup"><span data-stu-id="aae7e-119">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="aae7e-120">En el ejemplo siguiente, puesto que `Blog` no tiene ninguna propiedad CLR denominada `LastUpdated` , se crea una propiedad Shadow:</span><span class="sxs-lookup"><span data-stu-id="aae7e-120">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="aae7e-121">Si el nombre proporcionado al `Property` método coincide con el nombre de una propiedad existente (una propiedad Shadow o una definida en la clase de entidad), el código configurará esa propiedad existente en lugar de introducir una nueva propiedad Shadow.</span><span class="sxs-lookup"><span data-stu-id="aae7e-121">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="aae7e-122">Obtener acceso a las propiedades de sombra</span><span class="sxs-lookup"><span data-stu-id="aae7e-122">Accessing shadow properties</span></span>

<span data-ttu-id="aae7e-123">Los valores de las propiedades Shadow se pueden obtener y cambiar a través de la `ChangeTracker` API:</span><span class="sxs-lookup"><span data-stu-id="aae7e-123">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="aae7e-124">Se puede hacer referencia a las propiedades Shadow en consultas LINQ a través del `EF.Property` método estático:</span><span class="sxs-lookup"><span data-stu-id="aae7e-124">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="aae7e-125">No se puede tener acceso a las propiedades de sombra después de una consulta sin seguimiento, ya que el seguimiento de cambios no realiza un seguimiento de las entidades devueltas.</span><span class="sxs-lookup"><span data-stu-id="aae7e-125">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>

## <a name="configuring-indexer-properties"></a><span data-ttu-id="aae7e-126">Configuración de las propiedades del indexador</span><span class="sxs-lookup"><span data-stu-id="aae7e-126">Configuring indexer properties</span></span>

<span data-ttu-id="aae7e-127">Puede usar la API fluida para configurar las propiedades del indexador.</span><span class="sxs-lookup"><span data-stu-id="aae7e-127">You can use the Fluent API to configure indexer properties.</span></span> <span data-ttu-id="aae7e-128">Una vez que haya llamado al método `IndexerProperty` , puede encadenar cualquiera de las llamadas de configuración que desee para otras propiedades.</span><span class="sxs-lookup"><span data-stu-id="aae7e-128">Once you've called the method `IndexerProperty`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="aae7e-129">En el ejemplo siguiente, `Blog` tiene un indexador definido y se utilizará para crear una propiedad de indizador.</span><span class="sxs-lookup"><span data-stu-id="aae7e-129">In the following sample, `Blog` has an indexer defined and it will be used to create an indexer property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IndexerProperty.cs?name=ShadowProperty&highlight=3)]

<span data-ttu-id="aae7e-130">Si el nombre proporcionado al `IndexerProperty` método coincide con el nombre de una propiedad de indizador existente, el código configurará esa propiedad existente.</span><span class="sxs-lookup"><span data-stu-id="aae7e-130">If the name supplied to the `IndexerProperty` method matches the name of an existing indexer property, then the code will configure that existing property.</span></span> <span data-ttu-id="aae7e-131">Si el tipo de entidad tiene una propiedad, que está respaldada por una propiedad en la clase de entidad, se produce una excepción, ya que solo se debe tener acceso a las propiedades del indexador a través del indexador.</span><span class="sxs-lookup"><span data-stu-id="aae7e-131">If the entity type has a property, which is backed by a property on the entity class, then an exception is thrown since indexer properties must only be accessed via the indexer.</span></span>

## <a name="property-bag-entity-types"></a><span data-ttu-id="aae7e-132">Tipos de entidad de contenedor de propiedades</span><span class="sxs-lookup"><span data-stu-id="aae7e-132">Property bag entity types</span></span>

> [!NOTE]
> <span data-ttu-id="aae7e-133">La compatibilidad con los tipos de entidad del contenedor de propiedades se presentó en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="aae7e-133">Support for Property bag entity types was introduced in EF Core 5.0.</span></span>

<span data-ttu-id="aae7e-134">Los tipos de entidad que contienen solo propiedades de indizador se conocen como tipos de entidad de contenedor de propiedades.</span><span class="sxs-lookup"><span data-stu-id="aae7e-134">Entity types that contain only indexer properties are known as property bag entity types.</span></span> <span data-ttu-id="aae7e-135">Estos tipos de entidad no tienen propiedades de sombra; en su lugar, EF creará las propiedades del indexador.</span><span class="sxs-lookup"><span data-stu-id="aae7e-135">These entity types don't have shadow properties, instead EF will create indexer properties.</span></span> <span data-ttu-id="aae7e-136">Actualmente solo `Dictionary<string, object>` se admite como un tipo de entidad de contenedor de propiedades.</span><span class="sxs-lookup"><span data-stu-id="aae7e-136">Currently only `Dictionary<string, object>` is supported as a property bag entity type.</span></span> <span data-ttu-id="aae7e-137">Debe configurarse como un tipo de entidad compartida con un nombre único y la `DbSet` propiedad correspondiente debe implementarse mediante una `Set` llamada.</span><span class="sxs-lookup"><span data-stu-id="aae7e-137">It must be configured as a shared entity type with a unique name and the corresponding `DbSet` property must be implemented using a `Set` call.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
