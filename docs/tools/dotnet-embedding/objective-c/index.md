---
title: Compatibilidad con Objective-C
description: En este documento se proporciona una descripción de la compatibilidad con Objective-C en la inserción de .NET. Describe el recuento automático de referencias, NSString, protocolos, protocolo NSObject, excepciones, etc.
ms.prod: xamarin
ms.assetid: 3367A4A4-EC88-4B75-96D0-51B1FCBCE614
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: c172208b77728423617d17b3f4c5b5a516a0b932
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457359"
---
# <a name="objective-c-support"></a>Compatibilidad con Objective-C

## <a name="specific-features"></a>Características específicas

La generación de Objective-C tiene algunas características especiales que merece la pena tener en cuenta.

### <a name="automatic-reference-counting"></a>Recuento de referencias automático

El uso del recuento de referencias automático (ARC) es **necesario** para llamar a los enlaces generados. El proyecto que usa una biblioteca basada en la inserción de .NET se debe compilar con `-fobjc-arc` .

### <a name="nsstring-support"></a>Compatibilidad con NSString

Las API que exponen los `System.String` tipos se convierten en `NSString` . Esto facilita la administración de la memoria que al trabajar con `char*` .

### <a name="protocols-support"></a>Compatibilidad con protocolos

Las interfaces administradas se convierten en protocolos Objective-C donde todos los miembros son `@required` .

### <a name="nsobject-protocol-support"></a>Compatibilidad con el protocolo NSObject

De forma predeterminada, se supone que los valores hash y la igualdad predeterminados de .NET y el tiempo de ejecución de Objective-C son intercambiables, ya que comparten una semántica similar.

Cuando un tipo administrado invalida `Equals(Object)` o `GetHashCode` , normalmente significa que el comportamiento predeterminado (.net) no es suficiente; esto implica que probablemente no sea suficiente el comportamiento predeterminado de Objective-C.

