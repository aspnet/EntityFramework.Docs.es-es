---
title: 'Migraciones en entornos de equipo: EF Core'
description: Prácticas recomendadas para administrar migraciones y resolver conflictos en entornos de equipo con Entity Framework Core
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/teams
ms.openlocfilehash: 90549b369624301bc183e5a8a3cc1d6a92bb7008
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062508"
---
# <a name="migrations-in-team-environments"></a>Migraciones en entornos de equipo

Al trabajar con migraciones en entornos de equipo, preste especial atención al archivo de instantáneas del modelo. Este archivo puede indicarle si la migración de su compañero de equipo se combina correctamente con la suya o si necesita resolver un conflicto volviendo a crear la migración antes de compartirla.

## <a name="merging"></a>Combinación

Al fusionar mediante combinación las migraciones de sus compañeros de equipo, puede obtener conflictos en el archivo de instantánea del modelo. Si los dos cambios no están relacionados, la combinación es trivial y las dos migraciones pueden coexistir. Por ejemplo, puede obtener un conflicto de fusión mediante combinación en la configuración del tipo de entidad Customer, que tiene el siguiente aspecto:

```output
<<<<<<< Mine
b.Property<bool>("Deactivated");
=======
b.Property<int>("LoyaltyPoints");
>>>>>>> Theirs
```

Puesto que ambas propiedades deben existir en el modelo final, complete la combinación agregando ambas propiedades. En muchos casos, es posible que el sistema de control de versiones combine automáticamente estos cambios.

```csharp
b.Property<bool>("Deactivated");
b.Property<int>("LoyaltyPoints");
```

En estos casos, la migración y la migración de su compañero son independientes entre sí. Dado que cualquiera de ellas se podría aplicar en primer lugar, no es necesario realizar ningún cambio adicional en la migración antes de compartirla con el equipo.

## <a name="resolving-conflicts"></a>Resolución de conflictos

A veces se produce un conflicto real al combinar el modelo de instantánea de modelo. Por ejemplo, usted y su compañero de equipo pueden cambiar el nombre de la misma propiedad.

```output
<<<<<<< Mine
b.Property<string>("Username");
=======
b.Property<string>("Alias");
>>>>>>> Theirs
```

Si encuentra este tipo de conflicto, resuélvalos volviendo a crear la migración. Siga estos pasos:

1. Anular la combinación y revertir al directorio de trabajo antes de la fusión mediante combinación
2. Quitar la migración (pero mantener los cambios del modelo)
3. Combinar los cambios de su compañero en el directorio de trabajo
4. Volver a agregar la migración

Después de hacer esto, las dos migraciones se pueden aplicar en el orden correcto. En primer lugar, se aplica su migración, cambiando el nombre de la columna a *alias*y, a partir de ese momento, la migración lo cambia por nombre de *usuario*.

La migración puede compartirse de forma segura con el resto del equipo.
