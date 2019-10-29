---
title: TextKit en Xamarin. iOS
description: En este documento se describe cómo usar TextKit en Xamarin. iOS. TextKit proporciona características de representación y diseño de texto eficaces.
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 6c1dee464de1f7ba708b1f7d60affc1616e71ee9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031418"
---
# <a name="textkit-in-xamarinios"></a>TextKit en Xamarin. iOS

TextKit es una nueva API que ofrece características de representación y diseño de texto eficaces. Se basa en el marco de texto básico de nivel inferior, pero es mucho más fácil de usar que el texto principal.

Para que las características de TextKit estén disponibles para los controles estándar, se han vuelto a implementar varios controles de texto de iOS para usar TextKit, entre los que se incluyen:

- UITextView
- UITextField
- UILabel

## <a name="architecture"></a>Arquitectura

TextKit proporciona una arquitectura en capas que separa el almacenamiento de texto del diseño y la presentación, incluidas las clases siguientes:

- `NSTextContainer`: proporciona el sistema de coordenadas y la geometría que se usan para el diseño de texto.
- `NSLayoutManager`: diseña texto convirtiéndolo en glifos.
- `NSTextStorage`: contiene los datos de texto, así como las actualizaciones de las propiedades de texto por lotes. Todas las actualizaciones por lotes se entregan al administrador de diseño para el procesamiento real de los cambios, como volver a calcular el diseño y volver a dibujar el texto.

Estas tres clases se aplican a una vista que representa el texto. Las vistas de control de texto integradas, como `UITextView`, `UITextField`y `UILabel` ya las tienen establecidas, pero también se pueden crear y aplicar a cualquier instancia de `UIView`.

En la ilustración siguiente se muestra esta arquitectura:

 ![](textkit-images/textkitarch.png "This figure illustrates the TextKit architecture")

## <a name="text-storage-and-attributes"></a>Atributos y almacenamiento de texto

La clase `NSTextStorage` contiene el texto que se muestra en una vista. También se comunican los cambios realizados en el texto, como los cambios en los caracteres o sus atributos, en el administrador de diseño para su presentación. `NSTextStorage` hereda de `MSMutableAttributed` cadena, lo que permite especificar los cambios en los atributos de texto en lotes entre las llamadas a `BeginEditing` y `EndEditing`.

Por ejemplo, el siguiente fragmento de código especifica un cambio en los colores de primer plano y de fondo, respectivamente, y tiene como destino intervalos determinados:

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

Una vez que se llama a `EndEditing`, los cambios se envían al administrador de diseño, que a su vez realiza los cálculos de diseño y representación necesarios para el texto que se va a mostrar en la vista.

## <a name="layout-with-exclusion-path"></a>Diseño con ruta de exclusión

TextKit también admite el diseño y permite escenarios complejos como texto de varias columnas y flujo de texto alrededor de rutas de acceso especificadas denominadas *rutas de exclusión*. Las rutas de exclusión se aplican al contenedor de texto, que modifica la geometría del diseño del texto, lo que hace que el texto fluya alrededor de las rutas de acceso especificadas.

Para agregar una ruta de exclusión es necesario establecer la propiedad `ExclusionPaths` en el administrador de diseño. Establecer esta propiedad hace que el administrador de diseño invalide el diseño del texto y fluya el texto alrededor de la ruta de exclusión.

### <a name="exclusion-based-on-a-cgpath"></a>Exclusión basada en un CGPath

Considere la siguiente implementación de subclase `UITextView`:

```csharp
public class ExclusionPathView : UITextView
{
    CGPath exclusionPath;
    CGPoint initialPoint;
    CGPoint latestPoint;
    UIBezierPath bezierPath;

    public ExclusionPathView (string text)
    {
        Text = text;
        ContentInset = new UIEdgeInsets (20, 0, 0, 0);
        BackgroundColor = UIColor.White;
        exclusionPath = new CGPath ();
        bezierPath = UIBezierPath.Create ();

        LayoutManager.AllowsNonContiguousLayout = false;
    }

    public override void TouchesBegan (NSSet touches, UIEvent evt)
    {
        base.TouchesBegan (touches, evt);

        var touch = touches.AnyObject as UITouch;

        if (touch != null) {
            initialPoint = touch.LocationInView (this);
        }
    }

    public override void TouchesMoved (NSSet touches, UIEvent evt)
    {
        base.TouchesMoved (touches, evt);

        UITouch touch = touches.AnyObject as UITouch;

        if (touch != null) {
            latestPoint = touch.LocationInView (this);
            SetNeedsDisplay ();
        }
    }

    public override void TouchesEnded (NSSet touches, UIEvent evt)
    {
        base.TouchesEnded (touches, evt);

        bezierPath.CGPath = exclusionPath;
        TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
    }

    public override void Draw (CGRect rect)
    {
        base.Draw (rect);

        if (!initialPoint.IsEmpty) {

            using (var g = UIGraphics.GetCurrentContext ()) {

                g.SetLineWidth (4);
                UIColor.Blue.SetStroke ();

                if (exclusionPath.IsEmpty) {
                    exclusionPath.AddLines (new CGPoint[] { initialPoint, latestPoint });
                } else {
                    exclusionPath.AddLineToPoint (latestPoint);
                }

                g.AddPath (exclusionPath);
                g.DrawPath (CGPathDrawingMode.Stroke);
            }
        }
    }
}
```

Este código agrega compatibilidad para dibujar en la vista de texto mediante gráficos principales. Dado que la clase `UITextView` se ha creado para usar TextKit para su representación y presentación de texto, puede usar todas las características de TextKit, como establecer rutas de exclusión.

> [!IMPORTANT]
> En este ejemplo se subclases `UITextView` para agregar compatibilidad con el dibujo táctil. La subclase `UITextView` no es necesaria para obtener las características de TextKit.

Después de que el usuario dibuje en la vista de texto, la `CGPath` dibujada se aplica a una instancia de `UIBezierPath` mediante el establecimiento de la propiedad `UIBezierPath.CGPath`:

```csharp
bezierPath.CGPath = exclusionPath;
```

La actualización de la siguiente línea de código hace que el diseño de texto se actualice en torno a la ruta de acceso:

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

En la captura de pantalla siguiente se muestra cómo cambia el diseño del texto para fluir en torno a la ruta de acceso dibujada:

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")-->
![](textkit-images/exclusionpath2.png "This screenshot illustrates how the text layout changes to flow around the drawn path")

Observe que en este caso la propiedad `AllowsNonContiguousLayout` del administrador de diseño está establecida en false. Esto hace que el diseño se vuelva a calcular para todos los casos en los que el texto cambie. Si se establece en true, el rendimiento puede mejorar si se evita una actualización de diseño completo, especialmente en el caso de documentos grandes. Sin embargo, si se establece `AllowsNonContiguousLayout` en true, se evitará que la ruta de exclusión actualice el diseño en algunas circunstancias, por ejemplo, si el texto se escribe en tiempo de ejecución sin un retorno de carro final antes de que se establezca la ruta de acceso.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a iOS 7 (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [Introducción a la interfaz de usuario de iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md)
