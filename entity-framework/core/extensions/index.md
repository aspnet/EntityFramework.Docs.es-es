---
title: Herramientas y extensiones - EF Core
author: ErikEJ
ms.date: 04/11/2020
ms.assetid: 14fffb6c-a687-4881-a094-af4a1359a296
uid: core/extensions/index
ms.openlocfilehash: f450742b761ef3daf35e32cf87c63a8ee8a7b8c3
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526412"
---
# <a name="ef-core-tools--extensions"></a><span data-ttu-id="045ac-102">Herramientas y extensiones de EF Core</span><span class="sxs-lookup"><span data-stu-id="045ac-102">EF Core Tools & Extensions</span></span>

<span data-ttu-id="045ac-103">Estas herramientas y extensiones proporcionan más funcionalidades para Entity Framework Core 2.1 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="045ac-103">These tools and extensions provide additional functionality for Entity Framework Core 2.1 and later.</span></span>

> [!IMPORTANT]  
> <span data-ttu-id="045ac-104">Las extensiones están compiladas a partir de una gran variedad de orígenes, por lo que su mantenimiento no está incluido en el proyecto Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="045ac-104">Extensions are built by a variety of sources and aren't maintained as part of the Entity Framework Core project.</span></span> <span data-ttu-id="045ac-105">En lo que respecta a las extensiones de terceros y para asegurarse de que cumplan sus requisitos, no se olvide de evaluar la calidad, las licencias, la compatibilidad, el soporte técnico, etc.</span><span class="sxs-lookup"><span data-stu-id="045ac-105">When considering a third party extension, be sure to evaluate its quality, licensing, compatibility, support, etc. to ensure it meets your requirements.</span></span> <span data-ttu-id="045ac-106">En concreto, es posible que sea necesario actualizar una extensión compilada para una versión anterior de EF Core para que funcione con las versiones más recientes.</span><span class="sxs-lookup"><span data-stu-id="045ac-106">In particular, an extension built for an older version of EF Core may need updating before it will work with the latest versions.</span></span>

## <a name="tools"></a><span data-ttu-id="045ac-107">Herramientas</span><span class="sxs-lookup"><span data-stu-id="045ac-107">Tools</span></span>

### <a name="llblgen-pro"></a><span data-ttu-id="045ac-108">LLBLGen Pro</span><span class="sxs-lookup"><span data-stu-id="045ac-108">LLBLGen Pro</span></span>

<span data-ttu-id="045ac-109">LLBLGen Pro es una solución de modelado de entidad compatible con Entity Framework y Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="045ac-109">LLBLGen Pro is an entity modeling solution with support for Entity Framework and Entity Framework Core.</span></span> <span data-ttu-id="045ac-110">Permite definir fácilmente el modelo de entidad y asignarlo a la base de datos mediante Database First o Model First, de modo que pueda empezar a escribir consultas de inmediato.</span><span class="sxs-lookup"><span data-stu-id="045ac-110">It lets you easily define your entity model and map it to your database, using database first or model first, so you can get started writing queries right away.</span></span> <span data-ttu-id="045ac-111">Para EF Core: 2, 3</span><span class="sxs-lookup"><span data-stu-id="045ac-111">For EF Core: 2, 3</span></span>

