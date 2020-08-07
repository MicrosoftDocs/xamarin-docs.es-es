---
title: 'Xamarin.FormsFormas: geometrías'
description: Xamarin.Formslas clases Geometry permiten describir la geometría de una forma 2D.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 420099cadd3a541736084403265b9302c9a32b37
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918321"
---
# <a name="no-locxamarinforms-shapes-geometries"></a>Xamarin.FormsFormas: geometrías

![API de versión preliminar](~/media/shared/preview.png)

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Geometry` clase y las clases que derivan de ella permiten describir la geometría de una forma 2D. `Geometry`los objetos pueden ser simples, como rectángulos y círculos, o compuestos, creados a partir de dos o más objetos Geometry. Además, se pueden crear geometrías más complejas que incluyen arcos y curvas.

La `Geometry` clase es la clase primaria para varias clases que definen diferentes categorías de geometrías:

- `EllipseGeometry`, que representa la geometría de una elipse o un círculo.
- `GeometryGroup`, que representa un contenedor que puede combinar varios objetos Geometry en un solo objeto.
- `LineGeometry`, que representa la geometría de una línea.
- `PathGeometry`, que representa la geometría de una forma compleja que puede estar formada por arcos, curvas, elipses, líneas y rectángulos.
- `RectangleGeometry`, que representa la geometría de un rectángulo o cuadrado.

Las `Geometry` `Shape` clases y parecen similares, ya que ambos describen las formas 2D, pero tienen una diferencia importante. La `Geometry` clase se deriva de la [`BindableObject`](xref:Xamarin.Forms.BindableObject) clase, mientras que la `Shape` clase se deriva de la [`View`](xref:Xamarin.Forms.View) clase. Por lo tanto, `Shape` los objetos se pueden representar y participar en el sistema de diseño, mientras que los `Geometry` objetos no pueden. Aunque los objetos `Shape` se pueden usar más fácilmente que los `Geometry` objetos, los `Geometry` objetos son más versátiles. Mientras `Shape` que un objeto se usa para representar gráficos 2D, `Geometry` se puede usar un objeto para definir la región geométrica de los gráficos 2D y definir una región para el recorte.

Las siguientes clases tienen propiedades que se pueden establecer en `Geometry` objetos:

- La `Path` clase utiliza `Geometry` para describir su contenido. Puede representar una `Geometry` estableciendo la `Path.Data` propiedad en un `Geometry` objeto y estableciendo las `Path` `Fill` propiedades y del objeto `Stroke` .
- La [`VisualElement`](xref:Xamarin.Forms.VisualElement) clase tiene una `Clip` propiedad, de tipo `Geometry` , que define el contorno del contenido de un elemento. Cuando la `Clip` propiedad se establece en un `Geometry` objeto, solo se verá el área que está dentro de la región de `Geometry` . Para obtener más información, consulte [clip con Geometry](#clip-with-a-geometry).

Las clases que derivan de la `Geometry` clase se pueden agrupar en tres categorías: geometrías simples, geometrías de trazado y geometrías compuestas.

## <a name="simple-geometries"></a>Geometrías simples

Las clases de geometría simples son `EllipseGeometry` , `LineGeometry` y `RectangleGeometry` . Se usan para crear formas geométricas básicas, como círculos, líneas y rectángulos. Estas mismas formas, así como formas más complejas, se pueden crear mediante `PathGeometry` o combinando objetos Geometry, pero estas clases proporcionan un enfoque más sencillo para generar estas formas geométricas básicas.

### <a name="ellipsegeometry"></a>EllipseGeometry

Una geometría de elipse representa la geometría o una elipse o un círculo, y se define mediante un punto central, un radio x y un radio y.

La clase `EllipseGeometry` define las propiedades siguientes:

- `Center`, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa el punto central de la geometría.
- `RadiusX`, de tipo `double` , que representa el valor del radio x de la geometría. El valor predeterminado de esta propiedad es 0,0.
- `RadiusY`, de tipo `double` , que representa el valor del radio y de la geometría. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

En el ejemplo siguiente se muestra cómo crear y representar un `EllipseGeometry` en un `Path` objeto:

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

En este ejemplo, el centro de `EllipseGeometry` se establece en (50, 50) y los radios x e y se establecen en 50. Esto crea un círculo rojo con un diámetro de unidades independientes del dispositivo 100, cuyo interior se pinta azul:

![EllipseGeometry](geometry-images/ellipse.png "EllipseGeometry")

### <a name="linegeometry"></a>LineGeometry

Una geometría de línea representa la geometría de una línea y se define especificando el punto inicial de la línea y el punto final.

La clase `LineGeometry` define las propiedades siguientes:

- `StartPoint`, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa el punto inicial de la línea.
- `EndPoint`, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa el punto final de la línea.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

En el ejemplo siguiente se muestra cómo crear y representar un `LineGeometry` en un `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <LineGeometry StartPoint="10,20"
                  EndPoint="100,130" />
  </Path.Data>
