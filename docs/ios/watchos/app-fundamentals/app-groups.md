---
title: Trabajar con grupos de aplicaciones de watchos en Xamarin
description: En este documento se describen los grupos de aplicaciones y su uso en una aplicación de watchos. En él se describe cómo configurar un grupo de aplicaciones, los requisitos de aprovisionamiento, los derechos. plist y las consideraciones de implementación.
ms.prod: xamarin
ms.technology: xamarin-ios
ms.assetid: 6968606B-C287-424F-A321-2492E12BC0BB
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: ee3897f14c460149e840fcea8b3fb533beeab935
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931630"
---
# <a name="working-with-watchos-app-groups-in-xamarin"></a>Trabajar con grupos de aplicaciones de watchos en Xamarin

Un grupo de aplicaciones permite que aplicaciones diferentes (o una aplicación y sus extensiones) tengan acceso a una ubicación de almacenamiento de archivos compartidos. Los grupos de aplicaciones se pueden usar para datos como:

- Apple Watch [configuración](~/ios/watchos/app-fundamentals/settings.md).
- [NSUserDefaults](~/ios/watchos/app-fundamentals/parent-app.md#nsuserdefaults)compartido.
- [Archivos](~/ios/watchos/app-fundamentals/parent-app.md#files)compartidos.

## <a name="configure-an-app-group"></a>Configurar un grupo de aplicaciones

La ubicación compartida se configura mediante un [grupo de aplicaciones](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que se configura en la sección **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles) del [Centro para desarrolladores de iOS](https://developer.apple.com/devcenter/ios/). También se debe hacer referencia a este valor en el archivo **contitles. plist**de cada proyecto.

### <a name="provisioning"></a>Aprovisionamiento

El grupo de aplicaciones tendrá un identificador, que normalmente es el identificador de paquete con un `group.` prefijo. Por ejemplo, podríamos usar el identificador de paquete `com.xamarin.WatchSettings` y el grupo de aplicaciones `group.com.xamarin.WatchSettings` .

[![Use el ID. de paquete com. Xamarin. WatchSettings y el grupo de grupos de aplicaciones. com. Xamarin. WatchSettings](app-groups-images/app-group-sml.png)](app-groups-images/app-group.png#lightbox)

### <a name="entitlementsplist"></a>Entitlements.plist

Además de configurar el perfil de aprovisionamiento, habilite los **grupos de aplicaciones** en el archivo **contitles. plist** y escriba el identificador que ha elegido:

[![Configurar el archivo plist y escribir el identificador](app-groups-images/entitlements-sml.png)](app-groups-images/entitlements.png#lightbox)

### <a name="deployment"></a>Implementación

Asegúrese de configurar el grupo de aplicaciones correctamente en el aprovisionamiento de [implementación](~/ios/watchos/deploy-test/index.md#App_Groups) .

Para obtener más información, consulte la documentación sobre las [funcionalidades del grupo de aplicaciones](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

## <a name="related-links"></a>Vínculos relacionados

- [Uso compartido de datos de Apple con la aplicación contenedora](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
- [Documento del grupo de aplicaciones de Apple](https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19)
