---
title: Modos de fusión de SkiaSharp
description: Uso de blend modos de definir lo que ocurre cuando se apilan objetos gráficos entre sí.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: CE1B222E-A2D0-4016-A532-EC1E59EE3D6B
author: davidbritch
ms.author: dabritch
ms.date: 08/23/2018
ms.openlocfilehash: 8071f310e899575699e1d0b925541f2863b00676
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68645187"
---
# <a name="skiasharp-blend-modes"></a>Modos de fusión de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Estos artículos se centran en la [ `BlendMode` ](xref:SkiaSharp.SKPaint.BlendMode) propiedad de [ `SKPaint` ](xref:SkiaSharp.SKPaint). El `BlendMode` propiedad es de tipo [ `SKBlendMode` ](xref:SkiaSharp.SKBlendMode), una enumeración con miembros de 29.

El `BlendMode` propiedad determina qué ocurre cuando un objeto gráfico (a menudo denominada el _origen_) se representa por encima de los objetos de gráficos existentes (llamado el _destino_). Normalmente, se espera que el nuevo objeto gráfico para ocultar los objetos debajo de ella. Pero eso ocurre sólo porque es el modo de mezcla predeterminada `SKBlendMode.SrcOver`, lo que significa que el origen se dibuja _sobre_ el destino. Los demás miembros de 28 `SKBlendMode` provocar otros efectos. En la programación de gráficos, la técnica de combinar objetos gráficos de varias maneras se conoce como _composición_.

## <a name="the-skblendmodes-enumeration"></a>La enumeración SKBlendModes

Los modos de mezcla de SkiaSharp estrechamente corresponden a las descritas en el W3C [ **la composición y mezcla de nivel 1** ](https://www.w3.org/TR/compositing-1/) especificación. El Skia [ **SkBlendMode referencia** ](https://skia.org/user/api/SkBlendMode_Reference) también proporciona información general útil. Para obtener una introducción general a los modos de mezcla, la [ **modos de fusión** ](https://en.wikipedia.org/wiki/Blend_modes) artículo de Wikipedia es un buen punto de partida. Se admiten los modos de fusión de Adobe Photoshop, así que no hay tanta en línea información adicional acerca de los modos de blend en ese contexto.

Los miembros de 29 el `SKBlendMode` enumeración puede dividirse en tres categorías:

| Porter Duff | Separables    | No separables |
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

Los nombres de estas tres categorías tardará más significado en las discusiones que siguen. El orden que se enumeran los miembros aquí es igual que en la definición de la `SKBlendMode` enumeración. Los miembros de enumeración de 13 en la primera columna tienen los valores enteros del 0 al 12. La segunda columna son miembros de enumeración que se corresponden con números enteros de 13 a 24, y los miembros de la tercera columna tienen valores de 25 a 28.

Estos modos de mezcla se describen _aproximadamente_ en el mismo orden en el documento de **nivel 1 de composición y combinación** de W3C, pero hay algunas diferencias: El `Src` modo se denomina _copia_ en el documento W3C y `Plus` se llama _más claro_. El documento del W3C define un _Normal_ modo blend que no se incluye en `SKBlendModes` porque se quedaría igual `SrcOver`. El `Modulate` modo blend (en la parte superior de la primera columna) no se incluye en el documento del W3C y explicación de la `Multiply` precede modo `Screen`.

Dado que el `Modulate` modo blend es único en Skia, se tratará como un modo Porter Duff adicional, como un modo separable.

## <a name="the-importance-of-transparency"></a>La importancia de transparencia

Históricamente, la composición fue desarrollada en combinación con el concepto de la _canal alfa_. En una superficie de presentación como el `SKCanvas` objeto y un mapa de bits de color completo, cada píxel se compone de 4 bytes: 1 byte para los componentes rojo, verde y azul, y un byte adicional para la transparencia. Este componente alfa es 0 para una transparencia completa y 0xFF para opacidad completa, con distintos niveles de transparencia entre esos valores.

Muchos de los modos de mezcla se basan en transparencia. Normalmente, cuando un `SKCanvas` primero se obtiene en un `PaintSurface` controlador, o cuando un `SKCanvas` se crea para dibujar en un mapa de bits, el primer paso es esta llamada:

```csharp
canvas.Clear();
```

Este método reemplaza todos los píxeles del lienzo con los píxeles negros transparentes, equivalente a `new SKColor(0, 0, 0, 0)` o el entero de 0 x 00000000. Todos los bytes de todos los píxeles se inicializan en cero.

La superficie de dibujo de un `SKCanvas` que se obtiene en un `PaintSurface` puede parecer que el controlador tiene un fondo blanco, pero no sólo porque la `SKCanvasView` sí tiene un fondo transparente, y la página tiene un fondo blanco. Puede mostrar este hecho para usted mismo estableciendo Xamarin.Forms `BackgroundColor` propiedad de `SKCanvasView` a un color de Xamarin.Forms:

```csharp
canvasView.BackgroundColor = Color.Red;
```

O bien, en una clase que derive de `ContentPage`, puede establecer el color de fondo de página:

```csharp
BackgroundColor = Color.Red;
```

Verá que este fondo rojo detrás de los gráficos de SkiaSharp debido a la SkiaSharp propio lienzo es transparente.

El artículo [ **SkiaSharp transparencia** ](../../basics/transparency.md) mostramos algunas técnicas básicas de uso de transparencia para organizar varios gráficos en una imagen compuesta. Los modos de mezcla ir más allá de eso, pero transparencia permanece fundamental para los modos de mezcla. 

## <a name="skiasharp-porter-duff-blend-modesporter-duffmd"></a>[Modos de fusión de SkiaSharp Porter-Duff](porter-duff.md)

Use los modos de mezcla Porter Duff para redactar escenas basadas en imágenes de origen y destino.

## <a name="skiasharp-separable-blend-modesseparablemd"></a>[Modos de fusión separables de SkiaSharp](separable.md)

Utilizar los modos de mezcla separables para modificar los colores rojos, verde y azules.

## <a name="skiasharp-non-separable-blend-modesnon-separablemd"></a>[Modos de mezcla no separables de SkiaSharp](non-separable.md)

Utilizar los modos de mezcla no separables para alterar el matiz, saturación o luminosidad.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
