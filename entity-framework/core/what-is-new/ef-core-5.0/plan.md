---
title: Plan para Entity Framework Core 5.0
description: Características planeadas para Entity Framework Core 5.0
author: ajcvickers
ms.date: 08/22/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: 5ac7a48a70959b625b7c602ee48dc08a360bee73
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618669"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="de6a7-103">Plan para Entity Framework Core 5.0</span><span class="sxs-lookup"><span data-stu-id="de6a7-103">Plan for Entity Framework Core 5.0</span></span>

<span data-ttu-id="de6a7-104">Como se describe en el [proceso de planeamiento](xref:core/what-is-new/release-planning), se ha recopilado la información de las partes interesadas en un plan provisional para la versión EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-104">As described in the [planning process](xref:core/what-is-new/release-planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="de6a7-105">Este plan sigue siendo un trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="de6a7-105">This plan is still a work-in-progress.</span></span> <span data-ttu-id="de6a7-106">Nada de esto es un compromiso.</span><span class="sxs-lookup"><span data-stu-id="de6a7-106">Nothing here is a commitment.</span></span> <span data-ttu-id="de6a7-107">Este plan es un punto de partida que evolucionará a medida que se obtenga más información.</span><span class="sxs-lookup"><span data-stu-id="de6a7-107">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="de6a7-108">Es posible que algunos aspectos no planeados en la actualidad se incorporen a la versión 5.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-108">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="de6a7-109">Es posible que algunos aspectos planeados en la actualidad se eliminen de la versión 5.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-109">Some things currently planned for 5.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="de6a7-110">Información general</span><span class="sxs-lookup"><span data-stu-id="de6a7-110">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="de6a7-111">Número de versión y fecha de lanzamiento</span><span class="sxs-lookup"><span data-stu-id="de6a7-111">Version number and release date</span></span>

