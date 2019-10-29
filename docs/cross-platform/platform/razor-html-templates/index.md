---
title: Compilar vistas HTML mediante plantillas de Razor
description: " El uso de una página web de pantalla completa para representar HTML puede ser una manera sencilla y eficaz de representar el formato complejo de una manera multiplataforma, especialmente si ya tiene el código HTML, JavaScript y CSS de un proyecto de sitio Web."
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: davidortinau
ms.author: daortin
ms.date: 07/24/2018
ms.openlocfilehash: 5f1b1345f9abbf891cfbea6e45a8ed2abd7c0dac
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014600"
---
# <a name="building-html-views-using-razor-templates"></a>Compilar vistas HTML mediante plantillas de Razor

En el mundo del desarrollo móvil, el término "aplicación híbrida" hace referencia normalmente a una aplicación que presenta algunas (o todas) sus pantallas como páginas HTML dentro de un control de visor web hospedado.

Hay algunos entornos de desarrollo que le permiten compilar su aplicación móvil completamente en HTML y JavaScript, pero esas aplicaciones pueden sufrir problemas de rendimiento al intentar realizar complejos procesos o efectos de la interfaz de usuario, y también están limitadas en la plataforma características a las que pueden tener acceso.

Xamarin ofrece lo mejor de ambos mundos, especialmente cuando se usa el motor de plantillas HTML de Razor. Con Xamarin, tiene la flexibilidad de crear vistas HTML con plantillas multiplataforma que usan JavaScript y CSS, pero también tiene acceso completo a las API de la plataforma subyacente y un procesamiento C#rápido mediante.

En este documento se explica cómo usar el motor de plantillas Razor para compilar vistas HTML + JavaScript + CSS que se pueden usar en plataformas móviles con Xamarin.

## <a name="using-web-views-programmatically"></a>Usar vistas Web mediante programación

Antes de obtener información sobre Razor en esta sección se explica cómo usar las vistas web para mostrar contenido HTML directamente, específicamente el contenido HTML que se genera dentro de una aplicación.

Xamarin proporciona acceso completo a las API de la plataforma subyacente en iOS y Android, por lo que es fácil crear y mostrar HTML C#con. A continuación se muestra la sintaxis básica de cada plataforma.

### <a name="ios"></a>iOS

Mostrar HTML en un control UIWebView en Xamarin. iOS también toma solo unas pocas líneas de código:

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

