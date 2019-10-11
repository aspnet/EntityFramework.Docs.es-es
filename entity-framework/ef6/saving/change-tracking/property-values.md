---
title: 'Trabajar con valores de propiedad: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
ms.openlocfilehash: d8a18182754980d79b71df3f227b30c4ce40366f
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182143"
---
# <a name="working-with-property-values"></a><span data-ttu-id="a5047-102">Trabajar con valores de propiedad</span><span class="sxs-lookup"><span data-stu-id="a5047-102">Working with property values</span></span>
<span data-ttu-id="a5047-103">En la mayoría de los casos, Entity Framework se encargará del seguimiento del estado, los valores originales y los valores actuales de las propiedades de las instancias de la entidad.</span><span class="sxs-lookup"><span data-stu-id="a5047-103">For the most part Entity Framework will take care of tracking the state, original values, and current values of the properties of your entity instances.</span></span> <span data-ttu-id="a5047-104">Sin embargo, puede haber algunos casos, como escenarios desconectados, donde desea ver o manipular la información que EF tiene sobre las propiedades.</span><span class="sxs-lookup"><span data-stu-id="a5047-104">However, there may be some cases - such as disconnected scenarios - where you want to view or manipulate the information EF has about the properties.</span></span> <span data-ttu-id="a5047-105">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="a5047-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="a5047-106">Entity Framework realiza un seguimiento de dos valores para cada propiedad de una entidad de la que se ha realizado un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="a5047-106">Entity Framework keeps track of two values for each property of a tracked entity.</span></span> <span data-ttu-id="a5047-107">El valor actual es, como el nombre indica, el valor actual de la propiedad en la entidad.</span><span class="sxs-lookup"><span data-stu-id="a5047-107">The current value is, as the name indicates, the current value of the property in the entity.</span></span> <span data-ttu-id="a5047-108">El valor original es el valor que tenía la propiedad cuando la entidad se consultaba desde la base de datos o se adjuntó al contexto.</span><span class="sxs-lookup"><span data-stu-id="a5047-108">The original value is the value that the property had when the entity was queried from the database or attached to the context.</span></span>  

<span data-ttu-id="a5047-109">Hay dos mecanismos generales para trabajar con valores de propiedad:</span><span class="sxs-lookup"><span data-stu-id="a5047-109">There are two general mechanisms for working with property values:</span></span>  

- <span data-ttu-id="a5047-110">El valor de una propiedad única se puede obtener de una manera fuertemente tipada mediante el método de propiedad.</span><span class="sxs-lookup"><span data-stu-id="a5047-110">The value of a single property can be obtained in a strongly typed way using the Property method.</span></span>  
- <span data-ttu-id="a5047-111">Los valores de todas las propiedades de una entidad se pueden leer en un objeto DbPropertyValues.</span><span class="sxs-lookup"><span data-stu-id="a5047-111">Values for all properties of an entity can be read into a DbPropertyValues object.</span></span> <span data-ttu-id="a5047-112">A continuación, DbPropertyValues actúa como un objeto similar a un diccionario para permitir la lectura y el establecimiento de los valores de propiedad.</span><span class="sxs-lookup"><span data-stu-id="a5047-112">DbPropertyValues then acts as a dictionary-like object to allow property values to be read and set.</span></span> <span data-ttu-id="a5047-113">Los valores de un objeto DbPropertyValues se pueden establecer a partir de los valores de otro objeto DbPropertyValues o de los valores de otro objeto, como otra copia de la entidad o un objeto de transferencia de datos simple (DTO).</span><span class="sxs-lookup"><span data-stu-id="a5047-113">The values in a DbPropertyValues object can be set from values in another DbPropertyValues object or from values in some other object, such as another copy of the entity or a simple data transfer object (DTO).</span></span>  

<span data-ttu-id="a5047-114">En las secciones siguientes se muestran ejemplos del uso de los dos mecanismos anteriores.</span><span class="sxs-lookup"><span data-stu-id="a5047-114">The sections below show examples of using both of the above mechanisms.</span></span>  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a><span data-ttu-id="a5047-115">Obtener y establecer el valor actual o original de una propiedad individual</span><span class="sxs-lookup"><span data-stu-id="a5047-115">Getting and setting the current or original value of an individual property</span></span>  