</Path>
```

En este ejemplo, `LineGeometry` se dibuja un de (10, 20) a (100.130):

![LineGeometry](geometry-images/line.png "LineGeometry")

> [!NOTE]
> Establecer la `Fill` propiedad de un `Path` que representa un `LineGeometry` no tendrá ningún efecto, ya que una línea no tiene ningún interior.

### <a name="rectanglegeometry"></a>RectangleGeometry

Una geometría de rectángulo representa la geometría de un rectángulo o cuadrado y se define con una `Rect` estructura que especifica su posición relativa y su alto y ancho.

La `RectangleGeometry` clase define la `Rect` propiedad, de tipo `Rect` , que representa las dimensiones del rectángulo. Esta propiedad está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que puede ser el destino de los enlaces de datos y con estilo.

En el ejemplo siguiente se muestra cómo crear y representar un `RectangleGeometry` en un `Path` objeto:

```xaml
<Path Fill="Blue"
      Stroke="Red"
      StrokeThickness="1">
  <Path.Data>
    <RectangleGeometry Rect="10,10,150,100" />
  </Path.Data>
</Path>
```

La posición y las dimensiones del rectángulo se definen mediante una `Rect` estructura. En este ejemplo, la posición es (10, 10), el ancho es 150 y el alto es de 100 unidades independientes del dispositivo:

![RectangleGeometry](geometry-images/rectangle.png "RectangleGeometry")

## <a name="path-geometries"></a>Geometrías de ruta de acceso

Una geometría de trazado describe una forma compleja que puede estar formada por arcos, curvas, elipses, líneas y rectángulos.

La clase `PathGeometry` define las propiedades siguientes:

- `Figures`, de tipo `PathFigureCollection` , que representa la colección de `PathFigure` objetos que describen el contenido de la ruta de acceso.
- `FillRule`, de tipo `FillRule` , que determina cómo se combinan las áreas de intersección contenidas en la geometría. El valor predeterminado de esta propiedad es `FillRule.EvenOdd`.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Para obtener más información acerca de la `FillRule` enumeración, consulte [ Xamarin.Forms formas: rellenar reglas](fillrules.md).

> [!NOTE]
> La `Figures` propiedad es `ContentProperty` de la `PathGeometry` clase y, por tanto, no es necesario establecer explícitamente desde XAML.

Un `PathGeometry` objeto se compone de una colección de `PathFigure` objetos, cada uno de `PathFigure` los cuales describe una forma en la geometría. Cada una de ellas `PathFigure` se compone de uno o varios `PathSegment` objetos, cada uno de los cuales describe un segmento de la forma. Hay muchos tipos de segmentos:

- `ArcSegment`, que crea un arco elíptico entre dos puntos.
- `BezierSegment`, que crea una curva Bézier cúbica entre dos puntos.
- `LineSegment`, que crea una línea entre dos puntos.
- `PolyBezierSegment`, que crea una serie de curvas Bézier cúbicas.
- `PolyLineSegment`, que crea una serie de líneas.
- `PolyQuadraticBezierSegment`, que crea una serie de curvas Bézier cuadráticas.
- `QuadraticBezierSegment`, que crea una curva Bézier cuadrática.

Todas las clases anteriores derivan de la `PathSegment` clase abstracta.

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

### <a name="create-an-arcsegment"></a>Crear un ArcSegment

Un `ArcSegment` crea un arco elíptico entre dos puntos. Un arco elíptico se define mediante sus puntos inicial y final, x-e y-radio, factor de rotación del eje x, un valor que indica si el arco debe ser superior a 180 grados y un valor que describe la dirección en la que se dibuja el arco.

La clase `ArcSegment` define las propiedades siguientes:

- `Point`, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa el extremo del arco elíptico. El valor predeterminado de esta propiedad es (0,0).
- `Size`, de tipo [`Size`](xref:Xamarin.Forms.Size) , que representa el radio x e y del arco. El valor predeterminado de esta propiedad es (0,0).
- `RotationAngle`, de tipo `double` , que representa la cantidad en grados que la elipse gira alrededor del eje x. El valor predeterminado de esta propiedad es 0.
- `SweepDirection`, de tipo `SweepDirection` , que especifica la dirección en la que se dibuja el arco. El valor predeterminado de esta propiedad es `SweepDirection.CounterClockwise`.
- `IsLargeArc`, de tipo `bool` , que indica si el arco debe ser mayor que 180 grados. El valor predeterminado de esta propiedad es `false`.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

> [!NOTE]
> La `ArcSegment` clase no contiene una propiedad para el punto inicial del arco. Solo define el punto final del arco que representa. El punto inicial del arco es el punto actual del `PathFigure` al que `ArcSegment` se agrega.

La enumeración `SweepDirection` define los miembros siguientes:

- `CounterClockwise`, que especifica que los arcos se dibujan en sentido de las agujas del reloj.
- `Clockwise`, que especifica que los arcos se dibujan en el sentido de las agujas del reloj.

En el ejemplo siguiente se muestra cómo crear y representar un `ArcSegment` en un `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,100">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <ArcSegment Size="100,50"
                                            RotationAngle="45"
                                            IsLargeArc="True"
                                            SweepDirection="CounterClockwise"
                                            Point="200,100" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

