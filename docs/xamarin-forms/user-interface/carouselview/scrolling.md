---
title: Desplazamiento de CarouselView de Xamarin. Forms
description: Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Además, CarouselView define dos métodos ScrollTo, que desplazan los elementos mediante programación a la vista.
ms.prod: xamarin
ms.assetid: 92D7B618-07FA-4343-9D0F-212525E92C39
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/14/2019
ms.openlocfilehash: dc72dc7549a697c7231045601851ba4108f29e1b
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697852"
---
# <a name="xamarinforms-carouselview-scrolling"></a>Desplazamiento de CarouselView de Xamarin. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define las siguientes propiedades relacionadas con el desplazamiento:

- `HorizontalScrollBarVisibility`, de tipo `ScrollBarVisibility`, que especifica cuándo está visible la barra de desplazamiento horizontal.
- `IsDragging`, de tipo `bool`, que indica si el `CarouselView` se está desplazando. Se trata de una propiedad de solo lectura, cuyo valor predeterminado es `false`.
- `IsScrollAnimated`, de tipo `bool`, que especifica si se producirá una animación al desplazarse por el `CarouselView`. El valor predeterminado es `true`.
- `ItemsUpdatingScrollMode`, de tipo `ItemsUpdatingScrollMode`, que representa el comportamiento de desplazamiento de la `CarouselView` cuando se agregan nuevos elementos a ella.
- `VerticalScrollBarVisibility`, de tipo `ScrollBarVisibility`, que especifica cuándo está visible la barra de desplazamiento vertical.

