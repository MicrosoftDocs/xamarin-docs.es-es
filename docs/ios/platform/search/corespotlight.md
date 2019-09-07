---
title: Búsqueda con Spotlight principales en Xamarin. iOS
description: En este documento se describe cómo usar Spotlight principales en una aplicación de Xamarin. iOS para proporcionar vínculos a contenido en la aplicación. Describe cómo crear, restaurar, actualizar y eliminar los elementos que se pueden buscar.
ms.prod: xamarin
ms.assetid: 1374914C-0F63-41BF-BD97-EBCEE86E57B1
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/20/2017
ms.openlocfilehash: 00a973e670ff5100a44ba158fe50f134781a97e2
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769508"
---
# <a name="search-with-core-spotlight-in-xamarinios"></a>Búsqueda con Spotlight principales en Xamarin. iOS

Spotlight Core es un nuevo marco de trabajo de iOS 9 que presenta una API de tipo base de datos para agregar, editar o eliminar vínculos a contenido dentro de la aplicación. Los elementos que se han agregado mediante Spotlight principales estarán disponibles en la búsqueda de Spotlight en el dispositivo iOS.

Para ver un ejemplo de los tipos de contenido que se pueden indizar mediante Spotlight principales, eche un vistazo a las aplicaciones de mensajes, correo, calendario y notas de Apple. Todos ellos usan actualmente Spotlight principales para proporcionar resultados de búsqueda.

## <a name="creating-an-item"></a>Crear un elemento

A continuación se facilita un ejemplo de creación de un elemento y su indexación mediante Spotlight principal:

```csharp
using CoreSpotlight;
...

// Create attributes to describe an item
var attributes = new CSSearchableItemAttributeSet();
attributes.Title = "App Center Test";
attributes.ContentDescription = "Automatically test your app on 1,000 devices in the cloud.";

// Create item
var item = new CSSearchableItem ("1", "products", attributes);

// Index item
CSSearchableIndex.DefaultSearchableIndex.Index (new CSSearchableItem[]{ item }, (error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Esta información aparecería similar a la siguiente en un resultado de la búsqueda:

[![](corespotlight-images/corespotlight01.png "Introducción al resultado principal de la búsqueda de Spotlight")](corespotlight-images/corespotlight01.png#lightbox)

## <a name="restoring-an-item"></a>Restaurar un elemento

Cuando el usuario puntea en un elemento que se agrega al resultado de la búsqueda a través del centro destacado `AppDelegate` de `ContinueUserActivity` la aplicación, se llama al método (este `NSUserActivity`método también se usa para). Por ejemplo:

```csharp
public override bool ContinueUserActivity (UIApplication application,
   NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{

    // Take action based on the activity type
    switch (userActivity.ActivityType) {
    case "com.xamarin.platform":
        // Restore the state of the app here...
        break;
    default:
        if (userActivity.ActivityType == CSSearchableItem.ActionType.ToString ()) {
            // Display content for searchable item...
        }
        break;
    }

    return true;
}
```

Tenga en cuenta que esta vez se comprueba si la actividad tiene `ActivityType` un `CSSearchableItem.ActionType`de.

## <a name="updating-an-item"></a>Actualizar un elemento

Puede haber ocasiones en las que se necesite modificar un elemento de índice creado con Spotlight principales, como un cambio en el título o en la imagen en miniatura. Para efectuar este cambio, usamos el mismo método que se usó para crear inicialmente el índice.
Creamos un nuevo `CSSearchableItem` con el mismo identificador que se usó para crear el elemento y adjuntar `CSSearchableItemAttributeSet` un nuevo que contiene los atributos modificados:

[![](corespotlight-images/corespotlight02.png "Información general de actualización de un elemento")](corespotlight-images/corespotlight02.png#lightbox)

Cuando este elemento se escribe en el índice de búsqueda, el elemento existente se actualiza con la nueva información.

## <a name="deleting-an-item"></a>Eliminar un elemento

Spotlight Core proporciona varias maneras de eliminar un elemento de índice cuando ya no es necesario.

En primer lugar, puede eliminar un elemento por su identificador, por ejemplo:

```csharp
// Delete Items by ID
CSSearchableIndex.DefaultSearchableIndex.Delete(new string[]{"1","16"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Después, puede eliminar un grupo de elementos de índice por su nombre de dominio. Por ejemplo:

```csharp
// Delete by Domain Name
CSSearchableIndex.DefaultSearchableIndex.DeleteWithDomain(new string[]{"domain-name"},(error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

Por último, puede eliminar todos los elementos de índice con el código siguiente:

```csharp
// Delete all index items
CSSearchableIndex.DefaultSearchableIndex.DeleteAll((error) => {
    // Successful?
    if (error !=null) {
        Console.WriteLine(error.LocalizedDescription);
    }
});
```

## <a name="additional-core-spotlight-features"></a>Características principales adicionales de Spotlight

Spotlight Core tiene las siguientes características que ayudan a mantener la precisión y la actualización del índice:

- **Compatibilidad con la actualización por lotes** : Si la aplicación necesita crear o modificar un grupo grande de índices al mismo tiempo, todo el lote se puede enviar al `Index` método de la `CSSearchableIndex` clase en una llamada.
- **Responder a los cambios en** el índice `CSSearchableIndexDelegate` : el uso de la aplicación puede responder a los cambios y las notificaciones del índice de búsqueda.
- **Aplicar la protección de datos** : mediante las clases de protección de datos, puede implementar la seguridad en los elementos que se agregan al índice de búsqueda mediante Spotlight principal.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [iOS 9 para desarrolladores](https://developer.apple.com/ios/pre-release/)
- [iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Guía de programación de búsqueda de aplicaciones](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/AppSearch/index.html#//apple_ref/doc/uid/TP40016308)
