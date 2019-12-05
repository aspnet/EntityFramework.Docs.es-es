---
title: 'Ingeniería inversa: EF Core'
author: bricelam
ms.author: bricelam
ms.date: 11/13/2018
ms.assetid: 6263EF7D-4989-42E6-BDEE-45DA770342FB
uid: core/managing-schemas/scaffolding
ms.openlocfilehash: 1ba9352d261f1c131b0d70f8cdad2128d9afaefe
ms.sourcegitcommit: 7a709ce4f77134782393aa802df5ab2718714479
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2019
ms.locfileid: "74824467"
---
# <a name="reverse-engineering"></a><span data-ttu-id="b7840-102">Ingeniería inversa</span><span class="sxs-lookup"><span data-stu-id="b7840-102">Reverse Engineering</span></span>

<span data-ttu-id="b7840-103">La ingeniería inversa es el proceso de scaffolding de las clases de tipo de entidad y una clase DbContext basada en un esquema de base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7840-103">Reverse engineering is the process of scaffolding entity type classes and a DbContext class based on a database schema.</span></span> <span data-ttu-id="b7840-104">Puede realizarse mediante el comando `Scaffold-DbContext` de las herramientas de la consola del administrador de paquetes EF Core (PMC) o el comando `dotnet ef dbcontext scaffold` de las herramientas de la interfaz de la línea de comandos (CLI) de .NET.</span><span class="sxs-lookup"><span data-stu-id="b7840-104">It can be performed using the `Scaffold-DbContext` command of the EF Core Package Manager Console (PMC) tools or the `dotnet ef dbcontext scaffold` command of the .NET Command-line Interface (CLI) tools.</span></span>

## <a name="installing"></a><span data-ttu-id="b7840-105">Instalar</span><span class="sxs-lookup"><span data-stu-id="b7840-105">Installing</span></span>

<span data-ttu-id="b7840-106">Antes de la ingeniería inversa, deberá instalar las herramientas de [PMC](xref:core/miscellaneous/cli/powershell) (solo en Visual Studio) o las [herramientas](xref:core/miscellaneous/cli/dotnet)de la CLI.</span><span class="sxs-lookup"><span data-stu-id="b7840-106">Before reverse engineering, you'll need to install either the [PMC tools](xref:core/miscellaneous/cli/powershell) (Visual Studio only) or the [CLI tools](xref:core/miscellaneous/cli/dotnet).</span></span> <span data-ttu-id="b7840-107">Vea los vínculos para obtener más información.</span><span class="sxs-lookup"><span data-stu-id="b7840-107">See links for details.</span></span>

<span data-ttu-id="b7840-108">También necesitará instalar un [proveedor de base de datos](xref:core/providers/index) adecuado para el esquema de la base de datos al que desea aplicar ingeniería inversa.</span><span class="sxs-lookup"><span data-stu-id="b7840-108">You'll also need to install an appropriate [database provider](xref:core/providers/index) for the database schema you want to reverse engineer.</span></span>

## <a name="connection-string"></a><span data-ttu-id="b7840-109">Cadena de conexión</span><span class="sxs-lookup"><span data-stu-id="b7840-109">Connection string</span></span>

<span data-ttu-id="b7840-110">El primer argumento del comando es una cadena de conexión a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7840-110">The first argument to the command is a connection string to the database.</span></span> <span data-ttu-id="b7840-111">Las herramientas usarán esta cadena de conexión para leer el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7840-111">The tools will use this connection string to read the database schema.</span></span>

<span data-ttu-id="b7840-112">La forma de citar y escapar de la cadena de conexión depende del shell que use para ejecutar el comando.</span><span class="sxs-lookup"><span data-stu-id="b7840-112">How you quote and escape the connection string depends on which shell you are using to execute the command.</span></span> <span data-ttu-id="b7840-113">Consulte la documentación de su shell para obtener información específica.</span><span class="sxs-lookup"><span data-stu-id="b7840-113">Refer to your shell's documentation for specifics.</span></span> <span data-ttu-id="b7840-114">Por ejemplo, PowerShell requiere que se escape el carácter `$`, pero no `\`.</span><span class="sxs-lookup"><span data-stu-id="b7840-114">For example, PowerShell requires you to escape the `$` character, but not `\`.</span></span>

