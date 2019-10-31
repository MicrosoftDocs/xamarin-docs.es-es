---
title: Dispositivos de varios núcleos y Xamarin.Android
description: Android puede ejecutarse en varias arquitecturas de equipo diferentes. En este documento se describen las distintas arquitecturas de CPU que se pueden emplear con una aplicación Xamarin.Android. También se explica cómo las aplicaciones Android se empaquetan para admitir distintas arquitecturas de CPU. Se introducirá la Interfaz binaria de aplicación (ABI) y se proporcionarán instrucciones respecto a cuáles ABI usar en una aplicación Xamarin.Android.
ms.prod: xamarin
ms.assetid: D812883C-A14A-E74B-0F72-E50071E96328
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/30/2019
ms.openlocfilehash: 1141b96151df0adda755b7c6d60019c18825cc76
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028013"
---
# <a name="multi-core-devices--xamarinandroid"></a>Dispositivos de varios núcleos y Xamarin.Android

_Android puede ejecutarse en varias arquitecturas de equipo diferentes. En este documento se describen las distintas arquitecturas de CPU que se pueden emplear con una aplicación Xamarin.Android. También se explica cómo las aplicaciones Android se empaquetan para admitir distintas arquitecturas de CPU. Se introducirá la Interfaz binaria de aplicación (ABI) y se proporcionarán instrucciones respecto a cuáles ABI usar en una aplicación Xamarin.Android._

## <a name="overview"></a>Información general

Android permite la creación de "fat binaries" (también conocidos como binarios multiarquitectura), un único archivo `.apk` que contiene código máquina que admitirá varias arquitecturas diferentes de CPU. Para ello, cada trozo de código máquina se asocia con una *Interfaz binaria de aplicación*. La ABI se usa para controlar qué código máquina se ejecutará en un dispositivo de hardware determinado. Por ejemplo, para que una aplicación Android se ejecute en un dispositivo x86, es necesario incluir compatibilidad con la ABI x86 al compilar la aplicación.

En concreto, cada aplicación Android admitirá al menos una *interfaz binaria de aplicación insertada* (EABI). Las EABI son convenciones específicas de programas de software insertados. Por lo general, una EABI describe cosas como:

- El conjunto de instrucciones de CPU.

- El orden secuencial (endianness) en que la memoria se almacena y carga en tiempo de ejecución.

- El formato binario de archivos de objetos y bibliotecas de programa, así como el tipo de contenido que se permite o admite en estos archivos y bibliotecas.

- Las diversas convenciones usadas para pasar datos entre el código de aplicación y el sistema (por ejemplo, cómo se usan los registros o la pila al llamar a funciones, las restricciones de alineación, etc.)

- Restricciones de tamaño y alineación para tipos de enumeración, estructuras, campos y matrices.

- La lista de símbolos de función disponibles para el código máquina en tiempo de ejecución, en general desde un conjunto de bibliotecas muy específico seleccionado.

### <a name="armeabi-and-thread-safety"></a>armeabi y la seguridad para subprocesos

A continuación se examina en detalle la Interfaz binaria de aplicación, pero es importante recordar que el entorno de tiempo de ejecución de `armeabi` que se usa en Xamarin.Android *no es seguro para subprocesos*. Si una aplicación que tiene compatibilidad con `armeabi` se implementa en un dispositivo `armeabi-v7a`, se producirán muchas excepciones raras e inexplicables.

Debido a un error en Android 4.0.0, 4.0.1, 4.0.2 y 4.0.3, las bibliotecas nativas se seleccionarán del directorio `armeabi` aun en el caso de que haya un directorio `armeabi-v7a` y el dispositivo sea un dispositivo `armeabi-v7a`.

> [!NOTE]
> Xamarin.Android garantiza que `.so` se agrega al APK en el orden correcto. Este error no debería ser un problema para los usuarios de Xamarin.Android.

### <a name="abi-descriptions"></a>Descripciones de ABI

Cada ABI compatible con Android se identifica mediante un nombre único.

#### <a name="armeabi"></a>armeabi

Este es el nombre de una EABI para CPU basadas en ARM que admiten al menos el conjunto de instrucciones ARMv5TE. Android sigue la ABI GNU/Linux de ARM little endian. Esta ABI no es compatible con cálculos de punto flotante asistidos por hardware. Todas las operaciones de FP se realizan mediante funciones del asistente de software que proceden de la biblioteca estática `libgcc.a` del compilador. Los dispositivos SMP no admiten `armeabi`.

