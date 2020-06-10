---
title: Firebase Cloud Messaging
description: Firebase Cloud Messaging (FCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. En este artículo se proporciona información general sobre cómo funciona FCM y se explica cómo configurar Google Services para que la aplicación pueda usar FCM.
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: acb0e9ea23d4043a72b9d2688501c576a3d43358
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570270"
---
# <a name="firebase-cloud-messaging"></a>Firebase Cloud Messaging

_Firebase Cloud Messaging (FCM) es un servicio que facilita la mensajería entre aplicaciones móviles y aplicaciones de servidor. En este artículo se proporciona información general sobre cómo funciona FCM y se explica cómo configurar Google Services para que la aplicación pueda usar FCM._

[![Imagen de Firebase Cloud Messaging Hero](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

En este tema se proporciona información general de alto nivel sobre cómo Firebase Cloud Messaging enruta los mensajes entre la aplicación de Xamarin. Android y un servidor de aplicaciones, y proporciona un procedimiento paso a paso para adquirir credenciales para que la aplicación pueda usar los servicios de FCM.

## <a name="overview"></a>Información general

Firebase Cloud Messaging (FCM) es un servicio multiplataforma que controla el envío, el enrutamiento y la puesta en cola de los mensajes entre las aplicaciones de servidor y las aplicaciones de cliente móvil. FCM es el sucesor de Google Cloud Messaging (GCM) y se basa en Google Play Services.

Como se muestra en el diagrama siguiente, FCM actúa como intermediario entre los remitentes de mensajes y los clientes. Una *aplicación cliente* es una aplicación habilitada para FCM que se ejecuta en un dispositivo. El *servidor de aplicaciones* (proporcionado por usted o su empresa) es el servidor habilitado para FCM con el que se comunica la aplicación cliente a través de FCM. A diferencia de GCM, FCM permite enviar mensajes a las aplicaciones cliente directamente a través de la GUI de notificaciones de la consola de Firebase:

[![FCM se encuentra entre la aplicación cliente y un servidor de aplicaciones](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

Con FCM, los servidores de aplicaciones pueden enviar mensajes a un único dispositivo, a un grupo de dispositivos o a una serie de dispositivos que están suscritos a un tema. Una aplicación cliente puede usar FCM para suscribirse a mensajes de nivel inferior desde un servidor de aplicaciones (por ejemplo, para recibir notificaciones remotas). Para obtener más información sobre los diferentes tipos de mensajes de Firebase, vea [About FCM messages](https://firebase.google.com/docs/cloud-messaging/concept-options).

## <a name="firebase-cloud-messaging-in-action"></a><a name="fcm-in-action"></a>Mensajería en la nube Firebase en acción

Cuando se envía un mensaje de nivel inferior a una aplicación cliente desde un servidor de aplicaciones, el servidor de aplicaciones envía el mensaje a un *servidor de conexión FCM* proporcionado por Google; el servidor de conexión FCM, a su vez, reenvía el mensaje a un dispositivo que ejecuta la aplicación cliente. Los mensajes se pueden enviar a través de HTTP o [XMPP](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref) (Protocolo de mensajería y presencia extensible). Dado que las aplicaciones cliente no siempre están conectadas o en ejecución, el servidor de conexión FCM pone en cola y almacena los mensajes, enviándolos a las aplicaciones cliente a medida que se vuelven a conectar y están disponibles. Del mismo modo, FCM pondrá en cola los mensajes ascendentes desde la aplicación cliente al servidor de aplicaciones si el servidor de aplicaciones no está disponible. Para obtener más información sobre los servidores de conexión de FCM, consulte [acerca de Firebase Cloud Messaging Server](https://firebase.google.com/docs/cloud-messaging/server).

FCM usa las credenciales siguientes para identificar el servidor de aplicaciones y la aplicación cliente, y usa estas credenciales para autorizar las transacciones de mensajes a través de FCM:

- <a name="fcm-in-action-sender-id"></a>**ID.** &ndash; de remitente El *ID* . de remitente es un valor numérico único que se asigna al crear el proyecto Firebase. El identificador del remitente se usa para identificar cada servidor de aplicaciones que puede enviar mensajes a la aplicación cliente. El identificador del remitente también es el número del proyecto; al registrar el proyecto, se obtiene el identificador de remitente de la consola de Firebase. Un ejemplo de un identificador de remitente es `496915549731` .

- <a name="fcm-in-action-api-key"></a>**Clave** &ndash; de API La *clave de API* proporciona al servidor de aplicaciones acceso a Firebase Services. FCM usa esta clave para autenticar el servidor de aplicaciones. Esta credencial también se conoce como la *clave de servidor* o la *clave de API Web*. Un ejemplo de una clave de API es `AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk` .

- <a name="fcm-in-action-app-id"></a>IDENTIFICADOR de la **aplicación** &ndash; La identidad de la aplicación cliente (independiente de cualquier dispositivo determinado) que se registra para recibir mensajes de FCM. Un ejemplo de un identificador de aplicación es `1:415712510732:android:0e1eb7a661af2460` .

- <a name="fcm-in-action-registration-token"></a>**Token** &ndash; de registro El *token de registro* (también conocido como *ID. de instancia*) es la identidad FCM de la aplicación cliente en un dispositivo determinado. El token de registro se genera en tiempo de ejecución. &ndash; la aplicación recibe un token de registro cuando se registra por primera vez con FCM mientras se ejecuta en un dispositivo. El token de registro autoriza a una instancia de la aplicación cliente (que se ejecuta en ese dispositivo concreto) a recibir mensajes de FCM.
    Un ejemplo de token de registro es `fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS` (una cadena muy larga).

[La configuración de Firebase Cloud Messaging](#setup_fcm) (más adelante en esta guía) proporciona instrucciones detalladas para crear un proyecto y generar estas credenciales. Al crear un nuevo proyecto en la [consola de Firebase](https://console.firebase.google.com/), se crea un archivo de credenciales denominado **Google-Services. JSON.** &ndash; agregue este archivo al proyecto de Xamarin. Android, como se explica en [notificaciones remotas con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

En las secciones siguientes se explica cómo se usan estas credenciales cuando las aplicaciones cliente se comunican con los servidores de aplicaciones a través de FCM.

<a name="registration"></a>

### <a name="registration-with-fcm"></a>Registro con FCM

Una aplicación cliente debe registrarse primero con FCM antes de que pueda tener lugar la mensajería. La aplicación cliente debe completar los pasos de registro que se muestran en el diagrama siguiente:

[![Diagrama de pasos de registro de aplicaciones](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1. La aplicación cliente se pone en contacto con FCM para obtener un token de registro, pasando el identificador del remitente, la clave de API y el identificador de la aplicación a FCM.

2. FCM devuelve un token de registro a la aplicación cliente.

3. La aplicación cliente (opcionalmente) reenvía el token de registro al servidor de aplicaciones.

El servidor de aplicaciones almacena en caché el token de registro para las comunicaciones posteriores con la aplicación cliente. El servidor de aplicaciones puede devolver una confirmación a la aplicación cliente para indicar que se recibió el token de registro. Una vez que se realiza este protocolo de enlace, la aplicación cliente puede recibir mensajes de (o enviar mensajes a) el servidor de aplicaciones. La aplicación cliente puede recibir un nuevo token de registro si el token anterior se ve comprometido (consulte [notificaciones remotas con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md) para obtener un ejemplo de cómo una aplicación recibe actualizaciones de token de registro).

Cuando la aplicación cliente ya no quiere recibir mensajes del servidor de aplicaciones, puede enviar una solicitud al servidor de aplicaciones para eliminar el token de registro. Si se desinstala la aplicación cliente de un dispositivo, FCM lo detecta y notifica automáticamente al servidor de aplicaciones que elimine el token de registro.

### <a name="downstream-messaging"></a>Mensajería de nivel inferior

En el diagrama siguiente se ilustra el modo en que la mensajería en la nube Firebase almacena y reenvía mensajes de nivel inferior:

[![FCM usa el almacenamiento y el reenvío para la mensajería de nivel inferior](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

Cuando el servidor de aplicaciones envía un mensaje de nivel inferior a la aplicación cliente, usa los siguientes pasos tal y como se enumera en el diagrama anterior:

1. El servidor de aplicaciones envía el mensaje a FCM.

2. Si el dispositivo cliente no está disponible, el servidor FCM almacena el mensaje en una cola para su transmisión posterior. Los mensajes se retienen en el almacenamiento de FCM durante un máximo de 4 semanas (para obtener más información, consulte [configuración de la duración de un mensaje](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl)).

3. Cuando el dispositivo cliente está disponible, FCM reenvía el mensaje a la aplicación cliente en ese dispositivo.

4. La aplicación cliente recibe el mensaje de FCM, lo procesa y lo muestra al usuario. Por ejemplo, si el mensaje es una notificación remota, se presenta al usuario en el área de notificación.

En este escenario de mensajería (donde el servidor de aplicaciones envía un mensaje a una sola aplicación cliente), los mensajes pueden tener una longitud de hasta 4 KB.

Para obtener información detallada sobre cómo recibir mensajes FCM de bajada en Android, consulte [notificaciones remotas con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

### <a name="topic-messaging"></a>Mensajería de temas

La *mensajería de temas* permite que un servidor de aplicaciones envíe un mensaje a varios dispositivos que hayan participado en un tema determinado. También puede crear y enviar mensajes de tema a través de la GUI de notificaciones de la consola de Firebase. FCM controla el enrutamiento y la entrega de mensajes de tema a los clientes suscritos. Esta característica se puede usar para mensajes tales como alertas meteorológicas, cotizaciones bursátiles y noticias de titulares.

[![Diagrama de mensajes de tema](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

Los pasos siguientes se usan en la mensajería de temas (después de que la aplicación cliente obtenga un token de registro, como se explicó anteriormente):

1. La aplicación cliente se suscribe a un tema mediante el envío de un mensaje de suscripción a FCM.

2. El servidor de aplicaciones envía mensajes de tema a FCM para su distribución.

3. FCM reenvía los mensajes de tema a los clientes que se han suscrito a ese tema.

Para obtener más información sobre la mensajería de temas de Firebase, vea el [tema sobre mensajería en Android](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging)de Google.

<a name="setup_fcm"></a>

## <a name="setting-up-firebase-cloud-messaging"></a>Configuración de la mensajería en la nube Firebase

Antes de poder usar FCM Services en la aplicación, debe crear un nuevo proyecto (o importar un proyecto existente) a través de la [consola de Firebase](https://console.firebase.google.com/). Use los pasos siguientes para crear un proyecto de mensajería en la nube de Firebase para la aplicación:

1. Inicie sesión en la [consola de Firebase](https://console.firebase.google.com/) con su cuenta de Google (es decir, su dirección de Gmail) y haga clic en **crear nuevo proyecto**:

    [![Botón Crear nuevo proyecto](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    Si tiene un proyecto existente, haga clic en **importar un proyecto de Google**.

2. En el cuadro de diálogo **crear un proyecto** , escriba el nombre del proyecto y haga clic en **crear proyecto**. En el ejemplo siguiente, se crea un nuevo proyecto denominado **XamarinFCM** :

    [![Cuadro de diálogo crear un proyecto](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3. En la **Introducción**a la consola de Firebase, haga clic en **Agregar Firebase a la aplicación Android**:

    [![Agregar Firebase a una aplicación de Android](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4. En la siguiente pantalla, escriba el nombre del paquete de la aplicación. En este ejemplo, el nombre del paquete es **com. Xamarin. fcmexample**. Este valor debe coincidir con el nombre del paquete de la aplicación Android. También se puede escribir un alias de aplicación en el campo **sobrenombre** de la aplicación:

    [![Escribir el ejemplo de FCM como el alias de la aplicación](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5. Si su aplicación usa vínculos dinámicos, invitados o Google auth, también debe especificar el certificado de firma de depuración. Para obtener más información sobre cómo buscar el certificado de firma, consulte [búsqueda de la firma MD5 o SHA1 de su almacén de claves](~/android/deploy-test/signing/keystore-signature.md).
    En este ejemplo, el certificado de firma se deja en blanco.

6. Haga clic en **Agregar aplicación**:

    [![Hacer clic en el botón Agregar aplicación](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    Se genera automáticamente una clave de API de servidor y un identificador de cliente para la aplicación. Esta información se empaqueta en un archivo **Google-Services. JSON** que se descarga automáticamente al hacer clic en **Agregar aplicación**.
    Asegúrese de guardar este archivo en un lugar seguro.

Para obtener un ejemplo detallado de cómo agregar **Google-Services. JSON** a un proyecto de aplicación para recibir mensajes de notificación de envío de FCM en Android, consulte [notificaciones remotas con FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

## <a name="for-further-reading"></a>Lecturas adicionales

- La [mensajería en la nube Firebase](https://firebase.google.com/docs/cloud-messaging/) de Google proporciona una visión general de las funcionalidades clave de la mensajería en la nube de Firebase, una explicación de cómo funciona y las instrucciones de configuración.

- En [las solicitudes de envío del servidor de aplicaciones](https://firebase.google.com/docs/cloud-messaging/send-message) de Google, se explica cómo enviar mensajes con el servidor de aplicaciones.

- [Rfc 6120](https://tools.ietf.org/html/rfc6120) y [RFC 6121](https://tools.ietf.org/html/rfc6121) explican y definen el protocolo de mensajería y presencia extensible (XMPP).

- [Acerca](https://firebase.google.com/docs/cloud-messaging/concept-options) de los mensajes de FCM describe los distintos tipos de mensajes que se pueden enviar con la mensajería en la nube de Firebase.

## <a name="summary"></a>Resumen

En este artículo se proporciona información general sobre Firebase Cloud Messaging (FCM). Se han explicado las distintas credenciales que se usan para identificar y autorizar la mensajería entre los servidores de aplicaciones y las aplicaciones cliente. Se ilustran los escenarios de registro y mensajería de nivel inferior, y se detallan los pasos para registrar la aplicación con FCM para usar los servicios de FCM.

## <a name="related-links"></a>Vínculos relacionados

- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/)
