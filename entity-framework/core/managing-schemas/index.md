---
title: Administración de esquemas de base de datos - EF Core
description: Información general sobre las estrategias para administrar esquemas de base de datos con Entity Framework Core
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/index
ms.openlocfilehash: e4f8c82125534e9e7e0c2de552bce336a544a2aa
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619503"
---
# <a name="managing-database-schemas"></a><span data-ttu-id="dfed7-103">Administración de esquemas de base de datos</span><span class="sxs-lookup"><span data-stu-id="dfed7-103">Managing Database Schemas</span></span>

<span data-ttu-id="dfed7-104">EF Core proporciona dos métodos principales para mantener sincronizados el esquema de la base de datos y el modelo de EF Core. Para elegir entre los dos, decida si es el modelo de EF Core o el esquema de la base de datos el origen verdadero.</span><span class="sxs-lookup"><span data-stu-id="dfed7-104">EF Core provides two primary ways of keeping your EF Core model and database schema in sync. To choose between the two, decide whether your EF Core model or the database schema is the source of truth.</span></span>

<span data-ttu-id="dfed7-105">Si quiere que el modelo de EF Core sea el origen verdadero, use [Migraciones][1].</span><span class="sxs-lookup"><span data-stu-id="dfed7-105">If you want your EF Core model to be the source of truth, use [Migrations][1].</span></span> <span data-ttu-id="dfed7-106">Al realizar cambios en el modelo de EF Core, este método aplica de forma incremental los cambios de esquema correspondientes a la base de datos para que siga siendo compatible con el modelo de EF Core.</span><span class="sxs-lookup"><span data-stu-id="dfed7-106">As you make changes to your EF Core model, this approach incrementally applies the corresponding schema changes to your database so that it remains compatible with your EF Core model.</span></span>

<span data-ttu-id="dfed7-107">Si quiere que el esquema de la base de datos sea el origen verdadero, use [Ingeniería inversa][2].</span><span class="sxs-lookup"><span data-stu-id="dfed7-107">Use [Reverse Engineering][2] if you want your database schema to be the source of truth.</span></span> <span data-ttu-id="dfed7-108">Este método permite aplicar la técnica de scaffolding a un elemento DbContext y a las clases de tipo de entidad mediante la aplicación de ingeniería inversa al esquema de la base de datos de un modelo de EF Core.</span><span class="sxs-lookup"><span data-stu-id="dfed7-108">This approach allows you to scaffold a DbContext and the entity type classes by reverse engineering your database schema into an EF Core model.</span></span>

> [!NOTE]
> <span data-ttu-id="dfed7-109">Las [API de creación y eliminación][3] también pueden crear el esquema de la base de datos a partir del modelo de EF Core.</span><span class="sxs-lookup"><span data-stu-id="dfed7-109">The [create and drop APIs][3] can also create the database schema from your EF Core model.</span></span> <span data-ttu-id="dfed7-110">Pero son principalmente para pruebas, creación de prototipos y otros escenarios donde la eliminación de la base de datos es aceptable.</span><span class="sxs-lookup"><span data-stu-id="dfed7-110">However, they are primarily for testing, prototyping, and other scenarios where dropping the database is acceptable.</span></span>

  [1]: xref:core/managing-schemas/migrations/index
  [2]: xref:core/managing-schemas/scaffolding
  [3]: xref:core/managing-schemas/ensure-created
