---
title: 'Propiedades de sombra: EF Core'
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: b24ff85d8f5910a5625910e7225a94112d769824
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238286"
---
# <a name="shadow-properties"></a><span data-ttu-id="b6367-102">Propiedades reemplazadas</span><span class="sxs-lookup"><span data-stu-id="b6367-102">Shadow Properties</span></span>

<span data-ttu-id="b6367-103">Las propiedades de sombra son propiedades que no están definidas en la clase de entidad de .NET pero que se definen para ese tipo de entidad en el modelo de EF Core.</span><span class="sxs-lookup"><span data-stu-id="b6367-103">Shadow properties are properties that are not defined in your .NET entity class but are defined for that entity type in the EF Core model.</span></span> <span data-ttu-id="b6367-104">El valor y el estado de estas propiedades se mantienen únicamente en el seguimiento de cambios.</span><span class="sxs-lookup"><span data-stu-id="b6367-104">The value and state of these properties is maintained purely in the Change Tracker.</span></span> <span data-ttu-id="b6367-105">Las propiedades Shadow son útiles cuando hay datos en la base de datos que no se deben exponer en los tipos de entidad asignados.</span><span class="sxs-lookup"><span data-stu-id="b6367-105">Shadow properties are useful when there is data in the database that should not be exposed on the mapped entity types.</span></span>

## <a name="foreign-key-shadow-properties"></a><span data-ttu-id="b6367-106">Propiedades de sombra de clave externa</span><span class="sxs-lookup"><span data-stu-id="b6367-106">Foreign key shadow properties</span></span>

<span data-ttu-id="b6367-107">Las propiedades de sombra se utilizan con más frecuencia para las propiedades de clave externa, donde la relación entre dos entidades se representa mediante un valor de clave externa en la base de datos, pero la relación se administra en los tipos de entidad mediante propiedades de navegación entre los tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="b6367-107">Shadow properties are most often used for foreign key properties, where the relationship between two entities is represented by a foreign key value in the database, but the relationship is managed on the entity types using navigation properties between the entity types.</span></span> <span data-ttu-id="b6367-108">Por Convención, EF introducirá una propiedad Shadow cuando se detecte una relación, pero no se encuentra ninguna propiedad de clave externa en la clase de entidad dependiente.</span><span class="sxs-lookup"><span data-stu-id="b6367-108">By convention, EF will introduce a shadow property when a relationship is discovered but no foreign key property is found in the dependent entity class.</span></span>

<span data-ttu-id="b6367-109">La propiedad se denominará `<navigation property name><principal key property name>` (la navegación en la entidad dependiente, que apunta a la entidad principal, se usa para la nomenclatura).</span><span class="sxs-lookup"><span data-stu-id="b6367-109">The property will be named `<navigation property name><principal key property name>` (the navigation on the dependent entity, which points to the principal entity, is used for the naming).</span></span> <span data-ttu-id="b6367-110">Si el nombre de la propiedad de clave principal incluye el nombre de la propiedad de navegación, el nombre será simplemente `<principal key property name>` .</span><span class="sxs-lookup"><span data-stu-id="b6367-110">If the principal key property name includes the name of the navigation property, then the name will just be `<principal key property name>`.</span></span> <span data-ttu-id="b6367-111">Si no hay ninguna propiedad de navegación en la entidad dependiente, el nombre del tipo de entidad de seguridad se usa en su lugar.</span><span class="sxs-lookup"><span data-stu-id="b6367-111">If there is no navigation property on the dependent entity, then the principal type name is used in its place.</span></span>

<span data-ttu-id="b6367-112">Por ejemplo, la siguiente lista de código dará como resultado la inclusión de una `BlogId` propiedad Shadow en la `Post` entidad:</span><span class="sxs-lookup"><span data-stu-id="b6367-112">For example, the following code listing will result in a `BlogId` shadow property being introduced to the `Post` entity:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a><span data-ttu-id="b6367-113">Configurar propiedades de instantáneas</span><span class="sxs-lookup"><span data-stu-id="b6367-113">Configuring shadow properties</span></span>

<span data-ttu-id="b6367-114">Puede usar la API fluida para configurar las propiedades de las instantáneas.</span><span class="sxs-lookup"><span data-stu-id="b6367-114">You can use the Fluent API to configure shadow properties.</span></span> <span data-ttu-id="b6367-115">Una vez que haya llamado a la sobrecarga de la cadena de `Property` , puede encadenar cualquiera de las llamadas de configuración que desee para otras propiedades.</span><span class="sxs-lookup"><span data-stu-id="b6367-115">Once you have called the string overload of `Property`, you can chain any of the configuration calls you would for other properties.</span></span> <span data-ttu-id="b6367-116">En el ejemplo siguiente, puesto que `Blog` no tiene ninguna propiedad CLR denominada `LastUpdated` , se crea una propiedad Shadow:</span><span class="sxs-lookup"><span data-stu-id="b6367-116">In the following sample, since `Blog` has no CLR property named `LastUpdated`, a shadow property is created:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

<span data-ttu-id="b6367-117">Si el nombre proporcionado al `Property` método coincide con el nombre de una propiedad existente (una propiedad Shadow o una definida en la clase de entidad), el código configurará esa propiedad existente en lugar de introducir una nueva propiedad Shadow.</span><span class="sxs-lookup"><span data-stu-id="b6367-117">If the name supplied to the `Property` method matches the name of an existing property (a shadow property or one defined on the entity class), then the code will configure that existing property rather than introducing a new shadow property.</span></span>

## <a name="accessing-shadow-properties"></a><span data-ttu-id="b6367-118">Obtener acceso a las propiedades de sombra</span><span class="sxs-lookup"><span data-stu-id="b6367-118">Accessing shadow properties</span></span>

<span data-ttu-id="b6367-119">Los valores de las propiedades Shadow se pueden obtener y cambiar a través de la `ChangeTracker` API:</span><span class="sxs-lookup"><span data-stu-id="b6367-119">Shadow property values can be obtained and changed through the `ChangeTracker` API:</span></span>

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

<span data-ttu-id="b6367-120">Se puede hacer referencia a las propiedades Shadow en consultas LINQ a través del `EF.Property` método estático:</span><span class="sxs-lookup"><span data-stu-id="b6367-120">Shadow properties can be referenced in LINQ queries via the `EF.Property` static method:</span></span>

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

<span data-ttu-id="b6367-121">No se puede tener acceso a las propiedades de sombra después de una consulta sin seguimiento, ya que el seguimiento de cambios no realiza un seguimiento de las entidades devueltas.</span><span class="sxs-lookup"><span data-stu-id="b6367-121">Shadow properties cannot be accessed after a no-tracking query since the entities returned are not tracked by the change tracker.</span></span>
