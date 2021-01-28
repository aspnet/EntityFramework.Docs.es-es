---
title: Conversiones de valores-EF Core
description: Configuración de convertidores de valores en un modelo de Entity Framework Core
author: ajcvickers
ms.date: 01/16/2021
uid: core/modeling/value-conversions
ms.openlocfilehash: d9d3753c7f0b257a2109e4af1f587df913c15b44
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2021
ms.locfileid: "98983448"
---
# <a name="value-conversions"></a><span data-ttu-id="3f35b-103">Conversiones de valores</span><span class="sxs-lookup"><span data-stu-id="3f35b-103">Value Conversions</span></span>

<span data-ttu-id="3f35b-104">Los convertidores de valores permiten convertir los valores de propiedad al leer o escribir en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3f35b-104">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="3f35b-105">Esta conversión puede ser de un valor a otro del mismo tipo (por ejemplo, cifrar cadenas) o de un valor de un tipo a un valor de otro tipo (por ejemplo, convertir valores de enumeración en cadenas en la base de datos y desde ellas).</span><span class="sxs-lookup"><span data-stu-id="3f35b-105">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

> [!TIP]
> <span data-ttu-id="3f35b-106">Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span><span class="sxs-lookup"><span data-stu-id="3f35b-106">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Modeling/ValueConversions/).</span></span>

## <a name="overview"></a><span data-ttu-id="3f35b-107">Introducción</span><span class="sxs-lookup"><span data-stu-id="3f35b-107">Overview</span></span>

<span data-ttu-id="3f35b-108">Los convertidores de valores se especifican en términos de `ModelClrType` y `ProviderClrType` .</span><span class="sxs-lookup"><span data-stu-id="3f35b-108">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="3f35b-109">El tipo de modelo es el tipo .NET de la propiedad en el tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="3f35b-109">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="3f35b-110">El tipo de proveedor es el tipo .NET que entiende el proveedor de base de datos.</span><span class="sxs-lookup"><span data-stu-id="3f35b-110">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="3f35b-111">Por ejemplo, para guardar las enumeraciones como cadenas en la base de datos, el tipo de modelo es el tipo de la enumeración y el tipo de proveedor es `String` .</span><span class="sxs-lookup"><span data-stu-id="3f35b-111">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="3f35b-112">Estos dos tipos pueden ser iguales.</span><span class="sxs-lookup"><span data-stu-id="3f35b-112">These two types can be the same.</span></span>

<span data-ttu-id="3f35b-113">Las conversiones se definen mediante dos `Func` árboles de expresión: uno de `ModelClrType` a `ProviderClrType` y el otro de `ProviderClrType` a `ModelClrType` .</span><span class="sxs-lookup"><span data-stu-id="3f35b-113">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="3f35b-114">Los árboles de expresión se usan para que se puedan compilar en el delegado de acceso a la base de datos para conversiones eficaces.</span><span class="sxs-lookup"><span data-stu-id="3f35b-114">Expression trees are used so that they can be compiled into the database access delegate for efficient conversions.</span></span> <span data-ttu-id="3f35b-115">El árbol de expresión puede contener una llamada simple a un método de conversión para conversiones complejas.</span><span class="sxs-lookup"><span data-stu-id="3f35b-115">The expression tree may contain a simple call to a conversion method for complex conversions.</span></span>

> [!NOTE]
> <span data-ttu-id="3f35b-116">Una propiedad que se ha configurado para la conversión de valores también puede necesitar especificar un <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> .</span><span class="sxs-lookup"><span data-stu-id="3f35b-116">A property that has been configured for value conversion may also need to specify a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601>.</span></span> <span data-ttu-id="3f35b-117">Vea los ejemplos siguientes y la documentación de [comparadores de valores](xref:core/modeling/value-comparers) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="3f35b-117">See the examples below, and the [Value Comparers](xref:core/modeling/value-comparers) documentation for more information.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="3f35b-118">Configurar un convertidor de valores</span><span class="sxs-lookup"><span data-stu-id="3f35b-118">Configuring a value converter</span></span>

<span data-ttu-id="3f35b-119">Las conversiones de valores se configuran en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="3f35b-119">Value conversions are configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="3f35b-120">Por ejemplo, considere una enumeración y un tipo de entidad definidos como:</span><span class="sxs-lookup"><span data-stu-id="3f35b-120">For example, consider an enum and entity type defined as:</span></span>

<!--
        public class Rider
        {
            public int Id { get; set; }
            public EquineBeast Mount { get; set; }
        }

        public enum EquineBeast
        {
            Donkey,
            Mule,
            Horse,
            Unicorn
        }
-->
[!code-csharp[BeastAndRider](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=BeastAndRider)]

<span data-ttu-id="3f35b-121">Las conversiones se pueden configurar en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> para almacenar los valores de enumeración como cadenas como "Donkey", "Mule", etc. en la base de datos; solo tiene que proporcionar una función que convierta de `ModelClrType` a `ProviderClrType` y otra para la conversión opuesta:</span><span class="sxs-lookup"><span data-stu-id="3f35b-121">Conversions can be configured in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> to store the enum values as strings such as "Donkey", "Mule", etc. in the database; you simply need to provide one function which converts from the `ModelClrType` to the `ProviderClrType`, and another for the opposite conversion:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(
                        v => v.ToString(),
                        v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
            }
-->
[!code-csharp[ExplicitConversion](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ExplicitConversion)]

> [!NOTE]
> <span data-ttu-id="3f35b-122">`null`Nunca se pasará un valor a un convertidor de valores.</span><span class="sxs-lookup"><span data-stu-id="3f35b-122">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="3f35b-123">Un valor null en una columna de base de datos siempre es un valor null en la instancia de la entidad y viceversa.</span><span class="sxs-lookup"><span data-stu-id="3f35b-123">A null in a database column is always a null in the entity instance, and vice-versa.</span></span> <span data-ttu-id="3f35b-124">Esto hace que la implementación de conversiones sea más sencilla y permite que se compartan entre propiedades que aceptan valores NULL y que no aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="3f35b-124">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span> <span data-ttu-id="3f35b-125">Vea el [problema de GitHub #13850](https://github.com/dotnet/efcore/issues/13850) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="3f35b-125">See [GitHub issue #13850](https://github.com/dotnet/efcore/issues/13850) for more information.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="3f35b-126">Conversiones predefinidas</span><span class="sxs-lookup"><span data-stu-id="3f35b-126">Pre-defined conversions</span></span>

<span data-ttu-id="3f35b-127">EF Core contiene muchas conversiones predefinidas que evitan la necesidad de escribir funciones de conversión manualmente.</span><span class="sxs-lookup"><span data-stu-id="3f35b-127">EF Core contains many pre-defined conversions that avoid the need to write conversion functions manually.</span></span> <span data-ttu-id="3f35b-128">En su lugar, EF Core seleccionará la conversión que se va a usar en función del tipo de propiedad en el modelo y el tipo de proveedor de base de datos solicitado.</span><span class="sxs-lookup"><span data-stu-id="3f35b-128">Instead, EF Core will pick the conversion to use based on the property type in the model and the requested database provider type.</span></span>

<span data-ttu-id="3f35b-129">Por ejemplo, la enumeración de las conversiones de cadenas se usa como ejemplo anterior, pero EF Core realmente lo hará automáticamente cuando el tipo de proveedor esté configurado como `string` con el tipo genérico de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> :</span><span class="sxs-lookup"><span data-stu-id="3f35b-129">For example, enum to string conversions are used as an example above, but EF Core will actually do this automatically when the provider type is configured as `string` using the generic type of <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A>:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion<string>();
            }
-->
[!code-csharp[ConversionByClrType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByClrType)]

<span data-ttu-id="3f35b-130">Lo mismo se puede lograr especificando explícitamente el tipo de columna de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3f35b-130">The same thing can be achieved by explicitly specifying the database column type.</span></span> <span data-ttu-id="3f35b-131">Por ejemplo, si el tipo de entidad se define de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="3f35b-131">For example, if the entity type is defined like so:</span></span>

### <a name="data-annotations"></a>[<span data-ttu-id="3f35b-132">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="3f35b-132">Data Annotations</span></span>](#tab/data-annotations)

<!--
        public class Rider2
        {
            public int Id { get; set; }

            [Column(TypeName = "nvarchar(24)")]
            public EquineBeast Mount { get; set; }
        }
-->
[!code-csharp[ConversionByDatabaseType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseType)]

### <a name="fluent-api"></a>[<span data-ttu-id="3f35b-133">API fluida</span><span class="sxs-lookup"><span data-stu-id="3f35b-133">Fluent API</span></span>](#tab/fluent-api)

<!--
                modelBuilder
                    .Entity<Rider2>()
                    .Property(e => e.Mount)
                    .HasColumnType("nvarchar(24)");
-->
[!code-csharp[ConversionByDatabaseTypeFluent](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseTypeFluent)]

***

<span data-ttu-id="3f35b-134">A continuación, los valores de enumeración se guardarán como cadenas en la base de datos sin ninguna configuración adicional en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="3f35b-134">Then the enum values will be saved as strings in the database without any further configuration in <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="3f35b-135">La clase ValueConverter</span><span class="sxs-lookup"><span data-stu-id="3f35b-135">The ValueConverter class</span></span>

<span data-ttu-id="3f35b-136">Al llamar a <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> como se muestra anteriormente, se creará una <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> instancia de y se establecerá en la propiedad.</span><span class="sxs-lookup"><span data-stu-id="3f35b-136">Calling <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> as shown above will create a <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> instance and set it on the property.</span></span> <span data-ttu-id="3f35b-137">`ValueConverter`En su lugar, se puede crear explícitamente.</span><span class="sxs-lookup"><span data-stu-id="3f35b-137">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="3f35b-138">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3f35b-138">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByConverterInstance](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstance)]

<span data-ttu-id="3f35b-139">Esto puede ser útil cuando varias propiedades usan la misma conversión.</span><span class="sxs-lookup"><span data-stu-id="3f35b-139">This can be useful when multiple properties use the same conversion.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="3f35b-140">Convertidores integrados</span><span class="sxs-lookup"><span data-stu-id="3f35b-140">Built-in converters</span></span>

