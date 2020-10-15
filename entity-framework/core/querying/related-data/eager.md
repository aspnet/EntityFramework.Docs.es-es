---
title: 'Carga diligente de datos relacionados: EF Core'
description: Carga diligente de datos relacionados con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: 97ec45a0f8bfecce4d4a59e5d1c36c0268d96052
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062581"
---
# <a name="eager-loading-of-related-data"></a>Carga diligente de datos relacionados

## <a name="eager-loading"></a>Carga diligente

Puede usar el método `Include` para especificar los datos relacionados que se incluirán en los resultados de la consulta. En el ejemplo siguiente, las entradas relacionadas rellenarán la propiedad `Posts` de los blogs que se devuelvan en los resultados.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core corregirá automáticamente las propiedades de navegación para todas las entidades que se cargaron previamente en la instancia del contexto. Por tanto, incluso si los datos de una propiedad de navegación no se incluyen explícitamente, es posible que la propiedad se siga rellenando si algunas o todas las entidades relacionadas se cargaron previamente.

Puede incluir los datos relacionados de varias relaciones en una sola consulta.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

## <a name="including-multiple-levels"></a>Inclusión de varios niveles

Puede explorar en profundidad las relaciones para incluir varios niveles de datos relacionados con el método `ThenInclude`. En el ejemplo siguiente se cargan todos los blogs, las entradas relacionadas y el creador de cada entrada.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

Puede encadenar varias llamadas en `ThenInclude` para continuar incluyendo más niveles de datos relacionados.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

Puede combinar todas las llamadas para incluir datos relacionados provenientes de varios niveles y varias raíces en la misma consulta.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

Es posible que quiera incluir varias entidades relacionadas para una de las entidades que se está incluyendo. Por ejemplo, cuando consulte `Blogs`, incluye `Posts` y luego quiere incluir tanto `Author` como `Tags` de las `Posts`. Para ello, debe especificar cada inicio de ruta de acceso de inclusión en la raíz. Por ejemplo, `Blog -> Posts -> Author` y `Blog -> Posts -> Tags`. Esto no significa que vaya a obtener combinaciones redundantes; en la mayoría de los casos, EF mezclará las combinaciones al generar código SQL.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="single-and-split-queries"></a>Consultas únicas y divididas

### <a name="single-queries"></a>Consultas únicas

En las bases de datos relacionales, todas las entidades relacionadas se cargan de forma predeterminada mediante la introducción de instrucciones JOIN:

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url], [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title]
FROM [Blogs] AS [b]
LEFT JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId], [p].[PostId]
```

Si un blog típico tiene varias entradas relacionadas, las filas de estas entradas duplicarán la información del blog, lo que conduce al problema que se conoce como "explosión cartesiana". A medida que se cargan más relaciones uno a varios, la cantidad de datos duplicados puede crecer y afectar negativamente al rendimiento de la aplicación. De forma predeterminada, EF Core emite una advertencia si detecta consultas que cargan métodos Include de la colección que puedan provocar incidencias de rendimiento.

### <a name="split-queries"></a>Consultas divididas

> [!NOTE]
> Esta característica se incluye por primera vez en EF Core 5.0.

EF le permite especificar que una consulta LINQ determinada se debe *dividir* en varias consultas SQL. En lugar de instrucciones JOIN, las consultas divididas realizan una consulta SQL adicional por cada navegación de uno a varios incluida:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSplitQuery&highlight=5)]

Generará la consulta SQL siguiente:

```sql
SELECT [b].[BlogId], [b].[OwnerId], [b].[Rating], [b].[Url]
FROM [Blogs] AS [b]
ORDER BY [b].[BlogId]

