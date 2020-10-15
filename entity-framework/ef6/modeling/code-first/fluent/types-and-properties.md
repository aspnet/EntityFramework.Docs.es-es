---
title: 'API fluida: configuración y asignación de propiedades y tipos: EF6'
description: 'API fluida: configuración y asignación de propiedades y tipos en Entity Framework 6'
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/types-and-properties
ms.openlocfilehash: 821672bcb797314c96189443ace7f875a79c8582
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065152"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a><span data-ttu-id="77867-103">API fluida: configuración y asignación de propiedades y tipos</span><span class="sxs-lookup"><span data-stu-id="77867-103">Fluent API - Configuring and Mapping Properties and Types</span></span>
<span data-ttu-id="77867-104">Al trabajar con Entity Framework Code First el comportamiento predeterminado es asignar las clases POCO a las tablas mediante un conjunto de convenciones incorporadas en EF.</span><span class="sxs-lookup"><span data-stu-id="77867-104">When working with Entity Framework Code First the default behavior is to map your POCO classes to tables using a set of conventions baked into EF.</span></span> <span data-ttu-id="77867-105">Sin embargo, a veces no puede o no desea seguir estas convenciones y debe asignar entidades a un valor distinto del que dictan las convenciones.</span><span class="sxs-lookup"><span data-stu-id="77867-105">Sometimes, however, you cannot or do not want to follow those conventions and need to map entities to something other than what the conventions dictate.</span></span>  

<span data-ttu-id="77867-106">Hay dos formas principales de configurar EF para que use un elemento que no sea convenciones, como [anotaciones](xref:ef6/modeling/code-first/data-annotations) o API fluida de EFS.</span><span class="sxs-lookup"><span data-stu-id="77867-106">There are two main ways you can configure EF to use something other than conventions, namely [annotations](xref:ef6/modeling/code-first/data-annotations) or EFs fluent API.</span></span> <span data-ttu-id="77867-107">Las anotaciones solo cubren un subconjunto de la funcionalidad de la API fluida, por lo que hay escenarios de asignación que no se pueden lograr mediante anotaciones.</span><span class="sxs-lookup"><span data-stu-id="77867-107">The annotations only cover a subset of the fluent API functionality, so there are mapping scenarios that cannot be achieved using annotations.</span></span> <span data-ttu-id="77867-108">Este artículo está diseñado para demostrar cómo usar la API fluida para configurar las propiedades.</span><span class="sxs-lookup"><span data-stu-id="77867-108">This article is designed to demonstrate how to use the fluent API to configure properties.</span></span>  

<span data-ttu-id="77867-109">Normalmente, se tiene acceso a la API fluida de Code First mediante la invalidación del método [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) en [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)derivado.</span><span class="sxs-lookup"><span data-stu-id="77867-109">The code first fluent API is most commonly accessed by overriding the [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) method on your derived [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx).</span></span> <span data-ttu-id="77867-110">Los ejemplos siguientes están diseñados para mostrar cómo realizar varias tareas con la API fluida y permiten copiar el código y personalizarlo para que se adapte a su modelo; si desea ver el modelo con el que se pueden usar tal cual, se proporciona al final de este artículo.</span><span class="sxs-lookup"><span data-stu-id="77867-110">The following samples are designed to show how to do various tasks with the fluent api and allow you to copy the code out and customize it to suit your model, if you wish to see the model that they can be used with as-is then it is provided at the end of this article.</span></span>  

## <a name="model-wide-settings"></a><span data-ttu-id="77867-111">Configuración de Model-Wide</span><span class="sxs-lookup"><span data-stu-id="77867-111">Model-Wide Settings</span></span>  

### <a name="default-schema-ef6-onwards"></a><span data-ttu-id="77867-112">Esquema predeterminado (EF6 en adelante)</span><span class="sxs-lookup"><span data-stu-id="77867-112">Default Schema (EF6 onwards)</span></span>  

