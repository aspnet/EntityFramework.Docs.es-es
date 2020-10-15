---
title: 'Trabajo con entidades desconectadas: EF6'
description: Trabajo con entidades desconectadas en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/disconnected-entities/index
ms.openlocfilehash: 235609a20ff5a0ec50743a61c8aabeb2d46103b9
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065542"
---
# <a name="working-with-disconnected-entities"></a>Trabajo con entidades desconectadas

En una aplicación basada en Entity Framework, una clase de contexto es responsable de detectar los cambios aplicados a las entidades sometidas a seguimiento. Una llamada al método SaveChanges almacena los cambios que controla el contexto en la base de datos. Cuando se trabaja con aplicaciones de n niveles, los objetos de entidad generalmente se modifican mientras están desconectados del contexto y es necesario decidir cómo realizar el seguimiento de los cambios y notificar esos cambios al contexto. En este tema se habla de las distintas opciones disponibles cuando se usa Entity Framework con entidades desconectadas.

## <a name="web-service-frameworks"></a>Marcos de trabajo de servicio web

Las tecnologías de servicios web suelen admitir modelos que pueden usarse para almacenar los cambios en objetos individuales desconectados. Por ejemplo, ASP.NET Web API permite codificar acciones de controlador que pueden incluir llamadas a EF para almacenar los cambios realizados en un objeto en una base de datos. De hecho, las herramientas de Web API de Visual Studio facilitan la aplicación de scaffolding a un controlador de Web API desde el modelo de Entity Framework 6. Para obtener más información, vea [Usar Web API con Entity Framework 6](/aspnet/web-api/overview/data/using-web-api-with-entity-framework/).

Históricamente, ha habido otras tecnologías de servicios web que ofrecían integración con Entity Framework, como [WCF Data Services](/dotnet/framework/data/wcf/create-a-data-service-using-an-adonet-ef-data-wcf) y [RIA Services](/previous-versions/dotnet/wcf-ria/ee707344(v=vs.91)).

## <a name="low-level-ef-apis"></a>API de EF de bajo nivel

Si no quiere usar una solución de n niveles existente, o si quiere personalizar lo que ocurre dentro de una acción de controlador en los servicios de Web API, Entity Framework proporciona API que permiten aplicar los cambios realizados en un nivel desconectado. Para obtener más información, vea [Add, Attach, and entity state](xref:ef6/saving/change-tracking/entity-state) (Agregar, adjuntar y estado de entidad).  

## <a name="self-tracking-entities"></a>Entidades de seguimiento propio  

El seguimiento de los cambios en gráficos arbitrarios de entidades mientras se está desconectado del contexto de EF es un problema difícil. Uno de los intentos de solucionar el problema fue la plantilla de generación de código Entidades de autoseguimiento. Esta plantilla genera clases de entidad que contienen lógica para seguir los cambios realizados en un nivel desconectado como estado en las propias entidades. También se genera un conjunto de métodos de extensión para aplicar esos cambios a un contexto.

Esta plantilla se puede usar con los modelos creados mediante EF Designer, pero no con modelos de Code First. Para obtener más información, vea [Entidades de autoseguimiento](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index).  

> [!IMPORTANT]
> Ya no se recomienda usar la plantilla Entidades de autoseguimiento. Solo sigue estando disponible para la compatibilidad con las aplicaciones existentes. Si la aplicación necesita trabajar con gráficos desconectados de entidades, considere otras alternativas, como [Trackable Entities](https://trackableentities.github.io/), que es una tecnología similar a Entidades de autoseguimiento pero que la comunidad desarrolla de forma más activa, o escriba código personalizado mediante la API de seguimiento de cambios de bajo nivel.
