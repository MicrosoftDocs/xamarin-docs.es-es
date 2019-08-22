---
title: Desplazamientos de CollectionView de Xamarin. Forms
description: Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Además, CollectionView define dos métodos ScrollTo, que desplazan los elementos mediante programación a la vista.
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2019
ms.openlocfilehash: 303266f44664f7f57aeaf36869a3a06c8eb91870
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888650"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Desplazamientos de CollectionView de Xamarin. Forms

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define dos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos que desplazan los elementos en la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado a la vista. Ambas sobrecargas tienen argumentos adicionales que se pueden especificar para indicar la posición exacta del elemento una vez que se ha completado el desplazamiento y si se debe animar el desplazamiento.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define un [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento que se desencadena cuando se invoca a [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) uno de los métodos. El [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) objeto que acompaña al `ScrollToRequested` evento tiene muchas propiedades, entre las `IsAnimated`que se `Item`incluyen, `ScrollToPosition` `Index`, y. Estas propiedades se establecen a partir de los argumentos especificados en las `ScrollTo` llamadas de método.

Además, [`CollectionView`](xref:Xamarin.Forms.CollectionView) define un evento `Scrolled` que se desencadena para indicar que se ha producido el desplazamiento. El `ItemsViewScrolledEventArgs` objeto que acompaña al `Scrolled` evento tiene muchas propiedades. Para obtener más información, vea [detectar desplazamiento](#detect-scrolling).

[`CollectionView`](xref:Xamarin.Forms.CollectionView)también define una `ItemsUpdatingScrollMode` propiedad que representa el comportamiento `CollectionView` de desplazamiento de cuando se les agregan nuevos elementos. Para obtener más información sobre esta propiedad, vea [control de posición de desplazamiento cuando se agregan nuevos elementos](#control-scroll-position-when-new-items-are-added).

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Esta característica se conoce como ajuste, ya que los elementos se ajustan a la posición cuando se detiene el desplazamiento. Para obtener más información, vea [puntos de acoplamiento](#snap-points).

[`CollectionView`](xref:Xamarin.Forms.CollectionView)también puede cargar datos de forma incremental a medida que el usuario se desplaza. Para obtener más información, vea [cargar datos](populate-data.md#load-data-incrementally)de forma incremental.

## <a name="detect-scrolling"></a>Detectar desplazamiento

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define un `Scrolled` evento que se desencadena para indicar que se ha producido el desplazamiento. En el ejemplo de XAML siguiente `CollectionView` se muestra un que establece un controlador `Scrolled` de eventos para el evento:

```xaml
<CollectionView Scrolled="OnCollectionViewScrolled">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView();
collectionView.Scrolled += OnCollectionViewScrolled;
```

En este ejemplo de código, `OnCollectionViewScrolled` el controlador de eventos se ejecuta `Scrolled` cuando se desencadena el evento:

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

El `OnCollectionViewScrolled` controlador`ItemsViewScrolledEventArgs` de eventos genera los valores del objeto que acompaña al evento.

> [!IMPORTANT]
> El `Scrolled` evento se desencadena para los desplazamientos iniciados por el usuario y para los desplazamientos mediante programación.

## <a name="scroll-an-item-at-an-index-into-view"></a>Desplazar un elemento en un índice en la vista

La primera [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga del método desplaza el elemento en el índice especificado en la vista. Dado un [`CollectionView`](xref:Xamarin.Forms.CollectionView) objeto denominado `collectionView`, en el ejemplo siguiente se muestra cómo desplazar el elemento en el índice 12 a la vista:

```csharp
collectionView.ScrollTo(12);
```

> [!NOTE]
> El [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento se desencadena cuando se [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) invoca el método.

## <a name="scroll-an-item-into-view"></a>Desplazar un elemento a la vista

La segunda [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga del método desplaza el elemento especificado a la vista. Dado un [`CollectionView`](xref:Xamarin.Forms.CollectionView) objeto denominado `collectionView`, en el ejemplo siguiente se muestra cómo desplazar el elemento especificado a la vista:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

> [!NOTE]
> El [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento se desencadena cuando se [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) invoca el método.

## <a name="scroll-bar-visibility"></a>Visibilidad de la barra de desplazamiento

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define `HorizontalScrollBarVisibility` las `VerticalScrollBarVisibility` propiedades y, que están respaldadas por propiedades enlazables. Estas propiedades obtienen o establecen un [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valor de enumeración que representa Cuándo está visible la barra de desplazamiento horizontal o vertical. La enumeración `ScrollBarVisibility` define los miembros siguientes:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility)indica el comportamiento predeterminado de la barra de desplazamiento para la plataforma y es el valor predeterminado `HorizontalScrollBarVisibility` para `VerticalScrollBarVisibility` las propiedades y.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility)indica que las barras de desplazamiento serán visibles, incluso cuando el contenido quepa en la vista.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility)indica que las barras de desplazamiento no estarán visibles, incluso si el contenido no cabe en la vista.

## <a name="control-scroll-position"></a>Posición de desplazamiento de control

Al desplazar un elemento a la vista, se puede especificar la posición exacta del elemento una vez completado el desplazamiento con `position` el argumento de [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) los métodos. Este argumento acepta un [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) miembro de enumeración.

### <a name="makevisible"></a>MakeVisible

El [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento debe desplazarse hasta que esté visible en la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Este código de ejemplo produce el desplazamiento mínimo necesario para desplazar el elemento a la vista:

[ ![Captura de pantalla de una lista de CollectionView vertical con un elemento desplazado en la vista, en iOS y Android CollectionView,](scrolling-images/scrolltoposition-makevisible.png "lista vertical con elemento") ] desplazado (scrolling-images/scrolltoposition-makevisible-large.png#lightbox "Lista de CollectionView vertical con elemento") desplazado

> [!NOTE]
> El [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) miembro se utiliza de forma predeterminada si no `position` se especifica el argumento al llamar al `ScrollTo` método.

### <a name="start"></a>Iniciar

El [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento debe desplazarse hasta el inicio de la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Este código de ejemplo hace que el elemento se desplace hasta el inicio de la vista:

[ ![Captura de pantalla de una lista de CollectionView vertical con un elemento desplazado en la vista, en iOS y Android CollectionView,](scrolling-images/scrolltoposition-start.png "lista vertical con elemento") ] desplazado (scrolling-images/scrolltoposition-start-large.png#lightbox "Lista de CollectionView vertical con elemento") desplazado

### <a name="center"></a>Centrar

El [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento debe desplazarse hasta el centro de la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Este código de ejemplo hace que el elemento se desplace hasta el centro de la vista:

[ ![Captura de pantalla de una lista de CollectionView vertical con un elemento desplazado en la vista, en iOS y Android CollectionView,](scrolling-images/scrolltoposition-center.png "lista vertical con elemento") ] desplazado (scrolling-images/scrolltoposition-center-large.png#lightbox "Lista de CollectionView vertical con elemento") desplazado

### <a name="end"></a>Fin

El [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento debe desplazarse hasta el final de la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Este código de ejemplo hace que el elemento se desplace hasta el final de la vista:

[ ![Captura de pantalla de una lista de CollectionView vertical con un elemento desplazado en la vista, en iOS y Android CollectionView,](scrolling-images/scrolltoposition-end.png "lista vertical con elemento") ] desplazado (scrolling-images/scrolltoposition-end-large.png#lightbox "Lista de CollectionView vertical con elemento") desplazado

## <a name="disable-scroll-animation"></a>Deshabilitar animación de desplazamiento

Cuando se desplaza un elemento a la vista, se muestra una animación de desplazamiento. Sin embargo, esta animación se puede deshabilitar estableciendo `animate` el argumento `ScrollTo` del método en `false`:

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position-when-new-items-are-added"></a>Controlar la posición de desplazamiento cuando se agregan nuevos elementos

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define una `ItemsUpdatingScrollMode` propiedad, que está respaldada por una propiedad enlazable. Esta propiedad obtiene o establece un `ItemsUpdatingScrollMode` valor `CollectionView` de enumeración que representa el comportamiento de desplazamiento de cuando se agregan nuevos elementos a él. La enumeración `ItemsUpdatingScrollMode` define los miembros siguientes:

- `KeepItemsInView`ajusta el desplazamiento de desplazamiento para mantener el primer elemento visible que se muestra cuando se agregan nuevos elementos.
- `KeepScrollOffset`mantiene el desplazamiento de desplazamiento con respecto al principio de la lista cuando se agregan nuevos elementos.
- `KeepLastItemInView`ajusta el desplazamiento de desplazamiento para mantener visible el último elemento cuando se agregan nuevos elementos.

El valor predeterminado de la `ItemsUpdatingScrollMode` propiedad es `KeepItemsInView`. Por lo tanto, cuando se agreguen nuevos [`CollectionView`](xref:Xamarin.Forms.CollectionView) elementos a un primer elemento visible de la lista, se mostrarán. Para asegurarse de que los elementos recién agregados siempre estén visibles en la parte inferior de `ItemsUpdatingScrollMode` la lista, la propiedad `KeepLastItemInView`debe establecerse en:

```xaml
<CollectionView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="snap-points"></a>Puntos de acoplamiento

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Esta característica se conoce como ajuste, ya que los elementos se ajustan a la posición cuando se detiene el desplazamiento y se controlan mediante [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) las siguientes propiedades de la clase:

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), especifica el comportamiento de los puntos de ajuste al desplazarse.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), especifica cómo se alinean los puntos de ajuste con los elementos.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

> [!NOTE]
> Cuando se produce el ajuste, se producirá en la dirección que produce la menor cantidad de movimiento.

### <a name="snap-points-type"></a>Tipo de puntos de ajuste

La [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) enumeración define los siguientes miembros:

- `None`indica que el desplazamiento no se ajusta a los elementos.
- `Mandatory`indica que el contenido se ajusta siempre al punto de acoplamiento más cercano a donde se detendría el desplazamiento de forma natural, a lo largo de la dirección de inercia.
- `MandatorySingle`indica el mismo comportamiento que `Mandatory`, pero solo desplaza un elemento cada vez.

De forma predeterminada, [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) la propiedad se establece `SnapPointsType.None`en, lo que garantiza que el desplazamiento no ajusta los elementos, como se muestra en las siguientes capturas de pantallas:

[ ![Captura de pantalla de una lista de CollectionView vertical sin puntos de acoplamiento, en la lista vertical de CollectionView de iOS y Android](scrolling-images/snappoints-none.png "sin puntos de acoplamiento") ] (scrolling-images/snappoints-none-large.png#lightbox "Lista vertical de CollectionView sin puntos de acoplamiento")

### <a name="snap-points-alignment"></a>Alineación de puntos de acoplamiento

La [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) enumeración `Start`define `Center`los miembros `End` , y.

> [!IMPORTANT]
> El valor de la [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propiedad solo se respeta cuando la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propiedad está establecida en `Mandatory`, o `MandatorySingle`.

#### <a name="start"></a>Iniciar

El `SnapPointsAlignment.Start` miembro indica que los puntos de ajuste están alineados con el borde inicial de los elementos.

De forma predeterminada, [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) la propiedad se establece `SnapPointsAlignment.Start`en. Sin embargo, por integridad, en el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="Start">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

Cuando un usuario desliza el dedo para iniciar un desplazamiento, el elemento superior se alinea con la parte superior de la vista:

[ ![Captura de pantalla de una lista de CollectionView vertical con puntos de inicio, en iOS y Android,](scrolling-images/snappoints-start.png "lista vertical de CollectionView con puntos de ajuste iniciales") ] (scrolling-images/snappoints-start-large.png#lightbox "Lista de collectionviews verticales con puntos de acoplamiento iniciales")

#### <a name="center"></a>Centrar

El `SnapPointsAlignment.Center` miembro indica que los puntos de ajuste están alineados con el centro de los elementos. En el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="Center">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

Cuando un usuario desliza el dedo para iniciar un desplazamiento, el elemento superior se alineará en el centro de la parte superior de la vista:

[ ![Captura de pantalla de una lista vertical de CollectionView con puntos de acoplamiento, en la lista vertical de la CollectionView de iOS y Android](scrolling-images/snappoints-center.png "con puntos de acoplamiento") ] (scrolling-images/snappoints-center-large.png#lightbox "Lista vertical de CollectionView con puntos de acoplamiento")

#### <a name="end"></a>Fin

El `SnapPointsAlignment.End` miembro indica que los puntos de ajuste están alineados con el borde final de los elementos. En el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

```xaml
<CollectionView x:Name="collectionView"
                ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout SnapPointsType="MandatorySingle"
                         SnapPointsAlignment="End">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            ...
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    ItemTemplate = new DataTemplate(() =>
    {
        return null;
    })
};
```

Cuando un usuario desliza el dedo para iniciar un desplazamiento, el elemento inferior se alinea con la parte inferior de la vista:

[ ![Captura de pantalla de una lista de CollectionView vertical con puntos de acoplamiento finales, en iOS y Android,](scrolling-images/snappoints-end.png "lista vertical de CollectionView con puntos de ajuste finales") ] (scrolling-images/snappoints-end-large.png#lightbox "Lista vertical de CollectionView con puntos de ajuste finales")

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
