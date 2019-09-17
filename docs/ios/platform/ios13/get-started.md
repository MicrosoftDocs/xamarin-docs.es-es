---
title: Introducción a iOS 13, iPados 13, tvOS 13 y watchos 6
description: En este documento se describe cómo configurar para compilar aplicaciones iOS 13, iPados 13, tvOS 13 y watchos 6 con Xamarin. En él se describe cómo descargar Xcode 11 y actualizar Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 9dc6f234c4bc14c3644d953eef0d2e0f397436e5
ms.sourcegitcommit: 61a35d0643eb3bf5adb8f8831da54771d8dde626
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033073"
---
# <a name="get-started-with-ios-13"></a>Introducción a iOS 13

![Características en versión preliminar](~/media/shared/preview.png)

En este documento se describe cómo empezar a crear aplicaciones de Xamarin que llaman a las API publicadas con Xcode 11, para iOS 13. El uso de la versión preliminar requiere macOS 10.14.4 (Mojave) o una versión más reciente.

## <a name="download-and-install"></a>Descargar e instalar

1. **Instalar Xcode 11 beta** : los desarrolladores de Apple registrados pueden descargar e instalar la versión más reciente de Xcode 11 beta del [portal para desarrolladores de Apple](https://developer.apple.com/download/) o de la tienda de **aplicaciones**.

2. **Ejecute Xcode 11 beta** : ejecute Xcode 11 antes de actualizar y ejecutar Visual Studio para Mac, ya que instala algunas herramientas que necesita Xamarin.

3. En Visual Studio para Mac, seleccione **Visual Studio > buscar actualizaciones...** , seleccione el canal **Xcode 11 versiones preliminares** e instale las actualizaciones disponibles. Si no ve este canal, asegúrese de que ha iniciado sesión en su cuenta desde la cuenta de **> de Visual Studio..** ..

4. En Visual Studio para Mac, seleccione **Visual Studio > preferencias > proyectos > ubicaciones de SDK > Apple** y seleccione **Xcode-beta. app**.

5. Opta Si evalúa esta vista previa con _Xcode 11 beta 3_, debe habilitar la vinculación. Haga clic con el botón derecho en el proyecto, vaya a **opciones > compilación de iOS > comportamiento del enlazador** y establezca el valor del comportamiento del vinculador en **solo vincular SDK de Framework**. Esta solución no será necesaria en futuras versiones preliminares.

6. Opta **Instalación de iOS 13 en los dispositivos iOS** : para las pruebas de dispositivos de las aplicaciones que usan las API introducidas con Xcode 11, los desarrolladores de Apple registrados pueden [Descargar](https://developer.apple.com/download) e instalar el sistema operativo en sus dispositivos. Como iOS está en versión beta, tenga cuidado al instalarlo en el dispositivo principal.

   > [!TIP]
   > Incluso si la aplicación no usa ninguna API nueva, asegúrese de compilarla con los SDK de Xcode 11 más recientes y pruébelo para asegurarse de que todo funciona según lo previsto. Si una aplicación no llama a ninguna API nueva, puede volver a compilarla con estos nuevos SDK y probarla en dispositivos que aún no se hayan actualizado al nuevo sistema operativo.
   >
   > Antes de actualizar los dispositivos a las versiones más recientes del sistema operativo de Apple para probar sus aplicaciones de Xamarin, asegúrese de:
   >
   > - Lea las notas de la [versión de Apple](https://developer.apple.com/download/) para las actualizaciones del sistema operativo.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar Xcode](https://developer.apple.com/download/)
- [Notas de la versión de Xamarin. iOS Preview](/xamarin/ios/release-notes/12/12.99)
