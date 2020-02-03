---
title: Selectores de Objective-C en Xamarin. iOS
description: En este documento se explica cómo interactuar con los selectores de Objective C#-C desde. Describe cómo invocar los selectores y consideraciones técnicas que se deben tener en cuenta al hacerlo.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/12/2017
ms.openlocfilehash: 2a4d255500f68497fe7cb0cc439c5f9c0504b0f2
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725190"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Selectores de Objective-C en Xamarin. iOS

El lenguaje Objective-C se basa en los *selectores*. Un selector es un mensaje que se puede enviar a un objeto o una *clase*. [Xamarin. iOS](~/ios/internals/api-design/index.md) asigna selectores de instancia a métodos de instancia y selectores de clase a métodos estáticos.

A diferencia de las funciones normales de C C++ (y como las funciones miembro), no se puede invocar directamente un selector mediante [P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/) en su lugar, los selectores se envían a una instancia o clase de Objective-C mediante el [`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
.

Para obtener más información sobre los mensajes de Objective-C, eche un vistazo a la guía [trabajar con objetos](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2) de Apple.

## <a name="example"></a>Ejemplo

Supongamos que desea invocar el [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
selector en [`NSString`](https://developer.apple.com/documentation/foundation/nsstring).
La declaración (de la documentación de Apple) es:

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

Esta API tiene las siguientes características:

- El tipo de valor devuelto es `CGSize` para el Unified API.
- El parámetro `font` es un [UIFont](xref:UIKit.UIFont) (y un tipo (indirectamente) derivado de [NSObject](xref:Foundation.NSObject)y se asigna a [System. IntPtr](xref:System.IntPtr).
- El parámetro `width`, un `CGFloat`, se asigna a `nfloat`.
- El parámetro `lineBreakMode`, un [`UILineBreakMode`](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc), ya se ha enlazado en Xamarin. iOS como [`UILineBreakMode`](xref:UIKit.UILineBreakMode)
enumeración.

En conjunto, la declaración de `objc_msgSend` debe coincidir:

```csharp
CGSize objc_msgSend(
    IntPtr target,
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Declárela como se indica a continuación:

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target,
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Para llamar a este método, utilice código como el siguiente:

```csharp
NSString target = ...
Selector selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont font = ...
nfloat width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle,
    selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode
);
```

Si el valor devuelto era una estructura de menos de 8 bytes de tamaño (como el `SizeF` anterior usado antes de cambiar a las API unificadas), el código anterior se habría ejecutado en el simulador pero se bloqueó en el dispositivo. Para llamar a un selector que devuelve un valor de menos de 8 bits de tamaño, declare la función `objc_msgSend_stret`:

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target,
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Para llamar a este método, utilice código como el siguiente:

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle,
        selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode
    );
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode
    );
