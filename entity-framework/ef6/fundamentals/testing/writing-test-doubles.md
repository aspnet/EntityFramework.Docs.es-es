---
title: Pruebas con sus propias pruebas dobles-EF6
description: Las pruebas con su propia prueba se duplican en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 16a8b7c0-2d23-47f4-9cc0-e2eb2e738ca3
uid: ef6/fundamentals/testing/writing-test-doubles
ms.openlocfilehash: 5c8e50247c0e6ed4081e2403ba60e0738051312b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618234"
---
# <a name="testing-with-your-own-test-doubles"></a><span data-ttu-id="8e68e-103">Pruebas con sus propias dobles de pruebas</span><span class="sxs-lookup"><span data-stu-id="8e68e-103">Testing with your own test doubles</span></span>
> [!NOTE]
> <span data-ttu-id="8e68e-104">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="8e68e-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="8e68e-105">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="8e68e-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="8e68e-106">Al escribir pruebas para la aplicación, a menudo es conveniente evitar la llegada de la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8e68e-106">When writing tests for your application it is often desirable to avoid hitting the database.</span></span>  <span data-ttu-id="8e68e-107">Entity Framework le permite conseguirlo mediante la creación de un contexto, con el comportamiento definido por las pruebas, que hace uso de los datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="8e68e-107">Entity Framework allows you to achieve this by creating a context – with behavior defined by your tests – that makes use of in-memory data.</span></span>  

## <a name="options-for-creating-test-doubles"></a><span data-ttu-id="8e68e-108">Opciones para crear dobles de pruebas</span><span class="sxs-lookup"><span data-stu-id="8e68e-108">Options for creating test doubles</span></span>  

<span data-ttu-id="8e68e-109">Existen dos enfoques diferentes que se pueden usar para crear una versión en memoria del contexto.</span><span class="sxs-lookup"><span data-stu-id="8e68e-109">There are two different approaches that can be used to create an in-memory version of your context.</span></span>  

- <span data-ttu-id="8e68e-110">**Crear sus propios dobles de pruebas** : este enfoque implica escribir su propia implementación en memoria de su contexto y DbSets.</span><span class="sxs-lookup"><span data-stu-id="8e68e-110">**Create your own test doubles** – This approach involves writing your own in-memory implementation of your context and DbSets.</span></span> <span data-ttu-id="8e68e-111">Esto le ofrece un gran control sobre cómo se comportan las clases, pero puede implicar la escritura y la propiedad de una cantidad de código razonable.</span><span class="sxs-lookup"><span data-stu-id="8e68e-111">This gives you a lot of control over how the classes behave but can involve writing and owning a reasonable amount of code.</span></span>  
- <span data-ttu-id="8e68e-112">**Usar un marco ficticio para crear dobles de pruebas** : mediante un marco ficticio (como MOQ), puede tener las implementaciones en memoria de contexto y conjuntos creados dinámicamente en tiempo de ejecución.</span><span class="sxs-lookup"><span data-stu-id="8e68e-112">**Use a mocking framework to create test doubles** – Using a mocking framework (such as Moq) you can have the in-memory implementations of you context and sets created dynamically at runtime for you.</span></span>  

<span data-ttu-id="8e68e-113">En este artículo se tratará la creación de su propio Double de prueba.</span><span class="sxs-lookup"><span data-stu-id="8e68e-113">This article will deal with creating your own test double.</span></span> <span data-ttu-id="8e68e-114">Para obtener información sobre el uso de un marco ficticio, vea [probar con un marco ficticio](xref:ef6/fundamentals/testing/mocking).</span><span class="sxs-lookup"><span data-stu-id="8e68e-114">For information on using a mocking framework see [Testing with a Mocking Framework](xref:ef6/fundamentals/testing/mocking).</span></span>  

## <a name="testing-with-pre-ef6-versions"></a><span data-ttu-id="8e68e-115">Pruebas con versiones anteriores a EF6</span><span class="sxs-lookup"><span data-stu-id="8e68e-115">Testing with pre-EF6 versions</span></span>  

