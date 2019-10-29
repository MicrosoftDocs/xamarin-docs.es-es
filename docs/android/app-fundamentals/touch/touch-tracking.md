---
title: Seguimiento de dedo multitáctil en Xamarin. Android
description: En este tema se muestra cómo realizar un seguimiento de los eventos táctiles de varios dedos
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: f960c3cec90bd331f5a1433a869c7720b40c9680
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024278"
---
# <a name="multi-touch-finger-tracking"></a>Seguimiento de dedo multitáctil

_En este tema se muestra cómo realizar un seguimiento de los eventos táctiles de varios dedos_

Hay ocasiones en las que una aplicación multitáctil necesita realizar un seguimiento de los dedos individuales mientras se mueven simultáneamente en la pantalla. Una aplicación típica es un programa de pintado de dedos. Quiere que el usuario pueda dibujar con un solo dedo, pero también para dibujar varios dedos a la vez. A medida que el programa procesa varios eventos táctiles, debe distinguir qué eventos corresponden a cada dedo. Android proporciona un código de identificador para este propósito, pero obtener y controlar ese código puede ser un poco complicado.

Para todos los eventos asociados a un dedo determinado, el código de identificador sigue siendo el mismo. El código de identificador se asigna cuando un dedo toca primero la pantalla y deja de ser válido después de que el dedo se levante de la pantalla.
Estos códigos de identificador suelen ser enteros muy pequeños y Android los vuelve a usar para eventos de toque posteriores.

Casi siempre, un programa que realiza un seguimiento de los dedos individuales mantiene un diccionario para el seguimiento táctil. La clave del diccionario es el código de ID. que identifica un dedo determinado. El valor del diccionario depende de la aplicación. En el programa de [pintura](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) , cada trazo de dedo (de toque a lanzamiento) se asocia a un objeto que contiene toda la información necesaria para representar la línea dibujada con ese dedo. El programa define una clase de `FingerPaintPolyline` pequeña para este propósito:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new Path();
    }

    public Color Color { set; get; }

    public float StrokeWidth { set; get; }

    public Path Path { private set; get; }
}
```

Cada Polyline tiene un color, un ancho de trazo y un objeto de gráficos Android [`Path`](xref:Android.Graphics.Path) para acumular y representar varios puntos de la línea mientras se dibujan.

El resto del código que se muestra a continuación se encuentra en un `View` derivado denominado `FingerPaintCanvasView`. Esa clase mantiene un diccionario de objetos de tipo `FingerPaintPolyline` durante el tiempo en el que uno o varios dedos dibujan activamente:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Este diccionario permite a la vista obtener rápidamente la información `FingerPaintPolyline` asociada a un dedo determinado.

La clase `FingerPaintCanvasView` también mantiene un objeto `List` para las polilíneas que se han completado:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Los objetos de este `List` están en el mismo orden en que se dibujaron.

`FingerPaintCanvasView` invalida dos métodos definidos por `View`: [`OnDraw`](xref:Android.Views.View.OnDraw*)
y [`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*).
En su invalidación de `OnDraw`, la vista dibuja las polilíneas completadas y, a continuación, dibuja las polilíneas en curso.

