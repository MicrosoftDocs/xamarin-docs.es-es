---
title: ¿Cómo se puede actualizar la versión del kit de desarrollo de Java (JDK)?
description: En este artículo se muestra cómo actualizar la versión del kit de desarrollo de Java (JDK) en Windows y Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/07/2018
ms.openlocfilehash: 193f9f92f112608908d2feb50f815a670d48b39c
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523404"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>¿Cómo se puede actualizar la versión del kit de desarrollo de Java (JDK)?

_En este artículo se muestra cómo actualizar la versión del kit de desarrollo de Java (JDK) en Windows y Mac._

## <a name="overview"></a>Información general

Xamarin. Android usa el kit de desarrollo de Java (JDK) para integrarlo con la Android SDK para compilar aplicaciones de Android y ejecutar Android Designer. Las versiones más recientes del Android SDK (API 24 y versiones posteriores) requieren JDK 8 (1,8). Como alternativa, puede instalar la [versión preliminar de Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md). Microsoft Mobile OpenJDK reemplazará finalmente JDK 8 para el desarrollo de Xamarin. Android.

Para actualizar a Microsoft Mobile OpenJDK, consulte [Microsoft Mobile OpenJDK Preview](~/android/get-started/installation/openjdk.md). Para actualizar a JDK 8, siga estos pasos:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Descargue JDK 8 (1,8) del [sitio web de Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de pantalla de la página de descarga de JDK en el sitio web de Oracle](update-jdk-images/image1.png)

2. Seleccione la versión de 64 bits para permitir la representación de [controles personalizados](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/vs/xamarin.vs_4/xamarin.vs_4.2/index.md#androiddesignercustomcontrols) en Xamarin Android Designer:

    ![Seleccionar el paquete de Windows x64 JDK que se va a descargar de la página de descarga de JDK](update-jdk-images/image2.png)

3. Ejecute el archivo. exe e instale las **herramientas de desarrollo**:

    ![Instalar las herramientas de desarrollo en el instalador de JDK](update-jdk-images/image3.png)

4. Abra Visual Studio y actualice la **Ubicación del kit de desarrollo de Java** para que apunte al nuevo JDK en **herramientas > Opciones > la configuración de Xamarin > Android > Ubicación del kit de desarrollo de Java**:

    [![Configuración de la ruta de acceso del JDK en la página de configuración de Android](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Asegúrese de reiniciar Visual Studio después de actualizar la ubicación.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Descargue JDK 8 (1,8) del [sitio web de Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de pantalla de la página de descarga de JDK en el sitio web de Oracle](update-jdk-images/image1.png)

2. Abra el archivo. dmg y ejecute el instalador. pkg:

    ![Ejecutar el instalador de JDK en macOS](update-jdk-images/image5.png)

Mac OS establecerá automáticamente la nueva versión de JDK como valor predeterminado mediante la actualización de **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. A continuación, puede comprobar de forma que la ubicación del **SDK de Java (JDK)** esté establecida en el valor predeterminado esperado de **/usr** en **Visual Studio para Mac > preferencias > proyectos > ubicaciones de SDK > ubicaciones de > de Android > la ubicación del SDK de Java (JDK)** :

[![Establecimiento de la ubicación de JDK en la pestaña ubicaciones de Android](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----

