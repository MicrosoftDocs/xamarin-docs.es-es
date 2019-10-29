---
title: Compatibilidad con Objective-C
description: En este documento se proporciona una descripción de la compatibilidad con Objective-C en la inserción de .NET. Describe el recuento automático de referencias, NSString, protocolos, protocolo NSObject, excepciones, etc.
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: e72f950dc6fcf12e70714e0fbb996ad5ea432548
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029704"
---
# <a name="objective-c-support"></a>Compatibilidad con Objective-C

## <a name="specific-features"></a>Características específicas

La generación de Objective-C tiene algunas características especiales que merece la pena tener en cuenta.

### <a name="automatic-reference-counting"></a>Recuento de referencias automático

El uso del recuento de referencias automático (ARC) es **necesario** para llamar a los enlaces generados. El proyecto que usa una biblioteca basada en la inserción de .NET se debe compilar con `-fobjc-arc`.

### <a name="nsstring-support"></a>Compatibilidad con NSString

Las API que exponen `System.String` tipos se convierten en `NSString`. Esto facilita la administración de la memoria que cuando se trabaja con `char*`.

### <a name="protocols-support"></a>Compatibilidad con protocolos

Las interfaces administradas se convierten en protocolos de Objective-C donde todos los miembros se `@required`.

### <a name="nsobject-protocol-support"></a>Compatibilidad con el protocolo NSObject

De forma predeterminada, se supone que los valores hash y la igualdad predeterminados de .NET y el tiempo de ejecución de Objective-C son intercambiables, ya que comparten una semántica similar.

Cuando un tipo administrado invalida `Equals(Object)` o `GetHashCode`, normalmente significa que el comportamiento predeterminado (.NET) no era suficiente. Esto implica que es probable que el comportamiento predeterminado de Objective-C no sea suficiente.

En tales casos, el generador invalida el método [`isEqual:`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) y [`hash`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) propiedad definida en el [Protocolo`NSObject`](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). Esto permite usar la implementación administrada personalizada desde el código de Objective-C de forma transparente.

### <a name="exceptions-support"></a>Compatibilidad con excepciones

Al pasar `--nativeexception` como argumento a `objcgen`, se convertirán las excepciones administradas en excepciones de Objective-C que se pueden detectar y procesar. 

### <a name="comparison"></a>Comparación

Los tipos administrados que implementan `IComparable` (o su versión genérica `IComparable<T>`) producirán métodos descriptivos de Objective-C que devuelvan un `NSComparisonResult` y acepten un argumento de `nil`. Esto hace que la API generada sea más fácil para los desarrolladores de Objective-C. Por ejemplo:

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categorías

Los métodos de extensiones administradas se convierten en categorías. Por ejemplo, los siguientes métodos de extensión en `Collection`:

```csharp
public static class SomeExtensions {
    public static int CountNonNull (this Collection collection) { ... }
    public static int CountNull (this Collection collection) { ... }
}
```

crearía una categoría de Objective-C como esta:

```objc
@interface Collection (SomeExtensions)

- (int)countNonNull;
- (int)countNull;

@end
```

Cuando un tipo administrado único extiende varios tipos, se generan varias categorías de Objective-C.

### <a name="subscripting"></a>Subíndices

Las propiedades indizadas administradas se convierten en subíndices de objetos. Por ejemplo:

```csharp
public bool this[int index] {
    get { return c[index]; }
    set { c[index] = value; }
}
```

crearía Objective-C similar a:

```objc
- (id)objectAtIndexedSubscript:(int)idx;
- (void)setObject:(id)obj atIndexedSubscript:(int)idx;
```

que se puede usar a través de la sintaxis de subíndices de Objective-C:

```objc
if ([intCollection [0] isEqual:@42])
    intCollection[0] = @13;
```

En función del tipo de indexador, se generará el subíndice indexado o con clave cuando sea necesario.

