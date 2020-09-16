---
title: 'Filtros de consulta global: EF Core'
description: Uso de filtros de consulta globales para filtrar los resultados con Entity Framework Core
author: anpete
ms.date: 11/03/2017
uid: core/querying/filters
ms.openlocfilehash: d5793760ea2e61111416284db8d5a8102dd51a41
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616395"
---
# <a name="global-query-filters"></a>Filtros de consulta global

> [!NOTE]
> Esta característica se incluyó por primera vez en EF Core 2.0.

Los filtros de consulta global son predicados de consulta LINQ que se aplican a los tipos de entidad en el modelo de metadatos (normalmente en *OnModelCreating*). Un predicado de consulta es una expresión booleana que se pasa normalmente al operador de consulta *Where* de LINQ.  EF Core aplica estos filtros automáticamente a cualquier consulta LINQ que implique esos tipos de entidad.  EF Core también los aplica a los tipos de entidad, a los que se hace referencia de forma indirecta mediante el uso de la propiedad de navegación o Include. Algunas aplicaciones comunes de esta característica son:

* **Eliminación temporal**: un tipo de entidad define una propiedad *IsDeleted*.
* **Servicios multiinquilino**: un tipo de entidad define una propiedad *TenantId*.

## <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo usar los filtros de consulta global para implementar los comportamientos de consulta de multiinquilino y eliminación temporal en un modelo sencillo de creación de blogs.

> [!TIP]
> Puede ver un [ejemplo de servicios multiinquilino](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFilters) y [ejemplos con navegaciones](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/QueryFiltersNavigations) en GitHub.

En primer lugar, defina las entidades:

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Entities)]

Tenga en cuenta la declaración de un campo _tenantId_ en la entidad _Blog_. Este campo se usará para asociar cada instancia de blog a un inquilino específico. También hay definida una propiedad _IsDeleted_ en el tipo de entidad _Post_. Se usa para llevar un seguimiento de si una instancia _Post_ se ha eliminado de manera temporal. Es decir, la instancia se marca como eliminada sin quitar físicamente los datos subyacentes.

A continuación, configure los filtros de consulta en _OnModelCreating_ con la API `HasQueryFilter`.

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#Configuration)]

Las expresiones de predicado pasadas a las llamadas de _HasQueryFilter_ ahora se aplicarán automáticamente a cualquier consulta LINQ para esos tipos.

> [!TIP]
> Tenga en cuenta el uso de un campo en el nivel de instancia de DbContext: `_tenantId` se usa para establecer el inquilino actual. Los filtros de nivel de modelo usan el valor de la instancia de contexto correcta (es decir, la instancia que está ejecutando la consulta).

> [!NOTE]
> Actualmente no es posible definir varios filtros de consulta en la misma entidad. Solo se aplicará el último. Sin embargo, puede definir un único filtro con varias condiciones mediante el operador lógico _AND_ ([`&&` en C#](/dotnet/csharp/language-reference/operators/boolean-logical-operators#conditional-logical-and-operator-)).

## <a name="use-of-navigations"></a>Uso de navegaciones

También se pueden usar las navegaciones en la definición de filtros de consulta global. El uso de navegaciones en el filtro de consulta hará que los filtros de consulta se apliquen de forma recursiva. Cuando EF Core expande las navegaciones que se usan en los filtros de consulta, también aplica los filtros de consulta definidos en las entidades a las que se hace referencia.

> [!NOTE]
> Actualmente EF Core no detecta ciclos en las definiciones de filtros de consulta global, por lo que debe tener cuidado al definirlas. Si se especifican incorrectamente, los ciclos pueden provocar bucles infinitos durante la traslación de consultas.

## <a name="accessing-entity-with-query-filter-using-required-navigation"></a>Acceso a una entidad con filtro de consultas mediante la navegación necesaria

> [!CAUTION]
> El uso de la navegación necesaria para acceder a la entidad que tiene definido un filtro de consulta global puede producir resultados inesperados.

La navegación necesaria espera que la entidad relacionada esté siempre presente. Si el filtro de consulta filtra la entidad relacionada necesaria, es posible que la entidad primaria no esté en el resultado. Por lo tanto, puede que en el resultado obtenga menos elementos de los esperados.

Para ilustrar el problema, podemos usar las entidades `Blog` y `Post` especificadas anteriormente y el siguiente método _OnModelCreating_:

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

El modelo se puede inicializar con los siguientes datos:

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#SeedData)]

Se puede observar el problema al ejecutar dos consultas:

[!code-csharp[Main](../../../samples/core/QueryFiltersNavigations/Program.cs#Queries)]

Con la configuración anterior, la primera consulta devuelve las 6 `Post`; sin embargo, la segunda consulta solo devuelve 3. Esta falta de coincidencia se produce porque el método _Include_ de la segunda consulta carga las entidades `Blog` relacionadas. Dado que se requiere la navegación entre `Blog` y `Post`, EF Core utiliza `INNER JOIN` al construir la consulta:

```SQL
SELECT [p].[PostId], [p].[BlogId], [p].[Content], [p].[IsDeleted], [p].[Title], [t].[BlogId], [t].[Name], [t].[Url]
FROM [Post] AS [p]
INNER JOIN (
    SELECT [b].[BlogId], [b].[Name], [b].[Url]
    FROM [Blogs] AS [b]
    WHERE CHARINDEX(N'fish', [b].[Url]) > 0
) AS [t] ON [p].[BlogId] = [t].[BlogId]
```

El uso de `INNER JOIN` filtra todos los valores `Post` cuyos `Blog` relacionados se han quitado mediante un filtro de consulta global.

Se puede solucionar mediante el uso de la navegación opcional, en lugar de la necesaria.
De este modo, la primera consulta se mantiene igual que antes, pero la segunda consulta generará `LEFT JOIN` y devolverá 6 resultados.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired(false);
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
}
```

Un enfoque alternativo consiste en especificar filtros coherentes en las entidades `Blog` y `Post`.
De este modo, los filtros coincidentes se aplican tanto a `Blog` como a `Post`. Las `Post` que podrían acabar en un estado inesperado se quitan y ambas consultas devuelven 3 resultados.

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Blog>().HasMany(b => b.Posts).WithOne(p => p.Blog).IsRequired();
    modelBuilder.Entity<Blog>().HasQueryFilter(b => b.Url.Contains("fish"));
    modelBuilder.Entity<Post>().HasQueryFilter(p => p.Blog.Url.Contains("fish"));
}
```

## <a name="disabling-filters"></a>Deshabilitación de filtros

Los filtros se pueden deshabilitar para consultas LINQ individuales mediante el operador `IgnoreQueryFilters()`.

[!code-csharp[Main](../../../samples/core/QueryFilters/Program.cs#IgnoreFilters)]

## <a name="limitations"></a>Limitaciones

Los filtros de consulta global tienen las limitaciones siguientes:

* Solo se pueden definir filtros para el tipo de entidad raíz de una jerarquía de herencia.
