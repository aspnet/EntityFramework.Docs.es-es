---
title: 'Procedimientos almacenados con varios conjuntos de resultados: EF6'
description: Procedimientos almacenados con varios conjuntos de resultados en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/multiple-result-sets
ms.openlocfilehash: 8c80e2b8c861a763b7f63ea2523194028498dace
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066283"
---
# <a name="stored-procedures-with-multiple-result-sets"></a><span data-ttu-id="f39d1-103">Procedimientos almacenados con varios conjuntos de resultados</span><span class="sxs-lookup"><span data-stu-id="f39d1-103">Stored Procedures with Multiple Result Sets</span></span>
<span data-ttu-id="f39d1-104">A veces, al utilizar procedimientos almacenados, deberá devolver más de un conjunto de resultados.</span><span class="sxs-lookup"><span data-stu-id="f39d1-104">Sometimes when using stored procedures you will need to return more than one result set.</span></span> <span data-ttu-id="f39d1-105">Este escenario se usa normalmente para reducir el número de recorridos de ida y vuelta de base de datos necesarios para crear una sola pantalla.</span><span class="sxs-lookup"><span data-stu-id="f39d1-105">This scenario is commonly used to reduce the number of database round trips required to compose a single screen.</span></span><span data-ttu-id="f39d1-106">Antes de EF5, Entity Framework permitiría que se llamara al procedimiento almacenado, pero solo devolvería el primer conjunto de resultados al código de llamada.</span><span class="sxs-lookup"><span data-stu-id="f39d1-106"> Prior to EF5, Entity Framework would allow the stored procedure to be called but would only return the first result set to the calling code.</span></span>

<span data-ttu-id="f39d1-107">En este artículo se muestran dos formas de usar para tener acceso a más de un conjunto de resultados de un procedimiento almacenado en Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f39d1-107">This article will show you two ways that you can use to access more than one result set from a stored procedure in Entity Framework.</span></span> <span data-ttu-id="f39d1-108">Una que usa solo código y funciona con Code First y el diseñador de EF y otro que solo funciona con EF Designer.</span><span class="sxs-lookup"><span data-stu-id="f39d1-108">One that uses just code and works with both Code first and the EF Designer and one that only works with the EF Designer.</span></span> <span data-ttu-id="f39d1-109">Las herramientas y la compatibilidad con API para esto deberían mejorar en versiones futuras de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f39d1-109">The tooling and API support for this should improve in future versions of Entity Framework.</span></span>

## <a name="model"></a><span data-ttu-id="f39d1-110">Modelo</span><span class="sxs-lookup"><span data-stu-id="f39d1-110">Model</span></span>

<span data-ttu-id="f39d1-111">En los ejemplos de este artículo se usa un blog básico y un modelo de publicaciones en el que un blog tiene muchas publicaciones y una publicación pertenece a un solo blog.</span><span class="sxs-lookup"><span data-stu-id="f39d1-111">The examples in this article use a basic Blog and Posts model where a blog has many posts and a post belongs to a single blog.</span></span> <span data-ttu-id="f39d1-112">Usaremos un procedimiento almacenado en la base de datos que devuelva todos los blogs y publicaciones, algo parecido a esto:</span><span class="sxs-lookup"><span data-stu-id="f39d1-112">We will use a stored procedure in the database that returns all blogs and posts, something like this:</span></span>

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a><span data-ttu-id="f39d1-113">Obtener acceso a varios conjuntos de resultados con código</span><span class="sxs-lookup"><span data-stu-id="f39d1-113">Accessing Multiple Result Sets with Code</span></span>

<span data-ttu-id="f39d1-114">Podemos ejecutar usar código para emitir un comando SQL sin formato para ejecutar nuestro procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="f39d1-114">We can execute use code to issue a raw SQL command to execute our stored procedure.</span></span> <span data-ttu-id="f39d1-115">La ventaja de este enfoque es que funciona tanto con Code First como con el diseñador EF.</span><span class="sxs-lookup"><span data-stu-id="f39d1-115">The advantage of this approach is that it works with both Code first and the EF Designer.</span></span>

