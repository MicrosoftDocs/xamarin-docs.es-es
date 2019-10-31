---
title: Publicación de forma independiente
ms.prod: xamarin
ms.assetid: 6FB4DEF2-01AD-C5FE-0950-CE1BF088A9C6
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: dee49d556173ec0b9544820f129eef8a9cce404f
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73021318"
---
# <a name="publishing-independently"></a>Publicación de forma independiente

Es posible publicar una aplicación sin usar ninguno de los mercados de Android existentes. En esta sección se explican estos métodos de publicación alternativos y los niveles de licencia de Xamarin.Android.

## <a name="xamarin-licensing"></a>Licencias de Xamarin

Existen varias licencias para el desarrollo, la implementación y la distribución de aplicaciones de Xamarin.Android:

- **Visual Studio Community**: para estudiantes, equipos pequeños y desarrolladores de OSS que usan Windows.

- **Visual Studio Professional** &ndash; Para desarrolladores individuales o equipos pequeños (solo Windows). Esta licencia ofrece una suscripción estándar o en la nube y no tiene restricciones de uso.

- **Visual Studio Enterprise** &ndash; Para equipos de cualquier tamaño (solo Windows). Esta licencia incluye funcionalidades empresariales, una suscripción estándar o en la nube.

Visite la [visualstudio.com](https://visualstudio.microsoft.com/xamarin/) para descargar las ediciones Community o para obtener más información sobre la compra de las ediciones Professional y Enterprise.

## <a name="allow-installation-from-unknown-sources"></a>Permitir la instalación desde orígenes desconocidos

De forma predeterminada, Android impide que los usuarios descarguen e instalen aplicaciones desde ubicaciones distintas de Google Play. Para permitir la instalación desde otros orígenes, el usuario debe habilitar la opción *Fuentes desconocidas* en el dispositivo antes de intentar instalar una aplicación. Esta opción se encuentra en **Ajustes > Seguridad**, como se muestra en la imagen siguiente:

[![Pantalla de configuración de seguridad](publishing-independently-images/settings.png)](publishing-independently-images/settings.png#lightbox)

> [!IMPORTANT]
> Algunos proveedores de red podrían impedir la instalación de aplicaciones desde orígenes desconocidos, independientemente de esta configuración.

## <a name="publishing-by-e-mail"></a>Publicar por correo electrónico

Una forma rápida y fácil de distribuir una aplicación a los usuarios consiste en adjuntar el APK de lanzamiento a un correo electrónico. Cuando el usuario abra el correo electrónico en un dispositivo Android, Android reconocerá el APK adjunto y mostrará el botón **Instalar**, como se muestra en la siguiente imagen:

[![Botón Instalar para datos adjuntos](publishing-independently-images/publishing-via-email.png)](publishing-independently-images/publishing-via-email.png#lightbox)

Aunque la distribución por correo electrónico es sencilla, incluye algunas protecciones contra la piratería o la distribución no autorizada. Es mejor que la reserve para los casos en que los destinatarios de la aplicación son pocos y confía en que no la distribuyan.

## <a name="publishing-by-web"></a>Publicar por web

Es posible distribuir una aplicación a través de un servidor web. Para ello, cargue la aplicación en el servidor web y, después, proporcione a los usuarios un vínculo de descarga. Cuando un dispositivo Android examina un vínculo y, después, descarga la aplicación, esta se instalará automáticamente una vez que se complete la descarga.

## <a name="manually-installing-an-apk"></a>Instalar un APK de forma manual

La instalación manual es la tercera opción para instalar aplicaciones. Para llevar a cabo la instalación manual de una aplicación:

1. **Distribuya una copia del APK al usuario** &ndash; Por ejemplo, esta copia se puede distribuir en un CD o en una unidad flash USB.
1. **El usuario instala la aplicación en un dispositivo Android** &ndash; Use la herramienta de línea de comandos *Android Debug Bridge* (**adb**). **adb** es una herramienta de línea de comandos versátil que permite la comunicación con una instancia del emulador o con un dispositivo Android. **adb** está incluido en Android SDK; lo encontrará en el directorio **\<sdk>/platform-tools/** .

El dispositivo Android debe estar conectado con un cable USB al equipo.
Los equipos Windows también pueden requerir controladores USB adicionales del proveedor del teléfono para que **adb** los pueda reconocer. Las instrucciones de instalación de estos controladores USB adicionales no se tratan en este documento.

Antes de emitir un comando **adb**, es útil saber si hay dispositivos o instancias del emulador conectados y, en ese caso, cuáles son. Puede ver una lista de todo lo que está conectado mediante el comando `devices`, como se muestra en el siguiente fragmento de código:

```shell
$ adb devices
List of devices attached
        0149B2EC03012005device
```

Una vez que se hayan confirmado los dispositivos conectados, puede instalar la aplicación mediante la emisión del comando `install` con **adb**:

```shell
$ adb install <path-to-apk>
```

El fragmento de código siguiente es un ejemplo de cómo instalar una aplicación en un dispositivo conectado:

```shell
$ adb install helloworld.apk
3772 KB/s (3013594 bytes in 0.780s)
        pkg: /data/local/tmp/helloworld.apk
Success
```

Si la aplicación ya está instalada, `adb install` no podrá instalar el APK y notificará un error, como se muestra en el ejemplo siguiente:

```shell
$ adb install helloworld.apk
4037 KB/s (3013594 bytes in 0.728s)
        pkg: /data/local/tmp/helloworld.apk
Failure [INSTALL_FAILED_ALREADY_EXISTS]
```

Será necesario desinstalar la aplicación del dispositivo. Primero, ejecute el comando `adb uninstall`:

```shell
adb uninstall <package_name>
```

El fragmento siguiente es un ejemplo de cómo desinstalar una aplicación:

```shell
$ adb uninstall mono.samples.helloworld
Success
```
