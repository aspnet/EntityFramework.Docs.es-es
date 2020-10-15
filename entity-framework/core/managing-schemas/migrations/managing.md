---
title: 'Administración de migraciones: EF Core'
description: Agregar, quitar y administrar de otro modo migraciones de esquemas de base de datos con Entity Framework Core
author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: fdfda6f3dea306fbbc343c1be3f4d5754d1f65c4
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062066"
---
# <a name="managing-migrations"></a><span data-ttu-id="becba-103">Administrar migraciones</span><span class="sxs-lookup"><span data-stu-id="becba-103">Managing Migrations</span></span>

<span data-ttu-id="becba-104">A medida que cambia el modelo, las migraciones se agregan y se quitan como parte del desarrollo normal y los archivos de migración se protegen en el control de código fuente del proyecto.</span><span class="sxs-lookup"><span data-stu-id="becba-104">As your model changes, migrations are added and removed as part of normal development, and the migration files are checked into your project's source control.</span></span> <span data-ttu-id="becba-105">Para administrar las migraciones, primero debe instalar las [herramientas de línea de comandos de EF Core](xref:core/miscellaneous/cli/index).</span><span class="sxs-lookup"><span data-stu-id="becba-105">To manage migrations, you must first install the [EF Core command-line tools](xref:core/miscellaneous/cli/index).</span></span>

