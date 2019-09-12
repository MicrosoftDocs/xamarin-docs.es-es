---
title: Trabajar con capacidades en Xamarin.iOS
description: Agregar capacidades a una aplicación a menudo requiere una configuración de aprovisionamiento adicional. En esta guía se describe la configuración necesaria para todas las capacidades.
ms.prod: xamarin
ms.assetid: 98A4676F-992B-4593-8D38-6EEB2EB0801C
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/06/2018
ms.openlocfilehash: d7a2f14b55d9f8de2ea01bdfa5c68c5c9f8f2555
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70756148"
---
# <a name="working-with-capabilities-in-xamarinios"></a>Trabajar con capacidades en Xamarin.iOS

_Agregar funcionalidades a una aplicación requiere con frecuencia una configuración de aprovisionamiento adicional. En esta guía se describe la configuración necesaria para todas las funcionalidades._

Apple proporciona a los desarrolladores _capacidades_ (a veces denominadas _servicios de aplicaciones_) como método para extender la funcionalidad y ampliar el alcance de lo que pueden hacer las aplicaciones de iOS. Gracias a las capacidades, los desarrolladores pueden agregar a su aplicación una integración más profunda de las características de las plataformas, como por ejemplo la capacidad de iniciar transacciones monetarias desde la aplicación, servicios de dispositivo adicionales como Siri, etc.
Estas capacidades se pueden usar con los proyectos de Xamarin.iOS. A continuación se muestra la lista completa de los servicios:

- Grupos de aplicaciones
- Dominios asociados
- Protección de datos
- Game Center
- HealthKit
- HomeKit
- Configuración inalámbrica de accesorios
- iCloud
- Compra desde la aplicación
- Inter-App Audio
- Apple Pay
- Cartera
- Notificación de inserción
- VPN personal
- Siri
- Asignaciones
- Modos en segundo plano
- Uso compartido de cadenas de claves
- Extensiones de red
- Configuración de zona activa
- Múltiples rutas
- Lectura de etiquetas NFC

Las capacidades se pueden habilitar a través de Visual Studio para Mac y Visual Studio 2019, o manualmente en el portal para desarrolladores de Apple. Hay ciertas capacidades, como Wallet, Apple Pay y iCloud, que requieren una configuración adicional de los identificadores de aplicación.

En esta guía se explica cómo habilitar App Services en la aplicación automáticamente en Visual Studio y manualmente a través de Developer Center, incluida cualquier configuración adicional que pueda ser necesaria. 

## <a name="adding-app-services"></a>Agregar servicios de aplicaciones

Para usar las capacidades, la aplicación debe tener un perfil de aprovisionamiento válido que contenga un identificador de aplicación con el servicio correcto habilitado. Este perfil de aprovisionamiento se puede crear automáticamente en Visual Studio para Mac y Visual Studio 2019, o manualmente en Apple Developer Center.

En esta sección se explica cómo usar el aprovisionamiento automático de Visual Studio o Developer Center para habilitar la mayoría de las capacidades. Hay algunas capacidades, como Wallet, iCloud, Apple Pay y los grupos de aplicaciones, que requieren una configuración adicional. Se describen en detalle en las guías adyacentes.

