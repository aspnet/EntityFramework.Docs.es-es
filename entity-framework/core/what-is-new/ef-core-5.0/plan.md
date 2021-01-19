---
title: Plan para Entity Framework Core 5.0
description: Características planeadas para Entity Framework Core 5.0
author: ajcvickers
ms.date: 08/22/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: ba56a5f3c79dacc25b3402be114c57067da49433
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129049"
---
# <a name="plan-for-entity-framework-core-50"></a><span data-ttu-id="68364-103">Plan para Entity Framework Core 5.0</span><span class="sxs-lookup"><span data-stu-id="68364-103">Plan for Entity Framework Core 5.0</span></span>

> [!IMPORTANT]
> <span data-ttu-id="68364-104">[Se ha publicado](xref:core/what-is-new/index) EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="68364-104">EF Core 5.0 [has now been released](xref:core/what-is-new/index).</span></span> <span data-ttu-id="68364-105">Esta página se conserva como un registro histórico del plan.</span><span class="sxs-lookup"><span data-stu-id="68364-105">This page remains as a historical record of the plan.</span></span>

<span data-ttu-id="68364-106">Como se describe en el [proceso de planeamiento](xref:core/what-is-new/release-planning), se ha recopilado la información de las partes interesadas en un plan provisional para la versión EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="68364-106">As described in the [planning process](xref:core/what-is-new/release-planning), we have gathered input from stakeholders into a tentative plan for the EF Core 5.0 release.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="68364-107">Este plan sigue siendo un trabajo en curso.</span><span class="sxs-lookup"><span data-stu-id="68364-107">This plan is still a work-in-progress.</span></span> <span data-ttu-id="68364-108">Nada de esto es un compromiso.</span><span class="sxs-lookup"><span data-stu-id="68364-108">Nothing here is a commitment.</span></span> <span data-ttu-id="68364-109">Este plan es un punto de partida que evolucionará a medida que se obtenga más información.</span><span class="sxs-lookup"><span data-stu-id="68364-109">This plan is a starting point that will evolve as we learn more.</span></span> <span data-ttu-id="68364-110">Es posible que algunos aspectos no planeados en la actualidad se incorporen a la versión 5.0.</span><span class="sxs-lookup"><span data-stu-id="68364-110">Some things not currently planned for 5.0 may get pulled in.</span></span> <span data-ttu-id="68364-111">Es posible que algunos aspectos planeados en la actualidad se eliminen de la versión 5.0.</span><span class="sxs-lookup"><span data-stu-id="68364-111">Some things currently planned for 5.0 may get punted out.</span></span>

## <a name="general-information"></a><span data-ttu-id="68364-112">Información general</span><span class="sxs-lookup"><span data-stu-id="68364-112">General information</span></span>

### <a name="version-number-and-release-date"></a><span data-ttu-id="68364-113">Número de versión y fecha de lanzamiento</span><span class="sxs-lookup"><span data-stu-id="68364-113">Version number and release date</span></span>

