---
title: Diseño de CollectionView de Xamarin. Forms
description: De forma predeterminada, una CollectionView mostrará sus elementos en una lista vertical. Sin embargo, se pueden especificar listas y cuadrículas verticales y horizontales.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2019
ms.openlocfilehash: ac32e340212dd42c373a39df138436e7ee313958
ms.sourcegitcommit: 1341f2950b775a4daa7d0548a51fdef759afd6e3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69976534"
---
# <a name="xamarinforms-collectionview-layout"></a>Diseño de CollectionView de Xamarin. Forms

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define las siguientes propiedades que controlan el diseño:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), de tipo [`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout), especifica el diseño que se va a utilizar.
- [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy), de tipo [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy), especifica la estrategia de medida de elementos que se va a utilizar.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

De forma predeterminada, [`CollectionView`](xref:Xamarin.Forms.CollectionView) un mostrará sus elementos en una lista vertical. Sin embargo, se puede usar cualquiera de los siguientes diseños:

- Lista vertical: una lista de una sola columna que crece verticalmente a medida que se agregan nuevos elementos.
- Lista horizontal: una lista de una sola fila que crece horizontalmente a medida que se agregan nuevos elementos.
- Cuadrícula vertical: una cuadrícula de varias columnas que crece verticalmente a medida que se agregan nuevos elementos.
- Cuadrícula horizontal: una cuadrícula de varias filas que crece horizontalmente a medida que se agregan nuevos elementos.

Estos diseños se pueden especificar estableciendo la [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad en la clase que deriva de la [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) clase. Esta clase define las siguientes propiedades:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), de tipo [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation), especifica la dirección en la [`CollectionView`](xref:Xamarin.Forms.CollectionView) que se expande a medida que se agregan elementos.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), especifica cómo se alinean los puntos de ajuste con los elementos.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), especifica el comportamiento de los puntos de ajuste al desplazarse.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos. Para obtener más información sobre los puntos de acoplamiento, vea [puntos de acoplamiento](scrolling.md#snap-points) en la guía de desplazamientos de la [CollectionView de Xamarin. Forms](scrolling.md) .

La [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) enumeración define los siguientes miembros:

- `Vertical`indica que [`CollectionView`](xref:Xamarin.Forms.CollectionView) se expandirá verticalmente a medida que se agreguen elementos.
- `Horizontal`indica que [`CollectionView`](xref:Xamarin.Forms.CollectionView) se expandirá horizontalmente a medida que se agreguen elementos.

La [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) clase hereda de la [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) clase y define una `ItemSpacing` propiedad, de tipo `double`, que representa el espacio vacío alrededor de cada elemento. El valor predeterminado de esta propiedad es 0, y su valor siempre debe ser mayor o igual que 0. La `ListItemsLayout` clase también define los `Vertical` miembros `Horizontal` y estáticos. Estos miembros se pueden usar para crear listas verticales u horizontales, respectivamente. Como alternativa, se `ListItemsLayout` puede crear un objeto, especificando un [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) miembro de enumeración como argumento.

La [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) clase hereda de la [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) clase y define las siguientes propiedades:

- `VerticalItemSpacing`, de tipo `double`, que representa el espacio vacío vertical alrededor de cada elemento. El valor predeterminado de esta propiedad es 0, y su valor siempre debe ser mayor o igual que 0.
- `HorizontalItemSpacing`, de tipo `double`, que representa el espacio vacío horizontal alrededor de cada elemento. El valor predeterminado de esta propiedad es 0, y su valor siempre debe ser mayor o igual que 0.
- `Span`, de tipo `int`, que representa el número de columnas o filas que se van a mostrar en la cuadrícula. El valor predeterminado de esta propiedad es 1 y su valor siempre debe ser mayor o igual que 1.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)usa los motores de diseño nativo para realizar el diseño.

## <a name="vertical-list"></a>Lista vertical

De forma predeterminada [`CollectionView`](xref:Xamarin.Forms.CollectionView) , mostrará sus elementos en un diseño de lista vertical. Por lo tanto, no es necesario establecer la [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad para utilizar este diseño:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="Auto" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Sin embargo, por integridad, [`CollectionView`](xref:Xamarin.Forms.CollectionView) se puede establecer para mostrar sus elementos en una lista vertical estableciendo su [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad en `VerticalList`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

Como alternativa, también se puede lograr [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) estableciendo la propiedad en un objeto de la [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) clase, especificando el `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) miembro de la enumeración como `Orientation` el valor de la propiedad:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout Orientation="Vertical" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.Vertical
};
```

Esto da como resultado una lista de una sola columna, que crece verticalmente a medida que se agregan nuevos elementos:

[ ![Captura de pantalla de un diseño de lista vertical de CollectionView, en el diseño de](layout-images/vertical-list.png "lista vertical de CollectionView") de iOS y Android] (layout-images/vertical-list-large.png#lightbox "Diseño de lista vertical de CollectionView")

## <a name="horizontal-list"></a>Lista horizontal

[`CollectionView`](xref:Xamarin.Forms.CollectionView)puede mostrar sus elementos en una lista horizontal estableciendo su [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad en: `HorizontalList`

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="HorizontalList">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

Como alternativa, también se puede lograr [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) estableciendo la propiedad en un objeto de la [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) clase, especificando el `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) miembro de la enumeración como `Orientation` el valor de la propiedad:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout Orientation="Horizontal" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = ListItemsLayout.Horizontal
};
```

Esto da como resultado una lista de una sola fila, que crece horizontalmente a medida que se agregan nuevos elementos:

[ ![Captura de pantalla de un diseño de lista horizontal de CollectionView, en el diseño de](layout-images/horizontal-list.png "lista horizontal") de la CollectionView de iOS y Android] (layout-images/horizontal-list-large.png#lightbox "Diseño de lista horizontal de CollectionView")

## <a name="vertical-grid"></a>Cuadrícula vertical

[`CollectionView`](xref:Xamarin.Forms.CollectionView)puede mostrar sus elementos en una [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) cuadrícula vertical estableciendo su propiedad en un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) objeto cuya [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) propiedad se establece en `Vertical`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="80" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

De forma predeterminada, un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) elemento vertical mostrará los elementos en una sola columna. Sin embargo, en este ejemplo `GridItemsLayout.Span` se establece la propiedad en 2. Esto da como resultado una cuadrícula de dos columnas, que crece verticalmente a medida que se agregan nuevos elementos:

[ ![Captura de pantalla de un diseño de cuadrícula vertical de CollectionView, en el diseño de cuadrícula vertical de la CollectionView de iOS y Android](layout-images/vertical-grid.png "") ] (layout-images/vertical-grid-large.png#lightbox "Diseño de cuadrícula vertical de CollectionView")

## <a name="horizontal-grid"></a>Cuadrícula horizontal

[`CollectionView`](xref:Xamarin.Forms.CollectionView)puede mostrar sus elementos en una [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) cuadrícula horizontal estableciendo su propiedad en un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) objeto cuya[`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) propiedad se establece en `Horizontal`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Horizontal"
                        Span="4" />
    </CollectionView.ItemsLayout>
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                <Grid.RowDefinitions>
                    <RowDefinition Height="35" />
                    <RowDefinition Height="35" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="70" />
                    <ColumnDefinition Width="140" />
                </Grid.ColumnDefinitions>
                <Image Grid.RowSpan="2"
                       Source="{Binding ImageUrl}"
                       Aspect="AspectFill"
                       HeightRequest="60"
                       WidthRequest="60" />
                <Label Grid.Column="1"
                       Text="{Binding Name}"
                       FontAttributes="Bold"
                       LineBreakMode="TailTruncation" />
                <Label Grid.Row="1"
                       Grid.Column="1"
                       Text="{Binding Location}"
                       LineBreakMode="TailTruncation"
                       FontAttributes="Italic"
                       VerticalOptions="End" />
            </Grid>
        </DataTemplate>
    </CollectionView.ItemTemplate>
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