<span data-ttu-id="3f35b-141">Como se mencionó anteriormente, EF Core se suministra con un conjunto de clases predefinidas <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> , que se encuentra en el <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="3f35b-141">As mentioned above, EF Core ships with a set of pre-defined <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> classes, found in the <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> namespace.</span></span> <span data-ttu-id="3f35b-142">En muchos casos, EF elegirá el convertidor integrado adecuado en función del tipo de la propiedad en el modelo y del tipo solicitado en la base de datos, como se mostró anteriormente para las enumeraciones.</span><span class="sxs-lookup"><span data-stu-id="3f35b-142">In many cases EF will choose the appropriate built-in converter based on the type of the property in the model and the type requested in the database, as shown above for enums.</span></span> <span data-ttu-id="3f35b-143">Por ejemplo, `.HasConversion<int>()` el uso de en una propiedad hará que `bool` EF Core convierta valores bool a cero numérico y un valor:</span><span class="sxs-lookup"><span data-stu-id="3f35b-143">For example, using `.HasConversion<int>()` on a `bool` property will cause EF Core to convert bool values to numerical zero and one values:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<User>()
                    .Property(e => e.IsActive)
                    .HasConversion<int>();
            }
-->
[!code-csharp[ConversionByBuiltInBoolToInt](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByBuiltInBoolToInt)]

<span data-ttu-id="3f35b-144">Esto es funcionalmente lo mismo que crear una instancia del integrado <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> y establecerlo explícitamente:</span><span class="sxs-lookup"><span data-stu-id="3f35b-144">This is functionally the same as creating an instance of the built-in <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> and setting it explicitly:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new BoolToZeroOneConverter<int>();

                modelBuilder
                    .Entity<User>()
                    .Property(e => e.IsActive)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByBuiltInBoolToIntExplicit](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByBuiltInBoolToIntExplicit)]

<span data-ttu-id="3f35b-145">En la tabla siguiente se resumen las conversiones predefinidas usadas con frecuencia de tipos de modelo o propiedad a tipos de proveedor de bases de datos.</span><span class="sxs-lookup"><span data-stu-id="3f35b-145">The following table summarizes commonly-used pre-defined conversions from model/property types to database provider types.</span></span> <span data-ttu-id="3f35b-146">En la tabla `any_numeric_type` , una de `int` ,,, `short` `long` `byte` , `uint` , `ushort` , `ulong` , `sbyte` , `char` , `decimal` , `float` o `double` .</span><span class="sxs-lookup"><span data-stu-id="3f35b-146">In the table `any_numeric_type` means one of `int`, `short`, `long`, `byte`, `uint`, `ushort`, `ulong`, `sbyte`, `char`, `decimal`, `float`, or `double`.</span></span>

| <span data-ttu-id="3f35b-147">Tipo de modelo/propiedad</span><span class="sxs-lookup"><span data-stu-id="3f35b-147">Model/property type</span></span> | <span data-ttu-id="3f35b-148">Tipo de base de datos/proveedor</span><span class="sxs-lookup"><span data-stu-id="3f35b-148">Provider/database type</span></span> | <span data-ttu-id="3f35b-149">Conversión</span><span class="sxs-lookup"><span data-stu-id="3f35b-149">Conversion</span></span>                                                | <span data-ttu-id="3f35b-150">Uso</span><span class="sxs-lookup"><span data-stu-id="3f35b-150">Usage</span></span>
|:--------------------|------------------------|-----------------------------------------------------------|------
| <span data-ttu-id="3f35b-151">bool</span><span class="sxs-lookup"><span data-stu-id="3f35b-151">bool</span></span>                | <span data-ttu-id="3f35b-152">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="3f35b-152">any_numeric_type</span></span>       | <span data-ttu-id="3f35b-153">False/true a 0/1</span><span class="sxs-lookup"><span data-stu-id="3f35b-153">False/true to 0/1</span></span>                                         | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="3f35b-154">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="3f35b-154">any_numeric_type</span></span>       | <span data-ttu-id="3f35b-155">False/true para dos números cualesquiera</span><span class="sxs-lookup"><span data-stu-id="3f35b-155">False/true to any two numbers</span></span>                             | <span data-ttu-id="3f35b-156">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601></span><span class="sxs-lookup"><span data-stu-id="3f35b-156">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601></span></span>
|                     | <span data-ttu-id="3f35b-157">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-157">string</span></span>                 | <span data-ttu-id="3f35b-158">False/true en "Y"/"N"</span><span class="sxs-lookup"><span data-stu-id="3f35b-158">False/true to "Y"/"N"</span></span>                                     | `.HasConversion<string>()`
|                     | <span data-ttu-id="3f35b-159">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-159">string</span></span>                 | <span data-ttu-id="3f35b-160">False/true para dos cadenas cualesquiera</span><span class="sxs-lookup"><span data-stu-id="3f35b-160">False/true to any two strings</span></span>                             | <span data-ttu-id="3f35b-161">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter></span><span class="sxs-lookup"><span data-stu-id="3f35b-161">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter></span></span>
| <span data-ttu-id="3f35b-162">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="3f35b-162">any_numeric_type</span></span>    | <span data-ttu-id="3f35b-163">bool</span><span class="sxs-lookup"><span data-stu-id="3f35b-163">bool</span></span>                   | <span data-ttu-id="3f35b-164">de 0/1 a false/true</span><span class="sxs-lookup"><span data-stu-id="3f35b-164">0/1 to false/true</span></span>                                         | `.HasConversion<bool>()`
|                     | <span data-ttu-id="3f35b-165">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="3f35b-165">any_numeric_type</span></span>       | <span data-ttu-id="3f35b-166">Conversión simple</span><span class="sxs-lookup"><span data-stu-id="3f35b-166">Simple cast</span></span>                                               | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="3f35b-167">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-167">string</span></span>                 | <span data-ttu-id="3f35b-168">El número como una cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-168">The number as a string</span></span>                                    | `.HasConversion<string>()`
| <span data-ttu-id="3f35b-169">Enumeración</span><span class="sxs-lookup"><span data-stu-id="3f35b-169">Enum</span></span>                | <span data-ttu-id="3f35b-170">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="3f35b-170">any_numeric_type</span></span>       | <span data-ttu-id="3f35b-171">Valor numérico de la enumeración.</span><span class="sxs-lookup"><span data-stu-id="3f35b-171">The numeric value of the enum</span></span>                             | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="3f35b-172">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-172">string</span></span>                 | <span data-ttu-id="3f35b-173">Representación de cadena del valor de enumeración.</span><span class="sxs-lookup"><span data-stu-id="3f35b-173">The string representation of the enum value</span></span>               | `.HasConversion<string>()`
| <span data-ttu-id="3f35b-174">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-174">string</span></span>              | <span data-ttu-id="3f35b-175">bool</span><span class="sxs-lookup"><span data-stu-id="3f35b-175">bool</span></span>                   | <span data-ttu-id="3f35b-176">Analiza la cadena como un valor booleano.</span><span class="sxs-lookup"><span data-stu-id="3f35b-176">Parses the string as a bool</span></span>                               | `.HasConversion<bool>()`
|                     | <span data-ttu-id="3f35b-177">any_numeric_type</span><span class="sxs-lookup"><span data-stu-id="3f35b-177">any_numeric_type</span></span>       | <span data-ttu-id="3f35b-178">Analiza la cadena como el tipo numérico especificado</span><span class="sxs-lookup"><span data-stu-id="3f35b-178">Parses the string as the given numeric type</span></span>               | `.HasConversion<any_numeric_type>()`
|                     | <span data-ttu-id="3f35b-179">char</span><span class="sxs-lookup"><span data-stu-id="3f35b-179">char</span></span>                   | <span data-ttu-id="3f35b-180">Primer carácter de la cadena.</span><span class="sxs-lookup"><span data-stu-id="3f35b-180">The first character of the string</span></span>                         | `.HasConversion<char>()`
|                     | <span data-ttu-id="3f35b-181">DateTime</span><span class="sxs-lookup"><span data-stu-id="3f35b-181">DateTime</span></span>               | <span data-ttu-id="3f35b-182">Analiza la cadena como un valor de fecha y hora</span><span class="sxs-lookup"><span data-stu-id="3f35b-182">Parses the string as a DateTime</span></span>                           | `.HasConversion<DateTime>()`
|                     | <span data-ttu-id="3f35b-183">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="3f35b-183">DateTimeOffset</span></span>         | <span data-ttu-id="3f35b-184">Analiza la cadena como DateTimeOffset.</span><span class="sxs-lookup"><span data-stu-id="3f35b-184">Parses the string as a DateTimeOffset</span></span>                     | `.HasConversion<DateTimeOffset>()`
|                     | <span data-ttu-id="3f35b-185">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="3f35b-185">TimeSpan</span></span>               | <span data-ttu-id="3f35b-186">Analiza la cadena como un intervalo de tiempo</span><span class="sxs-lookup"><span data-stu-id="3f35b-186">Parses the string as a TimeSpan</span></span>                           | `.HasConversion<TimeSpan>()`
|                     | <span data-ttu-id="3f35b-187">Guid</span><span class="sxs-lookup"><span data-stu-id="3f35b-187">Guid</span></span>                   | <span data-ttu-id="3f35b-188">Analiza la cadena como un GUID.</span><span class="sxs-lookup"><span data-stu-id="3f35b-188">Parses the string as a Guid</span></span>                               | `.HasConversion<Guid>()`
|                     | <span data-ttu-id="3f35b-189">byte[]</span><span class="sxs-lookup"><span data-stu-id="3f35b-189">byte[]</span></span>                 | <span data-ttu-id="3f35b-190">La cadena como UTF8 bytes</span><span class="sxs-lookup"><span data-stu-id="3f35b-190">The string as UTF8 bytes</span></span>                                  | `.HasConversion<byte[]>()`
| <span data-ttu-id="3f35b-191">char</span><span class="sxs-lookup"><span data-stu-id="3f35b-191">char</span></span>                | <span data-ttu-id="3f35b-192">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-192">string</span></span>                 | <span data-ttu-id="3f35b-193">Una cadena de un solo carácter</span><span class="sxs-lookup"><span data-stu-id="3f35b-193">A single character string</span></span>                                 | `.HasConversion<string>()`
| <span data-ttu-id="3f35b-194">DateTime</span><span class="sxs-lookup"><span data-stu-id="3f35b-194">DateTime</span></span>            | <span data-ttu-id="3f35b-195">long</span><span class="sxs-lookup"><span data-stu-id="3f35b-195">long</span></span>                   | <span data-ttu-id="3f35b-196">Fecha y hora de la conservación de DateTime. Kind</span><span class="sxs-lookup"><span data-stu-id="3f35b-196">Encoded date/time preserving DateTime.Kind</span></span>                | `.HasConversion<long>()`
|                     | <span data-ttu-id="3f35b-197">long</span><span class="sxs-lookup"><span data-stu-id="3f35b-197">long</span></span>                   | <span data-ttu-id="3f35b-198">Pasos</span><span class="sxs-lookup"><span data-stu-id="3f35b-198">Ticks</span></span>                                                     | <span data-ttu-id="3f35b-199">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter></span><span class="sxs-lookup"><span data-stu-id="3f35b-199">Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter></span></span>
|                     | <span data-ttu-id="3f35b-200">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-200">string</span></span>                 | <span data-ttu-id="3f35b-201">Cadena de fecha y hora de la referencia cultural de todos los idiomas</span><span class="sxs-lookup"><span data-stu-id="3f35b-201">Invariant culture date/time string</span></span>                        | `.HasConversion<string>()`
| <span data-ttu-id="3f35b-202">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="3f35b-202">DateTimeOffset</span></span>      | <span data-ttu-id="3f35b-203">long</span><span class="sxs-lookup"><span data-stu-id="3f35b-203">long</span></span>                   | <span data-ttu-id="3f35b-204">Fecha y hora codificadas con desplazamiento</span><span class="sxs-lookup"><span data-stu-id="3f35b-204">Encoded date/time with offset</span></span>                             | `.HasConversion<long>()`
|                     | <span data-ttu-id="3f35b-205">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-205">string</span></span>                 | <span data-ttu-id="3f35b-206">Cadena de fecha y hora de la referencia cultural de todos los idiomas con desplazamiento</span><span class="sxs-lookup"><span data-stu-id="3f35b-206">Invariant culture date/time string with offset</span></span>            | `.HasConversion<string>()`
| <span data-ttu-id="3f35b-207">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="3f35b-207">TimeSpan</span></span>            | <span data-ttu-id="3f35b-208">long</span><span class="sxs-lookup"><span data-stu-id="3f35b-208">long</span></span>                   | <span data-ttu-id="3f35b-209">Pasos</span><span class="sxs-lookup"><span data-stu-id="3f35b-209">Ticks</span></span>                                                     | `.HasConversion<long>()`
|                     | <span data-ttu-id="3f35b-210">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-210">string</span></span>                 | <span data-ttu-id="3f35b-211">Cadena de intervalo de tiempo de referencia cultural de todos los idiomas</span><span class="sxs-lookup"><span data-stu-id="3f35b-211">Invariant culture time span string</span></span>                        | `.HasConversion<string>()`
| <span data-ttu-id="3f35b-212">Identificador URI</span><span class="sxs-lookup"><span data-stu-id="3f35b-212">Uri</span></span>                 | <span data-ttu-id="3f35b-213">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-213">string</span></span>                 | <span data-ttu-id="3f35b-214">El URI como una cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-214">The URI as a string</span></span>                                       | `.HasConversion<string>()`
| <span data-ttu-id="3f35b-215">PhysicalAddress</span><span class="sxs-lookup"><span data-stu-id="3f35b-215">PhysicalAddress</span></span>     | <span data-ttu-id="3f35b-216">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-216">string</span></span>                 | <span data-ttu-id="3f35b-217">La dirección como una cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-217">The address as a string</span></span>                                   | `.HasConversion<string>()`
|                     | <span data-ttu-id="3f35b-218">byte[]</span><span class="sxs-lookup"><span data-stu-id="3f35b-218">byte[]</span></span>                 | <span data-ttu-id="3f35b-219">Bytes en el orden de red Big-Endian</span><span class="sxs-lookup"><span data-stu-id="3f35b-219">Bytes in big-endian network order</span></span>                         | `.HasConversion<byte[]>()`
| <span data-ttu-id="3f35b-220">IPAddress</span><span class="sxs-lookup"><span data-stu-id="3f35b-220">IPAddress</span></span>           | <span data-ttu-id="3f35b-221">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-221">string</span></span>                 | <span data-ttu-id="3f35b-222">La dirección como una cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-222">The address as a string</span></span>                                   | `.HasConversion<string>()`
|                     | <span data-ttu-id="3f35b-223">byte[]</span><span class="sxs-lookup"><span data-stu-id="3f35b-223">byte[]</span></span>                 | <span data-ttu-id="3f35b-224">Bytes en el orden de red Big-Endian</span><span class="sxs-lookup"><span data-stu-id="3f35b-224">Bytes in big-endian network order</span></span>                         | `.HasConversion<byte[]>()`
| <span data-ttu-id="3f35b-225">Guid</span><span class="sxs-lookup"><span data-stu-id="3f35b-225">Guid</span></span>                | <span data-ttu-id="3f35b-226">string</span><span class="sxs-lookup"><span data-stu-id="3f35b-226">string</span></span>                 | <span data-ttu-id="3f35b-227">GUID en formato ' dddddddd-dddd-dddd-dddd-dddddddddddd '</span><span class="sxs-lookup"><span data-stu-id="3f35b-227">The GUID in 'dddddddd-dddd-dddd-dddd-dddddddddddd' format</span></span> | `.HasConversion<string>()`
|                     | <span data-ttu-id="3f35b-228">byte[]</span><span class="sxs-lookup"><span data-stu-id="3f35b-228">byte[]</span></span>                 | <span data-ttu-id="3f35b-229">Bytes en el orden de serialización binaria de .NET</span><span class="sxs-lookup"><span data-stu-id="3f35b-229">Bytes in .NET binary serialization order</span></span>                  | `.HasConversion<byte[]>()`

