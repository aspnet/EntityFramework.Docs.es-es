---
title: 'Configuración basada en código: EF6'
description: Configuración basada en código en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: 67bb7ebd620c90ebe80983cc5baa6cab032907f8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90070736"
---
# <a name="code-based-configuration"></a><span data-ttu-id="76a02-103">Configuración basada en código</span><span class="sxs-lookup"><span data-stu-id="76a02-103">Code-based configuration</span></span>
> [!NOTE]
> <span data-ttu-id="76a02-104">**Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6.</span><span class="sxs-lookup"><span data-stu-id="76a02-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="76a02-105">Si usa una versión anterior, no se aplica parte o la totalidad de la información.</span><span class="sxs-lookup"><span data-stu-id="76a02-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="76a02-106">La configuración de una aplicación Entity Framework se puede especificar en un archivo de configuración (app.config/web.config) o mediante código.</span><span class="sxs-lookup"><span data-stu-id="76a02-106">Configuration for an Entity Framework application can be specified in a config file (app.config/web.config) or through code.</span></span> <span data-ttu-id="76a02-107">Este último se conoce como configuración basada en código.</span><span class="sxs-lookup"><span data-stu-id="76a02-107">The latter is known as code-based configuration.</span></span>  

<span data-ttu-id="76a02-108">La configuración de un archivo de configuración se describe en un [artículo independiente](xref:ef6/fundamentals/configuring/config-file).</span><span class="sxs-lookup"><span data-stu-id="76a02-108">Configuration in a config file is described in a [separate article](xref:ef6/fundamentals/configuring/config-file).</span></span> <span data-ttu-id="76a02-109">El archivo de configuración tiene prioridad sobre la configuración basada en código.</span><span class="sxs-lookup"><span data-stu-id="76a02-109">The config file takes precedence over code-based configuration.</span></span> <span data-ttu-id="76a02-110">En otras palabras, si se establece una opción de configuración en el código y en el archivo de configuración, se usa la configuración del archivo de configuración.</span><span class="sxs-lookup"><span data-stu-id="76a02-110">In other words, if a configuration option is set in both code and in the config file, then the setting in the config file is used.</span></span>  

## <a name="using-dbconfiguration"></a><span data-ttu-id="76a02-111">Usar DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="76a02-111">Using DbConfiguration</span></span>  

<span data-ttu-id="76a02-112">La configuración basada en código en EF6 y versiones posteriores se consigue mediante la creación de una subclase de System.Data.Entity.Config. DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="76a02-112">Code-based configuration in EF6 and above is achieved by creating a subclass of System.Data.Entity.Config.DbConfiguration.</span></span> <span data-ttu-id="76a02-113">Se deben seguir las siguientes directrices para crear subclases de DbConfiguration:</span><span class="sxs-lookup"><span data-stu-id="76a02-113">The following guidelines should be followed when subclassing DbConfiguration:</span></span>  

- <span data-ttu-id="76a02-114">Cree solo una clase DbConfiguration para la aplicación.</span><span class="sxs-lookup"><span data-stu-id="76a02-114">Create only one DbConfiguration class for your application.</span></span> <span data-ttu-id="76a02-115">Esta clase especifica la configuración de todo el dominio de aplicación.</span><span class="sxs-lookup"><span data-stu-id="76a02-115">This class specifies app-domain wide settings.</span></span>  
- <span data-ttu-id="76a02-116">Coloque la clase DbConfiguration en el mismo ensamblado que la clase DbContext.</span><span class="sxs-lookup"><span data-stu-id="76a02-116">Place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="76a02-117">(Consulte la sección *mover DbConfiguration* si desea cambiar esto).</span><span class="sxs-lookup"><span data-stu-id="76a02-117">(See the *Moving DbConfiguration* section if you want to change this.)</span></span>  
- <span data-ttu-id="76a02-118">Asigne a la clase DbConfiguration un constructor sin parámetros público.</span><span class="sxs-lookup"><span data-stu-id="76a02-118">Give your DbConfiguration class a public parameterless constructor.</span></span>  
- <span data-ttu-id="76a02-119">Establezca las opciones de configuración llamando a métodos DbConfiguration protegidos desde dentro de este constructor.</span><span class="sxs-lookup"><span data-stu-id="76a02-119">Set configuration options by calling protected DbConfiguration methods from within this constructor.</span></span>  

