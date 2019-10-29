---
title: Actualización de la aplicación a iOS 11
description: Este documento contiene vínculos a varias guías que describen las nuevas características disponibles para los desarrolladores de Xamarin. iOS con la versión de iOS 11. Por ejemplo, actualizaciones de diseño visual, cambios en el almacén de aplicaciones y actualizaciones de iconos de aplicación.
ms.prod: xamarin
ms.assetid: EC809504-9CF6-4949-B6EE-36384297E744
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 81d863b12aa7748acc6876929fb5d6361a20b507
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032118"
---
# <a name="updating-your-app-to-ios-11"></a>Actualización de la aplicación a iOS 11

En iOS 11, Apple ha incorporado actualizaciones de arquitectura, nuevos cambios visuales y un proceso de iTunes Connect actualizado. Esta guía explora cada uno de estos cambios, lo que le ayuda a obtener la aplicación de Xamarin. iOS actualizada para iOS 11.

## <a name="architecture-changesarchitecture-changesmd"></a>[Cambios de arquitectura](architecture-changes.md)

Uno de los cambios más importantes que debe tener en cuenta con iOS 11 es el desuso de la compatibilidad de 32 bits con las aplicaciones, tal como se detalla en el comunicado de prensa de [Apple](https://developer.apple.com/news/?id=06282017b) .

Esta guía le guiará a través de la actualización de la aplicación para 64 bits.

## <a name="visual-design-updatesvisual-designmd"></a>[Actualizaciones de diseño Visual](visual-design.md)

En iOS 11, Apple presentó nuevos cambios visuales, como las actualizaciones de la barra de navegación, la barra de búsqueda y las vistas de tabla. Además, se han realizado mejoras para permitir una mayor flexibilidad sobre los márgenes y el contenido de pantalla completa. Estos cambios se describen en esta guía.

## <a name="app-store-changesapp-store-changesmd"></a>[Cambios en App Store](app-store-changes.md)

El App Store de iOS ha tenido un rediseño completo, que no solo permite a los usuarios navegar de forma eficaz por la tienda, sino que también le permite promover su aplicación a los usuarios. Estas promociones incluyen actualizaciones de compras desde la aplicación y actualizaciones en la página del producto. iOS 11 también agrega actualizaciones sobre cómo comunicarse con los usuarios, cómo agregar el icono de la aplicación y cómo publicar la aplicación en el público.

## <a name="app-icon-updates"></a>Actualizaciones de iconos de aplicación

> [!NOTE]
> Ahora, un _Catálogo de recursos_debe entregar iconos de aplicación. 

Para obtener información sobre el uso de los catálogos de recursos, consulte la guía de [iconos de App Store](~/ios/app-fundamentals/images-icons/app-store-icon.md) . Para obtener ayuda con la migración de iconos de info. plist a un catálogo de recursos, consulte la guía [migración desde info. plist a catálogos de recursos](~/ios/app-fundamentals/images-icons/app-icons.md) .

El icono necesario en el catálogo de recursos se denomina **App Store** y debe tener un tamaño de 1024 x 1024. Apple ha manifestado que el icono de App Store del catálogo de recursos no puede ser transparente ni contener un canal alfa.

![Ubicación del icono de App Store en el catálogo de recursos.](images/image1.png)

## <a name="related-links"></a>Vínculos relacionados

- [Novedades de iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Página del producto de App Store actualizada (Apple)](https://developer.apple.com/app-store/product-page/)
- [Actualización de la aplicación para iOS 11 (WWDC) (vídeo)](https://developer.apple.com/videos/play/wwdc2017/204/)