<span data-ttu-id="f39d1-116">Para conseguir que funcionen varios conjuntos de resultados, es necesario colocarlos en la API de ObjectContext mediante la interfaz IObjectContextAdapter.</span><span class="sxs-lookup"><span data-stu-id="f39d1-116">In order to get multiple result sets working we need to drop to the ObjectContext API by using the IObjectContextAdapter interface.</span></span>

<span data-ttu-id="f39d1-117">Una vez que tenemos un ObjectContext, podemos usar el método translate para traducir los resultados de nuestro procedimiento almacenado en entidades que se pueden seguir y usar en EF como normal.</span><span class="sxs-lookup"><span data-stu-id="f39d1-117">Once we have an ObjectContext then we can use the Translate method to translate the results of our stored procedure into entities that can be tracked and used in EF as normal.</span></span> <span data-ttu-id="f39d1-118">En el ejemplo de código siguiente se muestra esto en acción.</span><span class="sxs-lookup"><span data-stu-id="f39d1-118">The following code sample demonstrates this in action.</span></span>

``` csharp
    using (var db = new BloggingContext())
    {
        // If using Code First we need to make sure the model is built before we open the connection
        // This isn't required for models created with the EF Designer
        db.Database.Initialize(force: false);

        // Create a SQL command to execute the sproc
        var cmd = db.Database.Connection.CreateCommand();
        cmd.CommandText = "[dbo].[GetAllBlogsAndPosts]";

        try
        {

            db.Database.Connection.Open();
            // Run the sproc
            var reader = cmd.ExecuteReader();

            // Read Blogs from the first result set
            var blogs = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Blog>(reader, "Blogs", MergeOption.AppendOnly);   


            foreach (var item in blogs)
            {
                Console.WriteLine(item.Name);
            }        

            // Move to second result set and read Posts
            reader.NextResult();
            var posts = ((IObjectContextAdapter)db)
                .ObjectContext
                .Translate<Post>(reader, "Posts", MergeOption.AppendOnly);


            foreach (var item in posts)
            {
                Console.WriteLine(item.Title);
            }
        }
        finally
        {
            db.Database.Connection.Close();
        }
    }
```

<span data-ttu-id="f39d1-119">El método translate acepta el lector que se ha recibido cuando se ejecuta el procedimiento, un nombre de EntitySet y un MergeOption.</span><span class="sxs-lookup"><span data-stu-id="f39d1-119">The Translate method accepts the reader that we received when we executed the procedure, an EntitySet name, and a MergeOption.</span></span> <span data-ttu-id="f39d1-120">El nombre de EntitySet será el mismo que el de la propiedad DbSet en el contexto derivado.</span><span class="sxs-lookup"><span data-stu-id="f39d1-120">The EntitySet name will be the same as the DbSet property on your derived context.</span></span> <span data-ttu-id="f39d1-121">La enumeración MergeOption controla cómo se administran los resultados si la misma entidad ya existe en la memoria.</span><span class="sxs-lookup"><span data-stu-id="f39d1-121">The MergeOption enum controls how results are handled if the same entity already exists in memory.</span></span>

<span data-ttu-id="f39d1-122">Aquí se recorre en iteración la colección de blogs antes de que se llame a NextResult, lo que es importante con el código anterior, ya que se debe consumir el primer conjunto de resultados antes de pasar al siguiente conjunto de resultados.</span><span class="sxs-lookup"><span data-stu-id="f39d1-122">Here we iterate through the collection of blogs before we call NextResult, this is important given the above code because the first result set must be consumed before moving to the next result set.</span></span>

