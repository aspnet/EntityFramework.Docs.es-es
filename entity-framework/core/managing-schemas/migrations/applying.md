---
title: 'Aplicación de migraciones: EF Core'
description: Estrategias para aplicar migraciones de esquema a las bases de datos de desarrollo y de producción con Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 05/06/2020
uid: core/managing-schemas/migrations/applying
ms.openlocfilehash: cde83a944e1e698a7f8a00c4692c0ce08a87b5ab
ms.sourcegitcommit: c0e6a00b64c2dcd8acdc0fe6d1b47703405cdf09
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2020
ms.locfileid: "91210411"
---
# <a name="applying-migrations"></a>Aplicación de migraciones

Una vez agregadas las migraciones, deben implementarse y aplicarse a las bases de datos. Existen varias estrategias para hacerlo, con algunas más adecuadas para entornos de producción y otras para el ciclo de vida de desarrollo.

> [!NOTE]
> Sea cual sea su estrategia de implementación, inspeccione siempre las migraciones generadas y pruébelos antes de aplicarlas a una base de datos de producción. Una migración puede quitar una columna cuando el intento era cambiarle el nombre o puede producir un error por diversos motivos cuando se aplica a una base de datos.

## <a name="sql-scripts"></a>Scripts SQL

La manera recomendada de implementar las migraciones en una base de datos de producción es mediante la generación de scripts SQL. Entre las ventajas de esta estrategia se incluyen las siguientes:

* Se puede revisar la precisión de los scripts SQL; Esto es importante, ya que la aplicación de cambios de esquema en las bases de datos de producción es una operación potencialmente peligrosa que podría implicar la pérdida de datos.
* En algunos casos, los scripts se pueden optimizar para ajustarse a las necesidades específicas de una base de datos de producción.
* Los scripts SQL se pueden usar junto con una tecnología de implementación, y incluso pueden generarse como parte del proceso de CI.
* Se pueden proporcionar scripts SQL a un DBA y se pueden administrar y archivar por separado.

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

#### <a name="basic-usage"></a>Uso básico

Lo siguiente genera un script SQL desde una base de datos vacía a la migración más reciente:

```dotnetcli
dotnet ef migrations script
```

#### <a name="with-from-to-implied"></a>Con From (To implícito)

Lo siguiente genera un script SQL a partir de la migración dada a la última migración.

```dotnetcli
dotnet ef migrations script AddNewTables
```

#### <a name="with-from-and-to"></a>Con From y To

Lo siguiente genera un script SQL a partir de la `from` migración especificada a la `to` migración especificada.

```dotnetcli
dotnet ef migrations script AddNewTables AddAuditTable
```

Puede usar un valor `from` que sea más reciente que el valor `to` para generar un script de reversión.

> [!WARNING]
> Tome nota de los posibles escenarios de pérdida de datos.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

#### <a name="basic-usage"></a>Uso básico

Lo siguiente genera un script SQL desde una base de datos vacía a la migración más reciente:

``` powershell
Script-Migration
```

#### <a name="with-from-to-implied"></a>Con From (To implícito)

Lo siguiente genera un script SQL a partir de la migración dada a la última migración.

```powershell
Script-Migration AddNewTables
```

#### <a name="with-from-and-to"></a>Con From y To

Lo siguiente genera un script SQL a partir de la `from` migración especificada a la `to` migración especificada.

```powershell
Script-Migration AddNewTables AddAuditTable
```
Puede usar un valor `from` que sea más reciente que el valor `to` para generar un script de reversión. *Tome nota de los posibles escenarios de pérdida de datos.*

***

La generación de script acepta los dos argumentos siguientes en indica qué intervalo de migraciones se debe generar:

* La migración **from** debe ser la última migración aplicada a la base de datos antes de ejecutar el script. Si no se han aplicado migraciones, especifique `0` (es el valor predeterminado).
* La migración **to** debe ser la última migración que se va a aplicar a la base de datos después de ejecutar el script. El valor predeterminado es la última migración del proyecto.

## <a name="idempotent-sql-scripts"></a>Scripts SQL idempotentes

