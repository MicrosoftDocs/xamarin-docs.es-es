---
title: Xamarin.Forms en proyectos nativos de Xamarin
description: En este artículo se explica cómo consumir páginas derivadas de ContentPage que se agregan directamente a proyectos de Xamarin Native y cómo navegar entre ellas.
ms.prod: xamarin
ms.assetid: f343fc21-dfb1-4364-a332-9da6705d36bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/19/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aec9f0ec0b3092a5f84f183fb90cfc8bc9da7324
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374230"
---
# <a name="no-locxamarinforms-in-xamarin-native-projects"></a>Xamarin.Forms en proyectos nativos de Xamarin

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/native2forms)

Normalmente, una Xamarin.Forms aplicación incluye una o varias páginas que se derivan de [`ContentPage`](xref:Xamarin.Forms.ContentPage) y estas páginas se comparten entre todas las plataformas de un proyecto de biblioteca de .net Standard o un proyecto compartido. Sin embargo, los formularios nativos permiten `ContentPage` Agregar páginas derivadas directamente a aplicaciones de Xamarin. iOS, Xamarin. Android y UWP nativas. En comparación con el hecho de que el proyecto nativo consuma `ContentPage` páginas derivadas de un proyecto de biblioteca de .net Standard o un proyecto compartido, la ventaja de agregar páginas directamente a proyectos nativos es que las páginas se pueden extender con vistas nativas. Las vistas nativas se pueden denominar en XAML con `x:Name` y se puede hacer referencia a ellas desde el código subyacente. Para obtener más información sobre las vistas nativas, vea [vistas nativas](~/xamarin-forms/platform/native-views/index.md).

El proceso de consumo de una Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) Página derivada de en un proyecto nativo es el siguiente:

1. Agregue el Xamarin.Forms paquete NuGet al proyecto nativo.
1. Agregue la [`ContentPage`](xref:Xamarin.Forms.ContentPage) Página derivada de y las dependencias al proyecto nativo.
1. Llame al método `Forms.Init`.
1. Construya una instancia de la [`ContentPage`](xref:Xamarin.Forms.ContentPage) Página derivada de y conviértala en el tipo nativo adecuado mediante uno de los siguientes métodos de extensión: `CreateViewController` para iOS, `CreateSupportFragment` para Android o `CreateFrameworkElement` para UWP.
1. Navegue a la representación de tipo nativo de la [`ContentPage`](xref:Xamarin.Forms.ContentPage) Página derivada de mediante la API de navegación nativa.

Xamarin.Forms se debe inicializar llamando al `Forms.Init` método antes de que un proyecto nativo pueda construir una [`ContentPage`](xref:Xamarin.Forms.ContentPage) Página derivada de. Elegir cuándo hacerlo depende principalmente del momento en el que sea más conveniente en el flujo de la aplicación: puede realizarse durante el inicio de la aplicación o justo antes de que `ContentPage` se construya la página derivada de. En este artículo y en las aplicaciones de ejemplo que lo acompañan, `Forms.Init` se llama al método durante el inicio de la aplicación.

> [!NOTE]
> La solución de aplicación de ejemplo **NativeForms** no contiene ningún Xamarin.Forms proyecto. En su lugar, consta de un proyecto de Xamarin. iOS, un proyecto de Xamarin. Android y un proyecto de UWP. Cada proyecto es un proyecto nativo que utiliza formularios nativos para consumir [`ContentPage`](xref:Xamarin.Forms.ContentPage) páginas derivadas de. Sin embargo, no hay ningún motivo por el que los proyectos nativos no hayan podido consumir `ContentPage` páginas derivadas de un proyecto de biblioteca de .net Standard o un proyecto compartido.

Cuando se usan formularios nativos, las Xamarin.Forms características como [`DependencyService`](xref:Xamarin.Forms.DependencyService) , [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) y el motor de enlace de datos siguen funcionando. Sin embargo, la navegación de página debe realizarse mediante la API de navegación nativa.

