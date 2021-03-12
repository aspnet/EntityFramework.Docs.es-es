---
title: 'Características de Change Tracking adicionales: EF Core'
description: Varias características y escenarios que implican EF Core el seguimiento de cambios
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/miscellaneous
ms.openlocfilehash: 63d96227b6862e920d900a5cc3f1f85d7c6d85ac
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024438"
---
# <a name="additional-change-tracking-features"></a><span data-ttu-id="b060d-103">Características de Change Tracking adicionales</span><span class="sxs-lookup"><span data-stu-id="b060d-103">Additional Change Tracking Features</span></span>

<span data-ttu-id="b060d-104">En este documento se tratan varias características y escenarios que implican el seguimiento de cambios.</span><span class="sxs-lookup"><span data-stu-id="b060d-104">This document covers miscellaneous features and scenarios involving change tracking.</span></span>

> [!TIP]
> <span data-ttu-id="b060d-105">En este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de EF Core.</span><span class="sxs-lookup"><span data-stu-id="b060d-105">This document assumes that entity states and the basics of EF Core change tracking are understood.</span></span> <span data-ttu-id="b060d-106">Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener más información sobre estos temas.</span><span class="sxs-lookup"><span data-stu-id="b060d-106">See [Change Tracking in EF Core](xref:core/change-tracking/index) for more information on these topics.</span></span>

> [!TIP]
> <span data-ttu-id="b060d-107">Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).</span><span class="sxs-lookup"><span data-stu-id="b060d-107">You can run and debug into all the code in this document by [downloading the sample code from GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/AdditionalChangeTrackingFeatures).</span></span>

## <a name="add-versus-addasync"></a><span data-ttu-id="b060d-108">Diferencias entre `Add` y `AddAsync`</span><span class="sxs-lookup"><span data-stu-id="b060d-108">`Add` versus `AddAsync`</span></span>

<span data-ttu-id="b060d-109">Entity Framework Core (EF Core) proporciona métodos asincrónicos cada vez que se usa ese método, se puede producir una interacción con la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b060d-109">Entity Framework Core (EF Core) provides async methods whenever using that method may result in a database interaction.</span></span> <span data-ttu-id="b060d-110">También se proporcionan métodos sincrónicos para evitar la sobrecarga cuando se usan bases de datos que no admiten el acceso asincrónico de alto rendimiento.</span><span class="sxs-lookup"><span data-stu-id="b060d-110">Synchronous methods are also provided to avoid overhead when using databases that do not support high performance asynchronous access.</span></span>

<span data-ttu-id="b060d-111"><xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> normalmente no tienen acceso a la base de datos, ya que estos métodos solo inician el seguimiento de las entidades.</span><span class="sxs-lookup"><span data-stu-id="b060d-111"><xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> and <xref:Microsoft.EntityFrameworkCore.DbSet%601.Add%2A?displayProperty=nameWithType> do not normally access the database, since these methods inherently just start tracking entities.</span></span> <span data-ttu-id="b060d-112">Sin embargo, algunas formas de generación de valores _pueden_ tener acceso a la base de datos para generar un valor de clave.</span><span class="sxs-lookup"><span data-stu-id="b060d-112">However, some forms of value generation _may_ access the database in order to generate a key value.</span></span> <span data-ttu-id="b060d-113">El único generador de valores que realiza esta y se distribuye con EF Core es <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601> .</span><span class="sxs-lookup"><span data-stu-id="b060d-113">The only value generator that does this and ships with EF Core is <xref:Microsoft.EntityFrameworkCore.ValueGeneration.HiLoValueGenerator%601>.</span></span> <span data-ttu-id="b060d-114">No es habitual utilizar este generador. nunca se configura de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b060d-114">Using this generator is uncommon; it is never configured by default.</span></span> <span data-ttu-id="b060d-115">Esto significa que la mayoría de las aplicaciones deben usar `Add` , y no `AddAsync` .</span><span class="sxs-lookup"><span data-stu-id="b060d-115">This means that the vast majority of applications should use `Add`, and not `AddAsync`.</span></span>

<span data-ttu-id="b060d-116">Otros métodos similares como `Update` , `Attach` y `Remove` no tienen sobrecargas asincrónicas porque nunca generan nuevos valores de clave y, por lo tanto, no necesitan tener acceso a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b060d-116">Other similar methods like `Update`, `Attach`, and `Remove` do not have async overloads because they never generate new key values, and hence never need to access the database.</span></span>

## <a name="addrange-updaterange-attachrange-and-removerange"></a><span data-ttu-id="b060d-117">`AddRange`, `UpdateRange`, `AttachRange` y `RemoveRange`.</span><span class="sxs-lookup"><span data-stu-id="b060d-117">`AddRange`, `UpdateRange`, `AttachRange`, and `RemoveRange`</span></span>

<span data-ttu-id="b060d-118"><xref:Microsoft.EntityFrameworkCore.DbSet%601> y <xref:Microsoft.EntityFrameworkCore.DbContext> proporcionan versiones alternativas de `Add` , `Update` , `Attach` y `Remove` que aceptan varias instancias en una sola llamada.</span><span class="sxs-lookup"><span data-stu-id="b060d-118"><xref:Microsoft.EntityFrameworkCore.DbSet%601> and <xref:Microsoft.EntityFrameworkCore.DbContext> provide alternate versions of `Add`, `Update`, `Attach`, and `Remove` that accept multiple instances in a single call.</span></span> <span data-ttu-id="b060d-119">Estos métodos son <xref:Microsoft.EntityFrameworkCore.DbSet%601.AddRange%2A> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.UpdateRange%2A> , <xref:Microsoft.EntityFrameworkCore.DbSet%601.AttachRange%2A> y, <xref:Microsoft.EntityFrameworkCore.DbSet%601.RemoveRange%2A> respectivamente.</span><span class="sxs-lookup"><span data-stu-id="b060d-119">These methods are <xref:Microsoft.EntityFrameworkCore.DbSet%601.AddRange%2A>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.UpdateRange%2A>, <xref:Microsoft.EntityFrameworkCore.DbSet%601.AttachRange%2A>, and <xref:Microsoft.EntityFrameworkCore.DbSet%601.RemoveRange%2A> respectively.</span></span>

