---
title: Características de Jale Bean
description: 'En este documento se proporciona información general de alto nivel de las nuevas características para los desarrolladores que se introdujeron en Android 4,1. Estas características incluyen: notificaciones mejoradas, actualizaciones para el rayo de Android para compartir archivos de gran tamaño, actualizaciones a multimedia, detección de redes punto a punto, animaciones y nuevos permisos.'
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: e54f499316d2b99d87d05fbd202308eecaaed220
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643371"
---
# <a name="jelly-bean-features"></a>Características de Jale Bean

_En este documento se proporciona información general de alto nivel de las nuevas características para los desarrolladores que se introdujeron en Android 4,1. Estas características incluyen: notificaciones mejoradas, actualizaciones para el rayo de Android para compartir archivos de gran tamaño, actualizaciones a multimedia, detección de redes punto a punto, animaciones y nuevos permisos._



## <a name="overview"></a>Información general

Android 4,1 (nivel de API 16), también conocido como "Jaleis", se publicó el 9 de julio de 2012. En este artículo se proporciona una introducción de alto nivel a algunas de las nuevas características de Android 4,1 para desarrolladores que usan Xamarin. Android. Algunas de estas nuevas características introducidas son mejoras en las animaciones para iniciar una actividad, nuevos sonidos para una cámara y compatibilidad mejorada con la navegación por la pila de aplicaciones. Ahora es posible cortar y pegar con intenciones.

La estabilidad de las aplicaciones Android se ha mejorado con la capacidad de aislar la dependencia en los proveedores de contenido inestables. Los servicios también pueden estar aislados, de modo que solo sean accesibles por la actividad que los inició.

Se ha agregado compatibilidad con la detección de servicios de red mediante Bonjour, UPnP o servicios basados en DNS de multidifusión. Ahora es posible para las notificaciones más enriquecidas que tienen texto con formato, botones de acción e imágenes grandes.

Por último, se han agregado varios permisos nuevos en Android 4,1.



## <a name="requirements"></a>Requisitos

Para desarrollar aplicaciones de Xamarin. Android con el Bean de jale, se requiere Xamarin. Android 4.2.6 o posterior y Android 4,1 (nivel de API 16) se instala a través del administrador de Android SDK como se muestra en la siguiente captura de pantalla:

[![Seleccionar Android 4,1 en el administrador de Android SDK](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)



## <a name="whats-new"></a>Novedades



### <a name="animations"></a>Animaciones

Las actividades se pueden iniciar mediante animaciones de zoom o animaciones personalizadas mediante la `ActivityOptions` clase. Se proporcionan los siguientes métodos nuevos para admitir estas animaciones:

-   `MakeScaleUpAnimation`: De esta forma, se crea una animación que escala verticalmente una ventana de actividad a partir de una posición inicial y un tamaño en la pantalla.
-   `MakeThumbnailScaleUpAnimation`: Esto creará una animación que se escala verticalmente desde una imagen en miniatura desde la posición especificada en la pantalla.
-   `MakeCustomAnimation`: Crea una animación a partir de los recursos de la aplicación. Hay una animación para cuando se abre la actividad y otra para cuando se detiene la actividad.


La nueva `TimeAnimator` clase proporciona una interfaz `TimeAnimator.ITimeListener` que puede notificar a una aplicación cada vez que un fotograma cambia en una animación. Por ejemplo, considere la siguiente implementación de `TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

Y ahora se usa la clase, se crea una `TimeAnimator` instancia de y se establece el agente de escucha:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

A medida `TimeAnimator` que se ejecuta la instancia, se `ITimeAnimator.ITimeListener`invocará, que después registrará el tiempo durante el que se ha estado ejecutando el animador y cuánto tiempo ha transcurrido desde la última vez que se ha invocado el método.



### <a name="application-stack-navigation"></a>Navegación por la pila de aplicaciones

Android 4,1 mejora la navegación de la pila de aplicaciones que se presentó en Android 3,0. Al especificar la `ParentName` propiedad `ActivityAttribute`de, Android puede abrir la actividad primaria adecuada cuando el usuario presiona el [botón subir](https://developer.android.com/design/patterns/navigation.html#up-vs-back) en la barra de acciones: Android creará una instancia de la actividad especificada por `ParentName` la propiedad. Esto permite a las aplicaciones conservar la jerarquía de actividades que realizan una tarea determinada.

Para la mayoría de las `ParentName` aplicaciones, la configuración de en la actividad es suficiente información para que Android proporcione el comportamiento correcto para navegar por la pila de la aplicación. Android sintetizará la pila de retroceso necesaria creando una serie de intentos para cada actividad primaria. Sin embargo, dado que se trata de una pila de aplicaciones artificial, cada actividad sintética no tendrá el estado guardado que tendría una actividad natural. Para proporcionar el estado guardado a una actividad primaria sintética, una actividad puede invalidar el `OnPrepareNavigationUpTaskStack` método. Este método recibe una `TaskStackBuilder` instancia de que tendrá una colección de objetos de intención que Android usará para crear la pila de reserva. La actividad puede modificar estos intentos, de modo que, a medida que se crea la actividad sintética, recibirá la información de estado adecuada.

En escenarios más complejos, hay nuevos métodos en la clase de actividad que se pueden usar para controlar el comportamiento de la navegación hacia arriba y construir la pila de retroceso:

-   `OnNavigateUp`: Al invalidar este método, es posible realizar una acción personalizada cuando se presiona el botón <span class="ui">subir</span> .
-   `NavigateUpTo`: Si se llama a este método, la aplicación navega de la actividad actual a la actividad especificada por un intento determinado.
-   `ParentActivityIntent`: Se usa para obtener un intento que iniciará la actividad primaria de la actividad actual.
-   `ShouldUpRecreateTask`: Este método se utiliza para consultar si se debe crear la pila de retroceso sintética para navegar hasta una actividad primaria. Devuelve `true` si se debe crear la pila sintética. 
-   `FinishAffinity`: La llamada a este método finalizará la actividad actual y todas las actividades inferiores en la tarea actual que tengan la misma afinidad de tarea.
-   `OnCreateNavigateUpTaskStack`: Este método se reemplaza cuando es necesario tener un control completo sobre cómo se crea la pila sintética.




### <a name="camera"></a>Cámara

Hay una nueva interfaz, `Camera.IAutoFocusMoveCallback`, que se puede usar para detectar cuándo se ha iniciado o detenido el desplazamiento automático. En el siguiente fragmento de código puede verse un ejemplo de esta nueva interfaz:

```csharp
public class AutoFocusCallbackActivity : Activity, Camera.IAutoFocusCallback
{
    public void OnAutoFocus(bool success, Camera camera)
    {
        // camera is an instance of the camera service object.

        if (success)
        {
            // Auto focus was successful - do something here.
        }
        else
        {
            // Auto focus didn't happen for some reason - react to that here.
        }
    }
}
```

La nueva clase `MediaActionSound` proporciona un conjunto de API para generar sonidos para las diversas acciones multimedia. Hay varias acciones que se pueden producir con una cámara, que se definen mediante la enumeración `Android.Media.MediaActionSoundType`:

-   `MediaActionSoundType.FocusComplete`: Este sonido se reproduce cuando se ha completado el enfoque.
-   `MediaActionSoundType.ShutterClick`: Este sonido se reproducirá cuando se tome una imagen de imagen fija.
-   `MediaActionSoundType.StartVideoRecording`: Este sonido indica el inicio de la grabación de vídeo.
-   `MediaActionSoundType.StopVideoRecording`: Este sonido se reproducirá para indicar el final de la grabación de vídeo.


Un ejemplo de cómo usar la `MediaActionSound` clase puede verse en el siguiente fragmento de código:

```csharp
var mediaActionPlayer = new MediaActionSound();

// Preload the sound for a shutter click.
mediaActionPlayer.Load(MediaActionSoundType.ShutterClick);
var button = FindViewById<Button>(Resource.Id.MyButton);

// Play the sound on a button click.
button.Click += (sender, args) => mediaActionPlayer.Play(MediaActionSoundType.ShutterClick);

// This releases the preloaded resources. Don’t make any calls on
// mediaActionPlayer after this.
mediaActionPlayer.Release();
```



### <a name="connectivity"></a>Conectividad



#### <a name="android-beam"></a>Android Beam

El rayo de Android es una tecnología basada en NFC que permite que dos dispositivos Android se comuniquen entre sí. Android 4,1 proporciona una mejor compatibilidad para la transferencia de archivos grandes. Cuando use el nuevo método `NfcAdapter.SetBeamPushUris()` , Android cambiará entre mecanismos de transporte alternativos (como Bluetooth) para lograr una velocidad de transferencia rápida.



#### <a name="network-services-discovery"></a>Detección de Network Services

Android 4,1 contiene nuevas API para la detección de servicios basada en DNS de multidifusión.
Esto permite a una aplicación detectar y conectarse a través de Wi-Fi a otros dispositivos, como impresoras, cámaras y dispositivos multimedia. Estas nuevas API se encuentran en el `Android.Net.Nsd` paquete.

Para crear un servicio que puede ser consumido por otros servicios, `NsdServiceInfo` se usa la clase para crear un objeto que defina las propiedades de un servicio. A continuación, este objeto se `NsdManager.RegisterService()` proporciona junto con una implementación `NsdManager.ResolveListener`de. Las implementaciones `NsdManager.ResolveListener` de se usan para notificar un registro correcto y anular el registro del servicio.

Para detectar servicios en la red y la implementación de `Nsd.DiscoveryListener` que se `NsdManager.discoverServices()`pasa a.



#### <a name="network-usage"></a>Uso de red

Un nuevo método `ConnectivityManager.IsActiveNetworkMetered` permite que un dispositivo Compruebe si está conectado a una red de uso medido. Este método se puede usar para ayudar a administrar el uso de datos al informar a los usuarios de forma precisa de que puede haber costosos en las operaciones de datos.



#### <a name="wifi-direct-service-discovery"></a>Detección de servicios Wi-Fi Direct

La `WifiP2pManager` clase se presentó en Android 4,0 para admitir *Zeroconf*. Zeroconf (ninguna red de configuración) es un conjunto de técnicas que permite a los dispositivos (equipos, impresoras, teléfonos) conectarse a las redes automáticamente, con la intervención de los operadores de red humana o de los servidores de configuración especiales.

En jale Bean, `WifiP2pManager` puede detectar dispositivos cercanos mediante *Bonjour* o *UPnP*. Bonjour es la implementación de Zeroconf de Apple. UPnP es un conjunto de protocolos de red que también admite Zeroconf. Los métodos siguientes agregados a `WiFiP2pManager` para admitir la detección de servicios Wi-Fi:

-   `AddLocalService()`: Este método se usa para anunciar una aplicación como servicio a través de Wi-Fi para la detección por parte de los equipos del mismo nivel.
-   `AddServiceRequest(`): Este método es para enviar una solicitud de detección de servicios al marco. Se usa para inicializar la detección del servicio Wi-Fi.
-   `SetDnsSdResponseListeners()`: Este método se usa para registrar devoluciones de llamada que se van a invocar al recibir una respuesta a las solicitudes de detección de Bonjour.
-   `SetUpnpServiceResponseListener()`: Este método se usa para registrar devoluciones de llamada que se van a invocar al recibir una respuesta a las solicitudes de detección UPnP.




### <a name="content-providers"></a>Proveedores de contenido

La `ContentResolver` clase ha recibido un nuevo método, `AcquireUnstableContentProvider`. Este método permite a una aplicación adquirir un proveedor de contenido "inestable". Normalmente, cuando una aplicación adquiere un proveedor de contenido y ese proveedor de contenido se bloquea, lo hará la aplicación. Con esta llamada al método, una aplicación no se bloqueará si el proveedor de contenido se bloquea. En su lugar `Android.OS.DeadObjectionException` , se producirá desde las llamadas en el proveedor de contenido para informar a una aplicación de que el proveedor de contenido ha desaparecido. Un proveedor de contenido "inestable" es útil al interactuar con proveedores de contenido de otras aplicaciones; es menos probable que el código erróneo de otra aplicación afecte a otra aplicación.



### <a name="copy-and-paste-with-intents"></a>Copiar y pegar con intenciones

La `Intent` clase ahora puede tener un `ClipData` objeto asociado a ella a través `Intent.ClipData` de la propiedad. Este método permite que se transmitan datos adicionales desde el Portapapeles con el intento. Una instancia de `ClipData` puede contener uno o más `ClipData.Item`. `ClipData.Item`son elementos de los siguientes tipos:

-   **Texto** : cadena de texto, HTML o cualquier cadena cuyo formato sea compatible con los intervalos de estilo integrados de Android.
-  **Intención** : cualquier `Intent` objeto.
-   **URI** : puede ser cualquier URI, como un marcador http o el URI a un proveedor de contenido.




### <a name="isolated-services"></a>Servicios aislados

Un servicio aislado es un servicio que se ejecuta bajo su propio proceso especial y no tiene permisos propios. La única comunicación con el servicio es cuando se inicia el servicio y se enlaza a él a través de la API de servicio. Es posible declarar un servicio como aislado estableciendo la propiedad `IsolatedProcess="true"` `ServiceAttribute` en que adorna una clase de servicio.


### <a name="media"></a>Multimedia

La nueva `Android.Media.MediaCodec` clase proporciona una API a los códecs multimedia de bajo nivel. Las aplicaciones pueden consultar el sistema para averiguar qué códecs de nivel inferior están disponibles en el dispositivo.

Las nuevas `Android.Media.Audiofx.AudioEffect` subclases se han agregado para admitir el procesamiento previo de audio adicional en el audio capturado:

-   `Android.Media.Audiofx.AcousticEchoCanceler`: Esta clase se usa para el procesamiento previo de audio para quitar la señal de una parte remota de una señal de audio capturada. Por ejemplo, quitar el eco de una aplicación de comunicación de voz.
-   `Android.Media.Audiofx.AutomaticGainControl`: Esta clase se usa para normalizar la señal capturada aumentando o reduciendo una señal de entrada para que la señal de salida sea constante.
-   `Android.Media.Audiofx.NoiseSuppressor`: Esta clase quitará el ruido de fondo de la señal capturada.


No todos los dispositivos admiten estos efectos. Una aplicación `AudioEffect.IsAvailable` debe llamar al método para ver si se admite el efecto de audio en cuestión en el dispositivo que ejecuta la aplicación.

La `MediaPlayer` clase ahora admite la reproducción de sin huecos `SetNextMediaPlayer()` con el método. Este nuevo método especifica el siguiente MediaPlayer que se va a iniciar cuando el reproductor multimedia actual termine de reproducirse.

Las siguientes clases nuevas proporcionan mecanismos y la interfaz de usuario estándar para seleccionar dónde se reproducirá el medio:

-   `MediaRouter`: Esta clase permite a las aplicaciones controlar el enrutamiento de canales multimedia desde un dispositivo a altavoces externos u otros dispositivos.
-   `MediaRouterActionProvider`y `MediaRouteButton` : estas clases ayudan a proporcionar una interfaz de usuario coherente para seleccionar y reproducir elementos multimedia.




### <a name="notifications"></a>Notificaciones

Android 4,1 permite que las aplicaciones tengan más flexibilidad y control con la visualización de notificaciones. Ahora, las aplicaciones pueden mostrar notificaciones más grandes y mejores a los usuarios. Un nuevo método, `NotificationBuilder.SetStyle()` permite que uno de los nuevos tres nuevos estilos se establezca en las notificaciones:

-   `Notification.BigPictureStyle`: Se trata de una clase auxiliar que generará notificaciones que incluirán una imagen en ellas. La imagen siguiente muestra un ejemplo de una notificación con una imagen grande:


 [![Captura de pantalla de ejemplo de una notificación de BigPictureStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

-   `Notification.BigTextStyle`: Se trata de una clase auxiliar que generará notificaciones que tendrán varias líneas de texto, como el correo electrónico. En la siguiente captura de pantalla se puede consultar un ejemplo de este nuevo estilo de notificación:


 [![Captura de pantalla de ejemplo de una notificación de BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

-   `Notification.InboxStyle`: Se trata de una clase auxiliar que generará notificaciones que contienen una lista de cadenas, como fragmentos de código de un mensaje de correo electrónico, tal como se muestra en esta captura de pantalla:


 [![Captura de pantalla de ejemplo de una notificación de Notification. InboxStyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

Es posible agregar hasta dos botones de acción en la parte inferior de un mensaje de notificación cuando la notificación esté usando el estilo normal o mayor.
Un ejemplo de esto se puede ver en la siguiente captura de pantalla, donde los botones de acción están visibles en la parte inferior de la notificación:

 [![Captura de pantalla de ejemplo de los botones de acción que se muestran debajo de un mensaje de notificación](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

La `Notification` clase ha recibido nuevas constantes que permiten al desarrollador especificar uno de los cinco niveles de prioridad para una notificación. Se pueden establecer en una notificación mediante la `Priority` propiedad.



### <a name="permissions"></a>Permisos

Se han agregado los siguientes permisos nuevos:

-   `READ_EXTERNAL_STORAGE`-La aplicación requiere acceso de solo lectura al almacenamiento externo. Actualmente, todas las aplicaciones tienen acceso de lectura de forma predeterminada, pero las versiones futuras de Android requerirán que las aplicaciones soliciten explícitamente el acceso de lectura.
-   `READ_USER_DICTIONARY`: Permite el acceso de lectura al Diccionario de palabras del usuario.
-   `READ_CALL_LOG`: Permite que una aplicación obtenga información sobre las llamadas entrantes y salientes leyendo el registro de llamadas.
-   `WRITE_CALL_LOG`: Permite que una aplicación escriba en el registro de llamadas del teléfono.
-   `WRITE_USER_DICTIONARY`: Permite que una aplicación escriba en el Diccionario de palabras del usuario.


Un cambio importante a tener `READ_EXTERNAL_STORAGE` en cuenta: Actualmente, Android concede automáticamente este permiso. Las versiones futuras de Android requerirán que una aplicación solicite este permiso antes de que se le conceda el permiso.



## <a name="summary"></a>Resumen

En este artículo se presentaron algunas de las nuevas API que están disponibles en Android 4,1 (nivel de API 16). Resaltó algunos cambios para animaciones y animando el lanzamiento de una actividad, e incorporó las nuevas API para la detección de redes de otros dispositivos mediante protocolos como Bonjour o UPnP. También se resaltan otros cambios en la API, como la capacidad de cortar y pegar datos a través de la intención, la capacidad de usar los servicios aislados o los proveedores de contenido "inestables".

Este artículo se ha pasado a presentar las actualizaciones a las notificaciones y se han explicado algunos de los nuevos permisos que se han introducido en Android 4,1


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de animación de tiempo (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [API de Android 4,1](https://developer.android.com/about/versions/android-4.1.html)
- [Tareas y pilas de retroceso](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navegación con atrás y arriba](https://developer.android.com/design/patterns/navigation.html)
