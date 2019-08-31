---
title: Entrada táctil de Android
ms.prod: xamarin
ms.assetid: 405A1FA0-4EFA-4AEB-B672-F36307B9CF16
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: 0619dd9a81c40633c7c61f23ef9e8f3ef92c2e6a
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197624"
---
# <a name="touch-in-android"></a>Entrada táctil de Android

De forma similar a iOS, Android crea un objeto que contiene datos sobre la interacción física del usuario con &ndash; la `Android.View.MotionEvent` pantalla de un objeto. Este objeto contiene datos, como la acción que se lleva a cabo, dónde tuvo lugar el toque, cuánta presión se aplicó, etc. Un `MotionEvent` objeto divide el movimiento en hasta los valores siguientes:

- Código de acción que describe el tipo de movimiento, como el toque inicial, el movimiento táctil a través de la pantalla o la finalización táctil.

- Un conjunto de valores de eje que describen la posición de `MotionEvent` y otras propiedades de movimiento, como el lugar en el que se está llevando a cabo el toque, Cuándo tuvo lugar el toque y cuánta presión se usó.
   Los valores de los ejes pueden variar en función del dispositivo, por lo que en la lista anterior no se describen todos los valores de los ejes.


El `MotionEvent` objeto se pasará a un método adecuado en una aplicación. Hay tres maneras de que una aplicación de Xamarin. Android responda a un evento Touch:

- *Asignar un controlador de eventos `View.Touch` a* : `Android.Views.View` la clase tiene `EventHandler<View.TouchEventArgs>` una a la que las aplicaciones pueden asignar un controlador. Este es un comportamiento típico de .NET.

- *Implementar`View.IOnTouchListener`* : las instancias de esta interfaz pueden asignarse a un objeto de vista mediante la vista. `SetOnListener`forma. Es funcionalmente equivalente a asignar un controlador de eventos al `View.Touch` evento. Si hay algunas lógicas comunes o compartidas que pueden necesitar muchas vistas diferentes cuando se tocan, será más eficaz crear una clase e implementar este método que asignar cada vista a su propio controlador de eventos.

- *Override`View.OnTouchEvent`* : todas las vistas de la subclase `Android.Views.View`de Android. Cuando se toca una vista, Android llama a `OnTouchEvent` y le pasa un `MotionEvent` objeto como parámetro.


> [!NOTE]
> No todos los dispositivos Android admiten pantallas táctiles. 

Al agregar la siguiente etiqueta al archivo de manifiesto, Google Play solo muestra la aplicación en los dispositivos que están habilitados para tocar:

```xml
<uses-configuration android:reqTouchScreen="finger" />
```

## <a name="gestures"></a>Gestos

Un gesto es una forma dibujada a mano en la pantalla táctil. Los gestos pueden tener uno o varios trazos, cada uno de los cuales consta de una secuencia de puntos creados por un punto de contacto distinto con la pantalla. Android puede admitir muchos tipos diferentes de gestos, desde un Fling simple a través de la pantalla, a gestos complejos que implican múltiples toques.

Android proporciona el `Android.Gestures` espacio de nombres específicamente para administrar y responder a los gestos. En el corazón de todos los gestos hay una clase especial `Android.Gestures.GestureDetector`denominada. Como implica el nombre, esta clase escuchará los movimientos y eventos en función de `MotionEvents` los proporcionados por el sistema operativo.

Para implementar un detector de gestos, una actividad debe crear `GestureDetector` instancias de una clase y proporcionar `IOnGestureListener`una instancia de, como se muestra en el siguiente fragmento de código:

```csharp
GestureOverlayView.IOnGestureListener myListener = new MyGestureListener();
_gestureDetector = new GestureDetector(this, myListener);
```

Una actividad también debe implementar OnTouchEvent y pasar MotionEvent al detector de gestos. En el fragmento de código siguiente se muestra un ejemplo de esto:

```csharp
public override bool OnTouchEvent(MotionEvent e)
{
    // This method is in an Activity
    return _gestureDetector.OnTouchEvent(e);
}
```

Cuando una instancia de `GestureDetector` identifica un movimiento de interés, se lo notificará a la actividad o aplicación mediante la generación de un evento o a través `GestureDetector.IOnGestureListener`de una devolución de llamada proporcionada por.
Esta interfaz proporciona seis métodos para los distintos gestos:

- *Down* : se llama cuando se produce una derivación, pero no se libera.

- *OnFling* : se le llama cuando se produce una Fling y proporciona datos sobre el toque inicial y final que desencadenó el evento.

- *OnLongPress* : se le llama cuando se produce una presión larga.

- *Onscroll* : se llama cuando se produce un evento de desplazamiento.

- *OnShowPress* : se le llama después de que se haya producido una excepción y no se ha realizado un evento de movimiento o de arriba.

- *OnSingleTapUp* : se le llama cuando se produce un solo punteo.


En muchos casos, las aplicaciones solo pueden estar interesadas en un subconjunto de gestos. En este caso, las aplicaciones deben extender la clase GestureDetector. SimpleOnGestureListener e invalidar los métodos que corresponden a los eventos que le interesan.

## <a name="custom-gestures"></a>Gestos personalizados

