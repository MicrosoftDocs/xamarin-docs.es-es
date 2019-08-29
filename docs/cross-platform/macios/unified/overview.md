---
title: Información general de Unified API
description: La Unified API de Xamarin permite compartir código entre Mac e iOS y admite aplicaciones de 32 y 64 bits con el mismo binario.
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 99347ccc2490361bedda926b25076158a12e39bf
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065170"
---
# <a name="unified-api-overview"></a>Información general de Unified API

La Unified API de Xamarin permite compartir código entre Mac e iOS y admite aplicaciones de 32 y 64 bits con el mismo binario. El Unified API se utiliza de forma predeterminada en los nuevos proyectos de Xamarin. iOS y Xamarin. Mac.

> [!IMPORTANT]
> La Classic API de Xamarin, que precedía a la Unified API, ha quedado en desuso. 
> - La última versión de Xamarin. iOS para admitir el Classic API (MonoTouch. dll) era Xamarin. iOS 9,10.
> - Xamarin. Mac sigue admitiendo el Classic API, pero ya no se actualiza. Puesto que está en desuso, los desarrolladores deben trasladar sus aplicaciones a la Unified API.

## <a name="updating-classic-api-based-apps"></a>Actualización de aplicaciones basadas en Classic API

Siga las instrucciones pertinentes para su plataforma:

- [Actualización de aplicaciones existentes](updating-apps.md)
- [Actualización de aplicaciones iOS existentes](updating-ios-apps.md)
- [Actualización de aplicaciones Mac existentes](updating-mac-apps.md)
- [Actualización de aplicaciones Xamarin.Forms existentes](updating-xamarin-forms-apps.md)
- [Migración de un enlace a Unified API](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[Sugerencias para actualizar el código a Unified API](updating-tips.md)

Independientemente de las aplicaciones que migre, consulte [estas sugerencias](updating-tips.md) para ayudarle a actualizar correctamente a la Unified API.

## <a name="library-split"></a>División de biblioteca

A partir de este punto, nuestras API aparecerán de dos maneras:

- **Classic API:** Limitado a 32 bits (solo) y expuesto en los `monotouch.dll` ensamblados y. `XamMac.dll`
- **Unified API:** Admite el desarrollo de 32 y 64 bits con una única API disponible en `Xamarin.iOS.dll` los `Xamarin.Mac.dll` ensamblados y.

Esto significa que para la empresa a los desarrolladores (no orientados a la App Store), puede seguir usando las clásicas APIs  existentes, tal y como las estaremos manteniendo siempre, o también pueden actualizar a las nuevas API.

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Cambios de espacio de nombres

Para reducir la fricción para compartir código entre nuestros productos iOS y Mac, estamos cambiando los espacios de nombres de las API en los productos.

Estamos quitando el prefijo "MonoTouch" de nuestro producto de iOS y "MonoMac" de nuestro producto Mac en los tipos de datos.

Esto hace más fácil compartir código entre las plataformas iOS y Mac sin tener que recurrir a la compilación condicional y esto también reducirá el ruido en la parte superior de los archivos de código fuente.

- **Classic API:** Los espacios de `MonoTouch.` nombres `MonoMac.` usan o prefijo.
- **Unified API:** Sin prefijo de espacio de nombres

## <a name="runtime-defaults"></a>Valores predeterminados en tiempo de ejecución

La API unificada usa de manera predeterminada el recolector **SGen**  de elementos no utilizados y la [Nuevo sistema de Conteo de referencias](~/ios/internals/newrefcount.md) de seguimiento de la propiedad del objeto. Esta misma característica se ha migrado a Xamarin. Mac.

Esto soluciona número de problemas que los desarrolladores enfrentan el sistema antiguo y también facilita la [administración de memoria](~/cross-platform/deploy-test/memory-perf-best-practices.md).

Tenga en cuenta que es posible habilitar el nuevo Refcount incluso para el Classic API, pero el valor predeterminado es conservador y no requiere que los usuarios realicen cambios. Con el Unified API, se ha tomado la oportunidad de cambiar el valor predeterminado y ofrecer a los desarrolladores todas las mejoras a la vez que refactorizan y vuelven a probar su código.

## <a name="api-changes"></a>Cambios de la API

En el Unified API se eliminan los métodos en desuso y hay algunos casos en los que se produjeron errores tipográficos en los nombres de API cuando estaban enlazados a los espacios de nombres de MonoMac y MonoTouch originales en las API clásicas. Estas instancias se han corregido en las nuevas API unificadas y deberán actualizarse en las aplicaciones de componente, iOS y Mac. Esta es una lista de las más comunes en las que puede encontrarse:

|Nombre del método Classic API|Nombre del método Unified API|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

Para obtener una lista completa de cambios cuando se cambia desde el clásico a la API unificada, visite nuestra documentación [Diferencias en API Clasica(monotouch.dll) VS API unificada(Xamarin.iOS.dll)](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md).

## <a name="updating-to-unified"></a>Actualización a Unified

Varias API/dividir/obsoleta en **clásico** no están disponibles en la API **unificada**. Puede ser más fácil de corregir la advertencias `CS0616` antes de iniciar la actualización(manuales o automatizadas), puesto que tendrá el atributo mensaje `[Obsolete]` (parte de la advertencia) que le guiarán a la API de la derecha.

Tenga en cuenta que estamos publicando una [*diferencia*](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) de los cambios clásicos de la API unificada de vs que se pueden usar antes o después de las actualizaciones del proyecto. La corrección de las llamadas obsoletas en clásico suele ser un ahorro de tiempo (menos búsquedas de documentación).

Siga estas instrucciones para [actualizar aplicaciones iOS existentes](~/cross-platform/macios/unified/updating-ios-apps.md)o [aplicaciones Mac](~/cross-platform/macios/unified/updating-mac-apps.md) a la Unified API.
Revise el resto de esta página y [estas sugerencias](~/cross-platform/macios/unified/updating-tips.md) para obtener más información sobre cómo migrar el código.

### <a name="nuget"></a>NuGet

Los paquetes NuGet que anteriormente admitía Xamarin. iOS a través de la Classic API publicaron sus ensamblados mediante el moniker de plataforma **Monotouch10** .

El Unified API introduce un nuevo identificador de plataforma para los paquetes compatibles: **Xamarin. iOS10**. Los paquetes de NuGet existentes deberán actualizarse para agregar compatibilidad con esta plataforma, compilando en el Unified API.

> [!IMPORTANT]
> Si tiene un error con el formato _"el error 3 no puede incluir ' MonoTouch. dll ' y ' Xamarin. iOS. dll ' en el mismo proyecto de Xamarin. iOS, se hace referencia explícitamente a ' Xamarin. iOS. dll ', mientras que ' XXX, version = 0.0.000, Culture = neutral, PublicKeyToken = null ' "_ después de convertir la aplicación en las API unificadas, normalmente se debe a que hay un componente o un paquete NuGet en el proyecto que no se ha actualizado al Unified API. Deberá quitar el componente o NuGet existente, actualizar a una versión que admita las API unificadas y realizar una compilación limpia.

### <a name="the-road-to-64-bits"></a>El camino a 64 bits

Para obtener información sobre la compatibilidad con 32 y 64 bits aplicaciones y obtener información sobre marcos, consulte el [Consideraciones de la plataforma para 32 y 64 bits](~/cross-platform/macios/32-and-64/index.md).

 <a name="new-data-types" />

#### <a name="new-data-types"></a>Nuevos tipos de datos

En el núcleo de la diferencia, Mac y la API de iOS usan un tipo de datos específicos de la arquitectura que siempre es de 32 bits en plataformas de 32 bits y 64 bits en plataformas de 64 bits.

Por ejemplo,  Objective-c. asigna el tipo de dato `NSInteger` para `int32_t` en sistemas de 32 bits y a `int64_t` en sistemas de 64 bits.

Para que coincida con este comportamiento, en nuestro Unified API vamos a reemplazar los usos anteriores `int` de (que en .net se definen como `System.Int32`siempre) a un nuevo tipo de datos `System.nint`:.  Puede pensar en "n" como "Native", por lo que el tipo de entero nativo de la plataforma.

Estamos introduciendo `nint` `nuint` y`nfloat` proporcionando también tipos de datos creados sobre ellos cuando sea necesario.

Para obtener más información sobre estos cambios de tipo de datos, vea la documentación de [tipos nativos](~/cross-platform/macios/nativetypes.md) documento.

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>Cómo detectar la arquitectura de las aplicaciones de iOS

Podría haber situaciones en las que la aplicación necesita saber si se está ejecutando en un sistema iOS de 32 bits o de 64 bits. El siguiente código se puede usar para comprobar la arquitectura:

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>Matrices y System. Collections. Generic

Debido a que los indizadores de C# esperan un tipo de `int`, tendrá que convertir explícitamente `nint` valores `int` para tener acceso a los elementos de una colección o matriz. Por ejemplo:

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

Este es el comportamiento esperado, ya que la `int` conversión `nint` de a es una pérdida en 64 bits, no se realiza una conversión implícita.

### <a name="converting-datetime-to-nsdate"></a>Convertir DateTime en NSDate

Cuando se usan las API unificadas, ya no `DateTime` se `NSDate` realiza la conversión implícita de los valores de a. Estos valores se deben convertir explícitamente de un tipo a otro. Los siguientes métodos de extensión se pueden usar para automatizar este proceso:

```csharp
public static DateTime NSDateToDateTime(this NSDate date)
{
    // NSDate has a wider range than DateTime, so clip
    // the converted date to DateTime.Min|MaxValue.
    double secs = date.SecondsSinceReferenceDate;
    if (secs < -63113904000)
        return DateTime.MinValue;
    if (secs > 252423993599)
        return DateTime.MaxValue;
    return (DateTime) date;
}

public static NSDate DateTimeToNSDate(this DateTime date)
{
    if (date.Kind == DateTimeKind.Unspecified)
        date = DateTime.SpecifyKind (date, /* DateTimeKind.Local or DateTimeKind.Utc, this depends on each app */)
    return (NSDate) date;
}

```

<a name="deprecated-typos" />

### <a name="deprecated-apis-and-typos"></a>API y errores tipográficos en desuso

Dentro del API clásico de Xamarin.iOS  (monotouch.dll) el atributo `[Obsolete]` se utiliza de dos maneras diferentes:

- **API de iOS desusada:** Esto se debe a que Apple le sugiere que deje de usar una API porque se ha sustituido por otra más reciente. El Classic API sigue siendo preciso y a menudo es necesario (si admite la versión anterior de iOS).
 Dicha API (y el `[Obsolete]` atributo) se incluyen en los nuevos ensamblados de Xamarin. iOS.
- **API incorrecta** Algunas API tenían errores tipográficos en sus nombres.

En el caso de los ensamblados originales (MonoTouch. dll y XamMac. dll), mantuvimos el código anterior disponible por compatibilidad, pero se han quitado de los ensamblados de Unified API (Xamarin. iOS. dll y Xamarin. Mac).

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>Subclases de NSObject. ctor (IntPtr)

Cada `NSObject` subclase tiene un constructor que acepta un `IntPtr`. Así es como se pueden crear instancias de una nueva instancia administrada a partir de un identificador de ObjC nativo.

En el API clásico `public` era una  constructor. Sin embargo, era fácil utilizar el uso incorrecto de esta característica en el código de usuario, por ejemplo, la creación de varias instancias administradas para una sola instancia de ObjC *o* la creación de una instancia administrada que no tuviera el estado administrado esperado (para las subclases).

Para evitar esa clase de problemas los constructores `IntPtr`  ahora son `protected` en la API **unificada** , se usa únicamente para crear subclases de una. Esto garantizará que la API correcta o segura se usa para crear una instancia administrada a partir de identificadores, es decir,

```csharp
var label = Runtime.GetNSObject<UILabel> (handle);
```

Esta API devolverá una instancia administrada existente (si ya existe) o creará una nueva (si es necesario). Ya está disponible en la API clásica y la unificada.

Tenga en cuenta `.ctor(NSObjectFlag)` que ahora también `protected` es, pero este no se usaba rara vez fuera de la subclase.

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction reemplazado por acción

Con las API unificadas `NSAction` , se ha quitado en favor de .net `Action`estándar. Esta es una gran mejora porque `Action` es un tipo .net común, mientras `NSAction` que era específico de Xamarin. iOS. Ambos hacen exactamente lo mismo, pero eran tipos distintos e incompatibles y dieron lugar a que se tuviera que escribir más código para lograr el mismo resultado.

Por ejemplo, si la aplicación de Xamarin existente incluye el código siguiente:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```

Ahora se puede reemplazar por una expresión lambda simple:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

Anteriormente, esto sería un error del compilador porque `Action` no se puede asignar a `UITapGestureRecognizer` `NSAction`, pero como `Action` ahora toma un en `NSAction` lugar de un que es válido en las API unificadas.

### <a name="custom-delegates-replaced-with-actiont"></a>Delegados personalizados reemplazados por la acción\<T >

En **unificado** , algunos delegados de .net sencillos (por ejemplo `Action<T>`, un parámetro) se reemplazaron por. P. ej.,

```csharp
public delegate void NSNotificationHandler (NSNotification notification);
```

ahora se puede usar como `Action<NSNotification>`. Esto promueve la reutilización de código y reduce la duplicación de código dentro de Xamarin. iOS y sus propias aplicaciones.

### <a name="taskbool-replaced-with-taskbooleannserror"></a>Task\<bool > reemplazar por Task < Boolean, NSError > >

En el modelo **clásico** , algunas API asincrónicas devuelven `Task<bool>`. Sin embargo, algunos `NSError` de ellos son los `bool` que se usan cuando una formaba parte de la firma, es `true` decir, que ya era y `NSError`tenía que detectar una excepción para obtener.

Dado que algunos errores son muy comunes y el valor devuelto no era útil, este patrón se cambió en Unified `Task<Tuple<Boolean,NSError>>`para devolver. Esto le permite comprobar el éxito y cualquier error que se haya producido durante la llamada asincrónica.

### <a name="nsstring-vs-string"></a>NSString frente a cadena

En algunos casos, algunas constantes tuvieron que cambiarse de `string` a `NSString`, por ejemplo,`UITableViewCell`

**Clásico**

```csharp
public virtual string ReuseIdentifier { get; }
```

**Unificada**

```csharp
public virtual NSString ReuseIdentifier { get; }
```

En general, se prefiere el `System.String` tipo .net. Sin embargo, a pesar de las directrices de Apple, algunas API nativas comparan punteros constantes (no la propia cadena) y esto solo puede funcionar cuando se `NSString`exponen las constantes como.

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Protocolos de Objective-C

El MonoTouch original no tenía compatibilidad total con los protocolos ObjC y algunas API no óptimas se agregaron para admitir el escenario más común. Esta limitación ya no existe, pero, para mantener la compatibilidad con versiones anteriores, se mantienen `monotouch.dll` varias `XamMac.dll`API dentro de y.

Estas limitaciones se han quitado y limpiado en las API unificadas. La mayoría de los cambios tendrán el siguiente aspecto:

**Clásico**

```csharp
public virtual AVAssetResourceLoaderDelegate Delegate { get; }
```

**Unificada**

```csharp
public virtual IAVAssetResourceLoaderDelegate Delegate { get; }
```

El `I` prefijo significa que **Unified** expone una interfaz, en lugar de un tipo específico, para el protocolo ObjC. Esto facilitará los casos en los que no desea subclases del tipo específico que Xamarin. iOS proporcionó.

También permitió que algunas API sean más precisas y fáciles de usar, por ejemplo:

**Clásico**

```csharp
public virtual void SelectionDidChange (NSObject uiTextInput);
```

**Unificada**

```csharp
public virtual void SelectionDidChange (IUITextInput uiTextInput);
```

Estas API son ahora más fáciles de usar, sin hacer referencia a la documentación, y la finalización del código del IDE le proporcionará sugerencias más útiles basadas en el protocolo o la interfaz.

#### <a name="nscoding-protocol"></a>Protocolo NSCoding

Nuestro enlace original incluía un. ctor (NSCoder) para cada tipo, aunque no era compatible con el `NSCoding` protocolo.  Un único `Encode(NSCoder)` método estaba presente `NSObject` en para codificar el objeto.
Pero este método solo funcionaría si la instancia se ajusta al protocolo NSCoding.

En el Unified API hemos corregido este problema.  Los nuevos ensamblados solo tendrán `.ctor(NSCoder)` si el tipo se ajusta a. `NSCoding` Además, estos tipos ahora tienen `Encode(NSCoder)` un método que se ajusta a la `INSCoding` interfaz.

Impacto bajo: En la mayoría de los casos, este cambio no afectará a las aplicaciones, ya que no se pueden usar los constructores antiguos, quitados.

## <a name="further-tips"></a>Más sugerencias

Los cambios adicionales que se deben tener en cuenta se enumeran en las [sugerencias para actualizar aplicaciones al Unified API](~/cross-platform/macios/unified/updating-tips.md).

## <a name="sample-code"></a>Código de ejemplo

A partir del 31 de julio, hemos publicado puertos de los ejemplos de iOS en esta nueva API `magic-types` en la rama en MonoTouch [-Samples](https://github.com/xamarin/monotouch-samples/commits/magic-types).

Para Mac, estamos comprobando ejemplos en el repositorio de [ejemplos de Mac](https://github.com/xamarin/mac-samples) (que muestra las nuevas API en Mavericks/Yosemite), así como ejemplos de 32/64 bits en la rama de tipos mágicos [Mac-samples](https://github.com/xamarin/monotouch-samples/commits/magic-types).

## <a name="related-links"></a>Vínculos relacionados

- [Actualización de aplicaciones de iOS](updating-ios-apps.md)
- [Actualización de aplicaciones Mac](updating-mac-apps.md)
- [Actualización de aplicaciones de Xamarin. Forms](updating-xamarin-forms-apps.md)
- [Actualizar enlaces](update-binding.md)
- [Actualizar sugerencias](updating-tips.md)
- [Diferencias de Unified API clásicas](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
- [Trabajo con tipos nativos en aplicaciones multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