> [!IMPORTANT]
> El código `armeabi` de Xamarin.Android no es seguro para subprocesos y no se debe usar en dispositivos `armeabi-v7a` de varios núcleos (se describen a continuación). El uso de código `armeabi` en un dispositivo `armeabi-v7a` de un único núcleo es seguro.

#### <a name="armeabi-v7a"></a>armeabi-v7a

Se trata de otro conjunto de instrucciones de CPU basado en ARM que amplía la EABI `armeabi` descrita anteriormente. La EABI `armeabi-v7a` presenta compatibilidad con operaciones de punto flotante de hardware y varios dispositivos de CPU (SMP). Las aplicaciones que usan la EABI `armeabi-v7a` pueden esperar considerables mejoras en el rendimiento por encima de las aplicaciones que usan `armeabi`.

> [!NOTE]
> El código máquina `armeabi-v7a` no se ejecuta en dispositivos ARMv5.

#### <a name="arm64-v8a"></a>arm64-v8a

Se trata de un conjunto de instrucciones de 64 bits que se basa en la arquitectura de CPU de ARMv8. Esta arquitectura se usa en *Nexus 9*.
Xamarin.Android 5.1 ha introducido soporte técnico para esta arquitectura (para más información, consulte [64-bit runtime support](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#64-bit-runtime-support) [Soporte técnico del entorno de ejecución de 64 bits]).

#### <a name="x86"></a>x86

Este es el nombre de una ABI para CPU que admiten el conjunto de instrucciones conocido comúnmente como *x86* o *IA-32*. Esta ABI corresponde a las instrucciones del conjunto de instrucciones Pentium Pro, que incluye los conjuntos de instrucciones MMX, SSE, SSE2 y SSE3. No incluye ninguna otra extensión opcional de conjunto de instrucciones IA-32 como:

- La instrucción MOVBE.
- La extensión SSE3 complementaria (SSSE3).
- Cualquier variante de SSE4.

> [!NOTE]
> Aunque se ejecuta en x86, Google TV no es compatible con el NDK de Android.

#### <a name="x86_64"></a>x86_64

Este es el nombre de una ABI para CPU que admiten el conjunto de instrucciones x86 de 64 bits (también conocido como *x64* o *AMD64*). Xamarin.Android 5.1 ha introducido soporte técnico para esta arquitectura (para más información, consulte [64-bit runtime support](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#64-bit-runtime-support) [Soporte técnico del entorno de ejecución de 64 bits]).

#### <a name="apk-file-format"></a>Formato de archivo de APK

El paquete de aplicaciones Android es el formato de archivo que contiene todo el código, los activos, los recursos y los certificados necesarios para una aplicación Android. Es un archivo `.zip`, pero usa la extensión de nombre de archivo `.apk`. Cuando se expande, se puede ver el contenido de un archivo `.apk` creado en Xamarin.Android en la captura de pantalla siguiente:

[![Contenido del archivo .apk](multicore-devices-images/00.png)](multicore-devices-images/00.png#lightbox)

Una descripción rápida del contenido del archivo `.apk`:

- **AndroidManifest.xml**&ndash; este es el archivo `AndroidManifest.xml`, en formato XML binario.

- **classes.dex**&ndash; contiene el código de la aplicación, compilado en el formato de archivo `dex` que usa la máquina virtual en tiempo de ejecución de Android.

- **resources.arsc**&ndash; este archivo contiene todos los recursos precompilados de la aplicación.

- **lib**: este directorio contiene el código compilado de cada ABI. Contendrá una subcarpeta para cada ABI que se ha descrito en la sección anterior. En la captura de pantalla anterior, el archivo `.apk` en cuestión tiene bibliotecas nativas para `armeabi-v7a` y para `x86`.

- **META-INF**&ndash; este directorio (si existe) se usa para almacenar información de firma, paquetes y datos de configuración de extensión.

- **res**&ndash; este directorio contiene los recursos que no se compilaron en `resources.arsc`.

> [!NOTE]
> El archivo `libmonodroid.so` es la biblioteca nativa que necesitan todas las aplicaciones Xamarin.Android.

#### <a name="android-device-abi-support"></a>Compatibilidad con ABI en dispositivos Android

Cada dispositivo Android admite la ejecución de código nativo en hasta dos ABI:

- **La ABI "principal"** &ndash; corresponde al código máquina usado en la imagen del sistema.

- **Una ABI "secundaria"** &ndash; una ABI opcional que también es compatible con la imagen del sistema.

Por ejemplo, normalmente un dispositivo ARMv5TE solo tendrá una ABI principal de `armeabi`, mientras que un dispositivo ARMv7 especificaría una ABI principal de `armeabi-v7a` y una ABI secundaria de `armeabi`. Normalmente, un dispositivo x86 solo debería especificar una ABI principal de `x86`.

### <a name="android-native-library-installation"></a>Instalación de bibliotecas nativas de Android

En el momento de la instalación del paquete, las bibliotecas nativas que contiene el archivo `.apk` se extraen en el directorio de biblioteca nativa de la aplicación, normalmente `/data/data/<package-name>/lib` y, por tanto, se conocen como `$APP/lib`.

El comportamiento de la instalación de bibliotecas nativas de Android varía considerablemente según la versión de Android.

#### <a name="installing-native-libraries-pre-android-40"></a>Instalación de bibliotecas nativas: Versiones anteriores a Android 4.0

Las versiones de Android anteriores a 4.0 Ice Cream Sandwich solo extraían las bibliotecas nativas de una *única ABI* dentro del archivo `.apk`. Las aplicaciones Android de esta hornada primero intentan extraer todas las bibliotecas nativas de la ABI principal, y si no están ahí, de la ABI secundaria. No se realiza ninguna combinación.

Por ejemplo, considere una situación donde una aplicación está instalada en un dispositivo `armeabi-v7a`. El archivo `.apk,` que admite `armeabi` y `armeabi-v7a`, contiene los siguientes directorios y archivos `lib` de ABI:

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

Después de la instalación, el directorio de bibliotecas nativas contendrá:

```shell
$APP/lib/libtwo.so # from the armeabi-v7a directory in the apk
```

En otras palabras, no se instala ningún archivo `libone.so`. Como consecuencia, se producen problemas, dado que `libone.so` no existe para que la aplicación se cargue en tiempo de ejecución. Este comportamiento, aunque inesperado, se ha registrado como un error y se vuelve a clasificar como que [funciona según lo esperado](https://code.google.com/p/android/issues/detail?id=9089).

Por lo tanto, cuando el destino son versiones de Android anteriores a la 4.0, es necesario proporcionar *todas* las bibliotecas nativas para *cada* ABI que admita la aplicación, es decir, el archivo `.apk` debe contener:

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libone.so
lib/armeabi-v7a/libtwo.so
```

#### <a name="installing-native-libraries-android-40-ndash-android-403"></a>Instalación de bibliotecas nativas: Android 4.0 &ndash; Android 4.0.3

Android 4.0 Ice Cream Sandwich cambia la lógica de extracción. Enumera todas las bibliotecas nativas, comprueba si el nombre base del archivo ya se ha extraído y, si se cumplen las dos siguientes condiciones, se extrae la biblioteca:

- Aún no se ha extraído.

- La ABI de la biblioteca nativa corresponde a la ABI principal o secundaria del destino.

Al cumplirse estas dos condiciones, es posible un comportamiento de "combinación"; es decir, si tenemos un archivo `.apk` con el siguiente contenido:

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

Tras la instalación, el directorio de biblioteca nativa contendrá:

```shell
$APP/lib/libone.so
$APP/lib/libtwo.so
```

Lamentablemente, este comportamiento depende del orden, tal como se describe en el documento [Problema 24321: Galaxy Nexus 4.0.2 usa código nativo de armeabi cuando armeabi y armeabi-v7a se incluyen en el apk](https://code.google.com/p/android/issues/detail?id=25321).

Las bibliotecas nativas se procesan "en orden" (como se enumeran al descomprimir, por ejemplo) y se extrae la *primera coincidencia*. Dado que el archivo `.apk` contiene las versiones `armeabi` y `armeabi-v7a` de `libtwo.so`, y `armeabi` se muestra en primer lugar, la versión que se extrae es `armeabi` y *no* la versión `armeabi-v7a`:

```shell
$APP/lib/libone.so # armeabi
$APP/lib/libtwo.so # armeabi, NOT armeabi-v7a!
```

Además, incluso si se especifican ambas ABI ,`armeabi` y `armeabi-v7a`, (como se describe a continuación en la sección *Declaring Supported ABIs* [Declaración de ABI admitidas]), Xamarin.Android creará el siguiente elemento en .
`csproj`:

```xml
<AndroidSupportedAbis>armeabi,armeabi-v7a</AndroidSupportedAbis>
```

Como consecuencia, `armeabi` `libmonodroid.so` se encontrará primero en el archivo `.apk` y `armeabi` `libmonodroid.so` será el que se extraiga, aunque `armeabi-v7a` `libmonodroid.so` exista y esté optimizado para el destino. Esta situación puede dar también lugar a extraños errores en tiempo de ejecución, dado que `armeabi` no es seguro para SMP.

##### <a name="installing-native-libraries-android-404-and-later"></a>Instalación de bibliotecas nativas: Android 4.0.4 y versiones posteriores

Android 4.0.4 cambia la lógica de extracción: se enumeran todas las bibliotecas nativas, se lee el nombre base del archivo y luego se extrae la versión de la API principal o la ABI secundaria (la que sea que exista). Esto permite un comportamiento de "combinación"; es decir, si tenemos un archivo `.apk` con el siguiente contenido:

```shell
lib/armeabi/libone.so
lib/armeabi/libtwo.so
lib/armeabi-v7a/libtwo.so
```

Tras la instalación, el directorio de biblioteca nativa contendrá:

```shell
$APP/lib/libone.so # from armeabi
$APP/lib/libtwo.so # from armeabi-v7a
```

### <a name="xamarinandroid-and-abis"></a>Xamarin.Android y las ABI

Xamarin.Android admite las siguientes arquitecturas de _64 bits_:

- `arm64-v8a`
- `x86_64`

> [!NOTE]
> A partir de agosto de 2018 se necesitarán nuevas aplicaciones para alcanzar el nivel 26 de la API, y a partir de agosto de 2019 [se requerirá que las aplicaciones proporcionen versiones de 64 bits](https://android-developers.googleblog.com/2017/12/improving-app-security-and-performance.html) además de la versión de 32 bits.

Xamarin.Android admite estas arquitecturas de 32 bits:

- `armeabi` ^
- `armeabi-v7a`
- `x86`

> [!NOTE]
> **^** Desde [Xamarin.Android 9.2](https://docs.microsoft.com/xamarin/android/release-notes/9/9.2#removal-of-support-for-armeabi-cpu-architecture), `armeabi` ya no se admite.

Xamarin.Android no proporciona actualmente compatibilidad con `mips`.

### <a name="declaring-supported-abis"></a>Declaración de las ABI admitidas

De forma predeterminada, Xamarin.Android adopta `armeabi-v7a` para las compilaciones de **versión** y `armeabi-v7a` y `x86` para las compilaciones de **depuración**. La compatibilidad con distintas ABI se puede establecer mediante las opciones de proyecto de un proyecto de Xamarin.Android. En Visual Studio, esto se puede establecer en la página **Opciones de Android** de **Propiedades** del proyecto, en la pestaña **Avanzadas**, tal y como se muestra en la siguiente captura de pantalla:

![Propiedades avanzadas de Opciones de Android](multicore-devices-images/vs-abi-selections.png)

En Visual Studio para Mac, las arquitecturas admitidas se pueden seleccionar en la página **Compilación de Android** de **Opciones del proyecto**, en la pestaña **Avanzadas**, como se muestra en la siguiente captura de pantalla:

[![ABI admitidas en Compilación de Android](multicore-devices-images/xs-abi-selections-sml.png)](multicore-devices-images/xs-abi-selections.png#lightbox)

Existen algunas situaciones en las que puede ser necesario declarar compatibilidad adicional con ABI, por ejemplo:

- Al implementar la aplicación en un dispositivo `x86`.

- Al implementar la aplicación en un dispositivo `armeabi-v7a` para garantizar la seguridad para subprocesos.

## <a name="summary"></a>Resumen

En este documento se tratan las distintas arquitecturas de CPU en las que se puede ejecutar una aplicación Android. Se introduce la Interfaz binaria de aplicación y cómo se usa en Android para admitir arquitecturas de CPU dispares.
Luego, se pasa a describir cómo especificar la compatibilidad con ABI en una aplicación Xamarin.Android y se resaltan los problemas que surgen al utilizar aplicaciones Xamarin.Android en un dispositivo `armeabi-v7a` que está pensado solo para `armeabi`.

## <a name="related-links"></a>Vínculos relacionados

- [ABI para la arquitectura ARM (PDF)](http://infocenter.arm.com/help/topic/com.arm.doc.ihi0036b/IHI0036B_bsabi.pdf)
- [NDK de Android](https://developer.android.com/tools/sdk/ndk/index.html)
- [Problema 9089: Nexus One no carga ninguna biblioteca nativa de armeabi si hay al menos una biblioteca en armeabi-v7a](https://code.google.com/p/android/issues/detail?id=9089)
- [Problema 24321: Galaxy Nexus 4.0.2 usa código nativo de armeabi cuando armeabi y armeabi-v7a se incluyen en el apk](https://code.google.com/p/android/issues/detail?id=25321).
