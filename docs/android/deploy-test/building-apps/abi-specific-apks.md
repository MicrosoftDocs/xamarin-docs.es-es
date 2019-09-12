---
title: Compilar APK específicos de ABI
description: En este documento se describe cómo compilar un APK que será el destino de una única ABI mediante Xamarin.Android.
ms.prod: xamarin
ms.assetid: D21B195B-4530-4EB2-8704-5C4349A2CDD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 0c3bb547a21457a1666db5fe84560e10e3bb8eb1
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754273"
---
# <a name="building-abi-specific-apks"></a>Compilar APK específicos de ABI

_En este documento se describe cómo compilar un APK que será el destino de una única ABI mediante Xamarin.Android._

## <a name="overview"></a>Información general

En algunas situaciones, puede ser conveniente que una aplicación tenga varios APK; cada APK está firmado con el mismo almacén de claves y comparte el mismo nombre de paquete pero se compila para un dispositivo o una configuración de Android específicos. Este no es el enfoque recomendado: es mucho más sencillo tener un APK que pueda admitir varios dispositivos y configuraciones. Existen algunas situaciones en las que puede resultar útil crear varios APK, como por ejemplo:

- **Reducir el tamaño del APK**: Google Play impone un límite de tamaño de 100 MB en archivos APK. La creación de APK específicos del dispositivo puede reducir el tamaño del APK, dado que solo es necesario suministrar un subconjunto de activos y recursos para la aplicación.

- **Admitir diferentes arquitecturas de CPU**: si la aplicación ha compartido bibliotecas para CPU concretas, puede distribuir únicamente las bibliotecas compartidas para esa CPU.

