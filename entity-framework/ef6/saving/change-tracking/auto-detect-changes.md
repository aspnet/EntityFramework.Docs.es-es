---
title: 'Detección automática de cambios: EF6'
description: Detección automática de cambios en Entity Framework 6
author: divega
ms.date: 10/23/2016
uid: ef6/saving/change-tracking/auto-detect-changes
ms.openlocfilehash: aa8b94d843d99a8f040bdb065297d2e19b4770d7
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073787"
---
# <a name="automatic-detect-changes"></a>Detección automática de cambios
Al usar la mayoría de las entidades POCO, la determinación de cómo ha cambiado una entidad (y, por lo tanto, las actualizaciones que se deben enviar a la base de datos) se controla mediante el algoritmo de detección de cambios. Detectar cambios funciona detectando las diferencias entre los valores de propiedad actuales de la entidad y los valores de propiedad originales que se almacenan en una instantánea cuando se consulta o se adjunta la entidad. Las técnicas que se muestran en este tema se aplican igualmente a los modelos creados con Code First y EF Designer.  

De forma predeterminada, Entity Framework realiza la detección automática de cambios cuando se llama a los métodos siguientes:  

- DbSet.Find  
- DbSet. local  
- DbSet. Add  
- DbSet. AddRange
- DbSet. Remove  
- DbSet. RemoveRange
- DbSet. Attach  
- DbContext.SaveChanges  
- DbContext. GetValidationErrors  
- DbContext.Entry  
- DbChangeTracker. entradas  

## <a name="disabling-automatic-detection-of-changes"></a>Deshabilitación de la detección automática de cambios  

Si está realizando un seguimiento de muchas entidades en el contexto y llama a uno de estos métodos muchas veces en un bucle, puede obtener mejoras de rendimiento significativas desactivando la detección de cambios durante el bucle. Por ejemplo:  

``` csharp
using (var context = new BloggingContext())
{
    try
    {
        context.Configuration.AutoDetectChangesEnabled = false;

        // Make many calls in a loop
        foreach (var blog in aLotOfBlogs)
        {
            context.Blogs.Add(blog);
        }
    }
    finally
    {
        context.Configuration.AutoDetectChangesEnabled = true;
    }
}
```  

No olvide volver a habilitar la detección de cambios después del bucle: hemos usado try/finally para asegurarse de que siempre se vuelve a habilitar aunque el código del bucle produzca una excepción.  

Una alternativa a deshabilitar y volver a habilitar consiste en dejar la detección automática de los cambios desactivados en todo momento y en el contexto de llamada. ChangeTracker. DetectChanges explícitamente o usar los proxies de seguimiento de cambios diligentemente. Ambas opciones están avanzadas y pueden introducir fácilmente errores sutiles en la aplicación, por lo que deben usarse con cuidado.  

Si necesita agregar o quitar muchos objetos de un contexto, considere la posibilidad de usar DbSet. AddRange y DbSet. RemoveRange. Estos métodos detectan automáticamente los cambios solo una vez después de que se completen las operaciones de agregar o quitar. 
