---
title: Firma manual del APK
ms.prod: xamarin
ms.assetid: 08549E1C-7F04-4D20-9E7A-794B9D09FD12
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 3be55c2149aa58bf6d8462e5c1ff24166078355f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755898"
---
# <a name="manually-signing-the-apk"></a>Firma manual del APK

Tras compilar la aplicación para su lanzamiento, es necesario firmar el APK antes de distribuirla. De este modo podrá ejecutarse en un dispositivo Android. Este proceso normalmente se administra con el IDE, aunque hay casos en los que es necesario firmar el APK manualmente en la línea de comandos. El proceso de firma de APK incluye los siguientes pasos:

1. **Crear una clave privada**. Este paso debe realizarse una sola vez. Es necesaria una clave privada para firmar digitalmente el APK.
    Una vez que se ha preparado la clave privada, se puede omitir este paso para las futuras compilaciones de versión.

2. **Aplicar la herramienta zipalign al APK**. *Zipalign* es un proceso de optimización que se lleva a cabo en una aplicación. Permite a Android interactuar de forma más eficaz con el APK en tiempo de ejecución. Xamarin.Android realiza una comprobación en runtime y no permite que la aplicación se ejecute si no se ha aplicado la herramienta zipalign al APK.

3. **Firmar el APK**. Este paso conlleva usar la herramienta **apksigner** del SDK de Android y firmar el APK con la clave privada creada en el paso anterior. Las aplicaciones desarrolladas con versiones anteriores a la 24.0.3 de las herramientas de compilación del SDK de Android usarán la aplicación **jarsigner** del JDK. Ambas herramientas se tratarán con más detalle a continuación. 

El orden de los pasos es importante y depende de qué herramienta se utiliza para firmar el APK. Si se usa **apksigner**, en primer lugar es importante usar **zipalign** con la aplicación y, después, firmarla con **apksigner**.  En caso de que sea necesario usar **jarsigner** para firmar el APK, es importante firmar primero el APK y, después, ejecutar **zipalign**. 

## <a name="prerequisites"></a>Requisitos previos

Esta guía se centra en el uso de **apksigner** de las herramientas de compilación del SDK de Android, versión 24.0.3 o posterior. En ella se da por supuesto que ya ha creado el APK.

