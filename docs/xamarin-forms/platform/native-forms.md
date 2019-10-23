---
title: Xamarin. Forms en proyectos nativos de Xamarin
description: En este artículo se explica cómo consumir páginas derivadas de ContentPage que se agregan directamente a proyectos de Xamarin Native y cómo navegar entre ellas.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
ms.openlocfilehash: 0c84b844455b8a792b8cbe2f4dac97097e5ebd97
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2019
ms.locfileid: "69621060"
---
# <a name="xamarinforms-in-xamarin-native-projects"></a>Xamarin. Forms en proyectos nativos de Xamarin

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)

Normalmente, una aplicación de Xamarin. Forms incluye una o varias páginas que se derivan de [`ContentPage`](xref:Xamarin.Forms.ContentPage)y todas las plataformas comparten las mismas en un proyecto de biblioteca de .net Standard o un proyecto compartido. Sin embargo, los formularios nativos permiten agregar páginas derivadas de `ContentPage` directamente a aplicaciones de Xamarin. iOS, Xamarin. Android y UWP nativas. En comparación con el hecho de que el proyecto nativo consuma páginas derivadas de `ContentPage` de un proyecto de biblioteca de .NET Standard o un proyecto compartido, la ventaja de agregar páginas directamente a proyectos nativos es que las páginas se pueden extender con vistas nativas. A continuación, las vistas nativas se pueden denominar en XAML con `x:Name` y a las que se hace referencia desde el código subyacente. Para obtener más información sobre las vistas nativas, vea [vistas nativas](~/xamarin-forms/platform/native-views/index.md).

El proceso de consumo de una página derivada de Xamarin. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)en un proyecto nativo es el siguiente:

1. Agregue el paquete de NuGet de Xamarin. Forms al proyecto nativo.
1. Agregue la página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)y las dependencias al proyecto nativo.
1. Llame al método `Forms.Init`.
1. Construya una instancia de la página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)y conviértala en el tipo nativo adecuado mediante uno de los siguientes métodos de extensión: `CreateViewController` para iOS, `CreateSupportFragment` para Android o `CreateFrameworkElement` para UWP.
1. Navegue a la representación de tipo nativo de la página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)mediante la API de navegación nativa.

Xamarin. Forms se debe inicializar llamando al método `Forms.Init` antes de que un proyecto nativo pueda construir una página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Elegir cuándo hacerlo depende principalmente del momento en el que sea más conveniente en el flujo de la aplicación: puede realizarse durante el inicio de la aplicación o justo antes de que se construya la página derivada de `ContentPage`. En este artículo y en las aplicaciones de ejemplo que lo acompañan, se llama al método `Forms.Init` al inicio de la aplicación.

> [!NOTE]
> La solución de aplicación de ejemplo **NativeForms** no contiene ningún proyecto de Xamarin. Forms. En su lugar, consta de un proyecto de Xamarin. iOS, un proyecto de Xamarin. Android y un proyecto de UWP. Cada proyecto es un proyecto nativo que utiliza formularios nativos para consumir páginas derivadas de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Sin embargo, no hay ningún motivo por el que los proyectos nativos no hayan podido consumir páginas derivadas de `ContentPage` de un proyecto de biblioteca de .NET Standard o un proyecto compartido.

Al usar formularios nativos, las características de Xamarin. Forms como [`DependencyService`](xref:Xamarin.Forms.DependencyService), [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)y el motor de enlace de datos siguen funcionando. Sin embargo, la navegación de página debe realizarse mediante la API de navegación nativa.

## <a name="ios"></a>iOS

En iOS, el `FinishedLaunching` invalidación en la clase `AppDelegate` es normalmente el lugar para realizar tareas relacionadas con el inicio de la aplicación. Se llama después de que se haya iniciado la aplicación y normalmente se invalida para configurar la ventana principal y el controlador de vista. En el ejemplo de código siguiente se muestra la clase `AppDelegate` en la aplicación de ejemplo:

```csharp
[Register("AppDelegate")]
public class AppDelegate : UIApplicationDelegate
{
    public static AppDelegate Instance;
    UIWindow _window;
    AppNavigationController _navigation;

    public static string FolderPath { get; private set; }

    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        Forms.Init();

        Instance = this;
        _window = new UIWindow(UIScreen.MainScreen.Bounds);

        UINavigationBar.Appearance.SetTitleTextAttributes(new UITextAttributes
        {
            TextColor = UIColor.Black
        });

        FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
        UIViewController mainPage = new NotesPage().CreateViewController();
        mainPage.Title = "Notes";

        _navigation = new AppNavigationController(mainPage);
        _window.RootViewController = _navigation;
        _window.MakeKeyAndVisible();

        return true;
    }
    // ...
}
```