<span data-ttu-id="3f35b-230">Tenga en cuenta que estas conversiones suponen que el formato del valor es adecuado para la conversión.</span><span class="sxs-lookup"><span data-stu-id="3f35b-230">Note that these conversions assume that the format of the value is appropriate for the conversion.</span></span> <span data-ttu-id="3f35b-231">Por ejemplo, la conversión de cadenas en números producirá un error si los valores de cadena no se pueden analizar como números.</span><span class="sxs-lookup"><span data-stu-id="3f35b-231">For example, converting strings to numbers will fail if the string values cannot be parsed as numbers.</span></span>

<span data-ttu-id="3f35b-232">La lista completa de convertidores integrados es:</span><span class="sxs-lookup"><span data-stu-id="3f35b-232">The full list of built-in converters is:</span></span>

* <span data-ttu-id="3f35b-233">Convertir propiedades bool:</span><span class="sxs-lookup"><span data-stu-id="3f35b-233">Converting bool properties:</span></span>
  * <span data-ttu-id="3f35b-234"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> -Bool a cadenas como "Y" y "N"</span><span class="sxs-lookup"><span data-stu-id="3f35b-234"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> - Bool to strings such as "Y" and "N"</span></span>
  * <span data-ttu-id="3f35b-235"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> -Bool a dos valores cualesquiera</span><span class="sxs-lookup"><span data-stu-id="3f35b-235"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> - Bool to any two values</span></span>
  * <span data-ttu-id="3f35b-236"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> -Bool a cero y uno</span><span class="sxs-lookup"><span data-stu-id="3f35b-236"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> - Bool to zero and one</span></span>
* <span data-ttu-id="3f35b-237">Convertir propiedades de matriz de bytes:</span><span class="sxs-lookup"><span data-stu-id="3f35b-237">Converting byte array properties:</span></span>
  * <span data-ttu-id="3f35b-238"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> -Matriz de bytes a cadena codificada en Base64</span><span class="sxs-lookup"><span data-stu-id="3f35b-238"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="3f35b-239">Cualquier conversión que requiera solo una conversión de tipos</span><span class="sxs-lookup"><span data-stu-id="3f35b-239">Any conversion that requires only a type-cast</span></span>
  * <span data-ttu-id="3f35b-240"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> -Conversiones que requieren solo una conversión de tipo</span><span class="sxs-lookup"><span data-stu-id="3f35b-240"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> - Conversions that require only a type cast</span></span>
* <span data-ttu-id="3f35b-241">Convertir propiedades char:</span><span class="sxs-lookup"><span data-stu-id="3f35b-241">Converting char properties:</span></span>
  * <span data-ttu-id="3f35b-242"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> -Char a cadena de carácter único</span><span class="sxs-lookup"><span data-stu-id="3f35b-242"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> - Char to single character string</span></span>
* <span data-ttu-id="3f35b-243">Convertir <xref:System.DateTimeOffset> propiedades:</span><span class="sxs-lookup"><span data-stu-id="3f35b-243">Converting <xref:System.DateTimeOffset> properties:</span></span>
  * <span data-ttu-id="3f35b-244"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> en el valor de 64 bits con codificación binaria</span><span class="sxs-lookup"><span data-stu-id="3f35b-244"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> to binary-encoded 64-bit value</span></span>
  * <span data-ttu-id="3f35b-245"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> a la matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="3f35b-245"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> to byte array</span></span>
  * <span data-ttu-id="3f35b-246"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> a cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-246"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> to string</span></span>
