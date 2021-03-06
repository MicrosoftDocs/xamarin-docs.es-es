---
title: Varias ventanas para iPad en Xamarin. iOS
description: Agregar compatibilidad con varias ventanas a las aplicaciones de iPad.
ms.prod: xamarin
ms.assetid: 524b6f2e-dbdf-11e9-8a34-2a2ae2dbcce4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/20/2019
ms.openlocfilehash: ce7df59d41efdd2d151fd2ea73cf26b40ee7fa10
ms.sourcegitcommit: 834466c9d9cf35e9659e467ce0123e5f5ade6138
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2020
ms.locfileid: "85129914"
---
# <a name="multiple-windows-for-ipad"></a>Varias ventanas para iPad

iOS 13 ahora admite Windows en paralelo para la misma aplicación en iPad. Esto permite nuevas experiencias y interacciones de arrastrar y colocar entre ventanas. En este documento se muestra cómo configurar la aplicación para admitir esta característica y se presentan estas nuevas características. 

## <a name="project-configuration"></a>Configuración del proyecto

Para configurar el proyecto para varias ventanas, modifique la `info.plist` instrucción para que `NSUserActivityTypes` indique a iOS que la aplicación controlará las actividades de este tipo y `UIApplicationSceneManifest` para habilitar `UIApplicationSupportsMultipleScenes` varias ventanas y `UISceneConfigurations` asociar la escena a un guion gráfico.

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>com.xamarin.Gallery.openDetail</string>
</array>
<key>UIApplicationSceneManifest</key>
<dict>
    <key>UIApplicationSupportsMultipleScenes</key>
    <true/>
    <key>UISceneConfigurations</key>
    <dict>
        <key>UIWindowSceneSessionRoleApplication</key>
        <array>
            <dict>
                <key>UISceneConfigurationName</key>
                <string>Default Configuration</string>
                <key>UISceneDelegateClassName</key>
                <string>SceneDelegate</string>
                <key>UISceneStoryboardFile</key>
                <string>Main</string>
            </dict>
        </array>
    </dict>
</dict>
```

## <a name="opening-multiple-windows"></a>Abrir varias ventanas

Con la aplicación abierta y en ejecución en un iPad, hay algunas maneras de abrir varias ventanas de esa aplicación que los identificadores de iOS son predeterminados.

- **Aplicación expuesta** : Pulse el icono de la aplicación en el Dock para entrar en este modo mientras la aplicación está abierta.
- **Deslizar sobre** : arrastre el icono de la aplicación desde el acoplamiento en la parte superior de la aplicación en ejecución para tener una ventana flotante.
- **Dividir pantalla** : arrastre el icono de la aplicación desde el Dock al borde de la pantalla de iPad para crear una nueva ventana en paralelo.

Las interacciones adicionales para entrar en un modo de varias ventanas están disponibles en la aplicación.

**Arrastre desde la aplicación** : Use una interacción de arrastre dentro de la aplicación para iniciar un nuevo `NSUserActivity` igual que arrastrar el icono de la aplicación en ejemplos anteriores.

Cuando se usa una [interacción de arrastrar y colocar][0], se crea `NSUserActivity` y se asocian los datos que se van a pasar a la nueva ventana que se solicita a iOS que se abra automáticamente.

```csharp
public UIDragItem [] GetItemsForBeginningDragSession (UICollectionView collectionView, IUIDragSession session, NSIndexPath indexPath)
{
    var selectedPhoto = GetPhoto (indexPath);

    var userActivity = selectedPhoto.OpenDetailUserActivity ();
    var itemProvider = new NSItemProvider (UIImage.FromFile (selectedPhoto.Name));
    itemProvider.RegisterObject (userActivity, NSItemProviderRepresentationVisibility.All);

    var dragItem = new UIDragItem (itemProvider) {
        LocalObject = selectedPhoto
    };

    return new [] { dragItem };
}
```

En el código anterior, el `selectedPhoto` objeto de modelo tiene un método para devolver un `NSUserActivity` llamado `OpenDetailUserActivity()` . Cuando se completa el movimiento de arrastre, el `UIDragItem` con presenta `userActivity` a través de `NSItemProvider` .

**Acciones explícitas** : los gestos de usuario en botones o vínculos ofrecen la posibilidad de abrir una nueva ventana.

Desde el `UIApplication` , puede iniciar un nuevo `UISceneSession` mediante una llamada a `RequestSceneSessionActivation` . Si ya existe una escena existente, debe utilizarla. De forma predeterminada, se creará una nueva escena.

```csharp
public void ItemSelected(UICollectionView collectionView, NSIndexPath indexPath)
{
    var userActivity = selectedPhoto.OpenDetailUserActivity ();

    UIApplication.SharedApplication.RequestSceneSessionActivation(
        sceneSession: null,
        userActivity: userActivity,
        options: null,
        errorHandler: null
    );
}
```

En este ejemplo, `userActivity` es el único valor que se pasa al `RequestSceneSessionActivation` método para abrir una nueva ventana de la aplicación basándose en una acción explícita del usuario; en este caso, un `ItemSelected` controlador de `UICollectionView` .

## <a name="related-links"></a>Vínculos relacionados

- [Arrastrar y colocar en Xamarin. iOS][0]

[0]: ~/ios/platform/introduction-to-ios11/drag-and-drop.md
