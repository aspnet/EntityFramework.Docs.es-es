---
title: 'Propiedades de la entidad: EF Core'
description: Cómo configurar y asignar propiedades de entidad mediante Entity Framework Core
author: roji
ms.date: 12/10/2019
ms.assetid: e9dff604-3469-4a05-8f9e-18ac281d82a9
uid: core/modeling/entity-properties
ms.openlocfilehash: b67603fbffd1f1c8506bc21f8972c851eb8eef29
ms.sourcegitcommit: 32c51c22988c6f83ed4f8e50a1d01be3f4114e81
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/27/2019
ms.locfileid: "75502430"
---
# <a name="entity-properties"></a><span data-ttu-id="2b2a4-103">Propiedades de entidad</span><span class="sxs-lookup"><span data-stu-id="2b2a4-103">Entity Properties</span></span>

<span data-ttu-id="2b2a4-104">Cada tipo de entidad del modelo tiene un conjunto de propiedades, que EF Core leerán y escribirán en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-104">Each entity type in your model has a set of properties, which EF Core will read and write from the database.</span></span> <span data-ttu-id="2b2a4-105">Si utiliza una base de datos relacional, las propiedades de entidad se asignan a las columnas de la tabla.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-105">If you're using a relational database, entity properties map to table columns.</span></span>

## <a name="included-and-excluded-properties"></a><span data-ttu-id="2b2a4-106">Propiedades incluidas y excluidas</span><span class="sxs-lookup"><span data-stu-id="2b2a4-106">Included and excluded properties</span></span>

<span data-ttu-id="2b2a4-107">Por Convención, todas las propiedades públicas con un captador y un establecedor se incluirán en el modelo.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-107">By convention, all public properties with a getter and a setter will be included in the model.</span></span>

<span data-ttu-id="2b2a4-108">Las propiedades específicas se pueden excluir de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-108">Specific properties can be excluded as follows:</span></span>

### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="2b2a4-109">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="2b2a4-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="2b2a4-110">API fluida</span><span class="sxs-lookup"><span data-stu-id="2b2a4-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a><span data-ttu-id="2b2a4-111">Nombres de columna</span><span class="sxs-lookup"><span data-stu-id="2b2a4-111">Column names</span></span>

<span data-ttu-id="2b2a4-112">Por Convención, cuando se utiliza una base de datos relacional, las propiedades de entidad se asignan a las columnas de la tabla que tienen el mismo nombre que la propiedad.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-112">By convention, when using a relational database, entity properties are mapped to table columns having the same name as the property.</span></span>

<span data-ttu-id="2b2a4-113">Si prefiere configurar las columnas con nombres diferentes, puede hacerlo de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-113">If you prefer to configure your columns with different names, you can do so as following:</span></span>

### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="2b2a4-114">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="2b2a4-114">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="2b2a4-115">API fluida</span><span class="sxs-lookup"><span data-stu-id="2b2a4-115">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a><span data-ttu-id="2b2a4-116">Tipos de datos de columna</span><span class="sxs-lookup"><span data-stu-id="2b2a4-116">Column data types</span></span>

