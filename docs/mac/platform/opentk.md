---
title: Introducción a OpenTK en Xamarin. Mac
description: En este artículo se proporciona una introducción al uso de OpenTK en una aplicación de Xamarin. Mac. Trata la creación y el mantenimiento de una ventana de juego, la representación de un objeto simple y la visualización del objeto al usuario.
ms.prod: xamarin
ms.assetid: BDE05645-7273-49D3-809B-8642347678D2
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 0e283c9d9d1143f7cf4b0d2da0616e94d6ce5bce
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725017"
---
# <a name="introduction-to-opentk-in-xamarinmac"></a>Introducción a OpenTK en Xamarin. Mac

OpenTK (The Open Toolkit) es una biblioteca avanzada de bajo nivel C# que facilita el trabajo con OpenGL, OpenCL y openic. OpenTK se puede usar para juegos, aplicaciones científicas u otros proyectos que requieran gráficos 3D, audio o funcionalidad de cálculo. En este artículo se ofrece una breve introducción al uso de OpenTK en una aplicación de Xamarin. Mac.

[![](opentk-images/intro01.png "An example app run")](opentk-images/intro01.png#lightbox)

En este artículo, trataremos los aspectos básicos de OpenTK en una aplicación de Xamarin. Mac. Se recomienda encarecidamente que trabaje primero en el artículo [Hello, Mac](~/mac/get-started/hello-mac.md) , específicamente en las secciones [Introducción a Xcode y Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) y [salidas y acciones](~/mac/get-started/hello-mac.md#outlets-and-actions) , ya que trata conceptos clave y técnicas que vamos a usar en este artículo.

Es posible que desee echar un vistazo a la sección [exposición C# de clases y métodos a Objective-C](~/mac/internals/how-it-works.md) del documento [interno de Xamarin. Mac](~/mac/internals/how-it-works.md) , en él se explican los comandos `Register` y `Export` que se usan para conectar C# sus clases a objetos de Objective-C y elementos de la interfaz de usuario.

<a name="About_OpenTK" />

## <a name="about-opentk"></a>Acerca de OpenTK

Como se indicó anteriormente, OpenTK (el kit de herramientas abierto) es una biblioteca avanzada C# de bajo nivel que facilita el trabajo con OpenGL, OpenCL y openic. El uso de OpenTK en una aplicación de Xamarin. Mac proporciona las siguientes características:

- **Desarrollo rápido** : OpenTK proporciona tipos de datos seguros y documentación en línea para mejorar el flujo de trabajo de codificación y detectar errores más fácilmente y antes.
- **Integración sencilla** : OpenTK se ha diseñado para integrarse fácilmente con aplicaciones .net.
- **Licencia permisiva** : OpenTK se distribuye bajo las licencias MIT/X11 y es totalmente gratis.
- **Enlaces enriquecidos con seguridad de tipos** : OpenTK admite las versiones más recientes de OpenGL, OpenGL | es, openy y OpenCL con la carga de extensión automática, la comprobación de errores y la documentación en línea.
- **Opciones de GUI flexibles** : OpenTK proporciona una ventana de juego nativa y de alto rendimiento diseñada específicamente para juegos y Xamarin. Mac.
- **Code-OpenTK totalmente administrado y conforme a CLS** es compatible con las versiones de 32 y 64 bits de MacOS sin bibliotecas no administradas.
- **Kit de herramientas Math 3D** OpenTK proporciona Structs `Vector`, `Matrix`, `Quaternion` y `Bezier` a través de su kit de herramientas de matemáticas 3D.

OpenTK se puede usar para juegos, aplicaciones científicas u otros proyectos que requieran gráficos 3D, audio o funcionalidad de cálculo.

Para obtener más información, consulte [el sitio web del kit de herramientas abierto](https://opentk.net) .

<a name="OpenTK_Quickstart" />

## <a name="opentk-quickstart"></a>Inicio rápido de OpenTK

Como introducción rápida al uso de OpenTK en una aplicación de Xamarin. Mac, vamos a crear una aplicación sencilla que abre una vista de juego, representa un triángulo simple en esa vista y adjunta la vista del juego a la ventana principal de la aplicación Mac para mostrar el triángulo al usuario.

<a name="Starting_a_New_Project" />

### <a name="starting-a-new-project"></a>Iniciar un nuevo proyecto

Inicie Visual Studio para Mac y cree una nueva solución de Xamarin. Mac. Seleccione **Mac** > **app** > **General** > **aplicación de coco**:

[![](opentk-images/sample01.png "Adding a new Cocoa App")](opentk-images/sample01.png#lightbox)

Escriba `MacOpenTK` para el **nombre del proyecto**:

[![](opentk-images/sample02.png "Setting the project name")](opentk-images/sample02.png#lightbox)

Haga clic en el botón **crear** para compilar el nuevo proyecto.

<a name="Including_OpenTK" />

### <a name="including-opentk"></a>Inclusión de OpenTK

Antes de poder usar Open TK en una aplicación de Xamarin. Mac, debe incluir una referencia al ensamblado OpenTK. En el **Explorador de soluciones**, haga clic con el botón derecho en la carpeta **referencias** y seleccione **Editar referencias.** ...

Coloque una comprobación por `OpenTK` y haga clic en el botón **Aceptar** :

[![](opentk-images/sample03.png "Editing the project references")](opentk-images/sample03.png#lightbox)

<a name="Using_OpenTK" />

### <a name="using-opentk"></a>Usar OpenTK

Con el nuevo proyecto creado, haga doble clic en el archivo `MainWindow.cs` en el **Explorador de soluciones** para abrirlo para su edición. Haga que la clase de `MainWindow` tenga el aspecto siguiente:

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using AppKit;
using CoreGraphics;

namespace MacOpenTK
{
    public partial class MainWindow : NSWindow
    {
        #region Computed Properties
        public MonoMacGameView Game { get; set; }
        #endregion

        #region Constructors
        public MainWindow (IntPtr handle) : base (handle)
        {
        }

        [Export ("initWithCoder:")]
        public MainWindow (NSCoder coder) : base (coder)
        {
        }
        #endregion

        #region Override Methods
        public override void AwakeFromNib ()
        {
            base.AwakeFromNib ();

            // Create new Game View and replace the window content with it
            Game = new MonoMacGameView(ContentView.Frame);
            ContentView = Game;

            // Wire-up any required Game events
            Game.Load += (sender, e) =>
            {
                // TODO: Initialize settings, load textures and sounds here
            };

            Game.Resize += (sender, e) =>
            {
                // Adjust the GL view to be the same size as the window
                GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
            };

            Game.UpdateFrame += (sender, e) =>
            {
                // TODO: Add any game logic or physics
            };

            Game.RenderFrame += (sender, e) =>
            {
                // Setup buffer
                GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
                GL.MatrixMode(MatrixMode.Projection);

                // Draw a simple triangle
                GL.LoadIdentity();
                GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
                GL.Begin(BeginMode.Triangles);
                GL.Color3(Color.MidnightBlue);
                GL.Vertex2(-1.0f, 1.0f);
                GL.Color3(Color.SpringGreen);
                GL.Vertex2(0.0f, -1.0f);
                GL.Color3(Color.Ivory);
                GL.Vertex2(1.0f, 1.0f);
                GL.End();

            };

            // Run the game at 60 updates per second
            Game.Run(60.0);
        }
        #endregion
    }
}
```

Vamos a repasar este código en detalle más adelante.

<a name="Required_APIs" />

### <a name="required-apis"></a>API necesarias

Se requieren varias referencias para usar OpenTK en una clase de Xamarin. Mac. Al principio de la definición, hemos incluido las siguientes instrucciones de `using`:

```csharp
using System;
using System.Drawing;
using OpenTK;
using OpenTK.Graphics;
using OpenTK.Graphics.OpenGL;
using OpenTK.Platform.MacOS;
using Foundation;
using CoreGraphics;
```

Este conjunto mínimo será necesario para cualquier clase que use OpenTK.

<a name="Adding_the_Game_View" />

### <a name="adding-the-game-view"></a>Agregar la vista de juego

A continuación, es necesario crear una vista de juego para que contenga toda la interacción con OpenTK y mostrar los resultados. Usamos el código siguiente:

```csharp
public MonoMacGameView Game { get; set; }
...

// Create new Game View and replace the window content with it
Game = new MonoMacGameView(ContentView.Frame);
ContentView = Game;
```

Aquí hemos realizado la vista de juego del mismo tamaño que la ventana principal de Mac y hemos reemplazado la vista de contenido de la ventana por la nueva `MonoMacGameView`. Dado que reemplazamos el contenido de la ventana existente, se cambiará automáticamente el tamaño de la vista concedido cuando se cambie el tamaño de las ventanas principales.

<a name="Responding_to_Events" />

### <a name="responding-to-events"></a>Respuesta a eventos

Hay varios eventos predeterminados a los que debe responder cada vista de juego. En esta sección se tratarán los eventos principales necesarios.

<a name="The_Load_Event" />

### <a name="the-load-event"></a>Evento Load

El evento `Load` es el lugar para cargar recursos desde el disco, como imágenes, texturas o música. Para nuestra sencilla aplicación de prueba, no usamos el evento `Load`, pero lo hemos incluido como referencia:

```csharp
Game.Load += (sender, e) =>
{
    // TODO: Initialize settings, load textures and sounds here
};
```

<a name="The_Resize_Event" />

### <a name="the-resize-event"></a>El evento Resize

Se debe llamar al evento `Resize` cada vez que se cambia el tamaño de la vista del juego. En nuestra aplicación de ejemplo, vamos a hacer que la ventanilla de GL tenga el mismo tamaño que la vista de juego (a la que se cambia automáticamente el tamaño de la ventana principal de Mac) con el siguiente código:

```csharp
Game.Resize += (sender, e) =>
{
    // Adjust the GL view to be the same size as the window
    GL.Viewport(0, 0, Game.Size.Width, Game.Size.Height);
};
```

<a name="The_UpdateFrame_Event" />

### <a name="the-updateframe-event"></a>El evento UpdateFrame

El evento `UpdateFrame` se utiliza para controlar la entrada del usuario, actualizar las posiciones de los objetos, ejecutar cálculos físicos o de inteligencia artificial. Para nuestra sencilla aplicación de prueba, no usamos el evento `UpdateFrame`, pero lo hemos incluido como referencia:

```csharp
Game.UpdateFrame += (sender, e) =>
{
    // TODO: Add any game logic or physics
};
```

> [!IMPORTANT]
> La implementación de Xamarin. Mac de OpenTK no incluye el `Input API`, por lo que tendrá que usar las API proporcionadas por Apple para agregar compatibilidad con el teclado y el mouse. Opcionalmente, puede crear una instancia personalizada del `MonoMacGameView` e invalidar los métodos `KeyDown` y `KeyUp`.

<a name="The_RenderFrame_Event" />

### <a name="the-renderframe-event"></a>El evento RenderFrame

El evento `RenderFrame` contiene el código que se usa para representar (dibujar) los gráficos. En nuestra aplicación de ejemplo, vamos a rellenar la vista de juego con un triángulo sencillo:

```csharp
Game.RenderFrame += (sender, e) =>
{
    // Setup buffer
    GL.Clear(ClearBufferMask.ColorBufferBit | ClearBufferMask.DepthBufferBit);
    GL.MatrixMode(MatrixMode.Projection);

    // Draw a simple triangle
    GL.LoadIdentity();
    GL.Ortho(-1.0, 1.0, -1.0, 1.0, 0.0, 4.0);
    GL.Begin(BeginMode.Triangles);
    GL.Color3(Color.MidnightBlue);
    GL.Vertex2(-1.0f, 1.0f);
    GL.Color3(Color.SpringGreen);
    GL.Vertex2(0.0f, -1.0f);
    GL.Color3(Color.Ivory);
    GL.Vertex2(1.0f, 1.0f);
    GL.End();

};
```

Normalmente, el código de representación tendrá una llamada a `GL.Clear` para quitar los elementos existentes antes de dibujar los nuevos elementos.

> [!IMPORTANT]
> En el caso de la versión de Xamarin. Mac de OpenTK, **no** llame al método `SwapBuffers` de la instancia de `MonoMacGameView` al final del código de representación. Si lo hace, la vista de juego se verá estroboscópica rápidamente en lugar de mostrar la vista representada.

<a name="Running_the_Game_View" />

### <a name="running-the-game-view"></a>Ejecutar la vista de juego

Con todos los eventos necesarios definir y la vista de juego conectada a la ventana principal de Mac de nuestra aplicación, se lee para ejecutar la vista de juego y mostrar nuestros gráficos. Use el código siguiente:

```csharp
// Run the game at 60 updates per second
Game.Run(60.0);
```

Pasamos la velocidad de fotogramas deseada en la que queremos que se actualice la vista de juego, en nuestro ejemplo hemos elegido `60` fotogramas por segundo (la misma frecuencia de actualización que la de TV normal).

Vamos a ejecutar la aplicación y ver la salida:

[![](opentk-images/intro01.png "A sample of the apps output")](opentk-images/intro01.png#lightbox)

Si se cambia el tamaño de la ventana, la vista de juego también residirá y se cambiará el tamaño del triángulo y también se actualizará en tiempo real.

<a name="Where_to_Next" />

### <a name="where-to-next"></a>¿Dónde se debe a continuación?

Con los aspectos básicos del trabajo con OpenTk en una aplicación de Xamarin. Mac, estas son algunas sugerencias de lo que se debe probar a continuación:

- Intente cambiar el color del triángulo y el color de fondo de la vista del juego en los eventos `Load` y `RenderFrame`.
- Haga que el triángulo cambie de color cuando el usuario presione una tecla en los eventos `UpdateFrame` y `RenderFrame` o cree su propia clase de `MonoMacGameView` personalizada e invalide los métodos `KeyUp` y `KeyDown`.
- Haga que el triángulo se mueva por la pantalla mediante las teclas de reconocimiento del evento `UpdateFrame`. Hint: Use el método `Matrix4.CreateTranslation` para crear una matriz de traslación y llamar al método `GL.LoadMatrix` para cargarla en el evento `RenderFrame`.
- Use un bucle `for` para representar varios triángulos en el evento `RenderFrame`.
- Gire la cámara para dar una vista diferente del triángulo en el espacio 3D. Hint: Use el método `Matrix4.CreateTranslation` para crear una matriz de traslación y llamar al método `GL.LoadMatrix` para cargarla. También puede usar las clases `Vector2`, `Vector3`, `Vector4` y `Matrix4` para las manipulaciones de la cámara.

Para obtener más ejemplos, consulte el repositorio de [GitHub de ejemplos de OpenTK](https://github.com/opentk/opentk/tree/master/Source/Examples) . Contiene una lista oficial de ejemplos del uso de OpenTK. Tendrá que adaptar estos ejemplos para usar con la versión de Xamarin. Mac de OpenTK.

Para ver un ejemplo de Xamarin. Mac más complejo de una implementación de OpenTK, consulte nuestro ejemplo de [MonoMacGameView](https://docs.microsoft.com/samples/xamarin/mac-samples/monomacgamewindow) .

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha examinado rápidamente el trabajo con OpenTK en una aplicación de Xamarin. Mac. Vimos cómo crear una ventana de juego, cómo adjuntar la ventana de juego a una ventana de Mac y cómo representar una forma simple en la ventana de juego.

## <a name="related-links"></a>Vínculos relacionados

- [MacOpenTK (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/macopentk)
- [MonoMacGameView (ejemplo)](https://docs.microsoft.com/samples/xamarin/mac-samples/monomacgamewindow)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Trabajar con Windows](~/mac/user-interface/window.md)
- [El kit de herramientas abierto](https://opentk.net)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) (Directrices de interfaz humana de OS X)
- [Introducción a Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)
