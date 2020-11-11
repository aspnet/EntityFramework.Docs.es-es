---
title: Portabilidad de EF6 a EF Core - EF
description: Información general sobre cómo migrar una aplicación de Entity Framework 6 a Entity Framework Core
author: ajcvickers
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/index
ms.openlocfilehash: d50def47e65455c8cf5242cad4386f157148c0bc
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429213"
---
# <a name="porting-from-ef6-to-ef-core"></a><span data-ttu-id="b9da2-103">Portabilidad de EF6 a EF Core</span><span class="sxs-lookup"><span data-stu-id="b9da2-103">Porting from EF6 to EF Core</span></span>

<span data-ttu-id="b9da2-104">Debido a los cambios fundamentales en EF Core, no se recomienda que intente mover una aplicación de EF6 a EF Core, salvo que tenga una razón convincente para hacerlo.</span><span class="sxs-lookup"><span data-stu-id="b9da2-104">Because of the fundamental changes in EF Core we do not recommend attempting to move an EF6 application to EF Core unless you have a compelling reason to make the change.</span></span>
<span data-ttu-id="b9da2-105">Debe ver la migración de EF6 a EF Core como una portabilidad en lugar de una actualización.</span><span class="sxs-lookup"><span data-stu-id="b9da2-105">You should view the move from EF6 to EF Core as a port rather than an upgrade.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b9da2-106">Antes de comenzar el proceso de portabilidad, es importante validar que EF Core cumple los requisitos de acceso a los datos de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b9da2-106">Before you start the porting process it is important to validate that EF Core meets the data access requirements for your application.</span></span>

## <a name="missing-features"></a><span data-ttu-id="b9da2-107">Características que faltan</span><span class="sxs-lookup"><span data-stu-id="b9da2-107">Missing features</span></span>

<span data-ttu-id="b9da2-108">Asegúrese de que EF Core tenga todas las características que necesita para usar en la aplicación.</span><span class="sxs-lookup"><span data-stu-id="b9da2-108">Make sure that EF Core has all the features you need to use in your application.</span></span> <span data-ttu-id="b9da2-109">Vea [Comparación de características](xref:efcore-and-ef6/index) para obtener una comparación detallada del conjunto de características entre EF Core y EF6.</span><span class="sxs-lookup"><span data-stu-id="b9da2-109">See [Feature Comparison](xref:efcore-and-ef6/index) for a detailed comparison of how the feature set in EF Core compares to EF6.</span></span> <span data-ttu-id="b9da2-110">Si faltan algunas características necesarias, asegúrese de que puede compensar la falta de estas características antes de portar a EF Core.</span><span class="sxs-lookup"><span data-stu-id="b9da2-110">If any required features are missing, ensure that you can compensate for the lack of these features before porting to EF Core.</span></span>

## <a name="behavior-changes"></a><span data-ttu-id="b9da2-111">Cambios de comportamiento</span><span class="sxs-lookup"><span data-stu-id="b9da2-111">Behavior changes</span></span>

<span data-ttu-id="b9da2-112">Se trata de una lista no exhaustiva de algunos cambios de comportamiento entre EF6 y EF Core.</span><span class="sxs-lookup"><span data-stu-id="b9da2-112">This is a non-exhaustive list of some changes in behavior between EF6 and EF Core.</span></span> <span data-ttu-id="b9da2-113">Es importante tener esto en cuenta cuando porte la aplicación, ya que pueden cambiar la forma en que se comporta la aplicación, pero no se mostrarán como errores de compilación después de cambiar a EF Core.</span><span class="sxs-lookup"><span data-stu-id="b9da2-113">It is important to keep these in mind as your port your application as they may change the way your application behaves, but will not show up as compilation errors after swapping to EF Core.</span></span>

### <a name="dbsetaddattach-and-graph-behavior"></a><span data-ttu-id="b9da2-114">DbSet.Add/Attach y comportamiento del grafo</span><span class="sxs-lookup"><span data-stu-id="b9da2-114">DbSet.Add/Attach and graph behavior</span></span>