El método `FinishedLaunching` realiza las siguientes tareas:

- Xamarin. Forms se inicializa llamando al método `Forms.Init`.
- Una referencia a la clase `AppDelegate` se almacena en el campo `Instance` `static`. Esto es para proporcionar un mecanismo para que otras clases llamen a métodos definidos en la clase `AppDelegate`.
- Se crea el `UIWindow`, que es el contenedor principal de las vistas en aplicaciones nativas de iOS.
- La propiedad `FolderPath` se inicializa en una ruta de acceso en el dispositivo donde se almacenarán los datos de la nota.
- La clase `NotesPage`, que es una página derivada de Xamarin. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)definida en XAML, se construye y se convierte en una `UIViewController` mediante el método de extensión `CreateViewController`.
- Se establece la propiedad `Title` del `UIViewController`, que se mostrará en la `UINavigationBar`.
- Se crea un `AppNavigationController` para administrar la navegación jerárquica. Se trata de una clase de controlador de navegación personalizada, que deriva de `UINavigationController`. El objeto `AppNavigationController` administra una pila de controladores de vista y el `UIViewController` que se pasa al constructor se presentará inicialmente cuando se cargue la `AppNavigationController`.
- El objeto `AppNavigationController` se establece como `UIViewController` de nivel superior para el `UIWindow` y el `UIWindow` se establece como la ventana clave de la aplicación y se hace visible.

Una vez que se ha ejecutado el método de `FinishedLaunching`, se mostrará la interfaz de usuario definida en la clase de `NotesPage` de Xamarin. Forms, como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de una aplicación de Xamarin. iOS que usa una interfaz de usuario definida en XAML](native-forms-images/ios-notespage.png "Aplicación Xamarin. iOS con una interfaz de usuario XAML")](native-forms-images/ios-notespage-large.png#lightbox "Aplicación Xamarin. iOS con una interfaz de usuario XAML")

Al interactuar con la interfaz de usuario, por ejemplo, al puntear en el [`Button`](xref:Xamarin.Forms.Button) **+** , se producirá el siguiente controlador de eventos en el `NotesPage` la ejecución del código subyacente:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

El campo `static` `AppDelegate.Instance` permite invocar el método `AppDelegate.NavigateToNoteEntryPage`, que se muestra en el ejemplo de código siguiente:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    var noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateViewController();
    noteEntryPage.Title = "Note Entry";
    _navigation.PushViewController(noteEntryPage, true);
}
```

El método `NavigateToNoteEntryPage` convierte la página derivada de Xamarin. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)en un `UIViewController` con el método de extensión `CreateViewController` y establece la propiedad `Title` de la `UIViewController`. A continuación, el método `PushViewController` inserta el `UIViewController` en `AppNavigationController`. Por lo tanto, se mostrará la interfaz de usuario definida en la clase `NoteEntryPage` de Xamarin. Forms, como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de una aplicación de Xamarin. iOS que usa una interfaz de usuario definida en XAML](native-forms-images/ios-noteentrypage.png "Aplicación Xamarin. iOS con una interfaz de usuario XAML")](native-forms-images/ios-noteentrypage-large.png#lightbox "Aplicación Xamarin. iOS con una interfaz de usuario XAML")

Cuando se muestra el `NoteEntryPage`, la navegación hacia atrás extrae el `UIViewController` de la clase `NoteEntryPage` de la `AppNavigationController`, devolviendo el usuario a la `UIViewController` de la clase `NotesPage`. Sin embargo, si se extrae un `UIViewController` de la pila de navegación nativa de iOS, no se elimina automáticamente el `UIViewController` y el objeto `Page` asociado. Por lo tanto, la clase `AppNavigationController` invalida el método `PopViewController`, para desechar los controladores de vista en la navegación hacia atrás:

```csharp
public class AppNavigationController : UINavigationController
{
    //...
    public override UIViewController PopViewController(bool animated)
    {
        UIViewController topView = TopViewController;
        if (topView != null)
        {
            // Dispose of ViewController on back navigation.
            topView.Dispose();
        }
        return base.PopViewController(animated);
    }
}
```

La invalidación `PopViewController` llama al método `Dispose` en el objeto `UIViewController` que se ha sacado de la pila de navegación nativa de iOS. Si no lo hace, se producirá el `UIViewController` y el objeto `Page` asociado quede huérfano.

> [!IMPORTANT]
> Los objetos huérfanos no se pueden recolectar como elementos no utilizados y, por tanto, se produce una fuga de memoria.

## <a name="android"></a>Android

En Android, el `OnCreate` invalidación en la clase `MainActivity` es normalmente el lugar para realizar tareas relacionadas con el inicio de la aplicación. En el ejemplo de código siguiente se muestra la clase `MainActivity` en la aplicación de ejemplo:

```csharp
public class MainActivity : AppCompatActivity
{
    public static string FolderPath { get; private set; }

