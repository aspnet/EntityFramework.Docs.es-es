---
title: 'Consultas SQL sin procesar: EF6'
description: Consultas SQL sin procesar en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/querying/raw-sql
ms.openlocfilehash: 30fab3029d71fb560493af279231e46828aa43d4
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073891"
---
# <a name="raw-sql-queries-ef6"></a><span data-ttu-id="38364-103">Consultas SQL sin formato (EF6)</span><span class="sxs-lookup"><span data-stu-id="38364-103">Raw SQL Queries (EF6)</span></span>

<span data-ttu-id="38364-104">Entity Framework permite realizar consultas con LINQ con las clases de entidad.</span><span class="sxs-lookup"><span data-stu-id="38364-104">Entity Framework allows you to query using LINQ with your entity classes.</span></span> <span data-ttu-id="38364-105">Sin embargo, puede haber ocasiones en las que desee ejecutar consultas utilizando SQL sin formato directamente en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="38364-105">However, there may be times that you want to run queries using raw SQL directly against the database.</span></span> <span data-ttu-id="38364-106">Esto incluye llamar a procedimientos almacenados, que pueden ser útiles para Code First modelos que actualmente no admiten la asignación a procedimientos almacenados.</span><span class="sxs-lookup"><span data-stu-id="38364-106">This includes calling stored procedures, which can be helpful for Code First models that currently do not support mapping to stored procedures.</span></span> <span data-ttu-id="38364-107">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="38364-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="writing-sql-queries-for-entities"></a><span data-ttu-id="38364-108">Escribir consultas SQL para entidades</span><span class="sxs-lookup"><span data-stu-id="38364-108">Writing SQL queries for entities</span></span>  

<span data-ttu-id="38364-109">El método SqlQuery en DbSet permite escribir una consulta SQL sin formato que devolverá las instancias de la entidad.</span><span class="sxs-lookup"><span data-stu-id="38364-109">The SqlQuery method on DbSet allows a raw SQL query to be written that will return entity instances.</span></span> <span data-ttu-id="38364-110">El contexto realizará un seguimiento de los objetos devueltos tal como lo haría si se devolvieran mediante una consulta LINQ.</span><span class="sxs-lookup"><span data-stu-id="38364-110">The returned objects will be tracked by the context just as they would be if they were returned by a LINQ query.</span></span> <span data-ttu-id="38364-111">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="38364-111">For example:</span></span>  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="38364-112">Tenga en cuenta que, al igual que para las consultas LINQ, la consulta no se ejecuta hasta que se enumeran los resultados; en el ejemplo anterior, esto se realiza con la llamada a ToList.</span><span class="sxs-lookup"><span data-stu-id="38364-112">Note that, just as for LINQ queries, the query is not executed until the results are enumerated—in the example above this is done with the call to ToList.</span></span>  

<span data-ttu-id="38364-113">Se debe tener cuidado cuando las consultas SQL sin procesar se escriben por dos motivos.</span><span class="sxs-lookup"><span data-stu-id="38364-113">Care should be taken whenever raw SQL queries are written for two reasons.</span></span> <span data-ttu-id="38364-114">En primer lugar, se debe escribir la consulta para asegurarse de que solo devuelve entidades que son realmente del tipo solicitado.</span><span class="sxs-lookup"><span data-stu-id="38364-114">First, the query should be written to ensure that it only returns entities that are really of the requested type.</span></span> <span data-ttu-id="38364-115">Por ejemplo, al usar características como la herencia, es fácil escribir una consulta que creará entidades que son del tipo CLR incorrecto.</span><span class="sxs-lookup"><span data-stu-id="38364-115">For example, when using features such as inheritance it is easy to write a query that will create entities that are of the wrong CLR type.</span></span>  

<span data-ttu-id="38364-116">En segundo lugar, algunos tipos de consultas SQL sin procesar exponen posibles riesgos de seguridad, especialmente en torno a ataques por inyección de SQL.</span><span class="sxs-lookup"><span data-stu-id="38364-116">Second, some types of raw SQL query expose potential security risks, especially around SQL injection attacks.</span></span> <span data-ttu-id="38364-117">Asegúrese de usar los parámetros de la consulta de la manera correcta para protegerse frente a estos ataques.</span><span class="sxs-lookup"><span data-stu-id="38364-117">Make sure that you use parameters in your query in the correct way to guard against such attacks.</span></span>  

