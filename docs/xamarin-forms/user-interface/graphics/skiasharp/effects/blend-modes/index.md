---
title: Modos de combinación de SkiaSharp
description: Use los modos de mezcla para definir lo que ocurre cuando los objetos gráficos se apilan entre sí.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CE1B222E-A2D0-4016-A532-EC1E59EE3D6B
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1c345edf4c9980497d1fcd877a9142819afa9b56
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368655"
---
# <a name="skiasharp-blend-modes"></a>Modos de combinación de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Estos artículos se centran en la [`BlendMode`](xref:SkiaSharp.SKPaint.BlendMode) propiedad de [`SKPaint`](xref:SkiaSharp.SKPaint) . La `BlendMode` propiedad es de tipo [`SKBlendMode`](xref:SkiaSharp.SKBlendMode) , una enumeración con 29 miembros.

La `BlendMode` propiedad determina lo que ocurre cuando un objeto gráfico (a menudo denominado _origen_ ) se representa encima de los objetos gráficos existentes (denominados _destino_ ). Normalmente, esperamos que el nuevo objeto gráfico oculte los objetos debajo de él. Pero esto solo sucede porque el modo de mezcla predeterminado es `SKBlendMode.SrcOver` , lo que significa que el origen se dibuja _sobre_ el destino. Los otros 28 miembros de `SKBlendMode` causan otros efectos. En la programación de gráficos, la técnica de combinar objetos gráficos de varias maneras se denomina _composición_.

## <a name="the-skblendmodes-enumeration"></a>La enumeración SKBlendModes

Los modos de mezcla de SkiaSharp se corresponden estrechamente con los descritos en la especificación del [**nivel 1 de composición y fusión**](https://www.w3.org/TR/compositing-1/) del W3C. La [**Introducción a Skia SkBlendMode**](https://skia.org/user/api/SkBlendMode_Overview) también proporciona información general útil. Para obtener una introducción general a los modos de fusión, el artículo [**modos de fusión**](https://en.wikipedia.org/wiki/Blend_modes) de Wikipedia es un buen comienzo. Los modos de mezcla se admiten en Adobe Photoshop, por lo que hay mucha información en línea adicional sobre los modos de fusión en ese contexto.

Los 29 miembros de la `SKBlendMode` enumeración se pueden dividir en tres categorías:

| Porter-Duff | Siendo    | No separable |
| ----------- | ------------ | ------------- |
| `Clear`     | `Modulate`   | `Hue`         |
| `Src`       | `Screen`     | `Saturation`  |
| `Dst`       | `Overlay`    | `Color`       |
| `SrcOver`   | `Darken`     | `Luminosity`  |
| `DstOver`   | `Lighten`    |               |
| `SrcIn`     | `ColorDodge` |               |
| `DstIn`     | `ColorBurn`  |               |
| `SrcOut`    | `HardLight`  |               |
| `DstOut`    | `SoftLight`  |               |
| `SrcATop`   | `Difference` |               |
| `DstATop`   | `Exclusion`  |               |
| `Xor`       | `Multiply`   |               |
| `Plus`      |              |               |

Los nombres de estas tres categorías tendrán un significado mayor en los debates siguientes. El orden en que se enumeran los miembros es el mismo que en la definición de la `SKBlendMode` enumeración. Los 13 miembros de enumeración de la primera columna tienen los valores enteros del 0 al 12. La segunda columna son miembros de la enumeración que corresponden a los enteros de la 13 a la 24, y los miembros de la tercera columna tienen valores de 25 a 28.

Estos modos de mezcla se describen _aproximadamente_ en el mismo orden en el documento de **nivel 1 de composición y mezcla** de W3C, pero hay algunas diferencias: el `Src` modo se denomina _copia_ en el documento W3C y `Plus` se llama _más claro_. En el documento W3C se define un modo de mezcla _normal_ que no se incluye en `SKBlendModes` porque sería igual que `SrcOver` . El `Modulate` modo de mezcla (en la parte superior de la primera columna) no se incluye en el documento de W3C y la explicación del `Multiply` modo precede a `Screen` .

Dado que el `Modulate` modo de mezcla es único para Skia, se tratará como un modo de Porter-Duff adicional y como un modo separable.

## <a name="the-importance-of-transparency"></a>La importancia de la transparencia

Históricamente, la composición se desarrolló junto con el concepto de _canal alfa_. En una superficie de presentación, como el `SKCanvas` objeto y un mapa de bits de color completo, cada píxel se compone de 4 bytes: 1 byte para los componentes rojo, verde y azul, y un byte adicional para la transparencia. Este componente alfa es 0 para una transparencia completa y 0xFF para una opacidad completa, con distintos niveles de transparencia entre esos valores.

Muchos de los modos de combinación se basan en la transparencia. Normalmente, cuando `SKCanvas` se obtiene por primera vez en un `PaintSurface` controlador o cuando `SKCanvas` se crea una para dibujar en un mapa de bits, el primer paso es esta llamada:

```csharp
canvas.Clear();
```

Este método reemplaza todos los píxeles del lienzo con píxeles negros transparentes, equivalentes a `new SKColor(0, 0, 0, 0)` o el entero 0x00000000. Todos los bytes de todos los píxeles se inicializan en cero.

Es posible que la superficie de dibujo de un `SKCanvas` que se obtiene en un `PaintSurface` controlador parezca tener un fondo blanco, pero esto solo se debe a que el `SKCanvasView` propio tiene un fondo transparente y la página tiene un fondo blanco. Puede demostrar este hecho si establece la Xamarin.Forms `BackgroundColor` propiedad de `SKCanvasView` en un Xamarin.Forms color:

```csharp
canvasView.BackgroundColor = Color.Red;
```

O bien, en una clase que se deriva de `ContentPage` , puede establecer el color de fondo de la página:

```csharp
BackgroundColor = Color.Red;
```

Verá este fondo rojo detrás de los gráficos de SkiaSharp porque el lienzo de SkiaSharp es transparente.

En el artículo [**SkiaSharp transparencia**](../../basics/transparency.md) se mostraron algunas técnicas básicas para usar la transparencia con el fin de organizar varios gráficos en una imagen compuesta. Los modos de mezcla van más allá de eso, pero la transparencia sigue siendo fundamental para los modos de mezcla.

## <a name="skiasharp-porter-duff-blend-modes"></a>[SkiaSharp Porter-Duff modos de fusión](porter-duff.md)

Use los modos Porter-Duff Blend para crear escenas basadas en imágenes de origen y de destino.

## <a name="skiasharp-separable-blend-modes"></a>[Modos de fusión separables de SkiaSharp](separable.md)

Use los modos de mezcla separables para modificar los colores rojo, verde y azul.

## <a name="skiasharp-non-separable-blend-modes"></a>[SkiaSharp modos de mezcla no separables](non-separable.md)

Use los modos de mezcla no separables para modificar el matiz, la saturación o la luminosidad.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)