La invalidación del método `OnTouchEvent` comienza mediante la obtención de un valor `pointerIndex` de la propiedad `ActionIndex`. Esta `ActionIndex` valor distingue entre varios dedos, pero no es coherente en varios eventos. Por ese motivo, use el `pointerIndex` para obtener el puntero `id` valor del método `GetPointerId`. Este identificador *es* coherente en varios eventos:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        // ...
    }

    // Invalidate to update the view
    Invalidate();

    // Request continued touch input
    return true;
}
```

Observe que la invalidación utiliza la propiedad `ActionMasked` en la instrucción `switch` en lugar de la propiedad `Action`. El motivo es el siguiente:

Cuando se trabaja con multi-touch, la propiedad `Action` tiene un valor de `MotionEventsAction.Down` para que el primer dedo toque la pantalla y, a continuación, los valores de `Pointer2Down` y `Pointer3Down` como el segundo y el tercero también tocan la pantalla. A medida que los dedos cuarto y quinto hacen contacto, la propiedad `Action` tiene valores numéricos que no se corresponden incluso con los miembros de la enumeración `MotionEventsAction`. Debe examinar los valores de las marcas de bits en los valores para interpretar lo que significan.

Del mismo modo, cuando los dedos dejen el contacto con la pantalla, la propiedad `Action` tiene valores de `Pointer2Up` y `Pointer3Up` para el segundo y tercer dedo y `Up` para el primer dedo.

La propiedad `ActionMasked` toma un número de valores menor porque está diseñado para usarse junto con la propiedad `ActionIndex` para diferenciar entre varios dedos. Cuando los dedos tocan la pantalla, la propiedad solo puede ser igual `MotionEventActions.Down` del primer dedo y `PointerDown` para los dedos posteriores. Cuando los dedos salen de la pantalla, `ActionMasked` tiene valores de `Pointer1Up` para los dedos y `Up` siguientes para el primer dedo.

Cuando se usa `ActionMasked`, el `ActionIndex` distingue entre los dedos siguientes para tocar y dejar la pantalla, pero normalmente no es necesario usar ese valor excepto como argumento para otros métodos del objeto `MotionEvent`. En el caso de la funcionalidad multitáctil, uno de los más importantes de estos métodos se `GetPointerId` llama en el código anterior. Este método devuelve un valor que puede usar para que una clave de diccionario asocie eventos determinados a los dedos.

La invalidación de `OnTouchEvent` en el programa de [pintura](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) procesa los eventos `MotionEventActions.Down` y `PointerDown` de la misma forma que crea un nuevo objeto `FingerPaintPolyline` y lo agrega al diccionario:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // Get the pointer index
    int pointerIndex = args.ActionIndex;

    // Get the id to identify a finger over the course of its progress
    int id = args.GetPointerId(pointerIndex);

    // Use ActionMasked here rather than Action to reduce the number of possibilities
    switch (args.ActionMasked)
    {
        case MotionEventActions.Down:
        case MotionEventActions.PointerDown:

            // Create a Polyline, set the initial point, and store it
            FingerPaintPolyline polyline = new FingerPaintPolyline
            {
                Color = StrokeColor,
                StrokeWidth = StrokeWidth
            };

            polyline.Path.MoveTo(args.GetX(pointerIndex),
                                 args.GetY(pointerIndex));

            inProgressPolylines.Add(id, polyline);
            break;
        // ...
    }
    // ...        
}
```

Observe que el `pointerIndex` también se usa para obtener la posición del dedo dentro de la vista. Toda la información táctil está asociada con el valor `pointerIndex`. El `id` identifica de forma única los dedos entre varios mensajes, por lo que se usa para crear la entrada del diccionario.

Del mismo modo, la invalidación de `OnTouchEvent` también controla el `MotionEventActions.Up` y `Pointer1Up` de la misma manera que se transfiere la polilínea completada a la colección de `completedPolylines` para que se puedan dibujar durante la invalidación de `OnDraw`. El código también quita la entrada `id` del diccionario:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Up:
        case MotionEventActions.Pointer1Up:

            inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                args.GetY(pointerIndex));

            // Transfer the in-progress polyline to a completed polyline
            completedPolylines.Add(inProgressPolylines[id]);
            inProgressPolylines.Remove(id);
            break;

        case MotionEventActions.Cancel:
            inProgressPolylines.Remove(id);
            break;
    }
    // ...        
}
```

Ahora para la parte complicada.

Entre los eventos Down y up, normalmente hay muchos `MotionEventActions.Move` eventos. Se agrupan en una única llamada a `OnTouchEvent`, y deben administrarse de manera diferente a los eventos `Down` y `Up`. Se debe omitir el `pointerIndex` valor obtenido anteriormente de la propiedad `ActionIndex`. En su lugar, el método debe obtener varios valores `pointerIndex` mediante un bucle entre 0 y la propiedad `PointerCount` y, a continuación, obtener un `id` para cada uno de esos valores `pointerIndex`:

```csharp
public override bool OnTouchEvent(MotionEvent args)
{
    // ...
    switch (args.ActionMasked)
    {
        // ...
        case MotionEventActions.Move:

            // Multiple Move events are bundled, so handle them differently
            for (pointerIndex = 0; pointerIndex < args.PointerCount; pointerIndex++)
            {
                id = args.GetPointerId(pointerIndex);

                inProgressPolylines[id].Path.LineTo(args.GetX(pointerIndex),
                                                    args.GetY(pointerIndex));
            }
            break;
        // ...
    }
    // ...        
}
```

Este tipo de procesamiento permite que el programa de [pintura](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) realice un seguimiento de los dedos individuales y dibuje los resultados en la pantalla:

[![captura de pantalla de ejemplo del ejemplo de pintura](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Ahora ha visto cómo puede realizar el seguimiento de los dedos individuales en la pantalla y distinguirlos.

## <a name="related-links"></a>Vínculos relacionados

- [Guía de Xamarin iOS equivalente](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Pintura (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)
