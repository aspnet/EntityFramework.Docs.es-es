---
title: Consideraciones de rendimiento para EF4, EF5 y EF6-EF6
description: Consideraciones de rendimiento para Entity Framework 4, 5 y 6
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/perf-whitepaper
ms.openlocfilehash: ae9374401b66f0493f7318ffcbfd9c4d6a24ada5
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92063431"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>Consideraciones de rendimiento para EF 4, 5 y 6
Por David Obando, Eric Dettinger y otros

Publicado: abril 2012

Última actualización: 2014 de mayo

------------------------------------------------------------------------

## <a name="1-introduction"></a>1. Introducción

Object-Relational marcos de asignación son una forma cómoda de proporcionar una abstracción para el acceso a datos en una aplicación orientada a objetos. En el caso de las aplicaciones .NET, se recomienda que el O el RM de Microsoft se Entity Framework. Sin embargo, con cualquier abstracción, el rendimiento puede ser un problema.

Estas notas del producto se han escrito para mostrar las consideraciones de rendimiento al desarrollar aplicaciones mediante Entity Framework, para ofrecer a los desarrolladores una idea de los algoritmos internos de Entity Framework que pueden afectar al rendimiento y para proporcionar sugerencias para la investigación y la mejora del rendimiento en sus aplicaciones que usan Entity Framework. Hay una serie de buenos temas sobre el rendimiento que ya están disponibles en la web y también hemos intentado apuntar a estos recursos siempre que sea posible.

El rendimiento es un tema complicado. Estas notas del producto están pensadas como recursos para ayudarle a tomar decisiones relacionadas con el rendimiento de las aplicaciones que usan Entity Framework. Hemos incluido algunas métricas de prueba para demostrar el rendimiento, pero estas métricas no están pensadas como indicadores absolutos del rendimiento que verá en la aplicación.

A efectos prácticos, en este documento se supone Entity Framework 4 se ejecuta en .NET 4,0 y Entity Framework 5 y 6 se ejecutan en .NET 4,5. Muchas de las mejoras de rendimiento realizadas para Entity Framework 5 residen en los componentes principales que se incluyen con .NET 4,5.

Entity Framework 6 es una versión fuera de banda y no depende de los componentes Entity Framework que se incluyen con .NET. Entity Framework 6 funcionan tanto en .NET 4,0 como en .NET 4,5 y pueden ofrecer una gran ventaja de rendimiento a aquellos que no se han actualizado desde .NET 4,0, pero que quieren los bits de Entity Framework más recientes en su aplicación. Cuando este documento menciona Entity Framework 6, hace referencia a la última versión disponible en el momento de redactar este documento: versión 6.1.0.

## <a name="2-cold-vs-warm-query-execution"></a>2. actividad en frío frente a ejecución de consultas en caliente

La primera vez que se realiza una consulta en un modelo determinado, el Entity Framework realiza una gran cantidad de trabajo en segundo plano para cargar y validar el modelo. A menudo, hacemos referencia a esta primera consulta como una consulta "fría".Las consultas adicionales en un modelo ya cargado se conocen como consultas "calientes" y son mucho más rápidas.

Vamos a tomar una vista de alto nivel del tiempo que se dedica a la ejecución de una consulta mediante Entity Framework y ver dónde están mejorando las cosas en Entity Framework 6.

**Primera ejecución de la consulta: consulta en frío**

| Escritura de código de usuario                                                                                     | Acción                    | Impacto en el rendimiento de EF4                                                                                                                                                                                                                                                                                                                                                                                                        | Impacto en el rendimiento de EF5                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Impacto en el rendimiento de EF6                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Creación de contexto          | Media                                                                                                                                                                                                                                                                                                                                                                                                                        | Media                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Creación de expresiones de consulta | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                           | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | Ejecución de consultas LINQ      | -Carga de metadatos: alta pero almacenada en caché <br/> -Vista de la generación: posiblemente muy alta pero almacenada en caché <br/> -Evaluación de parámetros: media <br/> -Traducción de consultas: mediana <br/> -Generación de materializador: medio pero almacenado en caché <br/> -Ejecución de la consulta de base de datos: potencialmente alta <br/> + Conexión. abrir <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Materialización de objetos: medio <br/> -Búsqueda de identidad: mediana | -Carga de metadatos: alta pero almacenada en caché <br/> -Vista de la generación: posiblemente muy alta pero almacenada en caché <br/> -Evaluación de parámetros: baja <br/> -Traducción de consultas: mediana pero almacenada en caché <br/> -Generación de materializador: medio pero almacenado en caché <br/> -Ejecución de la consulta de base de datos: potencialmente alta (mejores consultas en algunas situaciones) <br/> + Conexión. abrir <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Materialización de objetos: medio <br/> -Búsqueda de identidad: mediana | -Carga de metadatos: alta pero almacenada en caché <br/> -Ver generación: medio pero almacenado en caché <br/> -Evaluación de parámetros: baja <br/> -Traducción de consultas: mediana pero almacenada en caché <br/> -Generación de materializador: medio pero almacenado en caché <br/> -Ejecución de la consulta de base de datos: potencialmente alta (mejores consultas en algunas situaciones) <br/> + Conexión. abrir <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Materialización de objetos: medio (más rápido que EF5) <br/> -Búsqueda de identidad: mediana |
| `}`                                                                                                  | Conexión. Close          | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                           | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**Segunda ejecución de consultas: consulta en caliente**

| Escritura de código de usuario                                                                                     | Acción                    | Impacto en el rendimiento de EF4                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Impacto en el rendimiento de EF5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Impacto en el rendimiento de EF6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Creación de contexto          | Media                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Media                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Creación de expresiones de consulta | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | Ejecución de consultas LINQ      | -Búsqueda de ~~carga~~ de metadatos: ~~alta pero caché~~ baja <br/> -Vista de la ~~generación~~ de vistas: ~~potencialmente muy alta pero con almacenamiento en caché~~ bajo <br/> -Evaluación de parámetros: media <br/> -Búsqueda de ~~traducción~~ de consultas: mediana <br/> -Búsqueda de la ~~generación~~ del materializador: ~~medio pero en caché~~ bajo <br/> -Ejecución de la consulta de base de datos: potencialmente alta <br/> + Conexión. abrir <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Materialización de objetos: medio <br/> -Búsqueda de identidad: mediana | -Búsqueda de ~~carga~~ de metadatos: ~~alta pero caché~~ baja <br/> -Vista de la ~~generación~~ de vistas: ~~potencialmente muy alta pero con almacenamiento en caché~~ bajo <br/> -Evaluación de parámetros: baja <br/> -Consulta de ~~traducción~~ de consultas: ~~mediana pero en caché~~ baja <br/> -Búsqueda de la ~~generación~~ del materializador: ~~medio pero en caché~~ bajo <br/> -Ejecución de la consulta de base de datos: potencialmente alta (mejores consultas en algunas situaciones) <br/> + Conexión. abrir <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Materialización de objetos: medio <br/> -Búsqueda de identidad: mediana | -Búsqueda de ~~carga~~ de metadatos: ~~alta pero caché~~ baja <br/> -Ver ~~generación~~ de la búsqueda: ~~medio pero en caché~~ bajo <br/> -Evaluación de parámetros: baja <br/> -Consulta de ~~traducción~~ de consultas: ~~mediana pero en caché~~ baja <br/> -Búsqueda de la ~~generación~~ del materializador: ~~medio pero en caché~~ bajo <br/> -Ejecución de la consulta de base de datos: potencialmente alta (mejores consultas en algunas situaciones) <br/> + Conexión. abrir <br/> + Command.ExecuteReader <br/> + DataReader. Read <br/> Materialización de objetos: medio (más rápido que EF5) <br/> -Búsqueda de identidad: mediana |
| `}`                                                                                                  | Conexión. Close          | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


Hay varias maneras de reducir el costo de rendimiento de las consultas en frío y en caliente, y echaremos un vistazo a ellas en la sección siguiente. En concreto, veremos cómo reducir el costo de la carga de modelos en consultas frías mediante el uso de vistas generadas previamente, que ayudan a mitigar los problemas de rendimiento experimentados durante la generación de la vista. En el caso de las consultas cálidas, trataremos el almacenamiento en caché del plan de consulta, ninguna consulta de seguimiento y opciones de ejecución de consulta diferentes.

### <a name="21-what-is-view-generation"></a>2,1 ¿Qué es la generación de vistas?

Para comprender qué es la generación de vistas, primero debemos comprender qué son las "vistas de asignación". Las vistas de asignación son representaciones ejecutables de las transformaciones especificadas en la asignación para cada conjunto de entidades y asociación. Internamente, estas vistas de asignación adoptan la forma de CQTs (árboles de consulta canónicos). Hay dos tipos de vistas de asignación:

-   Vistas de consulta: representan la transformación necesaria para pasar del esquema de la base de datos al modelo conceptual.
-   Vistas de actualización: representan la transformación necesaria para pasar del modelo conceptual al esquema de la base de datos.

Tenga en cuenta que el modelo conceptual podría diferir del esquema de la base de datos de varias maneras. Por ejemplo, se puede usar una sola tabla para almacenar los datos de dos tipos de entidad diferentes. La herencia y las asignaciones no triviales desempeñan un papel en la complejidad de las vistas de asignación.

El proceso de cálculo de estas vistas en función de la especificación de la asignación es lo que llamamos generación de vistas. La generación de vistas puede tener lugar dinámicamente cuando se carga un modelo, o en el momento de la compilación, mediante "vistas generadas previamente". estos últimos se serializan en forma de Entity SQL instrucciones en un archivo de C \# o VB.

Cuando se generan vistas, también se validan. Desde el punto de vista del rendimiento, la gran mayoría del costo de la generación de vistas es realmente la validación de las vistas, lo que garantiza que las conexiones entre las entidades tienen sentido y tienen la cardinalidad correcta para todas las operaciones admitidas.

Cuando se ejecuta una consulta en un conjunto de entidades, la consulta se combina con la vista de consulta correspondiente y el resultado de esta composición se ejecuta a través del compilador del plan para crear la representación de la consulta que la memoria auxiliar puede entender. Por SQL Server, el resultado final de esta compilación será una instrucción SELECT de T-SQL. La primera vez que se realiza una actualización en un conjunto de entidades, la vista de actualización se ejecuta a través de un proceso similar para transformarla en instrucciones DML para la base de datos de destino.

### <a name="22-factors-that-affect-view-generation-performance"></a>2,2 factores que afectan al rendimiento de la generación de vistas

El paso rendimiento de la generación de vistas no solo depende del tamaño del modelo, sino también de cómo se conecta el modelo. Si dos entidades están conectadas a través de una cadena de herencia o una asociación, se dice que están conectadas. Del mismo modo, si dos tablas están conectadas a través de una clave externa, están conectadas. A medida que aumenta el número de entidades conectadas y tablas en los esquemas, aumenta el costo de la generación de la vista.

El algoritmo que usamos para generar y validar vistas es exponencial en el peor de los casos, aunque usamos algunas optimizaciones para mejorar esto. Los factores más importantes que parecen afectar negativamente al rendimiento son:

-   Tamaño del modelo, que hace referencia al número de entidades y la cantidad de asociaciones entre estas entidades.
-   Complejidad del modelo, específicamente la herencia que implica un gran número de tipos.
-   Usar asociaciones independientes, en lugar de asociaciones de clave externa.

En el caso de los modelos pequeños y sencillos, el costo puede ser lo suficientemente pequeño como para no molestarse en usar vistas generadas previamente. A medida que aumentan el tamaño y la complejidad del modelo, hay varias opciones disponibles para reducir el costo de la generación y validación de las vistas.

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2,3 usar vistas generadas previamente para reducir el tiempo de carga del modelo

Para obtener información detallada sobre cómo usar las vistas generadas previamente en Entity Framework 6, visite [vistas de asignación previamente generadas](xref:ef6/fundamentals/performance/pre-generated-views)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>2.3.1 vistas previamente generadas mediante el Entity Framework Power Tools Community Edition

Puede usar la [edición Community Tools de Entity Framework 6](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) para generar vistas de los modelos EDMX y Code First haciendo clic con el botón secundario en el archivo de clase de modelo y usando el menú Entity Framework para seleccionar "generar vistas". Entity Framework Power Tools Community Edition solo funciona en contextos derivados de DbContext.

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 Cómo usar las vistas generadas previamente con un modelo creado por EDMGen

EDMGen es una utilidad que se incluye con .NET y funciona con Entity Framework 4 y 5, pero no con Entity Framework 6. EDMGen permite generar un archivo de modelo, la capa de objetos y las vistas desde la línea de comandos. Una de las salidas será un archivo de vistas en el lenguaje que prefiera, VB o C \# . Se trata de un archivo de código que contiene fragmentos de Entity SQL para cada conjunto de entidades. Para habilitar las vistas generadas previamente, solo tiene que incluir el archivo en el proyecto.

Si realiza modificaciones manualmente en los archivos de esquema para el modelo, tendrá que volver a generar el archivo de vistas. Para ello, ejecute EDMGen con la marca **/Mode: ViewGeneration** .

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 Cómo usar vistas generadas previamente con un archivo EDMX

