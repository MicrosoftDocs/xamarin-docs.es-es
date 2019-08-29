---
title: Instalación y uso de watchOS en Xamarin
description: Este documento describe cómo instalar y usar watchOS con Xamarin. Describe la instalación, los proyectos de watchOS estructura, cómo usar el Diseñador de iOS, la integración de Xcode y proporciona sugerencias para solucionar problemas.
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 12/05/2017
ms.openlocfilehash: b4d827715870d54de3116067a3e24e229ecda530
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065620"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>Instalación y uso de watchOS en Xamarin

watchOS 4 requiere macOS Sierra (10.12) con Xcode 9.

originalmente, watchOS 1 requiere OS X Yosemite (10.10) con Xcode 7.

> [!WARNING]
> [no se aceptarán las actualizaciones de watchOS 1 después del 1 de abril de 2018](https://developer.apple.com/news/?id=11162017a). Las actualizaciones futuras deben usar watchOS 2 SDK o posterior; compilar con watchOS 4 del SDK se recomienda.

## <a name="project-structure"></a>Estructura del proyecto

Una aplicación de inspección consta de tres proyectos:

- **Proyecto de aplicación de iPhone de Xamarin.iOS** -se trata de un proyecto de iPhone normal, puede ser cualquiera de las plantillas de Xamarin.iOS. La aplicación del reloj y su extensión incluidas dentro de este proyecto principal.

- **Proyecto de extensión de inspección** : contiene el código (por ejemplo, las clases de controlador) de la aplicación de inspección.

- **Proyecto** de la aplicación de inspección: contiene el archivo de guion gráfico de la interfaz de usuario con todos los recursos de la interfaz de usuario para la aplicación de inspección.

El [ejemplo inspección Kit Catalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) solución tiene este aspecto en Xamarin.Studio:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](installation-images/catalog-solution.png "La solución en Visual Studio")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/catalog-solution-vs.png "La solución en Visual Studio")

-----

Descargue y ejecute el [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) ejemplo para empezar a trabajar.
Las pantallas del ejemplo pueden encontrarse en el [controles](~/ios/watchos/user-interface/index.md) página.


## <a name="creating-a-new-project"></a>Crear un proyecto nuevo

No se puede crear una nueva "inspección solución"... en su lugar puede agregar una aplicación de inspección para una aplicación iOS existente. Siga estos pasos para crear una aplicación de inspección:

1. Si no tienes un proyecto existente, primero elija **archivo > nueva solución** y crear una aplicación de iOS (por ejemplo, un **aplicación de vista única**):

    [![](installation-images/cycle8-2-sml.png "Seleccione Archivo > nueva solución y crear una aplicación iOS")](installation-images/cycle8-2.png#lightbox)

2. Una vez creada la aplicación de iOS (o va a usar la aplicación iOS existente), haga doble clic en la solución y elija **Agregar > Agregar nuevo proyecto...** . En el **nuevo proyecto** ventana Seleccione **watchOS > aplicación > WatchKit App**:

    [![](installation-images/cycle8-6-sml.png "Seleccione watchOS > aplicación > WatchKit App")](installation-images/cycle8-6.png#lightbox)

3. La siguiente pantalla le permite elegir qué proyecto de aplicación de iOS debe incluir la aplicación del reloj:

    [![](installation-images/cycle8-7-sml.png "Elija el proyecto de aplicación de iOS debe incluir la aplicación del reloj")](installation-images/cycle8-7.png#lightbox)

4. Por último, elija la ubicación para guardar el proyecto (y, opcionalmente, habilita el control de código fuente):

    [![](installation-images/cycle8-8-sml.png "Elija la ubicación para guardar el proyecto")](installation-images/cycle8-8.png#lightbox)

5. Visual Studio para Mac configura automáticamente [referencias de proyecto y **Info.plist** configuración](~/ios/watchos/get-started/project-references.md) para usted.

## <a name="creating-the-watch-user-interface"></a>Creación de la interfaz de usuario de inspección

<a name="designer" />

### <a name="using-the-xamarin-ios-designer"></a>Mediante el Diseñador de iOS de Xamarin

Haga doble clic en la aplicación watch **Interface.storyboard** para editar en el Diseñador de iOS. Puede arrastrar los controladores de interfaz y los controles de interfaz de usuario en el guión gráfico desde el **cuadro de herramientas** y configurarlos mediante el **propiedades** panel:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

[![](installation-images/iosdesigner-sml.png "El guión gráfico en el diseñador")](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](installation-images/iosdesigner-sml-vs.png "El guión gráfico en el diseñador")](installation-images/iosdesigner-vs.png#lightbox)

-----

Debe dar a cada nuevo controlador de interfaz un **clase** , selecciónela y, a continuación, escriba el nombre de la **propiedades** pad (Esto creará los C# automáticamente archivos de código subyacente):

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

![](installation-images/iosdesigner-classname.png "Asigne a cada nuevo controlador de interfaz en una clase")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](installation-images/iosdesigner-classname-vs.png "Asigne a cada nuevo controlador de interfaz en una clase")

-----

Crear los objetos Segue por **Ctrl + arrastrar** desde un controlador de botón, la tabla o la interfaz a otro controlador de interfaz.


### <a name="using-xcode-on-the-mac"></a>Uso de Xcode en el equipo Mac

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

Aún puede usar Xcode para compilar la interfaz de usuario con el botón secundario en el archivo Interface.storyboard y seleccionando **abrir con > Interface Builder de Xcode**:

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Los usuarios de Visual Studio también pueden usar Xcode para compilar la interfaz de usuario mediante el traslado a usar directamente el Host de compilación de Mac.
Abra la solución en Visual Studio para Mac y, a continuación, haga doble clic en el archivo Interface.storyboard y seleccione **abrir con > Interface Builder de Xcode**:

-----

![](installation-images/openwith-xcode.png "Abra el Interface.storyboard en Interface Builder de Xcode")

Si usa Xcode, debe seguir los mismos pasos para las aplicaciones de watch en cuanto a normal [guiones gráficos de aplicación de iOS](~/ios/user-interface/storyboards/index.md) (como la creación de salidas y acciones por **Ctrl + arrastrar** en el **.h**archivo de encabezado).

Cuando guarda el guión gráfico en Interface Builder de Xcode agregará automáticamente las salidas y acciones que crear para el C# **. designer.cs** los archivos del proyecto de extensión de inspección.


### <a name="adding-additional-screens-in-xcode"></a>Adición de pantallas adicionales en Xcode

Al agregar pantallas adicionales (más allá de lo que se incluye en la plantilla de forma predeterminada) para el guion gráfico con Interface Builder de Xcode **debe agregar manualmente el C# archivos de código** para cada nuevo controlador de interfaz.

Hacer referencia a la [avanzada instrucciones sobre cómo agregar nuevos controladores de interfaz a un guión gráfico](~/ios/watchos/troubleshooting.md#add).

*El Diseñador de iOS de Xamarin hace esto automáticamente, se requiere ningún paso manual.*


## <a name="building"></a>Compilación

Compila un proyecto que incluye una aplicación de inspección, como otros proyectos de iOS. El proceso de creación dará como resultado en una aplicación de iPhone (.app) que contiene una extensión de inspección (.appex), que a su vez contiene la aplicación de inspección de código de menor (.app).


## <a name="launching"></a>Iniciar

Puede iniciar las aplicaciones de watch en el simulador mediante Visual Studio para Mac o Visual Studio (se inicia en el Host de compilación de Mac).

Existen dos modos para iniciar una aplicación:

- modo de aplicación normal (predeterminado), y
- [Las notificaciones](~/ios/watchos/platform/notifications.md) (lo que requiere una carga de notificación de prueba en formato JSON).

### <a name="xcode-8-support"></a>Compatibilidad con Xcode 8

Una vez instalado Xcode 8 (o posterior), simuladores de Apple Watch son independientes de los simuladores de iOS (a diferencia de [Xcode 6](#xcode6), donde aparecían como un *pantalla externa*).
Cuando se selecciona el proyecto de aplicación de inspección y asegúrese de el proyecto de inicio, se mostrará la lista del simulador *simuladores de iOS* elijan (como se muestra a continuación).

[![](installation-images/xs-xcode8-watchos3-sml.png "Seleccionar el tipo de simulador")](installation-images/xs-xcode8-watchos3.png#lightbox)

Al iniciar la depuración, *dos* debe iniciar simuladores: el simulador de iOS *y* el simulador de Apple Watch. Usar **comando + Mayús + H** para navegar hasta la pantalla del menú y el reloj del reloj; y utilice el **Hardware** menú para establecer el **Force Touch presión**. Desplazamiento en el panel de seguimiento o el mouse para simular mediante la corona Digital.

#### <a name="troubleshooting"></a>Solución de problemas

Aparecerá el siguiente error en la **resultado de la aplicación** si intenta iniciar en el simulador que no tiene una inspección emparejada:

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

Consulte [foros de Apple](https://forums.developer.apple.com/thread/7783) para obtener instrucciones sobre cómo configurar los simuladores, si los valores predeterminados no funcionan.


<a name="xcode6" />

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 y watchOS 1

Debe realizar la *proyecto de extensión de inspección* el **proyecto de inicio** antes de ejecutar o depurar la aplicación. No se puede "iniciar" la propia aplicación de inspección y, si elige la aplicación de iOS, a continuación, se iniciará con normalidad en el simulador de iOS.

De forma predeterminada se inicia una aplicación de inspección en normal **aplicación** (modo no vistazo o notificaciones) de Visual Studio para Mac **ejecutar** o **depurar** comandos.

Al usar Xcode 6, solo el iPhone 5, iPhone 5S, iPhone 6 y iPhone 6 Plus puede activar la pantalla externa cualquiera **Apple Watch - 38mm** o **Apple Watch - 42mm** donde las aplicaciones de watch será muestra.

> [!NOTE]
> Recuerde que la pantalla inspección no aparece automáticamente en el simulador de iOS cuando se usa Xcode 6.
> Use la **Hardware > pantallas externas** menú para mostrar la pantalla de inspección.

<a name="custommodes" />

## <a name="launching-notification-mode"></a>Iniciar modo de notificación

Hacer referencia a la [página notificaciones](~/ios/watchos/platform/notifications.md) para obtener información cómo controlar las notificaciones en el código.


Visual Studio para Mac puede iniciar la aplicación watch con una notificación _modos de inicio_ para las notificaciones:



Haga doble clic en el proyecto de aplicación de inspección y elija **ejecutar con > configuración personalizada...** :


[![](installation-images/runwith-customparams-sml.png "Ejecución de una configuración personalizada")](installation-images/runwith-customparams.png#lightbox)


Se abrirá el **parámetros personalizados** ventana donde puede seleccionar **notificación** (y proporcionar una carga JSON), a continuación, presione **ejecutar** para iniciar la aplicación del reloj en el simulador:


[![](installation-images/runwith-execargs-sml.png "Configuración de la notificación y la carga")](installation-images/runwith-execargs.png#lightbox)



## <a name="debugging"></a>Depuración

Se admite la depuración en Visual Studio para Mac y Visual Studio.
Recuerde que debe proporcionar un archivo JSON de notificación cuando se depura en modo de notificaciones. Esta captura de pantalla muestra un punto de interrupción de depuración que se vea afectado en una aplicación de inspección:

![](installation-images/debug-sml.png "Esta captura de pantalla muestra un punto de interrupción de depuración que se vea afectado en una aplicación de inspección")

Después de seguir las instrucciones de inicio terminará con la aplicación del reloj que se ejecutan en el **iOS Simulator (Watch)** .
Para el modo de notificación se puede seleccionar **Depurar > Abrir registro sistema** (**CMD + /** ) y usar `Console.WriteLine` en el código.

### <a name="debugging-lifecycle-event-handlers"></a>Depuración de controladores de eventos de ciclo de vida

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

Los archivos de plantilla de watchOS (como `InterfaceController`, `ExtensionDelegate`, `NotificationController`, y `ComplicationController`) vienen con sus métodos de ciclo de vida necesarios ya implementados. Agregar `Console.WriteLine` llamadas y leer el **resultado de la aplicación** para comprender mejor el ciclo de vida del evento.



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Primer vídeo de la aplicación del reloj](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Sugerencias de WatchKit de Apple](https://developer.apple.com/watchkit/tips/)
