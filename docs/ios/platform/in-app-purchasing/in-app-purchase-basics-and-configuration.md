---
title: Conceptos básicos y configuración de compras desde la aplicación en Xamarin. iOS
description: En este documento se describen las compras desde la aplicación en Xamarin. iOS, que analizan información relevante sobre las reglas, la configuración y iTunes Connect.
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: 9347a801c939fd715101253c6953eeec840af47d
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288584"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>Conceptos básicos y configuración de compras desde la aplicación en Xamarin. iOS

La implementación de compras desde la aplicación requiere que la aplicación use la API de StoreKit en el dispositivo. StoreKit administra toda la comunicación con los servidores iTunes de Apple para obtener información del producto y realizar transacciones. El perfil de aprovisionamiento debe estar configurado para la compra desde la aplicación y la información del producto debe especificarse en iTunes Connect.

 [![](in-app-purchase-basics-and-configuration-images/image1.png "StoreKit administra todas las comunicaciones con Apple, tal como se muestra en este gráfico.")](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

El uso de App Store para proporcionar la compra desde la aplicación requiere la siguiente configuración y configuración:

- **iTunes Connect** : configuración de los productos para vender y configurar cuentas de usuario de espacio aislado para probar la compra. También debe haber proporcionado su información bancaria y fiscal a Apple para que puedan remitir los fondos recopilados en su nombre.
- **portal de aprovisionamiento de iOS** : creación de un identificador de paquete y habilitación del acceso al almacén de aplicaciones para la aplicación.
- **Kit de tienda** : agregar código a la aplicación para mostrar productos, comprar productos y restaurar transacciones.
- **Código personalizado** : para realizar el seguimiento de las compras realizadas por los clientes y proporcionar los productos o servicios que han adquirido. También es posible que necesite implementar un proceso del lado servidor para validar las confirmaciones si los productos se componen del contenido descargado de un servidor (como los libros y los problemas de la revista).


Hay dos "entornos de servidor" del kit de tiendas:

- **Producción** : transacciones con dinero real. Solo es accesible a través de aplicaciones enviadas y aprobadas por Apple. Los productos de compra desde la aplicación también deben revisarse y aprobarse antes de que estén disponibles en el entorno de producción.
- **Espacio aislado** : Dónde se producen las pruebas. Los productos están disponibles aquí inmediatamente después de la creación (el proceso de aprobación solo se aplica al entorno de producción). Las transacciones en el espacio aislado requieren que los usuarios de prueba (no los ID. de Apple reales) realicen transacciones.

## <a name="in-app-purchase-rules"></a>Reglas de compra desde la aplicación

No puede aceptar otras formas de pago para productos o servicios digitales dentro de la aplicación, ni mencionarlos ni hacer referencia a ellos a ellos desde dentro de una aplicación. Esto significa que no puede aceptar tarjetas de crédito ni PayPal cuando la compra desde la aplicación es el mecanismo de pago más adecuado. Hay un caso especial para la compra de productos digitales fuera de la aplicación, pero para su uso en la aplicación, como los libros de compras en un sitio web que están asociados a un "Inicio de sesión" específico y el uso de ese "Inicio de sesión" en la aplicación permite que el usuario acceda a los libros comprados.
Las aplicaciones que funcionan de esta manera no pueden mencionar ni vincularse a la característica de compra externa: los desarrolladores deben comunicar esta funcionalidad a sus usuarios de otras maneras (por ejemplo, a través de marketing por correo electrónico u otro canal directo).

Sin embargo, puesto que no se pueden usar compras desde la aplicación para las mercancías físicas, en ese caso se permite usar un mecanismo de pago alternativo (por ejemplo, tarjeta de crédito, PayPal) desde dentro de la aplicación.

Apple debe aprobar todos los productos antes de su venta: el nombre, la descripción y una captura de pantalla del "producto" son necesarios para su revisión. Las horas de revisión del producto son las mismas que para las revisiones de la aplicación.

No puede elegir ningún precio para el producto: solo puede seleccionar un "plan de tarifa" que tenga un valor específico en cada país o moneda que admita Apple. No puede tener un nivel de precios diferente en distintos mercados.

## <a name="configuration"></a>Configuración

Antes de escribir el código de compra en la aplicación, debe realizar algún trabajo de configuración en iTunes Connect ( [itunesconnect.Apple.com](http://itunesconnect.apple.com)) y en el portal de aprovisionamiento de iOS ( [Developer.Apple.com/iOS](https://developer.apple.com/iOS)).

Estos tres pasos se deben completar antes de escribir cualquier código:

- **Cuenta de desarrollador de Apple** : Envíe su información bancaria e impuestos a Apple.
- **portal de aprovisionamiento de iOS** : Asegúrese de que la aplicación tiene un identificador de aplicación válido (no un carácter comodín con un asterisco * en él) y tiene habilitada la adquisición de aplicaciones.
- **Administración de aplicaciones de iTunes Connect** : agregar productos a la aplicación.


### <a name="apple-developer-account"></a>Cuenta de desarrollador de Apple

La creación y distribución de aplicaciones gratuitas requiere muy poca configuración en [iTunes Connect](https://itunesconnect.apple.com), sin embargo, para vender aplicaciones de pago o compras desde la aplicación, es necesario proporcionar a Apple información bancaria y fiscal. Haga clic en **contratos, impuestos y banca** en el menú principal que se muestra aquí:

 [![](in-app-purchase-basics-and-configuration-images/image2.png "Haga clic en contratos, impuestos y banca en el menú principal.")](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

Su cuenta de desarrollador debe tener un contrato de **aplicaciones de pago de iOS** en vigor, tal como se muestra en esta captura de pantalla:

 [![](in-app-purchase-basics-and-configuration-images/image3.png "Su cuenta de desarrollador debe tener un contrato de aplicaciones de pago de iOS en vigor.")](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

No podrá probar ninguna funcionalidad de StoreKit hasta que tenga un contrato de **aplicaciones de pago de iOS** : las llamadas a StoreKit en el código producirán un error hasta que Apple haya procesado la información de **contratos, impuestos y banca** .

### <a name="ios-provisioning-portal"></a>Portal de aprovisionamiento de iOS

Las nuevas aplicaciones se configuran en la sección **identificadores de aplicaciones** del **portal de aprovisionamiento de iOS**. Para crear un nuevo identificador de aplicación, vaya al [centro de usuarios del portal de aprovisionamiento de iOS](https://developer.apple.com/membercenter/index.action), vaya a **certificados, identificadores y perfiles** sección del portal y haga clic en **identificadores** en *aplicaciones iOS*. A continuación, haga clic en "+" en la parte superior derecha para generar un nuevo identificador de aplicación.


El formulario para crear nuevos **identificadores de aplicación**

 tiene el siguiente aspecto:

 [![](in-app-purchase-basics-and-configuration-images/image4.png "El formulario para crear nuevos identificadores de aplicación")](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

Escriba algo apropiado para la *Descripción*, de modo que pueda identificar fácilmente este identificador de aplicación en una lista. En el *Prefijo de ID*. de aplicación, seleccione el identificador del equipo.

#### <a name="bundle-identifierapp-id-suffix-format"></a>Identificador de paquete/formato de sufijo de ID. de aplicación

Puede usar cualquier cadena que desee para su **identificador de lote** (siempre que sea único en su cuenta); sin embargo, Apple recomienda seguir el formato de DNS inverso en lugar de usar cualquier cadena arbitraria. La aplicación de ejemplo que acompaña a este artículo usa com. Xamarin. storekit. Testing para el identificador de paquete, pero sería igualmente válido usar un identificador como my_store_example (aunque Apple no lo recomiende).

> [!IMPORTANT]
> Apple también permite agregar asteriscos comodín al final de un **identificador de paquete** para que se pueda usar un único identificador de aplicación para varias aplicaciones. sin embargo, los _identificadores de aplicación de la tarjeta no se pueden usar para in-AppPurchase_. Un identificador de lote de tarjetas comodín de ejemplo podría ser com. Xamarin. *

#### <a name="enabling-app-services"></a>Habilitar App Services

Tenga en cuenta que la **compra desde la aplicación** se habilitará automáticamente en la lista de servicios:

 [![](in-app-purchase-basics-and-configuration-images/image5.png "La compra desde la aplicación se habilitará automáticamente en la lista de servicios.")](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>Perfiles de aprovisionamiento

Cree perfiles de aprovisionamiento de desarrollo y producción como lo haría normalmente, seleccionando el identificador de aplicación que ha configurado para la compra desde la aplicación. Consulte las guías [aprovisionamiento de dispositivos iOS](~/ios/get-started/installation/device-provisioning/index.md) y [publicación en la tienda de aplicaciones](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) para obtener más información.

## <a name="itunes-connect"></a>iTunes Connect

Haga clic en **mis aplicaciones** en iTunes Connect para crear o editar una entrada de aplicación de iOS. La página información general de la aplicación se muestra aquí:

 [![](in-app-purchase-basics-and-configuration-images/image6.png "Página información general de la aplicación")](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

Haga clic **en compras desde la aplicación** para crear o editar productos para su venta. En esta captura de pantalla se muestra la aplicación de ejemplo con varios productos ya agregados:

 [![](in-app-purchase-basics-and-configuration-images/image7.png "La aplicación de ejemplo con varios productos ya agregados")](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

El proceso para agregar nuevos productos tiene dos pasos:

1. Elija el tipo de producto:  [![](in-app-purchase-basics-and-configuration-images/image8.png "Elegir el tipo de producto")](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2. Escriba los atributos del producto, incluido el ID. de producto, el plan de tarifa y las descripciones localizadas:  [![](in-app-purchase-basics-and-configuration-images/image9.png "Especificar los atributos de los productos")](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

Los campos necesarios para cada producto de compra desde la aplicación se describen a continuación:


### <a name="reference-name"></a>Nombre de referencia

El nombre de referencia no se muestra a los usuarios; es para uso interno y solo aparece en iTunes Connect.

### <a name="product-id-format"></a>Formato de ID. de producto

Un identificador de producto solo puede contener caracteres alfanuméricos (A-Z, a-z, 0-9), subrayado (_) y punto (.). Aunque puede usar cualquier cadena para los identificadores, Apple recomienda el formato DNS inverso. Por ejemplo, la aplicación de ejemplo usa este identificador de paquete:

 `com.xamarin.storekit.testing`

Por lo tanto, la Convención para identificar productos de compra en la aplicación sería la siguiente:

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

Esta Convención de nomenclatura no se aplica, solo una recomendación para ayudarle a administrar sus productos. Además, a pesar de seguir la misma Convención de DNS inverso, los identificadores de producto *no están relacionados* con el identificador de paquete y no se necesitan para empezar con la misma cadena. Todavía sería válido usar identificadores como photo_product_greyscale (aunque Apple no lo recomiende).

El identificador de producto no se muestra a los usuarios, pero se usa para hacer referencia al producto en el código de la aplicación.

### <a name="product-type"></a>Tipo de producto

Hay cinco tipos de productos de compra desde la aplicación que puede ofrecer:

1. **Consumible** : cosas que se "usan", como la moneda del juego que el jugador puede gastar. Si el usuario realiza una copia de seguridad o una restauración, o si se actualiza su dispositivo, no se restaura también una transacción consumible (lo que haría que el reproductor se conferira la misma ventaja). El código de aplicación debe asegurarse de proporcionar el ' elemento consumible ' en cuanto se complete la transacción.
1. **No consumible** : productos que el usuario posee una vez comprados, como un problema de la revista digital o un nivel de juego.
1. **Suscripciones renovables** automáticamente: al igual que una suscripción de revista del mundo real, al final del período de suscripción, Apple cobra automáticamente el cliente de nuevo y amplía el período de suscripción, siempre o hasta que el cliente lo cancela explícitamente. Este es el método de pago preferido para las aplicaciones de NewsStand (de hecho, las aplicaciones deben admitir este método de pago para su aprobación para la distribución de NewsStand).
1. **Suscripción gratuita** : solo se puede ofrecer en aplicaciones habilitadas para NewsStand y permite que el cliente tenga acceso al contenido de la suscripción en todos sus dispositivos. Las suscripciones gratuitas nunca expiran.
1. **Suscripción sin renovación** : debe usarse para vender acceso limitado de tiempo a contenido estático, como el acceso de un mes a un archivo de fotografías.


 *Actualmente, en este documento solo se tratan los dos primeros tipos de productos (consumibles y no consumibles).*

 <a name="Price_Tiers" />

### <a name="price-tiers"></a>Planes de tarifa

La tienda de aplicaciones no le permite elegir un precio arbitrario para los productos: Apple proporciona niveles de precio fijos entre los que puede elegir. Los precios se fijan en cada moneda y Apple se reserva el derecho de ajustar los precios relativos (por ejemplo, después de un cambio sostenido en la tasa de cambio de divisa relativa entre una moneda determinada y el dólar estadounidense).

Apple proporciona una matriz de precios para ayudarle a seleccionar el nivel correcto de la moneda o el precio que desee. A continuación se muestra un extracto de la matriz de precios (2012 de agosto):

 [![](in-app-purchase-basics-and-configuration-images/image10.png "Un extracto de la matriz de precios de agosto de 2012")](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

En el momento de la escritura (2013 de junio), hay 87 niveles de 0,99 USD a 999,99 USD. La matriz de precios muestra el precio que los clientes pagarán y también el importe que recibirá de Apple: se trata de un descuento del 30% y también de los impuestos locales que se necesitan para recopilar (observe en el ejemplo que los vendedores de Estados Unidos y Canadá reciben 70C para 99C p producto, mientras que los vendedores de Australia solo reciben 63c debido a &amp; «impuesto de los servicios de bienes» aplicado el precio de venta).

Los precios del producto pueden actualizarse en cualquier momento, incluidos los cambios de precio programados que se aplican en una fecha futura. En esta captura de pantalla se muestra cómo se agrega un cambio de precio con fecha futura: el precio se cambia temporalmente de nivel 1 a nivel 3 para el mes de septiembre únicamente:

 [![](in-app-purchase-basics-and-configuration-images/image11.png "Un cambio de precio con fecha en el futuro en el que se cambia temporalmente el precio del nivel 1 al nivel 3 para el mes de septiembre")](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>Productos gratuitos no admitidos

Aunque Apple ha proporcionado una opción de suscripción gratuita especial para las aplicaciones de NewsStand, no es posible establecer un precio cero (gratuito) para cualquier otro tipo de compra en la aplicación. Aunque puede editar los precios (IE. Lower) de promociones de ventas, no puede realizar compras desde la aplicación "gratis" a través de iTunes Connect.

### <a name="localization"></a>Localización

En iTunes Connect puede escribir un nombre diferente y texto descriptivo para cualquier número de idiomas admitidos. Cada idioma se puede Agregar o editar en a través de un elemento emergente:

 [![](in-app-purchase-basics-and-configuration-images/image12.png "Cada idioma se puede Agregar o editar en a través de un elemento emergente.")](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   
   
   
   
 Cuando se muestra información del producto en la aplicación, el texto localizado está disponible para que se muestre a través de StoreKit. La presentación de la moneda también debe estar localizada para mostrar el símbolo y el formato decimal correctos; este formato se trata más adelante en el documento.

### <a name="app-store-review"></a>Revisión de App Store

Igual que las aplicaciones: Apple revisa todos los productos antes de que se les permita su venta. Es posible que se rechacen los productos por contenido inadecuado en el nombre o la descripción, o que Apple decida que ha elegido el tipo de producto incorrecto (por ejemplo, ha creado un problema de libro o revista pero ha usado el tipo de producto consumible). Las revisiones del producto pueden tardar tanto como una revisión de la aplicación.

La primera vez que se envía una aplicación con la opción de compra desde la aplicación habilitada (ya sea una nueva aplicación o se ha agregado la funcionalidad a una existente), también debe elegir algunos productos para enviarlos. El portal de iTunes Connect le pedirá que lo haga, tal como se muestra en esta captura de pantalla:

 [![](in-app-purchase-basics-and-configuration-images/image13.png "El portal de iTunes Connect le pedirá que envíe también algunos productos.")](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   
   
   
   
 La aplicación y las compras desde la aplicación se revisarán juntas, de modo que todos se aprueben a la vez (de modo que la aplicación no vaya al almacén sin ningún producto aprobado).

Una vez aprobada su primera versión con la funcionalidad de compra desde la aplicación, puede agregar más productos y enviarlos para su revisión en cualquier momento. También puede optar por enviar una nueva versión junto con productos específicos de compras desde la aplicación, mediante la página de detalles de la **versión** , como sugiere la solicitud.

Consulte las [directrices de revisión del App Store](https://developer.apple.com/appstore/guidelines.html) para obtener más información.

 [Parte 2: Introducción al kit de almacenamiento y recuperando información del producto](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
