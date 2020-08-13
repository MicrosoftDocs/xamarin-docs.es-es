---
title: Comunicación entre componentes débilmente acoplados
description: 'En este capítulo se explica el modo en que la aplicación móvil eShopOnContainers implementa el patrón de publicación y suscripción, lo que permite la comunicación basada en mensajes entre los componentes que no se recomienda vincular mediante referencias de tipo y objeto. '
ms.prod: xamarin
ms.assetid: 1194af33-8a91-48d2-88b5-b84d77f2ce69
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bf0315d2d077e06ff3ded4d66814afe050fdfad4
ms.sourcegitcommit: f7fe46c0236a7130b63a33d9d1670d5111582dd2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186205"
---
# <a name="communicating-between-loosely-coupled-components"></a>Comunicación entre componentes débilmente acoplados

El patrón de publicación y suscripción es un patrón de mensajería en el que los publicadores envían mensajes sin tener conocimiento de los destinatarios, que se conocen como suscriptores. Del mismo modo, los suscriptores escuchan mensajes específicos, sin tener conocimiento de ningún publicador.

Los eventos de .NET implementan el patrón de publicación y suscripción, y son el enfoque más sencillo para una capa de comunicación entre los componentes si no se requiere el acoplamiento flexible, como sucede con un control y la página que lo contiene. Pero las duraciones del publicador y del suscriptor están acopladas por referencias a objetos, y el tipo de suscriptor debe tener una referencia al tipo de publicador. Esto puede generar problemas de administración de memoria, en especial cuando hay objetos de corta duración que se suscriben a un evento de un objeto estático o de larga duración. Si no se quita el controlador de eventos, el suscriptor se mantendrá activo mediante la referencia a él en el publicador y esto impedirá o retrasará la recolección de elementos no utilizados del suscriptor.

## <a name="introduction-to-messagingcenter"></a>Introducción a MessagingCenter

La clase [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) de Xamarin.Forms implementa el patrón de publicación y suscripción, lo que permite la comunicación basada en mensajes entre los componentes que no se recomienda vincular mediante referencias de tipo y objeto. Este mecanismo permite a los publicadores y suscriptores comunicarse sin tener una referencia mutua, lo que ayuda a reducir las dependencias entre los componentes, a la vez que permite que los componentes se desarrollen y prueben de forma independiente.

La clase [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) proporciona la funcionalidad de publicación y suscripción de multidifusión. Esto significa que puede haber varios publicadores que publican un único mensaje y puede haber varios suscriptores que escuchen el mismo mensaje. En la figura 4-1 se muestra esta relación:

![Funcionalidad de publicación y suscripción de multidifusión](communicating-between-loosely-coupled-components-images/messagingcenter.png)

**Figura 4-1:** Funcionalidad de publicación y suscripción de multidifusión

Los publicadores envían mensajes con el método [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*), mientras que los suscriptores escuchan mensajes con el método [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). Además, en caso necesario, los suscriptores también pueden cancelar la suscripción a mensajes con el método [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*).

Internamente, la clase [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) utiliza referencias débiles. Esto significa que no mantendrá los objetos activos y permitirá la recolección de elementos no utilizados. Por lo tanto, solo debería ser necesario cancelar la suscripción a un mensaje cuando una clase ya no quiere recibir el mensaje.

La aplicación móvil eShopOnContainers usa la [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) clase para la comunicación entre componentes de acoplamiento flexible. La aplicación define tres mensajes:

- `AddProduct`La clase publica el mensaje `CatalogViewModel` cuando se agrega un elemento a la cesta de la compra. A cambio, la `BasketViewModel` clase se suscribe al mensaje e incrementa el número de elementos de la cesta de la compra en respuesta. Además, la `BasketViewModel` clase también cancela la suscripción de este mensaje.
- La `Filter` clase publica el mensaje `CatalogViewModel` cuando el usuario aplica una marca o un filtro de tipo a los elementos que se muestran en el catálogo. A cambio, la `CatalogView` clase se suscribe al mensaje y actualiza la interfaz de usuario para que solo se muestren los elementos que coinciden con los criterios de filtro.
- La `ChangeTab` clase publica el mensaje `MainViewModel` cuando se `CheckoutViewModel` desplaza a la `MainViewModel` siguiente creación y envío correctos de un nuevo pedido. A cambio, la `MainView` clase se suscribe al mensaje y actualiza la interfaz de usuario para que la pestaña **mi perfil** esté activa, con el fin de mostrar los pedidos del usuario.

> [!NOTE]
> Aunque la [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) clase permite la comunicación entre clases de acoplamiento flexible, no ofrece la única solución arquitectónica a este problema. Por ejemplo, el motor de enlace y las notificaciones de cambio de propiedad también pueden lograr la comunicación entre un modelo de vista y una vista. Además, la comunicación entre dos modelos de vista también se puede lograr pasando datos durante la navegación.

En la aplicación móvil eShopOnContainers, [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) se usa para actualizar en la interfaz de usuario en respuesta a una acción que se produce en otra clase. Por lo tanto, los mensajes se publican en el subproceso de la interfaz de usuario y los suscriptores reciben el mensaje en el mismo subproceso.

> [!TIP]
> Calcular las referencias al subproceso de interfaz de usuario al realizar actualizaciones de la interfaz de usuario. Si se requiere un mensaje enviado desde un subproceso en segundo plano para actualizar la interfaz de usuario, procese el mensaje en el subproceso de la interfaz de usuario en el suscriptor mediante la invocación del [`Device.BeginInvokeOnMainThread`](xref:Xamarin.Forms.Device.BeginInvokeOnMainThread(System.Action)) método.

