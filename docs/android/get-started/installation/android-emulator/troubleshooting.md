---
title: Solución de problemas de Android Emulator
description: En este artículo se explica cómo diagnosticar y solucionar los problemas que pueden producirse al usar Android Emulator.
zone_pivot_groups: platform
ms.prod: xamarin
ms.assetid: 4F053CC9-9378-47CB-8002-978A6558C4D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/27/2018
ms.openlocfilehash: 34416145fcf66f28d80a35a7d4dbc9b2fc2c6351
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91454486"
---
# <a name="android-emulator-troubleshooting"></a>Solución de problemas de Android Emulator

_En este artículo se describen los mensajes de advertencia y los problemas más comunes que se producen al configurar y ejecutar Android Emulator. Además, se describen soluciones para resolver estos errores, así como algunas sugerencias para solucionar problemas que le ayudarán a diagnosticar problemas del emulador._

::: zone pivot="windows"

## <a name="deployment-issues-on-windows"></a>Problemas de implementación en Windows

El emulador puede mostrar algunos mensajes de error al implementar la aplicación. Aquí se explican los errores y las soluciones más comunes.

### <a name="deployment-errors"></a>Errores de implementación

Si se ve un error al instalar el APK en el emulador o al ejecutar Android Debug Bridge (**adb**), compruebe que Android SDK se pueda conectar con el emulador. Para comprobar la conectividad del emulador, siga estos pasos:

1. Inicie el emulador desde **Android Device Manager** (seleccione el dispositivo virtual y haga clic en **Iniciar**).

2. Abra un símbolo del sistema y vaya a la carpeta donde está instalado **adb**. Si Android SDK está instalado en su ubicación predeterminada, **adb** se encuentra en **C:\\Archivos de programa (x86)\\Android\\android-sdk\\platform-tools\\adb.exe**; de lo contrario, modifique esta ruta de acceso para la ubicación de Android SDK en el equipo.

3. Escriba el comando siguiente:

   ```shell
   adb devices
   ```

4. Si el emulador es accesible desde Android SDK, el emulador debe aparecer en la lista de dispositivos conectados. Por ejemplo:

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. Si el emulador no aparece en esta lista, inicie **Android SDK Manager**, aplique todas las actualizaciones e intente iniciar el emulador de nuevo.

### <a name="mmio-access-error"></a>Error de acceso MMIO

Si se muestra el mensaje **Se ha producido un error de acceso MMIO**, reinicie el emulador.

<a name="gps-win"></a>

## <a name="missing-google-play-services"></a>Ausencia de Google Play Services

