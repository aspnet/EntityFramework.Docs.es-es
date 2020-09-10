---
title: 'Pruebas con un marco ficticio: EF6'
description: Probar con un marco ficticio en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: bd66a638-d245-44d4-8e71-b9c6cb335cc7
uid: ef6/fundamentals/testing/mocking
ms.openlocfilehash: 01890ab3bb8dbf0caa7b3eff797e53b06bc8ec9b
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618306"
---
# <a name="testing-with-a-mocking-framework"></a>Probar con un marco ficticio
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.  

Al escribir pruebas para la aplicación, a menudo es conveniente evitar la llegada de la base de datos.  Entity Framework le permite conseguirlo mediante la creación de un contexto, con el comportamiento definido por las pruebas, que hace uso de los datos en memoria.  

## <a name="options-for-creating-test-doubles"></a>Opciones para crear dobles de pruebas  

Existen dos enfoques diferentes que se pueden usar para crear una versión en memoria del contexto.  

- **Crear sus propios dobles de pruebas** : este enfoque implica escribir su propia implementación en memoria de su contexto y DbSets. Esto le ofrece un gran control sobre cómo se comportan las clases, pero puede implicar la escritura y la propiedad de una cantidad de código razonable.  
- **Usar un marco ficticio para crear dobles de pruebas** : mediante un marco ficticio (como MOQ), puede tener las implementaciones en memoria del contexto y los conjuntos creados dinámicamente en tiempo de ejecución.  

En este artículo se tratará el uso de un marco ficticio. Para crear sus propios dobles de pruebas, consulte [pruebas con los dobles de pruebas](xref:ef6/fundamentals/testing/writing-test-doubles).  