> [!TIP]
> <span data-ttu-id="becba-106">Si `DbContext` está en un ensamblado diferente al del proyecto de inicio, puede especificar de manera explícita los proyectos de destino e inicio tanto en las [herramientas de la Consola del Administrador de paquetes](xref:core/miscellaneous/cli/powershell#target-and-startup-project) como en las [herramientas de la CLI de .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="becba-106">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="becba-107">Agregar una migración</span><span class="sxs-lookup"><span data-stu-id="becba-107">Add a migration</span></span>

<span data-ttu-id="becba-108">Una vez cambiado el modelo, puede Agregar una migración para ese cambio:</span><span class="sxs-lookup"><span data-stu-id="becba-108">After your model has been changed, you can add a migration for that change:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="becba-109">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="becba-109">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[<span data-ttu-id="becba-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="becba-110">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="becba-111">El nombre de la migración se puede usar como mensaje de confirmación en un sistema de control de versiones.</span><span class="sxs-lookup"><span data-stu-id="becba-111">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="becba-112">Por ejemplo, puede elegir un nombre como *AddBlogCreatedTimestamp* si el cambio es una nueva `CreatedTimestamp` propiedad en la `Blog` entidad.</span><span class="sxs-lookup"><span data-stu-id="becba-112">For example, you might choose a name like *AddBlogCreatedTimestamp* if the change is a new `CreatedTimestamp` property on your `Blog` entity.</span></span>

<span data-ttu-id="becba-113">Se agregan tres archivos al proyecto en el directorio **Migraciones**:</span><span class="sxs-lookup"><span data-stu-id="becba-113">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="becba-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp. CS**: el archivo de migraciones principal.</span><span class="sxs-lookup"><span data-stu-id="becba-114">**XXXXXXXXXXXXXX_AddCreatedTimestamp.cs**--The main migrations file.</span></span> <span data-ttu-id="becba-115">Contiene las operaciones necesarias para aplicar la migración (en `Up`) y para revertirla (en `Down`).</span><span class="sxs-lookup"><span data-stu-id="becba-115">Contains the operations necessary to apply the migration (in `Up`) and to revert it (in `Down`).</span></span>
* <span data-ttu-id="becba-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp. Designer. CS**: el archivo de metadatos de migraciones.</span><span class="sxs-lookup"><span data-stu-id="becba-116">**XXXXXXXXXXXXXX_AddCreatedTimestamp.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="becba-117">Contiene información que usa EF.</span><span class="sxs-lookup"><span data-stu-id="becba-117">Contains information used by EF.</span></span>
* <span data-ttu-id="becba-118">**MyContextModelSnapshot.cs**: instantánea del modelo actual.</span><span class="sxs-lookup"><span data-stu-id="becba-118">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="becba-119">Se usa para determinar qué ha cambiado al agregar la siguiente migración.</span><span class="sxs-lookup"><span data-stu-id="becba-119">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="becba-120">La marca de tiempo del nombre del archivo ayuda a mantenerlos ordenados cronológicamente para que se pueda ver la progresión de cambios.</span><span class="sxs-lookup"><span data-stu-id="becba-120">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="becba-121">Espacios de nombres</span><span class="sxs-lookup"><span data-stu-id="becba-121">Namespaces</span></span>

<span data-ttu-id="becba-122">Puede mover los archivos de Migraciones y cambiar su espacio de nombres manualmente cuando quiera.</span><span class="sxs-lookup"><span data-stu-id="becba-122">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="becba-123">Se crean nuevas migraciones como elementos del mismo nivel de la última migración.</span><span class="sxs-lookup"><span data-stu-id="becba-123">New migrations are created as siblings of the last migration.</span></span> <span data-ttu-id="becba-124">Como alternativa, puede especificar el espacio de nombres en el momento de la generación como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="becba-124">Alternatively, you can specify the namespace at generation time as follows:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="becba-125">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="becba-125">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="becba-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="becba-126">Visual Studio</span></span>](#tab/vs)

```powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="customize-migration-code"></a><span data-ttu-id="becba-127">Personalizar el código de migración</span><span class="sxs-lookup"><span data-stu-id="becba-127">Customize migration code</span></span>

<span data-ttu-id="becba-128">Aunque en general EF Core crea migraciones precisas, siempre debe revisar el código y asegurarse de que se corresponde con el cambio deseado; en algunos casos, incluso es necesario hacerlo.</span><span class="sxs-lookup"><span data-stu-id="becba-128">While EF Core generally creates accurate migrations, you should always review the code and make sure it corresponds to the desired change; in some cases, it is even necessary to do so.</span></span>

### <a name="column-renames"></a><span data-ttu-id="becba-129">Cambiar nombre de columna</span><span class="sxs-lookup"><span data-stu-id="becba-129">Column renames</span></span>

<span data-ttu-id="becba-130">Un ejemplo importante donde se requiere la personalización de las migraciones es al cambiar el nombre de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="becba-130">One notable example where customizing migrations is required is when renaming a property.</span></span> <span data-ttu-id="becba-131">Por ejemplo, si cambia el nombre de una propiedad de `Name` a `FullName` , EF Core generará la siguiente migración:</span><span class="sxs-lookup"><span data-stu-id="becba-131">For example, if you rename a property from `Name` to `FullName`, EF Core will generate the following migration:</span></span>

```csharp
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

<span data-ttu-id="becba-132">Por lo general, EF Core no puede saber si la intención es quitar una columna y crear una nueva (dos cambios independientes) y cuándo debe cambiarse el nombre de una columna.</span><span class="sxs-lookup"><span data-stu-id="becba-132">EF Core is generally unable to know when the intention is to drop a column and create a new one (two separate changes), and when a column should be renamed.</span></span> <span data-ttu-id="becba-133">Si la migración anterior se aplica tal cual, se perderán todos los nombres de cliente.</span><span class="sxs-lookup"><span data-stu-id="becba-133">If the above migration is applied as-is, all your customer names will be lost.</span></span> <span data-ttu-id="becba-134">Para cambiar el nombre de una columna, reemplace la migración anterior generada por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="becba-134">To rename a column, replace the above generated migration with the following:</span></span>

```csharp
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> <span data-ttu-id="becba-135">El proceso de scaffolding de la migración advierte si una operación puede ocasionar una pérdida de datos (como el borrado de una columna).</span><span class="sxs-lookup"><span data-stu-id="becba-135">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="becba-136">Si aparece dicha advertencia, asegúrese especialmente de revisar el código de las migraciones para mayor precisión.</span><span class="sxs-lookup"><span data-stu-id="becba-136">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

### <a name="adding-raw-sql"></a><span data-ttu-id="becba-137">Agregar SQL sin procesar</span><span class="sxs-lookup"><span data-stu-id="becba-137">Adding raw SQL</span></span>

<span data-ttu-id="becba-138">Aunque el cambio de nombre de una columna se puede lograr a través de una API integrada, en muchos casos no es posible.</span><span class="sxs-lookup"><span data-stu-id="becba-138">While renaming a column can be achieved via a built-in API, in many cases that is not possible.</span></span> <span data-ttu-id="becba-139">Por ejemplo, es posible que desee reemplazar `FirstName` `LastName` las propiedades y existentes por una sola `FullName` propiedad nueva.</span><span class="sxs-lookup"><span data-stu-id="becba-139">For example, we may want to replace existing `FirstName` and `LastName` properties with a single, new `FullName` property.</span></span> <span data-ttu-id="becba-140">La migración generada por EF Core será la siguiente:</span><span class="sxs-lookup"><span data-stu-id="becba-140">The migration generated by EF Core will be the following:</span></span>

```csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);
```

<span data-ttu-id="becba-141">Como antes, esto provocaría una pérdida de datos no deseada.</span><span class="sxs-lookup"><span data-stu-id="becba-141">As before, this would cause unwanted data loss.</span></span> <span data-ttu-id="becba-142">Para transferir los datos de las columnas anteriores, se reorganizan las migraciones y se introduce una operación SQL sin procesar de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="becba-142">To transfer the data from the old columns, we rearrange the migrations and introduce a raw SQL operation as follows:</span></span>

```csharp
migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET FullName = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a><span data-ttu-id="becba-143">Cambios arbitrarios a través de SQL sin formato</span><span class="sxs-lookup"><span data-stu-id="becba-143">Arbitrary changes via raw SQL</span></span>

<span data-ttu-id="becba-144">SQL sin formato también se puede usar para administrar objetos de base de datos que EF Core no tienen en cuenta.</span><span class="sxs-lookup"><span data-stu-id="becba-144">Raw SQL can also be used to manage database objects that EF Core isn't aware of.</span></span> <span data-ttu-id="becba-145">Para ello, agregue una migración sin realizar ningún cambio en el modelo; se generará una migración vacía, que se puede rellenar con operaciones SQL sin procesar.</span><span class="sxs-lookup"><span data-stu-id="becba-145">To do this, add a migration without making any model change; an empty migration will be generated, which you can then populate with raw SQL operations.</span></span>

<span data-ttu-id="becba-146">Por ejemplo, la siguiente migración crea un SQL Server procedimiento almacenado:</span><span class="sxs-lookup"><span data-stu-id="becba-146">For example, the following migration creates a SQL Server stored procedure:</span></span>

```csharp
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

<span data-ttu-id="becba-147">Se puede usar para administrar cualquier aspecto de la base de datos, incluidos:</span><span class="sxs-lookup"><span data-stu-id="becba-147">This can be used to manage any aspect of your database, including:</span></span>

* <span data-ttu-id="becba-148">Procedimientos almacenados</span><span class="sxs-lookup"><span data-stu-id="becba-148">Stored procedures</span></span>
* <span data-ttu-id="becba-149">Búsqueda de texto completo</span><span class="sxs-lookup"><span data-stu-id="becba-149">Full-Text Search</span></span>
* <span data-ttu-id="becba-150">Funciones</span><span class="sxs-lookup"><span data-stu-id="becba-150">Functions</span></span>
* <span data-ttu-id="becba-151">Desencadenadores</span><span class="sxs-lookup"><span data-stu-id="becba-151">Triggers</span></span>
* <span data-ttu-id="becba-152">Vistas</span><span class="sxs-lookup"><span data-stu-id="becba-152">Views</span></span>

<span data-ttu-id="becba-153">En la mayoría de los casos, EF Core ajustará automáticamente cada migración en su propia transacción al aplicar las migraciones.</span><span class="sxs-lookup"><span data-stu-id="becba-153">In most cases, EF Core will automatically wrap each migration in its own transaction when applying migrations.</span></span> <span data-ttu-id="becba-154">Desafortunadamente, algunas operaciones de migración no se pueden realizar en una transacción en algunas bases de datos. en estos casos, puede rechazar la transacción pasando `suppressTransaction: true` a `migrationBuilder.Sql` .</span><span class="sxs-lookup"><span data-stu-id="becba-154">Unfortunately, some migrations operations cannot be performed within a transaction in some databases; for these cases, you may opt out of the transaction by passing `suppressTransaction: true` to `migrationBuilder.Sql`.</span></span>

<span data-ttu-id="becba-155">Si `DbContext` está en un ensamblado diferente al del proyecto de inicio, puede especificar de manera explícita los proyectos de destino e inicio tanto en las [herramientas de la Consola del Administrador de paquetes](xref:core/miscellaneous/cli/powershell#target-and-startup-project) como en las [herramientas de la CLI de .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="becba-155">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="becba-156">Quitar una migración</span><span class="sxs-lookup"><span data-stu-id="becba-156">Remove a migration</span></span>

<span data-ttu-id="becba-157">A veces uno agrega una migración y se da cuenta de que debe realizar cambios adicionales en el modelo de EF Core antes de aplicarla.</span><span class="sxs-lookup"><span data-stu-id="becba-157">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="becba-158">Para quitar la última migración, use este comando.</span><span class="sxs-lookup"><span data-stu-id="becba-158">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="becba-159">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="becba-159">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="becba-160">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="becba-160">Visual Studio</span></span>](#tab/vs)

```powershell
Remove-Migration
```

***

<span data-ttu-id="becba-161">Después de quitar la migración, puede realizar los cambios de modelo adicionales y volver a agregarla.</span><span class="sxs-lookup"><span data-stu-id="becba-161">After removing the migration, you can make the additional model changes and add it again.</span></span>

> [!WARNING]
> <span data-ttu-id="becba-162">Tenga cuidado de no quitar las migraciones que ya se hayan aplicado a las bases de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="becba-162">Take care not to remove any migrations which are already applied to production databases.</span></span> <span data-ttu-id="becba-163">Si no lo hace, impedirá que pueda revertirla y puede romper las suposiciones realizadas por migraciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="becba-163">Not doing so will prevent you from being able to revert it, and may break the assumptions made by subsequent migrations.</span></span>

## <a name="listing-migrations"></a><span data-ttu-id="becba-164">Enumerar migraciones</span><span class="sxs-lookup"><span data-stu-id="becba-164">Listing migrations</span></span>

<span data-ttu-id="becba-165">Puede enumerar todas las migraciones existentes como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="becba-165">You can list all existing migrations as follows:</span></span>

```dotnetcli
dotnet ef migrations list
```

## <a name="resetting-all-migrations"></a><span data-ttu-id="becba-166">Restableciendo todas las migraciones</span><span class="sxs-lookup"><span data-stu-id="becba-166">Resetting all migrations</span></span>

<span data-ttu-id="becba-167">En algunos casos extremos, puede que sea necesario quitar todas las migraciones y empezar de nuevo.</span><span class="sxs-lookup"><span data-stu-id="becba-167">In some extreme cases, it may be necessary to remove all migrations and start over.</span></span> <span data-ttu-id="becba-168">Esto se puede hacer fácilmente si se elimina la carpeta **Migrations** y se quita la base de datos. en ese momento, puede crear una nueva migración inicial, que contendrá todo el esquema actual.</span><span class="sxs-lookup"><span data-stu-id="becba-168">This can be easily done by deleting your **Migrations** folder and dropping your database; at that point you can create a new initial migration, which will contain you entire current schema.</span></span>

<span data-ttu-id="becba-169">También es posible restablecer todas las migraciones y crear una sola sin perder los datos.</span><span class="sxs-lookup"><span data-stu-id="becba-169">It's also possible to reset all migrations and create a single one without losing your data.</span></span> <span data-ttu-id="becba-170">A veces, esto se denomina "" "en" ", y implica algún trabajo manual:</span><span class="sxs-lookup"><span data-stu-id="becba-170">This is sometimes called "squashing", and involves some manual work:</span></span>

* <span data-ttu-id="becba-171">Eliminación de la carpeta **Migrations**</span><span class="sxs-lookup"><span data-stu-id="becba-171">Delete your **Migrations** folder</span></span>
* <span data-ttu-id="becba-172">Cree una nueva migración y genere un script SQL para ella.</span><span class="sxs-lookup"><span data-stu-id="becba-172">Create a new migration and generate a SQL script for it</span></span>
* <span data-ttu-id="becba-173">En la base de datos, elimine todas las filas de la tabla de historial de migraciones</span><span class="sxs-lookup"><span data-stu-id="becba-173">In your database, delete all rows from the migrations history table</span></span>
* <span data-ttu-id="becba-174">Inserte una sola fila en el historial de migraciones para registrar que la primera migración ya se ha aplicado, ya que las tablas ya están allí.</span><span class="sxs-lookup"><span data-stu-id="becba-174">Insert a single row into the migrations history, to record that the first migration has already been applied, since your tables are already there.</span></span> <span data-ttu-id="becba-175">La instrucción INSERT SQL es la última operación del script SQL que se generó anteriormente.</span><span class="sxs-lookup"><span data-stu-id="becba-175">The insert SQL is the last operation in the SQL script generated above.</span></span>

> [!WARNING]
> <span data-ttu-id="becba-176">Cualquier [código de migración personalizado](#customize-migration-code) se perderá cuando se elimine la carpeta **Migrations** .</span><span class="sxs-lookup"><span data-stu-id="becba-176">Any [custom migration code](#customize-migration-code) will be lost when the **Migrations** folder is deleted.</span></span>  <span data-ttu-id="becba-177">Las personalizaciones deben aplicarse a la nueva migración inicial manualmente para que se conserven.</span><span class="sxs-lookup"><span data-stu-id="becba-177">Any customizations must be applied to the new initial migration manually in order to be preserved.</span></span>
