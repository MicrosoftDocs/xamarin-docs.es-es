---
title: Aceleración de hardware para el rendimiento del emulador (Hyper-V y HAXM)
description: En este artículo se explica cómo usar las características de aceleración de hardware del equipo para maximizar el rendimiento de Android Emulator.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 915874C3-2F0F-4D83-9C39-ED6B90BB2C8E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 08/27/2018
ms.openlocfilehash: 5c6eb6e49c7c8a4071591e46a5afc02a6ff6b4e6
ms.sourcegitcommit: 6b833f44d5fd8dc7ab7f8546e8b7d383e5a989db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105909"
---
# <a name="hardware-acceleration-for-emulator-performance-hyper-v--haxm"></a>Aceleración de hardware para el rendimiento del emulador (Hyper-V y HAXM)

_En este artículo se explica cómo usar las características de aceleración de hardware del equipo para maximizar el rendimiento de Android Emulator._

Visual Studio facilita a los desarrolladores la prueba y depuración de las aplicaciones Xamarin.Android mediante el uso de Android Emulator en situaciones en las que un dispositivo Android no está disponible o no es práctico.
Aun así, Android Emulator se ejecuta demasiado despacio si la aceleración de hardware no está disponible en el equipo que lo ejecuta. Es posible mejorar drásticamente el rendimiento de Android Emulator mediante el uso de imágenes de dispositivo virtual x86 especiales junto con las características de virtualización del equipo.

| Escenario    | HAXM        | WHPX       | Hypervisor.Framework |
| ----------- | ----------- | -----------| ----------- |
| Tiene un procesador Intel | X | X | X |
| Tiene un procesador AMD   |   | X |   |
| Quiere admitir Hyper-V |   | X |   |
| Quiere admitir la virtualización anidada |   | Limitado |   |
| Quiere usar tecnologías como Docker  |   | X | X |

::: zone pivot="windows"

## <a name="accelerating-android-emulators-on-windows"></a>Aceleración de Android Emulator en Windows

Las tecnologías de virtualización siguientes están disponibles para la aceleración de Android Emulator:

1. **Hyper-V y Windows Hypervisor Platform (WHPX) de Microsoft**.
   [Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/) es una característica de virtualización de Windows que permite ejecutar sistemas de equipos virtualizados en un equipo host físico.

2. **Hardware Accelerated Execution Manager (HAXM) de Intel**.
   HAXM es un motor de virtualización para los equipos que ejecutan CPU de Intel.

Para obtener la mejor experiencia en Windows, se recomienda usar HAXM para acelerar el emulador de Android. Si HAXM no está disponible en el equipo, se puede usar Windows Hypervisor Platform (WHPX). Android Emulator usará automáticamente la aceleración de hardware si se cumplen los criterios siguientes:

- La aceleración de hardware está disponible y habilitada en el equipo de desarrollo.

- El emulador ejecuta una imagen del sistema creada para un dispositivo virtual basado en **x86**.