* <span data-ttu-id="3f35b-247">Convertir <xref:System.DateTime> propiedades:</span><span class="sxs-lookup"><span data-stu-id="3f35b-247">Converting <xref:System.DateTime> properties:</span></span>
  * <span data-ttu-id="3f35b-248"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> en valor de 64 bits, incluido DateTimeKind</span><span class="sxs-lookup"><span data-stu-id="3f35b-248"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> to 64-bit value including DateTimeKind</span></span>
  * <span data-ttu-id="3f35b-249"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> a cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-249"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> to string</span></span>
  * <span data-ttu-id="3f35b-250"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> a TICs</span><span class="sxs-lookup"><span data-stu-id="3f35b-250"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> to ticks</span></span>
* <span data-ttu-id="3f35b-251">Convertir propiedades de enumeración:</span><span class="sxs-lookup"><span data-stu-id="3f35b-251">Converting enum properties:</span></span>
  * <span data-ttu-id="3f35b-252"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> -Enum al número subyacente</span><span class="sxs-lookup"><span data-stu-id="3f35b-252"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> - Enum to underlying number</span></span>
  * <span data-ttu-id="3f35b-253"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> -Enum a cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-253"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> - Enum to string</span></span>
* <span data-ttu-id="3f35b-254">Convertir <xref:System.Guid> propiedades:</span><span class="sxs-lookup"><span data-stu-id="3f35b-254">Converting <xref:System.Guid> properties:</span></span>
  * <span data-ttu-id="3f35b-255"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> a la matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="3f35b-255"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> to byte array</span></span>
  * <span data-ttu-id="3f35b-256"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> a cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-256"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> to string</span></span>
* <span data-ttu-id="3f35b-257">Convertir <xref:System.Net.IPAddress> propiedades:</span><span class="sxs-lookup"><span data-stu-id="3f35b-257">Converting <xref:System.Net.IPAddress> properties:</span></span>
  * <span data-ttu-id="3f35b-258"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> a la matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="3f35b-258"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> to byte array</span></span>
  * <span data-ttu-id="3f35b-259"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> a cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-259"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> to string</span></span>
* <span data-ttu-id="3f35b-260">Convertir propiedades numéricas (int, Double, decimal, etc.):</span><span class="sxs-lookup"><span data-stu-id="3f35b-260">Converting numeric (int, double, decimal, etc.) properties:</span></span>
  * <span data-ttu-id="3f35b-261"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> -Cualquier valor numérico a matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="3f35b-261"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> - Any numerical value to byte array</span></span>
  * <span data-ttu-id="3f35b-262"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> -Cualquier valor numérico a cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-262"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> - Any numerical value to string</span></span>
* <span data-ttu-id="3f35b-263">Convertir <xref:System.Net.NetworkInformation.PhysicalAddress> propiedades:</span><span class="sxs-lookup"><span data-stu-id="3f35b-263">Converting <xref:System.Net.NetworkInformation.PhysicalAddress> properties:</span></span>
  * <span data-ttu-id="3f35b-264"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> a la matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="3f35b-264"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> to byte array</span></span>
  * <span data-ttu-id="3f35b-265"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> a cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-265"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> to string</span></span>
* <span data-ttu-id="3f35b-266">Convertir propiedades de cadena:</span><span class="sxs-lookup"><span data-stu-id="3f35b-266">Converting string properties:</span></span>
  * <span data-ttu-id="3f35b-267"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> -Cadenas como "Y" y "N" a bool</span><span class="sxs-lookup"><span data-stu-id="3f35b-267"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> - Strings such as "Y" and "N" to bool</span></span>
  * <span data-ttu-id="3f35b-268"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> -Cadena en bytes UTF8</span><span class="sxs-lookup"><span data-stu-id="3f35b-268"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> - String to UTF8 bytes</span></span>
  * <span data-ttu-id="3f35b-269"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> -Cadena a carácter</span><span class="sxs-lookup"><span data-stu-id="3f35b-269"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> - String to character</span></span>
  * <span data-ttu-id="3f35b-270"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> : Cadena que se va a <xref:System.DateTime></span><span class="sxs-lookup"><span data-stu-id="3f35b-270"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> - String to <xref:System.DateTime></span></span>
  * <span data-ttu-id="3f35b-271"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> : Cadena que se va a <xref:System.DateTimeOffset></span><span class="sxs-lookup"><span data-stu-id="3f35b-271"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> - String to <xref:System.DateTimeOffset></span></span>
  * <span data-ttu-id="3f35b-272"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> -Cadena que se va a enumerar</span><span class="sxs-lookup"><span data-stu-id="3f35b-272"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> - String to enum</span></span>
  * <span data-ttu-id="3f35b-273"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> : Cadena que se va a <xref:System.Guid></span><span class="sxs-lookup"><span data-stu-id="3f35b-273"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> - String to <xref:System.Guid></span></span>
  * <span data-ttu-id="3f35b-274"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> -Cadena a tipo numérico</span><span class="sxs-lookup"><span data-stu-id="3f35b-274"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> - String to numeric type</span></span>
  * <span data-ttu-id="3f35b-275"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> : Cadena que se va a <xref:System.TimeSpan></span><span class="sxs-lookup"><span data-stu-id="3f35b-275"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> - String to <xref:System.TimeSpan></span></span>
  * <span data-ttu-id="3f35b-276"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> : Cadena que se va a <xref:System.Uri></span><span class="sxs-lookup"><span data-stu-id="3f35b-276"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> - String to <xref:System.Uri></span></span>
* <span data-ttu-id="3f35b-277">Convertir <xref:System.TimeSpan> propiedades:</span><span class="sxs-lookup"><span data-stu-id="3f35b-277">Converting <xref:System.TimeSpan> properties:</span></span>
  * <span data-ttu-id="3f35b-278"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> a cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-278"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> to string</span></span>
  * <span data-ttu-id="3f35b-279"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> a TICs</span><span class="sxs-lookup"><span data-stu-id="3f35b-279"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> to ticks</span></span>
* <span data-ttu-id="3f35b-280">Convertir <xref:System.Uri> propiedades:</span><span class="sxs-lookup"><span data-stu-id="3f35b-280">Converting <xref:System.Uri> properties:</span></span>
  * <span data-ttu-id="3f35b-281"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> a cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-281"><xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> to string</span></span>

<span data-ttu-id="3f35b-282">Tenga en cuenta que todos los convertidores integrados no tienen estado y, por tanto, una sola instancia puede compartirse de forma segura con varias propiedades.</span><span class="sxs-lookup"><span data-stu-id="3f35b-282">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="column-facets-and-mapping-hints"></a><span data-ttu-id="3f35b-283">Aspectos de las columnas y sugerencias de asignación</span><span class="sxs-lookup"><span data-stu-id="3f35b-283">Column facets and mapping hints</span></span>

<span data-ttu-id="3f35b-284">Algunos tipos de base de datos tienen aspectos que modifican la forma en que se almacenan.</span><span class="sxs-lookup"><span data-stu-id="3f35b-284">Some database types have facets that modify how the data is stored.</span></span> <span data-ttu-id="3f35b-285">Entre ellos, se incluye:</span><span class="sxs-lookup"><span data-stu-id="3f35b-285">These include:</span></span>

* <span data-ttu-id="3f35b-286">Precisión y escala para decimales y columnas de fecha y hora</span><span class="sxs-lookup"><span data-stu-id="3f35b-286">Precision and scale for decimals and date/time columns</span></span>
* <span data-ttu-id="3f35b-287">Tamaño/longitud de las columnas binarias y de cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-287">Size/length for binary and string columns</span></span>
* <span data-ttu-id="3f35b-288">Unicode para columnas de cadena</span><span class="sxs-lookup"><span data-stu-id="3f35b-288">Unicode for string columns</span></span>

<span data-ttu-id="3f35b-289">Estas caras se pueden configurar de forma normal para una propiedad que utiliza un convertidor de valores, y se aplicarán al tipo de base de datos convertido.</span><span class="sxs-lookup"><span data-stu-id="3f35b-289">These facets can be configured in the normal way for a property that uses a value converter, and will apply to the converted database type.</span></span> <span data-ttu-id="3f35b-290">Por ejemplo, al convertir de una enumeración a cadenas, podemos especificar que la columna de base de datos no debe ser Unicode y almacenar hasta 20 caracteres:</span><span class="sxs-lookup"><span data-stu-id="3f35b-290">For example, when converting from an enum to strings, we can specify that the database column should be non-Unicode and store up to 20 characters:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion<string>()
                    .HasMaxLength(20)
                    .IsUnicode(false);
            }
-->
[!code-csharp[ConversionByClrTypeWithFacets](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByClrTypeWithFacets)]

<span data-ttu-id="3f35b-291">O bien, al crear el convertidor explícitamente:</span><span class="sxs-lookup"><span data-stu-id="3f35b-291">Or, when creating the converter explicitly:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter)
                    .HasMaxLength(20)
                    .IsUnicode(false);
            }
-->
[!code-csharp[ConversionByConverterInstanceWithFacets](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstanceWithFacets)]

<span data-ttu-id="3f35b-292">Esto da como resultado una `varchar(20)` columna al utilizar EF Core migraciones en SQL Server:</span><span class="sxs-lookup"><span data-stu-id="3f35b-292">This results in a `varchar(20)` column when using EF Core migrations against SQL Server:</span></span>

```sql
CREATE TABLE [Rider] (
    [Id] int NOT NULL IDENTITY,
    [Mount] varchar(20) NOT NULL,
    CONSTRAINT [PK_Rider] PRIMARY KEY ([Id]));
```

<span data-ttu-id="3f35b-293">Sin embargo, si, de forma predeterminada, todas `EquineBeast` las columnas deben ser `varchar(20)` , esta información se puede proporcionar al convertidor de valores como <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints> .</span><span class="sxs-lookup"><span data-stu-id="3f35b-293">However, if by default all `EquineBeast` columns should be `varchar(20)`, then this information can be given to the value converter as a <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints>.</span></span> <span data-ttu-id="3f35b-294">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3f35b-294">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<EquineBeast, string>(
                    v => v.ToString(),
                    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v),
                    new ConverterMappingHints(size: 20, unicode: false));

                modelBuilder
                    .Entity<Rider>()
                    .Property(e => e.Mount)
                    .HasConversion(converter);
            }
