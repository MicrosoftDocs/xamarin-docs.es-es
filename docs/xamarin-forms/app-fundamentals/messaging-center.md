---
title: MessagingCenter de Xamarin.Forms
description: En este artículo se explica cómo usar MessagingCenter de Xamarin.Forms para enviar y recibir mensajes, a fin de reducir el acoplamiento entre clases como modelos de vista.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/01/2016
ms.openlocfilehash: b40617dc9ed2054540ce04d5527fae8de6e2285b
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644906"
---
# <a name="xamarinforms-messagingcenter"></a>MessagingCenter de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

_Xamarin.Forms incluye un servicio de mensajería simple para enviar y recibir mensajes._

<a name="Overview" />

## <a name="overview"></a>Información general

`MessagingCenter` de Xamarin.Forms permite que los modelos de vista y otros componentes se comuniquen sin tener que saber nada sobre los demás, salvo un sencillo contrato de mensajería.

<a name="How_the_MessagingCenter_Works" />

## <a name="how-the-messagingcenter-works"></a>Funcionamiento de MessagingCenter

`MessagingCenter` tiene dos partes:

-  **Subscribe**: escucha mensajes con una determinada firma y realiza alguna acción cuando se reciben. Varios suscriptores pueden estar escuchando el mismo mensaje.
-  **Send**: publica un mensaje para que los agentes de escucha actúen en consecuencia. Si no hay ningún agente de escucha suscrito, se omite el mensaje.

`MessagingCenter` es una clase estática con métodos `Subscribe` y `Send` que se usan en toda la solución.

Los mensajes tienen un parámetro de cadena `message` que se usa como forma de *dirigir* mensajes. Los métodos `Subscribe` y `Send` usan parámetros genéricos para un mayor control sobre la entrega de los mensajes: dos mensajes con el mismo texto `message` pero argumentos de tipo genérico diferentes no se entregan al mismo suscriptor.

La API de `MessagingCenter` es simple:

- `Subscribe<TSender> (object subscriber, string message, Action<TSender> callback, TSender source = null)`
- `Subscribe<TSender, TArgs> (object subscriber, string message, Action<TSender, TArgs> callback, TSender source = null)`
- `Send<TSender> (TSender sender, string message)`
- `Send<TSender, TArgs> (TSender sender, string message, TArgs args)`
- `Unsubscribe<TSender, TArgs> (object subscriber, string message)`
- `Unsubscribe<TSender> (object subscriber, string message)`

Estos métodos se explican a continuación.

<a name="Using_the_MessagingCenter" />

## <a name="using-the-messagingcenter"></a>Uso de MessagingCenter

Los mensajes se pueden enviar como resultado de la interacción del usuario (por ejemplo, un clic en un botón), un evento del sistema (por ejemplo, controles que cambian el estado) o algún otro incidente (como la finalización de una descarga asincrónica). Los suscriptores pueden escuchar para cambiar la apariencia de la interfaz de usuario, guardar datos o desencadenar alguna otra operación.

Para obtener más información sobre el uso de la clase `MessagingCenter`, vea [Comunicación entre componentes débilmente acoplados](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md).

### <a name="simple-string-message"></a>Mensaje de cadena simple

El mensaje más simple contiene solo una cadena en el parámetro `message`. A continuación se muestra un método `Subscribe` que *escucha* un mensaje de cadena simple: observe el tipo genérico que especifica que se espera que el remitente sea de tipo `MainPage`. Las clases de la solución pueden suscribirse al mensaje con esta sintaxis:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) => {
    // do something whenever the "Hi" message is sent
});
```

En la clase `MainPage`, el siguiente código *envía* el mensaje. El parámetro `this` es una instancia de `MainPage`.

```csharp
MessagingCenter.Send<MainPage> (this, "Hi");
```

La cadena no cambia: indica el *tipo de mensaje* y se usa para determinar a qué suscriptores se va a notificar. Este tipo de mensaje se usa para indicar que se ha producido un evento, como "carga completada", donde no se requiere ninguna información adicional.

### <a name="passing-an-argument"></a>Pasar un argumento

Para pasar un argumento con el mensaje, especifique el argumento Type en los argumentos genéricos `Subscribe` y en la firma Action.

```csharp
MessagingCenter.Subscribe<MainPage, string> (this, "Hi", (sender, arg) => {
    // do something whenever the "Hi" message is sent
    // using the 'arg' parameter which is a string
});
```

Para enviar el mensaje con argumento, incluya el parámetro genérico Type y el valor del argumento en la llamada al método `Send`.

```csharp
MessagingCenter.Send<MainPage, string> (this, "Hi", "John");
```

Este ejemplo sencillo usa un argumento `string`, pero se puede pasar cualquier objeto de C#.

### <a name="unsubscribe"></a>Cancelar suscripción

Un objeto puede cancelar la suscripción de una firma de mensaje para que no se entreguen los mensajes futuros. La sintaxis del método `Unsubscribe` debe reflejar la firma del mensaje (así que puede ser necesario incluir el parámetro genérico Type para el argumento del mensaje).

```csharp
MessagingCenter.Unsubscribe<MainPage> (this, "Hi");
MessagingCenter.Unsubscribe<MainPage, string> (this, "Hi");
```

<a name="Summary" />

## <a name="summary"></a>Resumen

MessagingCenter es una manera sencilla de reducir el acoplamiento, especialmente entre modelos de vista. Se puede usar para enviar y recibir mensajes sencillos o pasar un argumento entre clases. Las clases deben cancelar la suscripción a mensajes que ya no quieran recibir.


## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de MessagingCenter](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)
- [Xamarin.Forms Samples](https://github.com/xamarin/xamarin-forms-samples) (Ejemplos de Xamarin.Forms)
- [Comunicación entre componentes débilmente acoplados](~/xamarin-forms/enterprise-application-patterns/communicating-between-loosely-coupled-components.md)
