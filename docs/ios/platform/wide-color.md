---
title: Color ancho en Xamarin. iOS
description: En este documento se describe el color ancho y cómo se puede usar en una aplicación de Xamarin. iOS o Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 576E978A-F182-489A-83E4-D8CDC6890B24
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: e7240a271de1f0199c2c9fc045f5c95745eb98c5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031244"
---
# <a name="wide-color-in-xamarinios"></a>Color ancho en Xamarin. iOS

_En este artículo se trata el color ancho y cómo se puede usar en una aplicación Xamarin. iOS o Xamarin. Mac._

iOS 10 y macOS Sierra mejora la compatibilidad con los formatos de píxeles de rango extendido y los espacios de colores de gama extensa en todo el sistema, incluidos marcos como gráficos principales, imagen básica, metal y AVFoundation. La compatibilidad con dispositivos con pantallas de color ancho se facilita aún más proporcionando este comportamiento en toda la pila de gráficos.

## <a name="asset-catalogs"></a>Catálogos de recursos

### <a name="supporting-wide-color-with-asset-catalogs"></a>Compatibilidad con colores anchos con catálogos de recursos

En iOS 10 y macOS Sierra, Apple ha expandido los catálogos de recursos usados para incluir y categorizar el contenido de las imágenes estáticas en el paquete de la aplicación para admitir el color ancho.

El uso de catálogos de recursos proporciona las siguientes ventajas a una aplicación:

- Proporcionan la mejor opción de implementación para los recursos de imagen estáticos.
- Admiten la corrección automática del color.
- Admiten la optimización automática del formato de píxeles.
- Admiten la división de aplicaciones y el ligero de aplicaciones, lo que garantiza que solo se entregue el contenido pertinente al dispositivo del usuario final.

Apple ha realizado las siguientes mejoras en los catálogos de recursos para la compatibilidad con colores anchos:

- Admiten contenido de origen de 16 bits (por canal de color).
- Admiten la catalogación de contenido por gama de pantalla. El contenido se puede etiquetar para el sRGB o mostrar las gamas de P3.

El desarrollador tiene tres opciones para admitir el contenido de color ancho en sus aplicaciones:

1. **No hacer nada** : dado que el contenido de color ancho solo se debe usar en situaciones en las que la aplicación necesita mostrar colores intensos (donde mejorarán la experiencia del usuario), el contenido que se encuentra fuera de este requisito se debe dejar tal cual. Se seguirá representando correctamente en todas las situaciones de hardware.
2. **Actualizar contenido existente para mostrar P3** : Esto requiere que el desarrollador Reemplace el contenido de la imagen existente en su catálogo de recursos con un nuevo archivo actualizado en el formato P3 y lo etiquete como tal en el editor de recursos. En el momento de la compilación, se generará una imagen derivada de sRGB a partir de estos recursos.
3. **Proporcionar contenido de activo optimizado** : en esta situación, el desarrollador proporcionará un sRGB de 8 bits y una visión P3 de pantalla de 16 bits de cada imagen en el catálogo de recursos (etiquetada correctamente en el editor de activos).

### <a name="asset-catalog-deployment"></a>Implementación del catálogo de activos

Lo siguiente ocurrirá cuando el desarrollador envíe una aplicación a la tienda de aplicaciones con catálogos de recursos que incluyan contenido de imagen de color ancho:

- Cuando la aplicación se implementa en el usuario final, la segmentación de aplicaciones se asegurará de que solo se entregue la variante de contenido adecuada en el dispositivo del usuario.
- En el dispositivo que no admiten el color ancho, no hay ningún costo de carga por incluir el contenido de color ancho, ya que nunca se envía al dispositivo.
- `NSImage` en macOS Sierra (y versiones posteriores) seleccionará automáticamente la mejor representación de contenido para la pantalla del hardware.
- El contenido que se muestra se actualizará automáticamente cuando se cambien las características de visualización de hardware de los dispositivos.

### <a name="asset-catalog-storage"></a>Almacenamiento del catálogo de activos

El almacenamiento del catálogo de activos tiene las siguientes propiedades y implicaciones para una aplicación:

