---
title: 'Parte 1: Descripción de la plataforma móvil Xamarin'
description: En este documento se describe la plataforma de Xamarin a alto nivel, en el proceso de compilación, el acceso al SDK de la plataforma, el uso compartido de código, la creación de la interfaz de usuario, los diseñadores visuales, etc.
ms.prod: xamarin
ms.assetid: FBCEF258-D3D8-A420-79ED-3AAB4A7308E4
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: f68f1efe952d86e05fe1d874e94a64b590ad43d7
ms.sourcegitcommit: 4bbf54d2bc1df96af69814e2e5dae47be12e0474
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2021
ms.locfileid: "102602351"
---
# <a name="part-1--understanding-the-xamarin-mobile-platform"></a>Parte 1: Descripción de la plataforma móvil Xamarin

La plataforma Xamarin consta de muchos elementos que le permiten desarrollar aplicaciones para iOS y Android:

- **Lenguaje C#** : permite usar una sintaxis conocida y características sofisticadas como genéricos, LINQ y la biblioteca de tareas paralelas.
- **.NET Framework mono** : proporciona una implementación multiplataforma de las amplias características de .NET Framework de Microsoft.
- **Compilador** : en función de la plataforma, genera una aplicación nativa (por ejemplo, iOS) o una aplicación .net integrada y un tiempo de ejecución (por ejemplo, Android). El compilador también realiza muchas optimizaciones para la implementación móvil, como la vinculación del código no utilizado.
- **Herramientas del IDE** : Visual Studio en Mac y Windows le permite crear, compilar e implementar proyectos de Xamarin.

Además, dado que el lenguaje subyacente es C# con .NET Framework, los proyectos se pueden estructurar para compartir código que también se puede implementar en Windows Phone.

## <a name="under-the-hood"></a>Una mirada al interior

Aunque Xamarin permite escribir aplicaciones en C# y compartir el mismo código en varias plataformas, la implementación real en cada sistema es diferente.

## <a name="compilation"></a>Compilación

El código fuente de C# se convierte en una aplicación nativa de maneras diferentes en cada plataforma:

- **iOS** : C# es de antemano compilado en el lenguaje de ensamblado ARM. Se incluye .NET Framework, donde las clases sin usar se quitan durante la vinculación para reducir el tamaño de la aplicación. Apple no permite la generación de código en tiempo de ejecución en iOS, por lo que algunas características del lenguaje no están disponibles (consulte las  [limitaciones de Xamarin. iOS](~/ios/internals/limitations.md) ).
- **Android** : C# se COMPILA en Il y se empaqueta con MonoVM + JIT'ing. Las clases sin usar en el marco de trabajo se quitan durante la vinculación. La aplicación se ejecuta en paralelo con Java/ART (tiempo de ejecución de Android) e interactúa con los tipos nativos a través de JNI (consulte las  [limitaciones de Xamarin. Android](~/android/internals/limitations.md) ).
- **Windows** : C# se COMPILA en Il y lo ejecuta el tiempo de ejecución integrado y no requiere las herramientas de Xamarin. Diseñar aplicaciones de Windows siguiendo las instrucciones de Xamarin facilita la reutilización del código en iOS y Android.
  El Plataforma universal de Windows también tiene una opción **.net Native** , que se comporta de forma similar a la compilación AOT de Xamarin. iOS.

La documentación del enlazador de [Xamarin. iOS](~/ios/deploy-test/linker.md) y [Xamarin. Android](~/android/deploy-test/linker.md) proporciona más información sobre esta parte del proceso de compilación.

Se debe evitar la compilación en tiempo de ejecución, que genera código dinámicamente con `System.Reflection.Emit` –.

El kernel de Apple evita la generación dinámica de código en dispositivos iOS, por lo que la emisión de código sobre la marcha no funcionará en Xamarin. iOS. Del mismo modo, las características de Dynamic Language Runtime no se pueden usar con las herramientas de Xamarin.

Algunas características de reflexión funcionan (por ejemplo, MonoTouch. Dialog la usa para la API de reflexión), pero no para la generación de código.

## <a name="platform-sdk-access"></a>Acceso al SDK de la plataforma

