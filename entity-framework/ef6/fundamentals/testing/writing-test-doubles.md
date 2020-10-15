---
title: Pruebas con sus propias pruebas dobles-EF6
description: Las pruebas con su propia prueba se duplican en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/testing/writing-test-doubles
ms.openlocfilehash: 16a237edd4ff8ce72800cfd623de93a08469cfe1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062820"
---
# <a name="testing-with-your-own-test-doubles"></a>Pruebas con sus propias dobles de pruebas
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.  

Al escribir pruebas para la aplicación, a menudo es conveniente evitar la llegada de la base de datos.  Entity Framework le permite conseguirlo mediante la creación de un contexto, con el comportamiento definido por las pruebas, que hace uso de los datos en memoria.  

## <a name="options-for-creating-test-doubles"></a>Opciones para crear dobles de pruebas  

Existen dos enfoques diferentes que se pueden usar para crear una versión en memoria del contexto.  

- **Crear sus propios dobles de pruebas** : este enfoque implica escribir su propia implementación en memoria de su contexto y DbSets. Esto le ofrece un gran control sobre cómo se comportan las clases, pero puede implicar la escritura y la propiedad de una cantidad de código razonable.  
- **Usar un marco ficticio para crear dobles de pruebas** : mediante un marco ficticio (como MOQ), puede tener las implementaciones en memoria de contexto y conjuntos creados dinámicamente en tiempo de ejecución.  

En este artículo se tratará la creación de su propio Double de prueba. Para obtener información sobre el uso de un marco ficticio, vea [probar con un marco ficticio](xref:ef6/fundamentals/testing/mocking).  

## <a name="testing-with-pre-ef6-versions"></a>Pruebas con versiones anteriores a EF6  

El código que se muestra en este artículo es compatible con EF6. Para realizar pruebas con EF5 y versiones anteriores, consulte [pruebas con un contexto falso](https://romiller.com/2012/02/14/testing-with-a-fake-dbcontext/).  

## <a name="limitations-of-ef-in-memory-test-doubles"></a>Limitaciones de los dobles de pruebas en memoria de EF  

Los dobles de pruebas en memoria pueden ser una buena manera de proporcionar una cobertura de nivel de prueba unitaria de bits de la aplicación que usa EF. Sin embargo, al hacerlo, se usa LINQ to Objects para ejecutar consultas en los datos en memoria. Esto puede dar lugar a un comportamiento diferente al uso del proveedor LINQ (LINQ to Entities) de EF para traducir las consultas en SQL que se ejecutan en la base de datos.  

Un ejemplo de este tipo de diferencia es la carga de datos relacionados. Si crea una serie de blogs en los que cada uno tiene elementos relacionados, al usar los datos en memoria, los envíos relacionados se cargarán siempre para cada blog. Sin embargo, cuando se ejecuta en una base de datos, los datos solo se cargarán si se usa el método include.  

Por esta razón, se recomienda incluir siempre cierto nivel de pruebas de un extremo a otro (además de las pruebas unitarias) para asegurarse de que la aplicación funciona correctamente en una base de datos.  

## <a name="following-along-with-this-article"></a>Junto con este artículo  

En este artículo se proporcionan listas de código completas que se pueden copiar en Visual Studio para que se realicen a continuación, si así se desea. Es más fácil crear un **proyecto de prueba unitaria** y tendrá que tener como destino **.NET Framework 4,5** para completar las secciones que usan Async.  

## <a name="creating-a-context-interface"></a>Crear una interfaz de contexto  

Vamos a echar un vistazo a la prueba de un servicio que hace uso de un modelo EF. Para poder reemplazar el contexto de EF con una versión en memoria para las pruebas, vamos a definir una interfaz que implementará el contexto de EF (y el valor Double en memoria).

El servicio que se va a probar consultará y modificará los datos mediante las propiedades DbSet de nuestro contexto y también llamará a SaveChanges para enviar los cambios a la base de datos. Por tanto, vamos a incluir estos miembros en la interfaz.  

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

## <a name="the-ef-model"></a>El modelo EF  

El servicio que vamos a probar hace uso de un modelo EF compuesto por las clases BloggingContext y blog y post. Este código puede haber sido generado por EF Designer o ser un modelo de Code First.  

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

### <a name="implementing-the-context-interface-with-the-ef-designer"></a>Implementar la interfaz de contexto con EF Designer  

Tenga en cuenta que nuestro contexto implementa la interfaz IBloggingContext.  

Si usa Code First, puede modificar el contexto directamente para implementar la interfaz. Si usa el diseñador de EF, tendrá que editar la plantilla T4 que genera el contexto. Abra el \<model_name\> . Archivo Context.tt que está anidado en el archivo edmx, busque el fragmento de código siguiente y agréguelo en la interfaz como se muestra.  

``` csharp  
<#=Accessibility.ForType(container)#> partial class <#=code.Escape(container)#> : DbContext, IBloggingContext
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

## <a name="creating-the-in-memory-test-doubles"></a>Crear dobles de pruebas en memoria  

Ahora que tenemos el modelo de EF real y el servicio que puede usarlo, es el momento de crear el doble de prueba en memoria que se puede usar para las pruebas. Hemos creado un Double de prueba de TestContext para nuestro contexto. En la prueba, se puede elegir el comportamiento que se desea para admitir las pruebas que se van a ejecutar. En este ejemplo, vamos a capturar el número de veces que se llama a SaveChanges, pero puede incluir la lógica que se necesita para comprobar el escenario que se está probando.  

También hemos creado un TestDbSet que proporciona una implementación en memoria de DbSet. Hemos proporcionado una implementación completa de todos los métodos de DbSet (excepto buscar), pero solo tiene que implementar los miembros que usará el escenario de prueba.  

TestDbSet usa algunas otras clases de infraestructura que hemos incluido para asegurarse de que se puedan procesar las consultas asincrónicas.  

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

### <a name="implementing-find"></a>Implementación de Find  

El método Find es difícil de implementar de forma genérica. Si necesita probar el código que hace uso del método Find, es más fácil crear un DbSet de prueba para cada uno de los tipos de entidad que deben admitir la búsqueda. Después, puede escribir lógica para buscar ese tipo de entidad concreto, como se muestra a continuación.  

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

## <a name="writing-some-tests"></a>Escribir algunas pruebas  

Eso es todo lo que necesitamos hacer para iniciar las pruebas. La prueba siguiente crea una TestContext y luego un servicio basado en este contexto. Después, el servicio se usa para crear un nuevo blog: mediante el método AddBlog. Por último, la prueba comprueba que el servicio agregó un nuevo blog a la propiedad blogs del contexto y que se llama SaveChanges en el contexto.  

Este es solo un ejemplo de los tipos de cosas que puede probar con una prueba en memoria Double y puede ajustar la lógica de los dobles de pruebas y la comprobación para satisfacer sus requisitos.  

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

Este es otro ejemplo de una prueba; esta vez, una que realiza una consulta. La prueba se inicia creando un contexto de prueba con algunos datos en su propiedad de blog. tenga en cuenta que los datos no están en orden alfabético. A continuación, podemos crear un BlogService basado en nuestro contexto de prueba y asegurarse de que los datos que obtenemos de GetAllBlogs estén ordenados por nombre.  

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

Por último, vamos a escribir una prueba más que use nuestro método asincrónico para asegurarse de que la infraestructura asincrónica incluida en [TestDbSet](#creating-the-in-memory-test-doubles) funciona.  

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
