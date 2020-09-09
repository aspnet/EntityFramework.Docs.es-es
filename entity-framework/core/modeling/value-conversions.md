---
title: Conversiones de valores-EF Core
description: Configuración de convertidores de valores en un modelo de Entity Framework Core
author: ajcvickers
ms.date: 02/19/2018
ms.assetid: 3154BF3C-1749-4C60-8D51-AE86773AA116
uid: core/modeling/value-conversions
ms.openlocfilehash: 79e54392bf5503b4b651f25ce6e5fc63d418df90
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616662"
---
# <a name="value-conversions"></a><span data-ttu-id="cdcfb-103">Conversiones de valores</span><span class="sxs-lookup"><span data-stu-id="cdcfb-103">Value Conversions</span></span>

> [!NOTE]  
> <span data-ttu-id="cdcfb-104">Esta característica es nueva en EF Core 2.1.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-104">This feature is new in EF Core 2.1.</span></span>

<span data-ttu-id="cdcfb-105">Los convertidores de valores permiten convertir los valores de propiedad al leer o escribir en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-105">Value converters allow property values to be converted when reading from or writing to the database.</span></span> <span data-ttu-id="cdcfb-106">Esta conversión puede ser de un valor a otro del mismo tipo (por ejemplo, cifrar cadenas) o de un valor de un tipo a un valor de otro tipo (por ejemplo, convertir valores de enumeración en cadenas en la base de datos y desde ellas).</span><span class="sxs-lookup"><span data-stu-id="cdcfb-106">This conversion can be from one value to another of the same type (for example, encrypting strings) or from a value of one type to a value of another type (for example, converting enum values to and from strings in the database.)</span></span>

## <a name="fundamentals"></a><span data-ttu-id="cdcfb-107">Aspectos básicos</span><span class="sxs-lookup"><span data-stu-id="cdcfb-107">Fundamentals</span></span>

<span data-ttu-id="cdcfb-108">Los convertidores de valores se especifican en términos de `ModelClrType` y `ProviderClrType` .</span><span class="sxs-lookup"><span data-stu-id="cdcfb-108">Value converters are specified in terms of a `ModelClrType` and a `ProviderClrType`.</span></span> <span data-ttu-id="cdcfb-109">El tipo de modelo es el tipo .NET de la propiedad en el tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-109">The model type is the .NET type of the property in the entity type.</span></span> <span data-ttu-id="cdcfb-110">El tipo de proveedor es el tipo .NET que entiende el proveedor de base de datos.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-110">The provider type is the .NET type understood by the database provider.</span></span> <span data-ttu-id="cdcfb-111">Por ejemplo, para guardar las enumeraciones como cadenas en la base de datos, el tipo de modelo es el tipo de la enumeración y el tipo de proveedor es `String` .</span><span class="sxs-lookup"><span data-stu-id="cdcfb-111">For example, to save enums as strings in the database, the model type is the type of the enum, and the provider type is `String`.</span></span> <span data-ttu-id="cdcfb-112">Estos dos tipos pueden ser iguales.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-112">These two types can be the same.</span></span>

<span data-ttu-id="cdcfb-113">Las conversiones se definen mediante dos `Func` árboles de expresión: uno de `ModelClrType` a `ProviderClrType` y el otro de `ProviderClrType` a `ModelClrType` .</span><span class="sxs-lookup"><span data-stu-id="cdcfb-113">Conversions are defined using two `Func` expression trees: one from `ModelClrType` to `ProviderClrType` and the other from `ProviderClrType` to `ModelClrType`.</span></span> <span data-ttu-id="cdcfb-114">Los árboles de expresión se usan para que se puedan compilar en el código de acceso a la base de datos para conversiones eficientes.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-114">Expression trees are used so that they can be compiled into the database access code for efficient conversions.</span></span> <span data-ttu-id="cdcfb-115">En las conversiones complejas, el árbol de expresión puede ser una llamada simple a un método que realiza la conversión.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-115">For complex conversions, the expression tree may be a simple call to a method that performs the conversion.</span></span>

## <a name="configuring-a-value-converter"></a><span data-ttu-id="cdcfb-116">Configurar un convertidor de valores</span><span class="sxs-lookup"><span data-stu-id="cdcfb-116">Configuring a value converter</span></span>

<span data-ttu-id="cdcfb-117">Las conversiones de valores se definen en las propiedades `OnModelCreating` del de `DbContext` .</span><span class="sxs-lookup"><span data-stu-id="cdcfb-117">Value conversions are defined on properties in the `OnModelCreating` of your `DbContext`.</span></span> <span data-ttu-id="cdcfb-118">Por ejemplo, considere una enumeración y un tipo de entidad definidos como:</span><span class="sxs-lookup"><span data-stu-id="cdcfb-118">For example, consider an enum and entity type defined as:</span></span>

