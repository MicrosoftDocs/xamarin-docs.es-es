---
title: Colores de Xamarin.Forms
description: Xamarin.Forms proporciona una clase de Color flexible y multiplataforma. En este artículo se explica la funcionalidad proporcionada por la clase de Color y cómo usarlo.
ms.prod: xamarin
ms.assetid: 22288ABF-57BE-47A9-ACC3-AC604D787C46
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2019
ms.openlocfilehash: 2a17b037803d1ca6e54000ea7ba3f05c8ce6034f
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305760"
---
# <a name="colors-in-xamarinforms"></a>Colores de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_Xamarin. Forms proporciona una clase de color flexible multiplataforma._

En este artículo se presentan las distintas formas en que se puede usar la clase [`Color`](xref:Xamarin.Forms.Color) en Xamarin. Forms.

La clase [`Color`](xref:Xamarin.Forms.Color) proporciona varios métodos para compilar una instancia de color:

- **Colores con nombre** : una colección de colores con nombre comunes, incluidos `Red`, `Green`y `Blue`.
- **FromHex** : valor de cadena similar a la sintaxis usada en HTML, por ejemplo, "00FF00". Opcionalmente, se puede especificar alfa como el primer par de caracteres ("CC00FF00").
- **FromHsla** : matiz, saturación y luminosidad `double` valores, con valor alfa opcional (0,0-1,0).
- **FromRgb** : valores de `int` rojo, verde y azul (0-255).
- **FromRgba** : valores de `int` rojo, verde, azul y alfa (0-255).
- **FromUint** : establece un valor de `double` único que representa **ARGB**.

Estos son algunos ejemplos de colores, asignados al `BackgroundColor` de algunas etiquetas con diferentes variaciones de la sintaxis permitida:

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

Estos colores se muestran en cada plataforma a continuación. Observe el `Accent` de color final: es un color azul ish para iOS y Android. Xamarin. Forms define este valor.

 [![Demo de color](colors-images/colors-sml.png "Demo de color")](colors-images/colors.png#lightbox "Demo de color")

## <a name="colordefault"></a>Color.Default

Use el `Default` para establecer (o volver a establecer) un valor de color de nuevo en el valor predeterminado de la plataforma (sabiendo que esto representa un color subyacente diferente en cada plataforma para cada propiedad).

Los desarrolladores pueden usar este valor para establecer una propiedad `Color` pero **no** deben consultar a esta instancia los valores RGB de sus componentes (todos están establecidos en-1).

## <a name="colortransparent"></a>Color.Transparent

Establezca el color para borrar.

## <a name="coloraccent"></a>Color.Accent

En iOS y Android, esta instancia se establece en un color de contraste que está visible en el fondo predeterminado, pero no es el mismo que el color del texto de forma predeterminada.

## <a name="additional-methods"></a>Métodos adicionales

las instancias de [`Color`](xref:Xamarin.Forms.Color) incluyen los siguientes métodos adicionales:

- **AddLuminosity** : devuelve un `Color` modificando la luminosidad por el Delta proporcionado.
- **MultiplyAlpha** : devuelve un `Color` modificando el alfa, multiplicándolo por el valor alfa proporcionado.
- **ToHex** : devuelve una representación `string` hexadecimal de un `Color`.
- **WithHue** : devuelve un `Color`, reemplazando el matiz por el valor proporcionado.
- **WithLuminosity** : devuelve un `Color`, reemplazando la luminosidad por el valor proporcionado.
- **WithSaturation** : devuelve un `Color`, reemplazando la saturación por el valor proporcionado.

## <a name="implicit-conversions"></a>Conversiones implícitas

Se puede realizar una conversión implícita entre los tipos `Xamarin.Forms.Color` y `System.Drawing.Color`:

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Device.RuntimePlatform

Este fragmento de código usa la propiedad `Device.RuntimePlatform` para establecer de forma selectiva el color de un `ActivityIndicator`:

```csharp
ActivityIndicator activityIndicator = new ActivityIndicator
{
    Color = Device.RuntimePlatform == Device.iOS ? Color.Black : Color.Default,
    IsRunning = true
};
```

## <a name="using-from-xaml"></a>Uso de XAML

También se puede hacer referencia a los colores en XAML mediante los nombres de colores definidos o las representaciones hexadecimales que se muestran aquí:

```xaml
<Label Text="Sea color" BackgroundColor="Aqua" />
<Label Text="RGB" BackgroundColor="#00FF00" />
<Label Text="Alpha plus RGB" BackgroundColor="#CC00FF00" />
<Label Text="Tiny RGB" BackgroundColor="#0F0" />
<Label Text="Tiny Alpha plus RGB" BackgroundColor="#C0F0" />
```

> [!NOTE]
> Cuando se usa la compilación de XAML, los nombres de colores distinguen mayúsculas de minúsculas y por lo tanto, pueden escribirse en minúsculas. Para obtener más información acerca de la compilación XAML, consulte [XAML Compilation](~/xamarin-forms/xaml/xamlc.md) (Compilación XAML).

## <a name="related-links"></a>Vínculos relacionados

- [ColorsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)
- [Selector enlazable (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
