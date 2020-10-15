---
title: 'Creación y configuración de un modelo: EF Core'
description: Información general sobre la creación y configuración de un modelo con Entity Framework Core
author: AndriySvyryd
ms.date: 11/05/2019
uid: core/modeling/index
ms.openlocfilehash: 7f8ef17f33a294dc0b8cc9c4e514a8a29b761342
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063067"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="9aead-103">Creación y configuración de un modelo</span><span class="sxs-lookup"><span data-stu-id="9aead-103">Creating and configuring a model</span></span>

<span data-ttu-id="9aead-104">Entity Framework usa un conjunto de convenciones para compilar un modelo basado en la forma de las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="9aead-104">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="9aead-105">Puede especificar una configuración adicional para complementar o reemplazar lo que se ha detectado por convención.</span><span class="sxs-lookup"><span data-stu-id="9aead-105">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="9aead-106">Este artículo trata de la configuración que se puede aplicar a un modelo para cualquier almacén de datos y que se puede aplicar al elegir como destino cualquier base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="9aead-106">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="9aead-107">Los proveedores también pueden habilitar la configuración específica de un almacén de datos determinado.</span><span class="sxs-lookup"><span data-stu-id="9aead-107">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="9aead-108">Para obtener documentación sobre la configuración específica del proveedor, vea la sección  [Proveedores de bases de datos](xref:core/providers/index) .</span><span class="sxs-lookup"><span data-stu-id="9aead-108">For documentation on provider specific configuration see the [Database Providers](xref:core/providers/index) section.</span></span>

> [!TIP]  
> <span data-ttu-id="9aead-109">Puede ver un  [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples)  de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="9aead-109">You can view this article’s [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="9aead-110">Uso de la API fluida para configurar un modelo</span><span class="sxs-lookup"><span data-stu-id="9aead-110">Use fluent API to configure a model</span></span>

<span data-ttu-id="9aead-111">Puede reemplazar el método  `OnModelCreating`  del contexto derivado y usar  `ModelBuilder API` para configurar el modelo.</span><span class="sxs-lookup"><span data-stu-id="9aead-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="9aead-112">Este es el método más eficaz de configuración y permite especificar la configuración sin modificar las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="9aead-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="9aead-113">La configuración de API fluida tiene la prioridad más alta y reemplaza las anotaciones de datos y las convenciones.</span><span class="sxs-lookup"><span data-stu-id="9aead-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="9aead-114">Uso de anotaciones de datos para configurar un modelo</span><span class="sxs-lookup"><span data-stu-id="9aead-114">Use data annotations to configure a model</span></span>

<span data-ttu-id="9aead-115">También puede aplicar atributos (conocidos como anotaciones de datos) a las clases y las propiedades.</span><span class="sxs-lookup"><span data-stu-id="9aead-115">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="9aead-116">Las anotaciones de datos reemplazarán a las convenciones, pero la configuración de la API fluida también las reemplazará.</span><span class="sxs-lookup"><span data-stu-id="9aead-116">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
