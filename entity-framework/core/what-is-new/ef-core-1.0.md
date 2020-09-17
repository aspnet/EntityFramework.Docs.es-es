---
title: Novedades de EF Core 1.0 - EF Core
description: Cambios y mejoras en Entity Framework Core 1.0
author: divega
ms.date: 10/27/2016
uid: core/what-is-new/ef-core-1.0
ms.openlocfilehash: 898f243bace7f38ffb6b216af0bd9249d73f2fe4
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072465"
---
# <a name="features-included-in-ef-core-10"></a>Características incluidas en EF Core 1.0

## <a name="platforms"></a>Plataformas

### <a name="net-framework-451"></a>.NET Framework 4.5.1

Incluye la consola, WPF, WinForms, ASP.NET 4, etc.

### <a name="net-standard-13"></a>.NET Standard 1.3

Incluye ASP.NET Core que tiene como destino tanto .NET Framework como .NET Core en Windows, OSX y Linux.

## <a name="modelling"></a>Modelado

### <a name="basic-modelling"></a>Modelado básico

Según las entidades POCO con las propiedades get/set de tipos escalares comunes (`int`, `string`, etc.).

### <a name="relationships-and-navigation-properties"></a>Relaciones y propiedades de navegación

Las relaciones uno a varios y uno a cero se pueden especificar en el modelo en función de una clave externa. Las propiedades de navegación de tipos de referencia o colección simple se pueden asociar con estas relaciones.

### <a name="built-in-conventions"></a>Convenciones integradas

Construyen un modelo inicial en función de la forma de las clases de entidad.

### <a name="fluent-api"></a>API fluida

Permite reemplazar el método `OnModelCreating` en el contexto para seguir configurando el modelo que la convención detectó.

### <a name="data-annotations"></a>Anotaciones de datos

Son atributos que se pueden agregar a las propiedades o clases de entidad y que influyen en el modelo de EF. Por ejemplo, al agregar `[Required]` se indica a EF que una propiedad es obligatoria.

### <a name="relational-table-mapping"></a>Asignación de tabla relacional

Permite asignar las entidades a tablas o columnas.

### <a name="key-value-generation"></a>Generación de valor de clave

Incluye la generación de bases de datos y la generación del lado cliente.

### <a name="database-generated-values"></a>Valores generados por la base de datos

Permite que la base de datos genere los valores en la inserción (valores predeterminados) o la actualización (columnas calculadas).

### <a name="sequences-in-sql-server"></a>Secuencias en SQL Server

Permite definir los objetos de secuencia en el modelo.

### <a name="unique-constraints"></a>Restricciones únicas

Permite la definición de las claves alternativas y la capacidad de definir las relaciones que se dirigen a esa clave.

### <a name="indexes"></a>Índices

La definición de índices en el modelo introduce automáticamente índices en la base de datos. También se admiten los índices únicos.

### <a name="shadow-state-properties"></a>Propiedades de estado reemplazadas

Permite que las propiedades que se definen en el modelo no se declaren ni almacenen en la clase .NET, pero EF Core sí puede hacer un seguimiento de ellas y actualizarlas. Suele usarse para las propiedades de clave externa cuando no se desea exponerlas en el objeto.

### <a name="table-per-hierarchy-inheritance-pattern"></a>Patrón de herencia de tabla por jerarquía

Permite que las entidades de una jerarquía de herencia se guarde en una sola tabla a través de una columna de discriminador para identificar el tipo de entidad de un registro determinado en la base de datos.

### <a name="model-validation"></a>Validación de modelos

Detecta los patrones no válidos del modelo y proporciona mensajes de error útiles.

## <a name="change-tracking"></a>seguimiento de cambios

### <a name="snapshot-change-tracking"></a>Seguimiento de cambios de instantánea

Permite detectar automáticamente los cambios en las entidades a través de la comparación del estado actual con una copia (instantánea) del estado original.

### <a name="notification-change-tracking"></a>Seguimiento de cambios de notificación

Permite que las entidades notifiquen a la herramienta de seguimiento de cambios cuando se modifiquen los valores de propiedad.