-->
[!code-csharp[ConversionByConverterInstanceWithMappingHints](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByConverterInstanceWithMappingHints)]

<span data-ttu-id="3f35b-295">Ahora, cada vez que se use este convertidor, la columna de la base de datos no será Unicode con una longitud máxima de 20.</span><span class="sxs-lookup"><span data-stu-id="3f35b-295">Now any time this converter is used, the database column will be non-unicode with a max length of 20.</span></span> <span data-ttu-id="3f35b-296">Sin embargo, estas son solo sugerencias, ya que se reemplazan por cualquier aspecto establecida explícitamente en la propiedad asignada.</span><span class="sxs-lookup"><span data-stu-id="3f35b-296">However, these are only hints since they are be overridden by any facets explicitly set on the mapped property.</span></span>

## <a name="examples"></a><span data-ttu-id="3f35b-297">Ejemplos</span><span class="sxs-lookup"><span data-stu-id="3f35b-297">Examples</span></span>

### <a name="simple-value-objects"></a><span data-ttu-id="3f35b-298">Objetos de valor simple</span><span class="sxs-lookup"><span data-stu-id="3f35b-298">Simple value objects</span></span>

<span data-ttu-id="3f35b-299">En este ejemplo se usa un tipo simple para encapsular un tipo primitivo.</span><span class="sxs-lookup"><span data-stu-id="3f35b-299">This example uses a simple type to wrap a primitive type.</span></span> <span data-ttu-id="3f35b-300">Esto puede ser útil si desea que el tipo del modelo sea más específico (y, por tanto, más seguro de tipos) que un tipo primitivo.</span><span class="sxs-lookup"><span data-stu-id="3f35b-300">This can be useful when you want the type in your model to be more specific (and hence more type-safe) than a primitive type.</span></span> <span data-ttu-id="3f35b-301">En este ejemplo, ese tipo es `Dollars` , que ajusta el primitivo decimal:</span><span class="sxs-lookup"><span data-stu-id="3f35b-301">In this example, that type is `Dollars`, which wraps the decimal primitive:</span></span>

<!--
        public readonly struct Dollars
        {
            public Dollars(decimal amount) 
                => Amount = amount;
            
            public decimal Amount { get; }

            public override string ToString() 
                => $"${Amount}";
        }
-->
[!code-csharp[SimpleValueObject](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObject)]

<span data-ttu-id="3f35b-302">Se puede usar en un tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="3f35b-302">This can be used in an entity type:</span></span>

<!--
        public class Order
        {
            public int Id { get; set; }

            public Dollars Price { get; set; }
        }
-->
[!code-csharp[SimpleValueObjectModel](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObjectModel)]

<span data-ttu-id="3f35b-303">Y se convierten en los subyacentes `decimal` cuando se almacenan en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="3f35b-303">And converted to the underlying `decimal` when stored in the database:</span></span>

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => v.Amount,
                        v => new Dollars(v));
-->
[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=ConfigureImmutableStructProperty)]

> [!NOTE]
> <span data-ttu-id="3f35b-304">Este objeto de valor se implementa como una [estructura de solo lectura](/dotnet/csharp/language-reference/builtin-types/struct).</span><span class="sxs-lookup"><span data-stu-id="3f35b-304">This value object is implemented as a [readonly struct](/dotnet/csharp/language-reference/builtin-types/struct).</span></span> <span data-ttu-id="3f35b-305">Esto significa que EF Core puede realizar instantáneas y comparar valores sin problemas.</span><span class="sxs-lookup"><span data-stu-id="3f35b-305">This means that EF Core can snapshot and compare values without issue.</span></span> <span data-ttu-id="3f35b-306">Consulte [comparadores de valores](xref:core/modeling/value-comparers) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="3f35b-306">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="composite-value-objects"></a><span data-ttu-id="3f35b-307">Objetos de valor compuesto</span><span class="sxs-lookup"><span data-stu-id="3f35b-307">Composite value objects</span></span>

<span data-ttu-id="3f35b-308">En el ejemplo anterior, el tipo de objeto de valor solo contenía una propiedad única.</span><span class="sxs-lookup"><span data-stu-id="3f35b-308">In the previous example, the value object type contained only a single property.</span></span> <span data-ttu-id="3f35b-309">Es más común que un tipo de objeto de valor componga varias propiedades que juntos forman un concepto de dominio.</span><span class="sxs-lookup"><span data-stu-id="3f35b-309">It is more common for a value object type to compose multiple properties that together form a domain concept.</span></span> <span data-ttu-id="3f35b-310">Por ejemplo, un `Money` tipo general que contiene la cantidad y la moneda:</span><span class="sxs-lookup"><span data-stu-id="3f35b-310">For example, a general `Money` type that contains both the amount and the currency:</span></span>

<!--
        public readonly struct Money
        {
            [JsonConstructor]
            public Money(decimal amount, Currency currency)
            {
                Amount = amount;
                Currency = currency;
            }

            public override string ToString()
                => (Currency == Currency.UsDollars ? "$" : "£") + Amount;

            public decimal Amount { get; }
            public Currency Currency { get; }
        }

        public enum Currency
        {
            UsDollars,
            PoundsStirling
        }
-->
[!code-csharp[CompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObject)]

<span data-ttu-id="3f35b-311">Este objeto de valor se puede usar en un tipo de entidad como antes:</span><span class="sxs-lookup"><span data-stu-id="3f35b-311">This value object can be used in an entity type as before:</span></span>

<!--
        public class Order
        {
            public int Id { get; set; }

            public Money Price { get; set; }
        }
-->
[!code-csharp[CompositeValueObjectModel](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObjectModel)]

<span data-ttu-id="3f35b-312">Los convertidores de valores solo pueden convertir actualmente valores en una sola columna de base de datos.</span><span class="sxs-lookup"><span data-stu-id="3f35b-312">Value converters can currently only convert values to and from a single database column.</span></span> <span data-ttu-id="3f35b-313">Esta limitación significa que todos los valores de propiedad del objeto se deben codificar en un valor de columna única.</span><span class="sxs-lookup"><span data-stu-id="3f35b-313">This limitation means that all property values from the object must be encoded into a single column value.</span></span> <span data-ttu-id="3f35b-314">Normalmente, esto se controla mediante la serialización del objeto tal como entra en la base de datos y, a continuación, la deserialización de nuevo en el sentido. Por ejemplo, mediante <xref:System.Text.Json> :</span><span class="sxs-lookup"><span data-stu-id="3f35b-314">This is typically handled by serializing the object as it goes into the database, and then deserializing it again on the way out. For example, using <xref:System.Text.Json>:</span></span>

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<Money>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=ConfigureCompositeValueObject)]

> [!NOTE]
> <span data-ttu-id="3f35b-315">Tenemos previsto permitir la asignación de un objeto a varias columnas en EF Core 6,0, lo que elimina la necesidad de usar la serialización aquí.</span><span class="sxs-lookup"><span data-stu-id="3f35b-315">We plan to allow mapping an object to multiple columns in EF Core 6.0, removing the need to use serialization here.</span></span> <span data-ttu-id="3f35b-316">Esto se realiza mediante el [problema de GitHub #13947](https://github.com/dotnet/efcore/issues/13947).</span><span class="sxs-lookup"><span data-stu-id="3f35b-316">This is tracked by [GitHub issue #13947](https://github.com/dotnet/efcore/issues/13947).</span></span>

> [!NOTE]
> <span data-ttu-id="3f35b-317">Como en el ejemplo anterior, este objeto de valor se implementa como una [estructura de solo lectura](/dotnet/csharp/language-reference/builtin-types/struct).</span><span class="sxs-lookup"><span data-stu-id="3f35b-317">As with the previous example, this value object is implemented as a [readonly struct](/dotnet/csharp/language-reference/builtin-types/struct).</span></span> <span data-ttu-id="3f35b-318">Esto significa que EF Core puede realizar instantáneas y comparar valores sin problemas.</span><span class="sxs-lookup"><span data-stu-id="3f35b-318">This means that EF Core can snapshot and compare values without issue.</span></span> <span data-ttu-id="3f35b-319">Consulte [comparadores de valores](xref:core/modeling/value-comparers) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="3f35b-319">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="collections-of-primitives"></a><span data-ttu-id="3f35b-320">Colecciones de primitivas</span><span class="sxs-lookup"><span data-stu-id="3f35b-320">Collections of primitives</span></span>

<span data-ttu-id="3f35b-321">La serialización también se puede utilizar para almacenar una colección de valores primitivos.</span><span class="sxs-lookup"><span data-stu-id="3f35b-321">Serialization can also be used to store a collection of primitive values.</span></span> <span data-ttu-id="3f35b-322">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3f35b-322">For example:</span></span>

<!--
        public class Post
        {
            public int Id { get; set; }
            public string Title { get; set; }
            public string Contents { get; set; }

            public ICollection<string> Tags { get; set; }
        }
-->
[!code-csharp[PrimitiveCollectionModel](../../../samples/core/Modeling/ValueConversions/PrimitiveCollection.cs?name=PrimitiveCollectionModel)]

<span data-ttu-id="3f35b-323"><xref:System.Text.Json>Volver a usar:</span><span class="sxs-lookup"><span data-stu-id="3f35b-323">Using <xref:System.Text.Json> again:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.Tags)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<List<string>>(v, null),
                        new ValueComparer<ICollection<string>>(
                            (c1, c2) => c1.SequenceEqual(c2),
                            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
                            c => (ICollection<string>)c.ToList()));
-->
[!code-csharp[ConfigurePrimitiveCollection](../../../samples/core/Modeling/ValueConversions/PrimitiveCollection.cs?name=ConfigurePrimitiveCollection)]

