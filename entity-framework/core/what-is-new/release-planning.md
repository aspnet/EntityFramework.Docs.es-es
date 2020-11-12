---
title: Planeamiento de versiones de EF Core
description: Información sobre cómo se realizan la planificación y el lanzamiento de Entity Framework Core
author: ajcvickers
ms.date: 01/28/2020
uid: core/what-is-new/release-planning
ms.openlocfilehash: f84b8cef40a74245575df6013d94fcda5738e229
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429149"
---
# <a name="release-planning-process"></a>Proceso de planeamiento de versiones

A menudo nos preguntan cómo se eligen características específicas para incluirlas en una versión concreta.
En este documento se describe el proceso que usamos.
El proceso evoluciona continuamente a medida que encontramos mejores formas de planeación, pero las ideas generales siguen siendo las mismas.

## <a name="different-kinds-of-releases"></a>Diferentes tipos de versiones

Los distintos tipos de versión contienen distintos tipos de cambios.
Esto significa que, a su vez, el planeamiento de versiones es diferente para cada tipo de versión.

### <a name="patch-releases"></a>Versiones de revisión

Las versiones de revisión solo cambian la parte de "revisión" de la versión.
Por ejemplo, EF Core 3.1. **1** es una versión en la que se han revisado los problemas encontrados en EF Core 3.1. **0**.

Las versiones de revisión están diseñadas para corregir errores críticos para los clientes.
Esto significa que las versiones de revisión no incluyen nuevas características.
No se permiten cambios de API en las versiones de revisión, excepto en circunstancias especiales.

La dificultad para hacer un cambio en una versión de revisión es muy alta.
Esto se debe a que es fundamental que las versiones de revisión no presenten nuevos errores.
Por lo tanto, el proceso de toma de decisiones enfatiza el alto valor y el riesgo bajo.

Es más probable que revisemos un problema si se cumple una de las siguientes condiciones:

* Afecta a varios clientes.
* Es una regresión de una versión anterior.
* El error provoca daños en los datos.

Es menos probable que revisemos un problema si se cumple una de las siguientes condiciones:

* Existen soluciones alternativas razonables.
* La corrección implica un alto riesgo de interrumpir algo más.
* El error es un caso límite.

