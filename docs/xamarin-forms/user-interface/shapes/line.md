---
title: 'Xamarin.FormsFormas: línea'
description: La Xamarin.Forms clase line se puede usar para dibujar líneas.
ms.prod: xamarin
ms.assetid: 384F1A72-6D3B-4FD3-BC40-E00A73A463EC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/20/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a5d130922a9bd8f30b33b99f7f3dc512f056269f
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918633"
---
# <a name="no-locxamarinforms-shapes-line"></a>Xamarin.FormsFormas: línea

![API de versión preliminar](~/media/shared/preview.png)

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Line` clase se deriva de la `Shape` clase y se puede usar para dibujar líneas. Para obtener información sobre las propiedades que la `Line` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

`Line` define las siguientes propiedades:

- `X1`, de tipo Double, indica la coordenada x del punto inicial de la línea. El valor predeterminado de esta propiedad es 0,0.
- `Y1`, de tipo Double, indica la coordenada y del punto inicial de la línea. El valor predeterminado de esta propiedad es 0,0.
- `X2`, de tipo Double, indica la coordenada x del punto final de la línea. El valor predeterminado de esta propiedad es 0,0.
- `Y2`, de tipo Double, indica la coordenada y del punto final de la línea. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Para obtener información acerca de cómo controlar el modo en que se dibujan los extremos de línea, vea [extremos de línea de control](index.md#control-line-ends).

## <a name="create-a-line"></a>Crear una línea

Para dibujar una línea, cree un `Line` objeto y establezca sus `X1` `Y1` propiedades y en su punto inicial, y sus `X2` `Y` propiedades y en su punto final. Además, establezca su `Stroke` propiedad en [`Color`](xref:Xamarin.Forms.Color) porque una línea sin trazo no es visible.

> [!NOTE]
> Establecer la `Fill` propiedad de `Line` no tiene ningún efecto, ya que una línea no tiene ningún interior.

En el siguiente ejemplo de XAML se muestra cómo dibujar una línea:

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="Red"
      StrokeThickness="1" />
```

En este ejemplo, se dibuja una línea diagonal roja desde (40, 0) a (0120):

![Line](line-images/line.png "Línea")

Dado que `X1` las `Y1` propiedades,, `X2` y `Y2` tienen valores predeterminados de 0, es posible dibujar algunas líneas con la sintaxis mínima:

```xaml
<Line Stroke="Red"
      StrokeThickness="1"
      X2="200" />
```

En este ejemplo, se define una línea horizontal que tiene una longitud de 200 unidades independientes del dispositivo. Dado que las otras propiedades son 0 de forma predeterminada, se dibuja una línea de (0,0) a (200, 0).

En el siguiente ejemplo de XAML se muestra cómo dibujar una línea discontinua:

```xaml
<Line X1="40"
      Y1="0"
      X2="0"
      Y2="120"
      Stroke="DarkBlue"
      StrokeThickness="1"
      StrokeDashArray="1,1"
      StrokeDashOffset="6" />
```

En este ejemplo, se dibuja una línea diagonal discontinua azul oscura de (40, 0) a (0120):

![Línea discontinua](line-images/dashed-line.png "Línea discontinua")

Para obtener más información sobre cómo dibujar una línea discontinua, vea [dibujar formas discontinuas](index.md#draw-dashed-shapes).

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsColocar](index.md)
