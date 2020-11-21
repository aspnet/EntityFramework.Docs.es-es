---
title: 'Relaciones: EF Core'
description: Cómo configurar relaciones entre tipos de entidad al utilizar Entity Framework Core
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/relationships
ms.openlocfilehash: 716c034bd73d831996b727da18c2c1f83dd55290
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003268"
---
# <a name="relationships"></a>Relaciones

Una relación define el modo en que dos entidades se relacionan entre sí. En una base de datos relacional, se representa mediante una restricción FOREIGN KEY.

> [!NOTE]  
> La mayoría de los ejemplos de este artículo usan una relación de uno a varios para demostrar los conceptos. Para obtener ejemplos de relaciones de uno a uno y de varios a varios, consulte la sección [otros patrones de relación](#other-relationship-patterns) al final del artículo.

## <a name="definition-of-terms"></a>Definición de términos

Hay una serie de términos que se usan para describir las relaciones

* **Entidad dependiente:** Esta es la entidad que contiene las propiedades de clave externa. A veces se conoce como "secundario" de la relación.

* **Entidad de entidad** de seguridad: Esta es la entidad que contiene las propiedades de clave principal/alternativa. A veces se denomina "primario" de la relación.

* **Clave principal:** Propiedades que identifican de forma única la entidad principal. Puede ser la clave principal o una clave alternativa.

* **Clave externa:** Propiedades de la entidad dependiente que se usan para almacenar los valores de clave principal para la entidad relacionada.

* **Propiedad de navegación:** Propiedad definida en la entidad principal o dependiente que hace referencia a la entidad relacionada.

  * **Propiedad de navegación de colección:** Propiedad de navegación que contiene referencias a muchas entidades relacionadas.

  * **Propiedad de navegación de referencia:** Propiedad de navegación que contiene una referencia a una sola entidad relacionada.

  * **Propiedad de navegación inversa:** Al discutir una propiedad de navegación determinada, este término hace referencia a la propiedad de navegación en el otro extremo de la relación.
  
* **Relación que hace referencia a sí misma:** Una relación en la que los tipos de entidad dependiente y principal son iguales.

En el código siguiente se muestra una relación de uno a varios entre `Blog` y `Post`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* `Post` es la entidad dependiente.

* `Blog` es la entidad principal

* `Blog.BlogId` es la clave principal (en este caso, es una clave principal en lugar de una clave alternativa)

* `Post.BlogId` es la clave externa

* `Post.Blog` propiedad de navegación de referencia

* `Blog.Posts` es una propiedad de navegación de colección

* `Post.Blog` es la propiedad de navegación inversa de `Blog.Posts` (y viceversa).

## <a name="conventions"></a>Convenciones

De forma predeterminada, se creará una relación cuando se detecte una propiedad de navegación en un tipo. Una propiedad se considera una propiedad de navegación si el tipo al que señala no se puede asignar como un tipo escalar por el proveedor de base de datos actual.

> [!NOTE]  
> Las relaciones detectadas por la Convención siempre tendrán como destino la clave principal de la entidad principal. Para elegir como destino una clave alternativa, se debe realizar una configuración adicional mediante la API fluida.

### <a name="fully-defined-relationships"></a>Relaciones totalmente definidas

El patrón más común para las relaciones es tener propiedades de navegación definidas en ambos extremos de la relación y una propiedad de clave externa definida en la clase de entidad dependiente.

* Si se encuentra un par de propiedades de navegación entre dos tipos, se configurarán como propiedades de navegación inversa de la misma relación.

* Si la entidad dependiente contiene una propiedad con un nombre que coincide con uno de estos patrones, se configurará como la clave externa:
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

En este ejemplo, las propiedades resaltadas se usarán para configurar la relación.

> [!NOTE]
> Si la propiedad es la clave principal o es de un tipo no compatible con la clave principal, no se configurará como clave externa.

> [!NOTE]
> Antes de EF Core 3,0 la propiedad denominada exactamente igual que la propiedad de clave principal [también coincidía con la clave externa](https://github.com/dotnet/efcore/issues/13274) .

### <a name="no-foreign-key-property"></a>No hay propiedad de clave externa

Aunque se recomienda tener una propiedad de clave externa definida en la clase de entidad dependiente, no es necesario. Si no se encuentra ninguna propiedad de clave externa, se introducirá una [propiedad de clave externa de sombra](xref:core/modeling/shadow-properties) con el nombre `<navigation property name><principal key property name>` o `<principal entity name><principal key property name>` si no hay ninguna navegación presente en el tipo dependiente.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

En este ejemplo, la clave externa de la sombra se debe a que, si `BlogId` se antepone el nombre de navegación, sería redundante.

> [!NOTE]
> Si ya existe una propiedad con el mismo nombre, el nombre de la propiedad Shadow tendrá como sufijo un número.

### <a name="single-navigation-property"></a>Propiedad de navegación única

Incluir solo una propiedad de navegación (sin navegación inversa y sin propiedad de clave externa) es suficiente para tener una relación definida por Convención. También puede tener una propiedad de navegación única y una propiedad de clave externa.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a>Limitaciones

Cuando hay varias propiedades de navegación definidas entre dos tipos (es decir, más de un par de navegaciones que apuntan entre sí), las relaciones representadas por las propiedades de navegación son ambiguas. Tendrá que configurarlos manualmente para resolver la ambigüedad.

### <a name="cascade-delete"></a>Eliminación en cascada

Por Convención, la eliminación en cascada se establecerá en *Cascade* para las relaciones necesarias y *ClientSetNull* para las relaciones opcionales. *Cascade* significa que las entidades dependientes también se eliminan. *ClientSetNull* significa que las entidades dependientes que no se cargan en la memoria permanecerán sin cambios y deben eliminarse manualmente o actualizarse para que apunten a una entidad principal válida. En el caso de las entidades que se cargan en memoria, EF Core intentará establecer las propiedades de clave externa en NULL.

Vea la sección [relaciones obligatorias y opcionales](#required-and-optional-relationships) para ver la diferencia entre las relaciones obligatorias y opcionales.

Consulte [eliminación en cascada](xref:core/saving/cascade-delete) para obtener más detalles sobre los distintos comportamientos de eliminación y los valores predeterminados que usa la Convención.

## <a name="manual-configuration"></a>Configuración manual

### <a name="fluent-api"></a>[API fluida](#tab/fluent-api)

Para configurar una relación en la API fluida, empiece por identificar las propiedades de navegación que componen la relación. `HasOne` o `HasMany` identifica la propiedad de navegación en el tipo de entidad en el que va a comenzar la configuración. A continuación, encadenar una llamada a `WithOne` o `WithMany` para identificar la navegación inversa. `HasOne`/`WithOne`se utilizan para las propiedades de navegación de referencia y `HasMany` / `WithMany` se utilizan para las propiedades de navegación de colección.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[Anotaciones de datos](#tab/data-annotations)

Puede usar las anotaciones de datos para configurar cómo se emparejan las propiedades de navegación en las entidades de entidad de seguridad y dependencias. Normalmente, esto se hace cuando hay más de un par de propiedades de navegación entre dos tipos de entidad.

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> Solo puede utilizar [required] en las propiedades de la entidad dependiente para afectar a la necesidad de la relación. [Obligatorio] en la navegación de la entidad principal normalmente se omite, pero puede hacer que la entidad se convierta en la dependiente.

> [!NOTE]
> Las anotaciones de datos `[ForeignKey]` y `[InverseProperty]` están disponibles en el `System.ComponentModel.DataAnnotations.Schema` espacio de nombres. `[Required]` está disponible en el `System.ComponentModel.DataAnnotations` espacio de nombres.

---

### <a name="single-navigation-property"></a>Propiedad de navegación única

Si solo tiene una propiedad de navegación, hay sobrecargas sin parámetros de `WithOne` y `WithMany` . Esto indica que hay conceptualmente una referencia o una colección en el otro extremo de la relación, pero no hay ninguna propiedad de navegación incluida en la clase de entidad.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a>Configurar propiedades de navegación

> [!NOTE]
> Esta característica se presentó en EF Core 5,0.

Una vez creada la propiedad de navegación, puede que necesite configurarla más adelante.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> Esta llamada no se puede usar para crear una propiedad de navegación. Solo se usa para configurar una propiedad de navegación que se ha creado previamente definiendo una relación o una Convención.

### <a name="foreign-key"></a>Clave externa

#### <a name="fluent-api-simple-key"></a>[API fluida (clave simple)](#tab/fluent-api-simple-key)

Puede usar la API fluida para configurar qué propiedad se debe usar como la propiedad de clave externa para una relación determinada:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[API fluida (clave compuesta)](#tab/fluent-api-composite-key)

Puede usar la API fluida para configurar qué propiedades deben usarse como propiedades de clave externa compuesta para una relación determinada:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[Anotaciones de datos (clave simple)](#tab/data-annotations-simple-key)

Puede usar las anotaciones de datos para configurar qué propiedad se debe usar como la propiedad de clave externa para una relación determinada. Normalmente, esto se hace cuando la Convención no detecta la propiedad de clave externa:

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> La `[ForeignKey]` anotación puede colocarse en cualquier propiedad de navegación de la relación. No es necesario que vaya a la propiedad de navegación en la clase de entidad dependiente.

> [!NOTE]
> `[ForeignKey]`No es necesario que la propiedad especificada mediante en una propiedad de navegación exista en el tipo dependiente. En este caso, el nombre especificado se utilizará para crear una clave externa de sombra.

---

#### <a name="shadow-foreign-key"></a>Clave externa de sombra

Puede usar la sobrecarga de cadena de `HasForeignKey(...)` para configurar una propiedad Shadow como clave externa (consulte [propiedades de sombra](xref:core/modeling/shadow-properties) para obtener más información). Se recomienda agregar explícitamente la propiedad Shadow al modelo antes de usarla como clave externa (como se muestra a continuación).

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a>Nombre de restricción de clave externa

Por Convención, cuando el destino es una base de datos relacional, las restricciones Foreign Key se denominan FK \_ \<dependent type name> \_ \<principal type name> \_ \<foreign key property name> . En el caso de las claves externas compuestas, \<foreign key property name> se convierte en una lista de nombres de propiedades de clave externa separadas por guiones bajos.

También puede configurar el nombre de la restricción de la siguiente manera:

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a>Sin propiedad de navegación

No es necesario proporcionar una propiedad de navegación. Simplemente puede proporcionar una clave externa en un lado de la relación.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a>Clave principal

Si desea que la clave externa haga referencia a una propiedad que no sea la clave principal, puede usar la API fluida para configurar la propiedad de clave principal de la relación. La propiedad que se configura como clave principal se configurará automáticamente como una [clave alternativa](xref:core/modeling/keys#alternate-keys).

#### <a name="simple-key"></a>[Clave simple](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[Clave compuesta](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> El orden en el que se especifican las propiedades de clave principal debe coincidir con el orden en que se especifican para la clave externa.

---

### <a name="required-and-optional-relationships"></a>Relaciones obligatorias y opcionales

Puede usar la API fluida para configurar si la relación es obligatoria u opcional. En última instancia, controla si la propiedad de clave externa es obligatoria u opcional. Esto es muy útil cuando se usa una clave externa de estado de sombra. Si tiene una propiedad de clave externa en la clase de entidad, la necesidad de la relación se determina en función de si la propiedad de clave externa es necesaria u opcional (vea [propiedades obligatorias y opcionales](xref:core/modeling/entity-properties#required-and-optional-properties) para obtener más información).

Las propiedades de clave externa se encuentran en el tipo de entidad dependiente, por lo que si se configuran como requeridas, significa que todas las entidades dependientes deben tener una entidad principal correspondiente.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> `IsRequired(false)`La llamada también hace que la propiedad de clave externa sea opcional a menos que esté configurada de otro modo.

### <a name="cascade-delete"></a>Eliminación en cascada

Puede usar la API fluida para configurar explícitamente el comportamiento de eliminación en cascada para una relación determinada.

Consulte la [eliminación en cascada](xref:core/saving/cascade-delete) para obtener una explicación detallada de cada opción.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a>Otros patrones de relación

### <a name="one-to-one"></a>Uno a uno

Una relación de uno a uno tiene una propiedad de navegación de referencia en ambos lados. Siguen las mismas convenciones que las relaciones uno a varios, pero se incluye un índice único en la propiedad de clave externa para asegurarse de que solo un dependiente esté relacionado con cada entidad de seguridad.

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> EF elegirá una de las entidades como dependiente en función de su capacidad para detectar una propiedad de clave externa. Si se elige la entidad equivocada como dependiente, puede usar la API fluida para corregir este problema.

Al configurar la relación con la API fluida, se usan los `HasOne` `WithOne` métodos y.

Al configurar la clave externa, debe especificar el tipo de entidad dependiente: Observe el parámetro genérico que se proporciona `HasForeignKey` en la siguiente lista. En una relación uno a varios, es evidente que la entidad con la navegación de referencia es el dependiente y el que tiene la colección es la entidad de seguridad. Pero esto no es así en una relación uno a uno; por lo tanto, la necesidad de definirla explícitamente.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

El lado dependiente se considera opcional de forma predeterminada, pero se puede configurar según sea necesario. Sin embargo, EF no validará si se proporcionó una entidad dependiente, por lo que esta configuración solo marcará una diferencia cuando la asignación de base de datos permita su aplicación. Un escenario común para esto son los tipos de referencia que usan la división de tablas de forma predeterminada.

[!code-csharp[Main](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=Required&highlight=11-12)]

Con esta configuración, las columnas correspondientes a se `ShippingAddress` marcarán como que no aceptan valores NULL en la base de datos.

> [!NOTE]
> Si usa [tipos de referencia que no aceptan valores NULL](/dotnet/csharp/nullable-references) , `IsRequired` no es necesario llamar a.

> [!NOTE]
> La capacidad de configurar si el dependiente es necesario se presentó en EF Core 5,0.

### <a name="many-to-many"></a>Varios a varios

Una relación de varios a varios requiere una propiedad de navegación de colección en ambos lados. Se detectarán por Convención como otros tipos de relaciones.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=ManyToManyShared)]

La forma en que se implementa esta relación en la base de datos es mediante una tabla de combinación que contiene claves externas a `Post` y `Tag` . Por ejemplo, esto es lo que EF creará en una base de datos relacional para el modelo anterior.

```sql
CREATE TABLE [Posts] (
    [PostId] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([PostId])
);

CREATE TABLE [Tags] (
    [TagId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_Tags] PRIMARY KEY ([TagId])
);

CREATE TABLE [PostTag] (
    [PostsId] int NOT NULL,
    [TagsId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostsId], [TagsId]),
    CONSTRAINT [FK_PostTag_Posts_PostsId] FOREIGN KEY ([PostsId]) REFERENCES [Posts] ([PostId]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tags_TagsId] FOREIGN KEY ([TagsId]) REFERENCES [Tags] ([TagId]) ON DELETE CASCADE
);
```

Internamente, EF crea un tipo de entidad para representar la tabla de combinación a la que se hará referencia como el tipo de entidad de combinación. No hay ningún tipo de CLR específico que se pueda usar para esto, por lo que `Dictionary<string, object>` se utiliza. Puede haber más de una relación de varios a varios en el modelo, por lo que el tipo de entidad de combinación debe recibir un nombre único, en este caso `PostTag` . La característica que lo permite se denomina tipo de entidad de tipo compartido.

Las navegaciones de varios a varios se llaman omitir navegaciones, ya que omiten el tipo de entidad de combinación. Si está empleando la configuración masiva, todas las navegaciones de omitir se pueden obtener de `GetSkipNavigations` .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Metadata)]

Es habitual aplicar la configuración al tipo de entidad de combinación. Esta acción se puede realizar a través de `UsingEntity` .

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=SharedConfiguration)]

Se pueden proporcionar [datos de inicialización del modelo](xref:core/modeling/data-seeding) para el tipo de entidad de combinación mediante el uso de tipos anónimos. Puede examinar la vista de depuración del modelo para determinar los nombres de propiedad creados por la Convención.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Seeding)]

Los datos adicionales se pueden almacenar en el tipo de entidad de combinación, pero para ello es mejor crear un tipo de CLR de tipo. Al configurar la relación con un tipo de entidad de combinación personalizada, se deben especificar explícitamente las claves externas.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyPayload.cs?name=ManyToManyPayload)]

> [!NOTE]
> La capacidad de configurar relaciones varios a varios se presentó en EF Core 5,0, para la versión anterior, use el siguiente enfoque.

También puede representar una relación de varios a varios agregando el tipo de entidad de combinación y asignando dos relaciones uno a varios independientes.

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=16-19,21-24)]

> [!NOTE]
> Todavía no se ha agregado compatibilidad con la aplicación de scaffolding a relaciones de varios a varios desde la base de datos. Vea la [incidencia de seguimiento](https://github.com/dotnet/efcore/issues/22475).

## <a name="additional-resources"></a>Recursos adicionales

* [EF Core sesión reunión](https://www.youtube.com/watch?v=W1sxepfIMRM&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=32)de la comunidad, con un análisis profundo de varios a varios y de la infraestructura subyacente.