```

## <a name="invoking-a-selector"></a>Invocar un selector

La invocación de un selector consta de tres pasos:

1. Obtiene el destino del selector.
2. Obtiene el nombre del selector.
3. Llame a `objc_msgSend` con los argumentos adecuados.

### <a name="selector-targets"></a>Destinos de selector

Un destino de selector es una instancia de objeto o una clase de Objective-C. Si el destino es una instancia de y procede de un tipo de Xamarin. iOS enlazado, use la propiedad [`ObjCRuntime.INativeObject.Handle`](xref:ObjCRuntime.INativeObject.Handle) .

Si el destino es una clase, use [`ObjCRuntime.Class`](xref:ObjCRuntime.Class) para obtener una referencia a la instancia de la clase y, a continuación, use la propiedad [`Class.Handle`](xref:ObjCRuntime.Class.Handle) .

### <a name="selector-names"></a>Nombres de selector

Los nombres de selector se enumeran en la documentación de Apple. Por ejemplo, [`NSString`](https://developer.apple.com/documentation/foundation/nsstring?language=objc) incluye selectores de [`sizeWithFont:`](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc) y [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc) . Los dos puntos incrustados y finales forman parte del nombre del selector y no se pueden omitir.

Una vez que tenga un nombre de selector, puede crear una instancia de [`ObjCRuntime.Selector`](xref:ObjCRuntime.Selector) para él.

### <a name="calling-objc_msgsend"></a>Llamar a objc_msgSend

`objc_msgSend` envía un mensaje (selector) a un objeto. Esta familia de funciones toma al menos dos argumentos necesarios: el destino del selector (un identificador de instancia o clase), el propio selector y los argumentos necesarios para el selector. Los argumentos de instancia y selector deben ser `System.IntPtr`, y todos los demás argumentos deben coincidir con el tipo que el selector espera, por ejemplo, un `nint` para un `int`o un `System.IntPtr` para todos los tipos derivados de `NSObject`. Usar el [`NSObject.Handle`](xref:Foundation.NSObject.Handle)
propiedad para obtener un `IntPtr` para una instancia de tipo de Objective-C.

Hay más de una `objc_msgSend` función:

- Use [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc) para los selectores que devuelven un struct. En ARM, incluye todos los tipos de valor devueltos que no son una enumeración ni ninguno de los tipos integrados de C (`char`, `short`, `int`, `long`, `float`, `double`). En x86 (el simulador), este método se debe usar para todas las estructuras de más de 8 bytes de tamaño (`CGSize` es de 8 bytes y no usa `objc_msgSend_stret` en el simulador).
- Use [`objc_msgSend_fpret`](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc) para los selectores que devuelven un valor de punto flotante solo en x86. No es necesario usar esta función en ARM; en su lugar, use `objc_msgSend`.
- La función principal [objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend) se utiliza para todos los demás selectores.

Una vez que haya decidido qué `objc_msgSend` funciones debe llamar (el simulador y el dispositivo pueden requerir un método diferente), puede usar un método de [`[DllImport]`](xref:System.Runtime.InteropServices.DllImportAttribute) normal para declarar la función para la invocación posterior.

Puede encontrar un conjunto de declaraciones de `objc_msgSend` realizadas previamente en `ObjCRuntime.Messaging`.

## <a name="different-invocations-on-simulator-and-device"></a>Invocaciones diferentes en el simulador y el dispositivo

Tal y como se ha descrito anteriormente, Objective-C tiene tres tipos de métodos de `objc_msgSend`: uno para las invocaciones regulares, uno para las invocaciones que devuelven valores de punto flotante (solo x86) y otro para las invocaciones que devuelven valores de estructura. Este último incluye el sufijo `_stret` en `ObjCRuntime.Messaging`.

Si va a invocar un método que devolverá determinados Structs (reglas que se describen a continuación), debe invocar el método con el valor devuelto como primer parámetro como un valor `out`:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

La regla sobre Cuándo usar el método `_stret_` difiere en x86 y ARM.
Si desea que los enlaces funcionen tanto en el simulador como en el dispositivo, agregue código como el siguiente:

```csharp
if (Runtime.Arch == Arch.DEVICE)
{
    PointF ret;
    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);
    return ret;
}
else
{
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
}
```

### <a name="using-the-objc_msgsend_stret-method"></a>Usar el método objc_msgSend_stret

Al compilar para ARM, use el [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
para cualquier tipo de valor que no sea una enumeración ni ninguno de los tipos base de una enumeración (`int`, `byte`, `short`, `long`, `double`, `float`).

Al compilar para x86, use [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
para cualquier tipo de valor que no sea una enumeración ni ninguno de los tipos base de una enumeración (`int`, `byte`, `short`, `long`, `double`, `float`) y cuyo tamaño nativo sea mayor que 8 bytes.

### <a name="creating-your-own-signatures"></a>Crear sus propias firmas

La siguiente [idea](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) se puede usar para crear sus propias firmas, si es necesario.
