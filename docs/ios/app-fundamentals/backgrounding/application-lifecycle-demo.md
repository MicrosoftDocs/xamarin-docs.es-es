---
title: Demostración del ciclo de vida de la aplicación para Xamarin. iOS
description: En este documento se examinan varios eventos del ciclo de vida administrados por el delegado de la aplicación en una aplicación de iOS, que muestra Cuándo y cómo se administran estos eventos.
ms.prod: xamarin
ms.assetid: 5C8AACA6-49F8-4C6D-99C3-5F443C01B230
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/17/2018
ms.openlocfilehash: 08ef81f55ac1705224f2953197bfe582718b9cf7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432516"
---
# <a name="application-lifecycle-demo-for-xamarinios"></a>Demostración del ciclo de vida de la aplicación para Xamarin. iOS

En este artículo y el [código de ejemplo](/samples/xamarin/ios-samples/lifecycledemo) se muestran los cuatro Estados de aplicación en iOS y el rol de los `AppDelegate` métodos en notificar a la aplicación Cuándo se cambia el estado. La aplicación imprimirá actualizaciones en la consola cada vez que cambie el estado de la aplicación:

[![La aplicación de ejemplo](application-lifecycle-demo-images/image3-sml.png)](application-lifecycle-demo-images/image3.png#lightbox)

[![La aplicación imprimirá actualizaciones en la consola cada vez que cambie el estado de la aplicación.](application-lifecycle-demo-images/image4.png)](application-lifecycle-demo-images/image4.png#lightbox)

## <a name="walkthrough"></a>Tutorial

1. Abra el proyecto del **ciclo de vida** en la solución **LifecycleDemo** .
1. Abra la `AppDelegate` clase. Se ha agregado el registro a los métodos del ciclo de vida para indicar cuándo ha cambiado el estado de la aplicación:

    ```csharp
    public override void OnActivated(UIApplication application)
    {
        Console.WriteLine("OnActivated called, App is active.");
    }
    public override void WillEnterForeground(UIApplication application)
    {
        Console.WriteLine("App will enter foreground");
    }
    public override void OnResignActivation(UIApplication application)
    {
        Console.WriteLine("OnResignActivation called, App moving to inactive state.");
    }
    public override void DidEnterBackground(UIApplication application)
    {
        Console.WriteLine("App entering background state.");
    }
    // not guaranteed that this will run
    public override void WillTerminate(UIApplication application)
    {
        Console.WriteLine("App is terminating.");
    }
    ```

1. Inicie la aplicación en el simulador o en el dispositivo. `OnActivated` se llamará cuando se inicie la aplicación. La aplicación está ahora en estado _activo_ .
1. Presione el botón Inicio en el simulador o el dispositivo para poner la aplicación en segundo plano. `OnResignActivation` y se `DidEnterBackground` llamará cuando la aplicación pase de `Active` a y al `Inactive` `Backgrounded` Estado. Puesto que no hay ningún código de aplicación establecido para ejecutarse en segundo plano, la aplicación se considera _suspendida_ en la memoria.
1. Vuelva a la aplicación para volver a colocarla en primer plano. `WillEnterForeground` se `OnActivated` llamará a y a ambos:

    ![Cambios de estado impresos en la consola](application-lifecycle-demo-images/image4.png)

    La siguiente línea de código en el controlador de vista se ejecuta cuando la aplicación ha entrado en primer plano desde el fondo y cambia el texto que se muestra en la pantalla:

    ```csharp
    UIApplication.Notifications.ObserveWillEnterForeground ((sender, args) => {
        label.Text = "Welcome back!";
    });
    ```

1. Presione el botón **Inicio** para colocar la aplicación en segundo plano. A continuación, pulse dos veces el botón **Inicio** para abrir el conmutador de la aplicación. En el iPhone X, deslice el dedo hacia arriba desde la parte inferior de la pantalla:

    [![El conmutador de aplicación](application-lifecycle-demo-images/app-switcher-sml.png "El conmutador de aplicación")](application-lifecycle-demo-images/app-switcher.png#lightbox)
  
1. Busque la aplicación en el conmutador de aplicaciones y deslice el dedo hacia arriba para quitarla (en iOS 11, presione largo hasta que aparezcan los iconos rojos en la esquina):

    [![Deslizar rápidamente para quitar una aplicación en ejecución](application-lifecycle-demo-images/app-switcher-swipe-sml.png "Deslizar rápidamente para quitar una aplicación en ejecución")](application-lifecycle-demo-images/app-switcher-swipe.png#lightbox)

iOS finalizará la aplicación. Tenga en cuenta que `WillTerminate` no se llama a porque la aplicación ya está _suspendida_ en segundo plano.

## <a name="related-links"></a>Vínculos relacionados

- [LifecycleDemo (ejemplo)](/samples/xamarin/ios-samples/lifecycledemo)