<span data-ttu-id="b060d-120">Estos métodos se proporcionan por comodidad.</span><span class="sxs-lookup"><span data-stu-id="b060d-120">These methods are provided as a convenience.</span></span> <span data-ttu-id="b060d-121">El uso de un método "Range" tiene la misma funcionalidad que varias llamadas al método equivalente que no es de intervalo.</span><span class="sxs-lookup"><span data-stu-id="b060d-121">Using a "range" method has the same functionality as multiple calls to the equivalent non-range method.</span></span> <span data-ttu-id="b060d-122">No hay ninguna diferencia de rendimiento significativa entre los dos enfoques.</span><span class="sxs-lookup"><span data-stu-id="b060d-122">There is no significant performance difference between the two approaches.</span></span>

> [!NOTE]
> <span data-ttu-id="b060d-123">Esto es diferente de EF6, donde se `AddRange` llama a y a `Add` ambos automáticamente `DetectChanges` , pero la llamada `Add` varias veces hizo que DetectChanges se llamara varias veces en lugar de una vez.</span><span class="sxs-lookup"><span data-stu-id="b060d-123">This is different from EF6, where `AddRange` and `Add` both automatically called `DetectChanges`, but calling `Add` multiple times caused DetectChanges to be called multiple times instead of once.</span></span> <span data-ttu-id="b060d-124">Esto hace que sea `AddRange` más eficaz en EF6.</span><span class="sxs-lookup"><span data-stu-id="b060d-124">This made `AddRange` more efficient in EF6.</span></span> <span data-ttu-id="b060d-125">En EF Core, ninguno de estos métodos llama automáticamente a `DetectChanges` .</span><span class="sxs-lookup"><span data-stu-id="b060d-125">In EF Core, neither of these methods automatically call `DetectChanges`.</span></span>

## <a name="dbcontext-versus-dbset-methods"></a><span data-ttu-id="b060d-126">DbContext frente a métodos DbSet</span><span class="sxs-lookup"><span data-stu-id="b060d-126">DbContext versus DbSet methods</span></span>

<span data-ttu-id="b060d-127">Muchos métodos, incluidos `Add` , `Update` , `Attach` y `Remove` , tienen implementaciones en <xref:Microsoft.EntityFrameworkCore.DbSet%601> y <xref:Microsoft.EntityFrameworkCore.DbContext> .</span><span class="sxs-lookup"><span data-stu-id="b060d-127">Many methods, including `Add`, `Update`, `Attach`, and `Remove`, have implementations on both <xref:Microsoft.EntityFrameworkCore.DbSet%601> and <xref:Microsoft.EntityFrameworkCore.DbContext>.</span></span> <span data-ttu-id="b060d-128">Estos métodos tienen _exactamente el mismo comportamiento para los_ tipos de entidad normales.</span><span class="sxs-lookup"><span data-stu-id="b060d-128">These methods have _exactly the same behavior_ for normal entity types.</span></span> <span data-ttu-id="b060d-129">Esto se debe a que el tipo CLR de la entidad se asigna a un solo tipo de entidad del modelo de EF Core.</span><span class="sxs-lookup"><span data-stu-id="b060d-129">This is because the CLR type of the entity is mapped onto one and only one entity type in the EF Core model.</span></span> <span data-ttu-id="b060d-130">Por lo tanto, el tipo CLR define totalmente Dónde encaja la entidad en el modelo, por lo que el DbSet que se va a usar se puede determinar de forma implícita.</span><span class="sxs-lookup"><span data-stu-id="b060d-130">Therefore, the CLR type fully defines where the entity fits in the model, and so the DbSet to use can be determined implicitly.</span></span>

<span data-ttu-id="b060d-131">La excepción a esta regla es cuando se usan tipos de entidad de tipo compartido, que se introdujeron en EF Core 5,0, principalmente para entidades de combinación de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="b060d-131">The exception to this rule is when using shared-type entity types, which were introduced in EF Core 5.0, primarily for many-to-many join entities.</span></span> <span data-ttu-id="b060d-132">Cuando se usa un tipo de entidad de tipo compartido, primero se debe crear un DbSet para el tipo de modelo de EF Core que se está usando.</span><span class="sxs-lookup"><span data-stu-id="b060d-132">When using a shared-type entity type, a DbSet must first be created for the EF Core model type that is being used.</span></span> <span data-ttu-id="b060d-133">Los métodos como `Add` , `Update` , `Attach` y `Remove` se pueden usar en DbSet sin ninguna ambigüedad en cuanto a qué tipo de modelo de EF Core se está usando.</span><span class="sxs-lookup"><span data-stu-id="b060d-133">Methods like `Add`, `Update`, `Attach`, and `Remove` can then be used on the DbSet without any ambiguity as to which EF Core model type is being used.</span></span>

<span data-ttu-id="b060d-134">Los tipos de entidad de tipo compartido se usan de forma predeterminada para las entidades de combinación en las relaciones de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="b060d-134">Shared-type entity types are used by default for the join entities in many-to-many relationships.</span></span> <span data-ttu-id="b060d-135">Un tipo de entidad de tipo compartido también puede configurarse explícitamente para su uso en una relación de varios a varios.</span><span class="sxs-lookup"><span data-stu-id="b060d-135">A shared-type entity type can also be explicitly configured for use in a many-to-many relationship.</span></span> <span data-ttu-id="b060d-136">Por ejemplo, el código siguiente configura `Dictionary<string, int>` como un tipo de entidad de combinación:</span><span class="sxs-lookup"><span data-stu-id="b060d-136">For example, the code below configures `Dictionary<string, int>` as a join entity type:</span></span>

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder
                .SharedTypeEntity<Dictionary<string, int>>(
                    "PostTag",
                    b =>
                        {
                            b.IndexerProperty<int>("TagId");
                            b.IndexerProperty<int>("PostId");
                        });

            modelBuilder.Entity<Post>()
                .HasMany(p => p.Tags)
                .WithMany(p => p.Posts)
                .UsingEntity<Dictionary<string, int>>(
                    "PostTag",
                    j => j.HasOne<Tag>().WithMany(),
                    j => j.HasOne<Post>().WithMany());
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=OnModelCreating)]

