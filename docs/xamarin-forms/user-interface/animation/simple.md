---
title: Animaciones simples enXamarin.Forms
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a4644094de9c0fcad8f38b7014426a30263dc66f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137454"
---
# <a name="simple-animations-in-xamarinforms"></a>Animaciones simples enXamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)

_La clase ViewExtensions proporciona métodos de extensión que se pueden usar para construir animaciones simples. En este artículo se muestra cómo crear y cancelar animaciones mediante la clase ViewExtensions._

La [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) clase proporciona los siguientes métodos de extensión que se pueden usar para crear animaciones simples:

- [ `TranslateTo` ] (XREF: Xamarin.Forms . ViewExtensions. translateto ( Xamarin.Forms . VisualElement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Aceleración): anima las [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) propiedades y [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) de un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*)anima la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propiedad de un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- `ScaleXTo`anima la [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) propiedad de un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- `ScaleYTo`anima la [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) propiedad de un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RelScaleTo` ] (XREF: Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . La aceleración)) aplica un aumento incremental animado o una disminución a la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propiedad de [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RotateTo` ] (XREF: Xamarin.Forms . ViewExtensions. Rotato ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . La aceleración)) anima la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RelRotateTo` ] (XREF: Xamarin.Forms . ViewExtensions. RelRotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . La aceleración)) aplica un aumento incremental animado o una disminución a la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RotateXTo` ] (XREF: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . La aceleración)) anima la [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) propiedad de un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `RotateYTo` ] (XREF: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . La aceleración)) anima la [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) propiedad de un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .
- [ `FadeTo` ] (XREF: Xamarin.Forms . ViewExtensions. Desvaneceto ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . La aceleración)) anima la [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) propiedad de un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

De forma predeterminada, cada animación tardará 250 milisegundos. Sin embargo, se puede especificar una duración para cada animación al crear la animación.

La [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) clase también incluye [ `CancelAnimations` ] (XREF: Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . VisualElement)) que se puede usar para cancelar cualquier animación.

> [!NOTE]
> La [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) clase proporciona un [ `LayoutTo` ] (XREF: Xamarin.Forms . ViewExtensions. Layoutto ( Xamarin.Forms . VisualElement, Xamarin.Forms . Rectangle, System. UInt32, Xamarin.Forms . Aceleración)). Sin embargo, este método está pensado para que lo usen los diseños para animar las transiciones entre los Estados de diseño que contienen cambios de tamaño y posición. Por lo tanto, solo lo deben usar las [`Layout`](xref:Xamarin.Forms.Layout) subclases.

Los métodos de extensión de animación de la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) clase son todos asincrónicos y devuelven un `Task<bool>` objeto. El valor devuelto es `false` si la animación se completa y `true` si se cancela la animación. Por lo tanto, los métodos de animación deben usarse normalmente con el `await` operador, lo que permite determinar fácilmente Cuándo se ha completado una animación. Además, se pueden crear animaciones secuenciales con métodos de animación posteriores que se ejecuten después de que se haya completado el método anterior. Para obtener más información, vea [animaciones compuestas](#compound).

Si hay un requisito para permitir que una animación se complete en segundo plano, `await` se puede omitir el operador. En este escenario, los métodos de extensión de animación devolverán rápidamente después de iniciar la animación, con la animación en segundo plano. Esta operación se puede aprovechar al crear animaciones compuestas. Para obtener más información, vea [animaciones compuestas](#composite).

Para obtener más información sobre el `await` operador, vea [información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md).

## <a name="single-animations"></a>Animaciones únicas

Cada método de extensión de [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) implementa una operación de animación única que cambia progresivamente una propiedad de un valor a otro durante un período de tiempo. En esta sección se explora cada operación de animación.

### <a name="rotation"></a>Rotación

En el ejemplo de código siguiente se muestra el uso de [ `RotateTo` ] (XREF: Xamarin.Forms . ViewExtensions. Rotato ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)) para animar la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.RotateTo (360, 2000);
image.Rotation = 0;
```

Este código anima la [`Image`](xref:Xamarin.Forms.Image) instancia rotando hasta 360 grados en 2 segundos (2000 milisegundos). [ `RotateTo` ] (XREF: Xamarin.Forms . ViewExtensions. Rotato ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)) el método obtiene el [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) valor de propiedad actual para el inicio de la animación y, a continuación, lo gira desde ese valor hasta su primer argumento (360). Una vez completada la animación, la propiedad de la imagen [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) se restablece en 0. Esto garantiza que la `Rotation` propiedad no permanezca en 360 una vez finalizada la animación, lo que impediría rotaciones adicionales.

Las capturas de pantallas siguientes muestran la rotación en curso en cada plataforma:

![](simple-images/rotateto.png "Rotation Animation")

> [!NOTE]
> Además de [ `RotateTo` ] (XREF: Xamarin.Forms . ViewExtensions. Rotato ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)), también hay [ `RotateXTo` ] (XREF: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración) y [ `RotateYTo` ] (XREF: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)) que animan las [`RotationX`](xref:Xamarin.Forms.VisualElement.RotationX) [`RotationY`](xref:Xamarin.Forms.VisualElement.RotationY) propiedades y, respectivamente.

### <a name="relative-rotation"></a>Rotación relativa

En el ejemplo de código siguiente se muestra el uso de [ `RelRotateTo` ] (XREF: Xamarin.Forms . ViewExtensions. RelRotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)) para aumentar o disminuir incrementalmente la [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propiedad de un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.RelRotateTo (360, 2000);
```

Este código anima la [`Image`](xref:Xamarin.Forms.Image) instancia rotando 360 grados desde su posición inicial en más de 2 segundos (2000 milisegundos). [ `RelRotateTo` ] (XREF: Xamarin.Forms . ViewExtensions. RelRotateTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)): obtiene el valor de [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) propiedad actual para el inicio de la animación y, a continuación, gira desde ese valor hasta el valor más el primer argumento (360). Esto garantiza que cada animación siempre tendrá una rotación de 360 grados desde la posición inicial. Por lo tanto, si se invoca una nueva animación mientras una animación ya está en curso, se iniciará desde la posición actual y puede acabar en una posición que no sea un incremento de 360 grados.

