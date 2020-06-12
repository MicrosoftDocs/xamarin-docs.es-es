---
title: Aprovisionamiento manual de Xamarin.iOS
description: Una vez que se ha instalado correctamente Xamarin.iOS, el paso siguiente en el desarrollo de iOS consiste en aprovisionar el dispositivo iOS. En esta guía se analiza el uso del aprovisionamiento manual para establecer certificados de desarrollo y perfiles.
ms.prod: xamarin
ms.assetid: E26ACC94-F4A5-4FF5-B7D4-BE596745A665
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/06/2020
ms.openlocfilehash: 9333750432395d008a5454e293648f4e594ae112
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571693"
---
# <a name="manual-provisioning-for-xamarinios"></a>Aprovisionamiento manual de Xamarin.iOS

_Una vez que se ha instalado correctamente Xamarin.iOS, el paso siguiente en el desarrollo de iOS consiste en aprovisionar el dispositivo iOS. En esta guía se analiza el uso del aprovisionamiento manual para establecer certificados de desarrollo y perfiles._

> [!NOTE]
> Las instrucciones que se muestran en esta página son relevantes para los desarrolladores que han pagado por el acceso al programa de Apple Developer. Si tiene una cuenta gratuita, consulte la guía de [aprovisionamiento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md) para obtener más información acerca de las pruebas en dispositivos.

## <a name="create-a-development-certificate"></a>Crear un certificado de desarrollo

El primer paso para configurar un dispositivo de desarrollo consiste en crear un certificado de firma. Un certificado de firma consta de dos partes:

- Un certificado de desarrollo
- Una clave privada

