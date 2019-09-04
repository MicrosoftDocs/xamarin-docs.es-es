---
title: Optimizaciones de compilación
description: En este documento se explican las distintas optimizaciones que se aplican en tiempo de compilación para las aplicaciones de Xamarin. iOS y Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: conceptdev
ms.author: crdun
ms.date: 04/16/2018
ms.openlocfilehash: 4fdc40a8aed4b3137e418d6123fc000c2b36b6dd
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70226206"
---
# <a name="build-optimizations"></a>Optimizaciones de compilación

En este documento se explican las distintas optimizaciones que se aplican en tiempo de compilación para las aplicaciones de Xamarin. iOS y Xamarin. Mac.

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>Quite UIApplication. EnsureUIThread/NSApplication. EnsureUIThread

Quita las llamadas a [UIApplication. EnsureUIThread][1] (para Xamarin. iOS) `NSApplication.EnsureUIThread` o (para Xamarin. Mac).

Esta optimización cambiará el siguiente tipo de código:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

en lo siguiente:

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

Esta optimización requiere que el enlazador esté habilitado y solo se aplique a los métodos con `[BindingImpl (BindingImplOptions.Optimizable)]` el atributo.

De forma predeterminada, está habilitado para las compilaciones de versión.

El comportamiento predeterminado se puede invalidar pasando `--optimize=[+|-]remove-uithread-checks` a Mtouch/MMP.

[1]: https://docs.microsoft.com/dotnet/api/UIKit.UIApplication.EnsureUIThread

## <a name="inline-intptrsize"></a>IntPtr. Size insertado

Inserta el valor constante de `IntPtr.Size` según la plataforma de destino.

Esta optimización cambiará el siguiente tipo de código:

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

en el siguiente (al compilar para una plataforma de 64 bits):

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

Esta optimización requiere que el enlazador esté habilitado y solo se aplique a los métodos con `[BindingImpl (BindingImplOptions.Optimizable)]` el atributo.

De forma predeterminada, se habilita si el destino es una arquitectura única o para el ensamblado de plataforma (**Xamarin. iOS. dll**, **Xamarin. TVOS. dll**, **Xamarin. watchos. dll** o **Xamarin. Mac. dll**).

Si el destino es varias arquitecturas, esta optimización creará ensamblados distintos para la versión de 32 bits y la versión de 64 bits de la aplicación, y ambas versiones tendrán que incluirse en la aplicación, lo que aumentará el tamaño final de la aplicación en lugar de disminuir instalar.

El comportamiento predeterminado se puede invalidar pasando `--optimize=[+|-]inline-intptr-size` a Mtouch/MMP.

## <a name="inline-nsobjectisdirectbinding"></a>NSObject. IsDirectBinding en línea

`NSObject.IsDirectBinding`es una propiedad de instancia que determina si una instancia determinada es de un tipo contenedor o no (un tipo de contenedor es un tipo administrado que se asigna a un tipo nativo; por `UIKit.UIView` ejemplo, el tipo administrado `UIView` se asigna al tipo nativo: el contrario es un tipo de usuario. , en este caso `class MyUIView : UIKit.UIView` sería un tipo de usuario).

Es necesario conocer el valor de `IsDirectBinding` al llamar a Objective-C, ya que el valor determina la versión de `objc_msgSend` que se va a usar.

Solo se proporciona el código siguiente:

```csharp
class UIView : NSObject {
    public virtual string SomeProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class NSUrl : NSObject {
    public virtual string SomeOtherProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class MyUIView : UIView {
}
```

Podemos determinar que en `UIView.SomeProperty` el valor de `IsDirectBinding` no es una constante y no se puede insertar:

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

Sin embargo, es posible ver todos los tipos de la aplicación y determinar que no hay tipos que hereden de `NSUrl`y, por tanto, es seguro alinear el `IsDirectBinding` valor a una constante `true`:

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

En concreto, esta optimización cambiará el siguiente tipo de código (es decir, el código de `NSUrl.AbsoluteUrl`enlace para):

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

en el siguiente (cuando se puede determinar que no hay subclases de `NSUrl` en la aplicación):

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

Esta optimización requiere que el enlazador esté habilitado y solo se aplique a los métodos con `[BindingImpl (BindingImplOptions.Optimizable)]` el atributo.

