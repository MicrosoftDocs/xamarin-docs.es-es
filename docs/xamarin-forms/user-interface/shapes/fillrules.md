---
title: 'Xamarin.FormsFormas: reglas de relleno'
description: Xamarin.FormsLas formas de relleno de las formas determinan si un punto está en la región de relleno de una forma.
ms.prod: xamarin
ms.assetid: 5CABB22B-C6BE-43D1-91D9-6E90A4BD5622
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f2ad0104ee7ea5b44df0c6c24ac20e750d1b1be0
ms.sourcegitcommit: 8f6cc5208f675c8cfb645bd9ffb0fc1f8ea71411
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2020
ms.locfileid: "85326476"
---
# <a name="xamarinforms-shapes-fill-rules"></a>Xamarin.FormsFormas: reglas de relleno

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

Varias Xamarin.Forms clases Shapes tienen `FillRule` propiedades, de tipo `FillRule` . Entre ellos se incluyen `Polygon` , `Polyline` y `GeometryGroup` .

La `FillRule` enumeración `EvenOdd` define `Nonzero` los miembros y. Cada miembro representa una regla diferente para determinar si un punto está en la región de relleno de una forma.

> [!IMPORTANT]
> Todas las formas se consideran cerradas para los propósitos de las reglas de relleno.

## <a name="evenodd"></a>EvenOdd

La `EvenOdd` regla de relleno dibuja un rayo desde el punto hasta el infinito en cualquier dirección y cuenta el número de segmentos de la forma que cruza el rayo. Si este número es impar, el punto está dentro de. Si este número es par, el punto está fuera.

En el siguiente ejemplo de XAML se crea y se representa una forma compuesta, con el `FillRule` valor predeterminado `EvenOdd` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Fill="#CCCCFF"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <!-- FillRule doesn't need to be set, because EvenOdd is the default. -->
        <GeometryGroup>
            <EllipseGeometry RadiusX="50"
                             RadiusY="50"
                             Center="75,75" />
            <EllipseGeometry RadiusX="70"
                             RadiusY="70"
                             Center="75,75" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="75,75" />
            <EllipseGeometry RadiusX="120"
                             RadiusY="120"
                             Center="75,75" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

En este ejemplo, se muestra una forma compuesta formada por una serie de anillos concéntricos:

![Forma compuesta con regla de relleno de EvenOdd](fillrule-images/evenodd.png "Forma compuesta con regla de relleno de EvenOdd")

En la forma compuesta, observe que no se rellenan los anillos del centro y el tercer. Esto se debe a que un rayo dibujado desde cualquier punto dentro de cualquiera de esos dos anillos pasa a través de un número par de segmentos:

![Forma compuesta anotada con regla de relleno de EvenOdd](fillrule-images/evenodd-annotated.png "Forma compuesta anotada con regla de relleno de EvenOdd")

En la imagen anterior, los círculos rojos representan puntos y las líneas representan rayos arbitrarios. En el punto superior, los dos rayos arbitrarios atraviesan un número par de segmentos de línea. Por lo tanto, el anillo en el que se encuentra el punto no se rellena. En el punto inferior, los dos rayos arbitrarios pasan a través de un número impar de segmentos de línea. Por lo tanto, se rellena el anillo en el que se encuentra el punto.

## <a name="nonzero"></a>Distinto

La `Nonzero` regla de relleno dibuja un rayo desde el punto hasta el infinito en cualquier dirección y, a continuación, examina los lugares donde un segmento de la forma cruza el rayo. A partir de un recuento de cero, el recuento se incrementa cada vez que un segmento cruza el rayo de izquierda a derecha y disminuye cada vez que un segmento cruza el radio de derecha a izquierda. Después de contar las cruces, si el resultado es cero, el punto está fuera del polígono. De lo contrario, está dentro de.

