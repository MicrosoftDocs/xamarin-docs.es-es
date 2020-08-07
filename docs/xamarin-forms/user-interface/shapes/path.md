---
title: 'Xamarin.FormsFormas: ruta de acceso'
description: La Xamarin.Forms clase path se puede usar para dibujar curvas y formas complejas.
ms.prod: xamarin
ms.assetid: B29486F4-9A5E-4588-ABDF-7EB1E69B9AE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 274ff08d7fffc1cd3a817906e14ebddfac2c9337
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918260"
---
# <a name="no-locxamarinforms-shapes-path"></a>Xamarin.FormsFormas: ruta de acceso

![API de versión preliminar](~/media/shared/preview.png)

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Path` clase se deriva de la `Shape` clase y se puede usar para dibujar curvas y formas complejas. Estas curvas y formas suelen describirse mediante `Geometry` objetos. Para obtener información sobre las propiedades que la `Path` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

`Path` define las siguientes propiedades:

- `Data`, de tipo `Geometry` , que especifica la forma que se va a dibujar.
- `RenderTransform`, de tipo `Transform` , que representa la transformación que se aplica a la geometría de una ruta de acceso antes de que se dibuje.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Para obtener más información sobre las transformaciones, vea [ Xamarin.Forms transformaciones de trazado](path-transforms.md).

## <a name="create-a-path"></a>Crear una ruta de acceso

Para dibujar un trazado, cree un `Path` objeto y establezca su `Data` propiedad. Hay dos técnicas para establecer la `Data` propiedad:

- Puede establecer un valor de cadena para `Data` en XAML mediante la sintaxis de marcado de trazados. Con este enfoque, el `Path.Data` valor está consumiendo un formato de serialización para los gráficos. Normalmente, no edite este valor de cadena manualmente después de crearlo. En su lugar, se usan las herramientas de diseño para manipular los datos y se exportan como un fragmento de cadena que puede utilizar la `Data` propiedad.
- Puede establecer la `Data` propiedad en un `Geometry` objeto. Puede ser un objeto específico `Geometry` o una `GeometryGroup` que actúa como un contenedor que puede combinar varios objetos Geometry en un solo objeto.

### <a name="create-a-path-with-path-markup-syntax"></a>Crear una ruta de acceso con sintaxis de marcado de trazados

En el siguiente ejemplo de XAML se muestra cómo dibujar un triángulo mediante la sintaxis de marcado de trazados:

```xaml
<Path Data="M 10,100 L 100,100 100,50Z"
      Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start" />
```

La `Data` cadena comienza con el comando move, indicado por `M` , que establece un punto de inicio absoluto para la ruta de acceso. `L`es el comando de línea, que crea una línea recta desde el punto inicial hasta el punto final especificado. `Z`es el comando cerrar, que crea una línea que conecta el punto actual con el punto inicial. El resultado es un triángulo:

![Triángulo de trazado](path-images/triangle.png "Triángulo de trazado")

> [!NOTE]
> La sintaxis de marcado de rutas de acceso solo está disponible en XAML.

Para obtener más información sobre la sintaxis de marcado de trazados, vea [ Xamarin.Forms Sintaxis de marcado de trazados](path-markup-syntax.md).

### <a name="create-a-path-with-geometry-objects"></a>Crear una ruta de acceso con objetos Geometry

Las curvas y formas se pueden describir mediante `Geometry` objetos, que se utilizan para establecer la `Path` propiedad del objeto `Data` . Hay una variedad de `Geometry` objetos entre los que elegir. Las `EllipseGeometry` `LineGeometry` clases, y `RectangleGeometry` describen formas relativamente simples. Para crear formas más complejas o crear curvas, use `PathGeometry` .

`PathGeometry`los objetos se componen de uno o varios `PathFigure` objetos. Cada `PathFigure` objeto representa una forma diferente. Cada `PathFigure` objeto se compone de uno o varios `PathSegment` objetos, cada uno de los cuales representa una parte de conexión de la forma. Los tipos de segmento incluyen las `LineSegment` `BezierSegment` clases, y `ArcSegment` .

En el siguiente ejemplo de XAML se muestra cómo dibujar un triángulo mediante un `PathGeometry` objeto:

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

En este ejemplo, el punto inicial del triángulo es (10.100). Un segmento de línea se dibuja de (10.100) a (100.100) y de (100.100) a (100, 50). A continuación, se conectan los segmentos primero y último, porque la `PathFigure.IsClosed` propiedad está establecida en `true` . El resultado es un triángulo:

![Triángulo de trazado](path-images/triangle.png "Triángulo de trazado")

Para obtener más información sobre las geometrías, vea [ Xamarin.Forms geometrías](geometries.md).

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsColocar](index.md)
- [Xamarin.FormsGeometrías](geometries.md)
- [Xamarin.FormsSintaxis de marcado de trazados](path-markup-syntax.md)
- [Xamarin.FormsTransformaciones de ruta de acceso](path-transforms.md)
