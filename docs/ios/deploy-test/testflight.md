---
title: Uso de TestFlight para distribuir aplicaciones Xamarin.iOS
description: TestFlight es ahora propiedad de Apple y se considera la manera principal de realizar pruebas beta de las aplicaciones Xamarin.iOS. En este artículo se explican todos los pasos del proceso de TestFlight, desde cargar la aplicación hasta trabajar con iTunes Connect.
ms.prod: xamarin
ms.assetid: BA880768-2BC8-41E4-B57E-A56F8EED4690
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 1f86227573fc5e9dc39d4781125c49a067e8967d
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997493"
---
# <a name="using-testflight-to-distribute-xamarinios-apps"></a>Uso de TestFlight para distribuir aplicaciones Xamarin.iOS

_TestFlight es ahora propiedad de Apple y se considera la manera principal de realizar pruebas beta de las aplicaciones Xamarin.iOS. En este artículo se explican todos los pasos del proceso de TestFlight, desde cargar la aplicación hasta trabajar con iTunes Connect._

Las pruebas beta son una parte integral del ciclo de desarrollo de software y hay muchas aplicaciones multiplataforma para simplificar el proceso, como [HockeyApp](https://hockeyapp.net/features/), [Applause](https://www.applause.com/mobile-app-testing) y, por supuesto, la aplicación de pruebas beta de aplicaciones nativas de Google Play para aplicaciones Android. Este documento se centra en TestFlight de Apple.

TestFlight es el servicio de pruebas beta de Apple para aplicaciones iOS y solo es accesible a través de [iTunes Connect](https://itunesconnect.apple.com/). Está actualmente disponible para aplicaciones iOS 8.0 y versiones posteriores. TestFlight permite realizar pruebas beta con usuarios internos y externos y, debido a una revisión de la aplicación Beta para estos últimos, garantiza un proceso mucho más sencillo en la revisión final al publicar en el App Store.

Anteriormente, el binario se generaba en Visual Studio para Mac y se cargaba en el sitio web de TestFlightApp para la distribución a los evaluadores. Con el nuevo proceso, hay una serie de mejoras que le permitirán tener aplicaciones de alta calidad y bien probadas en el App Store. Por ejemplo:

- La revisión de la aplicación beta necesaria para las pruebas externas garantiza una mayor probabilidad de éxito de su revisión final en el App Store, ya que ambas requieren el cumplimiento de las directrices de Apple.
- Antes de cargar, la aplicación debe estar registrada en iTunes Connect. Esto garantiza que no habrá ninguna discrepancia entre los perfiles, los nombres y los certificados de aprovisionamiento.
- La aplicación TestFlight ahora es una aplicación iOS real, por lo que funciona más rápidamente.
- Una vez completada la fase de pruebas beta, el proceso de mover la aplicación para revisar es rápido y eficaz; un solo clic de botón.

## <a name="requirements"></a>Requisitos

Solo las aplicaciones que sean iOS 8.0 o versiones superiores se pueden probar mediante TestFlight.

Todos los evaluadores deben probar la aplicación, al menos, en un dispositivo iOS 8. Sin embargo se recomienda que la aplicación se pruebe en todas las versiones de iOS

## <a name="provisioning"></a>Aprovisionamiento

Para probar las compilaciones con TestFlight, deberá crear un *perfil de distribución del App Store* con los nuevos derechos beta. Este derecho permite realizar pruebas beta a través de TestFlight, y cualquier **nuevo** perfil de distribución del App Store contiene automáticamente este derecho. Puede seguir las instrucciones detalladas de la guía [Crear un perfil de distribución](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile) para generar un perfil nuevo.

Puede confirmar que el perfil de distribución contiene los derechos de la versión beta cuando [valide la compilación en Xcode](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md), como se muestra a continuación:

[![Envío de la aplicación a Apple](testflight-images/validate-build.png)](testflight-images/validate-build.png#lightbox)

## <a name="testflight-workflow"></a>Flujo de trabajo de TestFlight

El flujo de trabajo siguiente describe los pasos necesarios para empezar a usar TestFlight para realizar pruebas beta de la aplicación:

1. En el caso de aplicaciones nuevas, cree un [registro de iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md).
2. [Archive y publique](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) la aplicación en iTunes Connect.
3. Administrar las pruebas beta:
    - Agregue los metadatos.
    - Agregar usuarios internos:
      - 25 usuarios como máximo.
    - Agregar usuarios externos:
      - 1000 usuarios como máximo.
      - Requiere una revisión de prueba beta, que debe ser conforme a las directrices de Apple.
4. Reciba comentarios de los usuarios, tome las medidas oportunas y vuelva al paso 2.

## <a name="create-an-itunes-connect-record"></a>Crear un registro de iTunes Connect

1. Inicie sesión en el [Portal de iTunes Connect](https://itunesconnect.apple.com/) con sus credenciales de desarrollador de Apple.
2. Seleccione **Mis aplicaciones**:

    [![Selección de Mis aplicaciones](testflight-images/my-apps.png)](testflight-images/my-apps.png#lightbox)

3. En la pantalla **Mis aplicaciones**, haga clic en el botón **+** situado en la esquina superior izquierda de la pantalla para agregar una aplicación nueva. Si tiene cuentas de desarrollador de iOS y Mac, se le pedirá que elija el tipo de aplicación nueva aquí.

Se mostrará la ventana de envío **Nueva aplicación iOS**, que debe contener exactamente la misma información que el archivo Info.plist de la aplicación.

Para obtener más información acerca de cómo crear un nuevo registro de iTunes Connect, consulte la guía [Crear un registro de iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md).

### <a name="completing-the-new-ios-app-submission-form"></a>Completar el formulario de envío de la aplicación iOS nueva

El formulario debe reflejar exactamente la información que contiene el archivo Info.plist de la aplicación, como se muestra a continuación:

[![Info.plist de la aplicación](testflight-images/infoplist.png)](testflight-images/infoplist.png#lightbox)
[![Formulario en iTunes Connect](testflight-images/newiosapp.png)](testflight-images/newiosapp.png#lightbox)

- **Nombre**: el nombre descriptivo que se ha usado al configurar el paquete de aplicaciones. Debe coincidir exactamente con la entrada del **nombre de la aplicación** en su `Info.plist`.
- **Idioma principal**: el idioma base que se utiliza en la aplicación. Normalmente es el idioma que usted habla.
- **ID de paquete**: un menú desplegable en que se enumeran todos los ID de aplicaciones creados en la cuenta de desarrollador.
  - **Sufijo del ID de paquete**: si ha seleccionado un ID de paquete comodín (es decir, que termina en *, como en nuestro ejemplo anterior), se mostrará un cuadro adicional en que se pedirá el sufijo del ID de paquete. En el ejemplo, el **ID de paquete** es `mobi.chkn.*` y el sufijo es **PageView**. Los dos elementos componen el **Identificador de paquete** en nuestro `Info.plist`.
- **Versión**: el número de versión de la aplicación que se va a cargar. La elige el desarrollador.
- **SKU**: el SKU es un ID único para la aplicación, que los usuarios no verán. Se puede considerar como si fuera un ID de producto. En el ejemplo anterior, he elegido la fecha junto con un número de versión de esa fecha.

## <a name="upload-your-app"></a>Cargar la aplicación

Una vez creado el registro de iTunes Connect, podrá cargar nuevas compilaciones. Recuerde que las compilaciones deben tener el derecho beta nuevo.

En primer lugar, compile la versión [final distribuible](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) en el IDE y, a continuación, [envíe la aplicación a Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) mediante el cargador de aplicaciones o la función de archivo en Xcode.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

### <a name="create-an-archive"></a>Crear un archivo

 Para generar un archivo binario en Visual Studio para Mac, debe utilizar la función _Archivo_. Haga doble clic en el proyecto y seleccione **Archivar para la publicación**, como se muestra a continuación:

 [![Selección de Archivo para publicar](testflight-images/new-archive.png)](testflight-images/new-archive.png#lightbox)

 Consulte la guía [Compilar la versión distribuible](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) para obtener más información.

### <a name="sign-and-distribute-your-app"></a>Firmar y distribuir la aplicación

 Al crear un archivo se abre automáticamente la **Vista de archivos**, en que se muestran todos los proyectos archivados, agrupados por solución. Para firmar la aplicación y prepararla para su distribución, seleccione **Firmar y distribuir...** , como se muestra a continuación:

[![Al crear un archivo se abre automáticamente la vista Archivos.](testflight-images/archive-view.png)](testflight-images/archive-view.png#lightbox)

 De este modo, se abrirá el asistente para publicación. Seleccione el canal de distribución **App Store** para crear un paquete y abra el cargador de aplicaciones. En la pantalla Perfil de aprovisionamiento, seleccione su identidad de firma y el perfil de aprovisionamiento correspondiente, o vuelva a firmar con otra identidad. Compruebe los detalles del paquete y haga clic en **Publicar** para guardar su `.ipa`

[![Selección de la identidad de firma y el perfil de aprovisionamiento, nueva firma con otra identidad](testflight-images/group.png)](testflight-images/group.png#lightbox)

 Consulte la sección [Enviar la aplicación a Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) para obtener más información sobre estos pasos.

### <a name="submitting-your-build"></a>Enviar la compilación
 El Asistente de publicación abrirá el programa del cargador de aplicaciones para que cargue la compilación en iTunes Connect. Seleccione la opción **Entregar la aplicación** y cargue el archivo `.ipa` creado anteriormente. El cargador de aplicaciones validará y cargará la compilación en iTunes Connect.

 Consulte la sección [Enviar la aplicación a Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) para obtener más información sobre estos pasos.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

### <a name="building-your-final-distributable"></a>Compilar la versión final distribuible
 Como el complemento Xamarin para Visual Studio no admite archivar aplicaciones Xamarin.iOS para publicarlas en el App Store, hay dos opciones para publicar una aplicación iOS desde Visual Studio. Estos son:

1. Cargar un IPA creado mediante el comando de compilación ad hoc IPA.
1. Cargar un paquete `.app` comprimido.

 La guía [Compilar la versión distribuible](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) contiene instrucciones para ambas opciones.

### <a name="submitting-your-build"></a>Enviar la compilación
 Para enviar la aplicación a Apple, tendrá que migrar al host de compilación y usar el programa Application Loader, que está instalado como parte de Xcode. Para obtener más información sobre cómo acceder a Application Loader, consulte la guía de Apple [Access Application Loader](https://help.apple.com/itc/apploader/#/apdATD1E927-D1E1A1303-D1E927A1126) (Acceso a Application Loader).

Una vez abierto, seleccione la opción **Deliver Your App** (Entregar la aplicación) y cargue el archivo ZIP o `.ipa` creado anteriormente. El cargador de aplicaciones validará y cargará la compilación en iTunes Connect.

 Consulte la sección [Enviar la aplicación a Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) para obtener más información sobre estos pasos.

-----

En la guía [Publicación en el App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) se describen todos los pasos anteriores con más detalle; consúltela para obtener información más detallada sobre el proceso de envío al App Store.

Al volver a la sección **Mis aplicaciones** de iTunes Connect, comprobará que la aplicación se ha cargado correctamente. En este momento ya está listo para realizar algunas pruebas beta.

## <a name="manage-beta-testing"></a>Administrar las pruebas beta

### <a name="add-metadata"></a>Agregar los metadatos

Para empezar a usar TestFlight, vaya a la pestaña **Versión preliminar** de la aplicación. Verá tres pestañas en que se presenta una lista de compilaciones y evaluadores internos y externos, como se muestra a continuación:

[![Pestañas Compilaciones, Evaluadores internos y Evaluadores externos](testflight-images/app-uploaded.png)](testflight-images/app-uploaded.png#lightbox)

Para agregar metadatos a la aplicación, haga clic en el número de compilación y, a continuación, ejecute TestFlight:

[![Adición de metadatos](testflight-images/metadata.png)](testflight-images/metadata.png#lightbox)

En **Información sobre la prueba**, puede proporcionar a los evaluadores información importante sobre la aplicación, por ejemplo:

- Qué se va a probar
- Descripción de la aplicación.
- Dirección URL de marketing: proporciona información acerca de la aplicación que va a agregar.
- Dirección URL de la directiva de privacidad: una URL que proporciona información sobre la directiva de privacidad de su empresa.
- Correo electrónico de comentarios.

Tenga en cuenta que los evaluadores internos **no** necesitan estos metadatos, pero **sí** los evaluadores externos.

<a name="beta-testing"></a>

### <a name="enable-beta-testing"></a>Habilitar las pruebas beta

Cuando esté listo para comenzar a probar la aplicación, active el conmutador **Pruebas beta con TestFlight** correspondiente a su versión:

[![Activación del conmutador Pruebas beta con TestFlight](testflight-images/turn-on-testing.png)](testflight-images/turn-on-testing.png#lightbox)

Cada compilación está activa durante **60 días** desde la fecha en que haya activado el conmutador beta de TestFlight. Puede ver cuántos días quedan para cada compilación en la página **Información sobre la prueba**:

[![Página Información de la prueba](testflight-images/daysleft.png)](testflight-images/daysleft.png#lightbox)

Las pruebas se pueden desactivar en cualquier momento.

### <a name="internal-testers"></a>Evaluadores internos

Los evaluadores internos son miembros de su equipo de desarrollo a quienes se ha asignado uno de los roles siguientes en iTunes Connect:

- **Administrador**: un administrador es responsable de agregar y administrar los usuarios nuevos en iTunes Connect.
- **Legal**: el agente de equipo es el único usuario administrador a quien se asignará el rol Legal. Dicho rol le permite firmar contratos legales.
- **Técnico**: un usuario técnico puede cambiar la mayoría de las propiedades relativas a una aplicación. Por ejemplo, puede editar la información de la aplicación, cargar un archivo binario y enviar una aplicación para que se revise.

Cada compilación se puede compartir con un máximo de 25 miembros.

Para agregar evaluadores, vaya a **Usuarios y roles** en la pantalla principal de iTunes Connect:

[![Usuarios y roles en la pantalla principal de iTunes Connect](testflight-images/users-and-roles.png)](testflight-images/users-and-roles.png#lightbox)

Los usuarios existentes de iTunes Connect se mostrarán en la lista. Para seleccionarlos, haga clic en su nombre, active el conmutador **Evaluador interno** y haga clic en **Guardar**:

[![Activación del conmutador Evaluador interno](testflight-images/internal-tester.png)](testflight-images/internal-tester.png#lightbox)

Para agregar a un usuario que no está en la lista, seleccione el botón **+** situado junto a *Usuarios* y proporcione un nombre, un apellido y una dirección de correo electrónico para crear una cuenta. El usuario deberá confirmar su correo electrónico para activar la cuenta:

[![Adición de un usuario](testflight-images/add-new-user.png)](testflight-images/add-new-user.png#lightbox)

Si vuelve a **Mis aplicaciones > Versión preliminar > Evaluadores internos**, podrá ver a los usuarios que se han agregado para las pruebas beta internas de TestFlight:

[![Lista de usuarios agregados a las pruebas beta internas de TestFlight](testflight-images/select-users.png)](testflight-images/select-users.png#lightbox)

Puede invitar a estos evaluadores; para ello, seleccione su nombre y haga clic en el botón **Invitar**. Recibirán un correo electrónico con una invitación para probar la aplicación.

Puede ver el estado de su invitación en la columna de estado de la página Evaluadores internos:

[![Estado de la invitación](testflight-images/status-added.png)](testflight-images/status-added.png#lightbox)

### <a name="external-testers"></a>Evaluadores externos

Antes de invitar a los evaluadores externos a realizar pruebas beta de la aplicación se debe llevar a cabo una revisión de aplicación beta y, por tanto, debe ajustarse a las [Directrices de revisión del App Store](https://developer.apple.com/app-store/review/guidelines/).

Para enviar la aplicación para que se revise, haga clic en el texto **Enviar para revisión de aplicación beta** situado junto a la compilación, como se muestra en la imagen siguiente:

[![Envío para revisión de aplicación beta](testflight-images/beta-app-review.png)](testflight-images/beta-app-review.png#lightbox)

Para que la aplicación pase la revisión, debe escribir todos los metadatos necesarios en la página Información de la versión beta de TestFlight.

Ahora puede empezar a preparar invitaciones y agregar a hasta 2000 evaluadores externos a través de la pestaña Evaluadores externos; para ello, escriba su correo electrónico, nombre y apellido, como se muestra en la captura de pantalla siguiente. El correo electrónico que escriba no debe ser su ID de Apple; es solo el correo electrónico en que recibirán la invitación.

[![Invitación a los evaluadores](testflight-images/add-external.png)](testflight-images/add-external.png#lightbox)

Si tiene un gran número de evaluadores externos, puede utilizar el vínculo **Importar archivo** para importar un archivo `CSV` con el siguiente formato por línea:

```
first name, last name, email address
```

También puede agregar a evaluadores externos a diferentes grupos para que los evaluadores estén bien organizados.

Una vez que haya escrito los detalles de los evaluadores externos, haga clic en **Agregar** y confirme que cuenta con el consentimiento de los usuarios para invitarlos:

[![Confirmación de que cuenta con el consentimiento de los usuarios para invitarlos](testflight-images/confirm-consent.png)](testflight-images/confirm-consent.png#lightbox)

Solo después de realizar una revisión de aplicación beta correcta podrá enviar invitaciones a los evaluadores externos. En este punto, el texto **Externo** de la página de la compilación cambiará a **Enviar invitaciones**. Haga clic en dicho texto para enviar invitaciones a todos los evaluadores que ya ha agregado.

Si se la aplicación se ha rechazado, debe corregir los problemas que se muestran en el **Centro de resolución** y volver a enviar el archivo binario actualizado completo para que se revise.

## <a name="as-a-beta-tester"></a>Como evaluador beta

Cuando invite al evaluador, recibirá un correo electrónico similar al que se muestra en la captura de pantalla siguiente:

[![Correo electrónico de invitación de ejemplo](testflight-images/tester-email.png)](testflight-images/tester-email.png#lightbox)

Una vez que el evaluador haga clic en el botón **Abrir en TestFlight**, la aplicación se abrirá en la aplicación TestFlight o, si todavía no se ha descargado, lo dirigirá al App Store para que pueda descargarla.

Una vez que la aplicación se abre en TestFlight se mostrarán los detalles de lo que se va a comprobar y se solicitará al evaluador que instale la aplicación en su dispositivo iOS 8.0 (o una versión posterior):

[![TestFlight mostrará detalles de lo que se va a comprobar](testflight-images/install-app.png)](testflight-images/install-app.png#lightbox)

Las compilaciones de prueba se indicarán en la pantalla de inicio del dispositivo mediante un punto naranja que precede al nombre de la aplicación.

Los evaluadores pueden enviar comentarios a través de la aplicación TestFlight, y usted transmitirá esta información en la dirección de correo electrónico proporcionada en los metadatos.

### <a name="beta-testing-complete"></a>Pruebas beta completadas

Una vez completada la fase de pruebas beta, ahora puede enviar la aplicación para que Apple la revise en el App Store. Este proceso se lleva a cabo de una manera muy sencilla en iTunes Connect; para ello, hay que hacer clic en el botón **Enviar para revisión**, como se muestra a continuación:

[![Clic en el botón Enviar para revisión](testflight-images/submit-for-review.png)](testflight-images/submit-for-review.png#lightbox)

## <a name="summary"></a>Resumen

En este artículo examinamos cómo utilizar las pruebas beta con TestFlight de Apple a través de iTunes Connect. Hemos revisado cómo cargar una compilación nueva en iTunes Connect y cómo invitar a evaluadores beta internos y externos a usar nuestra aplicación.

## <a name="related-links"></a>Vínculos relacionados

- [Crear un registro de iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Publicación en App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Aprovisionamiento de una aplicación para su distribución en el App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#provisioning)
- [Uso de la versión beta de TestFlight de Apple](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/BetaTestingTheApp.html#//apple_ref/doc/uid/TP40011225-CH35-SW2)
