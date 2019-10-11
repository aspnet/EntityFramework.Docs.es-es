---
title: 'Pruebas con un marco ficticio: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
ms.openlocfilehash: 790e077c5b30c4a68a96b3c1a99b40893b2bbe55
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181574"
---
# <a name="testing-with-a-mocking-framework"></a><span data-ttu-id="3ab6c-102">Probar con un marco ficticio</span><span class="sxs-lookup"><span data-stu-id="3ab6c-102">Testing with a mocking framework</span></span>
> [!NOTE]
> <span data-ttu-id="3ab6c-103">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="3ab6c-104">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="3ab6c-105">Al escribir pruebas para la aplicación, a menudo es conveniente evitar la llegada de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-105">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="3ab6c-106">Entity Framework le permite conseguirlo mediante la creación de un contexto, con el comportamiento definido por las pruebas, que hace uso de los datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-106">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="3ab6c-107">Opciones para crear dobles de pruebas</span><span class="sxs-lookup"><span data-stu-id="3ab6c-107">Options for creating test doubles</span></span>  

<span data-ttu-id="3ab6c-108">Existen dos enfoques diferentes que se pueden usar para crear una versión en memoria del contexto.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-108">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="3ab6c-109">**Crear sus propios dobles de pruebas** : este enfoque implica escribir su propia implementación en memoria de su contexto y DbSets.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-109">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="3ab6c-110">Esto le ofrece un gran control sobre cómo se comportan las clases, pero puede implicar la escritura y la propiedad de una cantidad de código razonable.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-110">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="3ab6c-111">**Usar un marco ficticio para crear dobles de pruebas** : mediante un marco ficticio (como MOQ), puede tener las implementaciones en memoria del contexto y los conjuntos creados dinámicamente en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-111">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of your context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="3ab6c-112">En este artículo se tratará el uso de un marco ficticio.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-112">This article will deal with using a mocking framework.</span></span> <span data-ttu-id="3ab6c-113">Para crear sus propios dobles de pruebas, consulte [pruebas con los dobles de pruebas](writing-test-doubles.md).</span><span class="sxs-lookup"><span data-stu-id="3ab6c-113">For creating your own test doubles see [Testing with Your Own Test Doubles](writing-test-doubles.md).</span></span>  

