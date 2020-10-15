---
title: 'Eliminación en cascada: EF Core'
description: Configuración de los comportamientos de eliminación para entidades relacionadas al eliminar una entidad de seguridad
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/cascade-delete
ms.openlocfilehash: 037b018c669da76a70f134e3991ad22b36917920
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063613"
---
# <a name="cascade-delete"></a>Eliminación en cascada

En la terminología de las bases de datos, la eliminación en cascada se usa habitualmente para describir una característica que permite eliminar una fila para desencadenar de manera automática la eliminación de las filas relacionadas. Un concepto estrechamente relacionado que también abarcan los comportamientos de eliminación de EF Core es la eliminación automática de una entidad secundaria cuando se interrumpe su relación con una entidad primaria. Esto normalmente se conoce como la "eliminación de entidades huérfanas".

EF Core implementa varios comportamientos de eliminación distintos y permite configurar estos comportamientos de las relaciones individuales. EF Core también implementa convenciones que configuran automáticamente útiles comportamientos de eliminación predeterminados para cada relación en función de la [obligatoriedad de la relación](xref:core/modeling/relationships#required-and-optional-relationships).

## <a name="delete-behaviors"></a>Comportamientos de eliminación

Los comportamientos de eliminación se define en el tipo de enumerador *DeleteBehavior* y se pueden pasar a la API fluida *OnDelete* para controlar si la eliminación de una entidad principal o primaria o la interrupción de la relación con entidades dependientes o secundarias debería tener un efecto secundario en estas últimas.

Hay tres acciones que EF puede llevar a cabo cuando se elimina una entidad principal o primaria o cuando se interrumpe la relación con una entidad secundaria:

* Se puede eliminar la entidad secundaria o dependiente.
* Los valores de clave externa de la entidad secundaria se pueden establecer en NULL.
* La entidad secundaria se mantiene sin cambios.

> [!NOTE]
> El comportamiento de eliminación configurado en el modelo EF Core solo se aplica cuando la entidad principal se elimina mediante EF Core y las entidades dependientes se cargan en la memoria (es decir, en el caso de las entidades dependientes con seguimiento). Es necesario configurar un comportamiento en cascada correspondiente en la base de datos para garantizar que la acción necesaria se aplique a los datos a los que el contexto no hace seguimiento. Si usa EF Core para crear la base de datos, este comportamiento en cascada se configurará automáticamente.

La segunda acción mencionada, establecer un valor de clave externa en NULL, no es válida si la clave externa no admite un valor NULL. (Una clave externa que no admite un valor NULL equivale a una relación obligatoria). En estos casos, EF Core hace seguimiento de que la propiedad de la clave externa se haya marcado como NULL hasta que se llama a SaveChanges, momento en que se genera una excepción porque el cambio no puede durar hasta la base de datos. Esto es similar a obtener una infracción de restricción de la base de datos.

Existen cuatro comportamientos de eliminación, los que se indican en las tablas siguientes.

### <a name="optional-relationships"></a>Relaciones opcionales

En el caso de las relaciones opcionales (clave externa que admite un valor NULL) _es_ posible guardar un valor de clave externa NULL, lo que tiene como resultado los efectos siguientes:

| Nombre del comportamiento               | Efecto en la entidad dependiente o secundaria en la memoria    | Efecto en la entidad dependiente o secundaria en la base de datos  |
|:----------------------------|:---------------------------------------|:---------------------------------------|
| **Cascade**                 | Las entidades se eliminan                   | Las entidades se eliminan                   |
| **ClientSetNull** (valor predeterminado) | Las propiedades de clave externa se establecen en NULL | None                                   |
| **SetNull**                 | Las propiedades de clave externa se establecen en NULL | Las propiedades de clave externa se establecen en NULL |
| **Restrict**                | None                                   | None                                   |

### <a name="required-relationships"></a>Relaciones obligatorias

En el caso de las relaciones obligatorias (clave externa que no admite un valor NULL) _no_ es posible guardar un valor de clave externa NULL, lo que tiene como resultado los efectos siguientes:

| Nombre del comportamiento         | Efecto en la entidad dependiente o secundaria en la memoria | Efecto en la entidad dependiente o secundaria en la base de datos |
|:----------------------|:------------------------------------|:--------------------------------------|
| **Cascade** (valor predeterminado) | Las entidades se eliminan                | Las entidades se eliminan                  |
| **ClientSetNull**     | SaveChanges genera una excepción                  | None                                  |
| **SetNull**           | SaveChanges genera una excepción                  | SaveChanges genera una excepción                    |
| **Restrict**          | None                                | None                                  |

En las tablas anteriores, *None* puede dar lugar a una infracción de restricción. Por ejemplo, si se elimina una entidad principal o secundaria pero no se hace ninguna acción para cambiar la clave externa de una entidad dependiente o secundaria, es probable que la base de datos genere una excepción en SaveChanges debido a una infracción de restricción externa.

En un alto nivel:

* Si tiene entidades que no pueden existir sin una entidad primaria y quiere que EF se encargue de eliminar automáticamente las entidades secundarias, use *Cascade*.
  * Habitualmente, las entidades que no pueden existir sin una entidad primaria usan las relaciones obligatorias, en las que el valor predeterminado es *Cascade*.
* Si tiene entidades que pueden tener o no una entidad primaria y quiere que EF se encargue de anular automáticamente la clave externa, use *ClientSetNull*.
  * Habitualmente, las entidades que pueden existir sin una entidad primaria usan las relaciones opcionales, en las que el valor predeterminado es *ClientSetNull*.
  * Si quiere que la base de datos también intente propagar los valores NULL a las claves externas secundarias incluso si no se cargó la entidad secundaria, use *SetNull*. Sin embargo, tenga en cuenta que la base de datos debe admitir esta opción y que configurar de este modo la base de datos puede dar lugar a otras restricciones, por lo que, en la práctica, a menudo esta opción no es factible. Es por este motivo que *SetNull* no es el valor predeterminado.
* Si no quiere que EF Core elimine una entidad o anule la clave externa automáticamente, use *Restrict*. Tenga en cuenta que esta acción requiere que el código mantenga las entidades secundarias y sus valores de clave externa sincronizados de manera manual. Si no es así, se generarán excepciones de restricción.

> [!NOTE]
> En EF Core, a diferencia de lo que ocurre en EF6, los efectos en cascada no se producen de inmediato, sino que solo cuando se llama a SaveChanges.

## <a name="entity-deletion-examples"></a>Ejemplos de eliminación de entidades

El código siguiente forma parte de un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) que se puede descargar y ejecutar. El ejemplo muestra qué pasa con cada comportamiento de eliminación, tanto en las relaciones opcionales como en las obligatorias, cuando se elimina una entidad primaria.

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteBehaviorVariations)]

