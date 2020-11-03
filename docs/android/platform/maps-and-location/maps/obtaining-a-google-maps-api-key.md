---
title: Obtención de una clave de la API de Google Maps
description: Descubra cómo obtener una clave de la API de Google Maps para agregar la funcionalidad de mapas a la aplicación.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/25/2018
ms.openlocfilehash: c19a2c7c8b0a02f946bf4984945a3b24cca26d1c
ms.sourcegitcommit: 1550019cd1e858d4d13a4ae6dfb4a5947702f24b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897538"
---
# <a name="obtaining-a-google-maps-api-key"></a>Obtención de una clave de la API de Google Maps

Para usar la funcionalidad de Google Maps en Android, debe registrarse para obtener una clave de la API de Maps con Google. Hasta que no lo haga, solo verá una cuadrícula en blanco en lugar de un mapa en sus aplicaciones. Debe obtener una clave de la versión 2 de la API de Google Maps para Android: las claves de la versión 1 de la API de Google Maps para Android no funcionarán.

Para obtener una clave de la versión 2 de la API de Maps, hay que realizar los siguientes pasos:

1. Recupere la huella digital SHA-1 del almacén de claves que se usa para firmar la aplicación.
2. Cree un proyecto en la consola de API de Google.
3. Obtenga la clave de API.

## <a name="obtaining-your-signing-key-fingerprint"></a>Obtención de la huella digital de la clave de firma

Para solicitar una clave de API de Maps a Google, debe conocer la huella digital SHA-1 del almacén de claves que se usa para firmar la aplicación.
Normalmente, esto significa que tendrá que determinar la huella digital SHA-1 para el almacén de claves de depuración y, a continuación, la huella digital SHA-1 del almacén de claves que se usó para firmar la aplicación para su lanzamiento.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

De forma predeterminada, el almacén de claves que se utiliza para firmar las versiones de depuración de una aplicación de Xamarin.Android puede encontrarse en la siguiente ubicación:

**C:\\Users\\[NOMBREDEUSUARIO]\\AppData\\Local\\Xamarin\\Mono for Android\\debug.keystore**

La información acerca de un almacén de claves se obtiene ejecutando el comando `keytool` desde el JDK. La herramienta normalmente se encuentra en el siguiente directorio bin de Java:

**C:\\Archivos de programa\\Android\\jdk\\microsoft_dist_openjdk_[VERSIÓN]\\bin\\keytool.exe**

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

De forma predeterminada, el almacén de claves que se utiliza para firmar las versiones de depuración de una aplicación de Xamarin.Android puede encontrarse en la siguiente ubicación:

**/Users/[NOMBREDEUSUARIO]/.local/share/Xamarin/Mono for Android/debug.keystore**

La información acerca de un almacén de claves se obtiene ejecutando el comando `keytool` desde el JDK. La herramienta normalmente se encuentra en el siguiente directorio bin de Java:

**/System/Library/Java/JavaVirtualMachines/[VERSIÓN].jdk/Contents/Home/bin/keytool**

-----

Ejecute keytool con el siguiente comando (con las rutas de acceso de archivo que se muestran arriba):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Ejemplo de Debug.keystore

Para la clave de depuración predeterminada (que se crea automáticamente para su depuración), use este comando:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----

### <a name="production-keys"></a>Claves de producción

Al implementar una aplicación en Google Play, debe estar [firmada con una clave privada](~/android/deploy-test/signing/index.md).
`keytool` deberá ejecutarse con los detalles de la clave privada y la huella digital SHA-1 resultante utilizada para crear una clave de producción de la API de Google Maps. Recuerde actualizar el archivo **AndroidManifest.xml** con la clave de la API de Google Maps correcta antes de la implementación.

### <a name="keytool-output"></a>Salida de keytool

Debería ver algo parecido a la siguiente salida en la ventana de la consola:

```shell
Alias name: androiddebugkey
Creation date: Jan 01, 2016
Entry type: PrivateKeyEntry
Certificate chain length: 1
Certificate[1]:
Owner: CN=Android Debug, O=Android, C=US
Issuer: CN=Android Debug, O=Android, C=US
Serial number: 4aa9b300
Valid from: Mon Jan 01 08:04:04 UTC 2013 until: Mon Jan 01 18:04:04 PST 2033
Certificate fingerprints:
    MD5:  AE:9F:95:D0:A6:86:89:BC:A8:70:BA:34:FF:6A:AC:F9
    SHA1: BB:0D:AC:74:D3:21:E1:43:07:71:9B:62:90:AF:A1:66:6E:44:5D:75
    Signature algorithm name: SHA1withRSA
    Version: 3
```

En esta guía, usará la huella digital SHA-1 (que aparece después de **SHA1** ).

## <a name="creating-an-api-project"></a>Crear un proyecto de API

Una vez que haya recuperado la huella digital SHA-1 del almacén de claves de firma, es necesario crear un proyecto en la consola de API de Google (o agregar el servicio de la versión 2 de la API de Google Maps para Android a un proyecto).