<span data-ttu-id="a5047-116">En el ejemplo siguiente se muestra cómo se puede leer el valor actual de una propiedad y establecerla en un nuevo valor:</span><span class="sxs-lookup"><span data-stu-id="a5047-116">The example below shows how the current value of a property can be read and then set to a new value:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(3);

    // Read the current value of the Name property
    string currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;

    // Set the Name property to a new value
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";

    // Read the current value of the Name property using a string for the property name
    object currentName2 = context.Entry(blog).Property("Name").CurrentValue;

    // Set the Name property to a new value using a string for the property name
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

<span data-ttu-id="a5047-117">Use la propiedad OriginalValue en lugar de la propiedad CurrentValue para leer o establecer el valor original.</span><span class="sxs-lookup"><span data-stu-id="a5047-117">Use the OriginalValue property instead of the CurrentValue property to read or set the original value.</span></span>  

<span data-ttu-id="a5047-118">Tenga en cuenta que el valor devuelto se escribe como "objeto" cuando se utiliza una cadena para especificar el nombre de la propiedad.</span><span class="sxs-lookup"><span data-stu-id="a5047-118">Note that the returned value is typed as “object” when a string is used to specify the property name.</span></span> <span data-ttu-id="a5047-119">Por otro lado, el valor devuelto está fuertemente tipado si se utiliza una expresión lambda.</span><span class="sxs-lookup"><span data-stu-id="a5047-119">On the other hand, the returned value is strongly typed if a lambda expression is used.</span></span>  

<span data-ttu-id="a5047-120">Al establecer el valor de la propiedad de este modo, solo se marcará la propiedad como modificada si el nuevo valor es diferente del valor anterior.</span><span class="sxs-lookup"><span data-stu-id="a5047-120">Setting the property value like this will only mark the property as modified if the new value is different from the old value.</span></span>  

<span data-ttu-id="a5047-121">Cuando se establece un valor de propiedad de esta manera, el cambio se detecta automáticamente aunque AutoDetectChanges esté desactivado.</span><span class="sxs-lookup"><span data-stu-id="a5047-121">When a property value is set in this way the change is automatically detected even if AutoDetectChanges is turned off.</span></span>  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a><span data-ttu-id="a5047-122">Obtener y establecer el valor actual de una propiedad no asignada</span><span class="sxs-lookup"><span data-stu-id="a5047-122">Getting and setting the current value of an unmapped property</span></span>  

