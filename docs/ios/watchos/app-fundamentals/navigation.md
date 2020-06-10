---
title: Trabajar con la navegación de watchos en Xamarin
description: En este documento se describe cómo trabajar con la navegación en una aplicación de watchos. Describe las interfaces modales, la navegación jerárquica y las interfaces basadas en páginas.
ms.prod: xamarin
ms.assetid: 71A64C10-75C8-4159-A547-6A704F3B5C2E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 1ad4ecad90238436f8d2a02727596186c6205eeb
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2020
ms.locfileid: "84572096"
---
# <a name="working-with-watchos-navigation-in-xamarin"></a>Trabajar con la navegación de watchos en Xamarin

La opción de navegación más sencilla disponible en el reloj es un [elemento emergente modal](#modal) sencillo que aparece encima de la escena actual.

En el caso de las aplicaciones de inspección de varias escenas hay dos paradigmas de navegación disponibles:

- [Navegación jerárquica](#Hierarchical_Navigation)
- [Interfaces basadas en páginas](#Page-Based_Interfaces)

<a name="modal"></a>

## <a name="modal-interfaces"></a>Interfaces modales

Use el `PresentController` método para abrir un controlador de interfaz de forma modal. El controlador de interfaz ya debe estar definido en la **interfaz. Storyboard**.

```csharp
PresentController ("pageController","some context info");
```

Los controladores presentados de forma modal usan toda la pantalla (que abarca la escena anterior). De forma predeterminada, el título se establece en **Cancelar** y al pulsar se descartará el controlador.

Para cerrar mediante programación el controlador presentado de forma modal, llame a `DismissController` .

```csharp
DismissController();
```

Las pantallas modales pueden ser una sola escena o usar un diseño basado en páginas.

<a name="Hierarchical_Navigation"></a>

## <a name="hierarchical-navigation"></a>Navegación jerárquica

Presenta escenas como una pila de la que se puede navegar de nuevo, de forma similar a como `UINavigationController` funciona en iOS. Las escenas se pueden insertar en la pila de navegación y se extraen (mediante programación o mediante la selección del usuario).

![](navigation-images/hierarchy-1.png "Las escenas se pueden insertar en la pila de navegación") ![](navigation-images/hierarchy-2.png "Las escenas se pueden extraer de la pila de navegación")

Al igual que con iOS, un deslizador a la izquierda navega hacia el controlador primario en una pila de navegación jerárquica.

Los ejemplos [WatchKitCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog) y [WatchTables](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchtables) incluyen navegación jerárquica.

### <a name="pushing-and-popping-in-code"></a>Insertar y extraer código

El kit de inspección no requiere la creación de un "controlador de navegación" para crearse como iOS: simplemente inserte un controlador mediante el `PushController` método y se creará automáticamente una pila de navegación.

```csharp
PushController("secondPageController","some context info");
```

La pantalla del reloj incluirá un botón **atrás** en la parte superior izquierda, pero también puede quitar mediante programación una escena de la pila de navegación usando `PopController` .

```csharp
PopController();
```

Al igual que con iOS, también es posible volver a la raíz de la pila de navegación mediante `PopToRootController` .

```csharp
PopToRootController();
```

### <a name="using-segues"></a>Usar objetos segue

Objetos segue se puede crear entre bastidores en el guión gráfico para definir la navegación jerárquica. Para obtener el contexto de la escena de destino, el sistema operativo llama `GetContextForSegue` a para inicializar el nuevo controlador de interfaz.

```csharp
public override NSObject GetContextForSegue (string segueIdentifier)
{
  if (segueIdentifier == "mySegue") {
    return new NSString("some context info");
  }
  return base.GetContextForSegue (segueIdentifier);
}
```

<a name="Page-Based_Interfaces"></a>

## <a name="page-based-interfaces"></a>Interfaces basadas en páginas

Las interfaces basadas en páginas se deslizan de izquierda a derecha, de forma similar a como `UIPageViewController` funciona en iOS. Los puntos de indicador se muestran en la parte inferior de la pantalla para mostrar la página que se muestra actualmente.

![](navigation-images/paged-1.png "Primera página de ejemplo") ![](navigation-images/paged-2.png "Segunda página de ejemplo") ![](navigation-images/paged-5.png "Quinta página de ejemplo")

Para convertir una interfaz basada en páginas en la interfaz de usuario principal de la aplicación de inspección, use `ReloadRootControllers` con una matriz de controladores de interfaz y contextos:

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
ReloadRootControllers (controllerNames, contexts);
```

También puede presentar un controlador basado en páginas que no sea la raíz usando `PresentController` de una de las otras escenas en una aplicación.

```csharp
var controllerNames = new [] { "pageController", "pageController", "pageController", "pageController", "pageController" };
var contexts = new [] { "First", "Second", "Third", "Fourth", "Fifth" };
PresentController (controllerNames, contexts);
```

## <a name="related-links"></a>Vínculos relacionados

- [WatchKitCatalog (ejemplo)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WatchTables (ejemplo)](https://developer.xamarin.com//samples/monotouch/watchOS/WatchTables/)
