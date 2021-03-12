---
title: Conversiones de valores-EF Core
description: Configuración de convertidores de valores en un modelo de Entity Framework Core
author: ajcvickers
ms.date: 01/16/2021
uid: core/modeling/value-conversions
ms.openlocfilehash: 114a18e41804c85c97791047aa915c2ec38dea15
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023892"
---
# <a name="value-conversions"></a>Conversiones de valores

Los convertidores de valores permiten convertir los valores de propiedad al leer o escribir en la base de datos. Esta conversión puede ser de un valor a otro del mismo tipo (por ejemplo, cifrar cadenas) o de un valor de un tipo a un valor de otro tipo (por ejemplo, convertir valores de enumeración en cadenas en la base de datos y desde ellas).

> [!TIP]
> Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Modeling/ValueConversions/).

## <a name="overview"></a>Información general

Los convertidores de valores se especifican en términos de `ModelClrType` y `ProviderClrType` . El tipo de modelo es el tipo .NET de la propiedad en el tipo de entidad. El tipo de proveedor es el tipo .NET que entiende el proveedor de base de datos. Por ejemplo, para guardar las enumeraciones como cadenas en la base de datos, el tipo de modelo es el tipo de la enumeración y el tipo de proveedor es `String` . Estos dos tipos pueden ser iguales.

Las conversiones se definen mediante dos `Func` árboles de expresión: uno de `ModelClrType` a `ProviderClrType` y el otro de `ProviderClrType` a `ModelClrType` . Los árboles de expresión se usan para que se puedan compilar en el delegado de acceso a la base de datos para conversiones eficaces. El árbol de expresión puede contener una llamada simple a un método de conversión para conversiones complejas.

> [!NOTE]
> Una propiedad que se ha configurado para la conversión de valores también puede necesitar especificar un <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> . Vea los ejemplos siguientes y la documentación de [comparadores de valores](xref:core/modeling/value-comparers) para obtener más información.

## <a name="configuring-a-value-converter"></a>Configurar un convertidor de valores

Las conversiones de valores se configuran en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType> . Por ejemplo, considere una enumeración y un tipo de entidad definidos como:

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

