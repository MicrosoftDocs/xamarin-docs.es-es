---
title: Introducción a Xamarin.iOS para Visual Studio
description: En este documento se describe cómo compilar y probar aplicaciones Xamarin.iOS con Visual Studio. Se explica también cómo crear un proyecto, ejecutar y depurar una aplicación, y conectarse a un host de compilación de Mac desde Windows.
ms.prod: xamarin
ms.assetid: bf3c779f-959f-428d-babb-428f363f7e4e
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2018
ms.openlocfilehash: 2650bda7115cd132e87bf669914320f63214e295
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86933229"
---
# <a name="introduction-to-xamarinios-for-visual-studio"></a>Introducción a Xamarin.iOS para Visual Studio

Xamarin para Windows permite escribir y probar aplicaciones de iOS en Visual Studio con un equipo Mac en red que proporciona el servicio de compilación e implementación.

En este artículo se tratan los pasos para instalar y configurar las herramientas de Xamarin.iOS en cada equipo para compilar aplicaciones de iOS con Visual Studio.

El desarrollo para iOS en Visual Studio ofrece una serie de ventajas:

- Creación de soluciones multiplataforma para aplicaciones de iOS, Android y Windows.
- Uso de las herramientas de Visual Studio preferidas por el usuario (como **Resharper** y **Team Foundation Server**) para todos los proyectos multiplataforma, incluido el código de origen de iOS.
- Trabajo con un IDE conocido aprovechando los enlaces de Xamarin.iOS de todas las API de Apple.

## <a name="requirements-and-installation"></a>Requisitos e instalación

Al desarrollar para iOS en Visual Studio es necesario cumplir algunos requisitos. Como se ha mencionado brevemente en la información general, se necesita un equipo Mac para compilar archivos IPA y no es posible implementar aplicaciones en un dispositivo sin certificados de Apple ni herramientas de firma de código.

Hay una serie de opciones de configuración disponibles para poder decidir cuál funciona mejor para las necesidades de desarrollo. Se enumeran a continuación:

