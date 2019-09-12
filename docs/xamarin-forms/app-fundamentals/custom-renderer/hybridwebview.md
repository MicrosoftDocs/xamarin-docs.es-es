---
title: Implementación de HybridWebView
description: En este artículo se muestra cómo crear un representador personalizado para un control personalizado HybridWebView, lo que mejora los controles web específicos de la plataforma para permitir la invocación de código de C# desde JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: 66e26704f22a73559eef04608726ab3b4a935385
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771921"
---
# <a name="implementing-a-hybridwebview"></a>Implementación de HybridWebView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_Los controles de interfaz de usuario personalizados de Xamarin.Forms deben derivar de la clase View, que se usa para colocar diseños y controles en la pantalla. En este artículo se muestra cómo crear un representador personalizado para un control personalizado HybridWebView, lo que mejora los controles web específicos de la plataforma para permitir la invocación de código de C# desde JavaScript._

Todas las vistas de Xamarin.Forms tienen un representador adjunto para cada plataforma que crea una instancia de un control nativo. Cuando una aplicación de Xamarin.Forms representa una instancia de [`View`](xref:Xamarin.Forms.View) en iOS, se crea una instancia de la clase `ViewRenderer`, que a su vez crea una instancia del control `UIView` nativo. En la plataforma Android, la clase `ViewRenderer` crea una instancia de un control `View`. En Plataforma universal de Windows (UWP), la clase `ViewRenderer` crea una instancia de un control `FrameworkElement` nativo. Para obtener más información sobre el representador y las clases de control nativo a las que se asignan los controles de Xamarin.Forms, vea [Renderer Base Classes and Native Controls](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) (Clases base y controles nativos del representador).

El siguiente diagrama muestra la relación entre la clase [`View`](xref:Xamarin.Forms.View) y los controles nativos correspondientes que la implementan:

![](hybridwebview-images/view-classes.png "Relación entre la clase View y sus clases nativas de implementación")

El proceso de representación puede usarse para implementar personalizaciones específicas de plataforma al crear un representador personalizado para una clase [`View`](xref:Xamarin.Forms.View) en cada plataforma. Para ello, siga este procedimiento:

1. [Cree](#Creating_the_HybridWebView) el control `HybridWebView` personalizado.
1. [Use](#Consuming_the_HybridWebView) el elemento `HybridWebView` de Xamarin.Forms.
1. [Cree](#creating-the-custom-renderer-on-each-platform) el representador personalizado para el elemento `HybridWebView` en cada plataforma.

Ahora se va a hablar de cada elemento para implementar un representador de `HybridWebView` que mejore los controles web específicos de la plataforma a fin de permitir la invocación de código de C# desde JavaScript. Se usa la instancia de `HybridWebView` para mostrar una página HTML que pide al usuario que escriba su nombre. Luego, cuando el usuario hace clic en un botón HTML, una función de JavaScript invoca a un elemento `Action` de C# que muestra una ventana emergente que contiene el nombre de los usuarios.

Para obtener más información sobre el proceso de invocación de C# desde JavaScript, vea [Invocación a C# desde JavaScript](#Invoking_C_from_JavaScript). Para obtener más información sobre la página HTML, vea [Creación de la página web](#Creating_the_Web_Page).

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>Creación de HybridWebView

Se puede crear el control personalizado `HybridWebView` mediante la creación de subclases de la clase [`View`](xref:Xamarin.Forms.View), como se muestra en el siguiente ejemplo de código:

```csharp
public class HybridWebView : View
{
  Action<string> action;
  public static readonly BindableProperty UriProperty = BindableProperty.Create (
    propertyName: "Uri",
    returnType: typeof(string),
    declaringType: typeof(HybridWebView),
    defaultValue: default(string));

  public string Uri {
    get { return (string)GetValue (UriProperty); }
    set { SetValue (UriProperty, value); }
  }

  public void RegisterAction (Action<string> callback)
  {
    action = callback;
  }

  public void Cleanup ()
  {
    action = null;
  }

  public void InvokeAction (string data)
  {
    if (action == null || data == null) {
      return;
    }
    action.Invoke (data);
  }
}
```

El control `HybridWebView` personalizado se crea en el proyecto de biblioteca de .NET Standard y define la siguiente API para el control:

- Una propiedad `Uri` que especifica la dirección de la página web que se va a cargar.
- Un método `RegisterAction` que registra un elemento `Action` con el control. Se invoca a la acción registrada desde el código de JavaScript incluido en el archivo HTML al que hace referencia la propiedad `Uri`.
- Un método `CleanUp` que quita la referencia al elemento registrado `Action`.
- Un método `InvokeAction` que invoca al elemento registrado `Action`. Se llama a este método desde un representador personalizado en cada proyecto específico de plataforma.

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>Uso de HybridWebView

En XAML, se puede hacer referencia al control personalizado `HybridWebView` en el proyecto de biblioteca de .NET Standard al declarar un espacio de nombres para su ubicación y usar el prefijo del espacio de nombres en el control personalizado. El siguiente ejemplo de código muestra cómo se puede usar el control personalizado `HybridWebView` en una página XAML:

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <local:HybridWebView x:Name="hybridWebView" Uri="index.html"
          HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
    </ContentPage.Content>
</ContentPage>
```

El prefijo de espacio de nombres `local` puede tener cualquier nombre. Pero los valores `clr-namespace` y `assembly` deben coincidir con los detalles del control personalizado. Una vez que se declara el espacio de nombres, el prefijo se usa para hacer referencia al control personalizado.

El siguiente ejemplo de código muestra cómo se puede usar el control personalizado `HybridWebView` en una página C#:

```csharp
public class HybridWebViewPageCS : ContentPage
{
  public HybridWebViewPageCS ()
  {
    var hybridWebView = new HybridWebView {
      Uri = "index.html",
      HorizontalOptions = LayoutOptions.FillAndExpand,
      VerticalOptions = LayoutOptions.FillAndExpand
    };
    ...
    Padding = new Thickness (0, 20, 0, 0);
    Content = hybridWebView;
  }
}
```

La instancia de `HybridWebView` se usa para mostrar un control web nativo en cada plataforma. Su propiedad `Uri` se establece en un archivo HTML que se almacena en cada proyecto específico de plataforma y que muestra el control web nativo. El HTML representado pide al usuario que escriba su nombre, con una función de JavaScript que invoca a un elemento `Action` de C# en respuesta a un clic de botón HTML.

`HybridWebViewPage` registra la acción que se va a invocar desde JavaScript, como se muestra en el ejemplo de código siguiente:

```csharp
public partial class HybridWebViewPage : ContentPage
{
  public HybridWebViewPage ()
  {
    ...
    hybridWebView.RegisterAction (data => DisplayAlert ("Alert", "Hello " + data, "OK"));
  }
}
```

Esta acción llama al método [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) para mostrar un elemento emergente modal que presenta el nombre especificado en la página HTML que muestra la instancia de `HybridWebView`.

Ahora se puede agregar un representador personalizado a cada proyecto de aplicación para mejorar los controles web específicos de la plataforma al permitir la invocación de código de C# desde JavaScript.

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creación del representador personalizado en cada plataforma

El proceso para crear la clase del representador personalizado es el siguiente:

1. Cree una subclase de la clase `ViewRenderer<T1,T2>` que representa el control personalizado. El primer argumento de tipo debe ser el control personalizado para el que es el representador, en este caso `HybridWebView`. El segundo argumento de tipo debe ser el control nativo que va a implementar la vista personalizada.
1. Invalide el método `OnElementChanged` que representa el control personalizado y escriba lógica para personalizarlo. Se llama a este método cuando se crea el correspondiente control personalizado de Xamarin.Forms.
1. Agregue un atributo `ExportRenderer` a la clase del representador personalizado para especificar que se va a usar para representar el control personalizado de Xamarin.Forms. Este atributo se usa para registrar el representador personalizado con Xamarin.Forms.

> [!NOTE]
> Para la mayoría de los elementos de Xamarin.Forms, proporcionar un representador personalizado en cada proyecto de la plataforma es un paso opcional. Si no hay un representador personalizado registrado, se usa el representador predeterminado de la clase base del control. Pero los representadores personalizados son necesarios en cada proyecto de plataforma al representar un elemento [View](xref:Xamarin.Forms.View).

El siguiente diagrama muestra las responsabilidades de cada proyecto de la aplicación de ejemplo, junto con las relaciones entre ellos:

![](hybridwebview-images/solution-structure.png "Responsabilidades de proyecto del representador personalizado de HybridWebView")

El control personalizado `HybridWebView` se representa mediante clases de representador específicas de la plataforma, que se derivan de la clase `ViewRenderer` de cada plataforma. Esto da lugar a que cada control personalizado `HybridWebView` se represente con controles web específicos de la plataforma, como se muestra en las capturas de pantalla siguientes:

![](hybridwebview-images/screenshots.png "HybridWebView en cada plataforma")

La clase `ViewRenderer` expone el método `OnElementChanged`, al que se llama cuando se crea el control personalizado de Xamarin.Forms para representar el control web nativo correspondiente. Este método toma un parámetro `ElementChangedEventArgs` que contiene propiedades `OldElement` y `NewElement`. Estas propiedades representan al elemento de Xamarin.Forms al que *estaba* asociado el representador y al elemento de Xamarin.Forms al que *está* asociado el representador, respectivamente. En la aplicación de ejemplo, la propiedad `OldElement` es `null` y la propiedad `NewElement` contiene una referencia a la instancia de `HybridWebView`.

Una versión invalidada del método `OnElementChanged`, en cada clase de representador específica de la plataforma, es el lugar en el que realizar la personalización y la creación de instancias del control web nativo. Se debe usar el método `SetNativeControl` para crear instancias del control web nativo; además, este método también asigna la referencia del control a la propiedad `Control`. Además, se puede obtener una referencia al control de Xamarin.Forms que se va a representar mediante la propiedad `Element`.

En algunas circunstancias, se puede llamar al método `OnElementChanged` varias veces. Por lo tanto, para evitar pérdidas de memoria, se debe tener cuidado a la hora de crear instancias de un nuevo control nativo. El enfoque que usar al crear instancias de un nuevo control nativo en un presentador personalizado se muestra en el ejemplo de código siguiente:

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    if (Control == null) {
      // Instantiate the native control and assign it to the Control property with
      // the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

Solo se debe crear una instancia de un nuevo control nativo una vez, cuando la propiedad `Control` es `null`.  Además, solo se debe crear, configurar el control y suscribir los controladores de eventos cuando se adjunta el presentador personalizado a un nuevo elemento de Xamarin.Forms. De forma similar, solo se debe cancelar la suscripción de los controladores de eventos que se han suscrito cuando cambia el elemento al que está asociado el presentador. La adopción de este enfoque ayuda a crear un representador personalizado eficaz que no sufra pérdidas de memoria.

> [!IMPORTANT]
> El método `SetNativeControl` solo se debe llamar si `e.NewElement` no es `null`.

Cada clase de representador personalizado se decora con un atributo `ExportRenderer` que registra el representador con Xamarin.Forms. El atributo toma dos parámetros: el nombre de tipo del control personalizado de Xamarin.Forms que se va a representar y el nombre de tipo del representador personalizado. El prefijo `assembly` del atributo especifica que el atributo se aplica a todo el ensamblado.

En las secciones siguientes se habla de la estructura de la página web cargada por cada control web nativo, el proceso para invocar a C# desde JavaScript y su implementación en cada clase de representador personalizado específico de la plataforma.

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>Creación de la página web

El ejemplo de código siguiente muestra la página web que va a mostrar el control personalizado `HybridWebView`:

```html
<html>
<body>
<script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>
<h1>HybridWebView Test</h1>
<br/>
Enter name: <input type="text" id="name">
<br/>
<br/>
<button type="button" onclick="javascript:invokeCSCode($('#name').val());">Invoke C# Code</button>
<br/>
<p id="result">Result:</p>
<script type="text/javascript">
function log(str)
{
    $('#result').text($('#result').text() + " " + str);
}

function invokeCSCode(data) {
    try {
        log("Sending Data:" + data);
        invokeCSharpAction(data);
    }
    catch (err){
          log(err);
    }
}
</script>
</body>
</html>
```

La página web permite que un usuario escriba su nombre en un elemento `input` y proporciona un elemento `button` que va a invocar a código de C# cuando se haga clic sobre él. El proceso para lograrlo es el siguiente:

- Cuando el usuario hace clic en el elemento `button`, se llama a la función de JavaScript `invokeCSCode` y el valor del elemento `input` se pasa a la función.
- La función `invokeCSCode` llama a la función `log` para mostrar los datos que está enviando al elemento `Action` de C#. Luego llama al método `invokeCSharpAction` para invocar al elemento `Action` de C#, pasando el parámetro recibido desde el elemento `input`.

La función de JavaScript `invokeCSharpAction` no está definida en la página web, así que es cada representador personalizado el que la inserta en ella.

En iOS, este archivo HTML se encuentra en la carpeta de contenido del proyecto de la plataforma e incluye una acción de compilación de **BundleResource**. En Android, este archivo HTML se encuentra en la carpeta de contenido o recursos del proyecto de la plataforma e incluye una acción de compilación de **AndroidAsset**.

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>Invocación de C# desde JavaScript

El proceso para invocar a C# desde JavaScript es idéntico en cada plataforma:

- El representador personalizado crea un control web nativo y carga el archivo HTML especificado por la propiedad `HybridWebView.Uri`.
- Una vez que se ha cargado la página web, el representador personalizado inserta la función de JavaScript `invokeCSharpAction` en la página web.
- Cuando el usuario escribe su nombre y hace clic en el elemento HTML `button`, se invoca a la función `invokeCSCode`, que a su vez invoca a la función `invokeCSharpAction`.
- La función `invokeCSharpAction` invoca a un método del representador personalizado, que a su vez invoca al método `HybridWebView.InvokeAction`.
- El método `HybridWebView.InvokeAction` invoca al elemento registrado `Action`.

En las secciones siguientes se habla de cómo se implementa este proceso en cada plataforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creación del representador personalizado en iOS

El ejemplo de código siguiente muestra el representador personalizado para la plataforma iOS:

```csharp
[assembly: ExportRenderer (typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, WKWebView>, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        protected override void OnElementChanged (ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                userController.RemoveAllUserScripts ();
                userController.RemoveScriptMessageHandler ("invokeAction");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup ();
            }
            if (e.NewElement != null) {
                if (Control == null) {
                    userController = new WKUserContentController ();
                    var script = new WKUserScript (new NSString (JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
                    userController.AddUserScript (script);
                    userController.AddScriptMessageHandler (this, "invokeAction");

                    var config = new WKWebViewConfiguration { UserContentController = userController };
                    var webView = new WKWebView (Frame, config);
                    SetNativeControl (webView);
                }
                string fileName = Path.Combine (NSBundle.MainBundle.BundlePath, string.Format ("Content/{0}", Element.Uri));
                Control.LoadRequest (new NSUrlRequest (new NSUrl (fileName, false)));
            }
        }

        public void DidReceiveScriptMessage (WKUserContentController userContentController, WKScriptMessage message)
        {
            Element.InvokeAction (message.Body.ToString ());
        }
    }
}
```

La clase `HybridWebViewRenderer` carga la página web especificada en la propiedad `HybridWebView.Uri` en un control nativo [`WKWebView`](xref:WebKit.WKWebView) y la función de JavaScript `invokeCSharpAction` se inserta en la página web. Una vez que el usuario escribe su nombre y hace clic en el elemento HTML `button`, se ejecuta la función de JavaScript `invokeCSharpAction` y se llama al método `DidReceiveScriptMessage` después de que se reciba un mensaje de la página web. A su vez, este método invoca al método `HybridWebView.InvokeAction`, que invoca a la acción registrada para mostrar la ventana emergente.

Esta funcionalidad se logra del siguiente modo:

- Siempre que el representador personalizado está asociado a un nuevo elemento de Xamarin.Forms:
  - Siempre que la propiedad `Control` es `null`, se efectúan las siguientes operaciones:
    - Se crea una instancia de [`WKUserContentController`](xref:WebKit.WKUserContentController), lo que permite la publicación de mensajes y la inserción de scripts de usuario en una página web.
    - Se crea una instancia de [`WKUserScript`](xref:WebKit.WKUserScript) para insertar la función de JavaScript `invokeCSharpAction` en la página web una vez cargada la página web.
    - El método [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) agrega la instancia de [`WKUserScript`](xref:WebKit.WKUserScript) al controlador de contenido.
    - El método [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) agrega un controlador de mensajes de script denominado `invokeAction` a la instancia de [`WKUserContentController`](xref:WebKit.WKUserContentController), lo que hace que la función de JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` se defina en todos los marcos de todas las vistas web que van a usar la instancia de `WKUserContentController`.
    - Se crea una instancia de [`WKWebViewConfiguration`](xref:WebKit.WKWebViewConfiguration), con la instancia de [`WKUserContentController`](xref:WebKit.WKUserContentController) establecida como controlador de contenido.
    - Se crean instancias de un control [`WKWebView`](xref:WebKit.WKWebView) y se llama al método `SetNativeControl` para asignar una referencia al control `WKWebView` para la propiedad `Control`.
  - El método [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) carga el archivo HTML especificado por la propiedad `HybridWebView.Uri`. El código especifica que el archivo se almacena en la carpeta `Content` del proyecto. Una vez que se muestra la página web, la función de JavaScript `invokeCSharpAction` se inserta en la página web.
- Cuando cambia el elemento al que está asociado el representador:
  - Se liberan recursos.

> [!NOTE]
> La clase `WKWebView` solo se admite en iOS 8 y versiones posteriores.

Además, **Info.plist** debe actualizarse para que incluya los siguientes valores:

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

### <a name="creating-the-custom-renderer-on-android"></a>Creación del representador personalizado en Android

En el ejemplo de código siguiente se muestra el representador personalizado para la plataforma Android:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Android.Webkit.WebView>
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                    var webView = new Android.Webkit.WebView(_context);
                    webView.Settings.JavaScriptEnabled = true;
                    webView.SetWebViewClient(new JavascriptWebViewClient($"javascript: {JavascriptFunction}"));
                    SetNativeControl(webView);
                }
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{Element.Uri}");
            }
        }
    }
}
```

La clase `HybridWebViewRenderer` carga la página web especificada en la propiedad `HybridWebView.Uri` en un control nativo [`WebView`](xref:Android.Webkit.WebView) y la función de JavaScript `invokeCSharpAction` se inserta en la página web, una vez que la página web ha terminado de cargarse, con la invalidación `OnPageFinished` en la clase `JavascriptWebViewClient`:

```csharp
public class JavascriptWebViewClient : WebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(string javascript)
    {
        _javascript = javascript;
    }

    public override void OnPageFinished(WebView view, string url)
    {
        base.OnPageFinished(view, url);
        view.EvaluateJavascript(_javascript, null);
    }
}
```

Una vez que el usuario escribe su nombre y hace clic en el elemento HTML `button`, se ejecuta la función de JavaScript `invokeCSharpAction`. Esta funcionalidad se logra del siguiente modo:

- Siempre que el representador personalizado está asociado a un nuevo elemento de Xamarin.Forms:
  - Siempre que la propiedad `Control` es `null`, se efectúan las siguientes operaciones:
    - Se crea una instancia nativa de [`WebView`](xref:Android.Webkit.WebView), JavaScript se habilita en el control y se establece una instancia de `JavascriptWebViewClient` como la implementación de `WebViewClient`.
    - Se llama al método `SetNativeControl` para asignar una referencia al control nativo [`WebView`](xref:Android.Webkit.WebView) para la propiedad `Control`.
  - El método [`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*) inserta una nueva instancia de `JSBridge` en el marco principal del contexto de JavaScript de WebView y le asigna el nombre `jsBridge`. Esto permite acceder a los métodos de la clase `JSBridge` desde JavaScript.
  - El método [`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*) carga el archivo HTML especificado por la propiedad `HybridWebView.Uri`. El código especifica que el archivo se almacena en la carpeta `Content` del proyecto.
  - En la clase `JavascriptWebViewClient`, la función de JavaScript `invokeCSharpAction` se inserta en la página web una vez que esta termina de cargarse.
- Cuando cambia el elemento al que está asociado el representador:
  - Se liberan recursos.

Cuando se ejecuta la función de JavaScript `invokeCSharpAction`, a su vez invoca al método `JSBridge.InvokeAction`, que se muestra en el ejemplo de código siguiente:

```csharp
public class JSBridge : Java.Lang.Object
{
  readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

  public JSBridge (HybridWebViewRenderer hybridRenderer)
  {
    hybridWebViewRenderer = new WeakReference <HybridWebViewRenderer> (hybridRenderer);
  }

  [JavascriptInterface]
  [Export ("invokeAction")]
  public void InvokeAction (string data)
  {
    HybridWebViewRenderer hybridRenderer;

    if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget (out hybridRenderer))
    {
      hybridRenderer.Element.InvokeAction (data);
    }
  }
}
```

La clase debe derivar de `Java.Lang.Object` y los métodos que se exponen a JavaScript deben decorarse con los atributos `[JavascriptInterface]` y `[Export]`. Por lo tanto, cuando se inserta la función de JavaScript `invokeCSharpAction` en la página web y se ejecuta, llama al método `JSBridge.InvokeAction`, puesto que está decorado con los atributos `[JavascriptInterface]` y `[Export("invokeAction")]`. A su vez, el método `InvokeAction` invoca al método `HybridWebView.InvokeAction`, que invoca a la acción registrada para mostrar la ventana emergente.

> [!NOTE]
> Los proyectos que usan el atributo `[Export]` deben incluir una referencia a `Mono.Android.Export`, o se produce un error del compilador.

Tenga en cuenta que la clase `JSBridge` mantiene un elemento `WeakReference` para la clase `HybridWebViewRenderer`. Esto es para evitar la creación de una referencia circular entre las dos clases. Para obtener más información, vea [Referencias débiles](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx) en MSDN.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creación del representador personalizado en UWP

En el ejemplo de código siguiente se muestra el representador personalizado para UWP:

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Windows.UI.Xaml.Controls.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                    SetNativeControl(new Windows.UI.Xaml.Controls.WebView());
                }
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri(string.Format("ms-appx-web:///Content//{0}", Element.Uri));
            }
        }

        async void OnWebViewNavigationCompleted(WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            Element.InvokeAction(e.Value);
        }
    }
}
```

