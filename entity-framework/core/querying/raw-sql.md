---
title: 'Consultas SQL sin formato: EF Core'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 70aae9b5-8743-4557-9c5d-239f688bf418
uid: core/querying/raw-sql
ms.openlocfilehash: 91592ea9f7c73f10446993282c1874c852000871
ms.sourcegitcommit: c9c3e00c2d445b784423469838adc071a946e7c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2019
ms.locfileid: "68306544"
---
# <a name="raw-sql-queries"></a>Consultas SQL sin formato

Entity Framework Core le permite descender hasta las consultas SQL sin formato cuando trabaja con una base de datos relacional. Esto puede resultar útil si la consulta que desea hacer no se puede expresar con LINQ o si usar una consulta LINQ hará que se envíen consultas de SQL ineficaces a la base de datos. Las consultas SQL sin formato pueden devolver tipos de entidad o, a partir de EF Core 2.1, [tipos de consulta](xref:core/modeling/query-types) que son parte del modelo.

> [!TIP]  
> Puede ver un [ejemplo](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying) de este artículo en GitHub.

## <a name="basic-raw-sql-queries"></a>Consultas SQL básicas sin formato

Puede usar el método de extensión *FromSql* para empezar una consulta LINQ basada en una consulta SQL sin formato.

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("SELECT * FROM dbo.Blogs")
    .ToList();
```

Las consultas SQL sin formato se pueden usar para ejecutar un procedimiento almacenado.

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs")
    .ToList();
```

## <a name="passing-parameters"></a>Pasar parámetros

Al igual que con cualquier API que acepta código SQL, es importante parametrizar cualquier entrada de usuario como protección contra un ataque por inyección de código SQL. Puede incluir marcadores de posición de parámetro en la cadena de la consulta y luego suministrar valores de parámetro como argumentos adicionales. Cualquier valor de parámetro que suministre se convertirá automáticamente en `DbParameter`.

En el ejemplo siguiente, se pasa un parámetro único a un procedimiento almacenado. Si bien esto se puede ver como la sintaxis `String.Format`, el valor suministrado se encapsula en un parámetro y el nombre del parámetro generado se inserta donde se especificó el marcador de posición `{0}`.

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser {0}", user)
    .ToList();
```

Se trata de la misma consulta, pero usando la sintaxis de interpolación de cadena, que se admite en EF Core 2.0 y versiones superiores:

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = "johndoe";

var blogs = context.Blogs
    .FromSql($"EXECUTE dbo.GetMostPopularBlogsForUser {user}")
    .ToList();
```

También puede construir un elemento DbParameter y suministrarlo como un valor de parámetro:

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogsForUser @user", user)
    .ToList();
```

Esto permite usar los parámetros con nombre en la cadena de consulta SQL, lo que resulta útil cuando un procedimiento almacenado tiene parámetros opcionales:

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var user = new SqlParameter("user", "johndoe");

var blogs = context.Blogs
    .FromSql("EXECUTE dbo.GetMostPopularBlogs @filterByUser=@user", user)
    .ToList();
```

## <a name="composing-with-linq"></a>Redacción con LINQ

Si la consulta SQL admite redacción en la base de datos, puede redactarla sobre la consulta SQL sin formato inicial mediante operadores LINQ. Las consultas SQL que se pueden redactar empiezan con la palabra clave `SELECT`.

En el ejemplo siguiente, se usa una consulta SQL sin formato que se selecciona de una función con valores de tabla (TVF) y luego se redacta en ella con LINQ para realizar filtrado y ordenación.

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Where(b => b.Rating > 3)
    .OrderByDescending(b => b.Rating)
    .ToList();
```

## <a name="change-tracking"></a>Seguimiento de cambios

Las consultas que usan `FromSql()` siguen las mismas reglas de seguimiento de cambios que las demás consultas LINQ en EF Core. Por ejemplo, si la consulta proyecta tipos de entidad, se realizará un seguimiento de los resultados de forma predeterminada.  

En el ejemplo siguiente se usa una consulta SQL sin formato que realiza una selección en una función con valores de tabla (TVF) y, a continuación, deshabilita el seguimiento de cambios con la llamada a .AsNoTracking():

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Query<SearchBlogsDto>()
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .AsNoTracking()
    .ToList();
```

## <a name="including-related-data"></a>Inclusión de datos relacionados

El método `Include()` puede usarse para incluir datos relacionados, igual que cualquier otra consulta LINQ:

<!-- [!code-csharp[Main](samples/core/Querying/Querying/RawSQL/Sample.cs)] -->
``` csharp
var searchTerm = ".NET";

var blogs = context.Blogs
    .FromSql($"SELECT * FROM dbo.SearchBlogs({searchTerm})")
    .Include(b => b.Posts)
    .ToList();
```

## <a name="limitations"></a>Limitaciones

Existen algunas limitaciones que debe considerar al usar las consultas SQL sin formato:

* La consulta SQL debe devolver datos para todas las propiedades del tipo de entidad o de consulta.

* Los nombres de las columnas del conjunto de resultados deben coincidir con los nombres de las columnas a los que se asignan las propiedades. Observe que esto es diferente de lo que ocurre en EF6, donde las consultas SQL omitían la asignación de propiedades y columnas, y los nombres de las columnas del conjunto de resultados tenían que coincidir con los nombres de las propiedades.

* La consulta SQL no puede incluir datos relacionados. Sin embargo, en muchos casos puede redactar sobre la consulta si usa el operador `Include` para devolver datos relacionados (consulte [Inclusión de datos relacionados](#including-related-data)).

* Las instrucciones `SELECT` que se pasan a este método por lo general admiten composición: Si EF Core debe evaluar operadores de consulta adicionales en el servidor (por ejemplo, para trasladar los operadores LINQ aplicados después de `FromSql`), el código SQL suministrado se considerará una subconsulta. Esto significa que el código SQL que se pasa no contendrá ningún carácter ni opción que no sea válido en una subconsulta, como:
  * un punto y coma final
  * En SQL Server, una sugerencia en el nivel de consulta final (por ejemplo, `OPTION (HASH JOIN)`)
  * En SQL Server, cláusula `ORDER BY` que no va acompañada por `OFFSET 0` O BIEN `TOP 100 PERCENT` en la cláusula `SELECT`

* Las instrucciones SQL que no son `SELECT` se reconocen de manera automáticamente como instrucciones que no admiten composición. Como consecuencia, los resultados completos de los procedimientos almacenados siempre se devuelven al cliente y cualquier operador LINQ aplicado después de `FromSql` se evalúa en la memoria.

> [!WARNING]  
> **Siempre use la parametrización para las consultas SQL sin formato:** Además de validar la entrada del usuario, siempre use la parametrización para cualquier valor usado en un comando o consulta SQL sin formato. las API que aceptan una cadena SQL sin formato como `FromSql` y `ExecuteSqlCommand` permiten que los valores se pasen de manera sencilla como parámetros. Las sobrecargas de `FromSql` y `ExecuteSqlCommand` que aceptan FormattableString también permiten el uso de la sintaxis de interpolación de cadenas de una manera que mejora la protección contra ataques por inyección de código SQL. 
> 
> Si usa la concatenación o interpolación de cadenas para generar de forma dinámica cualquier elemento de la cadena de consulta, o bien pasa la entrada del usuario a instrucciones o procedimientos almacenados que la puedan ejecutar como SQL dinámico, será responsable de validar todas las entradas para protegerlas contra ataques por inyección de código SQL.
