---
title: ¿Cómo realizo una desinstalación exhaustiva de Xamarin para Visual Studio?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: c1742239-05ea-449d-9c99-611e5e5a90e4
author: conceptdev
ms.author: crdun
ms.date: 12/02/2016
ms.openlocfilehash: 4d8396574f02cde4c75fd96c98f79d5ed4080293
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291072"
---
# <a name="how-do-i-perform-a-thorough-uninstall-for-xamarin-for-visual-studio"></a>¿Cómo realizo una desinstalación exhaustiva de Xamarin para Visual Studio?


1. Desde el Panel de control de Windows, desinstale cualquiera de los siguientes elementos que aparezcan:

    - Xamarin
    - Xamarin para Windows
    - Xamarin.Android
    - Xamarin.iOS
    - Xamarin para Visual Studio

2. En el Explorador, elimine los archivos restantes de las carpetas de extensión de Xamarin Visual Studio (todas las versiones, incluidas _Archivos de programa_ y _Archivos de programa (x86)_ ):

    _C:\\Archivos de programa\*\\Microsoft Visual Studio 1\*.0\\Common7\\IDE\\Extensions\\Xamarin_

3. Elimine también el directorio de caché de componentes MEF de Visual Studio:

    _%LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*.0\\ComponentModelCache_

    De hecho, este paso por sí mismo a menudo es suficiente para resolver errores como:

    - "El paquete 'XamarinShellPackage' no se cargó correctamente"

    - "No se puede abrir el archivo de proyecto... Falta un subtipo de proyecto"

    - "Referencia a objeto no establecida como instancia de un objeto.  en Xamarin.VisualStudio.IOS.XamarinIOSPackage.Initialize()"

    - "No se pudo empaquetar SetSite" (en _ActivityLog.xml_ de Visual Studio)

    - "No se pudo empaquetar LegacySitePackage" (en _ActivityLog.xml_ de Visual Studio)

    (Consulte también la extensión de Visual Studio [Borrar la caché de componentes de MEF](https://visualstudiogallery.msdn.microsoft.com/22b94661-70c7-4a93-9ca3-8b6dd45f47cd).  Y consulte [Error 40781, comentario 19](https://bugzilla.xamarin.com/show_bug.cgi?id=40781#c19) para obtener más información sobre el problema de nivel superior en Visual Studio que puede provocar estos errores).

4. Compruebe también el directorio _VirtualStore_ para ver si es posible que Windows haya almacenado allí algún archivo de superposición para los directorios _Extensions\\Xamarin_ o _ComponentModelCache_:

    _%LOCALAPPDATA%\\VirtualStore_

5. Abra el Editor del Registro (`regedit`).

6. Busque la siguiente clave:

    _HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Wow6432Node\\Microsoft\\Windows\\CurrentVersion\\SharedDlls_

7. Busque y elimine las entradas que coincidan con este patrón:

    _C:\\Archivos de programa\*\\Microsoft Visual Studio 1\*.0\\Common7\\IDE\\Extensions\\Xamarin_

8. Busque esta clave:

    _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*.0\\ExtensionManager\\PendingDeletions_

9. Elimine las entradas que parezca que puedan estar relacionadas con Xamarin.  Por ejemplo, esta es la que solía producir problemas en versiones anteriores de Xamarin:

    _Mono.VisualStudio.Shell,1.0_

10. Abra un símbolo de sistema de administrador `cmd.exe` y, después, ejecute los comandos `devenv /setup` y `devenv /updateconfiguration` para cada versión instalada de Visual Studio.  Por ejemplo, para Visual Studio 2015:

    ```
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /setup
    "%ProgramFiles(x86)%\Microsoft Visual Studio 14.0\Common7\IDE\devenv.exe" /updateconfiguration
    ```

11. Reinicie el equipo.

12. Vuelva a instalar la versión estable actual de Xamarin mediante desde [VisualStudio.com](https://visualstudio.com/xamarin/).

## <a name="additional-troubleshooting-steps-for-package-did-not-load-correctly"></a>Pasos adicionales para la solución de problemas para "El paquete no se cargó correctamente"

Si los pasos anteriores no resuelven el error "El paquete no se cargó correctamente", presentamos algunos pasos más que puede probar.

1. Cree una nueva cuenta de usuario de Windows.

2. Compruebe si las extensiones de Xamarin Visual Studio se cargan sin error para el nuevo usuario.

3. Si las extensiones se cargan correctamente, es muy probable que el problema se deba a algunos de los valores almacenados para el usuario original:

    - **En el Explorador**: _%LOCALAPPDATA%\\Microsoft\\VisualStudio\\1\*.0_
    - **En regedit**: _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*.0_
    - **En regedit**: _HKEY\_CURRENT\_USER\\Software\\Microsoft\\VisualStudio\\1\*.0\_Config_

4. Si esos valores de configuración almacenados realmente parecen ser el problema, puede probar a crear una copia de seguridad y, después, eliminarlos.