Los scripts SQL generados anteriormente solo se pueden aplicar para cambiar el esquema de una migración a otra; es su responsabilidad aplicar el script adecuadamente y solo en la base de datos en el estado de migración correcto. EF Core también admite la generación de scripts **idempotente** , que internamente comprueban qué migraciones ya se han aplicado (a través de la tabla de historial de migraciones) y solo aplican las que faltan. Esto resulta útil si no sabe exactamente cuál fue la última migración aplicada a la base de datos, o si está implementando en varias bases de datos que pueden estar en una migración diferente.

Lo siguiente genera migraciones idempotente:

#### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations script --idempotent
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

``` powershell
Script-Migration -Idempotent
```

***

## <a name="command-line-tools"></a>Herramientas de la línea de comandos

Las herramientas de línea de comandos de EF se pueden usar para aplicar migraciones a una base de datos. Aunque es productivo para el desarrollo y las pruebas locales de las migraciones, este enfoque no es idóneo para la administración de bases de datos de producción:

* Los comandos SQL se aplican directamente a la herramienta, sin que el desarrollador tenga la oportunidad de inspeccionarlos o modificarlos. Esto puede ser peligroso en un entorno de producción.
* El SDK de .NET y la herramienta EF deben instalarse en servidores de producción.

### <a name="net-core-cli"></a>[CLI de .NET Core](#tab/dotnet-core-cli)

La siguiente actualización de la base de datos a la migración más reciente:

```dotnetcli
dotnet ef database update
```

La siguiente actualización de la base de datos a una migración determinada:

```dotnetcli
dotnet ef database update AddNewTables
```

Tenga en cuenta que esto puede usarse también para revertir a una migración anterior.

> [!WARNING]
> Tome nota de los posibles escenarios de pérdida de datos.

### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

La siguiente actualización de la base de datos a la migración más reciente:

``` powershell
Update-Database
```

La siguiente actualización de la base de datos a una migración determinada:

``` powershell
Update-Database AddNewTables
```

Tenga en cuenta que esto puede usarse también para revertir a una migración anterior.

> [!WARNING]
> Tome nota de los posibles escenarios de pérdida de datos.

***

Para obtener más información sobre cómo aplicar migraciones a través de las herramientas de línea de comandos, consulte la [referencia de herramientas de EF Core](xref:core/miscellaneous/cli/index).

## <a name="apply-migrations-at-runtime"></a>Aplicar migraciones en tiempo de ejecución

Es posible que la propia aplicación aplique migraciones mediante programación, normalmente durante el inicio. Aunque es productivo para el desarrollo y las pruebas locales de las migraciones, este enfoque no es apropiado para la administración de bases de datos de producción, por las siguientes razones:

* Si se están ejecutando varias instancias de la aplicación, ambas aplicaciones podrían intentar aplicar la migración simultáneamente y producir un error (o peor, causar daños en los datos).
* Del mismo modo, si una aplicación obtiene acceso a la base de datos mientras otra aplicación la migra, esto puede provocar problemas graves.
* La aplicación debe tener acceso elevado para modificar el esquema de la base de datos. Por lo general, se recomienda limitar los permisos de base de datos de la aplicación en producción.
* Es importante poder revertir una migración aplicada en caso de que se produzca un problema. Las otras estrategias proporcionan esto de forma sencilla y rápida.
* Los comandos SQL los aplica directamente el programa, sin ofrecer al desarrollador una oportunidad de inspeccionarlos o modificarlos. Esto puede ser peligroso en un entorno de producción.

Para aplicar las migraciones mediante programación, llame a `context.Database.Migrate()` . Por ejemplo, una aplicación típica de ASP.NET puede hacer lo siguiente:

```c#
public static void Main(string[] args)
{
    var host = CreateHostBuilder(args).Build();

    using (var scope = host.Services.CreateScope())
    {
        var db = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
        db.Database.Migrate();
    }

    host.Run();
}
```

Tenga en cuenta que `Migrate()` se basa en el `IMigrator` servicio, que se puede usar para escenarios más avanzados. Use `myDbContext.GetInfrastructure().GetService<IMigrator>()` para acceder a él.

> [!WARNING]
>
> * Considere atentamente antes de usar este enfoque en producción. La experiencia ha demostrado que la simplicidad de esta estrategia de implementación se ve compensada por los problemas que crea. Considere la posibilidad de generar scripts SQL a partir de migraciones.
> * No llame a `EnsureCreated()` antes de `Migrate()`. `EnsureCreated()` omite las migraciones para crear el esquema, lo cual provoca un error de `Migrate()`.
