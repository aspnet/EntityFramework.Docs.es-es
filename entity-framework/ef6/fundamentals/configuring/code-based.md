---
title: 'Configuración basada en código: EF6'
description: Configuración basada en código en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 13886d24-2c74-4a00-89eb-aa0dee328d83
uid: ef6/fundamentals/configuring/code-based
ms.openlocfilehash: 643aefff1d8a143e7df8251eff4e5051e2c6bd08
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618499"
---
# <a name="code-based-configuration"></a>Configuración basada en código
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.  

La configuración de una aplicación Entity Framework se puede especificar en un archivo de configuración (app.config/web.config) o mediante código. Este último se conoce como configuración basada en código.  

La configuración de un archivo de configuración se describe en un [artículo independiente](xref:ef6/fundamentals/configuring/config-file). El archivo de configuración tiene prioridad sobre la configuración basada en código. En otras palabras, si se establece una opción de configuración en el código y en el archivo de configuración, se usa la configuración del archivo de configuración.  

## <a name="using-dbconfiguration"></a>Usar DbConfiguration  

La configuración basada en código en EF6 y versiones posteriores se consigue mediante la creación de una subclase de System.Data.Entity.Config. DbConfiguration. Se deben seguir las siguientes directrices para crear subclases de DbConfiguration:  

- Cree solo una clase DbConfiguration para la aplicación. Esta clase especifica la configuración de todo el dominio de aplicación.  
- Coloque la clase DbConfiguration en el mismo ensamblado que la clase DbContext. (Consulte la sección *mover DbConfiguration* si desea cambiar esto).  
- Asigne a la clase DbConfiguration un constructor sin parámetros público.  
- Establezca las opciones de configuración llamando a métodos DbConfiguration protegidos desde dentro de este constructor.  

Si sigue estas instrucciones, EF podrá detectar y usar la configuración automáticamente con las herramientas que necesiten tener acceso a su modelo y cuando se ejecute la aplicación.  

## <a name="example"></a>Ejemplo  

Una clase derivada de DbConfiguration podría tener el siguiente aspecto:  

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

Esta clase establece EF para usar la estrategia de ejecución de SQL Azure: para reintentar automáticamente las operaciones de base de datos con errores y para usar la base de datos local para las bases de datos creadas por Convención desde Code First.  

## <a name="moving-dbconfiguration"></a>Mover DbConfiguration  

Hay casos en los que no es posible colocar la clase DbConfiguration en el mismo ensamblado que la clase DbContext. Por ejemplo, puede tener dos clases DbContext cada una en ensamblados distintos. Hay dos opciones para controlar esto.  

La primera opción es usar el archivo de configuración para especificar la instancia de DbConfiguration que se va a usar. Para ello, establezca el atributo codeConfigurationType de la sección entityFramework. Por ejemplo:  

``` xml
<entityFramework codeConfigurationType="MyNamespace.MyDbConfiguration, MyAssembly">
    ...Your EF config...
</entityFramework>
```  

El valor de codeConfigurationType debe ser el ensamblado y el nombre completo del espacio de nombres de la clase DbConfiguration.  

La segunda opción consiste en colocar DbConfigurationTypeAttribute en la clase de contexto. Por ejemplo:  

``` csharp  
[DbConfigurationType(typeof(MyDbConfiguration))]
public class MyContextContext : DbContext
{
}
```  

El valor que se pasa al atributo puede ser el tipo DbConfiguration, como se muestra arriba, o el ensamblado y el nombre de espacio de nombres de tipo completo String. Por ejemplo:  

``` csharp
[DbConfigurationType("MyNamespace.MyDbConfiguration, MyAssembly")]
public class MyContextContext : DbContext
{
}
```  

## <a name="setting-dbconfiguration-explicitly"></a>Establecer DbConfiguration explícitamente  

Hay algunas situaciones en las que puede ser necesaria la configuración antes de que se haya usado cualquier tipo DbContext. Estos son algunos ejemplos:  

- Usar DbModelBuilder para compilar un modelo sin un contexto  
- Usar algún otro código de la utilidad o del marco de trabajo que usa un DbContext donde ese contexto se usa antes de que se use el contexto de la aplicación  

En estas situaciones, EF no puede detectar la configuración automáticamente y, en su lugar, debe realizar una de las siguientes acciones:  

- Establezca el tipo DbConfiguration en el archivo de configuración, como se describe en la sección anterior *DbConfiguration*
- Llame al método estático DbConfiguration. SetConfiguration durante el inicio de la aplicación.  

## <a name="overriding-dbconfiguration"></a>Invalidar DbConfiguration  

Hay algunas situaciones en las que es necesario invalidar el conjunto de configuración en DbConfiguration. Normalmente, no lo hacen los desarrolladores de aplicaciones, sino los proveedores y complementos de terceros que no pueden usar una clase derivada de DbConfiguration.  

Para ello, EntityFramework permite registrar un controlador de eventos que puede modificar la configuración existente justo antes de que se bloquee.  También proporciona un método de azúcar específico para reemplazar cualquier servicio devuelto por el localizador del servicio EF. Así es como se pretende usar:  

- En el inicio de la aplicación (antes de que se use EF), el complemento o el proveedor deben registrar el método de control de eventos para este evento. (Tenga en cuenta que esto debe ocurrir antes de que la aplicación use EF).  
- El controlador de eventos realiza una llamada a ReplaceService para cada servicio que debe reemplazarse.  

Por ejemplo, para reemplazar IDbConnectionFactory y DbProviderService, debe registrar un controlador similar al siguiente:  

``` csharp
DbConfiguration.Loaded += (_, a) =>
   {
       a.ReplaceService<DbProviderServices>((s, k) => new MyProviderServices(s));
       a.ReplaceService<IDbConnectionFactory>((s, k) => new MyConnectionFactory(s));
   };
```  

En el código anterior MyProviderServices y MyConnectionFactory se representan las implementaciones del servicio.  

También puede agregar controladores de dependencia adicionales para obtener el mismo efecto.  

Tenga en cuenta que también puede incluir DbProviderFactory de esta manera, pero esto solo afectará a EF y no usará el DbProviderFactory fuera de EF. Por esta razón, es probable que desee seguir ajustando DbProviderFactory como antes.  

También debe tener en cuenta los servicios que se ejecutan externamente en la aplicación; por ejemplo, cuando se ejecutan migraciones desde la consola del administrador de paquetes. Al ejecutar la migración desde la consola, intentará encontrar su DbConfiguration. Sin embargo, tanto si obtendrá el servicio ajustado como si no depende de dónde se registró el controlador de eventos. Si se registra como parte de la construcción de DbConfiguration, el código debe ejecutarse y el servicio debe ajustarse. Normalmente, este no es el caso y esto significa que las herramientas no obtendrán el servicio ajustado.  
