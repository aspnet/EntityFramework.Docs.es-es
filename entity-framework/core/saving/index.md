---
title: Guardado de datos - EF Core
description: Información general sobre cómo guardar datos con Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: core/saving/index
ms.openlocfilehash: 98a23fd4956a8646e9760f9ecb0b48166ad6caa1
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072478"
---
# <a name="saving-data"></a>Guardado de datos

Cada instancia de contexto tiene un elemento `ChangeTracker` que es responsable de realizar el seguimiento de los cambios que deben escribirse en la base de datos. Al realizar cambios en instancias de las clases de entidad, estos cambios se registran en `ChangeTracker` y luego se escriben en la base de datos cuando se llama a `SaveChanges`. El proveedor de base de datos es responsable de convertir los cambios en operaciones específicas de la base de datos (por ejemplo, los comandos `INSERT`, `UPDATE` y `DELETE` de una base de datos relacional).
