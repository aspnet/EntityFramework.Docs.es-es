---
title: 'Procedimientos almacenados con varios conjuntos de resultados: EF6'
description: Procedimientos almacenados con varios conjuntos de resultados en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/designer/advanced/multiple-result-sets
ms.openlocfilehash: 6b213b944ca06a7ca141746d86f1127be2fd7d87
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070229"
---
# <a name="stored-procedures-with-multiple-result-sets"></a>Procedimientos almacenados con varios conjuntos de resultados
A veces, al utilizar procedimientos almacenados, deberá devolver más de un conjunto de resultados. Este escenario se usa normalmente para reducir el número de recorridos de ida y vuelta de base de datos necesarios para crear una sola pantalla.Antes de EF5, Entity Framework permitiría que se llamara al procedimiento almacenado, pero solo devolvería el primer conjunto de resultados al código de llamada.

En este artículo se muestran dos formas de usar para tener acceso a más de un conjunto de resultados de un procedimiento almacenado en Entity Framework. Una que usa solo código y funciona con Code First y el diseñador de EF y otro que solo funciona con EF Designer. Las herramientas y la compatibilidad con API para esto deberían mejorar en versiones futuras de Entity Framework.

## <a name="model"></a>Modelado

En los ejemplos de este artículo se usa un blog básico y un modelo de publicaciones en el que un blog tiene muchas publicaciones y una publicación pertenece a un solo blog. Usaremos un procedimiento almacenado en la base de datos que devuelva todos los blogs y publicaciones, algo parecido a esto:

``` SQL
    CREATE PROCEDURE [dbo].[GetAllBlogsAndPosts]
    AS
        SELECT * FROM dbo.Blogs
        SELECT * FROM dbo.Posts
```

## <a name="accessing-multiple-result-sets-with-code"></a>Obtener acceso a varios conjuntos de resultados con código

Podemos ejecutar usar código para emitir un comando SQL sin formato para ejecutar nuestro procedimiento almacenado. La ventaja de este enfoque es que funciona tanto con Code First como con el diseñador EF.

Para conseguir que funcionen varios conjuntos de resultados, es necesario colocarlos en la API de ObjectContext mediante la interfaz IObjectContextAdapter.

Una vez que tenemos un ObjectContext, podemos usar el método translate para traducir los resultados de nuestro procedimiento almacenado en entidades que se pueden seguir y usar en EF como normal. En el ejemplo de código siguiente se muestra esto en acción.

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

El método translate acepta el lector que se ha recibido cuando se ejecuta el procedimiento, un nombre de EntitySet y un MergeOption. El nombre de EntitySet será el mismo que el de la propiedad DbSet en el contexto derivado. La enumeración MergeOption controla cómo se administran los resultados si la misma entidad ya existe en la memoria.

Aquí se recorre en iteración la colección de blogs antes de que se llame a NextResult, lo que es importante con el código anterior, ya que se debe consumir el primer conjunto de resultados antes de pasar al siguiente conjunto de resultados.

Una vez que se llama a los dos métodos de traducción, EF realiza el seguimiento de las entidades blog y post de la misma manera que cualquier otra entidad, por lo que se puede modificar o eliminar y guardar como normal.

>[!NOTE]
> EF no tiene en cuenta ninguna asignación al crear entidades mediante el método translate. Simplemente coincidentes con los nombres de columna del conjunto de resultados con los nombres de propiedad de las clases.

>[!NOTE]
> Si tiene habilitada la carga diferida, el acceso a la propiedad postes en una de las entidades del blog, EF se conectará a la base de datos para cargar de forma diferida todas las publicaciones, aunque ya se hayan cargado todas. Esto se debe a que EF no puede saber si ha cargado o no todas las publicaciones o si hay más en la base de datos. Si desea evitar esto, tendrá que deshabilitar la carga diferida.

## <a name="multiple-result-sets-with-configured-in-edmx"></a>Varios conjuntos de resultados con configurado en EDMX

>[!NOTE]
> Debe tener como destino .NET Framework 4,5 para poder configurar varios conjuntos de resultados en EDMX. Si el destino es .NET 4,0, puede usar el método basado en código que se muestra en la sección anterior.

Si usa el diseñador de EF, también puede modificar el modelo para que conozca los diferentes conjuntos de resultados que se devolverán. Lo que hay que saber antes de la mano es que las herramientas no tienen un conjunto de resultados múltiple, por lo que tendrá que editar manualmente el archivo edmx. La edición del archivo edmx como este funcionará, pero también interrumpirá la validación del modelo en VS. Por lo tanto, si valida el modelo, siempre se producirán errores.

-   Para ello, debe agregar el procedimiento almacenado al modelo como lo haría para una sola consulta de conjunto de resultados.
-   Una vez hecho esto, debe hacer clic con el botón derecho en el modelo y seleccionar **abrir con.** a continuación, **XML**

    ![Abrir como](~/ef6/media/openas.png)

Una vez que tenga el modelo abierto como XML, debe realizar los siguientes pasos:

-   Busque el tipo complejo y la importación de función en el modelo:

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

 

-   Quitar el tipo complejo
-   Actualice la importación de la función para que se asigne a las entidades; en nuestro caso, tendrá un aspecto similar al siguiente:

``` xml
    <FunctionImport Name="GetAllBlogsAndPosts">
      <ReturnType EntitySet="Blogs" Type="Collection(BlogModel.Blog)" />
      <ReturnType EntitySet="Posts" Type="Collection(BlogModel.Post)" />
    </FunctionImport>
```

Esto indica al modelo que el procedimiento almacenado devolverá dos colecciones, una de las entradas del blog y una de las entradas post.

-   Busque el elemento de asignación de función:

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

-   Reemplace la asignación de resultados por una para cada entidad que se va a devolver, como la siguiente:

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

También es posible asignar los conjuntos de resultados a tipos complejos, como el que se crea de forma predeterminada. Para ello, cree un nuevo tipo complejo, en lugar de quitarlos, y use los tipos complejos en todos los lugares en los que haya usado los nombres de entidad en los ejemplos anteriores.

Una vez que se hayan cambiado estas asignaciones, puede guardar el modelo y ejecutar el código siguiente para usar el procedimiento almacenado:

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
> Si edita manualmente el archivo edmx para el modelo, se sobrescribirá si alguna vez vuelve a generar el modelo desde la base de datos.

## <a name="summary"></a>Resumen

Aquí hemos mostrado dos métodos diferentes para tener acceso a varios conjuntos de resultados mediante Entity Framework. Ambos son igualmente válidos en función de su situación y preferencias, y debe elegir el que mejor se adapte a sus circunstancias. Se prevé que la compatibilidad con varios conjuntos de resultados se mejorará en versiones futuras de Entity Framework y que ya no será necesario realizar los pasos descritos en este documento.
