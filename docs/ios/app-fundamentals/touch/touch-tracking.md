---
title: Seguimiento de dedo multitáctil en Xamarin. iOS
description: En este documento se describe cómo realizar un seguimiento de los dedos individuales en movimientos multitáctiles en una aplicación de Xamarin. iOS. Se centra en un ejemplo de aplicación de pintado de dedos.
ms.prod: xamarin
ms.assetid: 48E8B20D-0833-43D2-976A-0605DDB386E3
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: eca8a1bdc3c60ae4dbaabf022a9dbe91f7e30402
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767040"
---
# <a name="multi-touch-finger-tracking-in-xamarinios"></a>Seguimiento de dedo multitáctil en Xamarin. iOS

_En este documento se muestra cómo realizar un seguimiento de los eventos táctiles de varios dedos_

Hay ocasiones en las que una aplicación multitáctil necesita realizar un seguimiento de los dedos individuales mientras se mueven simultáneamente en la pantalla. Una aplicación típica es un programa de pintado de dedos. Quiere que el usuario pueda dibujar con un solo dedo, pero también para dibujar varios dedos a la vez. A medida que el programa procesa varios eventos táctiles, debe distinguir entre estos dedos.

Cuando un dedo toca primero la pantalla, iOS crea un [`UITouch`](xref:UIKit.UITouch) objeto para ese dedo. Este objeto se mantiene igual que el dedo se mueve en la pantalla y, a continuación, se eleva de la pantalla, momento en el que se elimina el objeto. Para realizar un seguimiento de los dedos, un programa debe evitar `UITouch` almacenar este objeto directamente. En su lugar, puede usar la [`Handle`](xref:Foundation.NSObject.Handle) propiedad de tipo `IntPtr` para identificar de forma única `UITouch` estos objetos.

Casi siempre, un programa que realiza un seguimiento de los dedos individuales mantiene un diccionario para el seguimiento táctil. Para un programa iOS, la clave del diccionario es `Handle` el valor que identifica un dedo determinado. El valor del diccionario depende de la aplicación. En el programa de [pintura](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) , cada trazo de dedo (de toque a lanzamiento) se asocia a un objeto que contiene toda la información necesaria para representar la línea dibujada con ese dedo. El programa define una clase `FingerPaintPolyline` pequeña para este propósito:

```csharp
class FingerPaintPolyline
{
    public FingerPaintPolyline()
    {
        Path = new CGPath();
    }

    public CGColor Color { set; get; }

    public float StrokeWidth { set; get; }

    public CGPath Path { private set; get; }
}
```

Cada Polyline tiene un color, un ancho de trazo y un objeto de [`CGPath`](xref:CoreGraphics.CGPath) gráficos de iOS para acumular y representar varios puntos de la línea a medida que se dibujan.

Todo el resto del código que se muestra a continuación se encuentra `UIView` en un `FingerPaintCanvasView`derivado denominado. Esa clase mantiene un diccionario de objetos de tipo `FingerPaintPolyline` en el momento en el que se dibujan activamente con uno o varios dedos:

```csharp
Dictionary<IntPtr, FingerPaintPolyline> inProgressPolylines = new Dictionary<IntPtr, FingerPaintPolyline>();
```

Este diccionario permite a la vista obtener rápidamente la `FingerPaintPolyline` información asociada a cada dedo en función de `Handle` la propiedad del `UITouch` objeto.

La `FingerPaintCanvasView` clase también mantiene un `List` objeto para las polilíneas que se han completado:

```csharp
List<FingerPaintPolyline> completedPolylines = new List<FingerPaintPolyline>();
```

Los objetos de esta `List` se encuentran en el mismo orden en que se dibujaron.

`FingerPaintCanvasView`invalida cinco métodos definidos por `View`:

- [`TouchesBegan`](xref:UIKit.UIResponder.TouchesBegan(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesMoved`](xref:UIKit.UIResponder.TouchesMoved(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesEnded`](xref:UIKit.UIResponder.TouchesEnded(Foundation.NSSet,UIKit.UIEvent))
- [`TouchesCancelled`](xref:UIKit.UIResponder.TouchesCancelled(Foundation.NSSet,UIKit.UIEvent))
- [`Draw`](xref:UIKit.UIView.Draw(CoreGraphics.CGRect))

Las distintas `Touches` invalidaciones acumulan los puntos que componen las polilíneas.

La invalidación [`Draw`] dibuja las polilíneas completadas y después las polilíneas en curso:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (CGContext context = UIGraphics.GetCurrentContext())
    {
        // Stroke settings
        context.SetLineCap(CGLineCap.Round);
        context.SetLineJoin(CGLineJoin.Round);

        // Draw the completed polylines
        foreach (FingerPaintPolyline polyline in completedPolylines)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }

        // Draw the in-progress polylines
        foreach (FingerPaintPolyline polyline in inProgressPolylines.Values)
        {
            context.SetStrokeColor(polyline.Color);
            context.SetLineWidth(polyline.StrokeWidth);
            context.AddPath(polyline.Path);
            context.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

Cada una de `Touches` las invalidaciones puede notificar potencialmente las acciones de varios dedos, indicadas `UITouch` por uno o más `touches` objetos almacenados en el argumento para el método. El `TouchesBegan` bucle invalida a través de estos objetos. Para cada `UITouch` objeto, el método crea e inicializa un nuevo `FingerPaintPolyline` objeto, incluido el almacenamiento de la ubicación inicial del dedo obtenido a partir `LocationInView` del método. Este `FingerPaintPolyline` objeto se agrega `InProgressPolylines` al `Handle` diccionario`UITouch` utilizando la propiedad del objeto como clave de diccionario:

```csharp
public override void TouchesBegan(NSSet touches, UIEvent evt)
{
    base.TouchesBegan(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Create a FingerPaintPolyline, set the initial point, and store it
        FingerPaintPolyline polyline = new FingerPaintPolyline
        {
            Color = StrokeColor,
            StrokeWidth = StrokeWidth,
        };

        polyline.Path.MoveToPoint(touch.LocationInView(this));
        inProgressPolylines.Add(touch.Handle, polyline);
    }
    SetNeedsDisplay();
}
```

El método finaliza `SetNeedsDisplay` llamando a para generar una llamada a la `Draw` invalidación y actualizar la pantalla.

Cuando el dedo o los dedos se mueven en la `View` pantalla, obtiene varias llamadas `TouchesMoved` a su invalidación. Esta invalidación se repite de `UITouch` forma similar a través `touches` de los objetos almacenados en el argumento y agrega la ubicación actual del dedo a la ruta de acceso de los gráficos:

```csharp
public override void TouchesMoved(NSSet touches, UIEvent evt)
{
    base.TouchesMoved(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Add point to path
        inProgressPolylines[touch.Handle].Path.AddLineToPoint(touch.LocationInView(this));
    }
    SetNeedsDisplay();
}
```

La `touches` colección contiene solo los `UITouch` objetos de los dedos que se han despasado desde la última `TouchesBegan` llamada `TouchesMoved`a o a. Si alguna vez necesita `UITouch` objetos correspondientes a *todos* los dedos que se encuentran actualmente en contacto con la pantalla, la información está `AllTouches` disponible a través `UIEvent` de la propiedad del argumento en el método.

La `TouchesEnded` invalidación tiene dos trabajos. Debe agregar el último punto a la ruta de acceso de los gráficos y `FingerPaintPolyline` transferir el objeto `inProgressPolylines` del diccionario a `completedPolylines` la lista:

```csharp
public override void TouchesEnded(NSSet touches, UIEvent evt)
{
    base.TouchesEnded(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        // Get polyline from dictionary and remove it from dictionary
        FingerPaintPolyline polyline = inProgressPolylines[touch.Handle];
        inProgressPolylines.Remove(touch.Handle);

        // Add final point to path and save with completed polylines
        polyline.Path.AddLineToPoint(touch.LocationInView(this));
        completedPolylines.Add(polyline);
    }
    SetNeedsDisplay();
}
```

La `TouchesCancelled` invalidación se controla simplemente abandonando el `FingerPaintPolyline` objeto en el diccionario:

```csharp
public override void TouchesCancelled(NSSet touches, UIEvent evt)
{
    base.TouchesCancelled(touches, evt);

    foreach (UITouch touch in touches.Cast<UITouch>())
    {
        inProgressPolylines.Remove(touch.Handle);
    }
    SetNeedsDisplay();
}
```

Por completo, este procesamiento permite que el programa de [pintura](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint) realice un seguimiento de los dedos individuales y dibuje los resultados en la pantalla:

[![](touch-tracking-images/image01.png "Seguimiento de dedos individuales y dibujo de los resultados en la pantalla")](touch-tracking-images/image01.png#lightbox)

Ahora ha visto cómo puede realizar el seguimiento de los dedos individuales en la pantalla y distinguirlos.

## <a name="related-links"></a>Vínculos relacionados

- [Guía de Xamarin Android equivalente](~/android/app-fundamentals/touch/touch-tracking.md)
- [Pintura (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-fingerpaint)