Analicemos cada variación para comprender lo que sucede.

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>DeleteBehavior.Cascade con relación obligatoria u opcional

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
  DELETE FROM [Posts] WHERE [PostId] = 1
  DELETE FROM [Posts] WHERE [PostId] = 2
  DELETE FROM [Blogs] WHERE [BlogId] = 1

After SaveChanges:
  Blog '1' is in state Detached with 2 posts referenced.
    Post '1' is in state Detached with FK '1' and no reference to a blog.
    Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* El blog se marca como eliminado
* Inicialmente, las entradas no se modifican porque las cascadas no se producen hasta que se genera SaveChanges
* SaveChanges envía eliminaciones para las entidades dependientes o secundarias (entradas) y para la entidad principal o primaria (blog)
* Después de guardar, todas las entidades se desasocian porque se eliminaron de la base de datos

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>DeleteBehavior.ClientSetNull o DeleteBehavior.SetNull con relación obligatoria

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* El blog se marca como eliminado
* Inicialmente, las entradas no se modifican porque las cascadas no se producen hasta que se genera SaveChanges
* SaveChanges intenta establecer la clave externa de la entrada en NULL, pero se produce un error porque la clave externa no admite un valor NULL

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>DeleteBehavior.ClientSetNull o DeleteBehavior.SetNull con relación opcional

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2
  DELETE FROM [Blogs] WHERE [BlogId] = 1

