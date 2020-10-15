---
title: 'Entidades desconectadas: EF Core'
description: Trabajo con entidades desconectadas y sin seguimiento en varias instancias de contexto en Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: core/saving/disconnected-entities
ms.openlocfilehash: f21cc71ef11ee4ef37618a68d5a5219e8174bf8b
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063574"
---
# <a name="disconnected-entities"></a>Entidades desconectadas

Una DbContext realizará seguimiento automático de las entidades que se devuelven de la base de datos. De ese modo, los cambios hechos en estas entidades se detectarán cuando se llame a SaveChanges y la base de datos se actualizará según sea necesario. Consulte [Basic Save](xref:core/saving/basic) (Guardado básico) y [Related Data](xref:core/saving/related-data) (Datos relacionados) para información detallada.

Sin embargo, en algunas ocasiones las entidades se consultan mediante el uso de una instancia de contexto y luego se guardan con una instancia distinta. Esto suele ocurrir en escenarios "desconectados", como una aplicación web, en los que las entidades se consultan, se envían al cliente, se modifican, se envían de vuelta al servidor en una solicitud y, a continuación, se guardan. En este caso, la segunda instancia de contexto debe saber si las entidades son nuevas (y se deben insertar) o existentes (y se deben actualizar).

<!-- markdownlint-disable MD028 -->
> [!TIP]
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Saving/Disconnected/) de este artículo en GitHub.

> [!TIP]
> EF Core solo puede hacer seguimiento de una instancia de una entidad con un valor de clave principal determinado. La mejor manera de evitar que esto se convierta en un problema es usar un contexto de corta duración para cada unidad de trabajo de manera que el contexto empiece vacío, tenga entidades asociadas, guarde esas entidades y, luego, se elimine y descarte el contexto.
<!-- markdownlint-enable MD028 -->

## <a name="identifying-new-entities"></a>Identificación de unidades nuevas

### <a name="client-identifies-new-entities"></a>El cliente identifica las unidades nuevas

El caso más sencillo de abordar es cuando el cliente informa al servidor si la entidad es nueva o existente. Por ejemplo, a menudo la solicitud para insertar una entidad nueva es distinta de la solicitud para actualizar una entidad existente.

En el resto de esta sección se analizan los casos en los que resulta necesario determinar de otro modo si se debe realizar una inserción o una actualización.

### <a name="with-auto-generated-keys"></a>Con claves generadas automáticamente

El valor de una clave generada automáticamente a menudo se puede usar para determinar si una entidad se debe insertar o actualizar. Si no se estableció la clave (es decir, si todavía tiene el valor predeterminado de CLR de NULL, cero, etc.), la entidad debe ser nueva y se debe insertar. Por el contrario, si el valor de la clave sí se estableció, ya se debe haber guardado anteriormente y ahora se debe actualizar. En otras palabras, si la clave tiene un valor, es porque la entidad ya se consultó, se envió al cliente y ahora vuelve para que la actualicen.

Resulta sencillo comprobar si una clave no se estableció cuando se conoce el tipo de entidad:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewSimple)]

Sin embargo, EF también tiene una manera integrada de hacer esto con cualquier tipo de entidad y cualquier tipo de clave:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewGeneral)]

> [!TIP]  
> Las claves se establecen tan pronto como el contexto hace seguimiento de las entidades, incluso si la entidad tiene el estado Added (Agregada). Esto resulta útil cuando se recorre un grafo de entidades y se decide qué hacer con cada una de ellas, como cuándo usar TrackGraph API. El valor de la clave solo se debe usar como se indica aquí _antes_ de cualquier llamada para hacer seguimiento de la entidad.

### <a name="with-other-keys"></a>Con otras claves

Es necesario algún otro mecanismo para identificar las entidades nuevas cuando los valores de clave no se generan automáticamente. Aquí existen dos enfoques generales:

* Consulta para la entidad
* Paso de una marca desde el cliente

Para consulta la entidad, simplemente use el método Find:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#IsItNewQuery)]

Mostrar el código completo para pasar una marca desde un cliente va más allá del ámbito del presente documento. En una aplicación web, habitualmente significa hacer distintas solicitudes para acciones diferentes o pasar algún estado en la solicitud para luego extraerlo en el controlador.

## <a name="saving-single-entities"></a>Guardado de entidades únicas

Cuando se sabe si es necesario o no realizar una inserción o una actualización, las acciones de agregar o actualizar se pueden usar correctamente:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertAndUpdateSingleEntity)]

Sin embargo, si la entidad usa valores de clave generados automáticamente, el método Update se puede usar para ambos casos:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntity)]

Habitualmente, el método Update marca la entidad para actualización y no para inserción. Sin embargo, si la entidad tiene una clave generada automáticamente y no se estableció ningún valor de clave, la entidad se marca automáticamente para inserción.

