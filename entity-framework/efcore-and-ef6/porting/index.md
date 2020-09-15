---
title: Portabilidad de EF6 a EF Core - EF
description: Información general sobre cómo migrar una aplicación de Entity Framework 6 a Entity Framework Core
author: rowanmiller
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/index
ms.openlocfilehash: 62360dda92d951fe9a4f43a2b742a7457ad8692f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073596"
---
# <a name="porting-from-ef6-to-ef-core"></a>Portabilidad de EF6 a EF Core

Debido a los cambios fundamentales en EF Core, no se recomienda que intente mover una aplicación de EF6 a EF Core, salvo que tenga una razón convincente para hacerlo.
Debe ver la migración de EF6 a EF Core como una portabilidad en lugar de una actualización.

> [!IMPORTANT]
> Antes de comenzar el proceso de portabilidad, es importante validar que EF Core cumple los requisitos de acceso a los datos de la aplicación.

## <a name="missing-features"></a>Características que faltan

Asegúrese de que EF Core tenga todas las características que necesita para usar en la aplicación. Vea [Comparación de características](xref:efcore-and-ef6/index) para obtener una comparación detallada del conjunto de características entre EF Core y EF6. Si faltan algunas características necesarias, asegúrese de que puede compensar la falta de estas características antes de portar a EF Core.

## <a name="behavior-changes"></a>Cambios de comportamiento

Se trata de una lista no exhaustiva de algunos cambios de comportamiento entre EF6 y EF Core. Es importante tener esto en cuenta cuando porte la aplicación, ya que pueden cambiar la forma en que se comporta la aplicación, pero no se mostrarán como errores de compilación después de cambiar a EF Core.

### <a name="dbsetaddattach-and-graph-behavior"></a>DbSet.Add/Attach y comportamiento del grafo

En EF6, llamar `DbSet.Add()` en una entidad provoca una búsqueda recursiva de todas las entidades a las que se hace referencia en sus propiedades de navegación. Las entidades que se encuentran, y a las que el contexto todavía no ha realizado un seguimiento, también se marcarán como agregadas. `DbSet.Attach()` se comporta de la misma forma, salvo que todas las entidades se marcan como sin cambios.

**EF Core realiza una búsqueda recursiva similar, pero con algunas reglas ligeramente diferentes.**

*  La entidad raíz siempre está en el estado de solicitada (agregada para `DbSet.Add` y sin cambios para `DbSet.Attach`).

*  **Para las entidades que se encuentran durante la búsqueda recursiva de propiedades de navegación:**

    *  **Si la clave principal de la entidad se genera en el almacén**

        * Si la clave principal no se establece en un valor, el estado se establece en agregada. El valor de la clave principal se considera "no establecido" si se le asigna el valor predeterminado de CLR para el tipo de propiedad (por ejemplo, `0` para `int`, `null` para `string`, etc.).

        * Si la clave principal no se establece en un valor, el estado se establece en sin cambios.

    *  Si la clave principal no se genera en la base de datos, la entidad se coloca en el mismo estado que la raíz.

### <a name="code-first-database-initialization"></a>Inicialización de la base de datos de Code First

**EF6 tiene cierta magia en torno a la selección de la conexión de base de datos y la inicialización de la base de datos. Algunas de estas reglas incluyen:**

* Si no se realiza ninguna configuración, EF6 seleccionará una base de datos en SQL Express o LocalDb.

* Si una cadena de conexión con el mismo nombre que el contexto está en el archivo `App/Web.config` de las aplicaciones, se usará esta conexión.

* Si la base de datos no existe, se creará.

* Si no existe ninguna de las tablas del modelo en la base de datos, el esquema del modelo actual se agrega a la base de datos. Si se habilitan las migraciones, se usan para crear la base de datos.

* Si la base de datos existe y EF6 ha creado previamente el esquema, entonces se comprueba la compatibilidad de dicho esquema con el modelo actual. Se inicia una excepción si el modelo ha cambiado desde que se creó el esquema.

**EF Core no lleva a cabo nada de esta magia.**

* La conexión de base de datos debe estar explícitamente configurada en el código.

* No se realiza ninguna inicialización. Debe usar `DbContext.Database.Migrate()` para aplicar las migraciones (o `DbContext.Database.EnsureCreated()` y `EnsureDeleted()` para crear o eliminar la base de datos sin usar migraciones).

### <a name="code-first-table-naming-convention"></a>Convención de nomenclatura de tablas de Code First

EF6 ejecuta el nombre de clase de entidad a través de un servicio de pluralización para calcular el nombre de tabla predeterminado al que está asignada la entidad.

EF Core usa el nombre de la propiedad `DbSet` en la que se expone la entidad en el contexto derivado. Si la entidad no tiene una propiedad `DbSet`, se utiliza el nombre de clase.
