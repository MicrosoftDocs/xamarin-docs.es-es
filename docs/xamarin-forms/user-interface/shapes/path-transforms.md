---
title: 'Xamarin.Forms Formas: transformaciones de trazado'
description: Una Xamarin.Forms transformación define cómo transformar un objeto de ruta de acceso de un espacio de coordenadas a otro.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6bc61eadb40a66c278955936e15158f34709fd18
ms.sourcegitcommit: aeefe667156ca8d3ee2ee21d9bec0fa629589e33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/22/2020
ms.locfileid: "90928645"
---
# <a name="no-locxamarinforms-shapes-path-transforms"></a>Xamarin.Forms Formas: transformaciones de trazado

![API de versión preliminar](~/media/shared/preview.png)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Transform`Define cómo transformar un `Path` objeto de un espacio de coordenadas en otro. Cuando se aplica una transformación a un `Path` objeto, cambia el modo en que el objeto se representa en la interfaz de usuario.

Las transformaciones se pueden clasificar en cuatro clasificaciones generales: giro, escala, sesgo y traslación. Xamarin.Forms define una clase para cada una de estas clasificaciones de transformación:

- `RotateTransform`, que gira un `Path` según un especificado `Angle` .
- `ScaleTransform`, que escala un `Path` objeto según los `ScaleX` valores y especificados `ScaleY` .
- `SkewTransform`, que sesga un `Path` objeto especificando `AngleX` y `AngleY` cantidades.
- `TranslateTransform`, que mueve un `Path` objeto especificando `X` y `Y` cantidades.

Xamarin.Forms también proporciona las siguientes clases para crear transformaciones más complejas:

- `TransformGroup`, que representa una transformación compuesta compuesta por varios objetos transform.
- `CompositeTransform`, que aplica varias operaciones de transformación a un `Path` objeto.
- `MatrixTransform`, que crea transformaciones personalizadas que no proporcionan las otras clases de transformación.

Todas estas clases se derivan de la `Transform` clase, que define una `Value` propiedad de tipo `Matrix` , que representa la transformación actual como un `Matrix` objeto. Esta propiedad está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que puede ser el destino de los enlaces de datos y con estilo. Para obtener más información sobre el `Matrix` struct, vea [Transform Matrix](#transform-matrix).

Para aplicar una transformación a un `Path` , cree una clase de transformación y establézcala como el valor de la `Path.RenderTransform` propiedad.

## <a name="rotation-transform"></a>Transformación de giro

Una transformación girar gira un `Path` objeto en el sentido de las agujas del reloj sobre un punto especificado en un sistema de coordenadas x-y 2D.

La `RotateTransform` clase, que deriva de la `Transform` clase, define las siguientes propiedades:

- `Angle`, de tipo `double` , representa el ángulo, en grados, de la rotación en el sentido de las agujas del reloj. El valor predeterminado de esta propiedad es 0,0.
- `CenterX`, de tipo `double` , representa la coordenada x del punto central de rotación. El valor predeterminado de esta propiedad es 0,0.
- `CenterY`, de tipo `double` , representa la coordenada y del punto central de rotación. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

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

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

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
                        ScaleX="1.5"
                        ScaleY="1.5" />
    </Path.RenderTransform>
</Path>
```

En este ejemplo, el `Path` objeto se escala hasta 1,5 veces el tamaño.

## <a name="skew-transform"></a>Sesgar transformación

Una transformación de sesgo sesga un `Path` objeto en el sistema de coordenadas x-y 2D, y es útil para crear la ilusión de profundidad 3D en un objeto 2D.

La `SkewTransform` clase, que deriva de la `Transform` clase, define las siguientes propiedades:

- `AngleX`, de tipo `double` , que representa el ángulo de sesgado del eje x, que se mide en grados en sentido contrario a las agujas del reloj desde el eje y. El valor predeterminado de esta propiedad es 0,0.
- `AngleY`, de tipo `double` , que representa el ángulo de sesgado del eje y, que se mide en grados en sentido contrario a las agujas del reloj desde el eje x. El valor predeterminado de esta propiedad es 0,0.
- `CenterX`, de tipo `double` , que representa la coordenada x del centro de la transformación. El valor predeterminado de esta propiedad es 0,0.
- `CenterY`, de tipo `double` , que representa la coordenada y del centro de la transformación. El valor predeterminado de esta propiedad es 0,0.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

