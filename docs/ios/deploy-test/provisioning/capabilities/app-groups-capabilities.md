---
title: Capacidades de los grupos de aplicaciones en Xamarin.iOS
description: Agregar capacidades a una aplicación a menudo requiere una configuración de aprovisionamiento adicional. En esta guía se describe la configuración necesaria para las capacidades de los grupos de aplicaciones.
ms.prod: xamarin
ms.assetid: 0A61220B-BBAC-492B-9D3B-578986E64064
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/15/2017
ms.openlocfilehash: f98603c2ffac35362c6697e4806bf4b2daf44710
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "73030174"
---
# <a name="app-group-capabilities-in-xamarinios"></a>Capacidades de los grupos de aplicaciones en Xamarin.iOS

_Agregar funcionalidades a una aplicación requiere con frecuencia una configuración de aprovisionamiento adicional. En esta guía se describe la configuración necesaria para las funcionalidades de los grupos de aplicaciones._

Un grupo de aplicaciones permite que aplicaciones diferentes (o una aplicación y sus extensiones) tengan acceso a una ubicación de almacenamiento de archivos compartidos. Los grupos de aplicaciones se pueden usar para datos como:

* [Configuración de Apple Watch](~/ios/watchos/app-fundamentals/settings.md)
* [Valores NSUserDefaults compartidos](~/ios/app-fundamentals/user-defaults.md)
* [Archivos compartidos](~/ios/watchos/app-fundamentals/parent-app.md#files)

## <a name="configure-a-new-app-group"></a>Configurar un nuevo grupo de aplicaciones

La ubicación compartida se configura mediante un [grupo de aplicaciones](https://developer.apple.com/library/content/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW19), que se configura en la sección **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles) del [Centro para desarrolladores de Apple](https://developer.apple.com/account/). También se debe hacer referencia a este valor en el archivo Entitlements.plist de cada proyecto.

El grupo de aplicaciones tendrá un identificador, que suele ser el identificador de paquete con un prefijo group. de grupo. Por ejemplo, el identificador de paquete  `com.xamarin.WatchSettings`  tendría el grupo de aplicaciones  `group.com.xamarin.WatchSettings`.

Para crear un grupo de aplicaciones, haga lo siguiente:

1. Vaya al  [Centro para desarrolladores de iOS](https://developer.apple.com/account/) de Apple, abra su **cuenta** e inicie sesión.
2. Seleccione **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles).
3. En **Identifiers** (Identificadores), seleccione **App Groups** (Grupos de aplicaciones) y haga clic en el botón **+** para crear un grupo.
4. Escriba un **nombre** y un **identificador** para el nuevo grupo y haga clic en el botón **Continue** (Continuar): 
   
    ![Agregar detalles del grupo de aplicaciones](app-groups-capabilities-images/image52.png)

5. Haga clic en el botón **Registrar** para crear el grupo y en el botón **Listo** para volver a la lista de grupos de aplicaciones registrados.

## <a name="configure-an-app-to-use-app-groups"></a>Configurar una aplicación para que use grupos de aplicaciones

Una vez creado el grupo de aplicaciones, configure los identificadores de aplicación para que las aplicaciones puedan usarlo.

Haga lo siguiente:

1. Vaya al  [Centro para desarrolladores de iOS](https://developer.apple.com/account/) de Apple e inicie sesión con una cuenta de desarrollador de Apple.
2. En el menú **Program Resources** (Recursos del programa), seleccione **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles).
3. En **Identificadores**, seleccione **App IDs** (Identificadores de aplicaciones) y haga clic en el botón **+** para crear un identificador.
4. Escriba un nombre para el identificador de aplicación y asígnele un identificador de aplicación explícito.
5. En **App Services**, habilite **Grupos de aplicaciones** y haga clic en el botón Continuar:

    ![Agregar servicios de aplicaciones en el grupo de aplicaciones](app-groups-capabilities-images/image53.png)

6. Compruebe la configuración y haga clic en el botón **Register** (Registrar) para crear el identificador de aplicación.
7. Haga clic en el botón **Listo** para volver a la lista de identificadores de aplicación registrados.
8. Seleccione el identificador de aplicación recién creado en la lista y haga clic en el botón **Editar**:

    ![Selección del identificador de aplicación en la lista](app-groups-capabilities-images/image54.png)

9. Debajo de Servicio, en **App Group** (Grupo de aplicaciones), haga clic en el botón **Editar**:

    ![Selección del identificador de aplicación en la lista](app-groups-capabilities-images/image55.png)

10. Seleccione el grupo de aplicaciones que se creó anteriormente y haga clic en el botón **Continuar**:

    ![Agregar el grupo de aplicaciones](app-groups-capabilities-images/image56.png)

11. Haga clic en el botón **Asignar** y, después, en el botón **Listo** para volver a la lista de identificadores de aplicación registrados.
12. Repita estos pasos para todas las aplicaciones (o extensiones) que vayan a usar el grupo de aplicaciones.

## <a name="next-steps"></a>Pasos siguientes

En la siguiente lista se describen los pasos adicionales que se deben seguir:

* Use el espacio de nombres del marco en su aplicación.
* Agregue los derechos necesarios a la aplicación. En la guía [Trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) se proporciona información sobre los derechos necesarios y sobre cómo agregarlos.
* En la opción **Firma de lote de iOS** de la aplicación, asegúrese de que **Derechos personalizados** esté establecido en **Entitlements.plist**. Esta  _no_ es la configuración predeterminada para las compilaciones de depuración y del simulador de iOS.

Si experimenta problemas con servicios de aplicaciones, vea la sección [Solución de problemas](~/ios/deploy-test/provisioning/capabilities/index.md) de la guía principal.