1. En un explorador, navegue hasta [consola de Google Developers y vaya al panel de control APIs y Servicios](https://console.developers.google.com/apis/dashboard/). Luego, haga clic en **Selecciona un proyecto**. Haga clic en el nombre de un proyecto o cree uno haciendo clic en **Nuevo proyecto** :

   [![Botón CREAR PROYECTO de la consola de Google Developers](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Si ha creado un proyecto, escriba el nombre del proyecto en el cuadro de diálogo **Nuevo proyecto** que se muestra. Este cuadro de diálogo creará un identificador de proyecto único basado en el nombre del proyecto. A continuación, haga clic en el botón **Crear** como se muestra en este ejemplo:

   [![El nuevo proyecto se denomina "XamarinMapsDemo"](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Después de un minuto aproximadamente, se crea el proyecto y se le redireccionará a la página **Panel de control** del proyecto. Desde allí, haga clic en **HABILITAR APIs Y SERVICIOS** :

   [![Clic en la API de Google Maps para Android en la sección Biblioteca](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. En la página **Biblioteca de APIs** , haga clic en **Maps SDK for Android** (SDK de Maps para Android). En la página siguiente, haga clic en **Habilitar** para activar el servicio en este proyecto:

   [![Clic en el botón HABILITAR en la sección Panel de control](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

En este punto, se ha creado el proyecto de API y se le ha agregado la versión 2 de la API de Google Maps para Android. Sin embargo, no puede usar esta API en el proyecto hasta que cree las credenciales para ella. En la sección siguiente se explica cómo crear una clave de API y autorizar a una aplicación de Xamarin.Android a usar esta clave.

## <a name="obtaining-the-api-key"></a>Obtención de la clave de API

Una vez creado el proyecto de API en la **consola de Google Developers** , es necesario crear una clave de API de Android. Las aplicaciones Xamarin.Android deben tener una clave de API antes de que se les conceda acceso a la versión 2 de Maps para Android.

1. En la página **Maps SDK for Android** (SDK de Maps para Android) que se muestra (después de hacer clic en **HABILITAR** en el paso anterior), vaya a la pestaña **Credenciales** y haga clic en el botón **Crear credenciales** :

   [![Mensaje de credenciales del SDK de Maps para Android](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Haga clic en **Clave de API** :

   [![Cuadro de diálogo para agregar credenciales al proyecto](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Después de hacer clic en este botón, se genera la clave de API. A continuación, es necesario restringir esta clave para que solo la aplicación pueda llamar a las API con esta clave. Haga clic en **RESTRINGIR CLAVE** :

   [![Clic en Restringir clave en la página Credenciales](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Cambie el campo **Nombre** de **Clave de API 1** a un nombre que lo ayude a recordar para qué se usará la clave (en este ejemplo se usa **XamarinMapsDemoKey** ). A continuación, haga clic en el botón de radio **Aplicaciones Android** :

   [![Selección de aplicaciones Android en la página Credenciales](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Para agregar la huella digital SHA-1, haga clic en **Agregar nombre de paquete y huella digital** :

   [![Clic en Agregar nombre de paquete y huella digital](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Escriba el nombre del paquete de la aplicación y el de la huella digital del certificado SHA-1 (obtenida a través de `keytool` como se explicó anteriormente en esta guía). En el ejemplo siguiente, se escribe el nombre del paquete de `XamarinMapsDemo`, seguido de la huella digital del certificado SHA-1 obtenida de **Debug.keystore** :

   [![El nombre del paquete especificado es com.xamarin.docs.android.map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Tenga en cuenta que, para que el APK tenga acceso a Google Maps, debe incluir los nombres de paquete y las huellas digitales SHA-1 para cada almacén de claves (depuración y lanzamiento) que se use para firmar el APK. Por ejemplo, si usa un equipo para la depuración y otro para generar el APK de versión, debe incluir la huella digital del certificado SHA-1 desde el almacén de claves de depuración del primer equipo y la huella digital del certificado SHA-1 del almacén de claves de versión del segundo equipo. Haga clic en **Agregar nombre de paquete y huella digital** para agregar otra huella digital y otro nombre del paquete, tal como se muestra en este ejemplo:

   [![Al agregar otra huella digital se crea otro certificado SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Haga clic en el botón **Guardar** para guardar los cambios. A continuación, volverá automáticamente a la lista de claves de API. Si tiene otras claves de API que ha creado anteriormente, también se mostrarán aquí. En este ejemplo, solo se muestra una clave de API (creada en los pasos anteriores):

   [![XamarinMapsDemoKey se muestra en la lista de claves de API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Conexión del proyecto a una cuenta facturable

A partir del 11 de junio de 2018, la clave de API no funcionará si el proyecto no está conectado a una cuenta facturable (aunque el servicio siga siendo gratuito para aplicaciones móviles).

1. Haga clic en el botón del menú lateral y seleccione la página **Facturación** :

   [![Selección de la sección Facturación en el menú lateral](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Para vincular el proyecto a una cuenta de facturación, haga clic en **Enlazar cuenta de facturación** y después en **CREAR CUENTA DE FACTURACIÓN** , en el menú emergente mostrado (si no tiene una cuenta, se le redireccionará a una página para crear una):

   [![Vinculación del proyecto a una cuenta de facturación](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Incorporación de la clave al proyecto

Por último, agregue esta clave de API al archivo **AndroidManifest.XML** de la aplicación Xamarin.Android. En el ejemplo siguiente, `YOUR_API_KEY` se reemplazará por la clave de API generada en los pasos anteriores:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    android:versionName="4.10" package="com.xamarin.docs.android.mapsandlocationdemo"
    android:versionCode="10">
...
  <application android:label="@string/app_name">
    <!-- Put your Google Maps V2 API Key here. -->
    <meta-data android:name="com.google.android.maps.v2.API_KEY" android:value="YOUR_API_KEY" />
    <meta-data android:name="com.google.android.gms.version" android:value="@integer/google_play_services_version" />
  </application>
</manifest>
```

## <a name="related-links"></a>Vínculos relacionados

- [Consola de API de Google](https://code.google.com/apis/console/)
- [La clave de la API de Google Maps](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](https://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
