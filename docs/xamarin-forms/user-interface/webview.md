---
title: Vista previa de Xamarin. Forms
description: En este artículo se explica cómo usar la clase WebView de Xamarin. Forms para presentar documentos y contenido Web local o de red a los usuarios.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2019
ms.openlocfilehash: cdb2a9f1a37dc7bca458a4291ce6fe5ac9c120aa
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696054"
---
# <a name="xamarinforms-webview"></a>Vista previa de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) es una vista para mostrar contenido web y HTML en la aplicación:

![En el explorador de aplicaciones](webview-images/in-app-browser.png)

## <a name="content"></a>Contenido

`WebView` admite los siguientes tipos de contenido:

- HTML & sitios web CSS &ndash; WebView es totalmente compatible con sitios web escritos con HTML & CSS, incluida la compatibilidad con JavaScript.
- Los documentos &ndash; porque WebView se implementa mediante componentes nativos en cada plataforma, WebView es capaz de mostrar documentos que se pueden ver en cada plataforma. Esto significa que los archivos PDF funcionan en iOS y Android.
- Las cadenas HTML &ndash; WebView pueden mostrar cadenas HTML de la memoria.
- Los archivos locales &ndash; WebView pueden presentar cualquiera de los tipos de contenido anteriores incrustados en la aplicación.

> [!NOTE]
> `WebView` en Windows no es compatible con Silverlight, flash ni ningún control ActiveX, aunque sea compatible con Internet Explorer en esa plataforma.

### <a name="websites"></a>Sitios web

Para mostrar un sitio web desde Internet, establezca la propiedad [`Source`](xref:Xamarin.Forms.WebViewSource) del `WebView` en una dirección URL de cadena:

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Las direcciones URL deben estar formadas por completo con el protocolo especificado (es decir, deben tener "http://" o "https://" antepuesto).

#### <a name="ios-and-ats"></a>iOS y ATS

Desde la versión 9, iOS solo permitirá que la aplicación se comunique con los servidores que implementan la seguridad de prácticas recomendadas de forma predeterminada. Los valores se deben establecer en `Info.plist` para habilitar la comunicación con servidores no seguros.

> [!NOTE]
> Si la aplicación requiere una conexión a un sitio web inseguro, siempre debe escribir el dominio como una excepción mediante `NSExceptionDomains` en lugar de desactivar completamente el ATS con `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` solo se debe usar en situaciones extremas de emergencia.

A continuación se muestra cómo habilitar un dominio específico (en este caso, xamarin.com) para omitir los requisitos de ATS:

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

Se recomienda habilitar solo algunos dominios para omitir ATS, lo que le permite usar sitios de confianza mientras benefician de la seguridad adicional en dominios que no son de confianza. A continuación se muestra el método menos seguro para deshabilitar ATS para la aplicación:

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

Consulte [seguridad de transporte de aplicaciones](~/ios/app-fundamentals/ats.md) para obtener más información sobre esta nueva característica en iOS 9.

### <a name="html-strings"></a>Cadenas HTML

Si desea presentar una cadena de HTML definida dinámicamente en el código, deberá crear una instancia de [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource):

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![Vista previa que muestra la cadena HTML](webview-images/html-string.png)

En el código anterior, `@` se usa para marcar el HTML como un literal de cadena, lo que significa que se omiten todos los caracteres de escape habituales.

> [!NOTE]
> Puede que sea necesario establecer las propiedades `WidthRequest` y `HeightRequest` de la [`WebView`](xref:Xamarin.Forms.WebView) para ver el contenido HTML, en función del diseño al que el `WebView` sea un elemento secundario. Por ejemplo, esto es necesario en una [`StackLayout`](xref:Xamarin.Forms.StackLayout).

### <a name="local-html-content"></a>Contenido HTML local

WebView puede mostrar contenido de HTML, CSS y JavaScript incrustado dentro de la aplicación. Por ejemplo:

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

CSS

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

Tenga en cuenta que las fuentes especificadas en la CSS anterior deberán personalizarse para cada plataforma, ya que no todas las plataformas tienen las mismas fuentes.

Para mostrar el contenido local mediante un `WebView`, deberá abrir el archivo HTML como cualquier otro y, a continuación, cargar el contenido como una cadena en la propiedad `Html` de una `HtmlWebViewSource`. Para obtener más información sobre cómo abrir archivos, vea [trabajar con archivos](~/xamarin-forms/data-cloud/data/files.md).

Las siguientes capturas de pantallas muestran el resultado de mostrar el contenido local en cada plataforma:

![Vista previa que muestra el contenido local](webview-images/local-content.png)