### <a name="loading-entities-from-stored-procedures"></a><span data-ttu-id="38364-118">Cargar entidades desde procedimientos almacenados</span><span class="sxs-lookup"><span data-stu-id="38364-118">Loading entities from stored procedures</span></span>  

<span data-ttu-id="38364-119">Puede usar DbSet. SqlQuery para cargar entidades de los resultados de un procedimiento almacenado.</span><span class="sxs-lookup"><span data-stu-id="38364-119">You can use DbSet.SqlQuery to load entities from the results of a stored procedure.</span></span> <span data-ttu-id="38364-120">Por ejemplo, el código siguiente llama a DBO. Procedimiento GetBlogs en la base de datos:</span><span class="sxs-lookup"><span data-stu-id="38364-120">For example, the following code calls the dbo.GetBlogs procedure in the database:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

<span data-ttu-id="38364-121">También puede pasar parámetros a un procedimiento almacenado con la sintaxis siguiente:</span><span class="sxs-lookup"><span data-stu-id="38364-121">You can also pass parameters to a stored procedure using the following syntax:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a><span data-ttu-id="38364-122">Escribir consultas SQL para tipos que no son de entidad</span><span class="sxs-lookup"><span data-stu-id="38364-122">Writing SQL queries for non-entity types</span></span>  

<span data-ttu-id="38364-123">Una consulta SQL que devuelve instancias de cualquier tipo, incluidos los tipos primitivos, se puede crear con el método SqlQuery en la clase de base de datos.</span><span class="sxs-lookup"><span data-stu-id="38364-123">A SQL query returning instances of any type, including primitive types, can be created using the SqlQuery method on the Database class.</span></span> <span data-ttu-id="38364-124">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="38364-124">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

<span data-ttu-id="38364-125">Nunca se realizará el seguimiento de los resultados devueltos de SqlQuery en la base de datos en el contexto, incluso si los objetos son instancias de un tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="38364-125">The results returned from SqlQuery on Database will never be tracked by the context even if the objects are instances of an entity type.</span></span>  

## <a name="sending-raw-commands-to-the-database"></a><span data-ttu-id="38364-126">Enviar comandos sin formato a la base de datos</span><span class="sxs-lookup"><span data-stu-id="38364-126">Sending raw commands to the database</span></span>  

<span data-ttu-id="38364-127">Los comandos que no son de consulta se pueden enviar a la base de datos mediante el método ExecuteSqlCommand en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="38364-127">Non-query commands can be sent to the database using the ExecuteSqlCommand method on Database.</span></span> <span data-ttu-id="38364-128">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="38364-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

<span data-ttu-id="38364-129">Tenga en cuenta que los cambios realizados en los datos de la base de datos mediante ExecuteSqlCommand son opacos en el contexto hasta que las entidades se cargan o se vuelven a cargar desde la base de datos.</span><span class="sxs-lookup"><span data-stu-id="38364-129">Note that any changes made to data in the database using ExecuteSqlCommand are opaque to the context until entities are loaded or reloaded from the database.</span></span>  

### <a name="output-parameters"></a><span data-ttu-id="38364-130">Parámetros de salida</span><span class="sxs-lookup"><span data-stu-id="38364-130">Output Parameters</span></span>  

<span data-ttu-id="38364-131">Si se utilizan parámetros de salida, sus valores no estarán disponibles hasta que los resultados se hayan leído por completo.</span><span class="sxs-lookup"><span data-stu-id="38364-131">If output parameters are used, their values will not be available until the results have been read completely.</span></span> <span data-ttu-id="38364-132">Esto se debe al comportamiento subyacente de DbDataReader, consulte [recuperación de datos mediante un DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) para obtener más detalles.</span><span class="sxs-lookup"><span data-stu-id="38364-132">This is due to the underlying behavior of DbDataReader, see [Retrieving Data Using a DataReader](https://go.microsoft.com/fwlink/?LinkID=398589) for more details.</span></span>  
