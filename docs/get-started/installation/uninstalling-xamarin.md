---
title: Desinstalación de Xamarin
description: En este documento se describe cómo desinstalar Xamarin en Mac y Windows. Además, se proporcionan instrucciones específicas sobre la desinstalación de Mono, Xamarin.Android, Xamarin.iOS y otras herramientas.
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: conceptdev
ms.author: crdun
ms.date: 04/08/2017
ms.openlocfilehash: fa7d5fc8d33cd7ed3a347209aaff2610e6d319dc
ms.sourcegitcommit: 5ddb107b0a56bef8a16fce5bc6846f9673b3b22e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/31/2019
ms.locfileid: "75558825"
---
# <a name="uninstall-xamarin"></a>Desinstalación de Xamarin

En esta guía se explica cómo quitar Xamarin de macOS o Visual Studio en Windows.

Si es necesario volver a instalar Xamarin con el instalador universal, se recomienda reiniciar siempre el equipo en primer lugar.

## <a name="uninstalling-xamarin-on-macos"></a>Desinstalación de Xamarin en macOS

Esta guía puede usarse para desinstalar cada producto de manera individual dirigiéndose a la sección correspondiente. El conjunto de aplicaciones completo de Xamarin, que incluye los productos que se enumeran a continuación, puede desinstalarse siguiendo esta guía exhaustiva:

- [Mono](#uninstallmono)
- [Xamarin.Android](#uninstallandroid)
- [Xamarin.iOS](#uninstallios)
- [Xamarin.Mac](#uninstallmac)
- [Libros](#uninstallworkbooks)
- [Xamarin Profiler](#uninstallprofiler)
- [Instalador](#uninstallinstaller)

> [!TIP]
> Le hemos proporcionado un [script de desinstalación](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh) que puede usar al quitar Xamarin de su equipo macOS. Para obtener más información sobre cómo usar dicho script, consulte la sección [Uso del script de desinstalación](#uninstallscript) de esta guía.

### <a name="uninstalling-visual-studio-for-mac"></a>Desinstalación de Visual Studio para Mac

El primer paso a la hora de desinstalar Xamarin de un equipo Mac es buscar **Visual Studio.app** en el directorio **/Applications** y arrastrarlo a la **Papelera**. También puede hacer clic con el botón derecho y seleccionar **Trasladar a la Papelera**, como se muestra aquí:

![Traslado de la aplicación Visual Studio a la Papelera](uninstalling-xamarin-images/uninstall-image1.png)

Al eliminar este lote de aplicaciones, se quita Visual Studio para Mac, aunque puede haber otros archivos relacionados con Xamarin en un sistema de archivos.

Para quitar todas las trazas de Visual Studio para Mac, ejecute los siguientes comandos en Terminal:

```bash
sudo rm -rf "/Applications/Visual Studio.app"
rm -rf ~/Library/Caches/VisualStudio
rm -rf ~/Library/Preferences/VisualStudio
rm -rf ~/Library/Preferences/Visual\ Studio
rm -rf ~/Library/Logs/VisualStudio
rm -rf ~/Library/VisualStudio
rm -rf ~/Library/Preferences/Xamarin/
rm -rf ~/Library/Developer/Xamarin
rm -rf ~/Library/Application\ Support/VisualStudio
rm -rf ~/Library/Application\ Support/VisualStudio/7.0/LocalInstall/Addins/
```

> [!NOTE]
> Para obtener información sobre cómo desinstalar Visual Studio para Mac, consulte el la guía para [Desinstalar](https://docs.microsoft.com/visualstudio/mac/uninstall) en docs.microsoft.com

<a name="uninstallmono" />

### <a name="uninstall-mono-sdk-mdk"></a>Desinstalación del SDK de Mono (MDK)

Mono es una implementación de código abierto de .NET Framework que usan todos los productos de Xamarin (Xamarin.iOS, Xamarin.Android y Xamarin.Mac) para permitir el desarrollo de estas plataformas en C#.

> [!WARNING]
> Existen otras aplicaciones fuera de Xamarin que también usan Mono, como Unity. Asegúrese de que no hay otras dependencias en Mono antes de desinstalarlo.

Para quitar Mono Framework, ejecute los siguientes comandos en Terminal:

```bash
sudo rm -rf /Library/Frameworks/Mono.framework
sudo pkgutil --forget com.xamarin.mono-MDK.pkg
sudo rm /etc/paths.d/mono-commands
```

<a name="uninstallandroid" />

### <a name="uninstall-xamarinandroid"></a>Desinstalación de Xamarin.Android

Hay ciertos elementos que son necesarios a la hora de utilizar Xamarin.Android, como Android SDK y Java SDK, que deben quitarse al desinstalar Xamarin.Android. En esta sección encontrará los pasos para desinstalar todos los elementos pertinentes.

Para quitar Xamarin.Android, ejecute los siguientes comandos en Terminal:

```bash
sudo rm -rf /Developer/MonoDroid
rm -rf ~/Library/MonoAndroid
sudo pkgutil --forget com.xamarin.android.pkg
sudo rm -rf /Library/Frameworks/Xamarin.Android.framework
```

#### <a name="uninstall-android-sdk-and-java-sdk"></a>Desinstalación de Android SDK y del SDK de Java

Android SDK es necesario para la implementación de aplicaciones de Android. Para quitar completamente todos los elementos de Android SDK, busque el archivo en **~/Library/Developer/Xamarin/** y muévalo a la **Papelera**.

El SDK de Java (JDK) no necesita desinstalarse, ya que se ha empaquetado previamente como parte de Mac OS X.

#### <a name="uninstall-android-avd"></a>Desinstalación de AVD

> [!WARNING]
> Hay otras aplicaciones fuera de Visual Studio para Mac que también usan AVD y estos componentes adicionales de Android, como Android Studio.
> La eliminación de este directorio puede hacer que los proyectos se interrumpan en Android Studio. 

Para quitar cualquier AVD y otros componentes de Android, use el comando siguiente:

```bash
rm -rf ~/.android
```

Para quitar solo los AVD de Android, use el comando siguiente:

```bash
rm -rf ~/.android/avd
```

<a name="uninstallios" />

### <a name="uninstall-xamarinios"></a>Desinstalación de Xamarin.iOS

Xamarin.iOS permite el desarrollo de aplicaciones de iOS con C# o F#. Para desinstalar Xamarin.iOS de un equipo, siga los pasos siguientes:

Para quitar todos los archivos de Xamarin.iOS, use los siguientes comandos en Terminal:

```bash
rm -rf ~/Library/MonoTouch
sudo rm -rf /Library/Frameworks/Xamarin.iOS.framework
sudo rm -rf /Developer/MonoTouch
sudo pkgutil --forget com.xamarin.monotouch.pkg
sudo pkgutil --forget com.xamarin.xamarin-ios-build-host.pkg
sudo pkgutil --forget com.xamarin.xamarin.ios.pkg
```

<a name="uninstallmac" />

### <a name="uninstall-xamarinmac"></a>Desinstalación de Xamarin.Mac

Xamarin.Mac se puede quitar del equipo con los siguientes dos comandos para erradicar el producto y la licencia del equipo Mac, respectivamente:

```bash
sudo rm -rf /Library/Frameworks/Xamarin.Mac.framework
rm -rf ~/Library/Xamarin.Mac
```

<a name="uninstallworkbooks" />

### <a name="uninstall-workbooks"></a>Desinstalación de Workbooks

Para quitar la versión 1.2.2 (y versiones posteriores) de Xamarin Workbooks, use los siguientes comandos en Terminal:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

Para versiones anteriores, consulte la guía para desinstalar [Workbooks](~/tools/workbooks/install.md#uninstall-macos).

<a name="uninstallprofiler" />

### <a name="uninstall-the-xamarin-profiler"></a>Desinstalar Xamarin Profiler

Para quitar los archivos de Xamarin Profiler, use los siguientes comandos en Terminal:

```bash
sudo rm -rf "/Applications/Xamarin Profiler.app"
```

<a name="uninstallinstaller" />

### <a name="uninstall-the-xamarin-installer"></a>Desinstalación del instalador de Xamarin

Para quitar todas las trazas del instalador universal de Xamarin, use los siguientes comandos en Terminal:

```bash
rm -rf ~/Library/Caches/XamarinInstaller/
rm -rf ~/Library/Caches/VisualStudioInstaller/
rm -rf ~/Library/Logs/XamarinInstaller/
rm -rf ~/Library/Logs/VisualStudioInstaller/
rm -rf ~/Library/Preferences/Xamarin/
rm -rf "~/Library/Preferences/Visual Studio/"
```

<a name="uninstallscript" />

### <a name="using-the-uninstall-script"></a>Usar el script de desinstalación

> [!TIP]
> Antes de ejecutar cualquier script que descargue, lea los comandos para comprender los cambios que se realizan en el equipo.

[Este script de desinstalación](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh) permite desinstalar Visual Studio para Mac y sus componentes de Xamarin asociados en un solo go.

Este script contiene la mayoría de los comandos que figuran en el artículo. Hay dos omisiones principales del script y no se incluyen debido a posibles dependencias externas (puede que otras aplicaciones usen mono o las herramientas de Android):

- Desinstalación de Mono
- Desinstalación de AVD de Android

Para ejecutar el script:

1. Haga clic con el botón derecho en [este vínculo a **Uninstall-vsmac.sh** ](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh) y guarde o descargue el archivo en el equipo Mac.

2. Abra **Terminal** y cambie el directorio de trabajo al lugar donde se haya descargado el script:

    ```bash
    cd /location/of/file
    ```

3. Haga que el script sea ejecutable y, después, ejecútelo con **sudo**:

    ```bash
    chmod +x ./uninstall-vsmac.sh
    sudo ./uninstall-vsmac.sh
    ```

4. Elimine el script de desinstalación.

Ahora se deben desinstalar las herramientas de Visual Studio para Mac y Xamarin del equipo.

<a name="uninstallwindows" />

## <a name="uninstalling-xamarin-on-windows"></a>Desinstalación de Xamarin en un equipo Windows

Xamarin se ha admitido en estos IDE:

- [Visual Studio 2019 y Visual Studio 2017](#uninstallvs2017)
- [Visual Studio 2015](#uninstallvs2015)
- [Visual Studio 2013](#uninstallvs2015) [**no admitido**]
- [Xamarin Studio](#uninstallxamarinstudio) [**no admitido**]

<a name="uninstallvs2017" />

### <a name="visual-studio-2019-and-visual-studio-2017"></a>Visual Studio 2019 y Visual Studio 2017

Xamarin se desinstala de Visual Studio 2019 y Visual Studio 2017 mediante la aplicación del instalador:

1. Use el **menú Inicio** para abrir el **instalador de Visual Studio**.

2. Pulse el botón **Modificar** para la instancia que quiera cambiar.

    [![](uninstalling-xamarin-images/vs2017-02-sml.png "Press the modify button")](uninstalling-xamarin-images/vs2017-02.png#lightbox)

3. En la pestaña **Cargas de trabajo**, anule la selección de la opción **Desarrollo móvil con .NET** (en la sección **Dispositivos móviles y juegos**).

    [![](uninstalling-xamarin-images/vs2017-03-sml.png "Uncheck the Mobile Development workload")](uninstalling-xamarin-images/vs2017-03.png#lightbox)

4. Haga clic en el botón **Modificar** en la parte inferior derecha de la ventana.

5. El instalador quitará los componentes cuya selección se haya anulado (Visual Studio 2017 debe cerrarse antes de que el instalador pueda realizar cambios).

    [![](uninstalling-xamarin-images/vs2017-04-sml.png "Press the Modify button")](uninstalling-xamarin-images/vs2017-04.png#lightbox)

Se quiere desinstalar componentes individuales de Xamarin, como el generador de perfiles o los libros, puede cambiar a la pestaña **Componentes individuales** en el paso 3 y desactivar componentes específicos:

[![](uninstalling-xamarin-images/vs2017-components-sml.png "Uninstall individual components")](uninstalling-xamarin-images/vs2017-components.png#lightbox)

Para desinstalar completamente Visual Studio 2017, elija **Desinstalar** en el menú de tres barras que hay junto al botón **Iniciar**.

[![](uninstalling-xamarin-images/vs2017-uninstall-sml.png "Uninstall Visual Studio completely")](uninstalling-xamarin-images/vs2017-uninstall.png#lightbox)

> [!IMPORTANT]
> Si tiene dos o más instancias de Visual Studio instaladas en paralelo (SxS), como una versión de lanzamiento y una versión preliminar, es posible que al desinstalar una instancia se quiten algunas funciones de Xamarin de las otras instancias de Visual Studio, incluidas:
>
> - Generador de perfiles de Xamarin
> - Libros/Inspector de Xamarin
> - Simulador iOS remoto de Xamarin
> - SDK de Apple Bonjour
>
> Bajo ciertas condiciones, la desinstalación de una de las instancias SxS puede ocasionar la eliminación incorrecta de estas características. Esto puede degradar el rendimiento de la plataforma Xamarin en las instancias de Visual Studio que permanecen en el sistema después de desinstalar la instancia SxS.
>
>Esto se puede resolver mediante la ejecución de la opción **Reparación** en el instalador de Visual Studio, que volverá a instalar los componentes que falten.

## <a name="uninstalling-older-and-unsupported-products"></a>Desinstalación de productos anteriores y no admitidos

<a name="uninstallvs2015"></a>

### <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 y anterior

Para desinstalar Visual Studio 2015 por completo, use la [respuesta de soporte técnico que encontrará en visualstudio.com](https://visualstudio.microsoft.com/vs/support/vs2015/uninstall-visual-studio-2015/).

Xamarin puede desinstalarse de un equipo Windows mediante el **Panel de control**. Vaya a **Programas y características** o **Programas > Desinstalar un programa** como se muestra a continuación:

 [![](uninstalling-xamarin-images/image3.png "Navigate to Programs and Features or Programs  Uninstall a Program as illustrated here")](uninstalling-xamarin-images/image3.png#lightbox) 

Desde el Panel de control, desinstale cualquiera de los siguientes elementos:

- Xamarin
- Xamarin para Windows
- Xamarin.Android
- Xamarin.iOS
- Xamarin para Visual Studio

En el Explorador, elimine los archivos restantes de las carpetas de la extensión de Xamarin Visual Studio (todas las versiones, incluidas las de Archivos de programa y Archivos de programa (x86)):

``` 
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

Elimine el directorio de la caché del componente MEF de Visual Studio, que debería estar en la ubicación siguiente:

``` 
%LOCALAPPDATA%\Microsoft\VisualStudio\1*.0\ComponentModelCache
```

Consulte el directorio **VirtualStore** para ver si es posible que Windows haya almacenado allí algún archivo de superposición para los directorios **Extensions\Xamarin** o **ComponentModelCache**:

``` 
%LOCALAPPDATA%\VirtualStore
```

Abra el editor del Registro (regedit) y busque la siguiente clave:

``` 
HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\SharedDlls
```

Busque y elimine las entradas que coincidan con este patrón:

``` 
C:\Program Files*\Microsoft Visual Studio 1*.0\Common7\IDE\Extensions\Xamarin
```

Busque esta clave:

``` 
HKEY_CURRENT_USER\Software\Microsoft\VisualStudio\1*.0\ExtensionManager\PendingDeletions
```

Elimine las entradas que parezca que puedan estar relacionadas con Xamarin. Por ejemplo, todo lo que contenga los términos `mono` o `xamarin`.

Abra un símbolo de sistema de administrador cmd.exe y, después, ejecute los comandos `devenv /setup` y `devenv /updateconfiguration` para cada versión instalada de Visual Studio. Por ejemplo, para Visual Studio 2015:

```cmd
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
"%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
```

<a name="uninstallxamarinstudio"></a>

### <a name="uninstall-xamarin-studio-on-windows"></a>Desinstalación de Xamarin Studio en Windows

Xamarin Studio se desinstala de un equipo Windows mediante el **Panel de control**. Vaya a **Programas y características** o **Programas > Desinstalar un programa**. 

Para desinstalar Xamarin Studio, busque **Xamarin Studio 5.x.x** en la lista de programas y haga clic en el botón **Desinstalar**. 

### <a name="uninstall-xamarin-studio-on-mac"></a>Desinstalación de Xamarin Studio en Mac

El primer paso a la hora de desinstalar Xamarin Studio de un equipo Mac es buscar **Xamarin Studio.app** en el directorio **/Applications** y arrastrarlo a la **Papelera**. De manera alternativa, haga clic con el botón derecho y seleccione **Trasladar a la Papelera** como se muestra a continuación:

 [![](uninstalling-xamarin-images/image1.png "Alternatively, right-click and select Move to Trash as illustrated here")](uninstalling-xamarin-images/image1.png#lightbox)

Al eliminar este lote de aplicaciones se quitará Xamarin Studio, en cambio, todavía existen otros archivos relacionados con Xamarin en un sistema de archivos.

Para quitar todos los seguimientos de Xamarin Studio, deben ejecutarse los siguientes comandos en Terminal:

```bash
sudo rm -rf "/Applications/Xamarin Studio.app"
rm -rf ~/Library/Caches/XamarinStudio-*
rm -rf ~/Library/Preferences/XamarinStudio-*
rm -rf ~/Library/Logs/XamarinStudio-*
rm -rf ~/Library/XamarinStudio-*
```

## <a name="summary"></a>Resumen

En este artículo se proporcionan instrucciones para desinstalar Xamarin por completo de un equipo Mac mediante los comandos de Terminal. También se incluyen instrucciones para desinstalar Xamarin de un equipo Windows mediante la opción **Programas y características** (Visual Studio 2015 y versiones anteriores) y para usar el **Instalador de Visual Studio** para Visual Studio 2017.

## <a name="related-links"></a>Vínculos relacionados

- [Script de desinstalación (ejemplo)](https://raw.githubusercontent.com/MicrosoftDocs/visualstudio-docs/master/mac/resources/uninstall-vsmac.sh)
