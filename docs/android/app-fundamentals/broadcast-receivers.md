---
title: Receptores de difusión en Xamarin. Android
description: En esta sección se describe cómo usar un receptor de difusión.
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: 9e702bdb31563588d20b9cd70e35b67aefe67e0c
ms.sourcegitcommit: d1980b2251999224e71c1289e4b4097595b7e261
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2020
ms.locfileid: "92928378"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Receptores de difusión en Xamarin. Android

_En esta sección se describe cómo usar un receptor de difusión._

## <a name="broadcast-receiver-overview"></a>Información general del receptor de difusión

Un _receptor de difusión_ es un componente de Android que permite a una aplicación responder a mensajes (un Android [`Intent`](xref:Android.Content.Intent) ) que difunde el sistema operativo Android o una aplicación. Las difusiones siguen un modelo _de publicación y suscripción_ &ndash; . un evento hace que se publique una difusión y la reciban los componentes interesados en el evento.

Android identifica dos tipos de difusiones:

- **Difusión explícita** &ndash; Estos tipos de difusiones se dirigen a una aplicación específica. El uso más común de una difusión explícita es iniciar una actividad. Un ejemplo de una difusión explícita cuando una aplicación necesita marcar un número de teléfono. se enviará un intento dirigido a la aplicación de teléfono en Android y pasará por el número de teléfono que se va a marcar. Después, Android enrutará el intento a la aplicación de teléfono.
- **Difusión implícita** &ndash; Estas difusiones se envían a todas las aplicaciones del dispositivo. Un ejemplo de una difusión implícita es la `ACTION_POWER_CONNECTED` intención. Esta intención se publica cada vez que Android detecta que la batería del dispositivo se está cargando. Android enrutará esta intención a todas las aplicaciones que se hayan registrado para este evento.

El receptor de difusión es una subclase del `BroadcastReceiver` tipo y debe reemplazar el [`OnReceive`](xref:Android.Content.BroadcastReceiver.OnReceive*) método. Android se ejecutará `OnReceive` en el subproceso principal, por lo que este método debe diseñarse para ejecutarse rápidamente. Se debe tener cuidado al generar subprocesos en `OnReceive` porque Android puede terminar el proceso cuando el método finaliza. Si un receptor de difusión debe realizar un trabajo de larga ejecución, se recomienda programar un _trabajo_ mediante el `JobScheduler` o el _distribuidor de trabajo de Firebase_ . La programación del trabajo con un trabajo se tratará en una guía independiente.

Un _filtro de intención_ se usa para registrar un receptor de difusión para que Android pueda enrutar los mensajes correctamente. El filtro de intención se puede especificar en tiempo de ejecución (esto se conoce a veces como _receptor registrado_ en el contexto o como _registro dinámico_ ) o se puede definir de forma estática en el manifiesto de Android (un _receptor registrado con el manifiesto_ ). Xamarin. Android proporciona un atributo de C#, `IntentFilterAttribute` , que registrará de forma estática el filtro de intención (esto se tratará con más detalle más adelante en esta guía). A partir de Android 8,0, no es posible que una aplicación se registre estáticamente para una difusión implícita.

La principal diferencia entre el receptor registrado en el manifiesto y el receptor registrado por el contexto es que un receptor registrado en el contexto solo responderá a las difusiones mientras se ejecuta una aplicación, mientras que un receptor registrado en el manifiesto puede responder a las difusiones aunque la aplicación no se esté ejecutando.  

Existen dos conjuntos de API para administrar un receptor de difusión y enviar difusiones:

1. **`Context`**&ndash;La `Android.Content.Context` clase se puede utilizar para registrar un receptor de difusión que responderá a los eventos de todo el sistema. `Context`También se usa para publicar difusiones en todo el sistema.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))**&ndash;Se trata de una API que está disponible a través del [paquete NuGet de la biblioteca de compatibilidad de Xamarin V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Esta clase se usa para mantener las difusiones y los receptores de difusión aislados en el contexto de la aplicación que los utiliza. Esta clase puede ser útil para evitar que otras aplicaciones respondan a difusiones solo de la aplicación o envíen mensajes a receptores privados.

Un receptor de difusión no puede mostrar cuadros de diálogo y no se recomienda iniciar una actividad desde dentro de un receptor de difusión. Si un receptor de difusión debe notificar al usuario, debe publicar una notificación.

No es posible enlazar o iniciar un servicio desde un receptor de difusión.

En esta guía se explica cómo crear un receptor de difusión y cómo registrarlo para que pueda recibir difusiones.

## <a name="creating-a-broadcast-receiver"></a>Creación de un receptor de difusión

Para crear un receptor de difusión en Xamarin. Android, una aplicación debe crear una subclase de la `BroadcastReceiver` clase, adornarla con `BroadcastReceiverAttribute` y reemplazar el `OnReceive` método:

```csharp
[BroadcastReceiver(Enabled = true, Exported = false)]
public class SampleReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here.

        String value = intent.GetStringExtra("key");
    }
}
```

Cuando Xamarin. Android compila la clase, también actualizará archivo AndroidManifest con los metadatos necesarios para registrar el receptor. Para un receptor de difusión registrado estáticamente, el `Enabled` valor de debe establecerse en `true` , de lo contrario, Android no podrá crear una instancia del receptor.

La `Exported` propiedad controla si el receptor de difusión puede recibir mensajes desde fuera de la aplicación. Si la propiedad no se establece explícitamente, Android determina el valor predeterminado de la propiedad basándose en si hay algún filtro de intención asociado al receptor de difusión. Si hay al menos un filtro de intención para el receptor de difusión, Android asumirá que la `Exported` propiedad es `true` . Si no hay ningún filtro de intención asociado al receptor de difusión, Android asumirá que el valor es `false` .

El `OnReceive` método recibe una referencia al `Intent` que se envió al receptor de difusión. Esto hace posible que el remitente de la intención pase valores al receptor de difusión.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>Registrar estáticamente un receptor de difusión con un filtro de intención

Cuando `BroadcastReceiver` se decora con [`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute) , Xamarin. Android agregará el `<intent-filter>` elemento necesario al manifiesto de Android en tiempo de compilación. El siguiente fragmento de código es un ejemplo de receptor de difusión que se ejecutará cuando un dispositivo haya terminado de arrancar (si el usuario ha concedido los permisos de Android correspondientes):

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { Android.Content.Intent.ActionBootCompleted })]
public class MyBootReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Work that should be done when the device boots.     
    }
}
```

> [!NOTE]
> En Android 8,0 (API 26 y versiones posteriores), [Google ha colocado limitaciones](https://developer.android.com/about/versions/oreo/background) en lo que las aplicaciones pueden hacer mientras los usuarios no interactúan directamente con ellos. Estas limitaciones afectan a los servicios en segundo plano y a los receptores de difusión implícitos como `Android.Content.Intent.ActionBootCompleted` . Debido a estas limitaciones, puede que tenga dificultades para registrar un `Boot Completed` receptor de difusión en las versiones más recientes de Android. En este caso, tenga en cuenta que estas restricciones no se aplican a los servicios en primer plano, a los que se puede llamar desde el receptor de difusión.

También es posible crear un filtro de intención que responderá a las intenciones personalizadas. Considere el ejemplo siguiente:

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { "com.xamarin.example.TEST" })]
public class MySampleBroadcastReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here
    }
}
```

Es posible que las aplicaciones destinadas a Android 8,0 (nivel de API 26) o posterior no se registren estáticamente para una difusión implícita. Las aplicaciones todavía se pueden registrar estáticamente para una difusión explícita. Hay una pequeña lista de difusiones implícitas que están exentas de esta restricción. Estas excepciones se describen en la guía de [excepciones de difusión implícita](https://developer.android.com/guide/components/broadcast-exceptions.html) en la documentación de Android. Las aplicaciones que están interesadas en difusiones implícitas deben hacerlo dinámicamente mediante el `RegisterReceiver` método. Esto se describe a continuación.

### <a name="context-registering-a-broadcast-receiver"></a>Context-Registering un receptor de difusión

El registro de contexto (también denominado registro dinámico) de un receptor se realiza invocando el `RegisterReceiver` método y se debe anular el registro del receptor de difusión con una llamada al `UnregisterReceiver` método. Para evitar la pérdida de recursos, es importante anular el registro del receptor cuando ya no es relevante para el contexto (la actividad o servicio). Por ejemplo, un servicio puede difundir un intento de informar a una actividad de que las actualizaciones están disponibles para que se muestren al usuario. Cuando se inicia la actividad, se registra en esas intenciones. Cuando la actividad se mueve al fondo y ya no es visible para el usuario, debe anular el registro del receptor porque la interfaz de usuario para mostrar las actualizaciones ya no está visible. El siguiente fragmento de código es un ejemplo de cómo registrar y anular el registro de un receptor de difusión en el contexto de una actividad:

```csharp
[Activity(Label = "MainActivity", MainLauncher = true, Icon = "@mipmap/icon")]
public class MainActivity: Activity
{
    MySampleBroadcastReceiver receiver;

    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        receiver = new MySampleBroadcastReceiver();

