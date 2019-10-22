---
title: Desplazamientos de CollectionView de Xamarin. Forms
description: Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Además, CollectionView define dos métodos ScrollTo, que desplazan los elementos mediante programación a la vista.
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 7aef14cbb854d89a2088a450353b943402f76a86
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697218"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Desplazamientos de CollectionView de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define dos métodos de [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , que desplazan los elementos a la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado a la vista. Ambas sobrecargas tienen argumentos adicionales que se pueden especificar para indicar el grupo al que pertenece el elemento, la posición exacta del elemento una vez que se ha completado el desplazamiento y si se debe animar el desplazamiento.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define un evento [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) que se desencadena cuando se invoca a uno de los métodos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) . El objeto [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) que acompaña al evento `ScrollToRequested` tiene muchas propiedades, como `IsAnimated`, `Index`, `Item` y `ScrollToPosition`. Estas propiedades se establecen a partir de los argumentos especificados en el método `ScrollTo` llama a.

Además, [`CollectionView`](xref:Xamarin.Forms.CollectionView) define un evento `Scrolled` que se desencadena para indicar que se ha producido el desplazamiento. El objeto `ItemsViewScrolledEventArgs` que acompaña al evento `Scrolled` tiene muchas propiedades. Para obtener más información, vea [detectar desplazamiento](#detect-scrolling).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) también define una propiedad `ItemsUpdatingScrollMode` que representa el comportamiento de desplazamiento de la `CollectionView` cuando se agregan nuevos elementos a ella. Para obtener más información sobre esta propiedad, vea [control de posición de desplazamiento cuando se agregan nuevos elementos](#control-scroll-position-when-new-items-are-added).

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Esta característica se conoce como ajuste, ya que los elementos se ajustan a la posición cuando se detiene el desplazamiento. Para obtener más información, vea [puntos de acoplamiento](#snap-points).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) también puede cargar datos de forma incremental a medida que el usuario se desplaza. Para obtener más información, vea [cargar datos de forma incremental](populate-data.md#load-data-incrementally).

## <a name="detect-scrolling"></a>Detectar desplazamiento

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define un evento de `Scrolled` que se desencadena para indicar que se ha producido el desplazamiento. En el ejemplo de XAML siguiente se muestra un `CollectionView` que establece un controlador de eventos para el evento `Scrolled`:

```xaml
<CollectionView Scrolled="OnCollectionViewScrolled">
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.Scrolled += OnCollectionViewScrolled;
```

En este ejemplo de código, se ejecuta el controlador de eventos `OnCollectionViewScrolled` cuando se activa el evento `Scrolled`:

```csharp
void OnCollectionViewScrolled(object sender, ItemsViewScrolledEventArgs e)
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

En este ejemplo, el controlador de eventos `OnCollectionViewScrolled` devuelve los valores del objeto `ItemsViewScrolledEventArgs` que acompaña al evento.

> [!IMPORTANT]
> El evento `Scrolled` se desencadena para los desplazamientos iniciados por el usuario y para los desplazamientos mediante programación.

## <a name="scroll-an-item-at-an-index-into-view"></a>Desplazar un elemento en un índice en la vista

La primera sobrecarga del método [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) desplaza el elemento en el índice especificado en la vista. Dado un [`CollectionView`](xref:Xamarin.Forms.CollectionView) objeto denominado `collectionView`, en el ejemplo siguiente se muestra cómo desplazar el elemento en el índice 12 a la vista:

```csharp
collectionView.ScrollTo(12);
```

Como alternativa, un elemento de los datos agrupados se puede desplazar en la vista especificando los índices de elemento y de grupo. En el ejemplo siguiente se muestra cómo desplazar el tercer elemento del segundo grupo a la vista:

```csharp
// Items and groups are indexed from zero.
collectionView.ScrollTo(2, 1);
```

> [!NOTE]
> El evento [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) se desencadena cuando se invoca el método [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) .

## <a name="scroll-an-item-into-view"></a>Desplazar un elemento a la vista

La segunda sobrecarga del método [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) desplaza el elemento especificado a la vista. Dado un [`CollectionView`](xref:Xamarin.Forms.CollectionView) objeto denominado `collectionView`, en el ejemplo siguiente se muestra cómo desplazar el elemento Proboscis Monkey a la vista:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

Como alternativa, un elemento de los datos agrupados se puede desplazar en la vista especificando el elemento y el grupo. En el ejemplo siguiente se muestra cómo desplazar el elemento Monkey de Proboscis en el grupo Monkeys a la vista:

```csharp
GroupedAnimalsViewModel viewModel = BindingContext as GroupedAnimalsViewModel;
AnimalGroup group = viewModel.Animals.FirstOrDefault(a => a.Name == "Monkeys");
Animal monkey = group.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey, group);
```

> [!NOTE]
> El evento [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) se desencadena cuando se invoca el método [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) .

## <a name="disable-scroll-animation"></a>Deshabilitar animación de desplazamiento

Cuando se desplaza un elemento a la vista, se muestra una animación de desplazamiento. Sin embargo, esta animación se puede deshabilitar estableciendo el argumento `animate` del método `ScrollTo` en `false`:

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>Posición de desplazamiento de control

Al desplazar un elemento a la vista, se puede especificar la posición exacta del elemento una vez completado el desplazamiento con el `position` argumento de los métodos de [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) . Este argumento acepta un miembro de enumeración de [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) .

### <a name="makevisible"></a>MakeVisible

El miembro [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) indica que el elemento debe desplazarse hasta que esté visible en la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Este código de ejemplo produce el desplazamiento mínimo necesario para desplazar el elemento a la vista:

[![Captura de pantalla de una lista de CollectionView vertical con un elemento desplazado en la vista, en iOS y Android](scrolling-images/scrolltoposition-makevisible.png "Lista de CollectionView vertical con elemento desplazado")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "Lista de CollectionView vertical con elemento desplazado")

> [!NOTE]
> De forma predeterminada, se utiliza el miembro [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) , si no se especifica el argumento `position` al llamar al método `ScrollTo`.

### <a name="start"></a>Iniciar

El miembro [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) indica que el elemento debe desplazarse hasta el inicio de la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Este código de ejemplo hace que el elemento se desplace hasta el inicio de la vista:

[![Captura de pantalla de una lista de CollectionView vertical con un elemento desplazado en la vista, en iOS y Android](scrolling-images/scrolltoposition-start.png "Lista de CollectionView vertical con elemento desplazado")](scrolling-images/scrolltoposition-start-large.png#lightbox "Lista de CollectionView vertical con elemento desplazado")

### <a name="center"></a>Centrar

El miembro [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) indica que el elemento debe desplazarse hasta el centro de la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Este código de ejemplo hace que el elemento se desplace hasta el centro de la vista:

[![Captura de pantalla de una lista de CollectionView vertical con un elemento desplazado en la vista, en iOS y Android](scrolling-images/scrolltoposition-center.png "Lista de CollectionView vertical con elemento desplazado")](scrolling-images/scrolltoposition-center-large.png#lightbox "Lista de CollectionView vertical con elemento desplazado")

### <a name="end"></a>Fin

El miembro [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) indica que el elemento debe desplazarse hasta el final de la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Este código de ejemplo hace que el elemento se desplace hasta el final de la vista:

[![Captura de pantalla de una lista de CollectionView vertical con un elemento desplazado en la vista, en iOS y Android](scrolling-images/scrolltoposition-end.png "Lista de CollectionView vertical con elemento desplazado")](scrolling-images/scrolltoposition-end-large.png#lightbox "Lista de CollectionView vertical con elemento desplazado")

## <a name="control-scroll-position-when-new-items-are-added"></a>Controlar la posición de desplazamiento cuando se agregan nuevos elementos

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define una propiedad `ItemsUpdatingScrollMode`, que está respaldada por una propiedad enlazable. Esta propiedad obtiene o establece un valor de enumeración `ItemsUpdatingScrollMode` que representa el comportamiento de desplazamiento del `CollectionView` cuando se agregan nuevos elementos a él. La enumeración `ItemsUpdatingScrollMode` define los miembros siguientes:

- `KeepItemsInView` ajusta el desplazamiento de desplazamiento para mantener el primer elemento visible que se muestra cuando se agregan nuevos elementos.
- `KeepScrollOffset` mantiene el desplazamiento de desplazamiento en relación con el principio de la lista cuando se agregan nuevos elementos.
- `KeepLastItemInView` ajusta el desplazamiento de desplazamiento para mantener visible el último elemento cuando se agregan nuevos elementos.

El valor predeterminado de la propiedad `ItemsUpdatingScrollMode` es `KeepItemsInView`. Por lo tanto, cuando se agreguen nuevos elementos a un [`CollectionView`](xref:Xamarin.Forms.CollectionView) el primer elemento visible de la lista seguirá mostrándose. Para asegurarse de que los elementos recién agregados siempre estén visibles en la parte inferior de la lista, la propiedad `ItemsUpdatingScrollMode` debe establecerse en `KeepLastItemInView`:

```xaml
<CollectionView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>Visibilidad de la barra de desplazamiento

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define las propiedades `HorizontalScrollBarVisibility` y `VerticalScrollBarVisibility`, que están respaldadas por propiedades enlazables. Estas propiedades obtienen o establecen un valor de enumeración [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) que representa Cuándo está visible la barra de desplazamiento horizontal o vertical. La enumeración `ScrollBarVisibility` define los miembros siguientes:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) indica el comportamiento predeterminado de la barra de desplazamiento para la plataforma y es el valor predeterminado para las propiedades `HorizontalScrollBarVisibility` y `VerticalScrollBarVisibility`.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) indica que las barras de desplazamiento serán visibles, incluso cuando el contenido quepa en la vista.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) indica que las barras de desplazamiento no estarán visibles, incluso si el contenido no cabe en la vista.