``` powershell
Scaffold-DbContext 'Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook' Microsoft.EntityFrameworkCore.SqlServer
```

```dotnetcli
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook" Microsoft.EntityFrameworkCore.SqlServer
```

### <a name="configuration-and-user-secrets"></a><span data-ttu-id="b7840-115">Configuración y secretos de usuario</span><span class="sxs-lookup"><span data-stu-id="b7840-115">Configuration and User Secrets</span></span>

<span data-ttu-id="b7840-116">Si tiene un proyecto de ASP.NET Core, puede usar la sintaxis de `Name=<connection-string>` para leer la cadena de conexión de la configuración.</span><span class="sxs-lookup"><span data-stu-id="b7840-116">If you have an ASP.NET Core project, you can use the `Name=<connection-string>` syntax to read the connection string from configuration.</span></span>

<span data-ttu-id="b7840-117">Esto funciona bien con la [herramienta de administración de secretos](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) para mantener la contraseña de la base de datos separada del código base.</span><span class="sxs-lookup"><span data-stu-id="b7840-117">This works well with the [Secret Manager tool](https://docs.microsoft.com/aspnet/core/security/app-secrets#secret-manager) to keep your database password separate from your codebase.</span></span>

```dotnetcli
dotnet user-secrets set ConnectionStrings.Chinook "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=Chinook"
dotnet ef dbcontext scaffold Name=Chinook Microsoft.EntityFrameworkCore.SqlServer
```

## <a name="provider-name"></a><span data-ttu-id="b7840-118">Nombre de proveedor</span><span class="sxs-lookup"><span data-stu-id="b7840-118">Provider name</span></span>

<span data-ttu-id="b7840-119">El segundo argumento es el nombre del proveedor.</span><span class="sxs-lookup"><span data-stu-id="b7840-119">The second argument is the provider name.</span></span> <span data-ttu-id="b7840-120">El nombre del proveedor suele ser el mismo que el nombre del paquete NuGet del proveedor.</span><span class="sxs-lookup"><span data-stu-id="b7840-120">The provider name is typically the same as the provider's NuGet package name.</span></span>

## <a name="specifying-tables"></a><span data-ttu-id="b7840-121">Especificar tablas</span><span class="sxs-lookup"><span data-stu-id="b7840-121">Specifying tables</span></span>

<span data-ttu-id="b7840-122">De forma predeterminada, se aplica ingeniería inversa a todas las tablas del esquema de la base de datos en tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="b7840-122">All tables in the database schema are reverse engineered into entity types by default.</span></span> <span data-ttu-id="b7840-123">Puede limitar las tablas a las que se aplica ingeniería inversa mediante la especificación de esquemas y tablas.</span><span class="sxs-lookup"><span data-stu-id="b7840-123">You can limit which tables are reverse engineered by specifying schemas and tables.</span></span>

<span data-ttu-id="b7840-124">El parámetro `-Schemas` en PMC y la opción `--schema` de la CLI se pueden usar para incluir todas las tablas de un esquema.</span><span class="sxs-lookup"><span data-stu-id="b7840-124">The `-Schemas` parameter in PMC and the `--schema` option in the CLI can be used to include every table within a schema.</span></span>

<span data-ttu-id="b7840-125">`-Tables` (PMC) y `--table` (CLI) se pueden usar para incluir tablas específicas.</span><span class="sxs-lookup"><span data-stu-id="b7840-125">`-Tables` (PMC) and `--table` (CLI) can be used to include specific tables.</span></span>

<span data-ttu-id="b7840-126">Para incluir varias tablas en PMC, use una matriz.</span><span class="sxs-lookup"><span data-stu-id="b7840-126">To include multiple tables in PMC, use an array.</span></span>

``` powershell
Scaffold-DbContext ... -Tables Artist, Album
```

<span data-ttu-id="b7840-127">Para incluir varias tablas en la CLI, especifique la opción varias veces.</span><span class="sxs-lookup"><span data-stu-id="b7840-127">To include multiple tables in the CLI, specify the option multiple times.</span></span>

```dotnetcli
dotnet ef dbcontext scaffold ... --table Artist --table Album
```

## <a name="preserving-names"></a><span data-ttu-id="b7840-128">Conservar nombres</span><span class="sxs-lookup"><span data-stu-id="b7840-128">Preserving names</span></span>

<span data-ttu-id="b7840-129">Los nombres de tablas y columnas se han corregido para que coincidan mejor con las convenciones de nomenclatura de .NET para tipos y propiedades de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b7840-129">Table and column names are fixed up to better match the .NET naming conventions for types and properties by default.</span></span> <span data-ttu-id="b7840-130">Si se especifica el modificador `-UseDatabaseNames` en PMC o la opción `--use-database-names` de la CLI, se deshabilitará este comportamiento para conservar los nombres de las bases de datos originales lo máximo posible.</span><span class="sxs-lookup"><span data-stu-id="b7840-130">Specifying the `-UseDatabaseNames` switch in PMC or the `--use-database-names` option in the CLI will disable this behavior preserving the original database names as much as possible.</span></span> <span data-ttu-id="b7840-131">Los identificadores de .NET no válidos seguirán siendo fijos y los nombres sintetizados, como las propiedades de navegación, seguirán conforme a las convenciones de nomenclatura de .NET.</span><span class="sxs-lookup"><span data-stu-id="b7840-131">Invalid .NET identifiers will still be fixed and synthesized names like navigation properties will still conform to .NET naming conventions.</span></span>

## <a name="fluent-api-or-data-annotations"></a><span data-ttu-id="b7840-132">Anotaciones de datos o API fluidas</span><span class="sxs-lookup"><span data-stu-id="b7840-132">Fluent API or Data Annotations</span></span>

<span data-ttu-id="b7840-133">Los tipos de entidad se configuran mediante la API fluida de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b7840-133">Entity types are configured using the Fluent API by default.</span></span> <span data-ttu-id="b7840-134">Especifique `-DataAnnotations` (PMC) o `--data-annotations` (CLI) para usar las anotaciones de datos siempre que sea posible.</span><span class="sxs-lookup"><span data-stu-id="b7840-134">Specify `-DataAnnotations` (PMC) or `--data-annotations` (CLI) to instead use data annotations when possible.</span></span>

<span data-ttu-id="b7840-135">Por ejemplo, el uso de la API fluida le aplicará esta técnica:</span><span class="sxs-lookup"><span data-stu-id="b7840-135">For example, using the Fluent API will scaffold this:</span></span>

``` csharp
entity.Property(e => e.Title)
    .IsRequired()
    .HasMaxLength(160);
```

<span data-ttu-id="b7840-136">Aunque el uso de anotaciones de datos es scaffolding:</span><span class="sxs-lookup"><span data-stu-id="b7840-136">While using Data Annotations will scaffold this:</span></span>

``` csharp
[Required]
[StringLength(160)]
public string Title { get; set; }
```

## <a name="dbcontext-name"></a><span data-ttu-id="b7840-137">Nombre de DbContext</span><span class="sxs-lookup"><span data-stu-id="b7840-137">DbContext name</span></span>

<span data-ttu-id="b7840-138">El nombre de la clase DbContext con scaffolding será el nombre de la base de datos con sufijo de *contexto* de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b7840-138">The scaffolded DbContext class name will be the name of the database suffixed with *Context* by default.</span></span> <span data-ttu-id="b7840-139">Para especificar otro, use `-Context` en PMC y `--context` en la CLI.</span><span class="sxs-lookup"><span data-stu-id="b7840-139">To specify a different one, use `-Context` in PMC and `--context` in the CLI.</span></span>

## <a name="directories-and-namespaces"></a><span data-ttu-id="b7840-140">Directorios y espacios de nombres</span><span class="sxs-lookup"><span data-stu-id="b7840-140">Directories and namespaces</span></span>

<span data-ttu-id="b7840-141">Las clases de entidad y una clase DbContext se scaffolding en el directorio raíz del proyecto y usan el espacio de nombres predeterminado del proyecto.</span><span class="sxs-lookup"><span data-stu-id="b7840-141">The entity classes and a DbContext class are scaffolded into the project's root directory and use the project's default namespace.</span></span> <span data-ttu-id="b7840-142">Puede especificar el directorio en el que se van a aplicar las scaffolding mediante `-OutputDir` (PMC) o `--output-dir` (CLI).</span><span class="sxs-lookup"><span data-stu-id="b7840-142">You can specify the directory where classes are scaffolded using `-OutputDir` (PMC) or `--output-dir` (CLI).</span></span> <span data-ttu-id="b7840-143">El espacio de nombres será el espacio de nombres raíz más los nombres de los subdirectorios del directorio raíz del proyecto.</span><span class="sxs-lookup"><span data-stu-id="b7840-143">The namespace will be the root namespace plus the names of any subdirectories under the project's root directory.</span></span>

<span data-ttu-id="b7840-144">También puede usar `-ContextDir` (PMC) y `--context-dir` (CLI) para aplicar scaffolding a la clase DbContext en un directorio independiente de las clases de tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b7840-144">You can also use `-ContextDir` (PMC) and `--context-dir` (CLI) to scaffold the DbContext class into a separate directory from the entity type classes.</span></span>

``` powershell
Scaffold-DbContext ... -ContextDir Data -OutputDir Models
```

```dotnetcli
dotnet ef dbcontext scaffold ... --context-dir Data --output-dir Models
```

## <a name="how-it-works"></a><span data-ttu-id="b7840-145">Cómo funciona</span><span class="sxs-lookup"><span data-stu-id="b7840-145">How it works</span></span>

<span data-ttu-id="b7840-146">La ingeniería inversa comienza leyendo el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7840-146">Reverse engineering starts by reading the database schema.</span></span> <span data-ttu-id="b7840-147">Lee información acerca de las tablas, columnas, restricciones e índices.</span><span class="sxs-lookup"><span data-stu-id="b7840-147">It reads information about tables, columns, constraints, and indexes.</span></span>

<span data-ttu-id="b7840-148">A continuación, usa la información de esquema para crear un modelo de EF Core.</span><span class="sxs-lookup"><span data-stu-id="b7840-148">Next, it uses the schema information to create an EF Core model.</span></span> <span data-ttu-id="b7840-149">Las tablas se usan para crear tipos de entidad. las columnas se usan para crear propiedades; y las claves externas se utilizan para crear relaciones.</span><span class="sxs-lookup"><span data-stu-id="b7840-149">Tables are used to create entity types; columns are used to create properties; and foreign keys are used to create relationships.</span></span>

<span data-ttu-id="b7840-150">Por último, el modelo se usa para generar código.</span><span class="sxs-lookup"><span data-stu-id="b7840-150">Finally, the model is used to generate code.</span></span> <span data-ttu-id="b7840-151">Las clases de tipo de entidad, la API fluida y las anotaciones de datos correspondientes son scaffolding para volver a crear el mismo modelo desde la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b7840-151">The corresponding entity type classes, Fluent API, and data annotations are scaffolded in order to re-create the same model from your app.</span></span>

## <a name="limitations"></a><span data-ttu-id="b7840-152">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="b7840-152">Limitations</span></span>

* <span data-ttu-id="b7840-153">No todo lo relacionado con un modelo se puede representar mediante un esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7840-153">Not everything about a model can be represented using a database schema.</span></span> <span data-ttu-id="b7840-154">Por ejemplo, la información sobre las [**jerarquías de herencia**](../modeling/inheritance.md), los [**tipos de propiedad**](../modeling/owned-entities.md)y la [**División de tablas**](../modeling/table-splitting.md) no están presentes en el esquema de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b7840-154">For example, information about [**inheritance hierarchies**](../modeling/inheritance.md), [**owned types**](../modeling/owned-entities.md), and [**table splitting**](../modeling/table-splitting.md) are not present in the database schema.</span></span> <span data-ttu-id="b7840-155">Por este motivo, estas construcciones nunca se aplicarán a ingeniería inversa.</span><span class="sxs-lookup"><span data-stu-id="b7840-155">Because of this, these constructs will never be reverse engineered.</span></span>
* <span data-ttu-id="b7840-156">Además, es posible que **algunos tipos de columna** no sean compatibles con el proveedor de EF Core.</span><span class="sxs-lookup"><span data-stu-id="b7840-156">In addition, **some column types** may not be supported by the EF Core provider.</span></span> <span data-ttu-id="b7840-157">Estas columnas no se incluirán en el modelo.</span><span class="sxs-lookup"><span data-stu-id="b7840-157">These columns won't be included in the model.</span></span>
* <span data-ttu-id="b7840-158">Puede definir [**tokens de simultaneidad**](../modeling/concurrency.md)en un modelo de EF Core para evitar que dos usuarios actualicen la misma entidad al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="b7840-158">You can define [**concurrency tokens**](../modeling/concurrency.md), in an EF Core model to prevent two users from updating the same entity at the same time.</span></span> <span data-ttu-id="b7840-159">Algunas bases de datos tienen un tipo especial para representar este tipo de columna (por ejemplo, rowversion en SQL Server), en cuyo caso se puede aplicar ingeniería inversa a esta información; sin embargo, no se aplicarán ingeniería inversa a otros tokens de simultaneidad.</span><span class="sxs-lookup"><span data-stu-id="b7840-159">Some databases have a special type to represent this type of column (for example, rowversion in SQL Server) in which case we can reverse engineer this information; however, other concurrency tokens will not be reverse engineered.</span></span>
* <span data-ttu-id="b7840-160">[La C# característica 8 tipos de referencia que aceptan valores NULL](/dotnet/csharp/tutorials/nullable-reference-types) no se admite actualmente en ingeniería inversa: EF Core C# siempre genera código que supone que la característica está deshabilitada.</span><span class="sxs-lookup"><span data-stu-id="b7840-160">[The C# 8 nullable reference type feature](/dotnet/csharp/tutorials/nullable-reference-types) is currently unsupported in reverse engineering: EF Core always generates C# code that assumes the feature is disabled.</span></span> <span data-ttu-id="b7840-161">Por ejemplo, las columnas de texto que aceptan valores NULL se scaffolding como una propiedad con el tipo `string`, no `string?`, con la API fluida o las anotaciones de datos que se usan para configurar si una propiedad es obligatoria o no.</span><span class="sxs-lookup"><span data-stu-id="b7840-161">For example, nullable text columns will be scaffolded as a property with type `string` , not `string?`, with either the Fluent API or Data Annotations used to configure whether a property is required or not.</span></span> <span data-ttu-id="b7840-162">Puede editar el código con scaffolding y reemplazarlo con anotaciones de C# nulabilidad.</span><span class="sxs-lookup"><span data-stu-id="b7840-162">You can edit the scaffolded code and replace these with C# nullability annotations.</span></span> <span data-ttu-id="b7840-163">El seguimiento de la compatibilidad con scaffolding para tipos de referencia que aceptan valores NULL se realiza mediante el problema [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).</span><span class="sxs-lookup"><span data-stu-id="b7840-163">Scaffolding support for nullable reference types is tracked by issue [#15520](https://github.com/aspnet/EntityFrameworkCore/issues/15520).</span></span>

## <a name="customizing-the-model"></a><span data-ttu-id="b7840-164">Personalización del modelo</span><span class="sxs-lookup"><span data-stu-id="b7840-164">Customizing the model</span></span>

<span data-ttu-id="b7840-165">El código generado por EF Core es el código.</span><span class="sxs-lookup"><span data-stu-id="b7840-165">The code generated by EF Core is your code.</span></span> <span data-ttu-id="b7840-166">No dude en cambiarlo.</span><span class="sxs-lookup"><span data-stu-id="b7840-166">Feel free to change it.</span></span> <span data-ttu-id="b7840-167">Solo se regenerará si vuelve a aplicar ingeniería inversa al mismo modelo.</span><span class="sxs-lookup"><span data-stu-id="b7840-167">It will only be regenerated if you reverse engineer the same model again.</span></span> <span data-ttu-id="b7840-168">El código con scaffolding representa *un* modelo que se puede utilizar para tener acceso a la base de datos, pero ciertamente no es el *único* modelo que se puede usar.</span><span class="sxs-lookup"><span data-stu-id="b7840-168">The scaffolded code represents *one* model that can be used to access the database, but it's certainly not the *only* model that can be used.</span></span>

<span data-ttu-id="b7840-169">Personalice las clases de tipo de entidad y la clase DbContext para que se adapte a sus necesidades.</span><span class="sxs-lookup"><span data-stu-id="b7840-169">Customize the entity type classes and DbContext class to fit your needs.</span></span> <span data-ttu-id="b7840-170">Por ejemplo, puede elegir cambiar el nombre de tipos y propiedades, introducir jerarquías de herencia o dividir una tabla en varias entidades.</span><span class="sxs-lookup"><span data-stu-id="b7840-170">For example, you may choose to rename types and properties, introduce inheritance hierarchies, or split a table into to multiple entities.</span></span> <span data-ttu-id="b7840-171">También puede quitar índices no únicos, secuencias sin usar y propiedades de navegación, propiedades escalares opcionales y nombres de restricción del modelo.</span><span class="sxs-lookup"><span data-stu-id="b7840-171">You can also remove non-unique indexes, unused sequences and navigation properties, optional scalar properties, and constraint names from the model.</span></span>

<span data-ttu-id="b7840-172">También puede Agregar constructores, métodos, propiedades, etc. adicionales.</span><span class="sxs-lookup"><span data-stu-id="b7840-172">You can also add additional constructors, methods, properties, etc.</span></span> <span data-ttu-id="b7840-173">usar otra clase parcial en un archivo independiente.</span><span class="sxs-lookup"><span data-stu-id="b7840-173">using another partial class in a separate file.</span></span> <span data-ttu-id="b7840-174">Este enfoque funciona incluso cuando se desea volver a aplicar ingeniería inversa al modelo.</span><span class="sxs-lookup"><span data-stu-id="b7840-174">This approach works even when you intend to reverse engineer the model again.</span></span>

## <a name="updating-the-model"></a><span data-ttu-id="b7840-175">Actualizar el modelo</span><span class="sxs-lookup"><span data-stu-id="b7840-175">Updating the model</span></span>

<span data-ttu-id="b7840-176">Después de realizar cambios en la base de datos, puede que tenga que actualizar el modelo de EF Core para reflejar los cambios.</span><span class="sxs-lookup"><span data-stu-id="b7840-176">After making changes to the database, you may need to update your EF Core model to reflect those changes.</span></span> <span data-ttu-id="b7840-177">Si los cambios en la base de datos son sencillos, puede que sea más fácil realizar los cambios manualmente en el modelo de EF Core.</span><span class="sxs-lookup"><span data-stu-id="b7840-177">If the database changes are simple, it may be easiest just to manually make the changes to your EF Core model.</span></span> <span data-ttu-id="b7840-178">Por ejemplo, cambiar el nombre de una tabla o columna, quitar una columna o actualizar el tipo de una columna son cambios triviales que se deben realizar en el código.</span><span class="sxs-lookup"><span data-stu-id="b7840-178">For example, renaming a table or column, removing a column, or updating a column's type are trivial changes to make in code.</span></span>

<span data-ttu-id="b7840-179">Sin embargo, los cambios más significativos no son tan sencillos como los que se realizan de forma manual.</span><span class="sxs-lookup"><span data-stu-id="b7840-179">More significant changes, however, are not as easy make manually.</span></span> <span data-ttu-id="b7840-180">Un flujo de trabajo común consiste en volver a aplicar ingeniería inversa del modelo de la base de datos mediante `-Force` (PMC) o `--force` (CLI) para sobrescribir el modelo existente con uno actualizado.</span><span class="sxs-lookup"><span data-stu-id="b7840-180">One common workflow is to reverse engineer the model from the database again using `-Force` (PMC) or `--force` (CLI) to overwrite the existing model with an updated one.</span></span>

<span data-ttu-id="b7840-181">Otra característica solicitada comúnmente es la posibilidad de actualizar el modelo de la base de datos a la vez que se conserva la personalización, como cambiar el nombre, las jerarquías de tipos, etc. Use el [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) de problemas para realizar el seguimiento del progreso de esta característica.</span><span class="sxs-lookup"><span data-stu-id="b7840-181">Another commonly requested feature is the ability to update the model from the database while preserving customization like renames, type hierarchies, etc. Use issue [#831](https://github.com/aspnet/EntityFrameworkCore/issues/831) to track the progress of this feature.</span></span>

> [!WARNING]
> <span data-ttu-id="b7840-182">Si vuelve a aplicar ingeniería inversa al modelo desde la base de datos, se perderán los cambios realizados en los archivos.</span><span class="sxs-lookup"><span data-stu-id="b7840-182">If you reverse engineer the model from the database again, any changes you've made to the files will be lost.</span></span>
