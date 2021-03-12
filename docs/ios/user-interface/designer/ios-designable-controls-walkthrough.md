---
title: uso de los controles personalizados con iOS Designer
description: En este documento se describe cómo crear un control personalizado y usarlo con el Xamarin Designer para iOS. Muestra cómo hacer que el control esté disponible en el cuadro de herramientas del diseñador de iOS, implementar el control para que se represente correctamente y el tiempo de diseño, etc.
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: ddde6736014f959c0f2906e716a681dc3db0e73a
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602385"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>uso de los controles personalizados con iOS Designer

> [!WARNING]
> IOS Designer quedó en desuso en Visual Studio 2019 versión 16,8 y Visual Studio 2019 para Mac versión 8,8 y se quitó en Visual Studio 2019 versión 16,9 y Visual Studio para Mac versión 8,9.
> La manera recomendada de compilar interfaces de usuario de iOS es directamente en un equipo Mac con Xcode. Para obtener más información, consulte [diseñar interfaces de usuario con Xcode](../storyboards/index.md). 

## <a name="requirements"></a>Requisitos

El Xamarin Designer para iOS está disponible en Visual Studio para Mac y Visual Studio 2017 y versiones posteriores en Windows.

En esta guía se da por supuesto que está familiarizado con el contenido que se incluye en las [guías de introducción](~/ios/get-started/index.md).

## <a name="walkthrough"></a>Tutorial

> [!IMPORTANT]
> A partir de Xamarin. Studio 5,5, la forma en que se crean los controles personalizados es ligeramente diferente a la de las versiones anteriores. Para crear un control personalizado, `IComponent` se requiere la interfaz (con los métodos de implementación asociados) o se puede anotar la clase con `[DesignTimeVisible(true)]` . El último método se usa en el siguiente ejemplo de tutorial.

