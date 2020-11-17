---
title: 'Consultas de seguimiento frente a consultas de no seguimiento: EF Core'
description: Información sobre las consultas de seguimiento y no seguimiento en Entity Framework Core
author: smitpatel
ms.date: 11/09/2020
uid: core/querying/tracking
ms.openlocfilehash: b4c059f9a9b726697009589271e007bd1d2afd56
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430448"
---
# <a name="tracking-vs-no-tracking-queries"></a>Consultas de seguimiento frente a consultas de no seguimiento

El comportamiento de seguimiento controla si Entity Framework Core mantendrá información sobre una instancia de entidad en su herramienta de seguimiento de cambios. Si se hace seguimiento de una entidad, cualquier cambio detectado en ella persistirá hasta la base de datos durante `SaveChanges()`. EF Core también corregirá las propiedades de navegación entre las entidades de un resultado de consulta de seguimiento y las entidades que se encuentran en la herramienta de seguimiento de cambios.

> [!NOTE]
> No se realiza el seguimiento de los [tipos de entidad sin clave](xref:core/modeling/keyless-entity-types). Siempre que en este artículo se mencionen los tipos de entidad, se refiere a aquellos con una clave definida.

> [!TIP]  
> Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/Tracking) de este artículo en GitHub.

## <a name="tracking-queries"></a>Consultas de seguimiento

De manera predeterminada, las consultas que devuelven tipos de entidad son consultas de seguimiento. Esto significa que puede hacer cambios en esas instancias de entidad y que esos cambios se conservan mediante `SaveChanges()`. En el ejemplo siguiente, se detectará el cambio en la clasificación de los blogs y persistirá hasta la base de datos durante `SaveChanges()`.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#Tracking)]

Cuando los resultados se devuelven en una consulta de seguimiento, EF Core comprobará si la entidad ya está en el contexto. Si EF Core encuentra una entidad existente, se devuelve la misma instancia. EF Core no sobrescribirá los valores actuales y originales de las propiedades de la entidad en la entrada con los valores de la base de datos. Si no se encuentra la entidad en el contexto, EF Core creará una nueva instancia de la entidad y la asociará al contexto. Los resultados de la consulta no contienen ninguna entidad, que se agrega al contexto pero aún no se guarda en la base de datos.

## <a name="no-tracking-queries"></a>Consultas de no seguimiento

Las consultas de no seguimiento son útiles cuando los resultados se usan en un escenario de solo lectura. Su ejecución es más rápida porque no es necesario configurar la información de seguimiento de cambios. Si no necesita actualizar las entidades recuperadas de la base de datos, se debe usar una consulta de no seguimiento. Puede cambiar una consulta individual para que sea una consulta de no seguimiento. Una consulta de no seguimiento también proporcionará los resultados en función de lo que haya en la base de datos, sin tener en cuenta los cambios locales ni las entidades agregadas.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTracking)]

También puede cambiar el comportamiento de seguimiento predeterminado en el nivel de instancia de contexto:

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ContextDefaultTrackingBehavior)]

## <a name="identity-resolution"></a>Resolución de identidad

Dado que una consulta de seguimiento usa la herramienta de seguimiento de cambios, EF Core realizará la resolución de identidades en una consulta de este tipo. Al materializar una entidad, EF Core devolverá la misma instancia de entidad de la herramienta de seguimiento de cambios si ya está en proceso de seguimiento. Si el resultado contiene la misma entidad varias veces, se devuelve la misma instancia con cada repetición. Las consultas de no seguimiento no usan la herramienta de seguimiento de cambios y no realizan la resolución de identidades. Así que, se devuelve una nueva instancia de la entidad incluso cuando la misma entidad está contenida en el resultado varias veces. Este comportamiento era diferente en las versiones anteriores a EF Core 3.0; consulte las [versiones anteriores](#previous-versions).

A partir de EF Core 5.0, puede combinar los dos comportamientos anteriores en la misma consulta. Es decir, puede tener una consulta de no seguimiento, que llevará a cabo la resolución de identidades en los resultados. Al igual que el operador consultable `AsNoTracking()`, hemos agregado otro operador `AsNoTrackingWithIdentityResolution()`. También hay una entrada asociada agregada en la enumeración <xref:Microsoft.EntityFrameworkCore.QueryTrackingBehavior>. Cuando se configura la consulta para usar la resolución de identidad sin seguimiento, se usa un seguimiento de cambios independiente en segundo plano al generar los resultados de la consulta, por lo que cada instancia se materializa solo una vez. Dado que esta herramienta de seguimiento de cambios es diferente de la que se encuentra en el contexto, el contexto no realiza el seguimiento de los resultados. Una vez que se completa la enumeración de la consulta, la herramienta de seguimiento de cambios queda fuera de ámbito y se recopilan los elementos no utilizados según sea necesario.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#NoTrackingWithIdentityResolution)]

## <a name="tracking-and-custom-projections"></a>Seguimiento y proyecciones personalizadas

Incluso si el tipo de resultado de la consulta no es un tipo de entidad, EF Core seguirá realizando el seguimiento de los tipos de entidad contenidos en el resultado de forma predeterminada. En la consulta siguiente, que devuelve un tipo anónimo, se hará seguimiento de las instancias de `Blog` en el conjunto de resultados.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection1)]