Los gestos son una excelente manera de que los usuarios puedan interactuar con una aplicación. Las API que vimos hasta ahora serían suficientes para los gestos sencillos, pero podrían resultar un poco onerosos para gestos más complicados. Para ayudar con gestos más complicados, Android proporciona otro conjunto de API en el espacio de nombres de Android. gestos que facilitará parte de la carga asociada a los gestos personalizados.

### <a name="creating-custom-gestures"></a>Crear gestos personalizados

Desde Android 1,6, la Android SDK incluye una aplicación preinstalada en el emulador llamado gestos de gestos. Esta aplicación permite a los desarrolladores crear gestos predefinidos que se pueden incrustar en una aplicación. En la captura de pantalla siguiente se muestra un ejemplo de gestos del generador:

[![Captura de pantalla del generador de gestos con gestos de ejemplo](touch-in-android-images/image11.png)](touch-in-android-images/image11.png#lightbox)

Se puede encontrar una versión mejorada de esta aplicación denominada herramienta de gestos Google Play. La herramienta de gestos es muy similar al generador de gestos, salvo que le permite probar los gestos después de haberlos creado. En la siguiente captura de pantalla se muestra el generador de gestos:

[![Captura de pantalla de la herramienta de gestos con gestos de ejemplo](touch-in-android-images/image12.png)](touch-in-android-images/image12.png#lightbox)

La herramienta de gestos es un poco más útil para crear gestos personalizados, ya que permite que los gestos se prueben a medida que se crean y que se pueden acceder fácilmente a través de Google Play.

La herramienta de gestos le permite crear un gesto dibujando en la pantalla y asignando un nombre. Una vez creados los gestos, se guardan en un archivo binario en la tarjeta SD del dispositivo. Este archivo debe recuperarse desde el dispositivo y, a continuación, empaquetarse con una aplicación en la carpeta/Resources/RAW. Este archivo se puede recuperar desde el emulador mediante el Android Debug Bridge. En el ejemplo siguiente se muestra cómo copiar el archivo de un archivo de Galaxy Nexus en el directorio de recursos de una aplicación:

```shell
$ adb pull /storage/sdcard0/gestures <projectdirectory>/Resources/raw
```

Una vez que haya recuperado el archivo, se debe empaquetar con la aplicación en el directorio/Resources/RAW. La forma más fácil de usar este archivo de gestos es cargar el archivo en un GestureLibrary, como se muestra en el siguiente fragmento de código:

```csharp
GestureLibrary myGestures = GestureLibraries.FromRawResources(this, Resource.Raw.gestures);
if (!myGestures.Load())
{
    // The library didn't load, so close the activity.
    Finish();
}
```

### <a name="using-custom-gestures"></a>Usar gestos personalizados

Para reconocer gestos personalizados en una actividad, debe tener un objeto Android. gesto. GestureOverlay agregado a su diseño. En el fragmento de código siguiente se muestra cómo agregar un GestureOverlayView a una actividad mediante programación:

```csharp
GestureOverlayView gestureOverlayView = new GestureOverlayView(this);
gestureOverlayView.AddOnGesturePerformedListener(this);
SetContentView(gestureOverlayView);
```

El siguiente fragmento XML muestra cómo agregar un GestureOverlayView mediante declaración:

```xml
<android.gesture.GestureOverlayView
    android:id="@+id/gestures"
    android:layout_width="match_parent "
    android:layout_height="match_parent" />
```

`GestureOverlayView` Tiene varios eventos que se producirán durante el proceso de dibujo de un gesto. El evento más interesante es `GesturePerformed`. Este evento se desencadena cuando el usuario ha terminado de dibujar su gesto.

Cuando se genera este evento, la actividad pide `GestureLibrary` a que pruebe y haga coincidir el gesto que el usuario con uno de los gestos creados por la herramienta de gestos. `GestureLibrary`devolverá una lista de objetos de predicción.

Cada objeto de predicción contiene una puntuación y el nombre de uno de los gestos `GestureLibrary`en. Cuanto mayor sea la puntuación, mayor será la probabilidad de que el gesto mencionado en la predicción coincida con el gesto dibujado por el usuario.
Por lo general, las puntuaciones inferiores a 1,0 se consideran malas coincidencias.

En el código siguiente se muestra un ejemplo de coincidencia de un gesto:

```csharp
private void GestureOverlayViewOnGesturePerformed(object sender, GestureOverlayView.GesturePerformedEventArgs gesturePerformedEventArgs)
{
    // In this example _gestureLibrary was instantiated in OnCreate
    IEnumerable<Prediction> predictions = from p in _gestureLibrary.Recognize(gesturePerformedEventArgs.Gesture)
    orderby p.Score descending
    where p.Score > 1.0
    select p;
    Prediction prediction = predictions.FirstOrDefault();

    if (prediction == null)
    {
        Log.Debug(GetType().FullName, "Nothing matched the user's gesture.");
        return;
    }

    Toast.MakeText(this, prediction.Name, ToastLength.Short).Show();
}
```

Una vez hecho esto, debe tener conocimientos sobre cómo usar los gestos táctiles y en una aplicación de Xamarin. Android. Vamos a pasar a un tutorial y ver todos los conceptos de una aplicación de ejemplo en funcionamiento.



## <a name="related-links"></a>Vínculos relacionados

- [Inicio táctil de Android (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-start)
- [Android Touch final (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-touch-final)
