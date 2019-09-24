---
title: 'Longitud máxima: EF Core'
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: c39c5d43-018d-48b8-94f2-b8bc7c686c69
uid: core/modeling/max-length
ms.openlocfilehash: b6f0594fed0c491b4f79dcda5273cdebe9ecf35f
ms.sourcegitcommit: ec196918691f50cd0b21693515b0549f06d9f39c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/23/2019
ms.locfileid: "71197228"
---
# <a name="maximum-length"></a><span data-ttu-id="57f5f-102">Longitud máxima</span><span class="sxs-lookup"><span data-stu-id="57f5f-102">Maximum Length</span></span>

<span data-ttu-id="57f5f-103">La configuración de una longitud máxima proporciona una sugerencia al almacén de datos sobre el tipo de datos adecuado que se va a usar para una propiedad determinada.</span><span class="sxs-lookup"><span data-stu-id="57f5f-103">Configuring a maximum length provides a hint to the data store about the appropriate data type to use for a given property.</span></span> <span data-ttu-id="57f5f-104">La longitud máxima solo se aplica a los tipos de datos `string` de `byte[]`matriz, como y.</span><span class="sxs-lookup"><span data-stu-id="57f5f-104">Maximum length only applies to array data types, such as `string` and `byte[]`.</span></span>

> [!NOTE]  
> <span data-ttu-id="57f5f-105">Entity Framework no realiza ninguna validación de la longitud máxima antes de pasar datos al proveedor.</span><span class="sxs-lookup"><span data-stu-id="57f5f-105">Entity Framework does not do any validation of maximum length before passing data to the provider.</span></span> <span data-ttu-id="57f5f-106">Depende del proveedor o del almacén de datos que se valide si es necesario.</span><span class="sxs-lookup"><span data-stu-id="57f5f-106">It is up to the provider or data store to validate if appropriate.</span></span> <span data-ttu-id="57f5f-107">Por ejemplo, cuando el destino es SQL Server, si se supera la longitud máxima, se producirá una excepción, ya que el tipo de datos de la columna subyacente no permitirá que se almacenen los datos sobrantes.</span><span class="sxs-lookup"><span data-stu-id="57f5f-107">For example, when targeting SQL Server, exceeding the maximum length will result in an exception as the data type of the underlying column will not allow excess data to be stored.</span></span>

## <a name="conventions"></a><span data-ttu-id="57f5f-108">Convenciones</span><span class="sxs-lookup"><span data-stu-id="57f5f-108">Conventions</span></span>

<span data-ttu-id="57f5f-109">Por Convención, se deja al proveedor de base de datos para elegir un tipo de datos adecuado para las propiedades.</span><span class="sxs-lookup"><span data-stu-id="57f5f-109">By convention, it is left up to the database provider to choose an appropriate data type for properties.</span></span> <span data-ttu-id="57f5f-110">En el caso de las propiedades que tienen una longitud, el proveedor de base de datos suele elegir un tipo de datos que permite la mayor longitud de los datos.</span><span class="sxs-lookup"><span data-stu-id="57f5f-110">For properties that have a length, the database provider will generally choose a data type that allows for the longest length of data.</span></span> <span data-ttu-id="57f5f-111">Por ejemplo, Microsoft SQL Server utilizará `nvarchar(max)` para `string` las propiedades ( `nvarchar(450)` o si la columna se utiliza como clave).</span><span class="sxs-lookup"><span data-stu-id="57f5f-111">For example, Microsoft SQL Server will use `nvarchar(max)` for `string` properties (or `nvarchar(450)` if the column is used as a key).</span></span>

## <a name="data-annotations"></a><span data-ttu-id="57f5f-112">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="57f5f-112">Data Annotations</span></span>

<span data-ttu-id="57f5f-113">Puede usar las anotaciones de datos para configurar una longitud máxima para una propiedad.</span><span class="sxs-lookup"><span data-stu-id="57f5f-113">You can use the Data Annotations to configure a maximum length for a property.</span></span> <span data-ttu-id="57f5f-114">En este ejemplo, al establecer como destino SQL Server esto daría lugar `nvarchar(500)` a que se usara el tipo de datos.</span><span class="sxs-lookup"><span data-stu-id="57f5f-114">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/MaxLength.cs?highlight=14)]

## <a name="fluent-api"></a><span data-ttu-id="57f5f-115">API fluida</span><span class="sxs-lookup"><span data-stu-id="57f5f-115">Fluent API</span></span>

<span data-ttu-id="57f5f-116">Puede usar la API fluida para configurar una longitud máxima para una propiedad.</span><span class="sxs-lookup"><span data-stu-id="57f5f-116">You can use the Fluent API to configure a maximum length for a property.</span></span> <span data-ttu-id="57f5f-117">En este ejemplo, al establecer como destino SQL Server esto daría lugar `nvarchar(500)` a que se usara el tipo de datos.</span><span class="sxs-lookup"><span data-stu-id="57f5f-117">In this example, targeting SQL Server this would result in the `nvarchar(500)` data type being used.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/MaxLength.cs?highlight=11-13)]