- En tiempo de compilación, Apple intenta optimizar el almacenamiento del contenido de la imagen a través de conversiones de imágenes de alta calidad.
- se usan 16 bits por canal de color para el contenido de color ancho.
- La compresión de imágenes dependiente del contenido se usa para reducir los tamaños de contenido de entrega. Se han agregado nuevas compresiones "perdidas" para optimizar aún más los tamaños de contenido.

## <a name="rendering-off-screen-images-in-app"></a>Representación de imágenes fuera de la pantalla en la aplicación

En función del tipo de aplicación que se va a crear, es posible que el usuario pueda incluir el contenido de la imagen que se ha recopilado de Internet o crear directamente el contenido de la imagen dentro de la aplicación (como una aplicación de dibujo vectorial, por ejemplo).

En ambos casos, la aplicación puede representar la imagen requerida fuera de la pantalla en color ancho mediante las características mejoradas que se han agregado a iOS y macOS.

### <a name="drawing-wide-color-in-ios"></a>Dibujo de color ancho en iOS

Antes de explicar cómo dibujar correctamente una imagen de color ancho en iOS 10, eche un vistazo al siguiente código de dibujo de iOS común:

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    UIGraphics.BeginImageContext (size);

    ...

    UIGraphics.EndImageContext ();
    return image = UIGraphics.GetImageFromCurrentImageContext ();
    }
```

Hay problemas con el código estándar que deben solucionarse _antes_ de que se puedan usar para dibujar una imagen de color ancho. El método `UIGraphics.BeginImageContext (size)` que se usa para iniciar el dibujo de imágenes de iOS tiene las siguientes limitaciones:

- No puede crear contextos de imagen con más de 8 bits por canal de color.
- No puede representar colores en el espacio de color sRGB de intervalo extendido.
- No tiene la capacidad de proporcionar una interfaz para crear contextos en un espacio de colores que no sea sRGB debido a las rutinas de C de bajo nivel a las que se llama en segundo plano.

Para controlar estas limitaciones y dibujar una imagen de color ancho en iOS 10, use el código siguiente en su lugar:

```csharp
public UIImage DrawWideColorImage ()
{
    var size = new CGSize (250, 250);
    var render = new UIGraphicsImageRenderer (size);

    var image = render.CreateImage ((UIGraphicsImageRendererContext context) => {
        var bounds = context.Format.Bounds;
        CGRect slice;
        CGRect remainder;
        bounds.Divide (bounds.Width / 2, CGRectEdge.MinXEdge, out slice, out remainder);

        var redP3 = UIColor.FromDisplayP3 (1.0F, 0.0F, 0.0F, 1.0F);
        redP3.SetColor ();
        context.FillRect (slice);

        var redRGB = UIColor.Red;
        redRGB.SetColor ();
        context.FillRect (remainder);
    });

    // Return results
    return image;
}
```

La nueva clase de `UIGraphicsImageRenderer` crea un nuevo contexto de imagen que es capaz de controlar el espacio de color sRGB de intervalo extendido y tiene las siguientes características:

- De forma predeterminada, está totalmente administrada por el color.
- De forma predeterminada, es compatible con el espacio de colores sRGB de rango extendido.
- Decide de forma inteligente si debe representarse en el espacio de color sRGB o en el intervalo extendido de color sRGB en función de las capacidades del dispositivo iOS en el que se ejecuta la aplicación.
- Administra por completo y automáticamente la duración del contexto de la imagen (`CGContext`), por lo que el desarrollador no tiene que preocuparse por llamar a los comandos de contexto de inicio y fin.
- Es compatible con el método `UIGraphics.GetCurrentContext()`.

Se llama al método `CreateImage` de la clase `UIGraphicsImageRenderer` para crear una imagen de color ancho y pasar un controlador de finalización con el contexto de la imagen que se va a dibujar en. Todo el dibujo se realiza dentro de este controlador de finalización de la siguiente manera:

- El método `UIColor.FromDisplayP3` crea un nuevo color rojo completamente saturado en la gama ancha que muestra el espacio de colores P3 y se utiliza para dibujar la primera mitad del rectángulo. 
- La segunda mitad del rectángulo se dibuja en el color rojo de sRGB normal de la comparación.

### <a name="drawing-wide-color-in-macos"></a>Dibujo de color ancho en macOS

La clase `NSImage` se ha expandido en macOS Sierra para admitir el dibujo de imágenes de color ancho. Por ejemplo:

```csharp
var size = CGSize(250,250);
var wideColorImage = new NSImage(size, false, (drawRect) =>{
    var rects = drawRect.Divide(drawRect.Size.Width/2,CGRect.MinXEdge);

    var color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    var path = new NSBezierPath(rects.Slice).Fill();

    color = new NSColor(1.0, 0.0, 0.0, 1.0).Set();
    path = new NSBezierPath(rects.Remainder).Fill();

    // Return modified
    return true;
});
```

## <a name="rendering-on-screen-images-in-app"></a>Representación de imágenes en pantalla en la aplicación

Para representar imágenes de color ancho en pantalla, el proceso funciona de forma similar a dibujar una imagen de color ancho fuera de la pantalla para macOS e iOS presentados anteriormente.

### <a name="rendering-on-screen-in-ios"></a>Representación en pantalla en iOS

Cuando la aplicación necesita representar una imagen en la pantalla de color ancho en iOS, invalide el método `Draw` del `UIView` en cuestión como de costumbre. Por ejemplo:

```csharp
using System;
using UIKit;
using CoreGraphics;

