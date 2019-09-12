---
title: Instalación en el equipo Windows
description: En esta guía se describen los pasos necesarios para instalar Xamarin.Android para Visual Studio en Windows y se explica cómo configurar Xamarin.Android para compilar su primera aplicación de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 2BE4D5AD-D468-B177-8F96-837D084E7DE1
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: 315cb8e58b0b9f29c06e4d0af2b8cf9adffb06e3
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758520"
---
# <a name="windows-installation"></a>Instalación en el equipo Windows

_En esta guía se describen los pasos necesarios para instalar Xamarin.Android para Visual Studio en Windows y se explica cómo configurar Xamarin.Android para compilar su primera aplicación de Xamarin.Android._

## <a name="overview"></a>Información general

Gracias a que ahora Xamarin se incluye con todas las ediciones de Visual Studio sin ningún costo adicional y no requiere ninguna licencia independiente, puede usar el instalador de Visual Studio para descargar e instalar las herramientas de Xamarin.Android
(los pasos relacionados con la instalación manual y la configuración de las licencias que eran necesarios para las versiones anteriores de Xamarin.Android ya no son necesarios). En esta guía aprenderá lo siguiente:

- Cómo configurar las ubicaciones personalizadas de Java Development Kit, Android SDK y Android NDK.

- Cómo iniciar Android SDK Manager para descargar e instalar componentes adicionales de Android SDK.

- Cómo preparar un emulador o un dispositivo de Android para la depuración y las pruebas.

- Cómo crear el primer proyecto de aplicación de Xamarin.Android.

Cuando acabe esta guía, tendrá una instalación operativa de Xamarin.Android integrada en Visual Studio y podrá empezar a compilar su primera aplicación de Xamarin.Android.

## <a name="installation"></a>Instalación

Para obtener información detallada sobre la instalación de Xamarin para su uso con Visual Studio en Windows, consulte la guía de [instalación de Windows](~/get-started/installation/windows.md).

## <a name="configuration"></a>Configuración

Xamarin.Android usa Java Development Kit (JDK) y el SDK de Android para compilar las aplicaciones. Durante la instalación, el instalador de Visual Studio coloca estas herramientas en sus ubicaciones predeterminadas y configura el entorno de desarrollo con la configuración de ruta de acceso adecuada. Puede ver y cambiar estas ubicaciones haciendo clic en **Herramientas > Opciones > Xamarin > Configuración de Android**:

![Captura de pantalla del cuadro de diálogo Configuración de Android de Xamarin](windows-images/07-settings.png)

Para la mayoría de los usuarios, estas ubicaciones predeterminadas funcionarán correctamente y no habrá que efectuar más cambios, pero tal vez quiera configurar Visual Studio con unas ubicaciones personalizadas para estas herramientas (por ejemplo, en el caso de que haya instalado el JDK de Java, el SDK de Android o el NDK en otra ubicación). Haga clic en **Cambiar** junto a la ruta de acceso que quiera cambiar; luego, desplácese hasta la nueva ubicación.

Xamarin.Android usa [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html), necesario para el desarrollo en el nivel de API 24 o superior. JDK 8 también admite niveles de API anteriores al 24. Si está desarrollando contenido específicamente para el nivel de API 23 o uno anterior, puede continuar usando [JDK 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html).

> [!IMPORTANT]
> Xamarin.Android no admite JDK 9.

### <a name="android-sdk-manager"></a>Administrador de SDK de Android

Android usa varias opciones de nivel de API de Android para determinar la compatibilidad de su aplicación entre las distintas versiones de Android (para obtener más información sobre los niveles de la API de Android, consulte [Understanding Android API Levels](~/android/app-fundamentals/android-api-levels.md) [Información de los niveles de API de Android]).
En función de los niveles de API de Android que quiera establecer como objetivo, es posible que tenga que descargar e instalar componentes adicionales del SDK de Android. Además, puede que tenga que instalar herramientas opcionales, así como las imágenes del emulador que se proporcionan en el SDK de Android. Para ello, use **Android SDK Manager**. Puede iniciar **Android SDK Manager** haciendo clic en **Herramientas > Android > Android SDK Manager**:

[![Cómo iniciar Android SDK Manager](windows-images/08-sdk-manager-sml.png)](windows-images/08-sdk-manager.png#lightbox)

De forma predeterminada, Visual Studio instala Android SDK Manager de Google:

![Captura de pantalla de ejemplo de Android SDK Manager de Google](windows-images/09-google-sdk-manager.png)

Puede usar Android SDK Manager de Google para instalar las versiones del paquete de Android SDK Tools hasta la versión 25.2.3, pero si necesita usar una versión posterior del paquete de Android SDK Tools, debe instalar el complemento de Android SDK Manager de Xamarin para Visual Studio (disponible en Visual Studio Marketplace). Esto es necesario porque el SDK Manager independiente de Google ha quedado en desuso en la versión 25.2.3 del paquete de Android SDK Tools. 

Para más información sobre cómo usar Android SDK Manager de Xamarin, vea [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md).

### <a name="android-emulator"></a>Emulador Android

[Android Emulator](https://developer.android.com/studio/run/emulator) puede ser una herramienta útil para desarrollar y probar una aplicación Xamarin.Android. Por ejemplo, un dispositivo físico, como una tableta, podría no estar disponible durante el desarrollo o un programador podría querer ejecutar algunas pruebas de integración en su equipo antes de confirmar el código.

La emulación de un dispositivo Android en un equipo implica a los siguientes componentes:

- **Google Android Emulator** &ndash; Se trata de un emulador basado en [QEMU](https://www.qemu.org/) que crea un dispositivo virtualizado que se ejecuta en la estación de trabajo del desarrollador.
- **Una imagen de emulador** &ndash; Una _imagen de emulador_ es una plantilla o una especificación del hardware y el sistema operativo que se va a virtualizar. Por ejemplo, una imagen de emulador identificaría los requisitos de hardware de un Nexus 5X con Android 7.0 con Google Play Services instalado. Otra imagen de emulador podría especificar una tableta de 10" con Android 6.0.
- **Dispositivo virtual Android (AVD)** &ndash; Un _dispositivo virtual Android_ es un dispositivo Android emulado creado a partir de una imagen de emulador. Al ejecutar y probar aplicaciones Android, Xamarin.Android inicia Android Emulator, inicia un AVD específico, instala el APK y luego ejecuta la aplicación.

Se puede lograr una mejora considerable del rendimiento al desarrollar en equipos basados en x86 mediante imágenes de emulador especiales optimizadas para la arquitectura x86 y una de entre dos tecnologías de virtualización:

1. Hyper-V de Microsoft: disponible en equipos con la actualización de abril de 2018 de Windows 10 o una versión posterior.
2. Hardware Accelerated Execution Manager (HAXM) de Intel: disponible en equipos x86 con OS X, macOS o versiones anteriores de Windows.

Para obtener más información sobre Android Emulator, Hyper-V y HAXM, vea la guía [Aceleración de hardware para el rendimiento del emulador](~/android/get-started/installation/android-emulator/hardware-acceleration.md).

> [!NOTE]
> En las versiones de Windows anteriores a la actualización de abril de 2018 de Windows 10, HAXM no es compatible con Hyper-V. En este escenario es necesario [deshabilitar Hyper-V](~/android/get-started/installation/android-emulator/troubleshooting.md#disable-hyperv) o usar las imágenes de emulador más lentas sin las optimizaciones de x86.

<a name="device" />

### <a name="android-device"></a>Dispositivo Android

Si dispone de un dispositivo Android físico para efectuar las pruebas, ahora es un buen momento para configurarlo para su uso de desarrollo. Consulte [Configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md) para configurar el dispositivo Android para el desarrollo; luego, conéctelo al equipo para ejecutar y depurar aplicaciones de Xamarin.Android.

## <a name="create-an-application"></a>Crear una aplicación

Ahora que ha instalado Xamarin.Android, puede iniciar Visual Studio para crear un proyecto. Haga clic en **Archivo > Nuevo > Proyecto** para comenzar a crear la aplicación:

![Cómo crear un proyecto](windows-images/10-new-project.png)

En el cuadro de diálogo **Nuevo proyecto**, seleccione **Android** en **Plantillas** y haga clic en **Aplicación de Android** en el panel de la derecha. Escriba un nombre para la aplicación (en la siguiente captura de pantalla, la aplicación se llama **MyApp**); luego, haga clic en **Aceptar**:

[![Captura de pantalla del cuadro de diálogo Nuevo proyecto, en el que se crea una aplicación en blanco para Android](windows-images/11-first-app-sml.w157.png)](windows-images/11-first-app.w157.png#lightbox)

Ya está. Ahora ya puede usar Xamarin.Android para crear aplicaciones de Android.

## <a name="summary"></a>Resumen

En este artículo ha aprendido a configurar e instalar la plataforma de Xamarin.Android en Windows, a configurar (opcional) Visual Studio con ubicaciones de instalación personalizadas de Java JDK y del SDK de Android, a iniciar SDK Manager para instalar componentes adicionales del SDK de Android, a configurar un emulador o dispositivo de Android y a empezar a compilar su primera aplicación.

El siguiente paso consiste en echar un vistazo a los tutoriales de [Hello, Android](~/android/get-started/hello-android/index.md) para aprender a crear una aplicación operativa de Xamarin.Android.

## <a name="related-links"></a>Vínculos relacionados

- [Descarga de Visual Studio](https://visualstudio.microsoft.com/vs/)
- [Instalación de Visual Studio Tools para Xamarin](~/get-started/installation/windows.md)
- [Requisitos del sistema](~/cross-platform/get-started/requirements.md)
- [Configuración de Android SDK](~/android/get-started/installation/android-sdk.md)
- [Configuración de Android Emulator](~/android/get-started/installation/android-emulator/index.md)
- [Configurar el dispositivo para el desarrollo](~/android/get-started/installation/set-up-device-for-development.md)
- [Ejecutar aplicaciones en Android Emulator](https://developer.android.com/studio/run/emulator#Requirements)
