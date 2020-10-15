---
title: 'Registro de cambios que afectan al proveedor: EF Core'
description: Un registro de cambios en Entity Framework Core los proveedores de impacto
author: ajcvickers
ms.date: 08/08/2018
uid: core/providers/provider-log
ms.openlocfilehash: e701bac32c39f13be039d093931510a99070abbf
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065386"
---
# <a name="provider-impacting-changes"></a>Cambios que afectan al proveedor

Esta página contiene vínculos a las solicitudes de incorporación de cambios realizadas en el repositorio de EF Core que puede requerir que reaccionen los autores de otros proveedores de bases de datos. La intención es proporcionar un punto de partida para los autores de proveedores de bases de datos de terceros existentes al actualizar su proveedor a una nueva versión.

Estamos iniciando este registro con los cambios de 2,1 a 2,2. Antes de 2,1 usamos las [`providers-beware`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-beware) etiquetas y [`providers-fyi`](https://github.com/aspnet/EntityFrameworkCore/labels/providers-fyi) en nuestros problemas y solicitudes de incorporación de cambios.

## <a name="22-----3x"></a>2,2---> 3. x

Tenga en cuenta que muchos de los [cambios importantes en el nivel](xref:core/what-is-new/ef-core-3.x/breaking-changes) de la aplicación también afectarán a los proveedores.

* <https://github.com/aspnet/EntityFrameworkCore/pull/14022>
  * Se han quitado las API obsoletas y las sobrecargas de parámetros opcionales contraídas
  * Se quitó DatabaseColumn. GetUnderlyingStoreType ()
* <https://github.com/aspnet/EntityFrameworkCore/pull/14589>
  * Se han quitado las API obsoletas
* <https://github.com/aspnet/EntityFrameworkCore/pull/15044>
  * Es posible que las subclases de CharTypeMapping se hayan interrumpido debido a cambios de comportamiento necesarios para corregir un par de errores en la implementación base.
* <https://github.com/aspnet/EntityFrameworkCore/pull/15090>
  * Se ha agregado una clase base para IDatabaseModelFactory y se ha actualizado para usar un objeto de parámetro para mitigar los saltos futuros.
* <https://github.com/aspnet/EntityFrameworkCore/pull/15123>
  * Usó objetos de parámetro en MigrationsSqlGenerator para mitigar los saltos futuros.
* <https://github.com/aspnet/EntityFrameworkCore/pull/14972>
  * La configuración explícita de los niveles de registro requiere algunos cambios en las API que los proveedores pueden estar usando. En concreto, si los proveedores usan la infraestructura de registro directamente, este cambio puede interrumpir ese uso. Además, los proveedores que usan la infraestructura (que será pública) al avanzar deberán derivar de `LoggingDefinitions` o `RelationalLoggingDefinitions` . Para obtener ejemplos, vea los SQL Server y los proveedores en memoria.
* <https://github.com/aspnet/EntityFrameworkCore/pull/15091>
  * Las cadenas de recursos Core, relacional y abstracciones son ahora públicas.
  * `CoreLoggerExtensions` y `RelationalLoggerExtensions` son ahora públicos. Los proveedores deben usar estas API al registrar eventos definidos en el nivel básico o relacional. No tener acceso a los recursos de registro directamente; todavía son internas.
  * `IRawSqlCommandBuilder` ha cambiado de un servicio singleton a un servicio con ámbito
  * `IMigrationsSqlGenerator` ha cambiado de un servicio singleton a un servicio con ámbito
* <https://github.com/aspnet/EntityFrameworkCore/pull/14706>
  * La infraestructura para la creación de comandos relacionales se ha convertido en pública, de modo que los proveedores pueden utilizarla de forma segura y refactorizar ligeramente.
* <https://github.com/aspnet/EntityFrameworkCore/pull/14733>
  * `ILazyLoader` ha cambiado de un servicio con ámbito a un servicio transitorio
* <https://github.com/aspnet/EntityFrameworkCore/pull/14610>
  * `IUpdateSqlGenerator` ha cambiado de un servicio con ámbito a un servicio singleton
  * Además, se `ISingletonUpdateSqlGenerator` ha quitado
* <https://github.com/aspnet/EntityFrameworkCore/pull/15067>
  * Una gran cantidad de código interno que usaban los proveedores ahora se ha hecho público
  * Ya no debe necssary a la referencia porque se ha `IndentedStringBuilder` factorizado fuera de los lugares que lo exponían
  * Los usos de `NonCapturingLazyInitializer` deben reemplazarse por `LazyInitializer` de la BCL
* <https://github.com/aspnet/EntityFrameworkCore/pull/14608>
  * Este cambio está totalmente incluido en el documento sobre cambios importantes de la aplicación. En el caso de los proveedores, esto puede ser más impactante, ya que la prueba de EF Core a menudo puede dar lugar a este problema, por lo que la infraestructura de prueba ha cambiado para que sea menos probable.
* <https://github.com/aspnet/EntityFrameworkCore/issues/13961>
  * `EntityMaterializerSource` se ha simplificado
* <https://github.com/aspnet/EntityFrameworkCore/pull/14895>
  * La traducción StartsWith ha cambiado de manera que los proveedores pueden querer o deben reaccionar
* <https://github.com/aspnet/EntityFrameworkCore/pull/15168>
  * Los servicios del conjunto de convenciones han cambiado. Los proveedores ahora deben heredar de "ProviderConventionSet" o "RelationalConventionSet".
  * Las personalizaciones se pueden agregar a través de los `IConventionSetCustomizer` servicios, pero está pensada para que las usen otras extensiones, no los proveedores.
  * Las convenciones usadas en tiempo de ejecución se deben resolver desde `IConventionSetBuilder` .
* <https://github.com/aspnet/EntityFrameworkCore/pull/15288>
  * La propagación de datos se ha refactorizado en una API pública para evitar la necesidad de usar tipos internos. Esto solo debe afectar a los proveedores no relacionales, puesto que la propagación se controla mediante la clase relacional base para todos los proveedores relacionales.

## <a name="21-----22"></a>2,1---> 2,2

### <a name="test-only-changes"></a>Cambios de solo prueba

* <https://github.com/aspnet/EntityFrameworkCore/pull/12057> -Permitir delimitadores SQL personalizable en las pruebas
  * Comprobar los cambios que permiten comparaciones de punto flotante no estrictas en BuiltInDataTypesTestBase
  * Probar los cambios que permiten que las pruebas de consulta se vuelvan a usar con diferentes delimitadores de SQL
* <https://github.com/aspnet/EntityFrameworkCore/pull/12072> -Agregar pruebas de DbFunction a las pruebas de especificación relacional
  * De este modo, estas pruebas se pueden ejecutar en todos los proveedores de bases de datos
* <https://github.com/aspnet/EntityFrameworkCore/pull/12362> -Limpieza de prueba asincrónica
  * Quitar `Wait` llamadas, Async innecesario y cambiar el nombre de algunos métodos de prueba
* <https://github.com/aspnet/EntityFrameworkCore/pull/12666> -Unificar la infraestructura de prueba de registro
  * Se ha agregado `CreateListLoggerFactory` y quitado una infraestructura de registro anterior, que requerirá que los proveedores que usan estas pruebas reaccionen
* <https://github.com/aspnet/EntityFrameworkCore/pull/12500> -Ejecutar más pruebas de consulta de forma sincrónica y asincrónica
  * Los nombres de prueba y la factorización han cambiado, lo que requerirá que los proveedores usen estas pruebas para reaccionar
* <https://github.com/aspnet/EntityFrameworkCore/pull/12766> -Cambiar el nombre de las navegaciones en el modelo ComplexNavigations
  * Es posible que los proveedores que usan estas pruebas tengan que reaccionar
* <https://github.com/aspnet/EntityFrameworkCore/pull/12141> -Devolver el contexto al grupo en lugar de desecharlo en las pruebas funcionales
  * Este cambio incluye alguna refactorización de prueba que puede requerir que los proveedores reaccionen

### <a name="test-and-product-code-changes"></a>Cambios de código de prueba y de producto

* <https://github.com/aspnet/EntityFrameworkCore/pull/12109> -Consolide los métodos RelationalTypeMapping. Clone
  * Se permiten cambios en 2,1 en RelationalTypeMapping para una simplificación en las clases derivadas. No creemos que esto se interrumpiera en los proveedores, pero los proveedores pueden aprovechar este cambio en sus clases derivadas de asignación de tipos.
* <https://github.com/aspnet/EntityFrameworkCore/pull/12069> -Consultas con nombre o etiquetadas
  * Agrega la infraestructura para etiquetar las consultas LINQ y hacer que esas etiquetas se muestren como comentarios en SQL. Esto puede requerir que los proveedores reaccionen en la generación de SQL.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13115> -Admitir datos espaciales a través de NTS
  * Permite que las asignaciones de tipos y los traductores de miembros se registren fuera del proveedor.
    * Los proveedores deben llamar a base. FindMapping () en su implementación de ITypeMappingSource para que funcione
  * Siga este patrón para agregar compatibilidad espacial a su proveedor que sea coherente entre los proveedores.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13199> -Agregar depuración mejorada para la creación del proveedor de servicios
  * Permite a DbContextOptionsExtensions implementar una nueva interfaz que puede ayudar a los usuarios a entender por qué se vuelve a crear el proveedor de servicios internos.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13289> -Agrega CanConnect API para su uso por las comprobaciones de estado
  * Este PR agrega el concepto de `CanConnect` que va a usar ASP.net Core comprobaciones de estado para determinar si la base de datos está disponible. De forma predeterminada, la implementación relacional solo llama a `Exist` , pero los proveedores pueden implementar algo diferente si es necesario. Los proveedores no relacionales deberán implementar la nueva API para poder usar la comprobación de estado.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13306> -Actualizar RelationalTypeMapping base para no establecer el tamaño de DbParameter
  * Deje de establecer el tamaño de forma predeterminada, ya que puede provocar el truncamiento. Los proveedores pueden necesitar agregar su propia lógica si es necesario establecer el tamaño.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13372> -RevEng: especificar siempre el tipo de columna para las columnas decimales
  * Configure siempre el tipo de columna para las columnas decimales en código con scaffolding en lugar de configurar por Convención.
  * Los proveedores no deben requerir cambios en su extremo.
* <https://github.com/aspnet/EntityFrameworkCore/pull/13469> -Agrega CaseExpression para generar expresiones CASE de SQL
* <https://github.com/aspnet/EntityFrameworkCore/pull/13648> -Agrega la capacidad de especificar asignaciones de tipos en SqlFunctionExpression para mejorar la inferencia de tipos de almacén de argumentos y resultados.