<span data-ttu-id="b060d-137">Al [cambiar las claves externas y las navegaciones](xref:core/change-tracking/relationship-changes) , se muestra cómo asociar dos entidades mediante el seguimiento de una nueva instancia de entidad join.</span><span class="sxs-lookup"><span data-stu-id="b060d-137">[Changing Foreign Keys and Navigations](xref:core/change-tracking/relationship-changes) shows how to associate two entities by tracking a new join entity instance.</span></span> <span data-ttu-id="b060d-138">El código siguiente hace esto para el `Dictionary<string, int>` tipo de entidad de tipo compartido que se usa para la entidad de combinación:</span><span class="sxs-lookup"><span data-stu-id="b060d-138">The code below does this for the `Dictionary<string, int>` shared-type entity type used for the join entity:</span></span>

<!--
            using var context = new BlogsContext();

            var post = context.Posts.Single(e => e.Id == 3);
            var tag = context.Tags.Single(e => e.Id == 1);

            var joinEntitySet = context.Set<Dictionary<string, int>>("PostTag");
            var joinEntity = new Dictionary<string, int>
            {
                ["PostId"] = post.Id,
                ["TagId"] = tag.Id
            };
            joinEntitySet.Add(joinEntity);

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();
-->
[!code-csharp[DbContext_versus_DbSet_methods_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=DbContext_versus_DbSet_methods_1)]

<span data-ttu-id="b060d-139">Observe que <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> se usa para crear un DbSet para el `PostTag` tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="b060d-139">Notice that <xref:Microsoft.EntityFrameworkCore.DbContext.Set%60%601(System.String)?displayProperty=nameWithType> is used to create a DbSet for the `PostTag` entity type.</span></span> <span data-ttu-id="b060d-140">Este DbSet se puede usar para llamar a `Add` con la nueva instancia de la entidad join.</span><span class="sxs-lookup"><span data-stu-id="b060d-140">This DbSet can then be used to call `Add` with the new join entity instance.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b060d-141">El tipo CLR que se usa para los tipos de entidad de combinación por Convención puede cambiar en futuras versiones para mejorar el rendimiento.</span><span class="sxs-lookup"><span data-stu-id="b060d-141">The CLR type used for join entity types by convention may change in future releases to improve performance.</span></span> <span data-ttu-id="b060d-142">No dependa de ningún tipo de entidad de combinación concreta, a menos que se haya configurado explícitamente como se hace `Dictionary<string, int>` en el código anterior.</span><span class="sxs-lookup"><span data-stu-id="b060d-142">Do not depend on any specific join entity type unless it has been explicitly configured as is done for `Dictionary<string, int>` in the code above.</span></span>

## <a name="property-versus-field-access"></a><span data-ttu-id="b060d-143">Propiedad frente al acceso a campos</span><span class="sxs-lookup"><span data-stu-id="b060d-143">Property versus field access</span></span>

<span data-ttu-id="b060d-144">A partir de EF Core 3,0, el acceso a las propiedades de la entidad usa el campo de respaldo de la propiedad de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="b060d-144">Starting with EF Core 3.0, access to entity properties uses the backing field of the property by default.</span></span> <span data-ttu-id="b060d-145">Esto es eficaz y evita que se desencadenen efectos secundarios al llamar a captadores y establecedores de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b060d-145">This is efficient and avoids triggering side effects from calling property getters and setters.</span></span> <span data-ttu-id="b060d-146">Por ejemplo, este es el modo en que la carga diferida puede evitar desencadenar bucles infinitos.</span><span class="sxs-lookup"><span data-stu-id="b060d-146">For example, this is how lazy-loading is able to avoid triggering infinite loops.</span></span> <span data-ttu-id="b060d-147">Vea [campos de respaldo](xref:core/modeling/backing-field) para obtener más información sobre cómo configurar los campos de respaldo en el modelo.</span><span class="sxs-lookup"><span data-stu-id="b060d-147">See [Backing Fields](xref:core/modeling/backing-field) for more information on configuring backing fields in the model.</span></span>

<span data-ttu-id="b060d-148">A veces puede ser deseable que EF Core genere efectos secundarios al modificar los valores de propiedad.</span><span class="sxs-lookup"><span data-stu-id="b060d-148">Sometimes it may be desirable for EF Core to generate side-effects when it modifies property values.</span></span> <span data-ttu-id="b060d-149">Por ejemplo, cuando se enlazan datos a entidades, el establecimiento de una propiedad puede generar notificaciones al U.I.</span><span class="sxs-lookup"><span data-stu-id="b060d-149">For example, when data binding to entities, setting a property may generate notifications to the U.I.</span></span> <span data-ttu-id="b060d-150">que no se producen al establecer el campo directamente.</span><span class="sxs-lookup"><span data-stu-id="b060d-150">which do not happen when setting the field directly.</span></span> <span data-ttu-id="b060d-151">Esto se puede lograr cambiando el <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> para:</span><span class="sxs-lookup"><span data-stu-id="b060d-151">This can be achieved by changing the <xref:Microsoft.EntityFrameworkCore.PropertyAccessMode> for:</span></span>

- <span data-ttu-id="b060d-152">Todos los tipos de entidad del modelo mediante <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="b060d-152">All entity types in the model using <xref:Microsoft.EntityFrameworkCore.ModelBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="b060d-153">Todas las propiedades y navegaciones de un tipo de entidad específico mediante <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="b060d-153">All properties and navigations of a specific entity type using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder%601.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="b060d-154">Una propiedad específica mediante <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="b060d-154">A specific property using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>
- <span data-ttu-id="b060d-155">Una navegación específica mediante <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span><span class="sxs-lookup"><span data-stu-id="b060d-155">A specific navigation using <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.NavigationBuilder.UsePropertyAccessMode%2A?displayProperty=nameWithType></span></span>

<span data-ttu-id="b060d-156">Los modos de acceso de propiedad y harán que `Field` `PreferField` EF Core tenga acceso al valor de propiedad a través de su campo de respaldo.</span><span class="sxs-lookup"><span data-stu-id="b060d-156">Property access modes `Field` and `PreferField` will cause EF Core to access the property value through its backing field.</span></span> <span data-ttu-id="b060d-157">Del mismo modo, y hará que `Property` `PreferProperty` EF Core tenga acceso al valor de propiedad a través de su captador y establecedor.</span><span class="sxs-lookup"><span data-stu-id="b060d-157">Likewise, `Property` and `PreferProperty` will cause EF Core to access the property value through its getter and setter.</span></span>

