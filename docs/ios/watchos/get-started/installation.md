---
title: Instalación y uso de watchos en Xamarin
description: En este documento se describe cómo instalar y usar watchos con Xamarin. Se describe la instalación, la estructura del proyecto watchos, cómo usar iOS Designer, la integración de Xcode y se proporcionan sugerencias para la solución de problemas.
ms.prod: xamarin
ms.assetid: 69F21F15-198D-4B42-A703-21D35CAB0CCA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 12/05/2017
ms.openlocfilehash: 89d074e14f2a66d472f06acecf42127a7ff44c4c
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91437371"
---
# <a name="installing-and-using-watchos-in-xamarin"></a>Instalación y uso de watchos en Xamarin

watchos 4 requiere macOS Sierra (10,12) con Xcode 9.

watchos 1 requería originalmente OS X Yosemite (10,10) con Xcode 7.

> [!WARNING]
> [no se aceptarán actualizaciones de watchos 1 después del 1 de abril de 2018](https://developer.apple.com/news/?id=11162017a). Las actualizaciones futuras deben usar el SDK de watchos 2 o posterior. se recomienda compilar con el SDK de watchos 4.

## <a name="project-structure"></a>Estructura del proyecto

Una aplicación de inspección consta de tres proyectos:

- **Proyecto de aplicación de iPhone de Xamarin. iOS** : se trata de un proyecto de iPhone normal, que puede ser cualquiera de las plantillas de Xamarin. iOS. La aplicación de inspección y su extensión se agruparán dentro de este proyecto principal.

- **Proyecto de extensión de inspección** : contiene el código (por ejemplo, las clases de controlador) de la aplicación de inspección.

- **Proyecto** de la aplicación de inspección: contiene el archivo de guion gráfico de la interfaz de usuario con todos los recursos de la interfaz de usuario para la aplicación de inspección.

La solución de [ejemplo de catálogo del kit de inspección](/samples/xamarin/ios-samples/watchos-watchkitcatalog) tiene este aspecto en Xamarin. Studio:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![La solución en Visual Studio](installation-images/catalog-solution.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![La solución en Visual Studio](installation-images/catalog-solution-vs.png)

-----

Descargue y ejecute el ejemplo [WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog) para comenzar.
Las pantallas del ejemplo se pueden encontrar en la página [controles](~/ios/watchos/user-interface/index.md) .

## <a name="creating-a-new-project"></a>Crear un proyecto nuevo

No se puede crear una nueva "inspección de la solución"... en su lugar, puede Agregar una aplicación de inspección a una aplicación de iOS existente. Siga estos pasos para crear una aplicación de inspección:

1. Si no tiene un proyecto existente, elija primero **archivo > nueva solución** y cree una aplicación de iOS (por ejemplo, una **aplicación de vista única**):

    [![Elegir archivo > nueva solución y crear una aplicación iOS](installation-images/cycle8-2-sml.png)](installation-images/cycle8-2.png#lightbox)

2. Una vez creada la aplicación iOS (o planea usar la aplicación iOS existente), haga clic con el botón derecho en la solución y elija **agregar > agregar nuevo proyecto..**. En la ventana **nuevo proyecto** , seleccione **watchos > aplicación > aplicación WatchKit**:

    [![Selección de la aplicación watchos > App > WatchKit](installation-images/cycle8-6-sml.png)](installation-images/cycle8-6.png#lightbox)

3. La siguiente pantalla le permite elegir qué proyecto de aplicación iOS debe incluir la aplicación Watch:

    [![Elección del proyecto de aplicación de iOS que debe incluir la aplicación de inspección](installation-images/cycle8-7-sml.png)](installation-images/cycle8-7.png#lightbox)

4. Por último, elija la ubicación para guardar el proyecto (y, opcionalmente, habilite el control de código fuente):

    [![Elegir la ubicación para guardar el proyecto](installation-images/cycle8-8-sml.png)](installation-images/cycle8-8.png#lightbox)

5. Visual Studio para Mac configura automáticamente [las referencias del proyecto y la configuración de **info. plist** ](~/ios/watchos/get-started/project-references.md) .

## <a name="creating-the-watch-user-interface"></a>Crear la interfaz de usuario de Watch

<a name="designer"></a>

### <a name="using-the-xamarin-ios-designer"></a>Usar el diseñador de Xamarin iOS

Haga doble clic en la aplicación de inspección **interface. Storyboard** para editarla con el diseñador de iOS. Puede arrastrar controladores de interfaz y controles de interfaz de usuario al guion gráfico desde el **cuadro de herramientas** y configurarlos mediante el panel de **propiedades** :

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

[![El guión gráfico en el diseñador](installation-images/iosdesigner-sml.png)](installation-images/iosdesigner.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![El guión gráfico en el diseñador](installation-images/iosdesigner-sml-vs.png)](installation-images/iosdesigner-vs.png#lightbox)

-----

Debe proporcionar a cada controlador de interfaz nuevo una **clase** seleccionándola y escribiendo el nombre en el panel de **propiedades** (Esto creará automáticamente los archivos de código subyacente de C# necesarios):

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

![Asigne a cada controlador de interfaz nuevo una clase](installation-images/iosdesigner-classname.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Asigne a cada controlador de interfaz nuevo una clase](installation-images/iosdesigner-classname-vs.png)

-----

Cree objetos segue con **Ctrl + arrastrando** desde un botón, una tabla o un controlador de interfaz a otro controlador de interfaz.

### <a name="using-xcode-on-the-mac"></a>Uso de Xcode en el equipo Mac

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Puede seguir usando Xcode para compilar la interfaz de usuario haciendo clic con el botón derecho en el archivo interface. Storyboard y seleccionando **abrir con > Xcode Interface Builder**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Los usuarios de Visual Studio también pueden usar Xcode para compilar la interfaz de usuario cambiando para usar el host de compilación de Mac directamente.
Abra la solución en Visual Studio para Mac y, a continuación, haga clic con el botón derecho en el archivo interface. Storyboard y seleccione **abrir con > Xcode Interface Builder**:

-----

![Abra el. Storyboard en Xcode Interface Builder](installation-images/openwith-xcode.png)

Si usa Xcode, debe seguir los mismos pasos para ver las aplicaciones de los [guiones gráficos de la aplicación iOS](~/ios/user-interface/storyboards/index.md) normal (como la creación de salidas y acciones, mediante **Ctrl + arrastrar** al archivo de encabezado **. h** ).

Al guardar el guion gráfico en Xcode Interface Builder agregará automáticamente las salidas y acciones que cree a los archivos de C# **. Designer.CS** en el proyecto de extensión de inspección.

### <a name="adding-additional-screens-in-xcode"></a>Agregar pantallas adicionales en Xcode

Al agregar más pantallas (más allá de lo que se incluye en la plantilla de forma predeterminada) al guion gráfico mediante Xcode Interface Builder **debe agregar manualmente los archivos de código de C#** para cada nuevo controlador de interfaz.

Consulte las [instrucciones avanzadas sobre cómo agregar nuevos controladores de interfaz a un guion gráfico](~/ios/watchos/troubleshooting.md#add).

*Xamarin iOS Designer hace esto automáticamente, no se requiere ningún paso manual.*

## <a name="building"></a>Compilación

Un proyecto que incluye una aplicación de inspección se compila como otros proyectos de iOS. El proceso de compilación producirá una aplicación de iPhone (. app) que contiene una extensión de inspección (. APPEX) que, a su vez, contiene la aplicación de inspección sin código (. app).

## <a name="launching"></a>Inicio

Puede iniciar las aplicaciones de inspección en el simulador mediante Visual Studio para Mac o Visual Studio (se inicia en el host de compilación de Mac).

Hay dos modos para iniciar una aplicación de WatchKit:

- modo normal de la aplicación (el valor predeterminado) y
- [Notificaciones](~/ios/watchos/platform/notifications.md) (que requieren una carga de notificación de prueba en formato JSON).

### <a name="xcode-8-support"></a>Compatibilidad con Xcode 8

Una vez instalado Xcode 8 (o posterior), Apple Watch simuladores son independientes de los simuladores de iOS (a diferencia de [Xcode 6](#xcode6), donde aparecían como una *pantalla externa*).
Al seleccionar el proyecto de la aplicación de inspección y convertirlo en el proyecto de inicio, la lista del simulador mostrará *simuladores de iOS* entre los que elegir (como se muestra a continuación).

[![Seleccionar el tipo de simulador](installation-images/xs-xcode8-watchos3-sml.png)](installation-images/xs-xcode8-watchos3.png#lightbox)

Al iniciar la depuración, deben iniciarse *dos* simuladores: el simulador de iOS *y* el simulador de Apple Watch. Use **comando + Mayús + H** para navegar hasta el menú inspección y la esfera del reloj; y use el menú **hardware** para establecer la **presión de Force Touch**. El desplazamiento en el botón de la barra táctil o el mouse se simulará mediante el Digital Crown.

#### <a name="troubleshooting"></a>Solución de problemas

El siguiente error aparecerá en el resultado de la **aplicación** si intenta iniciar en un simulador que no tenga una inspección emparejada:

```csharp
error MT0000: Unexpected error - Please file a bug report at https://github.com/xamarin/xamarin-macios/issues/new
error HE0020: Could not find a paired Watch device for the iOS device 'iPhone 6'.
```

Consulte los [foros de Apple](https://forums.developer.apple.com/thread/7783) para obtener instrucciones sobre cómo configurar los simuladores, si los valores predeterminados no funcionan.

<a name="xcode6"></a>

### <a name="xcode-6-and-watchos-1"></a>Xcode 6 y watchos 1

Debe hacer que el proyecto de *extensión de inspección* sea el **proyecto de inicio** antes de ejecutar o depurar la aplicación. No puede "iniciar" la propia aplicación de inspección y, si elige la aplicación iOS, se iniciará como normal en el simulador de iOS.

De forma predeterminada, una aplicación de inspección se inicia en el modo de **aplicación** normal (no en modo de vista o notificación) desde los comandos de **ejecución** o **depuración** de Visual Studio para Mac.

Al usar Xcode 6, solo el iPhone 5, iPhone 5 S, iPhone 6 y iPhone 6 Plus pueden activar la pantalla externa para **Apple Watch-38mm** o **Apple Watch-42mm** donde se mostrarán las aplicaciones de inspección.

> [!NOTE]
> Recuerde que la pantalla inspección no aparece automáticamente en el simulador de iOS cuando se usa Xcode 6.
> Use el menú **Hardware > pantallas externas** para mostrar la pantalla de inspección.

<a name="custommodes"></a>

## <a name="launching-notification-mode"></a>Inicio del modo de notificación

Consulte la [Página notificaciones](~/ios/watchos/platform/notifications.md) para obtener información sobre cómo controlar las notificaciones en el código.

Visual Studio para Mac puede iniciar la aplicación de inspección con _modos de inicio_ de notificación para las notificaciones:

Haga clic con el botón derecho en el proyecto de la aplicación de inspección y elija **ejecutar con > configuración personalizada...**:

[![Ejecución de una configuración personalizada](installation-images/runwith-customparams-sml.png)](installation-images/runwith-customparams.png#lightbox)

Se abrirá la ventana **parámetros personalizados** , donde puede seleccionar **notificación** (y proporcionar una carga JSON) y luego presionar **Ejecutar** para iniciar la aplicación de inspección en el simulador:

[![Establecimiento de la notificación y la carga](installation-images/runwith-execargs-sml.png)](installation-images/runwith-execargs.png#lightbox)

## <a name="debugging"></a>Depuración

La depuración se admite en Visual Studio para Mac y Visual Studio.
No olvide proporcionar un archivo JSON de notificación al depurar en modo de notificaciones. Esta captura de pantalla muestra un punto de interrupción de depuración que se alcanza en una aplicación de inspección:

![Esta captura de pantalla muestra un punto de interrupción de depuración que se alcanza en una aplicación de inspección](installation-images/debug-sml.png)

Después de seguir las instrucciones de inicio, terminará con la aplicación de inspección que se ejecuta en el **simulador de iOS (ver)**.
En el modo de notificación, puede seleccionar **Depurar > abrir el registro del sistema** (**cmd +/**) y usarlo `Console.WriteLine` en el código.

### <a name="debugging-lifecycle-event-handlers"></a>Depurar controladores de eventos de ciclo de vida

<!--
To test the functionality in your  and 
  methods, use the **Hardware > Lock** command in the iOS Simulator.
  Locking will trigger the `DidDeactivate` method and the watch simulator
  will indicate that it has been locked. Swipe the iOS Simulator to unlock,
  which triggers the `WillActivate` method of the watch app.
-->

Los archivos de plantilla de watchos (como `InterfaceController` ,, `ExtensionDelegate` `NotificationController` y `ComplicationController` ) se incorporan a los métodos de ciclo de vida necesarios ya implementados. Agregue `Console.WriteLine` llamadas y lea la **salida** de la aplicación para comprender mejor el ciclo de vida de los eventos.

## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Vídeo de la primera aplicación de inspección](https://blog.xamarin.com/your-first-watch-kit-app/)
- [Sugerencias de WatchKit de Apple](https://developer.apple.com/watchkit/tips/)