Xamarin facilita el acceso a las características proporcionadas por el SDK específico de la plataforma con la sintaxis de C# familiar:

- **iOS** : Xamarin. iOS expone los marcos de CocoaTouch SDK de Apple como espacios de nombres a los que se puede hacer referencia desde C#. Por ejemplo, el marco de trabajo de UIKit que contiene todos los controles de interfaz de usuario se puede incluir con una  `using UIKit;` instrucción simple.
- **Android** : Xamarin. Android expone los Android SDK de Google como espacios de nombres, por lo que puede hacer referencia a cualquier parte del SDK compatible con una instrucción using, como  `using Android.Views;` para tener acceso a los controles de la interfaz de usuario.
- **Windows** : las aplicaciones de Windows se compilan con Visual Studio en Windows. Los tipos de proyecto incluyen Windows Forms, WPF, WinRT y el Plataforma universal de Windows (UWP).

## <a name="seamless-integration-for-developers"></a>Perfecta integración para desarrolladores

La belleza de Xamarin es que, a pesar de las diferencias con el capó, Xamarin. iOS y Xamarin. Android (junto con los SDK de Windows de Microsoft) ofrecen una experiencia sin problemas para escribir código de C# que se puede reutilizar en las tres plataformas.

La lógica de negocios, el uso de la base de datos, el acceso a la red y otras funciones comunes pueden escribirse una vez y reutilizarse en cada plataforma, lo que proporciona una base para las interfaces de usuario específicas de la plataforma que buscan y funcionan como aplicaciones nativas.

## <a name="integrated-development-environment-ide-availability"></a>Disponibilidad del entorno de desarrollo integrado (IDE)

El desarrollo de Xamarin se puede realizar en Visual Studio en Mac o Windows. El IDE que elija estará determinado por las plataformas a las que desea dirigirse.

Dado que las aplicaciones de Windows solo se pueden desarrollar en Windows, para compilar para iOS, Android _y_ Windows se requiere Visual Studio para Windows. Sin embargo, es posible compartir proyectos y archivos entre equipos Windows y Mac, por lo que las aplicaciones iOS y Android pueden compilarse en un equipo Mac y el código compartido se podría agregar posteriormente a un proyecto de Windows.

Los requisitos de desarrollo para cada plataforma se describen con más detalle en la guía de [requisitos](~/cross-platform/get-started/requirements.md) .

### <a name="ios"></a>iOS

El desarrollo de aplicaciones de iOS requiere un equipo Mac que ejecute macOS. También puede usar Visual Studio para escribir e implementar aplicaciones de iOS con Xamarin en Visual Studio. Sin embargo, sigue siendo necesario un equipo Mac para fines de compilación y licencia.

El IDE de Xcode de Apple debe estar instalado para proporcionar el compilador y el simulador para las pruebas. Puede realizar pruebas en sus propios dispositivos [de forma gratuita](~/ios/get-started/installation/device-provisioning/free-provisioning.md), pero para compilar aplicaciones para su distribución (por ejemplo, App Store) debe unirse al programa para desarrolladores de Apple ($99 USD al año). Cada vez que envía o actualiza una aplicación, Apple debe revisarla y aprobarla antes de ponerla a disposición de los clientes para que la descarguen.

El código se escribe con el IDE de Visual Studio y los diseños de pantalla se pueden crear mediante programación o editar con Xcode en un equipo Mac.

Consulte la [Guía de instalación de Xamarin. iOS](~/ios/get-started/installation/index.md) para obtener instrucciones detalladas sobre cómo configurar.

### <a name="android"></a>Android

El desarrollo de aplicaciones de Android requiere la instalación de los SDK de Java y Android. Los SDK proporcionan el compilador, el emulador y otras herramientas necesarias para la compilación, implementación y prueba. Java, las herramientas de Android SDK y Xamarin de Google se pueden instalar y ejecutar en Windows y macOS. Se recomiendan las siguientes configuraciones:

- Windows 10 con Visual Studio 2019
- macOS Mojave (10.11 +) con Visual Studio 2019 para Mac

Xamarin proporciona un instalador unificado que configurará el sistema con las herramientas necesarias de Java, Android y Xamarin (incluido un diseñador visual para los diseños de pantalla). Consulte la [Guía de instalación de Xamarin. Android](~/android/get-started/installation/index.md) para obtener instrucciones detalladas.

