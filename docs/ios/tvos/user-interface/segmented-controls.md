---
title: Trabajar con controles segmentados de tvOS en Xamarin
description: En este documento se describe cómo trabajar con controles segmentados de tvOS en una aplicación compilada con Xamarin. Se describen los iconos de segmento y el texto, los eventos, la modificación de la apariencia del control, etc.
ms.prod: xamarin
ms.assetid: 23AD94CC-E93A-40B1-8E2B-ECD21FA355BE
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 47530b27fb754bf2efd50615fef9dfc558d9c7d2
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436187"
---
# <a name="working-with-tvos-segmented-controls-in-xamarin"></a>Trabajar con controles segmentados de tvOS en Xamarin

Un control segmentado proporciona un conjunto de elementos lineales, cada uno de los cuales puede contener un icono o texto, y se utiliza para proporcionar al usuario un conjunto de opciones relacionadas.

[![Controles de segmento de ejemplo](segmented-controls-images/segment01.png)](segmented-controls-images/segment01.png#lightbox)

Apple tiene las siguientes sugerencias para trabajar con controles segmentados:

- **Proporcione** una gran cantidad de espacio para proporcionar suficiente espacio entre otros elementos que puedan recibir el [foco](~/ios/tvos/app-fundamentals/navigation-focus.md) y un control segmentado. Un segmento individual se selecciona cuando tiene el foco (no cuando se hace clic en él) y el usuario puede cambiar los segmentos accidentalmente cuando realmente desea seleccionar otro elemento enfocable en el segmento actual.
- **Usar vistas divididas para el filtrado de contenido** : los controles segmentados no realizan buenas elecciones para el filtrado de contenido, ya que las vistas divididas se diseñaron para facilitar la navegación entre el contenido y los filtros.
- **Límite de siete segmentos** como máximo: debe intentar mantener el número máximo de segmentos por debajo de ocho (8), ya que es más fácil analizarlos desde la habitación del sofá y facilitar la navegación.
- **Usar el tamaño de contenido de segmento coherente** : todos los segmentos tienen el mismo ancho y, si es posible, debe intentar mantener el mismo tamaño del contenido de cada segmento. Esto no solo hace que los controles de segmento sean más agradables visualmente, pero facilita la lectura de un vistazo.
- **Evitar mezclar iconos y texto** : cada segmento individual puede contener un icono o texto, pero no ambos. Aunque es posible mezclar iconos y texto en el mismo control segmentado, debe evitarse.

<a name="About-Segment-Icons"></a>

## <a name="about-segment-icons"></a>Acerca de los iconos de segmento

Apple sugiere el uso de imágenes simples y reconocibles para iconos de segmento, como una lupa para la búsqueda. Los iconos demasiado complejos son difíciles de reconocer en una pantalla de TV en todo el salón, por lo que es mejor limitar los iconos a representaciones sencillas.

No puede mezclar texto e iconos en un segmento determinado y debe evitar mezclar iconos y texto en un solo control segmentado. Debe ser todos los iconos o todo el texto.

<a name="Summary"></a>

## <a name="segment-text"></a>Texto de segmento

Apple realiza las siguientes sugerencias para trabajar con texto de segmento:

- **Usar nombres cortos y significativos** : el título del segmento debe indicar claramente el tipo de contenido que el usuario debe esperar al seleccionar el segmento determinado. Por ejemplo: música o vídeos.
- Usar mayúsculas y **minúsculas** en el título: cada palabra del título de los segmentos debe escribirse en mayúsculas, excepto artículos, conjunciones y Preposiciones de menos de cuatro (4) caracteres.
- **Usar títulos cortos y centrados** : Mantenga los títulos, cortos y centrados en el tipo de contenido que se esperará cuando se seleccione el segmento.

De nuevo, no puede mezclar texto e iconos en un segmento determinado y debe evitar mezclar iconos y texto en un solo control segmentado.

<a name="Segment-Controls-and-Storyboards"></a>

## <a name="segment-controls-and-storyboards"></a>Controles de segmento y guiones gráficos

La manera más sencilla de trabajar con controles de segmento en una aplicación Xamarin. tvOS es agregarlos a la interfaz de usuario de la aplicación mediante el diseñador de iOS.

# <a name="visual-studio-for-mac"></a>[Visual Studio para Mac](#tab/macos)

1. En el **Panel de solución**, haga doble clic en el `Main.storyboard` archivo y ábralo para su edición.
1. Arrastre un **Control Segment** desde el **cuadro de herramientas** y colóquelo en la vista: 

    [![Un control de segmento](segmented-controls-images/segment02.png)](segmented-controls-images/segment02.png#lightbox)
1. En la **pestaña widget** del **Panel de propiedades**, puede ajustar varias propiedades del control de segmento, como su **estilo** y **Estado**: 

    [![La pestaña widget](segmented-controls-images/segment03.png)](segmented-controls-images/segment03.png#lightbox)
1. Use el campo **segmentos** para controlar el número de segmentos del controlador.
1. Seleccione un segmento determinado en la **lista desplegable segmento** para ajustar sus propiedades individuales, como **título** o **imagen** , y controlar si un segmento determinado está **habilitado** o **seleccionado** cuando se muestra el control.
1. Por último, asigne **nombres** a los controles para que pueda responder a ellos en código de C#. Por ejemplo: 

    [![Asignación de un nombre](segmented-controls-images/segment04.png)](segmented-controls-images/segment04.png#lightbox)
1. Guarde los cambios.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. En el **Explorador de soluciones**, haga doble clic en el `Main.storyboard` archivo y ábralo para su edición.
1. Arrastre un **Control Segment** desde el **cuadro de herramientas** y colóquelo en la vista: 

    [![Un control de segmento](segmented-controls-images/segment02-vs.png)](segmented-controls-images/segment02-vs.png#lightbox)
1. En la **pestaña widget** del **Explorador de propiedades**, puede ajustar varias propiedades del control de segmento, como su **estilo** y **Estado**: 

    [![La pestaña widget](segmented-controls-images/segment03-vs.png)](segmented-controls-images/segment03-vs.png#lightbox)
1. Use el campo **segmentos** para controlar el número de segmentos del controlador.
1. Seleccione un segmento determinado en la **lista desplegable segmento** para ajustar sus propiedades individuales, como **título** o **imagen** , y controlar si un segmento determinado está **habilitado** o **seleccionado** cuando se muestra el control.
1. Por último, asigne **nombres** a los controles para que pueda responder a ellos en código de C#. Por ejemplo: 

    [![Asignación de un nombre](segmented-controls-images/segment04-vs.png)](segmented-controls-images/segment04-vs.png#lightbox)
1. Guarde los cambios.

-----

Para obtener más información sobre cómo trabajar con guiones gráficos, vea nuestra [Guía de inicio rápido Hola, tvOS](~/ios/tvos/get-started/hello-tvos.md). 

<a name="Working-with-Segmented-Controls"></a>

## <a name="working-with-segmented-controls"></a>Trabajar con controles segmentados

Como se indicó anteriormente, el control segmentado s proporciona un conjunto de elementos lineales, cada uno de los cuales puede contener un icono o texto, y se usa para proporcionar al usuario un conjunto de opciones relacionadas.

Hay varias maneras diferentes de trabajar con controles segmentados en la aplicación Xamarin. tvOS.

<a name="Exposed-as-Outlets-and-Actions"></a>

## <a name="exposed-as-names-and-events"></a>Expuesto como nombres y eventos

Si ha creado el control de segmento en el diseñador de interfaces y lo ha expuesto como un control con nombre y un evento, puede usar el código siguiente para responder al cambio de segmento:

```csharp
partial void PlayerCountChanged (Foundation.NSObject sender) {

    // Take action based on the number of players selected
    switch(PlayerCount.SelectedSegment) {
    case 0:
        // Do something if the segment is selected
        ...
        break;
    case 1:
        // Do something if the segment is selected
        ...
        break;
    case 2:
        // Do something if the segment is selected
        ...
        break;
    }
}
```

En el caso del ejemplo anterior, el control de segmento se expuso como un `PlayerCount` nombre y una `PlayerCountChanged` acción de evento. Para obtener más información sobre cómo trabajar con acciones y salidas, consulte la sección [Writing The Code with Outlets and Actions (escritura del código con salidas y acciones](~/ios/tvos/get-started/hello-tvos.md#Writing-the-Code) ) de la [Guía de inicio rápido Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

La `SelectedSegment` propiedad obtiene o establece el segmento seleccionado actualmente como un índice basado en cero (0). Por tanto, si tiene cinco (5) segmentos, el primer segmento tendrá un índice de cero (0) y el último índice de cuatro (4).

<a name="Modifying-Segments"></a>

## <a name="modifying-segments"></a>Modificar segmentos

En cualquier momento, puede modificar tanto el número como el contenido de los controles segmentados. Use el código siguiente para insertar un nuevo segmento de icono:

```csharp
// Icon Segment
SegmentedControl.InsertSegment(UIImage.FromFile("icon.png"), 0, true);

// Text Segment
SegmentedControl.InsertSegment("New Segment", 0, true);
```

El segundo parámetro define dónde se insertará el segmento mediante un índice basado en cero (0). Si el último parámetro es `true` , se animará la inserción.

Para quitar un segmento determinado, use lo siguiente:

```csharp
SegmentedControl.RemoveSegmentAtIndex(0, true);
```

O lo siguiente para quitar todos los segmentos:

```csharp
SegmentedControl.RemoveAllSegments();
```

De nuevo, si el último parámetro es `true` , se animará la eliminación. Utilice la `NumberOfSegments` propiedad para devolver el número actual de segmentos.

Para obtener el **título** o el **icono** de un segmento determinado, use lo siguiente:

```csharp
// Get title
var title = SegmentedControl.TitleAt(0);

// Get icon
var icon = SegmentedControl.ImageAt(0);
```

Y para cambiar el **título** o el **icono**, use lo siguiente:

```csharp
// Set title
SegmentedControl.SetTitle("New Title", 0);

// Set icon
SegmentedControl.SetImage(UIImage.FromFile("icon.png"), 0);
```

Para ver si un segmento determinado está **habilitado**, use lo siguiente:

```csharp
if (SegmentedControl.IsEnabled(0)) {
    // Do something
    ...
}
```

Y para **habilitar o deshabilitar** un segmento determinado, use lo siguiente:

```csharp
SegmentedControl.SetEnabled(false, 0);
```

<a name="Modifying-the-Segmented-Controls-Appearance"></a>

## <a name="modifying-the-segmented-controls-appearance"></a>Modificar la apariencia del control segmentado

Puede usar el código siguiente para cambiar el fondo de un segmento determinado a una imagen:

```csharp
SegmentedControl.SetBackgroundImage (UIImage.FromFile("background.png"), UIControlState.Normal, UIBarMetrics.Default);
```

Donde `UIControlState` especifica el estado del control para el que está configurando la imagen:

- Normal
- Resaltado
- Disabled
- Seleccionado
- Con foco

Y `UIBarMetrics` especifican las métricas que se van a usar como:

- Valor predeterminado
- Compacto
- DefaultPrompt
- CompactPrompt

Además, puede establecer el divisor entre los segmentos mediante:

```csharp
SegmentedControl.SetDividerImage (UIImage.FromFile("divider.png"), UIControlState.Normal, UIControlState.Normal, UIBarMetrics.Default);
```

Donde el primero `UIControlState` especifica el estado del segmento a la izquierda del divisor y el segundo `UIControlState` especifica el estado del segmento a la derecha.

<a name="Summary"></a>

## <a name="summary"></a>Resumen

En este artículo se ha tratado el diseño y el uso del control segmentado dentro de una aplicación Xamarin. tvOS.

## <a name="related-links"></a>Vínculos relacionados

- [Ejemplos de tvOS](/samples/browse/?products=xamarin&term=Xamarin.iOS%2btvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guías de la interfaz humana de tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guía de programación de aplicaciones para tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)