<span data-ttu-id="b060d-158">Si `Field` `Property` se usan o y EF Core no pueden tener acceso al valor mediante el campo o captador/establecedor de propiedad respectivamente, EF Core producirá una excepción.</span><span class="sxs-lookup"><span data-stu-id="b060d-158">If `Field` or `Property` are used and EF Core cannot access the value through the field or property getter/setter respectively, then EF Core will throw an exception.</span></span> <span data-ttu-id="b060d-159">Esto garantiza que EF Core siempre use el acceso de campo o propiedad cuando cree que es.</span><span class="sxs-lookup"><span data-stu-id="b060d-159">This ensures EF Core is always using field/property access when you think it is.</span></span>

<span data-ttu-id="b060d-160">Por otro lado, los `PreferField` modos y revertirán `PreferProperty` al uso de la propiedad o el campo de respaldo, respectivamente, si no es posible usar el acceso preferido.</span><span class="sxs-lookup"><span data-stu-id="b060d-160">On the other hand, the `PreferField` and `PreferProperty` modes will fall back to using the property or backing field respectively if it is not possible to use the preferred access.</span></span> <span data-ttu-id="b060d-161">`PreferField` es el valor predeterminado de EF Core 3,0 en adelante.</span><span class="sxs-lookup"><span data-stu-id="b060d-161">`PreferField` is the default from EF Core 3.0 onwards.</span></span> <span data-ttu-id="b060d-162">Esto significa que EF Core usará los campos siempre que pueda, pero no producirá un error si se debe tener acceso a una propiedad a través de su captador o establecedor en su lugar.</span><span class="sxs-lookup"><span data-stu-id="b060d-162">This means EF Core will use fields whenever it can, but will not fail if a property must be accessed through its getter or setter instead.</span></span>

<span data-ttu-id="b060d-163">`FieldDuringConstruction` y `PreferFieldDuringConstruction` configure EF Core para usar los campos de respaldo _solo al crear instancias de entidad_.</span><span class="sxs-lookup"><span data-stu-id="b060d-163">`FieldDuringConstruction` and `PreferFieldDuringConstruction` configure EF Core to use of backing fields _only when creating entity instances_.</span></span> <span data-ttu-id="b060d-164">Esto permite que las consultas se ejecuten sin efectos secundarios de captador y establecedor, mientras que los cambios de propiedad posteriores de EF Core provocarán estos efectos secundarios.</span><span class="sxs-lookup"><span data-stu-id="b060d-164">This allows queries to be executed without getter and setter side effects, while later property changes by EF Core will cause these side effects.</span></span> <span data-ttu-id="b060d-165">`PreferFieldDuringConstruction` era el valor predeterminado antes de EF Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="b060d-165">`PreferFieldDuringConstruction` was the default prior to EF Core 3.0.</span></span>

<span data-ttu-id="b060d-166">Los diferentes modos de acceso a propiedades se resumen en la tabla siguiente:</span><span class="sxs-lookup"><span data-stu-id="b060d-166">The different property access modes are summarized in the following table:</span></span>

| <span data-ttu-id="b060d-167">PropertyAccessMode</span><span class="sxs-lookup"><span data-stu-id="b060d-167">PropertyAccessMode</span></span>              | <span data-ttu-id="b060d-168">Referencia</span><span class="sxs-lookup"><span data-stu-id="b060d-168">Preference</span></span> | <span data-ttu-id="b060d-169">Preferencia de creación de entidades</span><span class="sxs-lookup"><span data-stu-id="b060d-169">Preference creating entities</span></span> | <span data-ttu-id="b060d-170">Reserva</span><span class="sxs-lookup"><span data-stu-id="b060d-170">Fallback</span></span> | <span data-ttu-id="b060d-171">Creación de entidades de reserva</span><span class="sxs-lookup"><span data-stu-id="b060d-171">Fallback creating entities</span></span>
|:--------------------------------|------------|------------------------------|----------|---------------------------
| `Field`                         | <span data-ttu-id="b060d-172">Campo</span><span class="sxs-lookup"><span data-stu-id="b060d-172">Field</span></span>      | <span data-ttu-id="b060d-173">Campo</span><span class="sxs-lookup"><span data-stu-id="b060d-173">Field</span></span>                        | <span data-ttu-id="b060d-174">Produce</span><span class="sxs-lookup"><span data-stu-id="b060d-174">Throws</span></span>   | <span data-ttu-id="b060d-175">Produce</span><span class="sxs-lookup"><span data-stu-id="b060d-175">Throws</span></span>
| `Property`                      | <span data-ttu-id="b060d-176">Propiedad</span><span class="sxs-lookup"><span data-stu-id="b060d-176">Property</span></span>   | <span data-ttu-id="b060d-177">Propiedad</span><span class="sxs-lookup"><span data-stu-id="b060d-177">Property</span></span>                     | <span data-ttu-id="b060d-178">Produce</span><span class="sxs-lookup"><span data-stu-id="b060d-178">Throws</span></span>   | <span data-ttu-id="b060d-179">Produce</span><span class="sxs-lookup"><span data-stu-id="b060d-179">Throws</span></span>
| `PreferField`                   | <span data-ttu-id="b060d-180">Campo</span><span class="sxs-lookup"><span data-stu-id="b060d-180">Field</span></span>      | <span data-ttu-id="b060d-181">Campo</span><span class="sxs-lookup"><span data-stu-id="b060d-181">Field</span></span>                        | <span data-ttu-id="b060d-182">Propiedad</span><span class="sxs-lookup"><span data-stu-id="b060d-182">Property</span></span> | <span data-ttu-id="b060d-183">Propiedad</span><span class="sxs-lookup"><span data-stu-id="b060d-183">Property</span></span>
| `PreferProperty`                | <span data-ttu-id="b060d-184">Propiedad</span><span class="sxs-lookup"><span data-stu-id="b060d-184">Property</span></span>   | <span data-ttu-id="b060d-185">Propiedad</span><span class="sxs-lookup"><span data-stu-id="b060d-185">Property</span></span>                     | <span data-ttu-id="b060d-186">Campo</span><span class="sxs-lookup"><span data-stu-id="b060d-186">Field</span></span>    | <span data-ttu-id="b060d-187">Campo</span><span class="sxs-lookup"><span data-stu-id="b060d-187">Field</span></span>
| `FieldDuringConstruction`       | <span data-ttu-id="b060d-188">Propiedad</span><span class="sxs-lookup"><span data-stu-id="b060d-188">Property</span></span>   | <span data-ttu-id="b060d-189">Campo</span><span class="sxs-lookup"><span data-stu-id="b060d-189">Field</span></span>                        | <span data-ttu-id="b060d-190">Campo</span><span class="sxs-lookup"><span data-stu-id="b060d-190">Field</span></span>    | <span data-ttu-id="b060d-191">Produce</span><span class="sxs-lookup"><span data-stu-id="b060d-191">Throws</span></span>
| `PreferFieldDuringConstruction` | <span data-ttu-id="b060d-192">Propiedad</span><span class="sxs-lookup"><span data-stu-id="b060d-192">Property</span></span>   | <span data-ttu-id="b060d-193">Campo</span><span class="sxs-lookup"><span data-stu-id="b060d-193">Field</span></span>                        | <span data-ttu-id="b060d-194">Campo</span><span class="sxs-lookup"><span data-stu-id="b060d-194">Field</span></span>    | <span data-ttu-id="b060d-195">Propiedad</span><span class="sxs-lookup"><span data-stu-id="b060d-195">Property</span></span>