<span data-ttu-id="3f35b-324">`ICollection<string>` representa un tipo de referencia mutable.</span><span class="sxs-lookup"><span data-stu-id="3f35b-324">`ICollection<string>` represents a mutable reference type.</span></span> <span data-ttu-id="3f35b-325">Esto significa que <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> se necesita un para que EF Core pueda realizar un seguimiento de los cambios y detectarlos correctamente.</span><span class="sxs-lookup"><span data-stu-id="3f35b-325">This means that a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> is needed so that EF Core can track and detect changes correctly.</span></span> <span data-ttu-id="3f35b-326">Consulte [comparadores de valores](xref:core/modeling/value-comparers) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="3f35b-326">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="collections-of-value-objects"></a><span data-ttu-id="3f35b-327">Colecciones de objetos de valor</span><span class="sxs-lookup"><span data-stu-id="3f35b-327">Collections of value objects</span></span>

<span data-ttu-id="3f35b-328">Combinando los dos ejemplos anteriores juntos podemos crear una colección de objetos de valor.</span><span class="sxs-lookup"><span data-stu-id="3f35b-328">Combining the previous two examples together we can create a collection of value objects.</span></span> <span data-ttu-id="3f35b-329">Por ejemplo, considere un `AnnualFinance` tipo que modela las finanzas del blog para un solo año:</span><span class="sxs-lookup"><span data-stu-id="3f35b-329">For example, consider an `AnnualFinance` type that models blog finances for a single year:</span></span>

<!--
        public readonly struct AnnualFinance
        {
            [JsonConstructor]
            public AnnualFinance(int year, Money income, Money expenses)
            {
                Year = year;
                Income = income;
                Expenses = expenses;
            }

            public int Year { get; }
            public Money Income { get; }
            public Money Expenses { get; }
            public Money Revenue => new Money(Income.Amount - Expenses.Amount, Income.Currency);
        }
-->
[!code-csharp[ValueObjectCollection](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollection)]

<span data-ttu-id="3f35b-330">Este tipo compone algunos de los tipos que `Money` hemos creado anteriormente:</span><span class="sxs-lookup"><span data-stu-id="3f35b-330">This type composes several of the `Money` types we created previously:</span></span>

<!--
        public readonly struct Money
        {
            [JsonConstructor]
            public Money(decimal amount, Currency currency)
            {
                Amount = amount;
                Currency = currency;
            }

            public override string ToString()
                => (Currency == Currency.UsDollars ? "$" : "£") + Amount;

            public decimal Amount { get; }
            public Currency Currency { get; }
        }

        public enum Currency
        {
            UsDollars,
            PoundsStirling
        }
-->
[!code-csharp[ValueObjectCollectionMoney](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionMoney)]

<span data-ttu-id="3f35b-331">A continuación, podemos agregar una colección de `AnnualFinance` a nuestro tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="3f35b-331">We can then add a collection of `AnnualFinance` to our entity type:</span></span>

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            
            public IList<AnnualFinance> Finances { get; set; }
        }
-->
[!code-csharp[ValueObjectCollectionModel](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionModel)]

<span data-ttu-id="3f35b-332">Y usan de nuevo la serialización para almacenar esto:</span><span class="sxs-lookup"><span data-stu-id="3f35b-332">And again use serialization to store this:</span></span>

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Finances)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<List<AnnualFinance>>(v, null),
                        new ValueComparer<IList<AnnualFinance>>(
                            (c1, c2) => c1.SequenceEqual(c2),
                            c => c.Aggregate(0, (a, v) => HashCode.Combine(a, v.GetHashCode())),
                            c => (IList<AnnualFinance>)c.ToList()));
-->
[!code-csharp[ConfigureValueObjectCollection](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ConfigureValueObjectCollection)]

> [!NOTE]
> <span data-ttu-id="3f35b-333">Como antes, esta conversión requiere un <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> .</span><span class="sxs-lookup"><span data-stu-id="3f35b-333">As before, this conversion requires a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601>.</span></span> <span data-ttu-id="3f35b-334">Consulte [comparadores de valores](xref:core/modeling/value-comparers) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="3f35b-334">See [Value Comparers](xref:core/modeling/value-comparers) for more information.</span></span>

### <a name="value-objects-as-keys"></a><span data-ttu-id="3f35b-335">Objetos de valor como claves</span><span class="sxs-lookup"><span data-stu-id="3f35b-335">Value objects as keys</span></span>

<span data-ttu-id="3f35b-336">A veces, las propiedades de clave primitivas se pueden encapsular en objetos de valor para agregar un nivel adicional de seguridad de tipos en la asignación de valores.</span><span class="sxs-lookup"><span data-stu-id="3f35b-336">Sometimes primitive key properties may be wrapped in value objects to add an additional level of type-safety in assigning values.</span></span> <span data-ttu-id="3f35b-337">Por ejemplo, podríamos implementar un tipo de clave para los blogs y un tipo de clave para las publicaciones:</span><span class="sxs-lookup"><span data-stu-id="3f35b-337">For example, we could implement a key type for blogs, and a key type for posts:</span></span>

<!--
        public readonly struct BlogKey
        {
            public BlogKey(int id) => Id = id;
            public int Id { get; }
        }

        public readonly struct PostKey
        {
            public PostKey(int id) => Id = id;
            public int Id { get; }
        }
-->
[!code-csharp[KeyValueObjects](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=KeyValueObjects)]

<span data-ttu-id="3f35b-338">Estos se pueden usar en el modelo de dominio:</span><span class="sxs-lookup"><span data-stu-id="3f35b-338">These can then be used in the domain model:</span></span>

<!--
        public class Blog
        {
            public BlogKey Id { get; set; }
            public string Name { get; set; }

            public ICollection<Post> Posts { get; set; }
        }

        public class Post
        {
            public PostKey Id { get; set; }

            public string Title { get; set; }
            public string Content { get; set; }

            public BlogKey? BlogId { get; set; }
            public Blog Blog { get; set; }
        }
-->
[!code-csharp[KeyValueObjectsModel](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=KeyValueObjectsModel)]

<span data-ttu-id="3f35b-339">Observe que `Blog.Id` no se puede asignar accidentalmente un `PostKey` y `Post.Id` no se le puede asignar accidentalmente un `BlogKey` .</span><span class="sxs-lookup"><span data-stu-id="3f35b-339">Notice that `Blog.Id` cannot accidentally be assigned a `PostKey`, and `Post.Id` cannot accidentally be assigned a `BlogKey`.</span></span> <span data-ttu-id="3f35b-340">Del mismo modo, `Post.BlogId` se debe asignar a la propiedad de clave externa `BlogKey` .</span><span class="sxs-lookup"><span data-stu-id="3f35b-340">Similarly, the `Post.BlogId` foreign key property must be assigned a `BlogKey`.</span></span>

> [!NOTE]
> <span data-ttu-id="3f35b-341">La visualización de este patrón no significa que se recomiende.</span><span class="sxs-lookup"><span data-stu-id="3f35b-341">Showing this pattern does not mean we recommend it.</span></span> <span data-ttu-id="3f35b-342">Considere detenidamente si este nivel de abstracción está ayudando o dificultando su experiencia de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="3f35b-342">Carefully consider whether this level of abstraction is helping or hampering your development experience.</span></span> <span data-ttu-id="3f35b-343">Además, considere la posibilidad de usar navegaciones y claves generadas en lugar de tratar directamente con los valores de clave.</span><span class="sxs-lookup"><span data-stu-id="3f35b-343">Also, consider using navigations and generated keys instead of dealing with key values directly.</span></span>

<span data-ttu-id="3f35b-344">Después, estas propiedades de clave se pueden asignar mediante convertidores de valores:</span><span class="sxs-lookup"><span data-stu-id="3f35b-344">These key properties can then be mapped using value converters:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var blogKeyConverter = new ValueConverter<BlogKey, int>(
                    v => v.Id,
                    v => new BlogKey(v));

                modelBuilder.Entity<Blog>().Property(e => e.Id).HasConversion(blogKeyConverter);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).HasConversion(v => v.Id, v => new PostKey(v));
                            b.Property(e => e.BlogId).HasConversion(blogKeyConverter);
                        });
            }
-->
[!code-csharp[ConfigureKeyValueObjects](../../../samples/core/Modeling/ValueConversions/KeyValueObjects.cs?name=ConfigureKeyValueObjects)]

> [!NOTE]
> <span data-ttu-id="3f35b-345">Actualmente, las propiedades clave con conversiones no pueden utilizar valores de clave generados.</span><span class="sxs-lookup"><span data-stu-id="3f35b-345">Currently key properties with conversions cannot use generated key values.</span></span> <span data-ttu-id="3f35b-346">Vote por el [problema de GitHub #11597](https://github.com/dotnet/efcore/issues/11597) para quitar esta limitación.</span><span class="sxs-lookup"><span data-stu-id="3f35b-346">Vote for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/11597) to have this limitation removed.</span></span>

### <a name="use-ulong-for-timestamprowversion"></a><span data-ttu-id="3f35b-347">Usar ulong para timestamp/rowversion</span><span class="sxs-lookup"><span data-stu-id="3f35b-347">Use ulong for timestamp/rowversion</span></span>

<span data-ttu-id="3f35b-348">SQL Server admite la [simultaneidad optimista](xref:core/saving/concurrency) automática mediante [ `rowversion` / `timestamp` columnas binarias de 8 bytes](/sql/t-sql/data-types/rowversion-transact-sql).</span><span class="sxs-lookup"><span data-stu-id="3f35b-348">SQL Server supports automatic [optimistic concurrency](xref:core/saving/concurrency) using [8-byte binary `rowversion`/`timestamp` columns](/sql/t-sql/data-types/rowversion-transact-sql).</span></span> <span data-ttu-id="3f35b-349">Siempre se leen y se escriben en la base de datos mediante una matriz de 8 bytes.</span><span class="sxs-lookup"><span data-stu-id="3f35b-349">These are always read from and written to the database using an 8-byte array.</span></span> <span data-ttu-id="3f35b-350">Sin embargo, las matrices de bytes son un tipo de referencia mutable, lo que hace que sean bastante difíciles de tratar.</span><span class="sxs-lookup"><span data-stu-id="3f35b-350">However, byte arrays are a mutable reference type, which makes them somewhat painful to deal with.</span></span> <span data-ttu-id="3f35b-351">Los convertidores de valores permiten `rowversion` que se asigne en su lugar a una `ulong` propiedad, que es mucho más adecuada y fácil de usar que la matriz de bytes.</span><span class="sxs-lookup"><span data-stu-id="3f35b-351">Value converters allow the `rowversion` to instead be mapped to a `ulong` property, which is much more appropriate and easy to use than the byte array.</span></span> <span data-ttu-id="3f35b-352">Por ejemplo, considere una `Blog` entidad con un token de simultaneidad ulong:</span><span class="sxs-lookup"><span data-stu-id="3f35b-352">For example, consider a `Blog` entity with a ulong concurrency token:</span></span>

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public ulong Version { get; set; }
        }