> [!IMPORTANT]
> No todas las capacidades se pueden agregar y administrar con aprovisionamiento automático. La lista siguiente contiene las capacidades admitidas:
>
>- HealthKit 
>- HomeKit 
>- VPN personal 
>- Configuración inalámbrica de accesorios 
>- Inter-App Audio 
>- SiriKit 
>- Zona activa 
>- Extensiones de red 
>- Lectura de etiquetas NFC
>- Múltiples rutas 
>
>Las notificaciones push, Game Center, la compra desde la aplicación, los mapas, el uso compartido de cadenas de claves, los dominios asociados y las capacidades de protección de datos no se admiten de momento. Para agregar estas capacidades, use el aprovisionamiento manual y siga los pasos de la sección [Centro para desarrolladores](#devcenter).

## <a name="using-the-ide"></a>Uso de el IDE

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Las capacidades se agregan a **Entitlements.plist** en Visual Studio para Mac. Para agregar capacidades, siga estos pasos:

1. Abra el archivo **Info.plist** de la aplicación iOS y seleccione el esquema **Aprovisionamiento automático** y el **Equipo** en el cuadro combinado. Siga los pasos de la guía [Aprovisionamiento automático](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) si necesita ayuda:

    ![Opción Administrar automáticamente la firma](images/manage-signing.png)

2. Abra el archivo **Entitlements.plist** y seleccione la capacidad que quiere agregar:

    ![Agregar capacidades al archivo entitlements.plist](images/image17.png)

    Al seleccionar una capacidad suceden dos cosas:
    - Esa característica se agrega al Id. de aplicación.
    - Se agrega el par de clave/valor de derechos al archivo Entitlements.plist.

    Visual Studio para Mac le avisa una vez que se han realizado estas tareas mediante el siguiente mensaje de correcto:

    ![Agregar capacidades al archivo entitlements.plist](images/image18.png)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Las capacidades se agregan a **Entitlements.plist**. Para agregar capacidades en Visual Studio 2019, haga lo siguiente:

1. Empareje Visual Studio 2019 con un equipo Mac como se describe en la guía [Emparejar con Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

2. Para abrir las opciones de aprovisionamiento, seleccione **Proyecto > Propiedades de aprovisionamiento...**

3. Seleccione el esquema **Aprovisionamiento automático** y el **Equipo** en el cuadro combinado. Siga los pasos de la guía [Aprovisionamiento automático](~/ios/get-started/installation/device-provisioning/automatic-provisioning.md) si necesita ayuda:

    ![Opción Administrar automáticamente la firma](images/manage-signing-vs.png)

4. Abra el archivo **Entitlements.plist** y seleccione la capacidad que quiere agregar. Guarde el archivo.

    Al guardar **Entitlement.plist** ocurren dos cosas:

    - Esa característica se agrega al Id. de aplicación.
    - Se agrega el par de clave/valor de derechos al archivo Entitlements.plist.

-----

<a name="devcenter" />

## <a name="using-the-developer-center"></a>Uso de Developer Center

El uso del Centro para desarrolladores es un proceso de dos pasos en el que se debe crear un identificador de aplicación y se debe usar dicho identificador para crear un perfil de aprovisionamiento. Estos pasos se detallan a continuación.

### <a name="creating-an-app-id-with-an-app-service"></a>Crear un identificador de aplicación con un servicio de aplicaciones

1. Vaya al [Centro para desarrolladores de Apple](https://developer.apple.com/account) en un equipo Mac (el equipo Mac que hospeda la compilación en el caso de que use un equipo con Windows) e inicie sesión.
2. Seleccione **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles):

    ![Centro para desarrolladores de Apple](images/image5.png)

3. En **Identifiers** (Identificadores), seleccione **App IDs** (Identificadores de aplicación):

    ![Selección del identificador de aplicación en el Centro para desarrolladores](images/image6.png)

4. Presione el botón **+** situado en la esquina superior derecha para crear un identificador de aplicación.
5. Escriba una descripción para el identificador de aplicación, seleccione la opción Explicit App ID (Identificador de aplicación explícito) y especifique un identificador de paquete con el formato `com.domain.appname`. Este identificador de paquete debe coincidir con el identificador de paquete del proyecto:

    ![Agregar detalles del identificador de aplicación](images/image7.png)

6. En **App Services** (Servicios de aplicaciones), seleccione los servicios necesarios en la aplicación:

    ![Página de selección de servicios de aplicaciones](images/image8.png)

7. Presione **Continue** (Continuar).
8. Confirme el identificador de aplicación. Cada servicio se encontrará en uno de los estados siguientes: **Habilitado**, **Deshabilitado** o **Configurable**, como se muestra a continuación. Si tiene el estado **Enabled** (Habilitado), quiere decir que está listo para usarlo en un perfil de aprovisionamiento. Si tiene el estado **Configurable**, quiere decir que se debe efectuar una configuración adicional para esta capacidad. Estos pasos adicionales se describen con más detalle en secciones posteriores.

    ![Confirmación del identificador de aplicación](images/image9.png)

9. Haga clic en **Register** (Registrar) y en **Done** (Listo). El identificador de aplicación que acaba de crear debe aparecer en la lista de identificadores de aplicación de iOS.

<a name="provisioningprofile" />

### <a name="creating-a-provisioning-profile"></a>Crear un perfil de aprovisionamiento

Ahora cree un perfil de aprovisionamiento que contenga este identificador de aplicación. Siga los pasos que se describen a continuación:

1. En el Centro para desarrolladores de Apple, vaya a **Provisioning Profiles > All** (Perfiles de aprovisionamiento > Todos):

    ![Sección de los perfiles de aprovisionamiento](images/image10.png)

2. Presione el botón **+** situado en la esquina superior derecha para crear otro perfil de aprovisionamiento.
3. Seleccione el tipo de perfil de aprovisionamiento que necesita y haga clic en **Continue** (Continuar):

    ![Selección de un perfil de aprovisionamiento](images/image11.png)

4. En la lista desplegable, seleccione el identificador de aplicación que creó en los pasos anteriores y presione **Continue** (Continuar):

    ![Selección del identificador de aplicación](images/image12.png)

5. Seleccione los certificados usados para firmar la aplicación y presione **Continue** (Continuar):

    ![Selección de los certificados](images/image13.png)

6. Seleccione los dispositivos que quiere incluir en este perfil y presione **Continue** (Continuar):

    ![Selección de los dispositivos para el perfil de aprovisionamiento](images/image14.png)

7. Asigne un nombre al perfil para que se pueda identificar y presione **Continue** (Continuar) para generar el perfil:

    ![Asignación de un nombre al perfil de aprovisionamiento](images/image15.png)

8. Presione el botón **Download** (Descargar) para descargarlo y haga doble clic en el archivo que encontrará en Finder para instalar el perfil de aprovisionamiento.

9. Si usa Visual Studio, asegúrese de que la opción **Aprovisionamiento manual** esté seleccionada.

10. En Visual Studio para Mac o Visual Studio, vaya a **Opciones de proyecto > Firma de lote** y establezca el perfil de aprovisionamiento en el que se acaba de crear:

    ![Opciones de proyecto de Visual Studio para Mac](images/image16.png)

> [!IMPORTANT]
> Es posible que también deba establecer claves de derechos en el archivo Entitlement.plist y claves de privacidad en el archivo Info.plist. En la guía [Trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) se proporciona más información sobre estos derechos.

<a name="nextsteps" />

## <a name="next-steps"></a>Pasos siguientes

Una vez que se ha habilitado una funcionalidad en el servidor, se deben seguir unos pasos más para que la aplicación pueda usar la funcionalidad. En la siguiente lista se describen los pasos adicionales que se deben seguir:

- Use el espacio de nombres del marco en su aplicación.
- Agregue los derechos necesarios a la aplicación. En la guía [Trabajar con derechos](~/ios/deploy-test/provisioning/entitlements.md) se proporciona información sobre los derechos necesarios y sobre cómo agregarlos.

<a name="troubleshooting" />

## <a name="troubleshooting-capabilities"></a>Solución de problemas relacionados con las capacidades

En la siguiente lista se detallan algunos de los problemas más comunes que pueden crear obstáculos a la hora de desarrollar una aplicación con un servicio de aplicación habilitado.

- Asegúrese de que el identificador se creó y registró correctamente en la sección **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles) del Portal para desarrolladores de Apple.
- Asegúrese de que el servicio se haya agregado al identificador de la aplicación (o de la extensión) y de que el servicio esté configurado para usar el grupo de aplicaciones/identificador de comerciante/contenedor creado anteriormente en la sección **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles) del Portal para desarrolladores de Apple.
- Asegúrese de que se hayan instalado los perfiles de aprovisionamiento y los identificadores de aplicación y que el archivo **Info.plist** de la aplicación (en el proyecto de Xamarin) use uno de los identificadores de aplicación configurados anteriormente.
- Asegúrese de que el archivo **Entitlements.plist** de la aplicación (del proyecto de Xamarin) tiene habilitado el servicio correcto.
- Asegúrese de que en el archivo info.plist estén establecidas las claves de privacidad correspondientes.
- En la opción **Firma de lote de iOS** de la aplicación, asegúrese de que **Derechos personalizados** esté establecido en **Entitlements.plist**. Esta _no_ es la configuración predeterminada para las compilaciones de depuración y del simulador de iOS.

<a name="summary" />

## <a name="summary"></a>Resumen

En esta guía se han explicado las capacidades o los _servicios de aplicaciones_ y se ha descrito cómo habilitarlos en Visual Studio y en el Centro para desarrolladores de Apple. También se describe cómo configurar servicios más complicados, como Wallet, iCloud, Apple Pay y los grupos de aplicaciones. Por último, se detallan los siguientes pasos de configuración y opciones sencillas para la solución de problemas.
