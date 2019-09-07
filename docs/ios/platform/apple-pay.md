---
title: Apple Pay en Xamarin. iOS
description: En esta guía se describe la configuración del entorno de Xamarin. iOS para su uso con Apple Pay para pagar bienes físicos, como alimentos, ocio y pertenencias a través de la aplicación. Incluye información sobre los identificadores, certificados y derechos necesarios.
ms.prod: xamarin
ms.assetid: A25AE660-B145-465F-9CCE-8D82BFD614C6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/05/2017
ms.openlocfilehash: f264f210a9228fd213f0c041abb5b26023c796f4
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70753266"
---
# <a name="apple-pay-in-xamarinios"></a>Apple Pay en Xamarin. iOS

_En esta guía se describe la configuración del entorno de Xamarin. iOS para su uso con Apple Pay para pagar bienes físicos, como alimentos, ocio y pertenencias a través de la aplicación. Incluye información sobre los identificadores, certificados y derechos necesarios._

Apple Pay se presentó junto con iOS 8, lo que permite a los usuarios pagar por bienes físicos como alimentos, ocio y pertenencias a través de sus dispositivos iOS. Está disponible en iPhone 6 y iPhone 6 Plus, y también se puede emparejar con el Apple Watch para las compras en la tienda. Cuando se usa en un iPhone, usa Touch ID como una manera de confirmar y autorizar las transacciones a la tarjeta de crédito o débito de un usuario.

## <a name="requirements"></a>Requisitos

Apple Pay solo está disponible en iOS 8 y versiones posteriores y, por lo tanto, requiere Xcode 6 como mínimo.

También se requieren los elementos siguientes para integrar Apple Pay en la aplicación:

- Plataforma de procesador de pagos
- Identificador de comerciante
- Un certificado Apple Pay
- Apple Pay derecho

Este documento examinará estos elementos con más detalle.

## <a name="differences-between-apple-pay-and-iap"></a>Diferencias entre Apple Pay y IAP

La principal diferencia entre Apple Pay y la *compra desde la aplicación* (IAP) pertenece a los productos que venden. Las mercancías *físicas* se venden a través de Apple Pay; los alimentos, los alojamientos y el entretenimiento físico (como los tickets de cine) son ejemplos de esto. Por el contrario, IAP vende productos *virtuales* , como contenido premium o adicional, y suscripciones: Piense en meses adicionales de un servicio de streaming o en otras vidas en un juego.

Los marcos de trabajo utilizados también son una diferencia clave; [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) se usa para Apple Pay, mientras que [STOREKIT](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/) proporciona la API de .NET Framework para IAP.