Las conversiones se pueden configurar en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> para almacenar los valores de enumeración como cadenas como "Donkey", "Mule", etc. en la base de datos; solo tiene que proporcionar una función que convierta de `ModelClrType` a `ProviderClrType` y otra para la conversión opuesta:

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
> `null`Nunca se pasará un valor a un convertidor de valores. Un valor null en una columna de base de datos siempre es un valor null en la instancia de la entidad y viceversa. Esto hace que la implementación de conversiones sea más sencilla y permite que se compartan entre propiedades que aceptan valores NULL y que no aceptan valores NULL. Vea el [problema de GitHub #13850](https://github.com/dotnet/efcore/issues/13850) para obtener más información.

## <a name="pre-defined-conversions"></a>Conversiones predefinidas

EF Core contiene muchas conversiones predefinidas que evitan la necesidad de escribir funciones de conversión manualmente. En su lugar, EF Core seleccionará la conversión que se va a usar en función del tipo de propiedad en el modelo y el tipo de proveedor de base de datos solicitado.

Por ejemplo, la enumeración de las conversiones de cadenas se usa como ejemplo anterior, pero EF Core realmente lo hará automáticamente cuando el tipo de proveedor esté configurado como `string` con el tipo genérico de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> :

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

Lo mismo se puede lograr especificando explícitamente el tipo de columna de la base de datos. Por ejemplo, si el tipo de entidad se define de la manera siguiente:

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

<!--
        public class Rider2
        {
            public int Id { get; set; }

            [Column(TypeName = "nvarchar(24)")]
            public EquineBeast Mount { get; set; }
        }
-->
[!code-csharp[ConversionByDatabaseType](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseType)]

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

<!--
                modelBuilder
                    .Entity<Rider2>()
                    .Property(e => e.Mount)
                    .HasColumnType("nvarchar(24)");
-->
[!code-csharp[ConversionByDatabaseTypeFluent](../../../samples/core/Modeling/ValueConversions/EnumToStringConversions.cs?name=ConversionByDatabaseTypeFluent)]

***

A continuación, los valores de enumeración se guardarán como cadenas en la base de datos sin ninguna configuración adicional en <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .

## <a name="the-valueconverter-class"></a>La clase ValueConverter

Al llamar a <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.HasConversion%2A> como se muestra anteriormente, se creará una <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> instancia de y se establecerá en la propiedad. `ValueConverter`En su lugar, se puede crear explícitamente. Por ejemplo:

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

Esto puede ser útil cuando varias propiedades usan la misma conversión.

## <a name="built-in-converters"></a>Convertidores integrados

Como se mencionó anteriormente, EF Core se suministra con un conjunto de clases predefinidas <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ValueConverter%602> , que se encuentra en el <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion> espacio de nombres. En muchos casos, EF elegirá el convertidor integrado adecuado en función del tipo de la propiedad en el modelo y del tipo solicitado en la base de datos, como se mostró anteriormente para las enumeraciones. Por ejemplo, `.HasConversion<int>()` el uso de en una propiedad hará que `bool` EF Core convierta valores bool a cero numérico y un valor:

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

Esto es funcionalmente lo mismo que crear una instancia del integrado <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> y establecerlo explícitamente:

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

En la tabla siguiente se resumen las conversiones predefinidas usadas con frecuencia de tipos de modelo o propiedad a tipos de proveedor de bases de datos. En la tabla `any_numeric_type` , una de `int` ,,, `short` `long` `byte` , `uint` , `ushort` , `ulong` , `sbyte` , `char` , `decimal` , `float` o `double` .

| Tipo de modelo/propiedad | Tipo de base de datos/proveedor | Conversión                                                | Uso
|:--------------------|------------------------|-----------------------------------------------------------|------
| bool                | any_numeric_type       | False/true a 0/1                                         | `.HasConversion<any_numeric_type>()`
|                     | any_numeric_type       | False/true para dos números cualesquiera                             | Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601>
|                     | string                 | False/true en "Y"/"N"                                     | `.HasConversion<string>()`
|                     | string                 | False/true para dos cadenas cualesquiera                             | Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter>
| any_numeric_type    | bool                   | de 0/1 a false/true                                         | `.HasConversion<bool>()`
|                     | any_numeric_type       | Conversión simple                                               | `.HasConversion<any_numeric_type>()`
|                     | string                 | El número como una cadena                                    | `.HasConversion<string>()`
| Enumeración                | any_numeric_type       | Valor numérico de la enumeración.                             | `.HasConversion<any_numeric_type>()`
|                     | string                 | Representación de cadena del valor de enumeración.               | `.HasConversion<string>()`
| string              | bool                   | Analiza la cadena como un valor booleano.                               | `.HasConversion<bool>()`
|                     | any_numeric_type       | Analiza la cadena como el tipo numérico especificado               | `.HasConversion<any_numeric_type>()`
|                     | char                   | Primer carácter de la cadena.                         | `.HasConversion<char>()`
|                     | DateTime               | Analiza la cadena como un valor de fecha y hora                           | `.HasConversion<DateTime>()`
|                     | DateTimeOffset         | Analiza la cadena como DateTimeOffset.                     | `.HasConversion<DateTimeOffset>()`
|                     | TimeSpan               | Analiza la cadena como un intervalo de tiempo                           | `.HasConversion<TimeSpan>()`
|                     | Guid                   | Analiza la cadena como un GUID.                               | `.HasConversion<Guid>()`
|                     | byte[]                 | La cadena como UTF8 bytes                                  | `.HasConversion<byte[]>()`
| char                | string                 | Una cadena de un solo carácter                                 | `.HasConversion<string>()`
| DateTime            | long                   | Fecha y hora de la conservación de DateTime. Kind                | `.HasConversion<long>()`
|                     | long                   | Pasos                                                     | Use <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter>
|                     | string                 | Cadena de fecha y hora de la referencia cultural de todos los idiomas                        | `.HasConversion<string>()`
| DateTimeOffset      | long                   | Fecha y hora codificadas con desplazamiento                             | `.HasConversion<long>()`
|                     | string                 | Cadena de fecha y hora de la referencia cultural de todos los idiomas con desplazamiento            | `.HasConversion<string>()`
| TimeSpan            | long                   | Pasos                                                     | `.HasConversion<long>()`
|                     | string                 | Cadena de intervalo de tiempo de referencia cultural de todos los idiomas                        | `.HasConversion<string>()`
| Identificador URI                 | string                 | El URI como una cadena                                       | `.HasConversion<string>()`
| PhysicalAddress     | string                 | La dirección como una cadena                                   | `.HasConversion<string>()`
|                     | byte[]                 | Bytes en el orden de red Big-Endian                         | `.HasConversion<byte[]>()`
| IPAddress           | string                 | La dirección como una cadena                                   | `.HasConversion<string>()`
|                     | byte[]                 | Bytes en el orden de red Big-Endian                         | `.HasConversion<byte[]>()`
| Guid                | string                 | GUID en formato ' dddddddd-dddd-dddd-dddd-dddddddddddd ' | `.HasConversion<string>()`
|                     | byte[]                 | Bytes en el orden de serialización binaria de .NET                  | `.HasConversion<byte[]>()`

Tenga en cuenta que estas conversiones suponen que el formato del valor es adecuado para la conversión. Por ejemplo, la conversión de cadenas en números producirá un error si los valores de cadena no se pueden analizar como números.

La lista completa de convertidores integrados es:

* Convertir propiedades bool:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToStringConverter> -Bool a cadenas como "Y" y "N"
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToTwoValuesConverter%601> -Bool a dos valores cualesquiera
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BoolToZeroOneConverter%601> -Bool a cero y uno
* Convertir propiedades de matriz de bytes:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.BytesToStringConverter> -Matriz de bytes a cadena codificada en Base64
* Cualquier conversión que requiera solo una conversión de tipos
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CastingConverter%602> -Conversiones que requieren solo una conversión de tipo
* Convertir propiedades char:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.CharToStringConverter> -Char a cadena de carácter único
* Convertir <xref:System.DateTimeOffset> propiedades:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBinaryConverter> - <xref:System.DateTimeOffset> en el valor de 64 bits con codificación binaria
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToBytesConverter> - <xref:System.DateTimeOffset> a la matriz de bytes
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeOffsetToStringConverter> - <xref:System.DateTimeOffset> a cadena
* Convertir <xref:System.DateTime> propiedades:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToBinaryConverter> - <xref:System.DateTime> en valor de 64 bits, incluido DateTimeKind
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToStringConverter> - <xref:System.DateTime> a cadena
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.DateTimeToTicksConverter> - <xref:System.DateTime> a TICs
* Convertir propiedades de enumeración:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToNumberConverter%602> -Enum al número subyacente
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.EnumToStringConverter%601> -Enum a cadena
* Convertir <xref:System.Guid> propiedades:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToBytesConverter> - <xref:System.Guid> a la matriz de bytes
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.GuidToStringConverter> - <xref:System.Guid> a cadena
* Convertir <xref:System.Net.IPAddress> propiedades:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToBytesConverter> - <xref:System.Net.IPAddress> a la matriz de bytes
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.IPAddressToStringConverter> - <xref:System.Net.IPAddress> a cadena
* Convertir propiedades numéricas (int, Double, decimal, etc.):
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToBytesConverter%601> -Cualquier valor numérico a matriz de bytes
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.NumberToStringConverter%601> -Cualquier valor numérico a cadena
* Convertir <xref:System.Net.NetworkInformation.PhysicalAddress> propiedades:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToBytesConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> a la matriz de bytes
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.PhysicalAddressToStringConverter> - <xref:System.Net.NetworkInformation.PhysicalAddress> a cadena
* Convertir propiedades de cadena:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBoolConverter> -Cadenas como "Y" y "N" a bool
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToBytesConverter> -Cadena en bytes UTF8
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToCharConverter> -Cadena a carácter
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeConverter> : Cadena que se va a <xref:System.DateTime>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToDateTimeOffsetConverter> : Cadena que se va a <xref:System.DateTimeOffset>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToEnumConverter%601> -Cadena que se va a enumerar
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToGuidConverter> : Cadena que se va a <xref:System.Guid>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToNumberConverter%601> -Cadena a tipo numérico
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToTimeSpanConverter> : Cadena que se va a <xref:System.TimeSpan>
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.StringToUriConverter> : Cadena que se va a <xref:System.Uri>
* Convertir <xref:System.TimeSpan> propiedades:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToStringConverter> - <xref:System.TimeSpan> a cadena
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.TimeSpanToTicksConverter> - <xref:System.TimeSpan> a TICs
* Convertir <xref:System.Uri> propiedades:
  * <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.UriToStringConverter> - <xref:System.Uri> a cadena

Tenga en cuenta que todos los convertidores integrados no tienen estado y, por tanto, una sola instancia puede compartirse de forma segura con varias propiedades.

## <a name="column-facets-and-mapping-hints"></a>Aspectos de las columnas y sugerencias de asignación

Algunos tipos de base de datos tienen aspectos que modifican la forma en que se almacenan. Entre ellas se incluyen las siguientes:

* Precisión y escala para decimales y columnas de fecha y hora
* Tamaño/longitud de las columnas binarias y de cadena
* Unicode para columnas de cadena

Estas caras se pueden configurar de forma normal para una propiedad que utiliza un convertidor de valores, y se aplicarán al tipo de base de datos convertido. Por ejemplo, al convertir de una enumeración a cadenas, podemos especificar que la columna de base de datos no debe ser Unicode y almacenar hasta 20 caracteres:

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

O bien, al crear el convertidor explícitamente:

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

Esto da como resultado una `varchar(20)` columna al utilizar EF Core migraciones en SQL Server:

```sql
CREATE TABLE [Rider] (
    [Id] int NOT NULL IDENTITY,
    [Mount] varchar(20) NOT NULL,
    CONSTRAINT [PK_Rider] PRIMARY KEY ([Id]));
```

Sin embargo, si, de forma predeterminada, todas `EquineBeast` las columnas deben ser `varchar(20)` , esta información se puede proporcionar al convertidor de valores como <xref:Microsoft.EntityFrameworkCore.Storage.ValueConversion.ConverterMappingHints> . Por ejemplo:

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

Ahora, cada vez que se use este convertidor, la columna de la base de datos no será Unicode con una longitud máxima de 20. Sin embargo, estas son solo sugerencias, ya que se reemplazan por cualquier aspecto establecida explícitamente en la propiedad asignada.

## <a name="examples"></a>Ejemplos

### <a name="simple-value-objects"></a>Objetos de valor simple

En este ejemplo se usa un tipo simple para encapsular un tipo primitivo. Esto puede ser útil si desea que el tipo del modelo sea más específico (y, por tanto, más seguro de tipos) que un tipo primitivo. En este ejemplo, ese tipo es `Dollars` , que ajusta el primitivo decimal:

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

Se puede usar en un tipo de entidad:

<!--
        public class Order
        {
            public int Id { get; set; }

            public Dollars Price { get; set; }
        }
-->
[!code-csharp[SimpleValueObjectModel](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=SimpleValueObjectModel)]

Y se convierten en los subyacentes `decimal` cuando se almacenan en la base de datos:

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => v.Amount,
                        v => new Dollars(v));
