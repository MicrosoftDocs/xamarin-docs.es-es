---
title: Notificaciones de envío en iOS
description: En este documento se describe cómo trabajar con notificaciones de envío en iOS 9 y versiones anteriores. Se describen los certificados, el registro con el servicio de puerta de enlace de notificaciones push de Apple (APNS), etc.
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: ee2c54f2369682571520370e09d6d39c2cd86fa5
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290590"
---
# <a name="push-notifications-in-ios"></a>Notificaciones de envío en iOS

> [!IMPORTANT]
> La información de esta sección se refiere a iOS 9 y versiones anteriores, se ha dejado aquí para admitir versiones anteriores de iOS. Para iOS 10 y versiones posteriores, consulte la [Guía del marco de trabajo de notificaciones de usuario](~/ios/platform/user-notifications/index.md) para admitir la notificación local y remota en un dispositivo iOS.

Las notificaciones de extracción deben ser breves y contener suficientes datos para notificar a la aplicación móvil que debe ponerse en contacto con la aplicación de servidor para obtener una actualización. Por ejemplo, cuando llega un nuevo correo electrónico, la aplicación de servidor solo notificaría a la aplicación móvil que ha llegado el nuevo correo electrónico. La notificación no contendría el correo electrónico nuevo. A continuación, la aplicación móvil recuperará los correos electrónicos nuevos del servidor cuando sea adecuado.

En el centro de notificaciones Push en iOS se encuentra el *servicio de puerta de enlace de notificaciones push de Apple (APNS)* . Se trata de un servicio proporcionado por Apple que es responsable de enrutar las notificaciones de un servidor de aplicaciones a dispositivos iOS.
La siguiente imagen muestra la topología de notificaciones de envío para iOS: ![](remote-notifications-in-ios-images/image4.png "Esta imagen muestra la topología de notificaciones de envío para iOS")

