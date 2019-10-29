---
title: Suscripciones e informes en Xamarin. iOS
description: En este documento se describen las suscripciones que no son renovables, las suscripciones gratuitas, las suscripciones renovables automáticamente y el uso de iTunes Connect para informar sobre estos elementos.
ms.prod: xamarin
ms.assetid: 27EE4234-07F5-D2CD-DC1C-86E27C20141E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 8f930e2358562df7e68841b87b6a3df0914805fe
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032303"
---
# <a name="subscriptions-and-reporting-in-xamarinios"></a>Suscripciones e informes en Xamarin. iOS

## <a name="about-non-renewing-subscriptions"></a>Acerca de las suscripciones que no son de renovación

Las suscripciones que no son de renovación están pensadas para productos que representan la venta de un servicio con una restricción de tiempo (como el acceso de una semana a una aplicación de navegación o acceso limitado por tiempo a un archivo de datos).   
   
Diferencias principales entre las suscripciones sin renovación y otros tipos de productos:

- La definición del producto en iTunes Connect no incluye el término. El código de aplicación debe ser capaz de deducir el período de validez del ID. de producto. 
- Se pueden adquirir varias veces (como un producto consumible). Las aplicaciones son necesarias para administrar el período de suscripción, la expiración y la renovación, y evitar que el usuario compre suscripciones superpuestas. 
- Las compras no son compatibles con la función de restauración StoreKit. Si la suscripción debe estar disponible en todos los dispositivos de un usuario, la aplicación tendrá que diseñar e implementar esta característica junto con un servidor remoto. Las aplicaciones también son responsables de realizar copias de seguridad del estado de la suscripción en los casos en los que se realiza una copia de seguridad de un dispositivo y después se restaura a partir de la copia de seguridad. 
- Información general sobre la implementación
- Normalmente, las suscripciones que no son de renovación deberían implementarse mediante el flujo de trabajo proporcionado por el servidor y se pueden administrar como productos consumibles. 

## <a name="about-free-subscriptions"></a>Acerca de las suscripciones gratuitas

Las suscripciones gratuitas permiten a los desarrolladores colocar contenido gratuito en aplicaciones de NewsStand (no se pueden usar en aplicaciones que no son de NewsStand). Una vez que se inicia una suscripción gratuita, estará disponible en todos los dispositivos del usuario. Las suscripciones gratuitas nunca expiran; solo finalizan cuando se desinstala la aplicación.

### <a name="implementation-overview"></a>Información general sobre la implementación

Las suscripciones gratuitas se comportan de forma muy parecida a las suscripciones renovables automáticamente. La aplicación debe tener un producto de suscripción gratuita disponible para "compra" en iTunes Connect. Cuando el usuario lo compra, la compra de suscripción gratuita debe validarse como un producto de suscripción autorenovable. Las transacciones de suscripción gratuitas se pueden restaurar.

## <a name="about-auto-renewable-subscriptions"></a>Acerca de las suscripciones renovables automáticamente

Las suscripciones autorenovables se usan principalmente en aplicaciones NewsStand. Representan un producto que concede al usuario acceso al contenido dinámico durante un período de tiempo determinado, que se configura en iTunes Connect (establezca períodos que oscilan entre 7 días y 1 año). Las suscripciones se renuevan automáticamente y cobran el ID. de Apple de los usuarios al final de cada período de suscripción, a menos que el usuario opte por ello. Este tipo de producto funciona bien para las suscripciones de revistas o de noticias, donde el usuario obtiene acceso a cada uno de los problemas publicados mientras su suscripción es válida.

### <a name="implementation-overview"></a>Información general sobre la implementación

Las suscripciones renovables automáticamente deben implementarse mediante el flujo de trabajo productos entregados por el servidor (consulte la sección comprobación de la *confirmación y productos entregados por el servidor* ).

#### <a name="shared-secret"></a>Secreto compartido

El secreto compartido de compra desde la aplicación debe usarse en la solicitud JSON al comprobar las suscripciones renovables automáticamente en el servidor. El secreto compartido se crea o se obtiene acceso a él mediante iTunes Connect.

