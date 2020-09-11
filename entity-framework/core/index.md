---
title: Información general de Entity Framework Core - EF Core
description: Información general de Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: bc2a2676-bc46-493f-bf49-e3cc97994d57
uid: core/index
ms.openlocfilehash: 4816f2d57590ddd2ad4807daa2cc1c5000cd5713
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619487"
---
# <a name="entity-framework-core"></a>Entity Framework Core

Entity Framework (EF) Core es una versión ligera, extensible, [de código abierto](https://github.com/aspnet/EntityFrameworkCore) y multiplataforma de la popular tecnología de acceso a datos Entity Framework.

EF Core puede servir como asignador relacional de objetos (O/RM), lo que permite a los desarrolladores de .NET trabajar con una base de datos mediante objetos .NET y eliminar la mayoría del código de acceso a los datos que normalmente deben escribir.

EF Core es compatible con muchos motores de base de datos; vea [Proveedores de bases de datos](xref:core/providers/index) para más información.

## <a name="the-model"></a>El modelo

Con EF Core, el acceso a datos se realiza mediante un modelo. Un modelo se compone de clases de entidad y un objeto de contexto que representa una sesión con la base de datos, lo que permite consultar y guardar los datos. Vea [Creación de un modelo](xref:core/modeling/index) para más información.

Puede generar un modelo a partir de una base de datos existente, codificar manualmente un modelo para que coincida con la base de datos o usar [migraciones de EF](xref:core/managing-schemas/migrations/index) para crear una base de datos a partir del modelo y que evolucione a medida que cambia el modelo.

[!code-csharp[Main](../../samples/core/Intro/Model.cs)]

## <a name="querying"></a>Consultas

Las instancias de las clases de entidad se recuperan de la base de datos mediante Language Integrated Query (LINQ). Vea [Consulta de datos](xref:core/querying/index) para más información.

[!code-csharp[Main](../../samples/core/Intro/Program.cs#Querying)]

## <a name="saving-data"></a>Guardado de datos

Los datos se crean, se eliminan y se modifican en la base de datos mediante instancias de las clases de entidad. Vea [Guardado de datos](xref:core/saving/index) para más información.

[!code-csharp[Main](../../samples/core/Intro/Program.cs#SavingData)]

## <a name="next-steps"></a>Pasos siguientes

Para consultar tutoriales de introducción, vea [Introducción a Entity Framework Core](xref:core/get-started/index).
