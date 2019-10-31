---
title: Configurar el dispositivo para el desarrollo
description: En este artículo se explica cómo configurar un dispositivo Android y conectarlo a un equipo de modo que el dispositivo pueda usarse para ejecutar y depurar aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: f5b92bc202cbf9765a63219ad653442152c3eb24
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020715"
---
# <a name="set-up-device-for-development"></a>Configurar el dispositivo para el desarrollo

_En este artículo se explica cómo configurar un dispositivo Android y conectarlo a un equipo de modo que el dispositivo pueda usarse para ejecutar y depurar aplicaciones de Xamarin.Android._

Después de realizar las pruebas en un emulador de Android, querrá ver y probar la ejecución de sus aplicaciones en un dispositivo Android. Estos son los pasos que debe llevar a cabo para conectar un dispositivo a un equipo para la depuración:

1. **Habilitar la depuración en el dispositivo**: de forma predeterminada, no es posible depurar aplicaciones en un dispositivo Android.

2. **Instalar controladores USB**: este paso no es necesario para los equipos macOS. Los equipos de Windows podrían requerir la instalación de controladores USB.

3. **Conectar el dispositivo al equipo**: el último paso consiste en conectar el dispositivo al equipo mediante USB o Wi-Fi.

Cada uno de estos pasos se tratará con más detalle en las secciones siguientes.

## <a name="enable-debugging-on-the-device"></a>Habilitar la depuración en el dispositivo

Puede usar cualquier dispositivo Android para probar una aplicación de Android, pero el dispositivo debe estar configurado correctamente antes de realizar la depuración. Los pasos que se deben llevar a cabo son ligeramente diferentes en función de la versión de Android que se ejecute en el dispositivo.

### <a name="android-40-to-android-41"></a>De Android 4.0 a Android 4.1

En las versiones de Android de 4.0.x a 4.1.x, debe seguir estos pasos para habilitar la depuración:

1. Vaya a la pantalla **Configuración**.
2. Seleccione **Opciones del desarrollador**.
3. Desactive la opción **Depuración de USB**.

En esta captura de pantalla se muestra la pantalla **Opciones del desarrollador** en un dispositivo que ejecuta Android 4.0.3:

[![Opciones del desarrollador](set-up-device-for-development-images/developer-options-sml.png)](set-up-device-for-development-images/developer-options.png#lightbox)

### <a name="android-42-and-higher"></a>Android 4.2 y versiones posteriores

A partir de Android 4.2 y versiones posteriores, las **Opciones del desarrollador** están ocultas de forma predeterminada. Para que estén disponibles, vaya a **Ajustes > Acerca del dispositivo** y pulse el elemento **Número de compilación** siete veces para que se muestre la pestaña **Opciones de desarrollador**:

[![Elemento de número de compilación](set-up-device-for-development-images/about-phone-sml.png)](set-up-device-for-development-images/about-phone.png#lightbox)

Una vez que la pestaña **Opciones del desarrollador** está disponible en **Ajustes > Sistema**, ábrala para mostrar las opciones del desarrollador:

[![Pantalla de configuración del desarrollador](set-up-device-for-development-images/developer3.png)](set-up-device-for-development-images/developer3.png#lightbox)

Desde aquí puede habilitar las opciones del desarrollador, como la depuración de USB y el modo Permanecer activo.

## <a name="install-usb-drivers"></a>Instalar controladores USB

Este paso no es necesario para macOS. Basta con que conecte el dispositivo al equipo Mac con un cable USB.

Puede que sea necesario instalar algunos controladores adicionales para que un equipo Windows reconozca un dispositivo Android conectado mediante USB.

> [!NOTE]
> Estos son los pasos necesarios para configurar un dispositivo Google Nexus y se proporcionan a modo de referencia. Los pasos correspondientes a su dispositivo específico pueden variar, pero seguirán un patrón similar. Si tiene problemas, busque su dispositivo en Internet.

Ejecute la aplicación **android.bat** en el directorio **[ruta de instalación de Android SDK]\tools**. De forma predeterminada, el instalador de Xamarin.Android colocará el SDK de Android en la siguiente ubicación de un equipo de Windows:

`C:\Users\[username]\AppData\Local\Android\android-sdk`

### <a name="download-the-usb-drivers"></a>Descargar los controladores USB

Los dispositivos Google Nexus (a excepción de Galaxy Nexus) requieren el controlador USB de Google. El controlador para Galaxy Nexus [está distribuido por Samsung](https://www.samsung.com/us/support/downloads/).
Todos los demás dispositivos Android deben usar el [controlador USB de su fabricante correspondiente](https://developer.android.com/tools/extras/oem-usb.html#Drivers).

Para instalar el paquete del **controlador USB de Google**, inicie Android SDK Manager y expanda la carpeta **Extras**, como se ve en la captura de pantalla siguiente:

[![Paquete de controladores USB de Google seleccionado](set-up-device-for-development-images/usbdriverpackage.png)](set-up-device-for-development-images/usbdriverpackage.png#lightbox)

Active el cuadro del **controlador USB de Google** y haga clic en el botón **Instalar**.
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

### <a name="installing-unverified-drivers-in-windows-8"></a>Instalar controladores no comprobados en Windows 8

Puede que sea necesario llevar a cabo pasos adicionales para instalar un controlador no comprobado en Windows 8. En los pasos siguientes se describe cómo instalar los controladores para un Galaxy Nexus:

1. **Obtener acceso a las opciones de arranque avanzadas de Windows 8**: este paso consiste en reiniciar el equipo para tener acceso a las opciones de arranque avanzadas. Inicie un símbolo de la línea de comandos y reinicie el equipo mediante el comando siguiente:

    ```command
    shutdown.exe /r /o
    ```

2. **Conectar el dispositivo**: conecte el dispositivo al equipo.

3. **Iniciar el administrador de dispositivos**: ejecute **devmgmt.msc**; debería ver el dispositivo en la lista con un triángulo amarillo sobre él.

4. **Instalar los controladores del dispositivo**: instale los controladores del dispositivo como se describió anteriormente.

## <a name="connect-the-device-to-the-computer"></a>Conectar el dispositivo al equipo

El paso final consiste en conectar el dispositivo al equipo. Hay dos maneras de hacerlo:

- **Cable USB**: esta es la manera más fácil y más común. Basta con que conecte el cable USB al dispositivo y, después, al equipo.

- **Wi-Fi**: es posible conectar un dispositivo Android a un equipo a través de Wi-Fi, sin usar un cable USB. Esta técnica requiere algo más de esfuerzo, pero debería ser útil si el dispositivo está demasiado alejado del equipo como para estar conectado constantemente mediante cable. En la sección siguiente se explica cómo realizar la conexión a través de Wi-Fi.

### <a name="connecting-over-wifi"></a>Conectar a través de Wi-Fi

De forma predeterminada, [Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) está configurado para comunicarse con un dispositivo Android a través de USB. Es posible volver a configurarlo para que use TCP/IP en lugar de USB. Para ello, tanto el dispositivo como el equipo deben estar en la misma red Wi-Fi. Para configurar el entorno para depurar mediante Wi-Fi, siga estos pasos desde la línea de comandos:

1. Determine la dirección IP del dispositivo Android. Una manera de averiguar la dirección IP es mirar en **Ajustes > Wi-Fi** y, después, pulsar la red Wi-Fi a la que está conectado el dispositivo. Se abrirá una pantalla de configuración con información sobre la conexión de red, parecida a la que se muestra en la siguiente captura de pantalla:

    ![Dirección IP](set-up-device-for-development-images/ip-settings.png)

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

## <a name="summary"></a>Resumen

En este artículo se ha descrito cómo configurar un dispositivo Android para el desarrollo mediante la habilitación de la depuración en el dispositivo. Además, también se ha explicado cómo conectar el dispositivo a un equipo a través de USB o Wi-Fi.

## <a name="related-links"></a>Vínculos relacionados

- [Android Debug Bridge](https://developer.android.com/tools/help/adb.html)
- [Uso de dispositivos de hardware](https://developer.android.com/tools/device.html)
- [Descargas de controladores de Samsung](https://www.samsung.com/us/support/downloads/)
- [Controladores USB de OEM](https://developer.android.com/tools/extras/oem-usb.html#Drivers)
- [Controlador USB de Google](https://developer.android.com/sdk/win-usb.html)
- [Desarrolladores de XDA: problema del controlador ADB/fastboot resuelto en Windows 8](https://forum.xda-developers.com/showthread.php?t=1583801)
