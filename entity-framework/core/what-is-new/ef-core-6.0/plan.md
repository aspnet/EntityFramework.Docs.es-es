---
title: Plan para Entity Framework Core 6.0
description: Temas y características planeados para EF Core 6.0
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/ef-core-6.0/plan
ms.openlocfilehash: 612461bc6ad30778baa5c6d10dda5cabac91dcb2
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129568"
---
# <a name="plan-for-entity-framework-core-60"></a><span data-ttu-id="7318c-103">Plan para Entity Framework Core 6.0</span><span class="sxs-lookup"><span data-stu-id="7318c-103">Plan for Entity Framework Core 6.0</span></span>

<span data-ttu-id="7318c-104">Como se describe en el [proceso de planificación](xref:core/what-is-new/release-planning), se ha recopilado información de las partes interesadas en un plan para la versión 6.0 de Entity Framework Core (EF Core).</span><span class="sxs-lookup"><span data-stu-id="7318c-104">As described in the [planning process](xref:core/what-is-new/release-planning), we have gathered input from stakeholders into a plan for the Entity Framework Core (EF Core) 6.0 release.</span></span>

<span data-ttu-id="7318c-105">A diferencia de las versiones anteriores, este plan no intenta abarcar todo el trabajo de la versión 6.0.</span><span class="sxs-lookup"><span data-stu-id="7318c-105">Unlike previous releases, this plan does not attempt to cover all work for the 6.0 release.</span></span> <span data-ttu-id="7318c-106">En su lugar, indica dónde y cómo se pretende invertir en esta versión, pero con flexibilidad para ajustar el ámbito o incorporar el nuevo trabajo a medida que se recopilen comentarios y se aprenda mientras se trabaja en la versión.</span><span class="sxs-lookup"><span data-stu-id="7318c-106">Instead, it indicates where and how we intend to invest in this release, but with flexibility to adjust scope or pull in new work as we gather feedback and learn while working on the release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7318c-107">Este plan no es un compromiso.</span><span class="sxs-lookup"><span data-stu-id="7318c-107">This plan is not a commitment.</span></span> <span data-ttu-id="7318c-108">Es un punto de partida que evolucionará a medida que se obtenga más información.</span><span class="sxs-lookup"><span data-stu-id="7318c-108">It is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="7318c-109">Es posible que algunos aspectos no planeados en la actualidad se incorporen a la versión 6.0.</span><span class="sxs-lookup"><span data-stu-id="7318c-109">Some things not currently planned for 6.0 may get pulled in.</span></span> <span data-ttu-id="7318c-110">Es posible que algunos aspectos planeados en la actualidad se eliminen de la versión 6.0.</span><span class="sxs-lookup"><span data-stu-id="7318c-110">Some things currently planned for 6.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="7318c-111">Información general</span><span class="sxs-lookup"><span data-stu-id="7318c-111">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="7318c-112">Número de versión y fecha de lanzamiento</span><span class="sxs-lookup"><span data-stu-id="7318c-112">Version number and release date</span></span>

<span data-ttu-id="7318c-113">EF Core 6.0 es la siguiente versión después de EF Core 5.0 y actualmente está programada para su lanzamiento en noviembre de 2021 al mismo tiempo que .NET 6.</span><span class="sxs-lookup"><span data-stu-id="7318c-113">EF Core 6.0 is the next release after EF Core 5.0 and is currently scheduled for release in November 2021 at the same time as .NET 6.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="7318c-114">Plataformas admitidas</span><span class="sxs-lookup"><span data-stu-id="7318c-114">Supported platforms</span></span>

