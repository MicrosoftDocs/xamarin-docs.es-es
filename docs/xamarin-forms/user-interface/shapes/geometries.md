---
title: 'Xamarin.FormsFormas: geometrías'
description: Xamarin.Formslas clases Geometry permiten describir la geometría de una forma 2D.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c3b869d10d454453172065b30eb7ce32da81c8ce
ms.sourcegitcommit: 7fc658bbdcb8130cd9d611e55e79a1830fc5d5a2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2020
ms.locfileid: "85133040"
---
# <a name="xamarinforms-shapes-geometries"></a>Xamarin.FormsFormas: geometrías

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Geometry` clase y las clases que derivan de ella permiten describir la geometría de una forma 2D. `Geometry`los objetos pueden ser simples, como rectángulos y círculos, o compuestos, creados a partir de dos o más objetos Geometry. Se pueden crear geometrías más complejas mediante la `PathGeometry` clase, que permite describir arcos y curvas.

Las `Geometry` `Shape` clases y parecen similares, ya que ambos describen las formas 2D, pero tienen una diferencia importante. La `Geometry` clase se deriva de la `BindableObject` clase, mientras que la `Shape` clase se deriva de la `View` clase. Por lo tanto, `Shape` los objetos se pueden representar y participar en el sistema de diseño, mientras que los `Geometry` objetos no pueden. Aunque los objetos `Shape` se pueden usar más fácilmente que los `Geometry` objetos, los `Geometry` objetos son más versátiles. Mientras `Shape` que un objeto se usa para representar gráficos 2D, `Geometry` se puede usar un objeto para definir la región geométrica de los gráficos 2D y definir una región para el recorte.

La clase base para todas las geometrías es la clase abstracta `Geometry` . Las clases que derivan de esta clase se pueden agrupar en tres categorías: geometrías simples, geometrías de trazado y geometrías compuestas.

## <a name="simple-geometries"></a>Geometrías simples

Las clases de geometría simples son `EllipseGeometry` , `LineGeometry` y `RectangleGeometry` . Se usan para crear formas geométricas básicas, como círculos, líneas y rectángulos. Estas mismas formas, así como formas más complejas, se pueden crear mediante `PathGeometry` o combinando objetos Geometry, pero estas clases proporcionan un medio más sencillo para generar estas formas geométricas básicas.

### <a name="ellipsegeometry"></a>EllipseGeometry

Una geometría de elipse representa la geometría o una elipse o un círculo, y se define mediante un punto central, un radio x y un radio y.

La `EllipseGeometry` clase define las siguientes propiedades:

- `Center`, de tipo `Point` , que representa el punto central de la geometría.
- `RadiusX`, de tipo `double` , que representa el valor del radio x de la geometría. El valor predeterminado de esta propiedad es 0,0.
- `RadiusY`, de tipo `double` , que representa el valor del radio y de la geometría. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

En el ejemplo siguiente se muestra cómo crear y representar un `EllipseGeometry` :

```xaml
<Path Fill="Blue"
      Stroke="Red"
      StrokeThickness="1">
  <Path.Data>
    <EllipseGeometry Center="50,50"
                     RadiusX="50"
                     RadiusY="50" />
  </Path.Data>
</Path>
```

En este ejemplo, el centro de `EllipseGeometry` se establece en (50, 50) y los radios x e y se establecen en 50. Esto crea un círculo con un diámetro de 100, cuyo interior se pinta de azul.

### <a name="linegeometry"></a>LineGeometry

Una geometría de línea representa la geometría de una línea y se define especificando el punto inicial de la línea y el punto final.

La `LimeGeometry` clase define las siguientes propiedades:

- `StartPoint`, de tipo `Point` , que representa el punto inicial de la línea.
- `EndPoint`, de tipo `Point` , que representa el punto final de la línea.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

En el ejemplo siguiente se muestra cómo crear y representar un `LineGeometry` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <LineGeometry StartPoint="10,20"
                  EndPoint="100,130" />
  </Path.Data>
</Path>
```

En este ejemplo, `LineGeometry` se dibuja un de (10, 20) a (100.130).

### <a name="rectanglegeometry"></a>RectangleGeometry

Una geometría de rectángulo representa un rectángulo y se define con una `Rect` estructura que especifica su posición relativa y su alto y ancho.

La `RectangleGeometry` clase define las siguientes propiedades:

- `Rect`, de tipo `FormsRect` , que representa las dimensiones del rectángulo.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

En el ejemplo siguiente se muestra cómo crear y representar un `RectangleGeometry` :

```xaml
<Path Fill="Blue"
      Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <RectangleGeometry Rect="50,50,25,25" />
  </Path.Data>
</Path>
```

La posición y las dimensiones del rectángulo se definen mediante una `Rect` estructura. En este ejemplo, la posición es (50, 50) y el alto y el ancho son 25, lo que crea un cuadrado.

## <a name="path-geometries"></a>Geometrías de ruta de acceso

