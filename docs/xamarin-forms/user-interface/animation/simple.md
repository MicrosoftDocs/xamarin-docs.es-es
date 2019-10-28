---
title: Animaciones simples en Xamarin. Forms
description: La clase ViewExtensions proporciona métodos de extensión que se pueden usar para construir animaciones simples. En este artículo se muestra cómo crear y cancelar animaciones mediante la clase ViewExtensions.
ms.prod: xamarin
ms.assetid: 4A6FAE5A-848F-4CE0-BFA1-22A6309B5225
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2019
ms.openlocfilehash: 116911787db128b103fb555554076704a0549db5
ms.sourcegitcommit: f8583585c501607fdfa061b95e9a9f385ed1d591
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/26/2019
ms.locfileid: "72959159"
---
# <a name="simple-animations-in-xamarinforms"></a>Animaciones simples en Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_La clase ViewExtensions proporciona métodos de extensión que se pueden usar para construir animaciones simples. En este artículo se muestra cómo crear y cancelar animaciones mediante la clase ViewExtensions._

La clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) proporciona los siguientes métodos de extensión que se pueden usar para crear animaciones simples:

- [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima las propiedades [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) y [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) de un [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) anima la propiedad [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de una [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) aplica un aumento o disminución incremental animado a la propiedad [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de una [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima la propiedad [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de una [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) aplica un aumento o disminución incremental animado a la propiedad [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de una [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima la propiedad [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) de una [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima la propiedad [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) de una [`VisualElement`](xref:Xamarin.Forms.VisualElement).
- [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) anima la propiedad [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) de una [`VisualElement`](xref:Xamarin.Forms.VisualElement).

De forma predeterminada, cada animación tardará 250 milisegundos. Sin embargo, se puede especificar una duración para cada animación al crear la animación.

La clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) también incluye un método [`CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) que se puede utilizar para cancelar cualquier animación.

> [!NOTE]
> La clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) proporciona un método de extensión [`LayoutTo`](xref:Xamarin.Forms.ViewExtensions.LayoutTo(Xamarin.Forms.VisualElement,Xamarin.Forms.Rectangle,System.UInt32,Xamarin.Forms.Easing)) . Sin embargo, este método está pensado para que lo usen los diseños para animar las transiciones entre los Estados de diseño que contienen cambios de tamaño y posición. Por lo tanto, solo debe usarse en subclases [`Layout`](xref:Xamarin.Forms.Layout) .

Los métodos de extensión de animación de la clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) son todos asincrónicos y devuelven un objeto `Task<bool>`. El valor devuelto es `false` si la animación se completa y `true` si se cancela la animación. Por lo tanto, los métodos de animación deben usarse normalmente con el operador `await`, lo que permite determinar fácilmente Cuándo se ha completado una animación. Además, se pueden crear animaciones secuenciales con métodos de animación posteriores que se ejecuten después de que se haya completado el método anterior. Para obtener más información, vea [animaciones compuestas](#compound).

Si hay un requisito para permitir que una animación se complete en segundo plano, se puede omitir el operador `await`. En este escenario, los métodos de extensión de animación devolverán rápidamente después de iniciar la animación, con la animación en segundo plano. Esta operación se puede aprovechar al crear animaciones compuestas. Para obtener más información, vea [animaciones compuestas](#composite).

Para obtener más información acerca del operador `await`, consulte [información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animaciones únicas

Cada método de extensión del [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) implementa una operación de animación única que cambia progresivamente una propiedad de un valor a otro durante un período de tiempo. En esta sección se explora cada operación de animación.

### <a name="rotation"></a>Giro

En el ejemplo de código siguiente se muestra cómo utilizar el método [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para animar la propiedad [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Este código anima la instancia de [`Image`](xref:Xamarin.Forms.Image) mediante la rotación de hasta 360 grados en 2 segundos (2000 milisegundos). El método [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) obtiene el valor de propiedad [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) actual para el inicio de la animación y, a continuación, gira desde ese valor hasta su primer argumento (360). Una vez completada la animación, la propiedad [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de la imagen se restablece en 0. Esto garantiza que la propiedad `Rotation` no permanezca en 360 una vez finalizada la animación, lo que impediría rotaciones adicionales.

Las capturas de pantallas siguientes muestran la rotación en curso en cada plataforma:

![](simple-images/rotateto.png "Rotation Animation")

### <a name="relative-rotation"></a>Rotación relativa

En el ejemplo de código siguiente se muestra cómo usar el método [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para aumentar o disminuir incrementalmente la propiedad [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) de un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.RelRotateTo (360, 2000);
```

Este código anima la [`Image`](xref:Xamarin.Forms.Image) instancia rotando 360 grados desde su posición inicial en más de 2 segundos (2000 milisegundos). El método [`RelRotateTo`](xref:Xamarin.Forms.ViewExtensions.RelRotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) obtiene el valor de propiedad [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) actual para el inicio de la animación y, a continuación, gira desde ese valor hasta el valor más el primer argumento (360). Esto garantiza que cada animación siempre tendrá una rotación de 360 grados desde la posición inicial. Por lo tanto, si se invoca una nueva animación mientras una animación ya está en curso, se iniciará desde la posición actual y puede acabar en una posición que no sea un incremento de 360 grados.

Las capturas de pantallas siguientes muestran la rotación relativa en curso en cada plataforma:

![](simple-images/relrotateto.png "Relative Rotation Animation")

### <a name="scaling"></a>Cambiar escala

En el ejemplo de código siguiente se muestra cómo utilizar el método [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) para animar la propiedad [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.ScaleTo (2, 2000);
```

Este código anima la [`Image`](xref:Xamarin.Forms.Image) instancia escalando hasta dos veces su tamaño en más de 2 segundos (2000 milisegundos). El método [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) obtiene el valor de propiedad [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) actual (valor predeterminado de 1) para el inicio de la animación y, a continuación, se escala desde ese valor hasta su primer argumento (2). Esto tiene el efecto de expandir el tamaño de la imagen al doble de su tamaño.

Las capturas de pantallas siguientes muestran el escalado en curso en cada plataforma:

![](simple-images/scaleto.png "Scaling Animation")

> [!NOTE]
> La clase [`VisualElement`](xref:Xamarin.Forms.VisualElement) también define las propiedades [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) y [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY), que pueden escalar el elemento `VisualElement` de forma diferente en dirección horizontal y vertical. Estas propiedades se pueden animar con la clase [`Animation`](xref:Xamarin.Forms.Animation) . Para obtener más información, vea [animaciones personalizadas en Xamarin. Forms](custom.md).

### <a name="relative-scaling"></a>Escalado relativo

En el ejemplo de código siguiente se muestra cómo utilizar el método [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para animar la propiedad [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) de un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.RelScaleTo (2, 2000);
```

Este código anima la [`Image`](xref:Xamarin.Forms.Image) instancia escalando hasta dos veces su tamaño en más de 2 segundos (2000 milisegundos). El método [`RelScaleTo`](xref:Xamarin.Forms.ViewExtensions.RelScaleTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) obtiene el valor actual de la propiedad [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) para el inicio de la animación y, a continuación, se escala desde ese valor hasta el valor más el primer argumento (2). Esto garantiza que cada animación siempre será un escala de 2 desde la posición inicial.

### <a name="scaling-and-rotation-with-anchors"></a>Escala y giro con delimitadores

Las propiedades [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) y [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) establecen el centro del escalado o la rotación de las propiedades [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) y [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) . Por lo tanto, sus valores también afectan a los métodos [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) y [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) .

Dado un [`Image`](xref:Xamarin.Forms.Image) que se ha colocado en el centro de un diseño, en el ejemplo de código siguiente se muestra cómo se gira la imagen alrededor del centro del diseño estableciendo su propiedad [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) :

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

Para girar la instancia de [`Image`](xref:Xamarin.Forms.Image) alrededor del centro del diseño, las propiedades [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) y [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) deben establecerse en valores relativos al ancho y alto del `Image`. En este ejemplo, el centro del `Image` se define para que esté en el centro del diseño, por lo que el valor predeterminado de `AnchorX` de 0,5 no requiere un cambio. Sin embargo, la propiedad `AnchorY` se define de forma que sea un valor desde la parte superior del `Image` hasta el punto central del diseño. Esto garantiza que el `Image` realiza un giro completo de 360 grados alrededor del punto central del diseño, tal como se muestra en las siguientes capturas de pantallas:

![](simple-images/rotate-anchors.png "Rotation Animation with Anchors")

### <a name="translation"></a>Conversión

En el ejemplo de código siguiente se muestra cómo usar el método [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) para animar las propiedades [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) y [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) de un [`Image`](xref:Xamarin.Forms.Image):

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Este código anima la instancia de [`Image`](xref:Xamarin.Forms.Image) mediante su traducción horizontal y vertical en un segundo (1000 milisegundos). El método [`TranslateTo`](xref:Xamarin.Forms.ViewExtensions.TranslateTo(Xamarin.Forms.VisualElement,System.Double,System.Double,System.UInt32,Xamarin.Forms.Easing)) traduce simultáneamente la imagen 100 píxeles a la izquierda y 100 píxeles hacia arriba. Esto se debe a que el primer y el segundo argumento son números negativos. Proporcionar números positivos traduciría la imagen a la derecha y hacia abajo.

Las capturas de pantallas siguientes muestran la traducción en curso en cada plataforma:

![](simple-images/translateto.png "Translation Animation")

> [!NOTE]
> Si un elemento se coloca inicialmente fuera de la pantalla y, a continuación, se convierte en la pantalla, después de la conversión, el diseño de entrada del elemento permanece fuera de la pantalla y el usuario no puede interactuar con él. Por lo tanto, se recomienda disponer de una vista en su posición final y, a continuación, realizar las traducciones necesarias.

### <a name="fading"></a>Corrección selectiva

En el ejemplo de código siguiente se muestra cómo utilizar el método [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) para animar la propiedad [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) de un [`Image`](xref:Xamarin.Forms.Image):

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Este código anima la instancia de [`Image`](xref:Xamarin.Forms.Image) mediante la atenuación en más de 4 segundos (4000 milisegundos). El método [`FadeTo`](xref:Xamarin.Forms.ViewExtensions.FadeTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) obtiene el valor de propiedad [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) actual para el inicio de la animación y, a continuación, se atenúa desde ese valor hasta su primer argumento (1).

Las capturas de pantallas siguientes muestran el fundido en curso en cada plataforma:

![](simple-images/fadeto.png "Fading Animation")

<a name="compound" />

## <a name="compound-animations"></a>Animaciones compuestas

Una animación compuesta es una combinación secuencial de animaciones y se puede crear con el operador `await`, como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

En este ejemplo, el [`Image`](xref:Xamarin.Forms.Image) se traduce en 6 segundos (6000 milisegundos). La conversión del `Image` usa cinco animaciones, con el operador `await` que indica que cada animación se ejecuta secuencialmente. Por lo tanto, los métodos de animación subsiguientes se ejecutan después de que el método anterior se haya completado.

<a name="composite" />

## <a name="composite-animations"></a>Animaciones compuestas

Una animación compuesta es una combinación de animaciones en las que dos o más animaciones se ejecutan simultáneamente. Las animaciones compuestas se pueden crear mezclando animaciones esperadas y no esperadas, tal y como se muestra en el ejemplo de código siguiente:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

En este ejemplo, el [`Image`](xref:Xamarin.Forms.Image) se escala y se gira simultáneamente en 4 segundos (4000 milisegundos). El escalado del `Image` usa dos animaciones secuenciales que se producen al mismo tiempo que la rotación. El método [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) se ejecuta sin un operador `await` y vuelve inmediatamente, con la primera [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) animación a partir de entonces. El operador `await` en la primera llamada al método `ScaleTo` retrasa la segunda llamada al método `ScaleTo` hasta que se haya completado la primera llamada al método `ScaleTo`. Llegados a este punto, la animación `RotateTo` está completada en medio y el `Image` se girará 180 grados. Durante los últimos 2 segundos (2000 milisegundos), el segundo `ScaleTo` animación y la animación de `RotateTo` se completan.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Ejecutar varios métodos asincrónicos simultáneamente

Los métodos `Task.WhenAny` y `Task.WhenAll` de `static` se usan para ejecutar varios métodos asincrónicos simultáneamente y, por tanto, se pueden usar para crear animaciones compuestas. Ambos métodos devuelven un objeto `Task` y aceptan una colección de métodos que devuelven un objeto `Task`. El método `Task.WhenAny` se completa cuando se completa la ejecución de cualquier método de su colección, como se muestra en el ejemplo de código siguiente:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

En este ejemplo, la llamada al método `Task.WhenAny` contiene dos tareas. La primera tarea gira la imagen en más de 4 segundos (4000 milisegundos) y la segunda tarea escala la imagen en más de 2 segundos (2000 milisegundos). Cuando se completa la segunda tarea, se completa la llamada al método `Task.WhenAny`. Sin embargo, aunque el método de [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)) todavía se esté ejecutando, el segundo método de [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) puede comenzar.

El método `Task.WhenAll` se completa cuando se completan todos los métodos de su colección, como se muestra en el ejemplo de código siguiente:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

En este ejemplo, la llamada al método `Task.WhenAll` contiene tres tareas, cada una de las cuales se ejecuta en 10 minutos. Cada `Task` realiza un número diferente de giros de 360 grados: 307 giros para [`RotateTo`](xref:Xamarin.Forms.ViewExtensions.RotateTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)), 251 rotaciones para [`RotateXTo`](xref:Xamarin.Forms.ViewExtensions.RotateXTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing))y rotaciones 199 para [`RotateYTo`](xref:Xamarin.Forms.ViewExtensions.RotateYTo(Xamarin.Forms.VisualElement,System.Double,System.UInt32,Xamarin.Forms.Easing)). Estos valores son números primos, lo que garantiza que los giros no estén sincronizados y, por tanto, no producirán patrones repetitivos.

En las siguientes capturas de pantallas se muestran varias rotaciones en curso en cada plataforma:

![](simple-images/multiple-rotations.png "Composite Animation")

## <a name="canceling-animations"></a>Cancelar animaciones

Una aplicación puede cancelar una o varias animaciones con una llamada al método `static` [`ViewExtensions.CancelAnimations`](xref:Xamarin.Forms.ViewExtensions.CancelAnimations(Xamarin.Forms.VisualElement)) , tal y como se muestra en el ejemplo de código siguiente:

```csharp
ViewExtensions.CancelAnimations (image);
```

Esto cancelará inmediatamente todas las animaciones que se están ejecutando actualmente en la instancia de [`Image`](xref:Xamarin.Forms.Image) .

## <a name="summary"></a>Resumen

En este artículo se ha mostrado la creación y cancelación de animaciones con la clase [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) . Esta clase proporciona métodos de extensión que se pueden usar para construir animaciones simples que giran, escalan, traducen y atenúan [`VisualElement`](xref:Xamarin.Forms.VisualElement) instancias.

## <a name="related-links"></a>Vínculos relacionados

- [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)
- [Animación básica (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
