---
title: Serialización de excepciones en Xamarin. iOS
description: En este documento se describe cómo trabajar con excepciones nativas y administradas en una aplicación de Xamarin. iOS. Describe los problemas que pueden producirse y una solución para estos problemas.
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/05/2017
ms.openlocfilehash: a5dea7358e48ebb1961c1fa3253ad096d041c0cf
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279693"
---
# <a name="exception-marshaling-in-xamarinios"></a>Serialización de excepciones en Xamarin. iOS

_Xamarin. iOS contiene nuevos eventos para ayudar a responder a las excepciones, especialmente en el código nativo._

Tanto el código administrado como Objective-C admiten excepciones en tiempo de ejecución (cláusulas Try/Catch/Finally).

Sin embargo, sus implementaciones son diferentes, lo que significa que las bibliotecas en tiempo de ejecución (el tiempo de ejecución mono y las bibliotecas en tiempo de ejecución de Objective-C) tienen problemas cuando tienen que controlar excepciones y, a continuación, ejecutar código escrito en otros lenguajes.

En este documento se explican los problemas que pueden producirse y las posibles soluciones.

También incluye un proyecto de ejemplo, [serialización de excepciones](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling), que se puede usar para probar distintos escenarios y sus soluciones.

## <a name="problem"></a>Problema

El problema se produce cuando se produce una excepción y durante el desenredo de la pila se encuentra un marco que no coincide con el tipo de excepción que se produjo.

Un ejemplo típico de esto para Xamarin. iOS o Xamarin. Mac es cuando una API nativa produce una excepción de Objective-C y, a continuación, esa excepción de Objective-C debe controlarse de algún modo cuando el proceso de desenredado de la pila alcance un marco administrado.

La acción predeterminada es no hacer nada. En el ejemplo anterior, esto significa permitir que los marcos administrados de desenredado de tiempo de ejecución de Objective-C. Esto es problemático, porque el tiempo de ejecución de Objective-C no sabe cómo desenredar Marcos administrados; por ejemplo, no ejecutará `catch` ninguna `finally` cláusula OR en ese marco.

### <a name="broken-code"></a>Código roto

Considere el ejemplo de código siguiente:

``` csharp
var dict = new NSMutableDictionary ();
dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

Esto producirá un NSInvalidArgumentException de Objective-C en código nativo:

```
NSInvalidArgumentException *** setObjectForKey: key cannot be nil
```

Y el seguimiento de la pila será similar al siguiente:

```
0   CoreFoundation          __exceptionPreprocess + 194
1   libobjc.A.dylib         objc_exception_throw + 52
2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
3   libobjc.A.dylib         objc_msgSend + 102
4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()
```

Los fotogramas 0-3 son marcos nativos y el desenredo de pila en el tiempo de ejecución de Objective-C _puede_ desenredar esos fotogramas. En concreto, ejecutará cualquier cláusula Objective-C `@catch` o `@finally` .

Sin embargo, el desenredo de la pila de Objective-C _no_ es capaz de desenredar correctamente los fotogramas administrados (fotogramas 4-6), en el que los fotogramas se desenrollarán, pero la lógica de excepción administrada no se ejecutará.

Lo que significa que no suele ser posible detectar estas excepciones de la siguiente manera:

```csharp
try {
    var dict = new NSMutableDictionary ();
    dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero);
} catch (Exception ex) {
    Console.WriteLine (ex);
} finally {
    Console.WriteLine ("finally");
}
```

Esto se debe a que el desenredador de la pila de Objective-C no `catch` conoce la cláusula administrada y `finally` no se ejecutará la cláusula.

Cuando el ejemplo de código anterior _es_ efectivo, se debe a que Objective-c tiene un método para notificar las excepciones no controladas de Objective- [`NSSetUncaughtExceptionHandler`][2]c,, que usa Xamarin. iOS y Xamarin. Mac, y en ese momento intenta convertir cualquier excepción de Objective-c. a excepciones administradas.

## <a name="scenarios"></a>Escenarios

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>Escenario 1: detección de excepciones de Objective-C con un controlador Catch administrado

En el siguiente escenario, es posible detectar excepciones de Objective-C mediante controladores administrados `catch` :

1. Se produce una excepción de Objective-C.
2. El tiempo de ejecución de Objective-C recorre la pila (pero no la desenreda), buscando `@catch` un controlador nativo que pueda controlar la excepción.
3. El tiempo de ejecución de Objective-C `@catch` no encuentra ningún controlador `NSGetUncaughtExceptionHandler`, llama a e invoca el controlador instalado por Xamarin. iOS/Xamarin. Mac.
4. El controlador de Xamarin. iOS/Xamarin. Mac convertirá la excepción de Objective-C en una excepción administrada y la producirá. Dado que el tiempo de ejecución de Objective-C no desenredó la pila (solo la requería), el marco actual es el mismo en el que se produjo la excepción de Objective-C.

Aquí se produce otro problema, porque el tiempo de ejecución de mono no sabe cómo desenredar correctamente las tramas de Objective-C.

Cuando se llama a la devolución de llamada de excepción de Objective-C no detectada de Xamarin. iOS, la pila es similar a la siguiente:

```
 0 libxamarin-debug.dylib   exception_handler(exc=name: "NSInvalidArgumentException" - reason: "*** setObjectForKey: key cannot be nil")
 1 CoreFoundation           __handleUncaughtException + 809
 2 libobjc.A.dylib          _objc_terminate() + 100
 3 libc++abi.dylib          std::__terminate(void (*)()) + 14
 4 libc++abi.dylib          __cxa_throw + 122
 5 libobjc.A.dylib          objc_exception_throw + 337
 6 CoreFoundation           -[__NSDictionaryM setObject:forKey:] + 1015
 7 libxamarin-debug.dylib   xamarin_dyn_objc_msgSend + 102
 8 TestApp                  ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
 9 TestApp                  Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr) [0x00000]
10 TestApp                  ExceptionMarshaling.Exceptions.ThrowObjectiveCException () [0x00013]
```

Aquí, los únicos Marcos administrados son los fotogramas 8-10, pero la excepción administrada se inicia en el fotograma 0. Esto significa que el tiempo de ejecución de mono debe desenredar los marcos nativos 0-7, lo que provoca un problema equivalente al problema descrito anteriormente: aunque el tiempo de ejecución de mono desenredará los marcos nativos, `@catch` no `@finally` ejecutará ninguna cláusula Objective-C o .

Ejemplo de código:

```objc
-(id) setObject: (id) object forKey: (id) key
{
    @try {
        if (key == nil)
            [NSException raise: @"NSInvalidArgumentException"];
    } @finally {
        NSLog (@"This will not be executed");
    }
}
```

Y la `@finally` cláusula no se ejecutarán porque el tiempo de ejecución de mono que desenreda este fotograma no conoce.

Una variación de esto es iniciar una excepción administrada en código administrado y, después, desenredar a través de marcos nativos para llegar a `catch` la primera cláusula administrada:

```csharp
class AppDelegate : UIApplicationDelegate {
    public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
    {
        throw new Exception ("An exception");
    }
    static void Main (string [] args)
    {
        try {
            UIApplication.Main (args, null, typeof (AppDelegate));
        } catch (Exception ex) {
            Console.WriteLine ("Managed exception caught.");
        }
    }
}
```

El método `UIApplication:Main` administrado llamará al método `UIApplicationMain` nativo y, a continuación, iOS realizará gran parte de la ejecución del código nativo antes `AppDelegate:FinishedLaunching` de llamar al método administrado, con todavía muchos marcos nativos en la pila cuando la excepción administrada es produci

```
 0: TestApp                 ExceptionMarshaling.IOS.AppDelegate:FinishedLaunching (UIKit.UIApplication,Foundation.NSDictionary)
 1: TestApp                 (wrapper runtime-invoke) <Module>:runtime_invoke_bool__this___object_object (object,intptr,intptr,intptr) 
 2: libmonosgen-2.0.dylib   mono_jit_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
 3: libmonosgen-2.0.dylib   do_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
 4: libmonosgen-2.0.dylib   mono_runtime_invoke [inlined] mono_runtime_invoke_checked(method=<unavailable>, obj=<unavailable>, params=<unavailable>, error=0xbff45758)
 5: libmonosgen-2.0.dylib   mono_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>)
 6: libxamarin-debug.dylib  xamarin_invoke_trampoline(type=<unavailable>, self=<unavailable>, sel="application:didFinishLaunchingWithOptions:", iterator=<unavailable>), context=<unavailable>)
 7: libxamarin-debug.dylib  xamarin_arch_trampoline(state=0xbff45ad4)
 8: libxamarin-debug.dylib  xamarin_i386_common_trampoline
 9: UIKit                   -[UIApplication _handleDelegateCallbacksWithOptions:isSuspended:restoreState:]