1. Cree una nueva solución desde la aplicación de **> de iOS > aplicación de vista única >** plantilla de C#, asígnele un nombre `ScratchTicket` y continúe con el Asistente para nuevo proyecto:

    [![Crear una nueva solución](ios-designable-controls-walkthrough-images/01new.png)](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. Cree un nuevo archivo de clase vacía denominado `ScratchTicketView` :

    [![Crear una nueva clase ScratchTicketView](ios-designable-controls-walkthrough-images/02new.png)](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. Agregue el código siguiente para la `ScratchTicketView` clase:

    ```csharp
    using System;
    using System.ComponentModel;
    using CoreGraphics;
    using Foundation;
    using UIKit;

    namespace ScratchTicket
    {
        [Register("ScratchTicketView"), DesignTimeVisible(true)]
        public class ScratchTicketView : UIView
        {
            CGPath path;
            CGPoint initialPoint;
            CGPoint latestPoint;
            bool startNewPath = false;
            UIImage image;

            [Export("Image"), Browsable(true)]
            public UIImage Image
            {
                get { return image; }
                set
                {
                    image = value;
                    SetNeedsDisplay();
                }
            }

            public ScratchTicketView(IntPtr p)
                : base(p)
            {
                Initialize();
            }

            public ScratchTicketView()
            {
                Initialize();
            }

            void Initialize()
            {
                initialPoint = CGPoint.Empty;
                latestPoint = CGPoint.Empty;
                BackgroundColor = UIColor.Clear;
                Opaque = false;
                path = new CGPath();
                SetNeedsDisplay();
            }

            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);

                var touch = touches.AnyObject as UITouch;

                if (touch != null)
                {
                    initialPoint = touch.LocationInView(this);
                }
            }

            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);

                var touch = touches.AnyObject as UITouch;

                if (touch != null)
                {
                    latestPoint = touch.LocationInView(this);
                    SetNeedsDisplay();
                }
            }

            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                startNewPath = true;
            }

            public override void Draw(CGRect rect)
            {
                base.Draw(rect);

                using (var g = UIGraphics.GetCurrentContext())
                {
                    if (image != null)
                        g.SetFillColor((UIColor.FromPatternImage(image).CGColor));
                    else
                        g.SetFillColor(UIColor.LightGray.CGColor);
                    g.FillRect(rect);

                    if (!initialPoint.IsEmpty)
                    {
                        g.SetLineWidth(20);
                        g.SetBlendMode(CGBlendMode.Clear);
                        UIColor.Clear.SetColor();

                        if (path.IsEmpty || startNewPath)
                        {
                            path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                            startNewPath = false;
                        }
                        else
                        {
                            path.AddLineToPoint(latestPoint);
                        }

                        g.SetLineCap(CGLineCap.Round);
                        g.AddPath(path);
                        g.DrawPath(CGPathDrawingMode.Stroke);
                    }
                }
            }
        }
    }
    ```

1. Agregue los `FillTexture.png` `FillTexture2.png` archivos y `Monkey.png` (disponibles [en github](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) a la carpeta **recursos** .

1. Haga doble clic en el `Main.storyboard` archivo para abrirlo en el diseñador:

    [![Diseñador de iOS](ios-designable-controls-walkthrough-images/03new.png)](ios-designable-controls-walkthrough-images/03new.png#lightbox)

1. Arrastre y coloque una **vista de imagen** desde el **cuadro de herramientas** en la vista del guión gráfico.

    [![Una vista de imagen agregada al diseño](ios-designable-controls-walkthrough-images/04new.png)](ios-designable-controls-walkthrough-images/04new.png#lightbox)

1. Seleccione la **vista imagen** y cambie su propiedad **imagen** a `Monkey.png` .

    [![Establecimiento de la propiedad de imagen de vista de imagen en Monkey.png](ios-designable-controls-walkthrough-images/05new.png)](ios-designable-controls-walkthrough-images/05new.png#lightbox)

1. A medida que se usan clases de tamaño, es necesario restringir esta vista de imagen. Haga clic en la imagen dos veces para ponerla en modo de restricción. Vamos a restringirlo al centro; para ello, haga clic en el controlador de anclaje central y alinearlo vertical y horizontalmente:

    [![Centrar la imagen](ios-designable-controls-walkthrough-images/06new.png)](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. Para restringir el alto y el ancho, haga clic en los controladores de anclaje de tamaño (los manipuladores de forma "hueso") y seleccione ancho y alto, respectivamente:

    [![Agregar restricciones](ios-designable-controls-walkthrough-images/07new.png)](ios-designable-controls-walkthrough-images/07new.png#lightbox)

1. Actualice el marco en función de las restricciones haciendo clic en el botón actualizar de la barra de herramientas:

    [![Barra de herramientas restricciones](ios-designable-controls-walkthrough-images/08new.png)](ios-designable-controls-walkthrough-images/08new.png#lightbox)

1. A continuación, compile el proyecto para que la **vista de vales de tachado** aparezca en **componentes personalizados** en el cuadro de herramientas:

    [![Cuadro de herramientas componentes personalizados](ios-designable-controls-walkthrough-images/09new.png)](ios-designable-controls-walkthrough-images/09new.png#lightbox)

1. Arrastre y coloque una **vista de vale de tachado** para que aparezca sobre la imagen Monkey. Ajuste los manipuladores de arrastre para que la vista del vale de borrado cubra el Monkey por completo, como se muestra a continuación:

    [![Una vista de vale de borrado en la vista de imagen](ios-designable-controls-walkthrough-images/10new.png)](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. Restrinja la vista del vale de desecho a la vista de imagen dibujando un rectángulo delimitador para seleccionar ambas vistas. Seleccione las opciones para restringir el ancho, el alto, el centro y el centro y actualizar los fotogramas según las restricciones, como se muestra a continuación:

    [![Centrar y agregar restricciones](ios-designable-controls-walkthrough-images/11new.png)](ios-designable-controls-walkthrough-images/11new.png#lightbox)

1. Ejecute la aplicación y "desecho" la imagen para mostrar el Monkey.

    [![Ejecución de una aplicación de ejemplo](ios-designable-controls-walkthrough-images/10-app.png)](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>Agregar propiedades de Design-Time

El diseñador también incluye compatibilidad en tiempo de diseño para los controles personalizados del tipo de propiedad Numeric, Enumeration, String, bool, CGSize, UIColor y UIImage. Para demostrar, vamos a agregar una propiedad a `ScratchTicketView` para establecer la imagen que está "desactivada".

Agregue el código siguiente a la `ScratchTicketView` clase para la propiedad:

```csharp
[Export("Image"), Browsable(true)]
public UIImage Image
{
    get { return image; }
    set {
            image = value;
              SetNeedsDisplay ();
        }
}
```

También es posible que deseemos agregar una comprobación de NULL al método, de la siguiente `Draw` manera:

```csharp
public override void Draw(CGRect rect)
{
    base.Draw(rect);

    using (var g = UIGraphics.GetCurrentContext())
    {
        if (image != null)
            g.SetFillColor ((UIColor.FromPatternImage (image).CGColor));
        else
            g.SetFillColor (UIColor.LightGray.CGColor);

        g.FillRect(rect);

        if (!initialPoint.IsEmpty)
        {
             g.SetLineWidth(20);
             g.SetBlendMode(CGBlendMode.Clear);
             UIColor.Clear.SetColor();

             if (path.IsEmpty || startNewPath)
             {
                 path.AddLines(new CGPoint[] { initialPoint, latestPoint });
                 startNewPath = false;
             }
             else
             {
                 path.AddLineToPoint(latestPoint);
             }

             g.SetLineCap(CGLineCap.Round);
             g.AddPath(path);
             g.DrawPath(CGPathDrawingMode.Stroke);
        }
    }
}
```

Incluir un `ExportAttribute` y un `BrowsableAttribute` con el argumento establecido en `true` hace que la propiedad se muestre en el panel de **propiedades** del diseñador. Al cambiar la propiedad a otra imagen incluida en el proyecto, como `FillTexture2.png` , el control se actualiza en tiempo de diseño, como se muestra a continuación:

 [![Editar propiedades de tiempo de diseño](ios-designable-controls-walkthrough-images/11-customproperty.png)](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>Resumen

En este artículo se describe cómo crear un control personalizado, así como usarlo en una aplicación de iOS mediante el diseñador de iOS. Vimos cómo crear y compilar el control para que esté disponible para una aplicación en el cuadro de **herramientas** del diseñador. Además, analizamos cómo implementar el control para que se represente correctamente en tiempo de diseño y en tiempo de ejecución, así como en cómo exponer las propiedades del control personalizado en el diseñador.

## <a name="related-links"></a>Vínculos relacionados

- [ScratchTicket (ejemplo)](/samples/xamarin/ios-samples/scratchticket)
- [Imágenes necesarias (ejemplo)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
