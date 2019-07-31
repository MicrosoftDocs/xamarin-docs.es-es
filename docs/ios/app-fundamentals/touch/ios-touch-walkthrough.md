---
title: 'Tutorial: Uso de Touch en Xamarin. iOS'
description: En este documento se describe cómo controlar la interacción táctil en las aplicaciones de Xamarin. iOS, que tratan las interacciones táctiles de ejemplo, los reconocedores de gestos y los reconocedores de gestos personalizados.
ms.prod: xamarin
ms.assetid: 13F8289B-7A80-4959-AF3F-57874D866DCA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f772bf1309330b41f9faea812e2cdcdba8e969cb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653135"
---
# <a name="walkthrough-using-touch-in-xamarinios"></a>Tutorial: Uso de Touch en Xamarin. iOS

En este tutorial se muestra cómo escribir código que responda a distintos tipos de eventos de entrada táctil. Cada ejemplo se encuentra en una pantalla independiente:

- [Ejemplos táctiles](#Touch_Samples) : cómo responder a los eventos táctiles.
- [Ejemplos](#Gesture_Recognizer_Samples) de reconocedor de gestos: Cómo usar reconocedores de gestos integrados.
- [Ejemplo](#Custom_Gesture_Recognizer) de reconocedor de gestos personalizados: Cómo crear un reconocedor de gestos personalizado.

Cada sección contiene instrucciones para escribir el código desde cero.
El [código de ejemplo inicial](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start) ya incluye un guion gráfico completo y una pantalla de menú:

 [![](ios-touch-walkthrough-images/image3.png "El ejemplo incluye la pantalla de menú")](ios-touch-walkthrough-images/image3.png#lightbox)

Siga las instrucciones que se indican a continuación para agregar código al guion gráfico y obtenga información sobre los diferentes tipos de eventos táctiles disponibles en iOS. Como alternativa, abra el [ejemplo terminado](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final) para ver todo lo que funciona.

<a name="Touch_Samples"/>

## <a name="touch-samples"></a>Ejemplos táctiles

En este ejemplo, se mostrarán algunas de las API de Touch. Siga estos pasos para agregar el código necesario para implementar eventos táctiles:


1. Abra el proyecto **Touch_Start**. En primer lugar, ejecute el proyecto para asegurarse de que todo esté bien y toque el botón **Touch samples** . Debería ver una pantalla similar a la siguiente (aunque no funcionará ninguno de los botones):
    
    [![](ios-touch-walkthrough-images/image4.png "Ejecución de la aplicación de ejemplo con botones que no funcionan")](ios-touch-walkthrough-images/image4.png#lightbox)


1. Edite el archivo **TouchViewController.CS** y agregue las dos variables de instancia siguientes a `TouchViewController`la clase:

    ```csharp 
    #region Private Variables
    private bool imageHighlighted = false;
    private bool touchStartedInside;
    #endregion
    ```


1. Implemente `TouchesBegan` el método, como se muestra en el código siguiente:

    ```csharp 
    public override void TouchesBegan(NSSet touches, UIEvent evt)
    {
        base.TouchesBegan(touches, evt);
    
        // If Multitouch is enabled, report the number of fingers down
        TouchStatus.Text = string.Format ("Number of fingers {0}", touches.Count);
    
        // Get the current touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            // Check to see if any of the images have been touched
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Fist image touched
                TouchImage.Image = UIImage.FromBundle("TouchMe_Touched.png");
                TouchStatus.Text = "Touches Began";
            } else if (touch.TapCount == 2 && DoubleTouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                // Second image double-tapped, toggle bitmap
                if (imageHighlighted)
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
                    TouchStatus.Text = "Double-Tapped Off";
                }
                else
                {
                    DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
                    TouchStatus.Text = "Double-Tapped On";
                }
                imageHighlighted = !imageHighlighted;
            } else if (DragImage.Frame.Contains(touch.LocationInView(View)))
            {
                // Third image touched, prepare to drag
                touchStartedInside = true;
            }
        }
    }
    ```
    
    Este método funciona comprobando un `UITouch` objeto y, si existe, realiza alguna acción en función de dónde se produjo el toque:

    * _Dentro de TouchImage_ : muestra el `Touches Began` texto de una etiqueta y cambia la imagen.
    * _Dentro de DoubleTouchImage_ : cambie la imagen que se muestra si el gesto fue un doble punteo.
    * _Dentro de DragImage_ : establece una marca que indica que la entrada táctil se ha iniciado. El método `TouchesMoved` usará esta marca para determinar si `DragImage` se debe mover en la pantalla o no, como veremos en el paso siguiente.

    El código anterior solo se ocupa de los toques individuales, todavía no hay ningún comportamiento si el usuario mueve el dedo por la pantalla. Para responder al movimiento, implemente `TouchesMoved` como se muestra en el código siguiente:

    ```csharp 
    public override void TouchesMoved(NSSet touches, UIEvent evt)
    {
        base.TouchesMoved(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchStatus.Text = "Touches Moved";
            }

            //==== IMAGE DRAG
            // check to see if the touch started in the drag me image
            if (touchStartedInside)
            {
                // move the shape
                float offsetX = touch.PreviousLocationInView(View).X - touch.LocationInView(View).X;
                float offsetY = touch.PreviousLocationInView(View).Y - touch.LocationInView(View).Y;
                DragImage.Frame = new RectangleF(new PointF(DragImage.Frame.X - offsetX, DragImage.Frame.Y - offsetY), DragImage.Frame.Size);
            }
        }
    }
    ```

    Este método obtiene un `UITouch` objeto y, a continuación, comprueba si se ha producido el toque. Si el toque se produjo `TouchImage`en, se muestra en la pantalla el texto de los toques que se mueven. 

    Si `touchStartedInside` es true, sabemos que el usuario tiene el dedo encendido `DragImage` y lo está moviendo. El código se moverá `DragImage` cuando el usuario mueva el dedo por la pantalla.

1. Es necesario controlar el caso cuando el usuario levanta el dedo de la pantalla, o iOS cancela el evento Touch. Para ello, se implementará `TouchesEnded` y `TouchesCancelled` como se muestra a continuación:

    ```csharp
    public override void TouchesCancelled(NSSet touches, UIEvent evt)
    {
        base.TouchesCancelled(touches, evt);
    
        // reset our tracking flags
        touchStartedInside = false;
        TouchImage.Image = UIImage.FromBundle("TouchMe.png");
        TouchStatus.Text = "";
    }
    
    public override void TouchesEnded(NSSet touches, UIEvent evt)
    {
        base.TouchesEnded(touches, evt);
        // get the touch
        UITouch touch = touches.AnyObject as UITouch;
        if (touch != null)
        {
            //==== IMAGE TOUCH
            if (TouchImage.Frame.Contains(touch.LocationInView(TouchView)))
            {
                TouchImage.Image = UIImage.FromBundle("TouchMe.png");
                TouchStatus.Text = "Touches Ended";
            }
        }
        // reset our tracking flags
        touchStartedInside = false;
    }
    ```
    
    Ambos métodos restablecerán la `touchStartedInside` marca en false. `TouchesEnded`también se mostrará `TouchesEnded` en la pantalla.

1. En este punto, la pantalla de ejemplos de Touch ha finalizado. Observe cómo cambia la pantalla a medida que interactúa con cada una de las imágenes, tal como se muestra en la siguiente captura de pantalla:
        
    [![](ios-touch-walkthrough-images/image4.png "Pantalla de inicio de la aplicación")](ios-touch-walkthrough-images/image4.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image5.png "La pantalla después de que el usuario arrastre un botón")](ios-touch-walkthrough-images/image5.png#lightbox)
 

<a name="Gesture_Recognizer_Samples" />

## <a name="gesture-recognizer-samples"></a>Ejemplos del reconocedor de gestos

En la [sección anterior](#Touch_Samples) se mostró cómo arrastrar un objeto alrededor de la pantalla mediante el uso de eventos táctiles.
En esta sección, se desharán los eventos táctiles y se mostrará cómo usar los siguientes reconocedores de gestos:

-  `UIPanGestureRecognizer` Para arrastrar una imagen en torno a la pantalla.
-  `UITapGestureRecognizer` Para responder a los dobles punteos en la pantalla.

Si ejecuta el [código de ejemplo inicial](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start) y hace clic en el botón **ejemplos** del reconocedor de gestos, debería ver la siguiente pantalla:

 [![](ios-touch-walkthrough-images/image6.png "Al hacer clic en el botón ejemplos del reconocedor de gestos se muestra esta pantalla")](ios-touch-walkthrough-images/image6.png#lightbox)

Siga estos pasos para implementar los reconocedores de gestos:


1. Edite el archivo **GestureViewController.CS** y agregue la siguiente variable de instancia:

    ```csharp
    #region Private Variables
    private bool imageHighlighted = false;
    private RectangleF originalImageFrame = RectangleF.Empty;
    #endregion
    ```

    Necesitamos esta variable de instancia para realizar el seguimiento de la ubicación anterior de la imagen.
El reconocedor de gestos panorámico `originalImageFrame` usará el valor para calcular el desplazamiento necesario para volver a dibujar la imagen en la pantalla.

1. Agregue el método siguiente al controlador:

    ```csharp
    private void WireUpDragGestureRecognizer()
    {
        // Create a new tap gesture
        UIPanGestureRecognizer gesture = new UIPanGestureRecognizer();
    
        // Wire up the event handler (have to use a selector)
        gesture.AddTarget(() => HandleDrag(gesture));  // to be defined
    
        // Add the gesture recognizer to the view
        DragImage.AddGestureRecognizer(gesture);
    }
    ```

    Este código crea una `UIPanGestureRecognizer` instancia de y la agrega a una vista.
Observe que asignamos un destino al gesto en la forma del método `HandleDrag` : este método se proporciona en el paso siguiente.

1. Para implementar HandleDrag, agregue el código siguiente al controlador:

    ```csharp
    private void HandleDrag(UIPanGestureRecognizer recognizer)
    {
        // If it's just began, cache the location of the image
        if (recognizer.State == UIGestureRecognizerState.Began)
        {
            originalImageFrame = DragImage.Frame;
        }
    
        // Move the image if the gesture is valid
        if (recognizer.State != (UIGestureRecognizerState.Cancelled | UIGestureRecognizerState.Failed
            | UIGestureRecognizerState.Possible))
        {
            // Move the image by adding the offset to the object's frame
            PointF offset = recognizer.TranslationInView(DragImage);
            RectangleF newFrame = originalImageFrame;
            newFrame.Offset(offset.X, offset.Y);
            DragImage.Frame = newFrame;
        }
    }
    ```

    El código anterior comprobará primero el estado del reconocedor de gestos y, a continuación, moverá la imagen en torno a la pantalla. Con este código en su lugar, el controlador puede ahora admitir arrastrar la imagen por la pantalla.


1. Agregue un `UITapGestureRecognizer` que cambie la imagen que se muestra en DoubleTouchImage. Agregue el método siguiente al `GestureViewController` controlador:

    ```csharp
    private void WireUpTapGestureRecognizer()
    {
        // Create a new tap gesture
        UITapGestureRecognizer tapGesture = null;
    
        // Report touch
        Action action = () => {
            TouchStatus.Text = string.Format("Image touched at: {0}",tapGesture.LocationOfTouch(0, DoubleTouchImage));
    
            // Toggle the image
            if (imageHighlighted)
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe.png");
            }
            else
            {
                DoubleTouchImage.Image = UIImage.FromBundle("DoubleTapMe_Highlighted.png");
            }
            imageHighlighted = !imageHighlighted;
        };
    
        tapGesture = new UITapGestureRecognizer(action);
    
        // Configure it
        tapGesture.NumberOfTapsRequired = 2;
    
        // Add the gesture recognizer to the view
        DoubleTouchImage.AddGestureRecognizer(tapGesture);
    }
    ```

    Este código es muy similar al código de `UIPanGestureRecognizer` , pero en lugar de usar un delegado para un destino, se usa un. `Action` 

1. Lo último que debemos hacer es modificar `ViewDidLoad` para que llame a los métodos que acabamos de agregar. Cambie ViewDidLoad para que se parezca al código siguiente:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        Title = "Gesture Recognizers";
    
        // Save initial state
        originalImageFrame = DragImage.Frame;
    
        WireUpTapGestureRecognizer();
        WireUpDragGestureRecognizer();
    }
    ```

    Observe también que se inicializa el valor de `originalImageFrame`.


1. Ejecute la aplicación e interactúe con las dos imágenes.
La captura de pantalla siguiente es un ejemplo de estas interacciones:
    
    [![](ios-touch-walkthrough-images/image7.png "En esta captura de pantalla se muestra una interacción de arrastre")](ios-touch-walkthrough-images/image7.png#lightbox)



<a name="Custom_Gesture_Recognizer"/>

## <a name="custom-gesture-recognizer"></a>Reconocedor de gestos personalizados

En esta sección, aplicaremos los conceptos de las secciones anteriores para crear un reconocedor de gestos personalizado. El reconocedor de gestos personalizados creará subclases `UIGestureRecognizer`y reconocerá Cuándo el usuario dibuja una "V" en la pantalla y, a continuación, alterna un mapa de bits. La captura de pantalla siguiente es un ejemplo de esta pantalla:

 [![](ios-touch-walkthrough-images/image8.png "La aplicación se reconocerá cuando el usuario dibuje una \"V\" en la pantalla.")](ios-touch-walkthrough-images/image8.png#lightbox)

Siga estos pasos para crear un reconocedor de gestos personalizado:


1. Agregue una nueva clase al proyecto denominado `CheckmarkGestureRecognizer`y haga que sea similar al código siguiente:

    ```csharp
    using System;
    using CoreGraphics;
    using Foundation;
    using UIKit;
    
    namespace Touch
    {
        public class CheckmarkGestureRecognizer : UIGestureRecognizer
        {
            #region Private Variables
            private CGPoint midpoint = CGPoint.Empty;
            private bool strokeUp = false;
            #endregion
    
            #region Override Methods
            /// <summary>
            ///   Called when the touches end or the recognizer state fails
            /// </summary>
            public override void Reset()
            {
                base.Reset();
    
                strokeUp = false;
                midpoint = CGPoint.Empty;
            }
    
            /// <summary>
            ///   Is called when the fingers touch the screen.
            /// </summary>
            public override void TouchesBegan(NSSet touches, UIEvent evt)
            {
                base.TouchesBegan(touches, evt);
    
                // we want one and only one finger
                if (touches.Count != 1)
                {
                    base.State = UIGestureRecognizerState.Failed;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the touches are cancelled due to a phone call, etc.
            /// </summary>
            public override void TouchesCancelled(NSSet touches, UIEvent evt)
            {
                base.TouchesCancelled(touches, evt);
                // we fail the recognizer so that there isn't unexpected behavior
                // if the application comes back into view
                base.State = UIGestureRecognizerState.Failed;
            }
    
            /// <summary>
            ///   Called when the fingers lift off the screen
            /// </summary>
            public override void TouchesEnded(NSSet touches, UIEvent evt)
            {
                base.TouchesEnded(touches, evt);
                //
                if (base.State == UIGestureRecognizerState.Possible && strokeUp)
                {
                    base.State = UIGestureRecognizerState.Recognized;
                }
    
                Console.WriteLine(base.State.ToString());
            }
    
            /// <summary>
            ///   Called when the fingers move
            /// </summary>
            public override void TouchesMoved(NSSet touches, UIEvent evt)
            {
                base.TouchesMoved(touches, evt);
    
                // if we haven't already failed
                if (base.State != UIGestureRecognizerState.Failed)
                {
                    // get the current and previous touch point
                    CGPoint newPoint = (touches.AnyObject as UITouch).LocationInView(View);
                    CGPoint previousPoint = (touches.AnyObject as UITouch).PreviousLocationInView(View);
    
                    // if we're not already on the upstroke
                    if (!strokeUp)
                    {
                        // if we're moving down, just continue to set the midpoint at
                        // whatever point we're at. when we start to stroke up, it'll stick
                        // as the last point before we upticked
                        if (newPoint.X >= previousPoint.X && newPoint.Y >= previousPoint.Y)
                        {
                            midpoint = newPoint;
                        }
                        // if we're stroking up (moving right x and up y [y axis is flipped])
                        else if (newPoint.X >= previousPoint.X && newPoint.Y <= previousPoint.Y)
                        {
                            strokeUp = true;
                        }
                        // otherwise, we fail the recognizer
                        else
                        {
                            base.State = UIGestureRecognizerState.Failed;
                        }
                    }
                }
    
                Console.WriteLine(base.State.ToString());
            }
            #endregion
        }
    }
    ```

    Se llama al método RESET cuando la `State` propiedad cambia `Recognized` a o `Ended`. Este es el momento de restablecer cualquier conjunto de estado interno en el reconocedor de gestos personalizado.
Ahora la clase puede empezar de nuevo la próxima vez que el usuario interactúe con la aplicación y esté listo para volver a intentar reconocer el gesto.



1. Ahora que hemos definido un reconocedor de gestos`CheckmarkGestureRecognizer`personalizado (), edite el archivo **CustomGestureViewController.CS** y agregue las dos variables de instancia siguientes:

    ```csharp
    #region Private Variables
    private bool isChecked = false;
    private CheckmarkGestureRecognizer checkmarkGesture;
    #endregion
    ```

1. Para crear una instancia y configurar el reconocedor de gestos, agregue el siguiente método al controlador:

    ```csharp
    private void WireUpCheckmarkGestureRecognizer()
    {
        // Create the recognizer
        checkmarkGesture = new CheckmarkGestureRecognizer();
    
        // Wire up the event handler
        checkmarkGesture.AddTarget(() => {
            if (checkmarkGesture.State == (UIGestureRecognizerState.Recognized | UIGestureRecognizerState.Ended))
            {
                if (isChecked)
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Unchecked.png");
                }
                else
                {
                    CheckboxImage.Image = UIImage.FromBundle("CheckBox_Checked.png");
                }
                isChecked = !isChecked;
            }
        });
    
        // Add the gesture recognizer to the view
        View.AddGestureRecognizer(checkmarkGesture);
    }
    ```

1. Edite `ViewDidLoad` para que llame `WireUpCheckmarkGestureRecognizer`a, como se muestra en el siguiente fragmento de código:

    ```csharp
    public override void ViewDidLoad()
    {
        base.ViewDidLoad();
    
        // Wire up the gesture recognizer
        WireUpCheckmarkGestureRecognizer();
    }
    ```

1. Ejecute la aplicación e intente dibujar una "V" en la pantalla. Debería ver que la imagen que se muestra cambia, tal como se muestra en las siguientes capturas de pantalla:
    
    [![](ios-touch-walkthrough-images/image9.png "Botón activado")](ios-touch-walkthrough-images/image9.png#lightbox)
    
    [![](ios-touch-walkthrough-images/image10.png "Botón desactivado")](ios-touch-walkthrough-images/image10.png#lightbox)



Las tres secciones anteriores demostraron diferentes maneras de responder a eventos táctiles en iOS: mediante eventos táctiles, reconocedores de gestos integrados o con un reconocedor de gestos personalizado.



## <a name="related-links"></a>Vínculos relacionados

- [iOS Touch Start (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-start)
- [iOS Touch final (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/applicationfundamentals-touch-final)