Siempre está habilitado de forma predeterminada para Xamarin. iOS y siempre está deshabilitado de forma predeterminada para Xamarin. Mac (dado que es posible cargar ensamblados de forma dinámica en Xamarin. Mac, no es posible determinar que una clase determinada nunca se subclase).

El comportamiento predeterminado se puede invalidar pasando `--optimize=[+|-]inline-isdirectbinding` a Mtouch/MMP.

## <a name="inline-runtimearch"></a>Runtime en línea. Arch

Esta optimización cambiará el siguiente tipo de código:

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

en el siguiente (al compilar para el dispositivo):

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

Esta optimización requiere que el enlazador esté habilitado y solo se aplique a los métodos con `[BindingImpl (BindingImplOptions.Optimizable)]` el atributo.

Siempre está habilitado de forma predeterminada para Xamarin. iOS (no está disponible para Xamarin. Mac).

El comportamiento predeterminado se puede invalidar pasando `--optimize=[+|-]inline-runtime-arch` a Mtouch.

## <a name="dead-code-elimination"></a>Eliminación de código inactivo

Esta optimización cambiará el siguiente tipo de código:

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

dentro

```csharp
Console.WriteLine ("Doing this");
```

También evaluará las comparaciones constantes, de la siguiente forma:

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

y determinan que `8 == 8` la expresión es siempre true y la reduce a:

```csharp
Console.WriteLine ("Doing this");
```

Se trata de una optimización eficaz cuando se usa junto con las optimizaciones de inserción, ya que puede transformar el siguiente tipo de código (este es el código `NFCIso15693ReadMultipleBlocksConfiguration.Range`de enlace para):

```csharp
NSRange ret;
if (IsDirectBinding) {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret (out ret, this.Handle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    }
} else {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret (out ret, this.SuperHandle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    }
}
return ret;
```

en esto (al compilar para un dispositivo de 64 bits y cuando también se `NFCIso15693ReadMultipleBlocksConfiguration` puede garantizar que no hay subclases en la aplicación):

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

El compilador de AOT ya puede eliminar código inactivo como este, pero esta optimización se realiza dentro del enlazador, lo que significa que el enlazador puede ver que hay varios métodos que ya no se usan y que, por tanto, se pueden quitar (a menos que se usen en otro lugar). :

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

Esta optimización requiere que el enlazador esté habilitado y solo se aplique a los métodos con `[BindingImpl (BindingImplOptions.Optimizable)]` el atributo.

Siempre está habilitado de forma predeterminada (cuando el enlazador está habilitado).

El comportamiento predeterminado se puede invalidar pasando `--optimize=[+|-]dead-code-elimination` a Mtouch/MMP.

## <a name="optimize-calls-to-blockliteralsetupblock"></a>Optimizar llamadas a BlockLiteral. SetupBlock

El tiempo de ejecución de Xamarin. iOS/Mac debe conocer la firma del bloque al crear un bloque Objective-C para un delegado administrado. Puede tratarse de una operación bastante costosa. Esta optimización calculará la firma del bloque en el momento de la compilación y modificará el `SetupBlock` Il para llamar a un método que toma la firma como argumento en su lugar. Esto evita la necesidad de calcular la firma en tiempo de ejecución.

Las pruebas comparativas muestran que esto acelera la llamada de un bloque en un factor de 10 a 15.

Transformará el [código](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211)siguiente:

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

dentro

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

Esta optimización requiere que el enlazador esté habilitado y solo se aplique a los métodos con `[BindingImpl (BindingImplOptions.Optimizable)]` el atributo.

Está habilitada de forma predeterminada al usar el registrador estático (en Xamarin. iOS, el registrador estático está habilitado de forma predeterminada para las compilaciones de dispositivos y en Xamarin. Mac, el registrador estático está habilitado de forma predeterminada para las compilaciones de versión).

El comportamiento predeterminado se puede invalidar pasando `--optimize=[+|-]blockliteral-setupblock` a Mtouch/MMP.

## <a name="optimize-support-for-protocols"></a>Optimizar la compatibilidad con protocolos

El tiempo de ejecución de Xamarin. iOS/Mac necesita información sobre cómo los tipos administrados implementan los protocolos de Objective-C. Esta información se almacena en interfaces (y atributos en estas interfaces), que no es un formato muy eficaz, ni es compatible con el enlazador.

