---
title: 'Secuencias: EF Core'
description: Configurar secuencias en un modelo de Entity Framework Core
author: roji
ms.date: 12/18/2019
uid: core/modeling/sequences
ms.openlocfilehash: e388bde11b1251564b37227e8884d2b8c34dc02e
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128529"
---
# <a name="sequences"></a><span data-ttu-id="650ef-103">Secuencias</span><span class="sxs-lookup"><span data-stu-id="650ef-103">Sequences</span></span>

> [!NOTE]
> <span data-ttu-id="650ef-104">Las secuencias son una característica que normalmente solo admiten las bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="650ef-104">Sequences are a feature typically supported only by relational databases.</span></span> <span data-ttu-id="650ef-105">Si utiliza una base de datos no relacional como Cosmos, consulte la documentación de la base de datos para generar valores únicos.</span><span class="sxs-lookup"><span data-stu-id="650ef-105">If you're using a non-relational database such as Cosmos, check your database documentation on generating unique values.</span></span>

<span data-ttu-id="650ef-106">Una secuencia genera valores numéricos únicos y secuenciales en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="650ef-106">A sequence generates unique, sequential numeric values in the database.</span></span> <span data-ttu-id="650ef-107">Las secuencias no están asociadas a una tabla específica y se pueden configurar varias tablas para que dibujen valores de la misma secuencia.</span><span class="sxs-lookup"><span data-stu-id="650ef-107">Sequences are not associated with a specific table, and multiple tables can be set up to draw values from the same sequence.</span></span>

## <a name="basic-usage"></a><span data-ttu-id="650ef-108">Uso básico</span><span class="sxs-lookup"><span data-stu-id="650ef-108">Basic usage</span></span>

<span data-ttu-id="650ef-109">Puede configurar una secuencia en el modelo y, a continuación, utilizarla para generar valores para las propiedades:</span><span class="sxs-lookup"><span data-stu-id="650ef-109">You can set up a sequence in the model, and then use it to generate values for properties:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Sequence.cs?name=Sequence&highlight=3,7)]

<span data-ttu-id="650ef-110">Tenga en cuenta que el SQL específico que se usa para generar un valor a partir de una secuencia es específico de la base de datos; el ejemplo anterior funciona en SQL Server pero producirá un error en otras bases de datos.</span><span class="sxs-lookup"><span data-stu-id="650ef-110">Note that the specific SQL used to generate a value from a sequence is database-specific; the above example works on SQL Server but will fail on other databases.</span></span> <span data-ttu-id="650ef-111">Consulte la documentación específica de su base de datos para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="650ef-111">Consult your specific database's documentation for more information.</span></span>

## <a name="configuring-sequence-settings"></a><span data-ttu-id="650ef-112">Configuración de las opciones de secuencia</span><span class="sxs-lookup"><span data-stu-id="650ef-112">Configuring sequence settings</span></span>

<span data-ttu-id="650ef-113">También puede configurar aspectos adicionales de la secuencia, como su esquema, valor inicial, incremento, etc.:</span><span class="sxs-lookup"><span data-stu-id="650ef-113">You can also configure additional aspects of the sequence, such as its schema, start value, increment, etc.:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SequenceConfiguration.cs?name=SequenceConfiguration&highlight=3-5)]