Los certificados de desarrollo y las [claves](#understanding-certificate-key-pairs) asociadas son fundamentales para un desarrollador de iOS, ya que establecen su identidad con Apple y lo asocian a un dispositivo determinado y a un perfil de desarrollo, como si incluyeran su firma digital en las aplicaciones. Apple busca certificados para controlar el acceso a los dispositivos en los que se puede implementar.

Los equipos de desarrollo, los certificados y los perfiles pueden administrarse mediante el acceso a la sección [Certificados, identificadores y perfiles](https://developer.apple.com/account/resources/certificates/list) del Centro de usuarios registrados de Apple (se requiere iniciar sesión). Apple requiere que tenga una identidad de firma para compilar el código para el dispositivo o el simulador.  

> [!IMPORTANT]
> Es importante que tenga en cuenta que solo puede tener dos certificados de desarrollo de iOS en todo momento. Si necesita crear otro, deberá revocar uno existente. Si una máquina usa un certificado revocado, no podrá firmar la aplicación.

Antes de iniciar el proceso de aprovisionamiento manual, debe asegurarse de que ha agregado una cuenta de desarrollador de Apple en Visual Studio, tal y como se explica en el artículo [Administración de cuentas de Apple](~/cross-platform/macios/apple-account-management.md). Después de agregar su cuenta de desarrollador de Apple, haga lo siguiente para generar un certificado de firma:

1. Vaya a la ventana de cuentas de desarrollador de Apple en Visual Studio.
    1. Mac: **Visual Studio > Preferencias > Cuenta de desarrollador de Apple**
    2. Windows: **Herramientas > Opciones > Xamarin > Apple Accounts (Cuentas de Apple)**

2. Seleccione un equipo y haga clic en **Ver detalles…**
3. Haga clic en **Crear certificado** y seleccione **Apple Development** (Desarrollo de Apple) o **iOS Development** (Desarrollo de iOS). Si tiene los permisos correctos, aparecerá una nueva identidad de firma pasados unos segundos.

### <a name="understanding-certificate-key-pairs"></a>Introducción a los pares de claves de certificado

El perfil de desarrollador contiene certificados, sus claves asociadas y los perfiles de aprovisionamiento asociados con la cuenta. Cada perfil de desarrollador tiene dos versiones: una se encuentra en el portal para desarrolladores y la otra reside en un equipo Mac local. La diferencia entre ambas es el tipo de claves que contienen: _el perfil del portal contiene todas las claves públicas asociadas con los certificados, mientras que la copia del equipo Mac local contiene todas las claves privadas_. Para que los certificados sean válidos, los pares de claves deben coincidir.

> [!WARNING]
> La pérdida del certificado y de las claves asociadas puede causar muchos problemas, ya que requerirá que se revoquen los certificados existentes y se vuelvan a aprovisionar los dispositivos asociados, incluidos los registrados para la implementación ad hoc. Después de configurar correctamente los certificados de desarrollo, exporte una copia de seguridad y almacénelos en un lugar seguro. Para obtener más información sobre cómo hacerlo, consulte la sección sobre la exportación y la importación de certificados y perfiles de la guía [Maintaining Certificates](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html) (Mantener certificados) en la documentación de Apple.

<a name="provisioning"></a>

## <a name="provision-an-ios-device-for-development"></a>Aprovisionamiento de un dispositivo iOS para el desarrollo

Ahora que ha establecido su identidad con Apple y tiene un certificado de desarrollo, debe configurar un perfil de aprovisionamiento y las entidades necesarias para poder implementar una aplicación en un dispositivo Apple. El dispositivo debe ejecutar una versión de iOS compatible con Xcode. Por ello, puede ser necesario actualizar el dispositivo, Xcode o ambos.

<a name="adddevice"></a>

## <a name="add-a-device"></a>Agregación de un dispositivo

Al crear un perfil de aprovisionamiento para el desarrollo, hay que indicar qué dispositivos pueden ejecutar la aplicación. Para ello, se pueden agregar hasta 100 dispositivos por año natural en el portal para desarrolladores y, desde aquí, se pueden seleccionar los dispositivos que se agregarán a un determinado perfil de aprovisionamiento. Siga estos pasos en su Mac para agregar un dispositivo en el portal para desarrolladores.

1. Conecte el dispositivo que se va a aprovisionar al equipo Mac con el cable USB suministrado.
2. Abra Xcode y vaya a **Ventana > Dispositivos y simuladores**.
3. En la pestaña **Dispositivos**, seleccione el dispositivo en el menú del lado izquierdo.
4. Resalte la cadena **Identificador** y cópiela en el Portapapeles:

   ![La ventana del simulador y los dispositivos de Xcode con la ubicación de la cadena del identificador de iOS resaltada.](manual-provisioning-images/xcode-devices.png)

5. En un explorador web, vaya a la [sección de dispositivos en el portal para desarrolladores](https://developer.apple.com/account/resources/devices/list) y haga clic en el botón **+** :

   ![Captura de pantalla de la página de dispositivos en el sitio Apple Developer con el botón para agregar resaltado.](manual-provisioning-images/developer-portal-devices.png)

6. Seleccione la **plataforma** correcta y proporcione un nombre para el nuevo dispositivo. Pegue el identificador que ha copiado anteriormente en el campo **Id. del dispositivo**:

    ![Captura de pantalla de la nueva página de registro de dispositivos con los campos rellenados correctamente.](manual-provisioning-images/new-device-info.png)

7. Haga clic en **Continuar**.
8. Revise la información y, después, haga clic en **Registrar**.

Repita los pasos anteriores para todos los dispositivos iOS que se vayan a usar para probar o depurar una aplicación de Xamarin.iOS.

<a name="provisioningprofile"></a>

## <a name="create-a-development-provisioning-profile"></a>Creación de un perfil de aprovisionamiento de desarrollo

Después de agregar el dispositivo en el portal para desarrolladores, deberá crear un perfil de aprovisionamiento y agregarle el dispositivo. 

Antes de crear un perfil de aprovisionamiento, se debe crear un *Id. de aplicación*. Un identificador de aplicación es una cadena de estilo DNS inversa que identifica una aplicación de forma exclusiva. En los pasos siguientes se muestra cómo crear un **identificador de aplicación comodín**, que se puede usar para compilar e instalar la mayoría de las aplicaciones. Los **identificadores de aplicación explícitos** solo permiten la instalación de una aplicación (con el identificador de paquete coincidente) y generalmente se usan para determinadas características de iOS, como Apple Pay y HealthKit. Para más información sobre la creación de Id. de aplicación explícitos, vea la guía [Trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/index.md).

### <a name="new-wildcard-app-id"></a>Nuevo identificador de aplicación comodín

1. Vaya a la [sección de identificadores en el portal para desarrolladores](https://developer.apple.com/account/resources/identifiers/list) y haga clic en el botón **+** .
2. Seleccione **App IDs** (Id. de aplicación) y haga clic en **Continuar**.
3. Proporcione una **Descripción**. A continuación, establezca **Bundle ID** (Id. del lote) en **Wildcard** (Comodín) y escriba un identificador con el formato `com.[DomainName].*`:

   ![Captura de pantalla de la página de registro del nuevo identificador de aplicación con los campos obligatorios rellenados.](manual-provisioning-images/new-app-id.png)

4. Haga clic en **Continuar**.
5. Revise la información y, después, haga clic en **Registrar**.

### <a name="new-provisioning-profile"></a>Nuevo perfil de aprovisionamiento

Una vez que se ha creado el identificador de aplicación, se puede crear el perfil de aprovisionamiento. Este contiene información sobre a *qué* aplicación (o aplicaciones, si es un identificador de aplicación comodín) hace referencia este perfil, *quién* puede usar el perfil (en función de los certificados de desarrollador que se agreguen) y *qué* dispositivos pueden instalar la aplicación.

Para crear manualmente un perfil de aprovisionamiento para el desarrollo, haga lo siguiente:

1. Vaya a la [sección de perfiles en el portal para desarrolladores](https://developer.apple.com/account/resources/profiles/list) y haga clic en el botón **+** .

2. En **Development** (Desarrollo), seleccione **iOS App Development** (Desarrollo de aplicaciones iOS) y haga clic en **Continuar**.

3. En el menú desplegable, seleccione el identificador de aplicación que se va a usar y haga clic en **Continuar**.

4. Seleccione los certificados que se van a incluir en el perfil de aprovisionamiento y haga clic en **Continuar**.

5. Seleccione todos los dispositivos en los que se va a instalar la aplicación y, después, haga clic en **Continuar**.

6. Proporcione un **nombre del perfil de aprovisionamiento** y haga clic en **Generar**.

7. También puede hacer clic en **Descargar** en la página siguiente para descargar el perfil de aprovisionamiento en el equipo Mac.

<a name="download"></a>

## <a name="download-provisioning-profiles-in-visual-studio"></a>Descarga de perfiles de aprovisionamiento en Visual Studio

Después de crear un nuevo perfil de aprovisionamiento en el portal de Apple Developer, use Visual Studio para descargarlo para que esté disponible para la firma de lotes en la aplicación.

1. Vaya a la ventana de cuentas de desarrollador de Apple en Visual Studio.
    1. Mac: **Visual Studio > Preferencias > Cuenta de desarrollador de Apple**
    2. Windows: **Herramientas > Opciones > Xamarin > Apple Accounts (Cuentas de Apple)**

2. Seleccione su equipo y haga clic en **Ver detalles…**
3. Compruebe que el nuevo perfil aparece en la lista **Perfiles de aprovisionamiento**. Es posible que deba reiniciar Visual Studio para actualizar la lista. 
4. Haga clic en **Descargar todos los perfiles**.

El nuevo perfil de aprovisionamiento estará disponible en Visual Studio y listo para su uso.

## <a name="deploy-to-a-device"></a>Implementar en un dispositivo

En este momento, el aprovisionamiento se ha completado y la aplicación está lista para su implementación en el dispositivo. Para ello, siga estos pasos:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Conecte el dispositivo al equipo Mac.
2. Abra el archivo **Info.plist** y asegúrese de que el **identificador de lote** coincide con el identificador de la aplicación que ha creado anteriormente (a menos que este sea un carácter comodín):
3. En la sección **Firma**, seleccione **Aprovisionamiento manual** como el **esquema**:

    ![Captura de pantalla del archivo Info.plist en Visual Studio para Mac con el aprovisionamiento manual seleccionado.](manual-provisioning-images/vsm-info-plist.png)

4. Haga clic en **Bundle Signing Options…** (Opciones de firma de lotes…).
5. Asegúrese de que la configuración de compilación está establecida en **Depuración | iPhone**. Abra los menús desplegables **Identidad de firma** y **Perfil de aprovisionamiento** para comprobar que se muestran los certificados y los perfiles de aprovisionamiento correctos: 

   ![Página de propiedades de la firma de lotes de iOS con el menú desplegable “Perfil de aprovisionamiento” abierto; en él se enumeran todos los perfiles de aprovisionamiento disponibles para la aplicación.](manual-provisioning-images/vsm-bundle-signing.png)

6. Seleccione una identidad y un perfil específicos para usar o déjelo como **Automático**. Si está establecido en **Automático**, Visual Studio para Mac seleccionará la identidad y el perfil en función del **identificador de lote** de **Info.plist**. 
7. Haga clic en **Aceptar**.
8. Haga clic en **Ejecutar** para implementar la aplicación en su dispositivo.


# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Conecte su dispositivo al host de compilación de Mac.
2. Abra el archivo **Info.plist** y asegúrese de que el **identificador de lote** coincide con el identificador de la aplicación que ha creado anteriormente (a menos que este sea un carácter comodín):
3. En el **Explorador de soluciones**, haga clic con el botón derecho en el nombre del proyecto de iOS, seleccione **Propiedades** y vaya a la pestaña **Firma de lotes de iOS**.
4. Asegúrese de que la configuración de compilación está establecida en **Depuración | iPhone**. En **Firma de lotes**, seleccione **Aprovisionamiento manual** como el **esquema**:

    ![Captura de pantalla del archivo Info.plist en Visual Studio para Mac con el aprovisionamiento manual seleccionado.](manual-provisioning-images/vs-bundle-signing.png)

5. Abra los menús desplegables **Identidad de firma** y **Perfil de aprovisionamiento** para comprobar que se muestran los certificados y los perfiles de aprovisionamiento correctos.
6. Seleccione una identidad y un perfil específicos para usar o déjelo como **Automático**. Si está establecido en **Automático**, Visual Studio seleccionará la identidad y el perfil en función del **identificador de lote** de **Info.plist**. 
7. Haga clic en **Ejecutar** para implementar la aplicación en su dispositivo.

-----

## <a name="provisioning-for-application-services"></a>Aprovisionamiento de servicios de aplicación

Apple proporciona una selección de servicios de aplicación especiales, también denominados funciones, que pueden activarse para una aplicación de Xamarin.iOS. Estos servicios de aplicación deben configurarse en el portal de aprovisionamiento de iOS al crear el **identificador de aplicación** y en el archivo **Entitlements.plist** que forma parte del proyecto de la aplicación de Xamarin.iOS. Para obtener más información sobre cómo agregar servicios de aplicación a la suya propia, consulte las guías [Introduction to Capabilities](~/ios/deploy-test/provisioning/capabilities/index.md) (Introducción a las capacidades) y [Working with Entitlements](~/ios/deploy-test/provisioning/entitlements.md) (Trabajar con derechos).

- Cree un id. de aplicación con los servicios de aplicación necesarios.
- Cree un nuevo [perfil de aprovisionamiento](#provisioningprofile) que contenga dicho id. de aplicación.
- Establecer derechos en el proyecto de Xamarin.iOS

## <a name="related-links"></a>Vínculos relacionados

- [Aprovisionamiento gratis](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [App Distribution](~/ios/deploy-test/app-distribution/index.md) (Distribución de aplicaciones)
- [Solución de problemas](~/ios/deploy-test/troubleshooting.md)
- [Guía de distribución de aplicaciones de Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
