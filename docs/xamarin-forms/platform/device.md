---
title: Clase de dispositivo de Xamarin. Forms
description: En este artículo se explica cómo usar la clase de dispositivo de Xamarin. Forms para tener un mayor control sobre la funcionalidad y los diseños en cada plataforma.
ms.prod: xamarin
ms.assetid: 2F304AEC-8612-4833-81E5-B2F3F469B2DF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/12/2019
ms.openlocfilehash: 25ddbea75d0fd6858f848499281da5d5f0b68171
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697199"
---
# <a name="xamarinforms-device-class"></a>Clase de dispositivo de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)

La clase [`Device`](xref:Xamarin.Forms.Device) contiene una serie de propiedades y métodos para ayudar a los desarrolladores a personalizar el diseño y la funcionalidad para cada plataforma.

Además de los métodos y propiedades para el código de destino en tipos y tamaños de hardware específicos, la clase `Device` incluye métodos que se pueden usar para interactuar con los controles de interfaz de usuario de los subprocesos en segundo plano. Para obtener más información, vea [interactuar con la interfaz de usuario de subprocesos en segundo plano](#interact-with-the-ui-from-background-threads).

## <a name="providing-platform-specific-values"></a>Proporcionar valores específicos de la plataforma

Antes de Xamarin. Forms 2.3.4, la plataforma en la que se ejecutaba la aplicación se podía obtener examinando la propiedad [`Device.OS`](xref:Xamarin.Forms.Device.OS) y comparándola con la enumeración [`TargetPlatform.iOS`](xref:Xamarin.Forms.TargetPlatform.iOS), [`TargetPlatform.Android`](xref:Xamarin.Forms.TargetPlatform.Android), [`TargetPlatform.WinPhone`](xref:Xamarin.Forms.TargetPlatform.WinPhone)y [`TargetPlatform.Windows`](xref:Xamarin.Forms.TargetPlatform.Windows) valor. De forma similar, una de las sobrecargas de [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) podría usarse para proporcionar valores específicos de la plataforma a un control.

Sin embargo, como Xamarin. Forms 2.3.4, estas API han quedado en desuso y se han reemplazado. La clase [`Device`](xref:Xamarin.Forms.Device) contiene ahora constantes de cadena públicas que identifican las plataformas: [`Device.iOS`](xref:Xamarin.Forms.Device.iOS), [`Device.Android`](xref:Xamarin.Forms.Device.Android), `Device.WinPhone` (en desuso), `Device.WinRT` (en desuso), [`Device.UWP`](xref:Xamarin.Forms.Device.UWP)y [1](xref:Xamarin.Forms.Device.macOS). Del mismo modo, las sobrecargas de [`Device.OnPlatform`](xref:Xamarin.Forms.Device.OnPlatform(System.Action,System.Action,System.Action,System.Action)) se han reemplazado por las API [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) y [`On`](xref:Xamarin.Forms.On) .

En C#, se pueden proporcionar valores específicos de la plataforma mediante la creación de una instrucción `switch` en la propiedad [`Device.RuntimePlatform`](xref:Xamarin.Forms.Device.RuntimePlatform) y, a continuación, proporcionar instrucciones `case` para las plataformas necesarias:

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

Las clases [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) y [`On`](xref:Xamarin.Forms.On) proporcionan la misma funcionalidad en XAML:

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

La clase [`OnPlatform`](xref:Xamarin.Forms.OnPlatform`1) es una clase genérica en la que se deben crear instancias con un `x:TypeArguments` atributo que coincida con el tipo de destino. En la clase [`On`](xref:Xamarin.Forms.On) , el atributo [`Platform`](xref:Xamarin.Forms.On.Platform) puede aceptar un solo valor de `string` o varios valores de `string` delimitados por comas.

> [!IMPORTANT]
> Proporcionar un valor de atributo de `Platform` incorrecto en la clase `On` no producirá un error. En su lugar, se ejecutará el código sin que se aplique el valor específico de la plataforma.

Como alternativa, se puede usar el `OnPlatform` extensión de marcado en XAML para personalizar la apariencia de la interfaz de usuario por plataforma. Para obtener más información, consulte [extensión de marcado](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform)en la plataforma.

## <a name="deviceidiom"></a>Dispositivo. expresión

La propiedad `Device.Idiom` se puede usar para modificar los diseños o la funcionalidad según el dispositivo en el que se ejecuta la aplicación. La enumeración [`TargetIdiom`](xref:Xamarin.Forms.TargetIdiom) contiene los siguientes valores:

- **Teléfono** : dispositivos iPhone, iPod Touch y Android más estrechos que 600 DIP ^
- **Tableta** : iPad, dispositivos Windows y dispositivos Android más anchos que 600 DIP ^
- **Escritorio** : solo se devuelve en [aplicaciones para UWP](~/xamarin-forms/platform/windows/installation/index.md) en equipos de escritorio de windows 10 (devuelve `Phone` en dispositivos móviles de Windows, incluidos los escenarios de continuum).
- **TV** : dispositivos de TV Tizen
- **Inspección** : Tizen Watch Devices
- **No compatible** : sin usar

*^ DIP no es necesariamente el número de píxeles físicos*

La propiedad `Idiom` es especialmente útil para la creación de diseños que aprovechan pantallas más grandes, como esto:

```csharp
if (Device.Idiom == TargetIdiom.Phone) {
    // layout views vertically
} else {
    // layout views horizontally for a larger display (tablet or desktop)
}
```

La clase [`OnIdiom`](xref:Xamarin.Forms.OnIdiom`1) proporciona la misma funcionalidad en XAML:

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

La clase [`OnIdiom`](xref:Xamarin.Forms.OnPlatform`1) es una clase genérica en la que se deben crear instancias con un `x:TypeArguments` atributo que coincida con el tipo de destino.

Como alternativa, la extensión de marcado de `OnIdiom` se puede usar en XAML para personalizar la apariencia de la interfaz de usuario en función de la expresión del dispositivo en el que se ejecuta la aplicación. Para obtener más información, vea [extensión de marcado en idioma](~/xamarin-forms/xaml/markup-extensions/consuming.md#onidiom).

## <a name="deviceflowdirection"></a>Device. FlowDirection

El valor [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) recupera un valor de enumeración [`FlowDirection`](xref:Xamarin.Forms.FlowDirection) que representa la dirección de flujo actual utilizada por el dispositivo. La dirección de flujo es la dirección en la que el ojo humano lee los elementos de la interfaz de usuario en la página. Los valores de la enumeración son:

- [`LeftToRight`](xref:Xamarin.Forms.FlowDirection.LeftToRight)
- [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft)
- [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)

En XAML, el valor [`Device.FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) se puede recuperar mediante la extensión de marcado `x:Static`:

```xaml
<ContentPage ... FlowDirection="{x:Static Device.FlowDirection}"> />
```

El código equivalente en C# es:

```csharp
this.FlowDirection = Device.FlowDirection;
```

Para obtener más información acerca de la dirección de flujo, consulte [localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="devicestyles"></a>Device. Styles

La [propiedad `Styles`](~/xamarin-forms/user-interface/styles/index.md) contiene definiciones de estilo integradas que se pueden aplicar a algunos controles (como `Label`) `Style` propiedad. Los estilos disponibles son:

- BodyStyle
- CaptionStyle
- ListItemDetailTextStyle
- ListItemTextStyle
- SubtitleStyle
- TitleStyle

## <a name="devicegetnamedsize"></a>Device. GetNamedSize

`GetNamedSize` se puede usar al establecer [`FontSize`](~/xamarin-forms/user-interface/text/fonts.md) en C# el código:

```csharp
myLabel.FontSize = Device.GetNamedSize (NamedSize.Small, myLabel);
someLabel.FontSize = Device.OnPlatform (
      24,         // hardcoded size
      Device.GetNamedSize (NamedSize.Medium, someLabel),
      Device.GetNamedSize (NamedSize.Large, someLabel)
);
```

## <a name="devicestarttimer"></a>Device. StartTimer

La clase `Device` también tiene un método `StartTimer` que proporciona una manera sencilla de desencadenar tareas dependientes del tiempo que funcionan en el código común de Xamarin. Forms, incluida una biblioteca de .NET Standard. Pase un `TimeSpan` para establecer el intervalo y devuelva `true` para mantener el temporizador en ejecución o `false` para detenerlo después de la invocación actual.

```csharp
Device.StartTimer (new TimeSpan (0, 0, 60), () =>
{
    // do something every 60 seconds
    return true; // runs again, or false to stop
});
```

Si el código dentro del temporizador interactúa con la interfaz de usuario (por ejemplo, estableciendo el texto de una `Label` o mostrando una alerta), debe realizarse dentro de una expresión de `BeginInvokeOnMainThread` (consulte a continuación).

> [!NOTE]
> Las clases `System.Timers.Timer` y `System.Threading.Timer` son .NET Standard alternativas al uso del método `Device.StartTimer`.

## <a name="interact-with-the-ui-from-background-threads"></a>Interacción con la interfaz de usuario desde subprocesos en segundo plano

La mayoría de los sistemas operativos, incluidos iOS, Android y el Plataforma universal de Windows, utilizan un modelo de subprocesamiento único para el código que implica la interfaz de usuario. Este subproceso se llama a menudo el *subproceso principal* o el *subproceso*de la interfaz de usuario. Una consecuencia de este modelo es que todo el código que tiene acceso a los elementos de la interfaz de usuario se debe ejecutar en el subproceso principal de la aplicación.

A veces, las aplicaciones utilizan subprocesos en segundo plano para realizar operaciones que pueden tardar mucho tiempo, como recuperar datos de un servicio Web. Si el código que se ejecuta en un subproceso en segundo plano necesita acceder a los elementos de la interfaz de usuario, debe ejecutar ese código en el subproceso principal.

La clase `Device` incluye los siguientes métodos de `static` que se pueden usar para interactuar con los elementos de la interfaz de usuario de los subprocesos de fondo:

| Método | Argumentos | Valores devueltos | Finalidad |
|---|---|---|---|
| `BeginInvokeOnMainThread` | `Action` | `void` | Invoca un `Action` en el subproceso principal y no espera a que se complete. |
| `InvokeOnMainThreadAsync<T>` | `Func<T>` | `Task<T>` | Invoca un elemento `Func<T>` en el subproceso principal y espera a que se complete. |
| `InvokeOnMainThreadAsync` | `Action` | `Task` | Invoca un elemento `Action` en el subproceso principal y espera a que se complete. |
| `InvokeOnMainThreadAsync<T>`| `Func<Task<T>>` | `Task<T>` | Invoca un elemento `Func<Task<T>>` en el subproceso principal y espera a que se complete. |
| `InvokeOnMainThreadAsync` | `Func<Task>` | `Task` | Invoca un elemento `Func<Task>` en el subproceso principal y espera a que se complete. |
| `GetMainThreadSynchronizationContextAsync` | | `Task<SynchronizationContext>` | Devuelve el elemento `SynchronizationContext` para el subproceso principal. |

En el código siguiente se muestra un ejemplo del uso del método `BeginInvokeOnMainThread`:

```csharp
Device.BeginInvokeOnMainThread (() =>
{
    // interact with UI elements
});
```

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de dispositivo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithdevice)
- [Ejemplo de estilos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithstyles)
- [Dispositivo](xref:Xamarin.Forms.Device)
