---
title: ¿Cómo se puede actualizar la versión del kit de desarrollo de Java (JDK)?
description: En este artículo se muestra cómo actualizar la versión de Java Development Kit (JDK) en Windows y Mac.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 4b3ac51d-18dd-4034-87b4-4365194e4ece
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 09/07/2018
ms.openlocfilehash: 0f7499551db7d86d7978b9c3e1f562a2f054c202
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "73019529"
---
# <a name="how-do-i-update-the-java-development-kit-jdk-version"></a>¿Cómo se puede actualizar la versión del kit de desarrollo de Java (JDK)?

_En este artículo se muestra cómo actualizar la versión de Java Development Kit (JDK) en Windows y Mac._

## <a name="overview"></a>Información general

Xamarin.Android usa Java Development Kit (JDK) para la integración con Android SDK a fin de compilar aplicaciones de Android y ejecutar Android Designer. Las versiones más recientes de Android SDK (API 24 y superiores) requieren JDK 8 (1.8). Como alternativa, puede instalar la [versión preliminar de Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md). Con el tiempo, Microsoft Mobile OpenJDK reemplazará a JDK 8 para el desarrollo de Xamarin.Android.

Para actualizar a Microsoft Mobile OpenJDK, consulte [Vista previa de Microsoft Mobile OpenJDK](~/android/get-started/installation/openjdk.md). Para actualizar a JDK 8, siga estos pasos:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Descargue JDK 8 (1.8) desde el [sitio web de Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de pantalla de la página de descarga de JDK en el sitio web de Oracle](update-jdk-images/image1.png)

2. Seleccione la versión de 64 bits para permitir la representación de [controles personalizados](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/vs/xamarin.vs_4/xamarin.vs_4.2/index.md#androiddesignercustomcontrols) en Xamarin Android Designer:

    ![Selección del paquete de Windows x64 JDK que se va a descargar desde la página de descarga de JDK](update-jdk-images/image2.png)

3. Ejecute el archivo. exe e instale las **Herramientas de desarrollo**:

    ![Instalación de las Herramientas de desarrollo en el instalador de JDK](update-jdk-images/image3.png)

4. Abra Visual Studio y actualice **Ubicación de Ubicación de Java Development Kit** para que apunte al nuevo JDK en **Herramientas > Opciones > Xamarin > Configuración de Android > Ubicación de Java Development Kit**:

    [![Configuración de la ruta de acceso del JDK en la página Configuración de Android](update-jdk-images/image4-sml.png)](update-jdk-images/image4.png#lightbox)

Asegúrese de reiniciar Visual Studio después de actualizar la ubicación.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Descargue JDK 8 (1.8) desde el [sitio web de Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html):

    ![Captura de pantalla de la página de descarga de JDK en el sitio web de Oracle](update-jdk-images/image1.png)

2. Abra el archivo .dmg y ejecute el instalador .pkg:

    ![Ejecución del instalador de JDK en macOS](update-jdk-images/image5.png)

Mac OS establecerá automáticamente la nueva versión de JDK como valor predeterminado mediante la actualización de **/System/Library/Frameworks/JavaVM.framework/Versions/Current**. A continuación, puede comprobar que la ubicación del **SDK de Java (JDK)** está establecida en el valor predeterminado esperado de **/usr** en **Visual Studio para Mac > Preferencias > Proyectos > Ubicaciones de SDK > Android > Ubicaciones > Ubicación del SDK de Java (JDK)** :

[![Configuración de la ubicación de JDK en la pestaña de ubicaciones de Android](update-jdk-images/image6-sml.png)](update-jdk-images/image6.png#lightbox)

-----