<span data-ttu-id="b9da2-115">En EF6, llamar `DbSet.Add()` en una entidad provoca una búsqueda recursiva de todas las entidades a las que se hace referencia en sus propiedades de navegación.</span><span class="sxs-lookup"><span data-stu-id="b9da2-115">In EF6, calling `DbSet.Add()` on an entity results in a recursive search for all entities referenced in its navigation properties.</span></span> <span data-ttu-id="b9da2-116">Las entidades que se encuentran, y a las que el contexto todavía no ha realizado un seguimiento, también se marcarán como agregadas.</span><span class="sxs-lookup"><span data-stu-id="b9da2-116">Any entities that are found, and are not already tracked by the context, are also marked as added.</span></span> <span data-ttu-id="b9da2-117">`DbSet.Attach()` se comporta de la misma forma, salvo que todas las entidades se marcan como sin cambios.</span><span class="sxs-lookup"><span data-stu-id="b9da2-117">`DbSet.Attach()` behaves the same, except all entities are marked as unchanged.</span></span>

<span data-ttu-id="b9da2-118">**EF Core realiza una búsqueda recursiva similar, pero con algunas reglas ligeramente diferentes.**</span><span class="sxs-lookup"><span data-stu-id="b9da2-118">**EF Core performs a similar recursive search, but with some slightly different rules.**</span></span>

* <span data-ttu-id="b9da2-119">La entidad raíz siempre está en el estado de solicitada (agregada para `DbSet.Add` y sin cambios para `DbSet.Attach`).</span><span class="sxs-lookup"><span data-stu-id="b9da2-119">The root entity is always in the requested state (added for `DbSet.Add` and unchanged for `DbSet.Attach`).</span></span>
* <span data-ttu-id="b9da2-120">**Para las entidades que se encuentran durante la búsqueda recursiva de propiedades de navegación:**</span><span class="sxs-lookup"><span data-stu-id="b9da2-120">**For entities that are found during the recursive search of navigation properties:**</span></span>
  * <span data-ttu-id="b9da2-121">**Si la clave principal de la entidad se genera en el almacén**</span><span class="sxs-lookup"><span data-stu-id="b9da2-121">**If the primary key of the entity is store generated**</span></span>
    * <span data-ttu-id="b9da2-122">Si la clave principal no se establece en un valor, el estado se establece en agregada.</span><span class="sxs-lookup"><span data-stu-id="b9da2-122">If the primary key is not set to a value, the state is set to added.</span></span> <span data-ttu-id="b9da2-123">El valor de la clave principal se considera "no establecido" si se le asigna el valor predeterminado de CLR para el tipo de propiedad (por ejemplo, `0` para `int`, `null` para `string`, etc.).</span><span class="sxs-lookup"><span data-stu-id="b9da2-123">The primary key value is considered "not set" if it is assigned the CLR default value for the property type (for example, `0` for `int`, `null` for `string`, etc.).</span></span>
    * <span data-ttu-id="b9da2-124">Si la clave principal no se establece en un valor, el estado se establece en sin cambios.</span><span class="sxs-lookup"><span data-stu-id="b9da2-124">If the primary key is set to a value, the state is set to unchanged.</span></span>
  * <span data-ttu-id="b9da2-125">Si la clave principal no se genera en la base de datos, la entidad se coloca en el mismo estado que la raíz.</span><span class="sxs-lookup"><span data-stu-id="b9da2-125">If the primary key is not database generated, the entity is put in the same state as the root.</span></span>

### <a name="code-first-database-initialization"></a><span data-ttu-id="b9da2-126">Inicialización de la base de datos de Code First</span><span class="sxs-lookup"><span data-stu-id="b9da2-126">Code First database initialization</span></span>

<span data-ttu-id="b9da2-127">**EF6 tiene cierta magia en torno a la selección de la conexión de base de datos y la inicialización de la base de datos. Algunas de estas reglas incluyen:**</span><span class="sxs-lookup"><span data-stu-id="b9da2-127">**EF6 has a significant amount of magic it performs around selecting the database connection and initializing the database. Some of these rules include:**</span></span>

* <span data-ttu-id="b9da2-128">Si no se realiza ninguna configuración, EF6 seleccionará una base de datos en SQL Express o LocalDb.</span><span class="sxs-lookup"><span data-stu-id="b9da2-128">If no configuration is performed, EF6 will select a database on SQL Express or LocalDb.</span></span>

