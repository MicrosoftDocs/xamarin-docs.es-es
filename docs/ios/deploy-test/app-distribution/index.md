---
title: Introducción a la distribución de aplicaciones Xamarin.iOS
description: Este documento contiene información general sobre las técnicas de distribución que están disponibles para las aplicaciones de Xamarin.iOS y actúa como un punto de partida a documentos más detallados sobre el tema.
ms.prod: xamarin
ms.assetid: 341D36DB-BB07-FA94-BCC9-5F8C0B18C179
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: 20126849027f735e9ecd3599c290b4e7a57f837e
ms.sourcegitcommit: ec62e2624295aa502ec35ac782031d61d61c3aaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75886585"
---
# <a name="xamarinios-app-distribution-overview"></a>Introducción a la distribución de aplicaciones Xamarin.iOS

_Este documento contiene información general sobre las técnicas de distribución que están disponibles para las aplicaciones Xamarin.iOS y actúa como un punto de partida a documentos más detallados sobre el tema._

Una vez que se ha desarrollado una aplicación de Xamarin.iOS, el siguiente paso del ciclo de vida de desarrollo de software es distribuirla a los usuarios, como se muestra en la sección destacada del siguiente diagrama:

[![Una vez que se ha desarrollado la aplicación iOS, el siguiente paso consiste en distribuirla a los usuarios, como se muestra en la sección resaltada de este diagrama](images/publishingdiagram.png)](images/publishingdiagram.png#lightbox)

Apple proporciona los siguientes métodos para distribuir una aplicación de iOS:

- [**App Store**](#app-store-distribution)
- [**Interna (Enterprise)** ](#in-house-distribution)
- [**Ad Hoc**](#ad-hoc-distribution)
- [**Aplicaciones personalizadas para empresas**](#custom-apps-for-business)

Todos estos escenarios requieren que las aplicaciones se aprovisionen mediante el correspondiente *perfil de aprovisionamiento*. Los perfiles de aprovisionamiento son archivos que contienen información de firma de código, así como la identidad de la aplicación y el mecanismo de distribución previsto. También contienen información sobre en qué dispositivos se puede implementar la aplicación para la distribución que no se realice a través del App Store.

## <a name="app-store-distribution"></a>Distribución a través del App Store

> [!IMPORTANT]
> Apple [ha comunicado](https://developer.apple.com/ios/submit/) que, a partir de marzo de 2019, las aplicaciones y actualizaciones que se envíen al App Store deberán haberse compilado con el SDK de iOS 12.1 o posterior, incluido en Xcode 10.1 y versiones posteriores.
> Las aplicaciones también deberán admitir los tamaños de pantalla del iPhone XS y el iPad Pro de 12.9".

Se trata de la forma principal mediante la que se distribuyen las aplicaciones de iOS a los consumidores en dispositivos iOS. Todas las aplicaciones que se envían a la App Store requieren la aprobación de Apple.

Las aplicaciones se envían a la App Store a través de un portal llamado *iTunes Connect*. La guía [Configurar la aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) proporciona más información sobre cómo configurar y usar este portal para preparar una aplicación de Xamarin.iOS para su publicación en el App Store.

Es importante tener en cuenta que solo los desarrolladores que pertenecen al **Programa para desarrolladores de Apple** tienen acceso a iTunes Connect. Los miembros del **Programa para desarrolladores empresariales de Apple** no tienen acceso.

Para obtener más información, visite la guía [Distribución a través del App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md).

## <a name="in-house-distribution"></a>Distribución interna

A veces denominada *Distribución empresarial*, la distribución interna permite a los miembros del **Programa para desarrolladores empresariales de Apple** distribuir aplicaciones internamente a otros miembros de la misma organización. La distribución interna tiene las ventajas de no requerir una revisión de la App Store y no tener ningún límite en el número de dispositivos en los que se puede instalar una aplicación. Sin embargo, es importante tener en cuenta que los miembros del **Programa para desarrolladores empresariales de Apple** **no** tienen acceso a iTunes Connect y, por lo tanto, el licenciatario es responsable de distribuir la aplicación.

Para obtener más información sobre cómo configurar y cómo distribuir una aplicación de forma interna, consulte la [Guía de distribución interna](~/ios/deploy-test/app-distribution/in-house-distribution.md).

## <a name="ad-hoc-distribution"></a>Distribución ad hoc

Los usuarios pueden probar las aplicaciones de Xamarin.iOS a través de la distribución ad hoc, que está disponible tanto en el **Programa para desarrolladores de Apple** y el **Programa para desarrolladores empresariales de Apple**, y permite realizar la prueba en un máximo de 100 dispositivos iOS. El mejor caso de uso para la distribución ad hoc es la distribución dentro de una empresa cuando iTunes Connect no es una opción.

Para obtener más información sobre cómo configurar y cómo distribuir una aplicación de forma interna, consulte la [Guía de distribución ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md).

## <a name="custom-apps-for-business"></a>Aplicaciones personalizadas para empresas

Apple permite la [distribución personalizada](https://developer.apple.com/business/custom-apps/) de aplicaciones a empresas e instituciones educativas. Revise la [Guía de usuario de Apple Business Manager](https://support.apple.com/guide/apple-business-manager/welcome/web) para obtener información.

## <a name="related-links"></a>Vínculos relacionados

- [Distribución a través del App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Configuración de una aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publicación en App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribución interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribución ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Archivo iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Compatibilidad con IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
- [Solución de problemas](~/ios/deploy-test/troubleshooting.md)
