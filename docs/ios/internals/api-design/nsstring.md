---
title: NSString en Xamarin. iOS y Xamarin. Mac
description: En este documento se describe cómo Xamarin. iOS convierte de forma transparente objetos NSString C# en objetos de cadena, cuando esto no sucede.
ms.prod: xamarin
ms.assetid: 785744B3-42E2-4590-8F41-435325E609B9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 589b584e0526ffdc56dd5ec26aa25a0b61e2141a
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889900"
---
# <a name="nsstring-in-xamarinios-and-xamarinmac"></a>NSString en Xamarin. iOS y Xamarin. Mac

El diseño de Xamarin. iOS y Xamarin. Mac llama a la API de uso para exponer el tipo de cadena de .net nativo `string`,, para la manipulación C# de cadenas en y otros lenguajes de programación de .net y para exponer la cadena como el tipo de datos expuesto por la API en lugar de el `NSString`tipo de datos.

Esto significa que los desarrolladores no deben tener que mantener las cadenas que se van a usar para llamar a Xamarin. iOS & la API de Xamarin. Mac (unificada) en`Foundation.NSString`un tipo especial (), pueden seguir `System.String` usando mono para todas las operaciones y siempre que una API de Xamarin. iOS o Xamarin. Mac requiere una cadena, nuestro enlace de API se encarga de calcular las referencias de la información.

Por ejemplo, la propiedad de "texto" de Objective-C `UILabel` en una `NSString`de tipo, se declara de la siguiente manera:

```objc
@property(nonatomic, copy) NSString *text
```

Esto se expone en Xamarin. iOS como:

```csharp
class UILabel {
    public string Text { get; set; }
}
```

En segundo plano, la implementación de esta propiedad calcula las referencias C# de la cadena `NSString` en y llama `objc_msgSend` al método del mismo modo que lo haría Objective-C.

Hay una serie de API de Objective-C de terceros que no consumen un `NSString`, sino que consumen una cadena de c (un "*Char*"). En esos casos, todavía puede usar el tipo C# de datos de cadena, pero debe usar el atributo [[PlainString]](~/cross-platform/macios/binding/objective-c-libraries.md) para informar al generador de enlaces de que esta cadena no se debe serializar `NSString`como, sino como una cadena de C.

 <a name="Exceptions_to_the_Rule" />

## <a name="exceptions-to-the-rule"></a>Excepciones a la regla

En Xamarin. iOS y Xamarin. Mac, hemos realizado una excepción a esta regla. La decisión entre el momento en `string`que se exponen y cuando se realiza una `NSString`excepción Except y exponen, se realiza si el `NSString` método puede realizar una comparación de punteros en lugar de una comparación de contenido.

Esto puede ocurrir cuando una API de Objective-C usa una `NSString`constante pública como token que representa alguna acción, en lugar de comparar el contenido real de la cadena.

En esos casos, `NSString`  las API se exponen y hay una minoría de API que tienen este. También observará que las propiedades de NSString se exponen en algunas clases. Dichas `NSString` propiedades se exponen para elementos como las notificaciones. Estas propiedades suelen tener el siguiente aspecto:

```csharp
class Foo {
     public NSString FooNotification { get; }
}
```

Las notificaciones son claves que se usan para `NSNotification` la clase cuando desea registrarse para un evento determinado que se va a difundir mediante el tiempo de ejecución.

Las claves suelen tener un aspecto similar al siguiente:

```csharp
class Foo {
     public NSString FooBarKey { get; }
}
```

Otro lugar donde `NSString`se exponen en la API es como tokens que se usan como parámetros para determinadas API de iOS u OS X que `NSDictionary` toman objetos como parámetros. Normalmente, el diccionario `NSString` contiene claves. Xamarin. iOS, por Convención, nombra esas propiedades `NSString` estáticas agregando el nombre "clave".
