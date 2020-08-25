---
title: 'Trabajar con tipos de referencia que aceptan valores NULL: EF Core'
author: roji
ms.date: 09/09/2019
ms.assetid: bde4e0ee-fba3-4813-a849-27049323d301
uid: core/miscellaneous/nullable-reference-types
ms.openlocfilehash: 7d262ab9fb45535b626ce8d503b31a5e9a4630d3
ms.sourcegitcommit: 6f7af3f138bf7c724cbdda261f97e5cf7035e8d7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2020
ms.locfileid: "88847571"
---
# <a name="working-with-nullable-reference-types"></a>Trabajar con tipos de referencia que aceptan valores NULL

C# 8 presentó una nueva característica denominada [tipos de referencia que aceptan valores NULL](/dotnet/csharp/tutorials/nullable-reference-types), lo que permite anotar tipos de referencia, lo que indica si es válido para que contengan null o not. Si no está familiarizado con esta característica, se recomienda que se familiarice con ella leyendo los documentos de C#.

En esta página se presenta la compatibilidad de EF Core con tipos de referencia que aceptan valores NULL y se describen los procedimientos recomendados para trabajar con ellos.

## <a name="required-and-optional-properties"></a>Propiedades obligatorias y opcionales

La documentación principal sobre las propiedades obligatorias y opcionales y su interacción con tipos de referencia que aceptan valores NULL es la página de [propiedades obligatoria y opcional](xref:core/modeling/entity-properties#required-and-optional-properties) . Se recomienda comenzar leyendo primero esa página.

> [!NOTE]
> Tenga cuidado al habilitar los tipos de referencia que aceptan valores NULL en un proyecto existente: las propiedades de tipo de referencia que se configuraron anteriormente como opcional ahora se configurarán según sea necesario, a menos que se anoten explícitamente para que acepten valores NULL. Al administrar un esquema de base de datos relacional, esto puede provocar que se generen migraciones que modifiquen la nulabilidad de la columna de la base de datos.

## <a name="dbcontext-and-dbset"></a>DbContext y DbSet

Cuando se habilitan los tipos de referencia que aceptan valores NULL, el compilador de C# emite advertencias para cualquier propiedad no inicializada que no acepte valores NULL, ya que estos contendrían el valor null. Como resultado, la práctica común de tener propiedades DbSet sin inicializar en un tipo de contexto generará ahora una advertencia. Esto puede corregirse de la siguiente manera:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/NullableReferenceTypesContext.cs?name=Context&highlight=3-4)]

Otra estrategia consiste en usar propiedades automáticas que no aceptan valores NULL, pero para inicializarlas en null, mediante el operador null-permisivo (!) para silenciar la advertencia del compilador. El constructor DbContext garantiza que se inicialicen todas las propiedades DbSet y que nunca se observará null en ellas.

## <a name="non-nullable-properties-and-initialization"></a>Propiedades y inicialización que no aceptan valores NULL

Las advertencias del compilador para los tipos de referencia no inicializados que no aceptan valores null también son un problema para las propiedades normales en los tipos de entidad. En el ejemplo anterior, se han evitado estas advertencias mediante el [enlace de constructor](xref:core/modeling/constructors), una característica que funciona perfectamente con propiedades que no aceptan valores NULL, lo que garantiza que siempre se inicializan. Sin embargo, en algunos escenarios, el enlace del constructor no es una opción: las propiedades de navegación, por ejemplo, no se pueden inicializar de esta manera.

Las propiedades de navegación requeridas presentan una dificultad adicional: aunque un dependiente siempre existirá para una entidad de seguridad determinada, se puede cargar o no mediante una consulta determinada, dependiendo de las necesidades en ese punto del programa ([vea los distintos patrones para cargar datos](xref:core/querying/related-data)). Al mismo tiempo, no es deseable que estas propiedades acepten valores NULL, ya que esto obligaría a tener acceso a ellas para comprobar si son NULL, incluso aunque se requieran.

Una manera de tratar estos escenarios consiste en tener una propiedad que no acepte valores NULL con un [campo de respaldo](xref:core/modeling/backing-field)que acepte valores NULL:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=10-17)]

