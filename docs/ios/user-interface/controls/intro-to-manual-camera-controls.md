---
title: Controles de cámara manuales en Xamarin. iOS
description: En este documento se describe cómo se puede usar el marco de AVFoundation de iOS con Xamarin. iOS para habilitar los controles de cámara manuales. Los controles de cámara manuales permiten a los usuarios controlar la configuración de enfoque, balance de blanco y exposición.
ms.prod: xamarin
ms.assetid: 56340225-5F3C-4BFC-9A79-61496D7FE5B5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/22/2017
ms.openlocfilehash: 9162754dd41190a14ce1b385d63e94f7c8c1ced7
ms.sourcegitcommit: e7a5d1ec9e50a09b3b24f4c57850a4763c3406d2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/18/2021
ms.locfileid: "101087499"
---
# <a name="manual-camera-controls-in-xamarinios"></a>Controles de cámara manuales en Xamarin. iOS

Los controles de la cámara manual, proporcionados por `AVFoundation Framework` en iOS 8, permiten que una aplicación móvil tenga control total sobre la cámara de un dispositivo iOS. Este nivel específico de control se puede usar para crear aplicaciones de cámara de nivel profesional y proporcionar composiciones de artista mediante el ajuste de los parámetros de la cámara mientras se toma una imagen o un vídeo.

Estos controles también pueden ser útiles a la hora de desarrollar aplicaciones científicas o industriales, donde los resultados están menos orientados hacia la corrección o la belleza de la imagen, y están orientados más hacia el resaltado de alguna característica o elemento de la imagen que se está llevando a cabo.

## <a name="avfoundation-capture-objects"></a>Objetos de captura AVFoundation