<span data-ttu-id="f39d1-123">Una vez que se llama a los dos métodos de traducción, EF realiza el seguimiento de las entidades blog y post de la misma manera que cualquier otra entidad, por lo que se puede modificar o eliminar y guardar como normal.</span><span class="sxs-lookup"><span data-stu-id="f39d1-123">Once the two translate methods are called then the Blog and Post entities are tracked by EF the same way as any other entity and so can be modified or deleted and saved as normal.</span></span>

>[!NOTE]
> <span data-ttu-id="f39d1-124">EF no tiene en cuenta ninguna asignación al crear entidades mediante el método translate.</span><span class="sxs-lookup"><span data-stu-id="f39d1-124">EF does not take any mapping into account when it creates entities using the Translate method.</span></span> <span data-ttu-id="f39d1-125">Simplemente coincidentes con los nombres de columna del conjunto de resultados con los nombres de propiedad de las clases.</span><span class="sxs-lookup"><span data-stu-id="f39d1-125">It will simply match column names in the result set with property names on your classes.</span></span>

>[!NOTE]
> <span data-ttu-id="f39d1-126">Si tiene habilitada la carga diferida, el acceso a la propiedad postes en una de las entidades del blog, EF se conectará a la base de datos para cargar de forma diferida todas las publicaciones, aunque ya se hayan cargado todas.</span><span class="sxs-lookup"><span data-stu-id="f39d1-126">That if you have lazy loading enabled, accessing the posts property on one of the blog entities then EF will connect to the database to lazily load all posts, even though we have already loaded them all.</span></span> <span data-ttu-id="f39d1-127">Esto se debe a que EF no puede saber si ha cargado o no todas las publicaciones o si hay más en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f39d1-127">This is because EF cannot know whether or not you have loaded all posts or if there are more in the database.</span></span> <span data-ttu-id="f39d1-128">Si desea evitar esto, tendrá que deshabilitar la carga diferida.</span><span class="sxs-lookup"><span data-stu-id="f39d1-128">If you want to avoid this then you will need to disable lazy loading.</span></span>

## <a name="multiple-result-sets-with-configured-in-edmx"></a><span data-ttu-id="f39d1-129">Varios conjuntos de resultados con configurado en EDMX</span><span class="sxs-lookup"><span data-stu-id="f39d1-129">Multiple Result Sets with Configured in EDMX</span></span>

>[!NOTE]
> <span data-ttu-id="f39d1-130">Debe tener como destino .NET Framework 4,5 para poder configurar varios conjuntos de resultados en EDMX.</span><span class="sxs-lookup"><span data-stu-id="f39d1-130">You must target .NET Framework 4.5 to be able to configure multiple result sets in EDMX.</span></span> <span data-ttu-id="f39d1-131">Si el destino es .NET 4,0, puede usar el método basado en código que se muestra en la sección anterior.</span><span class="sxs-lookup"><span data-stu-id="f39d1-131">If you are targeting .NET 4.0 you can use the code-based method shown in the previous section.</span></span>

<span data-ttu-id="f39d1-132">Si usa el diseñador de EF, también puede modificar el modelo para que conozca los diferentes conjuntos de resultados que se devolverán.</span><span class="sxs-lookup"><span data-stu-id="f39d1-132">If you are using the EF Designer, you can also modify your model so that it knows about the different result sets that will be returned.</span></span> <span data-ttu-id="f39d1-133">Lo que hay que saber antes de la mano es que las herramientas no tienen un conjunto de resultados múltiple, por lo que tendrá que editar manualmente el archivo edmx.</span><span class="sxs-lookup"><span data-stu-id="f39d1-133">One thing to know before hand is that the tooling is not multiple result set aware, so you will need to manually edit the edmx file.</span></span> <span data-ttu-id="f39d1-134">La edición del archivo edmx como este funcionará, pero también interrumpirá la validación del modelo en VS.</span><span class="sxs-lookup"><span data-stu-id="f39d1-134">Editing the edmx file like this will work but it will also break the validation of the model in VS.</span></span> <span data-ttu-id="f39d1-135">Por lo tanto, si valida el modelo, siempre se producirán errores.</span><span class="sxs-lookup"><span data-stu-id="f39d1-135">So if you validate your model you will always get errors.</span></span>