Las capturas de pantallas siguientes muestran la rotación relativa en curso en cada plataforma:

![](simple-images/relrotateto.png "Relative Rotation Animation")

### <a name="scaling"></a>Ampliación

En el ejemplo de código siguiente se muestra cómo utilizar el [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) método para animar la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propiedad de un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.ScaleTo (2, 2000);
```

Este código anima la [`Image`](xref:Xamarin.Forms.Image) instancia escalando hasta dos veces su tamaño en más de 2 segundos (2000 milisegundos). El [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) método obtiene el valor de [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propiedad actual (valor predeterminado de 1) para el inicio de la animación y, a continuación, se escala desde ese valor hasta su primer argumento (2). Esto tiene el efecto de expandir el tamaño de la imagen al doble de su tamaño.

Las capturas de pantallas siguientes muestran el escalado en curso en cada plataforma:

![](simple-images/scaleto.png "Scaling Animation")

> [!NOTE]
> Además del [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) método, también hay `ScaleXTo` `ScaleYTo` métodos y que animan las [`ScaleX`](xref:Xamarin.Forms.VisualElement.ScaleX) [`ScaleY`](xref:Xamarin.Forms.VisualElement.ScaleY) propiedades y, respectivamente.

### <a name="relative-scaling"></a>Escalado relativo

En el ejemplo de código siguiente se muestra el uso de [ `RelScaleTo` ] (XREF: Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)) para animar la [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propiedad de un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.RelScaleTo (2, 2000);
```

Este código anima la [`Image`](xref:Xamarin.Forms.Image) instancia escalando hasta dos veces su tamaño en más de 2 segundos (2000 milisegundos). [ `RelScaleTo` ] (XREF: Xamarin.Forms . ViewExtensions. RelScaleTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)): obtiene el valor de [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propiedad actual para el inicio de la animación y, a continuación, se escala desde ese valor hasta el valor más el primer argumento (2). Esto garantiza que cada animación siempre será un escala de 2 desde la posición inicial.

### <a name="scaling-and-rotation-with-anchors"></a>Escala y giro con delimitadores

Las [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) propiedades y establecen el centro del escalado o la rotación de las [`Rotation`](xref:Xamarin.Forms.VisualElement.Rotation) [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) propiedades y. Por lo tanto, sus valores también afectan a [ `RotateTo` ] (XREF: Xamarin.Forms . ViewExtensions. Rotato ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)) y [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) métodos.

Dado que se ha [`Image`](xref:Xamarin.Forms.Image) colocado en el centro de un diseño, en el ejemplo de código siguiente se muestra cómo se gira la imagen alrededor del centro del diseño estableciendo su [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) propiedad:

```csharp
double radius = Math.Min(absoluteLayout.Width, absoluteLayout.Height) / 2;
image.AnchorY = radius / image.Height;
await image.RotateTo(360, 2000);
```

