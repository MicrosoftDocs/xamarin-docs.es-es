---
title: Cambios de arquitectura en iOS 11
description: En este documento se describe el desuso de las aplicaciones de 32 bits en iOS 11. Describe cómo actualizar las aplicaciones para que tengan como destino arquitecturas de 64 bits.
ms.prod: xamarin
ms.assetid: 55F62F3F-8570-402B-B7D9-2875F76CB946
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 15cd6139cc83639146e6044d2b791d94ee30f2d9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70286345"
---
# <a name="architecture-changes-in-ios-11"></a>Cambios de arquitectura en iOS 11

Uno de los cambios más importantes que debe tener en cuenta con iOS 11 es el desuso de la compatibilidad de 32 bits con las aplicaciones, tal como se detalla en el comunicado de prensa de [Apple](https://developer.apple.com/news/?id=06282017b) . Todas las aplicaciones y actualizaciones nuevas de las aplicaciones existentes deben admitir 64 bits. las aplicaciones de 32 bits **no se iniciarán** en iOS 11.

Para actualizar la aplicación en Visual Studio para Mac, siga estos pasos:

1. Haga clic con el botón derecho en el nombre del proyecto para abrir **Opciones del proyecto**.
2. Vaya a la pestaña **compilación de iOS** .
3. Establezca la lista desplegable **arquitecturas admitidas** en **X86_64** para **depurar | iPhoneSimulator** y **Release | iPhoneSimulator**:

    ![Desplegable de arquitecturas del simulador de cambios](architecture-changes-images/image1.png)

4. En el caso de dispositivos iOS, cambie la configuración a **depurar | iPhone** y establezca la lista desplegable **arquitecturas admitidas** en **ARM64**:

    ![Cambiar la lista desplegable de arquitecturas de dispositivos](architecture-changes-images/image2.png)

5. Cambie la configuración a **versión | iPhone** y establezca la lista desplegable de **arquitecturas admitidas** en **ARM64**.

Para obtener más información sobre las arquitecturas de 32 bits y de 64 bits, consulte la guía de [consideraciones sobre la plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md#ios) .

## <a name="related-links"></a>Vínculos relacionados

- [Novedades de iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página del producto de App Store actualizada (Apple)](https://developer.apple.com/app-store/product-page/)
- [Actualización de la aplicación para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