-->
[!code-csharp[ConfigureImmutableStructProperty](../../../samples/core/Modeling/ValueConversions/SimpleValueObject.cs?name=ConfigureImmutableStructProperty)]

> [!NOTE]
> Este objeto de valor se implementa como una [estructura de solo lectura](/dotnet/csharp/language-reference/builtin-types/struct). Esto significa que EF Core puede realizar instantáneas y comparar valores sin problemas. Consulte [comparadores de valores](xref:core/modeling/value-comparers) para obtener más información.

### <a name="composite-value-objects"></a>Objetos de valor compuesto

En el ejemplo anterior, el tipo de objeto de valor solo contenía una propiedad única. Es más común que un tipo de objeto de valor componga varias propiedades que juntos forman un concepto de dominio. Por ejemplo, un `Money` tipo general que contiene la cantidad y la moneda:

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

Este objeto de valor se puede usar en un tipo de entidad como antes:

<!--
        public class Order
        {
            public int Id { get; set; }

            public Money Price { get; set; }
        }
-->
[!code-csharp[CompositeValueObjectModel](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=CompositeValueObjectModel)]

Los convertidores de valores solo pueden convertir actualmente valores en una sola columna de base de datos. Esta limitación significa que todos los valores de propiedad del objeto se deben codificar en un valor de columna única. Normalmente, esto se controla mediante la serialización del objeto tal como entra en la base de datos y, a continuación, la deserialización de nuevo en el sentido. Por ejemplo, mediante <xref:System.Text.Json> :

