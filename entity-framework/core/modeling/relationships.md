---
title: 'Relaciones: EF Core'
description: Cómo configurar relaciones entre tipos de entidad al utilizar Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/relationships
ms.openlocfilehash: 567d212ddf41f33ee32443d85d2a17234fbc026b
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430188"
---
# <a name="relationships"></a><span data-ttu-id="dd1f7-103">Relaciones</span><span class="sxs-lookup"><span data-stu-id="dd1f7-103">Relationships</span></span>

<span data-ttu-id="dd1f7-104">Una relación define el modo en que dos entidades se relacionan entre sí.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-104">A relationship defines how two entities relate to each other.</span></span> <span data-ttu-id="dd1f7-105">En una base de datos relacional, se representa mediante una restricción FOREIGN KEY.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-105">In a relational database, this is represented by a foreign key constraint.</span></span>

> [!NOTE]  
> <span data-ttu-id="dd1f7-106">La mayoría de los ejemplos de este artículo usan una relación de uno a varios para demostrar los conceptos.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-106">Most of the samples in this article use a one-to-many relationship to demonstrate concepts.</span></span> <span data-ttu-id="dd1f7-107">Para obtener ejemplos de relaciones de uno a uno y de varios a varios, consulte la sección [otros patrones de relación](#other-relationship-patterns) al final del artículo.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-107">For examples of one-to-one and many-to-many relationships see the [Other Relationship Patterns](#other-relationship-patterns) section at the end of the article.</span></span>

## <a name="definition-of-terms"></a><span data-ttu-id="dd1f7-108">Definición de términos</span><span class="sxs-lookup"><span data-stu-id="dd1f7-108">Definition of terms</span></span>

<span data-ttu-id="dd1f7-109">Hay una serie de términos que se usan para describir las relaciones</span><span class="sxs-lookup"><span data-stu-id="dd1f7-109">There are a number of terms used to describe relationships</span></span>

* <span data-ttu-id="dd1f7-110">**Entidad dependiente:** Esta es la entidad que contiene las propiedades de clave externa.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-110">**Dependent entity:** This is the entity that contains the foreign key properties.</span></span> <span data-ttu-id="dd1f7-111">A veces se conoce como "secundario" de la relación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-111">Sometimes referred to as the 'child' of the relationship.</span></span>

* <span data-ttu-id="dd1f7-112">**Entidad de entidad** de seguridad: Esta es la entidad que contiene las propiedades de clave principal/alternativa.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-112">**Principal entity:** This is the entity that contains the primary/alternate key properties.</span></span> <span data-ttu-id="dd1f7-113">A veces se denomina "primario" de la relación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-113">Sometimes referred to as the 'parent' of the relationship.</span></span>

* <span data-ttu-id="dd1f7-114">**Clave principal:** Propiedades que identifican de forma única la entidad principal.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-114">**Principal key:** The properties that uniquely identify the principal entity.</span></span> <span data-ttu-id="dd1f7-115">Puede ser la clave principal o una clave alternativa.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-115">This may be the primary key or an alternate key.</span></span>

* <span data-ttu-id="dd1f7-116">**Clave externa:** Propiedades de la entidad dependiente que se usan para almacenar los valores de clave principal para la entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-116">**Foreign key:** The properties in the dependent entity that are used to store the principal key values for the related entity.</span></span>

* <span data-ttu-id="dd1f7-117">**Propiedad de navegación:** Propiedad definida en la entidad principal o dependiente que hace referencia a la entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-117">**Navigation property:** A property defined on the principal and/or dependent entity that references the related entity.</span></span>

  * <span data-ttu-id="dd1f7-118">**Propiedad de navegación de colección:** Propiedad de navegación que contiene referencias a muchas entidades relacionadas.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-118">**Collection navigation property:** A navigation property that contains references to many related entities.</span></span>

  * <span data-ttu-id="dd1f7-119">**Propiedad de navegación de referencia:** Propiedad de navegación que contiene una referencia a una sola entidad relacionada.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-119">**Reference navigation property:** A navigation property that holds a reference to a single related entity.</span></span>

  * <span data-ttu-id="dd1f7-120">**Propiedad de navegación inversa:** Al discutir una propiedad de navegación determinada, este término hace referencia a la propiedad de navegación en el otro extremo de la relación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-120">**Inverse navigation property:** When discussing a particular navigation property, this term refers to the navigation property on the other end of the relationship.</span></span>
  
* <span data-ttu-id="dd1f7-121">**Relación que hace referencia a sí misma:** Una relación en la que los tipos de entidad dependiente y principal son iguales.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-121">**Self-referencing relationship:** A relationship in which the dependent and the principal entity types are the same.</span></span>

<span data-ttu-id="dd1f7-122">En el código siguiente se muestra una relación de uno a varios entre `Blog` y `Post`</span><span class="sxs-lookup"><span data-stu-id="dd1f7-122">The following code shows a one-to-many relationship between `Blog` and `Post`</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* <span data-ttu-id="dd1f7-123">`Post` es la entidad dependiente.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-123">`Post` is the dependent entity</span></span>

* <span data-ttu-id="dd1f7-124">`Blog` es la entidad principal</span><span class="sxs-lookup"><span data-stu-id="dd1f7-124">`Blog` is the principal entity</span></span>

* <span data-ttu-id="dd1f7-125">`Blog.BlogId` es la clave principal (en este caso, es una clave principal en lugar de una clave alternativa)</span><span class="sxs-lookup"><span data-stu-id="dd1f7-125">`Blog.BlogId` is the principal key (in this case it is a primary key rather than an alternate key)</span></span>

* <span data-ttu-id="dd1f7-126">`Post.BlogId` es la clave externa</span><span class="sxs-lookup"><span data-stu-id="dd1f7-126">`Post.BlogId` is the foreign key</span></span>

* <span data-ttu-id="dd1f7-127">`Post.Blog` propiedad de navegación de referencia</span><span class="sxs-lookup"><span data-stu-id="dd1f7-127">`Post.Blog` is a reference navigation property</span></span>

* <span data-ttu-id="dd1f7-128">`Blog.Posts` es una propiedad de navegación de colección</span><span class="sxs-lookup"><span data-stu-id="dd1f7-128">`Blog.Posts` is a collection navigation property</span></span>

* <span data-ttu-id="dd1f7-129">`Post.Blog` es la propiedad de navegación inversa de `Blog.Posts` (y viceversa).</span><span class="sxs-lookup"><span data-stu-id="dd1f7-129">`Post.Blog` is the inverse navigation property of `Blog.Posts` (and vice versa)</span></span>

## <a name="conventions"></a><span data-ttu-id="dd1f7-130">Convenciones</span><span class="sxs-lookup"><span data-stu-id="dd1f7-130">Conventions</span></span>

<span data-ttu-id="dd1f7-131">De forma predeterminada, se creará una relación cuando se detecte una propiedad de navegación en un tipo.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-131">By default, a relationship will be created when there is a navigation property discovered on a type.</span></span> <span data-ttu-id="dd1f7-132">Una propiedad se considera una propiedad de navegación si el tipo al que señala no se puede asignar como un tipo escalar por el proveedor de base de datos actual.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-132">A property is considered a navigation property if the type it points to can not be mapped as a scalar type by the current database provider.</span></span>

> [!NOTE]  
> <span data-ttu-id="dd1f7-133">Las relaciones detectadas por la Convención siempre tendrán como destino la clave principal de la entidad principal.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-133">Relationships that are discovered by convention will always target the primary key of the principal entity.</span></span> <span data-ttu-id="dd1f7-134">Para elegir como destino una clave alternativa, se debe realizar una configuración adicional mediante la API fluida.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-134">To target an alternate key, additional configuration must be performed using the Fluent API.</span></span>

### <a name="fully-defined-relationships"></a><span data-ttu-id="dd1f7-135">Relaciones totalmente definidas</span><span class="sxs-lookup"><span data-stu-id="dd1f7-135">Fully defined relationships</span></span>

<span data-ttu-id="dd1f7-136">El patrón más común para las relaciones es tener propiedades de navegación definidas en ambos extremos de la relación y una propiedad de clave externa definida en la clase de entidad dependiente.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-136">The most common pattern for relationships is to have navigation properties defined on both ends of the relationship and a foreign key property defined in the dependent entity class.</span></span>

* <span data-ttu-id="dd1f7-137">Si se encuentra un par de propiedades de navegación entre dos tipos, se configurarán como propiedades de navegación inversa de la misma relación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-137">If a pair of navigation properties is found between two types, then they will be configured as inverse navigation properties of the same relationship.</span></span>

* <span data-ttu-id="dd1f7-138">Si la entidad dependiente contiene una propiedad con un nombre que coincide con uno de estos patrones, se configurará como la clave externa:</span><span class="sxs-lookup"><span data-stu-id="dd1f7-138">If the dependent entity contains a property with a name matching one of these patterns then it will be configured as the foreign key:</span></span>
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

<span data-ttu-id="dd1f7-139">En este ejemplo, las propiedades resaltadas se usarán para configurar la relación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-139">In this example the highlighted properties will be used to configure the relationship.</span></span>

> [!NOTE]
> <span data-ttu-id="dd1f7-140">Si la propiedad es la clave principal o es de un tipo no compatible con la clave principal, no se configurará como clave externa.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-140">If the property is the primary key or is of a type not compatible with the principal key then it won't be configured as the foreign key.</span></span>

> [!NOTE]
> <span data-ttu-id="dd1f7-141">Antes de EF Core 3,0 la propiedad denominada exactamente igual que la propiedad de clave principal [también coincidía con la clave externa](https://github.com/dotnet/efcore/issues/13274) .</span><span class="sxs-lookup"><span data-stu-id="dd1f7-141">Before EF Core 3.0 the property named exactly the same as the principal key property [was also matched as the foreign key](https://github.com/dotnet/efcore/issues/13274)</span></span>

### <a name="no-foreign-key-property"></a><span data-ttu-id="dd1f7-142">No hay propiedad de clave externa</span><span class="sxs-lookup"><span data-stu-id="dd1f7-142">No foreign key property</span></span>

<span data-ttu-id="dd1f7-143">Aunque se recomienda tener una propiedad de clave externa definida en la clase de entidad dependiente, no es necesario.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-143">While it is recommended to have a foreign key property defined in the dependent entity class, it is not required.</span></span> <span data-ttu-id="dd1f7-144">Si no se encuentra ninguna propiedad de clave externa, se introducirá una [propiedad de clave externa de sombra](xref:core/modeling/shadow-properties) con el nombre `<navigation property name><principal key property name>` o `<principal entity name><principal key property name>` si no hay ninguna navegación presente en el tipo dependiente.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-144">If no foreign key property is found, a [shadow foreign key property](xref:core/modeling/shadow-properties) will be introduced with the name `<navigation property name><principal key property name>` or `<principal entity name><principal key property name>` if no navigation is present on the dependent type.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

<span data-ttu-id="dd1f7-145">En este ejemplo, la clave externa de la sombra se debe a que, si `BlogId` se antepone el nombre de navegación, sería redundante.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-145">In this example the shadow foreign key is `BlogId` because prepending the navigation name would be redundant.</span></span>

> [!NOTE]
> <span data-ttu-id="dd1f7-146">Si ya existe una propiedad con el mismo nombre, el nombre de la propiedad Shadow tendrá como sufijo un número.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-146">If a property with the same name already exists then the shadow property name will be suffixed with a number.</span></span>

### <a name="single-navigation-property"></a><span data-ttu-id="dd1f7-147">Propiedad de navegación única</span><span class="sxs-lookup"><span data-stu-id="dd1f7-147">Single navigation property</span></span>

<span data-ttu-id="dd1f7-148">Incluir solo una propiedad de navegación (sin navegación inversa y sin propiedad de clave externa) es suficiente para tener una relación definida por Convención.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-148">Including just one navigation property (no inverse navigation, and no foreign key property) is enough to have a relationship defined by convention.</span></span> <span data-ttu-id="dd1f7-149">También puede tener una propiedad de navegación única y una propiedad de clave externa.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-149">You can also have a single navigation property and a foreign key property.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a><span data-ttu-id="dd1f7-150">Limitaciones</span><span class="sxs-lookup"><span data-stu-id="dd1f7-150">Limitations</span></span>

<span data-ttu-id="dd1f7-151">Cuando hay varias propiedades de navegación definidas entre dos tipos (es decir, más de un par de navegaciones que apuntan entre sí), las relaciones representadas por las propiedades de navegación son ambiguas.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-151">When there are multiple navigation properties defined between two types (that is, more than just one pair of navigations that point to each other) the relationships represented by the navigation properties are ambiguous.</span></span> <span data-ttu-id="dd1f7-152">Tendrá que configurarlos manualmente para resolver la ambigüedad.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-152">You will need to manually configure them to resolve the ambiguity.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="dd1f7-153">Eliminación en cascada</span><span class="sxs-lookup"><span data-stu-id="dd1f7-153">Cascade delete</span></span>

<span data-ttu-id="dd1f7-154">Por Convención, la eliminación en cascada se establecerá en *Cascade* para las relaciones necesarias y *ClientSetNull* para las relaciones opcionales.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-154">By convention, cascade delete will be set to *Cascade* for required relationships and *ClientSetNull* for optional relationships.</span></span> <span data-ttu-id="dd1f7-155">*Cascade* significa que las entidades dependientes también se eliminan.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-155">*Cascade* means dependent entities are also deleted.</span></span> <span data-ttu-id="dd1f7-156">*ClientSetNull* significa que las entidades dependientes que no se cargan en la memoria permanecerán sin cambios y deben eliminarse manualmente o actualizarse para que apunten a una entidad principal válida.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-156">*ClientSetNull* means that dependent entities that are not loaded into memory will remain unchanged and must be manually deleted, or updated to point to a valid principal entity.</span></span> <span data-ttu-id="dd1f7-157">En el caso de las entidades que se cargan en memoria, EF Core intentará establecer las propiedades de clave externa en NULL.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-157">For entities that are loaded into memory, EF Core will attempt to set the foreign key properties to null.</span></span>

<span data-ttu-id="dd1f7-158">Vea la sección [relaciones obligatorias y opcionales](#required-and-optional-relationships) para ver la diferencia entre las relaciones obligatorias y opcionales.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-158">See the [Required and Optional Relationships](#required-and-optional-relationships) section for the difference between required and optional relationships.</span></span>

<span data-ttu-id="dd1f7-159">Consulte [eliminación en cascada](xref:core/saving/cascade-delete) para obtener más detalles sobre los distintos comportamientos de eliminación y los valores predeterminados que usa la Convención.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-159">See [Cascade Delete](xref:core/saving/cascade-delete) for more details about the different delete behaviors and the defaults used by convention.</span></span>

## <a name="manual-configuration"></a><span data-ttu-id="dd1f7-160">Configuración manual</span><span class="sxs-lookup"><span data-stu-id="dd1f7-160">Manual configuration</span></span>

### <a name="fluent-api"></a>[<span data-ttu-id="dd1f7-161">API fluida</span><span class="sxs-lookup"><span data-stu-id="dd1f7-161">Fluent API</span></span>](#tab/fluent-api)

<span data-ttu-id="dd1f7-162">Para configurar una relación en la API fluida, empiece por identificar las propiedades de navegación que componen la relación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-162">To configure a relationship in the Fluent API, you start by identifying the navigation properties that make up the relationship.</span></span> <span data-ttu-id="dd1f7-163">`HasOne` o `HasMany` identifica la propiedad de navegación en el tipo de entidad en el que va a comenzar la configuración.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-163">`HasOne` or `HasMany` identifies the navigation property on the entity type you are beginning the configuration on.</span></span> <span data-ttu-id="dd1f7-164">A continuación, encadenar una llamada a `WithOne` o `WithMany` para identificar la navegación inversa.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-164">You then chain a call to `WithOne` or `WithMany` to identify the inverse navigation.</span></span> <span data-ttu-id="dd1f7-165">`HasOne`/`WithOne`se utilizan para las propiedades de navegación de referencia y `HasMany` / `WithMany` se utilizan para las propiedades de navegación de colección.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-165">`HasOne`/`WithOne` are used for reference navigation properties and `HasMany`/`WithMany` are used for collection navigation properties.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[<span data-ttu-id="dd1f7-166">Anotaciones de datos</span><span class="sxs-lookup"><span data-stu-id="dd1f7-166">Data annotations</span></span>](#tab/data-annotations)

<span data-ttu-id="dd1f7-167">Puede usar las anotaciones de datos para configurar cómo se emparejan las propiedades de navegación en las entidades de entidad de seguridad y dependencias.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-167">You can use the Data Annotations to configure how navigation properties on the dependent and principal entities pair up.</span></span> <span data-ttu-id="dd1f7-168">Normalmente, esto se hace cuando hay más de un par de propiedades de navegación entre dos tipos de entidad.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-168">This is typically done when there is more than one pair of navigation properties between two entity types.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> <span data-ttu-id="dd1f7-169">Solo puede utilizar [required] en las propiedades de la entidad dependiente para afectar a la necesidad de la relación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-169">You can only use [Required] on properties on the dependent entity to impact the requiredness of the relationship.</span></span> <span data-ttu-id="dd1f7-170">[Obligatorio] en la navegación de la entidad principal normalmente se omite, pero puede hacer que la entidad se convierta en la dependiente.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-170">[Required] on the navigation from the principal entity is usually ignored, but it may cause the entity to become the dependent one.</span></span>

> [!NOTE]
> <span data-ttu-id="dd1f7-171">Las anotaciones de datos `[ForeignKey]` y `[InverseProperty]` están disponibles en el `System.ComponentModel.DataAnnotations.Schema` espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-171">The data annotations `[ForeignKey]` and `[InverseProperty]` are available in the `System.ComponentModel.DataAnnotations.Schema` namespace.</span></span> <span data-ttu-id="dd1f7-172">`[Required]` está disponible en el `System.ComponentModel.DataAnnotations` espacio de nombres.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-172">`[Required]` is available in the `System.ComponentModel.DataAnnotations` namespace.</span></span>

---

### <a name="single-navigation-property"></a><span data-ttu-id="dd1f7-173">Propiedad de navegación única</span><span class="sxs-lookup"><span data-stu-id="dd1f7-173">Single navigation property</span></span>

<span data-ttu-id="dd1f7-174">Si solo tiene una propiedad de navegación, hay sobrecargas sin parámetros de `WithOne` y `WithMany` .</span><span class="sxs-lookup"><span data-stu-id="dd1f7-174">If you only have one navigation property then there are parameterless overloads of `WithOne` and `WithMany`.</span></span> <span data-ttu-id="dd1f7-175">Esto indica que hay conceptualmente una referencia o una colección en el otro extremo de la relación, pero no hay ninguna propiedad de navegación incluida en la clase de entidad.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-175">This indicates that there is conceptually a reference or collection on the other end of the relationship, but there is no navigation property included in the entity class.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a><span data-ttu-id="dd1f7-176">Configurar propiedades de navegación</span><span class="sxs-lookup"><span data-stu-id="dd1f7-176">Configuring navigation properties</span></span>

> [!NOTE]
> <span data-ttu-id="dd1f7-177">Esta característica se agregó en EF Core 5.0.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-177">This feature was added in EF Core 5.0.</span></span>

<span data-ttu-id="dd1f7-178">Una vez creada la propiedad de navegación, puede que necesite configurarla más adelante.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-178">After the navigation property has been created, you may need to further configure it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> <span data-ttu-id="dd1f7-179">Esta llamada no se puede usar para crear una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-179">This call cannot be used to create a navigation property.</span></span> <span data-ttu-id="dd1f7-180">Solo se usa para configurar una propiedad de navegación que se ha creado previamente definiendo una relación o una Convención.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-180">It is only used to configure a navigation property which has been previously created by defining a relationship or from a convention.</span></span>

### <a name="foreign-key"></a><span data-ttu-id="dd1f7-181">Clave externa</span><span class="sxs-lookup"><span data-stu-id="dd1f7-181">Foreign key</span></span>

#### <a name="fluent-api-simple-key"></a>[<span data-ttu-id="dd1f7-182">API fluida (clave simple)</span><span class="sxs-lookup"><span data-stu-id="dd1f7-182">Fluent API (simple key)</span></span>](#tab/fluent-api-simple-key)

<span data-ttu-id="dd1f7-183">Puede usar la API fluida para configurar qué propiedad se debe usar como la propiedad de clave externa para una relación determinada:</span><span class="sxs-lookup"><span data-stu-id="dd1f7-183">You can use the Fluent API to configure which property should be used as the foreign key property for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[<span data-ttu-id="dd1f7-184">API fluida (clave compuesta)</span><span class="sxs-lookup"><span data-stu-id="dd1f7-184">Fluent API (composite key)</span></span>](#tab/fluent-api-composite-key)

<span data-ttu-id="dd1f7-185">Puede usar la API fluida para configurar qué propiedades deben usarse como propiedades de clave externa compuesta para una relación determinada:</span><span class="sxs-lookup"><span data-stu-id="dd1f7-185">You can use the Fluent API to configure which properties should be used as the composite foreign key properties for a given relationship:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[<span data-ttu-id="dd1f7-186">Anotaciones de datos (clave simple)</span><span class="sxs-lookup"><span data-stu-id="dd1f7-186">Data annotations (simple key)</span></span>](#tab/data-annotations-simple-key)

<span data-ttu-id="dd1f7-187">Puede usar las anotaciones de datos para configurar qué propiedad se debe usar como la propiedad de clave externa para una relación determinada.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-187">You can use the Data Annotations to configure which property should be used as the foreign key property for a given relationship.</span></span> <span data-ttu-id="dd1f7-188">Normalmente, esto se hace cuando la Convención no detecta la propiedad de clave externa:</span><span class="sxs-lookup"><span data-stu-id="dd1f7-188">This is typically done when the foreign key property is not discovered by convention:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> <span data-ttu-id="dd1f7-189">La `[ForeignKey]` anotación puede colocarse en cualquier propiedad de navegación de la relación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-189">The `[ForeignKey]` annotation can be placed on either navigation property in the relationship.</span></span> <span data-ttu-id="dd1f7-190">No es necesario que vaya a la propiedad de navegación en la clase de entidad dependiente.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-190">It does not need to go on the navigation property in the dependent entity class.</span></span>

> [!NOTE]
> <span data-ttu-id="dd1f7-191">`[ForeignKey]`No es necesario que la propiedad especificada mediante en una propiedad de navegación exista en el tipo dependiente.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-191">The property specified using `[ForeignKey]` on a navigation property doesn't need to exist on the dependent type.</span></span> <span data-ttu-id="dd1f7-192">En este caso, el nombre especificado se utilizará para crear una clave externa de sombra.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-192">In this case the specified name will be used to create a shadow foreign key.</span></span>

---

#### <a name="shadow-foreign-key"></a><span data-ttu-id="dd1f7-193">Clave externa de sombra</span><span class="sxs-lookup"><span data-stu-id="dd1f7-193">Shadow foreign key</span></span>

<span data-ttu-id="dd1f7-194">Puede usar la sobrecarga de cadena de `HasForeignKey(...)` para configurar una propiedad Shadow como clave externa (consulte [propiedades de sombra](xref:core/modeling/shadow-properties) para obtener más información).</span><span class="sxs-lookup"><span data-stu-id="dd1f7-194">You can use the string overload of `HasForeignKey(...)` to configure a shadow property as a foreign key (see [Shadow Properties](xref:core/modeling/shadow-properties) for more information).</span></span> <span data-ttu-id="dd1f7-195">Se recomienda agregar explícitamente la propiedad Shadow al modelo antes de usarla como clave externa (como se muestra a continuación).</span><span class="sxs-lookup"><span data-stu-id="dd1f7-195">We recommend explicitly adding the shadow property to the model before using it as a foreign key (as shown below).</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a><span data-ttu-id="dd1f7-196">Nombre de restricción de clave externa</span><span class="sxs-lookup"><span data-stu-id="dd1f7-196">Foreign key constraint name</span></span>

<span data-ttu-id="dd1f7-197">Por Convención, cuando el destino es una base de datos relacional, las restricciones Foreign Key se denominan FK \_ \<dependent type name> \_ \<principal type name> \_ \<foreign key property name> .</span><span class="sxs-lookup"><span data-stu-id="dd1f7-197">By convention, when targeting a relational database, foreign key constraints are named FK\_\<dependent type name>\_\<principal type name>\_\<foreign key property name>.</span></span> <span data-ttu-id="dd1f7-198">En el caso de las claves externas compuestas, \<foreign key property name> se convierte en una lista de nombres de propiedades de clave externa separadas por guiones bajos.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-198">For composite foreign keys, \<foreign key property name> becomes an underscore separated list of foreign key property names.</span></span>

<span data-ttu-id="dd1f7-199">También puede configurar el nombre de la restricción de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="dd1f7-199">You can also configure the constraint name as follows:</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a><span data-ttu-id="dd1f7-200">Sin propiedad de navegación</span><span class="sxs-lookup"><span data-stu-id="dd1f7-200">Without navigation property</span></span>

<span data-ttu-id="dd1f7-201">No es necesario proporcionar una propiedad de navegación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-201">You don't necessarily need to provide a navigation property.</span></span> <span data-ttu-id="dd1f7-202">Simplemente puede proporcionar una clave externa en un lado de la relación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-202">You can simply provide a foreign key on one side of the relationship.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a><span data-ttu-id="dd1f7-203">Clave principal</span><span class="sxs-lookup"><span data-stu-id="dd1f7-203">Principal key</span></span>

<span data-ttu-id="dd1f7-204">Si desea que la clave externa haga referencia a una propiedad que no sea la clave principal, puede usar la API fluida para configurar la propiedad de clave principal de la relación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-204">If you want the foreign key to reference a property other than the primary key, you can use the Fluent API to configure the principal key property for the relationship.</span></span> <span data-ttu-id="dd1f7-205">La propiedad que se configura como clave principal se configurará automáticamente como una [clave alternativa](xref:core/modeling/keys#alternate-keys).</span><span class="sxs-lookup"><span data-stu-id="dd1f7-205">The property that you configure as the principal key will automatically be setup as an [alternate key](xref:core/modeling/keys#alternate-keys).</span></span>

#### <a name="simple-key"></a>[<span data-ttu-id="dd1f7-206">Clave simple</span><span class="sxs-lookup"><span data-stu-id="dd1f7-206">Simple key</span></span>](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[<span data-ttu-id="dd1f7-207">Clave compuesta</span><span class="sxs-lookup"><span data-stu-id="dd1f7-207">Composite key</span></span>](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> <span data-ttu-id="dd1f7-208">El orden en el que se especifican las propiedades de clave principal debe coincidir con el orden en que se especifican para la clave externa.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-208">The order in which you specify principal key properties must match the order in which they are specified for the foreign key.</span></span>

---

### <a name="required-and-optional-relationships"></a><span data-ttu-id="dd1f7-209">Relaciones obligatorias y opcionales</span><span class="sxs-lookup"><span data-stu-id="dd1f7-209">Required and optional relationships</span></span>

<span data-ttu-id="dd1f7-210">Puede usar la API fluida para configurar si la relación es obligatoria u opcional.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-210">You can use the Fluent API to configure whether the relationship is required or optional.</span></span> <span data-ttu-id="dd1f7-211">En última instancia, controla si la propiedad de clave externa es obligatoria u opcional.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-211">Ultimately this controls whether the foreign key property is required or optional.</span></span> <span data-ttu-id="dd1f7-212">Esto es muy útil cuando se usa una clave externa de estado de sombra.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-212">This is most useful when you are using a shadow state foreign key.</span></span> <span data-ttu-id="dd1f7-213">Si tiene una propiedad de clave externa en la clase de entidad, la necesidad de la relación se determina en función de si la propiedad de clave externa es necesaria u opcional (vea [propiedades obligatorias y opcionales](xref:core/modeling/entity-properties#required-and-optional-properties) para obtener más información).</span><span class="sxs-lookup"><span data-stu-id="dd1f7-213">If you have a foreign key property in your entity class then the requiredness of the relationship is determined based on whether the foreign key property is required or optional (see [Required and Optional properties](xref:core/modeling/entity-properties#required-and-optional-properties) for more information).</span></span>

<span data-ttu-id="dd1f7-214">Las propiedades de clave externa se encuentran en el tipo de entidad dependiente, por lo que si se configuran como requeridas, significa que todas las entidades dependientes deben tener una entidad principal correspondiente.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-214">The foreign key properties are located on the dependent entity type, so if they are configured as required it means that every dependent entity is required to have a corresponding principal entity.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> <span data-ttu-id="dd1f7-215">`IsRequired(false)`La llamada también hace que la propiedad de clave externa sea opcional a menos que esté configurada de otro modo.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-215">Calling `IsRequired(false)` also makes the foreign key property optional unless it's configured otherwise.</span></span>

### <a name="cascade-delete"></a><span data-ttu-id="dd1f7-216">Eliminación en cascada</span><span class="sxs-lookup"><span data-stu-id="dd1f7-216">Cascade delete</span></span>

<span data-ttu-id="dd1f7-217">Puede usar la API fluida para configurar explícitamente el comportamiento de eliminación en cascada para una relación determinada.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-217">You can use the Fluent API to configure the cascade delete behavior for a given relationship explicitly.</span></span>

<span data-ttu-id="dd1f7-218">Consulte la [eliminación en cascada](xref:core/saving/cascade-delete) para obtener una explicación detallada de cada opción.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-218">See [Cascade Delete](xref:core/saving/cascade-delete) for a detailed discussion of each option.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a><span data-ttu-id="dd1f7-219">Otros patrones de relación</span><span class="sxs-lookup"><span data-stu-id="dd1f7-219">Other relationship patterns</span></span>

### <a name="one-to-one"></a><span data-ttu-id="dd1f7-220">Uno a uno</span><span class="sxs-lookup"><span data-stu-id="dd1f7-220">One-to-one</span></span>

<span data-ttu-id="dd1f7-221">Una relación de uno a uno tiene una propiedad de navegación de referencia en ambos lados.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-221">One to one relationships have a reference navigation property on both sides.</span></span> <span data-ttu-id="dd1f7-222">Siguen las mismas convenciones que las relaciones uno a varios, pero se incluye un índice único en la propiedad de clave externa para asegurarse de que solo un dependiente esté relacionado con cada entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-222">They follow the same conventions as one-to-many relationships, but a unique index is introduced on the foreign key property to ensure only one dependent is related to each principal.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> <span data-ttu-id="dd1f7-223">EF elegirá una de las entidades como dependiente en función de su capacidad para detectar una propiedad de clave externa.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-223">EF will choose one of the entities to be the dependent based on its ability to detect a foreign key property.</span></span> <span data-ttu-id="dd1f7-224">Si se elige la entidad equivocada como dependiente, puede usar la API fluida para corregir este problema.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-224">If the wrong entity is chosen as the dependent, you can use the Fluent API to correct this.</span></span>

<span data-ttu-id="dd1f7-225">Al configurar la relación con la API fluida, se usan los `HasOne` `WithOne` métodos y.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-225">When configuring the relationship with the Fluent API, you use the `HasOne` and `WithOne` methods.</span></span>

<span data-ttu-id="dd1f7-226">Al configurar la clave externa, debe especificar el tipo de entidad dependiente: Observe el parámetro genérico que se proporciona `HasForeignKey` en la siguiente lista.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-226">When configuring the foreign key you need to specify the dependent entity type - notice the generic parameter provided to `HasForeignKey` in the listing below.</span></span> <span data-ttu-id="dd1f7-227">En una relación uno a varios, es evidente que la entidad con la navegación de referencia es el dependiente y el que tiene la colección es la entidad de seguridad.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-227">In a one-to-many relationship it is clear that the entity with the reference navigation is the dependent and the one with the collection is the principal.</span></span> <span data-ttu-id="dd1f7-228">Pero esto no es así en una relación uno a uno; por lo tanto, la necesidad de definirla explícitamente.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-228">But this is not so in a one-to-one relationship - hence the need to explicitly define it.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

<span data-ttu-id="dd1f7-229">El lado dependiente se considera opcional de forma predeterminada, pero se puede configurar según sea necesario.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-229">The dependent side is considered optional by default, but can be configured as required.</span></span> <span data-ttu-id="dd1f7-230">Sin embargo, EF no validará si se proporcionó una entidad dependiente, por lo que esta configuración solo marcará una diferencia cuando la asignación de base de datos permita su aplicación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-230">However EF will not validate whether a dependent entity was provided, so this configuration will only make a difference when the database mapping allows it to be enforced.</span></span> <span data-ttu-id="dd1f7-231">Un escenario común para esto son los tipos de referencia que usan la división de tablas de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-231">A common scenario for this are reference owned types that use table splitting by default.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=Required&highlight=11-12)]

<span data-ttu-id="dd1f7-232">Con esta configuración, las columnas correspondientes a se `ShippingAddress` marcarán como que no aceptan valores NULL en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-232">With this configuration the columns corresponding to `ShippingAddress` will be marked as non-nullable in the database.</span></span>

> [!NOTE]
> <span data-ttu-id="dd1f7-233">Si usa [tipos de referencia que no aceptan valores NULL](/dotnet/csharp/nullable-references) , `IsRequired` no es necesario llamar a.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-233">If you are using [non-nullable reference types](/dotnet/csharp/nullable-references) calling `IsRequired` is not necessary.</span></span>

> [!NOTE]
> <span data-ttu-id="dd1f7-234">La capacidad de configurar si el dependiente es necesario se agregó en EF Core 5,0.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-234">The ability to configure whether the dependent is required was added in EF Core 5.0.</span></span>

### <a name="many-to-many"></a><span data-ttu-id="dd1f7-235">Varios a varios</span><span class="sxs-lookup"><span data-stu-id="dd1f7-235">Many-to-many</span></span>

<span data-ttu-id="dd1f7-236">Una relación de varios a varios requiere una propiedad de navegación de colección en ambos lados.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-236">Many to many relationships require a collection navigation property on both sides.</span></span> <span data-ttu-id="dd1f7-237">Se detectarán por Convención como otros tipos de relaciones.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-237">They will be discovered by convention like other types of relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=ManyToManyShared)]

<span data-ttu-id="dd1f7-238">La forma en que se implementa esta relación en la base de datos es mediante una tabla de combinación que contiene claves externas a `Post` y `Tag` .</span><span class="sxs-lookup"><span data-stu-id="dd1f7-238">The way this relationship is implemented in the database is by a join table that contains foreign keys to both `Post` and `Tag`.</span></span> <span data-ttu-id="dd1f7-239">Por ejemplo, esto es lo que EF creará en una base de datos relacional para el modelo anterior.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-239">For example this is what EF will create in a relational database for the above model.</span></span>

```sql
CREATE TABLE [Posts] (
    [PostId] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([PostId])
);

CREATE TABLE [Tags] (
    [TagId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_Tags] PRIMARY KEY ([TagId])
);

CREATE TABLE [PostTag] (
    [PostId] int NOT NULL,
    [TagId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostId], [TagId]),
    CONSTRAINT [FK_PostTag_Posts_PostId] FOREIGN KEY ([PostId]) REFERENCES [Posts] ([PostId]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tags_TagId] FOREIGN KEY ([TagId]) REFERENCES [Tags] ([TagId]) ON DELETE CASCADE
);
```

<span data-ttu-id="dd1f7-240">Internamente, EF crea un tipo de entidad para representar la tabla de combinación a la que se hará referencia como el tipo de entidad de combinación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-240">Internally, EF creates an entity type to represent the join table that will be referred to as the join entity type.</span></span> <span data-ttu-id="dd1f7-241">No hay ningún tipo de CLR específico que se pueda usar para esto, por lo que `Dictionary<string, object>` se utiliza.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-241">There is no specific CLR type that can be used for this, so `Dictionary<string, object>` is used.</span></span> <span data-ttu-id="dd1f7-242">Puede haber más de una relación de varios a varios en el modelo, por lo que el tipo de entidad de combinación debe recibir un nombre único, en este caso `PostTag` .</span><span class="sxs-lookup"><span data-stu-id="dd1f7-242">More than one many-to-many relationships can exist in the model, therefore the join entity type must be given a unique name, in this case `PostTag`.</span></span> <span data-ttu-id="dd1f7-243">La característica que lo permite se denomina tipo de entidad de tipo compartido.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-243">The feature that allows this is called shared-type entity type.</span></span>

<span data-ttu-id="dd1f7-244">Las navegaciones de varios a varios se llaman omitir navegaciones, ya que omiten el tipo de entidad de combinación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-244">The many to many navigations are called skip navigations as they effectively skip over the join entity type.</span></span> <span data-ttu-id="dd1f7-245">Si está empleando la configuración masiva, todas las navegaciones de omitir se pueden obtener de `GetSkipNavigations` .</span><span class="sxs-lookup"><span data-stu-id="dd1f7-245">If you are employing bulk configuration all skip navigations can be obtained from `GetSkipNavigations`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Metadata)]

<span data-ttu-id="dd1f7-246">Es habitual aplicar la configuración al tipo de entidad de combinación.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-246">It is common to apply configuration to the join entity type.</span></span> <span data-ttu-id="dd1f7-247">Esta acción se puede realizar a través de `UsingEntity` .</span><span class="sxs-lookup"><span data-stu-id="dd1f7-247">This action can be accomplished via `UsingEntity`.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=SharedConfiguration)]

<span data-ttu-id="dd1f7-248">Se pueden proporcionar [datos de inicialización del modelo](xref:core/modeling/data-seeding) para el tipo de entidad de combinación mediante el uso de tipos anónimos.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-248">[Model seed data](xref:core/modeling/data-seeding) can be provided for the join entity type by using anonymous types.</span></span> <span data-ttu-id="dd1f7-249">Puede examinar la vista de depuración del modelo para determinar los nombres de propiedad creados por la Convención.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-249">You can examine the model debug view to determine the property names created by convention.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Seeding)]

<span data-ttu-id="dd1f7-250">Los datos adicionales se pueden almacenar en el tipo de entidad de combinación, pero para ello es mejor crear un tipo de CLR de tipo.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-250">Additional data can be stored in the join entity type, but for this it's best to create a bespoke CLR type.</span></span> <span data-ttu-id="dd1f7-251">Al configurar la relación con un tipo de entidad de combinación personalizada, se deben especificar explícitamente las claves externas.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-251">When configuring the relationship with a custom join entity type both foreign keys need to be specified explicitly.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyPayload.cs?name=ManyToManyPayload)]