En tales casos, el generador invalida el [`isEqual:`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418795-isequal?language=objc) método y la [`hash`](https://developer.apple.com/reference/objectivec/1418956-nsobject/1418859-hash?language=objc) propiedad definidos en el [ `NSObject` Protocolo](https://developer.apple.com/reference/objectivec/1418956-nsobject?language=objc). Esto permite usar la implementación administrada personalizada desde el código de Objective-C de forma transparente.

### <a name="exceptions-support"></a>Compatibilidad con excepciones

`--nativeexception`Al pasar como argumento a `objcgen` , se convertirán las excepciones administradas en excepciones de Objective-C que se pueden detectar y procesar. 

### <a name="comparison"></a>De comparación

Los tipos administrados que implementan `IComparable` (o su versión genérica) producirán `IComparable<T>` métodos descriptivos de Objective-C que devuelven `NSComparisonResult` y aceptan un `nil` argumento. Esto hace que la API generada sea más fácil para los desarrolladores de Objective-C. Por ejemplo:

```objc
- (NSComparisonResult)compare:(XAMComparableType * _Nullable)other;
```

### <a name="categories"></a>Categorías

Los métodos de extensiones administradas se convierten en categorías. Por ejemplo, los siguientes métodos de extensión en `Collection` :

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

En Objective-C, puede llamar a cualquiera de los prototipos de inicializador de cualquiera de las clases primarias de la cadena de herencia, a menos que se marque como no disponible ( `NS_UNAVAILABLE` ).

En C#, debe declarar explícitamente un miembro de constructor dentro de una clase, lo que significa que no se heredan los constructores.

Para exponer la representación correcta de la API de C# a Objective-C, `NS_UNAVAILABLE` se agrega a cualquier inicializador que no esté presente en la clase secundaria de la clase primaria.

API DE C#:

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

Aquí, se ha `initWithId:` marcado como no disponible.

### <a name="operator"></a>Operador

Objective-C no admite la sobrecarga de operadores como hace C#, por lo que los operadores se convierten en selectores de clase:

```csharp
public static AllOperators operator + (AllOperators c1, AllOperators c2)
{
    return new AllOperators (c1.Value + c2.Value);
}
```

en

```objc
+ (instancetype)add:(Overloads_AllOperators *)anObjectC1 c2:(Overloads_AllOperators *)anObjectC2;
```

Sin embargo, algunos lenguajes de .NET no admiten la sobrecarga de operadores, por lo que es habitual incluir también un método con nombre ["descriptivo"](/dotnet/standard/design-guidelines/operator-overloads) además de la sobrecarga del operador.

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

se convierte en:

```objc
+ (instancetype)add:(Overloads_AllOperatorsWithFriendly *)anObjectC1 c2:(Overloads_AllOperatorsWithFriendly *)anObjectC2;
```

### <a name="equality-operator"></a>Operador de igualdad

En general, `==` el operador de C# se trata como un operador general, tal y como se indicó anteriormente.

Sin embargo, si se encuentra el operador "descriptivo", se omitirán el operador y el operador `==` `!=` en la generación.

### <a name="datetime-vs-nsdate"></a>DateTime frente a NSDate

En la [`NSDate`](https://developer.apple.com/reference/foundation/nsdate?language=objc) documentación:

> `NSDate` los objetos encapsulan un único punto en el tiempo, independientemente de cualquier sistema calendrical determinado o zona horaria. Los objetos de fecha son inmutables, que representan un intervalo de tiempo invariable relativo a una fecha de referencia absoluta (00:00:00 UTC el 1 de enero de 2001).

Debido a la `NSDate` fecha de referencia, todas las conversiones entre ella y `DateTime` deben realizarse en UTC.

#### <a name="datetime-to-nsdate"></a>DateTime a NSDate

Al convertir de `DateTime` en `NSDate` , `Kind` `DateTime` se tiene en cuenta la propiedad en:

|Tipo|Results|
|---|---|
|`Utc`|La conversión se realiza utilizando el `DateTime` objeto proporcionado tal y como está.|
|`Local`|El resultado de llamar a `ToUniversalTime()` en el `DateTime` objeto proporcionado se usa para la conversión.|
|`Unspecified`|`DateTime`Se supone que el objeto proporcionado es una hora UTC, por lo que el mismo comportamiento `Kind` es `Utc` .|

La conversión utiliza la fórmula siguiente:

```
TimeInterval = DateTimeObjectTicks - NSDateReferenceDateTicks / TicksPerSecond
```

En esta fórmula: 

- `NSDateReferenceDateTicks` se calcula en función de la `NSDate` fecha de referencia de 00:00:00 UTC el 1 de enero de 2001: 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](/dotnet/api/system.timespan.tickspersecond) se define en [`TimeSpan`](/dotnet/api/system.timespan)

Para crear el `NSDate` objeto, `TimeInterval` se usa con el selector `NSDate` [dateWithTimeIntervalSinceReferenceDate:](https://developer.apple.com/reference/foundation/nsdate/1591577-datewithtimeintervalsincereferen?language=objc) .

#### <a name="nsdate-to-datetime"></a>NSDate a DateTime

La conversión de `NSDate` a `DateTime` usa la siguiente fórmula:

```
DateTimeTicks = NSDateTimeIntervalSinceReferenceDate * TicksPerSecond + NSDateReferenceDateTicks
```

En esta fórmula: 

- `NSDateReferenceDateTicks` se calcula en función de la `NSDate` fecha de referencia de 00:00:00 UTC el 1 de enero de 2001: 

    ```csharp
    new DateTime (year:2001, month:1, day:1, hour:0, minute:0, second:0, kind:DateTimeKind.Utc).Ticks;
    ```

- [`TicksPerSecond`](/dotnet/api/system.timespan.tickspersecond) se define en [`TimeSpan`](/dotnet/api/system.timespan)

Después de calcular `DateTimeTicks` , `DateTime` se invoca al [constructor](/dotnet/api/system.datetime.-ctor#System_DateTime__ctor_System_Int64_System_DateTimeKind_) , estableciendo su `kind` en `DateTimeKind.Utc` .

> [!NOTE]
> `NSDate` puede ser `nil` , pero a `DateTime` es un struct en .net que, por definición, no puede ser `null` . Si proporciona un `nil` `NSDate` , se traducirá al `DateTime` valor predeterminado, que se asigna a `DateTime.MinValue` .

`NSDate` admite un valor máximo mayor y un valor mínimo inferior a `DateTime` . Al convertir de `NSDate` en `DateTime` , estos valores superiores e inferiores se cambian a `DateTime` [MaxValue](/dotnet/api/system.datetime.maxvalue) o [MinValue](/dotnet/api/system.datetime.minvalue), respectivamente.