<span data-ttu-id="8e68e-116">El código que se muestra en este artículo es compatible con EF6.</span><span class="sxs-lookup"><span data-stu-id="8e68e-116">The code shown in this article is compatible with EF6.</span></span> <span data-ttu-id="8e68e-117">Para realizar pruebas con EF5 y versiones anteriores, consulte [pruebas con un contexto falso](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span><span class="sxs-lookup"><span data-stu-id="8e68e-117">For testing with EF5 and earlier version see [Testing with a Fake Context](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).</span></span>  

## <a name="limitations-of-ef-in-memory-test-doubles"></a><span data-ttu-id="8e68e-118">Limitaciones de los dobles de pruebas en memoria de EF</span><span class="sxs-lookup"><span data-stu-id="8e68e-118">Limitations of EF in-memory test doubles</span></span>  

<span data-ttu-id="8e68e-119">Los dobles de pruebas en memoria pueden ser una buena manera de proporcionar una cobertura de nivel de prueba unitaria de bits de la aplicación que usa EF.</span><span class="sxs-lookup"><span data-stu-id="8e68e-119">In-memory test doubles can be a good way to provide unit test level coverage of bits of your application that use EF.</span></span> <span data-ttu-id="8e68e-120">Sin embargo, al hacerlo, se usa LINQ to Objects para ejecutar consultas en los datos en memoria.</span><span class="sxs-lookup"><span data-stu-id="8e68e-120">However, when doing this you are using LINQ to Objects to execute queries against in-memory data.</span></span> <span data-ttu-id="8e68e-121">Esto puede dar lugar a un comportamiento diferente al uso del proveedor LINQ (LINQ to Entities) de EF para traducir las consultas en SQL que se ejecutan en la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8e68e-121">This can result in different behavior than using EF’s LINQ provider (LINQ to Entities) to translate queries into SQL that is run against your database.</span></span>  

<span data-ttu-id="8e68e-122">Un ejemplo de este tipo de diferencia es la carga de datos relacionados.</span><span class="sxs-lookup"><span data-stu-id="8e68e-122">One example of such a difference is loading related data.</span></span> <span data-ttu-id="8e68e-123">Si crea una serie de blogs en los que cada uno tiene elementos relacionados, al usar los datos en memoria, los envíos relacionados se cargarán siempre para cada blog.</span><span class="sxs-lookup"><span data-stu-id="8e68e-123">If you create a series of Blogs that each have related Posts, then when using in-memory data the related Posts will always be loaded for each Blog.</span></span> <span data-ttu-id="8e68e-124">Sin embargo, cuando se ejecuta en una base de datos, los datos solo se cargarán si se usa el método include.</span><span class="sxs-lookup"><span data-stu-id="8e68e-124">However, when running against a database the data will only be loaded if you use the Include method.</span></span>  

<span data-ttu-id="8e68e-125">Por esta razón, se recomienda incluir siempre cierto nivel de pruebas de un extremo a otro (además de las pruebas unitarias) para asegurarse de que la aplicación funciona correctamente en una base de datos.</span><span class="sxs-lookup"><span data-stu-id="8e68e-125">For this reason, it is recommended to always include some level of end-to-end testing (in addition to your unit tests) to ensure your application works correctly against a database.</span></span>  

## <a name="following-along-with-this-article"></a><span data-ttu-id="8e68e-126">Junto con este artículo</span><span class="sxs-lookup"><span data-stu-id="8e68e-126">Following along with this article</span></span>  

<span data-ttu-id="8e68e-127">En este artículo se proporcionan listas de código completas que se pueden copiar en Visual Studio para que se realicen a continuación, si así se desea.</span><span class="sxs-lookup"><span data-stu-id="8e68e-127">This article gives complete code listings that you can copy into Visual Studio to follow along if you wish.</span></span> <span data-ttu-id="8e68e-128">Es más fácil crear un **proyecto de prueba unitaria** y tendrá que tener como destino **.NET Framework 4,5** para completar las secciones que usan Async.</span><span class="sxs-lookup"><span data-stu-id="8e68e-128">It's easiest to create a **Unit Test Project** and you will need to target **.NET Framework 4.5** to complete the sections that use async.</span></span>  

## <a name="creating-a-context-interface"></a><span data-ttu-id="8e68e-129">Crear una interfaz de contexto</span><span class="sxs-lookup"><span data-stu-id="8e68e-129">Creating a context interface</span></span>  

<span data-ttu-id="8e68e-130">Vamos a echar un vistazo a la prueba de un servicio que hace uso de un modelo EF.</span><span class="sxs-lookup"><span data-stu-id="8e68e-130">We're going to look at testing a service that makes use of an EF model.</span></span> <span data-ttu-id="8e68e-131">Para poder reemplazar el contexto de EF con una versión en memoria para las pruebas, vamos a definir una interfaz que implementará el contexto de EF (y el valor Double en memoria).</span><span class="sxs-lookup"><span data-stu-id="8e68e-131">In order to be able to replace our EF context with an in-memory version for testing, we'll define an interface that our EF context (and it's in-memory double) will implement.</span></span>

<span data-ttu-id="8e68e-132">El servicio que se va a probar consultará y modificará los datos mediante las propiedades DbSet de nuestro contexto y también llamará a SaveChanges para enviar los cambios a la base de datos.</span><span class="sxs-lookup"><span data-stu-id="8e68e-132">The service we are going to test will query and modify data using the DbSet properties of our context and also call SaveChanges to push changes to the database.</span></span> <span data-ttu-id="8e68e-133">Por tanto, vamos a incluir estos miembros en la interfaz.</span><span class="sxs-lookup"><span data-stu-id="8e68e-133">So we're including these members on the interface.</span></span>  

``` csharp
using System.Data.Entity;

namespace TestingDemo
{
    public interface IBloggingContext
    {
        DbSet<Blog> Blogs { get; }
        DbSet<Post> Posts { get; }
        int SaveChanges();
    }
}
```  

## <a name="the-ef-model"></a><span data-ttu-id="8e68e-134">El modelo EF</span><span class="sxs-lookup"><span data-stu-id="8e68e-134">The EF model</span></span>  

<span data-ttu-id="8e68e-135">El servicio que vamos a probar hace uso de un modelo EF compuesto por las clases BloggingContext y blog y post.</span><span class="sxs-lookup"><span data-stu-id="8e68e-135">The service we're going to test makes use of an EF model made up of the BloggingContext and the Blog and Post classes.</span></span> <span data-ttu-id="8e68e-136">Este código puede haber sido generado por EF Designer o ser un modelo de Code First.</span><span class="sxs-lookup"><span data-stu-id="8e68e-136">This code may have been generated by the EF Designer or be a Code First model.</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;

namespace TestingDemo
{
    public class BloggingContext : DbContext, IBloggingContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a><span data-ttu-id="8e68e-137">Implementar la interfaz de contexto con EF Designer</span><span class="sxs-lookup"><span data-stu-id="8e68e-137">Implementing the context interface with the EF Designer</span></span>  

<span data-ttu-id="8e68e-138">Tenga en cuenta que nuestro contexto implementa la interfaz IBloggingContext.</span><span class="sxs-lookup"><span data-stu-id="8e68e-138">Note that our context implements the IBloggingContext interface.</span></span>  

<span data-ttu-id="8e68e-139">Si usa Code First, puede modificar el contexto directamente para implementar la interfaz.</span><span class="sxs-lookup"><span data-stu-id="8e68e-139">If you are using Code First then you can edit your context directly to implement the interface.</span></span> <span data-ttu-id="8e68e-140">Si usa el diseñador de EF, tendrá que editar la plantilla T4 que genera el contexto.</span><span class="sxs-lookup"><span data-stu-id="8e68e-140">If you are using the EF Designer then you’ll need to edit the T4 template that generates your context.</span></span> <span data-ttu-id="8e68e-141">Abra el \<model_name\> . Archivo Context.tt que está anidado en el archivo edmx, busque el fragmento de código siguiente y agréguelo en la interfaz como se muestra.</span><span class="sxs-lookup"><span data-stu-id="8e68e-141">Open up the \<model_name\>.Context.tt file that is nested under you edmx file, find the following fragment of code and add in the interface as shown.</span></span>  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
```  

## <a name="service-to-be-tested"></a><span data-ttu-id="8e68e-142">Servicio que se va a probar</span><span class="sxs-lookup"><span data-stu-id="8e68e-142">Service to be tested</span></span>  

<span data-ttu-id="8e68e-143">Para demostrar las pruebas con los dobles de pruebas en memoria, vamos a escribir un par de pruebas para un BlogService.</span><span class="sxs-lookup"><span data-stu-id="8e68e-143">To demonstrate testing with in-memory test doubles we are going to be writing a couple of tests for a BlogService.</span></span> <span data-ttu-id="8e68e-144">El servicio es capaz de crear nuevos blogs (AddBlog) y devolver todos los blogs ordenados por nombre (GetAllBlogs).</span><span class="sxs-lookup"><span data-stu-id="8e68e-144">The service is capable of creating new blogs (AddBlog) and returning all Blogs ordered by name (GetAllBlogs).</span></span> <span data-ttu-id="8e68e-145">Además de GetAllBlogs, también se proporciona un método que obtendrá de forma asincrónica todos los blogs ordenados por nombre (GetAllBlogsAsync).</span><span class="sxs-lookup"><span data-stu-id="8e68e-145">In addition to GetAllBlogs, we’ve also provided a method that will asynchronously get all blogs ordered by name (GetAllBlogsAsync).</span></span>  

``` csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class BlogService
    {
        private IBloggingContext _context;

        public BlogService(IBloggingContext context)
        {
            _context = context;
        }

        public Blog AddBlog(string name, string url)
        {
            var blog = new Blog { Name = name, Url = url };
            _context.Blogs.Add(blog);
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

## <a name="creating-the-in-memory-test-doubles"></a><span data-ttu-id="8e68e-146">Crear dobles de pruebas en memoria</span><span class="sxs-lookup"><span data-stu-id="8e68e-146">Creating the in-memory test doubles</span></span>  

<span data-ttu-id="8e68e-147">Ahora que tenemos el modelo de EF real y el servicio que puede usarlo, es el momento de crear el doble de prueba en memoria que se puede usar para las pruebas.</span><span class="sxs-lookup"><span data-stu-id="8e68e-147">Now that we have the real EF model and the service that can use it, it's time to create the in-memory test double that we can use for testing.</span></span> <span data-ttu-id="8e68e-148">Hemos creado un Double de prueba de TestContext para nuestro contexto.</span><span class="sxs-lookup"><span data-stu-id="8e68e-148">We've created a TestContext test double for our context.</span></span> <span data-ttu-id="8e68e-149">En la prueba, se puede elegir el comportamiento que se desea para admitir las pruebas que se van a ejecutar.</span><span class="sxs-lookup"><span data-stu-id="8e68e-149">In test doubles we get to choose the behavior we want in order to support the tests we are going to run.</span></span> <span data-ttu-id="8e68e-150">En este ejemplo, vamos a capturar el número de veces que se llama a SaveChanges, pero puede incluir la lógica que se necesita para comprobar el escenario que se está probando.</span><span class="sxs-lookup"><span data-stu-id="8e68e-150">In this example we're just capturing the number of times SaveChanges is called, but you can include whatever logic is needed to verify the scenario you are testing.</span></span>  

<span data-ttu-id="8e68e-151">También hemos creado un TestDbSet que proporciona una implementación en memoria de DbSet.</span><span class="sxs-lookup"><span data-stu-id="8e68e-151">We've also created a TestDbSet that provides an in-memory implementation of DbSet.</span></span> <span data-ttu-id="8e68e-152">Hemos proporcionado una implementación completa de todos los métodos de DbSet (excepto buscar), pero solo tiene que implementar los miembros que usará el escenario de prueba.</span><span class="sxs-lookup"><span data-stu-id="8e68e-152">We've provided a complete implemention for all the methods on DbSet (except for Find), but you only need to implement the members that your test scenario will use.</span></span>  

<span data-ttu-id="8e68e-153">TestDbSet usa algunas otras clases de infraestructura que hemos incluido para asegurarse de que se puedan procesar las consultas asincrónicas.</span><span class="sxs-lookup"><span data-stu-id="8e68e-153">TestDbSet makes use of some other infrastructure classes that we've included to ensure that async queries can be processed.</span></span>  

``` csharp
using System;
using System.Collections.Generic;
using System.Collections.ObjectModel;
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Linq;
using System.Linq.Expressions;
using System.Threading;
using System.Threading.Tasks;

namespace TestingDemo
{
    public class TestContext : IBloggingContext
    {
        public TestContext()
        {
            this.Blogs = new TestDbSet<Blog>();
            this.Posts = new TestDbSet<Post>();
        }

        public DbSet<Blog> Blogs { get; set; }
        public DbSet<Post> Posts { get; set; }
        public int SaveChangesCount { get; private set; }
        public int SaveChanges()
        {
            this.SaveChangesCount++;
            return 1;
        }
    }

    public class TestDbSet<TEntity> : DbSet<TEntity>, IQueryable, IEnumerable<TEntity>, IDbAsyncEnumerable<TEntity>
        where TEntity : class
    {
        ObservableCollection<TEntity> _data;
        IQueryable _query;

        public TestDbSet()
        {
            _data = new ObservableCollection<TEntity>();
            _query = _data.AsQueryable();
        }

        public override TEntity Add(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Remove(TEntity item)
        {
            _data.Remove(item);
            return item;
        }

        public override TEntity Attach(TEntity item)
        {
            _data.Add(item);
            return item;
        }

        public override TEntity Create()
        {
            return Activator.CreateInstance<TEntity>();
        }

        public override TDerivedEntity Create<TDerivedEntity>()
        {
            return Activator.CreateInstance<TDerivedEntity>();
        }

        public override ObservableCollection<TEntity> Local
        {
            get { return _data; }
        }

        Type IQueryable.ElementType
        {
            get { return _query.ElementType; }
        }

        Expression IQueryable.Expression
        {
            get { return _query.Expression; }
        }

        IQueryProvider IQueryable.Provider
        {
            get { return new TestDbAsyncQueryProvider<TEntity>(_query.Provider); }
        }

        System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IEnumerator<TEntity> IEnumerable<TEntity>.GetEnumerator()
        {
            return _data.GetEnumerator();
        }

        IDbAsyncEnumerator<TEntity> IDbAsyncEnumerable<TEntity>.GetAsyncEnumerator()
        {
            return new TestDbAsyncEnumerator<TEntity>(_data.GetEnumerator());
        }
    }

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

### <a name="implementing-find"></a><span data-ttu-id="8e68e-154">Implementación de Find</span><span class="sxs-lookup"><span data-stu-id="8e68e-154">Implementing Find</span></span>  

<span data-ttu-id="8e68e-155">El método Find es difícil de implementar de forma genérica.</span><span class="sxs-lookup"><span data-stu-id="8e68e-155">The Find method is difficult to implement in a generic fashion.</span></span> <span data-ttu-id="8e68e-156">Si necesita probar el código que hace uso del método Find, es más fácil crear un DbSet de prueba para cada uno de los tipos de entidad que deben admitir la búsqueda.</span><span class="sxs-lookup"><span data-stu-id="8e68e-156">If you need to test code that makes use of the Find method it is easiest to create a test DbSet for each of the entity types that need to support find.</span></span> <span data-ttu-id="8e68e-157">Después, puede escribir lógica para buscar ese tipo de entidad concreto, como se muestra a continuación.</span><span class="sxs-lookup"><span data-stu-id="8e68e-157">You can then write logic to find that particular type of entity, as shown below.</span></span>  

``` csharp
using System.Linq;

namespace TestingDemo
{
    class TestBlogDbSet : TestDbSet<Blog>
    {
        public override Blog Find(params object[] keyValues)
        {
            var id = (int)keyValues.Single();
            return this.SingleOrDefault(b => b.BlogId == id);
        }
    }
}
```  

## <a name="writing-some-tests"></a><span data-ttu-id="8e68e-158">Escribir algunas pruebas</span><span class="sxs-lookup"><span data-stu-id="8e68e-158">Writing some tests</span></span>  

<span data-ttu-id="8e68e-159">Eso es todo lo que necesitamos hacer para iniciar las pruebas.</span><span class="sxs-lookup"><span data-stu-id="8e68e-159">That’s all we need to do to start testing.</span></span> <span data-ttu-id="8e68e-160">La prueba siguiente crea una TestContext y luego un servicio basado en este contexto.</span><span class="sxs-lookup"><span data-stu-id="8e68e-160">The following test creates a TestContext and then a service based on this context.</span></span> <span data-ttu-id="8e68e-161">Después, el servicio se usa para crear un nuevo blog: mediante el método AddBlog.</span><span class="sxs-lookup"><span data-stu-id="8e68e-161">The service is then used to create a new blog – using the AddBlog method.</span></span> <span data-ttu-id="8e68e-162">Por último, la prueba comprueba que el servicio agregó un nuevo blog a la propiedad blogs del contexto y que se llama SaveChanges en el contexto.</span><span class="sxs-lookup"><span data-stu-id="8e68e-162">Finally, the test verifies that the service added a new Blog to the context's Blogs property and called SaveChanges on the context.</span></span>  

<span data-ttu-id="8e68e-163">Este es solo un ejemplo de los tipos de cosas que puede probar con una prueba en memoria Double y puede ajustar la lógica de los dobles de pruebas y la comprobación para satisfacer sus requisitos.</span><span class="sxs-lookup"><span data-stu-id="8e68e-163">This is just an example of the types of things you can test with an in-memory test double and you can adjust the logic of the test doubles and the verification to meet your requirements.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Linq;

namespace TestingDemo
{
    [TestClass]
    public class NonQueryTests
    {
        [TestMethod]
        public void CreateBlog_saves_a_blog_via_context()
        {
            var context = new TestContext();

            var service = new BlogService(context);
            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet");

            Assert.AreEqual(1, context.Blogs.Count());
            Assert.AreEqual("ADO.NET Blog", context.Blogs.Single().Name);
            Assert.AreEqual("http://blogs.msdn.com/adonet", context.Blogs.Single().Url);
            Assert.AreEqual(1, context.SaveChangesCount);
        }
    }
}
```  

<span data-ttu-id="8e68e-164">Este es otro ejemplo de una prueba; esta vez, una que realiza una consulta.</span><span class="sxs-lookup"><span data-stu-id="8e68e-164">Here is another example of a test - this time one that performs a query.</span></span> <span data-ttu-id="8e68e-165">La prueba se inicia creando un contexto de prueba con algunos datos en su propiedad de blog. tenga en cuenta que los datos no están en orden alfabético.</span><span class="sxs-lookup"><span data-stu-id="8e68e-165">The test starts by creating a test context with some data in its Blog property - note that the data is not in alphabetical order.</span></span> <span data-ttu-id="8e68e-166">A continuación, podemos crear un BlogService basado en nuestro contexto de prueba y asegurarse de que los datos que obtenemos de GetAllBlogs estén ordenados por nombre.</span><span class="sxs-lookup"><span data-stu-id="8e68e-166">We can then create a BlogService based on our test context and ensure that the data we get back from GetAllBlogs is ordered by name.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace TestingDemo
{
    [TestClass]
    public class QueryTests
    {
        [TestMethod]
        public void GetAllBlogs_orders_by_name()
        {
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = service.GetAllBlogs();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  

<span data-ttu-id="8e68e-167">Por último, vamos a escribir una prueba más que use nuestro método asincrónico para asegurarse de que la infraestructura asincrónica incluida en [TestDbSet](#creating-the-in-memory-test-doubles) funciona.</span><span class="sxs-lookup"><span data-stu-id="8e68e-167">Finally, we'll write one more test that uses our async method to ensure that the async infrastructure we included in [TestDbSet](#creating-the-in-memory-test-doubles) is working.</span></span>  

``` csharp
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.Collections.Generic;
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
            var context = new TestContext();
            context.Blogs.Add(new Blog { Name = "BBB" });
            context.Blogs.Add(new Blog { Name = "ZZZ" });
            context.Blogs.Add(new Blog { Name = "AAA" });

            var service = new BlogService(context);
            var blogs = await service.GetAllBlogsAsync();

            Assert.AreEqual(3, blogs.Count);
            Assert.AreEqual("AAA", blogs[0].Name);
            Assert.AreEqual("BBB", blogs[1].Name);
            Assert.AreEqual("ZZZ", blogs[2].Name);
        }
    }
}
```  
