---
title: ¿Es posible conectarse a emuladores de Android que se ejecutan en un Mac desde una máquina virtual de Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/21/2018
ms.openlocfilehash: 8537e075c4ac1dba8362ec9ce5fc77ee0935503c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523308"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>¿Es posible conectarse a emuladores de Android que se ejecutan en un Mac desde una máquina virtual de Windows?

Para conectarse al Android Emulator que se ejecuta en un equipo Mac desde una máquina virtual de Windows, siga estos pasos:

1. Inicie el emulador en el equipo Mac.

2. Eliminar el `adb` servidor en el equipo Mac:

    ```bash
    adb kill-server
    ```

3. Tenga en cuenta que el emulador está escuchando en dos puertos TCP en la interfaz de red de bucle invertido:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    El puerto impar es el que se utiliza para conectarse a `adb`. Vea también [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking).

4. _Opción 1_: Realice[`nc`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/nc.1.html)
    para reenviar paquetes TCP entrantes recibidos externamente en el puerto 5555 (o en cualquier otro puerto que desee) al puerto impar en la interfaz de bucle invertido (**127.0.0.1 5555** en este ejemplo) y para reenviar los paquetes salientes de otra manera:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Siempre y cuando los `nc` comandos sigan ejecutándose en una ventana de terminal, los paquetes se reenviarán según lo previsto. Puede escribir control-C en la ventana de terminal para salir de `nc` los comandos una vez que haya terminado de usar el emulador.

    (La opción 1 suele ser más fácil que la opción 2, especialmente si las **preferencias del sistema > seguridad & privacidad > Firewall** está activada). 

    _Opción 2_: Realice[`pfctl`](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man8/pfctl.8.html)
    para redirigir los paquetes TCP desde el `5555` puerto (o cualquier otro puerto que desee) de la interfaz de [red compartida](http://kb.parallels.com/en/4948) al puerto impar en la interfaz de bucle invertido`127.0.0.1:5555` (en este ejemplo):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Este comando configura el reenvío de puertos mediante `pf packet filter` el servicio de sistema de. Los saltos de línea son importantes. Asegúrese de mantenerlos intactos al pegarlos. También tendrá que ajustar el nombre de la interfaz de *vmnet8* si utiliza Parallels. `vmnet8`es el nombre del *dispositivo NAT* especial para el modo de *redes* compartidas en la fusión de VMware. Es probable que la interfaz de red adecuada en Parallels sea [vnic0](http://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5. Conéctese al emulador desde la máquina Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Reemplace "IP-address-of-the-Mac" por la dirección IP del equipo Mac, por ejemplo, como se `ifconfig vmnet8 | grep 'inet '`muestra en. Si es necesario, `5555` reemplace por el otro puerto que desee del paso 4.\. (Nota: una manera de obtener acceso a la línea de `adb` comandos a es mediante [**Herramientas > el símbolo del sistema Android > Android ADB**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) en Visual Studio).

### <a name="alternate-technique-using-ssh"></a>Técnica alternativa con`ssh`

Si ha habilitado _sesión remota_ en el equipo Mac, puede usar `ssh` el reenvío de puerto para conectarse al emulador.

1. Instale un cliente SSH en Windows. Una opción es instalar [git para Windows](https://git-for-windows.github.io/). El `ssh` comando estará disponible en el símbolo del sistema de **git Bash** .

2. Siga los pasos del 1-3 de arriba para iniciar el emulador `adb` , eliminar el servidor en el equipo Mac e identificar los puertos del emulador.

3. Ejecute `ssh` en Windows para configurar el reenvío de Puerto bidireccional entre un puerto local en Windows (`localhost:15555` en este ejemplo) y el puerto del emulador impar en la interfaz de bucle invertido de Mac`127.0.0.1:5555` (en este ejemplo):

    ```cmd 
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Reemplace `mac-username` por el nombre de usuario de Mac `whoami`como se indica en. Reemplace `ip-address-of-the-mac` por la dirección IP del equipo Mac.

4. Conéctese al emulador mediante el puerto local en Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Nota: una forma sencilla de obtener acceso a la línea de `adb` comandos a es mediante [ **Herramientas > el símbolo del sistema Android > Android ADB** en Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat)).

Una pequeña PRECAUCIÓN: Si usa el puerto `5555` para el puerto local, `adb` se considerará que el emulador se ejecuta localmente en Windows. Esto no causa ningún problema en Visual Studio, pero en Visual Studio para Mac hace que la aplicación se cierre inmediatamente después del inicio.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>Todavía no se `adb -H` admite la técnica alternativa con

En teoría, otro enfoque sería usar `adb`la funcionalidad integrada de para conectarse a un `adb` servidor que se ejecuta en un equipo remoto (vea por ejemplo [https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325)).
Sin embargo, las extensiones IDE de Xamarin. Android no proporcionan una manera de configurar esta opción.

## <a name="contact-information"></a>Información de contacto

En este documento se describe el comportamiento actual a partir de marzo de 2016. La técnica descrita en este documento no forma parte del conjunto de pruebas estable para Xamarin, por lo que podría interrumpirse en el futuro.

Si observa que la técnica ya no funciona o observa otros errores en el documento, no dude en agregarla a la discusión en el siguiente subproceso del Foro: [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm).
Gracias

