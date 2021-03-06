---
title: 'Xamarin.Forms Formas: Polyline'
description: La Xamarin.Forms clase Polyline se puede usar para dibujar una serie de líneas rectas conectadas.
ms.prod: xamarin
ms.assetid: 15D02690-AC03-457E-8815-8E4C17E4D642
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/05/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 95b60c4ab28200dd2bc2434396df0832532bdd7a
ms.sourcegitcommit: 06701714021545eb5e932847829b876082194ffc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585876"
---
# <a name="xamarinforms-shapes-polyline"></a>Xamarin.Forms Formas: Polyline

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Polyline` clase se deriva de la `Shape` clase y se puede usar para dibujar una serie de líneas rectas conectadas. Una polilínea es similar a un polígono, excepto que el último punto de una polilínea no está conectado al primer punto. Para obtener información sobre las propiedades que la `Polyline` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

`Polyline` define las siguientes propiedades:

- `Points`, de tipo `PointCollection` , que es una colección de `Point` estructuras que describen los puntos de vértice de la polilínea.
- `FillRule`, de tipo `FillRule` , que especifica cómo se combinan las áreas de intersección de la polilínea. El valor predeterminado de esta propiedad es `FillRule.EvenOdd`.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

El `PointsCollection` tipo es `ObservableCollection` de [`Point`](xref:Xamarin.Forms.Point) objetos. La `Point` estructura define `X` `Y` las propiedades y, de tipo `double` , que representan un par de coordenadas x e y en el espacio 2D. Por lo tanto, la `Points` propiedad debe establecerse en una lista de pares de coordenada x y de coordenada y que describan los puntos de vértice de la polilínea, delimitados por una sola coma y/o uno o varios espacios. Por ejemplo, "40, 10 70, 80" y "40 10, 70 80" son válidos.

Para obtener más información acerca de la `FillRule` enumeración, consulte [ Xamarin.Forms formas: rellenar reglas](fillrules.md).

## <a name="create-a-polyline"></a>Crear una polilínea

Para dibujar una polilínea, cree un `Polyline` objeto y establezca su `Points` propiedad en los vértices de una forma. Para dar a la polilínea un contorno, establezca su `Stroke` propiedad en un [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado de. La `StrokeThickness` propiedad especifica el grosor del contorno de polilínea. Para obtener más información sobre los `Brush` objetos, vea [ Xamarin.Forms pinceles](~/xamarin-forms/user-interface/brushes/index.md).

> [!IMPORTANT]
> Si establece la `Fill` propiedad de un `Polyline` en un [`Brush`](xref:Xamarin.Forms.Brush) objeto derivado de, se pinta el espacio interior de la polilínea, incluso si el punto inicial y el final no forman una intersección.

En el siguiente ejemplo de XAML se muestra cómo dibujar una polilínea:

```xaml
<Polyline Points="0,0 10,30, 15,0 18,60 23,30 35,30 40,0 43,60 48,30 100,30"
          Stroke="Red" />
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

- [ShapeDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Colocar](index.md)
- [Xamarin.Forms Formas: reglas de relleno](fillrules.md)
- [Pinceles de Xamarin.Forms](~/xamarin-forms/user-interface/brushes/index.md)