-->
[!code-csharp[ULongConcurrencyModel](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ULongConcurrencyModel)]

<span data-ttu-id="3f35b-353">Se puede asignar a una columna de SQL Server `rowversion` mediante un convertidor de valores:</span><span class="sxs-lookup"><span data-stu-id="3f35b-353">This can be mapped to a SQL server `rowversion` column using a value converter:</span></span>

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Version)
                    .IsRowVersion()
                    .HasConversion<byte[]>();
-->
[!code-csharp[ConfigureULongConcurrency](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ConfigureULongConcurrency)]

### <a name="specify-the-datetimekind-when-reading-dates"></a><span data-ttu-id="3f35b-354">Especificar DateTime. Kind al leer fechas</span><span class="sxs-lookup"><span data-stu-id="3f35b-354">Specify the DateTime.Kind when reading dates</span></span>

<span data-ttu-id="3f35b-355">SQL Server descarta la <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> marca al almacenar un <xref:System.DateTime> como [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) o [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql) .</span><span class="sxs-lookup"><span data-stu-id="3f35b-355">SQL Server discards the <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> flag when storing a <xref:System.DateTime> as a [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) or [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql).</span></span> <span data-ttu-id="3f35b-356">Esto significa que los valores DateTime que se devuelven de la base de datos siempre tienen un <xref:System.DateTimeKind> de `Unspecified` .</span><span class="sxs-lookup"><span data-stu-id="3f35b-356">This means that DateTime values coming back from the database always have a <xref:System.DateTimeKind> of `Unspecified`.</span></span>

<span data-ttu-id="3f35b-357">Los convertidores de valores se pueden usar de dos maneras para solucionar este.</span><span class="sxs-lookup"><span data-stu-id="3f35b-357">Value converters can be used in two ways to deal with this.</span></span> <span data-ttu-id="3f35b-358">En primer lugar, EF Core tiene un convertidor de valores que crea un valor opaco de 8 bytes que conserva la `Kind` marca.</span><span class="sxs-lookup"><span data-stu-id="3f35b-358">First, EF Core has a value converter that creates an 8-byte opaque value which preserves the `Kind` flag.</span></span> <span data-ttu-id="3f35b-359">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3f35b-359">For example:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.PostedOn)
                    .HasConversion<long>();
-->
[!code-csharp[ConfigurePreserveDateTimeKind1](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind1)]

<span data-ttu-id="3f35b-360">Esto permite mezclar valores de fecha y hora con `Kind` marcas diferentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3f35b-360">This allows DateTime values with different `Kind` flags to be mixed in the database.</span></span>

<span data-ttu-id="3f35b-361">El problema de este enfoque es que la base de datos ya no tiene `datetime` columnas o reconocibles `datetime2` .</span><span class="sxs-lookup"><span data-stu-id="3f35b-361">The problem with this approach is that the database no longer has recognizable `datetime` or `datetime2` columns.</span></span> <span data-ttu-id="3f35b-362">Por lo tanto, es habitual almacenar siempre la hora UTC (o, con menos frecuencia, la hora local siempre) y, a continuación, omitir la `Kind` marca o establecerla en el valor adecuado mediante un convertidor de valores.</span><span class="sxs-lookup"><span data-stu-id="3f35b-362">So instead it is common to always store UTC time (or, less commonly, always local time) and then either ignore the `Kind` flag or set it to the appropriate value using a value converter.</span></span> <span data-ttu-id="3f35b-363">Por ejemplo, el convertidor siguiente garantiza que el `DateTime` valor leído de la base de datos tendrá <xref:System.DateTimeKind> `UTC` :</span><span class="sxs-lookup"><span data-stu-id="3f35b-363">For example, the converter below ensures that the `DateTime` value read from the database will have the <xref:System.DateTimeKind> `UTC`:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v,
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind2](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind2)]

<span data-ttu-id="3f35b-364">Si se establece una combinación de valores locales y UTC en instancias de entidad, el convertidor se puede usar para convertir correctamente antes de la inserción.</span><span class="sxs-lookup"><span data-stu-id="3f35b-364">If a mix of local and UTC values are being set in entity instances, then the converter can be used to convert appropriately before inserting.</span></span> <span data-ttu-id="3f35b-365">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3f35b-365">For example:</span></span>

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v.ToUniversalTime(),
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind3](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind3)]

> [!NOTE]
> <span data-ttu-id="3f35b-366">Considere cuidadosamente la posibilidad de unificar todo el código de acceso a la base de datos para usar la hora UTC todo el tiempo, solo para tratar con la hora local al presentar los datos a los usuarios.</span><span class="sxs-lookup"><span data-stu-id="3f35b-366">Carefully consider unifying all database access code to use UTC time all the time, only dealing with local time when presenting data to users.</span></span>

### <a name="use-case-insensitive-string-keys"></a><span data-ttu-id="3f35b-367">Usar claves de cadena que no distinguen mayúsculas de minúsculas</span><span class="sxs-lookup"><span data-stu-id="3f35b-367">Use case-insensitive string keys</span></span>

<span data-ttu-id="3f35b-368">Algunas bases de datos, incluidas SQL Server, realizan comparaciones de cadenas que no distinguen mayúsculas de minúsculas de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="3f35b-368">Some databases, including SQL Server, perform case-insensitive string comparisons by default.</span></span> <span data-ttu-id="3f35b-369">.NET, por otro lado, realiza comparaciones de cadenas que distinguen entre mayúsculas y minúsculas de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="3f35b-369">.NET, on the other hand, performs case-sensitive string comparisons by default.</span></span> <span data-ttu-id="3f35b-370">Esto significa que un valor de clave externa como "DotNet" coincidirá con el valor de clave principal "dotnet" en SQL Server, pero no coincidirá en EF Core.</span><span class="sxs-lookup"><span data-stu-id="3f35b-370">This means that a foreign key value like "DotNet" will match the primary key value "dotnet" on SQL Server, but will not match it in EF Core.</span></span> <span data-ttu-id="3f35b-371">Un comparador de valores para las claves se puede usar para forzar la EF Core en comparaciones de cadenas sin distinción entre mayúsculas y minúsculas como en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3f35b-371">A value comparer for keys can be used to force EF Core into case-insensitive string comparisons like in the database.</span></span> <span data-ttu-id="3f35b-372">Por ejemplo, considere un modelo de blog o publicaciones con claves de cadena:</span><span class="sxs-lookup"><span data-stu-id="3f35b-372">For example, consider a blog/posts model with string keys:</span></span>

<!--
        public class Blog
        {
            public string Id { get; set; }
            public string Name { get; set; }

            public ICollection<Post> Posts { get; set; }
        }

        public class Post
        {
            public string Id { get; set; }
            public string Title { get; set; }
            public string Content { get; set; }

            public string BlogId { get; set; }
            public Blog Blog { get; set; }
        }
-->
[!code-csharp[CaseInsensitiveStringsModel](../../../samples/core/Modeling/ValueConversions/CaseInsensitiveStrings.cs?name=CaseInsensitiveStringsModel)]

<span data-ttu-id="3f35b-373">Esto no funcionará según lo esperado si algunos de los `Post.BlogId` valores tienen distintas mayúsculas y minúsculas.</span><span class="sxs-lookup"><span data-stu-id="3f35b-373">This will not work as expected if some of the `Post.BlogId` values have different casing.</span></span> <span data-ttu-id="3f35b-374">Los errores causados por esto dependerán de lo que esté haciendo la aplicación, pero normalmente implican gráficos de objetos que no se han [corregido](xref:core/change-tracking/relationship-changes) correctamente y/o actualizaciones que generan un error porque el valor de FK es incorrecto.</span><span class="sxs-lookup"><span data-stu-id="3f35b-374">The errors caused by this will depend on what the application is doing, but typically involve graphs of objects that are not [fixed-up](xref:core/change-tracking/relationship-changes) correctly, and/or updates that fail because the FK value is wrong.</span></span> <span data-ttu-id="3f35b-375">Se puede utilizar un comparador de valores para corregir este:</span><span class="sxs-lookup"><span data-stu-id="3f35b-375">A value comparer can be used to correct this:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var comparer = new ValueComparer<string>(
                    (l, r) => string.Equals(l, r, StringComparison.OrdinalIgnoreCase),
                    v => v.ToUpper().GetHashCode(),
                    v => v);

                modelBuilder.Entity<Blog>()
                    .Property(e => e.Id)
                    .Metadata.SetValueComparer(comparer);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).Metadata.SetValueComparer(comparer);
                            b.Property(e => e.BlogId).Metadata.SetValueComparer(comparer);
                        });
            }
-->
[!code-csharp[ConfigureCaseInsensitiveStrings](../../../samples/core/Modeling/ValueConversions/CaseInsensitiveStrings.cs?name=ConfigureCaseInsensitiveStrings)]

> [!NOTE]
> <span data-ttu-id="3f35b-376">Las comparaciones de cadenas de .NET y las comparaciones de cadenas de base de datos pueden diferir en algo más que la distinción de mayúsculas</span><span class="sxs-lookup"><span data-stu-id="3f35b-376">.NET string comparisons and database string comparisons can differ in more than just case sensitivity.</span></span> <span data-ttu-id="3f35b-377">Este modelo funciona con claves ASCII simples, pero puede producir un error en las claves con cualquier tipo de caracteres específicos de la referencia cultural.</span><span class="sxs-lookup"><span data-stu-id="3f35b-377">This pattern works for simple ASCII keys, but may fail for keys with any kind of culture-specific characters.</span></span> <span data-ttu-id="3f35b-378">Consulte [intercalaciones y distinción entre mayúsculas y minúsculas](xref:core/miscellaneous/collations-and-case-sensitivity) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="3f35b-378">See [Collations and Case Sensitivity](xref:core/miscellaneous/collations-and-case-sensitivity) for more information.</span></span>

