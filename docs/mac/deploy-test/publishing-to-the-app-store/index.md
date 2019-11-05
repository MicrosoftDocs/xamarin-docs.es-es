---
title: Publicación de aplicaciones Xamarin.Mac en el Mac App Store
description: En este documento se describe cómo implementar una aplicación Xamarin.Mac con Visual Studio para Mac. Se explica cómo se configura una cuenta de desarrollador de Mac, cómo se crean certificados para la firma de código y cómo se usan para compilar aplicaciones de Mac que se puedan distribuir directamente o a través del Mac App Store.
ms.prod: xamarin
ms.assetid: D26C5E54-EAD2-5487-264D-4263AEA1EBF2
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 60f51be5ffad67d839edd42b7826b0c57c46eb8a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029992"
---
# <a name="publishing-xamarinmac-apps-to-the-mac-app-store"></a>Publicación de aplicaciones Xamarin.Mac en el Mac App Store

## <a name="overview"></a>Información general

Las aplicaciones de Xamarin.Mac pueden distribuirse de dos maneras diferentes:

- **Developer ID**: las aplicaciones firmadas con un Developer ID se distribuyen fuera del App Store, pero Gatekeeper las reconoce y les da permiso para que se instalen.
- **Mac App Store**: las aplicaciones deben tener un paquete de instalación, y la aplicación y el programa de instalación deben estar firmados para enviarse al Mac App Store.

En este documento se explica cómo utilizar Visual Studio para Mac y Xcode para configurar una cuenta de desarrollador de Apple y configurar un proyecto de Xamarin.Mac para cada tipo de implementación.

## <a name="mac-developer-program"></a>Programa para desarrolladores de Mac

Al unirse al [Programa para desarrolladores de Mac](https://developer.apple.com/devcenter/mac/), el desarrollador tendrá la opción de hacerlo como una persona o una empresa, como se muestra en la captura de pantalla siguiente:

[![Portal para desarrolladores de Apple](images/image1.png "Portal para desarrolladores de Apple")](images/image1-large.png#lightbox)

Elija el tipo de inscripción correcto para su situación.

> [!NOTE]
> Las selecciones realizadas aquí afectarán al modo en que algunas pantallas aparecen al configurar una cuenta de desarrollador. Las descripciones y las capturas de pantalla de este documento se realizan desde la perspectiva de una cuenta de desarrollador **personal**. En una **empresa**, algunas opciones solo estarán disponibles para los usuarios del **Administrador del equipo**.

### <a name="certificates-and-identifiersmacdeploy-testpublishing-to-the-app-storecertificates-identifiersmd"></a>[Certificados e identificadores](~/mac/deploy-test/publishing-to-the-app-store/certificates-identifiers.md)

En esta guía se describe el proceso de creación de los certificados y los identificadores necesarios para publicar una aplicación Xamarin.Mac.

### <a name="create-provisioning-profilemacdeploy-testpublishing-to-the-app-storeprofilesmd"></a>[Crear un perfil de aprovisionamiento](~/mac/deploy-test/publishing-to-the-app-store/profiles.md)

En esta guía se describe el proceso de creación de los perfiles de aprovisionamiento necesarios para publicar una aplicación Xamarin.Mac.

### <a name="mac-app-configurationmacdeploy-testpublishing-to-the-app-storeapp-configurationmd"></a>[Configuración de aplicaciones de Mac](~/mac/deploy-test/publishing-to-the-app-store/app-configuration.md)

En esta guía se describe el proceso de configuración de una aplicación Xamarin.Mac para su publicación.

### <a name="sign-with-developer-idmacdeploy-testpublishing-to-the-app-storesigningmd"></a>[Firmar con Developer ID](~/mac/deploy-test/publishing-to-the-app-store/signing.md)

En esta guía se explican los pasos para firmar una aplicación Xamarin.Mac con Developer ID para su publicación.

### <a name="bundle-for-mac-app-storemacdeploy-testpublishing-to-the-app-storebundlingmd"></a>[Paquete para el Mac App Store](~/mac/deploy-test/publishing-to-the-app-store/bundling.md)

En esta guía se describe el proceso de creación de una aplicación Xamarin.Mac para su publicación en el Mac App Store.

### <a name="upload-to-mac-app-storemacdeploy-testpublishing-to-the-app-storeuploadingmd"></a>[Cargar en el Mac App Store](~/mac/deploy-test/publishing-to-the-app-store/uploading.md)

En esta guía se describe el proceso de carga de una aplicación Xamarin.Mac para su publicación en el Mac App Store.

## <a name="related-links"></a>Vínculos relacionados

- [Instalación](/visualstudio/mac/installation/)
- [Ejemplo de Hello, Mac](~/mac/get-started/hello-mac.md)
- [Identificador del desarrollador y equipo selector](https://developer.apple.com/resources/developer-id/)