Las notificaciones remotas son cadenas con formato JSON que cumplen el formato y los protocolos especificados en la sección de [carga de notificación](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1) de la guía de [programación de notificaciones de envío y local](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) en la documentación para desarrolladores de [iOS ](https://developer.apple.com/devcenter/ios/index.action).

Apple mantiene dos entornos de APNS: un *espacio aislado* y un entorno de *producción* . El entorno de espacio aislado está pensado para realizar pruebas durante la fase de desarrollo y `gateway.sandbox.push.apple.com` se puede encontrar en en el puerto TCP 2195. El entorno de producción está pensado para usarse en aplicaciones que se han implementado y se pueden `gateway.push.apple.com` encontrar en el puerto TCP 2195.

## <a name="requirements"></a>Requisitos

La notificación push debe observar las reglas siguientes dictadas por la arquitectura de APNS:

- **límite de mensajes de 256 bytes** : el tamaño completo del mensaje de la notificación no debe superar los 256 bytes.
- **No hay confirmación de recepción** : APNS no proporciona al remitente ninguna notificación de que un mensaje lo hizo al destinatario previsto. Si el dispositivo es inaccesible y se envían varias notificaciones secuenciales, se perderán todas las notificaciones excepto la más reciente. Solo se entregará la notificación más reciente al dispositivo.
- **Cada aplicación requiere un certificado seguro** : la comunicación con APNS debe realizarse a través de SSL.


## <a name="creating-and-using-certificates"></a>Crear y usar certificados

Cada uno de los entornos mencionados en la sección anterior requiere su propio certificado. En esta sección se explica cómo crear un certificado, asociarlo a un perfil de aprovisionamiento y, a continuación, obtener un certificado de intercambio de información personal para su uso con PushSharp.

1. Para crear certificados, vaya al portal de aprovisionamiento de iOS en el sitio web de Apple, tal como se muestra en la siguiente captura de pantalla (observe el elemento de menú ID. de aplicación a la izquierda):

    [![](remote-notifications-in-ios-images/image5new.png "El portal de aprovisionamiento de iOS en el sitio web de Apple")](remote-notifications-in-ios-images/image5new.png#lightbox)

2. A continuación, vaya a la sección del identificador de la aplicación y cree un nuevo identificador de aplicación, tal como se muestra en la siguiente captura de pantalla:

    [![](remote-notifications-in-ios-images/image6new.png "Vaya a la sección identificadores de aplicación y cree un nuevo identificador de aplicación.")](remote-notifications-in-ios-images/image6new.png#lightbox)

3. Al hacer clic en el **+** botón, podrá escribir la descripción y un identificador de paquete para el identificador de la aplicación, como se muestra en la siguiente captura de pantalla:

    [![](remote-notifications-in-ios-images/image7new.png "Escriba la descripción y un identificador de lote para el ID. de aplicación")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. Asegúrese de seleccionar el **identificador de aplicación explícito** y de que el identificador de paquete no finaliza `*` con un. Esto creará un identificador adecuado para varias aplicaciones y los certificados de notificación de extracción deben ser para una sola aplicación.

5. En App Services, seleccione **notificaciones de envío**:

    [![](remote-notifications-in-ios-images/image8new.png "Seleccionar notificaciones de envío")](remote-notifications-in-ios-images/image8new.png#lightbox)

6. Y presione **Enviar** para confirmar el registro del nuevo ID. de aplicación:

    [![](remote-notifications-in-ios-images/image9new.png "Confirmar el registro del nuevo ID. de aplicación")](remote-notifications-in-ios-images/image9new.png#lightbox)

7. A continuación, debe crear el certificado para el identificador de la aplicación. En el panel de navegación izquierdo, vaya a **certificados > todos** y seleccione `+` el botón, tal como se muestra en la siguiente captura de pantalla:

    [![](remote-notifications-in-ios-images/image10new.png "Crear el certificado para el identificador de la aplicación")](remote-notifications-in-ios-images/image8.png#lightbox)

8. Seleccione si desea usar un certificado de desarrollo o de producción:

    [![](remote-notifications-in-ios-images/image11new.png "Seleccionar un certificado de desarrollo o de producción")](remote-notifications-in-ios-images/image11new.png#lightbox)

9. Después, seleccione el nuevo identificador de aplicación que acabamos de crear:

    [![](remote-notifications-in-ios-images/image12new.png "Seleccione el nuevo identificador de aplicación que acaba de crear")](remote-notifications-in-ios-images/image12new.png#lightbox)

10. Se mostrarán instrucciones que le guiarán por el proceso de creación de una *solicitud de firma de certificado* con la aplicación de **acceso a llaves** en el equipo Mac.

11. Ahora que se ha creado el certificado, se debe usar como parte del proceso de compilación para firmar la aplicación para que se pueda registrar con APNs. Esto requiere la creación e instalación de un perfil de aprovisionamiento que use el certificado.

12. Para crear un perfil de aprovisionamiento de desarrollo, vaya a la sección **perfiles de aprovisionamiento** y siga los pasos para crearlo con el identificador de aplicación que acabamos de crear.

13. Una vez que haya creado el perfil de aprovisionamiento, abra el **organizador de Xcode** y actualícela. Si el perfil de aprovisionamiento que creó no aparece, puede que sea necesario descargar el perfil desde el portal de aprovisionamiento de iOS e importarlo manualmente. En la captura de pantalla siguiente se muestra un ejemplo del organizador con el perfil de aprovisionamiento agregado:  
    [![](remote-notifications-in-ios-images/image13new.png "Esta captura de pantalla muestra un ejemplo del organizador con el perfil de aprovisionamiento agregado")](remote-notifications-in-ios-images/image13new.png#lightbox)

14. En este momento, es necesario configurar el proyecto de Xamarin. iOS para usar este perfil de aprovisionamiento recién creado. Esto se realiza desde el cuadro de diálogo **Opciones del proyecto** , en la pestaña firma de **lote de iOS** , tal como se muestra en la siguiente captura de pantalla:  
    [![](remote-notifications-in-ios-images/image11.png "Configurar el proyecto de Xamarin. iOS para usar este perfil de aprovisionamiento recién creado")](remote-notifications-in-ios-images/image11.png#lightbox)

En este punto, la aplicación está configurada para trabajar con notificaciones de extracción. Sin embargo, todavía hay algunos pasos más necesarios con el certificado. Este certificado está en formato DER y no es compatible con PushSharp, que requiere un certificado de intercambio de información personal (PKCS12). Para convertir el certificado de modo que sea utilizable por PushSharp, siga estos pasos finales:

1. **Descargue el archivo de certificado** -login en el portal de aprovisionamiento de iOS, elija la pestaña certificados, seleccione el certificado asociado con el perfil de aprovisionamiento correcto y elija **Descargar** .
1. **Abrir acceso a llaves** : se trata de una interfaz de GUI para el sistema de administración de contraseñas en OS X.
1. **Importar el certificado** : Si el certificado no está ya instalado, **archivo... Importar elementos** desde el menú acceso a llaves. Navegue hasta el certificado que exportó anteriormente y selecciónelo.
1. **Exportar el certificado** : expanda el certificado para que la clave privada asociada esté visible, haga clic con el botón derecho en la clave y elija Exportar. Se le pedirá un nombre de archivo y una contraseña para el archivo exportado.

En este momento, hemos terminado con los certificados. Hemos creado un certificado que se usará para firmar las aplicaciones de iOS y convertir ese certificado a un formato que se pueda usar con PushSharp en una aplicación de servidor. A continuación, veamos cómo las aplicaciones de iOS interactúan con APNS.

## <a name="registering-with-apns"></a>Registro con APNS

Antes de que una aplicación de iOS pueda recibir notificaciones remotas, debe registrarse con APNS. APNS generará un token de dispositivo único y lo devolverá a la aplicación de iOS. La aplicación de iOS tomará el token del dispositivo y, a continuación, se registrará en el servidor de aplicaciones. Una vez hecho todo esto, el registro se ha completado y el servidor de aplicaciones puede enviar notificaciones de envío al dispositivo móvil.

En teoría, el token del dispositivo puede cambiar cada vez que una aplicación de iOS se registra con APNS, pero en la práctica esto no sucede con frecuencia. Como optimización, una aplicación puede almacenar en caché el token de dispositivo más reciente y solo actualizar el servidor de aplicaciones cuando cambia. En el diagrama siguiente se ilustra el proceso de registro y la obtención de un token de dispositivo:

 ![](remote-notifications-in-ios-images/image12.png "En este diagrama se muestra el proceso de registro y la obtención de un token de dispositivo.")

El registro con APNS se controla en `FinishedLaunching` el método de la clase de delegado de `RegisterForRemoteNotificationTypes` aplicación mediante una `UIApplication` llamada a en el objeto actual. Cuando una aplicación de iOS se registra con APNS, también debe especificar los tipos de notificaciones remotas que desea recibir. Estos tipos de notificación remota se declaran `UIRemoteNotificationType`en la enumeración. El siguiente fragmento de código es un ejemplo de cómo se puede registrar una aplicación de iOS para recibir notificaciones remotas de notificaciones y alertas:

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

    UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications ();
} else {
    UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
    UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
}
```

La solicitud de registro de APNS se produce en segundo plano: cuando se recibe la respuesta, iOS llamará al `AppDelegate` método `RegisteredForRemoteNotifications` en la clase y pasará el token de dispositivo registrado. El token estará incluido en un `NSData` objeto. En el fragmento de código siguiente se muestra cómo recuperar el token de dispositivo que ha proporcionado APNS:

```csharp
public override void RegisteredForRemoteNotifications (
UIApplication application, NSData deviceToken)
{
    // Get current device token
    var DeviceToken = deviceToken.Description;
    if (!string.IsNullOrWhiteSpace(DeviceToken)) {
        DeviceToken = DeviceToken.Trim('<').Trim('>');
    }

    // Get previous device token
    var oldDeviceToken = NSUserDefaults.StandardUserDefaults.StringForKey("PushDeviceToken");

    // Has the token changed?
    if (string.IsNullOrEmpty(oldDeviceToken) || !oldDeviceToken.Equals(DeviceToken))
    {
        //TODO: Put your own logic here to notify your server that the device token has changed/been created!
    }

    // Save new device token
    NSUserDefaults.StandardUserDefaults.SetString(DeviceToken, "PushDeviceToken");
}
```

Si se produce un error en el registro por algún motivo (por ejemplo, el dispositivo no está conectado a Internet) `FailedToRegisterForRemoteNotifications` , iOS llamará a en la clase de delegado de aplicación. En el fragmento de código siguiente se muestra cómo mostrar una alerta al usuario que informa de que se produjo un error en el registro:

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>Mantenimiento de tokens de dispositivo

Los tokens de dispositivo expirarán o cambiarán con el tiempo. Por este hecho, las aplicaciones de servidor deberán realizar alguna limpieza y purgar estos tokens caducados o modificados. Cuando una aplicación envía una notificación push a un dispositivo que tiene un token expirado, APNS registrará y guardará ese token caducado. A continuación, los servidores pueden consultar a APNS para averiguar qué tokens han expirado.

APNS que se usa para proporcionar un *servicio de comentarios* : un punto de conexión HTTPS que se autentica a través del certificado que se creó para enviar notificaciones Push y devuelve datos sobre los tokens que han expirado. Apple ha quedado en desuso y se ha quitado.

En su lugar, hay un nuevo código de Estado HTTP para el caso en el que el servicio de comentarios indicó anteriormente:

> 410: el token del dispositivo ya no está activo para el tema.

Además, una nueva `timestamp` clave de datos JSON estará en el cuerpo de la respuesta:

> Si el valor del encabezado: status es 410, el valor de esta clave es la última vez que APNs confirmó que el token del dispositivo ya no es válido para el tema.
>
> Detenga la inserción de notificaciones hasta que el dispositivo registre un token con una marca de tiempo posterior con el proveedor.

## <a name="summary"></a>Resumen

En esta sección se presentan los conceptos clave relacionados con las notificaciones de envío en iOS. Se explicó el rol del servicio de puerta de enlace de notificaciones push de Apple (APNS). Después, se ha tratado la creación y el uso de los certificados de seguridad esenciales para APNS. Por último, este documento ha finalizado con un debate sobre cómo los servidores de aplicaciones pueden usar los *servicios de comentarios* para detener el seguimiento de los tokens de dispositivo caducados.


## <a name="related-links"></a>Vínculos relacionados

- [Notificaciones: demostración de notificaciones locales y remotas (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/notifications)
- [Notificaciones de envío y locales para desarrolladores](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
