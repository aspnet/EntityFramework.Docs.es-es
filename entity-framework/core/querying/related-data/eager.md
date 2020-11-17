---
title: 'Carga diligente de datos relacionados: EF Core'
description: Carga diligente de datos relacionados con Entity Framework Core
author: roji
ms.date: 9/8/2020
uid: core/querying/related-data/eager
ms.openlocfilehash: bd9c9045c1c2707d69ee4070bea59ad8066789f3
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430110"
---
# <a name="eager-loading-of-related-data"></a>Carga diligente de datos relacionados

## <a name="eager-loading"></a>Carga diligente

Puede usar el método `Include` para especificar los datos relacionados que se incluirán en los resultados de la consulta. En el ejemplo siguiente, las entradas relacionadas rellenarán la propiedad `Posts` de los blogs que se devuelvan en los resultados.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleInclude)]

> [!TIP]
> Entity Framework Core corregirá automáticamente las propiedades de navegación para todas las entidades que se cargaron previamente en la instancia del contexto. Por tanto, incluso si los datos de una propiedad de navegación no se incluyen explícitamente, es posible que la propiedad se siga rellenando si algunas o todas las entidades relacionadas se cargaron previamente.

Puede incluir los datos relacionados de varias relaciones en una sola consulta.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleIncludes)]

> [!CAUTION]
> La carga diligente de navegación de una colección en una sola consulta puede producir problemas de rendimiento. Para obtener más información, vea [Consultas únicas frente a consultas divididas](xref:core/querying/single-split-queries).

## <a name="including-multiple-levels"></a>Inclusión de varios niveles

Puede explorar en profundidad las relaciones para incluir varios niveles de datos relacionados con el método `ThenInclude`. En el ejemplo siguiente se cargan todos los blogs, las entradas relacionadas y el creador de cada entrada.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#SingleThenInclude)]

Puede encadenar varias llamadas en `ThenInclude` para continuar incluyendo más niveles de datos relacionados.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleThenIncludes)]

Puede combinar todas las llamadas para incluir datos relacionados provenientes de varios niveles y varias raíces en la misma consulta.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#IncludeTree)]

Es posible que quiera incluir varias entidades relacionadas para una de las entidades que se está incluyendo. Por ejemplo, cuando consulte `Blogs`, incluye `Posts` y luego quiere incluir tanto `Author` como `Tags` de las `Posts`. Para ello, debe especificar cada inicio de ruta de acceso de inclusión en la raíz. Por ejemplo, `Blog -> Posts -> Author` y `Blog -> Posts -> Tags`. Esto no significa que vaya a obtener combinaciones redundantes; en la mayoría de los casos, EF mezclará las combinaciones al generar código SQL.

[!code-csharp[Main](../../../../samples/core/Querying/RelatedData/Program.cs#MultipleLeafIncludes)]

## <a name="filtered-include"></a>Inclusión filtrada

> [!NOTE]
> Esta característica se incluye por primera vez en EF Core 5.0.

Al aplicar la inclusión para cargar datos relacionados, puede agregar determinadas operaciones enumerables en la navegación de colección incluida, lo que permite filtrar y ordenar los resultados.

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

> [!NOTE]
> En el caso de las consultas de seguimiento, se considera que se ha cargado la navegación en la que se aplicó la inclusión filtrada. Esto significa que EF Core no intentará volver a cargar sus valores mediante [la ](xref:core/querying/related-data/explicit)carga explícita[ ni la ](xref:core/querying/related-data/lazy)carga diferida, aunque aún falten algunos elementos.

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