<span data-ttu-id="76a02-120">Si sigue estas instrucciones, EF podrá detectar y usar la configuración automáticamente con las herramientas que necesiten tener acceso a su modelo y cuando se ejecute la aplicación.</span><span class="sxs-lookup"><span data-stu-id="76a02-120">Following these guidelines allows EF to discover and use your configuration automatically by both tooling that needs to access your model and when your application is run.</span></span>  

## <a name="example"></a><span data-ttu-id="76a02-121">Ejemplo</span><span class="sxs-lookup"><span data-stu-id="76a02-121">Example</span></span>  

<span data-ttu-id="76a02-122">Una clase derivada de DbConfiguration podría tener el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="76a02-122">A class derived from DbConfiguration might look like this:</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.SqlServer;

namespace MyNamespace
{
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
            SetDefaultConnectionFactory(new LocalDbConnectionFactory("mssqllocaldb"));
        }
    }
}
```  

<span data-ttu-id="76a02-123">Esta clase establece EF para usar la estrategia de ejecución de SQL Azure: para reintentar automáticamente las operaciones de base de datos con errores y para usar la base de datos local para las bases de datos creadas por Convención desde Code First.</span><span class="sxs-lookup"><span data-stu-id="76a02-123">This class sets up EF to use the SQL Azure execution strategy - to automatically retry failed database operations - and to use Local DB for databases that are created by convention from Code First.</span></span>  

## <a name="moving-dbconfiguration"></a><span data-ttu-id="76a02-124">Mover DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="76a02-124">Moving DbConfiguration</span></span>  

<span data-ttu-id="76a02-125">Hay casos en los que no es posible colocar la clase DbConfiguration en el mismo ensamblado que la clase DbContext.</span><span class="sxs-lookup"><span data-stu-id="76a02-125">There are cases where it is not possible to place your DbConfiguration class in the same assembly as your DbContext class.</span></span> <span data-ttu-id="76a02-126">Por ejemplo, puede tener dos clases DbContext cada una en ensamblados distintos.</span><span class="sxs-lookup"><span data-stu-id="76a02-126">For example, you may have two DbContext classes each in different assemblies.</span></span> <span data-ttu-id="76a02-127">Hay dos opciones para controlar esto.</span><span class="sxs-lookup"><span data-stu-id="76a02-127">There are two options for handling this.</span></span>  

<span data-ttu-id="76a02-128">La primera opción es usar el archivo de configuración para especificar la instancia de DbConfiguration que se va a usar.</span><span class="sxs-lookup"><span data-stu-id="76a02-128">The first option is to use the config file to specify the DbConfiguration instance to use.</span></span> <span data-ttu-id="76a02-129">Para ello, establezca el atributo codeConfigurationType de la sección entityFramework.</span><span class="sxs-lookup"><span data-stu-id="76a02-129">To do this, set the codeConfigurationType attribute of the entityFramework section.</span></span> <span data-ttu-id="76a02-130">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="76a02-130">For example:</span></span>  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

<span data-ttu-id="76a02-131">El valor de codeConfigurationType debe ser el ensamblado y el nombre completo del espacio de nombres de la clase DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="76a02-131">The value of codeConfigurationType must be the assembly and namespace qualified name of your DbConfiguration class.</span></span>  

<span data-ttu-id="76a02-132">La segunda opción consiste en colocar DbConfigurationTypeAttribute en la clase de contexto.</span><span class="sxs-lookup"><span data-stu-id="76a02-132">The second option is to place DbConfigurationTypeAttribute on your context class.</span></span> <span data-ttu-id="76a02-133">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="76a02-133">For example:</span></span>  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

<span data-ttu-id="76a02-134">El valor que se pasa al atributo puede ser el tipo DbConfiguration, como se muestra arriba, o el ensamblado y el nombre de espacio de nombres de tipo completo String.</span><span class="sxs-lookup"><span data-stu-id="76a02-134">The value passed to the attribute can either be your DbConfiguration type - as shown above - or the assembly and namespace qualified type name string.</span></span> <span data-ttu-id="76a02-135">Por ejemplo:</span><span class="sxs-lookup"><span data-stu-id="76a02-135">For example:</span></span>  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a><span data-ttu-id="76a02-136">Establecer DbConfiguration explícitamente</span><span class="sxs-lookup"><span data-stu-id="76a02-136">Setting DbConfiguration explicitly</span></span>  

<span data-ttu-id="76a02-137">Hay algunas situaciones en las que puede ser necesaria la configuración antes de que se haya usado cualquier tipo DbContext.</span><span class="sxs-lookup"><span data-stu-id="76a02-137">There are some situations where configuration may be needed before any DbContext type has been used.</span></span> <span data-ttu-id="76a02-138">Estos son algunos ejemplos:</span><span class="sxs-lookup"><span data-stu-id="76a02-138">Examples of this include:</span></span>  

- <span data-ttu-id="76a02-139">Usar DbModelBuilder para compilar un modelo sin un contexto</span><span class="sxs-lookup"><span data-stu-id="76a02-139">Using DbModelBuilder to build a model without a context</span></span>  
- <span data-ttu-id="76a02-140">Usar algún otro código de la utilidad o del marco de trabajo que usa un DbContext donde ese contexto se usa antes de que se use el contexto de la aplicación</span><span class="sxs-lookup"><span data-stu-id="76a02-140">Using some other framework/utility code that utilizes a DbContext where that context is used before your application context is used</span></span>  

<span data-ttu-id="76a02-141">En estas situaciones, EF no puede detectar la configuración automáticamente y, en su lugar, debe realizar una de las siguientes acciones:</span><span class="sxs-lookup"><span data-stu-id="76a02-141">In such situations EF is unable to discover the configuration automatically and you must instead do one of the following:</span></span>  

- <span data-ttu-id="76a02-142">Establezca el tipo DbConfiguration en el archivo de configuración, como se describe en la sección anterior *DbConfiguration*</span><span class="sxs-lookup"><span data-stu-id="76a02-142">Set the DbConfiguration type in the config file, as described in the *Moving DbConfiguration* section above</span></span>
- <span data-ttu-id="76a02-143">Llame al método estático DbConfiguration. SetConfiguration durante el inicio de la aplicación.</span><span class="sxs-lookup"><span data-stu-id="76a02-143">Call the static DbConfiguration.SetConfiguration method during application startup</span></span>  

## <a name="overriding-dbconfiguration"></a><span data-ttu-id="76a02-144">Invalidar DbConfiguration</span><span class="sxs-lookup"><span data-stu-id="76a02-144">Overriding DbConfiguration</span></span>  

<span data-ttu-id="76a02-145">Hay algunas situaciones en las que es necesario invalidar el conjunto de configuración en DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="76a02-145">There are some situations where you need to override the configuration set in the DbConfiguration.</span></span> <span data-ttu-id="76a02-146">Normalmente, no lo hacen los desarrolladores de aplicaciones, sino los proveedores y complementos de terceros que no pueden usar una clase derivada de DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="76a02-146">This is not typically done by application developers but rather by third party providers and plug-ins that cannot use a derived DbConfiguration class.</span></span>  

<span data-ttu-id="76a02-147">Para ello, EntityFramework permite registrar un controlador de eventos que puede modificar la configuración existente justo antes de que se bloquee.</span><span class="sxs-lookup"><span data-stu-id="76a02-147">For this, EntityFramework allows an event handler to be registered that can modify existing configuration just before it is locked down.</span></span>  <span data-ttu-id="76a02-148">También proporciona un método de azúcar específico para reemplazar cualquier servicio devuelto por el localizador del servicio EF.</span><span class="sxs-lookup"><span data-stu-id="76a02-148">It also provides a sugar method specifically for replacing any service returned by the EF service locator.</span></span> <span data-ttu-id="76a02-149">Así es como se pretende usar:</span><span class="sxs-lookup"><span data-stu-id="76a02-149">This is how it is intended to be used:</span></span>  

- <span data-ttu-id="76a02-150">En el inicio de la aplicación (antes de que se use EF), el complemento o el proveedor deben registrar el método de control de eventos para este evento.</span><span class="sxs-lookup"><span data-stu-id="76a02-150">At app startup (before EF is used) the plug-in or provider should register the event handler method for this event.</span></span> <span data-ttu-id="76a02-151">(Tenga en cuenta que esto debe ocurrir antes de que la aplicación use EF).</span><span class="sxs-lookup"><span data-stu-id="76a02-151">(Note that this must happen before the application uses EF.)</span></span>  
- <span data-ttu-id="76a02-152">El controlador de eventos realiza una llamada a ReplaceService para cada servicio que debe reemplazarse.</span><span class="sxs-lookup"><span data-stu-id="76a02-152">The event handler makes a call to ReplaceService for every service that needs to be replaced.</span></span>  

<span data-ttu-id="76a02-153">Por ejemplo, para reemplazar IDbConnectionFactory y DbProviderService, debe registrar un controlador similar al siguiente:</span><span class="sxs-lookup"><span data-stu-id="76a02-153">For example, to replace IDbConnectionFactory and DbProviderService you would register a handler something like this:</span></span>  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

<span data-ttu-id="76a02-154">En el código anterior MyProviderServices y MyConnectionFactory se representan las implementaciones del servicio.</span><span class="sxs-lookup"><span data-stu-id="76a02-154">In the code above MyProviderServices and MyConnectionFactory represent your implementations of the service.</span></span>  

<span data-ttu-id="76a02-155">También puede agregar controladores de dependencia adicionales para obtener el mismo efecto.</span><span class="sxs-lookup"><span data-stu-id="76a02-155">You can also add additional dependency handlers to get the same effect.</span></span>  

<span data-ttu-id="76a02-156">Tenga en cuenta que también puede incluir DbProviderFactory de esta manera, pero esto solo afectará a EF y no usará el DbProviderFactory fuera de EF.</span><span class="sxs-lookup"><span data-stu-id="76a02-156">Note that you could also wrap DbProviderFactory in this way, but doing so will only affect EF and not uses of the DbProviderFactory outside of EF.</span></span> <span data-ttu-id="76a02-157">Por esta razón, es probable que desee seguir ajustando DbProviderFactory como antes.</span><span class="sxs-lookup"><span data-stu-id="76a02-157">For this reason you’ll probably want to continue to wrap DbProviderFactory as you have before.</span></span>  

<span data-ttu-id="76a02-158">También debe tener en cuenta los servicios que se ejecutan externamente en la aplicación; por ejemplo, cuando se ejecutan migraciones desde la consola del administrador de paquetes.</span><span class="sxs-lookup"><span data-stu-id="76a02-158">You should also keep in mind the services that you run externally to your application - for example, when running migrations from the Package Manager Console.</span></span> <span data-ttu-id="76a02-159">Al ejecutar la migración desde la consola, intentará encontrar su DbConfiguration.</span><span class="sxs-lookup"><span data-stu-id="76a02-159">When you run migrate from the console it will attempt to find your DbConfiguration.</span></span> <span data-ttu-id="76a02-160">Sin embargo, tanto si obtendrá el servicio ajustado como si no depende de dónde se registró el controlador de eventos.</span><span class="sxs-lookup"><span data-stu-id="76a02-160">However, whether or not it will get the wrapped service depends on where the event handler it registered.</span></span> <span data-ttu-id="76a02-161">Si se registra como parte de la construcción de DbConfiguration, el código debe ejecutarse y el servicio debe ajustarse.</span><span class="sxs-lookup"><span data-stu-id="76a02-161">If it is registered as part of the construction of your DbConfiguration then the code should execute and the service should get wrapped.</span></span> <span data-ttu-id="76a02-162">Normalmente, este no es el caso y esto significa que las herramientas no obtendrán el servicio ajustado.</span><span class="sxs-lookup"><span data-stu-id="76a02-162">Usually this won’t be the case and this means that tooling won’t get the wrapped service.</span></span>  