After SaveChanges:
  Blog '1' is in state Detached with 2 posts referenced.
    Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
    Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* El blog se marca como eliminado
* Inicialmente, las entradas no se modifican porque las cascadas no se producen hasta que se genera SaveChanges
* SaveChanges intenta establecer la clave externa de las entidades dependientes o secundarias (entradas) en NULL antes de eliminar la entidad principal o primaria (blog)
* Después de guardar, se elimina la entidad principal o primaria (blog), pero todavía se hace seguimiento de las entidades dependientes o secundarias (entradas)
* Las entidades dependientes o secundarias (entradas) con seguimiento ahora tienen valores de clave externa NULL y se quitó la referencia a la entidad principal o primaria (blog) que se eliminó

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>DeleteBehavior.Restrict con relación obligatoria u opcional

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After deleting blog '1':
  Blog '1' is in state Deleted with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

Saving changes:
SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* El blog se marca como eliminado
* Inicialmente, las entradas no se modifican porque las cascadas no se producen hasta que se genera SaveChanges
* Como *Restrict* le indica a EF que no establezca automáticamente la clave externa en NULL, no se anula y SaveChanges genera una excepción sin guardar

## <a name="delete-orphans-examples"></a>Ejemplos de eliminación de entidades huérfanas

El código siguiente forma parte de un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/CascadeDelete/) que se puede descargar y ejecutar. El ejemplo muestra qué sucede en cada comportamiento de eliminación, tanto para las relaciones opcionales como para las obligatorias, cuando se interrumpe la relación entre una entidad primaria o principal y sus entidades secundarias o dependientes. En este ejemplo, la relación se interrumpe al quitar las entidades dependientes o secundarias (entradas) de la propiedad de navegación de la colección en la entidad principal o primaria (blog). Sin embargo, el comportamiento es el mismo si se anula la referencia de la entidad secundaria o dependiente respecto de la entidad principal o primaria.

