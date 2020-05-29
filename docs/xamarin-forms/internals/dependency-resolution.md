---
Título: ' resolución de dependencia en Xamarin.Forms ' Descripción: ' en este artículo se explica cómo insertar un método de resolución de dependencias en Xamarin.Forms para que el contenedor de inserción de dependencias de una aplicación tenga control sobre la creación y la duración de los representadores, los efectos y las implementaciones de DependencyService personalizados. '
MS. Prod: MS. AssetID: MS. Technology: Author: MS. Author: MS. Date: no-LOC:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="dependency-resolution-in-xamarinforms"></a>Resolución de dependencias enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)

_En este artículo se explica cómo insertar un método de resolución de dependencias en Xamarin.Forms para que el contenedor de inserción de dependencias de una aplicación controle la creación y la duración de los representadores personalizados, los efectos y las implementaciones de DependencyService. Los ejemplos de código de este artículo se han tomado del ejemplo de [resolución de dependencias mediante contenedores](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo) ._

En el contexto de una Xamarin.Forms aplicación que usa el patrón Model-View-ViewModel (MVVM), se puede usar un contenedor de inserción de dependencias para registrar y resolver modelos de vista, así como para registrar servicios e insertarlos en modelos de vista. Durante la creación del modelo de vista, el contenedor inserta las dependencias necesarias. Si no se han creado estas dependencias, el contenedor crea y resuelve primero las dependencias. Para obtener más información sobre la inserción de dependencias, incluidos ejemplos de inserción de dependencias en los modelos de vista, consulte [inserción de dependencias](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md).

Normalmente, el control sobre la creación y la duración de los tipos de los proyectos de plataforma se realiza mediante Xamarin.Forms , que utiliza el `Activator.CreateInstance` método para crear instancias de representadores, efectos e [`DependencyService`](xref:Xamarin.Forms.DependencyService) implementaciones personalizados. Desafortunadamente, esto limita el control del desarrollador sobre la creación y la duración de estos tipos, y la capacidad de insertar dependencias en ellos. Este comportamiento se puede cambiar insertando un método de resolución de dependencia en Xamarin.Forms que controla cómo se crearán los tipos, ya sea por el contenedor de inserción de dependencias de la aplicación o por Xamarin.Forms . Sin embargo, tenga en cuenta que no hay ningún requisito para insertar un método de resolución de dependencias en Xamarin.Forms . Xamarin.Formscontinuará creando y administrando la duración de los tipos en los proyectos de plataforma si no se inserta un método de resolución de dependencias.

> [!NOTE]
> Aunque este artículo se centra en la inserción de un método de resolución de dependencias en Xamarin.Forms que resuelve los tipos registrados mediante un contenedor de inserción de dependencias, también es posible insertar un método de resolución de dependencias que use métodos de generador para resolver los tipos registrados. Para obtener más información, vea el ejemplo de [resolución de dependencias mediante métodos de generador](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-factoriesdemo) .

## <a name="injecting-a-dependency-resolution-method"></a>Insertar un método de resolución de dependencias

La [`DependencyResolver`](xref:Xamarin.Forms.Internals.DependencyResolver) clase proporciona la capacidad de insertar un método de resolución de dependencias en Xamarin.Forms , mediante el [`ResolveUsing`](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) método. A continuación, cuando Xamarin.Forms necesita una instancia de un tipo determinado, el método de resolución de dependencias tiene la oportunidad de proporcionar la instancia. Si el método de resolución de dependencias devuelve `null` para un tipo solicitado, Xamarin.Forms vuelve a intentar crear la instancia de tipo en sí mediante el `Activator.CreateInstance` método.

En el ejemplo siguiente se muestra cómo establecer el método de resolución de dependencias con el [`ResolveUsing`](xref:Xamarin.Forms.Internals.DependencyResolver.ResolveUsing*) método:

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

