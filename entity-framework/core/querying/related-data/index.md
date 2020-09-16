---
title: 'Carga de datos relacionados: EF Core'
description: Diferentes estrategias para cargar datos relacionados con Entity Framework Core
author: roji
ms.date: 9/11/2020
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
uid: core/querying/related-data
ms.openlocfilehash: 5c1addac6735de39d5c72f447d5d06280411a21e
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90078856"
---
# <a name="loading-related-data"></a>Carga de datos relacionados

Entity Framework Core permite usar las propiedades de navegación del modelo para cargar las entidades relacionados. Existen tres patrones de O/RM comunes que se usan para cargar los datos relacionados.

* **[Carga diligente](xref:core/querying/related-data/eager)** significa que los datos relacionados se cargan desde la base de datos como parte de la consulta inicial.
* **[Carga explícita](xref:core/querying/related-data/explicit)** significa que los datos relacionados se cargan de manera explícita desde la base de datos más adelante.
* **[Carga diferida](xref:core/querying/related-data/lazy)** significa que los datos relacionados se cargan de manera transparente desde la base de datos cuando se accede a la propiedad de navegación.

> [!TIP]
> Puede ver los [ejemplos](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying) que hay en esta sección en GitHub.
