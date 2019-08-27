---
title: Generar perfiles de aplicaciones Android
description: En esta guía se explica cómo usar las herramientas del generador de perfiles para examinar el rendimiento y el uso de memoria de una aplicación Android.
ms.topic: article
ms.prod: xamarin
ms.assetid: 8C823FEE-A6F6-4C31-9EB6-E51407A2FD8E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: d2999a21debefb05e5aa3cdaace56cb4f3ac2d81
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525579"
---
# <a name="profiling-android-apps"></a>Generar perfiles de aplicaciones Android

Antes de implementar la aplicación en una tienda de aplicaciones, es importante que identifique y corrija los cuellos de botella de rendimiento, los problemas de uso excesivo de memoria y el uso ineficaz de los recursos de red. Para este propósito hay disponibles dos herramientas de generador de perfiles:

- Generador de perfiles de Xamarin 
- Android Profiler en Android Studio

En esta guía se presenta el generador de perfiles de Xamarin y se proporciona información detallada sobre cómo empezar a usar Android Profiler.

 
## <a name="xamarin-profiler"></a>Generador de perfiles de Xamarin

Xamarin Profiler es una aplicación independiente integrada con Visual Studio y Visual Studio para Mac para generar perfiles de aplicaciones Xamarin desde el IDE. Para obtener más información sobre el uso de Xamarin Profiler, vea [Xamarin Profiler](~/tools/profiler/index.md).

