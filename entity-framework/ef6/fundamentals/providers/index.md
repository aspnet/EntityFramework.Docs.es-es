---
title: 'Proveedores de Entity Framework: EF6'
description: Proveedores de Entity Framework en Entity Framework 6
author: divega
ms.date: 06/27/2018
ms.assetid: 7BFB7763-CD6C-4520-93A2-7B265F5FA586
uid: ef6/fundamentals/providers/index
ms.openlocfilehash: 5140fefeb118b6e70ac27ab010d813d03ec7000c
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89615877"
---
# <a name="entity-framework-6-providers"></a>Proveedores de Entity Framework 6
> [!NOTE]
> **Solo EF6 y versiones posteriores**: las características, las API, etc. que se tratan en esta página se han incluido a partir de Entity Framework 6. Si usa una versión anterior, no se aplica parte o la totalidad de la información.

Entity Framework ahora se desarrolla bajo una licencia de código abierto, por lo que EF6 y versiones posteriores no se van a incluir como parte de .NET Framework. Esto ofrece muchas ventajas, pero también exige que los proveedores de EF se vuelvan a compilar en los ensamblados de EF6. Esto significa que los proveedores de EF para EF5 y versiones anteriores no funcionarán con EF6 hasta que se vuelvan a compilar.

## <a name="which-providers-are-available-for-ef6"></a>Proveedores disponibles para EF6

Los proveedores conocidos que se han vuelto a compilar para EF6 incluyen:

