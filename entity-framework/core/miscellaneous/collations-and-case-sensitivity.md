---
title: 'Intercalaciones y distinción entre mayúsculas y minúsculas: EF Core'
description: Configuración de intercalaciones y distinción de mayúsculas y minúsculas en la base de datos y consultas con Entity Framework Core
author: roji
ms.date: 04/27/2020
uid: core/miscellaneous/collations-and-case-sensitivity
ms.openlocfilehash: e327df8adf777bfa5603a71eca8297a051f5bd56
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90071724"
---
# <a name="collations-and-case-sensitivity"></a>Intercalaciones y distinción entre mayúsculas y minúsculas

> [!NOTE]
> Esta característica se incluye por primera vez en EF Core 5.0.

El procesamiento de texto en las bases de datos puede ser un proceso complejo y requiere más atención al usuario de que se sospeche. Por un lado, las bases de datos varían considerablemente en el modo en que controlan el texto; por ejemplo, mientras que algunas bases de datos distinguen mayúsculas de minúsculas de forma predeterminada (por ejemplo, SQLite, PostgreSQL), otras no distinguen mayúsculas de minúsculas (SQL Server, MySQL). Además, debido al uso de índices, la distinción de mayúsculas y minúsculas y los aspectos similares pueden tener un impacto de gran alcance en el rendimiento de las consultas: aunque puede ser tentador utilizar `string.Lower` para forzar una comparación sin distinción entre mayúsculas y minúsculas en una base de datos que distingue mayúsculas de minúsculas, si lo hace, puede evitar que la aplicación use índices. En esta página se detalla cómo configurar la distinción de mayúsculas y minúsculas, o más en general, las intercalaciones y cómo hacerlo de manera eficaz sin poner en peligro el rendimiento de las consultas.

## <a name="introduction-to-collations"></a>Introducción a las intercalaciones

Un concepto fundamental en el procesamiento de texto es la *Intercalación*, que es un conjunto de reglas que determinan cómo se ordenan y comparan la igualdad de los valores de texto. Por ejemplo, mientras que una intercalación que no distingue entre mayúsculas y minúsculas no tiene en cuenta las diferencias entre las letras mayúsculas y minúsculas para los fines de la comparación de igualdad, una intercalación que distingue entre mayúsculas y minúsculas no lo hace. Sin embargo, dado que la distinción de mayúsculas y minúsculas es dependiente de la referencia cultural (por ejemplo, `i` y `I` representa una letra diferente en Turco), existen varias intercalaciones que distinguen entre mayúsculas y minúsculas, cada una con su propio conjunto de reglas. El ámbito de las intercalaciones también se extiende más allá de la distinción entre mayúsculas y minúsculas y otros aspectos de los datos de caracteres. en alemán, por ejemplo, a veces es conveniente tratar (pero no siempre) `ä` `ae` como idéntico. Por último, las intercalaciones también definen cómo se *ordenan*los valores de texto: mientras que el alemán coloca `ä` después `a` de, sueco lo coloca al final del alfabeto.

Todas las operaciones de texto en una base de datos utilizan una intercalación, ya sea explícita o implícitamente, para determinar cómo la operación compara y ordena las cadenas. La lista real de intercalaciones disponibles y sus esquemas de nomenclatura son específicos de la base de datos; consulte [la sección siguiente](#database-specific-information) para obtener vínculos a las páginas de documentación relevantes de varias bases de datos. Afortunadamente, la base de datos normalmente permite definir una intercalación predeterminada en el nivel de base de datos o de columna, y especificar explícitamente qué intercalación debe usarse para las operaciones específicas en una consulta.

## <a name="database-collation"></a>Intercalación de base de datos

En la mayoría de los sistemas de base de datos, se define una intercalación predeterminada en el nivel de base de datos; a menos que se invalide, esa intercalación se aplica implícitamente a todas las operaciones de texto que se producen dentro de esa base de datos. La intercalación de base de datos se establece normalmente en el momento de creación de la base de datos (mediante la `CREATE DATABASE` instrucción DDL) y, si no se especifica, el valor predeterminado es un valor de nivel de servidor determinado en el momento de la instalación. Por ejemplo, la intercalación de nivel de servidor predeterminada en SQL Server es `SQL_Latin1_General_CP1_CI_AS` , que es una intercalación que distingue entre mayúsculas y minúsculas y con distinción de acentos. Aunque los sistemas de base de datos normalmente permiten modificar la intercalación de una base de datos existente, hacerlo puede provocar complicaciones; se recomienda elegir una intercalación antes de la creación de la base de datos.

Al usar migraciones de EF Core para administrar el esquema de la base de datos, lo siguiente en el método del modelo `OnModelCreating` configura una base de datos SQL Server para que use una intercalación que distinga entre mayúsculas y minúsculas:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?range=40)]

## <a name="column-collation"></a>Intercalación de columnas

Las intercalaciones también se pueden definir en columnas de texto, invalidando el valor predeterminado de la base de datos. Esto puede ser útil si algunas columnas deben distinguir entre mayúsculas y minúsculas, mientras que el resto de la base de datos debe distinguir entre mayúsculas y minúsculas.

