---
title: Herramientas y extensiones - EF Core
description: Herramientas y extensiones externas para Entity Framework Core
author: ErikEJ
ms.date: 01/06/2021
uid: core/extensions/index
ms.openlocfilehash: 1198cd586902cd6222a94225056d076c847c9197
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129023"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="b6765-103">Herramientas y extensiones de EF Core</span><span class="sxs-lookup"><span data-stu-id="b6765-103">EF Core Tools & Extensions</span></span>

<span data-ttu-id="b6765-104">Estas herramientas y extensiones proporcionan más funcionalidades para Entity Framework Core 2.1 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="b6765-104">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b6765-105">Las extensiones están compiladas a partir de una gran variedad de orígenes, por lo que su mantenimiento no está incluido en el proyecto Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b6765-105">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="b6765-106">En lo que respecta a las extensiones de terceros y para asegurarse de que cumplan sus requisitos, no se olvide de evaluar la calidad, las licencias, la compatibilidad, el soporte técnico, etc.</span><span class="sxs-lookup"><span data-stu-id="b6765-106">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="b6765-107">En concreto, es posible que sea necesario actualizar una extensión compilada para una versión anterior de EF Core para que funcione con las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="b6765-107">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="b6765-108">Herramientas</span><span class="sxs-lookup"><span data-stu-id="b6765-108">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="b6765-109">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="b6765-109">LLBLGen Pro</span></span>

<span data-ttu-id="b6765-110">LLBLGen Pro es una solución de modelado de entidad compatible con Entity Framework y Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b6765-110">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="b6765-111">Permite definir fácilmente el modelo de entidad y asignarlo a la base de datos mediante Database First o Model First, de modo que pueda empezar a escribir consultas de inmediato.</span><span class="sxs-lookup"><span data-stu-id="b6765-111">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="b6765-112">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-112">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="b6765-113">Sitio web</span><span class="sxs-lookup"><span data-stu-id="b6765-113">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="b6765-114">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="b6765-114">Devart Entity Developer</span></span>

<span data-ttu-id="b6765-115">Entity Developer es un potente diseñador O/RM para ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access y LINQ to SQL.</span><span class="sxs-lookup"><span data-stu-id="b6765-115">Entity Developer is a powerful O/RM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="b6765-116">Permite diseñar modelos EF Core de forma visual mediante los enfoques Database First y Model First, así como generar código C# y de Visual Basic.</span><span class="sxs-lookup"><span data-stu-id="b6765-116">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="b6765-117">Para EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="b6765-117">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="b6765-118">Sitio web</span><span class="sxs-lookup"><span data-stu-id="b6765-118">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="b6765-119">nHydrate ORM para Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b6765-119">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="b6765-120">O/RM que crea clases extensibles y fuertemente tipadas para Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="b6765-120">An O/RM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="b6765-121">El código generado es Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="b6765-121">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="b6765-122">No hay ninguna diferencia.</span><span class="sxs-lookup"><span data-stu-id="b6765-122">There is no difference.</span></span> <span data-ttu-id="b6765-123">Esto no es un reemplazo de EF ni un O/RM personalizado.</span><span class="sxs-lookup"><span data-stu-id="b6765-123">This is not a replacement for EF or a custom O/RM.</span></span> <span data-ttu-id="b6765-124">Es una capa de modelado visual que permite a un equipo administrar esquemas de base de datos complejos.</span><span class="sxs-lookup"><span data-stu-id="b6765-124">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="b6765-125">Funciona bien con software SCM como GIT, lo que permite el acceso de varios usuarios al modelo con conflictos mínimos.</span><span class="sxs-lookup"><span data-stu-id="b6765-125">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="b6765-126">El instalador realiza un seguimiento de los cambios del modelo y crea scripts de actualización.</span><span class="sxs-lookup"><span data-stu-id="b6765-126">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="b6765-127">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-127">For EF Core: 3.</span></span>