[!code-csharp[Main](../../../samples/core/Saving/CascadeDelete/Sample.cs#DeleteOrphansVariations)]

Analicemos cada variación para comprender lo que sucede.

### <a name="deletebehaviorcascade-with-required-or-optional-relationship"></a>DeleteBehavior.Cascade con relación obligatoria u opcional

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK '1' and no reference to a blog.
    Post '2' is in state Modified with FK '1' and no reference to a blog.

Saving changes:
  DELETE FROM [Posts] WHERE [PostId] = 1
  DELETE FROM [Posts] WHERE [PostId] = 2

After SaveChanges:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Detached with FK '1' and no reference to a blog.
    Post '2' is in state Detached with FK '1' and no reference to a blog.
```

* Las entradas se marcan como modificadas porque la interrupción de la relación hizo que la clave externa se marcara como NULL
  * Si la clave externa no admite un valor NULL, el valor actual no se modificará incluso si se marca como NULL
* SaveChanges envía eliminaciones para las entidades dependientes o secundarias (entradas)
* Después de guardar, las entidades dependientes o secundarias (entradas) se desasocian porque se eliminaron de la base de datos

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-required-relationship"></a>DeleteBehavior.ClientSetNull o DeleteBehavior.SetNull con relación obligatoria

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK 'null' and no reference to a blog.
    Post '2' is in state Modified with FK 'null' and no reference to a blog.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1

SaveChanges threw DbUpdateException: Cannot insert the value NULL into column 'BlogId', table 'EFSaving.CascadeDelete.dbo.Posts'; column does not allow nulls. UPDATE fails. The statement has been terminated.
```

* Las entradas se marcan como modificadas porque la interrupción de la relación hizo que la clave externa se marcara como NULL
  * Si la clave externa no admite un valor NULL, el valor actual no se modificará incluso si se marca como NULL
* SaveChanges intenta establecer la clave externa de la entrada en NULL, pero se produce un error porque la clave externa no admite un valor NULL

### <a name="deletebehaviorclientsetnull-or-deletebehaviorsetnull-with-optional-relationship"></a>DeleteBehavior.ClientSetNull o DeleteBehavior.SetNull con relación opcional

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK 'null' and no reference to a blog.
    Post '2' is in state Modified with FK 'null' and no reference to a blog.

Saving changes:
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 1
  UPDATE [Posts] SET [BlogId] = NULL WHERE [PostId] = 2

After SaveChanges:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK 'null' and no reference to a blog.
    Post '2' is in state Unchanged with FK 'null' and no reference to a blog.
```

* Las entradas se marcan como modificadas porque la interrupción de la relación hizo que la clave externa se marcara como NULL
  * Si la clave externa no admite un valor NULL, el valor actual no se modificará incluso si se marca como NULL
* SaveChanges establece la clave externa de las entidades dependientes o secundarias (entradas) en NULL
* Después de guardar, las entidades dependientes o secundarias (entradas) ahora tienen valores NULL de clave externa y se quitó la referencia a la entidad principal o primaria (blog) que se eliminó

### <a name="deletebehaviorrestrict-with-required-or-optional-relationship"></a>DeleteBehavior.Restrict con relación obligatoria u opcional

```output
After loading entities:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Unchanged with FK '1' and reference to blog '1'.
    Post '2' is in state Unchanged with FK '1' and reference to blog '1'.

After making posts orphans:
  Blog '1' is in state Unchanged with 2 posts referenced.
    Post '1' is in state Modified with FK '1' and no reference to a blog.
    Post '2' is in state Modified with FK '1' and no reference to a blog.

Saving changes:
SaveChanges threw InvalidOperationException: The association between entity types 'Blog' and 'Post' has been severed but the foreign key for this relationship cannot be set to null. If the dependent entity should be deleted, then setup the relationship to use cascade deletes.
```

* Las entradas se marcan como modificadas porque la interrupción de la relación hizo que la clave externa se marcara como NULL
  * Si la clave externa no admite un valor NULL, el valor actual no se modificará incluso si se marca como NULL
* Como *Restrict* le indica a EF que no establezca automáticamente la clave externa en NULL, no se anula y SaveChanges genera una excepción sin guardar

## <a name="cascading-to-untracked-entities"></a>Aplicación en cascada a las entidades sin seguimiento

Cuando llama a *SaveChanges*, se aplicarán las reglas de la aplicación en cascada a cualquier entidad de la que el contexto hace el seguimiento. Esto es lo que ocurre en todos los ejemplos anteriores, que es la razón por la cual se generó código SQL para eliminar tanto la entidad principal o primaria (blog) como todas las entidades dependientes o secundarias (entradas):

```sql
DELETE FROM [Posts] WHERE [PostId] = 1
DELETE FROM [Posts] WHERE [PostId] = 2
DELETE FROM [Blogs] WHERE [BlogId] = 1
```

Si solo se carga la entidad principal (por ejemplo, cuando se hace una solicitud para un blog sin `Include(b => b.Posts)` para que también incluya las entradas), SaveChanges solo generará código SQL para eliminar la entidad principal o primaria:

```sql
DELETE FROM [Blogs] WHERE [BlogId] = 1
```

Las entidades dependientes o secundarias (entradas) solo se eliminarán si la base de datos tiene configurado un comportamiento en cascada correspondiente. Si usa EF para crear la base de datos, este comportamiento en cascada se configurará automáticamente.