Las aplicaciones compiladas con una versión anterior de las herramientas de compilación del SDK de Android deben usar **jarsigner**, como se describe en la sección [Firma del APK con jarsigner](#Sign_the_APK_with_jarsigner) de a continuación.

## <a name="create-a-private-keystore"></a>Crear un almacén de claves privadas

Un *almacén de claves* es una base de datos de certificados de seguridad que se crea mediante el programa [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) del SDK de Java. Es fundamental que exista un almacén de claves para publicar una aplicación de Xamarin.Android, dado que Android no ejecuta las aplicaciones que no están firmadas digitalmente.

Durante el desarrollo, Xamarin.Android usa un almacén de claves de depuración para firmar la aplicación, lo que permite que la aplicación se implemente directamente en el emulador o en dispositivos configurados para usar aplicaciones depurables.
Pero este almacén de claves no se reconoce como un almacén de claves válido para distribuir aplicaciones.

Por este motivo, se debe crear y usar un almacén de claves privadas para firmar las aplicaciones. Este es un paso que solo debe realizarse una vez, dado que la misma clave se usará para publicar actualizaciones y, después, puede usarse para firmar otras aplicaciones.

Es importante proteger este almacén de claves. Si se pierde, no será posible publicar actualizaciones de la aplicación con Google Play.
La única manera de solucionar los problemas causados por la pérdida de un almacén de claves sería crear otro almacén de claves, volver a firmar el APK con la clave nueva y, después, enviar una aplicación nueva. Después, la aplicación anterior tendría que quitarse de Google Play. Del mismo modo, si este nuevo almacén de claves corre peligro o se distribuye públicamente, es posible que se distribuyan versiones no oficiales o malintencionadas de una aplicación.

### <a name="create-a-new-keystore"></a>Crear un almacén de claves nuevo

La creación de un almacén de claves nuevo requiere la herramienta de línea de comandos [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) del SDK de Java. El fragmento de código siguiente es un ejemplo de cómo usar **keytool** (reemplace `<my-filename>` por el nombre de archivo del almacén de claves y `<key-name>` por el nombre de la clave del almacén de claves):

```shell
$ keytool -genkeypair -v -keystore <filename>.keystore -alias <key-name> -keyalg RSA \
          -keysize 2048 -validity 10000
```

Lo primero que **keytool** pedirá es la contraseña del almacén de claves. Después, solicitará cierta información para ayudar a crear la clave. El fragmento de código siguiente es un ejemplo de cómo crear una nueva clave denominada `publishingdoc` que se almacenará en el archivo `xample.keystore`:

```shell
$ keytool -genkeypair -v -keystore xample.keystore -alias publishingdoc -keyalg RSA -keysize 2048 -validity 10000
Enter keystore password:
Re-enter new password:
What is your first and last name?
  [Unknown]:  Ham Chimpanze
What is the name of your organizational unit?
  [Unknown]:  NASA
What is the name of your organization?
  [Unknown]:  NASA
What is the name of your City or Locality?
  [Unknown]:  Cape Canaveral
What is the name of your State or Province?
  [Unknown]:  Florida
What is the two-letter country code for this unit?
  [Unknown]:  US
Is CN=Ham Chimpanze, OU=NASA, O=NASA, L=Cape Canaveral, ST=Florida, C=US correct?
  [no]:  yes

Generating 2,048 bit RSA key pair and self-signed certificate (SHA1withRSA) with a validity of 10,000 days
        for: CN=Ham Chimpanze, OU=NASA, O=NASA, L=Cape Canaveral, ST=Florida, C=US
Enter key password for <publishingdoc>
        (RETURN if same as keystore password):
Re-enter new password:
[Storing xample.keystore]
```

Para enumerar las claves que están almacenadas en un almacén de claves, use **keytool** con la opción &ndash; `list`:

```shell
$ keytool -list -keystore xample.keystore
```

## <a name="zipalign-the-apk"></a>Aplicar zipalign al APK

Antes de firmar un APK con **apksigner**, es importante optimizar el archivo con la herramienta **zipalign** del SDK de Android. La herramienta **zipalign** reestructurará los recursos de un APK con límites de 4 bytes. Esta alineación permite a Android cargar rápidamente los recursos del APK, con lo que se aumenta el rendimiento de la aplicación y se reduce sustancialmente el uso de memoria. Xamarin.Android llevará a cabo una comprobación en runtime para determinar si se ha aplicado zipalign al APK. Si no se ha aplicado zipalign al APK, no se ejecutará la aplicación.

El siguiente comando usará el APK firmado y generará un APK firmado con zipalign aplicado denominado **helloworld.apk** listo para su distribución.

```shell
$ zipalign -f -v 4 mono.samples.helloworld-unsigned.apk helloworld.apk
```

## <a name="sign-the-apk"></a>Firmar el APK

Después de usar zipalign con el APK, es necesario firmarlo con un almacén de claves. Esto se hace con la herramienta **apksigner**, disponible en el directorio **build-tools** de la versión de las herramientas de compilación del SDK.  Por ejemplo, si tiene instalada la versión 25.0.3 de las herramientas de compilación del SDK de Android, **apksigner** puede encontrarse en el directorio:

```bash
$ ls $ANDROID_HOME/build-tools/25.0.3/apksigner
/Users/tom/android-sdk-macosx/build-tools/25.0.3/apksigner*
```

El siguiente fragmento de código da por hecho que la variable de entorno `PATH` puede acceder a **apksigner**. Firmará un APK con el alias de clave `publishingdoc` incluido en el archivo **xample.keystore**:

```shell
$ apksigner sign --ks xample.keystore --ks-key-alias publishingdoc mono.samples.helloworld.apk
```

Al ejecutar este comando, y si fuera necesario, **apksigner** solicitará la contraseña del almacén de claves.

Para obtener las información sobre el uso de **apksigner**, consulte la [documentación de Google](https://developer.android.com/studio/command-line/apksigner.html).

> [!NOTE]
> De acuerdo con el [problema 62696222 de Google](https://issuetracker.google.com/issues/62696222), **apksigner** "no se encuentra" en Android SDK. La solución alternativa a este problema es instalar la versión 25.0.3 de las herramientas de compilación del SDK de Android y usar dicha versión de **apksigner**.  

<a name="Sign_the_APK_with_jarsigner" />

### <a name="sign-the-apk-with-jarsigner"></a>Firma del APK con jarsigner

> [!WARNING]
> Esta sección solo es aplicable si es necesario firmar el APK con la herramienta **jarsigner**. Se recomienda a los desarrolladores que usen **apksigner** para firmar el APK.

Este técnica implica firmar el archivo APK con el comando **[jarsigner](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html)** del SDK de Java.  La herramienta **jarsigner** se proporciona mediante el SDK de Java. 

A continuación se muestra cómo firmar un APK mediante **jarsigner** y la clave `publishingdoc` que se encuentra en un archivo de almacén de claves denominado **xample.keystore**:

```shell
$ jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore xample.keystore mono.samples.helloworld.apk publishingdoc
```

> [!NOTE]
> Al usar **jarsigner**, es importante firmar el APK _en primer lugar_ y, luego, usar **zipalign**.  

## <a name="related-links"></a>Vínculos relacionados

- [Firma de aplicaciones](https://source.android.com/security/apksigning/)
- [Firma de JAR de Java](https://docs.oracle.com/javase/8/docs/technotes~/jar/jar.html#Signed_JAR_File)
- [jarsigner](https://docs.oracle.com/javase/8/docs/technotes/tools/windows/jarsigner.html)
- [keytool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)
- [zipalign](https://developer.android.com/studio/command-line/zipalign.html)
- [Herramientas de compilación 26.0.0: ¿dónde está apksigner?](https://issuetracker.google.com/issues/62696222)
