---
title: Aprovisionamiento automático para Xamarin.iOS
description: Una vez que se ha instalado correctamente Xamarin.iOS, el paso siguiente en el desarrollo de iOS consiste en aprovisionar el dispositivo iOS. En esta guía se analiza el uso de la firma automática para solicitar certificados de desarrollo y perfiles.
ms.prod: xamarin
ms.assetid: 81FCB2ED-687C-40BC-ABF1-FB4303034D01
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.custom: video
ms.date: 03/05/2020
ms.openlocfilehash: 069c40b74876bea1d3a0c8fca23b3d90c4b91635
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "79510684"
---
# <a name="automatic-provisioning-for-xamarinios"></a>Aprovisionamiento automático para Xamarin.iOS

_Una vez que se ha instalado correctamente Xamarin.iOS, el paso siguiente en el desarrollo de iOS consiste en aprovisionar el dispositivo iOS. En esta guía se analiza el uso del aprovisionamiento automático para solicitar certificados de desarrollo y perfiles._

## <a name="requirements"></a>Requisitos

El aprovisionamiento automático está disponible en Visual Studio para Mac, Visual Studio 2019 y Visual Studio 2017 (versión 15.7 y posteriores). 

> [!NOTE]
> También debe tener una cuenta de desarrollador de Apple de pago para usar esta característica. Puede encontrar más información sobre las cuentas de desarrollador de Apple en la guía de [aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md).
> Si no tiene una cuenta de desarrollador de Apple de pago, consulte la guía [Aprovisionamiento gratuito para aplicaciones Xamarin.iOS] (~/ios/get-started/installation/device-provisioning/free-provisioning.md).

> [!NOTE]
> Antes de empezar, asegúrese de aceptar primero todos los contratos de licencia en el [portal para desarrolladores de Apple](https://developer.apple.com/account/) o [App Store Connect](https://appstoreconnect.apple.com/).


## <a name="enable-automatic-provisioning"></a>Habilitar el aprovisionamiento automático

Antes de iniciar el proceso de firma automática, debe asegurarse de que se haya agregado un ID de Apple en Visual Studio, como se explica en la guía de [administración de cuentas de Apple](~/cross-platform/macios/apple-account-management.md). 

Una vez que haya agregado un Id. de Apple, puede usar cualquier _equipo_ asociado. Así podrá crear certificados, perfiles y otros id. a partir de los datos del equipo. El id. de equipo también se usa para crear un prefijo para un id. de aplicación que se incluirá en el perfil de aprovisionamiento. Todo esto permite a Apple comprobar su identidad.

Para inscribir la aplicación automáticamente para su implementación en un dispositivo iOS, haga lo siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Abra un proyecto de iOS en Visual Studio para Mac.

2. Abra el archivo **Info.plist**.

3. En la sección **Firma**, seleccione **Aprovisionamiento automático**:

    ![Lista desplegable del selector de equipo](automatic-provisioning-images/image2.png)

4. En la lista desplegable **Equipo**, seleccione su equipo.

5. Después de unos segundos se crean un certificado de firma y un perfil de aprovisionamiento:

    ![Certificado y perfil creados correctamente](automatic-provisioning-images/image5.png)

    Si se produce un error en la firma automática, el panel **Firma automática** muestra el motivo de ese error.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

> [!NOTE]
> Si usa Visual Studio 2017 o Visual Studio 2019 (versión 16.4 y anteriores), tendrá que estar [emparejado con un host de compilación de Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) antes de continuar.

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el nombre del proyecto de iOS y seleccione **Propiedades**. Luego, vaya a la pestaña **Firma del lote de iOS**:

    ![Captura de pantalla de la página de firma de lote en las propiedades de iOS.](automatic-provisioning-images/bundle-signing-win.png)

2. Seleccione el esquema **Aprovisionamiento automático**.

3. Seleccione el equipo en el menú desplegable **Equipo** para iniciar el proceso de firma automática. Visual Studio indicará si el proceso se completó correctamente:

    ![Captura de pantalla de la página de firma del lote que resalta el mensaje de que el aprovisionamiento automático se completó correctamente.](automatic-provisioning-images/signing-success-win.png)

-----

## <a name="run-automatic-provisioning"></a>Ejecución del aprovisionamiento automático

Cuando el aprovisionamiento automático está habilitado, Visual Studio volverá a ejecutar el proceso si es necesario cuando se produzca cualquiera de las siguientes acciones:

- Se conecta un dispositivo iOS al equipo Mac
  - Comprueba automáticamente si el dispositivo está registrado en el portal Apple Developer. Si no es así, lo agrega y genera un nuevo perfil de aprovisionamiento que lo contiene.
- Se ha modificado el Id. de agrupación de la aplicación
  - Actualiza el Id. de aplicación. Se crea un nuevo perfil de aprovisionamiento que contiene este Id. de aplicación.
- Se ha habilitado una capacidad compatible en el archivo Entitlements.plist.
  - Esta capacidad se agrega al Id. de aplicación y se genera un nuevo perfil de aprovisionamiento con el Id. de aplicación actualizado.
  - No todas las capacidades son compatibles de momento. Para más información sobre las que lo son, vea la guía [Working with Capabilities (Trabajar con capacidades)](~/ios/deploy-test/provisioning/capabilities/index.md).

## <a name="wildcard-app-ids"></a>Identificadores de aplicación comodín

En Visual Studio para Mac y Visual Studio 2019 (versión 16.5 o superior), el aprovisionamiento automático, de forma predeterminada, intenta crear y usar el identificador de la aplicación comodín y perfiles de aprovisionamiento, en lugar de un identificador de aplicación basado en el **identificador del lote** especificado en **Info.plist**. Un identificador de aplicación comodín reduce el número de perfiles e identificadores que deben mantenerse en el portal para desarrolladores de Apple.

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

Si la aplicación utiliza uno de estos derechos, Visual Studio intentará crear un identificador de aplicación explícito (en lugar de un comodín).

## <a name="troubleshoot"></a>Solucionar problemas 

- Pueden pasar varias horas hasta que se apruebe una nueva cuenta de desarrollador de Apple. No podrá habilitar el aprovisionamiento automático hasta que se haya aprobado la cuenta.
- Si el proceso de aprovisionamiento automático no puede completarse y aparece el mensaje de error `Authentication Service Is Unavailable`, inicie sesión en [App Store Connect](https://appstoreconnect.apple.com/) o [appleid.apple.com](https://appleid.apple.com) para comprobar que ha aceptado los contratos de servicio más recientes.
- Si aparece el mensaje de error `Authentication Error: Xcode 7.3 or later is required to continue developing with your Apple ID.`, asegúrese de que el equipo seleccionado tiene una suscripción de pago activa al programa para desarrolladores de Apple. Para usar una cuenta de pago de desarrolladores de Apple, consulte la guía de [aprovisionamiento gratuito para aplicaciones Xamarin.iOS](~/ios/get-started/installation/device-provisioning/free-provisioning.md).

## <a name="related-links"></a>Vínculos relacionados

- [Aprovisionamiento gratis](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [App Distribution](~/ios/deploy-test/app-distribution/index.md) (Distribución de aplicaciones)
- [Solución de problemas](~/ios/deploy-test/troubleshooting.md)
- [Guía de distribución de aplicaciones de Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