        // Code omitted for clarity
    }

    protected override void OnResume()
    {
        base.OnResume();
        RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
        // Code omitted for clarity
    }

    protected override void OnPause()
    {
        UnregisterReceiver(receiver);
        // Code omitted for clarity
        base.OnPause();
    }
}
```

En el ejemplo anterior, cuando la actividad entra en primer plano, registrará un receptor de difusión que escuchará una intención personalizada mediante el método de `OnResume` ciclo de vida. A medida que la actividad se mueve al fondo, el `OnPause()` método anulará el registro del receptor.

## <a name="publishing-a-broadcast"></a>Publicar una difusión

Una difusión puede publicarse en todas las aplicaciones instaladas en el dispositivo creando un objeto de intención y enviándola con el `SendBroadcast` `SendOrderedBroadcast` método o.  

1. Métodos context **. SendBroadcast** &ndash; Hay varias implementaciones de este método.
   Estos métodos difundirán la intención a todo el sistema. Receptores de difusión que recibirán el intento en un orden indeterminado. Esto proporciona una gran flexibilidad, pero significa que es posible que otras aplicaciones se registren y reciban la intención. Esto puede suponer un riesgo de seguridad potencial. Es posible que las aplicaciones necesiten implementar la seguridad adicional para evitar el acceso no autorizado. Una posible solución es usar `LocalBroadcastManager` , que solo enviará mensajes dentro del espacio privado de la aplicación. Este fragmento de código es un ejemplo de cómo enviar una intención con uno de los `SendBroadcast` métodos:

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    Este fragmento de código es otro ejemplo de envío de una difusión mediante el  `Intent.SetAction` método para identificar la acción:

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context. SendOrderedBroadcast** &ndash; este método es muy similar a `Context.SendBroadcast` , con la diferencia de que la intención se publicará una vez a los receptores, en el orden en que se registraron los receptores.

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

La [biblioteca de compatibilidad con Xamarin V4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) proporciona una clase auxiliar denominada [`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html) . `LocalBroadcastManager`Está diseñado para aplicaciones que no desean enviar o recibir difusiones de otras aplicaciones del dispositivo. El `LocalBroadcastManager` solo publicará mensajes en el contexto de la aplicación y solo en los receptores de difusión registrados con `LocalBroadcastManager` . Este fragmento de código es un ejemplo de registro de un receptor de difusión con `LocalBroadcastManager` :

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

Otras aplicaciones del dispositivo no pueden recibir los mensajes que se publican con el `LocalBroadcastManager` . Este fragmento de código muestra cómo enviar una intención mediante `LocalBroadcastManager` :

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>Vínculos relacionados

- [API de BroadcastReceiver](xref:Android.Content.BroadcastReceiver)
- [API context. RegisterReceiver](xref:Android.Content.Context.RegisterReceiver*)
- [API context. SendBroadcast](xref:Android.Content.Context.SendBroadcast*)
- [API context. UnregisterReceiver](xref:Android.Content.Context.UnregisterReceiver*)
- [Intención de API](xref:Android.Content.Intent)
- [API de IntentFilter](xref:Android.App.IntentFilterAttribute)
- [LocalBroadcastManager (documentos de Android)](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Notificaciones locales en la guía de Android](~/android/app-fundamentals/notifications/local-notifications.md)
- [Biblioteca de compatibilidad de Android V4 (NuGet)](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
