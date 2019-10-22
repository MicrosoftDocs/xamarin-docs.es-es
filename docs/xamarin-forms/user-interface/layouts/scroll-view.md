---
title: ScrollView de Xamarin. Forms
description: En este artículo se explica cómo usar la clase ScrollView de Xamarin. Forms para presentar diseños que no caben en una sola pantalla y que tienen espacio de contenido para el teclado.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 8d523c6da6ca7feaf6894123822f789f37455865
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696853"
---
# <a name="xamarinforms-scrollview"></a>ScrollView de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) contiene diseños y les permite desplazarse fuera de la ventana desplegable. `ScrollView` también se usa para permitir que las vistas se muevan automáticamente a la parte visible de la pantalla cuando se muestra el teclado.

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Finalidad

[`ScrollView`](xref:Xamarin.Forms.ScrollView) puede usarse para asegurarse de que las vistas de mayor tamaño se muestran bien en teléfonos más pequeños. Por ejemplo, un diseño que funciona en un iPhone 6S puede estar recortado en un iPhone 4S. El uso de un `ScrollView` permitiría mostrar las partes recortadas del diseño en la pantalla más pequeña.

## <a name="usage"></a>Uso

> [!NOTE]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView) objetos no deben estar anidados. Además, `ScrollView`s no se deben anidar con otros controles que proporcionen desplazamiento, como `ListView` y `WebView`.

[`ScrollView`](xref:Xamarin.Forms.ScrollView) expone una propiedad `Content`, que se puede establecer en una sola vista o diseño. Considere este ejemplo de un diseño con un boxView muy grande, seguido de un `Entry`:

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

Antes de que el usuario se desplace hacia abajo, solo está visible la `BoxView`:

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Tenga en cuenta que cuando el usuario comienza a escribir texto en el `Entry`, la vista se desplaza para mantenerlo visible en la pantalla:

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>Propiedades

[`ScrollView`](xref:Xamarin.Forms.ScrollView) define las siguientes propiedades:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) obtiene un valor de [`Size`](xref:Xamarin.Forms.Size) que representa el tamaño del contenido.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) obtiene o establece un valor de enumeración de [`ScrollOrientation`](xref:Xamarin.Forms.ScrollOrientation) que representa la dirección de desplazamiento de la `ScrollView`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) obtiene un `double` que representa la posición de desplazamiento X actual.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) obtiene un `double` que representa la posición de desplazamiento Y actual.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) obtiene o establece un valor de [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) que representa Cuándo está visible la barra de desplazamiento horizontal.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) obtiene o establece un valor de [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) que representa Cuándo está visible la barra de desplazamiento vertical.

> [!NOTE]
> El desplazamiento se puede deshabilitar estableciendo la propiedad [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) en `Neither`.

## <a name="methods"></a>Métodos

[`ScrollView`](xref:Xamarin.Forms.ScrollView) proporciona un método de `ScrollToAsync`, que se puede utilizar para desplazar la vista usando las coordenadas o especificando una vista determinada que debe hacerse visible.

Al utilizar coordenadas, especifique las coordenadas `x` y `y`, junto con un valor booleano que indica si se debe animar el desplazamiento:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> El método `ScrollToAsync` no producirá un desplazamiento cuando la propiedad [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) esté establecida en `Neither`.

Al desplazarse a un elemento determinado, la enumeración `ScrollToPosition` especifica dónde aparecerá el elemento en la vista.

- **Center** &ndash; desplaza el elemento hasta el centro de la parte visible de la vista.
- **End** &ndash; desplaza el elemento hasta el final de la parte visible de la vista.
- **MakeVisible** &ndash; desplaza el elemento para que esté visible dentro de la vista.
- **Iniciar** &ndash; desplaza el elemento hasta el inicio de la parte visible de la vista.

La propiedad `IsAnimated` especifica cómo se desplazará la vista. Cuando se establece en `true`, se usará una animación Smooth, en lugar de mover el contenido al instante en la vista.

## <a name="events"></a>Events

[`ScrollView`](xref:Xamarin.Forms.ScrollView) define un solo evento, `Scrolled`. `Scrolled` se produce cuando la vista ha terminado de desplazarse. El controlador de eventos para `Scrolled` toma `ScrolledEventArgs`, que tiene las propiedades `ScrollX` y `ScrollY`. A continuación se muestra cómo actualizar una etiqueta con la posición de desplazamiento actual de un `ScrollView`:

```csharp
Label label = new Label { Text = "Position: " };
ScrollView scroll = new ScrollView();
scroll.Scrolled += (object sender, ScrolledEventArgs e) => {
    label.Text = "Position: " + e.ScrollX + " x " + e.ScrollY;
};
```

Tenga en cuenta que las posiciones de desplazamiento pueden ser negativas debido al efecto de rebote al desplazarse al final de una lista.

## <a name="related-links"></a>Vínculos relacionados

- [Diseño (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Ejemplo de BusinessTumble (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
