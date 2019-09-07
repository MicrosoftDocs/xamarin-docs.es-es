---
title: Google Cloud Messaging
description: Google Cloud Messaging (GCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. En este artículo se proporciona información general sobre cómo funciona GCM y se explica cómo configurar Google Services para que la aplicación pueda usar GCM.
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/02/2019
ms.openlocfilehash: fef12ab6dc2c42f97e5a2725f58ba4392c21762f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754500"
---
# <a name="google-cloud-messaging"></a>Google Cloud Messaging

> [!WARNING]
> Google dejó de usar GCM a partir del 10 de abril de 2018. Es posible que los siguientes documentos y proyectos de ejemplo ya no se mantengan. El servidor y las API de cliente de GCM de Google se quitarán en cuanto 29 de mayo de 2019. Google recomienda migrar aplicaciones de GCM a Firebase Cloud Messaging (FCM). Para obtener más información sobre el desuso y la migración de GCM, vea [Google en desuso de mensajería en la nube](https://developers.google.com/cloud-messaging/).
>
> Para empezar a usar Firebase Cloud Messaging con Xamarin, consulte [Firebase Cloud Messaging](firebase-cloud-messaging.md).

_Google Cloud Messaging (GCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. En este artículo se proporciona información general sobre cómo funciona GCM y se explica cómo configurar Google Services para que la aplicación pueda usar GCM._

[![Google Cloud Messaging logotipo](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

En este tema se proporciona información general de alto nivel sobre cómo Google Cloud Messaging enruta los mensajes entre la aplicación y un servidor de aplicaciones, y proporciona un procedimiento paso a paso para adquirir credenciales para que la aplicación pueda usar los servicios de GCM.

## <a name="overview"></a>Información general

Google Cloud Messaging (GCM) es un servicio que controla el envío, el enrutamiento y la puesta en cola de los mensajes entre las aplicaciones de servidor y las aplicaciones de cliente móvil. Una *aplicación cliente* es una aplicación habilitada para GCM que se ejecuta en un dispositivo. El *servidor de aplicaciones* (proporcionado por usted o su empresa) es el servidor habilitado para GCM con el que se comunica la aplicación cliente a través de GCM:

[![GCM reside entre la aplicación cliente y el servidor de aplicaciones](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

Con GCM, los servidores de aplicaciones pueden enviar mensajes a un único dispositivo, a un grupo de dispositivos o a una serie de dispositivos que están suscritos a un tema. La aplicación cliente puede usar GCM para suscribirse a mensajes de nivel inferior desde un servidor de aplicaciones (por ejemplo, para recibir notificaciones remotas). Además, GCM permite a las aplicaciones cliente enviar mensajes ascendentes de vuelta al servidor de aplicaciones.

Para obtener información sobre cómo implementar un servidor de aplicaciones para GCM, consulte [acerca del servidor de conexión GCM](https://developers.google.com/cloud-messaging/server).

## <a name="google-cloud-messaging-in-action"></a>Google Cloud Messaging en acción

Cuando se envían mensajes de nivel inferior desde un servidor de aplicaciones a una aplicación cliente, el servidor de aplicaciones envía el mensaje a un *servidor de conexión GCM*. el servidor de conexión de GCM, a su vez, reenvía el mensaje a un dispositivo que ejecuta la aplicación cliente. Los mensajes se pueden enviar a través de HTTP o [XMPP](https://developers.google.com/cloud-messaging/ccs) (Protocolo de mensajería y presencia extensible). Dado que las aplicaciones cliente no siempre están conectadas o en ejecución, el servidor de conexión de GCM pone en cola y almacena los mensajes, enviándolos a las aplicaciones cliente a medida que se vuelven a conectar y están disponibles. Del mismo modo, GCM pondrá en cola los mensajes ascendentes desde la aplicación cliente al servidor de aplicaciones si el servidor de aplicaciones no está disponible.

GCM usa las credenciales siguientes para identificar el servidor de aplicaciones y la aplicación cliente, y usa estas credenciales para autorizar las transacciones de mensajes a través de GCM:

- **Clave de API** La *clave de API* proporciona al servidor de aplicaciones acceso a Google Services. &ndash; GCM usa esta clave para autenticar el servidor de aplicaciones.
    Antes de poder usar el servicio GCM, primero debe obtener una clave de API de la [consola para desarrolladores de Google](https://console.developers.google.com/) mediante la creación de un *proyecto*. La clave de API debe mantenerse segura; para obtener más información sobre cómo proteger la clave de API, consulte [prácticas recomendadas para usar claves de API de forma segura](https://support.google.com/cloud/answer/6310037?hl=en).

- **ID. de remitente** El ID. de remitente autoriza el servidor de aplicaciones a la aplicación &ndash; cliente es un número único que identifica el servidor de aplicaciones que puede enviar mensajes a la aplicación cliente. &ndash;
    El identificador del remitente también es el número del proyecto; al registrar el proyecto, se obtiene el identificador de remitente de la consola de desarrolladores de Google.

- **Token de registro** El *token de registro* es la identidad de GCM de la aplicación cliente en un dispositivo determinado. &ndash; El token de registro se genera en tiempo &ndash; de ejecución. la aplicación recibe un token de registro cuando se registra por primera vez en GCM mientras se ejecuta en un dispositivo. El token de registro autoriza a una instancia de la aplicación cliente (que se ejecuta en ese dispositivo concreto) a recibir mensajes de GCM.

- **Identificador** de la aplicación &ndash; La identidad de la aplicación cliente (independiente de cualquier dispositivo determinado) que se registra para recibir mensajes de GCM. En Android, el identificador de la aplicación es el nombre del paquete registrado en **archivo AndroidManifest. XML**, `com.xamarin.gcmexample`como.

[Configuración de Google Cloud Messaging](#settingup) (más adelante en esta guía) se proporcionan instrucciones detalladas para crear un proyecto y generar estas credenciales.

En las secciones siguientes se explica cómo se usan estas credenciales cuando las aplicaciones cliente se comunican con los servidores de aplicaciones a través de GCM.

### <a name="registration-with-gcm"></a>Registro con GCM

Una aplicación cliente instalada en un dispositivo debe registrarse primero en GCM para que pueda tener lugar la mensajería. La aplicación cliente debe completar los pasos de registro que se muestran en el diagrama siguiente:

[![Pasos de registro de aplicaciones](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1. La aplicación cliente se pone en contacto con GCM para obtener un token de registro, pasando el identificador de remitente a GCM.

2. GCM devuelve un token de registro a la aplicación cliente.

3. La aplicación cliente reenvía el token de registro al servidor de aplicaciones.

El servidor de aplicaciones almacena en caché el token de registro para las comunicaciones posteriores con la aplicación cliente. Opcionalmente, el servidor de aplicaciones puede devolver una confirmación a la aplicación cliente para indicar que se recibió el token de registro. Una vez que se realiza este protocolo de enlace, la aplicación cliente puede recibir mensajes de (o enviar mensajes a) el servidor de aplicaciones.

Cuando la aplicación cliente ya no quiere recibir mensajes del servidor de aplicaciones, puede enviar una solicitud al servidor de aplicaciones para eliminar el token de registro. Si la aplicación cliente recibe mensajes de tema (que se explican más adelante en este artículo), puede cancelar la suscripción al tema.
Si se desinstala la aplicación cliente de un dispositivo, GCM lo detecta y notifica automáticamente al servidor de aplicaciones que elimine el token de registro.

Las [aplicaciones cliente de registro](https://developers.google.com/cloud-messaging/registration) de Google explican el proceso de registro con más detalle. se explica cómo anular el registro y quitar la suscripción, y se describe el proceso de anulación del registro cuando se desinstala una aplicación cliente.

### <a name="downstream-messaging"></a>Mensajería de nivel inferior

Cuando el servidor de aplicaciones envía un mensaje de nivel inferior a la aplicación cliente, sigue los pasos que se muestran en el diagrama siguiente:

[![Diagrama de enrutamiento y reenvío de mensajería de nivel inferior](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1. El servidor de aplicaciones envía el mensaje a GCM.

2. Si el dispositivo cliente no está disponible, el servidor GCM almacena el mensaje en una cola para su transmisión posterior.

3. Cuando el dispositivo cliente está disponible, GCM envía el mensaje a la aplicación cliente en ese dispositivo.

4. La aplicación cliente recibe el mensaje de GCM y lo controla en consecuencia. Por ejemplo, si el mensaje es una notificación remota, se presenta al usuario.

En este escenario de mensajería (donde el servidor de aplicaciones envía un mensaje a una sola aplicación cliente), los mensajes pueden tener una longitud de hasta 4 KB.

Para obtener información detallada (incluidos ejemplos de código) sobre la recepción de mensajes GCM de bajada en Android, consulte [notificaciones remotas](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md).

#### <a name="topic-messaging"></a>Mensajería de temas

La *mensajería de tema* es un tipo de mensajería de nivel inferior en el que el servidor de aplicaciones envía un solo mensaje a varios dispositivos de aplicación cliente que se suscriben a un tema (por ejemplo, una previsión meteorológica). Los mensajes de tema pueden tener una longitud de hasta 2 KB y la mensajería de temas admite hasta 1 millón suscripciones por aplicación. Si GCM solo se usa para la mensajería de temas, no es necesario que la aplicación cliente envíe un token de registro al servidor de aplicaciones. La [mensajería de temas de implementación](https://developers.google.com/cloud-messaging/topic-messaging) de Google explica cómo enviar mensajes desde un servidor de aplicaciones a varios dispositivos que se suscriben a un tema determinado.

#### <a name="group-messaging"></a>Mensajería de grupo

La *mensajería de grupo* es un tipo de mensajería de nivel inferior en el que el servidor de aplicaciones envía un solo mensaje a varios dispositivos de aplicación cliente que pertenecen a un grupo (por ejemplo, un grupo de dispositivos que pertenecen a un solo usuario). Los mensajes de grupo pueden tener una longitud de hasta 2 KB para dispositivos iOS y hasta 4 KB de longitud para dispositivos Android. Un grupo está limitado a un máximo de 20 miembros. La [mensajería de grupos de dispositivos](https://developers.google.com/cloud-messaging/notifications) de Google explica cómo los servidores de aplicaciones pueden enviar un solo mensaje a varias instancias de aplicación cliente que se ejecutan en dispositivos que pertenecen a un grupo.

### <a name="upstream-messaging"></a>Mensajería ascendente

Si la aplicación cliente se conecta a un servidor que admite [XMPP](https://developers.google.com/cloud-messaging/ccs), puede enviar mensajes de vuelta al servidor de aplicaciones, tal como se muestra en el diagrama siguiente:

[![Diagrama de mensajería ascendente](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1. La aplicación cliente envía un mensaje al servidor de conexión de GCM XMPP.

2. Si el servidor de aplicaciones está desconectado, el servidor GCM almacena el mensaje en una cola para su reenvío posterior.

3. Cuando el servidor de aplicaciones se vuelve a conectar, GCM reenvía el mensaje al servidor de aplicaciones.

4. El servidor de aplicaciones analiza el mensaje para comprobar la identidad de la aplicación cliente y, a continuación, envía una "confirmación" a GCM para confirmar la recepción de mensajes.

5. El servidor de aplicaciones procesa el mensaje.

En [los mensajes ascendentes](https://developers.google.com/cloud-messaging/ccs#upstream) de Google se explica cómo estructurar los mensajes codificados con JSON y enviarlos a los servidores de aplicaciones que ejecutan el servidor de conexión en la nube basado en XMPP de Google.

<a name="settingup" />

## <a name="setting-up-google-cloud-messaging"></a>Configuración de Google Cloud Messaging

Antes de poder usar los servicios de GCM en la aplicación, primero debe adquirir las credenciales para el acceso a los servidores GCM de Google. En las secciones siguientes se describen los pasos necesarios para completar este proceso:

### <a name="enable-google-services-for-your-app"></a>Habilitación de Google Services para su aplicación

1. Inicie sesión en la [consola de desarrolladores de Google](https://developers.google.com/mobile/add?platform=android) con su cuenta de Google (es decir, su dirección de Gmail) y cree un nuevo proyecto. Si tiene un proyecto existente, elija el proyecto que desea que esté habilitado para GCM. En el ejemplo siguiente, se crea un nuevo proyecto denominado **XamarinGCM** :

    [![Crear proyecto de XamarinGCM](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2. Después, escriba el nombre del paquete de la aplicación (en este ejemplo, el nombre del paquete es **com. Xamarin. gcmexample**) y haga clic en **continuar para elegir y configurar servicios**:

    [![Escribir el nombre del paquete](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    Tenga en cuenta que el nombre del paquete también es el identificador de la aplicación.

3. En la sección **elegir y configurar servicios** se enumeran los servicios de Google que se pueden agregar a la aplicación. Haga clic en **mensajería en la nube**:

    [![Elegir mensajería en la nube](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4. A continuación, haga clic en **Habilitar Google Cloud Messaging**:

    [![Habilitar Google Cloud Messaging](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5. Se genera una **clave de API de servidor** y un identificador de **remitente** para la aplicación. Registre estos valores y haga clic en **cerrar**:

    [![Se muestra la clave de API del servidor y el identificador del remitente](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    Proteja la clave &ndash; de API no está pensada para uso público. Si la clave de API se ve comprometida, los servidores no autorizados podrían publicar mensajes en las aplicaciones cliente.
    [Prácticas recomendadas para el uso seguro de claves de API](https://support.google.com/cloud/answer/6310037?hl=en) proporciona instrucciones útiles para proteger la clave de API.

### <a name="view-your-project-settings"></a>Visualización de la configuración del proyecto

Puede ver la configuración del proyecto en cualquier momento iniciando sesión en la [consola de Google Cloud](https://console.cloud.google.com/) y seleccionando el proyecto. Por ejemplo, puede ver el **identificador del remitente** seleccionando el proyecto en el menú desplegable de la parte superior de la página (en este ejemplo, el proyecto se denomina **XamarinGCM**). El identificador del remitente es el número de proyecto como se muestra en esta captura de pantalla (el identificador del remitente es **9349932736**):

[![Ver el identificador del remitente](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

Para ver la **clave de API**, haga clic en **Administrador de API** y, a continuación, haga clic en **credenciales**:

[![Ver la clave de API](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)

## <a name="for-further-reading"></a>Más información

- El [registro de aplicaciones cliente](https://developers.google.com/cloud-messaging/registration) de Google describe el proceso de registro del cliente con más detalle y proporciona información acerca de cómo configurar el reintento automático y mantener el estado de registro sincronizado.

- [Rfc 6120](https://tools.ietf.org/html/rfc6120) y [RFC 6121](https://tools.ietf.org/html/rfc6121) explican y definen el protocolo de mensajería y presencia extensible (XMPP).

## <a name="summary"></a>Resumen

En este artículo se proporciona información general sobre Google Cloud Messaging (GCM). Se han explicado las distintas credenciales que se usan para identificar y autorizar la mensajería entre los servidores de aplicaciones y las aplicaciones cliente. Se ilustran los escenarios de mensajería más comunes y se detallan los pasos para registrar la aplicación con GCM para usar los servicios de GCM.

## <a name="related-links"></a>Vínculos relacionados

- [Mensajería en la nube](https://developers.google.com/cloud-messaging/)
