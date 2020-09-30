---
title: Colores en Xamarin.Forms
description: Xamarin.Forms proporciona una clase de color flexible multiplataforma. En este artículo se explica la funcionalidad proporcionada por la clase color y cómo utilizarla.
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6b4e44cb667335ecca7a0c7e5ec77d6e14ed975c
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91557236"
---
# <a name="colors-in-no-locxamarinforms"></a>Colores en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_Xamarin.Forms proporciona una clase de color flexible multiplataforma._

En este artículo se presentan las distintas formas [`Color`](xref:Xamarin.Forms.Color) en que se puede usar la clase en Xamarin.Forms .

La [`Color`](xref:Xamarin.Forms.Color) clase proporciona una serie de métodos para compilar una `Color` instancia de:

- **Colores con nombre** : una colección de colores con nombre comunes, incluidos `Red` , `Green` y `Blue` .
- `FromHex` -valor de cadena similar a la sintaxis usada en HTML, por ejemplo "00FF00". Opcionalmente, se puede especificar alfa como el primer par de caracteres ("CC00FF00").
- `FromHsla` -Valores de matiz, saturación y luminosidad `double` , con valor alfa opcional (0,0-1,0).
- `FromHsv` : Matiz, saturación, valor `int` o `double` valores.
- `FromHsva` : Matiz, saturación, valor `int` o `double` valores.
- `FromRgb` -Valores rojo, verde y azul `int` (0-255).
- `FromRgba` -Valores rojo, verde, azul y alfa  `int` (0-255).
- `FromUint` : establezca un `double` valor único que represente **ARGB**.

A continuación se indican algunos colores de ejemplo asignados a la `BackgroundColor` de algunas etiquetas con diferentes variaciones de la sintaxis permitida:

```csharp
var red    = new Label { Text = "Red",   BackgroundColor = Color.Red };
var orange = new Label { Text = "Orange",BackgroundColor = Color.FromHex("FF6A00") };
var yellow = new Label { Text = "Yellow",BackgroundColor = Color.FromHsla(0.167, 1.0, 0.5, 1.0) };
var green  = new Label { Text = "Green", BackgroundColor = Color.FromRgb (38, 127, 0) };
var blue   = new Label { Text = "Blue",  BackgroundColor = Color.FromRgba(0, 38, 255, 255) };
var indigo = new Label { Text = "Indigo",BackgroundColor = Color.FromRgb (0, 72, 255) };
var violet = new Label { Text = "Violet",BackgroundColor = Color.FromHsla(0.82, 1, 0.25, 1) };

var transparent = new Label { Text = "Transparent",BackgroundColor = Color.Transparent };
var @default = new Label    { Text = "Default",    BackgroundColor = Color.Default };
var accent = new Label      { Text = "Accent",     BackgroundColor = Color.Accent };
```

Estos colores se muestran en cada plataforma siguiente. Observe el color final: `Accent` es un color azul ish para iOS y Android; este valor lo define Xamarin.Forms .

 [![Demo de color](colors-images/colors-sml.png "Demo de color")](colors-images/colors.png#lightbox "Demo de color")

## <a name="colordefault"></a>Color. default

Utilice `Default` para establecer (o volver a establecer) un valor de color de nuevo en el valor predeterminado de la plataforma (sabiendo que esto representa un color subyacente diferente en cada plataforma para cada propiedad).

Los desarrolladores pueden usar este valor para establecer una `Color` propiedad, pero **no** deben consultar a esta instancia los valores RGB de sus componentes (todos están establecidos en-1).

## <a name="colortransparent"></a>Color. Transparent

Establezca el color en borrar.

## <a name="coloraccent"></a>Color. acento

En iOS y Android, esta instancia se establece en un color de contraste que está visible en el fondo predeterminado pero no es el mismo que el color de texto predeterminado.

## <a name="additional-methods"></a>Métodos adicionales

[`Color`](xref:Xamarin.Forms.Color) las instancias de incluyen los siguientes métodos adicionales:

- `AddLuminosity` : devuelve un `Color` modificador la luminosidad por el Delta proporcionado.
- `MultiplyAlpha` : devuelve una `Color` modificando el alfa, multiplicándolo por el valor alfa proporcionado.
- `ToHex` : devuelve una `string` representación hexadecimal de un `Color` .
- `WithHue` : devuelve `Color` , reemplazando el matiz por el valor proporcionado.
- `WithLuminosity` : devuelve `Color` , reemplazando la luminosidad por el valor proporcionado.
- `WithSaturation` : devuelve `Color` , reemplazando la saturación por el valor proporcionado.

## <a name="implicit-conversions"></a>Conversiones implícitas

Se puede realizar la conversión implícita entre los `Xamarin.Forms.Color` `System.Drawing.Color` tipos y:

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Device. RuntimePlatform

Este fragmento de código usa la `Device.RuntimePlatform` propiedad para establecer de forma selectiva el color de un `ActivityIndicator` :

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="use-from-xaml"></a>Usar desde XAML

También se puede hacer referencia a los colores en XAML mediante los nombres de colores definidos o las representaciones hexadecimales que se muestran aquí:

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> Cuando se usa la compilación XAML, los nombres de color no distinguen mayúsculas de minúsculas y, por tanto, pueden escribirse en minúsculas. Para obtener más información acerca de la compilación XAML, consulte [XAML Compilation](~/xamarin-forms/xaml/xamlc.md) (Compilación XAML).

## <a name="related-links"></a>Vínculos relacionados

- [ColorsSample](/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [Selector enlazable (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)