De forma predeterminada, un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) horizontal mostrará los elementos en una sola fila. Sin embargo, en este ejemplo `GridItemsLayout.Span` se establece la propiedad en 4. Esto da como resultado una cuadrícula de cuatro filas, que crece horizontalmente a medida que se agregan nuevos elementos:

[ ![Captura de pantalla de un diseño de cuadrícula horizontal de CollectionView, en el diseño de cuadrícula horizontal de la CollectionView de iOS y Android](layout-images/horizontal-grid.png "") ] (layout-images/horizontal-grid-large.png#lightbox "Diseño de cuadrícula horizontal de CollectionView")

## <a name="headers-and-footers"></a>Encabezados y pies de página

[`CollectionView`](xref:Xamarin.Forms.CollectionView)puede presentar un encabezado y un pie de página que se desplacen con los elementos de la lista. El encabezado y el pie de página pueden ser cadenas, vistas [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) u objetos.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)define las siguientes propiedades para especificar el encabezado y el pie de página:

- `Header`, de tipo `object`, especifica la cadena, el enlace o la vista que se mostrarán al principio de la lista.
- `HeaderTemplate`, de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica el `DataTemplate` `Header`que se va a utilizar para dar formato a.
- `Footer`, de tipo `object`, especifica la cadena, el enlace o la vista que se mostrarán al final de la lista.
- `FooterTemplate`, de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica el `DataTemplate` `Footer`que se va a utilizar para dar formato a.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

