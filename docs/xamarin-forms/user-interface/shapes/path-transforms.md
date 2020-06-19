---
title: 'Xamarin.FormsFormas: transformaciones de trazado'
description: Una Xamarin.Forms transformación define cómo transformar un objeto de ruta de acceso de un espacio de coordenadas a otro.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: ab759428c1bc5de8840808443ba40c501fb43b51
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2020
ms.locfileid: "84947346"
---
# <a name="xamarinforms-shapes-path-transforms"></a>Xamarin.FormsFormas: transformaciones de trazado

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Transform`Define cómo transformar un `Path` objeto de un espacio de coordenadas en otro. Esta asignación se describe mediante una transformación `Matrix` , que es una colección de tres filas con tres columnas de `double` valores.

Una matriz de 3x3 se usa para las transformaciones en un plano 2D x-y. Las matrices de transformación afín se pueden multiplicar para formar cualquier número de transformaciones lineales, como la rotación y el sesgo, seguida de la traslación. En la tabla siguiente se muestra la estructura de una Xamarin.Forms matriz:

| | | |
|---------|---------|-----|
| M11     | M12     | 0,0 |
| M21     | M22     | 0,0 |
| OffsetX | OffsetY | 1.0 |

Al manipular los valores de la matriz, puede girar, escalar, sesgar y trasladar `Path` objetos. Por ejemplo, si cambia el `OffsetX` valor a 100, puede utilizarlo para trasladar un `Path` objeto 100 unidades independientes del dispositivo a lo largo del eje x. Si cambia el `M22` valor a 3, puede usarlo para expandir un `Path` objeto hasta tres veces su alto actual. Si cambia ambos valores, mueva el `Path` objeto 100 unidades independientes del dispositivo a lo largo del eje x y estire el alto con un factor de 3.

> [!NOTE]
> Una matriz de transformación afín tiene su columna final igual a (0, 0, 1), por lo que solo es necesario especificar los miembros de las dos primeras columnas. Los miembros de la fila final, `OffsetX` y `OffsetY` , representan valores de traslación.

Aunque puede usar una `Matrix` estructura directamente para traducir puntos individuales, Xamarin.Forms también proporciona las siguientes clases que le permiten transformar `Path` objetos sin trabajar directamente con matrices:

- `RotateTransform`, que gira un `Path` según un especificado `Angle` .
- `ScaleTransform`, que escala un `Path` objeto según los `ScaleX` valores y especificados `ScaleY` .
- `SkewTransform`, que sesga un `Path` objeto especificando `AngleX` y `AngleY` cantidades.
- `TranslateTransform`, que mueve un `Path` objeto especificando `X` y `Y` cantidades.

Xamarin.Formstambién proporciona las siguientes clases para crear transformaciones más complejas:

- `TransformGroup`, que representa un compuesto compuesto por `Transform` otros `Transform` objetos.
- `CompositeTransform`, que representa un compuesto compuesto por `Transform` otros `Transform` objetos.
- `MatrixTransform`, que crea transformaciones personalizadas que no proporcionan las otras `Transform` clases.

Todas estas clases se derivan de la `Transform` clase, que define una `Value` propiedad de tipo `Matrix` . Esta propiedad representa la transformación actual como un `Matrix` objeto.

Para aplicar una transformación a un `Path` , cree una clase de transformación y establézcala como el valor de la `Path.RenderTransform` propiedad.

## <a name="rotation-transform"></a>Transformación de giro

Una transformación girar gira un `Path` objeto en el sentido de las agujas del reloj sobre un punto especificado en un sistema de coordenadas x-y 2D.

La `RotateTransform` clase, que deriva de la `Transform` clase, define las siguientes propiedades:

- `Angle`, de tipo `double` , representa el ángulo, en grados, de la rotación en el sentido de las agujas del reloj. El valor predeterminado de esta propiedad es 0,0.
- `CenterX`, de tipo `double` , representa la coordenada x del punto central de rotación. El valor predeterminado de esta propiedad es 0,0.
- `CenterY`, de tipo `double` , representa la coordenada y del punto central de rotación. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Las `CenterX` `CenterY` propiedades y especifican el punto sobre el que `Path` se gira el objeto. Este punto central se expresa en el espacio de coordenadas del objeto que se transforma. De forma predeterminada, la rotación se aplica a (0,0), que es la esquina superior izquierda del `Path` objeto.

En el ejemplo siguiente se muestra cómo rotar un `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <RotateTransform CenterX="0"
                         CenterY="0"
                         Angle="45" />
    </Path.RenderTransform>
