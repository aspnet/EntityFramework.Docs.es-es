---
title: 'Carga de datos relacionados: EF Core'
description: Diferentes estrategias para cargar datos relacionados con Entity Framework Core
author: roji
ms.date: 9/11/2020
uid: core/querying/related-data
ms.openlocfilehash: a86cae91dbfbf5ebcf820c6afb72d812e278dc66
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063678"
---
# <a name="loading-related-data"></a>Carga de datos relacionados

Entity Framework Core permite usar las propiedades de navegación del modelo para cargar las entidades relacionados. Existen tres patrones de O/RM comunes que se usan para cargar los datos relacionados.

* **[Carga diligente](xref:core/querying/related-data/eager)** significa que los datos relacionados se cargan desde la base de datos como parte de la consulta inicial.
* **[Carga explícita](xref:core/querying/related-data/explicit)** significa que los datos relacionados se cargan de manera explícita desde la base de datos más adelante.
* **[Carga diferida](xref:core/querying/related-data/lazy)** significa que los datos relacionados se cargan de manera transparente desde la base de datos cuando se accede a la propiedad de navegación.

> [!TIP]
> Puede ver los [ejemplos](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Querying/RelatedData) que hay en esta sección en GitHub.
