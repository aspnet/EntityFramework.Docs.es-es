---
title: 'Creación y configuración de un modelo: EF Core'
description: Información general sobre la creación y configuración de un modelo con Entity Framework Core
author: AndriySvyryd
ms.date: 10/13/2020
uid: core/modeling/index
ms.openlocfilehash: 709e2bde60c8e2c31f0a39390624c5d31a9cfa08
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129205"
---
# <a name="creating-and-configuring-a-model"></a><span data-ttu-id="0dc73-103">Creación y configuración de un modelo</span><span class="sxs-lookup"><span data-stu-id="0dc73-103">Creating and configuring a model</span></span>

<span data-ttu-id="0dc73-104">Entity Framework usa un conjunto de convenciones para compilar un modelo basado en la forma de las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="0dc73-104">Entity Framework uses a set of conventions to build a model based on the shape of your entity classes.</span></span> <span data-ttu-id="0dc73-105">Puede especificar una configuración adicional para complementar o reemplazar lo que se ha detectado por convención.</span><span class="sxs-lookup"><span data-stu-id="0dc73-105">You can specify additional configuration to supplement and/or override what was discovered by convention.</span></span>

<span data-ttu-id="0dc73-106">Este artículo trata de la configuración que se puede aplicar a un modelo para cualquier almacén de datos y que se puede aplicar al elegir como destino cualquier base de datos relacional.</span><span class="sxs-lookup"><span data-stu-id="0dc73-106">This article covers configuration that can be applied to a model targeting any data store and that which can be applied when targeting any relational database.</span></span> <span data-ttu-id="0dc73-107">Los proveedores también pueden habilitar la configuración específica de un almacén de datos determinado.</span><span class="sxs-lookup"><span data-stu-id="0dc73-107">Providers may also enable configuration that is specific to a particular data store.</span></span> <span data-ttu-id="0dc73-108">Para obtener documentación sobre la configuración específica del proveedor, vea la sección [Proveedores de bases de datos](xref:core/providers/index).</span><span class="sxs-lookup"><span data-stu-id="0dc73-108">For documentation on provider specific configuration see the [Database Providers](xref:core/providers/index) section.</span></span>

> [!TIP]
> <span data-ttu-id="0dc73-109">Puede ver un [ejemplo](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) de este artículo en GitHub.</span><span class="sxs-lookup"><span data-stu-id="0dc73-109">You can view this article’s [sample](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples) on GitHub.</span></span>

## <a name="use-fluent-api-to-configure-a-model"></a><span data-ttu-id="0dc73-110">Uso de la API fluida para configurar un modelo</span><span class="sxs-lookup"><span data-stu-id="0dc73-110">Use fluent API to configure a model</span></span>

<span data-ttu-id="0dc73-111">Puede reemplazar el método `OnModelCreating` del contexto derivado y usar `ModelBuilder API` para configurar el modelo.</span><span class="sxs-lookup"><span data-stu-id="0dc73-111">You can override the `OnModelCreating` method in your derived context and use the `ModelBuilder API` to configure your model.</span></span> <span data-ttu-id="0dc73-112">Este es el método más eficaz de configuración y permite especificar la configuración sin modificar las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="0dc73-112">This is the most powerful method of configuration and allows configuration to be specified without modifying your entity classes.</span></span> <span data-ttu-id="0dc73-113">La configuración de API fluida tiene la prioridad más alta y reemplaza las anotaciones de datos y las convenciones.</span><span class="sxs-lookup"><span data-stu-id="0dc73-113">Fluent API configuration has the highest precedence and will override conventions and data annotations.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

### <a name="grouping-configuration"></a><span data-ttu-id="0dc73-114">Configuración de agrupación</span><span class="sxs-lookup"><span data-stu-id="0dc73-114">Grouping configuration</span></span>

<span data-ttu-id="0dc73-115">Para reducir el tamaño del método <xref:System.Data.Entity.DbContext.OnModelCreating%2A>, toda la configuración de un tipo de entidad se puede extraer en una clase independiente que implemente <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601>.</span><span class="sxs-lookup"><span data-stu-id="0dc73-115">To reduce the size of the <xref:System.Data.Entity.DbContext.OnModelCreating%2A> method all configuration for an entity type can be extracted to a separate class implementing <xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601>.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=IEntityTypeConfiguration)]

<span data-ttu-id="0dc73-116">A continuación, basta con invocar el método `Configure` desde `OnModelCreating`.</span><span class="sxs-lookup"><span data-stu-id="0dc73-116">Then just invoke the `Configure` method from `OnModelCreating`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyIEntityTypeConfiguration)]

<span data-ttu-id="0dc73-117">Es posible aplicar toda la configuración especificada en tipos que implementen `IEntityTypeConfiguration` en un ensamblado determinado.</span><span class="sxs-lookup"><span data-stu-id="0dc73-117">It is possible to apply all configuration specified in types implementing `IEntityTypeConfiguration` in a given assembly.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyConfigurationsFromAssembly)]

> [!NOTE]
> <span data-ttu-id="0dc73-118">El orden en que se aplicarán las configuraciones está sin definir, por lo que solo debe usarse este método si el orden no importa.</span><span class="sxs-lookup"><span data-stu-id="0dc73-118">The order in which the configurations will be applied is undefined, therefore this method should only be used when the order doesn't matter.</span></span>

## <a name="use-data-annotations-to-configure-a-model"></a><span data-ttu-id="0dc73-119">Uso de anotaciones de datos para configurar un modelo</span><span class="sxs-lookup"><span data-stu-id="0dc73-119">Use data annotations to configure a model</span></span>

<span data-ttu-id="0dc73-120">También puede aplicar atributos (conocidos como anotaciones de datos) a las clases y las propiedades.</span><span class="sxs-lookup"><span data-stu-id="0dc73-120">You can also apply attributes (known as Data Annotations) to your classes and properties.</span></span> <span data-ttu-id="0dc73-121">Las anotaciones de datos reemplazarán a las convenciones, pero la configuración de la API fluida también las reemplazará.</span><span class="sxs-lookup"><span data-stu-id="0dc73-121">Data annotations will override conventions, but will be overridden by Fluent API configuration.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
