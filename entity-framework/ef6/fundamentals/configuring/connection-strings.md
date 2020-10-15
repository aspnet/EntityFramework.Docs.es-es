---
title: 'Cadenas de conexión y modelos: EF6'
description: Cadenas de conexión y modelos de Entity Framework 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/configuring/connection-strings
ms.openlocfilehash: c60acb965b7062f3cd35dab94ee8dfe48394969a
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063392"
---
# <a name="connection-strings-and-models"></a>Cadenas de conexión y modelos
En este tema se explica cómo Entity Framework detecta qué conexión de base de datos se va a usar y cómo se puede cambiar. Los modelos creados con Code First y EF Designer se describen en este tema.  

Normalmente, una aplicación Entity Framework usa una clase derivada de DbContext. Esta clase derivada llamará a uno de los constructores de la clase base DbContext para controlar:  

- Cómo se conectará el contexto a una base de datos, es decir, cómo se encuentra o se usa una cadena de conexión  
- Si el contexto usará calcular un modelo con Code First o cargar un modelo creado con el diseñador de EF  
- Opciones avanzadas adicionales  

Los siguientes fragmentos muestran algunas de las formas en que se pueden usar los constructores DbContext.  

## <a name="use-code-first-with-connection-by-convention"></a>Usar Code First con conexión por Convención  

Si no ha realizado ninguna otra configuración en la aplicación, la llamada al constructor sin parámetros en DbContext hará que DbContext se ejecute en modo de Code First con una conexión de base de datos creada por Convención. Por ejemplo:  

``` csharp  
namespace Demo.EF
{
    public class BloggingContext : DbContext
    {
        public BloggingContext()
        // C# will call base class parameterless constructor by default
        {
        }
    }
}
```  

En este ejemplo DbContext usa el nombre completo del espacio de nombres de la clase de contexto derivada (demo. EF. BloggingContext) como nombre de la base de datos y crea una cadena de conexión para esta base de datos mediante SQL Express o LocalDB. Si ambos están instalados, se usará SQL Express.  

Visual Studio 2010 incluye SQL Express de forma predeterminada y Visual Studio 2012 y versiones posteriores incluyen LocalDB. Durante la instalación, el paquete NuGet de EntityFramework comprueba qué servidor de base de datos está disponible. Después, el paquete NuGet actualizará el archivo de configuración estableciendo el servidor de base de datos predeterminado que Code First usa al crear una conexión por Convención. Si SQL Express se está ejecutando, se usará. Si SQL Express no está disponible, LocalDB se registrará como predeterminado en su lugar. No se realiza ningún cambio en el archivo de configuración si ya contiene un valor para el generador de conexiones predeterminado.  

## <a name="use-code-first-with-connection-by-convention-and-specified-database-name"></a>Usar Code First con la conexión por Convención y el nombre de base de datos especificado  

Si no ha realizado ninguna otra configuración en la aplicación, la llamada al constructor de cadena en DbContext con el nombre de la base de datos que desea usar hará que DbContext se ejecute en modo de Code First con una conexión de base de datos creada por Convención en la base de datos de ese nombre. Por ejemplo:  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingDatabase")
    {
    }
}
```  

En este ejemplo DbContext usa "BloggingDatabase" como nombre de la base de datos y crea una cadena de conexión para esta base de datos mediante SQL Express (instalado con Visual Studio 2010) o LocalDB (instalado con Visual Studio 2012). Si ambos están instalados, se usará SQL Express.  

## <a name="use-code-first-with-connection-string-in-appconfigwebconfig-file"></a>Usar Code First con la cadena de conexión en el archivo de app.config/web.config  

Puede optar por colocar una cadena de conexión en el archivo de app.config o web.config. Por ejemplo:  

``` xml  
<configuration>
  <connectionStrings>
    <add name="BloggingCompactDatabase"
         providerName="System.Data.SqlServerCe.4.0"
         connectionString="Data Source=Blogging.sdf"/>
  </connectionStrings>