En este ejemplo, se dibuja un arco elíptico de (10.100) a (200.100).

### <a name="create-a-beziersegment"></a>Creación de un BezierSegment

Un `BezierSegment` crea una curva Bézier cúbica entre dos puntos. Una curva Bézier cúbica se define mediante cuatro puntos: un punto inicial, un punto final y dos puntos de control.

La clase `BezierSegment` define las propiedades siguientes:

- `Point1`, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa el primer punto de control de la curva. El valor predeterminado de esta propiedad es (0,0).
- `Point2`, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa el segundo punto de control de la curva. El valor predeterminado de esta propiedad es (0,0).
- `Point3`, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa el punto final de la curva. El valor predeterminado de esta propiedad es (0,0).

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

> [!NOTE]
> La `BezierSegment` clase no contiene una propiedad para el punto inicial de la curva. El punto inicial de la curva es el punto actual del `PathFigure` al que `BezierSegment` se agrega.

Los dos puntos de control de una curva Bézier cúbica se comportan como imanes, y atraen partes de lo que de otro modo serían una línea recta hacia ellos mismos y generando una curva. El primer punto de control afecta a la parte inicial de la curva. El segundo punto de control afecta a la parte final de la curva. La curva no pasa necesariamente a través de ninguno de los puntos de control. En su lugar, cada punto de control mueve su parte de la línea hacia sí misma, pero no a sí misma.

