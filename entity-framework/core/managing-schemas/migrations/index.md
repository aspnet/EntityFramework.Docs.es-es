---
title: 'Descripción general de las migraciones: EF Core'
description: Información general sobre el uso de migraciones para administrar esquemas de base de datos con Entity Framework Core
author: bricelam
ms.date: 10/28/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: b9547298714af59453aeae6d05742a03c067708b
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2020
ms.locfileid: "95003400"
---
# <a name="migrations-overview"></a>Descripción general de las migraciones

En proyectos reales, los modelos de datos cambian a medida que se implementan características: se agregan o se quitan nuevas entidades o propiedades, y los esquemas de base de datos se deben cambiar según corresponda para mantenerlos sincronizados con la aplicación. La característica de migraciones de EF Core proporciona una manera de actualizar incrementalmente el esquema de la base de datos para mantenerla sincronizada con el modelo de datos de la aplicación al tiempo que se conservan los datos existentes en la base de datos.

A nivel general, las migraciones funcionan de esta forma:

* Cuando se introduce un cambio en el modelo de datos, el desarrollador usa herramientas de EF Core para agregar una migración correspondiente en la que se describan las actualizaciones necesarias para mantener sincronizado el esquema de la base de datos. EF Core compara el modelo actual con una instantánea del modelo anterior para determinar las diferencias y genera los archivos de origen de la migración, de los que se puede realizar el seguimiento en el control de código fuente del proyecto como cualquier otro archivo de código fuente.
* Una vez que se ha generado una migración nueva, haya varias maneras de aplicarla a una base de datos. EF Core registra todas las migraciones aplicadas en una tabla de historial especial, lo que le permite saber qué migraciones se han aplicado y cuáles no.

El resto de esta página es una guía para principiantes paso a paso sobre el uso de las migraciones. Consulte las demás páginas de esta sección para obtener información más detallada.

## <a name="getting-started"></a>Introducción

Imagine que acaba de completar la primera aplicación de EF Core, que contiene el siguiente modelo simple:

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

Durante el desarrollo, es posible que haya usado las [API Create y Drop](xref:core/managing-schemas/ensure-created) para realizar una iteración rápida y cambiar el modelo según las necesidades, pero ahora que la aplicación se destina a producción, necesita una manera de desarrollar de forma segura el esquema sin quitar la base de datos completa.

### <a name="install-the-tools"></a>Instalar las herramientas

En primer lugar, tendrá que instalar las [herramientas de línea de comandos de EF Core](xref:core/cli/index):

* Por lo general, se recomienda usar las [herramientas de la CLI de .NET Core](xref:core/cli/dotnet), que funcionan en todas las plataformas.
* Si se siente más cómodo trabajando en Visual Studio o tiene experiencia con las migraciones de EF6, también puede usar las [ herramientas de la consola del administrador de paquetes](xref:core/cli/powershell).

### <a name="create-your-first-migration"></a>Creación de la primera migración

Ya está listo para agregar la primera migración. Indique a EF Core que cree una migración llamada **InitialCreate**:

#### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate
```

**_

EF Core creará un directorio denominado _ *Migrations** (Migraciones) en el proyecto y generará varios archivos. Es recomendable inspeccionar lo que EF Core ha generado exactamente y, posiblemente, rectificarlo, pero este paso se omitirá por ahora.

### <a name="create-your-database-and-schema"></a>Creación de la base de datos y el esquema

En este momento puede hacer que EF cree la base de datos y el esquema a partir de la migración. Esto también se puede hacer mediante:

#### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Update-Database
```

**_

Eso es todo: la aplicación está lista para ejecutarse en la base de datos nueva y no es necesario escribir una sola línea de SQL. Tenga en cuenta que esta manera de aplicar migraciones resulta idónea para el desarrollo local, pero es menos adecuada para los entornos de producción; vea la página [Aplicación de migraciones](xref:core/managing-schemas/migrations/applying) para obtener más información.

### <a name="evolving-your-model"></a>Evolución del modelo

Han pasado unos días y le piden que agregue una marca de tiempo de creación a los blogs. Ha realizado los cambios necesarios en la aplicación y el ahora modelo tiene este aspecto:

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

Ahora el modelo y la base de datos de producción están desincronizados; tendrá que agregar una nueva columna al esquema de la base de datos. Cree una migración para esto:

#### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

_*_

Tenga en cuenta que a las migraciones se les proporciona un nombre descriptivo para que después sea más fácil entender el historial del proyecto.

Como no se trata de la primera migración del proyecto, ahora EF Core compara el modelo actualizado con una instantánea del modelo anterior, antes de que se agregara la columna; la instantánea del modelo es uno de los archivos generados por EF Core cuando se agrega una migración y se inserta en el control de código fuente. En función de esa comparación, EF Core detecta que se ha agregado una columna y agrega la migración adecuada.

Ahora puede aplicar la migración como antes:

<!--markdownlint-disable MD024-->

#### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Update-Database
```

<!--markdownlint-enable MD024-->

_*_

Tenga en cuenta que, en esta ocasión, EF detecta que la base de datos ya existe. Además, cuando antes se ha aplicado la primera migración, esta operación se ha registrado en una tabla de historial de migraciones especial en la base de datos, lo que permite que EF solo aplique de forma automática la nueva migración.

### <a name="excluding-parts-of-your-model"></a>Exclusión de elementos del modelo

> [!NOTE]
> Esta característica se incluyó por primera vez en EF Core 5.0.

En ocasiones, es posible que quiera consultar tipos de otro DbContext. Esto puede dar lugar a conflictos de migración. Para evitarlo, excluya el tipo de las migraciones de uno de los elementos DbContext.

[!code-csharp[](../../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs#TableExcludeFromMigrations)]

### <a name="next-steps"></a>Pasos siguientes

Lo anterior solo era una breve introducción a las migraciones. Consulte las demás páginas de documentación para obtener más información sobre cómo [administrar migraciones](xref:core/managing-schemas/migrations/managing), [aplicarlas](xref:core/managing-schemas/migrations/applying) y otros aspectos. La [referencia de herramientas de la CLI de .NET Core](xref:core/cli/index) también contiene información útil sobre los distintos comandos

## <a name="additional-resources"></a>Recursos adicionales

_ [Sesión de Reunión de la comunidad de EF Core](https://www.youtube.com/watch?v=mSsGERmrhnE&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=20) donde se analizan las nuevas características de migración de EF Core 5.0.
