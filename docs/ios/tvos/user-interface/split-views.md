---
title: Trabajar con controladores de vista en dos paneles tvOS en Xamarin
description: En este documento se describe cómo trabajar con las vistas de división de tvOS en una aplicación compilada con Xamarin. Proporciona información general de alto nivel sobre los controladores de vista en dos paneles, cómo usarlos con guiones gráficos, el acceso a las vistas maestra y de detalle, y cómo mostrar y ocultar la vista maestra.
ms.prod: xamarin
ms.assetid: 21248CFB-5A94-4C19-B223-C72E0DC5F1D5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 52160d11c8c17a67a5db92e6a95f94815d54a0a6
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648928"
---
# <a name="working-with-tvos-split-view-controllers-in-xamarin"></a>Trabajar con controladores de vista en dos paneles tvOS en Xamarin

Un controlador de vista en dos paneles presenta y administra un controlador de vista principal y de detalles en paralelo, en la pantalla al mismo tiempo. Los controladores de vista en dos paneles se usan para presentar contenido persistente y enfocable en la vista maestra (la sección más pequeña a la izquierda) y los detalles relacionados en la vista de detalle (la sección más grande de la derecha).

[![](split-views-images/intro01.png "Vista de división de ejemplo")](split-views-images/intro01.png#lightbox)

<a name="About-Split-View-Controllers" />

## <a name="about-split-view-controllers"></a>Acerca de los controladores de vista en dos paneles

Como se indicó anteriormente, un controlador de vista en dos paneles administra un controlador de vista principal y de detalles que se presenta en paralelo, y el maestro es la vista más pequeña de la izquierda, el detalle más grande a la derecha. 

Además, el controlador de vista maestra se puede ocultar o mostrar según sea necesario: 

[![](split-views-images/intro02.png "El controlador de vista principal está oculto")](split-views-images/intro02.png#lightbox)

Los controladores de vistas divididas suelen usarse para presentar una lista de contenido filtrable, con las categorías de la vista maestra y los resultados filtrados en la vista de detalle. Normalmente, se presenta como una vista de tabla a la izquierda y una [vista de colección](~/ios/tvos/user-interface/collection-views.md) a la derecha.

Al diseñar una interfaz de usuario que requiere un controlador de vista en dos paneles, Apple sugiere el uso de controladores de vista maestra y de detalles que no cambian (solo los cambios de contenido, no la estructura). Si necesita intercambiar controladores de vista, es mejor usar un controlador de navegación como base del controlador de vista que debe cambiar (maestra o detalle).

Apple tiene las siguientes sugerencias para trabajar con controladores de vista en dos paneles:

- **Usar el porcentaje de división correcto** : de forma predeterminada, el controlador de vista en dos paneles usa un tercio de la pantalla para el controlador de vista maestro y dos tercios para el controlador de vista de detalle. Opcionalmente, puede usar una división 50/50. Elija el porcentaje correcto para que parezca que el contenido está equilibrado en la pantalla.
- **Conservar la selección principal** : aunque el contenido de la vista de detalle puede cambiar es respuesta a la selección de un usuario en la vista maestra, el contenido de la vista maestra debe corregirse. Además, debe mostrar claramente el elemento seleccionado actualmente en la vista maestra.
- **Usar un único título unificado** : por lo general, querrá usar un solo título centrado en la vista de detalle, en lugar de un título en la vista de detalle y la vista maestra.

<a name="Split-View-Controllers-and-Storyboards" />

## <a name="split-view-controllers-and-storyboards"></a>Guiones gráficos y controladores de vista en dos paneles

La manera más sencilla de trabajar con controladores de vista en dos paneles en una aplicación Xamarin. tvOS es agregarlos a la interfaz de usuario de la aplicación mediante el diseñador de iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. En el **Panel de solución**, haga doble clic en `Main.storyboard` el archivo y ábralo para su edición.
1. Arrastre un **controlador de vista en dos paneles** desde el **cuadro de herramientas** y colóquelo en la vista: 

    [![](split-views-images/activity01.png "Un controlador de vista en dos paneles")](split-views-images/activity01.png#lightbox)
1. De forma predeterminada, iOS Designer instalará un controlador de navegación y un controlador de vista en la vista maestra. Si esto no se ajusta a los requisitos de la aplicación, simplemente elimínelos.
1. Si quita la vista maestra predeterminada, arrastre un nuevo controlador de vista a la superficie de diseño: 

    [![](split-views-images/activity02.png "Un controlador de vista")](split-views-images/activity02.png#lightbox)
1. Control: haga clic y arrastre desde el controlador de vista en dos paneles al nuevo controlador de vista maestra. 
1. Seleccione **maestra** en el **menú emergente**: 

    [![](split-views-images/activity03.png "Seleccionar maestro en el menú emergente")](split-views-images/activity03.png#lightbox)
1. Diseñe el contenido de las vistas principal y de detalles: 

    [![](split-views-images/activity04.png "Diseño de ejemplo")](split-views-images/activity04.png#lightbox)
1. Asigne **nombres** en la **pestaña widget** del **Panel de propiedades** para trabajar con los controles de interfaz de C# usuario en el código.
1. Guarde los cambios y vuelva a Visual Studio para Mac.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. En el **Explorador de soluciones**, haga doble clic en `Main.storyboard` el archivo y ábralo para su edición.
1. Arrastre un **controlador de vista en dos paneles** desde el **cuadro de herramientas** y colóquelo en la vista: 

    [![](split-views-images/activity01-vs.png "Un controlador de vista en dos paneles")](split-views-images/activity01-vs.png#lightbox)
1. De forma predeterminada, el diseñador de iOS agregará un controlador de navegación y un controlador de vista en la vista maestra. Si esto no se ajusta a los requisitos de la aplicación, simplemente elimínelos.
1. Si quita la vista maestra predeterminada, arrastre un nuevo controlador de vista a la superficie de diseño: 

    [![](split-views-images/activity02-vs.png "Un controlador de vista")](split-views-images/activity02-vs.png#lightbox)
1. Control: haga clic y arrastre desde el controlador de vista en dos paneles al nuevo controlador de vista maestra. 
1. Seleccione **maestra** en el **menú emergente**: 

    [![](split-views-images/activity03-vs.png "Seleccionar maestro en el menú emergente")](split-views-images/activity03-vs.png#lightbox)
1. Diseñe el contenido de las vistas principal y de detalles: 

    [![](split-views-images/activity04.png "Diseño de contenido")](split-views-images/activity04.png#lightbox)
1. Asigne **nombres** en la **pestaña widget** del **Explorador de propiedades** para trabajar con los controles de interfaz C# de usuario en el código.
1. Guarde los cambios.
    
-----

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Guía de inicio rápido Hola, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Working-with-Split-View-Controllers" />

## <a name="working-with-split-view-controllers"></a>Trabajar con controladores de vista en dos paneles

Como se indicó anteriormente, un controlador de vista en dos paneles se suele usar en situaciones en las que se muestra el contenido filtrado al usuario. Las categorías principales se muestran a la izquierda en la vista maestra y los resultados filtrados a la derecha en la vista de detalle en función de la selección del usuario.

<a name="Accessing-Master-and-Detail" />

### <a name="accessing-master-and-detail"></a>Obtener acceso a maestro y detalles

Si necesita tener acceso a los controladores de vista maestra y de detalles mediante programación, `ViewControllers` utilice la propiedad del controlador de vista en dos paneles. Por ejemplo:

```csharp
// Gain access to master and detail view controllers
var masterController = ViewControllers [0] as MasterViewController;
var detailController = ViewControllers [1] as DetailViewController;
```

Se presenta como una matriz, donde el primer elemento (0) del controlador de vista principal y el segundo elemento (1) son los detalles.

<a name="Accessing-Detail-from-Master" />

### <a name="accessing-detail-from-master"></a>Obtener acceso a detalles desde la página maestra

Puesto que normalmente se muestra información detallada en la vista de detalle en función de la selección del usuario en la base de datos maestra, necesitará una manera de tener acceso a los detalles del maestro.

La forma más fácil de hacerlo es exponer una propiedad en la clase de controlador de vista maestra, por ejemplo:

```csharp
public DetailViewController DetailController { get; set;}
```

En el controlador de vista en dos paneles `ViewDidLoad` , invalide el método y asocie las dos vistas. Por ejemplo:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Gain access to master and detail view controllers
    var masterController = ViewControllers [0] as MasterViewController;
    var detailController = ViewControllers [1] as DetailViewController;

    // Wire-up views
    masterController.SplitViewController = this;
    masterController.DetailController = detailController;
    detailController.SplitViewController = this;
}
```

Puede exponer propiedades y métodos en el controlador de vista de detalle que el maestro pueda utilizar para presentar datos nuevos según sea necesario.

<a name="Showing-and-Hiding-Master" />

### <a name="showing-and-hiding-master"></a>Mostrar y ocultar maestro

Opcionalmente, puede mostrar y ocultar el controlador de vista maestra mediante la `PreferredDisplayMode` propiedad del controlador de vista en dos paneles. Por ejemplo:

```csharp
// Show hide split view
if (SplitViewController.DisplayMode == UISplitViewControllerDisplayMode.PrimaryHidden) {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.AllVisible;
} else {
    SplitViewController.PreferredDisplayMode = UISplitViewControllerDisplayMode.PrimaryHidden;
}
```

La `UISplitViewControllerDisplayMode` enumeración define cómo se presentará el controlador de vista principal como uno de los siguientes:

- **Automatic** -tvOS controlará la presentación de las vistas principal y de detalle.
- **PrimaryHidden** : oculta el controlador de vista maestra.
- **AllVisible** : muestra los controladores principal y de vista de detalle en paralelo. Esta es la presentación normal y predeterminada.
- **PrimaryOverlay** : el controlador de vista de detalle se extiende en y está incluido en el maestro.

Para obtener el estado de presentación actual, use `DisplayMode` la propiedad del controlador de vista en dos paneles.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha tratado el diseño y el trabajo con controladores de vista en dos paneles en una aplicación Xamarin. tvOS.



## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
