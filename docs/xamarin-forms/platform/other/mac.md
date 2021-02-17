---
title: Configuración de la plataforma Mac
description: En este artículo se explica cómo agregar un proyecto de Mac a un Xamarin.Forms proyecto, que generará una aplicación capaz de ejecutarse en MacOS Sierra y MacOS el Capitan.
ms.prod: xamarin
ms.assetid: EEC549E0-F182-4F9C-B2BA-B31D19569AA5
ms.technology: xamarin-forms
ms.custom: xamu-video
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5a5d26813719bfe807fff6745683fa13bde4429e
ms.sourcegitcommit: a0de974875f8fa1a29f7abc990137246789ad85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2021
ms.locfileid: "100630155"
---
# <a name="mac-platform-setup"></a>Configuración de la plataforma Mac

:::image type="icon" source="~/media/shared/preview.png" border="false":::

Antes de empezar, cree un proyecto (o use uno existente) Xamarin.Forms . Solo puede agregar aplicaciones Mac mediante Visual Studio para Mac.

> [!VIDEO https://youtube.com/embed/mvQ7jzaNseM]

**Agregar un proyecto de macOS a Xamarin.Forms vídeo**

## <a name="adding-a-mac-app"></a>Agregar una aplicación Mac

Siga estas instrucciones para agregar una aplicación Mac que se ejecutará en macOS Sierra y macOS el Capitan:

1. En Visual Studio para Mac, haga clic con el botón derecho en la Xamarin.Forms solución existente y elija **Agregar > agregar nuevo proyecto...**

2. En la ventana **nuevo proyecto** , elija **Mac > App > aplicación de coco** y presione **siguiente**.

3. Escriba un **nombre de aplicación** (y, opcionalmente, elija otro nombre para el elemento de acoplamiento) y, a continuación, presione **siguiente**.

4. Revise la configuración y presione **crear**. Estos pasos se muestran a continuación:

    ![Instrucciones animadas que muestran cómo agregar una aplicación de cacao](mac-images/add-macos-proj.gif)

5. En el proyecto de Mac, haga clic con el botón derecho en **paquetes > agregar paquetes...** para agregar [Xamarin.Forms](https://www.nuget.org/packages/Xamarin.Forms/) NuGet. También debe actualizar los otros proyectos para que usen la misma versión del Xamarin.Forms paquete NuGet.

6. En el proyecto de Mac, haga clic con el botón derecho en **referencias** y agregue una referencia al proyecto Xamarin.Forms (proyecto compartido o proyecto de biblioteca de .net Standard).

    ![Agregue una referencia al proyecto de código::: no-LOC (Xamarin. Forms)::: Shared Code](mac-images/references-sml.png)

7. Actualice **Main.CS** para inicializar `AppDelegate` :

    ```csharp
    static class MainClass
    {
        static void Main(string[] args)
        {
            NSApplication.Init();
            NSApplication.SharedApplication.Delegate = new AppDelegate(); // add this line
            NSApplication.Main(args);
        }
    }
    ```

8. Actualice `AppDelegate` para inicializar Xamarin.Forms , crear una ventana de y cargar la Xamarin.Forms aplicación (recordar para establecer un adecuado `Title` ). _Si tiene otras dependencias que deben inicializarse, hágalo aquí también._

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Platform.MacOS;
    // also add a using for the Xamarin.Forms project, if the namespace is different to this file
    ...
    [Register("AppDelegate")]
    public class AppDelegate : FormsApplicationDelegate
    {
        NSWindow window;
        public AppDelegate()
        {
            var style = NSWindowStyle.Closable | NSWindowStyle.Resizable | NSWindowStyle.Titled;

            var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);
            window = new NSWindow(rect, style, NSBackingStore.Buffered, false);
            window.Title = "Xamarin.Forms on Mac!"; // choose your own Title here
            window.TitleVisibility = NSWindowTitleVisibility.Hidden;
        }

        public override NSWindow MainWindow
        {
            get { return window; }
        }

        public override void DidFinishLaunching(NSNotification notification)
        {
            Forms.Init();
            LoadApplication(new App());
            base.DidFinishLaunching(notification);
        }
    }
    ```

9. Haga doble clic en **Main. Storyboard** para editarlo en Xcode. Seleccione la **ventana** y _desactive_ la casilla **es el controlador inicial** (esto se debe a que el código anterior crea una ventana):

    [![Desactive la casilla es el controlador inicial en Xcode](mac-images/xcode-init-controller-sml.png)](mac-images/xcode-init-controller.png#lightbox)

    Puede editar el sistema de menús en el guion gráfico para quitar elementos no deseados.

10. Por último, agregue los recursos locales (por ejemplo, archivos de imagen) de los proyectos de plataforma existentes que son necesarios.

11. El proyecto de Mac ahora debe ejecutar el Xamarin.Forms código en MacOS.

## <a name="next-steps"></a>Pasos siguientes

### <a name="styling"></a>Aplicación de estilos

Con los cambios recientes realizados en `OnPlatform` , ahora puede tener como destino cualquier número de plataformas. Esto incluye macOS.

```xml
<Button.TextColor>
    <OnPlatform x:TypeArguments="Color">
        <On Platform="iOS" Value="White"/>
        <On Platform="macOS" Value="White"/>
        <On Platform="Android" Value="Black"/>
    </OnPlatform>
</Button.TextColor>
```

Tenga en cuenta que también puede duplicar en plataformas como esta: `<On Platform="iOS, macOS" ...>` .

### <a name="window-size-and-position"></a>Tamaño y posición de la ventana

Puede ajustar el tamaño y la ubicación iniciales de la ventana en la `AppDelegate` :

```csharp
var rect = new CoreGraphics.CGRect(200, 1000, 1024, 768);  // x, y, width, height
```

## <a name="known-issues"></a>Problemas conocidos

Se trata de una versión preliminar, por lo que debe esperar que no todo esté listo para la producción. A continuación se muestran algunas cosas que puede encontrar al agregar macOS a los proyectos:

### <a name="not-all-nugets-are-ready-for-macos"></a>No todos los paquetes Nuget están listos para macOS

Es posible que algunas de las bibliotecas que usa todavía no admitan macOS. En este caso, deberá enviar una solicitud al mantenedor del proyecto para agregarlo. Hasta que tengan soporte técnico, es posible que tenga que buscar alternativas.

### <a name="missing-xamarinforms-features"></a>Características que faltan Xamarin.Forms

No todas Xamarin.Forms las características se han completado en esta versión preliminar. Para obtener más información, consulte el estado de compatibilidad de la [plataforma MacOS](https://github.com/xamarin/Xamarin.Forms/wiki/Platform-Support-macOS-Status) en el [Xamarin.Forms](https://github.com/xamarin/Xamarin.Forms) repositorio de github.

## <a name="related-links"></a>Vínculos relacionados

- [Xamarin.Mac](~/mac/index.yml)
