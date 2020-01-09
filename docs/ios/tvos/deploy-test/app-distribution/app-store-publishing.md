---
title: Publicación en Apple TV App Store
description: En este documento se describe cómo publicar una aplicación en la App Store de Apple TV. En él se describe cómo configurar, aprovisionar, compilar y enviar una aplicación de tvOS compilada con Xamarin.
ms.prod: xamarin
ms.assetid: 52448C93-DC19-40FA-BF8C-608AE680FF49
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: a3d0962eb8253ca72b6afce77036e02a7aa59b94
ms.sourcegitcommit: 6f09bc2b760e76a61a854f55d6a87c4f421ac6c8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2020
ms.locfileid: "75607898"
---
# <a name="publishing-to-the-apple-tv-app-store"></a>Publicación en Apple TV App Store

Para distribuir las aplicaciones a todos los dispositivos Apple TV, Apple requiere que las aplicaciones se publiquen a través de *Apple TV App Store*, lo que hace que la aplicación almacene la ubicación de compra única para las aplicaciones de tvOS. Los desarrolladores de muchos tipos de aplicaciones pueden capitalizarse en el éxito masivo de este único punto de distribución. Apple TV App Store es una solución llave en mano, que ofrece a los desarrolladores de aplicaciones los sistemas de pago y distribución.

El proceso de enviar una aplicación a la tienda de aplicaciones de Apple TV implica:

1. Crear un *Id. de aplicación* y seleccionar *Derechos*.
2. Crear un *perfil de aprovisionamiento de distribución*.
3. Usar este perfil para compilar la aplicación.
4. Envío de la aplicación a través de *iTunes Connect*.

En este artículo se tratarán todos los pasos necesarios para aprovisionar, compilar y enviar una aplicación para la distribución de App Store de Apple TV.

<a name="Before_you_Submit" />

## <a name="before-you-submit-an-application"></a>Antes de enviar una aplicación

