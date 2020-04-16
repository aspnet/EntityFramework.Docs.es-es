---
title: Consideraciones de rendimiento para EF4, EF5 y EF6 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: d6d5a465-6434-45fa-855d-5eb48c61a2ea
ms.openlocfilehash: 0ece383bb5083b41c7a2636c009473333af6d3e2
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434344"
---
# <a name="performance-considerations-for-ef-4-5-and-6"></a>Consideraciones de rendimiento para EF 4, 5 y 6
Por David Obando, Eric Dettinger y otros

Publicado: Abril 2012

última actualización: mayo de 2014

------------------------------------------------------------------------

## <a name="1-introduction"></a>1. Introducción

Los marcos de asignación relacional de objetos son una forma cómoda de proporcionar una abstracción para el acceso a datos en una aplicación orientada a objetos. Para las aplicaciones .NET, el O/RM recomendado por Microsoft es Entity Framework. Sin embargo, con cualquier abstracción, el rendimiento puede convertirse en una preocupación.

Este documento técnico se escribió para mostrar las consideraciones de rendimiento al desarrollar aplicaciones mediante Entity Framework, para dar a los desarrolladores una idea de los algoritmos internos de Entity Framework que pueden afectar al rendimiento y para proporcionar sugerencias para la investigación y la mejora del rendimiento en sus aplicaciones que usan Entity Framework. Hay una serie de buenos temas sobre el rendimiento ya disponibles en la web, y también hemos intentado apuntar a estos recursos siempre que sea posible.

El rendimiento es un tema complicado. Este documento técnico está pensado como un recurso para ayudarle a tomar decisiones relacionadas con el rendimiento de las aplicaciones que usan Entity Framework. Hemos incluido algunas métricas de prueba para demostrar el rendimiento, pero estas métricas no están pensadas como indicadores absolutos del rendimiento que verá en la aplicación.

A efectos prácticos, este documento supone que Entity Framework 4 se ejecuta en .NET 4.0 y Entity Framework 5 y 6 se ejecutan en .NET 4.5. Muchas de las mejoras de rendimiento realizadas para Entity Framework 5 residen dentro de los componentes principales que se incluyen con .NET 4.5.

Entity Framework 6 es una versión fuera de banda y no depende de los componentes de Entity Framework que se envían con .NET. Entity Framework 6 funciona tanto en .NET 4.0 como en .NET 4.5 y puede ofrecer una gran ventaja de rendimiento a aquellos que no han actualizado desde .NET 4.0 pero desean los bits de Entity Framework más recientes en su aplicación. Cuando este documento menciona Entity Framework 6, hace referencia a la última versión disponible en el momento de escribir este artículo: versión 6.1.0.

## <a name="2-cold-vs-warm-query-execution"></a>2. Ejecución de consultas frías y cálidas

La primera vez que se realiza una consulta en un modelo determinado, Entity Framework realiza mucho trabajo en segundo plano para cargar y validar el modelo. Con frecuencia nos referimos a esta primera consulta como una consulta "fría".Las consultas adicionales en un modelo ya cargado se conocen como consultas "cálidas" y son mucho más rápidas.

Vamos a tener una vista de alto nivel de dónde se emplea el tiempo al ejecutar una consulta mediante Entity Framework y veamos dónde están mejorando las cosas en Entity Framework 6.

**Primera ejecución de consultas: consulta en frío**

| Escrituras de usuario de código                                                                                     | Acción                    | Impacto en el rendimiento de EF4                                                                                                                                                                                                                                                                                                                                                                                                        | Impacto en el rendimiento de EF5                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Impacto en el rendimiento de EF6                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Creación de contexto          | Media                                                                                                                                                                                                                                                                                                                                                                                                                        | Media                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Creación de expresiones de consulta | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                           | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| `  var c1 = q1.First();`                                                                             | Ejecución de consultas LINQ      | - Carga de metadatos: Alta pero almacenada en caché <br/> - Generación de vistas: Potencialmente muy alta pero almacenada en caché <br/> - Evaluación de parámetros: Medio <br/> - Traducción de consultas: Medio <br/> - Generación de materializadores: Medio pero en caché <br/> - Ejecución de consultas de base de datos: potencialmente alta <br/> + Connection.Open <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Materialización de objetos: Medio <br/> - Búsqueda de identidad: Medio | - Carga de metadatos: Alta pero almacenada en caché <br/> - Generación de vistas: Potencialmente muy alta pero almacenada en caché <br/> - Evaluación de parámetros: Bajo <br/> - Traducción de consultas: Mediana pero almacenada en caché <br/> - Generación de materializadores: Medio pero en caché <br/> - Ejecución de consultas de base de datos: potencialmente alta (mejores consultas en algunas situaciones) <br/> + Connection.Open <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Materialización de objetos: Medio <br/> - Búsqueda de identidad: Medio | - Carga de metadatos: Alta pero almacenada en caché <br/> - Generación de vistas: Mediana pero almacenada en caché <br/> - Evaluación de parámetros: Bajo <br/> - Traducción de consultas: Mediana pero almacenada en caché <br/> - Generación de materializadores: Medio pero en caché <br/> - Ejecución de consultas de base de datos: potencialmente alta (mejores consultas en algunas situaciones) <br/> + Connection.Open <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Materialización de objetos: Medio (más rápido que EF5) <br/> - Búsqueda de identidad: Medio |
| `}`                                                                                                  | Connection.Close          | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                           | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |


**Segunda ejecución de consultas – consulta cálida**