SELECT [p].[PostId], [p].[AuthorId], [p].[BlogId], [p].[Content], [p].[Rating], [p].[Title], [b].[BlogId]
FROM [Blogs] AS [b]
INNER JOIN [Post] AS [p] ON [b].[BlogId] = [p].[BlogId]
ORDER BY [b].[BlogId]
```

> [!NOTE]
> Las entidades relacionadas uno a uno se cargan siempre a través de instrucciones JOIN en la misma consulta, ya que el rendimiento no se ve afectado.

### <a name="enabling-split-queries-globally"></a>Habilitación de consultas divididas globalmente

También puede configurar consultas divididas como el valor predeterminado para el contexto de la aplicación:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/SplitQueriesBloggingContext.cs?name=QuerySplittingBehaviorSplitQuery&highlight=6)]

Cuando las consultas divididas están configuradas como valor predeterminado, todavía es posible configurar consultas específicas para que se ejecuten como consultas únicas:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs?name=AsSingleQuery&highlight=5)]

Si el modo de división de consultas no se especifica de forma explícita, ni globalmente ni en la consulta, y EF Core detecta que una sola consulta carga varios métodos Include de la colección, se emite una advertencia para llamar la atención sobre las posibles incidencias de rendimiento resultantes. Establecer el modo de consulta en SingleQuery hará que no se genere la advertencia.

### <a name="characteristics-of-split-queries"></a>Características de las consultas divididas

Si bien las consultas divididas evitan las incidencias de rendimiento asociadas a las instrucciones JOIN y la explosión cartesiana, también existen algunas desventajas:

* Aunque la mayoría de las bases de datos garantizan la coherencia de los datos en las consultas únicas, no sucede lo mismo en el caso de varias consultas. Si la base de datos se actualiza de forma simultánea al ejecutar las consultas, es posible que los datos resultantes no sean coherentes. Esto se puede mitigar mediante el encapsulado de las consultas en una transacción serializable o de instantáneas, aunque esto puede generar incidencias propias de rendimiento. Para obtener más información, vea la documentación de la base de datos.
* Cada consulta implica actualmente un ciclo adicional de ida y vuelta de red a la base de datos. Varios ciclos de ida y vuelta de red pueden degradar el rendimiento, sobre todo si la latencia en la base de datos es alta (por ejemplo, servicios en la nube).
* Aunque algunas bases de datos permiten el consumo de los resultados de varias consultas al mismo tiempo (SQL Server con MARS, SQLite), la mayoría de ellas solo permiten sola consulta activa en un momento dado. Por lo tanto, todos los resultados de las consultas anteriores deben almacenarse en búfer en la memoria de la aplicación antes de ejecutar consultas posteriores, lo que llevará a un aumento en los requisitos de memoria.

Por desgracia, no hay una estrategia para cargar entidades relacionadas que se ajuste a todos los escenarios. Tenga en cuenta las ventajas y desventajas de las consultas únicas y divididas, y seleccione la que mejor se ajuste a sus necesidades.

## <a name="filtered-include"></a>Inclusión filtrada

> [!NOTE]
> Esta característica se incluye por primera vez en EF Core 5.0.

Al aplicar Include para cargar datos relacionados, puede aplicar determinadas operaciones enumerables en la navegación de colección incluida, lo que permite filtrar y ordenar los resultados.

Las operaciones que se admiten son: `Where`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `Skip` y `Take`.

Dichas operaciones se deben aplicar en la navegación de colección en la expresión lambda que se pasa al método Include, como se muestra en el ejemplo siguiente:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#FilteredInclude)]

Cada navegación incluida solo permite un único conjunto de operaciones de filtro. En los casos en los que se aplican varias operaciones Include para una navegación de colección determinada (`blog.Posts` en los ejemplos siguientes), las operaciones de filtro solo se pueden especificar en una de ellas:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered1)]

En lugar de eso, se pueden aplicar operaciones idénticas para cada navegación que esté incluida varias veces:

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludesFiltered2)]

> [!CAUTION]
> En el caso de las consultas de seguimiento, los resultados de Inclusión filtrada pueden ser inesperados debido a la [corrección de la navegación](xref:core/querying/tracking). Todas las entidades pertinentes que se hayan consultado anteriormente y que se hayan almacenado en el seguimiento de cambios estarán presentes en los resultados de la consulta Include filtrada aunque no cumplan los requisitos del filtro. Valore la posibilidad de usar consultas `NoTracking` o de volver a crear el elemento DbContext al emplear Inclusión filtrada en esas situaciones.

Ejemplo:

```csharp
var orders = context.Orders.Where(o => o.Id > 1000).ToList();

// customer entities will have references to all orders where Id > 1000, rather than > 5000
var filtered = context.Customers.Include(c => c.Orders.Where(o => o.Id > 5000)).ToList();
```

## <a name="include-on-derived-types"></a>Inclusión en tipos derivados

Puede incluir datos relacionados provenientes de la navegación que se define solo en un tipo derivado con `Include` y `ThenInclude`.

Dado el modelo siguiente:

```csharp
public class SchoolContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<School> Schools { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<School>().HasMany(s => s.Students).WithOne(s => s.School);
    }
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}

public class Student : Person
{
    public School School { get; set; }
}

public class School
{
    public int Id { get; set; }
    public string Name { get; set; }

    public List<Student> Students { get; set; }
}
```

El contenido de la navegación `School` de todas las personas que son estudiantes se puede cargar de manera diligente mediante el uso de diversos patrones:

* con conversión

  ```csharp
  context.People.Include(person => ((Student)person).School).ToList()
  ```

* con el operador `as`

  ```csharp
  context.People.Include(person => (person as Student).School).ToList()
  ```

* con la sobrecarga de `Include` que toma el parámetro del tipo `string`

  ```csharp
  context.People.Include("School").ToList()
  ```
