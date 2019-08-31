---
title: Xamarin.Forms WebView
description: Este artículo explica cómo usar la clase Xamarin.Forms WebView para presentar local o documentos y contenido web de red a los usuarios.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2019
ms.openlocfilehash: b13ee05f6d0e17287254b7c30c07b5a4a3c70638
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70198289"
---
# <a name="xamarinforms-webview"></a>Xamarin.Forms WebView

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) es una vista para mostrar la web y el contenido HTML en la aplicación. A diferencia de `OpenUri`, que lleva al usuario en el explorador web en el dispositivo, `WebView` muestra el contenido HTML dentro de la aplicación.

![](webview-images/in-app-browser.png "En el Explorador de la aplicación")

## <a name="content"></a>Contenido

`WebView` admite los siguientes tipos de contenido:

- Sitios Web HTML y CSS &ndash; WebView es totalmente compatible con sitios Web escritos con HTML y CSS, incluida la compatibilidad con JavaScript.
- Documentos &ndash; porque WebView se implementa con componentes nativos en cada plataforma, WebView es capaz de mostrar los documentos que están visibles en cada plataforma. Esto significa que los archivos PDF funcionan en iOS y Android.
- Las cadenas HTML &ndash; WebView puede mostrar las cadenas HTML de la memoria.
- Archivos locales &ndash; WebView puede presentar cualquiera de los tipos de contenido anteriores incrustado en la aplicación.

> [!NOTE]
> `WebView` en Windows no admite Silverlight, Flash o los controles ActiveX, incluso si son compatibles con Internet Explorer en esa plataforma.

### <a name="websites"></a>Sitios web

Para mostrar un sitio Web desde internet, establezca el `WebView`del [ `Source` ](xref:Xamarin.Forms.WebViewSource) propiedad a una dirección URL de cadena:

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Las direcciones URL se deben formar totalmente con el protocolo especificado (es decir, debe tener "http://" o "https://" antepuesto a él).

#### <a name="ios-and-ats"></a>iOS y ATS

Desde la versión 9, iOS solo permitirá a su aplicación comunicarse con servidores que implementan la seguridad de prácticas recomendadas de forma predeterminada. Los valores que se deben establecer `Info.plist` para habilitar la comunicación con servidores no seguras.

> [!NOTE]
> Si la aplicación requiere una conexión a un sitio Web no seguro, siempre debe escribir el dominio como una excepción mediante `NSExceptionDomains` en lugar de desactivar que ATS completamente utilizando `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` solo debe usarse en situaciones de emergencias extremas.

El siguiente muestra cómo habilitar un dominio específico (en este caso en xamarin.com) para omitir los requisitos de ATS:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSExceptionDomains</key>
        <dict>
            <key>xamarin.com</key>
            <dict>
                <key>NSIncludesSubdomains</key>
                <true/>
                <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
                <true/>
                <key>NSTemporaryExceptionMinimumTLSVersion</key>
                <string>TLSv1.1</string>
            </dict>
        </dict>
    </dict>
    ...
</key>
```

Es una práctica recomendada para permitir solo algunos dominios omitir ATS, lo que permite utilizar los sitios de confianza y seguir disfrutando de la seguridad adicional sobre los dominios de confianza. A continuación muestra el método menos seguro de deshabilitar ATS para la aplicación:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

Consulte [App Transport Security](~/ios/app-fundamentals/ats.md) para obtener más información acerca de esta nueva característica de iOS 9.

### <a name="html-strings"></a>Cadenas HTML

Si desea presentar una cadena HTML que se define de forma dinámica en el código, deberá crear una instancia de [ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![](webview-images/html-string.png "Cadena de WebView mostrar HTML")

En el código anterior, `@` se usa para marcar el código HTML como una cadena literal, lo que significa que se omiten todos los caracteres de escape habituales.

> [!NOTE]
> Puede que sea `WidthRequest` necesario establecer las propiedades y `HeightRequest` de [`WebView`](xref:Xamarin.Forms.WebView) para ver el contenido HTML, dependiendo del diseño del `WebView` que sea un elemento secundario de. Por ejemplo, esto es necesario en [`StackLayout`](xref:Xamarin.Forms.StackLayout).

### <a name="local-html-content"></a>Contenido HTML

WebView puede mostrar el contenido de HTML, CSS y Javascript incrustados dentro de la aplicación. Por ejemplo:

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamrin.Forms</h1>
    <p>This is an iOS web page.</p>
    <img src="XamarinLogo.png" />
  </body>
</html>
```

