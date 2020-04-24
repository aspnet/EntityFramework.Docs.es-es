---
title: 'Campos de respaldo: EF Core'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: a628795e-64df-4f24-a5e8-76bc261e7ed8
uid: core/modeling/backing-field
ms.openlocfilehash: 5c1b2e8036a8556d69cac2ec22722fc72d6da4aa
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103157"
---
# <a name="backing-fields"></a><span data-ttu-id="2032d-102">Campos de respaldo</span><span class="sxs-lookup"><span data-stu-id="2032d-102">Backing Fields</span></span>

<span data-ttu-id="2032d-103">Los campos de respaldo permiten a EF leer o escribir en un campo en lugar de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="2032d-103">Backing fields allow EF to read and/or write to a field rather than a property.</span></span> <span data-ttu-id="2032d-104">Esto puede ser útil cuando se usa la encapsulación en la clase para restringir el uso de y/o mejorar la semántica en torno al acceso a los datos por código de aplicación, pero el valor debe leerse o escribirse en la base de datos sin usar esas restricciones o mejoras.</span><span class="sxs-lookup"><span data-stu-id="2032d-104">This can be useful when encapsulation in the class is being used to restrict the use of and/or enhance the semantics around access to the data by application code, but the value should be read from and/or written to the database without using those restrictions/enhancements.</span></span>

## <a name="basic-configuration"></a><span data-ttu-id="2032d-105">Configuración básica</span><span class="sxs-lookup"><span data-stu-id="2032d-105">Basic configuration</span></span>

<span data-ttu-id="2032d-106">Por Convención, se detectarán los campos siguientes como campos de respaldo para una propiedad determinada (que se muestra en orden de prioridad).</span><span class="sxs-lookup"><span data-stu-id="2032d-106">By convention, the following fields will be discovered as backing fields for a given property (listed in precedence order).</span></span> 

* `_<camel-cased property name>`
* `_<property name>`
* `m_<camel-cased property name>`
* `m_<property name>`

<span data-ttu-id="2032d-107">En el ejemplo siguiente, la `Url` propiedad se configura para tener `_url` como su campo de respaldo:</span><span class="sxs-lookup"><span data-stu-id="2032d-107">In the following sample, the `Url` property is configured to have `_url` as its backing field:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/BackingField.cs#Sample)]

<span data-ttu-id="2032d-108">Tenga en cuenta que los campos de respaldo solo se detectan para las propiedades que se incluyen en el modelo.</span><span class="sxs-lookup"><span data-stu-id="2032d-108">Note that backing fields are only discovered for properties that are included in the model.</span></span> <span data-ttu-id="2032d-109">Para obtener más información sobre las propiedades que se incluyen en el modelo, vea [incluir & excluyendo las propiedades](included-properties.md).</span><span class="sxs-lookup"><span data-stu-id="2032d-109">For more information on which properties are included in the model, see [Including & Excluding Properties](included-properties.md).</span></span>

<span data-ttu-id="2032d-110">También puede configurar los campos de respaldo mediante una anotación de datos (disponible en EFCore 5,0) o la API fluida, por ejemplo, si el nombre del campo no se corresponde con las convenciones anteriores:</span><span class="sxs-lookup"><span data-stu-id="2032d-110">You can also configure backing fields by using a Data Annotation (available in EFCore 5.0) or the Fluent API, e.g. if the field name doesn't correspond to the above conventions:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="2032d-111">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="2032d-111">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/BackingField.cs?name=BackingField&highlight=7)]

### <a name="fluent-api"></a>[<span data-ttu-id="2032d-112">API fluida</span><span class="sxs-lookup"><span data-stu-id="2032d-112">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingField.cs?name=BackingField&highlight=5)]

## <a name="field-and-property-access"></a><span data-ttu-id="2032d-113">Acceso a campos y propiedades</span><span class="sxs-lookup"><span data-stu-id="2032d-113">Field and property access</span></span>

<span data-ttu-id="2032d-114">De forma predeterminada, EF siempre leerá y escribirá en el campo de respaldo, suponiendo que se haya configurado correctamente, y que nunca usará la propiedad.</span><span class="sxs-lookup"><span data-stu-id="2032d-114">By default, EF will always read and write to the backing field - assuming one has been properly configured - and will never use the property.</span></span> <span data-ttu-id="2032d-115">Sin embargo, EF también admite otros patrones de acceso.</span><span class="sxs-lookup"><span data-stu-id="2032d-115">However, EF also supports other access patterns.</span></span> <span data-ttu-id="2032d-116">Por ejemplo, en el ejemplo siguiente se indica a EF que escriba en el campo de respaldo solo mientras materializa y use la propiedad en todos los demás casos:</span><span class="sxs-lookup"><span data-stu-id="2032d-116">For example, the following sample instructs EF to write to the backing field only while materializing, and to use the property in all other cases:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldAccessMode.cs?name=BackingFieldAccessMode&highlight=6)]