En el ejemplo siguiente se muestra cómo crear y representar un `BezierSegment` en un `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <BezierSegment Point1="100,0"
                                               Point2="200,200"
                                               Point3="300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

En este ejemplo, una curva Bézier cúbica se dibuja de (10, 10) a (300, 10). La curva tiene dos puntos de control en (100, 0) y (200.200):

![BezierSegment](geometry-images/beziersegment.png "BezierSegment")

### <a name="create-a-linesegment"></a>Crear un LineSegment

Un `LineSegment` crea una línea entre dos puntos.

La `LineSegment` clase define la `Point` propiedad, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa el punto final del segmento de línea. El valor predeterminado de esta propiedad es (0,0) y está respaldado por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que puede ser el destino de los enlaces de datos y con estilo.

> [!NOTE]
> La `LineSegment` clase no contiene una propiedad para el punto inicial de la línea. Solo define el punto final. El punto inicial de la línea es el punto actual del `PathFigure` al que `LineSegment` se agrega.

En el ejemplo siguiente se muestra cómo crear y representar `LineSegment` objetos en un `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure IsClosed="True"
                                StartPoint="10,100">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="100,100" />
                                <LineSegment Point="100,50" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

En este ejemplo, un segmento de línea se dibuja de (10.100) a (100.100) y de (100.100) a (100, 50). Además, el `PathFigure` está cerrado porque su `IsClosed` propiedad está establecida en `true` . Esto da como resultado que se dibuje un triángulo:

![LineSegments](geometry-images/linesegments.png "LineSegments")

### <a name="create-a-polybeziersegment"></a>Creación de un PolyBezierSegment

Un `PolyBezierSegment` crea una o varias curvas Bézier cúbicas.

La `PolyBezierSegment` clase define la `Points` propiedad, de tipo `PointCollection` , que representa los puntos que definen `PolyBezierSegment` . Un `PointCollection` es `ObservableCollection` de [`Point`](xref:Xamarin.Forms.Point) objetos. Esta propiedad está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que puede ser el destino de los enlaces de datos y con estilo.

> [!NOTE]
> La `PolyBezierSegment` clase no contiene una propiedad para el punto inicial de la curva. El punto inicial de la curva es el punto actual del `PathFigure` al que `PolyBezierSegment` se agrega.

En el ejemplo siguiente se muestra cómo crear y representar un `PolyBezierSegment` en un `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <PolyBezierSegment Points="0,0 100,0 150,100 150,0 200,0 300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

En este ejemplo, `PolyBezierSegment` especifica dos curvas Bézier cúbicas. La primera curva va de (10, 10) a (150.100) con un punto de control de (0,0) y otro punto de control de (100, 0). La segunda curva va de (150.100) a (300, 10) con un punto de control de (150, 0) y otro punto de control de (200, 0):

![PolyBezierSegment](geometry-images/polybeziersegment.png "PolyBezierSegment")

### <a name="create-a-polylinesegment"></a>Creación de un PolyLineSegment

Un `PolyLineSegment` crea uno o más segmentos de línea.

La `PolyLineSegment` clase define la `Points` propiedad, de tipo `PointCollection` , que representa los puntos que definen `PolyLineSegment` . Un `PointCollection` es `ObservableCollection` de [`Point`](xref:Xamarin.Forms.Point) objetos. Esta propiedad está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que puede ser el destino de los enlaces de datos y con estilo.

> [!NOTE]
> La `PolyLineSegment` clase no contiene una propiedad para el punto inicial de la línea. El punto inicial de la línea es el punto actual del `PathFigure` al que `PolyLineSegment` se agrega.

En el ejemplo siguiente se muestra cómo crear y representar un `PolyLineSegment` en un `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigure StartPoint="10,10">
                    <PathFigure.Segments>
                        <PolyLineSegment Points="50,10 50,50" />
                    </PathFigure.Segments>
                </PathFigure>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

En este ejemplo, `PolyLineSegment` especifica dos líneas. La primera línea va de (10, 10) a (50, 10) y la segunda línea va de (50, 10) a (50, 50):

![PolyLineSegment](geometry-images/polylinesegment.png "PolyLineSegment")

### <a name="create-a-polyquadraticbeziersegment"></a>Creación de un PolyQuadraticBezierSegment

