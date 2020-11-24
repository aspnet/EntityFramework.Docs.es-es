---
title: 'Descripción general de las migraciones: EF Core'
description: Información general sobre el uso de migraciones para administrar esquemas de base de datos con Entity Framework Core
author: bricelam
ms.date: 10/28/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: b9547298714af59453aeae6d05742a03c067708b
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003400"
---
# <a name="migrations-overview"></a><span data-ttu-id="da966-103">Descripción general de las migraciones</span><span class="sxs-lookup"><span data-stu-id="da966-103">Migrations Overview</span></span>

<span data-ttu-id="da966-104">En proyectos reales, los modelos de datos cambian a medida que se implementan características: se agregan o se quitan nuevas entidades o propiedades, y los esquemas de base de datos se deben cambiar según corresponda para mantenerlos sincronizados con la aplicación.</span><span class="sxs-lookup"><span data-stu-id="da966-104">In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application.</span></span> <span data-ttu-id="da966-105">La característica de migraciones de EF Core proporciona una manera de actualizar incrementalmente el esquema de la base de datos para mantenerla sincronizada con el modelo de datos de la aplicación al tiempo que se conservan los datos existentes en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="da966-105">The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.</span></span>

<span data-ttu-id="da966-106">A nivel general, las migraciones funcionan de esta forma:</span><span class="sxs-lookup"><span data-stu-id="da966-106">At a high level, migrations function in the following way:</span></span>

* <span data-ttu-id="da966-107">Cuando se introduce un cambio en el modelo de datos, el desarrollador usa herramientas de EF Core para agregar una migración correspondiente en la que se describan las actualizaciones necesarias para mantener sincronizado el esquema de la base de datos. EF Core compara el modelo actual con una instantánea del modelo anterior para determinar las diferencias y genera los archivos de origen de la migración, de los que se puede realizar el seguimiento en el control de código fuente del proyecto como cualquier otro archivo de código fuente.</span><span class="sxs-lookup"><span data-stu-id="da966-107">When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.</span></span>
* <span data-ttu-id="da966-108">Una vez que se ha generado una migración nueva, haya varias maneras de aplicarla a una base de datos.</span><span class="sxs-lookup"><span data-stu-id="da966-108">Once a new migration has been generated, it can be applied to a database in various ways.</span></span> <span data-ttu-id="da966-109">EF Core registra todas las migraciones aplicadas en una tabla de historial especial, lo que le permite saber qué migraciones se han aplicado y cuáles no.</span><span class="sxs-lookup"><span data-stu-id="da966-109">EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.</span></span>

<span data-ttu-id="da966-110">El resto de esta página es una guía para principiantes paso a paso sobre el uso de las migraciones.</span><span class="sxs-lookup"><span data-stu-id="da966-110">The rest of this page is a step-by-step beginner's guide for using migrations.</span></span> <span data-ttu-id="da966-111">Consulte las demás páginas de esta sección para obtener información más detallada.</span><span class="sxs-lookup"><span data-stu-id="da966-111">Consult the other pages in this section for more in-depth information.</span></span>

## <a name="getting-started"></a><span data-ttu-id="da966-112">Introducción</span><span class="sxs-lookup"><span data-stu-id="da966-112">Getting started</span></span>

<span data-ttu-id="da966-113">Imagine que acaba de completar la primera aplicación de EF Core, que contiene el siguiente modelo simple:</span><span class="sxs-lookup"><span data-stu-id="da966-113">Let's assume you've just completed your first EF Core application, which contains the following simple model:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

<span data-ttu-id="da966-114">Durante el desarrollo, es posible que haya usado las [API Create y Drop](xref:core/managing-schemas/ensure-created) para realizar una iteración rápida y cambiar el modelo según las necesidades, pero ahora que la aplicación se destina a producción, necesita una manera de desarrollar de forma segura el esquema sin quitar la base de datos completa.</span><span class="sxs-lookup"><span data-stu-id="da966-114">During development, you may have used the [Create and Drop APIs](xref:core/managing-schemas/ensure-created) to iterate quickly, changing your model as needed; but now that your application is going to production, you need a way to safely evolve the schema without dropping the entire database.</span></span>

### <a name="install-the-tools"></a><span data-ttu-id="da966-115">Instalar las herramientas</span><span class="sxs-lookup"><span data-stu-id="da966-115">Install the tools</span></span>

<span data-ttu-id="da966-116">En primer lugar, tendrá que instalar las [herramientas de línea de comandos de EF Core](xref:core/cli/index):</span><span class="sxs-lookup"><span data-stu-id="da966-116">First, you'll have to install the [EF Core command-line tools](xref:core/cli/index):</span></span>