Este [artículo](https://nshipster.com/object-subscripting/) es una excelente introducción a la suscripción.

## <a name="main-differences-with-net"></a>Principales diferencias con .NET

### <a name="constructors-vs-initializers"></a>Constructores e inicializadores

En Objective-C, puede llamar a cualquiera de los prototipos de inicializador de cualquiera de las clases primarias de la cadena de herencia, a menos que se marque como no disponible (`NS_UNAVAILABLE`).

En C# , debe declarar explícitamente un miembro de constructor dentro de una clase, lo que significa que no se heredan los constructores.

Para exponer la representación correcta de la C# API a Objective-C,`NS_UNAVAILABLE`se agrega a cualquier inicializador que no esté presente en la clase secundaria de la clase primaria.

C#API

```csharp
public class Unique {
    public Unique () : this (1)
    {
    }

    public Unique (int id)
    {
    }
}

public class SuperUnique : Unique {
    public SuperUnique () : base (911)
    {
    }
}
```

API superficial de Objective-C:

```objc
@interface SuperUnique : Unique

- (instancetype)initWithId:(int)id NS_UNAVAILABLE;
- (instancetype)init;

@end
```

Aquí, `initWithId:` se ha marcado como no disponible.

### <a name="operator"></a>"??"

Objective-C no admite la sobrecarga C# de operadores, por lo que los operadores se convierten en selectores de clase:

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

por

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

Sin embargo, algunos lenguajes de .NET no admiten la sobrecarga de operadores, por lo que es habitual incluir también un método con nombre ["descriptivo"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) además de la sobrecarga del operador.

Si se encuentran la versión del operador y la versión "descriptiva", solo se generará la versión descriptiva, ya que se generarán en el mismo nombre de Objective-C.

```csharp
public static AllOperatorsWithFriendly operator + (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}

public static AllOperatorsWithFriendly Add (AllOperatorsWithFriendly c1, AllOperatorsWithFriendly c2)
{
    return new AllOperatorsWithFriendly (c1.Value + c2.Value);
}
```

empieza

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Operador de igualdad

En general, el operador C# `==` de se controla como un operador general, como se indicó anteriormente.

Sin embargo, si se encuentra el operador "descriptivo", los operadores `==` y `!=` de operador se omitirán en la generación.

### <a name="datetime-vs-nsdate"></a>DateTime frente a NSDate

En la documentación [`NSDate`](https://developer.apple.com/reference/foundation/nsdate?language=objc) :

> `NSDate` objetos encapsulan un único punto en el tiempo, independientemente de cualquier sistema calendrical determinado o zona horaria. Los objetos de fecha son inmutables, que representan un intervalo de tiempo invariable relativo a una fecha de referencia absoluta (00:00:00 UTC el 1 de enero de 2001).

Debido a `NSDate` fecha de referencia, todas las conversiones entre ella y `DateTime` deben realizarse en UTC.

#### <a name="datetime-to-nsdate"></a>DateTime a NSDate

Al convertir de `DateTime` a `NSDate`, se tiene en cuenta la propiedad `Kind` de `DateTime`:

|Kind|Resultados|
|---|---|
|`Utc`|La conversión se realiza utilizando el objeto de `DateTime` proporcionado tal y como está.|
|`Local`|El resultado de llamar a `ToUniversalTime()` en el objeto `DateTime` proporcionado se usa para la conversión.|
|`Unspecified`|Se supone que el objeto de `DateTime` proporcionado es una hora UTC, por lo que se trata del mismo comportamiento cuando se `Utc``Kind`.|

La conversión utiliza la fórmula siguiente:

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

En esta fórmula: 

- `NSDateReferenceDateTicks` se calcula en función de la fecha de referencia `NSDate` de 00:00:00 UTC el 1 de enero de 2001: 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) se define en [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Para crear el objeto de `NSDate`, el `TimeInterval` se utiliza con el `NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) selector.

#### <a name="nsdate-to-datetime"></a>NSDate a DateTime

La conversión de `NSDate` a `DateTime` utiliza la fórmula siguiente:

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

En esta fórmula: 

- `NSDateReferenceDateTicks` se calcula en función de la fecha de referencia `NSDate` de 00:00:00 UTC el 1 de enero de 2001: 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](https://docs.microsoft.com/dotnet/api/system.timespan.tickspersecond) se define en [`TimeSpan`](https://docs.microsoft.com/dotnet/api/system.timespan)

Después de calcular `DateTimeTicks`, se invoca el [constructor](https://docs.microsoft.com/dotnet/api/system.datetime.-ctor?#System_DateTime__ctor_System_Int64_System_DateTimeKind_) de `DateTime`, estableciendo su `kind` en `DateTimeKind.Utc`.

> [!NOTE]
> `NSDate` puede ser `nil`, pero un `DateTime` es un struct en .NET que, por definición, no se puede `null`. Si asigna un `nil` `NSDate`, se traducirá al valor predeterminado de `DateTime`, que se asigna a `DateTime.MinValue`.

`NSDate` admite un valor máximo mayor y un valor mínimo inferior a `DateTime`. Al convertir de `NSDate` a `DateTime`, estos valores superiores e inferiores se cambian a la `DateTime` [MaxValue](https://docs.microsoft.com/dotnet/api/system.datetime.maxvalue) o [MinValue](https://docs.microsoft.com/dotnet/api/system.datetime.minvalue), respectivamente.