<span data-ttu-id="68364-114">En la actualidad, el lanzamiento de EF Core 5.0 está programado al [mismo tiempo que .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="68364-114">EF Core 5.0 is currently scheduled for release at [the same time as .NET 5.0](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span> <span data-ttu-id="68364-115">Se ha elegido la versión "5.0" para la alineación con .NET 5.0.</span><span class="sxs-lookup"><span data-stu-id="68364-115">The version "5.0" was chosen to align with .NET 5.0.</span></span>

### <a name="supported-platforms"></a><span data-ttu-id="68364-116">Plataformas compatibles</span><span class="sxs-lookup"><span data-stu-id="68364-116">Supported platforms</span></span>

<span data-ttu-id="68364-117">Está previsto que EF Core 5.0 se ejecute en cualquier plataforma de .NET Standard 2.1, incluido .NET 5.0.</span><span class="sxs-lookup"><span data-stu-id="68364-117">EF Core 5.0 is planned to run on any .NET Standard 2.1 platform, including .NET 5.0.</span></span> <span data-ttu-id="68364-118">Esto forma parte de la [convergencia más general a nivel de .NET de las plataformas a .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span><span class="sxs-lookup"><span data-stu-id="68364-118">This is part of the more general .NET wide [convergence of platforms to .NET Core](https://devblogs.microsoft.com/dotnet/introducing-net-5/).</span></span>

<span data-ttu-id="68364-119">EF Core 5.0 no se ejecutará en .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="68364-119">EF Core 5.0 will not run on .NET Framework.</span></span>

### <a name="breaking-changes"></a><span data-ttu-id="68364-120">Cambios importantes</span><span class="sxs-lookup"><span data-stu-id="68364-120">Breaking changes</span></span>

<span data-ttu-id="68364-121">EF Core 5.0 contendrá algunos [cambios importantes](xref:core/what-is-new/ef-core-5.0/breaking-changes), pero serán mucho menos drásticos que en el caso de EF Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="68364-121">EF Core 5.0 will contain some [breaking changes](xref:core/what-is-new/ef-core-5.0/breaking-changes), but these will be much less severe than was the case for EF Core 3.0.</span></span> <span data-ttu-id="68364-122">El objetivo es permitir que la gran mayoría de las aplicaciones se actualicen sin interrupciones.</span><span class="sxs-lookup"><span data-stu-id="68364-122">Our goal is to allow the vast majority of applications to update without breaking.</span></span>

<span data-ttu-id="68364-123">Se espera que haya algunos cambios importantes para los proveedores de bases de datos, especialmente relacionados con la compatibilidad con TPT.</span><span class="sxs-lookup"><span data-stu-id="68364-123">It is expected that there will be some breaking changes for database providers, especially around TPT support.</span></span> <span data-ttu-id="68364-124">Pero se espera que el trabajo para actualizar un proveedor para 5.0 sea menor que el necesario para 3.0.</span><span class="sxs-lookup"><span data-stu-id="68364-124">However, we expect the work to update a provider for 5.0 will be less than was required to update for 3.0.</span></span>

## <a name="themes"></a><span data-ttu-id="68364-125">Temas</span><span class="sxs-lookup"><span data-stu-id="68364-125">Themes</span></span>

<span data-ttu-id="68364-126">Hemos extraído algunas áreas o temas importantes que formarán la base de las grandes inversiones en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="68364-126">We have extracted a few major areas or themes which will form the basis for the large investments in EF Core 5.0.</span></span>

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a><span data-ttu-id="68364-127">Asignación de varios a varios totalmente transparente por convención</span><span class="sxs-lookup"><span data-stu-id="68364-127">Fully transparent many-to-many mapping by convention</span></span>

<span data-ttu-id="68364-128">Jefes de desarrollo: @smitpatel, @AndriySvyryd y @lajones</span><span class="sxs-lookup"><span data-stu-id="68364-128">Lead developers: @smitpatel, @AndriySvyryd, and @lajones</span></span>

<span data-ttu-id="68364-129">Número de seguimiento: [10508](https://github.com/dotnet/efcore/issues/10508)</span><span class="sxs-lookup"><span data-stu-id="68364-129">Tracked by [#10508](https://github.com/dotnet/efcore/issues/10508)</span></span>

<span data-ttu-id="68364-130">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="68364-130">T-shirt size: L</span></span>

<span data-ttu-id="68364-131">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="68364-131">Status: Done</span></span>

<span data-ttu-id="68364-132">Varios a varios es la [característica más solicitada](https://github.com/dotnet/efcore/issues/1368) (506 votos aproximadamente) en el trabajo pendiente de GitHub.</span><span class="sxs-lookup"><span data-stu-id="68364-132">Many-to-many is the [most requested feature](https://github.com/dotnet/efcore/issues/1368) (~506 votes) on the GitHub backlog.</span></span>

<span data-ttu-id="68364-133">La compatibilidad con las relaciones varios a varios se puede dividir en tres áreas principales:</span><span class="sxs-lookup"><span data-stu-id="68364-133">Support for many-to-many relationships can be broken down into three major areas:</span></span>

* <span data-ttu-id="68364-134">Propiedades de navegación por omisión, que se describen en el siguiente tema.</span><span class="sxs-lookup"><span data-stu-id="68364-134">Skip navigation properties--covered by the next theme.</span></span>
* <span data-ttu-id="68364-135">Tipos de entidad de contenedor de propiedades.</span><span class="sxs-lookup"><span data-stu-id="68364-135">Property-bag entity types.</span></span> <span data-ttu-id="68364-136">Permiten usar un tipo CLR estándar (por ejemplo, `Dictionary`) para las instancias de entidad, de modo que no se necesite un tipo CLR explícito para cada tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="68364-136">These allow a standard CLR type (e.g. `Dictionary`) to be used for entity instances such that an explicit CLR type is not needed for each entity type.</span></span> <span data-ttu-id="68364-137">Número de seguimiento: [9914](https://github.com/dotnet/efcore/issues/9914).</span><span class="sxs-lookup"><span data-stu-id="68364-137">Tracked by [#9914](https://github.com/dotnet/efcore/issues/9914).</span></span>
* <span data-ttu-id="68364-138">Facilitar la configuración de relaciones varios a varios.</span><span class="sxs-lookup"><span data-stu-id="68364-138">Sugar for easy configuration of many-to-many relationships.</span></span>

<span data-ttu-id="68364-139">Además de la compatibilidad con la navegación por omisión, ahora vamos a incorporar estas otras áreas de varios a varios a EF Core 5.0 para proporcionar una experiencia completa.</span><span class="sxs-lookup"><span data-stu-id="68364-139">In addition to the skip navigation support, we are now pulling these other areas of many-to-many into EF Core 5.0 so as to provide a complete experience.</span></span>

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a><span data-ttu-id="68364-140">Propiedades de navegación de varios a varios (también denominado "omitir navegaciones")</span><span class="sxs-lookup"><span data-stu-id="68364-140">Many-to-many navigation properties (a.k.a "skip navigations")</span></span>

<span data-ttu-id="68364-141">Jefes de desarrollo: @smitpatel y @AndriySvyryd</span><span class="sxs-lookup"><span data-stu-id="68364-141">Lead developers: @smitpatel and @AndriySvyryd</span></span>

<span data-ttu-id="68364-142">Seguimiento realizado por [#19003](https://github.com/dotnet/efcore/issues/19003)</span><span class="sxs-lookup"><span data-stu-id="68364-142">Tracked by [#19003](https://github.com/dotnet/efcore/issues/19003)</span></span>

<span data-ttu-id="68364-143">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="68364-143">T-shirt size: L</span></span>

<span data-ttu-id="68364-144">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="68364-144">Status: Done</span></span>

<span data-ttu-id="68364-145">Tal y como se describe en el primer tema, la compatibilidad de varios a varios tiene varios aspectos que cabe destacar.</span><span class="sxs-lookup"><span data-stu-id="68364-145">As described in the first theme, many-to-many support has multiple aspects.</span></span>
<span data-ttu-id="68364-146">En este tema se realiza un seguimiento específico del uso de las navegaciones por omisión.</span><span class="sxs-lookup"><span data-stu-id="68364-146">This theme specifically tracks use of skip navigations.</span></span>
<span data-ttu-id="68364-147">Creemos que el principal obstáculo para los que quieren que se admitan las relaciones varios a varios es la imposibilidad de usar relaciones "naturales", sin hacer referencia a la tabla de combinación, en la lógica de negocios, como las consultas.</span><span class="sxs-lookup"><span data-stu-id="68364-147">We believe that the most significant blocker for those wanting many-to-many support is not being able to use the "natural" relationships, without referring to the join table, in business logic such as queries.</span></span>
<span data-ttu-id="68364-148">Es posible que el tipo de entidad de tabla de combinación siga existiendo, pero no debería interferir con la lógica de negocios.</span><span class="sxs-lookup"><span data-stu-id="68364-148">The join table entity type may still exist, but it should not get in the way of business logic.</span></span>

## <a name="table-per-type-tpt-inheritance-mapping"></a><span data-ttu-id="68364-149">Asignación de herencia de tabla por tipo (TPT)</span><span class="sxs-lookup"><span data-stu-id="68364-149">Table-per-type (TPT) inheritance mapping</span></span>

<span data-ttu-id="68364-150">Jefe de desarrollo: @AndriySvyryd y @smitpatel</span><span class="sxs-lookup"><span data-stu-id="68364-150">Lead developer: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="68364-151">Seguimiento realizado por [#2266](https://github.com/dotnet/efcore/issues/2266)</span><span class="sxs-lookup"><span data-stu-id="68364-151">Tracked by [#2266](https://github.com/dotnet/efcore/issues/2266)</span></span>

<span data-ttu-id="68364-152">Talla de camiseta: XL</span><span class="sxs-lookup"><span data-stu-id="68364-152">T-shirt size: XL</span></span>

<span data-ttu-id="68364-153">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="68364-153">Status: Done</span></span>

<span data-ttu-id="68364-154">TPT se va a incluir porque se trata de una característica muy solicitada (aproximadamente 289 votos; en tercera posición) y porque requiere algunos cambios de bajo nivel que consideramos adecuados para la naturaleza fundamental del plan general de .NET 5.</span><span class="sxs-lookup"><span data-stu-id="68364-154">We're doing TPT because it is both a highly requested feature (~289 votes; 3rd overall) and because it requires some low-level changes that we feel are appropriate for the foundational nature of the overall .NET 5 plan.</span></span> <span data-ttu-id="68364-155">Esperamos que esto genere cambios importantes para los proveedores de bases de datos, aunque deberían ser mucho menos graves que los necesarios para la versión 3.0.</span><span class="sxs-lookup"><span data-stu-id="68364-155">We expect this to result in breaking changes for database providers, although these should be much less severe than the changes required for 3.0.</span></span>

## <a name="filtered-include"></a><span data-ttu-id="68364-156">Inclusión filtrada</span><span class="sxs-lookup"><span data-stu-id="68364-156">Filtered Include</span></span>

<span data-ttu-id="68364-157">Jefe de desarrollo: @maumar</span><span class="sxs-lookup"><span data-stu-id="68364-157">Lead developer: @maumar</span></span>

<span data-ttu-id="68364-158">Seguimiento realizado por [#1833](https://github.com/dotnet/efcore/issues/1833)</span><span class="sxs-lookup"><span data-stu-id="68364-158">Tracked by [#1833](https://github.com/dotnet/efcore/issues/1833)</span></span>

<span data-ttu-id="68364-159">Talla de camiseta: M</span><span class="sxs-lookup"><span data-stu-id="68364-159">T-shirt size: M</span></span>

<span data-ttu-id="68364-160">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="68364-160">Status: Done</span></span>

<span data-ttu-id="68364-161">Inclusión filtrada es una característica muy solicitada (aproximadamente 376 votos; en segunda posición) que no requiere demasiado trabajo y que creemos que desbloqueará o facilitará escenarios que actualmente requieren filtros de nivel de modelo o consultas más complejas.</span><span class="sxs-lookup"><span data-stu-id="68364-161">Filtered Include is a highly-requested feature (~376 votes; 2nd overall) that isn't a huge amount of work, and that we believe will unblock or make easier many scenarios that currently require model-level filters or more complex queries.</span></span>

## <a name="split-include"></a><span data-ttu-id="68364-162">División de Include</span><span class="sxs-lookup"><span data-stu-id="68364-162">Split Include</span></span>

<span data-ttu-id="68364-163">Jefe de desarrollo: @smitpatel</span><span class="sxs-lookup"><span data-stu-id="68364-163">Lead developer: @smitpatel</span></span>

<span data-ttu-id="68364-164">Número de seguimiento: [20892](https://github.com/dotnet/efcore/issues/20892)</span><span class="sxs-lookup"><span data-stu-id="68364-164">Tracked by [#20892](https://github.com/dotnet/efcore/issues/20892)</span></span>

<span data-ttu-id="68364-165">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="68364-165">T-shirt size: L</span></span>

<span data-ttu-id="68364-166">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="68364-166">Status: Done</span></span>

<span data-ttu-id="68364-167">EF Core 3.0 cambió el comportamiento predeterminado para crear una consulta SQL única para una consulta LINQ determinada.</span><span class="sxs-lookup"><span data-stu-id="68364-167">EF Core 3.0 changed the default behavior to create a single SQL query for a given LINQ query.</span></span>
<span data-ttu-id="68364-168">Esto provocó una gran cantidad de degradaciones de rendimiento para las consultas que usan Include para varias colecciones.</span><span class="sxs-lookup"><span data-stu-id="68364-168">This caused large performance regressions for queries that use Include for multiple collections.</span></span>

<span data-ttu-id="68364-169">En EF Core 5.0, se mantiene el nuevo comportamiento predeterminado.</span><span class="sxs-lookup"><span data-stu-id="68364-169">In EF Core 5.0, we are retaining the new default behavior.</span></span>
<span data-ttu-id="68364-170">Sin embargo, EF Core 5.0 ahora permite la generación de varias consultas para los métodos Include de la colección en los que tener una sola consulta provoca deficiencias en el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="68364-170">However, EF Core 5.0 will now allow generation of multiple queries for collection Includes where having a single query is causing bad performance.</span></span>

## <a name="required-one-to-one-dependents"></a><span data-ttu-id="68364-171">Dependencias de uno a uno obligatorias</span><span class="sxs-lookup"><span data-stu-id="68364-171">Required one-to-one dependents</span></span>

<span data-ttu-id="68364-172">Jefes de desarrollo: @AndriySvyryd y @smitpatel</span><span class="sxs-lookup"><span data-stu-id="68364-172">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="68364-173">Número de seguimiento: [12100](https://github.com/dotnet/efcore/issues/12100)</span><span class="sxs-lookup"><span data-stu-id="68364-173">Tracked by [#12100](https://github.com/dotnet/efcore/issues/12100)</span></span>

<span data-ttu-id="68364-174">Talla de camiseta: M</span><span class="sxs-lookup"><span data-stu-id="68364-174">T-shirt size: M</span></span>

<span data-ttu-id="68364-175">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="68364-175">Status: Done</span></span>

<span data-ttu-id="68364-176">En EF Core 3.0, todas las dependencias, incluidos los tipos de propiedad, son opcionales (por ejemplo, Person.Address puede ser NULL).</span><span class="sxs-lookup"><span data-stu-id="68364-176">In EF Core 3.0, all dependents, including owned types are optional (e.g. Person.Address can be null).</span></span> <span data-ttu-id="68364-177">En EF Core 5.0, se pueden configurar las dependencias según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="68364-177">In EF Core 5.0, dependents can be configured as required.</span></span>

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a><span data-ttu-id="68364-178">Racionalización de ToTable, ToQuery, ToView, FromSql, etc.</span><span class="sxs-lookup"><span data-stu-id="68364-178">Rationalize ToTable, ToQuery, ToView, FromSql, etc</span></span>

<span data-ttu-id="68364-179">Jefes de desarrollo: @AndriySvyryd y @smitpatel</span><span class="sxs-lookup"><span data-stu-id="68364-179">Lead developers: @AndriySvyryd and @smitpatel</span></span>

<span data-ttu-id="68364-180">Seguimiento realizado por [#17270](https://github.com/dotnet/efcore/issues/17270)</span><span class="sxs-lookup"><span data-stu-id="68364-180">Tracked by [#17270](https://github.com/dotnet/efcore/issues/17270)</span></span>

<span data-ttu-id="68364-181">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="68364-181">T-shirt size: L</span></span>

<span data-ttu-id="68364-182">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="68364-182">Status: Done</span></span>

<span data-ttu-id="68364-183">Se han realizado avances en versiones anteriores hacia la compatibilidad con SQL sin procesar, tipos sin clave y áreas relacionadas.</span><span class="sxs-lookup"><span data-stu-id="68364-183">We have made progress in previous releases towards supporting raw SQL, keyless types, and related areas.</span></span> <span data-ttu-id="68364-184">Pero hay brechas e incoherencias en el funcionamiento en conjunto de todos los elementos.</span><span class="sxs-lookup"><span data-stu-id="68364-184">However, there are both gaps and inconsistencies in the way everything works together as a whole.</span></span> <span data-ttu-id="68364-185">El objetivo para la versión 5.0 es corregirlos y crear una buena experiencia para definir, migrar y usar otros tipos de entidades y sus consultas y artefactos de base de datos asociados.</span><span class="sxs-lookup"><span data-stu-id="68364-185">The goal for 5.0 is to fix these and create a good experience for defining, migrating, and using different types of entities and their associated queries and database artifacts.</span></span> <span data-ttu-id="68364-186">Esto también puede implicar actualizaciones de la API de consulta compilada.</span><span class="sxs-lookup"><span data-stu-id="68364-186">This may also involve updates to the compiled query API.</span></span>

<span data-ttu-id="68364-187">Tenga en cuenta que este elemento puede dar lugar a algunos cambios importantes en el nivel de la aplicación, ya que algunas de las funcionalidades actuales son demasiado permisivas, lo que puede conducir rápidamente a que los usuarios cometan errores.</span><span class="sxs-lookup"><span data-stu-id="68364-187">Note that this item may result in some application-level breaking changes since some of the functionality we currently have is too permissive such that it can quickly lead people into pits of failure.</span></span> <span data-ttu-id="68364-188">Lo más probable es que parte de esta funcionalidad se bloquee y se proporcionen instrucciones sobre lo que se debe hacer en su lugar.</span><span class="sxs-lookup"><span data-stu-id="68364-188">We will likely end up blocking some of this functionality together with guidance on what to do instead.</span></span>

## <a name="general-query-enhancements"></a><span data-ttu-id="68364-189">Mejoras generales de consultas</span><span class="sxs-lookup"><span data-stu-id="68364-189">General query enhancements</span></span>

<span data-ttu-id="68364-190">Jefes de desarrollo: @smitpatel y @maumar</span><span class="sxs-lookup"><span data-stu-id="68364-190">Lead developers: @smitpatel and @maumar</span></span>

<span data-ttu-id="68364-191">Seguimiento por [problemas etiquetados con `area-query` en el hito 5.0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="68364-191">Tracked by [issues labeled with `area-query` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="68364-192">Talla de camiseta: XL</span><span class="sxs-lookup"><span data-stu-id="68364-192">T-shirt size: XL</span></span>

<span data-ttu-id="68364-193">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="68364-193">Status: Done</span></span>

<span data-ttu-id="68364-194">El código de traducción de consultas se ha reescrito de forma exhaustiva para EF Core 3.0.</span><span class="sxs-lookup"><span data-stu-id="68364-194">The query translation code was extensively rewritten for EF Core 3.0.</span></span> <span data-ttu-id="68364-195">Por este motivo, el código de consulta tiene un estado mucho más robusto.</span><span class="sxs-lookup"><span data-stu-id="68364-195">The query code is generally in a much more robust state because of this.</span></span> <span data-ttu-id="68364-196">Para la versión 5.0, no se planean cambios importantes en las consultas más allá de los necesarios para admitir TPT y la omisión de propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="68364-196">For 5.0 we aren't planning on making major query changes, outside those needed to support TPT and skip navigation properties.</span></span> <span data-ttu-id="68364-197">Pero se necesita un trabajo importante para corregir las deudas técnicas generadas tras la revisión de la versión 3.0.</span><span class="sxs-lookup"><span data-stu-id="68364-197">However, there is still significant work needed to fix some technical debt left over from the 3.0 overhaul.</span></span> <span data-ttu-id="68364-198">También tenemos previsto corregir muchos errores e implementar pequeñas mejoras para mejorar aún más la experiencia general de las consultas.</span><span class="sxs-lookup"><span data-stu-id="68364-198">We also plan to fix many bugs and implement small enhancements to further improve the overall query experience.</span></span>

## <a name="migrations-and-deployment-experience"></a><span data-ttu-id="68364-199">Migraciones y experiencia de implementación</span><span class="sxs-lookup"><span data-stu-id="68364-199">Migrations and deployment experience</span></span>

<span data-ttu-id="68364-200">Jefes de desarrollo: @bricelam</span><span class="sxs-lookup"><span data-stu-id="68364-200">Lead developers: @bricelam</span></span>

<span data-ttu-id="68364-201">Seguimiento realizado por [#19587](https://github.com/dotnet/efcore/issues/19587)</span><span class="sxs-lookup"><span data-stu-id="68364-201">Tracked by [#19587](https://github.com/dotnet/efcore/issues/19587)</span></span>

<span data-ttu-id="68364-202">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="68364-202">T-shirt size: L</span></span>

<span data-ttu-id="68364-203">Estado: Ámbito y trabajo hecho</span><span class="sxs-lookup"><span data-stu-id="68364-203">Status: Scoped/Done</span></span>

<span data-ttu-id="68364-204">Ámbito: la [característica de conjunto de migraciones](https://github.com/dotnet/efcore/issues/19693) se ha aplazado hasta después de la versión EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="68364-204">Scoping: The [migrations bundles feature](https://github.com/dotnet/efcore/issues/19693) has been deferred until after the EF Core 5.0 release.</span></span> <span data-ttu-id="68364-205">Sin embargo, en EF Core 5.0 se incluirán [otras mejoras de destino relacionadas con las migraciones](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460).</span><span class="sxs-lookup"><span data-stu-id="68364-205">However, several other [targeted improvements related to migrations](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460) will be included in EF Core 5.0</span></span>

<span data-ttu-id="68364-206">En la actualidad, muchos desarrolladores migran sus bases de datos en el momento de inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="68364-206">Currently, many developers migrate their databases at application startup time.</span></span> <span data-ttu-id="68364-207">Esto es fácil, pero no se recomienda porque:</span><span class="sxs-lookup"><span data-stu-id="68364-207">This is easy but is not recommended because:</span></span>

* <span data-ttu-id="68364-208">Es posible que varios subprocesos, procesos o servidores intenten migrar la base de datos simultáneamente</span><span class="sxs-lookup"><span data-stu-id="68364-208">Multiple threads/processes/servers may attempt to migrate the database concurrently</span></span>
* <span data-ttu-id="68364-209">Es posible que las aplicaciones intenten acceder a un estado incoherente mientras se produce esta operación</span><span class="sxs-lookup"><span data-stu-id="68364-209">Applications may try to access inconsistent state while this is happening</span></span>
* <span data-ttu-id="68364-210">Normalmente, no se deben conceder los permisos de base de datos para modificar el esquema para la ejecución de la aplicación</span><span class="sxs-lookup"><span data-stu-id="68364-210">Usually the database permissions to modify the schema should not be granted for application execution</span></span>
* <span data-ttu-id="68364-211">Es difícil revertir a un estado limpio si se produce algún error</span><span class="sxs-lookup"><span data-stu-id="68364-211">It's hard to revert back to a clean state if something goes wrong</span></span>

<span data-ttu-id="68364-212">Queremos ofrecer una mejor experiencia que permita migrar la base de datos de forma sencilla en el momento de la implementación.</span><span class="sxs-lookup"><span data-stu-id="68364-212">We want to deliver a better experience here that allows an easy way to migrate the database at deployment time.</span></span> <span data-ttu-id="68364-213">Esto debería:</span><span class="sxs-lookup"><span data-stu-id="68364-213">This should:</span></span>

* <span data-ttu-id="68364-214">Funcionar en Linux, Mac y Windows</span><span class="sxs-lookup"><span data-stu-id="68364-214">Work on Linux, Mac, and Windows</span></span>
* <span data-ttu-id="68364-215">Ser una experiencia positiva en la línea de comandos</span><span class="sxs-lookup"><span data-stu-id="68364-215">Be a good experience on the command line</span></span>
* <span data-ttu-id="68364-216">Admitir escenarios con contenedores</span><span class="sxs-lookup"><span data-stu-id="68364-216">Support scenarios with containers</span></span>
* <span data-ttu-id="68364-217">Funcionar con flujos y herramientas de implementación del mundo real que se usan habitualmente</span><span class="sxs-lookup"><span data-stu-id="68364-217">Work with commonly used real-world deployment tools/flows</span></span>
* <span data-ttu-id="68364-218">Integrarse al menos en Visual Studio</span><span class="sxs-lookup"><span data-stu-id="68364-218">Integrate into at least Visual Studio</span></span>

<span data-ttu-id="68364-219">Lo más probable es que el resultado sea un gran número de pequeñas mejoras en EF Core (por ejemplo, mejores migraciones en SQLite), junto con instrucciones y colaboraciones a largo plazo con otros equipos para mejorar las experiencias de un extremo a otro que van más allá de EF exclusivamente.</span><span class="sxs-lookup"><span data-stu-id="68364-219">The result is likely to be many small improvements in EF Core (for example, better Migrations on SQLite), together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

## <a name="ef-core-platforms-experience"></a><span data-ttu-id="68364-220">Experiencia de las plataformas de EF Core</span><span class="sxs-lookup"><span data-stu-id="68364-220">EF Core platforms experience</span></span>

<span data-ttu-id="68364-221">Jefes de desarrollo: @roji y @bricelam</span><span class="sxs-lookup"><span data-stu-id="68364-221">Lead developers: @roji and @bricelam</span></span>

<span data-ttu-id="68364-222">Seguimiento realizado por [#19588](https://github.com/dotnet/efcore/issues/19588)</span><span class="sxs-lookup"><span data-stu-id="68364-222">Tracked by [#19588](https://github.com/dotnet/efcore/issues/19588)</span></span>

<span data-ttu-id="68364-223">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="68364-223">T-shirt size: L</span></span>

<span data-ttu-id="68364-224">Estado: Ámbito o hecho</span><span class="sxs-lookup"><span data-stu-id="68364-224">Status: Scope/Done</span></span>

<span data-ttu-id="68364-225">Ámbito: la guía y los ejemplos de la plataforma se publican para Blazor, Xamarin, WinForms y WPF.</span><span class="sxs-lookup"><span data-stu-id="68364-225">Scoping: Platform guidance and samples are published for Blazor, Xamarin, WinForms, and WPF.</span></span> <span data-ttu-id="68364-226">Xamarin y otros trabajos de AOT o enlazador ahora están previstos para EF Core 6.0.</span><span class="sxs-lookup"><span data-stu-id="68364-226">Xamarin and other AOT/linker work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="68364-227">Disponemos de instrucciones de calidad para usar EF Core en aplicaciones web tradicionales similares a MVC.</span><span class="sxs-lookup"><span data-stu-id="68364-227">We have good guidance for using EF Core in traditional MVC-like web applications.</span></span> <span data-ttu-id="68364-228">Las instrucciones para otras plataformas y modelos de aplicación no existen o no están actualizadas.</span><span class="sxs-lookup"><span data-stu-id="68364-228">Guidance for other platforms and application models is either missing or out-of-date.</span></span> <span data-ttu-id="68364-229">Para EF Core 5.0, el objetivo es investigar, mejorar y documentar la experiencia de uso de EF Core con:</span><span class="sxs-lookup"><span data-stu-id="68364-229">For EF Core 5.0, we plan to investigate, improve, and document the experience of using EF Core with:</span></span>

* <span data-ttu-id="68364-230">Blazor</span><span class="sxs-lookup"><span data-stu-id="68364-230">Blazor</span></span>
* <span data-ttu-id="68364-231">Xamarin, incluido el uso del artículo de AOT o vinculador</span><span class="sxs-lookup"><span data-stu-id="68364-231">Xamarin, including using the AOT/linker story</span></span>
* <span data-ttu-id="68364-232">WinForms, WPF, WinUI y posiblemente otras interfaces de usuario</span><span class="sxs-lookup"><span data-stu-id="68364-232">WinForms/WPF/WinUI and possibly other U.I.</span></span> <span data-ttu-id="68364-233">frameworks</span><span class="sxs-lookup"><span data-stu-id="68364-233">frameworks</span></span>

<span data-ttu-id="68364-234">Lo más probable es que el resultado sea un gran número de pequeñas mejoras en EF Core, junto con instrucciones y colaboraciones a largo plazo con otros equipos para mejorar las experiencias de un extremo a otro que van más allá de EF exclusivamente.</span><span class="sxs-lookup"><span data-stu-id="68364-234">This is likely to be many small improvements in EF Core, together with guidance and longer-term collaborations with other teams to improve end-to-end experiences that go beyond just EF.</span></span>

<span data-ttu-id="68364-235">Las áreas concretas que tenemos previsto examinar son las siguientes:</span><span class="sxs-lookup"><span data-stu-id="68364-235">Specific areas we plan to look at are:</span></span>

* <span data-ttu-id="68364-236">Implementación, incluida la experiencia en el uso de herramientas de EF como para migraciones</span><span class="sxs-lookup"><span data-stu-id="68364-236">Deployment, including the experience for using EF tooling such as for Migrations</span></span>
* <span data-ttu-id="68364-237">Modelos de aplicación, como Xamarin y Blazor, y probablemente otros</span><span class="sxs-lookup"><span data-stu-id="68364-237">Application models, including Xamarin and Blazor, and probably others</span></span>
* <span data-ttu-id="68364-238">Experiencias de SQLite, incluida la experiencia espacial y las recompilaciones de tabla</span><span class="sxs-lookup"><span data-stu-id="68364-238">SQLite experiences, including the spatial experience and table rebuilds</span></span>
* <span data-ttu-id="68364-239">Experiencias de AOT y vinculación</span><span class="sxs-lookup"><span data-stu-id="68364-239">AOT and linking experiences</span></span>
* <span data-ttu-id="68364-240">Integración de diagnósticos, incluidos los contadores de rendimiento</span><span class="sxs-lookup"><span data-stu-id="68364-240">Diagnostics integration, including perf counters</span></span>

## <a name="performance"></a><span data-ttu-id="68364-241">Rendimiento</span><span class="sxs-lookup"><span data-stu-id="68364-241">Performance</span></span>

<span data-ttu-id="68364-242">Jefe de desarrollo: @roji</span><span class="sxs-lookup"><span data-stu-id="68364-242">Lead developer: @roji</span></span>

<span data-ttu-id="68364-243">Seguimiento por [problemas etiquetados con `area-perf` en el hito 5.0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="68364-243">Tracked by [issues labeled with `area-perf` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="68364-244">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="68364-244">T-shirt size: L</span></span>

<span data-ttu-id="68364-245">Estado: Ámbito y trabajo hecho</span><span class="sxs-lookup"><span data-stu-id="68364-245">Status: Scoped/Done</span></span>

<span data-ttu-id="68364-246">Ámbito: se han completado las principales mejoras de rendimiento en el proveedor Npgsql.</span><span class="sxs-lookup"><span data-stu-id="68364-246">Scoping: Major performance improvements in the Npgsql provider are complete.</span></span> <span data-ttu-id="68364-247">Ahora se ha planificado otro trabajo de rendimiento para EF Core 6.0.</span><span class="sxs-lookup"><span data-stu-id="68364-247">Other performance work is now planned for EF Core 6.0.</span></span>

<span data-ttu-id="68364-248">Para EF Core, el plan es mejorar nuestro conjunto de pruebas comparativas de rendimiento y realizar mejoras de rendimiento dirigidas al tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="68364-248">For EF Core, we plan to improve our suite of performance benchmarks and make directed performance improvements to the runtime.</span></span> <span data-ttu-id="68364-249">Además, tenemos previsto completar la nueva API de procesamiento por lotes de ADO.NET, que se ha creado como prototipo durante el ciclo de versiones de 3.0.</span><span class="sxs-lookup"><span data-stu-id="68364-249">In addition, we plan to complete the new ADO.NET batching API which was prototyped during the 3.0 release cycle.</span></span> <span data-ttu-id="68364-250">En el nivel de ADO.NET también se planean mejoras de rendimiento adicionales para el proveedor Npgsql.</span><span class="sxs-lookup"><span data-stu-id="68364-250">Also at the ADO.NET layer, we plan additional performance improvements to the Npgsql provider.</span></span>

<span data-ttu-id="68364-251">Como parte de este trabajo, también está previsto agregar contadores de rendimiento de ADO.NET y EF Core, y otros diagnósticos según corresponda.</span><span class="sxs-lookup"><span data-stu-id="68364-251">As part of this work we also plan to add ADO.NET/EF Core performance counters and other diagnostics as appropriate.</span></span>

## <a name="architecturalcontributor-documentation"></a><span data-ttu-id="68364-252">Documentación de arquitectura y colaboradores</span><span class="sxs-lookup"><span data-stu-id="68364-252">Architectural/contributor documentation</span></span>

<span data-ttu-id="68364-253">Jefe de documentación: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="68364-253">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="68364-254">Seguimiento realizado por [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span><span class="sxs-lookup"><span data-stu-id="68364-254">Tracked by [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)</span></span>

<span data-ttu-id="68364-255">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="68364-255">T-shirt size: L</span></span>

<span data-ttu-id="68364-256">Estado: Cut</span><span class="sxs-lookup"><span data-stu-id="68364-256">Status: Cut</span></span>

<span data-ttu-id="68364-257">La idea es facilitar la comprensión de lo que sucede dentro de EF Core.</span><span class="sxs-lookup"><span data-stu-id="68364-257">The idea here is to make it easier to understand what is going on in the internals of EF Core.</span></span> <span data-ttu-id="68364-258">Esto puede ser útil para cualquiera que utilice EF Core, pero la motivación principal es facilitarlo para los usuarios externos:</span><span class="sxs-lookup"><span data-stu-id="68364-258">This can be useful to anyone using EF Core, but the primary motivation is to make it easier for external people to:</span></span>

* <span data-ttu-id="68364-259">Contribuir al código de EF Core</span><span class="sxs-lookup"><span data-stu-id="68364-259">Contribute to the EF Core code</span></span>
* <span data-ttu-id="68364-260">Crear proveedores de bases de datos</span><span class="sxs-lookup"><span data-stu-id="68364-260">Create database providers</span></span>
* <span data-ttu-id="68364-261">Compilar otras extensiones</span><span class="sxs-lookup"><span data-stu-id="68364-261">Build other extensions</span></span>

<span data-ttu-id="68364-262">Actualización: Desafortunadamente, este plan era demasiado ambicioso.</span><span class="sxs-lookup"><span data-stu-id="68364-262">Update: Unfortunately, this plan was too ambitious.</span></span>
<span data-ttu-id="68364-263">Seguimos creyendo que es un aspecto importante, pero lamentablemente no se incluirá en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="68364-263">We still believe this is important, but unfortunately it won't land with EF Core 5.0.</span></span>

## <a name="microsoftdatasqlite-documentation"></a><span data-ttu-id="68364-264">Documentación de Microsoft.Data.Sqlite</span><span class="sxs-lookup"><span data-stu-id="68364-264">Microsoft.Data.Sqlite documentation</span></span>

<span data-ttu-id="68364-265">Jefe de documentación: @bricelam</span><span class="sxs-lookup"><span data-stu-id="68364-265">Lead documenter: @bricelam</span></span>

<span data-ttu-id="68364-266">Seguimiento realizado por [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span><span class="sxs-lookup"><span data-stu-id="68364-266">Tracked by [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)</span></span>

<span data-ttu-id="68364-267">Talla de camiseta: M</span><span class="sxs-lookup"><span data-stu-id="68364-267">T-shirt size: M</span></span>

<span data-ttu-id="68364-268">Estado: Completado.</span><span class="sxs-lookup"><span data-stu-id="68364-268">Status: Completed.</span></span> <span data-ttu-id="68364-269">La nueva documentación está [activa en el sitio de documentación de Microsoft](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span><span class="sxs-lookup"><span data-stu-id="68364-269">The new documentation is [live on the Microsoft docs site](/dotnet/standard/data/sqlite/?tabs=netcore-cli).</span></span>

<span data-ttu-id="68364-270">El equipo de EF también posee el proveedor de ADO.NET Microsoft.Data.Sqlite.</span><span class="sxs-lookup"><span data-stu-id="68364-270">The EF Team also owns the Microsoft.Data.Sqlite ADO.NET provider.</span></span> <span data-ttu-id="68364-271">Tenemos previsto documentar completamente este proveedor como parte de la versión 5.0.</span><span class="sxs-lookup"><span data-stu-id="68364-271">We plan to fully document this provider as part of the 5.0 release.</span></span>

## <a name="general-documentation"></a><span data-ttu-id="68364-272">Documentación general</span><span class="sxs-lookup"><span data-stu-id="68364-272">General documentation</span></span>

<span data-ttu-id="68364-273">Jefe de documentación: @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="68364-273">Lead documenter: @ajcvickers</span></span>

<span data-ttu-id="68364-274">Seguimiento mediante [problemas en el repositorio de documentación del hito 5.0](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span><span class="sxs-lookup"><span data-stu-id="68364-274">Tracked by [issues in the docs repo in the 5.0 milestone](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)</span></span>

<span data-ttu-id="68364-275">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="68364-275">T-shirt size: L</span></span>

<span data-ttu-id="68364-276">Estado: En curso</span><span class="sxs-lookup"><span data-stu-id="68364-276">Status: In-progress</span></span>

<span data-ttu-id="68364-277">Ya se ha iniciado el proceso de actualización de la documentación de las versiones 3.0 y 3.1.</span><span class="sxs-lookup"><span data-stu-id="68364-277">We are already in the process of updating documentation for the 3.0 and 3.1 releases.</span></span> <span data-ttu-id="68364-278">También se está trabajando en:</span><span class="sxs-lookup"><span data-stu-id="68364-278">We are also working on:</span></span>

* <span data-ttu-id="68364-279">Una revisión de la documentación de introducción para que sea más fácil de seguir</span><span class="sxs-lookup"><span data-stu-id="68364-279">An overhaul of the getting started docs to make them more approachable/easier to follow</span></span>
* <span data-ttu-id="68364-280">La reorganización de la documentación para facilitar la búsqueda y la adición de referencias cruzadas</span><span class="sxs-lookup"><span data-stu-id="68364-280">Reorganization of docs to make things easier to find and to add cross-references</span></span>
* <span data-ttu-id="68364-281">La incorporación de más detalles y aclaraciones a la documentación existente</span><span class="sxs-lookup"><span data-stu-id="68364-281">Adding more details and clarifications to existing docs</span></span>
* <span data-ttu-id="68364-282">La actualización de los ejemplos y la incorporación de otros nuevos</span><span class="sxs-lookup"><span data-stu-id="68364-282">Updating the samples and adding more examples</span></span>

## <a name="fixing-bugs"></a><span data-ttu-id="68364-283">Corrección de errores</span><span class="sxs-lookup"><span data-stu-id="68364-283">Fixing bugs</span></span>

<span data-ttu-id="68364-284">Seguimiento por [problemas etiquetados con `type-bug` en el hito 5.0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span><span class="sxs-lookup"><span data-stu-id="68364-284">Tracked by [issues labeled with `type-bug` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)</span></span>

<span data-ttu-id="68364-285">Desarrolladores: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="68364-285">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="68364-286">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="68364-286">T-shirt size: L</span></span>

<span data-ttu-id="68364-287">Estado: En curso</span><span class="sxs-lookup"><span data-stu-id="68364-287">Status: In-progress</span></span>

<span data-ttu-id="68364-288">En el momento de escribir este documento, se han evaluado 135 errores para corregirlos en la versión 5.0 (ya se han corregido 62), pero hay una superposición significativa con la sección anterior _Mejoras generales de consultas_.</span><span class="sxs-lookup"><span data-stu-id="68364-288">At the time of writing, we have 135 bugs triaged to be fixed in the 5.0 release (with 62 already fixed), but there is significant overlap with the _General query enhancements_ section above.</span></span>

<span data-ttu-id="68364-289">La velocidad de entrada (problemas que acaban como trabajo en un hito) fue de aproximadamente 23 problemas al mes en el transcurso de la versión 3.0.</span><span class="sxs-lookup"><span data-stu-id="68364-289">The incoming rate (issues that end up as work in a milestone) was about 23 issues per month over the course of the 3.0 release.</span></span> <span data-ttu-id="68364-290">No todos se tendrán que corregir en la versión 5.0.</span><span class="sxs-lookup"><span data-stu-id="68364-290">Not all of these will need to be fixed in 5.0.</span></span> <span data-ttu-id="68364-291">Como estimación aproximada, tenemos previsto corregir unos 150 problemas adicionales para la versión 5.0.</span><span class="sxs-lookup"><span data-stu-id="68364-291">As a rough estimate we plan to fix an additional 150 issues in the 5.0 time frame.</span></span>

## <a name="small-enhancements"></a><span data-ttu-id="68364-292">Mejoras menores</span><span class="sxs-lookup"><span data-stu-id="68364-292">Small enhancements</span></span>

<span data-ttu-id="68364-293">Seguimiento por [problemas etiquetados con `type-enhancement` en el hito 5.0](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span><span class="sxs-lookup"><span data-stu-id="68364-293">Tracked by [issues labeled with `type-enhancement` in the 5.0 milestone](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)</span></span>

<span data-ttu-id="68364-294">Desarrolladores: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span><span class="sxs-lookup"><span data-stu-id="68364-294">Developers: @roji, @maumar, @bricelam, @smitpatel, @AndriySvyryd, @ajcvickers</span></span>

<span data-ttu-id="68364-295">Talla de camiseta: L</span><span class="sxs-lookup"><span data-stu-id="68364-295">T-shirt size: L</span></span>

<span data-ttu-id="68364-296">Estado: ¡Listo!</span><span class="sxs-lookup"><span data-stu-id="68364-296">Status: Done</span></span>

<span data-ttu-id="68364-297">Además de las características más importantes descritas antes, también hay muchas mejoras más pequeñas programadas para la versión 5.0 a fin de corregir los "elementos excluidos".</span><span class="sxs-lookup"><span data-stu-id="68364-297">In addition to the bigger features outlined above, we also have many smaller improvements scheduled for 5.0 to fix "paper-cuts".</span></span> <span data-ttu-id="68364-298">Tenga en cuenta que muchas de estas mejoras también se incluyen en los temas más generales descritos antes.</span><span class="sxs-lookup"><span data-stu-id="68364-298">Note that many of these enhancements are also covered by the more general themes outlined above.</span></span>

## <a name="below-the-line"></a><span data-ttu-id="68364-299">Nivel inferior</span><span class="sxs-lookup"><span data-stu-id="68364-299">Below-the-line</span></span>

<span data-ttu-id="68364-300">Seguimiento por [problemas etiquetados con `consider-for-next-release`](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span><span class="sxs-lookup"><span data-stu-id="68364-300">Tracked by [issues labeled with `consider-for-next-release`](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)</span></span>

<span data-ttu-id="68364-301">Se trata de correcciones de errores y mejoras **no** programadas actualmente para la versión 5.0, pero que se considerarán objetivos de extensión en función del progreso realizado en el trabajo anterior.</span><span class="sxs-lookup"><span data-stu-id="68364-301">These are bug fixes and enhancements that are **not** currently scheduled for the 5.0 release, but we will look at as stretch goals depending on the progress made on the work above.</span></span>

<span data-ttu-id="68364-302">Además, durante la planeación siempre se tienen en cuenta los [problemas más votados](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).</span><span class="sxs-lookup"><span data-stu-id="68364-302">In addition, we always consider the [most voted issues](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) when planning.</span></span> <span data-ttu-id="68364-303">Excluir cualquiera de estos problemas de una versión siempre es complicado, pero necesitamos un plan realista para los recursos que tenemos.</span><span class="sxs-lookup"><span data-stu-id="68364-303">Cutting any of these issues from a release is always painful, but we do need a realistic plan for the resources we have.</span></span>

## <a name="suggestions"></a><span data-ttu-id="68364-304">Sugerencias</span><span class="sxs-lookup"><span data-stu-id="68364-304">Suggestions</span></span>

<span data-ttu-id="68364-305">Sus comentarios sobre la planeación son importantes.</span><span class="sxs-lookup"><span data-stu-id="68364-305">Your feedback on planning is important.</span></span> <span data-ttu-id="68364-306">La mejor manera de indicar la importancia de un problema es votar (pulgar) por ese problema en GitHub.</span><span class="sxs-lookup"><span data-stu-id="68364-306">The best way to indicate the importance of an issue is to vote (thumbs-up) for that issue on GitHub.</span></span> <span data-ttu-id="68364-307">Estos datos se introducirán después en el [proceso de planeación](xref:core/what-is-new/release-planning) de la próxima versión.</span><span class="sxs-lookup"><span data-stu-id="68364-307">This data will then feed into the [planning process](xref:core/what-is-new/release-planning) for the next release.</span></span>