* <span data-ttu-id="da966-117">Por lo general, se recomienda usar las [herramientas de la CLI de .NET Core](xref:core/cli/dotnet), que funcionan en todas las plataformas.</span><span class="sxs-lookup"><span data-stu-id="da966-117">We generally recommend using the [.NET Core CLI tools](xref:core/cli/dotnet), which work on all platforms.</span></span>
* <span data-ttu-id="da966-118">Si se siente más cómodo trabajando en Visual Studio o tiene experiencia con las migraciones de EF6, también puede usar las [ herramientas de la consola del administrador de paquetes](xref:core/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="da966-118">If you're more comfortable working inside Visual Studio or have experience with EF6 migrations, you can also use the [Package Manager Console tools](xref:core/cli/powershell).</span></span>

### <a name="create-your-first-migration"></a><span data-ttu-id="da966-119">Creación de la primera migración</span><span class="sxs-lookup"><span data-stu-id="da966-119">Create your first migration</span></span>

<span data-ttu-id="da966-120">Ya está listo para agregar la primera migración.</span><span class="sxs-lookup"><span data-stu-id="da966-120">You're now ready to add your first migration!</span></span> <span data-ttu-id="da966-121">Indique a EF Core que cree una migración llamada **InitialCreate**:</span><span class="sxs-lookup"><span data-stu-id="da966-121">Instruct EF Core to create a migration named **InitialCreate**:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="da966-122">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="da966-122">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[<span data-ttu-id="da966-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da966-123">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate
```

<span data-ttu-id="da966-124">\*\*_</span><span class="sxs-lookup"><span data-stu-id="da966-124">\*\*_</span></span>

<span data-ttu-id="da966-125">EF Core creará un directorio denominado _ *Migrations*\* (Migraciones) en el proyecto y generará varios archivos.</span><span class="sxs-lookup"><span data-stu-id="da966-125">EF Core will create a directory called _ *Migrations*\* in your project, and generate some files.</span></span> <span data-ttu-id="da966-126">Es recomendable inspeccionar lo que EF Core ha generado exactamente y, posiblemente, rectificarlo, pero este paso se omitirá por ahora.</span><span class="sxs-lookup"><span data-stu-id="da966-126">It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.</span></span>

### <a name="create-your-database-and-schema"></a><span data-ttu-id="da966-127">Creación de la base de datos y el esquema</span><span class="sxs-lookup"><span data-stu-id="da966-127">Create your database and schema</span></span>

<span data-ttu-id="da966-128">En este momento puede hacer que EF cree la base de datos y el esquema a partir de la migración.</span><span class="sxs-lookup"><span data-stu-id="da966-128">At this point you can have EF create your database and create your schema from the migration.</span></span> <span data-ttu-id="da966-129">Esto también se puede hacer mediante:</span><span class="sxs-lookup"><span data-stu-id="da966-129">This can be done via the following:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="da966-130">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="da966-130">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[<span data-ttu-id="da966-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da966-131">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database
```

<span data-ttu-id="da966-132">\*\*_</span><span class="sxs-lookup"><span data-stu-id="da966-132">\*\*_</span></span>

<span data-ttu-id="da966-133">Eso es todo: la aplicación está lista para ejecutarse en la base de datos nueva y no es necesario escribir una sola línea de SQL.</span><span class="sxs-lookup"><span data-stu-id="da966-133">That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL.</span></span> <span data-ttu-id="da966-134">Tenga en cuenta que esta manera de aplicar migraciones resulta idónea para el desarrollo local, pero es menos adecuada para los entornos de producción; vea la página [Aplicación de migraciones](xref:core/managing-schemas/migrations/applying) para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="da966-134">Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](xref:core/managing-schemas/migrations/applying) for more info.</span></span>

### <a name="evolving-your-model"></a><span data-ttu-id="da966-135">Evolución del modelo</span><span class="sxs-lookup"><span data-stu-id="da966-135">Evolving your model</span></span>

<span data-ttu-id="da966-136">Han pasado unos días y le piden que agregue una marca de tiempo de creación a los blogs.</span><span class="sxs-lookup"><span data-stu-id="da966-136">A few days have passed, and you're asked to add a creation timestamp to your blogs.</span></span> <span data-ttu-id="da966-137">Ha realizado los cambios necesarios en la aplicación y el ahora modelo tiene este aspecto:</span><span class="sxs-lookup"><span data-stu-id="da966-137">You've done the necessary changes to your application, and your model now looks like this:</span></span>

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

<span data-ttu-id="da966-138">Ahora el modelo y la base de datos de producción están desincronizados; tendrá que agregar una nueva columna al esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="da966-138">Your model and your production database are now out of sync - we must add a new column to your database schema.</span></span> <span data-ttu-id="da966-139">Cree una migración para esto:</span><span class="sxs-lookup"><span data-stu-id="da966-139">Let's create a new migration for this:</span></span>

#### <a name="net-core-cli"></a>[<span data-ttu-id="da966-140">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="da966-140">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[<span data-ttu-id="da966-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da966-141">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

_*_

<span data-ttu-id="da966-142">Tenga en cuenta que a las migraciones se les proporciona un nombre descriptivo para que después sea más fácil entender el historial del proyecto.</span><span class="sxs-lookup"><span data-stu-id="da966-142">Note that we give migrations a descriptive name, to make it easier to understand the project history later.</span></span>

