---
title: Aprovisionamiento de dispositivos para Xamarin.iOS
description: En este documento se describe cómo aprovisionar un dispositivo de modo que se pueda usar para probar una aplicación. También se explica cómo configurar una aplicación para que pueda usar funcionalidades como las notificaciones de inserción.
ms.prod: xamarin
ms.assetid: CACA5236-3C90-F6DF-FD4E-0797B61670CE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/06/2018
ms.openlocfilehash: dea08895b59626da97c1ca3d97464cfc80f42b88
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86928710"
---
# <a name="device-provisioning-for-xamarinios"></a>Aprovisionamiento de dispositivos para Xamarin.iOS

Al desarrollar una aplicación de Xamarin.iOS es esencial probarla mediante su implementación en un dispositivo físico, además de en el simulador. Cuando se ejecuta en un dispositivo pueden producirse errores propios del dispositivo y problemas de rendimiento debido a los límites de hardware, como la memoria o la conectividad de red. Para realizar pruebas en un dispositivo físico, este debe estar *aprovisionado* y es necesario informar a Apple de que se usará para las pruebas.

En las secciones resaltadas en la imagen siguiente se muestran los pasos necesarios para configurar el aprovisionamiento de iOS:

[![En las secciones resaltadas de esta imagen se muestran los pasos necesarios para configurar el aprovisionamiento de iOS.](images/provisioningdiagram.png)](images/provisioningdiagram.png#lightbox)

Después de hacerlo, el paso siguiente consiste en distribuir la aplicación. Para obtener más información al respecto, visite la guía [App Distribution](~/ios/deploy-test/app-distribution/index.md) (Distribución de aplicaciones).

Antes de implementar la aplicación en un dispositivo, debe tener una suscripción activa al Programa para desarrolladores de Apple, *o bien* usar el [aprovisionamiento libre](~/ios/get-started/installation/device-provisioning/free-provisioning.md). Apple ofrece dos opciones de programa:

- **Programa para desarrolladores de Apple**: independientemente de si es un usuario individual o si representa una organización, el Programa para desarrolladores de Apple le permite desarrollar, probar y distribuir aplicaciones.
- **Programa para desarrolladores empresariales de Apple**: este programa es más adecuado para las organizaciones que quieren desarrollar y distribuir aplicaciones únicamente internas. Los miembros de este programa no tienen acceso a iTunes Connect y las aplicaciones creadas no se pueden publicar en App Store.

Para registrarse en cualquiera de estos programas, visite el [portal para desarrolladores de Apple](https://developer.apple.com/programs/enroll/). Tenga en cuenta que, para registrarse como desarrollador de Apple, debe tener un [ID de Apple](https://appleid.apple.com/). En esta guía se da por supuesto que usted **es** miembro de un Programa para desarrolladores de Apple.

Como alternativa, Apple introdujo el [aprovisionamiento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md) en Xcode 7, que permite ejecutar una aplicación en un dispositivo *sin* ser miembro del Programa para desarrolladores de Apple. Hay una serie de limitaciones cuando el aprovisionamiento se realiza de esta manera, como se detalla [aquí](~/ios/get-started/installation/device-provisioning/free-provisioning.md#limitations).

Las aplicaciones que se ejecuten en un dispositivo deben incluir un conjunto de metadatos (o *huella digital*) con información sobre la aplicación y el desarrollador. Apple usa esta huella digital para asegurarse de que la aplicación no se altere cuando se implemente o se ejecute en un dispositivo de usuario. Para conseguirlo, se solicita a los desarrolladores de aplicaciones que registren su identificador de Apple como desarrollador y, además, que configuren un identificador de Apple, soliciten un certificado y registren el dispositivo en el que se implementará la aplicación.

Al implementar una aplicación en un dispositivo, también se instala un perfil de aprovisionamiento en el dispositivo iOS. El perfil de aprovisionamiento existe para comprobar la información con la que se firmó la aplicación en tiempo de compilación y verificar que está firmada criptográficamente por Apple. Juntas, las comprobaciones del perfil de aprovisionamiento y de la "huella digital" determinan si una aplicación se puede implementar en un dispositivo. Para ello, comprueban lo siguiente:

- **Quién** (certificados: ¿se ha firmado la aplicación con una clave privada, que tiene su clave pública correspondiente en el perfil de aprovisionamiento? El certificado también asocia el desarrollador con un equipo de desarrollo)
- **Qué** (identificador de aplicación individual: ¿el identificador de paquete establecido en Info.plist coincide con el identificador de la aplicación del perfil de aprovisionamiento?)
- **Dónde** (dispositivos: ¿está incluido el dispositivo en el perfil de aprovisionamiento?)

Estos pasos garantizan que se pueda realizar un seguimiento de todo lo que se cree o se use durante el proceso de desarrollo, incluidas las aplicaciones y dispositivos, hasta una cuenta de desarrollador de Apple.

## <a name="provisioning-your-device"></a>Aprovisionamiento del dispositivo

Hay dos maneras de aprovisionar el dispositivo iOS:

- **Automáticamente (recomendado)** : seleccione el esquema **Aprovisionamiento automático** en el proyecto para que Visual Studio cree y administre automáticamente las identidades de firma, los Id. de aplicación y los perfiles de aprovisionamiento. Para más información sobre cómo administrar automáticamente el aprovisionamiento, vea la guía [Aprovisionamiento automático](automatic-provisioning.md). Esta es la forma recomendada de aprovisionar un dispositivo iOS.

- **Manualmente**: las identidades de firma, los Id. de aplicación y los perfiles de aprovisionamiento pueden crearse y administrarse a través del portal Apple Developer, como se explica en la guía [Aprovisionamiento manual](manual-provisioning.md). Luego estos artefactos se pueden administrar como se explica en la guía de [administración de cuentas de Apple](~/cross-platform/macios/apple-account-management.md).

## <a name="provisioning-for-application-services"></a>Aprovisionamiento de servicios de aplicación

Apple proporciona una selección de servicios de aplicación especiales, también denominados funciones, que pueden activarse para una aplicación de Xamarin.iOS. Estos servicios de aplicación deben configurarse en el portal de aprovisionamiento de iOS al crear el **identificador de aplicación** y en el archivo **Entitlements.plist** que forma parte del proyecto de la aplicación de Xamarin.iOS. Para obtener más información sobre cómo agregar servicios de aplicación a la suya propia, consulte las guías [Introduction to Capabilities](~/ios/deploy-test/provisioning/capabilities/index.md) (Introducción a las capacidades) y [Working with Entitlements](~/ios/deploy-test/provisioning/entitlements.md) (Trabajar con derechos).

- Cree un id. de aplicación con los servicios de aplicación necesarios.
- Cree un nuevo [perfil de aprovisionamiento](#provisioning-your-device) que contenga dicho id. de aplicación.
- Establecer derechos en el proyecto de Xamarin.iOS

## <a name="related-links"></a>Vínculos relacionados

- [Aprovisionamiento gratis](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [App Distribution](~/ios/deploy-test/app-distribution/index.md) (Distribución de aplicaciones)
- [Solución de problemas](~/ios/deploy-test/troubleshooting.md)
- [Guía de distribución de aplicaciones de Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
