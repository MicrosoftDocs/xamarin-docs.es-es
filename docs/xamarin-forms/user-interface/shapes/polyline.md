---
title: 'Xamarin.FormsFormas: Polyline'
description: La Xamarin.Forms clase Polyline se puede usar para dibujar una serie de líneas rectas conectadas.
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5b7e6404dec40814b800aef696afd058cafb69e5
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918364"
---
# <a name="no-locxamarinforms-shapes-polyline"></a>Xamarin.FormsFormas: Polyline

![API de versión preliminar](~/media/shared/preview.png)

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Polyline` clase se deriva de la `Shape` clase y se puede usar para dibujar una serie de líneas rectas conectadas. Una polilínea es similar a un polígono, excepto que el último punto de una polilínea no está conectado al primer punto. Para obtener información sobre las propiedades que la `Polyline` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

`Polyline` define las siguientes propiedades:

- `Points`, de tipo `PointCollection` , que es una colección de `Point` estructuras que describen los puntos de vértice de la polilínea.
- `FillRule`, de tipo `FillRule` , que especifica cómo se combinan las áreas de intersección de la polilínea. El valor predeterminado de esta propiedad es `FillRule.EvenOdd`.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

El `PointsCollection` tipo es `ObservableCollection` de [`Point`](xref:Xamarin.Forms.Point) objetos. La `Point` estructura define `X` `Y` las propiedades y, de tipo `double` , que representan un par de coordenadas x e y en el espacio 2D. Por lo tanto, la `Points` propiedad debe establecerse en una lista de pares de coordenada x y de coordenada y que describan los puntos de vértice de la polilínea, delimitados por una sola coma y/o uno o varios espacios. Por ejemplo, "40, 10 70, 80" y "40 10, 70 80" son válidos.

Para obtener más información acerca de la `FillRule` enumeración, consulte [ Xamarin.Forms formas: rellenar reglas](fillrules.md).

## <a name="create-a-polyline"></a>Crear una polilínea

Para dibujar una polilínea, cree un `Polyline` objeto y establezca su `Points` propiedad en los vértices de una forma. Para dar a la polilínea un contorno, establezca su `Stroke` propiedad en [`Color`](xref:Xamarin.Forms.Color) . La `StrokeThickness` propiedad especifica el grosor del contorno de polilínea.

> [!IMPORTANT]
> Si establece la `Fill` propiedad de un `Polyline` en [`Color`](xref:Xamarin.Forms.Color) , se pinta el espacio interior de la polilínea, incluso si el punto inicial y el final no forman una intersección.

En el siguiente ejemplo de XAML se muestra cómo dibujar una polilínea:

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          StrokeThickness="1" />
```

En este ejemplo, se dibuja una polilínea roja:

![Polilínea](polyline-images/stroke.png "Polilínea")

En el siguiente ejemplo de XAML se muestra cómo dibujar una polilínea discontinua:

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red"
          StrokeThickness="2"
          StrokeDashArray="1,1"
          StrokeDashOffset="6" />
```

En este ejemplo, la polilínea es discontinua:

![Polilínea discontinua](polyline-images/dashed.png "Polilínea discontinua")

Para obtener más información sobre cómo dibujar una polilínea discontinua, vea [dibujar formas con guiones](index.md#draw-dashed-shapes).

En el siguiente ejemplo de XAML se muestra una polilínea que utiliza la regla de relleno predeterminada:

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Blue"
          Stroke="Red"
          StrokeThickness="3" />
```

En este ejemplo, el comportamiento de relleno de la polilínea se determina mediante la `EvenOdd` regla de relleno.

![Polyline de EvenOdd](polyline-images/evenodd.png "EvenOdd polyine")

En el siguiente ejemplo de XAML se muestra una polilínea que utiliza la `Nonzero` regla de relleno:

```xaml
<Polyline Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
          Fill="Black"
          FillRule="Nonzero"
          Stroke="Yellow"
          StrokeThickness="3" />
```

![Polyline distinto de cero](polyline-images/nonzero.png "Polyline distinto de cero")

En este ejemplo, el comportamiento de relleno de la polilínea se determina mediante la `Nonzero` regla de relleno.

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsColocar](index.md)
- [Xamarin.FormsFormas: reglas de relleno](fillrules.md)