Tanto si se toman vídeo como imágenes fijas mediante la cámara en un dispositivo iOS, el proceso usado para capturar esas imágenes es en gran medida el mismo. Esto se aplica a las aplicaciones que usan los controles de cámara automatizados predeterminados o a los que aprovechan los nuevos controles de cámara manuales:

 [![AVFoundation información general sobre objetos de captura](intro-to-manual-camera-controls-images/image1.png)](intro-to-manual-camera-controls-images/image1.png#lightbox)

La entrada se toma de un `AVCaptureDeviceInput` en un `AVCaptureSession` por medio de `AVCaptureConnection` . El resultado se genera como una imagen fija o como una secuencia de vídeo. El proceso completo se controla mediante un `AVCaptureDevice` .

## <a name="manual-controls-provided"></a>Controles manuales proporcionados

Con las nuevas API proporcionadas por iOS 8, la aplicación puede tomar el control de las siguientes características de la cámara:

- **Enfoque manual** : al permitir que el usuario final tome el control del foco directamente, una aplicación puede proporcionar más control sobre la imagen tomada.
- **Exposición manual** : al proporcionar control manual sobre la exposición, una aplicación puede proporcionar más libertad a los usuarios y permitirles obtener una apariencia estilizada.
- **Balance manual en blanco** : el balance de blanco se usa para ajustar el color de una imagen, a menudo para que parezca realista. Diferentes fuentes de luz tienen temperaturas de color diferentes y la configuración de la cámara utilizada para capturar una imagen se ajusta para compensar estas diferencias. De nuevo, al permitir el control de usuario sobre el equilibrio de blanco, los usuarios pueden realizar ajustes que no se pueden realizar automáticamente.

iOS 8 proporciona extensiones y mejoras a las API de iOS existentes para proporcionar este control específico sobre el proceso de captura de imágenes.

## <a name="requirements"></a>Requisitos

Para completar los pasos que se describen en este artículo, es necesario lo siguiente:

- **Xcode 7 + e iOS 8 o posterior** : las API Xcode 7 e iOS 8 de Apple o las API más recientes deben estar instaladas y configuradas en el equipo del desarrollador.
- **Visual Studio para Mac** : la versión más reciente de Visual Studio para Mac debe estar instalada y configurada en el dispositivo del usuario.
- **dispositivo iOS 8** : un dispositivo iOS que ejecuta la versión más reciente de iOS 8. No se pueden probar las características de la cámara en el simulador de iOS.

## <a name="general-av-capture-setup"></a>Configuración de captura de AV general

Al grabar vídeo en un dispositivo iOS, hay un código de configuración general que siempre es necesario. En esta sección se tratará la configuración mínima necesaria para grabar vídeo desde la cámara del dispositivo iOS y mostrar ese vídeo en tiempo real en un `UIImageView` .

### <a name="output-sample-buffer-delegate"></a>Delegado de búfer de ejemplo de salida

Una de las primeras cosas necesarias será un delegado para supervisar el búfer de salida de ejemplo y mostrar una imagen capturada desde el búfer a un `UIImageView` en la interfaz de usuario de la aplicación.

La rutina siguiente supervisará el búfer de ejemplo y actualizará la interfaz de usuario:

```csharp
using System;
using Foundation;
using UIKit;
using System.CodeDom.Compiler;
using System.Collections.Generic;
using System.Linq;
using AVFoundation;
using CoreVideo;
using CoreMedia;
using CoreGraphics;

namespace ManualCameraControls
{
    public class OutputRecorder : AVCaptureVideoDataOutputSampleBufferDelegate
    {
        #region Computed Properties
        public UIImageView DisplayView { get; set; }
        #endregion

        #region Constructors
        public OutputRecorder ()
        {

        }
        #endregion

        #region Private Methods
        private UIImage GetImageFromSampleBuffer(CMSampleBuffer sampleBuffer) {

            // Get a pixel buffer from the sample buffer
            using (var pixelBuffer = sampleBuffer.GetImageBuffer () as CVPixelBuffer) {
                // Lock the base address
                pixelBuffer.Lock (0);

                // Prepare to decode buffer
                var flags = CGBitmapFlags.PremultipliedFirst | CGBitmapFlags.ByteOrder32Little;

                // Decode buffer - Create a new colorspace
                using (var cs = CGColorSpace.CreateDeviceRGB ()) {

                    // Create new context from buffer
                    using (var context = new CGBitmapContext (pixelBuffer.BaseAddress,
                        pixelBuffer.Width,
                        pixelBuffer.Height,
                        8,
                        pixelBuffer.BytesPerRow,
                        cs,
                        (CGImageAlphaInfo)flags)) {

                        // Get the image from the context
                        using (var cgImage = context.ToImage ()) {

                            // Unlock and return image
                            pixelBuffer.Unlock (0);
                            return UIImage.FromImage (cgImage);
                        }
                    }
                }
            }
        }
        #endregion

        #region Override Methods
        public override void DidOutputSampleBuffer (AVCaptureOutput captureOutput, CMSampleBuffer sampleBuffer, AVCaptureConnection connection)
        {
            // Trap all errors
            try {
                // Grab an image from the buffer
                var image = GetImageFromSampleBuffer(sampleBuffer);

                // Display the image
                if (DisplayView !=null) {
                    DisplayView.BeginInvokeOnMainThread(() => {
                        // Set the image
                        if (DisplayView.Image != null) DisplayView.Image.Dispose();
                        DisplayView.Image = image;

                        // Rotate image to the correct display orientation
                        DisplayView.Transform = CGAffineTransform.MakeRotation((float)Math.PI/2);
                    });
                }

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            }
            catch(Exception e) {
                // Report error
                Console.WriteLine ("Error sampling buffer: {0}", e.Message);
            }
        }
        #endregion
    }
}
```

Con esta rutina en su lugar, `AppDelegate` se puede modificar para abrir una sesión de captura de AV y grabar una fuente de vídeo en directo.

### <a name="creating-an-av-capture-session"></a>Creación de una sesión de captura de AV

La sesión de captura de AV se usa para controlar la grabación de vídeo en directo desde la cámara del dispositivo iOS y es necesario para obtener el vídeo en una aplicación de iOS. Dado que la aplicación de ejemplo de ejemplo `ManualCameraControl` utiliza la sesión de captura en varios lugares distintos, se configurará en el `AppDelegate` y estará disponible para toda la aplicación.

Haga lo siguiente para modificar la aplicación `AppDelegate` y agregar el código necesario:

1. Haga doble clic en el `AppDelegate.cs` archivo en el explorador de soluciones para abrirlo para su edición.
1. Agregue las siguientes instrucciones using al principio del archivo:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    ```

1. Agregue las siguientes variables privadas y propiedades calculadas a la `AppDelegate` clase:

    ```csharp
    #region Private Variables
    private NSError Error;
    #endregion

    #region Computed Properties
    public override UIWindow Window {get;set;}
    public bool CameraAvailable { get; set; }
    public AVCaptureSession Session { get; set; }
    public AVCaptureDevice CaptureDevice { get; set; }
    public OutputRecorder Recorder { get; set; }
    public DispatchQueue Queue { get; set; }
    public AVCaptureDeviceInput Input { get; set; }
    #endregion
    ```

1. Invalide el método terminado y cámbielo a:

    ```csharp
    public override void FinishedLaunching (UIApplication application)
    {
        // Create a new capture session
        Session = new AVCaptureSession ();
        Session.SessionPreset = AVCaptureSession.PresetMedium;

        // Create a device input
        CaptureDevice = AVCaptureDevice.DefaultDeviceWithMediaType (AVMediaType.Video);
        if (CaptureDevice == null) {
            // Video capture not supported, abort
            Console.WriteLine ("Video recording not supported on this device");
            CameraAvailable = false;
            return;
        }

        // Prepare device for configuration
        CaptureDevice.LockForConfiguration (out Error);
        if (Error != null) {
            // There has been an issue, abort
            Console.WriteLine ("Error: {0}", Error.LocalizedDescription);
            CaptureDevice.UnlockForConfiguration ();
            return;
        }

        // Configure stream for 15 frames per second (fps)
        CaptureDevice.ActiveVideoMinFrameDuration = new CMTime (1, 15);

        // Unlock configuration
        CaptureDevice.UnlockForConfiguration ();

        // Get input from capture device
        Input = AVCaptureDeviceInput.FromDevice (CaptureDevice);
        if (Input == null) {
            // Error, report and abort
            Console.WriteLine ("Unable to gain input from capture device.");
            CameraAvailable = false;
            return;
        }

        // Attach input to session
        Session.AddInput (Input);

        // Create a new output
        var output = new AVCaptureVideoDataOutput ();
        var settings = new AVVideoSettingsUncompressed ();
        settings.PixelFormatType = CVPixelFormatType.CV32BGRA;
        output.WeakVideoSettings = settings.Dictionary;

        // Configure and attach to the output to the session
        Queue = new DispatchQueue ("ManCamQueue");
        Recorder = new OutputRecorder ();
        output.SetSampleBufferDelegate (Recorder, Queue);
        Session.AddOutput (output);

        // Let tabs know that a camera is available
        CameraAvailable = true;
    }
    ```

1. Guarde los cambios en el archivo.

Con este código en su lugar, los controles de cámara manuales se pueden implementar fácilmente para experimentar y probar.

## <a name="manual-focus"></a>Enfoque manual

Al permitir que el usuario final tome controles del foco directamente, una aplicación puede proporcionar un control más artístico sobre la imagen tomada.

Por ejemplo, un fotógrafo profesional puede suavizar el foco de una imagen para lograr un [efecto bokeh](https://en.wikipedia.org/wiki/Bokeh):

[![Un efecto de bokeh](intro-to-manual-camera-controls-images/image2.png)](intro-to-manual-camera-controls-images/image2.png#lightbox)

O bien, cree un [efecto de extracción de foco](http://www.mediacollege.com/video/camera/focus/pull.html), como:

[![Efecto de extracción del foco](intro-to-manual-camera-controls-images/image3.png)](intro-to-manual-camera-controls-images/image3.png#lightbox)

En el caso de los científicos o de un escritor de aplicaciones médicas, es posible que la aplicación desee mover mediante programación la lente de los experimentos. En cualquier caso, la nueva API permite que el usuario final o la aplicación tomen el control sobre el foco en el momento en que se toma la imagen.

### <a name="how-focus-works"></a>Cómo funciona el foco

Antes de analizar los detalles de cómo controlar el foco en una aplicación de IOS 8. Echemos un vistazo rápido a cómo funciona el enfoque en un dispositivo iOS:

[![Cómo funciona el foco en un dispositivo iOS](intro-to-manual-camera-controls-images/image4.png)](intro-to-manual-camera-controls-images/image4.png#lightbox)

La luz entra en la lente de la cámara en el dispositivo iOS y se centra en un sensor de imagen. La distancia de la lente desde los controles del sensor donde se encuentra el punto focal (el área donde la imagen será la más nítida), en relación con el sensor. Cuanto más lejos sea el objetivo del sensor, los objetos de distancia parecen más nítidos y los objetos cercanos más cercanos parecen más nítidos.

En un dispositivo iOS, la lente se mueve más cerca o después del sensor por imanes y muelles. Como resultado, no es posible el posicionamiento exacto de la lente, ya que variará de un dispositivo a otro y puede verse afectado por parámetros como la orientación del dispositivo o la edad del dispositivo y la primavera.

### <a name="important-focus-terms"></a>Términos de enfoque importantes

Al trabajar con el foco, hay algunos términos con los que el desarrollador debe estar familiarizado:

- **Profundidad de campo** : la distancia entre los objetos enfocados más cercanos y más alejados.
- **Macro** : es el extremo cercano del espectro de foco y es la distancia más cercana a la que se puede centrar la lente.
- **Infinity** : este es el extremo del espectro de foco y es la distancia más lejana a la que se puede centrar la lente.
- **Distancia de hiperfocalización** : este es el punto en el espectro de foco, donde el objeto más alejado del marco está justo al final del foco. En otras palabras, esta es la posición focal que maximiza la profundidad del campo.
- **Posición del objetivo** : es lo que controla todos los demás términos. Es la distancia de la lente desde el sensor y, por tanto, el controlador de foco.

Teniendo en cuenta estos términos y conocimientos, los nuevos controles de enfoque manual se pueden implementar correctamente en una aplicación de iOS 8.

### <a name="existing-focus-controls"></a>Controles de foco existentes

iOS 7 y versiones anteriores proporcionaban los controles de enfoque existentes a través `FocusMode` de la propiedad como:

- `AVCaptureFocusModeLocked` : El foco está bloqueado en un único punto de enfoque.
- `AVCaptureFocusModeAutoFocus` : La cámara barre la lente a través de todos los puntos focales hasta que encuentra un enfoque agudo y luego permanece allí.
- `AVCaptureFocusModeContinuousAutoFocus` : La cámara se reenfoca cada vez que detecta una condición de desenfoque.

Los controles existentes también proporcionan un punto de interés configurable a través de la `FocusPointOfInterest` propiedad, por lo que el usuario puede pulsar para centrarse en un área determinada. La aplicación también puede realizar un seguimiento del movimiento de la lente supervisando la `IsAdjustingFocus` propiedad.

Además, la restricción de intervalo la proporcionó la `AutoFocusRangeRestriction` propiedad como:

- `AVCaptureAutoFocusRangeRestrictionNear` : Restringe el foco a profundidades cercanas. Útil en situaciones como el examen de un código QR o código de barras.
- `AVCaptureAutoFocusRangeRestrictionFar` : Restringe el foco a profundidades distantes. Resulta útil en situaciones en las que los objetos que se sabe que son irrelevantes se encuentran en el campo de la vista (por ejemplo, un marco de ventana).

Por último, hay la `SmoothAutoFocus` propiedad que reduce el algoritmo de enfoque automático y lo recorre en incrementos más pequeños para evitar mover los artefactos al grabar vídeo.

### <a name="new-focus-controls-in-ios-8"></a>Nuevos controles de enfoque en iOS 8

Además de las características ya proporcionadas por iOS 7 y versiones posteriores, las siguientes características están ahora disponibles para controlar el foco en iOS 8:

- Control manual completo de la posición del objetivo al bloquear el foco.
- Observación de clave-valor de la posición del objetivo en cualquier modo de enfoque.

Para implementar las características anteriores, la `AVCaptureDevice` clase se ha modificado para incluir una propiedad de solo lectura `LensPosition` que se usa para obtener la posición actual de la lente de la cámara.

Para tomar el control manual de la posición de la lente, el dispositivo de captura debe estar en modo de enfoque bloqueado. Ejemplo:

 `CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;`

El `SetFocusModeLocked` método del dispositivo de captura se usa para ajustar la posición de la lente de la cámara. Se puede proporcionar una rutina de devolución de llamada opcional para recibir una notificación cuando el cambio surta efecto. Ejemplo:

```csharp
ThisApp.CaptureDevice.LockForConfiguration(out Error);
ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
ThisApp.CaptureDevice.UnlockForConfiguration();
```

Tal como se ha mostrado en el código anterior, el dispositivo de captura debe estar bloqueado para la configuración antes de que se pueda realizar un cambio en la posición de la lente. Los valores de posición de lente válidos están entre 0,0 y 1,0.

### <a name="manual-focus-example"></a>Ejemplo de enfoque manual

Con el código de configuración de la captura de AV general en su lugar, `UIViewController` se puede Agregar al guión gráfico de la aplicación y configurarlo de la manera siguiente:

[![Un UIViewController se puede Agregar al guión gráfico de las aplicaciones y configurarse como se muestra aquí para el ejemplo de enfoque manual.](intro-to-manual-camera-controls-images/image5.png)](intro-to-manual-camera-controls-images/image5.png#lightbox)

La vista contiene los siguientes elementos principales:

- `UIImageView`Que mostrará la fuente de vídeo.
- `UISegmentedControl`Que cambiará el modo de enfoque de automático a bloqueado.
- `UISlider`Que mostrará y actualizará la posición actual del objetivo.

Realice lo siguiente para conectar el controlador de vista para el control de foco manual:

1. Agregue las siguientes instrucciones using:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```

1. Agregue las siguientes variables privadas:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```

1. Agregue las siguientes propiedades calculadas:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. Invalide el `ViewDidLoad` método y agregue el código siguiente:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Position.BeginInvokeOnMainThread(() =>{
                Position.Value = ThisApp.Input.Device.LensPosition;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {

            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);

            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto focus and start monitoring position
                Position.Enabled = false;
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.ContinuousAutoFocus;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Stop auto focus and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.FocusMode = AVCaptureFocusMode.Locked;
                Automatic = false;
                Position.Enabled = true;
                break;
            }

            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };

        // Monitor position changes
        Position.ValueChanged += (object sender, EventArgs e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic) return;

            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetFocusModeLocked(Position.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```

1. Invalide el `ViewDidAppear` método y agregue lo siguiente para iniciar la grabación cuando se cargue la vista:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;

            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```

1. Con la cámara en el modo auto, el control deslizante se moverá automáticamente cuando la cámara ajuste el foco:

    [![El control deslizante se moverá automáticamente cuando la cámara ajuste el foco en esta aplicación de ejemplo.](intro-to-manual-camera-controls-images/image6.png)](intro-to-manual-camera-controls-images/image6.png#lightbox)
1. Puntee en el segmento bloqueado y arrastre el control deslizante posición para ajustar la posición del objetivo manualmente:

    [![Ajuste manual de la posición de la lente](intro-to-manual-camera-controls-images/image7.png)](intro-to-manual-camera-controls-images/image7.png#lightbox)
1. Detenga la aplicación.

En el código anterior se ha mostrado cómo supervisar la posición de la lente cuando la cámara está en modo automático o usar un control deslizante para controlar la posición de la lente cuando está en modo bloqueado.

## <a name="manual-exposure"></a>Exposición manual

La exposición se refiere al brillo de una imagen en relación con el brillo del origen y se determina por la cantidad de luz que llega al sensor, durante cuánto tiempo y por el nivel de ganancia del sensor (asignación ISO). Al proporcionar un control manual sobre la exposición, una aplicación puede proporcionar más libertad al usuario final y permitirles obtener una apariencia estilizada.

Mediante el uso de los controles de exposición manual, el usuario puede tomar una imagen de poco realista a oscuro y Moody:

[![Un ejemplo de una imagen que muestra la exposición de brillante a Dark y Moody](intro-to-manual-camera-controls-images/image8.png)](intro-to-manual-camera-controls-images/image8.png#lightbox)

De nuevo, esto se puede hacer automáticamente mediante el control de programación para aplicaciones científicas o a través de controles manuales proporcionados por la interfaz de usuario de aplicaciones. En cualquier caso, las nuevas API de exposición de iOS 8 proporcionan un control exhaustivo sobre la configuración de exposición de la cámara.

### <a name="how-exposure-works"></a>Cómo funciona la exposición

Antes de analizar los detalles del control de la exposición en una aplicación de IOS 8. Echemos un vistazo rápido a cómo funciona la exposición:

[![Cómo funciona la exposición](intro-to-manual-camera-controls-images/image9.png)](intro-to-manual-camera-controls-images/image9.png#lightbox)

Los tres elementos básicos que se reúnen para controlar la exposición son:

- **Velocidad de obturación** : es el período de tiempo que está abierto el obturador para que se ilumine en el sensor de cámara. Cuanto más corta sea el obturador abierto, menos claro es dejar que la imagen sea más nítida (menos el desenfoque de movimiento). Cuanto más tiempo esté abierto el obturador, mayor será la luz y más desenfoque de movimiento que se produzca.
- **Asignación ISO** : se trata de un término que se toma prestado de la fotografía de la película y hace referencia a la confidencialidad de los productos químicos de la película que se van a aclarar. Los valores ISO bajos de la película tienen menos grano y una reproducción de color más fina. los valores ISO bajos de los sensores digitales tienen menos ruido del sensor pero menos brillo. Cuanto mayor sea el valor ISO, más brillante será la imagen, pero con más ruido de sensor. "ISO" en un sensor digital es una medida de [ganancia electrónica](https://en.wikipedia.org/wiki/Gain), no de una característica física.
- **Abertura de lente** : es el tamaño de la apertura de la lente. En todos los dispositivos iOS, la abertura de la lente es fija, por lo que los únicos dos valores que se pueden usar para ajustar la exposición son la velocidad de obturación e ISO.

### <a name="how-continuous-auto-exposure-works"></a>Cómo funciona la exposición automática continua

Antes de aprender cómo funciona la exposición manual, es una buena idea comprender cómo funciona la exposición automática continua en un dispositivo iOS.

[![Cómo funciona la exposición automática continua en un dispositivo iOS](intro-to-manual-camera-controls-images/image10.png)](intro-to-manual-camera-controls-images/image10.png#lightbox)

En primer lugar, el bloque de exposición automática tiene el trabajo de calcular la exposición ideal y se está introduciendo continuamente estadísticas de medición. Utiliza esta información para calcular la mezcla óptima de ISO y la velocidad de obturación para conseguir que la escena esté bien iluminada. Este ciclo se conoce como el bucle AE.

### <a name="how-locked-exposure-works"></a>Cómo funciona la exposición bloqueada

A continuación, vamos a examinar cómo funciona la exposición bloqueada en dispositivos iOS.

[![Cómo funciona la exposición bloqueada en dispositivos iOS](intro-to-manual-camera-controls-images/image11.png)](intro-to-manual-camera-controls-images/image11.png#lightbox)

De nuevo, tiene el bloque de exposición automática que está intentando calcular los valores óptimos de iOS y Duration. Sin embargo, en este modo, el bloque AE está desconectado del motor de estadísticas de medición.

### <a name="existing-exposure-controls"></a>Controles de exposición existentes

iOS 7 y versiones posteriores, proporcionan los siguientes controles de exposición existentes a través de la `ExposureMode` propiedad:

- `AVCaptureExposureModeLocked` : Muestrea la escena una vez y usa esos valores en toda la escena.
- `AVCaptureExposureModeContinuousAutoExposure` : Muestrea la escena continuamente para asegurarse de que está bien iluminada.

`ExposurePointOfInterest`Se puede usar para pulsar para exponer la escena seleccionando un objeto de destino que se va a exponer y la aplicación puede supervisar la `AdjustingExposure` propiedad para ver cuándo se está ajustando la exposición.

### <a name="new-exposure-controls-in-ios-8"></a>Nuevos controles de exposición en iOS 8

Además de las características ya proporcionadas por iOS 7 y versiones posteriores, las siguientes características están ahora disponibles para controlar la exposición en iOS 8:

- Exposición personalizada totalmente manual.
- Get, Set y Key-Value observar la velocidad de IOS y obturador (duración).

Para implementar las características anteriores, se ha `AVCaptureExposureModeCustom` agregado un nuevo modo. Cuando la cámara en es el modo personalizado, se puede usar el siguiente código para ajustar la duración de la exposición e ISO:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.LockExposure(DurationValue,ISOValue,null);
CaptureDevice.UnlockForConfiguration();
```

En los modos automático y bloqueado, la aplicación puede ajustar la diferencia de la rutina de exposición automática mediante el código siguiente:

```csharp
CaptureDevice.LockForConfiguration(out Error);
CaptureDevice.SetExposureTargetBias(Value,null);
CaptureDevice.UnlockForConfiguration();
```

Los intervalos de configuración mínima y máxima dependen del dispositivo en el que se ejecuta la aplicación, por lo que nunca deben estar codificados de forma rígida. En su lugar, use las siguientes propiedades para obtener los intervalos de valores mínimo y máximo:

- `CaptureDevice.MinExposureTargetBias`
- `CaptureDevice.MaxExposureTargetBias`
- `CaptureDevice.ActiveFormat.MinISO`
- `CaptureDevice.ActiveFormat.MaxISO`
- `CaptureDevice.ActiveFormat.MinExposureDuration`
- `CaptureDevice.ActiveFormat.MaxExposureDuration`

Tal como se ha mostrado en el código anterior, el dispositivo de captura debe estar bloqueado para la configuración antes de que se pueda realizar un cambio de exposición.

### <a name="manual-exposure-example"></a>Ejemplo de exposición manual

Con el código de configuración de la captura de AV general en su lugar, `UIViewController` se puede Agregar al guión gráfico de la aplicación y configurarlo de la manera siguiente:

[![Un UIViewController se puede Agregar al guión gráfico de las aplicaciones y configurarse como se muestra aquí para el ejemplo de exposición manual.](intro-to-manual-camera-controls-images/image12.png)](intro-to-manual-camera-controls-images/image12.png#lightbox)

La vista contiene los siguientes elementos principales:

- `UIImageView`Que mostrará la fuente de vídeo.
- `UISegmentedControl`Que cambiará el modo de enfoque de automático a bloqueado.
- Cuatro  `UISlider` controles que mostrarán y actualizarán el desplazamiento, la duración, la ISO y la diferencia.

Realice lo siguiente para conectar el controlador de vista para el control de exposición manual:

1. Agregue las siguientes instrucciones using:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```

1. Agregue las siguientes variables privadas:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    private nfloat ExposureDurationPower = 5;
    private nfloat ExposureMinimumDuration = 1.0f/1000.0f;
    #endregion
    ```

1. Agregue las siguientes propiedades calculadas:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. Invalide el `ViewDidLoad` método y agregue el código siguiente:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Offset.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Offset.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;

        Duration.MinValue = 0.0f;
        Duration.MaxValue = 1.0f;

        ISO.MinValue = ThisApp.CaptureDevice.ActiveFormat.MinISO;
        ISO.MaxValue = ThisApp.CaptureDevice.ActiveFormat.MaxISO;

        Bias.MinValue = ThisApp.CaptureDevice.MinExposureTargetBias;
        Bias.MaxValue = ThisApp.CaptureDevice.MaxExposureTargetBias;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Update position slider
            Offset.BeginInvokeOnMainThread(() =>{
                Offset.Value = ThisApp.Input.Device.ExposureTargetOffset;
            });

            Duration.BeginInvokeOnMainThread(() =>{
                var newDurationSeconds = CMTimeGetSeconds(ThisApp.Input.Device.ExposureDuration);
                var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration), ExposureMinimumDuration);
                var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
                var p = (newDurationSeconds - minDurationSeconds) / (maxDurationSeconds - minDurationSeconds);
                Duration.Value = (float)Math.Pow(p, 1.0f/ExposureDurationPower);
            });

            ISO.BeginInvokeOnMainThread(() => {
                ISO.Value = ThisApp.Input.Device.ISO;
            });

            Bias.BeginInvokeOnMainThread(() => {
                Bias.Value = ThisApp.Input.Device.ExposureTargetBias;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (object sender, EventArgs e) => {

            // Lock device for change
            ThisApp.CaptureDevice.LockForConfiguration(out Error);

            // Take action based on the segment selected
            switch(Segments.SelectedSegment) {
            case 0:
                // Activate auto exposure and start monitoring position
                Duration.Enabled = false;
                ISO.Enabled = false;
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.ContinuousAutoExposure;
                SampleTimer.Start();
                Automatic = true;
                break;
            case 1:
                // Lock exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Locked;
                Automatic = false;
                Duration.Enabled = false;
                ISO.Enabled = false;
                break;
            case 2:
                // Custom exposure and allow the user to control the camera
                SampleTimer.Stop();
                ThisApp.CaptureDevice.ExposureMode = AVCaptureExposureMode.Custom;
                Automatic = false;
                Duration.Enabled = true;
                ISO.Enabled = true;
                break;
            }

            // Unlock device
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };

        // Monitor position changes
        Duration.ValueChanged += (object sender, EventArgs e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic) return;

            // Calculate value
            var p = Math.Pow(Duration.Value,ExposureDurationPower);
            var minDurationSeconds = Math.Max(CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MinExposureDuration),ExposureMinimumDuration);
            var maxDurationSeconds = CMTimeGetSeconds(ThisApp.CaptureDevice.ActiveFormat.MaxExposureDuration);
            var newDurationSeconds = p * (maxDurationSeconds - minDurationSeconds) +minDurationSeconds;

            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(CMTime.FromSeconds(p,1000*1000*1000),ThisApp.CaptureDevice.ISO,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };

        ISO.ValueChanged += (object sender, EventArgs e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic) return;

            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.LockExposure(ThisApp.CaptureDevice.ExposureDuration,ISO.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };

        Bias.ValueChanged += (object sender, EventArgs e) => {

            // If we are in the automatic mode, ignore changes
            // if (Automatic) return;

            // Update Focus position
            ThisApp.CaptureDevice.LockForConfiguration(out Error);
            ThisApp.CaptureDevice.SetExposureTargetBias(Bias.Value,null);
            ThisApp.CaptureDevice.UnlockForConfiguration();
        };
    }
    ```

1. Invalide el `ViewDidAppear` método y agregue lo siguiente para iniciar la grabación cuando se cargue la vista:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;

            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```

