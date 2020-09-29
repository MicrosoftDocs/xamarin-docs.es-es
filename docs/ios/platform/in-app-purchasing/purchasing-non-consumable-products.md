---
title: Compra de productos no consumibles en Xamarin. iOS
description: En este documento se describen los productos que no se pueden consumir en Xamarin. iOS, que son características adquiridas por un usuario que permanecen disponibles indefinidamente, independientemente del dispositivo.
ms.prod: xamarin
ms.assetid: 635D9CA2-6BCA-53E1-7B10-968029AA3493
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 78a4f3ae90b1b20e9fe3cd4164726fee34f38eb7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435508"
---
# <a name="purchasing-non-consumable-products-in-xamarinios"></a>Compra de productos no consumibles en Xamarin. iOS

El cliente "posee" los productos que no se pueden consumir. La expectativa es que siempre tendrán acceso a ellos, incluso si su dispositivo se pierde o lo roban o compran uno nuevo. Son útiles para libros, problemas de revistas, niveles de juegos, filtros fotográficos, "características Pro", etc. Una vez que un usuario haya adquirido un producto que no se pueda consumir, nunca tendrá que pagar por él de nuevo. Si el código le permite accidentalmente probar, StoreKit mostrará un mensaje que le indica que ya se ha comprado.

## <a name="non-consumable-products-sample"></a>Ejemplo de productos no consumibles

El [código InAppPurchaseSample](/samples/xamarin/ios-samples/storekit) contiene un proyecto denominado *nonconsumibles*. En el ejemplo de código se muestra cómo implementar productos no consumibles mediante el uso de filtros de foto como ejemplo. Una vez que haya adquirido un filtro, puede aplicarlo a la fotografía una y otra vez. Nunca debe volver a comprarlo.   

El proceso de compra se muestra en esta serie de capturas de pantallas: el botón **comprar** se convierte en el botón de activación de características:   

 [![El proceso de compra se muestra en esta serie de capturas de pantallas](purchasing-non-consumable-products-images/image34.png)](purchasing-non-consumable-products-images/image34.png#lightbox)   

El proceso de compra es el mismo que el de un producto consumible; la diferencia clave radica en cómo se realiza el seguimiento de la compra en el código de la aplicación. En este ejemplo, el botón comprar solo está disponible si el producto aún no se ha comprado; de lo contrario, el botón activa la propia característica.   

En el diagrama siguiente se muestran las interacciones entre las clases y el servidor de App Store para realizar una compra de productos no consumible:   

 [![Las interacciones entre las clases y el servidor de App Store para realizar una compra de productos no consumible](purchasing-non-consumable-products-images/image35.png)](purchasing-non-consumable-products-images/image35.png#lightbox)   

La diferencia clave del ejemplo consumible es que una vez completada la compra, la interfaz de usuario se actualiza para evitar la recompra. En este ejemplo, la notificación de una transacción correcta actualiza la interfaz de usuario para que el botón **comprar** se convierta en un botón que activa la propia característica.

## <a name="re-purchasing-non-consumable-products"></a>Volver a comprar productos no consumibles

Normalmente, el código debe ocultar o reasignar un botón de compra una vez que el producto se ha comprado correctamente, para evitar que el usuario intente comprar el producto de nuevo. Para ello, la aplicación de ejemplo cambia el botón **comprar** en el botón que hace que el filtro fotográfico de ejemplo funcione.   

Hay situaciones en las que una aplicación no puede determinar si ya se ha comprado un producto no consumible:

- Si se elimina y se vuelve a instalar una aplicación en un dispositivo, se perderán todos los registros de compra (a menos que el usuario realice una restauración de copia de seguridad). 
- Si el usuario tiene la aplicación instalada en dos (o más) dispositivos y realiza una compra en uno de los dispositivos. Los demás dispositivos seguirán mostrando el producto disponible para su compra. 
- Si un cliente intenta volver a comprar un producto no consumible en estas situaciones, el App Store volverá a entregar el producto sin cargo alguno. La interfaz de usuario aparecerá inicialmente para realizar una compra (por ejemplo, se muestra una alerta de confirmación y se requerirá el identificador de Apple). sin embargo, el usuario verá un mensaje que le informa de que el producto ya se ha adquirido.  

La ruta de acceso del código en este escenario es exactamente la misma que una compra normal, las únicas diferencias son:

- El usuario no se cobra de nuevo para el producto.
- El  `SKPaymentTransaction` objeto que se pasa a la aplicación tendrá una  `OriginalTransaction` propiedad que hace referencia a la transacción que se generó cuando se adquirió inicialmente el producto. 
- Las aplicaciones que venden productos que no se pueden consumir también deben implementar la característica de  **restauración** de StoreKit para ayudar a los usuarios a recuperar las compras existentes.