-   <span data-ttu-id="f39d1-136">Para ello, debe agregar el procedimiento almacenado al modelo como lo haría para una sola consulta de conjunto de resultados.</span><span class="sxs-lookup"><span data-stu-id="f39d1-136">In order to do this you need to add the stored procedure to your model as you would for a single result set query.</span></span>
-   <span data-ttu-id="f39d1-137">Una vez hecho esto, debe hacer clic con el botón derecho en el modelo y seleccionar **abrir con.**</span><span class="sxs-lookup"><span data-stu-id="f39d1-137">Once you have this then you need to right click on your model and select **Open With..**</span></span> <span data-ttu-id="f39d1-138">a continuación, **XML**</span><span class="sxs-lookup"><span data-stu-id="f39d1-138">then **Xml**</span></span>

    ![Abrir como](~/ef6/media/openas.png)

<span data-ttu-id="f39d1-140">Una vez que tenga el modelo abierto como XML, debe realizar los siguientes pasos:</span><span class="sxs-lookup"><span data-stu-id="f39d1-140">Once you have the model opened as XML then you need to do the following steps:</span></span>

-   <span data-ttu-id="f39d1-141">Busque el tipo complejo y la importación de función en el modelo:</span><span class="sxs-lookup"><span data-stu-id="f39d1-141">Find the complex type and function import in your model:</span></span>

``` xml
    <!-- CSDL content -->
    <edmx:ConceptualModels>

    ...

      <FunctionImport Name="GetAllBlogsAndPosts" ReturnType="Collection(BlogModel.GetAllBlogsAndPosts_Result)" />

    ...

      <ComplexType Name="GetAllBlogsAndPosts_Result">
        <Property Type="Int32" Name="BlogId" Nullable="false" />
        <Property Type="String" Name="Name" Nullable="false" MaxLength="255" />
        <Property Type="String" Name="Description" Nullable="true" />
      </ComplexType>

    ...

    </edmx:ConceptualModels>
```

 

-   <span data-ttu-id="f39d1-142">Quitar el tipo complejo</span><span class="sxs-lookup"><span data-stu-id="f39d1-142">Remove the complex type</span></span>
-   <span data-ttu-id="f39d1-143">Actualice la importación de la función para que se asigne a las entidades; en nuestro caso, tendrá un aspecto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="f39d1-143">Update the function import so that it maps to your entities, in our case it will look like the following:</span></span>

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

<span data-ttu-id="f39d1-144">Esto indica al modelo que el procedimiento almacenado devolverá dos colecciones, una de las entradas del blog y una de las entradas post.</span><span class="sxs-lookup"><span data-stu-id="f39d1-144">This tells the model that the stored procedure will return two collections, one of blog entries and one of post entries.</span></span>

-   <span data-ttu-id="f39d1-145">Busque el elemento de asignación de función:</span><span class="sxs-lookup"><span data-stu-id="f39d1-145">Find the function mapping element:</span></span>

``` xml
    <!-- C-S mapping content -->
    <edmx:Mappings>

    ...

      <FunctionImportMapping FunctionImportName="GetAllBlogsAndPosts" FunctionName="BlogModel.Store.GetAllBlogsAndPosts">
        <ResultMapping>
          <ComplexTypeMapping TypeName="BlogModel.GetAllBlogsAndPosts_Result">
            <ScalarProperty Name="BlogId" ColumnName="BlogId" />
            <ScalarProperty Name="Name" ColumnName="Name" />
            <ScalarProperty Name="Description" ColumnName="Description" />
          </ComplexTypeMapping>
        </ResultMapping>
      </FunctionImportMapping>

    ...

    </edmx:Mappings>
```