    public static MainActivity Instance;

    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        Forms.Init(this, bundle);
        Instance = this;

        SetContentView(Resource.Layout.Main);
        var toolbar = FindViewById<Toolbar>(Resource.Id.toolbar);
        SetSupportActionBar(toolbar);
        SupportActionBar.Title = "Notes";

        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        Android.Support.V4.App.Fragment mainPage = new NotesPage().CreateSupportFragment(this);
        SupportFragmentManager
            .BeginTransaction()
            .Replace(Resource.Id.fragment_frame_layout, mainPage)
            .Commit();
        ...
    }
    ...
}
```

El método `OnCreate` realiza las siguientes tareas:

- Xamarin. Forms se inicializa llamando al método `Forms.Init`.
- Una referencia a la clase `MainActivity` se almacena en el campo `Instance` `static`. Esto es para proporcionar un mecanismo para que otras clases llamen a métodos definidos en la clase `MainActivity`.
- El contenido de `Activity` se establece desde un recurso de diseño. En la aplicación de ejemplo, el diseño está compuesto de un `LinearLayout` que contiene un `Toolbar` y un `FrameLayout` para actuar como contenedor de fragmentos.
- El `Toolbar` se recupera y se establece como la barra de acción para el `Activity` y se establece el título de la barra de acciones.
- La propiedad `FolderPath` se inicializa en una ruta de acceso en el dispositivo donde se almacenarán los datos de la nota.
- La clase `NotesPage`, que es una página derivada de Xamarin. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)definida en XAML, se construye y se convierte en una `Fragment` mediante el método de extensión `CreateSupportFragment`.
- La clase `SupportFragmentManager` crea y confirma una transacción que reemplaza la instancia de `FrameLayout` por la `Fragment` para la clase `NotesPage`.

Para obtener más información acerca de los fragmentos, vea [fragmentos](~/android/platform/fragments/index.md).

Una vez que se ha ejecutado el método de `OnCreate`, se mostrará la interfaz de usuario definida en la clase de `NotesPage` de Xamarin. Forms, como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de una aplicación de Xamarin. Android que usa una interfaz de usuario definida en XAML](native-forms-images/android-notespage.png "Aplicación Xamarin. Android con una interfaz de usuario XAML")](native-forms-images/android-notespage-large.png#lightbox "Aplicación Xamarin. Android con una interfaz de usuario XAML")

Al interactuar con la interfaz de usuario, por ejemplo, al puntear en el [`Button`](xref:Xamarin.Forms.Button) **+** , se producirá el siguiente controlador de eventos en el `NotesPage` la ejecución del código subyacente:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

El campo `static` `MainActivity.Instance` permite invocar el método `MainActivity.NavigateToNoteEntryPage`, que se muestra en el ejemplo de código siguiente:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    Android.Support.V4.App.Fragment noteEntryPage = new NoteEntryPage
    {
        BindingContext = note
    }.CreateSupportFragment(this);
    SupportFragmentManager
        .BeginTransaction()
        .AddToBackStack(null)
        .Replace(Resource.Id.fragment_frame_layout, noteEntryPage)
        .Commit();
}
```