CSS:

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

Tenga en cuenta que las fuentes especificadas en las CSS anterior debe personalizarse para cada plataforma, ya que no todas las plataformas tiene las mismas fuentes.

Para mostrar local contenido mediante un `WebView`, deberá abrir el archivo HTML como cualquier otro, a continuación, cargar el contenido como una cadena en el `Html` propiedad de un `HtmlWebViewSource`. Para obtener más información sobre los archivos de apertura, consulte [trabajar con archivos](~/xamarin-forms/data-cloud/data/files.md).

Las capturas de pantalla siguientes muestran el resultado de mostrar el contenido local en cada plataforma:

![](webview-images/local-content.png "Mostrar vista Web contenido Local")

Aunque la primera página se ha cargado, el `WebView` no tiene conocimiento de dónde procede el código HTML. Que es un problema cuando se trabaja con páginas que hacen referencia a los recursos locales. Cuando el vínculo local páginas unos de otros, una página hace uso de un archivo JavaScript independiente o una página se vincula a una hoja de estilos CSS son ejemplos de cuándo esto puede suceder.  

Para resolver este problema, deberá indicar el `WebView` dónde encontrar los archivos en el sistema de archivos. Hacerlo estableciendo la `BaseUrl` propiedad en el `HtmlWebViewSource` utilizado por el `WebView`.

Dado que el sistema de archivos en cada uno de los sistemas operativos es diferente, deberá determinar esa dirección URL en cada plataforma. Xamarin.Forms presenta el `DependencyService` para resolver las dependencias en tiempo de ejecución en cada plataforma.

Para usar el `DependencyService`, primero defina una interfaz que se puede implementar en cada plataforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Tenga en cuenta que hasta que la interfaz se implementa en cada plataforma, no se ejecutará la aplicación. En el proyecto común, asegúrese de que recuerde establecer la `BaseUrl` utilizando el `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

A continuación, se deben proporcionar implementaciones de la interfaz para cada plataforma.

#### <a name="ios"></a>iOS

En iOS, el contenido web debe estar ubicado en el directorio raíz del proyecto o **recursos** directorio con la acción de compilación *BundleResource*, tal y como se muestra a continuación:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](webview-images/ios-vs.png "Archivos locales en iOS")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](webview-images/ios-xs.png "Archivos locales en iOS")

-----

El `BaseUrl` debe establecerse en la ruta de acceso de la agrupación principal:

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS
{
  public class BaseUrl_iOS : IBaseUrl
  {
    public string Get()
    {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

En Android, coloque las imágenes, CSS y HTML en la carpeta de activos con la acción de compilación *AndroidAsset* como se muestra a continuación:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](webview-images/android-vs.png "Archivos locales en Android")

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](webview-images/android-xs.png "Archivos locales en Android")

-----

En Android, el `BaseUrl` debe establecerse en `"file:///android_asset/"`:

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android
{
  public class BaseUrl_Android : IBaseUrl
  {
    public string Get()
    {
      return "file:///android_asset/";
    }
  }
}
```

En Android, archivos en el **activos** carpeta también se puede acceder mediante el contexto de Android actual, que se expone mediante el `MainActivity.Instance` propiedad:

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En los proyectos de plataforma Universal de Windows (UWP), coloque HTML, CSS y las imágenes en la raíz del proyecto con la acción de compilación establecida en *contenido*.

El `BaseUrl` debe establecerse en `"ms-appx-web:///"`:

```csharp
[assembly: Dependency(typeof(BaseUrl))]
namespace WorkingWithWebview.UWP
{
    public class BaseUrl : IBaseUrl
    {
        public string Get()
        {
            return "ms-appx-web:///";
        }
    }
}
```

## <a name="navigation"></a>Navegación

Vista Web admite la navegación a través de varios métodos y propiedades que están disponible:

- **GoForward()** &ndash; si `CanGoForward` es true, una llamada a `GoForward` se desplaza hacia delante a la siguiente página visitada.
- **GoBack()** &ndash; si `CanGoBack` es true, una llamada a `GoBack` navegará a la última página visitada.
- **CanGoBack** &ndash; `true` si hay páginas para ir a, `false` si el explorador se encuentre en la dirección URL de inicio.
- **CanGoForward** &ndash; `true` si el usuario ha navegado hacia atrás y puede avanzar a una página que ya ha visitado.