<span data-ttu-id="3ab6c-114">Para demostrar el uso de EF con un marco ficticio, vamos a usar MOQ.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-114">To demonstrate using EF with a mocking framework we are going to use Moq.</span></span> <span data-ttu-id="3ab6c-115">La forma más fácil de obtener MOQ es instalar el [paquete MOQ desde NuGet](https://nuget.org/packages/Moq/).</span><span class="sxs-lookup"><span data-stu-id="3ab6c-115">The easiest way to get Moq is to install the [Moq package from NuGet](https://nuget.org/packages/Moq/).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="3ab6c-116">Pruebas con versiones anteriores a EF6</span><span class="sxs-lookup"><span data-stu-id="3ab6c-116">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="3ab6c-117">El escenario que se muestra en este artículo depende de algunos cambios realizados en DbSet en EF6.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-117">The scenario shown in this article is dependent on some changes we made to DbSet in EF6.</span></span> <span data-ttu-id="3ab6c-118">Para realizar pruebas con EF5 y versiones anteriores, consulte [pruebas con un contexto falso](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span><span class="sxs-lookup"><span data-stu-id="3ab6c-118">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="3ab6c-119">Limitaciones de los dobles de pruebas en memoria de EF</span><span class="sxs-lookup"><span data-stu-id="3ab6c-119">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="3ab6c-120">Los dobles de pruebas en memoria pueden ser una buena manera de proporcionar una cobertura de nivel de prueba unitaria de bits de la aplicación que usa EF.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-120">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="3ab6c-121">Sin embargo, al hacerlo, se usa LINQ to Objects para ejecutar consultas en los datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-121">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="3ab6c-122">Esto puede dar lugar a un comportamiento diferente al uso del proveedor LINQ (LINQ to Entities) de EF para traducir las consultas en SQL que se ejecutan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-122">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="3ab6c-123">Un ejemplo de este tipo de diferencia es la carga de datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-123">One example of such a difference is loading related data.</span></span> <span data-ttu-id="3ab6c-124">Si crea una serie de blogs en los que cada uno tiene elementos relacionados, al usar los datos en memoria, los envíos relacionados se cargarán siempre para cada blog.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-124">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="3ab6c-125">Sin embargo, cuando se ejecuta en una base de datos, los datos solo se cargarán si se usa el método include.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-125">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="3ab6c-126">Por esta razón, se recomienda incluir siempre cierto nivel de pruebas de un extremo a otro (además de las pruebas unitarias) para asegurarse de que la aplicación funciona correctamente en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-126">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="3ab6c-127">Junto con este artículo</span><span class="sxs-lookup"><span data-stu-id="3ab6c-127">Following along with this article</span></span>  

<span data-ttu-id="3ab6c-128">En este artículo se proporcionan listas de código completas que se pueden copiar en Visual Studio para que se realicen a continuación, si así se desea.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-128">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="3ab6c-129">Es más fácil crear un **proyecto de prueba unitaria** y tendrá que tener como destino **.NET Framework 4,5** para completar las secciones que usan Async.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-129">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="the-ef-model"></a><span data-ttu-id="3ab6c-130">El modelo EF</span><span class="sxs-lookup"><span data-stu-id="3ab6c-130">The EF model</span></span>  

<span data-ttu-id="3ab6c-131">El servicio que vamos a probar hace uso de un modelo EF compuesto por las clases BloggingContext y blog y post.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-131">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="3ab6c-132">Este código puede haber sido generado por EF Designer o ser un modelo de Code First.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-132">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext
    {
        public virtual DbSet<Blog> Blogs { get; set; }
        public virtual DbSet<Post> Posts { get; set; }
    }

    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }
        public string Url { get; set; }

        public virtual List<Post> Posts { get; set; }
    }

    public class Post
    {
        public int PostId { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }

        public int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
}
```  

### <a name="virtual-dbset-properties-with-ef-designer"></a><span data-ttu-id="3ab6c-133">Propiedades de DbSet virtuales con EF Designer</span><span class="sxs-lookup"><span data-stu-id="3ab6c-133">Virtual DbSet properties with EF Designer</span></span>  

<span data-ttu-id="3ab6c-134">Tenga en cuenta que las propiedades de DbSet en el contexto se marcan como virtuales.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-134">Note that the DbSet properties on the context are marked as virtual.</span></span> <span data-ttu-id="3ab6c-135">Esto permitirá que el marco ficticio se derive de nuestro contexto e invalide estas propiedades con una implementación ficticia.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-135">This will allow the mocking framework to derive from our context and overriding these properties with a mocked implementation.</span></span>  

<span data-ttu-id="3ab6c-136">Si usa Code First, puede editar las clases directamente.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-136">If you are using Code First then you can edit your classes directly.</span></span> <span data-ttu-id="3ab6c-137">Si usa el diseñador de EF, tendrá que editar la plantilla T4 que genera el contexto.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-137">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="3ab6c-138">Abra el @no__t 0model_name @ no__t-1. Archivo Context.tt que está anidado en el archivo edmx, busque el fragmento de código siguiente y agregue la palabra clave virtual como se muestra.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-138">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the virtual keyword as shown.</span></span>  

``` csharp
public string DbSet(EntitySet entitySet)
{
    return string.Format(
        CultureInfo.InvariantCulture,
        "{0} virtual DbSet\<{1}> {2} {{ get; set; }}",
        Accessibility.ForReadOnlyProperty(entitySet),
        _typeMapper.GetTypeName(entitySet.ElementType),
        _code.Escape(entitySet));
}
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="3ab6c-139">Servicio que se va a probar</span><span class="sxs-lookup"><span data-stu-id="3ab6c-139">Service to be tested</span></span>  

