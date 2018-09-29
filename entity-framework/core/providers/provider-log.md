---
title: Registro de cambios en el impacto de proveedor - EF Core
author: ajcvickers
ms.author: avickers
ms.date: 08/08/2018
ms.assetid: 7CEF496E-A5B0-4F5F-B68E-529609B23EF9
ms.technology: entity-framework-core
uid: core/providers/provider-log
ms.openlocfilehash: fa1362c84cb1954360d337670fb5fef21e5cf165
ms.sourcegitcommit: 15022dd06d919c29b1189c82611ea32f9fdc6617
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2018
ms.locfileid: "47415749"
---
# <a name="provider-impacting-changes"></a><span data-ttu-id="99df3-102">Cambios en el impacto de proveedor</span><span class="sxs-lookup"><span data-stu-id="99df3-102">Provider-impacting changes</span></span>

<span data-ttu-id="99df3-103">Esta página contiene vínculos para extraer las solicitudes realizadas en el repositorio de EF Core que puede exigir que los autores de otros proveedores de base de datos para reaccionar ante ellos.</span><span class="sxs-lookup"><span data-stu-id="99df3-103">This page contains links to pull requests made on the EF Core repo that may require authors of other database providers to react.</span></span> <span data-ttu-id="99df3-104">La intención es proporcionar un punto de partida para los autores de proveedores de base de datos de terceros existentes al actualizar su proveedor para una nueva versión.</span><span class="sxs-lookup"><span data-stu-id="99df3-104">The intention is to provide a starting point for authors of existing third-party database providers when updating their provider to a new version.</span></span>

<span data-ttu-id="99df3-105">Este registro estamos empezando con los cambios de 2.1 a 2.2.</span><span class="sxs-lookup"><span data-stu-id="99df3-105">We are starting this log with changes from 2.1 to 2.2.</span></span> <span data-ttu-id="99df3-106">Antes de 2.1 se utilizaba el [ `providers-beware` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) y [ `providers-fyi` ](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) etiquetas en nuestros problemas y solicitudes de extracción.</span><span class="sxs-lookup"><span data-stu-id="99df3-106">Prior to 2.1 we used the [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) and [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) labels on our issues and pull requests.</span></span>

## <a name="21-----22"></a><span data-ttu-id="99df3-107">2.1---> 2.2</span><span class="sxs-lookup"><span data-stu-id="99df3-107">2.1 ---> 2.2</span></span>

### <a name="test-only-changes"></a><span data-ttu-id="99df3-108">Cambios sólo para pruebas</span><span class="sxs-lookup"><span data-stu-id="99df3-108">Test-only changes</span></span>

* <span data-ttu-id="99df3-109">https://github.com/aspnet/EntityFrameworkCore/pull/12057 -Permitir delimitadores personalizable de SQL en las pruebas</span><span class="sxs-lookup"><span data-stu-id="99df3-109">https://github.com/aspnet/EntityFrameworkCore/pull/12057 - Allow customizable SQL delimeters in tests</span></span>
  * <span data-ttu-id="99df3-110">Probar los cambios que permiten las comparaciones de punto flotante no estricta en BuiltInDataTypesTestBase</span><span class="sxs-lookup"><span data-stu-id="99df3-110">Test changes that allow non-strict floating point comparisons in BuiltInDataTypesTestBase</span></span>
  * <span data-ttu-id="99df3-111">Cambios de prueba que permiten las pruebas de consulta pueden volver a usar con diferentes delimitadores SQL</span><span class="sxs-lookup"><span data-stu-id="99df3-111">Test changes that allow query tests to be re-used with different SQL delimeters</span></span>
* <span data-ttu-id="99df3-112">https://github.com/aspnet/EntityFrameworkCore/pull/12072 -Agregue DbFunction pruebas a las pruebas de la especificación relacional</span><span class="sxs-lookup"><span data-stu-id="99df3-112">https://github.com/aspnet/EntityFrameworkCore/pull/12072 - Add DbFunction tests to the relational specification tests</span></span>
  * <span data-ttu-id="99df3-113">Por ejemplo, que se pueden ejecutar estas pruebas en todos los proveedores de base de datos</span><span class="sxs-lookup"><span data-stu-id="99df3-113">Such that these tests can be run against all database providers</span></span>
* <span data-ttu-id="99df3-114">https://github.com/aspnet/EntityFrameworkCore/pull/12362 -Limpieza de la prueba Async</span><span class="sxs-lookup"><span data-stu-id="99df3-114">https://github.com/aspnet/EntityFrameworkCore/pull/12362 - Async test cleanup</span></span>
  * <span data-ttu-id="99df3-115">Quitar `Wait` , las llamadas innecesarias async y cambiar el nombre de algunos métodos de prueba</span><span class="sxs-lookup"><span data-stu-id="99df3-115">Remove `Wait` calls, unneeded async, and renamed some test methods</span></span>