<span data-ttu-id="de6a7-112">En la actualidad, el lanzamiento de EF Core 5.0 está programado al [mismo tiempo que .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="de6a7-112">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="de6a7-113">Se ha elegido la versión "5.0" para la alineación con .NET 5.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-113">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="de6a7-114">Plataformas compatibles</span><span class="sxs-lookup"><span data-stu-id="de6a7-114">Supported platforms</span></span>

<span data-ttu-id="de6a7-115">Está previsto que EF Core 5.0 se ejecute en cualquier plataforma de .NET Standard 2.1, incluido .NET 5.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-115">EF Core 5.0 is planned to run on any .NET Standard 2.1 platform, including .NET 5.0.</span></span> <span data-ttu-id="de6a7-116">Esto forma parte de la [convergencia más general a nivel de .NET de las plataformas a .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="de6a7-116">This is part of the more general .NET wide [convergence of platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span>

<span data-ttu-id="de6a7-117">EF Core 5.0 no se ejecutará en .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="de6a7-117">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="de6a7-118">Cambios importantes</span><span class="sxs-lookup"><span data-stu-id="de6a7-118">Breaking changes</span></span>

<span data-ttu-id="de6a7-119">EF Core 5.0 contendrá algunos [cambios importantes](xref:core/what-is-new/ef-core-5.0/breaking-changes), pero serán mucho menos drásticos que en el caso de EF Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-119">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="de6a7-120">El objetivo es permitir que la gran mayoría de las aplicaciones se actualicen sin interrupciones.</span><span class="sxs-lookup"><span data-stu-id="de6a7-120">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="de6a7-121">Se espera que haya algunos cambios importantes para los proveedores de bases de datos, especialmente relacionados con la compatibilidad con TPT.</span><span class="sxs-lookup"><span data-stu-id="de6a7-121">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="de6a7-122">Pero se espera que el trabajo para actualizar un proveedor para 5.0 sea menor que el necesario para 3.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-122">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="de6a7-123">Temas</span><span class="sxs-lookup"><span data-stu-id="de6a7-123">Themes</span></span>

<span data-ttu-id="de6a7-124">Hemos extraído algunas áreas o temas importantes que formarán la base de las grandes inversiones en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-124">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="de6a7-125">Asignación de varios a varios totalmente transparente por convención</span><span class="sxs-lookup"><span data-stu-id="de6a7-125">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="de6a7-126">Jefes de desarrollo: @smitpatel, @AndriySvyryd y @lajones</span><span class="sxs-lookup"><span data-stu-id="de6a7-126">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="de6a7-127">Número de seguimiento: [10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="de6a7-127">Tracked by [#10508](https://github.com/aspnet/EntityFrameworkCore/issues/10508)</span></span>

<span data-ttu-id="de6a7-128">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="de6a7-128">T-shirt size: L</span></span>

<span data-ttu-id="de6a7-129">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="de6a7-129">Status: Done</span></span>

<span data-ttu-id="de6a7-130">Varios a varios es la [característica más solicitada](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (506 votos aproximadamente) en el trabajo pendiente de GitHub.</span><span class="sxs-lookup"><span data-stu-id="de6a7-130">Many-to-many is the [most requested feature](https://github.com/aspnet/EntityFrameworkCore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="de6a7-131">La compatibilidad con las relaciones varios a varios se puede dividir en tres áreas principales:</span><span class="sxs-lookup"><span data-stu-id="de6a7-131">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="de6a7-132">Propiedades de navegación por omisión, que se describen en el siguiente tema.</span><span class="sxs-lookup"><span data-stu-id="de6a7-132">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="de6a7-133">Tipos de entidad de contenedor de propiedades.</span><span class="sxs-lookup"><span data-stu-id="de6a7-133">Property-bag entity types.</span></span> <span data-ttu-id="de6a7-134">Permiten usar un tipo CLR estándar (por ejemplo, `Dictionary`) para las instancias de entidad, de modo que no se necesite un tipo CLR explícito para cada tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="de6a7-134">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="de6a7-135">Número de seguimiento: [9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span><span class="sxs-lookup"><span data-stu-id="de6a7-135">Tracked by [#9914](https://github.com/aspnet/EntityFrameworkCore/issues/9914).</span></span>
* <span data-ttu-id="de6a7-136">Facilitar la configuración de relaciones varios a varios.</span><span class="sxs-lookup"><span data-stu-id="de6a7-136">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="de6a7-137">Además de la compatibilidad con la navegación por omisión, ahora vamos a incorporar estas otras áreas de varios a varios a EF Core 5.0 para proporcionar una experiencia completa.</span><span class="sxs-lookup"><span data-stu-id="de6a7-137">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="de6a7-138">Propiedades de navegación de varios a varios (también denominado "omitir navegaciones")</span><span class="sxs-lookup"><span data-stu-id="de6a7-138">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="de6a7-139">Jefes de desarrollo: @smitpatel y @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="de6a7-139">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="de6a7-140">Seguimiento realizado por [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="de6a7-140">Tracked by [#19003](https://github.com/aspnet/EntityFrameworkCore/issues/19003)</span></span>

<span data-ttu-id="de6a7-141">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="de6a7-141">T-shirt size: L</span></span>

<span data-ttu-id="de6a7-142">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="de6a7-142">Status: Done</span></span>

<span data-ttu-id="de6a7-143">Tal y como se describe en el primer tema, la compatibilidad de varios a varios tiene varios aspectos que cabe destacar.</span><span class="sxs-lookup"><span data-stu-id="de6a7-143">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="de6a7-144">En este tema se realiza un seguimiento específico del uso de las navegaciones por omisión.</span><span class="sxs-lookup"><span data-stu-id="de6a7-144">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="de6a7-145">Creemos que el principal obstáculo para los que quieren que se admitan las relaciones varios a varios es la imposibilidad de usar relaciones "naturales", sin hacer referencia a la tabla de combinación, en la lógica de negocios, como las consultas.</span><span class="sxs-lookup"><span data-stu-id="de6a7-145">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="de6a7-146">Es posible que el tipo de entidad de tabla de combinación siga existiendo, pero no debería interferir con la lógica de negocios.</span><span class="sxs-lookup"><span data-stu-id="de6a7-146">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="de6a7-147">Asignación de herencia de tabla por tipo (TPT)</span><span class="sxs-lookup"><span data-stu-id="de6a7-147">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="de6a7-148">Jefe de desarrollo: @AndriySvyryd y @smitpatel</span><span class="sxs-lookup"><span data-stu-id="de6a7-148">Lead developer: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="de6a7-149">Seguimiento realizado por [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="de6a7-149">Tracked by [#2266](https://github.com/aspnet/EntityFrameworkCore/issues/2266)</span></span>

<span data-ttu-id="de6a7-150">Talla de camiseta: XL</span><span class="sxs-lookup"><span data-stu-id="de6a7-150">T-shirt size: XL</span></span>

<span data-ttu-id="de6a7-151">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="de6a7-151">Status: Done</span></span>

<span data-ttu-id="de6a7-152">TPT se va a incluir porque se trata de una característica muy solicitada (aproximadamente 289 votos; en tercera posición) y porque requiere algunos cambios de bajo nivel que consideramos adecuados para la naturaleza fundamental del plan general de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="de6a7-152">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="de6a7-153">Esperamos que esto genere cambios importantes para los proveedores de bases de datos, aunque deberían ser mucho menos graves que los necesarios para la versión 3.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-153">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="de6a7-154">Inclusión filtrada</span><span class="sxs-lookup"><span data-stu-id="de6a7-154">Filtered Include</span></span>

<span data-ttu-id="de6a7-155">Jefe de desarrollo: @maumar</span><span class="sxs-lookup"><span data-stu-id="de6a7-155">Lead developer: @maumar</span></span>

<span data-ttu-id="de6a7-156">Seguimiento realizado por [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="de6a7-156">Tracked by [#1833](https://github.com/aspnet/EntityFrameworkCore/issues/1833)</span></span>

<span data-ttu-id="de6a7-157">Talla de camiseta: M</span><span class="sxs-lookup"><span data-stu-id="de6a7-157">T-shirt size: M</span></span>

<span data-ttu-id="de6a7-158">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="de6a7-158">Status: Done</span></span>

<span data-ttu-id="de6a7-159">Inclusión filtrada es una característica muy solicitada (aproximadamente 376 votos; en segunda posición) que no requiere demasiado trabajo y que creemos que desbloqueará o facilitará escenarios que actualmente requieren filtros de nivel de modelo o consultas más complejas.</span><span class="sxs-lookup"><span data-stu-id="de6a7-159">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="de6a7-160">División de Include</span><span class="sxs-lookup"><span data-stu-id="de6a7-160">Split Include</span></span>

<span data-ttu-id="de6a7-161">Jefe de desarrollo: @smitpatel</span><span class="sxs-lookup"><span data-stu-id="de6a7-161">Lead developer: @smitpatel</span></span>

<span data-ttu-id="de6a7-162">Número de seguimiento: [20892](https://github.com/dotnet/efcore/issues/20892)</span><span class="sxs-lookup"><span data-stu-id="de6a7-162">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="de6a7-163">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="de6a7-163">T-shirt size: L</span></span>

<span data-ttu-id="de6a7-164">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="de6a7-164">Status: Done</span></span>

<span data-ttu-id="de6a7-165">EF Core 3.0 cambió el comportamiento predeterminado para crear una consulta SQL única para una consulta LINQ determinada.</span><span class="sxs-lookup"><span data-stu-id="de6a7-165">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="de6a7-166">Esto provocó una gran cantidad de degradaciones de rendimiento para las consultas que usan Include para varias colecciones.</span><span class="sxs-lookup"><span data-stu-id="de6a7-166">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="de6a7-167">En EF Core 5.0, se mantiene el nuevo comportamiento predeterminado.</span><span class="sxs-lookup"><span data-stu-id="de6a7-167">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="de6a7-168">Sin embargo, EF Core 5.0 ahora permite la generación de varias consultas para los métodos Include de la colección en los que tener una sola consulta provoca deficiencias en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="de6a7-168">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance.</span></span>

## <a name="required-one-to-one-dependents"></a><span data-ttu-id="de6a7-169">Dependencias de uno a uno obligatorias</span><span class="sxs-lookup"><span data-stu-id="de6a7-169">Required one-to-one dependents</span></span>

<span data-ttu-id="de6a7-170">Jefes de desarrollo: @AndriySvyryd y @smitpatel</span><span class="sxs-lookup"><span data-stu-id="de6a7-170">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="de6a7-171">Número de seguimiento: [12100](https://github.com/dotnet/efcore/issues/12100)</span><span class="sxs-lookup"><span data-stu-id="de6a7-171">Tracked by [#12100](https://github.com/dotnet/efcore/issues/12100)</span></span>

<span data-ttu-id="de6a7-172">Talla de camiseta: M</span><span class="sxs-lookup"><span data-stu-id="de6a7-172">T-shirt size: M</span></span>

<span data-ttu-id="de6a7-173">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="de6a7-173">Status: Done</span></span>

<span data-ttu-id="de6a7-174">En EF Core 3.0, todas las dependencias, incluidos los tipos de propiedad, son opcionales (por ejemplo, Person.Address puede ser NULL).</span><span class="sxs-lookup"><span data-stu-id="de6a7-174">In EF Core 3.0, all dependents, including owned types are optional (e.g. Person.Address can be null).</span></span> <span data-ttu-id="de6a7-175">En EF Core 5.0, se pueden configurar las dependencias según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="de6a7-175">In EF Core 5.0, dependents can be configured as required.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="de6a7-176">Racionalización de ToTable, ToQuery, ToView, FromSql, etc.</span><span class="sxs-lookup"><span data-stu-id="de6a7-176">Rationalize ToTable, ToQuery, ToView, FromSql, etc</span></span>

<span data-ttu-id="de6a7-177">Jefes de desarrollo: @AndriySvyryd y @smitpatel</span><span class="sxs-lookup"><span data-stu-id="de6a7-177">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="de6a7-178">Seguimiento realizado por [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="de6a7-178">Tracked by [#17270](https://github.com/aspnet/EntityFrameworkCore/issues/17270)</span></span>

<span data-ttu-id="de6a7-179">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="de6a7-179">T-shirt size: L</span></span>

<span data-ttu-id="de6a7-180">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="de6a7-180">Status: Done</span></span>

<span data-ttu-id="de6a7-181">Se han realizado avances en versiones anteriores hacia la compatibilidad con SQL sin procesar, tipos sin clave y áreas relacionadas.</span><span class="sxs-lookup"><span data-stu-id="de6a7-181">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="de6a7-182">Pero hay brechas e incoherencias en el funcionamiento en conjunto de todos los elementos.</span><span class="sxs-lookup"><span data-stu-id="de6a7-182">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="de6a7-183">El objetivo para la versión 5.0 es corregirlos y crear una buena experiencia para definir, migrar y usar otros tipos de entidades y sus consultas y artefactos de base de datos asociados.</span><span class="sxs-lookup"><span data-stu-id="de6a7-183">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="de6a7-184">Esto también puede implicar actualizaciones de la API de consulta compilada.</span><span class="sxs-lookup"><span data-stu-id="de6a7-184">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="de6a7-185">Tenga en cuenta que este elemento puede dar lugar a algunos cambios importantes en el nivel de la aplicación, ya que algunas de las funcionalidades actuales son demasiado permisivas, lo que puede conducir rápidamente a que los usuarios cometan errores.</span><span class="sxs-lookup"><span data-stu-id="de6a7-185">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="de6a7-186">Lo más probable es que parte de esta funcionalidad se bloquee y se proporcionen instrucciones sobre lo que se debe hacer en su lugar.</span><span class="sxs-lookup"><span data-stu-id="de6a7-186">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="de6a7-187">Mejoras generales de consultas</span><span class="sxs-lookup"><span data-stu-id="de6a7-187">General query enhancements</span></span>

<span data-ttu-id="de6a7-188">Jefes de desarrollo: @smitpatel y @maumar</span><span class="sxs-lookup"><span data-stu-id="de6a7-188">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="de6a7-189">Seguimiento por [problemas etiquetados con `area-query` en el hito 5.0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="de6a7-189">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="de6a7-190">Talla de camiseta: XL</span><span class="sxs-lookup"><span data-stu-id="de6a7-190">T-shirt size: XL</span></span>

<span data-ttu-id="de6a7-191">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="de6a7-191">Status: Done</span></span>

<span data-ttu-id="de6a7-192">El código de traducción de consultas se ha reescrito de forma exhaustiva para EF Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-192">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="de6a7-193">Por este motivo, el código de consulta tiene un estado mucho más robusto.</span><span class="sxs-lookup"><span data-stu-id="de6a7-193">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="de6a7-194">Para la versión 5.0, no se planean cambios importantes en las consultas más allá de los necesarios para admitir TPT y la omisión de propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="de6a7-194">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="de6a7-195">Pero se necesita un trabajo importante para corregir las deudas técnicas generadas tras la revisión de la versión 3.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-195">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="de6a7-196">También tenemos previsto corregir muchos errores e implementar pequeñas mejoras para mejorar aún más la experiencia general de las consultas.</span><span class="sxs-lookup"><span data-stu-id="de6a7-196">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="de6a7-197">Migraciones y experiencia de implementación</span><span class="sxs-lookup"><span data-stu-id="de6a7-197">Migrations and deployment experience</span></span>

<span data-ttu-id="de6a7-198">Jefes de desarrollo: @bricelam</span><span class="sxs-lookup"><span data-stu-id="de6a7-198">Lead developers: @bricelam</span></span>

<span data-ttu-id="de6a7-199">Seguimiento realizado por [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="de6a7-199">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="de6a7-200">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="de6a7-200">T-shirt size: L</span></span>

<span data-ttu-id="de6a7-201">Estado: Ámbito y trabajo hecho</span><span class="sxs-lookup"><span data-stu-id="de6a7-201">Status: Scoped/Done</span></span>

<span data-ttu-id="de6a7-202">Ámbito: la [característica de conjunto de migraciones](https://github.com/dotnet/efcore/issues/19693) se ha aplazado hasta después de la versión EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-202">Scoping: The [migrations bundles feature](https://github.com/dotnet/efcore/issues/19693) has been deferred until after the EF Core 5.0 release.</span></span> <span data-ttu-id="de6a7-203">Sin embargo, en EF Core 5.0 se incluirán [otras mejoras de destino relacionadas con las migraciones](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460).</span><span class="sxs-lookup"><span data-stu-id="de6a7-203">However, several other [targeted improvements related to migrations](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) will be included in EF Core 5.0</span></span>

<span data-ttu-id="de6a7-204">En la actualidad, muchos desarrolladores migran sus bases de datos en el momento de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="de6a7-204">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="de6a7-205">Esto es fácil, pero no se recomienda porque:</span><span class="sxs-lookup"><span data-stu-id="de6a7-205">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="de6a7-206">Es posible que varios subprocesos, procesos o servidores intenten migrar la base de datos simultáneamente</span><span class="sxs-lookup"><span data-stu-id="de6a7-206">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="de6a7-207">Es posible que las aplicaciones intenten acceder a un estado incoherente mientras se produce esta operación</span><span class="sxs-lookup"><span data-stu-id="de6a7-207">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="de6a7-208">Normalmente, no se deben conceder los permisos de base de datos para modificar el esquema para la ejecución de la aplicación</span><span class="sxs-lookup"><span data-stu-id="de6a7-208">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="de6a7-209">Es difícil revertir a un estado limpio si se produce algún error</span><span class="sxs-lookup"><span data-stu-id="de6a7-209">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="de6a7-210">Queremos ofrecer una mejor experiencia que permita migrar la base de datos de forma sencilla en el momento de la implementación.</span><span class="sxs-lookup"><span data-stu-id="de6a7-210">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="de6a7-211">Esto debería:</span><span class="sxs-lookup"><span data-stu-id="de6a7-211">This should:</span></span>

* <span data-ttu-id="de6a7-212">Funcionar en Linux, Mac y Windows</span><span class="sxs-lookup"><span data-stu-id="de6a7-212">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="de6a7-213">Ser una experiencia positiva en la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="de6a7-213">Be a good experience on the command line</span></span>
* <span data-ttu-id="de6a7-214">Admitir escenarios con contenedores</span><span class="sxs-lookup"><span data-stu-id="de6a7-214">Support scenarios with containers</span></span>
* <span data-ttu-id="de6a7-215">Funcionar con flujos y herramientas de implementación del mundo real que se usan habitualmente</span><span class="sxs-lookup"><span data-stu-id="de6a7-215">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="de6a7-216">Integrarse al menos en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="de6a7-216">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="de6a7-217">Lo más probable es que el resultado sea un gran número de pequeñas mejoras en EF Core (por ejemplo, mejores migraciones en SQLite), junto con instrucciones y colaboraciones a largo plazo con otros equipos para mejorar las experiencias de un extremo a otro que van más allá de EF exclusivamente.</span><span class="sxs-lookup"><span data-stu-id="de6a7-217">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="de6a7-218">Experiencia de las plataformas de EF Core</span><span class="sxs-lookup"><span data-stu-id="de6a7-218">EF Core platforms experience</span></span>

<span data-ttu-id="de6a7-219">Jefes de desarrollo: @roji y @bricelam</span><span class="sxs-lookup"><span data-stu-id="de6a7-219">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="de6a7-220">Seguimiento realizado por [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="de6a7-220">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="de6a7-221">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="de6a7-221">T-shirt size: L</span></span>

<span data-ttu-id="de6a7-222">Estado: Ámbito o hecho</span><span class="sxs-lookup"><span data-stu-id="de6a7-222">Status: Scope/Done</span></span>

<span data-ttu-id="de6a7-223">Ámbito: la guía y los ejemplos de la plataforma se publican para Blazor, Xamarin, WinForms y WPF.</span><span class="sxs-lookup"><span data-stu-id="de6a7-223">Scoping: Platform guidance and samples are published for Blazor, Xamarin, WinForms, and WPF.</span></span> <span data-ttu-id="de6a7-224">Xamarin y otros trabajos de AOT o enlazador ahora están previstos para EF Core 6.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-224">Xamarin and other AOT/linker work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="de6a7-225">Disponemos de instrucciones de calidad para usar EF Core en aplicaciones web tradicionales similares a MVC.</span><span class="sxs-lookup"><span data-stu-id="de6a7-225">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="de6a7-226">Las instrucciones para otras plataformas y modelos de aplicación no existen o no están actualizadas.</span><span class="sxs-lookup"><span data-stu-id="de6a7-226">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="de6a7-227">Para EF Core 5.0, el objetivo es investigar, mejorar y documentar la experiencia de uso de EF Core con:</span><span class="sxs-lookup"><span data-stu-id="de6a7-227">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="de6a7-228">Blazor</span><span class="sxs-lookup"><span data-stu-id="de6a7-228">Blazor</span></span>
* <span data-ttu-id="de6a7-229">Xamarin, incluido el uso del artículo de AOT o vinculador</span><span class="sxs-lookup"><span data-stu-id="de6a7-229">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="de6a7-230">WinForms, WPF, WinUI y posiblemente otras interfaces de usuario</span><span class="sxs-lookup"><span data-stu-id="de6a7-230">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="de6a7-231">frameworks</span><span class="sxs-lookup"><span data-stu-id="de6a7-231">frameworks</span></span>

<span data-ttu-id="de6a7-232">Lo más probable es que el resultado sea un gran número de pequeñas mejoras en EF Core, junto con instrucciones y colaboraciones a largo plazo con otros equipos para mejorar las experiencias de un extremo a otro que van más allá de EF exclusivamente.</span><span class="sxs-lookup"><span data-stu-id="de6a7-232">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="de6a7-233">Las áreas concretas que tenemos previsto examinar son las siguientes:</span><span class="sxs-lookup"><span data-stu-id="de6a7-233">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="de6a7-234">Implementación, incluida la experiencia en el uso de herramientas de EF como para migraciones</span><span class="sxs-lookup"><span data-stu-id="de6a7-234">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="de6a7-235">Modelos de aplicación, como Xamarin y Blazor, y probablemente otros</span><span class="sxs-lookup"><span data-stu-id="de6a7-235">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="de6a7-236">Experiencias de SQLite, incluida la experiencia espacial y las recompilaciones de tabla</span><span class="sxs-lookup"><span data-stu-id="de6a7-236">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="de6a7-237">Experiencias de AOT y vinculación</span><span class="sxs-lookup"><span data-stu-id="de6a7-237">AOT and linking experiences</span></span>
* <span data-ttu-id="de6a7-238">Integración de diagnósticos, incluidos los contadores de rendimiento</span><span class="sxs-lookup"><span data-stu-id="de6a7-238">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="de6a7-239">Rendimiento</span><span class="sxs-lookup"><span data-stu-id="de6a7-239">Performance</span></span>

<span data-ttu-id="de6a7-240">Jefe de desarrollo: @roji</span><span class="sxs-lookup"><span data-stu-id="de6a7-240">Lead developer: @roji</span></span>

<span data-ttu-id="de6a7-241">Seguimiento por [problemas etiquetados con `area-perf` en el hito 5.0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="de6a7-241">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="de6a7-242">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="de6a7-242">T-shirt size: L</span></span>

<span data-ttu-id="de6a7-243">Estado: Ámbito y trabajo hecho</span><span class="sxs-lookup"><span data-stu-id="de6a7-243">Status: Scoped/Done</span></span>

<span data-ttu-id="de6a7-244">Ámbito: se han completado las principales mejoras de rendimiento en el proveedor Npgsql.</span><span class="sxs-lookup"><span data-stu-id="de6a7-244">Scoping: Major performance improvements in the Npgsql provider are complete.</span></span> <span data-ttu-id="de6a7-245">Ahora se ha planificado otro trabajo de rendimiento para EF Core 6.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-245">Other performance work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="de6a7-246">Para EF Core, el plan es mejorar nuestro conjunto de pruebas comparativas de rendimiento y realizar mejoras de rendimiento dirigidas al tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="de6a7-246">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="de6a7-247">Además, tenemos previsto completar la nueva API de procesamiento por lotes de ADO.NET, que se ha creado como prototipo durante el ciclo de versiones de 3.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-247">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="de6a7-248">En el nivel de ADO.NET también se planean mejoras de rendimiento adicionales para el proveedor Npgsql.</span><span class="sxs-lookup"><span data-stu-id="de6a7-248">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="de6a7-249">Como parte de este trabajo, también está previsto agregar contadores de rendimiento de ADO.NET y EF Core, y otros diagnósticos según corresponda.</span><span class="sxs-lookup"><span data-stu-id="de6a7-249">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="de6a7-250">Documentación de arquitectura y colaboradores</span><span class="sxs-lookup"><span data-stu-id="de6a7-250">Architectural/contributor documentation</span></span>

<span data-ttu-id="de6a7-251">Jefe de documentación: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="de6a7-251">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="de6a7-252">Seguimiento realizado por [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="de6a7-252">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="de6a7-253">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="de6a7-253">T-shirt size: L</span></span>

<span data-ttu-id="de6a7-254">Estado: Cut</span><span class="sxs-lookup"><span data-stu-id="de6a7-254">Status: Cut</span></span>

<span data-ttu-id="de6a7-255">La idea es facilitar la comprensión de lo que sucede dentro de EF Core.</span><span class="sxs-lookup"><span data-stu-id="de6a7-255">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="de6a7-256">Esto puede ser útil para cualquiera que utilice EF Core, pero la motivación principal es facilitarlo para los usuarios externos:</span><span class="sxs-lookup"><span data-stu-id="de6a7-256">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="de6a7-257">Contribuir al código de EF Core</span><span class="sxs-lookup"><span data-stu-id="de6a7-257">Contribute to the EF Core code</span></span>
* <span data-ttu-id="de6a7-258">Crear proveedores de bases de datos</span><span class="sxs-lookup"><span data-stu-id="de6a7-258">Create database providers</span></span>
* <span data-ttu-id="de6a7-259">Compilar otras extensiones</span><span class="sxs-lookup"><span data-stu-id="de6a7-259">Build other extensions</span></span>

<span data-ttu-id="de6a7-260">Actualización: Desafortunadamente, este plan era demasiado ambicioso.</span><span class="sxs-lookup"><span data-stu-id="de6a7-260">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="de6a7-261">Seguimos creyendo que es un aspecto importante, pero lamentablemente no se incluirá en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-261">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="de6a7-262">Documentación de Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="de6a7-262">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="de6a7-263">Jefe de documentación: @bricelam</span><span class="sxs-lookup"><span data-stu-id="de6a7-263">Lead documenter: @bricelam</span></span>

<span data-ttu-id="de6a7-264">Seguimiento realizado por [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="de6a7-264">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="de6a7-265">Talla de camiseta: M</span><span class="sxs-lookup"><span data-stu-id="de6a7-265">T-shirt size: M</span></span>

<span data-ttu-id="de6a7-266">Estado: Completado.</span><span class="sxs-lookup"><span data-stu-id="de6a7-266">Status: Completed.</span></span> <span data-ttu-id="de6a7-267">La nueva documentación está [activa en el sitio de documentación de Microsoft](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span><span class="sxs-lookup"><span data-stu-id="de6a7-267">The new documentation is [live on the Microsoft docs site](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="de6a7-268">El equipo de EF también posee el proveedor de ADO.NET Microsoft.Data.Sqlite.</span><span class="sxs-lookup"><span data-stu-id="de6a7-268">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="de6a7-269">Tenemos previsto documentar completamente este proveedor como parte de la versión 5.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-269">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="de6a7-270">Documentación general</span><span class="sxs-lookup"><span data-stu-id="de6a7-270">General documentation</span></span>

<span data-ttu-id="de6a7-271">Jefe de documentación: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="de6a7-271">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="de6a7-272">Seguimiento mediante [problemas en el repositorio de documentación del hito 5.0](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="de6a7-272">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="de6a7-273">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="de6a7-273">T-shirt size: L</span></span>

<span data-ttu-id="de6a7-274">Estado: En curso</span><span class="sxs-lookup"><span data-stu-id="de6a7-274">Status: In-progress</span></span>

<span data-ttu-id="de6a7-275">Ya se ha iniciado el proceso de actualización de la documentación de las versiones 3.0 y 3.1.</span><span class="sxs-lookup"><span data-stu-id="de6a7-275">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="de6a7-276">También se está trabajando en:</span><span class="sxs-lookup"><span data-stu-id="de6a7-276">We are also working on:</span></span>

* <span data-ttu-id="de6a7-277">Una revisión de la documentación de introducción para que sea más fácil de seguir</span><span class="sxs-lookup"><span data-stu-id="de6a7-277">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
* <span data-ttu-id="de6a7-278">La reorganización de la documentación para facilitar la búsqueda y la adición de referencias cruzadas</span><span class="sxs-lookup"><span data-stu-id="de6a7-278">Reorganization of docs to make things easier to find and to add cross-references</span></span>
* <span data-ttu-id="de6a7-279">La incorporación de más detalles y aclaraciones a la documentación existente</span><span class="sxs-lookup"><span data-stu-id="de6a7-279">Adding more details and clarifications to existing docs</span></span>
* <span data-ttu-id="de6a7-280">La actualización de los ejemplos y la incorporación de otros nuevos</span><span class="sxs-lookup"><span data-stu-id="de6a7-280">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="de6a7-281">Corrección de errores</span><span class="sxs-lookup"><span data-stu-id="de6a7-281">Fixing bugs</span></span>

<span data-ttu-id="de6a7-282">Seguimiento por [problemas etiquetados con `type-bug` en el hito 5.0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="de6a7-282">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="de6a7-283">Desarrolladores: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="de6a7-283">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="de6a7-284">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="de6a7-284">T-shirt size: L</span></span>

<span data-ttu-id="de6a7-285">Estado: En curso</span><span class="sxs-lookup"><span data-stu-id="de6a7-285">Status: In-progress</span></span>

<span data-ttu-id="de6a7-286">En el momento de escribir este documento, se han evaluado 135 errores para corregirlos en la versión 5.0 (ya se han corregido 62), pero hay una superposición significativa con la sección anterior _Mejoras generales de consultas_.</span><span class="sxs-lookup"><span data-stu-id="de6a7-286">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="de6a7-287">La velocidad de entrada (problemas que acaban como trabajo en un hito) fue de aproximadamente 23 problemas al mes en el transcurso de la versión 3.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-287">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="de6a7-288">No todos se tendrán que corregir en la versión 5.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-288">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="de6a7-289">Como estimación aproximada, tenemos previsto corregir unos 150 problemas adicionales para la versión 5.0.</span><span class="sxs-lookup"><span data-stu-id="de6a7-289">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="de6a7-290">Mejoras menores</span><span class="sxs-lookup"><span data-stu-id="de6a7-290">Small enhancements</span></span>

<span data-ttu-id="de6a7-291">Seguimiento por [problemas etiquetados con `type-enhancement` en el hito 5.0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span><span class="sxs-lookup"><span data-stu-id="de6a7-291">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="de6a7-292">Desarrolladores: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="de6a7-292">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="de6a7-293">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="de6a7-293">T-shirt size: L</span></span>

<span data-ttu-id="de6a7-294">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="de6a7-294">Status: Done</span></span>

<span data-ttu-id="de6a7-295">Además de las características más importantes descritas antes, también hay muchas mejoras más pequeñas programadas para la versión 5.0 a fin de corregir los "elementos excluidos".</span><span class="sxs-lookup"><span data-stu-id="de6a7-295">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="de6a7-296">Tenga en cuenta que muchas de estas mejoras también se incluyen en los temas más generales descritos antes.</span><span class="sxs-lookup"><span data-stu-id="de6a7-296">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="de6a7-297">Nivel inferior</span><span class="sxs-lookup"><span data-stu-id="de6a7-297">Below-the-line</span></span>

<span data-ttu-id="de6a7-298">Seguimiento por [problemas etiquetados con `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span><span class="sxs-lookup"><span data-stu-id="de6a7-298">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="de6a7-299">Se trata de correcciones de errores y mejoras **no** programadas actualmente para la versión 5.0, pero que se considerarán objetivos de extensión en función del progreso realizado en el trabajo anterior.</span><span class="sxs-lookup"><span data-stu-id="de6a7-299">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="de6a7-300">Además, durante la planeación siempre se tienen en cuenta los [problemas más votados](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="de6a7-300">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="de6a7-301">Excluir cualquiera de estos problemas de una versión siempre es complicado, pero necesitamos un plan realista para los recursos que tenemos.</span><span class="sxs-lookup"><span data-stu-id="de6a7-301">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="suggestions"></a><span data-ttu-id="de6a7-302">Sugerencias</span><span class="sxs-lookup"><span data-stu-id="de6a7-302">Suggestions</span></span>

<span data-ttu-id="de6a7-303">Sus comentarios sobre la planeación son importantes.</span><span class="sxs-lookup"><span data-stu-id="de6a7-303">Your feedback on planning is important.</span></span> <span data-ttu-id="de6a7-304">La mejor manera de indicar la importancia de un problema es votar (pulgar) por ese problema en GitHub.</span><span class="sxs-lookup"><span data-stu-id="de6a7-304">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="de6a7-305">Estos datos se introducirán después en el [proceso de planeación](xref:core/what-is-new/release-planning) de la próxima versión.</span><span class="sxs-lookup"><span data-stu-id="de6a7-305">This data will then feed into the [planning process](xref:core/what-is-new/release-planning) for the next release.</span></span>