namespace MonkeyTalk
{
    public class MonkeyView : UIView
    {
        public MonkeyView ()
        {
        }

        public override void Draw (CGRect rect)
        {
            base.Draw (rect);

            // Draw the image to screen
        ...
        }
    }
}
```

Como iOS 10 hace con la clase `UIGraphicsImageRenderer` mostrada anteriormente, decide de forma inteligente si debe representarse en el espacio de color sRGB o el intervalo extendido de color sRGB en función de las capacidades del dispositivo iOS en el que se ejecuta la aplicación cuando se llama al método `Draw`. Además, el `UIImageView` se ha administrado por colores desde iOS 9,3 también.

Si la aplicación necesita saber cómo se realiza la representación en un `UIView` o `UIViewController`, puede comprobar la nueva propiedad `DisplayGamut` de la clase `UITraitCollection`. Este valor será una enumeración `UIDisplayGamut` de lo siguiente:

- P3
- SRGB
- Sin especificar

Si la aplicación desea controlar qué espacio de colores se usa para dibujar una imagen, puede usar una nueva propiedad `ContentsFormat` del `CALayer` para especificar el espacio de colores deseado. Este valor puede ser una enumeración `CAContentsFormat` de lo siguiente:

- Gray8Uint
- Rgba16Float
- Rgba8Uint

### <a name="rendering-on-screen-in-macos"></a>Representación en pantalla en macOS

Cuando la aplicación necesita representar una imagen en color ancho en pantalla en macOS, invalide el método `DrawRect` del `NSView` en cuestión como de costumbre. Por ejemplo:

```csharp
using System;
using AppKit;
using CoreGraphics;
using CoreAnimation;

namespace MonkeyTalkMac
{
    public class MonkeyView : NSView
    {
        public MonkeyView ()
        {
        }

        public override void DrawRect (CGRect dirtyRect)
        {
            base.DrawRect (dirtyRect);

            // Draw graphics into the view
            ...
        }
    }
}
```

Una vez más, decide de manera inteligente si debe representarse en el espacio de colores sRGB o extendido de rango ampliado en función de las capacidades del hardware Mac en el que se ejecuta la aplicación cuando se llama al método `DrawRect`.

Si la aplicación desea controlar qué espacio de colores se usa para dibujar una imagen, puede usar una nueva propiedad `DepthLimit` de la clase `NSWindow` para especificar el espacio de colores deseado. Este valor puede ser una enumeración `NSWindowDepth` de lo siguiente:

- OneHundredTwentyEightBitRgb
- SixtyfourBitRgb
- TwentyfourBitRgb