> [!IMPORTANT]
> No se puede ejecutar un emulador acelerado por una máquina virtual dentro de otra, como sería el caso de una hospedada en VirtualBox, VMWare o Docker. Es necesario ejecutar Android Emulator [directamente en el hardware del sistema](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

Para obtener información sobre cómo iniciar y depurar con Android Emulator, vea [Depuración en Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).

<a name="hyper-v-win" />

## <a name="accelerating-with-hyper-v"></a>Aceleración con Hyper-V

Hyper-V es la opción recomendada para la aceleración de Android Emulator.
Antes de habilitar Hyper-V, lea la sección siguiente para comprobar que su equipo es compatible con Hyper-V.

### <a name="verifying-support-for-hyper-v"></a>Comprobación de la compatibilidad con Hyper-V

Hyper-V se ejecuta en Windows Hypervisor Platform. Para usar Android Emulator con Hyper-V, el equipo debe cumplir los siguientes criterios para admitir Windows Hypervisor Platform:

- El hardware del equipo debe cumplir los siguientes requisitos:

  - Una CPU AMD Ryzen o Intel de 64 bits con SLAT (Traducción de direcciones de segundo nivel).
  - Compatibilidad de CPU para la extensión de modo de monitor de máquina virtual (VT-c en CPU de Intel).
  - 4 GB de memoria, como mínimo.

- En el BIOS del equipo, deben habilitarse los siguientes elementos:

  - Tecnología de virtualización (puede tener otra denominación según el fabricante de la placa base).
  - Prevención de ejecución de datos forzada mediante hardware.

- El equipo debe actualizarse a Windows 10, actualización de abril de 2018 (compilación 1803) o una versión posterior. Puede comprobar que la versión de Windows está actualizada mediante los pasos siguientes:

  1. En el cuadro de búsqueda de Windows, escriba **Acerca de**.
  2. Seleccione **About your PC** (Acerca del equipo) en los resultados de búsqueda.
  3. Desplácese hacia abajo por el cuadro de diálogo **Acerca de** hasta la sección **Especificaciones de Windows**.
  4. Compruebe que el número de **Versión** es al menos 1803:

      [![Especificaciones de Windows](hardware-acceleration-images/win/01-about-windows-w10-sml.png)](hardware-acceleration-images/win/01-about-windows-w10.png#lightbox)

Para comprobar que el hardware y el software del equipo es compatible con Hyper-V, abra un símbolo del sistema y escriba el comando siguiente:

```cmd
systeminfo
```

Si todos los requisitos de Hyper-V enumerados tienen un valor de **Sí**, el equipo es compatible con Hyper-V. Por ejemplo:

[![Salida de ejemplo de systeminfo](hardware-acceleration-images/win/02-systeminfo-w158-sml.png)](hardware-acceleration-images/win/02-systeminfo-w158.png#lightbox)

### <a name="enabling-hyper-v-acceleration"></a>Habilitación de la aceleración de Hyper-V

Si el equipo cumple los criterios mencionados anteriormente, siga estos pasos para la aceleración de Android Emulator con Hyper-V:

1. Escriba **características de windows** en el cuadro de búsqueda de Windows y seleccione **Activar o desactivar las características de Windows** en los resultados de búsqueda. En el cuadro de diálogo **Características de Windows**, habilite **Hyper-V** y **Windows Hypervisor Platform**:

    [![Habilitación de Hyper-V y Windows Hypervisor Platform](hardware-acceleration-images/win/03-hyper-v-settings-w158-sml.png)](hardware-acceleration-images/win/03-hyper-v-settings-w158.png#lightbox)

   Después de realizar estos cambios, reinicie el equipo.

2. **Instale [Visual Studio 15.8 o una versión posterior](https://visualstudio.microsoft.com/vs/)** (esta versión de Visual Studio proporciona compatibilidad con el IDE para ejecutar Android Emulator con Hyper-V).

3. **Instale el paquete 27.2.7 o posterior de Android Emulator**. Para instalar este paquete, vaya a **Herramientas > Android > Android SDK Manager** en Visual Studio. Seleccione la pestaña **Herramientas** y asegúrese de que la versión de Android Emulator sea como mínimo la 27.2.7. Asegúrese también de que la versión de Android SDK Tools sea la 26.1.1 o posterior:

    [![Cuadro de diálogo Herramientas y Android SDK](hardware-acceleration-images/win/04-sdk-manager-w158-sml.png)](hardware-acceleration-images/win/04-sdk-manager-w158.png#lightbox)

Cuando cree un dispositivo virtual (consulte [Administración de dispositivos virtuales con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), no olvide seleccionar una imagen del sistema basada en **x86**. Si usa una imagen de sistema basada en ARM, el dispositivo virtual no se acelerará y se ejecutará con lentitud.

## <a name="accelerating-with-haxm"></a>Aceleración con HAXM

Utilice HAXM para la aceleración de Android Emulator si su equipo no es compatible con Hyper-V. Debe [deshabilitar Device Guard](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vswin#disable-devguard) si desea usar HAXM.

### <a name="verifying-haxm-support"></a>Comprobación de la compatibilidad con HAXM

Para determinar si el hardware es compatible con HAXM, siga los pasos detallados en [¿Admite mi procesador la Tecnología de virtualización Intel? ](https://www.intel.com/content/www/us/en/support/processors/000005486.html).
Si el hardware es compatible con HAXM, puede comprobar si HAXM ya está instalado mediante los pasos siguientes:

1. Abra una ventana de símbolo de sistema y escriba el siguiente comando:

    ```cmd
    sc query intelhaxm
    ```

2. Examine la salida para ver si se está ejecutando el proceso de HAXM. Si es así, debería ver que la salida indica el estado `intelhaxm` como `RUNNING`. Por ejemplo:

    ![Salida del comando sc query cuando HAXM está disponible](hardware-acceleration-images/win/05-sc_query-w158.png)

   Si `STATE` no está establecido en `RUNNING`, HAXM no está instalado.

Si el equipo es compatible con HAXM pero HAXM no está instalado, siga los pasos de la sección siguiente para instalar HAXM.

<a name="install-haxm-win" />

### <a name="installing-haxm"></a>Instalación de HAXM

En la página de [Intel Hardware Accelerated Execution Manager](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager) hay disponibles paquetes de instalación de HAXM para Windows. Siga estos pasos para descargar e instalar HAXM:

1. En el sitio web de Intel, descargue la última versión del instalador del [motor de virtualización de HAXM](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager/) para Windows. La ventaja de descargar el instalador de HAXM directamente desde el sitio web de Intel es que se asegura de que esté usando la versión más reciente.

2. Ejecute **intelhaxm-android.exe** para iniciar el instalador de HAXM. Acepte los valores predeterminados en los cuadros de diálogo del instalador:

   ![Ventana de instalación de Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/win/06-haxm-installer.png)

Cuando cree un dispositivo virtual (consulte [Administración de dispositivos virtuales con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), no olvide seleccionar una imagen del sistema basada en **x86**. Si usa una imagen de sistema basada en ARM, el dispositivo virtual no se acelerará y se ejecutará con lentitud.

## <a name="troubleshooting"></a>Solución de problemas

Para obtener ayuda con la solución de problemas de aceleración de hardware, consulte la guía de [solución de problemas](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vswin#accel-issues-win) de Android Emulator.

::: zone-end
::: zone pivot="macos"

## <a name="accelerating-android-emulators-on-macos"></a>Aceleración de Android Emulator en macOS

Las tecnologías de virtualización siguientes están disponibles para la aceleración de Android Emulator:

1. **Plataforma Hypervisor de Apple**.
   [Hypervisor](https://developer.apple.com/documentation/hypervisor) es una característica de macOS 10.10 y versiones posteriores que permite ejecutar máquinas virtuales en un equipo Mac.

2. **Hardware Accelerated Execution Manager (HAXM) de Intel**.
   [HAXM](https://software.intel.com/articles/intel-hardware-accelerated-execution-manager-intel-haxm) es un motor de virtualización para los equipos que ejecutan CPU de Intel.

Se recomienda el uso de la plataforma Hypervisor para la aceleración de Android Emulator. Si la plataforma Hypervisor no está disponible en el equipo Mac, se puede usar HAXM. Android Emulator usará automáticamente la aceleración de hardware si se cumplen los criterios siguientes:

- La aceleración de hardware está disponible y habilitada en el equipo de desarrollo.

- El emulador ejecuta una imagen del sistema creada para un dispositivo virtual basado en **x86**.

> [!IMPORTANT]
>
> No se puede ejecutar un emulador acelerado por una máquina virtual dentro de otra, como sería el caso de una hospedada en VirtualBox, VMware o Docker. Es necesario ejecutar Android Emulator [directamente en el hardware del sistema](https://developer.android.com/studio/run/emulator-acceleration.html#extensions).

Para obtener información sobre cómo iniciar y depurar con Android Emulator, vea [Depuración en Android Emulator](~/android/deploy-test/debugging/debug-on-emulator.md).

<a name="hypervisor" />

## <a name="accelerating-with-the-hypervisor-framework"></a>Aceleración con la plataforma Hypervisor

Para usar Android Emulator con la plataforma Hypervisor, el equipo Mac debe cumplir los siguientes criterios:

- El equipo Mac debe ejecutar macOS 10.10 o versiones posteriores.

- La CPU del equipo Mac debe ser compatible con la plataforma Hypervisor.

Si el equipo Mac cumple estos criterios, Android Emulator usará automáticamente la plataforma Hypervisor para la aceleración. Si no está seguro de si el equipo Mac admite la plataforma Hypervisor, consulte en la guía de [solución de problemas](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vsmac#hypervisor-issues) las distintas formas de comprobar que el equipo Mac es compatible con Hypervisor.

Si el equipo Mac no admite la plataforma Hypervisor, puede usar HAXM para la aceleración de Android Emulator (el proceso se describe a continuación).

<a name="haxm-mac" />

## <a name="accelerating-with-haxm"></a>Aceleración con HAXM

Si el equipo Mac no admite la plataforma Hypervisor (o si usa una versión de macOS anterior a la 10.10), puede usar **Hardware Accelerated Execution Manager** ([HAXM](https://software.intel.com/articles/intel-hardware-accelerated-execution-manager-intel-haxm)) de Intel para la aceleración de Android Emulator.

Antes de usar Android Emulator con HAXM por primera vez, conviene comprobar que HAXM esté instalado y disponible para Android Emulator.

### <a name="verifying-haxm-support"></a>Comprobación de la compatibilidad con HAXM

Puede comprobar si HAXM ya está instalado mediante los pasos siguientes:

1. Abra una sesión de Terminal y escriba el siguiente comando:

    ```bash
    ~/Library/Developer/Xamarin/android-sdk-macosx/tools/emulator -accel-check
    ```

   Este comando asume que Android SDK está instalado en la ubicación predeterminada: **~/Biblioteca/Desarrollador/Xamarin/android-sdk-macosx**; de lo contrario, modifique la ruta de acceso anterior para que sea la ubicación del SDK de Android en el equipo Mac.

2. Si HAXM está instalado, el comando anterior devolverá un mensaje similar al siguiente resultado:

    ```bash
    HAXM version 7.2.0 (3) is installed and usable.
    ```

   Si HAXM *no* está instalado, se devuelve un mensaje similar a la salida siguiente:

    ```bash
    HAXM is not installed on this machine (/dev/HAX is missing).
    ```

Si HAXM no está instalado, siga los pasos de la sección siguiente para instalar HAXM.

<a name="install-haxm-mac" />

### <a name="installing-haxm"></a>Instalación de HAXM

En la página de [Intel Hardware Accelerated Execution Manager](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager) hay disponibles paquetes de instalación de HAXM para macOS. Siga estos pasos para descargar e instalar HAXM:

1. En el sitio web de Intel, descargue la última versión del instalador del [motor de virtualización de HAXM](https://software.intel.com/android/articles/intel-hardware-accelerated-execution-manager/) para macOS.

2. Ejecute el instalador de HAXM. Acepte los valores predeterminados en los cuadros de diálogo del instalador:

   [![Ventana de instalación de Intel Hardware Accelerated Execution Manager](hardware-acceleration-images/mac/01-haxm-installer-sml.png)](hardware-acceleration-images/mac/01-haxm-installer.png#lightbox)

## <a name="troubleshooting"></a>Solución de problemas

Para obtener ayuda con la solución de problemas de aceleración de hardware, consulte la guía de [solución de problemas](~/android/get-started/installation/android-emulator/troubleshooting.md?tabs=vsmac#accel-issues-mac) de Android Emulator.

::: zone-end

## <a name="related-links"></a>Vínculos relacionados

- [Ejecutar aplicaciones en Android Emulator](https://developer.android.com/studio/run/emulator)