<span data-ttu-id="7318c-115">Actualmente, EF Core 6.0 se destina a .NET 5.</span><span class="sxs-lookup"><span data-stu-id="7318c-115">EF Core 6.0 currently targets .NET 5.</span></span> <span data-ttu-id="7318c-116">Lo más probable es que se actualice a .NET 6 a medida que se acerque el lanzamiento.</span><span class="sxs-lookup"><span data-stu-id="7318c-116">This will likely be updated to .NET 6 as we near the release.</span></span> <span data-ttu-id="7318c-117">EF Core 6.0 no tiene como destino ninguna versión de .NET Standard; para obtener más información, vea [El futuro de .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span><span class="sxs-lookup"><span data-stu-id="7318c-117">EF Core 6.0 does not target any .NET Standard version; for more information see [the future of .NET Standard](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/).</span></span>

<span data-ttu-id="7318c-118">EF Core 6.0 no se ejecutará en .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="7318c-118">EF Core 6.0 will not run on .NET Framework.</span></span>

<span data-ttu-id="7318c-119">EF Core 6.0 se alineará con .NET 6 como una [versión de soporte técnico a largo plazo (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span><span class="sxs-lookup"><span data-stu-id="7318c-119">EF Core 6.0 will align with .NET 6 as a [long-term support (LTS) release](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="7318c-120">Últimos cambios</span><span class="sxs-lookup"><span data-stu-id="7318c-120">Breaking changes</span></span>

<span data-ttu-id="7318c-121">EF Core 6.0 contendrá un pequeño número de [cambios importantes](xref:core/what-is-new/ef-core-6.0/breaking-changes) a medida que EF Core y la plataforma .NET sigan evolucionando.</span><span class="sxs-lookup"><span data-stu-id="7318c-121">EF Core 6.0 will contain a small number of [breaking changes](xref:core/what-is-new/ef-core-6.0/breaking-changes) as we continue to evolve both EF Core and the .NET platform.</span></span> <span data-ttu-id="7318c-122">El objetivo es permitir que la gran mayoría de las aplicaciones se actualicen sin interrupciones.</span><span class="sxs-lookup"><span data-stu-id="7318c-122">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

## <a name="themes"></a><span data-ttu-id="7318c-123">Temas</span><span class="sxs-lookup"><span data-stu-id="7318c-123">Themes</span></span>

<span data-ttu-id="7318c-124">Las siguientes áreas constituirán la base de las principales inversiones en EF Core 6.0.</span><span class="sxs-lookup"><span data-stu-id="7318c-124">The following areas will form the basis for the large investments in EF Core 6.0.</span></span>

## <a name="highly-requested-features"></a><span data-ttu-id="7318c-125">Características muy solicitadas</span><span class="sxs-lookup"><span data-stu-id="7318c-125">Highly requested features</span></span>

<span data-ttu-id="7318c-126">Como siempre, una entrada importante del [proceso de planificación](xref:core/what-is-new/release-planning) procede de la [votación (👍) de características en GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="7318c-126">As always, a major input into the [planning process](xref:core/what-is-new/release-planning) comes from the [voting (👍) for features on GitHub](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span></span> <span data-ttu-id="7318c-127">Para EF Core 6.0 está previsto trabajar en las siguientes características muy solicitadas:</span><span class="sxs-lookup"><span data-stu-id="7318c-127">For EF Core 6.0 we plan to work on the following highly requested features:</span></span>

### <a name="sql-server-temporal-tables"></a><span data-ttu-id="7318c-128">Tablas temporales de SQL Server</span><span class="sxs-lookup"><span data-stu-id="7318c-128">SQL Server temporal tables</span></span>

<span data-ttu-id="7318c-129">Número de seguimiento: [4693](https://github.com/dotnet/efcore/issues/4693)</span><span class="sxs-lookup"><span data-stu-id="7318c-129">Tracked by [#4693](https://github.com/dotnet/efcore/issues/4693)</span></span>

<span data-ttu-id="7318c-130">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-130">Status: Not started</span></span>

<span data-ttu-id="7318c-131">Talla de camiseta: Grande</span><span class="sxs-lookup"><span data-stu-id="7318c-131">T-shirt size: Large</span></span>

<span data-ttu-id="7318c-132">Las tablas temporales admiten consultas de datos almacenados en la tabla en _cualquier momento_, en lugar de solo los más recientes, como sucede con las tablas normales.</span><span class="sxs-lookup"><span data-stu-id="7318c-132">Temporal tables support queries for data stored in the table at _any point in time_, as opposed to only the most recent data stored as is the case for normal tables.</span></span> <span data-ttu-id="7318c-133">EF Core 6.0 permitirá crear tablas temporales mediante Migraciones, además de permitir el acceso a los datos a través de consultas LINQ.</span><span class="sxs-lookup"><span data-stu-id="7318c-133">EF Core 6.0 will allow temporal tables to be created via Migrations, as well as allowing access to the data through LINQ queries.</span></span>

<span data-ttu-id="7318c-134">Inicialmente, el ámbito de este trabajo se limita como se [describe en la incidencia](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974).</span><span class="sxs-lookup"><span data-stu-id="7318c-134">This work is initially scoped as [described on the issue](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974).</span></span> <span data-ttu-id="7318c-135">Es posible que se incorpore soporte adicional en función de los comentarios durante el lanzamiento.</span><span class="sxs-lookup"><span data-stu-id="7318c-135">We may pull in additional support based on feedback during the release.</span></span>

### <a name="json-columns"></a><span data-ttu-id="7318c-136">Columnas JSON</span><span class="sxs-lookup"><span data-stu-id="7318c-136">JSON columns</span></span>

<span data-ttu-id="7318c-137">Número de seguimiento: [4021](https://github.com/dotnet/efcore/issues/4021)</span><span class="sxs-lookup"><span data-stu-id="7318c-137">Tracked by [#4021](https://github.com/dotnet/efcore/issues/4021)</span></span>

<span data-ttu-id="7318c-138">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-138">Status: Not started</span></span>

<span data-ttu-id="7318c-139">Talla de camiseta: Medium</span><span class="sxs-lookup"><span data-stu-id="7318c-139">T-shirt size: Medium</span></span>

<span data-ttu-id="7318c-140">Esta característica presentará un mecanismo común y patrones para la compatibilidad con JSON que cualquier proveedor de bases de datos puede implementar.</span><span class="sxs-lookup"><span data-stu-id="7318c-140">This feature will introduce a common mechanism and patterns for JSON support that can be implemented by any database provider.</span></span> <span data-ttu-id="7318c-141">Se trabajará con la comunidad para alinear las implementaciones existentes de [Npgsql](https://github.com/npgsql/efcore.pg) y [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), además de agregar compatibilidad con SQL Server y SQLite.</span><span class="sxs-lookup"><span data-stu-id="7318c-141">We will work with the community to align existing implementations for [Npgsql](https://github.com/npgsql/efcore.pg) and [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql), and also add support for SQL Server and SQLite.</span></span>

### <a name="columnattributeorder"></a><span data-ttu-id="7318c-142">ColumnAttribute.Order</span><span class="sxs-lookup"><span data-stu-id="7318c-142">ColumnAttribute.Order</span></span>

<span data-ttu-id="7318c-143">Número de seguimiento: [10059](https://github.com/dotnet/efcore/issues/10059)</span><span class="sxs-lookup"><span data-stu-id="7318c-143">Tracked by [#10059](https://github.com/dotnet/efcore/issues/10059)</span></span>

<span data-ttu-id="7318c-144">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-144">Status: Not started</span></span>

<span data-ttu-id="7318c-145">Talla de camiseta: Pequeña</span><span class="sxs-lookup"><span data-stu-id="7318c-145">T-shirt size: Small</span></span>

<span data-ttu-id="7318c-146">Esta característica permitirá el orden arbitrario de las columnas al **crear una tabla** con Migraciones o `EnsureCreated`.</span><span class="sxs-lookup"><span data-stu-id="7318c-146">This feature will allow arbitrary ordering of columns when **creating a table** with Migrations or `EnsureCreated`.</span></span> <span data-ttu-id="7318c-147">Tenga en cuenta que para cambiar el orden de las columnas en las tablas existentes es necesario volver a generar la tabla, y esto es algo que no está previsto admitir en ninguna versión de EF Core.</span><span class="sxs-lookup"><span data-stu-id="7318c-147">Note that changing the order of columns in an existing tables requires that the table be rebuilt, and this is not something that we plan to support in any EF Core release.</span></span>

## <a name="performance"></a><span data-ttu-id="7318c-148">Rendimiento</span><span class="sxs-lookup"><span data-stu-id="7318c-148">Performance</span></span>

<span data-ttu-id="7318c-149">Aunque por lo general EF Core es más rápido que EF6, todavía hay áreas en las que se puede mejorar significativamente el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="7318c-149">While EF Core is generally faster than EF6, there are still areas where significant improvements in performance are possible.</span></span> <span data-ttu-id="7318c-150">Está previsto abordar varias de estas áreas en EF Core 6.0, a la vez que se mejoran la infraestructura y las pruebas de rendimiento.</span><span class="sxs-lookup"><span data-stu-id="7318c-150">We plan to tackle several of these areas in EF Core 6.0, while also improving our perf infrastructure and testing.</span></span>

<span data-ttu-id="7318c-151">Este tema implicará una gran cantidad de investigación iterativa, que informará de los recursos elegidos como destino.</span><span class="sxs-lookup"><span data-stu-id="7318c-151">This theme will involve a lot of iterative investigation, which will inform where we focus resources.</span></span> <span data-ttu-id="7318c-152">Está previsto comenzar con lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7318c-152">We plan to begin with:</span></span>

### <a name="performance-infrastructure-and-new-tests"></a><span data-ttu-id="7318c-153">Infraestructura de rendimiento y nuevas pruebas</span><span class="sxs-lookup"><span data-stu-id="7318c-153">Performance infrastructure and new tests</span></span>

<span data-ttu-id="7318c-154">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-154">Status: Not started</span></span>

<span data-ttu-id="7318c-155">Talla de camiseta: Medium</span><span class="sxs-lookup"><span data-stu-id="7318c-155">T-shirt size: Medium</span></span>

<span data-ttu-id="7318c-156">El código base de EF Core ya contiene un conjunto de bancos de pruebas de rendimiento que se ejecutan todos los días.</span><span class="sxs-lookup"><span data-stu-id="7318c-156">The EF Core codebase already contains a set of performance benchmarks that are executed every day.</span></span> <span data-ttu-id="7318c-157">En la versión 6.0, está previsto mejorar la infraestructura de estas pruebas, así como agregar otras nuevas.</span><span class="sxs-lookup"><span data-stu-id="7318c-157">For 6.0, we plan to improve the infrastructure for these tests as well as adding new tests.</span></span> <span data-ttu-id="7318c-158">También se generarán perfiles de escenarios de rendimiento principales y se corregirá cualquier instancia de nivel inferior.</span><span class="sxs-lookup"><span data-stu-id="7318c-158">We will also profile mainline perf scenarios and fix any low-hanging fruit found.</span></span>

### <a name="compiled-models"></a><span data-ttu-id="7318c-159">Modelos compilados</span><span class="sxs-lookup"><span data-stu-id="7318c-159">Compiled models</span></span>

<span data-ttu-id="7318c-160">Número de seguimiento: [1906](https://github.com/dotnet/efcore/issues/1906)</span><span class="sxs-lookup"><span data-stu-id="7318c-160">Tracked by [#1906](https://github.com/dotnet/efcore/issues/1906)</span></span>

<span data-ttu-id="7318c-161">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-161">Status: Not started</span></span>

<span data-ttu-id="7318c-162">Talla de camiseta: Extra grande</span><span class="sxs-lookup"><span data-stu-id="7318c-162">T-shirt size: X-Large</span></span>

<span data-ttu-id="7318c-163">Los modelos compilados permitirán la generación de un formato compilado del modelo de EF.</span><span class="sxs-lookup"><span data-stu-id="7318c-163">Compiled models will allow the generation of a compiled form of the EF model.</span></span> <span data-ttu-id="7318c-164">Esto proporcionará un mejor rendimiento de inicio, así como un mejor rendimiento general al acceder al modelo.</span><span class="sxs-lookup"><span data-stu-id="7318c-164">This will provide both better startup performance, as well as generally better performance when accessing the model.</span></span>

### <a name="techempower-fortunes"></a><span data-ttu-id="7318c-165">TechEmpower Fortunes</span><span class="sxs-lookup"><span data-stu-id="7318c-165">TechEmpower Fortunes</span></span>

<span data-ttu-id="7318c-166">Número de seguimiento: [23611](https://github.com/dotnet/efcore/issues/23611)</span><span class="sxs-lookup"><span data-stu-id="7318c-166">Tracked by [#23611](https://github.com/dotnet/efcore/issues/23611)</span></span>

<span data-ttu-id="7318c-167">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-167">Status: Not started</span></span>

<span data-ttu-id="7318c-168">Talla de camiseta: Extra grande</span><span class="sxs-lookup"><span data-stu-id="7318c-168">T-shirt size: X-Large</span></span>

<span data-ttu-id="7318c-169">Durante varios años se han ejecutado los [bancos de pruebas de TechEmpower](https://www.techempower.com/benchmarks/) estándar del sector en .NET sobre una base de datos PostgreSQL.</span><span class="sxs-lookup"><span data-stu-id="7318c-169">We have been running the industry standard [TechEmpower benchmarks](https://www.techempower.com/benchmarks/) on .NET against a PostgreSQL database for several years.</span></span> <span data-ttu-id="7318c-170">El [banco de pruebas de Fortune](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) es especialmente relevante para los escenarios de EF.</span><span class="sxs-lookup"><span data-stu-id="7318c-170">The [Fortunes benchmark](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune) is particularly relevant to EF scenarios.</span></span> <span data-ttu-id="7318c-171">Se dispone de varias variaciones de este banco de pruebas, entre las que se incluyen las siguientes:</span><span class="sxs-lookup"><span data-stu-id="7318c-171">We have multiple variations of this benchmark, including:</span></span>

- <span data-ttu-id="7318c-172">Una implementación en la que se usa ADO.NET directamente.</span><span class="sxs-lookup"><span data-stu-id="7318c-172">An implementation that uses ADO.NET directly.</span></span> <span data-ttu-id="7318c-173">Esta es la implementación más rápida de las tres que se muestran aquí.</span><span class="sxs-lookup"><span data-stu-id="7318c-173">This is the fastest implementation of the three listed here.</span></span>
- <span data-ttu-id="7318c-174">Una implementación en la que se usa [Dapper](https://www.nuget.org/packages/Dapper/).</span><span class="sxs-lookup"><span data-stu-id="7318c-174">An implementation that uses [Dapper](https://www.nuget.org/packages/Dapper/).</span></span> <span data-ttu-id="7318c-175">Es más lenta que cuando se usa ADO.NET directamente, pero sigue siendo rápida.</span><span class="sxs-lookup"><span data-stu-id="7318c-175">This is slower than using ADO.NET directly, but still fast.</span></span>
- <span data-ttu-id="7318c-176">Una implementación en la que se usa EF Core.</span><span class="sxs-lookup"><span data-stu-id="7318c-176">An implementation that uses EF Core.</span></span> <span data-ttu-id="7318c-177">Actualmente esta es la implementación más lenta de las tres.</span><span class="sxs-lookup"><span data-stu-id="7318c-177">This is currently the slowest implementation of the three.</span></span>

<span data-ttu-id="7318c-178">El objetivo de EF Core 6.0 es conseguir que el rendimiento de EF Core coincida con el de Dapper en el banco de pruebas de TechEmpower Fortunes.</span><span class="sxs-lookup"><span data-stu-id="7318c-178">The goal for EF Core 6.0 is to get the EF Core performance to match that of Dapper on the TechEmpower Fortunes benchmark.</span></span> <span data-ttu-id="7318c-179">(Es un reto importante, pero se hará todo lo posible para acercarse al máximo).</span><span class="sxs-lookup"><span data-stu-id="7318c-179">(This is a significant challenge but we will do our best to get as close as we can.)</span></span>

### <a name="linkeraot"></a><span data-ttu-id="7318c-180">Enlazador/AOT</span><span class="sxs-lookup"><span data-stu-id="7318c-180">Linker/AOT</span></span>

<span data-ttu-id="7318c-181">Número de seguimiento: [10963](https://github.com/dotnet/efcore/issues/10963)</span><span class="sxs-lookup"><span data-stu-id="7318c-181">Tracked by [#10963](https://github.com/dotnet/efcore/issues/10963)</span></span>

<span data-ttu-id="7318c-182">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-182">Status: Not started</span></span>

<span data-ttu-id="7318c-183">Talla de camiseta: Medium</span><span class="sxs-lookup"><span data-stu-id="7318c-183">T-shirt size: Medium</span></span>

<span data-ttu-id="7318c-184">EF Core realiza grandes cantidades de generación de código en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="7318c-184">EF Core performs large amounts of runtime code generation.</span></span> <span data-ttu-id="7318c-185">Esto supone un reto para los modelos de aplicación que dependen de la modificación del árbol del enlazador, como Xamarin y Blazor, y para las plataformas que no permiten la compilación dinámica, como iOS.</span><span class="sxs-lookup"><span data-stu-id="7318c-185">This is challenging for app models that depend on linker tree shaking, such as Xamarin and Blazor, and platforms that don't allow dynamic compilation, such as iOS.</span></span> <span data-ttu-id="7318c-186">Se seguirá investigando en este espacio como parte de EF Core 6.0 y, siempre que se pueda, se intentarán realizar mejoras concretas.</span><span class="sxs-lookup"><span data-stu-id="7318c-186">We will continue investigating in this space as part of EF Core 6.0 and make targeted improvements as we can.</span></span> <span data-ttu-id="7318c-187">Pero no se espera solventar todas las diferencias por completo durante el intervalo de tiempo de la versión 6.0.</span><span class="sxs-lookup"><span data-stu-id="7318c-187">However, we do not expect to fully close the gap in the 6.0 time frame.</span></span>

## <a name="migrations-and-deployment"></a><span data-ttu-id="7318c-188">Migraciones e implementación</span><span class="sxs-lookup"><span data-stu-id="7318c-188">Migrations and deployment</span></span>

<span data-ttu-id="7318c-189">A partir de las [investigaciones realizadas para EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), está previsto incorporar compatibilidad mejorada para la administración de migraciones y la implementación de bases de datos.</span><span class="sxs-lookup"><span data-stu-id="7318c-189">Following on from the [investigations done for EF Core 5.0](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience), we plan to introduce improved support for managing migrations and deploying databases.</span></span> <span data-ttu-id="7318c-190">Esto incluye dos áreas principales:</span><span class="sxs-lookup"><span data-stu-id="7318c-190">This includes two major areas:</span></span>

### <a name="migrations-bundles"></a><span data-ttu-id="7318c-191">Agrupaciones de migraciones</span><span class="sxs-lookup"><span data-stu-id="7318c-191">Migrations bundles</span></span>

<span data-ttu-id="7318c-192">Número de seguimiento: [19693](https://github.com/dotnet/efcore/issues/19693)</span><span class="sxs-lookup"><span data-stu-id="7318c-192">Tracked by [#19693](https://github.com/dotnet/efcore/issues/19693)</span></span>

<span data-ttu-id="7318c-193">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-193">Status: Not started</span></span>

<span data-ttu-id="7318c-194">Talla de camiseta: Medium</span><span class="sxs-lookup"><span data-stu-id="7318c-194">T-shirt size: Medium</span></span>

<span data-ttu-id="7318c-195">Una agrupación de migraciones es un archivo ejecutable independiente que aplica migraciones a una base de datos de producción.</span><span class="sxs-lookup"><span data-stu-id="7318c-195">A migrations bundle is a self-contained executable that applies migrations to a production database.</span></span> <span data-ttu-id="7318c-196">El comportamiento coincidirá con `dotnet ef database update`, pero debe facilitar considerablemente la implementación de SSH, Docker o PowerShell, ya que todo lo que se necesita se incluye en el ejecutable único.</span><span class="sxs-lookup"><span data-stu-id="7318c-196">The behavior will match `dotnet ef database update`, but should make SSH/Docker/PowerShell deployment much easier, since everything needed is contained in the single executable.</span></span>

### <a name="managing-migrations"></a><span data-ttu-id="7318c-197">Administración de migraciones</span><span class="sxs-lookup"><span data-stu-id="7318c-197">Managing migrations</span></span>

<span data-ttu-id="7318c-198">Número de seguimiento: [22945](https://github.com/dotnet/efcore/issues/22945)</span><span class="sxs-lookup"><span data-stu-id="7318c-198">Tracked by [#22945](https://github.com/dotnet/efcore/issues/22945)</span></span>

<span data-ttu-id="7318c-199">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-199">Status: Not started</span></span>

<span data-ttu-id="7318c-200">Talla de camiseta: Grande</span><span class="sxs-lookup"><span data-stu-id="7318c-200">T-shirt size: Large</span></span>

<span data-ttu-id="7318c-201">El número de migraciones creadas para una aplicación puede aumentar hasta convertirse en una carga.</span><span class="sxs-lookup"><span data-stu-id="7318c-201">The number of migrations created for an application can grow to become a burden.</span></span> <span data-ttu-id="7318c-202">Además, estas migraciones se suelen implementar con la aplicación aunque no sea necesario.</span><span class="sxs-lookup"><span data-stu-id="7318c-202">In addition, these migrations are frequently deployed with the application even when this is not needed.</span></span> <span data-ttu-id="7318c-203">En EF Core 6.0, el plan es mejorar esto mediante mejores herramientas y administración de proyectos y ensamblados.</span><span class="sxs-lookup"><span data-stu-id="7318c-203">In EF Core 6.0, we plan to improve this through better tooling and project/assembly management.</span></span> <span data-ttu-id="7318c-204">Dos problemas específicos que está previsto abordar son la [inclusión de muchas migraciones en una](https://github.com/dotnet/efcore/issues/2174) y la [regeneración de una instantánea de modelo limpia](https://github.com/dotnet/efcore/issues/18557).</span><span class="sxs-lookup"><span data-stu-id="7318c-204">Two specific issues we plan to address are [squash many migrations into one](https://github.com/dotnet/efcore/issues/2174) and [regenerate a clean model snapshot](https://github.com/dotnet/efcore/issues/18557).</span></span>

## <a name="improve-existing-features-and-fix-bugs"></a><span data-ttu-id="7318c-205">Mejora de las características existentes y corrección de errores</span><span class="sxs-lookup"><span data-stu-id="7318c-205">Improve existing features and fix bugs</span></span>

<span data-ttu-id="7318c-206">Actualmente, está previsto para esta versión cualquier [incidencia o error asignado al hito 6.0.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0).</span><span class="sxs-lookup"><span data-stu-id="7318c-206">Any [issue or bug assigned to the 6.0.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0) is currently planned for this release.</span></span> <span data-ttu-id="7318c-207">Esto incluye muchas mejoras pequeñas y correcciones de errores.</span><span class="sxs-lookup"><span data-stu-id="7318c-207">This includes many small enhancements and bug fixes.</span></span>

### <a name="ef6-query-parity"></a><span data-ttu-id="7318c-208">Paridad de consultas de EF6</span><span class="sxs-lookup"><span data-stu-id="7318c-208">EF6 query parity</span></span>

<span data-ttu-id="7318c-209">Seguimiento por [incidencias etiquetadas con "ef6-parity" y en el hito 6.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)</span><span class="sxs-lookup"><span data-stu-id="7318c-209">Tracked by [issues labeled with 'ef6-parity' and in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)</span></span>

<span data-ttu-id="7318c-210">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-210">Status: Not started</span></span>

<span data-ttu-id="7318c-211">Talla de camiseta: Grande</span><span class="sxs-lookup"><span data-stu-id="7318c-211">T-shirt size: Large</span></span>

<span data-ttu-id="7318c-212">EF Core 5.0 admite la mayoría de los patrones de consulta admitidos por EF6, además de los que no se admiten en EF6.</span><span class="sxs-lookup"><span data-stu-id="7318c-212">EF Core 5.0 supports most query patterns supported by EF6, in addition to patterns not supported in EF6.</span></span> <span data-ttu-id="7318c-213">Para EF Core 6.0, está previsto reducir las diferencias y hacer que las consultas de EF Core admitidas sean un superconjunto real de las admitidas en EF6.</span><span class="sxs-lookup"><span data-stu-id="7318c-213">For EF Core 6.0, we plan to close the gap and make supported EF Core queries a true superset of supported EF6 queries.</span></span> <span data-ttu-id="7318c-214">Esto se controla mediante la investigación de las diferencias, pero ya se incluyen incidencias de GroupBy como la [traducción de GroupBy seguido de FirstOrDefault](https://github.com/dotnet/efcore/issues/12088) y consultas SQL sin procesar para tipos [primitivos](https://github.com/dotnet/efcore/issues/11624) y [no asignados](https://github.com/dotnet/efcore/issues/10753).</span><span class="sxs-lookup"><span data-stu-id="7318c-214">This will be driven by investigation of the gaps, but already includes GroupBy issues such as [translate GroupBy followed by FirstOrDefault](https://github.com/dotnet/efcore/issues/12088), and raw SQL queries for [primitive](https://github.com/dotnet/efcore/issues/11624) and [unmapped](https://github.com/dotnet/efcore/issues/10753) types.</span></span>  

### <a name="value-objects"></a><span data-ttu-id="7318c-215">Objetos de valor</span><span class="sxs-lookup"><span data-stu-id="7318c-215">Value objects</span></span>

<span data-ttu-id="7318c-216">Número de seguimiento: [9906](https://github.com/dotnet/efcore/issues/9906)</span><span class="sxs-lookup"><span data-stu-id="7318c-216">Tracked by [#9906](https://github.com/dotnet/efcore/issues/9906)</span></span>

<span data-ttu-id="7318c-217">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-217">Status: Not started</span></span>

<span data-ttu-id="7318c-218">Talla de camiseta: Medium</span><span class="sxs-lookup"><span data-stu-id="7318c-218">T-shirt size: Medium</span></span>

<span data-ttu-id="7318c-219">Anteriormente, la vista de equipo era la propietaria de las entidades, destinadas a la [compatibilidad agregada](https://www.martinfowler.com/bliki/DDD_Aggregate.html), lo que también sería una aproximación razonable a los [objetos de valor](https://www.martinfowler.com/bliki/ValueObject.html).</span><span class="sxs-lookup"><span data-stu-id="7318c-219">It was previously the team view that owned entities, intended for [aggregate support](https://www.martinfowler.com/bliki/DDD_Aggregate.html), would also be a reasonable approximation to [value objects](https://www.martinfowler.com/bliki/ValueObject.html).</span></span> <span data-ttu-id="7318c-220">La experiencia ha demostrado que este no es el caso.</span><span class="sxs-lookup"><span data-stu-id="7318c-220">Experience has shown this not to be the case.</span></span> <span data-ttu-id="7318c-221">Por tanto, en EF Core 6.0 está previsto introducir una mejor experiencia centrada en las necesidades de los objetos de valor en el diseño controlado por dominios.</span><span class="sxs-lookup"><span data-stu-id="7318c-221">Therefore, in EF Core 6.0 we plan to introduce a better experience focused on the needs of value objects in domain-driven design.</span></span> <span data-ttu-id="7318c-222">Este enfoque se basará en convertidores de valores, en lugar de entidades de propiedad.</span><span class="sxs-lookup"><span data-stu-id="7318c-222">This approach will be based on value converters rather than owned entities.</span></span>

<span data-ttu-id="7318c-223">Inicialmente, el ámbito de este trabajo es permitir [convertidores de valores que se asignan a varias columnas](https://github.com/dotnet/efcore/issues/13947).</span><span class="sxs-lookup"><span data-stu-id="7318c-223">This work is initially scoped to allow [value converters which map to multiple columns](https://github.com/dotnet/efcore/issues/13947).</span></span> <span data-ttu-id="7318c-224">Es posible que se incorpore soporte adicional en función de los comentarios durante el lanzamiento.</span><span class="sxs-lookup"><span data-stu-id="7318c-224">We may pull in additional support based on feedback during the release.</span></span>

### <a name="cosmos-database-provider"></a><span data-ttu-id="7318c-225">Proveedor de base de datos Cosmos</span><span class="sxs-lookup"><span data-stu-id="7318c-225">Cosmos database provider</span></span>

<span data-ttu-id="7318c-226">Seguimiento por [incidencias etiquetadas con "area-cosmos" y en el hito 6.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)</span><span class="sxs-lookup"><span data-stu-id="7318c-226">Tracked by [issues labeled with 'area-cosmos' and in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)</span></span>

<span data-ttu-id="7318c-227">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-227">Status: Not started</span></span>

<span data-ttu-id="7318c-228">Talla de camiseta: Grande</span><span class="sxs-lookup"><span data-stu-id="7318c-228">T-shirt size: Large</span></span>

<span data-ttu-id="7318c-229">De forma activa se recopilan comentarios sobre las mejoras que se deben llevar a cabo en el proveedor Cosmos en EF Core 6.0.</span><span class="sxs-lookup"><span data-stu-id="7318c-229">We are actively gathering feedback on which improvements to make to the Cosmos provider in EF Core 6.0.</span></span> <span data-ttu-id="7318c-230">Este documento se actualizará a medida que se obtenga más información.</span><span class="sxs-lookup"><span data-stu-id="7318c-230">We will update this document as we learn more.</span></span> <span data-ttu-id="7318c-231">Por ahora, asegúrese de votar (👍) por las características de Cosmos que necesite.</span><span class="sxs-lookup"><span data-stu-id="7318c-231">For now, please make sure to vote (👍) for the Cosmos features that you need.</span></span>

### <a name="expose-model-building-conventions-to-applications"></a><span data-ttu-id="7318c-232">Exposición de convenciones de creación de modelos para las aplicaciones</span><span class="sxs-lookup"><span data-stu-id="7318c-232">Expose model building conventions to applications</span></span>

<span data-ttu-id="7318c-233">Número de seguimiento: [214](https://github.com/dotnet/efcore/issues/214)</span><span class="sxs-lookup"><span data-stu-id="7318c-233">Tracked by [#214](https://github.com/dotnet/efcore/issues/214)</span></span>

<span data-ttu-id="7318c-234">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-234">Status: Not started</span></span>

<span data-ttu-id="7318c-235">Talla de camiseta: mediana</span><span class="sxs-lookup"><span data-stu-id="7318c-235">T-shirt size: medium</span></span>

<span data-ttu-id="7318c-236">EF Core usa un conjunto de convenciones para crear un modelo a partir de tipos de .NET.</span><span class="sxs-lookup"><span data-stu-id="7318c-236">EF Core uses a set of conventions for building a model from .NET types.</span></span> <span data-ttu-id="7318c-237">Actualmente, estas convenciones las controla el proveedor de base de datos.</span><span class="sxs-lookup"><span data-stu-id="7318c-237">These conventions are currently controlled by the database provider.</span></span> <span data-ttu-id="7318c-238">En EF Core 6.0 está previsto permitir que las aplicaciones se conecten a estas convenciones y las cambien.</span><span class="sxs-lookup"><span data-stu-id="7318c-238">In EF Core 6.0, we intend to allow applications to hook into and change these conventions.</span></span>

### <a name="zero-bug-balance-zbb"></a><span data-ttu-id="7318c-239">Equilibrio de cero errores (ZBB)</span><span class="sxs-lookup"><span data-stu-id="7318c-239">Zero bug balance (ZBB)</span></span>

<span data-ttu-id="7318c-240">Seguimiento por [incidencias etiquetadas con `type-bug` en el hito 6.0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="7318c-240">Tracked by [issues labeled with `type-bug` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="7318c-241">Estado: En curso</span><span class="sxs-lookup"><span data-stu-id="7318c-241">Status: In-progress</span></span>

<span data-ttu-id="7318c-242">Talla de camiseta: Grande</span><span class="sxs-lookup"><span data-stu-id="7318c-242">T-shirt size: Large</span></span>

<span data-ttu-id="7318c-243">Está previsto corregir todos los errores pendientes durante el período de tiempo de EF Core 6.0.</span><span class="sxs-lookup"><span data-stu-id="7318c-243">We plan to fix all outstanding bugs during the EF Core 6.0 time frame.</span></span> <span data-ttu-id="7318c-244">Algunos aspectos que se deben tener en cuenta:</span><span class="sxs-lookup"><span data-stu-id="7318c-244">Some things to keep in mind:</span></span>

- <span data-ttu-id="7318c-245">Esto se aplica específicamente a las incidencias con la etiqueta [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug).</span><span class="sxs-lookup"><span data-stu-id="7318c-245">This specifically applies to issues labeled [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug).</span></span>
- <span data-ttu-id="7318c-246">Habrá excepciones, como cuando el error requiere un cambio de diseño o una característica nueva para corregirlo correctamente.</span><span class="sxs-lookup"><span data-stu-id="7318c-246">There will be exceptions, such as when the bug requires a design change or new feature to fix properly.</span></span> <span data-ttu-id="7318c-247">Estas incidencias se marcarán con la etiqueta `blocked`.</span><span class="sxs-lookup"><span data-stu-id="7318c-247">These issues will be marked with the `blocked` label.</span></span>
- <span data-ttu-id="7318c-248">Los errores se puntuarán en función del riesgo cuando sea necesario, lo normal a medida que se aproxima una versión de GA/RTM.</span><span class="sxs-lookup"><span data-stu-id="7318c-248">We will punt bugs based on risk when needed as is normal as we get close to a GA/RTM release.</span></span>

### <a name="miscellaneous-features"></a><span data-ttu-id="7318c-249">Características varias</span><span class="sxs-lookup"><span data-stu-id="7318c-249">Miscellaneous features</span></span>

<span data-ttu-id="7318c-250">Seguimiento por [incidencias etiquetadas con `type-enhancement` en el hito 6.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)</span><span class="sxs-lookup"><span data-stu-id="7318c-250">Tracked by [issues labeled with `type-enhancement` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)</span></span>

<span data-ttu-id="7318c-251">Estado: En curso</span><span class="sxs-lookup"><span data-stu-id="7318c-251">Status: In-progress</span></span>

<span data-ttu-id="7318c-252">Talla de camiseta: Grande</span><span class="sxs-lookup"><span data-stu-id="7318c-252">T-shirt size: Large</span></span>

<span data-ttu-id="7318c-253">Las características varias planeadas para EF 6.0 incluyen, entre otras, las siguientes:</span><span class="sxs-lookup"><span data-stu-id="7318c-253">Miscellaneous features planned for EF 6.0 include, but are not limited to:</span></span>

- [<span data-ttu-id="7318c-254">División de consultas para colecciones que no son de navegación</span><span class="sxs-lookup"><span data-stu-id="7318c-254">Split query for non-navigation collections</span></span>](https://github.com/dotnet/efcore/issues/21234)
- [<span data-ttu-id="7318c-255">Detección de tablas de combinación simples en técnicas de ingeniería inversa y creación de relaciones de varios a varios</span><span class="sxs-lookup"><span data-stu-id="7318c-255">Detect simple join tables in reverse engineering and create many-to-many relationships</span></span>](https://github.com/dotnet/efcore/issues/22475)
- [<span data-ttu-id="7318c-256">Finalización de la búsqueda de texto completo o libre en SQLite y SQL Server</span><span class="sxs-lookup"><span data-stu-id="7318c-256">Complete full/free-text search on SQLite and SQL Server</span></span>](https://github.com/dotnet/efcore/issues/4823)
- [<span data-ttu-id="7318c-257">Índices espaciales de SQL Server</span><span class="sxs-lookup"><span data-stu-id="7318c-257">SQL Server spatial indexes</span></span>](https://github.com/dotnet/efcore/issues/12538)
- [<span data-ttu-id="7318c-258">Mecanismo o API para especificar una conversión predeterminada para cualquier propiedad de un tipo determinado en el modelo</span><span class="sxs-lookup"><span data-stu-id="7318c-258">Mechanism/API to specify a default conversion for any property of a given type in the model</span></span>](https://github.com/dotnet/efcore/issues/10784)
- [<span data-ttu-id="7318c-259">Uso de la nueva API de procesamiento por lotes de ADO.NET</span><span class="sxs-lookup"><span data-stu-id="7318c-259">Use the new batching API from ADO.NET</span></span>](https://github.com/dotnet/efcore/issues/18990)

## <a name="net-integration"></a><span data-ttu-id="7318c-260">integración en .NET</span><span class="sxs-lookup"><span data-stu-id="7318c-260">.NET integration</span></span>

<span data-ttu-id="7318c-261">El equipo de EF Core también trabaja en varias tecnologías relacionadas pero independientes.</span><span class="sxs-lookup"><span data-stu-id="7318c-261">The EF Core team also works on several related but independent technologies.</span></span> <span data-ttu-id="7318c-262">En concreto, está previsto trabajar en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7318c-262">In particular, we plan to work on:</span></span>

### <a name="enhancements-to-systemdata"></a><span data-ttu-id="7318c-263">Mejoras en System.Data</span><span class="sxs-lookup"><span data-stu-id="7318c-263">Enhancements to System.Data</span></span>

<span data-ttu-id="7318c-264">Seguimiento por [incidencias en el repositorio dotnet\runtime etiquetadas con `area-System.Data` en el hito 6.0](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)</span><span class="sxs-lookup"><span data-stu-id="7318c-264">Tracked by [issues in the dotnet\runtime repo labeled with `area-System.Data` in the 6.0 milestone](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)</span></span>

<span data-ttu-id="7318c-265">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-265">Status: Not started</span></span>

<span data-ttu-id="7318c-266">Talla de camiseta: Grande</span><span class="sxs-lookup"><span data-stu-id="7318c-266">T-shirt size: Large</span></span>

<span data-ttu-id="7318c-267">Este trabajo incluye lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="7318c-267">This work includes:</span></span>

- <span data-ttu-id="7318c-268">Implementación de la nueva [API de procesamiento por lotes](https://github.com/dotnet/runtime/issues/28633).</span><span class="sxs-lookup"><span data-stu-id="7318c-268">Implementation of the new [batching API](https://github.com/dotnet/runtime/issues/28633).</span></span>
- <span data-ttu-id="7318c-269">Trabajo continuado con otros equipos de .NET y la comunidad para comprender y desarrollar ADO.NET.</span><span class="sxs-lookup"><span data-stu-id="7318c-269">Continued work with other .NET teams and the community to understand and evolve ADO.NET.</span></span>
- <span data-ttu-id="7318c-270">[Uso estándar de DiagnosticSource para el seguimiento en componentes System.Data.\*](https://github.com/dotnet/runtime/issues/22336).</span><span class="sxs-lookup"><span data-stu-id="7318c-270">[Standardize on DiagnosticSource for tracing in System.Data.\* components](https://github.com/dotnet/runtime/issues/22336).</span></span>

### <a name="enhancements-to-microsoftdatasqlite"></a><span data-ttu-id="7318c-271">Mejoras en Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="7318c-271">Enhancements to Microsoft.Data.Sqlite</span></span>

<span data-ttu-id="7318c-272">Seguimiento por [incidencias etiquetadas con `type-enhancement` y `area-adonet-sqlite` en el hito 6.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)</span><span class="sxs-lookup"><span data-stu-id="7318c-272">Tracked by [issues labeled with `type-enhancement` and `area-adonet-sqlite` in the 6.0 milestone](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)</span></span>

<span data-ttu-id="7318c-273">Estado: En curso</span><span class="sxs-lookup"><span data-stu-id="7318c-273">Status: In-progress</span></span>

<span data-ttu-id="7318c-274">Talla de camiseta: Medium</span><span class="sxs-lookup"><span data-stu-id="7318c-274">T-shirt size: Medium</span></span>

<span data-ttu-id="7318c-275">Se han planificado varias mejoras pequeñas para Microsoft.Data.SQLite, incluidas la [agrupación de conexiones](https://github.com/dotnet/efcore/issues/13837) y las [instrucciones preparadas](https://github.com/dotnet/efcore/issues/14044) para el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="7318c-275">Several small improvements are planned for the Microsoft.Data.Sqlite, including [connection pooling](https://github.com/dotnet/efcore/issues/13837) and [prepared statements](https://github.com/dotnet/efcore/issues/14044) for performance.</span></span>

### <a name="nullable-reference-types"></a><span data-ttu-id="7318c-276">Tipos de referencia que aceptan valores NULL</span><span class="sxs-lookup"><span data-stu-id="7318c-276">Nullable reference types</span></span>

<span data-ttu-id="7318c-277">Número de seguimiento: [14150](https://github.com/dotnet/efcore/issues/14150)</span><span class="sxs-lookup"><span data-stu-id="7318c-277">Tracked by [#14150](https://github.com/dotnet/efcore/issues/14150)</span></span>

<span data-ttu-id="7318c-278">Estado: En curso</span><span class="sxs-lookup"><span data-stu-id="7318c-278">Status: In-progress</span></span>

<span data-ttu-id="7318c-279">Talla de camiseta: Grande</span><span class="sxs-lookup"><span data-stu-id="7318c-279">T-shirt size: Large</span></span>

<span data-ttu-id="7318c-280">Se anotará el código de EF Core para usar [tipos de referencia que aceptan valores NULL](/dotnet/csharp/nullable-references).</span><span class="sxs-lookup"><span data-stu-id="7318c-280">We will annotate the EF Core code to use [nullable reference types](/dotnet/csharp/nullable-references).</span></span>

## <a name="experiments-and-investigations"></a><span data-ttu-id="7318c-281">Experimentos e investigaciones</span><span class="sxs-lookup"><span data-stu-id="7318c-281">Experiments and investigations</span></span>

<span data-ttu-id="7318c-282">Durante el período de tiempo de EF Core 6.0, el equipo de EF tiene previsto invertir en la experimentación e investigación en dos áreas.</span><span class="sxs-lookup"><span data-stu-id="7318c-282">The EF team is planning to invest time during the EF Core 6.0 timeframe experimenting and investigating in two areas.</span></span> <span data-ttu-id="7318c-283">Se trata de un proceso de aprendizaje y, como tal, no se prevé ninguna entrega concreta para la versión 6.0.</span><span class="sxs-lookup"><span data-stu-id="7318c-283">This is a learning process and as such no concrete deliverables are planned for the 6.0 release.</span></span>

### <a name="sqlservercore"></a><span data-ttu-id="7318c-284">SqlServer.Core</span><span class="sxs-lookup"><span data-stu-id="7318c-284">SqlServer.Core</span></span>

<span data-ttu-id="7318c-285">Seguimiento en el [repositorio de laboratorios de datos de .NET](https://github.com/dotnet/datalab/)</span><span class="sxs-lookup"><span data-stu-id="7318c-285">Tracked in the [.NET Data Lab repo](https://github.com/dotnet/datalab/)</span></span>

<span data-ttu-id="7318c-286">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-286">Status: Not started</span></span>

<span data-ttu-id="7318c-287">Talla de camiseta: En curso</span><span class="sxs-lookup"><span data-stu-id="7318c-287">T-shirt size: Ongoing</span></span>

<span data-ttu-id="7318c-288">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) es un proveedor de base de datos de ADO.NET con todas las características para SQL Server.</span><span class="sxs-lookup"><span data-stu-id="7318c-288">[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) is a fully-featured ADO.NET database provider for SQL Server.</span></span> <span data-ttu-id="7318c-289">Admite una amplia gama de características de SQL Server, tanto en .NET Core como en .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="7318c-289">It supports a broad range of SQL Server features on both .NET Core and .NET Framework.</span></span> <span data-ttu-id="7318c-290">Pero también es un código base grande y antiguo con muchas interacciones complejas entre sus comportamientos.</span><span class="sxs-lookup"><span data-stu-id="7318c-290">However, it is also a large and old codebase with many complex interactions between its behaviors.</span></span> <span data-ttu-id="7318c-291">Esto dificulta la investigación de las posibles ventajas que se pueden realizar con las nuevas características de .NET Core.</span><span class="sxs-lookup"><span data-stu-id="7318c-291">This makes it difficult to investigate the potential gains the could be made using newer .NET Core features.</span></span> <span data-ttu-id="7318c-292">Por tanto, se va a iniciar un experimento en colaboración con la comunidad para determinar las posibilidades de un controlador de SQL Server de alta rendimiento para .NET.</span><span class="sxs-lookup"><span data-stu-id="7318c-292">Therefore, we are starting an experiment in collaboration with the community to determine what potential there is for a highly performing SQL Server driver for .NET.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7318c-293">La inversión en Microsoft.Data.SqlClient no va a cambiar.</span><span class="sxs-lookup"><span data-stu-id="7318c-293">Investment in Microsoft.Data.SqlClient is not changing.</span></span> <span data-ttu-id="7318c-294">Seguirá siendo la forma recomendada de conectarse a SQL Server y SQL de Azure, tanto con EF Core como sin EF Core.</span><span class="sxs-lookup"><span data-stu-id="7318c-294">It will continue to be the recommended way to connect to SQL Server and SQL Azure, both with and without EF Core.</span></span> <span data-ttu-id="7318c-295">Seguirá admitiendo nuevas características de SQL Server a medida que se introduzcan.</span><span class="sxs-lookup"><span data-stu-id="7318c-295">It will continue to support new SQL Server features as they are introduced.</span></span>

### <a name="graphql"></a><span data-ttu-id="7318c-296">GraphQL</span><span class="sxs-lookup"><span data-stu-id="7318c-296">GraphQL</span></span>

<span data-ttu-id="7318c-297">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-297">Status: Not started</span></span>

<span data-ttu-id="7318c-298">Talla de camiseta: En curso</span><span class="sxs-lookup"><span data-stu-id="7318c-298">T-shirt size: Ongoing</span></span>

<span data-ttu-id="7318c-299">En los últimos años [GraphQL](https://graphql.org/) ha cobrado importancia en una variedad de plataformas.</span><span class="sxs-lookup"><span data-stu-id="7318c-299">[GraphQL](https://graphql.org/) has been gaining traction over the last few years across a variety of platforms.</span></span> <span data-ttu-id="7318c-300">Estás previsto investigar el espacio y encontrar formas de mejorar la experiencia con .NET.</span><span class="sxs-lookup"><span data-stu-id="7318c-300">We plan to investigate the space and find ways to improve the experience with .NET.</span></span> <span data-ttu-id="7318c-301">Esto implicará trabajar con la comunidad para comprender y respaldar el ecosistema existente.</span><span class="sxs-lookup"><span data-stu-id="7318c-301">This will involve working with the community on understanding and supporting the existing ecosystem.</span></span> <span data-ttu-id="7318c-302">También puede implicar una inversión específica por parte de Microsoft, ya sea en forma de contribuciones a trabajos existentes o en el desarrollo de elementos gratuitos en la pila de Microsoft.</span><span class="sxs-lookup"><span data-stu-id="7318c-302">It may also involve specific investment from Microsoft, either in the form of contributions to existing work or in developing complimentary pieces in the Microsoft stack.</span></span>

### <a name="dataverse-formerly-common-data-services"></a><span data-ttu-id="7318c-303">DataVerse (anteriormente Data Services comunes)</span><span class="sxs-lookup"><span data-stu-id="7318c-303">DataVerse (formerly Common Data Services)</span></span>

<span data-ttu-id="7318c-304">Estado: Sin iniciar</span><span class="sxs-lookup"><span data-stu-id="7318c-304">Status: Not started</span></span>

<span data-ttu-id="7318c-305">Talla de camiseta: En curso</span><span class="sxs-lookup"><span data-stu-id="7318c-305">T-shirt size: Ongoing</span></span>

<span data-ttu-id="7318c-306">[DataVerse](/powerapps/maker/data-platform/data-platform-intro) es un almacén de datos basado en columnas diseñado para el desarrollo rápido de aplicaciones empresariales.</span><span class="sxs-lookup"><span data-stu-id="7318c-306">[DataVerse](/powerapps/maker/data-platform/data-platform-intro) is a column-based data store designed for rapid development of business applications.</span></span> <span data-ttu-id="7318c-307">Controla automáticamente tipos de datos complejos como los objetos binarios (BLOB) y tiene entidades y relaciones integradas como organizaciones y contactos.</span><span class="sxs-lookup"><span data-stu-id="7318c-307">It automatically handles complex data types like binary objects (BLOBs) and has built-in entities and relationships like organizations and contacts.</span></span> <span data-ttu-id="7318c-308">Existe un SDK, pero los desarrolladores pueden beneficiarse de tener un proveedor de EF Core para usar consultas LINQ avanzadas, aprovechar las ventajas de la unidad de trabajo y tener una API de acceso a datos coherente.</span><span class="sxs-lookup"><span data-stu-id="7318c-308">An SDK exists but developers may benefit from having an EF Core provider to use advanced LINQ queries, take advantage of unit of work and have a consistent data access API.</span></span> <span data-ttu-id="7318c-309">El equipo considerará diferentes maneras de mejorar la experiencia de conexión a DataVerse para los desarrolladores de .NET.</span><span class="sxs-lookup"><span data-stu-id="7318c-309">The team will consider different ways to improve the .NET developer experience connecting to DataVerse.</span></span>

## <a name="below-the-cut-line"></a><span data-ttu-id="7318c-310">Por debajo de la línea de corte</span><span class="sxs-lookup"><span data-stu-id="7318c-310">Below-the-cut-line</span></span>

<span data-ttu-id="7318c-311">Seguimiento por [problemas etiquetados con `consider-for-current-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)</span><span class="sxs-lookup"><span data-stu-id="7318c-311">Tracked by [issues labeled with `consider-for-current-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)</span></span>

<span data-ttu-id="7318c-312">Se trata de correcciones de errores y mejoras **no** programadas actualmente para la versión 6.0, pero que se examinarán en función de los comentarios a lo largo del lanzamiento junto con el progreso realizado en el trabajo anterior.</span><span class="sxs-lookup"><span data-stu-id="7318c-312">These are bug fixes and enhancements that are **not** currently scheduled for the 6.0 release, but we will look at based on feedback throughout the release together with progress made on the work above.</span></span> <span data-ttu-id="7318c-313">Estas incidencias se pueden incorporar en EF Core 6.0 y se convierten automáticamente en candidatas para la próxima versión.</span><span class="sxs-lookup"><span data-stu-id="7318c-313">These issues may be pulled in to EF Core 6.0, and automatically become candidates for the next release.</span></span>

<span data-ttu-id="7318c-314">Además, durante la planeación siempre se tienen en cuenta los [problemas más votados](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="7318c-314">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="7318c-315">Excluir cualquiera de estos problemas de una versión siempre es complicado, pero necesitamos un plan realista para los recursos que tenemos.</span><span class="sxs-lookup"><span data-stu-id="7318c-315">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span> <span data-ttu-id="7318c-316">Asegúrese de que ha votado (👍) por las características que necesita.</span><span class="sxs-lookup"><span data-stu-id="7318c-316">Make sure you have voted (👍) for the features you need.</span></span>

## <a name="suggestions"></a><span data-ttu-id="7318c-317">Sugerencias</span><span class="sxs-lookup"><span data-stu-id="7318c-317">Suggestions</span></span>

<span data-ttu-id="7318c-318">Sus comentarios sobre la planeación son importantes.</span><span class="sxs-lookup"><span data-stu-id="7318c-318">Your feedback on planning is important.</span></span> <span data-ttu-id="7318c-319">La mejor manera de indicar la importancia de una incidencia consiste en votar (👍) por esa incidencia en GitHub.</span><span class="sxs-lookup"><span data-stu-id="7318c-319">The best way to indicate the importance of an issue is to vote (👍) for that issue on GitHub.</span></span> <span data-ttu-id="7318c-320">Estos datos se introducirán después en el [proceso de planeación](xref:core/what-is-new/release-planning) de la próxima versión.</span><span class="sxs-lookup"><span data-stu-id="7318c-320">This data will then feed into the [planning process](xref:core/what-is-new/release-planning) for the next release.</span></span>
