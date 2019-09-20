---
title: Entorno de Xamarin.Android
ms.prod: xamarin
ms.assetid: 67BFD4E1-276C-4B9F-9BD8-A5218D2BD529
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: 076e6bfd1155c0eba4045af59a599a34b0fca1d5
ms.sourcegitcommit: 13e43f510da37ad55f1c2f5de1913fb0aede6362
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/16/2019
ms.locfileid: "71021181"
---
# <a name="xamarinandroid-environment"></a>Entorno de Xamarin.Android

## <a name="execution-environment"></a>Entorno de ejecución

El *entorno de ejecución* es el conjunto de variables de entorno y las propiedades del sistema de Android que influyen en la ejecución del programa. Las propiedades del sistema de Android se pueden establecer con el comando `adb shell setprop`, mientras que las variables de entorno se pueden establecer mediante la propiedad del sistema `debug.mono.env`:

```shell
## Enable GREF logging
adb shell setprop debug.mono.log gref

## Set the MONO_LOG_LEVEL and MONO_LOG_MASK environment variables
## so that additional Mono messages will be written to `adb logcat`.
adb shell setprop debug.mono.env "'MONO_LOG_LEVEL=info|MONO_LOG_MASK=asm'"
```

Las propiedades del sistema de Android se establecen para todos los procesos del dispositivo de destino.

A partir de Xamarin.Android 4.6, las propiedades del sistema y las variables de entorno se pueden establecer o invalidar por cada aplicación agregando un *archivo de entorno* al proyecto. Un archivo de entorno es un archivo de texto sin formato Unix con una [**acción de compilación** de `AndroidEnvironment`](~/android/deploy-test/building-apps/build-process.md).
El archivo de entorno contiene líneas con el formato *clave=valor*.
Los comentarios son líneas que comienzan por `#`. Las líneas en blanco se omiten.

Si la *clave* comienza con una letra mayúscula, entonces se trata como una variable de entorno y **setenv**(3) se usa para establecer la variable de entorno en el *valor* especificado durante el inicio del proceso.

Si la *clave* comienza con una letra minúscula, se trata como una propiedad del sistema Android y el *valor* es el *valor predeterminado*: las propiedades del sistema de Android que controlan el comportamiento de ejecución de Xamarin.Android se buscan primero en el almacén de propiedades del sistema de Android y, si no existe ningún valor, se usa el valor especificado en el archivo de entorno. El objetivo es permitir que se use `adb shell setprop` para invalidar los valores que proceden del archivo de entorno con fines de diagnóstico.

## <a name="xamarinandroid-environment-variables"></a>Variables de entorno de Xamarin.Android

Xamarin.Android admite la variable `XA_HTTP_CLIENT_HANDLER_TYPE`, que se puede establecer mediante `adb shell setprop debug.mono.env` o por medio de la acción de compilación `$(AndroidEnvironment)`.

### `XA_HTTP_CLIENT_HANDLER_TYPE`