En las páginas, `WebView` no es compatible con los gestos multitoque. Es importante para asegurarse de que el contenido está optimizada para móviles y aparece sin la necesidad de zoom.

Es habitual que las aplicaciones mostrar un vínculo dentro de un `WebView`, en lugar del explorador del dispositivo. En estos casos, resulta útil permitir la navegación normal, pero cuando el usuario eligió una mientras están en el vínculo de inicio, la aplicación debe volver a la vista de aplicación normal.

Para habilitar este escenario, utilice las propiedades y métodos de navegación integrados.

Empiece por crear la página de la vista de explorador:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.InAppBrowserXaml"
             Title="Browser">
    <StackLayout Margin="20">
        <StackLayout Orientation="Horizontal">
            <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="OnBackButtonClicked" />
            <Button Text="Forward" HorizontalOptions="EndAndExpand" Clicked="OnForwardButtonClicked" />
        </StackLayout>
        <!-- WebView needs to be given height and width request within layouts to render. -->
        <WebView x:Name="webView" WidthRequest="1000" HeightRequest="1000" />
    </StackLayout>
</ContentPage>
```

En el código subyacente:

```csharp
public partial class InAppBrowserXaml : ContentPage
{
    public InAppBrowserXaml(string URL)
    {
        InitializeComponent();
        webView.Source = URL;
    }

    async void OnBackButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoBack)
        {
            webView.GoBack();
        }
        else
        {
            await Navigation.PopAsync();
        }
    }

    void OnForwardButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoForward)
        {
            webView.GoForward();
        }
    }
}
```

Ya está.

![](webview-images/in-app-browser.png "Botones de navegación de WebView")

## <a name="events"></a>Eventos

WebView provoca los eventos siguientes para ayudarle a responder a los cambios de estado:

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating): evento que se genera cuando la vista Web comienza a cargar una nueva página.
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated): evento que se genera cuando se carga la página y se detiene la navegación.
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested): evento que se genera cuando se realiza una solicitud para volver a cargar el contenido actual.

El [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) objeto que acompaña al [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) evento tiene cuatro propiedades:

- `Cancel`: indica si se debe cancelar o no la navegación.
- `NavigationEvent`: evento de navegación que se ha producido.
- `Source`: el elemento que llevó a cabo la navegación.
- `Url`: el destino de navegación.

El [`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs) objeto que acompaña al [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) evento tiene cuatro propiedades:

- `NavigationEvent`: evento de navegación que se ha producido.
- `Result`: describe el resultado de la navegación mediante un [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) miembro de enumeración. Los valores válidos son `Cancel`, `Failure`, `Success` y `Timeout`.
- `Source`: el elemento que llevó a cabo la navegación.
- `Url`: el destino de navegación.

Si prevé usar páginas web que tardan mucho tiempo en cargarse, considere la posibilidad de usar [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) los [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) eventos y para implementar un indicador de estado. Por ejemplo:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.LoadingLabelXaml"
             Title="Loading Demo">
    <StackLayout>
        <!--Loading label should not render by default.-->
        <Label x:Name="labelLoading" Text="Loading..." IsVisible="false" />
        <WebView HeightRequest="1000" WidthRequest="1000" Source="http://www.xamarin.com" Navigated="webviewNavigated" Navigating="webviewNavigating" />
    </StackLayout>
</ContentPage>
```

Los dos controladores de eventos:

```csharp
void webviewNavigating(object sender, WebNavigatingEventArgs e)
{
    labelLoading.IsVisible = true;
}

