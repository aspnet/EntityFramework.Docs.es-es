---
title: 'Propiedades de la entidad: EF Core'
description: Cómo configurar y asignar propiedades de entidad mediante Entity Framework Core
author: roji
ms.date: 05/27/2020
uid: core/modeling/entity-properties
ms.openlocfilehash: 55c6f31543d4ce3257cf203eaf9fd2191301ea7e
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429603"
---
# <a name="entity-properties"></a><span data-ttu-id="8ebe5-103">Propiedades de entidad</span><span class="sxs-lookup"><span data-stu-id="8ebe5-103">Entity Properties</span></span>

<span data-ttu-id="8ebe5-104">Cada tipo de entidad del modelo tiene un conjunto de propiedades, que EF Core leerán y escribirán en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-104">Each entity type in your model has a set of properties, which EF Core will read and write from the database.</span></span> <span data-ttu-id="8ebe5-105">Si utiliza una base de datos relacional, las propiedades de entidad se asignan a las columnas de la tabla.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-105">If you're using a relational database, entity properties map to table columns.</span></span>

## <a name="included-and-excluded-properties"></a><span data-ttu-id="8ebe5-106">Propiedades incluidas y excluidas</span><span class="sxs-lookup"><span data-stu-id="8ebe5-106">Included and excluded properties</span></span>

<span data-ttu-id="8ebe5-107">Por Convención, todas las propiedades públicas con un captador y un establecedor se incluirán en el modelo.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-107">By convention, all public properties with a getter and a setter will be included in the model.</span></span>

<span data-ttu-id="8ebe5-108">Las propiedades específicas se pueden excluir de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="8ebe5-108">Specific properties can be excluded as follows:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="8ebe5-109">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="8ebe5-109">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/IgnoreProperty.cs?name=IgnoreProperty&highlight=6)]

### <a name="fluent-api"></a>[<span data-ttu-id="8ebe5-110">API fluida</span><span class="sxs-lookup"><span data-stu-id="8ebe5-110">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/IgnoreProperty.cs?name=IgnoreProperty&highlight=3,4)]

***

## <a name="column-names"></a><span data-ttu-id="8ebe5-111">Nombres de columna</span><span class="sxs-lookup"><span data-stu-id="8ebe5-111">Column names</span></span>

<span data-ttu-id="8ebe5-112">Por Convención, cuando se utiliza una base de datos relacional, las propiedades de entidad se asignan a las columnas de la tabla que tienen el mismo nombre que la propiedad.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-112">By convention, when using a relational database, entity properties are mapped to table columns having the same name as the property.</span></span>

<span data-ttu-id="8ebe5-113">Si prefiere configurar las columnas con nombres diferentes, puede hacerlo como fragmento de código siguiente:</span><span class="sxs-lookup"><span data-stu-id="8ebe5-113">If you prefer to configure your columns with different names, you can do so as following code snippet:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="8ebe5-114">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="8ebe5-114">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnName.cs?Name=ColumnName&highlight=3)]

### <a name="fluent-api"></a>[<span data-ttu-id="8ebe5-115">API fluida</span><span class="sxs-lookup"><span data-stu-id="8ebe5-115">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnName.cs?Name=ColumnName&highlight=3-5)]

<span data-ttu-id="8ebe5-116">\*\*_</span><span class="sxs-lookup"><span data-stu-id="8ebe5-116">\*\*_</span></span>

## <a name="column-data-types"></a><span data-ttu-id="8ebe5-117">Tipos de datos de columna</span><span class="sxs-lookup"><span data-stu-id="8ebe5-117">Column data types</span></span>