* <span data-ttu-id="b9da2-129">Si una cadena de conexión con el mismo nombre que el contexto está en el archivo `App/Web.config` de las aplicaciones, se usará esta conexión.</span><span class="sxs-lookup"><span data-stu-id="b9da2-129">If a connection string with the same name as the context is in the applications `App/Web.config` file, this connection will be used.</span></span>

* <span data-ttu-id="b9da2-130">Si la base de datos no existe, se creará.</span><span class="sxs-lookup"><span data-stu-id="b9da2-130">If the database does not exist, it is created.</span></span>

* <span data-ttu-id="b9da2-131">Si no existe ninguna de las tablas del modelo en la base de datos, el esquema del modelo actual se agrega a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b9da2-131">If none of the tables from the model exist in the database, the schema for the current model is added to the database.</span></span> <span data-ttu-id="b9da2-132">Si se habilitan las migraciones, se usan para crear la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b9da2-132">If migrations are enabled, then they are used to create the database.</span></span>

* <span data-ttu-id="b9da2-133">Si la base de datos existe y EF6 ha creado previamente el esquema, entonces se comprueba la compatibilidad de dicho esquema con el modelo actual.</span><span class="sxs-lookup"><span data-stu-id="b9da2-133">If the database exists and EF6 had previously created the schema, then the schema is checked for compatibility with the current model.</span></span> <span data-ttu-id="b9da2-134">Se inicia una excepción si el modelo ha cambiado desde que se creó el esquema.</span><span class="sxs-lookup"><span data-stu-id="b9da2-134">An exception is thrown if the model has changed since the schema was created.</span></span>

<span data-ttu-id="b9da2-135">**EF Core no lleva a cabo nada de esta magia.**</span><span class="sxs-lookup"><span data-stu-id="b9da2-135">**EF Core does not perform any of this magic.**</span></span>

* <span data-ttu-id="b9da2-136">La conexión de base de datos debe estar explícitamente configurada en el código.</span><span class="sxs-lookup"><span data-stu-id="b9da2-136">The database connection must be explicitly configured in code.</span></span>

* <span data-ttu-id="b9da2-137">No se realiza ninguna inicialización.</span><span class="sxs-lookup"><span data-stu-id="b9da2-137">No initialization is performed.</span></span> <span data-ttu-id="b9da2-138">Debe usar `DbContext.Database.Migrate()` para aplicar las migraciones (o `DbContext.Database.EnsureCreated()` y `EnsureDeleted()` para crear o eliminar la base de datos sin usar migraciones).</span><span class="sxs-lookup"><span data-stu-id="b9da2-138">You must use `DbContext.Database.Migrate()` to apply migrations (or `DbContext.Database.EnsureCreated()` and `EnsureDeleted()` to create/delete the database without using migrations).</span></span>

### <a name="code-first-table-naming-convention"></a><span data-ttu-id="b9da2-139">Convención de nomenclatura de tablas de Code First</span><span class="sxs-lookup"><span data-stu-id="b9da2-139">Code First table naming convention</span></span>

<span data-ttu-id="b9da2-140">EF6 ejecuta el nombre de clase de entidad a través de un servicio de pluralización para calcular el nombre de tabla predeterminado al que está asignada la entidad.</span><span class="sxs-lookup"><span data-stu-id="b9da2-140">EF6 runs the entity class name through a pluralization service to calculate the default table name that the entity is mapped to.</span></span>

<span data-ttu-id="b9da2-141">EF Core usa el nombre de la propiedad `DbSet` en la que se expone la entidad en el contexto derivado.</span><span class="sxs-lookup"><span data-stu-id="b9da2-141">EF Core uses the name of the `DbSet` property that the entity is exposed in on the derived context.</span></span> <span data-ttu-id="b9da2-142">Si la entidad no tiene una propiedad `DbSet`, se utiliza el nombre de clase.</span><span class="sxs-lookup"><span data-stu-id="b9da2-142">If the entity does not have a `DbSet` property, then the class name is used.</span></span>