## <a name="ios"></a>iOS

En iOS, la `FinishedLaunching` invalidación en la `AppDelegate` clase suele ser el lugar en el que se realizan las tareas relacionadas con el inicio de la aplicación. Se llama después de que se haya iniciado la aplicación y normalmente se invalida para configurar la ventana principal y el controlador de vista. En el ejemplo de código siguiente se muestra la `AppDelegate` clase en la aplicación de ejemplo:

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

- Xamarin.Forms se inicializa llamando al `Forms.Init` método.
- `AppDelegate`En el campo se almacena una referencia a la clase `static` `Instance` . Esto es para proporcionar un mecanismo para que otras clases llamen a métodos definidos en la `AppDelegate` clase.
- `UIWindow`Se crea el, que es el contenedor principal de las vistas en aplicaciones nativas de iOS.
- La `FolderPath` propiedad se inicializa en una ruta de acceso en el dispositivo donde se almacenarán los datos de la nota.
- La `NotesPage` clase, que es una Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) Página derivada de definida en XAML, se construye y se convierte en `UIViewController` mediante el `CreateViewController` método de extensión.
- La `Title` propiedad de `UIViewController` se establece, que se mostrará en el `UINavigationBar` .
- `AppNavigationController`Se crea un para administrar la navegación jerárquica. Se trata de una clase de controlador de navegación personalizada, que se deriva de `UINavigationController` . El `AppNavigationController` objeto administra una pila de controladores de vista y el que se `UIViewController` pasa al constructor se presentará inicialmente cuando `AppNavigationController` se cargue.
- El `AppNavigationController` objeto se establece como el nivel superior `UIViewController` para `UIWindow` , y `UIWindow` se establece como la ventana de clave de la aplicación y se hace visible.