## <a name="snap-points"></a>Puntos de acoplamiento

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Esta característica se conoce como ajuste, ya que los elementos se ajustan a la posición cuando se detiene el desplazamiento y se controlan mediante las siguientes propiedades de la clase [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) :

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), especifica el comportamiento de los puntos de ajuste al desplazarse.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), especifica cómo se alinean los puntos de ajuste con los elementos.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que las propiedades pueden ser destinos de enlaces de datos.

> [!NOTE]
> Cuando se produce el ajuste, se producirá en la dirección que produce la menor cantidad de movimiento.

### <a name="snap-points-type"></a>Tipo de puntos de ajuste

La enumeración [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) define los siguientes miembros:

- `None` indica que el desplazamiento no se ajusta a los elementos.
- `Mandatory` indica que el contenido se ajusta siempre al punto de acoplamiento más cercano a donde se detendría el desplazamiento de forma natural, a lo largo de la dirección de inercia.
- `MandatorySingle` indica el mismo comportamiento que `Mandatory`, pero solo desplaza un elemento cada vez.

De forma predeterminada, la propiedad [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) está establecida en `SnapPointsType.None`, lo que garantiza que el desplazamiento no ajusta los elementos, como se muestra en las siguientes capturas de pantallas:

[![Captura de pantalla de una lista de CollectionView vertical sin puntos de acoplamiento, en iOS y Android](scrolling-images/snappoints-none.png "Lista vertical de CollectionView sin puntos de acoplamiento")](scrolling-images/snappoints-none-large.png#lightbox "Lista vertical de CollectionView sin puntos de acoplamiento")

### <a name="snap-points-alignment"></a>Alineación de puntos de acoplamiento

La enumeración [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) define `Start`, `Center` y `End` miembros.

> [!IMPORTANT]
> El valor de la propiedad [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) solo se respeta cuando la propiedad [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) se establece en `Mandatory` o `MandatorySingle`.

#### <a name="start"></a>Iniciar

El miembro `SnapPointsAlignment.Start` indica que los puntos de ajuste están alineados con el borde inicial de los elementos.

De forma predeterminada, la propiedad [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) está establecida en `SnapPointsAlignment.Start`. Sin embargo, por integridad, en el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

Cuando un usuario desliza el dedo para iniciar un desplazamiento, el elemento superior se alinea con la parte superior de la vista:

[![Captura de pantalla de una lista de CollectionView vertical con puntos de acoplamiento de inicio, en iOS y Android](scrolling-images/snappoints-start.png "Lista de CollectionViews verticales con puntos de acoplamiento iniciales")](scrolling-images/snappoints-start-large.png#lightbox "Lista de CollectionViews verticales con puntos de acoplamiento iniciales")

#### <a name="center"></a>Centrar

El miembro `SnapPointsAlignment.Center` indica que los puntos de ajuste están alineados con el centro de los elementos. En el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

Cuando un usuario desliza el dedo para iniciar un desplazamiento, el elemento superior se alineará en el centro de la parte superior de la vista:

[![Captura de pantalla de una lista de CollectionView vertical con puntos de acoplamiento, en iOS y Android](scrolling-images/snappoints-center.png "Lista vertical de CollectionView con puntos de acoplamiento")](scrolling-images/snappoints-center-large.png#lightbox "Lista vertical de CollectionView con puntos de acoplamiento")

#### <a name="end"></a>Fin

El miembro `SnapPointsAlignment.End` indica que los puntos de ajuste están alineados con el borde final de los elementos. En el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

Cuando un usuario desliza el dedo para iniciar un desplazamiento, el elemento inferior se alinea con la parte inferior de la vista:

[![Captura de pantalla de una lista de CollectionView vertical con puntos de acoplamiento finales, en iOS y Android](scrolling-images/snappoints-end.png "Lista vertical de CollectionView con puntos de ajuste finales")](scrolling-images/snappoints-end-large.png#lightbox "Lista vertical de CollectionView con puntos de ajuste finales")

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