El tipo completo de ensamblado que debe heredar de [HttpMessageHandler](https://docs.microsoft.com/dotnet/api/system.net.http.httpmessagehandler?view=xamarinandroid-7.1) y que se construye a partir de la [construcción predeterminada`HttpClient()`](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient.-ctor?view=xamarinandroid-7.1#System_Net_Http_HttpClient__ctor).

En Xamarin.Android 6.1, esta variable de entorno no se establece de forma predeterminada, y se usa [HttpClientHandler](https://docs.microsoft.com/dotnet/api/system.net.http.httpclienthandler?view=xamarinandroid-7.1).

Como alternativa, puede especificarse el valor `Xamarin.Android.Net.AndroidClientHandler` para usar [`java.net.URLConnection`](xref:Java.Net.URLConnection)
para el acceso a la red, que *puede* permitir el uso de TLS 1.2 cuando Android lo admita.

Agregado en Xamarin.Android 6.1.

## <a name="xamarinandroid-system-properties"></a>Propiedades del sistema de Xamarin.Android

Xamarin.Android admite las siguientes propiedades del sistema, que se pueden establecer mediante `adb shell setprop` o por medio de la acción de compilación `$(AndroidEnvironment)`.

- `debug.mono.debug`
- `debug.mono.env`
- `debug.mono.gc`
- `debug.mono.log`
- `debug.mono.max_grefc`
- `debug.mono.profile`
- `debug.mono.runtime_args`
- `debug.mono.trace`
- `debug.mono.wref`
- `XA_HTTP_CLIENT_HANDLER_TYPE`

### `debug.mono.debug`

El valor de la propiedad del sistema `debug.mono.debug` es un entero. Si `1`, se comporta "como si" el proceso se hubiera iniciado con `mono --debug`.
Por lo general, se muestra la información de archivo y línea en seguimientos de pila, etc., sin necesidad de que la aplicación se inicie desde un depurador.

### `debug.mono.env`

Contiene una lista de variables de entorno separada por `|`.

### `debug.mono.gc`

El valor de la propiedad del sistema `debug.mono.debug` es un entero.
Si `1`, se debe registrar entonces la información de GC.

Esto equivale a hacer que la propiedad del sistema `debug.mono.log` contenga `gc`.

### `debug.mono.log`

Controla qué información adicional registrará Xamarin.Android en `adb logcat`.
Es una cadena separada por comas (`,`), que contiene uno de los siguientes valores:

- `all`: imprimir *todos* los mensajes. En raras ocasiones es una buena idea, ya que incluye mensajes `lref`.
- `assembly`: imprimir `.apk` y ensamblar los mensajes de análisis.
- `gc`: imprimir los mensajes relacionados con GC.
- `gref`: imprimir los mensajes de referencia global de JNI.
- `lref`: imprimir los mensajes de referencia local de JNI.
  > [!NOTE]
  > Este valor *en realidad* enviará correo basura a `adb logcat`.
  > En Xamarin.Android 5.1, también creará un archivo `.__override__/lrefs.txt`, que puede volverse *gigantesco*.
  > Evítelo.
- `timing`: imprimir alguna información de tiempo de método. Este valor también creará los archivos `.__override__/methods.txt` y `.__override__/counters.txt`.

### `debug.mono.max_grefc`

El valor de la propiedad del sistema `debug.mono.max_grefc` es un entero.
Su valor *invalida* el recuento de GREF máximo detectado para el dispositivo de destino.

*Tenga en cuenta lo siguiente:* Solo se puede usar con `adb shell setprop
debug.mono.max_grefc`, ya que el valor no estará disponible a tiempo con un archivo **environment.txt**.

### `debug.mono.profile`

La propiedad del sistema `debug.mono.profile` habilita el generador de perfiles.
Es equivalente a la opción `mono --profile`, y utiliza sus mismos valores. (Para más información, consulte la página man [**mono**(1)](http://docs.go-mono.com/?link=man%3amono(1))).

### `debug.mono.runtime_args`

La propiedad del sistema `debug.mono.runtime_args` contiene opciones adicionales que se deben analizar mediante **mono**.

### `debug.mono.trace`

La propiedad del sistema `debug.mono.trace` permite el seguimiento.
Es equivalente a la opción `mono --trace`, y utiliza sus mismos valores. (Para más información, consulte la página man [**mono**(1)](http://docs.go-mono.com/?link=man%3amono(1))).

En general, *no la utilice*. El uso de seguimiento enviará correo basura a la salida de `adb logcat`, ralentizará gravemente el comportamiento del programa y modificará su comportamiento (hasta agregar condiciones de errores adicionales).

Sin embargo, *en ocasiones*, permite realizar cierta investigación adicional...

### `debug.mono.wref`

La propiedad del sistema `debug.mono.wref` permite invalidar el mecanismo de referencia débil de JNI detectado predeterminado. Hay dos valores admitidos:

- `jni`: se usan referencias débiles de JNI, creadas por `JNIEnv::NewWeakGlobalRef()` y destruidas por `JNIEnv::DeleteWeakGlobalREf()`.
- `java`: se usan referencias globales de JNI con instancias `java.lang.WeakReference` de referencia.

De forma predeterminada, se usa `java`, hasta API-7 y en API-19 (Kit Kat) con ART habilitado. (API-8 agregó referencias de `jni` y ART *interrumpe* las referencias de `jni`).

Esta propiedad del sistema es útil para pruebas y determinadas formas de investigación.
*En general*, no se debe cambiar.

### <a name="xa_http_client_handler_type"></a>XA\_HTTP\_CLIENT\_HANDLER\_TYPE

Introducida primero en Xamarin.Android 6.1, esta variable de entorno declara la implementación de `HttpMessageHandler` predeterminada que usará `HttpClient`. Esta variable no se establece de forma predeterminada, y Xamarin.Android usará `HttpClientHandler`.

```shell
XA_HTTP_CLIENT_HANDLER_TYPE=Xamarin.Android.Net.AndroidClientHandler
```

> [!NOTE]
> El dispositivo Android subyacente debe admitir TLS 1.2.
Android 5.0 y versiones posteriores admiten TLS 1.2

## <a name="example"></a>Ejemplo

```shell
## Comments are lines which start with '#'
## Blank lines are ignored.

## Enable GREF messages to `adb logcat`
debug.mono.log=gref

## Clear out a Mono environment variable to decrease logging
MONO_LOG_LEVEL=
```

## <a name="related-links"></a>Vínculos relacionados

- [Seguridad de la capa de transporte](~/cross-platform/app-fundamentals/transport-layer-security.md)
