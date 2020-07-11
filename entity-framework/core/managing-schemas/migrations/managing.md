---
title: 'Administración de migraciones: EF Core'
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/managing
ms.openlocfilehash: e52d3680360a1e83e05f04650c735c5a67680094
ms.sourcegitcommit: 31536e52b838a84680d2e93e5bb52fb16df72a97
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2020
ms.locfileid: "86238737"
---
# <a name="managing-migrations"></a><span data-ttu-id="0fe5e-102">Administrar migraciones</span><span class="sxs-lookup"><span data-stu-id="0fe5e-102">Managing Migrations</span></span>

<span data-ttu-id="0fe5e-103">A medida que cambia el modelo, las migraciones se agregan y se quitan como parte del desarrollo normal y los archivos de migración se protegen en el control de código fuente del proyecto.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-103">As your model changes, migrations are added and removed as part of normal development, and the migration files are checked into your project's source control.</span></span> <span data-ttu-id="0fe5e-104">Para administrar las migraciones, primero debe instalar las [herramientas de línea de comandos de EF Core](xref:core/miscellaneous/cli/index).</span><span class="sxs-lookup"><span data-stu-id="0fe5e-104">To manage migrations, you must first install the [EF Core command-line tools](xref:core/miscellaneous/cli/index).</span></span>