<span data-ttu-id="3ab6c-140">Para demostrar las pruebas con los dobles de pruebas en memoria, vamos a escribir un par de pruebas para un BlogService.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-140">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="3ab6c-141">El servicio es capaz de crear nuevos blogs (AddBlog) y devolver todos los blogs ordenados por nombre (GetAllBlogs).</span><span class="sxs-lookup"><span data-stu-id="3ab6c-141">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="3ab6c-142">Además de GetAllBlogs, también se proporciona un método que obtendrá de forma asincrónica todos los blogs ordenados por nombre (GetAllBlogsAsync).</span><span class="sxs-lookup"><span data-stu-id="3ab6c-142">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private BloggingContext _context;

        public BlogService(BloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = _context.Blogs.Add(new Blog { Name = name, Url = url });
            _context.SaveChanges();

            return blog;
        }

        public List<Blog> GetAllBlogs()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return query.ToList();
        }

        public async Task<List<Blog>> GetAllBlogsAsync()
        {
            var query = from b in _context.Blogs
                        orderby b.Name
                        select b;

            return await query.ToListAsync();
        }
    }
}
```  

## <a name="testing-non-query-scenarios"></a><span data-ttu-id="3ab6c-143">Probar escenarios que no son de consulta</span><span class="sxs-lookup"><span data-stu-id="3ab6c-143">Testing non-query scenarios</span></span>  

<span data-ttu-id="3ab6c-144">Eso es todo lo que necesitamos hacer para empezar a probar los métodos que no son de consulta.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-144">That’s all we need to do to start testing non-query methods.</span></span> <span data-ttu-id="3ab6c-145">La prueba siguiente usa MOQ para crear un contexto.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-145">The following test uses Moq to create a context.</span></span> <span data-ttu-id="3ab6c-146">Después crea un DbSet @ no__t-0Blog @ no__t-1 y lo conecta para que se devuelva desde la propiedad blogs del contexto.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-146">It then creates a DbSet\<Blog\> and wires it up to be returned from the context’s Blogs property.</span></span> <span data-ttu-id="3ab6c-147">Después, el contexto se usa para crear un nuevo BlogService que se usa para crear un nuevo blog: mediante el método AddBlog.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-147">Next, the context is used to create a new BlogService which is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="3ab6c-148">Por último, la prueba comprueba que el servicio agregó un nuevo blog y se llama SaveChanges en el contexto.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-148">Finally, the test verifies that the service added a new Blog and called SaveChanges on the context.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Data.Entity;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var mockSet = new Mock<DbSet<Blog>>();

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(m => m.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            mockSet.Verify(m => m.Add(It.IsAny<Blog>()), Times.Once());
            mockContext.Verify(m => m.SaveChanges(), Times.Once());
        }
    }
}
```  

## <a name="testing-query-scenarios"></a><span data-ttu-id="3ab6c-149">Probar escenarios de consulta</span><span class="sxs-lookup"><span data-stu-id="3ab6c-149">Testing query scenarios</span></span>  

<span data-ttu-id="3ab6c-150">Para poder ejecutar consultas en nuestra prueba de DbSet doble, es necesario configurar una implementación de IQueryable.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-150">In order to be able to execute queries against our DbSet test double we need to setup an implementation of IQueryable.</span></span> <span data-ttu-id="3ab6c-151">El primer paso es crear algunos datos en memoria: usamos List @ no__t-0Blog @ no__t-1.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-151">The first step is to create some in-memory data – we’re using a List\<Blog\>.</span></span> <span data-ttu-id="3ab6c-152">A continuación, creamos un contexto y DBSet @ no__t-0Blog @ no__t-1 y, a continuación, conectamos la implementación de IQueryable para el DbSet, simplemente delegamos en el LINQ to Objects proveedor que funciona con List @ no__t-2T @ no__t-3.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-152">Next, we create a context and DBSet\<Blog\> then wire up the IQueryable implementation for the DbSet – they’re just delegating to the LINQ to Objects provider that works with List\<T\>.</span></span>  

