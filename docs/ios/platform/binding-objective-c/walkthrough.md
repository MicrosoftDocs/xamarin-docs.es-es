---
title: 'Tutorial: enlace de una biblioteca de Objective-C a iOS'
description: En este artículo se proporciona un tutorial práctico para crear un enlace de Xamarin. iOS para una biblioteca de Objective-C existente, InfColorPicker. En él se tratan temas como la compilación de una biblioteca de Objective-C estática, el enlace y el uso del enlace en una aplicación de Xamarin. iOS.
ms.prod: xamarin
ms.assetid: D3F6FFA0-3C4B-4969-9B83-B6020B522F57
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 05/02/2017
ms.openlocfilehash: b53799f4b1c8d9299ab23191f6a702c2ec0983fb
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285760"
---
# <a name="walkthrough-binding-an-ios-objective-c-library"></a>Tutorial: enlace de una biblioteca de Objective-C a iOS

_En este artículo se proporciona un tutorial práctico para crear un enlace de Xamarin. iOS para una biblioteca de Objective-C existente, InfColorPicker. En él se tratan temas como la compilación de una biblioteca de Objective-C estática, el enlace y el uso del enlace en una aplicación de Xamarin. iOS._

Cuando se trabaja en iOS, pueden darse casos en los que se desea consumir una biblioteca de Objective-C de terceros. En esas situaciones, puede usar un _proyecto de enlace_ de Xamarin. iOS para crear un [ C# enlace](~/cross-platform/macios/binding/overview.md) que le permita utilizar la biblioteca en las aplicaciones de Xamarin. iOS.

Por lo general, en el ecosistema de iOS, puede encontrar bibliotecas en tres tipos:

- Como archivo de biblioteca estática precompilado `.a` con extensión junto con sus encabezados (archivos. h). Por ejemplo, [la biblioteca de análisis de Google](https://developers.google.com/analytics/devguides/collection/ios/v3/sdk-download?hl=es#download_sdk)
- Como marco precompilado. Se trata simplemente de una carpeta que contiene la biblioteca estática, encabezados y, a veces `.framework` , recursos adicionales con extensión. Por ejemplo, [la biblioteca AdMob de Google](https://developers.google.com/admob/ios/download).
- Como solo archivos de código fuente. Por ejemplo, una biblioteca que contenga `.h` los archivos de solo `.m` y Objective C.

En el primer y el segundo escenario, ya habrá una biblioteca estática CocoaTouch precompilada, por lo que en este artículo nos centraremos en el tercer escenario. Recuerde que, antes de empezar a crear un enlace, compruebe siempre la licencia proporcionada con la biblioteca para asegurarse de que tiene la libertad de enlazarla.

En este artículo se proporciona un tutorial paso a paso para crear un proyecto de enlace mediante el proyecto de código abierto [InfColorPicker](https://github.com/InfinitApps/InfColorPicker) Objective-c como ejemplo. sin embargo, toda la información de esta guía puede adaptarse para su uso con cualquier biblioteca de Objective-c de terceros. . La biblioteca InfColorPicker proporciona un controlador de vistas reutilizable que permite al usuario seleccionar un color en función de su representación HSB, lo que hace que la selección de color sea más fácil de usar.

[![](walkthrough-images/run01.png "Ejemplo de la biblioteca InfColorPicker que se ejecuta en iOS")](walkthrough-images/run01.png#lightbox)

Abordaremos todos los pasos necesarios para usar esta API de Objective-C en Xamarin. iOS:

- En primer lugar, crearemos una biblioteca estática de Objective-C con Xcode.
- A continuación, enlazaremos esta biblioteca estática con Xamarin. iOS.
- A continuación, muestre cómo Objective Sharpie puede reducir la carga de trabajo generando automáticamente algunas (pero no todas) las definiciones de API necesarias que requiere el enlace de Xamarin. iOS.
- Por último, vamos a crear una aplicación de Xamarin. iOS que usa el enlace.

En la aplicación de ejemplo se muestra cómo usar un delegado seguro para la comunicación entre la API de C# InfColorPicker y nuestro código. Una vez que hemos visto cómo usar un delegado fuerte, veremos cómo usar delegados débiles para realizar las mismas tareas.

## <a name="requirements"></a>Requisitos

En este artículo se supone que está familiarizado con Xcode y el lenguaje Objective-C y que ha leído nuestra documentación sobre [Objective-c de enlace](~/cross-platform/macios/binding/index.md) . Además, se requiere lo siguiente para completar los pasos que se presentan:

- **Xcode y el SDK de iOS** : Xcode de Apple y la API de iOS más reciente deben instalarse y configurarse en el equipo del desarrollador.
- **[Herramientas de línea de comandos de Xcode](#Installing_the_Xcode_Command_Line_Tools)** : las herramientas de línea de comandos de Xcode deben estar instaladas para la versión instalada actualmente de Xcode (consulte a continuación los detalles de la instalación).
- **Visual Studio para Mac o Visual Studio** : la versión más reciente de Visual Studio para Mac o Visual Studio debe estar instalada y configurada en el equipo de desarrollo. Se necesita un equipo Mac de Apple para desarrollar una aplicación de Xamarin. iOS y, al usar Visual Studio, debe estar conectado a [un host de compilación de Xamarin. iOS.](~/ios/get-started/installation/windows/connecting-to-mac/index.md)
- **La versión más reciente de Objective Sharpie** : una copia actual de la herramienta de objetivo Sharpie descargada [aquí](~/cross-platform/macios/binding/objective-sharpie/get-started.md). Si ya tiene el objetivo Sharpie instalado, puede actualizarlo a la versión más reciente mediante el`sharpie update`

<a name="Installing_the_Xcode_Command_Line_Tools"/>

## <a name="installing-the-xcode-command-line-tools"></a>Instalación de las herramientas de línea de comandos de Xcode

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


Como se indicó anteriormente, usaremos las herramientas de línea de comandos de `make` Xcode `lipo`(específicamente y) en este tutorial. El `make` comando es una utilidad de UNIX muy común que automatiza la compilación de programas ejecutables y bibliotecas mediante un _archivo make_ que especifica cómo se debe compilar el programa. El `lipo` comando es una utilidad de línea de comandos de OS X para crear archivos de varias arquitecturas; `.a` combinará varios archivos en un único archivo que pueden usar todas las arquitecturas de hardware.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Como se indicó anteriormente, usaremos las herramientas de línea de comandos de Xcode en el host de compilación `lipo`de **Mac** (específicamente `make` y) en este tutorial. El `make` comando es una utilidad de UNIX muy común que automatiza la compilación de programas ejecutables y bibliotecas mediante un _archivo make_ para especificar cómo compilar el programa. El `lipo` comando es una utilidad de línea de comandos de OS X para crear archivos de varias arquitecturas; `.a` combinará varios archivos en un único archivo que pueden usar todas las arquitecturas de hardware.


-----

Según la compilación de Apple [desde la línea de comandos con la documentación de preguntas más frecuentes de Xcode](https://developer.apple.com/library/ios/technotes/tn2339/_index.html) , en OS X 10,9 y versiones posteriores, el panel de **descargas** del cuadro de diálogo **preferencias** de Xcode no es compatible con las herramientas de línea de comandos de descarga.

Tendrá que usar uno de los métodos siguientes para instalar las herramientas:

- **Instalar Xcode** : al instalar Xcode, se incluye con todas las herramientas de línea de comandos. En OS X 10,9, las correcciones de `/usr/bin`compatibilidad (instaladas en) pueden asignar `/usr/bin` cualquier herramienta incluida en a la herramienta correspondiente en Xcode. Por ejemplo, el `xcrun` comando, que le permite buscar o ejecutar cualquier herramienta en Xcode desde la línea de comandos.
- **La aplicación de terminal** : desde la aplicación de terminal, puede instalar las herramientas de línea de comandos `xcode-select --install` ejecutando el comando:
  - Inicie la aplicación terminal.
  - Escriba `xcode-select --install` y presione **entrar**; por ejemplo:

  ```bash
  Europa:~ kmullins$ xcode-select --install
  ```

  - Se le pedirá que instale las herramientas de línea de comandos, haga clic en el botón **instalar** : [![](walkthrough-images/xcode01.png "Instalación de las herramientas de línea de comandos")](walkthrough-images/xcode01.png#lightbox)

  - Las herramientas se descargarán e instalarán desde los servidores de Apple: [![](walkthrough-images/xcode02.png "Descargar las herramientas")](walkthrough-images/xcode02.png#lightbox)

- **Descargas para desarrolladores de Apple** : el paquete de herramientas de línea de comandos está disponible en la Página Web [descargas para desarrolladores de Apple](https://developer.apple.com/downloads/index.action) . Inicie sesión con su ID de Apple y, a continuación, busque y descargue las herramientas de línea de comandos: [![](walkthrough-images/xcode03.png "Buscar las herramientas de la línea de comandos")](walkthrough-images/xcode03.png#lightbox)

Con las herramientas de línea de comandos instaladas, estamos listos para continuar con el tutorial.

## <a name="walkthrough"></a>Tutorial

En este tutorial, trataremos los pasos siguientes:

- **[Crear una biblioteca estática](#Creating_A_Static_Library)** : este paso implica la creación de una biblioteca estática del código de Objective-C de **InfColorPicker** . La biblioteca estática tendrá la `.a` extensión de archivo y se incrustará en el ensamblado .net del proyecto de biblioteca.
- **[Crear un proyecto de enlace de Xamarin. iOS](#Create_a_Xamarin.iOS_Binding_Project)** : una vez que tengamos una biblioteca estática, la usaremos para crear un proyecto de enlace de Xamarin. iOS. El proyecto de enlace consta de la biblioteca estática que se acaba de crear y de los C# metadatos en forma de código que explica cómo se puede usar la API de Objective-C. Estos metadatos se conocen normalmente como definiciones de la API. Vamos a usar **[Objective Sharpie](#Using_Objective_Sharpie)** para ayudarnos a crear las definiciones de la API.
- **[Normalizar las definiciones de la API](#Normalize_the_API_Definitions)** : Objective Sharpie realiza un trabajo excelente para ayudarnos, pero no puede hacer todo. Analizaremos algunos cambios que necesitamos realizar en las definiciones de la API antes de poder usarlos.
- **[Usar la biblioteca de enlaces](#Using_the_Binding)** : por último, crearemos una aplicación de Xamarin. iOS para mostrar cómo usar nuestro proyecto de enlace recién creado.

Ahora que sabemos qué pasos son necesarios, vamos a pasar al resto del tutorial.

<a name="Creating_A_Static_Library"/>

## <a name="creating-a-static-library"></a>Crear una biblioteca estática

Si inspeccionamos el código de InfColorPicker en github:

[![](walkthrough-images/image02.png "Inspeccione el código de InfColorPicker en github")](walkthrough-images/image02.png#lightbox)

Podemos ver los tres directorios siguientes en el proyecto:

- **InfColorPicker** : este directorio contiene el código de Objective-C para el proyecto.
- **PickerSamplePad** : este directorio contiene un proyecto de iPad de ejemplo.
- **PickerSamplePhone** : este directorio contiene un proyecto de iPhone de ejemplo.

Vamos a descargar el proyecto InfColorPicker de [GitHub](https://github.com/InfinitApps/InfColorPicker/archive/master.zip) y a descomprimirlo en el directorio de nuestro elección. Al abrir el destino de Xcode `PickerSamplePhone` para el proyecto, vemos la siguiente estructura de proyecto en Xcode Navigator:

[![](walkthrough-images/image03.png "La estructura del proyecto en Xcode Navigator")](walkthrough-images/image03.png#lightbox)

Este proyecto consigue la reutilización de código agregando directamente el código fuente de InfColorPicker (en el cuadro rojo) en cada proyecto de ejemplo. El código del proyecto de ejemplo se encuentra dentro del cuadro azul. Dado que este proyecto en particular no nos proporciona una biblioteca estática, es necesario crear un proyecto de Xcode para compilar la biblioteca estática.

El primer paso es agregar el código fuente de InfoColorPicker en la biblioteca estática. Para ello, haga lo siguiente:

1. Inicie Xcode.
2. En el menú **archivo** , seleccione **nuevo** > **proyecto...** :

    [![](walkthrough-images/image04.png "Iniciar un nuevo proyecto")](walkthrough-images/image04.png#lightbox)
3. Seleccione **Framework & Library**, la plantilla **biblioteca estática de Cocoa** y haga clic en el botón **siguiente** :

    [![](walkthrough-images/image05.png "Seleccione la plantilla biblioteca estática de coco Touch")](walkthrough-images/image05.png#lightbox)

4. Escriba `InfColorPicker` como **nombre del proyecto** y haga clic en el botón **siguiente** :

    [![](walkthrough-images/image06.png "Escriba InfColorPicker para el nombre del proyecto")](walkthrough-images/image06.png#lightbox)
5. Seleccione una ubicación para guardar el proyecto y haga clic en el botón **Aceptar** .
6. Ahora debemos agregar el origen del proyecto InfColorPicker a nuestro proyecto de biblioteca estática. Dado que el archivo **InfColorPicker. h** ya existe en nuestra biblioteca estática (de forma predeterminada), Xcode no nos permitirá sobrescribirlo. En el **buscador**, navegue hasta el código fuente de InfColorPicker en el proyecto original que descomprimido de Github, copie todos los archivos InfColorPicker y péguelos en nuestro nuevo proyecto de biblioteca estática:

    [![](walkthrough-images/image12.png "Copie todos los archivos InfColorPicker")](walkthrough-images/image12.png#lightbox)

7. Vuelva a Xcode, haga clic con el botón derecho en la carpeta **InfColorPicker** y seleccione **Agregar archivos a "InfColorPicker..."** :

    [![](walkthrough-images/image08.png "Agregar archivos")](walkthrough-images/image08.png#lightbox)

8. En el cuadro de diálogo Agregar archivos, vaya a los archivos de código fuente de InfColorPicker que acaba de copiar, selecciónelos todos y haga clic en el botón **Agregar** :

    [![](walkthrough-images/image09.png "Seleccionar todo y hacer clic en el botón Agregar")](walkthrough-images/image09.png#lightbox)

9. El código fuente se copiará en nuestro proyecto:

    [![](walkthrough-images/image10.png "El código fuente se copiará en el proyecto.")](walkthrough-images/image10.png#lightbox)

10. En el explorador de proyectos de Xcode, seleccione el archivo **InfColorPicker. m** y marque como comentario las dos últimas líneas (debido a la manera en que se escribió esta biblioteca, este archivo no se usa):

    [![](walkthrough-images/image14.png "Editar el archivo InfColorPicker. m")](walkthrough-images/image14.png#lightbox)

11. Ahora tenemos que comprobar si hay marcos de trabajo requeridos por la biblioteca. Puede encontrar esta información en el archivo Léame o abriendo uno de los proyectos de ejemplo proporcionados. En este ejemplo `Foundation.framework`se `UIKit.framework`usa, `CoreGraphics.framework` y, por tanto, vamos a agregarlos.

12. Seleccione el **destino InfColorPicker > fases de compilación** y expanda la sección **vínculo binario con bibliotecas** :

    [![](walkthrough-images/image16b.png "Expandir la sección vincular binario con bibliotecas")](walkthrough-images/image16b.png#lightbox)

13. Use el **+** botón para abrir el cuadro de diálogo que le permite agregar los marcos de trabajo de Marcos necesarios enumerados anteriormente:

    [![](walkthrough-images/image16c.png "Agregue los marcos de trabajo de Marcos necesarios enumerados anteriormente")](walkthrough-images/image16c.png#lightbox)

14. La sección **vincular binario con bibliotecas** debe ser ahora similar a la imagen siguiente:

    [![](walkthrough-images/image16d.png "La sección Link binario con bibliotecas")](walkthrough-images/image16d.png#lightbox)

En este momento estamos cerca, pero no estamos muy listos. La biblioteca estática se ha creado, pero es necesario compilarla para crear un archivo binario de Fat que incluya todas las arquitecturas necesarias para el dispositivo iOS y el simulador de iOS.

### <a name="creating-a-fat-binary"></a>Creación de un archivo binario de Fat

Todos los dispositivos iOS tienen procesadores con tecnología ARM que se han desarrollado con el tiempo. Cada nueva arquitectura agrega nuevas instrucciones y otras mejoras manteniendo la compatibilidad con versiones anteriores. los dispositivos iOS tienen conjuntos de instrucciones ARMv6, ARMv7, armv7s y arm64, aunque [ARMv6 no se usan más](~/ios/deploy-test/compiling-for-different-devices.md). El simulador de iOS no está basado en ARM y, en su lugar, es un simulador basado en x86 y x86_64. Esto significa que se deben proporcionar bibliotecas para cada conjunto de instrucciones.

Una biblioteca FAT es `.a` un archivo que contiene todas las arquitecturas admitidas.

La creación de un archivo binario FAT es un proceso de tres pasos:

- Compile una versión de ARM 7 & ARM64 de la biblioteca estática.
- Compile una versión x86 y x84_64 de la biblioteca estática.
- Use la `lipo` herramienta de línea de comandos para combinar las dos bibliotecas estáticas en una.

Aunque estos tres pasos son bastante sencillos, puede que sea necesario repetirlos en el futuro cuando la biblioteca de Objective-C Reciba actualizaciones o si se requieren correcciones de errores. Si decide automatizar estos pasos, se simplificará el mantenimiento y soporte técnico futuros del proyecto de enlace de iOS.

Hay muchas herramientas disponibles para automatizar estas tareas: un script de Shell, [inclinación](http://rake.rubyforge.org/), [xbuild](https://www.mono-project.com/docs/tools+libraries/tools/xbuild/)y [Make](https://developer.apple.com/library/mac/documentation/Darwin/Reference/ManPages/man1/make.1.html). Cuando se instalan las herramientas de línea de `make` comandos de Xcode, también se instala, por lo que es el sistema de compilación que se usará para este tutorial. Este es un **archivo make** que puede usar para crear una biblioteca compartida de varias arquitecturas que funcionará en un dispositivo iOS y en el simulador de cualquier biblioteca:

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

[![](walkthrough-images/lib00.png "Guarde el archivo con el nombre Makefile")](walkthrough-images/lib00.png#lightbox)

Abra la aplicación terminal en el equipo Mac y navegue hasta la ubicación del archivo make. Escriba `make` en el terminal, presione **entrar** y se ejecutará el **archivo make** :

[![](walkthrough-images/lib01.png "Salida de archivo make de ejemplo")](walkthrough-images/lib01.png#lightbox)

Al ejecutar make, verá una gran cantidad de texto desplazado por. Si todo funciona correctamente, verá las palabras **compilación correcta** y los `libInfColorPicker-armv7.a`archivos, `libInfColorPicker-i386.a` y `libInfColorPickerSDK.a` se copiarán en la misma ubicación que el **archivo make**:

[![](walkthrough-images/lib02.png "Los archivos libInfColorPicker-ARMv7. a, libInfColorPicker-i386. a y libInfColorPickerSDK. a generados por el archivo make")](walkthrough-images/lib02.png#lightbox)

Puede confirmar las arquitecturas dentro del archivo binario FAT mediante el comando siguiente:

```bash
xcrun -sdk iphoneos lipo -info libInfColorPicker.a
```

Esto debería mostrar lo siguiente:

```bash
Architectures in the fat file: libInfColorPicker.a are: i386 armv7 x86_64 arm64
```

En este punto, hemos completado el primer paso de nuestro enlace de iOS mediante la creación de una biblioteca estática con Xcode y las herramientas `make` de línea de comandos de Xcode y. `lipo` Vamos a pasar al siguiente paso y usar **Objective-Sharpie** para automatizar la creación de los enlaces de API para nosotros.

<a name="Create_a_Xamarin.iOS_Binding_Project"/>

## <a name="create-a-xamarinios-binding-project"></a>Creación de un proyecto de enlace de Xamarin. iOS

Antes de poder usar **Objective-Sharpie** para automatizar el proceso de enlace, es necesario crear un proyecto de enlace de Xamarin. iOS para hospedar las definiciones de API (que vamos a usar **Objective-Sharpie** para ayudarnos a compilar) y crear el C# enlace para nosotros.

Vamos a hacer lo siguiente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie Visual Studio para Mac.
1. En el menú **archivo** , seleccione **nueva** > **solución...** :

    ![](walkthrough-images/bind01.png "Iniciar una nueva solución")

1. En el cuadro de diálogo Nueva solución, seleccione **biblioteca** > **enlace de iOS proyecto**:

    ![](walkthrough-images/bind02.png "Selección del proyecto de enlace de iOS")

1. Haga clic en el botón **siguiente** .

1. Escriba "InfColorPickerBinding" como **nombre del proyecto** y haga clic en el botón **crear** para crear la solución:

    ![](walkthrough-images/bind02a.png "Escriba InfColorPickerBinding como nombre del proyecto")

Se creará la solución y se incluirán dos archivos predeterminados:

![](walkthrough-images/bind03.png "Estructura de la solución en el Explorador de soluciones")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. Inicie Visual Studio.

1. En el menú **archivo** , seleccione **nuevo** > **proyecto...** :

    ![Iniciar un nuevo proyecto](walkthrough-images/bind01vs.png "Iniciar un nuevo proyecto")

1. En el cuadro de diálogo nuevo proyecto, **Seleccione C# Visual > iPhone & iPad > biblioteca de enlaces de iOS (Xamarin)** :

    [![Selección de la biblioteca de enlaces de iOS](walkthrough-images/bind02.w157-sml.png)](walkthrough-images/bind02.w157.png#lightbox)

1. Escriba "InfColorPickerBinding" como **nombre** y haga clic en el botón **Aceptar** para crear la solución.

Se creará la solución y se incluirán dos archivos predeterminados:

![](walkthrough-images/bind03vs.png "Estructura de la solución en el Explorador de soluciones")

-----

- **ApiDefinition.CS** : este archivo contendrá los contratos que definen cómo se encapsularán las API de Objective-C C#en.
- **Structs.CS** : este archivo contendrá las estructuras o los valores de enumeración necesarios para las interfaces y los delegados.

Vamos a trabajar con estos dos archivos más adelante en el tutorial. En primer lugar, es necesario agregar la biblioteca InfColorPicker al proyecto de enlace.

### <a name="including-the-static-library-in-the-binding-project"></a>Incluir la biblioteca estática en el proyecto de enlace

Ahora que tenemos nuestro proyecto de enlace base listo, necesitamos agregar la biblioteca binaria FAT que hemos creado anteriormente para la biblioteca **InfColorPicker** .

Siga estos pasos para agregar la biblioteca:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Haga clic con el botón derecho en la carpeta **referencias nativas** en el panel de solución y seleccione **Agregar referencias nativas**:

    ![](walkthrough-images/bind04a.png "Agregar referencias nativas")

1. Navegue hasta el archivo binario de Fat que`libInfColorPickerSDK.a`hicimos anteriormente () y presione el botón **abrir** :

    ![](walkthrough-images/bind05.png "Seleccione el archivo libInfColorPickerSDK. a")
1. El archivo se incluirá en el proyecto:

    ![](walkthrough-images/bind04.png "Incluir un archivo")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Copie el `libInfColorPickerSDK.a` del **host de compilación de Mac** y péguelo en el proyecto de enlace.

1. Haga clic con el botón derecho en el proyecto y elija **agregar > elemento existente...** :

    ![](walkthrough-images/bind04vs.png "Agregar un archivo existente")

1. Navegue hasta el `libInfColorPickerSDK.a` y haga clic en el botón **Agregar** :

    ![](walkthrough-images/bind05vs.png "Adding libInfColorPickerSDK.a")

1. El archivo se incluirá en el proyecto.

-----

Cuando el archivo **. a** se agrega al proyecto, Xamarin. iOS establecerá automáticamente la **acción de compilación** del archivo en **ObjcBindingNativeLibrary**y creará un archivo especial denominado `libInfColorPickerSDK.linkwith.cs`.


Este archivo contiene el `LinkWith` atributo que indica a Xamarin. iOS cómo controlar la biblioteca estática que acabamos de agregar. El contenido de este archivo se muestra en el siguiente fragmento de código:

```csharp
using ObjCRuntime;

[assembly: LinkWith ("libInfColorPickerSDK.a", SmartLink = true, ForceLoad = true)]
```

El `LinkWith` atributo identifica la biblioteca estática del proyecto y algunas marcas importantes del enlazador.


Lo siguiente que debemos hacer es crear las definiciones de la API para el proyecto InfColorPicker. Para los fines de este tutorial, usaremos Sharpie objetivo para generar el archivo **ApiDefinition.CS**.

<a name="Using_Objective_Sharpie"/>

## <a name="using-objective-sharpie"></a>Uso de Sharpie objetivo

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


Objective Sharpie es una herramienta de línea de comandos (proporcionada por Xamarin) que puede ayudar a crear las definiciones necesarias para enlazar una biblioteca de Objective- C#C de terceros con. En esta sección, usaremos Sharpie objetivo para crear el **ApiDefinition.CS** inicial del proyecto InfColorPicker.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Objective Sharpie es una herramienta de línea de comandos (proporcionada por Xamarin) que puede ayudar a crear las definiciones necesarias para enlazar una biblioteca de Objective- C#C de terceros con. En esta sección, usaremos Sharpie objetivo en nuestro **host de compilación de Mac** para crear el **ApiDefinition.CS** inicial del proyecto InfColorPicker.


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

  xcode    Get information about Xcode installations and available SDKs.

  bind     Create a Xamarin C# binding to Objective-C APIs
Europa:Resources kmullins$
```

Para este tutorial, usaremos las siguientes herramientas de Sharpie de objetivos:

- **Xcode** : estas herramientas nos proporcionan información sobre nuestra instalación actual de Xcode y las versiones de las API de iOS y Mac que hemos instalado. Esta información se utilizará más adelante cuando se generen los enlaces.
- **BIND** : usaremos esta herramienta para analizar los archivos **. h** del proyecto InfColorPicker en los archivos **ApiDefinition.CS** y **StructsAndEnums.CS** iniciales.

Para obtener ayuda sobre una herramienta específica de Sharpie de objetivos, escriba el nombre de la herramienta `-help` y la opción. Por ejemplo, `sharpie xcode -help` devuelve el siguiente resultado:

```bash
Europa:Resources kmullins$ sharpie xcode -help
usage: sharpie xcode [OPTIONS]+

Options:
  -h, --help                 Show detailed help
  -v, --verbose              Be verbose with output
      --sdks                 List all available Xcode SDKs. Pass -verbose for
                               more details.
Europa:Resources kmullins$
```

Antes de que podamos iniciar el proceso de enlace, necesitamos obtener información sobre nuestros SDK instalados actuales escribiendo el siguiente comando en el terminal `sharpie xcode -sdks`:

```bash
amyb:Desktop amyb$ sharpie xcode -sdks
sdk: appletvos9.2    arch: arm64
sdk: iphoneos9.3     arch: arm64   armv7
sdk: macosx10.11     arch: x86_64  i386
sdk: watchos2.2      arch: armv7
```

En el caso anterior, podemos ver que tenemos instalado el `iphoneos9.3` SDK en nuestro equipo. Con esta información en su lugar, estamos preparados para analizar los archivos de `.h` proyecto de InfColorPicker en el ApiDefinition.CS `StructsAndEnums.cs` inicial y en el proyecto InfColorPicker.

Escriba el siguiente comando en la aplicación terminal:

```bash
sharpie bind --output=InfColorPicker --namespace=InfColorPicker --sdk=[iphone-os] [full-path-to-project]/InfColorPicker/InfColorPicker/*.h
```

Donde `[full-path-to-project]` es la ruta de acceso completa al directorio donde se encuentra el archivo de proyecto **InfColorPicker** Xcode en nuestro equipo y [iPhone-OS] es el SDK de iOS que se ha instalado, como se `sharpie xcode -sdks` indica en el comando. Tenga en cuenta que, en este ejemplo, pasamos  **\*. h** como parámetro, que incluye *todos* los archivos de encabezado de este directorio; normalmente, no debería hacer esto, sino que lea cuidadosamente los archivos de encabezado para buscar el **. h** de nivel superior. archivo que hace referencia a todos los demás archivos pertinentes y simplemente páselo a Objective Sharpie.

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

[![](walkthrough-images/os06.png "Los archivos InfColorPicker.enums.cs y InfColorPicker.cs")](walkthrough-images/os06.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


Abra ambos archivos en el proyecto de enlace que hemos creado anteriormente. Copie el contenido del archivo **InfColorPicker.CS** y péguelo en el archivo **ApiDefinition.CS** , reemplazando el bloque de `namespace ...` código existente por el contenido del archivo **InfColorPicker.CS** (saliendo de las `using` instrucciones). intacta):

![](walkthrough-images/os07.png "El archivo InfColorPickerControllerDelegate")


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


Abra ambos archivos en el proyecto de enlace que hemos creado anteriormente. Copie el contenido del archivo **InfColorPicker.CS** (del host de **compilación de Mac**) y péguelo en el archivo **ApiDefinition.CS** , reemplazando el bloque `namespace ...` de código existente por el contenido del archivo **InfColorPicker.CS** ( las `using` instrucciones permanecen intactas.


-----

<a name="Normalize_the_API_Definitions"/>

## <a name="normalize-the-api-definitions"></a>Normalizar las definiciones de API

A veces `Delegates`, el objetivo Sharpie tiene un problema de traducción, por lo que tendremos que modificar la `InfColorPickerControllerDelegate` definición de la interfaz `[Protocol, Model]` y reemplazar la línea por lo siguiente:

```csharp
[BaseType(typeof(NSObject))]
[Model]
```

Para que la definición tenga el siguiente aspecto:

[![](walkthrough-images/os11.png "La definición")](walkthrough-images/os11.png#lightbox)

A continuación, hacemos lo mismo con el contenido del `InfColorPicker.enums.cs` archivo, copiándolos y pegándolos en el `StructsAndEnums.cs` archivo que deja intactas las `using` instrucciones:

[![](walkthrough-images/os09.png "El contenido del archivo StructsAndEnums.cs")](walkthrough-images/os09.png#lightbox)

También puede encontrar que el objetivo Sharpie ha anotado el enlace con `[Verify]` atributos. Estos atributos indican que debe comprobar que Objective Sharpie hizo lo correcto comparando el enlace con la declaración C/Objective-C original (que se proporcionará en un comentario encima de la declaración enlazada). Una vez que haya comprobado los enlaces, debe quitar el atributo verify. Para obtener más información, consulte la guía de [comprobación](~/cross-platform/macios/binding/objective-sharpie/platform/verify.md) .

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)


En este momento, nuestro proyecto de enlace debe estar completo y listo para compilar. Vamos a compilar nuestro proyecto de enlace y asegurarse de que terminamos sin errores:

[Compile el proyecto de enlace y asegúrese de que no hay errores.](walkthrough-images/os12.png)


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


En este momento, nuestro proyecto de enlace debe estar completo y listo para compilar. Vamos a compilar nuestro proyecto de enlace y asegurarse de que terminamos sin errores.


-----

<a name="Using_the_Binding"/>

## <a name="using-the-binding"></a>Usar el enlace

Siga estos pasos para crear una aplicación de iPhone de ejemplo para usar la biblioteca de enlace de iOS creada anteriormente:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. **Crear proyecto de Xamarin. iOS** : Agregue un nuevo proyecto de Xamarin. iOS denominado **InfColorPickerSample** a la solución, tal como se muestra en las siguientes capturas de pantallas:

    ![](walkthrough-images/use01.png "Agregar una aplicación de vista única")

    ![](walkthrough-images/use01a.png "Establecer el identificador")

1. **Agregar referencia al** proyecto de enlace: actualice el proyecto **InfColorPickerSample** para que tenga una referencia al proyecto **InfColorPickerBinding** :

    ![](walkthrough-images/use02.png "Agregar referencia al proyecto de enlace")

1. **Crear la interfaz de usuario de iPhone** : haga doble clic en el archivo **archivo mainstoryboard. Storyboard** en el proyecto **InfColorPickerSample** para editarlo en el diseñador de iOS. Agregue un **botón** a la vista y llámelo `ChangeColorButton`, como se muestra a continuación:

    ![](walkthrough-images/use03.png "Agregar un botón a la vista")

1. **Agregue InfColorPickerView. Xib** : la biblioteca InfColorPicker Objective-C incluye un archivo **. Xib** . Xamarin. iOS no incluirá this **. Xib** en el proyecto de enlace, lo que producirá errores en tiempo de ejecución en nuestra aplicación de ejemplo. La solución alternativa para esto es agregar el archivo **. Xib** a nuestro proyecto de Xamarin. iOS. Seleccione el proyecto de Xamarin. iOS, haga clic con el botón derecho y seleccione **agregar > agregar archivos**y agregue el archivo **. Xib** como se muestra en la siguiente captura de pantalla:

    ![](walkthrough-images/use04.png "Agregue InfColorPickerView. Xib")

1. Cuando se le pregunte, copie el archivo **. Xib** en el proyecto.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. **Crear proyecto de Xamarin. iOS** : Agregue un nuevo proyecto de Xamarin. iOS denominado **InfColorPickerSample** mediante la plantilla de **aplicación de vista única** :

    [![proyecto de aplicación de iOS (Xamarin)](walkthrough-images/use01.w157-sml.png)](walkthrough-images/use01.w157.png#lightbox)

    [![Seleccionar plantilla](walkthrough-images/use01-2.w157-sml.png)](walkthrough-images/use01-2.w157.png#lightbox)

1. **Agregar referencia al** proyecto de enlace: actualice el proyecto **InfColorPickerSample** para que tenga una referencia al proyecto **InfColorPickerBinding** :

    ![](walkthrough-images/use02vs.png "Agregar referencia al proyecto de enlace")

1. **Crear la interfaz de usuario de iPhone** : haga doble clic en el archivo **archivo mainstoryboard. Storyboard** en el proyecto **InfColorPickerSample** para editarlo en el diseñador de iOS. Agregue un **botón** a la vista y llámelo `ChangeColorButton`, como se muestra a continuación:

    ![](walkthrough-images/use03vs.png "Crear la interfaz de usuario de iPhone")

1. **Agregue InfColorPickerView. Xib** : la biblioteca InfColorPicker Objective-C incluye un archivo **. Xib** . Xamarin. iOS no incluirá this **. Xib** en el proyecto de enlace, lo que producirá errores en tiempo de ejecución en nuestra aplicación de ejemplo. La solución alternativa para esto es agregar el archivo **. Xib** a nuestro proyecto de Xamarin. iOS desde nuestro **host de compilación de Mac**. Seleccione el proyecto de Xamarin. iOS, haga clic con el botón derecho y seleccione **Agregar** > **elemento existente...** y agregue el archivo **. Xib** .

-----

A continuación, echemos un vistazo a los protocolos en Objective-C y cómo los controlamos en el enlace C# y el código.

### <a name="protocols-and-xamarinios"></a>Protocolos y Xamarin. iOS

En Objective-C, un protocolo define métodos (o mensajes) que se pueden usar en determinadas circunstancias. Conceptualmente, son muy similares a las interfaces de C#. Una diferencia importante entre un protocolo Objective-C y una C# interfaz es que los protocolos pueden tener métodos opcionales: métodos que una clase no tiene que implementar. Objective-C usa la @optional palabra clave se usa para indicar qué métodos son opcionales. Para obtener más información sobre [los protocolos, vea eventos, protocolos y delegados](~/ios/app-fundamentals/delegates-protocols-and-events.md).

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

Cuando se compila la biblioteca de enlaces, Xamarin. iOS creará una clase base abstracta `InfColorPickerControllerDelegate`denominada, que implementa esta interfaz con métodos virtuales.

Hay dos maneras de implementar esta interfaz en una aplicación de Xamarin. iOS:

- **Delegado fuerte** : el uso de un delegado fuerte implica C# la creación de una clase `InfColorPickerControllerDelegate` que subclase y invalida los métodos adecuados. **InfColorPickerController** utilizará una instancia de esta clase para comunicarse con sus clientes.
- **Delegado débil** : un delegado débil es una técnica ligeramente diferente que implica la creación de un método público en alguna clase ( `InfColorPickerSampleViewController`como) y, a continuación, la exposición `InfColorPickerDelegate` de ese método `Export` al Protocolo a través de un atributo.

Los delegados fuertes proporcionan IntelliSense, seguridad de tipos y una mejor encapsulación. Por estos motivos, debe utilizar delegados fuertes en los que pueda, en lugar de un delegado débil.

En este tutorial se tratarán ambas técnicas: primero se implementa un delegado fuerte y, a continuación, se explica cómo implementar un delegado débil.

### <a name="implementing-a-strong-delegate"></a>Implementar un delegado fuerte

Finalice la aplicación de Xamarin. iOS mediante un delegado seguro para responder al `colorPickerControllerDidFinish:` mensaje:

**Subclase InfColorPickerControllerDelegate** : agregue una nueva clase al proyecto denominado `ColorSelectedDelegate`. Edite la clase para que tenga el código siguiente:

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

Xamarin. iOS enlazará el delegado de Objective-C mediante la creación de una clase `InfColorPickerControllerDelegate`base abstracta denominada. Subclase de este tipo e invalide el `ColorPickerControllerDidFinish` método para tener acceso al valor de la `ResultColor` propiedad de `InfColorPickerController`.

**Cree una instancia de ColorSelectedDelegate** : nuestro controlador de eventos necesitará una instancia del `ColorSelectedDelegate` tipo que hemos creado en el paso anterior. Edite la `InfColorPickerSampleViewController` clase y agregue la siguiente variable de instancia a la clase:

```csharp
ColorSelectedDelegate selector;
```

**Inicialice la variable ColorSelectedDelegate** : para asegurarse de `selector` que es una instancia válida, actualice el `ViewDidLoad` método `ViewController` de para que coincida con el siguiente fragmento de código:

```csharp
public override void ViewDidLoad ()
{
  base.ViewDidLoad ();
  ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithStrongDelegate;
  selector = new ColorSelectedDelegate (this);
}
```

**Implemente el método HandleTouchUpInsideWithStrongDelegate** -Next implemente el controlador de eventos para cuando el usuario toque **ColorChangeButton**. Edite `ViewController`y agregue el método siguiente:

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

Primero se obtiene una instancia de `InfColorPickerController` a través de un método estático y se hace que esa instancia sea consciente de nuestro delegado `InfColorPickerController.Delegate`fuerte a través de la propiedad. Esta propiedad se generó automáticamente por el objetivo Sharpie. Por último, `PresentModallyOverViewController` se llama a para `InfColorPickerSampleViewController.xib` Mostrar la vista de modo que el usuario pueda seleccionar un color.

**Ejecutar la aplicación** : en este momento, hemos terminado con todo el código. Si ejecuta la aplicación, debería poder cambiar el color de fondo de `InfColorColorPickerSampleView` como se muestra en las siguientes capturas de pantallas:

[![](walkthrough-images/run01.png "Ejecutar la aplicación")](walkthrough-images/run01.png#lightbox)

¡Enhorabuena! En este punto, ha creado y enlazado correctamente una biblioteca de Objective-C para su uso en una aplicación de Xamarin. iOS. A continuación, vamos a obtener información sobre el uso de delegados débiles.

### <a name="implementing-a-weak-delegate"></a>Implementar un delegado débil

En lugar de subclaser una clase enlazada al protocolo Objective-C para un determinado delegado, Xamarin. iOS también le permite implementar los métodos de protocolo en cualquier clase que derive de `NSObject`, decorando los métodos `ExportAttribute`con y, a continuación, proporcionar los selectores adecuados. Al adoptar este enfoque, se asigna una instancia de la clase a la `WeakDelegate` propiedad en lugar de a la `Delegate` propiedad. Un delegado débil le ofrece la flexibilidad de tomar la clase delegada en una jerarquía de herencia diferente. Vamos a ver cómo implementar y usar un delegado débil en nuestra aplicación de Xamarin. iOS.

**Crear controlador de eventos para TouchUpInside** : vamos a crear un nuevo controlador de eventos `TouchUpInside` para el evento del botón cambiar color de fondo. Este controlador rellenará el mismo rol que `HandleTouchUpInsideWithStrongDelegate` el controlador que hemos creado en la sección anterior, pero usará un delegado débil en lugar de un delegado fuerte. Edite la `ViewController`clase y agregue el método siguiente:

```csharp
private void HandleTouchUpInsideWithWeakDelegate (object sender, EventArgs e)
{
    InfColorPickerController picker = InfColorPickerController.ColorPickerViewController();
    picker.WeakDelegate = this;
    picker.SourceColor = this.View.BackgroundColor;
    picker.PresentModallyOverViewController (this);
}
```

**Update ViewDidLoad** : debemos cambiar `ViewDidLoad` para que use el controlador de eventos que acabamos de crear. Edite `ViewController` y `ViewDidLoad` cambie para que sea similar al siguiente fragmento de código:


```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    ChangeColorButton.TouchUpInside += HandleTouchUpInsideWithWeakDelegate;
}

```

**Controle el colorPickerControllerDidFinish: Mensaje** : `colorPickerControllerDidFinish:` `WeakDelegate`cuando finaliza, iOS enviará el mensaje a. `ViewController` Necesitamos crear un C# método que pueda controlar este mensaje. Para ello, creamos un C# método y lo adornamos con. `ExportAttribute` Edite `ViewController`y agregue el método siguiente a la clase:

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

- [Ejemplo de enlace (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/infcolorpicker)
- [Enlace de bibliotecas de Objective-C](~/cross-platform/macios/binding/objective-c-libraries.md)
- [Detalles de enlace](~/cross-platform/macios/binding/overview.md)
- [Guía de referencia de tipos de enlace](~/cross-platform/macios/binding/binding-types-reference.md)
- [Xamarin para desarrolladores de Objective-C](~/ios/get-started/objective-c-developers/index.md)
- [Instrucciones de diseño de .NET Framework](https://msdn.microsoft.com/library/ms229042.aspx)