10: UIKit                   -[UIApplication _callInitializationDelegatesForMainScene:transitionContext:]
11: UIKit                   -[UIApplication _runWithMainScene:transitionContext:completion:]
12: UIKit                   __84-[UIApplication _handleApplicationActivationWithScene:transitionContext:completion:]_block_invoke.3124
13: UIKit                   -[UIApplication workspaceDidEndTransaction:]
14: FrontBoardServices      __37-[FBSWorkspace clientEndTransaction:]_block_invoke_2
15: FrontBoardServices      __40-[FBSWorkspace _performDelegateCallOut:]_block_invoke
16: FrontBoardServices      __FBSSERIALQUEUE_IS_CALLING_OUT_TO_A_BLOCK__
17: FrontBoardServices      -[FBSSerialQueue _performNext]
18: FrontBoardServices      -[FBSSerialQueue _performNextFromRunLoopSource]
19: FrontBoardServices      FBSSerialQueueRunLoopSourceHandler
20: CoreFoundation          __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__
21: CoreFoundation          __CFRunLoopDoSources0
22: CoreFoundation          __CFRunLoopRun
23: CoreFoundation          CFRunLoopRunSpecific
24: CoreFoundation          CFRunLoopRunInMode
25: UIKit                   -[UIApplication _run]
26: UIKit                   UIApplicationMain
27: TestApp                 (wrapper managed-to-native) UIKit.UIApplication:UIApplicationMain (int,string[],intptr,intptr)
28: TestApp                 UIKit.UIApplication:Main (string[],intptr,intptr)
29: TestApp                 UIKit.UIApplication:Main (string[],string,string)
30: TestApp                 ExceptionMarshaling.IOS.Application:Main (string[])
```

Los marcos 0-1 y 27-30 se administran, mientras que todos los que hay entre ellos son nativos. Si mono se desenreda a través de estos fotogramas, no `@catch` se `@finally` ejecutará ninguna cláusula Objective-C o.

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>Escenario 2: no se pueden detectar excepciones de Objective-C

En el siguiente escenario, _no_ es posible detectar excepciones de Objective-c mediante controladores administrados `catch` porque la excepción de Objective-c se controló de otra manera:

1. Se produce una excepción de Objective-C.
2. El tiempo de ejecución de Objective-C recorre la pila (pero no la desenreda), buscando `@catch` un controlador nativo que pueda controlar la excepción.
3. El tiempo de ejecución de Objective- `@catch` C busca un controlador, lo desenreda y comienza a ejecutar el `@catch` controlador.

Este escenario suele encontrarse en aplicaciones de Xamarin. iOS, porque en el subproceso principal suele haber código similar al siguiente:

``` objective-c
void UIApplicationMain ()
{
    @try {
        while (true) {
            ExecuteRunLoop ();
        }
    } @catch (NSException *ex) {
        NSLog (@"An unhandled exception occured: %@", exc);
        abort ();
    }
}

