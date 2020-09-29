---
title: Introducción a tvOS 10
description: En este artículo se presentan todas las API y características nuevas y modificadas disponibles en tvOS 10 para desarrolladores de Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 5d59646f111e5ac3f50de7319f8d5065492c9942
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434901"
---
# <a name="introduction-to-tvos-10"></a>Introducción a tvOS 10

_En este artículo se presentan todas las API y características nuevas y modificadas disponibles en tvOS 10 para desarrolladores de Xamarin. tvOS._

Con el nuevo SDK de tvOS 10, Apple ha incluido nuevas API y servicios que permiten al desarrollador crear nuevas categorías de aplicaciones y características. 

Para más información sobre tvOS 10, consulte la documentación de [tvOS + apps](https://developer.apple.com/tvos/) de Apple.

## <a name="whats-new-in-tvos-10"></a>Novedades de tvOS 10

Apple ha agregado varias API y servicios nuevos en tvOS 10 junto con muchas mejoras en las características existentes, entre las que se incluyen:

## <a name="new-user-interface-styles"></a>Nuevos estilos de la interfaz de usuario

tvOS 10 ahora es compatible con un tema de la interfaz de usuario oscuro y ligero al que todos los controles de UIKit de la compilación se adaptarán automáticamente, en función de las preferencias del usuario.

Al crear e implementar nuevos controles de interfaz de usuario personalizados, el desarrollador debe usar la clase [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) para adaptarse al tema seleccionado del usuario.

Para obtener más información, consulte la documentación de [nuevos estilos](~/ios/tvos/platform/user-interface-styles.md) de la interfaz de usuario.

## <a name="security-and-privacy-enhancements"></a>Mejoras en la seguridad y la privacidad

Apple ha realizado varias mejoras en la seguridad y la privacidad en tvOS 10 que le ayudarán a los desarrolladores a mejorar la seguridad de sus aplicaciones y a garantizar la privacidad del usuario final.

Como resultado, las aplicaciones que se ejecutan en watchos 3 (o posterior) deben declarar estáticamente su intención de acceder a características específicas o información de usuario especificando una o más claves específicas de la privacidad en sus `Info.plist` archivos que expliquen al usuario por qué la aplicación quiere obtener acceso.

Dado que tvOS 10 comparte estos cambios con iOS 10, consulte nuestra guía de [mejoras de seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md) de iOS 10 para obtener más información.

## <a name="video-subscriber-account"></a>Cuenta de suscriptor de vídeo

Novedad de tvOS 10, el marco de cuentas del suscriptor de vídeo permite que las aplicaciones que admiten streaming autenticado o vídeo bajo demanda se autentiquen con su proveedor de TV por cable o satélite usando una experiencia de inicio de sesión único para el usuario final.

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>Color amplio

tvOS 10 amplía la compatibilidad con los formatos de píxeles de rango extendido y los espacios de colores de amplia gama en todo el sistema, incluidos marcos como gráficos principales, imagen básica, metal y AVFoundation. La compatibilidad con dispositivos con pantallas de color ancho se facilita aún más proporcionando este comportamiento en toda la pila de gráficos.

Además, se ha `UIKit` modificado para que funcione en el nuevo **sRGB** ColorSpace ampliado, lo que facilita la combinación de colores en gamas de colores anchos sin pérdida significativa de rendimiento.

Apple ofrece las siguientes prácticas recomendadas al trabajar con colores anchos:

- `UIColor` ahora usa el espacio de color sRGB y dejará de fijar valores en `0.0` el `1.0` intervalo para. Si la aplicación se basa en el comportamiento de la abrazadera anterior, deberá modificarla para tvOS 10.
- Si la aplicación realiza una representación personalizada de `UIImages` , use la nueva clase [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) para especificar el uso de los formatos de intervalo extendido o de intervalo estándar.
- Cuando se usa una API de bajo nivel como gráficos principales o metal para proporcionar el procesamiento de imágenes, la aplicación debe usar un espacio de colores extendido y un formato de píxel que admita valores de punto flotante de 16 bits. Cuando sea necesario, la aplicación tendrá que fijar manualmente los valores de componente de color.
- Los gráficos principales, los sombreadores de imagen principal y de rendimiento de metal proporcionan nuevos métodos para la conversión entre los dos espacios de color.

Para obtener más información, consulte la guía [de introducción a la amplia gama de colores](~/ios/platform/wide-color.md) .

## <a name="newly-available-existing-frameworks"></a>Marcos existentes recién disponibles

Varios marcos de trabajo que estaban disponibles en iOS (y no tvOS) se han puesto a disposición de tvOS 10, como:

- ExternalAccessory
- HomeKit
- MultipeerConnectivity
- Fotos
- ReplayKit
- UserNotification

## <a name="additional-framework-changes"></a>Cambios adicionales en el marco de trabajo

Además de los principales cambios en el marco de trabajo y las adiciones enumeradas anteriormente, Apple ha realizado muchos cambios más pequeños en el marco de trabajo de tvOS 10.

Para obtener más información, consulte nuestra guía [adicional de cambios](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) en el marco de trabajo.

## <a name="deprecated-apis"></a>Interfaces API desusadas

Ninguna API o marco de trabajo quedó en desuso en tvOS 10. Consulte la documentación sobre las diferencias de la [API de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) de Apple para obtener una lista completa de las modificaciones de la API.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [Novedades de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)