- Usar un equipo Mac como equipo de desarrollo principal y ejecutar una máquina virtual de Windows con Visual Studio instalado. Se recomienda usar software de máquina virtual como [Parallels](https://www.parallels.com/products/desktop/) o [VMWare](https://www.vmware.com/products/fusion/).
- Usar un equipo Mac solo como host de compilación. En este caso, se conectaría a la misma red que una máquina de Windows con las herramientas [necesarias](~/get-started/installation/windows.md#installation) instaladas.

En cualquier caso, debe seguir estos pasos:

- [Instalación de Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation)
- [Instalar las herramientas de Xamarin en Windows](~/get-started/installation/windows.md)

## <a name="connecting-to-the-mac"></a>Conexión al equipo Mac

Para conectar Visual Studio a su host de compilación de Mac, siga las instrucciones que aparecen en la guía [Emparejar con Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

## <a name="visual-studio-toolbar-overview"></a>Información general sobre la barra de herramientas de Visual Studio

Xamarin iOS para Visual Studio agrega elementos a la barra de herramientas estándar y a la nueva barra de herramientas de iOS.
A continuación se explican las funciones de estas barras de herramientas.

### <a name="standard-toolbar"></a>Barra de herramientas estándar

Los controles relevantes para el desarrollo de Xamarin iOS se han rodeado con un círculo rojo:

[![Los controles pertinentes para el desarrollo en Xamarin iOS se han rodeado con un círculo de color rojo.](introduction-to-xamarin-ios-for-visual-studio-images/03.png)](introduction-to-xamarin-ios-for-visual-studio-images/03.png#lightbox "Los controles relevantes para el desarrollo de Xamarin iOS se han rodeado con un círculo de color rojo")

- **Iniciar**: inicia la depuración o la ejecución de la aplicación en la plataforma seleccionada. Debe haber un equipo Mac conectado (vea el indicador de estado en la barra de herramientas de iOS).
- **Configuraciones de soluciones**: permite seleccionar la configuración que se va a usar (por ejemplo, Depurar, Publicar).
- **Plataformas de solución**: permite seleccionar iPhone o iPhoneSimulator para la implementación.

### <a name="ios-toolbar"></a>Barra de herramientas de iOS

El aspecto de la barra de herramientas de iOS de Visual Studio es similar en todas las versiones de Visual Studio. Se muestra a continuación:

[![Barra de herramientas de iOS](introduction-to-xamarin-ios-for-visual-studio-images/iostoolbar.png)](introduction-to-xamarin-ios-for-visual-studio-images/iostoolbar.png#lightbox)

Cada elemento se explica a continuación:

- **Mac Agent/Connection Manager**: muestra el cuadro de diálogo Xamarin Mac Agent. Este icono se verá *naranja* mientras se conecta y *verde* cuando está conectado.
- **Mostrar simulador de iOS**: coloca en primer plano la ventana del simulador de iOS en el equipo Mac.
- **Mostrar archivo IPA en servidor de compilación**: abre la aplicación Finder de Mac en la ubicación del archivo de salida IPA de la aplicación.

## <a name="ios-output-options"></a>Opciones de salida de iOS

### <a name="output-window"></a>Resultados (Ventana)

Hay opciones del panel *Salida* que se pueden ver para detectar errores y mensajes de compilación, implementación y conexión.

La captura de pantalla siguiente muestra las ventanas de salida disponibles, que pueden variar según el tipo de proyecto:

[![Ventanas de salida disponibles](introduction-to-xamarin-ios-for-visual-studio-images/output-sml.png)](introduction-to-xamarin-ios-for-visual-studio-images/output-large.png#lightbox)

- **Xamarin**: contiene información relacionada únicamente con Xamarin, como la conexión con el equipo Mac y el estado de activación.

  [![Información relacionada únicamente con Xamarin, como la conexión con el equipo Mac y el estado de activación](introduction-to-xamarin-ios-for-visual-studio-images/output3-sml.png)](introduction-to-xamarin-ios-for-visual-studio-images/output3-large.png#lightbox)

- **Diagnósticos de Xamarin**: muestra información más detallada sobre el proyecto de Xamarin, como la interacción con adb para Android.

  [![Información detallada sobre el proyecto de Xamarin](introduction-to-xamarin-ios-for-visual-studio-images/output4-sml.png)](introduction-to-xamarin-ios-for-visual-studio-images/output3-large.png#lightbox)

Otros paneles de salida predeterminados de Visual Studio como Depurar y Compilar siguen estando disponibles en la vista de salida y se usan para la salida de depuración y la salida de MSBuild:

- **Depurar**

  [![Salida de depuración](introduction-to-xamarin-ios-for-visual-studio-images/output2-sml.png)](introduction-to-xamarin-ios-for-visual-studio-images/output2-large.png#lightbox)

- **Compilar** & **Orden de compilación**

  [![Salida de MSBuild](introduction-to-xamarin-ios-for-visual-studio-images/output1-sml.png)](introduction-to-xamarin-ios-for-visual-studio-images/output1-large.png#lightbox)

## <a name="ios-project-properties"></a>Propiedades de proyecto de iOS

Es posible acceder a las propiedades del proyecto de Visual Studio si se hace clic con el botón derecho en el nombre del proyecto y se selecciona *Propiedades* en el menú contextual. Esto le permitirá configurar la aplicación de iOS, como se muestra en la siguiente captura de pantalla:

![Configuración de una aplicación de iOS](introduction-to-xamarin-ios-for-visual-studio-images/iosproperties.png)

- *Firma del lote de iOS*: se conecta al equipo Mac para rellenar las identidades de firma de código y los perfiles de aprovisionamiento:

  ![Rellene las identidades de firma de código y los perfiles de aprovisionamiento](introduction-to-xamarin-ios-for-visual-studio-images/bundlesigning.png)

- *Opciones IPA de iOS*: el archivo IPA se guardará en el sistema de archivos del equipo Mac:

  ![Opciones de IPA de iOS](introduction-to-xamarin-ios-for-visual-studio-images/ipaoptions.png)

- *Opciones de ejecución de iOS*: configure parámetros adicionales:

  ![Opciones de ejecución de iOS](introduction-to-xamarin-ios-for-visual-studio-images/iosrunoptions.png)

## <a name="creating-a-new-project-for-ios-applications"></a>Creación de un nuevo proyecto para aplicaciones de iOS

La creación de un nuevo proyecto de iOS desde Visual Studio se hace igual que cualquier otro tipo de proyecto. Al seleccionar **Archivo > Nuevo proyecto** se abre el cuadro de diálogo siguiente, que muestra algunos de los tipos de proyectos disponibles para crear un nuevo proyecto de iOS:

![Crear un proyecto nuevo](introduction-to-xamarin-ios-for-visual-studio-images/newproject.w157.png)

Al seleccionar **Aplicación de iOS (Xamarin)** se muestran las plantillas siguientes para crear una nueva aplicación Xamarin.iOS:

![Selección de la plantilla para una aplicación iOS](introduction-to-xamarin-ios-for-visual-studio-images/newproject-2.w157.png)

El guion gráfico y los archivos .xib se pueden editar en Visual Studio mediante el diseñador de iOS. Para crear un guion gráfico, elija una de las plantillas de guion gráfico. Esto generará un archivo **Main.storyboard** en el **Explorador de soluciones**, como se muestra en la siguiente captura de pantalla:

![Archivo Main.storyboard en el Explorador de soluciones](introduction-to-xamarin-ios-for-visual-studio-images/solution-explorer-new.w157.png)

Para empezar a crear o editar el guion gráfico, haga doble clic en `Main.storyboard` para abrirlo en el diseñador de iOS:

![Main.storyboard en el Diseñador de iOS](introduction-to-xamarin-ios-for-visual-studio-images/iosdesigner.png)

Para agregar objetos a la vista, use el panel **Cuadro de herramientas** para arrastrar y colocar elementos en la superficie de diseño. Si aún no está agregado, el cuadro de herramientas se puede agregar si se selecciona **Ver > Cuadro de herramientas**. Las propiedades de los objetos se pueden modificar, su diseño se puede ajustar y se pueden crear eventos mediante el panel **Propiedades**, como se muestra a continuación:

![Panel Propiedades](introduction-to-xamarin-ios-for-visual-studio-images/properties.png)

 Para más información sobre cómo usar el diseñador de iOS, vea las guías sobre el [Diseñador](~/ios/user-interface/designer/index.md).

## <a name="running--debugging-ios-applications"></a>Ejecución y depuración de aplicaciones de iOS

### <a name="device-logging"></a>Registro de dispositivos

En Visual Studio 2017, los paneles de registro de iOS y Android están unificados.

La nueva ventana de herramienta Registro de dispositivos para Visual Studio permite mostrar los registros de dispositivos iOS y Android. Se puede mostrar mediante la ejecución de cualquiera de los siguientes comandos:

- **Ver > Otras ventanas > Registro de dispositivos**
- **Herramientas > iOS > Registro de dispositivos**
- **Barra de herramientas de iOS > Registro de dispositivos**

Una vez que se muestra la ventana de herramientas, el usuario puede seleccionar el dispositivo físico en la lista desplegable de dispositivos. Cuando se selecciona un dispositivo, los registros se agregan automáticamente a la tabla. Se detendrá el cambio entre dispositivos y se iniciará el registro de dispositivos.

Para que los dispositivos aparezcan en el cuadro combinado, se debe cargar un proyecto de iOS. Además, en el caso de iOS, Visual Studio debe estar [conectado al servidor Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) para detectar los dispositivos iOS conectados al equipo Mac.

Esta ventana de herramientas proporciona: una tabla de entradas de registro, una lista desplegable para la selección de dispositivos, una forma de borrar entradas de registro, un cuadro de búsqueda y botones para reproducir, detener y pausar.

### <a name="set-debugging-stops"></a>Establecer paradas de depuración

En la aplicación se pueden establecer puntos de interrupción en cualquier momento para indicar al depurador que detenga temporalmente la ejecución del programa. Para establecer un punto de interrupción en Visual Studio, haga clic en el margen del editor, junto al número de línea del código en el que quiere interrumpir:

![Establecimiento de un punto de depuración](introduction-to-xamarin-ios-for-visual-studio-images/image18.png)

Inicie la depuración y use el simulador o el dispositivo para ir a un punto de interrupción de la aplicación. Cuando se alcanza un punto de interrupción, se resalta la línea y se habilita el comportamiento de depuración normal de Visual Studio: se puede entrar en el código, saltarlo o salir, examinar las variables locales o usar la ventana Inmediato.

En esta captura de pantalla se muestra la ejecución del simulador de iOS junto a Visual Studio con Parallels en macOS:

![En esta captura de pantalla se muestra la ejecución del simulador de iOS junto a Visual Studio con Parallels en macOS](introduction-to-xamarin-ios-for-visual-studio-images/image19.png)

### <a name="examine-local-variables"></a>Examinar variables locales

![Examen de variables locales con depuración](introduction-to-xamarin-ios-for-visual-studio-images/image20.png)

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo usar Xamarin iOS para Visual Studio. Se han enumerado las distintas características disponibles para crear, compilar y probar una aplicación de iOS en Visual Studio y se han explicado la compilación y la depuración de una aplicación sencilla de iOS.

## <a name="related-links"></a>Vínculos relacionados

- [Instalación de Xamarin.iOS](~/ios/get-started/installation/windows/index.md)
- [Aprovisionamiento de dispositivos](~/ios/get-started/installation/device-provisioning/index.md)
- [Creación de interfaz de usuario de iOS en código](~/ios/app-fundamentals/ios-code-only.md)
- [Conexión de un equipo Mac al entorno de Visual Studio con XMA (vídeo)](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)
