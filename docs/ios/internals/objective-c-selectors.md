---
title: Selectores de Objective-C en Xamarin. iOS
description: En este documento se explica cómo interactuar con los selectores de Objective C#-C desde. Describe cómo invocar los selectores y consideraciones técnicas que se deben tener en cuenta al hacerlo.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/12/2017
ms.openlocfilehash: 17b845345175d80237bcfdb171461f2c763c364e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291850"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Selectores de Objective-C en Xamarin. iOS

El lenguaje Objective-C se basa en los *selectores*. Un selector es un mensaje que se puede enviar a un objeto o una *clase*. [Xamarin. iOS](~/ios/internals/api-design/index.md) asigna selectores de instancia a métodos de instancia y selectores de clase a métodos estáticos.

A diferencia de las funciones normales de C C++ (y como las funciones miembro), no se puede invocar directamente un selector mediante [P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/) en su lugar, los selectores se envían a una instancia o clase de Objective-C mediante el[`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
funcionalidad.

Para obtener más información sobre los mensajes de Objective-C, eche un vistazo a la guía [trabajar con objetos](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2) de Apple.

## <a name="example"></a>Ejemplo

Supongamos que desea invocar la[`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
selector en [`NSString`](https://developer.apple.com/documentation/foundation/nsstring).
La declaración (de la documentación de Apple) es:

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

Esta API tiene las siguientes características:

- El tipo de valor `CGSize` devuelto es para el Unified API.
- El `font` parámetro es un [UIFont](xref:UIKit.UIFont) (y un tipo (indirectamente) derivado de [NSObject](xref:Foundation.NSObject)y se asigna a [System. IntPtr](xref:System.IntPtr).
- El `width` parámetro `nfloat`, se asigna a. `CGFloat`
- El `lineBreakMode` parámetro,, [`UILineBreakMode`](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc)ya se ha enlazado a Xamarin. iOS como el[`UILineBreakMode`](xref:UIKit.UILineBreakMode)
enumeración.

En conjunto, la `objc_msgSend` declaración debe coincidir con:

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

Si el valor devuelto era una estructura de menos de 8 bytes de tamaño (como el anterior `SizeF` usado antes de cambiar a las API unificadas), el código anterior se habría ejecutado en el simulador pero se bloqueó en el dispositivo. Para llamar a un selector que devuelve un valor de menos de 8 bits de tamaño, `objc_msgSend_stret` declare la función:

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
3. Llame `objc_msgSend` a con los argumentos adecuados.

### <a name="selector-targets"></a>Destinos de selector

Un destino de selector es una instancia de objeto o una clase de Objective-C. Si el destino es una instancia de y procede de un tipo de Xamarin. iOS enlazado [`ObjCRuntime.INativeObject.Handle`](xref:ObjCRuntime.INativeObject.Handle) , use la propiedad.

Si el destino es una clase, use [`ObjCRuntime.Class`](xref:ObjCRuntime.Class) para obtener una referencia a la instancia de la clase y, [`Class.Handle`](xref:ObjCRuntime.Class.Handle) a continuación, use la propiedad.

### <a name="selector-names"></a>Nombres de selector

Los nombres de selector se enumeran en la documentación de Apple. Por ejemplo, [`NSString`](https://developer.apple.com/documentation/foundation/nsstring?language=objc) incluye [`sizeWithFont:`](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc) y [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc) selectores. Los dos puntos incrustados y finales forman parte del nombre del selector y no se pueden omitir.

Una vez que tenga un nombre de selector, puede crear [`ObjCRuntime.Selector`](xref:ObjCRuntime.Selector) una instancia para él.

### <a name="calling-objc_msgsend"></a>Llamando a objc_msgSend

`objc_msgSend`envía un mensaje (selector) a un objeto. Esta familia de funciones toma al menos dos argumentos necesarios: el destino del selector (un identificador de instancia o clase), el propio selector y los argumentos necesarios para el selector. Los argumentos de la instancia y del `System.IntPtr`selector deben ser, y todos los argumentos restantes deben coincidir con el tipo que el `nint` selector espera `int`, por ejemplo `System.IntPtr` , para `NSObject`o para todos los tipos derivados de. Use el[`NSObject.Handle`](xref:Foundation.NSObject.Handle)
propiedad para obtener un `IntPtr` para una instancia de tipo de Objective-C.

Hay más de una `objc_msgSend` función:

- Se [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc) usa para los selectores que devuelven un struct. En ARM, esto incluye todos los tipos de valor devueltos que no son una enumeración ni ninguno de los`char`tipos integrados de C `float`( `double`, `short`, `int`, `long`,,). En x86 (el simulador), este método se debe usar para todas las estructuras de más de 8 bytes de`CGSize` tamaño (es 8 bytes y `objc_msgSend_stret` no se usa en el simulador). 
- Se [`objc_msgSend_fpret`](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc) usa para los selectores que devuelven un valor de punto flotante solo en x86. No es necesario usar esta función en ARM; en su lugar, `objc_msgSend`use. 
- La función [objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend) principal se usa para todos los demás selectores.

Una vez que haya decidido `objc_msgSend` qué funciones necesita llamar (el simulador y el dispositivo pueden requerir un método diferente), puede usar un método normal [`[DllImport]`](xref:System.Runtime.InteropServices.DllImportAttribute) para declarar la función para la invocación posterior.

Puede encontrar un conjunto de declaraciones `objc_msgSend` realizadas previamente en. `ObjCRuntime.Messaging`

## <a name="different-invocations-on-simulator-and-device"></a>Invocaciones diferentes en el simulador y el dispositivo

Tal y como se ha descrito anteriormente, Objective-C `objc_msgSend` tiene tres tipos de métodos: uno para las invocaciones regulares, uno para las invocaciones que devuelven valores de punto flotante (solo x86) y otro para las invocaciones que devuelven valores de estructura. El último incluye el sufijo `_stret` en `ObjCRuntime.Messaging`.

Si va a invocar un método que devolverá determinados Structs (reglas que se describen a continuación), debe invocar el método con el valor devuelto como `out` primer parámetro como un valor:

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

La regla para el uso del `_stret_` método difiere en x86 y ARM.
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

Al compilar para ARM, use el[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
para cualquier tipo de valor que no sea una enumeración ni ninguno de los tipos base de una`int`enumeración `short`( `long`, `double` `byte`, `float`,,,).

Al compilar para x86, use[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
para cualquier tipo de valor que no sea una enumeración ni ninguno de los tipos base de una`int`enumeración `short`( `long`, `double` `byte`, `float`,,,) y cuyo tamaño nativo sea superior a 8 bytes.

### <a name="creating-your-own-signatures"></a>Crear sus propias firmas

La siguiente [idea](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) se puede usar para crear sus propias firmas, si es necesario.

## <a name="related-links"></a>Vínculos relacionados

- Ejemplo [de selectores de Objective-C](https://developer.xamarin.com/samples/mac-ios/Objective-C/)
