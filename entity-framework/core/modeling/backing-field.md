---
title: 'Campos de respaldo: EF Core'
description: Configurar campos de respaldo para las propiedades de un modelo de Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/modeling/backing-field
ms.openlocfilehash: 48ef28e7b880fee571cb1857601fb2cead535fbf
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071633"
---
# <a name="backing-fields"></a><span data-ttu-id="f258d-103">Campos de respaldo</span><span class="sxs-lookup"><span data-stu-id="f258d-103">Backing Fields</span></span>

<span data-ttu-id="f258d-104">Los campos de respaldo permiten a EF leer o escribir en un campo en lugar de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="f258d-104">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="f258d-105">Esto puede ser útil cuando se usa la encapsulación en la clase para restringir el uso de y/o mejorar la semántica en torno al acceso a los datos por código de aplicación, pero el valor debe leerse o escribirse en la base de datos sin usar esas restricciones o mejoras.</span><span class="sxs-lookup"><span data-stu-id="f258d-105">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="f258d-106">Configuración básica</span><span class="sxs-lookup"><span data-stu-id="f258d-106">Basic configuration</span></span>

<span data-ttu-id="f258d-107">Por Convención, se detectarán los campos siguientes como campos de respaldo para una propiedad determinada (que se muestra en orden de prioridad).</span><span class="sxs-lookup"><span data-stu-id="f258d-107">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="f258d-108">En el ejemplo siguiente, la `Url` propiedad se configura para tener `_url` como su campo de respaldo:</span><span class="sxs-lookup"><span data-stu-id="f258d-108">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="f258d-109">Tenga en cuenta que los campos de respaldo solo se detectan para las propiedades que se incluyen en el modelo.</span><span class="sxs-lookup"><span data-stu-id="f258d-109">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="f258d-110">Para obtener más información sobre las propiedades que se incluyen en el modelo, vea [incluir & excluyendo las propiedades](xref:core/modeling/entity-properties).</span><span class="sxs-lookup"><span data-stu-id="f258d-110">For more information on which properties are included in the model, see [Including & Excluding Properties](xref:core/modeling/entity-properties).</span></span>

<span data-ttu-id="f258d-111">También puede configurar los campos de respaldo mediante una anotación de datos (disponible en EFCore 5,0) o la API fluida, por ejemplo, si el nombre del campo no se corresponde con las convenciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="f258d-111">You can also configure backing fields by using a Data Annotation (available in EFCore 5.0) or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="f258d-112">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="f258d-112">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="f258d-113">API fluida</span><span class="sxs-lookup"><span data-stu-id="f258d-113">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="f258d-114">Acceso a campos y propiedades</span><span class="sxs-lookup"><span data-stu-id="f258d-114">Field and property access</span></span>

<span data-ttu-id="f258d-115">De forma predeterminada, EF siempre leerá y escribirá en el campo de respaldo, suponiendo que se haya configurado correctamente, y que nunca usará la propiedad.</span><span class="sxs-lookup"><span data-stu-id="f258d-115">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="f258d-116">Sin embargo, EF también admite otros patrones de acceso.</span><span class="sxs-lookup"><span data-stu-id="f258d-116">However, EF also supports other access patterns.</span></span> <span data-ttu-id="f258d-117">Por ejemplo, en el ejemplo siguiente se indica a EF que escriba en el campo de respaldo solo mientras materializa y use la propiedad en todos los demás casos:</span><span class="sxs-lookup"><span data-stu-id="f258d-117">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="f258d-118">Vea la [enumeración PropertyAccessMode](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) para obtener el conjunto completo de opciones admitidas.</span><span class="sxs-lookup"><span data-stu-id="f258d-118">See the [PropertyAccessMode enum](/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="f258d-119">Con EF Core 3,0, el modo de acceso de propiedad predeterminado cambió de `PreferFieldDuringConstruction` a `PreferField` .</span><span class="sxs-lookup"><span data-stu-id="f258d-119">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="f258d-120">Propiedades de solo campo</span><span class="sxs-lookup"><span data-stu-id="f258d-120">Field-only properties</span></span>

<span data-ttu-id="f258d-121">También puede crear una propiedad conceptual en el modelo que no tiene una propiedad de CLR correspondiente en la clase de entidad, sino que usa un campo para almacenar los datos en la entidad.</span><span class="sxs-lookup"><span data-stu-id="f258d-121">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="f258d-122">Esto es diferente de [las propiedades de las instantáneas](xref:core/modeling/shadow-properties), donde los datos se almacenan en el seguimiento de cambios, en lugar de en el tipo CLR de la entidad.</span><span class="sxs-lookup"><span data-stu-id="f258d-122">This is different from [Shadow Properties](xref:core/modeling/shadow-properties), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="f258d-123">Las propiedades de solo campo se utilizan normalmente cuando la clase de entidad usa métodos en lugar de propiedades para obtener o establecer valores, o en casos en los que los campos no se deben exponer en absoluto en el modelo de dominio (por ejemplo, las claves principales).</span><span class="sxs-lookup"><span data-stu-id="f258d-123">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="f258d-124">Puede configurar una propiedad de solo campo proporcionando un nombre en la `Property(...)` API:</span><span class="sxs-lookup"><span data-stu-id="f258d-124">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="f258d-125">EF intentará buscar una propiedad CLR con el nombre especificado o un campo si no se encuentra una propiedad.</span><span class="sxs-lookup"><span data-stu-id="f258d-125">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="f258d-126">Si no se encuentra ninguna propiedad ni un campo, se configurará una propiedad Shadow en su lugar.</span><span class="sxs-lookup"><span data-stu-id="f258d-126">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="f258d-127">Es posible que tenga que hacer referencia a una propiedad de solo campo desde las consultas LINQ, pero estos campos suelen ser privados.</span><span class="sxs-lookup"><span data-stu-id="f258d-127">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="f258d-128">Puede usar el `EF.Property(...)` método en una consulta LINQ para hacer referencia al campo:</span><span class="sxs-lookup"><span data-stu-id="f258d-128">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