## <a name="temporary-values"></a><span data-ttu-id="b060d-196">Valores temporales</span><span class="sxs-lookup"><span data-stu-id="b060d-196">Temporary values</span></span>

<span data-ttu-id="b060d-197">EF Core crea valores de clave temporales al realizar el seguimiento de nuevas entidades que tendrán valores de clave reales generados por la base de datos cuando se llame a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="b060d-197">EF Core creates temporary key values when tracking new entities that will have real key values generated by the database when SaveChanges is called.</span></span> <span data-ttu-id="b060d-198">Consulte [Change Tracking en EF Core](xref:core/change-tracking/index) para obtener información general sobre cómo se usan estos valores temporales.</span><span class="sxs-lookup"><span data-stu-id="b060d-198">See [Change Tracking in EF Core](xref:core/change-tracking/index) for an overview of how these temporary values are used.</span></span>

### <a name="accessing-temporary-values"></a><span data-ttu-id="b060d-199">Obtener acceso a valores temporales</span><span class="sxs-lookup"><span data-stu-id="b060d-199">Accessing temporary values</span></span>

<span data-ttu-id="b060d-200">A partir de EF Core 3,0, los valores temporales se almacenan en el seguimiento de cambios y no se establecen directamente en instancias de entidad.</span><span class="sxs-lookup"><span data-stu-id="b060d-200">Starting with EF Core 3.0, temporary values are stored in the change tracker and not set onto entity instances directly.</span></span> <span data-ttu-id="b060d-201">Sin embargo, estos valores temporales _se_ exponen al usar los distintos mecanismos para [tener acceso a las entidades](xref:core/change-tracking/entity-entries)de las que se ha realizado un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="b060d-201">However, these temporary values _are_ exposed when using the various mechanisms for [Accessing Tracked Entities](xref:core/change-tracking/entity-entries).</span></span> <span data-ttu-id="b060d-202">Por ejemplo, el código siguiente obtiene acceso a un valor temporal mediante <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="b060d-202">For example, the following code accesses a temporary value using <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.CurrentValues%2A?displayProperty=nameWithType>:</span></span>

<!--
        using var context = new BlogsContext();

        var blog = new Blog { Name = ".NET Blog" };

        context.Add(blog);

        Console.WriteLine($"Blog.Id set on entity is {blog.Id}");
        Console.WriteLine($"Blog.Id tracked by EF is {context.Entry(blog).Property(e => e.Id).CurrentValue}");
-->
[!code-csharp[Temporary_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_1)]

<span data-ttu-id="b060d-203">El resultado de este código es:</span><span class="sxs-lookup"><span data-stu-id="b060d-203">The output from this code is:</span></span>

```output
Blog.Id set on entity is 0
Blog.Id tracked by EF is -2147482643
```

<span data-ttu-id="b060d-204"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> se puede usar para comprobar los valores temporales.</span><span class="sxs-lookup"><span data-stu-id="b060d-204"><xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A?displayProperty=nameWithType> can be used to check for temporary values.</span></span>

### <a name="manipulating-temporary-values"></a><span data-ttu-id="b060d-205">Manipular valores temporales</span><span class="sxs-lookup"><span data-stu-id="b060d-205">Manipulating temporary values</span></span>

<span data-ttu-id="b060d-206">A veces resulta útil trabajar explícitamente con valores temporales.</span><span class="sxs-lookup"><span data-stu-id="b060d-206">It is sometimes useful to explicitly work with temporary values.</span></span> <span data-ttu-id="b060d-207">Por ejemplo, se puede crear una colección de nuevas entidades en un cliente web y, a continuación, volver a serializarse en el servidor.</span><span class="sxs-lookup"><span data-stu-id="b060d-207">For example, a collection of new entities might be created on a web client and then serialized back to the server.</span></span> <span data-ttu-id="b060d-208">Los valores de clave externa son una manera de configurar relaciones entre estas entidades.</span><span class="sxs-lookup"><span data-stu-id="b060d-208">Foreign key values are one way to set up relationships between these entities.</span></span> <span data-ttu-id="b060d-209">En el código siguiente se usa este enfoque para asociar un gráfico de nuevas entidades por clave externa, a la vez que se permite que se generen valores de clave reales cuando se llama a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="b060d-209">The following code uses this approach to associate a graph of new entities by foreign key, while still allowing real key values to be generated when SaveChanges is called.</span></span>

<!--
            var blogs = new List<Blog>
            {
                new Blog { Id = -1, Name = ".NET Blog" },
                new Blog { Id = -2, Name = "Visual Studio Blog" }
            };

            var posts = new List<Post>()
            {
                new Post
                {
                    Id = -1,
                    BlogId = -1,
                    Title = "Announcing the Release of EF Core 5.0",
                    Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                },
                new Post
                {
                    Id = -2,
                    BlogId = -2,
                    Title = "Disassembly improvements for optimized managed debugging",
                    Content = "If you are focused on squeezing out the last bits of performance for your .NET service or..."
                }
            };

            using var context = new BlogsContext();

            foreach (var blog in blogs)
            {
                context.Add(blog).Property(e => e.Id).IsTemporary = true;
            }

            foreach (var post in posts)
            {
                context.Add(post).Property(e => e.Id).IsTemporary = true;
            }

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Temporary_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/Samples.cs?name=Temporary_values_2)]

