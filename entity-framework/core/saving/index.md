---
title: Guardado de datos - EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: ef044629-feca-4fd1-a48f-d208daedaf92
ms.technology: entity-framework-core
uid: core/saving/index
ms.openlocfilehash: 9280b9c34b41c0319f918488cd7d28eeceef12e7
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2017
---
# <a name="saving-data"></a><span data-ttu-id="8b09a-102">Guardado de datos</span><span class="sxs-lookup"><span data-stu-id="8b09a-102">Saving Data</span></span>

<span data-ttu-id="8b09a-103">Cada instancia de contexto tiene un elemento `ChangeTracker` que es responsable de realizar el seguimiento de los cambios que deben escribirse en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8b09a-103">Each context instance has a `ChangeTracker` that is responsible for keeping track of changes that need to be written to the database.</span></span> <span data-ttu-id="8b09a-104">Al realizar cambios en instancias de las clases de entidad, estos cambios se registran en `ChangeTracker` y luego se escriben en la base de datos cuando se llama a `SaveChanges`.</span><span class="sxs-lookup"><span data-stu-id="8b09a-104">As you make changes to instances of your entity classes, these changes are recorded in the `ChangeTracker` and then written to the database when you call `SaveChanges`.</span></span> <span data-ttu-id="8b09a-105">El proveedor de base de datos es responsable de convertir los cambios en operaciones específicas de la base de datos (por ejemplo, los comandos `INSERT`, `UPDATE` y `DELETE` de una base de datos relacional).</span><span class="sxs-lookup"><span data-stu-id="8b09a-105">The database provider is responsible for translating the changes into database-specific operations (e.g. `INSERT`, `UPDATE`, and `DELETE` commands for a relational database).</span></span>