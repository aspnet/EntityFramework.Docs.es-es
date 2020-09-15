---
title: 'Propiedades de la entidad: EF Core'
description: Cómo configurar y asignar propiedades de entidad mediante Entity Framework Core
author: lajones
ms.date: 05/27/2020
uid: core/modeling/entity-properties
ms.openlocfilehash: 3b76454bde153d3a6ddd035cefa00a55f47d2d7b
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071503"
---
# <a name="entity-properties"></a><span data-ttu-id="933a1-103">Propiedades de entidad</span><span class="sxs-lookup"><span data-stu-id="933a1-103">Entity Properties</span></span>

<span data-ttu-id="933a1-104">Cada tipo de entidad del modelo tiene un conjunto de propiedades, que EF Core leerán y escribirán en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="933a1-104">Each entity type in your model has a set of properties, which EF Core will read and write from the database.</span></span> <span data-ttu-id="933a1-105">Si utiliza una base de datos relacional, las propiedades de entidad se asignan a las columnas de la tabla.</span><span class="sxs-lookup"><span data-stu-id="933a1-105">If you're using a relational database, entity properties map to table columns.</span></span>

## <a name="included-and-excluded-properties"></a><span data-ttu-id="933a1-106">Propiedades incluidas y excluidas</span><span class="sxs-lookup"><span data-stu-id="933a1-106">Included and excluded properties</span></span>

<span data-ttu-id="933a1-107">Por Convención, todas las propiedades públicas con un captador y un establecedor se incluirán en el modelo.</span><span class="sxs-lookup"><span data-stu-id="933a1-107">By convention, all public properties with a getter and a setter will be included in the model.</span></span>

<span data-ttu-id="933a1-108">Las propiedades específicas se pueden excluir de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="933a1-108">Specific properties can be excluded as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="933a1-109">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="933a1-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="933a1-110">API fluida</span><span class="sxs-lookup"><span data-stu-id="933a1-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a><span data-ttu-id="933a1-111">Nombres de columna</span><span class="sxs-lookup"><span data-stu-id="933a1-111">Column names</span></span>

<span data-ttu-id="933a1-112">Por Convención, cuando se utiliza una base de datos relacional, las propiedades de entidad se asignan a las columnas de la tabla que tienen el mismo nombre que la propiedad.</span><span class="sxs-lookup"><span data-stu-id="933a1-112">By convention, when using a relational database, entity properties are mapped to table columns having the same name as the property.</span></span>

<span data-ttu-id="933a1-113">Si prefiere configurar las columnas con nombres diferentes, puede hacerlo como fragmento de código siguiente:</span><span class="sxs-lookup"><span data-stu-id="933a1-113">If you prefer to configure your columns with different names, you can do so as following code snippet:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="933a1-114">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="933a1-114">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="933a1-115">API fluida</span><span class="sxs-lookup"><span data-stu-id="933a1-115">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

***

## <a name="column-data-types"></a><span data-ttu-id="933a1-116">Tipos de datos de columna</span><span class="sxs-lookup"><span data-stu-id="933a1-116">Column data types</span></span>

