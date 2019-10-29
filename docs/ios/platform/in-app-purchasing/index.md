---
title: Compras desde la aplicación en Xamarin. iOS
description: En este documento se describe cómo vender productos y servicios digitales mediante las API de StoreKit. Incluye vínculos a guías que describen la configuración, los productos consumibles, los productos que no se usan, las transacciones, las suscripciones, etc.
ms.prod: xamarin
ms.assetid: B41929D8-47E4-466D-1F09-6CC3C09C83B2
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 1efb8df4bac307d0e73da6bcbf645077cb63c839
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032357"
---
# <a name="in-app-purchasing-in-xamarinios"></a>Compras desde la aplicación en Xamarin. iOS

las aplicaciones de iOS pueden vender productos o servicios digitales mediante StoreKit: un conjunto de API proporcionadas por iOS que se comunican con los servidores de Apple para realizar transacciones financieras con el usuario a través de su identificador de Apple. Las API de StoreKit están preocupadas principalmente por la recuperación de información del producto y la realización de transacciones, ya que no hay ningún componente de la interfaz de usuario. Las aplicaciones que implementan la compra desde la aplicación deben crear su propia interfaz de usuario y realizar un seguimiento de los elementos adquiridos con código personalizado para proporcionar al usuario los productos o servicios necesarios.

Proporcionar la funcionalidad de compra desde la aplicación requiere una serie de pasos:

- **Configuración de la aplicación** : el perfil de aprovisionamiento de la aplicación debe configurarse correctamente.
- **Creación de productos** : las descripciones y los precios del producto deben crearse en el portal de iTunes Connect.
- **Implementación de StoreKit** : la API de StoreKit debe implementarse según los tipos de productos que se venden.
- **Crear la interfaz de usuario y los propios productos** : se deben implementar los productos, incluidos los mecanismos para realizar el seguimiento de cada compra y copia de seguridad o restauración si es necesario.
- **Supervisión de ventas y recepción de fondos** : Use la información proporcionada por iTunes Connect para supervisar las tendencias de ventas y realizar un seguimiento de sus ingresos.

En este documento se explica cómo completar todos estos pasos para proporcionar compras desde la aplicación mediante Xamarin. iOS.

## <a name="requirements"></a>Requisitos

Para admitir la compra desde la aplicación, debe usar Xamarin. iOS 5,0 o una versión más reciente con Xcode 7 y versiones posteriores.

## <a name="contents"></a>Contenido

- [Configuración y conceptos básicos de las compras desde la aplicación](~/ios/platform/in-app-purchasing/in-app-purchase-basics-and-configuration.md)

- [Información general de StoreKit y recuperación de información de productos](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)

- [Compra de productos consumibles](~/ios/platform/in-app-purchasing/purchasing-consumable-products.md)

- [Compra de productos no consumibles](~/ios/platform/in-app-purchasing/purchasing-non-consumable-products.md)

- [Transacciones y comprobación](~/ios/platform/in-app-purchasing/transactions-and-verification.md)

- [Suscripciones e informes](~/ios/platform/in-app-purchasing/subscriptions-and-reporting.md)

## <a name="summary"></a>Resumen

En este artículo se ha introducido el concepto de compra desde la aplicación, en el que se describe cómo configurar la aplicación para aprovecharse de ella y se presentan ejemplos con Xamarin. iOS. Ha tratado:

- **portal de aprovisionamiento de iOS** : directrices para habilitar la funcionalidad de compras desde la aplicación.
- **iTunes Connect** : configuración de los productos que se van a vender en la aplicación.
- **Kit de tiendas** : explicación de las clases que se usan para compilar características de compras desde la aplicación.
- **Codificación de la aplicación para la** compra: ejemplos de cómo compilar la compra desde la aplicación en una aplicación de Xamarin. iOS.
- **Informes** : información general de las estadísticas disponibles a través de iTunes Connect.

## <a name="related-links"></a>Vínculos relacionados

- [InAppPurchaseSample](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit/)
- [En la guía de programación de compras de aplicaciones](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/StoreKitGuide/Introduction.html)
- [Guía para desarrolladores de iTunes Connect](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/iTunesConnect_Guide.pdf)
- [Referencia de Framework del kit de tiendas](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/StoreKit_Collection/StoreKit_Collection.pdf)
- [Identificadores de producto de compra desde la aplicación Q & A](https://developer.apple.com/library/ios/#qa/qa1329/_index.html)
- [Nota técnica de compras desde la aplicación](https://developer.apple.com/library/ios/#technotes/tn2259/_index.html)
- [El primer envío de la tienda de aplicaciones](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
- [Centro de recursos de App Store](https://developer.apple.com/appstore/index.html)
- [Sugerencias de envío al App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Directrices de revisión del App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Administración de las aplicaciones](https://developer.apple.com/appstore/resources/managing/index.html)
