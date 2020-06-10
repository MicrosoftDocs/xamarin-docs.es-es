---
title: Funcionamiento de Xamarin.Mac
description: En este documento se describen los trabajos internos de Xamarin. Mac. En concreto, examina los constructores, la administración de la memoria, la compilación por encima del tiempo y el registrador.
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 05/25/2017
ms.openlocfilehash: 05bb9a5552022ea1eb5cd92df90659f7ebacb7cc
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84571654"
---
# <a name="how-xamarinmac-works"></a>Funcionamiento de Xamarin.Mac

En la mayoría de los casos, el desarrollador nunca tendrá que preocuparse de la "magia" interna de Xamarin. Mac, sin embargo, tener una comprensión aproximada de cómo funciona todo en el capó le ayudará a interpretar la documentación existente con un objetivo de C# y a depurar problemas cuando surjan.

En Xamarin. Mac, una aplicación une dos mundos: el tiempo de ejecución basado en Objective-C que contiene instancias de clases nativas ( `NSString` , `NSApplication` , etc.) y el tiempo de ejecución de C# contiene instancias de clases administradas ( `System.String` , `HttpClient` , etc.). Entre estos dos mundos, Xamarin. Mac crea un puente bidireccionales para que una aplicación pueda llamar a métodos (selectores) en Objective-C (como `NSApplication.Init` ) y Objective-c puede volver a llamar a los métodos de C# de la aplicación (como métodos en un delegado de la aplicación). En general, las llamadas a Objective-C se controlan de forma transparente a través de **P/Invoke** y parte del código en tiempo de ejecución que proporciona Xamarin.

<a name="exposing-classes"></a>

## <a name="exposing-c-classes--methods-to-objective-c"></a>Exponer clases o métodos de C# a Objective-C

Sin embargo, para que Objective-C vuelva a llamar a los objetos de C# de una aplicación, deben exponerse de tal forma que Objective-C pueda entender. Esto se hace a través de los `Register` `Export` atributos y. Considere el ejemplo siguiente:

```csharp
[Register ("MyClass")]
public class MyClass : NSObject
{
   [Export ("init")]
   public MyClass ()
   {
   }

   [Export ("run")]
   public void Run ()
   {
   }
}
```

En este ejemplo, el tiempo de ejecución de Objective-C conocerá ahora una clase denominada `MyClass` con selectores denominados `init` y `run` .

En la mayoría de los casos, se trata de un detalle de implementación que el desarrollador puede pasar por alto, ya que la mayoría de las devoluciones de llamada que recibe una aplicación serán a través de métodos invalidados en `base` clases (como `AppDelegate` , `Delegates` `DataSources` ) o en **acciones** pasadas a las API. En todos esos casos, los `Export` atributos no son necesarios en el código de C#.

## <a name="constructor-runthrough"></a>Constructor repaso

En muchos casos, el desarrollador tendrá que exponer la API de construcción de clases C# de la aplicación al tiempo de ejecución de Objective-C, por lo que se puede crear una instancia de lugares como cuando se llama en archivos Storyboard o XIB. Estos son los cinco constructores más comunes que se usan en las aplicaciones de Xamarin. Mac:

```csharp
// Called when created from unmanaged code
public CustomView (IntPtr handle) : base (handle)
{
   Initialize ();
}

// Called when created directly from a XIB file
[Export ("initWithCoder:")]
public CustomView (NSCoder coder) : base (coder)
{
   Initialize ();
}

// Called from C# to instance NSView with a Frame (initWithFrame)
public CustomView (CGRect frame) : base (frame)
{
}

// Called from C# to instance NSView without setting the frame (init)
public CustomView () : base ()
{
}

// This is a special case constructor that you call on a derived class when the derived called has an [Export] constructor.
// For example, if you call init on NSString then you don’t want to call init on NSObject.
public CustomView () : base (NSObjectFlag.Empty)
{
}
```

En general, el desarrollador debe dejar los `IntPtr` `NSCoder` constructores y que se generan al crear algunos tipos, como solo se pueden personalizar `NSViews` . Si Xamarin. Mac necesita llamar a uno de estos constructores en respuesta a una solicitud en tiempo de ejecución de Objective-C y lo ha quitado, la aplicación se bloqueará dentro del código nativo y puede ser difícil averiguar exactamente el problema.

## <a name="memory-management-and-cycles"></a>Administración de memoria y ciclos

