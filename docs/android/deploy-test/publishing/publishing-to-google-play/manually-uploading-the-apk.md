---
title: Carga manual del APK
ms.prod: xamarin
ms.assetid: 1309C251-ABF0-4412-B1F5-200DC8321A9D
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/15/2018
ms.openlocfilehash: b54296da3f1253b77b63363583c34e1548cd6abc
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76724091"
---
# <a name="manually-uploading-the-apk"></a>Carga manual del APK

La primera vez que se envía un APK a Google Play (o si se usa una versión antigua de Xamarin.Android), se debe cargar manualmente el APK mediante [Google Play Developer Console](https://play.google.com/apps/publish).
En esta guía se explican los pasos necesarios para este proceso.

## <a name="google-play-developer-console"></a>Google Play Developer Console

Una vez que se ha compilado el APK y se han preparado los activos promocionales, la aplicación debe cargarse en Google Play. Para ello, inicie sesión en [Google Play Developer Console](https://play.google.com/apps/publish), que se muestra a continuación. Haga clic en el botón **Publish an Android App on Google Play (Publicar una aplicación de Android en Google Play)** para inicializar el proceso de distribución de una aplicación.

[![Google Play Developer Console](manually-uploading-the-apk-images/00-google-play-developer-console-sml.png)](manually-uploading-the-apk-images/00-google-play-developer-console.png#lightbox)

Si ya tiene una aplicación registrada con Google Play, haga clic en el botón **Add new application (Agregar nueva aplicación)** :

[![Botón Add new application (Agregar nueva aplicación)](manually-uploading-the-apk-images/01-existing-app-sml.png)](manually-uploading-the-apk-images/01-existing-app.png#lightbox)

Cuando aparece el cuadro de diálogo **ADD NEW APPLICATION (AGREGAR NUEVA APLICACIÓN)** , escriba el nombre de la aplicación y haga clic en **Upload APK (Cargar APK)** :

[![Botón Upload APK (Cargar APK)](manually-uploading-the-apk-images/02-add-new-application-sml.png)](manually-uploading-the-apk-images/02-add-new-application.png#lightbox)

La pantalla siguiente permite publicar la aplicación para pruebas alfa, pruebas beta o producción. En el ejemplo siguiente, la pestaña **ALPHA TESTING (PRUEBAS ALFA)** está seleccionada. Dado que **MyApp** no usa servicios de licencias, no hay que hacer clic en el botón **Get license key (Obtener clave de licencia)** para este ejemplo. En este caso, se hace clic en el botón **Upload your first APK to Alpha (Cargar el primer APK en alfa)** para publicar en el canal alfa:

[![Botón Upload your first APK to Alpha (Cargar nuevo APK en Alfa)](manually-uploading-the-apk-images/03-upload-to-alpha-sml.png)](manually-uploading-the-apk-images/03-upload-to-alpha.png#lightbox)

Aparece el cuadro de diálogo **UPLOAD NEW APK TO ALPHA (CARGAR NUEVO APK EN ALFA)** . El APK se puede cargar haciendo clic en **Browse files** (Examinar archivos) o arrastrando y soltando el APK:

[![Cuadro de diálogo Upload New APK to Alpha (Cargar nuevo APK en Alfa)](manually-uploading-the-apk-images/04-upload-dialog-sml.png)](manually-uploading-the-apk-images/04-upload-dialog.png#lightbox)

Asegúrese de cargar el APK listo para publicar que se va a distribuir.
El siguiente cuadro de diálogo indica el progreso de la carga del APK:

[![Indicación de progreso de la carga](manually-uploading-the-apk-images/05-upload-progress-sml.png)](manually-uploading-the-apk-images/05-upload-progress.png#lightbox)

Una vez cargado el APK, es posible seleccionar un método de prueba:

[![Cuadro de diálogo para elegir un método de prueba](manually-uploading-the-apk-images/06-select-testing-method-sml.png)](manually-uploading-the-apk-images/06-select-testing-method.png#lightbox)

Para más información sobre las pruebas de una aplicación, vea la guía de Google [Configurar pruebas alpha o beta](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en).

Una vez cargado el APK, se guarda como un borrador. No se puede publicar hasta que se proporcionan más detalles a Google Play, como se explica a continuación.

## <a name="store-listing"></a>Descripción de la tienda

Haga clic en **Store Listing (Descripción de la tienda)** en **Google Play Developer Console** para especificar la información que Google Play mostrará a los usuarios potenciales de la aplicación:

[![Cuadro de diálogo Descripción de la tienda](manually-uploading-the-apk-images/07-store-listing-sml.png)](manually-uploading-the-apk-images/07-store-listing.png#lightbox)

### <a name="graphics-assets"></a>Activos gráficos

Desplácese hacia abajo hasta la sección **GRAPHICS ASSETS (ACTIVOS GRÁFICOS)** de la página **Store Listing (Descripción de la tienda)** :

[![Sección Activos gráficos](manually-uploading-the-apk-images/08-graphic-assets-sml.png)](manually-uploading-the-apk-images/08-graphic-assets.png#lightbox)

Todos los activos promocionales que se prepararon anteriormente se cargan en esta sección. Se ofrece orientación sobre los activos promocionales que se deben proporcionar y el formato que deben tener.

### <a name="categorization"></a>Categorización

Después de la sección **GRAPHICS ASSETS (ACTIVOS GRÁFICOS)** hay una sección **CATEGORIZATION (CATEGORIZACIÓN)** donde se selecciona el tipo de aplicación y la categoría:

[![Sección Categorización](manually-uploading-the-apk-images/09-categorization-sml.png)](manually-uploading-the-apk-images/09-categorization.png#lightbox)

La clasificación de contenido se trata después de la sección siguiente.

### <a name="contact-details"></a>Detalles de contacto

La sección final de esta página es una sección **CONTACT DETAILS (DETALLES DE CONTACTO)** . Se usa para recopilar información de contacto sobre el desarrollador de la aplicación:

[![Sección Detalles de contacto](manually-uploading-the-apk-images/10-contact-details-sml.png)](manually-uploading-the-apk-images/10-contact-details.png#lightbox)

Es posible proporcionar una dirección URL para la directiva de privacidad de la aplicación en la sección **PRIVACY POLICY (DIRECTIVA DE PRIVACIDAD)** , como se ha indicado arriba.

## <a name="content-rating"></a>Clasificación de contenido

Haga clic en **Content Rating (Clasificación de contenido)** en **Google Play Developer Console**. En esta página, especifique la clasificación de contenido de la aplicación. Google Play exige que todas las aplicaciones especifiquen una clasificación de contenido. Haga clic en el botón **Continuar** para completar el cuestionario de clasificación de contenido:

[![Sección Clasificación de contenido](manually-uploading-the-apk-images/11-content-rating-sml.png)](manually-uploading-the-apk-images/11-content-rating.png#lightbox)

Todas las aplicaciones de Google Play deben clasificarse según el sistema de clasificación de Google Play. Además de la clasificación de contenido, todas las aplicaciones deben cumplir las [Políticas del Programa para Desarrolladores](https://www.android.com/us/developer-content-policy.html) de Google.

A continuación se muestran los cuatro niveles del sistema de clasificación de Google Play y se proporcionan algunas directrices como características o contenido que exigirían o forzarían el nivel de clasificación:

- **Apto para todo el público**: no debe publicar, compartir ni acceder a los datos de ubicación. No debe incluir ningún contenido generado por el usuario. No debe permitir la comunicación entre los usuarios.

- **Nivel de madurez bajo**: aplicaciones que acceden a datos de ubicación, pero no los comparten. Representaciones de violencia animada o moderada.

- **Nivel de madurez mediano**: referencias a alcohol, tabaco o drogas. Temas de juegos de apuestas o juegos de apuestas simulados. Contenido provocativo. Blasfemias y humor grosero. Referencias sexuales o provocativas.
    Violencia ficticia intensa. Violencia realista. Se permite que los usuarios se busquen entre ellos. Se permite que los usuarios se comuniquen entre ellos.
    Uso compartido de los datos de la ubicación de un usuario.

- **Nivel de madurez alto**: se centra en el consumo o la venta de alcohol, tabaco o drogas. Se centra en referencias sexuales o provocativas. Violencia explícita.

Los elementos de la lista de nivel de madurez mediano son subjetivos, ya que puede darse el caso de que una directriz que aparentemente indica una clasificación de nivel de madurez mediano sea lo suficientemente intensa para justificar una clasificación de nivel de madurez alto.

## <a name="pricing-amp-distribution"></a>Precios y distribución

Haga clic en **Pricing and Distribution (Precios y distribución)** en **Google Play Developer Console**. En esta página, establezca un precio si la aplicación es de pago.
La aplicación también se puede distribuir gratuitamente a todos los usuarios. Una vez que una aplicación se especifica como gratuita, debe seguir siéndolo.
Google Play no permite que una aplicación gratuita pase a ser de pago (aunque es posible vender contenido con facturación integrada en la aplicación con una aplicación gratuita). Google Play permite que una aplicación de pago pase a ser gratuita en cualquier momento.

Para publicar una aplicación de pago se necesita una cuenta de comerciante. Para ello, haga clic en **Set up a merchant account (Configurar una cuenta de comerciante)** y siga las instrucciones.

[![Cuadro de diálogo Pricing & Distribution (Precios y distribución)](manually-uploading-the-apk-images/12-pricing-sml.png)](manually-uploading-the-apk-images/12-pricing.png#lightbox)

### <a name="manage-countries"></a>Administrar países

La siguiente sección, **Administrar países**, proporciona control sobre los países en los que se puede distribuir una aplicación:

[![Cuadro de diálogo Manage Countries (Administrar países)](manually-uploading-the-apk-images/13-manage-countries-sml.png)](manually-uploading-the-apk-images/13-manage-countries.png#lightbox)

### <a name="other-information"></a>Otra información

Desplácese más hacia abajo para especificar si la aplicación contiene publicidad. Además, la sección **DEVICE CATEGORIES (CATEGORÍAS DE DISPOSITIVOS)** proporciona opciones para distribuir opcionalmente la aplicación para Android Wear, Android TV o Android Auto:

[![Sección Contains Ads (Contiene anuncios)](manually-uploading-the-apk-images/14-contains-ads-sml.png)](manually-uploading-the-apk-images/14-contains-ads.png#lightbox)

Después de esta sección hay opciones adicionales que se pueden seleccionar, como suscribirse a **Designed for Families (Diseñado para familias)** y distribuir la aplicación a través de Google Play for Education.

### <a name="consent"></a>Consentimiento

En la parte inferior de la página **Pricing &amp; Distribution (Precios y distribución)** está la sección **CONSENT (CONSENTIMIENTO)** .
Es una sección obligatoria que se usa para declarar que la aplicación se ajusta a las [directrices de contenido de Android](https://play.google.com/about/restricted-content/) y que cumple las leyes de exportación de los Estados Unidos:

[![Sección Consent (Consentimiento)](manually-uploading-the-apk-images/15-consent-sml.png)](manually-uploading-the-apk-images/15-consent.png#lightbox)

Hay muchos más aspectos de la publicación de una aplicación de Xamarin.Android que pueden tratarse en esta guía.
Para más información sobre cómo publicar la aplicación en Google Play, vea [Welcome to the Google Play Developer Console Help Center (Bienvenido al centro de ayuda de Google Play Developer Console)](https://support.google.com/googleplay/android-developer#topic=3450769).

## <a name="google-play-filters"></a>Filtros de Google Play

Cuando los usuarios exploran el sitio web de Google Play para aplicaciones, pueden buscar todas las aplicaciones publicadas. Cuando los usuarios exploran Google Play desde un dispositivo Android, los resultados son ligeramente diferentes. Los resultados se filtrarán según la compatibilidad con el dispositivo que se usa. Por ejemplo, si una aplicación debe enviar mensajes SMS, Google Play no mostrará esa aplicación a ningún dispositivo que no pueda enviar mensajes SMS. Los filtros que se aplican a una búsqueda se crean a partir de lo siguiente:

1. La configuración de hardware del dispositivo.
2. Las declaraciones del archivo de manifiesto de las aplicaciones.
3. El operador que se usa (si lo hubiera).
4. La ubicación del dispositivo.

Es posible agregar elementos al manifiesto de la aplicación para ayudar a controlar cómo se filtra la aplicación en Google Play Store. A continuación se recogen los elementos del manifiesto y los atributos que se pueden usar para filtrar aplicaciones:

- [supports-screen](https://developer.android.com/guide/topics/manifest/supports-screens-element.html): Google Play usa los atributos para determinar si se puede implementar una aplicación en un dispositivo en función del tamaño de la pantalla.
    Google Play supondrá que Android puede adaptar un diseño más pequeño en pantallas más grandes, pero no a la inversa. Así pues, una aplicación que declare su compatibilidad con pantallas normales aparecerá en las búsquedas para pantallas grandes, pero no para pantallas pequeñas. Si una determinada aplicación Xamarin.Android no proporciona un elemento `<supports-screen>` en el archivo de manifiesto, Google Play supondrá que todos los atributos tienen el valor true y que la aplicación admite todos los tamaños de pantalla. Este elemento se debe agregar manualmente a **AndroidManifest.xml**.

- [uses-configuration](https://developer.android.com/guide/topics/manifest/uses-configuration-element.html): este elemento del manifiesto se usa para solicitar determinadas características de hardware, como el tipo de teclado, los dispositivos de navegación, una pantalla táctil, etc. Este elemento se debe agregar manualmente a **AndroidManifest.xml**.

- [uses-feature](https://developer.android.com/guide/topics/manifest/uses-feature-element.html): este elemento del manifiesto declara características de hardware o de software con las que debe contar un dispositivo para que la aplicación funcione. Este atributo es solo informativo. Google Play no mostrará la aplicación en los dispositivos que no cumplan este filtro. Aun así, la aplicación se puede instalar de otras maneras (manualmente o bien descargándola). Este elemento se debe agregar manualmente a **AndroidManifest.xml**.

- [uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html): este elemento especifica que ciertas bibliotecas compartidas deben estar presentes en el dispositivo (por ejemplo, Google Maps). Este elemento también se puede especificar con `Android.App.UsesLibraryAttribute`. Por ejemplo:

    ```csharp
    [assembly: UsesLibrary("com.google.android.maps", true)]
    ```

- [uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html): este elemento se usa para deducir ciertas características de hardware necesarias para ejecutar la aplicación que tal vez no se declararon correctamente con un elemento `<uses-feature>`. Por ejemplo, si una aplicación solicita permiso para usar la cámara, Google Play presupone que los dispositivos deben tener una cámara, aunque no haya ningún elemento `<uses-feature>` que declare la cámara. Este elemento se puede establecer con `Android.App.UsesPermissionsAttribute`. Por ejemplo:

    ```csharp
    [assembly: UsesPermission(Manifest.Permission.Camera)]
    ```

- [uses-sdk](https://developer.android.com/guide/topics/manifest/uses-sdk-element.html): este elemento se usa para declarar el nivel mínimo de la API de Android necesario para la aplicación. Este elemento se puede establecer en las opciones de Xamarin.Android de un proyecto de Xamarin.Android.

- [compatible-screens](https://developer.android.com/guide/topics/manifest/compatible-screens-element.html): este elemento se usa para filtrar las aplicaciones que no coinciden con el tamaño de pantalla y la densidad especificados por este elemento. La mayoría de las aplicaciones no deben usar este filtro. Está diseñado para juegos o aplicaciones de alto rendimiento que precisan controles estrictos sobre la distribución de las aplicaciones. Se recomienda el uso del atributo `<support-screen>` mencionado anteriormente.

- [supports-gl-texture](https://developer.android.com/guide/topics/manifest/supports-gl-texture-element.html): este elemento se usa para declarar formaciones de compresión de textura GL necesarias para la aplicación. La mayoría de las aplicaciones no deben usar este filtro. Está diseñado para juegos o aplicaciones de alto rendimiento que precisan controles estrictos sobre la distribución de las aplicaciones.

Para más información sobre cómo configurar el manifiesto de la aplicación, vea el tema de Android [Manifiesto de la aplicación](https://developer.android.com/guide/topics/manifest/manifest-intro.html).
