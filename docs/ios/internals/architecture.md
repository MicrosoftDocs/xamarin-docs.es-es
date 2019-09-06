---
title: Arquitectura de aplicaciones de iOS
description: En este documento se describe Xamarin. iOS en un nivel bajo, en el que se explica cómo interactúan el código nativo y administrado, la compilación de AOT, los selectores, los registradores, el inicio de la aplicación y el generador.
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 7d59a295961c25ecfcc99bb54fdc188c957cf3ee
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291953"
---
# <a name="ios-app-architecture"></a>Arquitectura de aplicaciones de iOS

Las aplicaciones de Xamarin. iOS se ejecutan en el entorno de ejecución de mono y usan la compilación completa de antemano C# para compilar el código en el lenguaje de ensamblado ARM. Esto se ejecuta en paralelo con el [tiempo de ejecución de Objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Ambos entornos en tiempo de ejecución se ejecutan sobre un kernel similar a UNIX, específicamente [XNU](https://en.wikipedia.org/wiki/XNU), y exponen varias API al código de usuario, lo que permite a los desarrolladores tener acceso al sistema nativo o administrado subyacente.

En el diagrama siguiente se muestra una introducción básica de esta arquitectura:

[![](architecture-images/ios-arch-small.png "En este diagrama se muestra una introducción básica de la arquitectura de compilación de antemano de tiempo (AOT)")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>Código nativo y administrado: Una explicación

Al desarrollar para Xamarin, a menudo se utilizan los términos código *nativo y administrado* . El [código administrado](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/) es código que tiene su ejecución administrada por [.NET Framework Common Language Runtime](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx)o en el caso de Xamarin: el tiempo de ejecución de mono. Esto es lo que llamamos un lenguaje intermedio.

El código nativo es código que se ejecutará de forma nativa en la plataforma específica (por ejemplo, Objective-C o incluso código compilado AOT, en un chip ARM). En esta guía se describe cómo el AOT compila el código administrado en código nativo y se explica cómo funciona una aplicación de Xamarin. iOS, lo que hace un uso completo de las API de iOS de Apple a través del uso de enlaces, a la vez que también tiene acceso a. BCL de la red y un lenguaje sofisticado C#como.

## <a name="aot"></a>AOT

Al compilar cualquier aplicación de plataforma de Xamarin C# , el F#compilador mono (o) se C# ejecutará y compilará el código y F# en el lenguaje intermedio de Microsoft (MSIL). Si está ejecutando una aplicación de Xamarin. Android, Xamarin. Mac o incluso una aplicación de Xamarin. iOS en el simulador, [.net Common Language Runtime (CLR)](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx) COMPILA el MSIL mediante un compilador Just-in-Time (JIT). En tiempo de ejecución, se compila en un código nativo, que se puede ejecutar en la arquitectura correcta para la aplicación.

Sin embargo, hay una restricción de seguridad en iOS, establecida por Apple, que no permite la ejecución de código generado dinámicamente en un dispositivo.
Para asegurarse de que se adhiere a estos protocolos de seguridad, Xamarin. iOS usa en su lugar un compilador de antemano para compilar el código administrado. Esto genera un archivo binario de iOS nativo, optimizado opcionalmente con LLVM para dispositivos, que se puede implementar en el procesador Basado en ARM de Apple. A continuación se muestra un diagrama aproximado de cómo encaja esto:

[![](architecture-images/aot.png "Diagrama aproximado de cómo encaja esto")](architecture-images/aot-large.png#lightbox)

El uso de AOT tiene una serie de limitaciones, que se detallan en la guía de [limitaciones](~/ios/internals/limitations.md) . También proporciona varias mejoras con respecto a JIT a través de una reducción en el tiempo de inicio y varias optimizaciones de rendimiento.

Ahora que hemos explorado cómo se compila el código de origen a código nativo, echemos un vistazo a la vista de cómo Xamarin. iOS nos permite escribir aplicaciones de iOS totalmente nativas.

## <a name="selectors"></a>Selectores

Con Xamarin, tenemos dos ecosistemas independientes, .NET y Apple, que necesitamos reunir para que parezcan lo más sencillos posible, para asegurarse de que el objetivo final es una experiencia de usuario fluida. Hemos mostrado en la sección anterior cómo se comunican los dos tiempos de ejecución y es posible que haya oído hablar del término "enlaces", que permite usar las API nativas de iOS en Xamarin. Los enlaces se explican en profundidad en nuestra documentación de [enlace de Objective-C](~/cross-platform/macios/binding/overview.md) , por lo que ahora veremos cómo funciona iOS en el capó.

En primer lugar, debe haber una manera de exponer Objective-C a C#, que se realiza a través de selectores. Un selector es un mensaje que se envía a un objeto o una clase. Con Objective-C, esto se hace a través de las funciones de [objc_msgSend](~/cross-platform/macios/binding/overview.md) .
Para más información sobre el uso de selectores, consulte la guía de [selectores de Objective-C](~/ios/internals/objective-c-selectors.md) . También debe haber una manera de exponer el código administrado a Objective-C, que es más complicado debido al hecho de que Objective-C no conoce nada sobre el código administrado. Para solucionar este fin, usamos *registradores*. Estos se explican con más detalle en la sección siguiente.

## <a name="registrars"></a>Registradores

Como se mencionó anteriormente, el registrador es un código que expone código administrado a Objective-C. Para ello, crea una lista de todas las clases administradas que se derivan de NSObject:

- Para todas las clases que no contengan una clase de Objective-c existente, se crea una nueva clase de Objective-c con miembros de Objective-c que reflejan todos los miembros`Export`administrados que tienen un atributo [].

- En las implementaciones de cada miembro de Objective-C, se agrega automáticamente el código para llamar al miembro administrado reflejado.

El pseudo-código siguiente muestra un ejemplo de cómo hacerlo:

**C#(Código administrado)**

```csharp
 class MyViewController : UIViewController{
     [Export ("myFunc")]
     public void MyFunc ()
     {
     }
 }
```

**Objective-C:**

```objectivec
@interface MyViewController : UIViewController { }

    -(void)myFunc;
@end

@implementation MyViewController {}

    -(void) myFunc
    {
        /* code to call the managed MyViewController.MyFunc method */
    }
@end

```

El código administrado puede contener los atributos, `[Register]` y `[Export]`, que el registrador usa para saber que el objeto debe exponerse a Objective-C.
El `[Register]` atributo se usa para especificar el nombre de la clase de Objective-C generada en caso de que el nombre generado predeterminado no sea adecuado. Todas las clases derivadas de NSObject se registran automáticamente con Objective-C.
El atributo `[Export]` required contiene una cadena, que es el selector que se usa en la clase generada de Objective-C.

Hay dos tipos de registradores que se usan en Xamarin. iOS: dinámico y estático:


- **Registradores dinámicos** : el registrador dinámico realiza el registro de todos los tipos del ensamblado en tiempo de ejecución. Para ello, usa las funciones proporcionadas por [la API en tiempo de ejecución de Objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Por lo tanto, el registrador dinámico tiene un inicio más lento, pero un tiempo de compilación más rápido. Este es el valor predeterminado para el simulador de iOS. Las funciones nativas (normalmente en C), denominadas trampolines, se usan como implementaciones de método al usar los registradores dinámicos. Varían entre diferentes arquitecturas.

- **Registradores estáticos** : el registrador estático genera código de Objective-C durante la compilación, que se compila en una biblioteca estática y se vincula al archivo ejecutable. Esto permite un inicio más rápido, pero tarda más tiempo en el momento de la compilación. Se utiliza de forma predeterminada para las compilaciones de dispositivos. También se puede usar el registrador estático con el simulador de `--registrar:static` iOS pasando `mtouch` como atributo en las opciones de compilación del proyecto, como se muestra a continuación:

    [![](architecture-images/image1.png "Establecer argumentos Mtouch adicionales")](architecture-images/image1.png#lightbox)

Para obtener más información sobre los detalles del sistema de registro de tipo iOS que usa Xamarin. iOS, consulte la guía del [registrador de tipos](~/ios/internals/registrar.md) .

## <a name="application-launch"></a>Inicio de la aplicación

El punto de entrada de todos los ejecutables de Xamarin. iOS se proporciona `xamarin_main`mediante una función llamada, que inicializa mono.

En función del tipo de proyecto, se realiza lo siguiente:

- En el caso de las aplicaciones estándar de iOS y tvOS, se llama al método Main administrado, proporcionado por la aplicación de Xamarin. A continuación, este método Main `UIApplication.Main`administrado llama a, que es el punto de entrada de Objective-C. UIApplication. Main es el enlace para el método de `UIApplicationMain` Objective-C.
- En el caso de las extensiones, `NSExtensionMain` se llama`NSExtensionmain` a la función nativa, o (para las extensiones de watchos), proporcionada por las bibliotecas de Apple. Dado que estos proyectos son bibliotecas de clases y no proyectos ejecutables, no hay ningún método Main administrado para ejecutarse.

Toda esta secuencia de inicio se compila en una biblioteca estática, que luego se vincula al ejecutable final para que la aplicación sepa cómo sacar el suelo.

En este punto, la aplicación se ha iniciado, se está ejecutando mono, estamos en código administrado y sabemos cómo llamar a código nativo y volver a llamarlo. Lo siguiente que debemos hacer es empezar a agregar controles y hacer que la aplicación sea interactiva.


## <a name="generator"></a>Generator

Xamarin. iOS contiene definiciones para cada API de iOS única. Puede examinar cualquiera de ellas en el repositorio de [GitHub de MaciOS](https://github.com/xamarin/xamarin-macios/tree/master/src). Estas definiciones contienen interfaces con atributos, así como cualquier método y propiedad necesarios. Por ejemplo, el código siguiente se usa para definir un UIToolbar en el [espacio de nombres](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327)UIKit. Tenga en cuenta que se trata de una interfaz con una serie de métodos y propiedades:

```csharp
[BaseType (typeof (UIView))]
public interface UIToolbar : UIBarPositioning {
    [Export ("initWithFrame:")]
    IntPtr Constructor (CGRect frame);

    [Export ("barStyle")]
    UIBarStyle BarStyle { get; set; }

    [Export ("items", ArgumentSemantic.Copy)][NullAllowed]
    UIBarButtonItem [] Items { get; set; }

    [Export ("translucent", ArgumentSemantic.Assign)]
    bool Translucent { [Bind ("isTranslucent")] get; set; }

    // done manually so we can keep this "in sync" with 'Items' property
    //[Export ("setItems:animated:")][PostGet ("Items")]
    //void SetItems (UIBarButtonItem [] items, bool animated);

    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage ([NullAllowed] UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);

    ...
}
```

El generador, llamado [`btouch`](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) en Xamarin. iOS, toma estos archivos de definición y usa las herramientas de .net para [compilarlos en un ensamblado temporal](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318). Sin embargo, este ensamblado temporal no se utiliza para llamar a código de Objective-C. A continuación, el generador lee el ensamblado C# temporal y genera código que se puede usar en tiempo de ejecución.
Por este motivo, por ejemplo, si agrega un atributo Random al archivo Definition. CS, no aparecerá en el código de salida. El generador no lo conoce y, por `btouch` lo tanto, no sabe buscarlo en el ensamblado temporal para generarlo.

Una vez creado el archivo Xamarin. iOS. dll, Mtouch agrupará todos los componentes.

En un nivel alto, se consigue mediante la ejecución de las siguientes tareas:

- Cree una estructura de agrupación de aplicaciones.
- Copie los ensamblados administrados.
- Si la vinculación está habilitada, ejecute el enlazador administrado para optimizar los ensamblados; para ello, copie los elementos no utilizados.
- Compilación de AOT.
- Cree un archivo ejecutable nativo, que genera una serie de bibliotecas estáticas (una para cada ensamblado) que están vinculadas al archivo ejecutable nativo, de modo que el ejecutable nativo esté compuesto del código del iniciador, el código del registrador (si es estático) y todas las salidas del AOT. compilación


Para obtener información más detallada sobre el enlazador y cómo se usa, consulte la guía del [enlazador](~/ios/deploy-test/linker.md) .

## <a name="summary"></a>Resumen

En esta guía se ha examinado la compilación de AOT de aplicaciones de Xamarin. iOS y se ha explorado Xamarin. iOS y su relación con Objective-C en profundidad.

## <a name="related-links"></a>Vínculos relacionados

- [Limitaciones](~/ios/internals/limitations.md)
- [Enlace de Objective-C](~/cross-platform/macios/binding/overview.md)
- [Selectores de Objective-C](~/ios/internals/objective-c-selectors.md)
- [Registrar tipo](~/ios/internals/registrar.md)
- [Enlazador](~/ios/deploy-test/linker.md)