``` csharp
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
```

<span data-ttu-id="cdcfb-119">A continuación, se pueden definir conversiones en `OnModelCreating` para almacenar los valores de enumeración como cadenas (por ejemplo, "Donkey", "Mule",...) en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="cdcfb-119">Then conversions can be defined in `OnModelCreating` to store the enum values as strings (for example, "Donkey", "Mule", ...) in the database:</span></span>

``` csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder
        .Entity<Rider>()
        .Property(e => e.Mount)
        .HasConversion(
            v => v.ToString(),
            v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));
}
```

> [!NOTE]  
> <span data-ttu-id="cdcfb-120">`null`Nunca se pasará un valor a un convertidor de valores.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-120">A `null` value will never be passed to a value converter.</span></span> <span data-ttu-id="cdcfb-121">Esto hace que la implementación de conversiones sea más sencilla y permite que se compartan entre propiedades que aceptan valores NULL y que no aceptan valores NULL.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-121">This makes the implementation of conversions easier and allows them to be shared amongst nullable and non-nullable properties.</span></span>

## <a name="the-valueconverter-class"></a><span data-ttu-id="cdcfb-122">La clase ValueConverter</span><span class="sxs-lookup"><span data-stu-id="cdcfb-122">The ValueConverter class</span></span>

<span data-ttu-id="cdcfb-123">Al llamar a `HasConversion` como se muestra anteriormente, se creará una `ValueConverter` instancia de y se establecerá en la propiedad.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-123">Calling `HasConversion` as shown above will create a `ValueConverter` instance and set it on the property.</span></span> <span data-ttu-id="cdcfb-124">`ValueConverter`En su lugar, se puede crear explícitamente.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-124">The `ValueConverter` can instead be created explicitly.</span></span> <span data-ttu-id="cdcfb-125">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="cdcfb-125">For example:</span></span>