Si el dispositivo virtual que se ejecuta en el emulador no tiene instalado Google Play Services o Google Play Store, esta condición suele deberse a la creación de un dispositivo virtual sin incluir estos paquetes. Cuando cree un dispositivo virtual (vea [Administración de dispositivos virtuales con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), asegúrese de seleccionar una de las opciones siguientes, o las dos:

- **API de Google**: incluye Google Play Services en el dispositivo virtual.
- **Google Play Store**: incluye Google Play Store en el dispositivo virtual.

Por ejemplo, este dispositivo virtual incluirá Google Play Services y Google Play Store:

[![Dispositivo virtual Android de ejemplo con Google Play Services y Google Play Store habilitados](troubleshooting-images/win/00-add-gps-w158-sml.png)](troubleshooting-images/win/00-add-gps-w158.png#lightbox)

> [!NOTE]
> Las imágenes de Google Play Store solo están disponibles para algunos tipos de dispositivos base como Pixel, Pixel 2, Nexus 5 y Nexus 5X.

<a name="perf-win"></a>

## <a name="performance-issues"></a>Problemas de rendimiento

Los problemas de rendimiento se suelen producir por uno de los problemas siguientes:

- El emulador se ejecuta sin aceleración de hardware.

- El dispositivo virtual que se ejecuta en el emulador no usa una imagen del sistema basado en x86.

En las secciones siguientes se tratan estos escenarios con más detalle.

### <a name="hardware-acceleration-is-not-enabled"></a>La aceleración de hardware no está habilitada

Si la aceleración de hardware no está habilitada, el inicio de un dispositivo virtual desde Device Manager generará un cuadro de diálogo con un mensaje de error en el que se indica que Windows Hypervisor Platform (WHPX) no está correctamente configurado:

![Advertencia de ejemplo de Device Manager](troubleshooting-images/win/01-dev-mgr-warning-w158.png)

Si se muestra este mensaje de error, vea [Problemas de aceleración de hardware](#accel-issues-win) a continuación para obtener los pasos que se pueden seguir para comprobar y habilitar la aceleración de hardware.

### <a name="acceleration-is-enabled-but-the-emulator-runs-too-slowly"></a>La aceleración está habilitada, pero el emulador se ejecuta con demasiada lentitud 

Una causa común de este problema es que no se está usando una imagen basado en x86 en el dispositivo virtual (AVD). Cuando cree un dispositivo virtual (vea [Administración de dispositivos virtuales con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), asegúrese de seleccionar una imagen del sistema basado en x86:

[![Selección de una imagen del sistema x86 para un dispositivo virtual](troubleshooting-images/win/02-x86-virtual-device-w158-sml.png)](troubleshooting-images/win/02-x86-virtual-device-w158.png#lightbox)

<a name="accel-issues-win"></a>

## <a name="hardware-acceleration-issues"></a>Problemas de aceleración de hardware

Si usa Hyper-V o HAXM para la aceleración de hardware, es posible que experimente problemas o conflictos de configuración con otro software del equipo. Puede comprobar que la aceleración de hardware esté habilitada (y qué método de aceleración usa el emulador) si abre un símbolo del sistema y escribe el comando siguiente:

```cmd
"C:\Program Files (x86)\Android\android-sdk\emulator\emulator-check.exe" accel
```

Este comando asume que Android SDK está instalado en la ubicación predeterminada de **C:\\Archivos de programa (x86)\\Android\\android-sdk**; de lo contrario, modifique la ruta de acceso anterior para que sea la ubicación de Android SDK en el equipo.

### <a name="hardware-acceleration-not-available"></a>La aceleración de hardware no está disponible

Si Hyper-V está disponible, el comando **emulator-check.exe accel** devolverá un mensaje similar al ejemplo siguiente:

```cmd
HAXM is not installed, but Windows Hypervisor Platform is available.
```

Si HAXM está disponible, se devolverá un mensaje similar al ejemplo siguiente:

```cmd
HAXM version 6.2.1 (4) is installed and usable.
```

Si la aceleración de hardware no está disponible, se mostrará un mensaje similar al ejemplo siguiente (el emulador busca HAXM si no puede encontrar Hyper-V):

```cmd
HAXM is not installed on this machine
```

Si la aceleración de hardware no está disponible, vea [Aceleración con Hyper-V](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vswin#hyper-v-win) para obtener información sobre cómo habilitar la aceleración de hardware en el equipo.

### <a name="incorrect-bios-settings"></a>Configuración del BIOS incorrecta

Si el BIOS no se ha configurado correctamente para admitir la aceleración de hardware, se mostrará un mensaje similar al siguiente ejemplo al ejecutar el comando **emulator-check.exe accel**:

```cmd
VT feature disabled in BIOS/UEFI
```

Para corregir este problema, reinicie en el BIOS del equipo y habilite las opciones siguientes:

- Tecnología de virtualización (puede tener otra etiqueta según el fabricante de la placa base).
- Prevención de ejecución de datos forzada mediante hardware.

Si la aceleración de hardware está habilitada y el BIOS está configurado correctamente, el emulador se debería ejecutar de forma correcta con aceleración de hardware.
Pero pueden seguir surgiendo problemas debido a errores específicos de Hyper-V y HAXM, como se explica a continuación.

### <a name="hyper-v-issues"></a>Problemas de Hyper-V

En algunos casos, habilitar tanto **Hyper-V** como **Windows Hypervisor Platform** en el cuadro de diálogo **Activar o desactivar las características de Windows** puede que no habilite Hyper-V correctamente. Para comprobar que Hyper-V está habilitado, siga estos pasos:

1. En el cuadro de búsqueda de Windows, escriba **powershell**.

2. Haga clic con el botón derecho en **Windows PowerShell** en los resultados de la búsqueda y seleccione **Ejecutar como administrador**.

3. En la consola de PowerShell, escriba el comando siguiente:

    ```powershell
    Get-WindowsOptionalFeature -FeatureName Microsoft-Hyper-V-All -Online
    ```

    Si Hyper-V no está habilitado, se mostrará un mensaje similar al ejemplo siguiente para indicar que el estado de Hyper-V es **Deshabilitado**:

    ```
    FeatureName      : Microsoft-Hyper-V-All
    DisplayName      : Hyper-V
    Description      : Provides services and management tools for creating and running virtual machines and their resources.
    RestartRequired  : Possible
    State            : Disabled
    CustomProperties : 
    ```

4. En la consola de PowerShell, escriba el comando siguiente:

    ```powershell
    Get-WindowsOptionalFeature -FeatureName HypervisorPlatform -Online
    ```

    Si Hypervisor no está habilitado, se mostrará un mensaje similar al ejemplo siguiente para indicar que el estado de HypervisorPlatform es **Deshabilitado**:

    ```
    FeatureName      : HypervisorPlatform
    DisplayName      : Windows Hypervisor Platform
    Description      : Enables virtualization software to run on the Windows hypervisor
    RestartRequired  : Possible
    State            : Disabled
    CustomProperties : 
    ```

Si Hyper-V o HypervisorPlatform no están habilitados, use los comandos de PowerShell siguientes para habilitarlos:

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
Enable-WindowsOptionalFeature -Online -FeatureName HypervisorPlatform -All
```

Cuando se completen estos comandos, reinicie. 

Para obtener más información sobre cómo habilitar Hyper-V (incluidas las técnicas para habilitar Hyper-V mediante la herramienta de Administración y mantenimiento de imágenes de implementación), vea [Instalar Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

### <a name="haxm-issues"></a>Problemas de HAXM

Normalmente los problemas de HAXM son consecuencia de conflictos con otras tecnologías de virtualización, configuraciones incorrectas o un controlador HAXM que no está actualizado.

### <a name="haxm-process-is-not-running"></a>No se está ejecutando el proceso de HAXM

Si HAXM está instalado, puede comprobar que el proceso de HAXM se está ejecutando si abre un símbolo del sistema y escribe el comando siguiente:

```cmd
sc query intelhaxm
```

Si el proceso de HAXM se está ejecutando, el resultado debería ser similar al siguiente:

```cmd
SERVICE_NAME: intelhaxm
    TYPE               : 1  KERNEL_DRIVER
    STATE              : 4  RUNNING
                            (STOPPABLE, NOT_PAUSABLE, IGNORES_SHUTDOWN)
    WIN32_EXIT_CODE    : 0  (0x0)
    SERVICE_EXIT_CODE  : 0  (0x0)
    CHECKPOINT         : 0x0
    WAIT_HINT          : 0x0
```

Si `STATE` no está establecido en `RUNNING`, vea [How to Use the Intel Hardware Accelerated Execution Manager](https://software.intel.com/android/articles/how-to-use-the-intel-hardware-accelerated-execution-manager-intel-haxm-android-emulator) (Cómo usar Intel Hardware Accelerated Execution Manager) para resolver el problema.

<a name="virt-conflicts"></a>

#### <a name="haxm-virtualization-conflicts"></a>Conflictos de virtualización de HAXM

HAXM puede entrar en conflicto con otras tecnologías que usan la virtualización, como Hyper-V y Windows Device Guard, y cierto software antivirus:

- **Hyper-V**: si usa una versión de Windows anterior a la **actualización de abril de 2018 de Windows 10 (compilación 1803)** y Hyper-V está habilitado, siga los pasos indicados en [Deshabilitar Hyper-V](#disable-hyperv) para poder habilitar HAXM.

- **Device Guard**: Device Guard y Credential Guard pueden impedir que se deshabilite Hyper-V en máquinas con Windows. Para deshabilitar Device Guard y Credential Guard, consulte [Deshabilitación de Device Guard](#disable-devguard).

- **Software antivirus**: si está ejecutando software antivirus que usa la virtualización asistida por hardware (por ejemplo, Avast), deshabilite o desinstale este software, reinicie el equipo e intente iniciar de nuevo Android Emulator.

#### <a name="incorrect-bios-settings"></a>Configuración del BIOS incorrecta

Si está usando HAXM en un equipo con Windows, HAXM no funcionará a menos que la tecnología de virtualización (Intel VT-x) esté habilitada en el BIOS. Si VT-x está deshabilitada, obtendrá un error similar al siguiente al intentar iniciar Android Emulator:

**This computer meets the requirements for HAXM, but Intel Virtualization Technology (VT-x) is not turned on.** (Este equipo cumple los requisitos de HAXM, pero Intel Virtualization Technology (VT-x) no está activada.)

Para corregir este error, arranque el equipo en el BIOS, habilite VT-x y SLAT (traducción de direcciones de segundo nivel) y, después, reinicie el equipo en Windows.

<a name="disable-hyperv"></a>

#### <a name="disabling-hyper-v"></a>Deshabilitar Hyper-V

Si usa una versión de Windows anterior a la **actualización de abril de 2018 de Windows 10 (compilación 1803)** y Hyper-V está habilitado, debe deshabilitar Hyper-V y reiniciar el equipo para instalar y usar HAXM. Si usa la **actualización de abril de 2018 de Windows 10 (compilación 1803)** o una versión posterior, Android Emulator versión 27.2.7 o posterior puede usar Hyper-V en lugar de HAXM para la aceleración de hardware, por lo que no es necesario deshabilitar Hyper-V.

Puede deshabilitar Hyper-V desde el Panel de control mediante estos pasos:

1. Escriba **características de windows** en el cuadro de búsqueda de Windows y seleccione **Activar o desactivar las características de Windows** en los resultados de la búsqueda.

2. Desactive **Hyper-V**:

    ![Deshabilitar Hyper-V en el cuadro de diálogo Características de Windows](troubleshooting-images/win/03-uncheck-hyper-v.png)

3. Reinicie el equipo.

Como alternativa, puede usar el comando de PowerShell siguiente para deshabilitar el hipervisor Hyper-V:

`Disable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-Hypervisor`

HAXM de Intel y Microsoft Hyper-V no pueden estar activos al mismo tiempo. Desafortunadamente, no existe ninguna manera de alternar entre Hyper-V y HAXM sin reiniciar el equipo. 

En algunos casos, si Device Guard y Credential Guard están habilitados, con los pasos anteriores no se deshabilitará Hyper-V correctamente. Si no puede deshabilitar Hyper-V (o parece estar deshabilitado pero sigue habiendo errores en la instalación de HAXM), siga los pasos de la sección siguiente para deshabilitar Device Guard y Credential Guard.

<a name="disable-devguard"></a>

#### <a name="disabling-device-guard"></a>Deshabilitación de Device Guard

Device Guard y Credential Guard pueden evitar que se deshabilite Hyper-V en equipos Windows. Esta situación suele ser un problema para los equipos unidos a un dominio que se configuran y se controlan mediante una organización propietaria. En Windows 10, siga estos pasos para comprobar si se está ejecutando **Device Guard**:

1. Escriba **información del sistema** en el cuadro de búsqueda de Windows y seleccione **Información del sistema** en los resultados de la búsqueda.

2. En **Resumen del sistema**, compruebe si está presente **Seguridad basada en la virtualización de Device Guard** y si su estado es **En ejecución**:

   [![Device Guard está presente y en ejecución](troubleshooting-images/win/04-device-guard-sml.png)](troubleshooting-images/win/04-device-guard.png#lightbox)

Si Device Guard está habilitado, siga estos pasos para deshabilitarlo:

1. Asegúrese de que **Hyper-V** esté deshabilitado (en **Activar o desactivar las características de Windows**) como se explica en la sección anterior.

2. En el cuadro de búsqueda de Windows, escriba **gpedit.msc** y seleccione el resultado de búsqueda **Editar directiva de grupo**. Estos pasos inician el **Editor de directivas de grupo local**.

3. En el **Editor de directivas de grupo local**, vaya a **Configuración del equipo > Plantillas administrativas > Sistema > Device Guard**:

   [![Device Guard en el Editor de directivas de grupo local](troubleshooting-images/win/05-group-policy-editor-sml.png)](troubleshooting-images/win/05-group-policy-editor.png#lightbox)

4. Cambie **Activar la seguridad basada en la virtualización** a **Deshabilitado** (como se ha indicado arriba) y salga del **Editor de directivas de grupo local**.

5. En el cuadro de búsqueda de Windows, escriba **cmd**. Si aparece **Símbolo del sistema** en los resultados de búsqueda, haga clic con el botón derecho en **Símbolo del sistema** y seleccione **Ejecutar como administrador**.

6. Copie y pegue los siguientes comandos en la ventana del símbolo del sistema (si la unidad **Z:** se está usando, seleccione una letra de unidad no usada en su lugar):

    ```cmd
    mountvol Z: /s
    copy %WINDIR%\System32\SecConfig.efi Z:\EFI\Microsoft\Boot\SecConfig.efi /Y
    bcdedit /create {0cb3b571-2f2e-4343-a879-d86a476d7215} /d "DebugTool" /application osloader
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} path "\EFI\Microsoft\Boot\SecConfig.efi"
    bcdedit /set {bootmgr} bootsequence {0cb3b571-2f2e-4343-a879-d86a476d7215}
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} loadoptions DISABLE-LSA-ISO,DISABLE-VBS
    bcdedit /set {0cb3b571-2f2e-4343-a879-d86a476d7215} device partition=Z:
    mountvol Z: /d
    ```

7. Reinicie el equipo. En la pantalla de arranque, debería ver un mensaje similar al siguiente:

   **¿Quiere deshabilitar Credential Guard?**

   Pulse la tecla indicada para deshabilitar Credential Guard como se pide.

8. Una vez reiniciado el equipo, compruebe de nuevo que Hyper-V está deshabilitado (como se ha explicado en los pasos anteriores).

Si Hyper-V aún no está deshabilitado, es posible que las directivas del equipo unido a dominio eviten que se deshabiliten Device Guard o Credential Guard. En este caso, puede solicitar una exención al administrador del dominio que le permita no usar Credential Guard. Como alternativa, puede usar un equipo que no esté unido a un dominio si tiene que utilizar HAXM.

## <a name="additional-troubleshooting-tips"></a>Otras sugerencias para la de solución de problemas

Las sugerencias siguientes suelen ser útiles para diagnosticar problemas de Android Emulator.

### <a name="starting-the-emulator-from-the-command-line"></a>Inicio de Android Emulator desde la línea de comandos

Si el emulador no se está ejecutando, puede iniciarlo desde la línea de comandos (en lugar de desde dentro de Visual Studio) para ver su salida. Normalmente, las imágenes de AVD de Android Emulator se almacenan en la siguiente ubicación (reemplace *nombre_de_usuario* con el nombre de usuario de Windows):

**C:\\Usuarios\\*nombre_de_usuario*\\.android\\avd**

Puede iniciar el emulador con una imagen de AVD desde esta ubicación si pasa el nombre de la carpeta del AVD. Por ejemplo, este comando inicia un AVD denominado **Pixel_API_27**:

```cmd
"C:\Program Files (x86)\Android\android-sdk\emulator\emulator.exe" -partition-size 512 -no-boot-anim -verbose -feature WindowsHypervisorPlatform -avd Pixel_API_27 -prop monodroid.avdname=Pixel_API_27
```

En este ejemplo se asume que Android SDK está instalado en la ubicación predeterminada de **C:\\Archivos de programa (x86)\\Android\\android-sdk**; de lo contrario, modifique la ruta de acceso anterior para que sea la ubicación de Android SDK en el equipo.

Al ejecutar este comando, generará varias líneas de salida mientras se inicia el emulador. En concreto, se imprimirán líneas como las del ejemplo siguiente si la aceleración de hardware está habilitada y funciona correctamente (en este ejemplo, se usa HAXM para la aceleración de hardware):

```cmd
emulator: CPU Acceleration: working
emulator: CPU Acceleration status: HAXM version 6.2.1 (4) is installed and usable.
```

### <a name="viewing-device-manager-logs"></a>Visualización de registros de Device Manager

A menudo, se pueden ver los registros de Device Manager para diagnosticar problemas del emulador. Estos registros se escriben en la ubicación siguiente:

**C:\\Usuarios\\*nombre_de_usuario*\\AppData\\Roaming\\XamarinDeviceManager**

Puede ver cada archivo **DeviceManager.log** con un editor de texto como Bloc de notas. En la entrada de registro de ejemplo siguiente se indica que no se encontró HAXM en el equipo:

```cmd
Component Intel x86 Emulator Accelerator (HAXM installer) r6.2.1 [Extra: (Intel Corporation)] not present on the system
```

::: zone-end
::: zone pivot="macos"

## <a name="deployment-issues-on-macos"></a>Problemas de implementación en macOS

El emulador puede mostrar algunos mensajes de error al implementar la aplicación. A continuación se explican los errores y las soluciones más comunes.

### <a name="deployment-errors"></a>Errores de implementación

Si se ve un error al instalar el APK en el emulador o al ejecutar Android Debug Bridge (**adb**), compruebe que Android SDK se pueda conectar con el emulador. Para comprobar la conectividad, siga estos pasos:

1. Inicie el emulador desde **Android Device Manager** (seleccione el dispositivo virtual y haga clic en **Iniciar**).

2. Abra un símbolo del sistema y vaya a la carpeta donde está instalado **adb**. Si Android SDK está instalado en su ubicación predeterminada, **adb** se encuentra en **~/Library/Developer/Xamarin/android-sdk-macosx/platform-tools/adb**; de lo contrario, modifique esta ruta de acceso para la ubicación de Android SDK en el equipo.

3. Escriba el comando siguiente:

   ```shell
   adb devices
   ```

4. Si el emulador es accesible desde Android SDK, el emulador debe aparecer en la lista de dispositivos conectados. Por ejemplo:

   ```shell
   List of devices attached
   emulator-5554   device
   ```

5. Si el emulador no aparece en esta lista, inicie **Android SDK Manager**, aplique todas las actualizaciones e intente iniciar el emulador de nuevo.

### <a name="mmio-access-error"></a>Error de acceso MMIO

Si se muestra **Se ha producido un error de acceso MMIO**, reinicie el emulador.

<a name="gps-mac"></a>

## <a name="missing-google-play-services"></a>Ausencia de Google Play Services

Si el dispositivo virtual que se ejecuta en el emulador no tiene instalado Google Play Services o Google Play Store, esta condición suele deberse a la creación de un dispositivo virtual sin incluir estos paquetes. Cuando cree un dispositivo virtual (vea [Administración de dispositivos virtuales con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), asegúrese de seleccionar una de las opciones siguientes, o las dos:

- **API de Google**: incluye Google Play Services en el dispositivo virtual.
- **Google Play Store**: incluye Google Play Store en el dispositivo virtual.

Por ejemplo, este dispositivo virtual incluirá Google Play Services y Google Play Store:

[![Dispositivo virtual Android de ejemplo con Google Play Services y Google Play Store habilitados](troubleshooting-images/mac/01-google-play-services-m75-sml.png)](troubleshooting-images/mac/01-google-play-services-m75.png#lightbox)

> [!NOTE]
> Las imágenes de Google Play Store solo están disponibles para algunos tipos de dispositivos base como Pixel, Pixel 2, Nexus 5 y Nexus 5X.

<a name="perf-mac"></a>

## <a name="performance-issues"></a>Problemas de rendimiento

Los problemas de rendimiento se suelen producir por uno de los problemas siguientes:

- El emulador se ejecuta sin aceleración de hardware.

- El dispositivo virtual que se ejecuta en el emulador no usa una imagen del sistema basado en x86.

En las secciones siguientes se tratan estos escenarios con más detalle.

### <a name="hardware-acceleration-is-not-enabled"></a>La aceleración de hardware no está habilitada

Si la aceleración de hardware no está habilitada, es posible que aparezca un cuadro de diálogo con un mensaje como **el dispositivo se ejecutará sin aceleración** al implementar la aplicación en Android Emulator. Si no está seguro de si la aceleración de hardware está habilitada en el equipo (o le gustaría saber qué tecnología proporciona la aceleración), vea [Problemas de aceleración de hardware](#accel-issues-mac) a continuación para conocer los pasos que puede seguir para comprobar y habilitar la aceleración de hardware.

### <a name="acceleration-is-enabled-but-the-emulator-runs-too-slowly"></a>La aceleración está habilitada, pero el emulador se ejecuta con demasiada lentitud 

Una causa común de este problema es que no se está usando una imagen basada en x86 en el dispositivo virtual. Cuando cree un dispositivo virtual (vea [Administración de dispositivos virtuales con Android Device Manager](~/android/get-started/installation/android-emulator/device-manager.md)), asegúrese de seleccionar una imagen del sistema basada en x86:

[![Selección de una imagen del sistema x86 para un dispositivo virtual](troubleshooting-images/mac/02-x86-virtual-device-m75-sml.png)](troubleshooting-images/mac/02-x86-virtual-device-m75.png#lightbox)

<a name="accel-issues-mac"></a>

## <a name="hardware-acceleration-issues"></a>Problemas de aceleración de hardware

Con independencia de que use Hypervisor Framework o HAXM para la aceleración de hardware del emulador, es posible que experimente problemas causados por problemas de instalación o una versión obsoleta de macOS. Las secciones siguientes pueden ayudarle a resolver este problema.

<a name="hypervisor-issues"></a>

### <a name="hypervisor-framework-issues"></a>Problemas de Hypervisor Framework

Si usa macOS 10.10 o una versión posterior en un equipo Mac más reciente, Android Emulator usará automáticamente Hypervisor Framework para la aceleración de hardware. Pero es posible que algunos equipos Mac más antiguos o que ejecuten una versión de macOS anterior a 10.10 no proporcionen compatibilidad con Hypervisor Framework.

Para determinar si el equipo Mac es compatible con Hypervisor Framework, abra una ventana de Terminal y escriba el comando siguiente:

```bash
sysctl kern.hv_support
```

Si el equipo Mac es compatible con Hypervisor Framework, el comando anterior devolverá el resultado siguiente:

```bash
kern.hv_support: 1
```

Si Hypervisor Framework no está disponible en el equipo Mac, puede seguir los pasos descritos en [Aceleración con HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vsmac#haxm-mac) para usar HAXM para la aceleración en su lugar.

### <a name="haxm-issues"></a>Problemas de HAXM

Si Android Emulator no se inicia correctamente, esto suele deberse a problemas relacionados con HAXM. Normalmente los problemas de HAXM son consecuencia de conflictos con otras tecnologías de virtualización, configuraciones incorrectas o un controlador HAXM que no está actualizado. Intente volver a instalar el controlador HAXM mediante los pasos detallados en [Instalación de HAXM](~/android/get-started/installation/android-emulator/hardware-acceleration.md?tabs=vsmac#install-haxm-mac).

## <a name="additional-troubleshooting-tips"></a>Otras sugerencias para la de solución de problemas

Las sugerencias siguientes suelen ser útiles para diagnosticar problemas de Android Emulator.

### <a name="starting-the-emulator-from-the-command-line"></a>Inicio de Android Emulator desde la línea de comandos

Si el emulador no se está ejecutando, puede iniciarlo desde la línea de comandos (en lugar de desde dentro de Visual Studio para Mac) para ver su salida. Normalmente, las imágenes de AVD de Android Emulator se almacenan en la ubicación siguiente:

**~/.android/avd**

Puede iniciar el emulador con una imagen de AVD desde esta ubicación si pasa el nombre de la carpeta del AVD. Por ejemplo, este comando inicia un AVD denominado **Pixel_2_API_28**:

```cmd
~/Library/Developer/Xamarin/android-sdk-macosx/emulator/emulator -partition-size 512 -no-boot-anim -verbose -feature WindowsHypervisorPlatform -avd Pixel_2_API_28 -prop monodroid.avdname=Pixel_2_API_28
```

Si Android SDK está instalado en su ubicación predeterminada, el emulador se encuentra en el directorio **~/Library/Developer/Xamarin/android-sdk-macosx/emulator**; de lo contrario, modifique esta ruta de acceso para la ubicación de Android SDK en el equipo Mac.

Al ejecutar este comando, generará varias líneas de salida mientras se inicia el emulador. En concreto, se imprimirán líneas como las del ejemplo siguiente si la aceleración de hardware está habilitada y funciona correctamente (en este ejemplo, se usa Hypervisor Framework para la aceleración de hardware):

```cmd
emulator: CPU Acceleration: working
emulator: CPU Acceleration status: Hypervisor.Framework OS X Version 10.13
```

### <a name="viewing-device-manager-logs"></a>Visualización de registros de Device Manager

A menudo, se pueden ver los registros de Device Manager para diagnosticar problemas del emulador. Estos registros se escriben en la ubicación siguiente:

**~/Library/Logs/XamarinDeviceManager**

Puede ver los archivos **Android Devices.log** si hace doble clic para abrirlos en la aplicación de consola. La entrada de registro de ejemplo siguiente indica que no se encontró HAXM:

```cmd
Component Intel x86 Emulator Accelerator (HAXM installer) r6.2.1 [Extra: (Intel Corporation)] not present on the system
```

::: zone-end