Todas estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que pueden ser destinos de los enlaces de datos.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) también define dos métodos de [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , que desplazan los elementos a la vista. Una de las sobrecargas desplaza el elemento en el índice especificado en la vista, mientras que el otro desplaza el elemento especificado a la vista. Ambas sobrecargas tienen argumentos adicionales que se pueden especificar para indicar la posición exacta del elemento una vez que se ha completado el desplazamiento y si se debe animar el desplazamiento.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define un evento [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) que se desencadena cuando se invoca a uno de los métodos [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) . El objeto [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) que acompaña al evento `ScrollToRequested` tiene muchas propiedades, como `IsAnimated`, `Index`, `Item` y `ScrollToPosition`. Estas propiedades se establecen a partir de los argumentos especificados en el método `ScrollTo` llama a.

Además, [`CarouselView`](xref:Xamarin.Forms.CarouselView) define un evento `Scrolled` que se desencadena para indicar que se ha producido el desplazamiento. El objeto `ItemsViewScrolledEventArgs` que acompaña al evento `Scrolled` tiene muchas propiedades. Para obtener más información, vea [detectar desplazamiento](#detect-scrolling).

Cuando un usuario desliza el dedo para iniciar un desplazamiento, se puede controlar la posición final del desplazamiento para que los elementos se muestren por completo. Esta característica se conoce como ajuste, ya que los elementos se ajustan a la posición cuando se detiene el desplazamiento. Para obtener más información, vea [puntos de acoplamiento](#snap-points).

[`CarouselView`](xref:Xamarin.Forms.CarouselView) también puede cargar datos de forma incremental a medida que el usuario se desplaza. Para obtener más información, vea [cargar datos de forma incremental](populate-data.md#load-data-incrementally).

## <a name="detect-scrolling"></a>Detectar desplazamiento

Se puede examinar la propiedad `IsDragging` para determinar si el [`CarouselView`](xref:Xamarin.Forms.CarouselView) se desplaza actualmente por los elementos.

Además, [`CarouselView`](xref:Xamarin.Forms.CarouselView) define un evento `Scrolled` que se desencadena para indicar que se ha producido el desplazamiento. Este evento se debe usar cuando se requieren datos sobre el desplazamiento.

En el ejemplo de XAML siguiente se muestra un `CarouselView` que establece un controlador de eventos para el evento `Scrolled`:

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

En este ejemplo de código, se ejecuta el controlador de eventos `OnCarouselViewScrolled` cuando se activa el evento `Scrolled`:

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

En este ejemplo, el controlador de eventos `OnCarouselViewScrolled` devuelve los valores del objeto `ItemsViewScrolledEventArgs` que acompaña al evento.

> [!IMPORTANT]
> El evento `Scrolled` se desencadena para los desplazamientos iniciados por el usuario y para los desplazamientos mediante programación.

## <a name="scroll-an-item-at-an-index-into-view"></a>Desplazar un elemento en un índice en la vista

La primera sobrecarga del método [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) desplaza el elemento en el índice especificado en la vista. Dado un [`CarouselView`](xref:Xamarin.Forms.CarouselView) objeto denominado `carouselView`, en el ejemplo siguiente se muestra cómo desplazar el elemento en el índice 6 a la vista:

```csharp
carouselView.ScrollTo(6);
```

> [!NOTE]
> El evento [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) se desencadena cuando se invoca el método [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) .

## <a name="scroll-an-item-into-view"></a>Desplazar un elemento a la vista

La segunda sobrecarga del método [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) desplaza el elemento especificado a la vista. Dado un [`CarouselView`](xref:Xamarin.Forms.CarouselView) objeto denominado `carouselView`, en el ejemplo siguiente se muestra cómo desplazar el elemento Proboscis Monkey a la vista:

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
carouselView.ScrollTo(monkey);
```

> [!NOTE]
> El evento [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) se desencadena cuando se invoca el método [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) .

## <a name="disable-scroll-animation"></a>Deshabilitar animación de desplazamiento

Se muestra una animación de desplazamiento al moverse entre los elementos de una [`CarouselView`](xref:Xamarin.Forms.CarouselView). Esta animación se produce para los desplazamientos iniciados por el usuario y para los desplazamientos mediante programación. Al establecer la propiedad `IsScrollAnimated` en `false`, se deshabilitará la animación para ambas categorías de desplazamiento.

Como alternativa, el argumento `animate` del método `ScrollTo` se puede establecer en `false` para deshabilitar la animación de desplazamiento en desplazamientos mediante programación:

```csharp
carouselView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>Posición de desplazamiento de control

Al desplazar un elemento a la vista, se puede especificar la posición exacta del elemento una vez completado el desplazamiento con el `position` argumento de los métodos de [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) . Este argumento acepta un miembro de enumeración de [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) .

### <a name="makevisible"></a>MakeVisible

El miembro [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) indica que el elemento debe desplazarse hasta que esté visible en la vista:

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Este código de ejemplo produce el desplazamiento mínimo necesario para desplazar el elemento a la vista.

> [!NOTE]
> De forma predeterminada, se utiliza el miembro [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) , si no se especifica el argumento `position` al llamar al método `ScrollTo`.

### <a name="start"></a>Iniciar

El miembro [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) indica que el elemento debe desplazarse hasta el inicio de la vista:

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Este código de ejemplo hace que el elemento se desplace hasta el inicio de la vista.

### <a name="center"></a>Centrar

El miembro [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) indica que el elemento debe desplazarse hasta el centro de la vista:

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Este código de ejemplo hace que el elemento se desplace hasta el centro de la vista.

### <a name="end"></a>Fin

El miembro [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) indica que el elemento debe desplazarse hasta el final de la vista:

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Este código de ejemplo hace que el elemento se desplace hasta el final de la vista.

## <a name="control-scroll-position-when-new-items-are-added"></a>Controlar la posición de desplazamiento cuando se agregan nuevos elementos

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define una propiedad `ItemsUpdatingScrollMode`, que está respaldada por una propiedad enlazable. Esta propiedad obtiene o establece un valor de enumeración `ItemsUpdatingScrollMode` que representa el comportamiento de desplazamiento del `CarouselView` cuando se agregan nuevos elementos a él. La enumeración `ItemsUpdatingScrollMode` define los miembros siguientes:

- `KeepItemsInView` ajusta el desplazamiento de desplazamiento para mantener el primer elemento visible que se muestra cuando se agregan nuevos elementos.
- `KeepScrollOffset` mantiene el desplazamiento de desplazamiento en relación con el principio de la lista cuando se agregan nuevos elementos.
- `KeepLastItemInView` ajusta el desplazamiento de desplazamiento para mantener visible el último elemento cuando se agregan nuevos elementos.

El valor predeterminado de la propiedad `ItemsUpdatingScrollMode` es `KeepItemsInView`. Por lo tanto, cuando se agreguen nuevos elementos a un [`CarouselView`](xref:Xamarin.Forms.CarouselView) el primer elemento visible de la lista seguirá mostrándose. Para asegurarse de que los elementos recién agregados siempre estén visibles en la parte inferior de la lista, la propiedad `ItemsUpdatingScrollMode` debe establecerse en `KeepLastItemInView`:

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

[`CarouselView`](xref:Xamarin.Forms.CarouselView) define las propiedades `HorizontalScrollBarVisibility` y `VerticalScrollBarVisibility`, que están respaldadas por propiedades enlazables. Estas propiedades obtienen o establecen un valor de enumeración [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) que representa Cuándo está visible la barra de desplazamiento horizontal o vertical. La enumeración `ScrollBarVisibility` define los miembros siguientes:

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

De forma predeterminada en un [`CarouselView`](xref:Xamarin.Forms.CarouselView), la propiedad [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) se establece en `SnapPointsType.MandatorySingle`, lo que garantiza que el desplazamiento solo se desplaza un elemento cada vez.

### <a name="snap-points-alignment"></a>Alineación de puntos de acoplamiento

La enumeración [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) define `Start`, `Center` y `End` miembros.

> [!IMPORTANT]
> El valor de la propiedad [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) solo se respeta cuando la propiedad [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) se establece en `Mandatory` o `MandatorySingle`.

#### <a name="start"></a>Iniciar

El miembro `SnapPointsAlignment.Start` indica que los puntos de ajuste están alineados con el borde inicial de los elementos. En el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              NumberOfSideItems="1">
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

Cuando un usuario desliza el dedo para iniciar un desplazamiento en un [`CarouselView`](xref:Xamarin.Forms.CarouselView)de desplazamiento horizontal, el elemento de la izquierda se alineará a la izquierda de la vista.

#### <a name="center"></a>Centrar

El miembro `SnapPointsAlignment.Center` indica que los puntos de ajuste están alineados con el centro de los elementos.

De forma predeterminada, en un [`CarouselView`](xref:Xamarin.Forms.CarouselView), la propiedad [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) se establece en `Center`. Sin embargo, por integridad, en el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              NumberOfSideItems="1">
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

Cuando un usuario desliza el dedo para iniciar un desplazamiento en un [`CarouselView`](xref:Xamarin.Forms.CarouselView)de desplazamiento horizontal, el elemento central se alinea con el centro de la vista.

#### <a name="end"></a>Fin

El miembro `SnapPointsAlignment.End` indica que los puntos de ajuste están alineados con el borde final de los elementos. En el siguiente ejemplo de XAML se muestra cómo establecer este miembro de enumeración:

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              NumberOfSideItems="1">
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

Cuando un usuario desliza el dedo para iniciar un desplazamiento en un [`CarouselView`](xref:Xamarin.Forms.CarouselView)de desplazamiento horizontal, el elemento de la derecha se alineará a la derecha de la vista.

## <a name="related-links"></a>Vínculos relacionados

- [CarouselView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