Al usar migraciones de EF Core para administrar el esquema de la base de datos, lo siguiente configura la columna para `Name` que la propiedad no distinga entre mayúsculas y minúsculas en una base de datos que, de otro modo, está configurada para distinguir entre mayúsculas y minúsculas:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=OnModelCreating&highlight=6)]

## <a name="explicit-collation-in-a-query"></a>Intercalación explícita en una consulta

En algunos casos, la misma columna debe consultarse utilizando distintas intercalaciones en consultas diferentes. Por ejemplo, una consulta puede necesitar realizar una comparación con distinción entre mayúsculas y minúsculas en una columna, mientras que otra puede necesitar realizar una comparación sin distinción entre mayúsculas y minúsculas en la misma columna. Esto puede realizarse especificando explícitamente una intercalación dentro de la propia consulta:

[!code-csharp[Main](../../../samples/core/Miscellaneous/Collations/Program.cs?name=SimpleQueryCollation)]

Esto genera una `COLLATE` cláusula en la consulta SQL, que aplica una intercalación que distingue entre mayúsculas y minúsculas, independientemente de la intercalación definida en el nivel de columna o de base de datos:

```sql
SELECT [c].[Id], [c].[Name]
FROM [Customers] AS [c]
WHERE [c].[Name] COLLATE SQL_Latin1_General_CP1_CS_AS = N'John'
```

### <a name="explicit-collations-and-indexes"></a>Intercalaciones e índices explícitos

Los índices son uno de los factores más importantes en el rendimiento de la base de datos: una consulta que se ejecuta eficazmente con un índice puede triturarse en una detención sin ese índice. Los índices heredan implícitamente la intercalación de su columna; Esto significa que todas las consultas de la columna son válidas automáticamente para usar índices definidos en esa columna, siempre que la consulta no especifique una intercalación diferente. Especificar una intercalación explícita en una consulta normalmente impedirá que la consulta use un índice definido en esa columna, ya que las intercalaciones dejarán de coincidir; por lo tanto, se recomienda tener cuidado al usar esta característica. Siempre es preferible definir la intercalación en el nivel de columna (o base de datos), lo que permite que todas las consultas utilicen implícitamente esa intercalación y se beneficien de cualquier índice.

Tenga en cuenta que algunas bases de datos permiten definir la intercalación al crear un índice (por ejemplo, PostgreSQL, SQLite). Esto permite definir varios índices en la misma columna, con lo que se aceleran las operaciones con diferentes intercalaciones (por ejemplo, comparaciones que distinguen mayúsculas de minúsculas y no distinguen mayúsculas de minúsculas). Consulte la documentación del proveedor de bases de datos para obtener más detalles.

> [!WARNING]
> Inspeccione siempre los planes de consulta de las consultas y asegúrese de que se usan los índices adecuados en las consultas críticas para el rendimiento que se ejecutan en grandes cantidades de datos. Reemplazar la distinción de mayúsculas y minúsculas en una consulta mediante `EF.Functions.Collate` (o llamando a `string.ToLower` ) puede tener un impacto muy significativo en el rendimiento de la aplicación.

## <a name="translation-of-built-in-net-string-operations"></a>Traducción de operaciones de cadena integradas de .NET

En .NET, la igualdad de cadena distingue entre mayúsculas y minúsculas de forma predeterminada: `s1 == s2` realiza una comparación ordinal que requiere que las cadenas sean idénticas. Dado que la intercalación predeterminada de las bases de datos varía y porque es deseable para la igualdad simple usar índices, EF Core no intenta traducir la igualdad simple a una operación que distingue entre mayúsculas y minúsculas: la igualdad de C# se traduce directamente a la igualdad de SQL, que puede o no distinguir mayúsculas de minúsculas, según la base de datos específica en uso y su configuración de

Además, .NET proporciona sobrecargas [`string.Equals`](/dotnet/api/system.string.equals#System_String_Equals_System_String_System_StringComparison_) para aceptar una [`StringComparison`](/dotnet/api/system.stringcomparison) enumeración, que permite especificar la distinción de mayúsculas y minúsculas y la referencia cultural de la comparación. Por diseño, EF Core se abstendrán de traducir estas sobrecargas a SQL y se producirá una excepción al intentar usarlas. En un caso, EF Core no sabe qué intercalación que distingue entre mayúsculas y minúsculas o que no distingue mayúsculas de minúsculas. Lo que es más importante, al aplicar una intercalación en la mayoría de los casos, se evita el uso del índice, lo que afecta significativamente al rendimiento de una construcción .NET muy básica y de uso frecuente. Para forzar que una consulta use la comparación con distinción de mayúsculas y minúsculas o sin distinción de mayúsculas y minúsculas, especifique una intercalación explícitamente mediante `EF.Functions.Collate` como se [detailó anteriormente](#explicit-collations-and-indexes).

## <a name="database-specific-information"></a>Información específica de la base de datos

* [SQL Server documentación sobre las intercalaciones](/sql/relational-databases/collations/collation-and-unicode-support).
* [Documentación de Microsoft. Data. SQLite sobre las intercalaciones](/dotnet/standard/data/sqlite/collation).
* [Documentación de PostgreSQL sobre las intercalaciones](https://www.postgresql.org/docs/current/collation.html).
* [Documentación de MySQL sobre intercalaciones](https://dev.mysql.com/doc/refman/en/charset-general.html).
