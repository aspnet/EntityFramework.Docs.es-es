---
title: 'Operaciones de migración personalizadas: EF Core'
description: Administrar migraciones de SQL personalizadas y sin formato para la administración de esquemas de base de datos con Entity Framework Core
author: bricelam
ms.author: bricelam
ms.date: 11/07/2017
uid: core/managing-schemas/migrations/operations
ms.openlocfilehash: 708894d8d567a4644be3a4ace98cc837465710e0
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617956"
---
# <a name="custom-migrations-operations"></a>Operaciones de migración personalizadas

La API de MigrationBuilder permite realizar muchos tipos diferentes de operaciones durante una migración, pero está lejos de ser exhaustiva. Sin embargo, la API también es extensible, lo que le permite definir sus propias operaciones. Hay dos maneras de extender la API: mediante el `Sql()` método o mediante la definición de `MigrationOperation` objetos personalizados.

Para ilustrar, echemos un vistazo a la implementación de una operación que crea un usuario de base de datos mediante cada enfoque. En nuestras migraciones, queremos habilitar la escritura del código siguiente:

``` csharp
migrationBuilder.CreateUser("SQLUser1", "Password");
```

## <a name="using-migrationbuildersql"></a>Usar MigrationBuilder. SQL ()

La forma más fácil de implementar una operación personalizada es definir un método de extensión que llame a `MigrationBuilder.Sql()` . Este es un ejemplo que genera el correspondiente Transact-SQL.

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
    => migrationBuilder.Sql($"CREATE USER {name} WITH PASSWORD '{password}';");
```

Si las migraciones necesitan admitir varios proveedores de bases de datos, puede utilizar la `MigrationBuilder.ActiveProvider` propiedad. Este es un ejemplo que admite tanto Microsoft SQL Server como PostgreSQL.

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    switch (migrationBuilder.ActiveProvider)
    {
        case "Npgsql.EntityFrameworkCore.PostgreSQL":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD '{password}';");

        case "Microsoft.EntityFrameworkCore.SqlServer":
            return migrationBuilder
                .Sql($"CREATE USER {name} WITH PASSWORD = '{password}';");
    }

    return migrationBuilder;
}
```

Este enfoque solo funciona si conoce todos los proveedores en los que se va a aplicar la operación personalizada.

## <a name="using-a-migrationoperation"></a>Uso de un MigrationOperation

Para desacoplar la operación personalizada de SQL, puede definir la suya propia `MigrationOperation` para representarla. A continuación, la operación se pasa al proveedor para que pueda determinar el SQL adecuado que se va a generar.

``` csharp
class CreateUserOperation : MigrationOperation
{
    public string Name { get; set; }
    public string Password { get; set; }
}
```

Con este enfoque, el método de extensión solo tiene que agregar una de estas operaciones a `MigrationBuilder.Operations` .

``` csharp
static MigrationBuilder CreateUser(
    this MigrationBuilder migrationBuilder,
    string name,
    string password)
{
    migrationBuilder.Operations.Add(
        new CreateUserOperation
        {
            Name = name,
            Password = password
        });

    return migrationBuilder;
}
```

Este enfoque requiere que cada proveedor sepa cómo generar SQL para esta operación en su `IMigrationsSqlGenerator` servicio. Este es un ejemplo invalidando el generador del SQL Server para administrar la nueva operación.

``` csharp
class MyMigrationsSqlGenerator : SqlServerMigrationsSqlGenerator
{
    public MyMigrationsSqlGenerator(
        MigrationsSqlGeneratorDependencies dependencies,
        IMigrationsAnnotationProvider migrationsAnnotations)
        : base(dependencies, migrationsAnnotations)
    {
    }

    protected override void Generate(
        MigrationOperation operation,
        IModel model,
        MigrationCommandListBuilder builder)
    {
        if (operation is CreateUserOperation createUserOperation)
        {
            Generate(createUserOperation, builder);
        }
        else
        {
            base.Generate(operation, model, builder);
        }
    }

    private void Generate(
        CreateUserOperation operation,
        MigrationCommandListBuilder builder)
    {
        var sqlHelper = Dependencies.SqlGenerationHelper;
        var stringMapping = Dependencies.TypeMappingSource.FindMapping(typeof(string));

        builder
            .Append("CREATE USER ")
            .Append(sqlHelper.DelimitIdentifier(operation.Name))
            .Append(" WITH PASSWORD = ")
            .Append(stringMapping.GenerateSqlLiteral(operation.Password))
            .AppendLine(sqlHelper.StatementTerminator)
            .EndCommand();
    }
}
```

Reemplace el servicio de generador de SQL de migraciones predeterminado por el actualizado.

``` csharp
protected override void OnConfiguring(DbContextOptionsBuilder options)
    => options
        .UseSqlServer(connectionString)
        .ReplaceService<IMigrationsSqlGenerator, MyMigrationsSqlGenerator>();
```