<span data-ttu-id="2b2a4-117">Al utilizar una base de datos relacional, el proveedor de base de datos selecciona un tipo de datos basado en el tipo .NET de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-117">When using a relational database, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="2b2a4-118">También tiene en cuenta otros metadatos, como la [longitud máxima](#maximum-length)configurada, si la propiedad forma parte de una clave principal, etc.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-118">It also takes into account other metadata, such as the configured [maximum length](#maximum-length), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="2b2a4-119">Por ejemplo, SQL Server asigna propiedades de `DateTime` a columnas de `datetime2(7)` y `string` propiedades a `nvarchar(max)` columnas (o a `nvarchar(450)` para propiedades que se utilizan como clave).</span><span class="sxs-lookup"><span data-stu-id="2b2a4-119">For example, SQL Server maps `DateTime` properties to `datetime2(7)` columns, and `string` properties to `nvarchar(max)` columns (or to `nvarchar(450)` for properties that are used as a key).</span></span>

<span data-ttu-id="2b2a4-120">También puede configurar las columnas para especificar un tipo de datos exacto para una columna.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-120">You can also configure your columns to specify an exact data type for a column.</span></span> <span data-ttu-id="2b2a4-121">Por ejemplo, el código siguiente configura `Url` como una cadena no Unicode con una longitud máxima de `200` y `Rating` como decimal con precisión de `5` y la escala de `2`:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-121">For example the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`:</span></span>

### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="2b2a4-122">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="2b2a4-122">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="2b2a4-123">API fluida</span><span class="sxs-lookup"><span data-stu-id="2b2a4-123">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a><span data-ttu-id="2b2a4-124">Longitud máxima</span><span class="sxs-lookup"><span data-stu-id="2b2a4-124">Maximum length</span></span>

<span data-ttu-id="2b2a4-125">La configuración de una longitud máxima proporciona una sugerencia al proveedor de base de datos sobre el tipo de datos de columna adecuado que se debe elegir para una propiedad determinada.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-125">Configuring a maximum length provides a hint to the database provider about the appropriate column data type to choose for a given property.</span></span> <span data-ttu-id="2b2a4-126">La longitud máxima solo se aplica a los tipos de datos de matriz, como `string` y `byte[]`.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-126">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]
> <span data-ttu-id="2b2a4-127">Entity Framework no realiza ninguna validación de la longitud máxima antes de pasar datos al proveedor.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-127">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="2b2a4-128">Depende del proveedor o del almacén de datos que se valide si es necesario.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-128">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="2b2a4-129">Por ejemplo, cuando el destino es SQL Server, si se supera la longitud máxima, se producirá una excepción, ya que el tipo de datos de la columna subyacente no permitirá que se almacenen los datos sobrantes.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-129">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

<span data-ttu-id="2b2a4-130">En el ejemplo siguiente, la configuración de una longitud máxima de 500 hará que se cree una columna de tipo `nvarchar(500)` en SQL Server:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-130">In the following example, configuring a maximum length of 500 will cause a column of type `nvarchar(500)` to be created on SQL Server:</span></span>

#### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="2b2a4-131">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="2b2a4-131">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="2b2a4-132">API fluida</span><span class="sxs-lookup"><span data-stu-id="2b2a4-132">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

## <a name="required-and-optional-properties"></a><span data-ttu-id="2b2a4-133">Propiedades obligatorias y opcionales</span><span class="sxs-lookup"><span data-stu-id="2b2a4-133">Required and optional properties</span></span>

<span data-ttu-id="2b2a4-134">Una propiedad se considera opcional si es válida para que contenga `null`.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-134">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="2b2a4-135">Si `null` no es un valor válido que se va a asignar a una propiedad, se considera que es una propiedad obligatoria.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-135">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span> <span data-ttu-id="2b2a4-136">Al asignar a un esquema de base de datos relacional, las propiedades requeridas se crean como columnas que no aceptan valores NULL y las propiedades opcionales se crean como columnas que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-136">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

### <a name="conventions"></a><span data-ttu-id="2b2a4-137">Convenciones</span><span class="sxs-lookup"><span data-stu-id="2b2a4-137">Conventions</span></span>

<span data-ttu-id="2b2a4-138">Por Convención, una propiedad cuyo tipo .NET pueda contener NULL se configurará como opcional, mientras que las propiedades cuyo tipo .NET no puede contener valores NULL se configurarán según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-138">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="2b2a4-139">Por ejemplo, todas las propiedades con tipos de valor .NET (`int`, `decimal`, `bool`, etc.) se configuran según sea necesario y todas las propiedades con tipos de valor .NET que aceptan valores NULL (`int?`, `decimal?`, `bool?`, etc.) se configuran como opcionales.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-139">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="2b2a4-140">C#8 presentó una nueva característica denominada [tipos de referencia que aceptan valores NULL](/dotnet/csharp/tutorials/nullable-reference-types), que permite anotar tipos de referencia, lo que indica si es válido para que contengan null o not.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-140">C# 8 introduced a new feature called [nullable reference types](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="2b2a4-141">Esta característica está deshabilitada de forma predeterminada y, si está habilitada, modifica el comportamiento de EF Core de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-141">This feature is disabled by default, and if enabled, it modifies EF Core's behavior in the following way:</span></span>

* <span data-ttu-id="2b2a4-142">Si los tipos de referencia que aceptan valores NULL están deshabilitados (el valor predeterminado), todas las propiedades con tipos de referencia de .NET se configuran como opcionales por Convención (por ejemplo, `string`).</span><span class="sxs-lookup"><span data-stu-id="2b2a4-142">If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (e.g. `string`).</span></span>
* <span data-ttu-id="2b2a4-143">Si los tipos de referencia que aceptan valores NULL están habilitados, las propiedades C# se configurarán en función de la nulabilidad de su tipo .net: `string?` se configurarán como opcionales, mientras que `string` se configurarán según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-143">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, whereas `string` will be configured as required.</span></span>

<span data-ttu-id="2b2a4-144">En el ejemplo siguiente se muestra un tipo de entidad con propiedades obligatorias y opcionales, con la característica de referencia que acepta valores NULL deshabilitada (valor predeterminado) y habilitada:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-144">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

#### <a name="without-nullable-reference-types-defaulttabwithout-nrt"></a>[<span data-ttu-id="2b2a4-145">Sin tipos de referencia que aceptan valores NULL (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="2b2a4-145">Without nullable reference types (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-typestabwith-nrt"></a>[<span data-ttu-id="2b2a4-146">Con tipos de referencia que aceptan valores NULL</span><span class="sxs-lookup"><span data-stu-id="2b2a4-146">With nullable reference types</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="2b2a4-147">Se recomienda el uso de tipos de referencia que aceptan valores NULL, ya que C# fluye la nulabilidad expresada en el código para EF Core modelo y en la base de datos, e obvia el uso de las anotaciones de datos o la API fluida para expresar el mismo concepto dos veces.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-147">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="2b2a4-148">Tenga cuidado al habilitar los tipos de referencia que aceptan valores NULL en un proyecto existente: las propiedades de tipo de referencia que se configuraron anteriormente como opcional ahora se configurarán según sea necesario, a menos que se anoten explícitamente para que acepten valores NULL.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-148">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="2b2a4-149">Al administrar un esquema de base de datos relacional, esto puede provocar que se generen migraciones que modifiquen la nulabilidad de la columna de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="2b2a4-149">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="2b2a4-150">Para obtener más información sobre los tipos de referencia que aceptan valores NULL y cómo usarlos con EF Core, [consulte la página de documentación dedicada para esta característica](xref:core/miscellaneous/nullable-reference-types).</span><span class="sxs-lookup"><span data-stu-id="2b2a4-150">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

### <a name="explicit-configuration"></a><span data-ttu-id="2b2a4-151">Configuración explícita</span><span class="sxs-lookup"><span data-stu-id="2b2a4-151">Explicit configuration</span></span>

<span data-ttu-id="2b2a4-152">Una propiedad que sería opcional por Convención se puede configurar para que sea necesaria de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="2b2a4-152">A property that would be optional by convention can be configured to be required as follows:</span></span>

#### <a name="data-annotationstabdata-annotations"></a>[<span data-ttu-id="2b2a4-153">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="2b2a4-153">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-apitabfluent-api"></a>[<span data-ttu-id="2b2a4-154">API fluida</span><span class="sxs-lookup"><span data-stu-id="2b2a4-154">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***