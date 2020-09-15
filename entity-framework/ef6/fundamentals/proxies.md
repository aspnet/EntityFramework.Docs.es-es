---
title: 'Trabajo con servidores proxy: EF6'
description: Trabajo con servidores proxy en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/proxies
ms.openlocfilehash: e626a7dff67497a2fbb3dcd169704814ea4cf2cf
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070502"
---
# <a name="working-with-proxies"></a>Trabajar con servidores proxy
Al crear instancias de tipos de entidad POCO, Entity Framework a menudo crea instancias de un tipo derivado generado dinámicamente que actúa como un proxy para la entidad. Este proxy invalida algunas propiedades virtuales de la entidad para insertar enlaces para realizar acciones automáticamente cuando se tiene acceso a la propiedad. Por ejemplo, este mecanismo se usa para admitir la carga diferida de relaciones. Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.  

## <a name="disabling-proxy-creation"></a>Deshabilitar la creación de proxy  

A veces resulta útil evitar que Entity Framework Cree instancias de proxy. Por ejemplo, la serialización de instancias que no son de proxy es considerablemente más fácil que serializar instancias de proxy. La creación del proxy se puede desactivar borrando la marca ProxyCreationEnabled. Un lugar donde podría hacer esto es en el constructor del contexto. Por ejemplo:  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.ProxyCreationEnabled = false;
    }  

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

Tenga en cuenta que EF no creará servidores proxy para los tipos en los que no hay nada que pueda realizar el proxy. Esto significa que también puede evitar servidores proxy si tiene tipos que están sellados o no tienen ninguna propiedad virtual.  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a>Crear explícitamente una instancia de un proxy  

No se creará una instancia de proxy si crea una instancia de una entidad mediante el operador new. Esto puede no ser un problema, pero si necesita crear una instancia de proxy (por ejemplo, para que la carga diferida o el seguimiento de cambios de proxy funcionen), puede hacerlo mediante el método Create de DbSet. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

La versión genérica de Create se puede usar si desea crear una instancia de un tipo de entidad derivada. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

Tenga en cuenta que el método Create no agrega ni adjunta la entidad creada al contexto.  

Tenga en cuenta que el método Create solo creará una instancia del tipo de entidad si la creación de un tipo de proxy para la entidad no tiene ningún valor, ya que no haría nada. Por ejemplo, si el tipo de entidad está sellado y/o no tiene ninguna propiedad virtual, Create solo creará una instancia del tipo de entidad.  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a>Obtener el tipo de entidad real a partir de un tipo de proxy  

Los tipos de proxy tienen nombres que tienen un aspecto similar al siguiente:  

System. Data. Entity. DynamicProxies. Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6  

Puede encontrar el tipo de entidad para este tipo de proxy mediante el método GetObjectType de ObjectContext. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

Tenga en cuenta que si el tipo pasado a GetObjectType es una instancia de un tipo de entidad que no es un tipo de proxy, el tipo de entidad se sigue devolviendo. Esto significa que siempre puede usar este método para obtener el tipo de entidad real sin ninguna otra comprobación para ver si el tipo es un tipo de proxy.  