<!--
                modelBuilder.Entity<Order>()
                    .Property(e => e.Price)
                    .HasConversion(
                        v => JsonSerializer.Serialize(v, null),
                        v => JsonSerializer.Deserialize<Money>(v, null));
-->
[!code-csharp[ConfigureCompositeValueObject](../../../samples/core/Modeling/ValueConversions/CompositeValueObject.cs?name=ConfigureCompositeValueObject)]

> [!NOTE]
> Tenemos previsto permitir la asignación de un objeto a varias columnas en EF Core 6,0, lo que elimina la necesidad de usar la serialización aquí. Esto se realiza mediante el [problema de GitHub #13947](https://github.com/dotnet/efcore/issues/13947).

> [!NOTE]
> Como en el ejemplo anterior, este objeto de valor se implementa como una [estructura de solo lectura](/dotnet/csharp/language-reference/builtin-types/struct). Esto significa que EF Core puede realizar instantáneas y comparar valores sin problemas. Consulte [comparadores de valores](xref:core/modeling/value-comparers) para obtener más información.

### <a name="collections-of-primitives"></a>Colecciones de primitivas

La serialización también se puede utilizar para almacenar una colección de valores primitivos. Por ejemplo:

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

<xref:System.Text.Json>Volver a usar:

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

`ICollection<string>` representa un tipo de referencia mutable. Esto significa que <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> se necesita un para que EF Core pueda realizar un seguimiento de los cambios y detectarlos correctamente. Consulte [comparadores de valores](xref:core/modeling/value-comparers) para obtener más información.

### <a name="collections-of-value-objects"></a>Colecciones de objetos de valor

Combinando los dos ejemplos anteriores juntos podemos crear una colección de objetos de valor. Por ejemplo, considere un `AnnualFinance` tipo que modela las finanzas del blog para un solo año:

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

Este tipo compone algunos de los tipos que `Money` hemos creado anteriormente:

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

A continuación, podemos agregar una colección de `AnnualFinance` a nuestro tipo de entidad:

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            
            public IList<AnnualFinance> Finances { get; set; }
        }
