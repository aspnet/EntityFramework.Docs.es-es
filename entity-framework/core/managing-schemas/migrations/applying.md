---
title: 'Aplicación de migraciones: EF Core'
description: Estrategias para aplicar migraciones de esquema a las bases de datos de desarrollo y de producción con Entity Framework Core
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: 7ff84636fb0999941b832c6a2d65d77b0ad368c5
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429862"
---
# <a name="applying-migrations"></a><span data-ttu-id="1da6c-103">Aplicación de migraciones</span><span class="sxs-lookup"><span data-stu-id="1da6c-103">Applying Migrations</span></span>

<span data-ttu-id="1da6c-104">Una vez agregadas las migraciones, deben implementarse y aplicarse a las bases de datos.</span><span class="sxs-lookup"><span data-stu-id="1da6c-104">Once your migrations have been added, they need to be deployed and applied to your databases.</span></span> <span data-ttu-id="1da6c-105">Existen varias estrategias para hacerlo, con algunas más adecuadas para entornos de producción y otras para el ciclo de vida de desarrollo.</span><span class="sxs-lookup"><span data-stu-id="1da6c-105">There are various strategies for doing this, with some being more appropriate for production environments, and others for the development lifecycle.</span></span>

> [!NOTE]
> <span data-ttu-id="1da6c-106">Sea cual sea su estrategia de implementación, inspeccione siempre las migraciones generadas y pruébelos antes de aplicarlas a una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="1da6c-106">Whatever your deployment strategy, always inspect the generated migrations and test them before applying to a production database.</span></span> <span data-ttu-id="1da6c-107">Una migración puede quitar una columna cuando el intento era cambiarle el nombre o puede producir un error por diversos motivos cuando se aplica a una base de datos.</span><span class="sxs-lookup"><span data-stu-id="1da6c-107">A migration may drop a column when the intent was to rename it, or may fail for various reasons when applied to a database.</span></span>

## <a name="sql-scripts"></a><span data-ttu-id="1da6c-108">Scripts SQL</span><span class="sxs-lookup"><span data-stu-id="1da6c-108">SQL scripts</span></span>

<span data-ttu-id="1da6c-109">La manera recomendada de implementar las migraciones en una base de datos de producción es mediante la generación de scripts SQL.</span><span class="sxs-lookup"><span data-stu-id="1da6c-109">The recommended way to deploy migrations to a production database is by generating SQL scripts.</span></span> <span data-ttu-id="1da6c-110">Entre las ventajas de esta estrategia se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="1da6c-110">The advantages of this strategy include the following:</span></span>

* <span data-ttu-id="1da6c-111">Se puede revisar la precisión de los scripts SQL; Esto es importante, ya que la aplicación de cambios de esquema en las bases de datos de producción es una operación potencialmente peligrosa que podría implicar la pérdida de datos.</span><span class="sxs-lookup"><span data-stu-id="1da6c-111">SQL scripts can be reviewed for accuracy; this is important since applying schema changes to production databases is a potentially dangerous operation that could involve data loss.</span></span>
* <span data-ttu-id="1da6c-112">En algunos casos, los scripts se pueden optimizar para ajustarse a las necesidades específicas de una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="1da6c-112">In some cases, the scripts can be tuned to fit the specific needs of a production database.</span></span>
* <span data-ttu-id="1da6c-113">Los scripts SQL se pueden usar junto con una tecnología de implementación, y incluso pueden generarse como parte del proceso de CI.</span><span class="sxs-lookup"><span data-stu-id="1da6c-113">SQL scripts can be used in conjunction with a deployment technology, and can even be generated as part of your CI process.</span></span>
* <span data-ttu-id="1da6c-114">Se pueden proporcionar scripts SQL a un DBA y se pueden administrar y archivar por separado.</span><span class="sxs-lookup"><span data-stu-id="1da6c-114">SQL scripts can be provided to a DBA, and can be managed and archived separately.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="1da6c-115">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="1da6c-115">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a><span data-ttu-id="1da6c-116">Uso básico</span><span class="sxs-lookup"><span data-stu-id="1da6c-116">Basic Usage</span></span>

