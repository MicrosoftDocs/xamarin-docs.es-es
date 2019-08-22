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
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888473"
---
# <a name="colors-in-xamarinforms"></a>Colores de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithcolors)

_Xamarin.Forms proporciona una clase de Color flexible y multiplataforma._

En este artículo se presentan las distintas [`Color`](xref:Xamarin.Forms.Color) formas en que se puede usar la clase en Xamarin. Forms.

La [`Color`](xref:Xamarin.Forms.Color) clase proporciona una serie de métodos para compilar una instancia de color:

- **Colores con nombre** -una colección de comunes colores con nombre, incluidos `Red`, `Green`, y `Blue`.
- **FromHex** : valor de cadena similar a la sintaxis usada en HTML, por ejemplo, "00FF00". Opcionalmente, se puede especificar alfa como el primer par de caracteres ("CC00FF00").
- **FromHsla** -matiz, saturación y luminosidad `double` valores, con el valor alfa opcional (0.0-1.0).
- **FromRgb** -rojo, verde y azul `int` valores (0-255).
- **FromRgba** -rojo, verde, azul y alfa `int` valores (0-255).
- **FromUint** -establecer una sola `double` que representa el valor **argb**.

Presentamos algunos colores del ejemplo, asignados a la `BackgroundColor` de algunas etiquetas utilizando diferentes variaciones de la sintaxis permitida:

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

Estos colores se muestran en cada plataforma a continuación. Tenga en cuenta el color final - `Accent` -es un color blue-ish para iOS y Android; este valor se define mediante Xamarin.Forms.

 [![Demostración de color](colors-images/colors-sml.png "Color demostración")](colors-images/colors.png#lightbox "demostración de Color")

## <a name="colordefault"></a>Color.Default

Use el `Default` para establecer un valor de color en el valor predeterminado de plataforma (Esto representa un color subyacente diferente en cada plataforma para cada propiedad de descripción) (o volver a establecer).

Los desarrolladores pueden usar este valor para establecer un `Color` propiedad pero no debería **no** consultar esta instancia para sus valores RGB de componente (que están todo listo en -1).

## <a name="colortransparent"></a>Color.Transparent

Establezca el color para borrar.

## <a name="coloraccent"></a>Color.Accent

En iOS y Android, esta instancia se establece en un color de contraste que está visible en el fondo predeterminado, pero no es el mismo que el color del texto de forma predeterminada.

## <a name="additional-methods"></a>Métodos adicionales

[`Color`](xref:Xamarin.Forms.Color)las instancias de incluyen los siguientes métodos adicionales:

- **AddLuminosity** : devuelve una `Color` modificando la luminosidad por el Delta proporcionado.
- **MultiplyAlpha** : devuelve una `Color` modificando el alfa, multiplicándolo por el valor alfa proporcionado.
- **ToHex** : devuelve una representación `string` hexadecimal de un `Color`.
- **WithHue** : devuelve un `Color`, reemplazando el matiz por el valor proporcionado.
- **WithLuminosity** : devuelve un `Color`, reemplazando la luminosidad por el valor proporcionado.
- **WithSaturation** : devuelve un `Color`, reemplazando la saturación por el valor proporcionado.

## <a name="implicit-conversions"></a>Conversiones implícitas

Conversión implícita entre el `Xamarin.Forms.Color` y `System.Drawing.Color` tipos se pueden realizar:

```csharp
Xamarin.Forms.Color xfColor = Xamarin.Forms.Color.FromRgb(0, 72, 255);
System.Drawing.Color sdColor = System.Drawing.Color.FromArgb(38, 127, 0);

// Implicity convert from a Xamarin.Forms.Color to a System.Drawing.Color
System.Drawing.Color sdColor2 = xfColor;

// Implicitly convert from a System.Drawing.Color to a Xamarin.Forms.Color
Xamarin.Forms.Color xfColor2 = sdColor;
```

## <a name="deviceruntimeplatform"></a>Device.RuntimePlatform

Este fragmento de código usa el `Device.RuntimePlatform` propiedad para establecer el color de un `ActivityIndicator`:

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
- [Selector de enlazable (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
