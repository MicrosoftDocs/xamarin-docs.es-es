---
title: Configuración de la aplicación tvOS en iTunes Connect
description: En este artículo se proporciona una guía complementaria a la configuración de iOS de la aplicación en iTunes Connect para las configuraciones específicas de tvOS.
ms.prod: xamarin
ms.assetid: 86C7C5BD-C97D-4F1D-B611-A7694557BFDF
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 528eee6ade7236d83d5a5bdec6b27ba76f540ad0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649153"
---
# <a name="configure-your-tvos-app-in-itunes-connect"></a>Configuración de la aplicación tvOS en iTunes Connect

_En este artículo se proporciona una guía complementaria a la configuración de iOS de la aplicación en iTunes Connect para las configuraciones específicas de tvOS._


Además de las configuraciones y los valores que necesitará realizar mediante la guía de configuración de iOS de la [aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) , en este documento se describen las configuraciones específicas que se requerirán para liberar una aplicación Xamarin. tvOS en la aplicación Apple TV. Restaura.

<a name="Adding-a-tvOS-Release-Version" />

## <a name="adding-a-tvos-release-version"></a>Agregar una versión de lanzamiento de tvOS

Tanto si va a crear una nueva aplicación para su lanzamiento en la App Store de Apple TV como si va a agregar soporte técnico de Apple TV a una aplicación de iOS existente, deberá haber creado un registro de iTunes Connect y configurarlo mediante las siguientes guías específicas de iOS:

- [Crear un registro de iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Administración de vídeos y capturas de pantalla de aplicaciones](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#managing)
- [Administración de nombres, descripciones, novedades, palabras clave y direcciones URL](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#metadata)
- [Mantenimiento de la información general](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#general)

Opcionalmente, puede que también necesite:

- [Mantener la información del Game Center](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#game-center)
- [Mantener la información de compra en la aplicación](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#iap)

Una vez completados todos los pasos anteriores, abra el registro de iTunes Connect de la aplicación y seleccione Agregar compatibilidad con tvOS mediante la barra lateral izquierda:

[![](itunes-connect-images/connect01.png "Incorporación de compatibilidad con tvOS mediante la barra lateral izquierda")](itunes-connect-images/connect01.png#lightbox)

Las pantallas de información específicas de tvOS estarán disponibles para el registro de iTunes Connect dado:

[![](itunes-connect-images/connect02.png "Pantalla de información específica de tvOS")](itunes-connect-images/connect02.png#lightbox)

<a name="tvOS-Version-Information" />

## <a name="tvos-version-information"></a>Información de versión de tvOS

En la barra lateral izquierda, seleccione **1,0 preparar para el envío** en la sección de la aplicación tvOS:

[![](itunes-connect-images/connect03.png "Información de versión de tvOS")](itunes-connect-images/connect03.png#lightbox)

En esta pantalla, proporcione la siguiente información:

- Las capturas de pantallas, la descripción, las palabras clave y las direcciones URL necesarias.
- Información general de la aplicación, como el número de versión, copyright y clasificación de edad.
- Compras desde la aplicación opcionales.
- Compatibilidad opcional Game Center con los marcadores y los logros.
- Información de revisión de la aplicación necesaria como contacto, cuentas de demostración y notas.

Una vez que haya escrito la información necesaria, haga clic en el botón **Guardar** en la esquina superior derecha de la pantalla para guardar los cambios:

[![](itunes-connect-images/connect04.png "Información de versión de tvOS lista para su envío")](itunes-connect-images/connect04.png#lightbox)

<a name="Submitting-for-Review" />

## <a name="preparing-to-submit-for-review"></a>Preparación para enviar para revisión

Cuando esté listo para enviar la aplicación de Xamarin. tvOS a la tienda de aplicaciones de Apple TV para su revisión, vuelva al registro de iTunes Connect de la aplicación y haga clic en el botón **enviar para revisión** en la esquina superior derecha de la pantalla:

[![](itunes-connect-images/connect05.png "Enviar para revisión")](itunes-connect-images/connect05.png#lightbox)

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se proporciona información general sobre la configuración específica de tvOS requerida en iTunes Connect para publicar una aplicación de tvOS en la App Store de Apple TV.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
