---
title: Registro de depuración de Android
description: Describe cómo usar el registro de depuración para depurar aplicaciones de Xamarin.Android.
ms.prod: xamarin
ms.assetid: 01A715FE-9E9D-9B85-8A59-6568D8A09CA5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 06/22/2018
ms.openlocfilehash: ef3ba27b9056e1de92aabb87f86416b2985d6e1d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754219"
---
# <a name="android-debug-log"></a>Registro de depuración de Android

Un truco muy común que emplean los desarrolladores para depurar sus aplicaciones es realizar llamadas a `Console.WriteLine`. Pero en una plataforma móvil como Android no hay consola. Los dispositivos Android proporcionan un registro que puede usar al escribir aplicaciones. Este registro se conoce a veces como _logcat_ debido al comando escrito para recuperarlo. Use la herramienta **Registro de depuración** para ver los datos registrados.

## <a name="android-debug-log-overview"></a>Información general sobre el registro de depuración de Android

La herramienta **Registro de depuración** proporciona un método para ver la salida del registro mientras se depura una aplicación a través de Visual Studio. El registro de depuración es compatible con los siguientes dispositivos:

- Teléfonos, tabletas y ponibles Android físicos.
- Dispositivo virtual Android en ejecución en Android Emulator. 

> [!NOTE]
> La herramienta **Registro de depuración** no funciona con Xamarin Live Player.

El **Registro de depuración** no muestra mensajes de registro que se generan mientras la aplicación se ejecuta de forma independiente en el dispositivo (es decir, mientras está desconectada de Visual Studio).

## <a name="accessing-the-debug-log-from-visual-studio"></a>Acceso al registro de depuración desde Visual Studio

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Para abrir la herramienta **Registro de dispositivos**, haga clic en el icono **Registro de dispositivos (logcat)** en la barra de herramientas:

[![Ubicación de la herramienta Registro de dispositivos en la barra de herramientas](android-debug-log-images/vswin-01-logcat-sml.png)](android-debug-log-images/vswin-01-logcat.png#lightbox)

Como alternativa, inicie la herramienta **Registro de dispositivos** desde una de las selecciones de menú siguientes:

- **Ver > Otras ventanas > Registro de dispositivos**
- **Herramientas -> Android -> Registro de dispositivos**

La captura de pantalla siguiente muestra las distintas partes de la ventana **Herramienta de depuración**:

[![Partes de la ventana Herramienta de depuración](android-debug-log-images/vswin-03-features-sml.png)](android-debug-log-images/vswin-03-features.png#lightbox)

- **Selector de dispositivos**: selecciona qué dispositivo físico o emulador en ejecución se va a supervisar.

- **Entradas de registro** &ndash; Una tabla de mensajes de registro de logcat.

- **Borrar entradas del registro** &ndash; Borra todas las entradas de registro actuales de la tabla.

- **Reproducir/Pausar**: alterna entre la actualización o la pausa de la visualización de las nuevas entradas del registro.

- **Detener** &ndash; Detiene la visualización de las nuevas entradas de registro.

- **Cuadro de búsqueda**: escriba las cadenas de búsqueda en este cuadro para filtrar por un subconjunto de las entradas del registro.

Cuando se muestre la ventana de la herramienta **Registro de depuración**, use el menú desplegable de dispositivos para elegir el dispositivo Android que desea supervisar:

[![Ubicación del selector de dispositivos](android-debug-log-images/vswin-02-devices-combo-sml.png)](android-debug-log-images/vswin-02-devices-combo.png#lightbox)

Después de seleccionar el dispositivo, la herramienta **Registro de dispositivos** agrega las entradas del registro automáticamente desde una aplicación en ejecución (estas entradas del registro se muestran en la en la tabla de entradas del registro). El cambio entre dispositivos se detiene y se inicia el registro de dispositivos. Tenga en cuenta que un proyecto Android debe estar cargado antes de que los dispositivos aparezcan en el selector de dispositivos. Si el dispositivo no aparece en el selector de dispositivos, compruebe que está disponible en el menú desplegable de dispositivos de Visual Studio junto al botón **Iniciar**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Para abrir el **Registro de dispositivos**, haga clic en **Ver > Paneles > Registro de dispositivos**:

[![Ubicación del elemento de menú Registro de dispositivos](android-debug-log-images/vsmac-01-logcat-sml.png)](android-debug-log-images/vsmac-01-logcat.png#lightbox)

La captura de pantalla siguiente muestra las distintas partes de la ventana **Herramienta de depuración**:

[![Características de la ventana Herramienta de depuración](android-debug-log-images/vsmac-03-features-sml.png)](android-debug-log-images/vsmac-03-features.png#lightbox)

- **Selector de dispositivos**: selecciona qué dispositivo físico o emulador en ejecución se va a supervisar.

- **Entradas de registro** &ndash; Una tabla de mensajes de registro de logcat.

- **Borrar entradas del registro** &ndash; Borra todas las entradas de registro actuales de la tabla.

- **Cuadro de búsqueda**: escriba las cadenas de búsqueda en este cuadro para filtrar por un subconjunto de las entradas del registro.

- **Mostrar mensajes** &ndash; Alterna la visualización de los mensajes informativos.

- **Mostrar advertencias** &ndash; Alterna la visualización de mensajes de advertencia, que se muestran en amarillo.

- **Mostrar errores** &ndash; Alterna la visualización de mensajes de error, que se muestran en rojo.

- **Volver a conectar** &ndash; Vuelve a conectar el dispositivo y actualiza la visualización del registro de entrada.

- **Agregar marcador**: inserta un mensaje de marcador (como `--- Marker N ---`) después de la entrada de registro más reciente, donde _N_ es un contador que comienza en 1 y se incrementa en 1 cuando se agregan nuevos marcadores.

Cuando se muestre la ventana de la herramienta Registro de depuración, use el menú desplegable de dispositivos para elegir el dispositivo Android que desea supervisar:

[![Ubicación del selector de dispositivos](android-debug-log-images/vsmac-02-devices-combo-sml.png)](android-debug-log-images/vsmac-02-devices-combo.png#lightbox)

Después de seleccionar el dispositivo, la herramienta **Registro de dispositivos** agrega las entradas del registro automáticamente desde una aplicación en ejecución (estas entradas del registro se muestran en la en la tabla de entradas del registro). El cambio entre dispositivos se detiene y se inicia el registro de dispositivos. Tenga en cuenta que un proyecto Android debe estar cargado antes de que los dispositivos aparezcan en el selector de dispositivos. Si el dispositivo no aparece en el selector de dispositivos, compruebe que está disponible en el menú desplegable de dispositivos de Visual Studio junto al botón **Iniciar**.

-----

## <a name="accessing-from-the-command-line"></a>Acceso desde la línea de comandos

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Otra opción es ver el registro de depuración a través de la línea de comandos. Abra una ventana de símbolo del sistema y navegue hasta la carpeta de herramientas de la plataforma de Android SDK (normalmente, la carpeta de herramientas de la plataforma del SDK se encuentra en **C:\\Archivos de programa (x86)\\Android\\android-sdk\\platform-tools**).

Si se conecta solo un dispositivo (dispositivo físico o emulador), se puede ver el registro escribiendo el comando siguiente:

```shell
$ adb logcat
```

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Otra opción es ver el registro de depuración a través de la línea de comandos. Abra una ventana Terminal y navegue hasta la carpeta de herramientas de la plataforma de Android SDK (normalmente, la carpeta de herramientas de la plataforma del SDK se encuentra en **/Usuarios/nombredeusuario/Librería/Developer/Xamarin/android-sdk-macosx/platform-tools**).

Si se conecta solo un dispositivo (dispositivo físico o emulador), se puede ver el registro escribiendo el comando siguiente:

```shell
$ ./adb logcat
```

-----

Si se conecta más de un dispositivo, el dispositivo se debe identificar explícitamente. Por ejemplo, **adb -d logcat** muestra el registro del único dispositivo físico conectado, mientras que **adb -e logcat** muestra el registro del único emulador que se ejecuta.

Encontrará más comandos escribiendo **adb** y leyendo los mensajes de ayuda.

## <a name="writing-to-the-debug-log"></a>Escribir en el registro de depuración

Los mensajes pueden escribirse en el [registro de depuración](xref:Android.Util.Log) usando los métodos de la clase **Android.Util.Log**.
Por ejemplo: 

```csharp
string tag = "myapp";

Log.Info (tag, "this is an info message");
Log.Warn (tag, "this is a warning message");
Log.Error (tag, "this is an error message");
```

Esto genera una salida similar a la siguiente:

```shell
I/myapp   (11103): this is an info message
W/myapp   (11103): this is a warning message
E/myapp   (11103): this is an error message
```

También es posible utilizar `Console.WriteLine` para escribir en el **Registro de depuración**. Estos mensajes aparecen en logcat con un formato de salida ligeramente diferente (esta técnica es especialmente útil al depurar aplicaciones de Xamarin.Forms en Android):

```csharp
System.Console.WriteLine ("DEBUG - Button Clicked!");
```

Esto genera una salida similar a la siguiente en logcat:

```
Info (19543) / mono-stdout: DEBUG - Button Clicked!
```

## <a name="interesting-messages"></a>Mensajes interesantes

Al leer el registro (y especialmente al proporcionar fragmentos de código de registro a otras personas), a menudo es demasiado incómodo examinar el archivo de registro en su totalidad.
Para facilitar la navegación por los mensajes de registro, empiece buscando una entrada de registro que se parezca a la siguiente:

```shell
I/ActivityManager(12944): Starting: Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=GcTest.GcTest/gctest.Activity1 } from pid 24175
```

En concreto, busque una línea que coincida con la expresión regular que también contiene el nombre del paquete de aplicación:

```shell
^I.*ActivityManager.*Starting: Intent
```

Esta es la línea que corresponde al inicio de una actividad, y la *mayoría* de los mensajes siguientes (pero no todos) deberían estar relacionados con la aplicación.

Tenga en cuenta que cada mensaje contiene el identificador de proceso (pid) del proceso que genera el mensaje. En el mensaje de `ActivityManager` anterior, el proceso `12944` generó el mensaje. Para determinar qué proceso es el proceso de la aplicación que se está depurando, busque el mensaje **mono.MonoRuntimeProvider**: 

```shell
I/ActivityThread(  602): Pub TouchTest.TouchTest.__mono_init__: mono.MonoRuntimeProvider
```

Este mensaje procede del proceso que se inició. Todos los mensajes posteriores que contienen este pid proceden del mismo proceso.
