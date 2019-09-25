---
title: Obtención de una clave de la API de Google Maps
description: Cómo obtener una clave de API de Google Maps para agregar la funcionalidad de Maps a la aplicación.
ms.prod: xamarin
ms.assetid: D5969C57-3444-465E-D6FF-249AEE62E127
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: 3868b2a35894cdcd7a11c626268307338744ecb4
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250060"
---
# <a name="obtaining-a-google-maps-api-key"></a>Obtención de una clave de la API de Google Maps

Para usar la funcionalidad de Google Maps en Android, debe registrarse para obtener una clave de API de Maps con Google. Hasta que lo haga, solo verá una cuadrícula en blanco en lugar de una asignación en sus aplicaciones. Debe obtener una clave de las claves de Android API V2 de Google Maps de la antigua clave de API de Android de Google Maps no funcionará.

La obtención de una clave de Maps API V2 implica los siguientes pasos:

1. Recupere la huella digital SHA-1 del almacén de claves que se usa para firmar la aplicación.
2. Cree un proyecto en la consola de API de Google.
3. Obtención de la clave de API.

## <a name="obtaining-your-signing-key-fingerprint"></a>Obtención de la huella digital de clave de firma

Para solicitar una clave de API de Maps de Google, debe conocer la huella digital SHA-1 del almacén de claves que se usa para firmar la aplicación.
Normalmente, esto significa que tendrá que determinar la huella digital SHA-1 para el almacén de claves de depuración y, a continuación, la huella digital SHA-1 para el almacén de claves que se usa para firmar la aplicación para su lanzamiento.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

De forma predeterminada, el almacén de claves que se usa para firmar las versiones de depuración de una aplicación de Xamarin. Android se puede encontrar en la siguiente ubicación:

**C:\\usuarios\\[nombre de\\usuario\\]\\AppData\\local Xamarin mono\\for Android Debug. keystore**

La información acerca de un almacén de claves se obtiene ejecutando el comando `keytool` desde el JDK. Esta herramienta se encuentra normalmente en el directorio bin de Java:

**C:\\archivos de programa (x86\\)\\Java JDK [versión\\]\\bin keytool. exe**

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

De forma predeterminada, el almacén de claves que se usa para firmar las versiones de depuración de una aplicación de Xamarin. Android se puede encontrar en la siguiente ubicación:

**/Users/[nombre de usuario]/.local/share/Xamarin/Mono para Android/Debug. keystore**

La información acerca de un almacén de claves se obtiene ejecutando el comando `keytool` desde el JDK. Esta herramienta se encuentra normalmente en el directorio bin de Java:

**/System/Library/Java/JavaVirtualMachines/[VERSION].jdk/Contents/Home/bin/keytool**

-----

Ejecute keytool con el siguiente comando (con las rutas de acceso de archivo que se muestran arriba):

```shell
keytool -list -v -keystore [STORE FILENAME] -alias [KEY NAME] -storepass [STORE PASSWORD] -keypass [KEY PASSWORD]
```

### <a name="debugkeystore-example"></a>Ejemplo de Debug. keystore

Para la clave de depuración predeterminada (que se crea automáticamente para su depuración), use este comando:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

```cmd
keytool.exe -list -v -keystore "C:\Users\[USERNAME]\AppData\Local\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

```bash
keytool -list -v -keystore /Users/[USERNAME]/.local/share/Xamarin/Mono\ for\ Android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

-----

### <a name="production-keys"></a>Claves de producción

Al implementar una aplicación en Google Play, debe estar [firmada con una clave privada](~/android/deploy-test/signing/index.md).
`keytool` Tendrá que ejecutarse con los detalles de la clave privada y la huella digital de SHA-1 resultante utilizada para crear una clave de API de Google Maps de producción. Recuerde actualizar el archivo **archivo AndroidManifest. XML** con la clave de API de Google Maps correcta antes de la implementación.

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

En esta guía, usará la huella digital SHA-1 (enumerada después de **SHA1**).

