---
title: NSString en Xamarin.iOS y Xamarin.Mac
description: En este documento se describe cómo Xamarin.iOS convierte de forma transparente objetos NSString en objetos de cadena C#, cuando esto no sucede.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 314c94fc9208a63e2f9305511df262327df921a5
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84565075"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString en Xamarin.iOS y Xamarin.Mac

El diseño de Xamarin.iOS y Xamarin.Mac llama a la API de uso para exponer el tipo de cadena de .NET Native, `string`, para la manipulación de cadenas en C# y otros lenguajes de programación de .NET y para exponer la cadena como el tipo de datos expuesto por la API en lugar del tipo de datos  `NSString`.

Esto significa que no es necesario que los desarrolladores deban conservar las cadenas pensadas para llamar a la API Xamarin.iOS y Xamarin.Mac (unificadas) en un tipo especial (`Foundation.NSString`), pueden seguir usando `System.String` de Mono para todas las operaciones y, siempre que una API de Xamarin.iOS o Xamarin.Mac requiera una cadena, nuestro enlace de la API se encarga de serializar la información.

Por ejemplo, la propiedad "text" de Objective-C en un elemento `UILabel` de tipo `NSString`, se declara de la siguiente manera:

```objc
@property(nonatomic, copy) NSString *text
```

Esto se expone en Xamarin.iOS como:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

En segundo plano, la implementación de esta propiedad serializa las cadenas de C# en un elemento `NSString` y llama al método `objc_msgSend` de la misma manera que lo haría Objective-C.

Hay varias API de Objective-C de terceros que no consumen un elemento `NSString`, sino que consumen una cadena de C ("*char*"). En esos casos, todavía se puede usar el tipo de datos de cadena de C#, pero se debe usar el atributo [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) para informar al generador de enlaces de que esta cadena no debe serializarse como `NSString`, sino como una cadena de C.

 <a name="Exceptions_to_the_Rule"></a>

## <a name="exceptions-to-the-rule"></a>Excepciones a la regla

Tanto en Xamarin.iOS como en Xamarin.Mac, existe una excepción a esta regla. La decisión entre el momento en que se exponen los elementos `string` y cuando se aplica una excepción y se exponen los elementos `NSString`, se toma cuando el método `NSString` podría estar realizando una comparación de puntero en lugar de una comparación de contenido.

Esto puede ocurrir cuando una API de Objective-C usa una constante `NSString` pública como un token que representa alguna acción, en lugar de comparar el contenido real de la cadena.

En estos casos, se exponen las API`NSString` y hay una minoría de API que tienen esto. También observará que las propiedades NSString se exponen en algunas clases. Estas propiedades `NSString` se exponen para elementos como las notificaciones. Estas propiedades suelen tener el siguiente aspecto:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Las notificaciones son claves que se usan para la clase `NSNotification` cuando desea registrarse para un evento determinado que el tiempo de ejecución difunde.

Las claves suelen ser parecidas a esta:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Otro lugar donde se exponen los elementos `NSString` en la API es como tokens que se usan como parámetros para determinadas API de iOS u OS X que toman objetos `NSDictionary` como parámetros. Normalmente, el diccionario contiene claves `NSString`. Xamarin.iOS, por convención, nombra esas propiedades `NSString` estáticas agregando el nombre "Key".
