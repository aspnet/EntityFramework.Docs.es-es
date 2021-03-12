---
title: Detección y notificaciones de cambios-EF Core
description: Detección de cambios de propiedad y relación mediante DetectChanges o notificaciones
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/change-detection
ms.openlocfilehash: fae8bdb1a89478531535b377f4ba8b02d1c848f4
ms.sourcegitcommit: 4798ab8d04c1fdbe6dd204d94d770fcbf309d09b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2021
ms.locfileid: "103023190"
---
# <a name="change-detection-and-notifications"></a>Detección y notificaciones de cambios

Cada instancia de <xref:Microsoft.EntityFrameworkCore.DbContext> realiza un seguimiento de los cambios realizados en las entidades. Estas entidades de las que se realiza un seguimiento, a su vez, impulsan los cambios en la base de datos cuando se llama a <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A>. Esto se trata en [Change Tracking en EF Core](xref:core/change-tracking/index), y en este documento se da por supuesto que se entienden los Estados de las entidades y los aspectos básicos del seguimiento de cambios de Entity Framework Core (EF Core).

El seguimiento de los cambios de propiedades y relaciones requiere que DbContext pueda detectar estos cambios. En este documento se explica cómo se produce esta detección y cómo usar las notificaciones de propiedad o los proxies de seguimiento de cambios para forzar la detección inmediata de los cambios.

> [!TIP]
> Puede ejecutar y depurar en todo el código de este documento [descargando el código de ejemplo de GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/main/samples/core/ChangeTracking/ChangeDetectionAndNotifications).

## <a name="snapshot-change-tracking"></a>Seguimiento de cambios de instantánea

De forma predeterminada, EF Core crea una instantánea de los valores de propiedad de cada entidad cuando se realiza un seguimiento por primera instancia de DbContext. Los valores almacenados en esta instantánea se comparan con los valores actuales de la entidad para determinar qué valores de propiedad han cambiado.

Esta detección de cambios se produce cuando se llama a SaveChanges para asegurarse de que se detectan todos los valores cambiados antes de enviar actualizaciones a la base de datos. Sin embargo, la detección de cambios también se produce en otros momentos para asegurarse de que la aplicación funciona con información de seguimiento actualizada. La detección de cambios se puede forzar en cualquier momento mediante una llamada a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .

### <a name="when-change-detection-is-needed"></a>Cuando se necesita la detección de cambios

La detección de cambios es necesaria cuando se ha cambiado una propiedad o navegación _sin utilizar EF Core para realizar este cambio_. Por ejemplo, considere la posibilidad de cargar blogs y entradas y, a continuación, realizar cambios en estas entidades:

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        blog.Name = ".NET Blog (Updated!)";

        // Add a new entity to a navigation
        blog.Posts.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many..."
        });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
        context.ChangeTracker.DetectChanges();
        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_1)]

Al examinar la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) antes de llamar a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> , se muestra que los cambios realizados no se han detectado y, por lo tanto, no se reflejan en los Estados de la entidad y en los datos de propiedad modificados

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, <not found>]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

En concreto, el estado de la entrada de blog sigue siendo `Unchanged` y la nueva publicación no aparece como entidad sometida a seguimiento. (El astutos observará que las propiedades notifican sus nuevos valores, aunque EF Core no hayan detectado todavía estos cambios. Esto se debe a que la vista de depuración Lee los valores actuales directamente de la instancia de la entidad).

Compare esto con la vista de depuración después de llamar a DetectChanges:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified Originally '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

Ahora el blog está marcado correctamente como `Modified` y se ha detectado la nueva publicación y se realiza su seguimiento como `Added` .

Al principio de esta sección, se indica que se necesita la detección de cambios cuando no se usa _EF Core para realizar el cambio_. Esto es lo que sucede en el código anterior. Es decir, los cambios en la propiedad y la navegación se realizan _directamente en las instancias de la entidad_ y no mediante métodos de EF Core.

Compare esto con el siguiente código que modifica las entidades de la misma manera, pero esta vez mediante métodos EF Core:

<!--
        using var context = new BlogsContext();
        var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

        // Change a property value
        context.Entry(blog).Property(e => e.Name).CurrentValue = ".NET Blog (Updated!)";

        // Add a new entity to the DbContext
        context.Add(
            new Post
            {
                Blog = blog,
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

        Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Snapshot_change_tracking_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=Snapshot_change_tracking_2)]

En este caso, la vista de depuración del seguimiento de cambios muestra que se conocen todos los Estados de las entidades y las modificaciones de propiedades, aunque no se ha producido la detección de cambios. Esto se debe a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.CurrentValue?displayProperty=nameWithType> que es un método EF Core, lo que significa que EF Core conoce inmediatamente el cambio realizado por este método. Del mismo modo, la llamada a <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> permite a EF Core saber de inmediato la nueva entidad y realizar su seguimiento de forma adecuada.

> [!TIP]
> No intente evitar la detección de cambios mediante el uso de métodos EF Core para realizar cambios en la entidad. Esto suele ser más complicado y se comporta menos bien que realizar cambios en las entidades de la manera normal. La intención de este documento es informar de Cuándo se necesita detectar cambios y cuándo no lo está. La intención es no fomentar la prevención de la detección de cambios.

### <a name="methods-that-automatically-detect-changes"></a>Métodos que detectan automáticamente los cambios

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges> se llama automáticamente a los métodos en los que es probable que esto afecte a los resultados. Estos métodos son:

- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A?displayProperty=nameWithType> , para asegurarse de que se detectan todos los cambios antes de actualizar la base de datos.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> , para asegurarse de que los Estados de entidad y las propiedades modificadas están actualizados.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.HasChanges?displayProperty=nameWithType>, para asegurarse de que el resultado es preciso.
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.CascadeChanges?displayProperty=nameWithType>, para garantizar los Estados de entidad correctos para las entidades principal y primaria antes de la cascada.
- <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType>, para asegurarse de que el gráfico sometido a seguimiento esté actualizado.

También hay algunos lugares donde se produce la detección de cambios solo en una única instancia de entidad, en lugar de en el gráfico completo de entidades de las que se ha realizado un seguimiento. Estos lugares son:

- Al utilizar <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> , para asegurarse de que el estado y las propiedades modificadas de la entidad están actualizados.
- Cuando <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> se usan métodos como `Property` , `Collection` `Reference` o `Member` para garantizar que las modificaciones de propiedades, los valores actuales, etc. están actualizados.
- Cuando una entidad dependiente/secundaria se va a eliminar porque se ha roto una relación requerida. Esto detecta cuándo no se debe eliminar una entidad porque se ha vuelto a crear un elemento primario.

La detección local de los cambios de una sola entidad se puede desencadenar explícitamente mediante una llamada a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> .

> [!NOTE]
> Los cambios en la detección local pueden pasar por alto algunos cambios que encontraría una detección completa. Esto sucede cuando las acciones en cascada resultantes de cambios no detectados en otras entidades afectan a la entidad en cuestión. En tales casos, la aplicación puede necesitar forzar un examen completo de todas las entidades mediante una llamada explícita a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> .

### <a name="disabling-automatic-change-detection"></a>Deshabilitar la detección automática de cambios

El rendimiento de la detección de cambios no es un cuello de botella para la mayoría de las aplicaciones. Sin embargo, la detección de cambios puede suponer un problema de rendimiento para algunas aplicaciones que realizan el seguimiento de miles de entidades. (El número exacto dependerá de muchas cosas, como el número de propiedades de la entidad). Por este motivo, se puede deshabilitar la detección automática de cambios mediante <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.AutoDetectChangesEnabled?displayProperty=nameWithType> . Por ejemplo, considere el procesamiento de entidades de combinación en una relación de varios a varios con cargas:

<!--
        public override int SaveChanges()
        {
            foreach (var entityEntry in ChangeTracker.Entries<PostTag>()) // Detects changes automatically
            {
                if (entityEntry.State == EntityState.Added)
                {
                    entityEntry.Entity.TaggedBy = "ajcvickers";
                    entityEntry.Entity.TaggedOn = DateTime.Now;
                }
            }

            try
            {
                ChangeTracker.AutoDetectChangesEnabled = false;
                return base.SaveChanges(); // Avoid automatically detecting changes again here
            }
            finally
            {
                ChangeTracker.AutoDetectChangesEnabled = true;
            }
        }
