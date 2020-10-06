---
title: Compilar destinos
description: En este documento se muestran todos los destinos admitidos en el proceso de compilación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 17DE89FF-F316-4620-B865-EF6E0963A29C
ms.technology: xamarin-android
author: jonpryor
ms.author: jopryo
ms.date: 09/17/2020
ms.openlocfilehash: 4482e6c4bfe2a6952d59d896b7c79ca82432b42b
ms.sourcegitcommit: 38496cfd4d30fd40a011011f303a31de639bd699
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2020
ms.locfileid: "91247244"
---
# <a name="build-targets"></a>Compilar destinos

Los destinos de compilación siguientes se definen para proyectos de Xamarin.Android.

## <a name="build"></a>Compilar

Compila el código fuente en un proyecto y en todas las dependencias.

Este destino *no* crea un paquete de Android (archivo `.apk`).
Para crear un paquete de Android, use el destino [SignAndroidPackage](#signandroidpackage) *o* establezca la propiedad [`$(AndroidBuildApplicationPackage)](~/android/deploy-test/building-apps/build-properties.md#androidbuildapplicationpackage) en True al compilar:

```shell
msbuild /p:AndroidBuildApplicationPackage=True App.sln
```

## <a name="buildandstartaotprofiling"></a>BuildAndStartAotProfiling

Compila la aplicación con un generador de perfiles de AOT insertado, establece el puerto TCP del generador de perfiles en [`$(AndroidAotProfilerPort)`](~/android/deploy-test/building-apps/build-properties.md#androidaotprofilerport) e inicia la actividad predeterminada.

El puerto TCP predeterminado es `9999`.

Se ha agregado en Xamarin.Android 10.2.

## <a name="clean"></a>Clean

Elimina todos los archivos que genera el proceso de compilación.

## <a name="finishaotprofiling"></a>FinishAotProfiling

Se le debe llamar *después* del destino [BuildAndStartAotProfiling](#buildandstartaotprofiling).

Recopila los datos del generador de perfiles de AOT del dispositivo o emulador mediante el puerto TCP [`$(AndroidAotProfilerPort)`](~/android/deploy-test/building-apps/build-properties.md#androidaotprofilerport)
y los escribe en [`$(AndroidAotCustomProfilePath)`](~/android/deploy-test/building-apps/build-properties.md#androidaotcustomprofilepath).

Los valores predeterminados para el puerto y el perfil personalizado son `9999` y `custom.aprof`.

Para pasar opciones adicionales a `aprofutil`, establézcalas en [`$(AProfUtilExtraOptions)`](~/android/deploy-test/building-apps/build-properties.md#aprofutilextraoptions).
propiedad.

Esto equivale a:

```shell
aprofutil $(AProfUtilExtraOptions) -s -v -f -p $(AndroidAotProfilerPort) -o "$(AndroidAotCustomProfilePath)"
```

Se ha agregado en Xamarin.Android 10.2.

## <a name="install"></a>Instalar

[Crea, firma](#signandroidpackage) e instala el paquete de Android en el dispositivo predeterminado o el dispositivo virtual.

La propiedad [`$(AdbTarget)`](~/android/deploy-test/building-apps/build-properties.md#adbtarget) especifica el dispositivo Android de destino en el que se puede instalar el paquete Android o desde el que se puede quitar.

```bash
# Install package onto emulator via -e
# Use `/Library/Frameworks/Mono.framework/Commands/msbuild` on OS X
MSBuild /t:Install ProjectName.csproj /p:AdbTarget=-e
```

## <a name="signandroidpackage"></a>SignAndroidPackage

Crea y firma el archivo (`.apk`) del paquete de Android.

Úselo con `/p:Configuration=Release` para generar paquetes de comerciales.

## <a name="startandroidactivity"></a>StartAndroidActivity

Inicia la actividad predeterminada en el dispositivo o en el emulador en ejecución.

Para iniciar una actividad diferente, establezca la propiedad [`$(AndroidLaunchActivity)`](~/android/deploy-test/building-apps/build-properties.md#androidlaunchactivity)
en el nombre de la actividad.

Esto equivale a:

```shell
adb shell am start @PACKAGE_NAME@/$(AndroidLaunchActivity)
```

Se ha agregado en Xamarin.Android 10.2.

## <a name="stopandroidpackage"></a>StopAndroidPackage

Detiene completamente el paquete de aplicación en el dispositivo o en el emulador en ejecución.

Esto equivale a:

```shell
adb shell am force-stop @PACKAGE_NAME@
```

Se ha agregado en Xamarin.Android 10.2.

## <a name="uninstall"></a>Desinstalación

Desinstala el paquete de Android del dispositivo predeterminado o del virtual.

La propiedad [`$(AdbTarget)`](~/android/deploy-test/building-apps/build-properties.md#adbtarget) especifica el dispositivo Android de destino en el que se puede instalar el paquete Android o desde el que se puede quitar.

## <a name="updateandroidresources"></a>UpdateAndroidResources

Actualizan el archivo `Resource.designer.cs`.

El IDE llama normalmente a este destino cuando se agregan nuevos recursos al proyecto.