Una geometría de trazado describe una forma compleja que puede estar formada por arcos, curvas, elipses, líneas y rectángulos.

La `PathGeometry` clase define las siguientes propiedades:

- `Figures`, de tipo `PathFigureCollection` , que representa la colección de `PathFigure` objetos que describen el contenido de la ruta de acceso.
- `FillRule`, de tipo `FillRule` , que determina cómo se combinan las áreas de intersección contenidas en la geometría. El valor predeterminado de esta propiedad es `FillRule.EvenOdd`.

> [!NOTE]
> La `Figures` propiedad es `ContentProperty` de la `PathGeometry` clase y, por tanto, no es necesario establecer explícitamente desde XAML.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Un `PathGeometry` objeto se compone de una colección de `PathFigure` objetos, cada uno de `PathFigure` los cuales describe una forma en la geometría. Cada una de ellas `PathFigure` se compone de uno o varios `PathSegment` objetos, cada uno de los cuales describe un segmento de la forma. Hay muchos tipos de segmentos:

- `ArcSegment`, que crea un arco elíptico entre dos puntos.
- `BezierSegment`, que crea una curva Bézier cúbica entre dos puntos.
- `LineSegment`, que crea una línea entre dos puntos.
- `PolyBezierSegment`, que crea una serie de curvas Bézier cúbicas.
- `PolyLineSegment`, que crea una serie de líneas.
- `PolyQuadraticBezierSegment`, que crea una serie de curvas Bézier cuadráticas.
- `QuadraticBezierSegment`, que crea una curva Bézier cuadrática.

Los segmentos de un `PathFigure` se combinan en una sola forma geométrica con el punto final de cada segmento que es el punto inicial del segmento siguiente. La `StartPoint` propiedad de un `PathFigure` especifica el punto desde el que se dibuja el primer segmento. Cada segmento posterior comienza en el punto final del segmento anterior. Por ejemplo, se puede definir una línea vertical de `10,50` a estableciendo `10,150` la `StartPoint` propiedad en `10,50` y creando un `LineSegment` con un `Point` valor de propiedad de `10,150` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,50">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="10,150" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Se pueden crear geometrías más complejas mediante el uso de una combinación de `PathSegment` objetos y mediante el uso de varios `PathFigure` objetos dentro de `PathGeometry` .

## <a name="composite-geometries"></a>Geometrías compuestas

Los objetos de geometría compuestos se pueden crear mediante `GeometryGroup` . La `GeometryGroup` clase crea una geometría compuesta a partir de uno o más `Geometry` objetos. `Geometry`Se puede agregar cualquier número de objetos a `GeometryGroup` .

En el ejemplo siguiente se muestra cómo combinar las geometrías en un `GeometryGroup` :

```xaml
<Path Stroke="Green"
      StrokeThickness="2"
      Fill="Orange">
    <Path.Data>
        <GeometryGroup>
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="150,150" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="250,150" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="150,250" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="250,250" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

En este ejemplo, `EllipseGeometry` se combinan cuatro objetos con las mismas coordenadas x-radio e y, pero con distintas coordenadas de centro. Esto crea cuatro círculos superpuestos, cuyos interiores se rellenan de color naranja con la `EvenOdd` regla de relleno.

## <a name="clip-geometries"></a>Geometrías de clip

La [`VisualElement`](xref:Xamarin.Forms.VisualElement) clase tiene una `Clip` propiedad, de tipo `Geometry` , que define el contorno del contenido de un elemento. Cuando la `Clip` propiedad se establece en un `Geometry` objeto, solo se verá el área que está dentro de la región de `Geometry` .

En el ejemplo siguiente se muestra cómo usar un `Geometry` objeto como la región de recorte de un [`Image`](xref:Xamarin.Forms.Image) :

```xaml
<Image Source="monkeyface.png">
    <Image.Clip>
        <EllipseGeometry RadiusX="100"
                         RadiusY="100"
                         Center="180,180" />
    </Image.Clip>
</Image>
```

En este ejemplo, un `EllipseGeometry` con `RadiusX` `RadiusY` los valores y de 100 y un `Center` valor de (180.180) se establece en la `Clip` propiedad de [`Image`](xref:Xamarin.Forms.Image) . Solo se mostrará la parte de la imagen que se encuentra dentro del área de la elipse:

![Recortar una imagen con un EllipseGeometry](geometries-images/clip-ellipsegeometry.png "Recortar una imagen con un EllipseGeometry")

> [!NOTE]
> Las geometrías simples, las geometrías de trazado y las geometrías compuestas se pueden usar para recortar [`VisualElement`](xref:Xamarin.Forms.VisualElement) objetos.

## <a name="other-features"></a>Otras características

La `GeometryHelper` clase proporciona los siguientes métodos auxiliares:

- `FlattenGeometry`
- `FlattenCubicBezier`
- `FlattenQuadraticBezier`
- `FlattenArc`

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsColocar](index.md)
