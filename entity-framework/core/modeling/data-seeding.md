---
title: 'Propagación de datos: EF Core'
description: Usar la inicialización de datos para rellenar una base de datos con un conjunto inicial de datos mediante Entity Framework Core
author: AndriySvyryd
ms.date: 11/02/2018
uid: core/modeling/data-seeding
ms.openlocfilehash: 812d64ff2455e90436fc8c41e27f28115e207ff3
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103024048"
---
# <a name="data-seeding"></a>Propagación de datos

La propagación de datos es el proceso de rellenar una base de datos con un conjunto inicial de datos.

Hay varias maneras de lograrlo en EF Core:

* Datos de inicialización del modelo
* Personalización de la migración manual
* Lógica de inicialización personalizada

## <a name="model-seed-data"></a>Datos de inicialización del modelo

A diferencia de EF6, en EF Core, la propagación de datos se puede asociar a un tipo de entidad como parte de la configuración del modelo. A continuación, las [migraciones](xref:core/managing-schemas/migrations/index) de EF Core pueden calcular automáticamente las operaciones de inserción, actualización o eliminación que se deben aplicar al actualizar la base de datos a una nueva versión del modelo.

> [!NOTE]
> Las migraciones solo tienen en cuenta los cambios del modelo al determinar qué operación se debe realizar para obtener los datos de inicialización en el estado deseado. Por lo tanto, es posible que se pierdan los cambios realizados en los datos fuera de las migraciones o se produzca un error.

Como ejemplo, se configurarán los datos de inicialización de un `Blog` en `OnModelCreating` :

[!code-csharp[BlogSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=BlogSeed)]

Para agregar entidades que tienen una relación, es necesario especificar los valores de clave externa:

[!code-csharp[PostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=PostSeed)]

Si el tipo de entidad tiene propiedades en el estado de sombra, se puede usar una clase anónima para proporcionar los valores:

[!code-csharp[AnonymousPostSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=AnonymousPostSeed)]

Los tipos de entidad de propiedad se pueden inicializar de una manera similar:

[!code-csharp[OwnedTypeSeed](../../../samples/core/Modeling/DataSeeding/DataSeedingContext.cs?name=OwnedTypeSeed)]

Vea el [proyecto de ejemplo completo](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/Modeling/DataSeeding) para obtener más contexto.

Una vez que se han agregado los datos al modelo, se deben usar las [migraciones](xref:core/managing-schemas/migrations/index) para aplicar los cambios.

> [!TIP]
> Si necesita aplicar las migraciones como parte de una implementación automatizada, puede [crear un script SQL](xref:core/managing-schemas/migrations/index#generate-sql-scripts) que se pueda obtener como vista previa antes de la ejecución.

Como alternativa, puede usar `context.Database.EnsureCreated()` para crear una nueva base de datos que contenga los datos de inicialización, por ejemplo, para una base de datos de prueba o cuando se usa el proveedor en memoria o cualquier base de datos que no sea de relación. Tenga en cuenta que si la base de datos ya existe, no `EnsureCreated()` actualizará el esquema ni los datos de inicialización en la base de datos. En el caso de las bases de datos relacionales, no debe llamar a `EnsureCreated()` si planea usar las migraciones.

### <a name="limitations-of-model-seed-data"></a>Limitaciones de los datos de inicialización del modelo

Este tipo de datos de inicialización se administra mediante migraciones y el script para actualizar los datos que ya están en la base de datos debe generarse sin necesidad de conectarse a la base de datos. Esto impone algunas restricciones:

* El valor de clave principal debe especificarse incluso si la base de datos lo genera normalmente. Se usará para detectar los cambios de datos entre las migraciones.
* Los datos previamente inicializados se quitarán si se cambia la clave principal de cualquier manera.

Por lo tanto, esta característica es muy útil para los datos estáticos que no se espera que cambien fuera de las migraciones y no depende de nada más en la base de datos, por ejemplo códigos postales.

Si el escenario incluye alguno de los siguientes, se recomienda usar la lógica de inicialización personalizada que se describe en la última sección:

* Datos temporales para pruebas
* Datos que dependen del estado de la base de datos
* Los datos que son grandes (la propagación de datos se capturan en instantáneas de migración, y los datos grandes pueden conducir rápidamente a grandes archivos y a un rendimiento degradado).
* Datos que necesitan que la base de datos genere valores clave, incluidas las entidades que usan claves alternativas como identidad.
* Datos que requieren una transformación personalizada (que no se controlan mediante [conversiones de valores](xref:core/modeling/value-conversions)), como algunas operaciones hash de contraseñas.
* Datos que requieren llamadas a la API externa, como ASP.NET Core roles de identidad y la creación de usuarios

## <a name="manual-migration-customization"></a>Personalización de la migración manual

Cuando se agrega una migración, los cambios en los datos especificados con `HasData` se transforman en llamadas a `InsertData()` , `UpdateData()` y `DeleteData()` . Una manera de resolver algunas de las limitaciones de `HasData` es agregar manualmente estas llamadas o [operaciones personalizadas](xref:core/managing-schemas/migrations/operations) a la migración.

[!code-csharp[CustomInsert](../../../samples/core/Modeling/DataSeeding/Migrations/20181102235626_Initial.cs?name=CustomInsert)]

## <a name="custom-initialization-logic"></a>Lógica de inicialización personalizada

Una manera sencilla y eficaz de realizar la propagación de datos es usar [`DbContext.SaveChanges()`](xref:core/saving/index) antes de que la lógica de la aplicación principal comience la ejecución.

[!code-csharp[Main](../../../samples/core/Modeling/DataSeeding/Program.cs?name=CustomSeeding)]

> [!WARNING]
> El código de propagación no debe formar parte de la ejecución normal de la aplicación, ya que esto puede provocar problemas de simultaneidad cuando se ejecutan varias instancias y también requeriría que la aplicación tuviera permiso para modificar el esquema de la base de datos.

En función de las restricciones de la implementación, el código de inicialización se puede ejecutar de diferentes maneras:

* Ejecución local de la aplicación de inicialización
* Implementar la aplicación de inicialización con la aplicación principal, invocar la rutina de inicialización y deshabilitar o quitar la aplicación de inicialización.

Normalmente, esto se puede automatizar mediante el uso de [perfiles de publicación](/aspnet/core/host-and-deploy/visual-studio-publish-profiles).