Para predecir el efecto de una transformación de sesgo, tenga en cuenta que `AngleX` sesga los valores del eje x en relación con el sistema de coordenadas original. Por lo tanto, para un `AngleX` de 30, el eje y gira 30 grados a través del origen y sesga los valores en x por 30 grados desde ese origen. Del mismo modo, un `AngleY` de 30 sesga los valores y del `Path` objeto en 30 grados desde el origen.

> [!NOTE]
> Para sesgar un `Path` objeto en su lugar, establezca las `CenterX` `CenterY` propiedades y en el punto central del objeto.

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

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

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

## <a name="multiple-transforms"></a>Varias transformaciones

Xamarin.Forms tiene dos clases que admiten la aplicación de varias transformaciones a un `Path` objeto. Estos son `TransformGroup` , y `CompositeTransform` . Una `TransformGroup` realiza transformaciones en cualquier orden deseado, mientras que `CompositeTransform` realiza transformaciones en un orden concreto.

### <a name="transform-groups"></a>Transformar grupos

Los grupos de transformación representan transformaciones compuestas compuestas de varios `Transform` objetos.

La `TransformGroup` clase, que deriva de la `Transform` clase, define una `Children` propiedad, de tipo `TransformCollection` , que representa una colección de `Transform` objetos. Esta propiedad está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que puede ser el destino de los enlaces de datos y con estilo.

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
            <ScaleTransform ScaleX="1.5"
                            ScaleY="1.5" />
            <RotateTransform Angle="45" />
        </TransformGroup>
    </Path.RenderTransform>
</Path>
```

En este ejemplo, el `Path` objeto se escala hasta 1,5 veces su tamaño y, a continuación, se gira en 45 grados.

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

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty), lo que significa que pueden ser destinos de los enlaces de datos, y con estilo.

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
        <CompositeTransform ScaleX="1.5"
                            ScaleY="1.5"
                            Rotation="45"
                            TranslateX="50"
                            TranslateY="50" />
    </Path.RenderTransform>
</Path>
```

En este ejemplo, el `Path` objeto se escala hasta 1,5 veces su tamaño, girado en 45 grados y, a continuación, lo traducen las unidades independientes del dispositivo 50.

## <a name="transform-matrix"></a>Matriz de transformación

Una transformación se puede describir en términos de una matriz de transformación afín de 3x3 que realiza transformaciones en el espacio 2D. Esta matriz de 3x3 se representa mediante la `Matrix` estructura, que es una colección de tres filas y tres columnas de `double` valores.

La `Matrix` estructura define las siguientes propiedades:

- `Determinant`, de tipo `double` , que obtiene el factor determinante de la matriz.
- `HasInverse`, de tipo `bool` , que indica si la matriz se va a invertir.
- `Identity`, de tipo `Matrix` , que obtiene una matriz de identidad.
- `HasIdentity`, de tipo `bool` , que indica si la matriz es una matriz de identidad.
- `M11`, de tipo `double` , que representa el valor de la primera fila y la primera columna de la matriz.
- `M12`, de tipo `double` , que representa el valor de la primera fila y la segunda columna de la matriz.
- `M21`, de tipo `double` , que representa el valor de la segunda fila y la primera columna de la matriz.
- `M22`, de tipo `double` , que representa el valor de la segunda fila y la segunda columna de la matriz.
- `OffsetX`, de tipo `double` , que representa el valor de la tercera fila y la primera columna de la matriz.
- `OffsetY`, de tipo `double` , que representa el valor de la tercera fila y la segunda columna de la matriz.

Las `OffsetX` `OffsetY` propiedades y se denominan así porque especifican la cantidad de trasladar el espacio de coordenadas a lo largo del eje x, y del eje y, respectivamente.

