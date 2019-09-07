---
title: TextKit en Xamarin. iOS
description: En este documento se describe cómo usar TextKit en Xamarin. iOS. TextKit proporciona características de representación y diseño de texto eficaces.
ms.prod: xamarin
ms.assetid: 1D0477E8-CD1E-48A9-B7C8-7CA892069EFF
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: dba65eb11b6ae22a51610804e3a6a73549f4565b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769763"
---
# <a name="textkit-in-xamarinios"></a>TextKit en Xamarin. iOS

TextKit es una nueva API que ofrece características de representación y diseño de texto eficaces. Se basa en el marco de texto básico de nivel inferior, pero es mucho más fácil de usar que el texto principal.

Para que las características de TextKit estén disponibles para los controles estándar, se han vuelto a implementar varios controles de texto de iOS para usar TextKit, entre los que se incluyen:

- UITextView
- UITextField
- UILabel

## <a name="architecture"></a>Arquitectura

TextKit proporciona una arquitectura en capas que separa el almacenamiento de texto del diseño y la presentación, incluidas las clases siguientes:

- `NSTextContainer`: Proporciona el sistema de coordenadas y la geometría que se usan para el diseño de texto.
- `NSLayoutManager`: Diseña texto convirtiéndolo en glifos.
- `NSTextStorage`: Contiene los datos de texto, así como las actualizaciones de las propiedades de texto por lotes. Todas las actualizaciones por lotes se entregan al administrador de diseño para el procesamiento real de los cambios, como volver a calcular el diseño y volver a dibujar el texto.

Estas tres clases se aplican a una vista que representa el texto. Las vistas de control de texto integradas, como `UITextView`, `UITextField`y `UILabel` ya las tienen establecidas, pero también puede crearlas y aplicarlas a cualquier `UIView` instancia.

En la ilustración siguiente se muestra esta arquitectura:

 ![](textkit-images/textkitarch.png "En esta ilustración se muestra la arquitectura de TextKit")

## <a name="text-storage-and-attributes"></a>Atributos y almacenamiento de texto

La `NSTextStorage` clase contiene el texto que se muestra en una vista. También se comunican los cambios realizados en el texto, como los cambios en los caracteres o sus atributos, en el administrador de diseño para su presentación. `NSTextStorage`hereda de `MSMutableAttributed` String, lo que permite que se especifiquen cambios en los atributos de texto en `EndEditing` lotes entre `BeginEditing` las llamadas y.

Por ejemplo, el siguiente fragmento de código especifica un cambio en los colores de primer plano y de fondo, respectivamente, y tiene como destino intervalos determinados:

```csharp
textView.TextStorage.BeginEditing ();
textView.TextStorage.AddAttribute(UIStringAttributeKey.ForegroundColor, UIColor.Green, new NSRange(200, 400));
textView.TextStorage.AddAttribute(UIStringAttributeKey.BackgroundColor, UIColor.Black, new NSRange(210, 300));
textView.TextStorage.EndEditing ();
```

Después `EndEditing` de llamar a, los cambios se envían al administrador de diseño, que a su vez realiza los cálculos de diseño y representación necesarios para que el texto se muestre en la vista.

## <a name="layout-with-exclusion-path"></a>Diseño con ruta de exclusión

TextKit también admite el diseño y permite escenarios complejos como texto de varias columnas y flujo de texto alrededor de rutas de acceso especificadas denominadas *rutas de exclusión*. Las rutas de exclusión se aplican al contenedor de texto, que modifica la geometría del diseño del texto, lo que hace que el texto fluya alrededor de las rutas de acceso especificadas.

Para agregar una ruta de exclusión es `ExclusionPaths` necesario establecer la propiedad en el administrador de diseño. Establecer esta propiedad hace que el administrador de diseño invalide el diseño del texto y fluya el texto alrededor de la ruta de exclusión.

### <a name="exclusion-based-on-a-cgpath"></a>Exclusión basada en un CGPath

Considere la implementación `UITextView` de subclase siguiente:

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

Este código agrega compatibilidad para dibujar en la vista de texto mediante gráficos principales. Puesto que `UITextView` la clase ahora se ha creado para usar TextKit para su representación y presentación de texto, puede usar todas las características de TextKit, como establecer rutas de exclusión.

> [!IMPORTANT]
> Estas subclases `UITextView` de ejemplo permiten agregar compatibilidad con el dibujo táctil. La subclase `UITextView` no es necesaria para obtener las características de TextKit.

Después de que el usuario dibuje en la vista de texto `CGPath` , el dibujado se `UIBezierPath` aplica a una instancia `UIBezierPath.CGPath` de estableciendo la propiedad:

```csharp
bezierPath.CGPath = exclusionPath;
```

La actualización de la siguiente línea de código hace que el diseño de texto se actualice en torno a la ruta de acceso:

```csharp
TextContainer.ExclusionPaths = new UIBezierPath[] { bezierPath };
```

En la captura de pantalla siguiente se muestra cómo cambia el diseño del texto para fluir en torno a la ruta de acceso dibujada:

<!-- ![](textkit-images/exclusionpath1.png "This screenshot illustrates how the text layout changes to flow around the drawn path")-->
![](textkit-images/exclusionpath2.png "En esta captura de pantalla se muestra cómo cambia el diseño del texto para fluir alrededor del trazado dibujado.")

Observe que en este caso la `AllowsNonContiguousLayout` propiedad del administrador de diseño está establecida en false. Esto hace que el diseño se vuelva a calcular para todos los casos en los que el texto cambie. Si se establece en true, el rendimiento puede mejorar si se evita una actualización de diseño completo, especialmente en el caso de documentos grandes. Sin embargo, `AllowsNonContiguousLayout` si se establece en true, se impedirá que la ruta de exclusión actualice el diseño en algunas circunstancias, por ejemplo, si el texto se escribe en tiempo de ejecución sin un retorno de carro final antes de que se establezca la ruta de acceso.

## <a name="related-links"></a>Vínculos relacionados

- [Introducción a iOS 7 (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/introtoios7)
- [Introducción a la interfaz de usuario de iOS 7](~/ios/platform/introduction-to-ios7/ios7-ui.md)
- [Procesamiento en segundo plano](~/ios/app-fundamentals/backgrounding/index.md)