Una vez `FinishedLaunching` ejecutado el método, se mostrará la interfaz de usuario definida en la Xamarin.Forms `NotesPage` clase, como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de una aplicación de Xamarin. iOS que usa una interfaz de usuario definida en XAML](native-forms-images/ios-notespage.png "Aplicación Xamarin. iOS con una interfaz de usuario XAML")](native-forms-images/ios-notespage-large.png#lightbox "Aplicación Xamarin. iOS con una interfaz de usuario XAML")

La interacción con la interfaz de usuario, por ejemplo, al puntear en **+** [`Button`](xref:Xamarin.Forms.Button) , dará como resultado el siguiente controlador de eventos en la `NotesPage` ejecución del código subyacente:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    AppDelegate.Instance.NavigateToNoteEntryPage(new Note());
}
```

El `static` `AppDelegate.Instance` campo permite `AppDelegate.NavigateToNoteEntryPage` invocar el método, que se muestra en el ejemplo de código siguiente:

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

El `NavigateToNoteEntryPage` método convierte la Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) Página derivada de en una `UIViewController` con el `CreateViewController` método de extensión y establece la `Title` propiedad de `UIViewController` . `UIViewController`Después, se inserta en `AppNavigationController` el `PushViewController` método. Por lo tanto, se mostrará la interfaz de usuario definida en la Xamarin.Forms `NoteEntryPage` clase, como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de una aplicación de Xamarin. iOS que usa una interfaz de usuario definida en XAML](native-forms-images/ios-noteentrypage.png "Aplicación Xamarin. iOS con una interfaz de usuario XAML")](native-forms-images/ios-noteentrypage-large.png#lightbox "Aplicación Xamarin. iOS con una interfaz de usuario XAML")

Cuando `NoteEntryPage` se muestra, la navegación hacia atrás redirigirá la `UIViewController` para la `NoteEntryPage` clase de `AppNavigationController` y devolverá el usuario al `UIViewController` de la `NotesPage` clase. Sin embargo, si se extrae un `UIViewController` de la pila de navegación nativa de iOS, no se elimina automáticamente el `UIViewController` objeto asociado y `Page` . Por lo tanto, la `AppNavigationController` clase invalida el `PopViewController` método, para desechar los controladores de vista en la navegación hacia atrás:

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

La `PopViewController` invalidación llama al `Dispose` método en el `UIViewController` objeto que se ha sacado de la pila de navegación nativa de iOS. Si no se hace esto, se producirá el `UIViewController` huérfano y el `Page` objeto asociado.

> [!IMPORTANT]
> Los objetos huérfanos no se pueden recolectar como elementos no utilizados y, por tanto, se produce una fuga de memoria.

## <a name="android"></a>Android

En Android, la `OnCreate` invalidación en la `MainActivity` clase suele ser el lugar en el que se realizan las tareas relacionadas con el inicio de la aplicación. En el ejemplo de código siguiente se muestra la `MainActivity` clase en la aplicación de ejemplo:

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

- Xamarin.Forms se inicializa llamando al `Forms.Init` método.
- `MainActivity`En el campo se almacena una referencia a la clase `static` `Instance` . Esto es para proporcionar un mecanismo para que otras clases llamen a métodos definidos en la `MainActivity` clase.
- El `Activity` contenido se establece desde un recurso de diseño. En la aplicación de ejemplo, el diseño está compuesto de un `LinearLayout` que contiene un `Toolbar` y un `FrameLayout` para actuar como contenedor de fragmentos.
- `Toolbar`Se recupera y establece como la barra de acción para `Activity` , y se establece el título de la barra de acciones.
- La `FolderPath` propiedad se inicializa en una ruta de acceso en el dispositivo donde se almacenarán los datos de la nota.
- La `NotesPage` clase, que es una Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) Página derivada de definida en XAML, se construye y se convierte en `Fragment` mediante el `CreateSupportFragment` método de extensión.
- La `SupportFragmentManager` clase crea y confirma una transacción que reemplaza a la `FrameLayout` instancia `Fragment` de por la `NotesPage` clase.

Para obtener más información acerca de los fragmentos, vea [fragmentos](~/android/platform/fragments/index.md).

Una vez `OnCreate` ejecutado el método, se mostrará la interfaz de usuario definida en la Xamarin.Forms `NotesPage` clase, como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de una aplicación de Xamarin. Android que usa una interfaz de usuario definida en XAML](native-forms-images/android-notespage.png "Aplicación Xamarin. Android con una interfaz de usuario XAML")](native-forms-images/android-notespage-large.png#lightbox "Aplicación Xamarin. Android con una interfaz de usuario XAML")

La interacción con la interfaz de usuario, por ejemplo, al puntear en **+** [`Button`](xref:Xamarin.Forms.Button) , dará como resultado el siguiente controlador de eventos en la `NotesPage` ejecución del código subyacente:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainActivity.Instance.NavigateToNoteEntryPage(new Note());
}
```

El `static` `MainActivity.Instance` campo permite `MainActivity.NavigateToNoteEntryPage` invocar el método, que se muestra en el ejemplo de código siguiente:

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