<span data-ttu-id="a5047-123">También se puede leer el valor actual de una propiedad que no está asignada a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a5047-123">The current value of a property that is not mapped to the database can also be read.</span></span> <span data-ttu-id="a5047-124">Un ejemplo de una propiedad no asignada podría ser una propiedad RssLink en el blog.</span><span class="sxs-lookup"><span data-stu-id="a5047-124">An example of an unmapped property could be an RssLink property on Blog.</span></span> <span data-ttu-id="a5047-125">Este valor se puede calcular en función de BlogId y, por tanto, no es necesario almacenarlo en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a5047-125">This value may be calculated based on the BlogId, and therefore doesn't need to be stored in the database.</span></span> <span data-ttu-id="a5047-126">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a5047-126">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    // Read the current value of an unmapped property
    var rssLink = context.Entry(blog).Property(p => p.RssLink).CurrentValue;

    // Use a string to specify the property name
    var rssLinkAgain = context.Entry(blog).Property("RssLink").CurrentValue;
}
```  

<span data-ttu-id="a5047-127">También se puede establecer el valor actual si la propiedad expone un establecedor.</span><span class="sxs-lookup"><span data-stu-id="a5047-127">The current value can also be set if the property exposes a setter.</span></span>  

<span data-ttu-id="a5047-128">Leer los valores de las propiedades sin asignar es útil al realizar Entity Framework la validación de las propiedades no asignadas.</span><span class="sxs-lookup"><span data-stu-id="a5047-128">Reading the values of unmapped properties is useful when performing Entity Framework validation of unmapped properties.</span></span> <span data-ttu-id="a5047-129">Por el mismo motivo, los valores actuales se pueden leer y establecer para las propiedades de entidades a las que el contexto no realiza un seguimiento actualmente.</span><span class="sxs-lookup"><span data-stu-id="a5047-129">For the same reason current values can be read and set for properties of entities that are not currently being tracked by the context.</span></span> <span data-ttu-id="a5047-130">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a5047-130">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Create an entity that is not being tracked
    var blog = new Blog { Name = "ADO.NET Blog" };

    // Read and set the current value of Name as before
    var currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";
    var currentName2 = context.Entry(blog).Property("Name").CurrentValue;
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

<span data-ttu-id="a5047-131">Tenga en cuenta que los valores originales no están disponibles para las propiedades no asignadas o para las propiedades de entidades de las que el contexto no realiza un seguimiento.</span><span class="sxs-lookup"><span data-stu-id="a5047-131">Note that original values are not available for unmapped properties or for properties of entities that are not being tracked by the context.</span></span>  

## <a name="checking-whether-a-property-is-marked-as-modified"></a><span data-ttu-id="a5047-132">Comprobar si una propiedad está marcada como modificada</span><span class="sxs-lookup"><span data-stu-id="a5047-132">Checking whether a property is marked as modified</span></span>  

<span data-ttu-id="a5047-133">En el ejemplo siguiente se muestra cómo comprobar si una propiedad individual está marcada como modificada:</span><span class="sxs-lookup"><span data-stu-id="a5047-133">The example below shows how to check whether or not an individual property is marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

<span data-ttu-id="a5047-134">Los valores de las propiedades modificadas se envían como actualizaciones a la base de datos cuando se llama a SaveChanges.</span><span class="sxs-lookup"><span data-stu-id="a5047-134">The values of modified properties are sent as updates to the database when SaveChanges is called.</span></span>  

##  <a name="marking-a-property-as-modified"></a><span data-ttu-id="a5047-135">Marcar una propiedad como modificada</span><span class="sxs-lookup"><span data-stu-id="a5047-135">Marking a property as modified</span></span>  

<span data-ttu-id="a5047-136">En el ejemplo siguiente se muestra cómo forzar que una propiedad individual se marque como modificada:</span><span class="sxs-lookup"><span data-stu-id="a5047-136">The example below shows how to force an individual property to be marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

<span data-ttu-id="a5047-137">Marcar una propiedad como modificada obliga a que se envíe una actualización a la base de datos para la propiedad cuando se llama a SaveChanges incluso si el valor actual de la propiedad es el mismo que el valor original.</span><span class="sxs-lookup"><span data-stu-id="a5047-137">Marking a property as modified forces an update to be send to the database for the property when SaveChanges is called even if the current value of the property is the same as its original value.</span></span>  

<span data-ttu-id="a5047-138">Actualmente no es posible restablecer una propiedad individual para que no se modifique una vez que se ha marcado como modificada.</span><span class="sxs-lookup"><span data-stu-id="a5047-138">It is not currently possible to reset an individual property to be not modified after it has been marked as modified.</span></span> <span data-ttu-id="a5047-139">Esto es algo que tenemos previsto admitir en una versión futura.</span><span class="sxs-lookup"><span data-stu-id="a5047-139">This is something we plan to support in a future release.</span></span>  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a><span data-ttu-id="a5047-140">Leer los valores actuales, originales y de base de datos para todas las propiedades de una entidad</span><span class="sxs-lookup"><span data-stu-id="a5047-140">Reading current, original, and database values for all properties of an entity</span></span>  

<span data-ttu-id="a5047-141">En el ejemplo siguiente se muestra cómo leer los valores actuales, los valores originales y los valores reales de la base de datos para todas las propiedades asignadas de una entidad.</span><span class="sxs-lookup"><span data-stu-id="a5047-141">The example below shows how to read the current values, the original values, and the values actually in the database for all mapped properties of an entity.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Make a modification to Name in the tracked entity
    blog.Name = "My Cool Blog";

    // Make a modification to Name in the database
    context.Database.SqlCommand("update dbo.Blogs set Name = 'My Boring Blog' where Id = 1");

    // Print out current, original, and database values
    Console.WriteLine("Current values:");
    PrintValues(context.Entry(blog).CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(context.Entry(blog).OriginalValues);

    Console.WriteLine("\nDatabase values:");
    PrintValues(context.Entry(blog).GetDatabaseValues());
}

public static void PrintValues(DbPropertyValues values)
{
    foreach (var propertyName in values.PropertyNames)
    {
        Console.WriteLine("Property {0} has value {1}",
                          propertyName, values[propertyName]);
    }
}
```  

