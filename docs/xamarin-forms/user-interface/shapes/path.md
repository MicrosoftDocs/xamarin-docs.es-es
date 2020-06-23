---
title: 'Xamarin.FormsFormas: ruta de acceso'
description: La Xamarin.Forms clase path se puede usar para dibujar curvas y formas complejas.
ms.prod: xamarin
ms.assetid: B29486F4-9A5E-4588-ABDF-7EB1E69B9AE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 384dcef3c2b480166f17e91d547f8cda39c83dc0
ms.sourcegitcommit: 7fc658bbdcb8130cd9d611e55e79a1830fc5d5a2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2020
ms.locfileid: "85132975"
---
# <a name="xamarinforms-shapes-path"></a>Xamarin.FormsFormas: ruta de acceso

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Path` clase se deriva de la `Shape` clase y se puede usar para dibujar curvas y formas complejas. Estas curvas y formas suelen describirse mediante `Geometry` objetos. Para obtener información sobre las propiedades que la `Path` clase hereda de la `Shape` clase, vea [ Xamarin.Forms formas](index.md).

`Path` define las siguientes propiedades:

- `Data`, de tipo `Geometry` , que especifica la forma que se va a dibujar.
- `RenderTransform`, de tipo `Transform` , que representa la transformación que se aplica a la geometría de una ruta de acceso antes de que se dibuje.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Para obtener más información sobre las transformaciones, vea [ Xamarin.Forms transformaciones de trazado](path-transforms.md).

## <a name="create-a-path"></a>Crear una ruta de acceso

En el siguiente ejemplo de XAML se muestra cómo dibujar un polígono usando una sintaxis abreviada especial conocida como sintaxis de marcado de trazados:

```xaml
<Path Data="M 10,50 L 200,70"
      Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100" />
```

La `Data` cadena comienza con el comando "moveTo", indicado por `M` , que establece un punto de inicio para la ruta de acceso. `L`es el comando de línea, que crea una línea recta desde el punto inicial hasta el punto final especificado.

> [!NOTE]
> La sintaxis de marcado de rutas de acceso solo está disponible en XAML.

Para obtener más información sobre la sintaxis de marcado de trazados, vea [ Xamarin.Forms Sintaxis de marcado de trazados](path-markup-syntax.md).

## <a name="path-geometry"></a>Geometría de trazado

Las curvas y formas se pueden describir mediante `Geometry` objetos, que se utilizan para establecer la `Path` propiedad del objeto `Data` .

Hay una variedad de `Geometry` objetos entre los que elegir. Las `EllipseGeometry` `LineGeometry` clases, y `RectangleGeometry` describen formas relativamente simples. Para crear formas más complejas o crear curvas, use `PathGeometry` .

`PathGeometry`los objetos se componen de uno o varios `PathFigure` objetos. Cada `PathFigure` objeto representa una forma diferente. Cada `PathFigure` objeto se compone de uno o varios `PathSegment` objetos, cada uno de los cuales representa una parte de conexión de la forma. Entre los tipos de segmentos se incluyen los siguientes: `LineSegment` , `BezierSegment` y `ArcSegment` .

En el ejemplo siguiente, `Path` se usa para dibujar un triángulo:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100">
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

Para obtener más información sobre las geometrías, vea [ Xamarin.Forms geometrías](geometries.md).

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsColocar](index.md)
- [Xamarin.FormsGeometrías](geometries.md)
- [Xamarin.FormsSintaxis de marcado de trazados](path-markup-syntax.md)
- [Xamarin.FormsTransformaciones de ruta de acceso](path-transforms.md)
