---
title: Trabajar con controladores de barra de pestañas de tvOS en Xamarin
description: En este documento se describe cómo trabajar con controladores de barra de pestañas en una aplicación tvOS compilada con Xamarin. Proporciona un alto nivel sobre la vista de barras de pestañas y describe los elementos de la barra de pestañas, la integración de guiones gráficos y los elementos de barra de pestañas.
ms.prod: xamarin
ms.assetid: 99A2D7C6-0324-4DE5-B6E9-D39D0BAD8370
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 64b114cad16095a2afd80b690a4654b91b2aa203
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199942"
---
# <a name="working-with-tvos-tab-bar-controllers-in-xamarin"></a>Trabajar con controladores de barra de pestañas de tvOS en Xamarin

Para muchos tipos de aplicaciones de tvOS, la navegación principal se presenta como una barra de pestañas que se ejecuta en la parte superior de la pantalla. El usuario desliza el dedo a la izquierda y a la derecha en la lista de categorías posibles y el área de contenido debajo de los cambios para reflejar la selección del usuario.

[![](tab-bars-images/tab01.png "Barra de pestañas de ejemplo")](tab-bars-images/tab01.png#lightbox)

La barra de pestañas es traslúcida de forma predeterminada y siempre aparece en la parte superior de la pantalla. Cuando está en el foco, una barra de pestañas cubrirá los primeros 140 píxeles de la pantalla, pero se desplazará rápidamente cuando el foco se desplace al área de contenido siguiente.

<a name="Tab-Bars-in-tvOS" />

## <a name="tab-bars-in-tvos"></a>Barras de pestañas en tvOS

`UITabViewController` Funciona de forma similar y sirve para un propósito similar en tvOS, como en iOS, con las siguientes diferencias clave:

- A diferencia de la barra de pestañas de iOS que aparece en la parte inferior de la pantalla, las barras de pestañas de tvOS ocupan los primeros 140 píxeles de la pantalla y son translúcidas de forma predeterminada.
- Cuando el foco sale de la barra de pestañas para el área de contenido, la barra de pestañas se desliza rápidamente sobre la parte superior de la pantalla y se oculta. El usuario puede pulsar el botón de menú una vez o deslizar el dedo hacia arriba en el [control remoto Siri](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote) para volver a mostrar la barra de pestañas.
- Al deslizar hacia abajo en el control remoto de Siri, el foco se centra en el área de contenido debajo de la barra de pestañas hasta el primer [elemento enfocable](~/ios/tvos/app-fundamentals/navigation-focus.md#Focus-and-Selection) en el contenido que se muestra. De nuevo, se ocultará la barra de pestañas una vez que el foco cambie.
- Al hacer clic para seleccionar una categoría mostrada en la barra de pestañas se cambiará al contenido de esa categoría y el foco se cambiará al primer elemento que pueda recibir el foco en esa vista.
- El número de categorías que se muestran en la barra de pestañas debe ser fijo y todas las categorías deben ser accesibles en todo momento, una categoría determinada nunca debe estar deshabilitada.
- Las barras de pestañas no admiten la personalización en tvOS. Además, no muestran la categoría **más** (como iOS) si hay más categorías de las que caben en la barra de pestañas.

Apple tiene las siguientes sugerencias para trabajar con barras de pestañas:

- **Usar barras de pestañas para organizar el contenido lógicamente** : Use la barra de pestañas para organizar lógicamente el contenido con el que funciona la aplicación tvOS. Por ejemplo, gráficos destacados, comprados y de búsqueda.
- **Agregar distintivos para informar a los usuarios del nuevo contenido** : opcionalmente, puede mostrar un distintivo (un óvalo rojo con un número blanco o un signo de exclamación) para informar al usuario del nuevo contenido de una categoría.
- **Use** los distintivos con moderación: no se amontona la barra de pestañas con distintivos y solo se muestran los datos que proporcionan información crítica al usuario.
- **Limite el número de categorías** : para reducir la complejidad y mantener la aplicación administrable, no sobrecargue la barra de pestañas con categorías y asegúrese de que todas las categorías estén visibles y no se llenarán. Los títulos sencillos y cortos funcionan mejor.
- **No deshabilitar una categoría** : todas las pestañas (categorías) siempre deberían estar visibles y habilitadas en todo momento. Si una pestaña determinada no tiene contenido, proporcione una explicación al usuario por qué. Por ejemplo, la pestaña compras estará vacía si el usuario no ha realizado ninguna compra.

<a name="Tab-Bar-Items" />

## <a name="tab-bar-items"></a>Elementos de la barra de pestañas

Cada categoría (pestaña) de la barra de pestañas se representa mediante un elemento de`UITabBarItem`barra de tabulación (). Apple tiene las siguientes sugerencias para trabajar con elementos de barra de pestañas:

- **Usar pestañas basadas en texto** : aunque el elemento de barra de pestañas se puede representar como un icono, Apple sugiere el uso de texto solo porque un título conciso es más fácil de interpretar que un icono.
- **Usar nombres o verbos cortos y significativos** : un elemento de barra de pestañas debe retransmitir claramente el contenido que contiene y funciona mejor cuando se trata de un nombre sencillo (como fotos, películas o música) o verbos (como buscar o reproducir).

<a name="Tab-Bars-and-Storyboards" />

## <a name="tab-bars-and-storyboards"></a>Barras de tabulación y guiones gráficos

La manera más sencilla de trabajar con barras de pestañas en una aplicación de Xamarin. tvOS es agregarlas a la interfaz de usuario de la aplicación mediante el diseñador de iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio para Mac](#tab/macos)

1. Inicie una nueva aplicación de Xamarin. tvOS y > seleccione aplicación con**pestañas**de **tvOS** > : 

    [![](tab-bars-images/tab02.png "Seleccionar aplicación con pestañas")](tab-bars-images/tab02.png#lightbox)
1. Siga todas las indicaciones para crear una nueva solución de Xamarin. tvOS.
1. En el **Panel de solución**, haga doble clic en `Main.storyboard` el archivo y ábralo para su edición.
1. Para cambiar el **icono** o el **título** de una categoría determinada, seleccione el **elemento de barra** de pestañas del **controlador de vista** en el **esquema del documento**:

    [![](tab-bars-images/tab03a.png "El elemento de barra de pestañas del controlador de vistas en el esquema del documento")](tab-bars-images/tab03a.png#lightbox)
1. A continuación, establezca las propiedades necesarias en la **pestaña widget** del **Explorador de propiedades**: 

    [![](tab-bars-images/tab03.png "La pestaña widget")](tab-bars-images/tab03.png#lightbox)
1. Para agregar una nueva categoría (pestaña), coloque un **controlador de vista** en la superficie de diseño: 

    [![](tab-bars-images/tab04.png "Un controlador de vista")](tab-bars-images/tab04.png#lightbox)
1. Control: haga clic y arrastre desde el **controlador de vista de pestaña** al nuevo controlador de **vista**.
1. En el menú emergente, seleccione **Ver controladores** para agregar la nueva vista como una pestaña (categoría): 

    [![](tab-bars-images/tab05.png "Seleccionar pestaña")](tab-bars-images/tab05.png#lightbox)
1. Diseñe el diseño de la interfaz de usuario para cada área de contenido de Caterogies como de costumbre, agregando elementos de interfaz de usuario en el diseñador de iOS.
1. Exponga los eventos necesarios para trabajar con los controles de interfaz C# de usuario en el código.
1. Asigne un nombre a todos los controles de interfaz de C# usuario que desee exponer en el código.
1. Guarde los cambios.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Inicie una nueva aplicación de Xamarin. tvOS y > seleccione aplicación con**pestañas**de **tvOS** > : 

    [![](tab-bars-images/tab02vs.png "Seleccionar aplicación con pestañas")](tab-bars-images/tab02vs.png#lightbox)
1. Siga todas las indicaciones para crear una nueva solución de Xamarin. tvOS.
1. En el **Explorador de soluciones**, haga doble clic en `Main.storyboard` el archivo y ábralo para su edición.
1. Para cambiar el **icono** o el **título** de una categoría determinada, seleccione el **elemento de barra** de pestañas del **controlador de vista** en el **esquema del documento**:

    [![](tab-bars-images/tab03avs.png "El controlador de vista en el esquema del documento")](tab-bars-images/tab03avs.png#lightbox)
1. A continuación, establezca las propiedades necesarias en la **pestaña widget** del **Explorador de propiedades**: 

    [![](tab-bars-images/tab03vs.png "La pestaña widget")](tab-bars-images/tab03vs.png#lightbox)
1. Para agregar una nueva categoría (pestaña), arrastre un **controlador de vista** desde el **cuadro de herramientas** y colóquelo en la superficie de diseño: 

    [![](tab-bars-images/tab04vs.png "Un controlador de vista")](tab-bars-images/tab04vs.png#lightbox)
1. Control: haga clic y arrastre desde el **controlador de vista de pestaña** al nuevo controlador de **vista**.
1. En el menú emergente, seleccione **Ver controladores** para agregar la nueva vista como una pestaña (categoría): 

    [![](tab-bars-images/tab05vs.png "Seleccionar pestaña")](tab-bars-images/tab05vs.png#lightbox)
1. Diseñe el diseño de la interfaz de usuario para cada área de contenido de Caterogies como de costumbre, agregando elementos de interfaz de usuario en el diseñador de iOS.
1. Exponga los eventos necesarios para trabajar con los controles de interfaz C# de usuario en el código.
1. Asigne un nombre a todos los controles de interfaz de C# usuario que desee exponer en el código.
1. Guarde los cambios.

-----

> [!IMPORTANT]
> Aunque es posible asignar eventos como `TouchUpInside` a un elemento de la interfaz de usuario (como un `UIButton`) en el diseñador de iOS, nunca se llamará porque Apple TV no tiene una pantalla táctil ni eventos táctiles de soporte técnico. Siempre debe usar el `Primary Action` evento al crear controladores de eventos para los elementos de la interfaz de usuario de tvOS.

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Guía de inicio rápido Hola, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Tab-Bars" />

## <a name="working-with-tab-bars"></a>Trabajar con barras de pestañas

Utilice la `Items` propiedad `UITabBar` de para tener acceso a la colección `UITabBarItems` de que contiene como una matriz indizada de cero (0). La `SelectedItem` propiedad devolverá la pestaña (categoría) seleccionada actualmente `UITabBarItem`como.


<a name="Working-with-Tab-Bar-Items" />

## <a name="working-with-tab-bar-items"></a>Trabajar con elementos de barra de pestañas

Para mostrar un distintivo en una pestaña determinada (un óvalo rojo con texto blanco), use el código siguiente:

```csharp
// Display a badge
TabBar.Items [2].BadgeValue = "10";
```

Lo que produciría los siguientes resultados cuando se ejecutó:

[![](tab-bars-images/tab06.png "Un elemento de barra de pestañas con distintivo")](tab-bars-images/tab06.png#lightbox)

Utilice la `Title` propiedad `UITabBarItem` de para cambiar el título y la `Image` propiedad para cambiar el icono.

<a name="Summary" />

## <a name="summary"></a>Resumen

En este artículo se ha tratado el diseño y el uso del controlador de barra de pestañas dentro de una aplicación Xamarin. tvOS.




## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
