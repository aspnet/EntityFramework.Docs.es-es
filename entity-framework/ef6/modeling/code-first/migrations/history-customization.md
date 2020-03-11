---
title: 'Personalización de la tabla de historial de migraciones: EF6'
author: divega
ms.date: 10/23/2016
ms.assetid: ed5518f0-a9a6-454e-9e98-a4fa7748c8d0
ms.openlocfilehash: eb19f367611a86f685557a6741a5f2f0bad6b718
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415686"
---
# <a name="customizing-the-migrations-history-table"></a>Personalización de la tabla de historial de migraciones
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

> [!NOTE]
> En este artículo se supone que sabe cómo usar Migraciones de Code First en escenarios básicos. Si no lo hace, tendrá que leer [migraciones de Code First](~/ef6/modeling/code-first/migrations/index.md) antes de continuar.

## <a name="what-is-migrations-history-table"></a>¿Qué es la tabla de historial de migraciones?

La tabla de historial de migraciones es una tabla que usa Migraciones de Code First para almacenar detalles sobre las migraciones que se aplican a la base de datos. De forma predeterminada, el nombre de la tabla de la base de datos se \_\_MigrationHistory y se crea al aplicar la primera migración a la base de datos. En Entity Framework 5, esta tabla era una tabla del sistema si la aplicación usaba la base de datos de Microsoft SQL Server. No obstante, esto ha cambiado en Entity Framework 6 y la tabla de historial de migraciones ya no está marcada como una tabla del sistema.

## <a name="why-customize-migrations-history-table"></a>¿Por qué personalizar la tabla de historial de migraciones?

La tabla de historial de migraciones se supone que se usará únicamente en Migraciones de Code First y cambiarla manualmente puede interrumpir las migraciones. Sin embargo, a veces la configuración predeterminada no es adecuada y la tabla debe personalizarse, por ejemplo:

-   Debe cambiar los nombres y/o las caras de las columnas para habilitar un proveedor de migraciones<sup>de terceras partes.</sup>
-   Desea cambiar el nombre de la tabla
-   Debe usar un esquema no predeterminado para la \_\_tabla MigrationHistory
-   Necesita almacenar datos adicionales para una versión determinada del contexto y, por tanto, necesita agregar una columna adicional a la tabla.

## <a name="words-of-precaution"></a>Palabras de precaución

Cambiar la tabla de historial de migración es eficaz, pero debe tener cuidado de no abusar. En la actualidad, el tiempo de ejecución de EF no comprueba si la tabla de historial de migraciones personalizada es compatible con el tiempo de ejecución. Si no es así, es posible que la aplicación se interrumpa en tiempo de ejecución o se comporte de maneras imprevisibles. Esto es aún más importante si usa varios contextos por base de datos, en cuyo caso varios contextos pueden usar la misma tabla de historial de migración para almacenar información acerca de las migraciones.

## <a name="how-to-customize-migrations-history-table"></a>¿Cómo se personaliza la tabla de historial de migraciones?

Antes de empezar, debe saber que solo puede personalizar la tabla de historial de migraciones antes de aplicar la primera migración. Ahora, en el código.

En primer lugar, tendrá que crear una clase derivada de la clase System. Data. Entity. Migrations. History. HistoryContext. La clase HistoryContext se deriva de la clase DbContext, por lo que la configuración de la tabla de historial de migraciones es muy similar a la configuración de modelos EF con la API fluida. Solo tiene que invalidar el método OnModelCreating y usar la API fluida para configurar la tabla.

>[!NOTE]
> Normalmente, cuando se configuran modelos EF no es necesario llamar a base. OnModelCreating () del método OnModelCreating invalidado, ya que DbContext. OnModelCreating () tiene un cuerpo vacío. Este no es el caso cuando se configura la tabla de historial de migraciones. En este caso, lo primero que hay que hacer en la invalidación de OnModelCreating () es, en realidad, llamar a base. OnModelCreating (). De esta forma, se configurará la tabla de historial de migraciones de la manera predeterminada que se va a ajustar en el método de reemplazo.

Supongamos que desea cambiar el nombre de la tabla de historial de migraciones y colocarla en un esquema personalizado denominado "admin". Además, los DBA le gustaría cambiar el nombre de la columna MigrationId al identificador de\_de migración.  Para ello, cree la clase siguiente derivada de HistoryContext:

``` csharp
    using System.Data.Common;
    using System.Data.Entity;
    using System.Data.Entity.Migrations.History;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class MyHistoryContext : HistoryContext
        {
            public MyHistoryContext(DbConnection dbConnection, string defaultSchema)
                : base(dbConnection, defaultSchema)
            {
            }

            protected override void OnModelCreating(DbModelBuilder modelBuilder)
            {
                base.OnModelCreating(modelBuilder);
                modelBuilder.Entity<HistoryRow>().ToTable(tableName: "MigrationHistory", schemaName: "admin");
                modelBuilder.Entity<HistoryRow>().Property(p => p.MigrationId).HasColumnName("Migration_ID");
            }
        }
    }
```

Una vez que el HistoryContext personalizado esté listo, debe hacer que EF lo tenga al registrarlo mediante [la configuración basada en código](https://msdn.com/data/jj680699):

``` csharp
    using System.Data.Entity;

    namespace CustomizableMigrationsHistoryTableSample
    {
        public class ModelConfiguration : DbConfiguration
        {
            public ModelConfiguration()
            {
                this.SetHistoryContext("System.Data.SqlClient",
                    (connection, defaultSchema) => new MyHistoryContext(connection, defaultSchema));
            }
        }
    }
```

Eso es prácticamente todo. Ahora puede ir a la consola del administrador de paquetes, habilitar-migraciones, agregar-migrar y, finalmente, actualizar-base de datos. Esto debería hacer que se agregue a la base de datos una tabla de historial de migraciones configurada según los detalles especificados en la clase derivada HistoryContext.

![Base de datos](~/ef6/media/database.png)