</Path>
```

En este ejemplo, el `Path` objeto se gira 45 grados sobre su esquina superior izquierda.

## <a name="scale-transform"></a>Transformación de escala

Una transformación de escala escala un `Path` objeto en el sistema de coordenadas x-y 2D.

La `ScaleTransform` clase, que deriva de la `Transform` clase, define las siguientes propiedades:

- `ScaleX`, de tipo `double` , que representa el factor de escala del eje x. El valor predeterminado de esta propiedad es 1,0.
- `ScaleY`, de tipo `double` , que representa el factor de escala del eje y. El valor predeterminado de esta propiedad es 1,0.
- `CenterX`, de tipo `double` , que representa la coordenada x del punto central de esta transformación. El valor predeterminado de esta propiedad es 0,0.
- `CenterY`, de tipo `double` , que representa la coordenada y del punto central de esta transformación. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

El valor de `ScaleX` y `ScaleY` tiene un gran impacto en el escalado resultante:

- Los valores entre 0 y 1 disminuyen el ancho y el alto del objeto escalado.
- Los valores mayores que 1 aumentan el ancho y el alto del objeto escalado.
- Los valores 1 indican que el objeto no se ha escalado.
- Los valores negativos voltean el objeto de escala horizontal y verticalmente.
- Los valores entre 0 y-1 voltean el objeto de escala y reducen su ancho y alto.
- Los valores menores que-1 voltean el objeto y aumentan su ancho y alto.
- Los valores de-1 voltean el objeto escalado, pero no cambian su tamaño horizontal o vertical.

Las `CenterX` `CenterY` propiedades y especifican el punto sobre el que `Path` se escala el objeto. Este punto central se expresa en el espacio de coordenadas del objeto que se transforma. De forma predeterminada, el escalado se aplica a (0,0), que es la esquina superior izquierda del `Path` objeto. Esto tiene el efecto de mover el `Path` objeto y hacer que parezca mayor, porque al aplicar una transformación cambia el espacio de coordenadas en el que `Path` reside el objeto.

En el ejemplo siguiente se muestra cómo escalar un `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <ScaleTransform CenterX="0"
                        CenterY="0"
                        ScaleX="2"
                        ScaleY="2" />
    </Path.RenderTransform>
</Path>
```

En este ejemplo, el `Path` objeto se escala para doblar el tamaño.

## <a name="skew-transform"></a>Sesgar transformación

Una transformación de sesgo sesga un `Path` objeto en el sistema de coordenadas x-y 2D, y es útil para crear la ilusión de profundidad 3D en un objeto 2D.

La `SkewTransform` clase, que deriva de la `Transform` clase, define las siguientes propiedades:

- `AngleX`, de tipo `double` , que representa el ángulo de sesgado del eje x, que se mide en grados en sentido contrario a las agujas del reloj desde el eje y. El valor predeterminado de esta propiedad es 0,0.
- `AngleY`, de tipo `double` , que representa el ángulo de sesgado del eje y, que se mide en grados en sentido contrario a las agujas del reloj desde el eje x. El valor predeterminado de esta propiedad es 0,0.
- `CenterX`, de tipo `double` , que representa la coordenada x del centro de la transformación. El valor predeterminado de esta propiedad es 0,0.
- `CenterY`, de tipo `double` , que representa la coordenada y del centro de la transformación. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Para predecir el efecto de una transformación de sesgo, tenga en cuenta que `AngleX` sesga los valores del eje x en relación con el sistema de coordenadas original. Por lo tanto, para un `AngleX` de 30, el eje y gira 30 grados a través del origen y sesga los valores en x por 30 grados desde ese origen. Del mismo modo, un `AngleY` de 30 sesga los valores y del `Path` objeto en 30 grados desde el origen.

Para sesgar un `Path` objeto en su lugar, establezca las `CenterX` `CenterY` propiedades y en el punto central del objeto.

En el ejemplo siguiente se muestra cómo sesgar un `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <SkewTransform CenterX="0"
                       CenterY="0"
                       AngleX="45"
                       AngleY="0" />
    </Path.RenderTransform>