En este ejemplo, el método de resolución de dependencias se establece en una expresión lambda que usa el contenedor de inserción de dependencias Autofac para resolver los tipos que se han registrado con el contenedor. De lo contrario, se `null` devolverá, lo que provocará que se Xamarin.Forms intente resolver el tipo.

> [!NOTE]
> La API usada por un contenedor de inserción de dependencias es específica del contenedor. En los ejemplos de código de este artículo se usa Autofac como contenedor de inserción de dependencias, que proporciona los `IContainer` `ContainerBuilder` tipos y. También se podrían usar contenedores de inyección de dependencia alternativos, pero usarían API diferentes de las que se presentan aquí.

Tenga en cuenta que no hay ningún requisito para establecer el método de resolución de dependencias durante el inicio de la aplicación. Se puede establecer en cualquier momento. La única restricción es que Xamarin.Forms necesita conocer el método de resolución de dependencias en el momento en que la aplicación intenta consumir tipos almacenados en el contenedor de inserción de dependencias. Por lo tanto, si hay servicios en el contenedor de inserción de dependencias que la aplicación necesitará durante el inicio, el método de resolución de dependencias tendrá que establecerse en una fase temprana del ciclo de vida de la aplicación. Del mismo modo, si el contenedor de inserción de dependencias administra la creación y la duración de un determinado [`Effect`](xref:Xamarin.Forms.Effect) , Xamarin.Forms deberá conocer el método de resolución de dependencias antes de intentar crear una vista que lo use `Effect` .

> [!WARNING]
> El registro y la resolución de tipos con un contenedor de inserción de dependencias tiene un costo de rendimiento debido al uso del contenedor de la reflexión para crear cada tipo, especialmente si se reconstruyen las dependencias para cada navegación de página en la aplicación. Si hay muchas dependencias, o estas son muy amplias, el costo de la creación puede aumentar significativamente.

## <a name="registering-types"></a>Registrar tipos

Los tipos se deben registrar con el contenedor de inserción de dependencias para poder resolverlos a través del método de resolución de dependencias. En el ejemplo de código siguiente se muestran los métodos de registro que la aplicación de ejemplo expone en la `App` clase para el contenedor Autofac:

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

Cuando una aplicación usa un método de resolución de dependencias para resolver tipos de un contenedor, los registros de tipo se suelen realizar desde proyectos de plataforma. Esto permite a los proyectos de plataforma registrar los tipos de representadores, efectos e [`DependencyService`](xref:Xamarin.Forms.DependencyService) implementaciones personalizados.

Después del registro de tipos de un proyecto de plataforma, el `IContainer` objeto se debe compilar, lo que se logra mediante una llamada al `BuildContainer` método. Este método invoca `Build` el método de Autofac en la `ContainerBuilder` instancia de, que crea un nuevo contenedor de inserción de dependencias que contiene los registros que se han realizado.

En las secciones siguientes, `Logger` se inserta una clase que implementa la `ILogger` interfaz en constructores de clase. La `Logger` clase implementa la funcionalidad de registro simple mediante el `Debug.WriteLine` método y se usa para mostrar cómo se pueden insertar los servicios en representadores, efectos e [`DependencyService`](xref:Xamarin.Forms.DependencyService) implementaciones personalizados.

### <a name="registering-custom-renderers"></a>Registrar representadores personalizados

La aplicación de ejemplo incluye una página que reproduce vídeos Web, cuyo origen XAML se muestra en el ejemplo siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             ...>
    <video:VideoPlayer Source="https://archive.org/download/BigBuckBunny_328/BigBuckBunny_512kb.mp4" />
