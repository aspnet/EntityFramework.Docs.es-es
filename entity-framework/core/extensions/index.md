---
title: Herramientas y extensiones - EF Core
description: Herramientas y extensiones externas para Entity Framework Core
author: ErikEJ
ms.date: 04/11/2020
uid: core/extensions/index
ms.openlocfilehash: c7056bcb0831ae1919b3060aacf73dc5cb9c8cb1
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429941"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="7dd4a-103">Herramientas y extensiones de EF Core</span><span class="sxs-lookup"><span data-stu-id="7dd4a-103">EF Core Tools & Extensions</span></span>

<span data-ttu-id="7dd4a-104">Estas herramientas y extensiones proporcionan más funcionalidades para Entity Framework Core 2.1 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-104">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="7dd4a-105">Las extensiones están compiladas a partir de una gran variedad de orígenes, por lo que su mantenimiento no está incluido en el proyecto Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-105">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="7dd4a-106">En lo que respecta a las extensiones de terceros y para asegurarse de que cumplan sus requisitos, no se olvide de evaluar la calidad, las licencias, la compatibilidad, el soporte técnico, etc.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-106">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="7dd4a-107">En concreto, es posible que sea necesario actualizar una extensión compilada para una versión anterior de EF Core para que funcione con las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-107">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="7dd4a-108">Herramientas</span><span class="sxs-lookup"><span data-stu-id="7dd4a-108">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="7dd4a-109">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="7dd4a-109">LLBLGen Pro</span></span>

<span data-ttu-id="7dd4a-110">LLBLGen Pro es una solución de modelado de entidad compatible con Entity Framework y Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-110">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="7dd4a-111">Permite definir fácilmente el modelo de entidad y asignarlo a la base de datos mediante Database First o Model First, de modo que pueda empezar a escribir consultas de inmediato.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-111">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="7dd4a-112">Para EF Core: 2, 3</span><span class="sxs-lookup"><span data-stu-id="7dd4a-112">For EF Core: 2, 3</span></span>

