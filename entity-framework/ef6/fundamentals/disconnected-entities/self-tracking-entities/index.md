---
title: 'Entidades de autoseguimiento: EF6'
description: Entidades de autoseguimiento en Entity Framework 6
author: divega
ms.date: 10/23/2016
ms.assetid: 5e60f5be-7bbb-4bf8-835e-0ac808d6c84a
uid: ef6/fundamentals/disconnected-entities/self-tracking-entities/index
ms.openlocfilehash: 0e771c0f147589112779359ab8c06344eb05b8fc
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89616187"
---
# <a name="self-tracking-entities"></a>Entidades de autoseguimiento

> [!IMPORTANT]
> Ya no se recomienda usar la plantilla Entidades de autoseguimiento. Solo sigue estando disponible para la compatibilidad con las aplicaciones existentes. Si la aplicación necesita trabajar con gráficos desconectados de entidades, considere otras alternativas, como [Trackable Entities](https://trackableentities.github.io/), que es una tecnología similar a Entidades de autoseguimiento pero que la comunidad desarrolla de forma más activa, o escriba código personalizado mediante la API de seguimiento de cambios de bajo nivel.

En una aplicación basada en Entity Framework, el responsable de realizar el seguimiento de los cambios en los objetos es un contexto. Luego se usa el método SaveChanges para almacenar los cambios en la base de datos. Cuando se trabaja con aplicaciones de n niveles, los objetos de entidad generalmente están desconectados del contexto y es necesario decidir cómo realizar el seguimiento de los cambios y notificar esos cambios al contexto. Las entidades de autoseguimiento (STE) pueden ayudar a realizar el seguimiento de los cambios en cualquier nivel y luego reproducir estos cambios en un contexto que se vaya a guardar.  

Use STE solo si el contexto no está disponible en un nivel en el que se realizan cambios en el gráfico del objeto. Si el contexto está disponible, no hay necesidad de usar STE, ya que él mismo se encarga del seguimiento de los cambios.  

Este elemento de plantilla genera dos archivos .tt (plantilla de texto):  

- El archivo **\<model name\>.tt** genera los tipos de entidad y una clase del asistente que contiene la lógica de seguimiento de cambios que usan las entidades de autoseguimiento y los métodos de extensión que permiten establecer el estado en las entidades de autoseguimiento.  
- El archivo **\<model name\>.Context.tt** genera un contexto derivado y una clase de extensión que contiene métodos **ApplyChanges** para las clases **ObjectContext** y **ObjectSet**. Estos métodos examinan la información del seguimiento de cambios contenida en el grafo de entidades con seguimiento propio para deducir el conjunto de operaciones que se deben realizar con el fin de guardar los cambios en la base de datos.  

## <a name="get-started"></a>Introducción  

Para comenzar, visite la página [Self-Tracking Entities Walkthrough](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/walkthrough) (Tutorial sobre entidades de autoseguimiento).  

## <a name="functional-considerations-when-working-with-self-tracking-entities"></a>Consideraciones funcionales al trabajar con entidades de seguimiento propio  
> [!IMPORTANT]
> Ya no se recomienda usar la plantilla Entidades de autoseguimiento. Solo sigue estando disponible para la compatibilidad con las aplicaciones existentes. Si la aplicación necesita trabajar con gráficos desconectados de entidades, considere otras alternativas, como [Trackable Entities](https://trackableentities.github.io/), que es una tecnología similar a Entidades de autoseguimiento pero que la comunidad desarrolla de forma más activa, o escriba código personalizado mediante la API de seguimiento de cambios de bajo nivel.

Tenga en cuenta lo siguiente al trabajar con entidades de seguimiento propio:  

- Asegúrese de que el proyecto cliente tenga una referencia al ensamblado con los tipos de entidad. Si agrega solo la referencia de servicio al proyecto cliente, este proyecto usará los tipos de proxy WCF y no los tipos reales de entidad de seguimiento propio. Es decir, no obtendrá las características de notificación automatizadas que administran el seguimiento de las entidades en el cliente. Si, a propósito, no desea incluir los tipos de entidad, deberá establecer manualmente la información de seguimiento de cambios en el cliente de los cambios que se van a devolver al servicio.  
- Las llamadas a la operación de servicio no deben tener estado y deben crear una nueva instancia del contexto del objeto. Asimismo, se recomienda crear el contexto del objeto en un bloque **using**.  
- Si envía el gráfico modificado en el cliente al servicio y luego planea seguir trabajando con el mismo gráfico en el cliente, debe iterar manualmente el gráfico y llamar al método **AcceptChanges** en cada objeto para restablecer la herramienta de seguimiento de cambios.  

    > Si los objetos del gráfico contienen propiedades con valores generados por la base de datos (por ejemplo, valores de identidad o simultaneidad), Entity Framework reemplaza los valores de estas propiedades por los valores generados por la base de datos después de llamar al método **SaveChanges**. Puede implementar la operación del servicio para que devuelva objetos guardados o una lista de los valores de propiedad generados de los objetos devueltos al cliente. El cliente debe reemplazar las instancias del objeto o los valores de propiedad del objeto con los objetos o los valores de propiedad devueltos desde la operación de servicio.  
- Si se combinan gráficos de varias solicitudes de servicio, se pueden presentar objetos con valores de clave duplicada en el gráfico resultante. Entity Framework no quita los objetos con claves duplicadas cuando llama al método **ApplyChanges**, sino que produce una excepción. Para evitar tener gráficos con valores de clave duplicados, siga uno de los modelos descritos en el siguiente blog: [Self-Tracking Entities: ApplyChanges and duplicate entities](https://go.microsoft.com/fwlink/?LinkID=205119&clcid=0x409) (Entidades de autoseguimiento: ApplyChanges y entidades duplicadas).  
- Cuando se cambia la relación entre objetos mediante el establecimiento de la propiedad de clave externa, la propiedad de navegación de referencia se establece en NULL y no se sincroniza con la entidad de seguridad adecuada en el cliente. Después de adjuntar el gráfico al contexto del objeto (por ejemplo, después de llamar al método **ApplyChanges**), se sincronizan las propiedades de clave externa y de navegación.  

    > No disponer de ninguna propiedad de navegación de referencia sincronizada con el objeto principal adecuado podría representar un problema si ha especificado la eliminación en cascada en la relación de clave externa. Si elimina la entidad de seguridad, la eliminación no se propagará a los objetos dependientes. Si ha especificado eliminaciones en cascada, use las propiedades de navegación para cambiar las relaciones en vez de establecer la propiedad de clave externa.  
- Las entidades con seguimiento propio no están habilitadas para realizar una carga diferida.  
- La serialización binaria y la serialización a los objetos de administración de estado de ASP.NET no se admiten en las entidades de autoseguimiento. Sin embargo, puede personalizar la plantilla para agregar la compatibilidad de serialización binaria. Para obtener más información, vea [Using Binary Serialization and ViewState with Self-Tracking Entities](https://go.microsoft.com/fwlink/?LinkId=199208) (Uso de la serialización binaria y ViewState con entidades de autoseguimiento).  

## <a name="security-considerations"></a>Consideraciones sobre la seguridad  

Al trabajar con entidades de autoseguimiento, deben contemplarse las siguientes consideraciones de seguridad:  

- Un servicio no debería confiar en solicitudes de recuperación o actualización de datos que proceden de un cliente que no es de plena confianza o transmitidas a través de un canal que no es de plena confianza. Se debe autenticar un cliente: es necesario utilizar un canal seguro o la envoltura de mensajes. Se deben validar las solicitudes de los clientes de actualización o recuperación de datos para asegurarse de que se ajustan a los cambios legítimos y previstos del escenario dado.  
- Evite utilizar información confidencial como claves de entidad (por ejemplo, números de seguridad social). Esto reduce la posibilidad de que pueda serializarse de forma inadvertida información confidencial en los grafos de entidades con seguimiento propio de un cliente que no es de plena confianza. Con asociaciones independientes, se podría enviar también al cliente la clave original de una entidad que esté relacionada con la que se está serializando.  
- Para evitar propagar mensajes de excepción con datos confidenciales en el nivel de cliente, las llamadas a **ApplyChanges** y **SaveChanges** en el nivel del servidor se deben encapsular en un código de control de excepciones.  
