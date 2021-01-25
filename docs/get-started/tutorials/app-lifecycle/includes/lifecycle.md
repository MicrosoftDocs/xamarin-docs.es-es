---
ms.openlocfilehash: d0b00cf71a6121026dfa368592da8ce8d3be0373
ms.sourcegitcommit: a5a5c5de7d04f046a64e4875e180fc93227bf495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/21/2021
ms.locfileid: "98635013"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Para completar este tutorial debe tener Visual Studio 2019 (versión más reciente), con la carga de trabajo **Desarrollo para dispositivos móviles con .NET** instalada. Además, necesita un equipo Mac emparejado para compilar la aplicación del tutorial en iOS. Para obtener información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md). Para obtener información sobre cómo conectar Visual Studio 2019 a un host de compilación de Mac, consulte [Emparejar con Mac para el desarrollo de Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Inicie Visual Studio y cree una aplicación de Xamarin.Forms en blanco llamada **AppLifecycleTutorial**.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **AppLifecycleTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Explorador de soluciones**, en el proyecto **AppLifecycleTutorial**, expanda **App.xaml** y haga doble clic en **App.xaml.cs** para abrirlo. Después, en **App.xaml.cs**, actualice los reemplazos `OnStart`, `OnSleep` y `OnResume`, como se indica a continuación:

    ```csharp
    protected override void OnStart()
    {
        Console.WriteLine("OnStart");
    }

    protected override void OnSleep()
    {
        Console.WriteLine("OnSleep");
    }

    protected override void OnResume()
    {
        Console.WriteLine("OnResume");
    }
    ```

    Este código actualiza los reemplazos del método de ciclo de vida de las aplicaciones con instrucciones `Console.WriteLine` que indican cuándo se ha invocado cada método:

    - El método `OnStart` se invoca cuando se inicia la aplicación.
    - El método `OnSleep` se invoca cuando la aplicación pasa a segundo plano.
    - El método `OnResume` se invoca cuando la aplicación se reanuda desde segundo plano.

    > [!NOTE]
    > No hay ningún método para la finalización de aplicaciones. En circunstancias normales, la finalización de aplicaciones se produce desde el método `OnSleep`.

1. En la barra de herramientas de Visual Studio, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador remoto de iOS elegido. Cuando se inicia la aplicación, se invoca el método `OnStart` y se genera **OnStart** en la ventana **Salida de la aplicación** de Visual Studio:

    ```
    [Mono] Found as 'java_interop_jnienv_get_object_array_element'.
    OnStart
    [OpenGLRenderer] HWUI GL Pipeline
    ```

    Cuando la aplicación pasa a segundo plano (al pulsar el botón Inicio en iOS o Android), se invoca el método `OnSleep`:

    ```
    [EGL_emulation] eglMakeCurrent: 0x83ee2920: ver 3 0 (tinfo 0x8357eff0)
    OnSleep
    [Mono] Image addref System.Runtime.Serialization[0x83ee19c0] -> System.Runtime.Serialization.dll[0x83f57b00]: 2
    ```

    Después, cuando la aplicación se reanuda desde segundo plano (pulse el icono de aplicación en iOS; pulse el botón "Información general" en Android y seleccione la aplicación AppLifecycleTutorial), se invoca el método `OnResume`:

    ```
    Thread finished: <Thread Pool> #5
    OnResume
    [EGL_emulation] eglMakeCurrent: 0x83ee2920: ver 3 0 (tinfo 0x8357eff0)
    ```

    > [!NOTE]
    > En estos bloques de código, se muestra un resultado de ejemplo al ejecutar la aplicación en Android.

    En Visual Studio, detenga la aplicación.

    Para obtener más información sobre el ciclo de vida de las aplicaciones de Xamarin.Forms, vea [Ciclo de vida de aplicaciones de Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/vsmac)

Para completar este tutorial debe tener instalado Visual Studio para Mac (versión más reciente) con compatibilidad con la plataforma de iOS y Android. Además, también necesitará Xcode (versión más reciente). Para obtener más información sobre la instalación de la plataforma de Xamarin, consulte [Instalación de Xamarin](~/get-started/installation/index.md).

1. Inicie Visual Studio para Mac y cree una aplicación de Xamarin.Forms en blanco llamada **AppLifecycleTutorial**.

    > [!IMPORTANT]
    > Los fragmentos de código de C# y XAML en este tutorial necesitan que la solución se denomine **AppLifecycleTutorial**. El uso de otro nombre dará como resultado errores de compilación al copiar el código de este tutorial en la solución.

    Para obtener más información sobre la biblioteca de .NET Standard creada, vea [Anatomía de una aplicación de Xamarin.Forms](~/get-started/first-app/index.md) en [Análisis detallado de inicio rápido de Xamarin.Forms](~/get-started/first-app/index.md).

1. En el **Panel de solución**, en el proyecto **AppLifecycleTutorial**, expanda **App.xaml** y haga doble clic en **App.xaml.cs** para abrirlo. Después, en **App.xaml.cs**, actualice los reemplazos `OnStart`, `OnSleep` y `OnResume`, como se indica a continuación:

    ```csharp
    protected override void OnStart()
    {
        Console.WriteLine("OnStart");
    }

    protected override void OnSleep()
    {
        Console.WriteLine("OnSleep");
    }

    protected override void OnResume()
    {
        Console.WriteLine("OnResume");
    }
    ```

    Este código actualiza los reemplazos del método de ciclo de vida de las aplicaciones con instrucciones `Console.WriteLine` que indican cuándo se ha invocado cada método:

    - El método `OnStart` se invoca cuando se inicia la aplicación.
    - El método `OnSleep` se invoca cuando la aplicación pasa a segundo plano.
    - El método `OnResume` se invoca cuando la aplicación se reanuda desde segundo plano.

    > [!NOTE]
    > No hay ningún método para la finalización de aplicaciones. En circunstancias normales, la finalización de aplicaciones se produce desde el método `OnSleep`.

1. En la barra de herramientas de Visual Studio para Mac, pulse el botón **Iniciar** (el botón triangular similar a un botón de reproducción) para iniciar la aplicación en Android Emulator o en el simulador de iOS elegido. Cuando se inicia la aplicación, se invoca el método `OnStart` y se genera **OnStart** en la ventana **Salida de la aplicación** de Visual Studio para Mac:

    ```
    2019-02-11 12:05:23.164761+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4089
    2019-02-11 12:05:23.165297+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskCopyDebugDescription: AppLifecycleTuto[4089]/0#-1 LF=0
    2019-02-11 12:05:23.685430+0000 AppLifecycleTutorial.iOS[4089:361037] OnStart
    ```

    Cuando la aplicación pasa a segundo plano (al pulsar el botón Inicio en iOS o Android), se invoca el método `OnSleep`:

    ```
    2019-02-11 12:06:12.394301+0000 AppLifecycleTutorial.iOS[4089:361037] OnSleep
    Thread started: <Thread Pool> #3
    Thread started: <Thread Pool> #4
    Thread started: <Thread Pool> #5
    Thread started: <Thread Pool> #6
    2019-02-11 12:06:13.056968+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4089
    2019-02-11 12:06:13.057264+0000 AppLifecycleTutorial.iOS[4089:361037] SecTaskCopyDebugDescription: AppLifecycleTuto[4089]/0#-1 LF=0
    ```

    Después, cuando la aplicación se reanuda desde segundo plano (pulse el icono de aplicación en iOS; pulse el botón "Información general" en Android y seleccione la aplicación AppLifecycleTutorial), se invoca el método `OnResume`:

    ```
    2019-02-11 12:07:10.321905+0000 AppLifecycleTutorial.iOS[4130:365695] SecTaskLoadEntitlements failed error=22 cs_flags=200, pid=4130
    2019-02-11 12:07:10.322557+0000 AppLifecycleTutorial.iOS[4130:365695] SecTaskCopyDebugDescription: AppLifecycleTuto[4130]/0#-1 LF=0
    2019-02-11 12:07:17.110938+0000 AppLifecycleTutorial.iOS[4130:365695] OnResume
    ```

    > [!NOTE]
    > En estos bloques de código, se muestra un resultado de ejemplo al ejecutar la aplicación en iOS.

    En Visual Studio para Mac, detenga la aplicación.

    Para obtener más información sobre el ciclo de vida de las aplicaciones de Xamarin.Forms, vea [Ciclo de vida de aplicaciones de Xamarin.Forms](~/xamarin-forms/app-fundamentals/app-lifecycle.md).
