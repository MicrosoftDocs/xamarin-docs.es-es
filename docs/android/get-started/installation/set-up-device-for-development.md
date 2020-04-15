---
title: Configurar el dispositivo para el desarrollo
description: En este artículo se explica cómo configurar un dispositivo Android y conectarlo a un equipo de modo que el dispositivo pueda usarse para ejecutar y depurar aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 72e0a2adc79796b3df7b6fb4eca62448f1a1a7a4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "79510736"
---
# <a name="set-up-device-for-development"></a>Configurar el dispositivo para el desarrollo

_En este artículo se explica cómo configurar un dispositivo Android y conectarlo a un equipo de modo que el dispositivo pueda usarse para ejecutar y depurar aplicaciones de Xamarin.Android._

Después de realizar las pruebas en un emulador de Android, querrá ver y probar la ejecución de sus aplicaciones en un dispositivo Android. Tendrá que habilitar la depuración y conectar el dispositivo al equipo.

Cada uno de estos pasos se tratará con más detalle en las secciones siguientes.

## <a name="enable-debugging-on-the-device"></a>Habilitar la depuración en el dispositivo

Un dispositivo debe tener la depuración habilitada para probar una aplicación de Android. De manera predeterminada, se han ocultado las opciones para desarrolladores en Android a partir de la versión 4.2, y su habilitación puede variar en función de la versión de Android.

### <a name="android-90"></a>Android 9.0+

En las versiones de Android 9.0 y superiores, debe seguir estos pasos para habilitar la depuración:

1. Vaya a la pantalla **Configuración**.
2. Seleccione **Sobre el teléfono**.
3. Pulse **Número de compilación** 7 veces hasta que vea **¡Ahora eres un desarrollador!** .

### <a name="android-80-and-android-81"></a>Android 8.0 y Android 8.1

1. Vaya a la pantalla **Configuración**.
2. Seleccione **Sistema**.
3. Seleccione **Sobre el teléfono**.
4. Pulse **Número de compilación** 7 veces hasta que vea **¡Ahora eres un desarrollador!** .

### <a name="android-71-and-lower"></a>Android 7.1 y versiones inferiores

1. Vaya a la pantalla **Configuración**.
2. Seleccione **Sobre el teléfono**.
3. Pulse **Número de compilación** 7 veces hasta que vea **¡Ahora eres un desarrollador!** .