<span data-ttu-id="77867-113">A partir de EF6, puede usar el método HasDefaultSchema en DbModelBuilder para especificar el esquema de base de datos que se va a usar para todas las tablas, procedimientos almacenados, etc. Esta configuración predeterminada se invalidará para todos los objetos para los que se configure explícitamente un esquema diferente.</span><span class="sxs-lookup"><span data-stu-id="77867-113">Starting with EF6 you can use the HasDefaultSchema method on DbModelBuilder to specify the database schema to use for all tables, stored procedures, etc. This default setting will be overridden for any objects that you explicitly configure a different schema for.</span></span>  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a><span data-ttu-id="77867-114">Convenciones personalizadas (EF6 en adelante)</span><span class="sxs-lookup"><span data-stu-id="77867-114">Custom Conventions (EF6 onwards)</span></span>  

<span data-ttu-id="77867-115">A partir de EF6, puede crear sus propias convenciones para complementar las incluidas en Code First.</span><span class="sxs-lookup"><span data-stu-id="77867-115">Starting with EF6 you can create your own conventions to supplement the ones included in Code First.</span></span> <span data-ttu-id="77867-116">Para obtener más información, vea [convenciones de Code First personalizadas](xref:ef6/modeling/code-first/conventions/custom).</span><span class="sxs-lookup"><span data-stu-id="77867-116">For more details, see [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span></span>  

## <a name="property-mapping"></a><span data-ttu-id="77867-117">Asignación de propiedades</span><span class="sxs-lookup"><span data-stu-id="77867-117">Property Mapping</span></span>  

<span data-ttu-id="77867-118">El método [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) se utiliza para configurar los atributos de cada propiedad que pertenece a una entidad o un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="77867-118">The [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) method is used to configure attributes for each property belonging to an entity or complex type.</span></span> <span data-ttu-id="77867-119">El método de propiedad se utiliza para obtener un objeto de configuración para una propiedad determinada.</span><span class="sxs-lookup"><span data-stu-id="77867-119">The Property method is used to obtain a configuration object for a given property.</span></span> <span data-ttu-id="77867-120">Las opciones del objeto de configuración son específicas del tipo que se está configurando; IsUnicode solo está disponible en las propiedades de cadena, por ejemplo.</span><span class="sxs-lookup"><span data-stu-id="77867-120">The options on the configuration object are specific to the type being configured; IsUnicode is available only on string properties for example.</span></span>  

### <a name="configuring-a-primary-key"></a><span data-ttu-id="77867-121">Configuración de una clave principal</span><span class="sxs-lookup"><span data-stu-id="77867-121">Configuring a Primary Key</span></span>  

<span data-ttu-id="77867-122">La Convención de Entity Framework para las claves principales es:</span><span class="sxs-lookup"><span data-stu-id="77867-122">The Entity Framework convention for primary keys is:</span></span>  

1. <span data-ttu-id="77867-123">La clase define una propiedad cuyo nombre es "ID" o "ID"</span><span class="sxs-lookup"><span data-stu-id="77867-123">Your class defines a property whose name is “ID” or “Id”</span></span>  
2. <span data-ttu-id="77867-124">o un nombre de clase seguido de "ID" o "ID"</span><span class="sxs-lookup"><span data-stu-id="77867-124">or a class name followed by “ID” or “Id”</span></span>  

<span data-ttu-id="77867-125">Para establecer explícitamente una propiedad como clave principal, puede usar el método Haskey (.</span><span class="sxs-lookup"><span data-stu-id="77867-125">To explicitly set a property to be a primary key, you can use the HasKey method.</span></span> <span data-ttu-id="77867-126">En el ejemplo siguiente, se usa el método Haskey (para configurar la clave principal InstructorID en el tipo OfficeAssignment.</span><span class="sxs-lookup"><span data-stu-id="77867-126">In the following example, the HasKey method is used to configure the InstructorID primary key on the OfficeAssignment type.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a><span data-ttu-id="77867-127">Configuración de una clave principal compuesta</span><span class="sxs-lookup"><span data-stu-id="77867-127">Configuring a Composite Primary Key</span></span>  

<span data-ttu-id="77867-128">En el ejemplo siguiente se configuran las propiedades DepartmentID y Name para que sean la clave principal compuesta del tipo Department.</span><span class="sxs-lookup"><span data-stu-id="77867-128">The following example configures the DepartmentID and Name properties to be the composite primary key of the Department type.</span></span>  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a><span data-ttu-id="77867-129">Desactivar la identidad de las claves principales numéricas</span><span class="sxs-lookup"><span data-stu-id="77867-129">Switching off Identity for Numeric Primary Keys</span></span>  

<span data-ttu-id="77867-130">En el ejemplo siguiente se establece la propiedad DepartmentID en System. ComponentModel. DataAnnotations. DatabaseGeneratedOption. None para indicar que la base de datos no generará el valor.</span><span class="sxs-lookup"><span data-stu-id="77867-130">The following example sets the DepartmentID property to System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None to indicate that the value will not be generated by the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a><span data-ttu-id="77867-131">Especificar la longitud máxima de una propiedad</span><span class="sxs-lookup"><span data-stu-id="77867-131">Specifying the Maximum Length on a Property</span></span>  

<span data-ttu-id="77867-132">En el ejemplo siguiente, la propiedad Name no debe tener más de 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="77867-132">In the following example, the Name property should be no longer than 50 characters.</span></span> <span data-ttu-id="77867-133">Si hace que el valor supere los 50 caracteres, recibirá una excepción [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) .</span><span class="sxs-lookup"><span data-stu-id="77867-133">If you make the value longer than 50 characters, you will get a [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) exception.</span></span> <span data-ttu-id="77867-134">Si Code First crea una base de datos a partir de este modelo, también establecerá la longitud máxima de la columna de nombre en 50 caracteres.</span><span class="sxs-lookup"><span data-stu-id="77867-134">If Code First creates a database from this model it will also set the maximum length of the Name column to 50 characters.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a><span data-ttu-id="77867-135">Configuración de la propiedad para que sea necesaria</span><span class="sxs-lookup"><span data-stu-id="77867-135">Configuring the Property to be Required</span></span>  

<span data-ttu-id="77867-136">En el ejemplo siguiente, se requiere la propiedad Name.</span><span class="sxs-lookup"><span data-stu-id="77867-136">In the following example, the Name property is required.</span></span> <span data-ttu-id="77867-137">Si no especifica el nombre, obtendrá una excepción DbEntityValidationException.</span><span class="sxs-lookup"><span data-stu-id="77867-137">If you do not specify the Name, you will get a DbEntityValidationException exception.</span></span> <span data-ttu-id="77867-138">Si Code First crea una base de datos a partir de este modelo, la columna utilizada para almacenar esta propiedad no suele ser que acepte valores NULL.</span><span class="sxs-lookup"><span data-stu-id="77867-138">If Code First creates a database from this model then the column used to store this property will usually be non-nullable.</span></span>  

> [!NOTE]
> <span data-ttu-id="77867-139">En algunos casos, es posible que no sea posible que la columna de la base de datos no acepte valores NULL, aunque se requiera la propiedad.</span><span class="sxs-lookup"><span data-stu-id="77867-139">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="77867-140">Por ejemplo, cuando se usa un dato de estrategia de herencia de TPH para varios tipos, se almacena en una sola tabla.</span><span class="sxs-lookup"><span data-stu-id="77867-140">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="77867-141">Si un tipo derivado incluye una propiedad necesaria, no se puede hacer que la columna no acepte valores NULL, ya que no todos los tipos de la jerarquía tendrán esta propiedad.</span><span class="sxs-lookup"><span data-stu-id="77867-141">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a><span data-ttu-id="77867-142">Configuración de un índice en una o más propiedades</span><span class="sxs-lookup"><span data-stu-id="77867-142">Configuring an Index on one or more properties</span></span>  

> [!NOTE]
> <span data-ttu-id="77867-143">**EF 6.1 en adelante solo** : el atributo de índice se presentó en Entity Framework 6,1.</span><span class="sxs-lookup"><span data-stu-id="77867-143">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="77867-144">Si usa una versión anterior, no se aplica la información de esta sección.</span><span class="sxs-lookup"><span data-stu-id="77867-144">If you are using an earlier version the information in this section does not apply.</span></span>  

<span data-ttu-id="77867-145">La creación de índices no es compatible de forma nativa con la API fluida, pero puede usar la compatibilidad con **IndexAttribute** a través de la API fluida.</span><span class="sxs-lookup"><span data-stu-id="77867-145">Creating indexes isn't natively supported by the Fluent API, but you can make use of the support for **IndexAttribute** via the Fluent API.</span></span> <span data-ttu-id="77867-146">Los atributos de índice se procesan mediante la inclusión de una anotación de modelo en el modelo que luego se convierte en un índice de la base de datos más adelante en la canalización.</span><span class="sxs-lookup"><span data-stu-id="77867-146">Index attributes are processed by including a model annotation on the model that is then turned into an Index in the database later in the pipeline.</span></span> <span data-ttu-id="77867-147">Puede Agregar manualmente estas mismas anotaciones mediante la API fluida.</span><span class="sxs-lookup"><span data-stu-id="77867-147">You can manually add these same annotations using the Fluent API.</span></span>  

<span data-ttu-id="77867-148">La forma más fácil de hacerlo es crear una instancia de **IndexAttribute** que contenga todos los valores para el nuevo índice.</span><span class="sxs-lookup"><span data-stu-id="77867-148">The easiest way to do this is to create an instance of **IndexAttribute** that contains all the settings for the new index.</span></span> <span data-ttu-id="77867-149">Después, puede crear una instancia de **IndexAnnotation** , que es un tipo específico de EF que convertirá la configuración de **IndexAttribute** en una anotación de modelo que se puede almacenar en el modelo de EF.</span><span class="sxs-lookup"><span data-stu-id="77867-149">You can then create an instance of **IndexAnnotation** which is an EF specific type that will convert the **IndexAttribute** settings into a model annotation that can be stored on the EF model.</span></span> <span data-ttu-id="77867-150">A continuación, se pueden pasar al método **HasColumnAnnotation** en la API fluida, especificando el **Índice** de nombre de la anotación.</span><span class="sxs-lookup"><span data-stu-id="77867-150">These can then be passed to the **HasColumnAnnotation** method on the Fluent API, specifying the name **Index** for the annotation.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

<span data-ttu-id="77867-151">Para obtener una lista completa de los valores disponibles en **IndexAttribute**, consulte la sección *Índice* de [code First anotaciones de datos](xref:ef6/modeling/code-first/data-annotations).</span><span class="sxs-lookup"><span data-stu-id="77867-151">For a complete list of the settings available in **IndexAttribute**, see the *Index* section of [Code First Data Annotations](xref:ef6/modeling/code-first/data-annotations).</span></span> <span data-ttu-id="77867-152">Esto incluye la personalización del nombre del índice, la creación de índices únicos y la creación de índices de varias columnas.</span><span class="sxs-lookup"><span data-stu-id="77867-152">This includes customizing the index name, creating unique indexes, and creating multi-column indexes.</span></span>  

<span data-ttu-id="77867-153">Puede especificar varias anotaciones de índice en una sola propiedad pasando una matriz de **IndexAttribute** al constructor de **IndexAnnotation**.</span><span class="sxs-lookup"><span data-stu-id="77867-153">You can specify multiple index annotations on a single property by passing an array of **IndexAttribute** to the constructor of **IndexAnnotation**.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation(
        "Index",  
        new IndexAnnotation(new[]
            {
                new IndexAttribute("Index1"),
                new IndexAttribute("Index2") { IsUnique = true }
            })));
```  

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a><span data-ttu-id="77867-154">Especificar no asignar una propiedad CLR a una columna en la base de datos</span><span class="sxs-lookup"><span data-stu-id="77867-154">Specifying Not to Map a CLR Property to a Column in the Database</span></span>  

<span data-ttu-id="77867-155">En el ejemplo siguiente se muestra cómo especificar que una propiedad de un tipo CLR no está asignada a una columna de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77867-155">The following example shows how to specify that a property on a CLR type is not mapped to a column in the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a><span data-ttu-id="77867-156">Asignar una propiedad CLR a una columna específica en la base de datos</span><span class="sxs-lookup"><span data-stu-id="77867-156">Mapping a CLR Property to a Specific Column in the Database</span></span>  

<span data-ttu-id="77867-157">En el ejemplo siguiente se asigna el nombre de la propiedad CLR a la columna de base de datos DepartmentName.</span><span class="sxs-lookup"><span data-stu-id="77867-157">The following example maps the Name CLR property to the DepartmentName database column.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="77867-158">Cambiar el nombre de una clave externa que no está definida en el modelo</span><span class="sxs-lookup"><span data-stu-id="77867-158">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="77867-159">Si decide no definir una clave externa en un tipo CLR, pero desea especificar el nombre que debe tener en la base de datos, haga lo siguiente:</span><span class="sxs-lookup"><span data-stu-id="77867-159">If you choose not to define a foreign key on a CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a><span data-ttu-id="77867-160">Configurar si una propiedad de cadena admite contenido Unicode</span><span class="sxs-lookup"><span data-stu-id="77867-160">Configuring whether a String Property Supports Unicode Content</span></span>  

<span data-ttu-id="77867-161">De forma predeterminada, las cadenas son Unicode (nvarchar en SQL Server).</span><span class="sxs-lookup"><span data-stu-id="77867-161">By default strings are Unicode (nvarchar in SQL Server).</span></span> <span data-ttu-id="77867-162">Puede usar el método IsUnicode para especificar que una cadena debe ser de tipo VARCHAR.</span><span class="sxs-lookup"><span data-stu-id="77867-162">You can use the IsUnicode method to specify that a string should be of varchar type.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a><span data-ttu-id="77867-163">Configurar el tipo de datos de una columna de base de datos</span><span class="sxs-lookup"><span data-stu-id="77867-163">Configuring the Data Type of a Database Column</span></span>  

<span data-ttu-id="77867-164">El método [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) permite la asignación a distintas representaciones del mismo tipo básico.</span><span class="sxs-lookup"><span data-stu-id="77867-164">The [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) method enables mapping to different representations of the same basic type.</span></span> <span data-ttu-id="77867-165">El uso de este método no permite realizar ninguna conversión de los datos en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="77867-165">Using this method does not enable you to perform any conversion of the data at run time.</span></span> <span data-ttu-id="77867-166">Tenga en cuenta que IsUnicode es la forma preferida de establecer columnas en VARCHAR, ya que es independiente de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77867-166">Note that IsUnicode is the preferred way of setting columns to varchar, as it is database agnostic.</span></span>  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a><span data-ttu-id="77867-167">Configurar propiedades en un tipo complejo</span><span class="sxs-lookup"><span data-stu-id="77867-167">Configuring Properties on a Complex Type</span></span>  

<span data-ttu-id="77867-168">Hay dos maneras de configurar las propiedades escalares en un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="77867-168">There are two ways to configure scalar properties on a complex type.</span></span>  

<span data-ttu-id="77867-169">Puede llamar a la propiedad en ComplexTypeConfiguration.</span><span class="sxs-lookup"><span data-stu-id="77867-169">You can call Property on ComplexTypeConfiguration.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

<span data-ttu-id="77867-170">También puede usar la notación de puntos para tener acceso a una propiedad de un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="77867-170">You can also use the dot notation to access a property of a complex type.</span></span>  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a><span data-ttu-id="77867-171">Configuración de una propiedad que se va a usar como un token de simultaneidad optimista</span><span class="sxs-lookup"><span data-stu-id="77867-171">Configuring a Property to Be Used as an Optimistic Concurrency Token</span></span>  

<span data-ttu-id="77867-172">Para especificar que una propiedad de una entidad representa un token de simultaneidad, puede usar el atributo ConcurrencyCheck o el método IsConcurrencyToken.</span><span class="sxs-lookup"><span data-stu-id="77867-172">To specify that a property in an entity represents a concurrency token, you can use either the ConcurrencyCheck attribute or the IsConcurrencyToken method.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

<span data-ttu-id="77867-173">También puede usar el método IsRowVersion para configurar la propiedad de modo que sea una versión de fila en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77867-173">You can also use the IsRowVersion method to configure the property to be a row version in the database.</span></span> <span data-ttu-id="77867-174">Al establecer la propiedad como una versión de fila, se configura automáticamente para que sea un token de simultaneidad optimista.</span><span class="sxs-lookup"><span data-stu-id="77867-174">Setting the property to be a row version automatically configures it to be an optimistic concurrency token.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a><span data-ttu-id="77867-175">Asignación de tipos</span><span class="sxs-lookup"><span data-stu-id="77867-175">Type Mapping</span></span>  

### <a name="specifying-that-a-class-is-a-complex-type"></a><span data-ttu-id="77867-176">Especificar que una clase es un tipo complejo</span><span class="sxs-lookup"><span data-stu-id="77867-176">Specifying That a Class Is a Complex Type</span></span>  

<span data-ttu-id="77867-177">Por Convención, un tipo que no tiene ninguna clave principal especificada se trata como un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="77867-177">By convention, a type that has no primary key specified is treated as a complex type.</span></span> <span data-ttu-id="77867-178">Hay algunos escenarios en los que Code First no detectarán un tipo complejo (por ejemplo, si tiene una propiedad denominada ID, pero no significa que sea una clave principal).</span><span class="sxs-lookup"><span data-stu-id="77867-178">There are some scenarios where Code First will not detect a complex type (for example, if you do have a property called ID, but you do not mean for it to be a primary key).</span></span> <span data-ttu-id="77867-179">En tales casos, usaría la API fluida para especificar explícitamente que un tipo es un tipo complejo.</span><span class="sxs-lookup"><span data-stu-id="77867-179">In such cases, you would use the fluent API to explicitly specify that a type is a complex type.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a><span data-ttu-id="77867-180">Especificar no asignar un tipo de entidad CLR a una tabla de la base de datos</span><span class="sxs-lookup"><span data-stu-id="77867-180">Specifying Not to Map a CLR Entity Type to a Table in the Database</span></span>  

<span data-ttu-id="77867-181">En el ejemplo siguiente se muestra cómo excluir un tipo CLR para que no se asigne a una tabla de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77867-181">The following example shows how to exclude a CLR type from being mapped to a table in the database.</span></span>  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a><span data-ttu-id="77867-182">Asignar un tipo de entidad a una tabla específica de la base de datos</span><span class="sxs-lookup"><span data-stu-id="77867-182">Mapping an Entity Type to a Specific Table in the Database</span></span>  

<span data-ttu-id="77867-183">Todas las propiedades de Department se asignarán a las columnas de una tabla denominada t_ Department.</span><span class="sxs-lookup"><span data-stu-id="77867-183">All properties of Department will be mapped to columns in a table called t_ Department.</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

<span data-ttu-id="77867-184">También puede especificar el nombre del esquema de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="77867-184">You can also specify the schema name like this:</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a><span data-ttu-id="77867-185">Asignar la herencia de tabla por jerarquía (TPH)</span><span class="sxs-lookup"><span data-stu-id="77867-185">Mapping the Table-Per-Hierarchy (TPH) Inheritance</span></span>  

<span data-ttu-id="77867-186">En el escenario de asignación TPH, todos los tipos de una jerarquía de herencia se asignan a una sola tabla.</span><span class="sxs-lookup"><span data-stu-id="77867-186">In the TPH mapping scenario, all types in an inheritance hierarchy are mapped to a single table.</span></span> <span data-ttu-id="77867-187">Una columna de discriminador se utiliza para identificar el tipo de cada fila.</span><span class="sxs-lookup"><span data-stu-id="77867-187">A discriminator column is used to identify the type of each row.</span></span> <span data-ttu-id="77867-188">Al crear el modelo con Code First, TPH es la estrategia predeterminada para los tipos que participan en la jerarquía de herencia.</span><span class="sxs-lookup"><span data-stu-id="77867-188">When creating your model with Code First, TPH is the default strategy for the types that participate in the inheritance hierarchy.</span></span> <span data-ttu-id="77867-189">De forma predeterminada, la columna discriminadora se agrega a la tabla con el nombre "Discriminator" y el nombre de tipo de CLR de cada tipo de la jerarquía se usa para los valores de discriminador.</span><span class="sxs-lookup"><span data-stu-id="77867-189">By default, the discriminator column is added to the table with the name “Discriminator” and the CLR type name of each type in the hierarchy is used for the discriminator values.</span></span> <span data-ttu-id="77867-190">Puede modificar el comportamiento predeterminado mediante la API fluida.</span><span class="sxs-lookup"><span data-stu-id="77867-190">You can modify the default behavior by using the fluent API.</span></span>  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a><span data-ttu-id="77867-191">Asignación de la herencia de tabla por tipo (TPT)</span><span class="sxs-lookup"><span data-stu-id="77867-191">Mapping the Table-Per-Type (TPT) Inheritance</span></span>  

<span data-ttu-id="77867-192">En el escenario de asignación de TPT, todos los tipos se asignan a tablas individuales.</span><span class="sxs-lookup"><span data-stu-id="77867-192">In the TPT mapping scenario, all types are mapped to individual tables.</span></span> <span data-ttu-id="77867-193">Las propiedades que pertenecen solamente a un tipo base o a un tipo derivado se almacenan en una tabla que se asigna a ese tipo.</span><span class="sxs-lookup"><span data-stu-id="77867-193">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="77867-194">Las tablas que se asignan a tipos derivados también almacenan una clave externa que une la tabla derivada con la tabla base.</span><span class="sxs-lookup"><span data-stu-id="77867-194">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a><span data-ttu-id="77867-195">Asignación de la herencia de la tabla por clase concreta (TPC)</span><span class="sxs-lookup"><span data-stu-id="77867-195">Mapping the Table-Per-Concrete Class (TPC) Inheritance</span></span>  

<span data-ttu-id="77867-196">En el escenario de asignación de TPC, todos los tipos no abstractos de la jerarquía se asignan a tablas individuales.</span><span class="sxs-lookup"><span data-stu-id="77867-196">In the TPC mapping scenario, all non-abstract types in the hierarchy are mapped to individual tables.</span></span> <span data-ttu-id="77867-197">Las tablas que se asignan a las clases derivadas no tienen ninguna relación con la tabla que se asigna a la clase base en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="77867-197">The tables that map to the derived classes have no relationship to the table that maps to the base class in the database.</span></span> <span data-ttu-id="77867-198">Todas las propiedades de una clase, incluidas las propiedades heredadas, se asignan a las columnas de la tabla correspondiente.</span><span class="sxs-lookup"><span data-stu-id="77867-198">All properties of a class, including inherited properties, are mapped to columns of the corresponding table.</span></span>  

<span data-ttu-id="77867-199">Llame al método MapInheritedProperties para configurar cada tipo derivado.</span><span class="sxs-lookup"><span data-stu-id="77867-199">Call the MapInheritedProperties method to configure each derived type.</span></span> <span data-ttu-id="77867-200">MapInheritedProperties reasigna todas las propiedades heredadas de la clase base a nuevas columnas de la tabla para la clase derivada.</span><span class="sxs-lookup"><span data-stu-id="77867-200">MapInheritedProperties remaps all properties that were inherited from the base class to new columns in the table for the derived class.</span></span>  

> [!NOTE]
> <span data-ttu-id="77867-201">Tenga en cuenta que, dado que las tablas que participan en la jerarquía de herencia de TPC no comparten una clave principal, habrá claves de entidad duplicadas al insertar en las tablas que se asignan a las subclases si tiene valores generados por la base de datos con el mismo valor de inicialización de identidad.</span><span class="sxs-lookup"><span data-stu-id="77867-201">Note that because the tables participating in TPC inheritance hierarchy do not share a primary key there will be duplicate entity keys when inserting in tables that are mapped to subclasses if you have database generated values with the same identity seed.</span></span> <span data-ttu-id="77867-202">Para solucionar este problema, puede especificar un valor de inicialización inicial diferente para cada tabla o desactivar la identidad en la propiedad de clave principal.</span><span class="sxs-lookup"><span data-stu-id="77867-202">To solve this problem you can either specify a different initial seed value for each table or switch off identity on the primary key property.</span></span> <span data-ttu-id="77867-203">Identity es el valor predeterminado para las propiedades de clave de entero cuando se trabaja con Code First.</span><span class="sxs-lookup"><span data-stu-id="77867-203">Identity is the default value for integer key properties when working with Code First.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .Property(c => c.CourseID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);

modelBuilder.Entity<OnsiteCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnsiteCourse");
});

modelBuilder.Entity<OnlineCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnlineCourse");
});
```  

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a><span data-ttu-id="77867-204">Asignar propiedades de un tipo de entidad a varias tablas en la base de datos (División de entidades)</span><span class="sxs-lookup"><span data-stu-id="77867-204">Mapping Properties of an Entity Type to Multiple Tables in the Database (Entity Splitting)</span></span>  

<span data-ttu-id="77867-205">La división de entidades permite que las propiedades de un tipo de entidad se repartan entre varias tablas.</span><span class="sxs-lookup"><span data-stu-id="77867-205">Entity splitting allows the properties of an entity type to be spread across multiple tables.</span></span> <span data-ttu-id="77867-206">En el ejemplo siguiente, la entidad Department se divide en dos tablas: Department y DepartmentDetails.</span><span class="sxs-lookup"><span data-stu-id="77867-206">In the following example, the Department entity is split into two tables: Department and DepartmentDetails.</span></span> <span data-ttu-id="77867-207">La división de entidades utiliza varias llamadas al método Map para asignar un subconjunto de propiedades a una tabla específica.</span><span class="sxs-lookup"><span data-stu-id="77867-207">Entity splitting uses multiple calls to the Map method to map a subset of properties to a specific table.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Name });
        m.ToTable("Department");
    })
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Administrator, t.StartDate, t.Budget });
        m.ToTable("DepartmentDetails");
    });
