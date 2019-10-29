---
title: consideraciones sobre las plataformas de 32 y 64 bits
description: En este documento se describen varias consideraciones que hay que tener en cuenta al establecer como destino arquitecturas de 32 bits y de 64 bits para una aplicación Xamarin. iOS o Xamarin. Mac.
ms.prod: xamarin
ms.assetid: F7126340-04B2-4A10-B14D-394E23527C1A
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 5ba451de857444bc5b12b750ae479b62abdb75a3
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016332"
---
# <a name="3264-bit-platform-considerations"></a>consideraciones sobre las plataformas de 32 y 64 bits

Aunque iOS y macOS han admitido históricamente aplicaciones de 32 y 64 bits, Apple ha dejado de admitir gradualmente la compatibilidad con 32 bits.

A partir de iOS 11, las aplicaciones de 32 bits dejarán de iniciarse y [todos los envíos a la tienda de aplicaciones deben admitir 64 bits](https://developer.apple.com/news/?id=06282017b).

A partir del 2018 de enero, [las nuevas aplicaciones enviadas a Mac App Store deben admitir 64 bits](https://developer.apple.com/news/?id=06282017a)y las aplicaciones existentes deben actualizarse en junio de 2018.

Classic API de Xamarin (`XamMac.dll` y `monotouch.dll`) solo admiten aplicaciones de 32 bits. Sin embargo, las nuevas aplicaciones de Xamarin. iOS y Xamarin. Mac usan el [Unified API](~/cross-platform/macios/unified/index.md) (`Xamarin.iOS` y `Xamarin.Mac`) de forma predeterminada y, por tanto, pueden tener como destino 32 y 64 bits, según sea necesario.

## <a name="ios"></a>iOS

<a name="enable-64" />

### <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitar compilaciones de 64 bits de aplicaciones de Xamarin. iOS

> [!WARNING]
> Esta sección se incluye por motivos históricos y para ayudar a migrar proyectos de Xamarin. iOS antiguos al Unified API y admitir 64 bits. Todos los nuevos proyectos de Xamarin. iOS usarán el Unified API y el destino de 64 bits de forma predeterminada.

En el caso de las aplicaciones móviles de Xamarin. iOS que se han convertido en el Unified API, los desarrolladores deben actualizar manualmente la configuración de compilación para que tenga como destino 64 bits:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **Panel de solución**, haga doble clic en el proyecto de la aplicación para abrir la ventana **Opciones del proyecto** .
2. Seleccione **compilación de iOS**.
3. Para el simulador de iPhone, en la lista desplegable **arquitecturas admitidas** , seleccione **x86\_64** o **i386 + x86\_64**:

   [![Establecimiento de arquitecturas admitidas en x86\_64 o i386 + x86\_64](Images/Image01.png "Setting Supported architectures to x86\_64 or i386 + x86\_64")](Images/Image01-large.png#lightbox) 

4. En el caso de los dispositivos físicos, seleccione una de las combinaciones de **ARM64** disponibles:

   [![Establecimiento de arquitecturas admitidas en una de las combinaciones de ARM64](Images/Image02.png "Establecimiento de arquitecturas admitidas en una de las combinaciones de ARM64")](Images/Image02-large.png#lightbox)

5. Haga clic en **Aceptar**.
6. Realice una compilación limpia.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el proyecto de la aplicación y seleccione **propiedades**.
2. Seleccione **compilación de iOS**.
3. Para el simulador de iPhone, establezca las **arquitecturas admitidas** en **x86\_64** o **i386 + x86\_64**: 

   [![Establecimiento de arquitecturas admitidas en x86_64 o i386 + x86\_64](Images/VS02.png "Setting Supported architectures to x86_64 or i386 + x86\_64")](Images/VS02-large.png#lightbox)

4. En el caso de los dispositivos físicos, seleccione una de las combinaciones de **ARM64** disponibles:
    
   [![Establecimiento de arquitecturas admitidas en una de las combinaciones de ARM64](Images/VS01.png "Establecimiento de arquitecturas admitidas en una de las combinaciones de ARM64")](Images/VS01-large.png#lightbox)

5. Guarde los cambios.
6. Realice una compilación limpia.

-----

ARMv7s solo es compatible con el procesador A6 incluido en el iPhone 5 (o superior). El código ARMv7 es más rápido y más pequeño que el ARMv6, solo funciona con el iPhone 3GS y versiones posteriores, y es necesario para Apple cuando el destino es el iPad o una versión de iOS mínima de 5,0. ARMv6 funciona en todos los dispositivos, pero ya no es compatible con el compilador incluido en Xcode 4,5 y versiones posteriores. 

ARM64 es necesario para admitir iOS 8 en iPhone 6 u otros dispositivos de 64 bits y Apple lo necesitará cuando envíe aplicaciones de salida nuevas o actualizadas en iTunes App Store.

Para obtener una visión completa de las funcionalidades de varios dispositivos iOS, consulte el documento de [compatibilidad de dispositivos](https://developer.apple.com/library/content/documentation/DeviceInformation/Reference/iOSDeviceCompatibility/DeviceCompatibilityMatrix/DeviceCompatibilityMatrix.html) de Apple.

### <a name="64-bit-and-binary-size-increases"></a>64-bits y el tamaño binario aumenta

Durante la transición de Apple de 32 bits a 64, las aplicaciones de iOS tendrán que ejecutarse en hardware de 32 bits y de 64 bits. Por este motivo, el Unified API de Xamarin permite a los desarrolladores destinar ambos.

La compatibilidad con arquitecturas de 32 bits y de 64 bits aumentará considerablemente el tamaño de una aplicación. Sin embargo, si lo hace, permitirá que los dispositivos más recientes ejecuten código optimizado, al tiempo que se siguen admitiendo dispositivos antiguos.

> [!IMPORTANT]
> Si recibe el siguiente mensaje al enviar una aplicación de iOS a la tienda de aplicaciones de iTunes, _"ADVERTENCIA ITMS-9000: falta compatibilidad con 64 bits. A partir del 1 de febrero de 2015, las nuevas aplicaciones de iOS cargadas en App Store deben incluir compatibilidad con 64 bits y compilarse con el SDK de iOS 8, incluido en Xcode 6 o posterior. Para habilitar 64 bits en el proyecto, se recomienda usar la configuración de compilación predeterminada de Xcode de "arquitecturas estándar" para compilar un único binario con código de 32 bits y de 64 bits "._ Debe cambiar las arquitecturas admitidas a una de las combinaciones de **ARM64** disponibles (como se muestra arriba), volver a compilar y reenviar.

## <a name="mac"></a>Mac

> [!IMPORTANT]
> A partir del 2018 de enero, todas las nuevas aplicaciones Mac enviadas a Mac App Store deben admitir 64 bits. Las aplicaciones de Mac App Store existentes y sus actualizaciones deben admitir 64 bits a partir del 2018 de junio. Consulte el [anuncio de Apple](https://developer.apple.com/news/?id=06282017a) y [una guía que describe cómo actualizar las aplicaciones de Xamarin. Mac a 64 bits](~/cross-platform/macios/32-and-64/mac-64-bit.md).

La mayoría de los equipos Mac modernos admiten aplicaciones de 32 bits y de 64 bits.   MacOS 10,6 (el Leopard de nieve) era el último sistema operativo que se ejecutaba en sistemas de 32 bits.   La mayoría de los equipos Mac publicados desde 2010 admiten ambos sistemas.

A diferencia de iOS, muchos de los nuevos marcos introducidos en las versiones recientes de macOS solo se admiten en el modo de 64 bits (CloudKit, EventKit, GameController, LocalAuthentication, MediaLibrary, MultipeerConnectivity, NotificationCenter, GLKit, SpriteKit, social, y MapKit, entre otros).

Los Unified API permiten a los desarrolladores elegir qué tipo de aplicaciones quieren generar: 32 bits o 64 bits.

**las aplicaciones de 32 bits** se ejecutarán en equipos Mac de 32 bits y de 64 bits, tienen un espacio de direcciones limitado a 32 bits y requieren que todas las bibliotecas sean de 32 bits.

Normalmente usará este modo si tiene dependencias de 32 bits que no se ejecutan en el modo de 64 bits, si desea tener una descarga más pequeña o si no hay ninguna ventaja de rendimiento en pasar a 64 bits.

Este modo está limitando porque no podrá usar muchos marcos de trabajo disponibles en macOS Mavericks y macOS Yosemite.

**las aplicaciones de 64 bits** solo se ejecutarán en dispositivos Mac de 64 bits.

Para Mac, este es el modo de funcionamiento preferido, ya que la mayoría de los equipos Mac que se usan actualmente admiten el modo de 64 bits y tiene acceso al conjunto completo de marcos de trabajo que proporciona Apple.

### <a name="enabling-64-bit-builds-of-xamarinmac-apps"></a>Habilitar compilaciones de 64 bits de aplicaciones de Xamarin. Mac

Para obtener información sobre cómo compilar una aplicación de 64 bits con Xamarin. Mac, consulte la guía de [actualización de aplicaciones unificadas de Xamarin. Mac a 64](~/cross-platform/macios/32-and-64/mac-64-bit.md) bits.

## <a name="related-links"></a>Vínculos relacionados

- [Diferencias de Unified API clásicas](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