Después de enviar una aplicación para su publicación en el App Store de Apple TV, este pasa por un proceso de revisión de Apple para asegurarse de que cumple con las directrices de Apple para la calidad y el contenido. Si la aplicación no cumple estas directrices, Apple la rechazará, por lo que se deberá abordar la no conformidad mencionada por Apple y, después, volver a enviarla.
En vista de ello, y para tener más posibilidades de superar la revisión de Apple, familiarícese con estas directrices e intente adaptar su aplicación a ellas. Las instrucciones de Apple están disponibles en las [directrices de revisión del App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html) y preparan [el envío de la aplicación para el nuevo Apple TV](https://developer.apple.com/tvos/submit/).

Aquí se muestran un par de cosas a tener en cuenta a la hora de enviar una aplicación:

1. Asegúrese de que la descripción de la aplicación coincide con la funcionalidad incluida en la aplicación.
2. Compruebe que la aplicación no se bloquea con un uso normal. Esto incluye el uso en cada dispositivo Apple TV que admita.

Apple también mantiene una lista de sugerencias de envío de la tienda de aplicaciones de Apple TV. Puede leerlas en [Distribución en el App Store](https://developer.apple.com/appstore/resources/submission/tips.html).

<a name="Configuring_your_Application_in_iTunes_Connect" />

## <a name="configuring-your-application-in-itunes-connect"></a>Configuración de la aplicación en iTunes Connect

[iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) es un conjunto de herramientas basadas en web para, entre otras cosas, la administración de las aplicaciones de tvOS en el App Store de Apple TV. La aplicación de Xamarin. tvOS tendrá que configurarse y configurarse correctamente en iTunes Connect para que se pueda enviar a Apple para su revisión y, en última instancia, publicarse para su venta o como una aplicación gratuita en el App Store de Apple TV.

haga lo siguiente:

1. Compruebe que los contratos adecuados se encuentran en vigor y actualizados en la sección **Contratos, impuestos y banca** de iTunes Connect para publicar una aplicación de iOS como gratuita o para su venta.
2. Cree un nuevo **registro de iTunes Connect** para la aplicación y especifique su **nombre para mostrar** (como se muestra en la App Store de Apple TV).
3. Seleccione un **Precio de venta** o especifique que la aplicación se publicará de manera gratuita.
4. Proporcione un **icono de App Store** (icono grande) y capturas de pantallas de la aplicación en acción, en los dispositivos Apple TV que admite. Para más información, consulte la guía [trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) .
5. Proporcione una **Descripción** clara y concisa de la aplicación, incluidas sus características y beneficios para el usuario final.
6. Proporcione **categorías**, **subcategorías**y **palabras clave** para ayudar al usuario a encontrar la aplicación en la App Store de Apple TV.
7. Proporcione las direcciones URL de **Contacto** y **Soporte técnico** a su sitio web requeridas por Apple.
8. Establezca la **clasificación**de la aplicación, que es usada por el control parental en la tienda de aplicaciones de Apple TV.
9. Configure tecnologías opcionales del App Store, como **Game Center** y **Compra desde la aplicación**.

Para obtener más información, consulte [la documentación de configuración de la aplicación tvOS en iTunes Connect](~/ios/tvos/deploy-test/app-distribution/itunes-connect.md) .

<a name="Preparing_for_App_Store_Distribution" />

## <a name="preparing-for-app-store-distribution"></a>Preparación de la distribución a través del App Store

Para publicar una aplicación en la App Store de Apple TV, primero debe compilarla para su distribución, lo que implica muchos pasos. En las secciones siguientes se incluye todo lo necesario para preparar una aplicación de Xamarin. tvOS para su publicación para que se pueda compilar y enviar a la tienda de aplicaciones de Apple TV para su revisión y publicación.

<a name="Provisioning_for_Application_Services" />

### <a name="provisioning-for-application-services"></a>Aprovisionamiento de servicios de aplicación

Apple proporciona una selección de Servicios de aplicación especiales, también denominados derechos, que pueden activarse para la aplicación de tvOS cuando se crea un identificador único para ella. Tanto si usa derechos personalizados como si no, tendrá que crear un identificador único para la aplicación Xamarin. tvOS antes de que se pueda publicar en el App Store de Apple TV.

La creación de un id. de aplicación y la selección opcional de derechos incluye los siguientes pasos mediante el portal de aprovisionamiento de iOS basado en web de Apple:

1. Seleccione **aprovisionamiento** > **desarrollo**.
2. Haga clic en el botón **+** y proporcione un **Nombre** y un **Id. de agrupación** para la nueva aplicación.
3. Desplácese a la parte inferior de la pantalla y seleccione los **App Services** que necesitará la aplicación Xamarin. tvOS.
4. Haga clic en el botón **Continuar** y siga las instrucciones en pantalla para crear el nuevo id. de aplicación.

Además de seleccionar y configurar los Servicios de aplicación necesarios al definir el identificador de la aplicación, también debe configurar el identificador de la aplicación y los derechos en el proyecto de Xamarin. tvOS mediante la edición de los archivos `Info.plist` y `Entitlements.plist`.

Haga lo siguiente en Visual Studio para Mac:

1. Haga doble clic en el archivo `Info.plist` en el **Explorador de soluciones** para abrirlo para su edición.
2. En la sección destino de la **aplicación tvOS** , rellene un nombre para la aplicación y escriba el **identificador de paquete** que creó al definir el identificador de la aplicación.
3. Guarde los cambios en el archivo `Info.plist`.
4. Haga doble clic en el archivo `Entitlements.plist` en el **Explorador de soluciones** para abrirlo para su edición.
5. Seleccione y configure los derechos necesarios para la aplicación Xamarin. tvOS para que coincidan con el programa de instalación que realizó anteriormente cuando definió el identificador de la aplicación.
6. Guarde los cambios en el archivo `Entitlements.plist`.

Para obtener instrucciones detalladas, vea nuestra documentación sobre [Aprovisionamiento de servicios de aplicación](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioning-for-application-services). Aunque este documento se ha escrito para iOS, se usan los mismos pasos para aprovisionar una aplicación Xamarin. tvOS.

<a name="Setting_the_Apps_Icons_and_Launch_Screens" />

### <a name="setting-the-apps-icons-launch-image-and-top-shelf-image"></a>Establecer los iconos de aplicaciones, la imagen de inicio y la imagen de estante superior

Para que Apple acepte una aplicación de tvOS para su inclusión en la tienda de aplicaciones de Apple TV, requiere iconos, Inicio y imágenes de estante superior para todos los dispositivos de Apple TV en los que se ejecutará. Necesitará agregar los recursos de imagen necesarios que se compilarán en un archivo `Assets.car` y que se incluirán en el paquete de la aplicación de Xamarin. tvOS antes de cargarse en iTunes Connect.

Para obtener instrucciones detalladas, consulte la documentación sobre [Cómo trabajar con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md) .

<a name="Creating_and_Installing_a_Distribution_Profile" />

### <a name="creating-and-installing-a-distribution-profile"></a>Creación e instalación de un perfil de distribución

tvOS usa *perfiles de aprovisionamiento* para controlar el modo en que se puede implementar una compilación de aplicación concreta. Estos son archivos que contienen información sobre el certificado que se ha usado para firmar una aplicación, el *Id. de aplicación* y el lugar en el que puede instalarse la aplicación. Para el desarrollo y la distribución ad hoc, el perfil de aprovisionamiento también incluye la lista de dispositivos permitidos en los que puede implementar la aplicación. Sin embargo, para la distribución de la tienda de aplicaciones de Apple TV, solo se incluyen la información del certificado y del identificador de la aplicación, ya que el único mecanismo para la distribución pública se realiza a través de Apple TV App Store.

El aprovisionamiento incluye los siguientes pasos con el portal de aprovisionamiento de iOS basado en web de Apple:

1. Seleccione **Aprovisionamiento** > **Distribución**.
2. Haga clic en el botón **+** y seleccione el tipo de Perfil de distribución que quiere crear como **Apple TV App Store**.
3. Seleccione **Id. de aplicación** en la lista desplegable para el que quiere crear un perfil de distribución.
4. Seleccione certificado necesario para firmar la aplicación.
5. Escriba un **Nombre** para el nuevo **Perfil de distribución** y genere el perfil.
6. Actualice la lista de perfiles disponibles en Xcode.
7. Seleccione el perfil de aprovisionamiento de distribución en Visual Studio para la _configuración de compilación_de **App Store** .

Para obtener instrucciones detalladas, vea las secciones [Crear un perfil de distribución](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#creatingprofile) y [Seleccionar un perfil de distribución en un proyecto de Xamarin.iOS](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#selectprofile). De nuevo, ambos documentos son específicos de iOS, pero se usa la misma técnica para las aplicaciones de tvOS.

<a name="Setting_the_Build_Configuration_for_your_Application" />

### <a name="setting-the-build-configuration-for-your-application"></a>Establecer la configuración de compilación de la aplicación

De forma predeterminada, cuando se crea una nueva aplicación Xamarin. tvOS, se crean automáticamente _configuraciones de compilación_ para la implementación de **depuración** y **lanzamiento** . Antes de realizar la compilación final de la aplicación que va a enviar a Apple, hay algunas modificaciones que debe realizar en la configuración de la **versión** base.

haga lo siguiente:

1. Haga clic con el botón derecho en el **nombre del proyecto** en el **Explorador de soluciones** y seleccione **Opciones** para abrirlos para su edición.
2. Si el destino es una versión específica de tvOS, selecciónela en **TvOS Build** > versión del **SDK de iOS**. Para la versión preliminar de la compatibilidad con tvOS, deje este valor establecido en **predeterminado**.
3. La vinculación reduce el tamaño total del distribuible de la aplicación mediante la eliminación de métodos, propiedades, clases, etc. no usados y, en la mayoría de los casos, debe dejarse en el valor predeterminado del **SDK de Link Framework**. En algunas situaciones, como cuando se usan algunas bibliotecas de terceros determinadas, puede verse forzado a establecer este valor en **Don't link (No vincular)** para impedir que se quite el elemento necesario.
4. Para enviar una aplicación Xamarin. tvOS, debe usar el compilador de optimización de LLVM. Asegúrese de que la casilla **usar el compilador de optimización de LLVM** está activada en la configuración de **lanzamiento** .
5. Apple también requiere que las aplicaciones de tvOS usen Bitcode. De nuevo en la configuración de **versión** , agregue `--bitcode=asmonly` al cuadro **argumentos adicionales de Mtouch** .
6. La casilla **optimizar archivos de imagen PNG para iOS** debe estar activada, ya que esto le ayudará a reducir aún más el tamaño de entrega de la aplicación.
7. La depuración *no* debe habilitarse porque hará que la compilación sea innecesariamente más grande.

<a name="Building_and_Submitting_the_Distributable" />

## <a name="building-and-submitting-the-distributable"></a>Compilación y envío del distribuible

Con la aplicación Xamarin. tvOS configurada correctamente, ya está listo para realizar la compilación de distribución final que va a enviar a Apple para su revisión y publicación.

#### <a name="build-your-archive"></a>Compilar el archivo

1. Seleccione la configuración **Versión | Dispositivo** en Visual Studio para Mac:

    ![](app-store-publishing-images/buildxs01new.png "Select the Release configuration")
2. En el menú **Compilación**, seleccione **Archivo para publicar**:

    [![](app-store-publishing-images/buildxs02new.png "Select Archive for Publishing")](app-store-publishing-images/buildxs02new.png#lightbox)
3. Una vez creado el archivo, se mostrará la vista **Archives (Archivos)** :

    [![](app-store-publishing-images/buildxs03new.png "The Archives view")](app-store-publishing-images/buildxs03new.png#lightbox)

### <a name="sign-and-distribute-your-app"></a>Firmar y distribuir la aplicación

Cada vez que se compila la aplicación para el archivo, se abre automáticamente la *Archives View (Vista de archivos)* , que muestra todos los proyectos archivados, agrupados por solución. De manera predeterminada, esta vista solo muestra la solución abierta actual. Para ver todas las soluciones que tienen archivos, haga clic en la opción **Show all archives (Mostrar todos los archivos)** .

Se recomienda que se mantengan los archivos que se implementan en los clientes (implementaciones App Store o Enterprise), por lo que cualquier información de depuración generada puede considerarse con una fecha posterior.

Para firmar la aplicación y prepararla para la distribución:

1. Seleccione el **signo y distribuir...** , que se muestra a continuación:

    [![](app-store-publishing-images/buildxs04new.png ", Select theSign and Distribute...")](app-store-publishing-images/buildxs04new.png#lightbox)
2. De este modo, se abrirá el asistente para publicación. Seleccione el canal de distribución **App Store** para crear un paquete y abra Application Loader:

    [![](app-store-publishing-images/distribute01.png "Select the App Store distribution channel")](app-store-publishing-images/distribute01.png#lightbox)
3. En la pantalla Perfil de aprovisionamiento, seleccione su identidad de firma y el perfil de aprovisionamiento correspondiente, o vuelva a firmar con otra identidad:

    [![](app-store-publishing-images/distribute02.png "Select the signing identity and corresponding provisioning profile")](app-store-publishing-images/distribute02.png#lightbox)
4. Compruebe los detalles del paquete y haga clic en **Publicar** para guardar el paquete de `.ipa`:

    [![](app-store-publishing-images/distribute03.png "Verify the details of the package")](app-store-publishing-images/distribute03.png#lightbox)
5. Una vez que `.ipa` se haya guardado, la aplicación está lista para cargarse en iTunes Connect mediante Application Loader:

    [![](app-store-publishing-images/distribute04.png "Uploaded to iTunes Connect via the Application Loader")](app-store-publishing-images/distribute04.png#lightbox)

Una vez que se ha creado y archivado su compilación de distribución, ahora está listo para enviar su aplicación a iTunes Connect.

<a name="Submitting_Your_App_to_Apple" />

## <a name="submitting-your-app-to-apple"></a>Enviar su aplicación a Apple

Una vez que se ha completado la compilación de distribución, está listo para enviar su aplicación de iOS a Apple para su revisión y publicación en el App Store.

El flujo de trabajo de archivo en Visual Studio para Mac abrirá Application Loader automáticamente, una vez que haya guardado el `.ipa`:

1. Seleccione *Deliver Your App* (Entregar la aplicación) y haga clic en el botón *Elegir*:

    [![](app-store-publishing-images/publishvs01.png "Select Deliver Your App")](app-store-publishing-images/publishvs01.png#lightbox)

2. Seleccione el archivo IPA o ZIP que ha creado anteriormente y haga clic en el botón **Aceptar**.
3. Application Loader validará el archivo:

    [![](app-store-publishing-images/publishvs02.png "The Application Loader validation screen")](app-store-publishing-images/publishvs02.png#lightbox)
4. Haga clic en el botón *Siguiente* y la aplicación se validará en el App Store:

    [![](app-store-publishing-images/publishvs03.png "The application being validated against the App Store")](app-store-publishing-images/publishvs03.png#lightbox)
5. Haga clic en el botón **Enviar** para enviar la aplicación a Apple para su revisión.
6. Application Loader le informará cuando el archivo se haya cargado correctamente.

<a name="iTunes_Connect_Status" />

### <a name="itunes-connect-status"></a>Estado de iTunes Connect

Si vuelve a iniciar sesión en iTunes Connect y selecciona la aplicación en la lista de aplicaciones disponibles, el estado en iTunes Connect debe mostrar ahora que está en **espera de revisión** (puede que lea temporalmente la **carga recibida** mientras se procesa):

[![](app-store-publishing-images/image21.png "The status in iTunes Connect showing Waiting for Review")](app-store-publishing-images/image21.png#lightbox)

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Solucionar problemas

Si tiene problemas para enviar la aplicación Xamarin. tvOS a la tienda de aplicaciones de Apple TV, consulte nuestra guía de [solución de problemas](~/ios/tvos/troubleshooting.md) . Contiene varios problemas conocidos que pueden surgir y cómo resolverlos en Xamarin. tvOS.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se presenta una guía paso a paso para configurar, compilar y enviar una aplicación para la publicación de App Store de Apple TV. Primero, ha tratado los pasos necesarios para crear e instalar un perfil de aprovisionamiento de distribución. A continuación, se describe cómo usar Visual Studio para Mac para crear una compilación de distribución. Por último, le mostramos cómo usar iTunes Connect y la herramienta de archivo de Xcode para enviar una aplicación a la tienda de aplicaciones de Apple TV.

## <a name="related-links"></a>Vínculos relacionados

- [Trabajo con iconos e imágenes](~/ios/tvos/app-fundamentals/icons-images.md)
- [Preparación del envío de la aplicación para el nuevo Apple TV](https://developer.apple.com/tvos/submit/)
- [Sugerencias de envío al App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Rechazos de aplicaciones comunes](https://developer.apple.com/app-store/review/rejections/)
- [Directrices de revisión del App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
