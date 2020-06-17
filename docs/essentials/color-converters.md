---
title: "Xamarin.Essentials: Convertidores de colores" description: "La clase ColorConverters de Xamarin.Essentials ofrece varios métodos auxiliares y de extensión para trabajar con System.Drawing.Color."
ms.assetid: B10428D6-89E2-4714-A39F-7E6E626391B2 author: jamesmontemagno ms.author: jamont ms.date: 01/06/2020 ms.custom: video no-loc: [Xamarin.Forms, Xamarin.Essentials]
---

# <a name="xamarinessentials-color-converters"></a>Xamarin.Essentials: convertidores de color

La clase **ColorConverters** de Xamarin.Essentials ofrece varios métodos auxiliares para System.Drawing.Color.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-color-converters"></a>Uso de los convertidores de color

Agregue una referencia a Xamarin.Essentials en la clase:

```csharp
using Xamarin.Essentials;
```

Al trabajar con `System.Drawing.Color`, puede usar los convertidores integrados de Xamarin.Forms para crear un color a partir de Hsl, Hex o UInt.

```csharp
var blueHex = ColorConverters.FromHex("#3498db");
var blueHsl = ColorConverters.FromHsl(204, 70, 53);
var blueUInt = ColorConverters.FromUInt(3447003);
```

## <a name="using-color-extensions"></a>Uso de extensiones de color

Los método de extensión de `System.Drawing.Color` le permiten aplicar propiedades diferentes:

```csharp
var blue = ColorConverters.FromHex("#3498db");

// Multiplies the current alpha by 50%
var blueWithAlpha = blue.MultiplyAlpha(.5f);
```

Hay varios métodos de extensión, por ejemplo:

- GetComplementary
- MultiplyAlpha
- ToUInt
- WithAlpha
- WithHue
- WithLuminosity
- WithSaturation

## <a name="using-platform-extensions"></a>Uso de las extensiones de plataforma

Además, puede convertir System.Drawing.Color a la estructura de color específica de la plataforma. Estos métodos solo se pueden llamar desde proyectos de UWP, iOS y Android.

```csharp
var system = System.Drawing.Color.FromArgb(255, 52, 152, 219);

// Extension to convert to Android.Graphics.Color, UIKit.UIColor, or Windows.UI.Color
var platform = system.ToPlatformColor();
```

```csharp
var platform = new Android.Graphics.Color(52, 152, 219, 255);

// Back to System.Drawing.Color
var system = platform.ToSystemColor();
```

El método `ToSystemColor` se aplica a Android.Graphics.Color, UIKit.UIColor y Windows.UI.Color.

## <a name="api"></a>API

- [Código fuente de los convertidores de color](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [Documentación sobre la API de los convertidores de color](xref:Xamarin.Essentials.ColorConverters)
- [Color fuente de las extensiones de color](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [Documentación sobre la API de las extensiones de color](xref:Xamarin.Essentials.ColorExtensions)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Color-Converters-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