<span data-ttu-id="a5047-142">Los valores actuales son los valores que las propiedades de la entidad contienen actualmente.</span><span class="sxs-lookup"><span data-stu-id="a5047-142">The current values are the values that the properties of the entity currently contain.</span></span> <span data-ttu-id="a5047-143">Los valores originales son los valores que se leyeron de la base de datos cuando se realizó la consulta de la entidad.</span><span class="sxs-lookup"><span data-stu-id="a5047-143">The original values are the values that were read from the database when the entity was queried.</span></span> <span data-ttu-id="a5047-144">Los valores de la base de datos son los valores que están almacenados actualmente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a5047-144">The database values are the values as they are currently stored in the database.</span></span> <span data-ttu-id="a5047-145">Obtener los valores de la base de datos es útil cuando los valores de la base de datos pueden haber cambiado desde la consulta de la entidad, por ejemplo, cuando otro usuario ha realizado una edición simultánea en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="a5047-145">Getting the database values is useful when the values in the database may have changed since the entity was queried such as when a concurrent edit to the database has been made by another user.</span></span>  

## <a name="setting-current-or-original-values-from-another-object"></a><span data-ttu-id="a5047-146">Establecer valores actuales o originales de otro objeto</span><span class="sxs-lookup"><span data-stu-id="a5047-146">Setting current or original values from another object</span></span>  

<span data-ttu-id="a5047-147">Los valores actuales o originales de una entidad de la que se ha realizado un seguimiento se pueden actualizar mediante la copia de los valores de otro objeto.</span><span class="sxs-lookup"><span data-stu-id="a5047-147">The current or original values of a tracked entity can be updated by copying values from another object.</span></span> <span data-ttu-id="a5047-148">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a5047-148">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var coolBlog = new Blog { Id = 1, Name = "My Cool Blog" };
    var boringBlog = new BlogDto { Id = 1, Name = "My Boring Blog" };

    // Change the current and original values by copying the values from other objects
    var entry = context.Entry(blog);
    entry.CurrentValues.SetValues(coolBlog);
    entry.OriginalValues.SetValues(boringBlog);

    // Print out current and original values
    Console.WriteLine("Current values:");
    PrintValues(entry.CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(entry.OriginalValues);
}

public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```  

<span data-ttu-id="a5047-149">La ejecución del código anterior se imprimirá:</span><span class="sxs-lookup"><span data-stu-id="a5047-149">Running the code above will print out:</span></span>  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

<span data-ttu-id="a5047-150">Esta técnica se usa a veces al actualizar una entidad con valores obtenidos de una llamada de servicio o un cliente en una aplicación de n niveles.</span><span class="sxs-lookup"><span data-stu-id="a5047-150">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="a5047-151">Tenga en cuenta que el objeto utilizado no tiene que ser del mismo tipo que la entidad, siempre y cuando tenga propiedades cuyos nombres coincidan con los de la entidad.</span><span class="sxs-lookup"><span data-stu-id="a5047-151">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="a5047-152">En el ejemplo anterior, se usa una instancia de BlogDTO para actualizar los valores originales.</span><span class="sxs-lookup"><span data-stu-id="a5047-152">In the example above, an instance of BlogDTO is used to update the original values.</span></span>  

<span data-ttu-id="a5047-153">Tenga en cuenta que solo las propiedades que se establecen en valores diferentes cuando se copian del otro objeto se marcan como modificadas.</span><span class="sxs-lookup"><span data-stu-id="a5047-153">Note that only properties that are set to different values when copied from the other object will be marked as modified.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="a5047-154">Establecer los valores actuales o originales de un diccionario</span><span class="sxs-lookup"><span data-stu-id="a5047-154">Setting current or original values from a dictionary</span></span>  

<span data-ttu-id="a5047-155">Los valores actuales o originales de una entidad de la que se ha realizado un seguimiento se pueden actualizar mediante la copia de los valores de un diccionario u otra estructura de datos.</span><span class="sxs-lookup"><span data-stu-id="a5047-155">The current or original values of a tracked entity can be updated by copying values from a dictionary or some other data structure.</span></span> <span data-ttu-id="a5047-156">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a5047-156">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "The New ADO.NET Blog" },
        { "Url", "blogs.msdn.com/adonet" },
    };

    var currentValues = context.Entry(blog).CurrentValues;

    foreach (var propertyName in newValues.Keys)
    {
        currentValues[propertyName] = newValues[propertyName];
    }

    PrintValues(currentValues);
}
```  

<span data-ttu-id="a5047-157">Use la propiedad OriginalValues en lugar de la propiedad CurrentValues para establecer los valores originales.</span><span class="sxs-lookup"><span data-stu-id="a5047-157">Use the OriginalValues property instead of the CurrentValues property to set original values.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a><span data-ttu-id="a5047-158">Establecer valores actuales o originales de un diccionario mediante la propiedad</span><span class="sxs-lookup"><span data-stu-id="a5047-158">Setting current or original values from a dictionary using Property</span></span>  