Puede compilar y probar aplicaciones en un dispositivo real sin ninguna licencia de Google; sin embargo, para distribuir la aplicación a través de un almacén (como Google Play, Amazon o Barnes &amp; noble) se puede pagar una tarifa de registro al operador. Google Play publicará la aplicación al instante, mientras que las demás tiendas tienen un proceso de aprobación similar al de Apple.

### <a name="windows"></a>Windows

Las aplicaciones de Windows (WinForms, WPF o UWP) se compilan con Visual Studio. No usan Xamarin directamente. Sin embargo, el código de C# puede compartirse entre Windows, iOS y Android.
Visite el [centro de desarrollo](https://developer.microsoft.com/) de Microsoft para obtener información sobre las herramientas necesarias para el desarrollo de Windows.

## <a name="creating-the-user-interface-ui"></a>Crear la interfaz de usuario (UI)

Una ventaja clave de usar Xamarin es que la interfaz de usuario de la aplicación usa controles nativos en cada plataforma y crea aplicaciones que no se pueden distinguir desde una aplicación escrita en Objective-C o Java (para iOS y Android, respectivamente).

Al compilar pantallas en la aplicación, puede diseñar los controles en el código o crear pantallas completas con las herramientas de diseño disponibles para cada plataforma.

### <a name="create-controls-programmatically"></a>Crear controles mediante programación

Cada plataforma permite agregar controles de interfaz de usuario a una pantalla mediante código. Esto puede llevar mucho tiempo, ya que puede ser difícil visualizar el diseño terminado al codificar de forma rígida las coordenadas de píxeles para los tamaños y las posiciones de los controles.

Sin embargo, la creación mediante programación de controles tiene ventajas, en especial en iOS para crear vistas que se ajustan de forma diferente a los tamaños de pantalla de iPhone y iPad.

### <a name="visual-designer"></a>Diseñador visual

Cada plataforma tiene un método diferente para la disposición visual de las pantallas:

- **iOS** : los guiones gráficos contienen varias vistas y controles, y se puede tener acceso a ellos en **. Archivo Storyboard** incluido en el proyecto.
- **Android** : Xamarin proporciona un diseñador de interfaz de usuario de arrastrar y colocar de Android para Visual Studio.
- **Windows** : Microsoft proporciona un diseñador de la interfaz de usuario de arrastrar y colocar en Visual Studio y Blend. Los diseños de pantalla se almacenan como. Archivos XAML.

Estas capturas de pantalla muestran los diseñadores de pantallas visuales disponibles en cada plataforma:

 [![Estas capturas de pantalla muestran los diseñadores de pantallas visuales disponibles en cada plataforma](understanding-the-xamarin-mobile-platform-images/designer-all1.png)](understanding-the-xamarin-mobile-platform-images/designer-all1.png#lightbox)

En todos los casos, se puede hacer referencia a los elementos que se crean visualmente en el código.

### <a name="user-interface-considerations"></a>Consideraciones sobre la interfaz de usuario

Una ventaja clave de usar Xamarin para compilar aplicaciones multiplataforma es que pueden aprovechar los kits de versión de interfaz de usuario nativos para presentar una interfaz conocida para el usuario. La interfaz de usuario también funcionará tan rápido como cualquier otra aplicación nativa.

Algunas metáforas de la interfaz de usuario funcionan en varias plataformas (por ejemplo, las tres plataformas usan un control de lista de desplazamiento similar), pero para que la aplicación "sienta", la interfaz de usuario debe aprovechar los elementos de la interfaz de usuario específicos de la plataforma cuando sea necesario. Algunos ejemplos de metáforas de interfaz de usuario específicas de la plataforma son:

- **iOS** : navegación jerárquica con botón atrás, pestañas en la parte inferior de la pantalla.
- **Android** : botón atrás de hardware/sistema-software, menú Acción, pestañas en la parte superior de la pantalla.
- **Windows** : las aplicaciones Windows pueden ejecutarse en equipos de escritorio, tabletas (como Microsoft Surface) y teléfonos. Los dispositivos Windows 10 pueden tener el botón atrás de hardware y los iconos dinámicos, por ejemplo.

Se recomienda leer las directrices de diseño relacionadas con las plataformas a las que se destina:

- **iOS** :  [directrices de interfaz humana de Apple](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html)
- **Android** :  [directrices de la interfaz de usuario de Google](https://developer.android.com/guide/practices/ui_guidelines/index.html)
- **Windows** :  [directrices para el diseño de la experiencia del usuario para Windows](https://developer.microsoft.com/windows/design)

## <a name="library-and-code-reuse"></a>Reutilización de código y biblioteca

La plataforma Xamarin permite la reutilización de código C# existente en todas las plataformas y la integración de las bibliotecas escritas de forma nativa para cada plataforma.

### <a name="c-source-and-libraries"></a>Código fuente y bibliotecas de C#

Dado que los productos de Xamarin usan C# y .NET Framework, se puede volver a usar un gran número de código fuente existente (tanto proyectos internos como de código abierto) en los proyectos de Xamarin. iOS o Xamarin. Android. A menudo, el origen se puede agregar simplemente a una solución de Xamarin y funcionará inmediatamente. Si se ha utilizado una característica de .NET Framework no admitida, es posible que se requieran algunos ajustes.

Entre los ejemplos de código fuente de C# que se pueden usar en Xamarin. iOS o Xamarin. Android se incluyen: SQLite-NET, NewtonSoft.JSON y SharpZipLib.

### <a name="objective-c-bindings--binding-projects"></a>Enlaces de Objective-C + proyectos de enlace

Xamarin proporciona una herramienta denominada *btouch* que ayuda a crear enlaces que permiten el uso de bibliotecas de Objective-C en proyectos de Xamarin. iOS. Consulte la [documentación de tipos de Objective-C de enlace](~/cross-platform/macios/binding/binding-types-reference.md) para más información sobre cómo hacerlo.

Entre los ejemplos de bibliotecas de Objective-C que se pueden usar en Xamarin. iOS se incluyen: Análisis de códigos de barras RedLaser, Google Analytics e integración de PayPal. Los enlaces de Xamarin. iOS de código abierto están disponibles en [GitHub](https://github.com/mono/monotouch-bindings).

### <a name="jar-bindings--binding-projects"></a>Enlaces. jar + proyectos de enlace

Xamarin admite el uso de bibliotecas de Java existentes en Xamarin. Android. Consulte la documentación sobre el [enlace de una biblioteca de Java](~/android/platform/binding-java-library/index.md) para obtener más información sobre cómo usar un. Archivo JAR de Xamarin. Android.

Los enlaces de Xamarin. Android de código abierto están disponibles en [GitHub](https://github.com/mono/monodroid-bindings).

### <a name="c-via-pinvoke"></a>C a través de PInvoke

La tecnología de invocación de plataforma (P/Invoke) permite que el código administrado (C#) llame a métodos en bibliotecas nativas, así como a la compatibilidad con bibliotecas nativas para devolver la llamada a código administrado.

Por ejemplo, la biblioteca de [SQLite-net](https://github.com/praeclarum/sqlite-net) usa instrucciones como esta:

```csharp
[DllImport("sqlite3", EntryPoint = "sqlite3_open", CallingConvention=CallingConvention.Cdecl)]
public static extern Result Open (string filename, out IntPtr db);
```

Esto se enlaza a la implementación nativa de SQLite en lenguaje C en iOS y Android.
Los desarrolladores familiarizados con una API de C existente pueden construir un conjunto de clases de C# que se asignan a la API nativa y usan el código de plataforma existente. Hay documentación para la [vinculación de bibliotecas nativas](~/ios/platform/native-interop.md) en Xamarin. iOS; los principios similares se aplican a Xamarin. Android.

### <a name="c-via-cppsharp"></a>C++ a través de CppSharp

Miguel explica CXXI (ahora denominado [CppSharp](https://github.com/mono/CppSharp)) en este [blog](https://tirania.org/blog/archive/2011/Dec-19.html). Una alternativa al enlace a una biblioteca de C++ directamente es crear un contenedor de C y enlazarlo a a través de P/Invoke.