El `NavigateToNoteEntryPage` método convierte la Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) Página derivada de en una `Fragment` con el `CreateSupportFragment` método de extensión y agrega `Fragment` a la pila de retroceso de fragmento. Por lo tanto, se mostrará la interfaz de usuario definida en el Xamarin.Forms `NoteEntryPage` , tal y como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de una aplicación de Xamarin. Android que usa una interfaz de usuario definida en XAML](native-forms-images/android-noteentrypage.png "Aplicación Xamarin. Android con una interfaz de usuario XAML")](native-forms-images/android-noteentrypage-large.png#lightbox "Aplicación Xamarin. Android con una interfaz de usuario XAML")

Cuando `NoteEntryPage` se muestra, al puntear en la flecha atrás, se mostrará el elemento de la `Fragment` `NoteEntryPage` pila de retroceso de fragmento y se devolverá al usuario al `Fragment` de la `NotesPage` clase.

### <a name="enable-back-navigation-support"></a>Habilitar la compatibilidad con la navegación hacia atrás

La `SupportFragmentManager` clase tiene un `BackStackChanged` evento que se activa cada vez que cambia el contenido de la pila de retroceso de fragmento. El `OnCreate` método de la `MainActivity` clase contiene un controlador de eventos anónimo para este evento:

```csharp
SupportFragmentManager.BackStackChanged += (sender, e) =>
{
    bool hasBack = SupportFragmentManager.BackStackEntryCount > 0;
    SupportActionBar.SetHomeButtonEnabled(hasBack);
    SupportActionBar.SetDisplayHomeAsUpEnabled(hasBack);
    SupportActionBar.Title = hasBack ? "Note Entry" : "Notes";
};
```

Este controlador de eventos muestra un botón atrás en la barra de acciones siempre que haya una o más `Fragment` instancias en la pila de retroceso de fragmento. La respuesta al puntear en el botón atrás se controla mediante la `OnOptionsItemSelected` invalidación:

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

`OnOptionsItemSelected`Se llama a la invalidación cada vez que se selecciona un elemento en el menú opciones. Esta implementación extrae el fragmento actual de la pila de retroceso de fragmento, siempre que se haya seleccionado el botón atrás y haya una o más `Fragment` instancias en la pila de retroceso de fragmento.

### <a name="multiple-activities"></a>Varias actividades

Cuando una aplicación se compone de varias actividades, [`ContentPage`](xref:Xamarin.Forms.ContentPage) las páginas derivadas de se pueden incrustar en cada una de las actividades. En este escenario, `Forms.Init` solo es necesario llamar al método en la `OnCreate` invalidación del primer `Activity` que inserta un Xamarin.Forms `ContentPage` . Sin embargo, esto tiene el impacto siguiente:

- El valor de `Xamarin.Forms.Color.Accent` se tomará del `Activity` que llamó al `Forms.Init` método.
- El valor de se `Xamarin.Forms.Application.Current` asociará al `Activity` que llamó al `Forms.Init` método.

### <a name="choose-a-file"></a>Elegir un archivo

Al incrustar una [`ContentPage`](xref:Xamarin.Forms.ContentPage) Página derivada de que usa un [`WebView`](xref:Xamarin.Forms.WebView) que necesita admitir un botón "elegir archivo" HTML, `Activity` deberá invalidar el `OnActivityResult` método:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);
    ActivityResultCallbackRegistry.InvokeCallback(requestCode, resultCode, data);
}
```

## <a name="uwp"></a>UWP

En UWP, la `App` clase nativa es normalmente el lugar en el que se realizan las tareas relacionadas con el inicio de la aplicación. Xamarin.Forms normalmente se inicializa, en Xamarin.Forms las aplicaciones para UWP, en la `OnLaunched` invalidación de la `App` clase nativa, para pasar el `LaunchActivatedEventArgs` argumento al `Forms.Init` método. Por este motivo, las aplicaciones de UWP nativas que consumen una Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) Página derivada de pueden llamar más fácilmente al `Forms.Init` método desde el `App.OnLaunched` método.

De forma predeterminada, la `App` clase nativa inicia la `MainPage` clase como la primera página de la aplicación. En el ejemplo de código siguiente se muestra la `MainPage` clase en la aplicación de ejemplo:

```csharp
public sealed partial class MainPage : Page
{
    NotesPage notesPage;
    NoteEntryPage noteEntryPage;
    
    public static MainPage Instance;
    public static string FolderPath { get; private set; }

