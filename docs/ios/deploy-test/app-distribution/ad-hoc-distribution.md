---
title: Distribución ad hoc para aplicaciones Xamarin.iOS
description: En este documento se ofrece una introducción a las técnicas de distribución ad hoc que se usan principalmente para probar una aplicación de Xamarin.iOS con un gran grupo de personas.
ms.prod: xamarin
ms.assetid: 3B621CAD-103C-478A-97C3-829015F48D1A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 74e258eeca78260fbfe3811e1dc6d8051e68e5f2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030253"
---
# <a name="ad-hoc-distribution-for-xamarinios-apps"></a>Distribución ad hoc para aplicaciones Xamarin.iOS

_En este documento se ofrece una introducción a las técnicas de distribución ad hoc que se usan principalmente para probar una aplicación de Xamarin.iOS con un gran grupo de personas._

Una vez que se ha desarrollado una aplicación de Xamarin.iOS, el siguiente paso del ciclo de vida de desarrollo de software es distribuirla a los usuarios para que la prueben.

iTunes Connect es una opción para administrar las pruebas de las aplicación y se describe con más detalle en la guía de [TestFlight](~/ios/deploy-test/testflight.md). Pero los miembros del Programa para desarrolladores empresariales de Apple no tienen acceso a iTunes Connect, por lo que la distribución *ad hoc* es el mejor método para probar estas aplicaciones.

Los usuarios pueden probar las aplicaciones de Xamarin.iOS a través de la distribución *ad hoc*, que está disponible tanto en el Programa para desarrolladores de Apple como en el Programa para desarrolladores empresariales de Apple y permite realizar pruebas en un máximo de 100 dispositivos iOS.