Varios APK pueden complicar la distribución, un problema que soluciona Google Play. Google Play garantiza que se entrega el APK correcto a un dispositivo en función del código de versión de la aplicación y otros metadatos contenidos con **AndroidManifest.XML**. Para conocer de forma específica los detalles y las restricciones respecto a cómo Google Play admite varios APK para una aplicación, consulte la [documentación de Google sobre la compatibilidad con varios APK](https://developer.android.com/google/play/publishing/multiple-apks.html).

En esta guía se describe cómo crear un script para la compilación de varios APK para una aplicación Xamarin.Android, teniendo como destino cada APK una ABI específica. Se abarcan los siguientes temas:

1. Crear un *código de versión* único para el APK.
1. Crear una versión temporal de **AndroidManifest.XML** que se usará con este APK.
1. Compilar la aplicación mediante **AndroidManifest.XML** del paso anterior.
1. Preparar el APK para el lanzamiento mediante su firma y el uso de Zipalign.

Al final de esta guía encontrará un tutorial en el que se demuestra cómo crear scripts para estos pasos mediante [Rake](http://martinfowler.com/articles/rake.html).

### <a name="creating-the-version-code-for-the-apk"></a>Crear el código de la versión para el APK

Google recomienda un algoritmo determinado para el código de versión que usa un código de versión de siete dígitos; consulte la sección *Using a version code scheme* (Usar un esquema de código de versión) en el [documento sobre la compatibilidad con varios APK](https://developer.android.com/google/play/publishing/multiple-apks.html).
Al expandir este esquema de código de versión a ocho dígitos, es posible incluir alguna información de ABI en el código de versión que garantiza que Google Play distribuirá el APK correcto a un dispositivo. En la lista siguiente se explica este formato de código de versión de ocho dígitos (que se indexan de izquierda a derecha):

- **Índice 0** (rojo en el diagrama siguiente): un entero de la ABI:
  - 1 &ndash; `armeabi`
  - 2 &ndash; `armeabi-v7a`
  - 6 &ndash; `x86`

- **Índice 1-2** (naranja en el diagrama siguiente): el nivel de API mínimo admitido por la aplicación.

- **Índice 3-4** (azul en el diagrama siguiente): los tamaños de pantalla admitidos:
  - 1: pequeño
  - 2: normal
  - 3: grande
  - 4: extra grande

- **Índice 5-7** (verde en el diagrama siguiente): un número único para el código de versión. 
    Lo establece el desarrollador. Debería aumentar para cada versión pública de la aplicación.

En el siguiente diagrama se ilustra la posición de cada código descrito en la lista anterior:

[![Diagrama de formato de código de versión de ocho dígitos, codificado por color](abi-specific-apks-images/image00.png)](abi-specific-apks-images/image00.png#lightbox)

Google Play garantiza que se entrega el APK correcto al dispositivo en función del valor de `versionCode` y la configuración de APK. El APK con el código de versión más alto se entrega al dispositivo. Por ejemplo, una aplicación podría tener tres APK con los siguientes códigos de versión:

- 11413456: la ABI es `armeabi`; tiene como destino el nivel de API 14; pantallas de pequeña a grande; con un número de versión de 456.
- 21423456: la ABI es `armeabi-v7a`; tiene como destino el nivel de API 14; pantallas de normal a grande; con un número de versión de 456.
- 61423456: la ABI es `x86`; tiene como destino el nivel de API 14; pantallas de normal a grande; con un número de versión de 456.

Para continuar con este ejemplo, imagine que se corrigió un error que era específico de `armeabi-v7a`. La versión de la aplicación aumenta a 457 y se crea un nuevo APK con `android:versionCode` establecido en 21423457. Los códigos de versión para `armeabi` y las versiones `x86` permanecen igual.

Imagine ahora que la versión x86 recibe algunas actualizaciones o correcciones de errores que tienen como destino una API más reciente (nivel de API 19), de modo que se convierte en la versión 500 de la aplicación. El nuevo valor de `versionCode` cambiaría a 61923500 mientras que armeabi/armeabi-v7a permanecen sin cambios. En este momento, los códigos de versión serían:

- 11413456: la ABI es `armeabi`; tiene como destino el nivel de API 14; pantallas de pequeña a grande; con un número de versión de 456.
- 21423457: la ABI es `armeabi-v7a`; tiene como destino el nivel de API 14; pantallas de normal a grande; con un número de versión de 456.
- 61923500: la ABI es `x86`; tiene como destino el nivel de API 19; pantallas de normal a grande; con un número de versión de 500.

Mantener estos códigos de versión de forma manual puede ser una carga considerable para el desarrollador. El proceso de calcular el valor correcto de `android:versionCode` y luego generar el APK debería automatizarse.
En el tutorial al final de este documento, se incluye un ejemplo de cómo hacerlo.

### <a name="create-a-temporary-androidmanifestxml"></a>Crear un archivo AndroidManifest.XML temporal

Aunque no es estrictamente necesario, crear un archivo **AndroidManifest.XML** temporal para cada ABI puede ayudar a evitar problemas que podrían surgir debido a la pérdida de información de una APK a la otra. Por ejemplo, es fundamental que el atributo `android:versionCode` sea único para cada APK.

El modo en que se realice esto depende del sistema de scripts implicado, pero normalmente supone tomar una copia del manifiesto de Android usado durante el desarrollo, modificarlo y, luego, usar ese manifiesto modificado durante el proceso de compilación.

### <a name="compiling-the-apk"></a>Compilar el APK

La mejor manera de compilar el APK por ABI es usar `xbuild` o `msbuild`, como se muestra en la siguiente línea de comando de ejemplo:

```bash
/Library/Frameworks/Mono.framework/Commands/xbuild /t:Package /p:AndroidSupportedAbis=<TARGET_ABI> /p:IntermediateOutputPath=obj.<TARGET_ABI>/ /p:AndroidManifest=<PATH_TO_ANDROIDMANIFEST.XML> /p:OutputPath=bin.<TARGET_ABI> /p:Configuration=Release <CSPROJ FILE>
```

En la lista siguiente se explica cada parámetro de línea de comandos:

- `/t:Package`: crea un APK de Android que se firma con el almacén de claves de depuración.

- `/p:AndroidSupportedAbis=<TARGET_ABI>`: es la ABI para el destino. Debe ser `armeabi`, `armeabi-v7a` o `x86`.

- `/p:IntermediateOutputPath=obj.<TARGET_ABI>/`: es el directorio que contendrá los archivos intermedios que se crean como parte de la compilación. Si es necesario, Xamarin.Android creará un directorio con nombre después de la ABI, como `obj.armeabi-v7a`. Se recomienda usar una carpeta para cada ABI, ya que, de esta manera, se evitan problemas que pueden dar lugar a la pérdida de archivos entre una compilación y otra. Tenga en cuenta que este valor se termina con un separador de directorio (una `/` en el caso de OS X).

- `/p:AndroidManifest`: esta propiedad especifica la ruta de acceso al archivo **AndroidManifest.XML** que se usará durante la compilación.

- `/p:OutputPath=bin.<TARGET_ABI>`: es el directorio que albergará el APK final. Xamarin.Android creará un directorio con nombre después de la ABI, por ejemplo `bin.armeabi-v7a`.

- `/p:Configuration=Release`: realiza una compilación de versión del APK. Las compilaciones de depuración no se pueden cargar en Google Play.

- `<CS_PROJ FILE>`: es la ruta de acceso al archivo `.csproj` del proyecto de Xamarin.Android.

### <a name="sign-and-zipalign-the-apk"></a>Iniciar sesión y usar Zipalign con el APK

Es necesario firmar el APK antes de que se puede distribuir a través de Google Play. Para ello, se puede usar la aplicación `jarsigner` que forma parte del Kit para desarrolladores de Java. La siguiente línea de comandos demuestra cómo usar `jarsigner` en la línea de comandos:

```shell
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore <PATH/TO/KEYSTORE> -storepass <PASSWORD> -signedjar <PATH/FOR/SIGNED_JAR> <PATH/FOR/JAR/TO/SIGN> <NAME_OF_KEY_IN_KEYSTORE>
```

Se debe haber usado Zipaling en todas las aplicaciones de Xamarin.Android antes de que se puedan ejecutar en un dispositivo. Este es el formato de la línea de comandos que se usará:

```shell
zipalign -f -v 4 <SIGNED_APK_TO_ZIPALIGN> <PATH/TO/ZIP_ALIGNED.APK>
```

## <a name="automating-apk-creation-with-rake"></a>Automatizar la creación de APK con Rake

El proyecto de ejemplo [OneABIPerAPK](https://github.com/xamarin/monodroid-samples/tree/master/OneABIPerAPK) es un sencillo proyecto de Android que demuestra cómo calcular un número de versión específico de la ABI y compilar tres APK diferentes para cada una de las siguientes ABI:

- armeabi
- armeabi-v7a
- x86

El archivo [rakefile](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb) en el proyecto de ejemplo realiza cada uno de los pasos que se describieron en las secciones anteriores:

1. [Crea un elemento android:versionCode](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L30) para el APK.

1. [Escribe el elemento android:versionCode](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L55) en un archivo **AndroidManifest.XML** personalizado para ese APK.

1. [Compila una compilación de versión](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L63) del proyecto de Xamarin.Android que tendrá como destino particularmente la ABI y con el archivo **AndroidManifest.XML** que se creó en el paso anterior.

1. [Firma el APK](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L66) con un almacén de claves de producción.

1. Usa [Zipalign](https://github.com/xamarin/monodroid-samples/blob/master/OneABIPerAPK/Rakefile.rb#L67) con el APK.

Para compilar todos los APK de la aplicación, ejecute la tarea `build` de Rake desde la línea de comandos:

```shell
$ rake build
==> Building an APK for ABI armeabi with ./Properties/AndroidManifest.xml.armeabi, android:versionCode = 10814120.
==> Building an APK for ABI x86 with ./Properties/AndroidManifest.xml.x86, android:versionCode = 60814120.
==> Building an APK for ABI armeabi-v7a with ./Properties/AndroidManifest.xml.armeabi-v7a, android:versionCode = 20814120.
```

Una vez finalizada la tarea de Rake, habrá tres carpetas `bin` con el archivo `xamarin.helloworld.apk`. En la captura de pantalla siguiente se muestra cada una de estas carpetas con su contenido:

[![Ubicaciones de las carpetas específicas de la plataforma que contienen xamarin.helloworld.apk](abi-specific-apks-images/image01.png)](abi-specific-apks-images/image01.png#lightbox)

> [!NOTE]
> El proceso de compilación que se describe en esta guía se puede implementar en uno de muchos sistemas de compilación diferentes. Aunque no tenemos un ejemplo ya escrito, también debería ser posible con [Powershell](https://technet.microsoft.com/scriptcenter/powershell.aspx) / [psake](https://github.com/psake/psake) o [Fake](http://fsharp.github.io/FAKE/).

## <a name="summary"></a>Resumen

En esta guía se proporcionan algunas sugerencias con la creación del APK de Android que tienen como destino una ABI específica. También se examina un posible esquema para la creación de `android:versionCodes` que identificará la arquitectura de CPU destinada al APK. En el tutorial se incluye un proyecto de ejemplo que se ha compilado con scripts de Rake.

## <a name="related-links"></a>Vínculos relacionados

- [OneABIPerAPK (ejemplo)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/oneabiperapk)
- [Publicar una aplicación](~/android/deploy-test/publishing/index.md)
- [Multiple APK Support for Google Play](https://developer.android.com/google/play/publishing/multiple-apks.html) (Compatibilidad con varios APK en Google Play)