Un ejemplo es que estas interfaces almacenan información acerca de todos los miembros `[ProtocolMember]` del protocolo en un atributo, que entre otras cosas contienen referencias a los tipos de parámetro de esos miembros. Esto significa que, si se implementa una interfaz de este tipo, el vinculador conservará todos los tipos usados en esa interfaz, incluso para los miembros opcionales que la aplicación nunca llama o implementa.

Esta optimización hará que el registrador estático almacene la información necesaria en un formato eficaz que use poca memoria que sea fácil y rápida de encontrar en tiempo de ejecución.

También enseñará al vinculador que no es necesario conservar estas interfaces ni ninguno de los atributos relacionados.

Esta optimización requiere que el vinculador y el registrador estático estén habilitados.

En Xamarin. iOS, esta optimización está habilitada de forma predeterminada cuando el vinculador y el registrador estático están habilitados.

En Xamarin. Mac, esta optimización nunca está habilitada de forma predeterminada, ya que Xamarin. Mac admite la carga dinámica de ensamblados, y es posible que esos ensamblados no se hayan conocido en tiempo de compilación (y, por tanto, no estén optimizados).

El comportamiento predeterminado se puede invalidar pasando `--optimize=-register-protocols` a Mtouch/MMP.

## <a name="remove-the-dynamic-registrar"></a>Quitar el registrador dinámico

Tanto el entorno de tiempo de ejecución de Xamarin. iOS como el de Xamarin. Mac incluyen compatibilidad para [registrar tipos administrados](~/ios/internals/registrar.md) con el entorno de tiempo de ejecución de Objective-C. Puede realizarse en tiempo de compilación o en tiempo de ejecución (o parcialmente en tiempo de compilación y en el resto en el tiempo de ejecución), pero si se realiza por completo en tiempo de compilación, significa que se puede quitar el código auxiliar para hacerlo en tiempo de ejecución. Esto da como resultado una disminución significativa del tamaño de la aplicación, en particular para aplicaciones más pequeñas, como extensiones o aplicaciones de watchos.

Esta optimización requiere que el registrador estático y el vinculador estén habilitados.

El enlazador intentará determinar si es seguro quitar el registrador dinámico y, si es así, intentará quitarlo.

Dado que Xamarin. Mac admite la carga dinámica de ensamblados en tiempo de ejecución (que no se conocía en el momento de la compilación), es imposible determinar en tiempo de compilación si se trata de una optimización segura. Esto significa que esta optimización nunca está habilitada de forma predeterminada para las aplicaciones de Xamarin. Mac.

El comportamiento predeterminado se puede invalidar pasando `--optimize=[+|-]remove-dynamic-registrar` a Mtouch/MMP.

Si se invalida el valor predeterminado para quitar el registrador dinámico, el vinculador emitirá advertencias si detecta que no es seguro (pero el registrador dinámico se seguirá quitando).

## <a name="inline-runtimedynamicregistrationsupported"></a>Runtime en línea. DynamicRegistrationSupported

Inserta el valor de según se `Runtime.DynamicRegistrationSupported` determine en el momento de la compilación.

