---
title: Publicación en Google Play
ms.prod: xamarin
ms.assetid: FB1CC234-3554-8566-48BD-2B9B3A28CC7F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: b976f540fb5b9aa7ea86cabb5b50276e53bed9a6
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119803"
---
# <a name="publishing-to-google-play"></a>Publicación en Google Play

Aunque hay muchos mercados de aplicaciones para distribuirlas, Google Play es posiblemente el mayor almacén y el más visitado del mundo para aplicaciones Android. Google Play proporciona una plataforma única para distribuir, publicitar, vender y analizar las ventas de una aplicación Android.

En esta sección se tratan temas específicos de Google Play, como registrarse para convertirse en editor, recopilar recursos para ayudar a Google Play a promocionar y publicitar la aplicación, las directrices para la clasificación de la aplicación en Google Play y el uso de filtros para restringir la implementación de una aplicación en determinados dispositivos.


## <a name="requirements"></a>Requisitos

Para distribuir una aplicación a través de Google Play, debe crearse una cuenta de desarrollador. Solo debe efectuarse una vez y conlleva una tarifa única de 25 USD.

Todas las aplicaciones deben estar registradas con una clave criptográfica que expirará después del 22 de octubre de 2033.

El tamaño máximo de un APK publicado en Google Play es de 100 MB. Si una aplicación supera ese tamaño, Google Play permitirá la entrega de recursos adicionales mediante *archivos de expansión de APK*. Los archivos de expansión Android permiten que el APK tenga 2 archivos adicionales, cada uno de ellos con un tamaño máximo de 2 GB. Google Play hospedará y distribuirá estos archivos sin costo alguno. Los archivos de expansión se tratarán en otra sección.

Google Play no está disponible en todo el mundo. Es posible que en algunas ubicaciones no se admita la distribución de aplicaciones.



## <a name="becoming-a-publisher"></a>Convertirse en editor

Para publicar aplicaciones en Google Play, es necesario tener una cuenta de editor. Para suscribirse a una cuenta de editor, siga estos pasos:

1. Visite [Google Play Developer Console](https://play.google.com/apps/publish).
1. Escriba la información básica de su identidad de desarrollador.
1. Lea y acepte el contrato de distribución para desarrolladores que le corresponda.
1. Abone la tarifa de registro de 25 USD.
1. Confirme la verificación por correo electrónico.
1. Una vez creada la cuenta, ya es posible publicar aplicaciones mediante Google Play.


Google Play no se admite en todos los países del mundo. Las listas de países más recientes se pueden encontrar en los siguientes vínculos:

1. [Ubicaciones admitidas para el registro de comerciantes y desarrolladores](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=150324) &ndash; Se trata de una lista de todos los países en los que los desarrolladores pueden registrarse como comerciantes y vender aplicaciones de pago.

1. [Ubicaciones admitidas para la distribución de aplicaciones a usuarios de Google Play](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=138294) &ndash; Se trata de una lista de todos los países en los que se pueden distribuir aplicaciones.



### <a name="preparing-promotional-assets"></a>Preparación de los recursos promocionales

Para promocionar y publicitar de forma eficaz una aplicación en Google Play, Google permite a los desarrolladores enviar recursos promocionales, como capturas de pantalla, gráficos y vídeos. Google Play utiliza dichos recursos para publicitar y promocionar la aplicación.



#### <a name="launcher-icons"></a>Iconos del iniciador

Un *icono del iniciador* es un gráfico que representa una aplicación. Cada icono del iniciador debe ser un archivo PNG de 32 bits con un canal alfa en caso de transparencia. Una aplicación debe tener iconos de todas las densidades de pantalla generalizadas, tal como se describe en la siguiente lista:

- **ldpi** (120 ppp) &ndash; 36 x 36 px
- **mdpi** (160 ppp) &ndash; 48 x 48 px
- **hdpi** (240 ppp) &ndash; 72 x 72 px
- **xhdpi** (320 ppp) &ndash; 96 x 96 px


Los iconos del iniciador son lo primero que verá un usuario de aplicaciones de Google Play, por lo que debe prestarse mucha atención para conseguir que los iconos del iniciador sean visualmente atractivos y significativos.

Sugerencias para los iconos de iniciador:

1. **Simples y ordenados**&ndash; Los iconos del iniciador deben mostrarse de forma simple y ordenada. Esto significa que hay que excluir el nombre de la aplicación del icono. Los iconos más sencillos serán más fáciles de recordar y, además, serán más fáciles distinguir en tamaños más pequeños.

1. **Iconos que no sean finos**: los iconos demasiado finos no destacan bien en todos los fondos.

1. **Uso del canal alfa**: los iconos deben usar el canal alfa y no deben ser imágenes enmarcadas por completo.



#### <a name="high-resolution-application-icons"></a>Iconos de aplicaciones de alta resolución

Las aplicaciones de Google Play requieren una versión de alta fidelidad del icono de la aplicación. Solo se utiliza en Google Play y no reemplaza el icono del iniciador de la aplicación. Las especificaciones de los iconos de alta resolución son los siguientes:

1. PNG de 32 bits con un canal alfa
1. 512 x 512 píxeles
1. Tamaño máximo de 1024 KB

[Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/) es una herramienta útil para la creación de iconos adecuados para el iniciador y las aplicaciones de alta resolución.



#### <a name="screen-shots"></a>Capturas de pantalla

Google Play requiere un mínimo de dos y un máximo de ocho capturas de pantalla para una aplicación. Se muestran en la página de detalles de la aplicación en Google Play.

Las especificaciones para las capturas de pantalla son las siguientes:

1. PNG o JPG 24 bits sin ningún canal alfa
1. 320 de ancho x 480 de alto, 480 de ancho x 800 de alto o 480 de ancho x 854 de alto. Las imágenes con paisajes se recortarán.



#### <a name="promotional-graphic"></a>Gráfico promocional

Se trata de una imagen opcional utilizada por Google Play:

1. Es un archivo PNG o JPG de 24 bits y de 180 de ancho x 120 de alto sin ningún canal alfa.
1. No incluye bordes.



#### <a name="feature-graphic"></a>Gráfico de características

Se utiliza en la sección de características de Google Play. Este gráfico puede aparecer solo, sin un icono de aplicación.

1. Es un archivo PNG o JPG de 1024 de ancho x 500 de alto sin ningún canal alfa y sin transparencia.
1. Todo el contenido importante debe estar dentro de un marco de 924 x 500. Para fines estilísticos, se pueden recortar los píxeles que estén fuera de este marco.
1. También puede reducirse: utilice texto grande y simplifique los gráficos.



#### <a name="video-link"></a>Vínculo de vídeo

Se trata de una dirección URL de un vídeo de YouTube que muestra la aplicación. El vídeo debe durar entre 30 segundos y 2 minutos, y debe mostrar las mejores partes de la aplicación.



### <a name="publishing-to-google-play"></a>Publicación en Google Play

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin Android 7.0 introduce un flujo de trabajo integrado para publicar aplicaciones en Google Play desde Visual Studio. Si utiliza una versión de Xamarin Android anterior a la 7.0, debe cargar manualmente el APK mediante Google Play Developer Console. Además, debe tener al menos un APK que ya esté cargado para poder utilizar el flujo de trabajo integrado. Si todavía no ha cargado su primer APK, debe cargarlo manualmente. Para obtener más información, consulte [Manually Uploading the APK (Carga manual del APK)](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md).

En [Creating a New Certificate (Crear un certificado)](~/android/deploy-test/signing/index.md#newcert) se explica cómo crear un nuevo certificado para firmar aplicaciones Android. El siguiente paso es publicar una aplicación firmada en Google Play:

1. Inicie sesión en su cuenta de desarrollador de Google Play para crear un nuevo proyecto que esté vinculado a ella.
2. Cree un **Cliente de OAuth** que autentique su aplicación.
3. Escriba el id. de cliente y el secreto del cliente resultantes en Visual Studio.
4. Registre la cuenta con Visual Studio.
5. Firme la aplicación con el certificado.
6. Publique la aplicación firmada en Google Play.

En [Archive for Publishing](~/android/deploy-test/release-prep/index.md#archive) (Archivo para publicar), el cuadro de diálogo **Canal de distribución** ofrece dos opciones para la distribución: **Ad Hoc** y **Google Play**. Si aparece el cuadro de diálogo **Signing Identity (Identidad de firma)** , haga clic en **Atrás** para volver al cuadro de diálogo **Distribution Channel (Canal de distribución)** . Seleccione **Google Play** y haga clic en **Siguiente**:

[![Cuadro de diálogo Canal de distribución](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

En el cuadro de diálogo **Identidad de firma**, seleccione la identidad que se ha creado en [Creating a New Certificate (Crear un certificado)](~/android/deploy-test/signing/index.md#newcert) y haga clic en **Continuar**:

[![Cuadro de diálogo Identidad de firma](images/vs/02-select-identity-sml.png)](images/vs/02-select-identity.png#lightbox)

En el cuadro de diálogo **Cuentas de Google Play**, haga clic en el botón **+** para agregar una nueva cuenta de Google Play:

[![Cuadro de diálogo Cuentas de Google Play](images/vs/03-google-play-accounts-sml.png)](images/vs/03-google-play-accounts.png#lightbox)

En el cuadro de diálogo **Register Google API Access (Registrar acceso de API de Google)** debe proporcionar el _id. de cliente_ y el _secreto de cliente_ que proporcionan acceso a la API a su cuenta de desarrollador Google Play:

[![Cuadro de diálogo Registrar acceso de API de Google](images/vs/04-register-google-api-access-sml.png)](images/vs/04-register-google-api-access.png#lightbox)

En la siguiente sección se explica cómo crear un nuevo proyecto de API de Google y generar el _id. de cliente_ y el _secreto de cliente_ que se necesitan.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Visual Studio para Mac cuenta con un flujo de trabajo integrado para publicar aplicaciones en Google Play.

En [Creación de un certificado](~/android/deploy-test/signing/index.md#newcert) se describe la creación de un certificado para firmar aplicaciones Android. En los siguientes pasos se describe cómo publicar una aplicación de Xamarin.Android en Google Play:

1. Inicie sesión en su cuenta de desarrollador de Google Play para crear un nuevo proyecto que esté vinculado a ella.
2. Cree un _Cliente de OAuth_ que autentique su aplicación.
3. Escriba el _identificador de cliente_ y el _secreto del cliente_ resultantes en Visual Studio para Mac.
4. Registre la cuenta con Visual Studio para Mac.
5. Firme la aplicación con el certificado.
6. Publique la aplicación firmada en Google Play.

En [Archive for Publishing (Archivo para publicar)](~/android/deploy-test/release-prep/index.md#archive), el cuadro de diálogo **Sign and Distribute… (Firmar y distribuir…)** ofrece dos opciones de distribución. Seleccione **Google Play** y haga clic en **Siguiente**:

[![Cuadro de diálogo Select Android Distribution (Seleccionar distribución de Android)](images/xs/01-select-google-play-sml.png)](images/xs/01-select-google-play.png#lightbox)

En el cuadro de diálogo **Google Play API Account (Cuenta de la API de Google Play)** debe proporcionar el _id. de cliente_ y el _secreto de cliente_ que proporcionan acceso a la API a su cuenta de desarrollador de Google Play:

[![Cuadro de diálogo Cuenta de API de Google Play](images/xs/02-google-play-api-account-sml.png)](images/xs/02-google-play-api-account.png#lightbox)

En la siguiente sección se explica cómo crear un nuevo proyecto de API de Google y generar el _id. de cliente_ y el _secreto de cliente_ que se necesitan.

-----


#### <a name="create-a-google-api-project"></a>Creación de un proyecto de API de Google

En primer lugar, inicie sesión en su [cuenta de desarrollador de Google Play](https://play.google.com/apps/publish).
Si no dispone de una cuenta de desarrollador de Google Play, consulte [Primeros pasos con la publicación](https://developer.android.com/distribute/googleplay/start.html).
Además, en la [Introducción](https://developers.google.com/android-publisher/getting_started) la API para desarrolladores de Google Play se explica cómo utilizar la API. Después de iniciar sesión en Google Play Developer Console, haga clic en **Configuración**:

[![Icono Settings (Configuración)](images/01-google-play-developer-console-sml.png)](images/01-google-play-developer-console.png#lightbox)

En el la página **CONFIGURACIÓN**, seleccione **Acceso de API** y haga clic en el botón **Crear nuevo proyecto**:

[![Botón Create new project (Crear nuevo proyecto)](images/02-create-new-project-sml.png)](images/02-create-new-project.png#lightbox)

Tras un minuto aproximadamente, el nuevo proyecto de API se genera de forma automática y se vincula a su cuenta de Google Play Developer Console.

El siguiente paso es crear un cliente de OAuth para la aplicación, si aún no se ha creado uno. Cuando los usuarios solicitan acceso a sus datos privados mediante la aplicación, el id. de cliente OAuth se usa para autenticar su aplicación.
Haga clic en **Crear cliente de OAuth** para crear un nuevo cliente de OAuth:

[![Botón Create OAuth Client (Crear cliente de OAuth)](images/03-create-oauth-client-sml.png)](images/03-create-oauth-client.png#lightbox)

Después de unos segundos, se genera un nuevo id. de cliente. Haga clic en **Ver en Google Developers Console** para ver su nuevo id. de cliente en Google Developers Console:

[![Aparece el identificador de cliente](images/04-generated-client-id-sml.png)](images/04-generated-client-id.png#lightbox)

El id. de cliente se muestra con su fecha de creación y el nombre. Haga clic en el icono **Editar cliente de OAuth** para ver el secreto de cliente de la aplicación:

[![Consulta de las credenciales de la aplicación](images/05-google-developer-console-sml.png)](images/05-google-developer-console.png#lightbox)

El nombre predeterminado del cliente de OAuth es *Desarrollador de Google Play Android*. Se puede cambiar por nombre de la aplicación de Xamarin.Android o por cualquier nombre adecuado. En este ejemplo, se cambia el nombre de cliente OAuth por el nombre de la aplicación, **MyApp**:

[![Consulta del identificador y el secreto de cliente](images/06-client-id-and-secret-sml.png)](images/06-client-id-and-secret.png#lightbox)

Haga clic en **Guardar** para guardar los cambios. De este modo volverá a la página **Credenciales**, donde puede descargar las credenciales haciendo clic en el icono **Descargar JSON**:

[![Icono Download JSON (Descargar JSON)](images/07-download-json-sml.png)](images/07-download-json.png#lightbox)

Este archivo JSON contiene el id. de cliente y el secreto del cliente que puede cortar y pegar en el cuadro de diálogo **Sign and Distribute (Firmar y distribuir)** en el paso siguiente.


#### <a name="register-google-api-access"></a>Registro del acceso de la API de Google

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Utilice el identificador de cliente y el secreto de cliente para completar el cuadro de diálogo **Cuenta de API de Google Play** de Visual Studio para Mac. Es posible proporcionar una descripción a la cuenta, lo que permite registrar más de una cuenta de Google Play y cargar futuros APK en diferentes cuentas de Google Play. Copie el id. de cliente y el secreto de cliente en este cuadro de diálogo y haga clic en **Registrar**:

[![Cuadro de diálogo Registrar acceso de API de Google](images/vs/05-enter-client-id-and-secret-sml.png)](images/vs/05-enter-client-id-and-secret.png#lightbox)

Se abrirá un explorador web y le solicitará que, si aún no lo ha hecho, inicie sesión en su cuenta de desarrollador de Android de Google Play. Después de iniciar sesión, se mostrará el mensaje siguiente en el explorador web.
Haga clic en **Permitir** para autorizar la aplicación:

[![Cuadro de diálogo de autorización de la aplicación](images/vs/06-authorize-app-sml.png)](images/vs/06-authorize-app.png#lightbox)

#### <a name="publish"></a>Publicar

Después de hacer clic en **Permitir**, el explorador indica _Received verification code. Closing... (Código de verificación recibido. Cerrando...)_ y la aplicación se agrega a la lista de cuentas de Google Play en Visual Studio. En el cuadro de diálogo **Cuentas de Google Play**, haga clic en **Continuar**:

[![Cuenta agregada a las cuentas de Google Play](images/vs/07-account-added-sml.png)](images/vs/07-account-added.png#lightbox)

Después, se muestra el cuadro de diálogo **Pista de Google Play**. Google Play ofrece cuatro pistas posibles para cargar la aplicación:

- **Alfa** &ndash; Se usa para cargar una versión muy temprana de la aplicación en una lista reducida de evaluadores.
- **Beta** &ndash; Se usa para cargar una versión temprana de la aplicación en una lista amplia de evaluadores.
- **Lanzamiento** &ndash; Permite a un porcentaje de los usuarios recibir una versión actualizada de la aplicación. Así, puede aumentar lentamente el porcentaje de aproximadamente el 10 % de los usuarios hasta el 100 % mientras corrige errores.
- **Producción** &ndash; Seleccione esta pista cuando la aplicación esté lista para su distribución completa desde Google Play Store.

Elija qué pista de Google Play se utilizará para cargar la aplicación y haga clic en **Cargar**. Si selecciona **Rollout (Implementación)** , asegúrese de especificar un valor porcentual:

[![Selección de Alfa, Beta, Lanzamiento o Producción](images/vs/08-google-play-track-sml.png)](images/vs/08-google-play-track.png#lightbox)

Para obtener más información sobre las pruebas y las implementaciones graduales de Google Play, consulte [Configurar pruebas alpha o beta](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en).

A continuación, se muestra un cuadro de diálogo para especificar la contraseña del certificado de firma.
Escriba la contraseña y haga clic en **Aceptar**:

[![Cuadro de diálogo Contraseña de firma](images/vs/09-certificate-password-sml.png)](images/vs/09-certificate-password.png#lightbox)

El **administrador de archivos** muestra el progreso de la carga:

[![Progreso de carga del APK](images/vs/10-uploading-apk-sml.png)](images/vs/10-uploading-apk.png#lightbox)

Cuando finaliza la carga, el estado se muestra en la esquina inferior izquierda de Visual Studio:

[![Mensaje de publicación del proyecto finalizada](images/vs/11-published-sml.png)](images/vs/11-published.png#lightbox)


### <a name="troubleshooting"></a>Solución de problemas

Tenga en cuenta que se debe haber enviado antes un APK a Google Play Store para que **Publish to Google Play (Publicación en Google Play)** funcione. Si aún no se ha cargado un APK, el Asistente para publicación mostrará el siguiente error en la panel **Errores**:

[![Debe cargar manualmente el primer APK de esta aplicación](images/vs/12-upload-error-sml.png)](images/vs/12-upload-error.png#lightbox)

Si se produce este error, cargue un APK manualmente, por ejemplo, una compilación ad hoc, mediante Google Play Developer Console y utilice el cuadro de diálogo **Distribution Channel (Canal de distribución)** para posteriores actualizaciones del APK.  Para obtener más información, consulte [Manually Uploading the APK (Carga manual del APK)](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md). El código de la versión del APK debe cambiar con cada carga. De lo contrario, se producirá el siguiente error:

[![Ya se actualizó un APK con el código de versión (1)](images/vs/13-version-code-error-sml.png)](images/vs/13-version-code-error.png#lightbox)

Para resolver este error, vuelva a compilar la aplicación con un número de versión diferente y reenvíela a Google Play mediante el cuadro de diálogo **Distribution Channel (Canal de distribución)** .

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Utilice el identificador de cliente y el secreto de cliente para completar el cuadro de diálogo **Cuenta de API de Google Play** de Visual Studio para Mac. Es posible proporcionar una descripción a la cuenta, lo que permite registrar más de una cuenta de Google Play y cargar futuros APK en diferentes cuentas de Google Play. Copie el id. de cliente y el secreto de cliente en este cuadro de diálogo y haga clic en **Registrar**:

[![Cuadro de diálogo de autorización de acceso](images/xs/10-register-sml.png)](images/xs/10-register.png#lightbox)

Si se aceptan el id. de cliente y el secreto de cliente, se mostrará el mensaje **Registro aprobado**. Haga clic en **Siguiente**:

[![Mensaje de registro correcto](images/xs/11-registration-successful-sml.png)](images/xs/11-registration-successful.png#lightbox)

En el cuadro de diálogo **Google Play Account (Cuenta de Google Play)** , seleccione una cuenta de Google y una pista para cargar la aplicación:

[![Cuadro de diálogo para elegir una cuenta de Google](images/xs/12-choose-google-account-sml.png)](images/xs/12-choose-google-account.png#lightbox)

Google Play ofrece cuatro pistas posibles para cargar la aplicación:

- **Alfa** &ndash; Se usa para cargar una versión muy temprana de la aplicación en una lista reducida de evaluadores.

- **Beta** &ndash; Se usa para cargar una versión temprana de la aplicación en una lista amplia de evaluadores.

- **Lanzamiento** &ndash; Permite a un porcentaje de los usuarios recibir una versión actualizada de la aplicación. Así, puede aumentar lentamente el porcentaje de aproximadamente el 10 % de los usuarios hasta el 100 % mientras corrige errores.

- **Producción** &ndash; Seleccione esta pista cuando la aplicación esté lista para su distribución completa desde Google Play Store.

Para obtener más información sobre las pruebas y las implementaciones graduales de Google Play, consulte [Configurar pruebas alpha o beta](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en).

A continuación, elija una identidad de firma para firmar la aplicación.
Seleccione **Usar clave existente** para usar una identidad de firma existente. De lo contrario, consulte la guía [Creating a New Certificate (Crear un certificado nuevo)](~/android/deploy-test/signing/index.md#newcert) para obtener información sobre cómo crear una clave. Después de haber seleccionado un certificado para firmar la aplicación, haga clic en **Siguiente**:

[![Cuadro de diálogo Identidad de firma para Android](images/xs/13-android-signing-identity-sml.png)](images/xs/13-android-signing-identity.png#lightbox)

Llegados a este punto, la aplicación puede cargarse en Google Play. En el cuadro de diálogo **Publicar en Google Play** se resume la información de la aplicación. Haga clic en **Publicar** para publicar la aplicación en Google Play:

[![Cuadro de diálogo Publicar en Google Play](images/xs/14-publish-to-google-play-sml.png)](images/xs/14-publish-to-google-play.png#lightbox)

Tenga en cuenta que se debe haber enviado antes un APK a Google Play Store para que **Publish to Google Play (Publicación en Google Play)** funcione. Si no se carga un APK, puede producirse el error siguiente:

> _Google Play solicita que cargue manualmente el primer APK de esta aplicación. Puede usar un APK ad hoc para ello._

o

> _No application was found for the given package name. [404] (No se ha encontrado ninguna aplicación con el nombre de paquete especificado. [404])_

Para resolver este error, cargue un APK manualmente, por ejemplo, una compilación ad hoc, mediante Google Play Developer Console y utilice el cuadro de diálogo **Publish to Google Play (Publicación en Google Play)** para posteriores actualizaciones del APK. Para obtener más información sobre cómo cargar manualmente un APK, consulte [Manually Uploading the APK (Cargar manualmente el APK)](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md).

-----
