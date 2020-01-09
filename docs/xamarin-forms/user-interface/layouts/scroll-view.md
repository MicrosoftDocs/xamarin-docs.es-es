---
title: Xamarin.Forms ScrollView
description: En este artículo se explica cómo usar la clase Xamarin.Forms ScrollView para presentar los diseños que no caben en una sola pantalla y que tienen contenido dejar espacio para el teclado.
ms.prod: xamarin
ms.assetid: 7B542872-B3D1-49B3-B15E-0E98F53C1F6E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2019
ms.openlocfilehash: bb10cda7c9899f176861ceee712cc876984c56ef
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75488275"
---
# <a name="xamarinforms-scrollview"></a>Xamarin.Forms ScrollView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`ScrollView`](xref:Xamarin.Forms.ScrollView) contiene los diseños y les permite desplazamiento fuera de la pantalla. `ScrollView` También se utiliza para permitir que las vistas se mueva automáticamente a la parte visible de la pantalla cuando se muestra el teclado.

[![](scroll-view-images/layouts-sml.png "Xamarin.Forms Layouts")](scroll-view-images/layouts.png#lightbox "Xamarin.Forms Layouts")

## <a name="purpose"></a>Finalidad

[`ScrollView`](xref:Xamarin.Forms.ScrollView) puede usarse para asegurarse de que las vistas de mayor tamaño se muestran bien en teléfonos más pequeños. Por ejemplo, un diseño que funciona en un iPhone 6s puede quedar cortado en un iPhone 4s. Mediante un `ScrollView` permitirían las partes recortadas del diseño que se mostrará en la pantalla más pequeña.

## <a name="usage"></a>Usage

> [!NOTE]
> [`ScrollView`](xref:Xamarin.Forms.ScrollView) objetos no deben estar anidados. Además, `ScrollView`s no debería estar anidado con otros controles que proporcionan el desplazamiento, como `ListView` y `WebView`.

[`ScrollView`](xref:Xamarin.Forms.ScrollView) expone una propiedad `Content`, que se puede establecer en una sola vista o diseño. Considere este ejemplo de un diseño con un gran boxView, seguido por un `Entry`:

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
public class ScrollingDemoCode : ContentPage
{
    public ScrollingDemoCode()
    {
        StackLayout stackLayout = new StackLayout();
        stackLayout.Children.Add(new BoxView { BackgroundColor = Color.Red, HeightRequest = 600, WidthRequest = 150 });
        stackLayout.Children.Add(new Entry());
        ScrollView scrollView = new ScrollView();
        scrollView.Content = stackLayout;
        Content = scrollView;
    }
}
```

Antes de que el usuario se desplaza hacia abajo, solo el `BoxView` está visible:

![](scroll-view-images/scroll-start.png "BoxView in ScrollView")

Tenga en cuenta que cuando el usuario comienza a escribir texto en el `Entry`, la vista se desplaza para mantener visible en pantalla:

![](scroll-view-images/scroll-end.png "Entry in ScrollView")

## <a name="properties"></a>Propiedades

[`ScrollView`](xref:Xamarin.Forms.ScrollView) define las siguientes propiedades:

- [`ContentSize`](xref:Xamarin.Forms.ScrollView.ContentSizeProperty) Obtiene un [ `Size` ](xref:Xamarin.Forms.Size) valor que representa el tamaño del contenido.
- [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) Obtiene o establece un [ `ScrollOrientation` ](xref:Xamarin.Forms.ScrollOrientation) valor de enumeración que representa la dirección de desplazamiento de la `ScrollView`.
- [`ScrollX`](xref:Xamarin.Forms.ScrollView.ScrollXProperty) Obtiene un `double` que representa la actual posición de desplazamiento X.
- [`ScrollY`](xref:Xamarin.Forms.ScrollView.ScrollYProperty) Obtiene un `double` que representa la posición de desplazamiento Y actual.
- [`HorizontalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.HorizontalScrollBarVisibilityProperty) Obtiene o establece un [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) valor que representa cuándo está visible la barra de desplazamiento horizontal.
- [`VerticalScrollBarVisibility`](xref:Xamarin.Forms.ScrollView.VerticalScrollBarVisibilityProperty) Obtiene o establece un [ `ScrollBarVisibility` ](xref:Xamarin.Forms.ScrollBarVisibility) valor que representa el momento de la barra de desplazamiento vertical está visible.

> [!NOTE]
> El desplazamiento se puede deshabilitar estableciendo la propiedad [`Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) en `Neither`.

## <a name="methods"></a>Métodos

[`ScrollView`](xref:Xamarin.Forms.ScrollView) proporciona un método de `ScrollToAsync`, que se puede utilizar para desplazar la vista usando las coordenadas o especificando una vista determinada que debe hacerse visible.

Cuando se usa coordenadas, especifique el `x` y `y` coordenadas, junto con un valor booleano que indica si el desplazamiento debe animarse:

```csharp
scroll.ScrollToAsync(0, 150, true); //scrolls so that the position at 150px from the top is visible

scroll.ScrollToAsync(label, ScrollToPosition.Start, true); //scrolls so that the label is at the start of the list
```

> [!IMPORTANT]
> El método `ScrollToAsync` no producirá un desplazamiento cuando la propiedad [`ScrollView.Orientation`](xref:Xamarin.Forms.ScrollView.OrientationProperty) esté establecida en `Neither`.

Al desplazarse a un elemento determinado, la enumeración `ScrollToPosition` especifica dónde aparecerá el elemento en la vista.

- **Centro de** &ndash; desplaza el elemento en el centro de la parte visible de la vista.
- **End** &ndash; desplaza el elemento al final de la parte visible de la vista.
- **MakeVisible** &ndash; desplaza el elemento para que sea visible dentro de la vista.
- **Iniciar** &ndash; desplaza el elemento al principio de la parte visible de la vista.

El `IsAnimated` propiedad especifica cómo se desplazará la vista. Cuando se establece en `true`, se usará una animación Smooth, en lugar de mover el contenido al instante en la vista.

## <a name="events"></a>eventos

[`ScrollView`](xref:Xamarin.Forms.ScrollView) define un solo evento, `Scrolled`. `Scrolled` se produce cuando ha finalizado la vista de desplazamiento. El controlador de eventos `Scrolled` toma `ScrolledEventArgs`, que tiene el `ScrollX` y `ScrollY` propiedades. El siguiente ejemplo muestra cómo actualizar una etiqueta con la posición de desplazamiento actual de un `ScrollView`:

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
