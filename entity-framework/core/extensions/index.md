---
title: Herramientas y extensiones - EF Core
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: 3fa407ca36d32a33a6bc889cd54ff88537510e9c
ms.sourcegitcommit: 387cbd8109c0fc5ce6bdc85d0dec1aed72ad4c33
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103118"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="587a5-102">Herramientas y extensiones de EF Core</span><span class="sxs-lookup"><span data-stu-id="587a5-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="587a5-103">Estas herramientas y extensiones proporcionan más funcionalidades para Entity Framework Core 2.1 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="587a5-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="587a5-104">Las extensiones están compiladas a partir de una gran variedad de orígenes, por lo que su mantenimiento no está incluido en el proyecto Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="587a5-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="587a5-105">En lo que respecta a las extensiones de terceros y para asegurarse de que cumplan sus requisitos, no se olvide de evaluar la calidad, las licencias, la compatibilidad, el soporte técnico, etc.</span><span class="sxs-lookup"><span data-stu-id="587a5-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="587a5-106">En concreto, es posible que sea necesario actualizar una extensión compilada para una versión anterior de EF Core para que funcione con las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="587a5-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="587a5-107">Herramientas</span><span class="sxs-lookup"><span data-stu-id="587a5-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="587a5-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="587a5-108">LLBLGen Pro</span></span>

<span data-ttu-id="587a5-109">LLBLGen Pro es una solución de modelado de entidad compatible con Entity Framework y Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="587a5-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="587a5-110">Permite definir fácilmente el modelo de entidad y asignarlo a la base de datos mediante Database First o Model First, de modo que pueda empezar a escribir consultas de inmediato.</span><span class="sxs-lookup"><span data-stu-id="587a5-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="587a5-111">Para EF Core: 2, 3</span><span class="sxs-lookup"><span data-stu-id="587a5-111">For EF Core: 2, 3</span></span>

