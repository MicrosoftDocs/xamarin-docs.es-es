---
title: 'Tutorial: enlazar una biblioteca de Objective-C de iOS'
description: En este artículo se proporciona un tutorial práctico para crear un enlace de Xamarin. iOS para una biblioteca de Objective-C existente, InfColorPicker. En él se tratan temas como la compilación de una biblioteca de Objective-C estática, el enlace y el uso del enlace en una aplicación de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 82367d63d1cc7b52d69de52ee635e380cd68c382
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435665"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Tutorial: enlazar una biblioteca de Objective-C de iOS

> [!IMPORTANT]
> Estamos investigando el uso de enlaces personalizados en la plataforma Xamarin. Realice [**esta encuesta**](https://www.surveymonkey.com/r/KKBHNLT) para informar de esfuerzos de desarrollo futuros.

_En este artículo se proporciona un tutorial práctico para crear un enlace de Xamarin. iOS para una biblioteca de Objective-C existente, InfColorPicker. En él se tratan temas como la compilación de una biblioteca de Objective-C estática, el enlace y el uso del enlace en una aplicación de Xamarin. iOS._

Cuando se trabaja en iOS, pueden darse casos en los que se desea consumir una biblioteca de Objective-C de terceros. En esas situaciones, puede usar un _proyecto de enlace_ de Xamarin. iOS para crear un [enlace de C#](~/cross-platform/macios/binding/overview.md) que le permita utilizar la biblioteca en las aplicaciones de Xamarin. iOS.

Por lo general, en el ecosistema de iOS, puede encontrar bibliotecas en tres tipos:

- Como archivo de biblioteca estática precompilado con `.a` extensión junto con sus encabezados (archivos. h). Por ejemplo, [la biblioteca de análisis de Google](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
- Como marco precompilado. Se trata simplemente de una carpeta que contiene la biblioteca estática, encabezados y, a veces, recursos adicionales con `.framework` extensión. Por ejemplo, [la biblioteca AdMob de Google](https://developers.google.com/admob/ios/download).
- Como solo archivos de código fuente. Por ejemplo, una biblioteca que contenga `.m` los archivos de solo y `.h` Objective C.

En el primer y el segundo escenario, ya habrá una biblioteca estática CocoaTouch precompilada, por lo que en este artículo nos centraremos en el tercer escenario. Recuerde que, antes de empezar a crear un enlace, compruebe siempre la licencia proporcionada con la biblioteca para asegurarse de que tiene la libertad de enlazarla.

En este artículo se proporciona un tutorial paso a paso para crear un proyecto de enlace con el proyecto de código abierto [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-c como ejemplo. sin embargo, toda la información de esta guía puede adaptarse para su uso con cualquier biblioteca de Objective-c de terceros. La biblioteca InfColorPicker proporciona un controlador de vistas reutilizable que permite al usuario seleccionar un color en función de su representación HSB, lo que hace que la selección de color sea más fácil de usar.

[![Ejemplo de la biblioteca InfColorPicker que se ejecuta en iOS](walkthrough-images/run01.png)](walkthrough-images/run01.png#lightbox)

Abordaremos todos los pasos necesarios para usar esta API de Objective-C en Xamarin. iOS:

- En primer lugar, crearemos una biblioteca estática de Objective-C con Xcode.
- A continuación, enlazaremos esta biblioteca estática con Xamarin. iOS.
- A continuación, muestre cómo Objective Sharpie puede reducir la carga de trabajo generando automáticamente algunas (pero no todas) las definiciones de API necesarias que requiere el enlace de Xamarin. iOS.
- Por último, vamos a crear una aplicación de Xamarin. iOS que usa el enlace.

En la aplicación de ejemplo se muestra cómo usar un delegado seguro para la comunicación entre la API de InfColorPicker y el código de C#. Una vez que hemos visto cómo usar un delegado fuerte, veremos cómo usar delegados débiles para realizar las mismas tareas.

## <a name="requirements"></a>Requisitos

En este artículo se supone que está familiarizado con Xcode y el lenguaje Objective-C y que ha leído nuestra documentación sobre [Objective-c de enlace](~/cross-platform/macios/binding/index.md) . Además, se requiere lo siguiente para completar los pasos que se presentan:

- **Xcode y el SDK de iOS** : Xcode de Apple y la API de iOS más reciente deben instalarse y configurarse en el equipo del desarrollador.
- **[Herramientas de línea de comandos de Xcode](#Installing_the_Xcode_Command_Line_Tools)** : las herramientas de línea de comandos de Xcode deben estar instaladas para la versión instalada actualmente de Xcode (consulte a continuación los detalles de la instalación).
- **Visual Studio para Mac o Visual Studio** : la versión más reciente de Visual Studio para Mac o Visual Studio debe estar instalada y configurada en el equipo de desarrollo. Se necesita un equipo Mac de Apple para desarrollar una aplicación de Xamarin. iOS y, al usar Visual Studio, debe estar conectado a [un host de compilación de Xamarin. iOS.](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- **La versión más reciente de Objective Sharpie** : una copia actual de la herramienta de objetivo Sharpie descargada [aquí](~/cross-platform/macios/binding/objective-sharpie/get-started.md). Si ya tiene el objetivo Sharpie instalado, puede actualizarlo a la versión más reciente mediante el `sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"></a>

## <a name="installing-the-xcode-command-line-tools"></a>Instalación de las herramientas de línea de comandos de Xcode

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Como se indicó anteriormente, usaremos las herramientas de línea de comandos de Xcode (específicamente `make` y `lipo` ) en este tutorial. El `make` comando es una utilidad de UNIX muy común que automatiza la compilación de programas ejecutables y bibliotecas mediante un _archivo make_ que especifica cómo se debe compilar el programa. El `lipo` comando es una utilidad de línea de comandos de OS X para crear archivos de varias arquitecturas; combinará varios `.a` archivos en un único archivo que pueden usar todas las arquitecturas de hardware.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Como se indicó anteriormente, usaremos las herramientas de línea de comandos de Xcode en el **host de compilación de Mac** (específicamente `make` y `lipo` ) en este tutorial. El `make` comando es una utilidad de UNIX muy común que automatiza la compilación de programas ejecutables y bibliotecas mediante un _archivo make_ para especificar cómo compilar el programa. El `lipo` comando es una utilidad de línea de comandos de OS X para crear archivos de varias arquitecturas; combinará varios `.a` archivos en un único archivo que pueden usar todas las arquitecturas de hardware.

-----

Según la compilación de Apple [desde la línea de comandos con la documentación de preguntas más frecuentes de Xcode](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) , en OS X 10,9 y versiones posteriores, el panel de **descargas** del cuadro de diálogo **preferencias** de Xcode no es compatible con las herramientas de línea de comandos de descarga.

Tendrá que usar uno de los métodos siguientes para instalar las herramientas:

- **Instalar Xcode** : al instalar Xcode, se incluye con todas las herramientas de línea de comandos. En OS X 10,9, las correcciones de compatibilidad (instaladas en `/usr/bin` ) pueden asignar cualquier herramienta incluida en `/usr/bin` a la herramienta correspondiente en Xcode. Por ejemplo, el `xcrun` comando, que le permite buscar o ejecutar cualquier herramienta en Xcode desde la línea de comandos.
- **La aplicación de terminal** : desde la aplicación de terminal, puede instalar las herramientas de línea de comandos ejecutando el `xcode-select --install` comando:
  - Inicie la aplicación terminal.
  - Escriba `xcode-select --install` y presione **entrar**; por ejemplo:

  ```bash
  Europa:~ kmullins$ xcode-select --install
  ```

  - Se le pedirá que instale las herramientas de línea de comandos, haga clic en el botón **instalar** : [ ![ instalación de las herramientas de línea de comandos](walkthrough-images/xcode01.png)](walkthrough-images/xcode01.png#lightbox) .

  - Las herramientas se descargarán e instalarán desde los servidores de Apple: [ ![ descargar las herramientas](walkthrough-images/xcode02.png)](walkthrough-images/xcode02.png#lightbox)

- **Descargas para desarrolladores de Apple** : el paquete de herramientas de línea de comandos está disponible en la Página Web [descargas para desarrolladores de Apple](https://developer.apple.com/downloads/index.action) . Inicie sesión con su ID de Apple y, a continuación, busque y descargue las herramientas de línea de comandos: [ ![ búsqueda de las herramientas de línea de comandos](walkthrough-images/xcode03.png)](walkthrough-images/xcode03.png#lightbox)

Con las herramientas de línea de comandos instaladas, estamos listos para continuar con el tutorial.

## <a name="walkthrough"></a>Tutorial

En este tutorial, trataremos los pasos siguientes:

- **[Crear una biblioteca estática](#Creating_A_Static_Library)** : este paso implica la creación de una biblioteca estática del código de Objective-C de **InfColorPicker** . La biblioteca estática tendrá la `.a` extensión de archivo y se incrustará en el ensamblado .net del proyecto de biblioteca.
- **[Crear un proyecto de enlace de Xamarin. iOS](#Create_a_Xamarin.iOS_Binding_Project)** : una vez que tengamos una biblioteca estática, la usaremos para crear un proyecto de enlace de Xamarin. iOS. El proyecto de enlace consta de la biblioteca estática que se acaba de crear y de los metadatos en forma de código C# que explica cómo se puede usar la API de Objective-C. Estos metadatos se conocen normalmente como definiciones de la API. Vamos a usar **[Objective Sharpie](#Using_Objective_Sharpie)** para ayudarnos a crear las definiciones de la API.
- **[Normalizar las definiciones de la API](#Normalize_the_API_Definitions)** : Objective Sharpie realiza un trabajo excelente para ayudarnos, pero no puede hacer todo. Analizaremos algunos cambios que necesitamos realizar en las definiciones de la API antes de poder usarlos.
- **[Usar la biblioteca de enlaces](#Using_the_Binding)** : por último, crearemos una aplicación de Xamarin. iOS para mostrar cómo usar nuestro proyecto de enlace recién creado.

Ahora que sabemos qué pasos son necesarios, vamos a pasar al resto del tutorial.

<a name="Creating_A_Static_Library"></a>

## <a name="creating-a-static-library"></a>Crear una biblioteca estática

Si inspeccionamos el código de InfColorPicker en github:

[![Inspeccione el código de InfColorPicker en github](walkthrough-images/image02.png)](walkthrough-images/image02.png#lightbox)

Podemos ver los tres directorios siguientes en el proyecto:

- **InfColorPicker** : este directorio contiene el código de Objective-C para el proyecto.
- **PickerSamplePad** : este directorio contiene un proyecto de iPad de ejemplo.
- **PickerSamplePhone** : este directorio contiene un proyecto de iPhone de ejemplo.

Vamos a descargar el proyecto InfColorPicker de [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) y a descomprimirlo en el directorio de nuestro elección. Al abrir el destino de Xcode para el `PickerSamplePhone` proyecto, vemos la siguiente estructura de proyecto en Xcode Navigator:

[![La estructura del proyecto en Xcode Navigator](walkthrough-images/image03.png)](walkthrough-images/image03.png#lightbox)

Este proyecto consigue la reutilización de código agregando directamente el código fuente de InfColorPicker (en el cuadro rojo) en cada proyecto de ejemplo. El código del proyecto de ejemplo se encuentra dentro del cuadro azul. Dado que este proyecto en particular no nos proporciona una biblioteca estática, es necesario crear un proyecto de Xcode para compilar la biblioteca estática.

El primer paso es agregar el código fuente de InfoColorPicker en la biblioteca estática. Para ello, haga lo siguiente:

1. Inicie Xcode.
2. En el menú **archivo** , seleccione **nuevo**  >  **proyecto...**:

    [![Inicio de un nuevo proyecto](walkthrough-images/image04.png)](walkthrough-images/image04.png#lightbox)
3. Seleccione **Framework & Library**, la plantilla **biblioteca estática de Cocoa** y haga clic en el botón **siguiente** :

    [![Seleccione la plantilla biblioteca estática de coco Touch](walkthrough-images/image05.png)](walkthrough-images/image05.png#lightbox)

4. Escriba `InfColorPicker` como **nombre del proyecto** y haga clic en el botón **siguiente** :

    [![Escriba InfColorPicker para el nombre del proyecto](walkthrough-images/image06.png)](walkthrough-images/image06.png#lightbox)
5. Seleccione una ubicación para guardar el proyecto y haga clic en el botón **Aceptar** .
6. Ahora debemos agregar el origen del proyecto InfColorPicker a nuestro proyecto de biblioteca estática. Dado que el archivo **InfColorPicker. h** ya existe en nuestra biblioteca estática (de forma predeterminada), Xcode no nos permitirá sobrescribirlo. En el **buscador**, navegue hasta el código fuente de InfColorPicker en el proyecto original que descomprimido de Github, copie todos los archivos InfColorPicker y péguelos en nuestro nuevo proyecto de biblioteca estática:

    [![Copie todos los archivos InfColorPicker](walkthrough-images/image12.png)](walkthrough-images/image12.png#lightbox)

7. Vuelva a Xcode, haga clic con el botón derecho en la carpeta **InfColorPicker** y seleccione **Agregar archivos a "InfColorPicker..."**:

    [![Adición de archivos](walkthrough-images/image08.png)](walkthrough-images/image08.png#lightbox)

8. En el cuadro de diálogo Agregar archivos, vaya a los archivos de código fuente de InfColorPicker que acaba de copiar, selecciónelos todos y haga clic en el botón **Agregar** :

    [![Seleccionar todo y hacer clic en el botón Agregar](walkthrough-images/image09.png)](walkthrough-images/image09.png#lightbox)

9. El código fuente se copiará en nuestro proyecto:

    [![El código fuente se copiará en el proyecto.](walkthrough-images/image10.png)](walkthrough-images/image10.png#lightbox)

10. En el explorador de proyectos de Xcode, seleccione el archivo **InfColorPicker. m** y marque como comentario las dos últimas líneas (debido a la manera en que se escribió esta biblioteca, este archivo no se usa):

    [![Editar el archivo InfColorPicker. m](walkthrough-images/image14.png)](walkthrough-images/image14.png#lightbox)

11. Ahora tenemos que comprobar si hay marcos de trabajo requeridos por la biblioteca. Puede encontrar esta información en el archivo Léame o abriendo uno de los proyectos de ejemplo proporcionados. En este ejemplo se usa `Foundation.framework` , `UIKit.framework` y, `CoreGraphics.framework` por tanto, vamos a agregarlos.

12. Seleccione el **destino InfColorPicker > fases de compilación** y expanda la sección **vínculo binario con bibliotecas** :

    [![Expandir la sección vincular binario con bibliotecas](walkthrough-images/image16b.png)](walkthrough-images/image16b.png#lightbox)

13. Use el **+** botón para abrir el cuadro de diálogo que le permite agregar los marcos de trabajo de Marcos necesarios enumerados anteriormente:

    [![Agregue los marcos de trabajo de Marcos necesarios enumerados anteriormente](walkthrough-images/image16c.png)](walkthrough-images/image16c.png#lightbox)

14. La sección **vincular binario con bibliotecas** debe ser ahora similar a la imagen siguiente:

    [![La sección Link binario con bibliotecas](walkthrough-images/image16d.png)](walkthrough-images/image16d.png#lightbox)

En este momento estamos cerca, pero no estamos muy listos. La biblioteca estática se ha creado, pero es necesario compilarla para crear un archivo binario de Fat que incluya todas las arquitecturas necesarias para el dispositivo iOS y el simulador de iOS.

### <a name="creating-a-fat-binary"></a>Creación de un archivo binario de Fat

Todos los dispositivos iOS tienen procesadores con tecnología ARM que se han desarrollado con el tiempo. Cada nueva arquitectura agrega nuevas instrucciones y otras mejoras manteniendo la compatibilidad con versiones anteriores. los dispositivos iOS tienen conjuntos de instrucciones ARMv6, ARMv7, armv7s y arm64, aunque [ARMv6 no se usan más](~/ios/deploy-test/compiling-for-different-devices.md). El simulador de iOS no se basa en ARM y, en su lugar, es un simulador x86 y x86_64 encendido. Esto significa que se deben proporcionar bibliotecas para cada conjunto de instrucciones.

Una biblioteca FAT es un `.a` archivo que contiene todas las arquitecturas admitidas.

La creación de un archivo binario FAT es un proceso de tres pasos:

- Compile una versión de ARM 7 & ARM64 de la biblioteca estática.
- Compile una versión de x84_64 y x86 de la biblioteca estática.
- Use la `lipo` herramienta de línea de comandos para combinar las dos bibliotecas estáticas en una.

Aunque estos tres pasos son bastante sencillos, puede que sea necesario repetirlos en el futuro cuando la biblioteca de Objective-C Reciba actualizaciones o si se requieren correcciones de errores. Si decide automatizar estos pasos, se simplificará el mantenimiento y soporte técnico futuros del proyecto de enlace de iOS.

Hay muchas herramientas disponibles para automatizar estas tareas: un script de Shell, [inclinación](https://rake.rubyforge.org/), [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/)y make. Cuando se instalan las herramientas de línea de comandos de Xcode, `make` también se instala, por lo que es el sistema de compilación que se usará para este tutorial. Este es un **archivo make** que puede usar para crear una biblioteca compartida de varias arquitecturas que funcionará en un dispositivo iOS y en el simulador de cualquier biblioteca:

<!--markdownlint-disable MD010 -->
```makefile
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=./YOUR-PROJECT-NAME
PROJECT=$(PROJECT_ROOT)/YOUR-PROJECT-NAME.xcodeproj
TARGET=YOUR-PROJECT-NAME

all: lib$(TARGET).a

lib$(TARGET)-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

lib$(TARGET)-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET)-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

lib$(TARGET).a: lib$(TARGET)-i386.a lib$(TARGET)-armv7.a lib$(TARGET)-arm64.a
    xcrun -sdk iphoneos lipo -create -output $@ $^

clean:
    -rm -f *.a *.dll
```
<!--markdownlint-enable MD010 -->

Escriba los comandos de **archivos make** en el editor de texto sin formato de su elección y actualice las secciones con su nombre de **proyecto** con el nombre del proyecto. También es importante asegurarse de que pega exactamente las instrucciones anteriores, con las pestañas de las instrucciones conservadas.

Guarde el archivo con el nombre **Makefile** en la misma ubicación que la biblioteca estática de InfColorPicker Xcode que hemos creado anteriormente:

[![Guarde el archivo con el nombre Makefile](walkthrough-images/lib00.png)](walkthrough-images/lib00.png#lightbox)

Abra la aplicación terminal en el equipo Mac y navegue hasta la ubicación del archivo make. Escriba `make` en el terminal, presione **entrar** y se ejecutará el **archivo make** :

[![Salida de archivo make de ejemplo](walkthrough-images/lib01.png)](walkthrough-images/lib01.png#lightbox)

Al ejecutar make, verá una gran cantidad de texto desplazado por. Si todo funciona correctamente, verá las palabras **compilación correcta** y los `libInfColorPicker-armv7.a` `libInfColorPicker-i386.a` archivos, y se `libInfColorPickerSDK.a` copiarán en la misma ubicación que el **archivo make**:

[![Los archivos libInfColorPicker-ARMv7. a, libInfColorPicker-i386. a y libInfColorPickerSDK. a generados por el archivo make](walkthrough-images/lib02.png)](walkthrough-images/lib02.png#lightbox)

Puede confirmar las arquitecturas dentro del archivo binario FAT mediante el comando siguiente:

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Esto debería mostrar lo siguiente:

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

En este punto, hemos completado el primer paso de nuestro enlace de iOS mediante la creación de una biblioteca estática con Xcode y las herramientas de línea de comandos de Xcode `make` y `lipo` . Vamos a pasar al siguiente paso y usar **Objective-Sharpie** para automatizar la creación de los enlaces de API para nosotros.

<a name="Create_a_Xamarin.iOS_Binding_Project"></a>

## <a name="create-a-xamarinios-binding-project"></a>Creación de un proyecto de enlace de Xamarin. iOS

Antes de poder usar **Objective-Sharpie** para automatizar el proceso de enlace, es necesario crear un proyecto de enlace de Xamarin. iOS para hospedar las definiciones de API (que vamos a usar **Objective-Sharpie** para ayudarnos a compilar) y crear el enlace de C# para nosotros.

Vamos a hacer lo siguiente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie Visual Studio para Mac:
1. En el menú **archivo** , seleccione **nueva**  >  **solución...**:

    ![Iniciar una nueva solución](walkthrough-images/bind01.png)

1. En el cuadro de diálogo Nueva solución, seleccione **biblioteca**  >  **enlace de iOS proyecto**:

    ![Selección del proyecto de enlace de iOS](walkthrough-images/bind02.png)

1. Haga clic en el botón **Next** (Siguiente).

1. Escriba "InfColorPickerBinding" como **nombre del proyecto** y haga clic en el botón **crear** para crear la solución:

    ![Escriba InfColorPickerBinding como nombre del proyecto](walkthrough-images/bind02a.png)

Se creará la solución y se incluirán dos archivos predeterminados:

![Estructura de la solución en el Explorador de soluciones](walkthrough-images/bind03.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Inicie Visual Studio.

1. En el menú **archivo** , seleccione **nuevo**  >  **proyecto...**:

    ![Inicio de un nuevo proyecto](walkthrough-images/bind01vs.png "Inicio de un nuevo proyecto")

1. En el cuadro de diálogo nuevo proyecto, seleccione **Visual C# > iPhone & iPad > biblioteca de enlaces de iOS (Xamarin)**:

    [![Selección de la biblioteca de enlaces de iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Escriba "InfColorPickerBinding" como **nombre** y haga clic en el botón **Aceptar** para crear la solución.

Se creará la solución y se incluirán dos archivos predeterminados:

![Estructura de la solución en el Explorador de soluciones](walkthrough-images/bind03vs.png)

-----

- **ApiDefinition.CS** : este archivo contendrá los contratos que definen cómo se ajustará la API de Objective-C en C#.
- **Structs.CS** : este archivo contendrá las estructuras o los valores de enumeración necesarios para las interfaces y los delegados.

Vamos a trabajar con estos dos archivos más adelante en el tutorial. En primer lugar, es necesario agregar la biblioteca InfColorPicker al proyecto de enlace.

### <a name="including-the-static-library-in-the-binding-project"></a>Incluir la biblioteca estática en el proyecto de enlace

Ahora que tenemos nuestro proyecto de enlace base listo, necesitamos agregar la biblioteca binaria FAT que hemos creado anteriormente para la biblioteca **InfColorPicker** .

Siga estos pasos para agregar la biblioteca:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. Haga clic con el botón derecho en la carpeta **referencias nativas** en el panel de solución y seleccione **Agregar referencias nativas**:

    ![Agregar referencias nativas](walkthrough-images/bind04a.png)

1. Navegue hasta el archivo binario de Fat que hicimos anteriormente ( `libInfColorPickerSDK.a` ) y presione el botón **abrir** :

    ![Seleccione el archivo libInfColorPickerSDK. a](walkthrough-images/bind05.png)
1. El archivo se incluirá en el proyecto:

    ![Incluir un archivo](walkthrough-images/bind04.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Copie el `libInfColorPickerSDK.a` del **host de compilación de Mac** y péguelo en el proyecto de enlace.

1. Haga clic con el botón derecho en el proyecto y elija **agregar > elemento existente...**:

    ![Agregar un archivo existente](walkthrough-images/bind04vs.png)

1. Navegue hasta el `libInfColorPickerSDK.a` y haga clic en el botón **Agregar** :

    ![Agregando libInfColorPickerSDK. a](walkthrough-images/bind05vs.png)

1. El archivo se incluirá en el proyecto.

-----

Cuando el archivo **. a** se agrega al proyecto, Xamarin. iOS establecerá automáticamente la **acción de compilación** del archivo en **ObjcBindingNativeLibrary**y creará un archivo especial denominado `libInfColorPickerSDK.linkwith.cs` .

Este archivo contiene el `LinkWith` atributo que indica a Xamarin. iOS cómo controlar la biblioteca estática que acabamos de agregar. El contenido de este archivo se muestra en el siguiente fragmento de código:

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

El `LinkWith` atributo identifica la biblioteca estática del proyecto y algunas marcas importantes del enlazador.

Lo siguiente que debemos hacer es crear las definiciones de la API para el proyecto InfColorPicker. Para los fines de este tutorial, usaremos Sharpie objetivo para generar el archivo **ApiDefinition.CS**.

<a name="Using_Objective_Sharpie"></a>

## <a name="using-objective-sharpie"></a>Uso de Sharpie objetivo

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Objective Sharpie es una herramienta de línea de comandos (proporcionada por Xamarin) que puede ayudar a crear las definiciones necesarias para enlazar una biblioteca de Objective-C de terceros con C#. En esta sección, usaremos Sharpie objetivo para crear el **ApiDefinition.CS** inicial del proyecto InfColorPicker.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Objective Sharpie es una herramienta de línea de comandos (proporcionada por Xamarin) que puede ayudar a crear las definiciones necesarias para enlazar una biblioteca de Objective-C de terceros con C#. En esta sección, usaremos Sharpie objetivo en nuestro **host de compilación de Mac** para crear el **ApiDefinition.CS** inicial del proyecto InfColorPicker.

-----

Para empezar, vamos a descargar el archivo de instalador de Sharpie objetivo, tal como se detalla en esta [Guía](~/cross-platform/macios/binding/objective-sharpie/get-started.md#installing). Ejecute el instalador y siga todas las indicaciones en pantalla desde el Asistente para instalación para instalar Objective Sharpie en nuestro equipo de desarrollo.

Una vez que se ha instalado correctamente Sharpie de objetivos, vamos a iniciar la aplicación terminal y escribir el siguiente comando para obtener ayuda sobre todas las herramientas que proporciona para ayudar en el enlace:

```bash
sharpie -help
```

Si ejecutamos el comando anterior, se generará la siguiente salida:

```bash
Europa:Resources kmullins$ sharpie -help
usage: sharpie [OPTIONS] TOOL [TOOL_OPTIONS]

Options:
  -h, --helpShow detailed help
  -v, --versionShow version information

Available Tools:
  xcode              Get information about Xcode installations and available SDKs.
  pod                Create a Xamarin C# binding to Objective-C CocoaPods
  bind               Create a Xamarin C# binding to Objective-C APIs
  update             Update to the latest release of Objective Sharpie
  verify-docs        Show cross reference documentation for [Verify] attributes
  docs               Open the Objective Sharpie online documentation
```

Para este tutorial, usaremos las siguientes herramientas de Sharpie de objetivos:

- **Xcode** : estas herramientas nos proporcionan información sobre nuestra instalación actual de Xcode y las versiones de las API de iOS y Mac que hemos instalado. Esta información se utilizará más adelante cuando se generen los enlaces.
- **BIND** : usaremos esta herramienta para analizar los archivos **. h** del proyecto InfColorPicker en los archivos **ApiDefinition.CS** y **StructsAndEnums.CS** iniciales.

Para obtener ayuda sobre una herramienta específica de Sharpie de objetivos, escriba el nombre de la herramienta y la `-help` opción. Por ejemplo, `sharpie xcode -help` devuelve el siguiente resultado:

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, -help           Show detailed help
  -v, -verbose        Be verbose with output

Xcode Options:
  -sdks               List all available Xcode SDKs. Pass -verbose for more
                        details.
  -sdkpath SDK        Output the path of the SDK
  -frameworks SDK     List all available framework directories in a given SDK.
```

Antes de que podamos iniciar el proceso de enlace, necesitamos obtener información sobre nuestros SDK instalados actuales escribiendo el siguiente comando en el terminal `sharpie xcode -sdks` :

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

En el caso anterior, podemos ver que tenemos instalado el `iphoneos9.3` SDK en nuestro equipo. Con esta información en su lugar, estamos preparados para analizar los archivos de proyecto de InfColorPicker `.h` en el **ApiDefinition.CS** inicial y en `StructsAndEnums.cs` el proyecto InfColorPicker.

Escriba el siguiente comando en la aplicación terminal:

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] -scope [full-path-to-project]/InfColorPicker/InfColorPicker [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Donde `[full-path-to-project]` es la ruta de acceso completa al directorio donde se encuentra el archivo de proyecto **InfColorPicker** Xcode en nuestro equipo y [iPhone-OS] es el SDK de iOS que se ha instalado, como se indica en el `sharpie xcode -sdks` comando. Tenga en cuenta que, en este ejemplo, pasamos ** \* . h** como un parámetro, que incluye *todos* los archivos de encabezado de este directorio; normalmente no debería hacerlo, sino que debe leer cuidadosamente los archivos de encabezado para buscar el archivo **. h** de nivel superior que hace referencia a todos los demás archivos pertinentes y simplemente pasarlo a Objective Sharpie. 

> [!TIP] 
> En el `-scope` argumento, pase la carpeta que contiene los encabezados que desea enlazar. Sin el `-scope` argumento, Objective Sharpie intentará generar enlaces para cualquier encabezado de SDK de iOS que se importe, por ejemplo `#import <UIKit.h>` ,, lo que resulta en un archivo de definiciones de gran tamaño que probablemente generará errores al compilar el proyecto de enlace. Con el `-scope` conjunto de argumentos, Objective Sharpie no generará enlaces para ningún encabezado fuera de la carpeta con ámbito. 

La siguiente [salida](walkthrough-images/os05.png) se generará en el terminal:

```bash
Europa:Resources kmullins$ sharpie bind -output InfColorPicker -namespace InfColorPicker -sdk iphoneos8.1 /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h -unified
Compiler configuration:
    -isysroot /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk -miphoneos-version-min=8.1 -resource-dir /Library/Frameworks/ObjectiveSharpie.framework/Versions/1.1.1/clang-resources -arch armv7 -ObjC

[  0%] parsing /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h
In file included from /Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPicker.h:60:
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* sourceColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:28:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: no 'assign',
      'retain', or 'copy' attribute is specified - 'assign' is assumed [-Wobjc-property-no-attribute]
@property (nonatomic) UIColor* resultColor;
^
/Users/kmullins/Projects/InfColorPicker/InfColorPicker/InfColorPickerController.h:29:1: warning: default property
      attribute 'assign' not appropriate for non-GC object [-Wobjc-property-no-attribute]
4 warnings generated.
[100%] parsing complete
[bind] InfColorPicker.cs
Europa:Resources kmullins$
```

Y los archivos **InfColorPicker.Enums.CS** y **InfColorPicker.CS** se crearán en nuestro directorio:

[![Los archivos InfColorPicker.enums.cs y InfColorPicker.cs](walkthrough-images/os06.png)](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

Abra ambos archivos en el proyecto de enlace que hemos creado anteriormente. Copie el contenido del archivo **InfColorPicker.CS** y péguelo en el archivo **ApiDefinition.CS** , reemplazando el bloque de `namespace ...` código existente por el contenido del archivo **InfColorPicker.CS** (lo que deja las `using` instrucciones intactas):

![El archivo InfColorPickerControllerDelegate](walkthrough-images/os07.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Abra ambos archivos en el proyecto de enlace que hemos creado anteriormente. Copie el contenido del archivo **InfColorPicker.CS** (desde el **host de compilación de Mac**) y péguelo en el archivo **ApiDefinition.CS** , reemplazando el `namespace ...` bloque de código existente con el contenido del archivo **InfColorPicker.CS** (lo que deja las `using` instrucciones intactas).

-----

<a name="Normalize_the_API_Definitions"></a>

## <a name="normalize-the-api-definitions"></a>Normalizar las definiciones de API

A veces, el objetivo Sharpie tiene un problema `Delegates` de traducción, por lo que tendremos que modificar la definición de la `InfColorPickerControllerDelegate` interfaz y reemplazar la `[Protocol, Model]` línea por lo siguiente:

```csharp
[BaseType(typeof(NSObject))]
[Model]
```

Para que la definición tenga el siguiente aspecto:

[![La definición](walkthrough-images/os11.png)](walkthrough-images/os11.png#lightbox)

A continuación, hacemos lo mismo con el contenido del `InfColorPicker.enums.cs` archivo, copiándolos y pegándolos en el `StructsAndEnums.cs` archivo que deja `using` intactas las instrucciones:

[![El contenido del archivo StructsAndEnums.cs ](walkthrough-images/os09.png)](walkthrough-images/os09.png#lightbox)

También puede encontrar que el objetivo Sharpie ha anotado el enlace con `[Verify]` atributos. Estos atributos indican que debe comprobar que Objective Sharpie hizo lo correcto comparando el enlace con la declaración C/Objective-C original (que se proporcionará en un comentario encima de la declaración enlazada). Una vez que haya comprobado los enlaces, debe quitar el atributo verify. Para obtener más información, consulte la guía de [comprobación](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) .

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

En este momento, nuestro proyecto de enlace debe estar completo y listo para compilar. Vamos a compilar nuestro proyecto de enlace y asegurarse de que terminamos sin errores:

[Compile el proyecto de enlace y asegúrese de que no hay errores.](walkthrough-images/os12.png)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

En este momento, nuestro proyecto de enlace debe estar completo y listo para compilar. Vamos a compilar nuestro proyecto de enlace y asegurarse de que terminamos sin errores.

-----

<a name="Using_the_Binding"></a>

## <a name="using-the-binding"></a>Usar el enlace

Siga estos pasos para crear una aplicación de iPhone de ejemplo para usar la biblioteca de enlace de iOS creada anteriormente:

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. **Crear proyecto de Xamarin. iOS** : Agregue un nuevo proyecto de Xamarin. iOS denominado **InfColorPickerSample** a la solución, tal como se muestra en las siguientes capturas de pantallas:

    ![Agregar una aplicación de vista única](walkthrough-images/use01.png)

    ![Establecer el identificador](walkthrough-images/use01a.png)

1. **Agregar referencia al** proyecto de enlace: actualice el proyecto **InfColorPickerSample** para que tenga una referencia al proyecto **InfColorPickerBinding** :

    ![Agregar referencia al proyecto de enlace](walkthrough-images/use02.png)

1. **Crear la interfaz de usuario de iPhone** : haga doble clic en el archivo **archivo mainstoryboard. Storyboard** en el proyecto **InfColorPickerSample** para editarlo en el diseñador de iOS. Agregue un **botón** a la vista y llámelo `ChangeColorButton` , como se muestra a continuación:

    ![Agregar un botón a la vista](walkthrough-images/use03.png)

1. **Agregue InfColorPickerView. Xib** : la biblioteca InfColorPicker Objective-C incluye un archivo **. Xib** . Xamarin. iOS no incluirá this **. Xib** en el proyecto de enlace, lo que producirá errores en tiempo de ejecución en nuestra aplicación de ejemplo. La solución alternativa para esto es agregar el archivo **. Xib** a nuestro proyecto de Xamarin. iOS. Seleccione el proyecto de Xamarin. iOS, haga clic con el botón derecho y seleccione **agregar > agregar archivos**y agregue el archivo **. Xib** como se muestra en la siguiente captura de pantalla:

    ![Agregue InfColorPickerView. Xib](walkthrough-images/use04.png)

1. Cuando se le pregunte, copie el archivo **. Xib** en el proyecto.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. **Crear proyecto de Xamarin. iOS** : Agregue un nuevo proyecto de Xamarin. iOS denominado **InfColorPickerSample** mediante la plantilla de **aplicación de vista única** :

    [![proyecto de aplicación de iOS (Xamarin)](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![Seleccionar plantilla](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Agregar referencia al** proyecto de enlace: actualice el proyecto **InfColorPickerSample** para que tenga una referencia al proyecto **InfColorPickerBinding** :

    ![Agregar referencia al proyecto de enlace](walkthrough-images/use02vs.png)

1. **Crear la interfaz de usuario de iPhone** : haga doble clic en el archivo **archivo mainstoryboard. Storyboard** en el proyecto **InfColorPickerSample** para editarlo en el diseñador de iOS. Agregue un **botón** a la vista y llámelo `ChangeColorButton` , como se muestra a continuación:

    ![Crear la interfaz de usuario de iPhone](walkthrough-images/use03vs.png)

1. **Agregue InfColorPickerView. Xib** : la biblioteca InfColorPicker Objective-C incluye un archivo **. Xib** . Xamarin. iOS no incluirá this **. Xib** en el proyecto de enlace, lo que producirá errores en tiempo de ejecución en nuestra aplicación de ejemplo. La solución alternativa para esto es agregar el archivo **. Xib** a nuestro proyecto de Xamarin. iOS desde nuestro **host de compilación de Mac**. Seleccione el proyecto de Xamarin. iOS, haga clic con el botón derecho y seleccione **Agregar**  >  **elemento existente...** y agregue el archivo **. Xib** .

-----

A continuación, echemos un vistazo a los protocolos de Objective-C y cómo se controlan en el código de enlace y C#.

### <a name="protocols-and-xamarinios"></a>Protocolos y Xamarin. iOS

En Objective-C, un protocolo define métodos (o mensajes) que se pueden usar en determinadas circunstancias. Conceptualmente, son muy similares a las interfaces en C#. Una diferencia importante entre un protocolo Objective-C y una interfaz de C# es que los protocolos pueden tener métodos opcionales: métodos que una clase no tiene que implementar. Objective-C usa la @optional palabra clave se usa para indicar qué métodos son opcionales. Para obtener más información sobre [los protocolos, vea eventos, protocolos y delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md).

**InfColorPickerController** tiene un protocolo de este tipo, que se muestra en el siguiente fragmento de código:

```csharp
@protocol InfColorPickerControllerDelegate

@optional

- (void) colorPickerControllerDidFinish: (InfColorPickerController*) controller;
// This is only called when the color picker is presented modally.

- (void) colorPickerControllerDidChangeColor: (InfColorPickerController*) controller;

@end
```

**InfColorPickerController** usa este protocolo para informar a los clientes de que el usuario ha seleccionado un nuevo color y de que el **InfColorPickerController** ha finalizado. Objective Sharpie asigna este protocolo, tal y como se muestra en el siguiente fragmento de código:

```csharp
[BaseType(typeof(NSObject))]
[Model]
public partial interface InfColorPickerControllerDelegate {

    [Export ("colorPickerControllerDidFinish:")]
    void ColorPickerControllerDidFinish (InfColorPickerController controller);

    [Export ("colorPickerControllerDidChangeColor:")]
    void ColorPickerControllerDidChangeColor (InfColorPickerController controller);
}

```

Cuando se compila la biblioteca de enlaces, Xamarin. iOS creará una clase base abstracta denominada `InfColorPickerControllerDelegate` , que implementa esta interfaz con métodos virtuales.

Hay dos maneras de implementar esta interfaz en una aplicación de Xamarin. iOS:

- **Delegado fuerte** : el uso de un delegado fuerte implica la creación de una clase de C# que subclases `InfColorPickerControllerDelegate` e invalida los métodos adecuados. **InfColorPickerController** utilizará una instancia de esta clase para comunicarse con sus clientes.
- **Delegado débil** : un delegado débil es una técnica ligeramente diferente que implica la creación de un método público en alguna clase (como `InfColorPickerSampleViewController` ) y, a continuación, la exposición de ese método al `InfColorPickerDelegate` Protocolo a través de un `Export` atributo.

Los delegados fuertes proporcionan IntelliSense, seguridad de tipos y una mejor encapsulación. Por estos motivos, debe utilizar delegados fuertes en los que pueda, en lugar de un delegado débil.

En este tutorial se tratarán ambas técnicas: primero se implementa un delegado fuerte y, a continuación, se explica cómo implementar un delegado débil.

### <a name="implementing-a-strong-delegate"></a>Implementar un delegado fuerte

Finalice la aplicación de Xamarin. iOS mediante un delegado seguro para responder al `colorPickerControllerDidFinish:` mensaje:

**Subclase InfColorPickerControllerDelegate** : agregue una nueva clase al proyecto denominado `ColorSelectedDelegate` . Edite la clase para que tenga el código siguiente:

```csharp
using InfColorPickerBinding;
using UIKit;

namespace InfColorPickerSample
{
  public class ColorSelectedDelegate:InfColorPickerControllerDelegate
  {
    readonly UIViewController parent;

    public ColorSelectedDelegate (UIViewController parent)
    {
      this.parent = parent;
    }

    public override void ColorPickerControllerDidFinish (InfColorPickerController controller)
    {
      parent.View.BackgroundColor = controller.ResultColor;
      parent.DismissViewController (false, null);
    }
  }
}
```

Xamarin. iOS enlazará el delegado de Objective-C mediante la creación de una clase base abstracta denominada `InfColorPickerControllerDelegate` . Subclase de este tipo e invalide el `ColorPickerControllerDidFinish` método para tener acceso al valor de la `ResultColor` propiedad de `InfColorPickerController` .

**Cree una instancia de ColorSelectedDelegate** : nuestro controlador de eventos necesitará una instancia del `ColorSelectedDelegate` tipo que hemos creado en el paso anterior. Edite la clase `InfColorPickerSampleViewController` y agregue la siguiente variable de instancia a la clase:

```csharp
ColorSelectedDelegate selector;
```

**Inicialice la variable ColorSelectedDelegate** : para asegurarse de que `selector` es una instancia válida, actualice el método `ViewDidLoad` de `ViewController` para que coincida con el siguiente fragmento de código:

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();
  ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
  selector = new ColorSelectedDelegate (this);
}
```

**Implemente el método HandleTouchUpInsideWithStrongDelegate** -Next implemente el controlador de eventos para cuando el usuario toque **ColorChangeButton**. Edite `ViewController` y agregue el método siguiente:

```csharp
using InfColorPicker;
...

private void HandleTouchUpInsideWithStrongDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.Delegate = selector;
    picker.PresentModallyOverViewController (this);
}

```

Primero se obtiene una instancia de `InfColorPickerController` a través de un método estático y se hace que esa instancia sea consciente de nuestro delegado fuerte a través de la propiedad `InfColorPickerController.Delegate` . Esta propiedad se generó automáticamente por el objetivo Sharpie. Por último, se llama `PresentModallyOverViewController` a para mostrar la vista de `InfColorPickerSampleViewController.xib` modo que el usuario pueda seleccionar un color.

**Ejecutar la aplicación** : en este momento, hemos terminado con todo el código. Si ejecuta la aplicación, debería poder cambiar el color de fondo de `InfColorColorPickerSampleView` como se muestra en las siguientes capturas de pantallas:

[![Ejecutar la aplicación](walkthrough-images/run01.png)](walkthrough-images/run01.png#lightbox)

¡Enhorabuena! En este punto, ha creado y enlazado correctamente una biblioteca de Objective-C para su uso en una aplicación de Xamarin. iOS. A continuación, vamos a obtener información sobre el uso de delegados débiles.

### <a name="implementing-a-weak-delegate"></a>Implementar un delegado débil

En lugar de subclaser una clase enlazada al protocolo Objective-C para un determinado delegado, Xamarin. iOS también le permite implementar los métodos de protocolo en cualquier clase que derive de `NSObject` , decorar los métodos con `ExportAttribute` y, a continuación, proporcionar los selectores adecuados. Al adoptar este enfoque, se asigna una instancia de la clase a la `WeakDelegate` propiedad en lugar de a la `Delegate` propiedad. Un delegado débil le ofrece la flexibilidad de tomar la clase delegada en una jerarquía de herencia diferente. Vamos a ver cómo implementar y usar un delegado débil en nuestra aplicación de Xamarin. iOS.

**Crear controlador de eventos para TouchUpInside** : vamos a crear un nuevo controlador de eventos para el `TouchUpInside` evento del botón cambiar color de fondo. Este controlador rellenará el mismo rol que el `HandleTouchUpInsideWithStrongDelegate` controlador que hemos creado en la sección anterior, pero usará un delegado débil en lugar de un delegado fuerte. Edite la clase `ViewController` y agregue el método siguiente:

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```

**Update ViewDidLoad** : debemos cambiar `ViewDidLoad` para que use el controlador de eventos que acabamos de crear. Edite `ViewController` y cambie `ViewDidLoad` para que sea similar al siguiente fragmento de código:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Administrar colorPickerControllerDidFinish: mensaje** : cuando `ViewController` finaliza, iOS enviará el mensaje `colorPickerControllerDidFinish:` a `WeakDelegate` . Necesitamos crear un método de C# que pueda controlar este mensaje. Para ello, creamos un método de C# y, a continuación, lo adornamos con `ExportAttribute` . Edite `ViewController` y agregue el método siguiente a la clase:

```csharp
[Export("colorPickerControllerDidFinish:")]
public void ColorPickerControllerDidFinish (InfColorPickerController controller)
{
    View.BackgroundColor = controller.ResultColor;
    DismissViewController (false, null);
}

```

Ejecute la aplicación. Ahora debería comportarse exactamente como antes, pero utiliza un delegado débil en lugar del delegado fuerte. Llegados a este punto, ha completado correctamente este tutorial. Ahora debería conocer cómo crear y usar un proyecto de enlace de Xamarin. iOS.

## <a name="summary"></a>Resumen

En este artículo se explica el proceso de creación y uso de un proyecto de enlace de Xamarin. iOS. En primer lugar, hemos explicado cómo compilar una biblioteca de Objective-C existente en una biblioteca estática. Después, hemos explicado cómo crear un proyecto de enlace de Xamarin. iOS y cómo usar Objective Sharpie para generar las definiciones de API para la biblioteca de Objective-C. Hemos explicado cómo actualizar y retocar las definiciones de API generadas para que sean adecuadas para el consumo público. Una vez finalizado el proyecto de enlace de Xamarin. iOS, pasamos a consumir ese enlace en una aplicación de Xamarin. iOS, centrándose en el uso de delegados seguros y delegados débiles.

## <a name="related-links"></a>Vínculos relacionados

- [Enlace de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Detalles de enlace](~/cross-platform/macios/binding/overview.md)
- [Guía de referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desarrolladores de Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Directrices de diseño de marco](/dotnet/standard/design-guidelines/)