---
title: 'Propiedades de sombra e indexador: EF Core'
description: Configurar las propiedades de sombra e indexador en un modelo de Entity Framework Core
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: 417ab57a4a77ecf626e54eeca900744d84e3fe08
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063899"
---
# <a name="shadow-and-indexer-properties"></a><span data-ttu-id="baa62-103">Propiedades de sombra e indexador</span><span class="sxs-lookup"><span data-stu-id="baa62-103">Shadow and Indexer Properties</span></span>

<span data-ttu-id="baa62-104">Las propiedades de sombra son propiedades que no están definidas en la clase de entidad de .NET pero que se definen para ese tipo de entidad en el modelo de EF Core.</span><span class="sxs-lookup"><span data-stu-id="baa62-104">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="baa62-105">El valor y el estado de estas propiedades se mantienen únicamente en el seguimiento de cambios.</span><span class="sxs-lookup"><span data-stu-id="baa62-105">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="baa62-106">Las propiedades Shadow son útiles cuando hay datos en la base de datos que no se deben exponer en los tipos de entidad asignados.</span><span class="sxs-lookup"><span data-stu-id="baa62-106">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="baa62-107">Propiedades de sombra de clave externa</span><span class="sxs-lookup"><span data-stu-id="baa62-107">Foreign key shadow properties</span></span>

<span data-ttu-id="baa62-108">Las propiedades de sombra se utilizan con más frecuencia para las propiedades de clave externa, donde la relación entre dos entidades se representa mediante un valor de clave externa en la base de datos, pero la relación se administra en los tipos de entidad mediante propiedades de navegación entre los tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="baa62-108">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="baa62-109">Por Convención, EF introducirá una propiedad Shadow cuando se detecte una relación, pero no se encuentra ninguna propiedad de clave externa en la clase de entidad dependiente.</span><span class="sxs-lookup"><span data-stu-id="baa62-109">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="baa62-110">La propiedad se denominará `<navigation property name><principal key property name>` (la navegación en la entidad dependiente, que apunta a la entidad principal, se usa para la nomenclatura).</span><span class="sxs-lookup"><span data-stu-id="baa62-110">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="baa62-111">Si el nombre de la propiedad de clave principal incluye el nombre de la propiedad de navegación, el nombre será simplemente `<principal key property name>` .</span><span class="sxs-lookup"><span data-stu-id="baa62-111">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="baa62-112">Si no hay ninguna propiedad de navegación en la entidad dependiente, el nombre del tipo de entidad de seguridad se usa en su lugar.</span><span class="sxs-lookup"><span data-stu-id="baa62-112">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="baa62-113">Por ejemplo, la siguiente lista de código dará como resultado la inclusión de una `BlogId` propiedad Shadow en la `Post` entidad:</span><span class="sxs-lookup"><span data-stu-id="baa62-113">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="baa62-114">Configurar propiedades de instantáneas</span><span class="sxs-lookup"><span data-stu-id="baa62-114">Configuring shadow properties</span></span>

<span data-ttu-id="baa62-115">Puede usar la API fluida para configurar las propiedades de las instantáneas.</span><span class="sxs-lookup"><span data-stu-id="baa62-115">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="baa62-116">Una vez que haya llamado a la sobrecarga de la cadena de `Property` , puede encadenar cualquiera de las llamadas de configuración que desee para otras propiedades.</span><span class="sxs-lookup"><span data-stu-id="baa62-116">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="baa62-117">En el ejemplo siguiente, puesto que `Blog` no tiene ninguna propiedad CLR denominada `LastUpdated` , se crea una propiedad Shadow:</span><span class="sxs-lookup"><span data-stu-id="baa62-117">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="baa62-118">Si el nombre proporcionado al `Property` método coincide con el nombre de una propiedad existente (una propiedad Shadow o una definida en la clase de entidad), el código configurará esa propiedad existente en lugar de introducir una nueva propiedad Shadow.</span><span class="sxs-lookup"><span data-stu-id="baa62-118">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="baa62-119">Obtener acceso a las propiedades de sombra</span><span class="sxs-lookup"><span data-stu-id="baa62-119">Accessing shadow properties</span></span>

<span data-ttu-id="baa62-120">Los valores de las propiedades Shadow se pueden obtener y cambiar a través de la `ChangeTracker` API:</span><span class="sxs-lookup"><span data-stu-id="baa62-120">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="baa62-121">Se puede hacer referencia a las propiedades Shadow en consultas LINQ a través del `EF.Property` método estático:</span><span class="sxs-lookup"><span data-stu-id="baa62-121">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="baa62-122">No se puede tener acceso a las propiedades de sombra después de una consulta sin seguimiento, ya que el seguimiento de cambios no realiza un seguimiento de las entidades devueltas.</span><span class="sxs-lookup"><span data-stu-id="baa62-122">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>

## <a name="property-bag-entity-types"></a><span data-ttu-id="baa62-123">Tipos de entidad de contenedor de propiedades</span><span class="sxs-lookup"><span data-stu-id="baa62-123">Property bag entity types</span></span>

> [!NOTE]
> <span data-ttu-id="baa62-124">Se ha agregado compatibilidad con los tipos de entidad del contenedor de propiedades en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="baa62-124">Support for Property bag entity types was added in EF Core 5.0.</span></span>

<span data-ttu-id="baa62-125">Los tipos de entidad que contienen solo propiedades de indizador se conocen como tipos de entidad de contenedor de propiedades.</span><span class="sxs-lookup"><span data-stu-id="baa62-125">Entity types that contain only indexer properties are known as property bag entity types.</span></span> <span data-ttu-id="baa62-126">Estos tipos de entidad no tienen propiedades de sombra.</span><span class="sxs-lookup"><span data-stu-id="baa62-126">These entity types don't have shadow properties.</span></span> <span data-ttu-id="baa62-127">Actualmente solo `Dictionary<string, object>` se admite como un tipo de entidad de contenedor de propiedades.</span><span class="sxs-lookup"><span data-stu-id="baa62-127">Currently only `Dictionary<string, object>` is supported as a property bag entity type.</span></span> <span data-ttu-id="baa62-128">Esto significa que debe configurarse como un tipo de entidad compartida con un nombre único y la `DbSet` propiedad correspondiente debe implementarse mediante una `Set` llamada.</span><span class="sxs-lookup"><span data-stu-id="baa62-128">This means that it must be configured as a shared entity type with a unique name and the corresponding `DbSet` property must be implemented using a `Set` call.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]