Si el conjunto de resultados contiene tipos de entidad que proceden de la composición LINQ, EF Core realizará un seguimiento de ellos.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection2)]

Si el conjunto de resultados no contiene ningún tipo de entidad, no se realiza ningún seguimiento. En la consulta siguiente, se devuelve un tipo anónimo con algunos de los valores de la entidad (pero sin instancias del tipo de entidad real). No hay entidades con seguimiento que procedan de la consulta.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection3)]

 EF Core admite la evaluación del cliente en la proyección de nivel superior. Si EF Core materializa una instancia de entidad para la evaluación del cliente, se realizará un seguimiento de esta. Aquí, como se pasan entidades de `blog` al método cliente `StandardizeURL`, EF Core también realizará un seguimiento de las instancias del blog.

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientProjection)]

[!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientMethod)]

EF Core no realiza un seguimiento de las instancias de entidad sin clave contenidas en el resultado. Sin embargo, sí lo hace de todas las demás instancias de tipos de entidad con clave según las reglas anteriores.

Algunas de las reglas anteriores funcionaban de forma diferente antes de EF Core 3.0. Para más información, consulte las [versiones anteriores](#previous-versions).

## <a name="previous-versions"></a>Versiones anteriores

Antes de la versión 3.0, EF Core presentaba algunas diferencias en el modo en que se realizaba el seguimiento. Las diferencias destacables son las siguientes:

- Como se explica en la página [Evaluación de cliente frente a servidor](xref:core/querying/client-eval), EF Core admitía la evaluación de clientes admitidos en cualquier parte de la consulta anterior antes de la versión 3.0. La evaluación de clientes provocaba la materialización de entidades, las cuales no formaban parte del resultado. Por lo tanto, EF Core analizaba el resultado para detectar de qué realizar el seguimiento. Este diseño tenía algunas diferencias, como se indica a continuación:
  - No se realizaba el seguimiento de la evaluación de clientes en la proyección, lo que provocaba la materialización pero no se devolvía la instancia de la entidad materializada. En el ejemplo siguiente no se realizaba un seguimiento de entidades `blog`.
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#ClientProjection)]

  - En algunos casos, EF Core no realizaba un seguimiento de los objetos que procedían de la composición LINQ. En el ejemplo siguiente no se realizaba un seguimiento de `Post`.
    [!code-csharp[Main](../../../samples/core/Querying/Tracking/Program.cs#CustomProjection2)]

- Siempre que los resultados de consulta contenían tipos de entidad sin clave, significaba que no se hacía un seguimiento de la consulta completa. Esto quiere decir que tampoco se realizaba un seguimiento de los tipos de entidad con claves que estaban en el resultado.
- EF Coreó realizaba la resolución de identidades en consultas de no seguimiento. Se usaban referencias débiles para mantener el seguimiento de entidades que ya se habían devuelto. Por lo tanto, si un conjunto de resultados contenía la misma entidad varias veces, obtenía la misma instancia para cada caso. Sin embargo, si un resultado anterior con la misma identidad se salía del ámbito y generaba un elemento no utilizado, EF Core devolvía una nueva instancia.
