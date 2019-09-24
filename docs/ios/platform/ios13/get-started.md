---
title: Introducción a iOS 13, iPados 13, tvOS 13 y watchos 6
description: En este documento se describe cómo configurar para compilar aplicaciones iOS 13, iPados 13, tvOS 13 y watchos 6 con Xamarin. En él se describe cómo descargar Xcode 11 y actualizar Visual Studio para Mac.
ms.prod: xamarin
ms.assetid: 97414545-85D2-433C-9246-63B6763F488A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/01/2019
ms.openlocfilehash: 1f190ef2f99e71c8cf21680f9902caccf3454450
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2019
ms.locfileid: "71206323"
---
# <a name="get-started-with-ios-13"></a>Introducción a iOS 13

En este documento se describe cómo empezar a crear aplicaciones de Xamarin que llaman a las API publicadas con Xcode 11, para iOS 13. El uso de la versión preliminar requiere macOS 10.14.4 (Mojave) o una versión más reciente.

## <a name="download-and-install"></a>Descargar e instalar

1. **Instalar Xcode 11** : los desarrolladores de Apple registrados pueden descargar e instalar la versión más reciente de Xcode 11 del [portal para desarrolladores de Apple](https://developer.apple.com/download/) o de la tienda de **aplicaciones**.

2. **Ejecute Xcode 11** : ejecute Xcode 11 antes de actualizar y ejecutar Visual Studio para Mac, ya que instala algunas herramientas que necesita Xamarin.

3. En Visual Studio para Mac, seleccione **Visual Studio > buscar actualizaciones...** , seleccione el canal **estable** e instale las actualizaciones disponibles.

4. Opta **Instalación de iOS 13 en los dispositivos iOS** : para las pruebas de dispositivos de las aplicaciones que usan las API introducidas con Xcode 11, los desarrolladores de Apple registrados pueden [Descargar](https://developer.apple.com/download) e instalar el sistema operativo en sus dispositivos. 

   > [!TIP]
   > Incluso si la aplicación no usa ninguna API nueva, asegúrese de compilarla con los SDK de Xcode 11 más recientes y pruébelo para asegurarse de que todo funciona según lo previsto. Si una aplicación no llama a ninguna API nueva, puede volver a compilarla con estos nuevos SDK y probarla en dispositivos que aún no se hayan actualizado al nuevo sistema operativo.
   >
   > Antes de actualizar los dispositivos a las versiones más recientes del sistema operativo de Apple para probar sus aplicaciones de Xamarin, asegúrese de:
   >
   > - Lea las notas de la [versión de Apple](https://developer.apple.com/download/) para las actualizaciones del sistema operativo.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar Xcode](https://developer.apple.com/download/)
- [Notas de la versión de Xamarin.iOS](/xamarin/ios/release-notes/13/13.0)
