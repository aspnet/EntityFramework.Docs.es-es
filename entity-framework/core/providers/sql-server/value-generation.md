---
title: 'Proveedor de base de datos de Microsoft SQL Server: generación de valores-EF Core'
description: Patrones de generación de valores específicos del proveedor de bases de datos de SQL Server Entity Framework Core
author: roji
ms.date: 1/10/2020
uid: core/providers/sql-server/value-generation
ms.openlocfilehash: 90608f254a3d20e3c36586ae8325e0a982a7fa27
ms.sourcegitcommit: 7700840119b1639275f3b64836e7abb59103f2e7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987143"
---
# <a name="sql-server-value-generation"></a><span data-ttu-id="a70e8-103">Generación de SQL Server valor</span><span class="sxs-lookup"><span data-stu-id="a70e8-103">SQL Server Value Generation</span></span>

<span data-ttu-id="a70e8-104">En esta página se detallan los patrones y la configuración de generación de valores específicos del proveedor de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a70e8-104">This page details value generation configuration  and patterns that are specific to the SQL Server provider.</span></span> <span data-ttu-id="a70e8-105">Se recomienda leer primero [la página general en la generación de valores](xref:core/modeling/generated-properties).</span><span class="sxs-lookup"><span data-stu-id="a70e8-105">It's recommended to first read [the general page on value generation](xref:core/modeling/generated-properties).</span></span>

## <a name="identity-columns"></a><span data-ttu-id="a70e8-106">Columnas IDENTITY</span><span class="sxs-lookup"><span data-stu-id="a70e8-106">IDENTITY columns</span></span>

<span data-ttu-id="a70e8-107">Por Convención, las columnas numéricas que están configuradas para que los valores generados en Add se configuran como [SQL Server columnas de identidad](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property).</span><span class="sxs-lookup"><span data-stu-id="a70e8-107">By convention, numeric columns that are configured to have their values generated on add are set up as [SQL Server IDENTITY columns](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property).</span></span>

### <a name="seed-and-increment"></a><span data-ttu-id="a70e8-108">Inicialización e incremento</span><span class="sxs-lookup"><span data-stu-id="a70e8-108">Seed and increment</span></span>

<span data-ttu-id="a70e8-109">De forma predeterminada, las columnas de identidad se inician en 1 (el valor de inicialización) y se incrementan en 1 cada vez que se agrega una fila (el incremento).</span><span class="sxs-lookup"><span data-stu-id="a70e8-109">By default, IDENTITY columns start off at 1 (the seed), and increment by 1 each time a row is added (the increment).</span></span> <span data-ttu-id="a70e8-110">Puede configurar una inicialización y un incremento diferentes como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="a70e8-110">You can configure a different seed and increment as follows:</span></span>

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/IdentityOptionsContext.cs?name=IdentityOptions&highlight=5)]

> [!NOTE]
> <span data-ttu-id="a70e8-111">La capacidad de configurar el incremento e inicialización de identidad se presentó en EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="a70e8-111">The ability to configure IDENTITY seed and increment was introduced in EF Core 3.0.</span></span>

### <a name="inserting-explicit-values-into-identity-columns"></a><span data-ttu-id="a70e8-112">Insertar valores explícitos en columnas de identidad</span><span class="sxs-lookup"><span data-stu-id="a70e8-112">Inserting explicit values into IDENTITY columns</span></span>

<span data-ttu-id="a70e8-113">De forma predeterminada, SQL Server no permite insertar valores explícitos en columnas de identidad.</span><span class="sxs-lookup"><span data-stu-id="a70e8-113">By default, SQL Server doesn't allow inserting explicit values into IDENTITY columns.</span></span> <span data-ttu-id="a70e8-114">Para ello, debe habilitarlo manualmente `IDENTITY_INSERT` antes de llamar a `SaveChanges()` , como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="a70e8-114">To do so, you must manually enable `IDENTITY_INSERT` before calling `SaveChanges()`, as follows:</span></span>

[!code-csharp[Main](../../../../samples/core/SqlServer/ValueGeneration/ExplicitIdentityValues.cs?name=ExplicitIdentityValues)]

> [!NOTE]
> <span data-ttu-id="a70e8-115">Tenemos una [solicitud de característica](https://github.com/aspnet/EntityFramework/issues/703) en el trabajo pendiente para hacer esto de manera automática dentro del proveedor de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="a70e8-115">We have a [feature request](https://github.com/aspnet/EntityFramework/issues/703) on our backlog to do this automatically within the SQL Server provider.</span></span>

## <a name="sequences"></a><span data-ttu-id="a70e8-116">Secuencias</span><span class="sxs-lookup"><span data-stu-id="a70e8-116">Sequences</span></span>

<span data-ttu-id="a70e8-117">Como alternativa a las columnas de identidad, puede usar secuencias estándar.</span><span class="sxs-lookup"><span data-stu-id="a70e8-117">As an alternative to IDENTITY columns, you can use standard sequences.</span></span> <span data-ttu-id="a70e8-118">Esto puede ser útil en varios escenarios; por ejemplo, puede que desee que varias columnas dibuje sus valores predeterminados de una sola secuencia.</span><span class="sxs-lookup"><span data-stu-id="a70e8-118">This can be useful in various scenarios; for example, you may want to have multiple columns drawing their default values from a single sequence.</span></span>

<span data-ttu-id="a70e8-119">SQL Server permite crear secuencias y usarlas tal y como se detalla en [la página general en secuencias](xref:core/modeling/sequences).</span><span class="sxs-lookup"><span data-stu-id="a70e8-119">SQL Server allows you to create sequences and use them as detailed in [the general page on sequences](xref:core/modeling/sequences).</span></span> <span data-ttu-id="a70e8-120">Depende de usted configurar las propiedades para usar secuencias a través de `HasDefaultValueSql()` .</span><span class="sxs-lookup"><span data-stu-id="a70e8-120">It's up to you to configure your properties to use sequences via `HasDefaultValueSql()`.</span></span>
