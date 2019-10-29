---
title: Arquitectura de Xamarin. Mac
description: En esta guía se explora Xamarin. Mac y su relación con Objective-C en un nivel bajo. En él se explican conceptos como la compilación, los selectores, los registradores, el inicio de la aplicación y el generador.
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: 51900adb1dd15675e584671f3b06ad6d7572f47d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017562"
---
# <a name="xamarinmac-architecture"></a>Arquitectura de Xamarin. Mac

_En esta guía se explora Xamarin. Mac y su relación con Objective-C en un nivel bajo. En él se explican conceptos como la compilación, los selectores, los registradores, el inicio de la aplicación y el generador._

## <a name="overview"></a>Información general

Las aplicaciones de Xamarin. Mac se ejecutan en el entorno de ejecución de mono y usan el compilador de Xamarin para compilar en lenguaje intermedio (IL), que después se compila Just-in-Time (JIT) en el código nativo en tiempo de ejecución. Esto se ejecuta en paralelo con el tiempo de ejecución de Objective-C. Ambos entornos en tiempo de ejecución se ejecutan sobre un kernel similar a UNIX, específicamente XNU, y exponen varias API al código de usuario, lo que permite a los desarrolladores tener acceso al sistema nativo o administrado subyacente.

En el diagrama siguiente se muestra una introducción básica de esta arquitectura:

[![Diagrama que muestra una introducción básica de la arquitectura](architecture-images/mac-arch.png "Diagrama que muestra una introducción básica de la arquitectura")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>Código nativo y administrado

Al desarrollar para Xamarin, a menudo se utilizan los términos código *nativo* y *administrado* . El código administrado es código que tiene su ejecución administrada por .NET Framework Common Language Runtime o en el caso de Xamarin: el tiempo de ejecución de mono.

El código nativo es código que se ejecutará de forma nativa en la plataforma específica (por ejemplo, Objective-C o incluso código compilado AOT, en un chip ARM). En esta guía se describe cómo se compila el código administrado en código nativo y se explica cómo funciona una aplicación de Xamarin. Mac, lo que hace un uso completo de las API de Mac de Apple a través del uso de enlaces, a la vez que también tiene acceso a. BCL de la red y un lenguaje sofisticado C#como.

## <a name="requirements"></a>Requisitos

Para desarrollar una aplicación maOS con Xamarin.Mac, se requiere lo siguiente:

- Un equipo Mac que ejecuta macOS Sierra (10,12) o superior.
- La versión más reciente de Xcode (instalada desde la [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- La versión más reciente de Xamarin. Mac y Visual Studio para Mac

La ejecución de aplicaciones de Mac creadas con Xamarin.Mac tiene los siguientes requisitos del sistema:

- Un equipo Mac que ejecute Mac OS X 10,7 o superior.

## <a name="compilation"></a>Compilación

Al compilar cualquier aplicación de plataforma de Xamarin C# , el F#compilador mono (o) se C# ejecutará y compilará el código y F# en el lenguaje intermedio de Microsoft (MSIL o IL). A continuación, Xamarin. Mac usa un compilador *Just-in-Time (JIT)* en tiempo de ejecución para compilar código nativo, lo que permite la ejecución en la arquitectura correcta según sea necesario.

Esto contrasta con Xamarin. iOS, que usa la compilación de AOT. Al utilizar el compilador AOT, todos los ensamblados y todos los métodos que contiene se compilan en tiempo de compilación. Con JIT, la compilación se produce a petición solo para los métodos que se ejecutan.

Con las aplicaciones de Xamarin. Mac, mono normalmente se incrusta en el lote de aplicaciones (y se conoce como **mono incrustado**). Cuando se usa la API clásica de Xamarin. Mac, la aplicación podría usar en su lugar el **sistema mono**, pero esto no se admite en el Unified API. El sistema mono hace referencia a mono que se ha instalado en el sistema operativo. Al iniciarse la aplicación, la aplicación de Xamarin. Mac la usará.

## <a name="selectors"></a>Selectores

Con Xamarin, tenemos dos ecosistemas independientes, .NET y Apple, que necesitamos reunir para que parezcan lo más sencillos posible, para asegurarse de que el objetivo final es una experiencia de usuario fluida. Hemos mostrado en la sección anterior cómo se comunican los dos tiempos de ejecución y es posible que haya oído hablar del término "enlaces", que permite usar las API de Mac nativas en Xamarin. Los enlaces se explican en profundidad en la [documentación de enlace de Objective-C](~/mac/platform/binding.md), por lo que, por ahora, vamos a explorar cómo funciona Xamarin. Mac en el capó.

En primer lugar, debe haber una manera de exponer Objective-C a C#, que se realiza a través de selectores. Un selector es un mensaje que se envía a un objeto o una clase. Con Objective-C, esto se hace a través de las funciones de [objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html) . Para más información sobre el uso de selectores, consulte la guía de los [selectores de Objective-C](~/ios/internals/objective-c-selectors.md) de iOS. También debe haber una manera de exponer el código administrado a Objective-C, que es más complicado debido al hecho de que Objective-C no conoce nada sobre el código administrado. Para evitar esto, usamos un [registrador](~/mac/internals/registrar.md). Esto se explica con más detalle en la sección siguiente.

## <a name="registrar"></a>registrador

Como se mencionó anteriormente, el registrador es un código que expone código administrado a Objective-C. Para ello, crea una lista de todas las clases administradas que se derivan de NSObject:

- Para todas las clases que no contengan una clase de Objective-C existente, se crea una nueva clase de Objective-C con miembros de Objective-C que reflejen todos los miembros administrados que tienen un atributo `[Export]`.
- En las implementaciones de cada miembro de Objective-C, se agrega automáticamente el código para llamar al miembro administrado reflejado.

El pseudo-código siguiente muestra un ejemplo de cómo hacerlo:

**C#(código administrado):**

```csharp
class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
 ```

**Objective-C (código nativo):**

```objc
@interface MyViewController : UIViewController
 - (void)myFunc;
@end 

@implementation MyViewController
- (void)myFunc {
    // Code to call the managed C# MyFunc method in MyViewController
}
@end
```

El código administrado puede contener los atributos, `[Register]` y `[Export]`, que el registrador usa para saber que el objeto debe exponerse a Objective-C. El atributo [Register] se usa para especificar el nombre de la clase de Objective-C generada en caso de que el nombre generado predeterminado no sea adecuado. Todas las clases derivadas de NSObject se registran automáticamente con Objective-C. El atributo [Export] requerido contiene una cadena, que es el selector que se usa en la clase generada de Objective-C.

Hay dos tipos de registradores usados en Xamarin. Mac: dinámico y estático:

- Registradores dinámicos: este es el registrador predeterminado para todas las compilaciones de Xamarin. Mac. El registrador dinámico realiza el registro de todos los tipos del ensamblado en tiempo de ejecución. Para ello, usa las funciones proporcionadas por la API en tiempo de ejecución de Objective-C. Por lo tanto, el registrador dinámico tiene un inicio más lento, pero un tiempo de compilación más rápido. Las funciones nativas (normalmente en C), denominadas trampolines, se usan como implementaciones de método al usar los registradores dinámicos. Varían entre diferentes arquitecturas.
- Registradores estáticos: el registrador estático genera código de Objective-C durante la compilación, que se compila en una biblioteca estática y se vincula al archivo ejecutable. Esto permite un inicio más rápido, pero tarda más tiempo en el momento de la compilación.

## <a name="application-launch"></a>Inicio de la aplicación

La lógica de inicio de Xamarin. Mac variará en función de si se usa incrustado o mono del sistema. Para ver el código y los pasos para el inicio de la aplicación de Xamarin. Mac, consulte el archivo de [encabezado Launch](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h) en el repositorio público de Xamarin-macios.

## <a name="generator"></a>Generator

Xamarin. Mac contiene definiciones para cada API de Mac. Puede examinar cualquiera de ellas en el repositorio de [GitHub de MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Estas definiciones contienen interfaces con atributos, así como cualquier método y propiedad necesarios. Por ejemplo, el código siguiente se usa para definir un NSBox en el [espacio de nombres AppKit](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526). Tenga en cuenta que se trata de una interfaz con una serie de métodos y propiedades:

```csharp
[BaseType (typeof (NSView))]
public interface NSBox {

    …

    [Export ("borderRect")]
    CGRect BorderRect { get; }

    [Export ("titleRect")]
    CGRect TitleRect { get; }

    [Export ("titleCell")]
    NSObject TitleCell { get; }

    [Export ("sizeToFit")]
    void SizeToFit ();

    [Export ("contentViewMargins")]
    CGSize ContentViewMargins { get; set; }

    [Export ("setFrameFromContentFrame:")]
    void SetFrameFromContentFrame (CGRect contentFrame);

    …

}
```

El generador, llamado `bmac` en Xamarin. Mac, toma estos archivos de definición y usa las herramientas de .NET para compilarlos en un ensamblado temporal. Sin embargo, este ensamblado temporal no se utiliza para llamar a código de Objective-C. A continuación, el generador lee el ensamblado C# temporal y genera código que se puede usar en tiempo de ejecución. Por este motivo, por ejemplo, si agrega un atributo Random al archivo Definition. CS, no aparecerá en el código de salida. El generador no lo conoce y, por lo tanto, `bmac` no sabe buscarlo en el ensamblado temporal para generarlo.

Una vez creado el archivo Xamarin. Mac. dll, el empaquetador, `mmp`, agrupará todos los componentes.

En un nivel alto, se consigue mediante la ejecución de las siguientes tareas:

- Cree una estructura de agrupación de aplicaciones.
- Copie los ensamblados administrados.
- Si la vinculación está habilitada, ejecute el enlazador administrado para optimizar los ensamblados quitando las partes no utilizadas.
- Cree una aplicación de iniciador, vinculando en el código del iniciador que se ha hablado junto con el código del registrador en el modo estático.

A continuación, se ejecuta como parte del proceso de compilación de usuario que compila el código de usuario en un ensamblado que hace referencia a Xamarin. Mac. dll de y se ejecuta `mmp` para convertirlo en un paquete.

Para obtener información más detallada sobre el enlazador y cómo se usa, consulte la guía del [enlazador](~/ios/deploy-test/linker.md) de iOS.

## <a name="summary"></a>Resumen

En esta guía se ha examinado la compilación de aplicaciones de Xamarin. Mac y se ha explorado Xamarin. Mac y su relación con Objective-C.
