---
title: Creación de un proyecto de UWP de monogame
description: Monogame puede usarse para crear juegos y aplicaciones para Plataforma universal de Windows, destinados a varios dispositivos con un código base y un conjunto de contenido.
ms.prod: xamarin
ms.assetid: C6B99E44-00C1-4139-A1B7-FCFBE8749AB1
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 59bfad17e4c3a4720360f007ddf3e85835f972fd
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931084"
---
# <a name="creating-a-monogame-uwp-project"></a>Creación de un proyecto de UWP de monogame

_Monogame puede usarse para crear juegos y aplicaciones para Plataforma universal de Windows, destinados a varios dispositivos con un código base y un conjunto de contenido._

En este tutorial se aborda la creación de proyectos de Plataforma universal de Windows monogame (UWP) y la carga de contenido. Las aplicaciones para UWP pueden ejecutarse en todos los dispositivos de Windows 10, incluidos los equipos de escritorio, tabletas, Windows Phone y Xbox One.

En este tutorial se crea un proyecto vacío que muestra un fondo *azul azul aciano* (el color de fondo tradicional de las aplicaciones XNA).

## <a name="requirements"></a>Requisitos

El desarrollo de aplicaciones monogame para UWP requiere:

- Sistema operativo Windows 10
- Cualquier versión de Visual Studio 2017
- Herramientas de desarrollo de Windows 10
- Establecimiento del dispositivo en modo de desarrollador
- [Monogame 3.7.1 para Visual Studio](http://community.monogame.net/t/monogame-3-7-1-release/11173) o posterior

Para obtener más información, consulte esta [Página sobre cómo configurar el desarrollo para Windows 10 UWP](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up).

Los juegos de Xbox One se pueden desarrollar en hardware de Xbox One comercial. Se requiere software adicional tanto en el equipo de desarrollo como en la Xbox One. Para obtener información sobre la configuración de una consola Xbox One para el desarrollo de juegos, consulte esta página sobre la configuración de una [consola Xbox](https://msdn.microsoft.com/windows/uwp/xbox-apps/index)One.

## <a name="creating-an-empty-template"></a>Crear una plantilla vacía

Una vez que se han instalado todos los recursos necesarios y se ha habilitado el modo de Desarrollador en el equipo con Windows 10, podemos crear un nuevo proyecto de monogame con Visual Studio siguiendo estos pasos:

1. Seleccione **archivo**  >  **nuevo**  >  **proyecto...**
1. Seleccione las **plantillas instaladas**  >  **Templates**  >  categoría de**Visual C#**  >  **monogame** :

    ![Categoría de monogame](uwp-images/image1.png)

1. Seleccione la opción de **proyecto universal de Windows 10 monogame** :

    ![Selección de la opción de proyecto universal de Windows 10 de monogame](uwp-images/image2.png)

1. Escriba un nombre para el nuevo proyecto y haga clic en **Aceptar**.
Si Visual Studio muestra errores después de hacer clic en aceptar, compruebe que estén instaladas las herramientas de Windows 10 y que el dispositivo esté en modo de desarrollador.

Una vez que Visual Studio termina de crear la plantilla, podemos ejecutarla para ver el proyecto vacío en ejecución:

![Una vez que Visual Studio termine de crear la plantilla, ejecútela para ver el proyecto vacío en ejecución](uwp-images/image3.png)

Los números de las esquinas proporcionan información de diagnóstico. Esta información se puede quitar eliminando el código en `App.xaml.cs` el bloque del `DEBUG` `OnLaunched` método:

```csharp
protected override void OnLaunched(LaunchActivatedEventArgs e)
{
#if DEBUG
    if (System.Diagnostics.Debugger.IsAttached)
    {
        this.DebugSettings.EnableFrameRateCounter = true;
    }
#endif
    ...
```

## <a name="running-on-xbox-one"></a>Ejecutar en Xbox One

Los proyectos de UWP se pueden implementar en cualquier dispositivo de Windows 10 desde el mismo proyecto. Después de configurar el equipo de desarrollo de Windows 10 y la Xbox One, las aplicaciones UWP se pueden implementar cambiando el destino a la máquina remota y escribiendo la dirección IP de la Xbox One:

![Las aplicaciones UWP se pueden implementar cambiando el destino a la máquina remota y escribiendo la dirección IP de la Xbox.](uwp-images/remote.png)

En Xbox One, el borde blanco representa el área no segura de los televisores. Para obtener más información, consulte la [sección área segura](#safe-area-on-xbox-one).

![En Xbox One, el borde blanco representa el área no segura de los televisores](uwp-images/safearea.png)

### <a name="safe-area-on-xbox-one"></a>Área segura en Xbox One

Para desarrollar juegos para consolas, es necesario tener en cuenta el área segura, que es un área del centro de la pantalla que debe contener todos los objetos visuales críticos (como la interfaz de usuario o HUD). No se garantiza que el área fuera del área segura esté visible en todos los televisores, por lo que los objetos visuales colocados en esta área pueden ser parcial o totalmente invisibles en algunas pantallas.

La plantilla monogame para Xbox One considera el área segura y la representa como un borde blanco. Esta área también se refleja en tiempo de ejecución en la `Window.ClientBounds` propiedad del juego, tal como se muestra en esta imagen de la ventana Inspección de Visual Studio. Observe que el alto de los límites del cliente es 1016, a pesar de la resolución de pantalla de 1920 x 1080:

![Observe que el alto de los límites del cliente es 1016, a pesar de la resolución de pantalla de 1920 x 1080](uwp-images/clientbounds.png)

## <a name="referencing-content-in-uwp-projects"></a>Hacer referencia al contenido en proyectos de UWP

Se puede hacer referencia al contenido de los proyectos de monogame directamente desde el archivo o a través de la [canalización de contenido de monogame](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md). Los proyectos de juegos pequeños pueden beneficiarse de la simplicidad de carga desde el archivo. Los proyectos más grandes se beneficiarán del uso de la canalización de contenido para optimizar el contenido con el fin de reducir el tamaño y los tiempos de carga. A diferencia de XNA en la Xbox 360, la `System.IO.File` clase está disponible en las aplicaciones de Xbox One UWP.

Para obtener más información sobre cómo cargar contenido mediante la canalización de contenido, consulte la [Guía de canalizaciones de contenido](https://github.com/xamarin/docs-archive/blob/master/Docs/CocosSharp/content-pipeline/introduction.md).

### <a name="loading-content-from-file"></a>Cargando contenido desde el archivo

A diferencia de iOS y Android, los proyectos de UWP pueden hacer referencia a archivos relacionados con el archivo ejecutable. Los juegos sencillos pueden usar esta técnica para cargar contenido sin necesidad de modificar y compilar el proyecto de canalización de contenido.

Para cargar un `Texture2D` desde un archivo:

1. Agregue un archivo. png a la carpeta de contenido en el proyecto de UWP. Agregar contenido a la carpeta de contenido es una Convención en XNA y monogame.
1. Haga clic con el botón derecho en el PNG recién agregado y seleccione Propiedades.
1. Cambie la **copia al directorio de salida** a **copiar si es posterior**.
1. Agregue el código siguiente al método Initialize del juego para cargar un `Texture2D` :

    ```csharp
    Texture2D texture;
    using (var stream = System.IO.File.OpenRead("Content/YourPngName.png"))
    {
        texture = Texture2D.FromStream(graphics.GraphicsDevice, stream);
    }
    ```

Para obtener más información sobre el uso de `Texture2D` , consulte la [Guía de introducción a monogame](~/graphics-games/monogame/introduction/index.md).

## <a name="summary"></a>Resumen

En esta guía se explica cómo crear un nuevo proyecto de UWP y consideraciones específicas de UWP al cargar archivos. Los desarrolladores interesados en crear juegos de UWP completos pueden leer más sobre monogame en la [Guía de introducción a monogame](~/graphics-games/monogame/introduction/index.md).