-->
[!code-csharp[SaveChanges](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/SnapshotSamples.cs?name=SaveChanges)]

Como sabemos de la sección anterior, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> y <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A?displayProperty=nameWithType> detectan automáticamente los cambios. Sin embargo, después de llamar a las entradas, el código no realiza ningún cambio en el estado de la entidad o propiedad. (Establecer valores de propiedades normales en entidades agregadas no produce cambios de estado). Por lo tanto, el código deshabilita la detección de cambios automática innecesaria al llamar al método SaveChanges de base. El código también usa un bloque try/finally para asegurarse de que se restaure la configuración predeterminada incluso si se produce un error en SaveChanges.

> [!TIP]
> No asuma que el código debe deshabilitar la detección automática de cambios para que funcione correctamente. Esto solo es necesario cuando la generación de perfiles de una aplicación que realiza un seguimiento de muchas entidades indica que el rendimiento de la detección de cambios es un problema.

### <a name="detecting-changes-and-value-conversions"></a>Detección de cambios y conversiones de valores

Para usar el seguimiento de cambios de instantánea con un tipo de entidad, EF Core debe ser capaz de:

- Crear una instantánea de cada valor de propiedad cuando se realiza el seguimiento de la entidad
- Compara este valor con el valor actual de la propiedad.
- Generar un código hash para el valor

Esto lo controla automáticamente EF Core para los tipos que se pueden asignar directamente a la base de datos. Sin embargo, cuando [se utiliza un convertidor de valores para asignar una propiedad](xref:core/modeling/value-conversions), dicho convertidor debe especificar cómo realizar estas acciones. Esto se logra con un comparador de valores y se describe en detalle en la documentación de los [comparadores de valores](xref:core/modeling/value-comparers) .

## <a name="notification-entities"></a>Entidades de notificación

El seguimiento de cambios de instantánea se recomienda para la mayoría de las aplicaciones. Sin embargo, las aplicaciones que realizan el seguimiento de muchas entidades o realizan muchos cambios en esas entidades pueden beneficiarse de la implementación de entidades que notifican automáticamente EF Core cuando cambian sus valores de propiedad y navegación. Estos se conocen como "entidades de notificación".

### <a name="implementing-notification-entities"></a>Implementación de entidades de notificación

Las entidades de notificación hacen uso de las <xref:System.ComponentModel.INotifyPropertyChanging> <xref:System.ComponentModel.INotifyPropertyChanged> interfaces e, que forman parte de la biblioteca de clases base (BCL) de .net. Estas interfaces definen eventos que se deben desencadenar antes y después de cambiar un valor de propiedad. Por ejemplo:

<!--
    public class Blog : INotifyPropertyChanging, INotifyPropertyChanged
    {
        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private int _id;
        public int Id
        {
            get => _id;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Id)));
                _id = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Id)));
            }
        }

        private string _name;
        public string Name
        {
            get => _name;
            set
            {
                PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(nameof(Name)));
                _name = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(nameof(Name)));
            }
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationEntitiesSamples.cs?name=Model)]

Además, las navegaciones de las colecciones deben implementar `INotifyCollectionChanged` ; en el ejemplo anterior, esto se cumple mediante el uso <xref:System.Collections.ObjectModel.ObservableCollection%601> de un de posts. EF Core también se suministra con una <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ObservableHashSet%601> implementación que tiene búsquedas más eficaces a costa de una ordenación estable.

Normalmente, la mayor parte de este código de notificación se mueve a una clase base no asignada. Por ejemplo:

<!--
    public class Blog : NotifyingEntity
    {
        private int _id;
        public int Id
        {
            get => _id;
            set => SetWithNotify(value, out _id);
        }

        private string _name;
        public string Name
        {
            get => _name;
            set => SetWithNotify(value, out _name);
        }

        public IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public abstract class NotifyingEntity : INotifyPropertyChanging, INotifyPropertyChanged
    {
        protected void SetWithNotify<T>(T value, out T field, [CallerMemberName] string propertyName = "")
        {
            NotifyChanging(propertyName);
            field = value;
            NotifyChanged(propertyName);
        }

        public event PropertyChangingEventHandler PropertyChanging;
        public event PropertyChangedEventHandler PropertyChanged;

        private void NotifyChanged(string propertyName)
            => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));

        private void NotifyChanging(string propertyName)
            => PropertyChanging?.Invoke(this, new PropertyChangingEventArgs(propertyName));
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Model)]

