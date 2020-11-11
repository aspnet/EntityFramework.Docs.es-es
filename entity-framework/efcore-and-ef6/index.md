---
title: Comparación de EF6 y EF Core
description: Instrucciones sobre cómo elegir entre EF6 y EF Core.
author: ajcvickers
ms.date: 01/23/2019
uid: efcore-and-ef6/index
ms.openlocfilehash: e95bc572ac723dd22daa10117f5704f29ba97738
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430461"
---
# <a name="compare-ef-core--ef6"></a>Comparar EF Core y EF6

## <a name="ef-core"></a>EF Core

Entity Framework Core ([EF Core](xref:core/index)) es un asignador de base de datos de objeto moderno para .NET. Admite consultas LINQ, seguimiento de cambios, actualizaciones y migraciones de esquemas.

EF Core funciona con SQL Server o SQL Azure, SQLite, Azure Cosmos DB, MySQL, PostgreSQL y muchas otras bases de datos a través de un [modelo de complemento de proveedor de bases de datos](xref:core/providers/index).

## <a name="ef6"></a>EF6

Entity Framework 6 ([EF6](xref:ef6/index)) es un asignador relacional de objetos diseñado para .NET Framework, pero compatible con .NET Core. EF6 es un producto estable y compatible, pero ya no se desarrolla activamente.

## <a name="feature-comparison"></a>Comparación de características

EF Core ofrece nuevas características que no se implementarán en EF6. Sin embargo, no todas las características de EF6 están implementadas actualmente en EF Core.

En las tablas siguientes se comparan las características disponibles en EF Core y EF6. Se trata de una comparación general en la que no se muestran todas las características ni se explican las diferencias entre una misma característica en las distintas versiones de EF.

La columna EF Core indica la versión del producto en la que la característica apareció por primera vez.

### <a name="creating-a-model"></a>Creación de un modelo