[<span data-ttu-id="b6765-128">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-128">Github repository</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="b6765-129">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="b6765-129">EF Core Power Tools</span></span>

<span data-ttu-id="b6765-130">EF Core Power Tools es una extensión de Visual Studio que expone varias tareas de tiempo de diseño de EF Core en una interfaz de usuario sencilla.</span><span class="sxs-lookup"><span data-stu-id="b6765-130">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="b6765-131">Incluye técnicas de ingeniería inversa de DbContext y clases de entidades a partir de bases de datos existentes y [DACPAC de SQL Server](/sql/relational-databases/data-tier-applications/data-tier-applications), así como la administración de la migración de bases de datos y la visualización de modelos.</span><span class="sxs-lookup"><span data-stu-id="b6765-131">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="b6765-132">Para EF Core: 3.5.</span><span class="sxs-lookup"><span data-stu-id="b6765-132">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="b6765-133">Wiki de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-133">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="b6765-134">Editor visual de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b6765-134">Entity Framework Visual Editor</span></span>

<span data-ttu-id="b6765-135">El Editor de objetos visuales de Entity Framework es una extensión de Visual Studio que agrega un diseñador O/RM que permite generar objetos visuales de las clases EF 6 y EF Core.</span><span class="sxs-lookup"><span data-stu-id="b6765-135">Entity Framework Visual Editor is a Visual Studio extension that adds an O/RM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="b6765-136">El código se genera mediante plantillas T4, por lo que se puede personalizar.</span><span class="sxs-lookup"><span data-stu-id="b6765-136">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="b6765-137">Asimismo, admite enumeraciones y asociaciones de herencia, unidireccionales y bidireccionales, y permite asignar colores a las clases y agregar bloques de texto para explicar ciertas partes del diseño que puedan resultar difíciles de comprender.</span><span class="sxs-lookup"><span data-stu-id="b6765-137">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="b6765-138">Para EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="b6765-138">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="b6765-139">Marketplace</span><span class="sxs-lookup"><span data-stu-id="b6765-139">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="b6765-140">CatFactory</span><span class="sxs-lookup"><span data-stu-id="b6765-140">CatFactory</span></span>

<span data-ttu-id="b6765-141">CatFactory es un motor de scaffolding para .NET Core que permite automatizar la generación de clases DbContext, entidades, opciones de configuración de la asignación y clases de repositorios de una base de datos SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b6765-141">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="b6765-142">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="b6765-142">For EF Core: 2.</span></span>

[<span data-ttu-id="b6765-143">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-143">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="b6765-144">LoreSoft's Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="b6765-144">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="b6765-145">Entity Framework Core Generator (efg) es una herramienta de la CLI de .NET Core que permite generar modelos EF Core a partir de una base de datos existente. Es similar a `dotnet ef dbcontext scaffold`, pero también admite la [regeneración](https://efg.loresoft.com/en/latest/regeneration/) de código seguro mediante el reemplazo de la región o el análisis de los archivos de asignación.</span><span class="sxs-lookup"><span data-stu-id="b6765-145">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="b6765-146">Asimismo, la herramienta permite generar modelos de vista, efectuar la validación y crear el código de los asignadores de objetos.</span><span class="sxs-lookup"><span data-stu-id="b6765-146">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="b6765-147">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="b6765-147">For EF Core: 2.</span></span>

<span data-ttu-id="b6765-148">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentación](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="b6765-148">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="b6765-149">Extensiones</span><span class="sxs-lookup"><span data-stu-id="b6765-149">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="b6765-150">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="b6765-150">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="b6765-151">Biblioteca de extensiones que permite registrar automáticamente los cambios en los datos realizados por EF Core e incluirlos en una tabla a modo de historial.</span><span class="sxs-lookup"><span data-stu-id="b6765-151">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="b6765-152">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-152">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="b6765-153">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-153">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="b6765-154">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="b6765-154">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="b6765-155">El almacenamiento en caché de segundo nivel es una caché de consulta.</span><span class="sxs-lookup"><span data-stu-id="b6765-155">Second level caching is a query cache.</span></span> <span data-ttu-id="b6765-156">Los resultados de los comandos de EF se almacenarán en la memoria caché, para que los mismos comandos de EF recuperen los datos de la memoria caché en lugar de ejecutarlos de nuevo en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b6765-156">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="b6765-157">Para EF Core: 3.5.</span><span class="sxs-lookup"><span data-stu-id="b6765-157">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="b6765-158">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-158">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="b6765-159">Geco</span><span class="sxs-lookup"><span data-stu-id="b6765-159">Geco</span></span>

<span data-ttu-id="b6765-160">Geco (Generator Console) es un generador de código muy sencillo que se basa en un proyecto de consola. Para generar el código, se ejecuta en .NET Core y utiliza cadenas C# interpoladas.</span><span class="sxs-lookup"><span data-stu-id="b6765-160">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="b6765-161">Geco incluye un generador de modelos de ingeniería inversa para EF Core que admite la pluralización, la singularización y las plantillas editables.</span><span class="sxs-lookup"><span data-stu-id="b6765-161">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="b6765-162">También proporciona un generador de scripts de datos semilla, un ejecutor de scripts y una herramienta para limpiar las bases de datos.</span><span class="sxs-lookup"><span data-stu-id="b6765-162">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="b6765-163">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="b6765-163">For EF Core: 2.</span></span>

[<span data-ttu-id="b6765-164">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-164">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="b6765-165">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="b6765-165">EntityFrameworkCore.Scaffolding.Handlebars</span></span>

<span data-ttu-id="b6765-166">Permite la personalización de clases con ingeniería inversa a partir de una base de datos existente mediante la cadena de herramientas de Entity Framework Core con las plantillas de Handlebars.</span><span class="sxs-lookup"><span data-stu-id="b6765-166">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="b6765-167">Para EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="b6765-167">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="b6765-168">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-168">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="b6765-169">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="b6765-169">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="b6765-170">NeinLinq amplía las características de proveedores LINQ como Entity Framework para permitir la reutilización de las funciones, la reescritura de las consultas y la creación de consultas dinámicas mediante selectores y predicados traducibles.</span><span class="sxs-lookup"><span data-stu-id="b6765-170">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="b6765-171">Para EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="b6765-171">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="b6765-172">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-172">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="b6765-173">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="b6765-173">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="b6765-174">Extensión de Microsoft.EntityFrameworkCore para admitir los repositorios, los patrones de unidades de trabajo y varias bases de datos compatibles con las transacciones distribuidas.</span><span class="sxs-lookup"><span data-stu-id="b6765-174">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="b6765-175">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-175">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="b6765-176">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-176">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="b6765-177">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="b6765-177">EFCore.BulkExtensions</span></span>

<span data-ttu-id="b6765-178">Extensiones de EF Core para operaciones masivas (Insert, Update y Delete).</span><span class="sxs-lookup"><span data-stu-id="b6765-178">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="b6765-179">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-179">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="b6765-180">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-180">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="b6765-181">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="b6765-181">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="b6765-182">Agrega pluralización en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="b6765-182">Adds design-time pluralization.</span></span> <span data-ttu-id="b6765-183">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-183">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="b6765-184">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-184">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="b6765-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="b6765-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="b6765-186">Recuperación del atributo [Index] (con extensión para la creación de modelos).</span><span class="sxs-lookup"><span data-stu-id="b6765-186">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="b6765-187">Para EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="b6765-187">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="b6765-188">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-188">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="verifyentityframework"></a><span data-ttu-id="b6765-189">Verify.EntityFramework</span><span class="sxs-lookup"><span data-stu-id="b6765-189">Verify.EntityFramework</span></span>

<span data-ttu-id="b6765-190">Extiende la [comprobación](https://github.com/VerifyTests/Verify) para permitir las pruebas de instantáneas con EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="b6765-190">Extends [Verify](https://github.com/VerifyTests/Verify) to allow snapshot testing with EntityFramework.</span></span> <span data-ttu-id="b6765-191">Para EF Core: 3.5.</span><span class="sxs-lookup"><span data-stu-id="b6765-191">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="b6765-192">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-192">GitHub repository</span></span>](https://github.com/VerifyTests/Verify.EntityFramework)

### <a name="localdb"></a><span data-ttu-id="b6765-193">LocalDb</span><span class="sxs-lookup"><span data-stu-id="b6765-193">LocalDb</span></span>

<span data-ttu-id="b6765-194">Proporciona un contenedor alrededor de [SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb) para simplificar las pruebas en ejecución en Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="b6765-194">Provides a wrapper around [SQL Server Express LocalDB](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb) to simplify running tests against Entity Framework.</span></span> <span data-ttu-id="b6765-195">Para EF Core: 3.5.</span><span class="sxs-lookup"><span data-stu-id="b6765-195">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="b6765-196">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-196">GitHub repository</span></span>](https://github.com/SimonCropp/LocalDb)

### <a name="effluentvalidation"></a><span data-ttu-id="b6765-197">EfFluentValidation</span><span class="sxs-lookup"><span data-stu-id="b6765-197">EfFluentValidation</span></span>

<span data-ttu-id="b6765-198">Agrega la compatibilidad de [FluentValidation](https://fluentvalidation.net/) a Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="b6765-198">Adds [FluentValidation](https://fluentvalidation.net/) support to Entity Framework.</span></span> <span data-ttu-id="b6765-199">Para EF Core: 3.5.</span><span class="sxs-lookup"><span data-stu-id="b6765-199">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="b6765-200">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-200">GitHub repository</span></span>](https://github.com/SimonCropp/EfFluentValidation)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="b6765-201">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="b6765-201">EFCore.TemporalSupport</span></span>

<span data-ttu-id="b6765-202">Implementación de compatibilidad temporal.</span><span class="sxs-lookup"><span data-stu-id="b6765-202">An implementation of temporal support.</span></span> <span data-ttu-id="b6765-203">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="b6765-203">For EF Core: 2.</span></span>

[<span data-ttu-id="b6765-204">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-204">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="b6765-205">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="b6765-205">EfCoreTemporalTable</span></span>

<span data-ttu-id="b6765-206">Realice consultas temporales fácilmente en su base de datos favorita con los métodos de extensión incorporados: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span><span class="sxs-lookup"><span data-stu-id="b6765-206">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="b6765-207">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-207">For EF Core: 3.</span></span>

[<span data-ttu-id="b6765-208">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-208">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="b6765-209">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="b6765-209">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="b6765-210">Biblioteca de extensiones para Entity Framework Core que permite a los desarrolladores que usan SQL Server utilizar fácilmente tablas temporales.</span><span class="sxs-lookup"><span data-stu-id="b6765-210">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="b6765-211">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-211">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="b6765-212">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-212">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)

### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="b6765-213">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="b6765-213">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="b6765-214">Caché de consulta de segundo nivel y alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="b6765-214">A high-performance second-level query cache.</span></span> <span data-ttu-id="b6765-215">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="b6765-215">For EF Core: 2.</span></span>

[<span data-ttu-id="b6765-216">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-216">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a><span data-ttu-id="b6765-217">EntityFrameworkCore.NCache</span><span class="sxs-lookup"><span data-stu-id="b6765-217">EntityFrameworkCore.NCache</span></span>

<span data-ttu-id="b6765-218">NCache de Entity Framework Core es un proveedor de caché de segundo nivel distribuido para almacenar en caché resultados de consultas.</span><span class="sxs-lookup"><span data-stu-id="b6765-218">NCache Entity Framework Core Provider is a distributed second level cache provider for caching query results.</span></span> <span data-ttu-id="b6765-219">La arquitectura distribuida de NCache hace que sea más escalable y de alta disponibilidad.</span><span class="sxs-lookup"><span data-stu-id="b6765-219">The distributed architecture of NCache makes it more scalable and highly available.</span></span> <span data-ttu-id="b6765-220">Para EF Core 2, 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-220">For EF Core 2, 3.</span></span>

[<span data-ttu-id="b6765-221">Sitio web</span><span class="sxs-lookup"><span data-stu-id="b6765-221">Website</span></span>](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entityframeworkcoretriggered"></a><span data-ttu-id="b6765-222">EntityFrameworkCore.Triggered</span><span class="sxs-lookup"><span data-stu-id="b6765-222">EntityFrameworkCore.Triggered</span></span>

<span data-ttu-id="b6765-223">Desencadenadores para EF Core.</span><span class="sxs-lookup"><span data-stu-id="b6765-223">Triggers for EF Core.</span></span> <span data-ttu-id="b6765-224">Responda a los cambios en DbContext antes y después de que se confirmen en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b6765-224">Respond to changes in your DbContext before and after they are committed to the database.</span></span> <span data-ttu-id="b6765-225">Los desencadenadores son totalmente asincrónicos y admiten la inserción de dependencias, la herencia, las funciones en cascada y mucho más.</span><span class="sxs-lookup"><span data-stu-id="b6765-225">Triggers are fully asynchronous and support dependency injection, inheritance, cascading and more.</span></span> <span data-ttu-id="b6765-226">Para EF Core: 3.5.</span><span class="sxs-lookup"><span data-stu-id="b6765-226">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="b6765-227">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-227">GitHub repository</span></span>](https://github.com/koenbeuk/EntityFrameworkCore.Triggered)

### <a name="entity-framework-plus"></a><span data-ttu-id="b6765-228">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="b6765-228">Entity Framework Plus</span></span>

<span data-ttu-id="b6765-229">Amplía su DbContext con características como: Incluir filtro, Auditoría, Cache, Consulta de futuro, Batch Delete, Actualización por lotes y más.</span><span class="sxs-lookup"><span data-stu-id="b6765-229">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="b6765-230">Para EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="b6765-230">For EF Core: 2, 3, 5.</span></span>

<span data-ttu-id="b6765-231">[Sitio web](https://entityframework-plus.net/)
[Repositorio de GitHub](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="b6765-231">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="b6765-232">Extensiones de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="b6765-232">Entity Framework Extensions</span></span>

<span data-ttu-id="b6765-233">Extiende su DbContext con operaciones masivas de alto rendimiento: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge y más.</span><span class="sxs-lookup"><span data-stu-id="b6765-233">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="b6765-234">Para EF Core: 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="b6765-234">For EF Core: 2, 3, 5.</span></span>

[<span data-ttu-id="b6765-235">Sitio web</span><span class="sxs-lookup"><span data-stu-id="b6765-235">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="b6765-236">Expressionify</span><span class="sxs-lookup"><span data-stu-id="b6765-236">Expressionify</span></span>

<span data-ttu-id="b6765-237">Agregue compatibilidad para llamar a métodos de extensión en expresiones lambda LINQ.</span><span class="sxs-lookup"><span data-stu-id="b6765-237">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="b6765-238">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-238">For EF Core: 3.</span></span>

[<span data-ttu-id="b6765-239">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-239">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="elinq"></a><span data-ttu-id="b6765-240">ELinq</span><span class="sxs-lookup"><span data-stu-id="b6765-240">ELinq</span></span>

<span data-ttu-id="b6765-241">Tecnología Language Integrated Query (LINQ) para bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="b6765-241">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="b6765-242">Permite usar C# para escribir consultas fuertemente tipadas.</span><span class="sxs-lookup"><span data-stu-id="b6765-242">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="b6765-243">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-243">For EF Core: 3.</span></span>

- <span data-ttu-id="b6765-244">Compatibilidad total de C# con la creación de consultas: varias instrucciones dentro de lambda, variables, funciones, etc.</span><span class="sxs-lookup"><span data-stu-id="b6765-244">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="b6765-245">Sin vacío semántico con SQL.</span><span class="sxs-lookup"><span data-stu-id="b6765-245">No semantic gap with SQL.</span></span> <span data-ttu-id="b6765-246">ELinq declara instrucciones SQL (como `SELECT`, `FROM`, `WHERE`) como métodos de C# de primera clase, y combina la sintaxis conocida con IntelliSense, seguridad de tipos y refactorización.</span><span class="sxs-lookup"><span data-stu-id="b6765-246">ELinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="b6765-247">Como resultado, SQL se convierte en "otra" biblioteca de clases que expone su API localmente, literalmente *"Language Integrated SQL"* .</span><span class="sxs-lookup"><span data-stu-id="b6765-247">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="b6765-248">Sitio web</span><span class="sxs-lookup"><span data-stu-id="b6765-248">Website</span></span>](https://entitylinq.com/)

### <a name="ramses"></a><span data-ttu-id="b6765-249">Ramses</span><span class="sxs-lookup"><span data-stu-id="b6765-249">Ramses</span></span>

<span data-ttu-id="b6765-250">Enlaces de ciclo de vida (para SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="b6765-250">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="b6765-251">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-251">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="b6765-252">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-252">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="b6765-253">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="b6765-253">EFCore.NamingConventions</span></span>

<span data-ttu-id="b6765-254">Todos los nombres de tabla y columna tendrán automáticamente un formato con palabras combinadas unidas por barras bajas (snake_case), todo en MAYÚSCULAS o bien todo en minúsculas.</span><span class="sxs-lookup"><span data-stu-id="b6765-254">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="b6765-255">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-255">For EF Core: 3.</span></span>

[<span data-ttu-id="b6765-256">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-256">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="b6765-257">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="b6765-257">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="b6765-258">Agrega compatibilidad nativa a EntityFrameworkCore para SQL Server para los tipos NodaTime.</span><span class="sxs-lookup"><span data-stu-id="b6765-258">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="b6765-259">Para EF Core: 3.5.</span><span class="sxs-lookup"><span data-stu-id="b6765-259">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="b6765-260">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-260">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="b6765-261">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="b6765-261">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="b6765-262">Extensiones LINQ en Entity Framework Core 3.1 para admitir la realización de consultas a tablas temporales de Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b6765-262">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="b6765-263">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-263">For EF Core: 3.</span></span>

[<span data-ttu-id="b6765-264">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-264">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="b6765-265">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="b6765-265">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="b6765-266">Agrega compatibilidad con hierarchyid al proveedor de EF Core de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="b6765-266">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="b6765-267">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-267">For EF Core: 3.</span></span>

[<span data-ttu-id="b6765-268">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-268">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="b6765-269">linq2db.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="b6765-269">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="b6765-270">Traductor alternativo de consultas LINQ a expresiones SQL.</span><span class="sxs-lookup"><span data-stu-id="b6765-270">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="b6765-271">Para EF Core: 3.5.</span><span class="sxs-lookup"><span data-stu-id="b6765-271">For EF Core: 3, 5.</span></span>

<span data-ttu-id="b6765-272">Incluye compatibilidad con características SQL avanzadas como CTE, copia masiva, sugerencias de tabla, funciones de división de particiones, tablas temporales y operaciones de creación, actualización y eliminación en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b6765-272">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="b6765-273">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-273">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a><span data-ttu-id="b6765-274">EFCore.SoftDelete</span><span class="sxs-lookup"><span data-stu-id="b6765-274">EFCore.SoftDelete</span></span>

<span data-ttu-id="b6765-275">Implementación de entidades de eliminación temporal.</span><span class="sxs-lookup"><span data-stu-id="b6765-275">An implementation for soft deleting entities.</span></span> <span data-ttu-id="b6765-276">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-276">For EF Core: 3.</span></span>

[<span data-ttu-id="b6765-277">NuGet</span><span class="sxs-lookup"><span data-stu-id="b6765-277">NuGet</span></span>](https://www.nuget.org/packages/EFCore.SoftDelete)

### <a name="entityframeworkcoreconfigurationmanager"></a><span data-ttu-id="b6765-278">EntityFrameworkCore.ConfigurationManager</span><span class="sxs-lookup"><span data-stu-id="b6765-278">EntityFrameworkCore.ConfigurationManager</span></span>

<span data-ttu-id="b6765-279">Extiende EF Core para resolver cadenas de conexión de App.config. Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="b6765-279">Extends EF Core to resolve connection strings from App.config. For EF Core: 3.</span></span>

[<span data-ttu-id="b6765-280">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-280">GitHub repository</span></span>](https://github.com/efcore/EFCore.ConfigurationManager)

### <a name="detached-mapper"></a><span data-ttu-id="b6765-281">Asignador desasociado</span><span class="sxs-lookup"><span data-stu-id="b6765-281">Detached Mapper</span></span>

<span data-ttu-id="b6765-282">Un asignador de entidades DTO con control de composición/agregación (similar a GraphDiff).</span><span class="sxs-lookup"><span data-stu-id="b6765-282">A DTO-Entity mapper with composition/aggregation handling (similar to GraphDiff).</span></span> <span data-ttu-id="b6765-283">Para EF Core: 3.5.</span><span class="sxs-lookup"><span data-stu-id="b6765-283">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="b6765-284">NuGet</span><span class="sxs-lookup"><span data-stu-id="b6765-284">NuGet</span></span>](https://www.nuget.org/packages/Detached.Mappers.EntityFramework)

### <a name="entityframeworkcoresqlitenodatime"></a><span data-ttu-id="b6765-285">EntityFrameworkCore.Sqlite.NodaTime</span><span class="sxs-lookup"><span data-stu-id="b6765-285">EntityFrameworkCore.Sqlite.NodaTime</span></span>

<span data-ttu-id="b6765-286">Agrega compatibilidad con los tipos [NodaTime](https://nodatime.org) cuando se usa [SQLite](https://sqlite.org).</span><span class="sxs-lookup"><span data-stu-id="b6765-286">Adds support for [NodaTime](https://nodatime.org) types when using [SQLite](https://sqlite.org).</span></span> <span data-ttu-id="b6765-287">Para EF Core: 5.</span><span class="sxs-lookup"><span data-stu-id="b6765-287">For EF Core: 5.</span></span>

[<span data-ttu-id="b6765-288">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-288">GitHub repository</span></span>](https://github.com/khellang/EFCore.Sqlite.NodaTime)

### <a name="erikejentityframeworkcoresqlserverdacpac"></a><span data-ttu-id="b6765-289">ErikEJ.EntityFrameworkCore.SqlServer.Dacpac</span><span class="sxs-lookup"><span data-stu-id="b6765-289">ErikEJ.EntityFrameworkCore.SqlServer.Dacpac</span></span>

<span data-ttu-id="b6765-290">Habilita la utilización de técnicas de ingeniería inversa en un modelo de EF Core a partir de un paquete de aplicación de capa de datos de SQL Server (.dacpac).</span><span class="sxs-lookup"><span data-stu-id="b6765-290">Enables reverse engineering an EF Core model from a SQL Server data-tier application package (.dacpac).</span></span> <span data-ttu-id="b6765-291">Para EF Core: 3.5.</span><span class="sxs-lookup"><span data-stu-id="b6765-291">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="b6765-292">Wiki de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-292">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki/ErikEJ.EntityFrameworkCore.SqlServer.Dacpac)

### <a name="erikejentityframeworkcoredgmlbuilder"></a><span data-ttu-id="b6765-293">ErikEJ.EntityFrameworkCore.DgmlBuilder</span><span class="sxs-lookup"><span data-stu-id="b6765-293">ErikEJ.EntityFrameworkCore.DgmlBuilder</span></span>

<span data-ttu-id="b6765-294">Genera contenido de DGML (Graph) que visualiza su DbContext.</span><span class="sxs-lookup"><span data-stu-id="b6765-294">Generate DGML (Graph) content that visualizes your DbContext.</span></span> <span data-ttu-id="b6765-295">Agrega el método de extensión AsDgml() a la clase DbContext.</span><span class="sxs-lookup"><span data-stu-id="b6765-295">Adds the AsDgml() extension method to the DbContext class.</span></span> <span data-ttu-id="b6765-296">Para EF Core: 3.5.</span><span class="sxs-lookup"><span data-stu-id="b6765-296">For EF Core: 3, 5.</span></span>

[<span data-ttu-id="b6765-297">Wiki de GitHub</span><span class="sxs-lookup"><span data-stu-id="b6765-297">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki/Inspect-your-DbContext-model)