El método `NavigateToNoteEntryPage` convierte la página derivada de Xamarin. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)en un `Fragment` con el método de extensión `CreateSupportFragment` y agrega el `Fragment` a la pila de retroceso de fragmento. Por lo tanto, se mostrará la interfaz de usuario definida en el `NoteEntryPage` de Xamarin. Forms, como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de una aplicación de Xamarin. Android que usa una interfaz de usuario definida en XAML](native-forms-images/android-noteentrypage.png "Aplicación Xamarin. Android con una interfaz de usuario XAML")](native-forms-images/android-noteentrypage-large.png#lightbox "Aplicación Xamarin. Android con una interfaz de usuario XAML")

Cuando se muestre el `NoteEntryPage`, al pulsar en la flecha atrás se mostrará el `Fragment` de la `NoteEntryPage` de la pila de retroceso de fragmento y se devolverá al usuario al `Fragment` de la clase `NotesPage`.

### <a name="enable-back-navigation-support"></a>Habilitar la compatibilidad con la navegación hacia atrás

La clase `SupportFragmentManager` tiene un evento `BackStackChanged` que se activa cada vez que cambia el contenido de la pila de retroceso de fragmento. El método `OnCreate` de la clase `MainActivity` contiene un controlador de eventos anónimo para este evento:

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

Este controlador de eventos muestra un botón atrás en la barra de acciones siempre que haya una o varias instancias de `Fragment` en la pila de retroceso de fragmento. La respuesta al puntear en el botón atrás se controla mediante la invalidación del `OnOptionsItemSelected`:

```csharp
public override bool OnOptionsItemSelected(Android.Views.IMenuItem item)
{
    if (item.ItemId == global::Android.Resource.Id.Home && SupportFragmentManager.BackStackEntryCount > 0)
    {
        SupportFragmentManager.PopBackStack();
        return true;
    }
    return base.OnOptionsItemSelected(item);
}
```

Se llama al reemplazo `OnOptionsItemSelected` siempre que se selecciona un elemento en el menú opciones. Esta implementación extrae el fragmento actual de la pila de retroceso de fragmento, siempre que se haya seleccionado el botón atrás y haya una o varias instancias de `Fragment` en la pila de retroceso de fragmento.

### <a name="multiple-activities"></a>Varias actividades

Cuando una aplicación se compone de varias actividades, las páginas derivadas de [`ContentPage`](xref:Xamarin.Forms.ContentPage)se pueden incrustar en cada una de las actividades. En este escenario, solo es necesario llamar al método `Forms.Init` en la invalidación `OnCreate` de la primera `Activity` que inserta un `ContentPage` de Xamarin. Forms. Sin embargo, esto tiene el impacto siguiente:

- El valor de `Xamarin.Forms.Color.Accent` se tomará de la `Activity` que llamó al método `Forms.Init`.
- El valor de `Xamarin.Forms.Application.Current` se asociará al `Activity` que llamó al método `Forms.Init`.

### <a name="choose-a-file"></a>Elegir un archivo

Al incrustar una página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage)que utiliza un [`WebView`](xref:Xamarin.Forms.WebView) que necesita admitir un botón "elegir archivo" HTML, el `Activity` deberá invalidar el método de `OnActivityResult`:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

En UWP, la clase de `App` nativa es normalmente el lugar en el que se realizan las tareas relacionadas con el inicio de la aplicación. Normalmente, Xamarin. Forms se inicializa, en las aplicaciones para UWP de Xamarin. Forms, en el `OnLaunched` invalidar en la clase de `App` nativa, para pasar el argumento `LaunchActivatedEventArgs` al método `Forms.Init`. Por este motivo, las aplicaciones de UWP nativas que consumen una página derivada de Xamarin. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)pueden llamar más fácilmente al método `Forms.Init` desde el método `App.OnLaunched`.

De forma predeterminada, la clase `App` nativa inicia la clase `MainPage` como la primera página de la aplicación. En el ejemplo de código siguiente se muestra la clase `MainPage` en la aplicación de ejemplo:

```csharp
public sealed partial class MainPage : Page
{
    public static MainPage Instance;

    public static string FolderPath { get; private set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        this.Content = new Notes.UWP.Views.NotesPage().CreateFrameworkElement();
    }
    ...
}
```

El constructor `MainPage` realiza las siguientes tareas:

- El almacenamiento en caché está habilitado para la página, por lo que no se crea un nuevo `MainPage` cuando un usuario vuelve a la página.
- Una referencia a la clase `MainPage` se almacena en el campo `Instance` `static`. Esto es para proporcionar un mecanismo para que otras clases llamen a métodos definidos en la clase `MainPage`.
- La propiedad `FolderPath` se inicializa en una ruta de acceso en el dispositivo donde se almacenarán los datos de la nota.
- La clase `NotesPage`, que es una página derivada de Xamarin. Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage)definida en XAML, se construye y se convierte en un `FrameworkElement` mediante el método de extensión `CreateFrameworkElement` y, a continuación, se establece como el contenido de la clase `MainPage`.

