---
title: Resolución de dependencias en Xamarin.Forms
description: En este artículo se explica cómo insertar un método de resolución de dependencia en Xamarin.Forms para que el contenedor de inserción de dependencia de la aplicación tiene control sobre la creación y duración de los representadores personalizados, efectos y las implementaciones de DependencyService.
ms.prod: xamarin
ms.assetid: 491B87DC-14CB-4ADC-AC6C-40A7627B2524
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2018
ms.openlocfilehash: 6df393d59207cea9c316189059f8d0e08a5e5137
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290067"
---
# <a name="dependency-resolution-in-xamarinforms"></a>Resolución de dependencias en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)

_En este artículo se explica cómo insertar un método de resolución de dependencia en Xamarin.Forms para que el contenedor de inserción de dependencia de la aplicación tiene control sobre la creación y duración de los representadores personalizados, efectos y las implementaciones de DependencyService. Los ejemplos de código en este artículo se toman de la [resolución de dependencia mediante contenedores](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo) ejemplo._

En el contexto de una aplicación de Xamarin.Forms que usa el patrón Model-View-ViewModel (MVVM), un contenedor de inserción de dependencia puede utilizarse para registrar y resolver los modelos de vista y para registrar servicios e insertarlas en modelos de vista. Durante la creación del modelo de vista, el contenedor inserta las dependencias que son necesarias. Si no se han creado esas dependencias, el contenedor crea y resuelve primero las dependencias. Para obtener más información sobre la inserción de dependencias, incluidos ejemplos de inyección de dependencias en los modelos de vista, consulte [inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

Control sobre la creación y duración de los tipos de proyectos de la plataforma tradicionalmente se realiza mediante Xamarin.Forms, que usa el `Activator.CreateInstance` método para crear instancias de los representadores personalizados, los efectos, y [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implementaciones. Lamentablemente, esto limita el control del desarrollador sobre la creación y duración de estos tipos y la capacidad de insertar dependencias en ellos. Este comportamiento puede modificarse mediante la inserción de un método de resolución de dependencia en Xamarin.Forms que controla cómo se crearán los tipos: contenedor de inserción de dependencia de la aplicación, o Xamarin.Forms. Sin embargo, tenga en cuenta que no hay ningún requisito para insertar un método de resolución de dependencia en Xamarin.Forms. Xamarin.Forms continuará crear y administrar la duración de los tipos de proyectos de la plataforma, si no se ha insertado un método de resolución de dependencia.

> [!NOTE]
> Aunque en este artículo se centra en la inyección de un método de resolución de dependencia en Xamarin.Forms que resuelve los tipos registrados mediante un contenedor de inserción de dependencia, también es posible insertar un método de resolución de dependencia que usa los métodos de fábrica para resolver tipos registrados. Para obtener más información, consulte el [resolución de dependencia mediante métodos de generador](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-factoriesdemo) ejemplo.

## <a name="injecting-a-dependency-resolution-method"></a>Inserción de un método de resolución de dependencia

El [ `DependencyResolver` ](xref:Xamarin.Forms.Internals.DependencyResolver) clase proporciona la capacidad para insertar un método de resolución de dependencia en Xamarin.Forms, utilizando el [ `ResolveUsing` ](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) método. A continuación, cuando Xamarin.Forms tiene una instancia de un tipo determinado, el método de resolución de dependencia tiene la oportunidad para proporcionar la instancia. Si el método de resolución de dependencia devuelve `null` para un tipo solicitado, Xamarin.Forms vuelve a intentar crear el tipo de instancia de sí misma mediante el `Activator.CreateInstance` método.

El ejemplo siguiente muestra cómo establecer el método de resolución de dependencia con el [ `ResolveUsing` ](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) método:

```csharp
using Autofac;
using Xamarin.Forms.Internals;
...

public partial class App : Application
{
    // IContainer and ContainerBuilder are provided by Autofac
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();

    public App()
    {
        ...
        DependencyResolver.ResolveUsing(type => container.IsRegistered(type) ? container.Resolve(type) : null);
        ...
    }
    ...
}
```

En este ejemplo, el método de resolución de dependencia se establece en una expresión lambda que usa el contenedor de inserción de dependencia de Autofac para resolver los tipos que se han registrado con el contenedor. En caso contrario, `null` se devolverán, lo que dará como resultado en Xamarin.Forms al intentar resolver el tipo.

> [!NOTE]
> La API de un contenedor de inserción de dependencia es el contenedor específica. Los ejemplos de código en este artículo usan Autofac como un contenedor de inserción de dependencia, que proporciona el `IContainer` y `ContainerBuilder` tipos. Contenedores de inserción de dependencia alternativo podrían usarse por igual, pero usaría distintas API que se presentan aquí.

Tenga en cuenta que no hay ningún requisito para establecer el método de resolución de dependencia durante el inicio de la aplicación. Se puede establecer en cualquier momento. La única restricción es que necesita saber sobre el método de resolución de dependencia en el momento en que la aplicación intenta consumir tipos almacenados en el contenedor de inserción de dependencia Xamarin.Forms. Por lo tanto, si hay servicios en el contenedor de inserción de dependencias que requiera la aplicación durante el inicio, el método de resolución de dependencia deberá establecerse al principio de ciclo de vida de la aplicación. De forma similar, si el contenedor de inserción de dependencia administra la creación y duración de una determinada [ `Effect` ](xref:Xamarin.Forms.Effect), deberá conocer el método de resolución de dependencia antes de intentar crear una vista de Xamarin.Forms que usa `Effect`.

> [!WARNING]
> Al registrar y resolver los tipos con un contenedor de inserción de dependencia, estos tienen un costo por uso del contenedor de la reflexión para la creación de cada tipo, especialmente si se está reconstruyendo dependencias para cada navegación de páginas en la aplicación de rendimiento. Si hay muchas o pocas dependencias, puede aumentar significativamente el costo de la creación.

## <a name="registering-types"></a>Registro de tipos

Los tipos deben registrarse en el contenedor de inserción de dependencia antes de que pueda resolver a través del método de resolución de dependencia. El ejemplo de código siguiente muestra los métodos de registro que expone la aplicación de ejemplo en el `App` (clase), para el contenedor Autofac:

```csharp
using Autofac;
using Autofac.Core;
...

public partial class App : Application
{
    static IContainer container;
    static readonly ContainerBuilder builder = new ContainerBuilder();
    ...

    public static void RegisterType<T>() where T : class
    {
        builder.RegisterType<T>();
    }

    public static void RegisterType<TInterface, T>() where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>().As<TInterface>();
    }

    public static void RegisterTypeWithParameters<T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where T : class
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        });
    }

    public static void RegisterTypeWithParameters<TInterface, T>(Type param1Type, object param1Value, Type param2Type, string param2Name) where TInterface : class where T : class, TInterface
    {
        builder.RegisterType<T>()
               .WithParameters(new List<Parameter>()
        {
            new TypedParameter(param1Type, param1Value),
            new ResolvedParameter(
                (pi, ctx) => pi.ParameterType == param2Type && pi.Name == param2Name,
                (pi, ctx) => ctx.Resolve(param2Type))
        }).As<TInterface>();
    }

    public static void BuildContainer()
    {
        container = builder.Build();
    }
    ...
}
```

Cuando una aplicación utiliza un método de resolución de dependencia para resolver los tipos de un contenedor, los registros de tipo normalmente se realizan desde proyectos de la plataforma. Esto permite que los proyectos de plataforma registrar los tipos de representadores personalizados, los efectos, y [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implementaciones.

Después de registro del tipo desde un proyecto de la plataforma, el `IContainer` objeto debe compilarse, que se logra mediante una llamada a la `BuildContainer` método. Este método invoca de Autofac `Build` método en el `ContainerBuilder` instancia, que crea un nuevo contenedor de inyección de dependencia que contiene los registros que se han realizado.

En las secciones siguientes, un `Logger` clase que implementa el `ILogger` interfaz se inyecta en constructores de clase. El `Logger` clase registro simple implementa funcionalidad utilizando el `Debug.WriteLine` método y se usa para demostrar cómo los servicios se pueden insertar en los representadores personalizados, los efectos, y [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implementaciones.

### <a name="registering-custom-renderers"></a>Registrar a los representadores personalizados

La aplicación de ejemplo incluye una página que reproduce vídeos de web, cuyo origen XAML se muestra en el ejemplo siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

El `VideoPlayer` vista se implementa en cada plataforma mediante una `VideoPlayerRenderer` (clase), que proporciona la funcionalidad para reproducir el vídeo. Para obtener más información sobre estas clases de representador personalizado, consulte [implementa un Reproductor de vídeo](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md).

En iOS y la plataforma Universal de Windows (UWP), el `VideoPlayerRenderer` clases tienen el siguiente constructor, que requiere un `ILogger` argumento:

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

En todas las plataformas, realizar el registro de tipo con el contenedor de inserción de dependencia mediante el `RegisterTypes` método, que se invoca antes de la plataforma de carga de la aplicación con el `LoadApplication(new App())` método. El ejemplo siguiente se muestra el `RegisterTypes` método en la plataforma de iOS:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

En este ejemplo, el `Logger` tipo concreto está registrado a través de una asignación con el tipo de interfaz y el `VideoPlayerRenderer` tipo se registrado directamente sin una asignación de interfaz. Cuando el usuario navega a la página que contiene el `VideoPlayer` vista, se invocará el método de resolución de dependencia para resolver el `VideoPlayerRenderer` tipo desde el contenedor de inserción de dependencia, que también se resolver e insertar el `Logger` escriba en el `VideoPlayerRenderer` constructor.

El `VideoPlayerRenderer` constructor en la plataforma Android es un poco más complicado, ya que requiere un `Context` argumento además el `ILogger` argumento:

```csharp
public VideoPlayerRenderer(Context context, ILogger logger) : base(context)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

El ejemplo siguiente se muestra el `RegisterTypes` método en la plataforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<FormsVideoLibrary.Droid.VideoPlayerRenderer>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

En este ejemplo, el `App.RegisterTypeWithParameters` método registra el `VideoPlayerRenderer` con el contenedor de inserción de dependencia. El método de registro garantiza que el `MainActivity` instancia se insertará como el `Context` argumento y que la `Logger` se insertará el tipo como el `ILogger` argumento.

### <a name="registering-effects"></a>Efectos de registro

La aplicación de ejemplo incluye una página que usa un toque seguimiento efecto arrastrar [ `BoxView` ](xref:Xamarin.Forms.BoxView) instancias alrededor de la página. El [ `Effect` ](xref:Xamarin.Forms.Effect) se agrega a la `BoxView` con el código siguiente:

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

El `TouchEffect` clase es un [ `RoutingEffect` ](xref:Xamarin.Forms.RoutingEffect) que se implementa en cada plataforma mediante una `TouchEffect` clase que tiene un `PlatformEffect`. La plataforma `TouchEffect` clase proporciona la funcionalidad para arrastrar el `BoxView` alrededor de la página. Para obtener más información sobre estas clases efecto, consulte [invocar eventos de efectos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

En todas las plataformas, la `TouchEffect` clase tiene el siguiente constructor, que requiere un `ILogger` argumento:

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

En todas las plataformas, realizar el registro de tipo con el contenedor de inserción de dependencia mediante el `RegisterTypes` método, que se invoca antes de la plataforma de carga de la aplicación con el `LoadApplication(new App())` método. El ejemplo siguiente se muestra el `RegisterTypes` método en la plataforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

En este ejemplo, el `Logger` tipo concreto está registrado a través de una asignación con el tipo de interfaz y el `TouchEffect` tipo se registrado directamente sin una asignación de interfaz. Cuando el usuario navega a la página que contiene un [ `BoxView` ](xref:Xamarin.Forms.BoxView) instancia que tiene el `TouchEffect` conectadas a ella, se invocará el método de resolución de dependencia para resolver la plataforma `TouchEffect` tipo a partir de la dependencia contenedor de inserción, que también se resolver e insertar el `Logger` escriba en el `TouchEffect` constructor.

### <a name="registering-dependencyservice-implementations"></a>Registrar las implementaciones de DependencyService

La aplicación de ejemplo incluye una página que usa [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) implementaciones en cada plataforma para permitir al usuario elegir una foto de la biblioteca de imágenes del dispositivo. El `IPhotoPicker` interfaz define la funcionalidad que se implementa mediante el `DependencyService` implementaciones y se muestra en el ejemplo siguiente:

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

En cada proyecto de la plataforma, el `PhotoPicker` la clase implementa la `IPhotoPicker` interfaz mediante las API de plataforma. Para obtener más información acerca de estos servicios de dependencia, consulte [seleccionar una foto de la biblioteca de imágenes](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

En iOS y UWP, la `PhotoPicker` clases tienen el siguiente constructor, que requiere un `ILogger` argumento:

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

En todas las plataformas, realizar el registro de tipo con el contenedor de inserción de dependencia mediante el `RegisterTypes` método, que se invoca antes de la plataforma de carga de la aplicación con el `LoadApplication(new App())` método. El ejemplo siguiente se muestra el `RegisterTypes` método en UWP:

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

En este ejemplo, el `Logger` tipo concreto está registrado a través de una asignación con el tipo de interfaz y el `PhotoPicker` tipo también se registra a través de una asignación de interfaz.

El `PhotoPicker` constructor en la plataforma Android es un poco más complicado, ya que requiere un `Context` argumento además el `ILogger` argumento:

```csharp
public PhotoPicker(Context context, ILogger logger)
{
    _context = context ?? throw new ArgumentNullException(nameof(context));
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

El ejemplo siguiente se muestra el `RegisterTypes` método en la plataforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<IPhotoPicker, Services.Droid.PhotoPicker>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

En este ejemplo, el `App.RegisterTypeWithParameters` método registra el `PhotoPicker` con el contenedor de inserción de dependencia. El método de registro garantiza que el `MainActivity` instancia se insertará como el `Context` argumento y que la `Logger` se insertará el tipo como el `ILogger` argumento.

Cuando el usuario navega a la página de selección de fotos y elige para seleccionar una foto, la `OnSelectPhotoButtonClicked` controlador se ejecuta:

```csharp
async void OnSelectPhotoButtonClicked(object sender, EventArgs e)
{
    ...
    var photoPickerService = DependencyService.Resolve<IPhotoPicker>();
    var stream = await photoPickerService.GetImageStreamAsync();
    if (stream != null)
    {
        image.Source = ImageSource.FromStream(() => stream);
    }
    ...
}
```

Cuando el [ `DependencyService.Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*) se invoca el método, se invocará el método de resolución de dependencia para resolver el `PhotoPicker` tipo desde el contenedor de inserción de dependencia, que también se resolver e insertar el `Logger` tipo en el `PhotoPicker` constructor.

> [!NOTE]
> El [ `Resolve<T>` ](xref:Xamarin.Forms.DependencyService.Resolve*) método se debe usar al resolver un tipo de contenedor de inserción de dependencia de la aplicación a través de la [ `DependencyService` ](xref:Xamarin.Forms.DependencyService).

## <a name="related-links"></a>Vínculos relacionados

- [Resolución de dependencia mediante contenedores (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)
- [Inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [Implementación de un reproductor de vídeo](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [Invocación de eventos de efectos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [Seleccionar una foto de la biblioteca de imágenes](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