También puede usar EDMGen para generar vistas para un archivo EDMX: el tema de MSDN al que se hace referencia anteriormente describe cómo agregar un evento anterior a la compilación para hacer esto, pero esto es complicado y hay algunos casos en los que no es posible. Generalmente, es más fácil usar una plantilla T4 para generar las vistas cuando el modelo se encuentra en un archivo edmx.

El blog del equipo de ADO.NET tiene una publicación que describe cómo usar una plantilla T4 para la generación de vistas ( \<https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation> ). Esta publicación incluye una plantilla que se puede descargar y agregar al proyecto. La plantilla se escribió para la primera versión de Entity Framework, por lo que no se garantiza que funcionen con las versiones más recientes de Entity Framework. Sin embargo, puede descargar un conjunto más actualizado de plantillas de generación de vistas para Entity Framework 4 y 5from la galería de Visual Studio:

-   VB.NET: \<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C \# : \<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

Si usa Entity Framework 6, puede obtener las plantillas T4 de la generación de vistas de la galería de Visual Studio en \<http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f> .

### <a name="24-reducing-the-cost-of-view-generation"></a>2,4 reducir el costo de la generación de vistas

El uso de vistas generadas previamente mueve el costo de la generación de vistas de la carga del modelo (tiempo de ejecución) al tiempo de diseño. Aunque esto mejora el rendimiento de inicio en tiempo de ejecución, todavía experimentará el problema de la generación de vistas mientras está desarrollando. Hay varios trucos adicionales que pueden ayudar a reducir el costo de la generación de vistas, tanto en tiempo de compilación como en tiempo de ejecución.

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 usar asociaciones de clave externa para reducir el costo de la generación de vistas

Hemos detectado varios casos en los que el cambio de las asociaciones en el modelo de asociaciones independientes a asociaciones de claves externas mejoró considerablemente el tiempo empleado en la generación de la vista.

Para demostrar esta mejora, se generaron dos versiones del modelo de Navision mediante EDMGen. *Nota: vea el Apéndice C para obtener una descripción del modelo de Navision.* El modelo de Navision es interesante para este ejercicio debido a su gran cantidad de entidades y relaciones entre ellos.

Se generó una versión de este modelo muy grande con las asociaciones de claves externas y la otra se generó con asociaciones independientes. A continuación, se agotó el tiempo que se tardó en generar las vistas para cada modelo. La prueba de Entity Framework 5 usó el método GenerateViews () de la clase EntityViewGenerator para generar las vistas, mientras que la prueba de Entity Framework 6 usaba el método GenerateViews () de la clase StorageMappingItemCollection. Esto se debe a la reestructuración del código que se produjo en el código base de Entity Framework 6.

Con Entity Framework 5, la generación de vistas del modelo con claves externas tardó 65 minutos en un equipo de laboratorio. Se desconoce cuánto tiempo habría tardado en generar las vistas para el modelo que usaba asociaciones independientes. Hemos dejado la prueba en ejecución durante un mes antes de que el equipo se reinicie en nuestro laboratorio para instalar las actualizaciones mensuales.

Con Entity Framework 6, la generación de vistas del modelo con claves externas tardó 28 segundos en el mismo equipo de laboratorio. La generación de la vista para el modelo que usa asociaciones independientes tardó 58 segundos. Las mejoras realizadas en Entity Framework 6 en el código de generación de vistas significan que muchos proyectos no necesitarán vistas generadas previamente para obtener tiempos de inicio más rápidos.

Es importante volver a marcar que las vistas que se generan previamente en Entity Framework 4 y 5 se pueden realizar con EDMGen o con las herramientas avanzadas de Entity Framework. Para Entity Framework la generación de una vista de 6 puede realizarse mediante el Entity Framework herramientas avanzadas o mediante programación, tal y como se describe en [vistas de asignación previamente generadas](xref:ef6/fundamentals/performance/pre-generated-views).

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 Cómo usar claves externas en lugar de asociaciones independientes

Cuando se usa EDMGen o el diseñador de entidades en Visual Studio, se obtiene claves externas de forma predeterminada y solo se usa una marca de la línea de comandos o una casilla para cambiar entre claves externas e IAs.

Si tiene un modelo de Code First grande, el uso de asociaciones independientes tendrá el mismo efecto en la generación de la vista. Puede evitar este impacto si incluye propiedades de clave externa en las clases para los objetos dependientes, aunque algunos desarrolladores considerarán que esto va a contaminar su modelo de objetos. Puede encontrar más información sobre este tema en \<http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/> .

| Al usar      | Haga esto                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Diseñador de entidades | Después de agregar una asociación entre dos entidades, asegúrese de que tiene una restricción referencial. Las restricciones referenciales indican a Entity Framework que utilicen claves externas en lugar de asociaciones independientes. Para obtener más información, visite \<https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework> . |
| EDMGen          | Al usar EDMGen para generar los archivos de la base de datos, las claves externas se respetan y se agregan al modelo como tal. Para obtener más información sobre las distintas opciones expuestas por EDMGen, visite [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx) .                           |
| Code First      | Vea la sección "Convención de relación" del tema [code First convenciones](xref:ef6/modeling/code-first/conventions/built-in) para obtener información sobre cómo incluir propiedades de clave externa en objetos dependientes cuando se usa Code First.                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 mover el modelo a un ensamblado independiente

Cuando el modelo se incluya directamente en el proyecto de la aplicación y se generen vistas a través de un evento anterior a la compilación o de una plantilla T4, se realizará la generación y validación de la vista cada vez que se vuelva a generar el proyecto, incluso si el modelo no se ha cambiado. Si mueve el modelo a un ensamblado independiente y hace referencia a él desde el proyecto de la aplicación, puede realizar otros cambios en la aplicación sin necesidad de volver a compilar el proyecto que contiene el modelo.

*Nota:*   al mover el modelo a ensamblados independientes, recuerde copiar las cadenas de conexión para el modelo en el archivo de configuración de la aplicación del proyecto de cliente.

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 deshabilitar la validación de un modelo basado en edmx

Los modelos EDMX se validan en tiempo de compilación, incluso si el modelo no se ha modificado. Si el modelo ya se ha validado, puede suprimir la validación en tiempo de compilación estableciendo la propiedad "validar al compilar" en false en la ventana Propiedades. Al cambiar la asignación o el modelo, puede volver a habilitar temporalmente la validación para comprobar los cambios.

Tenga en cuenta que se han realizado mejoras en el rendimiento del Entity Framework Designer para Entity Framework 6 y el costo de "validar al compilar" es mucho menor que en versiones anteriores del diseñador.

## <a name="3-caching-in-the-entity-framework"></a>3 almacenamiento en caché en el Entity Framework

Entity Framework tiene los siguientes formatos de almacenamiento en caché integrados:

1.  Almacenamiento en caché de objetos: el ObjectStateManager integrado en una instancia de ObjectContext realiza un seguimiento de la memoria de los objetos que se han recuperado mediante esa instancia. Esto también se conoce como caché de primer nivel.
2.  Almacenamiento en caché del plan de consulta: reutilizar el comando de almacenamiento generado cuando una consulta se ejecuta más de una vez.
3.  Almacenamiento en caché de metadatos: compartir los metadatos de un modelo entre diferentes conexiones con el mismo modelo.

Además de las memorias caché que EF proporciona de forma integrada, también se puede usar un tipo especial de proveedor de datos ADO.NET conocido como proveedor de ajuste para extender Entity Framework con una memoria caché para los resultados recuperados de la base de datos, también denominado almacenamiento en caché de segundo nivel.

### <a name="31-object-caching"></a>ca3,1 almacenamiento en caché de objetos

De forma predeterminada, cuando una entidad se devuelve en los resultados de una consulta, justo antes de que EF lo materializa, el ObjectContext comprobará si ya se ha cargado una entidad con la misma clave en el ObjectStateManager. Si ya existe una entidad con las mismas claves, EF la incluirá en los resultados de la consulta. Aunque EF seguirá emitiendo la consulta en la base de datos, este comportamiento puede omitir la mayor parte del costo de materializar la entidad varias veces.

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 obtener entidades de la memoria caché de objetos mediante la búsqueda de DbContext

A diferencia de una consulta normal, el método Find de DbSet (las API que se incluyen por primera vez en EF 4,1) realizará una búsqueda en la memoria antes de emitir la consulta en la base de datos. Es importante tener en cuenta que dos instancias de ObjectContext diferentes tendrán dos instancias de ObjectStateManager diferentes, lo que significa que tienen cachés de objetos independientes.

Find usa el valor de la clave principal para intentar buscar una entidad de la que el contexto realiza un seguimiento. Si la entidad no está en el contexto, se ejecutará y evaluará una consulta en la base de datos y se devolverá null si no se encuentra la entidad en el contexto o en la base de datos. Tenga en cuenta que la búsqueda también devuelve las entidades que se han agregado al contexto pero que todavía no se han guardado en la base de datos.

Se debe tener en cuenta el rendimiento al usar la búsqueda. De forma predeterminada, las invocaciones a este método desencadenarán una validación de la memoria caché de objetos para detectar los cambios que aún están pendientes de confirmación en la base de datos. Este proceso puede resultar muy caro si hay un gran número de objetos en la memoria caché de objetos o en un gráfico de objetos grande que se va a agregar a la memoria caché de objetos, pero también se puede deshabilitar. En algunos casos, es posible que perciba un orden de magnitud de diferencia en la llamada al método Find cuando se deshabilitan los cambios de detección automática. Todavía se percibe un segundo orden de magnitud cuando el objeto realmente está en la memoria caché en lugar de cuando el objeto tiene que recuperarse de la base de datos. A continuación se muestra un gráfico de ejemplo con medidas tomadas con algunos de nuestros microbenchmarks, expresados en milisegundos, con una carga de 5000 entidades:

![Escala logarítmica de .NET 4,5](~/ef6/media/net45logscale.png ".NET 4,5: escala logarítmica")

Ejemplo de búsqueda con cambios de detección automática deshabilitados:

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

Lo que tiene que tener en cuenta al usar el método Find es:

1.  Si el objeto no está en la memoria caché, se niegan las ventajas de la búsqueda, pero la sintaxis es todavía más sencilla que una consulta por clave.
2.  Si la detección automática de cambios está habilitada, el costo del método buscar puede aumentar en un orden de magnitud, o incluso más en función de la complejidad del modelo y de la cantidad de entidades en la memoria caché de objetos.

Además, tenga en cuenta que la búsqueda solo devuelve la entidad que está buscando y no carga automáticamente sus entidades asociadas si aún no están en la memoria caché de objetos. Si necesita recuperar entidades asociadas, puede usar una consulta por clave con carga diligente. Para obtener más información, vea **carga diferida de 8,1 frente a carga diligente**.

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>3.1.2 problemas de rendimiento cuando la memoria caché de objetos tiene muchas entidades