-->
[!code-csharp[ValueObjectCollectionModel](../../../samples/core/Modeling/ValueConversions/ValueObjectCollection.cs?name=ValueObjectCollectionModel)]

Y usan de nuevo la serialización para almacenar esto:

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
> Como antes, esta conversión requiere un <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ValueComparer%601> . Consulte [comparadores de valores](xref:core/modeling/value-comparers) para obtener más información.

### <a name="value-objects-as-keys"></a>Objetos de valor como claves

A veces, las propiedades de clave primitivas se pueden encapsular en objetos de valor para agregar un nivel adicional de seguridad de tipos en la asignación de valores. Por ejemplo, podríamos implementar un tipo de clave para los blogs y un tipo de clave para las publicaciones:

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

Estos se pueden usar en el modelo de dominio:

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

Observe que `Blog.Id` no se puede asignar accidentalmente un `PostKey` y `Post.Id` no se le puede asignar accidentalmente un `BlogKey` . Del mismo modo, `Post.BlogId` se debe asignar a la propiedad de clave externa `BlogKey` .

> [!NOTE]
> La visualización de este patrón no significa que se recomiende. Considere detenidamente si este nivel de abstracción está ayudando o dificultando su experiencia de desarrollo. Además, considere la posibilidad de usar navegaciones y claves generadas en lugar de tratar directamente con los valores de clave.