Dado que la propiedad de navegación no admite valores NULL, se configura una navegación necesaria. y, siempre y cuando la navegación se haya cargado correctamente, se podrá acceder al dependiente a través de la propiedad. Sin embargo, si se tiene acceso a la propiedad sin cargar primero la entidad relacionada correctamente, se inicia una excepción InvalidOperationException, ya que el contrato de la API se ha utilizado incorrectamente. Tenga en cuenta que EF debe configurarse para tener acceso siempre al campo de respaldo y no a la propiedad, ya que se basa en poder leer el valor aunque no se haya establecido. Consulte la documentación sobre [los campos de respaldo](xref:core/modeling/backing-field) para ver cómo hacerlo y considere la posibilidad de especificar `PropertyAccessMode.Field` para asegurarse de que la configuración es correcta.

Como alternativa de terser, es posible simplemente inicializar la propiedad en NULL con la ayuda del operador null-permisivo (!):

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Order.cs?range=19)]

Nunca se observará un valor nulo real excepto como resultado de un error de programación, por ejemplo, el acceso a la propiedad de navegación sin cargar correctamente la entidad relacionada.

> [!NOTE]
> Las navegaciones de colección, que contienen referencias a varias entidades relacionadas, siempre deben ser no NULL. Una colección vacía significa que no existe ninguna entidad relacionada, pero la propia lista nunca debe ser null.

## <a name="navigating-and-including-nullable-relationships"></a>Navegar y incluir relaciones que aceptan valores NULL

Cuando se trabaja con relaciones opcionales, es posible encontrar advertencias del compilador en las que una excepción de referencia nula real sería imposible. Cuando se traducen y ejecutan las consultas LINQ, EF Core garantiza que si no existe una entidad relacionada opcional, cualquier navegación a ella simplemente se omitirá, en lugar de producirse. Sin embargo, el compilador no es consciente de esta garantía EF Core y genera advertencias como si la consulta LINQ se ejecutara en memoria, con LINQ to Objects. Como resultado, es necesario usar el operador null-permisivo (!) para notificar al compilador que no es posible un valor null real:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=46)]

Se produce un problema similar al incluir varios niveles de relaciones entre las navegaciones opcionales:

[!code-csharp[Main](../../../samples/core/Miscellaneous/NullableReferenceTypes/Program.cs?range=36-39&highlight=2)]

Si tiene que hacer esto mucho y los tipos de entidad en cuestión son principalmente (o exclusivamente) usados en consultas de EF Core, considere la posibilidad de hacer que las propiedades de navegación no acepten valores NULL y configurarlas como opcionales a través de la API fluida o las anotaciones de datos. Esto quitará todas las advertencias del compilador manteniendo la relación opcional; sin embargo, si las entidades se recorren fuera de EF Core, puede observar valores NULL, aunque las propiedades se anotan como que no aceptan valores NULL.

## <a name="limitations"></a>Limitaciones

* La ingeniería inversa no admite actualmente los [tipos de referencia que aceptan valores NULL de C# 8 (NRTs)](/dotnet/csharp/tutorials/nullable-reference-types): EF Core siempre genera código de c# que supone que la característica está desactivada. Por ejemplo, las columnas de texto que aceptan valores NULL se scaffolding como una propiedad con `string` el tipo, no `string?` , con la API fluida o las anotaciones de datos que se usan para configurar si una propiedad es obligatoria o no. Puede editar el código con scaffolding y reemplazarlo con anotaciones de nulabilidad de C#. El seguimiento de la compatibilidad con scaffolding para tipos de referencia que aceptan valores NULL se realiza mediante el problema [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).
* La superficie de la API pública de EF Core todavía no se ha anotado para la nulabilidad (la API pública es "null-desconocen"), lo que a veces es difícil de usar cuando la característica NRT está activada. Esto incluye principalmente los operadores Async LINQ expuestos por EF Core, como [FirstOrDefaultAsync](/dotnet/api/microsoft.entityframeworkcore.entityframeworkqueryableextensions.firstordefaultasync#Microsoft_EntityFrameworkCore_EntityFrameworkQueryableExtensions_FirstOrDefaultAsync__1_System_Linq_IQueryable___0__System_Linq_Expressions_Expression_System_Func___0_System_Boolean___System_Threading_CancellationToken_). Tenemos previsto abordar esto para la versión 5,0.