</Path>
```

En este ejemplo, se aplica un sesgo horizontal de 45 grados al `Path` objeto, desde un punto central de (0,0).

## <a name="translate-transform"></a>Trasladar transformación

Una transformación traducir mueve un objeto en el sistema de coordenadas x-y 2D.

La `TranslateTransform` clase, que deriva de la `Transform` clase, define las siguientes propiedades:

- `X`, de tipo `double` , que representa la distancia que se va a desplace a lo largo del eje x. El valor predeterminado de esta propiedad es 0,0.
- `Y`, de tipo `double` , que representa la distancia que se va a desplace a lo largo del eje y. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

`X`Los valores negativos mueven un objeto a la izquierda, mientras que los valores positivos mueven un objeto a la derecha. `Y`Los valores negativos mueven un objeto hacia arriba, mientras que los valores positivos mueven un objeto hacia abajo.

En el ejemplo siguiente se muestra cómo trasladar un `Path` objeto:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TranslateTransform X="50"
                            Y="50" />
    </Path.RenderTransform>
</Path>
```

En este ejemplo, el `Path` objeto se mueve 50 unidades independientes del dispositivo a la derecha y las unidades independientes del dispositivo 50.

## <a name="apply-multiple-transforms"></a>Aplicar varias transformaciones

Xamarin.Formstiene dos clases que admiten la aplicación de varias transformaciones a un `Path` objeto. Estos son `TransformGroup` , y `CompositeTransform` . Una `TransformGroup` realiza transformaciones en cualquier orden deseado, mientras que `CompositeTransform` realiza transformaciones en un orden concreto.

### <a name="transform-groups"></a>Transformar grupos

Los grupos de transformación representan transformaciones compuestas compuestas de varios `Transform` objetos.

La `TransformGroup` clase, que deriva de la `Transform` clase, define las siguientes propiedades:

- `Children`, de tipo `TransformCollection` , que representa una colección de `Transform` objetos.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

El orden de las transformaciones es importante en una transformación compuesta que utiliza la `TransformGroup` clase. Por ejemplo, si primero se gira, después se realiza la escala y después se traduce, se obtiene un resultado diferente que si primero se traslada, después se gira y luego se escala. Un orden de razón es importante es que las transformaciones como la rotación y el escalado se llevan a cabo en relación con el origen del sistema de coordenadas. El escalado de un objeto que está centrado en el origen genera un resultado diferente para escalar un objeto que se ha quitado del origen. Del mismo modo, la rotación de un objeto que está centrado en el origen genera un resultado diferente que la rotación de un objeto que se ha quitado del origen.

En el ejemplo siguiente se muestra cómo realizar una transformación compuesta mediante la `TransformGroup` clase:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TransformGroup>
            <ScaleTransform ScaleY="2" />
            <RotateTransform Angle="45" />
        </TransformGroup>
    </Path.RenderTransform>