La memoria caché de objetos ayuda a aumentar la capacidad de respuesta general de Entity Framework. Sin embargo, cuando la memoria caché de objetos tiene una cantidad muy grande de entidades cargadas, puede afectar a ciertas operaciones, como agregar, quitar, buscar, entrada, SaveChanges, etc. En concreto, las operaciones que desencadenan una llamada a DetectChanges se verán afectadas negativamente por las memorias caché de objetos de gran tamaño. DetectChanges sincroniza el gráfico de objetos con el administrador de estado de objetos y su rendimiento viene determinado directamente por el tamaño del gráfico de objetos. Para obtener más información sobre DetectChanges, consulte [seguimiento de cambios en entidades poco](https://msdn.microsoft.com/library/dd456848.aspx).

Al usar Entity Framework 6, los desarrolladores pueden llamar a AddRange y RemoveRange directamente en un DbSet, en lugar de recorrer en iteración una colección y llamar a agregar una vez por instancia. La ventaja de usar los métodos de intervalo es que el costo de DetectChanges solo se paga una vez para todo el conjunto de entidades en lugar de una vez por cada entidad agregada.

### <a name="32-query-plan-caching"></a>ca3,2 almacenamiento en caché del plan de consulta

La primera vez que se ejecuta una consulta, pasa por el compilador del plan interno para traducir la consulta conceptual en el comando de almacenamiento (por ejemplo, el T-SQL que se ejecuta cuando se ejecuta con SQL Server).Si está habilitado el almacenamiento en caché del plan de consulta, la próxima vez que se ejecute la consulta, el comando de almacenamiento se recupera directamente de la memoria caché del plan de consulta para su ejecución, omitiendo el compilador del plan.

La memoria caché del plan de consulta se comparte entre instancias de ObjectContext dentro del mismo AppDomain. No es necesario mantener en una instancia de ObjectContext para beneficiarse del almacenamiento en caché del plan de consulta.

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 algunas notas sobre el almacenamiento en caché del plan de consulta

-   La memoria caché del plan de consulta se comparte para todos los tipos de consulta: Entity SQL, LINQ to Entities y objetos CompiledQuery.
-   De forma predeterminada, el almacenamiento en caché del plan de consulta está habilitado para las consultas Entity SQL, tanto si se ejecutan a través de EntityCommand como a través de ObjectQuery. También está habilitada de forma predeterminada para las consultas LINQ to Entities en Entity Framework en .NET 4,5 y en Entity Framework 6
    -   El almacenamiento en caché del plan de consulta se puede deshabilitar estableciendo la propiedad EnablePlanCaching (en EntityCommand o ObjectQuery) en false. Por ejemplo:
``` csharp
                    var query = from customer in context.Customer
                                where customer.CustomerId == id
                                select new
                                {
                                    customer.CustomerId,
                                    customer.Name
                                };
                    ObjectQuery oQuery = query as ObjectQuery;
                    oQuery.EnablePlanCaching = false;
```
-   En el caso de las consultas con parámetros, el cambio del valor del parámetro seguirá alcanzando la consulta almacenada en caché. Pero cambiar las caras de un parámetro (por ejemplo, tamaño, precisión o escala) alcanzará una entrada diferente en la memoria caché.
-   Cuando se usa Entity SQL, la cadena de consulta forma parte de la clave. Si se cambia la consulta en todos, se producirán diferentes entradas de caché, incluso si las consultas son equivalentes funcionalmente. Esto incluye los cambios en el uso de mayúsculas o minúsculas.
-   Al utilizar LINQ, la consulta se procesa para generar una parte de la clave. Al cambiar la expresión LINQ, se generará una clave diferente.
-   Pueden aplicarse otras limitaciones técnicas; vea consultas compiladas para obtener más información.

#### <a name="322-cache-eviction-algorithm"></a>3.2.2 algoritmo de expulsión de caché

Comprender cómo funciona el algoritmo interno le ayudará a averiguar cuándo habilitar o deshabilitar el almacenamiento en caché del plan de consulta. El algoritmo de limpieza es el siguiente:

1.  Una vez que la memoria caché contiene un número establecido de entradas (800), se inicia un temporizador que, periódicamente (una vez por minuto), barre la memoria caché.
2.  Durante los barridos de caché, las entradas se quitan de la memoria caché en una base de LFRU (con menos frecuencia, usada recientemente). Este algoritmo toma en cuenta tanto el número de llamadas como la antigüedad al decidir qué entradas se expulsan.
3.  Al final de cada barrido de caché, la memoria caché contiene 800 entradas.

Todas las entradas de la memoria caché se tratan igualmente al determinar qué entradas se expulsan. Esto significa que el comando de almacenamiento de una CompiledQuery tiene la misma oportunidad de expulsión que el comando de almacenamiento de una consulta de Entity SQL.

Tenga en cuenta que el temporizador de expulsión de caché se inicia cuando hay 800 entidades en la caché, pero la memoria caché solo se explora 60 segundos después de que se inicie este temporizador. Esto significa que hasta 60 segundos la memoria caché puede crecer de forma bastante grande.

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 métricas de pruebas que muestran el rendimiento del almacenamiento en caché del plan de consulta

Para demostrar el efecto del almacenamiento en caché del plan de consulta en el rendimiento de la aplicación, hemos realizado una prueba en la que se ha ejecutado un número de consultas Entity SQL en el modelo de Navision. Vea el apéndice para obtener una descripción del modelo de Navision y los tipos de consultas que se ejecutaron. En esta prueba, primero se recorre en iteración la lista de consultas y se ejecuta cada una de ellas para agregarlas a la caché (si está habilitado el almacenamiento en caché). Este paso es inesperado. A continuación, se suspende el subproceso principal durante más de 60 segundos para permitir que se lleve a cabo el rastreo de la memoria caché. por último, se recorre en iteración la lista una segunda vez para ejecutar las consultas en caché. Además, la memoria caché del plan de SQL Server se vacía antes de que se ejecute cada conjunto de consultas, de modo que las horas que se obtengan con precisión reflejen el beneficio dado por la memoria caché del plan de consulta.

##### <a name="3231-test-results"></a>Resultados de pruebas 3.2.3.1

| Prueba                                                                   | EF5 sin caché | EF5 en caché | EF6 sin caché | EF6 en caché |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| Enumerar todas las consultas de 18723                                          | 124          | 125,4      | 124,3        | 125,3      |
| Evitar el rastreo (solo las primeras 800, independientemente de la complejidad)  | 41,7         | 5.5        | 40,5         | 5.4        |
| Solo las consultas de AggregatingSubtotals (178 en total, lo que evita el barrido) | 39,5         | 4.5.        | 38,1         | 4.6        |

*Todo el tiempo en segundos.*

Moral: cuando se ejecutan muchas consultas distintas (por ejemplo, consultas creadas dinámicamente), el almacenamiento en caché no ayuda y el vaciado resultante de la memoria caché puede mantener las consultas que más se beneficiarían del almacenamiento en caché del plan.

Las consultas de AggregatingSubtotals son las consultas más complejas que hemos probado con. Como cabría esperar, cuanto más compleja sea la consulta, más ventajas verá del almacenamiento en caché del plan de consulta.

Dado que CompiledQuery es realmente una consulta LINQ con su plan almacenado en memoria caché, la comparación de un CompiledQuery con respecto a la consulta de Entity SQL equivalente debe tener resultados similares. De hecho, si una aplicación tiene una gran cantidad de consultas de Entity SQL dinámicas, llenar la memoria caché con consultas también hará que CompiledQueries se "descompile" cuando se vacíen de la memoria caché. En este escenario, el rendimiento puede mejorar si se deshabilita el almacenamiento en caché en las consultas dinámicas para priorizar el CompiledQueries. Aún mejor, por supuesto, sería volver a escribir la aplicación para usar consultas con parámetros en lugar de consultas dinámicas.

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3,3 uso de CompiledQuery para mejorar el rendimiento con consultas LINQ

Nuestras pruebas indican que el uso de CompiledQuery puede aportar una ventaja del 7% sobre las consultas LINQ compiladas. Esto significa que va a pasar el 7% menos tiempo ejecutando el código de la pila de Entity Framework; no significa que la aplicación vaya a ser del 7% más rápida. Por lo general, el costo de escribir y mantener los objetos CompiledQuery en EF 5,0 podría no merecer el problema en comparación con las ventajas. El kilometraje puede variar, por lo que debe ejecutar esta opción si el proyecto requiere la extracción adicional. Tenga en cuenta que CompiledQueries solo son compatibles con los modelos derivados de ObjectContext y no son compatibles con los modelos derivados de DbContext.

Para obtener más información sobre cómo crear e invocar un CompiledQuery, vea [consultas compiladas (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).

Hay dos consideraciones que debe tomar al usar un CompiledQuery, es decir, el requisito de usar instancias estáticas y los problemas que tienen con composición. A continuación se muestra una explicación detallada de estas dos consideraciones.

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 usar instancias de CompiledQuery estáticas

Dado que la compilación de una consulta LINQ es un proceso que requiere mucho tiempo, no queremos hacerlo cada vez que necesitamos capturar datos de la base de datos. Las instancias de CompiledQuery permiten compilar una vez y ejecutarse varias veces, pero tiene que tener cuidado y adquirir para volver a usar la misma instancia de CompiledQuery cada vez en lugar de compilarla una y otra vez. Se necesita el uso de miembros estáticos para almacenar las instancias de CompiledQuery. en caso contrario, no verá ninguna ventaja.

Por ejemplo, supongamos que la página tiene el cuerpo del método siguiente para controlar la visualización de los productos de la categoría seleccionada:

``` csharp
    // Warning: this is the wrong way of using CompiledQuery
    using (NorthwindEntities context = new NorthwindEntities())
    {
        string selectedCategory = this.categoriesList.SelectedValue;

        var productsForCategory = CompiledQuery.Compile<NorthwindEntities, string, IQueryable<Product>>(
            (NorthwindEntities nwnd, string category) =>
                nwnd.Products.Where(p => p.Category.CategoryName == category)
        );

        this.productsGrid.DataSource = productsForCategory.Invoke(context, selectedCategory).ToList();
        this.productsGrid.DataBind();
    }

    this.productsGrid.Visible = true;
```

En este caso, creará una nueva instancia de CompiledQuery sobre la marcha cada vez que se llame al método. En lugar de ver las ventajas de rendimiento mediante la recuperación del comando de almacenamiento desde la memoria caché del plan de consulta, el CompiledQuery pasará por el compilador del plan cada vez que se cree una nueva instancia. De hecho, estará contaminando la memoria caché del plan de consulta con una nueva entrada CompiledQuery cada vez que se llame al método.

En su lugar, se desea crear una instancia estática de la consulta compilada, por lo que se invoca la misma consulta compilada cada vez que se llama al método. Una manera de hacerlo es agregar la instancia de CompiledQuery como un miembro del contexto del objeto.A continuación, puede hacer cosas un poco limpiador accediendo a CompiledQuery a través de un método auxiliar:

``` csharp
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IEnumerable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
            );

        public IEnumerable<Product> GetProductsForCategory(string categoryName)
        {
            return productsForCategoryCQ.Invoke(this, categoryName).ToList();
        }
```

Este método auxiliar se invocaría de la siguiente manera:

``` csharp
    this.productsGrid.DataSource = context.GetProductsForCategory(selectedCategory);
```

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 redacción sobre un CompiledQuery

La capacidad de componer sobre cualquier consulta LINQ es muy útil; para ello, basta con invocar un método después de IQueryable, como *SKIP ()* o *Count ()*. Sin embargo, si lo hace, se devuelve un nuevo objeto IQueryable. Aunque no hay nada que se detenga técnicamente de la composición en un CompiledQuery, si lo hace, se producirá la generación de un nuevo objeto IQueryable que requiera pasar de nuevo el compilador del plan.

Algunos componentes harán uso de objetos IQueryable compuestos para habilitar la funcionalidad avanzada. Por ejemplo, ASP. GridView de la red se puede enlazar a datos a un objeto IQueryable a través de la propiedad SelectMethod. A continuación, GridView se compondrá sobre este objeto IQueryable para permitir la ordenación y la paginación en el modelo de datos. Como puede ver, el uso de un CompiledQuery para GridView no alcanzaría la consulta compilada pero generaría una nueva consulta compilada.

Un lugar en el que pueda encontrarse es al agregar filtros progresivos a una consulta. Por ejemplo, supongamos que tiene una página clientes con varias listas desplegables para filtros opcionales (por ejemplo, Country y OrdersCount). Puede crear estos filtros a través de los resultados de IQueryable de un CompiledQuery, pero si lo hace, la nueva consulta pasará a través del compilador del plan cada vez que la ejecute.

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployee();

        if (this.orderCountFilterList.SelectedItem.Value != defaultFilterText)
        {
            int orderCount = int.Parse(orderCountFilterList.SelectedValue);
            myCustomers = myCustomers.Where(c => c.Orders.Count > orderCount);
        }

        if (this.countryFilterList.SelectedItem.Value != defaultFilterText)
        {
            myCustomers = myCustomers.Where(c => c.Address.Country == countryFilterList.SelectedValue);
        }

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 Para evitar esta recompilación, puede volver a escribir CompiledQuery para tener en cuenta los posibles filtros:

``` csharp
    private static readonly Func<NorthwindEntities, int, int?, string, IQueryable<Customer>> customersForEmployeeWithFiltersCQ = CompiledQuery.Compile(
        (NorthwindEntities context, int empId, int? countFilter, string countryFilter) =>
            context.Customers.Where(c => c.Orders.Any(o => o.EmployeeID == empId))
            .Where(c => countFilter.HasValue == false || c.Orders.Count > countFilter)
            .Where(c => countryFilter == null || c.Address.Country == countryFilter)
        );
```

Que se invocaría en la interfaz de usuario como:

``` csharp
    using (NorthwindEntities context = new NorthwindEntities())
    {
        int? countFilter = (this.orderCountFilterList.SelectedIndex == 0) ?
            (int?)null :
            int.Parse(this.orderCountFilterList.SelectedValue);

        string countryFilter = (this.countryFilterList.SelectedIndex == 0) ?
            null :
            this.countryFilterList.SelectedValue;

        IQueryable<Customer> myCustomers = context.InvokeCustomersForEmployeeWithFilters(
                countFilter, countryFilter);

        this.customersGrid.DataSource = myCustomers;
        this.customersGrid.DataBind();
    }
```

 Un inconveniente aquí es que el comando de almacenamiento generado siempre tendrá los filtros con las comprobaciones de valores NULL, pero deben ser bastante simples para que el servidor de base de datos pueda optimizar:

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3,4 almacenamiento en caché de metadatos

La Entity Framework también admite el almacenamiento en caché de metadatos. Esencialmente, se almacena en caché la información de tipo y la información de asignación de tipo a base de datos en distintas conexiones al mismo modelo. La caché de metadatos es única por AppDomain.

#### <a name="341-metadata-caching-algorithm"></a>algoritmo de almacenamiento en caché de metadatos de 3.4.1

1.  La información de metadatos de un modelo se almacena en ItemCollection para cada EntityConnection.
    -   Como nota lateral, hay diferentes objetos ItemCollection para diferentes partes del modelo. Por ejemplo, StoreItemCollections contiene la información acerca del modelo de base de datos; ObjectItemCollection contiene información sobre el modelo de datos. EdmItemCollection contiene información sobre el modelo conceptual.

2.  Si dos conexiones utilizan la misma cadena de conexión, compartirán la misma instancia de ItemCollection.
3.  Funcionalmente equivalente, pero las cadenas de conexión textualmente diferentes pueden dar lugar a diferentes cachés de metadatos. Vamos a acortar las cadenas de conexión, por lo que cambiar el orden de los tokens debe generar metadatos compartidos. Pero dos cadenas de conexión que parecen funcionalmente iguales no se pueden evaluar como idénticas después de la tokenización.
4.  La ItemCollection se comprueba periódicamente para su uso. Si se determina que no se ha tenido acceso recientemente a un área de trabajo, se marcará para su limpieza en el siguiente barrido de caché.
5.  Simplemente la creación de EntityConnection hará que se cree una memoria caché de metadatos (aunque las colecciones de elementos que contengan no se inicializarán hasta que se abra la conexión). Esta área de trabajo permanecerá en memoria hasta que el algoritmo de almacenamiento en caché determine que no está en uso.

El equipo de asesoramiento al cliente ha escrito una entrada de blog que describe la conservación de una referencia a un ItemCollection para evitar la "degradación" cuando se usan modelos grandes: \<https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services> .

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 la relación entre almacenamiento en caché de metadatos y almacenamiento en caché del plan de consulta

La instancia de la memoria caché del plan de consulta reside en el ItemCollection de los tipos de almacén de MetadataWorkspace. Esto significa que los comandos de almacenamiento almacenados en caché se usarán para las consultas en cualquier contexto del que se haya creado una instancia con un MetadataWorkspace determinado. También significa que si tiene dos cadenas de conexión que son ligeramente diferentes y no coinciden después de la tokenización, tendrá instancias de la memoria caché del plan de consulta diferente.

### <a name="35-results-caching"></a>Caching de resultados de 3,5

Con el almacenamiento en caché de resultados (también conocido como "almacenamiento en caché de segundo nivel"), se conservan los resultados de las consultas en una caché local. Al emitir una consulta, primero verá si los resultados están disponibles localmente antes de realizar la consulta en el almacén. Aunque el almacenamiento en caché de resultados no es compatible directamente con Entity Framework, es posible agregar una memoria caché de segundo nivel mediante un proveedor de ajuste. Un proveedor de ajuste de ejemplo con una memoria caché de segundo nivel es la Entity Framework de la [memoria caché de segundo nivel basada en NCache](https://www.alachisoft.com/ncache/entity-framework.html).

Esta implementación del almacenamiento en caché de segundo nivel es una funcionalidad insertada que tiene lugar después de evaluar la expresión LINQ (y funcletized) y de que el plan de ejecución de consulta se calcula o recupera de la caché de primer nivel. La memoria caché de segundo nivel solo almacenará los resultados de la base de datos sin formato, por lo que la canalización de materialización se seguirá ejecutando después.

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 referencias adicionales para el almacenamiento en caché de resultados con el proveedor de ajuste

-   Julia Lerman ha escrito un artículo de MSDN "almacenamiento en caché de segundo nivel en Entity Framework y Windows Azure" que incluye cómo actualizar el proveedor de empaquetado de ejemplo para usar el almacenamiento en caché de Windows Server AppFabric: [https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   Si está trabajando con Entity Framework 5, el blog del equipo tiene una publicación en la que se describe cómo hacer todo lo que se ejecuta con el proveedor de almacenamiento en caché para Entity Framework 5: \<https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider> . También incluye una plantilla T4 para ayudar a automatizar la adición del almacenamiento en caché de segundo nivel al proyecto.

## <a name="4-autocompiled-queries"></a>4 consultas compiladas

Cuando se emite una consulta en una base de datos mediante Entity Framework, debe pasar por una serie de pasos antes de materializar los resultados. uno de estos pasos es la compilación de consultas. Entity SQL se sabe que las consultas tienen un buen rendimiento, ya que se almacenan en caché automáticamente, por lo que la segunda o tercera vez que se ejecuta la misma consulta puede omitir el compilador del plan y usar el plan almacenado en caché en su lugar.

Entity Framework 5 presentó también el almacenamiento en caché automático para las consultas de LINQ to Entities. En las ediciones anteriores de Entity Framework crear un CompiledQuery para acelerar el rendimiento era una práctica común, ya que esto haría que su LINQ to Entities consulta se pudiera almacenar en caché. Dado que el almacenamiento en caché se realiza ahora automáticamente sin el uso de un CompiledQuery, se llama a esta característica "autocompiled queries". Para obtener más información acerca de la memoria caché del plan de consulta y su mecánica, vea almacenamiento en caché del plan de consulta.

Entity Framework detecta cuándo es necesario volver a compilar una consulta y lo hace cuando se invoca la consulta incluso si se había compilado antes. Las condiciones comunes que hacen que la consulta se vuelva a compilar son:

-   Cambio de MergeOption asociado a la consulta. No se usará la consulta almacenada en caché, sino que el compilador del plan se ejecutará de nuevo y el plan recién creado se almacenará en caché.
-   Cambiar el valor de ContextOptions. UseCSharpNullComparisonBehavior. Tiene el mismo efecto que cambiar MergeOption.

Otras condiciones pueden impedir que la consulta use la memoria caché. Los ejemplos comunes son:

-   Usar IEnumerable &lt; T &gt; . Contains &lt; &gt; (valor T).
-   Usar funciones que generan consultas con constantes.
-   Usar las propiedades de un objeto no asignado.
-   Vincular la consulta a otra consulta que requiere que se vuelva a compilar.

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4,1 mediante IEnumerable &lt; T &gt; . Contiene &lt; t &gt; (valor t)

Entity Framework no almacena en caché las consultas que invocan a IEnumerable &lt; T &gt; . Contiene &lt; t &gt; (valor t) en una colección en memoria, ya que los valores de la colección se consideran volátiles. La consulta de ejemplo siguiente no se almacenará en caché, por lo que el compilador del plan siempre la procesará:

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var query = context.MyEntities
                    .Where(entity => ids.Contains(entity.Id));

    var results = query.ToList();
    ...
}
```

Tenga en cuenta que el tamaño de IEnumerable en el que se ejecuta se determina la rapidez o la velocidad de compilación de la consulta. El rendimiento puede verse afectado de forma significativa al usar colecciones grandes como la que se muestra en el ejemplo anterior.

Entity Framework 6 contiene optimizaciones para la manera IEnumerable &lt; T &gt; . Contiene &lt; t &gt; (valor t) funciona cuando se ejecutan las consultas. El código SQL que se genera es mucho más rápido para generar y más legible y, en la mayoría de los casos, también se ejecuta más rápido en el servidor.

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4,2 usar funciones que generan consultas con constantes

Los operadores LINQ SKIP (), Take (), Contains () y DefautIfEmpty () no generan consultas SQL con parámetros sino que, en su lugar, colocan los valores que se les pasan como constantes. Por este motivo, las consultas que, de otro modo, podrían ser idénticas en la memoria caché del plan de consulta, tanto en la pila de EF como en el servidor de base de datos, y no se reutilizarán a menos que se usen las mismas constantes en una ejecución de consulta posterior. Por ejemplo:

``` csharp
var id = 10;
...
using (var context = new MyContext())
{
    var query = context.MyEntities.Select(entity => entity.Id).Contains(id);

    var results = query.ToList();
    ...
}
```

En este ejemplo, cada vez que se ejecuta esta consulta con un valor diferente para el identificador, la consulta se compilará en un nuevo plan.

En concreto, preste atención al uso de SKIP y Take al realizar la paginación. En EF6, estos métodos tienen una sobrecarga lambda que hace que el plan de consulta almacenado en caché se vuelva a usar porque EF puede capturar las variables que se pasan a estos métodos y traducirlas a SQLparameters. Esto también ayuda a mantener el limpiador de la memoria caché, ya que, de lo contrario, cada consulta con una constante diferente para Skip y Take obtendría su propia entrada de caché del plan de consulta.

Considere el siguiente código, que es poco óptimo, pero solo está pensado para ejemplificar esta clase de consultas:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Una versión más rápida de este mismo código implicaría llamar a SKIP con una expresión lambda:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

El segundo fragmento de código puede ejecutarse hasta un 11% más rápido, ya que se usa el mismo plan de consulta cada vez que se ejecuta la consulta, lo que ahorra tiempo de CPU y evita contaminar la caché de consultas. Además, dado que el parámetro que se va a omitir está en un cierre, el código también tendrá el siguiente aspecto:

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4,3 uso de las propiedades de un objeto no asignado

Cuando una consulta usa las propiedades de un tipo de objeto no asignado como parámetro, la consulta no se almacenará en caché. Por ejemplo:

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();

    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myObject.MyProperty)
                select entity;

   var results = query.ToList();
    ...
}
```

En este ejemplo, supongamos que la clase NonMappedType no forma parte del modelo de entidad. Esta consulta se puede cambiar fácilmente para que no use un tipo no asignado y, en su lugar, use una variable local como parámetro para la consulta:

``` csharp
using (var context = new MyContext())
{
    var myObject = new NonMappedType();
    var myValue = myObject.MyProperty;
    var query = from entity in context.MyEntities
                where entity.Name.StartsWith(myValue)
                select entity;

    var results = query.ToList();
    ...
}
```

En este caso, la consulta se podrá almacenar en caché y se beneficiará de la caché del plan de consulta.

### <a name="44-linking-to-queries-that-require-recompiling"></a>4,4 vincular a consultas que requieren volver a compilar

Siguiendo el mismo ejemplo anterior, si tiene una segunda consulta que se basa en una consulta que se debe volver a compilar, también se volverá a compilar toda la segunda consulta. Este es un ejemplo para ilustrar este escenario:

``` csharp
int[] ids = new int[10000];
...
using (var context = new MyContext())
{
    var firstQuery = from entity in context.MyEntities
                        where ids.Contains(entity.Id)
                        select entity;

    var secondQuery = from entity in context.MyEntities
                        where firstQuery.Any(otherEntity => otherEntity.Id == entity.Id)
                        select entity;

    var results = secondQuery.ToList();
    ...
}
```

El ejemplo es genérico, pero muestra cómo la vinculación a firstQuery hace que secondQuery no pueda almacenarse en caché. Si firstQuery no hubiera sido una consulta que requiera volver a compilar, entonces secondQuery se habría almacenado en caché.

## <a name="5-notracking-queries"></a>5 realizar un seguimiento de las consultas

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5,1 deshabilitar el seguimiento de cambios para reducir la sobrecarga de administración de estado

Si está en un escenario de solo lectura y desea evitar la sobrecarga que supone cargar los objetos en el ObjectStateManager, puede emitir consultas "sin seguimiento".El seguimiento de cambios se puede deshabilitar en el nivel de consulta.

Tenga en cuenta que, si deshabilita el seguimiento de cambios, se desactivará la caché de objetos. Al consultar una entidad, no se puede omitir la materialización mediante la extracción de los resultados de la consulta materializados previamente del ObjectStateManager. Si consulta repetidamente las mismas entidades en el mismo contexto, es posible que realmente vea una ventaja de rendimiento al habilitar el seguimiento de cambios.

Cuando se realiza una consulta con ObjectContext, las instancias de ObjectQuery y ObjectSet recordarán una MergeOption una vez que se haya establecido y las consultas que se componen en ellas heredarán la MergeOption en vigor de la consulta primaria. Al usar DbContext, el seguimiento se puede deshabilitar llamando al modificador AsNoTracking () en DbSet.

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 deshabilitar el seguimiento de cambios para una consulta al usar DbContext

Puede cambiar el modo de una consulta a NoTracking mediante el encadenamiento de una llamada al método AsNoTracking () en la consulta. A diferencia de ObjectQuery, las clases DbSet y DbQuery de la API DbContext no tienen una propiedad mutable para MergeOption.

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a>5.1.2 deshabilitar el seguimiento de cambios en el nivel de consulta mediante ObjectContext

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 deshabilitar el seguimiento de cambios para un conjunto de entidades completo mediante ObjectContext

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5,2 métricas de prueba que muestran las ventajas de rendimiento de las consultas NoTracking

En esta prueba, veremos el costo de rellenar el ObjectStateManager comparando el seguimiento con NoTracking queries for the Navision Model. Vea el apéndice para obtener una descripción del modelo de Navision y los tipos de consultas que se ejecutaron. En esta prueba, se recorre en iteración la lista de consultas y se ejecuta cada una de ellas. Se han ejecutado dos variaciones de la prueba, una vez con NoTracking Queries y una con la opción de fusión mediante combinación predeterminada de "AppendOnly". Se ejecutó cada variación 3 veces y se toma el valor medio de las ejecuciones. Entre las pruebas se borra la memoria caché de consultas en el SQL Server y se reduce la tempdb mediante la ejecución de los siguientes comandos:

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINKDATABASE (tempdb, 0)

Resultados de pruebas, la mediana en 3 ejecuciones:

|                        | SIN SEGUIMIENTO: ESPACIO DE TRABAJO | SIN SEGUIMIENTO: TIEMPO | SOLO ANEXAR: ESPACIO DE TRABAJO | SOLO ANEXAR: HORA |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **Entity Framework 5** | 460361728                 | 1163536 MS         | 596545536                 | 1273042 MS         |
| **Entity Framework 6** | 647127040                 | 190228 MS          | 832798720                 | 195521 MS          |

Entity Framework 5 tendrá una superficie de memoria menor al final de la ejecución que Entity Framework 6. La memoria adicional consumida por Entity Framework 6 es el resultado de estructuras de memoria y código adicionales que permiten nuevas características y un mejor rendimiento.

También hay una diferencia clara en la superficie de memoria cuando se usa ObjectStateManager. Entity Framework 5 aumentó su superficie en un 30% al realizar un seguimiento de todas las entidades que materializamos en la base de datos. Entity Framework 6 aumentó su superficie en un 28% al hacerlo.

En términos de tiempo, Entity Framework 6 supera Entity Framework 5 en esta prueba por un margen grande. Entity Framework 6 completó la prueba en aproximadamente el 16% del tiempo consumido por Entity Framework 5. Además, Entity Framework 5 tarda un 9% más en completarse cuando se usa ObjectStateManager. En comparación, Entity Framework 6 utiliza el 3% más de tiempo al usar el ObjectStateManager.

## <a name="6-query-execution-options"></a>6 opciones de ejecución de consultas

Entity Framework ofrece varias maneras de consultar. Echaremos un vistazo a las siguientes opciones, compararemos las ventajas y desventajas de cada una de ellas y examinaremos sus características de rendimiento:

-   LINQ to Entities.
-   Ningún LINQ to Entities de seguimiento.
-   Entity SQL en una ObjectQuery.
-   Entity SQL sobre un EntityCommand.
-   ExecuteStoreQuery.
-   SqlQuery.
-   CompiledQuery.

### <a name="61-linq-to-entities-queries"></a>6,1 consultas LINQ to Entities

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**Ventajas**

-   Adecuado para las operaciones CUD.
-   Objetos completamente materializados.
-   Más sencillo de escribir con la sintaxis integrada en el lenguaje de programación.
-   Buen rendimiento.

**Desventajas**

-   Ciertas restricciones técnicas, como:
    -   Los patrones que usan DefaultIfEmpty para consultas de combinación externas dan como resultado consultas más complejas que las instrucciones de combinación externa simples en Entity SQL.
    -   Todavía no se puede usar como con la coincidencia de patrones general.

### <a name="62-no-tracking-linq-to-entities-queries"></a>6,2 ninguna consulta de LINQ to Entities de seguimiento

Cuando el contexto deriva a ObjectContext:

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

Cuando el contexto derive DbContext:

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**Ventajas**

-   Rendimiento mejorado con respecto a las consultas LINQ normales.
-   Objetos completamente materializados.
-   Más sencillo de escribir con la sintaxis integrada en el lenguaje de programación.

**Desventajas**

-   No es adecuado para las operaciones de CUD.
-   Ciertas restricciones técnicas, como:
    -   Los patrones que usan DefaultIfEmpty para consultas de combinación externas dan como resultado consultas más complejas que las instrucciones de combinación externa simples en Entity SQL.
    -   Todavía no se puede usar como con la coincidencia de patrones general.

Tenga en cuenta que no se realiza el seguimiento de las consultas de las propiedades escalares del proyecto, aunque no se especifique el NoTracking. Por ejemplo:

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

Esta consulta en particular no especifica explícitamente que sea NoTracking, pero como no materializa un tipo conocido por el administrador de estado de objeto, no se realiza el seguimiento del resultado materializado.

### <a name="63-entity-sql-over-an-objectquery"></a>6,3 Entity SQL en una ObjectQuery

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**Ventajas**

-   Adecuado para las operaciones CUD.
-   Objetos completamente materializados.
-   Admite el almacenamiento en caché del plan de consulta.

**Desventajas**

-   Incluye cadenas de consulta textual que son más propensas a errores del usuario que las construcciones de consulta integradas en el lenguaje.

### <a name="64-entity-sql-over-an-entity-command"></a>6,4 Entity SQL sobre un comando de entidad

``` csharp
EntityCommand cmd = eConn.CreateCommand();
cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
{
    while (reader.Read())
    {
        // manually 'materialize' the product
    }
}
```

**Ventajas**

-   Admite el almacenamiento en caché del plan de consulta en .NET 4,0 (el almacenamiento en caché del plan es compatible con todos los demás tipos de consulta en .NET 4,5).

**Desventajas**

-   Incluye cadenas de consulta textual que son más propensas a errores del usuario que las construcciones de consulta integradas en el lenguaje.
-   No es adecuado para las operaciones de CUD.
-   Los resultados no se materializan automáticamente y deben leerse desde el lector de datos.

### <a name="65-sqlquery-and-executestorequery"></a>6,5 SqlQuery y ExecuteStoreQuery

SqlQuery en la base de datos:

``` csharp
// use this to obtain entities and not track them
var q1 = context.Database.SqlQuery<Product>("select * from products");
```

SqlQuery en DbSet:

``` csharp
// use this to obtain entities and have them tracked
var q2 = context.Products.SqlQuery("select * from products");
```

ExecyteStoreQuery:

``` csharp
var beverages = context.ExecuteStoreQuery<Product>(
@"     SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued, P.DiscontinuedDate
       FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
       WHERE        (C.CategoryName = 'Beverages')"
);
```

**Ventajas**

-   Rendimiento generalmente más rápido, ya que se omite el compilador del plan.
-   Objetos completamente materializados.
-   Adecuado para las operaciones de CUD cuando se usa desde DbSet.

**Desventajas**

-   La consulta es de texto y propenso a errores.
-   La consulta está asociada a un back-end específico mediante la semántica del almacén en lugar de la semántica conceptual.
-   Cuando la herencia está presente, la consulta handcrafted debe tener en cuenta las condiciones de asignación para el tipo solicitado.

### <a name="66-compiledquery"></a>6,6 CompiledQuery

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**Ventajas**

-   Proporciona una mejora del rendimiento del 7% sobre las consultas LINQ normales.
-   Objetos completamente materializados.
-   Adecuado para las operaciones CUD.

**Desventajas**

-   Mayor complejidad y sobrecarga de programación.
-   La mejora del rendimiento se pierde cuando se compone de una consulta compilada.
-   Algunas consultas LINQ no se pueden escribir como CompiledQuery, por ejemplo, proyecciones de tipos anónimos.

### <a name="67-performance-comparison-of-different-query-options"></a>6,7 comparación de rendimiento de diferentes opciones de consulta

Los microbenchmarks simples en los que no se ha agotado el tiempo de creación del contexto se han puesto en la prueba. Se ha medido la consulta de 5000 veces para un conjunto de entidades sin almacenamiento en caché en un entorno controlado. Estos números se van a tomar con una advertencia: no reflejan los números reales generados por una aplicación, sino que son una medición muy precisa de la cantidad de una diferencia de rendimiento que se produce cuando se comparan diferentes opciones de consulta en las distintas tareas, excepto el costo de crear un nuevo contexto.

| EF  | Prueba                                 | Tiempo (MS) | Memoria   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | ObjectContext (ESQL)                   | 2414      | 38801408 |
| EF5 | Consulta de ObjectContext para LINQ             | 2692      | 38277120 |
| EF5 | No seguimiento de consulta LINQ de DbContext     | 2818      | 41840640 |
| EF5 | Consulta LINQ de DbContext                 | 2930      | 41771008 |
| EF5 | No seguimiento de consultas LINQ de ObjectContext | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | ObjectContext (ESQL)                   | 2059      | 46039040 |
| EF6 | Consulta de ObjectContext para LINQ             | 3074      | 45248512 |
| EF6 | No seguimiento de consulta LINQ de DbContext     | 3125      | 47575040 |
| EF6 | Consulta LINQ de DbContext                 | 3420      | 47652864 |
| EF6 | No seguimiento de consultas LINQ de ObjectContext | 3593      | 45260800 |

![EF5 micro benchmarks, 5000 iteraciones calientes](~/ef6/media/ef5micro5000warm.png)

![EF6 micro benchmarks, 5000 iteraciones calientes](~/ef6/media/ef6micro5000warm.png)

Los microbenchmarks son muy sensibles a los pequeños cambios en el código. En este caso, la diferencia entre los costos de Entity Framework 5 y Entity Framework 6 se debe a la adición de mejoras de [interceptación](xref:ef6/fundamentals/logging-and-interception) y [transaccional](xref:ef6/saving/transactions). Sin embargo, estos números de microbenchmarks son una visión amplificada en un fragmento muy pequeño de lo que hace Entity Framework. Los escenarios reales de consultas cálidas no deben ver una regresión del rendimiento al actualizar desde Entity Framework 5 a Entity Framework 6.

Para comparar el rendimiento real de las distintas opciones de consulta, hemos creado 5 variaciones de pruebas independientes en las que usamos una opción de consulta diferente para seleccionar todos los productos cuyo nombre de categoría sea "bebidas". Cada iteración incluye el costo de crear el contexto y el costo de materializar todas las entidades devueltas. 10 iteraciones se ejecutan de vez en cuando, antes de tomar la suma de 1000 iteraciones con tiempo. Los resultados que se muestran son el promedio de ejecución de 5 ejecuciones de cada prueba. Para obtener más información, vea el Apéndice B, que incluye el código de la prueba.

| EF  | Prueba                                        | Tiempo (MS) | Memoria   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | ObjectContext (comando de entidad)                | 621       | 39350272 |
| EF5 | Consulta SQL DbContext en la base de datos             | 825       | 37519360 |
| EF5 | Consulta del almacén de ObjectContext                   | 878       | 39460864 |
| EF5 | No seguimiento de consultas LINQ de ObjectContext        | 969       | 38293504 |
| EF5 | ObjectContext Entity SQL mediante consulta de objeto | 1089      | 38981632 |
| EF5 | Consulta compilada de ObjectContext                | 1099      | 38682624 |
| EF5 | Consulta de ObjectContext para LINQ                    | 1152      | 38178816 |
| EF5 | No seguimiento de consulta LINQ de DbContext            | 1208      | 41803776 |
| EF5 | Consulta SQL DbContext en DbSet                | 1414      | 37982208 |
| EF5 | Consulta LINQ de DbContext                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | ObjectContext (comando de entidad)                | 480       | 47247360 |
| EF6 | Consulta del almacén de ObjectContext                   | 493       | 46739456 |
| EF6 | Consulta SQL DbContext en la base de datos             | 614       | 41607168 |
| EF6 | No seguimiento de consultas LINQ de ObjectContext        | 684       | 46333952 |
| EF6 | ObjectContext Entity SQL mediante consulta de objeto | 767       | 48865280 |
| EF6 | Consulta compilada de ObjectContext                | 788       | 48467968 |
| EF6 | No seguimiento de consulta LINQ de DbContext            | 878       | 47554560 |
| EF6 | Consulta de ObjectContext para LINQ                    | 953       | 47632384 |
| EF6 | Consulta SQL DbContext en DbSet                | 1023      | 41992192 |
| EF6 | Consulta LINQ de DbContext                        | 1290      | 47529984 |


![EF5 la consulta activa 1000 iteraciones](~/ef6/media/ef5warmquery1000.png)

![EF6 la consulta activa 1000 iteraciones](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> Por integridad, se incluye una variación en la que se ejecuta una consulta de Entity SQL en un EntityCommand. Sin embargo, dado que los resultados no se materializan para estas consultas, la comparación no es necesariamente de manzanas a manzanas. La prueba incluye una aproximación aproximada a la materialización para intentar hacer la comparación más justa.

En este caso de un extremo a otro, Entity Framework 6 supera Entity Framework 5 debido a las mejoras de rendimiento realizadas en varias partes de la pila, lo que incluye una inicialización de DbContext mucho más clara y búsquedas de MetadataCollection T más rápidas &lt; &gt; .

## <a name="7-design-time-performance-considerations"></a>7 consideraciones de rendimiento en tiempo de diseño

### <a name="71-inheritance-strategies"></a>7,1 estrategias de herencia

Otra consideración de rendimiento al usar Entity Framework es la estrategia de herencia que se usa. Entity Framework admite 3 tipos básicos de herencia y sus combinaciones:

-   Tabla por jerarquía (TPH): donde cada conjunto de herencia se asigna a una tabla con una columna discriminadora para indicar qué tipo determinado de la jerarquía se representa en la fila.
-   Tabla por tipo (TPT): cada tipo tiene su propia tabla en la base de datos; las tablas secundarias solo definen las columnas que no contiene la tabla primaria.
-   Tabla por clase (TPC): cada tipo tiene su propia tabla completa en la base de datos; las tablas secundarias definen todos sus campos, incluidos los definidos en tipos primarios.

Si el modelo utiliza la herencia de TPT, las consultas que se generan serán más complejas que las que se generan con las otras estrategias de herencia, lo que puede dar lugar a tiempos de ejecución más largos en el almacén.Por lo general, se tarda más tiempo en generar consultas en un modelo TPT y materializar los objetos resultantes.

Vea la herencia "consideraciones de rendimiento al usar TPT (tabla por tipo)" en la Entity Framework "entrada del blog de MSDN: \<https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework> .

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 evitar la aplicación de la Model First o Code First aplicaciones

Al crear un modelo sobre una base de datos existente que tiene un esquema TPT, no tiene muchas opciones. Pero al crear una aplicación mediante Model First o Code First, debe evitar la herencia de TPT por cuestiones de rendimiento.

Al usar Model First en el Asistente de Entity Designer, obtendrá el TPT para cualquier herencia del modelo. Si desea cambiar a una estrategia de herencia de TPH con Model First, puede usar "Entity Designer Database Generation Power Pack" disponible en la galería de Visual Studio ( \<http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/> ).

Cuando se usa Code First para configurar la asignación de un modelo con herencia, EF usará TPH de forma predeterminada, por lo que todas las entidades de la jerarquía de herencia se asignarán a la misma tabla. Consulte la sección "asignación con la API fluida" del artículo "Code First en Entity Framework 4.1" en MSDN Magazine ( [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx) ) para obtener más detalles.

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7,2 actualización de EF4 para mejorar el tiempo de generación de modelos

En Entity Framework 5 y 6 se encuentra disponible una mejora específica del SQL Server para el algoritmo que genera el nivel de almacenamiento (SSDL), y como una actualización de Entity Framework 4 cuando está instalado Visual Studio 2010 SP1. Los resultados de pruebas siguientes muestran la mejora al generar un modelo muy grande, en este caso el modelo de Navision. Consulte el Apéndice C para obtener más detalles sobre él.

El modelo contiene 1005 conjuntos de entidades y conjuntos de asociaciones de 4227.

| Configuración                              | Desglose del tiempo consumido                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visual Studio 2010, Entity Framework 4     | Generación de SSDL: 2 h 27 min <br/> Generación de asignaciones: 1 segundo <br/> Generación de CSDL: 1 segundo <br/> Generación de ObjectLayer: 1 segundo <br/> Generación de vista: 2 h 14 min |
| Visual Studio 2010 SP1, Entity Framework 4 | Generación de SSDL: 1 segundo <br/> Generación de asignaciones: 1 segundo <br/> Generación de CSDL: 1 segundo <br/> Generación de ObjectLayer: 1 segundo <br/> Generación de vista: 1 h 53 min   |
| Visual Studio 2013, Entity Framework 5     | Generación de SSDL: 1 segundo <br/> Generación de asignaciones: 1 segundo <br/> Generación de CSDL: 1 segundo <br/> Generación de ObjectLayer: 1 segundo <br/> Generación de vistas: 65 minutos    |
| Visual Studio 2013, Entity Framework 6     | Generación de SSDL: 1 segundo <br/> Generación de asignaciones: 1 segundo <br/> Generación de CSDL: 1 segundo <br/> Generación de ObjectLayer: 1 segundo <br/> Generación de vista: 28 segundos.   |


Merece la pena tener en cuenta que cuando se genera el SSDL, la carga se emplea casi por completo en el SQL Server, mientras que el equipo de desarrollo de cliente está esperando la inactividad de los resultados para volver del servidor. Los DBA deben apreciar especialmente esta mejora. También merece la pena tener en cuenta que básicamente el costo completo de la generación del modelo tiene lugar en la generación de la vista ahora.

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7,3 dividir modelos grandes con Database First y Model First

A medida que aumenta el tamaño del modelo, la superficie del diseñador se vuelve abarrotada y difícil de usar. Normalmente consideramos que un modelo con más de 300 entidades es demasiado grande para usar eficazmente el diseñador. En la siguiente entrada de blog se describen varias opciones para dividir modelos grandes: \<https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2> .

La publicación se escribió para la primera versión de Entity Framework, pero los pasos se siguen aplicando.

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7,4 consideraciones de rendimiento con el control de origen de datos de entidad

Hemos visto casos en pruebas de rendimiento y esfuerzo multiproceso en las que el rendimiento de una aplicación web que usa el control EntityDataSource se deteriora significativamente. La causa subyacente es que EntityDataSource llama repetidamente a MetadataWorkspace. LoadFromAssembly en los ensamblados a los que hace referencia la aplicación web para detectar los tipos que se van a usar como entidades.

La solución consiste en establecer el valor de ContextTypeName de EntityDataSource en el nombre de tipo de la clase derivada de ObjectContext. Esto desactiva el mecanismo que examina todos los ensamblados a los que se hace referencia para los tipos de entidad.

Al establecer el campo ContextTypeName también se evita un problema funcional en el que EntityDataSource en .NET 4,0 produce una excepción ReflectionTypeLoadException cuando no puede cargar un tipo de un ensamblado a través de la reflexión. Este problema se ha corregido en .NET 4,5.

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7,5 entidades POCO y servidores proxy de seguimiento de cambios

Entity Framework le permite utilizar clases de datos personalizadas junto con su modelo de datos sin realizar ninguna modificación en las clases de datos. Esto significa que podrá utilizar objetos CLR "antiguos" (POCO), tales como objetos de dominio existentes, con el modelo de datos. Estas clases de datos POCO (también conocidas como objetos que ignoran la persistencia), que se asignan a las entidades que se definen en un modelo de datos, admiten la mayoría de los mismos comportamientos de consulta, inserción, actualización y eliminación que los tipos de entidad generados por las herramientas de Entity Data Model.

Entity Framework también puede crear clases de proxy derivadas de los tipos POCO, que se usan cuando se desea habilitar características como la carga diferida y el seguimiento de cambios automático en entidades POCO. Las clases POCO deben cumplir ciertos requisitos para permitir que Entity Framework use servidores proxy, como se describe aquí: [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx) .

Los proxies de seguimiento de oportunidades enviarán una notificación al administrador de estado de objetos cada vez que se cambie el valor de cualquiera de las propiedades de las entidades, por lo que Entity Framework conoce el estado real de las entidades todo el tiempo. Esto se hace agregando eventos de notificación al cuerpo de los métodos de establecedor de las propiedades y haciendo que el administrador de estado de objetos procese dichos eventos. Tenga en cuenta que la creación de una entidad de proxy normalmente será más costosa que crear una entidad POCO que no sea de proxy debido al conjunto agregado de eventos creados por Entity Framework.

Cuando una entidad POCO no tiene un proxy de seguimiento de cambios, se encuentran los cambios comparando el contenido de las entidades con una copia de un estado guardado anterior. Esta comparación profunda se convertirá en un proceso largo cuando tenga muchas entidades en el contexto, o cuando las entidades tengan una gran cantidad de propiedades, aunque no hayan cambiado desde que se realizó la última comparación.

En Resumen: pagará un impacto en el rendimiento al crear el proxy de seguimiento de cambios, pero el seguimiento de cambios le ayudará a acelerar el proceso de detección de cambios cuando las entidades tengan muchas propiedades o cuando tenga muchas entidades en el modelo. En el caso de las entidades con un número pequeño de propiedades en las que la cantidad de entidades no crece demasiado, es posible que los proxies de seguimiento de cambios no tengan muchas ventajas.

## <a name="8-loading-related-entities"></a>8 carga de entidades relacionadas

### <a name="81-lazy-loading-vs-eager-loading"></a>8,1 carga diferida frente a carga diligente

Entity Framework ofrece varias maneras de cargar las entidades relacionadas con la entidad de destino. Por ejemplo, al consultar productos, hay diferentes maneras de cargar los pedidos relacionados en el administrador de estado de objetos. Desde el punto de vista del rendimiento, la pregunta más importante que hay que tener en cuenta al cargar entidades relacionadas será si se va a usar la carga diferida o la carga diligente.

Cuando se usa la carga diligente, las entidades relacionadas se cargan junto con el conjunto de entidades de destino. Use una instrucción include en la consulta para indicar qué entidades relacionadas desea incorporar.

Cuando se usa la carga diferida, la consulta inicial solo se coloca en el conjunto de entidades de destino. Pero siempre que se tiene acceso a una propiedad de navegación, se emite otra consulta en el almacén para cargar la entidad relacionada.

Una vez que se ha cargado una entidad, cualquier consulta adicional para la entidad la cargará directamente desde el administrador de estado de objetos, tanto si usa la carga diferida como la carga diligente.

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8,2 Cómo elegir entre la carga diferida y la carga diligente

Lo importante es que comprenda la diferencia entre la carga diferida y la carga diligente para que pueda tomar la decisión correcta para su aplicación. Esto le ayudará a evaluar el equilibrio entre varias solicitudes en la base de datos frente a una solicitud única que puede contener una carga grande. Puede ser adecuado usar la carga diligente en algunas partes de la aplicación y la carga diferida en otras partes.

Como ejemplo de lo que sucede en el capó, supongamos que desea consultar los clientes que viven en el Reino Unido y su recuento de pedidos.

**Uso de la carga diligente**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

**Usar la carga diferida**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    context.ContextOptions.LazyLoadingEnabled = true;

    //Notice that the Include method call is missing in the query
    var ukCustomers = context.Customers.Where(c => c.Address.Country == "UK");

    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

Cuando se usa la carga diligente, se emite una consulta única que devuelve todos los clientes y todos los pedidos. El comando de almacenamiento tiene el siguiente aspecto:

``` SQL
SELECT
[Project1].[C1] AS [C1],
[Project1].[CustomerID] AS [CustomerID],
[Project1].[CompanyName] AS [CompanyName],
[Project1].[ContactName] AS [ContactName],
[Project1].[ContactTitle] AS [ContactTitle],
[Project1].[Address] AS [Address],
[Project1].[City] AS [City],
[Project1].[Region] AS [Region],
[Project1].[PostalCode] AS [PostalCode],
[Project1].[Country] AS [Country],
[Project1].[Phone] AS [Phone],
[Project1].[Fax] AS [Fax],
[Project1].[C2] AS [C2],
[Project1].[OrderID] AS [OrderID],
[Project1].[CustomerID1] AS [CustomerID1],
[Project1].[EmployeeID] AS [EmployeeID],
[Project1].[OrderDate] AS [OrderDate],
[Project1].[RequiredDate] AS [RequiredDate],
[Project1].[ShippedDate] AS [ShippedDate],
[Project1].[ShipVia] AS [ShipVia],
[Project1].[Freight] AS [Freight],
[Project1].[ShipName] AS [ShipName],
[Project1].[ShipAddress] AS [ShipAddress],
[Project1].[ShipCity] AS [ShipCity],
[Project1].[ShipRegion] AS [ShipRegion],
[Project1].[ShipPostalCode] AS [ShipPostalCode],
[Project1].[ShipCountry] AS [ShipCountry]
FROM ( SELECT
      [Extent1].[CustomerID] AS [CustomerID],
       [Extent1].[CompanyName] AS [CompanyName],
       [Extent1].[ContactName] AS [ContactName],
       [Extent1].[ContactTitle] AS [ContactTitle],
       [Extent1].[Address] AS [Address],
       [Extent1].[City] AS [City],
       [Extent1].[Region] AS [Region],
       [Extent1].[PostalCode] AS [PostalCode],
       [Extent1].[Country] AS [Country],
       [Extent1].[Phone] AS [Phone],
       [Extent1].[Fax] AS [Fax],
      1 AS [C1],
       [Extent2].[OrderID] AS [OrderID],
       [Extent2].[CustomerID] AS [CustomerID1],
       [Extent2].[EmployeeID] AS [EmployeeID],
       [Extent2].[OrderDate] AS [OrderDate],
       [Extent2].[RequiredDate] AS [RequiredDate],
       [Extent2].[ShippedDate] AS [ShippedDate],
       [Extent2].[ShipVia] AS [ShipVia],
       [Extent2].[Freight] AS [Freight],
       [Extent2].[ShipName] AS [ShipName],
       [Extent2].[ShipAddress] AS [ShipAddress],
       [Extent2].[ShipCity] AS [ShipCity],
       [Extent2].[ShipRegion] AS [ShipRegion],
       [Extent2].[ShipPostalCode] AS [ShipPostalCode],
       [Extent2].[ShipCountry] AS [ShipCountry],
      CASE WHEN ([Extent2].[OrderID] IS NULL) THEN CAST(NULL AS int) ELSE 1 END AS [C2]
      FROM  [dbo].[Customers] AS [Extent1]
      LEFT OUTER JOIN [dbo].[Orders] AS [Extent2] ON [Extent1].[CustomerID] = [Extent2].[CustomerID]
      WHERE N'UK' = [Extent1].[Country]
)  AS [Project1]
ORDER BY [Project1].[CustomerID] ASC, [Project1].[C2] ASC
```

Al usar la carga diferida, emitirá la siguiente consulta inicialmente:

``` SQL
SELECT
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[CompanyName] AS [CompanyName],
[Extent1].[ContactName] AS [ContactName],
[Extent1].[ContactTitle] AS [ContactTitle],
[Extent1].[Address] AS [Address],
[Extent1].[City] AS [City],
[Extent1].[Region] AS [Region],
[Extent1].[PostalCode] AS [PostalCode],
[Extent1].[Country] AS [Country],
[Extent1].[Phone] AS [Phone],
[Extent1].[Fax] AS [Fax]
FROM [dbo].[Customers] AS [Extent1]
WHERE N'UK' = [Extent1].[Country]
```

Y cada vez que se accede a la propiedad de navegación Orders de un cliente, se emite otra consulta similar a la siguiente en la tienda:

``` SQL
exec sp_executesql N'SELECT
[Extent1].[OrderID] AS [OrderID],
[Extent1].[CustomerID] AS [CustomerID],
[Extent1].[EmployeeID] AS [EmployeeID],
[Extent1].[OrderDate] AS [OrderDate],
[Extent1].[RequiredDate] AS [RequiredDate],
[Extent1].[ShippedDate] AS [ShippedDate],
[Extent1].[ShipVia] AS [ShipVia],
[Extent1].[Freight] AS [Freight],
[Extent1].[ShipName] AS [ShipName],
[Extent1].[ShipAddress] AS [ShipAddress],
[Extent1].[ShipCity] AS [ShipCity],
[Extent1].[ShipRegion] AS [ShipRegion],
[Extent1].[ShipPostalCode] AS [ShipPostalCode],
[Extent1].[ShipCountry] AS [ShipCountry]
FROM [dbo].[Orders] AS [Extent1]
WHERE [Extent1].[CustomerID] = @EntityKeyValue1',N'@EntityKeyValue1 nchar(5)',@EntityKeyValue1=N'AROUT'
```

Para obtener más información, vea [cargar objetos relacionados](https://msdn.microsoft.com/library/bb896272.aspx).

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>hoja de referencia diferida de carga 8.2.1 frente a carga diligente

No hay nada que se ajuste a todo para elegir la carga diligente frente a la carga diferida. Pruebe primero para comprender las diferencias entre ambas estrategias, de modo que pueda tomar una decisión bien fundamentada; Además, considere la posibilidad de que el código se ajuste a cualquiera de los siguientes escenarios:

| Escenario                                                                    | Nuestra sugerencia                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ¿Necesita acceder a muchas propiedades de navegación desde las entidades capturadas? | **No** : las dos opciones probablemente sí lo hagan. Sin embargo, si la carga de la consulta no es demasiado grande, puede experimentar ventajas de rendimiento mediante la carga diligente, ya que requerirá menos viajes de ida y vuelta de red para materializar los objetos. <br/> <br/> **Sí** : Si necesita tener acceso a muchas propiedades de navegación desde las entidades, lo haría mediante el uso de varias instrucciones include en la consulta con la carga diligente. Cuanto más entidades incluya, mayor será la carga que devolverá la consulta. Una vez que incluya tres o más entidades en la consulta, considere la posibilidad de cambiar a la carga diferida. |
| ¿Sabe exactamente qué datos se necesitarán en tiempo de ejecución?                   | La carga **no** diferida será mejor para usted. De lo contrario, puede acabar consultando los datos que no necesitará. <br/> <br/> **Sí** : la carga diligente es probablemente su mejor apuesta; ayuda a cargar conjuntos completos más rápido. Si la consulta requiere la captura de una cantidad muy grande de datos y se vuelve demasiado lenta, intente la carga diferida en su lugar.                                                                                                                                                                                                                                                       |
| ¿El código se ejecuta lejos de la base de datos? (mayor latencia de red)  | **No** : cuando la latencia de red no es un problema, el uso de la carga diferida puede simplificar el código. Recuerde que la topología de la aplicación puede cambiar, de modo que no asuma la proximidad de la base de datos para concedido. <br/> <br/> **Sí** : cuando la red es un problema, solo puede decidir cuál se adapta mejor a su escenario. Normalmente, la carga diligente será mejor porque requiere menos viajes de ida y vuelta.                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>8.2.2 problemas de rendimiento con varios includes

Cuando se escuchan preguntas de rendimiento que implican problemas en el tiempo de respuesta del servidor, el origen del problema es con frecuencia consultas con varias instrucciones include. Aunque incluir las entidades relacionadas en una consulta es eficaz, es importante comprender lo que sucede en segundo plano.

Tarda bastante tiempo en una consulta con varias instrucciones include en ella para pasar a través de nuestro compilador del plan interno y generar el comando de la tienda. La mayor parte de este tiempo se dedica a intentar optimizar la consulta resultante. El comando de almacenamiento generado contendrá una combinación externa o una Unión para cada inclusión, en función de la asignación. En las consultas como esta se incluirán gráficos de gran tamaño de la base de datos en una sola carga, lo que acerbate cualquier problema de ancho de banda, especialmente cuando hay mucha redundancia en la carga (por ejemplo, cuando se usan varios niveles de include para atravesar las asociaciones en la dirección uno a varios).

Puede comprobar los casos en los que las consultas devuelven cargas excesivamente grandes mediante el acceso al TSQL subyacente para la consulta mediante ToTraceString y la ejecución del comando Store en SQL Server Management Studio para ver el tamaño de la carga. En tales casos, puede intentar reducir el número de instrucciones include en la consulta para incorporar los datos que necesita. O bien, puede dividir la consulta en una secuencia más pequeña de subconsultas, por ejemplo:

**Antes de interrumpir la consulta:**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var customers = from c in context.Customers.Include(c => c.Orders)
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

**Después de interrumpir la consulta:**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var orders = from o in context.Orders
                 where o.Customer.LastName.StartsWith(lastNameParameter)
                 select o;

    orders.Load();

    var customers = from c in context.Customers
                    where c.LastName.StartsWith(lastNameParameter)
                    select c;

    foreach (Customer customer in customers)
    {
        ...
    }
}
```

Esto solo funcionará en las consultas con seguimiento, ya que estamos haciendo uso de la capacidad que el contexto tiene para realizar la resolución de identidades y la corrección de asociación automáticamente.

Al igual que con la carga diferida, el compromiso será más consultas para cargas más pequeñas. También puede usar las proyecciones de propiedades individuales para seleccionar explícitamente los datos que necesita de cada entidad, pero no se cargarán las entidades en este caso y las actualizaciones no se admitirán.

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>8.2.3 solución alternativa para obtener la carga diferida de propiedades

Actualmente, Entity Framework no admite la carga diferida de propiedades escalares o complejas. Sin embargo, en los casos en los que tiene una tabla que incluye un objeto grande, como un BLOB, puede usar la división de tablas para separar las propiedades grandes en una entidad independiente. Por ejemplo, supongamos que tiene una tabla de productos que incluye una columna de foto varbinary. Si no necesita tener acceso a esta propiedad con frecuencia en las consultas, puede usar la división de tablas para traer solo las partes de la entidad que necesite normalmente. La entidad que representa la foto del producto solo se cargará cuando lo necesite explícitamente.

Un buen recurso que muestra cómo habilitar la división de tablas es la entrada de blog "división de tablas en Entity Framework" de Gil Fink: \<http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx> .

## <a name="9-other-considerations"></a>9 otras consideraciones

### <a name="91-server-garbage-collection"></a>Recolección de elementos no utilizados de 9,1 Server

Algunos usuarios podrían experimentar contención de recursos que limite el paralelismo que esperan cuando el recolector de elementos no utilizados no está configurado correctamente. Siempre que se use EF en un escenario multiproceso o en cualquier aplicación similar a un sistema de servidor, asegúrese de habilitar la recolección de elementos no utilizados de servidor. Esto se hace a través de una configuración simple en el archivo de configuración de la aplicación:

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

Esto debería reducir la contención del subproceso y aumentar el rendimiento hasta un 30% en escenarios saturados de CPU. En términos generales, siempre debe probar el comportamiento de la aplicación mediante la recolección de elementos no utilizados clásica (que está mejor optimizada para los escenarios de la interfaz de usuario y del cliente), así como la recolección de elementos no utilizados del servidor.

### <a name="92-autodetectchanges"></a>9,2 AutoDetectChanges

Como se mencionó anteriormente, Entity Framework podrían mostrar problemas de rendimiento cuando la memoria caché de objetos tiene muchas entidades. Ciertas operaciones, como agregar, quitar, buscar, entrada y SaveChanges, desencadenan llamadas a DetectChanges que pueden consumir una gran cantidad de CPU en función del tamaño de la memoria caché de objetos. La razón de esto es que la memoria caché de objetos y el administrador de estado de objetos intentan permanecer sincronizados como sea posible en cada operación realizada en un contexto para que se garantice que los datos generados son correctos en una amplia gama de escenarios.

Por lo general, se recomienda dejar habilitada la detección automática de cambios de Entity Framework para toda la vida de la aplicación. Si el escenario se ve afectado negativamente por el uso intensivo de la CPU y los perfiles indican que la causa es la llamada a DetectChanges, considere la posibilidad de desactivar temporalmente AutoDetectChanges en la parte confidencial del código:

``` csharp
try
{
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    ...
}
finally
{
    context.Configuration.AutoDetectChangesEnabled = true;
}
```

Antes de desactivar AutoDetectChanges, es conveniente comprender que esto podría hacer que Entity Framework pierda su capacidad de realizar un seguimiento de determinada información sobre los cambios que se están llevando a cabo en las entidades. Si se trata de forma incorrecta, puede provocar incoherencias de datos en la aplicación. Para obtener más información sobre la desactivación de AutoDetectChanges, lea \<http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/> .

### <a name="93-context-per-request"></a>9,3 contexto por solicitud

Los contextos de Entity Framework están diseñados para usarse como instancias de corta duración con el fin de proporcionar la experiencia de rendimiento óptima. Se espera que los contextos tengan una duración corta y se descarten, y como tal se han implementado para ser muy ligeros y reutilizar los metadatos siempre que sea posible. En escenarios web es importante tener esto en cuenta y no tener un contexto mayor que la duración de una única solicitud. Del mismo modo, en escenarios que no son Web, el contexto debe descartarse en función de los distintos niveles de almacenamiento en caché en el Entity Framework. En general, debe evitar tener una instancia de contexto a lo largo de la vida de la aplicación, así como contextos por subproceso y contextos estáticos.

### <a name="94-database-null-semantics"></a>9,4 semántica nula de la base de datos

De forma predeterminada, Entity Framework generará código SQL que tiene una semántica de comparación de C \# null. Considere la consulta de ejemplo siguiente:

``` csharp
            int? categoryId = 7;
            int? supplierId = 8;
            decimal? unitPrice = 0;
            short? unitsInStock = 100;
            short? unitsOnOrder = 20;
            short? reorderLevel = null;

            var q = from p incontext.Products
                    wherep.Category.CategoryName == "Beverages"
                          || (p.CategoryID == categoryId
                                || p.SupplierID == supplierId
                                || p.UnitPrice == unitPrice
                                || p.UnitsInStock == unitsInStock
                                || p.UnitsOnOrder == unitsOnOrder
                                || p.ReorderLevel == reorderLevel)
                    select p;

            var r = q.ToList();
```

En este ejemplo, vamos a comparar un número de variables que aceptan valores NULL con las propiedades que aceptan valores NULL en la entidad, como SupplierID y UnitPrice. El SQL generado para esta consulta le preguntará si el valor del parámetro es el mismo que el valor de la columna, o si los valores del parámetro y de la columna son NULL. Esto ocultará el modo en que el servidor de base de datos controla los valores NULL y proporcionará una experiencia coherente con C \# null en diferentes proveedores de bases de datos. Por otro lado, el código generado es un poco complicado y es posible que no funcione correctamente cuando la cantidad de comparaciones en la instrucción WHERE de la consulta aumenta a un número grande.

Una manera de tratar esta situación es usar la semántica de valores NULL de base de datos. Tenga en cuenta que esto podría comportarse de forma diferente a la \# semántica de valores NULL de C, ya que ahora Entity Framework generará un SQL más sencillo que exponga la manera en que el motor de base de datos controla los valores NULL. La semántica de valores NULL de base de datos se puede activar por contexto con una sola línea de configuración en la configuración de contexto:

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

Las consultas de tamaño pequeño a medio no mostrarán una mejora perceptible del rendimiento al usar la semántica de las bases de datos nulas, pero la diferencia se volverá más perceptible en las consultas con un gran número de comparaciones de NULL potenciales.

En la consulta de ejemplo anterior, la diferencia de rendimiento era inferior al 2% en un microbenchmark que se ejecuta en un entorno controlado.

### <a name="95-async"></a>9,5 Async

Entity Framework 6 presentó la compatibilidad con operaciones asincrónicas cuando se ejecutan en .NET 4,5 o versiones posteriores. En su mayor parte, las aplicaciones que tienen contención relacionada con la e/s beneficiarán al máximo el uso de las operaciones asincrónicas de consulta y guardado. Si la aplicación no se ve afectada por la contención de e/s, el uso de Async hará que, en los mejores casos, se ejecute sincrónicamente y devuelva el resultado en la misma cantidad de tiempo que una llamada sincrónica, o en el peor de los casos, simplemente postergue la ejecución a una tarea asincrónica y agregue tiempo adicional a la finalización del escenario.

Para obtener información sobre cómo funciona la programación asincrónica que le ayudará a decidir si Async mejorará el rendimiento de la aplicación [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx) . Para obtener más información sobre el uso de operaciones asincrónicas en Entity Framework, consulte [Async Query and Save](xref:ef6/fundamentals/async
).

### <a name="96-ngen"></a>NGEN 9,6

Entity Framework 6 no se incluye en la instalación predeterminada de .NET Framework. Como tal, los ensamblados de Entity Framework no son de forma predeterminada NGEN, lo que significa que todo el código de Entity Framework está sujeto a los mismos costos de JIT'ing que cualquier otro ensamblado de MSIL. Esto podría degradar la experiencia F5 durante el desarrollo y también el inicio en frío de la aplicación en los entornos de producción. Con el fin de reducir los costos de CPU y memoria de JIT'ing, es aconsejable que NGEN las imágenes de Entity Framework según corresponda. Para obtener más información sobre cómo mejorar el rendimiento de inicio de Entity Framework 6 con NGEN, vea [mejorar el rendimiento de inicio con Ngen](xref:ef6/fundamentals/performance/ngen).

### <a name="97-code-first-versus-edmx"></a>9,7 Code First frente a EDMX

Entity Framework razones sobre el problema de falta de coincidencia de impedancia entre la programación orientada a objetos y las bases de datos relacionales con una representación en memoria del modelo conceptual (los objetos), el esquema de almacenamiento (la base de datos) y una asignación entre los dos. Estos metadatos se denominan Entity Data Model o EDM para abreviar. Desde este EDM, Entity Framework derivarán las vistas para los datos de ida y vuelta de los objetos de la memoria a la base de datos y viceversa.

Cuando se usa Entity Framework con un archivo EDMX que especifica formalmente el modelo conceptual, el esquema de almacenamiento y la asignación, la fase de carga del modelo solo tiene que validar que el EDM es correcto (por ejemplo, asegúrese de que no faltan asignaciones), generar las vistas y, a continuación, validar las vistas y tener estos metadatos listos para su uso. Solo entonces se puede ejecutar una consulta o se pueden guardar datos nuevos en el almacén de datos.

El enfoque Code First es, en esencia, un generador de Entity Data Model sofisticado. El Entity Framework tiene que generar un EDM a partir del código proporcionado; lo hace mediante el análisis de las clases implicadas en el modelo, la aplicación de convenciones y la configuración del modelo a través de la API fluida. Una vez creado el EDM, el Entity Framework se comporta de la misma manera que tenía un archivo EDMX presente en el proyecto. Por lo tanto, la generación del modelo a partir de Code First agrega complejidad adicional que se traduce en un tiempo de inicio más lento para el Entity Framework en comparación con el uso de EDMX. El costo depende por completo del tamaño y la complejidad del modelo que se está compilando.

Al elegir usar EDMX frente a Code First, es importante saber que la flexibilidad introducida por Code First aumenta el costo de crear el modelo por primera vez. Si la aplicación puede resistir el costo de esta primera carga, normalmente Code First será la mejor manera de ir.

## <a name="10-investigating-performance"></a>10 investigación del rendimiento

### <a name="101-using-the-visual-studio-profiler"></a>10,1 uso del generador de perfiles de Visual Studio

Si tiene problemas de rendimiento con el Entity Framework, puede usar un generador de perfiles como el que está integrado en Visual Studio para ver dónde está gastando su aplicación su tiempo. Esta es la herramienta que se usa para generar los gráficos circulares en la entrada de blog "Exploring the Performance of the ADO.NET Entity Framework-Part 1" ( \<https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1> ) que muestra dónde Entity Framework dedica su tiempo durante las consultas en frío y en caliente.

La entrada de blog "Entity Framework de generación de perfiles con el generador de perfiles de Visual Studio 2010 Profiler" escrita por el equipo de asesoramiento de datos y modelado de clientes muestra un ejemplo real de cómo usaba el generador de perfiles para investigar un problema de   \<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler> rendimiento. Esta entrada se escribió para una aplicación Windows. Si tiene que generar perfiles de una aplicación Web, las herramientas del Windows performance Recorder (WPR) y del analizador de rendimiento de Windows (WPA) pueden funcionar mejor que si se trabajaran desde Visual Studio. WPR y WPA forman parte del kit de herramientas de rendimiento de Windows, que se incluye con el kit de evaluación e implementación de Windows ( [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982) ).

### <a name="102-applicationdatabase-profiling"></a>10,2 generación de perfiles de aplicación/base de datos

Herramientas como el generador de perfiles integrado en Visual Studio le indica dónde dedica tiempo la aplicación.Existe otro tipo de generador de perfiles que realiza el análisis dinámico de la aplicación en ejecución, ya sea en producción o en preproducción en función de las necesidades, y busca errores comunes y antipatrones de acceso a la base de datos.

Dos perfiles disponibles comercialmente son Entity Framework Profiler ( \<http://efprof.com> ) y ORMProfiler ( \<http://ormprofiler.com> ).

Si la aplicación es una aplicación MVC que usa Code First, puede usar MiniProfiler de StackExchange. Scott Hanselman describe esta herramienta en su blog en: \<http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx> .

Para más información sobre la generación de perfiles de la actividad de base de datos de la aplicación, consulte el artículo de MSDN Magazine de Julie Lerman titulado [actividad de base de datos de generación de perfiles en el Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).

### <a name="103-database-logger"></a>registrador de base de datos 10,3

Si usa Entity Framework 6, considere también el uso de la funcionalidad de registro integrada. Se puede indicar a la propiedad de base de datos del contexto que registre su actividad a través de una sencilla configuración de una línea:

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

En este ejemplo, la actividad de la base de datos se registrará en la consola, pero la propiedad log puede configurarse para llamar a cualquier delegado de cadena de acción &lt; &gt; .

Si desea habilitar el registro de base de datos sin volver a compilar y usa Entity Framework 6,1 o una versión posterior, puede hacerlo agregando un interceptor en el archivo web.config o app.config de la aplicación.

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

Para obtener más información sobre cómo agregar registro sin volver a compilar, vaya a \<http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/> .

## <a name="11-appendix"></a>11 Apéndice

### <a name="111-a-test-environment"></a>11,1 A. entorno de prueba

Este entorno utiliza una configuración de dos equipos con la base de datos en un equipo independiente de la aplicación cliente. Las máquinas están en el mismo bastidor, por lo que la latencia de red es relativamente baja, pero más realista que un entorno de un solo equipo.

#### <a name="1111-app-server"></a>Servidor de aplicaciones 11.1.1

##### <a name="11111-software-environment"></a>Entorno de software de 11.1.1.1

-   Entity Framework 4 entorno de software
    -   Nombre del sistema operativo: Windows Server 2008 R2 Enterprise SP1.
    -   Visual Studio 2010 – Ultimate.
    -   Visual Studio 2010 SP1 (solo para algunas comparaciones).
-   Entity Framework 5 y 6 entorno de software
    -   Nombre del sistema operativo: Windows 8.1 Enterprise
    -   Visual Studio 2013: Ultimate.

##### <a name="11112-hardware-environment"></a>Entorno de hardware de 11.1.1.2

-   Procesador dual: Intel (R) Xeon (R) CPU L5520 W3530 @ 2,27 GHz, 2261 Mhz8 GHz, 4 núcleos, 84 procesadores lógicos.
-   2412 GB RamRAM.
-   136 GB SCSI250GB SATA 7200 RPM GB/s unidad dividida en 4 particiones.

#### <a name="1112-db-server"></a>servidor de 11.1.2 DB

##### <a name="11121-software-environment"></a>Entorno de software de 11.1.2.1

-   Nombre del sistema operativo: Windows Server 2008 R 28.1 Enterprise SP1.
-   SQL Server 2008 R22012.

##### <a name="11122-hardware-environment"></a>Entorno de hardware de 11.1.2.2

-   Procesador único: Intel (R) Xeon (R) CPU L5520 @ 2,27 GHz, 2261 MhzES-1620 0 @ 3,60 GHz, 4 núcleos, 8 procesadores lógicos.
-   824 GB RamRAM.
-   465 GB ATA500GB SATA 7200 RPM 6 GB/s unidad dividida en 4 particiones.

### <a name="112-b-query-performance-comparison-tests"></a>11,2 B. pruebas de comparación de rendimiento de consultas

El modelo Northwind se utilizó para ejecutar estas pruebas. Se generó a partir de la base de datos mediante el diseñador de Entity Framework. A continuación, se usó el código siguiente para comparar el rendimiento de las opciones de ejecución de la consulta:

``` csharp
using System;
using System.Collections.Generic;
using System.Data;
using System.Data.Common;
using System.Data.Entity.Infrastructure;
using System.Data.EntityClient;
using System.Data.Objects;
using System.Linq;

namespace QueryComparison
{
    public partial class NorthwindEntities : ObjectContext
    {
        private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
            (NorthwindEntities context, string categoryName) =>
                context.Products.Where(p => p.Category.CategoryName == categoryName)
                );

        public IQueryable<Product> InvokeProductsForCategoryCQ(string categoryName)
        {
            return productsForCategoryCQ(this, categoryName);
        }
    }

    public class QueryTypePerfComparison
    {
        private static string entityConnectionStr = @"metadata=res://*/Northwind.csdl|res://*/Northwind.ssdl|res://*/Northwind.msl;provider=System.Data.SqlClient;provider connection string='data source=.;initial catalog=Northwind;integrated security=True;multipleactiveresultsets=True;App=EntityFramework'";

        public void LINQIncludingContextCreation()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTracking()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                context.Products.MergeOption = MergeOption.NoTracking;

                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void CompiledQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                var q = context.InvokeProductsForCategoryCQ("Beverages");
                q.ToList();
            }
        }

        public void ObjectQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
                products.ToList();
            }
        }

        public void EntityCommand()
        {
            using (EntityConnection eConn = new EntityConnection(entityConnectionStr))
            {
                eConn.Open();
                EntityCommand cmd = eConn.CreateCommand();
                cmd.CommandText = "Select p From NorthwindEntities.Products As p Where p.Category.CategoryName = 'Beverages'";

                using (EntityDataReader reader = cmd.ExecuteReader(CommandBehavior.SequentialAccess))
                {
                    List<Product> productsList = new List<Product>();
                    while (reader.Read())
                    {
                        DbDataRecord record = (DbDataRecord)reader.GetValue(0);

                        // 'materialize' the product by accessing each field and value. Because we are materializing products, we won't have any nested data readers or records.
                        int fieldCount = record.FieldCount;

                        // Treat all products as Product, even if they are the subtype DiscontinuedProduct.
                        Product product = new Product();  

                        product.ProductID = record.GetInt32(0);
                        product.ProductName = record.GetString(1);
                        product.SupplierID = record.GetInt32(2);
                        product.CategoryID = record.GetInt32(3);
                        product.QuantityPerUnit = record.GetString(4);
                        product.UnitPrice = record.GetDecimal(5);
                        product.UnitsInStock = record.GetInt16(6);
                        product.UnitsOnOrder = record.GetInt16(7);
                        product.ReorderLevel = record.GetInt16(8);
                        product.Discontinued = record.GetBoolean(9);

                        productsList.Add(product);
                    }
                }
            }
        }

        public void ExecuteStoreQuery()
        {
            using (NorthwindEntities context = new NorthwindEntities())
            {
                ObjectResult<Product> beverages = context.ExecuteStoreQuery<Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Database.SqlQuery\<QueryComparison.DbC.Product>(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void ExecuteStoreQueryDbSet()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var beverages = context.Products.SqlQuery(
@"    SELECT        P.ProductID, P.ProductName, P.SupplierID, P.CategoryID, P.QuantityPerUnit, P.UnitPrice, P.UnitsInStock, P.UnitsOnOrder, P.ReorderLevel, P.Discontinued
    FROM            Products AS P INNER JOIN Categories AS C ON P.CategoryID = C.CategoryID
    WHERE        (C.CategoryName = 'Beverages')"
);
                beverages.ToList();
            }
        }

        public void LINQIncludingContextCreationDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {                 
                var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }

        public void LINQNoTrackingDbContext()
        {
            using (var context = new QueryComparison.DbC.NorthwindEntities())
            {
                var q = context.Products.AsNoTracking().Where(p => p.Category.CategoryName == "Beverages");
                q.ToList();
            }
        }
    }
}
```

### <a name="113-c-navision-model"></a>Modelo 11,3 C. Navision

La base de datos de Navision es una base de datos grande que se usa para la demostración de Microsoft Dynamics – NAV. El modelo conceptual generado contiene 1005 conjuntos de entidades y conjuntos de asociaciones de 4227. El modelo usado en la prueba es "plano": no se ha agregado ninguna herencia a él.

#### <a name="1131-queries-used-for-navision-tests"></a>11.3.1 consultas usadas para las pruebas de Navision

La lista de consultas utilizada con el modelo de Navision contiene tres categorías de consultas Entity SQL:

##### <a name="11311-lookup"></a>búsqueda 11.3.1.1

Una consulta de búsqueda simple sin agregaciones

-   Recuento: 16232
-   Ejemplo:

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 SingleAggregating

Una consulta de BI normal con varias agregaciones, pero sin subtotales (consulta única)

-   Recuento: 2313
-   Ejemplo:

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

Donde MDF \_ SessionLogin \_ Time \_ Max () se define en el modelo como:

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 AggregatingSubtotals

Una consulta de BI con agregaciones y subtotales (a través de Union All)

-   Recuento: 178
-   Ejemplo:

``` xml
  <Query complexity="AggregatingSubtotals">
    <CommandText>