1. Con la cámara en el modo auto, los controles deslizantes se mueven automáticamente cuando la cámara ajusta la exposición:

    [![Los controles deslizantes se mueven automáticamente cuando la cámara ajusta la exposición](intro-to-manual-camera-controls-images/image13.png)](intro-to-manual-camera-controls-images/image13.png#lightbox)
1. Puntee en el segmento bloqueado y arrastre el control deslizante de sesgo para ajustar la diferencia de la exposición automática manualmente:

    [![Ajuste manual de la desviación de la exposición automática](intro-to-manual-camera-controls-images/image14.png)](intro-to-manual-camera-controls-images/image14.png#lightbox)
1. Puntee en el segmento personalizado y arrastre los controles deslizantes de duración e ISO para controlar manualmente la exposición:

    [![Arrastre los controles deslizante de duración e ISO para controlar la exposición manualmente](intro-to-manual-camera-controls-images/image15.png)](intro-to-manual-camera-controls-images/image15.png#lightbox)
1. Detenga la aplicación.

En el código anterior se ha mostrado cómo supervisar la configuración de exposición cuando la cámara está en modo automático y cómo usar los controles deslizantes para controlar la exposición cuando está en los modos bloqueado o personalizado.

## <a name="manual-white-balance"></a>Balance manual de blancos

Los controles de balance de blanco permiten a los usuarios ajustar el equilibrio de colosr en una imagen para que tengan un aspecto más realista. Diferentes fuentes de luz tienen temperaturas de color diferentes y la configuración de la cámara utilizada para capturar una imagen debe ajustarse para compensar estas diferencias. De nuevo, al permitir el control del usuario sobre el balance de blanco, pueden realizarse ajustes profesionales en los que las rutinas automáticas no pueden conseguir efectos artísticos.

[![Imagen de ejemplo que muestra los ajustes manuales de balance de blanco](intro-to-manual-camera-controls-images/image16.png)](intro-to-manual-camera-controls-images/image16.png#lightbox)

Por ejemplo, el horario de verano tiene una conversión azul, mientras que las luces incandescentes Tungsten tienen un matiz más cálido, naranja-naranja. (Confuso, los colores "esporádicos" tienen temperaturas de color más altas que los colores "semiactivos". Las temperaturas de color son una medida física, no una perceptual.

La mente humana es muy buena para compensar las diferencias en la temperatura del color, pero esto es algo que una cámara no puede hacer. La cámara funciona aumentando el color en el espectro opuesto para ajustarse a las diferencias de color.

La nueva API de exposición de iOS 8 permite que la aplicación tome el control del proceso y proporcione un control exhaustivo sobre la configuración de equilibrio de blancos de la cámara.

### <a name="how-white-balance-works"></a>Cómo funciona el equilibrio de blancos

Antes de analizar los detalles del control del equilibrio de blanco en una aplicación de IOS 8. Echemos un vistazo rápido a cómo funciona el equilibrio de blancos:

En el estudio de la percepción de color, el [espacio de colores de cie 1931 RGB y el espacio de colores cie 1931 XYZ](https://en.wikipedia.org/wiki/CIE_1931_color_space) son los primeros espacios de colores definidos matemáticamente. Fueron creados por la Comisión Internacional en iluminación (CIE) en 1931.

[![El espacio de colores CIE 1931 RGB y el espacio de colores CIE 1931 XYZ](intro-to-manual-camera-controls-images/image17.png)](intro-to-manual-camera-controls-images/image17.png#lightbox)

En el gráfico anterior se muestran todos los colores visibles para el ojo humano, desde el azul profundo hasta el verde brillante hasta el rojo brillante. Cualquier punto del diagrama se puede trazar con un valor X e y, tal y como se muestra en el gráfico anterior.

Como se muestra en el gráfico, hay valores X e y que se pueden trazar en el gráfico que estarán fuera del intervalo de visión humana y, como resultado, estos colores no se pueden reproducir mediante una cámara.

La curva más pequeña del gráfico anterior se denomina [Planckian raíz](https://en.wikipedia.org/wiki/Planckian_locus), que expresa la temperatura del color (en grados Kelvin), con números mayores en el lado azul (más cálido) y números inferiores en el lado rojo (refrigerado). Son útiles para las situaciones de iluminación típicas.

En condiciones de iluminación mixta, los ajustes del balance de blanco deberán desviarse de la raíz de Planckian para realizar los cambios necesarios. En estas situaciones, el ajuste tendrá que desplazarse al lado verde o rojo/magenta de la escala de CIE.

los dispositivos iOS compensan las conversiones de color aumentando la ganancia de color opuesta. Por ejemplo, si una escena tiene demasiado azul, el aumento rojo se incrementará para compensar. Estos valores de ganancia se calibran para dispositivos específicos para que sean dependientes del dispositivo.

### <a name="existing-white-balance-controls"></a>Controles de balance de blanco existentes

iOS 7 y versiones posteriores proporcionaron los siguientes controles de equilibrio de blancos existentes a través de la `WhiteBalanceMode` propiedad:

- `AVCapture WhiteBalance ModeLocked` : Muestrea la escena una vez y usa esos valores en toda la escena.
- `AVCapture WhiteBalance ModeContinuousAutoExposure` : Muestrea la escena continuamente para asegurarse de que está bien equilibrada.

Y la aplicación puede supervisar la `AdjustingWhiteBalance` propiedad para ver cuándo se está ajustando la exposición.

### <a name="new-white-balance-controls-in-ios-8"></a>Nuevos controles de equilibrio de blancos en iOS 8

Además de las características ya proporcionadas por iOS 7 y versiones posteriores, las siguientes características están ahora disponibles para controlar el equilibrio de blancos en iOS 8:

- Control totalmente manual del aumento de RGB del dispositivo.
- Get, Set y Key-Value observar el aumento de RGB del dispositivo.
- Compatibilidad con el balance de blanco mediante una tarjeta gris.
- Rutinas de conversión a y desde espacios de colores independientes del dispositivo.

Para implementar las características anteriores, la `AVCaptureWhiteBalanceGain` estructura se ha agregado con los siguientes miembros:

- `RedGain`
- `GreenGain`
- `BlueGain`

La ganancia máxima de balance de blanco es actualmente cuatro (4) y puede estar preparada desde la `MaxWhiteBalanceGain` propiedad. Por lo tanto, el intervalo válido es de uno (1) a `MaxWhiteBalanceGain` (4) actualmente.

La `DeviceWhiteBalanceGains` propiedad se puede utilizar para observar los valores actuales. Use `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` para ajustar el saldo de ganancia cuando la cámara está en el modo de equilibrio de blanco bloqueado.

#### <a name="conversion-routines"></a>Rutinas de conversión

Se han agregado rutinas de conversión a iOS 8 para ayudar en la conversión de espacios de colores independientes del dispositivo a y desde ellos. Para implementar las rutinas de conversión, se ha `AVCaptureWhiteBalanceChromaticityValues` agregado la estructura con los siguientes miembros:

- `X` : es un valor comprendido entre 0 y 1.
- `Y` : es un valor comprendido entre 0 y 1.

También se ha `AVCaptureWhiteBalanceTemperatureAndTintValues` agregado una estructura con los siguientes miembros:

- `Temperature` : es un valor de punto flotante en grados Kelvin.
- `Tint` : es un desplazamiento desde el verde o el magenta desde 0 hasta 150 con valores positivos hacia la dirección verde y negativos hacia el magenta.

Use los `CaptureDevice.GetTemperatureAndTintValues` métodos y `CaptureDevice.GetDeviceWhiteBalanceGains` para realizar conversiones entre los espacios de colores de la temperatura y el matiz, la cromaticidad y la ganancia de RGB.

> [!NOTE]
> Las rutinas de conversión son más precisas cuanto más se acerque el valor que se va a convertir a Planckian raíz.

#### <a name="gray-card-support"></a>Compatibilidad con tarjetas grises

Apple usa el término "mundo gris" para hacer referencia a la compatibilidad con tarjetas atenuadas integradas en iOS 8. Permite al usuario centrarse en una tarjeta gris física que cubre al menos el 50% del centro del fotograma y lo usa para ajustar el balance de blanco. El propósito de la tarjeta gris es conseguir un blanco que parezca neutro.

Esto se puede implementar en una aplicación solicitando al usuario que coloque una tarjeta gris física delante de la cámara, supervisando la `GrayWorldDeviceWhiteBalanceGains` propiedad y esperando hasta que los valores se liquiden.

A continuación, la aplicación bloqueará las ganancias de equilibrio de blanco para el `SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains` método usando los valores de la `GrayWorldDeviceWhiteBalanceGains` propiedad para aplicar los cambios.

El dispositivo de captura debe estar bloqueado para la configuración antes de que se pueda realizar un cambio en el equilibrio de blanco.

### <a name="manual-white-balance-example"></a>Ejemplo manual de balance de blanco

Con el código de configuración de la captura de AV general en su lugar, `UIViewController` se puede Agregar al guión gráfico de la aplicación y configurarlo de la manera siguiente:

[![Un UIViewController se puede Agregar al guión gráfico de las aplicaciones y configurarse como se muestra aquí para el ejemplo de balance de blanco manual.](intro-to-manual-camera-controls-images/image18.png)](intro-to-manual-camera-controls-images/image18.png#lightbox)

La vista contiene los siguientes elementos principales:

- `UIImageView`Que mostrará la fuente de vídeo.
- `UISegmentedControl`Que cambiará el modo de enfoque de automático a bloqueado.
- Dos  `UISlider` controles que mostrarán y actualizarán la temperatura y el matiz.
- `UIButton`Que se usa para muestrear un espacio de tarjeta gris (mundo gris) y establecer el balance de blanco con esos valores.

Realice lo siguiente para conectar el controlador de vista para el control manual de balance de blanco:

1. Agregue las siguientes instrucciones using:

    ```csharp
    using System;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using System.Timers;
    ```

1. Agregue las siguientes variables privadas:

    ```csharp
    #region Private Variables
    private NSError Error;
    private bool Automatic = true;
    #endregion
    ```

1. Agregue las siguientes propiedades calculadas:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    public Timer SampleTimer { get; set; }
    #endregion
    ```

1. Agregue el siguiente método privado para establecer el nuevo equilibrio de blanco temperatura y matiz:

    ```csharp
    #region Private Methods
    void SetTemperatureAndTint() {
        // Grab current temp and tint
        var TempAndTint = new AVCaptureWhiteBalanceTemperatureAndTintValues (Temperature.Value, Tint.Value);

        // Convert Color space
        var gains = ThisApp.CaptureDevice.GetDeviceWhiteBalanceGains (TempAndTint);

        // Set the new values
        if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
            gains = NomralizeGains (gains);
            ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
            ThisApp.CaptureDevice.UnlockForConfiguration ();
        }
    }

    AVCaptureWhiteBalanceGains NomralizeGains (AVCaptureWhiteBalanceGains gains)
    {
        gains.RedGain = Math.Max (1, gains.RedGain);
        gains.BlueGain = Math.Max (1, gains.BlueGain);
        gains.GreenGain = Math.Max (1, gains.GreenGain);

        float maxGain = ThisApp.CaptureDevice.MaxWhiteBalanceGain;
        gains.RedGain = Math.Min (maxGain, gains.RedGain);
        gains.BlueGain = Math.Min (maxGain, gains.BlueGain);
        gains.GreenGain = Math.Min (maxGain, gains.GreenGain);

        return gains;
    }
    #endregion
    ```

1. Invalide el `ViewDidLoad` método y agregue el código siguiente:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Set min and max values
        Temperature.MinValue = 1000f;
        Temperature.MaxValue = 10000f;

        Tint.MinValue = -150f;
        Tint.MaxValue = 150f;

        // Create a timer to monitor and update the UI
        SampleTimer = new Timer (5000);
        SampleTimer.Elapsed += (sender, e) => {
            // Convert color space
            var TempAndTint = ThisApp.CaptureDevice.GetTemperatureAndTintValues (ThisApp.CaptureDevice.DeviceWhiteBalanceGains);

            // Update slider positions
            Temperature.BeginInvokeOnMainThread (() => {
                Temperature.Value = TempAndTint.Temperature;
            });

            Tint.BeginInvokeOnMainThread (() => {
                Tint.Value = TempAndTint.Tint;
            });
        };

        // Watch for value changes
        Segments.ValueChanged += (sender, e) => {
            // Lock device for change
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {

                // Take action based on the segment selected
                switch (Segments.SelectedSegment) {
                case 0:
                // Activate auto focus and start monitoring position
                    Temperature.Enabled = false;
                    Tint.Enabled = false;
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.ContinuousAutoWhiteBalance;
                    SampleTimer.Start ();
                    Automatic = true;
                    break;
                case 1:
                // Stop auto focus and allow the user to control the camera
                    SampleTimer.Stop ();
                    ThisApp.CaptureDevice.WhiteBalanceMode = AVCaptureWhiteBalanceMode.Locked;
                    Automatic = false;
                    Temperature.Enabled = true;
                    Tint.Enabled = true;
                    break;
                }

                // Unlock device
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };

        // Monitor position changes
        Temperature.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        Tint.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Update white balance
            SetTemperatureAndTint ();
        };

        GrayCardButton.TouchUpInside += (sender, e) => {

            // If we are in the automatic mode, ignore changes
            if (Automatic)
                return;

            // Get gray card values
            var gains = ThisApp.CaptureDevice.GrayWorldDeviceWhiteBalanceGains;

            // Set the new values
            if (ThisApp.CaptureDevice.LockForConfiguration (out Error)) {
                ThisApp.CaptureDevice.SetWhiteBalanceModeLockedWithDeviceWhiteBalanceGains (gains, null);
                ThisApp.CaptureDevice.UnlockForConfiguration ();
            }
        };
    }
    ```

1. Invalide el `ViewDidAppear` método y agregue lo siguiente para iniciar la grabación cuando se cargue la vista:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;

            ThisApp.Session.StartRunning ();
            SampleTimer.Start ();
        }
    }
    ```

1. Guarde los cambios en el código y ejecute la aplicación.
1. Con la cámara en el modo auto, los controles deslizantes se mueven automáticamente a medida que la cámara ajusta el balance de blanco:

    [![Los controles deslizantes se desplazarán automáticamente a medida que la cámara ajuste el balance de blanco](intro-to-manual-camera-controls-images/image19.png)](intro-to-manual-camera-controls-images/image19.png#lightbox)
1. Puntee en el segmento bloqueado y arrastre los controles deslizantes Temp y matiz para ajustar el balance de blanco manualmente:

    [![Arrastre los controles deslizantes Temp y matiz para ajustar el balance de blanco manualmente](intro-to-manual-camera-controls-images/image20.png)](intro-to-manual-camera-controls-images/image20.png#lightbox)
1. Con el segmento bloqueado todavía seleccionado, coloque una tarjeta gris física delante de la cámara y pulse el botón de la tarjeta gris para ajustar el balance blanco al mundo gris:

    [![Puntee en el botón de tarjeta gris para ajustar el balance de blanco al mundo gris](intro-to-manual-camera-controls-images/image21.png)](intro-to-manual-camera-controls-images/image21.png#lightbox)
1. Detenga la aplicación.

En el código anterior se ha mostrado cómo supervisar la configuración de equilibrio de blanco cuando la cámara está en modo automático o usar controles deslizantes para controlar el equilibrio de blanco cuando está en modo bloqueado.

## <a name="bracketed-capture"></a>Captura entre corchetes

La captura entre corchetes se basa en la configuración de los controles de la cámara manual presentada anteriormente y permite a la aplicación capturar un momento dado, de varias maneras diferentes.

Simplemente se ha indicado que la captura entre corchetes es una ráfaga de imágenes fijas tomadas con una variedad de opciones de imagen a imagen.

[![Cómo funciona la captura entre corchetes](intro-to-manual-camera-controls-images/image22.png)](intro-to-manual-camera-controls-images/image22.png#lightbox)

Con la captura entre corchetes de iOS 8, una aplicación puede establecer valores predeterminados de una serie de controles de cámara manuales, emitir un solo comando y hacer que la escena actual devuelva una serie de imágenes para cada uno de los valores preestablecidos manuales.

### <a name="bracketed-capture-basics"></a>Aspectos básicos de la captura entre corchetes

De nuevo, la captura entre corchetes es una ráfaga de imágenes fijas tomadas con distintos valores de la imagen a la imagen. Los tipos de captura entre corchetes disponibles son:

- **Corchete de exposición automática** , donde todas las imágenes tienen una cantidad de sesgo variable.
- **Corchete de exposición manual** , donde todas las imágenes tienen una velocidad de obturador variable (duración) y una cantidad ISO.
- **Corchete de ráfaga simple** : una serie de imágenes fijas tomadas en una sucesión rápida.

### <a name="new-bracketed-capture-controls-in-ios-8"></a>Nuevos controles de captura entre corchetes en iOS 8

Todos los comandos de captura entre corchetes se implementan en la `AVCaptureStillImageOutput` clase. Use el `CaptureStillImageBracket` método para obtener una serie de imágenes con la matriz de valores especificada.

Se han implementado dos nuevas clases para controlar la configuración:

- `AVCaptureAutoExposureBracketedStillImageSettings` : Tiene una propiedad,  `ExposureTargetBias` , que se usa para establecer la diferencia para un corchete de exposición automática.
- `AVCaptureManual`  `ExposureBracketedStillImageSettings` : Tiene dos propiedades,  `ExposureDuration` y  `ISO` , que se usan para establecer la velocidad del obturador e ISO para un corchete de exposición manual.

### <a name="bracketed-capture-controls-dos-and-donts"></a>Los controles de captura entre corchetes hacen y no

#### <a name="dos"></a>Qué hacer

A continuación se muestra una lista de las cosas que deben realizarse cuando se usan los controles de captura entre corchetes en iOS 8:

- Prepare la aplicación para la situación de captura en el peor de los casos llamando al  `PrepareToCaptureStillImageBracket` método.
- Suponga que los búferes de ejemplo van a provienen del mismo grupo compartido.
- Para liberar la memoria asignada por una llamada a Prepare anterior, llame  `PrepareToCaptureStillImageBracket` de nuevo a y envíelo a una matriz de un objeto.

#### <a name="donts"></a>Qué no hacer

A continuación se muestra una lista de las cosas que no se deben realizar cuando se usan los controles de captura entre corchetes en iOS 8:

- No mezcle tipos de configuración de captura entre corchetes en una sola captura.
- No solicite más de  `MaxBracketedCaptureStillImageCount` imágenes en una sola captura.

### <a name="bracketed-capture-details"></a>Detalles de la captura entre corchetes

Se deben tener en cuenta los siguientes detalles al trabajar con la captura entre corchetes en iOS 8:

- La configuración entre corchetes invalida temporalmente la  `AVCaptureDevice` configuración.
- Se omite la configuración de la estabilización de imágenes y de Flash.
- Todas las imágenes deben usar el mismo formato de salida (JPEG, PNG, etc.).
- La vista previa de vídeo puede quitar fotogramas.
- La captura entre corchetes se admite en todos los dispositivos compatibles con iOS 8.

Teniendo en cuenta esta información, echemos un vistazo a un ejemplo del uso de la captura entre corchetes en iOS 8.

### <a name="bracket-capture-example"></a>Ejemplo de captura de corchetes

Con el código de configuración de la captura de AV general en su lugar, `UIViewController` se puede Agregar al guión gráfico de la aplicación y configurarlo de la manera siguiente:

[![Un UIViewController se puede Agregar al guión gráfico de las aplicaciones y configurarse como se muestra aquí ejemplo de captura de corchetes.](intro-to-manual-camera-controls-images/image23.png)](intro-to-manual-camera-controls-images/image23.png#lightbox)

La vista contiene los siguientes elementos principales:

- `UIImageView`Que mostrará la fuente de vídeo.
- Tres  `UIImageViews` que mostrarán los resultados de la captura.
- `UIScrollView`Para hospedar las vistas de fuente de vídeo y resultado.
- `UIButton`Que se usa para tomar una captura entre corchetes con algunas configuraciones preestablecidas.

Realice lo siguiente para conectar el controlador de vista para la captura entre corchetes:

1. Agregue las siguientes instrucciones using:

    ```csharp
    using System;
    using System.Drawing;
    using Foundation;
    using UIKit;
    using System.CodeDom.Compiler;
    using System.Collections.Generic;
    using System.Linq;
    using AVFoundation;
    using CoreVideo;
    using CoreMedia;
    using CoreGraphics;
    using CoreFoundation;
    using CoreImage;
    ```

1. Agregue las siguientes variables privadas:

    ```csharp
    #region Private Variables
    private NSError Error;
    private List<UIImageView> Output = new List<UIImageView>();
    private nint OutputIndex = 0;
    #endregion
    ```

1. Agregue las siguientes propiedades calculadas:

    ```csharp
    #region Computed Properties
    public AppDelegate ThisApp {
        get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
    }
    #endregion
    ```

1. Agregue el siguiente método privado para compilar las vistas de imagen de salida requeridas:

    ```csharp
    #region Private Methods
    private UIImageView BuildOutputView(nint n) {

        // Create a new image view controller
        var imageView = new UIImageView (new CGRect (CameraView.Frame.Width * n, 0, CameraView.Frame.Width, CameraView.Frame.Height));

        // Load a temp image
        imageView.Image = UIImage.FromFile ("Default-568h@2x.png");

        // Add a label
        UILabel label = new UILabel (new CGRect (0, 20, CameraView.Frame.Width, 24));
        label.TextColor = UIColor.White;
        label.Text = string.Format ("Bracketed Image {0}", n);
        imageView.AddSubview (label);

        // Add to scrolling view
        ScrollView.AddSubview (imageView);

        // Return new image view
        return imageView;
    }
    #endregion
    ```

1. Invalide el `ViewDidLoad` método y agregue el código siguiente:

    ```csharp
    public override void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        // Hide no camera label
        NoCamera.Hidden = ThisApp.CameraAvailable;

        // Attach to camera view
        ThisApp.Recorder.DisplayView = CameraView;

        // Setup scrolling area
        ScrollView.ContentSize = new SizeF (CameraView.Frame.Width * 4, CameraView.Frame.Height);

        // Add output views
        Output.Add (BuildOutputView (1));
        Output.Add (BuildOutputView (2));
        Output.Add (BuildOutputView (3));

        // Create preset settings
        var Settings = new AVCaptureBracketedStillImageSettings[] {
            AVCaptureAutoExposureBracketedStillImageSettings.Create(-2.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(0.0f),
            AVCaptureAutoExposureBracketedStillImageSettings.Create(2.0f)
        };

        // Wireup capture button
        CaptureButton.TouchUpInside += (sender, e) => {
            // Reset output index
            OutputIndex = 0;

            // Tell the camera that we are getting ready to do a bracketed capture
            ThisApp.StillImageOutput.PrepareToCaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings,async (bool ready, NSError err) => {
                // Was there an error, if so report it
                if (err!=null) {
                    Console.WriteLine("Error: {0}",err.LocalizedDescription);
                }
            });

            // Ask the camera to snap a bracketed capture
            ThisApp.StillImageOutput.CaptureStillImageBracket(ThisApp.StillImageOutput.Connections[0],Settings, (sampleBuffer, settings, err) =>{
                // Convert raw image stream into a Core Image Image
                var imageData = AVCaptureStillImageOutput.JpegStillToNSData(sampleBuffer);
                var image = CIImage.FromData(imageData);

                // Display the resulting image
                Output[OutputIndex++].Image = UIImage.FromImage(image);

                // IMPORTANT: You must release the buffer because AVFoundation has a fixed number
                // of buffers and will stop delivering frames if it runs out.
                sampleBuffer.Dispose();
            });
        };
    }
    ```

1. Invalide el `ViewDidAppear` método y agregue el código siguiente:

    ```csharp
    public override void ViewDidAppear (bool animated)
    {
        base.ViewDidAppear (animated);

        // Start udating the display
        if (ThisApp.CameraAvailable) {
            // Remap to this camera view
            ThisApp.Recorder.DisplayView = CameraView;

            ThisApp.Session.StartRunning ();
        }
    }

    ```

1. Guarde los cambios en el código y ejecute la aplicación.
1. Enmarcar una escena y pulsar el botón de corchete de captura:

    [![Enmarcar una escena y pulsar el botón de corchete de captura](intro-to-manual-camera-controls-images/image24.png)](intro-to-manual-camera-controls-images/image24.png#lightbox)
1. Deslice el dedo de derecha a izquierda para ver las tres imágenes tomadas por la captura entre corchetes:

    [![Deslice el dedo de derecha a izquierda para ver las tres imágenes tomadas por la captura entre corchetes](intro-to-manual-camera-controls-images/image25.png)](intro-to-manual-camera-controls-images/image25.png#lightbox)
1. Detenga la aplicación.

En el código anterior se ha mostrado cómo configurar y realizar una captura entre corchetes de exposición automática en iOS 8.

## <a name="summary"></a>Resumen

En este artículo se ha tratado una introducción a los nuevos controles de cámara manuales que proporciona iOS 8 y se han tratado los aspectos básicos de lo que hacen y cómo funcionan. Hemos proporcionado ejemplos de enfoque manual, exposición manual y balance manual de blancos. Por último, hemos dado un ejemplo en el que se realiza una captura entre corchetes mediante los controles de cámara manuales descritos anteriormente.

## <a name="related-links"></a>Vínculos relacionados

- [ManualCameraControls (ejemplo)](/samples/xamarin/ios-samples/manualcameracontrols)
- [Introducción a iOS 8](~/ios/platform/introduction-to-ios8.md)