[<span data-ttu-id="587a5-112">Sitio web</span><span class="sxs-lookup"><span data-stu-id="587a5-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="587a5-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="587a5-113">Devart Entity Developer</span></span>

<span data-ttu-id="587a5-114">Entity Developer es un potente diseñador ORM para ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access y LINQ to SQL.</span><span class="sxs-lookup"><span data-stu-id="587a5-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="587a5-115">Permite diseñar modelos EF Core de forma visual mediante los enfoques Database First y Model First, así como generar código C# y de Visual Basic.</span><span class="sxs-lookup"><span data-stu-id="587a5-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="587a5-116">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="587a5-116">For EF Core: 2.</span></span>

[<span data-ttu-id="587a5-117">Sitio web</span><span class="sxs-lookup"><span data-stu-id="587a5-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="587a5-118">nHydrate ORM para Entity Framework</span><span class="sxs-lookup"><span data-stu-id="587a5-118">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="587a5-119">ORM que crea clases extensibles y fuertemente tipadas para Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="587a5-119">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="587a5-120">El código generado es Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="587a5-120">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="587a5-121">No hay ninguna diferencia.</span><span class="sxs-lookup"><span data-stu-id="587a5-121">There is no difference.</span></span> <span data-ttu-id="587a5-122">Esto no es un reemplazo de EF ni un ORM personalizado.</span><span class="sxs-lookup"><span data-stu-id="587a5-122">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="587a5-123">Es una capa de modelado visual que permite a un equipo administrar esquemas de base de datos complejos.</span><span class="sxs-lookup"><span data-stu-id="587a5-123">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="587a5-124">Funciona bien con software SCM como GIT, lo que permite el acceso de varios usuarios al modelo con conflictos mínimos.</span><span class="sxs-lookup"><span data-stu-id="587a5-124">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="587a5-125">El instalador realiza un seguimiento de los cambios del modelo y crea scripts de actualización.</span><span class="sxs-lookup"><span data-stu-id="587a5-125">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="587a5-126">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-126">For EF Core: 3.</span></span>

[<span data-ttu-id="587a5-127">Sitio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-127">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="587a5-128">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="587a5-128">EF Core Power Tools</span></span>

<span data-ttu-id="587a5-129">EF Core Power Tools es una extensión de Visual Studio que expone varias tareas de tiempo de diseño de EF Core en una interfaz de usuario sencilla.</span><span class="sxs-lookup"><span data-stu-id="587a5-129">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="587a5-130">Incluye técnicas de ingeniería inversa de DbContext y clases de entidades a partir de bases de datos existentes y [DACPAC de SQL Server](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), así como la administración de la migración de bases de datos y la visualización de modelos.</span><span class="sxs-lookup"><span data-stu-id="587a5-130">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="587a5-131">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-131">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="587a5-132">Wiki de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-132">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="587a5-133">Editor visual de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="587a5-133">Entity Framework Visual Editor</span></span>

<span data-ttu-id="587a5-134">El Editor de objetos visuales de Entity Framework es una extensión de Visual Studio que agrega un diseñador ORM que permite generar objetos visuales de las clases EF 6 y EF Core.</span><span class="sxs-lookup"><span data-stu-id="587a5-134">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="587a5-135">El código se genera mediante plantillas T4, por lo que se puede personalizar.</span><span class="sxs-lookup"><span data-stu-id="587a5-135">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="587a5-136">Asimismo, admite enumeraciones y asociaciones de herencia, unidireccionales y bidireccionales, y permite asignar colores a las clases y agregar bloques de texto para explicar ciertas partes del diseño que puedan resultar difíciles de comprender.</span><span class="sxs-lookup"><span data-stu-id="587a5-136">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="587a5-137">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="587a5-137">For EF Core: 2.</span></span>

[<span data-ttu-id="587a5-138">Marketplace</span><span class="sxs-lookup"><span data-stu-id="587a5-138">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="587a5-139">CatFactory</span><span class="sxs-lookup"><span data-stu-id="587a5-139">CatFactory</span></span>

<span data-ttu-id="587a5-140">CatFactory es un motor de scaffolding para .NET Core que permite automatizar la generación de clases DbContext, entidades, opciones de configuración de la asignación y clases de repositorios de una base de datos SQL Server.</span><span class="sxs-lookup"><span data-stu-id="587a5-140">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="587a5-141">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="587a5-141">For EF Core: 2.</span></span>

[<span data-ttu-id="587a5-142">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-142">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="587a5-143">LoreSoft's Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="587a5-143">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="587a5-144">Entity Framework Core Generator (efg) es una herramienta de la CLI de .NET Core que permite generar modelos EF Core a partir de una base de datos existente. Es similar a `dotnet ef dbcontext scaffold`, pero también admite la [regeneración](https://efg.loresoft.com/en/latest/regeneration/) de código seguro mediante el reemplazo de la región o el análisis de los archivos de asignación.</span><span class="sxs-lookup"><span data-stu-id="587a5-144">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="587a5-145">Asimismo, la herramienta permite generar modelos de vista, efectuar la validación y crear el código de los asignadores de objetos.</span><span class="sxs-lookup"><span data-stu-id="587a5-145">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="587a5-146">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="587a5-146">For EF Core: 2.</span></span>

<span data-ttu-id="587a5-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentación](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="587a5-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="587a5-148">Extensiones</span><span class="sxs-lookup"><span data-stu-id="587a5-148">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="587a5-149">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="587a5-149">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="587a5-150">Biblioteca de extensiones que permite registrar automáticamente los cambios en los datos realizados por EF Core e incluirlos en una tabla a modo de historial.</span><span class="sxs-lookup"><span data-stu-id="587a5-150">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="587a5-151">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="587a5-151">For EF Core: 2.</span></span>

[<span data-ttu-id="587a5-152">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-152">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="587a5-153">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="587a5-153">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="587a5-154">El almacenamiento en caché de segundo nivel es una caché de consulta.</span><span class="sxs-lookup"><span data-stu-id="587a5-154">Second level caching is a query cache.</span></span> <span data-ttu-id="587a5-155">Los resultados de los comandos de EF se almacenarán en la memoria caché, para que los mismos comandos de EF recuperen los datos de la memoria caché en lugar de ejecutarlos de nuevo en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="587a5-155">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="587a5-156">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-156">For EF Core: 3.</span></span>

[<span data-ttu-id="587a5-157">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-157">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="587a5-158">Geco</span><span class="sxs-lookup"><span data-stu-id="587a5-158">Geco</span></span>

<span data-ttu-id="587a5-159">Geco (Generator Console) es un generador de código muy sencillo que se basa en un proyecto de consola. Para generar el código, se ejecuta en .NET Core y utiliza cadenas C# interpoladas.</span><span class="sxs-lookup"><span data-stu-id="587a5-159">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="587a5-160">Geco incluye un generador de modelos de ingeniería inversa para EF Core que admite la pluralización, la singularización y las plantillas editables.</span><span class="sxs-lookup"><span data-stu-id="587a5-160">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="587a5-161">También proporciona un generador de scripts de datos semilla, un ejecutor de scripts y una herramienta para limpiar las bases de datos.</span><span class="sxs-lookup"><span data-stu-id="587a5-161">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="587a5-162">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="587a5-162">For EF Core: 2.</span></span>

[<span data-ttu-id="587a5-163">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-163">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="587a5-164">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="587a5-164">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="587a5-165">Permite la personalización de clases con ingeniería inversa a partir de una base de datos existente mediante la cadena de herramientas de Entity Framework Core con las plantillas de Handlebars.</span><span class="sxs-lookup"><span data-stu-id="587a5-165">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="587a5-166">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-166">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="587a5-167">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-167">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="587a5-168">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="587a5-168">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="587a5-169">NeinLinq amplía las características de proveedores LINQ como Entity Framework para permitir la reutilización de las funciones, la reescritura de las consultas y la creación de consultas dinámicas mediante selectores y predicados traducibles.</span><span class="sxs-lookup"><span data-stu-id="587a5-169">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="587a5-170">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-170">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="587a5-171">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-171">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="587a5-172">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="587a5-172">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="587a5-173">Extensión de Microsoft.EntityFrameworkCore para admitir los repositorios, los patrones de unidades de trabajo y varias bases de datos compatibles con las transacciones distribuidas.</span><span class="sxs-lookup"><span data-stu-id="587a5-173">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="587a5-174">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="587a5-174">For EF Core: 2.</span></span>

[<span data-ttu-id="587a5-175">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-175">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="587a5-176">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="587a5-176">EFCore.BulkExtensions</span></span>

<span data-ttu-id="587a5-177">Extensiones de EF Core para operaciones masivas (Insert, Update y Delete).</span><span class="sxs-lookup"><span data-stu-id="587a5-177">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="587a5-178">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-178">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="587a5-179">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-179">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="587a5-180">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="587a5-180">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="587a5-181">Agrega pluralización en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="587a5-181">Adds design-time pluralization.</span></span> <span data-ttu-id="587a5-182">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="587a5-182">For EF Core: 2.</span></span>

[<span data-ttu-id="587a5-183">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-183">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="587a5-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="587a5-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="587a5-185">Recuperación del atributo [Index] (con extensión para la creación de modelos).</span><span class="sxs-lookup"><span data-stu-id="587a5-185">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="587a5-186">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-186">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="587a5-187">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-187">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="587a5-188">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="587a5-188">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="587a5-189">Proporciona un contenedor alrededor del proveedor de la base de datos en memoria de EF Core.</span><span class="sxs-lookup"><span data-stu-id="587a5-189">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="587a5-190">Así, hace que actúe de una forma más similar a la de un proveedor relacional.</span><span class="sxs-lookup"><span data-stu-id="587a5-190">Makes it act more like a relational provider.</span></span> <span data-ttu-id="587a5-191">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="587a5-191">For EF Core: 2.</span></span>

[<span data-ttu-id="587a5-192">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-192">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="587a5-193">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="587a5-193">EFCore.TemporalSupport</span></span>

<span data-ttu-id="587a5-194">Implementación de compatibilidad temporal.</span><span class="sxs-lookup"><span data-stu-id="587a5-194">An implementation of temporal support.</span></span> <span data-ttu-id="587a5-195">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="587a5-195">For EF Core: 2.</span></span>

[<span data-ttu-id="587a5-196">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-196">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="587a5-197">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="587a5-197">EfCoreTemporalTable</span></span>

<span data-ttu-id="587a5-198">Realice consultas temporales fácilmente en su base de datos favorita con los métodos de extensión incorporados: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span><span class="sxs-lookup"><span data-stu-id="587a5-198">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="587a5-199">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-199">For EF Core: 3.</span></span>

[<span data-ttu-id="587a5-200">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-200">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="587a5-201">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="587a5-201">EFCore.TimeTraveler</span></span>

<span data-ttu-id="587a5-202">Permita consultas completas de Entity Framework Core en el [historial temporal de SQL Server](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) mediante el código de EF Core, las entidades y las asignaciones que ya ha definido.</span><span class="sxs-lookup"><span data-stu-id="587a5-202">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="587a5-203">Para viajar a través del tiempo, encapsule el código en `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span><span class="sxs-lookup"><span data-stu-id="587a5-203">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="587a5-204">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-204">For EF Core: 3.</span></span>

[<span data-ttu-id="587a5-205">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-205">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="587a5-206">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="587a5-206">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="587a5-207">Biblioteca de extensiones para Entity Framework Core que permite a los desarrolladores que usan SQL Server utilizar fácilmente tablas temporales.</span><span class="sxs-lookup"><span data-stu-id="587a5-207">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="587a5-208">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="587a5-208">For EF Core: 2.</span></span>

[<span data-ttu-id="587a5-209">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-209">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="587a5-210">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="587a5-210">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="587a5-211">Caché de consulta de segundo nivel y alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="587a5-211">A high-performance second-level query cache.</span></span> <span data-ttu-id="587a5-212">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="587a5-212">For EF Core: 2.</span></span>

[<span data-ttu-id="587a5-213">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-213">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entity-framework-plus"></a><span data-ttu-id="587a5-214">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="587a5-214">Entity Framework Plus</span></span>

<span data-ttu-id="587a5-215">Amplía su DbContext con características como: Incluir filtro, Auditoría, Cache, Consulta de futuro, Batch Delete, Actualización por lotes y más.</span><span class="sxs-lookup"><span data-stu-id="587a5-215">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="587a5-216">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-216">For EF Core: 2, 3.</span></span>

<span data-ttu-id="587a5-217">[Sitio web](https://entityframework-plus.net/)
[Repositorio de GitHub](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="587a5-217">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="587a5-218">Extensiones de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="587a5-218">Entity Framework Extensions</span></span>

<span data-ttu-id="587a5-219">Extiende su DbContext con operaciones masivas de alto rendimiento: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge y más.</span><span class="sxs-lookup"><span data-stu-id="587a5-219">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="587a5-220">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-220">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="587a5-221">Sitio web</span><span class="sxs-lookup"><span data-stu-id="587a5-221">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="587a5-222">Expressionify</span><span class="sxs-lookup"><span data-stu-id="587a5-222">Expressionify</span></span>

<span data-ttu-id="587a5-223">Agregue compatibilidad para llamar a métodos de extensión en expresiones lambda LINQ.</span><span class="sxs-lookup"><span data-stu-id="587a5-223">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="587a5-224">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-224">For EF Core: 3.</span></span>

[<span data-ttu-id="587a5-225">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-225">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="587a5-226">XLinq</span><span class="sxs-lookup"><span data-stu-id="587a5-226">XLinq</span></span>

<span data-ttu-id="587a5-227">Tecnología Language Integrated Query (LINQ) para bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="587a5-227">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="587a5-228">Permite usar C# para escribir consultas fuertemente tipadas.</span><span class="sxs-lookup"><span data-stu-id="587a5-228">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="587a5-229">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-229">For EF Core: 3.</span></span>

- <span data-ttu-id="587a5-230">Compatibilidad total de C# con la creación de consultas: varias instrucciones dentro de lambda, variables, funciones, etc.</span><span class="sxs-lookup"><span data-stu-id="587a5-230">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="587a5-231">Sin vacío semántico con SQL.</span><span class="sxs-lookup"><span data-stu-id="587a5-231">No semantic gap with SQL.</span></span> <span data-ttu-id="587a5-232">XLinq declara instrucciones SQL (como `SELECT`, `FROM`, `WHERE`) como métodos de C# de primera clase, combinando la sintaxis conocida con IntelliSense, seguridad de tipos y refactorización.</span><span class="sxs-lookup"><span data-stu-id="587a5-232">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="587a5-233">Como resultado, SQL se convierte en "otra" biblioteca de clases que expone su API localmente, literalmente *"Language Integrated SQL"* .</span><span class="sxs-lookup"><span data-stu-id="587a5-233">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="587a5-234">Sitio web</span><span class="sxs-lookup"><span data-stu-id="587a5-234">Website</span></span>](http://xlinq.live/)

### <a name="ramses"></a><span data-ttu-id="587a5-235">Ramses</span><span class="sxs-lookup"><span data-stu-id="587a5-235">Ramses</span></span>

<span data-ttu-id="587a5-236">Enlaces de ciclo de vida (para SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="587a5-236">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="587a5-237">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-237">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="587a5-238">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-238">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="587a5-239">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="587a5-239">EFCore.NamingConventions</span></span>

<span data-ttu-id="587a5-240">Todos los nombres de tabla y columna tendrán automáticamente un formato con palabras combinadas unidas por barras bajas (snake_case), todo en MAYÚSCULAS o bien todo en minúsculas.</span><span class="sxs-lookup"><span data-stu-id="587a5-240">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="587a5-241">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-241">For EF Core: 3.</span></span>

[<span data-ttu-id="587a5-242">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-242">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="587a5-243">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="587a5-243">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="587a5-244">Agrega compatibilidad nativa a EntityFrameworkCore para SQL Server para los tipos NodaTime.</span><span class="sxs-lookup"><span data-stu-id="587a5-244">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="587a5-245">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-245">For EF Core: 3.</span></span>

[<span data-ttu-id="587a5-246">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-246">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="587a5-247">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="587a5-247">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="587a5-248">Extensiones LINQ en Entity Framework Core 3.1 para admitir la realización de consultas a tablas temporales de Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="587a5-248">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="587a5-249">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="587a5-249">For EF Core: 3.</span></span>

[<span data-ttu-id="587a5-250">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="587a5-250">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)
