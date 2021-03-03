---
title: Transformaciones de SkiaSharp
description: En este artículo se exploran las transformaciones para mostrar gráficos de SkiaSharp en Xamarin.Forms aplicaciones y se muestra con código de ejemplo.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: E9BE322E-ECB3-4395-AFE4-4474A0F25551
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6c59a2c669a6a60049b6bb6383faea35a7de3631
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371201"
---
# <a name="skiasharp-transforms"></a>Transformaciones de SkiaSharp

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Más información sobre las transformaciones para mostrar gráficos de SkiaSharp_

SkiaSharp admite las transformaciones de gráficos tradicionales que se implementan como métodos del [`SKCanvas`](xref:SkiaSharp.SKCanvas) objeto. Matemáticamente, las transformaciones modifican las coordenadas y los tamaños que se especifican en `SKCanvas` las funciones de dibujo a medida que se representan los objetos gráficos. Las transformaciones suelen ser útiles para dibujar gráficos repetitivos o para animaciones. Algunas técnicas &mdash; , como la rotación de mapas de bits o el texto, &mdash; no son posibles sin el uso de transformaciones.

Las transformaciones SkiaSharp admiten las siguientes operaciones:

- *Trasladar* a coordenadas de desplazamiento de una ubicación a otra
- *Escalar* para aumentar o disminuir las coordenadas y los tamaños
- *Girar* para rotar las coordenadas alrededor de un punto
- *Sesgar* para desplazar las coordenadas horizontal o verticalmente de forma que un rectángulo se convierta en un paralelogramo

Estas se conocen como transformaciones *afines* . Las transformaciones afines siempre conservan las líneas paralelas y nunca provocan que una coordenada o tamaño se convierta en infinito. Un cuadrado nunca se transforma en nada que no sea un paralelogramo y un círculo nunca se transforma en nada más que una elipse.

SkiaSharp también admite transformaciones no afines (también denominadas transformaciones *proyectadas* o de *perspectiva* ) basadas en una matriz de transformación estándar de 3 por 3. Una transformación no afín permite transformar un cuadrado en cualquier cuadrangular convexa, que es una figura de cuatro caras con todos los ángulos interiores inferiores a 180 grados. Las transformaciones no afines pueden hacer que las coordenadas o los tamaños se conviertan en infinitos, pero son fundamentales para los efectos 3D.

## <a name="differences-between-skiasharp-and-xamarinforms-transforms"></a>Diferencias entre SkiaSharp y Xamarin.Forms transformaciones

Xamarin.Forms también admite las transformaciones que son similares a las de SkiaSharp. La Xamarin.Forms [`VisualElement`](xref:Xamarin.Forms.VisualElement) clase define las propiedades de transformación siguientes:

- [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) y [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY)
- [`Scale`](xref:Xamarin.Forms.VisualElement.Scale)
- [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) , [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) y [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY)

Las `RotationX` `RotationY` propiedades y son transformaciones de perspectiva que crean efectos cuasi-3D.

Hay varias diferencias cruciales entre las transformaciones y transformaciones de SkiaSharp Xamarin.Forms :

La primera diferencia es que las transformaciones de SkiaSharp se aplican a todo el `SKCanvas` objeto, mientras que las Xamarin.Forms transformaciones se aplican a `VisualElement` derivados individuales. (Puede aplicar las Xamarin.Forms transformaciones al `SKCanvasView` propio objeto, ya `SKCanvasView` que deriva de `VisualElement` , pero dentro de esa `SKCanvasView` , se aplican las transformaciones SkiaSkarp).

Las transformaciones SkiaSharp son relativas a la esquina superior izquierda de `SKCanvas` , mientras que las Xamarin.Forms transformaciones son relativas a la esquina superior izquierda del `VisualElement` al que se aplican. Esta diferencia es importante al aplicar transformaciones de escala y giro, ya que estas transformaciones siempre son relativas a un punto determinado.

La gran diferencia es que las transformaciones de SKiaSharp son *métodos* mientras que las Xamarin.Forms transformaciones son *propiedades*. Esta es una diferencia semántica más allá de la diferencia sintáctica: las transformaciones SkiaSharp realizan una operación mientras las Xamarin.Forms transformaciones establecen un estado. Las transformaciones SkiaSharp se aplican a los objetos gráficos dibujados posteriormente, pero no a los objetos gráficos que se dibujan antes de aplicar la transformación. En cambio, una Xamarin.Forms transformación se aplica a un elemento representado previamente en cuanto se establece la propiedad. Las transformaciones SkiaSharp son acumulativas cuando se llama a los métodos; Xamarin.Forms las transformaciones se reemplazan cuando la propiedad se establece con otro valor.

Todos los programas de ejemplo de esta sección aparecen en la sección **transformaciones SkiaSharp** del programa [**SkiaSharpFormsDemos**](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . El código fuente se puede encontrar en la carpeta [**Transformations**](https://github.com/xamarin/xamarin-forms-samples/tree/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms) de la solución.

## <a name="the-translate-transform"></a>[Transformación de traslación](translate.md)

Obtenga información sobre cómo usar la transformación traducir para desplazar los gráficos SkiaSharp.

## <a name="the-scale-transform"></a>[Transformación de escala](scale.md)

Descubra la transformación de escala SkiaSharp para escalar objetos a varios tamaños.

## <a name="the-rotate-transform"></a>[Transformación de rotación](rotate.md)

Explore los efectos y las animaciones posibles con la transformación girar SkiaSharp.

## <a name="the-skew-transform"></a>[Transformación de sesgo](skew.md)

Vea cómo la transformación de sesgo puede crear un objeto gráfico inclinado.

## <a name="matrix-transforms"></a>[Transformaciones de matriz](matrix.md)

Profundice en las transformaciones de SkiaSharp con la matriz de transformación versátil.

## <a name="touch-manipulations"></a>[Manipulaciones táctiles](touch.md)

Use transformaciones de matriz para implementar manipulaciones táctiles para arrastrar, escalar y girar.

## <a name="non-affine-transforms"></a>[Transformaciones no afines](non-affine.md)

Vaya más allá de oridinary con efectos de transformación no afines.

## <a name="3d-rotation"></a>[Giro 3D](3d-rotation.md)

Use transformaciones no afines para girar objetos 2D en el espacio 3D.

## <a name="related-links"></a>Vínculos relacionados

- [API de SkiaSharp](/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (ejemplo)](/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)