La administración de memoria en Xamarin. Mac es muy similar a la de Xamarin. iOS. También es un tema complejo, uno más allá del ámbito de este documento. Lea las [prácticas recomendadas de memoria y rendimiento](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="ahead-of-time-compilation"></a>Compilación por encima de tiempo

Normalmente, las aplicaciones .NET no se compilan en el código máquina cuando se compilan, sino que se compilan en una capa intermedia denominada código IL que obtiene el código _Just-in-Time_ (JIT) compilado en el código máquina cuando se inicia la aplicación.

El tiempo que tarda el tiempo de ejecución de mono en compilar JIT este código de equipo puede ralentizar el inicio de una aplicación de Xamarin. Mac hasta un 20%, ya que se tarda en generarse el código de equipo necesario.

Debido a las limitaciones impuestas por Apple en iOS, la compilación JIT del código IL no está disponible para Xamarin. iOS. Como resultado, todas las aplicaciones de Xamarin. iOS se compilan _antes del tiempo_ (AOT) en el código del equipo durante el ciclo de compilación.

Una novedad de Xamarin. Mac es la posibilidad de que el código de IL se cree durante el ciclo de compilación de la aplicación, al igual que Xamarin. iOS. Xamarin. Mac usa un enfoque de AOT _híbrido_ que compila una mayoría del código de máquina necesario, pero permite que el tiempo de ejecución compile los trampolines necesarios y la flexibilidad para continuar admitiendo Reflection. Emit (y otros casos de uso que actualmente funcionan en Xamarin. Mac).

Hay dos áreas principales en las que AOT puede ayudar a una aplicación de Xamarin. Mac:

- **Mejores registros de bloqueos "nativos"** : Si una aplicación de Xamarin. Mac se bloquea en código nativo, lo que suele ocurrir cuando se realizan llamadas no válidas en las API de coco (como el envío de un `null` a un método que no la acepta), los registros de bloqueos nativos con fotogramas JIT son difíciles de analizar. Dado que los marcos JIT no tienen información de depuración, habrá varias líneas con desplazamientos hexadecimales y ninguna pista de lo que estaba ocurriendo. AOT genera marcos con nombre "reales" y los seguimientos son mucho más fáciles de leer. Esto también significa que la aplicación de Xamarin. Mac interactuará mejor con herramientas nativas como **lldb** e **Instruments**.
- **Mejor rendimiento de tiempo de inicio** : para aplicaciones grandes de Xamarin. Mac, con un tiempo de inicio de varios segundos, la compilación JIT de todo el código puede tardar bastante tiempo. AOT realiza esta tarea por adelantado.

### <a name="enabling-aot-compilation"></a>Habilitar la compilación de AOT

AOT está habilitado en Xamarin. Mac. para ello, haga doble clic en el **nombre del proyecto** en el **Explorador de soluciones**, vaya a la **compilación de Mac** y agregue `--aot:[options]` al campo **argumentos de MMP adicionales:** (donde `[options]` es una o varias opciones para controlar el tipo de AOT, consulte a continuación). Por ejemplo:

![Agregar AOT a argumentos MMP adicionales](how-it-works-images/aot01.png "Agregar AOT a argumentos MMP adicionales")

> [!IMPORTANT]
> La habilitación de la compilación de AOT aumenta drásticamente el tiempo de compilación, a veces hasta varios minutos, pero puede mejorar los tiempos de inicio de las aplicaciones en un promedio del 20%. Como resultado, la compilación de AOT solo se debe habilitar en las compilaciones de **versión** de una aplicación de Xamarin. Mac.

### <a name="aot-compilation-options"></a>Opciones de compilación de AOT

Hay varias opciones diferentes que se pueden ajustar al habilitar la compilación de AOT en una aplicación de Xamarin. Mac:

- `none`-Sin compilación de AOT. Esta es la configuración predeterminada.
- `all`-AOT compila todos los ensamblados de la monobundle.
- `core`-AOT compila los `Xamarin.Mac` `System` ensamblados, y `mscorlib` .
- `sdk`-AOT compila los `Xamarin.Mac` ensamblados de las bibliotecas de clases base (BCL) de y.
- `|hybrid`-Al agregarlo a una de las opciones anteriores, se habilita el AOT híbrido que permite la eliminación de IL, pero se producirán tiempos de compilación más largos.
- `+`: Incluye un solo archivo para la compilación de AOT.
- `-`: Quita un solo archivo de la compilación de AOT.

Por ejemplo, `--aot:all,-MyAssembly.dll` habilitaría la compilación de AOT en todos los ensamblados en el monobundle _excepto_ `MyAssembly.dll` y `--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll` habilitaría híbrido, el código de AOT incluirá `MyOtherAssembly.dll` y excluirá `mscorlib.dll` .

## <a name="partial-static-registrar"></a>Registrador estático parcial

Al desarrollar una aplicación de Xamarin. Mac, es importante minimizar el tiempo que se tarda entre completar un cambio y probarlo para cumplir las fechas límite de desarrollo. Las estrategias como la modularización de códigos base y las pruebas unitarias pueden ayudar a reducir los tiempos de compilación, ya que reducen el número de veces que una aplicación necesitará una recompilación completa costosa.

Además, y la novedad de Xamarin. Mac, el _registrador estático parcial_ (como pionera en Xamarin. iOS) puede reducir drásticamente las horas de inicio de una aplicación de Xamarin. Mac en la configuración de **depuración** . Entender cómo el uso del registrador estático parcial puede reducir una mejora de hasta cinco veces en el lanzamiento de depuración, que tardará un poco en lo que es el registrador, cuál es la diferencia entre el estático y el dinámico y lo que hace esta versión de "estática parcial".

### <a name="about-the-registrar"></a>Acerca del registrador

Bajo el capó de cualquier aplicación de Xamarin. Mac, se encuentra el marco de Cocoa de Apple y el tiempo de ejecución de Objective-C. La creación de un puente entre este "mundo nativo" y el "mundo administrado" de C# es la responsabilidad principal de Xamarin. Mac. Parte de esta tarea se controla mediante el registrador, que se ejecuta dentro del `NSApplication.Init ()` método. Esta es una de las razones por las que `NSApplication.Init` se debe llamar primero a cualquier uso de las API de cacao en Xamarin. Mac.

El trabajo del registrador es informar al tiempo de ejecución de Objective-C de la existencia de las clases de C# de la aplicación que derivan de clases como `NSApplicationDelegate` , `NSView` , `NSWindow` y `NSObject` . Esto requiere un examen de todos los tipos de la aplicación para determinar qué necesita registrarse y qué elementos de cada tipo se van a notificar.

Este análisis puede realizarse de **forma dinámica**, en el inicio de la aplicación con reflexión, o **estáticamente**, como un paso de tiempo de compilación. Al seleccionar un tipo de registro, el desarrollador debe tener en cuenta lo siguiente:

- El registro estático puede reducir drásticamente las horas de inicio, pero puede ralentizar considerablemente las compilaciones (normalmente más que el tiempo de compilación de depuración doble). Este será el valor predeterminado para las compilaciones de la configuración de **lanzamiento** .
- El registro dinámico retrasa este trabajo hasta que se inicia la aplicación y omite la generación de código, pero este trabajo adicional puede crear una pausa apreciable (al menos dos segundos) en el inicio de la aplicación. Esto es especialmente evidente en las compilaciones de configuración de depuración, que tiene como valor predeterminado el registro dinámico y cuya reflexión es más lenta.

El registro estático parcial, introducido por primera vez en Xamarin. iOS 8,13, proporciona al desarrollador lo mejor de ambas opciones. Al calcular previamente la información de registro de todos los elementos de `Xamarin.Mac.dll` y enviar esta información con Xamarin. Mac en una biblioteca estática (que solo debe estar vinculada en tiempo de compilación), Microsoft ha quitado la mayor parte del tiempo de reflexión del registrador dinámico mientras no afecta al tiempo de compilación.

### <a name="enabling-the-partial-static-registrar"></a>Habilitar el registrador estático parcial

El registrador estático parcial está habilitado en Xamarin. Mac. para ello, haga doble clic en el **nombre del proyecto** en el **Explorador de soluciones**, vaya a **Mac compilar** y agregue `--registrar:static` al campo **argumentos de MMP adicionales:** . Por ejemplo:

![Agregar el registrador estático parcial a argumentos MMP adicionales](how-it-works-images/psr01.png "Agregar el registrador estático parcial a argumentos MMP adicionales")

## <a name="additional-resources"></a>Recursos adicionales

Estas son algunas explicaciones más detalladas de cómo funcionan internamente:

- [Selectores de Objective-C](~/ios/internals/objective-c-selectors.md)
- [registrador](~/ios/internals/registrar.md)
- [Unified API de Xamarin para iOS y OS X](~/cross-platform/macios/unified/index.md)
- [Aspectos básicos de theading](~/ios/app-fundamentals/threading.md)
- [Delegados, protocolos y eventos](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Sobre`newrefcount`](~/ios/internals/newrefcount.md)