| **Característica**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Asignación de clase básica                                   | Sí      | 1.0                                   |
| Constructores con parámetros                          |          | 2.1                                   |
| Conversiones de valores de propiedad                            |          | 2.1                                   |
| Tipos asignados sin claves                             |          | 2.1                                   |
| Convenciones                                           | Sí      | 1.0                                   |
| Convenciones personalizadas                                    | Sí      | 1.0 (parcial; [n.º 214](https://github.com/dotnet/efcore/issues/214)) |
| Anotaciones de datos                                      | Sí      | 1.0                                   |
| API fluida                                            | Sí      | 1.0                                   |
| Herencia: tabla por jerarquía (TPH)                | Sí      | 1.0                                   |
| Herencia: tabla por tipo (TPT)                     | Sí      | Planeado para la versión 5.0 ([n.º 2266](https://github.com/dotnet/efcore/issues/2266)) |
| Herencia: tabla por clase concreta (TPC)           | Sí      | Stretch para la versión 5.0 ([n.º 3170](https://github.com/dotnet/efcore/issues/3170)) <sup>(1)</sup> |
| Propiedades de estado reemplazadas                               |          | 1.0                                   |
| Claves alternativas                                        |          | 1.0                                   |
| Navegaciones de varios a varios                              | Sí      | Planeado para la versión 5.0 ([n.º 19003](https://github.com/dotnet/efcore/issues/19003)) |
| Varios a varios sin entidad de combinación                      | Sí      | En el trabajo pendiente ([n.º 1368](https://github.com/dotnet/efcore/issues/1368)) |
| Generación de claves: base de datos                              | Sí      | 1.0                                   |
| Generación de claves: cliente                                |          | 1.0                                   |
| Tipos complejos/de propiedad                                   | Sí      | 2.0                                   |
| Datos espaciales                                          | Sí      | 2,2                                   |
| Formato de modelo: código                                    | Sí      | 1.0                                   |
| Crear un modelo desde base de datos: línea de comandos              | Sí      | 1.0                                   |
| Actualizar modelo desde base de datos                            | Parcial  | En el trabajo pendiente ([n.º 831](https://github.com/dotnet/efcore/issues/831)) |
| Filtros de consulta global                                  |          | 2.0                                   |
| División de tablas                                       | Sí      | 2.0                                   |
| División de entidades                                      | Sí      | Stretch para la versión 5.0 ([n.º 620](https://github.com/dotnet/efcore/issues/620)) <sup>(1)</sup> |
| Asignación de función escalar de base de datos                      | Insuficiente     | 2.0                                   |
| Asignación de campos                                         |          | 1,1                                   |
| Tipos de referencia que aceptan valores NULL (C# 8.0)                     |          | 3.0                                   |
| Visualización gráfica de modelo                      | Sí      | No hay soporte técnico planeado <sup>(2)</sup>     |
| Editor de modelo gráfico                                | Sí      | No hay soporte técnico planeado <sup>(2)</sup>     |
| Formato de modelo: EDMX (XML)                              | Sí      | No hay soporte técnico planeado <sup>(2)</sup>     |
| Crear un modelo desde base de datos: asistente de VS                 | Sí      | No hay soporte técnico planeado <sup>(2)</sup>     |

### <a name="querying-data"></a>Consultar datos

| **Característica**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Consultas LINQ                                          | Sí      | 1.0                                   |
| SQL generado legible                                | Insuficiente     | 1.0                                   |
| Traslación de GroupBy                                   | Sí      | 2.1                                   |
| Carga de datos relacionados: diligente                           | Sí      | 1.0                                   |
| Carga de datos relacionados: carga diligente de tipo derivados |          | 2.1                                   |
| Carga de datos relacionados: diferida                            | Sí      | 2.1                                   |
| Carga de datos relacionados: explícita                        | Sí      | 1,1                                   |
| Consultas SQL sin formato: tipos de entidad                         | Sí      | 1.0                                   |
| Consultas SQL sin formato: Tipos de entidad sin llave                 | Sí      | 2.1                                   |
| Consultas SQL sin procesar: componer con LINQ                  |          | 1.0                                   |
| Consultas compiladas de manera explícita                           | Insuficiente     | 2.0                                   |
| await foreach (C# 8.0)                                |          | 3.0                                   |
| Lenguaje de consulta basado en texto (Entity SQL)                | Sí      | No hay soporte técnico planeado <sup>(2)</sup>     |

### <a name="saving-data"></a>Guardado de datos

| **Característica**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Seguimiento de cambios: instantánea                             | Sí      | 1.0                                   |
| Seguimiento de cambios: notificación                         | Sí      | 1.0                                   |
| Seguimiento de cambios: servidores proxy                              | Sí      | Combinado para la versión 5.0 ([n.º 10949](https://github.com/dotnet/efcore/issues/10949)) |
| Acceso al estado con seguimiento                               | Sí      | 1.0                                   |
| Simultaneidad optimista                                | Sí      | 1.0                                   |
| Transacciones                                          | Sí      | 1.0                                   |
| Procesamiento de instrucciones por lotes                                |          | 1.0                                   |
| Asignación de procedimientos almacenados                              | Sí      | En el trabajo pendiente ([n.º 245](https://github.com/dotnet/efcore/issues/245)) |
| Grafo desconectado: API de bajo nivel                     | Insuficiente     | 1.0                                   |
| Grafo desconectado: de un extremo a otro                         |          | 1.0 (parcial; [n.º 5536](https://github.com/dotnet/efcore/issues/5536)) |

### <a name="other-features"></a>Otras características

| **Característica**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| Migraciones                                            | Sí      | 1.0                                   |
| API de creación o eliminación de la base de datos                       | Sí      | 1.0                                   |
| Datos de inicialización                                             | Sí      | 2.1                                   |
| Resistencia de la conexión                                 | Sí      | 1,1                                   |
| Interceptores                                          | Sí      | 3.0                                   |
| Eventos                                                | Sí      | 3.0 (parcial; [n.º 626](https://github.com/dotnet/efcore/issues/626)) |
| Registro simple (Database.Log)                         | Sí      | Combinado para la versión 5.0 ([n.º 1199](https://github.com/dotnet/efcore/issues/1199)) |
| Agrupación de DbContext                                     |          | 2.0                                   |

### <a name="database-providers-sup3sup"></a>Proveedores de bases de datos <sup>(3)</sup>

| **Característica**                                           | **EF6.4**| **EF Core**                           |
|:------------------------------------------------------|:---------|:--------------------------------------|
| SQL Server                                            | Sí      | 1.0                                   |
| MySQL                                                 | Sí      | 1.0                                   |
| PostgreSQL                                            | Sí      | 1.0                                   |
| Oracle                                                | Sí      | 1.0                                   |
| SQLite                                                | Sí      | 1.0                                   |
| SQL Server Compact                                    | Sí      | 1.0 <sup>(4)</sup>                    |
| DB2                                                   | Sí      | 1.0                                   |
| Firebird                                              | Sí      | 2.0                                   |
| Jet (Microsoft Access)                                |          | 2.0 <sup>(4)</sup>                    |
| Azure Cosmos DB                                       |          | 3.0                                   |
| En memoria (para pruebas)                               |          | 1.0                                   |

<sup>1</sup> Es probable que no se logren los objetivos de Stretch para una versión determinada. Sin embargo, si las cosas van bien, intentaremos lograrlos.

<sup>2</sup> Algunas características de EF6 no se implementarán en EF Core. Estas características dependen del Entity Data Model (EDM) subyacente de EF6 o son características complejas con una rentabilidad de la inversión relativamente baja. Siempre agradecemos los comentarios, pero, aunque EF Core permite muchas cosas que no son posibles en EF6, no es factible que EF Core admita todas las características de EF6.

<sup>3</sup> Es posible que los proveedores de bases de datos de EF Core implementados por terceros sufran un retraso en la actualización a las versiones principales de EF Core. Vea [Proveedores de bases de datos](xref:core/providers/index) para obtener más información.

<sup>4</sup> Los proveedores de SQL Server Compact y Jet solo funcionan en .NET Framework (no en .NET Core).

### <a name="supported-platforms"></a>Plataformas compatibles

EF Core 3.1 se ejecuta en .NET Core y .NET Framework a través del uso de .NET Standard 2.0. Sin embargo, EF Core 5.0 no se ejecutará en .NET Framework. Consulte [Plataformas](xref:core/miscellaneous/platforms) para obtener más información.

EF6.4 se ejecuta en .NET Core y .NET Framework a través de la compatibilidad con múltiples versiones.

## <a name="guidance-for-new-applications"></a>Guía para las aplicaciones nuevas

Use EF Core en .NET Core para todas las aplicaciones nuevas a menos que la aplicación necesite algún elemento que solo se [admita en .NET Framework](/dotnet/standard/choosing-core-framework-server).

## <a name="guidance-for-existing-ef6-applications"></a>Guía para las aplicaciones existentes de EF6

EF Core no es un reemplazo de EF6. Probablemente, cambiar de EF6 a EF Core requerirá cambios en la aplicación.

Al mover una aplicación de EF6 a .NET Core:

* Siga usando EF6 si el código de acceso a datos es estable y no es probable que evolucione o necesite nuevas características.
* Realice el traslado a EF Core si el código de acceso a datos evoluciona o si la aplicación necesita nuevas características que solo están disponibles en EF Core.
* El traslado a EF Core también se realiza a menudo para obtener un mayor rendimiento. Sin embargo, no todos los escenarios son más rápidos, así que genere primero algunos perfiles.

Para más información, consulte[Traslado de EF6 a EF Core](xref:efcore-and-ef6/porting/index).