Consulte las recetas de [UIWebView de iOS](https://github.com/xamarin/docs-archive/tree/master/Recipes/ios/content_controls/web_view) para obtener más información sobre el uso del control UIWebView.

### <a name="android"></a>Android

Mostrar HTML en un control WebView con Xamarin. Android se realiza con solo unas pocas líneas de código:

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable JavaScript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

Consulte las recetas de la [vista previa de Android](https://github.com/xamarin/docs-archive/tree/master/Recipes/android/controls/webview) para obtener más información sobre el uso del control WebView.

### <a name="specifying-the-base-directory"></a>Especificar el directorio base

En ambas plataformas hay un parámetro que especifica el directorio base para la página HTML. Esta es la ubicación en el sistema de archivos del dispositivo que se usa para resolver referencias relativas a recursos como imágenes y archivos CSS. Por ejemplo, etiquetas como

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

Consulte estos archivos: **style. CSS**, **Monkey. jpg** y **JScript. js**. La configuración del directorio base indica a la vista web dónde se encuentran estos archivos para que se puedan cargar en la página.

#### <a name="ios"></a>iOS

La salida de la plantilla se representa en iOS con el C# código siguiente:

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

El directorio base se especifica como `NSBundle.MainBundle.BundleUrl` que hace referencia al directorio en el que está instalada la aplicación. Todos los archivos de la carpeta **recursos** se copian en esta ubicación, como el archivo **style. CSS** que se muestra aquí:

 ![solución iPhoneHybrid](images/image1_240x163.png)

La acción de compilación para todos los archivos de contenido estático debe ser **BundleResource**:

 ![acción de compilación del proyecto de iOS: BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android también requiere que se pase un directorio base como parámetro cuando se muestran cadenas HTML en una vista Web.

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

La cadena especial **File:///android_asset/** hace referencia a la carpeta activos de Android de la aplicación, que se muestra aquí con el archivo **style. CSS** .

 ![Solución AndroidHybrid](images/image3_240x167.png)

La acción de compilación para todos los archivos de contenido estático debe ser **AndroidAsset**.

 ![Acción de compilación del proyecto de Android: AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>Llamar C# a desde HTML y JavaScript

Cuando se carga una página HTML en una vista Web, trata los vínculos y los formularios tal como lo haría si la página se cargara desde un servidor. Esto significa que si el usuario hace clic en un vínculo o envía un formulario, la vista Web intentará desplazarse hasta el destino especificado.

Si el vínculo se realiza a un servidor externo (como google.com), la vista Web intentará cargar el sitio web externo (suponiendo que haya una conexión a Internet).

```html
<a href="http://google.com/">Google</a>
```

Si el vínculo es relativo, la vista Web intentará cargar el contenido del directorio base. Obviamente, no se requiere ninguna conexión de red para que funcione, ya que el contenido se almacena en la aplicación en el dispositivo.

```html
<a href="somepage.html">Local content</a>
```

Las acciones de formulario siguen la misma regla.

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

No va a hospedar un servidor Web en el cliente. sin embargo, puede usar las mismas técnicas de comunicación de servidor empleadas en los patrones de diseño con capacidad de respuesta de hoy en día para llamar a los servicios a través de HTTP GET y controlar las respuestas de forma asincrónica emitiendo JavaScript (o llamando a JavaScript ya hospedado en la vista Web). Esto le permite pasar fácilmente los datos del HTML al C# código para su procesamiento y, a continuación, volver a mostrar los resultados en la página HTML.

Tanto iOS como Android proporcionan un mecanismo para que el código de aplicación intercepte estos eventos de navegación para que el código de la aplicación pueda responder (si es necesario). Esta característica es fundamental para compilar aplicaciones híbridas, ya que permite que el código nativo interactúe con la vista Web.

#### <a name="ios"></a>iOS

El evento ShouldStartLoad de la vista Web en iOS se puede invalidar para permitir que el código de la aplicación controle una solicitud de navegación (por ejemplo, un clic en un vínculo). Los parámetros de método proporcionan toda la información

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

y, a continuación, asigne el controlador de eventos:

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

En Android, simplemente subclase WebViewClient y, a continuación, implemente el código para responder a la solicitud de navegación.

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, IWebResourceRequest request) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

y después establezca el cliente en la vista Web:

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>Llamar a JavaScript desde C\#

Además de indicar a una vista Web que cargue una nueva página HTML, C# el código también puede ejecutar JavaScript en la página que se muestra actualmente. Los bloques de código JavaScript completos pueden crearse mediante C# cadenas y ejecutarse, o puede crear llamadas de método a JavaScript que ya están disponibles en la página a través de`script`etiquetas.

#### <a name="android"></a>Android

Cree el código JavaScript que se va a ejecutar y, a continuación, asígnele el prefijo "JavaScript:" e indique a la vista Web que cargue esa cadena:

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

las vistas Web de iOS proporcionan un método específico para llamar a JavaScript:

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>Resumen

En esta sección se han incorporado las características de los controles de vista Web en Android e iOS que nos permiten crear aplicaciones híbridas con Xamarin, entre las que se incluyen:

- La capacidad de cargar HTML a partir de cadenas generadas en el código.
- La capacidad de hacer referencia a archivos locales (CSS, JavaScript, imágenes u otros archivos HTML).
- La capacidad de interceptar solicitudes de C# navegación en el código.
- La capacidad de llamar a JavaScript C# desde el código.

En la siguiente sección se presenta Razor, que facilita la creación del código HTML para usarlo en aplicaciones híbridas.

## <a name="what-is-razor"></a>¿Qué es Razor?

Razor es un motor de plantillas que se presentó con ASP.NET MVC, originalmente para ejecutarse en el servidor y generar HTML para servir a los exploradores Web.

El motor de plantillas de Razor amplía la sintaxis C# HTML estándar con para que pueda expresar el diseño e incorporar fácilmente hojas de estilos CSS y JavaScript. La plantilla puede hacer referencia a una clase de modelo, que puede ser cualquier tipo personalizado y a cuyas propiedades se puede obtener acceso directamente desde la plantilla. Una de sus principales ventajas es la capacidad de mezclar HTML y C# sintaxis fácilmente.

Las plantillas de Razor no se limitan al uso del lado servidor, también se pueden incluir en aplicaciones de Xamarin. El uso de plantillas de Razor junto con la capacidad de trabajar con vistas Web mediante programación permite crear aplicaciones híbridas multiplataforma sofisticadas con Xamarin.

### <a name="razor-template-basics"></a>Aspectos básicos de las plantillas de Razor

Los archivos de plantilla de Razor tienen una extensión de archivo **. cshtml** . Se pueden agregar a un proyecto de Xamarin en la sección de plantillas de texto del cuadro de diálogo **nuevo archivo** :

 ![Nuevo archivo: plantilla de Razor](images/image5_400x201.png)

A continuación se muestra una plantilla de Razor simple ( **RazorView. cshtml**).

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

Tenga en cuenta las siguientes diferencias con respecto a un archivo HTML normal:

- El símbolo de `@` tiene un significado especial en las plantillas de Razor: indica que se C# va a evaluar la expresión siguiente.
- `@model` Directiva siempre aparece como la primera línea de un archivo de plantilla de Razor.
- La Directiva de `@model` debe ir seguida de un tipo. En este ejemplo, se pasa una cadena simple a la plantilla, pero puede ser cualquier clase personalizada.
- Cuando se hace referencia a `@Model` a través de la plantilla, se proporciona una referencia al objeto que se pasa a la plantilla cuando se genera (en este ejemplo será una cadena).
- El IDE generará automáticamente una clase parcial para las plantillas (archivos con la extensión **. cshtml** ). Puede ver este código, pero no se debe editar.
 ![RazorView. cshtml](images/image6_125x34.png) la clase parcial se denomina RazorView para que coincida con el nombre de archivo de la plantilla. cshtml. Este es el nombre que se usa para hacer referencia a la plantilla C# en el código.
- `@using` instrucciones también se pueden incluir en la parte superior de una plantilla de Razor para incluir espacios de nombres adicionales.

A continuación, la salida HTML final se puede generar con C# el código siguiente. Tenga en cuenta que especificamos que el modelo es una cadena "Hola mundo" que se incorporará a la salida de la plantilla representada.

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

Este es el resultado que se muestra en una vista Web en el simulador de iOS y Android Emulator:

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>Más sintaxis de Razor

En esta sección vamos a presentar algunos sintaxis Razor básicos para ayudarle a empezar a usarlo. Los ejemplos de esta sección rellenan la siguiente clase con datos y la muestran con Razor:

```csharp
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

Todos los ejemplos usan el siguiente código de inicialización de datos

```csharp
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>Mostrar Propiedades del modelo

Cuando el modelo es una clase con propiedades, se puede hacer referencia a ellos fácilmente en la plantilla de Razor, tal como se muestra en esta plantilla de ejemplo:

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

Esto se puede representar en una cadena mediante el código siguiente:

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

La salida final se muestra aquí en una vista Web en el simulador de iOS y Android Emulator:

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>C#afirma

En la C# plantilla se pueden incluir más complejos, como las actualizaciones de las propiedades del modelo y el cálculo de la edad en este ejemplo:

```html
@model Monkey
<html>
    <body>
    @{
        Model.Name = "Rupert X. Monkey";
        Model.Birthday = new DateTime(2011,3,1);
    }
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    </body>
</html>
```

Puede escribir expresiones de una sola línea C# complejas (como el formato de la edad) rodeando el código con `@()`.

Se C# pueden escribir varias instrucciones con `@{}`.

#### <a name="if-else-statements"></a>Instrucciones IF-Else

Las bifurcaciones de código se pueden expresar con `@if` tal y como se muestra en este ejemplo de plantilla.

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <p>@Model.FavoriteFoods.Count favorites</p>
    }
    </body>
</html>
```

#### <a name="loops"></a>Bucles

También se pueden agregar construcciones de bucle como `foreach`. El prefijo `@` se puede usar en la variable de bucle (`@food` en este caso) para representarlo en HTML.

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <ul>
            @foreach (var food in @Model.FavoriteFoods) {
                <li>@food</li>
            }
        </ul>
    }
    </body>
</html>
```

La salida de la plantilla anterior se muestra en ejecución en el simulador de iOS y Android Emulator:

 ![Rupert X Monkey](images/image9_520x277.png)

En esta sección se han tratado los aspectos básicos del uso de plantillas de Razor para representar vistas simples de solo lectura. En la sección siguiente se explica cómo compilar aplicaciones más completas con Razor que pueden aceptar la entrada del usuario e interoperar entre JavaScript C#en la vista HTML y.

## <a name="using-razor-templates-with-xamarin"></a>Uso de plantillas de Razor con Xamarin

En esta sección se explica cómo usar la creación de su propia aplicación híbrida con las plantillas de solución de Visual Studio para Mac. Hay tres plantillas disponibles en el **archivo > nueva >** la ventana de la solución...:

- **Aplicación Android > > aplicación WebView de Android**
- **Aplicación de > de iOS > aplicación WebView**
- **Proyecto ASP.NET MVC**

La **nueva** ventana de la solución tiene el siguiente aspecto para los proyectos de iPhone y Android: la descripción de la solución de la derecha destaca la compatibilidad con el motor de plantillas de Razor.

 ![Creación de soluciones de iPhone y Android](images/image13_1139x959.png)

Tenga en cuenta que puede agregar fácilmente una plantilla Razor de **. cshtml** a *cualquier* proyecto de Xamarin existente, no es necesario usar estas plantillas de solución. los proyectos de iOS no requieren un guion gráfico para usar Razor; simplemente agregue un control UIWebView a cualquier vista mediante programación y puede representar plantillas de Razor completo en C# el código.

A continuación se muestra el contenido de la solución de plantilla predeterminada para los proyectos de iPhone y Android:

 ![plantillas de iPhone y Android](images/image10_428x310.png)

Las plantillas proporcionan una infraestructura de aplicaciones lista para usar para cargar una plantilla de Razor con un objeto de modelo de datos, procesar la entrada del usuario y comunicarse de nuevo con el usuario a través de JavaScript.

Las partes importantes de la solución son:

- Contenido estático como el archivo **style. CSS** .
- Archivos de plantilla Razor. cshtml como **RazorView. cshtml** .
- Clases de modelo a las que se hace referencia en las plantillas de Razor, como **ExampleModel.CS** .
- La clase específica de la plataforma que crea la vista Web y representa la plantilla, como el `MainActivity` en Android y el `iPhoneHybridViewController` en iOS.

En la siguiente sección se explica cómo funcionan los proyectos.

### <a name="static-content"></a>Contenido estático

El contenido estático incluye hojas de estilos CSS, imágenes, archivos JavaScript u otro contenido que se puede vincular desde un archivo HTML que se muestra en una vista Web o al que se hace referencia en él.

Los proyectos de plantilla incluyen una hoja de estilos mínima para demostrar cómo incluir contenido estático en la aplicación híbrida. En la plantilla se hace referencia a la hoja de estilos CSS:

```html
<link rel="stylesheet" href="style.css" />
```

Puede agregar cualquier hoja de estilos y los archivos JavaScript que necesite, incluidos los marcos como JQuery.

### <a name="razor-cshtml-templates"></a>Plantillas de la cshtml de Razor

La plantilla incluye un archivo Razor **. cshtml** que tiene código escrito previamente para facilitar la comunicación de datos entre HTML/JavaScript y C#. Esto le permitirá crear sofisticadas aplicaciones híbridas que no solo muestran datos de solo lectura del modelo, sino que también aceptan la entrada del usuario en el código HTML y la C# devuelven al código para su procesamiento o almacenamiento.

#### <a name="rendering-the-template"></a>Representación de la plantilla

La llamada al `GenerateString` en una plantilla representa el código HTML preparado para su presentación en una vista Web. Si la plantilla usa un modelo, debe proporcionarse antes de la representación. En este diagrama se muestra cómo funciona la representación: no se resuelven los recursos estáticos mediante la vista Web en tiempo de ejecución, utilizando el directorio base proporcionado para buscar los archivos especificados.

 ![Diagrama de flujo de Razor](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>Llamar C# a código desde la plantilla

La comunicación desde una vista Web representada que C# devuelve una llamada a se realiza estableciendo la dirección URL de la vista Web y, a continuación C# , interceptando la solicitud en para controlar la solicitud nativa sin volver a cargar la vista Web.

Un ejemplo puede verse en cómo se controla el botón de RazorView. El botón tiene el siguiente código HTML:

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

La función `InvokeCSharpWithFormValues` JavaScript Lee todos los valores del formulario HTML y establece el `location.href` para la vista Web:

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

Esto intenta navegar por la vista Web a una dirección URL con un esquema personalizado (por ejemplo, `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

Cuando la vista Web nativa procesa esta solicitud de navegación, tenemos la oportunidad de interceptarla. En iOS, esto se realiza mediante el control del evento HandleShouldStartLoad de UIWebView. En Android, simplemente subclasemos el WebViewClient que se usa en el formulario e invalidan ShouldOverrideUrlLoading.

El interior de estos dos interceptores de navegación es esencialmente el mismo.

En primer lugar, Compruebe la dirección URL que está intentando cargar la vista Web y, si no se inicia con el esquema personalizado (`hybrid:`), permita que la navegación se realice de la forma habitual.

Para el esquema de dirección URL personalizada, todo en la dirección URL entre el esquema y el es el nombre del método que se va a controlar (en este caso, "UpdateLabel"). Todo lo que aparece en la cadena de consulta se tratará como los parámetros de la llamada al método:

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel` en este ejemplo realiza una cantidad mínima de manipulación de cadenas en el parámetro de cuadro de textoC# (anteponiendo "dice" a la cadena) y, a continuación, vuelve a llamar a la vista Web.

Después de administrar la dirección URL, el método anula la navegación para que la vista Web no intente terminar de navegar a la dirección URL personalizada.

#### <a name="manipulating-the-template-from-c"></a>Manipular la plantilla desde C\#

La comunicación con una vista web HTML presentada desde C# se realiza mediante una llamada a JavaScript en la vista Web. En iOS, esto se hace llamando a `EvaluateJavascript` en UIWebView:

```csharp
webView.EvaluateJavascript (js);
```

En Android, se puede invocar JavaScript en la vista Web mediante la carga de JavaScript como una dirección URL mediante el esquema de URL de `"javascript:"`:

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>Crear una aplicación realmente híbrida

Estas plantillas no hacen uso de controles nativos en cada plataforma; toda la pantalla se rellena con una sola vista Web.

HTML puede ser excelente para crear prototipos y mostrar los tipos de elementos que la web es mejor en, como el texto enriquecido y el diseño dinámico. Sin embargo, no todas las tareas son adecuadas para HTML y JavaScript: desplazarse a través de listas largas de datos, por ejemplo, funciona mejor con los controles de interfaz de usuario nativos (como UITableView en iOS o ListView en Android).

Las vistas Web de la plantilla se pueden aumentar fácilmente con controles específicos de la plataforma: simplemente Edite **archivo mainstoryboard. Storyboard** en iOS Designer o en **Resources/layout/main. axml** en Android.

### <a name="razortodo-sample"></a>Ejemplo de RazorTodo

El repositorio [RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo) contiene dos soluciones independientes para mostrar las diferencias entre una aplicación completamente controlada por HTML y una aplicación que combina HTML con controles nativos:

- **RazorTodo** : aplicación controlada por HTML completamente con plantillas de Razor.
- **RazorNativeTodo** : usa controles de vista de lista nativos para iOS y Android, pero muestra la pantalla de edición con HTML y Razor.

Estas aplicaciones Xamarin se ejecutan en iOS y Android, con las bibliotecas de clases portables (PCL) para compartir código común, como la base de datos y las clases de modelo. Las plantillas Razor **. cshtml** también se pueden incluir en la PCL para que se compartan fácilmente entre las plataformas.

Ambas aplicaciones de ejemplo incorporan las API de texto a voz y el uso compartido de Twitter desde la plataforma nativa, lo que demuestra que las aplicaciones híbridas con Xamarin todavía tienen acceso a toda la funcionalidad subyacente de las vistas orientadas a plantillas HTML de Razor.

La aplicación **RazorTodo** usa plantillas de Razor HTML para las vistas de lista y edición. Esto significa que podemos compilar la aplicación casi por completo en una biblioteca de clases portable compartida (incluida la base de datos y las plantillas de Razor de **. cshtml** ). Las capturas de pantallas siguientes muestran las aplicaciones iOS y Android.

 ![RazorTodo](images/Both_700x290.png)

La aplicación **RazorNativeTodo** usa una plantilla de Razor HTML para la vista de edición, pero implementa una lista de desplazamiento nativo en cada plataforma. Esto proporciona varias ventajas, entre las que se incluyen:

- Rendimiento: los controles de desplazamiento nativo utilizan la virtualización para garantizar un desplazamiento rápido y suave incluso con listas muy largas de datos.
- Experiencia nativa: los elementos de interfaz de usuario específicos de la plataforma se habilitan con facilidad, como la compatibilidad con el índice de desplazamiento rápido en iOS y Android.

Una ventaja clave de la creación de aplicaciones híbridas con Xamarin es que puede empezar con una interfaz de usuario controlada por HTML completamente (como la primera muestra) y, después, agregar funcionalidad específica de la plataforma cuando sea necesario (como se muestra en el segundo ejemplo). A continuación se muestran las pantallas de listas nativas y las pantallas de edición de Razor HTML en iOS y Android.

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>Resumen

En este artículo se han explicado las características de los controles de vista Web disponibles en iOS y Android que facilitan la creación de aplicaciones híbridas.

A continuación, se describe el motor de plantillas de Razor y la sintaxis que se puede usar para generar HTML fácilmente en las aplicaciones de Xamarin con. **cshtml** Archivos de plantilla de Razor. También se describen las plantillas de soluciones de Visual Studio para Mac que permiten comenzar a compilar aplicaciones híbridas rápidamente con Xamarin.

Finalmente, se introdujeron los ejemplos de RazorTodo que muestran cómo combinar vistas web con las interfaces de usuario y las API nativas.

### <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3-motor de vistas de Razor (Microsoft)](https://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [Introducción a la programación web de ASP.NET mediante la sintaxis de Razor (Microsoft)](https://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)
