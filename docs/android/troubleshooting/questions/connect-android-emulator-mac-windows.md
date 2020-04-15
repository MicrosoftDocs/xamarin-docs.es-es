---
title: ¿Es posible conectarse a emuladores de Android que se ejecutan en un Mac desde una máquina virtual de Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 49d1eea60f766f4cb61484a6e441506cf8f046ff
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725083"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>¿Es posible conectarse a emuladores de Android que se ejecutan en un Mac desde una máquina virtual de Windows?

Para conectarse a Android Emulator que se ejecuta en un equipo Mac desde una máquina virtual Windows, siga estos pasos:

1. Inicie el emulador en el equipo Mac.

2. Elimine el servidor `adb` en el equipo Mac:

    ```bash
    adb kill-server
    ```

3. Tenga en cuenta que el emulador realiza la escucha en dos puertos TCP en la interfaz de red de bucle invertido:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    El puerto con un número impar es el que se usa para conectarse a `adb`. Consulte también [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4. _Opción 1_: Use `nc` para reenviar los paquetes TCP entrantes recibidos externamente en el puerto 5555 (o en cualquier otro puerto que prefiera) al puerto con un número impar en la interfaz de bucle invertido (**127.0.0.1 5555** en este ejemplo) y para reenviar los paquetes salientes en la otra dirección:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Siempre y cuando los comandos `nc` sigan ejecutándose en una ventana de terminal, los paquetes se reenviarán según lo esperado. Puede escribir Control-C en la ventana de terminal para salir de los comandos `nc` una vez que haya terminado de usar el emulador.

    (La opción 1 suele ser más fácil que la opción 2, especialmente si **Preferencias del sistema > Seguridad y privacidad > Firewall** está activado).

    _Opción 2_: Use `pfctl` para redirigir los paquetes TCP desde el puerto `5555` (o cualquier otro puerto que prefiera) de la interfaz de [redes compartidas](https://kb.parallels.com/en/4948) hasta el puerto con un número impar de la interfaz de bucle invertido (`127.0.0.1:5555` en este ejemplo):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Este comando configura el reenvío de puertos mediante el servicio del sistema `pf packet filter`. Los saltos de línea son importantes. Asegúrese de mantenerlos intactos al copiarlos y pegarlos. También tendrá que ajustar el nombre de la interfaz *vmnet8* si usa Parallels. `vmnet8` es el nombre del *dispositivo NAT* especial para el modo de *redes compartidas* en VMWare Fusion. Es probable que la interfaz de red adecuada en Parallels sea [vnic0](https://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5. Conéctese al emulador desde la máquina Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Reemplace "ip-address-of-the-mac" por la dirección IP del equipo Mac, por ejemplo, como se muestra en `ifconfig vmnet8 | grep 'inet '`. Si es necesario, reemplace `5555` por el otro puerto que prefiera del paso 4.\. (Nota: Una manera de obtener acceso a `adb` mediante la línea de comandos es a través de [**Herramientas > Símbolo del sistema de Adb de Android**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) en Visual Studio).

### <a name="alternate-technique-using-ssh"></a>Técnica alternativa con `ssh`

Si ha habilitado _Sesión remota_ en el equipo Mac, puede usar el reenvío de puertos `ssh` para conectarse al emulador.

1. Instale un cliente SSH en Windows. Una opción es instalar [Git para Windows](https://git-for-windows.github.io/). El comando `ssh` estará entonces disponible en el símbolo del sistema de **Git Bash**.

2. Siga los pasos del 1 al 3 anteriores para iniciar el emulador, eliminar el servidor `adb` en el equipo Mac e identificar los puertos del emulador.

3. Ejecute `ssh` en Windows para configurar el reenvío de puertos bidireccional entre un puerto local de Windows (`localhost:15555` en este ejemplo) y el puerto del emulador con un número impar de la interfaz de bucle invertido de Mac (`127.0.0.1:5555` en este ejemplo):

    ```cmd
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Reemplace `mac-username` por el nombre de usuario de Mac como se indica en `whoami`. Reemplace `ip-address-of-the-mac` por la dirección IP del equipo Mac.

4. Conéctese al emulador mediante el puerto local en Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Nota: Una manera sencilla de obtener acceso a `adb` mediante la línea de comandos es a través de [Herramientas > Símbolo del sistema de Adb de Android**en**Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)).

Una pequeña precaución: si usa el puerto `5555` como puerto local, `adb` considerará que el emulador se ejecuta localmente en Windows. Esto no causa ningún problema en Visual Studio, pero sí en Visual Studio para Mac, donde hace que la aplicación se cierre inmediatamente después del inicio.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>Todavía no se admite el uso de `adb -H` como técnica alternativa.

En teoría, otro enfoque sería usar la funcionalidad integrada de `adb` para conectarse a un servidor `adb` que se ejecuta en una máquina remota (vea por ejemplo [https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325)).
Sin embargo, las extensiones IDE de Xamarin.Android no proporcionan actualmente una manera de configurar esta opción.

## <a name="contact-information"></a>Información de contacto

En este documento se describe el comportamiento actual a partir de marzo de 2016. La técnica descrita en este documento no forma parte del conjunto de pruebas estable para Xamarin, por lo que podría interrumpirse en el futuro.

Si observa que la técnica ya no funciona u observa otros errores en el documento, no dude en agregarlos a la discusión en el siguiente hilo del foro: [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](https://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm).
Gracias