```

Esto significa que, en el subproceso principal, nunca hay una excepción no controlada de Objective-C y, por tanto, nunca se llama a nuestra devolución de llamada que convierte excepciones de Objective-C en excepciones administradas.

Esto también es bastante común cuando se depuran aplicaciones de Xamarin. Mac en una versión de macOS anterior a la que admite Xamarin. Mac, ya que la inspección de la mayoría de los objetos de interfaz de usuario en el depurador intentará capturar propiedades que se correspondan con los selectores que no existen en la plataforma que se está ejecutando ( como Xamarin. Mac incluye compatibilidad con una versión de macOS superior). Al llamar a estos selectores `NSInvalidArgumentException` se producirá un ("selector no reconocido enviado a..."), lo que hace que el proceso se bloquee.

En Resumen, tener los marcos de tiempo de ejecución de Objective-C o de desenredado en tiempo de ejecución de mono que no están programados para controlar pueden provocar comportamientos indefinidos, como bloqueos, fugas de memoria y otros tipos de comportamientos imprevisibles (mis).

## <a name="solution"></a>Solución

En Xamarin. iOS 10 y Xamarin. Mac 2,10, se ha agregado compatibilidad para la detección de excepciones administradas y de Objective-C en cualquier límite nativo administrado y para convertir esa excepción al otro tipo.

En pseudo-Code, tiene un aspecto similar al siguiente:

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

Se intercepta P/Invoke a objc_msgSend y se llama en su lugar:

``` objective-c
void
xamarin_dyn_objc_msgSend (id obj, SEL sel)
{
    @try {
        objc_msgSend (obj, sel);
    } @catch (NSException *ex) {
        convert_to_and_throw_managed_exception (ex);
    }
}
```

Y se hace algo similar para el caso inverso (serialización de excepciones administradas en excepciones de Objective-C).

La detección de excepciones en el límite nativo administrado no es gratuita, por lo que no siempre está habilitada de forma predeterminada:

- Xamarin. iOS/tvOS: la interceptación de excepciones de Objective-C está habilitada en el simulador.
- Xamarin. watchos: la intercepción se aplica en todos los casos, ya que, al permitir que los marcos administrados de desenredado de Objective-C en tiempo de ejecución, se confunda el recolector de elementos no utilizados y se bloquee o se bloquee.
- Xamarin. Mac: la interceptación de excepciones de Objective-C está habilitada para las compilaciones de depuración.

La sección [de marcas de tiempo de compilación](#build_time_flags) explica cómo habilitar la interceptación cuando no está habilitada de forma predeterminada.

## <a name="events"></a>Events

Hay dos eventos nuevos que se generan una vez interceptada una excepción: `Runtime.MarshalManagedException` y. `Runtime.MarshalObjectiveCException`

A ambos eventos se les `EventArgs` pasa un objeto que contiene la excepción original que se produjo `Exception` (la propiedad) y `ExceptionMode` una propiedad para definir cómo se deben calcular las referencias de la excepción.

La `ExceptionMode` propiedad se puede cambiar en el controlador de eventos para cambiar el comportamiento de acuerdo con cualquier procesamiento personalizado realizado en el controlador. Un ejemplo sería anular el proceso si se produce una excepción determinada.

El cambio `ExceptionMode` de la propiedad se aplica a un solo evento, no afecta a ninguna excepción interceptada en el futuro.

Están disponibles los siguientes modos:

- `Default`: El valor predeterminado varía según la plataforma. Es `ThrowObjectiveCException` si el GC está en modo cooperativo (watchos) y `UnwindNativeCode` de lo contrario (iOS/watchos/MacOS). El valor predeterminado puede cambiar en el futuro.
- `UnwindNativeCode`: Este es el comportamiento anterior (sin definir). Esto no está disponible cuando se usa el GC en el modo cooperativo (que es la única opción en watchos; por lo tanto, no es una opción válida en watchos), pero es la opción predeterminada para todas las demás plataformas.
- `ThrowObjectiveCException`: Convierta la excepción administrada en una excepción de Objective-C e inicie la excepción de Objective-C. Este es el valor predeterminado en watchos.
- `Abort`: Anular el proceso.
- `Disable`: Deshabilita la interceptación de excepciones, por lo que no tiene sentido establecer este valor en el controlador de eventos, pero una vez que se produce el evento, es demasiado tarde para deshabilitarlo. En cualquier caso, si se establece, se comportará como `UnwindNativeCode`.

Para calcular las referencias de las excepciones de Objective-C al código administrado, están disponibles los siguientes modos:

- `Default`: El valor predeterminado varía según la plataforma. Es `ThrowManagedException` si el GC está en modo cooperativo (watchos) y `UnwindManagedCode` de lo contrario (iOS/tvOS/MacOS). El valor predeterminado puede cambiar en el futuro.
- `UnwindManagedCode`: Este es el comportamiento anterior (sin definir). Esto no está disponible cuando se usa el GC en modo cooperativo (que es el único modo GC válido en watchos; por lo tanto, no es una opción válida en watchos), pero es el valor predeterminado para todas las demás plataformas.
- `ThrowManagedException`: Convierta la excepción de Objective-C en una excepción administrada y genere la excepción administrada. Este es el valor predeterminado en watchos.
- `Abort`: Anular el proceso.
- `Disable`:D ISABLES la interceptación de excepciones, por lo que no tiene sentido establecer este valor en el controlador de eventos, pero una vez que se genera el evento, es demasiado tarde para deshabilitarlo. En cualquier caso, si se establece, anulará el proceso.

Por lo tanto, para ver cada vez que se calculan las referencias de una excepción, puede hacer lo siguiente:

``` csharp
Runtime.MarshalManagedException += (object sender, MarshalManagedExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling managed exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
    
};
Runtime.MarshalObjectiveCException += (object sender, MarshalObjectiveCExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling Objective-C exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
};
```

<a name="build_time_flags" />

## <a name="build-time-flags"></a>Marcas de tiempo de compilación

Es posible pasar las siguientes opciones a **Mtouch** (para aplicaciones de Xamarin. iOS) y **MMP** (para las aplicaciones de Xamarin. Mac), que determinará si está habilitada la interceptación de excepciones y establecerá la acción predeterminada que debe producirse:

- `--marshal-managed-exceptions=`
  - `default`
  - `unwindnativecode`
  - `throwobjectivecexception`
  - `abort`
  - `disable`

- `--marshal-objectivec-exceptions=`
  - `default`
  - `unwindmanagedcode`
  - `throwmanagedexception`
  - `abort`
  - `disable`

A excepción `disable`de, estos valores son idénticos `ExceptionMode` a los valores que se pasan `MarshalManagedException` a `MarshalObjectiveCException` los eventos y.

La `disable` opción deshabilitará _principalmente_ la intercepción, salvo que seguiremos interceptando excepciones cuando no agregue ninguna sobrecarga de ejecución. Los eventos de serialización se siguen generando para estas excepciones, siendo el modo predeterminado el modo predeterminado para la plataforma en ejecución.

## <a name="limitations"></a>Limitaciones

Solo intercepto P/Invoke en la `objc_msgSend` familia de funciones al intentar detectar excepciones de Objective-C. Esto significa que una P/Invoke a otra función de C, que después produce cualquier excepción de Objective-C, seguirá ejecutándose en el comportamiento antiguo y no definido (esto puede mejorarse en el futuro).

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc


## <a name="related-links"></a>Vínculos relacionados

- [Serialización de excepciones (ejemplo)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
