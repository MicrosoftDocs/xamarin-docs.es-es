---
title: Distribución interna para aplicaciones Xamarin.iOS
description: En este documento, se proporciona una breve introducción a la distribución interna de aplicaciones, como miembro del programa para desarrolladores empresariales de Apple.
ms.prod: xamarin
ms.assetid: 9466E51E-303E-466E-85D7-D0525E16BB37
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: f81e5a24957d4af1cd2ce27b39d1f398797cce73
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289884"
---
# <a name="in-house-distribution-for-xamarinios-apps"></a>Distribución interna para aplicaciones Xamarin.iOS

_En este documento, se proporciona una breve introducción a la distribución interna de aplicaciones, como miembro del programa para desarrolladores empresariales de Apple._

Una vez que se ha desarrollado la aplicación de Xamarin.iOS, el siguiente paso del ciclo de vida de desarrollo de software es distribuirla a los usuarios. Las aplicaciones propietarias se pueden distribuir de forma *interna* (anteriormente se denominaba como distribución empresarial) a través del **programa para desarrolladores empresariales de Apple**, que ofrece las siguientes ventajas:

- No hay que enviar la aplicación para que la revise Apple.
- No hay límites en la cantidad de dispositivos en que puede implementar una aplicación.
  - Es importante tener en cuenta que Apple deja muy claro que las aplicaciones internas son solo para uso interno.

También es importante tener en cuenta que el programa empresarial:

- No proporciona acceso a iTunes Connect para distribuir o probar (incluido TestFlight).
- El costo de pertenencia es de 299 $ al año.

Todas las aplicaciones deben estar firmadas por Apple.

<a name="testing" />

## <a name="testing-your-application"></a>Probar la aplicación

La prueba de la aplicación se lleva a cabo mediante la distribución ad hoc. Para obtener más información sobre la prueba, siga los pasos de la guía [Distribución ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md). Tenga en cuenta que solo puede probar en un máximo de 100 dispositivos.

<a name="setup" />

## <a name="getting-set-up-for-distribution"></a>Configuración de distribución

Al igual que con otros programas para desarrolladores de Apple, en el programa para desarrolladores empresariales de Apple, solo los administradores y agentes de equipo pueden crear certificados de distribución y perfiles de aprovisionamiento.

Los certificados del programa para desarrolladores empresariales de Apple durarán tres años y los perfiles de aprovisionamiento expirarán tras un año.

