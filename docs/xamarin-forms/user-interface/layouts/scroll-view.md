---
title: Xamarin.Forms ScrollView
description: En este artículo se explica cómo usar la clase Xamarin.Forms ScrollView para presentar los diseños que no caben en una sola pantalla y que tienen contenido dejar espacio para el teclado.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 131b9dc64db1ea0dba3982bd71459c2a281743bc
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/06/2019
ms.locfileid: "70770284"
---
# <a name="xamarinforms-scrollview"></a>Xamarin.Forms ScrollView

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) contiene los diseños y les permite desplazamiento fuera de la pantalla. `ScrollView` También se utiliza para permitir que las vistas se mueva automáticamente a la parte visible de la pantalla cuando se muestra el teclado.

[![](scroll-view-images/layouts-sml.png "Los diseños de Xamarin.Forms")](scroll-view-images/layouts.png#lightbox "los diseños de Xamarin.Forms")

En este artículo se tratan los aspectos siguientes:

- **[Propósito](#purpose)**  &ndash; el propósito de `ScrollView` y cuando se utiliza.
- **[Uso](#usage)**  &ndash; uso `ScrollView` en la práctica.
- **[Propiedades](#properties)**  &ndash; propiedades públicas que se pueden leer y modificar.
- **[Métodos](#methods)**  &ndash; métodos públicos que se pueden llamar para desplazar la vista.
- **[Eventos](#events)**  &ndash; eventos que pueden usarse para detectar cambios en los Estados de la vista.

## <a name="purpose"></a>Propósito

`ScrollView` puede utilizarse para asegurarse de que se muestran vistas mayor bien en los teléfonos más pequeños. Por ejemplo, un diseño que funciona en un iPhone 6s puede quedar cortado en un iPhone 4s. Mediante un `ScrollView` permitirían las partes recortadas del diseño que se mostrará en la pantalla más pequeña.

## <a name="usage"></a>Uso

> [!NOTE]
> `ScrollView`s no debería estar anidado. Además, `ScrollView`s no debería estar anidado con otros controles que proporcionan el desplazamiento, como `ListView` y `WebView`.

`ScrollView` expone un `Content` propiedad que se puede establecer en un diseño o vista única. Considere este ejemplo de un diseño con un gran boxView, seguido por un `Entry`:

```xaml
<ContentPage.Content>
    <ScrollView>
        <StackLayout>
            <BoxView BackgroundColor="Red" HeightRequest="600" WidthRequest="150" />
            <Entry />
        </StackLayout>
    </ScrollView>
</ContentPage.Content>
```

En C#:

```csharp
var scroll = new ScrollView();
Content = scroll;
var stack = new StackLayout();
stack.Children.Add(new BoxView { BackgroundColor = Color.Red,    HeightRequest = 600, WidthRequest = 600 });
stack.Children.Add(new Entry());
```

Antes de que el usuario se desplaza hacia abajo, solo el `BoxView` está visible:

![](scroll-view-images/scroll-start.png "BoxView en ScrollView")

Tenga en cuenta que cuando el usuario comienza a escribir texto en el `Entry`, la vista se desplaza para mantener visible en pantalla:

![](scroll-view-images/scroll-end.png "Entrada de ScrollView")

## <a name="properties"></a>Propiedades

`ScrollView` define las siguientes propiedades:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) Obtiene un [ `Size` ](xref:Xamarin.Forms.Size) valor que representa el tamaño del contenido.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) Obtiene o establece un [ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation) valor de enumeración que representa la dirección de desplazamiento de la `ScrollView`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) Obtiene un `double` que representa la actual posición de desplazamiento X.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) Obtiene un `double` que representa la posición de desplazamiento Y actual.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) Obtiene o establece un [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) valor que representa cuándo está visible la barra de desplazamiento horizontal.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) Obtiene o establece un [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) valor que representa el momento de la barra de desplazamiento vertical está visible.

## <a name="methods"></a>Métodos

`ScrollView` Proporciona un `ScrollToAsync` método, que se puede usar para desplazar la vista mediante coordenadas o bien especificando una vista concreta que debe hacerse visible.

Cuando se usa coordenadas, especifique el `x` y `y` coordenadas, junto con un valor booleano que indica si el desplazamiento debe animarse:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

Al desplazarse a un elemento determinado, el `ScrollToPosition` especifica enumeración donde en la vista del elemento aparecerá:

- **Centro de** &ndash; desplaza el elemento en el centro de la parte visible de la vista.
- **End** &ndash; desplaza el elemento al final de la parte visible de la vista.
- **MakeVisible** &ndash; desplaza el elemento para que sea visible dentro de la vista.
- **Iniciar** &ndash; desplaza el elemento al principio de la parte visible de la vista.

El `IsAnimated` propiedad especifica cómo se desplazará la vista. Cuando establece en true, una animación suave, se usará, en lugar de pasar al instante el contenido en la vista.

## <a name="events"></a>Eventos

`ScrollView` define un solo evento `Scrolled`. `Scrolled` se produce cuando ha finalizado la vista de desplazamiento. El controlador de eventos `Scrolled` toma `ScrolledEventArgs`, que tiene el `ScrollX` y `ScrollY` propiedades. El siguiente ejemplo muestra cómo actualizar una etiqueta con la posición de desplazamiento actual de un `ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Tenga en cuenta que las posiciones de desplazamiento pueden ser negativas, porque el efecto de rebote al desplazarse al final de una lista.

## <a name="related-links"></a>Vínculos relacionados

- [Diseño (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Ejemplo de BusinessTumble (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
