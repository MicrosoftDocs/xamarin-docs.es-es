---
title: Desinstalación de Xamarin
description: En este documento se describe cómo desinstalar Xamarin de Visual Studio en Windows.
ms.prod: xamarin
ms.assetid: b83a85ec-842a-444c-8f82-c2464eda099b
author: conceptdev
ms.author: crdun
ms.date: 01/22/2020
ms.openlocfilehash: 4c9096edddeb00070aaabc3e93b283f2d55c1bfa
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "79303646"
---
# <a name="uninstall-xamarin-from-visual-studio"></a>Desinstalación de Xamarin de Visual Studio

En esta guía se explica cómo quitar Xamarin de Visual Studio en Windows.

<a name="uninstallvs2017" />

## <a name="visual-studio-2019-and-visual-studio-2017"></a>Visual Studio 2019 y Visual Studio 2017

Xamarin se desinstala de Visual Studio 2019 y Visual Studio 2017 mediante la aplicación del instalador:

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

<a name="uninstallvs2015"></a>

## <a name="visual-studio-2015-and-earlier"></a>Visual Studio 2015 y anterior

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
