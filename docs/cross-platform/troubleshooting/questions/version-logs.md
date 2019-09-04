---
title: ¿Dónde se puede encontrar mi información de versión y los registros?
description: En este documento se describe dónde buscar información y registros de la versión de Xamarin. Esta información es útil para diagnosticar problemas, enviar errores u obtener soporte técnico.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: CF386485-EAB0-4B9E-AA17-CB1B6462E505
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: cd7a2026a4d1d1458455733a6f2710364cc7fec7
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226774"
---
# <a name="where-can-i-find-my-version-information-and-logs"></a>¿Dónde se puede encontrar mi información de versión y los registros?

## <a name="outline"></a>Contorno

- [Información de versión](#version-information)
  - Información de versión de Windows
  - Información de versión de Mac
  - Android SDK Tools, herramientas de plataforma, herramientas de compilación
- [Registros del instalador y del IDE](#ide-and-installer-logs)
  - [Registros de Windows](#windows-logs)
    - Xamarin Studio
    - Xamarin para Visual Studio
    - Instalador universal de Xamarin
    - Instaladores individuales `.msi` , registros detallados
    - Inicio de Visual Studio, registros detallados
  - [Registros de Mac](#mac-logs)
    - Host de compilación
  - Visual Studio para Mac
    - Xamarin Studio
    - Instalador de Xamarin
- [Resultados de compilación detallados](#verbose-build-output-logs)
- [Depuración de registros para aplicaciones de Xamarin. Android y Xamarin. iOS](#debug-logs-for-xamarin-apps)
  - Registros `adb` de logcat de Android
  - registros del simulador de iOS (en Mac)
  - registros de dispositivos iOS (en Mac)

## <a name="a-idversion-information-nameversion-information-version-information"></a><a id="version-information" name="version-information" />Información de versión

Normalmente es mejor devolver toda la información de los botones **copiar información** . En caso contrario, a menudo tendremos que solicitar información adicional. Por ejemplo, las versiones de sistema operativo, la versión de Xcode, los niveles de API de Android instalados y la versión de .NET pueden ser importantes a la hora de ayudar a solucionar un problema.

### <a name="a-idwindows-version-information-namewindows-version-information-windows-version-information"></a><a id="windows-version-information" name="windows-version-information" />Información de versión de Windows

#### <a name="xamarin-studio"></a>Xamarin Studio

**Ayuda > Acerca de > Mostrar detalles > copiar información [Button]**

#### <a name="visual-studio"></a>Visual Studio

**Ayuda > Acerca de Microsoft Visual Studio > copiar información [Button]**

### <a name="a-idmac-version-information-namemac-version-information-mac-version-information"></a><a id="mac-version-information" name="mac-version-information" />Información de versión de Mac

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Visual Studio > Acerca de Visual Studio > Mostrar detalles > copiar información [Button]**

### <a name="a-idandroid-sdk-tools-versions-nameandroid-sdk-tools-versions-android-sdk-tools-platform-tools-build-tools"></a><a id="android-sdk-tools-versions" name="android-sdk-tools-versions" />Android SDK Tools, herramientas de plataforma, herramientas de compilación

Abra el administrador de Android SDK y realice una captura de pantalla de la sección **herramientas** principales.

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Herramientas > Abrir Android SDK Manager**

#### <a name="visual-studio"></a>Visual Studio

**Herramientas > Android > Abrir Android SDK Manager...**

## <a name="a-idide-and-installer-logs-nameide-and-installer-logs-ide-and-installer-logs"></a><a id="ide-and-installer-logs" name="ide-and-installer-logs" />Registros del instalador y del IDE

Para cada ubicación de registro, asegúrese de comprimir y adjuntar toda la carpeta de registro.

### <a name="a-idwindows-logs-namewindows-logs-windows-logs"></a><a id="windows-logs" name="windows-logs" />Registros de Windows

#### <a name="a-idwindows-logs-xamarin-vs-namewindows-logs-xamarin-vs--visual-studio-tools-for-xamarin"></a><a id="windows-logs-xamarin-vs" name="windows-logs-xamarin-vs" />Visual Studio Tools para Xamarin

`%LOCALAPPDATA%\Xamarin\Logs`

#### <a name="a-idvs-2017-namevs-2017--visual-studio-2017"></a><a id="vs-2017" name="vs-2017" /> Visual Studio 2017

[Cómo obtener los registros de instalación de Visual Studio](https://docs.microsoft.com/visualstudio/install/troubleshooting-installation-issues#how-to-get-the-visual-studio-installation-logs)

#### <a name="a-idvs-2015-namevs-2015--visual-studio-2015"></a><a id="vs-2015" name="vs-2015" /> Visual Studio 2015

#### <a name="a-idwindows-universal-installer-namewindows-universal-installer--xamarin-universal-installer"></a><a id="windows-universal-installer" name="windows-universal-installer" />Instalador "universal" de Xamarin

`%LOCALAPPDATA%\Xamarin\Universal`

Estos son los registros del `XamarinInstaller.exe` instalador.

#### <a name="a-idindividual-msi-installers-verbose-logs-nameindividual-msi-installers-verbose-logs-individual-msi-installers-verbose-logs"></a><a id="individual-msi-installers-verbose-logs" name="individual-msi-installers-verbose-logs" />Instaladores individuales `.msi` , registros detallados

```csharp
msiexec /i Xamarin.msi /l*vx "%USERPROFILE%\Desktop\Xamarin.log"
```

Referencia [Opciones de la línea de comandos](https://msdn.microsoft.com/library/aa367988.aspx)

#### <a name="a-idvisual-studio-startup-verbose-logs-namevisual-studio-startup-verbose-logs-visual-studio-startup-verbose-logs"></a><a id="visual-studio-startup-verbose-logs" name="visual-studio-startup-verbose-logs" />Inicio de Visual Studio, registros detallados

```csharp
devenv.exe /log "%USERPROFILE%\Desktop\VisualStudio.log"
```

Referencia: [/log (devenv. exe)](https://msdn.microsoft.com/library/ms241272.aspx)

### <a name="a-idmac-logs-namemac-logs-mac-logs"></a><a id="mac-logs" name="mac-logs" />Registros de Mac

Puede seleccionar el elemento de menú **ir > carpeta** en Finder y, a continuación, copiar y pegar cualquiera de estas rutas de acceso en el cuadro de diálogo.

#### <a name="a-idmac-logs-visual-studio-namemac-logs-visual-studio-visual-studio-for-mac"></a><a id="mac-logs-visual-studio" name="mac-logs-visual-studio" />Visual Studio para Mac

`~/Library/Logs/VisualStudio/7.0`(este número puede variar en función de la versión que esté usando)

Esta carpeta también se puede abrir mediante "Help-> abrir el directorio de registro".

#### <a name="a-idmac-logs-xamarin-studio-namemac-logs-xamarin-studio-xamarin-studio"></a><a id="mac-logs-xamarin-studio" name="mac-logs-xamarin-studio" />Xamarin Studio

`~/Library/Logs/XamarinStudio-6.0`(este número puede variar en función de la versión que esté usando)

Esta carpeta también se puede abrir mediante "Help-> abrir el directorio de registro".

#### <a name="a-idmac-universal-installer-namemac-universal-installer-xamarin-universal-installer"></a><a id="mac-universal-installer" name="mac-universal-installer" />Instalador "universal" de Xamarin

`~/Library/Logs/XamarinInstaller/Universal`

Estos son los registros del `XamarinInstaller.dmg` instalador.

#### <a name="a-idmac-build-host-namemac-build-host-xamarin-build-host"></a><a id="mac-build-host" name="mac-build-host" />Host de compilación de Xamarin

`~/Library/Logs/Xamarin-[MAJOR.MINOR]`

## <a name="a-idverbose-build-output-logs-nameverbose-build-output-logs-verbose-build-output"></a><a id="verbose-build-output-logs" name="verbose-build-output-logs" />Resultados de compilación detallados

1. Habilite la [salida de MSBuild de diagnóstico](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output).

2. En el caso de las aplicaciones iOS, habilite también la **salida Mtouch detallada** agregando `-v -v -v -v` en **las propiedades del proyecto > compilación de iOS > General (pestaña) > opciones adicionales > argumentos adicionales de Mtouch**.

3. Limpie y recompile el proyecto.

4. Copie y pegue el resultado de la compilación del IDE en un archivo de texto.
     - Visual Studio (Windows): **Ver > salida > Mostrar salida de: Versión**
     - Visual Studio para Mac: **Ver paneles de > > errores > resultado de la compilación (pestaña)**

## <a name="a-iddebug-logs-for-xamarin-apps-namedebug-logs-for-xamarin-apps-debug-logs-for-xamarinandroid-and-xamarinios-apps"></a><a id="debug-logs-for-xamarin-apps" name="debug-logs-for-xamarin-apps" />Depuración de registros para aplicaciones de Xamarin. Android y Xamarin. iOS

### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Ver paneles de > > salida de la aplicación**

(Tenga en cuenta que este elemento de menú solo aparecerá una vez que se haya iniciado la aplicación).

### <a name="visual-studio"></a>Visual Studio

**Ver > salida > Mostrar salida de: Depura**

### <a name="a-idadb-logcat-nameadb-logcat-android-adbhttpsdeveloperandroidcomtoolshelpadbhtml-logcat-logs"></a><a id="adb-logcat" name="adb-logcat" />Registros [`adb`](https://developer.android.com/tools/help/adb.html) de logcat de Android

Después de ejecutar `adb` el comando, vuelva a adjuntar el archivo **android_logcat. txt** desde el escritorio. En estas instrucciones se supone que solo tiene un dispositivo conectado.

Vea también la página de registro de depuración de [Android](~/android/deploy-test/debugging/android-debug-log.md) .

#### <a name="visual-studio"></a>Visual Studio

1. **Herramientas > Android > Inicio del símbolo del sistema de Android ADB**
2. Limpie el registro:`adb logcat -c`
3. Reproduzca el problema.
4. Genere el registro:`adb logcat -vtime -d > "%USERPROFILE%\Desktop\android_logcat.txt"`

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

1. **Herramientas > abrir Android SDK símbolo del sistema**
2. Limpie el registro:`adb logcat -c`
3. Reproduzca el problema.
4. Genere el registro:`adb logcat -vtime -d > ~/Desktop/android_logcat.txt`

### <a name="a-idios-simulator-logs-nameios-simulator-logs-ios-simulator-logs-on-mac"></a><a id="ios-simulator-logs" name="ios-simulator-logs" />registros del simulador de iOS (en Mac)

- Para acceder al registro del sistema, seleccione Depurar **> abrir registro del sistema...** en la aplicación del simulador de iOS.

- Para ver los informes de bloqueos del simulador, abra Console. app `~/Library/Logs > DiagnosticReports`y vaya a.

### <a name="a-idios-device-logs-nameios-device-logs-ios-device-logs-on-mac"></a><a id="ios-device-logs" name="ios-device-logs" />registros de dispositivos iOS (en Mac)

#### <a name="visual-studio-for-mac"></a>Visual Studio para Mac

**Ver paneles de > > Registro de dispositivo iOS**

#### <a name="xcode"></a>Xcode

**Dispositivos > de ventana > $ {DeviceName}**

Los informes de bloqueo están disponibles en el botón **Ver registros de dispositivos** . El registro del sistema para el dispositivo aparece en la parte inferior de la ventana bajo la flecha de divulgación.

#### <a name="xcode-5"></a>Xcode 5

**Windows > Organizer > dispositivos (pestaña) > $ {DeviceName}**