## <a name="creating-an-api-project"></a>Creación de un proyecto de API

Una vez que haya recuperado la huella digital SHA-1 del almacén de claves de firma, es necesario crear un nuevo proyecto en la consola de API de Google (o agregar el servicio Google Maps Android API V2 a un proyecto existente).

1. En un explorador, vaya al [Panel de Google Developers Console API & Services](https://console.developers.google.com/apis/dashboard/) y haga clic en **seleccionar un proyecto**. Haga clic en un nombre de proyecto o cree uno nuevo haciendo clic en **nuevo proyecto**:

   [![Botón crear proyecto de la consola de desarrollador de Google](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs-sml.png)](obtaining-a-google-maps-api-key-images/01-google-developer-console-vs.png#lightbox)

2. Si ha creado un proyecto nuevo, escriba el nombre del proyecto en el cuadro de diálogo **nuevo proyecto** que se muestra. Este cuadro de diálogo fabricará un identificador de proyecto único basado en el nombre del proyecto. A continuación, haga clic en el botón **crear** , tal como se muestra en este ejemplo:

   [![El nuevo proyecto se denomina XamarinMapsDemo](obtaining-a-google-maps-api-key-images/02-new-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/02-new-project-vs.png#lightbox)

3. Después de un minuto, se crea el proyecto y se le dirigirá a la página **Panel** del proyecto. Desde allí, haga clic en **Habilitar API y servicios**:

   [![Hacer clic en Google Maps Android API en la sección biblioteca](obtaining-a-google-maps-api-key-images/03-api-selection-vs-sml.png)](obtaining-a-google-maps-api-key-images/03-api-selection-vs.png#lightbox)

4. En la página **biblioteca de API** , haga clic en **Maps SDK for Android**. En la página siguiente, haga clic en **Habilitar** para activar el servicio para este proyecto:

   [![Hacer clic en el botón habilitar en la sección panel](obtaining-a-google-maps-api-key-images/04-enable-api-vs-sml.png)](obtaining-a-google-maps-api-key-images/04-enable-api-vs.png#lightbox)

En este momento se ha creado el proyecto de API y se le ha agregado Google Maps API de Android v2. Sin embargo, no puede usar esta API en el proyecto hasta que cree las credenciales para ella. En la sección siguiente se explica cómo crear una clave de API y una lista en blanco una aplicación de Xamarin. Android para que esté autorizada para usar esta clave.

## <a name="obtaining-the-api-key"></a>Obtención de la clave de API

Una vez creado el proyecto de API de **Google Developer Console** , es necesario crear una clave de API de Android. Las aplicaciones de Xamarin. Android deben tener una clave de API antes de que se les conceda acceso a Android Map API V2.

1. En la página **mapas SDK para Android** que se muestra (después de hacer clic en **Habilitar** en el paso anterior), vaya a la pestaña **credenciales** y haga clic en el botón **crear credenciales** :

   [![Mensaje de credenciales del SDK de Maps para Android](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs-sml.png)](obtaining-a-google-maps-api-key-images/05-api-is-enabled-vs.png#lightbox)

2. Haga clic en **clave de API**:

   [![Agregar credenciales al cuadro de diálogo del proyecto](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs-sml.png)](obtaining-a-google-maps-api-key-images/06-add-credentials-to-your-project-vs.png#lightbox)

3. Después de hacer clic en este botón, se genera la clave de API. A continuación, es necesario restringir esta clave para que solo la aplicación pueda llamar a las API con esta clave. Haga clic en **restringir clave**:

   [![Hacer clic en restringir clave en la página credenciales](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs-sml.png)](obtaining-a-google-maps-api-key-images/07-generate-api-key-vs.png#lightbox)

4. Cambie el campo de **nombre** de la **clave de API 1** a un nombre que le ayude a recordar para qué se usa la clave (**XamarinMapsDemoKey** se usa en este ejemplo). A continuación, haga clic en el botón de radio **aplicaciones Android** :

   [![Selección de aplicaciones Android en la página credenciales](obtaining-a-google-maps-api-key-images/08-key-restriction-vs-sml.png)](obtaining-a-google-maps-api-key-images/08-key-restriction-vs.png#lightbox)

5. Para agregar la huella digital SHA-1, haga clic en **+ Agregar nombre de paquete y huella digital**:

   [![Hacer clic en Agregar nombre de paquete y huella digital](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/09-add-package-fingerprint-vs.png#lightbox)

6. Escriba el nombre del paquete de la aplicación y escriba la huella digital del certificado Sha- `keytool` 1 (obtenida a través de como se explicó anteriormente en esta guía). En el ejemplo siguiente, se escribe el nombre `XamarinMapsDemo` del paquete para, seguido de la huella digital del certificado SHA-1 obtenida de **Debug. keystore**:

   [![El nombre del paquete especificado es com. Xamarin. docs. Android. map](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs-sml.png)](obtaining-a-google-maps-api-key-images/10-enter-package-and-sha1-vs.png#lightbox)

7. Tenga en cuenta que, en orden para el APK tener acceso a Google Maps, debe incluir las huellas digitales de SHA-1 y empaquetar los nombres para cada almacén de claves (debug y release) que usó para firmar el APK. Por ejemplo, si usa un equipo para depuración y otro equipo para generar el APK de lanzamiento, debe incluir la huella digital de certificado de SHA-1 desde el almacén de claves de depuración del primer equipo y la huella digital de certificado de SHA-1 desde el almacén de claves de la versión de el segundo equipo. Haga clic en **+ Agregar nombre de paquete y huella digital** para agregar otra huella digital y el nombre del paquete, tal como se muestra en este ejemplo:

   [![Agregar otra huella digital crea otro certificado SHA-1](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs-sml.png)](obtaining-a-google-maps-api-key-images/11-second-fingerprint-vs.png#lightbox)

8. Haga clic en el botón **Guardar** para guardar los cambios. A continuación, se le devolverá a la lista de claves de API. Si tiene otras claves de API que ha creado anteriormente, también se mostrarán aquí. En este ejemplo, solo se muestra una clave de API (creada en los pasos anteriores):

   [![XamarinMapsDemoKey se muestra en la lista de claves de API](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs-sml.png)](obtaining-a-google-maps-api-key-images/12-list-of-apis-vs.png#lightbox)

## <a name="connect-the-project-to-a-billable-account"></a>Conectar el proyecto a una cuenta facturable

A partir del 11 de junio de 2018, la clave de API no funcionará si el proyecto no está conectado a una cuenta facturable (aunque el servicio siga siendo gratuito para aplicaciones móviles).

1. Haga clic en el botón del menú hamburguesa y seleccione la página **facturación** :

   [![Selección de la sección facturación del menú hamburguesa](obtaining-a-google-maps-api-key-images/13-goto-billing-vs-sml.png)](obtaining-a-google-maps-api-key-images/13-goto-billing-vs.png#lightbox)

2. Para vincular el proyecto a una cuenta de facturación, haga clic en **vincular una cuenta de facturación** seguida de **crear cuenta de facturación** en el menú emergente mostrado (si no tiene una cuenta, se le guiará para crear una nueva):

   [![Vincular proyecto a cuenta de facturación](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs-sml.png)](obtaining-a-google-maps-api-key-images/14-link-billing-account-vs.png#lightbox)

## <a name="adding-the-key-to-your-project"></a>Agregar la clave al proyecto

Por último, agregue esta clave de API al archivo **archivo AndroidManifest. XML** de la aplicación de Xamarin. Android. En el ejemplo siguiente, `YOUR_API_KEY` se reemplazará por la clave de API generada en los pasos anteriores:

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
- [La clave de API de Google Maps](https://developers.google.com/maps/documentation/android/start#the_google_maps_api_key)
- [keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/windows/keytool.html.)