</ContentPage>
```

`VideoPlayer`Una clase implementa la vista en cada plataforma `VideoPlayerRenderer` , que proporciona la funcionalidad para reproducir el vídeo. Para obtener más información sobre estas clases de representadores personalizados, vea [implementar un reproductor de vídeo](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md).

En iOS y en el Plataforma universal de Windows (UWP), las `VideoPlayerRenderer` clases tienen el siguiente constructor, que requiere un `ILogger` argumento:

```csharp
public VideoPlayerRenderer(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

En todas las plataformas, escriba el registro con el contenedor de inserción de dependencias se realiza mediante el `RegisterTypes` método, que se invoca antes de la plataforma que carga la aplicación con el `LoadApplication(new App())` método. En el ejemplo siguiente se muestra el `RegisterTypes` método en la plataforma iOS:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<FormsVideoLibrary.iOS.VideoPlayerRenderer>();
    App.BuildContainer();
}
```

En este ejemplo, el `Logger` tipo concreto se registra mediante una asignación en su tipo de interfaz y el `VideoPlayerRenderer` tipo se registra directamente sin una asignación de interfaz. Cuando el usuario navega a la página que contiene la `VideoPlayer` vista, se invocará el método de resolución de dependencias para resolver el `VideoPlayerRenderer` tipo del contenedor de inserción de dependencias, que también resolverá e insertará el `Logger` tipo en el `VideoPlayerRenderer` constructor.

El `VideoPlayerRenderer` constructor de la plataforma Android es ligeramente más complicado, ya que requiere un `Context` argumento además del `ILogger` argumento:

```csharp
public VideoPlayerRenderer(Context context, ILogger logger) : base(context)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

En el ejemplo siguiente se muestra el `RegisterTypes` método en la plataforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<FormsVideoLibrary.Droid.VideoPlayerRenderer>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

En este ejemplo, el `App.RegisterTypeWithParameters` método registra `VideoPlayerRenderer` con el contenedor de inserción de dependencias. El método de registro garantiza que la `MainActivity` instancia se insertará como `Context` argumento y que el tipo se `Logger` insertará como el `ILogger` argumento.

### <a name="registering-effects"></a>Registrar efectos

La aplicación de ejemplo incluye una página que utiliza un efecto de seguimiento táctil para arrastrar [`BoxView`](xref:Xamarin.Forms.BoxView) instancias alrededor de la página. El [`Effect`](xref:Xamarin.Forms.Effect) se agrega a `BoxView` mediante el código siguiente:

```csharp
var boxView = new BoxView { ... };
var touchEffect = new TouchEffect();
boxView.Effects.Add(touchEffect);
```

La `TouchEffect` clase es un [`RoutingEffect`](xref:Xamarin.Forms.RoutingEffect) que se implementa en cada plataforma mediante una `TouchEffect` clase que es `PlatformEffect` . La `TouchEffect` clase Platform proporciona la funcionalidad para arrastrar el `BoxView` alrededor de la página. Para obtener más información sobre estas clases de efectos, vea [invocación de eventos a partir de efectos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md).

En todas las plataformas, la `TouchEffect` clase tiene el siguiente constructor, que requiere un `ILogger` argumento:

```csharp
public TouchEffect(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

En todas las plataformas, escriba el registro con el contenedor de inserción de dependencias se realiza mediante el `RegisterTypes` método, que se invoca antes de la plataforma que carga la aplicación con el `LoadApplication(new App())` método. En el ejemplo siguiente se muestra el `RegisterTypes` método en la plataforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterType<TouchTracking.Droid.TouchEffect>();
    App.BuildContainer();
}
```

En este ejemplo, el `Logger` tipo concreto se registra mediante una asignación en su tipo de interfaz y el `TouchEffect` tipo se registra directamente sin una asignación de interfaz. Cuando el usuario navega a la página que contiene una [`BoxView`](xref:Xamarin.Forms.BoxView) instancia de que tiene `TouchEffect` adjunta el objeto, se invocará el método de resolución de dependencias para resolver el `TouchEffect` tipo de plataforma del contenedor de inserción de dependencias, que también resolverá e insertará el `Logger` tipo en el `TouchEffect` constructor.

### <a name="registering-dependencyservice-implementations"></a>Registrar implementaciones de DependencyService