Además, el `Matrix` struct expone una serie de métodos que se pueden usar para manipular los valores de la matriz, incluidos `Append` , `Invert` , `Multiply` `Prepend` y muchos más.

En la tabla siguiente se muestra la estructura de una Xamarin.Forms matriz:

:::row:::
    :::column:::
        M11
    :::column-end:::
    :::column:::
        M12
    :::column-end:::
    :::column:::
        0,0
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        M21
    :::column-end:::
    :::column:::
        M22
    :::column-end:::
    :::column:::
        0,0
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        OffsetX
    :::column-end:::
    :::column:::
        OffsetY
    :::column-end:::
    :::column:::
        1,0
    :::column-end:::
:::row-end:::

> [!NOTE]
> Una matriz de transformación afín tiene su columna final igual a (0, 0, 1), por lo que solo es necesario especificar los miembros de las dos primeras columnas.

Al manipular los valores de la matriz, puede girar, escalar, sesgar y trasladar `Path` objetos. Por ejemplo, si cambia el `OffsetX` valor a 100, puede utilizarlo para trasladar un `Path` objeto 100 unidades independientes del dispositivo a lo largo del eje x. Si cambia el `M22` valor a 3, puede usarlo para expandir un `Path` objeto hasta tres veces su alto actual. Si cambia ambos valores, mueva el `Path` objeto 100 unidades independientes del dispositivo a lo largo del eje x y estire el alto con un factor de 3. Además, las matrices de transformación afines se pueden multiplicar para formar cualquier número de transformaciones lineales, como la rotación y el sesgo, seguida de la traslación.

## <a name="custom-transforms"></a>Transformaciones personalizadas

La `MatrixTransform` clase, que se deriva de la `Transform` clase, define una `Matrix` propiedad, de tipo `Matrix` , que representa la matriz que define la transformación. Esta propiedad está respaldada por un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objeto, lo que significa que puede ser el destino de los enlaces de datos y con estilo.

Cualquier transformación que puede describir con un `TranslateTransform` objeto, `ScaleTransform` , `RotateTransform` o `SkewTransform` se puede describir igualmente mediante un `MatrixTransform` . Sin embargo, `TranslateTransform` las `ScaleTransform` clases,, `RotateTransform` y `SkewTransform` son más fáciles de conceptualizar que establecer los componentes de vector en un `Matrix` . Por lo tanto, la `MatrixTransform` clase se utiliza normalmente para crear transformaciones personalizadas que no se proporcionan mediante las `RotateTransform` `ScaleTransform` clases,, `SkewTransform` o `TranslateTransform` .

En el ejemplo siguiente se muestra cómo transformar un `Path` objeto mediante `MatrixTransform` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform>
            <MatrixTransform.Matrix>
                <!-- M11 stretches, M12 skews -->
                <Matrix OffsetX="10"
                        OffsetY="100"
                        M11="1.5"
                        M12="1" />
            </MatrixTransform.Matrix>
        </MatrixTransform>
    </Path.RenderTransform>
</Path>
```

En este ejemplo, el `Path` objeto se ajusta, se sesga y se desplaza en las dimensiones X e y.

Como alternativa, se puede escribir en una forma simplificada que usa un convertidor de tipos integrado en Xamarin.Forms :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform Matrix="1.5,1,0,1,10,100" />
    </Path.RenderTransform>
</Path>
```

En este ejemplo, la `Matrix` propiedad se especifica como una cadena delimitada por comas que consta de seis miembros `M11` :, `M12` , `M21` , `M22` , `OffsetX` , `OffsetY` . Aunque los miembros están delimitados por comas en este ejemplo, también se pueden delimitar con uno o varios espacios.

Además, el ejemplo anterior se puede simplificar aún más si se especifican los mismos seis miembros como el valor de la `RenderTransform` propiedad:

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      RenderTransform="1.5 1 0 1 10 100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z" />
```

## <a name="related-links"></a>Vínculos relacionados

- [ShapeDemos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Colocar](index.md)
