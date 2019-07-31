---
title: 'Tutorial: uso de Touch en Android'
ms.prod: xamarin
ms.assetid: E281F89B-4142-4BD8-8882-FB65508BF69E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/09/2018
ms.openlocfilehash: 78878e62a36e3f6dd6ca3c7fcfb6413da4f0e0f9
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644120"
---
# <a name="walkthrough---using-touch-in-android"></a>Tutorial: uso de Touch en Android

Vamos a ver cómo usar los conceptos de la sección anterior en una aplicación en funcionamiento. Crearemos una aplicación con cuatro actividades. La primera actividad será un menú o un panel de tareas que iniciará las otras actividades para mostrar las distintas API. En la captura de pantalla siguiente se muestra la actividad principal:

[![Captura de pantalla de ejemplo con el botón de Touch me](android-touch-walkthrough-images/image14.png)](android-touch-walkthrough-images/image14.png#lightbox)

La primera actividad, ejemplo táctil, mostrará cómo usar controladores de eventos para tocar las vistas. En la actividad del reconocedor de gestos se `Android.View.Views` muestra cómo subclases y control de eventos, así como cómo controlar los gestos de pinch. La tercera y última actividad, el **gesto personalizado**, mostrará cómo usar gestos personalizados. Para que los elementos sean más fáciles de seguir y absorber, vamos a desglosar este tutorial en secciones, donde cada sección se centra en una de las actividades.

## <a name="touch-sample-activity"></a>Actividad de ejemplo táctil

-   Abra el proyecto **TouchWalkthrough\_iniciar**. La **MainActivity** está configurada para &ndash; que se implemente el comportamiento táctil en la actividad. Si ejecuta la aplicación y hace clic en el **ejemplo Touch**, debe iniciarse la siguiente actividad:

    [![Se muestra una captura de pantalla de la actividad con la entrada táctil](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

-   Ahora que hemos confirmado que la actividad se inicia, abra el archivo **TouchActivity.CS** y agregue un controlador para el `Touch` evento del: `ImageView`

    ```csharp
    _touchMeImageView.Touch += TouchMeImageViewOnTouch;
    ```

-   A continuación, agregue el método siguiente a **TouchActivity.CS**:

    ```csharp
    private void TouchMeImageViewOnTouch(object sender, View.TouchEventArgs touchEventArgs)
    {
        string message;
        switch (touchEventArgs.Event.Action & MotionEventActions.Mask)
        {
            case MotionEventActions.Down:
            case MotionEventActions.Move:
            message = "Touch Begins";
            break;

            case MotionEventActions.Up:
            message = "Touch Ends";
            break;

            default:
            message = string.Empty;
            break;
        }

        _touchInfoTextView.Text = message;
    }
    ```

Observe que en el código anterior se trata la `Move` acción `Down` y como la misma. Esto se debe a que, aunque el usuario no levante el `ImageView`dedo, puede desplazarse o la presión ejercida por el usuario puede cambiar. Estos tipos de cambios generarán una `Move` acción.

Cada vez que el usuario toca `ImageView`el, `Touch` se generará el evento y el controlador mostrará el mensaje el **toque comienza** en la pantalla, tal como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de la actividad con Touch inicia](android-touch-walkthrough-images/image15.png)](android-touch-walkthrough-images/image15.png#lightbox)

Siempre que el usuario toque el `ImageView`, se mostrará la **entrada táctil** en el. `TextView` Cuando el usuario ya no toca el `ImageView`, se mostrarán los **extremos** del mensaje en el `TextView`, tal y como se muestra en la siguiente captura de pantalla:

[![Captura de pantalla de la actividad con los extremos táctiles](android-touch-walkthrough-images/image16.png)](android-touch-walkthrough-images/image16.png#lightbox)


## <a name="gesture-recognizer-activity"></a>Actividad de reconocedor de gestos

Ahora permite implementar la actividad del reconocedor de gestos. Esta actividad mostrará cómo arrastrar una vista alrededor de la pantalla y mostrar una manera de implementar el acercamiento a zoom.

-   Agregue una nueva actividad a la aplicación denominada `GestureRecognizer`.
    Edite el código de esta actividad para que sea similar al código siguiente:

    ```csharp
    public class GestureRecognizerActivity : Activity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            View v = new GestureRecognizerView(this);
            SetContentView(v);
        }
    }
    ```

-   Agregue una nueva vista de Android al proyecto y asígnele el nombre `GestureRecognizerView`. Agregue las siguientes variables a esta clase:

    ```csharp
    private static readonly int InvalidPointerId = -1;

    private readonly Drawable _icon;
    private readonly ScaleGestureDetector _scaleDetector;

    private int _activePointerId = InvalidPointerId;
    private float _lastTouchX;
    private float _lastTouchY;
    private float _posX;
    private float _posY;
    private float _scaleFactor = 1.0f;
    ```

-   Agregue el siguiente constructor a `GestureRecognizerView`. Este constructor agregará `ImageView` a nuestra actividad. En este punto el código todavía no se compilará &ndash; es necesario crear la clase `MyScaleListener` que le ayudará a cambiar el tamaño de la `ImageView` cuando el usuario pinche:

    ```csharp
    public GestureRecognizerView(Context context): base(context, null, 0)
    {
        _icon = context.Resources.GetDrawable(Resource.Drawable.Icon);
        _icon.SetBounds(0, 0, _icon.IntrinsicWidth, _icon.IntrinsicHeight);
        _scaleDetector = new ScaleGestureDetector(context, new MyScaleListener(this));
    }
    ```

-   Para dibujar la imagen en nuestra actividad, es necesario invalidar el `OnDraw` método de la clase de vista, tal y como se muestra en el siguiente fragmento de código. Este código moverá `ImageView` a la posición especificada por `_posX` y `_posY` también cambiará el tamaño de la imagen según el factor de escala:

    ```csharp
    protected override void OnDraw(Canvas canvas)
    {
        base.OnDraw(canvas);
        canvas.Save();
        canvas.Translate(_posX, _posY);
        canvas.Scale(_scaleFactor, _scaleFactor);
        _icon.Draw(canvas);
        canvas.Restore();
    }
    ```

-   A continuación deberá actualizar la variable de instancia `_scaleFactor` como el usuario pinche el `ImageView`. Agregaremos una clase denominada `MyScaleListener`. Esta clase realizará escuchas para los eventos de escala que se generará Android cuando el usuario pinche el `ImageView`.
    Agregue la siguiente clase interna a `GestureRecognizerView`. Esta clase es un `ScaleGesture.SimpleOnScaleGestureListener`. Esta clase es una clase útil que los agentes de escucha pueden subclases cuando está interesado en un subconjunto de gestos:

    ```csharp
    private class MyScaleListener : ScaleGestureDetector.SimpleOnScaleGestureListener
    {
        private readonly GestureRecognizerView _view;

        public MyScaleListener(GestureRecognizerView view)
        {
            _view = view;
        }

        public override bool OnScale(ScaleGestureDetector detector)
        {
            _view._scaleFactor *= detector.ScaleFactor;

            // put a limit on how small or big the image can get.
            if (_view._scaleFactor > 5.0f)
            {
                _view._scaleFactor = 5.0f;
            }
            if (_view._scaleFactor < 0.1f)
            {
                _view._scaleFactor = 0.1f;
            }

            _view.Invalidate();
            return true;
        }
    }
    ```

-   El método siguiente que se debe invalidar `GestureRecognizerView` en `OnTouchEvent`es. En el código siguiente se muestra la implementación completa de este método. Aquí hay una gran cantidad de código, por lo que Tómese un minuto y mire lo que está ocurriendo aquí. Lo primero que hace este método es escalar el icono si &ndash; es necesario para controlarlo `_scaleDetector.OnTouchEvent`mediante una llamada a. A continuación, se intenta averiguar qué acción llamó a este método:

    - Si el usuario toca la pantalla con, registramos las posiciones X e y y el identificador del primer puntero que toca la pantalla.

    - Si el usuario ha mudado su toque en la pantalla, se averigua hasta qué punto el usuario ha pasado el puntero.

    - Si el usuario ha quitado el dedo de la pantalla, se dejará de realizar el seguimiento de los gestos.

    ```csharp
    public override bool OnTouchEvent(MotionEvent ev)
    {
        _scaleDetector.OnTouchEvent(ev);

        MotionEventActions action = ev.Action & MotionEventActions.Mask;
        int pointerIndex;

        switch (action)
        {
            case MotionEventActions.Down:
            _lastTouchX = ev.GetX();
            _lastTouchY = ev.GetY();
            _activePointerId = ev.GetPointerId(0);
            break;

            case MotionEventActions.Move:
            pointerIndex = ev.FindPointerIndex(_activePointerId);
            float x = ev.GetX(pointerIndex);
            float y = ev.GetY(pointerIndex);
            if (!_scaleDetector.IsInProgress)
            {
                // Only move the ScaleGestureDetector isn't already processing a gesture.
                float deltaX = x - _lastTouchX;
                float deltaY = y - _lastTouchY;
                _posX += deltaX;
                _posY += deltaY;
                Invalidate();
            }

            _lastTouchX = x;
            _lastTouchY = y;
            break;

            case MotionEventActions.Up:
            case MotionEventActions.Cancel:
            // We no longer need to keep track of the active pointer.
            _activePointerId = InvalidPointerId;
            break;

            case MotionEventActions.PointerUp:
            // check to make sure that the pointer that went up is for the gesture we're tracking.
            pointerIndex = (int) (ev.Action & MotionEventActions.PointerIndexMask) >> (int) MotionEventActions.PointerIndexShift;
            int pointerId = ev.GetPointerId(pointerIndex);
            if (pointerId == _activePointerId)
            {
                // This was our active pointer going up. Choose a new
                // action pointer and adjust accordingly
                int newPointerIndex = pointerIndex == 0 ? 1 : 0;
                _lastTouchX = ev.GetX(newPointerIndex);
                _lastTouchY = ev.GetY(newPointerIndex);
                _activePointerId = ev.GetPointerId(newPointerIndex);
            }
            break;

        }
        return true;
    }
    ```

-   Ejecute ahora la aplicación e inicie la actividad del reconocedor de gestos.
    Cuando se inicia, la pantalla debe tener un aspecto similar al de la siguiente captura de pantalla:

    [![Pantalla de inicio del reconocedor de gestos con el icono de Android](android-touch-walkthrough-images/image17.png)](android-touch-walkthrough-images/image17.png#lightbox)

-   Ahora, toque el icono y arrástrelo alrededor de la pantalla. Pruebe el gesto de alejar para zoom. En algún momento la pantalla puede tener un aspecto similar al de la siguiente captura de pantalla:

    [![Movimientos mueven el icono alrededor de la pantalla](android-touch-walkthrough-images/image18.png)](android-touch-walkthrough-images/image18.png#lightbox)

Llegados a este punto, debe tener una Pat en la parte trasera: acaba de implementar el acercamiento a zoom en una aplicación Android. Realice un salto rápido y le permite pasar a la tercera y última actividad de este tutorial &ndash; mediante gestos personalizados.

## <a name="custom-gesture-activity"></a>Actividad de gestos personalizados

La última pantalla de este tutorial usará gestos personalizados.

Para los fines de este tutorial, la biblioteca de gestos ya se ha creado con la herramienta de gestos y se ha agregado al proyecto en los recursos de archivo **/raw/movimientos**. Con este poco de mantenimiento fuera del camino, le permite realizar la actividad final en el tutorial.

-   Agregue un archivo de diseño **denominado\_diseño\_de gestos personalizados. axml** al proyecto con el siguiente contenido. El proyecto ya tiene todas las imágenes en la carpeta de **recursos** :

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:orientation="vertical"
        android:layout_width="match_parent"
        android:layout_height="match_parent">
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
        <ImageView
            android:src="@drawable/check_me"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="3"
            android:id="@+id/imageView1"
            android:layout_gravity="center_vertical" />
        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1" />
    </LinearLayout>
    ```

-   A continuación, `CustomGestureRecognizerActivity.cs`agregue una nueva actividad al proyecto y asígnele el nombre. Agregue dos variables de instancia a la clase, tal y como se muestra en las dos líneas de código siguientes:

    ```csharp
    private GestureLibrary _gestureLibrary;
    private ImageView _imageView;
    ```

-   Edite `OnCreate` el método de esta actividad para que sea similar al código siguiente. Dedique un minuto a explicar lo que sucede en este código. Lo primero que hacemos es crear una `GestureOverlayView` instancia de y establecerla como la vista raíz de la actividad.
    También se asigna un controlador de eventos al `GesturePerformed` evento de `GestureOverlayView`. A continuación, se infla el archivo de diseño creado anteriormente y se agrega como una vista secundaria de `GestureOverlayView`. El paso final consiste en inicializar la `_gestureLibrary` variable y cargar el archivo de gestos de los recursos de la aplicación. Si el archivo de gestos no se puede cargar por alguna razón, no se pueden realizar muchas de estas actividades, por lo que se cierra:

    ```csharp
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);

        GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
        SetContentView(gestureOverlayView);
        gestureOverlayView.GesturePerformed += GestureOverlayViewOnGesturePerformed;

        View view = LayoutInflater.Inflate(Resource.Layout.custom_gesture_layout, null);
        _imageView = view.FindViewById<ImageView>(Resource.Id.imageView1);
        gestureOverlayView.AddView(view);

        _gestureLibrary = GestureLibraries.FromRawResource(this, Resource.Raw.gestures);
        if (!_gestureLibrary.Load())
        {
            Log.Wtf(GetType().FullName, "There was a problem loading the gesture library.");
            Finish();
        }
    }
    ```

-   Lo último que debemos hacer es implementar el método `GestureOverlayViewOnGesturePerformed` como se muestra en el siguiente fragmento de código. `GestureOverlayView` Cuando detecta un gesto, vuelve a llamar a este método. Lo primero que se intenta para obtener `IList<Prediction>` los objetos que coinciden con el gesto es llamar `_gestureLibrary.Recognize()`a. Usamos un bit de LINQ para obtener el `Prediction` que tenga la puntuación más alta para el gesto.

    Si no hay ningún movimiento coincidente con una puntuación lo suficientemente alta, el controlador de eventos se cierra sin hacer nada. En caso contrario, se comprueba el nombre de la predicción y se cambia la imagen que se muestra en función del nombre del gesto:

    ```csharp
    private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
    {
        IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
        orderby p.Score descending
        where p.Score > 1.0
        select p;
        Prediction prediction = predictions.FirstOrDefault();

        if (prediction == null)
        {
            Log.Debug(GetType().FullName, "Nothing seemed to match the user's gesture, so don't do anything.");
            return;
        }

        Log.Debug(GetType().FullName, "Using the prediction named {0} with a score of {1}.", prediction.Name, prediction.Score);

        if (prediction.Name.StartsWith("checkmark"))
        {
            _imageView.SetImageResource(Resource.Drawable.checked_me);
        }
        else if (prediction.Name.StartsWith("erase", StringComparison.OrdinalIgnoreCase))
        {
            // Match one of our "erase" gestures
            _imageView.SetImageResource(Resource.Drawable.check_me);
        }
    }
    ```

-   Ejecute la aplicación e inicie la actividad del reconocedor de gestos personalizado. Debe ser similar a la siguiente captura de pantalla:

    [![Captura de pantalla con la imagen de la marca de verificación](android-touch-walkthrough-images/image19.png)](android-touch-walkthrough-images/image19.png#lightbox)

    Ahora dibuje una marca de verificación en la pantalla y el mapa de bits que se muestra debería tener un aspecto similar al que se muestra en las siguientes capturas de pantalla:

    [![Marca de verificación dibujada, se reconoce la marca de verificación](android-touch-walkthrough-images/image20.png)](android-touch-walkthrough-images/image20.png#lightbox)

    Por último, dibuje un garabato en la pantalla. La casilla debe volver a cambiar a su imagen original, tal como se muestra en estas capturas de pantallas:

    [![Garabato en la pantalla, se muestra la imagen original](android-touch-walkthrough-images/image21.png)](android-touch-walkthrough-images/image21.png#lightbox)

Ahora conoce cómo integrar Touch y gestos en una aplicación Android con Xamarin. Android.


## <a name="related-links"></a>Vínculos relacionados

- [Inicio táctil de Android (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch final (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