<span data-ttu-id="a5047-159">Una alternativa al uso de CurrentValues o OriginalValues, como se muestra arriba, es usar el método de propiedad para establecer el valor de cada propiedad.</span><span class="sxs-lookup"><span data-stu-id="a5047-159">An alternative to using CurrentValues or OriginalValues as shown above is to use the Property method to set the value of each property.</span></span> <span data-ttu-id="a5047-160">Esto puede ser preferible cuando es necesario establecer los valores de las propiedades complejas.</span><span class="sxs-lookup"><span data-stu-id="a5047-160">This can be preferable when you need to set the values of complex properties.</span></span> <span data-ttu-id="a5047-161">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a5047-161">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "John Doe" },
        { "Location.City", "Redmond" },
        { "Location.State.Name", "Washington" },
        { "Location.State.Code", "WA" },
    };

    var entry = context.Entry(user);

    foreach (var propertyName in newValues.Keys)
    {
        entry.Property(propertyName).CurrentValue = newValues[propertyName];
    }
}
```  

<span data-ttu-id="a5047-162">En el ejemplo anterior se tiene acceso a las propiedades complejas mediante nombres con puntos.</span><span class="sxs-lookup"><span data-stu-id="a5047-162">In the example above complex properties are accessed using dotted names.</span></span> <span data-ttu-id="a5047-163">Para conocer otras formas de obtener acceso a propiedades complejas, vea las dos secciones más adelante en este tema específicamente sobre las propiedades complejas.</span><span class="sxs-lookup"><span data-stu-id="a5047-163">For other ways to access complex properties see the two sections later in this topic specifically about complex properties.</span></span>  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="a5047-164">Crear un objeto clonado que contenga valores actual, original o de base de datos</span><span class="sxs-lookup"><span data-stu-id="a5047-164">Creating a cloned object containing current, original, or database values</span></span>  

<span data-ttu-id="a5047-165">El objeto DbPropertyValues devuelto de CurrentValues, OriginalValues o GetDatabaseValues se puede usar para crear un clon de la entidad.</span><span class="sxs-lookup"><span data-stu-id="a5047-165">The DbPropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity.</span></span> <span data-ttu-id="a5047-166">Este clon contendrá los valores de propiedad del objeto DbPropertyValues que se usa para crearlo.</span><span class="sxs-lookup"><span data-stu-id="a5047-166">This clone will contain the property values from the DbPropertyValues object used to create it.</span></span> <span data-ttu-id="a5047-167">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="a5047-167">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

<span data-ttu-id="a5047-168">Tenga en cuenta que el objeto devuelto no es la entidad y el contexto no realiza su seguimiento.</span><span class="sxs-lookup"><span data-stu-id="a5047-168">Note that the object returned is not the entity and is not being tracked by the context.</span></span> <span data-ttu-id="a5047-169">El objeto devuelto tampoco tiene ninguna relación establecida en otros objetos.</span><span class="sxs-lookup"><span data-stu-id="a5047-169">The returned object also does not have any relationships set to other objects.</span></span>  

<span data-ttu-id="a5047-170">El objeto clonado puede ser útil para resolver problemas relacionados con las actualizaciones simultáneas en la base de datos, especialmente cuando se usa una interfaz de usuario que implique el enlace de datos a objetos de un tipo determinado.</span><span class="sxs-lookup"><span data-stu-id="a5047-170">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially where a UI that involves data binding to objects of a certain type is being used.</span></span>  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a><span data-ttu-id="a5047-171">Obtener y establecer los valores actuales o originales de propiedades complejas</span><span class="sxs-lookup"><span data-stu-id="a5047-171">Getting and setting the current or original values of complex properties</span></span>  

<span data-ttu-id="a5047-172">El valor de un objeto complejo completo se puede leer y establecer mediante el método de propiedad, tal como puede ser para una propiedad primitiva.</span><span class="sxs-lookup"><span data-stu-id="a5047-172">The value of an entire complex object can be read and set using the Property method just as it can be for a primitive property.</span></span> <span data-ttu-id="a5047-173">Además, puede explorar en profundidad el objeto complejo y leer o establecer las propiedades de ese objeto, o incluso un objeto anidado.</span><span class="sxs-lookup"><span data-stu-id="a5047-173">In addition you can drill down into the complex object and read or set properties of that object, or even a nested object.</span></span> <span data-ttu-id="a5047-174">A continuación se muestran algunos ejemplos:</span><span class="sxs-lookup"><span data-stu-id="a5047-174">Here are some examples:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    // Get the Location complex object
    var location = context.Entry(user)
                       .Property(u => u.Location)
                       .CurrentValue;

    // Get the nested State complex object using chained calls
    var state1 = context.Entry(user)
                     .ComplexProperty(u => u.Location)
                     .Property(l => l.State)
                     .CurrentValue;

    // Get the nested State complex object using a single lambda expression
    var state2 = context.Entry(user)
                     .Property(u => u.Location.State)
                     .CurrentValue;

    // Get the nested State complex object using a dotted string
    var state3 = context.Entry(user)
                     .Property("Location.State")
                     .CurrentValue;

    // Get the value of the Name property on the nested State complex object using chained calls
    var name1 = context.Entry(user)
                       .ComplexProperty(u => u.Location)
                       .ComplexProperty(l => l.State)
                       .Property(s => s.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a single lambda expression
    var name2 = context.Entry(user)
                       .Property(u => u.Location.State.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a dotted string
    var name3 = context.Entry(user)
                       .Property("Location.State.Name")
                       .CurrentValue;
}
```  