Un `PolyQuadraticBezierSegment` crea una o varias curvas Bézier cuadráticas.

La `PolyQuadraticBezierSegment` clase define la `Points` propiedad, de tipo `PointCollection` , que representa los puntos que definen `PolyQuadraticBezierSegment` . Un `PointCollection` es `ObservableCollection` de [`Point`](xref:Xamarin.Forms.Point) objetos. Esta propiedad está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que puede ser el destino de los enlaces de datos y con estilo.

> [!NOTE]
> La `PolyQuadraticBezierSegment` clase no contiene una propiedad para el punto inicial de la curva. El punto inicial de la curva es el punto actual del `PathFigure` al que `PolyQuadraticBezierSegment` se agrega.

En el ejemplo siguiente se muestra cómo crear y representar un `PolyQuadraticBezierSegment` en un `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <PolyQuadraticBezierSegment Points="100,100 150,50 0,100 15,200" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

En este ejemplo, `PolyQuadraticBezierSegment` especifica dos curvas de Bézier. La primera curva va de (10, 10) a (150, 50) con un punto de control en (100.100). La segunda curva va de (100.100) a (15.200) con un punto de control en (0100):

![PolyQuadraticBezierSegment](geometry-images/polyquadraticbeziersegment.png "PolyQuadraticBezierSegment")

### <a name="create-a-quadraticbeziersegment"></a>Creación de un QuadraticBezierSegment

Un `QuadraticBezierSegment` crea una curva Bézier cuadrática entre dos puntos.

La clase `QuadraticBezierSegment` define las propiedades siguientes:

- `Point1`, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa el punto de control de la curva. El valor predeterminado de esta propiedad es (0,0).
- `Point2`, de tipo [`Point`](xref:Xamarin.Forms.Point) , que representa el punto final de la curva. El valor predeterminado de esta propiedad es (0,0).

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

> [!NOTE]
> La `QuadraticBezierSegment` clase no contiene una propiedad para el punto inicial de la curva. El punto inicial de la curva es el punto actual del `PathFigure` al que `QuadraticBezierSegment` se agrega.

En el ejemplo siguiente se muestra cómo crear y representar un `QuadraticBezierSegment` en un `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <QuadraticBezierSegment Point1="200,200"
                                                        Point2="300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

En este ejemplo, se dibuja una curva Bézier cuadrática de (10, 10) a (300, 10). La curva tiene un punto de control en (200.200):

![QuadraticBezierSegment](geometry-images/quadraticbeziersegment.png "QuadraticBezierSegment")

### <a name="create-complex-geometries"></a>Crear geometrías complejas

Se pueden crear geometrías más complejas mediante una combinación de `PathSegment` objetos. En el ejemplo siguiente se crea una forma mediante un `BezierSegment` , un `LineSegment` y un `ArcSegment` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigure StartPoint="10,50">
                    <PathFigure.Segments>
                        <BezierSegment Point1="100,0"
                                       Point2="200,200"
                                       Point3="300,100"/>
                        <LineSegment Point="400,100" />
                        <ArcSegment Size="50,50"
                                    RotationAngle="45"
                                    IsLargeArc="True"
                                    SweepDirection="Clockwise"
                                    Point="200,100"/>
                    </PathFigure.Segments>
                </PathFigure>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

En este ejemplo, `BezierSegment` se define primero un con cuatro puntos. A continuación, en el ejemplo se agrega un `LineSegment` , que se dibuja entre el punto final de `BezierSegment` hasta el punto especificado por `LineSegment` . Por último, `ArcSegment` se dibuja desde el punto final del `LineSegment` hasta el punto especificado por `ArcSegment` .

Se pueden crear geometrías incluso más complejas mediante el uso `PathFigure` de varios objetos dentro de `PathGeometry` . En el ejemplo siguiente se crea un a `PathGeometry` partir de siete `PathFigure` objetos, algunos de los cuales contienen varios `PathSegment` objetos:

```xaml
<Path Stroke="Red"
      StrokeThickness="12"
      StrokeLineJoin="Round">
    <Path.Data>
        <PathGeometry>
            <!-- H -->
            <PathFigure StartPoint="0,0">
                <LineSegment Point="0,100" />
            </PathFigure>
            <PathFigure StartPoint="0,50">
                <LineSegment Point="50,50" />
            </PathFigure>
            <PathFigure StartPoint="50,0">
                <LineSegment Point="50,100" />
            </PathFigure>

            <!-- E -->
            <PathFigure StartPoint="125, 0">
                <BezierSegment Point1="60, -10"
                               Point2="60, 60"
                               Point3="125, 50" />
                <BezierSegment Point1="60, 40"
                               Point2="60, 110"
                               Point3="125, 100" />
            </PathFigure>

            <!-- L -->
            <PathFigure StartPoint="150, 0">
                <LineSegment Point="150, 100" />
                <LineSegment Point="200, 100" />
            </PathFigure>

            <!-- L -->
            <PathFigure StartPoint="225, 0">
                <LineSegment Point="225, 100" />
                <LineSegment Point="275, 100" />
            </PathFigure>

            <!-- O -->
            <PathFigure StartPoint="300, 50">
                <ArcSegment Size="25, 50"
                            Point="300, 49.9"
                            IsLargeArc="True" />
            </PathFigure>
        </PathGeometry>
    </Path.Data>
</Path>
```

En este ejemplo, la palabra "Hello" se dibuja utilizando una combinación de `LineSegment` `BezierSegment` objetos y, junto con un solo `ArcSegment` objeto:

![Varios objetos PathFigure](geometry-images/multiple-pathfigures.png "Varios objetos PathFigure")

## <a name="composite-geometries"></a>Geometrías compuestas

Los objetos de geometría compuestos se pueden crear mediante `GeometryGroup` . La `GeometryGroup` clase crea una geometría compuesta a partir de uno o más `Geometry` objetos. `Geometry`Se puede agregar cualquier número de objetos a `GeometryGroup` .

La clase `GeometryGroup` define las propiedades siguientes:

- `Children`, de tipo `GeometryCollection` , que tiene en especie los objetos que definen `GeomtryGroup` . Un `GeometryCollection` es `ObservableCollection` de `Geometry` objetos.
- `FillRule`, de tipo `FillRule` , que especifica cómo se combinan las áreas de intersección de `GeometryGroup` . El valor predeterminado de esta propiedad es `FillRule.EvenOdd`.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

> [!NOTE]
> La `Children` propiedad es `ContentProperty` de la `GeometryGroup` clase y, por tanto, no es necesario establecer explícitamente desde XAML.

Para obtener más información acerca de la `FillRule` enumeración, consulte [ Xamarin.Forms formas: rellenar reglas](fillrules.md).

Para dibujar una geometría compuesta, establezca los `Geometry` objetos necesarios como los elementos secundarios de un elemento `GeometryGroup` y muestrelos con un `Path` objeto. En el código XAML siguiente se muestra un ejemplo de esto:

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

En este ejemplo, `EllipseGeometry` se combinan cuatro objetos con las mismas coordenadas x-radio e y, pero con distintas coordenadas de centro. Esto crea cuatro círculos superpuestos, cuyos interiores se rellenan de color naranja debido a la `EvenOdd` regla de relleno predeterminada:

![GeometryGroup](geometry-images/geometrygroup.png "GeometryGroup")

## <a name="clip-with-a-geometry"></a>Recortar con una geometría

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

- `FlattenGeometry`, que alisa un `Geometry` en un `PathGeometry` .
- `FlattenCubicBezier`, que alisa una curva Bézier cúbica en una `List<Point>` colección.
- `FlattenQuadraticBezier`, que alisa una curva Bézier cuadrática en una `List<Point>` colección.
- `FlattenArc`, que alisa un arco elíptico en una `List<Point>` colección.

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsColocar](index.md)
- [Xamarin.FormsFormas: reglas de relleno](fillrules.md)