La distribución ad hoc tiene la ventaja de que no requiere aprobación de App Store y puede instalarse de forma remota desde un servidor web o a través de iTunes. Pero está limitada a **100** dispositivos por año de suscripción, tanto para el desarrollo como para la distribución, y deben agregarse manualmente por su UDID en el Centro de usuarios registrados. Para obtener más información sobre cómo agregar dispositivos, visite la guía [Device Provisioning](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#adddevice) (Aprovisionamiento de dispositivos).

La distribución ad hoc requiere que las aplicaciones se aprovisionen mediante un *perfil de aprovisionamiento* ad hoc que contenga información sobre la firma de código, así como la identidad de la aplicación y los dispositivos que pueden instalarla.

En esta guía se proporciona información sobre el aprovisionamiento para la distribución ad hoc y sobre la manera de distribuir una aplicación de Xamarin.iOS.

<a name="setup" />

## <a name="setting-up-for-distribution"></a>Configuración de distribución

Incluso si tiene previsto publicar una aplicación de Xamarin.iOS para la implementación interna con fines de prueba, debe crear un perfil de aprovisionamiento de distribución ad hoc específico para la aplicación. Este perfil permite que la aplicación se firme digitalmente para su publicación de modo que se pueda instalar en un dispositivo iOS.

En la sección siguiente se describe cómo realizar la configuración con un certificado de distribución y un perfil de aprovisionamiento de distribución.

> [!NOTE]
> Solo los agentes de equipo y los administradores pueden crear certificados de distribución y perfiles de aprovisionamiento.

<a name="createcertificate" />

## <a name="create-a-distribution-certificate"></a>Creación de un certificado de distribución

1. Vaya a la sección *Certificates, Identifiers & Profiles* (Certificados, identificadores y perfiles) del Centro de usuarios registrados de Apple Developer.
2. En *Certificates* (Certificados), seleccione **Production** (Producción).
3. Haga clic en el botón **+** para crear un nuevo certificado.
4. En el título *Production* (Producción), seleccione **In-House and Ad Hoc** (Interna y ad hoc) o **App Store and Ad Hoc** (App Store y ad hoc), en función de su pertenencia al programa:

   [![Selección de Interna y ad hoc o App Store y ad hoc](ad-hoc-distribution-images/cert-first-small.png)](ad-hoc-distribution-images/cert-first-large.png#lightbox)

5. Haga clic en Continuar y siga las instrucciones para crear una solicitud de firma de certificado a través del acceso a cadena de claves:

   [![Creación de una solicitud de firma de certificado a través de Acceso a llaves](ad-hoc-distribution-images/createcertmanually02.png)](ad-hoc-distribution-images/createcertmanually02.png#lightbox)

6. Una vez que haya creado la CSR tal como se indica, haga clic en Continuar y cárguela en el Centro de usuarios registrados:

   [![Carga de la CSR en el Centro de usuarios registrados](ad-hoc-distribution-images/createcertmanually03.png)](ad-hoc-distribution-images/createcertmanually03.png#lightbox)

7. Haga clic en Generar para crear el certificado.
8. Por último, descargue el certificado completado y haga doble clic en el archivo para instalarlo.
9. En este punto el certificado debería estar instalado en el equipo, aunque es posible que tenga que [actualizar sus perfiles](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download) para asegurarse de que sean visibles en Xcode.

Como alternativa, se puede solicitar un certificado mediante el cuadro de diálogo Preferencias de Xcode. Para ello, siga estos pasos:

1. Seleccione el equipo y haga clic en **Manage Certificates…** (Administrar certificados…):  [![Selección del equipo](ad-hoc-distribution-images/selectteam.png)](ad-hoc-distribution-images/selectteam.png#lightbox)

2. Después, haga clic en el botón **más (+)** y seleccione **iOS App Store** (App Store de iOS):  [![Selección de App Store de iOS](ad-hoc-distribution-images/selectcert.png)](ad-hoc-distribution-images/selectcert.png#lightbox)

<a name="createprofile" />

## <a name="create-a-distribution-provisioning-profile"></a>Creación de un perfil de aprovisionamiento de distribución

<a name="createappid" />

### <a name="create-an-app-id"></a>Creación de un identificador de aplicación
Al igual que con cualquier otro perfil de aprovisionamiento que cree, se necesita un identificador de aplicación para identificar la aplicación que va a distribuir al dispositivo del usuario. Si aún no lo ha creado, siga estos pasos para hacerlo:

1. En el [Centro de usuarios registrados de Apple Developer](https://developer.apple.com/account/overview.action), vaya a la sección *Certificate, Identifiers and Profiles* (Certificado, identificadores y perfiles). Seleccione **App IDs** (Identificadores de aplicación) en **Identifiers** (Identificadores).
2. Haga clic en el botón **+** y proporcione un **Nombre** que la identifique en el portal.
3. El prefijo de la aplicación ya debería estar establecido como el identificador del equipo y no se puede cambiar. Seleccione un identificador de aplicación explícito o comodín y especifique un identificador de paquete en un formato DNS inverso como:
    - **Explícito**: `com.[DomainName].[AppName]`
    - **Carácter comodín**: `com.[DomainName].*`
4. Seleccione los [App Services](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#appservices) que la aplicación necesite.
5. Haga clic en el botón **Continuar** y siga las instrucciones en pantalla para crear el nuevo identificador de aplicación.

Una vez que tenga los componentes necesarios para crear un perfil de distribución, siga estos pasos para crearlo:

1. Vuelva al portal de aprovisionamiento de Apple y seleccione **Aprovisionamiento > Distribución**:  [![Selección de Aprovisionamiento > Distribución](ad-hoc-distribution-images/distribute01.png)](ad-hoc-distribution-images/distribute01.png#lightbox)

2. Haga clic en el botón **+** y seleccione el tipo de perfil de distribución que quiere crear como **Ad hoc**:

    [![Creación de un tipo de distribución ad hoc](ad-hoc-distribution-images/distribute02.png)](ad-hoc-distribution-images/distribute02.png#lightbox)

3. Haga clic en el botón **Continuar** y seleccione el identificador de la aplicación en la lista desplegable para la que quiere crear un perfil de distribución:

    [![Selección de id. de aplicación en la lista desplegable](ad-hoc-distribution-images/distribute03.png)](ad-hoc-distribution-images/distribute03.png#lightbox)

4. Haga clic en el botón **Continuar** y seleccione el certificado de distribución necesario para firmar la aplicación:

    [![Seleccione el certificado de distribución necesario para firmar la aplicación](ad-hoc-distribution-images/distribute04.png)](ad-hoc-distribution-images/distribute04.png#lightbox)

5. Haga clic en el botón **Continuar** y escriba un **Nombre** para el nuevo perfil de distribución:

    [![Escritura de un nombre para el nuevo perfil de distribución](ad-hoc-distribution-images/distribute06.png)](ad-hoc-distribution-images/distribute06.png#lightbox)

6. Haga clic en el botón **Generar** para crear el nuevo perfil y finalizar el proceso.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Es posible que tenga que salir de Visual Studio para Mac y hacer que Xcode actualice su lista de identidades de firma y perfiles de aprovisionamiento disponibles (siga las instrucciones de la sección [Descargar perfiles y certificados en Xcode](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) para que un nuevo perfil de distribución esté disponible en Visual Studio para Mac.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Es posible que tenga que salir de Visual Studio y hacer que Xcode (en el Mac del host de compilación) actualice su lista de identidades de firma y perfiles de aprovisionamiento disponibles (siga las instrucciones de la sección [Descargar perfiles y certificados en Xcode](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#download)) para que un nuevo perfil de distribución esté disponible en Visual Studio.

-----

<a name="selectprofile" />

## <a name="selecting-a-distribution-profile-in-a-xamarinios-project"></a>Selección de un perfil de distribución en un proyecto de Xamarin.iOS

Cuando esté listo para realizar una compilación final de una aplicación de Xamarin.iOS, seleccione el perfil de distribución que creó anteriormente.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 En Visual Studio para Mac, haga lo siguiente:

1. Haga doble clic en el nombre del proyecto en el **Explorador de soluciones** para abrirlo para su edición.
2. Seleccione **Firma del lote de iOS** y el tipo de compilación en el elemento desplegable **Configuración**:

    ![Selección del tipo de compilación en la lista desplegable Configuración](ad-hoc-distribution-images/releasexs01.png)
3. En la mayoría de los casos, la **Identidad de firma** y el **Perfil de aprovisionamiento** pueden dejarse en el valor predeterminado **Automático** y Visual Studio para Mac elegirá el perfil correcto según el identificador de paquete de Info.plist:

    ![Identidad de firma y perfil de aprovisionamiento establecidos en los valores predeterminados de Automático](ad-hoc-distribution-images/releasexs02.png)
4. Si fuera necesario, seleccione la identidad de firma y el perfil de distribución (creado anteriormente) en las listas desplegables:

    ![Selección de la identidad de firma y el perfil de distribución](ad-hoc-distribution-images/releasexs03.png)
5. Haga clic en el botón **Aceptar** para guardar los cambios.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
 En Visual Studio, haga lo siguiente:

1. Haga clic con el botón derecho en el nombre del proyecto en el **Explorador de soluciones** y seleccione **Propiedades** para abrirlo para su edición.
2. Seleccione **Firma del lote de iOS** y el tipo de compilación en el elemento desplegable **Configuración**:

    ![Selección del tipo de compilación en la lista desplegable Configuración](ad-hoc-distribution-images/releasevs01.png)
3. En la mayoría de los casos, la **Identidad de firma** y el **Perfil de aprovisionamiento** pueden dejarse en el valor predeterminado **Automático** y Visual Studio elegirá el perfil correcto según el identificador de paquete de Info.plist:

    ![Identidad de firma y perfil de aprovisionamiento establecidos en los valores predeterminados de Automático](ad-hoc-distribution-images/releasevs02.png)
4. Si fuera necesario, seleccione la identidad de firma y el perfil de distribución (creado anteriormente) en las listas desplegables:

    ![Selección de la identidad de firma y el perfil de distribución](ad-hoc-distribution-images/releasevs03.png)
5. Guarde los cambios en las propiedades del proyecto.

-----

<a name="adhoc" />

## <a name="ad-hoc-distribution"></a>Distribución ad hoc

Aunque [TestFlight](~/ios/deploy-test/testflight.md) es un medio popular para realizar las pruebas beta y la distribución, forma parte de iTunes Connect y, por tanto, no está disponible para los miembros del **Programa para desarrolladores empresariales de Apple**.

La distribución ad hoc permite a los desarrolladores realizar pruebas beta de las aplicaciones en una amplia gama de dispositivos cuando iTunes Connect no es una opción válida. La distribución ad hoc funciona de forma parecida a la distribución interna y requiere la creación de un archivo IPA, que después se puede distribuir de forma remota, manual o a través de iTunes.

<a name="IPA_Creation" />

### <a name="ipa-support-for-ad-hoc-deployment"></a>Compatibilidad con IPA para la implementación ad hoc

Una vez aprovisionadas, las aplicaciones se pueden empaquetar en un archivo conocido como *IPA*. Se trata de un archivo zip que contiene la aplicación, junto con iconos y metadatos adicionales. El archivo IPA se usa para agregar una aplicación de forma local en iTunes para que se pueda sincronizar directamente en un dispositivo incluido en el perfil de aprovisionamiento.

Para obtener más información sobre cómo crear un archivo IPA, consulte la guía [IPA Support](~/ios/deploy-test/app-distribution/ipa-support.md) (Compatibilidad con IPA).

## <a name="summary"></a>Resumen

En este artículo se han explicado los mecanismos de la distribución ad hoc, que son necesarios para probar aplicaciones de Xamarin.iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Distribución a través del App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Distribución interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Archivo iTunesMetadata.plist](~/ios/deploy-test/app-distribution/itunesmetadata.md)
- [Compatibilidad con IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
