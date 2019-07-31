---
title: uso de los controles personalizados con iOS Designer
description: En este documento se describe cómo crear un control personalizado y usarlo con el Xamarin Designer para iOS. Muestra cómo hacer que el control esté disponible en el cuadro de herramientas del diseñador de iOS, implementar el control para que se represente correctamente y el tiempo de diseño, etc.
ms.prod: xamarin
ms.assetid: 9032B32E-97BD-4DA6-9955-811B84682578
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/22/2017
ms.openlocfilehash: 448bc60891a44d8cd5eea0480031d692b4fb5d31
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68657478"
---
# <a name="using-custom-controls-with-the-ios-designer"></a>uso de los controles personalizados con iOS Designer

## <a name="requirements"></a>Requisitos

El Xamarin Designer para iOS está disponible en Visual Studio para Mac y Visual Studio 2017 y versiones posteriores en Windows.

En esta guía se da por supuesto que está familiarizado con el contenido que se incluye en las [guías de introducción](~/ios/get-started/index.md).

## <a name="walkthrough"></a>Tutorial

> [!IMPORTANT]
> A partir de Xamarin. Studio 5,5, la forma en que se crean los controles personalizados es ligeramente diferente a la de las versiones anteriores. Para crear un control personalizado, se requiere `IComponent` la interfaz (con los métodos de implementación asociados) o se puede anotar la clase con `[DesignTimeVisible(true)]`. El último método se usa en el siguiente ejemplo de tutorial.


1. Cree una nueva solución desde la aplicación de **> de iOS > aplicación de C# vista única >** plantilla `ScratchTicket`, asígnele un nombre y continúe con el Asistente para nuevo proyecto:

    [![](ios-designable-controls-walkthrough-images/01new.png "Crear una nueva solución")](ios-designable-controls-walkthrough-images/01new.png#lightbox)

1. Cree un nuevo archivo de clase vacía `ScratchTicketView`denominado:

    [![](ios-designable-controls-walkthrough-images/02new.png "Crear una nueva clase ScratchTicketView")](ios-designable-controls-walkthrough-images/02new.png#lightbox)

1. Agregue el código siguiente para `ScratchTicketView` la clase:

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


1. Agregue los `FillTexture.png` `FillTexture2.png` archivos y`Monkey.png` (disponibles [en github](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)) a la carpeta **recursos** .
    
1. Haga doble clic en `Main.storyboard` el archivo para abrirlo en el diseñador:

    [![](ios-designable-controls-walkthrough-images/03new.png "El diseñador de iOS")](ios-designable-controls-walkthrough-images/03new.png#lightbox)


1. Arrastre y coloque una **vista de imagen** desde el **cuadro de herramientas** en la vista del guión gráfico.

    [![](ios-designable-controls-walkthrough-images/04new.png "Una vista de imagen agregada al diseño")](ios-designable-controls-walkthrough-images/04new.png#lightbox)


1. Seleccione la **vista imagen** y cambie su propiedad **imagen** a `Monkey.png`.

    [![](ios-designable-controls-walkthrough-images/05new.png "Establecimiento de la propiedad de imagen de vista de imagen en Monkey. png")](ios-designable-controls-walkthrough-images/05new.png#lightbox)

    
1. A medida que se usan clases de tamaño, es necesario restringir esta vista de imagen. Haga clic en la imagen dos veces para ponerla en modo de restricción. Vamos a restringirlo al centro; para ello, haga clic en el controlador de anclaje central y alinearlo vertical y horizontalmente:

    [![](ios-designable-controls-walkthrough-images/06new.png "Centrar la imagen")](ios-designable-controls-walkthrough-images/06new.png#lightbox)

1. Para restringir el alto y el ancho, haga clic en los controladores de anclaje de tamaño (los manipuladores de forma "hueso") y seleccione ancho y alto, respectivamente:

    [![](ios-designable-controls-walkthrough-images/07new.png "Agregar restricciones")](ios-designable-controls-walkthrough-images/07new.png#lightbox)


1. Actualice el marco en función de las restricciones haciendo clic en el botón actualizar de la barra de herramientas:

    [![](ios-designable-controls-walkthrough-images/08new.png "Barra de herramientas restricciones")](ios-designable-controls-walkthrough-images/08new.png#lightbox)


1. A continuación, compile el proyecto para que la **vista** de vales de tachado aparezca en **componentes personalizados** en el cuadro de herramientas:

    [![](ios-designable-controls-walkthrough-images/09new.png "Cuadro de herramientas componentes personalizados")](ios-designable-controls-walkthrough-images/09new.png#lightbox)


1. Arrastre y coloque una **vista de vale** de tachado para que aparezca sobre la imagen Monkey. Ajuste los manipuladores de arrastre para que la vista del vale de borrado cubra el Monkey por completo, como se muestra a continuación:

    [![](ios-designable-controls-walkthrough-images/10new.png "Una vista de vale de borrado en la vista de imagen")](ios-designable-controls-walkthrough-images/10new.png#lightbox)

1. Restrinja la vista del vale de desecho a la vista de imagen dibujando un rectángulo delimitador para seleccionar ambas vistas. Seleccione las opciones para restringir el ancho, el alto, el centro y el centro y actualizar los fotogramas según las restricciones, como se muestra a continuación:

    [![](ios-designable-controls-walkthrough-images/11new.png "Centrar y agregar restricciones")](ios-designable-controls-walkthrough-images/11new.png#lightbox)


1. Ejecute la aplicación y "desecho" la imagen para mostrar el Monkey.

    [![](ios-designable-controls-walkthrough-images/10-app.png "Ejecución de una aplicación de ejemplo")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="adding-design-time-properties"></a>Agregar propiedades en tiempo de diseño

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

También es posible que deseemos agregar una comprobación de NULL `Draw` al método, de la siguiente manera:

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

Incluir un `ExportAttribute` y un `BrowsableAttribute`conel argumento establecido en hacequelapropiedadsemuestreenelpaneldepropiedadesdel`true` diseñador. Al cambiar la propiedad a otra imagen incluida en el proyecto, `FillTexture2.png`como, el control se actualiza en tiempo de diseño, como se muestra a continuación:

 [![](ios-designable-controls-walkthrough-images/11-customproperty.png "Editar propiedades de tiempo de diseño")](ios-designable-controls-walkthrough-images/10-app.png#lightbox)

## <a name="summary"></a>Resumen

En este artículo se describe cómo crear un control personalizado, así como usarlo en una aplicación de iOS mediante el diseñador de iOS. Vimos cómo crear y compilar el control para que esté disponible para una aplicación en el cuadro de **herramientas**del diseñador. Además, analizamos cómo implementar el control para que se represente correctamente en tiempo de diseño y en tiempo de ejecución, así como en cómo exponer las propiedades del control personalizado en el diseñador.



## <a name="related-links"></a>Vínculos relacionados

- [ScratchTicket (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/scratchticket)
- [imágenes necesarias (ejemplo)](https://github.com/xamarin/ios-samples/blob/master/ScratchTicket/Resources/images.zip?raw=true)
