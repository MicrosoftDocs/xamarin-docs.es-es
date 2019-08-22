---
title: Aprovisionamiento automático para Xamarin.iOS
description: Una vez que se ha instalado correctamente Xamarin.iOS, el paso siguiente en el desarrollo de iOS consiste en aprovisionar el dispositivo iOS. En esta guía se analiza el uso de la firma automática para solicitar certificados de desarrollo y perfiles.
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: asb3993
ms.author: amburns
ms.custom: video
ms.date: 01/22/2019
ms.openlocfilehash: 796c196b5c8ebcd70a0529112189cea6116a9ab0
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69890101"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Aprovisionamiento automático para Xamarin.iOS

_Una vez que se ha instalado correctamente Xamarin.iOS, el paso siguiente en el desarrollo de iOS consiste en aprovisionar el dispositivo iOS. En esta guía se analiza el uso de la firma automática para solicitar certificados de desarrollo y perfiles._

## <a name="requirements"></a>Requisitos

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

- Visual Studio para Mac 7.3 o posterior
- Xcode 9 o posterior

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

- Visual Studio 2019
- O bien, Visual Studio 2017 versión 15.7 (o superior)

También debe estar emparejado a un host de compilación de Mac que tenga lo siguiente:

- Xcode 10 o posterior

-----

## <a name="enabling-automatic-signing"></a>Habilitación de la firma automática

Antes de iniciar el proceso de firma automática, debe asegurarse de que se haya agregado un Id. de Apple en Visual Studio, como se explica en la guía [Apple Account Management](~/cross-platform/macios/apple-account-management.md) (Administración de cuentas de Apple). Una vez que haya agregado un Id. de Apple, puede usar cualquier _equipo_ asociado. Así podrá crear certificados, perfiles y otros id. a partir de los datos del equipo. El id. de equipo también se usa para crear el prefijo de un id. de aplicación que se incluirá en el archivo de aprovisionamiento. Todo esto permite a Apple comprobar su identidad.

> [!IMPORTANT]
> Antes de comenzar, asegúrese de iniciar sesión en [iTunes Connect](https://itunesconnect.apple.com/) o [appleid.apple.com](https://appleid.apple.com) para comprobar que han aceptado las últimas directivas de cuenta de Apple. Si se le solicita, complete los pasos para aceptar los nuevos contratos de cuenta de Apple. Si no acepta el contrato de privacidad de mayo de 2018, verá una de las alertas siguientes al intentar aprovisionar el dispositivo:
>
> ```
> Unexpected authentication failure. Reason: {
> "authType" : "sa"
> }
> ```
>
> o
>
> ```
> Authentication Service Is Unavailable
> ```

Para inscribir la aplicación automáticamente para su implementación en un dispositivo iOS, haga lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Abra un proyecto de iOS en Visual Studio para Mac.

2. Abra el archivo **Info.plist**.

3. En la sección **Firma**, seleccione **Aprovisionamiento automático**:

    ![Lista desplegable del selector de equipo](automatic-provisioning-images/image2.png)

4. En la lista desplegable **Equipo**, seleccione su equipo.

5. Después de unos segundos se crean un certificado de firma y un perfil de aprovisionamiento:

    ![Certificado y perfil creados correctamente](automatic-provisioning-images/image5.png)

    Si se produce un error en la firma automática, el panel **Firma automática** muestra el motivo de ese error.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Empareje Visual Studio 2019 con un equipo Mac como se describe en la guía [Emparejar con Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

2. En el **Explorador de soluciones**, haga clic con el botón derecho en el nombre del proyecto y seleccione **Propiedades**. Vaya a la pestaña **Firma del lote de iOS**.

3. Seleccione el esquema **Aprovisionamiento automático**:

    ![Selección del esquema Automático](automatic-provisioning-images/prov4.png)

4. Seleccione el equipo en el cuadro combinado **Equipo** para iniciar el proceso de firma automática.

    ![Selección del equipo](automatic-provisioning-images/prov3.png)

5. Esto inicia el proceso de firma automática. Luego Visual Studio intenta generar un Id. de aplicación, un perfil de aprovisionamiento y una identidad de firma para usar estos artefactos para la firma. Puede ver el proceso de generación en la salida de la compilación:

    ![Salida de la compilación que muestra la generación de artefactos](automatic-provisioning-images/prov5.png)

-----

## <a name="triggering-automatic-provisioning"></a>Inicio del aprovisionamiento automático

Cuando se ha habilitado la firma automática, Visual Studio para Mac actualiza estos artefactos en caso necesario si sucede algo de lo siguiente:

* Se conecta un dispositivo iOS al equipo Mac
    - Comprueba automáticamente si el dispositivo está registrado en el portal Apple Developer. Si no es así, lo agrega y genera un nuevo perfil de aprovisionamiento que lo contiene.
* Se ha modificado el Id. de agrupación de la aplicación
    - Actualiza el Id. de aplicación. Se crea un nuevo perfil de aprovisionamiento que contiene este Id. de aplicación.
* Se ha habilitado una capacidad compatible en el archivo Entitlements.plist.
    - Esta capacidad se agrega al Id. de aplicación y se genera un nuevo perfil de aprovisionamiento con el Id. de aplicación actualizado.
    - No todas las capacidades son compatibles de momento. Para más información sobre las que lo son, vea la guía [Working with Capabilities (Trabajar con capacidades)](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="wildcard-app-ids"></a>Identificadores de aplicación comodín

A partir de Visual Studio para Mac 7.6, el aprovisionamiento automático, de forma predeterminada, intenta crear y usar el identificador de la aplicación comodín y perfiles de aprovisionamiento, en lugar de un identificador de aplicación basado en el **identificador del lote** especificado en **Info.plist**. Un identificador de aplicación comodín reduce el número de perfiles e identificadores que deben mantenerse en el portal para desarrolladores de Apple.

En algunos casos, los derechos de una aplicación requieren un identificador de aplicación explícito. Los siguientes derechos no admiten identificadores de aplicación comodín:

- Grupos de aplicaciones
- Dominios asociados
- Apple Pay
- Game Center
- HealthKit
- HomeKit
- Zona activa
- Compra desde la aplicación
- Múltiples rutas
- NFC
- VPN personal
- Notificaciones push
- Configuración inalámbrica de accesorios

Si la aplicación utiliza uno de estos derechos, Visual Studio para Mac intentará crear un identificador de aplicación explícito (en lugar de un comodín).

> [!NOTE]
> Actualmente, el aprovisionamiento automático con los identificadores de aplicación comodín solo está disponible en Visual Studio para Mac.

## <a name="related-links"></a>Vínculos relacionados

- [Aprovisionamiento gratis](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [App Distribution](~/ios/deploy-test/app-distribution/index.md) (Distribución de aplicaciones)
- [Solución de problemas](~/ios/deploy-test/troubleshooting.md)
- [Guía de distribución de aplicaciones de Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)

## <a name="related-video"></a>Vídeo relacionado

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Snack-Pack-Simplified-iOS-Provisioning-in-Visual-Studio-with-fastlane/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
