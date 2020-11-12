---
title: MessagingCenter de Xamarin.Forms
description: La clase MessagingCenter de Xamarin.Forms implementa el patrón de publicación y suscripción, lo que permite la comunicación basada en mensajes entre los componentes que no se recomienda vincular mediante referencias de tipo y objeto.
ms.prod: xamarin
ms.assetid: EDFE7B19-C5FD-40D5-816C-FAE56532E885
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5ccff2fd24537be0bbf2d809b20a00bc35667c95
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366872"
---
# <a name="no-locxamarinforms-messagingcenter"></a>MessagingCenter de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)

El patrón de publicación y suscripción es un patrón de mensajería en el que los publicadores envían mensajes sin tener conocimiento de los destinatarios, que se conocen como suscriptores. Del mismo modo, los suscriptores escuchan mensajes específicos, sin tener conocimiento de ningún publicador.

Los eventos de .NET implementan el patrón de publicación y suscripción, y son el enfoque más sencillo para una capa de comunicación entre los componentes si no se requiere el acoplamiento flexible, como sucede con un control y la página que lo contiene. Pero las duraciones del publicador y del suscriptor están acopladas por referencias a objetos, y el tipo de suscriptor debe tener una referencia al tipo de publicador. Esto puede generar problemas de administración de memoria, en especial cuando hay objetos de corta duración que se suscriben a un evento de un objeto estático o de larga duración. Si no se quita el controlador de eventos, el suscriptor se mantendrá activo mediante la referencia a él en el publicador y esto impedirá o retrasará la recolección de elementos no utilizados del suscriptor.

La clase [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) de Xamarin.Forms implementa el patrón de publicación y suscripción, lo que permite la comunicación basada en mensajes entre los componentes que no se recomienda vincular mediante referencias de tipo y objeto. Este mecanismo permite a los publicadores y suscriptores comunicarse sin tener ninguna referencia mutua, lo que ayuda a reducir su interdependencia.

La clase [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) proporciona la funcionalidad de publicación y suscripción de multidifusión. Esto significa que puede haber varios publicadores que publican un único mensaje, y puede haber varios suscriptores que escuchen el mismo mensaje:

![Funcionalidad de publicación y suscripción de multidifusión](messaging-center-images/messaging-center.png)

Los publicadores envían mensajes con el método [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*), mientras que los suscriptores escuchan mensajes con el método [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). Además, en caso necesario, los suscriptores también pueden cancelar la suscripción a mensajes con el método [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*).

> [!IMPORTANT]
> Internamente, la clase [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) utiliza referencias débiles. Esto significa que no mantendrá los objetos activos y permitirá la recolección de elementos no utilizados. Por lo tanto, solo debería ser necesario cancelar la suscripción a un mensaje cuando una clase ya no quiere recibir el mensaje.

## <a name="publish-a-message"></a>Publicación de un mensaje

Los mensajes de [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) son cadenas. Los publicadores informan a los suscriptores de un mensaje con una de las sobrecargas de [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*). En el ejemplo de código siguiente, se publica un mensaje de `Hi`:

```csharp
MessagingCenter.Send<MainPage>(this, "Hi");
```

En este ejemplo, el método [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) especifica un argumento genérico que representa al emisor. Para recibir el mensaje, un suscriptor también debe especificar el mismo argumento genérico, lo que indica que está escuchando un mensaje de ese emisor. Además, en este ejemplo se especifican dos argumentos de método:

- El primer argumento especifica la instancia del emisor.
- El segundo argumento especifica el mensaje.

Los datos de carga también se pueden enviar con un mensaje:

```csharp
MessagingCenter.Send<MainPage, string>(this, "Hi", "John");
```

En este ejemplo, el método [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) especifica dos argumentos genéricos. El primero es el tipo que envía el mensaje, y el segundo es el tipo de los datos de carga que se envían. Para recibir el mensaje, un suscriptor también debe especificar los mismos argumentos genéricos. Esto habilita varios mensajes que comparten una identidad de mensaje, pero que envían diferentes tipos de datos de carga para que los reciban los distintos suscriptores. Además, en este ejemplo se especifica un tercer argumento de método que representa los datos de carga que se van a enviar al suscriptor. En este caso, los datos de carga son un elemento `string`.

El método [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) publicará el mensaje y cualquier dato de carga mediante un enfoque del tipo "dispara y olvida". Por lo tanto, el mensaje se envía incluso aunque no haya ningún suscriptor registrado para recibir el mensaje. En esta situación, se omite el mensaje enviado.

## <a name="subscribe-to-a-message"></a>Suscripción a un mensaje

Los suscriptores pueden registrarse para recibir un mensaje mediante una de las sobrecargas de [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). El ejemplo de código siguiente muestra un ejemplo:

```csharp
MessagingCenter.Subscribe<MainPage> (this, "Hi", (sender) =>
{
    // Do something whenever the "Hi" message is received
});
```

En este ejemplo, el método [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) suscribe el objeto `this` a los mensajes de `Hi` enviados por el tipo `MainPage` y ejecuta un delegado de devolución de llamada en respuesta a la recepción del mensaje. El delegado de devolución de llamada, especificado como una expresión lambda, podría ser código que actualiza la IU, guarda algunos datos o desencadena alguna otra operación.

> [!NOTE]
> Es posible que un suscriptor no necesite controlar cada instancia de un mensaje publicado, lo cual se puede controlar mediante los argumentos de tipo genérico que se especifican en el método [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*).

En el ejemplo siguiente se muestra cómo suscribirse a un mensaje que contiene datos de carga:

```csharp
MessagingCenter.Subscribe<MainPage, string>(this, "Hi", async (sender, arg) =>
{
    await DisplayAlert("Message received", "arg=" + arg, "OK");
});
```

En este ejemplo, el método [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) se suscribe a los mensajes de `Hi` enviados por el tipo `MainPage`, cuyos datos de carga son un elemento `string`. Un delegado de devolución de llamada se ejecuta como respuesta a la recepción de este tipo de mensaje, que muestra los datos de carga en una alerta.

> [!IMPORTANT]
> El delegado que ejecuta el método `Subscribe` se ejecutará en el mismo subproceso que publica el mensaje mediante el método `Send`.

## <a name="unsubscribe-from-a-message"></a>Cancelación de la suscripción a un mensaje

Los suscriptores pueden cancelar la suscripción a los mensajes que ya no quieren recibir. Para ello, hay que usar una de las sobrecargas de [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*).

```csharp
MessagingCenter.Unsubscribe<MainPage>(this, "Hi");
```

En este ejemplo, el método [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) cancela la suscripción del objeto de `this` al mensaje `Hi` enviado por el tipo `MainPage`.

Se debe cancelar la suscripción a los mensajes que contienen datos de la sobrecarga [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) y que especifican dos argumentos genéricos:

```csharp
MessagingCenter.Unsubscribe<MainPage, string>(this, "Hi");
```

En este ejemplo, el método [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) cancela la suscripción del objeto `this` al mensaje `Hi` enviado por el tipo `MainPage`, cuyos datos de carga son un elemento `string`.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplo de MessagingCenter](/samples/xamarin/xamarin-forms-samples/usingmessagingcenter)