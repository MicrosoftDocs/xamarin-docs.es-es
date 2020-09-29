---
title: Seguimiento de dedo multitáctil en Xamarin. Android
description: En este tema se muestra cómo realizar un seguimiento de los eventos táctiles de varios dedos
ms.prod: xamarin
ms.assetid: 048D51F9-BD6C-4B44-8C53-CCEF276FC5CC
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/25/2018
ms.openlocfilehash: 8c2e06d2700cd7b61c16fc993d807ca4d042a063
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455123"
---
# <a name="multi-touch-finger-tracking"></a>Seguimiento de dedo multitáctil

_En este tema se muestra cómo realizar un seguimiento de los eventos táctiles de varios dedos_

Hay ocasiones en las que una aplicación multitáctil necesita realizar un seguimiento de los dedos individuales mientras se mueven simultáneamente en la pantalla. Una aplicación típica es un programa de pintado de dedos. Quiere que el usuario pueda dibujar con un solo dedo, pero también para dibujar varios dedos a la vez. A medida que el programa procesa varios eventos táctiles, debe distinguir qué eventos corresponden a cada dedo. Android proporciona un código de identificador para este propósito, pero obtener y controlar ese código puede ser un poco complicado.

Para todos los eventos asociados a un dedo determinado, el código de identificador sigue siendo el mismo. El código de identificador se asigna cuando un dedo toca primero la pantalla y deja de ser válido después de que el dedo se levante de la pantalla.
Estos códigos de identificador suelen ser enteros muy pequeños y Android los vuelve a usar para eventos de toque posteriores.

Casi siempre, un programa que realiza un seguimiento de los dedos individuales mantiene un diccionario para el seguimiento táctil. La clave del diccionario es el código de ID. que identifica un dedo determinado. El valor del diccionario depende de la aplicación. En el programa de [pintura](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) , cada trazo de dedo (de toque a lanzamiento) se asocia a un objeto que contiene toda la información necesaria para representar la línea dibujada con ese dedo. El programa define una `FingerPaintPolyline` clase pequeña para este propósito:

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

Cada Polyline tiene un color, un ancho de trazo y un [`Path`](xref:Android.Graphics.Path) objeto de gráficos Android para acumular y representar varios puntos de la línea mientras se dibujan.

El resto del código que se muestra a continuación se encuentra en un `View` derivado denominado `FingerPaintCanvasView` . Esa clase mantiene un diccionario de objetos de tipo `FingerPaintPolyline` en el momento en el que se dibujan activamente con uno o varios dedos:

```csharp
Dictionary<int, FingerPaintPolyline> inProgressPolylines = new Dictionary<int, FingerPaintPolyline>();
```

Este diccionario permite a la vista obtener rápidamente la `FingerPaintPolyline` información asociada a un dedo determinado.

La `FingerPaintCanvasView` clase también mantiene un `List` objeto para las polilíneas que se han completado:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Los objetos de esta `List` se encuentran en el mismo orden en que se dibujaron.

`FingerPaintCanvasView` invalida dos métodos definidos por `View` : [`OnDraw`](xref:Android.Views.View.OnDraw*)
y [`OnTouchEvent`](xref:Android.Views.View.OnTouchEvent*).
En su `OnDraw` invalidación, la vista dibuja las polilíneas completadas y, a continuación, dibuja las polilíneas en curso.

La invalidación del `OnTouchEvent` método comienza obteniendo un `pointerIndex` valor de la `ActionIndex` propiedad. Este `ActionIndex` valor distingue entre varios dedos, pero no es coherente en varios eventos. Por ese motivo, se usa `pointerIndex` para obtener el valor del puntero `id` del `GetPointerId` método. Este identificador *es* coherente en varios eventos:

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

Observe que la invalidación utiliza la `ActionMasked` propiedad en la `switch` instrucción en lugar de la `Action` propiedad. Aquí se detallan los motivos:

Cuando se trabaja con multi-touch, la `Action` propiedad tiene un valor de `MotionEventsAction.Down` para que el primer dedo toque la pantalla y, a continuación, los valores de `Pointer2Down` y `Pointer3Down` como segundo y tercero tocan también la pantalla. A medida que los dedos cuarto y quinto hacen contacto, la `Action` propiedad tiene valores numéricos que no se corresponden con los miembros de la `MotionEventsAction` enumeración. Debe examinar los valores de las marcas de bits en los valores para interpretar lo que significan.

Del mismo modo, cuando los dedos dejen el contacto con la pantalla, la `Action` propiedad tiene los valores `Pointer2Up` y `Pointer3Up` para los dedos segundo y tercero, y `Up` para el primer dedo.

La `ActionMasked` propiedad toma un número de valores menor porque está diseñado para usarse junto con la `ActionIndex` propiedad para diferenciar entre varios dedos. Cuando los dedos tocan la pantalla, la propiedad solo puede ser igual `MotionEventActions.Down` para el primer dedo y `PointerDown` para los dedos posteriores. Cuando los dedos salen de la pantalla, `ActionMasked` tiene valores de `Pointer1Up` para los dedos posteriores y `Up` para el primer dedo.

Cuando `ActionMasked` se usa, `ActionIndex` distingue entre los dedos siguientes para tocar y salir de la pantalla, pero normalmente no es necesario usar ese valor excepto como argumento para otros métodos del `MotionEvent` objeto. En el caso de la funcionalidad multitáctil, se llama a uno de los más importantes de estos métodos `GetPointerId` en el código anterior. Este método devuelve un valor que puede usar para que una clave de diccionario asocie eventos determinados a los dedos.

La `OnTouchEvent` invalidación en el programa [pintura](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) procesa los `MotionEventActions.Down` eventos y de `PointerDown` forma idéntica creando un nuevo `FingerPaintPolyline` objeto y agregándolo al diccionario:

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

Observe que `pointerIndex` también se usa para obtener la posición del dedo dentro de la vista. Toda la información táctil está asociada con el `pointerIndex` valor. `id`Identifica de forma única los dedos en varios mensajes, por lo que se usa para crear la entrada del diccionario.

Del mismo modo, la `OnTouchEvent` invalidación también controla `MotionEventActions.Up` y de `Pointer1Up` forma idéntica mediante la transferencia de la Polyline completada a la colección para que `completedPolylines` se puedan dibujar durante la `OnDraw` invalidación. El código también quita la `id` entrada del diccionario:

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

Entre los eventos Down y up, normalmente hay muchos `MotionEventActions.Move` eventos. Estas se agrupan en una sola llamada a `OnTouchEvent` , y deben administrarse de manera diferente a `Down` los `Up` eventos y. `pointerIndex`Se debe omitir el valor obtenido anteriormente de la `ActionIndex` propiedad. En su lugar, el método debe obtener varios `pointerIndex` valores mediante un bucle entre 0 y la `PointerCount` propiedad y, a continuación, obtener una `id` para cada uno de esos `pointerIndex` valores:

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

Este tipo de procesamiento permite que el programa de [pintura](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint) realice un seguimiento de los dedos individuales y dibuje los resultados en la pantalla:

[![Ejemplo de captura de pantalla del ejemplo de pintura](touch-tracking-images/image01.png)](touch-tracking-images/image01.png#lightbox)

Ahora ha visto cómo puede realizar el seguimiento de los dedos individuales en la pantalla y distinguirlos.

## <a name="related-links"></a>Vínculos relacionados

- [Guía de Xamarin iOS equivalente](~/ios/app-fundamentals/touch/touch-tracking.md)
- [Pintura (ejemplo)](/samples/xamarin/monodroid-samples/applicationfundamentals-fingerpaint)