La dificultad aumenta gradualmente a lo largo de la vigencia de una versión de [soporte técnico a largo plazo (LTS)](https://dotnet.microsoft.com/platform/support/policy/dotnet-core). Esto se debe a que las versiones de LTS enfatizan la estabilidad.

La decisión final sobre si un problema se revisa o no la realizan los directores de .NET en Microsoft.

### <a name="minor-releases"></a>Versiones secundarias

Las versiones secundarias solo cambian la parte "secundaria" de la versión.
Por ejemplo, EF Core 3. **1**.0 es una versión que mejora EF Core 3. **0**.0.

Versiones secundarias:

* Están diseñadas para mejorar la calidad y las características de la versión anterior.
* Normalmente contienen correcciones de errores y nuevas características.
* No incluyen cambios importantes intencionados.
* Tienen algunas vistas previas de versiones preliminares insertadas en NuGet.

### <a name="major-releases"></a>Versiones principales:

Las versiones principales cambian el número de versión "principal" de EF.
Por ejemplo, EF Core  **3**.0.0 es una versión principal que da un gran paso adelante con respecto a EF Core 2.2.x.

Versiones principales:

* Están diseñadas para mejorar la calidad y las características de la versión anterior.
* Normalmente contienen correcciones de errores y nuevas características.
  * Algunas de las nuevas características pueden ser cambios fundamentales en el funcionamiento de EF Core.
* Normalmente, incluyen cambios importantes intencionados.
  * Los cambios importantes son parte necesaria de la evolución de EF Core a medida que aprendemos.
  * Sin embargo, estudiamos mucho la realización de los cambios importantes debido al posible impacto que puedan tener en el cliente. Es posible que hayamos sido demasiado radicales con cambios importantes en el pasado. De ahora en adelante, nos esforzaremos por minimizar los cambios que interrumpan el funcionamiento de las aplicaciones y reducir los cambios que interrumpan el funcionamiento de los proveedores de bases de datos y las extensiones.
* Tienen muchas vistas previas de versiones preliminares insertadas en NuGet.

## <a name="planning-for-majorminor-releases"></a>Planeación de versiones principales o secundarias

### <a name="github-issue-tracking"></a>Seguimiento de problemas de GitHub

GitHub ([https://github.com/dotnet/efcore](https://github.com/dotnet/efcore)) es la fuente fiable de toda la planeación de EF Core.

Los problemas en GitHub cuentan con lo siguiente:

* Un estado
  * Si un problema está [abierto](https://github.com/dotnet/efcore/issues) significa que no se ha abordado.
  * Si un problema está [cerrado](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aclosed) significa que se ha abordado.
    * Todos los problemas que se han corregido se [etiquetan con el estado “closed-fixed” (cerrado: corregido)](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Aclosed-fixed+is%3Aclosed). Un problema con la etiqueta “closed-fixed” está corregido y combinado, pero es posible que no se haya publicado.
    * Las demás etiquetas de `closed-` indican otras razones por las que se ha cerrado un problema. Por ejemplo, los duplicados se etiquetan con “closed-duplicate” (cerrado: duplicado).
* Un tipo
  * El tipo [Bugs (Errores)](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-bug) representa errores.
  * El tipo [Enhancements (Mejoras)](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Atype-enhancement) corresponde a nuevas características o una mejor funcionalidad en las características existentes.
* Un hito
  * [Si un problema no tiene hito](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+no%3Amilestone), significa que el equipo lo está considerando. La decisión sobre qué hacer con el problema aún no se ha tomado o se está considerando cambiarla.
  * [Los problemas en el hito Backlog (Trabajo pendiente)](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3ABacklog) son elementos en los que el equipo de EF considerará que debe trabajar en una versión futura.
    * Es posible que los problemas del trabajo pendiente tengan la [etiqueta consider-for-next-release (considerar en la próxima versión)](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+label%3Aconsider-for-next-release), lo que indica que este elemento de trabajo es una de las prioridades para la próxima versión.
  * Los problemas abiertos en un hito con versión son elementos en los que el equipo tiene previsto trabajar en esa versión. Por ejemplo, [estos son los problemas con los que tenemos previsto trabajar en EF Core 5.0](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A5.0.0).
  * Los problemas cerrados en un hito con versión son los que se completan para esa versión. Tenga en cuenta que es posible que la versión todavía no se haya lanzado. Por ejemplo, [estos son los problemas completados para EF Core 3.0](https://github.com/dotnet/efcore/issues?q=is%3Aissue+milestone%3A3.0.0+is%3Aclosed).
* Votos
  * La votación es la mejor manera de que el usuario indique que un problema es importante.
  * Para votar, solo tiene que agregar un "pulgar" 👍 al problema. Por ejemplo, [estos son los problemas más votados](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc).
  * También debe incluir un comentario con las razones específicas por las que necesita la característica si cree que puede ser útil. Comentar "+ 1" o algo similar no agrega ningún valor.

### <a name="the-planning-process"></a>Proceso de planeación

El proceso de planeación es más complicado que simplemente tomar las principales características más solicitadas del trabajo pendiente.
Esto se debe a que se recopilan comentarios de varias partes interesadas de varias maneras.
Por lo tanto, formamos una versión basada en lo siguiente:

* Comentarios de los clientes
* Comentarios de otras partes interesadas
* Dirección estratégica
* Recursos disponibles
* Programación

Algunas de las preguntas que formulamos son:

1. **¿Cuántos desarrolladores creemos que usarán la característica y en qué medida mejorará las aplicaciones o la experiencia?** Para responder a esta pregunta, recopilamos información de varias fuentes, y los comentarios y los votos son una de ellas. Las involucraciones específicas con clientes importantes son otra.

2. **¿Qué soluciones alternativas pueden adoptar los usuarios si todavía no se ha implementado una característica?** Por ejemplo, hay muchos desarrolladores que pueden asignar una tabla de unión para poder trabajar a pesar de la falta de compatibilidad múltiple de forma nativa. Obviamente, no todos los desarrolladores quieren hacerlo, pero muchos pueden, y eso se considera un factor decisivo.

3. **¿La implementación de esta característica hará evolucionar la arquitectura de EF Core tanto como para poder implementar otras características?** Normalmente tienen preferencia las características que actúan como bloques de creación de otras características. Por ejemplo, las entidades contenedoras de propiedades pueden ayudarnos a avanzar hacia la compatibilidad de varios a varios, y los constructores de entidades han habilitado nuestra compatibilidad de carga diferida.

4. **¿La característica es un punto de extensibilidad?** Los puntos de extensibilidad suelten tener preferencia sobre las características normales porque permiten que los desarrolladores puedan crear sus propios comportamientos y compensar las funcionalidades que faltan.

5. **¿Cuál es la sinergia de la característica cuando se usa en combinación con otros productos?** Tienen preferencia las características que permiten o mejoran significativamente la experiencia de uso de EF Core con otros productos, como .NET Core, la última versión de Visual Studio, Microsoft Azure, etc.

6. **¿Cuáles son las habilidades de las personas disponibles para trabajar en una característica y cómo se aprovechan mejor estos recursos?** Todos los miembros del equipo de EF, e incluso los colaboradores de la comunidad, tienen diferentes niveles de experiencia en varias áreas, y tenemos que elaborar el plan de acuerdo con ello. Incluso aunque quisiéramos tener a todos trabajando en una característica específica, como las traducciones de GroupBy o las relaciones múltiples, no sería práctico.
