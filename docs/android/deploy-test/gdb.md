---
title: GDB
ms.prod: xamarin
ms.assetid: CD0BE462-FA38-4881-B481-82AD05B3B8FE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: fe2cc5fb7c51425c8030d31015236473a5264efb
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509020"
---
# <a name="gdb"></a>GDB

## <a name="overview"></a>Información general

Xamarin.Android 4.10 introdujo la compatibilidad parcial con el uso de `gdb` gracias al destino de MSBuild `_Gdb`. 

> [!NOTE]
> Para la compatibilidad con `gdb` es necesario tener instalado Android NDK.

Existen tres maneras de usar `gdb`:

1.  [Compilaciones de depuración con implementación rápida habilitada](#Debug_Builds_with_Fast_Deployment).
1.  [Compilaciones de depuración con implementación rápida deshabilitada](#Debug_Builds_without_Fast_Deployment).
1.  [Compilaciones de versión](#Release_Builds)


Cuando haya algún problema, consulte la sección [Solución de problemas](#Troubleshooting).

<a name="Debug_Builds_with_Fast_Deployment" />

### <a name="debug-builds-with-fast-deployment"></a>Compilaciones de depuración con implementación rápida

Al compilar e implementar una compilación de depuración con implementación rápida habilitada, se puede asociar `gdb` mediante el destino de MSBuild `_Gdb`.

En primer lugar, instale la aplicación. Para ello, puede usar el IDE o la línea de comandos:

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:Install *.csproj
```

En segundo lugar, ejecute el destino `_Gdb`. Al final de la ejecución, se imprime una línea de comandos de `gdb`:

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
...
```

El destino `_Gdb` iniciará una actividad de iniciador declarada dentro del archivo `AndroidManifest.xml`. Para especificar explícitamente qué actividad ejecutar, use la propiedad de MSBuild `RunActivity`. En este momento no se admite el inicio de servicios y otras construcciones de Android.

El destino `_Gdb` creará un directorio `gdb-symbols` y copiará ahí el contenido de los directorios `/system/lib` y `$APPDIR/lib` del destino.


> [!NOTE]
> El contenido del directorio `gdb-symbols` está vinculado al destino de Android en el que realizó la implementación, y no se reemplazará automáticamente si cambia el directorio. (Puede considerarlo un error). Si cambia los dispositivos de destino de Android, deberá eliminar manualmente este directorio.

Por último, copie el comando `gdb` generado y ejecútelo en el shell:

```bash
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) bt
#0  0x40082e84 in nanosleep () from /Users/jon/Development/Projects/Scratch.HelloXamarin20/gdb-symbols/libc.so
#1  0x4008ffe6 in sleep () from /Users/jon/Development/Projects/Scratch.HelloXamarin20/gdb-symbols/libc.so
#2  0x74e46240 in ?? ()
#3  0x74e46240 in ?? ()
(gdb) c
```

<a name="Debug_Builds_without_Fast_Deployment" />

## <a name="debug-builds-without-fast-deployment"></a>Compilaciones de depuración sin implementación rápida

Las compilaciones de depuración *con* implementación rápida funcionan mediante la copia del programa `gdbserver` de Android NDK en el directorio `.__override__` de implementación rápida. Si la implementación rápida está deshabilitada, puede que este directorio no exista.

Hay dos soluciones alternativas:

-   Establecer la propiedad del sistema `debug.mono.log` para que se cree el directorio `.__override__`.
-   Incluya `gdbserver` dentro de `.apk`.

### <a name="setting-the-debugmonolog-system-property"></a>Establecer la propiedad del sistema `debug.mono.log`

Para establecer la propiedad del sistema `debug.mono.log`, use el comando `adb`:

```bash
$ adb shell setprop debug.mono.log gc
```

Una vez que se ha establecido la propiedad del sistema, ejecute el destino `_Gdb` y el comando `gdb` impreso, lo mismo que hizo con la configuración de compilaciones de depuración con implementación rápida:

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
  ...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
  ...
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) c
```


### <a name="including-gdbserver-in-your-app"></a>Incluir `gdbserver` en la aplicación

Para incluir `gdbserver` dentro de la aplicación:

1. Busque `gdbserver` en su instancia de Android NDK (debe estar en **$ANDROID\_NDK\_PATH/prebuilt/android-arm/gdbserver/gdbserver**) y cópielo en el directorio del proyecto.

2. Cambie el nombre de `gdbserver` por **libs/armeabi-v7a/libgdbserver.so**.

3. Agregue **libs/armeabi-v7a/libgdbserver.so** al proyecto con una **acción de compilación** de `AndroidNativeLibrary`.

4. Vuelva a compilar y a instalar la aplicación.

Una vez que la aplicación se ha instalado de nuevo, ejecute el destino `_Gdb` y el comando `gdb` impreso, lo mismo que hizo con la configuración de compilaciones de depuración con implementación rápida:

```bash
$ /Library/Frameworks/Mono.framework/Commands/xbuild /t:_Gdb *.csproj
  ...
    Target _Gdb:
        "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
  ...
$ "/opt/android/ndk/toolchains/arm-linux-androideabi-4.4.3/prebuilt/darwin-x86/bin/arm-linux-androideabi-gdb" -x "/Users/jon/Development/Projects/Scratch.HelloXamarin20//gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
...
(gdb) c
```

<a name="Release_Builds" />

## <a name="release-builds"></a>Versiones de lanzamiento

Para la compatibilidad con `gdb` son necesarias tres cosas:

1.  El permiso de `INTERNET`.
2.  Tener habilitada la depuración de aplicaciones.
3.  Un elemento `gdbserver` accesible.

El permiso de `INTERNET` está habilitado de forma predeterminada en las aplicaciones de depuración. Si no existe aún en la aplicación, puede agregarlo; para ello, edite **Properties/AndroidManifest.xml** o las [propiedades del proyecto](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest).

Para habilitar la depuración de aplicaciones, se puede definir la propiedad de atributo personalizada [ApplicationAttribute.Debugging](xref:Android.App.ApplicationAttribute.Debuggable) como `true`, o se puede editar **Properties/AndroidManifest.xml** y definir el atributo `//application/@android:debuggable` como `true`:

```xml
<application android:label="Example.Name.Here" android:debuggable="true">
```

Para proporcionar un elemento `gdbserver` accesible, siga la sección [Compilaciones de depuración sin implementación rápida](#Debug_Builds_without_Fast_Deployment).

Un aspecto a tener en cuenta: el destino de MSBuild `_Gdb` terminará todas las instancias de aplicación que estaban en ejecución. Esto no funciona en destinos de Android v4.0.

<a name="Troubleshooting" />

## <a name="troubleshooting"></a>Solución de problemas

### <a name="monopmip-doesnt-work"></a>`mono_pmip` no funciona

La función `mono_pmip` (útil para [obtener marcos de pila administrados](https://www.mono-project.com/docs/debug+profile/debug/#debugging-with-gdb)) se exporta desde `libmonosgen-2.0.so`, que actualmente el destino `_Gdb` no puede extraer. (Este problema se corregirá en futuras versiones).

Para habilitar las funciones de llamada ubicadas en `libmonosgen-2.0.so`, cópielas del dispositivo de destino al directorio `gdb-symbols`:

```bash
$ adb pull /data/data/Mono.Android.DebugRuntime/lib/libmonosgen-2.0.so Project/gdb-symbols
```

A continuación, reinicie la sesión de depuración.

### <a name="bus-error-10-when-running-the-gdb-command"></a>Error de bus: 10 al ejecutar el comando `gdb`

Cuando el comando `gdb` produce el error `"Bus error: 10"`, reinicie el dispositivo Android.

```bash
$ "/path/to/arm-linux-androideabi-gdb" -x "Project/gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
...
Bus error: 10
$
```

### <a name="no-stack-trace-after-attach"></a>Ningún seguimiento de pila después de la asociación

```bash
$ "/path/to/arm-linux-androideabi-gdb" -x "Project/gdb-symbols/gdb.env"
GNU gdb (GDB) 7.3.1-gg2
Copyright (C) 2011 Free Software Foundation, Inc.
...
(gdb) bt
No stack.
```

Este problema suele ser un indicio de que el contenido del directorio `gdb-symbols` no está sincronizado con el destino de Android. (¿Cambió su destino de Android?)

Elimine el directorio `gdb-symbols` e inténtelo de nuevo.