<span data-ttu-id="b060d-210">Tenga en lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="b060d-210">Notice that:</span></span>

- <span data-ttu-id="b060d-211">Los números negativos se utilizan como valores de clave temporales; Esto no es necesario, pero es una Convención común para evitar conflictos de claves.</span><span class="sxs-lookup"><span data-stu-id="b060d-211">Negative numbers are used as temporary key values; this is not required, but is a common convention to prevent key clashes.</span></span>
- <span data-ttu-id="b060d-212">La `Post.BlogId` propiedad FK tiene asignado el mismo valor negativo que la clave principal del blog asociado.</span><span class="sxs-lookup"><span data-stu-id="b060d-212">The `Post.BlogId` FK property is assigned the same negative value as the PK of the associated blog.</span></span>
- <span data-ttu-id="b060d-213">Los valores PK se marcan como temporales estableciendo <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> una vez que se realiza el seguimiento de cada entidad.</span><span class="sxs-lookup"><span data-stu-id="b060d-213">The PK values are marked as temporary by setting <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary%2A> after each entity is tracked.</span></span> <span data-ttu-id="b060d-214">Esto es necesario porque se supone que cualquier valor de clave proporcionado por la aplicación es un valor de clave real.</span><span class="sxs-lookup"><span data-stu-id="b060d-214">This is necessary because any key value supplied by the application is assumed to be a real key value.</span></span>

<span data-ttu-id="b060d-215">Al examinar la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) antes de llamar a SaveChanges, se muestra que los valores PK están marcados como temporales y que las publicaciones están asociadas a los blogs correctos, incluida la corrección de las navegaciones:</span><span class="sxs-lookup"><span data-stu-id="b060d-215">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) before calling SaveChanges shows that the PK values are marked as temporary and posts are associated with the correct blogs, including fixup of navigations:</span></span>

```output
Blog {Id: -2} Added
  Id: -2 PK Temporary
  Name: 'Visual Studio Blog'
  Posts: [{Id: -2}]
Blog {Id: -1} Added
  Id: -1 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -1}]
Post {Id: -2} Added
  Id: -2 PK Temporary
  BlogId: -2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: -2}
  Tags: []
Post {Id: -1} Added
  Id: -1 PK Temporary
  BlogId: -1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -1}
```

