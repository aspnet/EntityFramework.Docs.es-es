---
title: 'Funciones de base de datos: EF Core'
description: Información sobre las funciones de base de datos compatibles con la traducción de consultas de EF Core
author: smitpatel
ms.date: 11/10/2020
uid: core/querying/database-functions
ms.openlocfilehash: 34ced2a602168f6ed84763c046ef581cb87026e8
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503787"
---
# <a name="database-functions"></a>Funciones de base de datos

Las funciones de base de datos son el equivalente a los [métodos de C#](/dotnet/csharp/programming-guide/classes-and-structs/methods) en las bases de datos. Se puede invocar una función de base de datos sin ningún parámetro o con varios, y esta calcula el resultado en función de los valores de los parámetros. La mayoría de las bases de datos, que utilizan SQL para realizar consultas, admiten las funciones de base de datos. Por lo tanto, la salida SQL generada por la traducción de consultas de EF Core también permite invocar funciones de base de datos. Los métodos de C# no tienen por qué traducirse estrictamente en funciones de base de datos en EF Core.

- Un método de C# puede no tener una función de base de datos equivalente.
  - El método <xref:System.String.IsNullOrEmpty%2A?displayProperty=nameWithType> se traduce en una comprobación de valores NULL y en una comparación con una cadena vacía en la base de datos, y no en una función.
  - El método <xref:System.String.Equals(System.String,System.StringComparison)?displayProperty=nameWithType> no tiene una base de datos equivalente, ya que la comparación de cadenas no puede representarse ni imitarse fácilmente en una base de datos.
- Una función de base de datos puede no tener un método de C# equivalente. El operador `??` en C#, que no tiene ningún método, se traduce en la función `COALESCE` en la base de datos.

## <a name="types-of-database-functions"></a>Tipos de funciones de base de datos

La generación de la salida SQL de EF Core admite un subconjunto de funciones que se pueden usar en las bases de datos. Esta limitación proviene de la capacidad de representar una consulta en LINQ para la función de base de datos determinada. Además, cada base de datos tiene una compatibilidad variable con las funciones de base de datos, por lo que EF Core proporciona un subconjunto común. Un proveedor de bases de datos es libre de ampliar la generación de salidas SQL de EF Core para admitir más patrones. A continuación, se indican los tipos de funciones de base de datos que EF Core admite e identifica de forma exclusiva. Estos términos también ayudan a comprender qué traducciones se integran con los proveedores de EF Core.

### <a name="built-in-vs-user-defined-functions"></a>Funciones integradas frente a las definidas por el usuario

Las funciones integradas incluyen las bases de datos predefinidas, pero las definidas por el usuario las define explícitamente el usuario en la base de datos. Cuando EF Core traduce las consultas para usar funciones de base de datos, usa funciones integradas para asegurarse de que la función siempre está disponible en la base de datos. Es necesario distinguir las funciones integradas en algunas bases de datos para generar las salidas SQL correctamente. Por ejemplo, SqlServer requiere que se invoquen todas las funciones definidas por el usuario con un nombre calificado con el esquema. Sin embargo, las funciones integradas en SqlServer no tienen un esquema. PostgreSQL define la función integrada en el esquema `public`, pero se puede invocar con nombres calificados con el esquema.

### <a name="aggregate-vs-scalar-vs-table-valued-functions"></a>Comparación entre las funciones de agregado, escalares y con valores de tabla

- Las funciones escalares adoptan valores escalares (como enteros o cadenas) como parámetros y devuelven un valor escalar como resultado. Las funciones escalares se pueden usar en cualquier parte de SQL donde se pueda pasar un valor escalar.
- Las funciones de agregado adoptan un flujo de valores escalares como parámetros y devuelven un valor escalar como resultado. Las funciones de agregado se aplican en todo el conjunto de resultados de consulta o en un grupo de valores generados al aplicar el operador `GROUP BY`.
- Las funciones con valores de tabla adoptan valores escalares como parámetros y devuelven un flujo de filas como resultado. Las funciones con valores de tabla se utilizan como un origen de tabla en la cláusula `FROM`.

### <a name="niladic-functions"></a>Funciones niládicas

Las funciones niládicas son funciones de base de datos especiales que no tienen ningún parámetro y deben invocarse sin paréntesis. Son similares al acceso a propiedades o campos en una instancia de C#. Las funciones niládicas se diferencian de las funciones sin parámetros, ya que las últimas requieren paréntesis vacíos. No hay ningún nombre especial para las funciones de base de datos que siempre requieran paréntesis. Otro subconjunto de funciones de base de datos basado en el número de parámetros son las funciones variádicas. Las funciones variádicas pueden adoptar un número variable de parámetros cuando se invocan.

## <a name="database-function-mappings-in-ef-core"></a>Asignaciones de funciones de base de datos en EF Core

EF Core admite tres formas diferentes de asignación entre las funciones de C# y las funciones de base de datos.

### <a name="built-in-function-mapping"></a>Asignación de funciones integradas

De forma predeterminada, los proveedores de EF Core proporcionan asignaciones para varias funciones integradas mediante tipos primitivos. Por ejemplo, <xref:System.String.ToLower?displayProperty=nameWithType> se traduce en `LOWER` en SqlServer. Esta funcionalidad permite a los usuarios escribir consultas en LINQ sin problemas. Normalmente, se ofrece una traducción en la base de datos que genera el mismo resultado que la función de C# proporciona en el lado cliente. A veces, para lograrlo, la traducción real puede ser algo más complicada que una función de base de datos. En algunos escenarios, también proporcionamos la traducción más adecuada en lugar de la semántica de C# coincidente. La misma característica también se usa para proporcionar traducciones comunes para algunos de los accesos de los miembros de C#. Por ejemplo, <xref:System.String.Length?displayProperty=nameWithType> se traduce en `LEN` en SqlServer. Además de los proveedores, los escritores de complementos también pueden agregar traducciones adicionales. Esta extensibilidad es útil cuando los complementos agregan compatibilidad con más tipos, como los primitivos, y desean traducir métodos a partir de ellos.

### <a name="effunctions-mapping"></a>Asignación de EF.Functions

Puesto que no todas las funciones de base de datos tienen funciones de C# equivalentes, los proveedores de EF Core tienen métodos de C# especiales para invocar determinadas funciones de base de datos. Estos métodos se definen como métodos de extensión mediante `EF.Functions` que se usarán en consultas LINQ. Estos métodos son específicos del proveedor, ya que están estrechamente relacionados con funciones de base de datos concretas. Por lo tanto, es probable que un método que funciona para un proveedor no funcione para ningún otro proveedor. Además, puesto que la intención de estos métodos es invocar una función de base de datos en la consulta traducida, intentar evaluarlos en el cliente produce una excepción.

### <a name="user-defined-function-mapping"></a>Asignación de funciones definidas por el usuario

Además de las asignaciones proporcionadas por los proveedores de EF Core, los usuarios también pueden definir asignaciones personalizadas. Una asignación definida por el usuario amplía la traducción de la consulta según las necesidades del usuario. Esta funcionalidad es útil cuando hay funciones definidas por el usuario en la base de datos, que el usuario desea invocar con su consulta LINQ.

## <a name="see-also"></a>Consulte también

- [Asignaciones de funciones integradas de SqlServer](xref:core/providers/sql-server/functions)
- [Asignaciones de funciones integradas de Sqlite](xref:core/providers/sqlite/functions)
- [Asignaciones de funciones integradas de Cosmos](xref:core/providers/cosmos/functions)