<span data-ttu-id="3ab6c-153">A continuación, podemos crear un BlogService basado en los dobles de pruebas y asegurarse de que los datos que se obtienen de GetAllBlogs se ordenan por nombre.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-153">We can then create a BlogService based on our test doubles and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Provider).Returns(data.Provider);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

### <a name="testing-with-async-queries"></a><span data-ttu-id="3ab6c-154">Pruebas con consultas asincrónicas</span><span class="sxs-lookup"><span data-stu-id="3ab6c-154">Testing with async queries</span></span>

<span data-ttu-id="3ab6c-155">Entity Framework 6 presentó un conjunto de métodos de extensión que se pueden usar para ejecutar una consulta de forma asincrónica.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-155">Entity Framework 6 introduced a set of extension methods that can be used to asynchronously execute a query.</span></span> <span data-ttu-id="3ab6c-156">Entre los ejemplos de estos métodos se incluyen ToListAsync, FirstAsync, ForEachAsync, etc.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-156">Examples of these methods include ToListAsync, FirstAsync, ForEachAsync, etc.</span></span>  

<span data-ttu-id="3ab6c-157">Dado que Entity Framework consultas usan LINQ, los métodos de extensión se definen en IQueryable y IEnumerable.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-157">Because Entity Framework queries make use of LINQ, the extension methods are defined on IQueryable and IEnumerable.</span></span> <span data-ttu-id="3ab6c-158">Sin embargo, dado que solo están diseñados para usarse con Entity Framework puede recibir el siguiente error si intenta utilizarlos en una consulta LINQ que no es una consulta de Entity Framework:</span><span class="sxs-lookup"><span data-stu-id="3ab6c-158">However, because they are only designed to be used with Entity Framework you may receive the following error if you try to use them on a LINQ query that isn’t an Entity Framework query:</span></span>

> <span data-ttu-id="3ab6c-159">IQueryable de origen no implementa IDbAsyncEnumerable @ no__t-0.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-159">The source IQueryable doesn't implement IDbAsyncEnumerable{0}.</span></span> <span data-ttu-id="3ab6c-160">Solo los orígenes que implementan IDbAsyncEnumerable se pueden usar para las operaciones asincrónicas de Entity Framework.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-160">Only sources that implement IDbAsyncEnumerable can be used for Entity Framework asynchronous operations.</span></span> <span data-ttu-id="3ab6c-161">Para obtener más información [, vea http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068).</span><span class="sxs-lookup"><span data-stu-id="3ab6c-161">For more details see [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068).</span></span>  

<span data-ttu-id="3ab6c-162">Mientras que los métodos asincrónicos solo se admiten cuando se ejecuta en una consulta EF, puede que desee usarlos en la prueba unitaria cuando se ejecuta en una prueba en memoria Double de un DbSet.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-162">Whilst the async methods are only supported when running against an EF query, you may want to use them in your unit test when running against an in-memory test double of a DbSet.</span></span>  

