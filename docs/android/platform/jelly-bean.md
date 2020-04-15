---
title: Características de Jelly Bean
description: 'En este documento se ofrecerá información general de alto nivel de las nuevas características para desarrolladores que se presentaron en Android 4.1. Estas características incluyen: notificaciones mejoradas, actualizaciones de Android Beam para compartir archivos de gran tamaño, actualizaciones de elementos multimedia, detección de redes punto a punto, animaciones y nuevos permisos.'
ms.prod: xamarin
ms.assetid: 23F57634-2EF9-5C15-C710-B3E19A5AF7E1
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: a638ccf7810c737faaeded7fcc98fcf657c85288
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027209"
---
# <a name="jelly-bean-features"></a>Características de Jelly Bean

_En este documento se ofrecerá información general de alto nivel de las nuevas características para desarrolladores que se presentaron en Android 4.1. Estas características incluyen: notificaciones mejoradas, actualizaciones de Android Beam para compartir archivos de gran tamaño, actualizaciones de elementos multimedia, detección de redes punto a punto, animaciones y nuevos permisos._

## <a name="overview"></a>Información general

Android 4.1 (nivel de API 16), también conocido como "Jelly Bean", se publicó el 9 de julio de 2012. En este artículo se proporciona una introducción de alto nivel a algunas de las nuevas características de Android 4.1 para desarrolladores que usan Xamarin.Android. Algunas de estas nuevas características presentadas son mejoras en las animaciones para iniciar una actividad, nuevos sonidos para una cámara y compatibilidad mejorada con la navegación por la pila de aplicaciones. Ahora es posible cortar y pegar con intenciones.

La estabilidad de las aplicaciones de Android se ha mejorado con la capacidad de aislar la dependencia en proveedores de contenido inestables. Los servicios también pueden aislarse, de modo que solo sean accesibles para la actividad que los inició.

Se ha agregado compatibilidad con la detección de servicios de red mediante Bonjour, UPnP o servicios basados en DNS de multidifusión. Ahora es posible enviar notificaciones más enriquecidas con texto con formato, botones de acción e imágenes grandes.

Por último, se han agregado varios permisos nuevos en Android 4.1.

## <a name="requirements"></a>Requisitos

Para desarrollar aplicaciones de Xamarin.Android con Jelly Bean es preciso instalar Xamarin.Android 4.2.6 o posterior y Android 4.1 (nivel de API 16) a través del Administrador de SDK, tal y como se muestra en la siguiente captura de pantalla:

[![Selección de Android 4.1 en el Administrador de SDK](jelly-bean-images/image1.png)](jelly-bean-images/image1.png#lightbox)

## <a name="whats-new"></a>Novedades

### <a name="animations"></a>Animaciones

Las actividades se pueden iniciar mediante animaciones de zoom o animaciones personalizadas mediante el uso de la clase `ActivityOptions`. Se proporcionan los siguientes métodos nuevos para admitir estas animaciones:

- `MakeScaleUpAnimation`: creará una animación que escala verticalmente una ventana de actividad a partir de una posición inicial y un tamaño en la pantalla.
- `MakeThumbnailScaleUpAnimation`: creará una animación que se escala verticalmente desde una imagen en miniatura desde la posición especificada en la pantalla.
- `MakeCustomAnimation`: crea una animación a partir de recursos de la aplicación. Hay una animación para cuando se abre la actividad y otra para cuando se detiene la actividad.

La nueva clase `TimeAnimator` proporciona una interfaz `TimeAnimator.ITimeListener` que puede notificar a una aplicación cada vez que un fotograma cambia en una animación. Por ejemplo, considere la siguiente implementación de `TimeAnimator.ITimeListener`:

```csharp
class MyTimeListener : Java.Lang.Object,  TimeAnimator.ITimeListener
{
    public void OnTimeUpdate(TimeAnimator animation, long totalTime, long deltaTime)
    {
        Log.Debug("Activity1", "totalTime={0}, deltaTime={1}", totalTime, deltaTime);
    }
}
```

Y ahora, para usar la clase, se crea una instancia de `TimeAnimator` y se establece el cliente de escucha:

```csharp
var animator = new TimeAnimator();
animator.SetTimeListener(new MyTimeListener());
animator.Start();
```

Cuando la instancia de `TimeAnimator` esté en ejecución, se invocará `ITimeAnimator.ITimeListener`, que entonces registrará el tiempo que el animador ha estado ejecutándose y el tiempo que ha pasado desde la última vez que se ha invocado el método.

### <a name="application-stack-navigation"></a>Navegación por la pila de aplicaciones

Android 4.1 mejora la navegación por la pila de aplicaciones que se presentaron en Android 3.0. Al especificar la propiedad `ParentName` del objeto `ActivityAttribute`, Android puede abrir la actividad primaria adecuada cuando el usuario presiona el botón [Arriba](https://developer.android.com/design/patterns/navigation.html#up-vs-back) en la barra de acciones: Android creará una instancia de la actividad especificada por la propiedad `ParentName`. Esto permite a las aplicaciones conservar la jerarquía de actividades que realizan una tarea determinada.

En la mayoría de las aplicaciones, establecer el objeto `ParentName` en la actividad supone información suficiente para que Android proporcione el comportamiento correcto para navegar por la pila de la aplicaciones. Android sintetizará la pila de retroceso necesaria creando una serie de intenciones para cada actividad primaria. Aunque, como se trata de una pila de aplicaciones artificial, ninguna actividad sintética tendrá el estado guardado que tendría una actividad natural. Para proporcionar el estado guardado a una actividad primaria sintética, una actividad puede invalidar el método `OnPrepareNavigationUpTaskStack`. Este método recibe una instancia de `TaskStackBuilder` que tendrá una colección de objetos de intención que Android usará para crear la pila de retroceso. La actividad puede modificar estas intenciones para que, cuando se cree la actividad sintética, reciba la información de estado adecuada.

En escenarios más complejos, hay nuevos métodos en la clase de actividad que se pueden usar para controlar el comportamiento de la navegación hacia arriba y construir la pila de retroceso:

- `OnNavigateUp`: al invalidar este método, es posible realizar una acción personalizada cuando se presiona el botón **Arriba**.
- `NavigateUpTo`: la llamada a este método hará que la aplicación navegue de la actividad actual a la actividad especificada por una intención determinada.
- `ParentActivityIntent`: se usa para obtener una intención que inicie la actividad primaria de la actividad actual.
- `ShouldUpRecreateTask`: este método se utiliza para consultar si se debe crear la pila de retroceso sintética para navegar hasta una actividad primaria. Devuelve `true` si debe crearse la pila sintética. 
- `FinishAffinity`: la llamada a este método finalizará la actividad actual y todas las actividades por debajo de ella en la tarea actual que tengan la misma afinidad de tarea.
- `OnCreateNavigateUpTaskStack`: este método se reemplaza cuando es necesario tener un control completo sobre cómo se crea la pila sintética.

### <a name="camera"></a>Cámara

Hay una nueva interfaz, `Camera.IAutoFocusMoveCallback`, que se puede usar para detectar cuándo el enfoque automático ha empezado o dejado de moverse. En el siguiente fragmento de código puede verse un ejemplo de esta nueva interfaz:

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

La nueva clase `MediaActionSound` ofrece un conjunto de interfaces API para generar sonidos con las diversas acciones multimedia. Hay varias acciones que pueden producirse con una cámara, que se definen mediante la enumeración `Android.Media.MediaActionSoundType`:

- `MediaActionSoundType.FocusComplete`: este sonido se reproduce cuando el enfoque se ha completado.
- `MediaActionSoundType.ShutterClick`: este sonido se reproducirá cuando se tome una foto fija.
- `MediaActionSoundType.StartVideoRecording`: este sonido indica el inicio de la grabación de vídeo.
- `MediaActionSoundType.StopVideoRecording`: este sonido se reproducirá para indicar el final de la grabación de vídeo.

Puede verse un ejemplo del uso de la clase `MediaActionSound` en el fragmento de código siguiente:

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

Android Beam es una tecnología basada en NFC que permite que dos dispositivos Android se comuniquen entre sí. Android 4.1 ofrece mejor compatibilidad con la transferencia de archivos de gran tamaño. Al usar el nuevo método `NfcAdapter.SetBeamPushUris()`, Android cambiará entre mecanismos de transporte alternativos (como Bluetooth) para lograr una velocidad de transferencia rápida.

#### <a name="network-services-discovery"></a>Detección de Network Services

Android 4.1 contiene nuevas API para la detección de servicios basada en DNS de multidifusión.
Esto permite a una aplicación detectar y conectarse a través de Wi-Fi con otros dispositivos, como impresoras, cámaras y dispositivos multimedia. Estas nuevas API se encuentran en el paquete `Android.Net.Nsd`.

Para crear un servicio que pueda ser consumido por otros servicios, se usa la clase `NsdServiceInfo` para crear un objeto que defina las propiedades de un servicio. Este objeto se proporciona después a `NsdManager.RegisterService()` junto con una implementación de `NsdManager.ResolveListener`. Las implementaciones de `NsdManager.ResolveListener` se usan para notificar un registro correcto y para anular el registro del servicio.

Para detectar servicios en la red y la implementación de `Nsd.DiscoveryListener` pasados a `NsdManager.discoverServices()`.

#### <a name="network-usage"></a>Uso de red

Un nuevo método, `ConnectivityManager.IsActiveNetworkMetered`, permite que un dispositivo compruebe si está conectado a una red de uso medido. Este método se puede usar para ayudar a administrar el uso de datos al informar con precisión a los usuarios de que puede haber cargos costosos en las operaciones de datos.

#### <a name="wifi-direct-service-discovery"></a>Detección del servicio WiFi Direct

La clase `WifiP2pManager` se presentó en Android 4.0 para admitir *zeroconf*. Zeroconf (redes de configuración rápida) es un conjunto de técnicas que permite a los dispositivos (equipos, impresoras, teléfonos) conectarse a las redes automáticamente, con la intervención de operadores de red humanos o de servidores de configuración especiales.

En Jale Bean, `WifiP2pManager` puede detectar dispositivos cercanos mediante *Bonjour* o *Upnp*. Bonjour es la implementación de Zeroconf de Apple. Upnp es un conjunto de protocolos de red que también admite Zeroconf. Los métodos siguientes se han agregado a la clase `WiFiP2pManager` para admitir la detección de servicios Wi-Fi:

- `AddLocalService()`: este método se usa para anunciar una aplicación como servicio a través de Wi-Fi para la detección por parte de los equipos del mismo nivel.
- `AddServiceRequest(`): este método tiene por objeto enviar una solicitud de detección de servicios al marco. Se usa para inicializar la detección del servicio Wi-Fi.
- `SetDnsSdResponseListeners()`: este método se usa para registrar devoluciones de llamada que se van a invocar al recibir una respuesta a las solicitudes de detección de Bonjour.
- `SetUpnpServiceResponseListener()`: este método se usa para registrar devoluciones de llamada que se van a invocar al recibir una respuesta a las solicitudes de detección de Upnp.

### <a name="content-providers"></a>Proveedores de contenido

La clase `ContentResolver` ha recibido un nuevo método, `AcquireUnstableContentProvider`. Este método permite a una aplicación adquirir un proveedor de contenido "inestable". Normalmente, cuando una aplicación adquiere un proveedor de contenido y ese proveedor de contenido se bloquea, lo hará la aplicación. Con la llamada a este método, una aplicación no se bloqueará si el proveedor de contenido se bloquea. En cambio, se producirá `Android.OS.DeadObjectionException` en llamadas del proveedor de contenido para informar a una aplicación de que el proveedor de contenido se ha ido. Un proveedor de contenido "inestable" resulta útil al interactuar con proveedores de contenido de otras aplicaciones: es menos probable que el código con errores de otra aplicación afecte a una aplicación distinta.

### <a name="copy-and-paste-with-intents"></a>Copiar y pegar con intenciones

La clase `Intent` ahora puede tener un objeto `ClipData` asociado a través de la propiedad `Intent.ClipData`. Este método permite que se transmitan datos adicionales desde el Portapapeles con la intención. Cada instancia de `ClipData` puede contener uno o varios objetos `ClipData.Item`. Las objetos `ClipData.Item` son elementos de los tipos siguientes:

- **Texto**: se trata de cualquier cadena de texto, ya sea HTML o cualquier cadena cuyo formato sea compatible con los intervalos de estilo integrados de Android.
- **Intención**: cualquier objeto `Intent`.
- **Uri**: puede ser cualquier URI, como un marcador HTTP o el URI de un proveedor de contenido.

### <a name="isolated-services"></a>Servicios aislados

Un servicio aislado es un servicio que se ejecuta en su propio proceso especial y no tiene permisos propios. La única comunicación con el servicio se establece al iniciar el servicio y enlazarlo a él a través de la API de servicio. Es posible declarar un servicio como aislado estableciendo la propiedad `IsolatedProcess="true"` en el objeto `ServiceAttribute` que adorna una clase de servicio.

### <a name="media"></a>Multimedia

La nueva clase `Android.Media.MediaCodec` proporciona una API a los códecs multimedia de bajo nivel. Las aplicaciones pueden consultar el sistema para averiguar qué códecs de bajo nivel están disponibles en el dispositivo.

Se han agregado las nuevas subclases de `Android.Media.Audiofx.AudioEffect` para admitir el procesamiento previo de audio adicional en el audio capturado:

- `Android.Media.Audiofx.AcousticEchoCanceler`: esta clase se usa con el procesamiento previo de audio para quitar la señal de una entidad remota en una señal de audio capturada. Por ejemplo, quitar el eco de una aplicación de comunicación de voz.
- `Android.Media.Audiofx.AutomaticGainControl`: esta clase se usa para normalizar la señal capturada aumentando o reduciendo una señal de entrada para que la señal de salida sea constante.
- `Android.Media.Audiofx.NoiseSuppressor`: esta clase quitará el ruido de fondo de la señal capturada.

No todos los dispositivos admiten estos efectos. Una aplicación debe llamar al método `AudioEffect.IsAvailable` para ver si se admite el efecto de audio en cuestión en el dispositivo que ejecuta la aplicación.

La clase `MediaPlayer` ahora admite la reproducción sin huecos con el método `SetNextMediaPlayer()`. Este nuevo método especifica el siguiente MediaPlayer que se va a iniciar cuando el reproductor multimedia actual termine de reproducirse.

Las nuevas clases siguientes ofrecen mecanismos estándar y la interfaz de usuario para seleccionar dónde se reproducirán los elementos multimedia:

- `MediaRouter`: esta clase permite a las aplicaciones controlar el enrutamiento de canales multimedia desde un dispositivo a altavoces externos u otros dispositivos.
- `MediaRouterActionProvider` y `MediaRouteButton`: estas clases ayudan a proporcionar una interfaz de usuario coherente para seleccionar y reproducir elementos multimedia.

### <a name="notifications"></a>Notificaciones

Android 4.1 permite que las aplicaciones tengan más flexibilidad y control con la visualización de notificaciones. Ahora, las aplicaciones pueden mostrar notificaciones más grandes y mejores a los usuarios. Un nuevo método, `NotificationBuilder.SetStyle()`, permite que se establezca uno de los tres nuevos estilos en las notificaciones:

- `Notification.BigPictureStyle`: se trata de una clase auxiliar que generará notificaciones que incluyen una imagen. La imagen siguiente muestra un ejemplo de una notificación con una imagen grande:

 [![Captura de pantalla de ejemplo de una notificación de BigPictureStyle](jelly-bean-images/image2.png)](jelly-bean-images/image2.png#lightbox)

- `Notification.BigTextStyle`: se trata de una clase auxiliar que generará notificaciones que tengan varias líneas de texto, como el correo electrónico. En la siguiente captura de pantalla se puede ver un ejemplo de este nuevo estilo de notificación:

 [![Captura de pantalla de ejemplo de una notificación de BigTextStyle](jelly-bean-images/image3.png)](jelly-bean-images/image3.png#lightbox)

- `Notification.InboxStyle`: se trata de una clase auxiliar que generará notificaciones que contienen una lista de cadenas, como fragmentos de código de un mensaje de correo, tal y como se muestra en esta captura de pantalla:

 [![Captura de pantalla de ejemplo de una notificación de Notification.InboxStyle](jelly-bean-images/image4.png)](jelly-bean-images/image4.png#lightbox)

Es posible agregar hasta dos botones de acción en la parte inferior de un mensaje de notificación cuando la notificación utiliza el estilo normal o grande.
Puede verse un ejemplo de esto en la siguiente captura de pantalla, donde los botones de acción están visibles en la parte inferior de la notificación:

 [![Captura de pantalla de ejemplo de los botones de acción que se muestran debajo de un mensaje de notificación](jelly-bean-images/image5.png)](jelly-bean-images/image5.png#lightbox)

La clase `Notification` ha recibido nuevos valores constantes que permiten el desarrollador especificar uno de cinco niveles de prioridad para una notificación. Se pueden establecer en una notificación mediante la propiedad `Priority`.

### <a name="permissions"></a>Permisos

Se han agregado los siguientes permisos nuevos:

- `READ_EXTERNAL_STORAGE`: la aplicación requiere acceso de solo lectura al almacenamiento externo. Actualmente, todas las aplicaciones tienen acceso de lectura de forma predeterminada, pero las versiones futuras de Android requerirán que las aplicaciones soliciten explícitamente el acceso de lectura.
- `READ_USER_DICTIONARY`: permite el acceso de lectura al diccionario del usuario.
- `READ_CALL_LOG`: permite que una aplicación obtenga información sobre las llamadas entrantes y salientes leyendo el registro de llamadas.
- `WRITE_CALL_LOG`: permite que una aplicación escriba en el registro de llamadas del teléfono.
- `WRITE_USER_DICTIONARY`: permite que una aplicación escriba en el diccionario del usuario.

Un cambio importante que se debe tener en cuenta sobre `READ_EXTERNAL_STORAGE`: actualmente, este permiso se concede automáticamente en Android. Las versiones futuras de Android requerirán que una aplicación solicite este permiso para que se le conceda.

## <a name="summary"></a>Resumen

En este artículo se presentan algunas de las nuevas API que están disponibles en Android 4.1 (nivel de API 16). Se destacan algunos cambios para las animaciones y la animación del inicio de una actividad, y se presentan nuevas API para la detección de redes de otros dispositivos mediante protocolos como Bonjour o UPnP. También se resaltan otros cambios en la API, como la capacidad de cortar y pegar datos a través de intenciones, la capacidad de usar servicios aislados o proveedores de contenido "inestables".

En este artículo se presentan las actualizaciones de notificaciones y se comentan algunos de los nuevos permisos que se han introducido con Android 4.1

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de animación de tiempo (muestra)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/platformfeatures-timeanimatorexample)
- [Interfaces API de Android 4.1](https://developer.android.com/about/versions/android-4.1.html)
- [Tareas y pilas de retroceso](https://developer.android.com/guide/components/tasks-and-back-stack.html)
- [Navegación con Atrás y Arriba](https://developer.android.com/design/patterns/navigation.html)