| Escrituras de usuario de código                                                                                     | Acción                    | Impacto en el rendimiento de EF4                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Impacto en el rendimiento de EF5                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Impacto en el rendimiento de EF6                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|:-----------------------------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `using(var db = new MyContext())` <br/> `{`                                                          | Creación de contexto          | Media                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            | Media                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var q1 = ` <br/> `    from c in db.Customers` <br/> `    where c.Id == id1` <br/> `    select c;` | Creación de expresiones de consulta | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `  var c1 = q1.First();`                                                                             | Ejecución de consultas LINQ      | - Búsqueda de ~~carga~~ de metadatos: ~~Alta pero en caché~~ Baja <br/> - Ver búsqueda de ~~generación:~~ ~~Potencialmente muy alta pero en caché~~ Baja <br/> - Evaluación de parámetros: Medio <br/> - Búsqueda de ~~traducción~~ de consultas: Medio <br/> - Búsqueda de ~~generación~~ de materializadores: ~~Medio pero en caché~~ Bajo <br/> - Ejecución de consultas de base de datos: potencialmente alta <br/> + Connection.Open <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Materialización de objetos: Medio <br/> - Búsqueda de identidad: Medio | - Búsqueda de ~~carga~~ de metadatos: ~~Alta pero en caché~~ Baja <br/> - Ver búsqueda de ~~generación:~~ ~~Potencialmente muy alta pero en caché~~ Baja <br/> - Evaluación de parámetros: Bajo <br/> - Búsqueda de ~~traducción~~ de consultas: ~~Medio pero en caché~~ Bajo <br/> - Búsqueda de ~~generación~~ de materializadores: ~~Medio pero en caché~~ Bajo <br/> - Ejecución de consultas de base de datos: potencialmente alta (mejores consultas en algunas situaciones) <br/> + Connection.Open <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Materialización de objetos: Medio <br/> - Búsqueda de identidad: Medio | - Búsqueda de ~~carga~~ de metadatos: ~~Alta pero en caché~~ Baja <br/> - Ver búsqueda de ~~generación:~~ ~~Medio pero en caché~~ Bajo <br/> - Evaluación de parámetros: Bajo <br/> - Búsqueda de ~~traducción~~ de consultas: ~~Medio pero en caché~~ Bajo <br/> - Búsqueda de ~~generación~~ de materializadores: ~~Medio pero en caché~~ Bajo <br/> - Ejecución de consultas de base de datos: potencialmente alta (mejores consultas en algunas situaciones) <br/> + Connection.Open <br/> + Command.ExecuteReader <br/> + DataReader.Read <br/> Materialización de objetos: Medio (más rápido que EF5) <br/> - Búsqueda de identidad: Medio |
| `}`                                                                                                  | Connection.Close          | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Bajo                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |


Hay varias maneras de reducir el costo de rendimiento de las consultas en frío y en caliente, y echaremos un vistazo a ellas en la siguiente sección. Específicamente, veremos cómo reducir el costo de carga del modelo en consultas en frío mediante el uso de vistas generadas previamente, lo que debería ayudar a aliviar los problemas de rendimiento experimentados durante la generación de vistas. Para las consultas en caliente, trataremos el almacenamiento en caché del plan de consultas, sin consultas de seguimiento y diferentes opciones de ejecución de consultas.

### <a name="21-what-is-view-generation"></a>2.1 ¿Qué es View Generation?

Para entender qué es la generación de vistas, primero debemos entender qué son las "vistas de asignación". Las vistas de asignación son representaciones ejecutables de las transformaciones especificadas en la asignación para cada conjunto de entidades y asociación. Internamente, estas vistas de asignación toman la forma de CQT (árboles de consulta canónicos). Hay dos tipos de vistas de asignación:

-   Vistas de consulta: representan la transformación necesaria para pasar del esquema de base de datos al modelo conceptual.
-   Actualizar vistas: representan la transformación necesaria para pasar del modelo conceptual al esquema de base de datos.

Tenga en cuenta que el modelo conceptual puede diferir del esquema de base de datos de varias maneras. Por ejemplo, se puede usar una sola tabla para almacenar los datos de dos tipos de entidad diferentes. La herencia y las asignaciones no triviales desempeñan un papel en la complejidad de las vistas de asignación.

El proceso de calcular estas vistas en función de la especificación de la asignación es lo que llamamos generación de vistas. La generación de vistas puede tener lugar dinámicamente cuando se carga un modelo, o en tiempo de compilación, mediante el uso de "vistas generadas previamente"; estos últimos se serializan en forma de\# instrucciones Entity SQL en un archivo C o VB.

Cuando se generan vistas, también se validan. Desde el punto de vista del rendimiento, la gran mayoría del costo de generación de vistas es en realidad la validación de las vistas, lo que garantiza que las conexiones entre las entidades tengan sentido y tengan la cardinalidad correcta para todas las operaciones admitidas.

Cuando se ejecuta una consulta sobre un conjunto de entidades, la consulta se combina con la vista de consulta correspondiente y el resultado de esta composición se ejecuta a través del compilador de plan para crear la representación de la consulta que el almacén de respaldo puede comprender. Para SQL ServerSQL Server, el resultado final de esta compilación será una instrucción SELECT de T-SQL. La primera vez que se realiza una actualización sobre un conjunto de entidades, la vista de actualización se ejecuta a través de un proceso similar para transformarla en instrucciones DML para la base de datos de destino.

### <a name="22-factors-that-affect-view-generation-performance"></a>2.2 Factores que afectan al rendimiento de View Generation

El rendimiento del paso de generación de vistas no solo depende del tamaño del modelo, sino también de la interconexión del modelo. Si dos Entidades están conectadas a través de una cadena de herencia o una Asociación, se dice que están conectadas. Del mismo modo, si dos tablas están conectadas a través de una clave externa, están conectadas. A medida que aumenta el número de entidades y tablas conectadas en los esquemas, aumenta el costo de generación de vistas.

El algoritmo que usamos para generar y validar vistas es exponencial en el peor de los casos, aunque usamos algunas optimizaciones para mejorar esto. Los factores más importantes que parecen afectar negativamente al rendimiento son:

-   Tamaño del modelo, haciendo referencia al número de entidades y a la cantidad de asociaciones entre estas entidades.
-   Complejidad del modelo, específicamente herencia que implica un gran número de tipos.
-   Uso de asociaciones independientes, en lugar de asociaciones de clave externa.

Para modelos pequeños y sencillos, el costo puede ser lo suficientemente pequeño como para no molestarse en el uso de vistas generadas previamente. A medida que aumenta el tamaño y la complejidad del modelo, hay varias opciones disponibles para reducir el costo de generación y validación de vistas.

### <a name="23-using-pre-generated-views-to-decrease-model-load-time"></a>2.3 Uso de vistas pregeneradas para reducir el tiempo de carga del modelo

Para obtener información detallada sobre cómo usar vistas generadas previamente en Entity Framework 6, visite [Vistas de asignación generadas previamente](~/ef6/fundamentals/performance/pre-generated-views.md)

#### <a name="231-pre-generated-views-using-the-entity-framework-power-tools-community-edition"></a>2.3.1 Vistas pregeneradas mediante Entity Framework Power Tools Community Edition

Puede usar [Entity Framework 6 Power Tools Community Edition](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) para generar vistas de los modelos EDMX y Code First haciendo clic con el botón derecho en el archivo de clase de modelo y utilizando el menú Entity Framework para seleccionar "Generar vistas". Entity Framework Power Tools Community Edition solo funciona en contextos derivados de DbContext.

#### <a name="232-how-to-use-pre-generated-views-with-a-model-created-by-edmgen"></a>2.3.2 Cómo utilizar vistas pregeneradas con un modelo creado por EDMGen

EDMGen es una utilidad que se incluye con .NET y funciona con Entity Framework 4 y 5, pero no con Entity Framework 6. EDMGen permite generar un archivo de modelo, la capa de objetos y las vistas desde la línea de comandos. Una de las salidas será un archivo Views en\#su idioma de elección, VB o C . Se trata de un archivo de código que contiene fragmentos de código de Entity SQLEntity SQL para cada conjunto de entidades. Para habilitar las vistas generadas previamente, simplemente incluya el archivo en el proyecto.

Si realiza modificaciones manualmente en los archivos de esquema del modelo, deberá volver a generar el archivo de vistas. Puede hacerlo ejecutando EDMGen con el **/mode:ViewGeneration marca.**

#### <a name="233-how-to-use-pre-generated-views-with-an-edmx-file"></a>2.3.3 Cómo utilizar vistas pregeneradas con un archivo EDMX

También puede usar EDMGen para generar vistas para un archivo EDMX -el tema MSDN al que se hace referencia anteriormente describe cómo agregar un evento previo a la compilación para hacerlo-, pero esto es complicado y hay algunos casos en los que no es posible. Por lo general, es más fácil usar una plantilla T4 para generar las vistas cuando el modelo está en un archivo edmx.

El blog del equipo de ADO.NET tiene una entrada que describe \< https://docs.microsoft.com/archive/blogs/adonet/how-to-use-a-t4-template-for-view-generation>)cómo usar una plantilla T4 para la generación de vistas ( . Esta publicación incluye una plantilla que se puede descargar y agregar al proyecto. La plantilla se escribió para la primera versión de Entity Framework, por lo que no se garantiza que funcionen con las versiones más recientes de Entity Framework. Sin embargo, puede descargar un conjunto más actualizado de plantillas de generación de vistas para Entity Framework 4 y 5 desde la Galería de Visual Studio:

-   VB.NET:\<http://visualstudiogallery.msdn.microsoft.com/118b44f2-1b91-4de2-a584-7a680418941d>
-   C\#:\<http://visualstudiogallery.msdn.microsoft.com/ae7730ce-ddab-470f-8456-1b313cd2c44d>

Si usa Entity Framework 6, puede obtener las plantillas T4 de \< http://visualstudiogallery.msdn.microsoft.com/18a7db90-6705-4d19-9dd1-0a6c23d0751f>generación de vistas de la Galería de Visual Studio en .

### <a name="24-reducing-the-cost-of-view-generation"></a>2.4 Reducción del costo de generación de vistas

El uso de vistas generadas previamente mueve el coste de la generación de vistas desde la carga del modelo (tiempo de ejecución) hasta el tiempo de diseño. Aunque esto mejora el rendimiento de inicio en tiempo de ejecución, seguirá experimentando el dolor de la generación de vistas mientras se desarrolla. Hay varios trucos adicionales que pueden ayudar a reducir el costo de generación de vistas, tanto en tiempo de compilación como en tiempo de ejecución.

#### <a name="241-using-foreign-key-associations-to-reduce-view-generation-cost"></a>2.4.1 Uso de asociaciones de clave extranjera para reducir el costo de generación de vistas

Hemos visto una serie de casos en los que cambiar las asociaciones del modelo de Asociaciones Independientes a Asociaciones clave Extranjeras mejoró drásticamente el tiempo dedicado a la generación de vistas.

Para demostrar esta mejora, generamos dos versiones del modelo Navision mediante EDMGen. *Nota: consulte el apéndice C para obtener una descripción del modelo de Navision.* El modelo navision es interesante para este ejercicio debido a su gran cantidad de entidades y relaciones entre ellas.

Una versión de este modelo muy grande se generó con Foreign Keys Associations y la otra se generó con Asociaciones Independientes. A continuación, cronometramos el tiempo que se tardó en generar las vistas para cada modelo. La prueba de Entity Framework 5 utilizó el método GenerateViews() de la clase EntityViewGenerator para generar las vistas, mientras que la prueba de Entity Framework 6 utilizó el método GenerateViews() de la clase StorageMappingItemCollection. Esto debido a la reestructuración de código que se produjo en el código base de Entity Framework 6.

Con Entity Framework 5, la generación de vistas para el modelo con claves externas tardó 65 minutos en una máquina de laboratorio. Se desconoce cuánto tiempo habría tardado en generar las vistas para el modelo que usaba asociaciones independientes. Dejamos la prueba en ejecución durante más de un mes antes de que la máquina se reiniciara en nuestro laboratorio para instalar actualizaciones mensuales.

Con Entity Framework 6, la generación de vistas para el modelo con claves externas tardó 28 segundos en la misma máquina de laboratorio. La generación de vistas para el modelo que utiliza asociaciones independientes tardó 58 segundos. Las mejoras realizadas en Entity Framework 6 en su código de generación de vistas significan que muchos proyectos no necesitarán vistas generadas previamente para obtener tiempos de inicio más rápidos.

Es importante señalar que las vistas de pregeneración en Entity Framework 4 y 5 se pueden realizar con EDMGen o Entity Framework Power Tools. Para la generación de vistas de Entity Framework 6 se puede realizar a través de Entity Framework Power Tools o mediante programación como se describe en Vistas de [asignación generadas previamente](~/ef6/fundamentals/performance/pre-generated-views.md).

##### <a name="2411-how-to-use-foreign-keys-instead-of-independent-associations"></a>2.4.1.1 Cómo utilizar claves extranjeras en lugar de asociaciones independientes

Cuando se usa EDMGen o Entity Designer en Visual Studio, se obtienen fK de forma predeterminada y solo se necesita una sola casilla de verificación o marca de línea de comandos para cambiar entre FKs e IA.

Si tiene un modelo de Code First grande, el uso de asociaciones independientes tendrá el mismo efecto en la generación de vistas. Puede evitar este impacto mediante la inclusión de propiedades de clave externa en las clases de los objetos dependientes, aunque algunos desarrolladores considerarán que esto contamina su modelo de objetos. Puede encontrar más información sobre \< http://blog.oneunicorn.com/2011/12/11/whats-the-deal-with-mapping-foreign-keys-using-the-entity-framework/>este tema en .

| Al usar      | Haga esto                                                                                                                                                                                                                                                                                                                              |
|:----------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Diseñador de entidades | Después de agregar una asociación entre dos entidades, asegúrese de que tiene una restricción referencial. Las restricciones referenciales indican a Entity Framework que use claves externas en lugar de asociaciones independientes. Para más \< https://docs.microsoft.com/archive/blogs/efdesign/foreign-keys-in-the-entity-framework>detalles visite . |
| EDMGen          | Al utilizar EDMGen para generar los archivos de la base de datos, las claves externas se respetarán y se agregarán al modelo como tal. Para obtener más información sobre las diferentes [http://msdn.microsoft.com/library/bb387165.aspx](https://msdn.microsoft.com/library/bb387165.aspx)opciones expuestas por EDMGen visite .                           |
| Code First      | Consulte la sección "Convención de [relaciones"](~/ef6/modeling/code-first/conventions/built-in.md) del tema Convenciones de Code First para obtener información sobre cómo incluir propiedades de clave externa en objetos dependientes al usar Code First.                                                                                              |

#### <a name="242-moving-your-model-to-a-separate-assembly"></a>2.4.2 Mover el modelo a un ensamblaje separado

Cuando el modelo se incluye directamente en el proyecto de la aplicación y se generan vistas a través de un evento previo a la compilación o una plantilla T4, la generación y validación de vistas tendrá lugar siempre que se reconstruya el proyecto, incluso si el modelo no se ha cambiado. Si mueve el modelo a un ensamblado independiente y hace referencia a él desde el proyecto de la aplicación, puede realizar otros cambios en la aplicación sin necesidad de volver a generar el proyecto que contiene el modelo.

*Nota:*  al mover el modelo a ensamblados independientes, recuerde copiar las cadenas de conexión del modelo en el archivo de configuración de la aplicación del proyecto cliente.

#### <a name="243-disable-validation-of-an-edmx-based-model"></a>2.4.3 Deshabilitar la validación de un modelo basado en edmx

Los modelos EDMX se validan en tiempo de compilación, incluso si el modelo no cambia. Si el modelo ya se ha validado, puede suprimir la validación en tiempo de compilación estableciendo la propiedad "Validar en compilación" en false en la ventana de propiedades. Al cambiar la asignación o el modelo, puede volver a habilitar temporalmente la validación para comprobar los cambios.

Tenga en cuenta que las mejoras de rendimiento se realizaron en Entity Framework Designer para Entity Framework 6 y el costo de la "Validar en compilación" es mucho menor que en versiones anteriores del diseñador.

## <a name="3-caching-in-the-entity-framework"></a>3 Almacenamiento en caché en Entity Framework

Entity Framework tiene las siguientes formas de almacenamiento en caché integrado:

1.  Almacenamiento en caché de objetos: ObjectStateManager integrado en una instancia de ObjectContext realiza un seguimiento en la memoria de los objetos que se han recuperado con esa instancia. Esto también se conoce como caché de primer nivel.
2.  Almacenamiento en caché del plan de consulta: reutiliza el comando de almacén generado cuando se ejecuta una consulta más de una vez.
3.  Almacenamiento en caché de metadatos: comparte los metadatos de un modelo en distintas conexiones con el mismo modelo.

Además de las memorias caché que EF proporciona de fábrica, un tipo especial de ADO.NET proveedor de datos conocido como proveedor de ajuste también se puede usar para extender Entity Framework con una memoria caché para los resultados recuperados de la base de datos, también conocido como almacenamiento en caché de segundo nivel.

### <a name="31-object-caching"></a>3.1 Almacenamiento en caché de objetos

De forma predeterminada, cuando se devuelve una entidad en los resultados de una consulta, justo antes de que EF la materialice, ObjectContext comprobará si una entidad con la misma clave ya se ha cargado en su ObjectStateManager. Si una entidad con las mismas claves ya está presente EF la incluirá en los resultados de la consulta. Aunque EF seguirá emitiendo la consulta en la base de datos, este comportamiento puede omitir gran parte del costo de materializar la entidad varias veces.

#### <a name="311-getting-entities-from-the-object-cache-using-dbcontext-find"></a>3.1.1 Obtención de entidades de la caché de objetos mediante DbContext Find

A diferencia de una consulta normal, el método Find en DbSet (API incluidas por primera vez en EF 4.1) realizará una búsqueda en memoria antes incluso de emitir la consulta en la base de datos. Es importante tener en cuenta que dos instancias de ObjectContext diferentes tendrán dos instancias ObjectStateManager diferentes, lo que significa que tienen cachés de objetos independientes.

Buscar utiliza el valor de clave principal para intentar encontrar una entidad de la que el contexto realiza un seguimiento. Si la entidad no está en el contexto, se ejecutará y evaluará una consulta en la base de datos, y se devuelve null si la entidad no se encuentra en el contexto o en la base de datos. Tenga en cuenta que Find también devuelve entidades que se han agregado al contexto pero que aún no se han guardado en la base de datos.

Hay una consideración de rendimiento que se debe tener en cuenta al usar Buscar. Las invocaciones a este método de forma predeterminada desencadenarán una validación de la memoria caché de objetos para detectar los cambios que aún están pendientes de confirmación en la base de datos. Este proceso puede ser muy costoso si hay un gran número de objetos en la caché de objetos o en un gráfico de objetos grande que se agrega a la caché de objetos, pero también se puede deshabilitar. En algunos casos, puede percibir más de un orden de magnitud de diferencia al llamar al método Find cuando deshabilita los cambios de detección automática. Sin embargo, se percibe un segundo orden de magnitud cuando el objeto está realmente en la memoria caché frente a cuando el objeto tiene que recuperarse de la base de datos. Aquí está un gráfico de ejemplo con mediciones tomadas usando algunos de nuestros microbenchmarks, expresados en milisegundos, con una carga de 5000 entidades:

![Escala logarítmica de .NET 4.5](~/ef6/media/net45logscale.png ".NET 4.5 - escala logarítmica")

Ejemplo de Buscar con detección automática de cambios deshabilitados:

``` csharp
    context.Configuration.AutoDetectChangesEnabled = false;
    var product = context.Products.Find(productId);
    context.Configuration.AutoDetectChangesEnabled = true;
    ...
