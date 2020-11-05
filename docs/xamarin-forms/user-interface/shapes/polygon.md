---
title: 'Xamarin.Forms Formas: Polígono'
description: La Xamarin.Forms clase Polygon se puede usar para dibujar polígonos, que son una serie de líneas conectadas que forman formas cerradas.
ms.prod: xamarin
ms.assetid: D6539F60-A5AC-46EF-86EB-E9F508EB1FA8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ff3d99759c161924260a465cf59a31c21795339d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373853"
---
# <a name="no-locxamarinforms-shapes-polygon"></a>Xamarin.Forms Formas: Polígono

![API de versión preliminar](~/media/shared/preview.png)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Polygon` clase se deriva de la `Shape` clase y se puede usar para dibujar polígonos, que son una serie de líneas conectadas que forman formas cerradas. Para obtener información sobre las propiedades que la `Polygon` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

`Polygon` define las siguientes propiedades:

- `Points`, de tipo `PointCollection` , que es una colección de [`Point`](xref:Xamarin.Forms.Point) estructuras que describen los puntos de vértice del polígono.
- `FillRule`, de tipo `FillRule` , que especifica cómo se determina el relleno interior de la forma. El valor predeterminado de esta propiedad es `FillRule.EvenOdd`.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

El `PointsCollection` tipo es `ObservableCollection` de [`Point`](xref:Xamarin.Forms.Point) objetos. La `Point` estructura define `X` `Y` las propiedades y, de tipo `double` , que representan un par de coordenadas x e y en el espacio 2D. Por lo tanto, la `Points` propiedad debe establecerse en una lista de pares de coordenada x y de coordenada y que describan los puntos de vértice del polígono, delimitados por una sola coma y/o uno o varios espacios. Por ejemplo, "40, 10 70, 80" y "40 10, 70 80" son válidos.

Para obtener más información acerca de la `FillRule` enumeración, consulte [ Xamarin.Forms formas: rellenar reglas](fillrules.md).

## <a name="create-a-polygon"></a>Crear un polígono

Para dibujar un polígono, cree un `Polygon` objeto y establezca su `Points` propiedad en los vértices de una forma. Se dibuja automáticamente una línea que conecta los puntos primero y último. Para pintar el interior del polígono, establezca su `Fill` propiedad en [`Color`](xref:Xamarin.Forms.Color) . Para dar un contorno al polígono, establezca su `Stroke` propiedad en [`Color`](xref:Xamarin.Forms.Color) . La `StrokeThickness` propiedad especifica el grosor del contorno del polígono.

En el siguiente ejemplo de XAML se muestra cómo dibujar un polígono relleno:

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5" />
```

En este ejemplo, se dibuja un polígono relleno que representa un triángulo:

![Polígono relleno](polygon-images/filled.png "Polígono relleno")

En el siguiente ejemplo de XAML se muestra cómo dibujar un polígono discontinuo:

```xaml
<Polygon Points="40,10 70,80 10,50"
         Fill="AliceBlue"
         Stroke="Green"
         StrokeThickness="5"
         StrokeDashArray="1,1"
         StrokeDashOffset="6" />
```

En este ejemplo, el contorno de polígono es discontinuo:

![Polígono discontinuo](polygon-images/dashed.png "Polígono discontinuo")

Para obtener más información sobre cómo dibujar un polígono discontinuo, vea [dibujar formas con guiones](index.md#draw-dashed-shapes).

En el ejemplo de XAML siguiente se muestra un polígono que usa la regla de relleno predeterminada:

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Blue"
         Stroke="Red"
         StrokeThickness="3" />
```

En este ejemplo, el comportamiento de relleno de cada polígono se determina mediante la `EvenOdd` regla de relleno.

![Polígono de EvenOdd](polygon-images/evenodd.png "Polígono de EvenOdd")

En el ejemplo de XAML siguiente se muestra un polígono que usa la `Nonzero` regla de relleno:

```xaml
<Polygon Points="0 48, 0 144, 96 150, 100 0, 192 0, 192 96, 50 96, 48 192, 150 200 144 48"
         Fill="Black"
         FillRule="Nonzero"
         Stroke="Yellow"
         StrokeThickness="3" />
```

![Polígono distinto de cero](polygon-images/nonzero.png "Polígono distinto de cero")

En este ejemplo, el comportamiento de relleno de cada polígono se determina mediante la `Nonzero` regla de relleno.

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Colocar](index.md)
- [Xamarin.Forms Formas: reglas de relleno](fillrules.md)