---
title: Archivo iTunesMetadata.plist en aplicaciones Xamarin.iOS
description: En este artículo se habla sobre el archivo iTunesMetadata.plist usado para proporcionar información a iTunes sobre una aplicación de iOS con distribución ad hoc para pruebas o implementación empresarial.
ms.prod: xamarin
ms.assetid: 70676eba-6a99-4a3a-bccc-84359fe9c2c3
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/19/2017
ms.openlocfilehash: 47b2428ddca47f554fea675a9d2e70374679688f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281160"
---
# <a name="the-itunesmetadataplist-file-in-xamarinios-apps"></a>Archivo iTunesMetadata.plist en aplicaciones Xamarin.iOS

_En este artículo se habla sobre el archivo iTunesMetadata.plist usado para proporcionar información a iTunes sobre una aplicación de iOS con distribución ad hoc para pruebas o implementación empresarial._

Cuando se crea una aplicación de iOS en iTune Connect (ya sea para su venta o su distribución gratuita desde iTunes App Store), el desarrollador puede especificar información como el género de la aplicación, el subgénero, el aviso sobre derechos de autor, los dispositivos iOS admitidos y los requisitos del dispositivo. En el caso de las aplicaciones de iOS que se proporcionan a evaluadores o usuarios empresariales mediante distribución ad hoc, falta esta información.

Para proporcionar la información que falta en una distribución ad hoc, se puede crear un archivo `iTunesMetadata.plist` opcional e incluirlo en el archivo IPA de las aplicaciones. Este archivo plist es un archivo XML con un formato especial (vea [Property List Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/PropertyLists/Introduction/Introduction.html) [Guía de programación de la lista de propiedades] para obtener más información) que contiene pares de clave/valor que definen la información sobre una aplicación de iOS determinada.

<a name="iTunesMetadata_contents" />

## <a name="the-itunesmetadataplist-contents"></a>Contenido de iTunesMetadata.plist

El siguiente es un ejemplo de un archivo `iTunesMetadata.plist` típico usado para definir la información de iTunes para una distribución ad hoc:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>UIRequiredDeviceCapabilities</key>
    <dict>
        <key>armv7</key>
        <true/>
        <key>front-facing-camera</key>
        <true/>
    </dict>
    <key>artistName</key>
    <string>Company, Inc.</string>
    <key>bundleDisplayName</key>
    <string>App Name</string>
    <key>bundleShortVersionString</key>
    <string>1.5.1</string>
    <key>bundleVersion</key>
    <string>1.5.1</string>
    <key>copyright</key>
    <string>© 2015 Company, Inc.</string>
    <key>drmVersionNumber</key>
    <integer>0</integer>
    <key>fileExtension</key>
    <string>.app</string>
    <key>gameCenterEnabled</key>
    <false/>
    <key>gameCenterEverEnabled</key>
    <false/>
    <key>genre</key>
    <string>Games</string>
    <key>genreId</key>
    <integer>6014</integer>
    <key>itemName</key>
    <string>App Name</string>
    <key>kind</key>
    <string>software</string>
    <key>playlistArtistName</key>
    <string>Company, Inc.</string>
    <key>playlistName</key>
    <string>App Name</string>
    <key>releaseDate</key>
    <string>2015-11-18T03:23:10Z</string>
    <key>s</key>
    <integer>143441</integer>
    <key>softwareIconNeedsShine</key>
    <false/>
    <key>softwareSupportedDeviceIds</key>
    <array>
        <integer>9</integer>
    </array>
    <key>softwareVersionBundleId</key>
    <string>com.company.appid</string>
    <key>subgenres</key>
    <array>
        <dict>
            <key>genre</key>
            <string>Puzzle</string>
            <key>genreId</key>
            <integer>7012</integer>
        </dict>
        <dict>
            <key>genre</key>
            <string>Word</string>
            <key>genreId</key>
            <integer>7019</integer>
        </dict>
    </array>
    <key>versionRestrictions</key>
    <integer>16843008</integer>
</dict>
</plist>