Después, estas propiedades de clave se pueden asignar mediante convertidores de valores:

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
> Actualmente, las propiedades clave con conversiones no pueden utilizar valores de clave generados. Vote por el [problema de GitHub #11597](https://github.com/dotnet/efcore/issues/11597) para quitar esta limitación.

### <a name="use-ulong-for-timestamprowversion"></a>Usar ulong para timestamp/rowversion

SQL Server admite la [simultaneidad optimista](xref:core/saving/concurrency) automática mediante [ `rowversion` / `timestamp` columnas binarias de 8 bytes](/sql/t-sql/data-types/rowversion-transact-sql). Siempre se leen y se escriben en la base de datos mediante una matriz de 8 bytes. Sin embargo, las matrices de bytes son un tipo de referencia mutable, lo que hace que sean bastante difíciles de tratar. Los convertidores de valores permiten `rowversion` que se asigne en su lugar a una `ulong` propiedad, que es mucho más adecuada y fácil de usar que la matriz de bytes. Por ejemplo, considere una `Blog` entidad con un token de simultaneidad ulong:

<!--
        public class Blog
        {
            public int Id { get; set; }
            public string Name { get; set; }
            public ulong Version { get; set; }
        }
-->
[!code-csharp[ULongConcurrencyModel](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ULongConcurrencyModel)]

Se puede asignar a una columna de SQL Server `rowversion` mediante un convertidor de valores:

<!--
                modelBuilder.Entity<Blog>()
                    .Property(e => e.Version)
                    .IsRowVersion()
                    .HasConversion<byte[]>();
-->
[!code-csharp[ConfigureULongConcurrency](../../../samples/core/Modeling/ValueConversions/ULongConcurrency.cs?name=ConfigureULongConcurrency)]

### <a name="specify-the-datetimekind-when-reading-dates"></a>Especificar DateTime. Kind al leer fechas

SQL Server descarta la <xref:System.DateTime.Kind%2A?displayProperty=nameWithType> marca al almacenar un <xref:System.DateTime> como [`datetime`](/sql/t-sql/data-types/datetime-transact-sql) o [`datetime2`](/sql/t-sql/data-types/datetime2-transact-sql) . Esto significa que los valores DateTime que se devuelven de la base de datos siempre tienen un <xref:System.DateTimeKind> de `Unspecified` .

Los convertidores de valores se pueden usar de dos maneras para solucionar este. En primer lugar, EF Core tiene un convertidor de valores que crea un valor opaco de 8 bytes que conserva la `Kind` marca. Por ejemplo:

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.PostedOn)
                    .HasConversion<long>();
-->
[!code-csharp[ConfigurePreserveDateTimeKind1](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind1)]

Esto permite mezclar valores de fecha y hora con `Kind` marcas diferentes en la base de datos.

El problema de este enfoque es que la base de datos ya no tiene `datetime` columnas o reconocibles `datetime2` . Por lo tanto, es habitual almacenar siempre la hora UTC (o, con menos frecuencia, la hora local siempre) y, a continuación, omitir la `Kind` marca o establecerla en el valor adecuado mediante un convertidor de valores. Por ejemplo, el convertidor siguiente garantiza que el `DateTime` valor leído de la base de datos tendrá <xref:System.DateTimeKind> `UTC` :

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v,
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind2](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind2)]

Si se establece una combinación de valores locales y UTC en instancias de entidad, el convertidor se puede usar para convertir correctamente antes de la inserción. Por ejemplo:

<!--
                modelBuilder.Entity<Post>()
                    .Property(e => e.LastUpdated)
                    .HasConversion(
                        v => v.ToUniversalTime(),
                        v => new DateTime(v.Ticks, DateTimeKind.Utc));
-->
[!code-csharp[ConfigurePreserveDateTimeKind3](../../../samples/core/Modeling/ValueConversions/PreserveDateTimeKind.cs?name=ConfigurePreserveDateTimeKind3)]

> [!NOTE]
> Considere cuidadosamente la posibilidad de unificar todo el código de acceso a la base de datos para usar la hora UTC todo el tiempo, solo para tratar con la hora local al presentar los datos a los usuarios.

### <a name="use-case-insensitive-string-keys"></a>Usar claves de cadena que no distinguen mayúsculas de minúsculas

Algunas bases de datos, incluidas SQL Server, realizan comparaciones de cadenas que no distinguen mayúsculas de minúsculas de forma predeterminada. .NET, por otro lado, realiza comparaciones de cadenas que distinguen entre mayúsculas y minúsculas de forma predeterminada. Esto significa que un valor de clave externa como "DotNet" coincidirá con el valor de clave principal "dotnet" en SQL Server, pero no coincidirá en EF Core. Un comparador de valores para las claves se puede usar para forzar la EF Core en comparaciones de cadenas sin distinción entre mayúsculas y minúsculas como en la base de datos. Por ejemplo, considere un modelo de blog o publicaciones con claves de cadena:

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