* <span data-ttu-id="99df3-116">https://github.com/aspnet/EntityFrameworkCore/pull/12666 -Unificar la infraestructura de registro de prueba</span><span class="sxs-lookup"><span data-stu-id="99df3-116">https://github.com/aspnet/EntityFrameworkCore/pull/12666 - Unify logging test infrastructure</span></span>
  * <span data-ttu-id="99df3-117">Agregar `CreateListLoggerFactory` y quitado algunos infraestructura de registro anteriores, lo que requiere con estas pruebas para reaccionar ante los proveedores</span><span class="sxs-lookup"><span data-stu-id="99df3-117">Added `CreateListLoggerFactory` and removed some previous logging infrastructure, which will require providers using these tests to react</span></span>
* <span data-ttu-id="99df3-118">https://github.com/aspnet/EntityFrameworkCore/pull/12500 -Ejecutar más pruebas de consulta tanto de forma sincrónica y asincrónica</span><span class="sxs-lookup"><span data-stu-id="99df3-118">https://github.com/aspnet/EntityFrameworkCore/pull/12500 - Run more query tests both synchronously and asynchronously</span></span>
  * <span data-ttu-id="99df3-119">Los nombres de prueba y la factorización ha cambiado, lo que requerirá con estas pruebas para reaccionar ante los proveedores</span><span class="sxs-lookup"><span data-stu-id="99df3-119">Test names and factoring has changed, which will require providers using these tests to react</span></span>
* <span data-ttu-id="99df3-120">https://github.com/aspnet/EntityFrameworkCore/pull/12766 -Cambiar el nombre durante la navegación en el modelo ComplexNavigations</span><span class="sxs-lookup"><span data-stu-id="99df3-120">https://github.com/aspnet/EntityFrameworkCore/pull/12766 - Renaming navigations in the ComplexNavigations model</span></span>
  * <span data-ttu-id="99df3-121">Proveedores de uso de estas pruebas que deba reaccionar</span><span class="sxs-lookup"><span data-stu-id="99df3-121">Providers using these tests may need to react</span></span>
* <span data-ttu-id="99df3-122">https://github.com/aspnet/EntityFrameworkCore/pull/12141 : Devuelve el contexto para el grupo en lugar de eliminarlos en las pruebas funcionales</span><span class="sxs-lookup"><span data-stu-id="99df3-122">https://github.com/aspnet/EntityFrameworkCore/pull/12141 - Return the context to the pool instead of disposing in functional tests</span></span>
  * <span data-ttu-id="99df3-123">Este cambio incluye algunas pruebas de refactorización que quizá requiera proveedores reaccionar ante ellos</span><span class="sxs-lookup"><span data-stu-id="99df3-123">This change includes some test refactoring which may require providers to react</span></span>


### <a name="test-and-product-code-changes"></a><span data-ttu-id="99df3-124">Cambios de código de prueba y producto</span><span class="sxs-lookup"><span data-stu-id="99df3-124">Test and product code changes</span></span>

* <span data-ttu-id="99df3-125">https://github.com/aspnet/EntityFrameworkCore/pull/12109 -Consolidar RelationalTypeMapping.Clone métodos</span><span class="sxs-lookup"><span data-stu-id="99df3-125">https://github.com/aspnet/EntityFrameworkCore/pull/12109 - Consolidate RelationalTypeMapping.Clone methods</span></span>
  * <span data-ttu-id="99df3-126">Se permiten cambios en el RelationalTypeMapping 2.1 para una simplificación de las clases derivadas.</span><span class="sxs-lookup"><span data-stu-id="99df3-126">Changes in 2.1 to the RelationalTypeMapping allowed for a simplification in derived classes.</span></span> <span data-ttu-id="99df3-127">No creemos que esto era importantes en los proveedores, pero los proveedores pueden aprovechar las ventajas de este cambio en su tipo derivado asignar clases.</span><span class="sxs-lookup"><span data-stu-id="99df3-127">We don't believe this was breaking to providers, but providers can take advantage of this change in their derived type mapping classes.</span></span>
* <span data-ttu-id="99df3-128">https://github.com/aspnet/EntityFrameworkCore/pull/12069 -Consultas con nombre o etiquetadas</span><span class="sxs-lookup"><span data-stu-id="99df3-128">https://github.com/aspnet/EntityFrameworkCore/pull/12069 - Tagged or named queries</span></span>
  * <span data-ttu-id="99df3-129">Agrega la infraestructura para el etiquetado de las consultas LINQ y tener esas etiquetas se muestran como comentarios en el código SQL.</span><span class="sxs-lookup"><span data-stu-id="99df3-129">Adds infrastructure for tagging LINQ queries and having those tags show up as comments in the SQL.</span></span> <span data-ttu-id="99df3-130">Esto puede requerir que los proveedores reaccionar ante la generación de SQL.</span><span class="sxs-lookup"><span data-stu-id="99df3-130">This may require providers to react in SQL generation.</span></span>