    public MainPage()
    {
        this.InitializeComponent();
        this.NavigationCacheMode = NavigationCacheMode.Enabled;
        Instance = this;
        FolderPath = Path.Combine(System.Environment.GetFolderPath(System.Environment.SpecialFolder.LocalApplicationData));
        notesPage = new Notes.UWP.Views.NotesPage();
        this.Content = notesPage.CreateFrameworkElement();
        // ...        
    } 
    // ...
}
```

El `MainPage` constructor realiza las siguientes tareas:

- El almacenamiento en caché está habilitado para la página, por lo que no se crea un nuevo `MainPage` cuando un usuario vuelve a la página.
- `MainPage`En el campo se almacena una referencia a la clase `static` `Instance` . Esto es para proporcionar un mecanismo para que otras clases llamen a métodos definidos en la `MainPage` clase.
- La `FolderPath` propiedad se inicializa en una ruta de acceso en el dispositivo donde se almacenarán los datos de la nota.
- La `NotesPage` clase, que es una Xamarin.Forms [`ContentPage`](xref:Xamarin.Forms.ContentPage) Página derivada de definida en XAML, se construye y se convierte en `FrameworkElement` mediante el `CreateFrameworkElement` método de extensión y, a continuación, se establece como el contenido de la `MainPage` clase.

Una vez `MainPage` ejecutado el constructor, se mostrará la interfaz de usuario definida en la Xamarin.Forms `NotesPage` clase, como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de una aplicación de UWP que usa una interfaz de usuario definida con::: no-LOC (Xamarin. Forms)::: XAML](native-forms-images/uwp-notespage.png "Aplicación para UWP con una interfaz de usuario::: no-LOC (Xamarin. Forms)::: XAML")](native-forms-images/uwp-notespage-large.png#lightbox "Aplicación para UWP con una interfaz de usuario::: no-LOC (Xamarin. Forms)::: XAML")

La interacción con la interfaz de usuario, por ejemplo, al puntear en **+** [`Button`](xref:Xamarin.Forms.Button) , dará como resultado el siguiente controlador de eventos en la `NotesPage` ejecución del código subyacente:

```csharp
void OnNoteAddedClicked(object sender, EventArgs e)
{
    MainPage.Instance.NavigateToNoteEntryPage(new Note());
}
```

El `static` `MainPage.Instance` campo permite `MainPage.NavigateToNoteEntryPage` invocar el método, que se muestra en el ejemplo de código siguiente:

```csharp
public void NavigateToNoteEntryPage(Note note)
{
    noteEntryPage = new Notes.UWP.Views.NoteEntryPage
    {
        BindingContext = note
    };
    this.Frame.Navigate(noteEntryPage);
}
```

La navegación en UWP se realiza normalmente con el `Frame.Navigate` método, que toma un `Page` argumento. Xamarin.Forms define un `Frame.Navigate` método de extensión que toma una [`ContentPage`](xref:Xamarin.Forms.ContentPage) instancia de página derivada de. Por lo tanto, cuando se `NavigateToNoteEntryPage` ejecute el método, se mostrará la interfaz de usuario definida en el Xamarin.Forms `NoteEntryPage` , tal y como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de una aplicación de UWP que usa una interfaz de usuario definida con::: no-LOC (Xamarin. Forms)::: XAML](native-forms-images/uwp-noteentrypage.png "Aplicación para UWP con una interfaz de usuario::: no-LOC (Xamarin. Forms)::: XAML")](native-forms-images/uwp-noteentrypage-large.png#lightbox "Aplicación para UWP con una interfaz de usuario::: no-LOC (Xamarin. Forms)::: XAML")

Cuando `NoteEntryPage` se muestra, al puntear en la flecha atrás se desactivará el elemento de la `FrameworkElement` pila de `NoteEntryPage` retroceso de la aplicación y se devolverá al usuario al `FrameworkElement` de la `NotesPage` clase.

### <a name="enable-page-resizing-support"></a>Habilitar la compatibilidad con el cambio de tamaño de página

Cuando se cambia el tamaño de la ventana de la aplicación de UWP, Xamarin.Forms también se debe cambiar el tamaño del contenido. Esto se logra registrando un controlador de eventos para el `Loaded` evento, en el `MainPage` constructor:

```csharp
public MainPage()
{
    // ...
    this.Loaded += OnMainPageLoaded;
    // ...
}
```

El `Loaded` evento se desencadena cuando la página se presenta, se representa y está lista para la interacción y ejecuta el `OnMainPageLoaded` método en respuesta:

```csharp
void OnMainPageLoaded(object sender, RoutedEventArgs e)
{
    this.Frame.SizeChanged += (o, args) =>
    {
        if (noteEntryPage != null)
            noteEntryPage.Layout(new Xamarin.Forms.Rectangle(0, 0, args.NewSize.Width, args.NewSize.Height));
        else
            notesPage.Layout(new Xamarin.Forms.Rectangle(0, 0, args.NewSize.Width, args.NewSize.Height));
    };
}
```

El `OnMainPageLoaded` método registra un controlador de eventos anónimo para el `Frame.SizeChanged` evento, que se produce cuando las `ActualHeight` `ActualWidth` propiedades o cambian en `Frame` . En respuesta, Xamarin.Forms se cambia el tamaño del contenido de la página activa mediante una llamada al `Layout` método.

### <a name="enable-back-navigation-support"></a>Habilitar la compatibilidad con la navegación hacia atrás

En UWP, las aplicaciones deben habilitar la navegación hacia atrás para todos los botones de retroceso de hardware y software, en distintos factores de forma de dispositivo. Esto puede realizarse registrando un controlador de eventos para el `BackRequested` evento, que se puede realizar en el `MainPage` constructor:

```csharp
public MainPage()
{
    // ...
    SystemNavigationManager.GetForCurrentView().BackRequested += OnBackRequested;
}
```

Cuando se inicia la aplicación, el `GetForCurrentView` método recupera el `SystemNavigationManager` objeto asociado a la vista actual y, a continuación, registra un controlador de eventos para el `BackRequested` evento. La aplicación solo recibe este evento si es la aplicación de primer plano y, en respuesta, llama al `OnBackRequested` controlador de eventos:

```csharp
void OnBackRequested(object sender, BackRequestedEventArgs e)
{
    Frame rootFrame = Window.Current.Content as Frame;
    if (rootFrame.CanGoBack)
    {
        e.Handled = true;
        rootFrame.GoBack();
        noteEntryPage = null;
    }
}
```

El `OnBackRequested` controlador de eventos llama al `GoBack` método en el marco raíz de la aplicación y establece la `BackRequestedEventArgs.Handled` propiedad en `true` para marcar el evento como controlado. Si no se marca el evento como controlado, se podría omitir el evento.

La aplicación elige si mostrar un botón atrás en la barra de título. Esto se consigue estableciendo la `AppViewBackButtonVisibility` propiedad en uno de los `AppViewBackButtonVisibility` valores de enumeración, en la `App` clase:

```csharp
void OnNavigated(object sender, NavigationEventArgs e)
{
    SystemNavigationManager.GetForCurrentView().AppViewBackButtonVisibility =
        ((Frame)sender).CanGoBack ? AppViewBackButtonVisibility.Visible : AppViewBackButtonVisibility.Collapsed;
}
```

El `OnNavigated` controlador de eventos, que se ejecuta en respuesta a la `Navigated` activación del evento, actualiza la visibilidad del botón atrás de la barra de título cuando se produce la navegación por la página. Esto garantiza que el botón atrás de la barra de título está visible si la pila de retroceso de la aplicación no está vacía o se quita de la barra de título si la pila de retroceso en aplicación está vacía.

Para más información sobre la compatibilidad con la navegación hacia atrás en UWP, consulte [navegación por el historial de navegación y la navegación hacia atrás para aplicaciones UWP](/windows/uwp/design/basics/navigation-history-and-backwards-navigation/).

## <a name="related-links"></a>Vínculos relacionados

- [NativeForms (ejemplo)](/samples/xamarin/xamarin-forms-samples/native2forms)
- [Vistas nativas](~/xamarin-forms/platform/native-views/index.md)