<span data-ttu-id="8ebe5-118">Al utilizar una base de datos relacional, el proveedor de base de datos selecciona un tipo de datos basado en el tipo .NET de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-118">When using a relational database, the database provider selects a data type based on the .NET type of the property.</span></span> <span data-ttu-id="8ebe5-119">También tiene en cuenta otros metadatos, como la [longitud máxima](#maximum-length)configurada, si la propiedad forma parte de una clave principal, etc.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-119">It also takes into account other metadata, such as the configured [maximum length](#maximum-length), whether the property is part of a primary key, etc.</span></span>

<span data-ttu-id="8ebe5-120">Por ejemplo, SQL Server asigna `DateTime` propiedades a `datetime2(7)` las columnas y `string` las propiedades a `nvarchar(max)` las columnas (o a `nvarchar(450)` para las propiedades que se usan como clave).</span><span class="sxs-lookup"><span data-stu-id="8ebe5-120">For example, SQL Server maps `DateTime` properties to `datetime2(7)` columns, and `string` properties to `nvarchar(max)` columns (or to `nvarchar(450)` for properties that are used as a key).</span></span>

<span data-ttu-id="8ebe5-121">También puede configurar las columnas para especificar un tipo de datos exacto para una columna.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-121">You can also configure your columns to specify an exact data type for a column.</span></span> <span data-ttu-id="8ebe5-122">Por ejemplo, el código siguiente configura `Url` como una cadena no Unicode con una longitud máxima de `200` y `Rating` como decimal con la precisión y la `5` escala de `2` :</span><span class="sxs-lookup"><span data-stu-id="8ebe5-122">For example, the following code configures `Url` as a non-unicode string with maximum length of `200` and `Rating` as decimal with precision of `5` and scale of `2`:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="8ebe5-123">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="8ebe5-123">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/ColumnDataType.cs?name=ColumnDataType&highlight=4,6)]

### <a name="fluent-api"></a>[<span data-ttu-id="8ebe5-124">API fluida</span><span class="sxs-lookup"><span data-stu-id="8ebe5-124">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ColumnDataType.cs?name=ColumnDataType&highlight=5-6)]

_*_

### <a name="maximum-length"></a><span data-ttu-id="8ebe5-125">Longitud máxima</span><span class="sxs-lookup"><span data-stu-id="8ebe5-125">Maximum length</span></span>

<span data-ttu-id="8ebe5-126">La configuración de una longitud máxima proporciona una sugerencia al proveedor de base de datos sobre el tipo de datos de columna adecuado que se debe elegir para una propiedad determinada.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-126">Configuring a maximum length provides a hint to the database provider about the appropriate column data type to choose for a given property.</span></span> <span data-ttu-id="8ebe5-127">La longitud máxima solo se aplica a los tipos de datos de matriz, como `string` y `byte[]` .</span><span class="sxs-lookup"><span data-stu-id="8ebe5-127">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]
> <span data-ttu-id="8ebe5-128">Entity Framework no realiza ninguna validación de la longitud máxima antes de pasar datos al proveedor.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-128">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="8ebe5-129">Depende del proveedor o del almacén de datos que se valide si es necesario.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-129">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="8ebe5-130">Por ejemplo, cuando el destino es SQL Server, si se supera la longitud máxima, se producirá una excepción, ya que el tipo de datos de la columna subyacente no permitirá que se almacenen los datos sobrantes.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-130">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

<span data-ttu-id="8ebe5-131">En el ejemplo siguiente, la configuración de una longitud máxima de 500 hará que se cree una columna de tipo `nvarchar(500)` en SQL Server:</span><span class="sxs-lookup"><span data-stu-id="8ebe5-131">In the following example, configuring a maximum length of 500 will cause a column of type `nvarchar(500)` to be created on SQL Server:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="8ebe5-132">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="8ebe5-132">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?name=MaxLength&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="8ebe5-133">API fluida</span><span class="sxs-lookup"><span data-stu-id="8ebe5-133">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?name=MaxLength&highlight=3-5)]

_*_

### <a name="precision-and-scale"></a><span data-ttu-id="8ebe5-134">Precisión y escala</span><span class="sxs-lookup"><span data-stu-id="8ebe5-134">Precision and Scale</span></span>