En la Página principal de iTunes Connect, seleccione **mis aplicaciones**:   
   
 [![](subscriptions-and-reporting-images/image2.png "Select My Apps")](subscriptions-and-reporting-images/image2.png#lightbox)  

Seleccione una aplicación y haga clic en la pestaña **compras desde la aplicación** :

[![](subscriptions-and-reporting-images/image6.png "Click on the In-App Purchases tab")](subscriptions-and-reporting-images/image6.png#lightbox)

En la parte inferior de la página, seleccione **ver o generar un secreto compartido**:
   
 [![](subscriptions-and-reporting-images/image40.png "Select View or generate a shared secret")](subscriptions-and-reporting-images/image40.png#lightbox)

 [![](subscriptions-and-reporting-images/image41.png "Generate a shared secret")](subscriptions-and-reporting-images/image41.png#lightbox)   

Para usar el secreto compartido, inclúyalo en la carga de JSON que se envía a los servidores de Apple cuando se valida una confirmación de compra desde la aplicación para una suscripción autorenovable, de la siguiente manera:

```csharp
{
   "receipt-data" : "(receipt bytes here)",
   "password"     : "(shared secret bytes here)"
}
```

El campo de estado de la respuesta será cero si la compra es válida, como con otros tipos de producto.

#### <a name="downloading-items-after-the-initial-subscription-term"></a>Descargar elementos después del período de suscripción inicial

Como parte de la entrega de productos de suscripción, el código debe comprobar con frecuencia la última recepción conocida en los servidores de Apple. Si una suscripción se ha renovado automáticamente desde la última comprobación, la respuesta JSON contendrá campos adicionales que notifican a la aplicación de la transacción que se ha producido (que debe ampliar la validez de las suscripciones). La respuesta JSON contendrá:

```csharp
{
   "status" : 0,
   "receipt" : { (receipt here) },
   "latest_receipt" : "(base-64 encoded receipt here)",
   "latest_receipt_info" : { (latest receipt info here) }
}
```

Si el estado es cero, la suscripción sigue siendo válida y los demás campos contienen datos válidos. Si el estado es 21006, la suscripción ha expirado. Consulte la documentación sobre la [comprobación de una notificación de suscripción de renovación automática](https://developer.apple.com/library/ios/releasenotes/General/ValidateAppStoreReceipt/Chapters/ValidateRemotely.html) para ver otros códigos de error.

#### <a name="restoring-auto-renewable-subscriptions"></a>Restaurar suscripciones autorenovables

Recibirá varias transacciones: la transacción de compra original más una transacción independiente para cada período de tiempo durante el cual se renovó la suscripción. Debe realizar un seguimiento de las fechas y los términos de inicio para comprender cuál es el período de validez.   

El objeto SKPaymentTransaction no incluye el término de suscripción: debe usar un identificador de producto diferente para cada término y escribir código que pueda extrapolar el período de suscripción de la fecha de compra de la transacción.

#### <a name="testing-auto-renewal"></a>Prueba de la renovación automática

Para facilitar la prueba de las suscripciones, sus duraciones se comprimen al realizar pruebas en el espacio aislado. las suscripciones de 1 semana se renuevan cada 3 minutos, 1 año suscripciones renovadas cada hora. Las suscripciones se renovarán automáticamente un máximo de 6 veces durante las pruebas en el espacio aislado.

## <a name="reporting"></a>Informes

iTunes Connect ( [itunesconnect.Apple.com](https://itunesconnect.apple.com)) proporciona:   
   
 **Ventas y tendencias** : muestra detalles de las descargas de aplicaciones, actualizaciones y compras desde la aplicación.   
   
 **Pagos e informes financieros** : detalla la información obtenida por las aplicaciones, además de enumerar los pagos que se han realizado en usted y el grado de su cuenta.

A continuación se muestra un informe de ventas y tendencias de ejemplo:   

 [![](subscriptions-and-reporting-images/image42.png "An example Sales and Trends report")](subscriptions-and-reporting-images/image42.png#lightbox)   
   
 También hay una aplicación de [ **ITC Connect Mobile**iOS (vínculo de iTunes)](https://itunes.apple.com/us/app/itunes-connect-mobile/id376771144?mt=8).
Aquí se muestran capturas de pantallas de iPhone para algunas de las estadísticas disponibles:   
   
 [![](subscriptions-and-reporting-images/image43.png "iPhone screenshots for some of the statistics available")](subscriptions-and-reporting-images/image43.png#lightbox)
