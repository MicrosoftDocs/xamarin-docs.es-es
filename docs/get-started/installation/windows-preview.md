---
title: Instalación de la versión preliminar de Xamarin en Windows
description: En este documento se describe cómo instalar una versión preliminar de Xamarin en Visual Studio 2019 mediante el canal de versión preliminar.
ms.prod: xamarin
ms.assetid: 9F730444-06E8-4B3F-8A19-CA95CD484FFA
author: conceptdev
ms.author: crdun
ms.date: 03/20/2018
ms.openlocfilehash: 805edf0aba0e45a631bb9e3f7536da3f18dc789d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70277654"
---
# <a name="installing-xamarin-preview-on-windows"></a>Instalación de la versión preliminar de Xamarin en Windows

Visual Studio 2019 y Visual Studio 2017 no admiten canales alfa, beta y estable de la misma manera que las versiones anteriores. En su lugar, hay solo dos opciones:

- **Lanzamiento**: equivalente al canal _estable_ en Visual Studio para Mac
- **Versión preliminar**: equivalente a los canales _alfa_ y _beta_ en Visual Studio para Mac

> [!TIP]
> Para probar las características de versión preliminar, debe [descargar el instalador de Visual Studio Preview](https://visualstudio.microsoft.com/vs/preview/), que ofrece la opción de instalar versiones **preliminares** de Visual Studio en paralelo con la versión estable (versión). Puede encontrar más información sobre las novedades de Visual Studio 2019 en las notas de la [versión](https://docs.microsoft.com/visualstudio/releases/2019/release-notes).

La versión preliminar de Visual Studio puede contener las versiones preliminares correspondientes de la funcionalidad de Xamarin, incluidas:

- Xamarin.Forms
- Xamarin.iOS
- Xamarin.Android
- Generador de perfiles de Xamarin
- Xamarin Inspector
- Simulador iOS remoto de Xamarin

La captura de pantalla del **instalador de versión preliminar** siguiente muestra las opciones de versión preliminar y lanzamiento (tenga en cuenta los números de versión en gris: la versión 15.0 es de lanzamiento y la versión 15.1 es una versión preliminar):

![instalador que muestra las opciones de versión preliminar](windows-images/vs2017-installer.jpg)

Durante el proceso de instalación, se puede aplicar un **alias de instalación** a la instalación en paralelo (por lo que pueden distinguirse en el menú Inicio), tal y como se muestra a continuación:

[![editar alias antes de instalar](windows-images/vs2017-nickname-sml.png "edit nickname before installing")](windows-images/vs2017-nickname.png#lightbox)

### <a name="uninstalling-visual-studio-2019-preview"></a>Desinstalación de Visual Studio 2019 Preview

También se debe usar el **instalador de Visual Studio** para desinstalar versiones preliminares de Visual Studio 2019. Lea la [guía de desinstalación de Xamarin](uninstalling-xamarin.md#uninstallvs2017) para más información.