Para girar la [`Image`](xref:Xamarin.Forms.Image) instancia alrededor del centro del diseño, las [`AnchorX`](xref:Xamarin.Forms.VisualElement.AnchorX) propiedades y [`AnchorY`](xref:Xamarin.Forms.VisualElement.AnchorY) deben establecerse en valores relativos al ancho y alto del `Image` . En este ejemplo, el centro de `Image` se define para que esté en el centro del diseño, por lo que el `AnchorX` valor predeterminado de 0,5 no requiere un cambio. Sin embargo, la `AnchorY` propiedad se redefine para que sea un valor desde la parte superior del `Image` hasta el punto central del diseño. Esto garantiza que `Image` realiza un giro completo de 360 grados alrededor del punto central del diseño, tal como se muestra en las siguientes capturas de pantallas:

![](simple-images/rotate-anchors.png "Rotation Animation with Anchors")

### <a name="translation"></a>Traducción

En el ejemplo de código siguiente se muestra el uso de [ `TranslateTo` ] (XREF: Xamarin.Forms . ViewExtensions. translateto ( Xamarin.Forms . VisualElement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Aceleración)) para animar las [`TranslationX`](xref:Xamarin.Forms.VisualElement.TranslationX) [`TranslationY`](xref:Xamarin.Forms.VisualElement.TranslationY) propiedades y de un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
await image.TranslateTo (-100, -100, 1000);
```

Este código anima la [`Image`](xref:Xamarin.Forms.Image) instancia mediante su traducción horizontal y vertical en un segundo (1000 milisegundos). [ `TranslateTo` ] (XREF: Xamarin.Forms . ViewExtensions. translateto ( Xamarin.Forms . VisualElement, System. Double, System. Double, System. UInt32, Xamarin.Forms . Aceleración)) convierte simultáneamente la imagen 100 píxeles a la izquierda y 100 píxeles hacia arriba. Esto se debe a que el primer y el segundo argumento son números negativos. Proporcionar números positivos traduciría la imagen a la derecha y hacia abajo.

Las capturas de pantallas siguientes muestran la traducción en curso en cada plataforma:

![](simple-images/translateto.png "Translation Animation")

> [!NOTE]
> Si un elemento se coloca inicialmente fuera de la pantalla y, a continuación, se convierte en la pantalla, después de la conversión, el diseño de entrada del elemento permanece fuera de la pantalla y el usuario no puede interactuar con él. Por lo tanto, se recomienda disponer de una vista en su posición final y, a continuación, realizar las traducciones necesarias.

### <a name="fading"></a>Corrección selectiva

En el ejemplo de código siguiente se muestra el uso de [ `FadeTo` ] (XREF: Xamarin.Forms . ViewExtensions. Desvaneceto ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)) para animar la [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) propiedad de un [`Image`](xref:Xamarin.Forms.Image) :

```csharp
image.Opacity = 0;
await image.FadeTo (1, 4000);
```

Este código anima la [`Image`](xref:Xamarin.Forms.Image) instancia mediante una atenuación en más de 4 segundos (4000 milisegundos). [ `FadeTo` ] (XREF: Xamarin.Forms . ViewExtensions. Desvaneceto ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)) el método obtiene el [`Opacity`](xref:Xamarin.Forms.VisualElement.Opacity) valor de propiedad actual para el inicio de la animación y, a continuación, se atenúa desde ese valor hasta su primer argumento (1).

Las capturas de pantallas siguientes muestran el fundido en curso en cada plataforma:

![](simple-images/fadeto.png "Fading Animation")

<a name="compound" />

## <a name="compound-animations"></a>Animaciones compuestas

Una animación compuesta es una combinación secuencial de animaciones y se puede crear con el `await` operador, como se muestra en el ejemplo de código siguiente:

```csharp
await image.TranslateTo (-100, 0, 1000);    // Move image left
await image.TranslateTo (-100, -100, 1000); // Move image up
await image.TranslateTo (100, 100, 2000);   // Move image diagonally down and right
await image.TranslateTo (0, 100, 1000);     // Move image left
await image.TranslateTo (0, 0, 1000);       // Move image up
```

En este ejemplo, [`Image`](xref:Xamarin.Forms.Image) se traduce en 6 segundos (6000 milisegundos). La conversión de `Image` utiliza cinco animaciones, con el `await` operador que indica que cada animación se ejecuta secuencialmente. Por lo tanto, los métodos de animación subsiguientes se ejecutan después de que el método anterior se haya completado.

<a name="composite" />

## <a name="composite-animations"></a>Animaciones compuestas

Una animación compuesta es una combinación de animaciones en las que dos o más animaciones se ejecutan simultáneamente. Las animaciones compuestas se pueden crear mezclando animaciones esperadas y no esperadas, tal y como se muestra en el ejemplo de código siguiente:

```csharp
image.RotateTo (360, 4000);
await image.ScaleTo (2, 2000);
await image.ScaleTo (1, 2000);
```

En este ejemplo, [`Image`](xref:Xamarin.Forms.Image) se escala y se gira simultáneamente en 4 segundos (4000 milisegundos). El escalado de `Image` usa dos animaciones secuenciales que se producen al mismo tiempo que la rotación. [ `RotateTo` ] (XREF: Xamarin.Forms . ViewExtensions. Rotato ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . El método de aceleración)) se ejecuta sin un `await` operador y se devuelve inmediatamente, con la primera [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) animación a partir de. El `await` operador de la primera `ScaleTo` llamada al método retrasa la segunda llamada al método `ScaleTo` hasta que `ScaleTo` se ha completado la primera llamada al método. En este punto `RotateTo` , la animación se completará a mitad y se `Image` girará a 180 grados. Durante los últimos 2 segundos (2000 milisegundos), la segunda `ScaleTo` animación y la `RotateTo` animación se completan.

### <a name="running-multiple-asynchronous-methods-concurrently"></a>Ejecutar varios métodos asincrónicos simultáneamente

Los `static` `Task.WhenAny` `Task.WhenAll` métodos y se usan para ejecutar varios métodos asincrónicos simultáneamente y, por tanto, se pueden usar para crear animaciones compuestas. Ambos métodos devuelven un `Task` objeto y aceptan una colección de métodos que devuelven un `Task` objeto. El `Task.WhenAny` método se completa cuando se completa la ejecución de cualquier método de su colección, como se muestra en el ejemplo de código siguiente:

```csharp
await Task.WhenAny<bool>
(
  image.RotateTo (360, 4000),
  image.ScaleTo (2, 2000)
);
await image.ScaleTo (1, 2000);
```

En este ejemplo, la `Task.WhenAny` llamada al método contiene dos tareas. La primera tarea gira la imagen en más de 4 segundos (4000 milisegundos) y la segunda tarea escala la imagen en más de 2 segundos (2000 milisegundos). Cuando se completa la segunda tarea, se `Task.WhenAny` completa la llamada al método. Sin embargo, aunque [ `RotateTo` ] (XREF: Xamarin.Forms . ViewExtensions. Rotato ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)) que todavía se está ejecutando, el segundo [`ScaleTo`](xref:Xamarin.Forms.ViewExtensions.ScaleTo*) método puede comenzar.

El `Task.WhenAll` método se completa cuando se completan todos los métodos de su colección, como se muestra en el ejemplo de código siguiente:

```csharp
// 10 minute animation
uint duration = 10 * 60 * 1000;