Cuando se agrega un encabezado a un diseño que crece horizontalmente, de izquierda a derecha, el encabezado se muestra a la izquierda de la lista. Del mismo modo, cuando un pie de página se agrega a un diseño que crece horizontalmente, de izquierda a derecha, el pie de página se muestra a la derecha de la lista.

### <a name="display-strings-in-the-header-and-footer"></a>Mostrar cadenas en el encabezado y el pie de página

Las `Header` propiedades `Footer` y se pueden establecer en `string` valores, tal y como se muestra en el ejemplo siguiente:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="Monkeys"
                Footer="2019">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    Header = "Monkeys",
    Footer = "2019"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Este código genera las siguientes capturas de pantalla, con el encabezado que se muestra en la captura de pantalla de iOS y el pie de página que se muestra en la captura de pantalla de Android:

[ ![Captura de pantalla de un encabezado y un pie de cadena de CollectionView, en iOS y en el](layout-images/header-footer-string.png "encabezado y pie de página") de la cadena de CollectionView de Android] (layout-images/header-footer-string-large.png#lightbox "Encabezado y pie de cadena de CollectionView")

### <a name="display-views-in-the-header-and-footer"></a>Mostrar vistas en el encabezado y el pie de página

Las `Header` propiedades `Footer` y se pueden establecer cada una de ellas en una vista. Puede ser una vista única o una vista que contiene varias vistas secundarias. En el ejemplo siguiente se `Header` muestran `Footer` las propiedades y establecidas en [`StackLayout`](xref:Xamarin.Forms.StackLayout) un objeto que contiene [`Label`](xref:Xamarin.Forms.Label) un objeto:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.Header>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Monkeys"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Header>
    <CollectionView.Footer>
        <StackLayout BackgroundColor="LightGray">
            <Label Margin="10,0,0,0"
                   Text="Friends of Xamarin Monkey"
                   FontSize="Small"
                   FontAttributes="Bold" />
        </StackLayout>
    </CollectionView.Footer>
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    Header = new StackLayout
    {
        Children =
        {
            new Label { Text = "Monkeys", ... }
        }
    },
    Footer = new StackLayout
    {
        Children =
        {
            new Label { Text = "Friends of Xamarin Monkey", ... }
        }
    }
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Este código genera las siguientes capturas de pantalla, con el encabezado que se muestra en la captura de pantalla de iOS y el pie de página que se muestra en la captura de pantalla de Android:

[ ![Captura de pantalla de un encabezado y pie de página de CollectionView con vistas, en iOS y en el](layout-images/header-footer-view.png "encabezado y pie de página") de la vista de CollectionView de Android] (layout-images/header-footer-view-large.png#lightbox "Encabezado y pie de página de la vista CollectionView")

### <a name="display-a-templated-header-and-footer"></a>Mostrar un encabezado y un pie de página con plantilla

Las `HeaderTemplate` propiedades `FooterTemplate` y se pueden establecer en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objetos que se utilizan para dar formato al encabezado y al pie de página. En este escenario, las `Header` propiedades `Footer` y deben enlazarse al origen actual de las plantillas que se van a aplicar, tal como se muestra en el ejemplo siguiente:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="{Binding .}"
                Footer="{Binding .}">
    <CollectionView.HeaderTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Monkeys"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.HeaderTemplate>
    <CollectionView.FooterTemplate>
        <DataTemplate>
            <StackLayout BackgroundColor="LightGray">
                <Label Margin="10,0,0,0"
                       Text="Friends of Xamarin Monkey"
                       FontSize="Small"
                       FontAttributes="Bold" />
            </StackLayout>
        </DataTemplate>
    </CollectionView.FooterTemplate>
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    HeaderTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    }),
    FooterTemplate = new DataTemplate(() =>
    {
        return new StackLayout { };
    })
};
collectionView.SetBinding(ItemsView.HeaderProperty, ".");
collectionView.SetBinding(ItemsView.FooterProperty, ".");
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Este código genera las siguientes capturas de pantalla, con el encabezado que se muestra en la captura de pantalla de iOS y el pie de página que se muestra en la captura de pantalla de Android:

