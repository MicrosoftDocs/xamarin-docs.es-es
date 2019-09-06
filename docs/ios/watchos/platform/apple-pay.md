---
title: Apple Pay en watchos en Xamarin
description: En este artículo se describen las mejoras que Apple ha realizado para Apple Pay en watchos 3 y cómo implementarlas en Xamarin. iOS para Apple Watch.
ms.prod: xamarin
ms.assetid: 32FF5D21-C252-485D-83AC-A7E592237962
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 7180ccd03429e3a23633744bae35f35b4d9a6678
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292227"
---
# <a name="apple-pay-on-watchos-in-xamarin"></a>Apple Pay en watchos en Xamarin

Apple ha realizado varias mejoras en Apple Pay en watchos 3 que agrega compatibilidad con los pagos desde la aplicación. Esto permite al usuario proporcionar de forma segura información de contacto y de pago para pagar los bienes y servicios físicos directamente desde el Apple Watch.


## <a name="about-apple-pay-enhancements"></a>Acerca de Apple Pay mejoras

Como se indicó anteriormente, Apple ha realizado varias mejoras en Apple Pay en watchos 3 que permiten el pago seguro y la información de contacto para pagar los bienes y servicios físicos directamente desde el Apple Watch. Estas mejoras se proporcionan mediante modificaciones en el marco de trabajo de PassKit.

Con iOS 10 y watchos 3, se han agregado varias API nuevas que funcionan con iOS y watchos para admitir redes de pago dinámico y un nuevo entorno de prueba de espacio aislado.

## <a name="passkit-framework-enhancements"></a>Mejoras de PassKit Framework

En iOS 10, el marco de trabajo de PassKit se ha ampliado para admitir `UIKit` Apple Pay fuera de y para permitir que los emisores de tarjetas presenten sus tarjetas dentro de sus aplicaciones. 

### <a name="supporting-apple-pay-outside-of-uikit"></a>Compatibilidad Apple Pay fuera de UIKit

Mediante el uso de [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) y [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), una aplicación puede admitir la misma funcionalidad proporcionada por [PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sin usar UIKit. Aunque esta nueva API es necesaria para admitir Apple Pay en el Apple Watch (y también en determinados intentos), es opcional en otras situaciones (como las aplicaciones existentes). Sin embargo, Apple sugiere pasar a la nueva API lo antes posible para proporcionar una amplia compatibilidad Apple Pay en todas las aplicaciones del desarrollador con un solo código base. Para obtener más información sobre la integración de intents y Siri, consulte nuestra [Introducción a la documentación de SiriKit](~/ios/platform/sirikit/index.md) .

### <a name="presenting-issuer-cards-from-within-apps"></a>Presentación de tarjetas de emisor desde dentro de las aplicaciones

Con iOS 10 y watchos 3, se han agregado nuevas características al marco PassKit que permiten a los emisores de tarjetas presentar sus tarjetas de pago desde sus propias aplicaciones. El desarrollador puede Agregar un `PKPaymentButtonTypeInStore` botón a la interfaz de usuario de la aplicación que mostrará un botón Apple Pay de una tarjeta.

El `PresentPaymentPass` método de la clase [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) también se puede usar para mostrar la tarjeta mediante programación.

## <a name="new-payment-network-support"></a>Nueva compatibilidad con redes de pago

Novedad de iOS 10 y watchos 3, una aplicación puede admitir automáticamente una nueva red de pago cuando está disponible sin que el desarrollador tenga que modificar, volver a compilar la aplicación y volver a enviarla a la tienda de aplicaciones.

El nuevo método [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) de la `PKPaymentNetwork` clase permite a una aplicación detectar las redes disponibles en el dispositivo del usuario en tiempo de ejecución. Además, la propiedad [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) se ha expandido para tomar el nombre del proveedor de pago como argumento. Con estos métodos, una aplicación puede admitir automáticamente cualquier red que admita el proveedor de pagos.

Para obtener más información, consulte la [configuración de Apple Pay](~/ios/platform/apple-pay.md) y la [Guía de Apple Pay](https://developer.apple.com/apple-pay/)de Apple.

## <a name="new-testing-environment"></a>Nuevo entorno de pruebas

Con iOS 10 y watchos 3, Apple presentó un nuevo entorno de prueba que permite al desarrollador aprovisionar tarjetas de pago de prueba directamente en un dispositivo iOS. Este nuevo entorno de prueba devuelve los datos de pago de prueba cifrados a la aplicación.

Para habilitar el nuevo entorno de prueba, haga lo siguiente:

1. Cree una nueva cuenta de iCloud de prueba en iTunes Connect.
2. Inicie sesión en el dispositivo iOS con la nueva cuenta de prueba.
3. Establezca la región deseada en la que se va a probar la aplicación.
4. Use una de las tarjetas de pago de prueba de la [Guía de Apple Pay](https://developer.apple.com/apple-pay/) para realizar pagos.

> [!NOTE]
> Al cambiar las cuentas de iCloud, el dispositivo cambiará automáticamente al nuevo entorno de pruebas. Sin embargo, Apple todavía **requiere** que la aplicación se pruebe con tarjetas reales en un entorno de producción antes de enviarse a iTunes App Store.

## <a name="summary"></a>Resumen

En este artículo se han explicado las mejoras que Apple ha realizado para Apple Pay en watchos 3 y cómo implementarlas en Xamarin. iOS.
