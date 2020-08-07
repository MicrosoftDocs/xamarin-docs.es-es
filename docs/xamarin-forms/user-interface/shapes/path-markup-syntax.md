---
title: 'Xamarin.FormsFormas: sintaxis de marcado de trazados'
description: Xamarin.FormsLa sintaxis de marcado de trazados le permite especificar de forma compacta las geometrías de ruta de acceso en XAML.
ms.prod: xamarin
ms.assetid: A2C1BD59-1A16-4E26-A825-0338E2AF9E65
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8250bd5e5f7d6c9cb93a986656e908b40cec9ae2
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918518"
---
# <a name="no-locxamarinforms-shapes-path-markup-syntax"></a>Xamarin.FormsFormas: sintaxis de marcado de trazados

![API de versión preliminar](~/media/shared/preview.png)

[![Descargar el ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

Xamarin.Formsla sintaxis de marcado de trazados le permite especificar de forma compacta las geometrías de ruta de acceso en XAML. La sintaxis se especifica como un valor de cadena para la `Path.Data` propiedad:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Data="M13.908992,16.207977 L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983Z" />
```

La sintaxis de marcado de trazados se compone de un `FillRule` valor opcional y una o varias descripciones de la figura. Esta sintaxis se puede expresar como: `<Path Data="` *[fillRule]* *figureDescription* *[figureDescription]* * `" ... />`

En esta sintaxis:

- *fillRule* es un opcional `Xamarin.Forms.Shapes.FillRule` que especifica si la geometría debe usar `EvenOdd` o `Nonzero` `FillRule` . `F0`se utiliza para especificar la `EvenOdd` regla de relleno, mientras `F1` que se usa para especificar la `Nonzero` regla de relleno. Para obtener más información sobre las reglas de relleno, consulte [ Xamarin.Forms formas: rellenar reglas](fillrules.md).
- *figureDescription* representa una figura compuesta por un comando de movimiento, comandos de dibujo y un comando de cierre opcional. Un comando de movimiento especifica el punto inicial de la figura. Los comandos Draw describen el contenido de la figura y el comando opcional Close cierra la figura.

En el ejemplo anterior, la sintaxis de marcado de trazados especifica un punto de inicio mediante el comando move ( `M` ), una serie de líneas rectas con el comando line ( `L` ) y cierra la ruta de acceso con el comando CLOSE ( `Z` ).

En la sintaxis de marcado de trazado, no se necesitan espacios antes ni después de los comandos. Además, no es necesario separar dos números con una coma o un espacio en blanco, pero esto solo se puede lograr cuando la cadena no es ambigua.

> [!TIP]
> El lenguaje de marcado de rutas de acceso usa una sintaxis compatible con las definiciones de rutas de acceso de imágenes SVG (Scalable Vector Graphics), por lo que puede ser útil para migrar gráficos del formato SVG.

## <a name="move-command"></a>Comando de movimiento

El comando move especifica el punto inicial de una nueva figura. La sintaxis de este comando es: `M` *startPoint* o `m` *StartPoint*.

En esta sintaxis, *startPoint* es una [`Point`](xref:Xamarin.Forms.Point) estructura que especifica el punto inicial de una nueva figura. Si enumera varios puntos después del comando de movimiento, se dibuja una línea en esos puntos.

`M 10,10`es un ejemplo de un comando de movimiento válido.

## <a name="draw-commands"></a>Comandos de dibujo

Un comando de dibujo puede constar de varios comandos de forma. Están disponibles los siguientes comandos Draw:

- Línea ( `L` o `l` ).
- Línea horizontal ( `H` o `h` ).
- Línea vertical ( `V` o `v` ).
- Arco elíptico ( `A` o `a` ).
- Curva Bézier cúbica ( `C` o `c` ).
- Curva Bézier cuadrática ( `Q` o `q` ).
- Curva Bézier cúbica suavizada ( `S` o `s` ).
- Curva Bézier cuadrática suavizada ( `T` o `t` ).

Cada comando Draw se especifica con una letra que no distingue mayúsculas de minúsculas. Cuando accedes a más de un comando del mismo tipo de forma secuencial, puedes omitir la entrada de comando duplicada. Por ejemplo, `L 100,200 300,400` es equivalente a `L 100,200 L 300,400` .

### <a name="line-command"></a>Comando de línea

El comando line crea una línea recta entre el punto actual y el punto final especificado. La sintaxis de este comando es: `L` *extremo* o `l` *punto de conexión*.

En esta sintaxis, el *punto de conexión* es un [`Point`](xref:Xamarin.Forms.Point) que representa el punto final de la línea.

`L 20,30` y `L 20 30` son ejemplos de comandos de línea válidos.

Para obtener información sobre cómo crear una línea recta como un `PathGeometry` objeto, consulte [crear un LineSegment](geometries.md#create-a-linesegment).

### <a name="horizontal-line-command"></a>Comando de línea horizontal

El comando de línea horizontal crea una línea horizontal entre el punto actual y la coordenada x especificada. La sintaxis de este comando es: `H` *x* o `h` *x*.

En esta sintaxis, *x* es un `double` que representa la coordenada x del punto final de la línea.

`H 90` es un ejemplo de un comando de línea horizontal válido.

### <a name="vertical-line-command"></a>Comando de línea vertical

El comando de línea vertical crea una línea vertical entre el punto actual y la coordenada y especificada. La sintaxis de este comando es: `V` *y* o `v` *y*.

En esta sintaxis, *y* es un `double` que representa la coordenada y del punto final de la línea.

`V 90` es un ejemplo de comando de línea vertical válido.

### <a name="elliptical-arc-command"></a>Comando de arco elíptico

El comando de arco elíptico crea un arco elíptico entre el punto actual y el punto final especificado. La sintaxis de este comando es: `A` *size* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *Endpoint* or `a` *size* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *endPoint*.

En esta sintaxis:

- `size`es un [`Size`](xref:Xamarin.Forms.Size) que representa el radio x e y del arco.
- `rotationAngle`es un `double` que representa el giro de la elipse, en grados.
- `isLargeArcFlag`debe establecerse en 1 si el ángulo del arco debe ser de 180 grados o superior; de lo contrario, establézcalo en 0.
- `sweepDirectionFlag`debe establecerse en 1 si el arco se dibuja en una dirección de ángulo positivo; de lo contrario, establézcalo en 0.
- `endPoint`es un [`Point`](xref:Xamarin.Forms.Point) en el que se dibuja el arco.

`A 150,150 0 1,0 150,-150`es un ejemplo de un comando de arco elíptico válido.

Para obtener información sobre cómo crear un arco elíptico como un `PathGeometry` objeto, vea [crear un objeto ArcSegment](geometries.md#create-an-arcsegment).

### <a name="cubic-bezier-curve-command"></a>Comando de curva Bézier cúbica

El comando de curva Bézier cúbica crea una curva Bézier cúbica entre el punto actual y el punto final especificado utilizando los dos puntos de control especificados. La sintaxis de este comando es: `C` *controlPoint1* *controlPoint2* *Endpoint* o `c` *controlPoint1* *controlPoint2* *endPoint*.

En esta sintaxis:

- *controlPoint1* es un [`Point`](xref:Xamarin.Forms.Point) que representa el primer punto de control de la curva, que determina la tangente inicial de la curva.
- *controlPoint2* es un [`Point`](xref:Xamarin.Forms.Point) que representa el segundo punto de control de la curva, que determina la tangente final de la curva.
- el punto de *conexión* es un [`Point`](xref:Xamarin.Forms.Point) que representa el punto en el que se dibuja la curva.

`C 100,200 200,400 300,200`es un ejemplo de un comando de curva Bézier cúbica válido.

Para obtener información sobre cómo crear una curva Bézier cúbica como un `PathGeometry` objeto, consulte [crear un BezierSegment](geometries.md#create-a-beziersegment).

### <a name="quadratic-bezier-curve-command"></a>Comando de curva Bézier cuadrática

El comando de curva Bézier cuadrática crea una curva Bézier cuadrática entre el punto actual y el punto final especificado utilizando el punto de control especificado. La sintaxis de este comando es: `Q` *controlPoint* *Endpoint* o `q` *controlPoint* *Endpoint*.

En esta sintaxis:

- *controlPoint* es un [`Point`](xref:Xamarin.Forms.Point) que representa el punto de control de la curva, que determina las tangentes inicial y final de la curva.
- el punto de *conexión* es un [`Point`](xref:Xamarin.Forms.Point) que representa el punto en el que se dibuja la curva.

`Q 100,200 300,200` es un ejemplo de un comando de curva Bézier cuadrática válido.

Para obtener información sobre cómo crear una curva Bézier cuadrática como un `PathGeometry` objeto, vea [crear un QuadraticBezierSegment](geometries.md#create-a-quadraticbeziersegment).

### <a name="smooth-cubic-bezier-curve-command"></a>Comando de curva Bézier cúbica suavizada

El comando de curva Bézier cúbica suavizada crea una curva Bézier cúbica entre el punto actual y el punto final especificado mediante el punto de control especificado. La sintaxis de este comando es: `S` *controlPoint2* *Endpoint* o `s` *controlPoint2* *Endpoint*.  

En esta sintaxis:

- *controlPoint2* es un [`Point`](xref:Xamarin.Forms.Point) que representa el segundo punto de control de la curva, que determina la tangente final de la curva.
- el punto de *conexión* es un [`Point`](xref:Xamarin.Forms.Point) que representa el punto en el que se dibuja la curva.

Se supone que el primer punto de control es el reflejo del segundo punto de control del comando anterior, con respecto al punto actual. Si no hay ningún comando anterior, o si el comando anterior no era un comando de curva Bézier cúbica o un comando de curva Bézier cúbica suavizada, se supone que el primer punto de control tiene coincidencias con el punto actual.

`S 100,200 200,300`es un ejemplo de un comando de curva Bézier cúbica suave válido.

### <a name="smooth-quadratic-bezier-curve-command"></a>Comando de curva Bézier cuadrática suavizada

El comando Smooth curva Bézier cuadrática crea una curva Bézier cuadrática entre el punto actual y el punto final especificado mediante un punto de control. La sintaxis de este comando es: `T` *extremo* o `t` *punto de conexión*.

En esta sintaxis, el *punto de conexión* es un [`Point`](xref:Xamarin.Forms.Point) que representa el punto en el que se dibuja la curva.

El punto de control se supone que es el reflejo del punto de control del comando anterior en relación al punto actual. Si no hay ningún comando anterior o si el comando anterior no era una curva de Bézier cuadrática o un comando de curva Bézier cuadrática suavizada, se supone que el punto de control tiene coincidencias con el punto actual.

`T 100,30`es un ejemplo de un comando válido de curva Bézier cuadrática suavizada.

## <a name="close-command"></a>Comando de cierre

El comando cerrar finaliza la figura actual y crea una línea que conecta el punto actual con el punto inicial de la figura. Por lo tanto, este comando crea una combinación de líneas entre el último segmento y el primer segmento de la figura.

La sintaxis del comando cerrar es: `Z` o `z` .

## <a name="additional-values"></a>Valores adicionales

En lugar de un valor numérico estándar, también puede usar los siguientes valores especiales que distinguen mayúsculas de minúsculas:

- `Infinity`representa `double.PositiveInfinity` .
- `-Infinity`representa `double.NegativeInfinity` .
- `NaN`representa `double.NaN` .

Además, también puede usar la notación científica sin distinción entre mayúsculas y minúsculas. Por lo tanto, `+1.e17` es un valor válido.

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsFormas: geometrías](geometries.md)
- [Xamarin.FormsFormas: reglas de relleno](fillrules.md)