using NavisionFK;
function AmountConsumed(entities Collection([CRONUS_International_Ltd__Zone])) as
(
    Edm.Sum(select value N.Block_Movement FROM entities as E, E.CRONUS_International_Ltd__Bin as N)
)
function AmountConsumed(P1 Edm.Int32) as
(
    AmountConsumed(select value e from NavisionFKContext.CRONUS_International_Ltd__Zone as e where e.Zone_Ranking = P1)
)
----------------------------------------------------------------------------------------------------------------------
(
    select top(10) Zone_Ranking, Cross_Dock_Bin_Zone, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking, E.Cross_Dock_Bin_Zone
)
union all
(
    select top(10) Zone_Ranking, Cast(null as Edm.Byte) as P2, AmountConsumed(GroupPartition(E))
    from NavisionFKContext.CRONUS_International_Ltd__Zone as E
    where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed
    group by E.Zone_Ranking
)
union all
{
    Row(Cast(null as Edm.Int32) as P1, Cast(null as Edm.Byte) as P2, AmountConsumed(select value E
                                                                         from NavisionFKContext.CRONUS_International_Ltd__Zone as E
                                                                         where AmountConsumed(E.Zone_Ranking) > @MinAmountConsumed))
}</CommandText>
    <Parameters>
      <Parameter Name="MinAmountConsumed" DbType="Int32" Value="10000" />
    </Parameters>
  </Query>
```