<span data-ttu-id="8ebe5-135">A partir de EFCore 5,0, puede usar la API fluida para configurar la precisión y la escala.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-135">Starting with EFCore 5.0, you can use fluent API to configure the precision and scale.</span></span> <span data-ttu-id="8ebe5-136">Indica al proveedor de base de datos cuánto espacio de almacenamiento se necesita para una columna determinada.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-136">It tells the database provider how much storage is needed for a given column.</span></span> <span data-ttu-id="8ebe5-137">Solo se aplica a los tipos de datos en los que el proveedor permite que la precisión y la escala varíen, normalmente `decimal` y `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="8ebe5-137">It only applies to data types where the provider allows the precision and scale to vary - usually `decimal` and `DateTime`.</span></span>

<span data-ttu-id="8ebe5-138">En `decimal` el caso de las propiedades, precisión define el número máximo de dígitos necesarios para expresar cualquier valor que contenga la columna y escala define el número máximo de posiciones decimales necesarias.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-138">For `decimal` properties, precision defines the maximum number of digits needed to express any value the column will contain, and scale defines the maximum number of decimal places needed.</span></span> <span data-ttu-id="8ebe5-139">En `DateTime` el caso de las propiedades, precisión define el número máximo de dígitos necesarios para expresar fracciones de segundos y no se usa la escala.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-139">For `DateTime` properties, precision defines the maximum number of digits needed to express fractions of seconds, and scale is not used.</span></span>

> [!NOTE]
> <span data-ttu-id="8ebe5-140">Entity Framework no realiza ninguna validación de precisión o escala antes de pasar los datos al proveedor.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-140">Entity Framework does not do any validation of precision or scale before passing data to the provider.</span></span> <span data-ttu-id="8ebe5-141">Depende del proveedor o del almacén de datos que se validen según corresponda.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-141">It is up to the provider or data store to validate as appropriate.</span></span> <span data-ttu-id="8ebe5-142">Por ejemplo, cuando el destino es SQL Server, una columna de tipo de datos no `datetime` permite establecer la precisión, mientras que una `datetime2` puede tener una precisión de entre 0 y 7, ambos inclusive.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-142">For example, when targeting SQL Server, a column of data type `datetime` does not allow the precision to be set, whereas a `datetime2` one can have precision between 0 and 7 inclusive.</span></span>

<span data-ttu-id="8ebe5-143">En el ejemplo siguiente, la configuración de la `Score` propiedad para que tenga la precisión 14 y la escala 2 hará que se cree una columna de tipo `decimal(14,2)` en SQL Server y la configuración de la `LastUpdated` propiedad para que tenga la precisión 3 producirá una columna de tipo `datetime2(3)` :</span><span class="sxs-lookup"><span data-stu-id="8ebe5-143">In the following example, configuring the `Score` property to have precision 14 and scale 2 will cause a column of type `decimal(14,2)` to be created on SQL Server, and configuring the `LastUpdated` property to have precision 3 will cause a column of type `datetime2(3)`:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="8ebe5-144">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="8ebe5-144">Data Annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="8ebe5-145">La precisión y la escala no se pueden configurar actualmente a través de anotaciones de datos.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-145">Precision and scale cannot currently be configured via data annotations.</span></span>

#### <a name="fluent-api"></a>[<span data-ttu-id="8ebe5-146">API fluida</span><span class="sxs-lookup"><span data-stu-id="8ebe5-146">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/PrecisionAndScale.cs?name=PrecisionAndScale&highlight=3-9)]

> [!NOTE]
> <span data-ttu-id="8ebe5-147">La escala nunca se define sin necesidad de definir primero la precisión, por lo que la API fluida para definir la escala es `HasPrecision(precision, scale)` .</span><span class="sxs-lookup"><span data-stu-id="8ebe5-147">Scale is never defined without first defining precision, so the Fluent API for defining the scale is `HasPrecision(precision, scale)`.</span></span>

_*_

## <a name="required-and-optional-properties"></a><span data-ttu-id="8ebe5-148">Propiedades obligatorias y opcionales</span><span class="sxs-lookup"><span data-stu-id="8ebe5-148">Required and optional properties</span></span>

<span data-ttu-id="8ebe5-149">Una propiedad se considera opcional si es válida para que la contenga `null` .</span><span class="sxs-lookup"><span data-stu-id="8ebe5-149">A property is considered optional if it is valid for it to contain `null`.</span></span> <span data-ttu-id="8ebe5-150">Si `null` no es un valor válido que se va a asignar a una propiedad, se considera que es una propiedad obligatoria.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-150">If `null` is not a valid value to be assigned to a property then it is considered to be a required property.</span></span> <span data-ttu-id="8ebe5-151">Al asignar a un esquema de base de datos relacional, las propiedades requeridas se crean como columnas que no aceptan valores NULL y las propiedades opcionales se crean como columnas que aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-151">When mapping to a relational database schema, required properties are created as non-nullable columns, and optional properties are created as nullable columns.</span></span>

### <a name="conventions"></a><span data-ttu-id="8ebe5-152">Convenciones</span><span class="sxs-lookup"><span data-stu-id="8ebe5-152">Conventions</span></span>

<span data-ttu-id="8ebe5-153">Por Convención, una propiedad cuyo tipo .NET pueda contener NULL se configurará como opcional, mientras que las propiedades cuyo tipo .NET no puede contener valores NULL se configurarán según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-153">By convention, a property whose .NET type can contain null will be configured as optional, whereas properties whose .NET type cannot contain null will be configured as required.</span></span> <span data-ttu-id="8ebe5-154">Por ejemplo, todas las propiedades con tipos de valor .net ( `int` , `decimal` , `bool` , etc.) se configuran como necesario y todas las propiedades con tipos de valor de .net que aceptan valores NULL ( `int?` , `decimal?` , `bool?` , etc.) se configuran como opcionales.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-154">For example, all properties with .NET value types (`int`, `decimal`, `bool`, etc.) are configured as required, and all properties with nullable .NET value types (`int?`, `decimal?`, `bool?`, etc.) are configured as optional.</span></span>

<span data-ttu-id="8ebe5-155">C# 8 presentó una nueva característica denominada [tipos de referencia que aceptan valores NULL (NRT)](/dotnet/csharp/tutorials/nullable-reference-types), que permite anotar tipos de referencia, lo que indica si es válido para que contengan null o not.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-155">C# 8 introduced a new feature called [nullable reference types (NRT)](/dotnet/csharp/tutorials/nullable-reference-types), which allows reference types to be annotated, indicating whether it is valid for them to contain null or not.</span></span> <span data-ttu-id="8ebe5-156">Esta característica está deshabilitada de forma predeterminada y afecta al comportamiento del EF Core de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="8ebe5-156">This feature is disabled by default, and affects EF Core's behavior in the following way:</span></span>

<span data-ttu-id="8ebe5-157">_ Si los tipos de referencia que aceptan valores NULL están deshabilitados (el valor predeterminado), todas las propiedades con tipos de referencia de .NET se configuran como opcionales por Convención (por ejemplo, `string` ).</span><span class="sxs-lookup"><span data-stu-id="8ebe5-157">_ If nullable reference types are disabled (the default), all properties with .NET reference types are configured as optional by convention (for example, `string`).</span></span>
* <span data-ttu-id="8ebe5-158">Si los tipos de referencia que aceptan valores NULL están habilitados, las propiedades se configurarán según la nulabilidad de C# de su tipo .NET: se `string?` configurarán como opcionales, pero se `string` configurarán según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-158">If nullable reference types are enabled, properties will be configured based on the C# nullability of their .NET type: `string?` will be configured as optional, but `string` will be configured as required.</span></span>

<span data-ttu-id="8ebe5-159">En el ejemplo siguiente se muestra un tipo de entidad con propiedades obligatorias y opcionales, con la característica de referencia que acepta valores NULL deshabilitada (valor predeterminado) y habilitada:</span><span class="sxs-lookup"><span data-stu-id="8ebe5-159">The following example shows an entity type with required and optional properties, with the nullable reference feature disabled (the default) and enabled:</span></span>

#### <a name="without-nrt-default"></a>[<span data-ttu-id="8ebe5-160">Sin NRT (valor predeterminado)</span><span class="sxs-lookup"><span data-stu-id="8ebe5-160">Without NRT (default)</span></span>](#tab/without-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/CustomerWithoutNullableReferenceTypes.cs?name=Customer&highlight=4-8)]

#### <a name="with-nrt"></a>[<span data-ttu-id="8ebe5-161">Con NRT</span><span class="sxs-lookup"><span data-stu-id="8ebe5-161">With NRT</span></span>](#tab/with-nrt)

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Customer.cs?name=Customer&highlight=4-6)]

***

<span data-ttu-id="8ebe5-162">Se recomienda el uso de tipos de referencia que aceptan valores NULL, ya que fluye la nulabilidad expresada en el código de C# para EF Core modelo de y a la base de datos, e obvia el uso de las anotaciones de datos o la API fluida para expresar el mismo concepto dos veces.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-162">Using nullable reference types is recommended since it flows the nullability expressed in C# code to EF Core's model and to the database, and obviates the use of the Fluent API or Data Annotations to express the same concept twice.</span></span>

> [!NOTE]
> <span data-ttu-id="8ebe5-163">Tenga cuidado al habilitar los tipos de referencia que aceptan valores NULL en un proyecto existente: las propiedades de tipo de referencia que se configuraron anteriormente como opcional ahora se configurarán según sea necesario, a menos que se anoten explícitamente para que acepten valores NULL.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-163">Exercise caution when enabling nullable reference types on an existing project: reference type properties which were previously configured as optional will now be configured as required, unless they are explicitly annotated to be nullable.</span></span> <span data-ttu-id="8ebe5-164">Al administrar un esquema de base de datos relacional, esto puede provocar que se generen migraciones que modifiquen la nulabilidad de la columna de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-164">When managing a relational database schema, this may cause migrations to be generated which alter the database column's nullability.</span></span>

<span data-ttu-id="8ebe5-165">Para obtener más información sobre los tipos de referencia que aceptan valores NULL y cómo usarlos con EF Core, [consulte la página de documentación dedicada para esta característica](xref:core/miscellaneous/nullable-reference-types).</span><span class="sxs-lookup"><span data-stu-id="8ebe5-165">For more information on nullable reference types and how to use them with EF Core, [see the dedicated documentation page for this feature](xref:core/miscellaneous/nullable-reference-types).</span></span>

### <a name="explicit-configuration"></a><span data-ttu-id="8ebe5-166">Configuración explícita</span><span class="sxs-lookup"><span data-stu-id="8ebe5-166">Explicit configuration</span></span>

<span data-ttu-id="8ebe5-167">Una propiedad que sería opcional por Convención se puede configurar para que sea necesaria de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="8ebe5-167">A property that would be optional by convention can be configured to be required as follows:</span></span>

#### <a name="data-annotations"></a>[<span data-ttu-id="8ebe5-168">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="8ebe5-168">Data Annotations</span></span>](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?name=Required&highlight=4)]

#### <a name="fluent-api"></a>[<span data-ttu-id="8ebe5-169">API fluida</span><span class="sxs-lookup"><span data-stu-id="8ebe5-169">Fluent API</span></span>](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?name=Required&highlight=3-5)]

***

## <a name="column-collations"></a><span data-ttu-id="8ebe5-170">Intercalaciones de columna</span><span class="sxs-lookup"><span data-stu-id="8ebe5-170">Column collations</span></span>

> [!NOTE]
> <span data-ttu-id="8ebe5-171">Esta característica se incluye por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-171">This feature is introduced in EF Core 5.0.</span></span>

<span data-ttu-id="8ebe5-172">Una intercalación se puede definir en columnas de texto, determinando cómo se comparan y ordenan.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-172">A collation can be defined on text columns, determining how they are compared and ordered.</span></span> <span data-ttu-id="8ebe5-173">Por ejemplo, el siguiente fragmento de código configura una columna de SQL Server para que no distinga entre mayúsculas y minúsculas:</span><span class="sxs-lookup"><span data-stu-id="8ebe5-173">For example, the following code snippet configures a SQL Server column to be case-insensitive:</span></span>

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=42-43)]

<span data-ttu-id="8ebe5-174">Si todas las columnas de una base de datos necesitan usar una intercalación determinada, defina la intercalación en el nivel de base de datos en su lugar.</span><span class="sxs-lookup"><span data-stu-id="8ebe5-174">If all columns in a database need to use a certain collation, define the collation at the database level instead.</span></span>

<span data-ttu-id="8ebe5-175">Puede encontrar información general sobre la compatibilidad de EF Core con las intercalaciones en la página de documentación de la [Intercalación](xref:core/miscellaneous/collations-and-case-sensitivity).</span><span class="sxs-lookup"><span data-stu-id="8ebe5-175">General information about EF Core support for collations can be found in the [collation documentation page](xref:core/miscellaneous/collations-and-case-sensitivity).</span></span>