<span data-ttu-id="1da6c-117">Lo siguiente genera un script SQL desde una base de datos vacía a la migración más reciente:</span><span class="sxs-lookup"><span data-stu-id="1da6c-117">The following generates a SQL script from a blank database to the latest migration:</span></span>

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="1da6c-118">Con From (To implícito)</span><span class="sxs-lookup"><span data-stu-id="1da6c-118">With From (to implied)</span></span>

<span data-ttu-id="1da6c-119">Lo siguiente genera un script SQL a partir de la migración dada a la última migración.</span><span class="sxs-lookup"><span data-stu-id="1da6c-119">The following generates a SQL script from the given migration to the latest migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="1da6c-120">Con From y To</span><span class="sxs-lookup"><span data-stu-id="1da6c-120">With From and To</span></span>

<span data-ttu-id="1da6c-121">Lo siguiente genera un script SQL a partir de la `from` migración especificada a la `to` migración especificada.</span><span class="sxs-lookup"><span data-stu-id="1da6c-121">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

<span data-ttu-id="1da6c-122">Puede usar un valor `from` que sea más reciente que el valor `to` para generar un script de reversión.</span><span class="sxs-lookup"><span data-stu-id="1da6c-122">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span>

> [!WARNING]
> <span data-ttu-id="1da6c-123">Tome nota de los posibles escenarios de pérdida de datos.</span><span class="sxs-lookup"><span data-stu-id="1da6c-123">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="1da6c-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1da6c-124">Visual Studio</span></span>](#tab/vs)

#### <a name="basic-usage"></a><span data-ttu-id="1da6c-125">Uso básico</span><span class="sxs-lookup"><span data-stu-id="1da6c-125">Basic Usage</span></span>

<span data-ttu-id="1da6c-126">Lo siguiente genera un script SQL desde una base de datos vacía a la migración más reciente:</span><span class="sxs-lookup"><span data-stu-id="1da6c-126">The following generates a SQL script from a blank database to the latest migration:</span></span>

```powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a><span data-ttu-id="1da6c-127">Con From (To implícito)</span><span class="sxs-lookup"><span data-stu-id="1da6c-127">With From (to implied)</span></span>

<span data-ttu-id="1da6c-128">Lo siguiente genera un script SQL a partir de la migración dada a la última migración.</span><span class="sxs-lookup"><span data-stu-id="1da6c-128">The following generates a SQL script from the given migration to the latest migration.</span></span>

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a><span data-ttu-id="1da6c-129">Con From y To</span><span class="sxs-lookup"><span data-stu-id="1da6c-129">With From and To</span></span>

<span data-ttu-id="1da6c-130">Lo siguiente genera un script SQL a partir de la `from` migración especificada a la `to` migración especificada.</span><span class="sxs-lookup"><span data-stu-id="1da6c-130">The following generates a SQL script from the specified `from` migration to the specified `to` migration.</span></span>

```powershell
Script-Migration AddNewTables AddAuditTable
```

<span data-ttu-id="1da6c-131">Puede usar un valor `from` que sea más reciente que el valor `to` para generar un script de reversión.</span><span class="sxs-lookup"><span data-stu-id="1da6c-131">You can use a `from` that is newer than the `to` in order to generate a rollback script.</span></span> <span data-ttu-id="1da6c-132">*Tome nota de los posibles escenarios de pérdida de datos.*</span><span class="sxs-lookup"><span data-stu-id="1da6c-132">*Please take note of potential data loss scenarios.*</span></span>

***

<span data-ttu-id="1da6c-133">La generación de script acepta los dos argumentos siguientes en indica qué intervalo de migraciones se debe generar:</span><span class="sxs-lookup"><span data-stu-id="1da6c-133">Script generation accepts the following two arguments in indicate which range of migrations should be generated:</span></span>

* <span data-ttu-id="1da6c-134">La migración **from** debe ser la última migración aplicada a la base de datos antes de ejecutar el script.</span><span class="sxs-lookup"><span data-stu-id="1da6c-134">The **from** migration should be the last migration applied to the database before running the script.</span></span> <span data-ttu-id="1da6c-135">Si no se han aplicado migraciones, especifique `0` (es el valor predeterminado).</span><span class="sxs-lookup"><span data-stu-id="1da6c-135">If no migrations have been applied, specify `0` (this is the default).</span></span>
* <span data-ttu-id="1da6c-136">La migración **to** debe ser la última migración que se va a aplicar a la base de datos después de ejecutar el script.</span><span class="sxs-lookup"><span data-stu-id="1da6c-136">The **to** migration is the last migration that will be applied to the database after running the script.</span></span> <span data-ttu-id="1da6c-137">El valor predeterminado es la última migración del proyecto.</span><span class="sxs-lookup"><span data-stu-id="1da6c-137">This defaults to the last migration in your project.</span></span>

## <a name="idempotent-sql-scripts"></a><span data-ttu-id="1da6c-138">Scripts SQL idempotentes</span><span class="sxs-lookup"><span data-stu-id="1da6c-138">Idempotent SQL scripts</span></span>

<span data-ttu-id="1da6c-139">Los scripts SQL generados anteriormente solo se pueden aplicar para cambiar el esquema de una migración a otra; es su responsabilidad aplicar el script adecuadamente y solo en la base de datos en el estado de migración correcto.</span><span class="sxs-lookup"><span data-stu-id="1da6c-139">The SQL scripts generated above can only be applied to change your schema from one migration to another; it is your responsibility to apply the script appropriately, and only to database in the correct migration state.</span></span> <span data-ttu-id="1da6c-140">EF Core también admite la generación de scripts **idempotente** , que internamente comprueban qué migraciones ya se han aplicado (a través de la tabla de historial de migraciones) y solo aplican las que faltan.</span><span class="sxs-lookup"><span data-stu-id="1da6c-140">EF Core also supports generating **idempotent** scripts, which internally check which migrations have already been applied (via the migrations history table), and only apply missing ones.</span></span> <span data-ttu-id="1da6c-141">Esto resulta útil si no sabe exactamente cuál fue la última migración aplicada a la base de datos, o si está implementando en varias bases de datos que pueden estar en una migración diferente.</span><span class="sxs-lookup"><span data-stu-id="1da6c-141">This is useful if you don't exactly know what the last migration applied to the database was, or if you are deploying to multiple databases that may each be at a different migration.</span></span>

<span data-ttu-id="1da6c-142">Lo siguiente genera migraciones idempotente:</span><span class="sxs-lookup"><span data-stu-id="1da6c-142">The following generates idempotent migrations:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="1da6c-143">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="1da6c-143">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

### <a name="visual-studio"></a>[<span data-ttu-id="1da6c-144">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1da6c-144">Visual Studio</span></span>](#tab/vs)

```powershell
Script-Migration -Idempotent
```

<span data-ttu-id="1da6c-145">\*\*_</span><span class="sxs-lookup"><span data-stu-id="1da6c-145">\*\*_</span></span>

## <a name="command-line-tools"></a><span data-ttu-id="1da6c-146">Herramientas de línea de comandos</span><span class="sxs-lookup"><span data-stu-id="1da6c-146">Command-line tools</span></span>

<span data-ttu-id="1da6c-147">Las herramientas de línea de comandos de EF se pueden usar para aplicar migraciones a una base de datos.</span><span class="sxs-lookup"><span data-stu-id="1da6c-147">The EF command-line tools can be used to apply migrations to a database.</span></span> <span data-ttu-id="1da6c-148">Aunque es productivo para el desarrollo y las pruebas locales de las migraciones, este enfoque no es idóneo para la administración de bases de datos de producción:</span><span class="sxs-lookup"><span data-stu-id="1da6c-148">While productive for local development and testing of migrations, this approach isn't ideal for managing production databases:</span></span>

<span data-ttu-id="1da6c-149">_ Los comandos SQL se aplican directamente a la herramienta, sin que el desarrollador tenga la oportunidad de inspeccionarlos o modificarlos.</span><span class="sxs-lookup"><span data-stu-id="1da6c-149">_ The SQL commands are applied directly by the tool, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="1da6c-150">Esto puede ser peligroso en un entorno de producción.</span><span class="sxs-lookup"><span data-stu-id="1da6c-150">This can be dangerous in a production environment.</span></span>
* <span data-ttu-id="1da6c-151">El SDK de .NET y la herramienta EF deben instalarse en servidores de producción.</span><span class="sxs-lookup"><span data-stu-id="1da6c-151">The .NET SDK and the EF tool must be installed on production servers.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="1da6c-152">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="1da6c-152">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

<span data-ttu-id="1da6c-153">La siguiente actualización de la base de datos a la migración más reciente:</span><span class="sxs-lookup"><span data-stu-id="1da6c-153">The following updates your database to the latest migration:</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="1da6c-154">La siguiente actualización de la base de datos a una migración determinada:</span><span class="sxs-lookup"><span data-stu-id="1da6c-154">The following updates your database to a given migration:</span></span>

```dotnetcli
dotnet ef database update AddNewTables
```

<span data-ttu-id="1da6c-155">Tenga en cuenta que esto puede usarse también para revertir a una migración anterior.</span><span class="sxs-lookup"><span data-stu-id="1da6c-155">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="1da6c-156">Tome nota de los posibles escenarios de pérdida de datos.</span><span class="sxs-lookup"><span data-stu-id="1da6c-156">Please take note of potential data loss scenarios.</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="1da6c-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1da6c-157">Visual Studio</span></span>](#tab/vs)

<span data-ttu-id="1da6c-158">La siguiente actualización de la base de datos a la migración más reciente:</span><span class="sxs-lookup"><span data-stu-id="1da6c-158">The following updates your database to the latest migration:</span></span>

```powershell
Update-Database
```

<span data-ttu-id="1da6c-159">La siguiente actualización de la base de datos a una migración determinada:</span><span class="sxs-lookup"><span data-stu-id="1da6c-159">The following updates your database to a given migration:</span></span>

```powershell
Update-Database AddNewTables
```

<span data-ttu-id="1da6c-160">Tenga en cuenta que esto puede usarse también para revertir a una migración anterior.</span><span class="sxs-lookup"><span data-stu-id="1da6c-160">Note that this can be used to roll back to an earlier migration as well.</span></span>

> [!WARNING]
> <span data-ttu-id="1da6c-161">Tome nota de los posibles escenarios de pérdida de datos.</span><span class="sxs-lookup"><span data-stu-id="1da6c-161">Please take note of potential data loss scenarios.</span></span>

***

<span data-ttu-id="1da6c-162">Para obtener más información sobre cómo aplicar migraciones a través de las herramientas de línea de comandos, consulte la [referencia de herramientas de EF Core](xref:core/cli/index).</span><span class="sxs-lookup"><span data-stu-id="1da6c-162">For more information on applying migrations via the command-line tools, see the [EF Core tools reference](xref:core/cli/index).</span></span>

## <a name="apply-migrations-at-runtime"></a><span data-ttu-id="1da6c-163">Aplicar migraciones en tiempo de ejecución</span><span class="sxs-lookup"><span data-stu-id="1da6c-163">Apply migrations at runtime</span></span>

<span data-ttu-id="1da6c-164">Es posible que la propia aplicación aplique migraciones mediante programación, normalmente durante el inicio.</span><span class="sxs-lookup"><span data-stu-id="1da6c-164">It's possible for the application itself to apply migrations programmatically, typically during startup.</span></span> <span data-ttu-id="1da6c-165">Aunque es productivo para el desarrollo y las pruebas locales de las migraciones, este enfoque no es apropiado para la administración de bases de datos de producción, por las siguientes razones:</span><span class="sxs-lookup"><span data-stu-id="1da6c-165">While productive for local development and testing of migrations, this approach is inappropriate for managing production databases, for the following reasons:</span></span>

* <span data-ttu-id="1da6c-166">Si se están ejecutando varias instancias de la aplicación, ambas aplicaciones podrían intentar aplicar la migración simultáneamente y producir un error (o peor, causar daños en los datos).</span><span class="sxs-lookup"><span data-stu-id="1da6c-166">If multiple instances of your application are running, both applications could attempt to apply the migration concurrently and fail (or worse, cause data corruption).</span></span>
* <span data-ttu-id="1da6c-167">Del mismo modo, si una aplicación obtiene acceso a la base de datos mientras otra aplicación la migra, esto puede provocar problemas graves.</span><span class="sxs-lookup"><span data-stu-id="1da6c-167">Similarly, if an application is accessing the database while another application migrates it, this can cause severe issues.</span></span>
* <span data-ttu-id="1da6c-168">La aplicación debe tener acceso elevado para modificar el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="1da6c-168">The application must have elevated access to modify the database schema.</span></span> <span data-ttu-id="1da6c-169">Por lo general, se recomienda limitar los permisos de base de datos de la aplicación en producción.</span><span class="sxs-lookup"><span data-stu-id="1da6c-169">It's generally good practice to limit the application's database permissions in production.</span></span>
* <span data-ttu-id="1da6c-170">Es importante poder revertir una migración aplicada en caso de que se produzca un problema.</span><span class="sxs-lookup"><span data-stu-id="1da6c-170">It's important to be able to roll back an applied migration in case of an issue.</span></span> <span data-ttu-id="1da6c-171">Las otras estrategias proporcionan esto de forma sencilla y rápida.</span><span class="sxs-lookup"><span data-stu-id="1da6c-171">The other strategies provide this easily and out of the box.</span></span>
* <span data-ttu-id="1da6c-172">Los comandos SQL los aplica directamente el programa, sin ofrecer al desarrollador una oportunidad de inspeccionarlos o modificarlos.</span><span class="sxs-lookup"><span data-stu-id="1da6c-172">The SQL commands are applied directly by the program, without giving the developer a chance to inspect or modify them.</span></span> <span data-ttu-id="1da6c-173">Esto puede ser peligroso en un entorno de producción.</span><span class="sxs-lookup"><span data-stu-id="1da6c-173">This can be dangerous in a production environment.</span></span>

<span data-ttu-id="1da6c-174">Para aplicar las migraciones mediante programación, llame a `context.Database.Migrate()` .</span><span class="sxs-lookup"><span data-stu-id="1da6c-174">To apply migrations programmatically, call `context.Database.Migrate()`.</span></span> <span data-ttu-id="1da6c-175">Por ejemplo, una aplicación típica de ASP.NET puede hacer lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="1da6c-175">For example, a typical ASP.NET application can do the following:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

<span data-ttu-id="1da6c-176">Tenga en cuenta que `Migrate()` se basa en el `IMigrator` servicio, que se puede usar para escenarios más avanzados.</span><span class="sxs-lookup"><span data-stu-id="1da6c-176">Note that `Migrate()` builds on top of the `IMigrator` service, which can be used for more advanced scenarios.</span></span> <span data-ttu-id="1da6c-177">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` para acceder a él.</span><span class="sxs-lookup"><span data-stu-id="1da6c-177">Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` to access it.</span></span>

> [!WARNING]
>
> * <span data-ttu-id="1da6c-178">Considere atentamente antes de usar este enfoque en producción.</span><span class="sxs-lookup"><span data-stu-id="1da6c-178">Carefully consider before using this approach in production.</span></span> <span data-ttu-id="1da6c-179">La experiencia ha demostrado que la simplicidad de esta estrategia de implementación se ve compensada por los problemas que crea.</span><span class="sxs-lookup"><span data-stu-id="1da6c-179">Experience has shown that the simplicity of this deployment strategy is outweighed by the issues it creates.</span></span> <span data-ttu-id="1da6c-180">Considere la posibilidad de generar scripts SQL a partir de migraciones.</span><span class="sxs-lookup"><span data-stu-id="1da6c-180">Consider generating SQL scripts from migrations instead.</span></span>
> * <span data-ttu-id="1da6c-181">No llame a `EnsureCreated()` antes de `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="1da6c-181">Don't call `EnsureCreated()` before `Migrate()`.</span></span> <span data-ttu-id="1da6c-182">`EnsureCreated()` omite las migraciones para crear el esquema, lo cual provoca un error de `Migrate()`.</span><span class="sxs-lookup"><span data-stu-id="1da6c-182">`EnsureCreated()` bypasses Migrations to create the schema, which causes `Migrate()` to fail.</span></span>