La aplicación de ejemplo incluye una página que usa [`DependencyService`](xref:Xamarin.Forms.DependencyService) implementaciones en cada plataforma para permitir que el usuario elija una foto de la biblioteca de imágenes del dispositivo. La `IPhotoPicker` interfaz define la funcionalidad que implementan las `DependencyService` implementaciones y se muestra en el ejemplo siguiente:

```csharp
public interface IPhotoPicker
{
    Task<Stream> GetImageStreamAsync();
}
```

En cada proyecto de la plataforma, la `PhotoPicker` clase implementa la `IPhotoPicker` interfaz mediante las API de la plataforma. Para obtener más información acerca de estos servicios de dependencia, consulte [seleccionar una foto de la biblioteca de imágenes](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

En iOS y UWP, las `PhotoPicker` clases tienen el siguiente constructor, que requiere un `ILogger` argumento:

```csharp
public PhotoPicker(ILogger logger)
{
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

En todas las plataformas, escriba el registro con el contenedor de inserción de dependencias se realiza mediante el `RegisterTypes` método, que se invoca antes de la plataforma que carga la aplicación con el `LoadApplication(new App())` método. En el ejemplo siguiente se muestra el `RegisterTypes` método en UWP:

```csharp
void RegisterTypes()
{
    DIContainerDemo.App.RegisterType<ILogger, Logger>();
    DIContainerDemo.App.RegisterType<IPhotoPicker, Services.UWP.PhotoPicker>();
    DIContainerDemo.App.BuildContainer();
}
```

En este ejemplo, el `Logger` tipo concreto se registra mediante una asignación en su tipo de interfaz y el `PhotoPicker` tipo también se registra a través de una asignación de interfaz.

El `PhotoPicker` constructor de la plataforma Android es ligeramente más complicado, ya que requiere un `Context` argumento además del `ILogger` argumento:

```csharp
public PhotoPicker(Context context, ILogger logger)
{
    _context = context ?? throw new ArgumentNullException(nameof(context));
    _logger = logger ?? throw new ArgumentNullException(nameof(logger));
}
```

En el ejemplo siguiente se muestra el `RegisterTypes` método en la plataforma Android:

```csharp
void RegisterTypes()
{
    App.RegisterType<ILogger, Logger>();
    App.RegisterTypeWithParameters<IPhotoPicker, Services.Droid.PhotoPicker>(typeof(Android.Content.Context), this, typeof(ILogger), "logger");
    App.BuildContainer();
}
```

En este ejemplo, el `App.RegisterTypeWithParameters` método registra `PhotoPicker` con el contenedor de inserción de dependencias. El método de registro garantiza que la `MainActivity` instancia se insertará como `Context` argumento y que el tipo se `Logger` insertará como el `ILogger` argumento.

Cuando el usuario navega a la página de selección de fotografías y elige seleccionar una foto, `OnSelectPhotoButtonClicked` se ejecuta el controlador:

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

Cuando [`DependencyService.Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) se invoca el método, se invocará el método de resolución de dependencias para resolver el `PhotoPicker` tipo del contenedor de inserción de dependencias, que también resolverá e insertará el `Logger` tipo en el `PhotoPicker` constructor.

> [!NOTE]
> El [`Resolve<T>`](xref:Xamarin.Forms.DependencyService.Resolve*) método se debe usar al resolver un tipo desde el contenedor de inserción de dependencias de la aplicación a través de [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

## <a name="related-links"></a>Vínculos relacionados

- [Resolución de dependencias mediante contenedores (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/advanced-dependencyresolution-dicontainerdemo)
- [Inserción de dependencia](~/xamarin-forms/enterprise-application-patterns/dependency-injection.md)
- [Implementación de un reproductor de vídeo](~/xamarin-forms/app-fundamentals/custom-renderer/video-player/index.md)
- [Invocar eventos a partir de efectos](~/xamarin-forms/app-fundamentals/effects/touch-tracking.md)
- [Seleccionar una foto de la biblioteca de imágenes](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