Con Apple Pay, Apple [indica](https://developer.apple.com/apple-pay/Getting-Started-with-Apple-Pay.pdf) que "[no] cobra a usuarios, comerciantes o desarrolladores a usar Apple Pay para pagos". En comparación, IAP tiene un cargo del 30% por cada transacción. Además, con Apple Pay, la transacción no pasa a través de Apple, sino a través de una plataforma de pago.

## <a name="using-a-payment-processor-platform"></a>Uso de una plataforma de procesador de pago

Una de las partes fundamentales de Apple Pay es el procesamiento de los pagos. Aunque es posible hacerlo usted mismo, requiere un conocimiento significativo de la criptografía.
- tal como se detalla en la [Guía de procesamiento de pagos](https://developer.apple.com/library/ios/ApplePay_Guide/ProcessPayment.html)de Apple.
Por otro lado, las plataformas de procesamiento de pagos controlan estas operaciones, lo que le permite concentrarse en la creación de la aplicación.

Entre las dos opciones se incluyen:

- **Stripe** : regístrese en [stripe.com](https://stripe.com/) para acceder a sus API.

- **JudoPay** : eche un vistazo a su [código de ejemplo de Xamarin en github](https://github.com/Judopay/Xamarin-Sample-App)y regístrese en [JudoPay.com](https://www.judopay.com/).

## <a name="provisioning-for-apple-pay"></a>Aprovisionamiento para Apple Pay

La configuración de una aplicación para usar Apple Pay requiere el programa de instalación en el portal para desarrolladores de Apple y dentro de la aplicación. Hay una serie de pasos que se deben seguir para aprovisionar correctamente la aplicación para Apple Pay:

1. Cree un identificador de comerciante:
    - Siga los pasos que se describen [aquí](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#merchantid) .
2. Cree un identificador de aplicación con la capacidad de aplicar pago y agréguele el comerciante:
    - Siga los pasos que se describen [aquí](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#appid) .
3. Genere un certificado para el ID. de comerciante:
    - Siga los pasos que se describen [aquí](~/ios/deploy-test/provisioning/capabilities/apple-pay-capabilities.md#certificate) .
4. Genere un perfil de aprovisionamiento con el identificador de aplicación recién creado:
    - Siga los pasos que se describen [aquí](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning) .
5. Agregar derechos de Apple Pay:
    - Seleccione el derecho de pago de Apple como se detalla [aquí](~/ios/deploy-test/provisioning/entitlements.md)o agregue manualmente el par clave-valor al archivo desde [aquí](~/ios/deploy-test/provisioning/entitlements.md) .

## <a name="working-with-apple-pay"></a>Trabajar con Apple Pay

Apple ha realizado varias mejoras en Apple Pay en iOS 10 que permiten al usuario realizar pagos seguros desde sitios web y a través de la interacción con Siri y Maps.

Con iOS 10, se han agregado varias API nuevas que funcionan con iOS y watchos para admitir redes de pago dinámico y un nuevo entorno de prueba de espacio aislado.

### <a name="apple-pay-website-integration"></a>Integración de Apple Pay sitios web

Como novedad de iOS 10, el desarrollador puede incorporar Apple Pay directamente en sus sitios web mediante **APPLEPAY JS**. Los usuarios que exploren el sitio web con Safari en iOS o macOS pueden realizar pagos con Apple Pay validando la transacción en su iPhone o Apple Watch. Para obtener más información, consulte la [referencia de APPLEPAY JP Framework](https://developer.apple.com/reference/applepayjs)de Apple.

### <a name="passkit-framework-enhancements"></a>Mejoras de PassKit Framework

En iOS 10, el marco de trabajo de PassKit se ha ampliado para admitir `UIKit` Apple Pay fuera de y para permitir que los emisores de tarjetas presenten sus propias tarjetas desde sus aplicaciones.

#### <a name="supporting-apple-pay-outside-of-uikit"></a>Compatibilidad Apple Pay fuera de UIKit

Mediante el uso de [PKPaymentAuthorizationController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontroller) y [PKPaymentAuthorixationControllerDelegate](https://developer.apple.com/reference/passkit/pkpaymentauthorizationcontrollerdelegate), una aplicación puede admitir la misma funcionalidad proporcionada por [PKPaymentAuthorizationViewController](https://developer.apple.com/reference/passkit/pkpaymentauthorizationviewcontroller) sin usar UIKit. Aunque esta nueva API es necesaria para admitir Apple Pay en el Apple Watch (y también en determinados intentos), es opcional en otras situaciones (como las aplicaciones existentes). Sin embargo, Apple sugiere pasar a la nueva API lo antes posible para proporcionar una amplia compatibilidad Apple Pay en todas las aplicaciones del desarrollador con un solo código base. Para obtener más información sobre la integración de intents y Siri, consulte nuestra [Introducción a la documentación de SiriKit](~/ios/platform/sirikit/index.md) .

#### <a name="presenting-issuer-cards-from-within-apps"></a>Presentación de tarjetas de emisor desde dentro de las aplicaciones

Con iOS 10, se han agregado nuevas características al marco PassKit que permiten a los emisores de tarjetas presentar sus tarjetas desde sus propias aplicaciones. El desarrollador puede Agregar un `PKPaymentButtonTypeInStore` botón a la interfaz de usuario de la aplicación que mostrará un botón Apple Pay de una tarjeta.

El `PresentPaymentPass` método de la clase [PKPassLibrary](https://developer.apple.com/reference/passkit/pkpasslibrary) también se puede usar para mostrar la tarjeta mediante programación.

### <a name="new-payment-network-support"></a>Nueva compatibilidad con redes de pago

Como novedad de iOS 10, una aplicación puede admitir automáticamente una nueva red de pago cuando está disponible sin que el desarrollador tenga que modificar, volver a compilar la aplicación y volver a enviarla a la tienda de aplicaciones.

El nuevo método [AvailableNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1833288-availablenetworks) de la `PKPaymentNetwork` clase permite a una aplicación detectar las redes disponibles en el dispositivo del usuario en tiempo de ejecución. Además, la propiedad [SupportedNetworks](https://developer.apple.com/reference/passkit/pkpaymentrequest/1619329-supportednetworks) se ha expandido para tomar el nombre del proveedor de pago como argumento. Con estos métodos, una aplicación puede admitir automáticamente cualquier red que admita el proveedor de pagos.

Para obtener más información, consulte la [configuración de Apple Pay](~/ios/platform/apple-pay.md) y la [Guía de Apple Pay](https://developer.apple.com/apple-pay/)de Apple.

### <a name="new-testing-environment"></a>Nuevo entorno de pruebas

Con iOS 10, Apple presentó un nuevo entorno de prueba que permite al desarrollador aprovisionar tarjetas de pago de prueba directamente en un dispositivo iOS. Este nuevo entorno de prueba devuelve los datos de pago de prueba cifrados a la aplicación.

Para habilitar el nuevo entorno de prueba, haga lo siguiente:

1. Cree una nueva cuenta de iCloud de prueba en iTunes Connect.
2. Inicie sesión en el dispositivo iOS con la nueva cuenta de prueba.
3. Establezca la región deseada en la que se va a probar la aplicación.
4. Use una de las tarjetas de pago de prueba de la [Guía de Apple Pay](https://developer.apple.com/apple-pay/) para realizar pagos.

> [!IMPORTANT]
> Al cambiar las cuentas de iCloud, el dispositivo cambiará automáticamente al nuevo entorno de pruebas. Sin embargo, Apple todavía **requiere** que la aplicación se pruebe con tarjetas reales en un entorno de producción antes de enviarse a iTunes App Store.

## <a name="summary"></a>Resumen

En este artículo, se exploran los distintos elementos necesarios para usar Apple Pay dentro de la aplicación. Hemos examinado cómo crear un identificador de comerciante y cómo se usa en el archivo **contitles. plist**, que debe modificarse manualmente.

## <a name="related-links"></a>Vínculos relacionados

- [Compras desde la aplicación](~/ios/platform/in-app-purchasing/index.md)
- [Introducción a PassKit](~/ios/platform/passkit.md)
- [PassKit](https://developer.apple.com/library/ios/documentation/PassKit/Reference/PKPaymentAuthorizationViewController_Ref/)
- [Apple Pay](https://developer.apple.com/apple-pay/)
- [Emporium (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-emporium)