Aunque la primera página se ha cargado, el `WebView` no tiene ningún conocimiento de dónde procede el código HTML. Esto es un problema cuando se trabaja con páginas que hacen referencia a recursos locales. Algunos ejemplos de Cuándo se pueden producir cuando las páginas locales se vinculan entre sí, una página utiliza un archivo de JavaScript independiente o una página se vincula a una hoja de estilos CSS.  

Para solucionar este problemas, debe indicar al `WebView` dónde encontrar los archivos en el sistema de archivos. Para ello, establezca la propiedad `BaseUrl` en la `HtmlWebViewSource` utilizada por la `WebView`.

Dado que el sistema de archivos de cada uno de los sistemas operativos es diferente, debe determinar esa dirección URL en cada plataforma. Xamarin. Forms expone el `DependencyService` para resolver las dependencias en tiempo de ejecución en cada plataforma.

Para usar el `DependencyService`, primero debe definir una interfaz que se pueda implementar en cada plataforma:

```csharp
public interface IBaseUrl { string Get(); }
```

Tenga en cuenta que hasta que la interfaz se implementa en cada plataforma, la aplicación no se ejecutará. En el proyecto común, asegúrese de que no se olvide de establecer el `BaseUrl` mediante el `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

A continuación, se deben proporcionar las implementaciones de la interfaz para cada plataforma.

#### <a name="ios"></a>iOS

En iOS, el contenido web debe estar ubicado en el directorio raíz del proyecto o en el directorio de **recursos** con la acción de compilación *BundleResource*, como se muestra a continuación:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Archivos locales en iOS](webview-images/ios-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Archivos locales en iOS](webview-images/ios-xs.png)

-----

El `BaseUrl` debe establecerse en la ruta de acceso del paquete principal:

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

En Android, coloque HTML, CSS e imágenes en la carpeta assets con la acción de compilación *AndroidAsset* como se muestra a continuación:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Archivos locales en Android](webview-images/android-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![Archivos locales en Android](webview-images/android-xs.png)

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

En Android, también se puede tener acceso a los archivos de la carpeta **assets** a través del contexto actual de Android, que se expone mediante la propiedad `MainActivity.Instance`:

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>Plataforma universal de Windows

En los proyectos de Plataforma universal de Windows (UWP), coloque HTML, CSS e imágenes en la raíz del proyecto con la acción de compilación establecida en *contenido*.

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

WebView admite la navegación a través de varios métodos y propiedades que pone a su disposición:

- **GoForward ()** &ndash; si `CanGoForward` es true, la llamada a `GoForward` navega hacia delante hasta la siguiente página visitada.
- **GoBack ()** &ndash; si `CanGoBack` es true, al llamar a `GoBack` se navegará a la última página visitada.
- **CanGoBack** &ndash; `true` si hay páginas a las que navegar de nuevo, `false` si el explorador está en la dirección URL de inicio.
- **CanGoForward** &ndash; `true` si el usuario ha navegado hacia atrás y puede avanzar a una página que ya se ha visitado.

Dentro de las páginas, `WebView` no admite movimientos multitáctiles. Es importante asegurarse de que el contenido esté optimizado para dispositivos móviles y aparezca sin necesidad de hacer zoom.

Es habitual que las aplicaciones muestren un vínculo dentro de un `WebView`, en lugar del explorador del dispositivo. En esas situaciones, es útil permitir la navegación normal, pero cuando el usuario se vuelve a utilizar mientras están en el vínculo de inicio, la aplicación debe volver a la vista normal de la aplicación.

Use los métodos y las propiedades de navegación integrados para habilitar este escenario.

Empiece por crear la página para la vista de explorador:

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

![Botones de navegación de vista previa](webview-images/in-app-browser.png)

## <a name="events"></a>Events

WebView genera los siguientes eventos para ayudarle a responder a los cambios en el estado:

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) : evento que se genera cuando WebView comienza a cargar una nueva página.
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) : evento que se genera cuando se carga la página y se detiene la navegación.
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested) : evento que se genera cuando se realiza una solicitud para volver a cargar el contenido actual.

El objeto [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) que acompaña al evento [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) tiene cuatro propiedades:

- `Cancel`: indica si se debe cancelar o no la navegación.
- `NavigationEvent`: el evento de navegación que se ha producido.
- `Source`: el elemento que llevó a cabo la navegación.
- `Url`: el destino de navegación.

El objeto [`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs) que acompaña al evento [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) tiene cuatro propiedades:

- `NavigationEvent`: el evento de navegación que se ha producido.
- `Result`: describe el resultado de la navegación mediante un miembro de enumeración de [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) . Los valores válidos son `Cancel`, `Failure`, `Success` y `Timeout`.
- `Source`: el elemento que llevó a cabo la navegación.
- `Url`: el destino de navegación.

Si prevé usar páginas web que tardan mucho tiempo en cargarse, considere la posibilidad de usar los eventos [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) y [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) para implementar un indicador de estado. Por ejemplo:

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

Esto da como resultado el siguiente resultado (cargando):

![Ejemplo de evento de navegación de vista en WebView](webview-images/loading-start.png)

Finalizó la carga:

![Ejemplo de evento de navegación de vista en WebView](webview-images/loading-end.png)

## <a name="reloading-content"></a>Recarga de contenido

[`WebView`](xref:Xamarin.Forms.WebView) tiene un método `Reload` que se puede usar para volver a cargar el contenido actual:

```csharp
var webView = new WebView();
...
webView.Reload();
```

Cuando se invoca el método de `Reload`, se desencadena el evento `ReloadRequested`, lo que indica que se ha realizado una solicitud para recargar el contenido actual.

## <a name="performance"></a>Rendimiento

Los conocidos exploradores Web ahora adoptan tecnologías como la representación acelerada de hardware y la compilación de JavaScript. En iOS, de forma predeterminada, el `WebView` de Xamarin. Forms se implementa mediante la clase `UIWebView`, y muchas de estas tecnologías no están disponibles en esta implementación. Sin embargo, una aplicación puede optar por usar la clase de `WkWebView` de iOS para implementar la `WebView` de Xamarin. Forms, que admite una exploración más rápida. Esto se puede lograr agregando el código siguiente al archivo **AssemblyInfo.CS** en el proyecto de la plataforma iOS para la aplicación:

```csharp
// Opt-in to using WkWebView instead of UIWebView.
[assembly: ExportRenderer(typeof(WebView), typeof(Xamarin.Forms.Platform.iOS.WkWebViewRenderer))]
```

> [!NOTE]
> En iOS, el `WkWebViewRenderer` tiene una sobrecarga de constructor que acepta un argumento de `WkWebViewConfiguration`. Esto permite configurar el representador durante la creación.

de forma predeterminada, `WebView` en Android es tan rápido como el explorador integrado.

La [WebView de UWP](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) usa el motor de representación de Microsoft Edge. Los dispositivos de escritorio y tableta deben ver el mismo rendimiento que usar el explorador Edge.

## <a name="permissions"></a>Permisos

Para que `WebView` funcionen, debe asegurarse de que los permisos se establecen para cada plataforma. Tenga en cuenta que en algunas plataformas, `WebView` funcionará en modo de depuración, pero no cuando se compile para la versión. Esto se debe a que algunos permisos, como los de acceso a Internet en Android, se establecen de forma predeterminada en Visual Studio para Mac en modo de depuración.

- **UWP** &ndash; requiere la funcionalidad de Internet (servidor de & de cliente) al mostrar el contenido de la red.
- **Android** &ndash; solo requiere `INTERNET` al mostrar el contenido de la red. El contenido local no requiere ningún permiso especial.
- **iOS** &ndash; no requiere ningún permiso especial.

## <a name="layout"></a>Diseño

A diferencia de la mayoría de las demás vistas de Xamarin. Forms, `WebView` requiere que se especifiquen `HeightRequest` y `WidthRequest` cuando se incluyen en StackLayout o RelativeLayout. Si no se especifican esas propiedades, no se representará el `WebView`.

En los siguientes ejemplos se muestran los diseños que dan como resultado el trabajo, la representación de `WebView`s:

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

Grid *sin* WidthRequest & HeightRequest. Grid es uno de los pocos diseños que no requieren la especificación de alto y ancho solicitados.:

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

## <a name="invoking-javascript"></a>Invocar JavaScript

[`WebView`](xref:Xamarin.Forms.WebView) incluye la capacidad de invocar una función de C#JavaScript desde y devolver cualquier resultado al código C# de llamada. Esto se logra con el método [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) , que se muestra en el ejemplo siguiente desde el ejemplo [WebView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) :

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

El método [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) evalúa el código JavaScript que se especifica como argumento y devuelve cualquier resultado como `string`. En este ejemplo, se invoca la función de JavaScript `factorial`, que devuelve el factorial de `number` como resultado. Esta función de JavaScript se define en el archivo HTML local que carga el [`WebView`](xref:Xamarin.Forms.WebView) y se muestra en el ejemplo siguiente:

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

- [Trabajar con WebView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [WebView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