### <a name="handle-fixed-length-database-strings"></a><span data-ttu-id="3f35b-379">Controlar cadenas de base de datos de longitud fija</span><span class="sxs-lookup"><span data-stu-id="3f35b-379">Handle fixed-length database strings</span></span>

<span data-ttu-id="3f35b-380">El ejemplo anterior no necesita un convertidor de valores.</span><span class="sxs-lookup"><span data-stu-id="3f35b-380">The previous example did not need a value converter.</span></span> <span data-ttu-id="3f35b-381">Sin embargo, un convertidor puede ser útil para los tipos de cadena de base de datos de longitud fija como `char(20)` o `nchar(20)` .</span><span class="sxs-lookup"><span data-stu-id="3f35b-381">However, a converter can be useful for fixed-length database string types like `char(20)` or `nchar(20)`.</span></span> <span data-ttu-id="3f35b-382">Las cadenas de longitud fija se rellenan hasta su longitud completa cada vez que se inserta un valor en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3f35b-382">Fixed-length strings are padded to their full length whenever a value is inserted into the database.</span></span> <span data-ttu-id="3f35b-383">Esto significa que el valor de clave " `dotnet` " se leerá desde la base de datos como " `dotnet..............` ", donde `.` representa un carácter de espacio.</span><span class="sxs-lookup"><span data-stu-id="3f35b-383">This means that a key value of "`dotnet`" will be read back from the database as "`dotnet..............`", where `.` represents a space character.</span></span> <span data-ttu-id="3f35b-384">Esto no se comparará correctamente con los valores de clave que no se rellenan.</span><span class="sxs-lookup"><span data-stu-id="3f35b-384">This will then not compare correctly with key values that are not padded.</span></span>

<span data-ttu-id="3f35b-385">Se puede usar un convertidor de valores para recortar el relleno al leer los valores de clave.</span><span class="sxs-lookup"><span data-stu-id="3f35b-385">A value converter can be used to trim the padding when reading key values.</span></span> <span data-ttu-id="3f35b-386">Se puede combinar con el comparador de valores en el ejemplo anterior para comparar correctamente las claves ASCII de longitud fija que no distinguen mayúsculas de minúsculas.</span><span class="sxs-lookup"><span data-stu-id="3f35b-386">This can be combined with the value comparer in the previous example to compare fixed length case-insensitive ASCII keys correctly.</span></span> <span data-ttu-id="3f35b-387">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="3f35b-387">For example:</span></span>

<!--
            protected override void OnModelCreating(ModelBuilder modelBuilder)
            {
                var converter = new ValueConverter<string, string>(
                    v => v,
                    v => v.Trim());
                
                var comparer = new ValueComparer<string>(
                    (l, r) => string.Equals(l, r, StringComparison.OrdinalIgnoreCase),
                    v => v.ToUpper().GetHashCode(),
                    v => v);

                modelBuilder.Entity<Blog>()
                    .Property(e => e.Id)
                    .HasColumnType("char(20)")
                    .HasConversion(converter, comparer);

                modelBuilder.Entity<Post>(
                    b =>
                        {
                            b.Property(e => e.Id).HasColumnType("char(20)").HasConversion(converter, comparer);
                            b.Property(e => e.BlogId).HasColumnType("char(20)").HasConversion(converter, comparer);
                        });
            }
-->
[!code-csharp[ConfigureFixedLengthStrings](../../../samples/core/Modeling/ValueConversions/FixedLengthStrings.cs?name=ConfigureFixedLengthStrings)]

### <a name="encrypt-property-values"></a><span data-ttu-id="3f35b-388">Cifrar valores de propiedad</span><span class="sxs-lookup"><span data-stu-id="3f35b-388">Encrypt property values</span></span>

<span data-ttu-id="3f35b-389">Los convertidores de valores se pueden usar para cifrar los valores de propiedad antes de enviarlos a la base de datos y, a continuación, descifrarlos de forma que salgan. Por ejemplo, el uso de la inversión de cadenas como sustituto de un algoritmo de cifrado real:</span><span class="sxs-lookup"><span data-stu-id="3f35b-389">Value converters can be used to encrypt property values before sending them to the database, and then decrypt them on the way out. For example, using string reversal as a substitute for a real encryption algorithm:</span></span>

<!--
                modelBuilder.Entity<User>().Property(e => e.Password).HasConversion(
                    v => new string(v.Reverse().ToArray()),
                    v => new string(v.Reverse().ToArray()));
-->
[!code-csharp[ConfigureEncryptPropertyValues](../../../samples/core/Modeling/ValueConversions/EncryptPropertyValues.cs?name=ConfigureEncryptPropertyValues)]

> [!NOTE]
> <span data-ttu-id="3f35b-390">Actualmente no hay ninguna manera de obtener una referencia al DbContext actual u otro estado de sesión, desde dentro de un convertidor de valores.</span><span class="sxs-lookup"><span data-stu-id="3f35b-390">There is currently no way to get a reference to the current DbContext, or other session state, from within a value converter.</span></span> <span data-ttu-id="3f35b-391">Esto limita los tipos de cifrado que se pueden usar.</span><span class="sxs-lookup"><span data-stu-id="3f35b-391">This limits the kinds of encryption that can be used.</span></span> <span data-ttu-id="3f35b-392">Vote por el [problema de GitHub #11597](https://github.com/dotnet/efcore/issues/12205) para quitar esta limitación.</span><span class="sxs-lookup"><span data-stu-id="3f35b-392">Vote for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/12205) to have this limitation removed.</span></span>

> [!WARNING]
> <span data-ttu-id="3f35b-393">Asegúrese de comprender todas las implicaciones si implementa su propio cifrado para proteger los datos confidenciales.</span><span class="sxs-lookup"><span data-stu-id="3f35b-393">Make sure to understand all the implications if you roll your own encryption to protect sensitive data.</span></span> <span data-ttu-id="3f35b-394">En su lugar, considere la posibilidad de usar mecanismos de cifrado predefinidos, como [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) en SQL Server.</span><span class="sxs-lookup"><span data-stu-id="3f35b-394">Consider instead using pre-built encryption mechanisms, such as [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) on SQL Server.</span></span>

## <a name="limitations"></a><span data-ttu-id="3f35b-395">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="3f35b-395">Limitations</span></span>

<span data-ttu-id="3f35b-396">Hay algunas limitaciones actuales conocidas del sistema de conversión de valores:</span><span class="sxs-lookup"><span data-stu-id="3f35b-396">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="3f35b-397">Actualmente no hay ninguna manera de especificar en un lugar que cada propiedad de un tipo determinado debe usar el mismo convertidor de valores.</span><span class="sxs-lookup"><span data-stu-id="3f35b-397">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="3f35b-398">Por favor, vote ( 👍 ) para obtener el [problema de github #10784](https://github.com/dotnet/efcore/issues/10784) si es algo que necesita.</span><span class="sxs-lookup"><span data-stu-id="3f35b-398">Please vote (👍) for [GitHub issue #10784](https://github.com/dotnet/efcore/issues/10784) if this is something you need.</span></span>
* <span data-ttu-id="3f35b-399">Como se indicó anteriormente, `null` no se puede convertir.</span><span class="sxs-lookup"><span data-stu-id="3f35b-399">As noted above, `null` cannot be converted.</span></span> <span data-ttu-id="3f35b-400">Por favor, vote ( 👍 ) para obtener el [problema de github #13850](https://github.com/dotnet/efcore/issues/13850) si es algo que necesita.</span><span class="sxs-lookup"><span data-stu-id="3f35b-400">Please vote (👍) for [GitHub issue #13850](https://github.com/dotnet/efcore/issues/13850) if this is something you need.</span></span>
* <span data-ttu-id="3f35b-401">Actualmente no hay ninguna manera de distribuir una conversión de una propiedad a varias columnas o viceversa.</span><span class="sxs-lookup"><span data-stu-id="3f35b-401">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span> <span data-ttu-id="3f35b-402">Por favor, vote ( 👍 ) para obtener el [problema de github #13947](https://github.com/dotnet/efcore/issues/13947) si es algo que necesita.</span><span class="sxs-lookup"><span data-stu-id="3f35b-402">Please vote (👍) for [GitHub issue #13947](https://github.com/dotnet/efcore/issues/13947) if this is something you need.</span></span>
* <span data-ttu-id="3f35b-403">La mayoría de las claves asignadas a través de convertidores de valores no admiten la generación de valores.</span><span class="sxs-lookup"><span data-stu-id="3f35b-403">Value generation is not supported for most keys mapped through value converters.</span></span> <span data-ttu-id="3f35b-404">Por favor, vote ( 👍 ) para obtener el [problema de github #11597](https://github.com/dotnet/efcore/issues/11597) si es algo que necesita.</span><span class="sxs-lookup"><span data-stu-id="3f35b-404">Please vote (👍) for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/11597) if this is something you need.</span></span>
* <span data-ttu-id="3f35b-405">Las conversiones de valores no pueden hacer referencia a la instancia de DbContext actual.</span><span class="sxs-lookup"><span data-stu-id="3f35b-405">Value conversions cannot reference the current DbContext instance.</span></span> <span data-ttu-id="3f35b-406">Por favor, vote ( 👍 ) para obtener el [problema de github #11597](https://github.com/dotnet/efcore/issues/12205) si es algo que necesita.</span><span class="sxs-lookup"><span data-stu-id="3f35b-406">Please vote (👍) for [GitHub issue #11597](https://github.com/dotnet/efcore/issues/12205) if this is something you need.</span></span>

<span data-ttu-id="3f35b-407">La eliminación de estas limitaciones se tiene en cuenta para futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="3f35b-407">Removal of these limitations is being considered for future releases.</span></span>