<span data-ttu-id="2032d-117">Vea la [enumeración PropertyAccessMode](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) para obtener el conjunto completo de opciones admitidas.</span><span class="sxs-lookup"><span data-stu-id="2032d-117">See the [PropertyAccessMode enum](https://docs.microsoft.com/dotnet/api/microsoft.entityframeworkcore.propertyaccessmode) for the complete set of supported options.</span></span>

> [!NOTE]
> <span data-ttu-id="2032d-118">Con EF Core 3,0, el modo de acceso de propiedad predeterminado `PreferFieldDuringConstruction` cambió `PreferField`de a.</span><span class="sxs-lookup"><span data-stu-id="2032d-118">With EF Core 3.0, the default property access mode changed from `PreferFieldDuringConstruction` to `PreferField`.</span></span>

## <a name="field-only-properties"></a><span data-ttu-id="2032d-119">Propiedades de solo campo</span><span class="sxs-lookup"><span data-stu-id="2032d-119">Field-only properties</span></span>

<span data-ttu-id="2032d-120">También puede crear una propiedad conceptual en el modelo que no tiene una propiedad de CLR correspondiente en la clase de entidad, sino que usa un campo para almacenar los datos en la entidad.</span><span class="sxs-lookup"><span data-stu-id="2032d-120">You can also create a conceptual property in your model that does not have a corresponding CLR property in the entity class, but instead uses a field to store the data in the entity.</span></span> <span data-ttu-id="2032d-121">Esto es diferente de [las propiedades de las instantáneas](shadow-properties.md), donde los datos se almacenan en el seguimiento de cambios, en lugar de en el tipo CLR de la entidad.</span><span class="sxs-lookup"><span data-stu-id="2032d-121">This is different from [Shadow Properties](shadow-properties.md), where the data is stored in the change tracker, rather than in the entity's CLR type.</span></span> <span data-ttu-id="2032d-122">Las propiedades de solo campo se utilizan normalmente cuando la clase de entidad usa métodos en lugar de propiedades para obtener o establecer valores, o en casos en los que los campos no se deben exponer en absoluto en el modelo de dominio (por ejemplo, las claves principales).</span><span class="sxs-lookup"><span data-stu-id="2032d-122">Field-only properties are commonly used when the entity class uses methods instead of properties to get/set values, or in cases where fields shouldn't be exposed at all in the domain model (e.g. primary keys).</span></span>

<span data-ttu-id="2032d-123">Puede configurar una propiedad de solo campo proporcionando un nombre en la `Property(...)` API:</span><span class="sxs-lookup"><span data-stu-id="2032d-123">You can configure a field-only property by providing a name in the `Property(...)` API:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/BackingFieldNoProperty.cs#Sample)]

<span data-ttu-id="2032d-124">EF intentará buscar una propiedad CLR con el nombre especificado o un campo si no se encuentra una propiedad.</span><span class="sxs-lookup"><span data-stu-id="2032d-124">EF will attempt to find a CLR property with the given name, or a field if a property isn't found.</span></span> <span data-ttu-id="2032d-125">Si no se encuentra ninguna propiedad ni un campo, se configurará una propiedad Shadow en su lugar.</span><span class="sxs-lookup"><span data-stu-id="2032d-125">If neither a property nor a field are found, a shadow property will be set up instead.</span></span>

<span data-ttu-id="2032d-126">Es posible que tenga que hacer referencia a una propiedad de solo campo desde las consultas LINQ, pero estos campos suelen ser privados.</span><span class="sxs-lookup"><span data-stu-id="2032d-126">You may need to refer to a field-only property from LINQ queries, but such fields are typically private.</span></span> <span data-ttu-id="2032d-127">Puede usar el `EF.Property(...)` método en una consulta LINQ para hacer referencia al campo:</span><span class="sxs-lookup"><span data-stu-id="2032d-127">You can use the `EF.Property(...)` method in a LINQ query to refer to the field:</span></span>

``` csharp
var blogs = db.blogs.OrderBy(b => EF.Property<string>(b, "_validatedUrl"));
```
