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
# <a name="saving-data"></a><span data-ttu-id="36392-103">Guardado de datos</span><span class="sxs-lookup"><span data-stu-id="36392-103">Saving Data</span></span>

<span data-ttu-id="36392-104">Cada instancia de contexto tiene un elemento `ChangeTracker` que es responsable de realizar el seguimiento de los cambios que deben escribirse en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="36392-104">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="36392-105">Al realizar cambios en instancias de las clases de entidad, estos cambios se registran en `ChangeTracker` y luego se escriben en la base de datos cuando se llama a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="36392-105">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="36392-106">El proveedor de base de datos es responsable de convertir los cambios en operaciones específicas de la base de datos (por ejemplo, los comandos `INSERT`, `UPDATE` y `DELETE` de una base de datos relacional).</span><span class="sxs-lookup"><span data-stu-id="36392-106">The database provider is responsible for translating the changes into database-specific operations (for example, `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>