<span data-ttu-id="3ab6c-163">Para utilizar los métodos asincrónicos, es necesario crear un DbAsyncQueryProvider en memoria para procesar la consulta asincrónica.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-163">In order to use the async methods we need to create an in-memory DbAsyncQueryProvider to process the async query.</span></span> <span data-ttu-id="3ab6c-164">Aunque sería posible configurar un proveedor de consultas mediante MOQ, es mucho más fácil crear una implementación de prueba Double en el código.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-164">Whilst it would be possible to setup a query provider using Moq, it is much easier to create a test double implementation in code.</span></span> <span data-ttu-id="3ab6c-165">El código para esta implementación es el siguiente:</span><span class="sxs-lookup"><span data-stu-id="3ab6c-165">The code for this implementation is as follows:</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
    internal class TestDbAsyncQueryProvider<TEntity> : IDbAsyncQueryProvider
    {
        private readonly IQueryProvider _inner;

        internal TestDbAsyncQueryProvider(IQueryProvider inner)
        {
            _inner = inner;
        }

        public IQueryable CreateQuery(Expression expression)
        {
            return new TestDbAsyncEnumerable<TEntity>(expression);
        }

        public IQueryable<TElement> CreateQuery<TElement>(Expression expression)
        {
            return new TestDbAsyncEnumerable<TElement>(expression);
        }

        public object Execute(Expression expression)
        {
            return _inner.Execute(expression);
        }

        public TResult Execute<TResult>(Expression expression)
        {
            return _inner.Execute<TResult>(expression);
        }

        public Task<object> ExecuteAsync(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute(expression));
        }

        public Task<TResult> ExecuteAsync<TResult>(Expression expression, CancellationToken cancellationToken)
        {
            return Task.FromResult(Execute<TResult>(expression));
        }
    }

    internal class TestDbAsyncEnumerable<T> : EnumerableQuery<T>, IDbAsyncEnumerable<T>, IQueryable<T>
    {
        public TestDbAsyncEnumerable(IEnumerable<T> enumerable)
            : base(enumerable)
        { }

        public TestDbAsyncEnumerable(Expression expression)
            : base(expression)
        { }

        public IDbAsyncEnumerator<T> GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<T>(this.AsEnumerable().GetEnumerator());
        }

        IDbAsyncEnumerator IDbAsyncEnumerable.GetAsyncEnumerator()
        {
            return GetAsyncEnumerator();
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<T>(this); }
        }
    }

    internal class TestDbAsyncEnumerator<T> : IDbAsyncEnumerator<T>
    {
        private readonly IEnumerator<T> _inner;

        public TestDbAsyncEnumerator(IEnumerator<T> inner)
        {
            _inner = inner;
        }

        public void Dispose()
        {
            _inner.Dispose();
        }

        public Task<bool> MoveNextAsync(CancellationToken cancellationToken)
        {
            return Task.FromResult(_inner.MoveNext());
        }

        public T Current
        {
            get { return _inner.Current; }
        }

        object IDbAsyncEnumerator.Current
        {
            get { return Current; }
        }
    }
}
```  

<span data-ttu-id="3ab6c-166">Ahora que tenemos un proveedor de consultas asincrónicas, podemos escribir una prueba unitaria para el nuevo método GetAllBlogsAsync.</span><span class="sxs-lookup"><span data-stu-id="3ab6c-166">Now that we have an async query provider we can write a unit test for our new GetAllBlogsAsync method.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using System.Collections.Generic;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    [TestClass]
    public class AsyncQueryTests
    {
        [TestMethod]
        public async Task GetAllBlogsAsync_orders_by_name()
        {

            var data = new List<Blog>
            {
                new Blog { Name = "BBB" },
                new Blog { Name = "ZZZ" },
                new Blog { Name = "AAA" },
            }.AsQueryable();

            var mockSet = new Mock<DbSet<Blog>>();
            mockSet.As<IDbAsyncEnumerable<Blog>>()
                .Setup(m => m.GetAsyncEnumerator())
                .Returns(new TestDbAsyncEnumerator<Blog>(data.GetEnumerator()));

            mockSet.As<IQueryable<Blog>>()
                .Setup(m => m.Provider)
                .Returns(new TestDbAsyncQueryProvider<Blog>(data.Provider));

            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType);
            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator());

            var mockContext = new Mock<BloggingContext>();
            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object);

            var service = new BlogService(mockContext.Object);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  
