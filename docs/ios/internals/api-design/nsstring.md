---
title: NSString en Xamarin. iOS y Xamarin. Mac
description: En este documento se describe cómo Xamarin. iOS convierte de forma transparente objetos NSString C# en objetos de cadena, cuando esto no sucede.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: f744f4ed5619e4e7f4a9d85897c4451bf7e5b9bc
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022348"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString en Xamarin. iOS y Xamarin. Mac

El diseño de Xamarin. iOS y Xamarin. Mac llama a la API de uso para exponer el tipo de cadena de .NET nativo, `string`, para la manipulación C# de cadenas en y otros lenguajes de programación de .net y para exponer la cadena como el tipo de datos expuesto por la API en lugar de la `NSString`tipo de datos .

Esto significa que los desarrolladores no deben tener que mantener las cadenas que se van a usar para llamar a Xamarin. iOS & la API de Xamarin. Mac (unificada) en un tipo especial (`Foundation.NSString`), pueden seguir usando `System.String` de mono para todas las operaciones y siempre que una API en Xamarin. iOS o Xamarin. Mac requieren una cadena, nuestro enlace de API se encarga de calcular las referencias de la información.

Por ejemplo, la propiedad de "texto" de Objective-C en un `UILabel` de tipo `NSString`, se declara de la siguiente manera:

```objc
@property(nonatomic, copy) NSString *text
```

Esto se expone en Xamarin. iOS como:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

En segundo plano, la implementación de esta propiedad calcula las referencias C# de la cadena en un`NSString`y llama al método`objc_msgSend`de la misma manera que lo haría Objective-C.

Hay unas cuantas API de Objective-C de terceros que no consumen un `NSString`, sino que consumen una cadena de C (un "*Char*"). En esos casos, todavía puede usar el tipo C# de datos de cadena, pero debe usar el atributo [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) para informar al generador de enlaces de que esta cadena no se debe serializar como`NSString`, sino como una cadena de C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Excepciones a la regla

En Xamarin. iOS y Xamarin. Mac, hemos realizado una excepción a esta regla. La decisión entre el momento en que se exponen `string`s y cuando se realiza una excepción Except y exponen `NSString`s, se realiza si el método `NSString` podría estar realizando una comparación de puntero en lugar de una comparación de contenido.

Esto puede ocurrir cuando una API de Objective-C usa una constante `NSString` pública como un token que representa alguna acción, en lugar de comparar el contenido real de la cadena.

En estos casos, se exponen `NSString`API de y hay una minoría de API que tienen este. También observará que las propiedades de NSString se exponen en algunas clases. Dichas propiedades de `NSString` se exponen para elementos como las notificaciones. Estas propiedades suelen tener el siguiente aspecto:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Las notificaciones son claves que se usan para la clase `NSNotification` cuando desea registrarse para un evento determinado que se difunde mediante el tiempo de ejecución.

Las claves suelen tener un aspecto similar al siguiente:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Otro lugar donde `NSString`s se exponen en la API es como tokens que se usan como parámetros para determinadas API de iOS u OS X que toman `NSDictionary` objetos como parámetros. Normalmente, el diccionario contiene claves `NSString`. Xamarin. iOS, por Convención, nombra esas propiedades de `NSString` estáticas agregando el nombre "Key".