```  

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a><span data-ttu-id="77867-208">Asignar varios tipos de entidad a una tabla de la base de datos (División de tablas)</span><span class="sxs-lookup"><span data-stu-id="77867-208">Mapping Multiple Entity Types to One Table in the Database (Table Splitting)</span></span>  

<span data-ttu-id="77867-209">En el ejemplo siguiente se asignan dos tipos de entidad que comparten una clave principal a una tabla.</span><span class="sxs-lookup"><span data-stu-id="77867-209">The following example maps two entity types that share a primary key to one table.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a><span data-ttu-id="77867-210">Asignar un tipo de entidad para insertar/actualizar/eliminar procedimientos almacenados (EF6 en adelante)</span><span class="sxs-lookup"><span data-stu-id="77867-210">Mapping an Entity Type to Insert/Update/Delete Stored Procedures (EF6 onwards)</span></span>  

<span data-ttu-id="77867-211">A partir de EF6, puede asignar una entidad para usar procedimientos almacenados para INSERT UPDATE y DELETE.</span><span class="sxs-lookup"><span data-stu-id="77867-211">Starting with EF6 you can map an entity to use stored procedures for insert update and delete.</span></span> <span data-ttu-id="77867-212">Para obtener más información, vea [code First INSERT/UPDATE/DELETE Stored Procedures](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).</span><span class="sxs-lookup"><span data-stu-id="77867-212">For more details see, [Code First Insert/Update/Delete Stored Procedures](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).</span></span>  

## <a name="model-used-in-samples"></a><span data-ttu-id="77867-213">Modelo usado en los ejemplos</span><span class="sxs-lookup"><span data-stu-id="77867-213">Model Used in Samples</span></span>  

<span data-ttu-id="77867-214">El siguiente modelo de Code First se usa para los ejemplos de esta página.</span><span class="sxs-lookup"><span data-stu-id="77867-214">The following Code First model is used for the samples on this page.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.ModelConfiguration.Conventions;
// add a reference to System.ComponentModel.DataAnnotations DLL
using System.ComponentModel.DataAnnotations;
using System.Collections.Generic;
using System;

public class SchoolEntities : DbContext
{
    public DbSet<Course> Courses { get; set; }
    public DbSet<Department> Departments { get; set; }
    public DbSet<Instructor> Instructors { get; set; }
    public DbSet<OfficeAssignment> OfficeAssignments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention then the generated tables will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}

public class Department
{
    public Department()
    {
        this.Courses = new HashSet<Course>();
    }
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }
    public decimal Budget { get; set; }
    public System.DateTime StartDate { get; set; }
    public int? Administrator { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; private set; }
}

public class Course
{
    public Course()
    {
        this.Instructors = new HashSet<Instructor>();
    }
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
    public virtual ICollection<Instructor> Instructors { get; private set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public OnsiteCourse()
    {
        Details = new Details();
    }

    public Details Details { get; set; }
}

public class Details
{
    public System.DateTime Time { get; set; }
    public string Location { get; set; }
    public string Days { get; set; }
}

public class Instructor
{
    public Instructor()
    {
        this.Courses = new List<Course>();
    }

    // Primary key
    public int InstructorID { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
    public System.DateTime HireDate { get; set; }

    // Navigation properties
    public virtual ICollection<Course> Courses { get; private set; }
}

public class OfficeAssignment
{
    // Specifying InstructorID as a primary
    [Key()]
    public Int32 InstructorID { get; set; }

    public string Location { get; set; }

    // When Entity Framework sees Timestamp attribute
    // it configures ConcurrencyCheck and DatabaseGeneratedPattern=Computed.
    [Timestamp]
    public Byte[] Timestamp { get; set; }

    // Navigation property
    public virtual Instructor Instructor { get; set; }
}
```  