[<span data-ttu-id="7dd4a-113">Sitio web</span><span class="sxs-lookup"><span data-stu-id="7dd4a-113">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="7dd4a-114">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="7dd4a-114">Devart Entity Developer</span></span>

<span data-ttu-id="7dd4a-115">Entity Developer es un potente diseñador O/RM para ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access y LINQ to SQL.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-115">Entity Developer is a powerful O/RM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="7dd4a-116">Permite diseñar modelos EF Core de forma visual mediante los enfoques Database First y Model First, así como generar código C# y de Visual Basic.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-116">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="7dd4a-117">Para EF Core: 1, 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-117">For EF Core: 1, 2, 3, 5.</span></span>

[<span data-ttu-id="7dd4a-118">Sitio web</span><span class="sxs-lookup"><span data-stu-id="7dd4a-118">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="7dd4a-119">nHydrate ORM para Entity Framework</span><span class="sxs-lookup"><span data-stu-id="7dd4a-119">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="7dd4a-120">O/RM que crea clases extensibles y fuertemente tipadas para Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-120">An O/RM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="7dd4a-121">El código generado es Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-121">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="7dd4a-122">No hay ninguna diferencia.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-122">There is no difference.</span></span> <span data-ttu-id="7dd4a-123">Esto no es un reemplazo de EF ni un O/RM personalizado.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-123">This is not a replacement for EF or a custom O/RM.</span></span> <span data-ttu-id="7dd4a-124">Es una capa de modelado visual que permite a un equipo administrar esquemas de base de datos complejos.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-124">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="7dd4a-125">Funciona bien con software SCM como GIT, lo que permite el acceso de varios usuarios al modelo con conflictos mínimos.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-125">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="7dd4a-126">El instalador realiza un seguimiento de los cambios del modelo y crea scripts de actualización.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-126">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="7dd4a-127">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-127">For EF Core: 3.</span></span>

[<span data-ttu-id="7dd4a-128">Sitio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-128">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="7dd4a-129">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="7dd4a-129">EF Core Power Tools</span></span>

<span data-ttu-id="7dd4a-130">EF Core Power Tools es una extensión de Visual Studio que expone varias tareas de tiempo de diseño de EF Core en una interfaz de usuario sencilla.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-130">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="7dd4a-131">Incluye técnicas de ingeniería inversa de DbContext y clases de entidades a partir de bases de datos existentes y [DACPAC de SQL Server](/sql/relational-databases/data-tier-applications/data-tier-applications), así como la administración de la migración de bases de datos y la visualización de modelos.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-131">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="7dd4a-132">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-132">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="7dd4a-133">Wiki de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-133">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="7dd4a-134">Editor visual de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="7dd4a-134">Entity Framework Visual Editor</span></span>

<span data-ttu-id="7dd4a-135">El Editor de objetos visuales de Entity Framework es una extensión de Visual Studio que agrega un diseñador O/RM que permite generar objetos visuales de las clases EF 6 y EF Core.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-135">Entity Framework Visual Editor is a Visual Studio extension that adds an O/RM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="7dd4a-136">El código se genera mediante plantillas T4, por lo que se puede personalizar.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-136">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="7dd4a-137">Asimismo, admite enumeraciones y asociaciones de herencia, unidireccionales y bidireccionales, y permite asignar colores a las clases y agregar bloques de texto para explicar ciertas partes del diseño que puedan resultar difíciles de comprender.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-137">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="7dd4a-138">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-138">For EF Core: 2.</span></span>

[<span data-ttu-id="7dd4a-139">Marketplace</span><span class="sxs-lookup"><span data-stu-id="7dd4a-139">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="7dd4a-140">CatFactory</span><span class="sxs-lookup"><span data-stu-id="7dd4a-140">CatFactory</span></span>

<span data-ttu-id="7dd4a-141">CatFactory es un motor de scaffolding para .NET Core que permite automatizar la generación de clases DbContext, entidades, opciones de configuración de la asignación y clases de repositorios de una base de datos SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-141">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="7dd4a-142">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-142">For EF Core: 2.</span></span>

[<span data-ttu-id="7dd4a-143">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-143">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="7dd4a-144">LoreSoft's Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="7dd4a-144">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="7dd4a-145">Entity Framework Core Generator (efg) es una herramienta de la CLI de .NET Core que permite generar modelos EF Core a partir de una base de datos existente. Es similar a `dotnet ef dbcontext scaffold`, pero también admite la [regeneración](https://efg.loresoft.com/en/latest/regeneration/) de código seguro mediante el reemplazo de la región o el análisis de los archivos de asignación.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-145">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="7dd4a-146">Asimismo, la herramienta permite generar modelos de vista, efectuar la validación y crear el código de los asignadores de objetos.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-146">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="7dd4a-147">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-147">For EF Core: 2.</span></span>

<span data-ttu-id="7dd4a-148">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentación](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="7dd4a-148">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="7dd4a-149">Extensiones</span><span class="sxs-lookup"><span data-stu-id="7dd4a-149">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="7dd4a-150">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="7dd4a-150">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="7dd4a-151">Biblioteca de extensiones que permite registrar automáticamente los cambios en los datos realizados por EF Core e incluirlos en una tabla a modo de historial.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-151">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="7dd4a-152">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-152">For EF Core: 2.</span></span>

[<span data-ttu-id="7dd4a-153">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-153">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="7dd4a-154">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="7dd4a-154">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="7dd4a-155">El almacenamiento en caché de segundo nivel es una caché de consulta.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-155">Second level caching is a query cache.</span></span> <span data-ttu-id="7dd4a-156">Los resultados de los comandos de EF se almacenarán en la memoria caché, para que los mismos comandos de EF recuperen los datos de la memoria caché en lugar de ejecutarlos de nuevo en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-156">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="7dd4a-157">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-157">For EF Core: 3.</span></span>

[<span data-ttu-id="7dd4a-158">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-158">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="7dd4a-159">Geco</span><span class="sxs-lookup"><span data-stu-id="7dd4a-159">Geco</span></span>

<span data-ttu-id="7dd4a-160">Geco (Generator Console) es un generador de código muy sencillo que se basa en un proyecto de consola. Para generar el código, se ejecuta en .NET Core y utiliza cadenas C# interpoladas.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-160">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="7dd4a-161">Geco incluye un generador de modelos de ingeniería inversa para EF Core que admite la pluralización, la singularización y las plantillas editables.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-161">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="7dd4a-162">También proporciona un generador de scripts de datos semilla, un ejecutor de scripts y una herramienta para limpiar las bases de datos.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-162">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="7dd4a-163">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-163">For EF Core: 2.</span></span>

[<span data-ttu-id="7dd4a-164">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-164">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="7dd4a-165">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="7dd4a-165">EntityFrameworkCore.Scaffolding.Handlebars</span></span>

<span data-ttu-id="7dd4a-166">Permite la personalización de clases con ingeniería inversa a partir de una base de datos existente mediante la cadena de herramientas de Entity Framework Core con las plantillas de Handlebars.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-166">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="7dd4a-167">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-167">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="7dd4a-168">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-168">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="7dd4a-169">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="7dd4a-169">NeinLinq.EntityFrameworkCore</span></span>

<span data-ttu-id="7dd4a-170">NeinLinq amplía las características de proveedores LINQ como Entity Framework para permitir la reutilización de las funciones, la reescritura de las consultas y la creación de consultas dinámicas mediante selectores y predicados traducibles.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-170">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="7dd4a-171">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-171">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="7dd4a-172">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-172">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="7dd4a-173">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="7dd4a-173">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="7dd4a-174">Extensión de Microsoft.EntityFrameworkCore para admitir los repositorios, los patrones de unidades de trabajo y varias bases de datos compatibles con las transacciones distribuidas.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-174">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="7dd4a-175">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-175">For EF Core: 2.</span></span>

[<span data-ttu-id="7dd4a-176">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-176">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="7dd4a-177">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="7dd4a-177">EFCore.BulkExtensions</span></span>

<span data-ttu-id="7dd4a-178">Extensiones de EF Core para operaciones masivas (Insert, Update y Delete).</span><span class="sxs-lookup"><span data-stu-id="7dd4a-178">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="7dd4a-179">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-179">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="7dd4a-180">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-180">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="7dd4a-181">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="7dd4a-181">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="7dd4a-182">Agrega pluralización en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-182">Adds design-time pluralization.</span></span> <span data-ttu-id="7dd4a-183">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-183">For EF Core: 2.</span></span>

[<span data-ttu-id="7dd4a-184">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-184">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="7dd4a-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="7dd4a-185">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="7dd4a-186">Recuperación del atributo [Index] (con extensión para la creación de modelos).</span><span class="sxs-lookup"><span data-stu-id="7dd4a-186">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="7dd4a-187">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-187">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="7dd4a-188">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-188">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="7dd4a-189">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="7dd4a-189">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="7dd4a-190">Proporciona un contenedor alrededor del proveedor de la base de datos en memoria de EF Core.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-190">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="7dd4a-191">Así, hace que actúe de una forma más similar a la de un proveedor relacional.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-191">Makes it act more like a relational provider.</span></span> <span data-ttu-id="7dd4a-192">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-192">For EF Core: 2.</span></span>

[<span data-ttu-id="7dd4a-193">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-193">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="7dd4a-194">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="7dd4a-194">EFCore.TemporalSupport</span></span>

<span data-ttu-id="7dd4a-195">Implementación de compatibilidad temporal.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-195">An implementation of temporal support.</span></span> <span data-ttu-id="7dd4a-196">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-196">For EF Core: 2.</span></span>

[<span data-ttu-id="7dd4a-197">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-197">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="7dd4a-198">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="7dd4a-198">EfCoreTemporalTable</span></span>

<span data-ttu-id="7dd4a-199">Realice consultas temporales fácilmente en su base de datos favorita con los métodos de extensión incorporados: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-199">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="7dd4a-200">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-200">For EF Core: 3.</span></span>

[<span data-ttu-id="7dd4a-201">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-201">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="7dd4a-202">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="7dd4a-202">EFCore.TimeTraveler</span></span>

<span data-ttu-id="7dd4a-203">Permita consultas completas de Entity Framework Core en el [historial temporal de SQL Server](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) mediante el código de EF Core, las entidades y las asignaciones que ya ha definido.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-203">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="7dd4a-204">Para viajar a través del tiempo, encapsule el código en `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-204">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="7dd4a-205">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-205">For EF Core: 3.</span></span>

[<span data-ttu-id="7dd4a-206">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-206">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)

### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="7dd4a-207">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="7dd4a-207">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="7dd4a-208">Biblioteca de extensiones para Entity Framework Core que permite a los desarrolladores que usan SQL Server utilizar fácilmente tablas temporales.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-208">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="7dd4a-209">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-209">For EF Core: 2.</span></span>

[<span data-ttu-id="7dd4a-210">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-210">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)

### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="7dd4a-211">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="7dd4a-211">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="7dd4a-212">Caché de consulta de segundo nivel y alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-212">A high-performance second-level query cache.</span></span> <span data-ttu-id="7dd4a-213">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-213">For EF Core: 2.</span></span>

[<span data-ttu-id="7dd4a-214">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-214">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a><span data-ttu-id="7dd4a-215">EntityFrameworkCore.NCache</span><span class="sxs-lookup"><span data-stu-id="7dd4a-215">EntityFrameworkCore.NCache</span></span>

<span data-ttu-id="7dd4a-216">NCache de Entity Framework Core es un proveedor de caché de segundo nivel distribuido para almacenar en caché resultados de consultas.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-216">NCache Entity Framework Core Provider is a distributed second level cache provider for caching query results.</span></span> <span data-ttu-id="7dd4a-217">La arquitectura distribuida de NCache hace que sea más escalable y de alta disponibilidad.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-217">The distributed architecture of NCache makes it more scalable and highly available.</span></span> <span data-ttu-id="7dd4a-218">Para EF Core 2.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-218">For EF Core 2.</span></span>

[<span data-ttu-id="7dd4a-219">Sitio web</span><span class="sxs-lookup"><span data-stu-id="7dd4a-219">Website</span></span>](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entity-framework-plus"></a><span data-ttu-id="7dd4a-220">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="7dd4a-220">Entity Framework Plus</span></span>

<span data-ttu-id="7dd4a-221">Amplía su DbContext con características como: Incluir filtro, Auditoría, Cache, Consulta de futuro, Batch Delete, Actualización por lotes y más.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-221">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="7dd4a-222">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-222">For EF Core: 2, 3.</span></span>

<span data-ttu-id="7dd4a-223">[Sitio web](https://entityframework-plus.net/)
[Repositorio de GitHub](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="7dd4a-223">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="7dd4a-224">Extensiones de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="7dd4a-224">Entity Framework Extensions</span></span>

<span data-ttu-id="7dd4a-225">Extiende su DbContext con operaciones masivas de alto rendimiento: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge y más.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-225">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="7dd4a-226">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-226">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="7dd4a-227">Sitio web</span><span class="sxs-lookup"><span data-stu-id="7dd4a-227">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="7dd4a-228">Expressionify</span><span class="sxs-lookup"><span data-stu-id="7dd4a-228">Expressionify</span></span>

<span data-ttu-id="7dd4a-229">Agregue compatibilidad para llamar a métodos de extensión en expresiones lambda LINQ.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-229">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="7dd4a-230">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-230">For EF Core: 3.</span></span>

[<span data-ttu-id="7dd4a-231">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-231">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="7dd4a-232">XLinq</span><span class="sxs-lookup"><span data-stu-id="7dd4a-232">XLinq</span></span>

<span data-ttu-id="7dd4a-233">Tecnología Language Integrated Query (LINQ) para bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-233">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="7dd4a-234">Permite usar C# para escribir consultas fuertemente tipadas.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-234">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="7dd4a-235">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-235">For EF Core: 3.</span></span>

- <span data-ttu-id="7dd4a-236">Compatibilidad total de C# con la creación de consultas: varias instrucciones dentro de lambda, variables, funciones, etc.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-236">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="7dd4a-237">Sin vacío semántico con SQL.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-237">No semantic gap with SQL.</span></span> <span data-ttu-id="7dd4a-238">XLinq declara instrucciones SQL (como `SELECT`, `FROM`, `WHERE`) como métodos de C# de primera clase, combinando la sintaxis conocida con IntelliSense, seguridad de tipos y refactorización.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-238">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="7dd4a-239">Como resultado, SQL se convierte en "otra" biblioteca de clases que expone su API localmente, literalmente *"Language Integrated SQL"* .</span><span class="sxs-lookup"><span data-stu-id="7dd4a-239">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="7dd4a-240">Sitio web</span><span class="sxs-lookup"><span data-stu-id="7dd4a-240">Website</span></span>](http://xlinq.live/)

### <a name="ramses"></a><span data-ttu-id="7dd4a-241">Ramses</span><span class="sxs-lookup"><span data-stu-id="7dd4a-241">Ramses</span></span>

<span data-ttu-id="7dd4a-242">Enlaces de ciclo de vida (para SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="7dd4a-242">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="7dd4a-243">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-243">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="7dd4a-244">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-244">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="7dd4a-245">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="7dd4a-245">EFCore.NamingConventions</span></span>

<span data-ttu-id="7dd4a-246">Todos los nombres de tabla y columna tendrán automáticamente un formato con palabras combinadas unidas por barras bajas (snake_case), todo en MAYÚSCULAS o bien todo en minúsculas.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-246">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="7dd4a-247">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-247">For EF Core: 3.</span></span>

[<span data-ttu-id="7dd4a-248">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-248">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="7dd4a-249">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="7dd4a-249">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="7dd4a-250">Agrega compatibilidad nativa a EntityFrameworkCore para SQL Server para los tipos NodaTime.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-250">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="7dd4a-251">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-251">For EF Core: 3.</span></span>

[<span data-ttu-id="7dd4a-252">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-252">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="7dd4a-253">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="7dd4a-253">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="7dd4a-254">Extensiones LINQ en Entity Framework Core 3.1 para admitir la realización de consultas a tablas temporales de Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-254">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="7dd4a-255">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-255">For EF Core: 3.</span></span>

[<span data-ttu-id="7dd4a-256">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-256">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="7dd4a-257">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="7dd4a-257">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="7dd4a-258">Agrega compatibilidad con hierarchyid al proveedor de EF Core de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-258">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="7dd4a-259">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-259">For EF Core: 3.</span></span>

[<span data-ttu-id="7dd4a-260">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-260">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="7dd4a-261">linq2db.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="7dd4a-261">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="7dd4a-262">Traductor alternativo de consultas LINQ a expresiones SQL.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-262">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="7dd4a-263">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-263">For EF Core: 3.</span></span>

<span data-ttu-id="7dd4a-264">Incluye compatibilidad con características SQL avanzadas como CTE, copia masiva, sugerencias de tabla, funciones de división de particiones, tablas temporales y operaciones de creación, actualización y eliminación en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-264">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="7dd4a-265">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-265">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a><span data-ttu-id="7dd4a-266">EFCore.SoftDelete</span><span class="sxs-lookup"><span data-stu-id="7dd4a-266">EFCore.SoftDelete</span></span>

<span data-ttu-id="7dd4a-267">Implementación de entidades de eliminación temporal.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-267">An implementation for soft deleting entities.</span></span> <span data-ttu-id="7dd4a-268">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-268">For EF Core: 3.</span></span>

[<span data-ttu-id="7dd4a-269">NuGet</span><span class="sxs-lookup"><span data-stu-id="7dd4a-269">NuGet</span></span>](https://www.nuget.org/packages/EFCore.SoftDelete)

### <a name="entityframeworkcoreconfigurationmanager"></a><span data-ttu-id="7dd4a-270">EntityFrameworkCore.ConfigurationManager</span><span class="sxs-lookup"><span data-stu-id="7dd4a-270">EntityFrameworkCore.ConfigurationManager</span></span>

<span data-ttu-id="7dd4a-271">Extiende EF Core para resolver cadenas de conexión de App.config. Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="7dd4a-271">Extends EF Core to resolve connection strings from App.config. For EF Core: 3.</span></span>

[<span data-ttu-id="7dd4a-272">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="7dd4a-272">GitHub repository</span></span>](https://github.com/efcore/EFCore.ConfigurationManager)
