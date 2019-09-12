---
title: Instalación de NUnit 2.6.4 mediante NuGet
description: En este documento se describe cómo degradar NUnit 3.0 a NUnit 2.6.4 mediante NuGet. Esto es necesario cuando se trabaja con Xamarin.UITest, ya que no admite NUnit 3.x.
ms.prod: xamarin
ms.assetid: 7683F2B8-7FDF-48C4-8E7D-649D4D4E79F0
author: conceptdev
ms.author: crdun
ms.date: 03/24/2017
ms.openlocfilehash: 2437a26ae0f9187d3042ef6e6064ddafa808804c
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766013"
---
# <a name="installing-nunit-264-using-nuget"></a>Instalación de NUnit 2.6.4 mediante NuGet

_En esta guía se explica cómo degradar NUnit 3.0 a NUnit 2.6.4 mediante NuGet._

Los desarrolladores que escriben pruebas en Visual Studio para Mac o usan Xamarin.UITest deben usar [NUnit 2.6.4](http://nunit.org/index.php?p=docHome&r=2.6.4), debido a que NUnit 3.0 (o una versión superior) no es compatible con Visual Studio para Mac ni Xamarin.UITest. Se producirá un error si se intenta ejecutar pruebas unitarias en Visual Studio para Mac o Xamarin.UITests con NUnit 3.0.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

En esta guía se describirá cómo instalar NUnit 2.6.4 mediante NuGet para Visual Studio para Mac. Con estos pasos también se podrá desinstalar NUnit 3.0, en caso de que sea necesario.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

En esta guía se describirá cómo degradar NUnit 3.0 a NUnit 2.6.4 mediante NuGet en Visual Studio 2015 o una versión posterior.

-----

## <a name="requirements"></a>Requisitos

En esta guía se da por hecho que hay una solución existente con un proyecto de aplicación móvil y un proyecto de prueba.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

## <a name="installing-nunit-264-in-visual-studio-for-mac"></a>Instalación de NUnit 2.6.4 en Visual Studio para Mac

Los pasos siguientes describen cómo instalar NUnit 2.6.4.

1. **Abra el Administrador de paquetes**, haga clic con el botón derecho en **Paquetes** y seleccione **Agregar paquetes** en el menú desplegable:

    [![](installing-nunit-using-nuget-images/add-packages-xs.png "Haga clic con el botón derecho en Paquetes y seleccione Agregar paquetes en el menú desplegable")](installing-nunit-using-nuget-images/add-packages-xs.png#lightbox)
    
1. **Busque `NUnit version:2.6.4`** . Si es necesario, Visual Studio para Mac desinstalará NUnit 3.0 y, luego, descargará e instalará NUnit 2.6.4. En el cuadro de diálogo **Agregar paquetes**, escriba el texto `nunit version:2.6.4` en el campo **Buscar** que se encuentra en la esquina superior derecha. Seleccione **NUnit** en los resultados de la búsqueda y haga clic en el botón **Agregar paquete**:

    [![](installing-nunit-using-nuget-images/nunit-search-xs.png "Seleccione NUnit en los resultados de la búsqueda y haga clic en el botón Agregar paquete")](installing-nunit-using-nuget-images/nunit-search-xs.png#lightbox)

Para confirmar que se instaló NUnit 2.6.4, revise el número de versión del paquete de NUnit en el Panel de solución:

[![](installing-nunit-using-nuget-images/nunit-2-6-4-installed.png "Inspeccione el número de versión del paquete NUnit en el Panel de solución")](installing-nunit-using-nuget-images/nunit-2-6-4-installed.png#lightbox)

## <a name="summary"></a>Resumen

En esta guía se ha descrito cómo cambiar NUnit 3.0 a la versión anterior NUnit 2.6.4 en Visual Studio para Mac mediante la Consola del Administrador de paquetes.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="installing-nunit-264-in-visual-studio"></a>Instalación de NUnit 2.6.4 en Visual Studio

Esta sección se centrará en el uso de la _Consola del Administrador de paquetes NuGet_ en Visual Studio 2015 o versiones posteriores para desinstalar NUnit 3.0 e instalar NUnit 2.6.4.

1. **Inicie la Consola del Administrador de paquetes NuGet**. Seleccione **Herramientas > Administrador de paquetes NuGet > Consola del Administrador de paquetes**:

    [![](installing-nunit-using-nuget-images/package-manager-console.png "Inicie la Consola del Administrador de paquetes NuGet. Seleccione Herramientas > Administrador de paquetes NuGet > Consola del Administrador de paquetes")](installing-nunit-using-nuget-images/package-manager-console.png#lightbox)
    
1. **Compruebe la versión de NUnit**. Si quiere, puede comprobar la versión instalada de NUnit con el comando `Get-Package -Project <UITEST PROJECT>`:

    ```bash
    [PM] Get-Package -Project [TEST PROJECT NAME]
    
        Id                                  Versions                                 ProjectName
        --                                  --------                                 -----------
    NUnit                               {3.0.1}                                  [TEST PROJECT NAME]
    Xamarin.UITest                      {1.2.0}                                  [TEST PROJECT NAME]
    ```

Si la versión es NUnit 3.0 o superior, debe degradarla a la versión NUnit 2.6.4.

1. **Desinstale NUnit 3.0**. Use el cmdlet `Uninstall-Package` para desinstalar NUnit 3.0:

    ```
    <PM> Uninstall-Package NUnit -Project <TEST PROJECT NAME>
    Attempting to gather dependencies information for package 'NUnit.3.0.1' with respect to project '<TEST PROJECT NAME>', targeting '.NETFramework,Version=v4.5'
    Resolving actions to uninstall package 'NUnit.3.0.1'
    Resolved actions to uninstall package 'NUnit.3.0.1'
    Removed package 'NUnit.3.0.1' from 'packages.config'
    Successfully uninstalled 'NUnit.3.0.1' from <TEST PROJECT NAME>
    ```

1. **Instale NUnit 2.6.4**. Instale Nunit 2.6.4 con el cmdlet `Install-Package`, tal como se muestra en el siguiente fragmento de código:

    ```
    <PM> Install-Package NUnit -Version 2.6.4 -Project <TEST PROJECT NAME>
    Attempting to gather dependencies information for package 'NUnit.2.6.4' with respect to project '<TEST PROJECT NAME>', targeting '.NETFramework,Version=v4.5'
    Attempting to resolve dependencies for package 'NUnit.2.6.4' with DependencyBehavior 'Lowest'
    Resolving actions to install package 'NUnit.2.6.4'
    Resolved actions to install package 'NUnit.2.6.4'
    Adding package 'NUnit.2.6.4' to folder 'Z:\Desktop\DowngradeNunit\packages'
    Added package 'NUnit.2.6.4' to folder 'Z:\Desktop\DowngradeNunit\packages'
    Added package 'NUnit.2.6.4' to 'packages.config'
    Successfully installed 'NUnit 2.6.4' to <TEST PROJECT NAME>
    ```

## <a name="summary"></a>Resumen

En esta guía se ha descrito cómo cambiar NUnit 3.0 a la versión anterior NUnit 2.6.4 en Visual Studio 2015 o versiones posteriores mediante la Consola del Administrador de paquetes.

-----

## <a name="related-links"></a>Vínculos relacionados

- [NUnit 2.6.4](http://nunit.org/index.php?p=docHome&r=2.6.4)
- [Paquete NuGet de NUnit 2.6.4](https://www.nuget.org/packages/NUnit/2.6.4)
