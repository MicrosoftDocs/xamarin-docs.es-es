---
title: Cambios en la tienda de aplicaciones en iOS 11
description: En este documento se exploran los cambios en la tienda de aplicaciones en iOS 11. Describe el icono de almacén de una aplicación, las compras desde la aplicación promocionadas, la página de productos rediseñada, la comunicación con los clientes y las versiones por fases.
ms.prod: xamarin
ms.assetid: 4A7A03FD-B4F2-4969-8676-A17260730FD6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 09/13/2016
ms.openlocfilehash: 0ac9b486defb74cac7ccd946d2b35b283e6aeca5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70752327"
---
# <a name="app-store-changes-in-ios-11"></a>Cambios en la tienda de aplicaciones en iOS 11

El App Store de iOS ha tenido un rediseño completo, que no solo permite a los usuarios navegar de forma eficaz por la tienda, sino que también le permite promover su aplicación a los usuarios. Estas promociones incluyen actualizaciones de compras desde la aplicación y actualizaciones en la página del producto. iOS 11 también agrega actualizaciones sobre cómo comunicarse con los usuarios, cómo agregar el icono de la aplicación y cómo publicar la aplicación en el público.

![Nuevo diseño de App Store](app-store-changes-images/image3.jpg)

La tienda de aplicaciones rediseñada tiene las siguientes secciones:

- **Hoy en día** : esta pestaña incluye aplicaciones que son una "selección del editor" o una aplicación destacada. Para promocionar su aplicación aquí, rellene la información de la página [promocionar](https://developer.apple.com//contact/app-store/promote/) .
- **Juegos** : las aplicaciones que se establecen en la categoría de **juego** en iTunes Connect se pueden encontrar en esta pestaña.
- **Aplicaciones** : esta pestaña incluye todas las demás aplicaciones. Los usuarios pueden navegar por aplicaciones destacadas, categorías, con un máximo pago/gratis.
- **Actualizaciones** : esta aplicación muestra las actualizaciones de las aplicaciones. Incluso si publica una aplicación a través de la [versión por fases](#Phased_Release), los usuarios todavía pueden ir a esta pestaña y descargar la versión más reciente.
- **Buscar** : esta pestaña permite que los usuarios busquen la aplicación.

## <a name="store-icon"></a>Icono de tienda

Los iconos de tienda (o los iconos de marketing) ya no se administran en iTunes Connect y, en su lugar, se deben incluir como un [Catálogo de recursos](~/ios/app-fundamentals/images-icons/app-icons.md) en el archivo binario de la aplicación, de forma similar a los iconos de la aplicación. Se debe incluir un icono de almacén 1024 x 1024 en formato PNG en un catálogo de recursos para el envío correcto de aplicaciones iOS 11.

La reducción de la aplicación garantiza que este catálogo de recursos adicional no aumente el tamaño de la aplicación.

## <a name="in-app-purchases-promoted-in-the-app-store"></a>Compras desde la aplicación promocionadas en la tienda de aplicaciones

Apple ha realizado compras desde la aplicación más reconocible en el App Store. Ahora puede Agregar hasta 20 compras desde la aplicación _promocionadas_ en la tienda de aplicaciones que ahora se pueden encontrar en la página aplicaciones, en la página del producto de la aplicación o mediante la búsqueda.

Para que las compras desde la aplicación se muestren en la tienda de aplicaciones, debe incluir los datos siguientes:

- **Imagen** : debe proporcionar una imagen especialmente diseñada para el icono que describe lo que hace la compra desde la aplicación. Esta imagen debe ser diferente del icono de la aplicación y no puede ser una captura de pantalla.
- **Nombre** : el nombre solo puede tener 30 caracteres como máximo.
- **Descripción** : la descripción solo puede tener un máximo de 45 caracteres.

Las promociones de compras desde la aplicación están sujetas a una revisión de la tienda de aplicaciones antes de que se puedan publicar.

Para que las compras desde la aplicación estén disponibles para promocionar, abra la aplicación y vaya a **características > compras desde la aplicación**. Vaya a la sección de promoción de la **tienda de aplicaciones (opcional)** y agregue una imagen 1024 x 1024 y **guárdela**, tal como se muestra en la siguiente imagen:

![Sección de promoción de App Store en iTunes Connect](app-store-changes-images/image4.png)

También debe agregar el `ShouldAddStorePayment` método `SKPaymentTransactionObserver` al protocolo en la aplicación.

Para obtener más información sobre las promociones de compras desde la aplicación, consulte la página sobre la [promoción de la compra desde la aplicación](https://developer.apple.com/app-store/promoting-in-app-purchases/) de Apple.

## <a name="redesigned-product-page"></a>Página del producto rediseñado

Se han realizado los siguientes cambios en la página del producto:

- Los títulos ahora se establecen en un máximo de 30 caracteres.
- Se ha agregado un subtítulo
  - Debe ser un breve resumen que complemente el título.
  - El subtítulo debe tener un máximo de 30 caracteres
- Versiones preliminares de la aplicación
  - Ahora puede tener tres vídeos o capturas de pantallas.
  - Vídeos reproducción automática cuando un usuario visita la página.
  - Para obtener más información, consulte la página de [vista previa de aplicaciones](https://developer.apple.com/app-store/app-previews/) de Apple.
- Texto promocional
  - Esta nueva característica proporciona 170 caracteres de texto que le permiten describir información de cambio frecuente sobre la aplicación.
  - Se puede actualizar en cualquier momento sin necesidad de enviar una nueva versión de la aplicación.

## <a name="customer-communication"></a>Comunicación con los clientes

En 10,3, Apple lanzó una nueva forma para que los desarrolladores se comuniquen directamente con los usuarios de la aplicación a través de la capacidad de responder a las revisiones. Puede tener acceso a esta nueva funcionalidad en iTunes Connect si va a la **actividad de > de la aplicación > clasificaciones y revisiones**, como se muestra en la siguiente imagen:

![Cuadro de diálogo que muestra el área para escribir la respuesta al comentario](app-store-changes-images/image5.png)

Hay algunos aspectos que se deben tener en cuenta al responder a los usuarios:

- Solo puede responder una vez, pero ambas partes pueden editar sus comentarios tantas veces como deseen.
- Los usuarios reciben una notificación cuando se responde a un comentario.
- Se ha creado un nuevo rol de soporte al cliente en iTunes Connect. Los usuarios con este rol o un rol de administrador pueden responder a los comentarios.

Para obtener más información, consulte la página de respuesta de Apple [a las revisiones](https://developer.apple.com/app-store/responding-to-reviews/) .

<a name="Phased_Release"/>

## <a name="phased-release"></a>Versión por fases

Con iOS 11, Apple ha implementado la opción de versiones por fases para las actualizaciones de la aplicación. Puede habilitar versiones por fases para permitir que la actualización de la aplicación se lance gradualmente a los clientes, asegurándose de que la demanda no sobrecargará el entorno de producción.

Las versiones por fases están habilitadas en iTunes Connect. Haga clic en la aplicación en la barra lateral, desplácese hasta la sección **lanzamiento por fases de actualizaciones automáticas** en la parte inferior y seleccione actualización de versión en un **período de 7 días mediante la versión por fases**:

![Opción que muestra la versión por fases para las actualizaciones automáticas](app-store-changes-images/image6.png)

La actualización está disponible inmediatamente para su descarga en la pestaña actualizaciones de la tienda de aplicaciones. Las versiones por fases solo están disponibles para los usuarios que tienen seleccionada la opción Descargas automáticas.

## <a name="related-links"></a>Vínculos relacionados

- [Novedades de iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página del producto de App Store actualizada (Apple)](https://developer.apple.com/app-store/product-page/)
- [Actualización de la aplicación para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