<span data-ttu-id="b060d-216">Después de llamar a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> , estos valores temporales se han reemplazado por valores reales generados por la base de datos:</span><span class="sxs-lookup"><span data-stu-id="b060d-216">After calling <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>, these temporary values have been replaced by real values generated by the database:</span></span>

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Blog {Id: 2} Unchanged
  Id: 2 PK
  Name: 'Visual Studio Blog'
  Posts: [{Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
  Tags: []
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 2 FK
  Content: 'If you are focused on squeezing out the last bits of perform...'
  Title: 'Disassembly improvements for optimized managed debugging'
  Blog: {Id: 2}
  Tags: []
```

## <a name="working-with-default-values"></a><span data-ttu-id="b060d-217">Trabajar con valores predeterminados</span><span class="sxs-lookup"><span data-stu-id="b060d-217">Working with default values</span></span>

<span data-ttu-id="b060d-218">EF Core permite que una propiedad obtenga su valor predeterminado de la base de datos cuando <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> se llama a.</span><span class="sxs-lookup"><span data-stu-id="b060d-218">EF Core allows a property to get its default value from the database when <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> is called.</span></span> <span data-ttu-id="b060d-219">Al igual que con los valores de clave generados, EF Core solo usará un valor predeterminado de la base de datos si no se ha establecido explícitamente ningún valor.</span><span class="sxs-lookup"><span data-stu-id="b060d-219">Like with generated key values, EF Core will only use a default from the database if no value has been explicitly set.</span></span> <span data-ttu-id="b060d-220">Por ejemplo, considere el siguiente tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="b060d-220">For example, consider the following entity type:</span></span>

<!--
    public class Token
    {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime ValidFrom { get; set; }
    }
-->
[!code-csharp[Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Token)]

<span data-ttu-id="b060d-221">La `ValidFrom` propiedad está configurada para obtener un valor predeterminado de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="b060d-221">The `ValidFrom` property is configured to get a default value from the database:</span></span>

<!--
        modelBuilder
            .Entity<Token>()
            .Property(e => e.ValidFrom)
            .HasDefaultValueSql("CURRENT_TIMESTAMP");
-->
[!code-csharp[OnModelCreating_Token](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Token)]

<span data-ttu-id="b060d-222">Al insertar una entidad de este tipo, EF Core permitirá a la base de datos generar el valor a menos que se haya establecido un valor explícito en su lugar.</span><span class="sxs-lookup"><span data-stu-id="b060d-222">When inserting an entity of this type, EF Core will let the database generate the value unless an explicit value has been set instead.</span></span> <span data-ttu-id="b060d-223">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b060d-223">For example:</span></span>

<!--
            using var context = new BlogsContext();

            context.AddRange(
                new Token { Name = "A" },
                new Token { Name = "B", ValidFrom = new DateTime(1111, 11, 11, 11, 11, 11)});

            context.SaveChanges();

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Working_with_default_values_1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_1)]

<span data-ttu-id="b060d-224">En la [vista de depuración del seguimiento de cambios](xref:core/change-tracking/debug-views) se muestra que el primer token fue `ValidFrom` generado por la base de datos, mientras que el segundo token usó el valor establecido explícitamente:</span><span class="sxs-lookup"><span data-stu-id="b060d-224">Looking at the [change tracker debug view](xref:core/change-tracking/debug-views) shows that the first token had `ValidFrom` generated by the database, while the second token used the value explicitly set:</span></span>

```output
Token {Id: 1} Unchanged
  Id: 1 PK
  Name: 'A'
  ValidFrom: '12/30/2020 6:36:06 PM'
Token {Id: 2} Unchanged
  Id: 2 PK
  Name: 'B'
  ValidFrom: '11/11/1111 11:11:11 AM'
```

> [!NOTE]
> <span data-ttu-id="b060d-225">El uso de valores predeterminados de base de datos requiere que la columna de base de datos tenga una restricción de valor predeterminado.</span><span class="sxs-lookup"><span data-stu-id="b060d-225">Using database default values requires that the database column has a default value constraint configured.</span></span> <span data-ttu-id="b060d-226">Esto lo hace automáticamente EF Core migraciones cuando se usa <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> o <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A> .</span><span class="sxs-lookup"><span data-stu-id="b060d-226">This is done automatically by EF Core migrations when using <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValueSql%2A> or <xref:Microsoft.EntityFrameworkCore.RelationalPropertyBuilderExtensions.HasDefaultValue%2A>.</span></span> <span data-ttu-id="b060d-227">Asegúrese de crear la restricción predeterminada en la columna de otra manera cuando no use migraciones de EF Core.</span><span class="sxs-lookup"><span data-stu-id="b060d-227">Make sure to create the default constraint on the column in some other way when not using EF Core migrations.</span></span>

### <a name="using-nullable-properties"></a><span data-ttu-id="b060d-228">Usar propiedades que aceptan valores NULL</span><span class="sxs-lookup"><span data-stu-id="b060d-228">Using nullable properties</span></span>

<span data-ttu-id="b060d-229">EF Core puede determinar si se ha establecido una propiedad comparando el valor de la propiedad con el valor predeterminado de CLR para ese tipo.</span><span class="sxs-lookup"><span data-stu-id="b060d-229">EF Core is able to determine whether or not a property has been set by comparing the property value to the CLR default for the that type.</span></span> <span data-ttu-id="b060d-230">Esto funciona bien en la mayoría de los casos, pero significa que el valor predeterminado de CLR no se puede insertar explícitamente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="b060d-230">This works well in most cases, but means that the CLR default cannot be explicitly inserted into the database.</span></span> <span data-ttu-id="b060d-231">Por ejemplo, considere una entidad con una propiedad de entero:</span><span class="sxs-lookup"><span data-stu-id="b060d-231">For example, consider an entity with an integer property:</span></span>

<!--
public class Foo1
{
    public int Id { get; set; }
    public int Count { get; set; }
}
-->
[!code-csharp[Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo1)]

<span data-ttu-id="b060d-232">Donde esa propiedad está configurada para tener una base de datos predeterminada de-1:</span><span class="sxs-lookup"><span data-stu-id="b060d-232">Where that property is configured to have a database default of -1:</span></span>

<!--
        modelBuilder
            .Entity<Foo1>()
            .Property(e => e.Count)
            .HasDefaultValue(-1);
-->
[!code-csharp[OnModelCreating_Foo1](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Foo1)]

<span data-ttu-id="b060d-233">La intención es que se use el valor predeterminado de-1 siempre que no se establezca un valor explícito.</span><span class="sxs-lookup"><span data-stu-id="b060d-233">The intention is that the default of -1 will be used whenever an explicit value is not set.</span></span> <span data-ttu-id="b060d-234">Sin embargo, no se puede distinguir si se establece el valor en 0 (el valor predeterminado de CLR para enteros) en EF Core no se establece ningún valor, lo que significa que no es posible insertar 0 para esta propiedad.</span><span class="sxs-lookup"><span data-stu-id="b060d-234">However, setting the value to 0 (the CLR default for integers) is indistinguishable to EF Core from not setting any value, this means that it is not possible to insert 0 for this property.</span></span> <span data-ttu-id="b060d-235">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b060d-235">For example:</span></span>

<!--
        using var context = new BlogsContext();

        var fooA = new Foo1 { Count = 10 };
        var fooB = new Foo1 { Count = 0 };
        var fooC = new Foo1 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == -1); // Not what we want!
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_2)]

<span data-ttu-id="b060d-236">Tenga en cuenta que la instancia en la que `Count` se estableció explícitamente en 0 todavía obtiene el valor predeterminado de la base de datos, que no es lo que se pretendía.</span><span class="sxs-lookup"><span data-stu-id="b060d-236">Notice that the instance where `Count` was explicitly set to 0 is still gets the default value from the database, which is not what we intended.</span></span> <span data-ttu-id="b060d-237">Una manera fácil de solucionarlo es hacer que la `Count` propiedad acepte valores NULL:</span><span class="sxs-lookup"><span data-stu-id="b060d-237">An easy way to deal with this is to make the `Count` property nullable:</span></span>

<!--
public class Foo2
{
    public int Id { get; set; }
    public int? Count { get; set; }
}
-->
[!code-csharp[Foo2](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo2)]

<span data-ttu-id="b060d-238">Esto hace que el valor predeterminado de CLR sea NULL, en lugar de 0, lo que significa que se insertará 0 cuando se establezca explícitamente:</span><span class="sxs-lookup"><span data-stu-id="b060d-238">This makes the CLR default null, instead of 0, which means 0 will now be inserted when explicitly set:</span></span>

<!--
        using var context = new BlogsContext();

        var fooA = new Foo2 { Count = 10 };
        var fooB = new Foo2 { Count = 0 };
        var fooC = new Foo2 { };

        context.AddRange(fooA, fooB, fooC);
        context.SaveChanges();

        Debug.Assert(fooA.Count == 10);
        Debug.Assert(fooB.Count == 0);
        Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_3)]

### <a name="using-nullable-backing-fields"></a><span data-ttu-id="b060d-239">Usar campos de respaldo que aceptan valores NULL</span><span class="sxs-lookup"><span data-stu-id="b060d-239">Using nullable backing fields</span></span>

> [!NOTE]
> <span data-ttu-id="b060d-240">Este patrón de campo de respaldo que acepta valores NULL es compatible con EF Core 5,0 y versiones posteriores.</span><span class="sxs-lookup"><span data-stu-id="b060d-240">This nullable backing field pattern is supported by EF Core 5.0 and later.</span></span>

<span data-ttu-id="b060d-241">El problema de hacer que la propiedad acepte valores NULL, por lo que puede que no sea conceptualmente aceptable en el modelo de dominio.</span><span class="sxs-lookup"><span data-stu-id="b060d-241">The problem with making the property nullable that it may not be conceptually nullable in the domain model.</span></span> <span data-ttu-id="b060d-242">Forzar que la propiedad acepte valores NULL, por tanto, pone en peligro el modelo.</span><span class="sxs-lookup"><span data-stu-id="b060d-242">Forcing the property to be nullable therefore compromises the model.</span></span>

<span data-ttu-id="b060d-243">A partir de EF Core 5,0, la propiedad puede dejarse que no admita valores NULL, y solo el campo de respaldo acepta valores NULL.</span><span class="sxs-lookup"><span data-stu-id="b060d-243">Starting with EF Core 5.0, the property can be left non-nullable, with only the backing field being nullable.</span></span> <span data-ttu-id="b060d-244">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b060d-244">For example:</span></span>

<!--
public class Foo3
{
    public int Id { get; set; }

    private int? _count;
    public int Count
    {
        get => _count ?? -1;
        set => _count = value;
    }
}
-->
[!code-csharp[Foo3](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Foo3)]

<span data-ttu-id="b060d-245">Esto permite que se inserte el valor predeterminado de CLR (0) si la propiedad se establece explícitamente en 0, aunque no es necesario exponer la propiedad como que acepta valores NULL en el modelo de dominio.</span><span class="sxs-lookup"><span data-stu-id="b060d-245">This allows the CLR default (0) to be inserted if the property is explicitly set to 0, while not needing to expose the property as nullable in the domain model.</span></span> <span data-ttu-id="b060d-246">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b060d-246">For example:</span></span>

<!--
            using var context = new BlogsContext();

            var fooA = new Foo3 { Count = 10 };
            var fooB = new Foo3 { Count = 0 };
            var fooC = new Foo3 { };

            context.AddRange(fooA, fooB, fooC);
            context.SaveChanges();

            Debug.Assert(fooA.Count == 10);
            Debug.Assert(fooB.Count == 0);
            Debug.Assert(fooC.Count == -1);
-->
[!code-csharp[Working_with_default_values_4](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_4)]

#### <a name="nullable-backing-fields-for-bool-properties"></a><span data-ttu-id="b060d-247">Campos de respaldo que aceptan valores NULL para las propiedades bool</span><span class="sxs-lookup"><span data-stu-id="b060d-247">Nullable backing fields for bool properties</span></span>

<span data-ttu-id="b060d-248">Este patrón es especialmente útil cuando se usan propiedades bool con valores predeterminados generados por el almacén.</span><span class="sxs-lookup"><span data-stu-id="b060d-248">This pattern is especially useful when using bool properties with store-generated defaults.</span></span> <span data-ttu-id="b060d-249">Dado que el valor predeterminado de CLR para `bool` es "false", significa que "false" no se puede insertar explícitamente mediante el patrón normal.</span><span class="sxs-lookup"><span data-stu-id="b060d-249">Since the CLR default for `bool` is "false", it means that "false" cannot be inserted explicitly using the normal pattern.</span></span> <span data-ttu-id="b060d-250">Por ejemplo, considere un `User` tipo de entidad:</span><span class="sxs-lookup"><span data-stu-id="b060d-250">For example, consider a `User` entity type:</span></span>

<!--
public class User
{
    public int Id { get; set; }
    public string Name { get; set; }

    private bool? _isAuthorized;
    public bool IsAuthorized
    {
        get => _isAuthorized ?? true;
        set => _isAuthorized = value;
    }
}
-->
[!code-csharp[User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=User)]

<span data-ttu-id="b060d-251">La `IsAuthorized` propiedad se configura con un valor predeterminado de la base de datos "true":</span><span class="sxs-lookup"><span data-stu-id="b060d-251">The `IsAuthorized` property is configured with a database default value of "true":</span></span>

<!--
        modelBuilder
            .Entity<User>()
            .Property(e => e.IsAuthorized)
            .HasDefaultValue(true);
-->
[!code-csharp[OnModelCreating_User](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_User)]

<span data-ttu-id="b060d-252">La `IsAuthorized` propiedad se puede establecer en "true" o "false" explícitamente antes de la inserción, o bien se puede dejar sin establecer, en cuyo caso se usará el valor predeterminado de la base de datos:</span><span class="sxs-lookup"><span data-stu-id="b060d-252">The `IsAuthorized` property can be set to "true" or "false" explicitly before inserting, or can be left unset in which case the database default will be used:</span></span>

<!--
        using var context = new BlogsContext();

        var userA = new User { Name = "Mac" };
        var userB = new User { Name = "Alice", IsAuthorized = true };
        var userC = new User { Name = "Baxter", IsAuthorized = false }; // Always deny Baxter access!

        context.AddRange(userA, userB, userC);

        context.SaveChanges();
-->
[!code-csharp[Working_with_default_values_5](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=Working_with_default_values_5)]

<span data-ttu-id="b060d-253">La salida de SaveChanges cuando se usa SQLite muestra que el valor predeterminado de la base de datos se usa para Mac, mientras que los valores explícitos están establecidos para Alice y Baxter:</span><span class="sxs-lookup"><span data-stu-id="b060d-253">The output from SaveChanges when using SQLite shows that the database default is used for Mac, while explicit values are set for Alice and Baxter:</span></span>

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='Mac' (Size = 3)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("Name")
VALUES (@p0);
SELECT "Id", "IsAuthorized"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='True' (DbType = String), @p1='Alice' (Size = 5)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='False' (DbType = String), @p1='Baxter' (Size = 6)], CommandType='Text', CommandTimeout='30']
INSERT INTO "User" ("IsAuthorized", "Name")
VALUES (@p0, @p1);
SELECT "Id"
FROM "User"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

### <a name="schema-defaults-only"></a><span data-ttu-id="b060d-254">Solo valores predeterminados de esquema</span><span class="sxs-lookup"><span data-stu-id="b060d-254">Schema defaults only</span></span>

<span data-ttu-id="b060d-255">A veces resulta útil tener valores predeterminados en el esquema de la base de datos creado por EF Core migraciones sin EF Core que nunca use estos valores para las inserciones.</span><span class="sxs-lookup"><span data-stu-id="b060d-255">Sometimes it is useful to have defaults in the database schema created by EF Core migrations without EF Core ever using these values for inserts.</span></span> <span data-ttu-id="b060d-256">Esto puede lograrse configurando la propiedad como, <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="b060d-256">This can be achieved by configuring the property as <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.PropertyBuilder.ValueGeneratedNever%2A?displayProperty=nameWithType> For example:</span></span>

<!--
        modelBuilder
            .Entity<Bar>()
            .Property(e => e.Count)
            .HasDefaultValue(-1)
            .ValueGeneratedNever();
-->
[!code-csharp[OnModelCreating_Bar](../../../samples/core/ChangeTracking/AdditionalChangeTrackingFeatures/DefaultValueSamples.cs?name=OnModelCreating_Bar)]
