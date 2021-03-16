---
title: Xamarin.Forms Desplazamientos de CollectionView
description: Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Además, CollectionView define dos métodos ScrollTo, que desplazan los elementos mediante programación a la vista.
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/15/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aa9b43c6a41e48557807270542efa3a1121ed0b5
ms.sourcegitcommit: 9e3ebcd0bd96dd7f7d716e105504e97a7f848c01
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/16/2021
ms.locfileid: "103557961"
---
# <a name="xamarinforms-collectionview-scrolling"></a>Xamarin.Forms Desplazamientos de CollectionView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define dos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos que desplazan los elementos en la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado a la vista. Ambas sobrecargas tienen argumentos adicionales que se pueden especificar para indicar el grupo al que pertenece el elemento, la posición exacta del elemento una vez que se ha completado el desplazamiento y si se debe animar el desplazamiento.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define un [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento que se desencadena cuando se invoca a uno de los [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos. El [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) objeto que acompaña al `ScrollToRequested` evento tiene muchas propiedades, entre las que se incluyen `IsAnimated` , `Index` , `Item` y `ScrollToPosition` . Estas propiedades se establecen a partir de los argumentos especificados en las `ScrollTo` llamadas de método.

Además, [`CollectionView`](xref:Xamarin.Forms.CollectionView) define un `Scrolled` evento que se desencadena para indicar que se ha producido el desplazamiento. El `ItemsViewScrolledEventArgs` objeto que acompaña al `Scrolled` evento tiene muchas propiedades. Para obtener más información, vea [detectar desplazamiento](#detect-scrolling).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) también define una `ItemsUpdatingScrollMode` propiedad que representa el comportamiento de desplazamiento de `CollectionView` cuando se les agregan nuevos elementos. Para obtener más información sobre esta propiedad, vea [control de posición de desplazamiento cuando se agregan nuevos elementos](#control-scroll-position-when-new-items-are-added).

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Esta característica se conoce como ajuste, ya que los elementos se ajustan a la posición cuando se detiene el desplazamiento. Para obtener más información, vea [puntos de acoplamiento](#snap-points).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) también puede cargar datos de forma incremental a medida que el usuario se desplaza. Para obtener más información, vea [cargar datos de forma incremental](populate-data.md#load-data-incrementally).

## <a name="detect-scrolling"></a>Detectar desplazamiento

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define un `Scrolled` evento que se desencadena para indicar que se ha producido el desplazamiento. La `ItemsViewScrolledEventArgs` clase, que representa el objeto que acompaña al `Scrolled` evento, define las siguientes propiedades:

- `HorizontalDelta`, de tipo `double` , representa el cambio en la cantidad de desplazamiento horizontal. Es un valor negativo al desplazarse a la izquierda y un valor positivo al desplazarse a la derecha.
- `VerticalDelta`, de tipo `double` , representa el cambio en la cantidad de desplazamiento vertical. Se trata de un valor negativo al desplazarse hacia arriba y un valor positivo al desplazarse hacia abajo.
- `HorizontalOffset`, de tipo `double` , define la cantidad por la que la lista se desplaza horizontalmente desde su origen.
- `VerticalOffset`, de tipo `double` , define la cantidad por la que la lista se desplaza verticalmente desde su origen.
- `FirstVisibleItemIndex`, de tipo `int` , es el índice del primer elemento que está visible en la lista.
- `CenterItemIndex`, de tipo `int` , es el índice del elemento del centro que está visible en la lista.
- `LastVisibleItemIndex`, de tipo `int` , es el índice del último elemento que está visible en la lista.

En el ejemplo de XAML siguiente se muestra un `CollectionView` que establece un controlador de eventos para el `Scrolled` evento:

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

En este ejemplo de código, el `OnCollectionViewScrolled` controlador de eventos se ejecuta cuando se `Scrolled` desencadena el evento:

```csharp
void OnCollectionViewScrolled(object sender, ItemsViewScrolledEventArgs e)
{
    // Custom logic
}
```

> [!IMPORTANT]
> El `Scrolled` evento se desencadena para los desplazamientos iniciados por el usuario y para los desplazamientos mediante programación.

## <a name="scroll-an-item-at-an-index-into-view"></a>Desplazar un elemento en un índice en la vista

La primera [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga del método desplaza el elemento en el índice especificado en la vista. Dado un [`CollectionView`](xref:Xamarin.Forms.CollectionView) objeto denominado `collectionView` , en el ejemplo siguiente se muestra cómo desplazar el elemento en el índice 12 a la vista:

```csharp
collectionView.ScrollTo(12);
```

Como alternativa, un elemento de los datos agrupados se puede desplazar en la vista especificando los índices de elemento y de grupo. En el ejemplo siguiente se muestra cómo desplazar el tercer elemento del segundo grupo a la vista:

```csharp
// Items and groups are indexed from zero.
collectionView.ScrollTo(2, 1);
```

> [!NOTE]
> El [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento se desencadena cuando [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) se invoca el método.

## <a name="scroll-an-item-into-view"></a>Desplazar un elemento a la vista

La segunda [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) sobrecarga del método desplaza el elemento especificado a la vista. Dado un [`CollectionView`](xref:Xamarin.Forms.CollectionView) objeto denominado `collectionView` , en el ejemplo siguiente se muestra cómo desplazar el elemento Proboscis Monkey a la vista:

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
> El [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) evento se desencadena cuando [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) se invoca el método.

## <a name="disable-scroll-animation"></a>Deshabilitar animación de desplazamiento

Cuando se desplaza un elemento a la vista, se muestra una animación de desplazamiento. Sin embargo, esta animación se puede deshabilitar estableciendo el `animate` argumento del `ScrollTo` método en `false` :

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>Posición de desplazamiento de control

Al desplazar un elemento a la vista, se puede especificar la posición exacta del elemento una vez completado el desplazamiento con el `position` argumento de los [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) métodos. Este argumento acepta un [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) miembro de enumeración.

### <a name="makevisible"></a>MakeVisible

El [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento debe desplazarse hasta que esté visible en la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Este código de ejemplo produce el desplazamiento mínimo necesario para desplazar el elemento a la vista:

[![Captura de pantalla de una lista de CollectionView vertical con ScrollToPosition. MakeVisible, en iOS y Android](scrolling-images/scrolltoposition-makevisible.png "Lista de CollectionView vertical con elemento desplazado")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "Lista de CollectionView vertical con elemento desplazado")

> [!NOTE]
> El [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) miembro se utiliza de forma predeterminada si `position` no se especifica el argumento al llamar al `ScrollTo` método.

### <a name="start"></a>Start

El [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento debe desplazarse hasta el inicio de la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Este código de ejemplo hace que el elemento se desplace hasta el inicio de la vista:

[![Captura de pantalla de una lista de CollectionView vertical con ScrollToPosition. Start, en iOS y Android](scrolling-images/scrolltoposition-start.png "Lista de CollectionView vertical con elemento desplazado")](scrolling-images/scrolltoposition-start-large.png#lightbox "Lista de CollectionView vertical con elemento desplazado")

### <a name="center"></a>Center

El [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento debe desplazarse hasta el centro de la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Este código de ejemplo hace que el elemento se desplace hasta el centro de la vista:

[![Captura de pantalla de una lista de CollectionView vertical con ScrollToPosition. Center, en iOS y Android](scrolling-images/scrolltoposition-center.png "Lista de CollectionView vertical con elemento desplazado")](scrolling-images/scrolltoposition-center-large.png#lightbox "Lista de CollectionView vertical con elemento desplazado")

### <a name="end"></a>End

El [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) miembro indica que el elemento debe desplazarse hasta el final de la vista:

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Este código de ejemplo hace que el elemento se desplace hasta el final de la vista:

[![Captura de pantalla de una lista de CollectionView vertical con ScrollToPosition. end, en iOS y Android](scrolling-images/scrolltoposition-end.png "Lista de CollectionView vertical con elemento desplazado")](scrolling-images/scrolltoposition-end-large.png#lightbox "Lista de CollectionView vertical con elemento desplazado")

## <a name="control-scroll-position-when-new-items-are-added"></a>Controlar la posición de desplazamiento cuando se agregan nuevos elementos

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define una `ItemsUpdatingScrollMode` propiedad, que está respaldada por una propiedad enlazable. Esta propiedad obtiene o establece un `ItemsUpdatingScrollMode` valor de enumeración que representa el comportamiento de desplazamiento de `CollectionView` cuando se agregan nuevos elementos a él. La enumeración `ItemsUpdatingScrollMode` define los miembros siguientes:

- `KeepItemsInView` mantiene el primer elemento de la lista que se muestra cuando se agregan nuevos elementos.
- `KeepScrollOffset` garantiza que la posición de desplazamiento actual se mantiene cuando se agregan nuevos elementos.
- `KeepLastItemInView` ajusta el desplazamiento de desplazamiento para mantener el último elemento de la lista que se muestra cuando se agregan nuevos elementos.

El valor predeterminado de la `ItemsUpdatingScrollMode` propiedad es `KeepItemsInView` . Por lo tanto, cuando se agreguen nuevos elementos al [`CollectionView`](xref:Xamarin.Forms.CollectionView) primer elemento de la lista, se mostrarán. Para asegurarse de que el último elemento de la lista se muestra cuando se agregan nuevos elementos, establezca la `ItemsUpdatingScrollMode` propiedad en `KeepLastItemInView` :

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

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` las propiedades y, que están respaldadas por propiedades enlazables. Estas propiedades obtienen o establecen un [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valor de enumeración que representa Cuándo está visible la barra de desplazamiento horizontal o vertical. La enumeración `ScrollBarVisibility` define los miembros siguientes:

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

De forma predeterminada, la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propiedad se establece en `SnapPointsType.None` , lo que garantiza que el desplazamiento no ajusta los elementos, como se muestra en las siguientes capturas de pantallas:

[![Captura de pantalla de una lista de CollectionView vertical sin puntos de acoplamiento, en iOS y Android](scrolling-images/snappoints-none.png "Lista vertical de CollectionView sin puntos de acoplamiento")](scrolling-images/snappoints-none-large.png#lightbox "Lista vertical de CollectionView sin puntos de acoplamiento")

### <a name="snap-points-alignment"></a>Alineación de puntos de acoplamiento

La [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) enumeración `Start` define `Center` `End` los miembros, y.

> [!IMPORTANT]
> El valor de la [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propiedad solo se respeta cuando la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propiedad está establecida en `Mandatory` , o `MandatorySingle` .

#### <a name="start"></a>Start

El `SnapPointsAlignment.Start` miembro indica que los puntos de ajuste están alineados con el borde inicial de los elementos.

De forma predeterminada, la [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propiedad se establece en `SnapPointsAlignment.Start` . Sin embargo, por integridad, en el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

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

#### <a name="center"></a>Center

El `SnapPointsAlignment.Center` miembro indica que los puntos de ajuste están alineados con el centro de los elementos. En el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

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

#### <a name="end"></a>End

El `SnapPointsAlignment.End` miembro indica que los puntos de ajuste están alineados con el borde final de los elementos. En el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

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

- [CollectionView (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