void webviewNavigated(object sender, WebNavigatedEventArgs e)
{
    labelLoading.IsVisible = false;
}
```

Esto da como resultado el siguiente resultado (cargar):

![](webview-images/loading-start.png "Ejemplo del evento de navegación de WebView")

Termina de cargar:

![](webview-images/loading-end.png "Ejemplo de eventos WebView")

## <a name="reloading-content"></a>Volver a cargar contenido

[`WebView`](xref:Xamarin.Forms.WebView) tiene un `Reload` método que se puede usar para volver a cargar el contenido actual:

```csharp
var webView = new WebView();
...
webView.Reload();
```

Cuando el `Reload` método se invoca el `ReloadRequested` se desencadena el evento, que indica que se ha realizado una solicitud para volver a cargar el contenido actual.

## <a name="performance"></a>Rendimiento

Ahora, los exploradores web populares adoptarán las tecnologías como la representación y la compilación de JavaScript de aceleración de hardware. En iOS, de forma predeterminada, Xamarin.Forms `WebView` se implementa mediante el `UIWebView` clase y muchas de estas tecnologías no están disponibles en esta implementación. Sin embargo, una aplicación puede participar en utilizar iOS `WkWebView` clase implemente Xamarin.Forms `WebView`, que es compatible con una navegación más rápida. Esto se consigue agregando el código siguiente a la **AssemblyInfo.cs** archivo en el proyecto de la plataforma iOS para la aplicación:

```csharp
// Opt-in to using WkWebView instead of UIWebView.
[assembly: ExportRenderer(typeof(WebView), typeof(Xamarin.Forms.Platform.iOS.WkWebViewRenderer))]
```

> [!NOTE]
> En iOS, `WkWebViewRenderer` tiene una sobrecarga de constructor que acepta un `WkWebViewConfiguration` argumento. Esto permite configurar el representador durante la creación.

`WebView` en Android de forma predeterminada es tan rápido como el explorador integrado.

El [UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) usa el motor de representación Microsoft Edge. Los dispositivos de escritorio y de tableta deben aparecer el mismo rendimiento como con el propio explorador Edge.

## <a name="permissions"></a>Permisos

En orden para `WebView` para que funcione, debe asegurarse de que los permisos se establecen para cada plataforma. Tenga en cuenta que en algunas plataformas, `WebView` funcionará en modo de depuración, pero no cuando se compila para la versión. Eso es porque algunos permisos, como los de acceso a internet en Android, se establecen de manera predeterminada Visual Studio para Mac en el modo de depuración.

- **UWP** &ndash; requiere la capacidad de Internet (cliente y servidor) al mostrar el contenido de la red.
- **Android** &ndash; requiere `INTERNET` solo al mostrar el contenido de la red. El contenido local no requiere ningún permiso especial.
- **iOS** &ndash; no requiere permisos especiales.

## <a name="layout"></a>Diseño

A diferencia de la mayoría de vistas de Xamarin.Forms, `WebView` requiere que `HeightRequest` y `WidthRequest` se especifican al contenido en StackLayout o RelativeLayout. Si no especifica las propiedades, los `WebView` no se representará.

Los ejemplos siguientes muestran los diseños que dar lugar a trabajar, representación `WebView`s:

StackLayout con WidthRequest & HeightRequest:

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

RelativeLayout con WidthRequest & HeightRequest:

```xaml
<RelativeLayout>
    <Label Text="test"
        RelativeLayout.XConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=10}"
        RelativeLayout.YConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=20}" />
    <WebView Source="http://www.xamarin.com/"
        RelativeLayout.XConstraint="{ConstraintExpression Type=Constant,
                                     Constant=10}"
        RelativeLayout.YConstraint="{ConstraintExpression Type=Constant,
                                     Constant=50}"
        WidthRequest="1000" HeightRequest="1000" />
</RelativeLayout>
```

AbsoluteLayout *sin* WidthRequest & HeightRequest:

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

Cuadrícula *sin* WidthRequest & HeightRequest. Cuadrícula es uno de los diseños de algunas que no necesita especificar los anchos y altos solicitadas.:

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="100" />
        <RowDefinition Height="*" />
    </Grid.RowDefinitions>
    <Label Text="test" Grid.Row="0" />
    <WebView Source="http://www.xamarin.com/" Grid.Row="1" />
</Grid>
```

## <a name="invoking-javascript"></a>Invocación de JavaScript

[`WebView`](xref:Xamarin.Forms.WebView) incluye la capacidad para invocar una función de JavaScript desde C#y se devuelve ningún resultado para la llamada a C# código. Esto se consigue con la [ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) método, que se muestra en el ejemplo siguiente de la [WebView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) ejemplo:

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

El [ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) método evalúa el código JavaScript que se especifica como argumento y devuelve los resultados como un `string`. En este ejemplo, el `factorial` se invoca la función de JavaScript, que devuelve el factorial de `number` como resultado. Esta función se define en el código HTML local de JavaScript de archivos que el [ `WebView` ](xref:Xamarin.Forms.WebView) carga y se muestra en el ejemplo siguiente:

```html
<html>
<body>
<script type="text/javascript">
function factorial(num) {
        if (num === 0 || num === 1)
            return 1;
        for (var i = num - 1; i >= 1; i--) {
            num *= i;
        }
        return num;
}
</script>
</body>
</html>
```

## <a name="related-links"></a>Vínculos relacionados

- [Trabajar con la vista Web (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [Vista Web (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
