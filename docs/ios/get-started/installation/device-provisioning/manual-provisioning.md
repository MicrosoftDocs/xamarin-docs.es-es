---
title: Aprovisionamiento manual de Xamarin.iOS
description: Una vez que se ha instalado correctamente Xamarin.iOS, el paso siguiente en el desarrollo de iOS consiste en aprovisionar el dispositivo iOS. En esta guía se analiza el uso del aprovisionamiento manual para establecer certificados de desarrollo y perfiles.
ms.prod: xamarin
ms.assetid: E26ACC94-F4A5-4FF5-B7D4-BE596745A665
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/15/2017
ms.openlocfilehash: 5f56a99e341f58bc06e8bf49305b5527a92f0413
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289714"
---
# <a name="manual-provisioning-for-xamarinios"></a>Aprovisionamiento manual de Xamarin.iOS

_Una vez que se ha instalado correctamente Xamarin.iOS, el paso siguiente en el desarrollo de iOS consiste en aprovisionar el dispositivo iOS. En esta guía se analiza el uso del aprovisionamiento manual para establecer certificados de desarrollo y perfiles._

> [!NOTE]
> Las instrucciones que se muestran en esta página son relevantes para los desarrolladores que han pagado por el acceso al programa de Apple Developer. Si tiene una cuenta gratuita, consulte la guía de [aprovisionamiento gratuito](~/ios/get-started/installation/device-provisioning/free-provisioning.md) para obtener más información acerca de las pruebas en dispositivos.

## <a name="creating-a-signing-identity"></a>Creación de una identidad de firma

El primer paso para configurar un dispositivo de desarrollo consiste en crear una identidad de firma. Una identidad de firma consta de dos partes:

- Un certificado de desarrollo
- Una clave privada