[ ![Captura de pantalla de un encabezado y pie de página de CollectionView con plantillas, en iOS y en el](layout-images/header-footer-template.png "encabezado y pie de página") de la plantilla de CollectionView de Android] (layout-images/header-footer-template-large.png#lightbox "Encabezado y pie de plantilla de CollectionView")

## <a name="item-spacing"></a>Espaciado de elementos

De forma predeterminada, cada elemento de [`CollectionView`](xref:Xamarin.Forms.CollectionView) una no tiene espacio vacío. Este comportamiento se puede cambiar estableciendo las propiedades en el diseño de los elementos que `CollectionView`utiliza.

Cuando un [`CollectionView`](xref:Xamarin.Forms.CollectionView) establece su [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad en un [`ListItemsLayout`](xref:Xamarin.Forms.ListItemsLayout) objeto, la `ListItemsLayout.ItemSpacing` propiedad se puede establecer en un `double` valor que represente el espacio vacío alrededor de cada elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <ListItemsLayout ItemSpacing="20">
            <x:Arguments>
                <ItemsLayoutOrientation>Vertical</ItemsLayoutOrientation>    
            </x:Arguments>
        </ListItemsLayout>
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> La `ListItemsLayout.ItemSpacing` propiedad tiene un conjunto de devoluciones de llamada de validación, que garantiza que el valor de la propiedad sea siempre mayor o igual que 0.

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new ListItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

Este código genera una lista de una sola columna vertical, que tiene un espaciado de 20 alrededor de cada elemento:

[ ![Captura de pantalla de una CollectionView con espaciado de elementos, en iOS y el espaciado de](layout-images/vertical-list-spacing.png "elementos") de la CollectionView de Android] (layout-images/vertical-list-spacing-large.png#lightbox "Espaciado de elementos de CollectionView")

Cuando un [`CollectionView`](xref:Xamarin.Forms.CollectionView) establece su [`ItemsLayout`](xref:Xamarin.Forms.ItemsView.ItemsLayout) propiedad en un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) objeto, las `GridItemsLayout.VerticalItemSpacing` propiedades `GridItemsLayout.HorizontalItemSpacing` y se pueden establecer en `double` valores que representan el espacio vacío vertical y horizontalmente alrededor de cada elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
       <GridItemsLayout Orientation="Vertical"
                        Span="2"
                        VerticalItemSpacing="20"
                        HorizontalItemSpacing="30" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> Las `GridItemsLayout.VerticalItemSpacing` propiedades `GridItemsLayout.HorizontalItemSpacing` y tienen conjuntos de devoluciones de llamada de validación, que garantizan que los valores de las propiedades sean siempre mayores o iguales que 0.

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
    {
        VerticalItemSpacing = 20,
        HorizontalItemSpacing = 30
    }
};
```

Este código da como resultado una cuadrícula vertical de dos columnas que tiene un espaciado vertical de 20 alrededor de cada elemento y un espaciado horizontal de 30 alrededor de cada elemento:

[ ![Captura de pantalla de una CollectionView con espaciado de elementos, en iOS y el espaciado de](layout-images/vertical-grid-spacing.png "elementos") de la CollectionView de Android] (layout-images/vertical-grid-spacing-large.png#lightbox "Espaciado de elementos de CollectionView")

## <a name="item-sizing"></a>Tamaño del elemento

De forma predeterminada, cada elemento de [`CollectionView`](xref:Xamarin.Forms.CollectionView) un objeto se mide y ajusta de forma individual, siempre que los [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) elementos de la interfaz de usuario de no especifiquen tamaños fijos. Este comportamiento, que se puede cambiar, se especifica mediante el [`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) valor de propiedad. Este valor de propiedad se puede establecer en uno de [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) los miembros de enumeración:

- `MeasureAllItems`: cada elemento se mide individualmente. Este es el valor predeterminado.
- `MeasureFirstItem`: solo se mide el primer elemento, donde todos los elementos subsiguientes reciben el mismo tamaño que el primer elemento.

> [!IMPORTANT]
> La `MeasureFirstItem` estrategia de ajuste de tamaño dará lugar a un aumento del rendimiento cuando se usa en situaciones en las que el tamaño del elemento está diseñado para ser uniforme en todos los elementos.

En el ejemplo de código siguiente se [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemsView.ItemSizingStrategy) muestra cómo establecer la propiedad:

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="dynamic-resizing-of-items"></a>Cambio de tamaño dinámico de los elementos

Los elementos de [`CollectionView`](xref:Xamarin.Forms.CollectionView) un se pueden cambiar de tamaño dinámicamente en tiempo de ejecución cambiando las propiedades relacionadas con el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)diseño de los elementos dentro de. Por ejemplo, en el ejemplo de código siguiente [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) se [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) cambian las propiedades [`Image`](xref:Xamarin.Forms.Image) y de un objeto:

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

El `OnImageTapped` controlador de eventos se ejecuta en respuesta a [`Image`](xref:Xamarin.Forms.Image) un objeto que se puntea y cambia las dimensiones de la imagen para que se vea más fácilmente:

[ ![Captura de pantalla de una CollectionView con el ajuste de tamaño de elementos dinámicos, en iOS y Android, cambio de tamaño de](layout-images/runtime-resizing.png "elemento dinámico") ] (layout-images/runtime-resizing-large.png#lightbox "Tamaño de elemento dinámico de CollectionView")

## <a name="right-to-left-layout"></a>Diseño de derecha a izquierda

[`CollectionView`](xref:Xamarin.Forms.CollectionView)puede diseñar su contenido en una dirección de flujo de derecha a izquierda estableciendo su [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) propiedad en. [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft) Sin embargo, `FlowDirection` la propiedad debe establecerse idealmente en un diseño de página o raíz, lo que hace que todos los elementos de la página o el diseño raíz respondan a la dirección del flujo:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="CollectionViewDemos.Views.VerticalListFlowDirectionPage"
             Title="Vertical list (RTL FlowDirection)"
             FlowDirection="RightToLeft">
    <StackLayout Margin="20">
        <CollectionView ItemsSource="{Binding Monkeys}">
            ...
        </CollectionView>
    </StackLayout>
</ContentPage>
```

El valor [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) predeterminado de un elemento con un elemento [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent)primario es. Por consiguiente, [`CollectionView`](xref:Xamarin.Forms.CollectionView) hereda el `FlowDirection` valor de propiedad de [`StackLayout`](xref:Xamarin.Forms.StackLayout), que a [`ContentPage`](xref:Xamarin.Forms.ContentPage)su vez hereda el `FlowDirection` valor de propiedad de. Esto da como resultado el diseño de derecha a izquierda que se muestra en las siguientes capturas de pantallas:

[ ![Captura de pantalla de un diseño de lista vertical de derecha a izquierda de un CollectionView, en](layout-images/vertical-list-rtl.png "el diseño de lista vertical de derecha a izquierda") de iOS y Android] (layout-images/vertical-list-rtl-large.png#lightbox "Diseño de lista vertical de derecha a izquierda de CollectionView")

Para obtener más información acerca de la dirección de flujo, consulte [localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Desplazamientos de CollectionView de Xamarin. Forms](scrolling.md)