Es importante tener en cuenta que no se pueden renovar los certificados expirados y, en su lugar, tendrá que reemplazar el certificado expirado por uno nuevo, tal como se detalla [a continuación](#certificate).

<a name="certificate" />

## <a name="creating-a-distribution-certificate"></a>Creación de un certificado de distribución

1. Vaya a la sección *Certificates, Identifiers & Profiles* (Certificados, identificadores y perfiles) del Centro de usuarios registrados de Apple Developer.
2. En *Certificates* (Certificados), seleccione **Production** (Producción).
3. Haga clic en el botón **+** para crear un nuevo certificado.
4. En el encabezado *Producción*, seleccione **In-House and Ad Hoc** (Interna y ad hoc):

   [![](in-house-distribution-images/createcertmanually01.png "Selección Interna y ad hoc")](in-house-distribution-images/createcertmanually01.png#lightbox)

5. Haga clic en Continuar y siga las instrucciones para crear una solicitud de firma de certificado a través del acceso a cadena de claves:

   [![](in-house-distribution-images/createcertmanually02.png "Creación de una solicitud de firma de certificado a través de Acceso a llaves")](in-house-distribution-images/createcertmanually02.png#lightbox)

6. Una vez que haya creado la CSR tal como se indica, haga clic en Continuar y cárguela en el Centro de usuarios registrados:

   [![](in-house-distribution-images/createcertmanually03.png "Carga de la CSR en el Centro de usuarios registrados")](in-house-distribution-images/createcertmanually03.png#lightbox)

7. Haga clic en Generar para crear el certificado.
8. Descargue el certificado completado y haga doble clic en el archivo para instalarlo.
9. En este momento, el certificado debería estar instalado en el equipo, aunque es posible que tenga que actualizar sus perfiles para asegurarse de que estén visibles en Xcode.

Como alternativa, se puede solicitar un certificado mediante el cuadro de diálogo Preferencias de Xcode. Para ello, siga estos pasos:

1. Seleccione el equipo y haga clic en *Ver detalles*:

   [![](in-house-distribution-images/selectteam.png "Selección del equipo")](in-house-distribution-images/selectteam.png#lightbox)

2. Luego, haga clic en el botón **Crear** situado junto a **iOS Distribution Certificate** (Certificado de distribución de iOS):

   [![](in-house-distribution-images/selectcert.png "Creación del certificado de distribución de iOS")](in-house-distribution-images/selectcert.png#lightbox)

3. Después, haga clic en el botón **más (+)** y seleccione **iOS App Store** (App Store de iOS):

   [![](in-house-distribution-images/selectcert.png "Selección de App Store de iOS")](in-house-distribution-images/selectcert.png#lightbox)

<a name="profile" />

## <a name="creating-a-distribution-provisioning-profile"></a>Creación de un perfil de aprovisionamiento de distribución

<a name="appid" />

### <a name="creating-an-app-id"></a>Creación de un identificador de aplicación

Al igual que con cualquier otro perfil de aprovisionamiento que cree, se necesita un identificador de aplicación para identificar la aplicación que va a distribuir al dispositivo del usuario. Si aún no lo ha creado, siga estos pasos para hacerlo:


1. En el [Centro de usuarios registrados de Apple Developer](https://developer.apple.com/account/overview.action), vaya a la sección *Certificate, Identifiers and Profiles* (Certificado, identificadores y perfiles). Seleccione **App IDs** (Identificadores de aplicación) en **Identifiers** (Identificadores).
2. Haga clic en el botón **+** y proporcione un **Nombre** que la identifique en el portal.
3. El prefijo de la aplicación ya debería estar establecido como el identificador del equipo y no se puede cambiar. Seleccione un identificador de aplicación explícito o comodín y especifique un identificador de paquete en un formato DNS inverso como: **Explícito**: com.[nombreDeDominio].[ NombreDeAplicación] **CarácterComodín**:com.[nombreDeDominio].*
4. Seleccione los [App Services](~/ios/get-started/installation/device-provisioning/index.md#provisioning-for-application-services) que la aplicación necesite.
5. Haga clic en el botón **Continuar** y siga las instrucciones en pantalla para crear el identificador de aplicación.

Una vez que tenga los componentes necesarios para crear un perfil de distribución, siga estos pasos para crearlo:

1. Vuelva al portal de aprovisionamiento de Apple y seleccione **Aprovisionamiento** > **Distribución**:

   [![](in-house-distribution-images/distribute01.png "Selección de Aprovisionamiento > Distribución")](in-house-distribution-images/distribute01.png#lightbox)

2. Haga clic en el botón **+** y seleccione el tipo de perfil de distribución que quiere crear como **Interno**:

   [![](in-house-distribution-images/distribute02.png "Cree un perfil de distribución de interna")](in-house-distribution-images/distribute02.png#lightbox)

3. Haga clic en el botón **Continuar** y seleccione el identificador de la aplicación en la lista desplegable para la que quiere crear un perfil de distribución:

   [![](in-house-distribution-images/distribute03.png "Selección de Id. de aplicación en la lista desplegable")](in-house-distribution-images/distribute03.png#lightbox)

4. Haga clic en el botón **Continuar** y seleccione el certificado de distribución necesario para firmar la aplicación:

   [![](in-house-distribution-images/distribute04.png "Seleccione el certificado de distribución necesario para firmar la aplicación")](in-house-distribution-images/distribute04.png#lightbox)

5. Haga clic en el botón **Continuar** y escriba un **Nombre** para el nuevo perfil de distribución:

   [![](in-house-distribution-images/distribute06.png "Escribir un nombre para el nuevo perfil de distribución")](in-house-distribution-images/distribute06.png#lightbox)

6. Haga clic en el botón **Generar** para crear el nuevo perfil y finalizar el proceso.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 Es posible que tenga que salir de Visual Studio para Mac y hacer que Xcode actualice su lista de identidades de firma y perfiles de aprovisionamiento disponibles (con las instrucciones de la sección [Solicitud de identidades de firma](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) para que un nuevo perfil de distribución esté disponible en Visual Studio para Mac.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Es posible que tenga que salir de Visual Studio y hacer que Xcode (en el equipo Mac del host de compilación) actualice su lista de identidades de firma y perfiles de aprovisionamiento disponibles (con las instrucciones de la sección sobre cómo [solicitar identidades de firma](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) para que un nuevo perfil de distribución esté disponible en Visual Studio.

-----

<a name="inhouse" />

## <a name="distributing-your-app-in-house"></a>Distribución interna de la aplicación

Con el programa para desarrolladores empresariales de Apple, el licenciatario es la persona responsable de distribuir la aplicación y atenerse a las [directrices](http://adcdownload.apple.com/Documentation/License_Agreements__Apple_Developer_Enterprise_Program/Apple_Developer_Program_Enterprise_Agreement_20150608.pdf) establecidas por Apple.

La aplicación se puede distribuir de forma segura de diferentes formas, como:

- De forma local a través de iTunes
- Servidor MDM
- Un servidor web interno y seguro
- Correo electrónico

Para distribuir la aplicación de cualquiera de estas formas, primero debe crear un archivo IPA, como se explica en la sección siguiente.


### <a name="creating-an-ipa-for-in-house-deployment"></a>Creación de un IPA para la implementación interna

Una vez aprovisionadas, las aplicaciones se pueden empaquetar en un archivo conocido como *IPA*. Se trata de un archivo zip que contiene la aplicación, junto con iconos y metadatos adicionales. El archivo IPA se usa para agregar una aplicación de forma local en iTunes para que se pueda sincronizar directamente en un dispositivo incluido en el perfil de aprovisionamiento.

Para obtener más información sobre cómo crear un IPA, consulte la guía [Compatibilidad con IPA](~/ios/deploy-test/app-distribution/ipa-support.md).


## <a name="summary"></a>Resumen

En este artículo, se proporciona una breve introducción a la distribución de aplicaciones de Xamarin.iOS de forma interna.

## <a name="related-links"></a>Vínculos relacionados

- [Distribución a través del App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribución ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Archivo iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Compatibilidad con IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