En el siguiente ejemplo de XAML se crea y se representa una forma compuesta, con el `FillRule` valor de establecido en `Nonzero` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Fill="#CCCCFF"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <GeometryGroup FillRule="Nonzero">
            <EllipseGeometry RadiusX="50"
                             RadiusY="50"
                             Center="75,75" />
            <EllipseGeometry RadiusX="70"
                             RadiusY="70"
                             Center="75,75" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="75,75" />
            <EllipseGeometry RadiusX="120"
                             RadiusY="120"
                             Center="75,75" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

En este ejemplo, se muestra una forma compuesta formada por una serie de anillos concéntricos:

![Forma compuesta con regla de relleno distinto de cero](fillrule-images/nonzero.png "Forma compuesta con regla de relleno distinto de cero")

En la forma compuesta, observe que se rellenan todos los anillos. Esto se debe a que todos los segmentos se ejecutan en la misma dirección, por lo que un rayo dibujado desde cualquier punto cruzará uno o más segmentos y la suma de las cruces no será igual a cero:

![Forma compuesta anotada con regla de relleno distinto de cero](fillrule-images/nonzero-annotated.png "Forma compuesta anotada con regla de relleno distinto de cero")

En la imagen anterior, las flechas rojas representan la dirección en la que se dibujan los segmentos y la flecha negra representa un rayo arbitrario que se ejecuta desde un punto en el anillo más interno. A partir de un valor de cero, para cada segmento que cruza el radio, se agrega un valor de uno porque el segmento cruza el radio de izquierda a derecha.

Se necesita una forma más compleja con segmentos que se ejecuten en diferentes direcciones para demostrar mejor el comportamiento de la `Nonzero` regla de relleno. En el ejemplo de XAML siguiente se crea una forma similar al ejemplo anterior, salvo que se crea con un `PathGeometry` en lugar de un `EllipseGeometry` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Fill="#CCCCFF">
     <Path.Data>
         <GeometryGroup FillRule="Nonzero">
             <PathGeometry>
                 <PathGeometry.Figures>
                     <!-- Inner ring -->
                     <PathFigure StartPoint="120,120">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="50,50"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,120" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Second ring -->
                     <PathFigure StartPoint="120,100">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="70,70"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,100" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Third ring  -->
                         <PathFigure StartPoint="120,70">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="100,100"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,70" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Outer ring -->
                     <PathFigure StartPoint="120,300">
                         <PathFigure.Segments>
                             <ArcSegment Size="130,130"
                                         IsLargeArc="True"
                                         SweepDirection="Clockwise"
                                         Point="140,300" />
                         </PathFigure.Segments>
                     </PathFigure>
                 </PathGeometry.Figures>
             </PathGeometry>
         </GeometryGroup>
     </Path.Data>
 </Path>
```

En este ejemplo, se dibuja una serie de segmentos de arco que no están cerrados:

![Forma compuesta con regla de relleno distinto de cero](fillrule-images/nonzero-gaps.png "Forma compuesta con regla de relleno distinto de cero")

En la imagen anterior, no se rellena el tercer arco desde el centro. Esto se debe a que la suma de los valores de un rayo determinado que cruza los segmentos en su ruta de acceso es cero:

![Forma compuesta anotada con regla de relleno distinto de cero](fillrule-images/nonzero-gaps-annotated.png "Forma compuesta anotada con regla de relleno distinto de cero")

En la imagen anterior, el círculo rojo representa un punto, las líneas negras representan rayos arbitrarios que salen del punto en la región no rellena y las flechas rojas representan la dirección en la que se dibujan los segmentos. Como se puede observar, la suma de los valores de los rayos que cruzan los segmentos es cero:

- El rayo arbitrario que se desplaza diagonalmente a la derecha cruza dos segmentos que se ejecutan en direcciones diferentes. Por lo tanto, los segmentos se cancelan entre sí y proporcionan un valor de cero.
- El rayo arbitrario que se desplaza diagonalmente a la izquierda cruza un total de seis segmentos. Sin embargo, las cruces se cancelan entre sí para que cero sea la suma final.

Una suma de cero da como resultado que el anillo no se rellene.

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsColocar](index.md)