[![Pantalla de opciones del desarrollador en Android 9.0](set-up-device-for-development-images/build-version-sml.png)](set-up-device-for-development-images/build-version.png#lightbox)

### <a name="verify-that-usb-debugging-is-enabled"></a>Comprobación de que está habilitada la depuración USB

Después de habilitar el modo de desarrollador en el dispositivo, debe asegurarse de que está habilitada la depuración USB en el dispositivo. Esto también varía en función de la versión de Android.

### <a name="android-90"></a>Android 9.0+

Vaya a **Configuración > Sistema > Opciones avanzadas > Opciones del desarrollador** y habilite **Depuración USB**.

### <a name="android-80-and-android-81"></a>Android 8.0 y Android 8.1

Vaya a **Configuración > Sistema > Opciones del desarrollador** y habilite **Depuración USB**.

### <a name="android-71-and-lower"></a>Android 7.1 y versiones inferiores

Vaya a **Configuración > Opciones del desarrollador** y habilite **Depuración USB**.

Una vez que la pestaña **Opciones del desarrollador** está disponible en **Ajustes > Sistema**, ábrala para mostrar las opciones del desarrollador:

[![Pantalla de opciones del desarrollador en Android 9.0](set-up-device-for-development-images/usb-debugging-sml.png)](set-up-device-for-development-images/usb-debugging.png#lightbox)

Desde aquí puede habilitar las opciones del desarrollador, como la depuración de USB y el modo Permanecer activo.

## <a name="connect-the-device-to-the-computer"></a>Conexión del dispositivo al equipo

El paso final consiste en conectar el dispositivo al equipo. La manera más sencilla y confiable es hacerlo a través de USB.

Recibirá un mensaje para confiar en el equipo en el dispositivo si no lo ha utilizado antes para depuración. También puede activar **Permitir siempre desde este ordenador** para evitar que le salga este aviso cada vez que conecte el dispositivo.

![](set-up-device-for-development-images/trust-computer-for-usb-debugging.png "Google USB")

## <a name="alternate-connection-via-wifi"></a>Conexión alternativa a través de Wi-Fi

Es posible conectar un dispositivo Android a un equipo a través de Wi-Fi, sin usar un cable USB. Esta técnica requiere más esfuerzo, pero debería ser útil si el dispositivo está demasiado alejado del equipo como para estar conectado constantemente mediante cable. 

### <a name="connecting-over-wifi"></a>Conectar a través de Wi-Fi

De forma predeterminada, [Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) está configurado para comunicarse con un dispositivo Android a través de USB. Es posible volver a configurarlo para que use TCP/IP en lugar de USB. Para ello, tanto el dispositivo como el equipo deben estar en la misma red Wi-Fi. Para configurar el entorno para depurar mediante Wi-Fi, realice los siguientes pasos desde la línea de comandos:

1. Determine la dirección IP del dispositivo Android. Una manera de averiguar la dirección IP es mirar en **Ajustes > Redes e Internet > Wi-Fi**, después, pulsar la red Wi-Fi a la que está conectado el dispositivo y luego pulsar en **Opciones avanzadas**. Se abrirá una lista desplegable con información sobre la conexión de red, parecida a la que se muestra en la siguiente captura de pantalla:

    [![Dirección IP](set-up-device-for-development-images/ip-settings-sml.png)](set-up-device-for-development-images/ip-settings.png#lightbox)

    En algunas versiones de Android, la dirección IP no aparecerá ahí, pero se puede encontrar en **Ajustes > Acerca del teléfono > Estado**.

2. Conecte el dispositivo Android al equipo a través de USB.

3. Después, reinicie ADB de modo que use TCP en el puerto 5555. En un símbolo del sistema, escriba el siguiente comando:

    ```command
    adb tcpip 5555
    ```

    Después de emitir este comando, el equipo no podrá escuchar los dispositivos que están conectados a través de USB.

4. Desconecte el cable USB que conecta el dispositivo al equipo.

5. Configure ADB para que se conecte al dispositivo Android en el puerto que se especificó en el paso 1 anterior:

    ```command
    adb connect 192.168.1.28:5555
    ```

    Una vez que se finalice este comando, el dispositivo Android se conecta al equipo a través de Wi-Fi.

    Cuando termine la depuración a través de Wi-Fi, puede restablecer ADB al modo USB con el comando siguiente:
    
    ```command
    adb usb
    ```
    
    Es posible solicitarle a ADB que enumere los dispositivos que están conectados al equipo. Independientemente de cómo estén conectados los dispositivos, puede emitir el comando siguiente en el símbolo del sistema para ver qué está conectado:
    
    ```command
    adb devices
    ```

## <a name="troubleshooting"></a>Solución de problemas

En algunos casos, es posible que el dispositivo no se pueda conectar al equipo. Si es así, quizá le interesaría comprobar que están instalados los controladores USB.

## <a name="install-usb-drivers"></a>Instalar controladores USB

Este paso no es necesario para macOS; basta con que conecte el dispositivo al equipo Mac con un cable USB.

Puede que sea necesario instalar algunos controladores adicionales para que un equipo Windows reconozca un dispositivo Android conectado mediante USB.

> [!NOTE]
> Estos son los pasos necesarios para configurar un dispositivo Google Nexus y se proporcionan a modo de referencia. Los pasos correspondientes a su dispositivo específico pueden variar, pero seguirán un patrón similar. Si tiene problemas, busque su dispositivo en Internet.

Ejecute la aplicación **android.bat** en el directorio **[ruta de instalación de Android SDK]\tools**. De forma predeterminada, el instalador de Xamarin.Android colocará el SDK de Android en la siguiente ubicación de un equipo de Windows:

`C:\Users\[username]\AppData\Local\Android\android-sdk`

### <a name="download-the-usb-drivers"></a>Descargar los controladores USB

Los dispositivos Google Nexus (a excepción de Galaxy Nexus) requieren el controlador USB de Google. El controlador para Galaxy Nexus [está distribuido por Samsung](https://www.samsung.com/us/support/downloads/).
Todos los demás dispositivos Android deben usar el [controlador USB de su fabricante correspondiente](https://developer.android.com/tools/extras/oem-usb.html#Drivers).

Para instalar el paquete del **controlador USB de Google**, inicie Android SDK Manager y expanda la carpeta **Extras**, como se ve en la captura de pantalla siguiente:

![](set-up-device-for-development-images/google-usb-driver.png "Google USB driver selected")

Active el cuadro del **controlador USB de Google** y haga clic en el botón **Aplicar cambios**.
Los archivos del controlador se descargan en la siguiente ubicación:

`[Android SDK install path]\extras\google\usb\_driver`

La ruta de acceso predeterminada para efectuar una instalación de Xamarin.Android es la siguiente:

`C:\Users\[username]\AppData\Local\Android\android-sdk\extras\google\usb_driver`

### <a name="installing-the-usb-driver"></a>Instalar el controlador USB

Una vez que haya descargado los controladores USB, deberá instalarlos.
Para instalar los controladores en Windows 7:

1. Conecte el dispositivo al equipo con un cable USB.

2. Haga clic con el botón derecho en el equipo desde el escritorio o el Explorador de Windows y seleccione **Administrar**.

3. Seleccione **Dispositivos** en el panel izquierdo.

4. Busque y expanda **Otros dispositivos** en el panel derecho.

5. Haga clic con el botón derecho en el nombre del dispositivo y seleccione **Actualizar software de controlador**.
    Se iniciará el Asistente para actualización de hardware.

6. Seleccione **Buscar software de controlador en el equipo** y haga clic en **Siguiente**.

7. Haga clic en **Examinar** y busque la carpeta del controlador USB (el controlador USB de Google se encuentra en **[ruta de instalación de Android SDK]\extras\google\usb_driver**).

8. Haga clic en **Siguiente** para instalar el controlador.

## <a name="summary"></a>Resumen

En este artículo se ha descrito cómo configurar un dispositivo Android para el desarrollo mediante la habilitación de la depuración en el dispositivo. Además, también se ha explicado cómo conectar el dispositivo a un equipo a través de USB o Wi-Fi.

## <a name="related-links"></a>Vínculos relacionados

- [Android Debug Bridge](https://developer.android.com/tools/help/adb.html)
- [Uso de dispositivos de hardware](https://developer.android.com/tools/device.html)
- [Descargas de controladores de Samsung](https://www.samsung.com/us/support/downloads/)
- [Controladores USB de OEM](https://developer.android.com/tools/extras/oem-usb.html#Drivers)
- [Controlador USB de Google](https://developer.android.com/sdk/win-usb.html)
- [Desarrolladores de XDA: problema del controlador ADB/fastboot resuelto en Windows 8](https://forum.xda-developers.com/showthread.php?t=1583801)