* <span data-ttu-id="99df3-131">https://github.com/aspnet/EntityFrameworkCore/pull/13115 -Compatibilidad con datos espaciales a través de interrupción</span><span class="sxs-lookup"><span data-stu-id="99df3-131">https://github.com/aspnet/EntityFrameworkCore/pull/13115 - Support spatial data via NTS</span></span>
  * <span data-ttu-id="99df3-132">Permite que las asignaciones de tipos y miembros traductores registrarse fuera del proveedor</span><span class="sxs-lookup"><span data-stu-id="99df3-132">Allows type mappings and member translators to be registered outside of the provider</span></span>
    * <span data-ttu-id="99df3-133">Los proveedores deben llamar a bases. FindMapping() en su implementación ITypeMappingSource para que funcione</span><span class="sxs-lookup"><span data-stu-id="99df3-133">Providers must call base.FindMapping() in their ITypeMappingSource implementation for it to work</span></span>
  * <span data-ttu-id="99df3-134">Siga este patrón para agregar compatibilidad espacial en el proveedor que es coherente entre todos los proveedores.</span><span class="sxs-lookup"><span data-stu-id="99df3-134">Follow this pattern to add spatial support to your provider that is consistent across providers.</span></span>
* <span data-ttu-id="99df3-135">https://github.com/aspnet/EntityFrameworkCore/pull/13199 -Agregar depuración mejorada para la creación del proveedor de servicio</span><span class="sxs-lookup"><span data-stu-id="99df3-135">https://github.com/aspnet/EntityFrameworkCore/pull/13199 - Add enhanced debugging for service provider creation</span></span>
  * <span data-ttu-id="99df3-136">Permite DbContextOptionsExtensions implementar una nueva interfaz que puede ayudarle a entender por qué se recompile el proveedor de servicios interno</span><span class="sxs-lookup"><span data-stu-id="99df3-136">Allows DbContextOptionsExtensions to implement a new interface that can help people understand why the internal service provider is being re-built</span></span>
* <span data-ttu-id="99df3-137">https://github.com/aspnet/EntityFrameworkCore/pull/13289 -Agrega CanConnect API para su uso por las comprobaciones de mantenimiento</span><span class="sxs-lookup"><span data-stu-id="99df3-137">https://github.com/aspnet/EntityFrameworkCore/pull/13289 - Adds CanConnect API for use by health checks</span></span>
  * <span data-ttu-id="99df3-138">Esta solicitud agrega el concepto de `CanConnect` que se usará en el estado de ASP.NET Core comprueba para determinar si la base de datos está disponible.</span><span class="sxs-lookup"><span data-stu-id="99df3-138">This PR adds the concept of `CanConnect` which will be used by ASP.NET Core health checks to determine if the database is available.</span></span> <span data-ttu-id="99df3-139">De forma predeterminada, la implementación relacional simplemente llama a `Exist`, pero los proveedores pueden implementar algo diferente si es necesario.</span><span class="sxs-lookup"><span data-stu-id="99df3-139">By default, the relational implementation just calls `Exist`, but providers can implement something different if necessary.</span></span> <span data-ttu-id="99df3-140">No relacionales proveedores debe implementar la nueva API para la comprobación de estado que se pueda usar.</span><span class="sxs-lookup"><span data-stu-id="99df3-140">Non-relational providers will need to implement the new API in order for the health check to be usable.</span></span>
* <span data-ttu-id="99df3-141">https://github.com/aspnet/EntityFrameworkCore/pull/13306 -Actualizar RelationalTypeMapping base para no establecer tamaño DbParameter</span><span class="sxs-lookup"><span data-stu-id="99df3-141">https://github.com/aspnet/EntityFrameworkCore/pull/13306 - Update base RelationalTypeMapping to not set DbParameter Size</span></span>
  * <span data-ttu-id="99df3-142">Detener configuración tamaño de forma predeterminada, ya que puede provocar el truncamiento.</span><span class="sxs-lookup"><span data-stu-id="99df3-142">Stop setting Size by default since it can cause truncation.</span></span> <span data-ttu-id="99df3-143">Los proveedores que necesite agregar su propia lógica si se debe establecer tamaño.</span><span class="sxs-lookup"><span data-stu-id="99df3-143">Providers may need to add their own logic if Size needs to be set.</span></span>
* <span data-ttu-id="99df3-144">https://github.com/aspnet/EntityFrameworkCore/pull/13372 -RevEng: Especifique siempre el tipo de columna para columnas decimales</span><span class="sxs-lookup"><span data-stu-id="99df3-144">https://github.com/aspnet/EntityFrameworkCore/pull/13372 - RevEng: Always specify column type for decimal columns</span></span>
  * <span data-ttu-id="99df3-145">Configure siempre el tipo de columna para columnas decimales en código con scaffold en lugar de configurar por convención.</span><span class="sxs-lookup"><span data-stu-id="99df3-145">Always configure column type for decimal columns in scaffolded code rather than configuring by convention.</span></span>
  * <span data-ttu-id="99df3-146">Los proveedores no deberían requerir cambios en su extremo.</span><span class="sxs-lookup"><span data-stu-id="99df3-146">Providers should not require any changes on their end.</span></span>