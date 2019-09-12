---
title: Convertidores de color de Xamarin.Essentials
description: La clase ColorConverters de Xamarin.Essentials ofrece varios métodos auxiliares y de extensión para trabajar con System.Drawing.Color.
ms.assetid: B10428D6-89E2-4714-A39F-7E6E626391B2
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 5f26edf9515be79660574de0ae621daab3d1ea7d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756914"
---
# <a name="xamarinessentials-color-converters"></a>Xamarin.Essentials: convertidores de color

La clase **ColorConverters** de Xamarin.Essentials ofrece varios métodos auxiliares para System.Drawing.Color.

## <a name="get-started"></a>Primeros pasos

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-color-converters"></a>Uso de los convertidores de color

Agregue una referencia a Xamarin.Essentials en su clase:

```csharp
using Xamarin.Essentials;
```

Al trabajar con `System.Drawing.Color`, puede usar los convertidores integrados de Xamarin.Forms para crear un color a partir de Hsl, Hex o UInt.

```csharp
var blueHex = ColorConverters.FromHex("#3498db");
var blueHsl = ColorConverters.FromHsl(204, 70, 53);
var blueUInt = ColorConverers.FromUInt(3447003);
```

## <a name="using-color-extensions"></a>Uso de extensiones de color

Los método de extensión de `System.Drawing.Color` le permiten aplicar propiedades diferentes:

```csharp
var blue = ColorConverters.FromHex("#3498db");

// Multiplies the current alpha by 50%
var blueWithAlpha = blue.MultiplyAlpha(.5f);
```

Hay varios métodos de extensión, por ejemplo:

- ToUInt
- MultiplyAlpha
- WithHue
- WithAlpha
- WithSaturation
- WithLuminosity

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