<span data-ttu-id="933a1-117">Al utilizar una base de datos relacional, el proveedor de base de datos selecciona un tipo de datos basado en el tipo .NET de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="933a1-117">When using a relational database, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="933a1-118">También tiene en cuenta otros metadatos, como la [longitud máxima](#maximum-length)configurada, si la propiedad forma parte de una clave principal, etc.</span><span class="sxs-lookup"><span data-stu-id="933a1-118">It also takes into account other metadata, such as the configured [maximum length](#maximum-length), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="933a1-119">Por ejemplo, SQL Server asigna `DateTime` propiedades a `datetime2(7)` las columnas y `string` las propiedades a `nvarchar(max)` las columnas (o a `nvarchar(450)` para las propiedades que se usan como clave).</span><span class="sxs-lookup"><span data-stu-id="933a1-119">For example, SQL Server maps `DateTime` properties to `datetime2(7)` columns, and `string` properties to `nvarchar(max)` columns (or to `nvarchar(450)` for properties that are used as a key).</span></span>

<span data-ttu-id="933a1-120">También puede configurar las columnas para especificar un tipo de datos exacto para una columna.</span><span class="sxs-lookup"><span data-stu-id="933a1-120">You can also configure your columns to specify an exact data type for a column.</span></span> <span data-ttu-id="933a1-121">Por ejemplo, el código siguiente configura `Url` como una cadena no Unicode con una longitud máxima de `200` y `Rating` como decimal con la precisión y la `5` escala de `2` :</span><span class="sxs-lookup"><span data-stu-id="933a1-121">For example, the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="933a1-122">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="933a1-122">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[<span data-ttu-id="933a1-123">API fluida</span><span class="sxs-lookup"><span data-stu-id="933a1-123">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

***

### <a name="maximum-length"></a><span data-ttu-id="933a1-124">Longitud máxima</span><span class="sxs-lookup"><span data-stu-id="933a1-124">Maximum length</span></span>

<span data-ttu-id="933a1-125">La configuración de una longitud máxima proporciona una sugerencia al proveedor de base de datos sobre el tipo de datos de columna adecuado que se debe elegir para una propiedad determinada.</span><span class="sxs-lookup"><span data-stu-id="933a1-125">Configuring a maximum length provides a hint to the database provider about the appropriate column data type to choose for a given property.</span></span> <span data-ttu-id="933a1-126">La longitud máxima solo se aplica a los tipos de datos de matriz, como `string` y `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="933a1-126">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]
> <span data-ttu-id="933a1-127">Entity Framework no realiza ninguna validación de la longitud máxima antes de pasar datos al proveedor.</span><span class="sxs-lookup"><span data-stu-id="933a1-127">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="933a1-128">Depende del proveedor o del almacén de datos que se valide si es necesario.</span><span class="sxs-lookup"><span data-stu-id="933a1-128">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="933a1-129">Por ejemplo, cuando el destino es SQL Server, si se supera la longitud máxima, se producirá una excepción, ya que el tipo de datos de la columna subyacente no permitirá que se almacenen los datos sobrantes.</span><span class="sxs-lookup"><span data-stu-id="933a1-129">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

<span data-ttu-id="933a1-130">En el ejemplo siguiente, la configuración de una longitud máxima de 500 hará que se cree una columna de tipo `nvarchar(500)` en SQL Server:</span><span class="sxs-lookup"><span data-stu-id="933a1-130">In the following example, configuring a maximum length of 500 will cause a column of type `nvarchar(500)` to be created on SQL Server:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="933a1-131">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="933a1-131">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="933a1-132">API fluida</span><span class="sxs-lookup"><span data-stu-id="933a1-132">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

***

### <a name="precision-and-scale"></a><span data-ttu-id="933a1-133">Precisión y escala</span><span class="sxs-lookup"><span data-stu-id="933a1-133">Precision and Scale</span></span>

<span data-ttu-id="933a1-134">A partir de EFCore 5,0, puede usar la API fluida para configurar la precisión y la escala.</span><span class="sxs-lookup"><span data-stu-id="933a1-134">Starting with EFCore 5.0, you can use fluent API to configure the precision and scale.</span></span> <span data-ttu-id="933a1-135">Indica al proveedor de base de datos cuánto espacio de almacenamiento se necesita para una columna determinada.</span><span class="sxs-lookup"><span data-stu-id="933a1-135">It tells the database provider how much storage is needed for a given column.</span></span> <span data-ttu-id="933a1-136">Solo se aplica a los tipos de datos en los que el proveedor permite que la precisión y la escala varíen, normalmente `decimal` y `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="933a1-136">It only applies to data types where the provider allows the precision and scale to vary - usually `decimal` and `DateTime`.</span></span>

<span data-ttu-id="933a1-137">En `decimal` el caso de las propiedades, precisión define el número máximo de dígitos necesarios para expresar cualquier valor que contenga la columna y escala define el número máximo de posiciones decimales necesarias.</span><span class="sxs-lookup"><span data-stu-id="933a1-137">For `decimal` properties, precision defines the maximum number of digits needed to express any value the column will contain, and scale defines the maximum number of decimal places needed.</span></span> <span data-ttu-id="933a1-138">En `DateTime` el caso de las propiedades, precisión define el número máximo de dígitos necesarios para expresar fracciones de segundos y no se usa la escala.</span><span class="sxs-lookup"><span data-stu-id="933a1-138">For `DateTime` properties, precision defines the maximum number of digits needed to express fractions of seconds, and scale is not used.</span></span>

> [!NOTE]
> <span data-ttu-id="933a1-139">Entity Framework no realiza ninguna validación de precisión o escala antes de pasar los datos al proveedor.</span><span class="sxs-lookup"><span data-stu-id="933a1-139">Entity Framework does not do any validation of precision or scale before passing data to the provider.</span></span> <span data-ttu-id="933a1-140">Depende del proveedor o del almacén de datos que se validen según corresponda.</span><span class="sxs-lookup"><span data-stu-id="933a1-140">It is up to the provider or data store to validate as appropriate.</span></span> <span data-ttu-id="933a1-141">Por ejemplo, cuando el destino es SQL Server, una columna de tipo de datos no `datetime` permite establecer la precisión, mientras que una `datetime2` puede tener una precisión de entre 0 y 7, ambos inclusive.</span><span class="sxs-lookup"><span data-stu-id="933a1-141">For example, when targeting SQL Server, a column of data type `datetime` does not allow the precision to be set, whereas a `datetime2` one can have precision between 0 and 7 inclusive.</span></span>

<span data-ttu-id="933a1-142">En el ejemplo siguiente, la configuración de la `Score` propiedad para que tenga la precisión 14 y la escala 2 hará que se cree una columna de tipo `decimal(14,2)` en SQL Server y la configuración de la `LastUpdated` propiedad para que tenga la precisión 3 producirá una columna de tipo `datetime2(3)` :</span><span class="sxs-lookup"><span data-stu-id="933a1-142">In the following example, configuring the `Score` property to have precision 14 and scale 2 will cause a column of type `decimal(14,2)` to be created on SQL Server, and configuring the `LastUpdated` property to have precision 3 will cause a column of type `datetime2(3)`:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="933a1-143">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="933a1-143">Data Annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="933a1-144">Actualmente no es posible usar anotaciones de datos para configurar.</span><span class="sxs-lookup"><span data-stu-id="933a1-144">Currently not possible to use data annotations to configure.</span></span>

#### <a name="fluent-api"></a>[<span data-ttu-id="933a1-145">API fluida</span><span class="sxs-lookup"><span data-stu-id="933a1-145">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> <span data-ttu-id="933a1-146">La escala nunca se define sin necesidad de definir primero la precisión, por lo que la API fluida para definir la escala es `HasPrecision(precision, scale)` .</span><span class="sxs-lookup"><span data-stu-id="933a1-146">Scale is never defined without first defining precision, so the Fluent API for defining the scale is `HasPrecision(precision, scale)`.</span></span>

***

## <a name="required-and-optional-properties"></a><span data-ttu-id="933a1-147">Propiedades obligatorias y opcionales</span><span class="sxs-lookup"><span data-stu-id="933a1-147">Required and optional properties</span></span>

<span data-ttu-id="933a1-148">Una propiedad se considera opcional si es válida para que la contenga `null` .</span><span class="sxs-lookup"><span data-stu-id="933a1-148">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="933a1-149">Si `null` no es un valor válido que se va a asignar a una propiedad, se considera que es una propiedad obligatoria.</span><span class="sxs-lookup"><span data-stu-id="933a1-149">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span> <span data-ttu-id="933a1-150">Al asignar a un esquema de base de datos relacional, las propiedades requeridas se crean como columnas que no aceptan valores NULL y las propiedades opcionales se crean como columnas que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="933a1-150">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

### <a name="conventions"></a><span data-ttu-id="933a1-151">Convenciones</span><span class="sxs-lookup"><span data-stu-id="933a1-151">Conventions</span></span>

<span data-ttu-id="933a1-152">Por Convención, una propiedad cuyo tipo .NET pueda contener NULL se configurará como opcional, mientras que las propiedades cuyo tipo .NET no puede contener valores NULL se configurarán según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="933a1-152">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="933a1-153">Por ejemplo, todas las propiedades con tipos de valor .net ( `int` , `decimal` , `bool` , etc.) se configuran como necesario y todas las propiedades con tipos de valor de .net que aceptan valores NULL ( `int?` , `decimal?` , `bool?` , etc.) se configuran como opcionales.</span><span class="sxs-lookup"><span data-stu-id="933a1-153">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="933a1-154">C# 8 presentó una nueva característica denominada [tipos de referencia que aceptan valores NULL](/dotnet/csharp/tutorials/nullable-reference-types), que permite anotar tipos de referencia, lo que indica si es válido que contengan null o not.</span><span class="sxs-lookup"><span data-stu-id="933a1-154">C# 8 introduced a new feature called [nullable reference types](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="933a1-155">Esta característica está deshabilitada de forma predeterminada y, si está habilitada, modifica el comportamiento de EF Core de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="933a1-155">This feature is disabled by default, and if enabled, it modifies EF Core's behavior in the following way:</span></span>

* <span data-ttu-id="933a1-156">Si los tipos de referencia que aceptan valores NULL están deshabilitados (el valor predeterminado), todas las propiedades con tipos de referencia de .NET se configuran como opcionales por Convención (por ejemplo, `string` ).</span><span class="sxs-lookup"><span data-stu-id="933a1-156">If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (for example, `string`).</span></span>
* <span data-ttu-id="933a1-157">Si los tipos de referencia que aceptan valores NULL están habilitados, las propiedades se configurarán según la nulabilidad de C# de su tipo .NET: se `string?` configurarán como opcionales, pero se `string` configurarán según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="933a1-157">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, but `string` will be configured as required.</span></span>

<span data-ttu-id="933a1-158">En el ejemplo siguiente se muestra un tipo de entidad con propiedades obligatorias y opcionales, con la característica de referencia que acepta valores NULL deshabilitada (valor predeterminado) y habilitada:</span><span class="sxs-lookup"><span data-stu-id="933a1-158">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

#### <a name="without-nullable-reference-types-default"></a>[<span data-ttu-id="933a1-159">Sin tipos de referencia que aceptan valores NULL (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="933a1-159">Without nullable reference types (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nullable-reference-types"></a>[<span data-ttu-id="933a1-160">Con tipos de referencia que aceptan valores NULL</span><span class="sxs-lookup"><span data-stu-id="933a1-160">With nullable reference types</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="933a1-161">Se recomienda el uso de tipos de referencia que aceptan valores NULL, ya que fluye la nulabilidad expresada en el código de C# para EF Core modelo de y a la base de datos, e obvia el uso de las anotaciones de datos o la API fluida para expresar el mismo concepto dos veces.</span><span class="sxs-lookup"><span data-stu-id="933a1-161">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="933a1-162">Tenga cuidado al habilitar los tipos de referencia que aceptan valores NULL en un proyecto existente: las propiedades de tipo de referencia que se configuraron anteriormente como opcional ahora se configurarán según sea necesario, a menos que se anoten explícitamente para que acepten valores NULL.</span><span class="sxs-lookup"><span data-stu-id="933a1-162">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="933a1-163">Al administrar un esquema de base de datos relacional, esto puede provocar que se generen migraciones que modifiquen la nulabilidad de la columna de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="933a1-163">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="933a1-164">Para obtener más información sobre los tipos de referencia que aceptan valores NULL y cómo usarlos con EF Core, [consulte la página de documentación dedicada para esta característica](xref:core/miscellaneous/nullable-reference-types).</span><span class="sxs-lookup"><span data-stu-id="933a1-164">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

### <a name="explicit-configuration"></a><span data-ttu-id="933a1-165">Configuración explícita</span><span class="sxs-lookup"><span data-stu-id="933a1-165">Explicit configuration</span></span>

<span data-ttu-id="933a1-166">Una propiedad que sería opcional por Convención se puede configurar para que sea necesaria de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="933a1-166">A property that would be optional by convention can be configured to be required as follows:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="933a1-167">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="933a1-167">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="933a1-168">API fluida</span><span class="sxs-lookup"><span data-stu-id="933a1-168">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a><span data-ttu-id="933a1-169">Intercalaciones de columna</span><span class="sxs-lookup"><span data-stu-id="933a1-169">Column collations</span></span>

> [!NOTE]
> <span data-ttu-id="933a1-170">Esta característica se incluye por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="933a1-170">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="933a1-171">Una intercalación se puede definir en columnas de texto, determinando cómo se comparan y ordenan.</span><span class="sxs-lookup"><span data-stu-id="933a1-171">A collation can be defined on text columns, determining how they are compared and ordered.</span></span> <span data-ttu-id="933a1-172">Por ejemplo, el siguiente fragmento de código configura una columna de SQL Server para que no distinga entre mayúsculas y minúsculas:</span><span class="sxs-lookup"><span data-stu-id="933a1-172">For example, the following code snippet configures a SQL Server column to be case-insensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

<span data-ttu-id="933a1-173">Si todas las columnas de una base de datos necesitan usar una intercalación determinada, defina la intercalación en el nivel de base de datos en su lugar.</span><span class="sxs-lookup"><span data-stu-id="933a1-173">If all columns in a database need to use a certain collation, define the collation at the database level instead.</span></span>

<span data-ttu-id="933a1-174">Puede encontrar información general sobre la compatibilidad de EF Core con las intercalaciones en la página de documentación de la [Intercalación](xref:core/miscellaneous/collations-and-case-sensitivity).</span><span class="sxs-lookup"><span data-stu-id="933a1-174">General information about EF Core support for collations can be found in the [collation documentation page](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>
