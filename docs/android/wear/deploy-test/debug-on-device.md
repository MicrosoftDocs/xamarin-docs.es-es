---
title: Depuración en un dispositivo Wear
description: En este artículo se explica cómo depurar una aplicación de desgaste de Xamarin. Android en un dispositivo de desgaste.
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 965ed4e802c05f8450192c0fec17fe31e464c779
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305970"
---
# <a name="debug-on-a-wear-device"></a>Depuración en un dispositivo Wear

_En este artículo se explica cómo depurar una aplicación de desgaste de Xamarin. Android en un dispositivo de desgaste._

## <a name="overview"></a>Información general

Si tiene un dispositivo de desgaste de Android, como un desgaste de SmartWatch de Android, puede ejecutar la aplicación en el dispositivo en lugar de usar un emulador. (Si aún no está familiarizado con el proceso de implementación y ejecución de aplicaciones de uso de Android, consulte [Hola, desgaste](~/android/wear/get-started/hello-wear.md)).

## <a name="prepare-the-wear-device"></a>Preparar el dispositivo de desgaste:

Siga estos pasos para habilitar la depuración en el dispositivo de desgaste de Android:

1. Abra el menú de **configuración** en el dispositivo de desgaste de Android.

2. Desplácese a la parte inferior del menú y puntee en **acerca**de.

3. Puntee en el número de compilación 7 veces.

4. En el menú **configuración** , pulse **Opciones de desarrollador**.

5. Confirme que está habilitada la **depuración de ADB** .

## <a name="debugging-over-usb"></a>Depuración a través de USB

Si el dispositivo de desgaste tiene un puerto USB, puede conectarlo al equipo, implementarlo y ejecutar o depurar la aplicación como lo haría con un teléfono Android (para más información, consulte [depurar en un dispositivo](~/android/deploy-test/debugging/debug-on-device.md)).

## <a name="debugging-over-bluetooth"></a>Depuración a través de Bluetooth

Si el dispositivo de desgaste no tiene un puerto USB, puede implementar la aplicación en el dispositivo de desgaste a través de Bluetooth mediante el enrutamiento de la salida de depuración de la aplicación a un teléfono Android que esté conectado al equipo. 

### <a name="prepare-your-phone"></a>Preparación del teléfono

Siga estos pasos para preparar el teléfono para realizar conexiones Bluetooth al dispositivo de desgaste: 

1. Si todavía no lo ha hecho, configure el teléfono para el desarrollo de Xamarin. Android, como se explica en [configuración del dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md).

2. Descargue e instale la aplicación gratuita de [desgaste de Android](https://play.google.com/store/apps/details?id=com.google.android.wearable.app) desde el Google Play Store.

### <a name="connect-the-device"></a>Conectar el dispositivo

Siga estos pasos para conectar el dispositivo de desgaste a su teléfono:

1. En el teléfono que actuará como intermediario de Bluetooth (configurado anteriormente), inicie la aplicación de desgaste de Android. 

2. Pulse el icono de **configuración** .

3. Habilite la **depuración a través de Bluetooth**. Debería ver el siguiente estado en la pantalla de la aplicación de desgaste de Android:

    ```
    Host: disconnected
    Target: connected
    ```

4. Conecte el teléfono al equipo a través de USB. En el equipo, escriba los siguientes comandos:

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    Si el puerto 4444 no está disponible, puede usar cualquier otro puerto disponible al que tenga acceso. 

    > [!NOTE]
    > Si reinicia Visual Studio o Visual Studio para Mac, debe volver a ejecutar estos comandos para configurar una conexión con el dispositivo de desgaste.

5. Cuando el dispositivo de desgaste le solicite, confirme que permite la **depuración de ADB**. En la aplicación de desgaste de Android, debería ver el cambio de estado a:

    ```
    Host: connected
    Target: connected
    ```

6. Después de completar los pasos anteriores, al ejecutar `adb devices` se muestra el estado del teléfono y del dispositivo de desgaste de Android:

    ```
    List of devices attached
    127.0.0.1:4444    device
    019ad61df0a69399  device
    ```

Llegados a este punto, puede implementar la aplicación en el dispositivo de desgaste.

<a name="screenshots" />

### <a name="taking-screenshots"></a>Realizar capturas de pantallas

Puede realizar una captura de pantalla del dispositivo de desgaste escribiendo el siguiente comando: 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

Copie la captura de pantalla en el equipo escribiendo el siguiente comando:

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

Escriba el siguiente comando para eliminar la captura de pantalla del dispositivo:

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```

### <a name="uninstalling-an-app"></a>Desinstalación de una aplicación

Puede desinstalar una aplicación del dispositivo de desgaste escribiendo el siguiente comando:

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

Por ejemplo, para quitar la aplicación con el nombre del paquete `com.xamarin.weartest`, escriba el siguiente comando:

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

Para obtener más información sobre la depuración de dispositivos de desgaste de Android a través de Bluetooth, consulte [depuración a través de Bluetooth](https://developer.android.com/training/wearables/apps/bt-debugging.html).

## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>Depuración de una aplicación de desgaste con una aplicación de teléfono complementaria

Las aplicaciones de desgaste de Android se empaquetan con una aplicación de teléfono Android complementaria para su distribución en Google Play (para obtener más información, consulte [trabajar con empaquetado](~/android/wear/deploy-test/packaging.md)). Sin embargo, todavía desarrolla la aplicación de desgaste y su aplicación complementaria por separado. Al publicar la aplicación a través del Google Play Store, la aplicación de desgaste se empaquetará con la aplicación complementaria y se instalará automáticamente si es posible.

Para depurar la aplicación de desgaste con una aplicación complementaria: 

1. Cree e implemente la aplicación complementaria en el teléfono.

2. Haga clic con el botón derecho en el proyecto de desgaste y establézcalo como proyecto de inicio predeterminado.

3. Implemente el proyecto de desgaste en el dispositivo portátil.

4. Ejecute y depure la aplicación de desgaste en el dispositivo.

## <a name="summary"></a>Resumen

En este artículo se explica cómo configurar un dispositivo de desgaste de Android para el desgaste de la depuración desde Visual Studio a través de Bluetooth y cómo depurar una aplicación de desgaste con una aplicación de teléfono complementaria. También proporciona sugerencias de depuración comunes para depurar una aplicación de desgaste a través de Bluetooth.