</Path>
```

En este ejemplo, el `Path` objeto se escala al doble de su tamaño y gira en 45 grados.

## <a name="composite-transforms"></a>Transformaciones compuestas

Una transformación compuesta aplica varias transformaciones a un objeto.

La `CompositeTransform` clase, que deriva de la `Transform` clase, define las siguientes propiedades:

- `CenterX`, de tipo `double` , que representa la coordenada x del punto central de esta transformación. El valor predeterminado de esta propiedad es 0,0.
- `CenterY`, de tipo `double` , que representa la coordenada y del punto central de esta transformación. El valor predeterminado de esta propiedad es 0,0.
- `ScaleX`, de tipo `double` , que representa el factor de escala del eje x. El valor predeterminado de esta propiedad es 1,0.
- `ScaleY`, de tipo `double` , que representa el factor de escala del eje y. El valor predeterminado de esta propiedad es 1,0.
- `SkewX`, de tipo `double` , que representa el ángulo de sesgado del eje x, que se mide en grados en sentido contrario a las agujas del reloj desde el eje y. El valor predeterminado de esta propiedad es 0,0.
- `SkewY`, de tipo `double` , que representa el ángulo de sesgado del eje y, que se mide en grados en sentido contrario a las agujas del reloj desde el eje x. El valor predeterminado de esta propiedad es 0,0.
- `Rotation`, de tipo `double` , representa el ángulo, en grados, de la rotación en el sentido de las agujas del reloj. El valor predeterminado de esta propiedad es 0,0.
- `TranslateX`, de tipo `double` , que representa la distancia que se va a desplace a lo largo del eje x. El valor predeterminado de esta propiedad es 0,0.
- `TranslateY`, de tipo `double` , que representa la distancia que se va a desplace a lo largo del eje y. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

Un `CompositeTransform` aplica las transformaciones en este orden:

1. Escala ( `ScaleX` y `ScaleY` ).
1. Sesgar ( `SkewX` y `SkewY` ).
1. Rotate ( `Rotation` ).
1. Translate ( `TranslateX` , `TranslateY` ).

Si desea aplicar varias transformaciones a un objeto en un orden diferente, debe crear `TransformGroup` e insertar las transformaciones en el orden previsto.

> [!IMPORTANT]
> `CompositeTransform`Usa los mismos puntos centrales, `CenterX` y `CenterY` , para todas las transformaciones. Si desea especificar diferentes puntos centrales por transformación, use `TransformGroup` ,

En el ejemplo siguiente se muestra cómo realizar una transformación compuesta mediante la `CompositeTransform` clase:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <CompositeTransform ScaleX="2"
                            ScaleY="2"
                            Rotation="45"
                            TranslateX="50"
                            TranslateY="50" />
    </Path.RenderTransform>
</Path>
```

En este ejemplo, el `Path` objeto se escala a dos veces su tamaño, girado en 45 grados y se traduce en unidades independientes del dispositivo 50.

## <a name="custom-transforms"></a>Transformaciones personalizadas

Una transformación de matriz manipula objetos o sistemas de coordenadas en un plano 2D mediante una matriz afín. Una matriz de 3x3 se usa para las transformaciones. Puede multiplicar las transformaciones de matriz afín para formar transformaciones lineales, como la rotación y el sesgo seguidos de la traducción.

La `MatrixTransform` clase, que deriva de la `Transform` clase, define las siguientes propiedades:

- `Matrix`, de tipo `Matrix` , que representa la matriz que define la transformación.

Estas propiedades están respaldadas por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que pueden ser destinos de enlaces de datos y con estilo.

La `MatrixTransform` clase se utiliza para crear una transformación personalizada no proporcionada por las `RotateTransform` `ScaleTransform` clases,, `SkewTransform` o `TranslateTransform` .

En el ejemplo siguiente se muestra cómo transformar un `Path` objeto mediante `MatrixTransform` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform>
            <MatrixTransform.Matrix>
                <!-- M11 stretches, M12 skews -->
                <Matrix OffsetX="10"
                        OffsetY="100"
                        M11="3"
                        M12="2" />
            </MatrixTransform.Matrix>
        </MatrixTransform>
    </Path.RenderTransform>
</Path>
```

En este ejemplo, el `Path` objeto se ajusta, se sesga y se desplaza en las dimensiones X e y.

Como alternativa, se puede escribir en una forma simplificada que usa un convertidor de tipos que está integrado en Xamarin.Forms :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform Matrix="3,2,0,1,10,100" />
    </Path.RenderTransform>
</Path>
```

En este ejemplo, la `Matrix` propiedad se especifica como una cadena delimitada por comas que consta de seis miembros `M11` :, `M12` , `M21` , `M22` , `OffsetX` , `OffsetY` .

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapedemos/)
- [Xamarin.FormsColocar](index.md)
