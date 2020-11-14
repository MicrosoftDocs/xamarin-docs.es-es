---
title: Xamarin.Forms Desplazar CarouselView
description: Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Además, CarouselView define dos métodos ScrollTo, que desplazan los elementos mediante programación a la vista.
ms.prod: xamarin
ms.assetid: 92D7B618-07FA-4343-9D0F-212525E92C39
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 58d0da21c81586a2cee2268c5d2df668eab0515a
ms.sourcegitcommit: f2942b518f51317acbb263be5bc0c91e66239f50
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590303"
---
# <a name="no-locxamarinforms-carouselview-scrolling"></a>Xamarin.Forms Desplazar CarouselView

![API de versión preliminar](~/media/shared/preview.png)

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define las siguientes propiedades relacionadas con el desplazamiento:

- `HorizontalScrollBarVisibility`, de tipo `ScrollBarVisibility` , que especifica cuándo está visible la barra de desplazamiento horizontal.
- `IsDragging`, de tipo `bool` , que indica si `CarouselView` se está desplazando el. Se trata de una propiedad de solo lectura, cuyo valor predeterminado es `false` .
- `IsScrollAnimated`, de tipo `bool` , que especifica si se producirá una animación al desplazarse `CarouselView` . El valor predeterminado es `true`.
- `ItemsUpdatingScrollMode`, de tipo `ItemsUpdatingScrollMode` , que representa el comportamiento de desplazamiento de `CarouselView` cuando se agregan nuevos elementos a él.
- `VerticalScrollBarVisibility`, de tipo `ScrollBarVisibility` , que especifica cuándo está visible la barra de desplazamiento vertical.