Los certificados de desarrollo y las [claves](#understanding-certificate-key-pairs) asociadas son fundamentales para un desarrollador de iOS, ya que establecen su identidad con Apple y lo asocian a un dispositivo determinado y a un perfil de desarrollo, como si incluyeran su firma digital en las aplicaciones. Apple busca certificados para controlar el acceso a los dispositivos en los que se puede implementar.

Los equipos de desarrollo, los certificados y los perfiles pueden administrarse mediante el acceso a la sección [Certificados, identificadores y perfiles](https://developer.apple.com/account/overview.action) del Centro de usuarios registrados de Apple (se requiere iniciar sesión). Apple requiere que tenga una identidad de firma para compilar el código para el dispositivo o el simulador.  

> [!IMPORTANT]
> Es importante que tenga en cuenta que solo puede tener dos certificados de desarrollo de iOS en todo momento. Si necesita crear otro, deberá revocar uno existente. Si una máquina usa un certificado revocado, no podrá firmar la aplicación.

Para generar una identidad de firma, haga lo siguiente:

1. Inicie sesión en la sección [Certificates, Identifiers, and Profiles](https://developer.apple.com/account/overview.action) (Certificados, identificadores y perfiles) del portal para desarrolladores y seleccione la sección **Certificados** en la columna **iOS Apps** (Aplicaciones iOS). Después, pulse el botón **+** para crear un certificado:

    [![](manual-provisioning-images/cert-plus.png "Clic en el botón + para crear un certificado")](manual-provisioning-images/cert-plus.png#lightbox)

2. Seleccione la opción **iOS App Development** (Desarrollo de aplicaciones iOS) para el tipo de certificado y haga clic en **Continuar**. Esta pantalla puede tener un aspecto diferente, en función de los privilegios de cuenta:

    [![](manual-provisioning-images/cert-first.png "Selección de la opción iOS App Development (Desarrollo de aplicaciones iOS) para el tipo de certificado")](manual-provisioning-images/cert-first.png#lightbox)

3. Cree una solicitud de firma de certificado, que se cargará para generar un certificado manualmente. Para ello, inicie **Acceso a llaves** en un equipo Mac. Vaya al menú principal y seleccione **Certificate Assistant** (Asistente de certificados) y **Request a Certificate from a Certificate Authority…** (Solicitar un certificado de una entidad de certificación…), como se muestra a continuación:

      [![](manual-provisioning-images/key-first.png "Solicitud de firma de certificado")](manual-provisioning-images/key-first.png#lightbox)

4. Rellene la información y seleccione la opción **Guardar en disco**:

    [![](manual-provisioning-images/key-second.png "Rellenado de la información")](manual-provisioning-images/key-second.png#lightbox)

5. Guarde la CSR en una ubicación donde se pueda encontrar fácilmente:

    [![](manual-provisioning-images/cert-third.png "Guardar el CSR")](manual-provisioning-images/cert-third.png#lightbox)

6. Vuelva al portal de aprovisionamiento, cargue el certificado en el portal y envíelo:

    [![](manual-provisioning-images/cert-second.png "Carga del certificado en el portal")](manual-provisioning-images/cert-second.png#lightbox)

    Si no tiene privilegios de administrador, un administrador o un agente de equipo deberán aprobar el certificado.

7. Una vez que se haya aprobado el certificado, descárguelo desde el portal de aprovisionamiento:

    [![](manual-provisioning-images/status-dev.png "Descarga del certificado desde el portal de aprovisionamiento")](manual-provisioning-images/status-dev.png#lightbox)

8. Haga doble clic en el certificado descargado para iniciar Acceso a llaves y abra el panel **Mis certificados**, en el que se muestran los certificados nuevos, y clave privada asociada:

    [![](manual-provisioning-images/keychain.png "Certificado en Acceso a llaves")](manual-provisioning-images/keychain.png#lightbox)

### <a name="understanding-certificate-key-pairs"></a>Introducción a los pares de claves de certificado

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

El perfil de desarrollador contiene certificados, sus claves asociadas y los perfiles de aprovisionamiento asociados con la cuenta. Cada perfil de desarrollador tiene dos versiones: una se encuentra en el portal para desarrolladores y la otra reside en un equipo Mac local. La diferencia entre ambas es el tipo de claves que contienen: _el perfil del portal contiene todas las claves públicas asociadas con los certificados, mientras que la copia del equipo Mac local contiene todas las claves privadas_. Para que los certificados sean válidos, los pares de claves deben coincidir. Conserve una copia de seguridad del perfil de desarrollador en el equipo Mac local, porque si se pierden las claves privadas, habrá que volver a generar todos los certificados y los perfiles de aprovisionamiento.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

El perfil de desarrollador contiene certificados, sus claves asociadas y los perfiles de aprovisionamiento asociados con la cuenta. Cada perfil de desarrollador tiene dos versiones: una se encuentra en el portal para desarrolladores y la otra reside en un equipo Mac. La diferencia entre ambas es el tipo de claves contienen: _el perfil del portal contiene todas las claves públicas asociadas con los certificados, mientras que la copia del equipo Mac contiene todas las claves privadas_. Para que los certificados sean válidos, los pares de claves deben coincidir. Conserve una copia de seguridad del perfil de desarrollador en el equipo Mac del host de compilación de Xamarin, porque si se pierden las claves privadas, habrá que volver a generar todos los certificados y los perfiles de aprovisionamiento.

-----

> [!WARNING]
> La pérdida del certificado y de las claves asociadas puede causar muchos problemas, ya que requerirá que se revoquen los certificados existentes y que se vuelvan a aprovisionar los dispositivos asociados, incluidos los registrados para la implementación ad hoc. Después de configurar correctamente los certificados de desarrollo, exporte una copia de seguridad y almacénelos en un lugar seguro. Para obtener más información sobre cómo hacerlo, consulte la sección sobre la exportación y la importación de certificados y perfiles de la guía [Maintaining Certificates](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/MaintainingCertificates/MaintainingCertificates.html) (Mantener certificados) en la documentación de Apple.

<a name="provisioning" />

## <a name="provisioning-an-ios-device-for-development"></a>Aprovisionamiento de un dispositivo iOS para el desarrollo

Ahora que ha establecido la identidad con Apple y tiene un certificado de desarrollo, debe configurar un perfil de aprovisionamiento y las entidades necesarias para poder implementar una aplicación en un dispositivo Apple. El dispositivo debe ejecutar una versión de iOS compatible con Xcode. Por ello, puede ser necesario actualizar el dispositivo, Xcode o ambos.

<a name="adddevice" />

## <a name="add-a-device"></a>Agregación de un dispositivo

Al crear un perfil de aprovisionamiento para el desarrollo, hay que indicar qué dispositivos pueden ejecutar la aplicación. Para ello, se pueden agregar hasta 100 dispositivos por año natural en el portal para desarrolladores y, desde aquí, se pueden seleccionar los dispositivos que se agregarán a un determinado perfil de aprovisionamiento. Siga estos pasos en su Mac para agregar un dispositivo en el portal para desarrolladores.

1. Inicie Xcode.
2. Conecte el dispositivo que se va a aprovisionar al equipo Mac con el cable USB suministrado.
3. En el menú **Ventana**, seleccione **Dispositivos**:

   [![](manual-provisioning-images/add01.png "Selección de Dispositivos en el menú Ventana")](manual-provisioning-images/add01.png#lightbox)

4. Seleccione el dispositivo iOS deseado en la lista **DISPOSITIVOS** que aparece en el lado izquierdo de la ventana Dispositivos.
5. Resalte la cadena **Identificador** y cópiela en el Portapapeles:

   [![](manual-provisioning-images/add02.png "Resaltado de la cadena Identificador")](manual-provisioning-images/add02.png#lightbox)

6. En Safari, vaya al [Centro para desarrolladores de Apple](https://developer.apple.com/membercenter/index.action) e inicie sesión.
7. Haga clic en el vínculo **Certificates, Identifiers & Profiles** (Certificados, identificadores y perfiles):

   [![](manual-provisioning-images/add03.png "Clic en el vínculo Certificados, identificadores y perfiles")](manual-provisioning-images/add03.png#lightbox)

8. Haga clic en el vínculo **Dispositivos**:

   [![](manual-provisioning-images/add04.png "Clic en el vínculo Dispositivos")](manual-provisioning-images/add04.png#lightbox)

9. Haga clic en el botón **+** :

   [![](manual-provisioning-images/add05.png "Clic en el botón +")](manual-provisioning-images/add05.png#lightbox)

10. Proporcione un nombre para el nuevo dispositivo y pegue el **identificador** de dispositivo que copió anteriormente en el campo **UUID**:

    [![](manual-provisioning-images/add06.png "Nombre para el nuevo dispositivo e identificador de dispositivo")](manual-provisioning-images/add06.png#lightbox)

11. Haga clic en el botón **Continuar**.
12. Por último, revise la información y haga clic en el botón **Registrar**:

    [![](manual-provisioning-images/add07.png "Revisión de la información")](manual-provisioning-images/add07.png#lightbox)

Repita los pasos anteriores para todos los dispositivos iOS que se vayan a usar para probar o depurar una aplicación de Xamarin.iOS.

Después de agregar el dispositivo en el portal para desarrolladores, deberá crear un perfil de aprovisionamiento y agregarle el dispositivo.

<a name="provisioningprofile" />

## <a name="creating-a-development-provisioning-profile"></a>Creación de un perfil de aprovisionamiento de desarrollo

Al igual que con el certificado de desarrollo, se pueden crear perfiles de aprovisionamiento de forma manual a través de la sección [Certificados, identificadores y perfiles](https://developer.apple.com/account/overview.action) del Centro de usuarios registrados de Apple.

Antes de crear un perfil de aprovisionamiento, se debe crear un *Id. de aplicación*. Un identificador de aplicación es una cadena de estilo DNS inversa que identifica una aplicación de forma exclusiva. En los pasos siguientes se muestra cómo crear un **identificador de aplicación comodín**, que se puede usar para compilar e instalar la mayoría de las aplicaciones. Los **identificadores de aplicación explícitos** solo permiten la instalación de una aplicación (con el identificador de paquete coincidente) y generalmente se usan para determinadas características de iOS, como Apple Pay y HealthKit. Para más información sobre la creación de Id. de aplicación explícitos, vea la guía [Trabajar con capacidades](~/ios/deploy-test/provisioning/capabilities/index.md).

### <a name="app-id"></a>Identificador de la aplicación

1. En el [portal para desarrolladores](https://developer.apple.com/account/overview.action), vaya a la sección *Certificate, Identifiers and Profiles* (Certificado, identificadores y perfiles) del Centro para desarrolladores de Apple. Seleccione **App IDs** (Identificadores de aplicación) en **Identifiers** (Identificadores).
2. Haga clic en el botón **+** y proporcione un **nombre**:

    [![](manual-provisioning-images/appid05a.png "Proporcionar un nombre")](manual-provisioning-images/appid05a.png#lightbox)
3. El prefijo de la aplicación debería estar preestablecido. Seleccione **Wildcard App ID** (Identificador de aplicación comodín) para el sufijo de la aplicación. Escriba un identificador de paquete en el formato `com.[DomainName].*`:

   [![](manual-provisioning-images/appid05b.png "Escribir un identificador de lote")](manual-provisioning-images/appid05b.png#lightbox)

4. Haga clic en el botón **Continuar** y siga las instrucciones en pantalla para crear el identificador de aplicación.

### <a name="provisioning-profile"></a>Perfil de aprovisionamiento

Una vez que se ha creado el identificador de aplicación, se pueden generar el perfil de aprovisionamiento. Este contiene información sobre a *qué* aplicación (o aplicaciones, si es un identificador de aplicación comodín) hace referencia este perfil, *quién* puede usar el perfil (en función de los certificados de desarrollador que se agreguen) y *qué* dispositivos pueden instalar la aplicación.

Para crear manualmente un perfil de aprovisionamiento para el desarrollo, haga lo siguiente:

1. Use Safari para ir al [Centro de usuarios registrados de Apple Developer](https://developer.apple.com/membercenter/index.action) y, en la sección *Certificates, Identifiers & Profiles* (Certificados, identificadores y perfiles), seleccione Perfiles de aprovisionamiento.
2. Haga clic en el botón **+** en la esquina superior derecha para crear un perfil.
3. En la sección de **desarrollo**, seleccione el botón de opción situado junto a **iOS App Development** (Desarrollo de aplicaciones iOS) y presione **Continuar**:

    [![](manual-provisioning-images/provisioning-profile01.png "Selección del tipo de perfil que se va a crear")](manual-provisioning-images/provisioning-profile01.png#lightbox)
4. En el menú desplegable, seleccione el identificador de aplicación que quiera usar:

    [![](manual-provisioning-images/provisioning-profile02.png "Selección del id. de aplicación que se va a usar")](manual-provisioning-images/provisioning-profile02.png#lightbox)
5. Seleccione los certificados que se van a incluir en el perfil de aprovisionamiento y presione **Continuar**:

    [![](manual-provisioning-images/provisioning-profile03.png "Selección de los certificados que se van a incluir en el perfil de aprovisionamiento")](manual-provisioning-images/provisioning-profile03.png#lightbox)
6. Seleccione todos los dispositivos en los que se instalará la aplicación.

    [![](manual-provisioning-images/provisioning-profile04.png "Selección de todos los dispositivos en los que se va a instalar la aplicación")](manual-provisioning-images/provisioning-profile04.png#lightbox)
7. Asígnele al perfil de aprovisionamiento un nombre identificable y presione **Continuar** para crear el perfil:

    [![](manual-provisioning-images/provisioning-profile05.png "Proporcionar un nombre identificable al perfil de aprovisionamiento")](manual-provisioning-images/provisioning-profile05.png#lightbox)
8. Presione **Descargar** para descargar el perfil de aprovisionamiento en un equipo Mac:

    [![](manual-provisioning-images/provisioning-profile06.png "Descarga del perfil de aprovisionamiento")](manual-provisioning-images/provisioning-profile06.png#lightbox)
9. Haga doble clic en el archivo para instalar el perfil de aprovisionamiento en Xcode. Tenga en cuenta que Xcode podría no mostrar ninguna indicación visual de que ha instalado el perfil, excepto para abrirlo. Para comprobarlo, vaya a **Xcode > Preferencias > Cuentas**. Seleccione su identificador de Apple y haga clic en **Ver detalles…** . El nuevo perfil de aprovisionamiento debería aparecer en la lista, como se muestra a continuación:

      [![](manual-provisioning-images/provisioning-profile07.png "Visualización del perfil en Xcode")](manual-provisioning-images/provisioning-profile07.png#lightbox)

Después de que se haya creado correctamente el perfil de aprovisionamiento, podría ser necesario actualizar Xcode para que todos los certificados de desarrollo estén disponibles para Visual Studio para Mac y Visual Studio.

<a name="download" />

## <a name="downloading-profiles-and-certificates-in-xcode"></a>Descarga de perfiles y certificados en Xcode

Los certificados y los perfiles de aprovisionamiento que se han creado en el portal para desarrolladores de Apple podrían no aparecer automáticamente en Xcode. Por lo tanto, puede ser necesario descargarlos para que Visual Studio para Mac y Visual Studio tengan acceso a ellos. Para actualizar y descargar los certificados creados en el portal para desarrolladores de Apple, haga lo siguiente:

1. Salga de Visual Studio para Mac o Visual Studio.
2. Inicie Xcode.
3. Seleccione el **menú de Xcode > Preferencias…** .
4. Haga clic en la pestaña **Cuentas**.
5. Seleccione un equipo y haga clic en el botón **Download Manual Profiles** (Descargar perfiles manuales):  [![](manual-provisioning-images/selectteam1.png "Descarga de perfiles manuales")](manual-provisioning-images/selectteam1.png#lightbox)

6. Salga de Xcode.
7. Inicie Visual Studio para Mac o Visual Studio.

Los nuevos certificados o perfiles de aprovisionamiento estarán disponibles en Visual Studio para Mac o Visual Studio y ya podrán usarse.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

> [!IMPORTANT]
> Puede que sea necesario detener y reiniciar Visual Studio para Mac para que pueda ver los certificados o perfiles nuevos o modificados que haya actualizado Xcode.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

> [!IMPORTANT]
> Puede que sea necesario detener y reiniciar Visual Studio para que pueda ver los certificados o perfiles nuevos o modificados que haya actualizado Xcode.

-----

<a name="appservices" />

## <a name="provisioning-for-application-services"></a>Aprovisionamiento de servicios de aplicación

Apple proporciona una selección de servicios de aplicación especiales, también denominados funciones, que pueden activarse para una aplicación de Xamarin.iOS. Estos servicios de aplicación deben configurarse en el portal de aprovisionamiento de iOS al crear el **identificador de aplicación** y en el archivo **Entitlements.plist** que forma parte del proyecto de la aplicación de Xamarin.iOS. Para obtener más información sobre cómo agregar servicios de aplicación a la suya propia, consulte las guías [Introduction to Capabilities](~/ios/deploy-test/provisioning/capabilities/index.md) (Introducción a las capacidades) y [Working with Entitlements](~/ios/deploy-test/provisioning/entitlements.md) (Trabajar con derechos).

- Cree un id. de aplicación con los servicios de aplicación necesarios.
- Cree un nuevo [perfil de aprovisionamiento](#provisioningprofile) que contenga dicho id. de aplicación.
- Establecer derechos en el proyecto de Xamarin.iOS

## <a name="deploying-to-a-device"></a>Implementación en un dispositivo

En este momento, el aprovisionamiento se ha completado y la aplicación está lista para su implementación en el dispositivo. Para ello, siga estos pasos:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

> [!IMPORTANT]
> Antes de comenzar, asegúrese de seleccionar **Aprovisionamiento manual** en **Info.plist**.

1. Conecte el dispositivo a un equipo Mac.
2. En el archivo **Info.plist** del proyecto, asegúrese de que el identificador de paquete coincide con el identificador de aplicación (a menos que este sea un carácter comodín):

   ![](manual-provisioning-images/deploydevice01xs.png "Especificación de un identificador")

3. Haga clic con el botón derecho en el proyecto para ver el cuadro de diálogo de opciones de proyecto y vaya a **Compilar > agrupación de trabajos iOS** (Firma de paquete de iOS). En la lista desplegable situada junto a **Identidad de firma** y **Perfil de aprovisionamiento**, compruebe que Visual Studio para Mac puede ver los perfiles correctos y seleccione una identidad y un perfil específicos:

   ![](manual-provisioning-images/deploydevice02xs.png "Seleccione una identidad y un perfil concretos")

   Si está establecido en **Automático**, Visual Studio para Mac seleccionará la identidad y el perfil en función del identificador de paquete que se estableció en el paso 2.

4. Asegúrese de establecer la configuración de compilación en **iPhone** / **iPad**, en lugar de en simulador.
5. Haga clic en **Ejecutar** en Visual Studio para Mac y compruebe que la aplicación se ejecuta en el dispositivo.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

> [!IMPORTANT]
> Antes de comenzar, asegúrese de seleccionar **Aprovisionamiento manual** en **Proyecto > Propiedades de aprovisionamiento…**

1. Conecte el dispositivo al host de compilación de Mac.
2. En el archivo **Info.plist** del proyecto, asegúrese de que el identificador de paquete coincide con el identificador de aplicación:

   ![](manual-provisioning-images/servicevs01.png "Especificación de un identificador")

3. Haga clic con el botón derecho en el proyecto para ver el cuadro de diálogo de opciones de proyecto y vaya a **Compilar > agrupación de trabajos iOS** (Firma de paquete de iOS). En la lista desplegable situada junto a **Identidad de firma** y **Perfil de aprovisionamiento**, compruebe que Visual Studio puede ver los perfiles correctos y seleccione una identidad y un perfil específicos.

    Si está establecido en **Automático**, Visual Studio seleccionará la identidad y el perfil en función del identificador de paquete que se estableció en el paso 2.

4. Asegúrese de establecer la configuración de compilación en **iPhone** o **iPad**, en lugar de en simulador.
5. Haga clic en **Ejecutar** en Visual Studio y compruebe que la aplicación se ejecuta en el dispositivo.


-----

## <a name="summary"></a>Resumen

En esta guía hemos tratado los pasos necesarios para configurar el entorno de desarrollo para Xamarin.iOS. Hemos visto cómo se firma el código de una aplicación con información sobre el desarrollador, su equipo, los dispositivos en los que se puede ejecutar la aplicación y el identificador de aplicación individual.

## <a name="related-links"></a>Vínculos relacionados

- [Aprovisionamiento gratis](~/ios/get-started/installation/device-provisioning/free-provisioning.md)
- [App Distribution](~/ios/deploy-test/app-distribution/index.md) (Distribución de aplicaciones)
- [Solución de problemas](~/ios/deploy-test/troubleshooting.md)
- [Guía de distribución de aplicaciones de Apple](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html)
