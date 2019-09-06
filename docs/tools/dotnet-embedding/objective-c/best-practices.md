---
title: Procedimientos recomendados para la inserción de .NET para Objective-C
description: En este documento se describen varias prácticas recomendadas para usar la inserción de .NET con Objective-C. Describe la exposición de un subconjunto del código administrado, la exposición de una API de chunkier, la nomenclatura y mucho más.
ms.prod: xamarin
ms.assetid: 63C7F5D2-8933-4D4A-8348-E9CBDA45C472
author: conceptdev
ms.author: crdun
ms.date: 11/14/2017
ms.openlocfilehash: ff04c001193eb897aac81cdc66ed535c76d81717
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70285114"
---
# <a name="net-embedding-best-practices-for-objective-c"></a>Procedimientos recomendados para la inserción de .NET para Objective-C

Se trata de un borrador y es posible que no esté sincronizado con las características admitidas actualmente por la herramienta. Esperamos que este documento evolucione por separado y que finalmente coincida con la herramienta final, es decir, se le sugerirán los mejores enfoques a largo plazo, no soluciones inmediatas.

Una gran parte de este documento también se aplica a otros idiomas admitidos. Sin embargo, todos los ejemplos C# proporcionados se encuentran en y Objective-C.

## <a name="exposing-a-subset-of-the-managed-code"></a>Exponer un subconjunto del código administrado

El marco de trabajo o biblioteca nativa generada contiene código de Objective-C para llamar a cada una de las API administradas que se exponen. Cuanto más API se surfacee (hacer pública), mayor será la biblioteca nativa de _glue_ .

Podría ser una buena idea crear un ensamblado más pequeño para exponer solo las API necesarias al desarrollador nativo. Esa fachada también le permitirá tener más control sobre la visibilidad, la nomenclatura y la comprobación de errores... del código generado.

## <a name="exposing-a-chunkier-api"></a>Exposición de una API de chunkier

Hay un precio para pagar por la transición de nativo a administrado (y viceversa). Como tal, es mejor exponer la _fragmentación en lugar de las API de chat_ a los desarrolladores nativos, por ejemplo,

**Locuaces**

```csharp
public class Person {
  public string FirstName { get; set; }
  public string LastName { get; set; }
}
```

```objc
// this requires 3 calls / transitions to initialize the instance
Person *p = [[Person alloc] init];
p.firstName = @"Sebastien";
p.lastName = @"Pouliot";
```

**Chunky**

```csharp
public class Person {
  public Person (string firstName, string lastName) {}
}
```

```objc
// a single call / transition will perform better
Person *p = [[Person alloc] initWithFirstName:@"Sebastien" lastName:@"Pouliot"];
```

Dado que el número de transiciones es más pequeño, el rendimiento será mejor. También requiere que se genere menos código, por lo que también se generará una biblioteca nativa más pequeña.

## <a name="naming"></a>Nombra

La denominación de cosas es uno de los dos problemas más difíciles de informática, mientras que los demás almacenan en caché los errores de invalidación y desactivación. Afortunadamente, la incrustación de .NET puede protegerle de todos los nombres pero.

### <a name="types"></a>Tipos

Objective-C no admite espacios de nombres. En general, sus tipos tienen como prefijo un prefijo de carácter 2 (para Apple) o 3 (para terceros), `UIView` como en el caso de la vista de UIKit, que denota el marco de trabajo.

En el caso de los tipos .NET, no es posible omitir el espacio de nombres ya que puede introducir nombres duplicados o confusos. Esto hace que los tipos .NET existentes sean muy largos, por ejemplo,

```csharp
namespace Xamarin.Xml.Configuration {
  public class Reader {}
}
```

se utilizaría como:

```objc
id reader = [[Xamarin_Xml_Configuration_Reader alloc] init];
```

Sin embargo, puede volver a exponer el tipo como:

```csharp
public class XAMXmlConfigReader : Xamarin.Xml.Configuration.Reader {}
```

hacer que sea más fácil de usar, por ejemplo:

```objc
id reader = [[XAMXmlConfigReader alloc] init];
```

### <a name="methods"></a>Métodos

Incluso los nombres de .NET buenos podrían no ser ideales para una API de Objective-C.

Las convenciones de nomenclatura en Objective-C son diferentes de .NET (mayúsculas y minúsculas Camel en lugar de mayúsculas y minúsculas Pascal, más detallado).
Lea las [directrices de codificación de cacao](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html#//apple_ref/doc/uid/20001282-BCIGIJJF).

Desde el punto de vista de un desarrollador de Objective-C, un método `Get` con un prefijo implica que no es propietario de la instancia, es decir, la [regla get](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-SW1).

Esta regla de nomenclatura no tiene ninguna coincidencia en el mundo de GC de .NET; un método .net con un `Create` prefijo se comportará de forma idéntica en .net. Sin embargo, para los desarrolladores de Objective-C, normalmente significa que es el propietario de la instancia devuelta, es decir, la [regla de creación](https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029).

## <a name="exceptions"></a>Excepciones

Es bastante habitual en .NET usar las excepciones extensamente para notificar los errores. Sin embargo, son lentos y no son bastante idénticos en Objective-C. Siempre que sea posible, debe ocultarlos del desarrollador de Objective-C.

Por ejemplo, el patrón `Try` .net será mucho más fácil de consumir del código Objective-C:

```csharp
public int Parse (string number)
{
  return Int32.Parse (number);
}
```

utilizándo

```csharp
public bool TryParse (string number, out int value)
{
  return Int32.TryParse (number, out value);
}
```

### <a name="exceptions-inside-init"></a>Excepciones dentro de`init*`

En .NET, un constructor debe ejecutarse correctamente y devolver una instancia válida (_afortunadamente_) o producir una excepción.

En cambio, Objective-C permite `init*` que se `nil` devuelva cuando no se puede crear una instancia. Este es un patrón común, pero no general, que se usa en muchos de los marcos de trabajo de Apple. En algunos otros casos puede `assert` producirse un error (y eliminar el proceso actual).

El generador sigue el mismo `return nil` patrón para los `init*` métodos generados. Si se produce una excepción administrada, se imprimirá (mediante `NSLog`) y `nil` se devolverá al autor de la llamada.

## <a name="operators"></a>Operadores

Objective-C no permite que los operadores se sobrecarguen como C# lo hace, por lo que se convierten en selectores de clase.

Los métodos con nombre ["descriptivos"](https://docs.microsoft.com/dotnet/standard/design-guidelines/operator-overloads) se generan en preferencia a las sobrecargas de operador cuando se encuentran y pueden generar una API más fácil de usar.

Las clases que invalidan `!=` los operadores `==` ///deben reemplazar también el método Equals (Object) estándar.
