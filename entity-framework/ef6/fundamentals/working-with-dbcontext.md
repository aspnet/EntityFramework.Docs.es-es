---
title: 'Trabajo con DbContext: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: b0e6bddc-8a87-4d51-b1cb-7756df938c23
ms.openlocfilehash: d961ffd8bed7f5b2f82dcfa30fc0241b7437be50
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413886"
---
# <a name="working-with-dbcontext"></a>Trabajar con DbContext

Para usar Entity Framework para consultar, insertar, actualizar y eliminar datos mediante objetos .NET, primero debe [crear un modelo](~/ef6/modeling/index.md) que asigne las entidades y relaciones que se definen en el modelo a las tablas de una base de datos.

Una vez que tiene un modelo, la clase principal con la que interactúa la aplicación es `System.Data.Entity.DbContext` (a menudo se conoce como la clase de contexto). Puede usar un DbContext asociado a un modelo para:
- Escribir y ejecutar consultas   
- Materializar resultados de una consulta como objetos entidad
- Realizar un seguimiento de los cambios realizados en esos objetos
- Almacenar los cambios del objeto en la base de datos
- Enlazar objetos en memoria a controles de interfaz de usuario

En esta página se proporcionan instrucciones sobre cómo administrar la clase de contexto.  

## <a name="defining-a-dbcontext-derived-class"></a>Definir una clase derivada de DbContext  

La manera recomendada para trabajar con el contexto es definir una clase que derive de DbContext y exponga las propiedades DbSet que representan colecciones de las entidades especificadas en el contexto. Si está trabajando con el diseñador de EF, el contexto se generará automáticamente. Si está trabajando con Code First, normalmente usted mismo escribirá el contexto.  

``` csharp
public class ProductContext : DbContext
{
    public DbSet<Category> Categories { get; set; }
    public DbSet<Product> Products { get; set; }
}
```  

Una vez que tenga un contexto, debe consultar, agregar (mediante métodos `Add` o `Attach`) o quitar (mediante `Remove`) entidades en el contexto mediante estas propiedades. El acceso a una propiedad de `DbSet` en un objeto de contexto representa una consulta de inicio que devuelve todas las entidades del tipo especificado. Tenga en cuenta acceder a la propiedad no ejecutará la consulta. Una consulta se ejecuta cuando:  

- Se enumera mediante una instrucción `foreach` (C#) o `For Each` (Visual Basic).  
- Se enumera mediante una operación de recopilación como `ToArray`, `ToDictionary`o `ToList`.  
- Los operadores de LINQ como `First` o `Any` se especifican en la parte más externa de la consulta.  
- Se llama a uno de los métodos siguientes: el método de extensión `Load`, `DbEntityEntry.Reload`, `Database.ExecuteSqlCommand`y `DbSet<T>.Find`, si no se encuentra una entidad con la clave especificada en el contexto.  

## <a name="lifetime"></a>Vigencia  

La duración del contexto comienza cuando se crea y finaliza cuando la instancia se elimina o es recogida por el recolector de basura. Use el **uso** de si desea que todos los recursos que controla el contexto se eliminen al final del bloque. Cuando se usa **con**, el compilador crea automáticamente un bloque try/finally y llama a Dispose en el bloque **Finally** .  

``` csharp
public void UseProducts()
{
    using (var context = new ProductContext())
    {     
        // Perform data access using the context
    }
}
```  

Estas son algunas directrices generales a la hora de decidir la duración del contexto:  

- Al trabajar con aplicaciones Web, use una instancia de contexto por solicitud.  
- Al trabajar con Windows Presentation Foundation (WPF) o Windows Forms, utilice una instancia de contexto por formulario. Esto le permite usar la funcionalidad de seguimiento de cambios que proporciona el contexto.  
- Si la instancia de contexto se crea un contenedor de inyección de dependencia, normalmente es responsabilidad del contenedor el eliminar el contexto.
- Si el contexto se crea en el código de la aplicación, no olvide eliminar el contexto cuando ya no sea necesario.  
- Al trabajar con un contexto de ejecución prolongada, tenga en cuenta lo siguiente:  
    - A medida que se cargan más objetos y sus referencias en la memoria, el consumo de memoria del contexto puede aumentar rápidamente. lo que puede ocasionar problemas de rendimiento.  
    - El contexto no es seguro para subprocesos, por lo tanto no debe compartirse entre varios subprocesos que realizan el trabajo en él al mismo tiempo.
    - Si una excepción provoca que el contexto entre en un estado irrecuperable, puede finalizar toda la aplicación.  
    - Las posibilidades de que haya problemas relacionados con la simultaneidad se incrementan a medida que aumenta la distancia entre el momento en que se consultan los datos y el momento en que se actualizan.  

## <a name="connections"></a>Conexiones  

De forma predeterminada, el contexto administra las conexiones a la base de datos. El contexto abre y cierra las conexiones según sea necesario. Por ejemplo, el contexto abre una conexión para ejecutar una consulta y, a continuación, cierra la conexión cuando se han procesado todos los conjuntos de resultados.  

Hay casos en los que se desea más control sobre el momento en que se abre y cierra la conexión. Por ejemplo, al trabajar con SQL Server Compact, a menudo se recomienda mantener una conexión abierta independiente con la base de datos mientras dure la aplicación para mejorar el rendimiento. Puede administrar este proceso manualmente mediante la propiedad `Connection`.  