Si se quita el registrador dinámico (consulte [quitar la optimización del registrador dinámico](#remove-the-dynamic-registrar) ), se trata `false` de un valor constante, en caso `true` contrario, es un valor constante.

Esta optimización cambiará el siguiente tipo de código:

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

en el siguiente momento en que se quita el registrador dinámico:

```csharp
throw new Exception ("dynamic registration is not supported");
```

en el siguiente cuando no se quita el registrador dinámico:

```csharp
Console.WriteLine ("do something");
```

Esta optimización requiere que el enlazador esté habilitado y solo se aplique a los métodos con `[BindingImpl (BindingImplOptions.Optimizable)]` el atributo.

Siempre está habilitado de forma predeterminada (cuando el enlazador está habilitado).

El comportamiento predeterminado se puede invalidar pasando `--optimize=[+|-]inline-dynamic-registration-supported` a Mtouch/MMP.

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>Calcular previamente métodos para crear delegados administrados para bloques de Objective-C

Cuando Objective-C llama a un selector que toma un bloque como parámetro y, a continuación, el código administrado ha invalidado ese método, el tiempo de ejecución de Xamarin. iOS/Xamarin. Mac debe crear un delegado para dicho bloque.

El código de enlace generado por el generador de enlaces incluirá un `[BlockProxy]` atributo, que especifica el tipo con un `Create` método que puede hacer esto.

Dado el siguiente código de Objective-C:

```objc
@interface ObjCBlockTester : NSObject {
}
-(void) classCallback: (void (^)())completionHandler;
-(void) callClassCallback;
@end

@implementation ObjCBlockTester
-(void) classCallback: (void (^)())completionHandler
{
}

-(void) callClassCallback
{
    [self classCallback: ^()
    {
        NSLog (@"called!");
    }];
}
@end
```

y el siguiente código de enlace:

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

el generador generará:

```csharp
[Register("ObjCBlockTester", true)]
public unsafe partial class ObjCBlockTester : NSObject {
    // unrelated code...

    [Export ("callClassCallback")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public virtual void CallClassCallback ()
    {
        if (IsDirectBinding) {
            ApiDefinition.Messaging.void_objc_msgSend (this.Handle, Selector.GetHandle ("callClassCallback"));
        } else {
            ApiDefinition.Messaging.void_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("callClassCallback"));
        }
    }

    [Export ("classCallback:")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public unsafe virtual void ClassCallback ([BlockProxy (typeof (Trampolines.NIDActionArity1V0))] System.Action completionHandler)
    {
        // ...

    }
}

static class Trampolines
{
    [UnmanagedFunctionPointerAttribute (CallingConvention.Cdecl)]
    [UserDelegateType (typeof (System.Action))]
    internal delegate void DActionArity1V0 (IntPtr block);

    static internal class SDActionArity1V0 {
        static internal readonly DActionArity1V0 Handler = Invoke;

        [MonoPInvokeCallback (typeof (DActionArity1V0))]
        static unsafe void Invoke (IntPtr block) {
            var descriptor = (BlockLiteral *) block;
            var del = (System.Action) (descriptor->Target);
            if (del != null)
                del (obj);
        }
    }

    internal class NIDActionArity1V0 {
        IntPtr blockPtr;
        DActionArity1V0 invoker;

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe NIDActionArity1V0 (BlockLiteral *block)
        {
            blockPtr = _Block_copy ((IntPtr) block);
            invoker = block->GetDelegateForBlock<DActionArity1V0> ();
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        ~NIDActionArity1V0 ()
        {
            _Block_release (blockPtr);
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe static System.Action Create (IntPtr block)
        {
            if (block == IntPtr.Zero)
                return null;
            if (BlockLiteral.IsManagedBlock (block)) {
                var existing_delegate = ((BlockLiteral *) block)->Target as System.Action;
                if (existing_delegate != null)
                    return existing_delegate;
            }
            return new NIDActionArity1V0 ((BlockLiteral *) block).Invoke;
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        unsafe void Invoke ()
        {
            invoker (blockPtr);
        }
    }
}
```

Cuando se llama `[ObjCBlockTester callClassCallback]`a Objective-C, el tiempo de ejecución de Xamarin. iOS/Xamarin. `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` Mac examinará el atributo en el parámetro. A continuación, buscará el `Create` método en ese tipo y llamará a ese método para crear el delegado.

Esta optimización encontrará el `Create` método en tiempo de compilación y el registrador estático generará código que busca el método en tiempo de ejecución utilizando los tokens de metadatos en su lugar mediante el atributo y la reflexión (esto es mucho más rápido y también permite el enlazador). para quitar el código en tiempo de ejecución correspondiente, lo que reduce la aplicación).

Si MMP/Mtouch no encuentra el `Create` método, se mostrará una advertencia MT4174/MM4174 y la búsqueda se realizará en tiempo de ejecución en su lugar.
La causa más probable es el código de enlace escrito manualmente sin `[BlockProxy]` los atributos necesarios.

Esta optimización requiere que esté habilitado el registrador estático.

Siempre está habilitado de forma predeterminada (siempre que esté habilitado el registrador estático).

El comportamiento predeterminado se puede invalidar pasando `--optimize=[+|-]static-delegate-to-block-lookup` a Mtouch/MMP.