<span data-ttu-id="a5047-175">Use la propiedad OriginalValue en lugar de la propiedad CurrentValue para obtener o establecer un valor original.</span><span class="sxs-lookup"><span data-stu-id="a5047-175">Use the OriginalValue property instead of the CurrentValue property to get or set an original value.</span></span>  

<span data-ttu-id="a5047-176">Tenga en cuenta que se puede utilizar la propiedad o el método ComplexProperty para tener acceso a una propiedad compleja.</span><span class="sxs-lookup"><span data-stu-id="a5047-176">Note that either the Property or the ComplexProperty method can be used to access a complex property.</span></span> <span data-ttu-id="a5047-177">Sin embargo, se debe usar el método ComplexProperty si desea explorar en profundidad el objeto complejo con llamadas adicionales a propiedades o ComplexProperty.</span><span class="sxs-lookup"><span data-stu-id="a5047-177">However, the ComplexProperty method must be used if you wish to drill down into the complex object with additional Property or ComplexProperty calls.</span></span>  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a><span data-ttu-id="a5047-178">Usar DbPropertyValues para tener acceso a propiedades complejas</span><span class="sxs-lookup"><span data-stu-id="a5047-178">Using DbPropertyValues to access complex properties</span></span>  

<span data-ttu-id="a5047-179">Cuando se usa CurrentValues, OriginalValues o GetDatabaseValues para obtener todos los valores actuales, originales o de la base de datos de una entidad, los valores de las propiedades complejas se devuelven como objetos DbPropertyValues anidados.</span><span class="sxs-lookup"><span data-stu-id="a5047-179">When you use CurrentValues, OriginalValues, or GetDatabaseValues to get all the current, original, or database values for an entity, the values of any complex properties are returned as nested DbPropertyValues objects.</span></span> <span data-ttu-id="a5047-180">Estos objetos anidados se pueden usar para obtener valores del objeto complejo.</span><span class="sxs-lookup"><span data-stu-id="a5047-180">These nested objects can then be used to get values of the complex object.</span></span> <span data-ttu-id="a5047-181">Por ejemplo, el método siguiente imprimirá los valores de todas las propiedades, incluidos los valores de las propiedades complejas y las propiedades complejas anidadas.</span><span class="sxs-lookup"><span data-stu-id="a5047-181">For example, the following method will print out the values of all properties, including values of any complex properties and nested complex properties.</span></span>  

``` csharp
public static void WritePropertyValues(string parentPropertyName, DbPropertyValues propertyValues)
{
    foreach (var propertyName in propertyValues.PropertyNames)
    {
        var nestedValues = propertyValues[propertyName] as DbPropertyValues;
        if (nestedValues != null)
        {
            WritePropertyValues(parentPropertyName + propertyName + ".", nestedValues);
        }
        else
        {
            Console.WriteLine("Property {0}{1} has value {2}",
                              parentPropertyName, propertyName,
                              propertyValues[propertyName]);
        }
    }
}
```  

<span data-ttu-id="a5047-182">Para imprimir todos los valores de propiedad actuales, se llamaría al método de la siguiente manera:</span><span class="sxs-lookup"><span data-stu-id="a5047-182">To print out all current property values the method would be called like this:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
