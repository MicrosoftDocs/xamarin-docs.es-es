---
title: Instalación y requisitos de los libros
description: En este documento se describe cómo descargar e instalar Xamarin Workbooks, y se describen las plataformas admitidas y los requisitos del sistema.
ms.prod: xamarin
ms.assetid: 9D4E10E8-A288-4C6C-9475-02969198C119
author: conceptdev
ms.author: crdun
ms.date: 06/19/2018
ms.openlocfilehash: 6fc204e8524d53820407b2efd3ab0de6af28c669
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "71249909"
---
# <a name="workbooks-installation-and-requirements"></a>Instalación y requisitos de los libros

<a name="install" />

## <a name="download-and-install"></a>Descargar e instalar

<!-- markdownlint-disable MD001 -->

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

1. Compruebe los [requisitos](#requirements) siguientes.
2. Descargue e instale [Xamarin Workbooks para Windows](https://dl.xamarin.com/interactive/XamarinInteractive.msi).
3. Comience a [reproducirse](~/tools/workbooks/workbook.md) con libros.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

1. Compruebe los [requisitos](#requirements) siguientes.
2. Descargue e instale [Xamarin Workbooks para Mac](https://dl.xamarin.com/interactive/XamarinInteractive.pkg).
3. Empiece a [reproducir](~/tools/workbooks/workbook.md).

-----

## <a name="requirements"></a>Requisitos

#### <a name="supported-operating-systems"></a>Sistemas operativos admitidos

- **Mac** -OS X 10,11 o superior
- **Windows** -Windows 7 o posterior (con Internet Explorer 11 o posterior y .net 4.6.1 o superior)

#### <a name="supported-app-platforms"></a>Plataformas de aplicaciones admitidas

|Plataforma de aplicaciones|Compatibilidad con sistemas operativos|Notas|
|--- |--- |--- |
|Mac|Solo se admite en Mac|
|iOS|Compatible con Mac y Windows|Xamarin. iOS 11,0 y Xcode 9,0 o posterior deben instalarse en Mac. La ejecución de libros de iOS en Windows requiere un host de compilación de Mac que ejecute todo lo anterior y el [simulador de iOS remoto](~/tools/ios-simulator/index.md) instalado en Windows.|
|Android|Compatible con Mac y Windows|Debe usar el emulador de Google, Visual Studio o Xamarin Android con un dispositivo virtual > = 5,0|
|WPF|Solo se admite en Windows|
|Consola (.NET Framework)|Compatible con Mac y Windows|
|Consola (.NET Core)|Compatible con Mac y Windows|

## <a name="reporting-bugs"></a>Informes de errores

[Notifique los problemas en github][bugs]e incluya toda la información siguiente:

### <a name="log-files"></a>Archivos de registro

Adjuntar siempre los archivos de registro de cliente de los libros:

- Macintosh`~/Library/Logs/Xamarin/Workbooks/Xamarin Workbooks {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Workbooks\logs\Xamarin Workbooks {date}.log`

1.4. x también ofrece la posibilidad de seleccionar el archivo de registro en Finder (macOS) o en el explorador (Windows) directamente desde el menú principal:

- **Ayuda > Mostrar archivo de registro**

#### <a name="log-paths-for-workbooks-13-and-earlier"></a>Rutas de acceso de registro para los libros 1,3 y anteriores:

- Macintosh`~/Library/Logs/Xamarin/Inspector/Xamarin Inspector {date}.log`
- Windows: `%LOCALAPPDATA%\Xamarin\Inspector\logs\Xamarin Inspector {date}.log`

### <a name="platform-version-information"></a>Información de versión de la plataforma

Resulta muy útil conocer los detalles sobre el sistema operativo y los productos de Xamarin instalados.

En el menú principal de los libros:

- **Ayuda > copiar información de versión**

#### <a name="instructions-for-workbooks-13-and-earlier"></a>Instrucciones para los libros 1,3 y anteriores:

Visual Studio para Mac

- **Visual Studio > Acerca de Visual Studio > Mostrar detalles > copiar información**
- Pegar en informe de errores

Programa para la mejora

- **Ayuda > Acerca de Visual Studio > copiar información**
- Háganoslo saber la versión del sistema operativo y si está ejecutando Windows de 32 bits o de 64 bits.

### <a name="samples"></a>Muestras

Si puede adjuntar o vincular al archivo **. Workbooks** con el que tiene problemas, puede que le resulte más fácil resolver el error.

### <a name="devices"></a>Dispositivos

Si tiene problemas para conectar su libro de iOS o Android y ya ha comprobado [nuestra página de solución de problemas](~/tools/workbooks/troubleshooting/index.md), deberá saber:

- Nombre del dispositivo al que está intentando conectarse
- Versión del sistema operativo del dispositivo
- Android: Comprobar que se usa un emulador x86
- Android: ¿Qué plataforma de emulador usa? ¿Es el emulador de Google?
  ¿Android Emulator de Visual Studio? ¿Xamarin Android Player?
- iOS en Windows: ¿Qué versión del simulador de iOS remoto de Xamarin tiene instalado (Active **Agregar o quitar programas** en el **Panel de control**)?
- iOS en Windows: Proporcione también información de versión de la plataforma para el host de compilación de Mac.
- ¿El dispositivo tiene conectividad de red (consulte a través del explorador Web)?

[bugs]: https://github.com/Microsoft/workbooks/issues/new

## <a name="uninstall"></a>Desinstalar

### <a name="windows"></a>Windows

En función de cómo haya adquirido los libros, es posible que tenga que realizar dos procedimientos de desinstalación. Consulte ambas opciones para desinstalar completamente el software.

#### <a name="visual-studio-installer"></a>Instalador de Visual Studio

Si tiene Visual Studio 2017, Abra **instalador de Visual Studio**y busque **Xamarin Workbooks**en **los componentes individuales** . Si está activada, desactive esta casilla y, a continuación, haga clic en **modificar** para desinstalar.

#### <a name="system-uninstall"></a>Desinstalación del sistema

Si instaló libros con un instalador descargado, tendrá que desinstalarlo a través de la página de configuración del sistema de **aplicaciones & características** de Windows 10 o a través de **Agregar o quitar programas** en el panel de control de versiones anteriores de Windows.

> **Iniciar > Configuración > características & de System > apps**

![](install-images/windows-remove.png "Xamarin Workbooks como se muestra &quot;en &amp; características de aplicaciones&quot;")

**Siga el procedimiento del Instalador de Visual Studio para asegurarse de que los libros no se vuelvan a instalar sin su conocimiento.**

<a name="uninstall-macos" />

### <a name="macos"></a>macOS

A partir de [1.2.2](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/interactive/interactive-1.2.md), Xamarin Workbooks se puede desinstalar de un terminal mediante la ejecución de:

```bash
sudo /Library/Frameworks/Xamarin.Interactive.framework/Versions/Current/uninstall
```

El desinstalador detallará los archivos y directorios que se quitarán y pedirá confirmación antes de continuar.

Pase el `-help` argumento `uninstall` al script para escenarios más avanzados.

En el caso de versiones anteriores, debe quitar manualmente lo siguiente:

1. Eliminar la aplicación Workbooks de `"/Applications/Xamarin Workbooks.app"`
2. Eliminar la aplicación Inspector de `"Applications/Xamarin Inspector.app"`
3. Eliminar los complementos: `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Interactive"` y `"~/Library/Application Support/XamarinStudio-6.0/LocalInstall/Addins/Xamarin.Inspector"`
4. Eliminar Inspector y los archivos auxiliares aquí: `/Library/Frameworks/Xamarin.Interactive.framework` y `/Library/Frameworks/Xamarin.Inspector.framework`

## <a name="downgrading"></a>Degradar

El identificador de paquete para los **libros de/Applications/Xamarin.** la aplicación `com.xamarin.Inspector` cambió `com.xamarin.Workbooks` de a en la versión 1,4, ya que los libros y el inspector se han dividido por completo.

Debido a un error en instaladores más antiguos, no es posible degradar las versiones 1,4 o más recientes con los instaladores de 1.3.2 o anteriores.

Para cambiar de 1,4 o posterior a 1.3.2 o anterior:

1. [Desinstalar libros & inspector manualmente](#uninstall-macos)
2. Ejecutar el instalador 1.3.2 o `.pkg` anterior
