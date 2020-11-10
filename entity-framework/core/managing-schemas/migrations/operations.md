---
title: 'Operaciones de migración personalizadas: EF Core'
description: Administrar migraciones de SQL personalizadas y sin formato para la administración de esquemas de base de datos con Entity Framework Core
author: bricelam
ms.date: 10/27/2020
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 2abde4d5eac977a746863dcfd77bc85a34e2166c
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429837"
---
# <a name="custom-migrations-operations"></a><span data-ttu-id="22271-103">Operaciones de migración personalizadas</span><span class="sxs-lookup"><span data-stu-id="22271-103">Custom Migrations Operations</span></span>

<span data-ttu-id="22271-104">La API de MigrationBuilder permite realizar muchos tipos diferentes de operaciones durante una migración, pero está lejos de ser exhaustiva.</span><span class="sxs-lookup"><span data-stu-id="22271-104">The MigrationBuilder API allows you to perform many different kinds of operations during a migration, but it's far from exhaustive.</span></span> <span data-ttu-id="22271-105">Sin embargo, la API también es extensible, lo que le permite definir sus propias operaciones.</span><span class="sxs-lookup"><span data-stu-id="22271-105">However, the API is also extensible allowing you to define your own operations.</span></span> <span data-ttu-id="22271-106">Hay dos maneras de extender la API: mediante el `Sql()` método o mediante la definición de `MigrationOperation` objetos personalizados.</span><span class="sxs-lookup"><span data-stu-id="22271-106">There are two ways to extend the API: Using the `Sql()` method, or by defining custom `MigrationOperation` objects.</span></span>

<span data-ttu-id="22271-107">Para ilustrar, echemos un vistazo a la implementación de una operación que crea un usuario de base de datos mediante cada enfoque.</span><span class="sxs-lookup"><span data-stu-id="22271-107">To illustrate, let's look at implementing an operation that creates a database user using each approach.</span></span> <span data-ttu-id="22271-108">En nuestras migraciones, queremos habilitar la escritura del código siguiente:</span><span class="sxs-lookup"><span data-stu-id="22271-108">In our migrations, we want to enable writing the following code:</span></span>

```csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a><span data-ttu-id="22271-109">Usar MigrationBuilder. SQL ()</span><span class="sxs-lookup"><span data-stu-id="22271-109">Using MigrationBuilder.Sql()</span></span>

<span data-ttu-id="22271-110">La forma más fácil de implementar una operación personalizada es definir un método de extensión que llame a `MigrationBuilder.Sql()` .</span><span class="sxs-lookup"><span data-stu-id="22271-110">The easiest way to implement a custom operation is to define an extension method that calls `MigrationBuilder.Sql()`.</span></span> <span data-ttu-id="22271-111">Este es un ejemplo que genera el correspondiente Transact-SQL.</span><span class="sxs-lookup"><span data-stu-id="22271-111">Here is an example that generates the appropriate Transact-SQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationSql.cs#snippet_CustomOperationSql)]

> [!TIP]
> <span data-ttu-id="22271-112">Utilice la `EXEC` función cuando una instrucción debe ser la primera o solo una en un lote de SQL.</span><span class="sxs-lookup"><span data-stu-id="22271-112">Use the `EXEC` function when a statement must be the first or only one in a SQL batch.</span></span> <span data-ttu-id="22271-113">También podría ser necesario para solucionar los errores del analizador en scripts de migración idempotente que se pueden producir cuando las columnas a las que se hace referencia no existen actualmente en una tabla.</span><span class="sxs-lookup"><span data-stu-id="22271-113">It might also be needed to work around parser errors in idempotent migration scripts that can occur when referenced columns don't currently exist on a table.</span></span>

<span data-ttu-id="22271-114">Si las migraciones necesitan admitir varios proveedores de bases de datos, puede utilizar la `MigrationBuilder.ActiveProvider` propiedad.</span><span class="sxs-lookup"><span data-stu-id="22271-114">If your migrations need to support multiple database providers, you can use the `MigrationBuilder.ActiveProvider` property.</span></span> <span data-ttu-id="22271-115">Este es un ejemplo que admite tanto Microsoft SQL Server como PostgreSQL.</span><span class="sxs-lookup"><span data-stu-id="22271-115">Here's an example supporting both Microsoft SQL Server and PostgreSQL.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperationMultiSql.cs#snippet_CustomOperationMultiSql)]

<span data-ttu-id="22271-116">Este enfoque solo funciona si conoce todos los proveedores en los que se va a aplicar la operación personalizada.</span><span class="sxs-lookup"><span data-stu-id="22271-116">This approach only works if you know every provider where your custom operation will be applied.</span></span>

## <a name="using-a-migrationoperation"></a><span data-ttu-id="22271-117">Uso de un MigrationOperation</span><span class="sxs-lookup"><span data-stu-id="22271-117">Using a MigrationOperation</span></span>

<span data-ttu-id="22271-118">Para desacoplar la operación personalizada de SQL, puede definir la suya propia `MigrationOperation` para representarla.</span><span class="sxs-lookup"><span data-stu-id="22271-118">To decouple the custom operation from the SQL, you can define your own `MigrationOperation` to represent it.</span></span> <span data-ttu-id="22271-119">A continuación, la operación se pasa al proveedor para que pueda determinar el SQL adecuado que se va a generar.</span><span class="sxs-lookup"><span data-stu-id="22271-119">The operation is then passed to the provider so it can determine the appropriate SQL to generate.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_CreateUserOperation)]

<span data-ttu-id="22271-120">Con este enfoque, el método de extensión solo tiene que agregar una de estas operaciones a `MigrationBuilder.Operations` .</span><span class="sxs-lookup"><span data-stu-id="22271-120">With this approach, the extension method just needs to add one of these operations to `MigrationBuilder.Operations`.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationBuilderExtension)]

<span data-ttu-id="22271-121">Este enfoque requiere que cada proveedor sepa cómo generar SQL para esta operación en su `IMigrationsSqlGenerator` servicio.</span><span class="sxs-lookup"><span data-stu-id="22271-121">This approach requires each provider to know how to generate SQL for this operation in their `IMigrationsSqlGenerator` service.</span></span> <span data-ttu-id="22271-122">Este es un ejemplo invalidando el generador del SQL Server para administrar la nueva operación.</span><span class="sxs-lookup"><span data-stu-id="22271-122">Here is an example overriding the SQL Server's generator to handle the new operation.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_MigrationsSqlGenerator)]

<span data-ttu-id="22271-123">Reemplace el servicio de generador de SQL de migraciones predeterminado por el actualizado.</span><span class="sxs-lookup"><span data-stu-id="22271-123">Replace the default migrations sql generator service with the updated one.</span></span>

[!code-csharp[](../../../../samples/core/Schemas/Migrations/CustomOperation.cs#snippet_OnConfiguring)]