Si la entidad no usa claves generadas automáticamente, la aplicación debe decidir si la entidad se debe inserta ro actualizar. Por ejemplo:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateSingleEntityWithFind)]

Estos son los pasos:

* Si Find devuelve un valor NULL, la base de datos todavía no contiene el blog con su identificador, por lo que llamamos a Add para marcarlo para inserción.
* Si Find devuelve una entidad es porque existe en la base de datos y ahora el contexto hace seguimiento de esa entidad existente.
  * Luego usamos SetValues para establecer los valores de todas las propiedades de esta entidad en los valores que provienen del cliente.
  * La llamada a SetValues marcará la entidad para actualizarla según sea necesario.

> [!TIP]  
> SetValues solo marcará como modificadas las propiedades que tengan valores distintos a los de la entidad con seguimiento. Esto significa que, cuando se envíe la actualización, solo se actualizarán las columnas que se hayan modificado realmente. (Si no se modificó nada, no se enviará ninguna actualización).

## <a name="working-with-graphs"></a>Trabajo con grafos

### <a name="identity-resolution"></a>Resolución de identidad

Como se indicó anteriormente, EF Core solo puede hacer seguimiento de una instancia de una entidad con un valor de clave principal determinado. Cuando se trabaja con grafos, idealmente el grafo se debe crear de manera tal que se mantenga esta invariable y el contexto se debe usar solo para una unidad de trabajo. Si el grafo contiene duplicados, será necesario procesarlo antes de enviarlo a EF para consolidar varias instancias en una sola. Es posible que esta acción no sea trivial cuando haya instancias con valores y relaciones en conflicto, por lo que la consolidación de los duplicados se debe hacer tan pronto como sea posible en la canalización de aplicación para evitar la resolución de conflictos.

### <a name="all-newall-existing-entities"></a>Todas las entidades nuevas o todas las entidades existentes

Un ejemplo de trabajar con grafos es insertar o actualizar un blog junto con su colección de entradas asociadas. Si las entidades del grafo se deben insertar o actualizar en su totalidad, el proceso es el mismo que se describió anteriormente para las entidades únicas. Por ejemplo, un grafo de blogs y entradas creado de esta manera:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#CreateBlogAndPosts)]

se puede insertar así:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertGraph)]

La llamada a Add marcará el blog y todas las entradas para su inserción.

Del mismo modo, si es necesario actualizar todas las entidades de un grafo, se puede usar Update:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#UpdateGraph)]

El blog y todas las entradas se marcarán para su actualización.

### <a name="mix-of-new-and-existing-entities"></a>Combinación de entidades nuevas y entidades existentes

Con las claves generadas automáticamente, Update se puede volver a usar tanto para inserciones como para actualizaciones, incluso si el grafo contiene una combinación de entidades que requiere inserción y las que se deben actualizar:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraph)]

Update marcará una entidad en el grafo, ya sea el blog o una entrada, para inserción si no tiene establecido un valor de clave, mientras que todas las demás entidades se marcarán para actualización.

Como antes, cuando no se usan claves generadas automáticamente, es posible usar una consulta y algún procesamiento:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertOrUpdateGraphWithFind)]

## <a name="handling-deletes"></a>Control de eliminaciones

Puede ser difícil controlar las eliminaciones porque, habitualmente, la ausencia de una entidad implica que se debe eliminar. Una manera de solucionar esto es usar las "eliminaciones temporales" en que la entidad se marca como eliminada en lugar de eliminarla realmente. Luego, las eliminaciones pasan a ser iguales a las actualizaciones. Las eliminaciones temporales se pueden implementar usando [filtros de consulta](xref:core/querying/filters).

En el caso de las eliminaciones reales, un patrón común es usar una extensión del modelo de consulta para realizar lo que esencialmente es una diferencia de grafo. Por ejemplo:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#InsertUpdateOrDeleteGraphWithFind)]

## <a name="trackgraph"></a>TrackGraph

De manera interna, Add, Attach y Update usan el recorrido de grafo con una determinación hecha para cada entidad a fin de saber si se debe marcar como Added (para inserción), Modified (para actualización), Unchanged (para no hacer nada) o Deleted (para eliminación). Este mecanismo se expone a través de TrackGraph API. Por ejemplo, supongamos que cuando el cliente envió de vuelta un grafo de entidades, estableció alguna marca en cada entidad para indicar cómo se debe controlar. Entonces se puede usar TrackGraph para procesar esta marca:

[!code-csharp[Main](../../../samples/core/Saving/Disconnected/Sample.cs#TrackGraph)]

Las marcas solo se muestran como parte de la entidad para simplificar el ejemplo. Habitualmente, las marcas serían parte de una DTO o alguno otro estado incluido en la solicitud.