> [!NOTE]
> Debe ser un suscriptor de [Visual Studio Enterprise](https://visualstudio.microsoft.com/vs/compare/) para desbloquear la característica Xamarin Profiler en Visual Studio Enterprise en Windows o Visual Studio para Mac.
 
## <a name="android-studio-profiler"></a>Android Studio Profiler

Android Studio 3.0 y posterior incluye la herramienta Android Profiler. Puede usar Android Profiler para medir el rendimiento de una aplicación Android de Xamarin compilada con Visual Studio &ndash; sin necesidad de disponer de una licencia de Visual Studio Enterprise. Aun así, a diferencia de Xamarin Profiler, Android Profiler no está integrado con Visual Studio y solo puede usarse para generar perfiles de un paquete de aplicaciones Android (APK) que se haya compilado con antelación e importado en Android Profiler.

### <a name="launching-a-xamarin-android-app-in-android-profiler"></a>Iniciar una aplicación Android de Xamarin en Android Profiler

En los pasos siguientes se explica cómo iniciar una aplicación Android de Xamarin en la herramienta Android Profiler de Android Studio. En las siguientes capturas de pantalla de ejemplo, se usa Android Profiler para compilar la aplicación [XamagonXuzzle](https://docs.microsoft.com/samples/xamarin/mobile-samples/liveplayer-xamagonxuzzlelp/) de Xamarin.Forms y generar los perfiles:

1. En las opciones de compilación del proyecto de Android, deshabilite **Use Shared Runtime** (Usar tiempo de ejecución compartido). Esto garantiza que el paquete de aplicaciones Android (APK) se compile sin una dependencia en el tiempo de ejecución de Mono en tiempo de desarrollo compartido.

    ![Deshabilitación del uso del tiempo de ejecución compartido](profiling-images/vswin/01-turn-off-shared-runtime.png)

2. Compile la aplicación para la **depuración** e impleméntela en un dispositivo físico o un emulador. Esto hace que se compile una versión de **depuración** del APK.
    Para el ejemplo de **XamagonXuzzle**, el APK resultante se denomina **com.companyname.XamagonXuzzle-Signed.apk**.

3. Abra la carpeta del proyecto y vaya a **bin/Debug**. En esta carpeta, busque la versión **Signed.apk** de la aplicación y cópiela en un lugar fácilmente accesible (como el escritorio). En la siguiente captura de pantalla, se localiza el APK **com.companyname.XamagonXuzzle-Signed.apk** y se copia en el escritorio:

    [![Ubicación del archivo APK firmado de depuración](profiling-images/vswin/02-locating-the-debug-apk-sml.png)](profiling-images/vswin/02-locating-the-debug-apk.png#lightbox)

4. Inicie Android Studio y seleccione **Profile or debug APK** (Generar perfiles del APK o depurarlo):

    ![Iniciar el generador de perfiles desde la pantalla de inicio de Android Studio](profiling-images/vswin/03-android-studio.png)

5. En el cuadro de diálogo **Select APK File** (Seleccionar archivo APK), desplácese hasta el APK que ha compilado y copiado anteriormente. Seleccione el APK y haga clic en **Aceptar**: 
    
    ![Seleccionar el APK en el cuadro de diálogo de selección del archivo APK](profiling-images/vswin/04-select-apk-dialog.png)

6. Android Studio cargará el APK y desensamblará **classes.dex**:

    ![Configuración del APK](profiling-images/vswin/05-setting-up-the-apk.png)

7. Una vez cargado el APK, Android Studio mostrará la siguiente pantalla de proyecto para el APK. Haga clic con el botón derecho en el nombre de la aplicación en la vista de árbol a la izquierda y seleccione **Open Module Settings** (Abrir configuración del módulo):

    [![Ubicación del elemento de menú para abrir la configuración del módulo](profiling-images/vswin/06-open-module-settings-sml.png)](profiling-images/vswin/06-open-module-settings.png#lightbox)

8. Vaya a **Configuración del proyecto > Módulos**, seleccione el nodo **-Signed** de la aplicación y haga clic en **&lt;No SDK&gt;** (Sin SDK):

    [![Navegar hasta la configuración del SDK](profiling-images/vswin/07-project-settings-modules-sml.png)](profiling-images/vswin/07-project-settings-modules.png#lightbox)

9. En el menú desplegable **Module SDK** (SDK de módulo), seleccione el nivel de Android SDK que se ha usado para compilar la aplicación (en este ejemplo, se ha usado el nivel de API 26 para compilar **XamagonXuzzle**):

    [![Establecer el nivel del SDK del proyecto](profiling-images/vswin/08-project-sdk-level-sml.png)](profiling-images/vswin/08-project-sdk-level.png#lightbox)

    Haga clic en **Aplicar** y **Aceptar** para guardar esta configuración.

10. Inicie el generador de perfiles desde el icono de la barra de herramientas:

    [![Ubicación del icono del generador de perfiles en la barra de herramientas](profiling-images/vswin/09-launch-profiler-sml.png)](profiling-images/vswin/09-launch-profiler.png#lightbox)

11. Seleccione el destino de implementación para ejecutar la aplicación o generar perfiles y haga clic en **Aceptar**. El destino de implementación puede ser un dispositivo físico o un dispositivo virtual que se ejecute en un emulador. En este ejemplo, se usa un dispositivo Nexus 5X:

    ![Seleccionar el destino de implementación](profiling-images/vswin/10-select-deployment-target.png)

12. Cuando se inicie el generador de perfiles, tardará unos segundos en conectar con el dispositivo de implementación y el proceso de aplicación. Mientras está instalando el APK, Android Profiler mostrará **No connected devices** (No hay dispositivos conectados) y **No debuggable processes** (No hay procesos depurables).

    [![El generador de perfiles instala el APK](profiling-images/vswin/11-no-connected-devices-sml.png)](profiling-images/vswin/11-no-connected-devices.png#lightbox)

13. Tras unos segundos, Android Profiler completará la instalación del APK y lo iniciará. Además, mostrará el nombre del dispositivo y el nombre del proceso de aplicación del que se van a generar perfiles (en este ejemplo, **LGE Nexus 5X** y **com.companyname.XamagonXuzzle**, respectivamente):

    [![Ventana del generador de perfiles después de iniciarlo](profiling-images/vswin/12-profiler-starts-sml.png)](profiling-images/vswin/12-profiler-starts.png#lightbox)

14. Después de identificar el dispositivo y los procesos depurables, Android Profiler comienza la generación de perfiles de la aplicación:

    [![Información mostrada por el generador de perfiles para la aplicación en ejecución](profiling-images/vswin/13-profiler-running-sml.png)](profiling-images/vswin/13-profiler-running.png#lightbox)

15. Si pulsa el botón **Aleatorizar** en **XamagonXuzzle** (que hace que desplace y aleatorice los iconos), verá que el uso de la CPU aumentará durante el intervalo de aleatorización de la aplicación:

    [![Uso de CPU al pulsar el botón para aleatorizar](profiling-images/vswin/14-tap-randomize-sml.png)](profiling-images/vswin/14-tap-randomize.png#lightbox)


### <a name="using-the-android-profiler"></a>Uso de Android Profiler

En la [documentación de Android Studio](https://developer.android.com/studio/profile/android-profiler.html) se incluye información detallada para usar Android Profiler.
Los temas siguientes pueden ser de interés para desarrolladores de Android de Xamarin:

- [CPU Profiler](https://developer.android.com/studio/profile/cpu-profiler.html): explica cómo inspeccionar la actividad de subprocesos y el uso de CPU de la aplicación en tiempo real.

- [Memory Profiler](https://developer.android.com/studio/profile/memory-profiler.html): muestra un gráfico en tiempo real del uso de memoria de la aplicación e incluye un botón para registrar las asignaciones de memoria para el análisis.

- [Network Profiler](https://developer.android.com/studio/profile/network-profiler.html): muestra la actividad de red en tiempo real de los datos que la aplicación envía y recibe.