*   **Proveedor de Microsoft SQL Server**
    *   Compilado a partir del [código base fuente abierto de Entity Framework](https://github.com/aspnet/EntityFramework6)
    *   Incluido como parte del [paquete NuGet de Entity Framework](https://nuget.org/packages/EntityFramework)
*   **Proveedor de Microsoft SQL Server Compact Edition**
    *   Compilado a partir del [código base fuente abierto de Entity Framework](https://github.com/aspnet/EntityFramework6)
    *   Incluido en el [paquete NuGet de EntityFramework.SqlServerCompact](https://nuget.org/packages/EntityFramework.SqlServerCompact)
*   [**Proveedores de datos de Devart dotConnect**](https://www.devart.com/dotconnect/)
    *   Existen proveedores de terceros de [Devart](https://www.devart.com/) para una serie de bases de datos como Oracle, MySQL, PostgreSQL, SQLite, Salesforce, DB2 y SQL Server
*   [**Proveedores de CData Software**](https://www.cdata.com/ado/)
    *   Existen proveedores de terceros de [CData Software](https://www.cdata.com/ado/) para una serie de almacenes de datos como Salesforce, Azure Table Storage, MySql, etc.
*   **Proveedor de Firebird**
    *   Disponible como [paquete NuGet](https://www.nuget.org/packages/EntityFramework.Firebird/)
*   **Proveedor de Visual Fox Pro**
    *   Disponible como [paquete NuGet](https://www.nuget.org/packages/VFPEntityFrameworkProvider2/)
*   **MySQL**
    *   [Conector MySQL/NET para Entity Framework](https://dev.mysql.com/doc/connector-net/en/connector-net-entityframework60.html)
*   **PostgreSQL**
    *   Npgsql está disponible como [paquete NuGet](https://www.nuget.org/packages/EntityFramework6.Npgsql/)
*   **Oracle**
    *   ODP.NET está disponible como [paquete NuGet](https://www.nuget.org/packages/Oracle.ManagedDataAccess.EntityFramework/)

Tenga en cuenta que la inclusión en esta lista no indica el nivel de funcionalidad o compatibilidad de un proveedor determinado, solo que hay una compilación para EF6 disponible.

## <a name="registering-ef-providers"></a>Registro de proveedores de EF

A partir de Entity Framework 6, los proveedores de EF se pueden registrar mediante configuración basada en código o en el archivo de configuración de la aplicación.

### <a name="config-file-registration"></a>Registro en el archivo de configuración

El registro del proveedor de EF en app.config o web.config tiene el formato siguiente:


``` xml
    <entityFramework>
       <providers>
         <provider invariantName="My.Invariant.Name" type="MyProvider.MyProviderServices, MyAssembly" />
       </providers>
    </entityFramework>
```

Tenga en cuenta que, a menudo, si el proveedor de EF se instala desde NuGet, el paquete NuGet agrega automáticamente este registro al archivo de configuración. Si instala el paquete NuGet en un proyecto que no es el proyecto de inicio de la aplicación, es posible que tenga que copiar el registro en el archivo de configuración del proyecto de inicio.

El elemento "invariantName" de este registro es el mismo nombre invariable usado para identificar a un proveedor de ADO.NET. Se puede encontrar como atributo "invariant" en un registro DbProviderFactories y como atributo "providerName" en un registro de cadena de conexión. El nombre invariable que se va a usar también debe incluirse en la documentación del proveedor. Ejemplos de nombres invariables son "System.Data.SqlClient" para SQL Server y "System.Data.SqlServerCe.4.0" para SQL Server Compact.

El elemento "type" de este registro es el nombre calificado con el ensamblado del tipo de proveedor que se deriva de "System.Data.Entity.Core.Common.DbProviderServices". Por ejemplo, la cadena que se va a usar para SQL Compact es "System.Data.Entity.SqlServerCompact.SqlCeProviderServices, EntityFramework.SqlServerCompact". El tipo que se va a usar aquí debe incluirse en la documentación del proveedor.

### <a name="code-based-registration"></a>Registro basado en código

A partir de Entity Framework 6, la configuración de la aplicación de EF puede especificarse en el código. Para obtener todos los detalles, vea _[Entity Framework Code-Based Configuration](https://msdn.microsoft.com/data/jj680699)_ (Configuración basada en código de Entity Framework). La forma habitual de registrar un proveedor de EF mediante configuración basada en código es crear una nueva clase que se derive de System.Data.Entity.DbConfiguration y colocarla en el mismo ensamblado que la clase DbContext. Luego la clase DbConfiguration debe registrar el proveedor en su constructor. Por ejemplo, para registrar el proveedor de SQL Compact, la clase DbConfiguration tiene este aspecto:

``` csharp
    public class MyConfiguration : DbConfiguration
    {
        public MyConfiguration()
        {
            SetProviderServices(
                SqlCeProviderServices.ProviderInvariantName,
                SqlCeProviderServices.Instance);
        }
    }
```

En este código, "SqlCeProviderServices.ProviderInvariantName" es una comodidad de la cadena de nombre invariable del proveedor de SQL Server Compact ("System.Data.SqlServerCe.4.0") y SqlCeProviderServices.Instance devuelve la instancia singleton del proveedor de EF de SQL Compact.

## <a name="what-if-the-provider-i-need-isnt-available"></a>¿Qué ocurre si el proveedor que necesito no está disponible?

Si el proveedor está disponible para versiones anteriores de EF, se le anima a ponerse en contacto con el propietario del proveedor y pedirle que cree una versión de EF6. Debe incluir una referencia a la [documentación para el modelo de proveedor de EF6](xref:ef6/fundamentals/providers/provider-model).

## <a name="can-i-write-a-provider-myself"></a>¿Puedo escribir un proveedor por mí mismo?

Por supuesto que es posible crear un proveedor de EF por sí mismo, aunque no se debe considerar una tarea trivial. El vínculo anterior sobre el modelo de proveedor de EF6 es un buen punto de partida. También le puede resultar útil usar el código para el proveedor de SQL Server y SQL CE incluido en el [código base fuente abierto de EF](https://github.com/aspnet/EntityFramework6) como punto de partida o como referencia.

Tenga en cuenta que a partir de EF6, el proveedor de EF está menos estrechamente ligado al proveedor de ADO.NET subyacente. Esto facilita la escritura de un proveedor de EF sin necesidad de escribir o ajustar las clases de ADO.NET.