```

Los valores de las claves individuales se explicarán detalladamente a continuación.

### <a name="uirequireddevicecapabilities"></a>UIRequiredDeviceCapabilities

La clave `UIRequiredDeviceCapabilities` permite a iTunes saber qué características específicas de dispositivo necesita una aplicación de iOS para poder instalarse en un dispositivo iOS determinado. Se proporciona como un diccionario (`<dict>...</dict>`) de características (`<key>...</key>`) y un valor booleano para cada característica. Si el valor de una característica es `true`, esa característica debe estar presente. Si es `false`, la característica no tiene que estar presente en el dispositivo. Por ejemplo:

```xml
<key>UIRequiredDeviceCapabilities</key>
<dict>
    <key>armv7</key>
    <true/>
    <key>front-facing-camera</key>
    <true/>
</dict>
```

Especifica que el dispositivo iOS debe admitir el conjunto de instrucciones ARM7 y tener una cámara frontal para que esta aplicación se pueda instalar en él. Para obtener una lista completa de valores permitidos, vea la documentación [UIRequiredDeviceCapabilities](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW3) de Apple.

### <a name="artistname-and-playlistartistname"></a>artistName y playlistArtistName

Use las claves `artistName` y `playlistArtistName` para definir el nombre de la empresa que ha creado la aplicación de iOS que se mostrará en iTunes. Ejemplo:

```xml
<key>artistName</key>
<string>Company, Inc.</string>
...
<key>playlistArtistName</key>
<string>Company, Inc.</string>
```

### <a name="bundledisplayname-itemname-and-playlistname"></a>bundleDisplayName, itemName y playlistName

Use las claves `bundleDisplayName`, `itemName` y `playlistName` para definir el nombre de la aplicación de iOS que se mostrará en iTunes. Ejemplo:

```xml
<key>bundleDisplayName</key>
<string>App Name</string>
...
<key>itemName</key>
<string>App Name</string>
...
<key>playlistName</key>
<string>App Name</string>
```

### <a name="bundleshortversionstring-and-bundleversion"></a>bundleShortVersionString y bundleVersion

Use las claves `bundleShortVersionString` y `bundleVersion` para definir el número de versión de la aplicación de iOS que se mostrará en iTunes. Ejemplo:

```xml
<key>bundleShortVersionString</key>
<string>1.5.1</string>
<key>bundleVersion</key>
<string>1.5.1</string>
```

### <a name="softwareversionbundleid"></a>softwareVersionBundleId

Use la clave `softwareVersionBundleId` para especificar el identificador de paquete de la aplicación de iOS. Ejemplo:

```xml
<key>softwareVersionBundleId</key>
<string>com.company.appid</string>
```

### <a name="copyright"></a>copyright

Use la clave `copyright` para definir el aviso sobre derechos de autor que se muestra en iTunes. Ejemplo:

```xml
<key>copyright</key>
<string>© 2015 Company, Inc.</string>
```

### <a name="releasedate"></a>releaseDate

Use la clave `releaseDate` para proporcionar una fecha de lanzamiento para la aplicación de iOS que se mostrará en iTunes. Ejemplo:

```xml
<key>releaseDate</key>
<string>2015-11-18T03:23:10Z</string>
```

### <a name="softwareiconneedsshine"></a>softwareIconNeedsShine

Use la clave `softwareIconNeedsShine` para indicar a iTunes si el icono de la aplicación de iOS necesita un _resaltado de brillo_ para iOS 6 (y anterior). Ejemplo:

```xml
<key>softwareIconNeedsShine</key>
<false/>
```

### <a name="gamecenterenabled-and-gamecentereverenabled"></a>gameCenterEnabled y gameCenterEverEnabled

Use las claves `gameCenterEnabled` y `gameCenterEverEnabled` para indicar a iTunes si esta aplicación de iOS es compatible con Game Center de Apple. Ejemplo:

```xml
<key>gameCenterEnabled</key>
<false/>
<key>gameCenterEverEnabled</key>
<false/>
```

### <a name="genre-genreid-and-subgenres"></a>genre, genreId y subgenres

Use las claves `genre` y `genreId` para indicar a iTunes a qué género pertenece la aplicación de iOS. Ejemplo:

```xml
<key>genre</key>
<string>Games</string>
<key>genreId</key>
<integer>6014</integer>
```

Opcionalmente, se puede usar la clave `subgenres` para definir hasta dos subgéneros para la aplicación de iOS. Ejemplo:

```xml
<key>subgenres</key>
<array>
    <dict>
        <key>genre</key>
        <string>Puzzle</string>
        <key>genreId</key>
        <integer>7012</integer>
    </dict>
    <dict>
        <key>genre</key>
        <string>Word</string>
        <key>genreId</key>
        <integer>7019</integer>
    </dict>