Esto no funcionará según lo esperado si algunos de los `Post.BlogId` valores tienen distintas mayúsculas y minúsculas. Los errores causados por esto dependerán de lo que esté haciendo la aplicación, pero normalmente implican gráficos de objetos que no se han [corregido](xref:core/change-tracking/relationship-changes) correctamente y/o actualizaciones que generan un error porque el valor de FK es incorrecto. Se puede utilizar un comparador de valores para corregir este:

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
> Las comparaciones de cadenas de .NET y las comparaciones de cadenas de base de datos pueden diferir en algo más que la distinción de mayúsculas Este modelo funciona con claves ASCII simples, pero puede producir un error en las claves con cualquier tipo de caracteres específicos de la referencia cultural. Consulte [intercalaciones y distinción entre mayúsculas y minúsculas](xref:core/miscellaneous/collations-and-case-sensitivity) para obtener más información.

### <a name="handle-fixed-length-database-strings"></a>Controlar cadenas de base de datos de longitud fija

El ejemplo anterior no necesita un convertidor de valores. Sin embargo, un convertidor puede ser útil para los tipos de cadena de base de datos de longitud fija como `char(20)` o `nchar(20)` . Las cadenas de longitud fija se rellenan hasta su longitud completa cada vez que se inserta un valor en la base de datos. Esto significa que el valor de clave " `dotnet` " se leerá desde la base de datos como " `dotnet..............` ", donde `.` representa un carácter de espacio. Esto no se comparará correctamente con los valores de clave que no se rellenan.

Se puede usar un convertidor de valores para recortar el relleno al leer los valores de clave. Se puede combinar con el comparador de valores en el ejemplo anterior para comparar correctamente las claves ASCII de longitud fija que no distinguen mayúsculas de minúsculas. Por ejemplo:

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

### <a name="encrypt-property-values"></a>Cifrar valores de propiedad

Los convertidores de valores se pueden usar para cifrar los valores de propiedad antes de enviarlos a la base de datos y, a continuación, descifrarlos de forma que salgan. Por ejemplo, el uso de la inversión de cadenas como sustituto de un algoritmo de cifrado real:

<!--
                modelBuilder.Entity<User>().Property(e => e.Password).HasConversion(
                    v => new string(v.Reverse().ToArray()),
                    v => new string(v.Reverse().ToArray()));
-->
[!code-csharp[ConfigureEncryptPropertyValues](../../../samples/core/Modeling/ValueConversions/EncryptPropertyValues.cs?name=ConfigureEncryptPropertyValues)]

> [!NOTE]
> Actualmente no hay ninguna manera de obtener una referencia al DbContext actual u otro estado de sesión, desde dentro de un convertidor de valores. Esto limita los tipos de cifrado que se pueden usar. Vote por el [problema de GitHub #11597](https://github.com/dotnet/efcore/issues/12205) para quitar esta limitación.

> [!WARNING]
> Asegúrese de comprender todas las implicaciones si implementa su propio cifrado para proteger los datos confidenciales. En su lugar, considere la posibilidad de usar mecanismos de cifrado predefinidos, como [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) en SQL Server.

## <a name="limitations"></a>Limitaciones

Hay algunas limitaciones actuales conocidas del sistema de conversión de valores:

* Actualmente no hay ninguna manera de especificar en un lugar que cada propiedad de un tipo determinado debe usar el mismo convertidor de valores. Por favor, vote ( 👍 ) para obtener el [problema de github #10784](https://github.com/dotnet/efcore/issues/10784) si es algo que necesita.
* Como se indicó anteriormente, `null` no se puede convertir. Por favor, vote ( 👍 ) para obtener el [problema de github #13850](https://github.com/dotnet/efcore/issues/13850) si es algo que necesita.
* Actualmente no hay ninguna manera de distribuir una conversión de una propiedad a varias columnas o viceversa. Por favor, vote ( 👍 ) para obtener el [problema de github #13947](https://github.com/dotnet/efcore/issues/13947) si es algo que necesita.
* La mayoría de las claves asignadas a través de convertidores de valores no admiten la generación de valores. Por favor, vote ( 👍 ) para obtener el [problema de github #11597](https://github.com/dotnet/efcore/issues/11597) si es algo que necesita.
* Las conversiones de valores no pueden hacer referencia a la instancia de DbContext actual. Por favor, vote ( 👍 ) para obtener el [problema de github #11597](https://github.com/dotnet/efcore/issues/12205) si es algo que necesita.

La eliminación de estas limitaciones se tiene en cuenta para futuras versiones.
