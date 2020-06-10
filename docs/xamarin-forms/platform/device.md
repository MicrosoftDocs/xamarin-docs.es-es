---
title: " Xamarin.Forms Device class" Description: "en este artículo se explica cómo usar la Xamarin.Forms clase Device para tener un mayor control sobre la funcionalidad y los diseños en cada plataforma".
MS. Prod: Xamarin ms. AssetID: 2F304AEC-8612-4833-81E5-B2F3F469B2DF ms. Technology: Xamarin-Forms Author: davidbritch ms. Author: dabritch ms. Date: 04/17/2020 no-LOC: [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-device-class"></a>Xamarin.FormsClase de dispositivo

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

La [`Device`](xref:Xamarin.Forms.Device) clase contiene una serie de propiedades y métodos para ayudar a los desarrolladores a personalizar el diseño y la funcionalidad para cada plataforma.

Además de los métodos y las propiedades para el código de destino en tipos y tamaños de hardware específicos, la `Device` clase incluye métodos que se pueden usar para interactuar con los controles de interfaz de usuario de los subprocesos en segundo plano. Para obtener más información, vea [interactuar con la interfaz de usuario de subprocesos en segundo plano](#interact-with-the-ui-from-background-threads).

## <a name="provide-platform-specific-values"></a>Proporcionar valores específicos de la plataforma

Antes de Xamarin.Forms 2.3.4, la plataforma en la que se ejecutaba la aplicación se podía obtener examinando la [`Device.OS`](xref:Xamarin.Forms.Device.OS) propiedad y comparándola con los [`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS) [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android) valores de [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone) enumeración,, y [`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) . De forma similar, una de las [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) sobrecargas podría usarse para proporcionar valores específicos de la plataforma a un control.

Sin embargo, como Xamarin.Forms 2.3.4, estas API han quedado en desuso y se han reemplazado. La [`Device`](xref:Xamarin.Forms.Device) clase contiene ahora constantes de cadena públicas que identifican las plataformas: [`Device.iOS`](xref:Xamarin.Forms.Device.iOS) , [`Device.Android`](xref:Xamarin.Forms.Device.Android) , `Device.WinPhone` (en desuso), `Device.WinRT` (desusado), [`Device.UWP`](xref:Xamarin.Forms.Device.UWP) y [`Device.macOS`](xref:Xamarin.Forms.Device.macOS) . Del mismo modo, las [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) sobrecargas se han reemplazado por las [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) API y.

En C#, se pueden proporcionar valores específicos de la plataforma mediante la creación de una `switch` instrucción en la [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) propiedad y, a continuación, proporcionar `case` instrucciones para las plataformas necesarias:

```csharp
double top;
switch (Device.RuntimePlatform)
{
  case Device.iOS:
    top = 20;
    break;
  case Device.Android:
  case Device.UWP:
  default:
    top = 0;
    break;
}
layout.Margin = new Thickness(5, top, 5, 0);
```

Las [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) [`On`](xref:Xamarin.Forms.On) clases y proporcionan la misma funcionalidad en XAML:

```xaml
<StackLayout>
  <StackLayout.Margin>
    <OnPlatform x:TypeArguments="Thickness">
      <On Platform="iOS" Value="0,20,0,0" />
      <On Platform="Android, UWP" Value="0,0,0,0" />
    </OnPlatform>
  </StackLayout.Margin>
  ...
</StackLayout>
```

La [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) clase es una clase genérica en la que se deben crear instancias con un `x:TypeArguments` atributo que coincida con el tipo de destino. En la [`On`](xref:Xamarin.Forms.On) clase, el [`Platform`](xref:Xamarin.Forms.On.Platform) atributo puede aceptar un solo `string` valor o varios valores delimitados por comas `string` .

> [!IMPORTANT]
> Proporcionar un `Platform` valor de atributo incorrecto en la `On` clase no producirá un error. En su lugar, se ejecutará el código sin que se aplique el valor específico de la plataforma.

Como alternativa, `OnPlatform` se puede usar la extensión de marcado en XAML para personalizar la apariencia de la interfaz de usuario en cada plataforma. Para obtener más información, consulte [extensión de marcado](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension)en la plataforma.

## <a name="deviceidiom"></a>Dispositivo. expresión

La `Device.Idiom` propiedad se puede usar para modificar los diseños o la funcionalidad según el dispositivo en el que se ejecuta la aplicación. La [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) enumeración contiene los siguientes valores:

- **Teléfono** : dispositivos iPhone, iPod Touch y Android más estrechos que 600 DIP ^
- **Tableta** : iPad, dispositivos Windows y dispositivos Android más anchos que 600 DIP ^
- **Escritorio** : solo se devuelve en [aplicaciones para UWP](~/xamarin-forms/platform/windows/installation/index.md) en equipos de escritorio de Windows 10 (se devuelve `Phone` en dispositivos móviles de Windows, incluidos los escenarios de continuum).
- **TV** : dispositivos de TV Tizen
- **Inspección** : Tizen Watch Devices
- **No compatible** : sin usar

*^ DIP no es necesariamente el número de píxeles físicos*

La `Idiom` propiedad es especialmente útil para compilar diseños que aprovechan pantallas más grandes, como esto:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

La [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) clase proporciona la misma funcionalidad en XAML:

```xaml
<StackLayout>
    <StackLayout.Margin>
        <OnIdiom x:TypeArguments="Thickness">
            <OnIdiom.Phone>0,20,0,0</OnIdiom.Phone>
            <OnIdiom.Tablet>0,40,0,0</OnIdiom.Tablet>
            <OnIdiom.Desktop>0,60,0,0</OnIdiom.Desktop>
        </OnIdiom>
    </StackLayout.Margin>
    ...
</StackLayout>
```

La [`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1) clase es una clase genérica en la que se deben crear instancias con un `x:TypeArguments` atributo que coincida con el tipo de destino.

Como alternativa, `OnIdiom` se puede usar la extensión de marcado en XAML para personalizar la apariencia de la interfaz de usuario en función de la expresión del dispositivo en el que se ejecuta la aplicación. Para obtener más información, vea [extensión de marcado en idioma](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom-markup-extension).

## <a name="deviceflowdirection"></a>Device. FlowDirection

El [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valor recupera un [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) valor de enumeración que representa la dirección de flujo actual utilizada por el dispositivo. La dirección de flujo es la dirección en la que el ojo humano lee los elementos de la interfaz de usuario en la página. Los valores de la enumeración son:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

En XAML, el [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) valor se puede recuperar mediante la `x:Static` extensión de marcado:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

El código equivalente en C# es:

```csharp
this.FlowDirection = Device.FlowDirection;
```

Para obtener más información acerca de la dirección de flujo, consulte [localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="devicestyles"></a>Device. Styles

La [ `Styles` propiedad](~/xamarin-forms/user-interface/styles/index.md) contiene definiciones de estilo integradas que se pueden aplicar a algunos controles de la propiedad (como `Label` ) `Style` . Los estilos disponibles son:

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Device. GetNamedSize

`GetNamedSize`se puede usar al establecer [`FontSize`](~/xamarin-forms/user-interface/text/fonts.md) en el código de C#:

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicegetnamedcolor"></a>Device.GetNamedColor

Xamarin.Forms4,6 introduce compatibilidad con los colores con nombre. Un color con nombre es un color que tiene un valor diferente en función del modo del sistema (por ejemplo, claro o oscuro) que está activo en el dispositivo. En Android, se tiene acceso a los colores con nombre a través de la clase [R. color](https://developer.android.com/reference/android/R.color#constants_2) . En iOS, los colores con nombre se denominan [colores del sistema](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#system-colors). En el Plataforma universal de Windows, los colores con nombre se denominan [recursos de tema XAML](/windows/uwp/design/controls-and-patterns/xaml-theme-resources).

El `GetNamedColor` método se puede usar para recuperar los colores con nombre en Android, iOS y UWP. El método toma un `string` argumento y devuelve [`Color`](xref:Xamarin.Forms.Color) :

```csharp
// Retrieve an Android named color
Color color = Device.GetNamedColor(NamedPlatformColor.HoloBlueBright);
```

`Color.Default`se devolverá cuando no se pueda encontrar un nombre de color o cuando `GetNamedColor` se invoque en una plataforma no compatible.

> [!NOTE]
> Dado `GetNamedColor` que el método devuelve un `Color` que es específico de una plataforma, normalmente se debe utilizar junto con la [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) propiedad.

La `NamedPlatformColor` clase contiene las constantes que definen los colores con nombre para Android, iOS y UWP:

| Android | iOS | UWP |
| --- | --- | --- |
| `BackgroundDark` | `Label` | `SystemAltHighColor` |
| `BackgroundLight` | `Link` | `SystemAltLowColor` |
| `Black` | `OpaqueSeparator` | `SystemAltMediumColor` |
| `DarkerGray` | `PlaceholderText` | `SystemAltMediumHighColor` |
| `HoloBlueBright` | `QuaternaryLabel` | `SystemAltMediumLowColor` |
| `HoloBlueDark` | `SecondaryLabel` | `SystemBaseHighColor` |
| `HoloBlueLight` | `Separator` | `SystemBaseLowColor` |
| `HoloGreenDark` | `SystemBlue` | `SystemBaseMediumColor` |
| `HoloGreenLight` | `SystemGray` | `SystemBaseMediumHighColor` |
| `HoloOrangeDark` | `SystemGray2` | `SystemBaseMediumLowColor` |
| `HoloOrangeLight` | `SystemGray3` | `SystemChromeAltLowColor` |
| `HoloPurple` | `SystemGray4` | `SystemChromeBlackHighColor` |
| `HoloRedDark` | `SystemGray5` | `SystemChromeBlackLowColor` |
| `HoloRedLight` | `SystemGray6` | `SystemChromeBlackMediumColor` |
| `TabIndicatorText` | `SystemGreen` | `SystemChromeBlackMediumLowColor` |
| `Transparent` | `SystemIndigo` | `SystemChromeDisabledHighColor` |
| `White` | `SystemListLowColor` | `SystemChromeDisabledLowColor` |
| `WidgetEditTextDark` | `SystemListMediumColor` | `SystemChromeHighColor` |
| | `SystemPink` | `SystemChromeLowColor` |
| | `SystemPurple` | `SystemChromeMediumColor` |
| | `SystemRed` | `SystemChromeMediumLowColor` |
| | `SystemTeal` | `SystemChromeWhiteColor` |
| | `SystemYellow` |
| | `TertiaryLabel` |

## <a name="devicestarttimer"></a>Device. StartTimer

La `Device` clase también tiene un `StartTimer` método que proporciona una manera sencilla de desencadenar tareas dependientes del tiempo que funcionan en Xamarin.Forms código común, incluida una biblioteca .net Standard. Pase un `TimeSpan` para establecer el intervalo y volver `true` a mantener el temporizador en ejecución o `false` detenerlo después de la invocación actual.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Si el código dentro del temporizador interactúa con la interfaz de usuario (por ejemplo, al establecer el texto de un `Label` o mostrar una alerta), debe realizarse dentro de una `BeginInvokeOnMainThread` expresión (consulte a continuación).

> [!NOTE]
> Las `System.Timers.Timer` `System.Threading.Timer` clases y son .net Standard alternativas al uso del `Device.StartTimer` método.

## <a name="interact-with-the-ui-from-background-threads"></a>Interacción con la interfaz de usuario desde subprocesos en segundo plano

La mayoría de los sistemas operativos, incluidos iOS, Android y el Plataforma universal de Windows, utilizan un modelo de subprocesamiento único para el código que implica la interfaz de usuario. Este subproceso se llama a menudo el *subproceso principal* o el *subproceso*de la interfaz de usuario. Una consecuencia de este modelo es que todo el código que tiene acceso a los elementos de la interfaz de usuario se debe ejecutar en el subproceso principal de la aplicación.

A veces, las aplicaciones utilizan subprocesos en segundo plano para realizar operaciones que pueden tardar mucho tiempo, como recuperar datos de un servicio Web. Si el código que se ejecuta en un subproceso en segundo plano necesita acceder a los elementos de la interfaz de usuario, debe ejecutar ese código en el subproceso principal.

La `Device` clase incluye los siguientes `static` métodos que se pueden usar para interactuar con los elementos de la interfaz de usuario de los subprocesos de fondo:

| Método | Argumentos | Valores devueltos | Propósito |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | Invoca un `Action` en el subproceso principal y no espera a que se complete. |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Invoca un elemento `Func<T>` en el subproceso principal y espera a que se complete. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Invoca un elemento `Action` en el subproceso principal y espera a que se complete. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Invoca un elemento `Func<Task<T>>` en el subproceso principal y espera a que se complete. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Invoca un elemento `Func<Task>` en el subproceso principal y espera a que se complete. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Devuelve el elemento `SynchronizationContext` para el subproceso principal. |

En el código siguiente se muestra un ejemplo del uso del `BeginInvokeOnMainThread` método:

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de dispositivo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [Ejemplo de estilos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [API del dispositivo](xref:Xamarin.Forms.Device)
