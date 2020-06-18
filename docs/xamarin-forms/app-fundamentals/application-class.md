---
title: 'title: "Xamarin.Forms: Clase App" description: "En este artículo se explican las características de la clase App predeterminada, lo que incluye una propiedad para establecer la página inicial de la aplicación y un diccionario persistente para almacenar valores simples a lo largo de los cambios de estado del ciclo de vida."'
description: 'ms.prod: xamarin ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D ms.technology: xamarin-forms ms.custom: video author: davidbritch ms.author: dabritch ms.date: 02/19/2016 no-loc: [Xamarin.Forms, Xamarin.Essentials]'
ms.prod: xamarin
ms.assetid: 421F8294-1944-46A4-8459-D2BD5AAABC9D
ms.technology: xamarin-forms
ms.custom: video
author: davidbritch
ms.author: dabritch
ms.date: 02/19/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1e761830fb08662a82a8bf98b4544aafaa6eccf7
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84573357"
---
# <a name="xamarinforms-app-class"></a>Clase de aplicación de Xamarin.Forms

La clase base `Application` ofrece las siguientes características, que se exponen en la subclase predeterminada `App` de los proyectos:

* Una propiedad `MainPage`, que es donde se establece la página inicial de la aplicación.
* Un [diccionario](#properties-dictionary)`Properties` persistente para almacenar valores simples a lo largo de los cambios de estado del ciclo de vida.
* Una propiedad estática `Current` que contiene una referencia al objeto de aplicación actual.

También expone [Métodos del ciclo de vida](~/xamarin-forms/app-fundamentals/app-lifecycle.md) como `OnStart`, `OnSleep` y `OnResume`, así como eventos de navegación modales.

Según la plantilla que se elija, la clase `App` puede definirse de alguna de estas dos maneras:

* **C#** , o bien
* **XAML y C#**

Para crear una clase **App** mediante XAML, se debe reemplazar la clase **App** predeterminada por una clase **App** de XAML y el código subyacente asociado, como se muestra en el ejemplo de código siguiente:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Photos.App">

</Application>
```

El ejemplo de código siguiente muestra el código subyacente asociado:

```csharp
public partial class App : Application
{
    public App ()
    {
        InitializeComponent ();
        MainPage = new HomePage ();
    }
    ...
}
```

Además de establecer la propiedad [`MainPage`](xref:Xamarin.Forms.Application.MainPage), el código subyacente también debe llamar al método `InitializeComponent` para cargar y analizar el XAML asociado.

## <a name="mainpage-property"></a>Propiedad MainPage

La propiedad `MainPage` de la clase `Application` establece la página raíz de la aplicación.

Por ejemplo, puede crear lógica en la clase `App` para mostrar otra página en función de si el usuario ha iniciado sesión o no.

La propiedad `MainPage` debe establecerse en el constructor `App`,

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }
}
```

## <a name="properties-dictionary"></a>Diccionario de propiedades

La subclase `Application` tiene un diccionario estático `Properties` que se puede usar para almacenar datos, en particular para su uso en los métodos `OnStart`, `OnSleep` y `OnResume`. Se puede acceder a él desde cualquier lugar del código de Xamarin.Forms con `Application.Current.Properties`.

El diccionario `Properties` usa una clave `string` y almacena un valor `object`.

Por ejemplo, puede establecer una propiedad persistente `"id"` en cualquier lugar del código (al seleccionar un elemento, en el método `OnDisappearing` de una página, o en el método `OnSleep`) del modo siguiente:

```csharp
Application.Current.Properties ["id"] = someClass.ID;
```

En los métodos `OnStart` u `OnResume` puede usar este valor para volver a crear la experiencia del usuario de alguna manera. El diccionario `Properties` almacena elementos `object`, por lo que debe convertir su valor antes de usarlo.

```csharp
if (Application.Current.Properties.ContainsKey("id"))
{
    var id = Application.Current.Properties ["id"] as int;
    // do something with id
}
```

Verifique siempre la presencia de la clave antes de acceder para evitar errores inesperados.

> [!NOTE]
> El diccionario `Properties` solo puede serializar tipos primitivos para el almacenamiento. El intento de almacenar otros tipos (como `List<string>`) puede producir un error en modo silencioso.

<!-- bugzilla 28657 -->

### <a name="persistence"></a>Persistencia

El diccionario `Properties` se guarda automáticamente en el dispositivo.
Los datos agregados al diccionario están disponibles cuando la aplicación vuelve desde el segundo plano o incluso después de su reinicio.

Xamarin.Forms 1.4 ha incorporado un método adicional a la clase `Application` (`SavePropertiesAsync()`) al que se puede llamar para conservar de forma proactiva el diccionario `Properties`. Esto permite guardar propiedades después de actualizaciones importantes en lugar de arriesgarse a que no se serialicen debido a un bloqueo o a su eliminación por parte del sistema operativo.

Encontrará referencias al uso del diccionario `Properties` en los capítulos [6](https://developer.xamarin.com/r/xamarin-forms/book/chapter06.pdf), [15](https://developer.xamarin.com/r/xamarin-forms/book/chapter15.pdf) y [20](https://developer.xamarin.com/r/xamarin-forms/book/chapter20.pdf) del libro **Creating Mobile Apps with Xamarin.Forms** (Creación de aplicaciones móviles con Xamarin.Forms) y en los [ejemplos](https://github.com/xamarin/xamarin-forms-book-preview-2) asociados.

## <a name="the-application-class"></a>La clase Application

A continuación se muestra una completa implementación de la clase `Application` como referencia:

```csharp
public class App : Xamarin.Forms.Application
{
    public App ()
    {
        MainPage = new ContentPage { Title = "App Lifecycle Sample" }; // your page here
    }

    protected override void OnStart()
    {
        // Handle when your app starts
        Debug.WriteLine ("OnStart");
    }

    protected override void OnSleep()
    {
        // Handle when your app sleeps
        Debug.WriteLine ("OnSleep");
    }

    protected override void OnResume()
    {
        // Handle when your app resumes
        Debug.WriteLine ("OnResume");
    }
}
```

Luego se crean instancias de esta clase en cada proyecto específico de plataforma y se pasa al método `LoadApplication`, que es donde se carga `MainPage` y se muestra al usuario.
El código para cada plataforma se muestra en las secciones siguientes. Las plantillas de solución de Xamarin.Forms más recientes ya contienen todo este código, preconfigurado para la aplicación.

### <a name="ios-project"></a>Proyecto de iOS

La clase `AppDelegate` de iOS hereda de `FormsApplicationDelegate`. Debe:

* Llamar a `LoadApplication` con una instancia de la clase `App`.

* Devolver siempre `base.FinishedLaunching (app, options);`.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="android-project"></a>Proyecto de Android

`MainActivity` de Android hereda de `FormsAppCompatActivity`. En la invalidación `OnCreate`, se llama al método `LoadApplication` con una instancia de la clase `App`.

```csharp
[Activity (Label = "App Lifecycle Sample", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true,
    ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity : FormsAppCompatActivity
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="universal-windows-project-uwp-for-windows-10"></a>Proyecto universal de Windows (UWP) para Windows 10

La página principal del proyecto de UWP debe heredar de `WindowsPage`:

```xaml
<forms:WindowsPage
   ...
   xmlns:forms="using:Xamarin.Forms.Platform.UWP"
   ...>
</forms:WindowsPage>
```

El código de C# detrás de la construcción debe llamar a `LoadApplication` para crear una instancia del elemento Xamarin.Forms de `App`. Tenga en cuenta que es recomendable usar explícitamente el espacio de nombres de aplicación para calificar a `App`, dado que las aplicaciones de UWP también tienen su propia clase `App` no relacionada con Xamarin.Forms.

```csharp
public sealed partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();

        LoadApplication(new YOUR_NAMESPACE.App());
    }
 }
```

Tenga en cuenta que `Forms.Init()` debe llamarse desde **App.xaml.cs** en el proyecto UWP.

Para obtener más información, consulte [Configurar proyectos de Windows](~/xamarin-forms/platform/windows/installation/index.md), donde se incluyen instrucciones para agregar un proyecto UWP a una solución existente de Xamarin.Forms cuyo destino no es UWP.

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Series/Xamarin-101/Xamarin-Solution-Architecture-4-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