[<span data-ttu-id="045ac-112">Sitio web</span><span class="sxs-lookup"><span data-stu-id="045ac-112">Website</span></span>](https://www.llblgen.com/)

### <a name="devart-entity-developer"></a><span data-ttu-id="045ac-113">Devart Entity Developer</span><span class="sxs-lookup"><span data-stu-id="045ac-113">Devart Entity Developer</span></span>

<span data-ttu-id="045ac-114">Entity Developer es un potente diseñador ORM para ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access y LINQ to SQL.</span><span class="sxs-lookup"><span data-stu-id="045ac-114">Entity Developer is a powerful ORM designer for ADO.NET Entity Framework, NHibernate, LinqConnect, Telerik Data Access, and LINQ to SQL.</span></span> <span data-ttu-id="045ac-115">Permite diseñar modelos EF Core de forma visual mediante los enfoques Database First y Model First, así como generar código C# y de Visual Basic.</span><span class="sxs-lookup"><span data-stu-id="045ac-115">It supports designing EF Core models visually, using model first or database first approaches, and C# or Visual Basic code generation.</span></span> <span data-ttu-id="045ac-116">Para EF Core: 1, 2, 3, 5.</span><span class="sxs-lookup"><span data-stu-id="045ac-116">For EF Core: 1, 2, 3, 5.</span></span>

[<span data-ttu-id="045ac-117">Sitio web</span><span class="sxs-lookup"><span data-stu-id="045ac-117">Website</span></span>](https://www.devart.com/entitydeveloper/)

### <a name="nhydrate-orm-for-entity-framework"></a><span data-ttu-id="045ac-118">nHydrate ORM para Entity Framework</span><span class="sxs-lookup"><span data-stu-id="045ac-118">nHydrate ORM for Entity Framework</span></span>

<span data-ttu-id="045ac-119">ORM que crea clases extensibles y fuertemente tipadas para Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="045ac-119">An ORM that creates strongly-typed, extendable classes for Entity Framework.</span></span> <span data-ttu-id="045ac-120">El código generado es Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="045ac-120">The generated code is Entity Framework Core.</span></span> <span data-ttu-id="045ac-121">No hay ninguna diferencia.</span><span class="sxs-lookup"><span data-stu-id="045ac-121">There is no difference.</span></span> <span data-ttu-id="045ac-122">Esto no es un reemplazo de EF ni un ORM personalizado.</span><span class="sxs-lookup"><span data-stu-id="045ac-122">This is not a replacement for EF or a custom ORM.</span></span> <span data-ttu-id="045ac-123">Es una capa de modelado visual que permite a un equipo administrar esquemas de base de datos complejos.</span><span class="sxs-lookup"><span data-stu-id="045ac-123">It is a visual, modeling layer that allows a team to manage complex database schemas.</span></span> <span data-ttu-id="045ac-124">Funciona bien con software SCM como GIT, lo que permite el acceso de varios usuarios al modelo con conflictos mínimos.</span><span class="sxs-lookup"><span data-stu-id="045ac-124">It works well with SCM software like Git, allowing multi-user access to your model with minimal conflicts.</span></span> <span data-ttu-id="045ac-125">El instalador realiza un seguimiento de los cambios del modelo y crea scripts de actualización.</span><span class="sxs-lookup"><span data-stu-id="045ac-125">The installer tracks model changes and creates upgrade scripts.</span></span> <span data-ttu-id="045ac-126">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-126">For EF Core: 3.</span></span>

[<span data-ttu-id="045ac-127">Sitio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-127">Github site</span></span>](https://github.com/nHydrate/nHydrate)

### <a name="ef-core-power-tools"></a><span data-ttu-id="045ac-128">EF Core Power Tools</span><span class="sxs-lookup"><span data-stu-id="045ac-128">EF Core Power Tools</span></span>

<span data-ttu-id="045ac-129">EF Core Power Tools es una extensión de Visual Studio que expone varias tareas de tiempo de diseño de EF Core en una interfaz de usuario sencilla.</span><span class="sxs-lookup"><span data-stu-id="045ac-129">EF Core Power Tools is a Visual Studio extension that exposes various EF Core design-time tasks in a simple user interface.</span></span> <span data-ttu-id="045ac-130">Incluye técnicas de ingeniería inversa de DbContext y clases de entidades a partir de bases de datos existentes y [DACPAC de SQL Server](/sql/relational-databases/data-tier-applications/data-tier-applications), así como la administración de la migración de bases de datos y la visualización de modelos.</span><span class="sxs-lookup"><span data-stu-id="045ac-130">It includes reverse engineering of DbContext and entity classes from existing databases and [SQL Server DACPACs](/sql/relational-databases/data-tier-applications/data-tier-applications), management of database migrations, and model visualizations.</span></span> <span data-ttu-id="045ac-131">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-131">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="045ac-132">Wiki de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-132">GitHub wiki</span></span>](https://github.com/ErikEJ/EFCorePowerTools/wiki)

### <a name="entity-framework-visual-editor"></a><span data-ttu-id="045ac-133">Editor visual de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="045ac-133">Entity Framework Visual Editor</span></span>

<span data-ttu-id="045ac-134">El Editor de objetos visuales de Entity Framework es una extensión de Visual Studio que agrega un diseñador ORM que permite generar objetos visuales de las clases EF 6 y EF Core.</span><span class="sxs-lookup"><span data-stu-id="045ac-134">Entity Framework Visual Editor is a Visual Studio extension that adds an ORM designer for visual design of EF 6, and EF Core classes.</span></span> <span data-ttu-id="045ac-135">El código se genera mediante plantillas T4, por lo que se puede personalizar.</span><span class="sxs-lookup"><span data-stu-id="045ac-135">Code is generated using T4 templates so can be customized to suit any needs.</span></span> <span data-ttu-id="045ac-136">Asimismo, admite enumeraciones y asociaciones de herencia, unidireccionales y bidireccionales, y permite asignar colores a las clases y agregar bloques de texto para explicar ciertas partes del diseño que puedan resultar difíciles de comprender.</span><span class="sxs-lookup"><span data-stu-id="045ac-136">It supports inheritance, unidirectional and bidirectional associations, enumerations, and the ability to color-code your classes and add text blocks to explain potentially arcane parts of your design.</span></span> <span data-ttu-id="045ac-137">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="045ac-137">For EF Core: 2.</span></span>

[<span data-ttu-id="045ac-138">Marketplace</span><span class="sxs-lookup"><span data-stu-id="045ac-138">Marketplace</span></span>](https://marketplace.visualstudio.com/items?itemName=michaelsawczyn.EFDesigner)

### <a name="catfactory"></a><span data-ttu-id="045ac-139">CatFactory</span><span class="sxs-lookup"><span data-stu-id="045ac-139">CatFactory</span></span>

<span data-ttu-id="045ac-140">CatFactory es un motor de scaffolding para .NET Core que permite automatizar la generación de clases DbContext, entidades, opciones de configuración de la asignación y clases de repositorios de una base de datos SQL Server.</span><span class="sxs-lookup"><span data-stu-id="045ac-140">CatFactory is a scaffolding engine for .NET Core that can automate the generation of DbContext classes, entities, mapping configurations, and repository classes from a SQL Server database.</span></span> <span data-ttu-id="045ac-141">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="045ac-141">For EF Core: 2.</span></span>

[<span data-ttu-id="045ac-142">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-142">GitHub repository</span></span>](https://github.com/hherzl/CatFactory.EntityFrameworkCore)

### <a name="loresofts-entity-framework-core-generator"></a><span data-ttu-id="045ac-143">LoreSoft's Entity Framework Core Generator</span><span class="sxs-lookup"><span data-stu-id="045ac-143">LoreSoft's Entity Framework Core Generator</span></span>

<span data-ttu-id="045ac-144">Entity Framework Core Generator (efg) es una herramienta de la CLI de .NET Core que permite generar modelos EF Core a partir de una base de datos existente. Es similar a `dotnet ef dbcontext scaffold`, pero también admite la [regeneración](https://efg.loresoft.com/en/latest/regeneration/) de código seguro mediante el reemplazo de la región o el análisis de los archivos de asignación.</span><span class="sxs-lookup"><span data-stu-id="045ac-144">Entity Framework Core Generator (efg) is a .NET Core CLI tool that can generate EF Core models from an existing database, much like `dotnet ef dbcontext scaffold`, but it also supports safe code [regeneration](https://efg.loresoft.com/en/latest/regeneration/) via region replacement or by parsing mapping files.</span></span> <span data-ttu-id="045ac-145">Asimismo, la herramienta permite generar modelos de vista, efectuar la validación y crear el código de los asignadores de objetos.</span><span class="sxs-lookup"><span data-stu-id="045ac-145">This tool supports generating view models, validation, and object mapper code.</span></span> <span data-ttu-id="045ac-146">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="045ac-146">For EF Core: 2.</span></span>

<span data-ttu-id="045ac-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentación](https://efg.loresoft.com/en/latest/)</span><span class="sxs-lookup"><span data-stu-id="045ac-147">[Tutorial](https://www.loresoft.com/Generate-ASP-NET-Web-API)
[Documentation](https://efg.loresoft.com/en/latest/)</span></span>

## <a name="extensions"></a><span data-ttu-id="045ac-148">Extensiones</span><span class="sxs-lookup"><span data-stu-id="045ac-148">Extensions</span></span>

### <a name="microsoftentityframeworkcoreautohistory"></a><span data-ttu-id="045ac-149">Microsoft.EntityFrameworkCore.AutoHistory</span><span class="sxs-lookup"><span data-stu-id="045ac-149">Microsoft.EntityFrameworkCore.AutoHistory</span></span>

<span data-ttu-id="045ac-150">Biblioteca de extensiones que permite registrar automáticamente los cambios en los datos realizados por EF Core e incluirlos en una tabla a modo de historial.</span><span class="sxs-lookup"><span data-stu-id="045ac-150">A plugin library that enables automatically recording the data changes performed by EF Core into a history table.</span></span> <span data-ttu-id="045ac-151">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="045ac-151">For EF Core: 2.</span></span>

[<span data-ttu-id="045ac-152">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-152">GitHub repository</span></span>](https://github.com/Arch/AutoHistory/)

### <a name="efcoresecondlevelcacheinterceptor"></a><span data-ttu-id="045ac-153">EFCoreSecondLevelCacheInterceptor</span><span class="sxs-lookup"><span data-stu-id="045ac-153">EFCoreSecondLevelCacheInterceptor</span></span>

<span data-ttu-id="045ac-154">El almacenamiento en caché de segundo nivel es una caché de consulta.</span><span class="sxs-lookup"><span data-stu-id="045ac-154">Second level caching is a query cache.</span></span> <span data-ttu-id="045ac-155">Los resultados de los comandos de EF se almacenarán en la memoria caché, para que los mismos comandos de EF recuperen los datos de la memoria caché en lugar de ejecutarlos de nuevo en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="045ac-155">The results of EF commands will be stored in the cache, so that the same EF commands will retrieve their data from the cache rather than executing them against the database again.</span></span> <span data-ttu-id="045ac-156">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-156">For EF Core: 3.</span></span>

[<span data-ttu-id="045ac-157">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-157">GitHub repository</span></span>](https://github.com/VahidN/EFCoreSecondLevelCacheInterceptor)

### <a name="geco"></a><span data-ttu-id="045ac-158">Geco</span><span class="sxs-lookup"><span data-stu-id="045ac-158">Geco</span></span>

<span data-ttu-id="045ac-159">Geco (Generator Console) es un generador de código muy sencillo que se basa en un proyecto de consola. Para generar el código, se ejecuta en .NET Core y utiliza cadenas C# interpoladas.</span><span class="sxs-lookup"><span data-stu-id="045ac-159">Geco (Generator Console) is a simple code generator based on a console project, that runs on .NET Core and uses C# interpolated strings for code generation.</span></span> <span data-ttu-id="045ac-160">Geco incluye un generador de modelos de ingeniería inversa para EF Core que admite la pluralización, la singularización y las plantillas editables.</span><span class="sxs-lookup"><span data-stu-id="045ac-160">Geco includes a reverse model generator for EF Core with support for pluralization, singularization, and editable templates.</span></span> <span data-ttu-id="045ac-161">También proporciona un generador de scripts de datos semilla, un ejecutor de scripts y una herramienta para limpiar las bases de datos.</span><span class="sxs-lookup"><span data-stu-id="045ac-161">It also provides a seed data script generator, a script runner, and a database cleaner.</span></span> <span data-ttu-id="045ac-162">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="045ac-162">For EF Core: 2.</span></span>

[<span data-ttu-id="045ac-163">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-163">GitHub repository</span></span>](https://github.com/iQuarc/Geco)

### <a name="entityframeworkcorescaffoldinghandlebars"></a><span data-ttu-id="045ac-164">EntityFrameworkCore.Scaffolding.Handlebars</span><span class="sxs-lookup"><span data-stu-id="045ac-164">EntityFrameworkCore.Scaffolding.Handlebars</span></span> 

<span data-ttu-id="045ac-165">Permite la personalización de clases con ingeniería inversa a partir de una base de datos existente mediante la cadena de herramientas de Entity Framework Core con las plantillas de Handlebars.</span><span class="sxs-lookup"><span data-stu-id="045ac-165">Allows customization of classes reverse engineered from an existing database using the Entity Framework Core toolchain with Handlebars templates.</span></span> <span data-ttu-id="045ac-166">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-166">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="045ac-167">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-167">GitHub repository</span></span>](https://github.com/TrackableEntities/EntityFrameworkCore.Scaffolding.Handlebars)

### <a name="neinlinqentityframeworkcore"></a><span data-ttu-id="045ac-168">NeinLinq.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="045ac-168">NeinLinq.EntityFrameworkCore</span></span> 

<span data-ttu-id="045ac-169">NeinLinq amplía las características de proveedores LINQ como Entity Framework para permitir la reutilización de las funciones, la reescritura de las consultas y la creación de consultas dinámicas mediante selectores y predicados traducibles.</span><span class="sxs-lookup"><span data-stu-id="045ac-169">NeinLinq extends LINQ providers such as Entity Framework to enable reusing functions, rewriting queries, and building dynamic queries using translatable predicates and selectors.</span></span> <span data-ttu-id="045ac-170">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-170">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="045ac-171">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-171">GitHub repository</span></span>](https://github.com/axelheer/nein-linq/)

### <a name="microsoftentityframeworkcoreunitofwork"></a><span data-ttu-id="045ac-172">Microsoft.EntityFrameworkCore.UnitOfWork</span><span class="sxs-lookup"><span data-stu-id="045ac-172">Microsoft.EntityFrameworkCore.UnitOfWork</span></span>

<span data-ttu-id="045ac-173">Extensión de Microsoft.EntityFrameworkCore para admitir los repositorios, los patrones de unidades de trabajo y varias bases de datos compatibles con las transacciones distribuidas.</span><span class="sxs-lookup"><span data-stu-id="045ac-173">A plugin for Microsoft.EntityFrameworkCore to support repository, unit of work patterns, and multiple databases with distributed transaction supported.</span></span> <span data-ttu-id="045ac-174">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="045ac-174">For EF Core: 2.</span></span>

[<span data-ttu-id="045ac-175">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-175">GitHub repository</span></span>](https://github.com/Arch/UnitOfWork/)

### <a name="efcorebulkextensions"></a><span data-ttu-id="045ac-176">EFCore.BulkExtensions</span><span class="sxs-lookup"><span data-stu-id="045ac-176">EFCore.BulkExtensions</span></span>

<span data-ttu-id="045ac-177">Extensiones de EF Core para operaciones masivas (Insert, Update y Delete).</span><span class="sxs-lookup"><span data-stu-id="045ac-177">EF Core extensions for Bulk operations (Insert, Update, Delete).</span></span> <span data-ttu-id="045ac-178">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-178">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="045ac-179">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-179">GitHub repository</span></span>](https://github.com/borisdj/EFCore.BulkExtensions)

### <a name="bricelamentityframeworkcorepluralizer"></a><span data-ttu-id="045ac-180">Bricelam.EntityFrameworkCore.Pluralizer</span><span class="sxs-lookup"><span data-stu-id="045ac-180">Bricelam.EntityFrameworkCore.Pluralizer</span></span>

<span data-ttu-id="045ac-181">Agrega pluralización en tiempo de diseño.</span><span class="sxs-lookup"><span data-stu-id="045ac-181">Adds design-time pluralization.</span></span> <span data-ttu-id="045ac-182">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="045ac-182">For EF Core: 2.</span></span>

[<span data-ttu-id="045ac-183">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-183">GitHub repository</span></span>](https://github.com/bricelam/EFCore.Pluralizer)

### <a name="toolbeltentityframeworkcoreindexattribute"></a><span data-ttu-id="045ac-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span><span class="sxs-lookup"><span data-stu-id="045ac-184">Toolbelt.EntityFrameworkCore.IndexAttribute</span></span>

<span data-ttu-id="045ac-185">Recuperación del atributo [Index] (con extensión para la creación de modelos).</span><span class="sxs-lookup"><span data-stu-id="045ac-185">Revival of [Index] attribute (with extension for model building).</span></span> <span data-ttu-id="045ac-186">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-186">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="045ac-187">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-187">GitHub repository</span></span>](https://github.com/jsakamoto/EntityFrameworkCore.IndexAttribute)

### <a name="efcoreinmemoryhelpers"></a><span data-ttu-id="045ac-188">EfCore.InMemoryHelpers</span><span class="sxs-lookup"><span data-stu-id="045ac-188">EfCore.InMemoryHelpers</span></span>

<span data-ttu-id="045ac-189">Proporciona un contenedor alrededor del proveedor de la base de datos en memoria de EF Core.</span><span class="sxs-lookup"><span data-stu-id="045ac-189">Provides a wrapper around the EF Core In-Memory Database Provider.</span></span> <span data-ttu-id="045ac-190">Así, hace que actúe de una forma más similar a la de un proveedor relacional.</span><span class="sxs-lookup"><span data-stu-id="045ac-190">Makes it act more like a relational provider.</span></span> <span data-ttu-id="045ac-191">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="045ac-191">For EF Core: 2.</span></span>

[<span data-ttu-id="045ac-192">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-192">GitHub repository</span></span>](https://github.com/SimonCropp/EfCore.InMemoryHelpers)

### <a name="efcoretemporalsupport"></a><span data-ttu-id="045ac-193">EFCore.TemporalSupport</span><span class="sxs-lookup"><span data-stu-id="045ac-193">EFCore.TemporalSupport</span></span>

<span data-ttu-id="045ac-194">Implementación de compatibilidad temporal.</span><span class="sxs-lookup"><span data-stu-id="045ac-194">An implementation of temporal support.</span></span> <span data-ttu-id="045ac-195">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="045ac-195">For EF Core: 2.</span></span>

[<span data-ttu-id="045ac-196">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-196">GitHub repository</span></span>](https://github.com/cpoDesign/EFCore.TemporalSupport)

### <a name="efcoretemporaltable"></a><span data-ttu-id="045ac-197">EfCoreTemporalTable</span><span class="sxs-lookup"><span data-stu-id="045ac-197">EfCoreTemporalTable</span></span>

<span data-ttu-id="045ac-198">Realice consultas temporales fácilmente en su base de datos favorita con los métodos de extensión incorporados: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span><span class="sxs-lookup"><span data-stu-id="045ac-198">Easily perform temporal queries on your favourite database using introduced extension methods: `AsTemporalAll()`, `AsTemporalAsOf(date)`, `AsTemporalFrom(startDate, endDate)`, `AsTemporalBetween(startDate, endDate)`, `AsTemporalContained(startDate, endDate)`.</span></span> <span data-ttu-id="045ac-199">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-199">For EF Core: 3.</span></span>

[<span data-ttu-id="045ac-200">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-200">GitHub repository</span></span>](https://github.com/glautrou/EfCoreTemporalTable)

### <a name="efcoretimetraveler"></a><span data-ttu-id="045ac-201">EFCore.TimeTraveler</span><span class="sxs-lookup"><span data-stu-id="045ac-201">EFCore.TimeTraveler</span></span>

<span data-ttu-id="045ac-202">Permita consultas completas de Entity Framework Core en el [historial temporal de SQL Server](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) mediante el código de EF Core, las entidades y las asignaciones que ya ha definido.</span><span class="sxs-lookup"><span data-stu-id="045ac-202">Allow full-featured Entity Framework Core queries against [SQL Server Temporal History](/sql/relational-databases/tables/temporal-table-usage-scenarios#point-in-time-analysis-time-travel) using the EF Core code, entities, and mappings you already have defined.</span></span>  <span data-ttu-id="045ac-203">Para viajar a través del tiempo, encapsule el código en `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span><span class="sxs-lookup"><span data-stu-id="045ac-203">Travel through time by wrapping your code in `using (TemporalQuery.AsOf(targetDateTime)) {...}`.</span></span> <span data-ttu-id="045ac-204">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-204">For EF Core: 3.</span></span>

[<span data-ttu-id="045ac-205">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-205">GitHub repository</span></span>](https://github.com/VantageSoftware/EFCore.TimeTraveler)


### <a name="entityframeworkcoretemporaltables"></a><span data-ttu-id="045ac-206">EntityFrameworkCore.TemporalTables</span><span class="sxs-lookup"><span data-stu-id="045ac-206">EntityFrameworkCore.TemporalTables</span></span>

<span data-ttu-id="045ac-207">Biblioteca de extensiones para Entity Framework Core que permite a los desarrolladores que usan SQL Server utilizar fácilmente tablas temporales.</span><span class="sxs-lookup"><span data-stu-id="045ac-207">Extension library for Entity Framework Core which allows developers who use SQL Server to easily use temporal tables.</span></span> <span data-ttu-id="045ac-208">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="045ac-208">For EF Core: 2.</span></span>

[<span data-ttu-id="045ac-209">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-209">GitHub repository</span></span>](https://github.com/findulov/EntityFrameworkCore.TemporalTables)


### <a name="entityframeworkcorecacheable"></a><span data-ttu-id="045ac-210">EntityFrameworkCore.Cacheable</span><span class="sxs-lookup"><span data-stu-id="045ac-210">EntityFrameworkCore.Cacheable</span></span>

<span data-ttu-id="045ac-211">Caché de consulta de segundo nivel y alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="045ac-211">A high-performance second-level query cache.</span></span> <span data-ttu-id="045ac-212">Para EF Core: 2.</span><span class="sxs-lookup"><span data-stu-id="045ac-212">For EF Core: 2.</span></span>

[<span data-ttu-id="045ac-213">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-213">GitHub repository</span></span>](https://github.com/SteffenMangold/EntityFrameworkCore.Cacheable)

### <a name="entityframeworkcorencache"></a><span data-ttu-id="045ac-214">EntityFrameworkCore.NCache</span><span class="sxs-lookup"><span data-stu-id="045ac-214">EntityFrameworkCore.NCache</span></span>

<span data-ttu-id="045ac-215">NCache de Entity Framework Core es un proveedor de caché de segundo nivel distribuido para almacenar en caché resultados de consultas.</span><span class="sxs-lookup"><span data-stu-id="045ac-215">NCache Entity Framework Core Provider is a distributed second level cache provider for caching query results.</span></span> <span data-ttu-id="045ac-216">La arquitectura distribuida de NCache hace que sea más escalable y de alta disponibilidad.</span><span class="sxs-lookup"><span data-stu-id="045ac-216">The distributed architecture of NCache makes it more scalable and highly available.</span></span> <span data-ttu-id="045ac-217">Para EF Core 2.</span><span class="sxs-lookup"><span data-stu-id="045ac-217">For EF Core 2.</span></span>

[<span data-ttu-id="045ac-218">Sitio web</span><span class="sxs-lookup"><span data-stu-id="045ac-218">Website</span></span>](https://www.alachisoft.com/ncache/ef-core-cache.html)

### <a name="entity-framework-plus"></a><span data-ttu-id="045ac-219">Entity Framework Plus</span><span class="sxs-lookup"><span data-stu-id="045ac-219">Entity Framework Plus</span></span>

<span data-ttu-id="045ac-220">Amplía su DbContext con características como: Incluir filtro, Auditoría, Cache, Consulta de futuro, Batch Delete, Actualización por lotes y más.</span><span class="sxs-lookup"><span data-stu-id="045ac-220">Extends your DbContext with features such as: Include Filter, Auditing, Caching, Query Future, Batch Delete, Batch Update, and more.</span></span> <span data-ttu-id="045ac-221">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-221">For EF Core: 2, 3.</span></span>

<span data-ttu-id="045ac-222">[Sitio web](https://entityframework-plus.net/)
[Repositorio de GitHub](https://github.com/zzzprojects/EntityFramework-Plus)</span><span class="sxs-lookup"><span data-stu-id="045ac-222">[Website](https://entityframework-plus.net/)
[GitHub repository](https://github.com/zzzprojects/EntityFramework-Plus)</span></span>

### <a name="entity-framework-extensions"></a><span data-ttu-id="045ac-223">Extensiones de Entity Framework</span><span class="sxs-lookup"><span data-stu-id="045ac-223">Entity Framework Extensions</span></span>

<span data-ttu-id="045ac-224">Extiende su DbContext con operaciones masivas de alto rendimiento: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge y más.</span><span class="sxs-lookup"><span data-stu-id="045ac-224">Extends your DbContext with high-performance bulk operations: BulkSaveChanges, BulkInsert, BulkUpdate, BulkDelete, BulkMerge, and more.</span></span> <span data-ttu-id="045ac-225">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-225">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="045ac-226">Sitio web</span><span class="sxs-lookup"><span data-stu-id="045ac-226">Website</span></span>](https://entityframework-extensions.net/)

### <a name="expressionify"></a><span data-ttu-id="045ac-227">Expressionify</span><span class="sxs-lookup"><span data-stu-id="045ac-227">Expressionify</span></span>

<span data-ttu-id="045ac-228">Agregue compatibilidad para llamar a métodos de extensión en expresiones lambda LINQ.</span><span class="sxs-lookup"><span data-stu-id="045ac-228">Add support for calling extension methods in LINQ lambdas.</span></span> <span data-ttu-id="045ac-229">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-229">For EF Core: 3.</span></span>

[<span data-ttu-id="045ac-230">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-230">GitHub repository</span></span>](https://github.com/ClaveConsulting/Expressionify)

### <a name="xlinq"></a><span data-ttu-id="045ac-231">XLinq</span><span class="sxs-lookup"><span data-stu-id="045ac-231">XLinq</span></span>

<span data-ttu-id="045ac-232">Tecnología Language Integrated Query (LINQ) para bases de datos relacionales.</span><span class="sxs-lookup"><span data-stu-id="045ac-232">Language Integrated Query (LINQ) technology for relational databases.</span></span> <span data-ttu-id="045ac-233">Permite usar C# para escribir consultas fuertemente tipadas.</span><span class="sxs-lookup"><span data-stu-id="045ac-233">It allows you to use C# to write strongly typed queries.</span></span> <span data-ttu-id="045ac-234">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-234">For EF Core: 3.</span></span>

- <span data-ttu-id="045ac-235">Compatibilidad total de C# con la creación de consultas: varias instrucciones dentro de lambda, variables, funciones, etc.</span><span class="sxs-lookup"><span data-stu-id="045ac-235">Full C# support for query creation: multiple statements inside lambda, variables, functions, etc.</span></span>
- <span data-ttu-id="045ac-236">Sin vacío semántico con SQL.</span><span class="sxs-lookup"><span data-stu-id="045ac-236">No semantic gap with SQL.</span></span> <span data-ttu-id="045ac-237">XLinq declara instrucciones SQL (como `SELECT`, `FROM`, `WHERE`) como métodos de C# de primera clase, combinando la sintaxis conocida con IntelliSense, seguridad de tipos y refactorización.</span><span class="sxs-lookup"><span data-stu-id="045ac-237">XLinq declares SQL statements (like `SELECT`, `FROM`, `WHERE`) as first class C# methods, combining familiar syntax with intellisense, type safety and refactoring.</span></span>

<span data-ttu-id="045ac-238">Como resultado, SQL se convierte en "otra" biblioteca de clases que expone su API localmente, literalmente *"Language Integrated SQL"* .</span><span class="sxs-lookup"><span data-stu-id="045ac-238">As a result SQL becomes just "another" class library exposing its API locally, literally *"Language Integrated SQL"*.</span></span>

[<span data-ttu-id="045ac-239">Sitio web</span><span class="sxs-lookup"><span data-stu-id="045ac-239">Website</span></span>](http://xlinq.live/)

### <a name="ramses"></a><span data-ttu-id="045ac-240">Ramses</span><span class="sxs-lookup"><span data-stu-id="045ac-240">Ramses</span></span>

<span data-ttu-id="045ac-241">Enlaces de ciclo de vida (para SaveChanges).</span><span class="sxs-lookup"><span data-stu-id="045ac-241">Lifecycle hooks (for SaveChanges).</span></span> <span data-ttu-id="045ac-242">Para EF Core: 2, 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-242">For EF Core: 2, 3.</span></span>

[<span data-ttu-id="045ac-243">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-243">GitHub repository</span></span>](https://github.com/JValck/Ramses)

### <a name="efcorenamingconventions"></a><span data-ttu-id="045ac-244">EFCore.NamingConventions</span><span class="sxs-lookup"><span data-stu-id="045ac-244">EFCore.NamingConventions</span></span>

<span data-ttu-id="045ac-245">Todos los nombres de tabla y columna tendrán automáticamente un formato con palabras combinadas unidas por barras bajas (snake_case), todo en MAYÚSCULAS o bien todo en minúsculas.</span><span class="sxs-lookup"><span data-stu-id="045ac-245">This will automatically make all your table and column names have snake_case, all UPPER or all lower case naming.</span></span> <span data-ttu-id="045ac-246">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-246">For EF Core: 3.</span></span>

[<span data-ttu-id="045ac-247">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-247">GitHub repository</span></span>](https://github.com/efcore/EFCore.NamingConventions)

### <a name="simplersoftwareentityframeworkcoresqlservernodatime"></a><span data-ttu-id="045ac-248">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span><span class="sxs-lookup"><span data-stu-id="045ac-248">SimplerSoftware.EntityFrameworkCore.SqlServer.NodaTime</span></span>

<span data-ttu-id="045ac-249">Agrega compatibilidad nativa a EntityFrameworkCore para SQL Server para los tipos NodaTime.</span><span class="sxs-lookup"><span data-stu-id="045ac-249">Adds native support to EntityFrameworkCore for SQL Server for the NodaTime types.</span></span> <span data-ttu-id="045ac-250">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-250">For EF Core: 3.</span></span>

[<span data-ttu-id="045ac-251">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-251">GitHub repository</span></span>](https://github.com/StevenRasmussen/EFCore.SqlServer.NodaTime)

### <a name="dabbleentityframeworkcoretemporalquery"></a><span data-ttu-id="045ac-252">Dabble.EntityFrameworkCore.Temporal.Query</span><span class="sxs-lookup"><span data-stu-id="045ac-252">Dabble.EntityFrameworkCore.Temporal.Query</span></span>

<span data-ttu-id="045ac-253">Extensiones LINQ en Entity Framework Core 3.1 para admitir la realización de consultas a tablas temporales de Microsoft SQL Server.</span><span class="sxs-lookup"><span data-stu-id="045ac-253">LINQ extensions to Entity Framework Core 3.1 to support Microsoft SQL Server Temporal Table Querying.</span></span> <span data-ttu-id="045ac-254">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-254">For EF Core: 3.</span></span>

[<span data-ttu-id="045ac-255">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-255">GitHub repository</span></span>](https://github.com/Adam-Langley/efcore-temporal-query)

### <a name="entityframeworkcoresqlserverhierarchyid"></a><span data-ttu-id="045ac-256">EntityFrameworkCore.SqlServer.HierarchyId</span><span class="sxs-lookup"><span data-stu-id="045ac-256">EntityFrameworkCore.SqlServer.HierarchyId</span></span>

<span data-ttu-id="045ac-257">Agrega compatibilidad con hierarchyid al proveedor de EF Core de SQL Server.</span><span class="sxs-lookup"><span data-stu-id="045ac-257">Adds hierarchyid support to the SQL Server EF Core provider.</span></span> <span data-ttu-id="045ac-258">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-258">For EF Core: 3.</span></span>

[<span data-ttu-id="045ac-259">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-259">GitHub repository</span></span>](https://github.com/efcore/EFCore.SqlServer.HierarchyId)

### <a name="linq2dbentityframeworkcore"></a><span data-ttu-id="045ac-260">linq2db.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="045ac-260">linq2db.EntityFrameworkCore</span></span>

<span data-ttu-id="045ac-261">Traductor alternativo de consultas LINQ a expresiones SQL.</span><span class="sxs-lookup"><span data-stu-id="045ac-261">Alternative translator of LINQ queries to SQL expressions.</span></span> <span data-ttu-id="045ac-262">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-262">For EF Core: 3.</span></span>

<span data-ttu-id="045ac-263">Incluye compatibilidad con características SQL avanzadas como CTE, copia masiva, sugerencias de tabla, funciones de división de particiones, tablas temporales y operaciones de creación, actualización y eliminación en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="045ac-263">Includes support for advanced SQL features such as CTEs, bulk copy, table hints, windowed functions, temporary tables, and database-side create/update/delete operations.</span></span>

[<span data-ttu-id="045ac-264">Repositorio de GitHub</span><span class="sxs-lookup"><span data-stu-id="045ac-264">GitHub repository</span></span>](https://github.com/linq2db/linq2db.EntityFrameworkCore)

### <a name="efcoresoftdelete"></a><span data-ttu-id="045ac-265">EFCore.SoftDelete</span><span class="sxs-lookup"><span data-stu-id="045ac-265">EFCore.SoftDelete</span></span>

<span data-ttu-id="045ac-266">Implementación de entidades de eliminación temporal.</span><span class="sxs-lookup"><span data-stu-id="045ac-266">An implementation for soft deleting entities.</span></span> <span data-ttu-id="045ac-267">Para EF Core: 3.</span><span class="sxs-lookup"><span data-stu-id="045ac-267">For EF Core: 3.</span></span>

[<span data-ttu-id="045ac-268">NuGet</span><span class="sxs-lookup"><span data-stu-id="045ac-268">NuGet</span></span>](https://www.nuget.org/packages/EFCore.SoftDelete)