### <a name="configuring-notification-entities"></a>Configuración de entidades de notificación

No hay ninguna manera de que EF Core valide que `INotifyPropertyChanging` o `INotifyPropertyChanged` estén totalmente implementados para su uso con EF Core. En concreto, algunos usos de estas interfaces lo hacen con las notificaciones solo en determinadas propiedades, en lugar de en todas las propiedades (incluidas las navegaciones) según lo requiera EF Core. Por esta razón, EF Core no se enlaza automáticamente a estos eventos.

En su lugar, EF Core debe estar configurado para usar estas entidades de notificación. Normalmente, esto se hace para todos los tipos de entidad mediante una llamada a <xref:Microsoft.EntityFrameworkCore.ModelBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> . Por ejemplo:

<!--
        protected override void OnModelCreating(ModelBuilder modelBuilder)
        {
            modelBuilder.HasChangeTrackingStrategy(ChangeTrackingStrategy.ChangingAndChangedNotifications);
        }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=OnModelCreating)]

(La estrategia también se puede establecer de manera diferente para los distintos tipos de entidad mediante, pero suele ser un valor de <xref:Microsoft.EntityFrameworkCore.Metadata.Builders.EntityTypeBuilder.HasChangeTrackingStrategy%2A?displayProperty=nameWithType> contraproducción, ya que DetectChanges sigue siendo necesario para los tipos que no son entidades de notificación).

El seguimiento de cambios de notificación completo requiere que tanto `INotifyPropertyChanging` como `INotifyPropertyChanged` estén implementados. Esto permite guardar los valores originales justo antes de cambiar el valor de la propiedad, evitando la necesidad de EF Core para crear una instantánea al realizar el seguimiento de la entidad. Los tipos de entidad que implementan solo `INotifyPropertyChanged` se pueden usar con EF Core. En este caso, EF todavía crea una instantánea al realizar el seguimiento de una entidad para realizar un seguimiento de los valores originales, pero usa las notificaciones para detectar los cambios inmediatamente, en lugar de tener que llamar a DetectChanges.

Los diferentes <xref:Microsoft.EntityFrameworkCore.ChangeTrackingStrategy> valores se resumen en la tabla siguiente.

| ChangeTrackingStrategy                              | Interfaces necesarias                                      | Necesita DetectChanges | Valores originales de instantáneas
|:----------------------------------------------------|--------------------------------------------------------|---------------------|--------------------------
| Instantánea                                            | Ninguno                                                   | Sí                 | Sí
| ChangedNotifications                                | INotifyPropertyChanged                                 | No                  | Sí
| ChangingAndChangedNotifications                     | INotifyPropertyChanged y INotifyPropertyChanging     | No                  | No
| ChangingAndChangedNotificationsWithOriginalValues   | INotifyPropertyChanged y INotifyPropertyChanging     | No                  | Sí

### <a name="using-notification-entities"></a>Usar entidades de notificación

Las entidades de notificación se comportan como cualquier otra entidad, salvo que realizar cambios en las instancias de la entidad no requiere una llamada a <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.DetectChanges?displayProperty=nameWithType> para detectar estos cambios. Por ejemplo:

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(new Post
            {
                Title = "What’s next for System.Text.Json?",
                Content = ".NET 5.0 was released recently and has come with many..."
            });

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Notification_entities_2](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/NotificationWithBaseSamples.cs?name=Notification_entities_2)]

Con las entidades normales, la [vista de depuración de Change Tracker](xref:core/change-tracking/debug-views) mostró que estos cambios no se detectaron hasta que se llamó a DetectChanges. Al examinar la vista de depuración cuando se usan las entidades de notificación, se muestra que estos cambios se han detectado inmediatamente:

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog (Updated!)' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482643}]
Post {Id: -2147482643} Added
  Id: -2147482643 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 was released recently and has come with many...'
  Title: 'What's next for System.Text.Json?'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