La clase `HybridWebViewRenderer` carga la página web especificada en la propiedad `HybridWebView.Uri` en un control nativo `WebView` y la función de JavaScript `invokeCSharpAction` se inserta en la página web, una vez cargada, con el método `WebView.InvokeScriptAsync`. Una vez que el usuario escribe su nombre y hace clic en el elemento HTML `button`, se ejecuta la función de JavaScript `invokeCSharpAction` y se llama al método `OnWebViewScriptNotify` después de que se reciba una notificación de la página web. A su vez, este método invoca al método `HybridWebView.InvokeAction`, que invoca a la acción registrada para mostrar la ventana emergente.

Esta funcionalidad se logra del siguiente modo:

- Siempre que el representador personalizado está asociado a un nuevo elemento de Xamarin.Forms:
  - Siempre que la propiedad `Control` es `null`, se efectúan las siguientes operaciones:
    - Se llama al método `SetNativeControl` para crear instancias de un nuevo control nativo `WebView` y asignar una referencia a él para la propiedad `Control`.
  - Se registran controladores de eventos para los eventos `NavigationCompleted` y `ScriptNotify`. El evento `NavigationCompleted` se desencadena cuando el control nativo `WebView` ha terminado de cargar el contenido actual o si se ha producido un error de navegación. El evento `ScriptNotify` se desencadena cuando el contenido del control nativo `WebView` usa JavaScript para pasar una cadena a la aplicación. La página web desencadena el evento `ScriptNotify` mediante una llamada a `window.external.notify` al pasar un parámetro `string`.
  - La propiedad `WebView.Source` está establecida en el URI del archivo HTML especificado por la propiedad `HybridWebView.Uri`. El código da por supuesto que el archivo está almacenado en la carpeta `Content` del proyecto. Una vez que se muestra la página web, se desencadena el evento `NavigationCompleted` y se invoca al método `OnWebViewNavigationCompleted`. La función de JavaScript `invokeCSharpAction` se inserta en la página web con el método `WebView.InvokeScriptAsync`, siempre que la navegación se haya realizado correctamente.
- Cuando cambia el elemento al que está asociado el representador:
  - Se cancela la suscripción a los eventos.

## <a name="summary"></a>Resumen

En este artículo se ha mostrado cómo crear un representador personalizado para un control personalizado `HybridWebView`, lo que mejora los controles web específicos de la plataforma para permitir la invocación de código de C# desde JavaScript.

## <a name="related-links"></a>Vínculos relacionados

- [CustomRendererHybridWebView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)
- [Call C# from JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) (Llamada a C# desde JavaScript)