await Task.WhenAll (
  image.RotateTo (307 * 360, duration),
  image.RotateXTo (251 * 360, duration),
  image.RotateYTo (199 * 360, duration)
);
```

En este ejemplo, la `Task.WhenAll` llamada al método contiene tres tareas, cada una de las cuales se ejecuta en 10 minutos. Cada `Task` una de ellas realiza un número diferente de giros de 360 grados: 307 giros para [ `RotateTo` ] (XREF: Xamarin.Forms . ViewExtensions. Rotato ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración), 251 giros para [ `RotateXTo` ] (XREF: Xamarin.Forms . ViewExtensions. RotateXTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración) y 199 giros para [ `RotateYTo` ] (XREF: Xamarin.Forms . ViewExtensions. RotateYTo ( Xamarin.Forms . VisualElement, System. Double, System. UInt32, Xamarin.Forms . Aceleración)). Estos valores son números primos, lo que garantiza que los giros no estén sincronizados y, por tanto, no producirán patrones repetitivos.

En las siguientes capturas de pantallas se muestran varias rotaciones en curso en cada plataforma:

![](simple-images/multiple-rotations.png "Composite Animation")

## <a name="canceling-animations"></a>Cancelar animaciones

Una aplicación puede cancelar una o varias animaciones con una llamada a `static` [ `ViewExtensions.CancelAnimations` ] (XREF: Xamarin.Forms . ViewExtensions. CancelAnimations ( Xamarin.Forms . VisualElement)), como se muestra en el ejemplo de código siguiente:

```csharp
ViewExtensions.CancelAnimations (image);
```

Esto cancelará inmediatamente todas las animaciones que se están ejecutando actualmente en la [`Image`](xref:Xamarin.Forms.Image) instancia.

## <a name="summary"></a>Resumen

En este artículo se ha mostrado la creación y cancelación de animaciones con la [`ViewExtensions`](xref:Xamarin.Forms.ViewExtensions) clase. Esta clase proporciona métodos de extensión que se pueden usar para construir animaciones simples que giran, escalan, traducen y atenúan [`VisualElement`](xref:Xamarin.Forms.VisualElement) instancias.

## <a name="related-links"></a>Vínculos relacionados

- [Información general sobre la compatibilidad con Async](~/cross-platform/platform/async.md)
- [Animación básica (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-animation-basic)
- [ViewExtensions](xref:Xamarin.Forms.ViewExtensions)