Para demostrar el uso de EF con un marco ficticio, vamos a usar MOQ. La forma más fácil de obtener MOQ es instalar el [paquete MOQ desde NuGet](https://nuget.org/packages/Moq/).  

## <a name="testing-with-pre-ef6-versions"></a>Pruebas con versiones anteriores a EF6  

El escenario que se muestra en este artículo depende de algunos cambios realizados en DbSet en EF6. Para realizar pruebas con EF5 y versiones anteriores, consulte [pruebas con un contexto falso](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>Limitaciones de los dobles de pruebas en memoria de EF  

Los dobles de pruebas en memoria pueden ser una buena manera de proporcionar una cobertura de nivel de prueba unitaria de bits de la aplicación que usa EF. Sin embargo, al hacerlo, se usa LINQ to Objects para ejecutar consultas en los datos en memoria. Esto puede dar lugar a un comportamiento diferente al uso del proveedor LINQ (LINQ to Entities) de EF para traducir las consultas en SQL que se ejecutan en la base de datos.  

Un ejemplo de este tipo de diferencia es la carga de datos relacionados. Si crea una serie de blogs en los que cada uno tiene elementos relacionados, al usar los datos en memoria, los envíos relacionados se cargarán siempre para cada blog. Sin embargo, cuando se ejecuta en una base de datos, los datos solo se cargarán si se usa el método include.  

Por esta razón, se recomienda incluir siempre cierto nivel de pruebas de un extremo a otro (además de las pruebas unitarias) para asegurarse de que la aplicación funciona correctamente en una base de datos.  

## <a name="following-along-with-this-article"></a>Junto con este artículo  

En este artículo se proporcionan listas de código completas que se pueden copiar en Visual Studio para que se realicen a continuación, si así se desea. Es más fácil crear un **proyecto de prueba unitaria** y tendrá que tener como destino **.NET Framework 4,5** para completar las secciones que usan Async.  

## <a name="the-ef-model"></a>El modelo EF  

El servicio que vamos a probar hace uso de un modelo EF compuesto por las clases BloggingContext y blog y post. Este código puede haber sido generado por EF Designer o ser un modelo de Code First.  

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

### <a name="virtual-dbset-properties-with-ef-designer"></a>Propiedades de DbSet virtuales con EF Designer  

Tenga en cuenta que las propiedades de DbSet en el contexto se marcan como virtuales. Esto permitirá que el marco ficticio se derive de nuestro contexto e invalide estas propiedades con una implementación ficticia.  

Si usa Code First, puede editar las clases directamente. Si usa el diseñador de EF, tendrá que editar la plantilla T4 que genera el contexto. Abra el \<model_name\> . Archivo Context.tt que está anidado en el archivo edmx, busque el fragmento de código siguiente y agregue la palabra clave virtual como se muestra.  

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

## <a name="service-to-be-tested"></a>Servicio que se va a probar  

Para demostrar las pruebas con los dobles de pruebas en memoria, vamos a escribir un par de pruebas para un BlogService. El servicio es capaz de crear nuevos blogs (AddBlog) y devolver todos los blogs ordenados por nombre (GetAllBlogs). Además de GetAllBlogs, también se proporciona un método que obtendrá de forma asincrónica todos los blogs ordenados por nombre (GetAllBlogsAsync).  

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

## <a name="testing-non-query-scenarios"></a>Probar escenarios que no son de consulta  

Eso es todo lo que necesitamos hacer para empezar a probar los métodos que no son de consulta. La prueba siguiente usa MOQ para crear un contexto. Después crea un DbSet \<Blog\> y lo conecta para que se devuelva desde la propiedad blogs del contexto. Después, el contexto se usa para crear un nuevo BlogService que se usa para crear un nuevo blog: mediante el método AddBlog. Por último, la prueba comprueba que el servicio agregó un nuevo blog y se llama SaveChanges en el contexto.  

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

## <a name="testing-query-scenarios"></a>Probar escenarios de consulta  

Para poder ejecutar consultas en nuestra prueba de DbSet doble, es necesario configurar una implementación de IQueryable. El primer paso es crear algunos datos en memoria: usamos una lista \<Blog\> . A continuación, creamos un contexto y DBSet \<Blog\> , a continuación, conectamos la implementación de IQueryable para DBSet: simplemente están delegando en el LINQ to Objects proveedor que funciona con la lista \<T\> .  

A continuación, podemos crear un BlogService basado en los dobles de pruebas y asegurarse de que los datos que se obtienen de GetAllBlogs se ordenan por nombre.  

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

### <a name="testing-with-async-queries"></a>Pruebas con consultas asincrónicas

Entity Framework 6 presentó un conjunto de métodos de extensión que se pueden usar para ejecutar una consulta de forma asincrónica. Entre los ejemplos de estos métodos se incluyen ToListAsync, FirstAsync, ForEachAsync, etc.  

Dado que Entity Framework consultas usan LINQ, los métodos de extensión se definen en IQueryable y IEnumerable. Sin embargo, dado que solo están diseñados para usarse con Entity Framework puede recibir el siguiente error si intenta utilizarlos en una consulta LINQ que no es una consulta de Entity Framework:

> IQueryable de origen no implementa IDbAsyncEnumerable {0} . Solo los orígenes que implementan IDbAsyncEnumerable se pueden usar para las operaciones asincrónicas de Entity Framework. Para obtener más información, vea [http://go.microsoft.com/fwlink/?LinkId=287068](https://go.microsoft.com/fwlink/?LinkId=287068) .  

Mientras que los métodos asincrónicos solo se admiten cuando se ejecuta en una consulta EF, puede que desee usarlos en la prueba unitaria cuando se ejecuta en una prueba en memoria Double de un DbSet.  

Para utilizar los métodos asincrónicos, es necesario crear un DbAsyncQueryProvider en memoria para procesar la consulta asincrónica. Aunque sería posible configurar un proveedor de consultas mediante MOQ, es mucho más fácil crear una implementación de prueba Double en el código. El código para esta implementación es el siguiente:  

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

Ahora que tenemos un proveedor de consultas asincrónicas, podemos escribir una prueba unitaria para el nuevo método GetAllBlogsAsync.  

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
