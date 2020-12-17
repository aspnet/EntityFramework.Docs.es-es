---
title: 'Trabajo con servidores proxy: EF6'
description: Trabajo con servidores proxy en Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/proxies
ms.openlocfilehash: de44e891c074a8811e466d040f6451bea0f950f4
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635853"
---
# <a name="working-with-proxies"></a><span data-ttu-id="1fd23-103">Trabajar con servidores proxy</span><span class="sxs-lookup"><span data-stu-id="1fd23-103">Working with proxies</span></span>
<span data-ttu-id="1fd23-104">Al crear instancias de tipos de entidad POCO, Entity Framework a menudo crea instancias de un tipo derivado generado dinámicamente que actúa como un proxy para la entidad.</span><span class="sxs-lookup"><span data-stu-id="1fd23-104">When creating instances of POCO entity types, Entity Framework often creates instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="1fd23-105">Este proxy invalida algunas propiedades virtuales de la entidad para insertar enlaces para realizar acciones automáticamente cuando se tiene acceso a la propiedad.</span><span class="sxs-lookup"><span data-stu-id="1fd23-105">This proxy overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="1fd23-106">Por ejemplo, este mecanismo se usa para admitir la carga diferida de relaciones.</span><span class="sxs-lookup"><span data-stu-id="1fd23-106">For example, this mechanism is used to support lazy loading of relationships.</span></span> <span data-ttu-id="1fd23-107">Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.</span><span class="sxs-lookup"><span data-stu-id="1fd23-107">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

## <a name="disabling-proxy-creation"></a><span data-ttu-id="1fd23-108">Deshabilitar la creación de proxy</span><span class="sxs-lookup"><span data-stu-id="1fd23-108">Disabling proxy creation</span></span>  

<span data-ttu-id="1fd23-109">A veces resulta útil evitar que Entity Framework Cree instancias de proxy.</span><span class="sxs-lookup"><span data-stu-id="1fd23-109">Sometimes it is useful to prevent Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="1fd23-110">Por ejemplo, la serialización de instancias que no son de proxy es considerablemente más fácil que serializar instancias de proxy.</span><span class="sxs-lookup"><span data-stu-id="1fd23-110">For example, serializing non-proxy instances is considerably easier than serializing proxy instances.</span></span> <span data-ttu-id="1fd23-111">La creación del proxy se puede desactivar borrando la `ProxyCreationEnabled` marca.</span><span class="sxs-lookup"><span data-stu-id="1fd23-111">Proxy creation can be turned off by clearing the `ProxyCreationEnabled` flag.</span></span> <span data-ttu-id="1fd23-112">Un lugar donde podría hacer esto es en el constructor del contexto.</span><span class="sxs-lookup"><span data-stu-id="1fd23-112">One place you could do this is in the constructor of your context.</span></span> <span data-ttu-id="1fd23-113">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1fd23-113">For example:</span></span>  

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

<span data-ttu-id="1fd23-114">Tenga en cuenta que EF no creará servidores proxy para los tipos en los que no hay nada que pueda realizar el proxy.</span><span class="sxs-lookup"><span data-stu-id="1fd23-114">Note that the EF will not create proxies for types where there is nothing for the proxy to do.</span></span> <span data-ttu-id="1fd23-115">Esto significa que también puede evitar servidores proxy si tiene tipos que están sellados o no tienen ninguna propiedad virtual.</span><span class="sxs-lookup"><span data-stu-id="1fd23-115">This means that you can also avoid proxies by having types that are sealed and/or have no virtual properties.</span></span>  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a><span data-ttu-id="1fd23-116">Crear explícitamente una instancia de un proxy</span><span class="sxs-lookup"><span data-stu-id="1fd23-116">Explicitly creating an instance of a proxy</span></span>  

<span data-ttu-id="1fd23-117">No se creará una instancia de proxy si crea una instancia de una entidad mediante el operador new.</span><span class="sxs-lookup"><span data-stu-id="1fd23-117">A proxy instance will not be created if you create an instance of an entity using the new operator.</span></span> <span data-ttu-id="1fd23-118">Esto puede no ser un problema, pero si necesita crear una instancia de proxy (por ejemplo, para que la carga diferida o el seguimiento de cambios de proxy funcionen), puede hacerlo mediante el `Create` método de `DbSet` .</span><span class="sxs-lookup"><span data-stu-id="1fd23-118">This may not be a problem, but if you need to create a proxy instance (for example, so that lazy loading or proxy change tracking will work) then you can do so using the `Create` method of `DbSet`.</span></span> <span data-ttu-id="1fd23-119">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1fd23-119">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

<span data-ttu-id="1fd23-120">Se puede usar la versión genérica de `Create` si desea crear una instancia de un tipo de entidad derivada.</span><span class="sxs-lookup"><span data-stu-id="1fd23-120">The generic version of `Create` can be used if you want to create an instance of a derived entity type.</span></span> <span data-ttu-id="1fd23-121">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1fd23-121">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

<span data-ttu-id="1fd23-122">Tenga en cuenta que el `Create` método no agrega ni adjunta la entidad creada al contexto.</span><span class="sxs-lookup"><span data-stu-id="1fd23-122">Note that the `Create` method does not add or attach the created entity to the context.</span></span>  

<span data-ttu-id="1fd23-123">Tenga en cuenta que el `Create` método solo creará una instancia del tipo de entidad si la creación de un tipo de proxy para la entidad no tiene ningún valor, ya que no haría nada.</span><span class="sxs-lookup"><span data-stu-id="1fd23-123">Note that the `Create` method will just create an instance of the entity type itself if creating a proxy type for the entity would have no value because it would not do anything.</span></span> <span data-ttu-id="1fd23-124">Por ejemplo, si el tipo de entidad está sellado y/o no tiene ninguna propiedad virtual, `Create` solo creará una instancia del tipo de entidad.</span><span class="sxs-lookup"><span data-stu-id="1fd23-124">For example, if the entity type is sealed and/or has no virtual properties then `Create` will just create an instance of the entity type.</span></span>  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a><span data-ttu-id="1fd23-125">Obtener el tipo de entidad real a partir de un tipo de proxy</span><span class="sxs-lookup"><span data-stu-id="1fd23-125">Getting the actual entity type from a proxy type</span></span>  

<span data-ttu-id="1fd23-126">Los tipos de proxy tienen nombres que tienen un aspecto similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="1fd23-126">Proxy types have names that look something like this:</span></span>  

```
System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6
```

<span data-ttu-id="1fd23-127">Puede encontrar el tipo de entidad para este tipo de proxy mediante el `GetObjectType` método de `ObjectContext` .</span><span class="sxs-lookup"><span data-stu-id="1fd23-127">You can find the entity type for this proxy type using the `GetObjectType` method from `ObjectContext`.</span></span> <span data-ttu-id="1fd23-128">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="1fd23-128">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

<span data-ttu-id="1fd23-129">Tenga en cuenta que si el tipo que se pasa a `GetObjectType` es una instancia de un tipo de entidad que no es un tipo de proxy, el tipo de entidad se sigue devolviendo.</span><span class="sxs-lookup"><span data-stu-id="1fd23-129">Note that if the type passed to `GetObjectType` is an instance of an entity type that is not a proxy type then the type of entity is still returned.</span></span> <span data-ttu-id="1fd23-130">Esto significa que siempre puede usar este método para obtener el tipo de entidad real sin ninguna otra comprobación para ver si el tipo es un tipo de proxy.</span><span class="sxs-lookup"><span data-stu-id="1fd23-130">This means you can always use this method to get the actual entity type without any other checking to see if the type is a proxy type or not.</span></span>  