### <a name="accessing-tracked-state"></a>Acceso al estado con seguimiento

A través de `DbContext.Entry` y `DbContext.ChangeTracker`.

### <a name="attaching-detached-entitiesgraphs"></a>Adjuntar grafos o entidades desasociados

La nueva API `DbContext.AttachGraph` ayuda a volver a adjuntar entidades a un contexto para guardar las entidades nuevas o modificadas.

## <a name="saving-data"></a>Guardado de datos

### <a name="basic-save-functionality"></a>Funcionalidad básica de guardado

Permite que los cambios en las instancias de la entidad se conserven en la base de datos.

### <a name="optimistic-concurrency"></a>Simultaneidad optimista

Impide sobrescribir los cambios realizados por otro usuario desde que se capturaron de la base de datos.

### <a name="async-savechanges"></a>Característica SaveChanges asincrónica

Puede liberar el subproceso actual para que procese otras solicitudes mientras la base de datos procesa los comandos que se emiten desde `SaveChanges`.

### <a name="database-transactions"></a>Transacciones de bases de datos

Es decir, `SaveChanges` siempre es atómica (lo que significa que siempre se completa correctamente o que no se realiza ningún cambio en la base de datos). También hay API relacionadas con transacciones que permiten compartir las transacciones entre las instancias de contexto, etc.

### <a name="relational-batching-of-statements"></a>Relacional: procesamiento de instrucciones por lotes

Proporciona un mejor rendimiento mediante el procesamiento por lotes de varios comandos INSERT/UPDATE/DELETE en un solo ciclo de ida y vuelta a la base de datos.

## <a name="query"></a>Consultar

### <a name="basic-linq-support"></a>Compatibilidad básica con LINQ

Proporciona la capacidad de usar LINQ para recuperar datos de la base de datos.

### <a name="mixed-clientserver-evaluation"></a>Evaluación combinada de cliente/servidor

Permite que las consultas contengan una lógica que no se puede evaluar en la base de datos y, por lo tanto, se debe evaluar después de que los datos se recuperan en la memoria.

### <a name="notracking"></a>NoTracking

Las consultas permiten ejecutar más rápido las consultas cuando el contexto no necesita supervisar los cambios realizados en las instancias de entidad (esto es útil si los resultados son de solo lectura).

### <a name="eager-loading"></a>Carga diligente

Proporciona los métodos `Include` y `ThenInclude` para identificar los datos relacionados que se deben capturar cuando se realizan las consultas.

### <a name="async-query"></a>Consulta asincrónica

Puede liberar el subproceso actual (y los recursos asociados) para que procese otras solicitudes mientras la base de datos procesa la consulta.

### <a name="raw-sql-queries"></a>Consultas SQL sin formato

Proporciona el método `DbSet.FromSql` para usar consultas SQL sin procesar para capturar datos. Estas consultas también se pueden componer mediante LINQ.

## <a name="database-schema-management"></a>Administración de esquemas de la base de datos

### <a name="database-creationdeletion-apis"></a>API de creación o eliminación de la base de datos

Diseñadas principalmente para realizar pruebas en las que desea crear o eliminar rápidamente la base de datos sin usar migraciones.

### <a name="relational-database-migrations"></a>Migraciones de la base de datos relacional

Permiten que un esquema de la base de datos relacional evolucione en el tiempo a medida que cambia el modelo.

### <a name="reverse-engineer-from-database"></a>Ingeniería inversa desde la base de datos

Aplica scaffolding a un modelo de EF en función de un esquema de la base de datos relacional.

## <a name="database-providers"></a>Proveedores de bases de datos

### <a name="sql-server"></a>SQL Server

Se conecta a Microsoft SQL Server 2008 y versiones posteriores.

### <a name="sqlite"></a>SQLite

Se conecta a una base de datos SQLite 3.

### <a name="in-memory"></a>En memoria

Diseñado para habilitar fácilmente la realización de pruebas sin conectarse a una base de datos real.

### <a name="3rd-party-providers"></a>Proveedores de terceros

Existen varios proveedores disponibles para otros motores de base de datos. Para una lista completa, consulte [Proveedores de bases de datos](xref:core/providers/index).