## <a name="change-tracking-proxies"></a>Proxies de seguimiento de cambios

> [!NOTE]
> Los proxies de seguimiento de cambios se introdujeron en EF Core 5,0.

EF Core pueden generar dinámicamente tipos de proxy que implementan <xref:System.ComponentModel.INotifyPropertyChanging> y <xref:System.ComponentModel.INotifyPropertyChanged> . Esto requiere la instalación del paquete NuGet [Microsoft. EntityFrameworkCore. Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) y la habilitación de los proxies de seguimiento de cambios con, <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseChangeTrackingProxies%2A> por ejemplo:

<!--
        protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            => optionsBuilder.UseChangeTrackingProxies();
-->
[!code-csharp[OnConfiguring](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=OnConfiguring)]

La creación de un proxy dinámico implica la creación de un nuevo tipo .net dinámico (mediante la implementación de los proxies de [. Core](https://www.nuget.org/packages/Castle.Core/) ), que hereda del tipo de entidad y, a continuación, invalida todos los establecedores de propiedad. Por tanto, los tipos de entidad de los servidores proxy deben ser tipos que se pueden heredar de y deben tener propiedades que se puedan invalidar. Además, las navegaciones de la colección creadas explícitamente deben implementar, <xref:System.Collections.Specialized.INotifyCollectionChanged> por ejemplo:

<!--
    public class Blog
    {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }

        public virtual IList<Post> Posts { get; } = new ObservableCollection<Post>();
    }

    public class Post
    {
        public virtual int Id { get; set; }
        public virtual string Title { get; set; }
        public virtual string Content { get; set; }

        public virtual int BlogId { get; set; }
        public virtual Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Model)]

Una desventaja importante de los proxies de seguimiento de cambios es que EF Core siempre debe realizar un seguimiento de las instancias del proxy, nunca instancias del tipo de entidad subyacente. Esto se debe a que las instancias del tipo de entidad subyacente no generarán notificaciones, lo que significa que se omitirán los cambios realizados en estas entidades.

EF Core crea instancias de proxy automáticamente al consultar la base de datos, por lo que generalmente se limita a realizar el seguimiento de nuevas instancias de entidad. Estas instancias deben crearse mediante los <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.CreateProxy%2A> métodos de extensión y **no** de la manera normal mediante `new` . Esto significa que el código de los ejemplos anteriores ahora debe hacer uso de `CreateProxy` :

<!--
            using var context = new BlogsContext();
            var blog = context.Blogs.Include(e => e.Posts).First(e => e.Name == ".NET Blog");

            // Change a property value
            blog.Name = ".NET Blog (Updated!)";

            // Add a new entity to a navigation
            blog.Posts.Add(
                context.CreateProxy<Post>(
                    p =>
                        {
                            p.Title = "What’s next for System.Text.Json?";
                            p.Content = ".NET 5.0 was released recently and has come with many...";
                        }));

            Console.WriteLine(context.ChangeTracker.DebugView.LongView);
-->
[!code-csharp[Change_tracking_proxies_1](../../../samples/core/ChangeTracking/ChangeDetectionAndNotifications/ChangeTrackingProxiesSamples.cs?name=Change_tracking_proxies_1)]

## <a name="change-tracking-events"></a>Eventos de seguimiento de cambios

EF Core activa el <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Tracked?displayProperty=nameWithType> evento cuando se realiza un seguimiento de una entidad por primera vez. Los cambios futuros de estado de la entidad producen <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.StateChanged?displayProperty=nameWithType> eventos. Consulte [Eventos de .NET en EF Core](xref:core/logging-events-diagnostics/events) para obtener más información.

> [!NOTE]
> El `StateChanged` evento no se desencadena cuando se realiza el seguimiento de una entidad por primera vez, aunque el Estado haya cambiado de `Detached` a uno de los otros Estados. Asegúrese de escuchar los `StateChanged` `Tracked` eventos y para obtener todas las notificaciones pertinentes.