<span data-ttu-id="da966-143">Como no se trata de la primera migración del proyecto, ahora EF Core compara el modelo actualizado con una instantánea del modelo anterior, antes de que se agregara la columna; la instantánea del modelo es uno de los archivos generados por EF Core cuando se agrega una migración y se inserta en el control de código fuente.</span><span class="sxs-lookup"><span data-stu-id="da966-143">Since this isn't the project's first migration, EF Core now compares your updated model against a snapshot of the old model, before the column was added; the model snapshot is one of the files generated by EF Core when you add a migration, and is checked into source control.</span></span> <span data-ttu-id="da966-144">En función de esa comparación, EF Core detecta que se ha agregado una columna y agrega la migración adecuada.</span><span class="sxs-lookup"><span data-stu-id="da966-144">Based on that comparison, EF Core detects that a column has been added, and adds the appropriate migration.</span></span>

<span data-ttu-id="da966-145">Ahora puede aplicar la migración como antes:</span><span class="sxs-lookup"><span data-stu-id="da966-145">You can now apply your migration as before:</span></span>

<!--markdownlint-disable MD024-->

#### <a name="net-core-cli"></a>[<span data-ttu-id="da966-146">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="da966-146">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[<span data-ttu-id="da966-147">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="da966-147">Visual Studio</span></span>](#tab/vs)

```powershell
Update-Database
```

<!--markdownlint-enable MD024-->

_*_

<span data-ttu-id="da966-148">Tenga en cuenta que, en esta ocasión, EF detecta que la base de datos ya existe.</span><span class="sxs-lookup"><span data-stu-id="da966-148">Note that this time, EF detects that the database already exists.</span></span> <span data-ttu-id="da966-149">Además, cuando antes se ha aplicado la primera migración, esta operación se ha registrado en una tabla de historial de migraciones especial en la base de datos, lo que permite que EF solo aplique de forma automática la nueva migración.</span><span class="sxs-lookup"><span data-stu-id="da966-149">In addition, when our first migration was applied above, this fact was recorded in a special migrations history table in your database; this allows EF to automatically apply only the new migration.</span></span>

### <a name="excluding-parts-of-your-model"></a><span data-ttu-id="da966-150">Exclusión de elementos del modelo</span><span class="sxs-lookup"><span data-stu-id="da966-150">Excluding parts of your model</span></span>

> [!NOTE]
> <span data-ttu-id="da966-151">Esta característica se incluyó por primera vez en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="da966-151">This feature was introduced EF in Core 5.0.</span></span>

<span data-ttu-id="da966-152">En ocasiones, es posible que quiera consultar tipos de otro DbContext.</span><span class="sxs-lookup"><span data-stu-id="da966-152">Sometimes you may want to reference types from another DbContext.</span></span> <span data-ttu-id="da966-153">Esto puede dar lugar a conflictos de migración.</span><span class="sxs-lookup"><span data-stu-id="da966-153">This can lead to migration conflicts.</span></span> <span data-ttu-id="da966-154">Para evitarlo, excluya el tipo de las migraciones de uno de los elementos DbContext.</span><span class="sxs-lookup"><span data-stu-id="da966-154">To prevent this, exclude the type from the migrations of one of the DbContexts.</span></span>

[!code-csharp[](../../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs#TableExcludeFromMigrations)]

### <a name="next-steps"></a><span data-ttu-id="da966-155">Pasos siguientes</span><span class="sxs-lookup"><span data-stu-id="da966-155">Next steps</span></span>

<span data-ttu-id="da966-156">Lo anterior solo era una breve introducción a las migraciones.</span><span class="sxs-lookup"><span data-stu-id="da966-156">The above was only a brief introduction to migrations.</span></span> <span data-ttu-id="da966-157">Consulte las demás páginas de documentación para obtener más información sobre cómo [administrar migraciones](xref:core/managing-schemas/migrations/managing), [aplicarlas](xref:core/managing-schemas/migrations/applying) y otros aspectos.</span><span class="sxs-lookup"><span data-stu-id="da966-157">Please consult the other documentation pages to learn more about [managing migrations](xref:core/managing-schemas/migrations/managing), [applying them](xref:core/managing-schemas/migrations/applying), and other aspects.</span></span> <span data-ttu-id="da966-158">La [referencia de herramientas de la CLI de .NET Core](xref:core/cli/index) también contiene información útil sobre los distintos comandos</span><span class="sxs-lookup"><span data-stu-id="da966-158">The [.NET Core CLI tool reference](xref:core/cli/index) also contains useful information on the different commands</span></span>

## <a name="additional-resources"></a><span data-ttu-id="da966-159">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="da966-159">Additional resources</span></span>

<span data-ttu-id="da966-160">_ [Sesión de Reunión de la comunidad de EF Core](https://www.youtube.com/watch?v=mSsGERmrhnE&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=20) donde se analizan las nuevas características de migración de EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="da966-160">_ [EF Core Community Standup session](https://www.youtube.com/watch?v=mSsGERmrhnE&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=20) going over new migration features in EF Core 5.0.</span></span>