```

Lo que debe tener en cuenta al utilizar el método Find es:

1.  Si el objeto no está en la memoria caché, se anulan las ventajas de Buscar, pero la sintaxis sigue siendo más sencilla que una consulta por clave.
2.  Si la detección automática de cambios está habilitada, el costo del método Find puede aumentar en un orden de magnitud, o incluso más en función de la complejidad del modelo y la cantidad de entidades de la caché de objetos.

Además, tenga en cuenta que Buscar solo devuelve la entidad que está buscando y no carga automáticamente sus entidades asociadas si aún no están en la caché de objetos. Si necesita recuperar entidades asociadas, puede usar una consulta por clave con carga diligente. Para obtener más información, consulte **8.1 Lazy Loading vs.**

#### <a name="312-performance-issues-when-the-object-cache-has-many-entities"></a>3.1.2 Problemas de rendimiento cuando la caché de objetos tiene muchas entidades

La caché de objetos ayuda a aumentar la capacidad de respuesta general de Entity Framework. Sin embargo, cuando la caché de objetos tiene una gran cantidad de entidades cargadas, puede afectar a ciertas operaciones como Agregar, Quitar, Buscar, Entrada, SaveChanges y más. En particular, las operaciones que desencadenan una llamada a DetectChanges se verán afectadas negativamente por cachés de objetos muy grandes. DetectChanges sincroniza el gráfico de objetos con el administrador de estado de objetos y su rendimiento determinará directamente por el tamaño del gráfico de objetos. Para obtener más información acerca de DetectChanges, vea Seguimiento de [cambios en entidades POCO](https://msdn.microsoft.com/library/dd456848.aspx).

Al usar Entity Framework 6, los desarrolladores pueden llamar a AddRange y RemoveRange directamente en un DbSet, en lugar de iterar en una colección y llamar a Add una vez por instancia. La ventaja de usar los métodos de rango es que el costo de DetectChanges solo se paga una vez para todo el conjunto de entidades en lugar de una vez por cada entidad agregada.

### <a name="32-query-plan-caching"></a>3.2 Almacenamiento en caché del plan de consulta

La primera vez que se ejecuta una consulta, pasa por el compilador de plan interno para traducir la consulta conceptual en el comando store (por ejemplo, el T-SQL que se ejecuta cuando se ejecuta en SQL Server).Si el almacenamiento en caché del plan de consulta está habilitado, la próxima vez que se ejecute la consulta, el comando store se recupera directamente de la caché del plan de consulta para su ejecución, omitiendo el compilador del plan.

La memoria caché del plan de consulta se comparte entre instancias de ObjectContext dentro del mismo AppDomain. No es necesario retener una instancia de ObjectContext para beneficiarse del almacenamiento en caché del plan de consulta.

#### <a name="321-some-notes-about-query-plan-caching"></a>3.2.1 Algunas notas sobre el almacenamiento en caché del plan de consulta

-   La memoria caché del plan de consulta se comparte para todos los tipos de consulta: objetos Entity SQL, LINQ to Entities y CompiledQuery.
-   De forma predeterminada, el almacenamiento en caché del plan de consulta está habilitado para las consultas de Entity SQLEntity SQL, ya sea que se ejecuten a través de EntityCommand o a través de ObjectQuery. También está habilitado de forma predeterminada para las consultas LINQ to Entities en Entity Framework en .NET 4.5 y en Entity Framework 6
    -   El almacenamiento en caché del plan de consulta se puede deshabilitar estableciendo la propiedad EnablePlanCaching (en EntityCommand u ObjectQuery) en false. Por ejemplo:
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
-   Para las consultas parametrizadas, cambiar el valor del parámetro seguirá teniendo lugar a la consulta almacenada en caché. Pero cambiar las facetas de un parámetro (por ejemplo, tamaño, precisión o escala) golpeará una entrada diferente en la memoria caché.
-   Cuando se usa Entity SQLEntity SQL, la cadena de consulta forma parte de la clave. Cambiar la consulta en absoluto dará lugar a diferentes entradas de caché, incluso si las consultas son funcionalmente equivalentes. Esto incluye cambios en mayúsculas y minúsculas o espacios en blanco.
-   Cuando se usa LINQ, la consulta se procesa para generar una parte de la clave. Por lo tanto, cambiar la expresión LINQ generará una clave diferente.
-   Pueden aplicarse otras limitaciones técnicas; consulte Consultas autocompiladas para obtener más detalles.

#### <a name="322-cache-eviction-algorithm"></a>3.2.2 Algoritmo de desalojo de caché

Comprender cómo funciona el algoritmo interno le ayudará a averiguar cuándo habilitar o deshabilitar el almacenamiento en caché del plan de consulta. El algoritmo de limpieza es el siguiente:

1.  Una vez que la memoria caché contiene un número establecido de entradas (800), iniciamos un temporizador que barre periódicamente (una vez por minuto) la memoria caché.
2.  Durante los barridos de caché, las entradas se eliminan de la memoria caché en una base LFRU (menos frecuentemente – utilizado recientemente). Este algoritmo tiene en cuenta tanto el recuento de aciertos como la edad al decidir qué entradas se expulsan.
3.  Al final de cada barrido de caché, la memoria caché contiene de nuevo 800 entradas.

Todas las entradas de caché se tratan por igual al determinar qué entradas se van a desalojar. Esto significa que el comando store para un CompiledQuery tiene la misma probabilidad de desalojo que el comando store para una consulta de Entity SQLEntity SQL.

Tenga en cuenta que el temporizador de desalojo de caché se activa cuando hay 800 entidades en la memoria caché, pero la memoria caché solo se barre 60 segundos después de que se inicie este temporizador. Eso significa que hasta 60 segundos su caché puede crecer hasta ser bastante grande.

#### <a name="323-test-metrics-demonstrating-query-plan-caching-performance"></a>3.2.3 Métricas de prueba que demuestran el rendimiento del almacenamiento en caché del plan de consulta

Para demostrar el efecto del almacenamiento en caché del plan de consulta en el rendimiento de la aplicación, realizamos una prueba en la que ejecutamos varias consultas de Entity SQLEntity SQL en el modelo de Navision. Consulte el apéndice para obtener una descripción del modelo de Navision y los tipos de consultas que se ejecutaron. En esta prueba, primero recorremos en iteración la lista de consultas y ejecutamos cada una para agregarlas a la memoria caché (si el almacenamiento en caché está habilitado). Este paso no tiene tiempo. A continuación, dormimos el hilo principal durante más de 60 segundos para permitir que se lleve a cabo el barrido de caché; finalmente, recorremos en iteración la lista una segunda vez para ejecutar las consultas almacenadas en caché. Además, la caché del plan de SQL ServerSQL Server se vacía antes de que se ejecute cada conjunto de consultas para que las horas que obtenemos reflejen con precisión la ventaja proporcionada por la caché del plan de consulta.

##### <a name="3231-test-results"></a>3.2.3.1 Resultados de las pruebas

| Prueba                                                                   | EF5 sin caché | EF5 almacenado en caché | EF6 sin caché | EF6 almacenado en caché |
|:-----------------------------------------------------------------------|:-------------|:-----------|:-------------|:-----------|
| Enumeración de todas las consultas 18723                                          | 124          | 125.4      | 124.3        | 125.3      |
| Evitar el barrido (solo las primeras 800 consultas, independientemente de la complejidad)  | 41.7         | 5.5        | 40,5         | 5.4        |
| Sólo las consultas AggregatingSubtotals (178 en total , lo que evita el barrido) | 39.5         | 4.5.        | 38.1         | 4.6        |

*Todo el tiempo en segundos.*

Moral: al ejecutar muchas consultas distintas (por ejemplo, consultas creadas dinámicamente), el almacenamiento en caché no ayuda y el vaciado resultante de la memoria caché puede mantener las consultas que más se beneficiarían del almacenamiento en caché del plan de usarlo realmente.

Las consultas AggregatingSubtotals son las consultas más complejas con las que hemos probado. Como era de esperar, cuanto más compleja sea la consulta, más ventaja verá del almacenamiento en caché del plan de consulta.

Dado que un CompiledQuery es realmente una consulta LINQ con su plan almacenado en caché, la comparación de un CompiledQuery en comparación con la consulta de Entity SQLEntity SQL equivalente debe tener resultados similares. De hecho, si una aplicación tiene muchas consultas de Entity SQLEntity SQL dinámicas, rellenar la memoria caché con consultas también hará que CompiledQueries "descompile" cuando se vacíen de la memoria caché. En este escenario, el rendimiento se puede mejorar deshabilitando el almacenamiento en caché en las consultas dinámicas para priorizar las consultas compiladas. Mejor aún, por supuesto, sería reescribir la aplicación para usar consultas parametrizadas en lugar de consultas dinámicas.

### <a name="33-using-compiledquery-to-improve-performance-with-linq-queries"></a>3.3 Uso de CompiledQuery para mejorar el rendimiento con consultas LINQ

Nuestras pruebas indican que el uso de CompiledQuery puede traer una ventaja del 7% sobre las consultas LINQ compiladas automáticamente; esto significa que pasará un 7% menos de tiempo ejecutando código desde la pila de Entity Framework; no significa que su aplicación será un 7% más rápida. En términos generales, el costo de escribir y mantener CompiledQuery objetos en EF 5.0 puede no valer la pena el problema en comparación con las ventajas. Su kilometraje puede variar, así que ejerza esta opción si su proyecto requiere el empuje adicional. Tenga en cuenta que CompiledQueries solo es compatible con modelos derivados de ObjectContext y no es compatible con modelos derivados de DbContext.

Para obtener más información sobre cómo crear e invocar un CompiledQuery, vea [Consultas compiladas (LINQ to Entities)](https://msdn.microsoft.com/library/bb896297.aspx).

Hay dos consideraciones que debe tener al usar un CompiledQuery, a saber, el requisito de usar instancias estáticas y los problemas que tienen con la composición. A continuación se explica en profundidad estas dos consideraciones.

#### <a name="331-use-static-compiledquery-instances"></a>3.3.1 Usar instancias estáticas de CompiledQuery

Dado que compilar una consulta LINQ es un proceso que consume mucho tiempo, no queremos hacerlo cada vez que necesitemos capturar datos de la base de datos. Las instancias de CompiledQuery le permiten compilar una vez y ejecutarvarias veces, pero debe tener cuidado y adquirir para volver a usar la misma instancia de CompiledQuery cada vez en lugar de compilarla una y otra vez. El uso de miembros estáticos para almacenar las instancias de CompiledQuery se convierte en necesario; de lo contrario no verá ningún beneficio.

Por ejemplo, supongamos que la página tiene el siguiente cuerpo del método para controlar la visualización de los productos de la categoría seleccionada:

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

En este caso, creará una nueva instancia de CompiledQuery sobre la marcha cada vez que se llame al método. En lugar de ver las ventajas de rendimiento recuperando el comando de almacén de la caché del plan de consulta, CompiledQuery pasará por el compilador del plan cada vez que se cree una nueva instancia. De hecho, va a contaminar la caché del plan de consulta con una nueva entrada CompiledQuery cada vez que se llame al método.

En su lugar, desea crear una instancia estática de la consulta compilada, por lo que está invocando la misma consulta compilada cada vez que se llama al método. Una forma de hacerlo es agregando la instancia de CompiledQuery como miembro del contexto del objeto.A continuación, puede hacer las cosas un poco más limpias accediendo a CompiledQuery a través de un método auxiliar:

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

#### <a name="332-composing-over-a-compiledquery"></a>3.3.2 Composición a través de una Consulta Compilada

La capacidad de componer sobre cualquier consulta LINQ es extremadamente útil; para ello, simplemente se invoca un método después de IQueryable como *Skip()* o *Count()*. Sin embargo, al hacerlo esencialmente devuelve un nuevo objeto IQueryable. Aunque no hay nada que le impida técnicamente componer a través de un CompiledQuery, al hacerlo se producirá la generación de un nuevo objeto IQueryable que requiere pasar el compilador de plan de nuevo.

Algunos componentes harán uso de objetos IQueryable compuestos para habilitar la funcionalidad avanzada. Por ejemplo, ASP. GridView de NET se puede enlazar a datos a un IQueryable objeto a través de la SelectMethod propiedad. El control GridView, a continuación, componer sobre este IQueryable objeto para permitir la ordenación y paginación sobre el modelo de datos. Como puede ver, el uso de un CompiledQuery para el control GridView no golpearía la consulta compilada, pero generaría una nueva consulta autocompilada.

Un lugar donde puede ejecutarse en esto es al agregar filtros progresivos a una consulta. Por ejemplo, supongamos que tenía una página Customers con varias listas desplegables para filtros opcionales (por ejemplo, Country y OrdersCount). Puede componer estos filtros sobre los resultados de IQueryable de un CompiledQuery, pero al hacerlo se producirá la nueva consulta que pasa por el compilador de plan cada vez que se ejecuta.

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

 Una compensación aquí es el comando store generado siempre tendrá los filtros con las comprobaciones nulas, pero estos deben ser bastante simples para el servidor de bases de datos para optimizar:

``` SQL
...
WHERE ((0 = (CASE WHEN (@p__linq__1 IS NOT NULL) THEN cast(1 as bit) WHEN (@p__linq__1 IS NULL) THEN cast(0 as bit) END)) OR ([Project3].[C2] > @p__linq__2)) AND (@p__linq__3 IS NULL OR [Project3].[Country] = @p__linq__4)
```

### <a name="34-metadata-caching"></a>3.4 Almacenamiento en caché de metadatos

Entity Framework también admite el almacenamiento en caché de metadatos. Esto es esencialmente el almacenamiento en caché de la información de tipo y la información de asignación de tipo a base de datos a través de diferentes conexiones al mismo modelo. La caché de metadatos es única por AppDomain.

#### <a name="341-metadata-caching-algorithm"></a>3.4.1 Algoritmo de almacenamiento en caché de metadatos

1.  La información de metadatos de un modelo se almacena en un ItemCollection para cada EntityConnection.
    -   Como nota secundaria, hay diferentes ItemCollection objetos para diferentes partes del modelo. Por ejemplo, StoreItemCollections contiene la información sobre el modelo de base de datos; ObjectItemCollection contiene información sobre el modelo de datos; EdmItemCollection contiene información sobre el modelo conceptual.

2.  Si dos conexiones usan la misma cadena de conexión, compartirán la misma instancia de ItemCollection.
3.  Las cadenas de conexión funcionalmente equivalentes pero textualmente diferentes pueden dar lugar a diferentes cachés de metadatos. Hacemos tokenize cadenas de conexión, por lo que simplemente cambiar el orden de los tokens debe dar lugar a metadatos compartidos. Pero dos cadenas de conexión que parecen funcionalmente iguales no se pueden evaluar como idénticas después de la tokenización.
4.  El ItemCollection se comprueba periódicamente para su uso. Si se determina que no se ha tenido acceso a un espacio de trabajo recientemente, se marcará para su limpieza en el siguiente barrido de caché.
5.  La mera creación de EntityConnection hará que se cree una caché de metadatos (aunque las colecciones de elementos en ella no se inicializarán hasta que se abra la conexión). Este espacio de trabajo permanecerá en memoria hasta que el algoritmo de almacenamiento en caché determine que no está "en uso".

El equipo de asesoramiento al cliente ha escrito una entrada de blog que describe la celebración \< https://docs.microsoft.com/archive/blogs/appfabriccat/holding-a-reference-to-the-ef-metadataworkspace-for-wcf-services>de una referencia a un ItemCollection con el fin de evitar la "desuso" cuando se utilizan modelos grandes: .

#### <a name="342-the-relationship-between-metadata-caching-and-query-plan-caching"></a>3.4.2 La relación entre el almacenamiento en caché de metadatos y el almacenamiento en caché del plan de consulta

La instancia de caché del plan de consulta se encuentra en el MetadataWorkspace ItemCollection de tipos de almacén. Esto significa que los comandos de almacén almacenados en caché se usarán para consultas en cualquier contexto que se cree una instancia mediante un MetadataWorkspace determinado. También significa que si tiene dos cadenas de conexiones que son ligeramente diferentes y no coinciden después de la tokenización, tendrá diferentes instancias de caché del plan de consulta.

### <a name="35-results-caching"></a>3.5 Almacenamiento en caché de resultados

Con el almacenamiento en caché de resultados (también conocido como "almacenamiento en caché de segundo nivel"), se mantienen los resultados de las consultas en una memoria caché local. Al emitir una consulta, primero verá si los resultados están disponibles localmente antes de consultar en el almacén. Aunque Entity Framework no admite directamente el almacenamiento en caché de resultados, es posible agregar una caché de segundo nivel mediante un proveedor de encapsulación. Un proveedor de ajuste de ejemplo con una caché de segundo nivel es Entity Framework Second Level Cache de Alachisoft [basado en NCache](https://www.alachisoft.com/ncache/entity-framework.html).

Esta implementación de almacenamiento en caché de segundo nivel es una funcionalidad inyectada que tiene lugar después de que la expresión LINQ se haya evaluado (y funcletized) y el plan de ejecución de consultas se calcule o recupere de la memoria caché de primer nivel. A continuación, la memoria caché de segundo nivel almacenará solo los resultados de la base de datos sin procesar, por lo que la canalización de materialización se sigue ejecutando después.

#### <a name="351-additional-references-for-results-caching-with-the-wrapping-provider"></a>3.5.1 Referencias adicionales para el almacenamiento en caché de resultados con el proveedor de ajuste

-   Julie Lerman ha escrito un artículo MSDN "Almacenamiento en caché de segundo nivel en Entity Framework y Windows Azure" que incluye cómo actualizar el proveedor de encapsulado de ejemplo para usar el almacenamiento en caché de AppFabric de Windows Server:[https://msdn.microsoft.com/magazine/hh394143.aspx](https://msdn.microsoft.com/magazine/hh394143.aspx)
-   Si está trabajando con Entity Framework 5, el blog del equipo tiene una entrada que describe \< https://docs.microsoft.com/archive/blogs/adonet/ef-caching-with-jarek-kowalskis-provider>cómo ejecutar las cosas con el proveedor de almacenamiento en caché para Entity Framework 5: . También incluye una plantilla T4 para ayudar a automatizar la adición del almacenamiento en caché de 2o nivel al proyecto.

## <a name="4-autocompiled-queries"></a>4 Consultas autocompiladas

Cuando se emite una consulta en una base de datos mediante Entity Framework, debe pasar por una serie de pasos antes de materializar realmente los resultados; uno de estos pasos es la compilación de consultas. Se sabía que las consultas de Entity SQLEntity SQL tenían un buen rendimiento, ya que se almacenan automáticamente en caché, por lo que la segunda o tercera vez que ejecuta la misma consulta puede omitir el compilador del plan y usar el plan almacenado en caché en su lugar.

Entity Framework 5 introdujo el almacenamiento en caché automático para las consultas LINQ to Entities también. En ediciones anteriores de Entity Framework crear un CompiledQuery para acelerar el rendimiento era una práctica común, ya que esto haría que la consulta LINQ to Entities se pueda almacenar en caché. Dado que el almacenamiento en caché ahora se realiza automáticamente sin el uso de un CompiledQuery, llamamos a esta característica "consultas compiladas automáticamente". Para obtener más información acerca de la caché del plan de consulta y su mecánica, consulte Almacenamiento en caché del plan de consulta.

Entity Framework detecta cuándo se requiere volver a compilar una consulta y lo hace cuando se invoca la consulta incluso si se ha compilado antes. Las condiciones comunes que hacen que la consulta se vuelva a compilar son:

-   Cambiar el MergeOption asociado a la consulta. La consulta almacenada en caché no se usará, en su lugar, el compilador de plan se ejecutará de nuevo y el plan recién creado se almacenará en caché.
-   Cambiar el valor de ContextOptions.UseCSharpNullComparisonBehavior. Obtiene el mismo efecto que cambiar mergeOption.

Otras condiciones pueden impedir que la consulta use la memoria caché. Los ejemplos comunes son:

-   Uso de&lt;IEnumerable T&gt;. Contiene&lt;&gt;(valor T).
-   Uso de funciones que generan consultas con constantes.
-   Usar las propiedades de un objeto no asignado.
-   Vincular la consulta a otra consulta que requiere volver a compilarse.

### <a name="41-using-ienumerablelttgtcontainslttgtt-value"></a>4.1 Uso de&lt;&gt;IEnumerable T . Contiene&lt;&gt;T (valor T)

Entity Framework no almacena en caché&lt;las&gt;consultas que invocan IEnumerable T . Contiene&lt;&gt;T (valor T) en una colección en memoria, ya que los valores de la colección se consideran volátiles. La siguiente consulta de ejemplo no se almacenará en caché, por lo que siempre la procesará el compilador del plan:

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

Tenga en cuenta que el tamaño de la IEnumerable en el que se ejecuta Contains determina la rapidez o la velocidad con la que se compila la consulta. El rendimiento puede verse afectado significativamente cuando se utilizan colecciones grandes como la que se muestra en el ejemplo anterior.

Entity Framework 6 contiene optimizaciones en&lt;&gt;la forma en que IEnumerable T . Contiene&lt;&gt;T (valor T) funciona cuando se ejecutan consultas. El código SQL que se genera es mucho más rápido de producir y más legible, y en la mayoría de los casos también se ejecuta más rápido en el servidor.

### <a name="42-using-functions-that-produce-queries-with-constants"></a>4.2 Uso de funciones que producen consultas con constantes

Los operadores LINQ Skip(), Take(), Contains() y DefautIfEmpty() no generan consultas SQL con parámetros, sino que colocan los valores que se les pasan como constantes. Por este caso, las consultas que, de lo contrario, podrían ser idénticas terminan contaminando la caché del plan de consulta, tanto en la pila de EF como en el servidor de base de datos, y no se reutilizan a menos que se usen las mismas constantes en una ejecución de consulta posterior. Por ejemplo:

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

En este ejemplo, cada vez que se ejecuta esta consulta con un valor diferente para id la consulta se compilará en un nuevo plan.

En particular, preste atención al uso de Skip y Take al realizar la paginación. En EF6, estos métodos tienen una sobrecarga lambda que hace que el plan de consulta almacenado en caché sea reutilizable porque EF puede capturar las variables pasadas a estos métodos y traducirlas a Parámetros SQL. Esto también ayuda a mantener la memoria caché más limpia, ya que de lo contrario cada consulta con una constante diferente para Omitir y Tomar recibiría su propia entrada de caché del plan de consulta.

Considere el código siguiente, que es subóptimo, pero solo está destinado a ejemplificar esta clase de consultas:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

Una versión más rápida de este mismo código implicaría llamar a Skip con una lambda:

``` csharp
var customers = context.Customers.OrderBy(c => c.LastName);
for (var i = 0; i < count; ++i)
{
    var currentCustomer = customers.Skip(() => i).FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

El segundo fragmento de código puede ejecutarse hasta un 11 % más rápido porque se usa el mismo plan de consulta cada vez que se ejecuta la consulta, lo que ahorra tiempo de CPU y evita contaminar la caché de consultas. Además, debido a que el parámetro Skip está en un cierre, el código también podría tener este aspecto ahora:

``` csharp
var i = 0;
var skippyCustomers = context.Customers.OrderBy(c => c.LastName).Skip(() => i);
for (; i < count; ++i)
{
    var currentCustomer = skippyCustomers.FirstOrDefault();
    ProcessCustomer(currentCustomer);
}
```

### <a name="43-using-the-properties-of-a-non-mapped-object"></a>4.3 Uso de las propiedades de un objeto no asignado

Cuando una consulta utiliza las propiedades de un tipo de objeto no asignado como parámetro, la consulta no se almacenará en caché. Por ejemplo:

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

En este ejemplo, supongamos que la clase NonMappedType no forma parte del modelo Entity. Esta consulta se puede cambiar fácilmente para no utilizar un tipo no asignado y en su lugar utilizar una variable local como parámetro para la consulta:

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

En este caso, la consulta podrá almacenarse en caché y se beneficiará de la caché del plan de consulta.

### <a name="44-linking-to-queries-that-require-recompiling"></a>4.4 Vinculación a consultas que requieren recompilación

Siguiendo el mismo ejemplo que el anterior, si tiene una segunda consulta que se basa en una consulta que debe volver a compilarse, también se volverá a compilar toda la segunda consulta. Este es un ejemplo para ilustrar este escenario:

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

El ejemplo es genérico, pero ilustra cómo la vinculación a firstQuery está provocando que secondQuery no se pueda almacenar en caché. Si firstQuery no hubiera sido una consulta que requiere volver a compilar, secondQuery se habría almacenado en caché.

## <a name="5-notracking-queries"></a>5 Consultas de NoTracking

### <a name="51-disabling-change-tracking-to-reduce-state-management-overhead"></a>5.1 Deshabilitar el seguimiento de cambios para reducir la sobrecarga de administración del estado

Si se encuentra en un escenario de solo lectura y desea evitar la sobrecarga de cargar los objetos en ObjectStateManager, puede emitir consultas "Sin seguimiento".El seguimiento de cambios se puede deshabilitar en el nivel de consulta.

Sin embargo, tenga en cuenta que al deshabilitar el seguimiento de cambios, está desactivando eficazmente la caché de objetos. Al consultar una entidad, no podemos omitir la materialización extrayendo los resultados de la consulta materializados previamente de ObjectStateManager. Si está consultando repetidamente las mismas entidades en el mismo contexto, es posible que vea un beneficio de rendimiento al habilitar el seguimiento de cambios.

Al realizar consultas mediante ObjectContext, las instancias de ObjectQuery y ObjectSet recordarán un MergeOption una vez que se establece, y las consultas que se componen en ellas heredarán la MergeOption efectiva de la consulta primaria. Cuando se utiliza DbContext, el seguimiento se puede deshabilitar llamando al modificador AsNoTracking() en DbSet.

#### <a name="511-disabling-change-tracking-for-a-query-when-using-dbcontext"></a>5.1.1 Deshabilitar el seguimiento de cambios para una consulta al usar DbContext

Puede cambiar el modo de una consulta a NoTracking encadenando una llamada al método AsNoTracking() de la consulta. A diferencia de ObjectQuery, las clases DbSet y DbQuery de la API DbContext no tienen una propiedad mutable para MergeOption.

``` csharp
    var productsForCategory = from p in context.Products.AsNoTracking()
                                where p.Category.CategoryName == selectedCategory
                                select p;


```

#### <a name="512-disabling-change-tracking-at-the-query-level-using-objectcontext"></a>5.1.2 Deshabilitar el seguimiento de cambios en el nivel de consulta mediante ObjectContext

``` csharp
    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;

    ((ObjectQuery)productsForCategory).MergeOption = MergeOption.NoTracking;
```

#### <a name="513-disabling-change-tracking-for-an-entire-entity-set-using-objectcontext"></a>5.1.3 Deshabilitar el seguimiento de cambios para todo un conjunto de entidades mediante ObjectContext

``` csharp
    context.Products.MergeOption = MergeOption.NoTracking;

    var productsForCategory = from p in context.Products
                                where p.Category.CategoryName == selectedCategory
                                select p;
```

### <a name="52test-metrics-demonstrating-the-performance-benefit-of-notracking-queries"></a>5.2 Métricas de prueba que demuestran el beneficio de rendimiento de las consultas NoTracking

En esta prueba analizamos el costo de rellenar ObjectStateManager comparando las consultas Tracking con NoTracking para el modelo de Navision. Consulte el apéndice para obtener una descripción del modelo de Navision y los tipos de consultas que se ejecutaron. En esta prueba, recorremos en iteración la lista de consultas y ejecutamos cada una una vez. Ejecutamos dos variaciones de la prueba, una con consultas NoTracking y otra con la opción de combinación predeterminada de "AppendOnly". Ejecutamos cada variación 3 veces y tomamos el valor medio de las corridas. Entre las pruebas borramos la caché de consultas en SQL Server y reducimos la tempdb ejecutando los siguientes comandos:

1.  DBCC DROPCLEANBUFFERS
2.  DBCC FREEPROCCACHE
3.  DBCC SHRINKDATABASE (tempdb, 0)

Resultados de la prueba, mediana en 3 corridas:

|                        | SIN SEGUIMIENTO – CONJUNTO DE TRABAJO | SIN SEGUIMIENTO – TIEMPO | APPEND ONLY – CONJUNTO DE TRABAJO | APPEND SOLAMENTE – TIEMPO |
|:-----------------------|:--------------------------|:-------------------|:--------------------------|:-------------------|
| **Entity Framework 5** | 460361728                 | 1163536 ms         | 596545536                 | 1273042 ms         |
| **Entity Framework 6** | 647127040                 | 190228 ms          | 832798720                 | 195521 ms          |

Entity Framework 5 tendrá una huella de memoria más pequeña al final de la ejecución que Entity Framework 6. La memoria adicional consumida por Entity Framework 6 es el resultado de estructuras de memoria adicionales y código que permiten nuevas características y un mejor rendimiento.

También hay una clara diferencia en la huella de memoria cuando se utiliza el ObjectStateManager. Entity Framework 5 aumentó su huella en un 30% al realizar un seguimiento de todas las entidades que hemos materializado desde la base de datos. Entity Framework 6 aumentó su huella en un 28% al hacerlo.

En términos de tiempo, Entity Framework 6 supera a Entity Framework 5 en esta prueba por un amplio margen. Entity Framework 6 completó la prueba en aproximadamente el 16% del tiempo consumido por Entity Framework 5. Además, Entity Framework 5 tarda un 9% más en completarse cuando se usa ObjectStateManager. En comparación, Entity Framework 6 usa un 3% más de tiempo al usar ObjectStateManager.

## <a name="6-query-execution-options"></a>6 Opciones de ejecución de consultas

Entity Framework ofrece varias formas diferentes de realizar consultas. Echaremos un vistazo a las siguientes opciones, compararemos los pros y los contras de cada una de ellas y examinaremos sus características de rendimiento:

-   LINQ to Entities.
-   Sin seguimiento LINQ to Entities.
-   Entity SQLEntity SQL sobre un ObjectQuery.
-   Entity SQLEntity SQL sobre EntityCommand.
-   ExecuteStoreQuery.
-   SqlQuery.
-   CompiledQuery.

### <a name="61-linq-to-entities-queries"></a>6.1 Consultas LINQ to Entities

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

**Ventajas**

-   Adecuado para operaciones CUD.
-   Objetos totalmente materializados.
-   Más sencillo escribir con sintaxis integrada en el lenguaje de programación.
-   Buen rendimiento.

**Desventajas**

-   Ciertas restricciones técnicas, tales como:
    -   Los patrones que usan DefaultIfEmpty para consultas OUTER JOIN dan como resultado consultas más complejas que simples instrucciones OUTER JOIN en Entity SQLEntity SQL.
    -   Todavía no puede usar LIKE con la coincidencia general de patrones.

### <a name="62-no-tracking-linq-to-entities-queries"></a>6.2 Consultas sin seguimiento de LINQ to Entities

Cuando el contexto deriva ObjectContext:

``` csharp
context.Products.MergeOption = MergeOption.NoTracking;
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
```

Cuando el contexto deriva DbContext:

``` csharp
var q = context.Products.AsNoTracking()
                        .Where(p => p.Category.CategoryName == "Beverages");
```

**Ventajas**

-   Rendimiento mejorado con las consultas LINQ normales.
-   Objetos totalmente materializados.
-   Más sencillo escribir con sintaxis integrada en el lenguaje de programación.

**Desventajas**

-   No es adecuado para operaciones CUD.
-   Ciertas restricciones técnicas, tales como:
    -   Los patrones que usan DefaultIfEmpty para consultas OUTER JOIN dan como resultado consultas más complejas que simples instrucciones OUTER JOIN en Entity SQLEntity SQL.
    -   Todavía no puede usar LIKE con la coincidencia general de patrones.

Tenga en cuenta que no se realiza un seguimiento de las consultas que proyectan propiedades escalares aunque no se especifique NoTracking. Por ejemplo:

``` csharp
var q = context.Products.Where(p => p.Category.CategoryName == "Beverages").Select(p => new { p.ProductName });
```

Esta consulta en particular no especifica explícitamente ser NoTracking, pero como no está materializando un tipo conocido por el administrador de estado de objetos, no se realiza un seguimiento del resultado materializado.

### <a name="63-entity-sql-over-an-objectquery"></a>6.3 Entity SQL sobre ObjectQuery

``` csharp
ObjectQuery<Product> products = context.Products.Where("it.Category.CategoryName = 'Beverages'");
```

**Ventajas**

-   Adecuado para operaciones CUD.
-   Objetos totalmente materializados.
-   Admite el almacenamiento en caché del plan de consulta.

**Desventajas**

-   Implica cadenas de consulta textuales que son más propensas a errores de usuario que construcciones de consulta integradas en el lenguaje.

### <a name="64-entity-sql-over-an-entity-command"></a>6.4 Entity SQL sobre un comando de entidad

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

-   Admite el almacenamiento en caché del plan de consulta en .NET 4.0 (el almacenamiento en caché del plan es compatible con todos los demás tipos de consulta de .NET 4.5).

**Desventajas**

-   Implica cadenas de consulta textuales que son más propensas a errores de usuario que construcciones de consulta integradas en el lenguaje.
-   No es adecuado para operaciones CUD.
-   Los resultados no se materializan automáticamente y deben leerse desde el lector de datos.

### <a name="65-sqlquery-and-executestorequery"></a>6.5 SqlQuery y ExecuteStoreQuery

SqlQuery en base de datos:

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

-   Por lo general, se omite el rendimiento más rápido, ya que se omite el compilador de planes.
-   Objetos totalmente materializados.
-   Adecuado para operaciones CUD cuando se utiliza desde el DbSet.

**Desventajas**

-   La consulta es textual y propensa a errores.
-   La consulta está vinculada a un back-end específico mediante la semántica de almacén en lugar de la semántica conceptual.
-   Cuando la herencia está presente, la consulta manual debe tener en cuenta las condiciones de asignación para el tipo solicitado.

### <a name="66-compiledquery"></a>6.6 CompiledQuery

``` csharp
private static readonly Func<NorthwindEntities, string, IQueryable<Product>> productsForCategoryCQ = CompiledQuery.Compile(
    (NorthwindEntities context, string categoryName) =>
        context.Products.Where(p => p.Category.CategoryName == categoryName)
        );
…
var q = context.InvokeProductsForCategoryCQ("Beverages");
```

**Ventajas**

-   Proporciona hasta un 7% de mejora del rendimiento con respecto a las consultas LINQ normales.
-   Objetos totalmente materializados.
-   Adecuado para operaciones CUD.

**Desventajas**

-   Mayor complejidad y sobrecarga de programación.
-   La mejora del rendimiento se pierde al componer encima de una consulta compilada.
-   Algunas consultas LINQ no se pueden escribir como CompiledQuery, por ejemplo, proyecciones de tipos anónimos.

### <a name="67-performance-comparison-of-different-query-options"></a>6.7 Comparación de rendimiento de diferentes opciones de consulta

Se pusieron a prueba micropuntos simples en los que no se ha cronometrado la creación del contexto. Medimos la consulta 5000 veces para un conjunto de entidades no almacenadas en caché en un entorno controlado. Estos números deben tomarse con una advertencia: no reflejan los números reales producidos por una aplicación, sino que son una medida muy precisa de la cantidad de una diferencia de rendimiento que hay cuando se comparan diferentes opciones de consulta de manzanas a manzanas, excluyendo el costo de crear un nuevo contexto.

| EF  | Prueba                                 | Tiempo (ms) | Memoria   |
|:----|:-------------------------------------|:----------|:---------|
| EF5 | ObjectContext ESQL                   | 2414      | 38801408 |
| EF5 | ObjectContext Linq Query             | 2692      | 38277120 |
| EF5 | DbContext Linq Query No Tracking     | 2818      | 41840640 |
| EF5 | Consulta DbContext Linq                 | 2930      | 41771008 |
| EF5 | ObjectContext Linq Query No Tracking | 3013      | 38412288 |
|     |                                      |           |          |
| EF6 | ObjectContext ESQL                   | 2059      | 46039040 |
| EF6 | ObjectContext Linq Query             | 3074      | 45248512 |
| EF6 | DbContext Linq Query No Tracking     | 3125      | 47575040 |
| EF6 | Consulta DbContext Linq                 | 3420      | 47652864 |
| EF6 | ObjectContext Linq Query No Tracking | 3593      | 45260800 |

![Micro puntos de referencia EF5, 5000 iteraciones cálidas](~/ef6/media/ef5micro5000warm.png)

![Micro puntos de referencia EF6, 5000 iteraciones cálidas](~/ef6/media/ef6micro5000warm.png)

Los micropuntos de referencia son muy sensibles a los pequeños cambios en el código. En este caso, la diferencia entre los costos de Entity Framework 5 y Entity Framework 6 se debe a la adición de [interceptación](~/ef6/fundamentals/logging-and-interception.md) y [mejoras transaccionales.](~/ef6/saving/transactions.md) Estos números de micropuntos, sin embargo, son una visión amplificada en un fragmento muy pequeño de lo que hace Entity Framework. Los escenarios del mundo real de consultas en caliente no deben ver una regresión de rendimiento al actualizar de Entity Framework 5 a Entity Framework 6.

Para comparar el rendimiento real de las diferentes opciones de consulta, creamos 5 variaciones de prueba independientes donde usamos una opción de consulta diferente para seleccionar todos los productos cuyo nombre de categoría es "Beverages". Cada iteración incluye el costo de crear el contexto y el costo de materializar todas las entidades devueltas. 10 iteraciones se ejecutan sin tiempo antes de tomar la suma de 1000 iteraciones cronometradas. Los resultados mostrados son la mediana de ejecución tomada a partir de 5 ejecuciones de cada prueba. Para obtener más información, consulte el Apéndice B, que incluye el código de la prueba.

| EF  | Prueba                                        | Tiempo (ms) | Memoria   |
|:----|:--------------------------------------------|:----------|:---------|
| EF5 | Comando de entidad ObjectContext                | 621       | 39350272 |
| EF5 | DbContext Sql Query on Database             | 825       | 37519360 |
| EF5 | Consulta de almacén ObjectContext                   | 878       | 39460864 |
| EF5 | ObjectContext Linq Query No Tracking        | 969       | 38293504 |
| EF5 | Entidad ObjectContext Sql mediante la consulta de objetos | 1089      | 38981632 |
| EF5 | ObjectContext Compiled Query                | 1099      | 38682624 |
| EF5 | ObjectContext Linq Query                    | 1152      | 38178816 |
| EF5 | DbContext Linq Query No Tracking            | 1208      | 41803776 |
| EF5 | Consulta Sql de DbContext en DbSet                | 1414      | 37982208 |
| EF5 | Consulta DbContext Linq                        | 1574      | 41738240 |
|     |                                             |           |          |
| EF6 | Comando de entidad ObjectContext                | 480       | 47247360 |
| EF6 | Consulta de almacén ObjectContext                   | 493       | 46739456 |
| EF6 | DbContext Sql Query on Database             | 614       | 41607168 |
| EF6 | ObjectContext Linq Query No Tracking        | 684       | 46333952 |
| EF6 | Entidad ObjectContext Sql mediante la consulta de objetos | 767       | 48865280 |
| EF6 | ObjectContext Compiled Query                | 788       | 48467968 |
| EF6 | DbContext Linq Query No Tracking            | 878       | 47554560 |
| EF6 | ObjectContext Linq Query                    | 953       | 47632384 |
| EF6 | Consulta Sql de DbContext en DbSet                | 1023      | 41992192 |
| EF6 | Consulta DbContext Linq                        | 1290      | 47529984 |


![Consulta en caliente de EF5 1000 iteraciones](~/ef6/media/ef5warmquery1000.png)

![Consulta en caliente de EF6 1000 iteraciones](~/ef6/media/ef6warmquery1000.png)

> [!NOTE]
> Para completar, incluimos una variación en la que ejecutamos una consulta de Entity SQLEntity SQL en EntityCommand. Sin embargo, dado que los resultados no se materializan para este tipo de consultas, la comparación no es necesariamente de manzanas a manzanas. La prueba incluye una aproximación cercana a la materialización para tratar de hacer la comparación más justa.

En este caso de extremo a extremo, Entity Framework 6 supera a Entity Framework 5 debido a las mejoras de&lt;rendimiento&gt; realizadas en varias partes de la pila, incluida una inicialización de DbContext mucho más ligera y búsquedas de MetadataCollection T más rápidas.

## <a name="7-design-time-performance-considerations"></a>7 Consideraciones sobre el rendimiento del tiempo de diseño

### <a name="71-inheritance-strategies"></a>7.1 Estrategias de herencia

Otra consideración de rendimiento al usar Entity Framework es la estrategia de herencia que se usa. Entity Framework admite 3 tipos básicos de herencia y sus combinaciones:

-   Tabla por jerarquía (TPH): donde cada conjunto de herencia se asigna a una tabla con una columna discriminadora para indicar qué tipo concreto de la jerarquía se representa en la fila.
-   Tabla por tipo (TPT): donde cada tipo tiene su propia tabla en la base de datos; las tablas secundarias solo definen las columnas que la tabla primaria no contiene.
-   Tabla por clase (TPC): donde cada tipo tiene su propia tabla completa en la base de datos; las tablas secundarias definen todos sus campos, incluidos los definidos en los tipos primarios.

Si el modelo utiliza la herencia TPT, las consultas que se generan serán más complejas que las que se generan con las otras estrategias de herencia, lo que puede dar lugar a tiempos de ejecución más largos en el almacén.Por lo general, se tarda más tiempo en generar consultas a través de un modelo TPT y en materializar los objetos resultantes.

Consulte la entrada \< https://docs.microsoft.com/archive/blogs/adonet/performance-considerations-when-using-tpt-table-per-type-inheritance-in-the-entity-framework>de blog MSDN "Consideraciones de rendimiento al usar la herencia TPT (tabla por tipo) en Entity Framework:.

#### <a name="711-avoiding-tpt-in-model-first-or-code-first-applications"></a>7.1.1 Evitar TPT en aplicaciones Model First o Code First

Cuando se crea un modelo sobre una base de datos existente que tiene un esquema TPT, no tiene muchas opciones. Pero al crear una aplicación con Model First o Code First, debe evitar la herencia de TPT por problemas de rendimiento.

Cuando use Model First en el Asistente para Entity Designer, obtendrá TPT para cualquier herencia en el modelo. Si desea cambiar a una estrategia de herencia de TPH con Model First, puede usar el \< http://visualstudiogallery.msdn.microsoft.com/df3541c3-d833-4b65-b942-989e7ec74c87/>)"Entity Designer Database Generation Power Pack" disponible en la Galería de Visual Studio ( .

Al usar Code First para configurar la asignación de un modelo con herencia, EF usará TPH de forma predeterminada, por lo tanto, todas las entidades de la jerarquía de herencia se asignarán a la misma tabla. Consulte la sección "Asignación con la API fluida" del artículo "Code [http://msdn.microsoft.com/magazine/hh126815.aspx](https://msdn.microsoft.com/magazine/hh126815.aspx)First in Entity Framework4.1" en MSDN Magazine ( ) para obtener más detalles.

### <a name="72-upgrading-from-ef4-to-improve-model-generation-time"></a>7.2 Actualización de EF4 para mejorar el tiempo de generación del modelo

Una mejora específica de SQL Server en el algoritmo que genera la capa de almacén (SSDL) del modelo está disponible en Entity Framework 5 y 6, y como una actualización de Entity Framework 4 cuando se instala Visual Studio 2010 SP1. Los siguientes resultados de la prueba demuestran la mejora al generar un modelo muy grande, en este caso el modelo navision. Consulte el Apéndice C para obtener más detalles al respecto.

El modelo contiene 1005 conjuntos de entidades y 4227 conjuntos de asociaciones.

| Configuración                              | Desglose del tiempo consumido                                                                                                                                               |
|:-------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Visual Studio 2010, Entity Framework 4     | Generación SSDL: 2 h 27 min <br/> Generación de mapeo: 1 segundo <br/> Generación CSDL: 1 segundo <br/> Generación ObjectLayer: 1 segundo <br/> Generación de vistas: 2 h 14 min |
| Visual Studio 2010 SP1, Entity Framework 4 | Generación SSDL: 1 segundo <br/> Generación de mapeo: 1 segundo <br/> Generación CSDL: 1 segundo <br/> Generación ObjectLayer: 1 segundo <br/> Generación de vistas: 1 hora 53 min   |
| Visual Studio 2013, Entity Framework 5     | Generación SSDL: 1 segundo <br/> Generación de mapeo: 1 segundo <br/> Generación CSDL: 1 segundo <br/> Generación ObjectLayer: 1 segundo <br/> Generación de vistas: 65 minutos    |
| Visual Studio 2013, Entity Framework 6     | Generación SSDL: 1 segundo <br/> Generación de mapeo: 1 segundo <br/> Generación CSDL: 1 segundo <br/> Generación ObjectLayer: 1 segundo <br/> Generación de vistas: 28 segundos.   |


Vale la pena señalar que al generar el SSDL, la carga se gasta casi por completo en SQL Server, mientras que el equipo de desarrollo de cliente está esperando inactivo para que los resultados vuelvan del servidor. Los administradores de bases de datos deberían apreciar especialmente esta mejora. También vale la pena señalar que esencialmente todo el costo de la generación de modelos tiene lugar en View Generation ahora.

### <a name="73-splitting-large-models-with-database-first-and-model-first"></a>7.3 Dividir modelos grandes con la base de datos primero y el modelo primero

A medida que aumenta el tamaño del modelo, la superficie del diseñador se vuelve desordenada y difícil de usar. Normalmente consideramos que un modelo con más de 300 entidades es demasiado grande para usar eficazmente el diseñador. La siguiente entrada de blog describe varias \< https://docs.microsoft.com/archive/blogs/adonet/working-with-large-models-in-entity-framework-part-2>opciones para dividir modelos grandes: .

La publicación se escribió para la primera versión de Entity Framework, pero los pasos siguen siendo aplicables.

### <a name="74-performance-considerations-with-the-entity-data-source-control"></a>7.4 Consideraciones de rendimiento con Entity Data Source Control

Hemos visto casos en pruebas de esfuerzo y rendimiento multiproceso en los que el rendimiento de una aplicación web mediante EntityDataSource Control se deteriora significativamente. La causa subyacente es que EntityDataSource llama repetidamente a MetadataWorkspace.LoadFromAssembly en los ensamblados a los que hace referencia la aplicación web para detectar los tipos que se usarán como entidades.

La solución consiste en establecer el ContextTypeName de EntityDataSource en el nombre de tipo de la clase ObjectContext derivada. Esto desactiva el mecanismo que examina todos los ensamblados a los que se hace referencia en busca de tipos de entidad.

Establecer el campo ContextTypeName también evita un problema funcional en el que EntityDataSource en .NET 4.0 produce una ReflectionTypeLoadException cuando no se puede cargar un tipo desde un ensamblado a través de la reflexión. Este problema se ha corregido en .NET 4.5.

### <a name="75-poco-entities-and-change-tracking-proxies"></a>7.5 Entidades POCO y proxies de seguimiento de cambios

Entity Framework permite usar clases de datos personalizadas junto con el modelo de datos sin realizar modificaciones en las propias clases de datos. Esto significa que podrá utilizar objetos CLR "antiguos" (POCO), tales como objetos de dominio existentes, con el modelo de datos. Estas clases de datos POCO (también conocidas como objetos de persistencia ignorante), que se asignan a entidades definidas en un modelo de datos, admiten la mayoría de los mismos comportamientos de consulta, inserción, actualización y eliminación que los tipos de entidad generados por las herramientas de Entity Data Model.

Entity Framework también puede crear clases de proxy derivadas de los tipos POCO, que se usan cuando se desea habilitar características como la carga diferida y el seguimiento automático de cambios en entidades POCO. Las clases POCO deben cumplir ciertos requisitos para permitir [http://msdn.microsoft.com/library/dd468057.aspx](https://msdn.microsoft.com/library/dd468057.aspx)que Entity Framework use servidores proxy, como se describe aquí: .

Los servidores proxy de seguimiento de oportunidades notificarán al administrador de estado de objetos cada vez que cualquiera de las propiedades de las entidades tenga su valor cambiado, por lo que Entity Framework conoce el estado real de las entidades todo el tiempo. Esto se hace agregando eventos de notificación al cuerpo de los métodos de establecedor de sus propiedades y haciendo que el administrador de estado de objetos procese estos eventos. Tenga en cuenta que la creación de una entidad proxy suele ser más costosa que crear una entidad POCO no proxy debido al conjunto agregado de eventos creados por Entity Framework.

Cuando una entidad POCO no tiene un proxy de seguimiento de cambios, los cambios se encuentran comparando el contenido de las entidades con una copia de un estado guardado anterior. Esta comparación profunda se convertirá en un proceso largo cuando tenga muchas entidades en el contexto, o cuando las entidades tengan una gran cantidad de propiedades, incluso si ninguna de ellas ha cambiado desde que tuvo lugar la última comparación.

En resumen: pagará un hit de rendimiento al crear el proxy de seguimiento de cambios, pero el seguimiento de cambios le ayudará a acelerar el proceso de detección de cambios cuando las entidades tengan muchas propiedades o cuando tenga muchas entidades en el modelo. Para las entidades con un pequeño número de propiedades donde la cantidad de entidades no crece demasiado, tener proxies de seguimiento de cambios puede no ser de gran beneficio.

## <a name="8-loading-related-entities"></a>8 Carga de entidades relacionadas

### <a name="81-lazy-loading-vs-eager-loading"></a>8.1 Carga diferida frente a carga anhelada

Entity Framework ofrece varias formas diferentes de cargar las entidades relacionadas con la entidad de destino. Por ejemplo, al consultar Productos, hay diferentes maneras en que los pedidos relacionados se cargarán en el Administrador de estado de objetos. Desde el punto de vista del rendimiento, la pregunta más importante a tener en cuenta al cargar entidades relacionadas será si se debe usar La carga diferida o la carga diligente.

Cuando se usa Eager Loading, las entidades relacionadas se cargan junto con el conjunto de entidades de destino. Utilice una instrucción Include en la consulta para indicar qué entidades relacionadas desea incorporar.

Cuando se usa la carga diferida, la consulta inicial solo incluye el conjunto de entidades de destino. Pero cada vez que tiene acceso a una propiedad de navegación, se emite otra consulta en el almacén para cargar la entidad relacionada.

Una vez que se ha cargado una entidad, cualquier otra consulta para la entidad la cargará directamente desde el Administrador de estado de objetos, ya sea que esté utilizando la carga diferida o la carga diligente.

### <a name="82-how-to-choose-between-lazy-loading-and-eager-loading"></a>8.2 Cómo elegir entre Lazy Loading y Eager Loading

Lo importante es que comprenda la diferencia entre Lazy Loading y Eager Loading para que pueda tomar la decisión correcta para su aplicación. Esto le ayudará a evaluar el equilibrio entre varias solicitudes en la base de datos frente a una única solicitud que puede contener una carga grande. Puede ser apropiado utilizar la carga diligente en algunas partes de la aplicación y la carga diferida en otras partes.

Como ejemplo de lo que está sucediendo bajo el capó, supongamos que desea consultar para los clientes que viven en el Reino Unido y su recuento de pedidos.

**Uso de La carga de Eager**

``` csharp
using (NorthwindEntities context = new NorthwindEntities())
{
    var ukCustomers = context.Customers.Include(c => c.Orders).Where(c => c.Address.Country == "UK");
    var chosenCustomer = AskUserToPickCustomer(ukCustomers);
    Console.WriteLine("Customer Id: {0} has {1} orders", customer.CustomerID, customer.Orders.Count);
}
```

**Uso de la carga diferida**

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

Al usar la carga diligente, emitirá una sola consulta que devuelve todos los clientes y todos los pedidos. El comando store tiene el siguiente aspecto:

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

Y cada vez que accede a la propiedad de navegación Pedidos de un cliente, se emite otra consulta como la siguiente en la tienda:

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

Para obtener más información, consulte [Carga de objetos relacionados](https://msdn.microsoft.com/library/bb896272.aspx).

#### <a name="821-lazy-loading-versus-eager-loading-cheat-sheet"></a>8.2.1 Carga diferida frente a hoja de trucos de carga ansiosa

No hay tal cosa como un tamaño único para elegir la carga diligente frente a la carga diferida. Trate primero de entender las diferencias entre ambas estrategias para que pueda hacer una decisión bien informada; Además, considere si el código se ajusta a cualquiera de los siguientes escenarios:

| Escenario                                                                    | Nuestra sugerencia                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
|:----------------------------------------------------------------------------|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ¿Necesita tener acceso a muchas propiedades de navegación de las entidades recuperadas? | **No** - Ambas opciones probablemente lo harán. Sin embargo, si la carga útil que trae la consulta no es demasiado grande, puede experimentar beneficios de rendimiento mediante la carga de Eager, ya que requerirá menos viajes de ida y vuelta de red para materializar los objetos. <br/> <br/> **Sí:** si necesita tener acceso a muchas propiedades de navegación de las entidades, lo haría mediante varias instrucciones include en la consulta con la carga de Eager. Cuantas más entidades incluya, mayor será la carga que devolverá la consulta. Una vez que incluya tres o más entidades en la consulta, considere la posibilidad de cambiar a carga diferida. |
| ¿Sabe exactamente qué datos se necesitarán en tiempo de ejecución?                   | **No** - La carga diferida será mejor para usted. De lo contrario, puede terminar consultando datos que no necesitará. <br/> <br/> **Sí** - La carga de Eager es probablemente su mejor apuesta; ayudará a cargar conjuntos enteros más rápido. Si la consulta requiere la obtención de una gran cantidad de datos y esto se vuelve demasiado lento, intente la carga diferida en su lugar.                                                                                                                                                                                                                                                       |
| ¿El código se está ejecutando lejos de la base de datos? (aumento de la latencia de red)  | **No:** cuando la latencia de red no es un problema, el uso de la carga diferida puede simplificar el código. Recuerde que la topología de la aplicación puede cambiar, por lo que no des la proximidad de la base de datos por la que se concede. <br/> <br/> **Sí** - Cuando la red es un problema, solo usted puede decidir qué se ajusta mejor a su escenario. Típicamente la carga de Eager será mejor porque requiere menos viajes de ida y vuelta.                                                                                                                                                                                                      |


#### <a name="822-performance-concerns-with-multiple-includes"></a>8.2.2 Problemas de rendimiento con múltiples incluye

Cuando escuchamos preguntas de rendimiento que implican problemas de tiempo de respuesta del servidor, el origen del problema son consultas con frecuencia con varias instrucciones Include. Aunque la inclusión de entidades relacionadas en una consulta es eficaz, es importante comprender lo que está sucediendo debajo de las cubiertas.

Una consulta con varias instrucciones Include tarda un tiempo relativamente largo en pasar por nuestro compilador de plan interno para generar el comando store. La mayor parte de este tiempo se dedica a intentar optimizar la consulta resultante. El comando store generado contendrá una unión externa o unión para cada inclusión, dependiendo de la asignación. Consultas como esta traerán grandes gráficos conectados de la base de datos en una sola carga útil, lo que acerbará cualquier problema de ancho de banda, especialmente cuando hay mucha redundancia en la carga útil (por ejemplo, cuando se utilizan varios niveles de Incluir para atravesar asociaciones en la dirección de uno a varios).

Puede comprobar los casos en los que las consultas devuelven cargas excesivamente grandes accediendo al TSQL subyacente para la consulta mediante ToTraceString y ejecutando el comando store en SQL Server Management StudioSQL Server Management Studio para ver el tamaño de la carga. En tales casos, puede intentar reducir el número de instrucciones Include en la consulta para incorporar los datos que necesita. O puede dividir la consulta en una secuencia más pequeña de subconsultas, por ejemplo:

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

Esto solo funcionará en consultas rastreadas, ya que estamos haciendo uso de la capacidad que tiene el contexto para realizar la resolución de identidady la corrección de asociación automáticamente.

Al igual que con la carga diferida, el equilibrio será más consultas para cargas útiles más pequeñas. También puede usar proyecciones de propiedades individuales para seleccionar explícitamente solo los datos que necesita de cada entidad, pero no cargará entidades en este caso y no se admitirán actualizaciones.

#### <a name="823-workaround-to-get-lazy-loading-of-properties"></a>8.2.3 Solución alternativa para obtener una carga diferida de las propiedades

Entity Framework actualmente no admite la carga diferida de propiedades escalares o complejas. Sin embargo, en los casos en los que tiene una tabla que incluye un objeto grande como un BLOB, puede usar la división de tablas para separar las propiedades grandes en una entidad independiente. Por ejemplo, supongamos que tiene una tabla Product que incluye una columna de foto varbinary. Si no necesita tener acceso a esta propiedad con frecuencia en las consultas, puede usar la división de tablas para incorporar solo las partes de la entidad que normalmente necesita. La entidad que representa la foto del producto solo se cargará cuando la necesite explícitamente.

Un buen recurso que muestra cómo habilitar la división de tablas es la \< http://blogs.microsoft.co.il/blogs/gilf/archive/2009/10/13/table-splitting-in-entity-framework.aspx>entrada de blog "División de tablas en Entity Framework" de Gil Fink: .

## <a name="9-other-considerations"></a>9 Otras consideraciones

### <a name="91-server-garbage-collection"></a>9.1 Recolección de basura del servidor

Algunos usuarios pueden experimentar contención de recursos que limita el paralelismo que esperan cuando el recolector de elementos no utilizados no está configurado correctamente. Siempre que se use EF en un escenario multiproceso o en cualquier aplicación que se asemeje a un sistema del lado servidor, asegúrese de habilitar la recolección de elementos no utilizados del servidor. Esto se hace a través de una configuración simple en el archivo de configuración de la aplicación:

``` xml
<?xmlversion="1.0" encoding="utf-8" ?>
<configuration>
        <runtime>
               <gcServer enabled="true" />
        </runtime>
</configuration>
```

Esto debería disminuir la contención de subprocesos y aumentar el rendimiento hasta en un 30 % en escenarios saturados de CPU. En términos generales, siempre debe probar cómo se comporta la aplicación mediante la colección de elementos no utilizados clásica (que se ajusta mejor para la interfaz de usuario y los escenarios del lado cliente), así como la recolección de elementos no utilizados del servidor.

### <a name="92-autodetectchanges"></a>9.2 AutoDetectChanges

Como se mencionó anteriormente, Entity Framework puede mostrar problemas de rendimiento cuando la caché de objetos tiene muchas entidades. Ciertas operaciones, como Add, Remove, Find, Entry y SaveChanges, desencadenan llamadas a DetectChanges que podrían consumir una gran cantidad de CPU en función del tamaño de la caché de objetos. La razón de esto es que la memoria caché de objetos y el administrador de estado de objetos intentan permanecer lo más sincronizados posible en cada operación realizada con un contexto para que se garantice que los datos generados son correctos en una amplia gama de escenarios.

Por lo general, es una buena práctica dejar habilitada la detección automática de cambios de Entity Framework durante toda la vida útil de la aplicación. Si su escenario se ve afectado negativamente por el uso elevado de la CPU y sus perfiles indican que el culpable es la llamada a DetectChanges, considere la posibilidad de desactivar temporalmente AutoDetectChanges en la parte confidencial del código:

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

Antes de desactivar AutoDetectChanges, es bueno comprender que esto podría hacer que Entity Framework pierda su capacidad de realizar un seguimiento de cierta información sobre los cambios que se están produciendo en las entidades. Si se controla incorrectamente, esto podría causar incoherencia de datos en la aplicación. Para obtener más información sobre cómo \< http://blog.oneunicorn.com/2012/03/12/secrets-of-detectchanges-part-3-switching-off-automatic-detectchanges/>desactivar AutoDetectChanges, lea .

### <a name="93-context-per-request"></a>9.3 Contexto por solicitud

Los contextos de Entity Framework están diseñados para usarse como instancias de corta duración con el fin de proporcionar la experiencia de rendimiento más óptima. Se espera que los contextos sean de corta duración y se descarten, y como tal se han implementado para ser muy ligeros y reutilizar metadatos siempre que sea posible. En escenarios web es importante tener esto en cuenta y no tener un contexto durante más de la duración de una sola solicitud. De forma similar, en escenarios no web, el contexto debe descartarse en función de su comprensión de los diferentes niveles de almacenamiento en caché en Entity Framework. En términos generales, se debe evitar tener una instancia de contexto a lo largo de la vida de la aplicación, así como contextos por subproceso y contextos estáticos.

### <a name="94-database-null-semantics"></a>9.4 Semántica nula de base de datos

Entity Framework de forma predeterminada generará\# código SQL que tiene semántica de comparación nula de C. Considere la consulta de ejemplo siguiente:

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

En este ejemplo, estamos comparando un número de variables que aceptan valores NULL con propiedades que aceptan valores NULL en la entidad, como SupplierID y UnitPrice. El SQL generado para esta consulta le preguntará si el valor del parámetro es el mismo que el valor de columna o si el parámetro y los valores de columna son null. Esto ocultará la forma en que el servidor\# de base de datos controla los valores NULL y proporcionará una experiencia de c nulo coherente entre los distintos proveedores de bases de datos. Por otro lado, el código generado es un poco complicado y puede no funcionar bien cuando la cantidad de comparaciones en la instrucción where de la consulta crece a un número grande.

Una manera de lidiar con esta situación es mediante el uso de la semántica nula de la base de datos. Tenga en cuenta que esto podría\# comportarse de forma diferente a la semántica nula de C, ya que ahora Entity Framework generará SQL más simple que expone la forma en que el motor de base de datos controla los valores nulos. La semántica nula de la base de datos se puede activar por contexto con una sola línea de configuración en la configuración de contexto:

``` csharp
                context.Configuration.UseDatabaseNullSemantics = true;
```

Las consultas de tamaño pequeño a mediano no mostrarán una mejora perceptible del rendimiento al usar la semántica nula de la base de datos, pero la diferencia se hará más notable en las consultas con un gran número de posibles comparaciones nulas.

En la consulta de ejemplo anterior, la diferencia de rendimiento era inferior al 2% en un microbenchmark que se ejecutaba en un entorno controlado.

### <a name="95-async"></a>9.5 Asincrónico

Entity Framework 6 introdujo compatibilidad con operaciones asincrónicas cuando se ejecuta en .NET 4.5 o posterior. En su mayor parte, las aplicaciones que tienen contención relacionada con E/S aprovecharán al máximo el uso de operaciones asincrónicas de consulta y guardado. Si la aplicación no sufre la contención de E/S, el uso de async se ejecutará, en el mejor de los casos, sincrónicamente y devolverá el resultado en la misma cantidad de tiempo que una llamada sincrónica, o en el peor de los casos, simplemente aplazará la ejecución a una tarea asincrónica y agregará tiempo adicional a la finalización del escenario.

Para obtener información sobre cómo funciona la programación asincrónica que le [http://msdn.microsoft.com/library/hh191443.aspx](https://msdn.microsoft.com/library/hh191443.aspx)ayudará a decidir si async mejorará el rendimiento de la aplicación, visite . Para obtener más información sobre el uso de operaciones asincrónicas en Entity Framework, vea [Consulta asincrónica y Guardar](~/ef6/fundamentals/async.md
).

### <a name="96-ngen"></a>9.6 NGEN

Entity Framework 6 no viene en la instalación predeterminada de .NET Framework. Como tal, los ensamblados de Entity Framework no son NGEN'd de forma predeterminada, lo que significa que todo el código de Entity Framework está sujeto a los mismos costos de JIT'ing que cualquier otro ensamblado MSIL. Esto podría degradar la experiencia F5 durante el desarrollo y también el inicio en frío de la aplicación en los entornos de producción. Con el fin de reducir los costos de CPU y memoria de JIT'ing es aconsejable NGEN las imágenes de Entity Framework según corresponda. Para obtener más información sobre cómo mejorar el rendimiento de inicio de Entity Framework 6 con NGEN, consulte Mejora del rendimiento de [inicio con NGen](~/ef6/fundamentals/performance/ngen.md).

### <a name="97-code-first-versus-edmx"></a>9.7 Código Primero contra EDMX

Entity Framework razones sobre el problema de discordancia de impedancia entre la programación orientada a objetos y las bases de datos relacionales mediante una representación en memoria del modelo conceptual (los objetos), el esquema de almacenamiento (la base de datos) y una asignación entre los dos. Estos metadatos se denominan Entity Data Model o EDM para abreviar. De este EDM, Entity Framework derivará las vistas a los datos de ida y vuelta de los objetos en memoria a la base de datos y de nuevo.

Cuando Entity Framework se utiliza con un archivo EDMX que especifica formalmente el modelo conceptual, el esquema de almacenamiento y la asignación, la fase de carga del modelo solo tiene que validar que el EDM es correcto (por ejemplo, asegúrese de que no faltan asignaciones), genere las vistas y, a continuación, valide las vistas y tenga estos metadatos listos para su uso. Solo entonces se puede ejecutar una consulta o guardar nuevos datos en el almacén de datos.

El enfoque de Code First es, en el fondo, un sofisticado generador de Entity Data Model. Entity Framework tiene que producir un EDM a partir del código proporcionado; lo hace analizando las clases implicadas en el modelo, aplicando convenciones y configurando el modelo a través de la API Fluent. Después de compilar el EDM, Entity Framework se comporta esencialmente de la misma manera que si hubiera estado presente un archivo EDMX en el proyecto. Por lo tanto, la creación del modelo desde Code First agrega una complejidad adicional que se traduce en un tiempo de inicio más lento para Entity Framework en comparación con tener un EDMX. El costo depende completamente del tamaño y la complejidad del modelo que se está construyendo.

Al elegir usar EDMX frente a Code First, es importante saber que la flexibilidad introducida por Code First aumenta el costo de crear el modelo por primera vez. Si la aplicación puede soportar el costo de esta carga por primera vez, normalmente Code First será la forma preferida de hacerlo.

## <a name="10-investigating-performance"></a>10 Investigar el rendimiento

### <a name="101-using-the-visual-studio-profiler"></a>10.1 Uso de Visual Studio Profiler

Si tiene problemas de rendimiento con Entity Framework, puede usar un generador de perfiles como el integrado en Visual Studio para ver dónde pasa su tiempo la aplicación. Esta es la herramienta que usamos para generar los gráficos circulares en la \< https://docs.microsoft.com/archive/blogs/adonet/exploring-the-performance-of-the-ado-net-entity-framework-part-1>) entrada de blog "Explorando el rendimiento de la ADO.NET Entity Framework - Parte 1" (que muestran dónde Entity Framework pasa su tiempo durante consultas frías y cálidas.

La entrada de blog "Profiling Entity Framework using the Visual Studio 2010 Profiler" escrita por el equipo de asesoramiento al cliente de datos y modelado muestra un ejemplo real de cómo usaron el generador de perfiles para investigar un problema de rendimiento.\<https://docs.microsoft.com/archive/blogs/dmcat/profiling-entity-framework-using-the-visual-studio-2010-profiler>. Este post fue escrito para una aplicación de Windows. Si necesita generar perfiles de una aplicación web, las herramientas de Windows Performance Recorder (WPR) y Windows Performance Analyzer (WPA) pueden funcionar mejor que trabajar desde Visual Studio. WPR y WPA forman parte del Kit de herramientas de rendimiento [http://www.microsoft.com/download/details.aspx?id=39982](https://www.microsoft.com/download/details.aspx?id=39982)de Windows que se incluye con el Kit de evaluación e implementación de Windows ( ).

### <a name="102-applicationdatabase-profiling"></a>10.2 Elaboración de perfiles de aplicaciones/bases de datos

Herramientas como el generador de perfiles integrado en Visual Studio le indican dónde está pasando el tiempo la aplicación.Hay disponible otro tipo de generador de perfiles que realiza un análisis dinámico de la aplicación en ejecución, ya sea en producción o en preproducción en función de las necesidades, y busca dificultades comunes y antipatrones de acceso a la base de datos.

Dos generadores de perfiles disponibles \< http://efprof.com>) comercialmente son Entity \< http://ormprofiler.com>)Framework Profiler ( y ORMProfiler ( .

Si la aplicación es una aplicación MVC que usa Code First, puede usar MiniProfiler de StackExchange. Scott Hanselman describe esta herramienta en \< http://www.hanselman.com/blog/NuGetPackageOfTheWeek9ASPNETMiniProfilerFromStackExchangeRocksYourWorld.aspx>su blog en: .

Para obtener más información sobre la generación de perfiles de la actividad de la base de datos de la aplicación, consulte el artículo de Julie Lerman en MSDN Magazine titulado Actividad de base de datos de generación de [perfiles en Entity Framework](https://msdn.microsoft.com/magazine/gg490349.aspx).

### <a name="103-database-logger"></a>10.3 Registrador de bases de datos

Si usa Entity Framework 6 también considere la posibilidad de usar la funcionalidad de registro integrada. La propiedad Database del contexto puede indicarse que registre su actividad a través de una configuración simple de una línea:

``` csharp
    using (var context = newQueryComparison.DbC.NorthwindEntities())
    {
        context.Database.Log = Console.WriteLine;
        var q = context.Products.Where(p => p.Category.CategoryName == "Beverages");
        q.ToList();
    }
```

En este ejemplo, la actividad de la base de datos se registrará&lt;&gt; en la consola, pero la propiedad Log se puede configurar para llamar a cualquier delegado de cadena de acción.

Si desea habilitar el registro de base de datos sin volver a compilar y usa Entity Framework 6.1 o posterior, puede hacerlo agregando un interceptor en el archivo web.config o app.config de la aplicación.

``` xml
  <interceptors>
    <interceptor type="System.Data.Entity.Infrastructure.Interception.DatabaseLogger, EntityFramework">
      <parameters>
        <parameter value="C:\Path\To\My\LogOutput.txt"/>
      </parameters>
    </interceptor>
  </interceptors>
```

Para obtener más información sobre cómo agregar \< http://blog.oneunicorn.com/2014/02/09/ef-6-1-turning-on-logging-without-recompiling/>el registro sin volver a compilar, vaya a .

## <a name="11-appendix"></a>11 Apéndice

### <a name="111-a-test-environment"></a>11.1 A. Entorno de prueba

Este entorno utiliza una configuración de 2 equipos con la base de datos en un equipo independiente de la aplicación cliente. Las máquinas están en el mismo rack, por lo que la latencia de red es relativamente baja, pero más realista que un entorno de una sola máquina.

#### <a name="1111-app-server"></a>11.1.1 Servidor de aplicaciones

##### <a name="11111-software-environment"></a>11.1.1.1 Entorno de software

-   Entorno de software de Entity Framework 4
    -   Nombre del sistema operativo: Windows Server 2008 R2 Enterprise SP1.
    -   Visual Studio 2010 – Ultimate.
    -   Visual Studio 2010 SP1 (solo para algunas comparaciones).
-   Entorno de software de Entity Framework 5 y 6
    -   Nombre del sistema operativo: Windows 8.1 Enterprise
    -   Visual Studio 2013 – Ultimate.

##### <a name="11112-hardware-environment"></a>11.1.1.2 Entorno de hardware

-   Procesador dual: Cpu Intel(R) Xeon(R) L5520 W3530 a 2,27 GHz, 2261 Mhz8 GHz, 4 núcleos, 84 procesadores lógicos.
-   2412 GB RamRAM.
-   136 GB SCSI250GB SATA 7200 rpm 3GB/s unidad dividida en 4 particiones.

#### <a name="1112-db-server"></a>11.1.2 Servidor DB

##### <a name="11121-software-environment"></a>11.1.2.1 Entorno de software

-   Nombre del sistema operativo: Windows Server 2008 R28.1 Enterprise SP1.
-   SQL Server 2008 R22012.

##### <a name="11122-hardware-environment"></a>11.1.2.2 Entorno de hardware

-   Procesador único: Intel(R) Xeon(R) CPU L5520 a 2,27 GHz, 2261 MhzES-1620 0 a 3,60 GHz, 4 núcleos, 8 procesadores lógicos.
-   824 GB RamRAM.
-   465 GB ATA500GB SATA 7200 rpm 6GB/s unidad dividida en 4 particiones.

### <a name="112-b-query-performance-comparison-tests"></a>11.2 B. Pruebas de comparación del rendimiento de las consultas

El modelo Northwind se utilizó para ejecutar estas pruebas. Se generó a partir de la base de datos mediante el diseñador de Entity Framework. A continuación, se utilizó el código siguiente para comparar el rendimiento de las opciones de ejecución de consultas:

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

### <a name="113-c-navision-model"></a>11.3 C. Modelo Navision

La base de datos de Navision es una base de datos grande que se utiliza para demostrar Microsoft Dynamics – NAV. El modelo conceptual generado contiene 1005 conjuntos de entidades y 4227 conjuntos de asociaciones. El modelo utilizado en la prueba es "plano": no se le ha agregado ninguna herencia.

#### <a name="1131-queries-used-for-navision-tests"></a>11.3.1 Consultas utilizadas para las pruebas de Navision

La lista de consultas utilizada con el modelo de Navision contiene 3 categorías de consultas de Entity SQLEntity SQL:

##### <a name="11311-lookup"></a>11.3.1.1 Búsqueda

Una consulta de búsqueda simple sin agregaciones

-   Recuento: 16232
-   Ejemplo:

``` xml
  <Query complexity="Lookup">
    <CommandText>Select value distinct top(4) e.Idle_Time From NavisionFKContext.Session as e</CommandText>
  </Query>
```

##### <a name="11312singleaggregating"></a>11.3.1.2 SingleAggregating

Una consulta de BI normal con varias agregaciones, pero sin subtotales (consulta única)

-   Recuento: 2313
-   Ejemplo:

``` xml
  <Query complexity="SingleAggregating">
    <CommandText>NavisionFK.MDF_SessionLogin_Time_Max()</CommandText>
  </Query>
```

Donde MDF\_\_SessionLogin Time\_Max() se define en el modelo como:

``` xml
  <Function Name="MDF_SessionLogin_Time_Max" ReturnType="Collection(DateTime)">
    <DefiningExpression>SELECT VALUE Edm.Min(E.Login_Time) FROM NavisionFKContext.Session as E</DefiningExpression>
  </Function>
```

##### <a name="11313aggregatingsubtotals"></a>11.3.1.3 Agregación Subtotales

Una consulta de BI con agregaciones y subtotales (a través de union all)

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