``` csharp
var converter = new ValueConverter<EquineBeast, string>(
    v => v.ToString(),
    v => (EquineBeast)Enum.Parse(typeof(EquineBeast), v));

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="cdcfb-126">Esto puede ser útil cuando varias propiedades usan la misma conversión.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-126">This can be useful when multiple properties use the same conversion.</span></span>

> [!NOTE]  
> <span data-ttu-id="cdcfb-127">Actualmente no hay ninguna manera de especificar en un lugar que cada propiedad de un tipo determinado debe usar el mismo convertidor de valores.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-127">There is currently no way to specify in one place that every property of a given type must use the same value converter.</span></span> <span data-ttu-id="cdcfb-128">Esta característica se considerará para futuras versiones.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-128">This feature will be considered for a future release.</span></span>

## <a name="built-in-converters"></a><span data-ttu-id="cdcfb-129">Convertidores integrados</span><span class="sxs-lookup"><span data-stu-id="cdcfb-129">Built-in converters</span></span>

<span data-ttu-id="cdcfb-130">EF Core incluye un conjunto de clases predefinidas `ValueConverter` , que se encuentran en el `Microsoft.EntityFrameworkCore.Storage.ValueConversion` espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-130">EF Core ships with a set of pre-defined `ValueConverter` classes, found in the `Microsoft.EntityFrameworkCore.Storage.ValueConversion` namespace.</span></span> <span data-ttu-id="cdcfb-131">Dichos componentes son:</span><span class="sxs-lookup"><span data-stu-id="cdcfb-131">These are:</span></span>

* <span data-ttu-id="cdcfb-132">`BoolToZeroOneConverter` -Bool a cero y uno</span><span class="sxs-lookup"><span data-stu-id="cdcfb-132">`BoolToZeroOneConverter` - Bool to zero and one</span></span>
* <span data-ttu-id="cdcfb-133">`BoolToStringConverter` -Bool a cadenas como "Y" y "N"</span><span class="sxs-lookup"><span data-stu-id="cdcfb-133">`BoolToStringConverter` - Bool to strings such as "Y" and "N"</span></span>
* <span data-ttu-id="cdcfb-134">`BoolToTwoValuesConverter` -Bool a dos valores cualesquiera</span><span class="sxs-lookup"><span data-stu-id="cdcfb-134">`BoolToTwoValuesConverter` - Bool to any two values</span></span>
* <span data-ttu-id="cdcfb-135">`BytesToStringConverter` -Matriz de bytes a cadena codificada en Base64</span><span class="sxs-lookup"><span data-stu-id="cdcfb-135">`BytesToStringConverter` - Byte array to Base64-encoded string</span></span>
* <span data-ttu-id="cdcfb-136">`CastingConverter` -Conversiones que requieren solo una conversión de tipo</span><span class="sxs-lookup"><span data-stu-id="cdcfb-136">`CastingConverter` - Conversions that require only a type cast</span></span>
* <span data-ttu-id="cdcfb-137">`CharToStringConverter` -Char a cadena de carácter único</span><span class="sxs-lookup"><span data-stu-id="cdcfb-137">`CharToStringConverter` - Char to single character string</span></span>
* <span data-ttu-id="cdcfb-138">`DateTimeOffsetToBinaryConverter` -DateTimeOffset a código binario codificado de 64 bits</span><span class="sxs-lookup"><span data-stu-id="cdcfb-138">`DateTimeOffsetToBinaryConverter` - DateTimeOffset to binary-encoded 64-bit value</span></span>
* <span data-ttu-id="cdcfb-139">`DateTimeOffsetToBytesConverter` -DateTimeOffset a la matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="cdcfb-139">`DateTimeOffsetToBytesConverter` - DateTimeOffset to byte array</span></span>
* <span data-ttu-id="cdcfb-140">`DateTimeOffsetToStringConverter` -DateTimeOffset a cadena</span><span class="sxs-lookup"><span data-stu-id="cdcfb-140">`DateTimeOffsetToStringConverter` - DateTimeOffset to string</span></span>
* <span data-ttu-id="cdcfb-141">`DateTimeToBinaryConverter` -DateTime al valor de 64 bits, incluido DateTimeKind</span><span class="sxs-lookup"><span data-stu-id="cdcfb-141">`DateTimeToBinaryConverter` - DateTime to 64-bit value including DateTimeKind</span></span>
* <span data-ttu-id="cdcfb-142">`DateTimeToStringConverter` -DateTime a cadena</span><span class="sxs-lookup"><span data-stu-id="cdcfb-142">`DateTimeToStringConverter` - DateTime to string</span></span>
* <span data-ttu-id="cdcfb-143">`DateTimeToTicksConverter` : Fecha y hora en pasos</span><span class="sxs-lookup"><span data-stu-id="cdcfb-143">`DateTimeToTicksConverter` - DateTime to ticks</span></span>
* <span data-ttu-id="cdcfb-144">`EnumToNumberConverter` -Enum al número subyacente</span><span class="sxs-lookup"><span data-stu-id="cdcfb-144">`EnumToNumberConverter` - Enum to underlying number</span></span>
* <span data-ttu-id="cdcfb-145">`EnumToStringConverter` -Enum a cadena</span><span class="sxs-lookup"><span data-stu-id="cdcfb-145">`EnumToStringConverter` - Enum to string</span></span>
* <span data-ttu-id="cdcfb-146">`GuidToBytesConverter` -GUID a matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="cdcfb-146">`GuidToBytesConverter` - Guid to byte array</span></span>
* <span data-ttu-id="cdcfb-147">`GuidToStringConverter` -GUID a cadena</span><span class="sxs-lookup"><span data-stu-id="cdcfb-147">`GuidToStringConverter` - Guid to string</span></span>
* <span data-ttu-id="cdcfb-148">`NumberToBytesConverter` -Cualquier valor numérico a matriz de bytes</span><span class="sxs-lookup"><span data-stu-id="cdcfb-148">`NumberToBytesConverter` - Any numerical value to byte array</span></span>
* <span data-ttu-id="cdcfb-149">`NumberToStringConverter` -Cualquier valor numérico a cadena</span><span class="sxs-lookup"><span data-stu-id="cdcfb-149">`NumberToStringConverter` - Any numerical value to string</span></span>
* <span data-ttu-id="cdcfb-150">`StringToBytesConverter` -Cadena en bytes UTF8</span><span class="sxs-lookup"><span data-stu-id="cdcfb-150">`StringToBytesConverter` - String to UTF8 bytes</span></span>
* <span data-ttu-id="cdcfb-151">`TimeSpanToStringConverter` -TimeSpan a cadena</span><span class="sxs-lookup"><span data-stu-id="cdcfb-151">`TimeSpanToStringConverter` - TimeSpan to string</span></span>
* <span data-ttu-id="cdcfb-152">`TimeSpanToTicksConverter` -Intervalo de tiempo a pasos</span><span class="sxs-lookup"><span data-stu-id="cdcfb-152">`TimeSpanToTicksConverter` - TimeSpan to ticks</span></span>

<span data-ttu-id="cdcfb-153">Tenga en cuenta que `EnumToStringConverter` se incluye en esta lista.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-153">Notice that `EnumToStringConverter` is included in this list.</span></span> <span data-ttu-id="cdcfb-154">Esto significa que no es necesario especificar la conversión explícitamente, como se muestra anteriormente.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-154">This means that there is no need to specify the conversion explicitly, as shown above.</span></span> <span data-ttu-id="cdcfb-155">En su lugar, use simplemente el convertidor integrado:</span><span class="sxs-lookup"><span data-stu-id="cdcfb-155">Instead, just use the built-in converter:</span></span>

``` csharp
var converter = new EnumToStringConverter<EquineBeast>();

modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion(converter);
```

<span data-ttu-id="cdcfb-156">Tenga en cuenta que todos los convertidores integrados no tienen estado y, por tanto, una sola instancia puede compartirse de forma segura con varias propiedades.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-156">Note that all the built-in converters are stateless and so a single instance can be safely shared by multiple properties.</span></span>

## <a name="pre-defined-conversions"></a><span data-ttu-id="cdcfb-157">Conversiones predefinidas</span><span class="sxs-lookup"><span data-stu-id="cdcfb-157">Pre-defined conversions</span></span>

<span data-ttu-id="cdcfb-158">En el caso de las conversiones comunes para las que existe un convertidor integrado, no es necesario especificar el convertidor explícitamente.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-158">For common conversions for which a built-in converter exists there is no need to specify the converter explicitly.</span></span> <span data-ttu-id="cdcfb-159">En su lugar, solo tiene que configurar el tipo de proveedor que se debe usar y EF usará automáticamente el convertidor integrado adecuado.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-159">Instead, just configure which provider type should be used and EF will automatically use the appropriate built-in converter.</span></span> <span data-ttu-id="cdcfb-160">La enumeración de las conversiones de cadenas se usa como ejemplo anterior, pero EF lo hará automáticamente si se configura el tipo de proveedor:</span><span class="sxs-lookup"><span data-stu-id="cdcfb-160">Enum to string conversions are used as an example above, but EF will actually do this automatically if the provider type is configured:</span></span>

``` csharp
modelBuilder
    .Entity<Rider>()
    .Property(e => e.Mount)
    .HasConversion<string>();
```

<span data-ttu-id="cdcfb-161">Lo mismo se puede lograr especificando explícitamente el tipo de columna.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-161">The same thing can be achieved by explicitly specifying the column type.</span></span> <span data-ttu-id="cdcfb-162">Por ejemplo, si el tipo de entidad se define de la manera siguiente:</span><span class="sxs-lookup"><span data-stu-id="cdcfb-162">For example, if the entity type is defined like so:</span></span>

``` csharp
public class Rider
{
    public int Id { get; set; }

    [Column(TypeName = "nvarchar(24)")]
    public EquineBeast Mount { get; set; }
}
```

<span data-ttu-id="cdcfb-163">A continuación, los valores de enumeración se guardarán como cadenas en la base de datos sin ninguna configuración adicional en `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="cdcfb-163">Then the enum values will be saved as strings in the database without any further configuration in `OnModelCreating`.</span></span>

## <a name="limitations"></a><span data-ttu-id="cdcfb-164">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="cdcfb-164">Limitations</span></span>

<span data-ttu-id="cdcfb-165">Hay algunas limitaciones actuales conocidas del sistema de conversión de valores:</span><span class="sxs-lookup"><span data-stu-id="cdcfb-165">There are a few known current limitations of the value conversion system:</span></span>

* <span data-ttu-id="cdcfb-166">Como se indicó anteriormente, `null` no se puede convertir.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-166">As noted above, `null` cannot be converted.</span></span>
* <span data-ttu-id="cdcfb-167">Actualmente no hay ninguna manera de distribuir una conversión de una propiedad a varias columnas o viceversa.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-167">There is currently no way to spread a conversion of one property to multiple columns or vice-versa.</span></span>
* <span data-ttu-id="cdcfb-168">El uso de conversiones de valores puede afectar a la capacidad de EF Core para traducir expresiones a SQL.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-168">Use of value conversions may impact the ability of EF Core to translate expressions to SQL.</span></span> <span data-ttu-id="cdcfb-169">Se registrará una advertencia para dichos casos.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-169">A warning will be logged for such cases.</span></span>
<span data-ttu-id="cdcfb-170">La eliminación de estas limitaciones se está considerando en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="cdcfb-170">Removal of these limitations is being considered for a future release.</span></span>