Para obtener más información sobre [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) , vea [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md).

## <a name="defining-a-message"></a>Definir un mensaje

[`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter)los mensajes son cadenas que se usan para identificar mensajes. En el ejemplo de código siguiente se muestran los mensajes definidos dentro de la aplicación móvil eShopOnContainers:

```csharp
public class MessageKeys  
{  
    // Add product to basket  
    public const string AddProduct = "AddProduct";  

    // Filter  
    public const string Filter = "Filter";  

    // Change selected Tab programmatically  
    public const string ChangeTab = "ChangeTab";  
}
```

En este ejemplo, los mensajes se definen mediante constantes. La ventaja de este enfoque es que proporciona compatibilidad con la refactorización y la seguridad de tipos en tiempo de compilación.

## <a name="publishing-a-message"></a>Publicar un mensaje

Los publicadores informan a los suscriptores de un mensaje con una de las sobrecargas de [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*). En el ejemplo de código siguiente se muestra cómo publicar el `AddProduct` mensaje:

```csharp
MessagingCenter.Send(this, MessageKeys.AddProduct, catalogItem);
```

En este ejemplo, el [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método especifica tres argumentos:

- El primer argumento especifica la clase Sender. Los suscriptores que deseen recibir el mensaje deben especificar la clase Sender.
- El segundo argumento especifica el mensaje.
- El tercer argumento especifica los datos de carga que se enviarán al suscriptor. En este caso, los datos de carga son una `CatalogItem` instancia de.

El [`Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método publicará el mensaje y sus datos de carga, con un enfoque de desencadenamiento y olvidar. Por lo tanto, el mensaje se envía incluso aunque no haya ningún suscriptor registrado para recibir el mensaje. En esta situación, se omite el mensaje enviado.

> [!NOTE]
> El [`MessagingCenter.Send`](xref:Xamarin.Forms.MessagingCenter.Send*) método puede usar parámetros genéricos para controlar el modo en que se entregan los mensajes. Por lo tanto, los distintos suscriptores pueden recibir varios mensajes que comparten una identidad de mensaje, pero envían distintos tipos de datos de carga.

## <a name="subscribing-to-a-message"></a>Suscribirse a un mensaje

Los suscriptores pueden registrarse para recibir un mensaje mediante una de las sobrecargas de [`MessagingCenter.Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). En el ejemplo de código siguiente se muestra cómo la aplicación móvil eShopOnContainers se suscribe y procesa el `AddProduct` mensaje:

```csharp
MessagingCenter.Subscribe<CatalogViewModel, CatalogItem>(  
    this, MessageKeys.AddProduct, async (sender, arg) =>  
{  
    BadgeCount++;  

    await AddCatalogItemAsync(arg);  
});
```

En este ejemplo, el [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*) método se suscribe al `AddProduct` mensaje y ejecuta un delegado de devolución de llamada en respuesta a la recepción del mensaje. Este delegado de devolución de llamada, especificado como una expresión lambda, ejecuta código que actualiza la interfaz de usuario.

> [!TIP]
> Considere la posibilidad de utilizar datos de carga inmutables. No intente modificar los datos de carga desde dentro de un delegado de devolución de llamada porque varios subprocesos podrían tener acceso a los datos recibidos simultáneamente. En este escenario, los datos de carga deben ser inmutables para evitar errores de simultaneidad.

Es posible que un suscriptor no necesite controlar cada instancia de un mensaje publicado, lo cual se puede controlar mediante los argumentos de tipo genérico que se especifican en el método [`Subscribe`](xref:Xamarin.Forms.MessagingCenter.Subscribe*). En este ejemplo, el suscriptor solo recibirá `AddProduct` los mensajes que se envían desde la `CatalogViewModel` clase, cuyos datos de carga son una `CatalogItem` instancia.

## <a name="unsubscribing-from-a-message"></a>Cancelar la suscripción de un mensaje

Los suscriptores pueden cancelar la suscripción a los mensajes que ya no quieren recibir. Esto se logra con una de las [`MessagingCenter.Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) sobrecargas, tal y como se muestra en el ejemplo de código siguiente:

```csharp
MessagingCenter.Unsubscribe<CatalogViewModel, CatalogItem>(this, MessageKeys.AddProduct);
```

En este ejemplo, la [`Unsubscribe`](xref:Xamarin.Forms.MessagingCenter.Unsubscribe*) Sintaxis del método refleja los argumentos de tipo especificados cuando se suscribe para recibir el `AddProduct` mensaje.

## <a name="summary"></a>Resumen

La clase [`MessagingCenter`](xref:Xamarin.Forms.MessagingCenter) de Xamarin.Forms implementa el patrón de publicación y suscripción, lo que permite la comunicación basada en mensajes entre los componentes que no se recomienda vincular mediante referencias de tipo y objeto. Este mecanismo permite a los publicadores y suscriptores comunicarse sin tener una referencia mutua, lo que ayuda a reducir las dependencias entre los componentes, a la vez que permite que los componentes se desarrollen y prueben de forma independiente.

## <a name="related-links"></a>Vínculos relacionados

- [Descargar libro electrónico (2 MB PDF)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (ejemplo)](https://github.com/dotnet-architecture/eShopOnContainers)