</array>
```

En el caso de las aplicaciones de iOS, Apple actualmente define los géneros e identificadores de género siguientes:

[!include[](~/ios/includes/table-appstore.md)]

Para más información, vea la documentación [Genre IDs Appendix (Apéndice de identificadores de género)](http://www.apple.com/itunes/affiliates/resources/documentation/genre-mapping.html) de Apple.

### <a name="softwaresupporteddeviceids"></a>softwareSupportedDeviceIds

Use la clave `softwareSupportedDeviceIds` para indicar a iTunes con qué dispositivos iOS es compatible esta aplicación de iOS. Ejemplo:

```xml
<key>softwareSupportedDeviceIds</key>
<array>
    <integer>9</integer>
</array>
```

Donde están disponibles los valores siguientes:

- 1 – Dispositivos iPhone clásicos
- 2 – iPod Touch
- 4 – iPad
- 9 – Dispositivos iPhone modernos

### <a name="standard-keys"></a>Claves estándar

Las claves siguientes están incluidas en todos los archivos `iTunesMetadata.plist` de las aplicaciones de iOS y siempre tienen los mismos valores:

```xml
<key>drmVersionNumber</key>
<integer>0</integer>
<key>fileExtension</key>
<string>.app</string>
...
<key>kind</key>
<string>software</string>
...
<key>s</key>
<integer>143441</integer>
...
<key>versionRestrictions</key>
<integer>16843008</integer>
```

<a name="iTunesMetadata_creating" />

## <a name="creating-an-itunesmetadataplist-file"></a>Creación de un archivo iTunesMetadata.plist

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

 Al trabajar con un archivo `iTunesMetadata.plist` en Visual Studio para Mac, tiene dos opciones:

- Crear y mantener el archivo mediante el editor de plist visual de Visual Studio para Mac.
- Crear y mantener el archivo en un editor de texto sin formato.

 Ambas opciones se explicarán en detalle a continuación.

### <a name="using-the-visual-plist-editor"></a>Con el editor de plist visual

Haga lo siguiente:

1. En el **Explorador de soluciones**, haga clic con el botón derecho en el archivo de proyecto de Xamarin.iOS y seleccione **Agregar** > **Nuevo archivo…** .
2. En el cuadro de diálogo Nuevo archivo, seleccione **iOS** > **Lista de propiedades**:

    ![](itunesmetadata-images/image01.png "Seleccione la lista de propiedades de iOS")
3. Escriba `iTunesMetadata` para el **Nombre** y haga clic en el botón **Nuevo**.
4. Haga doble clic en el archivo `iTunesMetadata.plist` en el **Explorador de soluciones** para abrirlo para su edición:

    ![](itunesmetadata-images/image02.png "Editor de iTunesMetadata.plist")
5. Haga clic en el **+** verde para crear una nueva entrada y escriba `UIRequiredDeviceCapabilities` como nombre de clave:

    ![](itunesmetadata-images/image03.png "Cree una entrada y escriba UIRequiredDeviceCapabilities como el nombre de clave")
6. Haga clic en el tipo de valor **Cadena** y seleccione **Diccionario** en la lista emergente:

    ![](itunesmetadata-images/image04.png "Seleccione el diccionario en la lista emergente")
7. Haga clic en el control situado a la izquierda del nombre de la propiedad para mostrar las entradas del diccionario:

    ![](itunesmetadata-images/image05.png "Mostrar las entradas del diccionario")
8. Haga clic en el texto **Agregar nueva entrada** y luego haga clic en el **+** verde para agregar una entrada al diccionario:

    ![](itunesmetadata-images/image06.png "Agregue una entrada al diccionario")
9. Escriba `armv7` para el nombre de clave, seleccione un tipo de **booleano** y escriba **Sí** como valor:

    ![](itunesmetadata-images/image07.png "Escriba armv7 para el nombre de clave, seleccione un tipo de booleano y escriba Sí como valor")
10. Repita los pasos anteriores hasta que haya rellenado el archivo `iTunesMetadata.plist` con todos los pares clave-valor necesarios (vea la sección anterior [Contenido de iTunesMetadata.plist](#iTunesMetadata_contents) para obtener más información).

11. Guarde los cambios en el archivo plist.

### <a name="using-a-plain-text-editor"></a>Uso de un editor de texto sin formato

Haga lo siguiente:

1. En un editor de texto sin formato, cree un nuevo archivo de texto y asígnele el nombre `iTunesMetadata.plist`.
2. Copie el contenido del ejemplo de la sección anterior [Contenido de iTunesMetadata.plist](#iTunesMetadata_contents).
3. Pegue el contenido en el archivo y edítelo según sea necesario.
4. Guarde el archivo y vuelva a Visual Studio para Mac.
5. En el **Explorador de soluciones**, haga clic con el botón derecho en el archivo de proyecto de Xamarin.iOS y seleccione **Agregar** > **Archivos existentes…** .
6. En el cuadro de diálogo Abrir archivo, seleccione el archivo `iTunesMetadata.plist` que creó arriba y haga clic en el botón **Aceptar**.
7. Deje la **Acción de compilación** de este archivo establecida en **Ninguna**.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

El complemento Xamarin para Visual Studio solo admite un editor visual para archivos `Info.plist` y `Entitlement.plist`, tendrá que crear el archivo `iTunesMetadata.plist` en un editor de texto estándar e incluirlo manualmente en el proyecto de Xamarin.iOS.

Haga lo siguiente:

1. En un editor de texto sin formato, cree un nuevo archivo de texto y asígnele el nombre `iTunesMetadata.plist`.
2. Copie el contenido del ejemplo de la sección anterior [Contenido de iTunesMetadata.plist](#iTunesMetadata_contents).
3. Pegue el contenido en el archivo y edítelo según sea necesario.
4. Guarde el archivo y vuelva a Visual Studio.
5. En el **Explorador de soluciones**, haga clic con el botón derecho en el archivo de proyecto de Xamarin.iOS y seleccione **Agregar** > **Archivos existentes…** .
6. En el cuadro de diálogo Abrir archivo, seleccione el archivo `iTunesMetadata.plist` que creó arriba y haga clic en el botón **Abrir**.
7. Deje la **Acción de compilación** de este archivo establecida en **Ninguna**.

-----

Luego, seleccione este archivo `iTunesMetadata.plist` al prepararse para crear su IPA en el IDE.

## <a name="summary"></a>Resumen

En este artículo se ha tratado el archivo `iTunesMetadata.plist` que se puede usar para informar a iTunes sobre una aplicación de iOS distribuida ad hoc. Se ha hablado de la clave estándar del archivo plist y de cómo crear y mantener el archivo en Visual Studio y Visual Studio para Mac.

## <a name="related-links"></a>Vínculos relacionados

- [Distribución a través del App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md)
- [Configuración de una aplicación en iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md)
- [Publicación en App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Distribución interna](~/ios/deploy-test/app-distribution/in-house-distribution.md)
- [Distribución ad hoc](~/ios/deploy-test/app-distribution/ad-hoc-distribution.md)
- [Compatibilidad con IPA](~/ios/deploy-test/app-distribution/ipa-support.md)
- [Solución de problemas](~/ios/deploy-test/troubleshooting.md)