Todas estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que pueden ser destinos de enlaces de datos.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) también define dos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos, que desplazan los elementos a la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado a la vista. Ambas sobrecargas tienen argumentos adicionales que se pueden especificar para indicar la posición exacta del elemento una vez que se ha completado el desplazamiento y si se debe animar el desplazamiento.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define un [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento que se desencadena cuando se invoca a uno de los [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos. El [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) objeto que acompaña al `ScrollToRequested` evento tiene muchas propiedades, entre las que se incluyen `IsAnimated` , `Index` , `Item` y `ScrollToPosition` . Estas propiedades se establecen a partir de los argumentos especificados en las `ScrollTo` llamadas de método.

Además, [`CarouselView`](xref:Xamarin.Forms.CarouselView) define un `Scrolled` evento que se desencadena para indicar que se ha producido el desplazamiento. El `ItemsViewScrolledEventArgs` objeto que acompaña al `Scrolled` evento tiene muchas propiedades. Para obtener más información, vea [detectar desplazamiento](#detect-scrolling).

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Esta característica se conoce como ajuste, ya que los elementos se ajustan a la posición cuando se detiene el desplazamiento. Para obtener más información, vea [puntos de acoplamiento](#snap-points).

[`CarouselView`](xref:Xamarin.Forms.CarouselView) también puede cargar datos de forma incremental a medida que el usuario se desplaza. Para obtener más información, vea [cargar datos de forma incremental](populate-data.md#load-data-incrementally).

## <a name="detect-scrolling"></a>Detectar desplazamiento

Se `IsDragging` puede examinar la propiedad para determinar si [`CarouselView`](xref:Xamarin.Forms.CarouselView) se está desplazando actualmente por los elementos.

Además, [`CarouselView`](xref:Xamarin.Forms.CarouselView) define un `Scrolled` evento que se desencadena para indicar que se ha producido el desplazamiento. Este evento se debe usar cuando se requieren datos sobre el desplazamiento.

En el ejemplo de XAML siguiente se muestra un `CarouselView` que establece un controlador de eventos para el `Scrolled` evento:

```xaml
<CarouselView Scrolled="OnCollectionViewScrolled">
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView();
carouselView.Scrolled += OnCarouselViewScrolled;
```

En este ejemplo de código, el `OnCarouselViewScrolled` controlador de eventos se ejecuta cuando se `Scrolled` desencadena el evento:

```csharp
void OnCarouselViewScrolled(object sender, ItemsViewScrolledEventArgs e)
{
    Debug.WriteLine("HorizontalDelta: " + e.HorizontalDelta);
    Debug.WriteLine("VerticalDelta: " + e.VerticalDelta);
    Debug.WriteLine("HorizontalOffset: " + e.HorizontalOffset);
    Debug.WriteLine("VerticalOffset: " + e.VerticalOffset);
    Debug.WriteLine("FirstVisibleItemIndex: " + e.FirstVisibleItemIndex);
    Debug.WriteLine("CenterItemIndex: " + e.CenterItemIndex);
    Debug.WriteLine("LastVisibleItemIndex: " + e.LastVisibleItemIndex);
}
```

En este ejemplo, el `OnCarouselViewScrolled` controlador de eventos genera los valores del `ItemsViewScrolledEventArgs` objeto que acompaña al evento.

> [!IMPORTANT]
> El `Scrolled` evento se desencadena para los desplazamientos iniciados por el usuario y para los desplazamientos mediante programación.

## <a name="scroll-an-item-at-an-index-into-view"></a>Desplazar un elemento en un índice en la vista

La primera [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga del método desplaza el elemento en el índice especificado en la vista. Dado un [`CarouselView`](xref:Xamarin.Forms.CarouselView) objeto denominado `carouselView` , en el ejemplo siguiente se muestra cómo desplazar el elemento en el índice 6 a la vista:

```csharp
carouselView.ScrollTo(6);
```

> [!NOTE]
> El [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento se desencadena cuando [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) se invoca el método.

## <a name="scroll-an-item-into-view"></a>Desplazar un elemento a la vista

La segunda [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga del método desplaza el elemento especificado a la vista. Dado un [`CarouselView`](xref:Xamarin.Forms.CarouselView) objeto denominado `carouselView` , en el ejemplo siguiente se muestra cómo desplazar el elemento Proboscis Monkey a la vista:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
carouselView.ScrollTo(monkey);
```

> [!NOTE]
> El [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento se desencadena cuando [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) se invoca el método.

## <a name="disable-scroll-animation"></a>Deshabilitar animación de desplazamiento

Se muestra una animación de desplazamiento al moverse entre los elementos de un [`CarouselView`](xref:Xamarin.Forms.CarouselView) . Esta animación se produce para los desplazamientos iniciados por el usuario y para los desplazamientos mediante programación. Al establecer la `IsScrollAnimated` propiedad en `false` , se deshabilitará la animación para ambas categorías de desplazamiento.

Como alternativa, el `animate` argumento del `ScrollTo` método se puede establecer en `false` para deshabilitar la animación de desplazamiento en desplazamientos mediante programación:

```csharp
carouselView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>Posición de desplazamiento de control

Al desplazar un elemento a la vista, se puede especificar la posición exacta del elemento una vez completado el desplazamiento con el `position` argumento de los [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos. Este argumento acepta un [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) miembro de enumeración.

### <a name="makevisible"></a>MakeVisible

El [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento debe desplazarse hasta que esté visible en la vista:

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Este código de ejemplo produce el desplazamiento mínimo necesario para desplazar el elemento a la vista.

> [!NOTE]
> El [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) miembro se utiliza de forma predeterminada si `position` no se especifica el argumento al llamar al `ScrollTo` método.

### <a name="start"></a>Start

El [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento debe desplazarse hasta el inicio de la vista:

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Este código de ejemplo hace que el elemento se desplace hasta el inicio de la vista.

### <a name="center"></a>Center

El [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento debe desplazarse hasta el centro de la vista:

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Este código de ejemplo hace que el elemento se desplace hasta el centro de la vista.

### <a name="end"></a>End

El [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento debe desplazarse hasta el final de la vista:

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Este código de ejemplo hace que el elemento se desplace hasta el final de la vista.

## <a name="control-scroll-position-when-new-items-are-added"></a>Controlar la posición de desplazamiento cuando se agregan nuevos elementos

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define una `ItemsUpdatingScrollMode` propiedad, que está respaldada por una propiedad enlazable. Esta propiedad obtiene o establece un `ItemsUpdatingScrollMode` valor de enumeración que representa el comportamiento de desplazamiento de `CarouselView` cuando se agregan nuevos elementos a él. La enumeración `ItemsUpdatingScrollMode` define los miembros siguientes:

- `KeepItemsInView` mantiene el primer elemento de la lista que se muestra cuando se agregan nuevos elementos.
- `KeepScrollOffset` garantiza que la posición de desplazamiento actual se mantiene cuando se agregan nuevos elementos.
- `KeepLastItemInView` ajusta el desplazamiento de desplazamiento para mantener el último elemento de la lista que se muestra cuando se agregan nuevos elementos.

El valor predeterminado de la `ItemsUpdatingScrollMode` propiedad es `KeepItemsInView` . Por lo tanto, cuando se agreguen nuevos elementos al [`CarouselView`](xref:Xamarin.Forms.CarouselView) primer elemento de la lista, se mostrarán. Para asegurarse de que el último elemento de la lista se muestra cuando se agregan nuevos elementos, establezca la `ItemsUpdatingScrollMode` propiedad en `KeepLastItemInView` :

```xaml
<CarouselView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>Visibilidad de la barra de desplazamiento

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` las propiedades y, que están respaldadas por propiedades enlazables. Estas propiedades obtienen o establecen un [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valor de enumeración que representa Cuándo está visible la barra de desplazamiento horizontal o vertical. La enumeración `ScrollBarVisibility` define los miembros siguientes:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) indica el comportamiento predeterminado de la barra de desplazamiento para la plataforma y es el valor predeterminado para las `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` propiedades y.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) indica que las barras de desplazamiento serán visibles, incluso cuando el contenido quepa en la vista.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) indica que las barras de desplazamiento no estarán visibles, incluso si el contenido no cabe en la vista.

## <a name="snap-points"></a>Puntos de acoplamiento

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Esta característica se conoce como ajuste, ya que los elementos se ajustan a la posición cuando se detiene el desplazamiento y se controlan mediante las siguientes propiedades de la [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) clase:

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) , especifica el comportamiento de los puntos de ajuste al desplazarse.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) , especifica cómo se alinean los puntos de ajuste con los elementos.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

> [!NOTE]
> Cuando se produce el ajuste, se producirá en la dirección que produce la menor cantidad de movimiento.

### <a name="snap-points-type"></a>Tipo de puntos de ajuste

La [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) enumeración define los siguientes miembros:

- `None` indica que el desplazamiento no se ajusta a los elementos.
- `Mandatory` indica que el contenido se ajusta siempre al punto de acoplamiento más cercano a donde se detendría el desplazamiento de forma natural, a lo largo de la dirección de inercia.
- `MandatorySingle` indica el mismo comportamiento que `Mandatory` , pero solo desplaza un elemento cada vez.

De forma predeterminada [`CarouselView`](xref:Xamarin.Forms.CarouselView) , en, la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propiedad se establece en `SnapPointsType.MandatorySingle` , lo que garantiza que el desplazamiento solo se desplaza un elemento cada vez.

Las siguientes capturas de pantallas muestran un [`CarouselView`](xref:Xamarin.Forms.CarouselView) con el ajuste desactivado:

[![Captura de pantalla de un CarouselView sin puntos de acoplamiento, en iOS y Android](scrolling-images/snappoints-none.png "CarouselView sin puntos de acoplamiento")](scrolling-images/snappoints-none-large.png#lightbox "CarouselView sin puntos de acoplamiento")

### <a name="snap-points-alignment"></a>Alineación de puntos de acoplamiento

La [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) enumeración `Start` define `Center` `End` los miembros, y.

> [!IMPORTANT]
> El valor de la [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propiedad solo se respeta cuando la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propiedad está establecida en `Mandatory` , o `MandatorySingle` .

#### <a name="start"></a>Start

El `SnapPointsAlignment.Start` miembro indica que los puntos de ajuste están alineados con el borde inicial de los elementos. En el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

Cuando un usuario desliza el dedo para iniciar un desplazamiento en un desplazamiento horizontal [`CarouselView`](xref:Xamarin.Forms.CarouselView) , el elemento de la izquierda se alineará a la izquierda de la vista:

[![Captura de pantalla de un CarouselView con puntos de acoplamiento iniciales, en iOS y Android](scrolling-images/snappoints-start.png "CarouselView con puntos de acoplamiento iniciales")](scrolling-images/snappoints-start-large.png#lightbox "CarouselView con puntos de acoplamiento iniciales")

#### <a name="center"></a>Center

El `SnapPointsAlignment.Center` miembro indica que los puntos de ajuste están alineados con el centro de los elementos.

De forma predeterminada [`CarouselView`](xref:Xamarin.Forms.CarouselView) , en, la [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propiedad se establece en `Center` . Sin embargo, por integridad, en el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

Cuando un usuario desliza el dedo para iniciar un desplazamiento en un desplazamiento horizontal [`CarouselView`](xref:Xamarin.Forms.CarouselView) , el elemento central se alinea con el centro de la vista:

[![Captura de pantalla de un CarouselView con puntos de acoplamiento, en iOS y Android](scrolling-images/snappoints-center.png "CarouselView con puntos de acoplamiento")](scrolling-images/snappoints-center-large.png#lightbox "CarouselView con puntos de acoplamiento")

#### <a name="end"></a>End

El `SnapPointsAlignment.End` miembro indica que los puntos de ajuste están alineados con el borde final de los elementos. En el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

El código de C# equivalente es el siguiente:

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

Cuando un usuario desliza el dedo para iniciar un desplazamiento en un desplazamiento horizontal [`CarouselView`](xref:Xamarin.Forms.CarouselView) , el elemento de la derecha se alineará a la derecha de la vista.

[![Captura de pantalla de un CarouselView con puntos de acoplamiento finales, en iOS y Android](scrolling-images/snappoints-end.png "CarouselView con puntos de acoplamiento finales")](scrolling-images/snappoints-end-large.png#lightbox "CarouselView con puntos de acoplamiento finales")

## <a name="related-links"></a>Vínculos relacionados

- [CarouselView (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
