---
title: Trabajar con la navegación en Xamarin de watchOS
description: Este documento describe cómo trabajar con la navegación en una aplicación para watchOS. Describe las interfaces modales, navegación jerárquica y interfaces basadas en la página.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: e0c70418849bdf394a00cd5f53744b0c01bc5794
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69889620"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Trabajar con la navegación en Xamarin de watchOS

La opción de navegación más simple disponible en el reloj es una sencilla [emergente modal](#modal) que aparece encima de la escena actual.

Para varios escena watch hay aplicaciones son dos paradigmas de exploración disponibles:

- [Navegación jerárquica](#Hierarchical_Navigation)
- [Interfaces basadas en la página](#Page-Based_Interfaces)

<a name="modal"/>

## <a name="modal-interfaces"></a>Interfaces modales

Use el `PresentController` método para abrir un controlador de interfaz de forma modal. El controlador de interfaz debe estar ya definido en el **Interface.storyboard**.

```csharp
PresentController ("pageController","some context info");
```

Los controladores de forma modal presentan utilizan toda la pantalla (que trata de la escena anterior). De forma predeterminada, el título se establece en **cancelar** y pulsando en él, se cerrará el controlador.

Para cerrar mediante programación el controlador presenta de forma modal, llame a `DismissController`.

```csharp
DismissController();
```

Las pantallas modales pueden ser una sola escena o usar un diseño basado en la página.

<a name="Hierarchical_Navigation"/>

## <a name="hierarchical-navigation"></a>Navegación jerárquica

Segundo plano como una pila que se puede navegar hacia atrás por, de forma similar a la forma en que se presenta `UINavigationController` funciona en iOS. Segundo plano puede inserta en la pila de navegación y extrae (ya sea mediante programación o mediante la selección del usuario).

![](navigation-images/hierarchy-1.png "Plano se puede insertar en la pila de navegación") ![](navigation-images/hierarchy-2.png "plano puede extrae de la pila de navegación")

Al igual que con iOS, un deslizamiento de edge izquierda se desplaza hasta el controlador primario en una pila de navegación jerárquica.

Tanto el [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) y [WatchTables](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables) ejemplos incluyen la navegación jerárquica.

### <a name="pushing-and-popping-in-code"></a>Insertando e incluyendo en el código

Vea el Kit no requiere un "controlador de navegación" cobros crearse como iOS - simplemente insertar un controlador con el `PushController` método y una pila de navegación se creará automáticamente.

```csharp
PushController("secondPageController","some context info");
```

Pantalla de inspección incluirá un **Atrás** botón en la esquina superior izquierda, pero puede quitar mediante programación una escena de la pila de navegación usando `PopController`.

```csharp
PopController();
```

Como con iOS, también es posible volver a la raíz de la pila de navegación usando `PopToRootController`.

```csharp
PopToRootController();
```

### <a name="using-segues"></a>Uso de los objetos Segue

Los objetos Segue se pueden crear entre bastidores en el guión gráfico para definir la navegación jerárquica. Para obtener el contexto de la escena de destino, las llamadas al sistema operativo `GetContextForSegue` para inicializar el nuevo controlador de interfaz.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```

<a name="Page-Based_Interfaces"/>

## <a name="page-based-interfaces"></a>Interfaces basadas en la página

Deslice el dedo interfaces basadas en la página de izquierda a derecha, similar a la forma `UIPageViewController` funciona en iOS. Puntos de indicador se muestran en la parte inferior de la pantalla para mostrar la página que se muestra actualmente.

![](navigation-images/paged-1.png "Primera página de ejemplo") ![](navigation-images/paged-2.png "segunda página de ejemplo") ![](navigation-images/paged-5.png "quinta página de ejemplo")


Para que una interfaz basada en la página de la interfaz de usuario principal para la aplicación del reloj, utilice `ReloadRootControllers` con una matriz de los controladores de interfaz y contextos:

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

También puede presentar un controlador basado en la página que no es la raíz con `PresentController` desde uno de los otros bastidores en una aplicación.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```



## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (ejemplo)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)