Una vez que se ha ejecutado el constructor de `MainPage`, se mostrará la interfaz de usuario definida en la clase de `NotesPage` de Xamarin. Forms, como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de una aplicación de UWP que usa una interfaz de usuario definida con Xamarin. Forms XAML](native-forms-images/uwp-notespage.png "Aplicación para UWP con una interfaz de usuario XAML de Xamarin. Forms")](native-forms-images/uwp-notespage-large.png#lightbox "Aplicación para UWP con una interfaz de usuario XAML de Xamarin. Forms")

Al interactuar con la interfaz de usuario, por ejemplo, al puntear en el [`Button`](xref:Xamarin.Forms.Button) **+** , se producirá el siguiente controlador de eventos en el `NotesPage` la ejecución del código subyacente:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

El campo `static` `MainPage.Instance` permite invocar el método `MainPage.NavigateToNoteEntryPage`, que se muestra en el ejemplo de código siguiente:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    this.Frame.Navigate(new NoteEntryPage
    {
        BindingContext = note
    });
}
```

La navegación en UWP se realiza normalmente con el método `Frame.Navigate`, que toma un argumento `Page`. Xamarin. Forms define un método de extensión `Frame.Navigate` que toma una instancia de página derivada de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Por lo tanto, cuando se ejecuta el método de `NavigateToNoteEntryPage`, se mostrará la interfaz de usuario definida en el `NoteEntryPage` de Xamarin. Forms, como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de una aplicación de UWP que usa una interfaz de usuario definida con Xamarin. Forms XAML](native-forms-images/uwp-noteentrypage.png "Aplicación para UWP con una interfaz de usuario XAML de Xamarin. Forms")](native-forms-images/uwp-noteentrypage-large.png#lightbox "Aplicación para UWP con una interfaz de usuario XAML de Xamarin. Forms")

Cuando se muestre el `NoteEntryPage`, al pulsar en la flecha atrás se mostrará el `FrameworkElement` de la `NoteEntryPage` desde la pila de retroceso de la aplicación, devolviendo al usuario a la `FrameworkElement` de la clase `NotesPage`.

### <a name="enable-back-navigation-support"></a>Habilitar la compatibilidad con la navegación hacia atrás

En UWP, las aplicaciones deben habilitar la navegación hacia atrás para todos los botones de retroceso de hardware y software, en distintos factores de forma de dispositivo. Esto puede realizarse mediante el registro de un controlador de eventos para el evento `BackRequested`, que se puede realizar en el método `OnLaunched` de la clase `App` nativa:

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;

    if (rootFrame == null)
    {
        ...      
        // Place the frame in the current Window
        Window.Current.Content = rootFrame;

        SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
    }
    ...
}
```

Cuando se inicia la aplicación, el método `GetForCurrentView` recupera el `SystemNavigationManager` objeto asociado a la vista actual y, después, registra un controlador de eventos para el evento `BackRequested`. La aplicación solo recibe este evento si es la aplicación de primer plano y, en respuesta, llama al controlador de eventos `OnBackRequested`:

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
    }
}
```

El controlador de eventos `OnBackRequested` llama al método `GoBack` en el marco raíz de la aplicación y establece la propiedad `BackRequestedEventArgs.Handled` en `true` para marcar el evento como controlado. Si no se marca el evento como controlado, se podría omitir el evento.

La aplicación elige si mostrar un botón atrás en la barra de título. Esto se consigue estableciendo la propiedad `AppViewBackButtonVisibility` en uno de los valores de enumeración de `AppViewBackButtonVisibility`:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

El controlador de eventos `OnNavigated`, que se ejecuta en respuesta a la activación del evento `Navigated`, actualiza la visibilidad del botón atrás de la barra de título cuando se produce la navegación por la página. Esto garantiza que el botón atrás de la barra de título está visible si la pila de retroceso de la aplicación no está vacía o se quita de la barra de título si la pila de retroceso en aplicación está vacía.

Para más información sobre la compatibilidad con la navegación hacia atrás en UWP, consulte [navegación por el historial de navegación y la navegación hacia atrás para aplicaciones UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="related-links"></a>Vínculos relacionados

- [NativeForms (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/native2forms)
- [Vistas nativas](~/xamarin-forms/platform/native-views/index.md)