</configuration>
```  

Esta es una manera fácil de indicar a DbContext que use un servidor de base de datos que no sea SQL Express o LocalDB; el ejemplo anterior especifica una base de datos de SQL Server Compact Edition.  

Si el nombre de la cadena de conexión coincide con el nombre del contexto (ya sea con o sin calificación de espacio de nombres), DbContext lo buscará cuando se use el constructor sin parámetros. Si el nombre de la cadena de conexión es diferente del nombre del contexto, puede indicar a DbContext que use esta conexión en el modo Code First pasando el nombre de la cadena de conexión al constructor DbContext. Por ejemplo:  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("BloggingCompactDatabase")
    {
    }
}
```  

Como alternativa, puede usar el formato "name = \<connection string name\> " para la cadena que se pasa al constructor DbContext. Por ejemplo:  

``` csharp  
public class BloggingContext : DbContext
{
    public BloggingContext()
        : base("name=BloggingCompactDatabase")
    {
    }
}
```  

Este formulario hace que sea explícito esperar que la cadena de conexión se encuentre en el archivo de configuración. Se producirá una excepción si no se encuentra una cadena de conexión con el nombre especificado.  

## <a name="databasemodel-first-with-connection-string-in-appconfigwebconfig-file"></a>Base de datos/Model First con una cadena de conexión en el archivo de app.config/web.config  

Los modelos creados con EF Designer son diferentes de Code First en que el modelo ya existe y no se genera a partir del código cuando se ejecuta la aplicación. El modelo normalmente existe como archivo EDMX en el proyecto.  

El diseñador agregará una cadena de conexión EF al archivo app.config o web.config. Esta cadena de conexión es especial, ya que contiene información sobre cómo encontrar la información en el archivo EDMX. Por ejemplo:  

``` xml  
<configuration>  
  <connectionStrings>  
    <add name="Northwind_Entities"  
         connectionString="metadata=res://*/Northwind.csdl|  
                                    res://*/Northwind.ssdl|  
                                    res://*/Northwind.msl;  
                           provider=System.Data.SqlClient;  
                           provider connection string=  
                               &quot;Data Source=.\sqlexpress;  
                                     Initial Catalog=Northwind;  
                                     Integrated Security=True;  
                                     MultipleActiveResultSets=True&quot;"  
         providerName="System.Data.EntityClient"/>  
  </connectionStrings>  
</configuration>
```  

El diseñador de EF también generará código que indica a DbContext que use esta conexión pasando el nombre de la cadena de conexión al constructor DbContext. Por ejemplo:  

``` csharp  
public class NorthwindContext : DbContext
{
    public NorthwindContext()
        : base("name=Northwind_Entities")
    {
    }
}
```  

DbContext sabe cargar el modelo existente (en lugar de usar Code First para calcularlo a partir del código) porque la cadena de conexión es una cadena de conexión EF que contiene los detalles del modelo que se va a usar.  

## <a name="other-dbcontext-constructor-options"></a>Otras opciones del constructor DbContext  

La clase DbContext contiene otros constructores y patrones de uso que permiten algunos escenarios más avanzados. Algunas de éstas son:  

- Puede usar la clase DbModelBuilder para crear un modelo de Code First sin crear instancias de una instancia de DbContext. El resultado de este es un objeto DbModel. Después, puede pasar este objeto DbModel a uno de los constructores DbContext cuando esté listo para crear la instancia de DbContext.  
- Puede pasar una cadena de conexión completa a DbContext en lugar de solo la base de datos o el nombre de la cadena de conexión. De forma predeterminada, esta cadena de conexión se usa con el proveedor System. Data. SqlClient; Esto se puede cambiar estableciendo una implementación diferente de IConnectionFactory en el contexto. Database. DefaultConnectionFactory.  
- Puede usar un objeto DbConnection existente pasándolo a un constructor DbContext. Si el objeto de conexión es una instancia de EntityConnection, se usará el modelo especificado en la conexión en lugar de calcular un modelo mediante Code First. Si el objeto es una instancia de algún otro tipo (por ejemplo, SqlConnection), el contexto lo utilizará para el modo de Code First.  
- Puede pasar un ObjectContext existente a un constructor DbContext para crear un DbContext que ajuste el contexto existente. Se puede usar para las aplicaciones existentes que usan ObjectContext pero que desean aprovechar DbContext en algunas partes de la aplicación.  