> [!NOTE]
> <span data-ttu-id="dd1f7-252">La capacidad de configurar relaciones varios a varios se agregó en EF Core 5,0, para la versión anterior, use el siguiente enfoque.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-252">The ability to configure many-to-many relationships was added in EF Core 5.0, for previous version use the following approach.</span></span>

<span data-ttu-id="dd1f7-253">También puede representar una relación de varios a varios agregando el tipo de entidad de combinación y asignando dos relaciones uno a varios independientes.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-253">You can also represent a many-to-many relationship by just adding the join entity type and mapping two separate one-to-many relationships.</span></span>

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]

> [!NOTE]
> <span data-ttu-id="dd1f7-254">Todavía no se ha agregado compatibilidad con la scaffolding de relaciones de varios a varios desde la base de datos.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-254">Support for scaffolding many-to-many relationships from the database is not yet added.</span></span> <span data-ttu-id="dd1f7-255">Consulte [problema de seguimiento](https://github.com/dotnet/efcore/issues/22475).</span><span class="sxs-lookup"><span data-stu-id="dd1f7-255">See [tracking issue](https://github.com/dotnet/efcore/issues/22475).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dd1f7-256">Recursos adicionales</span><span class="sxs-lookup"><span data-stu-id="dd1f7-256">Additional resources</span></span>

* <span data-ttu-id="dd1f7-257">[EF Core sesión reunión](https://www.youtube.com/watch?v=W1sxepfIMRM&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=32)de la comunidad, con un análisis profundo de varios a varios y de la infraestructura subyacente.</span><span class="sxs-lookup"><span data-stu-id="dd1f7-257">[EF Core Community Standup session](https://www.youtube.com/watch?v=W1sxepfIMRM&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=32), with a deep dive into Many-to-many and the infrastructure underpinning it.</span></span>