> [!TIP]
> <span data-ttu-id="0fe5e-105">Si `DbContext` está en un ensamblado diferente al del proyecto de inicio, puede especificar de manera explícita los proyectos de destino e inicio tanto en las [herramientas de la Consola del Administrador de paquetes](xref:core/miscellaneous/cli/powershell#target-and-startup-project) como en las [herramientas de la CLI de .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="0fe5e-105">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="add-a-migration"></a><span data-ttu-id="0fe5e-106">Agregar una migración</span><span class="sxs-lookup"><span data-stu-id="0fe5e-106">Add a migration</span></span>

<span data-ttu-id="0fe5e-107">Una vez cambiado el modelo, puede Agregar una migración para ese cambio:</span><span class="sxs-lookup"><span data-stu-id="0fe5e-107">After your model has been changed, you can add a migration for that change:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0fe5e-108">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0fe5e-108">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

### <a name="visual-studio"></a>[<span data-ttu-id="0fe5e-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0fe5e-109">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration AddBlogCreatedTimestamp
```

***

<span data-ttu-id="0fe5e-110">El nombre de la migración se puede usar como mensaje de confirmación en un sistema de control de versiones.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-110">The migration name can be used like a commit message in a version control system.</span></span> <span data-ttu-id="0fe5e-111">Por ejemplo, puede elegir un nombre como *AddBlogCreatedTimestamp* si el cambio es una nueva `CreatedTimestamp` propiedad en la `Blog` entidad.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-111">For example, you might choose a name like *AddBlogCreatedTimestamp* if the change is a new `CreatedTimestamp` property on your `Blog` entity.</span></span>

<span data-ttu-id="0fe5e-112">Se agregan tres archivos al proyecto en el directorio **Migraciones**:</span><span class="sxs-lookup"><span data-stu-id="0fe5e-112">Three files are added to your project under the **Migrations** directory:</span></span>

* <span data-ttu-id="0fe5e-113">**XXXXXXXXXXXXXX_AddCreatedTimestamp. CS**: el archivo de migraciones principal.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-113">**XXXXXXXXXXXXXX_AddCreatedTimestamp.cs**--The main migrations file.</span></span> <span data-ttu-id="0fe5e-114">Contiene las operaciones necesarias para aplicar la migración (en `Up`) y para revertirla (en `Down`).</span><span class="sxs-lookup"><span data-stu-id="0fe5e-114">Contains the operations necessary to apply the migration (in `Up`) and to revert it (in `Down`).</span></span>
* <span data-ttu-id="0fe5e-115">**XXXXXXXXXXXXXX_AddCreatedTimestamp. Designer. CS**: el archivo de metadatos de migraciones.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-115">**XXXXXXXXXXXXXX_AddCreatedTimestamp.Designer.cs**--The migrations metadata file.</span></span> <span data-ttu-id="0fe5e-116">Contiene información que usa EF.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-116">Contains information used by EF.</span></span>
* <span data-ttu-id="0fe5e-117">**MyContextModelSnapshot.cs**: instantánea del modelo actual.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-117">**MyContextModelSnapshot.cs**--A snapshot of your current model.</span></span> <span data-ttu-id="0fe5e-118">Se usa para determinar qué ha cambiado al agregar la siguiente migración.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-118">Used to determine what changed when adding the next migration.</span></span>

<span data-ttu-id="0fe5e-119">La marca de tiempo del nombre del archivo ayuda a mantenerlos ordenados cronológicamente para que se pueda ver la progresión de cambios.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-119">The timestamp in the filename helps keep them ordered chronologically so you can see the progression of changes.</span></span>

### <a name="namespaces"></a><span data-ttu-id="0fe5e-120">Espacios de nombres</span><span class="sxs-lookup"><span data-stu-id="0fe5e-120">Namespaces</span></span>

<span data-ttu-id="0fe5e-121">Puede mover los archivos de Migraciones y cambiar su espacio de nombres manualmente cuando quiera.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-121">You are free to move Migrations files and change their namespace manually.</span></span> <span data-ttu-id="0fe5e-122">Se crean nuevas migraciones como elementos del mismo nivel de la última migración.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-122">New migrations are created as siblings of the last migration.</span></span> <span data-ttu-id="0fe5e-123">Como alternativa, puede especificar el espacio de nombres en el momento de la generación como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="0fe5e-123">Alternatively, you can specify the namespace at generation time as follows:</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0fe5e-124">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0fe5e-124">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate --namespace Your.Namespace
```

### <a name="visual-studio"></a>[<span data-ttu-id="0fe5e-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0fe5e-125">Visual Studio</span></span>](#tab/vs)

``` powershell
Add-Migration InitialCreate -Namespace Your.Namespace
```

***

## <a name="customize-migration-code"></a><span data-ttu-id="0fe5e-126">Personalizar el código de migración</span><span class="sxs-lookup"><span data-stu-id="0fe5e-126">Customize migration code</span></span>

<span data-ttu-id="0fe5e-127">Aunque en general EF Core crea migraciones precisas, siempre debe revisar el código y asegurarse de que se corresponde con el cambio deseado; en algunos casos, incluso es necesario hacerlo.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-127">While EF Core generally creates accurate migrations, you should always review the code and make sure it corresponds to the desired change; in some cases, it is even necessary to do so.</span></span>

### <a name="column-renames"></a><span data-ttu-id="0fe5e-128">Cambiar nombre de columna</span><span class="sxs-lookup"><span data-stu-id="0fe5e-128">Column renames</span></span>

<span data-ttu-id="0fe5e-129">Un ejemplo importante donde se requiere la personalización de las migraciones es al cambiar el nombre de una propiedad.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-129">One notable example where customizing migrations is required is when renaming a property.</span></span> <span data-ttu-id="0fe5e-130">Por ejemplo, si cambia el nombre de una propiedad de `Name` a `FullName` , EF Core generará la siguiente migración:</span><span class="sxs-lookup"><span data-stu-id="0fe5e-130">For example, if you rename a property from `Name` to `FullName`, EF Core will generate the following migration:</span></span>

```c#
migrationBuilder.DropColumn(
    name: "Name",
    table: "Customers");

migrationBuilder.AddColumn<string>(
    name: "FullName",
    table: "Customers",
    nullable: true);
```

<span data-ttu-id="0fe5e-131">Por lo general, EF Core no puede saber si la intención es quitar una columna y crear una nueva (dos cambios independientes) y cuándo debe cambiarse el nombre de una columna.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-131">EF Core is generally unable to know when the intention is to drop a column and create a new one (two separate changes), and when a column should be renamed.</span></span> <span data-ttu-id="0fe5e-132">Si la migración anterior se aplica tal cual, se perderán todos los nombres de cliente.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-132">If the above migration is applied as-is, all your customer names will be lost.</span></span> <span data-ttu-id="0fe5e-133">Para cambiar el nombre de una columna, reemplace la migración anterior generada por lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="0fe5e-133">To rename a column, replace the above generated migration with the following:</span></span>

```c#
migrationBuilder.RenameColumn(
    name: "Name",
    table: "Customers",
    newName: "FullName");
```

> [!TIP]
> <span data-ttu-id="0fe5e-134">El proceso de scaffolding de la migración advierte si una operación puede ocasionar una pérdida de datos (como el borrado de una columna).</span><span class="sxs-lookup"><span data-stu-id="0fe5e-134">The migration scaffolding process warns when an operation might result in data loss (like dropping a column).</span></span> <span data-ttu-id="0fe5e-135">Si aparece dicha advertencia, asegúrese especialmente de revisar el código de las migraciones para mayor precisión.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-135">If you see that warning, be especially sure to review the migrations code for accuracy.</span></span>

### <a name="adding-raw-sql"></a><span data-ttu-id="0fe5e-136">Agregar SQL sin procesar</span><span class="sxs-lookup"><span data-stu-id="0fe5e-136">Adding raw SQL</span></span>

<span data-ttu-id="0fe5e-137">Aunque el cambio de nombre de una columna se puede lograr a través de una API integrada, en muchos casos no es posible.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-137">While renaming a column can be achieved via a built-in API, in many cases that is not possible.</span></span> <span data-ttu-id="0fe5e-138">Por ejemplo, es posible que desee reemplazar `FirstName` `LastColumn` las propiedades y existentes por una sola `FullName` propiedad nueva.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-138">For example, we may want to replace existing `FirstName` and `LastColumn` properties with a single, new `FullName` property.</span></span> <span data-ttu-id="0fe5e-139">La migración generada por EF Core será la siguiente:</span><span class="sxs-lookup"><span data-stu-id="0fe5e-139">The migration generated by EF Core will be the following:</span></span>

``` csharp
migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");

migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);
```

<span data-ttu-id="0fe5e-140">Como antes, esto provocaría una pérdida de datos no deseada.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-140">As before, this would cause unwanted data loss.</span></span> <span data-ttu-id="0fe5e-141">Para transferir los datos de las columnas anteriores, se reorganizan las migraciones y se introduce una operación SQL sin procesar de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="0fe5e-141">To transfer the data from the old columns, we rearrange the migrations and introduce a raw SQL operation as follows:</span></span>

``` csharp
migrationBuilder.AddColumn<string>(
    name: "Name",
    table: "Customer",
    nullable: true);

migrationBuilder.Sql(
@"
    UPDATE Customer
    SET Name = FirstName + ' ' + LastName;
");

migrationBuilder.DropColumn(
    name: "FirstName",
    table: "Customer");

migrationBuilder.DropColumn(
    name: "LastName",
    table: "Customer");
```

### <a name="arbitrary-changes-via-raw-sql"></a><span data-ttu-id="0fe5e-142">Cambios arbitrarios a través de SQL sin formato</span><span class="sxs-lookup"><span data-stu-id="0fe5e-142">Arbitrary changes via raw SQL</span></span>

<span data-ttu-id="0fe5e-143">SQL sin formato también se puede usar para administrar objetos de base de datos que EF Core no tienen en cuenta.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-143">Raw SQL can also be used to manage database objects that EF Core isn't aware of.</span></span> <span data-ttu-id="0fe5e-144">Para ello, agregue una migración sin realizar ningún cambio en el modelo; se generará una migración vacía, que se puede rellenar con operaciones SQL sin procesar.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-144">To do this, add a migration without making any model change; an empty migration will be generated, which you can then populate with raw SQL operations.</span></span>

<span data-ttu-id="0fe5e-145">Por ejemplo, la siguiente migración crea un SQL Server procedimiento almacenado:</span><span class="sxs-lookup"><span data-stu-id="0fe5e-145">For example, the following migration creates a SQL Server stored procedure:</span></span>

```c#
migrationBuilder.Sql(
@"
    EXEC ('CREATE PROCEDURE getFullName
        @LastName nvarchar(50),
        @FirstName nvarchar(50)
    AS
        RETURN @LastName + @FirstName;')");
```

<span data-ttu-id="0fe5e-146">Se puede usar para administrar cualquier aspecto de la base de datos, incluidos:</span><span class="sxs-lookup"><span data-stu-id="0fe5e-146">This can be used to manage any aspect of your database, including:</span></span>

* <span data-ttu-id="0fe5e-147">Procedimientos almacenados</span><span class="sxs-lookup"><span data-stu-id="0fe5e-147">Stored procedures</span></span>
* <span data-ttu-id="0fe5e-148">Búsqueda de texto completo</span><span class="sxs-lookup"><span data-stu-id="0fe5e-148">Full-Text Search</span></span>
* <span data-ttu-id="0fe5e-149">Funciones</span><span class="sxs-lookup"><span data-stu-id="0fe5e-149">Functions</span></span>
* <span data-ttu-id="0fe5e-150">Desencadenadores</span><span class="sxs-lookup"><span data-stu-id="0fe5e-150">Triggers</span></span>
* <span data-ttu-id="0fe5e-151">Vistas</span><span class="sxs-lookup"><span data-stu-id="0fe5e-151">Views</span></span>

<span data-ttu-id="0fe5e-152">En la mayoría de los casos, EF Core ajustará automáticamente cada migración en su propia transacción al aplicar las migraciones.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-152">In most cases, EF Core will automatically wrap each migration in its own transaction when applying migrations.</span></span> <span data-ttu-id="0fe5e-153">Desafortunadamente, algunas operaciones de migración no se pueden realizar en una transacción en algunas bases de datos. en estos casos, puede rechazar la transacción pasando `suppressTransaction: true` a `migrationBuilder.Sql` .</span><span class="sxs-lookup"><span data-stu-id="0fe5e-153">Unfortunately, some migrations operations cannot be performed within a transaction in some databases; for these cases, you may opt out of the transaction by passing `suppressTransaction: true` to `migrationBuilder.Sql`.</span></span>

<span data-ttu-id="0fe5e-154">Si `DbContext` está en un ensamblado diferente al del proyecto de inicio, puede especificar de manera explícita los proyectos de destino e inicio tanto en las [herramientas de la Consola del Administrador de paquetes](xref:core/miscellaneous/cli/powershell#target-and-startup-project) como en las [herramientas de la CLI de .NET Core](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span><span class="sxs-lookup"><span data-stu-id="0fe5e-154">If the `DbContext` is in a different assembly than the startup project, you can explicitly specify the target and startup projects in either the [Package Manager Console tools](xref:core/miscellaneous/cli/powershell#target-and-startup-project) or the [.NET Core CLI tools](xref:core/miscellaneous/cli/dotnet#target-project-and-startup-project).</span></span>

## <a name="remove-a-migration"></a><span data-ttu-id="0fe5e-155">Quitar una migración</span><span class="sxs-lookup"><span data-stu-id="0fe5e-155">Remove a migration</span></span>

<span data-ttu-id="0fe5e-156">A veces uno agrega una migración y se da cuenta de que debe realizar cambios adicionales en el modelo de EF Core antes de aplicarla.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-156">Sometimes you add a migration and realize you need to make additional changes to your EF Core model before applying it.</span></span> <span data-ttu-id="0fe5e-157">Para quitar la última migración, use este comando.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-157">To remove the last migration, use this command.</span></span>

### <a name="net-core-cli"></a>[<span data-ttu-id="0fe5e-158">CLI de .NET Core</span><span class="sxs-lookup"><span data-stu-id="0fe5e-158">.NET Core CLI</span></span>](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations remove
```

### <a name="visual-studio"></a>[<span data-ttu-id="0fe5e-159">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0fe5e-159">Visual Studio</span></span>](#tab/vs)

``` powershell
Remove-Migration
```

***

<span data-ttu-id="0fe5e-160">Después de quitar la migración, puede realizar los cambios de modelo adicionales y volver a agregarla.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-160">After removing the migration, you can make the additional model changes and add it again.</span></span>

> [!WARNING]
> <span data-ttu-id="0fe5e-161">Tenga cuidado de no quitar las migraciones que ya se hayan aplicado a las bases de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-161">Take care not to remove any migrations which are already applied to production databases.</span></span> <span data-ttu-id="0fe5e-162">Si no lo hace, impedirá que pueda revertirla y puede romper las suposiciones realizadas por migraciones posteriores.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-162">Not doing so will prevent you from being able to revert it, and may break the assumptions made by subsequent migrations.</span></span>

## <a name="listing-migrations"></a><span data-ttu-id="0fe5e-163">Enumerar migraciones</span><span class="sxs-lookup"><span data-stu-id="0fe5e-163">Listing migrations</span></span>

<span data-ttu-id="0fe5e-164">Puede enumerar todas las migraciones existentes como se indica a continuación:</span><span class="sxs-lookup"><span data-stu-id="0fe5e-164">You can list all existing migrations as follows:</span></span>

```dotnetcli
dotnet ef migrations list
```

## <a name="resetting-all-migrations"></a><span data-ttu-id="0fe5e-165">Restableciendo todas las migraciones</span><span class="sxs-lookup"><span data-stu-id="0fe5e-165">Resetting all migrations</span></span>

<span data-ttu-id="0fe5e-166">En algunos casos extremos, puede que sea necesario quitar todas las migraciones y empezar de nuevo.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-166">In some extreme cases, it may be necessary to remove all migrations and start over.</span></span> <span data-ttu-id="0fe5e-167">Esto se puede hacer fácilmente si se elimina la carpeta **Migrations** y se quita la base de datos. en ese momento, puede crear una nueva migración inicial, que contendrá todo el esquema actual.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-167">This can be easily done by deleting your **Migrations** folder and dropping your database; at that point you can create a new initial migration, which will contain you entire current schema.</span></span>

<span data-ttu-id="0fe5e-168">También es posible restablecer todas las migraciones y crear una sola sin perder los datos.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-168">It's also possible to reset all migrations and create a single one without losing your data.</span></span> <span data-ttu-id="0fe5e-169">A veces, esto se denomina "" "en" ", y implica algún trabajo manual:</span><span class="sxs-lookup"><span data-stu-id="0fe5e-169">This is sometimes called "squashing", and involves some manual work:</span></span>

* <span data-ttu-id="0fe5e-170">Eliminación de la carpeta **Migrations**</span><span class="sxs-lookup"><span data-stu-id="0fe5e-170">Delete your **Migrations** folder</span></span>
* <span data-ttu-id="0fe5e-171">Cree una nueva migración y genere un script SQL para ella.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-171">Create a new migration and generate a SQL script for it</span></span>
* <span data-ttu-id="0fe5e-172">En la base de datos, elimine todas las filas de la tabla de historial de migraciones</span><span class="sxs-lookup"><span data-stu-id="0fe5e-172">In your database, delete all rows from the migrations history table</span></span>
* <span data-ttu-id="0fe5e-173">Inserte una sola fila en el historial de migraciones para registrar que la primera migración ya se ha aplicado, ya que las tablas ya están allí.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-173">Insert a single row into the migrations history, to record that the first migration has already been applied, since your tables are already there.</span></span> <span data-ttu-id="0fe5e-174">La instrucción INSERT SEQL es la última operación del script SQL que se generó anteriormente.</span><span class="sxs-lookup"><span data-stu-id="0fe5e-174">The insert SEQL is the last operation in the SQL script generated above.</span></span>