-   <span data-ttu-id="f39d1-146">Reemplace la asignación de resultados por una para cada entidad que se va a devolver, como la siguiente:</span><span class="sxs-lookup"><span data-stu-id="f39d1-146">Replace the result mapping with one for each entity being returned, such as the following:</span></span>

``` xml
    <ResultMapping>
      <EntityTypeMapping TypeName ="BlogModel.Blog">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="Name" ColumnName="Name" />
        <ScalarProperty Name="Description" ColumnName="Description" />
      </EntityTypeMapping>
    </ResultMapping>
    <ResultMapping>
      <EntityTypeMapping TypeName="BlogModel.Post">
        <ScalarProperty Name="BlogId" ColumnName="BlogId" />
        <ScalarProperty Name="PostId" ColumnName="PostId"/>
        <ScalarProperty Name="Title" ColumnName="Title" />
        <ScalarProperty Name="Text" ColumnName="Text" />
      </EntityTypeMapping>
    </ResultMapping>
```

<span data-ttu-id="f39d1-147">También es posible asignar los conjuntos de resultados a tipos complejos, como el que se crea de forma predeterminada.</span><span class="sxs-lookup"><span data-stu-id="f39d1-147">It is also possible to map the result sets to complex types, such as the one created by default.</span></span> <span data-ttu-id="f39d1-148">Para ello, cree un nuevo tipo complejo, en lugar de quitarlos, y use los tipos complejos en todos los lugares en los que haya usado los nombres de entidad en los ejemplos anteriores.</span><span class="sxs-lookup"><span data-stu-id="f39d1-148">To do this you create a new complex type, instead of removing them, and use the complex types everywhere that you had used the entity names in the examples above.</span></span>

<span data-ttu-id="f39d1-149">Una vez que se hayan cambiado estas asignaciones, puede guardar el modelo y ejecutar el código siguiente para usar el procedimiento almacenado:</span><span class="sxs-lookup"><span data-stu-id="f39d1-149">Once these mappings have been changed then you can save the model and execute the following code to use the stored procedure:</span></span>

``` csharp
    using (var db = new BlogEntities())
    {
        var results = db.GetAllBlogsAndPosts();

        foreach (var result in results)
        {
            Console.WriteLine("Blog: " + result.Name);
        }

        var posts = results.GetNextResult<Post>();

        foreach (var result in posts)
        {
            Console.WriteLine("Post: " + result.Title);
        }

        Console.ReadLine();
    }
```

>[!NOTE]
> <span data-ttu-id="f39d1-150">Si edita manualmente el archivo edmx para el modelo, se sobrescribirá si alguna vez vuelve a generar el modelo desde la base de datos.</span><span class="sxs-lookup"><span data-stu-id="f39d1-150">If you manually edit the edmx file for your model it will be overwritten if you ever regenerate the model from the database.</span></span>

## <a name="summary"></a><span data-ttu-id="f39d1-151">Resumen</span><span class="sxs-lookup"><span data-stu-id="f39d1-151">Summary</span></span>

<span data-ttu-id="f39d1-152">Aquí hemos mostrado dos métodos diferentes para tener acceso a varios conjuntos de resultados mediante Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="f39d1-152">Here we have shown two different methods of accessing multiple result sets using Entity Framework.</span></span> <span data-ttu-id="f39d1-153">Ambos son igualmente válidos en función de su situación y preferencias, y debe elegir el que mejor se adapte a sus circunstancias.</span><span class="sxs-lookup"><span data-stu-id="f39d1-153">Both of them are equally valid depending on your situation and preferences and you should choose the one that seems best for your circumstances.</span></span> <span data-ttu-id="f39d1-154">Se prevé que la compatibilidad con varios conjuntos de resultados se mejorará en versiones futuras de Entity Framework y que ya no será necesario realizar los pasos descritos en este documento.</span><span class="sxs-lookup"><span data-stu-id="f39d1-154">It is planned that the support for multiple result sets will be improved in future versions of Entity Framework and that performing the steps in this document will no longer be necessary.</span></span>
